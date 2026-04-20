### 4.5 Scaling Up: From Sequential to Concurrent Processing

So far, we have processed the yearly files **sequentially**: one file, then the next, then the next.

That is the simplest and often the safest approach. It is:

- easy to debug
- easier on memory
- deterministic in execution order
- usually the right starting point before introducing concurrency

You can think of the scaling options like this:

#### 1. Single-threaded / sequential execution

This is what we have done so far:

```python
for year in YEARS:
    process_year(year)
```

Only one task is active at a time.
This is often the best first version because it is simple, stable, and makes it easier to understand memory use.

In our notebook, this means one year at a time creates objects such as:

* `ds_year`
* `df`
* `ds_year_clean`
* `agg_year`
* `agg_year_df`

and then frees them before moving to the next year.

#### 2. Multithreaded execution

With multithreading, multiple tasks are started in the **same process**, and the threads **share memory**.

This is useful when tasks spend a lot of time **waiting** — for example waiting on file I/O, network access, or other external operations.

In CPython, threads do **not** usually speed up pure Python CPU-heavy work because of the **Global Interpreter Lock (GIL)**. But threads can still help when the workload is I/O-heavy or when underlying libraries release the GIL internally.

That makes threads a reasonable option for workflows like:

```text
open file -> read data -> transform -> save file
```

especially when there are many independent files.

#### 3. Multiprocess execution

With multiprocessing, each worker runs in a **separate process** with its **own Python interpreter** and memory space.

This is better for strongly **CPU-bound** workloads, because multiple processes can use multiple CPU cores without being limited by the GIL.

The trade-off is that processes are heavier than threads:

* they use more memory
* startup overhead is larger
* data sharing is harder because memory is not shared automatically

#### Which one fits this notebook?

For our workflow, each year is independent, which means the task is naturally parallelisable.

However, each worker also loads a full year into memory, and the pipeline is a mixture of:

* file reading
* object creation
* dataframe/xarray transformations
* spatial aggregation
* file writing

So the best choice depends on where the real bottleneck is:

* if the bottleneck is mostly **reading and writing files**, threads can help
* if the bottleneck is mostly **heavy computation**, processes may help more
* if the data becomes larger than memory or the workflow grows more complex, **Dask** may be a better fit than manually managing threads or processes

For a moderate “many independent files” workflow, `ThreadPoolExecutor` is often a practical first step.

### Parallel version with `ThreadPoolExecutor`

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def process_year(year):
    """Process one year of data."""
    ds_year = xr.open_dataset(DATA_DIR / f"ldn_tp_{year}.nc")
    df = ds_year.to_dataframe().reset_index().dropna(subset=["nrr95p"])
    ds_year_clean = (
        df.drop(columns=["rlon", "rlat"])
        .set_index(["latitude", "longitude", "time"])
        .to_xarray()
    )
    agg_year = xagg.aggregate(ds_year_clean, weightmap)
    agg_year_df = agg_year.to_dataframe().reset_index()[["name", "time", "nrr95p"]]
    agg_year_df.to_parquet(OUTPUT_DIR / f"ldn_nrr95p_{year}.parquet", index=False)

    del ds_year, df, ds_year_clean, agg_year, agg_year_df
    gc.collect()
    return year

with ThreadPoolExecutor(max_workers=4) as executor:
    futures = {executor.submit(process_year, year): year for year in YEARS}
    for future in tqdm(as_completed(futures), total=len(YEARS), desc="Processing"):
        year = future.result()
```

### Why this can help

Instead of waiting for one year to fully finish before starting the next, we allow multiple years to be in progress at once.

That can improve throughput when there is enough independent work and when part of the time is spent waiting on file operations.

### Important trade-offs

* **More workers = more memory use**
  If one year needs a lot of RAM, four workers may need roughly four times as much.

* **Threads are not always faster**
  If most of the runtime is pure computation in Python, threads may provide little benefit.

* **Order is no longer guaranteed**
  `as_completed(...)` returns tasks as they finish, not in year order.

* **Concurrency adds complexity**
  Debugging errors, profiling memory, and handling exceptions all become a bit harder.


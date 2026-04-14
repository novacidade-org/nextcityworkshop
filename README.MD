# From Climate Grids to Urban Insights

## Hands-On: Data Preparation and Exploration

**Summer School on Data Analytics and Simulation for City Digital Twins**

---

### Description

In this hands-on workshop, participants will work through a complete data pipeline that transforms raw gridded climate data (2 km resolution daily extreme precipitation indicators from the Copernicus Climate Data Store) into borough-level urban datasets ready for analysis and integration into city digital twins. Using London as a case study, participants will learn to load and explore multi-dimensional climate data with xarray, perform area-weighted spatial aggregation from grid cells to administrative boundaries, store and manage large datasets efficiently with Apache Parquet, and conduct exploratory data analysis to identify spatial and temporal patterns in extreme precipitation. If time permits, the workshop also addresses performance and scalability: participants will learn vectorised computation patterns that replace slow Python loops, memory-efficient dtype strategies, and how to use Dask for out-of-memory datasets. The workflow patterns taught are general-purpose and transferable to any combination of gridded data and administrative boundaries.

---

### Tools

- Python
- Jupyter Notebooks
- xarray / Dask
- pandas / GeoPandas
- xagg
- Apache Parquet
- NumPy
- Plotly / Matplotlib / Seaborn

---

### Topics

- Multi-dimensional gridded data handling (NetCDF / xarray)
- Spatial aggregation from raster grids to vector polygons
- Data cleaning, integration, and transformation
- Spatial and temporal data preparation
- Memory management and efficient processing workflows
- Exploratory data analysis and interactive visualisation
- Vectorised computation and performance optimisation
- Scalable data processing with Dask

---

### Learning Outcomes

After completing the workshop, participants will be able to:

- Load and explore multi-dimensional spatiotemporal data using xarray.
- Perform area-weighted spatial aggregation from gridded data to administrative boundaries.
- Apply efficient data processing patterns (memory management, progress tracking, columnar storage) for large datasets.
- Replace slow iterative code with vectorised operations for significant performance gains.
- Use Dask to process datasets that exceed available memory.

Today I want to start from a very simple observation. In most applied data problems now, especially in the IoT and information age, the problem is usually no longer that we have too little data. The problem is that we have too much data.

We have sensors, platforms, satellite products, administrative systems, logs, APIs, all producing huge volumes of spatial and temporal information. Some of that information is highly valuable. Some of it is noise. But either way, we still have to process it, transform it, and turn it into something useful for analysis or modelling.

And that is really the context for this workshop.

Because if we are building models, simulations, or digital twins, raw data by itself is not enough. We need input data that is structured in a way that supports the processes or relationships we actually want to study. So the challenge is not just collecting data. The challenge is preparing it efficiently.



Now, if we had effectively unlimited compute, then one possible solution would be to just throw hardware at the problem. If you are a venture-backed company with a massive infrastructure budget, maybe that is a realistic strategy.

For most of us, it is not.

Most of us work under constraints: limited RAM, limited CPU, limited storage, limited time, and limited patience. So we need other ways to deal with large data.

Broadly, there are two ways.

One is to use tools and frameworks designed for larger-scale computation, things like Dask or PySpark.

The other is often much simpler and much more immediately useful: write better, more efficient code. Avoid unnecessary operations. Use the right data structures. Choose better storage formats. Think carefully about what actually needs to be computed.

That second point is the main focus of this notebook. And then, if time permits, in the second notebook I’ll show some examples with vectorisation and Dask.


The example I’m using today is climate data, mainly because that is my own area of expertise. But I want to stress that the workshop is not really about climate specifically.

You could replace this with air quality data, mobility data, energy data, land-use data, satellite data, or sensor data. The source can change, the format can change, the variable can change, but the logic of efficient processing remains very similar.

So what I want you to focus on is not just the domain example, but the workflow pattern.


In this case, we start from raw gridded data. That kind of data is common in environmental and geospatial analysis. But for urban analytics or digital twins, raw grids are usually not the form we ultimately want.

City stakeholders often think in terms of boroughs, neighbourhoods, districts, service areas, or administrative units. So one very common task is to take data in one spatial representation and transform it into another representation that is more meaningful for downstream modelling and decision-making.

That is exactly what we’ll do here.

We’ll start with raw gridded data, inspect it, aggregate it to borough-level units, store the result in a more efficient format, and then do some initial exploration.


The dataset itself comes from the Copernicus Climate Data Store and gives us gridded precipitation-related indicators for London. I’m using it here because it is a nice example of a large spatiotemporal dataset stored in NetCDF format, which is a very common format in scientific computing. ([Climate Data Store][1])

But again, the main takeaway is not the precipitation variable. The main takeaway is how to think about efficient operations on large spatial and temporal data.



So the guiding question for this session is:

How do we take large raw spatiotemporal data and turn it into something analysis-ready for urban modelling, while being careful with computation?

That is what we’ll work through step by step.


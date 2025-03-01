---
layout: post
title: InfluxDays
subtitle: Presentation
author: K. E. Claytor
tags: programming, presentation, talk, sensor data, database
---

I've been using the [InfluxDB](https://www.influxdata.com) time series NoSQL database lately.
I applied and was accecpted for a talk at [InfluxDays](https://influxdays.com/past-events-san-francisco-2019/), their 2019 user conference.

Here's the recording:

<iframe width="560" height="315" src="https://www.youtube.com/embed/bwarSNbC344" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

I think it's a decent match for research systems.
You can get up to speed fairly quickly without a background in database systems, and the time-series first aspect of it is good for a wide range of datasets.

Pros:
- Easy to get started with many clients across most languages and a HTTP API.
- Telegraf also records system metrics
- Chronograf integration gives you visualization with low overhead.
- NoSQL means you can change your schema as the experiment evolves (and you don't even need a schema to get started).

Cons:
- Memory issues on IoT platforms (I'm using a snickerdoodle board).
  - Runs out of memory on start (supposedly fixed v1.7.8?)
  - Runs out of memory on compactions (fixed by moving to smaller shards)
  - Takes a long time to boot up and read lots of shards.
- Python client doesn't read into Pandas tables very well (it can *write* from them well).
I usually export from chronograf into a .csv file and then `pivot_table()` a bunch.
- Will drop points if the type doesn't match (explicitly cast python variables).

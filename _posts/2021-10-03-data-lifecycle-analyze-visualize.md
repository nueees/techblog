---
toc: true
layout: post
description: section3
categories: [gcp,bigquery]
title: Data Lifecycle - Process and analyze & Explore and visualize
---
[Cloud Architecture Center](https://cloud.google.com/architecture/data-lifecycle-cloud-platform#process_and_analyze)
---

![]({{site.baseurl}}/images/post/data-lifecycle-4.svg)


## 3.1. Processing large-scale data



### Dataproc
cluster에서 실행 (auto scaling)  
기존 Hadoop, Hive, Spark 에플리캐이션과 연동  
use case: Log processing, Reporting, On-demand Spark clusters, Machine learning  

### Dataflow
Serverless(cluster X), parallel 처리 (No-Ops)  
Spark나 Hadoop과 연동이 아닌 새로운 데이터 pipeline 처리  
use case: MapReduce 안쓰는 parallel processing, User analytics, Data science, ETL, Log processing  

### Dataprep
UI-Driven Data Preparation (No-Ops, 필요시 scaling 가능)
use case: Machine learning, Analytics  



## 3.2. Analyzing and querying data

### BigQuery



## 3.3. Understanding data with machine learning






---
# Explore and visualize

---
[Cloud Architecture Center](https://cloud.google.com/architecture/data-lifecycle-cloud-platform#explore_and_visualize)
---

## 4.1. Explore and visualize






---
toc: true
layout: post
description: section3,4
categories: [gcp,datastudio]
title: Data Lifecycle - Analyze & Visualize
---

[Cloud Architecture Center](https://cloud.google.com/architecture/data-lifecycle-cloud-platform#process_and_analyze)
---

![]({{site.baseurl}}/images/post/data-lifecycle-4.svg)

# Process and analyze

## 3.1. Processing large-scale data
source systems (Google Cloud Storage, Bigtable, Google Cloud SQL) 읽어온 큰 데이터를 처리하고
정규화하고 집계함.
데이터 양이 커서 클러스터로 분산 처리하거나 소프트웨어 툴들의 도움을 받음

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


---
## 3.2. Analyzing and querying data

### BigQuery
앞서 store 할 때 뿐만 아니라 분석할 때도 사용
use case: User analysis(adtech, clickstream, game telemetry), Device and operational metrics(IoT), BI


### Machine learning
처리된 결과를 확대시키거나 data-collection 최적화를 제공하기도 하고 결과 예측도 함

- 음성 인식  
- 자연어 처리  
- 번역  
- 동영상 자동 분석  
- AI 플랫폼 (TensorFlow)  





---
# Explore and visualize

---
[Cloud Architecture Center](https://cloud.google.com/architecture/data-lifecycle-cloud-platform#explore_and_visualize)
---

## 4.1. Explore and visualize

### Datalab
interactive한 web 기반 툴  
pandas, numpy, scikit-learn 등의 다양한 toolkit 지원  


### Data science ecosystem
Datalab 말고도, web 기반 툴인 Apache Zeppelin 지원
R 사용하면 Rstudio Server나 Microsoft ML Server 지원
Scala나 Java 사용하면 Jupyter 지원


## 4.2. Visualizing business intelligence results

### Looker
BI platform

### BI Engine
analysis service 관리  

### Sheets
Spreadsheet visualization  

### Data Catalog
Data discovery and metadata management  

### Data Studio
Dashboarding and visualization  





---
















---
toc: false
layout: post
description: section2
categories: [gcp,bigquery]
title: Data Lifecycle - Store
---

[Cloud Architecture Center](https://cloud.google.com/architecture/data-lifecycle-cloud-platform#store)

---


![]({{site.baseurl}}/images/post/data-lifecycle-3.svg)


## 2.1. Storing object data
bulk로 넣는 File 형식일 때  

### Google Cloud Storage

타 cloud storages이 제공하는 대부분의 특성을 가짐  

1) Off-site management  
2) Quick implementation  
3) Cost-effective  
4) Scalability  
5) Business continuity  


---
## 2.2. Storing database data
RDBMS와 NoSQL같은 DB 저장 시  

### Relational (RDBMS)
- Google Cloud SQL (MySQL, PostgreSQL)
- Spanner (Horizontally scalable)
- 기존 ecosystem (Microsoft SQL Server)

### Non-relational (NoSQL)
- Google Bigtable(wide-column)
- Firestore (Flexible, scalable)
- 기존 ecosystem (MongoDB, Cassandra)


---
## 2.3. Storing data warehouse data
분석용도로 DW에 큰 data 저장 시

### BigQuery
managed DW  

#### Feature  
a fully-managed, serverless data warehouse that enables scalable analysis over petabytes of data.

- serverless cloud 서비스로 설치/운영이 필요없음 (NoOps)
- RDBMS의 SQL 사용 (ANSI)
- speed (in-memory BI Engine and machine learning built in)
- handling big data supported by cloud scale infra
- save 3 replication (data loss risk X)
- batch / streaming

-  easier than Hadoop, Spark: install, configure env, develop MapReduce logic...


prerequisite:  
create Google Cloud project  
enable the BigQuery API  
create a service account (key)    
permissions to open the BigQuery Geo Viz  
permissions to open the Bucket[^1]   

#### Interface

1) Web UI

Query를 WEB UI 상으로 실행

3) Command-Line

Shell 사용해서 실행  
```
bq mk dataset
```

3) REST API

Java나 Python 프로그래밍으로 구현 HTTP 실행  
```
from google.cloud import bigquery
client = bigquery.Client()
```

4) Geospatial analytics 

Query를 WEB UI 상으로 실행해서 지도에서 표시  

#### for reducing the price

1) query 하기 전에 preview 사용 (비용 X)
2) 사전에 query result size 체크 (오른쪽 display)
3) maximum billing limit 설정
4) prevent asterisk(\*): column oriented storage로 wild card 사용시 column마다 압축 풀어서 가져와야 함
5) partition table 및 clustered index table 사용: random access 줄어듦
6) array 형 사용
7) 적절한 슬롯[^2] 수 사용 ( on-demand / flat-rate pricing )

<!-- each query ran against your datasets의 audit logs 저장함  -->



---

### Working with arrays

[참고](https://cloud.google.com/bigquery/docs/reference/standard-sql/arrays)




---
### Cloud Data Fusion
 pipeline building tool  
 
1) Control Center

2) Pipelines

3) Wrangler

4) Metadata

5) Hub

6) Entities

7) Administration

### Building a Pipeline

#### Directed Acyclic Graph (DAG)



### Exploring Data using Wrangler















[^1]: elastic storage bins in Google Cloud Storage
[^2]: parallel processing에 사용되는 virtual CPU 수

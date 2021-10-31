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
Data pipeline 구축 및 관리 (fully-managed)
 
![Google Cloud Next 19 Recap Seoul]({{site.baseurl}}/images/post/datafusion.JP)

1) Pipelines
create complex data processing workflows (both batch and realtime) using an intuitive UI (Directed Acylic Graph, DAG)   

2) Wrangler
connect to data, and transform it using point-and-click transformation steps  
view, explore, and transform a small sample (10 MB) of your data in one place before running the logic on the entire dataset in the Pipeline Studio   

3) Metadata
how datasets and programs are related to each other,  
full visibility into the impact of changes   

4) Hub
distribute reusable applications, data, and code to all users in their organization  


#### Wrangler  

1) create bucket
```
export BUCKET=$GOOGLE_CLOUD_PROJECT
gsutil mb gs://$BUCKET
```

2) data copy to bucker
```
gsutil cp gs://cloud-training/OCBL163/titanic.csv gs://$BUCKET
```

3) Wangler로 preprocessing 작업
Google Cloud Storage에 있는 titanic raw가 있는 data 탭으로 가서 먼저 csv parsing을 함  

Preprocessing (CLI)  
```
drop :body # 기존 body(:은 컬럼임) 날리기
fill-null-or-empty :Cabin 'none' # Cabin 결측치 처리
send-to-error empty(Age) # Age empty면 에러 처리
parse-as-csv :Name ',' false # Name 컬럼 콤마 기준으로 컬럼을 두개로 나눔
drop Name
fill-null-or-empty :Name_2 'none'
rename Name_1 Last_Name
rename Name_2 First_Name
set-type :PassengerId integer
parse-as-csv :First_Name '.' false
drop First_Name
drop First_Name_3
rename First_Name_1 Salutation
fill-null-or-empty :First_Name_2 'none'
rename First_Name_2 First_Name
send-to-error !dq:isNumber(Age) || !dq:isInteger(Age) || (Age == 0  || Age > 125)
set-type :Age integer
set-type :Fare double
set-column Today_Fare (Fare * 23.4058)+1
generate-uuid id
mask-shuffle First_Name
```

[wrangler-docs 참고](https://github.com/data-integrations/wrangler/tree/develop/wrangler-docs)

more에 가서 view schema 하면 해당 데이터 meta 정보 json으로 추출 가능  

Transformation steps 탭에 있는 다운로드 아이콘 누르면 preprocessing 내역 추출 가능



4) insight 탭으로 가서 columns별 데이터 distribution 시각화 된 것 확인  

![Google Cloud Next 19 Recap Seoul]({{site.baseurl}}/images/post/datafusion-insight.JPG)  

Pipelines Studio 내 Wrangler로 작업한 노드의 Properties에 들어가면, 
Wrangler로 한 작업들의 명세를 확인할 수 있음



#### Pipelines  

1) target인 BigQuery에 dataset 미리 생성 (demo_cdf)
2) Data Fusion에서 create pipeline (Batch) 눌러서 Studio 이동  
3) Sink 섹션에 있는 BigQuery를 배치해서 pipeline 연결하고 Properties 구성 (dataset: demo_cdf)  
4) save 후 deploy 하고, run 
![]({{site.baseurl}}/images/post/datafusion-pipelines.png)
5) summary에서 해당 job의 dash board 확인 가능




[^1]: elastic storage bins in Google Cloud Storage
[^2]: parallel processing에 사용되는 virtual CPU 수

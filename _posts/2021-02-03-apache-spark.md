---
toc: true
layout: post
description: section3
categories: [spark,etl]
title: Apache Spark
---
 
📎 Tacademy

---

## 3.1. Spark
Unified Computing Engine and a Set of Libraries for Parallel Data Processing on Computer Clusters  
용도: Machine learning, real time analytics, graph processing, etc.  

- Structured Streaming, Advanced Analytics, Libraries & Ecosystem
- High level APIs (Java, Python, ...)
- Low level APIs (RDDs, Distributed Variables)
- Structured APIs (Datasets, DataFrames, SQL)

기존 MapReduce에서 disk I/O 부하가 심했던 부분을 memory에서 빠르게 작업  

1) Low level APIs
직접 map와 reduce를 function으로 구현하여야 함  

2) High level APIs (functional language)
scala와 python으로 간단하게 처리 가능 (추상화되어 있음)  


---
## 3.2. Spark Architecture
![]({{site.baseurl}}/images/post/spark-cluster-overview.png)


executor 개수와 resource 할당  

### Cluster Manager
worker와 executors 사이에 자원을 중계해주는 역할  
resource를 효율적으로 분배  

ex) Spark StandAlone(cluster X), (Hadoop)Yarn, Mesos, Kubernetes

### Driver Process
SparkContext를 생성하고 RDD를 만들고 operation을 실행하는 프로세스  

Spark-submit을 하면,  
Spark Driver가 Cluster Manager로부터 Executor 실행을 위한 리소스를 요청  
Spark Context는 작업 내용을 task 단위로 분할하여 Executor로 보냄


### Executors
주어진 작업의 개별 task들을 실행하는 작업 실행하고 결과 return

1) 애플리케이션을 구성하는 작업들을 실행하여 driver에 그 결과를 return  
2) 각 executor 안에 존재하는 **block manager**라는 서비스를 통해 사용자 프로그램에서 캐시하는 RDD를 저장하기 위한 메모리 저장소를 제공   

Python,R Process <-> JVM(Spark session) -> Executors  

End to End: csv file read(**narrow**) -> DataFrame sort(**wide**) -> Array  



#### Operations
1) Transformations: map, filter, groupBy, join
lazy operation으로 즉시 실행하는 단계가 아님

2) Actions: count, collect, save
실제로 실행후 driver로 결과 return


---
## 3.3. Processing 방식

### Batch Processing
- big & complex
- processing massive data
- higher latencies


### Real-time Processing (Stream)
- relatively simple and generally independent
- one at a time processing
- sub-second latency

input data stream -> spark streaming -> batch input data -> spark engine -> processed batch data

__spark streaming의 경우 개발하기는 어렵지 않으나, 운영(debugging)이 어려움__


---
## 3.4. Variables
### Accumulator
aggregate multiple values as it progresses


### Broadcast
large read-only variable shared across tasks, operations
large lookup tables


디버깅이나 검증용으로 쓰고
스파크 스트리밍에서는 accumulator, broadcast 지양 (GC문제..)


Fault Tolerance
비즈니스 요건에 따라,
실패시 누락이 되면 되는지 안되는지에 따라 고려 (Kafka)



---
## 3.5. Interface

### RDDs (Resilient Distributed Datasets) 
low level interface, Data Containers  
각기 다른 프로세스 요소들을 추상화한 같은 형태  

1) Hadoop에서 읽은 RDD
path = hdfs://...   

2) Filtered된 RDD
func = contains(...)   

3) Mapped된 RDD
func = split(...)   

Fault되면 Loss된 데이터 이전부터 다시 계산

```
data = sc.textFile(...).split("\t")
data.map(lambda x: (x[0], [int(x[1]), 1]))
.reduceByKey(lambda x, y: [x[0] + y[0], x[1] + y[1]])
.map(lambda x: [x[0], x[1][0] / x[1][1]])
.collect()
```


### DataFrames
코드가 직관적이고 schema를 가지는 interface


```
sqlCtx.table("people").groupBy("name").agg("name", 
avg("age")).collect()
```



---
## 3.6. Scheduling

![]({{site.baseurl}}/images/post/DAG scheduler.jpeg)

### DAG (Directed Acyclic Graphs) Scheduler















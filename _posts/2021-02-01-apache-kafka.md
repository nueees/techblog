---
toc: true
layout: post
description: section1
categories: [kafka,message_broker,queue]
title: Apache Kafka
---
 
๐ [kafka documentation](https://kafka.apache.org/documentation/)

---

## 1.1. Kafka
a distributed, partitioned, and replicated commit log service  

์์ค ์ ํ๋ฆฌ์ผ์ด์(mysql, oracle, nosql,.../app/was/...)๊ณผ ํ๊ฒ ์ ํ๋ฆฌ์ผ์ด์(hadoop, search engine, monitoring, email...)๊ฐ์๊ฐ ๋ง์์ง๊ณ ,ํ๋กํ ์ฝ ํฌ๋งท ํํธํ๊ฐ ์ฌํด์ ธ ์ ์ง๋ณด์๊ฐ ์ด๋ ค์์ง

์ด๋ฌํ ๋ณต์กํจ์ ํด๊ฒฐํ๊ธฐ ์ํด ์ค๊ฐ์์ ์์ค ์ ํ๋ฆฌ์ผ์ด์๊ณผ ํ๊ฒ ์ ํ๋ฆฌ์ผ์ด์ ์ฐ๊ฒฐ์ ๋์จํ๊ฒ ํจ (scale out)

ํ๋ก๋์๊ฐ ํ์ ์ ์ฌํ๊ณ  ์ปจ์๋จธ๊ฐ ๊ฐ์ ธ๊ฐ๋ ๊ตฌ์กฐ
<!-- ํด๋ฆญ๋ก๊ทธ, ๊ฒฐ์ ๋ก๊ทธ -> ๋ก๊ทธ์ ์ฌ, ๋ก๊ทธ์ฒ๋ฆฌ -->

- Kafka Client: kafka์ ๋ฐ์ดํฐ๋ฅผ ์ฃผ๊ณ  ๋ฐ๊ธฐ ์ํด ์ฌ์ฉํ๋ (producer, consumer, admin, stream ๋ฑ) API


---
## 1.2. Brocker & Cluster

- brocker: Kafka application ์๋ฒ ๋จ์
- cluster: 3๋ ์ด์์ brocker๋ก ๊ตฌ์ฑ

- zookeeper: metadata ์ ์ฅํ์ฌ brockers ๊ด๋ฆฌ
- controller: n๋ brocker ์ค ๋ง์คํฐ brocker (allocate partition, health check)


---
## 1.3. Topic

![]({{site.baseurl}}/images/post/kafka-basic-concepts.png)
๋ฉ์์ง(๋ฐ์ดํฐ) ๋ถ๋ฅ ๋จ์
n๊ฐ์ ํํฐ์ ํ ๋น ๊ฐ๋ฅ
๊ฐ ํํฐ์ ๋ง๋ค ๊ณ ์  offset ๊ฐ์ง
๋ฉ์์ง ์ฒ๋ฆฌ์์๋ ํํฐ์ ๋ณ๋ก ๊ด๋ฆฌ
- partition: ๋ ์ฝ๋๋ฅผ ๋ด๊ณ  ์๊ณ  replication ๊ฐ๋ฅ 
- ISR(In-Sync Replica): replication(leader+follower)์ sync๋ก ๋ ๋ฌถ์
- segment: ์ค์ ๋ก ๋ฉ์์ง๊ฐ ์ ์ฅ๋๋ ํ์ผ ์์คํ ๋จ์
![]({{site.baseurl}}/images/post/topic.JPG)
![]({{site.baseurl}}/images/post/topic2.JPG)

---
## 1.4. Producer
๋ฉ์์ง(๋ ์ฝ๋)๋ฅผ brocker๋ก ์ก์  API

### Role
Topic์ ํด๋นํ๋ ๋ฉ์์ง๋ฅผ ํด๋น ํํฐ์์ offset๊ณผ ํจ๊ป ๊ธฐ๋ก
- Publishing: ํน์  topic์ผ๋ก ๋ฐ์ดํฐ ์ก์ 
- ์ฒ๋ฆฌ ์คํจ ์ ์ฌ์๋
- key ์ฌ์ฉ์, ํด๋น partition์ผ๋ก ๋ฐ์ดํฐ ๋งคํ


---
## 1.5. Consumer
๋ฉ์์ง(๋ ์ฝ๋) brocker์์ ์์  API
๋ค์์ consumer๋ฅผ groupํ ํ  ์ ์์

### Role
- Polling: Topic์ partition์ผ๋ก ๋ถํฐ ๋ฉ์์ง(๋ ์ฝ๋)๋ฅผ ๊ฐ์ ธ๊ฐ
- Commit: Partition offset ์์น๋ฅผ ๊ธฐ๋ก
- Parallel Processing: Consumer ์ฌ๋ฌ๊ฐ์ผ ๊ฒฝ์ฐ, Consumer group์ ํตํด ๋ณ๋ ฌ ์ฒ๋ฆฌ

#### Offset number:
ํํฐ์ ๋ด ๊ฐ๊ฒ๋๋ ๊ณ ์  ํค
ํ ํฝ ๋ณ, ํํฐ์ ๋ณ๋ก ๊ณ ์ ํ๊ฒ ์ง์ ๋จ
์ปจ์๋จธ๊ฐ ๋ฐ์ดํฐ๋ฅผ ์ด๋๊น์ง ์ฝ์๋์ง ํ์ธํ๋ ์ฉ๋
๋ฐ์ดํฐ๋ฅผ ์ฝ์ ๊ณณ๊น์ง commit

์ปจ์๋จธ ์ด์๊ฐ ๋ฐ์ํด๋ \_\_consumer_offset ํ ํฝ์ ์ ์ฅ๋ ์ง์  ํ์ธ(High availablility)

![]({{site.baseurl}}/images/post/multi_consumer.JPG)
![]({{site.baseurl}}/images/post/different_consumer_group.JPG)


**๋ณ๋ ฌ์ฒ๋ฆฌ๋ฅผ ์ํด์๋ consumer ๊ฐ์๋ partition ๊ฐ์๋ณด๋ค ์ ๊ฑฐ๋ ๊ฐ์์ผ ํจ**


---
## 1.6. Kafka Streams
๋ฐ์ดํฐ ๋ณํ(transformation) API

- stateful, stateless์ ๊ฐ์ ์ํ ๊ธฐ๋ฐ stream ์ฒ๋ฆฌ ๊ฐ๋ฅ
- stream api์ DSL(Domain Specific Language) ๋์ ์ง์
- Exactly-once ์ฒ๋ฆฌ, HA ํน์ง
- Kafka security(acl, sasl ๋ฑ) ์ง์
- stream ์ฒ๋ฆฌ๋ฅผ ์ํ ๋ณ๋ cluster(yarn ๋ฑ) ๋ถํ์

### ESP (Event Stream Platform)
๋ํ์ ์ผ๋ก Kafka, Kinesis, Flink, Spark, Storm, Logstash...

### Kafka Streams API 
- Source processor
- Sink processor






---
## 1.7. Kafka Connect
Kafka client๊ฐ ์๋ connect๋ฅผ ํตํด ๋ฐ์ดํฐ๋ฅผ import/export ๊ฐ๋ฅ
code ์์ด configuration์ผ๋ก ๋ฐ์ดํฐ migration ์ ์ฌ์ฉ







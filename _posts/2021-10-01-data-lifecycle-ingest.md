---
toc: true
layout: post
description: section1
categories: [gcp,ingest]
title: Data Lifecycle - Ingest
---
> [https://cloud.google.com/architecture/data-lifecycle-cloud-platform](https://cloud.google.com/architecture/data-lifecycle-cloud-platform#ingest)
---

![]({{site.baseurl}}/images/post/data-lifecycle-1.svg)


![]({{site.baseurl}}/images/post/data-lifecycle-2.svg)


## 1.1. App
Data from app events, such as log files or user events, is typically collected in a push model, where the app calls an API to send the data to storage.  

## Logging 

## Pub/Sub

## Cloud SQL

## Datastore

## Cloud Bigtable

## Cloud Firestore

## Cloud Spanner






## 1.2. Streaming
The data consists of a continuous stream of small, asynchronous messages.  

## Pub/Sub



## 1.3. Batch
Large amounts of data are stored in a set of files that are transferred to storage in bulk.  

## Cloud Run

## Storage Transfer Service

## BigQuery Data Transfer Service

## Transfer Appliance










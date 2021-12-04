---
title: "Hadoop"
description: "Notes from a course on Haddop ecosystem"
layout: post
toc: false
comments: true
hide: false
search_exclude: false
categories: [en, bigdata]
---

# Hadoop Platform and Application Framework
## Lesson 1

Hadoop Stack: [ Clients ] > [ MapReduce ] > [ YARN ] > [ HDFS ]

1. Hadoop File System: Distributed, scalable and portable file-system written in Java for the Hadoop framework
  - replicates accross several hosts
  - system is composed of Namenode(s) which keep some metadata on the contained folders (e.g. name, number of replica...) and Datanodes which contain the (replicated) data blocks.
  - secondary namenode: scans and builds snapshots of the primary namenode (raptures information, location etc.)
  - A Hadoop based system always sits on some version of a MapReduce engine:
    * Job/Task trackers: job tracker on the namenode (client's job tracking) and task tracker on the datanodes (operation tracking)
    * MapReduceV2 -> YARN (Hadoop 2.0): Separates the research management and process component (generalization of the hadoop architecture to other processing than mapreduce)
    * Before YARN, the hdfs stack was [ MapReduce ] > [ HDFS ], now it is possible to have others data processing: [ Map Reduce | Others ] > [ YARN ] > [ HDFS ]
  - Yarns = scheduling, MapReduce (in V2) = data processing
2. The Hadoop Zoo
  - Started from the Google FS, and incrementally added functionalities (SQL like queries, BigTable, Sawzall, ...) -> variations accross big tech companies, but with the same global architecture:
    (cloudera's implem) 
    ```
    [ UI Framework (hue) | SDK (hue) ]
    [ Workflow mgmt (oozie) | Scheduling (oozie) | Metadata (Hive) ]
    [ Data Integration (flume, sqoop) | [ Languages, compilers (pig/hive) ] > [ Hadoop ] | Fast read/write access (hbase) ]
    [ Coordination (zookeeper) ]
    ```
3. Hadoop Ecosystem Major Components
  - PIG: 
    * High level programming on to for Hadoop MapReduce
    * Multiple languages: JPython, Java ...
    * Data analysis problems as data flows
    * Pig for ETL: inport, extract, transform, write back on the hdfs [Q: difference with Beam ?]
  - Hive: 
    * Facilitates queriying and managing large datasets in distributed storage
    * Hive QL
  - Oozie:
    * Workflow scheduler to manage Hadoop jobs
    * Coordinator jobs
    * Supports: MapReduce, Pig, Hive, Sqoop...
  - Zookeeper:
    * Provides centralized, AOM and synchronization
  - Flume: 
    * Distributed, reliable and available service for collecting, aggregating and moving large amount of log data
  - Many others (Impala, Cloudera search, Spark, Majout, ...)
  - Spark:
    * Parallel, in-memory, large scale data processing

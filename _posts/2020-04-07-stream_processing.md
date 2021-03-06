---
title: "Stream Processing"
date: 2020-04-07
tags: [stream processing, kinesis, AWS, redis, jenkins, slack, apache spark, databricks]
header:
  image: "/images/Redis.png"
excerpt: "Stream processing, Amazon Kinesis, AWS, Redis, Jenkins, Slack, Apache Spark, Databricks"
mathjax: "true"
---

## Streaming exchange rates on binance

I created a stream processing application using the AWS Kinesis R package's daemon + Redis to record the overall amount of coins exchanged on Binance (per symbol) in the most recent micro-batch.
Then I created a Jenkins job that reads from this Redis cache and prints the overall value (in USD) of the transactions -- based on the coin prices reported by the Binance API at the time of request. 
I report two charts to the "#bots-final-project" Slack channel.

I documented my steps to show my general understanding on how to build data pipelines using Amazon Web Services and R and how to implement a stream processing application (either running in almost real-time or batched/scheduled way) in practice.

[-> find the PDF here](/assets/stream.pdf)


## Apache Spark

In this project I use Spark in a practical manner: I look at cloud configurations, examine databricks (a software as a service running on Amazon)as well as managed Spark EMR, or elastic map reduce, on Amazon and many others.




---
layout: post
title:  Designing data intensive applications - Part 1
date:   2018-06-05 15:30:00
categories: main
---

# Reliable, Scalable and Maintainable applications:
---------------------------------------------------

1. Reliability:
   - _System should continue to work correctly even in the face of adversity_
   - Tolerating hardware and software faults
   - Human error

2. Scalability:
   - _As the system grows (in data, traffic volumes, complexity), we should reasonably deal with that growth_
   - Measuring load and performance
   - Latency percentiles
   - Throughput

3. Maintainability:
   - _Many different people that work on the system, should be able to do so productively_
   - Operability
   - Simplicity and evolvability

A data-intensive application is typically built from standard building blocks that provide commonly needed functionality. For example, many applications need to: 
- Store data so that they can find it again later (databases) 
- Remember the result of an expensive operation to speed up reads (caches) 
- Allow users to search data by keyword or filter it in various ways (search indexes) 
- Send a message to another process, to be handled asynchronously (stream processing) 
- Periodically crunch a large amount of accumulated data (batch processing) 

And to each of these requirements, there are various approaches - different ways of caching, several ways of building search indexes etc. 
Each of these have different access patterns, which means different performance characteristics and thus different implementations. 

Given the variety of use cases we see today, these traditional categories have blurred lines. For example:
- Redis is a datastore that can also be used as message queues
- Kafka is a message queue that can be used with database-like durability guarantees 
So we break down the work into tasks that _can_ be performed on a single tool, and the application stitches various tools together.
---
author: philiphendry
comments: true
date: 2008-03-14 17:13:01+00:00
layout: post
slug: sql-server-replication-lesson-1
title: 'SQL Server Replication : Lesson 1'
wordpress_id: 33
categories:
- Lesson
- Replication
- SQL Server
---

I've decided to chronicle things I learn about replication (above and beyond the normal things to learn in the documentation) after a very hard lesson yesterday during an upgrade which lasted a lot longer than necessary.

So, what's the first lesson I've learnt...

  * **Just because you don't have any subscribers doesn't mean to say that the publisher won't end up doing a lot of work.**
  * **Snapshot generation can be locked by the log reader agent.**

So how did I come across this fact? Well, we had planned a production server upgrade which involved installing a new server with all the publications against an existing database of about 12Gb. However, before installing the subscribers, it was planned to run some updates against the database in order to prevent a lot of data from being replicated and therefore extending the upgrade time.

However, the lesson learned was that _logread.exe_ (the log reader agent) scans through **all** the transactions found in the transaction log and inserts into the distribution database whether there are any subscribers or not. I've assumed that _logread.exe_ only includes data from the transaction log for replication articles and ignores anything else.

These current facts lead to the problems experienced in the implementation. We had run a couple of very substantial queries which had filled the transaction log to almost 8Gb therefore giving a lot of work for the log reader agent to perform - although we had assumed there wouldn't be any work because there were no subscribers!

After the queries had finished we began creating our subscribers - however, we found that the snapshot agents would not complete. A quick look with _sp_who2_ identified the problem - they were being blocked by the log reader agent - something we definitely didn't expect.

We decided that to expedite the implementation we would stop the log reader agent then run the snapshots. Big mistake! The log reader stopped but issued a rollback. What we didn't realise was that the transaction must have been huge and we waited almost 45 minutes for it to stop! However, once it had finished we could trigger the snapshots and continue as expected.

So next time, I'll not underestimate the effects of any query I run on a server that has replication installed!!

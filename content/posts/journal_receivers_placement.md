+++
title = "Journal receivers placement"
date = "2025-11-04T07:52:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","performance","db2"]
keywords = ["ibmi","performance","db2"]
description = ""
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

If you ask 10 IBM i administrators about the best placement for journal receivers, you will likely get 11 different answers. The truth is that there is no one-size-fits-all solution, and the optimal placement depends on various factors such as system workload, storage architecture, and recovery objectives. Also, hardware and software changed significantly over the years, so recommendations that were valid 10 or 20 years ago may not be applicable today.

## Why journal receivers placement matters

Journal receivers store the changes made to database files and other objects. When journaling was introduced, one of its primary purposes was to ensure data integrity and recoverability. That's why writes to journal receivers are synchronous. Therefore journal receiver write performance has a direct impact on application performance.

To ensure the best performance, a common recommendation is to place journal receivers on separate physical disks in a dedicated Auxiliary Storage Pool (ASP).

## Why a dedicated ASP

### Sequential writes

Journal receivers receive a high volume of sequential writes. Before the widespread adoption of solid-state drives (SSD) or Flash storage, IBM i systems were using mechanical hard drives. These drives perform relatively well with sequential writes when they don't handle random I/O operations at the same time. By placing journal receivers in a dedicated ASP, you can minimize the contention with other workloads and improve write performance.

When using external storage (even backed by mechanical drives), this is not a factor since all I/O generally end up on the same drives.

SSD/Flash storage (whether it's internal or external) is far less sensitive to a mixed workload because they don't have a mechanical arm that must move for random operations.

Since current system use either internal Flash storage or external storage, this factor has become less critical.

### Disk protection

When RAID 5 (and later 6) became available on IBM i system, it was often favored over mirroring because of its better storage efficiency (and therefore lower cost) while maintaining a good level of performance for random reads and writes. Unfortunately, RAID 5/6 tends to impact sequential write performance because of a write randomization introduced by the parity data.

To keep the efficiency of RAID 5/6 for file data while maintaining good sequential write performance for journal receivers, the dedicated ASP for journal receivers was often configured with mirroring.

With current systems, internal storage only supports mirroring and external storage generally uses RAID 6 but the storage controllers can handle sequential writes efficiently with their cache.

### Journal receiver storage management

Before IBM i V7R1 (with PTF MF51614), journal receivers had dedicated storage management. Without the *MAXOPT1 option, journal receivers were using a maximum of 10 disks, with the *MAXOPT1 option, this limit could increase to up to 100 disks depending on the max size defined on the journal. Data ASPs often had far more than 10 disks in medium to large environments, so placing journal receivers in the same ASP would create an imbalance in the ASP disks usage that would impact performance. Placing journal receivers in a dedicated ASP would limit this imbalance.

In current releases this is no longer a factor since journal receivers now use same storage management as the rest of the system and use all the disks in the ASP.

### I/O queue contention

Each disk seen by an IBM i partition, whether internal or external, is an I/O queue. Mixing synchronous writes from the journal receivers traffic with the regular random I/O of the database files can lead to increased response times. This factor is still important today.

## Conclusion

While many of justifications for dedicated ASP for journal receivers are not relevant today, having dedicated queues remains beneficial. Also, external storage makes having a dedicated ASP easier and cheaper, that's why it is still a recommendation for high activity systems.
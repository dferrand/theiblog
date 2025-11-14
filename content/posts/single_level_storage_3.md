+++
title = "Single level storage part 3"
date = "2025-11-14T07:52:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","singlelevelstorage"]
keywords = ["ibmi","singlelevelstorage"]
description = "Discover how IBM i's single level storage unifies memory and disk into one address space—no explicit file I/O, automatic caching, fast process switching, and implicit memory sharing. Plus: why security is different."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

This is the third part of a serie about single level storage on IBM i:
* [Part 1](/posts/single_level_storage_1/)
* [Part 2](/posts/single_level_storage_2/)

In this third part, we will look at how single level storage.

## One address space for everything and everyone

While virtual memory systems provide each process with its own address space, single level storage take the opposite approach: there is a single address space shared by all processes and the operating system itself. Also, both memory and disk are mapped into this single address space.

## No need for explicit disk access

In a single level storage system, programs do not need to explicitly request disk access. Instead, they simply obtain virtual addresses and read or write data at those addresses. If the data is not currently in memory, the operating system automatically retrieves it from disk and loads it into memory. Similarly, if the system needs to free up memory, it can automatically move data from memory to disk without any intervention from the program.

Basically, every disk access is a paging/swapping operation.

## Single level storage benefits

Single level storage is very effective at caching data in memory since all disk access are automatically managed by the operating system. Frequently accessed data is kept in memory, while less frequently accessed data is moved to disk as needed.

Process switching is also much faster since there is no need to reconfigure the MMU or flush caches. All processes share the same address space, so switching between them is (almost) simply a matter of jumping to the new process code.

Sharing memory between processes is done implicitly since all processes share the same address space. If a process writes data to a specific address, any other process can read that data from the same address.

## Single lelvel storage drawbacks

One drawback of single level storage is that it can be difficult to determine how much memory a workload actually needs. The system will always try to use all the available memory.

## Security and isolation

Since all processes share the same address space, security and isolation must be enforced by the operating system in other ways. IBM i uses pointer tagging to ensure that processes can only access data they are authorized to access.

In the next part, we will look at how pointer tagging works to provide security and isolation.
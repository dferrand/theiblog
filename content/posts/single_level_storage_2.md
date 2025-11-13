+++
title = "Single level storage part 2"
date = "2025-11-13T07:28:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","singlelevelstorage"]
keywords = ["ibmi","singlelevelstorage"]
description = "Single level storage, Part 2: a clear tour of virtual memory OS design—MMU, process isolation, paging/swapping, and disk access—before we dive into IBM i’s model."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

This is the second part of a serie about single level storage on IBM i:
* [Part 1](/posts/single_level_storage_1/)

In this second part, we will look at how virtual memory Operating Systems work.

## Virtual memory

Virtual memory is a memory management technique used by most modern operating systems to provide an abstraction of memory to programs. Each process is given the illusion of having its own contiguous block of memory starting at address 0. This is called the virtual address space. 

The operating system uses a component called the Memory Management Unit (MMU) to translate virtual addresses used by programs into physical addresses used by the actual memory hardware.

## Security and isolation

One of the main benefits of virtual memory is that it provides memory protection and isolation between programs. Each process has its own virtual address space, it is therefore unable to access (voluntarily or not) another process memory.

## Memory overcommitment

Virtual memory also allows the operating system to use more memory than is physically available in the system. This is done by using a technique called paging or swapping, where parts of the memory that are not currently being used are temporarily moved to disk to free up space for other processes. When a process needs to access data that has been moved to disk, the operating system retrieves it from disk and loads it back into memory.

## Virtual memory limitations

While the MMU makes the translation from virtual to physical addresses pretty fast, switching between processes is extremelly heavy since the MMU must be reconfigured to use the new process page tables and the caches must be flushed.

Sharing memory, while it is generaly possible through the operating system, is explicit and the involvded processes need to cooperate.

## Disk management

Apart from paging/swapping, the programs are responsible for managing their own files on disk. The operating system provides system calls to create, read, write, and delete files on disk.

When a program wants to read or write data to a file, it typically uses system calls provided by the operating system. The OS then translates these requests into commands sent to the disk controller to read or write specific blocks on the disk. The data is transferred between the disk and memory, and the program can then access the data in memory using virtual addresses.

## Virtual memory operating systems

The first fully hardware-enforced virtual memory system was the IBM System/360, ancestor of the IBM z.

Most current general-purpose operating systems are virtual memory operating systems, including Z/OS, AIX, Windows, Linux, macOS, and many others.

## Next

In the next part, we will look at how single level storage works (finally!).
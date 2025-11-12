+++
title = "Single level storage part 1"
date = "2025-11-12T07:52:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","singlelevelstorage"]
keywords = ["ibmi","singlelevelstorage"]
description = "A simplified introduction to single level storage on IBM i—covering memory addressing, processor operations, disk management, and how real memory systems work."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

Single level storage is one of the key architectural features of IBM i that makes it different from most other operating systems.

Single level storage is about memory (main storage) and disk (auxiliary storage) management.

This post series will simplify some concepts to be more accessible.

In this first part, we will look at how processors, memory and disks interact together and how a real memory operating system (or no OS at all) works.

## Memory

Memory, also called main storage or RAM (Random Access Memory), is a fast storage area that the processor can access directly to read and write data. Memory is volatile, meaning that its contents are lost when the system is powered off.

Each memory location has an address, which is a unique identifier that the processor uses to access the data stored at that location. Memory is typically organized into bytes, with each byte having its own address. The address is usually represented as a hexadecimal number starting from 0 up to the maximum memory size minus one. This address is called a physical address.

## Processor

The processor (CPU) is the brain of the computer. It executes instructions to perform operations. The processor contains registers that are small storage locations used to hold data temporarily during instruction execution. Most instructions operate on data stored in registers while some others transfer data between registers and memory.

The processor has a an address bus (a set of wires) that it uses to send memory addresses to the memory controller to read or write data and a data bus (another set of wires) that it uses to transfer the actual data between the processor and memory.

## Disk

Disk, also called auxiliary storage or secondary storage, is a non-volatile storage area that is used to store data and programs permanently. Disk is much slower than memory but it can store much larger amounts of data and retains its contents when the system is powered off.

Disks are either mechanical (HDD) or solid state (SSD or Flash). Both types of disks are organized into blocks (or sectors), which are fixed-size units of data that the disk can read or write in a single operation. The processor cannot access disk blocks directly. Instead, it must instruct the disk controller to read or write data from/to specific blocks, the controller will then transfer the data to/from memory.

## Real memory OS (or no OS)

In a real memory operating system (like MS-DOS for example) or if there is no operating system at all (like an Arduino microcontroller for example), the program interacts directly with memory reading and writing using physical addresses.

In this case, the program can typically access all the memory installed in the system. If multiple programs are running at the same time, they must cooperate to share the memory without interfering with each other. A single program misbehaving can easily corrupt the memory used by other programs or by the operating system itself leading to crashes or data loss.

Accessing disks is done by sending commands to the disk controller to read or write specific blocks. The data is transferred between the disk and memory, and the program can then access the data in memory using physical addresses. If there is an operating system, disk access is typically done through system calls provided by the OS, otherwise the program must interact directly with the disk controller.

## Next

In the next part, we will look at how virtual memory works to provide memory protection and isolation between programs.
+++
title = "Single level storage part 4"
date = "2025-11-24T10:35:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","singlelevelstorage"]
keywords = ["ibmi","singlelevelstorage"]
description = "Single Level Storage — Part 4: pointer tagging explained. Learn how IBM i uses tag bits (in memory and on disk) and processor support to enforce isolation without separate address spaces."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

This is the fourth part of a series about single level storage on IBM i:
* [Part 1](/posts/single_level_storage_1/)
* [Part 2](/posts/single_level_storage_2/)
* [Part 3](/posts/single_level_storage_3/)

In this fourth part, we will look at how pointer tagging works to provide security and isolation in a single level storage system.

## The problem

In a traditional virtual memory system, each process has its own address space, which provides a natural way to isolate processes from each other. Also, since all disk I/O go through the operating system, it can easily enforce filesystem permissions.

However, in a single level storage system where all processes share the same address space and that address space includes data on disk, a process could potentially access another process data or data on disk by simply reading or writing to specific addresses.

## Pointer tagging to the rescue

A pointer is a variable that holds a memory address. On IBM i, a single level storage pointer is a 128 bit (16 bytes) value.

To secure access to memory, a process must be prevented from assigning arbitrary values to pointers but they must still be able to perform pointer arithmetic (e.g., incrementing a pointer to point to the next byte of a memory area it is allowed to access).

To achieve this, the single level storage address space is divided into 16MB and 64KB regions. A process is only allowed to modify the lower 24 (or 16) bits of a pointer, which represent the offset within a region. The upper bits of the pointer, which identify the region and contains some metadata, are managed by the operating system and cannot be modified by the process. Only the operating system can create a pointer.

A pointer is stored in memory as any other variable, a process can therefore write the whole 128 bits of a pointer.

To enforce the rules, the system keeps track of memory areas that contain pointers, any illegitimate modification of a pointer will be detected and any subsequent use of that pointer will be blocked.

This is acheived through pointer tagging: for each 16 bytes of memory, there is a corresponding tag bit that indicates whether that memory area contains a pointer or not. When a process writes to a memory area, it sets the tag bit to 0 unless it is an allowed operation. When a process attempts to use a pointer, the system checks the tag bit to ensure that the pointer has not been illegitimately modified. 

## Allowed operations

### Copying pointers

When a process copies a pointer from one memory location to another, the tag bit is also copied. This ensures that the destination memory location is correctly marked as containing a pointer.

### Pointer arithmetic

When a process performs pointer arithmetic, such as incrementing a pointer to point to the next byte of a memory area it is allowed to access, the system allows modification of the lower 24 bits of the pointer while keeping the upper bits unchanged. The tag bit remains set to 1, indicating that the memory location still contains a valid pointer.

### Pointer creation

When the operating system creates a pointer for a process, it sets the tag bit to 1, indicating that the memory location contains a valid pointer.

## Processor support

To efficiently implement pointer tagging, the processors IBM i runs on include hardware support for tag bits. This hardware support includes the following features:
* Storing the tag bits in a register
* Checking if the tag bit is set

It is important to not that the processor does not enforce pointer tagging by itself, the code must use specific instructions to check and manage tag bits. This means that arbitrary code could bypass pointer tagging. However, since machine code is never directly generated but compiled to MI and then translated to machine code by the operating system, the pointer tagging rules are always enforced.

While the Power processors currently used by IBM i (as well as the older IMPI CISC processors) include hardware support for pointer tagging, it is not required, pointer tagging could be implemented purely in software, albeit with a performance penalty.

## Pointer tags storage

Pointer are stored both in memory and on disk. Therefore, the tag bits must also be stored both in memory and on disk.

For storage on disk, there are a few variations:
* For older internal storage, disks were formatted with 520 byte sectors (instead of the usual 512 bytes). The extra 8 bytes were used to store pointer tag bits. This explains why IBM i internal disks had a smaller advertised capacity than the equivalent disks for AIX or Linux (for example, IBM i disks were 282GB while AIX/Linux disks were 300GB). When external storage was first supported on IBM i, it was limited to the DS8000 which would provide 520 byte sectors LUNs to IBM i.
* Most storage arrays (including the very popular Storwize and Flashsystem) only support 512 byte sectors. When this is used, IBM i uses 9 sectors instead of 8 to store 4KB pages. The extra sector is used to store pointer tag bits. This explains why IBM i disks appear smaller in IBM i than in the storage array management interface. 
* Newer internal storage on IBM i uses 4K sectors instead of 512 byte sectors. When formatted for IBM i, 4K sectors are actually 4160 bytes (instead of 4096 bytes) to store pointer tag bits.

For memory, IBM i uses some ECC bits to store pointer tag bits since error correction doesn't use all of the available bits.
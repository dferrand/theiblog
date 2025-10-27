+++
title = "Filesystems"
date = "2025-10-23T18:12:54-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
cover = ""
tags = ["ibmi", "filesystems"]
keywords = ["", ""]
description = "Presentation of IBM i filesystems"
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

Like most modern operating systems, IBM i relies on multiple filesystems. This
post will present the local filesystems currently supported.

### Library filesystem (QSYS.LIB)

This is generally the most well known and used filesystem.

It is a non hierarchical filesystem (objects are in a library but a library
cannot be in a library).

The library and the object name can be up to 10 characters long. The object type
can be up to 6 characters long. The filesystem is (mostly) case insensitive.

The notation is `library/object` (that was changed from the S/38 notation
`object.library`).

In the IFS, this filesystem is accessible in `/QSYS.LIB`.

### Documents and folders (DLOs or QDLS)

This is the original hierarchical stream oriented filesystem before the IFS was
introduced in V3R1.

It uses the MS-DOS naming convention (up to 8 character name plus 3 characher
extension all case insensitive).

The documents are actually stored in the QDOC library (plus QDOCnnn for user
ASPs).

While still officially supported by IBM, this filesystem has many limitations
and poor performances compared to the IFS. Its used is discouraged.

In the IFS, this filesystem is accessible in `/QDLS`

### root filesystem (/)

Often also called the integrated filesystem. It is a hierarchical filesystem
very similar to unix filesystems.

One key difference with most unix filesystems is that the root filesystem is
case insensitive (you can't have 2 files named Foo and foo in the same
directory) but it will store a file case when created.

As the name implies, this filesystem is the root of the IFS (`/`).

### QOpensys filesystem

QOpenSys is almost identical to the root filesystem, notably, QOpenSys is case
sensitive.

In the IFS, it is accessible in `/QOpenSys`.

### Optical filesystem (QOPT)

The optical filesystem is a hierarchical filesystem used to access optical
media.

In the IFS, it is accessible in `/QOPT`.

### User defined filesystem (UDFS)

User defined filesystems are similar to the root and QOpenSys filesystems but
can be created in a set ASP or independent ASP.

They are mounted in an existing directory in the IFS.

This is all the local filesystems IBM i supports.

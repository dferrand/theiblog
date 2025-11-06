+++
title = "IBM i and Unicode"
date = "2025-11-06T07:52:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","i18n","db2"]
keywords = ["ibmi","i18n","db2"]
description = "Why Unicode matters for IBM i: move beyond EBCDIC limitations with UTF-8 and UTF-16 support for multilingual DB2 data and global applications."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

IBM i has always has strong support for internationalization (i18n) and localization (l10n). This support relies (among other things) on operating system translations (primary and secondary languages) and multiple EBCDIC code pages to represent characters from different languages.

While this approach has worked well for many years, multiple EBCDIC code pages still present challenges when dealing with multilingual data or applications that need to support multiple languages simultaneously. In an increasingly globalized world, this limitation can hinder application development and data exchange.

To solve theses challenges, the computer industry has largely moved to Unicode, a universal character encoding standard that can represent characters from virtually all writing systems. IBM i has embraced Unicode support over the years, and it is now possible to use Unicode throughout the system, including in DB2 databases.

## Unicode, UTF-8, and UTF-16, what is the difference?

In the past, character sets and character encodings were often used interchangeably, mostly because they were generally created together. However, with the introduction of Unicode, it is important to understand the distinction between character sets and character encodings.

Character Set: A character set is a collection of characters that are used in a particular language or group of languages. For example, the ASCII character set includes 128 characters, including letters, numbers, and symbols used in English.

Character Encoding: A character encoding is a method of representing characters from a character set as binary data that can be stored and transmitted by computers. Different character encodings can represent the same character set in different ways.

Unicode is a character set that includes characters from virtually all writing systems, including Latin, Cyrillic, Arabic, Chinese, and many others. Unicode assigns a unique code point (a number) to each character in the set.

UTF-8 and UTF-16 are two different character encodings that can be used to represent Unicode characters as binary data.

## Unicode

Unicode is an open character set. Its current version (17.0) include almost 160,000 characters. It is designed to support up to 1.1 million code points, although not all of these code points are currently assigned to characters.

## UTF-8

UTF-8 (8-bit Unicode Transformation Format) is a variable-length character encoding that uses one to four bytes to represent each Unicode character. It is backward compatible with ASCII, meaning that the first 128 characters in UTF-8 are the same as in ASCII. This makes UTF-8 a popular choice for web applications and other systems that need to support both English and non-English characters.

The key advantages of UTF-8 are its compatibility with ASCII and its efficiency in representing characters from the Latin alphabet, which are common in many languages.

The main drawback of UTF-8 is that any character outside the ASCII range (i.e., characters with code points above U+007F) will require more than one byte, which can lead to increased storage requirements and processing overhead for languages that use many non-ASCII characters.

## UTF-16

UTF-16 (16-bit Unicode Transformation Format) is another variable-length character encoding that uses one or two 16-bit code units to represent each Unicode character. It can represent all Unicode characters, but it is not backward compatible with ASCII.

While UTF-16 can require 4 bytes for some characters (those outside the Basic Multilingual Plane), a large number of non-ASCII characters, such as Chinese, Japanese, and Korean, can be represented with 2 bytes, making the size in bytes more consistent with the number of characters than UTF-8.

## UTF-32

UTF-32 (32-bit Unicode Transformation Format) is a fixed-length character encoding that uses 4 bytes to represent each Unicode character. While it is straightforward and allows for easy indexing of characters, it is less efficient in terms of storage compared to UTF-8 and UTF-16, especially for texts that primarily consist of characters from the Basic Multilingual Plane.

## UCS-2

UCS-2 (Universal Character Set 2-byte) is a fixed-length character encoding that uses 2 bytes to represent each Unicode character. It can represent characters in the Basic Multilingual Plane (BMP) but cannot represent characters outside this range (code points above U+FFFF). UCS-2 has largely been superseded by UTF-16, which can represent all Unicode characters. 

## UTF-EBCDIC

UTF-EBCDIC is a character encoding that represents Unicode characters using the EBCDIC (Extended Binary Coded Decimal Interchange Code) encoding scheme. It is very similar to UTF-8 (both are variable-length encodings) but uses EBCDIC as the base encoding instead of ASCII. It is very rarely used even on mainframe systems for which it was initially designed.

## Unicode on IBM i

IBM i supports Unicode through the following CCSIDs:
* UTF-8: CCSID 1208
* UTF-16 (Big Endian): CCSID 1200
* UTF-16 (Little Endian): CCSID 1201 (rarely used)
* UCS-2 (similar to UTF-16 but fixed-length): CCSID 13488. The system treats this CCSID as UTF-16 for most operations.

While it is not possible to use Unicode CCSIDs as system, job or profile CCSID, they can be used for almost everything else, including DB2 database files.

CCSID 1208 (UTF-8) can be used for CHAR and VARCHAR columns, while CCSID 1200 (UTF-16) can be used for GRAPHIC and VARGRAPHIC columns.

## CCSID 1208 caveats

While CCSID 1208 can easily represent characters from multiple languages, its high variability in storage size (1 to 4 bytes per character) can lead to some challenges when defining database columns.

When defining a CHAR or VARCHAR column with CCSID 1208, the length specified is in bytes, not characters. This means that if you define a VARCHAR(100) column, it can store up to 100 bytes of data, which may be fewer than 100 characters if the data includes multi-byte characters (as little as 25).

That is why CCSID 1200 is often preferred for applications that need to support multiple languages simultaneously, as it provides a more consistent storage size per character (2 bytes for the common ones).
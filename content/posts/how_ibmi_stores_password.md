+++
title = "How does IBM i store your password?"
date = "2025-11-05T08:29:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","security"]
keywords = ["ibmi","security"]
description = "A deep dive into how IBM i stores user passwords, the evolution of its hashing algorithms, and what recent security improvements mean for your system."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

For obvious security reasons, IBM i, like most operating systems, doesn't store user passwords in clear text form.

Encrypting the password before storing it and decrypting it when authenticating a user only slightly improves security because the security of the encryption relies on an encryption key the system has to store somewhere. If the encryption key is found, all the passwords are at risk.

To secure the password storage, IBM i uses a one-way function to transform the password and stores that transformed password. When authenticating a user, the system applies the same one-way function to the password entered by the user and verifies it matches the stored transformed password.

## One-way function

A one-way function is a mathematical function that is easy to compute in one direction but difficult to reverse. In the context of password storage, a one-way function takes the user's password as input and produces a fixed-size output, often referred to as a hash or digest. The key property of a one-way function is that it is computationally infeasible to derive the original password from its hash.

Ideally, a one-way function should also exhibit the following properties for password storage:
* **Deterministic**: The same input should always produce the same output.
* **Pre-image resistant**: It shouldn't be feasible to have a list of known hashes and their corresponding passwords.
* **Collision resistant**: It should be difficult to find two different inputs that produce the same output.
* **Avalanche effect**: A small change in the input should produce a significantly different output.
* **Slow to compute**: The function should be computationally intensive to deter brute-force attacks.

## A grain of salt

The deterministic and pre-image resistant properties of one-way functions can be antagonistic. If the function is deterministic, an attacker can build a list of common passwords and their corresponding hashes (a rainbow table) to speed up the process of finding the original password from its hash. To mitigate this risk, modern password storage techniques often incorporate a "salt" — a random value added to the password before hashing, the salt is stored in clear alongside the password hash. This ensures that even if two users have the same password, their stored hashes will be different. This also makes it impractical to use precomputed rainbow tables for attacks.

## The problem with Netserver

Netserver, the IBM i service that provides file and print sharing over SMB/CIFS, needs to authenticate users based on Microsoft password hashes. Unfortunately, older Windows version (up to 95/98/ME) used a very weak hashing algorithm (LM hash) that is vulnerable to brute-force attacks. To support these older clients, IBM i has to store the LM hash of the password in addition to the more secure hash used for native IBM i authentication. This means that if an attacker gains access to the password hashes stored on the system, they could potentially crack the LM hashes and recover the original passwords, compromising the security of user accounts.

## IBM i password hashing algorithms

IBM i password hashing is controlled by the QPWDLVL system value. It also defines the password case sensitivity and maximum length:
* **Level 0**: at this level, the password is not technically hashed but used as a key to encrypt a value different for each password with the DES algorithm. This level supports passwords up to 10 characters long and is case-insensitive. The weak LM hash is also stored for Netserver compatibility.
* **Level 1**: same as level 0 but the weak LM hash is not stored anymore.
* **Level 2**: at this level, IBM i uses the SHA-1 hashing algorithm to hash passwords with a salt. This level supports passwords up to 128 characters long and is case-sensitive. The weak LM hash is stored.
* **Level 3**: same as level 2 but the weak LM hash is not stored anymore.
* **Level 4**: (available since IBM  i 7.5) at this level, IBM i uses the SHA-2 hashing algorithm to hash passwords with a salt. This level supports passwords up to 128 characters long and is case-sensitive. The weak LM hash is not stored.

Level 0 and 1 have the following limitations:
* Passwords are very short (maximum 10 characters).
* Passwords have low complexity due to the limited character set (uppercase letters, digits, and a few special characters).
* The hashing/encryption algorithm (DES) is weak by modern standards.
* Level 0 stores the very weak LM hash. Current consumer GPUs can brute-force those in a matter of seconds.

Level 3 is a great improvement over levels 0 and 1, but SHA-1 is no longer considered secure against well-funded attackers.

Level 2 is a transitional level that improves password security for native IBM i authentication but still exposes passwords to attacks via the weak LM hash.

Level 4 is the recommended level for modern IBM i systems as it uses a strong hashing algorithm (SHA-2), supports long and complex passwords, and does not store the weak LM hash.

## IBM i password storage location

IBM i stores password hashes in an internal control block that has all the software and hardware access protections of IBM i and IBM Power Systems.

There are 2 main ways to access password hashes on IBM i:
* The `QSYRUPWD` API can retrieve the password hash for a given user profile if the caller has sufficient authority (*ALLOBJ and *SECADM). This API was introduced for HA software to replicate user passwords. This was corrected in IBM i 7.5.
* A backup media containing a `SAVSYS` or `SAVSECDTA` contains the password hashes for all user profiles. Unless the backup is encrypted, an attacker with access to the backup media could extract the password hashes and attempt to crack them offline.

## IBM i 7.5 improvements

IBM i 7.5 contained several security improvements. While the new password hashing level 4 is the most significant and the most advertised, other improvements were made to protect password hashes:
* The `QSYRUPWD` API was modified to not return password hashes anymore.
* The weak LM hash is no longer stored at any password level. This addresses the weakest link of password storage on IBM i and makes password level 0 and 1 technically identical.
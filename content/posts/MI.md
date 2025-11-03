+++
title = "MI, TIMI"
date = "2025-11-03T07:52:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","s38","timi"]
keywords = ["ibmi","s38","timi"]
description = "A concise technical look at the IBM System/38 Machine Interface (MI) and its successor TIMI: what they are, how program conversion works, and why creation data and observability matter for binary portability and OS migrations."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

One of the key technologies introduced with the IBM System/38 is the Machine Interface architecture to isolate the application software and most of the operating system from the underlying hardware.

The Machine Interface (MI) is a virtual instruction set, that is, an instruction set for a virtual CPU rather than a physical one.

Compilers for the System/38 convert the source code into MI instructions. The system then converts the MI instructions into executable instructions for the actual CPU of the system.

Both the MI instructions and the executable instructions are stored in the program object. When the program is called, the system can directly run the executable instructions if they match the CPU architecture or recreate them from the MI instructions if needed (without having to recompile from the original source code).

## Comparison with other virtual machines

The most well-known virtual machine nowadays is probably the Java Virtual Machine. The Java compiler converts the Java source code into a virtual instruction set called the Java Bytecode. Unlike the System/38, there is no conversion to executable instructions at this stage.

When a Java program is executed, the Java Virtual Machine (JVM) interprets the Bytecode instructions on the fly to run the program. Most modern JVMs integrate a Just-In-Time (JIT) compiler that converts the most used Bytecode parts of a program into executable instructions to increase performance. This compilation is only stored in the JVM memory. It is lost when the JVM ends.

On the System/38, MI instruction are never interpreted, their purpose is only to recreate the executable instructions if needed.

Another popular virtual machine is Microsoft CLR used by Microsoft .Net. It works in a similar way to the JVM.

## From MI to TIMI

When IBM created the OS/400 (now IBM i) to replace the System/38, they created the Technology Independent Machine Interface (TIMI) as a backward compatible extension to the System/38 MI. This way, the OS/400 was capable from day one to run System/38 programs without recompilation.

## When is program conversion required?

The first time program conversion was required was when running System/38 programs on AS/400.

The second time program conversion was required was when migrating from CISC to RISC systems.

The third time program conversion was required was with V6R1. Even if running on the same hardware, V6R1 introduced changes in the operating system that required all programs to be converted.

## Observability and creation data

To convert a program, the Licensed Internal Code (LIC) needs to access the program creation data. Program creation data is a subset of observability.

It was common in the System/38 era to remove observability from programs to reduce their size. It was also commonly done by software editors to make decompilation more difficult.

All programs compiled with a target release of V5R1 or higher, always retain creation data even if observability is removed meaning their conversion is always possible.

## Program conversion after V6R1

All Power RISC processor are backward compatible meaning a newer generation processor can run all instructions from previous generations. They are not always forward compatible since IBM introduces new instructions that older generations can't run.

Before V6R1, when a program was created (or converted), the executable code generated was using the instruction set of the oldest processor supported by the target release. For example a program with a target release of V5R4 would use the Pulsar instruction set even if compiled on a Power6.

Starting with V6R1, a program can use the instruction set of a newer generation of processors than the oldest supported by the target release. For example a program with a target release of V7R4 can use the instruction set of the Power9 even though this version can run on a Power8. This feature is called Adaptive Code Generation. In this case, if the program is moved to a Power8, it will be converted to use the Power8 instruction set.
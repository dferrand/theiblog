+++
title = "The LODRUN command"
date = "2025-11-07T07:46:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi"]
keywords = ["ibmi"]
description = "Everything you need to know about LODRUN on IBM i—how it works, when to use it, and how it can make program loading and running easier."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

The LODRUN command is a nice little command on IBM i that allows you to restore and run a program in one step. It is particularly useful when you want to install a tool or a software on multiple systems without having to manually run many commands. It is suitable for use by users with little experience on IBM i.

It can be used to load and run programs from various sources, such as:
* A save file (SAVF)
* A tape device (phyisical tape, VTL or virtual image catalog)
* An optical device (physical, virtual, virtual image catalog or network image catalog)

## How to create a LODRUN "package"

When you run the LODRUN command, it will restore a program named `QINSTAPP` to QTEMP and call this program.

If using a tape or optical device, the `QINSTAPP` program must have been saved from the QTEMP library, if using a save file, it can be saved from any library.

To create the LODRUN package, you need to:
* Create the program `QINSTAPP`. This program will contain the logic to install your tool or software. It can be written in any language supported by IBM i, such as RPG, COBOL, CL, etc.
* Save the program `QINSTAPP` to a save file, tape or optical device (from QTEMP unless saving to a save file) with the SAVLIB or SAVOBJ command. The media can contain other objects the `QINSTAPP` program will need to perform the installation but the LODRUN command will only restore the `QINSTAPP` program to QTEMP.

## QINSTAPP parameters

The `QINSTAPP` is always called with at least on 10 char parameter containing the device name used with LODRUN command.

If a save file is used, this parameter will contain `*SAVF` and a second 20 char parameter will contain the name of the save file followed by the library name.

## `QINSTAPP` restrictions

The `QINSTAPP` has very few restrictions:
* The owner of the program on the media must exist on the target system. An IBM supplied user is a good choice.
* The `QINSTAPP` program must not call the LODRUN command.

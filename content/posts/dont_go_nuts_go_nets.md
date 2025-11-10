+++
title = "Don't go nuts, GO NETS!"
date = "2025-11-10T12:00:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","netserver"]
keywords = ["ibmi","netserver"]
description = "A guide to GO NETS: IBM's command-line tool for managing IBM i NetServer (SMB file sharing) from a 5250 session. Covers installation, usage, and the benefits of a terminal-based approach"
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

Originally introduced in V4R2 as iSeries support for Windows Network Neighborhood, now known as IBM i NetServer, this function allows IBM i systems to share their resources (generally IFS directories) with Windows clients using the SMB protocol.

When it was first released, the only official way to setup IBM i NetServer (beside using APIs) was the the now defunct Operations Navigator, the Java based Windows GUI management tool for IBM i. Nowadays you can use IBM Navigator for i, the web based management tool for IBM i.

Beside the official GUI tool, IBM also offers a command line tool called `GO NETS` that can be used from a 5250 session to manage IBM i NetServer since V5R2.

Be aware that, while it is created by IBM, this tool is provided "as is", you can't ask IBM support for help with it.

## Why a 5250 tool?

There were a few reasons to provide a 5250 tool to manage IBM i NetServer:
* *psychological*: Operations Navigatore was not everybody's favorite tool. It was a very heavy tool that was pretty slow to start and required a lot of memory. Some long time IBM i administrators didn't like not being able to manage some aspects of their system from a 5250 session.
* *security*: Some system were only accessible through management workstations where keeping Operations Navigator up to date was not always easy. A 5250 tool could be used from any terminal or terminal emulator.
* *Windows dependency*: Operations Navigator required a Windows workstation to run. A 5250 tool could be used from any platform capable of running a 5250 terminal emulator.
* *automation*: `GO NETS` also provides CL commands that can be used in CL scripts to automate IBM i NetServer management tasks.

## Installing the tool

`GO NETS` is not installed by default. To install it, follow these steps:
* Install option 7 of the operating system (57XXSS1)
* Create the `NETSRVCMD` library: `CRTLIB LIB(NETSRVCMD) TEXT('i5/OS NetServer menu and commands')`
* Add the library to the library list: `ADDLIBLE NETSRVCMD`
* Unpack the tool: `CALL QUSRTOOL/UNPACKAGE PARM('*ALL ' 1)`
* Create the installation program: `CRTCLPGM NETSRVCMD/TZLSINST QUSRTOOL/QATTCL`
* Run the installation program: `CALL NETSRVCMD/TZLSINST NETSRVCMD`
* Enjoy: `GO NETS`

## Using the tool

To use, the tool, you can add the `NETSRVCMD` library to your library list and run the command `GO NETS`.

More information about the tool and its commands can be found in member `TZLSINFO` in `QUSRTOOL/QATTINFO`.

## Upgrading the tool

To upgrade the tool, you simply clear the `NETSRVCMD` library (`CLRLIB NETSRVCMD`) and reinstall it following the installation steps above.
+++
title = "The *JOBSCD object type"
date = "2025-10-28T06:30:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi"]
keywords = ["ibmi"]
description = "The *JOBSCD object type"
showFullContent = false
readingTime = true
hideComments = false
+++

There are many object types we encounter every day while working on IBM i,
typically \*LIB, \*FILE, \*DTAARA, or \*PGM.

The \*JOBSCD object type is far less common but it has some interesting
properties. As its name implies, it contains the data of the IBM i integrated
job scheduler (often called `WRKJOBSCDE`). There is always one and only one
*JOBSCD object on any IBM i system, it is called `QDFTJOBSCD` and is located in
`QUSRSYS` library.

You can't delete, rename, duplicate, or move this object, it can only be
QDFTJOBSCD in QUSRSYS.

The object can be saved and restored at will (as long as you don't try to
restore it in another library). This has two main use cases:

- For systems with software replication of PowerHA, you can save and restore the
  QDFTJOBSCD object to switch them between production and backup system during a
  switchover.
- Before V7R5, there was no way to suspend the scheduler while retaining the
  status of the scheduler individual entries. The workaround was to backup the
  QDFTJOBSCD object, hold all the scheduler entries with `HLDJOBSCDE` and
  restore the QDFTJOBSCD object to resume scheduling. V7R5 introduced the
  special value *JOBSCD in the `HLDJOBSCDE` command that prevents the scheduler
  to start any job until it is enabled with `RLSJOBSCDE`, all while keeping the
  status of the individual scheduler entries.

It is a bit surprising to see that a dedicated object type was created while
being restricted to only one object per system, IBM was probably thinking about
allowing multiple job schedulers one day (as hinted by David Novey in
[this article](https://www.mcpressonline.com/it-infrastructure/it-infrastructure-other/job-scheduling-compliments-of-ibm)
from 1992 when the scheduler was introduced in V2R2!). This hasn't happened (and
probably never will!).

While the integrated job scheduler is still fully supported by IBM and even saw
some improvements in V7R5 (addition of the yearly schedule and the possibility
to hold the scheduler without changing the entries state), it is still a very
basic scheduler (as it's intended to be). If you need more advanced functions
like calendars, job dependencies, sub daily schedules, the IBM Advanced Job
Scheduler (also known as JS1) is now usable for free!

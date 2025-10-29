+++
title = "Why do I need to run RSTAUT?"
date = "2025-10-29T06:30:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi"]
keywords = ["ibmi"]
description = "Why do I need to run RSTAUT?"
showFullContent = false
readingTime = true
hideComments = false
+++

One of the final steps of an IBM i system recovery is to restore the private authorities using the RSTAUT command. Why is that necessary?

## What are private authorities?

Private authorities are all the authorities granted to a user profile on an object beside the owner and the public authorities.

## How are private authorities different?

The owner authority, the public authority, as well as the authorization list (if any) of an object are stored as part of the object attributes. When an object is backed up, these attributes are saved along with the object data.

Private authorities, on the other hand, are stored with the user profile the authority was granted to. When a user profile is backed up, all the private authorities granted to that user profile are saved along with it.

## Chicken and egg problem

When restoring a system from a backup, there is a circular dependency:
* The user profile owning an object must exist before the object can be restored.
* The object must exist before the private authorities can be restored.

To solve this problem, the following sequence is used during a system recovery:
* Restore user profiles. The private authorities can't be restored at this point since the objects don't exist yet. The system stores the private authorities information in a temporary location.
* Restore objects. Since the user profiles exist, the owner, the owner authorities, and the public authorities can be restored correctly.
* Restore private authorities from the temporary location using RSTAUT. Since both user profiles and objects exist, the private authorities can now be restored correctly.

RSTAUT is therefore a necessary step to ensure that all authorities are restored correctly after a system recovery.

RSTAUT can take a very long time to complete on systems with many private authorities, this is one of the reasons to minimize the use of private authorities on IBM i systems.

## The PVTAUT(*YES) parameter

In V6R1, IBM introduced the PVTAUT parameter on all the applicable save and restore commands. When set to *YES, the system will save or restore private authorities along with the objects. Of course, PVTAUT(*YES) on the save command for it to work on the restore command.

The default value is *NO to maintain compatibility with older versions of IBM i on restore operations and to maintain the backup performance since saving private authorities can significantly increase the time it takes to complete a backup.

IBM does not recommend using PVTAUT(*YES) for daily and full system backups for performance reasons, but it can be useful for ad-hoc partial backups and restores.
+++
title = "Private authorities and why you (probably) don't want them"
date = "2025-10-30T08:30:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi"]
keywords = ["ibmi"]
description = "Private authorities allowed IBM i administrators to finely tune object access for user profiles, but they come with significant management and performance costs. This article explores why minimizing their use is beneficial."
showFullContent = false
readingTime = true
hideComments = false
+++

Private authorities on IBM i allow administrators to grant specific access rights to user profiles on individual objects. While this feature provides granular control over object access, it also introduces complexity and potential performance issues. In most cases, it's advisable to minimize the use of private authorities in favor of simpler and more manageable access control methods.

## What are private authorities?

Private authorities are specific access rights granted to a user profile on an object, beyond the owner and public authorities. They allow for fine-tuned control over who can access what on a system.

## What's the problem with private authorities?

### Runtime performance impact

Since private authorities are stored with user profiles rather than objects, the system must perform additional lookups to determine access rights. This can lead to slower performance, especially on systems with a large number of private authorities.

### Backup and restore complexity

Private authorities complicate backup and restore processes. During a system recovery, private authorities must be restored separately after user profiles and objects have been restored, adding time and complexity to the process (see this [post](https://thei.blog/posts/why_rstaut/)). On a system with many privates authorities, the RSTAUT command can run for hours.

### Management overhead

With greater granularity comes greater management overhead. Keeping track of who has what access on which objects can become cumbersome, leading to potential security risks if not managed properly.

For example, applying private authorities to 1000  objects for 1000 user profiles results in 1,000,000 private authority entries to manage. Adding or remnoving a user profile or object requires updating 1000 private authority entries.

Granting or revoking private authorities to database files (or any object when revoking) requires the object to be locked, which can be disruptive in a production environment.

## Alternatives to private authorities

One (bad) alternative would be to grant the required public authorities to the objects. This is of course not recommended as there is no security anymore.

A better alternative is to use authorization lists (*AUTL). Authorization lists allow you to group access rights and assign them to multiple objects. This reduces the number of individual access rights that need to be managed and can simplify administration.

## Runtime performance considerations

Authorization lists are more efficient than private authorities because the system only needs to check the authorization for one object (the authorization list) and can cache the results for all objects using that authorization list. This reduces the number of lookups required and improves performance.

### Backup and restore complexity

Authorization lists are restored with the user profiles, therefore, they are available when the objects are restored. This eliminates the need for a separate step to restore private authorities, simplifying the backup and restore process. While the RSTAUT command is still needed during a system recovery because some private authorities may still exist, the command will run much faster since there are fewer private authorities to restore.

### Management overhead

Authorization lists reduce management overhead by allowing administrators to manage access rights in groups rather than individually. This makes it easier to keep track of who has access to what and reduces the risk of mismanagement.

If a database file is secured by an authorization list, granting or revoking access does not require locking the object, which is less disruptive in a production environment.

### Authorization list caveats

Authorization lists are not mutually exclusive with private authorities. While this can be useful to handle special cases, it can also lead to confusion if not managed properly. It's important to have clear policies and procedures in place for managing both authorization lists and private authorities.

Authorization lists can only exist in QSYS library. This means that a proper naming convention must be established to avoid name collisions between different applications and environments.

While authorization lists simplify access management, implementing them in an existing system with many private authorities can be challenging. A thorough review of current access rights and careful planning are necessary to transition effectively.
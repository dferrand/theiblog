+++
title = "IBM i remote filesystems"
date = "2025-10-24T14:48:22-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi", "filesystems"]
keywords = ["ibmi", "filesystems"]
description = "Presentation of IBM i remote filesystems."
showFullContent = false
readingTime = true
hideComments = false
+++

Beside the [local filesystems](/posts/filesystems), IBM i supports several
remote filesystems allowing access to files not residing on the logical
partition.

### Network File System (NFS)

NFS is the traditional UNIX file sharing protocol.

There are two NFS versions currently in use: v3 and v4.

NFSv3, still the most widely used has the following notable characteristics:

- Security is enforced by the client, not by the server, this means that, while
  the server stores permissions, it is the client responsibility to authenticate
  and enforce permissions. This is a very uncommon situation by today's
  standards.
- Multiple TCP and UDP ports are used, some of them dynamically assigned, making
  the protocol difficult to manage in firewalls.
- Data is not encrypted and the protocol doesn't allow encryption (encryption
  can be done through a VPN or tunnel)

NFSv4, while being more than 20 year old, is still less used than NFSv3, partly
because its implementation is more complex than NFSv3. It provides the following
improvements:

- Authentication and authorization is enforced by the server.
- Only one static TCP port is used making it trivial to manage in a firewall.
- Data can be encrypted by the protocol

To use the NFS client on IBM i, we use the MOUNT command to mount an NFS share
in the IFS. The files are then used as if they were on the local system.

### NetClient file system (QNTC)

QNTC was used to access files in Integrated xSeries Servers (aka Integrated PC
Servers aka FSIOP) running Windows, Linux or OS/2. Those are not supported on
current IBM i release.

Nowadays, QNTC is used as a SMB client. SMB is the file sharing protocol
typically used by Microsoft Windows computers. Most UNIX/Linux can serve files
with the SMB protocol via Samba. IBM i can serve files with this protocol
through NetServer.

Authentication can be done through Kerberos if using single sign on, otherwise,
the IBM i user name and password will be used to authenticate to the SMB server.

Recent version of SMB (v3), offer encryption. Previous versions didn't allow
encryption.

The `/QNTC` directory in the IFS is used to access SMB servers. Each directory
in `/QNTC` will connect to a SMB server with the same name as the directory.
Directories can be created in `/QNTC` with the `MKDIR` command.

### IBM i file server file system (QFileSvr.400)

This filesystem allows and IBM i to access another IBM i IFS.

Like QNTC, authentication can be done through Kerberos is using single sign on
on both server and client, otherwise, the client user name and password are used
to authenticate to the SMB server.

By default, the protocol isn't encrypted but it is possible to setup ssl
encryption.

The `/QFileSvr.400` directory in the IFS is used to access another IBM i IFS.
Each directory in `/QFileSvr.400` will connect to an IBM i with the same name as
the directory. Directories can be created in `/QFileSvr.400` with the `MKDIR`
command.

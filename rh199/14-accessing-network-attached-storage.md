## Chapter 14.1 - Mounting Network-Attached Storage with NFS

There are several versions of NFS.

* NFS 4.2 is the default version on RHEL8. NFSv4 uses only the TCP protocol to communicate with the server.
* NFSv4 and NFSv3 are major versions still supported on RHEL8.
* NFSv2 is no longer supported.

### Mounting and Unounting NFS Shares

There are multiple ways NFS shares can be mounted:

* Manually, using the `mount` command.
* Automatically at boot time using `/etc/fstab` entries.
* On demand, using either the `autofs` service or the `systemd.automount` facility.

### The nfsconf Tool

The `nfsconf` tool is used to manage NFS client and server configuration files under NFSv4 and NFSv3. It is configured via `/etc/nfs.conf`. 

## Chapter 14.3 - Automounting Network-Attached Storage

The `automounter` is a service called `autofs` that automatically mounts NFS shared "on-demand", and will automatically unmount NFS shares when they are no longer being used.

### Create an automount

1. Install the `autofs` package: `yum install autofs`
1. Add master map file to `/etc/auto.master.d`. This file identifies the base directory used to mount points and identifies the mapping file used: `vim /etc/auto.master.d/demo.autofs`
    * Note that the name is arbitrary, but it must have a `.autofs` extension.
1. Add a master map entry, in this case, for indirectly mapped mounts:
    `/shares /etc/auto.demo`
    * This entry uses the `/shares` directory as the based for indirect automounts.
    * The `/etc/auto.demo` file contains the mount details.
1. Create the mapping files, eahich identifies the mount point, mount options, and source location to mount for a set of automounts: `vim /etc/audo.demo`
    `work -rw-sync serverb:/shares/work
    * The format of an entry is mount point, mount options, and source location. This example shows a basic indirect mapping entry. Direct maps and indirect maps using wildcards are covered later in this section.
1. Start and enable the automounter service: `systemctl enable --now autofs`

### Direct Maps

Direct maps are used to map an NFS share to an exiting absolute path mount point.

To use directly mapped mount points, the master map file might appear as follows:

`/- /etc/auto.direct`

All direct map entries use `/-` as the base directory. In this case, the mapping file that containt the mount details is `/etc/auto.direct` (contents below).

`/mnt/docs -rw,sync serverb:/shares/docs`

### Indirect Wildcard Maps

When as NFS server exports multiple subdirectories within a directory, then the automounter can be configured to access any one of those subdirectories using a single mapping entry. For example:

`* -rw,sync serverb:/shares/&`

In the above example, the mount point (or key) is an asterisk character (&), and the subdirectory on the source location is an ampersand character (&). When a user attempts to access `/shares/work`, the key * (which is "work" in this example) replaces the ampersand in the source location and `serverb:/shares/work` is mounted.

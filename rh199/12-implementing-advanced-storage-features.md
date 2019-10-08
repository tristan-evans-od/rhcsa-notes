## Chapter 12.1 - Implementing Advanced Storage Features

### Creating Logical Volumes

Logical volumes and logical volume management make it easier to manage disk space: 

* If a file system that hosts a logical volume needs more space, it can be allocated to its logical volume from the free space in its volume group and the file system can be resized. 
* If a disk starts to fail, a replacement disk can be registered as a `physical volume` with the `volume group` and the logical volume's `extents` can be migrated to the new disk.

A `logical volume` is composed on several parts:

* `physical devices` are storage devices used to save data stored in a logical volume. These are block devices and could be disk partitions, whole disks, RAID arrays, or SAN disks. A device must be initialized as an LVM physical volume in order to be used with LVM. The entire device will be used as a physical volume.
* `physical volumes (PVs)` are initialized from a physical device to be used in a LVM system. LVM tools segment physical volumes into `physical extents (PEs)`, which are small chunks of data that act as the smallest storage block on a physical volume.
* `volume groups (VGs)` are storage pools made up of one or more physical volumes. This is the functional equivalent of a while disk in basic storage. A PV can only be allocated to a single VG. A VG can consist of unused space and any number of logical volumes.
* `logical volumes (LVs)` are created from free physical extents in a volume group and provide the "storage" device used by applications, users, and the operating system. 

The high-level steps to creating a VM are the following:

1. Determine which physical devices to use.
1. Initialize devices as physical volumes.
1. Combine physical volumes into a volume group, creating a pool of disk space out of which logical volumes can be allocated.
1. Create logical volumes from the available space in the volume group and format it with a file system.

LVM provides a comprehensive set of commands-line tools for implementing the managing LVM storage.

* `pvcreate` - labels a partition (or other physical device) as a physical volume. This devices the physical volume into physical extents (PEs) for a fixed size (4 MiB blocks for example).
    * Example: `pvcreate /dev/vdb1 /dev/vdb2`
* `vgcreate` - create a volume group (functional equivalent of a hard disk) out of physical volumes. 
    * Example: `vgcreate vg01 /dev/vdb2 /dev/vdb1`
* `lvcreate` - creates a new logical volume from the available physical extents in a volume group.
    * `-n` sets the LV name.
    * `-L` sets LV size in bytes.
    * `-l` sets size in extents.
    * Example: `lvcreate -n lv01 -L 700M vg01`

The logical volume device file can be found as `/dev/vgname/lvname`. You can format the LV like so:

`mkfs -t xfs /dev/vg01/lv01`

LVM also provides commands for moving logical volumes, volume groups, and physical volumes.

* `lvremove` - deletes the specified logical volume.
    * Example: `lvremove /dev/vg01/lv01`
* `vgremove` - deletes the specified volume group.
    * Example: `vgremove vg01`
* `pvremove` - removes physical columes that are no longer needed.
    * Example: `pvremove /dev/vdb2 /dev/vdb1`

LVM also provides commands for displaying information about logical volumes, volume groups, and physical volumes.

* `pvdisplay` - displays information about specified physical volume.
* `vgdisplay` - displays information about specified volume group.
* `lvdisplay` - displays information about specified logical volume.

## Chapter 12.3 - Extending Logical Volumes

You can add more disk space to a volume group by adding additional physical volumes (extending). You can then assign the new physical extents from the physical volumes to logical volumes.

1. Create a new physical volume with `pvcreate`.
1. Use the `vgextend` command to add the new physical volume to the volume group.
    * Example: `vgextend vg01 /dev/vdb3`
1. Verify that the space is now available with `vgdisplay`.

You can reduce a volume group by performing the following steps:

1. Use `pvmove` to relocate any physical extents from the physical volume you want to remove to other physical volumes in the volume group.
    * Example: `pvmove /dev/vdb3`
1. Use `vgreduce` to now actually reduce the volume group. This removes the specified PV from the specified VG.
    * Example: `vgreduce vg01 /dev/sdb3`

### Extending a Logical Volume and XFS File System

One benefit of logical volumes is the ability to increase their size without experiencing downtime. Free physical extents in a volume group can be added to a logical volume to extend its capacity, which can then by used to extend the file system it contains.

Perform the below steps to extend a logical volume:

1. Use `vgdisplay` to verify that there are sufficient physical extents available.
1. Use `lvextend` the logical volume to a new size.
    * Example: `lvextend -L +300M /dev/vg01/lv01`
    * `-L` option expects bytes.
    * `-l` option expects extents.
1. Use `xfs_growfs` to expand the file system to occupy the extended LV.
    * `xfs_growfs /mnt/data`

### Extending a Logical Volume and ext4 File System

Use the same steps for extending a XFS file system, but instead use the `resize2fs` command (instead of `xfs_growfs`).

`resize2fs /dev/vg01/lv01`

### Extending a logical volume and swap space

1. Verify the volume group has space available with `vgdisplay`.
1. Deactivate the swap space.
    * Example: `swapoff -v /dev/vg01/lv02`
1. Extend the logical volume.
    * Example: `lvextend -p +300M /dev/vg01/lv01`
1. Format the logical volume as swap space.
    * Example: `mkswap /dev/vg01/lv01`
1. Activate the swap space.
    * Example: `swapon -va /dev/vg01/lv02`

## Chapter 12.5 - Managing Layered Storage with Stratis

## Chapter 12.7 - Compressing and Decuplicating Storage with VDO

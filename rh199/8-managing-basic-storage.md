## Section 8.1 - Mounting and Unmounting Filesystems

* `mount` - Allows the `root` user to mount a filesystem. The target filesystem can be referenced by it's dev node name or by it's UUID (universally unique identifier) (mount UUID="").
* `umount` - Unmounts target filesystem.
* `lsblk` - Lists the details of a specific block device or all the available devices.
  * `-fp` Lists devices with UUID.
* `lsof` - Lists all open file and the process accessing them in the provided directory. It is useful to identify which processes currently prevent the file system from unmounting.

## Section 8.3 - Adding Partitions, File Systems, and Persistent Mounts

Disk partitioning allows system administrators to divide a hard drive into multiple logical storage units.

* MBR Partitioning Scheme - For BIOS systems. Supports maximum of four primary partitions, as well as an extended and logical partitions (max of 15 partitions). Maximum disk and partition size of 2 TiB.
* GPT Partitioning Scheme - FOR UEFI systems. Supports maximum of 128 partitions. Supports partitions and disks of up to 8 billion tebibytes.

### Managing Partitions with Parted

* `parted` - Used for managing partitions for both the MBR and GPT partitioning schemes. Takes the device name of the whole disk as an argument.

#### Writing the Partition Table on a New Disk

To write a MBR to a disk:

`parted /dev/vdb mklabel msdos`

To write a GPT disk label:

`parted /dev/vdb mklabel gpt`

#### Creating MBR Partitions

1. Specify the disk device to create the partition on:

`parted /dev/sdb`

1. Use the `mkpart` subcommand to create a new primary or extended partition. Enter "primary" or "extended"

`(parted) mkpart`

1. Indicate the file-system type that you want to create on the partition, such as `xfs` or `ext4`. To get a list of available types:

`parted /dev/vdb help mkpart`

1. Specify the sector on the disk that the new partition starts on. You can use s (sector), MiB, GiB, TiB, MB, GB, or TB suffixes.

`Start? 2048s`

1. Specify the disk sector where the new partition should end. Use the "Size = End - Start" formula to calculate the size of the partition.

`End? 1000MB`

1. Exit `parted`:

`(parted) quit`

1. Run the `udevadm settle` command. This command waits for the system to detect the new partition and to create the associated device file under the `/dev` directory. 

As an alternative to interactive mode, the above steps can be turned into a one-liner:

`parted /dev/vdb mkpart primary xfs 2048s 1000MB`

#### Creating GPT Partitions

This is roughly the same steps as creating MBR partitions. The main different being that you will be prompted for a partition name after running `mkpart`.

#### Deleting Partitions

1. Specify the disk that contains the partition to be removed.

`parted /dev/vdb`

2. Identify the partition number of the partition to delete.

`(parted) print`

3. Delete the partition.

`(parted) rm 1`

4. Exit parted.

`(parted) quit`

### Creating File Systems

After the creation of a block device, the next step is to add a file s ystem to it. The two most popular file system types are `xfs` and `ext4`.

To create a `xfs` file system:

`mkfs.xfs /dev/vdb1`

To create a `ext4` file system:

`mkfs.ext4 /dev/vdb1`

### Persistently Mounting File Sytems

* `/etc/fstab` - A configuration file which lists the file systems to mount at system boot. After editing, use `systemctl daemon-reload` to register the new configuration to `systemd`. Each entry is broken into 6 fields.

1. The device (UUID or node name).
1. Mount point.
1. Filesystem type.
1. Comma seperated list of options.
1. The `fsck` order field.

### Section 8.5 - Managing Swap Space

The `swap` space is an area of the disk under the control of the Linux kernel memory management subsystem. The kernel uses swap space to supplement the system RAM by holding inactive pages of memory. The combined system RAM plus swap space is called `virtual memory`.

When memory usage on a system exceeds a defined limit, the kernel searches through RAM looking for idle memory pages assigned to processes. The kernel writes the idle pages to the swap area and reassigns the RAM pages to other processes.

#### Sizing the Swap Space

The below table outlines common swap sizing:

| RAM | Swap Space | Swap Space if Allowing for Hibernation |
|-|-|-|
| 2 GiB of less | Twice the RAM | Three times the RAM |
| Between 2 GiB and 8 GiB | Same as RAM | Twice as RAM |
| Between 8 GiB and 64 Gib | At least 4 GiB | 1.5 times the RAM |
| More than 64 GiB | At least 4 GiB | Hibernation is not recommended |

#### Creating Swap Space

Use the `parted` utility to prepare a partition with a file system type of `linux-swap`.

1. `parted /dev/sdb`
1. `(parted) mkpart`
1. `udevadm settle`

#### Formatting The Device

Use the `mkswap` command to format a partition as swap space.

`mkswap /dev/vdb2`

#### Activating Swap Space

Use the `swapon` command to activate a formatted swap space.

`swapon /dev/sdb2`

#### Activating Swap Space Persistently

Swap can be added to the `/etc/fstab` for persistence.

`UUID=39e2667a-9458-42fe-9665-c5c854605881   swap   swap   defaults   0 0`


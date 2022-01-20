# Determining Hardware Configuration Requirements and Formatting Options

**Table of Contents**

- [Determining Hardware Configuration Requirements and Formatting Options](#determining-hardware-configuration-requirements-and-formatting-options)
  * [Hardware Considerations](#hardware-considerations)
    + [MGT and MDT Storage Hardware Considerations](#mgt-and-mdt-storage-hardware-considerations)
    + [OST Storage Hardware Considerations](#ost-storage-hardware-considerations)
  * [Determining Space Requirements](#determining-space-requirements)
    + [Determining MGT Space Requirements](#determining-mgt-space-requirements)
    + [Determining MDT Space Requirements](#determining-mdt-space-requirements)
    + [Determining OST Space Requirements](#determining-ost-space-requirements)
  * [Setting ldiskfs File System Formatting Options](#setting-ldiskfs-file-system-formatting-options)
    + [Setting Formatting Options for an ldiskfs MDT](#setting-formatting-options-for-an-ldiskfs-mdt)
    + [Setting Formatting Options for an ldiskfs OST](#setting-formatting-options-for-an-ldiskfs-ost)
  * [File and File System Limits](#file-and-file-system-limits)
  * [Determining Memory Requirements](#determining-memory-requirements)
    + [Client Memory Requirements](#client-memory-requirements)
    + [MDS Memory Requirements](#mds-memory-requirements)
      - [Calculating MDS Memory Requirements](#calculating-mds-memory-requirements)
    + [OSS Memory Requirements](#oss-memory-requirements)
      - [Calculating OSS Memory Requirements](#calculating-oss-memory-requirements)
  * [Implementing Networks To Be Used by the Lustre File System](#implementing-networks-to-be-used-by-the-lustre-file-system)
  
  

This chapter describes hardware configuration requirements for a Lustre file system including:

- [the section called “ Hardware Considerations”](#hardware-considerations)
- [the section called “ Determining Space Requirements”](#determining-space-requirements)
- [the section called “ Setting ldiskfs File System Formatting Options ”](#setting-ldiskfs-file-system-formatting-options)
- [the section called “Determining Memory Requirements”](#determining-memory-requirements)
- [the section called “Implementing Networks To Be Used by the Lustre File System”](#implementing-networks-to-be-used-by-the-lustre-file-system)

## Hardware Considerations

- [MGT and MDT Storage Hardware Considerations](#mgt-and-mdt-storage-hardware-considerations)
- [OST Storage Hardware Considerations](#ost-storage-hardware-considerations)

A Lustre file system can utilize any kind of block storage device such as single disks, software RAID, hardware RAID, or a logical volume manager. In contrast to some networked file systems, the block devices are only attached to the MDS and OSS nodes in a Lustre file system and are not accessed by the clients directly.

Since the block devices are accessed by only one or two server nodes, a storage area network (SAN) that is accessible from all the servers is not required. Expensive switches are not needed because point-to-point connections between the servers and the storage arrays normally provide the simplest and best attachments. (If failover capability is desired, the storage must be attached to multiple servers.)

For a production environment, it is preferable that the MGS have separate storage to allow future expansion to multiple file systems. However, it is possible to run the MDS and MGS on the same machine and have them share the same storage device.

For best performance in a production environment, dedicated clients are required. For a non-production Lustre environment or for testing, a Lustre client and server can run on the same machine. However, dedicated clients are the only supported configuration.

### Warning

Performance and recovery issues can occur if you put a client on an MDS or OSS:

- Running the OSS and a client on the same machine can cause issues with low memory and memory pressure. If the client consumes all the memory and then tries to write data to the file system, the OSS will need to allocate pages to receive data from the client but will not be able to perform this operation due to low memory. This can cause the client to hang.
- Running the MDS and a client on the same machine can cause recovery and deadlock issues and impact the performance of other Lustre clients.

Only servers running on 64-bit CPUs are tested and supported. 64-bit CPU clients are typically used for testing to match expected customer usage and avoid limitations due to the 4 GB limit for RAM size, 1 GB low-memory limitation, and 16 TB file size limit of 32-bit CPUs. Also, due to kernel API limitations, performing backups of Lustre filesystems on 32-bit clients may cause backup tools to confuse files that report the same 32-bit inode number, if the backup tools depend on the inode number for correct operation.

The storage attached to the servers typically uses RAID to provide fault tolerance and can optionally be organized with logical volume management (LVM), which is then formatted as a Lustre file system. Lustre OSS and MDS servers read, write and modify data in the format imposed by the file system.

The Lustre file system uses journaling file system technology on both the MDTs and OSTs. For a MDT, as much as a 20 percent performance gain can be obtained by placing the journal on a separate device.

The MDS can effectively utilize a lot of CPU cycles. A minimum of four processor cores are recommended. More are advisable for files systems with many clients.

### Note

Lustre clients running on different CPU architectures is supported. One limitation is that the PAGE_SIZE kernel macro on the client must be as large as the PAGE_SIZE of the server. In particular, ARM or PPC clients with large pages (up to 64kB pages) can run with x86 servers (4kB pages).

### MGT and MDT Storage Hardware Considerations

MGT storage requirements are small (less than 100 MB even in the largest Lustre file systems), and the data on an MGT is only accessed on a server/client mount, so disk performance is not a consideration. However, this data is vital for file system access, so the MGT should be reliable storage, preferably mirrored RAID1.

MDS storage is accessed in a database-like access pattern with many seeks and read-and-writes of small amounts of data. Storage types that provide much lower seek times, such as SSD or NVMe is strongly preferred for the MDT, and high-RPM SAS is acceptable.

For maximum performance, the MDT should be configured as RAID1 with an internal journal and two disks from different controllers.

If you need a larger MDT, create multiple RAID1 devices from pairs of disks, and then make a RAID0 array of the RAID1 devices. For ZFS, use `mirror` VDEVs for the MDT. This ensures maximum reliability because multiple disk failures only have a small chance of hitting both disks in the same RAID1 device.

Doing the opposite (RAID1 of a pair of RAID0 devices) has a 50% chance that even two disk failures can cause the loss of the whole MDT device. The first failure disables an entire half of the mirror and the second failure has a 50% chance of disabling the remaining mirror.

Introduced in Lustre 2.4If multiple MDTs are going to be present in the system, each MDT should be specified for the anticipated usage and load. For details on how to add additional MDTs to the filesystem, see [the section called “Adding a New MDT to a Lustre File System”](03.03-Lustre%20Maintenance.md#adding-a-new-mdt-to-a-lustre-file-system).

Introduced in Lustre 2.4

### Warning

MDT0000 contains the root of the Lustre file system. If MDT0000 is unavailable for any reason, the file system cannot be used.

### Note

Using the DNE feature it is possible to dedicate additional MDTs to sub-directories off the file system root directory stored on MDT0000, or arbitrarily for lower-level subdirectories. using the `lfs mkdir -i mdt_index` command. If an MDT serving a subdirectory becomes unavailable, any subdirectories on that MDT and all directories beneath it will also become inaccessible. This is typically useful for top-level directories to assign different users or projects to separate MDTs, or to distribute other large working sets of files to multiple MDTs.

Introduced in Lustre 2.8

### Note

Starting in the 2.8 release it is possible to spread a single large directory across multiple MDTs using the DNE striped directory feature by specifying multiple stripes (or shards) at creation time using the `lfs mkdir -c stripe_count` command, where *stripe_count* is often the number of MDTs in the filesystem. Striped directories should typically not be used for all directories in the filesystem, since this incurs extra overhead compared to non-striped directories, but is useful for larger directories (over 50k entries) where many output files are being created at one time.

### OST Storage Hardware Considerations

The data access pattern for the OSS storage is a streaming I/O pattern that is dependent on the access patterns of applications being used. Each OSS can manage multiple object storage targets (OSTs), one for each volume with I/O traffic load-balanced between servers and targets. An OSS should be configured to have a balance between the network bandwidth and the attached storage bandwidth to prevent bottlenecks in the I/O path. Depending on the server hardware, an OSS typically serves between 2 and 8 targets, with each target between 24-48TB, but may be up to 256 terabytes (TBs) in size.

Lustre file system capacity is the sum of the capacities provided by the targets. For example, 64 OSSs, each with two 8 TB OSTs, provide a file system with a capacity of nearly 1 PB. If each OST uses ten 1 TB SATA disks (8 data disks plus 2 parity disks in a RAID-6 configuration), it may be possible to get 50 MB/sec from each drive, providing up to 400 MB/sec of disk bandwidth per OST. If this system is used as storage backend with a system network, such as the InfiniBand network, that provides a similar bandwidth, then each OSS could provide 800 MB/sec of end-to-end I/O throughput. (Although the architectural constraints described here are simple, in practice it takes careful hardware selection, benchmarking and integration to obtain such results.)

## Determining Space Requirements

The desired performance characteristics of the backing file systems on the MDT and OSTs are independent of one another. The size of the MDT backing file system depends on the number of inodes needed in the total Lustre file system, while the aggregate OST space depends on the total amount of data stored on the file system. If MGS data is to be stored on the MDT device (co-located MGT and MDT), add 100 MB to the required size estimate for the MDT.

Each time a file is created on a Lustre file system, it consumes one inode on the MDT and one OST object over which the file is striped. Normally, each file's stripe count is based on the system-wide default stripe count. However, this can be changed for individual files using the `lfs setstripe` option. For more details, see [*Managing File Layout (Striping) and Free Space*](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md).

In a Lustre ldiskfs file system, all the MDT inodes and OST objects are allocated when the file system is first formatted. When the file system is in use and a file is created, metadata associated with that file is stored in one of the pre-allocated inodes and does not consume any of the free space used to store file data. The total number of inodes on a formatted ldiskfs MDT or OST cannot be easily changed. Thus, the number of inodes created at format time should be generous enough to anticipate near term expected usage, with some room for growth without the effort of additional storage.

By default, the ldiskfs file system used by Lustre servers to store user-data objects and system data reserves 5% of space that cannot be used by the Lustre file system. Additionally, an ldiskfs Lustre file system reserves up to 400 MB on each OST, and up to 4GB on each MDT for journal use and a small amount of space outside the journal to store accounting data. This reserved space is unusable for general storage. Thus, at least this much space will be used per OST before any file object data is saved.

Introduced in Lustre 2.4

With a ZFS backing filesystem for the MDT or OST, the space allocation for inodes and file data is dynamic, and inodes are allocated as needed. A minimum of 4kB of usable space (before mirroring) is needed for each inode, exclusive of other overhead such as directories, internal log files, extended attributes, ACLs, etc. ZFS also reserves approximately 3% of the total storage space for internal and redundant metadata, which is not usable by Lustre. Since the size of extended attributes and ACLs is highly dependent on kernel versions and site-specific policies, it is best to over-estimate the amount of space needed for the desired number of inodes, and any excess space will be utilized to store more inodes.

### Determining MGT Space Requirements

Less than 100 MB of space is typically required for the MGT. The size is determined by the total number of servers in the Lustre file system cluster(s) that are managed by the MGS.

### Determining MDT Space Requirements

When calculating the MDT size, the important factor to consider is the number of files to be stored in the file system, which depends on at least 2 KiB per inode of usable space on the MDT. Since MDTs typically use RAID-1+0 mirroring, the total storage needed will be double this.

Please note that the actual used space per MDT depends on the number of files per directory, the number of stripes per file, whether files have ACLs or user xattrs, and the number of hard links per file. The storage required for Lustre file system metadata is typically 1-2 percent of the total file system capacity depending upon file size. If the [*Data on MDT (DoM)*](03.09-Data%20on%20MDT%20(DoM).md) feature is in use for Lustre 2.11 or later, MDT space should typically be 5 percent or more of the total space, depending on the distribution of small files within the filesystem and the `lod.*.dom_stripesize` limit on the MDT and file layout used.

For ZFS-based MDT filesystems, the number of inodes created on the MDT and OST is dynamic, so there is less need to determine the number of inodes in advance, though there still needs to be some thought given to the total MDT space compared to the total filesystem size.

For example, if the average file size is 5 MiB and you have 100 TiB of usable OST space, then you can calculate the *minimum*total number of inodes for MDTs and OSTs as follows:

(500 TB * 1000000 MB/TB) / 5 MB/inode = 100M inodes

It is recommended that the MDT(s) have at least twice the minimum number of inodes to allow for future expansion and allow for an average file size smaller than expected. Thus, the minimum space for ldiskfs MDT(s) should be approximately:

2 KiB/inode x 100 million inodes x 2 = 400 GiB ldiskfs MDT

For details about formatting options for ldiskfs MDT and OST file systems, see [the section called “Setting Formatting Options for an ldiskfs MDT”](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-formatting-options-for-an-ldiskfs-mdt).

### Note

If the median file size is very small, 4 KB for example, the MDT would use as much space for each file as the space used on the OST, so the use of Data-on-MDT is strongly recommended in that case. The MDT space per inode should be increased correspondingly to account for the extra data space usage for each inode:

6 KiB/inode x 100 million inodes x 2 = 1200 GiB ldiskfs MDT

### Note

If the MDT has too few inodes, this can cause the space on the OSTs to be inaccessible since no new files can be created. In this case, the `lfs df -i`and `df -i` commands will limit the number of available inodes reported for the filesystem to match the total number of available objects on the OSTs. Be sure to determine the appropriate MDT size needed to support the filesystem before formatting. It is possible to increase the number of inodes after the file system is formatted, depending on the storage. For ldiskfs MDT filesystems the `resize2fs` tool can be used if the underlying block device is on a LVM logical volume and the underlying logical volume size can be increased. For ZFS new (mirrored) VDEVs can be added to the MDT pool to increase the total space available for inode storage. Inodes will be added approximately in proportion to space added.

Introduced in Lustre 2.4

### Note

Note that the number of total and free inodes reported by `lfs df -i` for ZFS MDTs and OSTs is estimated based on the current average space used per inode. When a ZFS filesystem is first formatted, this free inode estimate will be very conservative (low) due to the high ratio of directories to regular files created for internal Lustre metadata storage, but this estimate will improve as more files are created by regular users and the average file size will better reflect actual site usage.

Introduced in Lustre 2.4

### Note

Using the DNE remote directory feature it is possible to increase the total number of inodes of a Lustre filesystem, as well as increasing the aggregate metadata performance, by configuring additional MDTs into the filesystem, see[the section called “Adding a New MDT to a Lustre File System”](03.03-Lustre%20Maintenance.md#adding-a-new-mdt-to-a-lustre-file-system) for details.

### Determining OST Space Requirements

For the OST, the amount of space taken by each object depends on the usage pattern of the users/applications running on the system. The Lustre software defaults to a conservative estimate for the average object size (between 64 KiB per object for 10 GiB OSTs, and 1 MiB per object for 16 TiB and larger OSTs). If you are confident that the average file size for your applications will be different than this, you can specify a different average file size (number of total inodes for a given OST size) to reduce file system overhead and minimize file system check time. See [the section called “Setting Formatting Options for an ldiskfs OST”](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-formatting-options-for-an-ldiskfs-ost) for more details.

## Setting ldiskfs File System Formatting Options

By default, the `mkfs.lustre` utility applies these options to the Lustre backing file system used to store data and metadata in order to enhance Lustre file system performance and scalability. These options include:

- `flex_bg` - When the flag is set to enable this flexible-block-groups feature, block and inode bitmaps for multiple groups are aggregated to minimize seeking when bitmaps are read or written and to reduce read/modify/write operations on typical RAID storage (with 1 MiB RAID stripe widths). This flag is enabled on both OST and MDT file systems. On MDT file systems the `flex_bg` factor is left at the default value of 16. On OSTs, the `flex_bg` factor is set to 256 to allow all of the block or inode bitmaps in a single `flex_bg` to be read or written in a single 1MiB I/O typical for RAID storage.
- `huge_file` - Setting this flag allows files on OSTs to be larger than 2 TiB in size.
- `lazy_journal_init` - This extended option is enabled to prevent a full overwrite to zero out the large journal that is allocated by default in a Lustre file system (up to 400 MiB for OSTs, up to 4GiB for MDTs), to reduce the formatting time.

To override the default formatting options, use arguments to`mkfs.lustre` to pass formatting options to the backing file system:

```
--mkfsoptions='backing fs options'
```

For other `mkfs.lustre` options, see the Linux man page for`mke2fs(8)`.

### Setting Formatting Options for an ldiskfs MDT

The number of inodes on the MDT is determined at format time based on the total size of the file system to be created. The default bytes-per-inode ratio ("inode ratio") for an ldiskfs MDT is optimized at one inode for every 2560 bytes of file system space.

This setting takes into account the space needed for additional ldiskfs filesystem-wide metadata, such as the journal (up to 4 GB), bitmaps, and directories, as well as files that Lustre uses internally to maintain cluster consistency. There is additional per-file metadata such as file layout for files with a large number of stripes, Access Control Lists (ACLs), and user extended attributes.

Starting in Lustre 2.11, the [*Data on MDT (DoM)*](03.09-Data%20on%20MDT%20(DoM).md)  (DoM) feature allows storing small files on the MDT to take advantage of high-performance flash storage, as well as reduce space and network overhead. If you are planning to use the DoM feature with an ldiskfs MDT, it is recommended to increase the bytes-per-inode ratio to have enough space on the MDT for small files, as described below.

It is possible to change the recommended default of 2560 bytes per inode for an ldiskfs MDT when it is first formatted by adding the `--mkfsoptions="-i bytes-per-inode"` option to `mkfs.lustre`. Decreasing the inode ratio tunable `bytes-per-inode` will create more inodes for a given MDT size, but will leave less space for extra per-file metadata and is not recommended. The inode ratio must always be strictly larger than the MDT inode size, which is 1024 bytes by default. It is recommended to use an inode ratio at least 1536 bytes larger than the inode size to ensure the MDT does not run out of space. Increasing the inode ratio with enough space for the most commonly file size (e.g. 5632 or 66560 bytes if 4KB or 64KB files are widely used) is recommended for DoM.

The size of the inode may be changed  at format time by adding the `--stripe-count-hint=N` to have `mkfs.lustre` automatically calculate a reasonable inode size based on the default stripe count that will be used by the filesystem, or directly by specifying the `--mkfsoptions="-I inode-size"` option. Increasing the inode size will provide more space in the inode for a larger Lustre file layout, ACLs, user and system extended attributes, SELinux and other security labels, and other internal metadata and DoM data. However, if these features or other in-inode xattrs are not needed, the larger inode size may hurt metadata performance as 2x, 4x, or 8x as much data would be read or written for each MDT inode access.

### Setting Formatting Options for an ldiskfs OST

When formatting an OST file system, it can be beneficial to take local file system usage into account, for example by running `df` and `df -i` on a current filesystem to get the used bytes and used inodes respectively, then computing the average bytes-per-inode value. When deciding on the ratio for a new filesystem, try to avoid having too many inodes on each OST, while keeping enough margin to allow for future usage of smaller files. This helps reduce the format and e2fsck time and makes more space available for data.

The table below shows the default bytes-per-inode ratio ("inode ratio") used for OSTs of various sizes when they are formatted.

##### Table 3. Default Inode Ratios Used for Newly Formatted OSTs

| **LUN/OST size** | **Default Inode ratio** | **Total inodes** |
| ---------------- | ----------------------- | ---------------- |
| under 10GiB      | 1 inode/16KiB           | 640 - 655k       |
| 10GiB - 1TiB     | 1 inode/68KiB           | 153k - 15.7M     |
| 1TiB - 8TiB      | 1 inode/256KiB          | 4.2M - 33.6M     |
| over 8TiB        | 1 inode/1MiB            | 8.4M - 268M      |

In environments with few small files, the default inode ratio may result in far too many inodes for the average file size. In this case, performance can be improved by increasing the number ofbytes-per-inode. To set the inode ratio, use the `--mkfsoptions="-i *bytes-per-inode*"` argument to `mkfs.lustre`to specify the expected average (mean) size of OST objects. For example, to create an OST with an expected average object size of 8 MiB run:

```
[oss#] mkfs.lustre --ost --mkfsoptions="-i $((8192 * 1024))" ...
```

### Note

OSTs formatted with ldiskfs should preferably have fewer than 320 million objects per MDT, and up to a maximum of 4 billion inodes. Specifying a very small bytes-per-inode ratio for a large OST that exceeds this limit can cause either premature out-of-space errors and prevent the full OST space from being used, or will waste space and slow down e2fsck more than necessary. The default inode ratios are chosen to ensure the total number of inodes remain below this limit.

### Note

File system check time on OSTs is affected by a number of variables in addition to the number of inodes, including the size of the file system, the number of allocated blocks, the distribution of allocated blocks on the disk, disk speed, CPU speed, and the amount of RAM on the server. Reasonable file system check times for valid filesystems are 5-30 minutes per TiB, but may increase significantly if substantial errors are detected and need to be repaired.

For further details about optimizing MDT and OST file systems, see [the section called “ Formatting Options for ldiskfs RAID Devices”](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#formatting-options-for-ldiskfs-raid-devices).

## File and File System Limits

[Table 4, “File and file system limits”](#table-4-file-and-file-system-limits) describes current known limits of Lustre. These limits may be imposed by either the Lustre architecture or the Linux virtual file system (VFS) and virtual memory subsystems. In a few cases, a limit is defined within the code Lustre based on tested values and could be changed by editing and re-compiling the Lustre software. In these cases, the indicated limit was used for testing of the Lustre software.

##### Table 4. File and file system limits

| **Limit**                                                    | **Value**                                                    | **Description**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Maximum number of MDTs                                       | 256                                                          | A single MDS can host one or more MDTs, either for separate filesystems, or aggregated into a single namespace. Each filesystem requires a separate MDT for the filesystem root directory. Up to 255 more MDTs can be added to the filesystem and are attached into the filesystem namespace with creation of DNE remote or striped directories. |
| Maximum number of OSTs                                       | 8150                                                         | The maximum number of OSTs is a constant that can be changed at compile time. Lustre file systems with up to 4000 OSTs have been configured in the past. Multiple OST targets can be configured on a single OSS node. |
| Maximum OST size                                             | 1024TiB (ldiskfs), 1024TiB (ZFS)                             | This is not a *hard* limit. Larger OSTs are possible, but most production systems do not typically go beyond the stated limit per OST because Lustre can add capacity and performance with additional OSTs, and having more OSTs improves aggregate I/O performance, minimizes contention, and allows parallel recovery (e2fsck for ldiskfs OSTs, scrub for ZFS OSTs).With 32-bit kernels, due to page cache limits, 16TB is the maximum block device size, which in turn applies to the size of OST. It is **strongly** recommended to run Lustre clients and servers with 64-bit kernels. |
| Maximum number of clients                                    | 131072                                                       | The maximum number of clients is a constant that can be changed at compile time. Up to 30000 clients have been used in production accessing a single filesystem. |
| Maximum size of a single file system                         | 2EiB or larger                                               | Each OST can have a file system up to the "Maximum OST size" limit, and the Maximum number of OSTs can be combined into a single filesystem. |
| Maximum stripe count                                         | 2000                                                         | This limit is imposed by the size of the layout that needs to be stored on disk and sent in RPC requests, but is not a hard limit of the protocol. The number of OSTs in the filesystem can exceed the stripe count, but this is the maximum number of OSTs on which a single file can be striped.<br/>**Note** <br/>Before 2.13, the default for ldiskfs MDTs the maximum stripe count for a single file is limited to 160 OSTs. In order to increase the maximum file stripe count, use `--mkfsoptions="- O ea_inode"` when formatting the MDT, or `use tune2fs -O ea_inode` to enable it after the MDT has been formatted. |
| Maximum stripe size                                          | < 4 GiB                                                      | The amount of data written to each object before moving on to next object. |
| Minimum stripe size                                          | 64 KiB                                                       | Due to the use of 64 KiB PAGE_SIZE on some CPU architectures such as ARM and POWER, the minimum stripe size is 64 KiB so that a single page is not split over multiple servers. |
| Maximum single object size                                   | 16TiB (ldiskfs), 256TiB (ZFS)                                | The amount of data that can be stored in a single object. An object corresponds to a stripe. The ldiskfs limit of 16 TB for a single object applies. For ZFS the limit is the size of the underlying OST. Files can consist of up to 2000 stripes, each stripe can be up to the maximum object size. |
| Maximum file size                                            | 16 TiB on 32-bit systems 31.25 PiB on 64-bit ldiskfs systems, 8EiB on 64-bit ZFS systems | Individual files have a hard limit of nearly 16 TiB on 32-bit systems imposed by the kernel memory subsystem. On 64-bit systems this limit does not exist. Hence, files can be 2^63 bits (8EiB) in size if the backing filesystem can support large enough objects and/or the files are sparse. A single file can have a maximum of 2000 stripes, which gives an upper single file limit of 31.25 PiB for 64-bit ldiskfs systems. The actual amount of data that can be stored in a file depends upon the amount of free space in each OST on which the file is striped. |
| Maximum number of files or subdirectories in a single directory | 10 million files (ldiskfs), 2^48 (ZFS)                       | The Lustre software uses the ldiskfs hashed directory code, which has a limit of at least 600 million files, depending on the length of the file name. The limit on subdirectories is the same as the limit on regular files.<br />**Note**<br />Starting in the 2.8 release it is possible to exceed this limit by striping a single directory over multiple MDTs with the `lfs mkdir -c` command, which increases the single directory limit by a factor of the number of directory stripes used.<br />**Note**<br />Starting in the 2.14 release, the `large_dir` feature of ldiskfs is enabled by default to allow directories with more than 10M entries. In the 2.12 release, the `large_dir` feature was present but not enabled by default. |
| Maximum number of files in the file system                   | 4 billion (ldiskfs), 256 trillion (ZFS) *per MDT*            | The ldiskfs filesystem imposes an upper limit of 4 billion inodes per filesystem. By default, the MDT filesystem is formatted with one inode per 2KB of space, meaning 512 million inodes per TiB of MDT space. This can be increased initially at the time of MDT filesystem creation. For more information, see [*Determining Hardware Configuration Requirements and Formatting Options*](#determining-hardware-configuration-requirements-and-formatting-options).                                                                                            The ZFS filesystem dynamically allocates inodes and does not have a fixed ratio of inodes per unit of MDT space, but consumes approximately 4KiB of mirrored space per inode, depending on the configuration.<br/>Each additional MDT can hold up to the above maximum number of additional files, depending on available space and the distribution directories and files in the filesystem. |
| Maximum length of a filename                                 | 255 bytes (filename)                                         | This limit is 255 bytes for a single filename, the same as the limit in the underlying filesystems. |
| Maximum length of a pathname                                 | 4096 bytes (pathname)                                        | The Linux VFS imposes a full pathname length of 4096 bytes.  |
| Maximum number of open files for a Lustre file system        | No limit                                                     | The Lustre software does not impose a maximum for the number of open files, but the practical limit depends on the amount of RAM on the MDS. No "tables" for open files exist on the MDS, as they are only linked in a list to a given client's export. Each client process has a limit of several thousands of open files which depends on its ulimit. |

## Determining Memory Requirements

This section describes the memory requirements for each Lustre file system component.

### Client Memory Requirements

A minimum of 2 GB RAM is recommended for clients.

### MDS Memory Requirements

MDS memory requirements are determined by the following factors:

- Number of clients
- Size of the directories
- Load placed on server

The amount of memory used by the MDS is a function of how many clients are on the system, and how many files they are using in their working set. This is driven, primarily, by the number of locks a client can hold at one time. The number of locks held by clients varies by load and memory availability on the server. Interactive clients can hold in excess of 10,000 locks at times. On the MDS, memory usage is approximately 2 KB per file, including the Lustre distributed lock manager (LDLM) lock and kernel data structures for the files currently in use. Having file data in cache can improve metadata performance by a factor of 10x or more compared to reading it from storage.

MDS memory requirements include:

- **File system metadata** : A reasonable amount of RAM needs to be available for file system metadata. While no hard limit can be placed on the amount of file system metadata, if more RAM is available, then the disk I/O is needed less often to retrieve the metadata.
- **Network transport** : If you are using TCP or other network transport that uses system memory for send/ receive buffers, this memory requirement must also be taken into consideration.
- **Journal size** : By default, the journal size is 4096 MB for each MDT ldiskfs file system. This can pin up to an equal amount of RAM on the MDS node per file system.
- **Failover configuration** : If the MDS node will be used for failover from another node, then the RAM for each journal should be doubled, so the backup server can handle the additional load if the primary server fails.

#### Calculating MDS Memory Requirements

By default, 4096 MB are used for the ldiskfs filesystem journal. Additional RAM is used for caching file data for the larger working set, which is not actively in use by clients but should be kept "hot" for improved access times. Approximately 1.5 KB per file is needed to keep a file in cache without a lock. 

For example, for a single MDT on an MDS with 1,024 compute nodes, 12 interactive login nodes, and a 20 million file working set (of which 9 million files are cached on the clients at one time): 

Operating system overhead = 4096 MB (RHEL8) 

File system journal = 4096 MB 

1024 * 32-core clients * 256 files/core * 2KB = 16384 MB 

12 interactive clients * 100,000 files * 2KB = 2400 MB 

20 million file working set * 1.5KB/file = 30720 MB

Thus, a reasonable MDS configuration for this workload is at least 60 GB of RAM. For active-active DNE MDT failover pairs, each MDS should have at least 96 GB of RAM. The additional memory can be used during normal operation to allow more metadata and locks to be cached and improve performance, depending on the workload. 

For directories containing 1 million or more files, more memory can provide a significant benefit. For example, in an environment where clients randomly a single directory with 10 million files can consume as much as 35GB of RAM on the MDS.

### OSS Memory Requirements

When planning the hardware for an OSS node, consider the memory usage of several components in the Lustre file system (i.e., journal, service threads, file system metadata, etc.). Also, consider the effect of the OSS read cache feature, which consumes memory as it caches data on the OSS node.

In addition to the MDS memory requirements mentioned in above, the OSS requirements also include:

- **Service threads** : The service threads on the OSS node pre-allocate an RPC-sized MB I/O buffer for each `ost_io` service thread, so these large buffers do not need to be allocated and freed for each I/O request.
- **OSS read cache** :  OSS read cache provides read-only caching of data on an HDD-based OSS, using the regular Linux page cache to store the data. Just like caching from a regular file system in the Linux operating system, OSS read cache uses as much physical memory as is available.

The same calculation applies to files accessed from the OSS as for the MDS, but the load is typically distributed over more OSS nodes, so the amount of memory required for locks, inode cache, etc. listed for the MDS is spread out over the OSS nodes.

Because of these memory requirements, the following calculations should be taken as determining the minimum RAM required in an OSS node.

#### Calculating OSS Memory Requirements

The minimum recommended RAM size for an OSS with eight OSTs, handling objects for 1/4 of the active files for the MDS:

Network send/receive buffers (16 MB * 512 threads) = 8192 MB 

1024 MB ldiskfs journal size * 8 OST devices = 8192 MB 

16 MB read/write buffer per OST IO thread * 512 threads = 8192 MB 

2048 MB file system read cache * 8 OSTs = 16384 MB 

1024 * 32-core clients * 64 objects/core * 2KB/object = 4096 MB 

12 interactive clients * 25,000 objects * 2KB/object = 600 MB 

5 million object working set * 1.5KB/object = 7500 MB 

For a non-failover configuration, the minimum RAM would be about 60 GB for an OSS node with eight OSTs. Additional memory on the OSS will improve the performance of reading smaller, frequently-accessed files. 

For a failover configuration, the minimum RAM would be about 90 GB, as some of the memory is pernode. When the OSS is not handling any failed-over OSTs the extra RAM will be used as a read cache. 

As a reasonable rule of thumb, about 24 GB of base memory plus 4 GB per OST can be used. In failover configurations, about 8 GB per primary OST is needed.

## Implementing Networks To Be Used by the Lustre File System

As a high performance file system, the Lustre file system places heavy loads on networks. Thus, a network interface in each Lustre server and client is commonly dedicated to Lustre file system traffic. This is often a dedicated TCP/IP subnet, although other network hardware can also be used.

A typical Lustre file system implementation may include the following:

- A high-performance backend network for the Lustre servers, typically an InfiniBand (IB) network.
- A larger client network.
- Lustre routers to connect the two networks.

Lustre networks and routing are configured and managed by specifying parameters to the Lustre Networking (`lnet`) module in`/etc/modprobe.d/lustre.conf`.

To prepare to configure Lustre networking, complete the following steps:

1. **Identify all machines that will be running Lustre software and the network interfaces they will use to run Lustre file system traffic. These machines will form the Lustre network .**

   A network is a group of nodes that communicate directly with one another. The Lustre software includes Lustre network drivers (LNDs) to support a variety of network types and hardware (see [*Understanding Lustre Networking (LNet)*](02-Introducing%20the%20Lustre%20File%20System.md#understanding-lustre-networking-lnet) for a complete list). The standard rules for specifying networks applies to Lustre networks. For example, two TCP networks on two different subnets (`tcp0` and `tcp1`) are considered to be two different Lustre networks.

2. **If routing is needed, identify the nodes to be used to route traffic between networks.**

   If you are using multiple network types, then you will need a router. Any node with appropriate interfaces can route Lustre networking (LNet) traffic between different network hardware types or topologies --the node may be a server, a client, or a standalone router. LNet can route messages between different network types (such as TCP-to-InfiniBand) or across different topologies (such as bridging two InfiniBand or TCP/IP networks). Routing will be configured in [*Configuring Lustre Networking (LNet)*](02.06-Configuring%20Lustre%20Networking%20(LNet).md).

3. **Identify the network interfaces to include in or exclude from LNet.**

   If not explicitly specified, LNet uses either the first available interface or a pre-defined default for a given network type. Interfaces that LNet should not use (such as an administrative network or IP-over-IB), can be excluded.

   Network interfaces to be used or excluded will be specified using the lnet kernel module parameters `networks` and`ip2nets` as described in [*Configuring Lustre Networking (LNet)*](02.06-Configuring%20Lustre%20Networking%20(LNet).md).

4. **To ease the setup of networks with complex network configurations, determine a cluster-wide module configuration.**

   For large clusters, you can configure the networking setup for all nodes by using a single, unified set of parameters in the `lustre.conf` file on each node. Cluster-wide configuration is described in [*Configuring Lustre Networking (LNet)*](02.06-Configuring%20Lustre%20Networking%20(LNet).md).

### Note

We recommend that you use 'dotted-quad' notation for IP addresses rather than host names to make it easier to read debug logs and debug configurations with multiple interfaces.
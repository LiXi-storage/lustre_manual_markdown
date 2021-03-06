# Configuring Storage on a Lustre File System

**Table of Contents**

- [Configuring Storage on a Lustre File System](#configuring-storage-on-a-lustre-file-system)
    * [Selecting Storage for the MDT and OSTs](#selecting-storage-for-the-mdt-and-osts)
      * [Metadata Target (MDT)](#metadata-target-mdt)
      * [Object Storage Server (OST)](#object-storage-server-ost)
  * [Reliability Best Practices](#reliability-best-practices)
  * [Performance Tradeoffs](#performance-tradeoffs)
  * [Formatting Options for ldiskfs RAID Devices](#formatting-options-for-ldiskfs-raid-devices)
    + [Computing file system parameters for mkfs](#computing-file-system-parameters-for-mkfs)
    + [Choosing Parameters for an External Journal](#choosing-parameters-for-an-external-journal)
  * [Connecting a SAN to a Lustre File System](#connecting-a-san-to-a-lustre-file-system)

This chapter describes best practices for storage selection and file system options to optimize performance on RAID, and includes the following sections:

- [the section called “ Selecting Storage for the MDT and OSTs”](#selecting-storage-for-the-mdt-and-osts)
- [the section called “Reliability Best Practices”](#reliability-best-practices)
- [the section called “Performance Tradeoffs”](#performance-tradeoffs)
- [the section called “ Formatting Options for ldiskfs RAID Devices”](#formatting-options-for-ldiskfs-raid-devices)
- [the section called “Connecting a SAN to a Lustre File System”](#connecting-a-san-to-a-lustre-file-system)

### Note

**It is strongly recommended that storage used in a Lustre file system be configured with hardware RAID.** The Lustre software does not support redundancy at the file system level and RAID is required to protect against disk failure.

## Selecting Storage for the MDT and OSTs

The Lustre architecture allows the use of any kind of block device as backend storage. The characteristics of such devices, particularly in the case of failures, vary significantly and have an impact on configuration choices.

This section describes issues and recommendations regarding backend storage.

### Metadata Target (MDT)

I/O on the MDT is typically mostly reads and writes of small amounts of data. For this reason, we recommend that you use RAID 1 for MDT storage. If you require more capacity for an MDT than one disk provides, we recommend RAID 1 + 0 or RAID 10.

### Object Storage Server (OST)

A quick calculation makes it clear that without further redundancy, RAID 6 is required for large clusters and RAID 5 is not acceptable:

> For a 2 PB file system (2,000 disks of 1 TB capacity) assume the mean time to failure (MTTF) of a disk is about 1,000 days. This means that the expected failure rate is 2000/1000 = 2 disks per day. Repair time at 10% of disk bandwidth is 1000 GB at 10MB/sec = 100,000 sec, or about 1 day.
>
> For a RAID 5 stripe that is 10 disks wide, during 1 day of rebuilding, the chance that a second disk in the same array will fail is about 9/1000 or about 1% per day. After 50 days, you have a 50% chance of a double failure in a RAID 5 array leading to data loss.
>
> Therefore, RAID 6 or another double parity algorithm is needed to provide sufficient redundancy for OST storage.

For better performance, we recommend that you create RAID sets with 4 or 8 data disks plus one or two parity disks. Using larger RAID sets will negatively impact performance compared to having multiple independent RAID sets.

To maximize performance for small I/O request sizes, storage configured as RAID 1+0 can yield much better results but will increase cost or reduce capacity.

## Reliability Best Practices

RAID monitoring software is recommended to quickly detect faulty disks and allow them to be replaced to avoid double failures and data loss. Hot spare disks are recommended so that rebuilds happen without delays.

Backups of the metadata file systems are recommended. For details, see [*Backing Up and Restoring a File System*](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md).

## Performance Tradeoffs

A writeback cache in a RAID storage controller can dramatically increase write performance on many types of RAID arrays if the writes are not done at full stripe width. Unfortunately, unless the RAID array has battery-backed cache (a feature only found in some higher-priced hardware RAID arrays), interrupting the power to the array may result in out-of-sequence or lost writes, and corruption of RAID parity and/ or filesystem metadata, resulting in data loss.

Having a read or writeback cache onboard a PCI adapter card installed in an MDS or OSS is NOT SAFE in a high-availability (HA) failover configuration, as this will result in inconsistencies between nodes and immediate or eventual filesystem corruption. Such devices should not be used, or should have the onboard cache disabled.

If writeback cache is enabled, a file system check is required after the array loses power. Data may also be lost because of this.

Therefore, we recommend against the use of writeback cache when data integrity is critical. You should carefully consider whether the benefits of using writeback cache outweigh the risks.

## Formatting Options for ldiskfs RAID Devices

When formatting an ldiskfs file system on a RAID device, it can be beneficial to ensure that I/O requests are aligned with the underlying RAID geometry. This ensures that Lustre RPCs do not generate unnecessary disk operations which may reduce performance dramatically. Use the `--mkfsoptions` parameter to specify additional parameters when formatting the OST or MDT.

For RAID 5, RAID 6, or RAID 1+0 storage, specifying the following option to the `--mkfsoptions` parameter option improves the layout of the file system metadata, ensuring that no single disk contains all of the allocation bitmaps:

```
-E stride = chunk_blocks 
```

The `*chunk_blocks*` variable is in units of 4096-byte blocks and represents the amount of contiguous data written to a single disk before moving to the next disk. This is alternately referred to as the RAID stripe size. This is applicable to both MDT and OST file systems.

For more information on how to override the defaults while formatting MDT or OST file systems, see [the section called “ Setting ldiskfs File System Formatting Options ”](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-ldiskfs-file-system-formatting-options).

### Computing file system parameters for mkfs

For best results, use RAID 5 with 5 or 9 disks or RAID 6 with 6 or 10 disks, each on a different controller. The stripe width is the optimal minimum I/O size. Ideally, the RAID configuration should allow 1 MB Lustre RPCs to fit evenly on a single RAID stripe without an expensive read-modify-write cycle. Use this formula to determine the `*stripe_width*`, where`*number_of_data_disks*` does *not* include the RAID parity disks (1 for RAID 5 and 2 for RAID 6):

```
stripe_width_blocks = chunk_blocks * number_of_data_disks = 1 MB 
```

If the RAID configuration does not allow `*chunk_blocks*` to fit evenly into 1 MB, select `*stripe_width_blocks*`, such that is close to 1 MB, but not larger.

The `*stripe_width_blocks*` value must equal `*chunk_blocks* * *number_of_data_disks*`. Specifying the `*stripe_width_blocks*` parameter is only relevant for RAID 5 or RAID 6, and is not needed for RAID 1 plus 0.

Run `--reformat` on the file system device (`/dev/sdc`), specifying the RAID geometry to the underlying ldiskfs file system, where:

```
--mkfsoptions "other_options -E stride=chunk_blocks, stripe_width=stripe_width_blocks"
```

A RAID 6 configuration with 6 disks has 4 data and 2 parity disks. The`*chunk_blocks*` <= 1024KB/4 = 256KB.

Because the number of data disks is equal to the power of 2, the stripe width is equal to 1 MB.

```
--mkfsoptions "other_options -E stride=chunk_blocks, stripe_width=stripe_width_blocks"...
```

### Choosing Parameters for an External Journal

If you have configured a RAID array and use it directly as an OST, it contains both data and metadata. For better performance, we recommend putting the OST journal on a separate device, by creating a small RAID 1 array and using it as an external journal for the OST.

In a typical Lustre file system, the default OST journal size is up to 1GB, and the default MDT journal size is up to 4GB, in order to handle a high transaction rate without blocking on journal flushes. Additionally, a copy of the journal is kept in RAM. Therefore, make sure you have enough RAM on the servers to hold copies of all journals.

The file system journal options are specified to `mkfs.lustre` using the `--mkfsoptions` parameter. For example:

```
--mkfsoptions "other_options -j -J device=/dev/mdJ" 
```

To create an external journal, perform these steps for each OST on the OSS:

1. Create a 400 MB (or larger) journal partition (RAID 1 is recommended).

   In this example, `/dev/sdb` is a RAID 1 device.

2. Create a journal device on the partition. Run:

   ```
   oss# mke2fs -b 4096 -O journal_dev /dev/sdb journal_size
   ```

   The value of `*journal_size*` is specified in units of 4096-byte blocks. For example, 262144 for a 1 GB journal size.

3. Create the OST.

   In this example, `/dev/sdc` is the RAID 6 device to be used as the OST, run:

   ```
   [oss#] mkfs.lustre --ost ... \
   --mkfsoptions="-J device=/dev/sdb1" /dev/sdc
   ```

4. Mount the OST as usual.

## Connecting a SAN to a Lustre File System

Depending on your cluster size and workload, you may want to connect a SAN to a Lustre file system. Before making this connection, consider the following:

- In many SAN file systems, clients allocate and lock blocks or inodes individually as they are updated. The design of the Lustre file system avoids the high contention that some of these blocks and inodes may have.
- The Lustre file system is highly scalable and can have a very large number of clients. SAN switches do not scale to a large number of nodes, and the cost per port of a SAN is generally higher than other networking.
- File systems that allow direct-to-SAN access from the clients have a security risk because clients can potentially read any data on the SAN disks, and misbehaving clients can corrupt the file system for many reasons like improper file system, network, or other kernel software, bad cabling, bad memory, and so on. The risk increases with increase in the number of clients directly accessing the storage.
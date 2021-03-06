# Configuring a Lustre File System

**Table of Contents**

- [Configuring a Lustre File System](#configuring-a-lustre-file-system)
  * [Configuring a Simple Lustre File System](#configuring-a-simple-lustre-file-system)
    + [Simple Lustre Configuration Example](#simple-lustre-configuration-example)
  * [Additional Configuration Options](#additional-configuration-options)
    + [Scaling the Lustre File System](#scaling-the-lustre-file-system)
    + [Changing Striping Defaults](#changing-striping-defaults)
    + [Using the Lustre Configuration Utilities](#using-the-lustre-configuration-utilities)

This chapter shows how to configure a simple Lustre file system comprised of a combined MGS/MDT, an OST and a client. It includes:

- [the section called “ Configuring a Simple Lustre File System”](#configuring-a-simple-lustre-file-system)
- [the section called “ Additional Configuration Options”](#additional-configuration-options)

## Configuring a Simple Lustre File System

A Lustre file system can be set up in a variety of configurations by using the administrative utilities provided with the Lustre software. The procedure below shows how to configure a simple Lustre file system consisting of a combined MGS/MDS, one OSS with two OSTs, and a client. For an overview of the entire Lustre installation procedure, see [*Installation Overview*](02.01-Installation%20Overview.md).

This configuration procedure assumes you have completed the following:

- **\*Set up and configured your hardware*** . For more information about hardware requirements, see [*Determining Hardware Configuration Requirements and Formatting Options*](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md).
- **Downloaded and installed the Lustre software.**For more information about preparing for and installing the Lustre software, see [*Installing the Lustre Software*](02.05-Installing%20the%20Lustre%20Software.md).

The following optional steps should also be completed, if needed, before the Lustre software is configured:

- *Set up a hardware or software RAID on block devices to be used as OSTs or MDTs.*For information about setting up RAID, see the documentation for your RAID controller or [*Configuring Storage on a Lustre File System*](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md).
- *Set up network interface bonding on Ethernet interfaces.*For information about setting up network interface bonding, see [*Setting Up Network Interface Bonding*](02.04-Setting%20Up%20Network%20Interface%20Bonding.md).
- *Set* lnet *module parameters to specify how Lustre Networking (LNet) is to be configured to work with a Lustre file system and test the LNet configuration.*LNet will, by default, use the first TCP/IP interface it discovers on a system. If this network configuration is sufficient, you do not need to configure LNet. LNet configuration is required if you are using InfiniBand or multiple Ethernet interfaces.

For information about configuring LNet, see [*Configuring Lustre Networking (LNet)*](02.06-Configuring%20Lustre%20Networking%20(LNet).md). For information about testing LNet, see [*Testing Lustre Network Performance (LNet Self-Test)*](04.01-Testing%20Lustre%20Network%20Performance%20(LNet%20Self-Test).md).

- *Run the benchmark script sgpdd-survey to determine baseline performance of your hardware.*Benchmarking your hardware will simplify debugging performance issues that are unrelated to the Lustre software and ensure you are getting the best possible performance with your installation. For information about running `sgpdd-survey`, see [*Benchmarking Lustre File System Performance (Lustre I/O Kit)*](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md).

### Note

The `sgpdd-survey` script overwrites the device being tested so it must be run before the OSTs are configured.

To configure a simple Lustre file system, complete these steps:

1. Create a combined MGS/MDT file system on a block device. On the MDS node, run:

   ```
   mkfs.lustre --fsname=
   fsname --mgs --mdt --index=0 
   /dev/block_device
   ```

   The default file system name ( `fsname`) is `lustre`.

   ### Note

   If you plan to create multiple file systems, the MGS should be created separately on its own dedicated block device, by running:

   ```
   mkfs.lustre --fsname=
   fsname --mgs 
   /dev/block_device
   ```

   See [the section called “ Running Multiple Lustre File Systems”](03.02-Lustre%20Operations.md#running-multiple-lustre-file-systems)for more details.

2. Optionally add in additional MDTs.

   ```
   mkfs.lustre --fsname=
   fsname --mgsnode=
   nid --mdt --index=1 
   /dev/block_device
   ```

   ### Note

   Up to 4095 additional MDTs can be added.

3. Mount the combined MGS/MDT file system on the block device. On the MDS node, run:

   ```
   mount -t lustre 
   /dev/block_device 
   /mount_point
   ```

   ### Note

   If you have created an MGS and an MDT on separate block devices, mount them both.

4. Create the OST. On the OSS node, run:

   ```
   mkfs.lustre --fsname=
   fsname --mgsnode=
   MGS_NID --ost --index=
   OST_index 
   /dev/block_device
   ```

   When you create an OST, you are formatting a `ldiskfs` or `ZFS` file system on a block storage device like you would with any local file system.

   You can have as many OSTs per OSS as the hardware or drivers allow. For more information about storage and memory requirements for a Lustre file system, see [*Determining Hardware Configuration Requirements and Formatting Options*](c02-02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md).

   You can only configure one OST per block device. You should create an OST that uses the raw block device and does not use partitioning.

   You should specify the OST index number at format time in order to simplify translating the OST number in error messages or file striping to the OSS node and block device later on.

   If you are using block devices that are accessible from multiple OSS nodes, ensure that you mount the OSTs from only one OSS node at at time. It is strongly recommended that multiple-mount protection be enabled for such devices to prevent serious data corruption. For more information about multiple-mount protection, see [*Lustre File System Failover and Multiple-Mount Protection*](03.13-Lustre%20File%20System%20Failover%20and%20Multiple-Mount%20Protection.md).

   ### Note

   The Lustre software currently supports block devices up to 128 TB on Red Hat Enterprise Linux 5 and 6 (up to 8 TB on other distributions). If the device size is only slightly larger that 16 TB, it is recommended that you limit the file system size to 16 TB at format time. We recommend that you not place DOS partitions on top of RAID 5/6 block devices due to negative impacts on performance, but instead format the whole disk for the file system.

5. Mount the OST. On the OSS node where the OST was created, run:

   ```
   mount -t lustre 
   /dev/block_device 
   /mount_point
   ```

   ### Note

   To create additional OSTs, repeat Step [4](#configuring-a-simple-lustre-file-system)and Step [5](#simple-lustre-configuration-example), specifying the next higher OST index number.

6. Mount the Lustre file system on the client. On the client node, run:

   ```
   mount -t lustre 
   MGS_node:/
   fsname 
   /mount_point 
   ```

   ### Note

   To mount the filesystem on additional clients, repeat Step [6](#configuring-a-simple-lustre-file-system).

   ### Note

   If you have a problem mounting the file system, check the syslogs on the client and all the servers for errors and also check the network settings. A common issue with newly-installed systems is that `hosts.deny` or firewall rules may prevent connections on port 988.

7. Verify that the file system started and is working correctly. Do this by running`lfs df`, `dd` and `ls` commands on the client node.

8. *(Optional)*Run benchmarking tools to validate the performance of hardware and software layers in the cluster. Available tools include:

   - `obdfilter-survey`- Characterizes the storage performance of a Lustre file system. For details, see [the section called “Testing OST Performance (`obdfilter-survey`)”](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-ost-performance-obdfilter-survey).
   - `ost-survey`- Performs I/O against OSTs to detect anomalies between otherwise identical disk subsystems. For details, see [the section called “Testing OST I/O Performance (`ost-survey`)”](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-ost-io-performance-ost-survey).

### Simple Lustre Configuration Example

To see the steps to complete for a simple Lustre file system configuration, follow this example in which a combined MGS/MDT and two OSTs are created to form a file system called `temp`. Three block devices are used, one for the combined MGS/MDS node and one for each OSS node. Common parameters used in the example are listed below, along with individual node parameters.

| **Common Parameters** | **Value**        | **Description** |                                                |
| --------------------- | ---------------- | --------------- | ---------------------------------------------- |
|                       | **MGS node**     | `10.2.0.1@tcp0` | Node for the combined MGS/MDS                  |
|                       | **file system**  | `temp`          | Name of the Lustre file system                 |
|                       | **network type** | `TCP/IP`        | Network type used for Lustre file system`temp` |

| **Node Parameters** | **Value**        | **Description** |                                                              |
| ------------------- | ---------------- | --------------- | ------------------------------------------------------------ |
| MGS/MDS node        |                  |                 |                                                              |
|                     | **MGS/MDS node** | `mdt0`          | MDS in Lustre file system `temp`                             |
|                     | **block device** | `/dev/sdb`      | Block device for the combined MGS/MDS node                   |
|                     | **mount point**  | `/mnt/mdt`      | Mount point for the `mdt0` block device ( `/dev/sdb`) on the MGS/MDS node |
| First OSS node      |                  |                 |                                                              |
|                     | **OSS node**     | `oss0`          | First OSS node in Lustre file system `temp`                  |
|                     | **OST**          | `ost0`          | First OST in Lustre file system `temp`                       |
|                     | **block device** | `/dev/sdc`      | Block device for the first OSS node ( `oss0`)                |
|                     | **mount point**  | `/mnt/ost0`     | Mount point for the `ost0` block device ( `/dev/sdc`) on the `oss1` node |
| Second OSS node     |                  |                 |                                                              |
|                     | **OSS node**     | `oss1`          | Second OSS node in Lustre file system `temp`                 |
|                     | **OST**          | `ost1`          | Second OST in Lustre file system `temp`                      |
|                     | **block device** | `/dev/sdd`      | Block device for the second OSS node (oss1)                  |
|                     | **mount point**  | `/mnt/ost1`     | Mount point for the `ost1` block device ( `/dev/sdd`) on the `oss1` node |
| Client node         |                  |                 |                                                              |
|                     | **client node**  | `client1`       | Client in Lustre file system `temp`                          |
|                     | **mount point**  | `/lustre`       | Mount point for Lustre file system `temp` on the`client1` node |

### Note

We recommend that you use 'dotted-quad' notation for IP addresses rather than host names to make it easier to read debug logs and debug configurations with multiple interfaces.

For this example, complete the steps below:

1. Create a combined MGS/MDT file system on the block device. On the MDS node, run:

   ```
   [root@mds /]# mkfs.lustre --fsname=temp --mgs --mdt --index=0 /dev/sdb
   ```

   This command generates this output:

   ```
       Permanent disk data:
   Target:            temp-MDT0000
   Index:             0
   Lustre FS: temp
   Mount type:        ldiskfs
   Flags:             0x75
      (MDT MGS first_time update )
   Persistent mount opts: errors=remount-ro,iopen_nopriv,user_xattr
   Parameters: mdt.identity_upcall=/usr/sbin/l_getidentity
    
   checking for existing Lustre data: not found
   device size = 16MB
   2 6 18
   formatting backing filesystem ldiskfs on /dev/sdb
      target name             temp-MDTffff
      4k blocks               0
      options                 -i 4096 -I 512 -q -O dir_index,uninit_groups -F
   mkfs_cmd = mkfs.ext2 -j -b 4096 -L temp-MDTffff  -i 4096 -I 512 -q -O 
   dir_index,uninit_groups -F /dev/sdb
   Writing CONFIGS/mountdata 
   ```

2. Mount the combined MGS/MDT file system on the block device. On the MDS node, run:

   ```
   [root@mds /]# mount -t lustre /dev/sdb /mnt/mdt
   ```

   This command generates this output:

   ```
   Lustre: temp-MDT0000: new disk, initializing 
   Lustre: 3009:0:(lproc_mds.c:262:lprocfs_wr_identity_upcall()) temp-MDT0000:
   group upcall set to /usr/sbin/l_getidentity
   Lustre: temp-MDT0000.mdt: set parameter identity_upcall=/usr/sbin/l_getidentity
   Lustre: Server temp-MDT0000 on device /dev/sdb has started 
   ```

3. Create and mount `ost0`.

   In this example, the OSTs ( `ost0` and `ost1`) are being created on different OSS nodes ( `oss0` and `oss1` respectively).

   1. Create `ost0`. On `oss0` node, run:

      ```
      [root@oss0 /]# mkfs.lustre --fsname=temp --mgsnode=10.2.0.1@tcp0 --ost
      --index=0 /dev/sdc
      ```

      The command generates this output:

      ```
          Permanent disk data:
      Target:            temp-OST0000
      Index:             0
      Lustre FS: temp
      Mount type:        ldiskfs
      Flags:             0x72
      (OST first_time update)
      Persistent mount opts: errors=remount-ro,extents,mballoc
      Parameters: mgsnode=10.2.0.1@tcp
       
      checking for existing Lustre data: not found
      device size = 16MB
      2 6 18
      formatting backing filesystem ldiskfs on /dev/sdc
         target name             temp-OST0000
         4k blocks               0
         options                 -I 256 -q -O dir_index,uninit_groups -F
      mkfs_cmd = mkfs.ext2 -j -b 4096 -L temp-OST0000  -I 256 -q -O
      dir_index,uninit_groups -F /dev/sdc
      Writing CONFIGS/mountdata 
      ```

   2. Mount ost0 on the OSS on which it was created. On `oss0` node, run:

      ```
      root@oss0 /] mount -t lustre /dev/sdc /mnt/ost0
      ```

      The command generates this output:

      ```
      LDISKFS-fs: file extents enabled 
      LDISKFS-fs: mballoc enabled
      Lustre: temp-OST0000: new disk, initializing
      Lustre: Server temp-OST0000 on device /dev/sdb has started
      ```

      Shortly afterwards, this output appears:

      ```
      Lustre: temp-OST0000: received MDS connection from 10.2.0.1@tcp0
      Lustre: MDS temp-MDT0000: temp-OST0000_UUID now active, resetting orphans 
      ```

4. Create and mount `ost1`.

   1. Create ost1. On `oss1` node, run:

      ```
      [root@oss1 /]# mkfs.lustre --fsname=temp --mgsnode=10.2.0.1@tcp0 \
                 --ost --index=1 /dev/sdd
      ```

      The command generates this output:

      ```
          Permanent disk data:
      Target:            temp-OST0001
      Index:             1
      Lustre FS: temp
      Mount type:        ldiskfs
      Flags:             0x72
      (OST first_time update)
      Persistent mount opts: errors=remount-ro,extents,mballoc
      Parameters: mgsnode=10.2.0.1@tcp
       
      checking for existing Lustre data: not found
      device size = 16MB
      2 6 18
      formatting backing filesystem ldiskfs on /dev/sdd
         target name             temp-OST0001
         4k blocks               0
         options                 -I 256 -q -O dir_index,uninit_groups -F
      mkfs_cmd = mkfs.ext2 -j -b 4096 -L temp-OST0001  -I 256 -q -O
      dir_index,uninit_groups -F /dev/sdc
      Writing CONFIGS/mountdata 
      ```

   2. Mount ost1 on the OSS on which it was created. On `oss1` node, run:

      ```
      root@oss1 /] mount -t lustre /dev/sdd /mnt/ost1 
      ```

      The command generates this output:

      ```
      LDISKFS-fs: file extents enabled 
      LDISKFS-fs: mballoc enabled
      Lustre: temp-OST0001: new disk, initializing
      Lustre: Server temp-OST0001 on device /dev/sdb has started
      ```

      Shortly afterwards, this output appears:

      ```
      Lustre: temp-OST0001: received MDS connection from 10.2.0.1@tcp0
      Lustre: MDS temp-MDT0000: temp-OST0001_UUID now active, resetting orphans 
      ```

5. Mount the Lustre file system on the client. On the client node, run:

   ```
   root@client1 /] mount -t lustre 10.2.0.1@tcp0:/temp /lustre 
   ```

   This command generates this output:

   ```
   Lustre: Client temp-client has started
   ```

6. Verify that the file system started and is working by running the `df`, `dd` and `ls`commands on the client node.

   1. Run the `lfs df -h` command:

      ```
      [root@client1 /] lfs df -h 
      ```

      The `lfs df -h` command lists space usage per OST and the MDT in human-readable format. This command generates output similar to this:

      ```
      UUID               bytes      Used      Available   Use%    Mounted on
      temp-MDT0000_UUID  8.0G      400.0M       7.6G        0%      /lustre[MDT:0]
      temp-OST0000_UUID  800.0G    400.0M     799.6G        0%      /lustre[OST:0]
      temp-OST0001_UUID  800.0G    400.0M     799.6G        0%      /lustre[OST:1]
      filesystem summary:  1.6T    800.0M       1.6T        0%      /lustre
      ```

   2. Run the `lfs df -ih` command.

      ```
      [root@client1 /] lfs df -ih
      ```

      The `lfs df -ih` command lists inode usage per OST and the MDT. This command generates output similar to this:

      ```
      UUID              Inodes      IUsed       IFree   IUse%     Mounted on
      temp-MDT0000_UUID   2.5M        32         2.5M      0%       /lustre[MDT:0]
      temp-OST0000_UUID   5.5M        54         5.5M      0%       /lustre[OST:0]
      temp-OST0001_UUID   5.5M        54         5.5M      0%       /lustre[OST:1]
      filesystem summary: 2.5M        32         2.5M      0%       /lustre
      ```

   3. Run the `dd` command:

      ```
      [root@client1 /] cd /lustre
      [root@client1 /lustre] dd if=/dev/zero of=/lustre/zero.dat bs=4M count=2
      ```

      The `dd` command verifies write functionality by creating a file containing all zeros ( `0`s). In this command, an 8 MB file is created. This command generates output similar to this:

      ```
      2+0 records in
      2+0 records out
      8388608 bytes (8.4 MB) copied, 0.159628 seconds, 52.6 MB/s
      ```

   4. Run the `ls` command:

      ```
      [root@client1 /lustre] ls -lsah
      ```

      The `ls -lsah` command lists files and directories in the current working directory. This command generates output similar to this:

      ```
      total 8.0M
      4.0K drwxr-xr-x  2 root root 4.0K Oct 16 15:27 .
      8.0K drwxr-xr-x 25 root root 4.0K Oct 16 15:27 ..
      8.0M -rw-r--r--  1 root root 8.0M Oct 16 15:27 zero.dat 
       
      ```

Once the Lustre file system is configured, it is ready for use.

## Additional Configuration Options

This section describes how to scale the Lustre file system or make configuration changes using the Lustre configuration utilities.

### Scaling the Lustre File System

A Lustre file system can be scaled by adding OSTs or clients. For instructions on creating additional OSTs repeat Step and Step above. For mounting additional clients, repeat Step for each client.

### Changing Striping Defaults

The default settings for the file layout stripe pattern are shown in [Table 8, “Default stripe pattern”](#table-8-default-stripe-pattern).

**Table 8. Default stripe pattern**

| **File Layout Parameter** | **Default** | **Description**                                              |
| ------------------------- | ----------- | ------------------------------------------------------------ |
| `stripe_size`             | 1 MB        | Amount of data to write to one OST before moving to the next OST. |
| `stripe_count`            | 1           | The number of OSTs to use for a single file.                 |
| `start_ost`               | -1          | The first OST where objects are created for each file. The default -1 allows the MDS to choose the starting index based on available space and load balancing. *It's strongly recommended not to change the default for this parameter to a value other than -1.* |



Use the `lfs setstripe` command described in [*Managing File Layout (Striping) and Free Space*](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md)to change the file layout configuration.

### Using the Lustre Configuration Utilities

If additional configuration is necessary, several configuration utilities are available:

- `mkfs.lustre`- Use to format a disk for a Lustre service.
- `tunefs.lustre`- Use to modify configuration information on a Lustre target disk.
- `lctl`- Use to directly control Lustre features via an `ioctl` interface, allowing various configuration, maintenance and debugging features to be accessed.
- `mount.lustre`- Use to start a Lustre client or target service.

For examples using these utilities, see the topic [*System Configuration Utilities*](06.07-System%20Configuration%20Utilities.md)

The `lfs` utility is useful for configuring and querying a variety of options related to files. For more information, see [*User Utilities*](06.03-User%20Utilities.md).

### Note

Some sample scripts are included in the directory where the Lustre software is installed. If you have installed the Lustre source code, the scripts are located in the `lustre/tests` sub-directory. These scripts enable quick setup of some simple standard Lustre configurations.



 
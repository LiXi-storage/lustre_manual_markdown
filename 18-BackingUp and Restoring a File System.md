# Backing Up and Restoring a File System

- [Backing Up and Restoring a File System](#backing-up-and-restoring-a-file-system)
  * [Backing up a File System](#backing-up-a-file-system)
    + [Lustre_rsync](#lustre_rsync)
      - [Using Lustre_rsync](#using-lustre_rsync)
      - [`lustre_rsync` Examples](#lustre_rsync-examples)
  * [Backing Up and Restoring an MDT or OST (ldiskfs Device Level)](#backing-up-and-restoring-an-mdt-or-ost-ldiskfs-device-level)
  * [Backing Up an OST or MDT (Backend File System Level)](#backing-up-an-ost-or-mdt-backend-file-system-level)
    + [Backing Up an OST or MDT (Backend File System Level)](#backing-up-an-ost-or-mdt-backend-file-system-level)L 2.11
    + [Backing Up an OST or MDT](#backing-up-an-ost-or-mdt)
  * [Restoring a File-Level Backup](#restoring-a-file-level-backup)
  * [Using LVM Snapshots with the Lustre File System](#using-lvm-snapshots-with-the-lustre-file-system)
    + [Creating an LVM-based Backup File System](#creating-an-lvm-based-backup-file-system)
    + [Backing up New/Changed Files to the Backup File System](#backing-up-newchanged-files-to-the-backup-file-system)
    + [Creating Snapshot Volumes](#creating-snapshot-volumes)
    + [Restoring the File System From a Snapshot](#restoring-the-file-system-from-a-snapshot)
    + [Deleting Old Snapshots](#deleting-old-snapshots)
    + [Changing Snapshot Volume Size](#changing-snapshot-volume-size)
  * [Migration Between ZFS and ldiskfs Target Filesystems](#migration-between-zfs-and-ldiskfs-target-filesystems)L 2.11
    + [Migrate from a ZFS to an ldiskfs based filesystem](#migrate-from-a-zfs-to-an-ldiskfs-based-filesystem)
    + [Migrate from an ldiskfs to a ZFS based filesystem](#migrate-from-an-ldiskfs-to-a-zfs-based-filesystem)

This chapter describes how to backup and restore at the file system-level, device-level and file-level in a Lustre file system. Each backup approach is described in the the following sections:

* [the section called “ Backing up a File System”](#backing-up-a-file-system)
* [the section called “ Backing Up and Restoring an MDT or OST (ldiskfs Device Level)”](#backing-up-and-restoring-an-mdt-or-ost-ldiskfs-device-level)
* [the section called “ Backing Up an OST or MDT (Backend File System Level)”](#backing-up-an-ost-or-mdt-backend-file-system-level)
* [the section called “ Restoring a File-Level Backup”](#restoring-a-file-level-backup)
* [the section called “ Using LVM Snapshots with the Lustre File System”](#using-lvm-snapshots-with-the-lustre-file-system)

It is *strongly* recommended that sites perform periodic device-level backup of the MDT(s) ([the section called “ Backing Up and Restoring an MDT or OST (ldiskfs Device Level)”](#backing-up-and-restoring-an-mdt-or-ost-ldiskfs-device-level)), for example twice a week with alternate backups going to a separate device, even if there is not enough capacity to do a full backup of all of the filesystem data. Even if there are separate file-level backups of some or all files in the filesystem, having a device-level backup of the MDT can be very useful in case of MDT failure or corruption. Being able to restore a device-level MDT backup can avoid the significantly longer process of restoring the entire filesystem from backup. Since the MDT is required for access to all files, its loss would otherwise force full restore of the filesystem (if that is even possible) even if the OSTs are still OK.

Performing a periodic device-level MDT backup can be done relatively inexpensively because the storage need only be connected to the primary MDS (it can be manually connected to the backup MDS in the rare case it is needed), and only needs good linear read/write performance. While the device-level MDT backup is not useful for restoring individual files, it is most efficient to handle the case of MDT failure or corruption.

## Backing up a File System

Backing up a complete file system gives you full control over the files to back up, and allows restoration of individual files as needed. File system-level backups are also the easiest to integrate into existing backup solutions.

File system backups are performed from a Lustre client (or many clients working parallel in different directories) rather than on individual server nodes; this is no different than backing up any other file system.

However, due to the large size of most Lustre file systems, it is not always possible to get a complete backup. We recommend that you back up subsets of a file system. This includes subdirectories of the entire file system, filesets for a single user, files incremented by date, and so on, so that restores can be done more efficiently.

**Note**

Lustre internally uses a 128-bit file identifier (FID) for all files. To interface with user applications, the 64-bit inode numbers are returned by the `stat()`, `fstat()`, and `readdir()` system calls on 64-bit applications, and 32-bit inode numbers to 32-bit applications.

Some 32-bit applications accessing Lustre file systems (on both 32-bit and 64-bit CPUs) may experience problems with the `stat()`, `fstat()` or `readdir()` system calls under certain circumstances, though the Lustre client should return 32-bit inode numbers to these applications.

In particular, if the Lustre file system is exported from a 64-bit client via NFS to a 32-bit client, the Linux NFS server will export 64-bit inode numbers to applications running on the NFS client. If the 32-bit applications are not compiled with Large File Support (LFS), then they return `EOVERFLOW` errors when accessing the Lustre files. To avoid this problem, Linux NFS clients can use the kernel command-line option "`nfs.enable_ino64=0`" in order to force the NFS client to export 32-bit inode numbers to the client.

**Workaround**: We very strongly recommend that backups using `tar(1)` and other utilities that depend on the inode number to uniquely identify an inode to be run on 64-bit clients. The 128-bit Lustre file identifiers cannot be uniquely mapped to a 32-bit inode number, and as a result these utilities may operate incorrectly on 32-bit clients. While there is still a small chance of inode number collisions with 64-bit inodes, the FID allocation pattern is designed to avoid collisions for long periods of usage.

### Lustre_rsync

The `lustre_rsync` feature keeps the entire file system in sync on a backup by replicating the file system's changes to a second file system (the second file system need not be a Lustre file system, but it must be sufficiently large). `lustre_rsync` uses Lustre changelogs to efficiently synchronize the file systems without having to scan (directory walk) the Lustre file system. This efficiency is critically important for large file systems, and distinguishes the Lustre `lustre_rsync` feature from other replication/backup solutions.

#### Using Lustre_rsync

The `lustre_rsync` feature works by periodically running `lustre_rsync`, a userspace program used to synchronize changes in the Lustre file system onto the target file system. The `lustre_rsync` utility keeps a status file, which enables it to be safely interrupted and restarted without losing synchronization between the file systems.

The first time that `lustre_rsync` is run, the user must specify a set of parameters for the program to use. These parameters are described in the following table and in [the section called “ lustre_rsync”](06.07-System%20Configuration%20Utilities.md#lustre-rsync). On subsequent runs, these parameters are stored in the the status file, and only the name of the status file needs to be passed to `lustre_rsync`.

Before using `lustre_rsync`:

- Register the changelog user. For details, see the [*System Configuration Utilities*](06.07-System%20Configuration%20Utilities.md)( `changelog_register`) parameter in the [*System Configuration Utilities*](06.07-System%20Configuration%20Utilities.md)( `lctl`).

\- AND -

- Verify that the Lustre file system (source) and the replica file system (target) are identical *before* registering the changelog user. If the file systems are discrepant, use a utility, e.g. regular `rsync`(not `lustre_rsync`), to make them identical.

The `lustre_rsync` utility uses the following parameters:

| **Parameter**       | **Description**                                              |
| ------------------- | ------------------------------------------------------------ |
| `--source=*src*`    | The path to the root of the Lustre file system (source) which will be synchronized. This is a mandatory option if a valid status log created during a previous synchronization operation ( `--statuslog`) is not specified. |
| `--target=*tgt*`    | The path to the root where the source file system will be synchronized (target). This is a mandatory option if the status log created during a previous synchronization operation ( `--statuslog`) is not specified. This option can be repeated if multiple synchronization targets are desired. |
| `--mdt= *mdt*`      | The metadata device to be synchronized. A changelog user must be registered for this device. This is a mandatory option if a valid status log created during a previous synchronization operation ( `--statuslog`) is not specified. |
| `--user=*userid*`   | The changelog user ID for the specified MDT. To use `lustre_rsync`, the changelog user must be registered. For details, see the `changelog_register` parameter in [*System Configuration Utilities*](06.07-System%20Configuration%20Utilities.md)( `lctl`). This is a mandatory option if a valid status log created during a previous synchronization operation ( `--statuslog`) is not specified. |
| `--statuslog=*log*` | A log file to which synchronization status is saved. When the `lustre_rsync` utility starts, if the status log from a previous synchronization operation is specified, then the state is read from the log and otherwise mandatory `--source`, `--target` and `--mdt` options can be skipped. Specifying the `--source`, `--target` and/or `--mdt` options, in addition to the `--statuslog` option, causes the specified parameters in the status log to be overridden. Command line options take precedence over options in the status log. |
| `--xattr*yes|no*`   | Specifies whether extended attributes ( `xattrs`) are synchronized or not. The default is to synchronize extended attributes.NoteDisabling xattrs causes Lustre striping information not to be synchronized. |
| `--verbose`         | Produces verbose output.                                     |
| `--dry-run`         | Shows the output of `lustre_rsync` commands ( `copy`, `mkdir`, etc.) on the target file system without actually executing them. |
| `--abort-on-err`    | Stops processing the `lustre_rsync` operation if an error occurs. The default is to continue the operation. |

#### `lustre_rsync` Examples

Sample `lustre_rsync` commands are listed below.

Register a changelog user for an MDT (e.g. `testfs-MDT0000`).

```
# lctl --device testfs-MDT0000 changelog_register testfs-MDT0000
Registered changelog userid 'cl1'
```

Synchronize a Lustre file system ( `/mnt/lustre`) to a target file system ( `/mnt/target`).

```
$ lustre_rsync --source=/mnt/lustre --target=/mnt/target \
           --mdt=testfs-MDT0000 --user=cl1 --statuslog sync.log  --verbose 
Lustre filesystem: testfs 
MDT device: testfs-MDT0000 
Source: /mnt/lustre 
Target: /mnt/target 
Statuslog: sync.log 
Changelog registration: cl1 
Starting changelog record: 0 
Errors: 0 
lustre_rsync took 1 seconds 
Changelog records consumed: 22
```

After the file system undergoes changes, synchronize the changes onto the target file system. Only the `statuslog` name needs to be specified, as it has all the parameters passed earlier.

```
$ lustre_rsync --statuslog sync.log --verbose 
Replicating Lustre filesystem: testfs 
MDT device: testfs-MDT0000 
Source: /mnt/lustre 
Target: /mnt/target 
Statuslog: sync.log 
Changelog registration: cl1 
Starting changelog record: 22 
Errors: 0 
lustre_rsync took 2 seconds 
Changelog records consumed: 42
```

To synchronize a Lustre file system ( `/mnt/lustre`) to two target file systems ( `/mnt/target1` and `/mnt/target2`).

```
$ lustre_rsync --source=/mnt/lustre --target=/mnt/target1 \
           --target=/mnt/target2 --mdt=testfs-MDT0000 --user=cl1  \
           --statuslog sync.log
```

## Backing Up and Restoring an MDT or OST (ldiskfs Device Level)

In some cases, it is useful to do a full device-level backup of an individual device (MDT or OST), before replacing hardware, performing maintenance, etc. Doing full device-level backups ensures that all of the data and configuration files is preserved in the original state and is the easiest method of doing a backup. For the MDT file system, it may also be the fastest way to perform the backup and restore, since it can do large streaming read and write operations at the maximum bandwidth of the underlying devices.

**Note**

Keeping an updated full backup of the MDT is especially important because permanent failure or corruption of the MDT file system renders the much larger amount of data in all the OSTs largely inaccessible and unusable. The storage needed for one or two full MDT device backups is much smaller than doing a full filesystem backup, and can use less expensive storage than the actual MDT device(s) since it only needs to have good streaming read/write speed instead of high random IOPS.

If hardware replacement is the reason for the backup or if a spare storage device is available, it is possible to do a raw copy of the MDT or OST from one block device to the other, as long as the new device is at least as large as the original device. To do this, run:

```
dd if=/dev/{original} of=/dev/{newdev} bs=4M
```

If hardware errors cause read problems on the original device, use the command below to allow as much data as possible to be read from the original device while skipping sections of the disk with errors:

```
dd if=/dev/{original} of=/dev/{newdev} bs=4k conv=sync,noerror /
      count={original size in 4kB blocks}
```

Even in the face of hardware errors, the `ldiskfs` file system is very robust and it may be possible to recover the file system data after running `e2fsck -fy /dev/{newdev}` on the new device.

With Lustre software version 2.6 and later,  the `LFSCK` scanning will automatically move objects from `lost+found` back into its correct location on the OST after directory corruption.

In order to ensure that the backup is fully consistent, the MDT or OST must be unmounted, so that there are no changes being made to the device while the data is being transferred. If the reason for the backup is preventative (i.e. MDT backup on a running MDS in case of future failures) then it is possible to perform a consistent backup from an LVM snapshot. If an LVM snapshot is not available, and taking the MDS offline for a backup is unacceptable, it is also possible to perform a backup from the raw MDT block device. While the backup from the raw device will not be fully consistent due to ongoing changes, the vast majority of ldiskfs metadata is statically allocated, and inconsistencies in the backup can be fixed by running `e2fsck` on the backup device, and is still much better than not having any backup at all.

## Backing Up an OST or MDT (Backend File System Level)

This procedure provides an alternative to backup or migrate the data of an OST or MDT at the file level. At the file-level, unused space is omitted from the backup and the process may be completed quicker with a smaller total backup size. Backing up a single OST device is not necessarily the best way to perform backups of the Lustre file system, since the files stored in the backup are not usable without metadata stored on the MDT and additional file stripes that may be on other OSTs. However, it is the preferred method for migration of OST devices, especially when it is desirable to reformat the underlying file system with different configuration options or to reduce fragmentation.

**Note**

Since Lustre stores internal metadata that maps FIDs to local inode numbers via the Object Index file, they need to be rebuilt at first mount after a restore is detected so that file-level MDT backup and restore is supported. The OI Scrub rebuilds these automatically at first mount after a restore is detected, which may affect MDT performance after mount until the rebuild is completed. Progress can be monitored via `lctl get_param osd-*.*.oi_scrub` on the MDS or OSS node where the target filesystem was restored.



Introduced in Lustre 2.11

### Backing Up an OST or MDT (Backend File System Level)

Prior to Lustre software release 2.11.0, we can only do the backend file system level backup and restore process for ldiskfs-based systems. The ability to perform a zfs-based MDT/OST file system level backup and restore is introduced beginning in Lustre software release 2.11.0. Differing from an ldiskfs-based system, index objects must be backed up before the unmount of the target (MDT or OST) in order to be able to restore the file system successfully. To enable index backup on the target, execute the following command on the target server:

```
# lctl set_param osd-*.${fsname}-${target}.index_backup=1
```

*${target}* is composed of the target type (MDT or OST) plus the target index, such as `MDT0000`, `OST0001`, and so on.

**Note**

The index_backup is also valid for an ldiskfs-based system, that will be used when migrating data between ldiskfs-based and zfs-based systems as described in [the section called “ Migration Between ZFS and ldiskfs Target Filesystems ”](#migration-between-zfs-and-ldiskfs-target-filesystems).

### Backing Up an OST or MDT

The below examples show backing up an OST filesystem. When backing up an MDT, substitute `mdt` for `ost` in the instructions below.

1. **Umount the target**

2. **Make a mountpoint for the file system.**

   ```
   [oss]# mkdir -p /mnt/ost
   ```

3. **Mount the file system.**

   For ldiskfs-based systems:

   ```
   [oss]# mount -t ldiskfs /dev/{ostdev} /mnt/ost
   ```

   For zfs-based systems:

   1. Import the pool for the target if it is exported. For example:

      ```
      [oss]# zpool import lustre-ost [-d ${ostdev_dir}]
      ```

      

   2. Enable the `canmount` property on the target filesystem. For example:

      ```
      [oss]# zfs set canmount=on ${fsname}-ost/ost
      ```

      You also can specify the mountpoint property. By default, it will be: `/${fsname}-ost/ost`

   3. Mount the target as 'zfs'. For example:

      ```
      [oss]# zfs mount ${fsname}-ost/ost
      ```

      

4. **Change to the mountpoint being backed up.**

   ```
   [oss]# cd /mnt/ost
   ```

5. **Back up the extended attributes.**

   ```
   [oss]# getfattr -R -d -m '.*' -e hex -P . > ea-$(date +%Y%m%d).bak
   ```

   ### Note

   If the `tar(1)` command supports the `--xattr` option (see below), the `getfattr` step may be unnecessary as long as tar correctly backs up the `trusted.*` attributes. However, completing this step is not harmful and can serve as an added safety measure.

   ### Note

   In most distributions, the `getfattr` command is part of the `attr` package. If the `getfattr` command returns errors like `Operation not supported`, then the kernel does not correctly support EAs. Stop and use a different backup method.

6. **Verify that the ea-$date.bak file has properly backed up the EA data on the OST.**

   Without this attribute data, the MDT restore process will fail and result in an unusable filesystem. The OST restore process may be missing extra data that can be very useful in case of later file system corruption. Look at this file with `more` or a text editor. Each object file should have a corresponding item similar to this:

   ```
   [oss]# file: O/0/d0/100992
   trusted.fid= \
   0x0d822200000000004a8a73e500000000808a0100000000000000000000000000
   ```

7. **Back up all file system data.**

   ```
   [oss]# tar czvf {backup file}.tgz [--xattrs] [--xattrs-include="trusted.*" --sparse .
   ```

   ### Note

   The tar `--sparse` option is vital for backing up an MDT. Very old versions of tar may not support the `--sparse` option correctly, which may cause the MDT backup to take a long time. Known-working versions include the tar from Red Hat Enterprise Linux distribution (RHEL version 6.3 or newer) or GNU tar version 1.25 and newer.

   ### Warning

   The tar `--xattrs` option is only available in GNU tar version 1.27 or later or in RHEL 6.3 or newer. The `--xattrs-include="trusted.*"` option is *required* for correct restoration of the xattrs when using GNU tar 1.27 or RHEL 7 and newer.

8. **Change directory out of the file system.**

   ```
   [oss]# cd -
   ```

9. **Unmount the file system.**

   ```
   [oss]# umount /mnt/ost
   ```

   ### Note

   When restoring an OST backup on a different node as part of an OST migration, you also have to change server NIDs and use the `--writeconf` command to re-generate the configuration logs. See [*Lustre Maintenance*](03.03-Lustre%20Maintenance.md)(Changing a Server NID).

## Restoring a File-Level Backup

To restore data from a file-level backup, you need to format the device, restore the file data and then restore the EA data.

1. Format the new device.

   ```
   [oss]# mkfs.lustre --ost --index {OST index}
   --replace --fstype=${fstype} {other options} /dev/{newdev}
   ```

2. Set the file system label (**ldiskfs-based systems only**).

   ```
   [oss]# e2label {fsname}-OST{index in hex} /mnt/ost
   ```

3. Mount the file system.

   For ldiskfs-based systems:

   ```
   [oss]# mount -t ldiskfs /dev/{newdev} /mnt/ost
   ```

   For zfs-based systems:

   1. Import the pool for the target if it is exported. For example:

      ```
      [oss]# zpool import lustre-ost [-d ${ostdev_dir}]
      ```

   2. Enable the canmount property on the target filesystem. For example:

      ```
      [oss]# zfs set canmount=on ${fsname}-ost/ost
      ```

      You also can specify the `mountpoint` property. By default, it will be: `/${fsname}-ost/ost`

   3. Mount the target as 'zfs'. For example:

      ```
      [oss]# zfs mount ${fsname}-ost/ost
      ```

4. Change to the new file system mount point.

   ```
   [oss]# cd /mnt/ost
   ```

5. Restore the file system backup.

   ```
   [oss]# tar xzvpf {backup file} [--xattrs] [--xattrs-include="trusted.*"] --sparse
   ```

   ### Warning

   The tar `--xattrs` option is only available in GNU tar version 1.27 or later or in RHEL 6.3 or newer. The `--xattrs-include="trusted.*"` option is *required* for correct restoration of the MDT xattrs when using GNU tar 1.27 or RHEL 7 and newer. Otherwise, the `setfattr` step below should be used.

6. If not using a version of tar that supports direct xattr backups, restore the file system extended attributes.

   ```
   [oss]# setfattr --restore=ea-${date}.bak
   ```

   ### Note

   If `--xattrs` option is supported by tar and specified in the step above, this step is redundant.

7. Verify that the extended attributes were restored.

   ```
   [oss]# getfattr -d -m ".*" -e hex O/0/d0/100992 trusted.fid= \
   0x0d822200000000004a8a73e500000000808a0100000000000000000000000000
   ```

8. Remove old OI and LFSCK files.

   ```
   [oss]# rm -rf oi.16* lfsck_* LFSCK
   ```

9. Remove old CATALOGS.

   ```
   [oss]# rm -f CATALOGS
   ```

   ### Note

   This is optional for the MDT side only. The CATALOGS record the llog file handlers that are used for recovering cross-server updates. Before OI scrub rebuilds the OI mappings for the llog files, the related recovery will get a failure if it runs faster than the background OI scrub. This will result in a failure of the whole mount process. OI scrub is an online tool, therefore, a mount failure means that the OI scrub will be stopped. Removing the old CATALOGS will avoid this potential trouble. The side-effect of removing old CATALOGS is that the recovery for related cross-server updates will be aborted. However, this can be handled by LFSCK after the system mount is up.

10. Change directory out of the file system.

    ```
    [oss]# cd -
    ```

11. Unmount the new file system.

    ```
    [oss]# umount /mnt/ost
    ```

    ### Note

    If the restored system has a different NID from the backup system, please change the NID. For detail, please refer to [the section called “ Changing a Server NID”](03.03-Lustre%20Maintenance.md#changing-a-server-nid). For example:

    ```
    [oss]# mount -t lustre -o nosvc ${fsname}-ost/ost /mnt/ost
    [oss]# lctl replace_nids ${fsname}-OSTxxxx $new_nids
    [oss]# umount /mnt/ost
    ```

12. Mount the target as `lustre`.

    Usually, we will use the `-o abort_recov` option to skip unnecessary recovery. For example:

    ```
    [oss]# mount -t lustre -o abort_recov #{fsname}-ost/ost /mnt/ost
    ```

    Lustre can detect the restore automatically when mounting the target, and then trigger OI scrub to rebuild the OIs and index objects asynchronously in the background. You can check the OI scrub status with the following command:

    ```
    [oss]# lctl get_param -n osd-${fstype}.${fsname}-${target}.oi_scrub
    ```

If the file system was used between the time the backup was made and when it was restored, then the online `LFSCK` tool will automatically be run to ensure the filesystem is coherent. If all of the device filesystems were backed up at the same time after Lustre was was stopped, this step is unnecessary. In either case, the filesystem will be immediately although there may be I/O errors reading from files that are present on the MDT but not the OSTs, and files that were created after the MDT backup will not be accessible or visible. See [the section called “ Checking the file system with LFSCK”](05.02-Troubleshooting%20Recovery.md#checking-the-file-system-with-lfsck)for details on using LFSCK.

## Using LVM Snapshots with the Lustre File System

If you want to perform disk-based backups (because, for example, access to the backup system needs to be as fast as to the primary Lustre file system), you can use the Linux LVM snapshot tool to maintain multiple, incremental file system backups.

Because LVM snapshots cost CPU cycles as new files are written, taking snapshots of the main Lustre file system will probably result in unacceptable performance losses. You should create a new, backup Lustre file system and periodically (e.g., nightly) back up new/changed files to it. Periodic snapshots can be taken of this backup file system to create a series of "full" backups.

**Note**

Creating an LVM snapshot is not as reliable as making a separate backup, because the LVM snapshot shares the same disks as the primary MDT device, and depends on the primary MDT device for much of its data. If the primary MDT device becomes corrupted, this may result in the snapshot being corrupted.

### Creating an LVM-based Backup File System

Use this procedure to create a backup Lustre file system for use with the LVM snapshot mechanism.

1. Create LVM volumes for the MDT and OSTs.

   Create LVM devices for your MDT and OST targets. Make sure not to use the entire disk for the targets; save some room for the snapshots. The snapshots start out as 0 size, but grow as you make changes to the current file system. If you expect to change 20% of the file system between backups, the most recent snapshot will be 20% of the target size, the next older one will be 40%, etc. Here is an example:

   ```
   cfs21:~# pvcreate /dev/sda1
      Physical volume "/dev/sda1" successfully created
   cfs21:~# vgcreate vgmain /dev/sda1
      Volume group "vgmain" successfully created
   cfs21:~# lvcreate -L200G -nMDT0 vgmain
      Logical volume "MDT0" created
   cfs21:~# lvcreate -L200G -nOST0 vgmain
      Logical volume "OST0" created
   cfs21:~# lvscan
      ACTIVE                  '/dev/vgmain/MDT0' [200.00 GB] inherit
      ACTIVE                  '/dev/vgmain/OST0' [200.00 GB] inherit
   ```

2. Format the LVM volumes as Lustre targets.

   In this example, the backup file system is called `main` and designates the current, most up-to-date backup.

   ```
   cfs21:~# mkfs.lustre --fsname=main --mdt --index=0 /dev/vgmain/MDT0
    No management node specified, adding MGS to this MDT.
       Permanent disk data:
    Target:     main-MDT0000
    Index:      0
    Lustre FS:  main
    Mount type: ldiskfs
    Flags:      0x75
                  (MDT MGS first_time update )
    Persistent mount opts: errors=remount-ro,iopen_nopriv,user_xattr
    Parameters:
   checking for existing Lustre data
    device size = 200GB
    formatting backing filesystem ldiskfs on /dev/vgmain/MDT0
            target name  main-MDT0000
            4k blocks     0
            options        -i 4096 -I 512 -q -O dir_index -F
    mkfs_cmd = mkfs.ext2 -j -b 4096 -L main-MDT0000  -i 4096 -I 512 -q
     -O dir_index -F /dev/vgmain/MDT0
    Writing CONFIGS/mountdata
   cfs21:~# mkfs.lustre --mgsnode=cfs21 --fsname=main --ost --index=0
   /dev/vgmain/OST0
       Permanent disk data:
    Target:     main-OST0000
    Index:      0
    Lustre FS:  main
    Mount type: ldiskfs
    Flags:      0x72
                  (OST first_time update )
    Persistent mount opts: errors=remount-ro,extents,mballoc
    Parameters: mgsnode=192.168.0.21@tcp
   checking for existing Lustre data
    device size = 200GB
    formatting backing filesystem ldiskfs on /dev/vgmain/OST0
            target name  main-OST0000
            4k blocks     0
            options        -I 256 -q -O dir_index -F
    mkfs_cmd = mkfs.ext2 -j -b 4096 -L lustre-OST0000 -J size=400 -I 256 
     -i 262144 -O extents,uninit_bg,dir_nlink,huge_file,flex_bg -G 256 
     -E resize=4290772992,lazy_journal_init, -F /dev/vgmain/OST0
    Writing CONFIGS/mountdata
   cfs21:~# mount -t lustre /dev/vgmain/MDT0 /mnt/mdt
   cfs21:~# mount -t lustre /dev/vgmain/OST0 /mnt/ost
   cfs21:~# mount -t lustre cfs21:/main /mnt/main
   ```

### Backing up New/Changed Files to the Backup File System

At periodic intervals e.g., nightly, back up new and changed files to the LVM-based backup file system.

```
cfs21:~# cp /etc/passwd /mnt/main 
 
cfs21:~# cp /etc/fstab /mnt/main 
 
cfs21:~# ls /mnt/main 
fstab  passwd
```

### Creating Snapshot Volumes

Whenever you want to make a "checkpoint" of the main Lustre file system, create LVM snapshots of all target MDT and OSTs in the LVM-based backup file system. You must decide the maximum size of a snapshot ahead of time, although you can dynamically change this later. The size of a daily snapshot is dependent on the amount of data changed daily in the main Lustre file system. It is likely that a two-day old snapshot will be twice as big as a one-day old snapshot.

You can create as many snapshots as you have room for in the volume group. If necessary, you can dynamically add disks to the volume group.

The snapshots of the target MDT and OSTs should be taken at the same point in time. Make sure that the cronjob updating the backup file system is not running, since that is the only thing writing to the disks. Here is an example:

```
cfs21:~# modprobe dm-snapshot
cfs21:~# lvcreate -L50M -s -n MDT0.b1 /dev/vgmain/MDT0
   Rounding up size to full physical extent 52.00 MB
   Logical volume "MDT0.b1" created
cfs21:~# lvcreate -L50M -s -n OST0.b1 /dev/vgmain/OST0
   Rounding up size to full physical extent 52.00 MB
   Logical volume "OST0.b1" created
```

After the snapshots are taken, you can continue to back up new/changed files to "main". The snapshots will not contain the new files.

```
cfs21:~# cp /etc/termcap /mnt/main
cfs21:~# ls /mnt/main
fstab  passwd  termcap
```

### Restoring the File System From a Snapshot

Use this procedure to restore the file system from an LVM snapshot.

1. Rename the LVM snapshot.

   Rename the file system snapshot from "main" to "back" so you can mount it without unmounting "main". This is recommended, but not required. Use the `--reformat` flag to `tunefs.lustre` to force the name change. For example:

   ```
   cfs21:~# tunefs.lustre --reformat --fsname=back --writeconf /dev/vgmain/MDT0.b1
    checking for existing Lustre data
    found Lustre data
    Reading CONFIGS/mountdata
   Read previous values:
    Target:     main-MDT0000
    Index:      0
    Lustre FS:  main
    Mount type: ldiskfs
    Flags:      0x5
                 (MDT MGS )
    Persistent mount opts: errors=remount-ro,iopen_nopriv,user_xattr
    Parameters:
   Permanent disk data:
    Target:     back-MDT0000
    Index:      0
    Lustre FS:  back
    Mount type: ldiskfs
    Flags:      0x105
                 (MDT MGS writeconf )
    Persistent mount opts: errors=remount-ro,iopen_nopriv,user_xattr
    Parameters:
   Writing CONFIGS/mountdata
   cfs21:~# tunefs.lustre --reformat --fsname=back --writeconf /dev/vgmain/OST0.b1
    checking for existing Lustre data
    found Lustre data
    Reading CONFIGS/mountdata
   Read previous values:
    Target:     main-OST0000
    Index:      0
    Lustre FS:  main
    Mount type: ldiskfs
    Flags:      0x2
                 (OST )
    Persistent mount opts: errors=remount-ro,extents,mballoc
    Parameters: mgsnode=192.168.0.21@tcp
   Permanent disk data:
    Target:     back-OST0000
    Index:      0
    Lustre FS:  back
    Mount type: ldiskfs
    Flags:      0x102
                 (OST writeconf )
    Persistent mount opts: errors=remount-ro,extents,mballoc
    Parameters: mgsnode=192.168.0.21@tcp
   Writing CONFIGS/mountdata
   ```

   When renaming a file system, we must also erase the last_rcvd file from the snapshots

   ```
   cfs21:~# mount -t ldiskfs /dev/vgmain/MDT0.b1 /mnt/mdtback
   cfs21:~# rm /mnt/mdtback/last_rcvd
   cfs21:~# umount /mnt/mdtback
   cfs21:~# mount -t ldiskfs /dev/vgmain/OST0.b1 /mnt/ostback
   cfs21:~# rm /mnt/ostback/last_rcvd
   cfs21:~# umount /mnt/ostback
   ```

2. Mount the file system from the LVM snapshot. For example:

   ```
   cfs21:~# mount -t lustre /dev/vgmain/MDT0.b1 /mnt/mdtback
   cfs21:~# mount -t lustre /dev/vgmain/OST0.b1 /mnt/ostback
   cfs21:~# mount -t lustre cfs21:/back /mnt/back
   ```

3. Note the old directory contents, as of the snapshot time. For example:

   ```
   cfs21:~/cfs/b1_5/lustre/utils# ls /mnt/back
   fstab  passwds
   ```

### Deleting Old Snapshots

To reclaim disk space, you can erase old snapshots as your backup policy dictates. Run:

```
lvremove /dev/vgmain/MDT0.b1
```

### Changing Snapshot Volume Size

You can also extend or shrink snapshot volumes if you find your daily deltas are smaller or larger than expected. Run:

```
lvextend -L10G /dev/vgmain/MDT0.b1
```

**Note**

Extending snapshots seems to be broken in older LVM. It is working in LVM v2.02.01.



Introduced in Lustre 2.11

## Migration Between ZFS and ldiskfs Target Filesystems

Beginning with Lustre 2.11.0, it is possible to migrate between ZFS and ldiskfs backends. For migrating OSTs, it is best to use `lfs find`/`lfs_migrate` to empty out an OST while the filesystem is in use and then reformat it with the new fstype. For instructions on removing the OST, please see [the section called “Removing an OST from the File System”](03.03-Lustre%20Maintenance.md#removing-an-ost-from-the-file-system).

### Migrate from a ZFS to an ldiskfs based filesystem

The first step of the process is to make a ZFS backend backup using `tar` as described in [the section called “ Backing Up an OST or MDT (Backend File System Level)”](#backing-up-an-ost-or-mdt-backend-file-system-level).

Next, restore the backup to an ldiskfs-based system as described in [the section called “ Restoring a File-Level Backup”](#restoring-a-file-level-backup).

### Migrate from an ldiskfs to a ZFS based filesystem

The first step of the process is to make an ldiskfs backend backup using `tar` as described in [the section called “ Backing Up an OST or MDT (Backend File System Level)”](#backing-up-an-ost-or-mdt-backend-file-system-level).

**Caution:**For a migration from ldiskfs to zfs, it is required to enable index_backup before the unmount of the target. This is an additional step for a regular ldiskfs-based backup/restore and easy to be missed.

Next, restore the backup to an ldiskfs-based system as described in [the section called “ Restoring a File-Level Backup”](#restoring-a-file-level-backup).

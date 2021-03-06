# Lustre Maintenance

- [Lustre Maintenance](#lustre-maintenance)
  * [Working with Inactive OSTs](#working-with-inactive-osts)
  * [Finding Nodes in the Lustre File System](#finding-nodes-in-the-lustre-file-system)
  * [Mounting a Server Without Lustre Service](#mounting-a-server-without-lustre-service)
  * [Regenerating Lustre Configuration Logs](#regenerating-lustre-configuration-logs)
  * [Changing a Server NID](#changing-a-server-nid)
  * [Clearing configuration](#clearing-configuration)L 2.11
  * [Adding a New MDT to a Lustre File System](#adding-a-new-mdt-to-a-lustre-file-system)L 2.4
  * [Adding a New OST to a Lustre File System](#adding-a-new-ost-to-a-lustre-file-system)
  * [Removing and Restoring MDTs and OSTs](#removing-and-restoring-mdts-and-osts)
    + [Removing an MDT from the File System](#removing-an-mdt-from-the-file-system)L 2.4
    + [Working with Inactive MDTs](#working-with-inactive-mdts)L 2.4
    + [Removing an OST from the File System](#removing-an-ost-from-the-file-system)
    + [Backing Up OST Configuration Files](#backing-up-ost-configuration-files)
    + [Restoring OST Configuration Files](#restoring-ost-configuration-files)
    + [Returning a Deactivated OST to Service](#returning-a-deactivated-ost-to-service)
  * [Aborting Recovery](#aborting-recovery)
  * [Determining Which Machine is Serving an OST](#determining-which-machine-is-serving-an-ost)
  * [Changing the Address of a Failover Node](#changing-the-address-of-a-failover-node)
  * [Separate a combined MGS/MDT](#separate-a-combined-mgsmdt)

Once you have the Lustre file system up and running, you can use the procedures in this section to perform these basic Lustre maintenance tasks:

- [the section called “ Working with Inactive OSTs”](#working-with-inactive-osts)
- [the section called “ Finding Nodes in the Lustre File System”](#finding-nodes-in-the-lustre-file-system)
- [the section called “ Mounting a Server Without Lustre Service”](#mounting-a-server-without-lustre-service)
- [the section called “ Regenerating Lustre Configuration Logs”](#regenerating-lustre-configuration-logs)
- [the section called “ Changing a Server NID”](#regenerating-lustre-configuration-logs)
- [the section called “ Adding a New MDT to a Lustre File System”](#adding-a-new-mdt-to-a-lustre-file-system)
- [the section called “ Adding a New OST to a Lustre File System”](#adding-a-new-ost-to-a-lustre-file-system)
- [the section called “ Removing and Restoring MDTs and OSTs”](#removing-and-restoring-mdts-and-osts)
- [the section called “ Removing an MDT from the File System”](#removing-an-mdt-from-the-file-system)
- [the section called “ Working with Inactive MDTs”](#working-with-inactive-mdts)
- [the section called “ Removing an OST from the File System”](#removing-an-ost-from-the-file-system)
- [the section called “ Backing Up OST Configuration Files”](#backing-up-ost-configuration-files)
- [the section called “ Restoring OST Configuration Files”](#restoring-ost-configuration-files)
- [the section called “ Returning a Deactivated OST to Service”](#returning-a-deactivated-ost-to-service)
- [the section called “ Aborting Recovery”](#aborting-recovery)
- [the section called “ Determining Which Machine is Serving an OST ”](#determining-which-machine-is-serving-an-ost)
- [the section called “ Changing the Address of a Failover Node”](#changing-the-address-of-a-failover-node)
- [the section called “ Separate a combined MGS/MDT”](#separate-a-combined-mgsmdt)
- [the section called “ Set an MDT to read-only”](#Set an MDT to read-only)
- [the section called “ Tune Fallocate for ldiskfs”](#Tune Fallocate for ldiskfs)


## Working with Inactive OSTs

To mount a client or an MDT with one or more inactive OSTs, run commands similar to this:

```
client# mount -o exclude=testfs-OST0000 -t lustre \
           uml1:/testfs /mnt/testfs
            client# lctl get_param lov.testfs-clilov-*.target_obd
```

To activate an inactive OST on a live client or MDT, use the `lctl activate` command on the OSC device. For example:

```
lctl --device 7 activate
```

**Note**

A colon-separated list can also be specified. For example, `exclude=testfs-OST0000:testfs-OST0001`.

## Finding Nodes in the Lustre File System

There may be situations in which you need to find all nodes in your Lustre file system or get the names of all OSTs.

To get a list of all Lustre nodes, run this command on the MGS:

```
# lctl get_param mgs.MGS.live.*
```

**Note**

This command must be run on the MGS.

In this example, file system `testfs` has three nodes, `testfs-MDT0000`, `testfs-OST0000`, and `testfs-OST0001`.

```
mgs:/root# lctl get_param mgs.MGS.live.* 
                fsname: testfs 
                flags: 0x0     gen: 26 
                testfs-MDT0000 
                testfs-OST0000 
                testfs-OST0001 
```

To get the names of all OSTs, run this command on the MDS:

```
mds:/root# lctl get_param lov.*-mdtlov.target_obd 
```

**Note**

This command must be run on the MDS.

In this example, there are two OSTs, testfs-OST0000 and testfs-OST0001, which are both active.

```
mgs:/root# lctl get_param lov.testfs-mdtlov.target_obd 
0: testfs-OST0000_UUID ACTIVE 
1: testfs-OST0001_UUID ACTIVE 
```

## Mounting a Server Without Lustre Service

If you are using a combined MGS/MDT, but you only want to start the MGS and not the MDT, run this command:

```
mount -t lustre /dev/mdt_partition -o nosvc /mount_point
```

The `*mdt_partition*` variable is the combined MGS/MDT block device.

In this example, the combined MGS/MDT is `testfs-MDT0000` and the mount point is `/mnt/test/mdt`.

```
$ mount -t lustre -L testfs-MDT0000 -o nosvc /mnt/test/mdt
```

## Regenerating Lustre Configuration Logs

If the Lustre file system configuration logs are in a state where the file system cannot be started, use the `tunefs.lustre --writeconf` command to regenerate them. After the `writeconf` command is run and the servers restart, the configuration logs are re-generated and stored on the MGS (as with a new file system).

You should only use the `writeconf` command if:

- The configuration logs are in a state where the file system cannot start
- A server NID is being changed

The `writeconf` command is destructive to some configuration items (e.g. OST pools information and tunables set via `conf_param`), and should be used with caution.

**Caution**

The OST pools feature enables a group of OSTs to be named for file striping purposes. If you use OST pools, be aware that running the `writeconf` command erases **all** pools information (as well as any other parameters set via `lctl conf_param`). We recommend that the pools definitions (and `conf_param` settings) be executed via a script, so they can be regenerated easily after `writeconf` is performed. However, tunables saved with `lctl set_param -P` are *not* erased in this case.

**Note**

If the MGS still holds any configuration logs, it may be possible to dump these logs to save any parameters stored with `lctl conf_param` by dumping the config logs on the MGS and saving the output:

```
mgs# lctl --device MGS llog_print fsname-client
mgs# lctl --device MGS llog_print fsname-MDT0000
mgs# lctl --device MGS llog_print fsname-OST0000
```

To regenerate Lustre file system configuration logs:

1. Stop the file system services in the following order before running the `tunefs.lustre --writeconf` command:

   a. Unmount the clients.
   b. Unmount the MDT(s).
   c. Unmount the OST(s).
   d. If the MGS is separate from the MDT it can remain mounted during this process.

2. Make sure the MDT and OST devices are available.

3. Run the `tunefs.lustre --writeconf` command on all target devices.

   Run writeconf on the MDT(s) first, and then the OST(s).

   a. On each MDS, for each MDT run:

      ```
      mds# tunefs.lustre --writeconf /dev/mdt_device
      ```

   b. On each OSS, for each OST run:

      ```
      oss# tunefs.lustre --writeconf /dev/ost_device
      ```

4. Restart the file system in the following order:

   1. Mount the separate MGT, if it is not already mounted.
   2. Mount the MDT(s) in order, starting with MDT0000.
   3. Mount the OSTs in order, starting with OST0000.
   4. Mount the clients.

After the `tunefs.lustre --writeconf` command is run, the configuration logs are re-generated as servers connect to the MGS.

## Changing a Server NID

In order to totally rewrite the Lustre configuration, the `tunefs.lustre --writeconf` command is used to rewrite all of the configuration files.

If you need to change only the NID of the MDT or OST, the `replace_nids` command can simplify this process. The `replace_nids` command differs from `tunefs.lustre --writeconf` in that it does not erase the entire configuration log, precluding the need the need to execute the `writeconf` command on all servers and re-specify all permanent parameter settings. However, the `writeconf` command can still be used if desired.

Change a server NID in these situations:

- New server hardware is added to the file system, and the MDS or an OSS is being moved to the new machine.
- New network card is installed in the server.
- You want to reassign IP addresses.

To change a server NID:

1. Update the LNet configuration in the `/etc/modprobe.conf` file so the list of server NIDs is correct. Use `lctl list_nids` to view the list of server NIDS.

   The `lctl list_nids` command indicates which network(s) are configured to work with the Lustre file system.

2. Shut down the file system in this order:

   a. Unmount the clients.
   b. Unmount the MDT.
   c. Unmount all OSTs.

3. If the MGS and MDS share a partition, start the MGS only:

   ```
   mount -t lustre MDT partition -o nosvc mount_point
   ```

4. Run the `replace_nids` command on the MGS:

   ```
   lctl replace_nids devicename nid1[,nid2,nid3 ...]
   ```

   where *devicename* is the Lustre target name, e.g. `testfs-OST0013`

5. If the MGS and MDS share a partition, stop the MGS:

   ```
   umount mount_point
   ```

**Note**

The `replace_nids` command also cleans all old, invalidated records out of the configuration log, while preserving all other current settings.

**Note**

The previous configuration log is backed up on the MGS disk with the suffix `'.bak'`.



Introduced in Lustre 2.11

## Clearing configuration

This command runs on MGS node having the MGS device mounted with `-o nosvc.` It cleans up configuration files stored in the CONFIGS/ directory of any records marked SKIP. If the device name is given, then the specific logs for that filesystem (e.g. testfs-MDT0000) are processed. Otherwise, if a filesystem name is given then all configuration files are cleared. The previous configuration log is backed up on the MGS disk with the suffix 'config.timestamp.bak'. Eg: Lustre-MDT0000-1476454535.bak.

To clear a configuration:

1. Shut down the file system in this order:

   a. Unmount the clients.
   b. Unmount the MDT.
   c. Unmount all OSTs.

2. If the MGS and MDS share a partition, start the MGS only using "nosvc" option.

   ```
   mount -t lustre MDT partition -o nosvc mount_point
   ```

3. Run the `clear_conf` command on the MGS:

   ```
   lctl clear_conf config
   ```

   Example: To clear the configuration for `MDT0000` on a filesystem named `testfs`

   ```
   mgs# lctl clear_conf testfs-MDT0000
   ```



Introduced in Lustre 2.4

## Adding a New MDT to a Lustre File System

Additional MDTs can be added using the DNE feature to serve one or more remote sub-directories within a filesystem, in order to increase the total number of files that can be created in the filesystem, to increase aggregate metadata performance, or to isolate user or application workloads from other users of the filesystem. It is possible to have multiple remote sub-directories reference the same MDT. However, the root directory will always be located on MDT0000. To add a new MDT into the file system:

1. Discover the maximum MDT index. Each MDT must have unique index.

   ```
   client$ lctl dl | grep mdc
   36 UP mdc testfs-MDT0000-mdc-ffff88004edf3c00 4c8be054-144f-9359-b063-8477566eb84e 5
   37 UP mdc testfs-MDT0001-mdc-ffff88004edf3c00 4c8be054-144f-9359-b063-8477566eb84e 5
   38 UP mdc testfs-MDT0002-mdc-ffff88004edf3c00 4c8be054-144f-9359-b063-8477566eb84e 5
   39 UP mdc testfs-MDT0003-mdc-ffff88004edf3c00 4c8be054-144f-9359-b063-8477566eb84e 5
   ```

2. Add the new block device as a new MDT at the next available index. In this example, the next available index is 4.

   ```
   mds# mkfs.lustre --reformat --fsname=testfs --mdt --mgsnode=mgsnode --index 4 /dev/mdt4_device
   ```

3. Mount the MDTs.

   ```
   mds# mount –t lustre /dev/mdt4_blockdevice /mnt/mdt4
   ```

4. In order to start creating new files and directories on the new MDT(s) they need to be attached into the namespace at one or more subdirectories using the `lfs mkdir` command. All files and directories below those created with `lfs mkdir` will also be created on the same MDT unless otherwise specified.

   ```
   client# lfs mkdir -i 3 /mnt/testfs/new_dir_on_mdt3
   client# lfs mkdir -i 4 /mnt/testfs/new_dir_on_mdt4
   client# lfs mkdir -c 4 /mnt/testfs/new_directory_striped_across_4_mdts
   ```

## Adding a New OST to a Lustre File System

A new OST can be added to existing Lustre file system on either an existing OSS node or on a new OSS node. In order to keep client IO load balanced across OSS nodes for maximum aggregate performance, it is not recommended to configure different numbers of OSTs to each OSS node.

1. Add a new OST by using `mkfs.lustre` as when the filesystem was first formatted, see [4](02.07-Configuring%20a%20Lustre%20File%20System.md#configuring-a-simple-lustre-file-system) for details. Each new OST must have a unique index number, use `lctl dl` to see a list of all OSTs. For example, to add a new OST at index 12 to the `testfs`filesystem run following commands should be run on the OSS:

   ```
   oss# mkfs.lustre --fsname=testfs --mgsnode=mds16@tcp0 --ost --index=12 /dev/sda
   oss# mkdir -p /mnt/testfs/ost12
   oss# mount -t lustre /dev/sda /mnt/testfs/ost12
   ```

2. Balance OST space usage (possibly).

   The file system can be quite unbalanced when new empty OSTs are added to a relatively full filesystem. New file creations are automatically balanced to favour the new OSTs. If this is a scratch file system or files are pruned at regular intervals, then no further work may be needed to balance the OST space usage as new files being created will preferentially be placed on the less full OST(s). As old files are deleted, they will release space on the old OST(s).

   Files existing prior to the expansion can optionally be rebalanced using the `lfs_migrate` utility. This redistributes file data over the entire set of OSTs.

   For example, to rebalance all files within the directory `/mnt/lustre/dir`, enter:

   ```
   client# lfs_migrate /mnt/lustre/dir
   ```

   To migrate files within the `/test` file system on `OST0004` that are larger than 4GB in size to other OSTs, enter:

   ```
   client# lfs find /test --ost test-OST0004 -size +4G | lfs_migrate -y
   ```

   See [*the section called “ `lfs_migrate` ”*](06.03-User%20Utilities.md#lfs_migrate) for details.

## Removing and Restoring MDTs and OSTs

OSTs and DNE MDTs can be removed from and restored to a Lustre filesystem. Deactivating an OST means that it is temporarily or permanently marked unavailable. Deactivating an OST on the MDS means it will not try to allocate new objects there or perform OST recovery, while deactivating an OST the client means it will not wait for OST recovery if it cannot contact the OST and will instead return an IO error to the application immediately if files on the OST are accessed. An OST may be permanently deactivated from the file system, depending on the situation and commands used.

**Note**

A permanently deactivated MDT or OST still appears in the filesystem configuration until the configuration is regenerated with `writeconf` or it is replaced with a new MDT or OST at the same index and permanently reactivated. A deactivated OST will not be listed by `lfs df`.

You may want to temporarily deactivate an OST on the MDS to prevent new files from being written to it in several situations:

- A hard drive has failed and a RAID resync/rebuild is underway, though the OST can also be marked *degraded* by the RAID system to avoid allocating new files on the slow OST which can reduce performance, see [*the section called “ Handling Degraded OST RAID Arrays”*](03.02-Lustre%20Operations.md#handling-degraded-ost-raid-arrays) for more details.
- OST is nearing its space capacity, though the MDS will already try to avoid allocating new files on overly-full OSTs if possible, see [*the section called “Allocating Free Space on OSTs”*](06.02-Lustre%20Parameters.md#allocating-free-space-on-osts) for details.
- MDT/OST storage or MDS/OSS node has failed, and will not be available for some time (or forever), but there is still a desire to continue using the filesystem before it is repaired.



Introduced in Lustre 2.4

### Removing an MDT from the File System

If the MDT is permanently inaccessible, `lfs rm_entry {directory}` can be used to delete the directory entry for the unavailable MDT. Using `rmdir` would otherwise report an IO error due to the remote MDT being inactive. Please note that if the MDT *is*available, standard `rm -r` should be used to delete the remote directory. After the remote directory has been removed, the administrator should mark the MDT as permanently inactive with:

```
lctl conf_param {MDT name}.mdc.active=0
```

A user can identify which MDT holds a remote sub-directory using the `lfs` utility. For example:

```
client$ lfs getstripe --mdt-index /mnt/lustre/remote_dir1
1
client$ mkdir /mnt/lustre/local_dir0
client$ lfs getstripe --mdt-index /mnt/lustre/local_dir0
0
```

The `lfs getstripe --mdt-index` command returns the index of the MDT that is serving the given directory.



Introduced in Lustre 2.4

### Working with Inactive MDTs

Files located on or below an inactive MDT are inaccessible until the MDT is activated again. Clients accessing an inactive MDT will receive an EIO error.

### Removing an OST from the File System     

When deactivating an OST, note that the client and MDS each have an OSC device that handles communication with the corresponding OST.  To remove an OST from the file system:      

1. If the OST is functional, and there are files located on the OST that need to be migrated off of the OST, the file creation for that OST should be temporarily deactivated on the MDS (each MDS if running with multiple MDS nodes in DNE mode).           

   1. Introduced in Lustre 2.9

      With Lustre 2.9 and later, the MDS should be set to only disable file creation on that OST by setting   `max_create_count` to zero:

       `mds# lctl set_param osp.*osc_name*.max_create_count=0` 

      This ensures that files deleted or migrated off of the OST will have their corresponding OST objects destroyed, and the space will be freed.  For example, to disable `OST0000`               in the filesystem `testfs`, run: 

       `mds# lctl set_param osp.testfs-OST0000-osc-MDT*.max_create_count=0`               on each MDS in the `testfs` filesystem.

   2. With older versions of Lustre, to deactivate the OSC on the MDS node(s) use:               

      ```
      mds# lctl set_param osp.osc_name.active=0
      ```

      This will prevent the MDS from attempting any communication with that OST, including destroying objects located thereon.  This is fine if the OST will be removed permanently, if the OST is not stable in operation, or if it is in a read-only state.  Otherwise,  the free space and objects on the OST will not decrease when files are deleted, and object destruction will be deferred until the MDS reconnects to the OST.

      For example, to deactivate `OST0000` in the filesystem `testfs`, run:               

      ```
      mds# lctl set_param osp.testfs-OST0000-osc-MDT*.active=0
      ```

      Deactivating the OST on the *MDS* does not prevent use of existing objects for read/write by a client.

      ### Note

      If migrating files from a working OST, do not deactivate the OST on clients. This causes IO errors when accessing files located there, and migrating files on the OST would fail.

      ### Caution

      Do not use `lctl conf_param` to deactivate the OST if it is still working, as this immediately and permanently deactivates it in the file system configuration on both the MDS and all clients.

2. Discover all files that have objects residing on the deactivated OST. Depending on whether the deactivated OST is available or not, the data from that OST may be migrated to           other OSTs, or may need to be restored from backup.

   1. If the OST is still online and available, find all files with objects on the deactivated OST, and copy them to other OSTs in the file system to: 

      ```
      client# lfs find --ost ost_name /mount/point | lfs_migrate -y
      ```

      Note that if multiple OSTs are being deactivated at one time, the `lfs find` command can take multiple  `--ost` arguments, and will return files that are located on *any* of the specified OSTs. 	      

   2. If the OST is no longer available, delete the files on that OST and restore them from backup:                 

      ```
      client# lfs find --ost ost_uuid -print0 /mount/point |
              tee /tmp/files_to_restore | xargs -0 -n 1 unlink
      ```

      The list of files that need to be restored from backup is stored in `/tmp/files_to_restore`. Restoring these files is beyond the scope of this document.

3. Deactivate the OST.

   1. If there is expected to be a replacement OST in some short time (a few days), the OST can temporarily be deactivated on the clients using:                 

      ```
      client# lctl set_param osc.fsname-OSTnumber-*.active=0                 
      ```

      ### Note

      This setting is only temporary and will be reset if the clients are remounted or rebooted. It needs to be run on all clients.

   2. If there is not expected to be a replacement for this OST in the near future, permanently deactivate it on all clients and the MDS by running the following command on the MGS:               

      ```
      mgs# lctl conf_param ost_name.osc.active=0
      ```

      ### Note

      A deactivated OST still appears in the file system configuration, though a replacement OST can be created that re-uses the same OST index with the `mkfs.lustre --replace` option, see [the section called “ Restoring OST Configuration Files”](#restoring-ost-configuration-files). 

To totally remove the OST from the filesystem configuration, the OST configuration records should be found in the startup logs by running the command "`lctl --device MGS llog_print fsname-client`" on the MGS (and also "... `$fsname-MDTxxxx`" for all the MDTs) to list all attach, setup, add_osc, add_pool, and other records related to the removed OST(s). Once the index value is known for each configuration record, the command "`lctl --device MGS llog_cancel llog_name -i index` " will drop that record from the configuration log llog_name for each of the `fsname-client` and `fsname-MDTxxxx` configuration logs so that new mounts will no longer process it. If a whole OSS is being removed, the`add_uuid` records for the OSS should similarly be canceled.

```
mgs# lctl --device MGS llog_print testfs-client | egrep "192.168.10.99@tcp|OST0003"
- { index: 135, event: add_uuid, nid: 192.168.10.99@tcp(0x20000c0a80a63), node: 192.168.10.99@tcp }
- { index: 136, event: attach, device: testfs-OST0003-osc, type: osc, UUID: testfs-clilov_UUID }
- { index: 137, event: setup, device: testfs-OST0003-osc, UUID: testfs-OST0003_UUID, node: 192.168.10.99@tcp }
- { index: 138, event: add_osc, device: testfs-clilov, ost: testfs-OST0003_UUID, index: 3, gen: 1 }
mgs# lctl --device MGS llog_cancel testfs-client -i 138
mgs# lctl --device MGS llog_cancel testfs-client -i 137
mgs# lctl --device MGS llog_cancel testfs-client -i 136
```



### Backing Up OST Configuration Files

If the OST device is still accessible, then the Lustre configuration files on the OST should be backed up and saved for future use in order to avoid difficulties when a replacement OST is returned to service. These files rarely change, so they can and should be backed up while the OST is functional and accessible. If the deactivated OST is still available to mount (i.e. has not permanently failed or is unmountable due to severe corruption), an effort should be made to preserve these files.

1. Mount the OST file system.

   ```
   oss# mkdir -p /mnt/ost
   oss# mount -t ldiskfs /dev/ost_device /mnt/ost
   ```

   

2. Back up the OST configuration files.

   ```
   oss# tar cvf ost_name.tar -C /mnt/ost last_rcvd \
              CONFIGS/ O/0/LAST_ID
   ```

   

3. Unmount the OST file system.

   ```
   oss# umount /mnt/ost
   ```

   

### Restoring OST Configuration Files

If the original OST is still available, it is best to follow the OST backup and restore procedure given in either [*the section called “ Backing Up and Restoring an MDT or OST (ldiskfs Device Level)”*](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-and-restoring-an-mdt-or-ost-ldiskfs-device-level), or [*the section called “ Backing Up an OST or MDT (Backend File System Level)”*](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-an-ost-or-mdt-backend-file-system-level) and [*the section called “ Restoring a File-Level Backup”*](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#restoring-a-file-level-backup).

To replace an OST that was removed from service due to corruption or hardware failure, the replacement OST needs to be formatted using `mkfs.lustre`, and the Lustre file system configuration should be restored, if available. Any objects stored on the OST will be permanently lost, and files using the OST should be deleted and/or restored from backup.

Introduced in Lustre 2.5

With Lustre 2.5 and later, it is possible to replace an OST to the same index without restoring the configuration files, using the `--replace` option at format time.

```
oss# mkfs.lustre --ost --reformat --replace --index=old_ost_index \
        other_options /dev/new_ost_dev
```

The MDS and OSS will negotiate the `LAST_ID` value for the replacement OST.

If the OST configuration files were not backed up, due to the OST file system being completely inaccessible, it is still possible to replace the failed OST with a new one at the same OST index.

1. For older versions, format the OST file system without the `--replace` option and restore the saved configuration:

   ```
   oss# mkfs.lustre --ost --reformat --index=old_ost_index \
              other_options /dev/new_ost_dev
   ```

2. Mount the OST file system.

   ```
   oss# mkdir /mnt/ost
   oss# mount -t ldiskfs /dev/new_ost_dev /mnt/ost
   ```

3. Restore the OST configuration files, if available.

   ```
   oss# tar xvf ost_name.tar -C /mnt/ost
   ```

4. Recreate the OST configuration files, if unavailable.

   Follow the procedure in [*the section called “Fixing a Bad LAST_ID on an OST”*](05.01-Lustre%20File%20System%20Troubleshooting.md#fixing-a-bad-last-id-on-an-ost) to recreate the LAST_ID file for this OST index. The `last_rcvd` file will be recreated when the OST is first mounted using the default parameters, which are normally correct for all file systems. The `CONFIGS/mountdata` file is created by `mkfs.lustre` at format time, but has flags set that request it to register itself with the MGS. It is possible to copy the flags from another working OST (which should be the same):

   ```
   oss1# debugfs -c -R "dump CONFIGS/mountdata /tmp" /dev/other_osdev
   oss1# scp /tmp/mountdata oss0:/tmp/mountdata
   oss0# dd if=/tmp/mountdata of=/mnt/ost/CONFIGS/mountdata bs=4 count=1 seek=5 skip=5 conv=notrunc
   ```

5. Unmount the OST file system.

   ```
   oss# umount /mnt/ost
   ```

### Returning a Deactivated OST to Service

If the OST was permanently deactivated, it needs to be reactivated in the MGS configuration.

```
mgs# lctl conf_param ost_name.osc.active=1
```

If the OST was temporarily deactivated, it needs to be reactivated on the MDS and clients.

```
mds# lctl set_param osp.fsname-OSTnumber-*.active=1
client# lctl set_param osc.fsname-OSTnumber-*.active=1
```
## Aborting Recovery

You can abort recovery with either the `lctl` utility or by mounting the target with the `abort_recov` option (`mount -o abort_recov`). When starting a target, run:

```
mds# mount -t lustre -L mdt_name -o abort_recov /mount_point
```

**Note**

The recovery process is blocked until all OSTs are available.

## Determining Which Machine is Serving an OST

In the course of administering a Lustre file system, you may need to determine which machine is serving a specific OST. It is not as simple as identifying the machine’s IP address, as IP is only one of several networking protocols that the Lustre software uses and, as such, LNet does not use IP addresses as node identifiers, but NIDs instead. To identify the NID that is serving a specific OST, run one of the following commands on a client (you do not need to be a root user):

```
client$ lctl get_param osc.fsname-OSTnumber*.ost_conn_uuid
```

For example:

```
client$ lctl get_param osc.*-OST0000*.ost_conn_uuid 
osc.testfs-OST0000-osc-f1579000.ost_conn_uuid=192.168.20.1@tcp
```

\- OR -

```
client$ lctl get_param osc.*.ost_conn_uuid 
osc.testfs-OST0000-osc-f1579000.ost_conn_uuid=192.168.20.1@tcp
osc.testfs-OST0001-osc-f1579000.ost_conn_uuid=192.168.20.1@tcp
osc.testfs-OST0002-osc-f1579000.ost_conn_uuid=192.168.20.1@tcp
osc.testfs-OST0003-osc-f1579000.ost_conn_uuid=192.168.20.1@tcp
osc.testfs-OST0004-osc-f1579000.ost_conn_uuid=192.168.20.1@tcp
```

## Changing the Address of a Failover Node

To change the address of a failover node (e.g, to use node X instead of node Y), run this command on the OSS/OST partition (depending on which option was used to originally identify the NID):

```
oss# tunefs.lustre --erase-params --servicenode=NID /dev/ost_device
```

or

```
oss# tunefs.lustre --erase-params --failnode=NID /dev/ost_device
```

For more information about the `--servicenode` and `--failnode` options, see [*Configuring Failover in a Lustre File System*](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md).

## Separate a combined MGS/MDT

These instructions assume the MGS node will be the same as the MDS node. For instructions on how to move MGS to a different node, see [*the section called “ Changing a Server NID”*](#changing-a-server-nid).

These instructions are for doing the split without shutting down other servers and clients.

1. Stop the MDS.

   Unmount the MDT

   ```
   umount -f /dev/mdt_device 
   ```

2. Create the MGS.

   ```
   mds# mkfs.lustre --mgs --device-size=size /dev/mgs_device
   ```

3. Copy the configuration data from MDT disk to the new MGS disk.

   ```
   mds# mount -t ldiskfs -o ro /dev/mdt_device /mdt_mount_point
   ```

   ```
   mds# mount -t ldiskfs -o rw /dev/mgs_device /mgs_mount_point 
   ```

   ```
   mds# cp -r /mdt_mount_point/CONFIGS/filesystem_name-* /mgs_mount_point/CONFIGS/. 
   ```

   ```
   mds# umount /mgs_mount_point
   ```

   ```
   mds# umount /mdt_mount_point
   ```

   See [*the section called “ Regenerating Lustre Configuration Logs”*](#regenerating-lustre-configuration-logs) for alternative method.

4. Start the MGS.

   ```
   mgs# mount -t lustre /dev/mgs_device /mgs_mount_point
   ```

   Check to make sure it knows about all your file system

   ```
   mgs:/root# lctl get_param mgs.MGS.filesystems
   ```

5. Remove the MGS option from the MDT, and set the new MGS nid.

   ```
   mds# tunefs.lustre --nomgs --mgsnode=new_mgs_nid /dev/mdt-device
   ```

6. Start the MDT.

   ```
   mds# mount -t lustre /dev/mdt_device /mdt_mount_point
   ```

   Check to make sure the MGS configuration looks right:

   ```
   mgs# lctl get_param mgs.MGS.live.filesystem_name
   ```

## Set an MDT to read-only

It is sometimes desirable to be able to mark the filesystem read-only directly on the server, rather than remounting the clients and setting the option there. This can be useful if there is a rogue client that is deleting files, or when decommissioning a system to prevent already-mounted clients from modifying it anymore. 

Set the `mdt.*.readonly` parameter to 1 to immediately set the MDT to read-only. All future MDT access will immediately return a "Read-only file system" error (EROFS) until the parameter is set to `0` again. 

Example of setting the `readonly` parameter to `1`, verifying the current setting, accessing from a client, and setting the parameter back to `0`:

```
mds# lctl set_param mdt.fs-MDT0000.readonly=1
mdt.fs-MDT0000.readonly=1

mds# lctl get_param mdt.fs-MDT0000.readonly
mdt.fs-MDT0000.readonly=1

client$ touch test_file
touch: cannot touch ‘test_file’: Read-only file system

mds# lctl set_param mdt.fs-MDT0000.readonly=0
mdt.fs-MDT0000.readonly=0
```

## Tune Fallocate for ldiskfs

This section shows how to tune/enable/disable fallocate for ldiskfs OSTs. 

The default `mode=0` is the standard "allocate unwritten extents" behavior used by ext4. This is by far the fastest for space allocation, but requires the unwritten extents to be split and/or zeroed when they are overwritten. 

The OST fallocate `mode=1` can also be set to use "zeroed extents", which may be handled by "WRITE SAME", "TRIM zeroes data", or other low-level functionality in the underlying block device. 

`mode=-1` completely disables fallocate.

Example: To completely disable fallocate 

```
lctl set_param osd-ldiskfs.*.fallocate_zero_blocks=-1
```

 Example: To enable fallocate to use 'zeroed extents' 

```
lctl set_param osd-ldiskfs.*.fallocate_zero_blocks=1
```


# Upgrading a Lustre File System

- [Upgrading a Lustre File System](#upgrading-a-lustre-file-system)
  * [Release Interoperability and Upgrade Requirements](#release-interoperability-and-upgrade-requirements)
  * [Upgrading to Lustre Software Release 2.x (Major Release)](#upgrading-to-lustre-software-release-2x-major-release)
  * [Upgrading to Lustre Software Release 2.x.y (Minor Release)](#upgrading-to-lustre-software-release-2xy-minor-release)

This chapter describes interoperability between Lustre software releases. It also provides procedures for upgrading from older Lustre 2.x software releases to a more recent 2.y Lustre release a (major release upgrade), and from a Lustre software release 2.x.y to a more recent Lustre software release 2.x.z (minor release upgrade). It includes the following sections:

* [the section called “ Release Interoperability and Upgrade Requirements”](#release-interoperability-and-upgrade-requirements)

* [the section called “ Upgrading to Lustre Software Release 2.x (Major Release)”](#upgrading-to-lustre-software-release-2x-major-release)

* [the section called “ Upgrading to Lustre Software Release 2.x.y (Minor Release)”](#upgrading-to-lustre-software-release-2xy-minor-release)

## Release Interoperability and Upgrade Requirements

**Lustre software release 2.x (major) upgrade:**

- All servers must be upgraded at the same time, while some or all clients may be upgraded independently of the servers.
- All servers must be be upgraded to a Linux kernel supported by the Lustre software. See the Lustre Release Notes for your Lustre version for a list of tested Linux distributions.
- Clients to be upgraded must be running a compatible Linux distribution as described in the Release Notes.

**Lustre software release 2.x.y release (minor) upgrade:**

- All servers must be upgraded at the same time, while some or all clients may be upgraded.
- Rolling upgrades are supported for minor releases allowing individual servers and clients to be upgraded without stopping the Lustre file system.

## Upgrading to Lustre Software Release 2.x (Major Release)

The procedure for upgrading from a Lustre software release 2.x to a more recent 2.y major release of the Lustre software is described in this section. To upgrade an existing 2.x installation to a more recent major release, complete the following steps:

1. Create a complete, restorable file system backup.

   ### Caution

   Before installing the Lustre software, back up ALL data. The Lustre software contains kernel modifications that interact with storage devices and may introduce security issues and data loss if not installed, configured, or administered properly. If a full backup of the file system is not practical, a device-level backup of the MDT file system is recommended. See [*Backing Up and Restoring a File System*](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md) for a procedure.

2. Shut down the entire filesystem by following [the section called “ Stopping the Filesystem”](03.02-Lustre%20Operations.md#stopping-the-filesystem)

3. Upgrade the Linux operating system on all servers to a compatible (tested) Linux distribution and reboot.

4. Upgrade the Linux operating system on all clients to a compatible (tested) distribution and reboot.

5. Download the Lustre server RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases) repository. See [Table 5, “Packages Installed on Lustre Servers”](02.05-Installing%20the%20Lustre%20Software.md#table-5-packages-installed-on-lustre-servers) for a list of required packages.

6. Install the Lustre server packages on all Lustre servers (MGS, MDSs, and OSSs).

   a. Log onto a Lustre server as the `root` user

   b. Use the `yum` command to install the packages:

   ```
   # yum --nogpgcheck install pkg1.rpm pkg2.rpm ... 
   ```

   c. Verify the packages are installed correctly:

   ```
   rpm -qa|egrep "lustre|wc"
   ```

   d. Repeat these steps on each Lustre server.

   ```
   
   ```

7. Download the Lustre client RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases) repository. See [Table 6, “Packages Installed on Lustre Clients”](02.05-Installing%20the%20Lustre%20Software.md#table-6-packages-installed-on-lustre-clients) for a list of required packages.

   ### Note

   The version of the kernel running on a Lustre client must be the same as the version of the `lustre-client-modules-ver`package being installed. If not, a compatible kernel must be installed on the client before the Lustre client packages are installed.

8. Install the Lustre client packages on each of the Lustre clients to be upgraded.

   a. Log onto a Lustre client as the `root` user.

   b. Use the `yum` command to install the packages:

   ```
   # yum --nogpgcheck install pkg1.rpm pkg2.rpm ... 
   ```

   c. Verify the packages were installed correctly:

   ```
   # rpm -qa|egrep "lustre|kernel"
   ```

   d. Repeat these steps on each Lustre client.

   

9. The DNE feature allows using multiple MDTs within a single filesystem namespace, and each MDT can each serve one or more remote sub-directories in the file system. The root directory is always located on MDT0.

   Note that clients running a release prior to the Lustre software release 2.4 can only see the namespace hosted by MDT0 and will return an IO error if an attempt is made to access a directory on another MDT.

   (Optional) To format an additional MDT, complete these steps:

   a. Determine the index used for the first MDT (each MDT must have unique index). Enter:

   ```
   client$ lctl dl | grep mdc
   36 UP mdc lustre-MDT0000-mdc-ffff88004edf3c00 
         4c8be054-144f-9359-b063-8477566eb84e 5
   ```

   In this example, the next available index is 1.

   b. Format the new block device as a new MDT at the next available MDT index by entering (on one line):

   ```
   mds# mkfs.lustre --reformat --fsname=filesystem_name --mdt \
       --mgsnode=mgsnode --index new_mdt_index
   /dev/mdt1_device
   ```

10. (Optional) If you are upgrading from Lustre software release 2.10, to enable the project quota feature enter the following on every ldiskfs backend target while unmounted:

   ```
    tune2fs –O dirdata /dev/mdtdev
   ```

​		**Note**

​		Enabling the project feature will prevent the filesystem from being used by older versions of ldiskfs, so it should only be enabled if the project quota feature is required and/or after it is known that the upgraded release does not need to be downgraded.

11. When setting up the file system, enter:

    ```
    conf_param $FSNAME.quota.mdt=$QUOTA_TYPE
    conf_param $FSNAME.quota.ost=$QUOTA_TYPE
    ```

12. (Optional) If upgrading an ldiskfs MDT formatted prior to Lustre 2.13, the "wide striping" feature that allows files to have more than 160 stripes and store other large xattrs was not enabled by default. This feature can be enabled on existing MDTs by running the following command on all MDT devices:

    ```
    mds# tune2fs -O ea_inode /dev/mdtdev
    ```

    For more information about wide striping, see Section “Lustre Striping Internals”.

13. Start the Lustre file system by starting the components in the order shown in the following steps:

    a. Mount the MGT. On the MGS, run

    ```
    mgs# mount -a -t lustre
    ```

    b. Mount the MDT(s). On each MDT, run:

    ```
    mds# mount -a -t lustre
    ```

    c. Mount all the OSTs. On each OSS node, run:

    ```
    oss# mount -a -t lustre
    ```

    ### Note

    This command assumes that all the OSTs are listed in the `/etc/fstab` file. OSTs that are not listed in the `/etc/fstab` file, must be mounted individually by running the mount command:

    ```
    mount -t lustre /dev/block_device/mount_point
    ```

    d. Mount the file system on the clients. On each client node, run:

    ```
    client# mount -a -t lustre
    ```
    
14. (Optional) If you are upgrading from a release before Lustre 2.7, to enable OST FIDs to also store the
    OST index (to improve reliability of LFSCK and debug messages), after the OSTs are mounted run
    once on each OSS:

    ```
    oss# lctl set_param osd-ldiskfs.*.osd_index_in_idif=1
    ```

    **Note**

    Enabling the `index_in_idif` feature will prevent the OST from being used by older versions of Lustre, so it should only be enabled once it is known there is no need for the OST to be downgraded to an earlier release.

15. If a new MDT was added to the filesystem, the new MDT must be attached into the namespace by creating one or more new DNE subdirectories with the lfs mkdir command that use the new MDT:

    ```
    client# lfs mkdir -i new_mdt_index /testfs/new_dir
    ```

    In Lustre 2.8 and later, it is possible to split a new directory across multiple MDTs by creating it with multiple stripes:

    ```
    client# lfs mkdir -c 2 /testfs/new_striped_dir
    ```

    In Lustre 2.13 and later, it is possible to set the default striping on existing directories so that new remote subdirectories are created on less-full MDTs:

    ```
    client# lfs setdirstripe -c 1 -i -1 /testfs/some_dir
    ```

**Note **

The mounting order described in the steps above must be followed for the initial mount and registration of a Lustre file system after an upgrade. For a normal start of a Lustre file system, the mounting order is MGT, OSTs, MDT(s), clients.

If you have a problem upgrading a Lustre file system, see [the section called “Reporting a Lustre File System Bug”](05.01-Lustre%20File%20System%20Troubleshooting.md#reporting-a-lustre-file-system-bug) for ways to get help.

## Upgrading to Lustre Software Release 2.x.y (Minor Release)

Rolling upgrades are supported for upgrading from any Lustre software release 2.x.y to a more recent Lustre software release 2.X.y. This allows the Lustre file system to continue to run while individual servers (or their failover partners) and clients are upgraded one at a time. The procedure for upgrading a Lustre software release 2.x.y to a more recent minor release is described in this section.

To upgrade Lustre software release 2.x.y to a more recent minor release, complete these steps:

1. Create a complete, restorable file system backup.

   ### Caution

   Before installing the Lustre software, back up ALL data. The Lustre software contains kernel modifications that interact with storage devices and may introduce security issues and data loss if not installed, configured, or administered properly. If a full backup of the file system is not practical, a device-level backup of the MDT file system is recommended. See *Backing Up and Restoring a File System* for a procedure.

2. Download the Lustre server RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases) repository. See [Table 5, “Packages Installed on Lustre Servers”](02.05-Installing%20the%20Lustre%20Software.md#table-5-packages-installed-on-lustre-servers) for a list of required packages.

3. For a rolling upgrade, complete any procedures required to keep the Lustre file system running while the server to be upgraded is offline, such as failing over a primary server to its secondary partner.

4. Unmount the Lustre server to be upgraded (MGS, MDS, or OSS)

5. Install the Lustre server packages on the Lustre server.

   1. Log onto the Lustre server as the `root` user

   2. Use the `yum` command to install the packages:

      

      ```
      # yum --nogpgcheck install pkg1.rpm pkg2.rpm ... 
      ```

      

   3. Verify the packages are installed correctly:

      

      ```
      rpm -qa|egrep "lustre|wc"
      ```

      

   4. Mount the Lustre server to restart the Lustre software on the server:

      ```
      server# mount -a -t lustre
      ```

   5. Repeat these steps on each Lustre server.

6. Download the Lustre client RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases) repository. See [Table 6, “Packages Installed on Lustre Clients”](02.05-Installing%20the%20Lustre%20Software.md#table-6-packages-installed-on-lustre-clients) for a list of required packages.

7. Install the Lustre client packages on each of the Lustre clients to be upgraded.

   1. Log onto a Lustre client as the `root` user.

   2. Use the `yum` command to install the packages:

      ```
      # yum --nogpgcheck install pkg1.rpm pkg2.rpm ... 
      ```

   3. Verify the packages were installed correctly:

      ```
      # rpm -qa|egrep "lustre|kernel"
      ```

   4. Mount the Lustre client to restart the Lustre software on the client:

      ```
      client# mount -a -t lustre
      ```

   5. Repeat these steps on each Lustre client.

If you have a problem upgrading a Lustre file system, see [the section called “Reporting a Lustre File System Bug”](05.01-Lustre%20File%20System%20Troubleshooting.md#reporting-a-lustre-file-system-bug) for some suggestions for how to get help. 

# Installing the Lustre Software

**Table of Contents**

- [Installing the Lustre Software](#installing-the-lustre-software)
  
  * [Preparing to Install the Lustre Software](#preparing-to-install-the-lustre-software)
    + [Software Requirements](#software-requirements)
    + [Environmental Requirements](#environmental-requirements)
  * [Lustre Software Installation Procedure](#lustre-software-installation-procedure)
  
  

This chapter describes how to install the Lustre software from RPM packages. It includes:

- [the section called “ Preparing to Install the Lustre Software”](#preparing-to-install-the-lustre-software)
- [the section called “Lustre Software Installation Procedure”](#lustre-software-installation-procedure)

For hardware and system requirements and hardware configuration information, see[*Determining Hardware Configuration Requirements and Formatting Options*](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md).

## Preparing to Install the Lustre Software

- [Software Requirements](section_rqs_tjw_3k.html)
- [Environmental Requirements](section_rh2_d4w_gk.html)

You can install the Lustre software from downloaded packages (RPMs) or directly from the source code. This chapter describes how to install the Lustre RPM packages. Instructions to install from source code are beyond the scope of this document, and can be found elsewhere online.

The Lustre RPM packages are tested on current versions of Linux enterprise distributions at the time they are created. See the release notes for each version for specific details.

### Software Requirements

To install the Lustre software from RPMs, the following are required:

- **Lustre server packages** . The required packages for Lustre 2.9 EL7 servers are listed in the table below, where *ver* refers to the Lustre release and kernel version (e.g., 2.9.0-1.el7) and *arch* refers to the processor architecture (e.g., x86_64). These packages are available in the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases) repository, and may differ depending on your distro and version.

  

  ##### Table 5. Packages Installed on Lustre Servers

  | Package Name                            | Description                                                  |
| --------------------------------------- | ------------------------------------------------------------ |
  | `kernel-*ver*_lustre.*arch*`            | Linux kernel with Lustre software patches (often referred to as "patched kernel") |
  | `lustre-*ver*.*arch*`                   | Lustre software command line tools                           |
  | `kmod-lustre-*ver*.*arch*`              | Lustre-patched kernel modules                                |
  | `kmod-lustre-osd-ldiskfs-*ver*.*arch*`  | Lustre back-end file system tools for ldiskfs-based servers. |
  | `lustre-osd-ldiskfs-mount-*ver*.*arch*` | Helper library for `mount.lustre` and `mkfs.lustre` for ldiskfs-based servers. |
  | `kmod-lustre-osd-zfs-*ver*.*arch*`      | Lustre back-end file system tools for ZFS. This is an alternative to `lustre-osd-ldiskfs`(kmod-spl and kmod-zfs available separately). |
  | `lustre-osd-zfs-mount-*ver*.*arch*`     | Helper library for `mount.lustre` and `mkfs.lustre` for ZFS-based servers (zfs utilities available separately). |
  | `e2fsprogs`                             | Utilities to maintain Lustre ldiskfs back-end file system(s) |
  | `lustre-tests-*ver*_lustre.*arch*`      | Scripts and programs used for running regression tests for Lustre, but likely only of interest to Lustre developers or testers. |
  
- **Lustre client packages** . The required packages for Lustre 2.9 EL7 clients are listed in the table below, where *ver* refers to the Linux distribution (e.g., 3.6.18-348.1.1.el5). These packages are available in the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases)repository.

  ##### Table 6. Packages Installed on Lustre Clients

  | Package Name                      | Description                                                  |
| --------------------------------- | ------------------------------------------------------------ |
  | `kmod-lustre-client-*ver*.*arch*` | Patchless kernel modules for client                          |
| `lustre-client-*ver*.*arch*`      | Client command line tools                                    |
  | `lustre-client-dkms-*ver*.*arch*` | Alternate client RPM to kmod-lustre-client with Dynamic Kernel Module Support (DKMS) installation. This avoids the need to install a new RPM for each kernel update, but requires a full build environment on the client. |
  
  ### Note
  
  The version of the kernel running on a Lustre client must be the same as the version of the `kmod-lustre-client-*ver*` package being installed, unless the DKMS package is installed. If the kernel running on the client is not compatible, a kernel that is compatible must be installed on the client before the Lustre file system software is used.

- **Lustre LNet network driver (LND)** . The Lustre LNDs provided with the Lustre software are listed in the table below. For more information about Lustre LNet, see [*Understanding Lustre Networking (LNet)*](02-Introducing%20the%20Lustre%20File%20System.md#understanding-lustre-networking-lnet).

  

  ##### Table 7. Network Types Supported by Lustre LNDs

  | Supported Network Types | Notes                                                        |
  | ----------------------- | ------------------------------------------------------------ |
  | TCP                     | Any network carrying TCP traffic, including GigE, 10GigE, and IPoIB |
  | InfiniBand network      | OpenFabrics OFED (o2ib)                                      |
  | gni                     | Gemini (Cray)                                                |

   

   

### Note

The InfiniBand and TCP Lustre LNDs are routinely tested during release cycles. The other LNDs are maintained by their respective owners

- **High availability software** . If needed, install third party high-availability software. For more information, see [the section called “Preparing a Lustre File System for Failover”](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md#preparing-a-lustre-file-system-for-failover).
- **Optional packages.** Optional packages provided in the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases)repository may include the following (depending on the operating system and platform):
  - `kernel-debuginfo`, `kernel-debuginfo-common`, `lustre-debuginfo`,`lustre-osd-ldiskfs-debuginfo`- Versions of required packages with debugging symbols and other debugging options enabled for use in troubleshooting.
  - `kernel-devel`, - Portions of the kernel tree needed to compile third party modules, such as network drivers.
  - `kernel-firmware`- Standard Red Hat Enterprise Linux distribution that has been recompiled to work with the Lustre kernel.
  - `kernel-headers`- Header files installed under /user/include and used when compiling user-space, kernel-related code.
  - `lustre-source`- Lustre software source code.
  - (Recommended) `perf`, `perf-debuginfo`, `python-perf`, `python-perf-debuginfo`- Linux performance analysis tools that have been compiled to match the Lustre kernel version.

### Environmental Requirements

Before installing the Lustre software, make sure the following environmental requirements are met.

- (Required) **Use the same user IDs (UID) and group IDs (GID) on all clients.** If use of supplemental groups is required, see [the section called “User/Group Upcall”](06.04-Programming%20Interfaces.md#usergroup-upcall) for information about supplementary user and group cache upcall (`identity_upcall`).
- (Recommended) **Provide remote shell access to clients.** It is recommended that all cluster nodes have remote shell client access to facilitate the use of Lustre configuration and monitoring scripts. Parallel Distributed SHell (pdsh) is preferable, although Secure SHell (SSH) is acceptable.
- (Recommended) **Ensure client clocks are synchronized.** The Lustre file system uses client clocks for timestamps. If clocks are out of sync between clients, files will appear with different time stamps when accessed by different clients. Drifting clocks can also cause problems by, for example, making it difficult to debug multi-node issues or correlate logs, which depend on timestamps. We recommend that you use Network Time Protocol (NTP) to keep client and server clocks in sync with each other. For more information about NTP, see: [https://www.ntp.org](https://www.ntp.org/).
- (Recommended) **Make sure security extensions** (such as the Novell AppArmor *security system) and **network packet filtering tools** (such as iptables) do not interfere with the Lustre software.

## Lustre Software Installation Procedure

### Caution

Before installing the Lustre software, back up ALL data. The Lustre software contains kernel modifications that interact with storage devices and may introduce security issues and data loss if not installed, configured, or administered properly.

To install the Lustre software from RPMs, complete the steps below.

1. Verify that all Lustre installation requirements have been met.

   - For hardware requirements, see [*Determining Hardware Configuration Requirements and Formatting Options*](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md).
   - For software and environmental requirements, see the section [the section called “ Preparing to Install the Lustre Software”](#installing-the-lustre-software)above.

2. Download the `e2fsprogs` RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases)repository.

3. Download the Lustre server RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases)repository. See [Table 5, “Packages Installed on Lustre Servers”](#table-5-packages-installed-on-lustre-servers)for a list of required packages.

4. Install the Lustre server and `e2fsprogs` packages on all Lustre servers (MGS, MDSs, and OSSs).

   1. Log onto a Lustre server as the `root` user

   2. Use the `yum` command to install the packages:

      

      ```
      # yum --nogpgcheck install pkg1.rpm pkg2.rpm ...
      ```

      

   3. Verify the packages are installed correctly:

      

      ```
      rpm -qa|egrep "lustre|wc"|sort
      ```

      

   4. Reboot the server.

   5. Repeat these steps on each Lustre server.

5. Download the Lustre client RPMs for your platform from the [Lustre Releases](https://wiki.whamcloud.com/display/PUB/Lustre+Releases)repository. See [Table 6, “Packages Installed on Lustre Clients”](#table-6-packages-installed-on-lustre-clients)for a list of required packages.

6. Install the Lustre client packages on all Lustre clients.

   ### Note

   The version of the kernel running on a Lustre client must be the same as the version of the `lustre-client-modules-` *ver*package being installed. If not, a compatible kernel must be installed on the client before the Lustre client packages are installed.

   1. Log onto a Lustre client as the root user.

   2. Use the `yum` command to install the packages:

      

      ```
      # yum --nogpgcheck install pkg1.rpm pkg2.rpm ...
      ```

      

   3. Verify the packages were installed correctly:

      

      ```
      # rpm -qa|egrep "lustre|kernel"|sort
      ```

      

   4. Reboot the client.

   5. Repeat these steps on each Lustre client.

To configure LNet, go to [*Configuring Lustre Networking (LNet)*](02.06-Configuring%20Lustre%20Networking%20(LNet).md). If default settings will be used for LNet, go to [*Configuring a Lustre File System*](02.07-Configuring%20a%20Lustre%20File%20System.md).


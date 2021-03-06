# Configuring Failover in a Lustre File System

**Table of Contents**

- [Configuring Failover in a Lustre File System](#configuring-failover-in-a-lustre-file-system)
  * [Setting Up a Failover Environment](#setting-up-a-failover-environment)
    + [Selecting Power Equipment](#selecting-power-equipment)
    + [Selecting Power Management Software](#selecting-power-management-software)
    + [Selecting High-Availability (HA) Software](#selecting-high-availability-ha-software)
  * [Preparing a Lustre File System for Failover](#preparing-a-lustre-file-system-for-failover)
  * [Administering Failover in a Lustre File System](#administering-failover-in-a-lustre-file-system)

This chapter describes how to configure failover in a Lustre file system. It includes:

- [the section called “Setting Up a Failover Environment”](#setting-up-a-failover-environment)
- [the section called “Preparing a Lustre File System for Failover”](#preparing-a-lustre-file-system-for-failover)
- [the section called “Administering Failover in a Lustre File System”](#administering-failover-in-a-lustre-file-system)

For an overview of failover functionality in a Lustre file system, see [*Understanding Failover in a Lustre File System*](02-Introducing%20the%20Lustre%20File%20System.md#understanding-failover-in-a-lustre-file-system).

## Setting Up a Failover Environment

The Lustre software provides failover mechanisms only at the layer of the Lustre file system. No failover functionality is provided for system-level components such as failing hardware or applications, or even for the entire failure of a node, as would typically be provided in a complete failover solution. Failover functionality such as node monitoring, failure detection, and resource fencing must be provided by external HA software, such as PowerMan or the open source Corosync and Pacemaker packages provided by Linux operating system vendors. Corosync provides support for detecting failures, and Pacemaker provides the actions to take once a failure has been detected.

### Selecting Power Equipment

Failover in a Lustre file system requires the use of a remote power control (RPC) mechanism, which comes in different configurations. For example, Lustre server nodes may be equipped with IPMI/BMC devices that allow remote power control. In the past, software or even “sneakerware” has been used, but these are not recommended. For recommended devices, refer to the list of supported RPC devices on the website for the PowerMan cluster power management utility:

<http://code.google.com/p/powerman/wiki/SupportedDevs>

### Selecting Power Management Software

Lustre failover requires RPC and management capability to verify that a failed node is shut down before I/O is directed to the failover node. This avoids double-mounting the two nodes and the risk of unrecoverable data corruption. A variety of power management tools will work. Two packages that have been commonly used with the Lustre software are PowerMan and Linux-HA (aka. STONITH ).

The PowerMan cluster power management utility is used to control RPC devices from a central location. PowerMan provides native support for several RPC varieties and Expect-like configuration simplifies the addition of new devices. The latest versions of PowerMan are available at:

<http://code.google.com/p/powerman/>

STONITH, or “Shoot The Other Node In The Head”, is a set of power management tools provided with the Linux-HA package prior to Red Hat Enterprise Linux 6. Linux-HA has native support for many power control devices, is extensible (uses Expect scripts to automate control), and provides the software to detect and respond to failures. With Red Hat Enterprise Linux 6, Linux-HA is being replaced in the open source community by the combination of Corosync and Pacemaker. For Red Hat Enterprise Linux subscribers, cluster management using CMAN is available from Red Hat.

### Selecting High-Availability (HA) Software

The Lustre file system must be set up with high-availability (HA) software to enable a complete Lustre failover solution. Except for PowerMan, the HA software packages mentioned above provide both power management and cluster management. For information about setting up failover with Pacemaker, see:

- Pacemaker Project website: <http://clusterlabs.org/>
- Article Using Pacemaker with a Lustre File System:<https://wiki.whamcloud.com/display/PUB/Using+Pacemaker+with+a+Lustre+File+System>

## Preparing a Lustre File System for Failover

To prepare a Lustre file system to be configured and managed as an HA system by a third-party HA application, each storage target (MGT, MGS, OST) must be associated with a second node to create a failover pair. This configuration information is then communicated by the MGS to a client when the client mounts the file system.

The per-target configuration is relayed to the MGS at mount time. Some rules related to this are:

- When a target is initially mounted, the MGS reads the configuration information from the target (such as mgt vs. ost, failnode, fsname) to configure the target into a Lustre file system. If the MGS is reading the initial mount configuration, the mounting node becomes that target's “primary” node.
- When a target is subsequently mounted, the MGS reads the current configuration from the target and, as needed, will reconfigure the MGS database target information

When the target is formatted using the `mkfs.lustre` command, the failover service node(s) for the target are designated using the `--servicenode` option. In the example below, an OST with index `0` in the file system `testfs` is formatted with two service nodes designated to serve as a failover pair:

```
mkfs.lustre --reformat --ost --fsname testfs --mgsnode=192.168.10.1@o3ib \  
              --index=0 --servicenode=192.168.10.7@o2ib \
              --servicenode=192.168.10.8@o2ib \  
              /dev/sdb
```

More than two potential service nodes can be designated for a target. The target can then be mounted on any of the designated service nodes.

When HA is configured on a storage target, the Lustre software enables multi-mount protection (MMP) on that storage target. MMP prevents multiple nodes from simultaneously mounting and thus corrupting the data on the target. For more about MMP, see [*Lustre File System Failover and Multiple-Mount Protection*](03.13-Lustre%20File%20System%20Failover%20and%20Multiple-Mount%20Protection.md).

If the MGT has been formatted with multiple service nodes designated, this information must be conveyed to the Lustre client in the mount command used to mount the file system. In the example below, NIDs for two MGSs that have been designated as service nodes for the MGT are specified in the mount command executed on the client:

```
mount -t lustre 10.10.120.1@tcp1:10.10.120.2@tcp1:/testfs /lustre/testfs
```

When a client mounts the file system, the MGS provides configuration information to the client for the MDT(s) and OST(s) in the file system along with the NIDs for all service nodes associated with each target and the service node on which the target is mounted. Later, when the client attempts to access data on a target, it will try the NID for each specified service node until it connects to the target.

## Administering Failover in a Lustre File System

For additional information about administering failover features in a Lustre file system, see:

- [the section called “ Specifying Failout/Failover Mode for OSTs”](03.02-Lustre%20Operations.md#specifying-failoutfailover-mode-for-osts)
- [the section called “ Specifying NIDs and Failover”](03.02-Lustre%20Operations.md#specifying-nids-and-failover)
- [the section called “ Changing the Address of a Failover Node”](03.03-Lustre%20Maintenance.md#changing-the-address-of-a-failover-node)
- [the section called “ mkfs.lustre”](06.07-System%20Configuration%20Utilities.md#mkfslustre)
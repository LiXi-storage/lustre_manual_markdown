# Index

## A
Access Control List (ACL), [Using ACLs](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#using-acls)<br>
	&emsp;examples, [Examples](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#examples)<br>
	&emsp;how they work, [How ACLs Work](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#how-acls-work)<br>
	&emsp;using, [Using ACLs with the Lustre Software](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#using-acls-with-the-lustre-software)<br>
audit<br>
	&emsp;change logs, [Audit with Changelogs](03.01-Monitoring%20a%20Lustre%20File%20System.md#audit-with-changelogs)<br><br>

## B
backup, [Backing up a File System](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-a-file-system)<br>
	&emsp;aborting recovery, [Aborting Recovery](03.03-Lustre%20Maintenance.md#aborting-recovery)<br>
	&emsp;index objects, [Backing Up an OST or MDT (Backend File System Level)](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-an-ost-or-mdt-backend-file-system-level)<br>
	&emsp;MDT file system, [Backing Up an OST or MDT (Backend File System Level)](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-an-ost-or-mdt-backend-file-system-level)<br>
	&emsp;MDT/OST device level, [Backing Up and Restoring an MDT or OST (ldiskfs Device Level)](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-and-restoring-an-mdt-or-ost-ldiskfs-device-level)<br>
	&emsp;new/changed files, [Backing up New/Changed Files to the Backup File System](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-newchanged-files-to-the-backup-file-system)<br>
	&emsp;OST and MDT, [Backing Up an OST or MDT](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-an-ost-or-mdt)<br>
	&emsp;OST config, [Backing Up OST Configuration Files](03.03-Lustre%20Maintenance.md#backing-up-ost-configuration-files)<br>
	&emsp;OST file system, [Backing Up an OST or MDT (Backend File System Level)](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#backing-up-an-ost-or-mdt-backend-file-system-level)<br>
	&emsp;restoring file system backup, [Restoring a File-Level Backup](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#restoring-a-file-level-backup)<br>
	&emsp;restoring OST config, [Restoring OST Configuration Files](03.03-Lustre%20Maintenance.md#restoring-ost-configuration-files)<br>
	&emsp;rsync, [Lustre_rsync](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#lustre_rsync)<br><br>
		&emsp;&emsp;examples, [lustre_rsync Examples](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#lustre_rsync-examples)<br>
		&emsp;&emsp;using, [Using Lustre_rsync](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#using-lustre_rsync)<br><br>

​	&emsp;using LVM, [Using LVM Snapshots with the Lustre File System](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#using-lvm-snapshots-with-the-lustre-file-system)<br><br>

​		&emsp;&emsp;creating, [Creating an LVM-based Backup File System](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#creating-an-lvm-based-backup-file-system)<br>
​		&emsp;&emsp;creating snapshots, [Creating Snapshot Volumes](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#creating-snapshot-volumes)<br>
​		&emsp;&emsp;deleting, [Deleting Old Snapshots](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#deleting-old-snapshots)<br>
​		&emsp;&emsp;resizing, [Changing Snapshot Volume Size](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#changing-snapshot-volume-size)<br>
​		&emsp;&emsp;restoring, [Restoring the File System From a Snapshot](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#restoring-the-file-system-from-a-snapshot)<br><br>

​	&emsp;ZFS to ldiskfs, [Migrate from a ZFS to an ldiskfs based filesystem](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#migrate-from-a-zfs-to-an-ldiskfs-based-filesystem), [Migrate from an ldiskfs to a ZFS based filesystem](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#migrate-from-an-ldiskfs-to-a-zfs-based-filesystem)<br>
​	&emsp;ZFS ZPL, [Migration Between ZFS and ldiskfs Target Filesystems](03.07-BackingUp%20and%20Restoring%20a%20File%20System.md#migration-between-zfs-and-ldiskfs-target-filesystems)<br><br>

barrier, [Global Write Barriers](03.19-Lustre%20ZFS%20Snapshots.md#global-write-barriers)<br><br>

​	&emsp;impose, [Impose Barrier](03.19-Lustre%20ZFS%20Snapshots.md#impose-barrier)<br>
​	&emsp;query, [Query Barrier](03.19-Lustre%20ZFS%20Snapshots.md#query-barrier)<br>
​	&emsp;remove, [Remove Barrier](03.19-Lustre%20ZFS%20Snapshots.md#remove-barrier)<br>
​	&emsp;rescan, [Rescan Barrier](03.19-Lustre%20ZFS%20Snapshots.md#rescan-barrier)<br><br>

###### benchmarking

<br>

​	&emsp;application profiling, [Collecting Application Profiling Information (stats-collect)](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#collecting-application-profiling-information-stats-collect)<br>
​	&emsp;local disk, [Testing Local Disk Performance](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-local-disk-performance)<br>
​	&emsp;MDS performance, [Testing MDS Performance (mds-survey)](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-mds-performance-mds-survey)<br>
​	&emsp;network, [Testing Network Performance](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-network-performance)<br>
​	&emsp;OST I/O, [Testing OST I/O Performance (ost-survey)](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-ost-io-performance-ost-survey)<br>
​	&emsp;OST performance, [Testing OST Performance (obdfilter-survey)](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-ost-performance-obdfilter-survey)<br>
​	&emsp;raw hardware with sgpdd-survey, [Testing I/O Performance of Raw Hardware (sgpdd-survey)](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-io-performance-of-raw-hardware-sgpdd-survey)<br>
​	&emsp;remote disk, [Testing Remote Disk Performance](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#testing-remote-disk-performance)<br>
​	&emsp;tuning storage, [Tuning Linux Storage Devices](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#tuning-linux-storage-devices)<br>
​	&emsp;with Lustre I/O Kit, [Using Lustre I/O Kit Tools](04.02-Benchmarking%20Lustre%20File%20System%20Performance%20(Lustre%20IO%20Kit).md#using-lustre-io-kit-tools)<br><br>

## C
change logs (see [monitoring](monitoring))<br>
commit on share, [Commit on Share](06.01-Lustre%20File%20System%20Recovery.md#commit-on-share)	<br><br>

​	&emsp;tuning, [Tuning Commit On Share](06.01-Lustre%20File%20System%20Recovery.md#tuning-commit-on-share)<br>
​	&emsp;working with, [Working with Commit on Share](06.01-Lustre%20File%20System%20Recovery.md#working-with-commit-on-share)<br><br>

configlogs, [Lustre Configuration Logs](03.19-Lustre%20ZFS%20Snapshots.md#lustre-configuration-logs)<br>

###### configuring,

 [Introduction](06.06-Configuration%20Files%20and%20Module%20Parameters.md#introduction)<br><br>

​	&emsp;adaptive timeouts, [Configuring Adaptive Timeouts](06.02-Lustre%20Parameters.md#configuring-adaptive-timeouts)<br>
​	&emsp;LNet options, [LNet Options](06.06-Configuration%20Files%20and%20Module%20Parameters.md#lnet-options)<br>
​	&emsp;module options, [Module Options](06.06-Configuration%20Files%20and%20Module%20Parameters.md#module-options)<br>
​	&emsp;multihome, [Multihome Server Example](02.06-Configuring%20Lustre%20Networking%20(LNet).md#multihome-server-example)<br>
​	&emsp;network<br><br>

​		&emsp;&emsp;forwarding, [forwarding ("")](06.06-Configuration%20Files%20and%20Module%20Parameters.md#forwarding-)<br>
​		&emsp;&emsp;rnet_htable_size, [rnet_htable_size](06.06-Configuration%20Files%20and%20Module%20Parameters.md#rnet_htable_size)<br>
​		&emsp;&emsp;routes, [routes ("")](06.06-Configuration%20Files%20and%20Module%20Parameters.md#routes-)<br>
​		&emsp;&emsp;SOCKLND, [SOCKLND Kernel TCP/IP LND](06.06-Configuration%20Files%20and%20Module%20Parameters.md#socklnd-kernel-tcpip-lnd)<br>
​		&emsp;&emsp;tcp, [networks ("tcp")](06.06-Configuration%20Files%20and%20Module%20Parameters.md#networks-tcp)<br><br>

​	&emsp;network topology, [Network Topology](06.06-Configuration%20Files%20and%20Module%20Parameters.md#network-topology)<br><br>

## D
debug<br><br>

​	&emsp;utilities, [Testing / Debugging Utilities](06.07-System%20Configuration%20Utilities.md#testing--debugging-utilities)<br><br>

debugging, [Diagnostic and Debugging Tools](05.03-Debugging%20a%20Lustre%20File%20System.md#diagnostic-and-debugging-tools)<br><br>

​	&emsp;admin tools, [Tools for Administrators and Developers](05.03-Debugging%20a%20Lustre%20File%20System.md#tools-for-administrators-and-developers)<br>
​	&emsp;developer tools, [Tools for Developers](05.03-Debugging%20a%20Lustre%20File%20System.md#tools-for-developers)<br>
​	&emsp;developers tools, [Lustre Debugging for Developers](05.03-Debugging%20a%20Lustre%20File%20System.md#lustre-debugging-for-developers)<br>
​	&emsp;disk contents, [Looking at Disk Content](05.03-Debugging%20a%20Lustre%20File%20System.md#looking-at-disk-content)<br>
​	&emsp;external tools, [External Debugging Tools](05.03-Debugging%20a%20Lustre%20File%20System.md#external-debugging-tools)<br>
​	&emsp;kernel debug log, [Controlling Information Written to the Kernel Debug Log](05.03-Debugging%20a%20Lustre%20File%20System.md#controlling-information-written-to-the-kernel-debug-log)<br>
​	&emsp;lctl example, [Sample lctl Run](05.03-Debugging%20a%20Lustre%20File%20System.md#sample-lctl-run)<br>
​	&emsp;memory leaks, [Finding Memory Leaks Using leak_finder.pl](05.03-Debugging%20a%20Lustre%20File%20System.md#finding-memory-leaks-using-leak_finderpl)<br>
​	&emsp;message format, [Understanding the Lustre Debug Messaging Format](05.03-Debugging%20a%20Lustre%20File%20System.md#understanding-the-lustre-debug-messaging-format)<br>
​	&emsp;procedure, [Lustre Debugging Procedures](05.03-Debugging%20a%20Lustre%20File%20System.md#lustre-debugging-procedures)<br>
​	&emsp;tools, [Lustre Debugging Tools](05.03-Debugging%20a%20Lustre%20File%20System.md#lustre-debugging-tools)<br>
​	&emsp;using lctl, [Using the lctl Tool to View Debug Messages](05.03-Debugging%20a%20Lustre%20File%20System.md#using-the-lctl-tool-to-view-debug-messages)<br>
​	&emsp;using strace, [Troubleshooting with strace](05.03-Debugging%20a%20Lustre%20File%20System.md#troubleshooting-with-strace)<br><br>

design (see [setup](#setup)<br>
DLC<br><br>

​	&emsp;Code Example, [Adding a route code example](06.08-LNet%20Configuration%20C-API.md#adding-a-route-code-example)<br><br>

dom, [Introduction to Data on MDT (DoM)](03.09-Data%20on%20MDT%20(DoM).md#introduction-to-data-on-mdt-dom), [User Commands](03.09-Data%20on%20MDT%20(DoM).md#user-commands), [DoM Stripe Size Restrictions](03.09-Data%20on%20MDT%20(DoM).md#dom-stripe-size-restrictions), [lfs getstripe for DoM files](03.09-Data%20on%20MDT%20(DoM).md#lfs-getstripe-for-dom-files), [lfs find for DoM files](03.09-Data%20on%20MDT%20(DoM).md#lfs-find-for-dom-files), [The dom_stripesize parameter](03.09-Data%20on%20MDT%20(DoM).md#the-dom_stripesize-parameter), [Disable DoM](03.09-Data%20on%20MDT%20(DoM).md#disable-dom)<br><br>

​	&emsp;disabledom, [Disable DoM](03.09-Data%20on%20MDT%20(DoM).md#disable-dom)<br>
​	&emsp;domstripesize, [DoM Stripe Size Restrictions](03.09-Data%20on%20MDT%20(DoM).md#dom-stripe-size-restrictions)<br>
​	&emsp;dom_stripesize, [The dom_stripesize parameter](03.09-Data%20on%20MDT%20(DoM).md#the-dom_stripesize-parameter)<br>
​	&emsp;intro, [Introduction to Data on MDT (DoM)](03.09-Data%20on%20MDT%20(DoM).md#introduction-to-data-on-mdt-dom)<br>
​	&emsp;lfsfind, [lfs find for DoM files](03.09-Data%20on%20MDT%20(DoM).md#lfs-find-for-dom-files)<br>
​	&emsp;lfsgetstripe, [lfs getstripe for DoM files](03.09-Data%20on%20MDT%20(DoM).md#lfs-getstripe-for-dom-files)<br>
​	&emsp;lfssetstripe, [lfs setstripe for DoM files](03.09-Data%20on%20MDT%20(DoM).md#lfs-setstripe-for-dom-files)<br>
​	&emsp;usercommands, [User Commands](03.09-Data%20on%20MDT%20(DoM).md#user-commands)<br><br>

## E
e2scan, [e2scan](06.07-System%20Configuration%20Utilities.md#e2scan)<br>
errors (see [troubleshooting](#t))<br>
## F
failover, [What is Failover?](02-Introducing%20the%20Lustre%20File%20System.md#what-is-failover), [Setting Up a Failover Environment](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md#setting-up-a-failover-environment)<br><br>

​	&emsp;and Lustre, [Failover Functionality in a Lustre File System](02-Introducing%20the%20Lustre%20File%20System.md#failover-functionality-in-a-lustre-file-system)<br>
​	&emsp;capabilities, [Failover Capabilities](02-Introducing%20the%20Lustre%20File%20System.md#failover-capabilities)<br>
​	&emsp;configuration, [Types of Failover Configurations](02-Introducing%20the%20Lustre%20File%20System.md#types-of-failover-configurations)<br>
​	&emsp;high-availability (HA) software, [Selecting High-Availability (HA) Software](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md#selecting-high-availability-ha-software)<br>
​	&emsp;MDT, [MDT Failover Configuration (Active/Passive)](02-Introducing%20the%20Lustre%20File%20System.md#mdt-failover-configuration-activepassive), [MDT Failover Configuration (Active/Active)](02-Introducing%20the%20Lustre%20File%20System.md#mdt-failover-configuration-activeactive)<br>
​	&emsp;OST, [OST Failover Configuration (Active/Active)](02-Introducing%20the%20Lustre%20File%20System.md#ost-failover-configuration-activeactive)<br>
​	&emsp;power control device, [Selecting Power Equipment](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md#selecting-power-equipment)<br>
​	&emsp;power management software, [Selecting Power Management Software](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md#selecting-power-management-software)<br>
​	&emsp;setup, [Preparing a Lustre File System for Failover](02.08-Configuring%20Failover%20in%20a%20Lustre%20File%20System.md#preparing-a-lustre-file-system-for-failover)<br><br>

feature overview<br><br>

​	&emsp;configuration, [Configuration](03.19-Lustre%20ZFS%20Snapshots.md#configuration)<br><br>

file layout<br><br>

​	&emsp;See striping, [Lustre File Layout (Striping) Considerations](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#lustre-file-layout-striping-considerations)<br><br>

filefrag, [filefrag](06.03-User%20Utilities.md#filefrag)<br>
fileset, [Fileset Feature](06.07-System%20Configuration%20Utilities.md#fileset-feature)<br>
fragmentation, [Description](06.03-User%20Utilities.md#description-1)<br><br>

## H
Hierarchical Storage Management (HSM)<br><br>

​	&emsp;introduction, [Introduction](03.15-Hierarchical%20Storage%20Management%20(HSM).md#introduction)<br>
High availability (see [failover](#f))<br>
HSM<br><br>

​	&emsp;agents, [Agents](03.15-Hierarchical%20Storage%20Management%20(HSM).md#agents)<br>
​	&emsp;agents and copytools, [Agents and copytool](03.15-Hierarchical%20Storage%20Management%20(HSM).md#agents-and-copytool)<br>
​	&emsp;archiveID backends, [Archive ID, multiple backends](03.15-Hierarchical%20Storage%20Management%20(HSM).md#archive-id-multiple-backends)<br>
​	&emsp;automatic restore, [Automatic restore](03.15-Hierarchical%20Storage%20Management%20(HSM).md#automatic-restore)<br>
​	&emsp;changelogs, [change logs](03.15-Hierarchical%20Storage%20Management%20(HSM).md#change-logs)<br>
​	&emsp;commands, [Commands](03.15-Hierarchical%20Storage%20Management%20(HSM).md#commands)<br>
​	&emsp;coordinator, [Coordinator](03.15-Hierarchical%20Storage%20Management%20(HSM).md#coordinator)<br>
​	&emsp;file states, [File states](03.15-Hierarchical%20Storage%20Management%20(HSM).md#file-states)<br>
​	&emsp;grace_delay, [grace_delay](03.15-Hierarchical%20Storage%20Management%20(HSM).md#grace_delay)<br>
​	&emsp;hsm_control, [hsm_controlpolicy](03.15-Hierarchical%20Storage%20Management%20(HSM).md#hsm_controlpolicy)<br>
​	&emsp;max_requests, [max_requests](03.15-Hierarchical%20Storage%20Management%20(HSM).md#max_requests)<br>
​	&emsp;policy, [policy](03.15-Hierarchical%20Storage%20Management%20(HSM).md#policy)<br>
​	&emsp;policy engine, [Policy engine](03.15-Hierarchical%20Storage%20Management%20(HSM).md#policy-engine)<br>
​	&emsp;registered agents, [Registered agents](03.15-Hierarchical%20Storage%20Management%20(HSM).md#registered-agents)<br>
​	&emsp;request monitoring, [Request monitoring](03.15-Hierarchical%20Storage%20Management%20(HSM).md#request-monitoring)<br>
​	&emsp;requests, [Requests](03.15-Hierarchical%20Storage%20Management%20(HSM).md#requests)<br>
​	&emsp;requirements, [Requirements](03.15-Hierarchical%20Storage%20Management%20(HSM).md#requirements)<br>
​	&emsp;robinhood, [Robinhood](03.15-Hierarchical%20Storage%20Management%20(HSM).md#robinhood)<br>
​	&emsp;setup, [Setup](03.15-Hierarchical%20Storage%20Management%20(HSM).md#setup)<br>
​	&emsp;timeout, [Timeout](03.15-Hierarchical%20Storage%20Management%20(HSM).md#timeout)<br>
​	&emsp;tuning, [Tuning](03.15-Hierarchical%20Storage%20Management%20(HSM).md#tuning)<br><br>

## I
I/O, [Handling Full OSTs](03.12-Managing%20the%20File%20System%20and%20IO.md#handling-full-osts)<br><br>

​	&emsp;adding an OST, [Adding an OST to a Lustre File System](03.12-Managing%20the%20File%20System%20and%20IO.md#adding-an-ost-to-a-lustre-file-system)<br>
​	&emsp;bringing OST online, [Returning an Inactive OST Back Online](03.12-Managing%20the%20File%20System%20and%20IO.md#returning-an-inactive-ost-back-online)<br>
​	&emsp;direct, [Performing Direct I/O](03.12-Managing%20the%20File%20System%20and%20IO.md#performing-direct-io)<br>
​	&emsp;disabling OST creates, [Disabling creates on a Full OST](03.12-Managing%20the%20File%20System%20and%20IO.md#disabling-creates-on-a-full-ost)<br>
​	&emsp;full OSTs, [Handling Full OSTs](03.12-Managing%20the%20File%20System%20and%20IO.md#handling-full-osts)<br>
​	&emsp;migrating data, [Migrating Data within a File System](03.12-Managing%20the%20File%20System%20and%20IO.md#migrating-data-within-a-file-system)<br>
​	&emsp;OST space usage, [Checking OST Space Usage](03.12-Managing%20the%20File%20System%20and%20IO.md#checking-ost-space-usage)<br>
​	&emsp;pools, [Creating and Managing OST Pools](03.12-Managing%20the%20File%20System%20and%20IO.md#creating-and-managing-ost-pools)<br><br>

imperative recovery, [Imperative Recovery](06.01-Lustre%20File%20System%20Recovery.md#imperative-recovery)<br><br>

​	&emsp;Configuration Suggestions, [Configuration Suggestions for Imperative Recovery](06.01-Lustre%20File%20System%20Recovery.md#configuration-suggestions-for-imperative-recovery)<br>
​	&emsp;MGS role, [MGS role](06.01-Lustre%20File%20System%20Recovery.md#mgs-role)<br>
​	&emsp;Tuning, [Tuning Imperative Recovery](06.01-Lustre%20File%20System%20Recovery.md#tuning-imperative-recovery)<br><br>

inodes<br><br>

​	&emsp;MDS, [Setting Formatting Options for an ldiskfs MDT](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-formatting-options-for-an-ldiskfs-mdt)<br>
​	&emsp;OST, [Setting Formatting Options for an ldiskfs OST](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-formatting-options-for-an-ldiskfs-ost)<br><br>

installing, [Steps to Installing the Lustre Software](02.01-Installation%20Overview.md#steps-to-installing-the-lustre-software)<br>

​	&emsp;preparation, [Preparing to Install the Lustre Software](02.05-Installing%20the%20Lustre%20Software.md#preparing-to-install-the-lustre-software)<br><br>

Introduction, [Introduction](03.19-Lustre%20ZFS%20Snapshots.md#introduction)<br>

​	&emsp;Requirements, [Requirements](03.19-Lustre%20ZFS%20Snapshots.md#requirements)<br><br>

ior-survey, [ior-survey](06.07-System%20Configuration%20Utilities.md#ior-survey)<br>
Isolation, [Isolating Clients to a Sub-directory Tree](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#isolating-clients-to-a-sub-directory-tree)<br><br>

​	&emsp;client identification, [Identifying Clients](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#identifying-clients)<br>
​	&emsp;configuring, [Configuring Isolation](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#configuring-isolation)<br>
​	&emsp;making permanent, [Making Isolation Permanent](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#making-isolation-permanent)<br><br>

## J
jobstats (see [monitoring](monitoring))<br>
## L
large_xattr<br><br>

​	&emsp;ea_inode, [File and File System Limits](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#file-and-file-system-limits), [Upgrading to Lustre Software Release 2.x (Major Release)](03.06-Upgrading%20a%20Lustre%20File%20System.md#upgrading-to-lustre-software-release-2x-major-release)<br><br>

lctl, [lctl](06.07-System%20Configuration%20Utilities.md#lctl)<br>
ldiskfs<br><br>

​	&emsp;formatting options, [Setting ldiskfs File System Formatting Options](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-ldiskfs-file-system-formatting-options)<br><br>

lfs, [lfs](06.03-User%20Utilities.md#lfs)<br>
lfs_migrate, [lfs_migrate](06.03-User%20Utilities.md#lfs_migrate)<br>
llodbstat, [llobdstat](06.07-System%20Configuration%20Utilities.md#llobdstat)<br>
llog_reader, [llog_reader](06.07-System%20Configuration%20Utilities.md#llog_reader)<br>
llstat, [llstat](06.07-System%20Configuration%20Utilities.md#llstat)<br>
llverdev, [llverdev](06.07-System%20Configuration%20Utilities.md#llverdev)<br>
ll_decode_filter_fid, [ll_decode_filter_fid](06.07-System%20Configuration%20Utilities.md#ll_decode_filter_fid)<br>
ll_recover_lost_found_objs, [ll_recover_lost_found_objs](06.07-System%20Configuration%20Utilities.md#ll_recover_lost_found_objs)<br>
LNet, [Introducing LNet](02-Introducing%20the%20Lustre%20File%20System.md#introducing-lnet), [Overview of LNet Module Parameters](02.06-Configuring%20Lustre%20Networking%20(LNet).md#overview-of-lnet-module-parameters), [Dynamically Configuring LNet Routes](03.04-ManagingLustreNetworking(LNet).md#dynamically-configuring-lnet-routes), [lustre_routes_config](03.04-ManagingLustreNetworking(LNet).md#lustre_routes_config), [lustre_routes_conversion](03.04-ManagingLustreNetworking(LNet).md#lustre_routes_conversion), [Route Configuration Examples](03.04-ManagingLustreNetworking(LNet).md#route-configuration-examples) (see [configuring](configuring))<br><br>

​	&emsp;best practice, [Best Practices for LNet Options](02.06-Configuring%20Lustre%20Networking%20(LNet).md#best-practices-for-lnet-options)<br>
​	&emsp;buffer yaml syntax, [Enable Routing and Adjust Router Buffer Configuration](02.06-Configuring%20Lustre%20Networking%20(LNet).md#enable-routing-and-adjust-router-buffer-configuration)<br>
​	&emsp;capi general information, [General API Information](06.08-LNet%20Configuration%20C-API.md#general-api-information)<br>
​	&emsp;capi input params, [API Common Input Parameters](06.08-LNet%20Configuration%20C-API.md#api-common-input-parameters)<br>
​	&emsp;capi output params, [API Common Output Parameters](06.08-LNet%20Configuration%20C-API.md#api-common-output-parameters)<br>
​	&emsp;capi return code, [API Return Code](06.08-LNet%20Configuration%20C-API.md#api-return-code)<br>
​	&emsp;cli, [Configuring LNet](02.06-Configuring%20Lustre%20Networking%20(LNet).md#configuring-lnet), [Displaying Global Settings](02.06-Configuring%20Lustre%20Networking%20(LNet).md#displaying-global-settings), [Adding, Deleting and Showing Networks](02.06-Configuring%20Lustre%20Networking%20(LNet).md#adding-deleting-and-showing-networks), [Manual Adding, Deleting and Showing Peers](02.06-Configuring%20Lustre%20Networking%20(LNet).md#manual-adding-deleting-and-showing-peers), [Adding, Deleting and Showing routes](02.06-Configuring%20Lustre%20Networking%20(LNet).md#adding-deleting-and-showing-routes), [Enabling and Disabling Routing](02.06-Configuring%20Lustre%20Networking%20(LNet).md#enabling-and-disabling-routing), [Showing routing information](02.06-Configuring%20Lustre%20Networking%20(LNet).md#showing-routing-information), [Configuring Routing Buffers](02.06-Configuring%20Lustre%20Networking%20(LNet).md#configuring-routing-buffers), [Importing YAML Configuration File](02.06-Configuring%20Lustre%20Networking%20(LNet).md#importing-yaml-configuration-file), [Exporting Configuration in YAML format](02.06-Configuring%20Lustre%20Networking%20(LNet).md#exporting-configuration-in-yaml-format), [Showing LNet Traffic Statistics](02.06-Configuring%20Lustre%20Networking%20(LNet).md#showing-lnet-traffic-statistics)<br><br>

​		&emsp;&emsp;asymmetrical route, [Asymmetrical Routes](02.06-Configuring%20Lustre%20Networking%20(LNet).md#asymmetrical-routes)<br>
​		&emsp;&emsp;dynamic discovery, [Dynamic Peer Discovery](02.06-Configuring%20Lustre%20Networking%20(LNet).md#dynamic-peer-discovery)<br><br>

​	&emsp;comments, [Including comments](02.06-Configuring%20Lustre%20Networking%20(LNet).md#including-comments)<br>
​	&emsp;Configuring LNet, [Configuring LNet via lnetctl](02.06-Configuring%20Lustre%20Networking%20(LNet).md#configuring-lnet-via-lnetctl)<br>
​	&emsp;cyaml, [Internal YAML Representation (cYAML)](06.08-LNet%20Configuration%20C-API.md#internal-yaml-representation-cyaml)
​	&emsp;error block, [Error Block](06.08-LNet%20Configuration%20C-API.md#error-block)<br>
​	&emsp;escaping commas with quotes, [Escaping commas with quotes](02.06-Configuring%20Lustre%20Networking%20(LNet).md#escaping-commas-with-quotes)<br>
​	&emsp;features, [Key Features of LNet](02-Introducing%20the%20Lustre%20File%20System.md#key-features-of-lnet)<br>
​	&emsp;hardware multi-rail configuration, [Hardware Based Multi-Rail Configurations with LNet](03.04-ManagingLustreNetworking(LNet).md#hardware-based-multi-rail-configurations-with-lnet)<br>
​	&emsp;InfiniBand load balancing, [Load Balancing with an InfiniBand* Network](03.04-ManagingLustreNetworking(LNet).md#load-balancing-with-an-infiniband-network)<br>
​	&emsp;ip2nets, [Setting the LNet Module ip2nets Parameter](02.06-Configuring%20Lustre%20Networking%20(LNet).md#setting-the-lnet-module-ip2nets-parameter)<br>
​	&emsp;lustre.conf, [Setting Up lustre.conf for Load Balancing](03.04-ManagingLustreNetworking(LNet).md#setting-up-lustreconf-for-load-balancing)<br>
​	&emsp;lustre_lnet_config_buf, [Adjusting Router Buffer Pools](06.08-LNet%20Configuration%20C-API.md#adjusting-router-buffer-pools)<br>
​	&emsp;lustre_lnet_config_net, [Adding a Network Interface](06.08-LNet%20Configuration%20C-API.md#adding-a-network-interface)<br>
​	&emsp;lustre_lnet_config_ni_system, [Configuring LNet](06.08-LNet%20Configuration%20C-API.md#configuring-lnet)<br>
​	&emsp;lustre_lnet_config_route, [Adding Routes](06.08-LNet%20Configuration%20C-API.md#adding-routes)<br>
​	&emsp;lustre_lnet_del_net, [Deleting a Network Interface](06.08-LNet%20Configuration%20C-API.md#deleting-a-network-interface)<br>
​	&emsp;lustre_lnet_del_route, [Deleting Routes](06.08-LNet%20Configuration%20C-API.md#deleting-routes)<br>
​	&emsp;lustre_lnet_enable_routing, [Enabling and Disabling Routing](06.08-LNet%20Configuration%20C-API.md#enabling-and-disabling-routing)<br>
​	&emsp;lustre_lnet_show stats, [Showing LNet Traffic Statistics](06.08-LNet%20Configuration%20C-API.md#showing-lnet-traffic-statistics)<br>
​	&emsp;lustre_lnet_show_buf, [Showing Routing information](06.08-LNet%20Configuration%20C-API.md#showing-routing-information)<br>
​	&emsp;lustre_lnet_show_net, [Showing Network Interfaces](06.08-LNet%20Configuration%20C-API.md#showing-network-interfaces)<br>
​	&emsp;lustre_lnet_show_route, [Showing Routes](06.08-LNet%20Configuration%20C-API.md#showing-routes)<br>
​	&emsp;lustre_yaml, [Adding/Deleting/Showing Parameters through a YAML Block](06.08-LNet%20Configuration%20C-API.md#addingdeletingshowing-parameters-through-a-yaml-block)<br>
​	&emsp;management, [Updating the Health Status of a Peer or Router](03.04-ManagingLustreNetworking(LNet).md#updating-the-health-status-of-a-peer-or-router)<br>
​	&emsp;module parameters, [Setting the LNet Module networks Parameter](02.06-Configuring%20Lustre%20Networking%20(LNet).md#setting-the-lnet-module-networks-parameter)<br>
​	&emsp;network yaml syntax, [Network Configuration](02.06-Configuring%20Lustre%20Networking%20(LNet).md#network-configuration)<br>
​	&emsp;proc, [Monitoring LNet](06.02-Lustre%20Parameters.md#monitoring-lnet)<br>
​	&emsp;route checker, [Configuring the Router Checker](02.06-Configuring%20Lustre%20Networking%20(LNet).md#configuring-the-router-checker)<br>
​	&emsp;router yaml syntax, [Route Configuration](02.06-Configuring%20Lustre%20Networking%20(LNet).md#route-configuration)<br>
​	&emsp;routes, [Setting the LNet Module routes Parameter](02.06-Configuring%20Lustre%20Networking%20(LNet).md#setting-the-lnet-module-routes-parameter)<br>
​	&emsp;routing example, [Routing Example](02.06-Configuring%20Lustre%20Networking%20(LNet).md#routing-example)<br>
​	&emsp;self-test, [LNet Self-Test Overview](04.01-Testing%20Lustre%20Network%20Performance%20(LNet%20Self-Test).md#lnet-self-test-overview)<br>
​	&emsp;show block, [Show Block](06.08-LNet%20Configuration%20C-API.md#show-block), [The LNet Configuration C-API](06.08-LNet%20Configuration%20C-API.md#the-lnet-configuration-c-api)<br>
​	&emsp;starting/stopping, [Starting and Stopping LNet](03.04-ManagingLustreNetworking(LNet).md#starting-and-stopping-lnet)<br>
​	&emsp;statistics yaml syntax, [Show Statistics](02.06-Configuring%20Lustre%20Networking%20(LNet).md#show-statistics)<br>
​	&emsp;supported networks, [Supported Network Types](02-Introducing%20the%20Lustre%20File%20System.md#supported-network-types)<br>
​	&emsp;testing, [Testing the LNet Configuration](02.06-Configuring%20Lustre%20Networking%20(LNet).md#testing-the-lnet-configuration)<br>
​	&emsp;tuning, [Tuning LNet Parameters](04.03-Tuning%20a%20Lustre%20File%20System.md#tuning-lnet-parameters)<br>
​	&emsp;understanding, [Introducing LNet](02-Introducing%20the%20Lustre%20File%20System.md#introducing-lnet)<br>
​	&emsp;using NID, [Using a Lustre Network Identifier (NID) to Identify a Node](02.06-Configuring%20Lustre%20Networking%20(LNet).md#using-a-lustre-network-identifier-nid-to-identify-a-node)<br>
​	&emsp;yaml syntax, [YAML Syntax](02.06-Configuring%20Lustre%20Networking%20(LNet).md#yaml-syntax)<br><br>

logs, [Snapshot Logs](03.19-Lustre%20ZFS%20Snapshots.md#snapshot-logs)<br>
lr_reader, [lr_reader](06.07-System%20Configuration%20Utilities.md#lr_reader)<br>
lshowmount, [lshowmount](06.07-System%20Configuration%20Utilities.md#lshowmount)<br>
lsom<br><br>

​	enablelsom, [Enable LSoM](03.10-Lazy%20Size%20on%20MDT%20(LSoM).md#enable-lsom)<br>
​	intro, [Introduction to Lazy Size on MDT (LSoM)](03.10-Lazy%20Size%20on%20MDT%20(LSoM).md#introduction-to-lazy-size-on-mdt-lsom)<br>
​	lfsgetsom, [lfs getsom for LSoM data](03.10-Lazy%20Size%20on%20MDT%20(LSoM).md#lfs-getsom-for-lsom-data)<br>
​	usercommands, [User Commands](03.10-Lazy%20Size%20on%20MDT%20(LSoM).md#user-commands)<br><br>

lst, [lst](06.07-System%20Configuration%20Utilities.md#lst)<br>
Lustre, [What a Lustre File System Is (and What It Isn't)](02-Introducing%20the%20Lustre%20File%20System.md#what-a-lustre-file-system-is-and-what-it-isnt)<br><br>

​	&emsp;at scale, [Lustre Cluster](02-Introducing%20the%20Lustre%20File%20System.md#lustre-cluster)<br>
​	&emsp;cluster, [Lustre Cluster](02-Introducing%20the%20Lustre%20File%20System.md#lustre-cluster)<br>
​	&emsp;components, [Lustre Components](02-Introducing%20the%20Lustre%20File%20System.md#lustre-components)<br>
​	&emsp;configuring, [Configuring a Simple Lustre File System](02.07-Configuring%20a%20Lustre%20File%20System.md#configuring-a-simple-lustre-file-system)<br><br>

​		&emsp;&emsp;additional options, [Additional Configuration Options](02.07-Configuring%20a%20Lustre%20File%20System.md#additional-configuration-options)<br>
​		&emsp;&emsp;for scale, [Scaling the Lustre File System](02.07-Configuring%20a%20Lustre%20File%20System.md#scaling-the-lustre-file-system)<br>
​		&emsp;&emsp;simple example, [Simple Lustre Configuration Example](02.07-Configuring%20a%20Lustre%20File%20System.md#simple-lustre-configuration-example)<br>
​		&emsp;&emsp;striping, [Changing Striping Defaults](02.07-Configuring%20a%20Lustre%20File%20System.md#changing-striping-defaults)<br>
​		&emsp;&emsp;utilities, [Using the Lustre Configuration Utilities](02.07-Configuring%20a%20Lustre%20File%20System.md#using-the-lustre-configuration-utilities)<br><br>

​	&emsp;features, [Lustre Features](02-Introducing%20the%20Lustre%20File%20System.md#lustre-features)<br>
​	&emsp;fileset, [Fileset Feature](06.07-System%20Configuration%20Utilities.md#fileset-feature)<br>
​	&emsp;I/O, [Lustre File System Storage and I/O](02-Introducing%20the%20Lustre%20File%20System.md#lustre-file-system-storage-and-io)<br>
​	&emsp;LNet, [Lustre Networking (LNet)](02-Introducing%20the%20Lustre%20File%20System.md#lustre-networking-lnet)<br>
​	&emsp;MGS, [Management Server (MGS)](02-Introducing%20the%20Lustre%20File%20System.md#management-server-mgs)<br>
​	&emsp;Networks, [Lustre Networks](02-Introducing%20the%20Lustre%20File%20System.md#lustre-networks)<br>
​	&emsp;requirements, [Lustre File System Components](02-Introducing%20the%20Lustre%20File%20System.md#lustre-file-system-components)<br>
​	&emsp;storage, [Lustre File System Storage and I/O](02-Introducing%20the%20Lustre%20File%20System.md#lustre-file-system-storage-and-io)<br>
​	&emsp;striping, [Lustre File System and Striping](02-Introducing%20the%20Lustre%20File%20System.md#lustre-file-system-and-striping)<br>
​	&emsp;upgrading (see [upgrading](#u))<br><br>

lustre<br><br>

​	&emsp;errors (see [troubleshooting](#t))<br>
​	&emsp;recovery (see [recovery](#r))<br>
​	&emsp;troubleshooting (see [troubleshooting](#t))<br><br>

lustre_rmmod.sh, [lustre_rmmod.sh](06.07-System%20Configuration%20Utilities.md#lustre_rmmodsh)<br>
lustre_rsync, [lustre_rsync](06.07-System%20Configuration%20Utilities.md#lustre_rsync)<br>
LVM (see [backup](#b))<br>
l_getidentity, [l_getidentity](06.07-System%20Configuration%20Utilities.md#l_getidentity)<br><br>

## M
maintenance, [Working with Inactive OSTs](03.03-Lustre%20Maintenance.md#working-with-inactive-osts), [Working with Inactive MDTs](03.03-Lustre%20Maintenance.md#working-with-inactive-mdts)<br><br>

​	&emsp;aborting recovery, [Aborting Recovery](03.03-Lustre%20Maintenance.md#aborting-recovery)<br>
​	&emsp;adding a OST, [Adding a New OST to a Lustre File System](03.03-Lustre%20Maintenance.md#adding-a-new-ost-to-a-lustre-file-system)<br>
​	&emsp;adding an MDT, [Adding a New MDT to a Lustre File System](03.03-Lustre%20Maintenance.md#adding-a-new-mdt-to-a-lustre-file-system)<br>
​	&emsp;backing up OST config, [Backing Up OST Configuration Files](03.03-Lustre%20Maintenance.md#backing-up-ost-configuration-files)<br>
​	&emsp;bringing OST online, [Returning an Inactive OST Back Online](03.12-Managing%20the%20File%20System%20and%20IO.md#returning-an-inactive-ost-back-online)<br>
​	&emsp;changing a NID, [Changing a Server NID](03.03-Lustre%20Maintenance.md#changing-a-server-nid)<br>
​	&emsp;changing failover node address, [Changing the Address of a Failover Node](03.03-Lustre%20Maintenance.md#changing-the-address-of-a-failover-node)<br>
​	&emsp;Clearing a config, [Clearing configuration](03.03-Lustre%20Maintenance.md#clearing-configuration)<br>
​	&emsp;finding nodes, [Finding Nodes in the Lustre File System](03.03-Lustre%20Maintenance.md#finding-nodes-in-the-lustre-file-system)<br>
​	&emsp;full OSTs, [Migrating Data within a File System](03.12-Managing%20the%20File%20System%20and%20IO.md#migrating-data-within-a-file-system)<br>
​	&emsp;identifying OST host, [Determining Which Machine is Serving an OST](03.03-Lustre%20Maintenance.md#determining-which-machine-is-serving-an-ost)<br>
​	&emsp;inactive MDTs, [Working with Inactive MDTs](03.03-Lustre%20Maintenance.md#working-with-inactive-mdts)L 2.4<br>
​	&emsp;inactive OSTs, [Working with Inactive OSTs](03.03-Lustre%20Maintenance.md#working-with-inactive-osts)<br>
​	&emsp;mounting a server, [Mounting a Server Without Lustre Service](03.03-Lustre%20Maintenance.md#mounting-a-server-without-lustre-service)<br>
​	&emsp;pools, [Creating and Managing OST Pools](03.12-Managing%20the%20File%20System%20and%20IO.md#creating-and-managing-ost-pools)<br>
​	&emsp;regenerating config logs, [Regenerating Lustre Configuration Logs](03.03-Lustre%20Maintenance.md#regenerating-lustre-configuration-logs)<br>
​	&emsp;reintroducing an OSTs, [Returning a Deactivated OST to Service](03.03-Lustre%20Maintenance.md#returning-a-deactivated-ost-to-service)<br>
​	&emsp;removing an MDT, [Removing an MDT from the File System](03.03-Lustre%20Maintenance.md#removing-an-mdt-from-the-file-system)<br>
​	&emsp;removing an OST, [Removing and Restoring MDTs and OSTs](03.03-Lustre%20Maintenance.md#removing-and-restoring-mdts-and-osts), [Removing an OST from the File System](03.03-Lustre%20Maintenance.md#removing-an-ost-from-the-file-system)<br>
​	&emsp;restoring an OST, [Removing and Restoring MDTs and OSTs](03.03-Lustre%20Maintenance.md#removing-and-restoring-mdts-and-osts)<br>
​	&emsp;restoring OST config, [Restoring OST Configuration Files](03.03-Lustre%20Maintenance.md#restoring-ost-configuration-files)<br>
​	&emsp;separate a combined MGS/MDT, [Separate a combined MGS/MDT](03.03-Lustre%20Maintenance.md#separate-a-combined-mgsmdt)<br><br>

MDT<br><br>

​	&emsp;multiple MDSs, [Upgrading to Lustre Software Release 2.x (Major Release)](03.06-Upgrading%20a%20Lustre%20File%20System.md#upgrading-to-lustre-software-release-2x-major-release)<br><br>

migrating metadata, [Migrating Metadata within a Filesystem](03.12-Managing%20the%20File%20System%20and%20IO.md#migrating-metadata-within-a-filesystem), [Whole Directory Migration](03.12-Managing%20the%20File%20System%20and%20IO.md#whole-directory-migration), [Striped Directory Migration](03.12-Managing%20the%20File%20System%20and%20IO.md#striped-directory-migration)
mkfs.lustre, [mkfs.lustre](06.07-System%20Configuration%20Utilities.md#mkfslustre)<br>

###### monitoring, 

[Lustre Changelogs](03.01-Monitoring%20a%20Lustre%20File%20System.md#lustre-changelogs), [Lustre Jobstats](03.01-Monitoring%20a%20Lustre%20File%20System.md#lustre-jobstats)<br><br>

​	&emsp;additional tools, [Other Monitoring Options](03.01-Monitoring%20a%20Lustre%20File%20System.md#other-monitoring-options)<br>
​	&emsp;change logs, [Lustre Changelogs](03.01-Monitoring%20a%20Lustre%20File%20System.md#lustre-changelogs), [Working with Changelogs](03.01-Monitoring%20a%20Lustre%20File%20System.md#working-with-changelogs)<br>
​	&emsp;jobstats, [Lustre Jobstats](03.01-Monitoring%20a%20Lustre%20File%20System.md#lustre-jobstats), [How Jobstats Works](03.01-Monitoring%20a%20Lustre%20File%20System.md#how-jobstats-works), [Enable/Disable Jobstats](03.01-Monitoring%20a%20Lustre%20File%20System.md#enabledisable-jobstats), [Check Job Stats](03.01-Monitoring%20a%20Lustre%20File%20System.md#check-job-stats), [Clear Job Stats](03.01-Monitoring%20a%20Lustre%20File%20System.md#clear-job-stats), [Configure Auto-cleanup Interval](03.01-Monitoring%20a%20Lustre%20File%20System.md#configure-auto-cleanup-interval)<br>
​	&emsp;Lustre Monitoring Tool, [Lustre Monitoring Tool (LMT)](03.01-Monitoring%20a%20Lustre%20File%20System.md#lustre-monitoring-tool-lmt)<br><br>

mount, [mount](06.03-User%20Utilities.md#mount)<br>
mount.lustre, [mount.lustre](06.07-System%20Configuration%20Utilities.md#mountlustre)<br>
MR<br><br>

​	&emsp;addremotepeers, [Adding Remote Peers that are Multi-Rail Capable](03.05-LNet%20Software%20Multi-Rail%202.10.md#adding-remote-peers-that-are-multi-rail-capable)<br>
​	&emsp;configuring, [Configuring Multi-Rail](03.05-LNet%20Software%20Multi-Rail%202.10.md#configuring-multi-rail)<br>
​	&emsp;deleteinterfaces, [Deleting Network Interfaces](03.05-LNet%20Software%20Multi-Rail%202.10.md#deleting-network-interfaces)<br>
​	&emsp;deleteremotepeers, [Deleting Remote Peers](03.05-LNet%20Software%20Multi-Rail%202.10.md#deleting-remote-peers)<br>
​	&emsp;health, [LNet Health](03.05-LNet%20Software%20Multi-Rail%202.10.md#lnet-health)<br>
​	&emsp;mrhealth<br><br>

​		&emsp;&emsp;display, [Displaying Information](03.05-LNet%20Software%20Multi-Rail%202.10.md#displaying-information)<br>
​		&emsp;&emsp;failuretypes, [Failure Types and Behavior](03.05-LNet%20Software%20Multi-Rail%202.10.md#failure-types-and-behavior)<br>
​		&emsp;&emsp;initialsetup, [Initial Settings Recommendations](03.05-LNet%20Software%20Multi-Rail%202.10.md#initial-settings-recommendations)<br>
​		&emsp;&emsp;interface, [User Interface](03.05-LNet%20Software%20Multi-Rail%202.10.md#user-interface)<br>
​		&emsp;&emsp;value, [Health Value](03.05-LNet%20Software%20Multi-Rail%202.10.md#health-value)<br>

​	&emsp;mrrouting, [Notes on routing with Multi-Rail](03.05-LNet%20Software%20Multi-Rail%202.10.md#notes-on-routing-with-multi-rail)<br><br>

​		&emsp;&emsp;routingex, [Multi-Rail Cluster Example](03.05-LNet%20Software%20Multi-Rail%202.10.md#multi-rail-cluster-example)<br>
​		&emsp;&emsp;routingmixed, [Mixed Multi-Rail/Non-Multi-Rail Cluster](03.05-LNet%20Software%20Multi-Rail%202.10.md#mixed-multi-railnon-multi-rail-cluster)<br>
​		&emsp;&emsp;routingresiliency, [Utilizing Router Resiliency](03.05-LNet%20Software%20Multi-Rail%202.10.md#utilizing-router-resiliency)<br><br>

​	&emsp;multipleinterfaces, [Configure Multiple Interfaces on the Local Node](03.05-LNet%20Software%20Multi-Rail%202.10.md#configure-multiple-interfaces-on-the-local-node)<br>
​	&emsp;overview, [Multi-Rail Overview](03.05-LNet%20Software%20Multi-Rail%202.10.md#multi-rail-overview)<br><br>

multiple-mount protection, [Overview of Multiple-Mount Protection](03.13-Lustre%20File%20System%20Failover%20and%20Multiple-Mount%20Protection.md#overview-of-multiple-mount-protection)<br><br>

## O
obdfilter-survey, [obdfilter-survey](06.07-System%20Configuration%20Utilities.md#obdfilter-survey)<br>
operations, [Mounting by Label](03.02-Lustre%20Operations.md#mounting-by-label), [Snapshot Operations](03.19-Lustre%20ZFS%20Snapshots.md#snapshot-operations)<br><br>

​	&emsp;create, [Creating a Snapshot](03.19-Lustre%20ZFS%20Snapshots.md#creating-a-snapshot)<br>
​	&emsp;degraded OST RAID, [Handling Degraded OST RAID Arrays](03.02-Lustre%20Operations.md#handling-degraded-ost-raid-arrays)<br>
​	&emsp;delete, [Delete a Snapshot](03.19-Lustre%20ZFS%20Snapshots.md#delete-a-snapshot)<br>
​	&emsp;erasing a file system, [Erasing a File System](03.02-Lustre%20Operations.md#erasing-a-file-system)<br>
​	&emsp;failover, [Specifying Failout/Failover Mode for OSTs](03.02-Lustre%20Operations.md#specifying-failoutfailover-mode-for-osts), [Specifying NIDs and Failover](03.02-Lustre%20Operations.md#specifying-nids-and-failover)<br>
​	&emsp;identifying OSTs, [Identifying To Which Lustre File an OST Object Belongs](03.02-Lustre%20Operations.md#identifying-to-which-lustre-file-an-ost-object-belongs)<br>
​	&emsp;list, [List Snapshots](03.19-Lustre%20ZFS%20Snapshots.md#list-snapshots)<br>
​	&emsp;mkdir, [Creating a directory striped across multiple MDTs](03.02-Lustre%20Operations.md#creating-a-directory-striped-across-multiple-mdts)<br>
​	&emsp;modify, [Modify Snapshot Attributes](03.19-Lustre%20ZFS%20Snapshots.md#modify-snapshot-attributes)<br>
​	&emsp;mount, [Mounting a Snapshot](03.19-Lustre%20ZFS%20Snapshots.md#mounting-a-snapshot)<br>
​	&emsp;mounting, [Mounting a Server](03.02-Lustre%20Operations.md#mounting-a-server)<br>
​	&emsp;mounting by label, [Mounting by Label](03.02-Lustre%20Operations.md#mounting-by-label)<br>
​	&emsp;multiple file systems, [Running Multiple Lustre File Systems](03.02-Lustre%20Operations.md#running-multiple-lustre-file-systems)<br>
​	&emsp;parameters, [Setting and Retrieving Lustre Parameters](03.02-Lustre%20Operations.md#setting-and-retrieving-lustre-parameters)<br>
​	&emsp;reclaiming space, [Reclaiming Reserved Disk Space](03.02-Lustre%20Operations.md#reclaiming-reserved-disk-space)<br>
​	&emsp;remote directory, [Creating a sub-directory on a given MDT](03.02-Lustre%20Operations.md#creating-a-sub-directory-on-a-given-mdt)<br>
​	&emsp;replacing an OST or MDS, [Replacing an Existing OST or MDT](03.02-Lustre%20Operations.md#replacing-an-existing-ost-or-mdt)<br>
​	&emsp;setdirstripe, [Creating a directory striped across multiple MDTs](03.02-Lustre%20Operations.md#creating-a-directory-striped-across-multiple-mdts)<br>
​	&emsp;shutdownLustre, [Stopping the Filesystem](03.02-Lustre%20Operations.md#stopping-the-filesystem)<br>
​	&emsp;starting, [Starting Lustre](03.02-Lustre%20Operations.md#starting-lustre)<br>
​	&emsp;striped directory, [Creating a directory striped across multiple MDTs](03.02-Lustre%20Operations.md#creating-a-directory-striped-across-multiple-mdts)<br>
​	&emsp;unmount, [Unmounting a Snapshot](03.19-Lustre%20ZFS%20Snapshots.md#unmounting-a-snapshot)<br>
​	&emsp;unmounting, [Unmounting a Specific Target on a Server](03.02-Lustre%20Operations.md#unmounting-a-specific-target-on-a-server)<br><br>

ost-survey, [ost-survey](06.07-System%20Configuration%20Utilities.md#ost-survey)<br><br>

## P
performance (see [benchmarking](#benchmarking))<br>
pings<br><br>

​	&emsp;evict_client, [Client Death Notification](06.01-Lustre%20File%20System%20Recovery.md#client-death-notification)
​	&emsp;suppress_pings, ["suppress_pings" Kernel Module Parameter](06.01-Lustre%20File%20System%20Recovery.md#suppress_pings-kernel-module-parameter)<br><br>

plot-llstat, [plot-llstat](06.07-System%20Configuration%20Utilities.md#plot-llstat)<br>
pools, [Creating and Managing OST Pools](03.12-Managing%20the%20File%20System%20and%20IO.md#creating-and-managing-ost-pools)<br><br>

​	&emsp;usage tips, [Tips for Using OST Pools](03.12-Managing%20the%20File%20System%20and%20IO.md#tips-for-using-ost-pools)<br><br>

proc<br><br>

​	&emsp;adaptive timeouts, [Configuring Adaptive Timeouts](06.02-Lustre%20Parameters.md#configuring-adaptive-timeouts)<br>
​	&emsp;block I/O, [Monitoring the OST Block I/O Stream](06.02-Lustre%20Parameters.md#monitoring-the-ost-block-io-stream)<br>
​	&emsp;client metadata performance, [Tuning the Client Metadata RPC Stream](06.02-Lustre%20Parameters.md#tuning-the-client-metadata-rpc-stream)<br>
​	&emsp;client stats, [Monitoring Client Activity](06.02-Lustre%20Parameters.md#monitoring-client-activity)<br>
​	&emsp;configuring adaptive timeouts, [Configuring Adaptive Timeouts](06.02-Lustre%20Parameters.md#configuring-adaptive-timeouts)<br>
​	&emsp;debug, [Enabling and Interpreting Debugging Logs](06.02-Lustre%20Parameters.md#enabling-and-interpreting-debugging-logs)<br>
​	&emsp;free space, [Allocating Free Space on OSTs](06.02-Lustre%20Parameters.md#allocating-free-space-on-osts)<br>
​	&emsp;LNet, [Monitoring LNet](06.02-Lustre%20Parameters.md#monitoring-lnet)<br>
​	&emsp;locking, [Configuring Locking](06.02-Lustre%20Parameters.md#configuring-locking)<br>
​	&emsp;OSS journal, [Enabling OSS Asynchronous Journal Commit](06.02-Lustre%20Parameters.md#enabling-oss-asynchronous-journal-commit)<br>
​	&emsp;read cache, [Tuning OSS Read Cache](06.02-Lustre%20Parameters.md#tuning-oss-read-cache)<br>
​	&emsp;read/write survey, [Monitoring Client Read-Write Offset Statistics](06.02-Lustre%20Parameters.md#monitoring-client-read-write-offset-statistics), [Monitoring Client Read-Write Extent Statistics](06.02-Lustre%20Parameters.md#monitoring-client-read-write-extent-statistics)<br>
​	&emsp;readahead, [Tuning File Readahead and Directory Statahead](06.02-Lustre%20Parameters.md#tuning-file-readahead-and-directory-statahead)<br>
​	&emsp;RPC tunables, [Tuning the Client I/O RPC Stream](06.02-Lustre%20Parameters.md#tuning-the-client-io-rpc-stream)<br>
​	&emsp;static timeouts, [Setting Static Timeouts](06.02-Lustre%20Parameters.md#setting-static-timeouts)<br>
​	&emsp;thread counts, [Setting MDS and OSS Thread Counts](06.02-Lustre%20Parameters.md#setting-mds-and-oss-thread-counts)<br>
​	&emsp;watching RPC, [Monitoring the Client RPC Stream](06.02-Lustre%20Parameters.md#monitoring-the-client-rpc-stream)<br><br>

profiling (see [benchmarking](#benchmarking))<br>
programming<br><br>

​	&emsp;upcall, [User/Group Upcall](06.04-Programming%20Interfaces.md#usergroup-upcall)<br>

## Q
Quotas<br><br>

​	&emsp;allocating, [Quota Allocation](03.14-Configuring%20and%20Managing%20Quotas.md#quota-allocation)<br>
​	&emsp;configuring, [Working with Quotas](03.14-Configuring%20and%20Managing%20Quotas.md#working-with-quotas)<br>
​	&emsp;creating, [Quota Administration](03.14-Configuring%20and%20Managing%20Quotas.md#quota-administration)<br>
​	&emsp;enabling disk, [Enabling Disk Quotas](03.14-Configuring%20and%20Managing%20Quotas.md#enabling-disk-quotas)<br>
​	&emsp;Interoperability, [Quotas and Version Interoperability](03.14-Configuring%20and%20Managing%20Quotas.md#quotas-and-version-interoperability)<br>
​	&emsp;known issues, [Granted Cache and Quota Limits](03.14-Configuring%20and%20Managing%20Quotas.md#granted-cache-and-quota-limits)<br>
​	&emsp;statistics, [Lustre Quota Statistics](03.14-Configuring%20and%20Managing%20Quotas.md#lustre-quota-statistics)<br>
​	&emsp;verifying, [Quota Verification](03.14-Configuring%20and%20Managing%20Quotas.md#quota-verification)<br><br>

## R
recovery, [Recovery Overview](06.01-Lustre%20File%20System%20Recovery.md#recovery-overview)<br><br>

​	&emsp;client eviction, [Client Eviction](06.01-Lustre%20File%20System%20Recovery.md#client-eviction)<br>
​	&emsp;client failure, [Client Failure](06.01-Lustre%20File%20System%20Recovery.md#client-failure)<br>
​	&emsp;commit on share (see [commit on share](06.01-Lustre%20File%20System%20Recovery.md#commit-on-share))<br>
​	&emsp;corruption of backing ldiskfs file system, [Recovering from Errors or Corruption on a Backing ldiskfs File System](05.02-Troubleshooting%20Recovery.md#recovering-from-errors-or-corruption-on-a-backing-ldiskfs-file-system)<br>
​	&emsp;corruption of Lustre file system, [Recovering from Corruption in the Lustre File System](05.02-Troubleshooting%20Recovery.md#recovering-from-corruption-in-the-lustre-file-system)<br>
​	&emsp;failed recovery, [Failed Recovery](06.01-Lustre%20File%20System%20Recovery.md#failed-recovery)<br>
​	&emsp;LFSCK, [Checking the file system with LFSCK](05.02-Troubleshooting%20Recovery.md#checking-the-file-system-with-lfsck)<br>
​	&emsp;locks, [Lock Recovery](06.01-Lustre%20File%20System%20Recovery.md#lock-recovery)<br>
​	&emsp;MDS failure, [MDS Failure (Failover)](06.01-Lustre%20File%20System%20Recovery.md#mds-failure-failover)<br>
​	&emsp;metadata replay, [Metadata Replay](06.01-Lustre%20File%20System%20Recovery.md#metadata-replay)<br>
​	&emsp;network, [Network Partition](06.01-Lustre%20File%20System%20Recovery.md#network-partition)<br>
​	&emsp;oiscrub, [Checking the file system with LFSCK](05.02-Troubleshooting%20Recovery.md#checking-the-file-system-with-lfsck)<br>
​	&emsp;orphaned objects, [Working with Orphaned Objects](05.02-Troubleshooting%20Recovery.md#working-with-orphaned-objects)<br>
​	&emsp;OST failure, [OST Failure (Failover)](06.01-Lustre%20File%20System%20Recovery.md#ost-failure-failover)<br>
​	&emsp;unavailable OST, [Recovering from an Unavailable OST](05.02-Troubleshooting%20Recovery.md#recovering-from-an-unavailable-ost)<br>
​	&emsp;VBR (see [version-based recovery](06.01-Lustre%20File%20System%20Recovery.md#version-based-recovery))<br><br>

reporting bugs (see [troubleshooting](#t))<br>
restoring (see [backup](#b))<br>
root squash, [Using Root Squash](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#using-root-squash)<br><br>

​	&emsp;configuring, [Configuring Root Squash](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#configuring-root-squash)<br>
​	&emsp;enabling, [Enabling and Tuning Root Squash](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#enabling-and-tuning-root-squash)<br>
​	&emsp;tips, [Tips on Using Root Squash](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#tips-on-using-root-squash)<br><br>

round-robin algorithm, [Managing Free Space](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#managing-free-space)<br>
routerstat, [routerstat](06.07-System%20Configuration%20Utilities.md#routerstat)<br>
rsync (see [backup](#b))<br><br>

## S
selinux policy check, [Checking SELinux Policy Enforced by Lustre Clients](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#checking-selinux-policy-enforced-by-lustre-clients)<br><br>

​	&emsp;determining, [Determining SELinux Policy Info](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#determining-selinux-policy-info)<br>
​	&emsp;enforcing, [Enforcing SELinux Policy Check](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#enforcing-selinux-policy-check)<br>
​	&emsp;making permanent, [Making SELinux Policy Check Permanent](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#making-selinux-policy-check-permanent)<br>
​	&emsp;sending client, [Sending SELinux Status Info from Clients](03.18-Managing%20Security%20in%20a%20Lustre%20File%20System.md#sending-selinux-status-info-from-clients)<br><br>

setup, [Hardware Considerations](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#hardware-considerations)<br><br>

​	&emsp;hardware, [Hardware Considerations](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#hardware-considerations)<br>
​	&emsp;inodes, [Setting Formatting Options for an ldiskfs MDT](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-formatting-options-for-an-ldiskfs-mdt)<br>
​	&emsp;ldiskfs, [Setting ldiskfs File System Formatting Options](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#setting-ldiskfs-file-system-formatting-options)<br>
​	&emsp;limits, [File and File System Limits](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#file-and-file-system-limits)<br>
​	&emsp;MDT, [MGT and MDT Storage Hardware Considerations](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#mgt-and-mdt-storage-hardware-considerations), [Determining MDT Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-mdt-space-requirements)<br>
​	&emsp;memory, [Determining Memory Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-memory-requirements)<br><br>

​		&emsp;&emsp;client, [Client Memory Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#client-memory-requirements)<br>
​		&emsp;&emsp;MDS, MDS Memory Requirements, [Calculating MDS Memory Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#calculating-mds-memory-requirements)<br>
​		&emsp;&emsp;OSS, OSS Memory Requirements, [Calculating OSS Memory Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#calculating-oss-memory-requirements)<br><br>

​	&emsp;MGT, [Determining MGT Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-mgt-space-requirements)<br>
​	&emsp;network, [Implementing Networks To Be Used by the Lustre File System](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#implementing-networks-to-be-used-by-the-lustre-file-system)<br>
​	&emsp;OST, [OST Storage Hardware Considerations](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#ost-storage-hardware-considerations), [Determining OST Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-ost-space-requirements)<br>
​	&emsp;space, [Determining Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-space-requirements)<br><br>

sgpdd-survey, [sgpdd-survey](06.07-System%20Configuration%20Utilities.md#sgpdd-survey)<br><br>

###### space,

 [How Lustre File System Striping Works](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#how-lustre-file-system-striping-works)<br><br>

​	&emsp;considerations, [Lustre File Layout (Striping) Considerations](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#lustre-file-layout-striping-considerations)<br>
​	&emsp;determining MDT requirements, [Determining MDT Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-mdt-space-requirements)<br>
​	&emsp;determining MGT requirements, [Determining MGT Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-mgt-space-requirements)<br>
​	&emsp;determining OST requirements, [Determining OST Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-ost-space-requirements)<br>
​	&emsp;determining requirements, [Determining Space Requirements](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#determining-space-requirements)<br>
​	&emsp;free space, [Managing Free Space](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#managing-free-space)<br>
​	&emsp;location weighting, [Adjusting the Weighting Between Free Space and Location](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#adjusting-the-weighting-between-free-space-and-location)<br>
​	&emsp;striping, [How Lustre File System Striping Works](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#how-lustre-file-system-striping-works)<br><br>

stats-collect, [stats-collect](06.07-System%20Configuration%20Utilities.md#stats-collect)<br>
storage<br><br>

​	&emsp;configuring, [Selecting Storage for the MDT and OSTs](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#selecting-storage-for-the-mdt-and-osts)<br><br>

​		&emsp;&emsp;external journal, [Choosing Parameters for an External Journal](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#choosing-parameters-for-an-external-journal)<br>
​		&emsp;&emsp;for best practice, [Reliability Best Practices](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#reliability-best-practices)<br>
​		&emsp;&emsp;for mkfs, [Computing file system parameters for mkfs](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#computing-file-system-parameters-for-mkfs)<br>
​		&emsp;&emsp;MDT, [Metadata Target (MDT)](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#metadata-target-mdt)<br>
​		&emsp;&emsp;OST, [Object Storage Server (OST)](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#object-storage-server-ost)<br>
​		&emsp;&emsp;RAID options, [Formatting Options for ldiskfs RAID Devices](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#formatting-options-for-ldiskfs-raid-devices)<br>
​		&emsp;&emsp;SAN, [Connecting a SAN to a Lustre File System](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#connecting-a-san-to-a-lustre-file-system)<br><br>

​	&emsp;performance tradeoffs, [Performance Tradeoffs](02.03-Configuring%20Storage%20on%20a%20Lustre%20File%20System.md#performance-tradeoffs)<br><br>

striping (see [space](#space))<br><br>

​	&emsp;allocations, [Stripe Allocation Methods](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#stripe-allocation-methods)<br>
​	&emsp;configuration, [Setting the File Layout/Striping Configuration (lfs setstripe)](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#setting-the-file-layoutstriping-configuration-lfs-setstripe)<br>
​	&emsp;considerations, [Lustre File Layout (Striping) Considerations](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#lustre-file-layout-striping-considerations)<br>
​	&emsp;count, [Setting the Stripe Count](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#setting-the-stripe-count)<br>
​	&emsp;getting information, [Retrieving File Layout/Striping Information (getstripe)](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#retrieving-file-layoutstriping-information-getstripe)<br>
​	&emsp;how it works, [How Lustre File System Striping Works](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#how-lustre-file-system-striping-works)<br>
​	&emsp;metadata, [Creating a directory striped across multiple MDTs](03.02-Lustre%20Operations.md#creating-a-directory-striped-across-multiple-mdts)<br>
​	&emsp;on specific OST, [Creating a File on a Specific OST](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#creating-a-file-on-a-specific-ost)<br>
​	&emsp;overview, [Lustre File System and Striping](02-Introducing%20the%20Lustre%20File%20System.md#lustre-file-system-and-striping)<br>
​	&emsp;per directory, [Setting the Striping Layout for a Directory](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#setting-the-striping-layout-for-a-directory)<br>
​	&emsp;per file system, [Setting the Striping Layout for a File System](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#setting-the-striping-layout-for-a-file-system)<br>
​	&emsp;PFL, [Progressive File Layout(PFL)](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#progressive-file-layoutpfl)<br>
​	&emsp;remote directories, [Locating the MDT for a remote directory](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#locating-the-mdt-for-a-remote-directory)<br>
​	&emsp;round-robin algorithm, [Managing Free Space](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#managing-free-space)<br>
​	&emsp;size, [Choosing a Stripe Size](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#choosing-a-stripe-size)<br>
​	&emsp;weighted algorithm, [Managing Free Space](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#managing-free-space)<br>
​	&emsp;wide striping, [Lustre Striping Internals](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#lustre-striping-internals)<br>

suppressing pings, [Suppressing Pings](06.01-Lustre%20File%20System%20Recovery.md#suppressing-pings)<br>

## T
troubleshooting, [Lustre Error Messages](05.01-Lustre%20File%20System%20Troubleshooting.md#lustre-error-messages)<br><br>

​	&emsp;'Address already in use', [Handling/Debugging "Bind: Address already in use" Error](05.01-Lustre%20File%20System%20Troubleshooting.md#handlingdebugging-bind-address-already-in-use-error)<br>
​	&emsp;'Error -28', [Handling/Debugging Error "- 28"](05.01-Lustre%20File%20System%20Troubleshooting.md#handlingdebugging-error---28)<br>
​	&emsp;common problems, [Common Lustre File System Problems](05.01-Lustre%20File%20System%20Troubleshooting.md#common-lustre-file-system-problems)<br>
​	&emsp;error messages, [Viewing Error Messages](05.01-Lustre%20File%20System%20Troubleshooting.md#viewing-error-messages)<br>
​	&emsp;error numbers, [Error Numbers](05.01-Lustre%20File%20System%20Troubleshooting.md#error-numbers)<br>
​	&emsp;OST out of memory, [Log Message 'Out of Memory' on OST](05.01-Lustre%20File%20System%20Troubleshooting.md#log-message-out-of-memory-on-ost)<br>
​	&emsp;reporting bugs, [Reporting a Lustre File System Bug](05.01-Lustre%20File%20System%20Troubleshooting.md#reporting-a-lustre-file-system-bug)<br>
​	&emsp;slowdown during startup, [Slowdown Occurs During Lustre File System Startup](05.01-Lustre%20File%20System%20Troubleshooting.md#slowdown-occurs-during-lustre-file-system-startup)<br>
​	&emsp;timeouts on setup, [Handling Timeouts on Initial Lustre File System Setup](05.01-Lustre%20File%20System%20Troubleshooting.md#handling-timeouts-on-initial-lustre-file-system-setup)<br><br>

tunefs.lustre, [tunefs.lustre](06.07-System%20Configuration%20Utilities.md#tunefslustre)<br>
tuning, [Optimizing the Number of Service Threads](04.03-Tuning%20a%20Lustre%20File%20System.md#optimizing-the-number-of-service-threads) (see [benchmarking](#b))<br><br>

​	&emsp;for small files, [Improving Lustre I/O Performance for Small Files](04.03-Tuning%20a%20Lustre%20File%20System.md#improving-lustre-io-performance-for-small-files)<br>
​	&emsp;Large Bulk IO, [Large Bulk IO (16MB RPC)](04.03-Tuning%20a%20Lustre%20File%20System.md#large-bulk-io-16mb-rpc)<br>
​	&emsp;libcfs, [libcfs Tuning](04.03-Tuning%20a%20Lustre%20File%20System.md#libcfs-tuning)<br>
​	&emsp;LND tuning, [LND Tuning](04.03-Tuning%20a%20Lustre%20File%20System.md#lnd-tuning)<br>
​	&emsp;LNet, [Tuning LNet Parameters](04.03-Tuning%20a%20Lustre%20File%20System.md#tuning-lnet-parameters)<br>
​	&emsp;lockless I/O, [Lockless I/O Tunables](04.03-Tuning%20a%20Lustre%20File%20System.md#lockless-io-tunables)<br>
​	&emsp;MDS binding, [Binding MDS Service Thread to CPU Partitions](04.03-Tuning%20a%20Lustre%20File%20System.md#binding-mds-service-thread-to-cpu-partitions)<br>
​	&emsp;MDS threads, [Specifying the MDS Service Thread Count](04.03-Tuning%20a%20Lustre%20File%20System.md#specifying-the-mds-service-thread-count)<br>
​	&emsp;Network interface binding, [Binding Network Interface Against CPU Partitions](04.03-Tuning%20a%20Lustre%20File%20System.md#binding-network-interface-against-cpu-partitions)<br>
​	&emsp;Network interface credits, [Network Interface Credits](04.03-Tuning%20a%20Lustre%20File%20System.md#network-interface-credits)<br>
​	&emsp;Network Request Scheduler (NRS) Tuning, [Network Request Scheduler (NRS) Tuning](04.03-Tuning%20a%20Lustre%20File%20System.md#network-request-scheduler-nrs-tuning)<br><br>

​		&emsp;&emsp;client round-robin over NIDs (CRR-N) policy, [Client Round-Robin over NIDs (CRR-N) policy](04.03-Tuning%20a%20Lustre%20File%20System.md#client-round-robin-over-nids-crr-n-policy)<br>
​		&emsp;&emsp;Delay policy, [Delay policy](04.03-Tuning%20a%20Lustre%20File%20System.md#delay-policy)<br>
​		&emsp;&emsp;first in, first out (FIFO) policy, [First In, First Out (FIFO) policy](04.03-Tuning%20a%20Lustre%20File%20System.md#first-in-first-out-fifo-policy)<br>
​		&emsp;&emsp;object-based round-robin (ORR) policy, [Object-based Round-Robin (ORR) policy](04.03-Tuning%20a%20Lustre%20File%20System.md#object-based-round-robin-orr-policy)<br>
​		&emsp;&emsp;Target-based round-robin (TRR) policy, [Target-based Round-Robin (TRR) policy](04.03-Tuning%20a%20Lustre%20File%20System.md#target-based-round-robin-trr-policy)<br>
​		&emsp;&emsp;Token Bucket Filter (TBF) policy, [Token Bucket Filter (TBF) policy](04.03-Tuning%20a%20Lustre%20File%20System.md#token-bucket-filter-tbf-policy)<br><br>

​	&emsp;OSS threads, [Specifying the OSS Service Thread Count](04.03-Tuning%20a%20Lustre%20File%20System.md#specifying-the-oss-service-thread-count)<br>
​	&emsp;portal round-robin, [Portal Round-Robin](04.03-Tuning%20a%20Lustre%20File%20System.md#portal-round-robin)<br>
​	&emsp;router buffers, [Router Buffers](04.03-Tuning%20a%20Lustre%20File%20System.md#router-buffers)<br>
​	&emsp;service threads, [Optimizing the Number of Service Threads](04.03-Tuning%20a%20Lustre%20File%20System.md#optimizing-the-number-of-service-threads)<br>
​	&emsp;with lfs ladvise, [Server-Side Advice and Hinting](04.03-Tuning%20a%20Lustre%20File%20System.md#server-side-advice-and-hinting)<br>
​	&emsp;write performance, [Understanding Why Write Performance is Better Than Read Performance](04.03-Tuning%20a%20Lustre%20File%20System.md#understanding-why-write-performance-is-better-than-read-performance)<br>

## U
upgrading, [Release Interoperability and Upgrade Requirements](03.06-Upgrading%20a%20Lustre%20File%20System.md#release-interoperability-and-upgrade-requirements)<br><br>

​	&emsp;2.X.y to 2.X.y (minor release), [Upgrading to Lustre Software Release 2.x.y (Minor Release)](03.06-Upgrading%20a%20Lustre%20File%20System.md#upgrading-to-lustre-software-release-2xy-minor-release)<br>
​	&emsp;major release (2.x to 2.x), [Upgrading to Lustre Software Release 2.x (Major Release)](03.06-Upgrading%20a%20Lustre%20File%20System.md#upgrading-to-lustre-software-release-2x-major-release)<br>

utilities<br><br>

​	&emsp;application profiling, [Application Profiling Utilities](06.07-System%20Configuration%20Utilities.md#application-profiling-utilities)<br>
​	&emsp;debugging, [Testing / Debugging Utilities](06.07-System%20Configuration%20Utilities.md#testing--debugging-utilities)
​	&emsp;system config, [Additional System Configuration Utilities](06.07-System%20Configuration%20Utilities.md#additional-system-configuration-utilities)<br>

## V
version<br><br>

​	&emsp;which version of Lustre am I running?, [Revisions](01-Preface.md#revisions)<br>

Version-based recovery (VBR), [Version-based Recovery](06.01-Lustre%20File%20System%20Recovery.md#version-based-recovery)<br><br>

​	&emsp;messages, [VBR Messages](06.01-Lustre%20File%20System%20Recovery.md#vbr-messages)<br>
​	&emsp;tips, [Tips for Using VBR](06.01-Lustre%20File%20System%20Recovery.md#tips-for-using-vbr)<br>

## W
weighted algorithm, [Managing Free Space](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#managing-free-space)<br>
wide striping, [File and File System Limits](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#file-and-file-system-limits), [Upgrading to Lustre Software Release 2.x (Major Release)](03.06-Upgrading%20a%20Lustre%20File%20System.md#upgrading-to-lustre-software-release-2x-major-release), [Lustre Striping Internals](03.08-Managing%20File%20Layout%20(Striping)%20and%20Free%20Space.md#lustre-striping-internals)<br>

​	&emsp;large_xattr<br><br>
​		&emsp;&emsp;ea_inode, [File and File System Limits](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#file-and-file-system-limits), [Upgrading to Lustre Software Release 2.x (Major Release)](03.06-Upgrading%20a%20Lustre%20File%20System.md#upgrading-to-lustre-software-release-2x-major-release)<br>

## X
xattr<br><br>
​	&emsp;See wide striping, [File and File System Limits](02.02-Determining%20Hardware%20Configuration%20Requirements%20and%20Formatting%20Options.md#file-and-file-system-limits)<br>

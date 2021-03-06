## Programming Interfaces

- [Programming Interfaces](#programming-interfaces)
- [User/Group Upcall](#usergroup-upcall)
  * [Synopsis](#synopsis)
  * [Description](#description)
    + [Primary and Secondary Groups](#primary-and-secondary-groups)
  * [Data Structures](#data-structures)


This chapter describes public programming interfaces to that can be used to control various aspects of a Lustre file system from userspace. This chapter includes the following sections:

- [the section called “User/Group Upcall”](#usergroup-upcall)
- [the section called “Data Structures”](#data-structures)

**Note**

Lustre programming interface man pages are found in the `lustre/doc` folder.

## User/Group Upcall

This section describes the supplementary user/group upcall, which allows the MDS to retrieve and verify the supplementary groups to which a particular user is assigned. This avoids the need to pass all the supplementary groups from the client to the MDS with every RPC.

**Note**

For information about universal UID/GID requirements in a Lustre file system environment, see [*the section called “Environmental Requirements”*](02.05-Installing%20the%20Lustre%20Software.md#environmental-requirements).

### Synopsis

The MDS uses the utility as specified by `lctl get_param mdt.${FSNAME}-MDT{xxxx}.identity_upcall` to look up the supplied UID in order to retrieve the user's supplementary group membership. The result is temporarily cached in the kernel (for five minutes, by default) to avoid the overhead of calling into userspace repeatedly.

### Description

The `identity_upcall` parameter contains the path to an executable that is run to map a numeric UID to a group membership list. This upcall executable opens the `mdt.${FSNAME}-MDT{xxxx}.identity_info` parameter file and writes the related `identity_downcall_data` data structure (see *[the section called “Data Structures”](#data-structures)*). The upcall is configured with `lctl set_param mdt.${FSNAME}-MDT{xxxx}.identity_upcall`.

The default identity upcall program installed is `lustre/utils/l_getidentity.c` in the Lustre source distribution.

#### Primary and Secondary Groups

The mechanism for the primary/secondary group is as follows:

- The MDS issues an upcall (set per MDS) to map the numeric UID to the supplementary group(s).
- If there is no upcall or if there is an upcall and it fails, one supplementary group at most will be added as supplied by the client.
- The default upcall `/usr/sbin/l_getidentity` can interact with the user/group database on the MDS to map the UID to the GID and supplementary GID. The user/group database depends on how authentication is configured on the MDS, such as local `/etc/passwd`, Network Information Service (NIS), Lightweight Directory Access Protocol (LDAP), or SMB Domain services, as configured. If the upcall interface is set to NONE, then upcall is disabled, and the MDS uses only the UID, GID, and one supplementary GID supplied by the client.
- The MDS will wait a limited time for the group upcall program to complete, to avoid MDS threads and clients hanging due to errors accessing a remote service node. The upcall must finish within 30s before the MDS will continue without the supplementary data. The upcall timeout in seconds can be set on the MDS using: `lctl set_param mdt.*.identity_acquire_expire=*seconds*`
- The default group upcall is set permanently by `mkfs.lustre`. To set a custom upcall for a particular filesystem, use `tunefs.lustre --param` or `lctl set_param -P mdt.FSNAME-MDTxxxx.identity_upcall=path`
- The group downcall data is cached by the kernel to avoid repeated upcalls for the same user slowing down the MDS. This cache is expired from the kernel after 1200s (20 minutes) by default. The cache age in seconds can be set on the MDS using: `lctl set_param mdt.*.identity_expire=seconds`
- To force eviction of cached identity data (e.g. after adding or removing a user from a supplementary group), the cache entry for a specific numeric UID can be flushed on the MDS using:`lctl set_param mdt.*.identity_flush=UID` To flush the cached records for all users from cache, use `-1` for the UID: `lctl set_param mdt.*.identity_flush=-1`

### Data Structures

```
struct perm_downcall_data {
     __u64 pdd_nid;
     __u32 pdd_perm;
     __u32 pdd_padding;
};

struct identity_downcall_data{
     __u32        idd_magic;
     :         
     :
```

 

# Managing Security in a Lustre File System

- [Managing Security in a Lustre File System](#managing-security-in-a-lustre-file-system)
  * [Using ACLs](#using-acls)
    + [How ACLs Work](#how-acls-work)
    + [Using ACLs with the Lustre Software](#using-acls-with-the-lustre-software)
    + [Examples](#examples)
  * [Using Root Squash](#using-root-squash)
    + [Configuring Root Squash](#configuring-root-squash)
    + [Enabling and Tuning Root Squash](#enabling-and-tuning-root-squash)
    + [Tips on Using Root Squash](#tips-on-using-root-squash)
  * [Isolating Clients to a Sub-directory Tree](#isolating-clients-to-a-sub-directory-tree)
    + [Identifying Clients](#identifying-clients)
    + [Configuring Isolation](#configuring-isolation)
    + [Making Isolation Permanent](#making-isolation-permanent)
  * [Checking SELinux Policy Enforced by Lustre Clients](#checking-selinux-policy-enforced-by-lustre-clients)
    + [Determining SELinux Policy Info](#determining-selinux-policy-info)
    + [Enforcing SELinux Policy Check](#enforcing-selinux-policy-check)
    + [Making SELinux Policy Check Permanent](#making-selinux-policy-check-permanent)
    + [Sending SELinux Status Info from Clients](#sending-selinux-status-info-from-clients)

This chapter describes security features of the Lustre file system and includes the following sections:

- [the section called “Using ACLs”](#using-acls)
- [the section called “Using Root Squash”](#using-root-squash)
- [the section called “ Isolating Clients to a Sub-directory Tree”](#isolating-clients-to-a-sub-directory-tree)
- [the section called “ Checking SELinux Policy Enforced by Lustre Clients”](#checking-selinux-policy-enforced-by-lustre-clients)
- [the section called “ Encrypting files and directories” ](#Encrypting files and directories)

## Using ACLs

An access control list (ACL), is a set of data that informs an operating system about permissions or access rights that each user or group has to specific system objects, such as directories or files. Each object has a unique security attribute that identifies users who have access to it. The ACL lists each object and user access privileges such as read, write or execute.

### How ACLs Work

Implementing ACLs varies between operating systems. Systems that support the Portable Operating System Interface (POSIX) family of standards share a simple yet powerful file system permission model, which should be well-known to the Linux/UNIX administrator. ACLs add finer-grained permissions to this model, allowing for more complicated permission schemes. For a detailed explanation of ACLs on a Linux operating system, refer to the SUSE Labs article [Posix Access Control Lists on Linux](https://wiki.lustre.org/images/5/57/PosixAccessControlInLinux.pdf).

We have implemented ACLs according to this model. The Lustre software works with the standard Linux ACL tools, setfacl, getfacl, and the historical chacl, normally installed with the ACL package.

**Note**

ACL support is a system-range feature, meaning that all clients have ACL enabled or not. You cannot specify which clients should enable ACL.

### Using ACLs with the Lustre Software

POSIX Access Control Lists (ACLs) can be used with the Lustre software. An ACL consists of file entries representing permissions based on standard POSIX file system object permissions that define three classes of user (owner, group and other). Each class is associated with a set of permissions [read (r), write (w) and execute (x)].

- Owner class permissions define access privileges of the file owner.
- Group class permissions define access privileges of the owning group.
- Other class permissions define access privileges of all users not in the owner or group class.

The `ls -l` command displays the owner, group, and other class permissions in the first column of its output (for example, `-rw-r- --` for a regular file with read and write access for the owner class, read access for the group class, and no access for others).

Minimal ACLs have three entries. Extended ACLs have more than the three entries. Extended ACLs also contain a mask entry and may contain any number of named user and named group entries.

The MDS needs to be configured to enable ACLs. Use `--mountfsoptions` to enable ACLs when creating your configuration:

```
$ mkfs.lustre --fsname spfs --mountfsoptions=acl --mdt -mgs /dev/sda
```

Alternately, you can enable ACLs at run time by using the `--acl` option with `mkfs.lustre`:

```
$ mount -t lustre -o acl /dev/sda /mnt/mdt
```

To check ACLs on the MDS:

```
$ lctl get_param -n mdc.home-MDT0000-mdc-*.connect_flags | grep acl acl
```

To mount the client with no ACLs:

```
$ mount -t lustre -o noacl ibmds2@o2ib:/home /home
```

ACLs are enabled in a Lustre file system on a system-wide basis; either all clients enable ACLs or none do. Activating ACLs is controlled by MDS mount options `acl` / `noacl` (enable/disable ACLs). Client-side mount options acl/noacl are ignored. You do not need to change the client configuration, and the 'acl' string will not appear in the client /etc/mtab. The client acl mount option is no longer needed. If a client is mounted with that option, then this message appears in the MDS syslog:

```
...MDS requires ACL support but client does not
```

The message is harmless but indicates a configuration issue, which should be corrected.

If ACLs are not enabled on the MDS, then any attempts to reference an ACL on a client return an Operation not supported error.

### Examples

These examples are taken directly from the POSIX paper referenced above. ACLs on a Lustre file system work exactly like ACLs on any Linux file system. They are manipulated with the standard tools in the standard manner. Below, we create a directory and allow a specific user access.

```
[root@client lustre]# umask 027
[root@client lustre]# mkdir rain
[root@client lustre]# ls -ld rain
drwxr-x---  2 root root 4096 Feb 20 06:50 rain
[root@client lustre]# getfacl rain
# file: rain
# owner: root
# group: root
user::rwx
group::r-x
other::---
 
[root@client lustre]# setfacl -m user:chirag:rwx rain
[root@client lustre]# ls -ld rain
drwxrwx---+ 2 root root 4096 Feb 20 06:50 rain
[root@client lustre]# getfacl --omit-header rain
user::rwx
user:chirag:rwx
group::r-x
mask::rwx
other::---
```

## Using Root Squash

Root squash is a security feature which restricts super-user access rights to a Lustre file system. Without the root squash feature enabled, Lustre file system users on untrusted clients could access or modify files owned by root on the file system, including deleting them. Using the root squash feature restricts file access/modifications as the root user to only the specified clients. Note, however, that this does *not* prevent users on insecure clients from accessing files owned by *other* users.

The root squash feature works by re-mapping the user ID (UID) and group ID (GID) of the root user to a UID and GID specified by the system administrator, via the Lustre configuration management server (MGS). The root squash feature also enables the Lustre file system administrator to specify a set of client for which UID/GID re-mapping does not apply.

**Note**

Nodemaps (*Mapping UIDs and GIDs with Nodemap*) are an alternative to root squash, since it also allows root squash on a per-client basis. With UID maps, the clients can even have a local root UID without actually having root access to the filesystem itself.

### Configuring Root Squash

Root squash functionality is managed by two configuration parameters, `root_squash` and `nosquash_nids`.

- The `root_squash` parameter specifies the UID and GID with which the root user accesses the Lustre file system.
- The `nosquash_nids` parameter specifies the set of clients to which root squash does not apply. LNet NID range syntax is used for this parameter (see the NID range syntax rules described in *the section called “Using Root Squash”*). For example:

```
nosquash_nids=172.16.245.[0-255/2]@tcp
```

In this example, root squash does not apply to TCP clients on subnet 172.16.245.0 that have an even number as the last component of their IP address.

### Enabling and Tuning Root Squash

The default value for `nosquash_nids` is NULL, which means that root squashing applies to all clients. Setting the root squash UID and GID to 0 turns root squash off.

Root squash parameters can be set when the MDT is created (`mkfs.lustre --mdt`). For example:

```
mds# mkfs.lustre --reformat --fsname=testfs --mdt --mgs \
       --param "mdt.root_squash=500:501" \
       --param "mdt.nosquash_nids='0@elan1 192.168.1.[10,11]'" /dev/sda1
```

Root squash parameters can also be changed on an unmounted device with `tunefs.lustre`. For example:

```
tunefs.lustre --param "mdt.root_squash=65534:65534"  \
--param "mdt.nosquash_nids=192.168.0.13@tcp0" /dev/sda1
```

Root squash parameters can also be changed with the `lctl conf_param` command. For example:

```
mgs# lctl conf_param testfs.mdt.root_squash="1000:101"
mgs# lctl conf_param testfs.mdt.nosquash_nids="*@tcp"
```

To retrieve the current root squash parameter settings, the following `lctl get_param` commands can be used:

```
mgs# lctl get_param mdt.*.root_squash
mgs# lctl get_param mdt.*.nosquash_nids
```

**Note**

When using the lctl conf_param command, keep in mind:

- `lctl conf_param` must be run on a live MGS
- `lctl conf_param` causes the parameter to change on all MDSs
- `lctl conf_param` is to be used once per a parameter

The root squash settings can also be changed temporarily with `lctl set_param` or persistently with `lctl set_param -P`. For example:

```
mgs# lctl set_param mdt.testfs-MDT0000.root_squash="1:0"
mgs# lctl set_param -P mdt.testfs-MDT0000.root_squash="1:0"
```

The `nosquash_nids` list can be cleared with:

```
mgs# lctl conf_param testfs.mdt.nosquash_nids="NONE"
```

\- OR -

```
mgs# lctl conf_param testfs.mdt.nosquash_nids="clear"
```

If the `nosquash_nids` value consists of several NID ranges (e.g. `0@elan`, `1@elan1`), the list of NID ranges must be quoted with single (') or double ('') quotation marks. List elements must be separated with a space. For example:

```
mds# mkfs.lustre ... --param "mdt.nosquash_nids='0@elan1 1@elan2'" /dev/sda1
lctl conf_param testfs.mdt.nosquash_nids="24@elan 15@elan1"
```

These are examples of incorrect syntax:

```
mds# mkfs.lustre ... --param "mdt.nosquash_nids=0@elan1 1@elan2" /dev/sda1
lctl conf_param testfs.mdt.nosquash_nids=24@elan 15@elan1
```

To check root squash parameters, use the lctl get_param command:

```
mds# lctl get_param mdt.testfs-MDT0000.root_squash
lctl get_param mdt.*.nosquash_nids
```

**Note**

An empty nosquash_nids list is reported as NONE.

### Tips on Using Root Squash

Lustre configuration management limits root squash in several ways.

- The `lctl conf_param` value overwrites the parameter's previous value. If the new value uses an incorrect syntax, then the system continues with the old parameters and the previously-correct value is lost on remount. That is, be careful doing root squash tuning.
- `mkfs.lustre` and `tunefs.lustre` do not perform parameter syntax checking. If the root squash parameters are incorrect, they are ignored on mount and the default values are used instead.
- Root squash parameters are parsed with rigorous syntax checking. The root_squash parameter should be specified as `<decnum>:<decnum>`. The `nosquash_nids` parameter should follow LNet NID range list syntax.

LNet NID range syntax:

```
<nidlist>     :== <nidrange> [ ' ' <nidrange> ]
<nidrange>   :== <addrrange> '@' <net>
<addrrange>  :== '*' |
           <ipaddr_range> |
           <numaddr_range>
<ipaddr_range>       :==
<numaddr_range>.<numaddr_range>.<numaddr_range>.<numaddr_range>
<numaddr_range>      :== <number> |
                   <expr_list>
<expr_list>  :== '[' <range_expr> [ ',' <range_expr>] ']'
<range_expr> :== <number> |
           <number> '-' <number> |
           <number> '-' <number> '/' <number>
<net>        :== <netname> | <netname><number>
<netname>    :== "lo" | "tcp" | "o2ib"
           | "ra" | "elan"
<number>     :== <nonnegative decimal> | <hexadecimal>
```

**Note**

For networks using numeric addresses (e.g. elan), the address range must be specified in the`<numaddr_range>` syntax. For networks using IP addresses, the address range must be in the`<ipaddr_range>`. For example, if elan is using numeric addresses, `1.2.3.4@elan` is incorrect.

## Isolating Clients to a Sub-directory Tree

Isolation is the Lustre implementation of the generic concept of multi-tenancy, which aims at providing separated namespaces from a single filesystem. Lustre Isolation enables different populations of users on the same file system beyond normal Unix permissions/ACLs, even when users on the clients may have root access. Those tenants share the same file system, but they are isolated from each other: they cannot access or even see each other’s files, and are not aware that they are sharing common file system resources.

Lustre Isolation leverages the Fileset feature (*the section called “Fileset Feature”*) to mount only a subdirectory of the filesystem rather than the root directory. In order to achieve isolation, the subdirectory mount, which presents to tenants only their own fileset, has to be imposed to the clients. To that extent, we make use of the nodemap feature (*Mapping UIDs and GIDs with Nodemap*). We group all clients used by a tenant under a common nodemap entry, and we assign to this nodemap entry the fileset to which the tenant is restricted.

### Identifying Clients

Enforcing multi-tenancy on Lustre relies on the ability to properly identify the client nodes used by a tenant, and trust those identities. This can be achieved by having physical hardware and/or network security, so that client nodes have well-known NIDs. It is also possible to make use of strong authentication with Kerberos or Shared-Secret Key (see *Configuring Shared-Secret Key (SSK) Security*). Kerberos prevents NID spoofing, as every client needs its own credentials, based on its NID, in order to connect to the servers. Shared-Secret Key also prevents tenant impersonation, because keys can be linked to a specific nodemap. See *the section called “Role of Nodemap in SSK”* for detailed explanations.

### Configuring Isolation

Isolation on Lustre can be achieved by setting the `fileset` parameter on a nodemap entry. All clients belonging to this nodemap entry will automatically mount this fileset instead of the root directory. For example:

```
mgs# lctl nodemap_set_fileset --name tenant1 --fileset '/dir1'
```

So all clients matching the `tenant1` nodemap will be automatically presented the fileset `/dir1` when mounting. This means these clients are doing an implicit subdirectory mount on the subdirectory `/dir1`.

**Note**

If subdirectory defined as fileset does not exist on the file system, it will prevent any client belonging to the nodemap from mounting Lustre.

To delete the fileset parameter, just set it to an empty string:

```
mgs# lctl nodemap_set_fileset --name tenant1 --fileset ''
```
### Making Isolation Permanent

In order to make isolation permanent, the fileset parameter on the nodemap has to be set with `lctl set_param`with the `-P` option.

```
mgs# lctl set_param nodemap.tenant1.fileset=/dir1
mgs# lctl set_param -P nodemap.tenant1.fileset=/dir1
```

This way the fileset parameter will be stored in the Lustre config logs, letting the servers retrieve the information after a restart.

Introduced in Lustre 2.13

## Encrypting files and directoriesChecking SELinux Policy Enforced by Lustre Clients

SELinux provides a mechanism in Linux for supporting Mandatory Access Control (MAC) policies. When a MAC policy is enforced, the operating system’s (OS) kernel defines application rights, firewalling applications from compromising the entire system. Regular users do not have the ability to override the policy.

One purpose of SELinux is to protect the **OS** from privilege escalation. To that extent, SELinux defines confined and unconfined domains for processes and users. Each process, user, file is assigned a security context, and rules define the allowed operations by processes and users on files.

Another purpose of SELinux can be to protect **data** sensitivity, thanks to Multi-Level Security (MLS). MLS works on top of SELinux, by defining the concept of security levels in addition to domains. Each process, user and file is assigned a security level, and the model states that processes and users can read the same or lower security level, but can only write to their own or higher security level.

From a file system perspective, the security context of files must be stored permanently. Lustre makes use of the`security.selinux` extended attributes on files to hold this information. Lustre supports SELinux on the client side. All you have to do to have MAC and MLS on Lustre is to enforce the appropriate SELinux policy (as provided by the Linux distribution) on all Lustre clients. No SELinux is required on Lustre servers.

Because Lustre is a distributed file system, the specificity when using MLS is that Lustre really needs to make sure data is always accessed by nodes with the SELinux MLS policy properly enforced. Otherwise, data is not protected. This means Lustre has to check that SELinux is properly enforced on client side, with the right, unaltered policy. And if SELinux is not enforced as expected on a client, the server denies its access to Lustre.

### Determining SELinux Policy Info

A string that represents the SELinux Status info will be used by servers as a reference, to check if clients are enforcing SELinux properly. This reference string can be obtained on a client node known to enforce the right SELinux policy, by calling the `l_getsepol` command line utility:

```
client# l_getsepol
SELinux status info: 1:mls:31:40afb76d077c441b69af58cccaaa2ca63641ed6e21b0a887dc21a684f508b78f
```

The string describing the SELinux policy has the following syntax:

`mode:name:version:hash`

where:

- `mode` is a digit telling if SELinux is in Permissive mode (0) or Enforcing mode (1)
- `name` is the name of the SELinux policy
- `version` is the version of the SELinux policy
- `hash` is the computed hash of the binary representation of the policy, as exported in /etc/selinux/`name`/policy/policy. `version`

### Enforcing SELinux Policy Check

SELinux policy check can be enforced by setting the `sepol` parameter on a nodemap entry. All clients belonging to this nodemap entry must enforce the SELinux policy described by this parameter, otherwise they are denied access to the Lustre file system. For example:

```
mgs# lctl nodemap_set_sepol --name restricted
     --sepol '1:mls:31:40afb76d077c441b69af58cccaaa2ca63641ed6e21b0a887dc21a684f508b78f'
```

So all clients matching the `restricted` nodemap must enforce the SELinux policy which description matches`1:mls:31:40afb76d077c441b69af58cccaaa2ca63641ed6e21b0a887dc21a684f508b78f`. If not, they will get Permission Denied when trying to mount or access files on the Lustre file system.

To delete the `sepol` parameter, just set it to an empty string:

```
mgs# lctl nodemap_set_sepol --name restricted --sepol ''
```

See *Mapping UIDs and GIDs with Nodemap* for more details about the Nodemap feature.

### Making SELinux Policy Check Permanent

In order to make SELinux Policy check permanent, the sepol parameter on the nodemap has to be set with `lctl set_param` with the `-P` option.

```
mgs# lctl set_param nodemap.restricted.sepol=1:mls:31:40afb76d077c441b69af58cccaaa2ca63641ed6e21b0a887dc21a684f508b78f
mgs# lctl set_param -P nodemap.restricted.sepol=1:mls:31:40afb76d077c441b69af58cccaaa2ca63641ed6e21b0a887dc21a684f508b78f
```

This way the sepol parameter will be stored in the Lustre config logs, letting the servers retrieve the information after a restart.

### Sending SELinux Status Info from Clients

In order for Lustre clients to send their SELinux status information, in	case SELinux is enabled locally, the`send_sepol` ptlrpc kernel module's parameter has to be set to a non-zero value. `send_sepol` accepts various values:

- 0: do not send SELinux policy info;
- -1: fetch SELinux policy info for every request;
- N > 0: only fetch SELinux policy info every N seconds. Use `N = 2^31-1` to have SELinux policy info fetched only at mount time.

Clients that are part of a nodemap on which `sepol` is defined must send SELinux status info. And the SELinux policy they enforce must match the representation stored into the nodemap. Otherwise they will be denied access to the Lustre file system.

 ## Encrypting files and directories

The purpose that client-side encryption wants to serve is to be able to provide a special directory for each user, to safely store sensitive files. The goals are to protect data in transit between clients and servers, and protect data at rest.

This feature is implemented directly at the Lustre client level. Lustre client-side encryption relies on kernel `fscrypt. fscrypt` is a library which filesystems can hook into to support transparent encryption of files and directories. As a consequence, the key points described below are extracted from `fscrypt` documentation.

For full details, please refer to documentation available with the Lustre sources, under the `Documentation/client_side_encryption` directory.

**Note**

The client-side encryption feature is available natively on Lustre clients running a Linux distribution with at least kernel 5.4. It is also available thanks to an additional kernel library provided by Lustre, on clients that run a Linux distribution with basic support for encryption, including:

* CentOS/RHEL 8.1 and later;
* Ubuntu 18.04 and later;
* SLES 15 SP2 and later.

### Client-side encryption access semantics

Only Lustre clients need access to encryption master keys. Keys are added to the filesystem-level encryption keyring on the Lustre client.

* **With the key**

  With the encryption key, encrypted regular files, directories, and symlinks behave very similarly to their unencrypted counterparts --- after all, the encryption is intended to be transparent. However, astute users may notice some differences in behavior:

  * Unencrypted files, or files encrypted with a different encryption policy (i.e. different key, modes, or flags), cannot be renamed or linked into an encrypted directory. However, encrypted files can be renamed within an encrypted directory, or into an unencrypted directory.

    **Note**

    "moving" an unencrypted file into an encrypted directory, e.g. with the mv program, is implemented in userspace by a copy followed by a delete. Be aware the original unencrypted data may remain recoverable from free space on the disk; it is best to keep all files encrypted from the very beginning.

  * On Lustre, Direct I/O is supported for encrypted files.

  * The fallocate() operations FALLOC_FL_COLLAPSE_RANGE,
    FALLOC_FL_INSERT_RANGE, and FALLOC_FL_ZERO_RANGE are not supported on encrypted
    files and will fail with EOPNOTSUPP.

  * DAX (Direct Access) is not supported on encrypted files.

  * mmap is supported. This is possible because the pagecache for an encrypted file contains the plaintext, not the ciphertext.

* **Without the key**

  Some filesystem operations may be performed on encrypted regular files, directories, and symlinks even before their encryption key has been added, or after their encryption key has been removed: 
  
  • File metadata may be read, e.g. using stat(). 
  
  • Directories may be listed, and the whole namespace tree may be walked through. 
  
  • Files may be deleted. That is, nondirectory files may be deleted with unlink() as usual, and empty directories may be deleted with rmdir() as usual. Therefore, rm and rm -r will work as expected. 
  
  • Symlink targets may be read and followed, but they will be presented in encrypted form, similar to filenames in directories. Hence, they are unlikely to point to anywhere useful. 
  
  Without the key, regular files cannot be opened or truncated. Attempts to do so will fail with ENOKEY. This implies that any regular file operations that require a file descriptor, such as read(), write(), mmap(), fallocate(), and ioctl(), are also forbidden. 
  
  Also without the key, files of any type (including directories) cannot be created or linked into an encrypted directory, nor can a name in an encrypted directory be the source or target of a rename, nor can an O_TMPFILE temporary file be created in an encrypted directory. All such operations will fail with ENOKEY. 
  
  It is not currently possible to backup and restore encrypted files without the encryption key. This would require special APIs which have not yet been implemented
  
* **Encryption policy enforcement**

  After an encryption policy has been set on a directory, all regular files, directories, and symbolic links created in that directory (recursively) will inherit that encryption policy. Special files --- that is, named pipes, device nodes, and UNIX domain sockets --- will not be encrypted. 
  
  Except for those special files, it is forbidden to have unencrypted files, or files encrypted with a different encryption policy, in an encrypted directory tree.

### Client-side encryption key hierarchy

Each encrypted directory tree is protected by a master key. 

To "unlock" an encrypted directory tree, userspace must provide the appropriate master key. There can be any number of master keys, each of which protects any number of directory trees on any number of filesystems.

### Client-side encryption modes and usage

`fscrypt` allows one encryption mode to be specified for file contents and one encryption mode to be specified for filenames. Different directory trees are permitted to use different encryption modes. Currently, the following pairs of encryption modes are supported:

• AES-256-XTS for contents and AES-256-CTS-CBC for filenames 

• AES-128-CBC for contents and AES-128-CTS-CBC for filenames 

If unsure, you should use the (AES-256-XTS, AES-256-CTS-CBC) pair.

**Warning** 

In Lustre 2.14, client-side encryption only supports content encryption, and not filename encryption. As a consequence, only content encryption mode will be taken into account, and filename encryption mode will be ignored to leave filenames in clear text.

### Client-side encryption threat model

* **Offline attacks**

  For the Lustre case, block devices are Lustre targets attached to the Lustre servers. Manipulating the filesystem offline means accessing the filesystem on these targets while Lustre is offline. 

  Provided that a strong encryption key is chosen, fscrypt protects the confidentiality of file contents in the event of a single point-in-time permanent offline compromise of the block device content. Lustre client-side encryption does not protect the confidentiality of metadata, e.g. file names, file sizes, file permissions, file timestamps, and extended attributes. Also, the existence and location of holes (unallocated blocks which logically contain all zeroes) in files is not protected.

* **Online attacks**

  * On Lustre client

    After an encryption key has been added, fscrypt does not hide the plaintext file contents or filenames from other users on the same node. Instead, existing access control mechanisms such as file mode bits, POSIX ACLs, LSMs, or namespaces should be used for this purpose. 

    For the Lustre case, it means plaintext file contents or filenames are not hidden from other users on the same Lustre client. 

    An attacker who compromises the system enough to read from arbitrary memory, e.g. by exploiting a kernel security vulnerability, can compromise all encryption keys that are currently in use. However, fscrypt allows encryption keys to be removed from the kernel, which may protect them from later compromise. Key removal can be carried out by non-root users. In more detail, the key removal will wipe the master encryption key from kernel memory. Moreover, it will try to evict all cached inodes which had been "unlocked" using the key, thereby wiping their per-file keys and making them once again appear "locked", i.e. in ciphertext or encrypted form.

  * On Lustre server

    An attacker on a Lustre server who compromises the system enough to read arbitrary memory, e.g. by exploiting a kernel security vulnerability, cannot compromise Lustre files content. Indeed, encryption keys are not forwarded to the Lustre servers, and servers do not carry out decryption or encryption. Moreover, bulk RPCs received by servers contain encrypted data, which is written as-is to the underlying filesystem.

###  Manage encryption on directories

By default, Lustre client-side encryption is enabled, letting users define encryption policies on a perdirectory basis.

**Note** 

Administrators can decide to prevent a Lustre client mount-point from using encryption by specifying the `noencrypt` client mount option. This can be also enforced from server side thanks to the `forbid_encryption` property on nodemaps. See Section “Altering Properties” for how to manage nodemaps.

`fscrypt` userspace tool can be used to manage encryption policies. See https://github.com/google/fscrypt for comprehensive explanations. Below are examples on how to use this tool with Lustre. If not told otherwise, commands must be run on Lustre client side.

* Two preliminary steps are required before actually deciding which directories to encrypt, and this is the only functionality which requires root privileges. Administrator has to run:

  ```
  # fscrypt setup
  Customizing passphrase hashing difficulty for this system...
  Created global config file at "/etc/fscrypt.conf".
  Metadata directories created at "/.fscrypt".
  ```

  This first command has to be run on all clients that want to use encryption, as it sets up global fscrypt parameters outside of Lustre.

  ```
  # fscrypt setup /mnt/lustre
  Metadata directories created at "/mnt/lustre/.fscrypt"
  ```

  This second command has to be run on just one Lustre client.

  **Note**

  The file `/etc/fscrypt.conf` can be edited. It is strongly recommended to set policy_version to 2, so that fscrypt wipes files from memory when the encryption key is removed.

* Now a regular user is able to select a directory to encrypt:

  ```
  $ fscrypt encrypt /mnt/lustre/vault
  The following protector sources are available:
  1 - Your login passphrase (pam_passphrase)
  2 - A custom passphrase (custom_passphrase)
  3 - A raw 256-bit key (raw_key)
  Enter the source number for the new protector [2 - custom_passphrase]: 2
  Enter a name for the new protector: shield
  Enter custom passphrase for protector "shield":
  Confirm passphrase:
  "/mnt/lustre/vault" is now encrypted, unlocked, and ready for use.
  ```

  Starting from here, all files and directories created under /mnt/lustre/vault will be encrypted, according to the policy defined at the previsous step.

  **Note** 

  The encryption policy is inherited by all subdirectories. It is not possible to change the policy for a subdirectory.

* Another user can decide to encrypt a different directory with its own protector:

  ```
  $ fscrypt encrypt /mnt/lustre/private
  Should we create a new protector? [y/N] Y
  The following protector sources are available:
  1 - Your login passphrase (pam_passphrase)
  2 - A custom passphrase (custom_passphrase)
  3 - A raw 256-bit key (raw_key)
  Enter the source number for the new protector [2 - custom_passphrase]: 2
  Enter a name for the new protector: armor
  Enter custom passphrase for protector "armor":
  Confirm passphrase:
  "/mnt/lustre/private" is now encrypted, unlocked, and ready for use.
  ```

* Users can decide to lock an encrypted directory at any time:

  ```
  $ fscrypt lock /mnt/lustre/vault
  "/mnt/lustre/vault" is now locked.
  ```

* Users regain access to the encrypted directory with the command:

  ```
  $ fscrypt unlock /mnt/lustre/vault
  Enter custom passphrase for protector "shield":
  "/mnt/lustre/vault" is now unlocked and ready for use.
  ```

* Actually, fscrypt does not give direct access to master keys, but to protectors that are used to encrypt them. This mechanism gives the ability to change a passphrase:

  ```
  $ fscrypt status /mnt/lustre
  lustre filesystem "/mnt/lustre" has 2 protectors and 2 policies
  PROTECTOR LINKED DESCRIPTION
  deacab807bf0e788 No custom protector "shield"
  e691ae7a1990fc2a No custom protector "armor"
  POLICY UNLOCKED PROTECTORS
  52b2b5aff0e59d8e0d58f962e715862e No deacab807bf0e788
  374e8944e4294b527e50363d86fc9411 No e691ae7a1990fc2a
  $ fscrypt metadata change-passphrase --protector=/mnt/lustre:deacab807bf0e788
  Enter old custom passphrase for protector "shield":
  Enter new custom passphrase for protector "shield":
  Confirm passphrase:
  Passphrase for protector deacab807bf0e788 successfully changed.
  ```

  It makes also possible to have multiple protectors for the same policy. This is really useful when several users share an encrypted directory, because it avoids the need to share any secret between them.

  ```
  $ fscrypt status /mnt/lustre/vault
  "/mnt/lustre/vault" is encrypted with fscrypt.
  Policy: 52b2b5aff0e59d8e0d58f962e715862e
  Options: padding:32 contents:AES_256_XTS filenames:AES_256_CTS policy_version:2
  Unlocked: No
  Protected with 1 protector:
  PROTECTOR LINKED DESCRIPTION
  deacab807bf0e788 No custom protector "shield"
  $ fscrypt metadata create protector /mnt/lustre
  Create new protector on "/mnt/lustre" [Y/n] Y
  The following protector sources are available:
  1 - Your login passphrase (pam_passphrase)
  2 - A custom passphrase (custom_passphrase)
  3 - A raw 256-bit key (raw_key)
  Enter the source number for the new protector [2 - custom_passphrase]: 2
  Enter a name for the new protector: bunker
  Enter custom passphrase for protector "bunker":
  Confirm passphrase:
  Protector f3cc1b5cf9b8f41c created on filesystem "/mnt/lustre".
  $ fscrypt metadata add-protector-to-policy
   --protector=/mnt/lustre:f3cc1b5cf9b8f41c
   --policy=/mnt/lustre:52b2b5aff0e59d8e0d58f962e715862e
  WARNING: All files using this policy will be accessible with this protector!!
  Protect policy 52b2b5aff0e59d8e0d58f962e715862e with protector f3cc1b5cf9b8f41c? [Y/n] Y
  Enter custom passphrase for protector "bunker":
  Enter custom passphrase for protector "shield":
  Protector f3cc1b5cf9b8f41c now protecting policy 52b2b5aff0e59d8e0d58f962e715862e.
  $ fscrypt status /mnt/lustre/vault
  "/mnt/lustre/vault" is encrypted with fscrypt.
  Policy: 52b2b5aff0e59d8e0d58f962e715862e
  Options: padding:32 contents:AES_256_XTS filenames:AES_256_CTS policy_version:2
  Unlocked: No
  Protected with 2 protectors:
  PROTECTOR LINKED DESCRIPTION
  deacab807bf0e788 No custom protector "shield"
  f3cc1b5cf9b8f41c No custom protector "bunker"
  ```

## Configuring Kerberos (KRB) Security

This chapter describes how to use Kerberos with Lustre.

###  What Is Kerberos?

Kerberos is a mechanism for authenticating all entities (such as users and servers) on an "unsafe" network. Each of these entities, known as "principals", negotiate a runtime key with the Kerberos server. This key enables principals to verify that messages from the Kerberos server are authentic. By trusting the Kerberos server, users and services can authenticate one another. 

Setting up Lustre with Kerberos can provide advanced security protections for the Lustre network. Broadly, Kerberos offers three types of benefit:

• Allows Lustre connection peers (MDS, OSS and clients) to authenticate one another. 
• Protects the integrity of PTLRPC messages from being modified during network transfer. 
• Protects the privacy of the PTLRPC message from being eavesdropped during network transfer.

Kerberos uses the “kernel keyring” client upcall mechanism.

### Security Flavor

A security flavor is a string to describe what kind authentication and data transformation be performed upon a PTLRPC connection. It covers both RPC message and BULK data.

The supported flavors are described in following table:

| Base Flavor | Authentication | RPC Message Protection   | Bulk Data Protection | Notes                                                        |
| ----------- | -------------- | ------------------------ | -------------------- | ------------------------------------------------------------ |
| null        | N/A            | N/A                      | N/A                  |                                                              |
| krb5n       | GSS/Kerberos5  | null                     | checksum             | No protection of RPC message, checksum protection of bulk data, light performance overhead. |
| krb5a       | GSS/Kerberos5  | partial integrity (krb5) | checksum             | Only header of RPC message is integrity protected, and checksum protection of bulk data, more performance overhead compare to krb5n. |
| krb5i       | GSS/Kerberos5  | integrity (krb5)         | integrity (krb5)     | transformation algorithm is determined by actual Kerberos algorithms enforced by KDC and principals; heavy performance penalty. |
| krb5p       | GSS/Kerberos5  | privacy (krb5)           | privacy (krb5)       | transformation privacy protection algorithm is determined by actual Kerberos algorithms enforced by KDC and principals; the heaviest performance penalty. |

### Kerberos Setup

#### Distribution

We only support MIT Kerberos 5, from version 1.3. 

For environmental requirements in general, and clock synchronization in particular, please refer to section Section 8.1.2, “Environmental Requirements”.

#### Principals Configuration

* Configure client nodes:

  * For each client node, create a lustre_root principal and generate keytab.

    ```
    kadmin> addprinc -randkey lustre_root/client_host.domain@REALM
    kadmin> ktadd lustre_root/client_host.domain@REALM
    ```

  * Install the keytab on the client node.
  
* Configure MGS nodes:
  
  * For each MGS node, create a lustre_mgs principal and generate keytab.
  
    ```
    kadmin> addprinc -randkey lustre_mgs/mgs_host.domain@REALM
    kadmin> ktadd lustre_mds/mgs_host.domain@REALM
    ```
  
  * Install the keytab on the MGS nodes.
  
* Configure MDS nodes:

  * For each MDS node, create a lustre_mds principal and generate keytab.

    ```
    kadmin> addprinc -randkey lustre_mds/mds_host.domain@REALM
    kadmin> ktadd lustre_mds/mds_host.domain@REALM
    ```

  * Install the keytab on the MDS nodes.

* Configure OSS nodes:

  * For each OSS node, create a lustre_oss principal and generate keytab.

    ```
    kadmin> addprinc -randkey lustre_oss/oss_host.domain@REALM
    kadmin> ktadd lustre_oss/oss_host.domain@REALM
    ```

  * Install the keytab on the client node.

    **Note**

  * The host.domain should be the FQDN in your network, otherwise server might not recognize any GSS request.

  * As an alternative for the client keytab, if you want to save the trouble of assigning unique keytab for each client node, you can create a general lustre_root principal and its keytab, and install the same keytab on as many client nodes as you want. **Be aware that in this way one compromised client means all clients are insecure.**

    ```
    kadmin> addprinc -randkey lustre_root@REALM
    kadmin> ktadd lustre_root@REALM
    ```

  * Lustre support following enctypes for MIT Kerberos 5 version 1.3 or higher

    * aes128-cts 
    * aes256-cts

### Networking

On networks for which name resolution to IP address is possible, like TCP or InfiniBand, the names used in the principals must be the ones that resolve to the IP addresses used by the Lustre NIDs. 

If you are using a network which is **NOT** TCP or InfiniBand (e.g. PTL4LND), you need to have a / etc/lustre/nid2hostname script on **each** node, which purpose is to translate NID into hostname. Following is a possible example for PTL4LND:

```
#!/bin/bash
set -x
# convert a NID for a LND to a hostname
# called with thre arguments: lnd netid nid
# $lnd is the string "PTL4LND", etc.
# $netid is the network identifier in hex string format
# $nid is the NID in hex format
# output the corresponding hostname,
# or error message leaded by a '@' for error logging.
lnd=$1
netid=$2
# convert hex NID number to decimal
nid=$((0x$3))
case $lnd in
 PTL4LND) # simply add 'node' at the beginning
 echo "node$nid"
 ;;
 *)
 echo "@unknown LND: $lnd"
 ;;
esac
```

### Required packages

Every node should have following packages installed:

• krb5-workstation 

• krb5-libs 

• keyutils 

• keyutils-libs

On the node used to build Lustre with GSS support, following packages should be installed:

• krb5-devel 

• keyutils-libs-devel

### Build Lustre

Enable GSS at configuration time:

```
./configure --enable-gss --other-options
```

### Running

#### GSS Daemons

Make sure to start the daemon process lsvcgssd on each server node (MGS, MDS and OSS) before starting Lustre. The command syntax is:

```
lsvcgssd [-f] [-v] [-g] [-m] [-o] -k
```

• -f: run in foreground, instead of as daemon 

• -v: increase verbosity by 1. For example, to set the verbose level to 3, run 'lsvcgssd -vvv'. Verbose logging can help you make sure Kerberos is set up correctly. 

• -g: service MGS 

• -m: service MDS 

• -o: service OSS 

• -k: enable kerberos support

#### Setting Security Flavors

Security flavors can be set by defining sptlrpc rules on the MGS. These rules are persistent, and are in the form: ` <spec>=<flavor>`

* To add a rule:

  ```
  mgs> lctl conf_param <spec>=<flavor>
  ```

  If there is an existing rule on `<spec>`, it will be overwritten.

* To delete a rule:

  ```
  mgs> lctl conf_param -d <spec>
  ```

* To list existing rules:

  ```
  msg> lctl get_param mgs.MGS.live.<fs-name> | grep "srpc.flavor"
  ```

  **Note**

  * If nothing is specified, by default all RPC connections will use null flavor, which means no security.
  * After you change a rule, it usually takes a few minutes to apply the new rule to all nodes, depending on global system load.
  * Before you change a rule, make sure affected nodes are ready for the new security flavor. E.g. if you change flavor from null to krb5p but GSS/Kerberos environment is not properly configured on affected nodes, those nodes might be evicted because they cannot communicate with each other.

####  Rules Syntax & Examples

The general syntax is: `<target>.srpc.flavor.<network <direction>]=flavor`
* `<target>` can be filesystem name, or specific MDT/OST device name. For example `testfs, testfs-MDT0000, testfs-OST0001`.

* `<network>` is the LNet network name, for example tcp0, o2ib0, or default to not filter on LNet network.
* `<direction>` can be one of cli2mdt, cli2ost, mdt2mdt, mdt2ost. Direction is optional.

Examples:

* Apply krb5i on ALL connections for file system testfs:
```
mgs> lctl conf_param testfs.srpc.flavor.default=krb5i
```
* Nodes in network tcp0 use krb5p; all other nodes use null.
```
mgs> lctl conf_param testfs.srpc.flavor.tcp0=krb5p
mgs> lctl conf_param testfs.srpc.flavor.default=null
```
* Nodes in network tcp0 use krb5p; nodes in o2ib0 use krb5n; among other nodes, clients use krb5i to MDT/OST, MDTs use null to other MDTs, MDTs use krb5a to OSTs.
```
mgs> lctl conf_param testfs.srpc.flavor.tcp0=krb5p
mgs> lctl conf_param testfs.srpc.flavor.o2ib0=krb5n
mgs> lctl conf_param testfs.srpc.flavor.default.cli2mdt=krb5i
mgs> lctl conf_param testfs.srpc.flavor.default.cli2ost=krb5i
mgs> lctl conf_param testfs.srpc.flavor.default.mdt2mdt=null
mgs> lctl conf_param testfs.srpc.flavor.default.mdt2ost=krb5a
```
#### Regular Users Authentication

On client nodes, non-root users need to issue kinit before accessing Lustre, just like other Kerberized applications.
• Required by kerberos, the user's principal (username@REALM) should be added to the KDC.
• Client and MDT nodes should have the same user database used for name and uid/gid translation. 

Regular users can destroy the established security contexts before logging out, by issuing:
```
lfs flushctx -k -r <mount point>
```
Here -k is to destroy the on-disk Kerberos credential cache, similar to kdestroy, and -r is to reap the revoked keys from the keyring when flushing the GSS context. Otherwise it only destroys established contexts in kernel memory.

### Secure MGS connection

Each node can specify which flavor to use to connect to the MGS, by using the mgssec=flavor mount option. Once a flavor is chosen, it cannot be changed until re-mount. 

Because a Lustre node only has one connection to the MGS, if there is more than one target or client on the node, they necessarily use the same security flavor to the MGS, being the one enforced when the first connection to the MGS was established. 

By default, the MGS accepts RPCs with any flavor. But it is possible to configure the MGS to only accept a given flavor. The syntax is identical to what is explained in paragraph Section 30.6.7.3, “Rules Syntax & Examples”, but with special target _mgs:

```
mgs> lctl conf_param _mgs.srpc.flavor.<network>=<flavor>
```


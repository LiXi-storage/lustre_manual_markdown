## User Utilities

- [User Utilities](#user-utilities)
- [`lfs`](#lfs)
  * [Synopsis](#synopsis)
  * [Description](#description)
  * [Options](#options)
  * [Examples](#examples)
  * [See Also](#see-also)
- [`lfs_migrate`](#lfs_migrate)
  * [Synopsis](#synopsis-1)
  * [Description](#description-1)
  * [Options](#options-1)
  * [Examples](#examples-1)
  * [See Also](#see-also-1)
- [`filefrag`](#filefrag)
  * [Synopsis](#synopsis-2)
  * [Description](#description-2)
  * [Options](#options-2)
  * [Examples](#examples-2)
- [`mount`](#mount)
- [Handling Timeouts](#handling-timeouts)


This chapter describes user utilities.

## `lfs`

The `lfs` utility can be used for user configuration routines and monitoring.

### Synopsis

```
lfs
lfs changelog [--follow] mdt_name [startrec [endrec]]
lfs changelog_clear mdt_name id endrec
lfs check mds|osts|servers
lfs data_version [-nrw] filename
lfs df [-i] [-h] [--pool]-p fsname[.pool] [path] [--lazy]
lfs find [[!] --atime|-A [-+]N] [[!] --mtime|-M [-+]N]
         [[!] --ctime|-C [-+]N] [--maxdepth|-D N] [--name|-n pattern]
         [--print|-p] [--print0|-P] [[!] --obd|-O ost_name[,ost_name...]]
         [[!] --size|-S [+-]N[kMGTPE]] --type |-t {bcdflpsD}]
         [[!] --gid|-g|--group|-G gname|gid]
         [[!] --uid|-u|--user|-U uname|uid]
         dirname|filename
lfs getname [-h]|[path...]
lfs getstripe [--obd|-O ost_name] [--quiet|-q] [--verbose|-v]		  
              [--stripe-count|-c] [--stripe-index|-i]
              [--stripe-size|-s] [--pool|-p] [--directory|-d]
              [--mdt-index|-M] [--recursive|-r] [--raw|-R]
              [--layout|-L]
              dirname|filename ...
lfs setstripe [--size|-s stripe_size]  [--stripe-count|-c stripe_count]
              [--overstripe-count|-C stripe_count]
              [--stripe-index|-i start_ost_index]
              [--ost-list|-o ost_indicies]
              [--pool|-p pool]
              dirname|filename
lfs setstripe -d dir
lfs osts [path]
lfs pool_list filesystem[.pool]| pathname
lfs quota [-q] [-v] [-h] [-o obd_uuid|-I ost_idx|-i mdt_idx]
          [-u username|uid|-g group|gid|-p projid] /mount_point
lfs quota -t -u|-g|-p /mount_point
lfs quotacheck [-ug] /mount_point
lfs quotachown [-i] /mount_point
lfs quotainv [-ug] [-f] /mount_point
lfs quotaon [-ugf] /mount_point
lfs quotaoff [-ug] /mount_point
lfs setquota {-u|--user|-g|--group|-p|--project} uname|uid|gname|gid|projid
             [--block-softlimit block_softlimit]
             [--block-hardlimit block_hardlimit]
             [--inode-softlimit inode_softlimit]
             [--inode-hardlimit inode_hardlimit]
             /mount_point
lfs setquota -u|--user|-g|--group|-p|--project uname|uid|gname|gid|projid
             [-b block_softlimit] [-B block_hardlimit]
             [-i inode-softlimit] [-I inode_hardlimit]
             /mount_point
lfs setquota -t -u|-g|-p [--block-grace block_grace]
             [--inode-grace inode_grace]
             /mount_point
lfs setquota -t -u|-g|-p [-b block_grace] [-i inode_grace]
             /mount_point
lfs help
```

**Note**

In the above example, the *`/mount_point `* parameter refers to the mount point of the Lustre file system.

**Note**

The old lfs quota output was very detailed and contained cluster-wide quota statistics (including cluster-wide limits for a user/group and cluster-wide usage for a user/group), as well as statistics for each MDS/OST. Now, `lfs quota` has been updated to provide only cluster-wide statistics, by default. To obtain the full report of cluster-wide limits, usage and statistics, use the `-v` option with `lfs quota`.

The `quotacheck, quotaon` and `quotaoff` sub-commands were deprecated in the Lustre 2.4 release, and removed completely in the Lustre 2.8 release. See Section “ Enabling Disk Quotas” for details on configuring and checking quotas.

### Description

The `lfs` utility is used to create a new file with a specific striping pattern, determine the default striping pattern, gather the extended attributes (object numbers and location) for a specific file, find files with specific attributes, list OST information or set quota limits. It can be invoked interactively without any arguments or in a non-interactive mode with one of the supported arguments.

### Options

The various `lfs` options are listed and described below. For a complete list of available options, type help at the `lfs`prompt.

| **Option**                                                   | **Description**                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `changelog`                                                  | Shows the metadata changes on an MDT. Start and end points are optional. The `--follow` option blocks on new changes; this option is only valid when run directly on the MDT node. |                                                              |
| `changelog_clear`                                            | Indicates that changelog records previous to `*endrec* `are no longer of interest to a particular consumer `*id* `, potentially allowing the MDT to free up disk space. An `*endrec* `of 0 indicates the current last record. Changelog consumers must be registered on the MDT node using `lctl`. |                                                              |
| `check`                                                      | Displays the status of MDS or OSTs (as specified in the command) or all servers (MDS and OSTs). |                                                              |
| `data_version [-nrw] *filename*`                             | Displays the current version of file data. If `-n` is specified, the data version is read without taking a lock. As a consequence, the data version could be outdated if there are dirty caches on filesystem clients, but this option will not force data flushes and has less of an impact on the filesystem. If `-r` is specified, the data version is read after dirty pages on clients are flushed. If `-w` is specified, the data version is read after all caching pages on clients are flushed.Even with `-r` or `-w`, race conditions are possible and the data version should be checked before and after an operation to be confident the data did not change during it.The data version is the sum of the last committed transaction numbers of all data objects of a file. It is used by HSM policy engines for verifying that file data has not been changed during an archive operation or before a release operation, and by OST migration, primarily for verifying that file data has not been changed during a data copy, when done in non-blocking mode. |                                                              |
| `df [-i] [-h] [--pool|-p*fsname*[. *pool*] [ *path*] [--lazy]` | Use `-i` to report file system disk space usage or inode usage of each MDT or OST or, if a pool is specified with the `-p` option, a subset of OSTs.By default, the usage of all mounted Lustre file systems is reported. If the`path` option is included, only the usage for the specified file system is reported. If the `-h` option is included, the output is printed in human-readable format, using SI base-2 suffixes for **M**ega-, **G**iga-, **T**era-, **P**eta-, or**E**xabytes.If the `--lazy` option is specified, any OST that is currently disconnected from the client will be skipped. Using the `--lazy` option prevents the `df` output from being blocked when an OST is offline. Only the space on the OSTs that can currently be accessed are returned. The `llite.*.lazystatfs` tunable can be enabled to make this the default behaviour for all `statfs()`operations. |                                                              |
| `find`                                                       | Searches the directory tree rooted at the given directory/filename for files that match the given parameters.Using `!` before an option negates its meaning (files NOT matching the parameter). Using `+` before a numeric value means files with the parameter OR MORE. Using `-` before a numeric value means files with the parameter OR LESS. |                                                              |
|                                                              | `--atime`                                                    | File was last accessed N*24 hours ago. (There is no guarantee that `atime` is kept coherent across the cluster.) OSTs by default only hold a transient `atime` that is updated when clients do read requests. Permanent `atime` is written to the MDT when the file is closed. However, on-disk atime is only updated if it is more than 60 seconds old (`mdd.*.atime_diff`). <br />In Lustre 2.14, it is possible to set the OSTs to persistently store atime with each object, in order to get more accurate persistent atime updates for files that are open for a long time via the similarly-named `obdfilter.*.atime_diff `parameter.<br />The client considers the latest atime from all OSTs and MDTs. If a setattr is set by user, then it is updated on both the MDT and OST, allowing the atime to go backward. |
|                                                              | `--ctime`                                                    | File status was last changed N*24 hours ago.                 |
|                                                              | `--mtime`                                                    | File data was last modified N*24 hours ago.                  |
|                                                              | `--obd`                                                      | File has an object on a specific OST(s).                     |
|                                                              | `--size`                                                     | File has a size in bytes, or kilo-, Mega-, Giga-, Tera-, Peta- or Exabytes if a suffix is given. |
|                                                              | `--type`                                                     | File has the type - block, character, directory, pipe, file, symlink, socket or door (used in Solaris operating system). |
|                                                              | `--uid`                                                      | File has a specific numeric user ID.                         |
|                                                              | `--user`                                                     | File owned by a specific user (numeric user ID allowed).     |
|                                                              | `--gid`                                                      | File has a specific group ID.                                |
|                                                              | `--group`                                                    | File belongs to a specific group (numeric group ID allowed). |
|                                                              | - `-maxdepth`                                                | Limits find to descend at most N levels of the directory tree. |
|                                                              | `--print`/ `--print0`                                        | Prints the full filename, followed by a new line or NULL character correspondingly. |
| `osts [path]`                                                | Lists all OSTs for the file system. If a path located on a mounted Lustre file system is specified, then only OSTs belonging to this file system are displayed. |                                                              |
| `getname [path...]`                                          | List each Lustre file system instance associated with each Lustre mount point. If no path is specified, all Lustre mount points are interrogated. If a list of paths is provided, the instance of each path is provided. If the path is not a Lustre instance 'No such device' is returned. |                                                              |
| `getstripe`                                                  | Lists striping information for a given filename or directory. By default, the stripe count, stripe size and offset are returned.If you only want specific striping information, then the options of `--stripe-count`, `--stripe-size`, `--stripe-index`, `--layout`, or `--pool` plus various combinations of these options can be used to retrieve specific information.If the `--raw` option is specified, the stripe information is printed without substituting the file system default values for unspecified fields. If the striping EA is not set, 0, 0, and -1 will be printed for the stripe count, size, and offset respectively.Introduced in Lustre 2.4The `--mdt-index` prints the index of the MDT for a given directory. See [*the section called “Removing an MDT from the File System”*](03.03-Lustre%20Maintenance.md#removing-and-restoring-mdts-and-osts)). |                                                              |
|                                                              | `--obd *ost_name*`                                           | Lists files that have an object on a specific OST.           |
|                                                              | `--quiet`                                                    | Lists details about the file's object ID information.        |
|                                                              | `--stripe-count`                                             | Prints additional striping information.                      |
|                                                              | `--count`                                                    | Lists the stripe count (how many OSTs to use).               |
|                                                              | `--index`                                                    | Lists the index for each OST in the file system.             |
|                                                              | `--offset`                                                   | Lists the OST index on which file striping starts.           |
|                                                              | `--pool`                                                     | Lists the pools to which a file belongs.                     |
|                                                              | `--size`                                                     | Lists the stripe size (how much data to write to one OST before moving to the next OST). |
|                                                              | `--directory`                                                | Lists entries about a specified directory instead of its contents (in the same manner as `ls -d`). |
|                                                              | `--recursive`                                                | Recurses into all sub-directories.                           |
| `setstripe`                                                  |                                                              | Create new files with a specific file layout (stripe pattern) configuration[a]. |
|                                                              | `--stripe-count stripe_cnt`                                  | Number of OSTs over which to stripe a file. A `stripe_cnt` of 0 uses the file system-wide default stripe count (default is 1). A `stripe_cnt` of -1 stripes over all available OSTs. |
|                                                              | `--overstripe-count stripe_cnt`                              | The same as --stripe-count, but allows overstriping, which will place more than one stripe per OST if `stripe_cnt` is greater than the number of OSTs. Overstriping is useful for matching the number of stripes to the number of processes, or with very fast OSTs, where one stripe per OST is not enough to get full performance. |
|                                                              | `--size stripe_size`[b]                                      | Number of bytes to store on an OST before moving to the next OST. A stripe_size of 0 uses the file system's default stripe size, (default is 1 MB). Can be specified with **k**(KB), **m**(MB), or **g**(GB), respectively. |
|                                                              | `--stripe-index start_ost_index`                             | The OST index (base 10, starting at 0) on which to start striping for this file. A start_ost_index value of -1 allows the MDS to choose the starting index. This is the default value, and it means that the MDS selects the starting OST as it wants. We strongly recommend selecting this default, as it allows space and load balancing to be done by the MDS as needed. The `start_ost_index`value has no relevance on whether the MDS will use round-robin or QoS weighted allocation for the remaining stripes in the file. |
|                                                              | `--ost-index ost_indices`                                    | This option is used to specify the exact stripe layout on the the file system. `ost_indices` is a list of OSTs referenced by their indices and index ranges separated by commas, e.g. `1,2-4,7`. |
|                                                              | `--pool pool`                                                | Name of the pre-defined pool of OSTs (see [*the section called “ lctl”*](06.07-System%20Configuration%20Utilities.md#lctl)) that will be used for striping. The `stripe_cnt`, `stripe_size` and `start_ost` values are used as well. The start-ost value must be part of the pool or an error is returned. |
| `setstripe -d`                                               | Deletes default striping on the specified directory.         |                                                              |
| `pool_list {filesystem}[.poolname]|{pathname}`               | Lists pools in the file system or pathname, or OSTs in the file system's pool. |                                                              |
| `quota [-q] [-v] [-o*obd_uuid*|-i *mdt_idx*|-I*ost_idx*] [-u|-g|-p*uname|uid|gname|gid|projid]**/mount_point*` | Displays disk usage and limits, either for the full file system or for objects on a specific OBD. A user or group name or an usr, group and project ID can be specified. If all user, group project ID are omitted, quotas for the current UID/GID are shown. The `-q` option disables printing of additional descriptions (including column titles). It fills in blank spaces in the `grace` column with zeros (when there is no grace period set), to ensure that the number of columns is consistent. The `-v` option provides more verbose (per-OBD statistics) output. |                                                              |
| `quota -t *-u|-g|-p**/mount_point*`                          | Displays block and inode grace times for user ( `-u`) or group ( `-g`) or project ( `-p`) quotas. |                                                              |
| `setquota {-u|-g|-p uname|uid|gname|gid|projid}  [--block-softlimit block_softlimit ] [--block-hardlimit block_hardlimit] [--inode-softlimit inode_softlimit] [--inode-hardlimit inode_hardlimit] /mount_point` | Sets file system quotas for users, groups or one project. Limits can be specified with `--{block|inode}-{softlimit|hardlimit}` or their short equivalents `-b`, `-B`, `-i`, `-I`. Users can set 1, 2, 3 or 4 limits. [c]Also, limits can be specified with special suffixes, -b, -k, -m, -g, -t, and -p to indicate units of 1, 2^10, 2^20, 2^30, 2^40 and 2^50, respectively. By default, the block limits unit is 1 kilobyte (1,024), and block limits are always kilobyte-grained (even if specified in bytes). See [*the section called “Examples”*](#examples). |                                                              |
| `setquota -t -u|-g|-p [--block-grace *block_grace*] [--inode-grace *inode_grace*]*/mount_point*` | Sets the file system quota grace times for users or groups. Grace time is specified in ' `XXwXXdXXhXXmXXs`' format or as an integer seconds value. See [*the section called “Examples”*](#examples). |                                                              |
| `help`                                                       | Provides brief help on various `lfs` arguments.              |                                                              |
| `exit/quit`                                                  | Quits the interactive `lfs` session.                         |                                                              |
| [a]The file cannot exist prior to using `setstripe`. A directory must exist prior to using `setstripe`.[b]The default stripe-size is 0. The default start-ost is -1. Do NOT confuse them! If you set start-ost to 0, all new file creations occur on OST 0 (seldom a good idea).                                                                                [c]The old `setquota` interface is supported, but it may be removed in a future Lustre software release. |                                                              |                                                              |

### Examples

Creates a file striped on two OSTs with 128 KB on each stripe.

```
$ lfs setstripe -s 128k -c 2 /mnt/lustre/file1
```

Deletes a default stripe pattern on a given directory. New files use the default striping pattern.

```
$ lfs setstripe -d /mnt/lustre/dir
```

Lists the detailed object allocation of a given file.

```
$ lfs getstripe -v /mnt/lustre/file1
```

List all the mounted Lustre file systems and corresponding Lustre instances.

```
$ lfs getname
```

Efficiently lists all files in a given directory and its subdirectories.

```
$ lfs find /mnt/lustre
```

Recursively lists all regular files in a given directory more than 30 days old.

```
$ lfs find /mnt/lustre -mtime +30 -type f -print
```

Recursively lists all files in a given directory that have objects on OST2-UUID. The lfs check servers command checks the status of all servers (MDT and OSTs).

```
$ lfs find --obd OST2-UUID /mnt/lustre/
```

Lists all OSTs in the file system.

```
$ lfs osts
```

Lists space usage per OST and MDT in human-readable format.

```
$ lfs df -h
```

Lists inode usage per OST and MDT.

```
$ lfs df -i
```

List space or inode usage for a specific OST pool.

```
$ lfs df --pool 
filesystem[.
pool] | 
pathname
```

List quotas of user 'bob'.

```
$ lfs quota -u bob /mnt/lustre
```

List quotas of project ID '1'.

```
$ lfs quota -p 1 /mnt/lustre
```

Show grace times for user quotas on `/mnt/lustre`.

```
$ lfs quota -t -u /mnt/lustre
```

Sets quotas of user 'bob', with a 1 GB block quota hardlimit and a 2 GB block quota softlimit.

```
$ lfs setquota -u bob --block-softlimit 2000000 --block-hardlimit 1000000
/mnt/lustre
```

Sets grace times for user quotas: 1000 seconds for block quotas, 1 week and 4 days for inode quotas.

```
$ lfs setquota -t -u --block-grace 1000 --inode-grace 1w4d /mnt/lustre
```

Checks the status of all servers (MDT, OST)

```
$ lfs check servers
```

Creates a file striped on two OSTs from the pool `my_pool`

```
$ lfs setstripe --pool my_pool -c 2 /mnt/lustre/file
```

Lists the pools defined for the mounted Lustre file system `/mnt/lustre`

```
$ lfs pool_list /mnt/lustre/
```

Lists the OSTs which are members of the pool `my_pool` in file system `my_fs`

```
$ lfs pool_list my_fs.my_pool
```

Finds all directories/files associated with `poolA`.

```
$ lfs find /mnt/lustre --pool poolA
```

Finds all directories/files not associated with a pool.

```
$ lfs find /mnt//lustre --pool ""
```

Finds all directories/files associated with pool.

```
$ lfs find /mnt/lustre ! --pool ""
```

Associates a directory with the pool `my_pool`, so all new files and directories are created in the pool.

```
$ lfs setstripe --pool my_pool /mnt/lustre/dir
```

### See Also

[*the section called “ lctl”*](06.07-System%20Configuration%20Utilities.md#lctl)

## `lfs_migrate`

The `lfs_migrate` utility is a simple to migrate file *data* between OSTs.

### Synopsis

```
lfs_migrate [lfs_setstripe_options]
	[-h] [-n] [-q] [-R] [-s] [-y] [-0] [file|directory ...]
```

### Description

The `lfs_migrate` utility is a tool to assist migration of file data between Lustre OSTs. The utility copies each specified file to a temporary file using supplied `lfs setstripe` options, if any, optionally verifies the file contents have not changed, and then swaps the layout (OST objects) from the temporary file and the original file (for Lustre 2.5 and later), or renames the temporary file to the original filename. This allows the user/administrator to balance space usage between OSTs, or move files off OSTs that are starting to show hardware problems (though are still functional) or will be removed.

**Warning**

For versions of Lustre before 2.5, `lfs_migrate` was not integrated with the MDS at all. That made it UNSAFE for use on files that were being modified by other applications, since the file was migrated through a copy and rename of the file. With Lustre 2.5 and later, the new file layout is swapped with the existing file layout, which ensures that the user-visible inode number is kept, and open file handles and locks on the file are kept.

Files to be migrated can be specified as command-line arguments. If a directory is specified on the command-line then all files within the directory are migrated. If no files are specified on the command-line, then a list of files is read from the standard input, making `lfs_migrate` suitable for use with `lfs find` to locate files on specific OSTs and/or matching other file attributes, and other tools that generate a list of files on standard output.

Unless otherwise specified through command-line options, the file allocation policies on the MDS dictate where the new files are placed, taking into account whether specific OSTs have been disabled on the MDS via `lctl` (preventing new files from being allocated there), whether some OSTs are overly full (reducing the number of files placed on those OSTs), or if there is a specific default file striping for the parent directory (potentially changing the stripe count, stripe size, OST pool, or OST index of a new file).

**Note**

The `lfs_migrate` utility can also be used in some cases to reduce file fragmentation. File fragmentation will typically reduce Lustre file system performance. File fragmentation may be observed on an aged file system and will commonly occur if the file was written by many threads. Provided there is sufficient free space (or if it was written when the file system was nearly full) that is less fragmented than the file being copied, re-writing a file with `lfs_migrate` will result in a migrated file with reduced fragmentation. The tool `filefrag` can be used to report file fragmentation. See [*the section called “ `filefrag` ”*](#filefrag)

**Note**

As long as a file has extent lengths of tens of megabytes ( *read_bandwidth \* seek_time*) or more, the read performance for the file will not be significantly impacted by fragmentation, since the read pipeline can be filled by large reads from disk even with an occasional disk seek.

### Options

Options supporting `lfs_migrate` are described below.

| **Option**        | **Description**                                              |
| ----------------- | ------------------------------------------------------------ |
| `-c*stripecount*` | Restripe file using the specified stripe count. This option may not be specified at the same time as the `-R` option. |
| `-h`              | Display help information.                                    |
| `-l`              | Migrate files with hard links (skips, by default). Files with multiple hard links are split into multiple separate files by `lfs_migrate`, so they are skipped, by default, to avoid breaking the hard links. |
| `-n`              | Only print the names of files to be migrated.                |
| `-q`              | Run quietly (does not print filenames or status).            |
| `-R`              | Restripe file using default directory striping instead of keeping striping. This option may not be specified at the same time as the `-c` option. |
| `-s`              | Skip file data comparison after migrate. Default is to compare migrated file against original to verify correctness. |
| `-y`              | Answer ' `y`' to usage warning without prompting (for scripts, use with caution). |
| `-0`              | Expect NUL-terminated filenames on standard input, as generated by `lfs find -print0` or `find -print0`. This allows filenames with embedded newlines to be handled correctly. |

### Examples

Rebalance all files in `/mnt/lustre/dir`:

```
$ lfs_migrate /mnt/lustre/dir
```

Migrate files in /test filesystem on OST0004 larger than 4 GB in size and older than a day old:

```
$ lfs find /test -obd test-OST0004 -size +4G -mtime +1 | lfs_migrate -y
```

### See Also

*the section called “ `lfs` ”*

## `filefrag`

The `e2fsprogs` package contains the `filefrag` tool which reports the extent of file fragmentation.

### Synopsis

```
filefrag [ -belsv ] [ files...  ]
```

### Description

The `filefrag` utility reports the extent of fragmentation in a given file. The `filefrag` utility obtains the extent information from Lustre files using the `FIEMAP ioctl`, which is efficient and fast, even for very large files.

In default mode [5], `filefrag` prints the number of physically discontiguous extents in the file. In extent or verbose mode, each extent is printed with details such as the blocks allocated on each OST. For a Lustre file system, the extents are printed in device offset order (i.e. all of the extents for one OST first, then the next OST, etc.), not file logical offset order. If the file logical offset order was used, the Lustre striping would make the output very verbose and difficult to see if there was file fragmentation or not.

**Note**

Note that as long as a file has extent lengths of tens of megabytes or more (i.e. *read_bandwidth \* seek_time > extent_length*), the read performance for the file will not be significantly impacted by fragmentation, since file readahead can fully utilize the disk disk bandwidth even with occasional seeks.

In default mode [6], `filefrag` returns the number of physically discontiguous extents in the file. In extent or verbose mode, each extent is printed with details. For a Lustre file system, the extents are printed in device offset order, not logical offset order.

-------------

[5]The default mode is faster than the verbose/extent mode since it only counts the number of extents.

[6]The default mode is faster than the verbose/extent mode 

-------

### Options

The options and descriptions for the `filefrag` utility are listed below.

| **Option** | **Description**                                              |
| ---------- | ------------------------------------------------------------ |
| `-b`       | Uses the 1024-byte blocksize for the output. By default, this blocksize is used by the Lustre file system, since OSTs may use different block sizes. |
| `-e`       | Uses the extent mode when printing the output. This is the default for Lustre files in verbose mode. |
| `-l`       | Displays extents in LUN offset order. This is the only available mode for Lustre. |
| `-s`       | Synchronizes any unwritten file data to disk before requesting the mapping. |
| `-v`       | Prints the file's layout in verbose mode when checking file fragmentation, including the logical to physical mapping for each extent in the file and the OST index. |

### Examples

Lists default output.

```
$ filefrag /mnt/lustre/foo
/mnt/lustre/foo: 13 extents found
```

Lists verbose output in extent format.

```
$ filefrag -v /mnt/lustre/foo
Filesystem type is: bd00bd0
File size of /mnt/lustre/foo is 1468297786 (1433888 blocks of 1024 bytes)
 ext:     device_logical:        physical_offset: length:  dev: flags:
   0:        0..  122879: 2804679680..2804802559: 122880: 0002: network
   1:   122880..  245759: 2804817920..2804940799: 122880: 0002: network
   2:   245760..  278527: 2804948992..2804981759:  32768: 0002: network
   3:   278528..  360447: 2804982784..2805064703:  81920: 0002: network
   4:   360448..  483327: 2805080064..2805202943: 122880: 0002: network
   5:   483328..  606207: 2805211136..2805334015: 122880: 0002: network
   6:   606208..  729087: 2805342208..2805465087: 122880: 0002: network
   7:   729088..  851967: 2805473280..2805596159: 122880: 0002: network
   8:   851968..  974847: 2805604352..2805727231: 122880: 0002: network
   9:   974848.. 1097727: 2805735424..2805858303: 122880: 0002: network
  10:  1097728.. 1220607: 2805866496..2805989375: 122880: 0002: network
  11:  1220608.. 1343487: 2805997568..2806120447: 122880: 0002: network
  12:  1343488.. 1433599: 2806128640..2806218751:  90112: 0002: network
/mnt/lustre/foo: 13 extents found
```

## `mount`

The standard `mount(8)` Linux command is used to mount a Lustre file system. When mounting a Lustre file system, mount(8) executes the `/sbin/mount.lustre` command to complete the mount. The mount command supports these options specific to a Lustre file system:

| **Server options**     | **Description**                                              |
| ---------------------- | ------------------------------------------------------------ |
| `abort_recov`          | Aborts recovery when starting a target                       |
| `nosvc`                | Starts only MGS/MGC servers                                  |
| `nomgs`                | Start a MDT with a co-located MGS without starting the MGS   |
| `exclude`              | Starts with a dead OST                                       |
| `md_stripe_cache_size` | Sets the stripe cache size for server side disk with a striped raid configuration |

| **Client options**              | **Description**                                              |
| ------------------------------- | ------------------------------------------------------------ |
| `flock/noflock/localflock`      | Enables/disables global flock or local flock support         |
| `user_xattr/nouser_xattr`       | Enables/disables user-extended attributes                    |
| `user_fid2path/nouser_fid2path` | Enables/disables FID to path translation by regular users    |
| `retry=`                        | Number of times a client will retry to mount the file system |

## Handling Timeouts

Timeouts are the most common cause of hung applications. After a timeout involving an MDS or failover OST, applications attempting to access the disconnected resource wait until the connection gets established.

When a client performs any remote operation, it gives the server a reasonable amount of time to respond. If a server does not reply either due to a down network, hung server, or any other reason, a timeout occurs which requires a recovery.

If a timeout occurs, a message (similar to this one), appears on the console of the client, and in`/var/log/messages`:

```
LustreError: 26597:(client.c:810:ptlrpc_expire_one_request()) @@@ timeout

req@a2d45200 x5886/t0 o38->mds_svc_UUID@NID_mds_UUID:12 lens 168/64 ref 1 fl

RPC:/0/0 rc 0
```


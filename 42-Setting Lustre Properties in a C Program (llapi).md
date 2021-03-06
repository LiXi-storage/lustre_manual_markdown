## Setting Lustre Properties in a C Program (`llapi`)

- [Setting Lustre Properties in a C Program (`llapi`)](#setting-lustre-properties-in-a-c-program-llapi)
- [`llapi_file_create`](#llapi_file_create)
  * [Synopsis](#synopsis)
  * [Description](#description)
  * [Examples](#examples)
- [llapi_file_get_stripe](#llapi_file_get_stripe)
  * [Synopsis](#synopsis-1)
  * [Description](#description-1)
  * [Return Values](#return-values)
  * [Errors](#errors)
  * [Examples](#examples-1)
- [`llapi_file_open`](#llapi_file_open)
  * [Synopsis](#synopsis-2)
  * [Description](#description-2)
  * [Return Values](#return-values-1)
  * [Errors](#errors-1)
  * [Example](#example)
- [`llapi_quotactl`](#llapi_quotactl)
  * [Synopsis](#synopsis-3)
  * [Description](#description-3)
  * [Return Values](#return-values-2)
  * [Errors](#errors-2)
- [`llapi_path2fid`](#llapi_path2fid)
  * [Synopsis](#synopsis-4)
  * [Description](#description-4)
  * [Return Values](#return-values-3)
- [`llapi_ladvise`](#llapi_ladvise)
  * [Synopsis](#synopsis-5)
  * [Description](#description-5)
  * [Return Values](#return-values-4)
  * [Errors](#errors-3)
- [Example Using the `llapi` Library](#example-using-the-llapi-library)
  * [See Also](#see-also)


This chapter describes the `llapi` library of commands used for setting Lustre file properties within a C program running in a cluster environment, such as a data processing or MPI application. The commands described in this chapter are:

- [the section called “ `llapi_file_create` ”](#llapi_file_create)
- [the section called “ llapi_file_get_stripe”](#llapi_file_get_stripe)
- [the section called “ `llapi_file_open` ”](#llapi_file_open)
- [the section called “ `llapi_quotactl` ”](#llapi_quotactl)
- [the section called “ `llapi_path2fid` ”](#llapi_path2fid)

**Note**

Lustre programming interface man pages are found in the `lustre/doc` folder.

## `llapi_file_create`

Use `llapi_file_create` to set Lustre properties for a new file.

### Synopsis

```
#include <lustre/lustreapi.h>

int llapi_file_create(char *name, long stripe_size, int stripe_offset, int stripe_count, int stripe_pattern);
```

### Description

The `llapi_file_create()` function sets a file descriptor's Lustre file system striping information. The file descriptor is then accessed with `open()`.

| **Option**            | **Description**                                              |
| --------------------- | ------------------------------------------------------------ |
| `llapi_file_create()` | If the file already exists, this parameter returns to '`EEXIST`'. If the stripe parameters are invalid, this parameter returns to '`EINVAL`'. |
| `stripe_size`         | This value must be an even multiple of system page size, as shown by `getpagesize()`. The default Lustre stripe size is 4MB. |
| `stripe_offset`       | Indicates the starting OST for this file.                    |
| `stripe_count`        | Indicates the number of OSTs that this file will be striped across. |
| `stripe_pattern`      | Indicates the RAID pattern.                                  |

**Note**

Currently, only RAID 0 is supported. To use the system defaults, set these values: `stripe_size` = 0, `stripe_offset` = -1, `stripe_count` = 0, `stripe_pattern` = 0

### Examples

System default size is 4 MB.

```
char *tfile = TESTFILE;
int stripe_size = 65536
```

To start at default, run:

```
int stripe_offset = -1
```

To start at the default, run:

```
int stripe_count = 1
```

To set a single stripe for this example, run:

```
int stripe_pattern = 0
```

Currently, only RAID 0 is supported.

```
int stripe_pattern = 0; 
int rc, fd; 
rc = llapi_file_create(tfile, stripe_size,stripe_offset, stripe_count,stripe_pattern);
```

Result code is inverted, you may return with '`EINVAL`' or an ioctl error.

```
if (rc) {
fprintf(stderr,"llapi_file_create failed: %d (%s) 0, rc, strerror(-rc));return -1; }
```

`llapi_file_create` closes the file descriptor. You must re-open the descriptor. To do this, run:

```
fd = open(tfile, O_CREAT | O_RDWR | O_LOV_DELAY_CREATE, 0644); if (fd < 0) \ { 
fprintf(stderr, "Can't open %s file: %s0, tfile,
str-
error(errno));
return -1;
}
```

## llapi_file_get_stripe

Use `llapi_file_get_stripe` to get striping information for a file or directory on a Lustre file system.

### Synopsis

```
#include <lustre/lustreapi.h>
 
int llapi_file_get_stripe(const char *path, void *lum);
```
### Description

The `llapi_file_get_stripe()` function returns striping information for a file or directory *path* in *lum* (which should point to a large enough memory region) in one of the following formats:

```
struct lov_user_md_v1 {
__u32 lmm_magic;
__u32 lmm_pattern;
__u64 lmm_object_id;
__u64 lmm_object_seq;
__u32 lmm_stripe_size;
__u16 lmm_stripe_count;
__u16 lmm_stripe_offset;
struct lov_user_ost_data_v1 lmm_objects[0];
} __attribute__((packed));
struct lov_user_md_v3 {
__u32 lmm_magic;
__u32 lmm_pattern;
__u64 lmm_object_id;
__u64 lmm_object_seq;
__u32 lmm_stripe_size;
__u16 lmm_stripe_count;
__u16 lmm_stripe_offset;
char lmm_pool_name[LOV_MAXPOOLNAME];
struct lov_user_ost_data_v1 lmm_objects[0];
} __attribute__((packed));
```

### Return Values

`llapi_file_get_stripe()` returns:

`0` On success

`!= 0` On failure, `errno` is set appropriately

### Errors

| **Errors**     | **Description**                                     |
| -------------- | --------------------------------------------------- |
| `ENOMEM`       | Failed to allocate memory                           |
| `ENAMETOOLONG` | Path was too long                                   |
| `ENOENT`       | Path does not point to a file or directory          |
| `ENOTTY`       | Path does not point to a Lustre file system         |
| `EFAULT`       | Memory region pointed by lum is not properly mapped |

### Examples

```
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <lustre/lustreapi.h>

static inline int maxint(int a, int b)
{
	return a > b ? a : b;
}
static void *alloc_lum()
{
	int v1, v3, join;
	v1 = sizeof(struct lov_user_md_v1) +
		LOV_MAX_STRIPE_COUNT * sizeof(struct lov_user_ost_data_v1);
	v3 = sizeof(struct lov_user_md_v3) +
		LOV_MAX_STRIPE_COUNT * sizeof(struct lov_user_ost_data_v1);
	return malloc(maxint(v1, v3));
}
int main(int argc, char** argv)
{
	struct lov_user_md *lum_file = NULL;
	int rc;
	int lum_size;
	if (argc != 2) {
		fprintf(stderr, "Usage: %s <filename>\n", argv[0]);
		return 1;
	}
	lum_file = alloc_lum();
	if (lum_file == NULL) {
		rc = ENOMEM;
		goto cleanup;
	}
	rc = llapi_file_get_stripe(argv[1], lum_file);
	if (rc) {
		rc = errno;
		goto cleanup;
	}
	/* stripe_size stripe_count */
	printf("%d %d\n",
			lum_file->lmm_stripe_size,
			lum_file->lmm_stripe_count);
cleanup:
	if (lum_file != NULL)
		free(lum_file);
	return rc;
}
```

## `llapi_file_open`

The `llapi_file_open` command opens (or creates) a file or device on a Lustre file system.

### Synopsis

```
#include <lustre/lustreapi.h>
int llapi_file_open(const char *name, int flags, int mode, 
   unsigned long long stripe_size, int stripe_offset, 
   int stripe_count, int stripe_pattern);
int llapi_file_create(const char *name, unsigned long long stripe_size, 
   int stripe_offset, int stripe_count, 
   int stripe_pattern);
```

### Description

The `llapi_file_create()` call is equivalent to the `llapi_file_open` call with *flags* equal to `O_CREAT|O_WRONLY`and *mode* equal to `0644`, followed by file close.

`llapi_file_open()` opens a file with a given name on a Lustre file system.

| **Option**       | **Description**                                              |
| ---------------- | ------------------------------------------------------------ |
| `flags`          | Can be a combination of `O_RDONLY`, `O_WRONLY`, `O_RDWR`, `O_CREAT`, `O_EXCL`, `O_NOCTTY`, `O_TRUNC`, `O_APPEND`, `O_NONBLOCK`, `O_SYNC`, `FASYNC`, `O_DIRECT`, `O_LARGEFILE`, `O_DIRECTORY`, `O_NOFOLLOW`, `O_NOATIME`. |
| `mode`           | Specifies the permission bits to be used for a new file when `O_CREAT` is used. |
| `stripe_size`    | Specifies stripe size (in bytes). Should be multiple of 64 KB, not exceeding 4 GB. |
| `stripe_offset`  | Specifies an OST index from which the file should start. The default value is -1. |
| `stripe_count`   | Specifies the number of OSTs to stripe the file across. The default value is -1. |
| `stripe_pattern` | Specifies the striping pattern. In this release of the Lustre software, only `LOV_PATTERN_RAID0`is available. The default value is 0. |

### Return Values

`llapi_file_open()` and `llapi_file_create()` return:

`>=0` On success, for `llapi_file_open` the return value is a file descriptor

`<0` On failure, the absolute value is an error code

### Errors

| **Errors** | **Description**                                              |
| ---------- | ------------------------------------------------------------ |
| `EINVAL`   | `stripe_size` or `stripe_offset` or `stripe_count` or `stripe_pattern` is invalid. |
| `EEXIST`   | Striping information has already been set and cannot be altered; `name` already exists. |
| `EALREADY` | Striping information has already been set and cannot be altered |
| `ENOTTY`   | `name` may not point to a Lustre file system.                |

### Example

```
#include <stdio.h>
#include <lustre/lustreapi.h>

int main(int argc, char *argv[])
{
	int rc;
	if (argc != 2)
		return -1;
	rc = llapi_file_create(argv[1], 1048576, 0, 2, LOV_PATTERN_RAID0);
	if (rc < 0) {
		fprintf(stderr, "file creation has failed, %s\n",         strerror(-rc));
		return -1;
	}
	printf("%s with stripe size 1048576, striped across 2 OSTs,"
			" has been created!\n", argv[1]);
	return 0;
}
```

## `llapi_quotactl`

Use `llapi_quotact`l to manipulate disk quotas on a Lustre file system.

### Synopsis

```
#include <lustre/lustreapi.h>
int llapi_quotactl(char" " *mnt," " struct if_quotactl" " *qctl)
 
struct if_quotactl {
        __u32                   qc_cmd;
        __u32                   qc_type;
        __u32                   qc_id;
        __u32                   qc_stat;
        struct obd_dqinfo       qc_dqinfo;
        struct obd_dqblk        qc_dqblk;
        char                    obd_type[16];
        struct obd_uuid         obd_uuid;
};
struct obd_dqblk {
        __u64 dqb_bhardlimit;
        __u64 dqb_bsoftlimit;
        __u64 dqb_curspace;
        __u64 dqb_ihardlimit;
        __u64 dqb_isoftlimit;
        __u64 dqb_curinodes;
        __u64 dqb_btime;
        __u64 dqb_itime;
        __u32 dqb_valid;
        __u32 padding;
};
struct obd_dqinfo {
        __u64 dqi_bgrace;
        __u64 dqi_igrace;
        __u32 dqi_flags;
        __u32 dqi_valid;
};
struct obd_uuid {
        char uuid[40];
};
```

### Description

The `llapi_quotactl()` command manipulates disk quotas on a Lustre file system mount. qc_cmd indicates a command to be applied to UID `qc_id` or GID `qc_id`.

| **Option**          | **Description**                                              |
| ------------------- | ------------------------------------------------------------ |
| `LUSTRE_Q_GETQUOTA` | Gets disk quota limits and current usage for user or group *qc_id*. *qc_type* is `USRQUOTA` or `GRPQUOTA`. *uuid* may be filled with `OBD UUID` string to query quota information from a specific node. *dqb_valid* may be set nonzero to query information only from MDS. If *uuid* is an empty string and *dqb_valid* is zero then cluster-wide limits and usage are returned. On return, *obd_dqblk* contains the requested information (block limits unit is kilobyte). Quotas must be turned on before using this command. |
| `LUSTRE_Q_SETQUOTA` | Sets disk quota limits for user or group *qc_id*. *qc_type* is `USRQUOTA` or `GRPQUOTA`. *dqb_valid*must be set to `QIF_ILIMITS`, `QIF_BLIMITS` or `QIF_LIMITS` (both inode limits and block limits) dependent on updating limits. *obd_dqblk* must be filled with limits values (as set in *dqb_valid*, block limits unit is kilobyte). Quotas must be turned on before using this command. |
| `LUSTRE_Q_GETINFO`  | Gets information about quotas. *qc_type* is either `USRQUOTA` or `GRPQUOTA`. On return,*dqi_igrace* is inode grace time (in seconds), *dqi_bgrace* is block grace time (in seconds),*dqi_flags* is not used by the current release of the Lustre software. |
| `LUSTRE_Q_SETINFO`  | Sets quota information (like grace times). *qc_type* is either `USRQUOTA` or `GRPQUOTA`.*dqi_igrace* is inode grace time (in seconds), *dqi_bgrace* is block grace time (in seconds),*dqi_flags* is not used by the current release of the Lustre software and must be zeroed. |

### Return Values

`llapi_quotactl()` returns:

`0` On success

`-1 `On failure and sets error number (`errno`) to indicate the error

### Errors

`llapi_quotactl` errors are described below.

| **Errors** | **Description**                                              |
| ---------- | ------------------------------------------------------------ |
| `EFAULT`   | *qctl* is invalid.                                           |
| `ENOSYS`   | Kernel or Lustre modules have not been compiled with the `QUOTA` option. |
| `ENOMEM`   | Insufficient memory to complete operation.                   |
| `ENOTTY`   | *qc_cmd* is invalid.                                         |
| `ENOENT`   | *uuid* does not correspond to OBD or *mnt* does not exist.   |
| `EPERM`    | The call is privileged and the caller is not the super user. |
| `ESRCH`    | No disk quota is found for the indicated user. Quotas have not been turned on for this file system. |

## `llapi_path2fid`

Use `llapi_path2fid` to get the FID from the pathname.

### Synopsis

```
#include <lustre/lustreapi.h>
 
int llapi_path2fid(const char *path, unsigned long long *seq, unsigned long *oid, unsigned long *ver)
```

### Description

The `llapi_path2fid` function returns the FID (sequence : object ID : version) for the pathname.

### Return Values

`llapi_path2fid` returns:

`0` On success

non-zero value On failure



Introduced in Lustre 2.9

## `llapi_ladvise`

Use `llapi_ladvise` to give IO advice/hints on a Lustre file to the server.

### Synopsis

```
#include <lustre/lustreapi.h>
int llapi_ladvise(int fd, unsigned long long flags,
                  int num_advise, struct llapi_lu_ladvise *ladvise);
                                
struct llapi_lu_ladvise {
  __u16 lla_advice;       /* advice type */
  __u16 lla_value1;       /* values for different advice types */
  __u32 lla_value2;
  __u64 lla_start;        /* first byte of extent for advice */
  __u64 lla_end;          /* last byte of extent for advice */
  __u32 lla_value3;
  __u32 lla_value4;
};
```

### Description

The `llapi_ladvise` function passes an array of *num_advise* I/O hints (up to a maximum of *LAH_COUNT_MAX*items) in ladvise for the file descriptor *fd* from an application to one or more Lustre servers. Optionally, *flags* can modify how the advice will be processed via bitwise-or'd values:

- `LF_ASYNC`: Clients return to userspace immediately after submitting ladvise RPCs, leaving server threads to handle the advices asynchronously.
- `LF_UNSET`: Unset/clear a previous advice (Currently only supports LU_ADVISE_LOCKNOEXPAND).

Each of the *ladvise* elements is an *llapi_lu_ladvise* structure, which contains the following fields:

| **Field**                                        | **Description**                                              |
| ------------------------------------------------ | ------------------------------------------------------------ |
| `lla_ladvice`                                    | Specifies the advice for the given file range, currently one of:`LU_LADVISE_WILLREAD`: Prefetch data into server cache using optimum I/O size for the server.`LU_LADVISE_DONTNEED`: Clean cached data for the specified file range(s) on the server. |
| `lla_start`                                      | The offset in bytes for the start of this advice.            |
| `lla_end`                                        | The offset in bytes (non-inclusive) for the end of this advice. |
| `lla_value1``lla_value2``lla_value3``lla_value4` | Additional arguments for future advice types and should be set to zero if not explicitly required for a given advice type. Advice-specific names for these fields follow. |
| `lla_lockahead_mode`                             | When using LU_ADVISE_LOCKAHEAD, the 'lla_value1' field is used to communicate the requested lock mode, and can be referred to as lla_lockahead_mode. |
| `lla_peradvice_flags`                            | When using advices which support them, the 'lla_value2' field is used to communicate per-advice flags and can be referred to as 'lla_peradvice_flags'. Both LF_ASYNC and LF_UNSET are supported as peradvice flags. |
| `lla_lockahead_result`                           | When using LU_ADVISE_LOCKAHEAD, the 'lla_value3' field is used to communicate the result of the request, and can be referred to as lla_lockahead_result. |

`llapi_ladvise()` forwards the advice to Lustre servers without guaranteeing how and when servers will react to the advice. Actions may or may not be triggered when the advices are received, depending on the type of the advice as well as the real-time decision of the affected server-side components.

A typical usage of `llapi_ladvise()` is to enable applications and users (via `lfs ladvise`) with external knowledge about application I/O patterns to intervene in server-side I/O handling. For example, if a group of different clients are doing small random reads of a file, prefetching pages into OSS cache with big linear reads before the random IO is an overall net benefit. Fetching that data into each client cache with *fadvise()* may not be beneficial, due to much more data being sent to the clients.

LU_LADVISE_LOCKAHEAD merits a special comment. While it is possible and encouraged to use it directly in your application to avoid lock contention (primarily for writing to a single file from multiple clients), it will also be available in the MPI-I/O / MPICH library from ANL for use with the i/o aggregation mode of that library. This is intended (eventually) to be the primary way this feature is used.

At the time of writing, this support is proposed as a patch but is not yet merged in to the public ANL code base. Users are encouraged to check their MPICH documentation and/or check with their library provider about support.

While conceptually similar to the *posix_fadvise* and Linux *fadvise* system calls, the main difference of`llapi_ladvise()` is that *fadvise() / posix_fadvise()* are client side mechanisms that do not pass advice to the filesystem, while `llapi_ladvise()` sends advice or hints to one or more Lustre servers on which the file is stored. In some cases it may be desirable to use both interfaces.

### Return Values

`llapi_ladvise` returns:

`0` On success

`-1` if an error occurred (in which case, errno is set appropriately).

### Errors

| **Error**  | **Description**                                            |
| ---------- | ---------------------------------------------------------- |
| `ENOMEM`   | Insufficient memory to complete operation.                 |
| `EINVAL`   | One or more invalid arguments are given.                   |
| `EFAULT`   | Memory region pointed by `ladvise` is not properly mapped. |
| `ENOTSUPP` | Advice type is not supported.                              |

## Example Using the `llapi` Library

Use `llapi_file_create` to set Lustre software properties for a new file. For a synopsis and description of `llapi_file_create` and examples of how to use it, see [*Configuration Files and Module Parameters*](06.06-Configuration%20Files%20and%20Module%20Parameters.md).

You can set striping from inside programs like `ioctl`. To compile the sample program, you need to install the Lustre client source RPM.

**A simple C program to demonstrate striping API - libtest.c**

```
/* -*- mode: c; c-basic-offset: 8; indent-tabs-mode: nil; -*-
 * vim:expandtab:shiftwidth=8:tabstop=8:
 *
 * lustredemo - a simple example of lustreapi functions
 */
#include <stdio.h>
#include <fcntl.h>
#include <dirent.h>
#include <errno.h>
#include <stdlib.h>
#include <lustre/lustreapi.h>
#define MAX_OSTS 1024
#define LOV_EA_SIZE(lum, num) (sizeof(*lum) + num * sizeof(*lum->lmm_objects))
#define LOV_EA_MAX(lum) LOV_EA_SIZE(lum, MAX_OSTS)

/*
 * This program provides crude examples of using the lustreapi API functions
 */
/* Change these definitions to suit */

#define TESTDIR "/tmp"           /* Results directory */
#define TESTFILE "lustre_dummy"  /* Name for the file we create/destroy */
#define FILESIZE 262144                    /* Size of the file in words */
#define DUMWORD "DEADBEEF"       /* Dummy word used to fill files */
#define MY_STRIPE_WIDTH 2                  /* Set this to the number of OST required */
#define MY_LUSTRE_DIR "/mnt/lustre/ftest"

int close_file(int fd)
{
        if (close(fd) < 0) {
                fprintf(stderr, "File close failed: %d (%s)\n", errno, strerror(errno));
                return -1;
        }
        return 0;
}

int write_file(int fd)
{
        char *stng =  DUMWORD;
        int cnt = 0;

        for( cnt = 0; cnt < FILESIZE; cnt++) {
                write(fd, stng, sizeof(stng));
        }
        return 0;
}
/* Open a file, set a specific stripe count, size and starting OST
 *    Adjust the parameters to suit */
int open_stripe_file()
{
        char *tfile = TESTFILE;
        int stripe_size = 65536;    /* System default is 4M */
        int stripe_offset = -1;     /* Start at default */
        int stripe_count = MY_STRIPE_WIDTH;  /*Single stripe for this demo*/
        int stripe_pattern = 0;     /* only RAID 0 at this time */
        int rc, fd;

        rc = llapi_file_create(tfile,
                        stripe_size,stripe_offset,stripe_count,stripe_pattern);
        /* result code is inverted, we may return -EINVAL or an ioctl error.
         * We borrow an error message from sanity.c
         */
        if (rc) {
                fprintf(stderr,"llapi_file_create failed: %d (%s) \n", rc, strerror(-rc));
                return -1;
        }
        /* llapi_file_create closes the file descriptor, we must re-open */
        fd = open(tfile, O_CREAT | O_RDWR | O_LOV_DELAY_CREATE, 0644);
        if (fd < 0) {
                fprintf(stderr, "Can't open %s file: %d (%s)\n", tfile, errno, strerror(errno));
                return -1;
        }
        return fd;
}

/* output a list of uuids for this file */
int get_my_uuids(int fd)
{
        struct obd_uuid uuids[1024], *uuidp;        /* Output var */
        int obdcount = 1024;
        int rc,i;

        rc = llapi_lov_get_uuids(fd, uuids, &obdcount);
        if (rc != 0) {
                fprintf(stderr, "get uuids failed: %d (%s)\n",errno, strerror(errno));
        }
        printf("This file system has %d obds\n", obdcount);
        for (i = 0, uuidp = uuids; i < obdcount; i++, uuidp++) {
                printf("UUID %d is %s\n",i, uuidp->uuid);
        }
        return 0;
}

/* Print out some LOV attributes. List our objects */
int get_file_info(char *path)
{

        struct lov_user_md *lump;
        int rc;
        int i;

        lump = malloc(LOV_EA_MAX(lump));
        if (lump == NULL) {
                return -1;
        }

        rc = llapi_file_get_stripe(path, lump);

        if (rc != 0) {
                fprintf(stderr, "get_stripe failed: %d (%s)\n",errno, strerror(errno));
                return -1;
        }

        printf("Lov magic %u\n", lump->lmm_magic);
        printf("Lov pattern %u\n", lump->lmm_pattern);
        printf("Lov object id %llu\n", lump->lmm_object_id);
        printf("Lov stripe size %u\n", lump->lmm_stripe_size);
        printf("Lov stripe count %hu\n", lump->lmm_stripe_count);
        printf("Lov stripe offset %u\n", lump->lmm_stripe_offset);
        for (i = 0; i < lump->lmm_stripe_count; i++) {
                printf("Object index %d Objid %llu\n", lump->lmm_objects[i].l_ost_idx, lump->lmm_objects[i].l_object_id);
        }

        free(lump);
        return rc;

}

/* Ping all OSTs that belong to this filesystem */
int ping_osts()
{
        DIR *dir;
        struct dirent *d;
        char osc_dir[100];
        int rc;

        sprintf(osc_dir, "/proc/fs/lustre/osc");
        dir = opendir(osc_dir);
        if (dir == NULL) {
                printf("Can't open dir\n");
                return -1;
        }
        while((d = readdir(dir)) != NULL) {
                if ( d->d_type == DT_DIR ) {
                        if (! strncmp(d->d_name, "OSC", 3)) {
                                printf("Pinging OSC %s ", d->d_name);
                                rc = llapi_ping("osc", d->d_name);
                                if (rc) {
                                        printf("  bad\n");
                                } else {
                                        printf("  good\n");
                                }
                        }
                }
        }
        return 0;

}

int main()
{
        int file;
        int rc;
        char filename[100];
        char sys_cmd[100];

        sprintf(filename, "%s/%s",MY_LUSTRE_DIR, TESTFILE);

        printf("Open a file with striping\n");
        file = open_stripe_file();
        if ( file < 0 ) {
                printf("Exiting\n");
                exit(1);
        }
        printf("Getting uuid list\n");
        rc = get_my_uuids(file);
        printf("Write to the file\n");
        rc = write_file(file);
        rc = close_file(file);
        printf("Listing LOV data\n");
        rc = get_file_info(filename);
        printf("Ping our OSTs\n");
        rc = ping_osts();

        /* the results should match lfs getstripe */
        printf("Confirming our results with lfs getstripe\n");
        sprintf(sys_cmd, "/usr/bin/lfs getstripe %s/%s", MY_LUSTRE_DIR, TESTFILE);
        system(sys_cmd);

        printf("All done\n");
        exit(rc);
}
```

**Makefile for sample application:**

```
gcc -g -O2 -Wall -o lustredemo libtest.c -llustreapi
clean:
rm -f core lustredemo *.o
run: 
make
rm -f /mnt/lustre/ftest/lustredemo
rm -f /mnt/lustre/ftest/lustre_dummy
cp lustredemo /mnt/lustre/ftest/
```
### See Also

- [the section called “ `llapi_file_create` ”](#llapi_file_create)
- [the section called “`llapi_file_get_stripe`”](#llapi_file_get_stripe)
- [the section called “ `llapi_file_open` ”](#llapi_file_open)
- [the section called “ `llapi_quotactl` ”](#llapi_quotactl)


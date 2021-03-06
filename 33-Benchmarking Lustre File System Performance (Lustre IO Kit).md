# Benchmarking Lustre File System Performance (Lustre I/O Kit)

- [Benchmarking Lustre File System Performance (Lustre I/O Kit)](#benchmarking-lustre-file-system-performance-lustre-io-kit)
  * [Using Lustre I/O Kit Tools](#using-lustre-io-kit-tools)
    + [Contents of the Lustre I/O Kit](#contents-of-the-lustre-io-kit)
    + [Preparing to Use the Lustre I/O Kit](#preparing-to-use-the-lustre-io-kit)
  * [Testing I/O Performance of Raw Hardware (`sgpdd-survey`)](#testing-io-performance-of-raw-hardware-sgpdd-survey)
    + [Tuning Linux Storage Devices](#tuning-linux-storage-devices)
    + [Running sgpdd-survey](#running-sgpdd-survey)
  * [Testing OST Performance (`obdfilter-survey`)](#testing-ost-performance-obdfilter-survey)
    + [Testing Local Disk Performance](#testing-local-disk-performance)
    + [Testing Network Performance](#testing-network-performance)
    + [Testing Remote Disk Performance](#testing-remote-disk-performance)
    + [Output Files](#output-files)
      - [Script Output](#script-output)
      - [Visualizing Results](#visualizing-results)
  * [Testing OST I/O Performance (`ost-survey`)](#testing-ost-io-performance-ost-survey)
  * [Testing MDS Performance (`mds-survey`)](#testing-mds-performance-mds-survey)
    + [Output Files](#output-files-1)
    + [Script Output](#script-output-1)
  * [Collecting Application Profiling Information (`stats-collect`)](#collecting-application-profiling-information-stats-collect)
    + [Using `stats-collect`](#using-stats-collect)

This chapter describes the Lustre I/O kit, a collection of I/O benchmarking tools for a Lustre cluster. It includes:

- [the section called “ Using Lustre I/O Kit Tools”](#using-lustre-io-kit-tools)
- [the section called “Testing I/O Performance of Raw Hardware (sgpdd-survey)”](#testing-io-performance-of-raw-hardware-sgpdd-survey)
- [the section called “Testing OST Performance (obdfilter-survey)”](#testing-ost-performance-obdfilter-survey)
- [the section called “Testing OST I/O Performance (ost-survey)”](#testing-ost-io-performance-ost-survey)
- [the section called “Testing MDS Performance (mds-survey)”](#testing-mds-performance-mds-survey)
- [the section called “Collecting Application Profiling Information (stats-collect)”](#collecting-application-profiling-information-stats-collect)

## Using Lustre I/O Kit Tools

The tools in the Lustre I/O Kit are used to benchmark Lustre file system hardware and validate that it is working as expected before you install the Lustre software. It can also be used to to validate the performance of the various hardware and software layers in the cluster and also to find and troubleshoot I/O issues.

Typically, performance is measured starting with single raw devices and then proceeding to groups of devices. Once raw performance has been established, other software layers are then added incrementally and tested.

### Contents of the Lustre I/O Kit

The I/O kit contains three tests, each of which tests a progressively higher layer in the Lustre software stack:

- `sgpdd-survey` - Measure basic 'bare metal' performance of devices while bypassing the kernel block device layers, buffer cache, and file system.
- `obdfilter-survey` - Measure the performance of one or more OSTs directly on the OSS node or alternately over the network from a Lustre client.
- `ost-survey` - Performs I/O against OSTs individually to allow performance comparisons to detect if an OST is performing sub-optimally due to hardware issues.

Typically with these tests, a Lustre file system should deliver 85-90% of the raw device performance.

A utility `stats-collect` is also provided to collect application profiling information from Lustre clients and servers. See [*the section called “Collecting Application Profiling Information (`stats-collect`)”*](#collecting-application-profiling-information-stats-collect) for more information.

### Preparing to Use the Lustre I/O Kit

The following prerequisites must be met to use the tests in the Lustre I/O kit:

- Password-free remote access to nodes in the system (provided by `ssh` or `rsh`).
- LNet self-test completed to test that Lustre networking has been properly installed and configured. See [*Testing Lustre Network Performance (LNet Self-Test)*](04.01-Testing%20Lustre%20Network%20Performance%20(LNet%20Self-Test).md).
- Lustre file system software installed.
- `sg3_utils` package providing the `sgp_dd` tool (`sg3_utils` is a separate RPM package available online using YUM).

Download the Lustre I/O kit (`lustre-iokit`)from:

<http://downloads.whamcloud.com/>

## Testing I/O Performance of Raw Hardware (`sgpdd-survey`)

The `sgpdd-survey` tool is used to test bare metal I/O performance of the raw hardware, while bypassing as much of the kernel as possible. This survey may be used to characterize the performance of a SCSI device by simulating an OST serving multiple stripe files. The data gathered by this survey can help set expectations for the performance of a Lustre OST using this device.

The script uses `sgp_dd` to carry out raw sequential disk I/O. It runs with variable numbers of sgp_dd threads to show how performance varies with different request queue depths.

The script spawns variable numbers of `sgp_dd` instances, each reading or writing a separate area of the disk to demonstrate performance variance within a number of concurrent stripe files.

Several tips and insights for disk performance measurement are described below. Some of this information is specific to RAID arrays and/or the Linux RAID implementation.

- *Performance is limited by the slowest disk.*

  Before creating a RAID array, benchmark all disks individually. We have frequently encountered situations where drive performance was not consistent for all devices in the array. Replace any disks that are significantly slower than the rest.

- *Disks and arrays are very sensitive to request size.*

  To identify the optimal request size for a given disk, benchmark the disk with different record sizes ranging from 4 KB to 1 to 2 MB.

**Caution**

The `sgpdd-survey` script overwrites the device being tested, which results in the **\*LOSS OF ALL DATA*** on that device. Exercise caution when selecting the device to be tested.

**Note**

Array performance with all LUNs loaded does not always match the performance of a single LUN when tested in isolation.

**Prerequisites:**

- `sgp_dd` tool in the `sg3_utils` package
- Lustre software is *NOT* required

The device(s) being tested must meet one of these two requirements:

- If the device is a SCSI device, it must appear in the output of `sg_map` (make sure the kernel module `sg` is loaded).
- If the device is a raw device, it must appear in the output of `raw -qa`.

Raw and SCSI devices cannot be mixed in the test specification.

**Note**

If you need to create raw devices to use the `sgpdd-survey` tool, note that raw device 0 cannot be used due to a bug in certain versions of the "raw" utility (including the version shipped with Red Hat Enterprise Linux 4U4.)

### Tuning Linux Storage Devices

To get large I/O transfers (1 MB) to disk, it may be necessary to tune several kernel parameters as specified:

```
/sys/block/sdN/queue/max_sectors_kb = 4096
/sys/block/sdN/queue/max_phys_segments = 256
/proc/scsi/sg/allow_dio = 1
/sys/module/ib_srp/parameters/srp_sg_tablesize = 255
/sys/block/sdN/queue/scheduler
```

**Note**

Recommended schedulers are **deadline** and **noop**. The scheduler is set by default to **deadline**, unless it has already been set to **noop**.

### Running sgpdd-survey

The `sgpdd-survey` script must be customized for the particular device being tested and for the location where the script saves its working and result files (by specifying the `${rslt}` variable). Customization variables are described at the beginning of the script.

When the `sgpdd-survey` script runs, it creates a number of working files and a pair of result files. The names of all the files created start with the prefix defined in the variable `${rslt}`. (The default value is `/tmp`.) The files include:

- File containing standard output data (same as `stdout`)

  ```
  rslt_date_time.summary
  ```

- Temporary (tmp) files

  ```
  rslt_date_time_*
  ```

- Collected tmp files for post-mortem

  ```
  rslt_date_time.detail
  ```

The `stdout` and the `.summary` file will contain lines like this:

```
total_size 8388608K rsz 1024 thr 1 crg 1 180.45 MB/s 1 x 180.50 \
        = 180.50 MB/s
```

Each line corresponds to a run of the test. Each test run will have a different number of threads, record size, or number of regions.

- `total_size` - Size of file being tested in KBs (8 GB in above example).
- `rsz` - Record size in KBs (1 MB in above example).
- `thr` - Number of threads generating I/O (1 thread in above example).
- `crg` - Current regions, the number of disjoint areas on the disk to which I/O is being sent (1 region in above example, indicating that no seeking is done).
- `MB/s` - Aggregate bandwidth measured by dividing the total amount of data by the elapsed time (180.45 MB/s in the above example).
- `MB/s` - The remaining numbers show the number of regions X performance of the slowest disk as a sanity check on the aggregate bandwidth.

If there are so many threads that the `sgp_dd` script is unlikely to be able to allocate I/O buffers, then `ENOMEM` is printed in place of the aggregate bandwidth result.

If one or more `sgp_dd` instances do not successfully report a bandwidth number, then `FAILED` is printed in place of the aggregate bandwidth result.

## Testing OST Performance (`obdfilter-survey`)

The `obdfilter-survey` script generates sequential I/O from varying numbers of threads and objects (files) to simulate the I/O patterns of a Lustre client.

The `obdfilter-survey` script can be run directly on the OSS node to measure the OST storage performance without any intervening network, or it can be run remotely on a Lustre client to measure the OST performance including network overhead.

The `obdfilter-survey` is used to characterize the performance of the following:

- **Local file system** - In this mode, the `obdfilter-survey` script exercises one or more instances of the obdfilter directly. The script may run on one or more OSS nodes, for example, when the OSSs are all attached to the same multi-ported disk subsystem.

  Run the script using the `case=disk` parameter to run the test against all the local OSTs. The script automatically detects all local OSTs and includes them in the survey.

  To run the test against only specific OSTs, run the script using the `targets=parameter` to list the OSTs to be tested explicitly. If some OSTs are on remote nodes, specify their hostnames in addition to the OST name (for example, `oss2:lustre-OST0004`).

  All `obdfilter` instances are driven directly. The script automatically loads the `obdecho` module (if required) and creates one instance of `echo_client` for each `obdfilter` instance in order to generate I/O requests directly to the OST.

  For more details, see [*the section called “Testing Local Disk Performance”*](#testing-local-disk-performance).

- **Network** - In this mode, the Lustre client generates I/O requests over the network but these requests are not sent to the OST file system. The OSS node runs the obdecho server to receive the requests but discards them before they are sent to the disk.

  Pass the parameters `case=network` and `targets=*hostname|IP_of_server*` to the script. For each network case, the script does the required setup.

  For more details, see [*the section called “Testing Network Performance”*](#testing-network-performance).

- **Remote file system over the network** - In this mode the `obdfilter-survey` script generates I/O from a Lustre client to a remote OSS to write the data to the file system.

  To run the test against all the local OSCs, pass the parameter `case=netdisk` to the script. Alternately you can pass the target= parameter with one or more OSC devices (e.g., `lustre-OST0000-osc-ffff88007754bc00`) against which the tests are to be run.

  For more details, see [*the section called “Testing Remote Disk Performance”*](#testing-remote-disk-performance).

**Caution**

The `obdfilter-survey` script is potentially destructive and there is a small risk data may be lost. To reduce this risk, `obdfilter-survey` should not be run on devices that contain data that needs to be preserved. Thus, the best time to run `obdfilter-survey` is before the Lustre file system is put into production. The reason `obdfilter-survey` may be safe to run on a production file system is because it creates objects with object sequence 2. Normal file system objects are typically created with object sequence 0.

**Note**

If the `obdfilter-survey` test is terminated before it completes, some small amount of space is leaked. you can either ignore it or reformat the file system.

**Note**

The `obdfilter-survey` script is *NOT* scalable beyond tens of OSTs since it is only intended to measure the I/O performance of individual storage subsystems, not the scalability of the entire system.

**Note**

The `obdfilter-survey` script must be customized, depending on the components under test and where the script's working files should be kept. Customization variables are described at the beginning of the `obdfilter-survey` script. In particular, pay attention to the listed maximum values listed for each parameter in the script.

### Testing Local Disk Performance

The `obdfilter-survey` script can be run automatically or manually against a local disk. This script profiles the overall throughput of storage hardware, including the file system and RAID layers managing the storage, by sending workloads to the OSTs that vary in thread count, object count, and I/O size.

When the `obdfilter-survey` script is run, it provides information about the performance abilities of the storage hardware and shows the saturation points.

The `plot-obdfilter` script generates from the output of the `obdfilter-survey` a CSV file and parameters for importing into a spreadsheet or gnuplot to visualize the data.

To run the `obdfilter-survey` script, create a standard Lustre file system configuration; no special setup is needed.

**To perform an automatic run:**

1. Start the Lustre OSTs.

   The Lustre OSTs should be mounted on the OSS node(s) to be tested. The Lustre client is not required to be mounted at this time.

2. Verify that the obdecho module is loaded. Run:

   ```
   modprobe obdecho
   ```

3. Run the `obdfilter-survey` script with the parameter `case=disk`.

   For example, to run a local test with up to two objects (nobjhi), up to two threads (thrhi), and 1024 MB transfer size (size):

   ```
   $ nobjhi=2 thrhi=2 size=1024 case=disk sh obdfilter-survey
   ```

4. Performance measurements for write, rewrite, read etc are provided below:

   ```
   # example output
   Fri Sep 25 11:14:03 EDT 2015 Obdfilter-survey for case=disk from hds1fnb6123
   ost 10 sz 167772160K rsz 1024K obj   10 thr   10 write 10982.73 [ 601.97,2912.91] rewrite 15696.54 [1160.92,3450.85] read 12358.60 [ 938.96,2634.87] 
   ...
   ```

   The file `./lustre-iokit/obdfilter-survey/README.obdfilter-survey` provides an explaination for the output as follows:

   ```
   ost 10          is the total number of OSTs under test.
   sz 167772160K   is the total amount of data read or written (in bytes).
   rsz 1024K       is the record size (size of each echo_client I/O, in bytes).
   obj    10       is the total number of objects over all OSTs
   thr    10       is the total number of threads over all OSTs and objects
   write           is the test name.  If more tests have been specified they
              all appear on the same line.
   10982.73        is the aggregate bandwidth over all OSTs measured by
              dividing the total number of MB by the elapsed time.
   [601.97,2912.91] are the minimum and maximum instantaneous bandwidths seen on
              any individual OST.
   Note that although the numbers of threads and objects are specifed per-OST
   in the customization section of the script, results are reported aggregated
   over all OSTs.
   ```

To perform a manual run:

1. Start the Lustre OSTs.

   The Lustre OSTs should be mounted on the OSS node(s) to be tested. The Lustre client is not required to be mounted at this time.

2. Verify that the `obdecho` module is loaded. Run:

   ```
   modprobe obdecho
   ```

3. Determine the OST names.

   On the OSS nodes to be tested, run the `lctl dl` command. The OST device names are listed in the fourth column of the output. For example:

   ```
   $ lctl dl |grep obdfilter
   0 UP obdfilter lustre-OST0001 lustre-OST0001_UUID 1159
   2 UP obdfilter lustre-OST0002 lustre-OST0002_UUID 1159
   ...
   ```

4. List all OSTs you want to test.

   Use the `targets=parameter` to list the OSTs separated by spaces. List the individual OSTs by name using the format `*fsname*-*OSTnumber*` (for example, `lustre-OST0001`). You do not have to specify an MDS or LOV.

5. Run the `obdfilter-survey` script with the `targets=parameter`.

   For example, to run a local test with up to two objects (`nobjhi`), up to two threads (`thrhi`), and 1024 Mb (size) transfer size:

   ```
   $ nobjhi=2 thrhi=2 size=1024 targets="lustre-OST0001 \
   	   lustre-OST0002" sh obdfilter-survey
   ```

### Testing Network Performance

The `obdfilter-survey` script can only be run automatically against a network; no manual test is provided.

To run the network test, a specific Lustre file system setup is needed. Make sure that these configuration requirements have been met.

**To perform an automatic run:**

1. Start the Lustre OSTs.

   The Lustre OSTs should be mounted on the OSS node(s) to be tested. The Lustre client is not required to be mounted at this time.

2. Verify that the `obdecho` module is loaded. Run:

   ```
   modprobe obdecho
   ```

3. Start `lctl` and check the device list, which must be empty. Run:

   ```
   lctl dl
   ```

4. Run the `obdfilter-survey` script with the parameters `case=network` and `targets=*hostname|ip_of_server*`. For example:

   ```
   $ nobjhi=2 thrhi=2 size=1024 targets="oss0 oss1" \
   	   case=network sh obdfilter-survey
   ```

5. On the server side, view the statistics at:

   ```
   lctl get_param obdecho.echo_srv.stats
   ```

   where `*echo_srv*` is the `obdecho` server created by the script.

### Testing Remote Disk Performance

The `obdfilter-survey` script can be run automatically or manually against a network disk. To run the network disk test, start with a standard Lustre configuration. No special setup is needed.

**To perform an automatic run:**

1. Start the Lustre OSTs.

   The Lustre OSTs should be mounted on the OSS node(s) to be tested. The Lustre client is not required to be mounted at this time.

2. Verify that the `obdecho` module is loaded. Run:

   ```
   modprobe obdecho
   ```

3. Run the `obdfilter-survey` script with the parameter `case=netdisk`. For example:

   ```
   $ nobjhi=2 thrhi=2 size=1024 case=netdisk sh obdfilter-survey
   ```

**To perform a manual run:**

1. Start the Lustre OSTs.

   The Lustre OSTs should be mounted on the OSS node(s) to be tested. The Lustre client is not required to be mounted at this time.

2. Verify that the `obdecho` module is loaded. Run:

   modprobe obdecho

3. Determine the OSC names.

   On the OSS nodes to be tested, run the `lctl dl` command. The OSC device names are listed in the fourth column of the output. For example:

   ```
   $ lctl dl |grep obdfilter
   3 UP osc lustre-OST0000-osc-ffff88007754bc00 \
              54b91eab-0ea9-1516-b571-5e6df349592e 5
   4 UP osc lustre-OST0001-osc-ffff88007754bc00 \
              54b91eab-0ea9-1516-b571-5e6df349592e 5
   ...
   ```

4. List all OSCs you want to test.

   Use the `targets=parameter` to list the OSCs separated by spaces. List the individual OSCs by name separated by spaces using the format `*fsname*-*OST_name*-osc-*instance*` (for example, `lustre-OST0000-osc-ffff88007754bc00`). You *do not have to specify an MDS or LOV.*

5. Run the `obdfilter-survey` script with the `targets=*osc*` and `case=netdisk`.

   An example of a local test run with up to two objects (`nobjhi`), up to two threads (`thrhi`), and 1024 Mb (size) transfer size is shown below:

   ```
   $ nobjhi=2 thrhi=2 size=1024 \
              targets="lustre-OST0000-osc-ffff88007754bc00 \
              lustre-OST0001-osc-ffff88007754bc00" sh obdfilter-survey
   ```

### Output Files

When the `obdfilter-survey` script runs, it creates a number of working files and a pair of result files. All files start with the prefix defined in the variable `${rslt}`.

| **File**              | **Description**                        |
| --------------------- | -------------------------------------- |
| `${rslt}.summary`     | Same as stdout                         |
| `${rslt}.script_*`    | Per-host test script files             |
| `${rslt}.detail_tmp*` | Per-OST result files                   |
| `${rslt}.detail`      | Collected result files for post-mortem |

The `obdfilter-survey` script iterates over the given number of threads and objects performing the specified tests and checks that all test processes have completed successfully.

**Note**

The `obdfilter-survey` script may not clean up properly if it is aborted or if it encounters an unrecoverable error. In this case, a manual cleanup may be required, possibly including killing any running instances of `lctl` (local or remote), removing `echo_client` instances created by the script and unloading `obdecho`.

#### Script Output

The `.summary` file and `stdout` of the `obdfilter-survey` script contain lines like:

```
ost 8 sz 67108864K rsz 1024 obj 8 thr 8 write 613.54 [ 64.00, 82.00]
```

Where:

| **Parameter and value** | **Description**                                              |
| ----------------------- | ------------------------------------------------------------ |
| ost 8                   | Total number of OSTs being tested.                           |
| sz 67108864K            | Total amount of data read or written (in KB).                |
| rsz 1024                | Record size (size of each echo_client I/O, in KB).           |
| obj 8                   | Total number of objects over all OSTs.                       |
| thr 8                   | Total number of threads over all OSTs and objects.           |
| write                   | Test name. If more tests have been specified, they all appear on the same line. |
| 613.54                  | Aggregate bandwidth over all OSTs (measured by dividing the total number of MB by the elapsed time). |
| [64, 82.00]             | Minimum and maximum instantaneous bandwidths on an individual OST. |

**Note**

Although the numbers of threads and objects are specified per-OST in the customization section of the script, the reported results are aggregated over all OSTs. 

#### Visualizing Results

It is useful to import the `obdfilter-survey` script summary data (it is fixed width) into Excel (or any graphing package) and graph the bandwidth versus the number of threads for varying numbers of concurrent regions. This shows how the OSS performs for a given number of concurrently-accessed objects (files) with varying numbers of I/Os in flight.

It is also useful to monitor and record average disk I/O sizes during each test using the 'disk io size' histogram in the file `lctl get_param obdfilter.*.brw_stats` (see [*the section called “Monitoring the OST Block I/O Stream”*](06.02-Lustre%20Parameters.md#monitoring-the-ost-block-io-stream) for details). These numbers help identify problems in the system when full-sized I/Os are not submitted to the underlying disk. This may be caused by problems in the device driver or Linux block layer.

The `plot-obdfilter` script included in the I/O toolkit is an example of processing output files to a .csv format and plotting a graph using `gnuplot`.

## Testing OST I/O Performance (`ost-survey`)

The `ost-survey` tool is a shell script that uses `lfs setstripe` to perform I/O against a single OST. The script writes a file (currently using `dd`) to each OST in the Lustre file system, and compares read and write speeds. The `ost-survey` tool is used to detect anomalies between otherwise identical disk subsystems.

**Note**

We have frequently discovered wide performance variations across all LUNs in a cluster. This may be caused by faulty disks, RAID parity reconstruction during the test, or faulty network hardware.

To run the `ost-survey` script, supply a file size (in KB) and the Lustre file system mount point. For example, run:

```
$ ./ost-survey.sh -s 10 /mnt/lustre
```

Typical output is:

```
Number of Active OST devices : 4
Worst  Read OST indx: 2 speed: 2835.272725
Best   Read OST indx: 3 speed: 2872.889668
Read Average: 2852.508999 +/- 16.444792 MB/s
Worst  Write OST indx: 3 speed: 17.705545
Best   Write OST indx: 2 speed: 128.172576
Write Average: 95.437735 +/- 45.518117 MB/s
Ost#  Read(MB/s)  Write(MB/s)  Read-time  Write-time
----------------------------------------------------
0     2837.440       126.918        0.035      0.788
1     2864.433       108.954        0.035      0.918
2     2835.273       128.173        0.035      0.780
3     2872.890       17.706        0.035      5.648
```
## Testing MDS Performance (`mds-survey`)

The `mds-survey` script tests the local metadata performance using the `echo_client` to drive the MDD layers of the MDS stack. It can be used with the following classes of operations:

- `Open-create/mkdir/create`
- `Lookup/getattr/setxattr`
- `Delete/destroy`
- `Unlink/rmdir`

These operations will be run by a variable number of concurrent threads and will test with the number of directories specified by the user. The run can be executed such that all threads operate in a single directory (dir_count=1) or in private/unique directory (dir_count=x thrlo=x thrhi=x).

The mdd instance is driven directly. The script automatically loads the obdecho module if required and creates instance of echo_client.

This script can also create OST objects by providing stripe_count greater than zero.

**To perform a run:**

1. Start the Lustre MDT.

   The Lustre MDT should be mounted on the MDS node to be tested.

2. Start the Lustre OSTs (optional, only required when test with OST objects)

   The Lustre OSTs should be mounted on the OSS node(s).

3. Run the `mds-survey` script as explain below

   The script must be customized according to the components under test and where it should keep its working files. Customization variables are described as followed:

   - `thrlo` - threads to start testing. skipped if less than `dir_count`
   - `thrhi` - maximum number of threads to test
   - `targets` - MDT instance
   - `file_count` - number of files per thread to test
   - `dir_count` - total number of directories to test. Must be less than or equal to `thrhi`
   - `stripe_count `- number stripe on OST objects
   - `tests_str` - test operations. Must have at least "create" and "destroy"
   - `start_number` - base number for each thread to prevent name collisions
   - `layer` - MDS stack's layer to be tested

   Run without OST objects creation:

   Setup the Lustre MDS without OST mounted. Then invoke the `mds-survey` script

   ```
   $ thrhi=64 file_count=200000 sh mds-survey
   ```

   Run with OST objects creation:

   Setup the Lustre MDS with at least one OST mounted. Then invoke the `mds-survey` script with `stripe_count`parameter

   ```
   $ thrhi=64 file_count=200000 stripe_count=2 sh mds-survey
   ```

   Note: a specific MDT instance can be specified using targets variable.

   ```
   $ targets=lustre-MDT0000 thrhi=64 file_count=200000 stripe_count=2 sh mds-survey
   ```

### Output Files

When the `mds-survey` script runs, it creates a number of working files and a pair of result files. All files start with the prefix defined in the variable `${rslt}`.

| **File**              | **Description**                        |
| --------------------- | -------------------------------------- |
| `${rslt}.summary`     | Same as stdout                         |
| `${rslt}.script_*`    | Per-host test script files             |
| `${rslt}.detail_tmp*` | Per-mdt result files                   |
| `${rslt}.detail`      | Collected result files for post-mortem |

The `mds-survey` script iterates over the given number of threads performing the specified tests and checks that all test processes have completed successfully.

**Note**

The `mds-survey` script may not clean up properly if it is aborted or if it encounters an unrecoverable error. In this case, a manual cleanup may be required, possibly including killing any running instances of `lctl`, removing `echo_client` instances created by the script and unloading `obdecho`.

### Script Output

The `.summary` file and `stdout` of the `mds-survey` script contain lines like:

```
mdt 1 file 100000 dir 4 thr 4 create 5652.05 [ 999.01,46940.48] destroy 5797.79 [ 0.00,52951.55] 
```

Where:

| **Parameter and value** | **Description**                                              |
| ----------------------- | ------------------------------------------------------------ |
| mdt 1                   | Total number of MDT under test                               |
| file 100000             | Total number of files per thread to operate                  |
| dir 4                   | Total number of directories to operate                       |
| thr 4                   | Total number of threads operate over all directories         |
| create, destroy         | Tests name. More tests will be displayed on the same line.   |
| 565.05                  | Aggregate operations over MDT measured by dividing the total number of operations by the elapsed time. |
| [999.01,46940.48]       | Minimum and maximum instantaneous operation seen on any individual MDT |

**Note**

If script output has "ERROR", this usually means there is issue during the run such as running out of space on the MDT and/or OST. More detailed debug information is available in the ${rslt}.detail file

## Collecting Application Profiling Information (`stats-collect`)

The `stats-collect` utility contains the following scripts used to collect application profiling information from Lustre clients and servers:

- `lstat.sh` - Script for a single node that is run on each profile node.
- `gather_stats_everywhere.sh` - Script that collect statistics.
- `config.sh` - Script that contains customized configuration descriptions.

The `stats-collect` utility requires:

- Lustre software to be installed and set up on your cluster
- SSH and SCP access to these nodes without requiring a password

### Using `stats-collect`

The stats-collect utility is configured by including profiling configuration variables in the config.sh script. Each configuration variable takes the following form, where 0 indicates statistics are to be collected only when the script starts and stops and *n* indicates the interval in seconds at which statistics are to be collected:

```
statistic_INTERVAL=0|n
```

Statistics that can be collected include:

- `VMSTAT` - Memory and CPU usage and aggregate read/write operations
- `SERVICE` - Lustre OST and MDT RPC service statistics
- `BRW` - OST bulk read/write statistics (brw_stats)
- `SDIO` - SCSI disk IO statistics (sd_iostats)
- `MBALLOC` - `ldiskfs` block allocation statistics
- `IO` - Lustre target operations statistics
- `JBD` - ldiskfs journal statistics
- `CLIENT` - Lustre OSC request statistics

To collect profile information:

Begin collecting statistics on each node specified in the config.sh script.

1. Starting the collect profile daemon on each node by entering:

   ```
   sh gather_stats_everywhere.sh config.sh start 
   ```

2. Run the test.

3. Stop collecting statistics on each node, clean up the temporary file, and create a profiling tarball.

   Enter:

   ```
   sh gather_stats_everywhere.sh config.sh stop log_name.tgz
   ```

   When `*log_name*.tgz` is specified, a profile tarball `*/tmp/log_name*.tgz` is created.

4. Analyze the collected statistics and create a csv tarball for the specified profiling data.

   ```
   sh gather_stats_everywhere.sh config.sh analyse log_tarball.tgz csv
   ```
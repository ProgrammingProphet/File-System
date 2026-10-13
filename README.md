## Linux RHEL File System — Complete Teaching/Learning Guide

When you learn Linux, especially Red Hat Enterprise Linux (RHEL), the first concept students must understand is how Linux organizes files, directories, devices, configurations, applications, and system information.

Your understanding is correct: the Linux file system starts from the root directory, represented by `/`. Inside the root directory, there are multiple directories such as `/etc`, `/home`, `/root`, `/var`, `/usr`, `/boot`, and `/tmp`.

However, one important distinction is that Linux does not organize everything as ordinary files and folders. It also exposes devices, running process information, kernel information, and other system resources through special file-system interfaces.

Let's understand this from the fundamentals, using RHEL examples that you can explain to students in a classroom or practical lab.

### 1\. What is the Linux file system?

A file system is the structure Linux uses to organize and access data stored on disks and other storage devices.

Think of your college as a computer system:

* The entire college represents the root directory `/`.

* Departments represent directories.

* Files in each department represent documents and records.

* The system administrator's office represents the `/root` home directory.

* The notice board represents system logs and status information.

* The security office represents configuration and access controls.

Linux follows a hierarchical directory structure, meaning directories can contain files and other directories.

![Day 3: Understanding the Linux Filesystem | by Karthick Dk | devsecops-community | Medium](https://images.openai.com/static-rsc-4/LlKHLc9FPQmhIoANpPR56yxhtN4noyPD8R8zFZEw9VKbZ-mqK0hCYAbXZiKEBoHgnVgqfxE7nPafjlH2ralctku7-YQNV-snuoYfoWEwHl3wwUg4Dw0hSR1ZlHU8v1SmpnvVWjKiGBYctrOWgiM-oRZNMzRm0E2HkDHzeAJ83j2AvAIfX5jAfO-TUODWzI7B?purpose=fullsize)

### The most important concept: `/` versus `/root`

These two paths look similar but have completely different meanings.

| Path         | Meaning                                                                    |
| ------------ | -------------------------------------------------------------------------- |
| /            | The root of the entire Linux directory hierarchy                           |
| /root        | The home directory of the root administrator account                       |
| /home        | The parent directory where ordinary users' home directories usually reside |
| /home/aditya | An example of an ordinary user's home directory                            |

Teaching example: Imagine a college campus as `/`. The principal's office is `/root`. The principal's office is inside the campus, but it is not the campus itself.

In the same way, `/root` is a directory inside `/`, not the root of the entire file system.

### 2\. Complete overview of the RHEL root directory

On a typical RHEL installation, you will encounter the following directories. The exact contents depend on the RHEL version, installed software, system role, and whether the system uses a minimal installation or a graphical desktop.

RHEL root directory map

### /

Root of the entire hierarchy

### `/etc`

Configuration

### `/home`

User files

### `/root`

Administrator's home

### `/usr`

Programs and libraries

### `/var`

Logs and changing data

### `/boot`

Boot files and kernel

### `/dev`

Device interfaces

### `/proc` and `/sys`

Kernel and system information

### `/tmp`

Temporary files

### `/run`

Current runtime state

### `/opt`

Optional application software

### `/mnt`, `/media`

Mount points

Simplified conceptual map. Some directories may be symbolic links, empty, or absent depending on the RHEL installation.

This structure follows the Linux Filesystem Hierarchy Standard, which defines conventional locations for system files, user data, applications, and runtime information.

### 3\. Detailed explanation of every important directory

### 3.1 `/bin` — Essential user commands

Meaning: Traditionally, `bin` means binaries. A binary is a compiled executable program.

This directory historically held essential commands required for basic system operation, such as:

* `ls` — list directory contents

* `cp` — copy files

* `mv` — move or rename files

* `cat` — display file contents

* `mkdir` — create directories

* `rm` — remove files

On modern RHEL, `/bin` is typically a symbolic link to `/usr/bin`. Therefore, students might see the same commands when examining both locations.

```bash
ls -ld /bin
ls /bin
which ls
```

Why it matters: These commands are the basic tools used to navigate and manage Linux files.

### 3.2 `/sbin` — System administration commands

Traditionally, `sbin` means system binaries. It contains commands used primarily for system administration and essential system maintenance.

Examples commonly found on RHEL include:

* `fsck` — file-system checking tools

* `ip` — network configuration and inspection

* `reboot` — restart the system

* `shutdown` — shut down the system

Modern systems may place these programs in `/usr/sbin`, with `/sbin` linking to that directory.

```bash
ls -ld /sbin
ls /usr/sbin
command -v reboot
```

Important: The distinction between `/bin` and `/sbin` is historical, not an absolute security boundary. Some administration commands can be run by ordinary users, while others require elevated privileges.

### 3.3 `/boot` — Files needed to start Linux

This directory contains important files used during the boot process.

Common examples on RHEL:

| File or directory | Purpose                                                              |
| ----------------- | -------------------------------------------------------------------- |
| vmlinuz-*         | Compressed Linux kernel image                                        |
| initramfs-*.img   | Initial RAM file system used during early boot                       |
| grub2/            | GRUB bootloader configuration and supporting files, where applicable |
| loader/           | May contain bootloader-related data, depending on the installation   |

The kernel is the core of the operating system. It manages CPU scheduling, memory, hardware interactions, and processes.

The `initramfs` provides the early userspace environment and tools needed to locate and mount the actual root file system.

Example:

```bash
ls -lh /boot
uname -r
```

* `ls -lh /boot` displays boot files and their sizes.

* `uname -r` displays the kernel version currently running.

Real-world example: If a system has a bootloader problem or cannot locate the required boot files, the operating system may fail to start normally.

Do not delete files from `/boot` manually. Kernel updates and package-management tools should manage these files. RHEL's installation documentation explains the role of the boot file system.

### 3.4 `/dev` — Devices represented as files

One of the defining concepts in Unix-like systems is that many devices are accessed through file-like interfaces.

`/dev` contains device nodes and links representing devices or device interfaces.

Examples:

| Path         | Meaning                                          |
| ------------ | ------------------------------------------------ |
| /dev/null    | Discards data written to it                      |
| /dev/zero    | Produces zero bytes when read                    |
| /dev/tty     | Represents the controlling terminal of a process |
| /dev/sda     | May represent a disk device                      |
| /dev/nvme0n1 | May represent an NVMe storage device             |
| /dev/pts/    | Contains pseudo-terminal device nodes            |

Device names depend on the hardware, virtual machine, storage configuration, and device drivers. Not every system will have `/dev/sda`.

```bash
ls -l /dev/null
ls -l /dev/tty
lsblk
```

`lsblk` displays block devices such as disks, partitions, and logical volumes.

Practical example:

```bash
echo "Hello Linux" > /dev/null
```

The command succeeds without saving the text in a normal file. `/dev/null` discards the data.

Teaching point: `/dev` is not an ordinary directory of documents. Many entries are special device nodes managed by the system, commonly through `udev`.

### 3.5 `/etc` — System configuration files

This is one of the most important directories for a Linux administrator.

Meaning: `/etc` stores system-wide configuration files, including configuration for users, networking, authentication, services, and installed applications.

Important examples:

| File or directory    | What it controls                                                       |
| -------------------- | ---------------------------------------------------------------------- |
| /etc/passwd          | User account records                                                   |
| /etc/shadow          | Password hashes and password-aging information, with restricted access |
| /etc/group           | Group records                                                          |
| /etc/hostname        | Persistent system hostname on typical RHEL installations               |
| /etc/hosts           | Local hostname-to-address mappings                                     |
| /etc/resolv.conf     | DNS resolver configuration; may be managed automatically               |
| /etc/fstab           | File systems configured for mounting                                   |
| /etc/ssh/            | SSH server and client configuration                                    |
| /etc/sudoers         | Sudo privilege rules                                                   |
| /etc/sudoers.d/      | Additional sudo configuration files                                    |
| /etc/NetworkManager/ | NetworkManager configuration                                           |
| /etc/systemd/        | Systemd configuration and overrides                                    |
| /etc/yum.repos.d/    | DNF/YUM repository definitions                                         |
| /etc/selinux/        | SELinux configuration                                                  |
| /etc/cron.d/         | System-wide cron job definitions, where used                           |

Some files are generated or managed by services. For example, `/etc/resolv.conf` may be a symbolic link or automatically maintained file.

Explore these files safely:

```bash
ls /etc
cat /etc/hostname
cat /etc/hosts
cat /etc/fstab
ls /etc/yum.repos.d/
```

For protected files, use:

```bash
sudo less /etc/shadow
```

Do not modify `/etc/shadow`, `/etc/sudoers`, or network configuration files casually. Incorrect changes can lock users out or interrupt system access.

Real-world example: If an SSH server must listen on a different port, its configuration is typically adjusted in `/etc/ssh/sshd_config` or an appropriate configuration drop-in, then the service is validated and reloaded or restarted as necessary.

The key distinction is that `/etc` generally contains configuration, not the executable programs themselves.

### 3.6 `/home` — Ordinary users' personal directories

Each ordinary user generally has a home directory under `/home`.

Example:

```text
/home/
├── aditya/
│   ├── Documents/
│   ├── Downloads/
│   └── projects/
└── student1/
    ├── Documents/
    └── projects/
```

These are illustrative names; Linux does not automatically create every directory shown.

Commands:

```bash
ls -la /home
echo "$HOME"
pwd
```

If the logged-in user is `aditya`, their home directory might be `/home/aditya`.

The `~` character is shorthand for the current user's home directory:

```bash
cd ~
pwd
```

Why it matters: User data is separated from most operating-system files. Administrators can also place `/home` on a separate file system to simplify storage management and some system upgrades.

### 3.7 `/root` — The root administrator's home directory

The `root` account is the traditional Linux superuser, usually with user ID `0`.

Its home directory is typically `/root`.

It can contain:

* Administrator scripts

* Configuration files specific to the root account

* SSH configuration and keys

* Command history

* Maintenance files

Inspect it with:

```bash
sudo ls -la /root
```

An ordinary user typically cannot access this directory without appropriate privileges.

Remember the difference:

* `/` = entire Linux directory hierarchy.

* `/root` = home directory of the root account.

* `sudo` = a mechanism for running authorized commands with elevated privileges.

### 3.8 `/usr` — Programs, libraries, and shared resources

`/usr` is one of the largest and most important directory trees on a Linux installation. It contains much of the installed operating system's software.

The name is historical; do not interpret it as meaning that it contains only user-created files.

| Directory    | Meaning                                                                  |
| ------------ | ------------------------------------------------------------------------ |
| /usr/bin     | Most user commands and executable programs                               |
| /usr/sbin    | Many system-administration executables                                   |
| /usr/lib     | Libraries and supporting files for programs                              |
| /usr/lib64   | Common location for 64-bit libraries on RHEL x86-64                      |
| /usr/share   | Architecture-independent data, documentation, and other resources        |
| /usr/local   | Software installed locally by the administrator                          |
| /usr/include | Header files, typically relevant when development packages are installed |
| /usr/src     | Source-related files, where installed                                    |

For example, the `ls` command may be located at `/usr/bin/ls`.

```bash
command -v ls
ls /usr/bin | head
ls /usr/sbin | head
```

Real-world example: When DNF installs a package, its executable programs and supporting files may be placed under `/usr`, while configuration files commonly go under `/etc` and changing application data under `/var`.

Do not manually remove files from `/usr/bin` or `/usr/lib`. Use DNF or the appropriate package-management process.

### 3.9 `/var` — Variable data that changes while the system operates

The word variable is the key to understanding `/var`.

Unlike most software binaries under `/usr`, many files under `/var` are regularly created, updated, or deleted while the machine runs.

Important directories:

| Directory        | Purpose                                                          |
| ---------------- | ---------------------------------------------------------------- |
| /var/log         | System and application logs                                      |
| /var/cache       | Reusable cached data                                             |
| /var/lib         | Persistent application and service state                         |
| /var/tmp         | Temporary files intended to survive more reboots than /tmp files |
| /var/spool       | Queued work, such as mail or scheduled job data                  |
| /var/www         | Common web-content location, if configured                       |
| /var/log/audit   | Audit logs when the audit service is configured                  |
| /var/log/journal | Persistent systemd journal data, if configured                   |

Explore:

```bash
ls /var
sudo ls /var/log
sudo journalctl -n 20
```

Important distinction: `/var/log/messages` may contain general system messages on a typical RHEL installation, while `journalctl` reads the systemd journal. Log locations vary by service and configuration.

Real-world example: If a server's disk fills up because an application generates too many logs, `/var` may consume substantial disk space. This can affect services or even the operating system if critical space is exhausted.

That is why monitoring `/var/log`, log rotation, and disk utilization is important in Linux administration and DevOps.

### 3.10 `/lib`, `/lib64`, and `/usr/lib` — Libraries used by programs

A library is reusable code that applications can use instead of implementing every function themselves.

For example, when a program needs to print output, access files, or communicate with the operating system, it may depend on system libraries.

Common locations include:

* `/lib` — traditionally held essential shared libraries and other files needed for basic operation.

* `/lib64` — traditionally held 64-bit libraries on applicable architectures.

* `/usr/lib` and `/usr/lib64` — contain libraries and supporting files for installed software.

On modern RHEL, some of these top-level directories may be symbolic links into `/usr`.

Check them:

```bash
ls -ld /lib /lib64 /usr/lib64
ldd /usr/bin/ls
```

`ldd` shows the shared libraries required by a dynamically linked executable such as `/usr/bin/ls`.

Teaching example: Think of an application as a car and its libraries as reusable components. Several applications can use the same library rather than each carrying a separate implementation.

Do not delete library files manually. A missing or incompatible system library can prevent programs from running.

### 3.11 `/tmp` — Temporary files

`/tmp` is intended for temporary data created by applications and users.

Examples include:

* Temporary processing files

* Short-lived application data

* Temporary files created by scripts

* Intermediate files during an operation

Practical example:

```bash
cd /tmp
touch student-demo.txt
ls -l student-demo.txt
```

This creates a temporary demonstration file.

A useful command is:

```bash
df -h /tmp
```

It displays the file system that provides storage for `/tmp`.

Important: Files under `/tmp` may be deleted automatically during reboot, cleanup, or according to system policy. Do not treat `/tmp` as permanent storage.

On many RHEL installations, `/tmp` is a directory on the root file system rather than a separate partition. Its implementation can vary.

### 3.12 `/run` — Runtime information

`/run` stores volatile runtime information associated with the currently running system.

Examples include:

* PID files

* Runtime sockets

* Service state

* Other information created during the current boot

A PID is a process identifier, the numeric ID assigned to a running process.

For example:

```bash
ls /run
ls /run/systemd
```

Some services may create files or directories under `/run`.

Difference between `/run` and `/var`:

| /run                                  | /var                                                                 |
| ------------------------------------- | -------------------------------------------------------------------- |
| Runtime information                   | Variable data, including persistent state                            |
| Usually cleared during boot           | Many files survive reboot                                            |
| Used by active services and processes | Used for logs, databases, caches, queues, and other application data |

Do not confuse runtime state with permanent application data.

### 3.13 `/proc` — Process and kernel information

`/proc` is a virtual file system. Its entries provide information about running processes and the kernel; they are not ordinary files stored on disk in the usual way.

Common entries:

| Path          | Meaning                                          |
| ------------- | ------------------------------------------------ |
| /proc/cpuinfo | CPU information exposed by the kernel            |
| /proc/meminfo | Memory statistics                                |
| /proc/uptime  | System uptime and idle-time information          |
| /proc/mounts  | Information about mounted file systems           |
| /proc/version | Kernel version and build information             |
| /proc/PID/    | Information about a process with that process ID |

For example:

```bash
cat /proc/cpuinfo
cat /proc/meminfo
cat /proc/uptime
ls /proc
```

You can also use the more readable commands:

```bash
free -h
uptime
```

Real-world example: When a Linux administrator investigates memory usage or a running process, information from `/proc` helps the administrator understand the system's current state.

Teaching point: `/proc` is an interface to kernel-maintained information, not a directory full of ordinary disk files.

### 3.14 `/sys` — Hardware, devices, and kernel interfaces

`/sys` is another virtual file system, commonly called `sysfs`.

It exposes structured information about devices, drivers, kernel subsystems, and related system features.

Examples include:

* `/sys/class/net/` — network interfaces

* `/sys/class/block/` — block devices

* `/sys/devices/` — the device hierarchy

* `/sys/class/` — various device classes

Try:

```bash
ls /sys/class/net
ls /sys/class/block
```

The first command may display interfaces such as `ens160`, `enp0s3`, or `eth0`, depending on the machine.

Difference between `/proc` and `/sys`:

* `/proc` primarily exposes process and kernel information.

* `/sys` primarily exposes structured device, driver, and kernel-object information.

Some sysfs entries allow changes to kernel settings or device attributes. Do not write to them unless you understand the effect.

### 3.15 `/opt` — Optional application software

`/opt` is a conventional location for optional or add-on application software.

It is useful when an organization installs a standalone application that is not managed as part of the normal operating-system directory layout.

Example:

```text
/opt/
└── company-app/
    ├── bin/
    ├── config/
    └── lib/
```

This is an illustrative application structure, not one created automatically by RHEL.

For example, an organization might install a vendor-provided application under `/opt/company-app`.

Check:

```bash
ls -la /opt
```

Important: `/opt` is not the only valid location for applications. The appropriate location depends on the vendor's instructions and the installation method.

### 3.16 `/mnt` — Temporary mount point

A mount point is a directory through which a file system becomes accessible.

`/mnt` is conventionally used for temporary or administrative mounts.

Suppose you attach an additional disk to a RHEL server and mount its file system at `/mnt/data`.

The files on that disk then become accessible through:

```text
/mnt/data/
```

You can inspect mounts using:

```bash
findmnt
findmnt /mnt
df -hT
```

Key concept: A disk is not accessible through its file-system contents merely because it exists as a device. Its file system must be mounted at an appropriate mount point.

### 3.17 `/media` — Removable media

`/media` is conventionally used as a mount location for removable media, such as USB storage and optical discs.

On systems with a graphical desktop, removable devices may be mounted automatically in subdirectories.

On headless RHEL servers and cloud VMs, `/media` may be empty or unused.

```bash
ls -la /media
```

Difference:

* `/mnt` is commonly used for temporary or administrator-controlled mounts.

* `/media` is conventionally associated with removable media.

These are conventions; Linux administrators can mount file systems elsewhere when appropriate.

### 3.18 `/srv` — Data served by services

`/srv` is intended for data provided by services running on the machine.

Examples might include:

* Files served through an FTP service

* Website data

* Data provided by other network services

For example:

```text
/srv/
└── website/
    └── index.html
```

This is a possible layout, not a mandatory one.

For web servers, document roots are often configured elsewhere. For example, Apache on RHEL commonly uses `/var/www/html` as a default document root.

Teaching point: `/srv` is a conventional location for service data, but the service's configuration determines where it actually reads its content.

### 3.19 `/lost+found` — Recovered file-system objects

You may see `/lost+found` on certain Linux file systems, particularly ext-family file systems.

It can be used by file-system checking and recovery tools to place recovered objects that cannot be reconnected to their original directory entries.

It is not guaranteed to exist on every RHEL installation. Its presence depends on the file system and where the directory is located.

Important: RHEL commonly uses XFS by default, and XFS does not use the same `/lost+found` recovery-directory convention as ext-family file systems.

### 4\. Understand the difference between configuration, executable programs, and data

This is the most useful mental model for your students. When troubleshooting a Linux server, first identify what kind of file you are looking for.

Configuration — `/etc`

Defines how a service or application should behave.

Example: `/etc/ssh/sshd_config`

Executable programs — `/usr/bin`, `/usr/sbin`

Contains commands and programs that perform work.

Example: `/usr/bin/ls`

Logs — `/var/log`

Records events and diagnostic information.

Example: `/var/log/messages`, if configured

Persistent application state — `/var/lib`

Stores service-managed data needed across restarts.

Example: application databases or service state

User files — `/home`

Contains users' personal files, projects, and scripts.

Example: `/home/student1/projects`

A useful example is an Apache web server:

* The Apache executable may be under `/usr/sbin/`.

* Its main configuration is commonly `/etc/httpd/conf/httpd.conf`.

* Additional configuration can be under `/etc/httpd/conf.d/`.

* Its default document root is commonly `/var/www/html/`.

* Its logs are commonly under `/var/log/httpd/`.

* Its service is managed using `systemctl`.

This example demonstrates why Linux administrators need to understand the directory hierarchy rather than memorizing individual commands.

### 5\. How to navigate the file system in RHEL

Now let's convert the theory into commands students can run in their RHEL 9 or RHEL 10 virtual machine.

### Command 1: `pwd` — Where am I?

```bash
pwd
```

Example output:

```text
/home/student1
```

It displays the current working directory.

### Command 2: `ls` — What is inside this directory?

```bash
ls
ls -l
ls -la
```

* `ls` — lists visible entries.

* `ls -l` — displays details such as permissions, ownership, size, and modification time.

* `ls -la` — also displays hidden entries, including names beginning with `.`.

### Command 3: `cd` — Change directory

```bash
cd /
pwd
```

Output:

```text
/
```

You are now at the root of the directory hierarchy.

Next, explore the configuration directory:

```bash
cd /etc
pwd
ls
```

Then return to the root:

```bash
cd /
```

### Command 4: `tree` — Display directories hierarchically

If the `tree` command is installed, you can run:

```bash
tree -L 2 /
```

This shows a directory tree up to two levels deep. However, `/proc`, `/sys`, and other virtual or permission-restricted directories may produce extensive output or warnings.

On RHEL, you can install `tree` if it is available in your configured repositories:

```bash
sudo dnf install tree
```

You do not need `tree` to learn the file system; `ls` and `find` are sufficient.

### Command 5: `find` — Locate files and directories

```bash
find /etc -name "hostname"
```

This searches under `/etc` for entries named `hostname`.

Another example:

```bash
find /var/log -type f
```

This lists regular files under `/var/log`.

For a beginner, explain the command's components:

* `find` — search utility.

* `/etc` — starting location.

* `-name "hostname"` — filename pattern.

* `-type f` — restrict results to regular files.

### 6\. Linux paths: absolute versus relative

Students often become confused by the difference between a directory's name and its path.

### Absolute path

A complete path starting at `/`.

```bash
cd /etc/ssh
```

This works regardless of the current directory, assuming the path exists and access is permitted.

### Relative path

A path interpreted from the current working directory.

```bash
cd etc/ssh
```

This works only if the current directory contains the relative path `etc/ssh`.

### Special path symbols

| /   | Root directory                |
| --- | ----------------------------- |
| .   | Current directory             |
| ..  | Parent directory              |
| ~   | Current user's home directory |

### 7\. What happens when you create a file in Linux?

Suppose a student executes:

```bash
cd /tmp
touch demo.txt
```

The command creates an empty file named `demo.txt` in `/tmp`, assuming the operation is permitted.

Next:

```bash
ls -l demo.txt
```

An example output could be:

```text
-rw-r--r--. 1 student1 student1 0 Oct 13 10:00 demo.txt
```

Explain the output:

| Part         | Meaning                        |
| ------------ | ------------------------------ |
| -            | Regular file                   |
| rw-r--r--    | File permissions               |
| student1     | Owner                          |
| student1     | Group                          |
| 0            | File size in bytes             |
| Oct 13 10:00 | Example modification timestamp |
| demo.txt     | Filename                       |

The exact timestamp and metadata will differ on the students' machines.

Now write content:

```bash
echo "Learning RHEL filesystem" > demo.txt
cat demo.txt
```

The `>` operator redirects output into a file, replacing its existing content. By contrast, `>>` appends output.

```bash
echo "Day 1 practice" >> demo.txt
cat demo.txt
```

This demonstrates that Linux file-system knowledge connects directly to file management, permissions, shell scripting, and administration.

### 8\. Practical lab for your students

### RHEL File System Exploration

Beginner lab · 20–30 minutes · Terminal practice

Objective: Identify major system directories, inspect important files, and create a simple directory structure without modifying critical system configuration.

Progress

### 0 / 10

1. Display the current directory

pwd

2. Navigate to the root directory

cd / && pwd

3. List root directory contents

ls -la /

4. Inspect configuration files

ls /etc

5. Display the hostname

cat /etc/hostname

6. Inspect kernel information

uname -r && cat /proc/uptime

7. Inspect mounted file systems

df -hT

8. Create a practice directory

mkdir -p ~/rhel-lab/filesystem

9. Create and read a file

echo 'Hello RHEL' > ~/rhel-lab/filesystem/demo.txt && cat ~/rhel-lab/filesystem/demo.txt

10. Verify the directory structure

find ~/rhel-lab -maxdepth 3 -print

 Copy all lab commands

The commands above are intended for a RHEL training VM. They avoid changing system configuration or deleting files.

### 9\. How the file system connects to AWS and DevOps

Once students understand these directories, they can apply the knowledge to actual servers.

| Task                                   | Important locations                               |
| -------------------------------------- | ------------------------------------------------- |
| Configure an SSH server                | /etc/ssh/                                         |
| Inspect Linux system logs              | /var/log/, systemd journal                        |
| Deploy an Apache website               | /var/www/html/, /etc/httpd/                       |
| Install system packages                | Commonly /usr/, /etc/, /var/                      |
| Inspect disk devices                   | /dev/, lsblk                                      |
| Inspect memory and processes           | /proc/, free -h, ps                               |
| Investigate a service                  | /etc/, /usr/lib/systemd/system/, /run/, /var/log/ |
| Mount an additional EBS volume         | /dev/, a mount point such as /data, /etc/fstab    |
| Create user projects                   | /home/username/                                   |
| Store optional standalone applications | /opt/                                             |

For an EC2 instance running RHEL, the same principles apply. For example, when attaching an EBS volume, students identify the device, prepare the file system if necessary, mount it at a directory such as `/data`, and configure persistent mounting when appropriate. They should verify device identity before formatting anything.

### 10\. Quick revision quiz

1. Which directory is the root of the entire Linux hierarchy?

/root

/

/home

2. Where are system-wide configuration files normally stored?

/etc

/tmp

/dev

3. Where are system logs commonly stored?

/boot

/var/log

/home

4. Which directory exposes process and kernel information through a virtual file system?

/proc

/opt

/mnt

5. Which directory is the root account's home directory?

/

/root

/usr

6. Which command shows mounted file systems and disk usage?

whoami

df -hT

touch

Reset quiz

### Final summary for your classroom

The simplest way to teach the Linux file system is to group directories by their purpose rather than asking students to memorize a long list.

* Boot the machine: `/boot`

* Configure the machine: `/etc`

* Run programs: `/usr/bin`, `/usr/sbin`

* Store libraries: `/usr/lib`, `/usr/lib64` and related locations

* Store user files: `/home`

* Administer the system: `/root`

* Record changing data: `/var`

* Access devices: `/dev`

* Inspect processes and the kernel: `/proc`

* Inspect devices and kernel interfaces: `/sys`

* Keep temporary files: `/tmp`

* Keep runtime state: `/run`

* Mount storage: `/mnt`, `/media`, or an appropriate custom mount point

* Store optional applications or service data: `/opt`, `/srv`

---
title: How to check how many CPUs are there in Linux system
date: 2026-04-07
tags: [基础设施, Linux]
type: guide
status: complete
---

# How to check how many CPUs are there in Linux system

URL: https://www.cyberciti.biz/faq/check-how-many-cpus-are-there-in-linux-system/

![](https://www.cyberciti.biz/media/new/faq/2018/10/Check-how-many-CPUs-are-there-in-Linux-system.png)

I am a new Linux user. How do you check how many CPUs are there in Linux system using the command line option?

Introduction: One can obtain the number of CPUs or cores in Linux from the command line. The /proc/cpuinfo file stores CPU and system architecture dependent items, for each supported architecture. You can view /proc/cpuinfo with the help of [cat command](https://www.cyberciti.biz/faq/linux-unix-appleosx-bsd-cat-command-examples/) or [grep command](https://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/)/[egrep command](https://www.cyberciti.biz/faq/grep-regular-expressions/). This page shows how to use /proc/cpuinfo file and lscpu command to display number of processors on Linux.

Tutorial details

Difficulty level

[Easy](https://www.cyberciti.biz/faq/tag/easy/)

Root privileges

[Yes](https://www.cyberciti.biz/faq/how-can-i-log-in-as-root/)

Requirements

Linux terminal

Category

System Management

OS compatibility

Alma • [Alpine](https://www.cyberciti.biz/faq/category/alpine-linux/) • [Arch](https://www.cyberciti.biz/faq/category/arch-linux/) • [CentOS](https://www.cyberciti.biz/faq/category/centos/) • [Debian](https://www.cyberciti.biz/faq/category/debian-ubuntu/) • [Fedora](https://www.cyberciti.biz/faq/category/fedora-linux/) • [Linux](https://www.cyberciti.biz/faq/category/linux/) • Mint • [openSUSE](https://www.cyberciti.biz/faq/tag/opensuse/) • Pop!_OS • [RHEL](https://www.cyberciti.biz/faq/category/redhat-and-friends/) • Rocky • [Stream](https://www.cyberciti.biz/faq/tag/centos-stream/) • [SUSE](https://www.cyberciti.biz/faq/category/suse/) • [Ubuntu](https://www.cyberciti.biz/faq/category/ubuntu-linux/)

Est. reading time

4 minutes

nixCraft: Privacy First, Reader Supported

- **nixCraft is a one-person operation**. I create all the content myself, with no help from AI or ML. I keep the content accurate and up-to-date.
- **Your privacy is my top priority**. I don’t track you, show you ads, or spam you with emails. Just pure content in the true spirit of Linux and FLOSS.
- **Fast and clean browsing experience**. nixCraft is designed to be fast and easy to use. You won’t have to deal with pop-ups, ads, cookie banners, or other distractions.
- **Support independent content creators**. nixCraft is a labor of love, and it’s only possible thanks to the support of our readers. If you enjoy the content, please support us on Patreon or share this page on social media or your blog. Every bit helps.

[Join **Patreon** ➔](https://www.patreon.com/nixcraft)

## How do you check how many CPUs are there in Linux system?

You can use one of the following command to find the number of physical CPU cores including all cores on Linux:

1. lscpu command
2. cat /proc/cpuinfo
3. top or htop command
4. nproc command
5. dmidecode -t processor command
6. getconf _NPROCESSORS_ONLN command

Let us see all commands and examples in details.

## How to display information about the CPU on Linux

Just run the lscpu command:
`$ lscpu  
$ lscpu | egrep 'Model name|Socket|Thread|NUMA|CPU\(s\)'  
$ lscpu -p`

The output clearly indicate that I have:

![](https://www.cyberciti.biz/media/new/faq/2018/10/Check-how-many-CPUs-are-there-in-Linux-system.png)

1. **CPU model/make**: AMD Ryzen 7 1700 Eight-Core Processor
2. **Socket**: Single (1)
3. **CPU Core**: 8
4. **Thread per core**: 2
5. **Total threads**: 16 (CPU core[8] * Thread per core [2])

The following line indicates that I have two physical CPUs:

CPU socket(s):         2

## Use /proc/cpuinfo to find out how many CPUs are there in Linux

The [lscpu command gathers CPU architecture information from sysfs, /proc/cpuinfo](https://www.cyberciti.biz/faq/lscpu-command-find-out-cpu-architecture-information/) and other sources. To view use the [cat command](https://www.cyberciti.biz/faq/linux-unix-appleosx-bsd-cat-command-examples/) and [more command](https://bash.cyberciti.biz/guide/More_command) as follows:
`$ cat /proc/cpuinfo`
OR
`$ more /proc/cpuinfo`

Let us print cpu thread count:
`$ echo "CPU threads: $(grep -c processor /proc/cpuinfo)"  
$ grep 'cpu cores' /proc/cpuinfo | uniq`

![](https://www.cyberciti.biz/media/new/faq/2018/10/Command-To-Check-Number-Of-CPU-Cores-On-Linux.png)

## Run top or htop command to obtain the number of CPUs/cores in Linux

Simply run the following command and hit ‘1’:
`$ top`

Another option is to run lovely htop:
`$ htop`

![](https://www.cyberciti.biz/media/new/faq/2018/10/Obtain-the-number-of-CPUs-cores-in-Linux-from-the-command-line.png)

![](https://www.cyberciti.biz/media/new/faq/2018/10/Show-number-of-cores-of-a-system-in-Linux-with-htop.png)

[How do I Find Out Linux CPU Utilization?](https://www.cyberciti.biz/tips/how-do-i-find-out-linux-cpu-utilization.html)

## Execute nproc print the number of CPUs available on Linux

Let us print the number of installed processors on your system i.e core count:
`$ nproc --all  
$ echo "Threads/core: $(nproc --all)"`
Sample outputs:

Threads/core: 16

**Prerequisite**
By default,

hwinfo

command may not be installed on your system. Hence, use the [apk command](https://www.cyberciti.biz/faq/10-alpine-linux-apk-command-examples/) on Alpine Linux, dnf command/[yum command](https://www.cyberciti.biz/faq/rhel-centos-fedora-linux-yum-command-howto/) on RHEL & co, [apt command](https://www.cyberciti.biz/faq/ubuntu-lts-debian-linux-apt-command-examples/)/[apt-get command](https://www.cyberciti.biz/tips/linux-debian-package-management-cheat-sheet.html) on Debian, Ubuntu & co, zypper command on SUSE/OpenSUSE, pacman command on Arch Linux to install the

hwinfo

.

## How to probe for CPU/core on Linux using hwinfo command

![](https://www.cyberciti.biz/media/new/faq/2018/10/Linux-see-CPU-core-information-with-hwinfo.png)

## Linux display CPU core with getconf _NPROCESSORS_ONLN command

One can query Linux system configuration variables with getconf command:
`$ getconf _NPROCESSORS_ONLN  
$ echo "Number of CPU/cores online at $HOSTNAME: $(getconf _NPROCESSORS_ONLN)"`
Sample outputs:

Number of CPU/cores online at nixcraft-asus.nixcraft.com: 16

## dmidecode -t processor command

You can use [get BIOS and hardware information with dmidecode command (DMI table decoder) on Linux](https://www.cyberciti.biz/tips/querying-dumping-bios-from-linux-command-prompt.html). To find out how many CPUs are there in Linux system, run:
`$ sudo dmidecode -t 4  
$ sudo dmidecode -t 4 | egrep -i 'core (count|enabled)|thread count|Version'`

![](https://www.cyberciti.biz/media/new/faq/2018/10/Linux-dmidecode-show-CPU-thread-and-core-count.png)

[Linux Setting processor affinity for a certain task or process](https://www.cyberciti.biz/tips/setting-processor-affinity-certain-task-or-process.html)

### Here is a quick video demo of lscpu and other commands:

![](https://i.ytimg.com/vi/ID/hqdefault.jpg)

## Conclusion

You learned how to display information about the CPU architecture, core, threads, CPU version/model, vendor and other information using various Linux command line options. See the following manula pages using the [help command](https://bash.cyberciti.biz/guide/Help_command) or [man command](https://bash.cyberciti.biz/guide/Man_command):
`$ man lscpu  
$ man 5 proc  
$ man getconf  
$ man nproc  
$ man dmidecode  
$ man hwinfo`

### Did you notice? 🧐

nixCraft is ad-free to protect your privacy and security. We rely on reader support to keep the site running. Please consider subscribing to us on Patreon or supporting us with a one-time support through PayPal. Your support will help us cover the costs of hosting, CDN, DNS, and tutorial creation.

[Join **Patreon** ➔](https://www.patreon.com/nixcraft) [**PayPal** ➔](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=LJF8UGD7QKF3U)
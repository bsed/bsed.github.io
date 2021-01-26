---
title: "在CentOS中使用setns系统调用"
categories: [ "Linux" ]
tags: [ "linux","centos","setns" ]
draft: false
slug: "use-the-setns-system-call-in-centos"
date: "2015-08-26 12:54:00"
---

![centos.jpg][1]
系统调用setns是内核在3.0引入的一个新的系统调用，参考[这里](http://kernelnewbies.org/Linux_3.0#head-69fb31d5d1d284f3a95e56d0ec43a2b23c30c4f3)

centos6.5的内核支持该系统调用，但用户态的glibc(2.12)并不支持该系统调用。

考虑下面的示例，引自[这里](http://man7.org/linux/man-pages/man2/setns.2.html)


<!--more-->


```c
#define _GNU_SOURCE
#include <fcntl.h>
#include <sched.h>
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>

#define errExit(msg)    do { perror(msg); exit(EXIT_FAILURE); \
} while (0)

        int
main(int argc, char *argv[])
{
        int fd;

        if (argc < 3) {
                fprintf(stderr, "%s /proc/PID/ns/FILE cmd args...\n", argv[0]);
                exit(EXIT_FAILURE);
        }

        fd = open(argv[1], O_RDONLY);  /* Get descriptor for namespace */
        if (fd == -1)
                errExit("open");

        if (setns(fd, 0) == -1)        /* Join that namespace */
                errExit("setns");

        execvp(argv[2], &argv[2]);     /* Execute a command in namespace */
        errExit("execvp");
}
```

```sh
# gcc -o setns setns.c 
/tmp/ccYruvAB.o: In function `main':
setns.c:(.text+0x8c): undefined reference to `setns'
collect2: ld 返回 1
```

链接时会出错。

```sh
# grep "SYS_setns" /usr/include/bits/syscall.h  -n
220:#define SYS_setns __NR_setns
```

可以看到头文件的确有该系统调用的声明。

为了在CentOS6.5上也可以使用该系统调用，我们可以自己实现该函数：

```c
// Use raw setns syscall for versions of glibc that don't include it (namely glibc-2.12)
#if __GLIBC__ == 2 && __GLIBC_MINOR__ < 14
#include <sched.h>
#include "syscall.h"
#ifdef SYS_setns
int setns(int fd, int nstype)
{
	return syscall(SYS_setns, fd, nstype);
}
#endif
#endif
```


  [1]: https://imgs.gnux.cn/usr/uploads/2015/08/2045101484.jpg
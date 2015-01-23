Useful SystemTap scripts for daily tasks
========================================

This repo holds some SystemTap scripts, which I have used on my job
on a more or less daily basis.

filewatch.stp
-------------
Used for watching file accesses from Kernel space. Useful if you 
want to know which user/program accesses a file.

Example:

    [root@devel-el6 ~]# stap filewatch.stap /etc/passwd
    PROG(PID)            UID     FILE/FLAGS
    crond(1118)          0       "/etc/passwd", O_RDONLY|O_CLOEXEC
    crond(1118)          0       "/etc/passwd", O_RDONLY|O_CLOEXEC
    crond(1118)          0       "/etc/passwd", O_RDONLY|O_CLOEXEC
    crond(1118)          0       "/etc/passwd", O_RDONLY|O_CLOEXEC
    whoami(1693)         500     "/etc/passwd", O_RDONLY|O_CLOEXEC
    getent(1695)         500     "/etc/passwd", O_RDONLY|O_CLOEXEC

malloc.stap
-----------
Used for generating memory allocation stacktraces, which then could 
be transformed into a flamegraph with the magnificent toolchain from 
Brendan Gregg, which resides [here](https://github.com/brendangregg/FlameGraph).

Example:

    [root@devel-el6 ~]# stap -d /lib64/ld-2.12.so -d /lib64/libc-2.12.so -d /home/devel/memeater malloc.stap memeater
    sys_mmap+0x0 [kernel]
    tracesys+0xd9 [kernel]
    mmap64+0xa [ld-2.12.so]
    __libc_memalign+0x81 [ld-2.12.so]
    _dl_init_paths+0x8b [ld-2.12.so]
    dl_main+0xb5a [ld-2.12.so]
    _dl_sysdep_start+0x18e [ld-2.12.so]
    _dl_start+0x374 [ld-2.12.so]
    0x7ff7137c9b08 [ld-2.12.so+0xb08]
    4096
    
    sys_mmap+0x0 [kernel]
    tracesys+0xd9 [kernel]
    mmap64+0xa [libc-2.12.so]
    _int_malloc+0x1265 [libc-2.12.so]
    malloc+0x71 [libc-2.12.so]
    memeat+0x5a [memeater]
    main+0x11f [memeater]
    __libc_start_main+0xfd [libc-2.12.so]
    _start+0x29 [memeater]
    104898560
    
    sys_mmap+0x0 [kernel]
    tracesys+0xd9 [kernel]
    mmap64+0xa [libc-2.12.so]
    _IO_file_doallocate+0x7c [libc-2.12.so]
    _IO_doallocbuf+0x2c [libc-2.12.so]
    _IO_file_overflow@@GLIBC_2.2.5+0x190 [libc-2.12.so]
    _IO_file_xsputn@@GLIBC_2.2.5+0x6d [libc-2.12.so]
    _IO_vfprintf+0x159 [libc-2.12.so]
    printf+0x9a [libc-2.12.so]
    main+0x16c [memeater]
    __libc_start_main+0xfd [libc-2.12.so]
    _start+0x29 [memeater]
    4096
    
    sys_mmap+0x0 [kernel]
    tracesys+0xd9 [kernel]
    mmap64+0xa [libc-2.12.so]
    _IO_file_doallocate+0x7c [libc-2.12.so]
    _IO_doallocbuf+0x2c [libc-2.12.so]
    _IO_file_underflow@@GLIBC_2.2.5+0x1b4 [libc-2.12.so]
    _IO_default_uflow+0xe [libc-2.12.so]
    getchar+0xbc [libc-2.12.so]
    main+0x171 [memeater]
    __libc_start_main+0xfd [libc-2.12.so]
    _start+0x29 [memeater]
    4096

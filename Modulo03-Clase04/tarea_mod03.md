Eres un DevOps Engineer que recibio una alerta de que una aplicación crítica en una instancia EC2 (en AWS) está experimentando problemas de rendimiento y accesibilidad. Los usuarios han reportado que la aplicación Apache 2 está respondiendo lentamente y, en algunos casos, no está accesible.

### Para realizar este ejercicio, tenes que instalar Apache 2 en tu Multipass
*sudo apt-get install apache2*

Tarea:

#### Paso 1: Verificar el Estado del Sistema de Archivos y Dispositivos
##### - Lista los dispositivos de bloque y asegúrate de que todos los discos necesarios están disponibles y no presentan problemas.
    ```shell
    ubuntu@foo:~$ ls -l /dev | grep "^b"
    brw-rw---- 1 root disk      7,   0 Jul 22 14:29 loop0
    brw-rw---- 1 root disk      7,   1 Jul 22 14:28 loop1
    brw-rw---- 1 root disk      7,   2 Jul 22 14:28 loop2
    brw-rw---- 1 root disk      7,   3 Jul 22 14:28 loop3
    brw-rw---- 1 root disk      7,   4 Jul 22 14:28 loop4
    brw-rw---- 1 root disk      7,   5 Jul 22 14:28 loop5
    brw-rw---- 1 root disk      7,   6 Jul 22 14:28 loop6
    brw-rw---- 1 root disk      7,   7 Jul 22 14:28 loop7
    brw-rw---- 1 root disk      8,   0 Jul 22 14:28 sda
    brw-rw---- 1 root disk      8,   1 Jul 22 14:29 sda1
    brw-rw---- 1 root disk      8,  14 Jul 22 14:28 sda14
    brw-rw---- 1 root disk      8,  15 Jul 22 14:28 sda15
    brw-rw---- 1 root disk    259,   0 Jul 22 14:28 sda16
    brw-rw---- 1 root cdrom    11,   0 Jul 22 14:28 sr0
    ```
##### - Lista los dispositivos de carácter y confirma que no hay problemas con los dispositivos de entrada/salida.
    ```shell
    ubuntu@foo:~$ ls -l /dev | grep "^c"
    crw-r--r-- 1 root root     10, 235 Jul 22 14:28 autofs
    crw-rw---- 1 root disk     10, 234 Jul 22 14:28 btrfs-control
    crw--w---- 1 root tty       5,   1 Jul 22 14:28 console
    crw------- 1 root root     10, 123 Jul 22 14:28 cpu_dma_latency
    crw------- 1 root root     10, 203 Jul 22 14:28 cuse
    crw------- 1 root root     10, 125 Jul 22 14:28 ecryptfs
    crw-rw---- 1 root video    29,   0 Jul 22 14:28 fb0
    crw-rw-rw- 1 root root      1,   7 Jul 22 14:28 full
    crw-rw-rw- 1 root root     10, 229 Jul 22 14:28 fuse
    crw------- 1 root root     10, 228 Jul 22 14:28 hpet
    crw------- 1 root root     10, 183 Jul 22 14:28 hwrng
    crw-r--r-- 1 root root      1,  11 Jul 22 14:28 kmsg
    crw-rw---- 1 root disk     10, 237 Jul 22 14:28 loop-control
    crw------- 1 root root     10, 227 Jul 22 14:28 mcelog
    crw-r----- 1 root kmem      1,   1 Jul 22 14:28 mem
    crw-rw-rw- 1 root root      1,   3 Jul 22 14:28 null
    crw------- 1 root root     10, 144 Jul 22 14:28 nvram
    crw-r----- 1 root kmem      1,   4 Jul 22 14:28 port
    crw------- 1 root root    108,   0 Jul 22 14:28 ppp
    crw------- 1 root root     10,   1 Jul 22 14:28 psaux
    crw-rw-rw- 1 root tty       5,   2 Jul 23 14:16 ptmx
    crw------- 1 root root    246,   0 Jul 22 14:28 ptp0
    crw-rw-rw- 1 root root      1,   8 Jul 22 14:28 random
    crw-rw-r-- 1 root root     10, 242 Jul 22 14:28 rfkill
    crw------- 1 root root    248,   0 Jul 22 14:28 rtc0
    crw-rw---- 1 root disk     21,   0 Jul 22 14:28 sg0
    crw-rw---- 1 root cdrom    21,   1 Jul 22 14:28 sg1
    crw------- 1 root root     10, 231 Jul 22 14:28 snapshot
    crw-rw-rw- 1 root tty       5,   0 Jul 22 22:52 tty
    crw--w---- 1 root tty       4,   0 Jul 22 14:28 tty0
    crw--w---- 1 root tty       4,   1 Jul 22 14:29 tty1
    crw--w---- 1 root tty       4,  10 Jul 22 14:28 tty10
    crw--w---- 1 root tty       4,  11 Jul 22 14:28 tty11
    crw--w---- 1 root tty       4,  12 Jul 22 14:28 tty12
    crw--w---- 1 root tty       4,  13 Jul 22 14:28 tty13
    crw--w---- 1 root tty       4,  14 Jul 22 14:28 tty14
    crw--w---- 1 root tty       4,  15 Jul 22 14:28 tty15
    crw--w---- 1 root tty       4,  16 Jul 22 14:28 tty16
    crw--w---- 1 root tty       4,  17 Jul 22 14:28 tty17
    crw--w---- 1 root tty       4,  18 Jul 22 14:28 tty18
    crw--w---- 1 root tty       4,  19 Jul 22 14:28 tty19
    crw--w---- 1 root tty       4,   2 Jul 22 14:29 tty2
    crw--w---- 1 root tty       4,  20 Jul 22 14:28 tty20
    crw--w---- 1 root tty       4,  21 Jul 22 14:28 tty21
    crw--w---- 1 root tty       4,  22 Jul 22 14:28 tty22
    crw--w---- 1 root tty       4,  23 Jul 22 14:28 tty23
    crw--w---- 1 root tty       4,  24 Jul 22 14:28 tty24
    crw--w---- 1 root tty       4,  25 Jul 22 14:28 tty25
    crw--w---- 1 root tty       4,  26 Jul 22 14:28 tty26
    crw--w---- 1 root tty       4,  27 Jul 22 14:28 tty27
    crw--w---- 1 root tty       4,  28 Jul 22 14:28 tty28
    crw--w---- 1 root tty       4,  29 Jul 22 14:28 tty29
    crw--w---- 1 root tty       4,   3 Jul 22 14:29 tty3
    crw--w---- 1 root tty       4,  30 Jul 22 14:28 tty30
    crw--w---- 1 root tty       4,  31 Jul 22 14:28 tty31
    crw--w---- 1 root tty       4,  32 Jul 22 14:28 tty32
    crw--w---- 1 root tty       4,  33 Jul 22 14:28 tty33
    crw--w---- 1 root tty       4,  34 Jul 22 14:28 tty34
    crw--w---- 1 root tty       4,  35 Jul 22 14:28 tty35
    crw--w---- 1 root tty       4,  36 Jul 22 14:28 tty36
    crw--w---- 1 root tty       4,  37 Jul 22 14:28 tty37
    crw--w---- 1 root tty       4,  38 Jul 22 14:28 tty38
    crw--w---- 1 root tty       4,  39 Jul 22 14:28 tty39
    crw--w---- 1 root tty       4,   4 Jul 22 14:29 tty4
    crw--w---- 1 root tty       4,  40 Jul 22 14:28 tty40
    crw--w---- 1 root tty       4,  41 Jul 22 14:28 tty41
    crw--w---- 1 root tty       4,  42 Jul 22 14:28 tty42
    crw--w---- 1 root tty       4,  43 Jul 22 14:28 tty43
    crw--w---- 1 root tty       4,  44 Jul 22 14:28 tty44
    crw--w---- 1 root tty       4,  45 Jul 22 14:28 tty45
    crw--w---- 1 root tty       4,  46 Jul 22 14:28 tty46
    crw--w---- 1 root tty       4,  47 Jul 22 14:28 tty47
    crw--w---- 1 root tty       4,  48 Jul 22 14:28 tty48
    crw--w---- 1 root tty       4,  49 Jul 22 14:28 tty49
    crw--w---- 1 root tty       4,   5 Jul 22 14:29 tty5
    crw--w---- 1 root tty       4,  50 Jul 22 14:28 tty50
    crw--w---- 1 root tty       4,  51 Jul 22 14:28 tty51
    crw--w---- 1 root tty       4,  52 Jul 22 14:28 tty52
    crw--w---- 1 root tty       4,  53 Jul 22 14:28 tty53
    crw--w---- 1 root tty       4,  54 Jul 22 14:28 tty54
    crw--w---- 1 root tty       4,  55 Jul 22 14:28 tty55
    crw--w---- 1 root tty       4,  56 Jul 22 14:28 tty56
    crw--w---- 1 root tty       4,  57 Jul 22 14:28 tty57
    crw--w---- 1 root tty       4,  58 Jul 22 14:28 tty58
    crw--w---- 1 root tty       4,  59 Jul 22 14:28 tty59
    crw--w---- 1 root tty       4,   6 Jul 22 14:29 tty6
    crw--w---- 1 root tty       4,  60 Jul 22 14:28 tty60
    crw--w---- 1 root tty       4,  61 Jul 22 14:28 tty61
    crw--w---- 1 root tty       4,  62 Jul 22 14:28 tty62
    crw--w---- 1 root tty       4,  63 Jul 22 14:28 tty63
    crw--w---- 1 root tty       4,   7 Jul 22 14:28 tty7
    crw--w---- 1 root tty       4,   8 Jul 22 14:28 tty8
    crw--w---- 1 root tty       4,   9 Jul 22 14:28 tty9
    crw-rw---- 1 root dialout   4,  64 Jul 22 14:28 ttyS0
    crw-rw---- 1 root dialout   4,  65 Jul 22 14:28 ttyS1
    crw-rw---- 1 root dialout   4,  74 Jul 22 14:28 ttyS10
    crw-rw---- 1 root dialout   4,  75 Jul 22 14:28 ttyS11
    crw-rw---- 1 root dialout   4,  76 Jul 22 14:28 ttyS12
    crw-rw---- 1 root dialout   4,  77 Jul 22 14:28 ttyS13
    crw-rw---- 1 root dialout   4,  78 Jul 22 14:28 ttyS14
    crw-rw---- 1 root dialout   4,  79 Jul 22 14:28 ttyS15
    crw-rw---- 1 root dialout   4,  80 Jul 22 14:28 ttyS16
    crw-rw---- 1 root dialout   4,  81 Jul 22 14:28 ttyS17
    crw-rw---- 1 root dialout   4,  82 Jul 22 14:28 ttyS18
    crw-rw---- 1 root dialout   4,  83 Jul 22 14:28 ttyS19
    crw-rw---- 1 root dialout   4,  66 Jul 22 14:28 ttyS2
    crw-rw---- 1 root dialout   4,  84 Jul 22 14:28 ttyS20
    crw-rw---- 1 root dialout   4,  85 Jul 22 14:28 ttyS21
    crw-rw---- 1 root dialout   4,  86 Jul 22 14:28 ttyS22
    crw-rw---- 1 root dialout   4,  87 Jul 22 14:28 ttyS23
    crw-rw---- 1 root dialout   4,  88 Jul 22 14:28 ttyS24
    crw-rw---- 1 root dialout   4,  89 Jul 22 14:28 ttyS25
    crw-rw---- 1 root dialout   4,  90 Jul 22 14:28 ttyS26
    crw-rw---- 1 root dialout   4,  91 Jul 22 14:28 ttyS27
    crw-rw---- 1 root dialout   4,  92 Jul 22 14:28 ttyS28
    crw-rw---- 1 root dialout   4,  93 Jul 22 14:28 ttyS29
    crw-rw---- 1 root dialout   4,  67 Jul 22 14:28 ttyS3
    crw-rw---- 1 root dialout   4,  94 Jul 22 14:28 ttyS30
    crw-rw---- 1 root dialout   4,  95 Jul 22 14:28 ttyS31
    crw-rw---- 1 root dialout   4,  68 Jul 22 14:28 ttyS4
    crw-rw---- 1 root dialout   4,  69 Jul 22 14:28 ttyS5
    crw-rw---- 1 root dialout   4,  70 Jul 22 14:28 ttyS6
    crw-rw---- 1 root dialout   4,  71 Jul 22 14:28 ttyS7
    crw-rw---- 1 root dialout   4,  72 Jul 22 14:28 ttyS8
    crw-rw---- 1 root dialout   4,  73 Jul 22 14:28 ttyS9
    crw------- 1 root root      5,   3 Jul 22 14:28 ttyprintk
    crw-rw---- 1 root kvm      10, 124 Jul 22 14:28 udmabuf
    crw------- 1 root root     10, 223 Jul 22 14:28 uinput
    crw-rw-rw- 1 root root      1,   9 Jul 22 14:28 urandom
    crw------- 1 root root     10, 126 Jul 22 14:28 userfaultfd
    crw-rw---- 1 root tty       7,   0 Jul 22 14:28 vcs
    crw-rw---- 1 root tty       7,   1 Jul 22 14:28 vcs1
    crw-rw---- 1 root tty       7,   2 Jul 22 14:29 vcs2
    crw-rw---- 1 root tty       7,   3 Jul 22 14:29 vcs3
    crw-rw---- 1 root tty       7,   4 Jul 22 14:29 vcs4
    crw-rw---- 1 root tty       7,   5 Jul 22 14:29 vcs5
    crw-rw---- 1 root tty       7,   6 Jul 22 14:29 vcs6
    crw-rw---- 1 root tty       7, 128 Jul 22 14:28 vcsa
    crw-rw---- 1 root tty       7, 129 Jul 22 14:28 vcsa1
    crw-rw---- 1 root tty       7, 130 Jul 22 14:29 vcsa2
    crw-rw---- 1 root tty       7, 131 Jul 22 14:29 vcsa3
    crw-rw---- 1 root tty       7, 132 Jul 22 14:29 vcsa4
    crw-rw---- 1 root tty       7, 133 Jul 22 14:29 vcsa5
    crw-rw---- 1 root tty       7, 134 Jul 22 14:29 vcsa6
    crw-rw---- 1 root tty       7,  64 Jul 22 14:28 vcsu
    crw-rw---- 1 root tty       7,  65 Jul 22 14:28 vcsu1
    crw-rw---- 1 root tty       7,  66 Jul 22 14:29 vcsu2
    crw-rw---- 1 root tty       7,  67 Jul 22 14:29 vcsu3
    crw-rw---- 1 root tty       7,  68 Jul 22 14:29 vcsu4
    crw-rw---- 1 root tty       7,  69 Jul 22 14:29 vcsu5
    crw-rw---- 1 root tty       7,  70 Jul 22 14:29 vcsu6
    crw------- 1 root root     10, 127 Jul 22 14:28 vga_arbiter
    crw-rw---- 1 root kvm      10, 238 Jul 22 14:28 vhost-net
    crw-rw---- 1 root kvm      10, 241 Jul 22 14:28 vhost-vsock
    crw-rw-rw- 1 root root      1,   5 Jul 22 14:28 zero
    crw------- 1 root root     10, 249 Jul 22 14:28 zfs
    ```
##### - Lista todas las particiones y asegúrate de que no hay particiones llenas o mal configuradas.
    ```shell
    ubuntu@foo:~$ df -ha
    Filesystem      Size  Used Avail Use% Mounted on
    sysfs              0     0     0    - /sys
    proc               0     0     0    - /proc
    udev            431M     0  431M   0% /dev
    devpts             0     0     0    - /dev/pts
    tmpfs            90M  948K   89M   2% /run
    efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
    /dev/sda1       3.9G  2.0G  1.9G  51% /
    securityfs         0     0     0    - /sys/kernel/security
    tmpfs           449M     0  449M   0% /dev/shm
    tmpfs           5.0M     0  5.0M   0% /run/lock
    cgroup2            0     0     0    - /sys/fs/cgroup
    pstore             0     0     0    - /sys/fs/pstore
    bpf                0     0     0    - /sys/fs/bpf
    systemd-1          -     -     -    - /proc/sys/fs/binfmt_misc
    hugetlbfs          0     0     0    - /dev/hugepages
    mqueue             0     0     0    - /dev/mqueue
    debugfs            0     0     0    - /sys/kernel/debug
    tracefs            0     0     0    - /sys/kernel/tracing
    fusectl            0     0     0    - /sys/fs/fuse/connections
    configfs           0     0     0    - /sys/kernel/config
    /dev/sda16      881M  112M  708M  14% /boot
    /dev/sda15      105M  6.1M   99M   6% /boot/efi
    binfmt_misc        0     0     0    - /proc/sys/fs/binfmt_misc
    tmpfs            90M   12K   90M   1% /run/user/1000
    tracefs            -     -     -    - /sys/kernel/debug/tracing
    ```
##### - Revisa la información de montajes para asegurarte de que todas las particiones necesarias están montadas correctamente.
    ```shell
    ubuntu@foo:~$ cat /proc/mounts
    sysfs /sys sysfs rw,nosuid,nodev,noexec,relatime 0 0
    proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
    udev /dev devtmpfs rw,nosuid,relatime,size=441328k,nr_inodes=110332,mode=755,inode64 0 0
    devpts /dev/pts devpts rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 0 0
    tmpfs /run tmpfs rw,nosuid,nodev,noexec,relatime,size=91780k,mode=755,inode64 0 0
    efivarfs /sys/firmware/efi/efivars efivarfs rw,nosuid,nodev,noexec,relatime 0 0
    /dev/sda1 / ext4 rw,relatime,discard,errors=remount-ro,commit=30 0 0
    securityfs /sys/kernel/security securityfs rw,nosuid,nodev,noexec,relatime 0 0
    tmpfs /dev/shm tmpfs rw,nosuid,nodev,inode64 0 0
    tmpfs /run/lock tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k,inode64 0 0
    cgroup2 /sys/fs/cgroup cgroup2 rw,nosuid,nodev,noexec,relatime,nsdelegate,memory_recursiveprot 0 0
    pstore /sys/fs/pstore pstore rw,nosuid,nodev,noexec,relatime 0 0
    bpf /sys/fs/bpf bpf rw,nosuid,nodev,noexec,relatime,mode=700 0 0
    systemd-1 /proc/sys/fs/binfmt_misc autofs rw,relatime,fd=32,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=2229 0 0
    hugetlbfs /dev/hugepages hugetlbfs rw,nosuid,nodev,relatime,pagesize=2M 0 0
    mqueue /dev/mqueue mqueue rw,nosuid,nodev,noexec,relatime 0 0
    debugfs /sys/kernel/debug debugfs rw,nosuid,nodev,noexec,relatime 0 0
    tracefs /sys/kernel/tracing tracefs rw,nosuid,nodev,noexec,relatime 0 0
    fusectl /sys/fs/fuse/connections fusectl rw,nosuid,nodev,noexec,relatime 0 0
    configfs /sys/kernel/config configfs rw,nosuid,nodev,noexec,relatime 0 0
    /dev/sda16 /boot ext4 rw,relatime 0 0
    /dev/sda15 /boot/efi vfat rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro 0 0
    binfmt_misc /proc/sys/fs/binfmt_misc binfmt_misc rw,nosuid,nodev,noexec,relatime 0 0
    tmpfs /run/user/1000 tmpfs rw,nosuid,nodev,relatime,size=91776k,nr_inodes=22944,mode=700,uid=1000,gid=1000,inode64 0 0
    tracefs /sys/kernel/debug/tracing tracefs rw,nosuid,nodev,noexec,relatime 0 0
    ```
#### Paso 2: Verificar el Rendimiento del Sistema
##### - Revisa la información del CPU para entender mejor la capacidad del sistema.
    ```shell
    ubuntu@foo:~$ cat /proc/cpuinfo
    processor       : 0
    vendor_id       : GenuineIntel
    cpu family      : 6
    model           : 140
    model name      : 11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz
    stepping        : 1
    microcode       : 0xffffffff
    cpu MHz         : 1689.599
    cache size      : 12288 KB
    physical id     : 0
    siblings        : 1
    core id         : 0
    cpu cores       : 1
    apicid          : 0
    initial apicid  : 0
    fpu             : yes
    fpu_exception   : yes
    cpuid level     : 21
    wp              : yes
    flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc rep_good nopl xtopology cpuid pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch ssbd ibrs ibpb stibp ibrs_enhanced fsgsbase bmi1 avx2 smep bmi2 erms invpcid avx512f avx512dq rdseed adx smap avx512ifma clflushopt clwb avx512cd sha_ni avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves avx512vbmi avx512_vbmi2 gfni vaes vpclmulqdq avx512_vnni avx512_bitalg avx512_vpopcntdq rdpid fsrm flush_l1d arch_capabilities
    bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs retbleed eibrs_pbrsb gds bhi
    bogomips        : 3379.19
    clflush size    : 64
    cache_alignment : 64
    address sizes   : 39 bits physical, 48 bits virtual
    power management:
    ```
##### - Analiza la información de la memoria para identificar posibles cuellos de botella.
    ````shell
    ubuntu@foo:~$ cat /proc/meminfo
    MemTotal:         917796 kB
    MemFree:          152828 kB
    MemAvailable:     567144 kB
    Buffers:          103396 kB
    Cached:           414192 kB
    SwapCached:            0 kB
    Active:           190324 kB
    Inactive:         366296 kB
    Active(anon):      48896 kB
    Inactive(anon):      184 kB
    Active(file):     141428 kB
    Inactive(file):   366112 kB
    Unevictable:       27440 kB
    Mlocked:           27440 kB
    SwapTotal:             0 kB
    SwapFree:              0 kB
    Zswap:                 0 kB
    Zswapped:              0 kB
    Dirty:                 0 kB
    Writeback:             0 kB
    AnonPages:         66508 kB
    Mapped:            75380 kB
    Shmem:              1300 kB
    KReclaimable:      61684 kB
    Slab:             110596 kB
    SReclaimable:      61684 kB
    SUnreclaim:        48912 kB
    KernelStack:        2844 kB
    PageTables:         3156 kB
    SecPageTables:         0 kB
    NFS_Unstable:          0 kB
    Bounce:                0 kB
    WritebackTmp:          0 kB
    CommitLimit:      458896 kB
    Committed_AS:     745332 kB
    VmallocTotal:   34359738367 kB
    VmallocUsed:       35512 kB
    VmallocChunk:          0 kB
    Percpu:              632 kB
    HardwareCorrupted:     0 kB
    AnonHugePages:         0 kB
    ShmemHugePages:        0 kB
    ShmemPmdMapped:        0 kB
    FileHugePages:         0 kB
    FilePmdMapped:         0 kB
    Unaccepted:            0 kB
    HugePages_Total:       0
    HugePages_Free:        0
    HugePages_Rsvd:        0
    HugePages_Surp:        0
    Hugepagesize:       2048 kB
    Hugetlb:               0 kB
    DirectMap4k:       87336 kB
    DirectMap2M:      960512 kB
    DirectMap1G:           0 kB
    ```
##### - Analiza los últimos 20 registros del log del sistema para confirmar que no haya nada mal con el SO.
```shell
ubuntu@foo:~$ tail -n 20 /var/log/syslog
2024-07-22T18:24:29.112346+00:00 foo kernel: audit: type=1400 audit(1721658586.551:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="busybox" pid=550 comm="apparmor_parser"
2024-07-22T18:24:29.112346+00:00 foo kernel: audit: type=1400 audit(1721658586.556:10): apparmor="STATUS" operation="profile_load" profile="unconfined" name="bwrap" pid=551 comm="apparmor_parser"
2024-07-22T18:24:29.112347+00:00 foo kernel: audit: type=1400 audit(1721658586.556:11): apparmor="STATUS" operation="profile_load" profile="unconfined" name="unpriv_bwrap" pid=551 comm="apparmor_parser"
2024-07-22T18:24:29.112347+00:00 foo kernel: ISO 9660 Extensions: Microsoft Joliet Level 3
2024-07-22T18:24:29.112347+00:00 foo kernel: ISOFS: changing to secondary root
2024-07-22T18:24:29.112349+00:00 foo kernel: loop0: detected capacity change from 0 to 8
2024-07-22T18:24:29.112349+00:00 foo kernel: systemd-journald[323]: /var/log/journal/ceade4b1ee5041efaa125996f18ca79a/user-1000.journal: Journal file uses a different sequence number ID, rotating.
2024-07-22T18:24:29.112350+00:00 foo kernel: kauditd_printk_skb: 113 callbacks suppressed
2024-07-22T18:24:29.112350+00:00 foo kernel: audit: type=1400 audit(1721672669.108:125): apparmor="DENIED" operation="open" class="file" profile="rsyslogd" name="/proc/1851/fd/" pid=1851 comm="rsyslogd" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:24:29.112350+00:00 foo kernel: audit: type=1400 audit(1721672669.108:126): apparmor="DENIED" operation="unlink" class="file" profile="rsyslogd" name="/run/systemd/journal/syslog" pid=1851 comm="rsyslogd" requested_mask="d" denied_mask="d" fsuid=0 ouid=0
2024-07-22T18:29:21.761932+00:00 foo kernel: audit: type=1400 audit(1721672961.760:127): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/lib/apt/lists/" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.761946+00:00 foo kernel: audit: type=1400 audit(1721672961.760:128): apparmor="DENIED" operation="exec" class="file" profile="ubuntu_pro_apt_news" name="/usr/bin/dpkg" pid=2312 comm="python3" requested_mask="x" denied_mask="x" fsuid=0 ouid=0
2024-07-22T18:29:21.762926+00:00 foo kernel: audit: type=1400 audit(1721672961.761:129): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/cache/apt/pkgcache.bin" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.762932+00:00 foo kernel: audit: type=1400 audit(1721672961.761:130): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/cache/apt/srcpkgcache.bin" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.763933+00:00 foo kernel: audit: type=1400 audit(1721672961.762:131): apparmor="DENIED" operation="exec" class="file" profile="ubuntu_pro_apt_news" name="/usr/bin/dpkg" pid=2313 comm="python3" requested_mask="x" denied_mask="x" fsuid=0 ouid=0
2024-07-22T18:29:21.763939+00:00 foo kernel: audit: type=1400 audit(1721672961.762:132): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/lib/apt/lists/archive.ubuntu.com_ubuntu_dists_noble_main_binary-amd64_Packages" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.764919+00:00 foo kernel: audit: type=1400 audit(1721672961.763:133): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/lib/apt/lists/archive.ubuntu.com_ubuntu_dists_noble_main_i18n_Translation-en" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.764923+00:00 foo kernel: audit: type=1400 audit(1721672961.763:134): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/lib/apt/lists/archive.ubuntu.com_ubuntu_dists_noble_universe_binary-amd64_Packages" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.764923+00:00 foo kernel: audit: type=1400 audit(1721672961.763:135): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/lib/apt/lists/archive.ubuntu.com_ubuntu_dists_noble_universe_i18n_Translation-en" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
2024-07-22T18:29:21.764924+00:00 foo kernel: audit: type=1400 audit(1721672961.763:136): apparmor="DENIED" operation="open" class="file" profile="ubuntu_pro_apt_news" name="/var/lib/apt/lists/archive.ubuntu.com_ubuntu_dists_noble_restricted_binary-amd64_Packages" pid=1923 comm="python3" requested_mask="r" denied_mask="r" fsuid=0 ouid=0
```
#### Paso 3: Verificación y Reinicio de Servicios
##### - Verificar el estado del servicio Apache
    ```shell
    ubuntu@foo:~$ sudo systemctl status apache2
    ● apache2.service - The Apache HTTP Server
        Loaded: loaded (/usr/lib/systemd/system/apache2.service; enabled; preset: enabled)
        Active: active (running) since Mon 2024-07-22 21:16:41 UTC; 17h ago
        Docs: https://httpd.apache.org/docs/2.4/
        Process: 3894 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
    Main PID: 3897 (apache2)
        Tasks: 55 (limit: 1034)
        Memory: 5.3M (peak: 5.5M)
            CPU: 4.064s
        CGroup: /system.slice/apache2.service
                ├─3897 /usr/sbin/apache2 -k start
                ├─3899 /usr/sbin/apache2 -k start
                └─3900 /usr/sbin/apache2 -k start

    Jul 22 21:16:41 foo systemd[1]: Starting apache2.service - The Apache HTTP Server...
    Jul 22 21:16:41 foo apachectl[3896]: AH00558: apache2: Could not reliably determine the server's fully qualified domain>
    Jul 22 21:16:41 foo systemd[1]: Started apache2.service - The Apache HTTP Server.
    ```
##### - Reiniciar el servicio Apache
    ```shell
    ubuntu@foo:~$ sudo service apache2 restart
    ubuntu@foo:~$ sudo systemctl status apache2
    ● apache2.service - The Apache HTTP Server
        Loaded: loaded (/usr/lib/systemd/system/apache2.service; enabled; preset: enabled)
        Active: active (running) since Tue 2024-07-23 14:38:48 UTC; 9s ago
        Docs: https://httpd.apache.org/docs/2.4/
        Process: 6128 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
    Main PID: 6132 (apache2)
        Tasks: 55 (limit: 1034)
        Memory: 5.3M (peak: 5.4M)
            CPU: 12ms
        CGroup: /system.slice/apache2.service
                ├─6132 /usr/sbin/apache2 -k start
                ├─6134 /usr/sbin/apache2 -k start
                └─6135 /usr/sbin/apache2 -k start

    Jul 23 14:38:48 foo systemd[1]: Starting apache2.service - The Apache HTTP Server...
    Jul 23 14:38:48 foo apachectl[6131]: AH00558: apache2: Could not reliably determine the server's fully qualified domain>
    Jul 23 14:38:48 foo systemd[1]: Started apache2.service - The Apache HTTP Server.
    ```
##### - Verificar los logs en /var/log/apache2/error.log para ver si todo funciona bien
    ```shell
    ubuntu@foo:~$ tail -n 50  /var/log/apache2/error.log
    [Mon Jul 22 20:53:37.788138 2024] [mpm_event:notice] [pid 3148:tid 140126104180608] AH00489: Apache/2.4.58 (Ubuntu) configured -- resuming normal operations
    [Mon Jul 22 20:53:37.788200 2024] [core:notice] [pid 3148:tid 140126104180608] AH00094: Command line: '/usr/sbin/apache2'
    [Mon Jul 22 21:16:11.023303 2024] [mpm_event:notice] [pid 3148:tid 140126104180608] AH00492: caught SIGWINCH, shutting down gracefully
    [Mon Jul 22 21:16:41.977833 2024] [mpm_event:notice] [pid 3897:tid 130661923329920] AH00489: Apache/2.4.58 (Ubuntu) configured -- resuming normal operations
    [Mon Jul 22 21:16:41.977895 2024] [core:notice] [pid 3897:tid 130661923329920] AH00094: Command line: '/usr/sbin/apache2'
    [Tue Jul 23 14:38:47.654142 2024] [mpm_event:notice] [pid 3897:tid 130661923329920] AH00492: caught SIGWINCH, shutting down gracefully
    [Tue Jul 23 14:38:48.701371 2024] [mpm_event:notice] [pid 6132:tid 126199555938176] AH00489: Apache/2.4.58 (Ubuntu) configured -- resuming normal operations
    [Tue Jul 23 14:38:48.701430 2024] [core:notice] [pid 6132:tid 126199555938176] AH00094: Command line: '/usr/sbin/apache2'
    ```

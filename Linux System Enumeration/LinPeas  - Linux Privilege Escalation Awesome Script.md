```shell-session
bash linpeas.sh -a -N > linpeas_results.txt
```
   
   /---------------------------------------------------------------------------------\
    |                             Do you like PEASS?                                  |
    |---------------------------------------------------------------------------------|
    |         Learn Cloud Hacking       :     https://training.hacktricks.xyz         |
    |         Follow on Twitter         :     @hacktricks_live                        |
    |         Respect on HTB            :     SirBroccoli                             |
    |---------------------------------------------------------------------------------|
    |                                 Thank you!                                      |
    \---------------------------------------------------------------------------------/
          LinPEAS-ng by carlospolop

ADVISORY: This script should be used for authorized penetration testing and/or educational purposes only. Any misuse of this software will not be the responsibility of the author or of any other collaborator. Use it at your own computers and/or with the computer owner's permission.

Linux Privesc Checklist: https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html
 [1;4mLEGEND[0m:
  RED/YELLOW: 95% a PE vector
  RED: You should take a look to it
  LightCyan: Users with console
  Blue: Users without console & mounted devs
  Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts, cronjobs) 
  LightMagenta: Your username

 Starting LinPEAS. Caching Writable Folders...
                               ╔═══════════════════╗
═══════════════════════════════╣ Basic information ╠═══════════════════════════════
                               ╚═══════════════════╝
OS: Linux version 5.15.0-135-generic (buildd@lcy02-amd64-070) (gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #146-Ubuntu SMP Sat Feb 15 17:06:22 UTC 2025
User & Groups: uid=1000(john) gid=1000(john) groups=1000(john),27(sudo)
Hostname: ubuntu

Remember that you can use the '-t' option to call the Internet connectivity checks and automatic network recon!
[+] /usr/bin/ping is available for network discovery (LinPEAS can discover hosts, learn more with -h)
[+] /usr/bin/bash is available for network discovery, port scanning and port forwarding (LinPEAS can discover hosts, scan ports, and forward ports. Learn more with -h)
[+] /usr/bin/nc is available for network discovery & port scanning (LinPEAS can discover hosts and scan ports, learn more with -h)


Caching directories DONE

                              ╔════════════════════╗
══════════════════════════════╣ System Information ╠══════════════════════════════
                              ╚════════════════════╝
╔══════════╣ Operative system
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#kernel-exploits
Linux version 5.15.0-135-generic (buildd@lcy02-amd64-070) (gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0, GNU ld (GNU Binutils for Ubuntu) 2.38) #146-Ubuntu SMP Sat Feb 15 17:06:22 UTC 2025
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.4 LTS
Release:	22.04
Codename:	jammy

╔══════════╣ Sudo version
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-version
Sudo version 1.9.9


╔══════════╣ PATH
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#writable-path-abuses
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

╔══════════╣ Date & uptime
Sat Sep 27 03:47:52 PM UTC 2025
 15:47:52 up  1:24,  1 user,  load average: 1.03, 0.28, 0.09

╔══════════╣ CPU info
Architecture:                         x86_64
CPU op-mode(s):                       32-bit, 64-bit
Address sizes:                        43 bits physical, 48 bits virtual
Byte Order:                           Little Endian
CPU(s):                               2
On-line CPU(s) list:                  0,1
Vendor ID:                            AuthenticAMD
Model name:                           AMD EPYC 7513 32-Core Processor
CPU family:                           25
Model:                                1
Thread(s) per core:                   1
Core(s) per socket:                   1
Socket(s):                            2
Stepping:                             1
BogoMIPS:                             5190.24
Flags:                                fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm constant_tsc rep_good nopl tsc_reliable nonstop_tsc cpuid extd_apicid tsc_known_freq pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw invpcid_single ibpb vmmcall fsgsbase bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xsaves clzero arat pku ospke overflow_recov succor
Hypervisor vendor:                    VMware
Virtualization type:                  full
L1d cache:                            64 KiB (2 instances)
L1i cache:                            64 KiB (2 instances)
L2 cache:                             1 MiB (2 instances)
L3 cache:                             256 MiB (2 instances)
NUMA node(s):                         1
NUMA node0 CPU(s):                    0,1
Vulnerability Gather data sampling:   Not affected
Vulnerability Itlb multihit:          Not affected
Vulnerability L1tf:                   Not affected
Vulnerability Mds:                    Not affected
Vulnerability Meltdown:               Not affected
Vulnerability Mmio stale data:        Not affected
Vulnerability Reg file data sampling: Not affected
Vulnerability Retbleed:               Not affected
Vulnerability Spec rstack overflow:   Mitigation; safe RET
Vulnerability Spec store bypass:      Vulnerable
Vulnerability Spectre v1:             Mitigation; usercopy/swapgs barriers and __user pointer sanitization
Vulnerability Spectre v2:             Mitigation; Retpolines; IBPB conditional; STIBP disabled; RSB filling; PBRSB-eIBRS Not affected; BHI Not affected
Vulnerability Srbds:                  Not affected
Vulnerability Tsx async abort:        Not affected

╔══════════╣ Unmounted file-system?
╚ Check if you can mount umounted devices
/dev/disk/by-uuid/790c151f-485f-4bba-b3ed-fe9b06df494a / ext4 defaults 0 1
/dev/disk/by-uuid/35a4e304-7894-4205-9531-118c4571fde9 none swap sw 0 0

╔══════════╣ Any sd*/disk* disk in /dev? (limit 20)
disk
sda
sda1
sda2
sda3

╔══════════╣ System stats
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           392M  1.2M  391M   1% /run
/dev/sda2        14G   12G  2.3G  84% /
tmpfs           2.0G  4.0K  2.0G   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           392M  4.0K  392M   1% /run/user/1000
               total        used        free      shared  buff/cache   available
Mem:         4005820     1001256     1986128       30880     1018436     2711444
Swap:        2094076           0     2094076

╔══════════╣ Environment
╚ Any private information inside environment variables?
SHELL=/bin/bash
PWD=/home/john
LOGNAME=john
MOTD_SHOWN=pam
HOME=/home/john
LANG=en_US.UTF-8
SSH_CONNECTION=10.10.14.54 48016 10.129.173.56 22
LESSCLOSE=/usr/bin/lesspipe %s %s
TERM=tmux-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=john
SHLVL=2
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=10.10.14.54 48016 22
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
SSH_TTY=/dev/pts/0
_=/usr/bin/env

╔══════════╣ Searching Signature verification failed in dmesg
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#dmesg-signature-verification-failed
dmesg Not Found

╔══════════╣ Executing Linux Exploit Suggester
╚ https://github.com/mzet-/linux-exploit-suggester
[+] [CVE-2022-0847] DirtyPipe

   Details: https://dirtypipe.cm4all.com/
   Exposure: less probable
   Tags: ubuntu=(20.04|21.04),debian=11
   Download URL: https://haxx.in/files/dirtypipez.c

[+] [CVE-2021-4034] PwnKit

   Details: https://www.qualys.com/2022/01/25/cve-2021-4034/pwnkit.txt
   Exposure: less probable
   Tags: ubuntu=10|11|12|13|14|15|16|17|18|19|20|21,debian=7|8|9|10|11,fedora,manjaro
   Download URL: https://codeload.github.com/berdav/CVE-2021-4034/zip/main

[+] [CVE-2021-3156] sudo Baron Samedit

   Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
   Exposure: less probable
   Tags: mint=19,ubuntu=18|20, debian=10
   Download URL: https://codeload.github.com/blasty/CVE-2021-3156/zip/main

[+] [CVE-2021-3156] sudo Baron Samedit 2

   Details: https://www.qualys.com/2021/01/26/cve-2021-3156/baron-samedit-heap-based-overflow-sudo.txt
   Exposure: less probable
   Tags: centos=6|7|8,ubuntu=14|16|17|18|19|20, debian=9|10
   Download URL: https://codeload.github.com/worawit/CVE-2021-3156/zip/main

[+] [CVE-2021-22555] Netfilter heap out-of-bounds write

   Details: https://google.github.io/security-research/pocs/linux/cve-2021-22555/writeup.html
   Exposure: less probable
   Tags: ubuntu=20.04{kernel:5.8.0-*}
   Download URL: https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
   ext-url: https://raw.githubusercontent.com/bcoles/kernel-exploits/master/CVE-2021-22555/exploit.c
   Comments: ip_tables kernel module must be loaded

[+] [CVE-2017-5618] setuid screen v4.5.0 LPE

   Details: https://seclists.org/oss-sec/2017/q1/184
   Exposure: less probable
   Download URL: https://www.exploit-db.com/download/https://www.exploit-db.com/exploits/41154


╔══════════╣ Protections
═╣ AppArmor enabled? .............. You do not have enough privilege to read the profile set.
apparmor module is loaded.
═╣ AppArmor profile? .............. unconfined
═╣ is linuxONE? ................... s390x Not Found
═╣ grsecurity present? ............ grsecurity Not Found
═╣ PaX bins present? .............. PaX Not Found
═╣ Execshield enabled? ............ Execshield Not Found
═╣ SELinux enabled? ............... sestatus Not Found
═╣ Seccomp enabled? ............... disabled
═╣ User namespace? ................ enabled
═╣ Cgroup2 enabled? ............... enabled
═╣ Is ASLR enabled? ............... Yes
═╣ Printer? ....................... No
═╣ Is this a virtual machine? ..... Yes (vmware)

╔══════════╣ Kernel Modules Information
══╣ Loaded kernel modules
Module                  Size  Used by
intel_rapl_msr         20480  0
vmw_balloon            24576  0
intel_rapl_common      40960  1 intel_rapl_msr
joydev                 32768  0
input_leds             16384  0
serio_raw              20480  0
vsock_loopback         16384  0
vmw_vsock_virtio_transport_common    40960  1 vsock_loopback
mac_hid                16384  0
vmw_vsock_vmci_transport    32768  1
vsock                  49152  5 vmw_vsock_virtio_transport_common,vsock_loopback,vmw_vsock_vmci_transport
vmw_vmci               90112  2 vmw_balloon,vmw_vsock_vmci_transport
binfmt_misc            24576  1
sch_fq_codel           20480  3
dm_multipath           40960  0
scsi_dh_rdac           20480  0
scsi_dh_emc            16384  0
scsi_dh_alua           20480  0
msr                    16384  0
efi_pstore             16384  0
ip_tables              32768  0
x_tables               53248  1 ip_tables
autofs4                49152  2
btrfs                1564672  0
blake2b_generic        20480  0
zstd_compress         229376  1 btrfs
raid10                 69632  0
raid456               163840  0
async_raid6_recov      24576  1 raid456
async_memcpy           20480  2 raid456,async_raid6_recov
async_pq               24576  2 raid456,async_raid6_recov
async_xor              20480  3 async_pq,raid456,async_raid6_recov
async_tx               20480  5 async_pq,async_memcpy,async_xor,raid456,async_raid6_recov
xor                    24576  2 async_xor,btrfs
raid6_pq              122880  4 async_pq,btrfs,raid456,async_raid6_recov
libcrc32c              16384  2 btrfs,raid456
raid1                  49152  0
raid0                  24576  0
multipath              20480  0
linear                 20480  0
crct10dif_pclmul       16384  1
crc32_pclmul           16384  0
ghash_clmulni_intel    16384  0
sha256_ssse3           32768  0
sha1_ssse3             32768  0
vmwgfx                372736  2
ttm                    86016  1 vmwgfx
aesni_intel           376832  0
crypto_simd            16384  1 aesni_intel
cryptd                 24576  2 crypto_simd,ghash_clmulni_intel
drm_kms_helper        311296  1 vmwgfx
syscopyarea            16384  1 drm_kms_helper
sysfillrect            20480  1 drm_kms_helper
sysimgblt              16384  1 drm_kms_helper
fb_sys_fops            16384  1 drm_kms_helper
psmouse               184320  0
cec                    65536  1 drm_kms_helper
rc_core                65536  1 cec
mptspi                 24576  2
drm                   622592  5 vmwgfx,drm_kms_helper,ttm
mptscsih               49152  1 mptspi
mptbase               106496  2 mptspi,mptscsih
vmxnet3                69632  0
scsi_transport_spi     32768  1 mptspi
pata_acpi              16384  0
i2c_piix4              32768  0
══╣ Kernel modules with weak perms?

══╣ Kernel modules loadable? 
Modules can be loaded



                                   ╔═══════════╗
═══════════════════════════════════╣ Container ╠═══════════════════════════════════
                                   ╚═══════════╝
╔══════════╣ Container related tools present (if any):
/snap/bin/lxc
/usr/sbin/apparmor_parser
/usr/bin/nsenter
/usr/bin/unshare
/usr/sbin/chroot
/usr/sbin/capsh
/usr/sbin/setcap
/usr/sbin/getcap

╔══════════╣ Container details
═╣ Is this a container? ........... No═╣ LXC version ................ Client version: 5.0.4
Server version: unreachable
═╣ LXC info ................... lxc Not Found
═╣ Any running containers? ........ No



                                     ╔═══════╗
═════════════════════════════════════╣ Cloud ╠═════════════════════════════════════
                                     ╚═══════╝
Learn and practice cloud hacking techniques in https://training.hacktricks.xyz

═╣ GCP Virtual Machine? ................. No
═╣ GCP Cloud Funtion? ................... No
═╣ AWS ECS? ............................. No
═╣ AWS EC2? ............................. No
═╣ AWS EC2 Beanstalk? ................... No
═╣ AWS Lambda? .......................... No
═╣ AWS Codebuild? ....................... No
═╣ DO Droplet? .......................... No
═╣ IBM Cloud VM? ........................ No
═╣ Azure VM or Az metadata? ............. No
═╣ Azure APP or IDENTITY_ENDPOINT? ...... No
═╣ Azure Automation Account? ............ No
═╣ Aliyun ECS? .......................... No
═╣ Tencent CVM? ......................... No



                ╔════════════════════════════════════════════════╗
════════════════╣ Processes, Crons, Timers, Services and Sockets ╠════════════════
                ╚════════════════════════════════════════════════╝
╔══════════╣ Running processes (cleaned)
╚ Check weird & unexpected processes run by root: https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#processes
root           1  0.0  0.3 167716 13040 ?        Ss   14:23   0:03 /sbin/init
root         424  0.0  1.7 109952 69512 ?        S<s  14:23   0:04 /lib/systemd/systemd-journald
root         461  0.0  0.6 289352 27060 ?        SLsl 14:23   0:00 /sbin/multipathd -d -s
root         464  0.0  0.1  26288  7308 ?        Ss   14:23   0:00 /lib/systemd/systemd-udevd
systemd+     513  0.0  0.1  16128  7828 ?        Ss   14:23   0:00 /lib/systemd/systemd-networkd
  └─(Caps) 0x0000000000003c00=cap_net_bind_service,cap_net_broadcast,cap_net_admin,cap_net_raw
systemd+     536  0.0  0.3  25804 13724 ?        Ss   14:23   0:00 /lib/systemd/systemd-resolved
  └─(Caps) 0x0000000000002000=cap_net_raw
systemd+     537  0.0  0.1  89364  6616 ?        Ssl  14:23   0:00 /lib/systemd/systemd-timesyncd
  └─(Caps) 0x0000000002000000=cap_sys_time
root         538  0.0  0.2  51144 11748 ?        Ss   14:23   0:00 /usr/bin/VGAuthService
root         539  0.1  0.2 241304  8792 ?        Ssl  14:23   0:05 /usr/bin/vmtoolsd
root         542  0.0  0.0  11440  2072 ?        S<sl 14:23   0:00 /sbin/auditd
root         666  0.0  0.1 101244  5964 ?        Ssl  14:23   0:00 /sbin/dhclient -1 -4 -v -i -pf /run/dhclient.eth0.pid -lf /var/lib/dhcp/dhclient.eth0.leases -I -df /var/lib/dhcp/dhclient6.eth0.leases eth0
message+     790  0.0  0.1   8792  4744 ?        Ss   14:23   0:00 @dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
  └─(Caps) 0x0000000020000000=cap_audit_write
root         795  0.0  0.0  82832  3828 ?        Ssl  14:23   0:00 /usr/sbin/irqbalance --foreground
root         796  0.0  0.4  32636 18964 ?        Ss   14:23   0:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root         800  0.0  0.1 234504  6716 ?        Ssl  14:23   0:00 /usr/libexec/polkitd --no-debug
syslog       802  0.0  0.1 222404  6144 ?        Ssl  14:23   0:00 /usr/sbin/rsyslogd -n -iNONE
root         805  0.1  0.8 1321184 32056 ?       Ssl  14:23   0:06 /usr/lib/snapd/snapd
root         806  0.0  0.1  15548  7488 ?        Ss   14:23   0:00 /lib/systemd/systemd-logind
root         808  0.0  0.3 392500 12408 ?        Ssl  14:23   0:00 /usr/libexec/udisks2/udisksd
root         824  0.0  0.9 165848 39348 ?        SNsl 14:23   0:02 /opt/osquery/bin/osqueryd --flagfile /etc/osquery/osquery.flags --config_path /etc/osquery/osquery.conf
root         957  0.0  1.0 929220 43496 ?        SNl  14:23   0:01  _ /opt/osquery/bin/osqueryd
root         850  0.0  0.3 244228 12268 ?        Ssl  14:23   0:00 /usr/sbin/ModemManager
root        1161  0.0  0.0   6896  3004 ?        Ss   14:23   0:00 /usr/sbin/cron -f -P
root        1162  0.0  0.7 229220 31760 ?        Ss   14:23   0:00 php-fpm: master process (/etc/php/8.1/fpm/php-fpm.conf)
www-data    1301  0.0  0.2 229748 11780 ?        S    14:23   0:00  _ php-fpm: pool www
www-data    1302  0.0  0.2 229748 11780 ?        S    14:23   0:00  _ php-fpm: pool www
tcpdump     1170  0.0  0.1  16476  7904 ?        Ss   14:23   0:00 /usr/bin/tcpdump -i ens160 -C 100 -G 86400 -w /tmp/tcp_dump_2025-09-27-14:23:46.pcap -z gzip -s 0
root        1171  0.2  1.7 5999836 69484 ?       Ssl  14:23   0:14 /usr/local/bin/velociraptor -c /root/client.config.yaml client -v
root        1173  0.0  1.1 6071584 46588 ?       Ssl  14:23   0:01 /usr/local/bin/velociraptor -c /root/server.config.yaml frontend -v
root        1340  1.2  1.9 6073824 77300 ?       Sl   14:23   1:01  _ /usr/local/bin/velociraptor -c /root/server.config.yaml frontend -v
john        1641  0.0  0.1  17220  7868 ?        S    14:38   0:00      _ sshd: john@pts/0
john        1649  0.0  0.1   8816  5612 pts/0    Ss   14:38   0:00          _ -bash
john        2258  0.3  0.1   9832  6368 pts/0    S+   15:47   0:00              _ bash linpeas.sh -a -N
john        5467  0.0  0.1   9832  4372 pts/0    S+   15:48   0:00                  _ bash linpeas.sh -a -N
john        5470  0.0  0.0  10408  3724 pts/0    R+   15:48   0:00                  |   _ ps fauxwww
john        5471  0.0  0.0   9832  2868 pts/0    S+   15:48   0:00                  _ bash linpeas.sh -a -N
root        1262  0.1  0.7 203060 30208 ?        S    14:23   0:06 /opt/sysmon/sysmon -i /opt/sysmon/config.xml -service
root        1267  0.0  0.0   6176  1076 tty1     Ss+  14:23   0:00 /sbin/agetty -o -p -- u --noclear tty1 linux
root        1279  0.0  0.0  55228  1744 ?        Ss   14:23   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
www-data    1280  0.0  0.1  55856  5560 ?        S    14:23   0:00  _ nginx: worker process
www-data    1281  0.0  0.1  55856  5560 ?        S    14:23   0:00  _ nginx: worker process
root        1298  0.0  0.8 233080 34244 ?        Ss   14:23   0:00 /usr/sbin/apache2 -k start
www-data    1332  0.0  1.3 310364 53564 ?        S    14:23   0:01  _ /usr/sbin/apache2 -k start
www-data    1334  0.0  1.3 238560 55732 ?        S    14:23   0:00  _ /usr/sbin/apache2 -k start
www-data    1335  0.0  1.2 312376 51900 ?        S    14:23   0:00  _ /usr/sbin/apache2 -k start
www-data    1337  0.0  1.3 238580 52428 ?        S    14:23   0:00  _ /usr/sbin/apache2 -k start
www-data    1338  0.0  0.4 233732 19368 ?        S    14:23   0:00  _ /usr/sbin/apache2 -k start
www-data    1536  0.0  1.2 234532 48112 ?        S    14:25   0:00  _ /usr/sbin/apache2 -k start
mysql       1304  0.7 10.2 1784944 411040 ?      Ssl  14:23   0:37 /usr/sbin/mysqld
proftpd     1333  0.0  0.0  18184  3572 ?        Ss   14:23   0:00 proftpd: (accepting connections)
root        1336  0.5  1.2 545412 49548 ?        Ssl  14:23   0:28 /usr/bin/suricata -c /etc/suricata/suricata.yaml --pidfile /var/run/suricata.pid --af-packet -D -vvv
john        1554  0.0  0.2  17208  9708 ?        Ss   14:38   0:00 /lib/systemd/systemd --user
john        1555  0.0  0.1 170628  5164 ?        S    14:38   0:00  _ (sd-pam)
john        5267  0.0  0.1   8300  4220 ?        Ss   15:48   0:00  _ /usr/bin/dbus-daemon --session --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only
root        1986  0.0  0.8 391524 32228 ?        Ssl  15:11   0:00 /usr/libexec/fwupd/fwupd
root        1991  0.0  0.2 239500  8224 ?        Ssl  15:11   0:00 /usr/libexec/upowerd

╔══════════╣ Processes with unusual configurations

╔══════════╣ Processes with credentials in memory (root req)
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#credentials-from-process-memory
gdm-password Not Found
gnome-keyring-daemon Not Found
lightdm Not Found
vsftpd Not Found
apache2 process found (dump creds from memory as root)
sshd: process found (dump creds from memory as root)
mysql process found (dump creds from memory as root)
postgres Not Found
redis-server Not Found
mongod Not Found
memcached Not Found
elasticsearch Not Found
jenkins Not Found
tomcat Not Found
nginx process found (dump creds from memory as root)
php-fpm process found (dump creds from memory as root)
supervisord Not Found
vncserver Not Found
xrdp Not Found
teamviewer Not Found

╔══════════╣ Opened Files by processes
Process 1554 (john) - /lib/systemd/systemd --user 
  └─ Has open files:
    └─ /proc/1554/mountinfo
    └─ /proc/swaps
    └─ /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service
Process 1649 (john) - -bash 
  └─ Has open files:
    └─ /dev/pts/0

╔══════════╣ Processes with memory-mapped credential files

╔══════════╣ Processes whose PPID belongs to a different user (not root)
╚ You will know if a user can somehow spawn processes as a different user

╔══════════╣ Files opened by processes belonging to other users
╚ This is usually empty because of the lack of privileges to read other user processes information

╔══════════╣ Different processes executed during 1 min (interesting is low number of repetitions)
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#frequent-cron-jobs
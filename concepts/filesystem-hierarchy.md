# Filesystem Hierarchy

### The Root Filesystem

| Directory | Description                                       |
| --------- | ------------------------------------------------- |
| /bin      | Essential command binaries                        |
| /boot     | Static files of the boot loader                   |
| /dev      | Device files                                      |
| /etc      | Host-specific system configuration                |
| /lib      | Essential shared libraries and kernel modules     |
| /media    | Mount point for removable media                   |
| /mnt      | Mount point for mounting a filesystem temporarily |
| /opt      | Add-on application software packages              |
| /run      | Data relevant to running processes                |
| /sbin     | Essential system binaries                         |
| /srv      | Data for services provided by this system         |
| /tmp      | Temporary files                                   |
| /usr      | Secondary hierarchy                               |
| /var      | Variable data                                     |
| /home     | User home directories                             |
| /root     | Home directory for the root user                  |
| /proc     | Kernel and process information virtual filesystem |
| /sys      | Kernel and system information virtual filesystem  |



### /etc : Host-specific system configuration

| Directory or File               | Description                                            |
| ------------------------------- | ------------------------------------------------------ |
| /etc/sysconfig/network-scripts/ | 存放网卡配置文件                                               |
| /etc/profile.d                  | 用户登录后执行的脚本所在的目录                                        |
| /etc/init.d                     | 软件启动程序所在的目录（CentOS7废弃了，统一由systemctl取代）                 |
| /etc/fstab                      | 存放开机自动挂载文件系统的文件                                        |
| /etc/rc.local                   | 存放开机自启动程序命令的文件                                         |
| /etc/resolv                     | DNS客户端的配置文件                                            |
| /etc/hostname                   | 主机名的配置文件                                               |
| /etc/hosts                      | 本地的DNS配置文件                                             |
| /etc/vimrc                      | 存放vim的配置文件                                             |
| /etc/issue & /etc/issue.net     | 配置用户登录终端前显示信息的文件（在企业服务器中，为了防止泄露服务器版本，一般会把issue文件的内容清空） |
| /etc/motd                       | 配置用户登录系统之后显示提示内容的文件（登录提示）                              |
| /etc/os-release                 | 声明 Red Hat 版本号和名称信息的文件                                 |
| /etc/redhat-release             | 查看系统版本                                                 |
| /etc/sysctl.conf                | 内核参数设置文件，主要用于内核的配置和优化                                  |



### /usr : Secondary hierarchy

| Directory   | Description   |
| ----------- | ------------- |
| /usr/local/ | 编译安装软件默认的位置路径 |
| /usr/src/   | 存放源码文件的目录     |



### /var : /var/log/

| File                | Description                   |
| ------------------- | ----------------------------- |
| /var/log/messages\* | Linux系统级别日志文件                 |
| /var/log/secure     | 用户登录信息日志文件（安全日志文件）            |
| /var/log/dmesg      | 记录硬件信息加载情况的日志                 |
| /var/log/cron       | 定时任务日志文件                      |
| /var/log/wtmp       | 记录登陆者信息的文件，使用last命令自动读取该文件    |
| /var/log/lastlog    | 记录用户近期登陆情况，使用lastlog命令自动读取该文件 |



### /proc : Kernel and process information virtual filesystem

| File             | Description  |
| ---------------- | ------------ |
| /proc/meminfo    | 查看内存信息       |
| /proc/cpuinfo    | 查看CPU信息      |
| /proc/loadavg    | 查看负载信息       |
| /proc/mounts     | 当前设备挂载列表信息文件 |
| /proc/interrupts | 当前系统中断信息文件   |


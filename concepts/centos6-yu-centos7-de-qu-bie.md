# CentOS6 与 CentOS7的区别

|            | CentOS 6                                             | CentOS 7                                                                     |
| ---------- | ---------------------------------------------------- | ---------------------------------------------------------------------------- |
| 架构         | 32/64 位                                              | 64 位                                                                         |
| 启动原理       | 串行 (sysvint)                                         | 并行 (systemd)                                                                 |
| 安装过程       | 顺序一步步安装模式                                            | 平台化安装模式                                                                      |
| 网卡名称       | eth0                                                 | en01 / ens33 / enp0s3                                                        |
| 默认文件系统     | ext4                                                 | xfs                                                                          |
| 网卡管理服务     | network                                              | NetworkManager / network                                                     |
| 网络配置       | setup                                                | nmtui                                                                        |
|            |                                                      |                                                                              |
| 服务管理       | <p>/etc/init.d/SERVICE start</p><p>stop  restart</p> | <p>systemctl start SERVICE</p><p>stop  restart</p>                           |
| 平滑加载服务     | /etc/init.d/SERVICE reload                           | systemctl reload SERVICE                                                     |
| 查看服务状态     | /etc/init.d/SERVICE status                           | systemctl status SERVICE                                                     |
| 设置服务开机自启动  | <p>chkconfig SERVICE on</p><p>off</p>                | <p>systemctl enable SERVICE</p><p>disable</p>                                |
| 查看服务开机启动状态 | chkconfig --list SERVICE                             | systemctl status SERVICE                                                     |
| 查看服务开机启动列表 | chkconfig --list \| grep "3:on"                      | systemctl list-unit-files                                                    |
| 开机自启动文件    | /etc/init.d/rc.d/SERVICE                             | /usr/lib/systemd/system/SERVICE                                              |
|            |                                                      |                                                                              |
| tab 补全     | 命令 路径                                                | 命令 路径 参数                                                                     |
| 默认防火墙      | iptables                                             | firewalld                                                                    |
| 主机名修改      | hostname HOSTNAME                                    | hostnamectl set-hostname HOSTNAME                                            |
| 主机名文件      | /etc/sysconfig/network                               | /etc/hostname                                                                |
| 字符集文件      | /etc/sysconfig/i18n                                  | /etc/locale.conf                                                             |
| 字符集命令配置    | export LANG=                                         | <p>export LANG="en_US.UTF-8"</p><p>localectl set-locale LANG=en_US.UTF-8</p> |
| 运行级别       | 0-6  runlevel / init                                 | /usr/lib/systemd/system/runlevel\*.target                                    |
| 默认数据库      | MySQL                                                | MariaDB                                                                      |
| IO 调度      | cfq                                                  | deadline                                                                     |


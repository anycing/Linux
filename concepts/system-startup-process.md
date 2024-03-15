# System Startup Process

### CentOS 6

{% hint style="info" %}
开机 BIOS 自检
{% endhint %}

{% hint style="info" %}
读取 MBR 引导
{% endhint %}

{% hint style="info" %}
加载 GRUB 菜单
{% endhint %}

{% hint style="info" %}
加载内核
{% endhint %}

{% hint style="info" %}
运行 INIT 进程
{% endhint %}

| 读取                 | 用于                         |
| ------------------ | -------------------------- |
| /etc/inittab       | 设定系统运行级别                   |
| /etc/init/rcS.conf | 执行 /etc/rc.d/rc.sysinit 脚本 |
| /etc/init/rc.conf  | 执行 /etc/rc.d/rc 3脚本        |
| /etc/rc.local      | 加载用户开机自启动程序                |
| /etc/init/tty.conf | 启动 mingetty 3 进程           |

###

### CentOS 7

{% hint style="info" %}
开机 BIOS 自检
{% endhint %}

{% hint style="info" %}
读取 MBR 引导
{% endhint %}

{% hint style="info" %}
加载 GRUB 菜单
{% endhint %}

{% hint style="info" %}
加载内核
{% endhint %}

{% hint style="info" %}
运行 systemd 进程
{% endhint %}

| 读取             | 用于                |
| -------------- | ----------------- |
| initrd.target  | 包含挂载 fstab 中的文件系统 |
| default.target | 设定 target 模式及加载脚本 |
| sysinit.target | 初始化系统及加载basic     |
| /etc/rc.local  | 加载用户开机自启动程序       |
| getty.target   | 启动 mingetty 进程    |


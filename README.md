# OpenCore on Dell OptiPlex 7080 MFF

> 1、OpenCore版本： 0.99 常规升级
>
> 2、MacOS： Ventura (13.6.6) on Dell OptiPlex 7080 MFF
>
> 2、使用本分支里的EFI首先 要生成自己的五码，我已经EFI里的改了，为了不和我现在使用的序列号冲突
>
> 3、目前仿冒的mac机型我选择是: iMac 20,1
>
> 4、~~bios的主板版本我已经1.1.5 升级到 1.20.1 版本【官网最新驱动时间：2023-06-23 】，为了散热使用 Macs fan control 软件，将风扇转速2700转，噪音和散热稍微平衡些。~~  注释掉了，因为dell 放在公司已经使用 Macs Fan Control软件控制设置全速转了【3300转】，使得cpu的温度即使高负载 温度在60℃左右。为了不降频，已经忽略了风扇的吵闹。
>
> 

‼️‼️‼️‼️‼️‼️：【**BCM94360CS2的白果卡，导致电脑频繁死机，页面卡死的问题**】

```
上周帮同事搞了一个与我同配置的小主机，朋友的电脑会频繁死机，只能强制电源键重启。想起来是网卡的针脚屏蔽的问题。
之前忘记写了，我本机使用BCM94360CS2+反向转接卡，实在转接卡上一面上，做了指针屏蔽，参照之前远景论坛上一个回复【https://bbs.pcbeta.com/viewthread-1846508-1-1.html】 
屏蔽的转接卡上的11、12 的针脚。
```



## 硬件配置详情

-   Dell OptiPlex 7080 MFF 【标压版本，180W电源】
-   CPU: Intel Comet Lake i7-10700 【标压】
-   主板型号: Intel Q470
-   内存条: 16G DDR4 3200 \* 2
-   iGPU: UHD 630
-   硬盘:  固态- S690MQ 【2023-11-03 原因：主控芯片群联，兼容想好一下，速度有提升】
-   声卡: ALC256
-   千兆有线网卡: Intel I219-LM7
-   无线网卡  BCM94360CS2+反向转接卡
### @界面效果

1、关于黑苹果的信息
![dell.png](./images/dell.png)

### @ 常工作的硬件功能 (使用 BCM94360CS2白果卡)

-   HWP
-   睡眠、隔空、随行等正常使用
-   iGPU with HiDPI
-   千兆有线网卡
-   wifi无线网正常使用
-   蓝牙
-   喇叭声音正常
-   DP/HDMI 声音正常使用
-   额外在机器后边的扩展口 添加了 type-c口的视频转接口(咸鱼淘的)，实现了type-c的视频转接，支持4K@60hz。

### @ 注意事项

1、建议将系统的bios版本升级到 1.20.1 版本，风扇可以智能的根据cpu温度自动调速了。

2、使用了BCM94360cs2 免驱网卡， dell 外置天线连接网卡的触点槽不要安装反了（暂时无图，以后会补），安装反了，导致 连接wifi2.4G情况下，使用苹果的触控板，触控板的光标会发虚，光标在屏幕内发飘，或者光标卡顿，看了网上说的是蓝牙和2.4G冲突了，连接wifi选5G 就没事，暂时手头无线网不支持5G的频段，没测试；**我更换了网卡上的连接天线的触点位置，wifi正常、妙控板正常**。

## BIOS主板设置【dell主机必须进行cfg-lock和DVMT的解锁，我手里的这台标压版本的bios版本不解锁 无法进入引导，！！！工具已经提供，使用前必须解锁】

| Settings                                        |Value|
|-------------------------------------------------|---|
| System Configuration → Integrated NIC           | Enabled |
| System Configuration → SATA Operation           | AHCI |
| Security → TPM 2.0  （必须关掉，不然影响睡眠，导致睡眠秒醒）       | Disabled |
| Secure Boot → Secure Boot Enable                | Disabled |
| Secure Boot → Secure Boot Mode                  | Audit Mode |
| Intel SGE → SGX                                 | Disabled |
| Performance → Intel SpeedStep                   | Enabled |
| Performance → C-States Control                  | Enabled |
| Performance → Turboost                          | Enabled |
| Performance → HyperThread Control               | Enabled |
| Power Management → Intel Speed Shift Technology | Enabled |
| Power Management → Deep Sleep Control           | Disabled |
| Power Management → USB Wake Support             | Disabled |
| Power Management → Wake on LAN/WLAN             | Lan only |
| Power Management → Block Sleep                  | Disabled |
| POST Behavior → Fastboot                        | Minimal |
| Virtualization Support → Virtualization         | Enabled |
| Virtualization Support → VT For Direct I/O      | Disabled |
| Advanced configurations → ASPM                  | Auto |

## 修改DVMT和CFG LOCK的值 提供参考，
* 无法使用Grub Setup_var 需要用到Ru.efi 将Ru.efi在BIOS中添加进Boot Menus 后启动 进入Ru后按 "Alt" + "=" 并
  查找 **CPUSetup** 和 **SaSetup**
* 解锁"CFG-LOCK" 找到CPUSetup 将横排 "0030" "0E" 位改为 00 按 Ctrl + W 保存
* 修改DVMT 搜索 SaSetup 将横排 "00F0" "05" 位改为 "02" 按 Ctrl + W 保存

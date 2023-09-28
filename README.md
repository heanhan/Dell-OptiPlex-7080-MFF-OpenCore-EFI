# OpenCore on Dell OptiPlex 7080 MFF

> 1、OpenCore版本： 0.95 
>
> 2、MacOS： Ventura (13.6) on Dell OptiPlex 7080 MFF
>
> 2、使用本分支里的EFI首先 要生成自己的五码，我已经EFI里的改了，为了不和我现在使用的序列号冲突
>
> 3、目前仿冒的mac机型我选择是: iMac20,1
>
> 4、bios的主板版本我已经1.1.5 升级到 1.2.1 版本，解决了7080mff小主机的风扇忽大忽小的问题。最新版本1.2.1 的bios,目前能做到稍微智能点，工作40——60摄氏度之间的转速自动维持在1800～2800左右了，一天几乎都很安静了，持续观察中。。。

## 硬件配置详情

-   Dell OptiPlex 7080 MFF 【标压版本，180W电源】
-   CPU: Intel Comet Lake i7-10700 【标压】
-   主板型号: Intel Q470
-   内存条: 16G DDR4 3200 \* 2
-   iGPU: UHD 630
-   硬盘: 致钛Tiplus5000 512GB
-   声卡: ALC256
-   千兆有线网卡: Intel I219-LM7
-   无线网卡  BCM94360CS2 M.2

## 黑苹果的效果

### @界面效果

1、关于本机
![dell.png](./images/dell.png)

### @ 常工作的硬件功能 (使用 BCM94360CS2白果卡)

-   HWP
-   睡眠【当前版本已经修复正常】、隔空、随行等正常使用
-   iGPU with HiDPI
-   千兆有线网卡
-   wifi无线网正常使用
-   蓝牙
-   喇叭声音正常
-   DP/HDMI 声音正常使用
-   额外在机器后边的扩展口 添加了 type-c口的视频转接口(咸鱼淘的)，实现了type-c的视频转接，支持4K@60hz。

### @ 注意事项

1、建议将系统的bios版本升级到 1.2.1 版本，风扇可以智能的根据cpu温度自动调速了。

2、使用了BCM94360cs2 免驱网卡， dell 外置天线连接网卡的触点槽不要安装反了（暂时无图，以后会补），安装反了，导致 连接wifi2.4G情况下，使用苹果的触控板，触控板的光标会发虚，光标在屏幕内发飘，或者光标卡顿，看了网上说的是蓝牙和2.4G冲突了，连接wifi选5G 就没事，暂时手头无线网不支持5G的频段，没测试；**我更换了网卡上的连接天线的触点位置，wifi正常、妙控板正常**。

## BIOS主板设置【dell主机必须进行cfg-lock和DVMT的解锁，我手里的这台标压版本的bios版本不解锁 无法进入引导，！！！工具已经提供，使用前必须解锁】

| Settings                                        |Value|
|-------------------------------------------------|---|
| System Configuration → Integrated NIC           | Enabled |
| System Configuration → SATA Operation           | AHCI |
| Security → TPM 2.0  （必须关掉，不然影响睡眠）               | Disabled |
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

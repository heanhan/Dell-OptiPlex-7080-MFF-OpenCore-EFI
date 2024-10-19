# OpenCore on Dell OptiPlex 7080 MFF

> 1、OpenCore版本： 1.0.2 常规升级，支持MacOS Sequoia正式系统【重要！！！：本次添加了一个博通的蓝牙驱动，使得电脑连接蓝牙设备更稳定，尤其使使用苹果的原装键盘和触控板，指针发飘乱窜的问题。】
>
> 2、MacOS： Sequoia (15.0.1) on Dell Option 7080 MFF【从ventura 直接升级到macOS 15 正式版，目前各功能正常】
>
> ```
> 目前使用下来 macOS 15 还挺流畅。没有遇到坑。
> 使用本efi 准备工作：
> 1、efi中 OC->Misc->Security 中 SecureBootModel 属性设置  Disable
> 2、关闭系统的 SIP ,即通过csrutil status 为 disable
> 
> 关于修复免去博通网卡的 wifi 需要通过  OpenCore-Patcher 这个软件将苹果删除的驱动安装进去
> ```
>
> 2、使用本分支里的EFI首先 要生成自己的五码，我已经EFI里的改了，为了不和我现在使用的序列号冲突
>
> 3、目前仿冒的mac机型我选择是: iMac 20,1
>
> 4、~~bios的主板版本我已经1.1.5 升级到 1.20.1 版本【官网最新驱动时间：2023-06-23 】，为了散热使用 Macs fan control 软件，将风扇转速2700转，噪音和散热稍微平衡些。~~  注释掉了，因为dell 放在公司已经使用 Macs Fan Control软件控制设置全速转了【3300转】，使得cpu的温度即使高负载 温度在60℃左右。为了不降频，已经忽略了风扇的吵闹。
>
> 备注：电脑内存尽量选择2666的，如果选择 3200频率的内存，尽量选大厂的，之前给朋友装的就是遇到内存频率导致无故的卡屏、死机、重启的问题，最后排查是内存条的问题
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

2、使用了BCM94360cs2 免驱网卡， dell 外置天线连接网卡的触点槽不要安装反了，，安装反了，导致 连接wifi2.4G情况下，使用苹果的触控板，触控板的光标会发虚，光标在屏幕内发飘，或者光标卡顿,网络连接wifi优先选5G频段，减少2.4GHz与5GHz的频段冲突；**我更换了网卡上的连接天线的触点位置，wifi正常、妙控板正常**。


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

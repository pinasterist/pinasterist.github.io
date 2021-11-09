---
layout:     post
title:      "磁盘Smart信息"
subtitle:   "如何查看Smart信息，如何查看移动硬盘的Smart信息"
date:       2021-11-09
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - terminal
    - hdd
    - smart
    - MacOS
---

磁盘Smart特性可以搜集磁盘的健康信息，包括运行时间、磁头启停次数等等。

Linux中查看比较简单，只需安装smartmontools这个工具即可：

```
sudo apt install smartmontools
```

MacOS中，使用brew安装smartmontools，也可以达到同样效果：

```
brew install smartmontools
```

安装好后，只需在终端中执行下面的命令，就可以看到所有的smart信息：

```
smartctl -a /dev/disk4
smartctl 7.2 2020-12-30 r5155 [Darwin 21.1.0 arm64] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Device Model:     TOSHIBA MQ04UBB400
Serial Number:    814DT1RAT
LU WWN Device Id: 0 000000 000000000
Firmware Version: JS0B0U
User Capacity:    4,000,787,030,016 bytes [4.00 TB]
Sector Sizes:     512 bytes logical, 4096 bytes physical
Rotation Rate:    5400 rpm
Form Factor:      2.5 inches
Zoned Device:     Device managed zones
Device is:        Not in smartctl database [for details use: -P showall]
ATA Version is:   ACS-3 T13/2161-D revision 5
SATA Version is:  SATA 3.3, 3.0 Gb/s (current: 3.0 Gb/s)
Local Time is:    Mon Nov  8 22:33:23 2021 CST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled

=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

General SMART Values:
Offline data collection status:  (0x00) Offline data collection activity
                                        was never started.
                                        Auto Offline Data Collection: Disabled.
Self-test execution status:      (   0) The previous self-test routine completed
                                        without error or no self-test has ever
                                        been run.
Total time to complete Offline
data collection:                (  120) seconds.
Offline data collection
capabilities:                    (0x5b) SMART execute Offline immediate.
                                        Auto Offline data collection on/off support.
                                        Suspend Offline collection upon new
                                        command.
                                        Offline surface scan supported.
                                        Self-test supported.
                                        No Conveyance Self-test supported.
                                        Selective Self-test supported.
SMART capabilities:            (0x0003) Saves SMART data before entering
                                        power-saving mode.
                                        Supports SMART auto save timer.
Error logging capability:        (0x01) Error logging supported.
                                        General Purpose Logging supported.
Short self-test routine
recommended polling time:        (   2) minutes.
Extended self-test routine
recommended polling time:        ( 664) minutes.
SCT capabilities:              (0x003d) SCT Status supported.
                                        SCT Error Recovery Control supported.
                                        SCT Feature Control supported.
                                        SCT Data Table supported.

SMART Attributes Data Structure revision number: 16
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x000b   100   100   050    Pre-fail  Always       -       0
  2 Throughput_Performance  0x0005   100   100   050    Pre-fail  Offline      -       0
  3 Spin_Up_Time            0x0027   100   100   001    Pre-fail  Always       -       5081
  4 Start_Stop_Count        0x0032   100   100   000    Old_age   Always       -       48
  5 Reallocated_Sector_Ct   0x0033   100   100   050    Pre-fail  Always       -       0
  7 Seek_Error_Rate         0x000b   100   100   050    Pre-fail  Always       -       0
  8 Seek_Time_Performance   0x0005   100   100   050    Pre-fail  Offline      -       0
  9 Power_On_Hours          0x0032   100   100   000    Old_age   Always       -       4
 10 Spin_Retry_Count        0x0033   100   100   030    Pre-fail  Always       -       0
 12 Power_Cycle_Count       0x0032   100   100   000    Old_age   Always       -       15
191 G-Sense_Error_Rate      0x0032   100   100   000    Old_age   Always       -       0
192 Power-Off_Retract_Count 0x0032   100   100   000    Old_age   Always       -       4
193 Load_Cycle_Count        0x0032   100   100   000    Old_age   Always       -       86
194 Temperature_Celsius     0x0022   100   100   000    Old_age   Always       -       29 (Min/Max 9/42)
196 Reallocated_Event_Count 0x0032   100   100   000    Old_age   Always       -       0
197 Current_Pending_Sector  0x0032   100   100   000    Old_age   Always       -       0
198 Offline_Uncorrectable   0x0030   100   100   000    Old_age   Offline      -       0
199 UDMA_CRC_Error_Count    0x0032   200   200   000    Old_age   Always       -       0
220 Disk_Shift              0x0002   100   100   000    Old_age   Always       -       0
222 Loaded_Hours            0x0032   100   100   000    Old_age   Always       -       1
223 Load_Retry_Count        0x0032   100   100   000    Old_age   Always       -       0
224 Load_Friction           0x0022   100   100   000    Old_age   Always       -       0
226 Load-in_Time            0x0026   100   100   000    Old_age   Always       -       283
240 Head_Flying_Hours       0x0001   100   100   001    Pre-fail  Offline      -       0

SMART Error Log Version: 1
No Errors Logged

SMART Self-test log structure revision number 1
No self-tests have been logged.  [To run self-tests, use: smartctl -t]

SMART Selective self-test log data structure revision number 1
 SPAN  MIN_LBA  MAX_LBA  CURRENT_TEST_STATUS
    1        0        0  Not_testing
    2        0        0  Not_testing
    3        0        0  Not_testing
    4        0        0  Not_testing
    5        0        0  Not_testing
Selective self-test flags (0x0):
  After scanning selected spans, do NOT read-scan remainder of disk.
If Selective self-test is pending on power-up, resume after 0 minute delay.
```

但是，MacOS中使用smartmontools只能查看本地磁盘的信息，移动硬盘是无效的，只会得到下面的回执：

```
smartctl 7.2 2020-12-30 r5155 [Darwin 21.1.0 arm64] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

Smartctl open device: /dev/disk5 failed: Operation not supported by device
```

原因是出在USB中。原来smartmontools通过发送ATA命令获取磁盘信息。但是移动硬盘并非直连主板，而是中间插了个USB，导致ATA命令无法发送。解决方案自然是想办法将ATA命令通过USB直接发送过去（pass-through）。网络上有个开源的MacOS驱动就是这么干的。点击<https://github.com/kasbert/OS-X-SAT-SMART-Driver>查看更详细信息。自己编译安装略显麻烦，好在已经有人帮忙做了个带签名的驱动<https://binaryfruit.com/drivedx/usb-drive-support>。按照说明安装后，smartmontools就可以愉快滴在MacOS中执行了。

下面的表格是smart部分字段的解释：

| ID# | ATTRIBUTE_NAME          |                                                |
| --- | ----------------------- | ---------------------------------------------- |
|   1 | Raw_Read_Error_Rate     | 读取错误率                                     |
|   2 | Throughput_Performance  | 读写通量性能                                   |
|   3 | Spin_Up_Time            | 起转时间                                       |
|   4 | Start_Stop_Count        | 启动停止次数                                   |
|   5 | Reallocated_Sector_Ct   | 重新分配扇区计数                               |
|   7 | Seek_Error_Rate         | 寻道错误率                                     |
|   8 | Seek Time Performance   | 寻道性能                                       |
|   9 | Power_On_Hours          | 通电时间                                       |
|  10 | Spin_Retry_Count        | 起转重试次数                                   |
|  11 | Calibration_Retry_Count | 重新校准重试次数                               |
|  12 | Power_Cycle_Count       | 启动<->关闭循环次数                            |
|  13 | Soft Read Error Rate    | 软件读取错误率                                 |
| 180 | Unused Reserved Block Count | Total未用的备用块计数（惠普）              |
| 183 | SATA Downshift Error Count  | 串口降速错误计数                           |
| 184 | End to End Error Detection Count  | 端到端错误检测次数                   |
| 187 | Reported Uncorrectable Errors | 无法校正的错误                           |
| 188 | Command Timeout         | 通信超时                                       |
| 189 | High Fly Writes         | 高飞写入                                       |
| 190 | Airflow Temperature     | 气流温度                                       |
| 191 | G-sense Error Rate      | 冲击错误率                                     |
| 192 | Power-Off_Retract_Count | 断电磁头缩回计数                               |
| 193 | Load_Cycle_Count        | 磁头加载/卸载循环计数                          |
| 194 | Temperature_Celsius     | 温度                                           |
| 195 | Hardware ECC Recovered  | ECC矫正次数                                    |
| 196 | Reallocated_Event_Count | 在分配扇区物理位置事件计数（与坏道无关）       |
| 197 | Current_Pending_Sector  | 当前等待中扇区数（状态存疑/不稳定-等待后续判断 |
| 198 | Offline_Uncorrectable   | 无法修正的扇区总数                             |
| 199 | UDMA_CRC_Error_Count    | UltraDMA CRC错误计数                           |
| 200 | Multi_Zone_Error_Rate   | 写入错误率                                     |
| 206 | Flying Height           | 磁头飞行高度                                   |
| 207 | Spin High Current       | 主轴过电流                                     |
| 208 | Spin Buzz               | 主轴电机重启次数                               |
| 220 | Disk Shift              | 盘片偏移量                                     |
| 241 | Total LBAs Written      | LBA写入总数                                    |
| 242 | Total LBAs Read         | LBA读取总数                                    |
| 254 | Free Fall Protection    | 自由坠落保护                                   |

这里是字段的详细释义：

* 01h(001) Raw Read Error Rate 底层数据读取错误率

数据为0或任意值，当前值应远大于与临界值。

底层数据读取错误率是磁头从磁盘表面读取数据时出现的错误，对某些硬盘来说，大于0的数据表明磁盘表面或者读写磁头发生问题，如介质损伤、磁头污染、磁头共振等等。不过对希捷硬盘来说，许多硬盘的这一项会有很大的数据量，这不代表有任何问题，主要是看当前值下降的程度。

在固态硬盘中，此项的数据值包含了可校正的错误与不可校正的RAISE错误（UECC＋URAISE）。

* 02h(002) Throughput Performance 读写通量性能

此参数表示硬盘的读写通量性能，数据值越大越好。当前值如果偏低或趋近临界值，表示硬盘存在严重的问题，但现在的硬盘通常显示数据值为0或根本不显示此项，一般在进行了人工脱机SMART测试后才会有数据量。
03h(003) Spin-Up Time 硬盘主轴起转时间

主轴起旋时间就是主轴电机从启动至达到额定转速所用的时间，数据值直接显示时间，单位为毫秒或者秒，因此数据值越小越好。不过对于正常硬盘来说，这一项仅仅是一个参考值，硬盘每次的启动时间都不相同，某次启动的稍慢些也不表示就有问题。 

硬盘的主轴电机从启动至达到额定转速大致需要4秒～15秒左右，过长的启动时间说明电机驱动电路或者轴承机构有问题。一旦这一参数的数据值在某些型号的硬盘上总是为0，这就要看当前值和最差值来判断了。  

对于固态硬盘来说，所有的数据都是保存在半导体集成电路中，没有主轴电机，所以这项没有意义，数据固定为0，当前值固定为100。

* 04h(004) Start/Stop Count 马达启停次数(又称加电次数)

这一参数的数据是累计值，表示硬盘主轴电机启动/停止的次数，新硬盘通常只有几次，以后会逐渐增加。系统的某些功能如空闲时关闭硬盘等会使硬盘启动/停止的次数大为增加，在排除定时功能的影响下，过高的启动/停止次数（远大于通电次数0C）暗示硬盘电机及其驱动电路可能有问题。  

这个参数的当前值是依据某种公式计算的结果，例如对希捷某硬盘来说临界值为20，当前值是通过公式“100－（启停计数/1024）”计算得出的。若新硬盘的启停计数为0，当前值为100－(0/1024)＝100，随着启停次数的增加，该值不断下降，当启停次数达到81920次时，当前值为100－(81920/1024)＝20，已达到临界值，表示从启停次数来看，该硬盘已达设计寿命，当然这只是个寿命参考值，并不具有确定的指标性。  

这一项对于固态硬盘同样没有意义，数据固定为0，当前值固定为100。

* 05h(005) Relocated Sector Count 重映射扇区数（Glist）

数据应为0，当前值应远大于临界值。

这项参数的数据值直接表示已经被重映射扇区的数量，当前值则随着数据值的增加而持续下降。当发现此项的数据值不为零时，要密切注意其发展趋势，若能长期保持稳定，则硬盘还可以正常运行；若数值不断上升，说明不良扇区不断增加，硬盘已处于不稳定状态，应当考虑更换了。如果当前值接近或已到达临界值（此时的数据值并不一定很大，因为不同硬盘保留的备用扇区数并不相同），表示缺陷表已满或备用扇区已用尽，已经失去了重映射功能，再出现不良扇区就会显现出来并直接导致数据丢失。  

这一项不仅是硬盘的寿命关键参数，而且重映射扇区的数量也直接影响硬盘的性能，例如某些硬盘会出现数据量很大，但当前值下降不明显的情况，这种硬盘尽管还可正常运行，但也不宜继续使用。因为备用扇区都是位于磁盘尾部（靠近盘片轴心处），大量的使用备用扇区会使寻道时间增加，硬盘性能明显下降。

* 07h(007) Seek Error Rate 寻道错误率

数据应为0或者远远大于0，当前值应远大于与临界值。  

这一项表示磁头寻道时的错误率，有众多因素可导致寻道错误率上升，如磁头组件的机械系统、伺服电路有局部问题，盘片表面介质不良，硬盘温度过高等。

如果这里接近阈值，就说明硬盘马上就要挂了。

* 08h(008) Seek Time Performance 寻道性能

此项表示硬盘寻道操作的平均性能（寻道速度），通常与前一项（寻道错误率）相关联。当前值持续下降标志着磁头组件、寻道电机或伺服电路出现问题，但现在许多硬盘并不显示这一项。

* 09h(009) Power-On Hours硬盘加电时间

此参数表示硬盘自出厂以来加电启动的统计时间，单位为小时。

* 0Ah(010) Spin up Retry Count 马达起转重试次数(即硬盘启动重试次数)

数据应为0，当前值应大于临界值。

主轴电机在一次加速至正常速度失败后尝试重新继续加速到正常运行速度的统计数；这个数值的增加说明电机驱动或者机械子系统出现问题，也可能是整机供电不足。

* 0Bh(011) Calibration Retry Count 磁头校准重试次数

数据应为0，当前值应远大于与临界值。

磁头在一次运行失败时尝试校准至正常状态的统计数，这个数值的增加表示点击驱动电路或者机械子系统出现问题。

* 0Ch(012) Power Cycle Count 通电周期计数

通电周期计数的数据值表示了硬盘通电/断电的次数，即电源开关次数的累计，反映硬盘寿命的一个参考值。

* 0Dh(013) Soft Read Error Rate 软件读取错误率

软件读取错误率也称为可校正的读取误码率，就是报告给操作系统的未经校正的读取错误。数据值越低越好，过高则可能暗示盘片磁介质有问题。

* B4h(180) Unused Reserved Block Count Total未用的备用块计数（惠普）

固态硬盘会保留一些容量来准备替换损坏的存储单元，所以可用的预留空间数是非常重要的。这个参数的当前值表示尚未使用的预留的存储单元数量。

* B7h(183) SATA Downshift Error Count 串口降速错误计数

这一项表示SATA接口速率错误下降的次数。通常硬盘与主板之间的兼容问题会导致SATA传输级别降级运行。

* B8h(184) End to End Error Detection Count 端到端错误检测次数

Intel第二代的34nm固态硬盘有点到点错误检测计数这一项。固态硬盘里有一个LBA（logical block addressing，逻辑块地址）记录，这一项显示了SSD内部逻辑块地址与真实物理地址间映射的出错次数。

* BBh(187) Reported Uncorrectable Errors 无法校正的错误

报告给OS的无法通过硬件ECC校正的错误，如果数值大于0，应该注意硬盘安全，甚至备份数据。

* BCh(188) Command Timeout 通信超时

由于无法连接至硬盘而终止操作的统计数，一般为0，如果远超过0，则可能电源供电问题或数据线接口氧化，也可能是硬盘出现更严重的问题。

* BDh(189) High Fly Writes 高飞写入

这一项的数据值就统计了写入时磁头飞行高度出现偏差的次数。硬盘进行写入时对磁头高度进行监控以提供额外的保障;当磁头处于不正常高度进行写入时，写入操作会被终止，原有数据重写入或者将该扇区重映射到安全区域,该属性是统计值。

* BEh(190) Airflow Temperature 气流温度

这一项表示的是硬盘内部盘片表面的气流温度。在希捷公司的某些硬盘中，当前值=（100－当前温度），因此气流温度越高，当前值就越低，最差值则是当前值曾经到达过的最低点，临界值由制造商定义的最高允许温度来确定，而数据值不具实际意义。许多硬盘也没有这一项参数。

* BFh(191) G-sense Error Rate 冲击错误率

这一项的数值记录了硬盘受到机械冲击导致出错的频度。

* C0h(192) Power-Off Retract Count断电返回计数

此参数表示不安全断电时候磁头返回的次数。但要注意这个参数对某些硬盘来说，仅记录意外断电时磁头的返回动作；而某些硬盘记录了所有（包括休眠、待机，但不包括关机时）的磁头返回动作；还有些硬盘这一项没有记录。

因此这一参数的数据值在某些硬盘上持续为0或稍大于0，但在另外的硬盘上则会大于通电周期计数（0C）或启停计数（04）的数据。在一些新型节能硬盘中，这一参数的数据量还与硬盘的节能设计相关，可能会远大于通电周期计数（0C）或启停计数（04）的数据，但又远小于磁头加载/卸载计数（C1）的数据量。

对于固态硬盘来说，虽然没有磁头的加载/卸载操作，但这一项的数据量仍然代表了不安全关机，即发生意外断电的次数。

* C1h(193) Load Cycle Count 磁头加载/卸载次数

这个参数的数据值就是磁头执行加载/卸载操作的累计次数。从原理上讲，这个加载/卸载次数应当与硬盘的启停次数相当，但对于笔记本内置硬盘以及台式机新型节能硬盘来说，这一项的数据量会很大。这是因为磁头臂组件设计有一个固定的返回力矩，保证在意外断电时磁头能靠弹簧力自动离开盘片半径范围，迅速返回停靠架。所以要让硬盘运行时磁头保持在盘片的半径之内，就要使磁头臂驱动电机（寻道电机）持续通以电流。而让磁头臂在硬盘空闲几分钟后就立即执行卸载动作，返回到停靠架上，既有利于节能，又降低了硬盘受外力冲击导致磁头与盘片接触的概率。虽然再次加载会增加一点寻道时间，但毕竟弊大于利，所以在这类硬盘中磁头的加载/卸载次数会远远大于通电周期计数（0C）或启停计数（04）的数据量。不过这种加载/卸载方式已经没有了磁头与盘片的接触，所以设计值也已大大增加，通常笔记本内置硬盘的磁头加载/卸载额定值在30～60万次，而台式机新型节能硬盘的磁头加载/卸载设计值可达一百万次。

* C2h(194) Temperature 温度

这个数值直接表示了硬盘内部的当前温度。硬盘运行时最好不要超过45℃，温度过高虽不会导致数据丢失，但引起的机械变形会导致寻道与读写错误率上升，降低硬盘性能。硬盘的最高允许运行温度可查看硬盘厂商给出的数据，一般不会超过60℃。

* C3h(195) Hardware ECC Recovered

这个数值记录了磁头在盘片上读写时通过ECC技术校正错误的次数，不过许多硬盘有其制造商特定的数据结构，因此数据量的大小并不能直接说明问题。

* C4h(196) Reallocation Event Count 重映射事件计数

数据值应为0，当前值应该远大于临界值。

记录已重映射操作的计数，无论是否成功转移，都会被计数。反映了硬盘已经存在了不良扇区。

* C5h(197) Current Pending Sector Count 当前待映射扇区计数

数据值应为0，当前值应该远大于临界值。

记录了不稳定的扇区的数量，即等待被映射的扇区。如果不稳定扇区在随后的读写中成功了，那么此扇区就不再列入等待的范围，数值会下降。仅仅读取时出错的扇区并不会导致重映射，只是被列入“等待”，也许以后读取就没有问题，所以只有在写入失败时才会发生重映射。下次对该扇区写入时如果继续出错，就会产生一次重映射操作，此时重映射扇区计数（05）与重映射事件计数（C4）的数据值增加，此参数的数据值下降。

* C6h(198) Offline Uncorrectable Sector Count 脱机无法校正的扇区计数

数据应为0，当前值应远大于临界值。 

这个参数的数据累计了读写扇区时发生的无法校正的错误总数。数值上升表明盘片表面介质或机械子系统出现问题，有些扇区肯定已经不能读取，如果有文件正在使用这些扇区，操作系统会返回读盘错误的信息。下一次写操作时会对该扇区执行重映射。

* C7h(199) UDMA CRC Error CountULTRA DMA访问校验错误率
  C7h(199) Ultra ATA CRC Error Rate Ultra DMA访问校验错误率

这个参数的数据值累计了通过接口循环冗余校验（Interface Cyclic Redundancy Check，ICRC）发现的数据线传输错误的次数。如果数据值不为0且持续增长，表示“硬盘控制器→数据线→硬盘接口”出现错误，劣质的数据线、接口接触不良都可能导致此现象。由于这一项的数据值不会复零，所以某些新硬盘也会出现一定的数据量，只要更换数据线后数值不再继续增长，即表示问题已得到解决。

* C8h(200) Multi-zone Error Rate 多区域错误率
  C8h(200) Write Error Rate 写入错误率

数据应为0，当前值应远大于临界值。  

这个参数的数据累计了向扇区写入数据时出现错误的总数。有的新硬盘也会有一定的数据量，若数值持续快速升高（当前值偏低），表示盘片、磁头组件可能有问题。  

* CEh(206) Flying Height 磁头飞行高度

磁头距离盘片表面的垂直距离。高度过低则增加了磁头与盘片接触导致损坏的可能性；高度偏高则增大了读写错误率。不过准确地说，硬盘中并没有任何装置可以直接测出磁头的飞行高度，制造商也只是根据磁头读取的信号强度来推算磁头飞行高度。

* CFh(207) Spin High Current 主轴过电流

数值记录了主轴电机运行时出现浪涌电流的次数，数据量的增加意味着轴承或电机可能有问题。

* D0h(208) Spin Buzz 主轴电机重启次数

数值记录了主轴电机反复尝试启动的次数，这通常是由于电源供电不足引起的。

* DCh(220) Disk Shift 盘片偏移量

硬盘中的盘片相对主轴的偏移量（通常是受外力冲击或温度变化所致），单位未知，数据值越小越好。

* F1h(241) Total LBAs Written LBA写入总数

LBA写入数的累计。

* F2h(242) Total LBAs Read LBA读取总数

LBA读取数的累计。某些SMART读取工具会显示负的数据值，是因为采用了48位LBA，而不是32位LBA。

* FEh(254) Free Fall Protection 自由坠落保护

现在有些笔记本硬盘具有自由坠落保护功能，当硬盘内置的加速度探测装置检测到硬盘位移时，会立即停止读写操作，将磁头臂复位。这个措施防止了磁头与盘片之间发生摩擦撞击，提高了硬盘的抗震性能。这个参数的数据里记录了这一保护装置动作的次数。


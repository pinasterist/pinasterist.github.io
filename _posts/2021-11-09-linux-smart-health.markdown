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

```tsx
ID# ATTRIBUTE_NAME          
  1 Raw_Read_Error_Rate     读取错误率
  3 Spin_Up_Time            起转时间
  4 Start_Stop_Count        启动停止次数
  5 Reallocated_Sector_Ct   重新分配扇区计数
  7 Seek_Error_Rate         寻道错误率
  9 Power_On_Hours          通电时间
 10 Spin_Retry_Count        起转重试次数
 11 Calibration_Retry_Count 重新校准重试次数
 12 Power_Cycle_Count       启动<->关闭循环次数
192 Power-Off_Retract_Count 断电磁头缩回计数
193 Load_Cycle_Count        磁头加载/卸载循环计数
194 Temperature_Celsius     温度
196 Reallocated_Event_Count 在分配扇区物理位置事件计数（与坏道无关）
197 Current_Pending_Sector  当前等待中扇区数（状态存疑/不稳定-等待后续判断）
198 Offline_Uncorrectable   无法修正的扇区总数
199 UDMA_CRC_Error_Count    UltraDMA CRC错误计数
200 Multi_Zone_Error_Rate   写入错误率
```


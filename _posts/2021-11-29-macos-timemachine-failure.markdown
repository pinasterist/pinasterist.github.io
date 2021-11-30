---
layout:     post
title:      "解决Monterey中TimeMachine无法备份的问题"
subtitle:   "升级Monterey后遗症之一：时间机器挂住"
date:       2021-11-28
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - macos
    - time machine
    - finder
    - containers

---

升级Monterey之前，就有了心理准备，幺蛾子是肯定会出现，只是不知道出在哪里。这不，最大的一只幺蛾子出现了：时间机器备份一直不成功，导致我的系统主盘容量越来越小。看来不解决它是不行了。

观察时间机器的表征，发现它没有报告任何错误，唯一的提示就是等待第一次备份完成。估计是什么东西阻碍时间机器完成备份。

为了省事，我直接上谷歌看看有没有现成的答案。这一找还真有。在威锋网上，找到两篇帖子。

<https://www.feng.com/post/13509451>

<http://www.feng.com/post/13511333>

大概意思是有几个文件夹被锁，导致时间机器挂住，无法完成备份。其中有位网友还给出了方法。我不禁大喜，马上按图索骥，操作起来。

* 查找时间机器的错误日志

首先，手工执行一次备份。然后，用下面的命令拿到相关日志。

```
log show --predicate 'subsystem == "com.apple.TimeMachine"' --info --last 10m > tm.log
```

10m表示10分钟，可以根据需要修改。

tm.log文件内容太多，使用下列命令过滤一下。

```
cat tm.log | grep 'Failed to acquire device lock'  > tm2.log
cat tm2.log | cut -d ' ' -f 48 > tm3.log
cat tm3.log | sed 's#.*(/Users.*)#$1#g' > tm4.log
cat tm4.log | sort -u > tm5.log 
```

最终得到下面几个目录

```
/Users/$(username}/Library/Containers/A7F256E7-E6CB-48F4-BE3B-5F3759D8B5D2/Data/Library/TalkingData
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetIntentsItems/Data/Documents
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetIntentsItems/Data/Library
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetIntentsItems/Data/tmp
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetIntentsPeople/Data/Documents
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetIntentsPeople/Data/Library
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetIntentsPeople/Data/tmp
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetItems/Data/Documents
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetItems/Data/Library
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetItems/Data/tmp
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetPeople/Data/Documents
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetPeople/Data/Library
/Users/$(username}/Library/Containers/com.apple.findmy.FindMyWidgetPeople/Data/tmp
```

除了第一个外，其他几个都是帖子中提到的目录。

* 设置时间机器，排除这几个目录

按照帖子的要求，进入时间机器的设置窗口，打开option，选择查看隐藏文件。注意，文件窗口中是没有完整目录的，只能找到FindMyWidgetIntentsItems、FindMyWidgetIntentsPeople、FindMyWidgetItems、FindMyWidgetPeople这四项。将其选中排除后，你就可以在窗口中看到完整目录。

* 解决TalkingData

在上一步中，第一个目录无法排除，虽然我找了半天。抱着万一的心态（万一成了呢），试着备份了一次，奇迹没有出现，看来绕不过去了。继续找办法。时间又过去了半小时，还是一无所获，只能用最后一招了，删除！

到这个目录中去，先看看文件有没有用。

```
~/Library/Containers/A7F256E7-E6CB-48F4-BE3B-5F3759D8B5D2/Data/Library/TalkingData  ls -la
total 24
drwxr-xr-x@  5 ${username}  staff   160 Apr 29  2021 .
drwx------  43 ${username}  staff  1376 Apr 29  2021 ..
-rw-r--r--@  1 ${username}  staff    16 Apr 29  2021 TDAA.TD_SendMessage_AppAnalytics
-rw-r--r--@  1 ${username}  staff    16 Apr 29  2021 TDAA.TD_SendMessage_TXEnv
-rw-r--r--@  1 ${username}  staff   208 Apr 29  2021 TDAA.TmpData_v1
```

感觉是用来搜集调试信息的，估计不是什么关键信息，直接删除。后来回想这是不对滴，应该先备份。)-0

重新备份，十几分钟后，时间机器有了变化，居然开始做清除工作了。看来是ok了。果然，20分钟后，时间机器完成备份，不再提示等待第一次备份完成。

* 删除snapshot

时间机器是好了，可我的系统盘的空余空间开始没出来。估计是时间机器的snapshot没清除。打开磁盘管理器，选中Data盘，果然看到大量snapshot。全部选中，右击删除。不用担心误删除，时间机器会加锁的。

至此，备份问题得到解决，系统空间也恢复正常。这个过程中，命令log起了很大作用，后面找机会学习一下，写篇文章。













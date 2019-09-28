---
layout:     post
title:      "Linux终端编程知识一二"
subtitle:   "关于终端机制、编码、历史那些事"
date:       2019-09-28
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - terminal
    - ascii
    - c0
    - c1
---

刚刚完成一段程序，可以将任何字符流通过mqtt协议映射到远端机器。期间用到很多终端方面的知识，这里特地留笔记录，以待日后所用。


# 字符设备里面的字符

我们知道，计算机世界里面只有二进制数，和我们人类使用的各种符号（包括文字、数字）没啥关系。为了让计算机能够识别处理这些符号，人们只能设计出字符编码，定义字符和数字的映射关系。

字符编码有非常多。对于终端编程来说，最重要的是ASCII编码。当然，8859-n编码也需要稍微提一下。


### ASCII和C0控制字符

最开始的终端其实就是一台打印机，通过键盘输入，通过纸张输出。终端本身没有计算能力，他唯一的任务就是连接到大型机，为大型机提供一个输入输出通道。ASCII编码就是那个时候发明的。

ASCII编码集只使用7个bit来表示字符。7个bit一共可以表示127个字符。ASCII将其分成打印字符和控制字符：打印字符的编码从33到126，共93个；控制字符的编码包括0到32和127，共34个。注意，空格也属于控制字符，因为空格无法打印。其实把空格理解成将打印头右移一个字符位就行。就像回车符就是将打印头移到最左端，换行符就是将纸张前进一行。这34个控制字符又称为C0，请看下图。

![ALT C0 Table](/img/ascii_c0_table.png "C0")

对终端编程而言，C0控制字符中最有用的是，LF（换行）、CR（回车）、ESC（转义）、HT（TAB）、BS（退格）、SP（空格）、DEL（删除）等，这些字符都是些格式化字符。其他的控制字符很多都是用于控制老式的终端机、通讯线路等。现代终端早已不使用这些字符。但是为了兼容以前的软件，这些字符还保留着。

### C1控制字符

CPU所能使用的最小存储单元是8比特的字节，ASCII只用了7比特，这意味还有128个编码是闲置的。为了用上这些资源，人们又制定了新的映射方法。C1控制字符包括128到159共32个编码，如下图所示。

![ALT C1 Table](/img/ascii_c1_table.png "C1")

这些编码很少被用到，估计制定这个编码的科学家会比较郁闷。

### ISO8859特殊字符

ISO8859特殊字符很简单，只有两个，包括160和173（二进制分别为A0和AD），主要用在视窗系统和unicode中。如图所示。

![ALT C1 Table](/img/iso8859_special_character_table.png "ISO8859 Special Characters")


现在让我们总结一下单字节控制字符的编码。请看下图。

![ALT ISO8859 Special Character Table](/img/iso8859_special_character_table.png "ISO8859 Special Character")

需要注意的是，图中的方案只是一个比较流行的方案。在其他地方，还有很多其他种类的字符编码，详细情况可以参看这个网址，[International Register of Coded Character Sets](https://www.itscj.ipsj.or.jp/itscj_english/iso-ir/ISO-IR.pdf)。

# 字符设备控制标准

上面我们说到控制字符的编码有很多种，那么字符设备到底用哪种呢？设备厂商可不愿意被绑在某个特定的编码上，毕竟兼容性越强，越有可能打开销路。因此， 很多字符设备的控制标准被提出来。

### Posix.1、SVR4和4.3+BSD控制字符

POSIX标准定义操作系统的几乎所有方方面面的标准，其中就包括字符设备的标准。为了控制字符设备，POSIX定义了7种控制字符。另外再加上SVR和BSD这两个UNIX变种分别定义的6个和7个控制字符，UNIX终端设备总共使用20个控制字符。如下图所示。

注意，不要把这里定义的控制字符和C0、C1的控制字符混为一谈。实际上，这里的20个控制字符，你几乎可以映射到任意一个ASCII字符。

![ALT C1 Table](/img/posix_svr_bsc_control_characters.png POSIX、SVR、BSC控制字符)

我们可以看，POSIX的NL（新行）和CR（回车）对应到C0的LF（换行）和CR（回车），且不可更换。

### Posix的终端IO函数


控制字符只是POSIX终端标准中的一部分，另一部分就是它的程序控制标准。Posix的程序控制标准主要由下面的12个函数组成：

tcgetattr    取属性(termios结构)；
tcsetattr    设置属性(termios结构)；
cfgetispeed  得到输入速度；
cfgetospeed  得到输出速度；
cfsetispeed  设置输入速度；
cfsetospeed  设置输出速度；
tcdrain      等待所有输出都被传输；
tcflow       挂起传输或接收；
tcflush      刷清未决输入和/或输出；
tcsendbreak  送BREAK字符；
tcgetpgrp    得到前台进程组ID；
tcsetpgrp    设置前台进程组ID；

我们常用的就是tcgetattr、tcsetattr这两个。其他的函数主要用于控制字符设备的通讯、传输等行为。

### tcgetattr和tcsetattr

```
#include <termios.h>
int tcgetattr(int filedes, struct termios *termptr);
int tcsetattr(int filedes, int opt, const struct termios *termptr);
两个函数返回：若成功则为0，若出错则为-1
```

这两个函数都有一个指向termios结构的指针作为其参数，它们返回当前终端的属性，或者设置该终端的属性。因为这两个函数只对终端设备进行操作，所以若filedes并不引用一个终端设备则出错返回，errno设置为ENOTTY。

tcsetattr的参数opt使我们可以指定在什么时候新的终端属性才起作用。opt可以指定为下列常数中的一个：

TCSANOW：更改立即发生；
TCSADRAIN：发送了所有输出后更改才发生。若更改输出参数则应使用此选择项。
TCSAFLUSH：发送了所有输出后更改才发生。更进一步，在更改发生时未读的所有输入数据都被删除(刷清)。
tcsetattr函数的返回值易于产生混淆。如果它执行了任意一种所要求的动作，即使未能执行所有要求的动作，它也返回0(表示成功)。如果该函数返回0，则我们有责任检查该函数是否执行了所有要求的动作。这就意味着，在调用tcsetattr设置所希望的属性后，需调用tcgetattr，然后将实际终端属性与所希望的属性相比较，以检测两者是否有区别。

### termios结构

```
struct termios {
    tcflag_t c_iflag; /* Input modes */
    tcflag_t c_oflag; /* Output modes */
    tcflag_t c_cflag; /* Control modes */
    tcflag_t c_lflag; /* Local modes */
    cc_t c_cc[NCCS]; /* Control characters */
};
```

各个字段的选项如下(不是所有UNIX系统都支持)：

**c_iflag**：

* BRKINT：接到BREAK时产生SIGINT；
* ICRNL：将输入的CR转换为NL；
* IGNBRK：忽略BREAK条件；
* IGNCR：忽略CR；
* IGNPAR：忽略奇偶错字符；
* IMAXBEL：在输入队列空时振铃；
* INLCR：将输入的NL转换为CR；
* INPCK：打开输入奇偶校验；
* ISTRIP：剥除输入字符的第8位；
* IUCLC：将输入的大写字符转换成小写字符(仅SVR4)；
* IXANY：使任一字符都重新起动输出；
* IXOFF：使起动/停止输入控制流起作用；
* IXON：使起动/停止输出控制流起作用；
* PARMRK：标记奇偶错；

**c_oflag**：

* BSDLY：退格延迟屏蔽(仅SVR4)；
* CRDLY：CR延迟屏蔽(仅SVR4)；
* FFDLY：换页延迟屏蔽(仅SVR4)；
* NLDLY：NL延迟屏蔽(仅SVR4)；
* OCRNL：将输出的CR转换为NL(仅SVR4)；
* OFDEL：填充符为DEL，否则为NUL(仅SVR4)；
* OFILL：对于延迟使用填充符(仅SVR4)；
* OLCUC：将输出的小写字符转换为大写字符(仅SVR4)；
* ONLCR：将NL转换为CR-NL；
* ONLRET：NL执行CR功能(仅SVR4)；
* ONOCR：在0列不输出CR(仅SVR4)；
* ONOEOT：在输出中删除EOT字符(仅4.3+BSD)；
* OPOST：执行输出处理；
* OXTABS：将制表符扩充为空格(仅4.3+BSD)；
* TABDLY：水平制表符延迟屏蔽(仅SVR4)；
* VTDLY：垂直制表符延迟屏蔽(仅SVR4)；

**c_cflag**

* CCTS_OFLOW：输出的CTS流控制(仅4.3+BSD)；
* CIGNORE：忽略控制标志(仅4.3+BSD)；
* CLOCAL：忽略解制解调器状态行；
* CREAD：启用接收装置；
* CRTS_IFLOW：输入的RTS流控制(仅4.3+BSD)；
* CSIZE：字符大小屏蔽；
* CSTOPB：送两个停止位，否则为1位；
* HUPCL：最后关闭时断开；
* MDMBUF：经载波的流控输出(仅4.3+BSD)；
* PARENB：进行奇偶校；
* PARODD：奇校，否则为偶校；

**c_lflag**：

* ALTWERASE：使用替换WERASE算法(仅4.3+BSD)；
* ECHO：进行回送；
* ECHOCTL：回送控制字符为^(char)；
* ECHOE：可见擦除符；
* ECHOK：回送kill符；
* ECHOKE：kill的可见擦除；
* ECHONL：回送NL；
* ECHOPRT：硬拷贝的可见擦除方式；
* FLUSHO：刷清输出；
* ICANON：规范输入；
* IEXTEN：使扩充的输入字符处理起作用；
* ISIG：使终端产生的信号起作用；
* NOFLSH：在中断或退出键后不刷清；
* NOKERNINFO：STATUS不使内核输出(仅4.3+BSD)；
* PENDIN：重新打印；
* TOSTOP：对于后台输出发送SIGTTOU；
* XCASE：规范大/小写表示(仅SVR4)；

所有列出的选择标志(除屏蔽标志外)都用一或多位表示，而屏蔽标志则定义多位。屏蔽标志有一个定义名，每个值也有一个名字。例如，为了设置字符长度，首先用字符长度屏蔽标志CSIZE将表示字符长度的位清0，然后设置下列值之一：CS5、CS6、CS7或CS8。由SVR4支持的6个延迟值也有屏蔽标志：BSDLY、CRDLY、FFDLY、NLDLY、TABDLY和VTDLY。

各个标志的含义如下：

* ALTWERASE：(c_lflag, 4.3+BSD)此标志设置时，若输入了WERASE字符，则使用一个替换的字擦除算法。它不是向后移动到前一个白空字符为止，而是向后移动到第一个非字母、数字符为止。
* BRKINT：(c_iflag, POSIX.1)若此标志设置，而IGNBRK未设置，则在接到BREAK时，输入、输出队列被刷清，并产生一个SIGINT信号。如果此终端设备是一个控制终端，则将此信号送给前台进程组各进程。如果IGNBRK和BRKINT都没有设置，但是设置了PARMRK，则BREAK被读作为三个字节序列\377，\0和\0，如果PARMRK也没有设置，则BREAK被读作为单个字符\0。
* BSDLY：(c_oflag, SVR4)退格延迟屏蔽，此屏蔽的值是BS0或BS1。
* CCTS_OFLOW：(c_cflag, 4.3+BSD)输出的CTS流控制。
* CIGNORE：(c_cflag, 4.3+BSD)忽略控制标志。
* CLOCAL：(c_cflag, POSIX.1)如若设置，则忽略调制解调器状态线。这通常意味着该设备是本地连接的。若此标志未设置，则打开一个终端设备常常会阻塞到调制解调器回应。
* CRDLY：(c_oflag, SVR4)回车延迟屏蔽。此屏蔽的值是CR0、CR1、CR2和CR3。
* CREAD：(c_cflag, POSIX.1)如若设置，则接收装置被启用，可以接收字符。
* CRTS_IFLOW：(c_cflag, 4.3+BSD)输入的RTS流控制。
* CSIZE：(c_cflag, POSIX.1)此字段是一个屏蔽标志，它指明发送和接收的每个字节的位数。此长度不包括可能有的奇偶校验位。由此屏蔽定义的字段值是CS5、CS6、CS7和CS8，分别表示每个字节包含5、6、7和8位。
* CSTOPB：(c_cflag, POSIX.1)如若设置，则使用两位作为停止位，否则只使用一位作为停止位。
* ECHO：(c_lflag, POSIX.1)如若设置，则将输入字符回送到终端设备。在规范方式和非规范方式下都可以回送字符。
* ECHOCTL：(c_lflag, SVR4和4.3+BSD)如若设置并且ECHO也设置，则除ASCII TAB、ASCII NL、START和STOP字符外，其他ASCII控制符(ASCII字符集中的0～037)都被回送为^X，其中，X是相应控制字符代码值加0100所构成的字符。这就意味着ASCII Ctrl-A字符(01)被回送为^A。ASCII DELETE字符(0177)则回送为^?。如若此标志未设置，则ASCII控制字符按其原样回送。如同ECHO标志，在规范方式和非规范方式下此标志对控制字符回送都起作用。应当了解的是：某些系统回送EOF字符产生的作用有所不同，其原因是EOF的典型值是Ctrl-D，而这是ASCII EOT字符，它可能使某些终端挂断。
* ECHOE：(c_lflag, POSIX.1)如若设置并且ICANON也设置，则ERASE字符从显示中擦除当前行中的最后一个字符。这通常是在终端驱动程序中写三个字符序列：退格，空格，退格实现的。如若支持WERASE字符，则ECHOE用一个或若干个上述三字符序列擦除前一个字。如若支持ECHOPRT标志，则在这里所说明的ECHOE动作假定ECHOPRT标志没有设置。
* ECHOK：(c_lflag, POSIX.1)如若设置并且ICANON也设置，则KILL字符从显示中擦除当前行，或者输出NL字符(用以强调已擦除整个行)。如若支持ECHOKE标志，则这里的说明假定ECHOKE标志没有设置。
* ECHOKE：(c_lflag, SVR4和4.3+BSD)如若设置并且ICANON也设置，则回送KILL字符的方式是擦去行中的每一个字符。擦除每个字符的方法则由ECHOE和ECHOPRT标志选择。
* ECHONL：(c_lflag, POSIX.1)如若设置并且ICANON也设置，即使没有设置ECHO也回送NL字符。
* ECHOPRT：(c_lflag, SVR4和4.3+BSD)如若设置并且ICANON和ECHO也都设置，则ERASE字符(以及WERASE字符，若受到支持)使所有正被擦除的字符按它们被擦除的方式打印。在硬拷贝终端上这常常是有用的，这样可以确切地看到哪些字符正被擦去。
* FFDLY：(c_oflag, SVR4)换页延迟屏蔽。此屏蔽标志值是FF0或FF1。
* FLUSHO：(c_lflag, SVR4和4.3+BSD)如若设置，则刷清输出。当键入DISCARD字符时设置此标志，当键入另一个DISCARD字符时，此标志被清除。设置或清除此终端标志也可设置或清除此条件。
* HUPCL：(c_cflag, POSIX.1)如若设置，则当最后一个进程关闭此设备时，调制解调器控制线降至低电平(也就是调制解调器的连接断开)。
* ICANON：(c_lflag, POSIX.1)如若设置，则按规范方式工作。这使下列字符起作用：EOF、EOL、EOL2、ERASE、KILL、REPRINT、STATUS和WERASE。输入字符被装配成行。如果不以规范方式工作，则读请求直接从输入队列取字符。在至少接到MIN个字节或已超过TIME值之前，read将不返回。
* ICRNL：(c_iflag, POSIX.1)如若设置并且IGNCR未设置，即将接收到的CR字符转换成一个NL字符。
* IEXTEN：(c_lflag, POSIX.1)如若设置，则识别并处理扩充的、实现定义的特殊字符。
* IGNBRK：(c_iflag, POSIX.1)在设置时，忽略输入中的BREAK条件。关于BREAK条件是产生信号还是被读作为数据，请见BRKINT。
* IGNCR：(c_iflag, POSIX.1)如若设置，忽略接收到的CR字符。若此标志未设置，而设置了ICRNL标志则将接收到的CR字符转换成一个NL字符。
* IGNPAR：(c_iflag, POSIX.1)在设置时，忽略带有结构错误(非BREAK)或奇偶错的输入字节。
* IMAXBEL：(c_iflag, SVR4和4.3+BSD)当输入队列满时响铃。
* INLCR：(c_iflag, POSIX.1)如若设置，则接收到的NL字符转换成CR字符。
* INPCK：(c_iflag, POSIX.1)当设置时，使输入奇偶校验起作用。如若未设置INPCK，则使输入奇偶校验不起作用。奇偶“产生和检测”和“输入奇偶性检验”是不同的两件事。奇偶位的产生和检测是由PARENB标志控制的。设置该标志后使串行界面的设备驱动程序对输出字符产生奇偶位，对输入字符则验证其奇偶性。标志PARODD决定该奇偶性应当是奇还是偶。如果一个其奇偶性为错的字符已经来到，则检查INPCK标志的状态。若此标志已设置，则检查IGNPAR标志(以决定是否应忽略带奇偶错的输入字节)，若不应忽略此输入字节，则检查PARMRK标志以决定向读进程应传送那种字符。
* ISIG：(c_lflag, POSIX.1)如若设置，则判别输入字符是否是要产生终端信号的特殊字符(INTR，QUIT，SUSP和DSUSP)，若是，则产生相应信号。
* ISTRIP：(c_iflag, POSIX.1)当设置时，有效输入字节被剥离为7位。当此标志未设置时，则保留全部8位。
* IUCLC：(c_iflag, SVR4)将输入的大写字符映射为小写字符。
* IXANY：(c_iflag, SVR4和4.3+BSD)使任一字符都能重新起动输出。
* IXOFF：(c_iflag, POSIX.1)如若设置，则使起动-停止输入控制起作用。当终端驱动程序发现输入队列将要填满时，输出一个STOP字符。此字符应当由发送数据的设备识别，并使该设备暂停。此后，当已对输入队列中的字符进行了处理后，该终端驱动程序将输出一个START字符，使该设备恢复发送数据。
* IXON：(c_iflag, POSIX.1)如若设置，则使起动-停止输出控制起作用。当终端驱动程序接收到一个STOP字符时，输出暂停。在输出暂停时，下一个START字符恢复输出。如若未设置此标志，则START和STOP字符由进程读作为一般字符。
* MDMBUF：(c_cflag, 4.3+BSD)按照调制解调器的载波标志进行输出流控制。
* NLDLY：(c_oflag, SVR4)新行延迟屏蔽。此屏蔽的值是NL0和NL1。
* NOFLSH：(c_lflag, POSIX.1)按系统默认，当终端驱动程序产生SIGINT和SIGQUIT信号时，输入、出队列都被刷新。另外，当它产生SIGSUSP信号时，输入队列被刷新。如若设置了NOFLSH标志，则在这些信号产生时，不对输入、出队列进行刷新。
* NOKERNINFO：(c_lflag, 4.3+BSD)当设置时，此标志阻止STATUS字符使前台进程组的状态信息显示在终端上。但是不论本标志是否设置，STATUS字符使SIGINFO信号送至前台进程组中的所有进程。
* OCRNL：(c_oflag, SVR4)如若设置，将输出的CR字符映照为NL。
* OFDEL：(c_oflag, SVR4)如若设置，则输出填充字符是ASCII DEL，否则它是ASCII NUL，见OFILL标志。
* OFILL：(c_oflag, SVR4)如若设置，则为实现延迟，发送填充字符(ASCII DEL或ASCII NUL，见OFDEL标志)，而不使用时间延迟。见6个延迟屏蔽：BSDLY，CRDLY，FFDLY，NLDLY，TABDLY以及VTDLY。
* OLCUC：(c_oflag, SVR4)如若设置，将小写字符映射为大写。
* ONLCR：(c_oflag, SVR4和4.3+BSD)如若设置，将输出的NL字符映照为CR-NL。
* ONLRET：(c_oflag, SVR4)如若设置，则输出的NL字符将执行回车功能。
* ONOCR：(c_oflag, SVR4)如若设置，则在0列不输出CR。
* ONOEOT：(c_oflag, 4.3+BSD)如若设置，则在输出中删除EOT字符(^D)。在将Ctrl-D解释为挂断的终端上这可能是需要的。
* OPOST：(c_oflag, POSIX.1)如若设置，则进行实现定义的输出处理。
* OXTABS：(c_oflag, 4.3+BSD)如若设置，制表符在输出中被扩展为空格。这与将水平制表延迟(TABDLY)设置为XTABS或TAB3产生同样效果。
* PARENB：(c_cflag, POSIX.1)如若设置，则对输出字符产生奇偶位，对输入字符则执行奇偶性检验。若PARODD已设置，则奇偶校验是奇校验，否则是偶校验。也见INPCK、IGNPAR和PARMRK标志部分。
* PARMRK：(c_iflag, POSIX.1)，当设置时，并且IGNPAR未设置，则结构性错(非BREAK)和奇偶错的字节由进程读作为三个字符序列\377, \0和X，其中X是接收到的具有错误的字节。如若ISTRIP未设置，则一个有效的\377被传送给进程时为\377，\377。如若IGNPAR和PARMRK都未设置，则结构性错和奇偶错的字节都被读作为一个字符\0。
* PARODD：(c_cflag, POSIX.1)如若设置，则输出和输入字符的奇偶性都是奇，否则为偶。注意，PARENB标志控制奇偶性的产生和检测。
* PENDIN：(c_lflag, SVR4和4.3+BSD)如若设置，则在下一个字符输入时，尚未读的任何输入都由系统重新打印。这一动作与键入REPRINT字符时的作用相类似。
* TABDLY：(c_oflag, SVR4)水平制表延迟屏蔽。此屏蔽的值是TAB0、TAB1、TAB2或TAB3。XTABS的值等于TAB3。此值使系统将制表符扩展成空格。系统假定制表符所扩展的空格数到屏幕上最近一个8的倍数处为止。不能更改此假定。
* TOSTOP：(c_lflag, POSIX.1)如若设置，并且该实现支持作业控制，则将信号SIGTTOU送到试图与控制终端的一个后台进程的进程组。按默认，此信号暂停该进程组中所有进程。如果写控制终端的进程忽略或阻塞此信号，则终端驱动程序不产生此信号。
* VTDLY：(c_oflag, SVR4)垂直制表延迟屏蔽。此屏蔽的值是VT0或VT1。
* XCASE：(c_lflag, SVR4)如若设置，并且ICANON也设置，则认为终端是大写终端，所以输入都变换为小写。为了输入一个大写字符，在其前加一个\。与之类似，输出一个大写字符也在其前加一个\(这一标志已经过时，现在几乎所有终端都支持大、小写字符)。

关于POSIX终端规范，更详细的内容可以参看[IEEE Std 1003.1-2017](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap11.html#tag_11_01_09)





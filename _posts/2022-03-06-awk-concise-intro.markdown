---
layout:     post
title:      "AWK简单介绍"
subtitle:   "常用的AWK命令"
date:       2022-03-06
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - sed
    - reg

---

awk是linux下的一个命令，他对其他命令的输出，对文件的处理都十分强大，其实他更像一门编程语言，他可以自定义变量，有条件语句，有循环，有数组，有正则，有函数等。他读取输出，或者文件的方式是一行，一行的读，根据你给出的条件进行查找，并在找出来的行中进行操作，感觉他的设计思想，真的很简单，但是结合实际情况，具体操作起来就没有那么简单了。他有三种形势，awk，gawk，nawk，平时所说的awk其实就是gawk。

## 参数选项
-F 紧跟分隔符，表示读入的字段以输入的分隔符分割
-v 进入变量模式 可以进行变量的赋值及调用（调用不需要加$符）

## 变量

```
变 量	描述
$n	             当前记录的第n个字段，字段间由 FS分隔。
$0	             完整的输入记录。
ARGC             	命 令行参数的数目。
ARGIND             	命令行中当前文件的位置(从0开始算)。
ARGV             	包 含命令行参数的数组。
CONVFMT             	数字转换格式(默认值为%.6g)
ENVIRON             	环 境变量关联数组。
ERRNO             	最后一个系统错误的描述。
FIELDWIDTHS             	字 段宽度列表(用空格键分隔)。
FILENAME             	当前文件名。
FNR	同 NR，但相对于当前文件。
FS	字段分隔符(默认是任何空格)。
IGNORECASE	如 果为真，则进行忽略大小写的匹配。
NF	当前记录中的字段数。
NR	当 前记录数。
OFMT	数字的输出格式(默认值是%.6g)。
OFS	输 出字段分隔符(默认值是一个空格)。
ORS	输出记录分隔符(默认值是一个换行符)。
RLENGTH	由 match函数所匹配的字符串的长度。
RS	记录分隔符(默认是一个换行符)。
RSTART	由 match函数所匹配的字符串的第一个位置。
SUBSEP	数组下标分隔符(默认值是\034)。
```

## 运算符

```
运算符	描述
= += -= *= /= %= ^= **=	赋值
?:	C条件表达式
||	逻 辑或
&&	逻辑与
~ !~	匹 配正则表达式和不匹配正则表达式
< <= > >= != ==	关 系运算符
空格	连接
+ -	加，减
* / &	乘，除与求余
+ - !	一元加，减和逻辑非
^ ***	求幂
++ --	增加或减少，作为前缀或后缀
$	字 段引用
in	数组成员
```

## awk的正则

```
匹配符	描述
\Y	匹配一个单词开头或者末尾的空字符串
\B	匹配单词内的空字符串
\<	匹配一个单词的开头的空字符串，锚定开始
\>	匹配一个单词的末尾的空字符串，锚定末尾
\W	匹配一个非字母数字组成的单词
\w	匹配一个字母数字组成的单词
\'	匹配字符串末尾的一个空字符串
\‘	匹配字符串开头的一个空字符串
```

## 字符串函数

```
函数名	描述
sub	匹配记录中最大、最靠左边的子字符串的正则表达式，并用替换字符串替换这些字符串。如果没有指定目标字符串就默认使用整个记录。替换只发生在第一次匹配的 时候
gsub	整个文档中进行匹配
index	返回子字符串第一次被匹配的位置，偏移量从位置1开始
substr	返回从位置1开始的子字符串，如果指定长度超过实际长度，就返回整个字符串
split	可按给定的分隔符把字符串分割为一个数组。如果分隔符没提供，则按当前FS值进行分割
length	返回记录的字符数
match	返回在字符串中正则表达式位置的索引，如果找不到指定的正则表达式则返回0。match函数会设置内建变量RSTART为字符串中子字符串的开始位 置，RLENGTH为到子字符串末尾的字符个数。substr可利于这些变量来截取字符串
toupper和tolower	可用于字符串大小间的转换，该功能只在gawk中有效
```

## 数学函数

```
函数名	返回值
atan2(x,y)	y,x 范围内的余切
cos(x)	余弦函数
exp(x)	求 幂
int(x)	取整
log(x)	自然对 数
rand()	随机数
sin(x)	正弦
sqrt(x)	平 方根
srand(x)	x是rand()函数的种子
int(x)	取 整，过程没有舍入
rand()	产生一个大于等于0而小于1的随机数
```

## format的使用

要点：
1、其与print命令的最大不同是，printf需要指定format；
2、format用于指定后面的每个item的输出格式；
3、printf语句不会自动打印换行符；\\n

```
format格式的指示符都以%开头，后跟一个字符；如下：
%c: 显示字符的ASCII码；
%d, %i：十进制整数；
%e, %E：科学计数法显示数值；
%f: 显示浮点数；
%g, %G: 以科学计数法的格式或浮点数的格式显示数值；
%s: 显示字符串；
%u: 无符号整数；
%%: 显示%自身；
```

## 修饰符：

```
N: 显示宽度；
-: 左对齐；
+：显示数值符号；
```

## 测试

* 测试文件test

```
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/bin/false
daemon:x:2:2:daemon:/sbin:/bin/false
mail:x:8:12:mail:/var/spool/mail:/bin/false
ftp:x:14:11:ftp:/home/ftp:/bin/false
&nobody:$:99:99:nobody:/:/bin/false
zhangy:x:1000:100:,,,:/home/zhangy:/bin/bash
http:x:33:33::/srv/http:/bin/false
dbus:x:81:81:System message bus:/:/bin/false
hal:x:82:82:HAL daemon:/:/bin/false
mysql:x:89:89::/var/lib/mysql:/bin/false
aaa:x:1001:1001::/home/aaa:/bin/bash
ba:x:1002:1002::/home/zhangy:/bin/bash
test:x:1003:1003::/home/test:/bin/bash
@zhangying:*:1004:1004::/home/test:/bin/bash
policykit:x:102:1005:Po
```

* 例a

```
cat test | awk -F: '{\
 if ($1 == "root"){\
 print $1;\
 }else if($1 == "bin"){\
 print $2;\
 }else{\
 print $3;\
 } \
}'
```

* 例b

```
awk '{\
 for(i=0;i<NF;i++){\
 if ($i ~/^root/){\
 print $i;\
 }else if($i ~/zhangy/){\
 print $i;continue;\
 }else if($i ~/mysql/){\
 print $i;next;\
 }else if($i ~/^test/){\
 print $i;break;\
 } \
 }\
}' test
```
* 例c

```
tail test | awk 'BEGIN{while(getline d){ split(d,test);for(i in test){\
 print test[i]\
}}}'
```
* 例d

```
ls -al /home/zhangy/mytest | awk 'BEGIN{while(getline d){ split(d,test);\
 print test[9] ;}
}'
```
* 例e

```
echo "32:34" |awk -F: '{print "max = ",max($1,$2)}\
function max(one,two){
if(one > two){
 return one;
}else{
 return two;
}
}'
```
* 例f

```
awk -F: '{mat=match($1,/^[a-zA-Z]+$/);print mat,RSTART,RLENGTH}' test
```

* 例g

```
cat test |awk -F: '\
 NF != 7{\
printf("line %d,does not have 7 fields:%s\n",NR,$0)}\
$1 !~ /^[A-Za-z0-9]/{printf("line %d,non alpha and numeric user id:%s: %s\n",NR,$1,$0)}\
$2 == "*" {printf("lind %d,no password:%s\n",NR,$0)}'
```

## 测试文件

```
[root@Blackghost test2]# cat aaa //测试文件aaa
1111:23434:zhang
hoadsf:asdf:ccc
[root@Blackghost test2]# cat ccc  //测试文件ccc
1111:23434:zhang
hoadsf:asdf:ccc
tank:zhang:x20342
ying:zhasdf:72342
hosa:asdfa:2345sdf
```

例a


```
[root@Blackghost test2]# awk '{print NR;print FNR;print $0;}' aaa
1   //NR
1   //FNR
1111:23434:zhang
2
2
hoadsf:asdf:ccc
```
例b

```
[root@Blackghost test2]# awk '{print NR;print FNR;print $0;}' aaa ccc
1
1
1111:23434:zhang
2    //NR
2    //FNR
hoadsf:asdf:ccc
3    //NR
1    //FNR   下面的数据是来自ccc，所以NFR重置为1
1111:23434:zhang
4
2
hoadsf:asdf:ccc
5
3
tank:zhang:x20342
6
4
ying:zhasdf:72342
7
5
hosa:asdfa:2345sdf
```

例3
只显示出现过一次的记录

```
# cat aaa

59314
46791
59992
60311
60134
59992
60311
97343

# cat aaa | awk '!a[$1]++'


59314
46791
59992
60311
60134
97343
```

例4
   
```
#输出/etc/passwd中关于root的第二个位置的内容
# cat /etc/passwd |grep root |awk -F ':' '{print $2}'
```

例5
  

处理前:

```
[root@practice ~]# ip addr show 
1: lo:  mtu 16436 qdisc noqueue 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet 10.99.133.33/32 scope global lo
2: eth2:  mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether 0:1b:21:48:3e:b3 brd ff:ff:ff:ff:ff:ff
    inet 172.20.33.44/23 brd 172.20.33.255 scope global eth2
3: eth3:  mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether 00:1b:31:39:3e:2c brd ff:ff:ff:ff:ff:ff
4: eth0:  mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether 00:25:29:09:8e:f2 brd ff:ff:ff:ff:ff:ff
    inet 228.215.154.140/26 brd 228.215.154.191 scope global eth0
5: eth1:  mtu 1500 qdisc pfifo_fast qlen 1000
    link/ether 00:25:09:09:8e:f3 brd ff:ff:ff:ff:ff:ff
    inet 228.215.154.150/26 brd228.215.154.191 scope global eth1
```

处理后

```
[root@practice ~]# ip addr show | awk 'BEGIN{FS="[/ ]+";OFS=" -- "}$2~"eth"{$3~"NO-CARRIER"?a=0:a=1}$NF~"eth"&&a{print $NF,$3}' 
eth2 -- 172.20.33.44
eth0 -- 228.215.154.140
eth1 -- 228.215.154.150

获取本机上网络接口上可用的公网IP地址

BEGIN{FS="[/ ]+";OFS=" -- "} 是指把一个或多个空格或者/作为读取文本时的字段分隔符,把" -- "作为执行完后的输出字段分隔符

$2~"eth"{$3~"NO-CARRIER"?a=0:a=1}找到第二个字段匹配到"eth"的行并判断第三个字段是否匹配到"NO-CARRIER",匹配到则a=0,否则a=1

$NF~"eth"&&a将最后一个字段和a相与,结果为真则打印最后一个字段和第三个字段,否则不处理
```

例6
   

awk取出last命令结果中非空行中的每

```
[root@practice ~]# last |awk '$0!=""&&$2!~"boot"&&$3~"[[:digit:]]"{ips[$3]++}END{for(i in ips)printf "%18-s%10-d\n",i,ips[i]}' 
172.20.33.1       1         
172.20.33.95      7         
192.168.2.100     6         
192.168.2.101     3         
172.20.33.26      1         
192.168.2.102     8         
172.20.32.123     7         
192.168.140.1     2         
172.20.33.93      1       

&&表示 且,与

$0!=""表示排除结果中的空行

$2!~"boot"表示排除重启的记录

$3~"[[:digit:]]表示第三个字段匹配数字而不是字符

{ips[$3]++}表示把第三个字段即IP地址作为下标,组成一个数组ips,IP地址每出现一次,其出现次数累加一

END{for(i in ips)printf "%18-s%10-d\n",i,ips[i]}

表示从数组中获取每个IP地址,及其出现的次数,并定义对齐方式和变量类型打印出来
```

例7
   

```
#将passwd中的第三列放到test中
[root@localhost ~]# awk -F: '{if(NR>=10 && NR<=20) print $3 }' /etc/passwd > test
You have mail in /var/spool/mail/root
[root@localhost ~]# cat test
10
11
12
13
14
99
81
113
69
32
499
```


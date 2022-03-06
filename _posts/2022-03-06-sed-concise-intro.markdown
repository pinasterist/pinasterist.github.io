---
layout:     post
title:      "SED简单介绍"
subtitle:   "常用的SED命令"
date:       2022-03-06
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - sed
    - reg

---

常用SED命令：

## sed命令行参数

```
sed -h
 -n, --quiet, --silent    取消自动打印模式空间
 -e 脚本, --expression=脚本   添加“脚本”到程序的运行列表
 -f 脚本文件, --file=脚本文件  添加“脚本文件”到程序的运行列表
 --follow-symlinks    直接修改文件时跟随软链接
 -i[扩展名], --in-place[=扩展名]    直接修改文件(如果指定扩展名就备份文件)
 -l N, --line-length=N   指定“l”命令的换行期望长度
 --posix  关闭所有 GNU 扩展
 -r, --regexp-extended  在脚本中使用扩展正则表达式
 -s, --separate  将输入文件视为各个独立的文件而不是一个长的连续输入
 -u, --unbuffered  从输入文件读取最少的数据，更频繁的刷新输出
 --help     打印帮助并退出
 --version  输出版本信息并退出
 -a ∶新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
 -c ∶取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
 -d ∶删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
 -i ∶插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
 -p ∶列印，亦即将某个选择的资料印出。通常 p 会与参数 sed -n 一起运作～
 -s ∶取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法
```

## sed命令

```
a\\ 在当前行下面插入文本。
i\\ 在当前行上面插入文本。
c\\ 把选定的行改为新的文本。
d 删除，删除选择的行。
D 删除模板块的第一行。
s 替换指定字符
h 拷贝模板块的内容到内存中的缓冲区。
H 追加模板块的内容到内存中的缓冲区。
g 获得内存缓冲区的内容，并替代当前模板块中的文本。
G 获得内存缓冲区的内容，并追加到当前模板块文本的后面。
l 列表不能打印字符的清单。
n 读取下一个输入行，用下一个命令处理新的行而不是用第一个命令。
N 追加下一个输入行到模板块后面并在二者间嵌入一个新行，改变当前行号码。
p 打印模板块的行。
P(大写) 打印模板块的第一行。
q 退出Sed。
b lable 分支到脚本中带有标记的地方，如果分支不存在则分支到脚本的末尾。
r file 从file中读行。
t label if分支，从最后一行开始，条件一旦满足或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
T label 错误分支，从最后一行开始，一旦发生错误或者T，t命令，将导致分支到带有标号的命令处，或者到脚本的末尾。
w file 写并追加模板块到file末尾。 
W file 写并追加模板块的第一行到file末尾。 
! 表示后面的命令对所有没有被选定的行发生作用。 
= 打印当前行号码。 
# 把注释扩展到下一个换行符以前。 
```

## sed替换标记

```
g 表示行内全面替换。 
p 表示打印行。 
w 表示把行写入一个文件。 
x 表示互换模板块中的文本和缓冲区中的文本。 
y 表示把一个字符翻译为另外的字符（但是不用于正则表达式）
\\1 子串匹配标记
& 已匹配字符串标记
```

## sed元字符集

```
^ 匹配行开始，如：/^sed/匹配所有以sed开头的行。
$ 匹配行结束，如：/sed$/匹配所有以sed结尾的行。
. 匹配一个非换行符的任意字符，如：/s.d/匹配s后接一个任意字符，最后是d。
* 匹配0个或多个字符，如：/*sed/匹配所有模板是一个或多个空格后紧跟sed的行。
[] 匹配一个指定范围内的字符，如/[sS]ed/匹配sed和Sed。 
[^] 匹配一个不在指定范围内的字符，如：/[^A-RT-Z]ed/匹配不包含A-R和T-Z的一个字母开头，紧跟ed的行。
\\(..\\) 匹配子串，保存匹配的字符，如s/\\(love\\)able/\\1rs，loveable被替换成lovers。
& 保存搜索字符用来替换其他字符，如s/love/**&**/，love这成**love**。
\\< 匹配单词的开始，如:/\\\\> 匹配单词的结束，如/love\\>/匹配包含以love结尾的单词的行。
x\\{m\\} 重复字符x，m次，如：/0\\{5\\}/匹配包含5个0的行。
x\\{m,\\} 重复字符x，至少m次，如：/0\\{5,\\}/匹配至少有5个0的行。
x\\{m,n\\} 重复字符x，至少m次，不多于n次，如：/0\\{5,10\\}/匹配5~10个0的行。
```

## sed特殊字符

```
[:alnum:] 字母数字 [a-z A-Z 0-9]
[:alpha:] 字母 [a-z A-Z]
[:blank:] 空格或制表键
[:cntrl:] 任何控制字符
[:digit:] 数字 [0-9]
[:graph:] 任何可视字符（无空格）
[:lower:] 小写 [a-z]
[:print:] 非控制字符
[:punct:] 标点字符
[:space:] 空格
[:upper:] 大写 [A-Z]
[:xdigit:] 十六进制数字 [0-9 a-f A-F]
```

## 测试

* 测试文件

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

* 测试正则

```
# 把test文件中的root替换成tankzhang，只不过只替换一次及终止在这一行的操作，转到下一行
sed 's/root/tankzhang/' test |grep tank
# 用tankzhang把文件test中的root全部替换掉，请注意g这个字母，global的缩写
sed 's/root/tankzhang/g' test |grep zhang
# 加了-n p后表示只打印那些发生替换的行（部分替换），上面的例子，我并没有加上grep
sed -n 's/root/tankzhang/p' test
# 加了-n pg后表示只打印那些发生替换的行（全部替换），上面的例子，我并没有加上grep
sed -n 's/root/tankzhang/pg' test
# 在第二行，到第八行之间，替换以zhang开头的行，用ying来替换，并显示替换的行
cat test | sed -ne '2,8s/^zhang/ying/gp'
# 当有多个命令要执行时，可以用分号来分开，并且分隔符可以自定义，默认是／。上面的例子意思是在第二行，到第八行之间，替换以zhang开头的行，用ying来替换，在5，到10间，用goodbay来替换dbus，并显示替换的行
cat test | sed -n  '2,8s/^zhang/ying/gp;5,10s#dbus#goodbay#gp'
# 这个例子根上面的那个例子一样，只不过有一点不同，那就是-e来充当了分号的作用，-e也能分割多个命令。
cat test | sed -ne '2,8s/zhang/ying/gp' -ne  '5,10s#dbus#goodbay#gp'
# 正则的用法，在sed里面用括号的话要加上\的，不然会报错的。
sed -ne '2,8s/^\(zhangy\)/\1ing/gp' test
# ＆的用处是，在找到的字符串后加上＆后面的字符串，zhang后都加上了ying
sed -ne '2,15s/zhang/&ying/gp' test
# 这个例子是说，在以zhang开头的行开始，到匹配Po的行结束，在他们之间进行替换
sed -ne '/^zhang/,/Po/s/zhang/ying/gp' test
# n;这里的n是next的缩写，找到root的行后，将其下一行的中的bin换成tank
sed  '/root/{n;s/bin/tank/}' test
# y的作用是将匹配的字符换成大写，不过替换字符和被替换字符长度要一样
sed -e '1,2y/root/ROOT/' test
# h的作用是将找到的行，放到一个缓存区，G的作用是将缓存区中的内容放到最后一行
sed -e '/root/h' -e '$G' test
# 行替换，用匹配root的行，来替换匹配zhangy的行
sed -e '/root/h' -e '/zhangy/g' test
# 这个例子是说，在以zhang开头的行开始，到匹配Po的行结束，在他们之间进行替换
sed -ne '/^zhang/,/Po/s/zhang/ying/gp' test
# 3q的意思是到第三行的时候，退出
sed -e 's/bin/tank/g;3q' test
# 特殊匹配，匹配数字别忘了中括号外面还有一个中括号。
sed -ne '2,15s/zhangy.*[[:digit:]]/=======/gp'  test
```

```
# 删除1，14行
sed -e '1,14d' test
# 删除4以后的行，包括第4行，把$当成最大行数就行了。
sed -e '4,$d' test
# 删除包括false的行，或者包括bash的行，别忘了加\
sed -e '/\(false\|bash\)$/d' test
# 删除从匹配root的行，到匹配以test开头的行，中间的行
sed -e '/root/,/^test/d' test
```

```
# 读取test2的内容，并将其写入到匹配行的下面
sed -e '/^root/r test2' test
# 将匹配数字的行，写入test2中
sed '/[[:digit:]]/w test2' test
# 将要插入的东西，插入匹配行的下面
sed  '/root/a\\ ===aaaa====' test
# 正好根a相反，将要插入的东西，插入到匹配行的上面
sed '/^daemon/i\\=================' test
```

```
# 取得一个文件(或目录)路径的父目录,s@@@为替换格式,\(/.*/\)是指一个"/"后面跟了任意字符又跟了一个"/",其中\(\)是用来把匹配内容作为一个整体后向引用,[^/]\{1,\}是指一个非"/"字符出现了一次,两次,或多次;/\?是指"/"出现了0次或1次,\1是后向引用前面匹配的内容
echo "/usr/local/bin/" |sed 's@\(/.*/\)[^/]\{1,\}/\?@\1@'
# 使用扩展正则表达式后,亦可如此:
echo "/etc/rc.d/rc.sysinit" | sed -r 's@(/.*/)[^/]+/?@\1@'
```

```
# 替换第3行的每个my为your，若没有则无替换
sed \"3s/my/your/g\" pets.txt 
# 替换每行第3个my
sed \"s/my/your/3\" pets.txt 
# 只替换第3行的第1个my
sed \"3s/my/your/1\" pets.txt 
# 替换3~6行，每个my
sed \"3,6s/my/your/g\" pets.txt
# 只替换每行的第2个以后的my
sed \'s/my/your/3g\' pets.txt
```

```
# 注释匹配到的整行
sed -i 's/^[^#].*netmask/#&/' change_network.yml 
```

```
# 把 MyScanActivity.java 文件中 /*...*/之间的内容删除
sed -i '/\/\*/,/\*\//'D ./MyScanActivity.java
```

```
sed -rn 's#^UUID=(.*) /.*#\1#p' /etc/fstab
```

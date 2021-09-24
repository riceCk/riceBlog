---
title: Linux系统学习壹
categories: Linux从入门到精通
tags: Linux指令
date: 2018-10-18 22:32:00
---
[文档学习引用鸟叔的Linux私房菜](http://linux.vbird.org/new_linux.php)

# 档案与目录管理
## 创建目录
```bash
mkdir test
mkdir -p test1/test2/test3/test4
mkdir -m 711 test2
```

## 修改文件权限详情
```bsah
mkdir -m 723 test1 
// drwx-w--wx test1
chmod 777 test1
// drwxrwxrwx test1
chmod u=rwx,g=rx,o=r test1
// drwxr-xr-- text1
chmod o+w test1
// drwxr-xrw- text1
chmod a-r test1
// d-wx--x-w- text1
```

## cp:文件的复制
**例题资料来自鸟哥Linux私房菜，感谢！**
```bash
范例一：用root身份，将家目录下的.bashrc复制到/ tmp下，并更名为bashrc
[root@study ~]# cp ~/.bashrc /tmp/bashrc
[root@study ~]# cp -i ~/.bashrc /tmp/bashrc
cp: overwrite `/tmp/bashrc'? n  <==n不覆盖，y为覆盖
# 重复做两次动作，由于/tmp地下已经存在bashrc了，加上 -i 选项之后，
# 则在覆盖前会询问使用者是否确定！可以按下n或者y来二次确认！

范例二：变换目录到/tmp，并将/var/log/wtmp复制到/tmp且观察属性：
[root@study ~]# cd /tmp
[root@study tmp]# cp /var/log/wtmp . <==想要复制到目前的目录，最后的 . 不要忘
[root@study tmp]# ls -l /var/log/wtmp wtmp
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
-rw-r--r--. 1 root root 28416 Jun 11 19:01 wtmp
# 注意上面的特殊字体，在不加任何选项的情况下，档案的某些属性/权限会改变；
# 这是个很重要的特性！要注意喔！还有，连档案建立的时间也不一样了！
# 那如果你想要将档案的所有特性都一起复制过来该怎办？可以加上 -a 喔！如下所示：

[root@study tmp]# cp -a /var/log/wtmp wtmp_2
[root@study tmp]# ls -l /var/log/wtmp wtmp_2
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 /var/log/wtmp
-rw-rw-r--. 1 root utmp 28416 Jun 11 18:56 wtmp_2
# 整个资料特性完全一模一样ㄟ！真是不赖～这就是 -a 的特性！
```
这个 cp 的功能很多，由于我们常常会进行一些资料的复制，所以也会常常用到这个指令的。一般来说，我们如果去复制别人的资料(当然，该档案你必须要有read 的权限才行啊！ ^_^) 时， 总是希望复制到的资料最后是我们自己的，所以，在预设的条件中， cp 的来源档与目的档的权限是不同的，目的档的拥有者通常会是指令操作者本身。举例来说， 上面的范例二中，由于我是 root 的身份，因此复制过来的档案拥有者与群组就改变成为 root 所有了！这样说，可以明白吗？ ^_^

由于具有这个特性，因此当我们在进行备份的时候，某些需要特别注意的特殊权限档案， 例如密码档(/etc/shadow) 以及一些设定档，就不能直接以cp 来复制，而必须要加上-a 或者是-p 等等可以完整复制档案权限的选项才行！另外，如果你想要复制档案给其他的使用者， 也必须要注意到档案的权限(包含读、写、执行以及档案拥有者等等)， 否则，其他人还是无法针对你给予的档案进行修订的动作喔！注意注意！
```bash
范例三：复制 /etc/ 这个目录下的所有内容到 /tmp 底下
[root@study tmp]# cp /etc/ /tmp
# cp: omitting directory /etc   <== 如果是目录则不能直接复制，要加上 -r 的选项
[root@study tmp]# cp -r /etc/ /tmp
# 还是要再次的强调喔！ -r 是可以复制目录，但是，档案与目录的权限可能会被改变
# 所以，也可以利用『 cp -a /etc /tmp 』来下达指令喔！尤其是在备份的情况下！

范例四：将范例一复制的 bashrc 建立一个连结档 (symbolic link)
[root@study tmp]# ls -l bashrc
-rw-r--r--. 1 root root 176 Jun 11 19:01 bashrc  <==先观察一下档案情况
[root@study tmp]# cp -s bashrc bashrc_slink
[root@study tmp]# cp -l bashrc bashrc_hlink
[root@study tmp]# ls -l bashrc*
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc         <==与原始档案不太一样了！
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc_hlink
lrwxrwxrwx. 1 root root   6 Jun 11 19:06 bashrc_slink -> bashrc
```
范例四可有趣了！使用 -l 及 -s 都会建立所谓的连结档(link file)，但是这两种连结档却有不一样的情况。这是怎么一回事啊？那个 -l 就是所谓的实体连结(hard link)，至于 -s 则是符号连结(symbolic link)， 简单来说，bashrc_slink 是一个『捷径』，这个捷径会连结到bashrc去！所以你会看到档名右侧会有个指向(->)的符号！

至于bashrc_hlink档案与bashrc的属性与权限完全一模一样，与尚未进行连结前的差异则是第二栏的link数由1变成2了！

```bash
范例五：若 ~/.bashrc 比 /tmp/bashrc 新才复制过来
[root@study tmp]# cp -u ~/.bashrc /tmp/bashrc
# 这个 -u 的特性，是在目标档案与来源档案有差异时，才会复制的。
# 所以，比较常被用于『备份』的工作当中喔！ ^_^

范例六：将范例四造成的 bashrc_slink 复制成为 bashrc_slink_1 与bashrc_slink_2
[root@study tmp]# cp bashrc_slink bashrc_slink_1
[root@study tmp]# cp -d bashrc_slink bashrc_slink_2
[root@study tmp]# ls -l bashrc bashrc_slink*
-rw-r--r--. 2 root root 176 Jun 11 19:01 bashrc
lrwxrwxrwx. 1 root root   6 Jun 11 19:06 bashrc_slink -> bashrc
-rw-r--r--. 1 root root 176 Jun 11 19:09 bashrc_slink_1            <==与原始档案相同
lrwxrwxrwx. 1 root root   6 Jun 11 19:10 bashrc_slink_2 -> bashrc  <==是连结档！
# 这个例子也是很有趣喔！原本复制的是连结档，但是却将连结档的实际档案复制过来了
# 也就是说，如果没有加上任何选项时，cp复制的是原始档案，而非连结档的属性！
# 若要复制连结档的属性，就得要使用 -d 的选项了！如 bashrc_slink_2 所示。

范例七：将家目录的 .bashrc 及 .bash_history 复制到 /tmp 底下
[root@study tmp]# cp ~/.bashrc ~/.bash_history /tmp
# 可以将多个资料一次复制到同一个目录去！最后面一定是目录！
```

## MV:移动档案与目录，或者更名
```bash
[root@study ~]# mv [-fiu] source destination
[root@study ~]# mv [options] source1 source2 source3 .... directory
选项与参数：
-f ：force 强制的意思，如果目标档案已经存在，不会询问而直接覆盖；
-i ：若目标档案 (destination) 已经存在时，就会询问是否覆盖！
-u ：若目标档案已经存在，且 source 比较新，才会更新 (update)

范例一：复制一档案，建立一目录，将档案移动到目录中
[root@study ~]# cd /tmp
[root@study tmp]# cp ~/.bashrc bashrc
[root@study tmp]# mkdir mvtest
[root@study tmp]# mv bashrc mvtest
# 将某个档案移动到某个目录去，就是这样做！

范例二：将刚刚的目录名称更名为 mvtest2
[root@study tmp]# mv mvtest mvtest2 <== 这样就更名了！简单～
# 其实在 Linux 底下还有个有趣的指令，名称为 rename ，
# 该指令专职进行多个档名的同时更名，并非针对单一档名变更，与mv不同。请man rename。

范例三：再建立两个档案，再全部移动到 /tmp/mvtest2 当中
[root@study tmp]# cp ~/.bashrc bashrc1
[root@study tmp]# cp ~/.bashrc bashrc2
[root@study tmp]# mv bashrc1 bashrc2 mvtest2
# 注意到这边，如果有多个来源档案或目录，则最后一个目标档一定是『目录！ 』
# 意思是说，将所有的资料移动到该目录的意思！
```
这是搬移 (move) 的意思！当你要移动档案或目录的时后，呵呵！这个指令就很重要啦！同样的，你也可以使用 -u ( update )来测试新旧档案，看看是否需要搬移啰！另外一个用途就是『变更档名！ 』，我们可以很轻易的使用 mv 来变更一个档案的档名呢！不过，在 Linux 才有的指令当中，有个 rename ， 可以用来更改大量档案的档名，你可以利用 man rename 来查阅一下，也是挺有趣的指令喔！

# 档案内容查阅
* cat由第一行开始显示档案内容
* tac从最后一行开始显示，可以看出tac是cat的倒着写
* nl显示的时候，顺道输出行号
* more一页一页的显示档案内容
* less与more类似，但是比more更好的是，他可以往前翻页
* head只看都几行
* tail只看尾巴几行
* od以二进位的方式读取档案内容

## 直接监视档案内容 
### cat
[root@study ~]# cat [-AbEnTv] 
选项与参数：
-A ：相当于-vET 的整合选项，可列出一些特殊字符而不是空白而已；
-b ：列出行号，仅针对非空白行做行号显示，空白行不标行号！
-E ：将结尾的断行字元$ 显示出来；
-n ：列印出行号，连同空白行也会有行号，与-b 的选项不同；
-T ：将[tab] 按键以^I 显示出来；
-v ：列出一些看不出来的特殊字符
```bash
范例一：检阅/etc/issue这个档案的内容 
[root@study ~]# cat /etc/issue
\S
Kernel \r on an \m

范例二：承上题，如果还要加印行号呢？
[root@study ~]# cat -n /etc/issue
     1 \S
     2 Kernel \r on an \m
     3
```
### tac（反向列式）
```bash
[root@study ~]# tac /etc/issue

Kernel \r on an \m
\S
```
### nl（添加行号列印）
[root@study ~]# nl [-bnw]档案
选项与参数：
-b: 指定行号指定的方式，主要有两种
    -ba：表示不论是否为空行，也同样列出行号（类似cat -n）
    -bt：如果有空行，空的哪一行不要列出行号（预设值）
-n: 列出行号表示的方法，主要有三种：
    -n ln：行号在荧幕的最左方显示
    -n rn: 行号在自己栏位最右方显示，且不加0
    -n rz：行号在自己栏位的最右方显示，且加0
```bash
范例一：用nl列出/etc/issue的内容 
[root@study ~]# nl /etc/issue
     1 \S
     2 Kernel \r on an \m

# 注意看，这个档案其实有三行，第三行为空白(没有任何字元)，
# 因为他是空白行，所以nl 不会加上行号喔！如果确定要加上行号，可以这样做：

[root@study ~]# nl -ba /etc/issue
     1 \S
     2 Kernel \r on an \m
     3
# 呵呵！行号加上来啰～那么如果要让行号前面自动补上0 呢？可这样

[root@study ~]# nl -ba -n rz /etc/issue
000001 \S
000002 Kernel \r on an \m
000003
# 嘿嘿！自动在自己栏位的地方补上0 了～预设栏位是六位数，如果想要改成3 位数？

[root@study ~]# nl -ba -n rz -w 3 /etc/issue
001 \S
002 Kernel \r on an \m
003
# 变成仅有3 位数啰～
```

## 可翻页检视
### more （一页一页翻动）
* 空白键（space）：代表向下翻一页
* Enter：代表向下翻一行
* /字符：代表在这个显示的内容当中，向下搜寻字符这个关键词
* :f：立刻显示出档名以及目前显示的行数
* q：代表立刻离开more，不再显示该档案内容
* b：代表往回翻页，不过这个动作只对档案有用，对管线没用

```bash
[root@study ~]# more /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath(5) man page.
#
.....(中间省略)..... 
--More--(28%)   <==重点在这一行喔！你的游标也会在这里等待你的指令
```

### less（一页一页翻动）
* 空格键：向下翻动一页
* 【pagedown】: 向下翻动一页
* 【pageup】：向上翻动一页
* /字串：向下搜寻字串的功能
* ？字串：向上搜寻字串功能
* n：重复前一个搜寻（与/或？有关！）
* N：反向的重复前一个搜寻（与/或？有关！）
* g：前进的这一个资料的第一行去
* G：前进到这个资料的最后一行去（注意大小写）
* q：离开less这个程式

```bash
[root@study ~]# less /etc/man_db.conf
#
#
# This file is used by the man-db package to configure the man and cat paths.
# It is also used to provide a manpath for those without one by examining
# their PATH environment variable. For details see the manpath(5) man page.
#
.....(中间省略)..... 
: <==这里可以等待你输入指令！   
```

## 资料撷取
### head（取出前面几行）
```bash
[root@study ~]# head [-n number]档案
选项与参数：
-n ：后面接数字，代表显示几行的意思

[root@study ~]# head /etc/man_db.conf 
#预设的情况中，显示前面十行！若要显示前20行，就得要这样： 
[root@study ~]# head -n 20 /etc/man_db.conf

范例：如果后面100行的资料都不列印，只列印/etc/man_db.conf的前面几行，该如何是好？
[root@study ~]# head -n -100 /etc/man_db.conf
```
### tail（取出后面几行）
```bash
[root@study ~]# tail [-n number]档案
选项与参数：
-n ：后面接数字，代表显示几行的意思
-f ：表示持续侦测后面所接的档名，要等到按下[ctrl]-c才会结束tail的侦测

[root@study ~]# tail /etc/man_db.conf 
#预设的情况中，显示最后的十行！若要显示最后的20行，就得要这样： 
[root@study ~]# tail -n 20 /etc/man_db.conf

范例一：如果不知道/etc/man_db.conf有几行，却只想列出100行以后的资料时？
[root@study ~]# tail -n +100 /etc/man_db.conf

范例二：持续侦测/var/log/messages的内容 
[root@study ~]# tail -f /var/log/messages 
  <==要等到输入[ctrl]-c之后才会离开tail这个指令的侦测！
```

#  档案与档案系统的压缩,打包与备份
## 压缩档案的用途与技术
* Z compress 程式压缩的档案；
* zip zip 程式压缩的档案；
* gz gzip 程式压缩的档案；
* bz2 bzip2 程式压缩的档案；
* xz xz 程式压缩的档案；
* tar tar 程式打包的资料，并没有压缩过；
* tar.gz tar 程式打包的档案，其中并且经过gzip 的压缩
* tar.bz2 tar 程式打包的档案，其中并且经过bzip2 的压缩
* tar.xz tar 程式打包的档案，其中并且经过xz 的压缩

### gzip, zcat/zmore/zless/zgrep
方法
```bash
[dmtsai@study ~]$ gzip [-cdtv#]档名 
[dmtsai@study ~]$ zcat档名.gz 
选项与参数：
-c ：将压缩的资料输出到萤幕上，可透过资料流重导向来处理；
-d ：解压缩的参数；
-t ：可以用来检验一个压缩档的一致性～看看档案有无错误；
-v ：可以显示出原档案/压缩档案的压缩比等资讯；
-# ：# 为数字的意思，代表压缩等级，-1 最快，但是压缩比最差、-9 最慢，但是压缩比最好！预设是-6
```
范例一：找出/etc底下(不含子目录)容量最大的档案，并将它复制到/tmp ，然后以gzip压缩 
`-v`压缩会替换掉原本的文件
```bash
[dmtsai@study ~]$ cd /tmp 
[dmtsai@study tmp]$ cp /etc/services . 
[dmtsai@study tmp]$ gzip -v services   #压缩指令
services: 79.7% -- replaced with services.gz
[dmtsai@study tmp]$ ll /etc/services /tmp/services* 
-rw-r--r--. 1 root root    670293 Jun 7 2013 /etc/services
-rw-r--r--. 1 dmtsai dmtsai 136088 Jun 30 18:40 /tmp/services.gz
```
范例二：由于services是文字档，请将范例一的压缩档的内容读出来！
```bash
[dmtsai@study tmp]$ zcat services.gz 
#由于services这个原本的档案是是文字档，因此我们可以尝试使用zcat/zmore/zless去读取！
# 此时萤幕上会显示servcies.gz 解压缩之后的原始档案内容！
```
范例三：将范例一的档案解压缩 
```bash
[dmtsai@study tmp]$ gzip -d services.gz 
# 不要使用gunzip这个指令，不好背！使用gzip -d来进行解压缩！
# 与gzip 相反， gzip -d 会将原本的.gz 删除，回复到原本的services 档案。
```
范例四：将范例三解开的services用最佳的压缩比压缩，并保留原本的档案 
```bash
[dmtsai@study tmp]$ gzip -9 -c services > services.gz
```
### bzip2, bzcat/bzmore/bzless/bzgrep
先下载bzip2
```bash
yum install bzip2
```
方法
```bash
[dmtsai@study ~]$ bzip2 [-cdkzv#]档名 
[dmtsai@study ~]$ bzcat档名.bz2 
选项与参数：
-c ：将压缩的过程产生的资料输出到萤幕上！
-d ：解压缩的参数
-k ：保留原始档案，而不会删除原始的档案喔！
-z ：压缩的参数(预设值，可以不加)
-v ：可以显示出原档案/压缩档案的压缩比等资讯；
-# ：与gzip 同样的，都是在计算压缩比的参数， -9 最佳， -1 最快！
```
范例一：将刚刚gzip范例留下来的/tmp/services以bzip2压缩 
```bash
[dmtsai@study tmp]$ bzip2 -v services
  services: 5.409:1, 1.479 bits/byte, 81.51% saved, 670293 in, 123932 out.
[dmtsai@study tmp]$ ls -l services* 
-rw-r--r--. 1 dmtsai dmtsai 123932 Jun 30 18:40 services.bz2
-rw-rw-r--. 1 dmtsai dmtsai 135489 Jun 30 18:46 services.gz
 #此时services会变成services.bz2之外，你也可以发现bzip2的压缩比要较gzip好喔！！
# 压缩率由gzip 的79% 提升到bzip2 的81% 哩！
```
范例二：将范例一的档案内容读出来！
```bash
[dmtsai@study tmp]$ bzcat services.bz2
```
范例三：将范例一的档案解压缩 
```bash
[dmtsai@study tmp]$ bzip2 -d services.bz2
```
范例四：将范例三解开的services用最佳的压缩比压缩，并保留原本的档案 
```bash
[dmtsai@study tmp]$ bzip2 -9 -c services > services.bz2
```

###  xz, xzcat/xzmore/xzless/xzgrep
方法
```bash
[dmtsai@study ~]$ xz [-dtlkc#]档名 
[dmtsai@study ~]$ xcat档名.xz 
选项与参数：
-d ：就是解压缩啊！
-t ：测试压缩档的完整性，看有没有错误
-l ：列出压缩档的相关资讯
-k ：保留原本的档案不删除～
-c ：同样的，就是将资料由萤幕上输出的意思！
-# ：同样的，也有较佳的压缩比的意思！
```
范例一：将刚刚由bzip2所遗留下来的/tmp/services透过xz来压缩！
```bash
[dmtsai@study tmp]$ xz -v services
services (1/1)
  100 % 97.3 KiB / 654.6 KiB = 0.149

[dmtsai@study tmp]$ ls -l services* 
-rw-rw-r--. 1 dmtsai dmtsai 123932 Jun 30 19:09 services.bz2
-rw-rw-r--. 1 dmtsai dmtsai 135489 Jun 30 18:46 services.gz
-rw-r--r--. 1 dmtsai dmtsai 99608 Jun 30 18:40 services.xz
 #容量又进一步下降的更多耶！好棒的压缩比！
```
范例二：列出这个压缩档的资讯，然后读出这个压缩档的内容 
```bash
[dmtsai@study tmp]$ xz -l services.xz
Strms Blocks Compressed Uncompressed Ratio Check Filename
    1 1 97.3 KiB 654.6 KiB 0.149 CRC64 services.xz
# 竟然可以列出这个档案的压缩前后的容量，真是太人性化了！这样观察就方便多了！

[dmtsai@study tmp]$ xzcat services.xz
```
范例三：将他解压缩吧！
```bash
[dmtsai@study tmp]$ xz -d services.xz
```

范例四：保留原档案的档名，并且建立压缩档！
```bash
[dmtsai@study tmp]$ xz -k services
```

## 打包指令： tar
### tar
选项与参数：
```bash
[dmtsai@study ~]$ tar [-z|-j|-J] [cv] [-f待建立的新档名] filename...  <==打包与压缩 
[dmtsai@study ~]$ tar [ -z|-j|-J] [tv] [-f既有的tar档名]              <==察看档名 
[dmtsai@study ~]$ tar [-z|-j|-J] [xv] [ -f既有的tar档名] [-C目录]     <==解压缩
-c ：建立打包档案，可搭配-v 来察看过程中被打包的档名(filename)
-t ：察看打包档案的内容含有哪些档名，重点在察看『档名』就是了；
-x ：解打包或解压缩的功能，可以搭配-C (大写) 在特定目录解开
      特别留意的是， -c, -t, -x 不可同时出现在一串指令列中。
-z ：透过gzip 的支援进行压缩/解压缩：此时档名最好为*.tar.gz
-j ：透过bzip2 的支援进行压缩/解压缩：此时档名最好为*.tar.bz2
-J ：透过xz 的支援进行压缩/解压缩：此时档名最好为*.tar.xz
      特别留意， -z, -j, -J 不可以同时出现在一串指令列中
-v ：在压缩/解压缩的过程中，将正在处理的档名显示出来！
-f filename：-f 后面要立刻接要被处理的档名！建议-f 单独写一个选项啰！(比较不会忘记)
-C 目录：这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。

其他后续练习会使用到的选项介绍：
-p(小写) ：保留备份资料的原本权限与属性，常用于备份(-c)重要的设定档
-P(大写) ：保留绝对路径，亦即允许备份资料中含有根目录存在之意；
--exclude=FILE：在压缩的过程中，不要将FILE 打包！ 
```
### 使用tar 加入-z, -j 或-J 的参数备份/etc/ 目录
打包gz格式 & 查看
```bash
[dmtsai@study ~]$ su -   #因为备份/etc需要root的权限，否则会出现一堆错误 
[root@study ~]# time tar -zpcv -f /root/etc.tar.gz /etc 
# 打包
[root@study ~]# tar -ztv -f /root/etc.tar.gz
# 查看

# 由于加上-v 这个选项，因此正在作用中的档名就会显示在萤幕上。
# 如果你可以翻到第一页，会发现出现上面的错误讯息！底下会讲解。
# 至于-p 的选项，重点在于『保留原本档案的权限与属性』之意。
```
打包bz2 & 查看
```bash
[root@study ~]# time tar -jpcv -f /root/etc.tar.bz2 /etc 
# 打包
[root@study ~]# tar -jtv -f /root/etc.tar.bz2 
# 查看
```
打包xz & 查看
```bash
[root@study ~]# time tar -Jpcv -f /root/etc.tar.xz /etc 
# 显示的讯息会跟上面一模一样啰！不过时间会花比较多！使用了-J 时，会花更多时间
[root@study ~]# tar -Jtv -f /root/etc.tar.xz
# 查看

[root@study ~]# ll /root/etc* 
-rw-r--r--. 1 root root 6721809 Jul 1 00:16 /root/etc.tar.bz2
-rw-r--r--. 1 root root 7758826 Jul 1 00:14 /root/etc.tar.gz
-rw-r--r--. 1 root root 5511500 Jul 1 00:16 /root/etc.tar.xz
[root@study ~]# du -sm /etc 
28 /etc   #实际目录约占有28MB的意思！
```

### 将备份的资料解压缩，并考虑特定目录的解压缩动作(-C 选项的应用）
压缩在当前目录
```bash
[root@study ~]# tar -jxv -f /root/etc.tar.bz2 
```
压缩到tmp目录
```bash
[root@study ~]# tar -jxv -f /root/etc.tar.bz2 -C /tmp 
```
压缩文件其中一个文件
```bash
[root@study ~]# tar -jxv -f /root/etc.tar.bz2 etc /shadow
```

# vim 程式编辑器
## 按键说明
| 按键| 移动游标的方法 |
|:----:|:-----:|
|h 或向左方向键(←) |游标向左移动一个字元 |
|j 或向下方向键(↓)|	游标向下移动一个字元|
|k 或向上方向键(↑) |游标向上移动一个字元 |
|l 或向右方向键(→)|游标向右移动一个字元|
|[Ctrl] + [f]|	萤幕『向下』移动一页，相当于[Page Down]按键|
|[Ctrl] + [b]|萤幕『向上』移动一页，相当于[Page Up]按键|
|0 或功能键[Home]|	这是数字『 0 』：移动到这一列的最前面字元处|
|$ 或功能键[End]|移动到这一列的最后面字元处|
|G|移动到这个档案的最后一列|
|gg|移动到这个档案的第一列，相当于1G啊！|
|n<Enter>|n为数字。游标向下移动n列|
|:n1,n2s/word1/word2/g	|n1与n2为数字。在第n1与n2列之间寻找word1这个字串，并将该字串取代为word2 ！举例来说，在100到200列之间搜寻vbird并取代为VBIRD则：『:100,200s/vbird/VBIRD/g』。|
|:1,$s/word1/word2/g	|从第一列到最后一列寻找word1字串，并将该字串取代为word2 ！|
|:1,$s/word1/word2/gc|从第一列到最后一列寻找word1字串，并将该字串取代为word2 ！且在取代前显示提示字元给使用者确认(confirm)是否需要取代！|

# 正规表示法与文件格式化处理

## grep的一些进阶选项
#### 范例一：搜索regular_express.txt中的the的语句
```bash
[dmtsai@study ~]$ grep -n 'the' regular_express.txt 
8:I can't finish the test.
12: the symbol '*' is represented as start.
15:You are the best is mean you are the no. 1.
16:The world <Happy> is the same with "glad".
18:google is the best tools for search keyword.

[dmtsai@study ~]$ cat regular_express.txt | grep 'the'
I can't finish the test.
the symbol '*' is represented as start.
You are the best is mean you are the no. 1.
The world <Happy> is the same with "glad".
google is the best tools for search keyword.
```
#### 范例二：不分大小写的筛选关键词the
```bash
[dmtsai@study ~]$ grep -in 'the' regular_express.txt 
8:I can't finish the test.
9:Oh! The soup taste good.
12: the symbol '*' is represented as start.
14: The gd software is a library for drafting programs.
15:You are the best is mean you are the no. 1.
16: The world <Happy> is the same with "glad".
18:google is the best tools for search keyword.
```
#### 范例三：利用中括号[]来搜寻集合字元
可以匹配正则类型
```bash
[dmtsai@study ~]$ grep -n 't[ae]st' regular_express.txt 
8:I can't finish the test .
9:Oh! The soup tast e good.

[dmtsai@study ~]$ grep -n 'oo' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
9:Oh! The soup taste good.
18:google is the best tools for search keyword.
19:goooooogle yes!

[dmtsai@study ~]$ grep -n '[^g]oo' regular_express.txt
2:apple is my favorite food.
3:Football game is not use feet only.
18:google is the best tools for search keyword.
19:goooooogle yes!
```
#### 范例四：行首與行尾字元 ^ $
```bash
[dmtsai@study ~]$ grep -n '^the' regular_express.txt
12:the symbol '*' is represented as start.

[dmtsai@study ~]$ grep -n '^[a-z]' regular_express.txt
2:apple is my favorite food.
4:this dress doesn't fit me.
10:motorcycle is cheap than car.
12:the symbol '*' is represented as start.
18:google is the best tools for search keyword.
19:goooooogle yes!
20:go! go! Let's go.

[dmtsai@study ~]$ grep -n '^[^a-zA-Z]' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
21:# I am VBird
# 指令也可以是： grep -n '^[^[:alpha:]]' regular_express.txt
```
#### 范例五： 任意一個字元 . 與重複字元 *
*  .（小数点）：代表‘一定有一个任意字元’的意思
*  *（星星号）：代表‘重复前一个字元，0到无穷多次’的意思，组合形态 
```bash
[dmtsai@study ~]$ grep -n 'g..d' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
9:Oh! The soup taste good.
16:The world <Happy> is the same with "glad".

# 当我们需要‘至少两个o以上的字串’时，就需要ooo*
[dmtsai@study ~]$ grep -n 'ooo*' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
2:apple is my favorite food.
3:Football game is not use feet only.
9:Oh! The soup taste good.
18:google is the best tools for search keyword.
19:goooooogle yes!

# 当我们需要找到g开头与g结尾的字串，当中的可有可无
[dmtsai@study ~]$ grep -n 'g.*g' regular_express.txt
1:"Open Source" is a good mechanism to develop programs.
14:The gd software is a library for drafting programs.
18:google is the best tools for search keyword.
19:goooooogle yes!
20:go! go! Let's go.

# 当我们想要找出‘任意数字’的行列
[dmtsai@study ~]$ grep -n '[0-9][0-9]*' regular_express.txt
5:However, this dress is about $ 3183 dollars.
15:You are the best is mean you are the no. 1.
```

#### 范例六：限定连续RE字符范围{}
```bash
[dmtsai@study ~]$ grep -n 'go\{2,5\}g' regular_express.txt 
18: goog le is the best tools for search keyword.

[dmtsai@study ~]$ grep -n 'go\{2,\}g' regular_express.txt 
18: goog le is the best tools for search keyword.
19: goooooog le yes!
```

## sed 工具
```bash
mtsai@study ~]$ sed [-nefr] [动作] 
选项与参数：
-n ：使用安静(silent)模式。在一般sed 的用法中，所有来自STDIN 的资料一般都会被列出到萤幕上。
      但如果加上-n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
-e ：直接在指令列模式上进行sed 的动作编辑；
-f ：直接将sed 的动作写在一个档案内， -f filename 则可以执行filename 内的sed 动作；
-r ：sed 的动作支援的是延伸型正规表示法的语法。(预设是基础正规表示法语法)
-i ：直接修改读取的档案内容，而不是由萤幕输出。

动作说明： [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作
         是需要在10 到20 行之间进行的，则『 10,20[动作行为] 』

function 有底下这些咚咚：
a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
c ：取代， c 的后面可以接字串，这些字串可以取代n1,n2 之间的行！
d ：删除，因为是删除啊，所以d 后面通常不接任何咚咚；
i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
p ：列印，亦即将某个选择的资料印出。通常p 会与参数sed -n 一起运作～
s ：取代，可以直接进行取代的工作哩！通常这个s 的动作可以搭配正规表示法！
      例如1,20s/old/new/g 就是啦
```
### 以行为单位的新增/删除功能 
#### 范例一：将/etc/passwd的内容列出并且列印行号，同时，请将第2~5行删除
如果只要删除第2行，可以使用『 nl /etc/passwd | sed '2d' 』来达成，至于若是要删除第3到最后一行，则是『 nl /etc /passwd | sed '3,$d' 』的啦，那个钱字号『 $ 』代表最后一行！
```bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2,5d' 
     1   root : x : 0:0 : root:/root :/bin/bash
     6 sync:x:5:0:sync:/sbin:/bin/sync
     7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(后面省略).....
```
#### 范例二：在第二行后(亦即是加在第三行)加上『drink tea?』字样！
如果是要在第二行前呢？『 nl /etc/passwd | sed '2i drink tea' 』就对啦！就是将『 a 』变成『 i 』即可
```bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2a drink tea' 
     1   root : x : 0:0 : root:/root :/bin/bash
     2 bin:x:1:1:bin:/bin:/sbin/nologin
drink tea
     3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(后面省略).....
```
#### 范例三：在第二行后面加入两行字
我们可以发现在第一行的最后面就有\ 存在啦！在多行新增的情况下， \ 是一定要的
```bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2a Drink tea or . .....\ 
> drink beer ?' 
     1   root : x : 0:0 : root:/root :/bin/bash
     2 bin:x:1:1:bin:/bin:/sbin/nologin
Drink tea or ......
drink beer ?
     3 daemon:x:2:2:daemon:/sbin:/sbin/nologin
.....(后面省略).....
```

### 以行为单位的取代与显示功能
####范例四：我想将第2-5行的内容取代成为『No 2-5 number』
```bash
[dmtsai@study ~]$ nl /etc/passwd | sed '2,5c No 2-5 number' 
     1   root : x : 0:0 : root:/root :/bin/bash
No 2-5 number
     6 sync:x:5:0:sync:/sbin:/bin/sync
.....(后面省略).....
```
####范例五：仅列出/etc/passwd档案内的第5-7行
```bash
[dmtsai@study ~]$ nl /etc/passwd | sed -n '5,7p'
     5 lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
     6 sync:x:5:0:sync:/sbin:/bin/sync
     7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
```
### 部分资料的搜寻并取代的功能
sed 's/要被取代的字串/新的字串/g'
#### 范例一：
```bash
步骤二：利用关键字配合grep撷取出关键的一行资料 
[dmtsai@study ~]$ /sbin/ifconfig eth0 | grep 'inet ' 
        inet 192.168.1.100 netmask 255.255.255.0 broadcast 192.168.1.255
 #当场仅剩下一行！要注意， CentOS 7与CentOS 6以前的ifconfig指令输出结果不太相同，
# 鸟哥这个范例主要是针对CentOS 7 以后的喔！接下来，我们要将开始到addr: 通通删除，
# 就是像底下这样：
# inet 192.168.1.100 netmask 255.255.255.0 broadcast 192.168.1.255
# 上面的删除关键在于『 ^.*inet 』啦！正规表示法出现！^_^

步骤三：将IP前面的部分予以删除 
[dmtsai@study ~]$ /sbin/ifconfig eth0 | grep 'inet ' | sed 's/^.*inet //g'
192.168.1.100 netmask 255.255.255.0 broadcast 192.168.1.255
# 仔细与上个步骤比较一下，前面的部分不见了！接下来则是删除后续的部分，亦即：
192.168.1.100   netmask 255.255.255.0 broadcast 192.168.1.255 
#此时所需的正规表示法为：『 ' *netmask.*$ 』就是啦！

步骤四：将IP后面的部分予以删除 
[dmtsai@study ~]$ /sbin/ifconfig eth0 | grep 'inet ' | sed 's/^.*inet //g' \ 
>   | sed 's/ *netmask. *$//g'
192.168.1.100
```
#### 范例二：
```bash
步骤一：先使用grep将关键字MAN所在行取出来 
[dmtsai@study ~]$ cat /etc/man_db.conf | grep 'MAN'
# MANDATORY_MANPATH manpath_element
# MANPATH_MAP path_element manpath_element
# MANDB_MAP global_manpath [relative_catpath]
# every automatically generated MANPATH includes these fields
....(后面省略)....

步骤二：删除掉注解之后的资料！
[dmtsai@study ~]$ cat /etc/man_db.conf | grep 'MAN'| sed 's/#.*$//g'





MANDATORY_MANPATH /usr/man
....(后面省略)....
# 从上面可以看出来，原本注解的资料都变成空白行啦！所以，接下来要删除掉空白行

[dmtsai@study ~]$ cat /etc/man_db.conf | grep 'MAN'| sed 's/#.*$//g' | sed '/^$/d'
MANDATORY_MANPATH /usr/man
MANDATORY_MANPATH /usr/share/man
MANDATORY_MANPATH /usr/local/share/man
....(后面省略)....
```
### 直接修改档案内容(危险动作)
#### 范例六：利用sed将regular_express.txt内每一行结尾若为.则换成! 
```bash
[dmtsai@study ~]$ sed -i 's/\.$/\!/g' regular_express.txt 
```
#### 范例七：利用sed直接在regular_express.txt最后一行加入『# This is a test』 
```bash
[dmtsai@study ~]$ sed -i '$a # This is a test' regular_express.txt 
```

## egrep延伸正规表示法
### 范例一：『零个或一个』的前一个RE字符范例：搜寻(gd) (god)这两个字串。那个o?代表『空的或1个o 』所以，上面的执行成果会将第13, 14行列出来。有没有发现到，这两个案例( 'go+d'与'go?d' )的结果集合与'go*d'相同？想想看，这是为什么喔！^_^egrep -n 'go?d' regular_express.txt|
范例：搜寻(god) (good) (goood)...等等的字串。那个o+代表『一个以上的o 』所以，底下的执行成果会将第1, 9, 13行列出来。
```bash
egrep -n 'go+d' regular_express.txt
```
### 范例二：意义：『零个或一个』的前一个RE字符
搜寻(gd) (god)这两个字串。那个o?代表『空的或1个o 』所以，上面的执行成果会将第13, 14行列出来。有没有发现到，这两个案例( 'go+d'与'go?d' )的结果集合与'go*d'相同？想想看，这是为什么喔！^_^
```bash
egrep -n 'go?d' regular_express.txt
```
### 范例三：用或( or )的方式找出数个字串
搜寻gd或good这两个字串，注意，是『或』！所以，第1,9,14这三行都可以被列印出来喔！那如果还想要找出dog呢？
```bash
egrep -n 'gd|good' regular_express.txt 
egrep -n 'gd|good|dog' regular_express.txt
```
### 范例四：找出『群组』字串
搜寻(glad)或(good)这两个字串，因为g与d是重复的，所以，我就可以将la与oo列于( )当中，并以|来分隔开来，就可以啦！
```bash
egrep -n 'g(la|oo)d' regular_express.txt
```
### 范例五：意义：多个重复群组的判别
将『AxyzxyzxyzxyzC』用echo叫出，然后再使用如下的方法搜寻一下！
```bash
echo 'AxyzxyzxyzxyzC' | egrep 'A(xyz)+C'
```

## 文件的格式化与相关处理
### 格式化列印： printf
```bash
[dmtsai@study ~]$ printf '列印格式'实际内容
选项与参数：
关于格式方面的几个特殊样式：
       \a 警告声音输出
       \b 倒退键(backspace)
       \f 清除萤幕(form feed)
       \n 输出新的一行
       \r 亦即Enter 按键
       \t 水平的[tab] 按键
       \v 垂直的[tab] 按键
       \xNN NN 为两位数的数字，可以转换数字成为字元。
关于C 程式语言内，常见的变数格式
       %ns 那个n 是数字， s 代表string ，亦即多少个字元；
       %ni 那个n 是数字， i 代表integer ，亦即多少整数位数；
       %N.nf 那个n 与N 都是数字， f 代表floating (浮点)，如果有小数位数，
             假设我共要十个位数，但小数点有两位，即为%10.2f 啰
```
#### 范例一：将刚刚上头资料的档案(printf.txt)内容仅列出姓名与成绩：(用[tab]分隔) 
```bash
[dmtsai@study ~]$ cat printf.txt
Name Chinese English Math Average
DmTsai 80 60 92 77.33
VBird 75 55 80 70.00
Ken 60 90 70 73.33

[dmtsai@study ~]$ printf '%s\t %s\t %s\t %s\t %s\t \n' $(cat printf.txt)
Name    Chinese     English     Math    Average
DmTsai  80          60          92      77.33
VBird   75          55          80      70.00
Ken     60          90          70      73.33
```
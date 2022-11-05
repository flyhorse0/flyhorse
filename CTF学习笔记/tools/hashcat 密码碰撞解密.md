
## md5 密码解密 ，md5值为 69f7...fbfc，以下计算1-8位的数字+小写字幕组合
`hashcat -a 3 -m 0 -O 384ddc5b831e55ba9d1f7eb41d5c8464 -1 ?l?d --increment --increment-min 1 --increment-max 8 ?1?1?1?1?1?1?1?1`

`-1 ?l?d  表示自定义字符集，在结尾用 ?1 引用`

# 简介

Hashcat是自称世界上最快的密码恢复工具。它在2015年之前拥有专有代码库，但现在作为免费软件发布。适用于Linux，OS X和Windows的版本可以使用基于CPU或基于[GPU](https://so.csdn.net/so/search?q=GPU&spm=1001.2101.3001.7020)的变体。支持hashcat的散列算法有Microsoft LM哈希，MD4，MD5，SHA系列，Unix加密格式，MySQL和Cisco PIX等。

hashcat支持多种计算核心：GPU、CPU、APU、DSP、FPGA、Coprocessor
GPU的驱动要求：
```
AMD GPUs on Linux require "RadeonOpenCompute (ROCm)" Software Platform (1.6.180 or later)
AMD GPUs on Windows require "AMD Radeon Software Crimson Edition" (15.12 or later)
Intel CPUs require "OpenCL Runtime for Intel Core and Intel Xeon Processors" (16.1.1 or later)
Intel GPUs on Linux require "OpenCL 2.0 GPU Driver Package for Linux" (2.0 or later)
Intel GPUs on Windows require "OpenCL Driver for Intel Iris and Intel HD Graphics"
NVIDIA GPUs require "NVIDIA Driver" (367.x or later)
```
最新版hashcat下载地址：[https://hashcat.net/files/hashcat-5.1.0.7z](http://https//hashcat.net/files/hashcat-5.1.0.7z)  
GitHub地址：[https://github.com/hashcat/hashcat](https://github.com/hashcat/hashcat)
kali已经安装~~
# 参数

下面使常见的参数，想了解更多的参数可以hashcat --help查看
```bash
-a  指定要使用的破解模式，其值参考后面对参数。“-a 0”字典攻击，“-a 1” 组合攻击；“-a 3”掩码攻击。
-m  指定要破解的hash类型，如果不指定类型，则默认是MD5
-o  指定破解成功后的hash及所对应的明文密码的存放位置,可以用它把破解成功的hash写到指定的文件中
--force 忽略破解过程中的警告信息,跑单条hash可能需要加上此选项
--show  显示已经破解的hash及该hash所对应的明文
--increment  启用增量破解模式,你可以利用此模式让hashcat在指定的密码长度范围内执行破解过程
--increment-min  密码最小长度,后面直接等于一个整数即可,配置increment模式一起使用
--increment-max  密码最大长度,同上
--outfile-format 指定破解结果的输出格式id,默认是3
--username   忽略hash文件中的指定的用户名,在破解linux系统用户密码hash可能会用到
--remove     删除已被破解成功的hash
-r       使用自定义破解规则
```

## 攻击模式：
```sh
# | Mode
 ===+======
  0 | Straight（字段破解）
  1 | Combination（组合破解）
  3 | Brute-force（掩码暴力破解）
  6 | Hybrid Wordlist + Mask（字典+掩码破解）
  7 | Hybrid Mask + Wordlist（掩码+字典破解）
```

## 输出格式
```sh
1 = hash[:salt]
2 = plain
3 = hash[:salt]:plain
4 = hex_plain
5 = hash[:salt]:hex_plain
6 = plain:hex_plain
7 = hash[:salt]:plain:hex_plain
8 = crackpos
9 = hash[:salt]:crackpos
10 = plain:crackpos
11 = hash[:salt]:plain:crackpos
12 = hex_plain:crackpos
13 = hash[:salt]:hex_plain:crackpos
14 = plain:hex_plain:crackpos
15 = hash[:salt]:plain:hex_plain:crackpos
```

## [Hash](https://so.csdn.net/so/search?q=Hash&spm=1001.2101.3001.7020) id对照表

因为实在是太多了，所有我就贴一部分常见的hash类型，要想了解所有的参数可到hashcat的[Wiki](https://hashcat.net/wiki/doku.php?id=hashcat)上去看，或者直接hashcat --help查看hash对照表
```sh
- [ Hash modes ] -
 
      # | Name                                             | Category
  ======+==================================================+======================================
    900 | MD4                                              | Raw Hash
      0 | MD5                                              | Raw Hash
   5100 | Half MD5                                         | Raw Hash
    100 | SHA1                                             | Raw Hash
   1300 | SHA2-224                                         | Raw Hash
   1400 | SHA2-256                                         | Raw Hash
  10800 | SHA2-384                                         | Raw Hash
   1700 | SHA2-512                                         | Raw Hash
  17300 | SHA3-224                                         | Raw Hash
  17400 | SHA3-256                                         | Raw Hash
  17500 | SHA3-384                                         | Raw Hash
# 查看help命令即可
```
## 掩码设置

这里列一下常见的掩码字符集
```
l | abcdefghijklmnopqrstuvwxyz          纯小写字母
u | ABCDEFGHIJKLMNOPQRSTUVWXYZ          纯大写字母
d | 0123456789                  纯数字
h | 0123456789abcdef                常见小写子目录和数字
H | 0123456789ABCDEF                常见大写字母和数字
s |  !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~       特殊字符
a | ?l?u?d?s                    键盘上所有可见的字符
b | 0x00 - 0xff                 可能是用来匹配像空格这种密码的
```
下面举几个简单的例子来了解一下掩码的设置
```
八位数字密码：?d?d?d?d?d?d?d?d
八位未知密码：?a?a?a?a?a?a?a?a
前四位为大写字母，后面四位为数字：?u?u?u?u?d?d?d?d
前四位为数字或者是小写字母，后四位为大写字母或者数字：?h?h?h?h?H?H?H?H
前三个字符未知，中间为admin，后三位未知：?a?a?aadmin?a?a?a
6-8位数字密码：--increment --increment-min 6 --increment-max 8 ?l?l?l?l?l?l?l?l
6-8位数字+小写字母密码：--increment --increment-min 6 --increment-max 8 ?h?h?h?h?h?h?h?h
```
如果我们想设置字符集为：abcd123456!@-+，那该怎么做呢。这就需要用到自定义字符集这个参数了，hashcat支持用户最多定义4组字符集
```
--custom-charset1 [chars]等价于 -1
--custom-charset2 [chars]等价于 -2
--custom-charset3 [chars]等价于 -3
--custom-charset4 [chars]等价于 -4
在掩码中用?1、?2、?3、?4来表示。
```
再来举几个例子：
```
--custom-charset1 abcd123456!@-+。然后我们就可以用"?1"去表示这个字符集了
--custom-charset2 ?l?d，这里和?2就等价于?h
-1 ?d?l?u，?1就表示数字+小写字母+大写字母
-3 abcdef -4 123456 那么?3?3?3?3?4?4?4?4就表示为前四位可能是“abcdef”，后四位可能是“123456”
```
# 例子
## 7位数字破解
`hashcat -a 3 -m 0 --force 25c3e88f81b4853f2a8faacad4c871b6 ?d?d?d?d?d?d?d`
## 7位小写字母破解：
`hashcat -a 3 -m 0 --force 7a47c6db227df60a6d67245d7d8063f3 ?l?l?l?l?l?l?l`
## 1-8位数字破解：
`hashcat -a 3 -m 0 --force 4488cec2aea535179e085367d8a17d75 --increment --increment-min 1 --increment-max 8 ?d?d?d?d?d?d?d?d`
## 1-8位小写字母+数字破解
`hashcat -a 3 -m 0 --force ab65d749cba1656ca11dfa1cc2383102 --increment --increment-min 1 --increment-max 8 ?h?h?h?h?h?h?h?h`
## 特定字符集：123456abcdf!@+-
```sh
hashcat64.exe -a 3 -1 123456abcdf!@+- 8b78ba5089b11326290bc15cf0b9a07d ?1?1?1?1?1
注意一下：这里的-1和?1是数字1，不是字母l
```
## 1-8为位符集:123456abcdf!@+-
`hashcat64.exe -a 3 -1 123456abcdf!@+- 9054fa315ce16f7f0955b4af06d1aa1b --increment --increment-min 1 --increment-max 8 ?1?1?1?1?1?1?1?1`
## 1-8位数字+大小写字母+可见特殊符号
```sh
hashcat64.exe -a 3 -1 ?d?u?l?s d37fc9ee39dd45a7717e3e3e9415f65d --increment --increment-min 1 --increment-max 8 ?1?1?1?1?1?1?1?1
或者：
hashcat64.exe -a 3 d37fc9ee39dd45a7717e3e3e9415f65d --increment --increment-min 1 --increment-max 8 ?a?a?a?a?a?a?a?a
```
## 字典破解
`-a 0是指定字典破解模式，-o是输出结果到文件中
hashcat64.exe -a 0 ede900ac1424436b55dc3c9f20cb97a8 password.txt -o result.txt`
## 批量破解
`hashcat -a 0 hash.txt password.txt -o result.txt`
## 字典组合破解：
`hashcat64.exe -a 1 25f9e794323b453885f5181f1b624d0b pwd1.txt pwd2.txt`
## 字典+掩码破解
`hashcat64.exe -a 6 9dc9d5ed5031367d42543763423c24ee password.txt ?l?l?l?l?l`

## Mysql4.1/5的PASSWORD函数
`hashcat64.exe -a 3 -m 300 --force 6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 ?d?d?d?d?d?d`
## sha512crypt $6$, SHA512 (Unix)破解
可以cat /etc/shadow获取
`hashcat64.exe -a 3 -m 1800 --force $6$mxuA5cdy$XZRk0CvnPFqOgVopqiPEFAFK72SogKVwwwp7gWaUOb7b6tVwfCpcSUsCEk64ktLLYmzyew/xd0O0hPG/yrm2X. ?l?l?l?l`

不用整理用户名，使用--username
`hashcat64.exe -a 3 -m 1800 --force qiyou:$6$QDq75ki3$jsKm7qTDHz/xBob0kF1Lp170Cgg0i5Tslf3JW/sm9k9Q916mBTyilU3PoOsbRdxV8TAmzvdgNjrCuhfg3jKMY1 ?l?l?l?l?l --username`

## Windows NT-hash，LM-hash破解
可以用saminside获取NT-hash,LM-hash的值
```
NT-hash:
hashcat64.exe -a 3 -m 1000 209C6174DA490CAEB422F3FA5A7AE634 ?l?l?l?l?l
LM-hash:
hashcat64.exe -a 3 -m 3000 F0D412BD764FFE81AAD3B435B51404EE ?l?l?l?l?l
```

## mssql
`hashcat64.exe -a 3 -m 132 --force 0x01008c8006c224f71f6bf0036f78d863c3c4ff53f8c3c48edafb ?l?l?l?l?l?d?d?d`

## wordpress密码hash破解

具体[加密](https://so.csdn.net/so/search?q=%E5%8A%A0%E5%AF%86&spm=1001.2101.3001.7020)脚本在`./wp-includes/class-phpass.php`的`HashPassword`函数

```swift
hashcat64.exe -a 3 -m 400 --force $P$BYEYcHEj3vDhV1lwGBv6rpxurKOEWY/ ?d?d?d?d?d?d
```

## discuz用户密码hash破解

其密码加密方式[md5](https://so.csdn.net/so/search?q=md5&spm=1001.2101.3001.7020)(md5($pass).$salt)

```css
hashcat64.exe -a 3 -m 2611 --force 14e1b600b1fd579f47433b88e8d85291: ?d?d?d?d?d?d
```

## 破解RAR压缩密码

首先rar2john获取rar文件hash值[下载地址](http://openwall.info/wiki/_media/john/johntheripper-v1.8.0.12-jumbo-1-bleeding-e6214ceab-2018-02-07-win-x64.7z)

```swift
获取rar文件的hash值：rar2john.exe 1.rar结果：1.rar:$rar5$16$639e9ce8344c680da12e8bdd4346a6a3$15$a2b056a21a9836d8d48c2844d171b73d$8$04a52d2224ad082e
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129204720-047d9804-23c4-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129204720-047d9804-23c4-1.png)

```swift
hashcat64.exe -a 3 -m 13000 --force $rar5$16$639e9ce8344c680da12e8bdd4346a6a3$15$a2b056a21a9836d8d48c2844d171b73d$8$04a52d2224ad082e ?d?d?d?d?d?d
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129163411-a6de7830-23a0-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129163411-a6de7830-23a0-1.png)  
注意：

```crystal
hashcat 支持 RAR3-hp 和 RAR5，官方示例如下： -m参数    类型      示例 hash12500    RAR3-hp    $RAR3$*0*45109af8ab5f297a*adbf6c5385d7a40373e8f77d7b89d31713000    RAR5       $rar5$16$74575567518807622265582327032280$15$f8b4064de34ac02ecabfe
```

## zip密码破解

```crystal
用zip2john获取文件的hash值：zip2john.exe 1.zip结果：1.zip:$zip2$*0*3*0*554bb43ff71cb0cac76326f292119dfd*ff23*5*24b28885ee*d4fe362bb1e91319ab53*$/zip2$:::::1.zip-1.txt
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129204743-120aca1e-23c4-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129204743-120aca1e-23c4-1.png)

```ruby
hashcat64.exe -a 3 -m 13600 $zip2$*0*3*0*554bb43ff71cb0cac76326f292119dfd*ff23*5*24b28885ee*d4fe362bb1e91319ab53*$/zip2$ --force ?d?d?d?d?d?d
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129164533-3d55655c-23a2-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129164533-3d55655c-23a2-1.png)

## 破解office密码

```markdown
获取office的hash值：python office2john.py 11.docx结果：11.docx:$office$*2013*100000*256*16*e4a3eb62e8d3576f861f9eded75e0525*9eeb35f0849a7800d48113440b4bbb9c*577f8d8b2e1c5f60fed76e62327b38d28f25230f6c7dfd66588d9ca8097aabb9
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129204911-467e16b6-23c4-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129204911-467e16b6-23c4-1.png)

```markdown
hashcat64.exe -a 3 -m 9600 $office$*2013*100000*256*16*e4a3eb62e8d3576f861f9eded75e0525*9eeb35f0849a7800d48113440b4bbb9c*577f8d8b2e1c5f60fed76e62327b38d28f25230f6c7dfd66588d9ca8097aabb9 --force ?d?d?d?d?d?d
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129165754-f7280e66-23a3-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129165754-f7280e66-23a3-1.png)

## 破解WIFI密码

首先先把我们的握手包转化为hccapx格式，现在最新版的hashcat只支持hccapx格式了，以前的hccap格式已经不支持了

```crystal
官方在线转化https://hashcat.net/cap2hccapx/
```

```css
hashcat64.exe -a 3 -m 2500 1.hccapx 1391040?d?d?d?d
```

[![](https://xzfile.aliyuncs.com/media/upload/picture/20190129172412-a3e3e92e-23a7-1.png)](https://xzfile.aliyuncs.com/media/upload/picture/20190129172412-a3e3e92e-23a7-1.png)

# Others

1.  对于破解过的hash值，用`hashcat64.exe hash --show`查看结果
2.  所有的hash破解结果都在hashcat.potfile文件中
3.  如果破解的时间太长，可以按s键可以查看破解的状态，p键暂停，r键继续破解，q键退出破解。
4.  在使用GPU模式进行破解时，可以使用-O参数自动进行优化
5.  在实际破解中的建议，如果我们盲目的去破解，会占用我们大量的时间和资源
    
    ```undefined
    1.首先走一遍常用的弱口令字典2.组合密码，如：zhang1999，用姓氏和出生年组合，当然也可以用其它的组合，这里举个例子而已3.把常用的掩码组合整理起来放在masks中的.hcmask文件中，然后让它自动加载破解4.如果实在不行，你可以尝试低位数的所有组合去跑，不过不建议太高位数的组合去破解，因为如果对方设置的密码很复杂的话，到头来你密码没有破解到，却浪费了大量的时间和资源，得不偿失
    ```
    
    6.HashCat参数优化  
    考虑到hashcat的破解速度以及资源的分配，我们可以对一些参数进行配置  
    1.Workload tuning 负载调优。  
    该参数支持的值有1,8,40,80,160
    
    ```scss
    --gpu-accel 160 可以让GPU发挥最大性能。
    ```
    
    2.Gpu loops 负载微调  
    该参数支持的值的范围是8-1024（有些算法只支持到1000）。
    
    ```scss
    --gpu-loops 1024 可以让GPU发挥最大性能。
    ```
    
    3.Segment size 字典缓存大小  
    该参数是设置内存缓存的大小，作用是将字典放入内存缓存以加快字典破解速度，默认为32MB，可以根据自身内存情况进行设置，当然是越大越块了。
    
    ```scss
    --segment-size 512 可以提高大字典破解的速度。
    ```
    
    #Reference  
    [Hashcat Wiki](https://hashcat.net/wiki/doku.php?id=hashcat)  
    [https://klionsec.github.io/2017/04/26/use-hashcat-crack-hash/](https://klionsec.github.io/2017/04/26/use-hashcat-crack-hash/)
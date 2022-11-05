# docker中使用
`docker run -it --rm remnux/ciphey ciphey -t "ZmxhZ3tHYXFZN0t0RXRyVklYMVE1b1A1aUVCUkNZWEVBeThyVH0="`

Ciphey-自动解码神器

There are 3 ways to run Ciphey.

1.  File Input `ciphey -f encrypted.txt`
2.  Unqualified input `ciphey -- "Encrypted input"`
3.  Normal way `ciphey -t "Encrypted input"`

Github地址：[https://github.com/Ciphey/Ciphey](https://link.zhihu.com/?target=https%3A//github.com/Ciphey/Ciphey)  
建议给作者点星✔️  

**使用自然语言处理和人工智能以及一些常识的全自动解密/解码/破解工具**

### 特性

-   **支持 30+的加密方法** 例如编码（二进制，base64）和常规加密（例如 Caesar 密码，重复密钥 XOR 等） [有关完整列表，请单击此处](https://link.zhihu.com/?target=https%3A//github.com/Ciphey/Ciphey/wiki/Supported-Ciphers)
-   **具有增强搜索功能的定制人工智能（AuSearch）可以回答“使用了哪种加密技术?"** 解密时间不到 3 秒
-   **定制的自然语言处理系统** Ciphey 可以确定某些东西是否是纯文本。无论该纯文本是 JSON，CTF 标志还是英语 Ciphey，都可以在几毫秒内获得它
-   **多国语言支持** 目前，仅有德语和英语（带有 AU，UK，CAN，USA 变体）
-   **支持加密和哈希** 诸如 CyberChef Magic 之类的替代品则没有。
-   [C++ 核心](https://link.zhihu.com/?target=https%3A//github.com/Ciphey/CipheyCore) 这会使整个过程变得非常快

### Ciphey安装方法

要安装 Ciphey，您需要 2 个核心内容：

1.  Python3.7或以上
2.  Pip（在 Python 3 上）

检查 Python 是否已经安装。运行这两个命令：

python -c "import sys; print(sys.version)"

或

python3 -c "import sys; print(sys.version)"

**Linux命令安装**

在 Linux 上运行以下命令：

python3 -m pip install -U ciphey

**Windows Python 默认安装 32 位。Ciphey 仅支持 64 位。确保您使用的是 64 位 Python**

建议用linux，如果Linux安装过程中，报错 ERROR: Cannot uninstall 'PyYAML'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall. 可以通过执行以下命令来解决 sudo -H pip3 install --ignore-installed PyYAML

### ciphey命令参数

ciphey --help 用法: ciphey **[**选项**]** **[**TEXT_STDIN**]** Ciphey - 自动解密工具 文档地址: https://github.com/Ciphey/Ciphey/wiki Discord **(**这里支持，我们大部分时间都在线**)**: https://discord.ciphey.online/ GitHub: https://github.com/ciphey/ciphey cipher是一种使用智能人工智能的自动解密工具 和自然语言处理。输入加密文本，获取解密文本 例如: 基本用法: ciphey -t "aGVsbG8gbXkgbmFtZSBpcyBiZWU=" 选项: -t, --text TEXT 您想要解密的密文。 -q, --quiet 减少冗长的显示,直接给结果 -g, --greppable 只输出答案**(**对于grep很有用**)** -v, --verbose -C, --checker TEXT 使用给定的检查器 -c, --config TEXT 使用给定的配置文件。默认为 appdirs.user_config_dir**(**'ciphey', 'ciphey'**)**/'config.yml' -w, --wordlist TEXT 使用给定的密码字典 -p, --param TEXT 将参数传递给语言检查器 -l, --list-params BOOLEAN 列出所选模块的参数 --searcher TEXT 选择要使用的搜索算法 -b, --bytes 强制密码使用二进制模式作为输入 --default-dist TEXT 设置默认的字符/字节分布 -m, --module PATH 从给定路径添加模块 -A, --appdirs 输出密码到想要的文件位置 -f, --file FILENAME --help 显示此帮助消息并退出。

以一道题为例：

将flag经过base32->base58->base64加密

  

![](https://cdn.nlark.com/yuque/0/2022/png/28894646/1662384989337-aa654fe9-1a06-45b3-ad02-2b25f96a9ef4.png)

  

`ciphey -t "I44EOOLCIFUDQ5KLME======"`

运行，会自动运算，如果不是想要的结果可以输入 n，继续运算，直至正确结果

### 附录

### 支持解密列表

支持破解的密码列表  
基本加密  

-   Caesar Cipher-凯撒密码
-   ROT47（使用 ROT47 字母高达 ROT94）
-   ASCII 移位（高达 ROT127，带有完整的 ASCII 字母表）
-   Vigenère Cipher-维吉尼亚密码
-   Affine Cipher-仿射密码
-   Binary Substitution Cipher-二进制替换密码 (XY-Cipher)
-   Baconian Cipher -培根密码（两种变体）
-   Soundex
-   Transposition Cipher-转置密码
-   Pig Latin-猪拉丁语

现代密码学  

-   Repeating-key XOR-重复键异或
-   Single XOR-单异或
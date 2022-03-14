## 基础

1. #### 单引号与双引号的区别

   双引号解析 `$` 开头的变量和转义字符，单引号不解析也不转义字符。

   

2. #### PHP底层数组实现方式

   > [PHP 数组底层实现原理](https://www.cnblogs.com/mzhaox/p/11295445.html)

   

3. #### PHP 查看扩展路径及查看进程和占用

   ```bash
   php -i | grep extension_dir #php -i | grep swoole 或者在 PHP 中： phpinfo 来看一下 或者 echo ini_get('extension_dir');
   
   ps -ef | grep "php-fpm" # 查看进程
   
   top | grep "php-fpm" # 查看占用
   ```
   
   

4. #### 进程、线程、协程（用户态线程，减少数据拷贝，降低CPU开销，无callback函数）

   > [【面试高频问题】线程、进程、协程](https://zhuanlan.zhihu.com/p/70256971)
   >
   > [干货 | 进程、线程、协程 10 张图讲明白了！](https://zhuanlan.zhihu.com/p/337978321)

   

5. #### 写时复制

   > [PHP 写时复制（Copy On Write）](https://www.cnblogs.com/lovezbs/p/14071228.html)

   ```php
   $var = "laruence";
   $var_dup = $var;
   $var = 1;
   ```

   ?> `PHP` 在修改一个变量以前，会首先查看这个变量的 `refcount`，如果 `refcount` 大于 `1` ， `PHP` 就会执行一个分离的例程， 对于上面的代码，当执行到第三行的时候， `PHP`  发现 `var` 指向的 `zval` 的 `refcount` 大于 `1` ，那么 `PHP` 就会复制一个新的 `zval` 出来，将原 `zval` 的 `refcount` 减 `1`，并修改  `symbol_table` ，使得 `var` 指向的 `zval` 的 `refcount` 大于 `1` ，那么 `PHP` 就会复制一个新的 `zval` 出来，将原 `zval` 的 `refcount` 减 `1` ，并修改 `symboltable` ，使得 `var` 和 `$var_dup`  分离( `Separation` )。这个机制就是所谓的 `copy on write` (写时复制)。

   

   如果两个变量是相同的值，则共享同一块内存，而那块内存的 `is_ref = 1` `refcount = 1` 后者被引用一次 `+1`，为 `0` 的时候被销毁，相当于资源延迟分配。

   

6. #### 解决内存溢出

   - 要增加PHP可用内存大小，修改 `php.ini` 的 `memory_limi = 128M` 或 `init_set("memory_limit", "128M")`；
   - 对数组进行分批处理，将用过的变量及时销毁 `unset()`；
   - 尽可能减少静态变量的使用，在需要数据重用时，可以考虑使用引用( `&` )；
   - 可以使用 `memory_get_usage ()` 函数，获取当前占用内存 根据当前使用的内存来调整程序；
   - 数据库操作完成后，要马上关闭连接；
   - 一个对象使用完，要及时调用析构函数( `__destruct()` )
   
   ###### 引申
   
   - `unset ()` 函数只能在变量值占用内存空间超过 `256` 字节时才会释放内存空间 ， `PHP` 内核的 `gc` 垃圾回收机制决定；
   
   - 有当指向该变量的所有变量（如引用变量）都被销毁后，才会释放内存 ；
   
   - `unset` 被引用的变量只会解除引用关系，不会销毁该变量。
   
   
   
7. #### 如何设计一个高可用的架构

   > [PHP 如何打造一个高可用高性能的网站呢？](https://www.cnblogs.com/lxwphp/p/9967782.html)

   

8. #### 秒杀如何设计

   > [如何设计秒杀系统？ - 敖丙的回答 - 知乎 :100:](https://www.zhihu.com/question/54895548/answer/923987542)
   >
   > [淘宝秒杀系统怎么设计？](https://www.bilibili.com/video/BV1DV411B7Jq?share_source=copy_web)
   >
   > [如何设计秒杀系统？ - 九章算法的回答 - 知乎 ](https://www.zhihu.com/question/54895548/answer/1352510403)

   

9. #### 如何进行性能优化

   > [PHP 性能优化](https://zhuanlan.zhihu.com/p/136007449)

   ```php
   // 静态变量
   function myFunc() {
   	static $a = 1;// 静态
   
   	echo $a++ . PHP_EOL;
   }
   
   myFunc(); // 1
   myFunc(); // 2
   myFunc(); // 3
   ```



## 框架

#### `thinkphp`  生命周期

1. 请求先通过 `apache / nginx`  访问到 `public/index.php` 文件
2. 载入`Composer`的自动加载`autoload`文件
3. 实例化系统应用基础类`think\App`

#### `laravel` 生命周期

1. 请求先通过 `apache / nginx`  访问到 `public/index.php` 文件
2. 载入`Composer`的自动加载`autoload`文件
3. 载入`Composer`的自动加载`autoload`文件
4. 对于 `HTTP` 应用来说，请求被发送到 `HTTP` 内核， 定义了一个 `HTTP` 中间件列表，确定应用程序是否处于维护模式，校验 `CSRF` 令牌 等等
5. 应用程序加载 `service providers` 服务提供者，服务提供者负责引导框架的所有不同组件，如数据库、队列、验证和路由组件。基本上， `Laravel` 提供的每个主要功能都是由服务提供商引导和配置的。
6. “请求” 将被传递给路由器进行调度。路由器将请求发送到路由或控制器，并运行任何路由特定的中间件。
7. 如果请求通过了所有匹配路由分配的中间件，则执行路由或控制器方法，并通过路由的中间件链路返回路由或控制器方法的响应。
8. 一旦路由或控制器方法返回一个响应，该响应将通过路由的中间件返回
9. 一旦响应通过中间件传回，`HTTP` 内核的 `handle` 方法将返回响应对象，并且 `index.php` 文件对返回的响应调用 `send` 方法。 `send` 方法将响应内容发送到用户的 `web` 浏览器。至此，我们已经完成了整个 `Laravel` 请求生命周期的所有步骤！

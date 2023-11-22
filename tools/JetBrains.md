# JetBrains IDEA 使用

> [**激活码**](http://idea.hicxy.com/?t=dg)

## PHPStorm

1. ### `Terminal` 中使用 `git-bash`

   打开 `phpstorm` 设置，修改 Tooles -> Terminal 中的 Shell path 为：

   ```shell
   D:\SoftWare\Git\bin\sh.exe #修改为自己安装 git-bash 的位置
   ```

   可能出现乱码，则解决：在 `~/.bashrc` 文件中加入一下代码，重启 `IDEA` 

   ```shell
   # 解决IDEA下的terminal中文Unicode编码问题
   export LANG="zh_CN.UTF-8"
   export LC_ALL="zh_CN.UTF-8"
   ```

2. ### 字符串语言注入

   在字符串上按 `Alt + Enter` 选择语言注入，选中要注入的语言，设置后字符串会显示对应语言的格式，再次按 `Alt + Enter` 可以在单独窗口编辑字符串。

3. ### 将代码装进 `if` `try/catch` 等语句中

   windows: `Ctrl + Alt + T`

4. ### 书签

   普通书签： `F11`

   助记书签： `Ctrl + F11` ，如果是数字，可以 `Ctrl + 数字` 快速跳到该书签

   书签列表： `Shift + F11`



### 插件推荐

1. #### any-rule：常用正则表达式插件

   `IDEA` 版使用： `ALT + A`

2. #### AceJump

   使用：`Ctrl + ;`



## DataGrip

1. ### Show Aggregate View 

   #### 时间戳转时间格式

   ```groovy
   ROWS.each { row ->
     COLUMNS.each { column ->
       def value = row.value(column)
       if (value instanceof Number) {
         long timeValue = value.toString().toBigDecimal() * 1000
         // OUT.append(timeValue.toString() + "\n")
         Date date = new Date(timeValue)
         // OUT.append(date.toString() + "\n")
         // OUT.append(value.toString().toBigDecimal() + "\n")
         // OUT.append((value.toString().toBigDecimal() * 1000).toString() + "\n")
   
         Integer year = date.year + 1900
         Integer month = date.month + 1
         Integer day = date.date
         Integer hour = date.hours
         Integer minute = date.minutes
         Integer seconds = date.seconds
   
         String yearStr = year.toString()
         String monthStr = month.toString()
         String dayStr = day.toString()
         String hourStr = hour.toString()
         String minuteStr = minute.toString()
         String secondsStr = seconds.toString()
   
         if (month < 10) {
           monthStr = "0" + monthStr
         }
         if (day < 10) {
           dayStr = "0" + dayStr
         }
         if (hour < 10) {
           hourStr = "0" + hourStr
         }
         if (minute < 10) {
           minuteStr = "0" + minuteStr
         }
         if (seconds < 10) {
           secondsStr = "0" + secondsStr
         }
   
         OUT.append(yearStr + "-" + monthStr + "-" + dayStr + " "+ hourStr + ":" + minuteStr + ":" + secondsStr + "\n")
       }
       else {
         OUT.append("Value is not a number" + "\n")
       }
     }
   }
   ```

2. ### 快速查看表格

   - 按 `Ctrl + N` →键入表的名称，然后按 `Enter` 键；
   - 如果光标上脚本内表的名称，按 `F4` 两次；
   - 如果您在控制台中，键入 `SELECT * FROM` 的快速方法是键入 `sel` 并按 `Tab` 键，然后放置表的名称并运行查询。



---



## GoLand

1. ### 设置中 GOROOT 显示 no version

   解决方法：

   修改 `src/runtime/internal/sys/zversion.go`：

   添加一行代码：

   ```go
   const TheVersion = `go1.18.3`
   ```

   

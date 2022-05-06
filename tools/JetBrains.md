# JetBrains IDEA 使用

1. ##  `Terminal` 中使用 `git-bash`

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

2. ## 字符串语言注入

   在字符串上按 `Alt + Enter` 选择语言注入，选中要注入的语言，设置后字符串会显示对应语言的格式，再次按 `Alt + Enter` 可以在单独窗口编辑字符串。

3. ## 将代码装进 `if` `try/catch` 等语句中

   windows: `Ctrl + Alt + T`

4. ## 书签

   普通书签： `F11`

   助记书签： `Ctrl + F11` ，如果是数字，可以 `Ctrl + 数字` 快速跳到该书签

   书签列表： `Shift + F11`

5. ##  `AceJump` 插件

   `Ctrl + ;`

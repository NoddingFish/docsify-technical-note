## 常见问题

1. ###  `docker-desktop` 占用 `C` 盘 过大

   1. 查看

      ```shell
      wsl -l -v
      # 返回
      #   NAME                   STATE           VERSION
      # * Ubuntu                 Stopped         2
      #   Ubuntu-18.04           Stopped         2
      #   docker-desktop-data    Stopped         2
      ```

   2. 首先关闭 `docker`

   3. 关闭所有发行版

      ```shell
      wsl --shutdown
      ```


   

2. ### 解决 `WSL2` 中 `Vmmem` 内存占用过大问题

   1. 按下 `Windows + R` 键，输入 `%UserProfile%` 并运行进入用户文件夹

   2. 新建文件 `.wslconfig` ，然后记事本编辑

   3. 填入以下内容并保存, `memory` 为系统内存上限，这里我限制最大 `2gb` ，可根据自身电脑配置设置

      ```ini
      [wsl2]
      memory=2GB
      swap=0
      localhostForwarding=true
      ```

   4. 然后启动 `cmd` 命令提示符，输入 `wsl --shutdown` 来关闭当前的子系统
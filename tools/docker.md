## 常见问题

1. ###  `docker-desktop` 占用 `C盘` 过大

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

      
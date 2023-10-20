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

   

3. ### 容器获取宿主机的 `IP`

   ```shell
   ip addr show
   # 或
   ip addr show docker0
   
   # 例如接口：
   3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
       link/ether 02:42:2c:a0:e0:b0 brd ff:ff:ff:ff:ff:ff
       inet 172.17.0.1/16 scope global docker0
          valid_lft forever preferred_lft forever
       inet6 fe80::42:2cff:fea0:e0b0/64 scope link 
          valid_lft forever preferred_lft forever
          
   # 宿主机 ip 是：172.17.0.1
   ```




## 常用命令

```shell
# Docker 查看容器映射路径（container_name 为容器名称）
docker inspect container_name | grep Mounts -A 20
```


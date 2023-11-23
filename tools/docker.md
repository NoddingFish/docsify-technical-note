## 常见问题

1. ###  `docker-desktop` 占用 `C` 盘 过大

   > [win10使用WSL 2运行Docker Desktop，运行文件从C盘迁移到其他目录](https://www.cnblogs.com/xhznl/p/13184398.html)

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

   3. 关闭所有发行版：
      ```shell
      wsl --shutdown
      ```
   
      
   
   4. 将 `docker-desktop-data` 导出到 `D:\SoftwareData\wsl\docker-desktop-data\docker-desktop-data.tar` （注意，原有的 `docker images` 不会一起导出）
      ```shell
      wsl --export docker-desktop-data D:\SoftwareData\wsl\docker-desktop-data\docker-desktop-data.tar
      ```
   
      
   
   5. 注销 `docker-desktop-data` ：
      ```shell
      wsl --unregister docker-desktop-data
      ```
   
      
   
   6. 重新导入 `docker-desktop-data` 到要存放的文件夹： `D:\SoftwareData\wsl\docker-desktop-data\` ：
      ```shell
      wsl --import docker-desktop-data D:\SoftwareData\wsl\docker-desktop-data\ D:\SoftwareData\wsl\docker-desktop-data\docker-desktop-data.tar --version 2
      ```
   
      
   
      只需要迁移 `docker-desktop-data` 一个发行版就行，另外一个不用管，它占用空间很小。
   
      完成以上操作后，原来的 `%LOCALAPPDATA%/Docker/wsl/data/ext4.vhdx` 就迁移到新目录了。
   
      重启 `docker` ，这下不用担心 `C盘` 爆满了！
   
      


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



> [CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html#:~:text=CentOS%20Docker%20%E5%AE%89%E8%A3%85%20Docker%20%E6%94%AF%E6%8C%81%E4%BB%A5%E4%B8%8B%E7%9A%84%2064%20%E4%BD%8D%20CentOS,%E5%8D%B8%E8%BD%BD%E6%97%A7%E7%89%88%E6%9C%AC%20%E8%BE%83%E6%97%A7%E7%9A%84%20Docker%20%E7%89%88%E6%9C%AC%E7%A7%B0%E4%B8%BA%20docker%20%E6%88%96%20docker-engine%20%E3%80%82)


## 常用命令

```shell
# Docker 查看容器映射路径（container_name 为容器名称）
docker inspect container_name | grep Mounts -A 20

# Linux 中启动 Docker
systemctl start docker
```


### 常见问题

1. #### 代理问题

   提示：

   ```shell
   fatal: unable to access 'https://github.com/***/': Failed to connect to 127.0.0.1 port 31181: Connection refused
   ```

   查询是否使用代理：

   ```shell
   git config --global http.proxy
   ```

    取消代理：

   ```shell
   git config --global --unset http.proxy
   ```

   
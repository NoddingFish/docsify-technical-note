## 知识

> [一个学习 Git 的网站 Learn Git Branching](https://learngitbranching.js.org/)



## 常见问题

1. ### 代理问题

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

2. ###  `OpenSSL`

   提示：

   ```shell
   fatal: unable to access 'https://github.com/NoddingFish/docsify-technical-note.git/': Failed to connect to github.com port 443: Timed out
   ```

   解决：

   ```shell
   git config http.sslVerify "false"
   git config --global http.sslVerify "false"
   ```

   
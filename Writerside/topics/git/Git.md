# Git

## Foreword

1. 统一概念：
   - 工作区：改动（增删文件和内容）
   - 暂存区：输入命令：`git add 改动的文件名`，此次改动就放到了 ‘暂存区’
   - 本地仓库(简称：本地)：输入命令：`git commit 此次修改的描述`，此次改动就放到了 ’本地仓库’，每个 commit，我叫它为一个 ‘版本’。
   - 远程仓库(简称：远程)：输入命令：`git push 远程仓库`，此次改动就放到了 ‘远程仓库’（GitHub 等)
   - commit-id：输出命令：`git log`，最上面那行 `commit xxxxxx`，后面的字符串就是 commit-id

## Git指令

### Git配置

```bash
git config --global -l  
```

### Github访问问题

#### 全局代理（不推荐）

##### 设置

```bash
git config --global http.proxy socks5://127.0.0.1:1080 #http://127.0.0.1:1080 
git config --global https.proxy socks5://127.0.0.1:1080 #https://127.0.0.1:1080
```

##### 取消

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

#### 只对部分域名代理（推荐）

##### 只对github进行代理

```bash
git config --global http.https://github.com.proxy socks5://127.0.0.1:10808
git config --global https.https://github.com.proxy socks5://127.0.0.1:10808
```

#### 设置Host

> 1. [Github IP](https://github.com.ipaddress.com)
>    1. 140.82.114.4 github.com
> 2. [域名 IP](https://fastly.net.ipaddress.com/github.global.ssl.fastly.net)
>    1. 199.232.69.194 github.global.ssl.fastly.net
> 3. [静态资源 IP](https://github.com.ipaddress.com/assets-cdn.github.com)
>    1. 185.199.108.153 assets-cdn.github.com
>    2. 185.199.110.153 assets-cdn.github.com
>    3. 185.199.111.153 assets-cdn.github.com
> 4. C:\Windows\System32\drivers\etc\hosts
> 5. ipconfig /flushdns




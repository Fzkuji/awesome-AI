


[如何使用WSL在Windows上安装Linux](https://learn.microsoft.com/zh-cn/windows/wsl/install)

WSL基于Clash的全局代理：

```
#!/bin/bash
host_ip=$(cat /etc/resolv.conf |grep "nameserver" |cut -f 2 -d " ")
export ALL_PROXY="http://$host_ip:7890"
```


Git 无法访问 GitHub - 解决方法

```
git config --global http.proxy http://$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):7890
git config --global https.proxy http://$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):7890
```


## WSL 代理配置 - 简单流程

### 步骤 1：获取 Windows IP

```bash
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
```

记住这个 IP（比如 `172.20.144.1`）

### 步骤 2：设置代理环境变量

```bash
export http_proxy="http://172.20.144.1:7890"
export https_proxy="http://172.20.144.1:7890"
export all_proxy="http://172.20.144.1:7890"
```

（把 `172.20.144.1` 换成你的 IP，`7890` 换成你的代理端口）

### 步骤 3：测试代理是否生效

```bash
curl google.com
```

### 步骤 4：为 apt 配置代理

```bash
echo 'Acquire::http::Proxy "http://172.20.144.1:7890";' | sudo tee /etc/apt/apt.conf.d/proxy.conf
echo 'Acquire::https::Proxy "http://172.20.144.1:7890";' | sudo tee -a /etc/apt/apt.conf.d/proxy.conf
```

### 步骤 5：使用 apt

```bash
sudo apt-get update
sudo apt-get install 你要装的软件
```

### 步骤 6：需要取消代理时

```bash
unset http_proxy
unset https_proxy
unset all_proxy
sudo rm /etc/apt/apt.conf.d/proxy.conf
```

---

## 更简单的一行命令版本

如果你不想记 IP，直接复制粘贴：

### 设置代理（一行搞定）

```bash
export all_proxy="http://$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):7890" && export http_proxy=$all_proxy && export https_proxy=$all_proxy
```

### 配置 apt 代理（一行搞定）

```bash
echo "Acquire::http::Proxy \"http://$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):7890\";" | sudo tee /etc/apt/apt.conf.d/proxy.conf
```

**注意**：把 `7890` 改成你的代理端口号！
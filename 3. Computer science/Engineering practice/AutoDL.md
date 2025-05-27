# AutoDL子账号使用指南

## 登录

AutoDL网址：
https://www.autodl.com/

登录网址：
https://www.autodl.com/login

![](../../Attachments/3.%20Computer%20science/Engineering%20practice/AutoDL/IMG-20250307222610313.jpg)

子账号登录网址：
https://www.autodl.com/subAccountLogin

子账号登录有专门的页面，使用我给你们的账号登录，一人一个。

## 租用GPU服务器

1. 在必须要GPU环境的时候再考虑租用服务器。
2. AutoDL服务器主要用于代码调试。有大规模训练需求可以向我申请其他算力。
3. 首先考虑显存需求（贵），然后再考虑计算速度（可以通过加卡解决）。目前每个人500的额度。
4. 使用结束记得关机（不用释放），否则会持续扣费。

![](../../Attachments/3.%20Computer%20science/Engineering%20practice/AutoDL/IMG-20250307222610343.png)


## 使用方法

1. 具体使用方法看[帮助文档](https://www.autodl.com/docs/)。
2. GPU服务器在国内，若需要下载GitHub文件、Huggingface模型，参考学术资源加速使用代理。
3. 下载资源时可以使用无卡模式开机（每小时0.1元）。

![](../../Attachments/3.%20Computer%20science/Engineering%20practice/AutoDL/IMG-20250307224304152.png)

### [学术资源加速](https://www.autodl.com/docs/network_turbo/)

#### 公开服务

点击访问网址，站点内有具体教程。
- Github： [https://ghproxy.link/](https://ghproxy.link/) （点击后查看当前可用的域名地址，然后点击地址跳转查看文档）
- HuggingFace镜像站：[https://hf-mirror.com/](https://hf-mirror.com/)

#### AutoDL内置服务

> 声明：限于学术使用github和huggingface网络速度慢的问题，以下为方便用户学术用途使用相关资源提供的加速代理，不承诺稳定性保证。此外如遭遇恶意攻击等，将随时停止该加速服务

以下为可以加速访问的学术资源地址：
- github.com
- githubusercontent.com
- githubassets.com
- huggingface.co

如果在终端中使用：

```shell
source /etc/network_turbo
```

如果是在Notebook中使用：

```python
import subprocess  
import os  
  
result = subprocess.run('bash -c "source /etc/network_turbo && env | grep proxy"', shell=True, capture_output=True, text=True)  
output = result.stdout  
for line in output.splitlines():  
    if '=' in line:  
        var, value = line.split('=', 1)  
        os.environ[var] = value
```

**取消学术加速**，如果不再需要建议关闭学术加速，因为该加速可能对正常网络造成一定影响。

```shell
unset http_proxy && unset https_proxy
```


#### [clash-for-AutoDL](https://github.com/VocabVictor/clash-for-AutoDL)

AutoDL自带的网络加速服务在访问某些受限服务（如Claude、OpenAI API等）时可能并不生效，为此我们推荐使用社区项目 [clash-for-AutoDL](https://github.com/VocabVictor/clash-for-AutoDL) 配置自己的科学上网节点。

##### 安装Clash代理（一次性配置）

1. 进入你创建的 AutoDL 云服务器。
2. 在终端执行如下命令下载并安装：

```bash
bash <(curl -fsSL https://raw.githubusercontent.com/VocabVictor/clash-for-AutoDL/main/install.sh)
```

3. 安装完成后，文件将位于 `~/ShellCrash` 目录。

##### 配置你的节点

1. 将你自己订阅的 Clash 节点配置（YAML 链接）保存为 `config.yaml`，放到 `~/ShellCrash/configs/` 目录中（若目录不存在请手动创建）。
    

```bash
mkdir -p ~/ShellCrash/configs
mv config.yaml ~/ShellCrash/configs/
```

2. 编辑 `start.sh` 脚本或直接运行脚本加载你的配置。

##### 启动代理

1. 启动 Clash 代理服务：

```bash
cd ~/ShellCrash
bash start.sh
```

2. 首次启动可能会无效，这时请先关闭再重新开启代理：

```bash
bash proxy_off.sh
bash proxy_on.sh
```

（或者手动执行 `proxy_off && proxy_on`）

##### 测试代理是否生效

```bash
curl ip.sb
```

输出的 IP 应为你代理节点的 IP，而非 AutoDL 默认出口。

你现在可以正常使用受地域限制的服务，比如：

- Claude
- OpenAI API
- Huggingface Space
- Google Scholar

##### Web 管理界面（可选）

如果你需要更方便的节点管理，可以访问内置 Web 管理页面：

```text
http://localhost:9999/ui
```

本地访问方式（需开启端口转发）：

```bash
ssh -L 9999:localhost:9999 -p [端口号] root@[服务器IP]
```

然后本地浏览器访问：

```text
http://localhost:9999/ui
```

##### 关闭代理

```bash
bash proxy_off.sh
```

建议在不使用代理或访问国内资源时关闭，以避免影响速度或出现连接异常。



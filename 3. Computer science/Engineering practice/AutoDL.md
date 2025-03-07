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
2. GPU服务器在国内，若需要下载GitHub文件、Huggingface模型，参考[学术资源加速](https://www.autodl.com/docs/network_turbo/)使用代理。

### 学术资源加速[¶](https://www.autodl.com/docs/network_turbo/#_1 "Permanent link")

#### 公开服务[¶](https://www.autodl.com/docs/network_turbo/#_2 "Permanent link")

点击访问网址，站点内有具体教程。

Github： [https://ghproxy.link/](https://ghproxy.link/) （点击后查看当前可用的域名地址，然后点击地址跳转查看文档）

HuggingFace镜像站：[https://hf-mirror.com/](https://hf-mirror.com/)

#### AutoDL内置服务[¶](https://www.autodl.com/docs/network_turbo/#autodl "Permanent link")

> 声明：限于学术使用github和huggingface网络速度慢的问题，以下为方便用户学术用途使用相关资源提供的加速代理，不承诺稳定性保证。此外如遭遇恶意攻击等，将随时停止该加速服务

以下为可以加速访问的学术资源地址：

- github.com
- githubusercontent.com
- githubassets.com
- huggingface.co

如果在终端中使用：

`source /etc/network_turbo`

如果是在Notebook中使用：

```

```

**取消学术加速**，如果不再需要建议关闭学术加速，因为该加速可能对正常网络造成一定影响。

`unset http_proxy && unset https_proxy`
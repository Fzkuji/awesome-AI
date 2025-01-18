国内镜像下载: https://blog.csdn.net/wandererXX/article/details/135371458

永久配置: https://blog.csdn.net/qq_38238956/article/details/134673618

https://huggingface.co/docs/huggingface_hub/en/guides/cli#getting-started

Qwen/Qwen2-7B-Instruct

```shell
pip install -U "huggingface_hub[cli]"
```

```shell
export HF_ENDPOINT=https://hf-mirror.com
```

```shell
huggingface-cli download openwebtext --repo-type dataset
```

```shell
huggingface-cli download Qwen/Qwen2-7B-Instruct --cache_dir=
```

#### [Huggingface 默认下载位置更改](https://www.cnblogs.com/kongaobo/p/17528720.html "发布于 2023-07-05 15:40")

Ubuntu 系统中 Huggingface 模型等默认的下载位置如下：

```none
~\.cache\huggingface
```

通过修改环境变量更改默认下载位置：

```shell
# 打开配置文件
vi ~/.bashrc
# 添加下述变量
export HF_HOME="目标地址/.cache/huggingface"
# 使配置文件生效
source ~/.bashrc
```

```shell
export HF_HOME=" /Volumes/Aurora/huggingface"
```

### 通过clone下载模型


```
git lfs install

git clone https://fzkuji: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@huggingface.co/meta-llama/Llama-2-7b-chat-hf

git lfs pull
```


```
git lfs install

git clone https://fzkuji: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@huggingface.co/Qwen/Qwen2-7B-Instruct

git lfs pull
```




最新推荐还是用modelscope下载，再迁移到huggingface的路径下

首先下载模型：

```
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Qwen-7B
```

然后尝试从hf-mirror下载模型（网不好多半是缺损文件）：

```
huggingface-cli download --resume-download deepseek-ai/DeepSeek-R1-Distill-Qwen-7B
```

于是有了两个路径，将modelscope下载的mv到：

```
mv /mnt/data/zichuanfu/.cache/modelscope/hub/models/deepseek-ai/DeepSeek-R1-Distill-Qwen-7B ~/.cache/huggingface/hub/models--deepseek-ai--DeepSeek-R1-Distill-Qwen-7B/snapshots/916b56a44061fd5cd7d6a8fb632557ed4f724f60
```

#### hf-mirror

最近hf-mirror越来越慢了

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


1. 打开配置文件：

```shell
vi ~/.bashrc
```

2. 添加下述变量：

```shell
export HF_HOME="目标地址/.cache/huggingface"
```

- 比如，在autodl里，就是：

```shell
export HF_HOME="/root/autodl-tmp/.cache/huggingface"
```

- 在本地U盘，就是：

```shell
export HF_HOME=" /Volumes/Aurora/huggingface"
```

3. 使配置文件生效

```shell
source ~/.bashrc
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


第一次是multi lingu multi task、性能提升

finetune 是upper bond，数据和训练需要时间，而且无法获取。

metric rouge-4 和其他指标一致

ranking


### 删除缓存数据集或模型

https://medium.com/@airabbitX/how-to-safely-delete-a-hugging-face-model-from-the-cache-7d9dcd9a7036
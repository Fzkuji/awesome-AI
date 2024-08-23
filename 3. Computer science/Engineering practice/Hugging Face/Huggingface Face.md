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


远程衰减：

- https://zhuanlan.zhihu.com/p/647109286

- https://zhuanlan.zhihu.com/p/662790439



CUDA_VISIBLE_DEVICES=4,5,6,7 lm_eval --model hf \
    --model_args pretrained=deepseek-ai/DeepSeek-R1-Distill-Qwen-7B \
    --tasks gsm8k \
    --device cuda:0 \
    --batch_size 8
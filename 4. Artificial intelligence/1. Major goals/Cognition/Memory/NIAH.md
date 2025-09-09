I'll fetch the README from that GitHub repository to check if it can test Hugging Face models.Let me search for information about this tool to understand its capabilities.根据搜索结果，原版的 **LLMTest_NeedleInAHaystack** 主要支持 OpenAI、Anthropic 和 Cohere 的模型，**不直接支持 Hugging Face 模型**。

不过，有几个替代方案可以测试 Hugging Face 模型：

## 1. **THUDM/LongAlign 的修改版本**

THUDM团队重构了原始的 "Needle In A Haystack - Pressure Test" 代码，添加了对 Hugging Face 模型的支持。使用步骤包括：

- 在 `config-prompt.yaml` 中配置测试参数
- 设置模型路径（或 Hugging Face 路径）在 `config-pred.yaml`
- 运行测试并使用 GPT-4 进行评分

项目地址：https://github.com/THUDM/LongAlign

## 2. **OpenCompass 框架**

OpenCompass 框架的 NeedleBench 数据集可以自动从 Hugging Face 接口加载，无需手动下载或配置。它支持评估多种模型，包括 Qwen、InternLM 等 Hugging Face 上的模型。

安装和使用：

```bash
conda create --name opencompass python=3.10 pytorch torchvision pytorch-cuda -c nvidia -c pytorch -y
conda activate opencompass
git clone https://github.com/open-compass/opencompass opencompass
cd opencompass
pip install -e .
```

## 3. **Arize-ai 的分支版本**

Arize-ai 也维护了一个分支版本，虽然主要还是支持原始的几个 API 提供商，但他们的目标是测试更多模型。

## 总结

如果你想用 Needle in a Haystack 测试来评估 Hugging Face 模型，建议使用：

- **THUDM/LongAlign** - 如果你需要最直接的 Hugging Face 支持
- **OpenCompass** - 如果你需要更全面的评估框架和多种长文本测试场景

原版工具主要是为商业 API 设计的，所以对开源模型的支持需要使用这些修改版本。
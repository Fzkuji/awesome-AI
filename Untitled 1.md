
# Contents

# [Uni-CTR Description](#contents)

The proposed framework for Uni-CTR comprises three parts,. Initially, the input text undergoes processing via the selected **LLM Backbone** to extract the commonalities and distinctions of the data across domains. Subsequently, Subsequently, LLM provides the representations obtained from different layers to the **domain-specific networks** to learn domain-specific characteristics. Additionally, a **general network** is incorporated to learn the representations of all known domains, which enables zero-shot prediction of newly unseen domains.

A Unified Framework for Multi-Domain CTR Prediction via Large Language Models

CIKM2022

# [Dataset](#contents)

- [Movie-Lens-1M](https://grouplens.org/datasets/movielens/1m/)

# [Environment Requirements](#contents)

- Hardware（CPU）
    - Prepare hardware environment with CPU  processor.
- Framework
    - [MindSpore-1.8.1](https://www.mindspore.cn/install/en)
- Requirements
  - pandas
  - numpy
  - random
  - mindspre==1.8.1
  - tqdm
  - sklearn
- For more information, please check the resources below：
  - [MindSpore Tutorials](https://www.mindspore.cn/tutorials/en/master/index.html)
  - [MindSpore Python API](https://www.mindspore.cn/docs/en/master/api_python/mindspore.html)

# [Quick Start](#contents)

After installing MindSpore via the official website, you can start training and evaluation as follows:

- running on CPU

  ```python
  # run training and evaluation example
  python main.py
  ```

# [Script Description](#contents)

## [Script and Sample Code](#contents)

```bash
.
└─IntTower
  ├─README.md             # descriptions of warpctc
  ├─eval.py               # model evaluation processing
  ├─export.py             # export model to MindIR format
  ├─get_dataset.py        # data process  
  ├─model.py              # IntTower structure
  ├─model_config.py       # model training parameters
  ├─module.py             # modules in IntTower
  ├─train.py              # train IntTower
  ├─util.py               # some process function
  └─requirements.txt      # model requirements
```

## [Script Parameters](#contents)

Parameters for both training and evaluation can be set in `model_config.py`

- Parameters for Movielens-1M Dataset

```python
mlp_layers = [300, 300, 128]   # mlp units in every layer
feblock_size = 256 # number of units in Fe-block
head_num = 4 # number of pieces in Fe-block
user_embedding_dim = 129 # size of user embedding
item_embedding_dim = 33 # size of item embedding
sparse_embedding_dim = 32 # size of single sparse feature embedding dim
use_multi_layer = True # use every user layer
user_sparse_field = 4 # number of user sparse feature
keep_rate = 0.9 # dropout keep_rate
epoch = 10 # training epoch
batch_size = 2048 # training batch size
seed = 3047 # random seed
lr = 0.0005 # learn rate
 ```

## [Training Process](#contents)

### Training

- running on Ascend

  ```python
  python train.py > ms_log/output.log 2>&1 &
  ```

- The python command above will run in the background, you can view the results through the file `ms_log/output.log`.

  ```txt
   13%|█▎        | 31/230 [00:23<02:26,  1.36it/s, train_auc=0.813, train_loss=0.60894054]
   ...
  ```

- The model checkpoint will be saved in the current directory.

## [Evaluation Process](#contents)

### Evaluation

- evaluation on dataset

  Before running the command below, please check the checkpoint path used for evaluation.

  ```python
  python eval.py > ms_log/eval_output.log 2>&1 &
  ```

  The above python command will run in the background. You can view the results through the file "eval_output.log". The accuracy is saved in auc.log file.

  ```txt
   [00:31,  2.29it/s, test_auc=0.896, test_loss=0.3207327]
  ```

## Inference Process

**Before inference, please refer to [MindSpore Inference with C++ Deployment Guide](https://gitee.com/mindspore/models/blob/master/utils/cpp_infer/README.md) to set environment variables.**

### [Export MindIR](#contents)

- Export on local

  ```shell
  python export.py
  ```

# [Model Description](#contents)

## [Performance](#contents)

### Training Performance

| Parameters          | CPU                               |
|---------------------|-----------------------------------|
| Model Version       | IntTower                          |
| Resource            | CPU 2.90GHz;16Core;32G Memory     |
| uploaded Date       | 09/24/2022 (month/day/year)       |
| MindSpore Version   | 1.8.1                             |
| Dataset             | [1]                               |
| Training Parameters | epoch=8, batch_size=2048, lr=1e-3 |
| Optimizer           | Adam                              |
| Loss Function       | Sigmoid Cross Entropy With Logits |
| outputs             | AUC                               |
| Loss                | 0.892                             |
| Per Step Time       | 34.50 ms                          |

### Inference Performance

| Parameters        | CPU                           |
|-------------------|-------------------------------|
| Model Version     | IntTower                      |
| Resource          | CPU 2.90GHz;16Core;32G Memory |                        |
| Uploaded Date     | 09/24/2022 (month/day/year)   |
| MindSpore Version | 1.8.1                         |
| Dataset           | [1]                           |
| batch_size        | 2048                          |
| outputs           | AUC                           |
| AUC               | 0.896                         |

# [Description of Random Situation](#contents)

We set the random seed before training in model_config.py.

# [ModelZoo Homepage](#contents)

 Please check the official [homepage](https://gitee.com/mindspore/models)
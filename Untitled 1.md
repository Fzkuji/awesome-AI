
# Contents

# [Uni-CTR Description](#contents)

The proposed framework for Uni-CTR comprises three parts,. Initially, the input text undergoes processing via the selected **LLM Backbone** to extract the commonalities and distinctions of the data across domains. Subsequently, Subsequently, LLM provides the representations obtained from different layers to the **domain-specific networks** to learn domain-specific characteristics. Additionally, a **general network** is incorporated to learn the representations of all known domains, which enables zero-shot prediction of newly unseen domains.

A Unified Framework for Multi-Domain CTR Prediction via Large Language Models

# [Dataset](#contents)

- [Amazon Review Data (2018)](https://cseweb.ucsd.edu/~jmcauley/datasets/amazon_v2/)

# [Environment Requirements](#contents)

- Hardware（GPU）
	- Prepare hardware environment with GPU  processor.
- Framework
	- Pytorch
- Requirements
	- accelerate  
	- huggingface-hub  
	- numpy  
	- peft  
	- scipy  
	- sympy  
	- tensorboard  
	- tokenizers  
	- torch-summary  
	- torchvision  
	- tqdm  
	- transformers  
	- scikit-learn  
	- pandas  
	- tensorflow  
	- matplotlib

# [Quick Start](#contents)

After configuring the environment, you can start training and evaluation as follows:

- running on GPU

  ```python
  # run training and evaluation example
  python training/main.py
  ```

# [Script Description](#contents)

## [Script and Sample Code](#contents)

```bash
.
├── configs                                          # configurations for different paradigm models
│   ├── __init__.py                                  # relative package import
│   ├── config.py                                    # configuration for Uni-CTR
│   ├── config_multi_domain.py                       # configuration for multi-domain baselines
│   └── config_single_domain.py                      # configuration for single-domain baselines
├── layers                                           # network layers in models (mostly from package DeepCTR-torch)
│   ├── __init__.py                                  # relative package import
│   ├── activation.py                                # activation networks
│   ├── core.py                                      # core networks including ladders
│   ├── interaction.py                               # modules for single-domain models
│   ├── sequence.py                                  # sequence processing networks
│   └── utils.py                                     # other data processing methods and additional networks
├── miscellaneous
├── models                                           # all baseline models
│   ├── autoint.py
│   ├── basemodel.py
│   ├── dcn.py
│   ├── deepfm.py
│   ├── fibinet.py
│   ├── mmoe.py
│   ├── ple.py
│   ├── pnn.py
│   ├── sharedbottom.py
│   ├── star.py
│   └── xdeepfm.py
├── preprocessing                                    # data preprocessing
│   ├── amazon_review_data                           # preprocessing methods for Amazon Review Data (2018)
│   │   ├── data_analysis.ipynb
│   │   ├── multi_domain_raw_data_processing.py
│   │   ├── multi_domain_text_processing.py
│   │   ├── multi_domain_train_text_processing.py
│   │   └── one_for_all.py
│   └── utils.py
├── requirements.txt
├── training
│   ├── main.py
│   ├── main_multi_domain.py
│   └── main_single_domain.py
├── callbacks.py                                     # Early Stopping for single-domain models
├── inputs.py                                        # 
└── utils.py

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

# [Model Description](#contents)



# [Description of Random Situation](#contents)

We set the random seed before training in model_config.py.

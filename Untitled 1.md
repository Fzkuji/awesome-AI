
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
│   │   ├── data_analysis.ipynb                      # analyse the distributions of the domains
│   │   ├── multi_domain_raw_data_processing.py      # data preprocessing for baseline models
│   │   ├── multi_domain_text_processing.py          # prompt generation
│   │   └── one_for_all.py                           # whole dataset preprocessing pipeline for Uni-CTR
│   └── utils.py                                     # data preprocessing methods
├── training                                         # training files
│   ├── main.py                                      # train file for Uni-CTR
│   ├── main_multi_domain.py                         # train file for multi-domain models
│   └── main_single_domain.py                        # train file for single-domain models
├── requirements.txt                                 # package requirements
├── callbacks.py                                     # Early Stopping for single-domain models
├── inputs.py                                        # data transformation
└── utils.py                                         # general functions for Uni-CTR 

```

## [Script Parameters](#contents)

### [Uni-CTR](#contents)

Parameters for Uni-CTR can be set in `configs/config.py`

- Parameters for Amazon Review Data (2018)

```python
# LLM  
seed = 2012  
  
dataset = "amazon"  
dataset_mode = "w_clicked_product_title"  
if dataset_mode == "standard_dataset":  
    data_path = "./datasets/amazon_review_data/hybrid_data/hybrid_5_id.csv"  
    text_path = "./datasets/amazon_review_data/text_data/text_5_id.txt"  
    struct_path = "./datasets/amazon_review_data/filtered_data/filtered_5_id.csv"  
elif dataset_mode == "w_clicked_product_title":  
    data_path = "./datasets/amazon_review_data/hybrid_data/hybrid_5_title.csv"  
    text_path = "./datasets/amazon_review_data/text_data/text_5_title.txt"  
    struct_path = "./datasets/amazon_review_data/filtered_data/filtered_5_title.csv"  
  
'''Scenarios  
0: Amazon Fashion  
1: Digital Music  
2: Musical Instruments  
3: Gift Cards  
4: All Beauty  
'''  
scenarios = [0, 2, 3]  
device_ids = [0, 1, 2]  
device = torch.device("cuda:" + str(device_ids[0]) if torch.cuda.is_available() else "cpu")  
  
num_workers = 0  
weight_decay = 0.001  
  
# epoch 0  
lr = 8e-5  
max_lr = 5e-4  
  
text_encoder_models = [  
    # Name, num_hidden_layers, text_embedding_dim, max_length  
    ["tiny-bert-4l-en", 4, 312, 512],  
    ["bert-base-uncased", 12, 768, 512],  
    ["deberta-v3-base", 12, 768, 512],  
    ["deberta-v3-large", 24, 1024, 512],  
    ["gpt2", 12, 768, 1024],  
    ["Llama-2-7b-hf", 32, 4096, 4096],  
]  
  
text_encoder_model_name, layer_num, text_embedding_dim, max_length = text_encoder_models[0]  
  
if text_encoder_model_name == "tiny-bert-4l-en":  
    nlp_finetune_batch_size = 180 * len(device_ids)  
    ladder_frequency = 2  
elif text_encoder_model_name == "deberta-v3-base":  
    nlp_finetune_batch_size = 60 * len(device_ids)  
    ladder_frequency = 6  
else:  
    nlp_finetune_batch_size = 3 * len(device_ids)  
    ladder_frequency = 6  
  
text_encoder_model = os.path.join("./pretrained_models/", text_encoder_model_name)  
text_tokenizer = os.path.join("./pretrained_models/", text_encoder_model_name)  
  
ladder_block = ["wo_block", "w_lora", "w_self_attention", "w_transformer_block"]  
ladder_block = ladder_block[3]  
r = 4  
num_heads = 2  
narrowed_ratio = 0.25  

use_peft = True  
pretrained = False  
load_path = "/root/multi-domain/saved_models/amazon/multi_domain/llm_based/deberta-v3-base/w_transformer_block/w_clicked_product_title/v2/epoch0.pt"  
save_path = os.path.join("./saved_models", str(dataset), "multi_domain", "llm_based", text_encoder_model_name,  
                         ladder_block, dataset_mode)  
  
epochs = 2  
dropout = 0.2  
  
mixed_precision = True  
clip_grad = False  
clip_value = 1.0  
  
use_special_token = False
 ```

### [Multi-Domain Models](#contents)





### [Single-Domain Models](#contents)






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

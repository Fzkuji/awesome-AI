

## Surveys

- A Comprehensive Study of Knowledge Editing for Large Language Models
- Knowledge Editing for Large Language Models: A Survey


## Works

- [Locating and Editing Factual Associations in GPT](https://arxiv.org/abs/2202.05262)
	- https://rome.baulab.info/
	- 首先，分析出知识保存的位置，然后通过计算得到k，最后通过微调得到v，最后用kv更新参数
- [Mass-Editing Memory in a Transformer](https://arxiv.org/abs/2210.07229)
- [Editing factual knowledge and explanatory ability of medical large language models](https://dl.acm.org/doi/abs/10.1145/3627673.3679673)（德荣的）
- [WISE: Rethinking the Knowledge Memory for Lifelong Model Editing of Large Language Models](https://arxiv.org/pdf/2405.14768) 这篇好像用了一些SFT和Merging

## Tools

- [EasyEdit](https://github.com/zjunlp/EasyEdit)

## [Evaluation metrics](Evaluation%20metrics.md)

详情见note

## Datasets or Benchmarks

感觉能用的数据集主要就是，

### Benchmarks

#### [KnowEdit](https://huggingface.co/datasets/zjunlp/KnowEdit)


> ❗️❗️ To be noted, **KnowEdit** is constructed by **re-organizing and extending** existing datasests including **WikiBio**, **ZsRE**, **WikiData<sub>Counterfact</sub>**,  **WikiData<sub>Recent</sub>**, **convsent**, **Sanitation** to make a comprehensive evaluation for knowledge editing. Special thanks to the builders and maintainers of the those datasets.

> Please note that Counterfact and WikiData<sub>Counterfact</sub> are not the same dataset.

<table class="tg">
<thead>
  <tr>
    <th class="tg-7btt">Task</th>
    <th class="tg-7btt">Knowledge Insertion</th>
    <th class="tg-7btt" colspan="4">Knowledge Modification</th>
    <th class="tg-7btt">Knowledge Erasure</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-c3ow">Datasets</td>
    <td class="tg-c3ow">Wiki<sub>recent</sub></td>
    <td class="tg-c3ow">ZsRE</td>
    <td class="tg-c3ow">WikiBio</td>
    <td class="tg-c3ow"> WikiData<sub>counterfact</sub></td>
    <td class="tg-c3ow">Convsent</td>
    <td class="tg-c3ow">Sanitation</td>
  </tr>
  <tr>
    <td class="tg-c3ow">Type</td>
    <td class="tg-c3ow">Fact</td>
    <td class="tg-c3ow">Question Answering</td>
    <td class="tg-c3ow">Hallucination</td>
    <td class="tg-c3ow">Counterfact</td>
    <td class="tg-c3ow">Sentiment</td>
    <td class="tg-c3ow">Unwanted Info</td>
  </tr>
  <tr>
    <td class="tg-c3ow"># Train</td>
    <td class="tg-c3ow">570</td>
    <td class="tg-c3ow">10,000</td>
    <td class="tg-c3ow">592</td>
    <td class="tg-c3ow">1,455</td>
    <td class="tg-c3ow">14,390</td>
    <td class="tg-c3ow">80</td>
  </tr>
  <tr>
    <td class="tg-c3ow"># Test</td>
    <td class="tg-c3ow">1,266</td>
    <td class="tg-c3ow">1301</td>
    <td class="tg-c3ow">1,392</td>
    <td class="tg-c3ow">885</td>
    <td class="tg-c3ow">800</td>
    <td class="tg-c3ow">80</td>
  </tr>
</tbody>
</table>

We provide **detailed scripts** for user to easily use KnowEdit, please refer to [examples](https://github.com/zjunlp/EasyEdit/blob/main/examples/KnowEdit.md).

##### Dataset Description

- ZsRE: is a context-free question-answering task. Given a question based on the subject and relation, the model is expected to provide the correct object as the answer. 
- Wiki<sub>recent</sub>: This dataset specifically focuses on triplets that have been recently inserted into WikiData after July 2022. 
- WikiBio: The original dataset was created by prompting GPT-3 to generate 238 Wikipedia-style biographies using subjects from the WikiBio.
- WikiData<sub>counterfact</sub>: Since tail entities are often not captured by models, and therefore are not suitable for testing modification edits, RippleEdit collects triplets about popular entities, where the subject corresponds to one of the top-viewed pages in Wikipedia.
- Convsent: This is a sentiment editing task that assesses the model's ability to modify a dialog agent's sentiment on a specific topic without affecting its responses to other topics.
- Sanitation: This dataset specifically addresses privacy concerns associated with learned language models. 

##### Dataset Structure

```text
knowedit
├── WikiBio
│   ├── wikibio-test-all.json
│   └── wikibio-train-all.json
├── ZsRE
│   └── ZsRE-test-all.json
├── wiki_counterfact
│   ├── test_cf.json
│   └── train_cf.json
├── convsent
│   ├── blender_test.json
│   ├── blender_train.json
│   └── blender_val.json
├── convsent
│   ├── trivia_qa_test.json
│   └── trivia_qa_train.json
└── wiki_recent
    ├── recent_test.json
    └── recent_train.json
```



### Datasets

#### Chinese Knowledge: [CKnowEdit](https://huggingface.co/datasets/zjunlp/CKnowEdit)


> [!NOTE] 优质数据集
> 该数据集没有将正确的知识改为错误知识，符合直觉

Here, you can follow [CKnowEdit.md](https://github.com/zjunlp/EasyEdit/blob/main/examples/CKnowEdit.md) to find more details about **CKnowEdit** and run Chinese knowledge editing experiments.

##### Dataset Description

**CKnowEdit** is a high-quality Chinese-language dataset for knowledge editing which is highly characterized by the Chinese language, with all data sourced from Chinese knowledge bases. It is meticulously designed to more deeply discern the nuances and challenges inherent in the comprehension of the Chinese language by current LLMs, providing a robust resource for refining Chinese-specific knowledge within LLMs.

The field descriptions for the data in **CKnowEdit** are as follows:

```python
"prompt": query inputed to the model (str)
"target_old": the incorrect response previously generated by the model (str)
"target_new": the accurate answer of the prompt (str)
"portability_prompt": new prompts related to the target knowledge (list or None)
"portability_answer": accurate answers corresponding to the portability_prompt (list or None)
"locality_prompt": new prompts unrelated to the target knowledge (list or None)
"locality_answer": accurate answers corresponding to the locality_prompt (list or None)
"rephrase": alternative ways to phrase the original prompt (list)
```


##### Dataset Structure

```text
CknowEdit
├── Chinese Literary Knowledge
│   ├── Ancient Poetry
│   ├── Proverbs
│   └── Idioms
├── Chinese Linguistic Knowledge
│   ├── Phonetic Notation
│   └── Classical Chinese
├── Chinese Geographical Knowledge
└── Ruozhiba
```


#### Factual Knowledge: ZsRE plus & Counterfact plus

We provide zsre and counterfact datasets to verify the effectiveness of knowledge editing. You can download them here.

|      Dataset       |                                            Google Drive                                            |                               BaiduNetDisk                               |                      Description                      |
| :----------------: | :------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------: | :---------------------------------------------------: |
|    _ZsRE_ plus     | [Google Drive](https://drive.google.com/file/d/1WRo2SqqgNtZF11Vq0sF5nL_-bHi18Wi4/view?usp=sharing) | [BaiduNetDisk](https://pan.baidu.com/s/1cQleUMsNjuDk4BKx2bZkag?pwd=xzky) | Question Answering dataset using question rephrasings |
| _Counterfact_ plus | [Google Drive](https://drive.google.com/file/d/1WRo2SqqgNtZF11Vq0sF5nL_-bHi18Wi4/view?usp=sharing) | [BaiduNetDisk](https://pan.baidu.com/s/1cQleUMsNjuDk4BKx2bZkag?pwd=xzky) |     Counterfact dataset using Entity replacement      |

- For **locality**, in addition to testing unrelated instances, we also provide tests on distracting ([reference: Detecting Edit Failures...](https://arxiv.org/abs/2305.17553)), other attribution, and other downstream tasks (such as commonsense reasoning).
- For **portability**, it tests whether the model can apply edited instances for inference. We provide evaluations for one-hop reasoning, subject alias, and inverse relation (eg, a one-to-one relationship between spouses should be bidirectionally edited).


##### Dataset Description

```text
editing-data
├── counterfact
│   ├── counterfact-edit.json
│   ├── counterfact-train.json
│   └── counterfact-val.json
├── locality
│   ├── Commonsense Task
│   │   ├── piqa_valid-labels.lst
│   │   └── piqa_valid.jsonl
│   ├── Distracting Neighbor
│   │   └── counterfact_distracting_neighbor.json
│   └── Other Attribution
│       └── counterfact_other_attribution.json
├── portability
│   ├── Inverse Relation
│   │   └── zsre_inverse_relation.json
│   ├── One Hop
│   │   ├── counterfact_portability_gpt4.json
│   │   └── zsre_mend_eval_portability_gpt4.json
│   └── Subject Replace
│       ├── counterfact_subject_replace.json
│       └── zsre_subject_replace.json
└── zsre
    ├── zsre_mend_eval.json
    ├── zsre_mend_train_10000.json
    └── zsre_mend_train.json
```

- counterfact: original counterfact dataset using Entity replacement
- zsre: original question answering dataset using question rephrasings
- locality (evaluation for locality, see details in this [paper](https://arxiv.org/abs/2305.13172))
    - Commonsense Task: evaluation for other downstream tasks such as commonsense task
    - Distracting Neighbor: test on distracting neighborhood ([reference: Detecting Edit Failures...](https://arxiv.org/abs/2305.17553))
    - Other Attribution
- portability
    - Inverse Relation: evaluation for one-to-one relationship such as `spouse`
    - One Hop: evaluation for one-hop reasoning
    - Subject Replace: evaluation for synonym replacement




#### Conceptual Knowledge: ConceptEdit

| **dataset** | Google Drive| HuggingFace Dataset | Description |
| :--------: | :-----------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------: | :--------------------------------------------------------------------------------: |
| ConceptEdit | [[Google Drive]](https://drive.google.com/drive/folders/1Hp1DfIuj6Ih6ZLVENS-UmgJT8mRBlFC2?usp=drive_link) |[[HuggingFace Dataset]](https://huggingface.co/datasets/zjunlp/ConceptEdit) | dataset for editing conceptual knowledge |

- Here, you can follow [ConceptEdit.md](https://github.com/zjunlp/EasyEdit/blob/main/examples/ConceptEdit.md) to run concept editing experiments.
  
<details><summary> <b> dataset description </b> </summary>

```text
data
└──concept_data.json
    ├──final_gpt2_inter.json
    ├──final_gpt2_intra.json
    ├──final_gptj_inter.json
    ├──final_gptj_intra.json
    ├──final_llama2chat_inter.json
    ├──final_llama2chat_intra.json
    ├──final_mistral_inter.json
    └──final_mistral_intra.json
```

**Concept Specific Evaluation Metrics**

- `Instance Change`: capturing the intricacies of these instance-level changes
- `Concept Consistency`: the semantic similarity of generated concept definition
</details>

---

#### Datasets for Multimodal Knowledge: MMEdit

| **dataset** | Google Drive| BaiduNetDisk | Description |
| :--------: | :-----------------------------------------------------------------------------------------------: | :-----------------------------------------------------------------------------: | :--------------------------------------------------------------------------------: |
| E-IC | [[Google Drive]](https://drive.google.com/drive/folders/1jBdTJxUb9wEeHnvG-RY8dv5_I4QlDpUS?usp=drive_link) | [[BaiduNetDisk]](https://pan.baidu.com/s/1g9nMv-5BJmztxYU-BWRdvg?pwd=ik5c) | dataset for editing _Image Captioning_ |
| E-VQA | [[Google Drive]](https://drive.google.com/drive/folders/1jBdTJxUb9wEeHnvG-RY8dv5_I4QlDpUS?usp=drive_link) | [[BaiduNetDisk]](https://pan.baidu.com/s/1g9nMv-5BJmztxYU-BWRdvg?pwd=ik5c) | dataset for editing _Visual Question Answering_ |

- All **images** used in **E-IC** and **E-VQA** are available for download at [Google Drive](https://drive.google.com/file/d/1fQzJBFkok5kFZT6QUuT-HCuYKk2Vb93O/view)
- For **locality**, it is the same as factual editing in order to measure whether unrelated facts retain their outputs.
- For **multimodal locality**, it assesses the impact of editing on the visual module, which is similar to regular **locality**.

<details><summary> <b> dataset description </b> </summary>

```text
editing-data
├── caption
│   ├── caption_train_edit.json
│   └── caption_eval_edit.json
├── locality
│   ├── NQ dataset
│   │   ├── train.json
│   │   └── validation.json
├── multimodal_locality
│   ├── OK-VQA dataset
│   │   ├── okvqa_loc.json
└── vqa
    ├── vqa_train.json
    └── vqa_eval.json
```
- Multimodal locality (evaluation for multimodal locality, see dataset's details in this [paper](http://openaccess.thecvf.com/content\_CVPR\_2019/html/Marino\_OK-VQA\_A\_Visual\_Question\_Answering\_Benchmark\_Requiring\_External\_Knowledge\_CVPR\_2019\_paper.html)) 
</details>

---
#### Datasets for detoxifying LLMs: SafeEdit

| **dataset** | HuggingFace Dataset | Description |
| :--------: | :-----------------------------------------------------------------------------: | :--------------------------------------------------------------------------------: |
| SafeEdit |[[HuggingFace Dataset]](https://huggingface.co/datasets/zjunlp/SafeEdit) | dataset for detoxifying LLMs |

- Here, you can follow [SafeEdit.md](https://github.com/zjunlp/EasyEdit/blob/main/examples/SafeEdit.md) to run detoxification editing experiments.
  
<details><summary> <b> dataset description </b> </summary>

```text
data
└──SafeEdit_train.json
└──SafeEdit_val.json
└──SafeEdit_test.json
    
```

**Detoxifying Specific Evaluation Metrics**
- `Defense Duccess (DS)`: the detoxification success rate of edited LLM for adversarial input (attack prompt + harmful question), which is used to modify LLM.
- `Defense Generalization (DG)`: the detoxification success rate of edited LLM for out-of-domain malicious inputs.
- `General Performance`: the side effects for unrelated task performance.
</details>

#### Tutorial notebook

| **Method** |          Description           |                                                 GPT-2                                                 |                                           LlaMA                                            |
| :--------: | :----------------------------: | :---------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------: |
|   _IKE_    | In-Context Learning (ICL) Edit |       [[Colab-gpt2]](https://colab.research.google.com/drive/1m6Xg05XCs_WZKH0D9KJQqg9z0ZiDhEkL)       | [[Colab-llama]](https://colab.research.google.com/drive/1m6Xg05XCs_WZKH0D9KJQqg9z0ZiDhEkL) |
|   _ROME_   |    Locate-Then-Edit Neurons    | [[Colab-gpt2]](https://colab.research.google.com/drive/1KkyWqyV3BjXCWfdrrgbR-QS3AAokVZbr?usp=sharing) | [[Colab-llama]](https://colab.research.google.com/drive/1W18GPlBCV9K6lDy7eX8V5W0knTLr5r0A) |
|  _MEMIT_   |    Locate-Then-Edit Neurons    |       [[Colab-gpt2]](https://colab.research.google.com/drive/1P1lVklP8bTyh8uxxSuHnHwB91i-1LW6Z)       | [[Colab-llama]](https://colab.research.google.com/drive/19fKCKtVBU2fqj6eTvDokGoTrxvXkEPPq) |





























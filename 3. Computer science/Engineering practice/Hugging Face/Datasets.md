


#### datasets.load_dataset

Load a dataset from the Hugging Face Hub:

```python
from datasets import load_dataset
ds = load_dataset(
	name='rotten_tomatoes', 
	split='train',
	cache_dir='~/.cache/huggingface/datasets'
)

data_files = {'train': 'train.csv', 'test': 'test.csv'}
ds = load_dataset('namespace/your_dataset_name', data_files=data_files)
```
Given performance:
Model | hyperparams file | Val. PER | Test PER | GPUs
------------ | ------------ | ------------ | ------------ | ------------
CRDNN + CTC | train.yaml | 12.80 | 14.78 | 1xV100 16GB
CRDNN + AttentionalRNN + CTC | train.yaml | 12.50 | 14.07| 1xV100 16GB
wav2vec + VanillaNN + AttentionalRNN + CTC | train_with_wav2vec2.yaml | 7.11 | 8.04 | 1xV100 32GB

Trained performance:
Model | hyperparams file | Val. PER | Test PER | GPUs
------------ | ------------ | ------------ | ------------ | ------------
CRDNN + CTC | train.yaml | 12.31 | 14.36 | 1xRTX3080 Laptop 16GB
CRDNN + AttentionalRNN + CTC | train.yaml | 12.31 | 13.81| 1xRTX3080 Laptop 16GB
wav2vec + VanillaNN + AttentionalRNN + CTC | train_with_wav2vec2.yaml | 7.24 | 8.87 | 1xV100 32GB

Title: SpaRCe Improved Learning of Reservoir Computing Systems through Sparse Representations

## Link
Link: https://arxiv.org/abs/1912.08124

## Abstract

"Sparse" neural networks, in which relatively few neurons or connections are active, are common in both machine learning and neuroscience. Whereas in machine learning, "sparsity" is related to a penalty term that leads to some connecting weights becoming small or zero, in biological brains, sparsity is often created when high spiking thresholds prevent neuronal activity. Here we introduce sparsity into a reservoir computing network via neuron-specific learnable thresholds of activity, allowing neurons with low thresholds to contribute to decision-making but suppressing information from neurons with high thresholds. This approach, which we term "SpaRCe", optimises the sparsity level of the reservoir without affecting the reservoir dynamics. The read-out weights and the thresholds are learned by an on-line gradient rule that minimises an error function on the outputs of the network. Threshold learning occurs by the balance of two opposing forces: reducing inter-neuronal correlations in the reservoir by deactivating redundant neurons, while increasing the activity of neurons participating in correct decisions. We test SpaRCe on classification problems and find that threshold learning improves performance compared to standard reservoir computing. SpaRCe alleviates the problem of catastrophic forgetting, a problem most evident in standard echo state networks and recurrent neural networks in general, due to increasing the number of task-specialised neurons that are included in the network decisions.



## 概述
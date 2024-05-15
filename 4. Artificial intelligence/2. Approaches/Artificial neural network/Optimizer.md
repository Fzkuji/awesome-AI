

## Examples

### SGD/AdaGrad/Adam

https://zhuanlan.zhihu.com/p/32230623

https://www.zhihu.com/question/323747423/answer/2576604040

### Adam

```python
# Initialize hyperparameters
alpha = learning_rate
beta1 = 0.9  # Exponential decay rate for the 1st moment estimates
beta2 = 0.999  # Exponential decay rate for the 2nd moment estimates
epsilon = 1e-8  # Small constant for numerical stability
lambda = weight_decay

# Initialize variables
t = 0  # Time step
m = 0  # 1st moment (mean) of gradients
v = 0  # 2nd moment (uncentered variance) of gradients

for epoch in range(epochs):
    for batch in dataset:
        t += 1  # Increment time step
        grad = compute_gradient(batch)  # Calculate gradient for the current batch

        # Apply weight decay to the gradients
        grad += lambda * model_parameters

        # Update biased 1st moment estimate
        m = beta1 * m + (1 - beta1) * grad

        # Update biased 2nd moment estimate
        v = beta2 * v + (1 - beta2) * grad**2

        # Compute bias-corrected 1st moment estimate
        m_hat = m / (1 - beta1**t)

        # Compute bias-corrected 2nd moment estimate
        v_hat = v / (1 - beta2**t)

        # Update model parameters
        for param in model_parameters:
            param -= alpha * m_hat / (torch.sqrt(v_hat) + epsilon)

```

In the pseudocode, `alpha`, `beta1`, `beta2`, `epsilon`, and `lambda` are hyperparameters that control the behavior of the optimizer. The loop iterates through each epoch and batch of the dataset, calculating gradients and updating the model parameters.

### AdamW

```python
# Initialize hyperparameters
alpha = learning_rate
beta1 = 0.9  # Exponential decay rate for the 1st moment estimates
beta2 = 0.999  # Exponential decay rate for the 2nd moment estimates
epsilon = 1e-8  # Small constant for numerical stability
lambda = weight_decay

# Initialize variables
t = 0  # Time step
m = 0  # 1st moment (mean) of gradients
v = 0  # 2nd moment (uncentered variance) of gradients

for epoch in range(epochs):
    for batch in dataset:
        t += 1  # Increment time step
        grad = compute_gradient(batch)  # Calculate gradient for the current batch

        # Update biased 1st moment estimate
        m = beta1 * m + (1 - beta1) * grad

        # Update biased 2nd moment estimate
        v = beta2 * v + (1 - beta2) * grad**2

        # Compute bias-corrected 1st moment estimate
        m_hat = m / (1 - beta1**t)

        # Compute bias-corrected 2nd moment estimate
        v_hat = v / (1 - beta2**t)

        # Update model parameters with decoupled weight decay
        for param in model_parameters:
            delta = alpha * m_hat / (torch.sqrt(v_hat) + epsilon)
            param -= delta + lambda * param
```

In the pseudocode, `alpha`, `beta1`, `beta2`, `epsilon`, and `lambda` are hyperparameters that control the behavior of the optimizer. The loop iterates through each epoch and batch of the dataset, calculating gradients and updating the model parameters with decoupled weight decay.

The main difference between the original Adam and AdamW is how they handle weight decay regularization. In the original Adam optimizer, weight decay is applied directly to the gradients before they are used to update the model weights (see the line `grad += lambda * model_parameters`). This can lead to an undesired interaction between the weight decay and the adaptive learning rates.

> We use AdamW (Loshchilov and Hutter, 2019) for Chinchilla rather than Adam (Kingma and Ba, 2014) as this improves the language modelling loss and the downstream task performance after finetuning.
> -- [](4.%20Artificial%20intelligence/2.%20Approaches/Artificial%20neural%20network/Transformer/Large%20language%20model/Models/Chinchilla.md#^f76ade|Chinchilla)



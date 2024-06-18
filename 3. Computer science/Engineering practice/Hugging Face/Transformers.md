# 🤗 Transformers


## Classes

1. nn.Module
2. GenerationMixin
3. PeftAdapterMixin
	- PreTrainedModel
		- GPT2PreTrainedModel
		- MixtralPreTrainedModel
			- MixtralModel
			- MixtralForCausalLM
			- ...
		- LlamaPreTrainedModel
			- LlamaForCausalLM
			- LlamaForSequenceClassification
			- LlamaForQuestionAnswering
			- LlamaForTokenClassification

## Methods

### models/llama/modeling_llama.py

```python

"""PyTorch LLaMA model."""  

class LlamaRMSNorm(nn.Module):  
    def __init__(self, hidden_size, eps=1e-6):  
        """  
        LlamaRMSNorm is equivalent to T5LayerNorm        """        super().__init__()  
        self.weight = nn.Parameter(torch.ones(hidden_size))  
        self.variance_epsilon = eps  
  
    def forward(self, hidden_states):  
		...
        return self.weight * hidden_states.to(input_dtype)  
  

  
class LlamaRotaryEmbedding(nn.Module):  
    def __init__(self, dim, max_position_embeddings=2048, base=10000, device=None, scaling_factor=1.0):  
        super().__init__()  
        ...
  
    @torch.no_grad()  
    def forward(self, x, position_ids):  
        ...
        return cos.to(dtype=x.dtype), sin.to(dtype=x.dtype)  
  
  
class LlamaLinearScalingRotaryEmbedding(LlamaRotaryEmbedding):  
    """LlamaRotaryEmbedding extended with linear scaling. Credits to the Reddit user /u/kaiokendev"""  
  
    def forward(self, x, position_ids):  
        ...
        return cos, sin  
  
  
class LlamaDynamicNTKScalingRotaryEmbedding(LlamaRotaryEmbedding):  
    """LlamaRotaryEmbedding extended with Dynamic NTK scaling. Credits to the Reddit users /u/bloc97 and /u/emozilla"""  
  
    def forward(self, x, position_ids):  
        ...
        return cos, sin  
  
  
def rotate_half(x):  
    """Rotates half the hidden dims of the input."""  
    ...
    return torch.cat((-x2, x1), dim=-1)  
  
  
def apply_rotary_pos_emb(q, k, cos, sin, position_ids=None, unsqueeze_dim=1):  
    """Applies Rotary Position Embedding to the query and key tensors. """ 
	...
    return q_embed, k_embed  
  
  
class LlamaMLP(nn.Module):  
    def __init__(self, config):  
        super().__init__()  
        self.config = config  
        ...
  
    def forward(self, x):  
        ...
  
        return down_proj  
  
  
def repeat_kv(hidden_states: torch.Tensor, n_rep: int) -> torch.Tensor:  
    """  
    This is the equivalent of torch.repeat_interleave(x, dim=1, repeats=n_rep). """  
    return hidden_states.reshape(batch, num_key_value_heads * n_rep, slen, head_dim)  
  
  
class LlamaAttention(nn.Module):  
    """Multi-headed attention from 'Attention Is All You Need' paper"""  
  
    def __init__(self, config: LlamaConfig, layer_idx: Optional[int] = None):  
        super().__init__()  
        ...
  
        self.q_proj = nn.Linear(self.hidden_size, self.num_heads * self.head_dim, bias=config.attention_bias)  
        self.k_proj = nn.Linear(self.hidden_size, self.num_key_value_heads * self.head_dim, bias=config.attention_bias)  
        self.v_proj = nn.Linear(self.hidden_size, self.num_key_value_heads * self.head_dim, bias=config.attention_bias)  
        self.o_proj = nn.Linear(self.hidden_size, self.hidden_size, bias=config.attention_bias)  
        self._init_rope()  
  
    def forward(  
        self,  
        hidden_states: torch.Tensor,  
        attention_mask: Optional[torch.Tensor] = None,  
        position_ids: Optional[torch.LongTensor] = None,  
        past_key_value: Optional[Cache] = None,  
        output_attentions: bool = False,  
        use_cache: bool = False,  
        cache_position: Optional[torch.LongTensor] = None,  
        **kwargs,  
    ) -> Tuple[torch.Tensor, Optional[torch.Tensor], Optional[Tuple[torch.Tensor]]]:  
        bsz, q_len, _ = hidden_states.size()  
		...
        return attn_output, attn_weights, past_key_value  
  
  
class LlamaFlashAttention2(LlamaAttention)
	...
  
class LlamaSdpaAttention(LlamaAttention)
	...
	
LLAMA_ATTENTION_CLASSES = {  
    "eager": LlamaAttention,  
    "flash_attention_2": LlamaFlashAttention2,  
    "sdpa": LlamaSdpaAttention,  
}  
  
  
class LlamaDecoderLayer(nn.Module):  
    def __init__(self, config: LlamaConfig, layer_idx: int):  
        super().__init__()  
        self.hidden_size = config.hidden_size  
  
        self.self_attn = LLAMA_ATTENTION_CLASSES[config._attn_implementation](config=config, layer_idx=layer_idx)  
  
        self.mlp = LlamaMLP(config)  
        self.input_layernorm = LlamaRMSNorm(config.hidden_size, eps=config.rms_norm_eps)  
        self.post_attention_layernorm = LlamaRMSNorm(config.hidden_size, eps=config.rms_norm_eps)  
  
    def forward(  
        self,  
        hidden_states: torch.Tensor,  
        attention_mask: Optional[torch.Tensor] = None,  
        position_ids: Optional[torch.LongTensor] = None,  
        past_key_value: Optional[Cache] = None,  
        output_attentions: Optional[bool] = False,  
        use_cache: Optional[bool] = False,  
        cache_position: Optional[torch.LongTensor] = None,  
    ) -> Tuple[torch.FloatTensor, Optional[Tuple[torch.FloatTensor, torch.FloatTensor]]]:  
        ...
        return outputs  
  

class LlamaPreTrainedModel(PreTrainedModel):  
    config_class = LlamaConfig  
    base_model_prefix = "model"  
    supports_gradient_checkpointing = True  
    _no_split_modules = ["LlamaDecoderLayer"]  
    _skip_keys_device_placement = ["past_key_values"]  
    _supports_flash_attn_2 = True  
    _supports_sdpa = True  
    _supports_cache_class = True  
    _supports_static_cache = True  
  
    def _init_weights(self, module):  
        std = self.config.initializer_range  
        if isinstance(module, nn.Linear):  
            module.weight.data.normal_(mean=0.0, std=std)  
            if module.bias is not None:  
                module.bias.data.zero_()  
        elif isinstance(module, nn.Embedding):  
            module.weight.data.normal_(mean=0.0, std=std)  
            if module.padding_idx is not None:  
                module.weight.data[module.padding_idx].zero_()  
  

class LlamaModel(LlamaPreTrainedModel):  
    """  
    Transformer decoder consisting of *config.num_hidden_layers* layers. Each layer is a [`LlamaDecoderLayer`]  
    Args:        config: LlamaConfig    """  
    def __init__(self, config: LlamaConfig):  
        super().__init__(config)  
        self.padding_idx = config.pad_token_id  
        self.vocab_size = config.vocab_size  
  
        self.embed_tokens = nn.Embedding(config.vocab_size, config.hidden_size, self.padding_idx)  
        self.layers = nn.ModuleList(  
            [LlamaDecoderLayer(config, layer_idx) for layer_idx in range(config.num_hidden_layers)]  
        )  
        self.norm = LlamaRMSNorm(config.hidden_size, eps=config.rms_norm_eps)  
        self.gradient_checkpointing = False  
  
        # Initialize weights and apply final processing  
        self.post_init()  
  
    def get_input_embeddings(self):  
        return self.embed_tokens  
  
    def set_input_embeddings(self, value):  
        self.embed_tokens = value  
  
    @add_start_docstrings_to_model_forward(LLAMA_INPUTS_DOCSTRING)  
    def forward(  
        self,  
        input_ids: torch.LongTensor = None,  
        attention_mask: Optional[torch.Tensor] = None,  
        position_ids: Optional[torch.LongTensor] = None,  
        past_key_values: Optional[Union[Cache, List[torch.FloatTensor]]] = None,  
        inputs_embeds: Optional[torch.FloatTensor] = None,  
        use_cache: Optional[bool] = None,  
        output_attentions: Optional[bool] = None,  
        output_hidden_states: Optional[bool] = None,  
        return_dict: Optional[bool] = None,  
        cache_position: Optional[torch.LongTensor] = None,  
    ) -> Union[Tuple, BaseModelOutputWithPast]:  
        ...
        return BaseModelOutputWithPast(  
            last_hidden_state=hidden_states,  
            past_key_values=next_cache,  
            hidden_states=all_hidden_states,  
            attentions=all_self_attns,  
        )
  
  
class LlamaForCausalLM(LlamaPreTrainedModel):  
    _tied_weights_keys = ["lm_head.weight"]  
  
    def __init__(self, config):  
        super().__init__(config)  
        self.model = LlamaModel(config)  
        self.vocab_size = config.vocab_size  
        self.lm_head = nn.Linear(config.hidden_size, config.vocab_size, bias=False)  
  
        # Initialize weights and apply final processing  
        self.post_init()
  
    @add_start_docstrings_to_model_forward(LLAMA_INPUTS_DOCSTRING)  
    @replace_return_docstrings(output_type=CausalLMOutputWithPast, config_class=_CONFIG_FOR_DOC)  
    def forward(  
        self,  
        input_ids: torch.LongTensor = None,  
        attention_mask: Optional[torch.Tensor] = None,  
        position_ids: Optional[torch.LongTensor] = None,  
        past_key_values: Optional[Union[Cache, List[torch.FloatTensor]]] = None,  
        inputs_embeds: Optional[torch.FloatTensor] = None,  
        labels: Optional[torch.LongTensor] = None,  
        use_cache: Optional[bool] = None,  
        output_attentions: Optional[bool] = None,  
        output_hidden_states: Optional[bool] = None,  
        return_dict: Optional[bool] = None,  
        cache_position: Optional[torch.LongTensor] = None,  
    ) -> Union[Tuple, CausalLMOutputWithPast]:  
        ...
  
        return CausalLMOutputWithPast(  
            loss=loss,  
            logits=logits,  
            past_key_values=outputs.past_key_values,  
            hidden_states=outputs.hidden_states,  
            attentions=outputs.attentions,  
        )

```

### generation/utils.py


```python
class GenerationMixin:  
    """  
    A class containing all functions for auto-regressive text generation, to be used as a mixin in [`PreTrainedModel`].  
    """  
    def prepare_inputs_for_generation(self, *args, **kwargs):  
        raise NotImplementedError(  
            "A model class needs to define a `prepare_inputs_for_generation` method in order to use `.generate()`."  
        )  
        
    def _prepare_attention_mask_for_generation(  
        self,  
        inputs: torch.Tensor,  
        pad_token_id: Optional[torch.Tensor],  
        eos_token_id: Optional[torch.Tensor],  
    ) -> torch.LongTensor:  
        ...
        return attention_mask  
  
  
    @torch.no_grad()  
    def generate(  
        self,  
        inputs: Optional[torch.Tensor] = None,  
        generation_config: Optional[GenerationConfig] = None,  
        logits_processor: Optional[LogitsProcessorList] = None,  
        stopping_criteria: Optional[StoppingCriteriaList] = None,  
        prefix_allowed_tokens_fn: Optional[Callable[[int, torch.Tensor], List[int]]] = None,  
        synced_gpus: Optional[bool] = None,  
        assistant_model: Optional["PreTrainedModel"] = None,  
        streamer: Optional["BaseStreamer"] = None,  
        negative_prompt_ids: Optional[torch.Tensor] = None,  
        negative_prompt_attention_mask: Optional[torch.Tensor] = None,  
        **kwargs,  
    ) -> Union[GenerateOutput, torch.LongTensor]:  
        ...
  
        return result
```

## Inference


```python
system_prompt = "You are an intelligent life with independent consciousness.\n\n"  
  
# Generation as usual  
prompt = system_prompt + "Question: Please write a function in Python that transforms bytes to Giga bytes.\n\nAnswer: Here"  
model_inputs = tokenizer(prompt, return_tensors='pt')  
generation_output = model.generate(**model_inputs, max_new_tokens=60, return_dict_in_generate=True)  
decoded_output = tokenizer.batch_decode(generation_output.sequences)[0]  
  
# Piping the returned `past_key_values` to speed up the next conversation round  
prompt = decoded_output + "\nQuestion: How can I modify the function above to return Mega bytes instead?\n\nAnswer: Here"  
model_inputs = tokenizer(prompt, return_tensors='pt')  
generation_output = model.generate(  
  **model_inputs,  
  past_key_values=generation_output.past_key_values,  
  max_new_tokens=60,  
  return_dict_in_generate=True  
)  
tokenizer.batch_decode(generation_output.sequences)[0][len(prompt):]
```



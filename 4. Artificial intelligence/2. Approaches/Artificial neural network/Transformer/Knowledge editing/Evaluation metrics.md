以下是适配 **Obsidian** 格式的完善版本：

---

# Reliability

**定义**  
Reliability 衡量编辑操作是否成功执行，即模型是否能够根据指定的**编辑描述符**（editing descriptor）正确地更新知识。

**公式**

Reliability=成功编辑的样本数总编辑样本数\text{Reliability} = \frac{\text{成功编辑的样本数}}{\text{总编辑样本数}}

**示例**  
编辑任务：

```
Subject: Space Needle  
Editing Target: Located in → Paris  
Prompt: "The Space Needle is located in which city?"
```

若编辑后的模型能够回答 "Paris"，则视为成功。Reliability 是成功编辑的比例。

---

# Generalization

**定义**  
Generalization 衡量编辑后的知识能否在不同语义表述或相关上下文中成功应用。编辑范围包括
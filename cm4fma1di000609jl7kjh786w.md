---
title: "PaliGemma 2 - VLMs made easy"
datePublished: Sun Dec 08 2024 13:05:51 GMT+0000 (Coordinated Universal Time)
cuid: cm4fma1di000609jl7kjh786w
slug: paligemma-2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733661916659/5268b3ad-67c5-4364-90aa-692989f51f43.webp
tags: opensource, google, huggingface, finetuning, vlm

---

# Introduction

The evolution of vision-language models has been nothing short of remarkable. From their early stages of independently handling images and text to their current ability to seamlessly integrate the two, these models have reached new heights. Imagine describing the content of a photo, answering detailed questions about it, or creating vivid images from mere text — these are the feats made possible by modern vision-language models.

Fine-tuning these models is the key to unlocking their full potential. While pre-trained models like PaliGemma 2 offer impressive capabilities out of the box, adapting them to specific datasets or tasks can significantly boost their performance. Fine-tuning ensures the model not only generalizes well but also excels in understanding the context and nuances of your application, whether it's medical imaging, e-commerce, or creative content generation.

---

### Meet PaliGemma 2

PaliGemma 2, the latest open-source vision-language model released by Google, is a testament to how far these technologies have come. This sophisticated system takes images and text as inputs and generates textual outputs. Whether you’re creating captions for photos or answering intricate visual questions, PaliGemma 2 is designed to handle it all.

#### Key Components

* **SigLIP-So400m**: The image encoder, built with a philosophy similar to CLIP, excels at jointly understanding images and text. It processes visual data with remarkable accuracy, making it a robust foundation for multimodal tasks.
    
* **Gemma-2B**: The text decoder, a powerhouse explicitly crafted for generating coherent and contextually rich text.
    

By connecting **SigLIP**'s capabilities with **Gemma** via a simple linear adapter, **PaliGemma 2** emerges as a comprehensive solution. Pre-trained on image-text datasets, it is versatile enough to tackle various tasks, such as:

* **Image Captioning**: Generating detailed descriptions of images.
    
* **Segmentation**: Identifying and labeling objects in images.
    
* **Question Answering**: Given multimodal inputs of Images and related questions, we can have it answer questions for us.
    

---

# Let’s get started

<div data-node-type="callout">
<div data-node-type="callout-emoji">🖥</div>
<div data-node-type="callout-text">Before diving into fine-tuning PaliGemma 2, it's crucial to be prepared for the resource demands. This process will require <strong>a TON of GPU memory</strong>. If you're planning to experiment with Kaggle's free-tier environment, note that its <strong>2x T4 GPUs</strong> were not powerful enough.</div>
</div>

<div data-node-type="callout">
<div data-node-type="callout-emoji">🦾</div>
<div data-node-type="callout-text">However, You can try using <strong>Google Cloud Platform</strong> with <strong>AI Notebooks</strong> and opt for a <strong>NVIDIA A100 GPU</strong>, which provides significantly more memory and computational power. This setup should offer a smoother experience for fine-tuning the model effectively.</div>
</div>

### Installing Packages

```python
!pip install -q -U git+https://github.com/huggingface/transformers.git datasets accelerate peft
!pip install -U bitsandbytes  # for QLoRA and LoRA
```

### Loading our Authentication Keys from HuggingFace

To fine-tune **PaliGemma 2** or work with any Hugging Face tools, you'll need to authenticate using an access token. Follow these steps to generate and export it:

1. **Get Your Access Token**  
    Log in to your Hugging Face account and navigate to the Access Tokens page.
    
    * If you don’t already have a token, create one by clicking "New Token".
        
    * Assign the necessary scope (e.g., `write` access for fine-tuning tasks).
        
2. **Load your Token into your code**
    

```python
from kaggle_secrets import UserSecretsClient
import os
user_secrets = UserSecretsClient()
hf_secret = user_secrets.get_secret("HF General")
os.environ["HF_General"] = hf_secret
```

**OR**

```python
import os  
os.environ["HF_General"] = "<your_access_token>"
```

### Authenticate using the Hugging Face

```python
!huggingface-cli login --token $HF_General
print("Done Authentication")
```

### Loading our Data

To fine-tune **PaliGemma 2**, we’ll use a [**Chart Question Answering**](https://huggingface.co/datasets/HuggingFaceM4/ChartQA) **(ChartQA) dataset** available on Hugging Face's `datasets` library. This dataset includes pairs of images and questions about them, along with corresponding answers, making it perfect for multimodal fine-tuning tasks.

```python
from datasets import load_dataset
print("Started to Load Dataset")
train_ds = load_dataset('HuggingFaceM4/ChartQA', split="train+val")
print("Done Loading Dataset")
```

```python
cols_remove = ["human_or_machine"]
train_ds = train_ds.remove_columns(cols_remove)
```

```python
test_ds = load_dataset('HuggingFaceM4/ChartQA', split="test") 
test_ds = test_ds.remove_columns(cols_remove)
```

### Loading the (Pre) Processor

To prepare our dataset for **Paligemma 2**, we’ll use the `PaliGemmaProcessor`. This processor handles both image processing and text tokenization, simplifying the workflow for fine-tuning vision-language models.

#### Loading the Processor

First, load the processor for the 224x224 version of **PaliGemma 2**, which is more memory-efficient and suitable for general-purpose tasks:

```python
from transformers import PaliGemmaProcessor
model_id = "google/paligemma2-3b-pt-224"
processor = PaliGemmaProcessor.from_pretrained(model_id)
print("Done Loading Model")
```

There are higher-resolution versions available (448x448 and 896x896) as well as models with larger number of Parameters (10B, 28B) for tasks requiring more precision, like OCR or detailed segmentation. However, these demand more GPU memory and computation power.

Set the device to ‘cuda’ to use the GPU and load the model. We will Specify that the model should use `bfloat16` (Brain Float 16) precision for its parameters. `bfloat16` is a 16-bit floating point format that helps speed up computation and reduces memory usage while maintaining a similar range to `float32`.

### Preparing the model layers

To prepare **PaliGemma 2** for fine-tuning, we freeze the vision tower by setting `requires_grad=False` for its parameters, preserving its pre-trained visual features, while enabling training for the multi-modal projector by setting `requires_grad=True`, allowing it to adapt image-text alignment to the task. This setup ensures efficient use of pre-trained features while optimizing task-specific components.

```python
# Vision Tower Parameters (Image Encoder)
for param in model.vision_tower.parameters():
    param.requires_grad = False

# Multi-Modal Projector Parameters (Fine-Tuning the Decoder)
for param in model.multi_modal_projector.parameters():
    param.requires_grad = True
```

> We will load the model, and freeze the image encoder and the projector, and only fine-tune the decoder. If your images are within a particular domain, which might not be in the dataset the model was pre-trained with, you might want to skip freezing the image encoder. —Hugging Face Blog.

### Why Freeze the Image Encoder and Projector?

Freezing the **image encoder** and **multi-modal projector** in a pre-trained model offers several benefits:

* **General Features**: The image encoder, often trained on large datasets like ImageNet, has learned to extract universal visual features that are widely applicable.
    
* **Pre-Trained Integration**: The multi-modal projector is already designed to align image and text features effectively, minimizing the need for additional fine-tuning.
    
* **Resource Efficiency**: By reducing the number of trainable parameters, freezing these components speeds up training and lowers computational demands, making the process more efficient.
    

This strategy allows the model to leverage pre-trained strengths while focusing training resources on task-specific components.

---

## Why Fine-Tune the Decoder?

**Task Specificity:** The decoder must be fine-tuned for the specific task. Fine-tuning allows it to learn how to generate the appropriate output based on the particular types of input it will receive in your application.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Define a <code>collate_fn</code> function. The function returns the final batch of tokens containing the tokenized text, images, and labels, all converted to the appropriate format and moved to the right device for efficient computation.</div>
</div>

```python
import torch
device = "cuda"

image_token = processor.tokenizer.convert_tokens_to_ids("<image>")
def collate_fn(examples):
  texts = ["Answer the following Question: " + example["query"] for example in examples]
  labels= [example['label'][0] for example in examples]
  images = [example["image"].convert("RGB") for example in examples]
  tokens = processor(text=texts, images=images, suffix=labels,
                    return_tensors="pt", padding="longest",
                    tokenize_newline_separately=False)

  tokens = tokens.to(torch.bfloat16).to(device)
  return tokens
```

### Defining the Trainer

Hugging Face makes it really easy to finetune models, either through their GUI based AutoTrain as well as their Trainer Module.

```python
from transformers import TrainingArguments
args = TrainingArguments(
    num_train_epochs=2,
    remove_unused_columns=False,
    per_device_train_batch_size=2,
    gradient_accumulation_steps=4,
    warmup_steps=1,
    learning_rate=2e-5,
    weight_decay=1e-6,
    adam_beta2=0.999,
    logging_steps=100,
    optim="adamw_hf",
    save_strategy="epoch",
    save_steps=5000,
    push_to_hub=True,
    save_total_limit=1,
    output_dir="paligemma2-3b-pt-224_HuggingFaceM4_ChartQA",
    bf16=True,
    report_to=["tensorboard"],
    dataloader_pin_memory=False,
    gradient_checkpointing=True,
    dataloader_drop_last=True,
)
```

```python
from transformers import Trainer

trainer = Trainer(
        model=model,
        train_dataset=train_ds ,
        eval_dataset = test_ds,
        data_collator=collate_fn,
        args=args
        )
```

```python
trainer.train()
```

### And that’s it

Your model should be training now, Give it an hour or so and you’ll be ready with your very own finetuned version of PaliGemma 2.

You can Infer from the model using the code below:

```python
from transformers import AutoProcessor, PaliGemmaForConditionalGeneration

model_id = "YourUserID/paligemma2-3b-pt-224_HuggingFaceM4_ChartQA"
model = PaliGemmaForConditionalGeneration.from_pretrained(model_id)
processor = AutoProcessor.from_pretrained("google/paligemma2-3b-pt-224")
```

```python
from PIL import Image
import requests


prompt = "Question"
image_file = "Link to Image"
raw_image = Image.open(requests.get(image_file, stream=True).raw)
```

```python
inputs = processor(prompt, raw_image.convert("RGB"), return_tensors="pt")
output = model.generate(**inputs, max_new_tokens=20)

print(processor.decode(output[0], skip_special_tokens=True)[len(prompt):])
```

# Conclusion

Fine-tuning PaliGemma 2 marks a significant step in leveraging advanced vision-language models for specialized tasks. By customizing the model to your specific dataset, you enhance its ability to perform with greater accuracy and relevance in applications like image captioning and visual question answering. Freezing the image encoder while training the decoder efficiently utilizes computational resources, allowing the model to focus on generating precise textual outputs. Setting up the appropriate environmental resources, such as using a GPU with sufficient memory, ensures a smoother fine-tuning process. As you finalize your model, you're not just adapting a powerful tool to your needs—you're expanding the possibilities of multimodal AI in your field. Embrace this opportunity to push the boundaries and see how fine-tuned models can revolutionize your projects.
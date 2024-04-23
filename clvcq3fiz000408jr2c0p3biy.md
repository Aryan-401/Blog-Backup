---
title: "An honest Guide to Optimize LLMs for upto 10x Inference"
datePublished: Tue Apr 23 2024 18:32:02 GMT+0000 (Coordinated Universal Time)
cuid: clvcq3fiz000408jr2c0p3biy
slug: an-honest-guide-to-optimize-llms-for-upto-10x-inference
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713896656614/5a565c29-ab49-4478-b70c-fc1c4307d36a.png
tags: optimization, ai, python, nlp, huggingface, llm

---

# Introduction

The AI revolution has officially gone mainstream. From crafting the perfect 'Good Morning' message with Chat-GPT to generating human-like responses, Large Language Models (LLMs) have taken the world by storm. But behind the scenes, these behemoths of AI require staggering amounts of compute power and energy to train. The latest example is Llama3, Meta AI's massive model trained on two super clusters of 24,000+ Nvidia H100 GPUs each. As the scale of these models continues to grow, so do the costs of building and maintaining them. In fact, [some](https://www.scientificamerican.com/article/the-ai-boom-could-use-a-shocking-amount-of-electricity/) projections suggest that the compute and electrical power needed to train such models could soon surpass the requirements of small countries.

## Inference Time Optimizations.

In this landscape, optimizing inference time has become crucial. While model parameter count gets most of the attention, inference time - the time it takes for a model to make a prediction from a given input - is a critical metric that can make or break the usability of an AI system. In the context of language models, inference time is often measured in tokens per second (tk/s). Reducing inference time can significantly lower operational costs, making AI more accessible and sustainable in the future.

In the below Image, the training time would be the time to train the Neural network to identify images of cats, and inference time would be the time it takes for the pre-trained neural network to return a confidence value, if a cat is in the image.

![](https://backblazeprod.wpenginepowered.com/wp-content/uploads/2023/11/bb-bh-Training-vs-Inference_Final-1536x875.png align="center")

In this discussion, we'll delve into the world of inference time optimizations, exploring techniques and strategies to speed up your PyTorch models without sacrificing the final output.

### Quantization using PyTorch

Quantization is a technique used to reduce the precision of model weights from floating-point numbers to integers. This process, also known as weight quantization, aims to decrease the memory footprint and computational requirements of LLMs, making them more efficient and deployable on resource-constrained devices. By representing model parameters with fewer bits, quantization can lead to significant reductions in model size, inference time, and energy consumption, while maintaining acceptable accuracy. However, quantization can also introduce accuracy degradation, and careful tuning of quantization parameters is necessary to balance the trade-off between model efficiency and accuracy.

![](https://www.allaboutcircuits.com/uploads/articles/qc-tech_quantization_gif-2_final.jpg align="center")

Let's code out a simple example using `facebook/mbart-large-50-many-to-many-mmt` model. This model developed by Facebook can easily Translate to 50 languages from any of its supported base languages. It has over 611 Million parameters. To magnify the efforts of each of the following optimizations, we will be running them on the CPU, but also sharing statistics of their GPU counterparts.

We can easily initiate the model by using the HuggingFace Transformers Library.

```python
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM

tokenizer = AutoTokenizer.from_pretrained("facebook/mbart-large-50-many-to-many-mmt")
model = AutoModelForSeq2SeqLM.from_pretrained("facebook/mbart-large-50-many-to-many-mmt")
```

As well as other imports we may need from PyTorch

```python
import torch
import torch.quantization
```

We have a choice on how much we want to quantize the model. This can range from float32(basically no change since models are usually stored in Float32) to int1 (which is not available on PyTorch Quantization, but was talked about extensively in [this](https://arxiv.org/abs/2402.17764) paper by Microsoft Research). The options available on Pyorch are:

* `torch.quint8`
    
* `torch.qint8`
    
* `torch.qint32`
    
* `torch.float16`
    

Out of these, `torch.float16` would perform the 'worst' while `torch.quint8` will anecdotally perform the best. Let us translate from Chinese to English with a relatively complex phrase picked up from [Byjus](https://byjus.com/english/paragraph-on-india/).

```python
article_ch = '印度是一片美麗的土地，擁有多種野生動物和豐富的文化多樣性。孟加拉虎被認為是印度的國獸。印度每年 8 月 15 日慶祝獨立紀念日。人們慶祝這個節日是為了紀念印度從英國統治下獲得自由。三色國旗稱為“Tiranga”，由藏紅花、白色和綠色設計，國旗中央為海軍藍色的阿肖克脈輪。 「阿育王獅都」是該國的國徽。國家座右銘是 "Satyameva Jayate"，意思是只有真理才能獲勝。 為了順利管理國家，並使其成為一個獨立的國家，需要一部於1950年1月26日生效的憲法。 印度是一個擁有多種不同語言和多種宗教的國家，如佛教、耆那教、伊斯蘭教、印度教等。 。'
```

After running the quantized and non-optimized model we see the following differences in Translation with their approximate time of execution below.

```python
quantized_model = torch.quantization.quantize_dynamic(
    model, dtype=torch.qint8
)
tokenizer.src_lang = "zh_CN"

encoded_ch = tokenizer(article_ch, return_tensors="pt")

generated_tokens = quantized_model.generate(
    **encoded_ch,
    forced_bos_token_id=tokenizer.lang_code_to_id["en_XX"]
)
tokenizer.batch_decode(generated_tokens, skip_special_tokens=True)
```

**OUTPUT:**

India is a beautiful land with a wide variety of wildlife and rich cultural diversity. The Bengal tiger is considered to be India's national beast. India celebrates Independence Day on August 15, every year. It is celebrated to commemorate India's liberation from British rule. The three-coloured flag is called "Tiranga", designed with Tibetan red flowers, white and green, and the flag is centered on the Navy's blue Ashok Ring. The lion is the national emblem of the country. The right wing of the flag is "Satyameva Jayate", meaning that only truth can prevail. In order to successfully govern the country and make it an independent country, it is necessary to have a constitution in force on January 26, 1950. India is a country with a wide variety of languages and religions, such as Buddhism, Jainism, Islam, Hinduism,

Inference Time: 26.802 sec

```plaintext
encoded_ch = tokenizer(article_ch, return_tensors="pt")
generated_tokens = model.generate(
    **encoded_ch,
    forced_bos_token_id=tokenizer.lang_code_to_id["en_XX"]
)
tokenizer.batch_decode(generated_tokens, skip_special_tokens=True)
```

**OUTPUT:**

India is a beautiful land with a wide variety of wildlife and rich cultural diversity. The Bengal tiger is considered to be India's national animal. India celebrates Independence Day on August 15 every year. It is celebrated to commemorate India's liberation from British rule. The three-coloured flag is called "Tiranga", designed with Tibetan red flowers, white and green, with the flag centered on the Navy's blue Ashok Ring. "Ayurveda Lion" is the national emblem of the country. The country's right-hand inscription is "Satyameva Jayate", meaning that only truth can prevail. In order to successfully govern the country and make it an independent country, it is necessary to have a constitution that entered into force on January 26, 1950. India is a country with a wide variety of languages and religions, such as Buddhism, Jainism,

Inference Time: 99.025

We can see with a simple 2 lines of additional code, we have generated an improvement of 3.69 (nice!) with little loss to the end result. The final output of both LLMs are not identical to the initial input string, but we can chalk that up to Google Translate not being the best at what it does.

## Optimum by Hugging Face

Optimum is an open-source library developed by Hugging Face. It leverages various optimization techniques, such as quantization, pruning, and knowledge distillation. Optimum enables developers to reduce the computational requirements and memory usage of their models, making them more efficient and deployable on resource-constrained devices. Since its release, Optimum has gained immense popularity within the machine learning community, with thousands of stars on GitHub and widespread adoption in industries such as computer vision, natural language processing, and autonomous driving. Its popularity can be attributed to its ease of use, flexibility, and the significant performance improvements it offers, making it an essential tool for anyone looking to deploy AI models in real-world applications. By providing a simple and standardized way to optimize models, Optimum has enabled developers to focus on building innovative applications rather than worrying about the underlying infrastructure associated with machine learning tasks.

A key part of using Optimum would be converting the model to ONNX(Open Neural Network Exchange). ONNX is an open format used to represent deep learning models, allowing them to be exchanged and executed across different frameworks and platforms. Developed by Microsoft, Amazon, and Facebook, ONNX provides a common language for AI models, enabling seamless interoperability between various deep learning frameworks, such as TensorFlow, PyTorch etc. This open standard enables developers to train models in one framework and deploy them in another, without the need for retraining or rewriting the model.

Out of the gate, Optimum allows us to either pragmatically use its interface or navigate through via its CLI. We will be using the CLI in this example. We will be attempting to reduce the inference time on a well known summarization model, i.e. `t5-small` developed by Google AI.

Start by downloading the required libraries

```bash
pip install optimum[onnxruntime-gpu]
pip install optimum[onnxruntime]
```

Now using the `optimum-cli` we can optimize the model on 4 levels:

* O1 basic general optimizations
    
* O2 basic and extended general optimizations, transformers-specific fusions
    
* O3 same as O2 with GELU approximation
    
* O4 same as O3 with mixed precision (fp16, GPU-only)
    

```bash
optimum-cli export onnx --model t5-small --optimize O3 t5_onnx/ --device cuda
```

```python
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM, pipeline
from optimum.onnxruntime import ORTModelForSeq2SeqLM
import torch

tokenizer = AutoTokenizer.from_pretrained("t5-small")
model = AutoModelForSeq2SeqLM.from_pretrained('t5-small')
onnx_model = ORTModelForSeq2SeqLM.from_pretrained("t5_onnx")

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
_ = model.to(device).eval()
```

```python
import random
import time

sentences = [
    "In recent years, advancements in artificial intelligence (AI) have revolutionized various industries, from healthcare to finance and beyond. AI technologies such as machine learning and natural language processing have enabled computers to perform tasks that were once thought to be exclusive to human intelligence. For instance, AI-powered systems can now diagnose diseases, predict stock market trends, and even generate creative content like music and art. These developments have sparked both excitement and concern among experts and the general public. While AI offers immense potential for improving efficiency and solving complex problems, there are also fears about its impact on jobs, privacy, and ethical considerations surrounding its use.",
    "The rise of renewable energy sources, such as solar and wind power, has gained significant momentum in recent years as the world seeks to address climate change and reduce reliance on fossil fuels. Governments, businesses, and individuals are increasingly investing in renewable energy infrastructure and technologies to transition towards a more sustainable energy system. Solar photovoltaic (PV) panels and wind turbines have become common sights in many parts of the world, harnessing the power of sunlight and wind to generate electricity. This shift towards renewable energy is not only driven by environmental concerns but also by economic factors, as the cost of renewable energy technologies continues to decline, making them increasingly competitive with traditional energy sources.",
    "The internet has transformed the way we communicate, access information, and conduct business on a global scale. With the proliferation of smartphones and high-speed internet connections, people are more connected than ever before, allowing for instant communication and collaboration across geographical boundaries. Social media platforms have become central hubs for sharing ideas, connecting with friends and family, and consuming news and entertainment. E-commerce has also experienced exponential growth, with online shopping becoming a convenient and preferred method for many consumers. However, along with the benefits of connectivity come challenges such as cybersecurity threats, online privacy concerns, and the spread of misinformation. As the internet continues to evolve, it remains crucial for individuals, businesses, and policymakers to address these issues while harnessing the full potential of digital technology.",
]

len_dataset = 1

texts = []
for _ in range(len_dataset):
    n_times = random.randint(1, 5)
    texts.append(" ".join(random.choice(sentences) for _ in range(n_times)))
```

```python
summarization = pipeline("summarization", model=model, tokenizer=tokenizer, max_length= 100)
start = time.time()
print(summarization(texts))
end = time.time()
print(f"Average response time for original T5: {(end-start)/len_dataset} ms")
```

**OUTPUT:**

'in recent years, advancements in artificial intelligence (AI) have revolutionized various industries, from healthcare to finance and beyond . AI-powered systems can now diagnose diseases, predict stock market trends, and even generate creative content like music and art .'

Inference Time: 3.17 s

```python
onnx_summarization = pipeline("summarization", model=onnx_model, tokenizer=tokenizer, max_length=100)
start = time.time()
print(onnx_summarization(texts))
end = time.time()
print(f"Average response time for optimized onnx T5: {(end-start)/len_dataset} ms")
```

'in recent years, advancements in artificial intelligence (AI) have revolutionized various industries, from healthcare to finance and beyond . AI-powered systems can now diagnose diseases, predict stock market trends, and even generate creative content like music and art .'

Inference Time: 1.17 s

The Same inference at 2.71 times the speed. Imaging what would happen if we Quantize the model too. That might be a bit out of scope for this article, but we could have a theoretical speedup of 9.99. Almost 10x of the base speed. Although both models we used were inherently different and this may not be a fair method of judging the speed of the LLM.

## Static vs Dynamic Quantization

As discussed previously, quantization is a process in machine learning and deep learning that reduces the precision of a model's weights and activations from floating-point numbers to integers. This is done to reduce the memory footprint and computational requirements of the model, making it more efficient and suitable for deployment on resource-constrained devices.

There are two types of quantization: static quantization and dynamic quantization:

### Static Quantization

In static quantization, the quantization parameters (such as the scale and zero-point) are determined during the training process or during a separate calibration step. The model is then quantized using these fixed parameters, and the resulting quantized model is used for inference.

* **Faster inference**: Since the quantization parameters are fixed, the inference process is faster and more efficient.
    
* **Lower memory usage**: The quantized model requires less memory, making it suitable for deployment on devices with limited memory.
    

### Dynamic Quantization

In dynamic quantization, the quantization parameters are determined dynamically during inference, based on the input data. This means that the model adapts to the input distribution and adjusts the quantization parameters accordingly.

* **Improved accuracy**: Dynamic quantization can adapt to changing input distributions, leading to improved accuracy and reduced accuracy loss.
    

* **Flexibility**: Dynamic quantization can be used on different hardware platforms and with different input distributions, without requiring retraining or recalibration.
    

### Pre Training Quantization

Pre-training quantization, also known as quantization-aware training, involves quantizing the model's weights and activations during the training process. This means that the model is trained using quantized values, rather than full-precision floating-point numbers.

**Advantages:**

1. **Improved accuracy**: Pre-training quantization can lead to improved accuracy, as the model is trained to adapt to the quantization noise and errors.
    
2. **Better optimization**: The model is optimized for the quantized precision, which can lead to better convergence and optimization.
    
3. **Faster deployment**: Since the model is already quantized, it can be deployed directly on hardware that supports quantized inference, without the need for additional quantization steps.
    

**Challenges:**

1. **Training complexity**: Pre-training quantization can increase the training complexity, as the model needs to adapt to the quantization noise and errors.
    
2. **Hyperparameter tuning**: Hyperparameter tuning can be more challenging, as the optimal hyperparameters may vary depending on the quantization precision.
    

### Post Training Quantization

Post-training quantization, also known as quantization after training, involves quantizing a pre-trained model's weights and activations after the training process is complete. This is a more common approach, as it allows for the use of pre-trained models and fine-tuning them for specific hardware platforms.

**Advantages:**

1. **Flexibility**: Post-training quantization allows for the use of pre-trained models, which can be fine-tuned for specific hardware platforms.
    
2. **Simpler deployment**: Post-training quantization is a simpler process, as it only requires quantizing the pre-trained model's weights and activations.
    
3. **Wider applicability**: Post-training quantization can be applied to a wide range of models and hardware platforms.
    

**Challenges:**

1. **Accuracy loss**: Post-training quantization can result in accuracy loss, as the model is not optimized for the quantized precision.
    
2. **Calibration required**: Post-training quantization often requires calibration to determine the optimal quantization parameters, which can be time-consuming.
    

In general, pre-training quantization can lead to improved accuracy, with models like MobileNetV2 achieving an accuracy of 72.0% on the ImageNet benchmark, while reducing the model size by 75%. On the other hand, post-training quantization can offer significant space savings, with models like ResNet-50 requiring only 7.5MB of storage space, a reduction of 90% compared to the full-precision model. With the post-training quantized model achieving an accuracy of 69.5% on the same benchmark. Despite this, post-training quantization can still be a viable option for many applications, especially those where memory constraints are a major concern.

## Conclusion

In conclusion, optimizing inference time is crucial for making AI systems more accessible and sustainable. We explored techniques to speed up PyTorch models without sacrificing accuracy, including quantization, Optimum, and static vs dynamic quantization, demonstrating significant reductions in model size, inference time, and energy consumption. As AI continues to evolve, optimizing inference time will become increasingly important, and by leveraging these techniques, developers can build more efficient and deployable AI models, making AI more accessible and sustainable for a wider range of applications.
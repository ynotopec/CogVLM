# CogVLM

📖 [Paper（论文）](./assets/cogvlm-paper.pdf)

🌐 [web demo（测试网址）](http://36.103.203.44:7861/)

🔥 **News**: ```2023/12/8``` cogvlm-grounding-generalist 更新 v1.1，该版本在训练时加入图像数据增强，表现更加鲁棒。[点击](#简介)查看更多细节。

🔥 **News**: ```2023/12/7``` CogVLM 支持4-bit量化了！推理只需要占用 **11GB** 显存！[点击](#CLI)查看更多细节。

🔥 **News**: ```2023/11/20``` cogvlm-chat 更新 v1.1 版本，该版本同时支持对话和问答，在多项数据集刷新 SOTA 效果。[点击](#简介)查看更多细节。

🔥 **News**: ```2023/10/27``` CogVLM 中英双语版正式[上线](https://chatglm.cn/)了！欢迎体验！

🔥 **News**: ```2023/11/20``` CogVLM 的 🤗huggingface 版已开源！包括[**cogvlm-chat**](https://huggingface.co/THUDM/cogvlm-chat-hf), **[cogvlm-grounding-generalist](https://huggingface.co/THUDM/cogvlm-grounding-generalist-hf)/[base](https://huggingface.co/THUDM/cogvlm-grounding-base-hf)**, **[cogvlm-base-490](https://huggingface.co/THUDM/cogvlm-base-490-hf)/[224](https://huggingface.co/THUDM/cogvlm-base-224-hf)**. 仅使用几行代码即可进行推理，具体使用方法请参考[这里](#-transformers)。

[README in English](./README.md)

## 简介
- CogVLM 是一个强大的开源视觉语言模型（VLM）。CogVLM-17B 拥有 100 亿视觉参数和 70 亿语言参数。

- CogVLM-17B 在 10 个经典跨模态基准测试上取得了 SOTA 性能，包括 NoCaps、Flicker30k captioning、RefCOCO、RefCOCO+、RefCOCOg、Visual7W、GQA、ScienceQA、VizWiz VQA 和 TDIUC，而在 VQAv2、OKVQA、TextVQA、COCO captioning 等方面则排名第二，超越或与 PaLI-X 55B 持平。您可以通过线上 [demo](http://36.103.203.44:7861) 体验 CogVLM 多模态对话。

<div align="center">
    <img src=assets/metrics-min.png width=80% />
</div>

cogvlm-chat-v1.1:

| Method           | LLM           | MM-VET | POPE(adversarial) | TouchStone |
| ---------------- | ------------- |--------| --------- |------------|
| BLIP-2           | Vicuna-13B    | 22.4   | -         | -          |
| Otter            | MPT-7B        | 24.7   | -         | -          |
| MiniGPT4         | Vicuna-13B    | 24.4   | 70.4      | 531.7      |
| InstructBLIP     | Vicuna-13B    | 25.6   | 77.3      | 552.4      |
| LLaMA-Adapter v2 | LLaMA-7B      | 31.4   | -         | 590.1      |
| LLaVA            | LLaMA2-7B     | 28.1   | 66.3      | 602.7      |
| mPLUG-Owl        | LLaMA-7B      | -      | 66.8      | 605.4      |
| LLaVA-1.5        | Vicuna-13B    | 36.3   | 84.5      | -          |
| Emu              | LLaMA-13B     | 36.3   | -         | -          |
| Qwen-VL-Chat     | -             | -      | -         | 645.2      |
| DreamLLM         | Vicuna-7B     | 35.9   | 76.5      | -          |
| CogVLM           | Vicuna-7B     | **52.8**   | **87.6**      | **742.0**      |

cogvlm-grounding-generalist-v1.1:

| | RefCOCO | | | RefCOCO+ | | | RefCOCOg | | Visual7W |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| | val | testA | testB | val | testA | testB | val | test | test |
| cogvim-grounding-generalist | 92.51 | 93.95 | 88.73 | 87.52 | 91.81 | 81.43 | 89.46 | 90.09 | 90.96 |
| cogvim-grounding-generalist-v1.1 | **92.76** | **94.75** | **88.99** | **88.68** | **92.91** | **83.39** | **89.75** | **90.79** | **91.05** |

## 示例

<!-- CogVLM is powerful for answering various types of visual questions, including **Detailed Description & Visual Question Answering**,  **Complex Counting**, **Visual Math Problem Solving**, **OCR-Free Reasonging**, **OCR-Free Visual Question Answering**, **World Knowledge**, **Referring Expression Comprehension**, **Programming with Visual Input**, **Grounding with Caption**, **Grounding Visual Question Answering**, etc. -->
* CogVLM 能够准确地描述图像，**几乎不会出现幻觉**。
    <details>
    <summary>点击查看与 LLAVA-1.5 和 MiniGPT-4 的比较。</summary>

    ![LLAVA Comparision](assets/llava-comparison-min.png)
    </details>
    <br>

* CogVLM 能理解和回答各种类型的问题，并有一个**视觉定位**版本。
<div align="center">
    <img src=assets/pear_grounding.png width=90% />
</div>

<br>

* CogVLM 有时比 GPT-4V(ision) 提取到更多的细节信息。
<div align="center">
    <img src=assets/compare-min.png width=90% />
</div>

<!-- ![compare](assets/compare.png) -->
<br> 

<details>
<summary>点击展开更多示例。</summary>

![Chat Examples](assets/chat.png)

</details>

## 方法
CogVLM 模型包括四个基本组件：视觉变换器（ViT）编码器、MLP适配器、预训练的大型语言模型（GPT）和一个**视觉专家模块**。更多细节请参见[论文](./assets/cogvlm-paper.pdf)。

<div align="center">
    <img src=assets/method-min.png width=70% />
</div>

## 入门指南
我们提供两种图形用户界面（GUI）进行模型推断，分别是**网页演示**和**命令行界面（CLI）**。如果您想在Python代码中使用它，很容易修改CLI脚本以适应您的情况。

首先，需要安装依赖项。

```bash
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

#### 硬件要求
* 模型推断：1 * A100(80G) 或 2 * RTX 3090(24G)。
* 微调：4 * A100(80G) [推荐] 或 8 * RTX 3090(24G)。

<!-- ### Online Web Demo
We provide a [web demo](http://36.103.203.44:7861/) based on [Gradio](https://gradio.app). -->

### 网页演示
我们还提供基于Gradio的本地网页演示。首先，通过运行 pip install gradio 安装Gradio。然后下载并进入此仓库，运行 web_demo.py。具体使用方式如下：

```bash
python web_demo.py --from_pretrained cogvlm-chat --version chat --english --bf16
python web_demo.py --from_pretrained cogvlm-grounding-generalist --version base --english --bf16
```
网页演示的 GUI 界面如下：
<div align="center">
    <img src=assets/web_demo-min.png width=70% />
</div>

### CLI
我们开源了不同下游任务的模型权重：

* cogvlm-chat 用于对齐的模型，在此之后支持像 GPT-4V 一样的聊天。
* cogvlm-base-224 文本-图像预训练后的原始权重。
* cogvlm-base-490 从 cogvlm-base-224 微调得到的 490px 分辨率版本。
* cogvlm-grounding-generalist 这个权重支持不同的视觉定位任务，例如 REC、Grounding Captioning 等。

通过CLI演示，执行以下命令：
```bash
python cli_demo.py --from_pretrained cogvlm-base-224 --version base --english --bf16 --no_prompt
python cli_demo.py --from_pretrained cogvlm-base-490 --version base --english --bf16 --no_prompt
python cli_demo.py --from_pretrained cogvlm-chat --version chat --english --bf16
python cli_demo.py --from_pretrained cogvlm-grounding-generalist --version base --english --bf16
```
该程序会自动下载 sat 模型并在命令行中进行交互。您可以通过输入指令并按 Enter 生成回复。
输入 clear 可清除对话历史，输入 stop 可停止程序。

我们的模型支持4-bit量化，使用方法如下。

SAT版本：

```bash
python cli_demo.py --from_pretrained cogvlm-chat-v1.1 --version chat --fp16 --quant 4 --english --stream_chat
```

huggingface版本：

```python
tokenizer = LlamaTokenizer.from_pretrained('vicuna-7b-v1.5')
    model = AutoModelForCausalLM.from_pretrained(
        'THUDM/cogvlm-chat-hf',
        load_in_4bit=True,
        trust_remote_code=True,
    ).eval()
query = 'Describe this image in details.'
image = Image.open('image-path').convert('RGB')
inputs = model.build_conversation_input_ids(tokenizer, query=query, history=[], images=[image])  # chat mode
inputs = {
    'input_ids': inputs['input_ids'].unsqueeze(0).to('cuda'),
    'token_type_ids': inputs['token_type_ids'].unsqueeze(0).to('cuda'),
    'attention_mask': inputs['attention_mask'].unsqueeze(0).to('cuda'),
    'images': [[inputs['images'][0].to('cuda').to(torch.float16)]],
}
gen_kwargs = {"max_length": 2048, "do_sample": False}

with torch.no_grad():
    outputs = model.generate(**inputs, **gen_kwargs)
    outputs = outputs[:, inputs['input_ids'].shape[1]:]
    print(tokenizer.decode(outputs[0]))
```

### 🤗 Transformers

使用Transformers对CogVLM进行推理，只需要如下几行代码：

```python
import torch
import requests
from PIL import Image
from transformers import AutoModelForCausalLM, LlamaTokenizer

tokenizer = LlamaTokenizer.from_pretrained('lmsys/vicuna-7b-v1.5')
model = AutoModelForCausalLM.from_pretrained(
    'THUDM/cogvlm-chat-hf',
    torch_dtype=torch.bfloat16,
    low_cpu_mem_usage=True,
    trust_remote_code=True
).to('cuda').eval()


# chat example

query = 'Describe this image'
image = Image.open(requests.get('https://github.com/THUDM/CogVLM/blob/main/examples/1.png?raw=true', stream=True).raw).convert('RGB')
inputs = model.build_conversation_input_ids(tokenizer, query=query, history=[], images=[image])  # chat mode
inputs = {
    'input_ids': inputs['input_ids'].unsqueeze(0).to('cuda'),
    'token_type_ids': inputs['token_type_ids'].unsqueeze(0).to('cuda'),
    'attention_mask': inputs['attention_mask'].unsqueeze(0).to('cuda'),
    'images': [[inputs['images'][0].to('cuda').to(torch.bfloat16)]],
}
gen_kwargs = {"max_length": 2048, "do_sample": False}

with torch.no_grad():
    outputs = model.generate(**inputs, **gen_kwargs)
    outputs = outputs[:, inputs['input_ids'].shape[1]:]
    print(tokenizer.decode(outputs[0]))

# This image captures a moment from a basketball game. Two players are prominently featured: one wearing a yellow jersey with the number
# 24 and the word 'Lakers' written on it, and the other wearing a navy blue jersey with the word 'Washington' and the number 34. The player
# in yellow is holding a basketball and appears to be dribbling it, while the player in navy blue is reaching out with his arm, possibly
# trying to block or defend. The background shows a filled stadium with spectators, indicating that this is a professional game.</s>

# vqa example

query = 'How many houses are there in this cartoon?'
image = Image.open(requests.get('https://github.com/THUDM/CogVLM/blob/main/examples/3.jpg?raw=true', stream=True).raw).convert('RGB')
inputs = model.build_conversation_input_ids(tokenizer, query=query, history=[], images=[image], template_version='vqa')   # vqa mode
inputs = {
    'input_ids': inputs['input_ids'].unsqueeze(0).to('cuda'),
    'token_type_ids': inputs['token_type_ids'].unsqueeze(0).to('cuda'),
    'attention_mask': inputs['attention_mask'].unsqueeze(0).to('cuda'),
    'images': [[inputs['images'][0].to('cuda').to(torch.bfloat16)]],
}
gen_kwargs = {"max_length": 2048, "do_sample": False}

with torch.no_grad():
    outputs = model.generate(**inputs, **gen_kwargs)
    outputs = outputs[:, inputs['input_ids'].shape[1]:]
    print(tokenizer.decode(outputs[0]))

# 4</s>
```

### OpenAI Vision formate

我们提供了与 `GPT-4V` 相同的API示例，你可以在 `openai_demo`中查看。
1. 首先，启动节点
```
python openai_demo/openai_api.py
```
2. 接着，运行请求示例节点，这是一个连续对话例子
```
python openai_demo/openai_api_request.py
```
3. 你将能获得类似如下的输出
```
This image showcases a tranquil natural scene with a wooden pathway leading through a field of lush green grass. In the distance, there are trees and some scattered structures, possibly houses or small buildings. The sky is clear with a few scattered clouds, suggesting a bright and sunny day.
```

## 许可

此存储库中的代码是根据 [Apache-2.0 许可](./LICENSE) 开放源码，而使用 CogVLM 模型权重必须遵循 [模型许可](./MODEL_LICENSE)。

## 引用 & 鸣谢

如果您觉得我们的工作有帮助，请考虑引用以下论文：
```

```
在 CogVLM 的指令微调阶段，我们使用了来自 [MiniGPT-4](https://github.com/Vision-CAIR/MiniGPT-4) 、 [LLAVA](https://github.com/haotian-liu/LLaVA) 、 [LRV-Instruction](https://github.com/FuxiaoLiu/LRV-Instruction)、 [LLaVAR](https://github.com/SALT-NLP/LLaVAR) 和 [Shikra](https://github.com/shikras/shikra) 项目的一些英文图像-文本数据，以及许多经典的跨模态工作数据集。我们衷心感谢他们的贡献。

---
language:
- zh
- en
tags:
- glm
- chatglm
- thudm
---
# ChatGLM-6B
## 介绍
ChatGLM-6B 是一个开源的、支持中英双语问答和对话的预训练语言模型，基于 [General Language Model (GLM)](https://github.com/THUDM/GLM) 架构，具有 62 亿参数。ChatGLM-6B 使用了和 [ChatGLM]((https://chatglm.cn)) 相同的技术面向中文问答和对话进行优化。结合模型量化技术，用户可以在消费级的显卡上进行本地部署（INT4 量化级别下最低只需 6GB 显存）。在经过了约 1T 标识符的中英双语训练，并辅以监督微调、反馈自助、人类反馈强化学习等技术的加持，62 亿参数的模型已经能生成相当符合人类偏好的回答。

## 硬件需求

| **量化等级**    | **最低 GPU 显存** |
| -------------- | ----------------- |
| FP16（无量化）   | 19 GB             |
| INT8           | 10 GB              |
| INT4           | 6 GB               |

## 软件依赖

```shell
pip install "transformers>=4.23.1,icetk,cpm_kernels"
```

## 代码调用 

可以通过如下代码调用 ChatGLM-6B 模型来生成对话。

```python
from transformers import AutoTokenizer, AutoModel

tokenizer = AutoTokenizer.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True)
model = AutoModel.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True).half().cuda()
model = model.eval()

history = []
query = "你好"
response, history = model.chat(tokenizer, query, history=history)
print(response)

query = "晚上睡不着应该怎么办"
response, history = model.chat(tokenizer, query, history=history)
print(history)
```

关于更多的使用说明，以及如何运行命令行和网页版本的 DEMO，请参考我们的 [Github repo](https://github.com/THUDM/ChatGLM-6B)。

## 模型量化

默认情况下，模型以 FP16 精度加载，运行上述代码需要大概 19GB 显存。如果你的 GPU 显存有限，可以尝试运行量化后的模型，即将下述代码

```python
model = AutoModel.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True).half().cuda()
```

替换为（8-bit 量化）
```python
model = AutoModel.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True).half().quantize(8).cuda()
```

或者（4-bit 量化）
```python
model = AutoModel.from_pretrained("THUDM/chatglm-6b", trust_remote_code=True).half().quantize(4).cuda()
```

进行 2 至 3 轮对话后，8-bit 量化下约占用 10GB 的 GPU 显存，4-bit 量化仅需占用 6GB 的 GPU 显存。随着对话轮数的增多，对应消耗显存也随之增长。

## 引用

如果你觉得我们的工作有帮助的话，请考虑引用下列论文

```
@inproceedings{
  zeng2023glm-130b,
  title={{GLM}-130B: An Open Bilingual Pre-trained Model},
  author={Aohan Zeng and Xiao Liu and Zhengxiao Du and Zihan Wang and Hanyu Lai and Ming Ding and Zhuoyi Yang and Yifan Xu and Wendi Zheng and Xiao Xia and Weng Lam Tam and Zixuan Ma and Yufei Xue and Jidong Zhai and Wenguang Chen and Zhiyuan Liu and Peng Zhang and Yuxiao Dong and Jie Tang},
  booktitle={The Eleventh International Conference on Learning Representations (ICLR)},
  year={2023},
  url={https://openreview.net/forum?id=-Aw0rrrPUF}
}
```
```
@inproceedings{du2022glm,
  title={GLM: General Language Model Pretraining with Autoregressive Blank Infilling},
  author={Du, Zhengxiao and Qian, Yujie and Liu, Xiao and Ding, Ming and Qiu, Jiezhong and Yang, Zhilin and Tang, Jie},
  booktitle={Proceedings of the 60th Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers)},
  pages={320--335},
  year={2022}
}
```
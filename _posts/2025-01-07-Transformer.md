---
title: Transformer
author: lonelywatch
date: 2025-1-7 1:28 +0800
categories: [ML]
tags: [AIGC,ML,NLP]
usemath: latex
---

内容来自[Attention is all you need](https://arxiv.org/abs/1706.03762),该论文提出了自注意力机制与多头自注意力机制，所提出的Transformer架构在此基础上完全摒弃了以往的循环结构，而是完全依靠注意力机制，极大提高了训练效率和上下文捕捉能力。

## Self Attention

[注意力机制](https://arxiv.org/abs/1409.0473)最早于2014年提出,用于解决传统的seq2seq模型（2个RNN将变长输入变为固定上下文向量，再生成目标序列，导致过长输入会损失很多信息）不能较好地处理长文本序列地问题。

注意力机制取名自人类的注意力机制，即在处理问题时，人类会根据问题的不同部分分配不同的注意力，具体来说，注意力机制会学习一个可变的权重分布，用来表示输入中哪些部分对解码词最"重要"。





![](https://lonelywatch-1306651324.cos.ap-beijing.myqcloud.com/image-20250114212334510.png)

## Multi-Head Self Attention


## Transformer


![](https://lonelywatch-1306651324.cos.ap-beijing.myqcloud.com/image-20250114212252405.png)
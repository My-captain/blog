
## Quantization

#### Attention中的outliers现象的原因
> https://www.qualcomm.com/content/dam/qcomm-martech/dm-assets/documents/whats-next-in-on-device-generative-ai.pdf
> https://arxiv.org/pdf/2306.12929[Quantizable Transformers: Removing Outliers by Helping Attention Heads Do Nothing]

高通的论文认为：
- Transformer 中强烈的激活值异常值，与注意力头试图学习一种“无操作”（no-op）或部分更新（partial update）的行为密切相关。
- “无操作”的必要性：在残差网络中，当一个注意力头认为当前信息对更新某个词元的表示没有帮助时，它最好的选择就是“什么都不做”，即不对残差连接产生贡献。
- Softmax的“笨拙”实现：为了通过 Softmax 函数实现“无更新”，注意力头必须将注意力概率高度集中在像 [SEP] 标记、句号、逗号或图像背景块这类信息含量低的标记上。要让 Softmax 输出接近 0 的概率，其输入（logits）就必须有极大的动态范围，这迫使前一层网络产生巨大的激活值，形成异常值。
- 恶性循环：由于 Softmax 永远不会输出绝对的 0，在反向传播中总会有梯度信号驱使网络产生更大的输入值，导致训练越久，异常值越强。

提出的方法：
- ClipSoftmax：让Softmax能够确定输出0值
- GatedAttention：Gated_attention(x) = sigmoid(G(x)) ⊙ Attention(x)

## Inference Acceleration
### 投机解码
#### TVD++
https://arxiv.org/pdf/2403.00858v3


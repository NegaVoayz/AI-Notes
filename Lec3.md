# Lecture 3

## 一、课程核心问题

1. **为什么需要深度学习？**
   - 浅层架构（如 SVM）只有1-2层，表达能力有限
   - 深层架构可以复用低层特征，指数级增加表示能力
   - 深度网络能更经济地使用神经元资源

2. **为什么用神经网络实现深度学习？**
   - 神经网络可通过反向传播自动学习特征层次
   - 可端到端训练，避免手工特征工程

---

## 二、机器学习基础回顾

### 1. 特征表示的重要性
> 讲义强调：**表示（Representation）非常非常重要**。如果表示足够鲁棒，分类器的贡献是有限的。

### 2. 基展开与正则化
核心思想：用输入 `X` 的变换增广/替换输入向量，然后在新空间中用线性模型。

---

## 三、深度学习三大核心组件（会考！）

1. **目标函数（Objective Functions）**
2. **学习规则（Learning Rules）**
3. **架构（Architectures）**

---

## 四、损失函数（重点公式）

### 1. 平方误差
$$
Error = \frac{1}{2} \sum_{i=1}^{n} (t_i - y(x_i))^2
$$

### 2. 交叉熵（二分类）
$$
J(\theta) = -\frac{1}{n}\sum_{i=1}^{n}[t_i\ln y(x_i) + (1-t_i)\ln(1-y(x_i))]
$$
其中 $ y(x_i) = \frac{1}{1+\exp(-f_\theta(x_i))} $，$ f_\theta(x) = \theta_0 + \theta_1 x_1 + \cdots + \theta_d x_d $

### 3. Logit（对数几率）
$$
\ln \frac{y}{1-y} = \theta_0 + \theta_1 x_1 + \cdots + \theta_d x_d
$$

---

## 五、Softmax 回归（必考公式！）

### 假设函数（输出 k 维概率向量）
$$
p(t_i = j | x_i) = \frac{\exp(f_\theta(x_i))}{\sum_{j=1}^{k}\exp(f_\theta(x_i))}
$$

### 损失函数
$$
J(\theta) = -\frac{1}{n}\sum_{i=1}^{n}\sum_{j=1}^{k}\mathbf{I}\{t_i = j\} \ln \frac{\exp(f_\theta(x_i))}{\sum_{l=1}^{k}\exp(f_\theta(x_i))}
$$

### 梯度
$$
\nabla_{\theta_j}J(\theta) = -\frac{1}{n}\sum_{i=1}^{n}\left[x_i(\mathbf{I}\{t_i = j\} - p(t_i = j|x_i;\theta))\right]
$$

### 带权重衰减（L2正则化）的 Softmax
$$
J(\theta) = -\frac{1}{n}\sum_{i=1}^{n}\sum_{j=1}^{k}\mathbf{I}\{t_i = j\} \ln \frac{\exp(f_\theta(x_i))}{\sum_{l=1}^{k}\exp(f_\theta(x_i))} + \frac{\lambda}{2}\sum_{i=1}^{n}\sum_{j=1}^{k}\theta_{ij}^2
$$
梯度加正则项：
$$
\nabla_{\theta_j}J(\theta) = -\frac{1}{n}\sum_{i=1}^{n}[x_i(\mathbf{I}\{t_i = j\} - p(t_i = j|x_i;\theta))] + \lambda\theta_j
$$

---

## 六、多项式曲线拟合与正则化

### 模型
$$
y(x, \mathbf{w}) = \sum_{j=0}^{M} w_j x^j
$$

### 误差函数
$$
E(\mathbf{w}) = \frac{1}{2}\sum_{n=1}^{N}\{y(x_n, \mathbf{w}) - t_n\}^2
$$

### 正则化误差
$$
\tilde{E}(\mathbf{w}) = \frac{1}{2}\sum_{n=1}^{N}\{y(x_n, \mathbf{w}) - t_n\}^2 + \frac{\lambda}{2}\|\mathbf{w}\|^2
$$
其中 $ \|\mathbf{w}\|^2 = \mathbf{w}^T\mathbf{w} = \sum_{j=0}^{M} w_j^2 $

---

## 七、贝叶斯视角（重要概念）

### 贝叶斯定理
$$
p(\mathbf{w}|\mathcal{D}) = \frac{p(\mathcal{D}|\mathbf{w})p(\mathbf{w})}{p(\mathcal{D})}
$$
**后验 ∝ 似然 × 先验**

### 曲线拟合的贝叶斯形式
- 似然：$ p(t|x, w, \beta) = \mathcal{N}(t|y(x,w), \beta^{-1}) $
- 先验：$ p(w|\alpha) = \mathcal{N}(w|0, \alpha^{-1}I) $
- 最大化后验等价于最小化 $ \frac{\beta}{2}\sum\{y(x_n,w)-t_n\}^2 + \frac{\alpha}{2}w^Tw $

---

## 八、卷积与池化（会考概念）

### 卷积
- 图像：二维卷积
- 语音/语言：一维卷积（沿时间/序列维度）

### 特征图（Feature Maps）
- 每个卷积核产生一个特征图

### 池化方法
- **Max**（通常最好）
- Min
- Average（通常不好）
- k-max

---

## 九、训练技巧（可能考简答）

- **权重初始化**：$ [-1,1]/\text{units} $ 或 $ [-1,1]/\sqrt{\text{units}} $
- **偏置初始化**：最后一层 $[-0.2, 0.2]$，其他层 $[0, -1]$
- **学习率**：0.05 是好的起点，参数越多学习率应越小
- **隐藏单元数**：只要足够大，对性能影响有限
- **使用 mini-batch 更新**

---

## 十、HMM（手算必考！）

### HMM 五元组
$$
(S, K, \Pi, A, B)
$$
- S：状态集合
- K：输出符号集合
- $\Pi$：初始状态概率
- A：状态转移概率
- B：发射概率

### HMM 三个基本问题（必考！）
1. **评估（Evaluation）**：给定模型，计算观测序列概率 $P(O|\mu)$
2. **解码（Decoding）**：给定观测，找最优状态序列（Viterbi）
3. **学习（Learning）**：给定观测，估计模型参数（Baum-Welch / EM）

---

### 前向算法（手算！）
$$
\alpha_i(1) = \pi_i
$$
$$
\alpha_j(t+1) = \sum_{i=1}^{N} \alpha_i(t) a_{ij} b_{ijo_t}
$$
$$
P(O|\mu) = \sum_{i=1}^{N} \alpha_i(T+1)
$$

### 后向算法
$$
\beta_i(T+1) = 1
$$
$$
\beta_i(t) = \sum_{j=1}^{N} a_{ij} b_{jo_t} \beta_j(t+1)
$$

---

### Viterbi 算法（手算！）
**初始化**：
$$
\delta_j(1) = \pi_j
$$

**递推**：
$$
\delta_j(t+1) = \max_{1 \le i \le N} \delta_i(t) a_{ij} b_{ijo_t}
$$
$$
\psi_j(t+1) = \arg\max_{1 \le i \le N} \delta_i(t) a_{ij} b_{ijo_t}
$$

**终止**：
$$
\hat{X}_{T+1} = \arg\max_{1 \le i \le N} \delta_i(T+1)
$$
$$
\hat{X}_t = \psi_{\hat{X}_{t+1}}(t+1)
$$

---

### Baum-Welch（EM）参数重估（可能考公式）
$$
\gamma_i(t) = P(X_t = i | O, \mu) = \frac{\alpha_i(t)\beta_i(t)}{\sum_j \alpha_j(t)\beta_j(t)}
$$
$$
\xi_t(i,j) = P(X_t=i, X_{t+1}=j | O, \mu) = \frac{\alpha_i(t)a_{ij}b_{jo_t}\beta_j(t+1)}{\sum_m\sum_n \alpha_m(t)a_{mn}b_{no_t}\beta_n(t+1)}
$$

重估公式：
$$
\hat{\pi}_i = \gamma_i(1)
$$
$$
\hat{a}_{ij} = \frac{\sum_{t=1}^{T}\xi_t(i,j)}{\sum_{t=1}^{T}\gamma_i(t)}
$$
$$
\hat{b}_{ijk} = \frac{\sum_{\{t:o_t=k\}}\xi_t(i,j)}{\sum_{t=1}^{T}\xi_t(i,j)}
$$

---

## 十一、条件随机场 CRF（重点概念）

### CRF vs HMM
- **HMM**：生成模型，建模联合概率 $P(X,Y)$
- **CRF**：判别模型，直接建模条件概率 $P(Y|X)$

### CRF 的优势
- 可以融入任意特征
- 没有标签偏置问题（Label Bias Problem）

### CRF 的势函数
$$
p(y|x, \lambda) = \frac{1}{Z(x)} \exp\left(\sum_j \lambda_j F_j(y,x)\right)
$$

### 特征模板（会考概念）
- 一元特征：$ f(S_0, C_0) $
- 二元特征：$ f(S_{-1}, S_0) $
- 组合特征：$ f(S_0, C_{-1}, C_0) $、$ f(S_0, C_0, C_1) $ 等

---

## 十二、深度学习中的序列标注（CRF + NN）

### 句子得分
$$
s(c_{[1:n]}, t_{[1:n]}, \theta) = \sum_{i=1}^{n} (A_{t_{i-1}t_i} + f_\theta(t_i|i))
$$

### 解码（Viterbi 风格）
$$
t_{[1:n]}^* = \arg\max_{t'_{[1:n]}} s(c_{[1:n]}, t'_{[1:n]}, \theta)
$$

### 训练（最大化对数似然）
$$
\log p(t|c, \theta) = s(c, t, \theta) - \log\sum_{\forall t'} \exp\{s(c, t', \theta)\}
$$

### 参数更新
$$
\theta \leftarrow \theta + \lambda \frac{\partial \log p(t|c,\theta)}{\partial \theta}
$$

---

## 十三、词嵌入（Word Embeddings）

### 经典现象（会考！）
- **类比推理**：King - Man + Woman ≈ Queen
- **语义合成**：Madrid - Spain + France ≈ Paris
- **注意**：讲义提到中文词嵌入没有发现类似现象

### 训练方法
- Bengio 2003：神经概率语言模型
- Collobert 2008：ranking 损失函数
- Mikolov 2013：Word2Vec（Skip-gram + CBOW）

---

## 十四、RNN 与 LSTM（会考概念）

### RNN 问题
- **梯度爆炸**（Blow up）
- **梯度消失**（Vanishing gradient）

### LSTM 核心公式（可能考写公式）
$$
f_t = \sigma_g(W_f x_t + U_f h_{t-1} + b_f)
$$
$$
i_t = \sigma_g(W_i x_t + U_i h_{t-1} + b_i)
$$
$$
o_t = \sigma_g(W_o x_t + U_o h_{t-1} + b_o)
$$
$$
c_t = f_t \circ c_{t-1} + i_t \circ \sigma_c(W_c x_t + U_c h_{t-1} + b_c)
$$
$$
h_t = o_t \circ \sigma_h(c_t)
$$

### GRU（更简洁，无输出门）
$$
z_t = \sigma_g(W_z x_t + U_z h_{t-1} + b_z)
$$
$$
r_t = \sigma_g(W_r x_t + U_r h_{t-1} + b_r)
$$
$$
h_t = (1-z_t) \circ h_{t-1} + z_t \circ \sigma_h(W_h x_t + U_h(r_t \circ h_{t-1}) + b_h)
$$

---

## 十五、现代架构（概念了解）

### Transformer
- 基于**自注意力机制**（Self-Attention）
- 取代 RNN，并行计算

### BERT
- **掩码语言模型**（Masked Language Model）
- 双向编码器

### GPT
- 自回归语言模型
- 单向（从左到右）

### Prompt-based Learning
- 用提示（Prompt）让预训练模型解决下游任务
- 无需微调或少量微调

---

## 十六、生成对抗网络 GAN

### 核心思想
- 生成器（Generator）：生成假样本
- 判别器（Discriminator）：区分真/假
- 对抗训练，最终生成器可生成逼真样本

### 应用
- 文本到图像合成（Text-to-Image）

---

## 十七、脉冲神经网络 SNN（新兴方向）

### 动机
- 大脑功耗仅 20W，而计算机 250W
- 脉冲时序携带更多信息
- 更适合硬件实现，能效更高

### 典型神经元模型
- **LIF（Leaky Integrate-and-Fire）**：漏积分发放神经元

---

## 十八、可能考到的延伸知识（讲义没写但老师可能讲）

### 1. 专家系统（Expert Systems）
- 基于规则的知识库 + 推理引擎
- 典型代表：MYCIN（医学诊断）、DENDRAL（化学分析）
- 局限性：知识获取瓶颈、无法处理不确定性

### 2. 多臂老虎机（Multi-Armed Bandit）
- 探索-利用困境（Exploration-Exploitation Dilemma）
- ε-greedy、UCB、Thompson Sampling
- RL 的简化形式

### 3. 反向传播（Backpropagation，BP）
- 链式法则
- 可能要求手算简单网络的梯度更新

### 4. 感知机（Perceptron）
- 线性分类器
- 异或问题（XOR）不可分 → 需要多层

### 5. 梯度消失/爆炸
- 原因：链式法则中多次乘以小于1/大于1的数
- 解决方案：ReLU、Batch Normalization、残差连接

### 6. Dropout
- 随机丢弃神经元，防止过拟合
- 相当于模型集成

### 7. Batch Normalization
- 标准化每层输入
- 加速训练，允许更大学习率

---

## 十九、总结（讲义结论）

1. 深度学习旨在学习**特征层次**，高层特征由低层特征组合而成
2. 深层网络的特征通过**反向传播**自动训练，与任务相关
3. **2006年突破**：DBN 和 Stacked Autoencoder 的**逐层无监督预训练 + 监督微调**策略

---

## 考试准备建议

| 题型         | 准备方向                               |
| ------------ | -------------------------------------- |
| **手算 HMM** | 前向、后向、Viterbi 的数值计算         |
| **手算 BP**  | 简单网络的前向传播 + 反向传播梯度      |
| **解释概念** | SVM vs DL、HMM vs CRF、RNN vs LSTM     |
| **公式推导** | Softmax 梯度、正则化、贝叶斯公式       |
| **简答题**   | 三大组件、训练技巧、GAN 原理、SNN 动机 |
| **延伸知识** | 专家系统、多臂老虎机、感知机局限性     |

# Lecture 9 强化学习讲义笔记

> 本笔记基于讲义整理，涵盖核心概念、数学推导、经典算法及可能的延伸考点。

---

## 一、课程定位与三大学习范式

| 范式           | 特点                                               |
| -------------- | -------------------------------------------------- |
| **监督学习**   | 有标签示例，外部监督                               |
| **无监督学习** | 无标签数据，发现隐藏结构                           |
| **强化学习**   | 目标导向的智能体与不确定环境交互，考虑完整决策问题 |

---

## 二、强化学习基本框架

### 2.1 核心元素

- **状态集合** $ S $
- **动作集合** $ A $
- **奖励集合** $ R $
- **策略** $ \pi(a|s) $：在状态 $ s $ 下采取动作 $ a $ 的概率
- **状态价值函数** $ v_\pi(s) $：在策略 $ \pi $ 下状态 $ s $ 的价值
- **动作价值函数** $ q_\pi(s,a) $：在策略 $ \pi $ 下状态 $ s $ 采取动作 $ a $ 的价值

### 2.2 轨迹与回报

轨迹：
$$
S_0, A_0, R_1, S_1, A_1, R_2, S_2, A_2, R_3, \ldots
$$

**回报（Return）**：
- 有限步：$ G_t = R_{t+1} + R_{t+2} + \cdots + R_T $
- 无限步（折扣）：$ G_t = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} $

### 2.3 Bellman 方程（⭐必考推导）

$$
v_\pi(s) = \mathbb{E}_\pi[G_t | S_t = s]
$$

展开：
$$
v_\pi(s) = \sum_a \pi(a|s) \sum_{s', r} p(s', r|s, a) \left[ r + \gamma v_\pi(s') \right]
$$

**最优 Bellman 方程**：
$$
v_*(s) = \max_a \sum_{s', r} p(s', r|s, a) [r + \gamma v_*(s')]
$$

$$
q_*(s,a) = \sum_{s', r} p(s', r|s, a) [r + \gamma \max_{a'} q_*(s', a')]
$$

---

## 三、多臂老虎机（Multi-armed Bandit）—— 可能手算考点

### 3.1 基本设定

- 每轮选择 $ k $ 个动作之一
- 每次选择获得随机奖励
- 目标：最大化累积奖励

### 3.2 动作价值估计

设 $ R_t $ 为第 $ i $ 次选择某动作后的奖励，$ Q_n $ 为该动作被选择 $ n-1 $ 次后的估计值：

$$
Q_n = \frac{R_1 + R_2 + \cdots + R_{n-1}}{n-1}
$$

**增量更新形式**（考试可能让推导）：
$$
Q_{n+1} = Q_n + \frac{1}{n} [R_n - Q_n]
$$

### 3.3 非平稳环境

对于非平稳问题，使用常数步长 $ \alpha $：
$$
Q_{n+1} = Q_n + \alpha [R_n - Q_n]
$$

### 3.4 探索-利用困境

- **ε-greedy**：以 $ 1-\varepsilon $ 概率选择最优动作，$ \varepsilon $ 概率随机探索
- **UCB（Upper Confidence Bound）**：考虑不确定性
- **梯度 bandit 算法**：使用偏好函数

---

## 四、动态规划与策略迭代

### 4.1 广义策略迭代（GPI）

```
策略评估 → 策略改进 → 策略评估 → ...（迭代收敛）
```

### 4.2 策略迭代算法（⭐可能手算步骤）

**初始化**：对所有 $ s \in S $，任意初始化 $ V(s) $ 和 $ \pi(s) $

**策略评估（迭代）**：
$$
V(s) \leftarrow \sum_{s', r} p(s', r|s, \pi(s)) [r + \gamma V(s')]
$$
重复直到 $ \Delta < \theta $

**策略改进**：
$$
\pi(s) \leftarrow \arg\max_a \sum_{s', r} p(s', r|s, a) [r + \gamma V(s')]
$$
若策略稳定则停止，否则回到策略评估

### 4.3 价值迭代

直接使用 Bellman 最优方程迭代：
$$
V_{k+1}(s) = \max_a \sum_{s', r} p(s', r|s, a) [r + \gamma V_k(s')]
$$

---

## 五、蒙特卡洛方法

### 5.1 核心思想

- 从经验中学习，不需要环境模型
- 使用完整的回报 $ G_t $ 来更新价值估计
- **首次访问 MC** vs **每次访问 MC**

### 5.2 MC 更新公式

$$
V(S_t) \leftarrow V(S_t) + \alpha [G_t - V(S_t)]
$$

### 5.3 MC 控制

- 策略评估：MC 方法估计 $ Q_\pi $
- 策略改进：$ \pi(s) = \arg\max_a Q(s,a) $
- 使用 ε-greedy 保证探索

---

## 六、时序差分学习（TD Learning）（⭐核心考点）

> Sutton 称 TD 为强化学习中"最核心、最新颖"的思想

### 6.1 TD(0) 更新

**MC 更新**：
$$
V(S_t) \leftarrow V(S_t) + \alpha [G_t - V(S_t)]
$$

**TD(0) 更新**（自举）：
$$
V(S_t) \leftarrow V(S_t) + \alpha [R_{t+1} + \gamma V(S_{t+1}) - V(S_t)]
$$

其中 $ R_{t+1} + \gamma V(S_{t+1}) $ 称为 **TD 目标**，$ \delta_t = R_{t+1} + \gamma V(S_{t+1}) - V(S_t) $ 称为 **TD 误差**

### 6.2 Sarsa（同轨策略 TD 控制）

更新动作价值：
$$
Q(S,A) \leftarrow Q(S,A) + \alpha [R + \gamma Q(S', A') - Q(S,A)]
$$
- 使用当前策略选择 $ A $ 和 $ A' $
- 同轨策略（on-policy）

### 6.3 Expected Sarsa

$$
Q(S,A) \leftarrow Q(S,A) + \alpha [R + \gamma \sum_a \pi(a|S') Q(S', a) - Q(S,A)]
$$
- 比 Sarsa 方差更小

### 6.4 Q-learning（异轨策略 TD 控制）（⭐必考）

$$
Q(S,A) \leftarrow Q(S,A) + \alpha [R + \gamma \max_a Q(S', a) - Q(S,A)]
$$
- 直接学习最优动作价值 $ q_* $
- 异轨策略（off-policy）：行为策略用于探索，目标策略用于改进

---

## 七、n步 TD 与 λ-回报

### 7.1 n步回报

$$
G_{t:t+n} = R_{t+1} + \gamma R_{t+2} + \cdots + \gamma^{n-1} R_{t+n} + \gamma^n \hat{v}(S_{t+n})
$$

- 1步 TD：$ n=1 $
- MC：$ n \to \infty $（或到终止）

### 7.2 λ-回报

$$
G_t^\lambda = (1-\lambda) \sum_{n=1}^{\infty} \lambda^{n-1} G_{t:t+n}
$$
- $ \lambda = 0 $：1步 TD
- $ \lambda = 1 $：MC

### 7.3 前向视图 vs 后向视图

- **前向视图**：理论框架，需要知道未来
- **后向视图**：使用**资格迹**实现在线学习

---

## 八、资格迹（Eligibility Traces）（⭐可能概念解释）

### 8.1 核心公式

资格迹更新：
$$
\mathbf{z} \leftarrow \gamma \lambda \mathbf{z} + \nabla \hat{v}(S, \mathbf{w})
$$

TD(λ) 权重更新：
$$
\mathbf{w} \leftarrow \mathbf{w} + \alpha \delta \mathbf{z}
$$

其中 $ \delta = R + \gamma \hat{v}(S', \mathbf{w}) - \hat{v}(S, \mathbf{w}) $

### 8.2 统一视角

| λ 值      | 对应方法             |
| --------- | -------------------- |
| λ = 0     | 1步 TD               |
| λ = 1     | MC                   |
| 0 < λ < 1 | 中间方法（通常更优） |

- 资格迹提供了 **MC 的在线实现**方式
- 可以处理**无终止条件的连续问题**

---

## 九、策略梯度方法

### 9.1 策略参数化

使用 softmax 参数化数值偏好：
$$
\pi(a|s, \theta) = \frac{\exp(h(s,a,\theta))}{\sum_b \exp(h(s,b,\theta))}
$$

### 9.2 REINFORCE 算法

**REINFORCE with Baseline**：

生成整条轨迹后，对每一步：
$$
\delta = G_t - \hat{v}(S_t, \mathbf{w})
$$
$$
\mathbf{w} \leftarrow \mathbf{w} + \alpha^{\mathbf{w}} \gamma^t \delta \nabla_{\mathbf{w}} \hat{v}(S_t, \mathbf{w})
$$
$$
\theta \leftarrow \theta + \alpha^{\theta} \gamma^t \delta \nabla_{\theta} \ln \pi(A_t|S_t, \theta)
$$

- **Baseline（基线）** 减少方差
- 策略梯度定理支撑

### 9.3 Actor-Critic 方法

- **Actor**：策略 $ \pi(a|s,\theta) $
- **Critic**：价值函数 $ \hat{v}(s,\mathbf{w}) $
- 使用 TD 误差作为优势函数

---

## 十、深度强化学习

### 10.1 DQN

- 使用深度神经网络近似 Q 函数
- **经验回放**：打破时间相关性
- **目标网络**：稳定训练

### 10.2 AlphaGo

- **策略网络**：选择落子
- **价值网络**：评估局面
- 训练方式：监督学习（人类棋谱）+ 强化学习（自我对弈）

### 10.3 多智能体自课程学习（OpenAI hide-and-seek）

- 智能体通过竞争自发产生复杂行为
- 涌现工具使用等行为

---

## 十一、RL 用于执行优化（Trade Execution）

### 11.1 应用框架

- **状态**：时间、剩余股份、订单簿位置等
- **动作**：订单在订单簿中的位置
- **奖励**：执行价格
- **随机性**：相同状态可演化不同

### 11.2 微结构建模

将执行问题建模为**状态依赖的随机最优控制**问题

---

## 十二、RL 的主要挑战（可能延伸为论述题）

1. **状态表示与特征学习**：高维连续状态/动作空间
2. **奖励设计**：未指定、多目标或风险敏感的奖励
3. **策略评估**：传感器/执行器/奖励的大延迟
4. **探索-利用权衡**
5. **有限样本快速学习**
6. **世界模型学习与规划**
7. **子问题自动选择**：智能体如何结构化自己的认知
8. **多智能体强化学习**

---

## 十三、可能延伸的课堂补充内容（建议自行查证）

### 13.1 动态规划相关

- **策略迭代 vs 价值迭代** 收敛性比较
- **异步 DP**：原位更新、优先扫描
- **广义策略迭代（GPI）** 的理论保证

### 13.2 近似方法

- **线性价值函数近似**：特征向量 + 权重
- **最小二乘 TD（LSTD）**
- **梯度 TD（GTD）**：解决发散问题

### 13.3 模型学习

- **Dyna 架构**：真实经验 + 模拟经验
- **规划 vs 学习** 的权衡

### 13.4 分布强化学习

- 学习回报的分布而非期望
- C51、IQN 等算法

### 13.5 分层强化学习

- Option 框架
- 子目标与层级策略

---

## 十四、公式速查表（考试必备）

| 公式                                                         | 说明           |
| ------------------------------------------------------------ | -------------- |
| $ G_t = \sum_{k=0}^{\infty} \gamma^k R_{t+k+1} $             | 折扣回报       |
| $ v_\pi(s) = \sum_a \pi(a\|s) \sum_{s',r} p(s',r\|s,a)[r + \gamma v_\pi(s')] $ | Bellman 方程   |
| $ Q_{n+1} = Q_n + \frac{1}{n}[R_n - Q_n] $                   | 采样平均更新   |
| $ Q_{n+1} = Q_n + \alpha[R_n - Q_n] $                        | 常数步长更新   |
| $ V(S_t) \leftarrow V(S_t) + \alpha[R_{t+1} + \gamma V(S_{t+1}) - V(S_t)] $ | TD(0)          |
| $ Q(S,A) \leftarrow Q(S,A) + \alpha[R + \gamma \max_a Q(S',a) - Q(S,A)] $ | Q-learning     |
| $ Q(S,A) \leftarrow Q(S,A) + \alpha[R + \gamma Q(S',A') - Q(S,A)] $ | Sarsa          |
| $ G_{t:t+n} = R_{t+1} + \gamma R_{t+2} + \cdots + \gamma^{n-1}R_{t+n} + \gamma^n \hat{v}(S_{t+n}) $ | n步回报        |
| $ \mathbf{z} \leftarrow \gamma\lambda\mathbf{z} + \nabla\hat{v}(S,\mathbf{w}) $ | 资格迹         |
| $ \delta = R + \gamma \hat{v}(S',\mathbf{w}) - \hat{v}(S,\mathbf{w}) $ | TD 误差        |
| $ \pi(a\|s,\theta) = \exp(h(s,a,\theta)) / \sum_b \exp(h(s,b,\theta)) $ | Softmax 策略   |
| $ \theta \leftarrow \theta + \alpha^\theta \gamma^t \delta \nabla_\theta \ln \pi(A_t\|S_t,\theta) $ | REINFORCE 更新 |

---

## 十五、概念辨析（考试可能出）

| 概念                     | 关键区别                                                   |
| ------------------------ | ---------------------------------------------------------- |
| **同轨 vs 异轨**         | 同轨：评估和改进同一策略；异轨：行为策略 ≠ 目标策略        |
| **MC vs TD**             | MC 需要完整轨迹，无偏但方差大；TD 可在线学习，有偏但方差小 |
| **策略迭代 vs 价值迭代** | 策略迭代包含完整策略评估步骤；价值迭代直接迭代最优价值     |
| **前向 vs 后向视图**     | 前向：理论框架；后向：在线实现                             |
| **探索 vs 利用**         | 探索收集信息；利用最大化当前知识                           |
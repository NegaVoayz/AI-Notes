# Lecture 7

---

## 一、命题逻辑 (Propositional Logic)

### 1.1 基本符号
- **命题符号**：P, Q, R, S, ...
- **真值符号**：true, false
- **连接词**：
  - ∧ 合取（and）
  - ∨ 析取（or）
  - ¬ 否定（not）
  - → 蕴含（implication）
  - ≡ 等价（equivalence）

### 1.2 合式公式 (WFF)
- 每个命题符号和真值符号都是句子
- 句子的否定、合取、析取、蕴含、等价仍是句子

### 1.3 语义（真值表）
| 连接词 | 真值条件                       |
| ------ | ------------------------------ |
| ¬P     | P为true时false，P为false时true |
| P∧Q    | 仅当P和Q都为true时为true       |
| P∨Q    | 仅当P和Q都为false时为false     |
| P→Q    | 仅当P为true且Q为false时为false |
| P≡Q    | 当P和Q真值相同时为true         |

### 1.4 重要等价关系（需掌握）
```
双重否定:  ¬(¬P) ≡ P
蕴含转换:  (P→Q) ≡ (¬P∨Q)
逆否律:    (P→Q) ≡ (¬Q→¬P)
德摩根律:  ¬(P∨Q) ≡ ¬P∧¬Q
          ¬(P∧Q) ≡ ¬P∨¬Q
交换律:    P∨Q ≡ Q∨P, P∧Q ≡ Q∧P
结合律:    (P∨Q)∨R ≡ P∨(Q∨R), (P∧Q)∧R ≡ P∧(Q∧R)
分配律:    P∨(Q∧R) ≡ (P∨Q)∧(P∨R)
          P∧(Q∨R) ≡ (P∧Q)∨(P∧R)
```

---

## 二、一阶谓词逻辑 (First-Order Logic)

### 2.1 基本组成
- **常量符号**：小写字母开头（如 socrates, 0, π）
- **变量符号**：小写字母开头（如 x, y, z）
- **函数符号**：小写开头，有元数（arity）（如 father(x), sum(x,y)）
- **谓词符号**：小写开头，有元数（如 man(x), parent(x,y)）
- **量词**：∀（全称量词），∃（存在量词）

### 2.2 原子句子
- 谓词常量+括号内n个项：`p(t₁, t₂, ..., tₙ)`
- true和false也是原子句子

### 2.3 句子构成规则
1. 每个原子句子是句子
2. 句子的否定、合取、析取、蕴含、等价仍是句子
3. ∀x s 和 ∃x s 是句子

### 2.4 解释 (Interpretation)
- 域 D 为非空集合
- 每个常量 → D中元素
- 每个变量 → D的非空子集（可替换的值）
- 每个n元函数 → Dⁿ → D 的映射
- 每个n元谓词 → Dⁿ → {true, false} 的映射

### 2.5 真值判定
- ∀x s：对所有x的赋值s都为true时为true
- ∃x s：存在x的赋值使s为true时为true
- 其他连接词真值同命题逻辑

### 2.6 满足、模型、有效、不一致
- **满足 (Satisfy)**：解释I下表达式x为true
- **模型 (Model)**：对所有变量赋值都满足x的解释
- **可满足 (Satisfiable)**：存在解释和变量赋值使其为true
- **不可满足/不一致 (Inconsistent)**：不存在使其为true的解释
- **有效 (Valid)**：所有解释下都为true
  - 不一致例：∃x(P(x)∧¬P(x))
  - 有效例：P(x)∨¬P(x)

---

## 三、推理 (Inference)

### 3.1 基本概念
- **逻辑蕴涵 (Logical Follows)**：所有满足S的解释也满足x
- **可靠 (Sound)**：推理规则产生的表达式都逻辑蕴涵于S
- **完备 (Complete)**：规则能推出所有逻辑蕴涵的表达式

### 3.2 推理规则
| 规则                                 | 形式    | 结论            |
| ------------------------------------ | ------- | --------------- |
| 假言推理 (Modus Ponens)              | P, P→Q  | Q               |
| 否定后件 (Modus Tollens)             | P→Q, ¬Q | ¬P              |
| 合取消去 (Elimination)               | P∧Q     | P, Q            |
| 合取引入 (Introduction)              | P, Q    | P∧Q             |
| 全称实例化 (Universal Instantiation) | ∀x P(x) | P(a)（a在域中） |

### 3.3 合一 (Unification)
- **最一般合一子 (MGU)**：最一般的替换使表达式匹配
- 合一条件：
  - 常量不能被替换
  - 变量不能与包含该变量的项合一（出现检查）
  - 替换必须一致作用于变量所有出现
- **例子**：
  ```
  parents(x, father(x), mother(bill))
  parents(bill, father(bill), y)
  MGU: {bill/x, mother(bill)/y}
  ```

---

## 四、归结反驳 (Resolution Refutation)

### 4.1 步骤
1. 将前提/公理化为子句形式
2. 将要证明的结论取反，加入子句集
3. 归结子句产生新子句
4. 产生空子句（矛盾）即证明完成

### 4.2 子句形式转换步骤（重点）
1. **消去蕴含**：a→b ≡ ¬a∨b
2. **缩小否定范围**（德摩根律、量词否定）：
   - ¬∃x a(x) ≡ ∀x ¬a(x)
   - ¬∀x b(x) ≡ ∃x ¬b(x)
3. **标准化变量**：不同量词绑定的变量用不同名称
4. **量词左移**（前束范式）
5. **Skolem化**：消去存在量词（用Skolem函数/常量替换）
6. **去掉全称量词**
7. **化为合取范式（CNF）**：用分配律
8. **拆分为独立子句**
9. **再次标准化变量**：不同子句变量不同名

### 4.3 归结例子（需会手算）
**Happy Student 故事**：
```
前提：
1. ∀x(pass(x,history)∧win(x,lottery)→happy(x))
2. ∀x∀y(study(x)∨lucky(x)→pass(x,y))
3. ¬study(john)∧lucky(john)
4. ∀x(lucky(x)→win(x,lottery))
结论：happy(john)
```

子句形式：
```
1. ¬pass(x,history)∨¬win(x,lottery)∨happy(x)
2a. ¬study(y)∨pass(y,z)
2b. ¬lucky(w)∨pass(w,v)
3a. ¬study(john)
3b. lucky(john)
4. ¬lucky(u)∨win(u,lottery)
否定结论：¬happy(john)
```

**归结过程**：
```
¬happy(john) + [1] → ¬pass(john,history)∨¬win(john,lottery)
+ [4] {john/u} → ¬pass(john,history)∨¬lucky(john)
+ [3b] → ¬pass(john,history)
+ [2b] {john/w, history/v} → lucky(john)
+ [3b] → □ (空子句)
```

---

## 五、逻辑编程与Prolog

### 5.1 Horn子句
- 最多一个正文字的析取式：`a ∨ ¬b₁ ∨ ¬b₂ ∨ ... ∨ ¬bₙ`
- 写成蕴含形式：`a ← b₁ ∧ b₂ ∧ ... ∧ bₙ`
- **三种形式**：
  - 事实 (Facts)：`a ←` （无体）
  - 规则 (Rules)：`a ← b₁∧...∧bₙ`
  - 目标 (Goals)：`← a₁∧...∧aₙ`（无头）

### 5.2 Prolog执行机制
- **深度优先搜索**（DFS）+ 回溯
- **从左到右**选择子目标
- **从上到下**搜索匹配子句
- **合一**匹配头部
- **封闭世界假设**：未证实的为假（否定即失败）

### 5.3 Prolog语法对应
| 含义 | 谓词逻辑 | Prolog |
| ---- | -------- | ------ |
| 合取 | ∧        | ,      |
| 析取 | ∨        | ;      |
| 蕴含 | ←        | :-     |
| 否定 | ¬        | not    |

### 5.4 列表与匹配（需会手算）
- `[X|Y]`：头为X，尾为Y
- 匹配例子：
  ```
  [tom, dick, harry] 匹配 [x|y] → x=tom, y=[dick,harry]
  [tom, dick, harry] 匹配 [x,y|z] → x=tom, y=dick, z=[harry]
  ```

### 5.5 member谓词实现
```
member(X, [X|T]).
member(X, [Y|T]) :- member(X, T).
```

### 5.6 回溯与Cut (!)
- Cut (`!`)：截断回溯，固定已做的选择
- 可减少搜索空间，但改变程序语义

---

## 六、重要扩展内容

### 6.1 默认推理 (Default Reasoning)
- **默认逻辑**：`p : ¬r₁∧...∧¬rₙ / q`（若p成立且无法证明rᵢ，则推出q）
- **限制逻辑 (Circumscription)**：通过最小化异常谓词进行推理

### 6.2 模态逻辑 (Modal Logic)
- □p：必然p
- ◇p：可能p
- 关系：□p ↔ ¬◇¬p
- 公理：□(p→q) → (□p→□q)

### 6.3 真值维护系统 (TMS)
- 记录每个结论的依赖/证明过程
- 发现矛盾时定位错误假设
- 撤销错误假设及其所有依赖结论

### 6.4 Herbrand结构
- **Herbrand域**：由所有常量、函数构成的ground项集合
- 用于判定一阶逻辑公式的可满足性

### 6.5 逻辑系统分类
| 类型               | 特点               |
| ------------------ | ------------------ |
| 一阶/高阶逻辑      | 表达力强，不可判定 |
| 命题逻辑片段       | P/NP完全，无量化   |
| 描述逻辑、模态逻辑 | 受限量化，可判定   |

---

## 七、经典题目类型（理解原理）

### 7.1 金融顾问系统
规则：
```
1. savings_account(inadequate) → investment(savings)
2. savings_account(adequate)∧income(adequate) → investment(stocks)
3. savings_account(adequate)∧income(inadequate) → investment(combination)
4-5. 储蓄充足/不足判定
6-8. 收入充足/不足判定
```
- 手算推理链：基于事实和规则推导结论

### 7.2 农夫过河问题
- 状态表示：`state(F,W,G,C)`，每个位置e或w
- 约束：狼+羊无人时狼吃羊；羊+菜无人时羊吃菜
- 搜索策略：DFS + 避免重复状态

### 7.3 骑士巡游
- 路径查找：`path(X,Y,L)`，L为已访问列表
- 防止循环：`not(member(Z,L))`

---

## 八、需重点掌握的技能

1. **真值表构建**：验证等价关系
2. **一阶逻辑形式化**：自然语言→逻辑公式
3. **子句形式转换**：6步标准流程（特别是Skolem化）
4. **归结证明**：手算归结树
5. **Prolog程序追踪**：递归执行过程
6. **列表匹配**：模式匹配与变量绑定
7. **解答提取**：从归结证明中提取答案

---

## 九、可能涉及的课上延伸内容

- **专家系统**：规则库、推理引擎（前向/后向链）
- **不确定性推理**：贝叶斯网络、置信度
- **非单调逻辑**：默认逻辑、限制逻辑
- **时序逻辑**：CTL, LTL
- **描述逻辑**：SHIQ, SHOIN（OWL基础）
- **与Prolog相关**：CLP（约束逻辑编程）、DCG（定子句语法）

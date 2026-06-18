# Lecture 8 CLIPS 专家系统讲义笔记

---

## 二、专家系统基础概念

### 2.1 基于规则的专家系统结构
- **模块化性质** (Modular nature)
- **解释机制** (Explanation facilities)
- **与人类认知过程的相似性**

### 2.2 基本组件
1. **事实列表** (Fact list) - 存储已知事实
2. **知识库** (Knowledge base) - 存储规则
3. **推理引擎** (Inference engine) - 匹配规则与事实

---

## 三、CLIPS 数据类型

### 3.1 基本数据类型
| 类型                | 示例                                  |
| ------------------- | ------------------------------------- |
| **整数** (integer)  | 1, +3, -1, 65                         |
| **浮点数** (float)  | 1.5, 0.7, 9e+1, 3.5e10                |
| **符号** (symbol)   | fire, 345B, activate-sprinkler-system |
| **字符串** (string) | "John Smith", "string"                |

### 3.2 分隔符 (Delimiters)
空格、制表符、回车、换行、`()` `,` `;` `&` `|` `~` `<` `>`

**注意**：`?` 和 `$?` 不能放在符号开头（用于表示变量）

---

## 四、事实 (Facts) 与模板 (Deftemplate)

### 4.1 事实示例
```lisp
(person (name "John Smith") (age 21) (eye-color black) (hair-color black))
```

### 4.2 模板定义 (deftemplate)
```lisp
(deftemplate person "A person template"
  (slot name)
  (slot age)
  (slot eye-color)
  (slot hair-color))
```

### 4.3 槽类型 (Slot Types)
| 类型        | 说明           |
| ----------- | -------------- |
| `?VARIABLE` | 默认，任意类型 |
| `?SYMBOL`   | 符号           |
| `?STRING`   | 字符串         |
| `?LEXEME`   | 符号或字符串   |
| `?INTEGER`  | 整数           |
| `?FLOAT`    | 浮点数         |
| `?NUMBER`   | 整数或浮点数   |

### 4.4 multislot 示例
```lisp
(multislot values)  ; 可匹配 (values 7 9 3 4 20)
```

### 4.5 模板属性
```lisp
(deftemplate person
  (multislot name (type SYMBOL) (cardinality 1 6))
  (slot age (type INTEGER) (range 0 ?VARIABLE))
  (slot gender (allowed-values male female) (default female)))
```

### 4.6 属性总结表
| 属性              | 语法                            | 说明                   |
| ----------------- | ------------------------------- | ---------------------- |
| **type**          | `(type <type-spec>)`            | 指定数据类型           |
| **allowed-value** | `(allowed-values <value>+)`     | 允许的值列表           |
| **range**         | `(range <lower> <upper>)`       | 数值范围               |
| **cardinality**   | `(cardinality <lower> <upper>)` | multislot 元素数量范围 |
| **default**       | `(default <spec>)`              | 默认值                 |

---

## 五、事实操作

### 5.1 基本操作
| 操作        | 语法                                     | 说明     |
| ----------- | ---------------------------------------- | -------- |
| **assert**  | `(assert <fact>+)`                       | 添加事实 |
| **facts**   | `(facts [<start> [<end> [<max>]]])`      | 显示事实 |
| **retract** | `(retract <fact-index>+)`                | 删除事实 |
| **modify**  | `(modify <fact-index> <slot-modifier>+)` | 修改事实 |

### 5.2 defacts（预定义事实）
```lisp
(defacts people "Some people we know"
  (person (name "Sean Smith") (age 21) (eye-color black) (hair-color black))
  (person (name "John Riley") (age 24) (eye-color blue) (hair-color black)))
```

---

## 六、规则 (Rules)

### 6.1 规则基本结构
```lisp
(defrule fire-emergency
  (emergency (type fire))        ; LHS (Left-Hand Side)
  =>                              ; 箭头分隔
  (assert (response (action activate-sprinkler-system)))  ; RHS
)
```

### 6.2 运行命令
- `(run [<limit>])` - 执行规则

### 6.3 规则语法
```lisp
(defrule <rule-name> [<optional-comment>]
  <patterns>*    ; LHS
  =>
  <actions>*     ; RHS
)
```

---

## 七、规则操作与调试

### 7.1 显示构造
| 命令                  | 说明             |
| --------------------- | ---------------- |
| `(list-defrules)`     | 列出所有规则     |
| `(list-deftemplates)` | 列出所有模板     |
| `(list-deffacts)`     | 列出所有事实定义 |

### 7.2 美化打印
| 命令                              | 说明             |
| --------------------------------- | ---------------- |
| `(ppdefrule <rule-name>)`         | 美化打印规则     |
| `(ppdeftemplate <template-name>)` | 美化打印模板     |
| `(ppdeffacts <deffacts-name>)`    | 美化打印事实定义 |

### 7.3 删除构造
| 命令                              | 说明         |
| --------------------------------- | ------------ |
| `(undefrule <rule-name>)`         | 删除规则     |
| `(undeftemplate <template-name>)` | 删除模板     |
| `(undeffacts <deffacts-name>)`    | 删除事实定义 |

### 7.4 printout 与 clear
```lisp
(printout t "Activate the sprinkler system" crlf)
(clear)  ; 清除所有构造
```

---

## 八、数学函数（前缀形式）

| 表达式              | CLIPS 形式                      |
| ------------------- | ------------------------------- |
| 3 + 4 × 5           | `(+ 3 (* 4 5))`                 |
| (y₂-y₁)/(x₂-x₁) > 0 | `(> (/ (- y2 y1) (- x2 x1)) 0)` |

---

## 九、变量与模式匹配

### 9.1 变量绑定
```lisp
(defrule find-blue-eyes
  (person (name ?name) (eyes blue))
  =>
  (printout t ?name " has blue eyes." crlf))
```

### 9.2 变量共享
```lisp
(defrule find-eyes
  (find (eyes ?eyes))
  (person (name ?name) (eyes ?eyes))
  =>
  (printout t ?name " has " ?eyes " eyes." crlf))
```

---

## 十、通配符 (Wildcards)

### 10.1 单字段通配符 `?`
```lisp
(person (name ? ? ?last-name))  ; 匹配三个名字部分，绑定最后一个
```

### 10.2 未指定槽位
```lisp
(person (name John Q. Smith))  ; 等价于 (person (name John Q. Smith) (identity-card-number ?))
```

### 10.3 多字段通配符 `$?`
```lisp
(person (children $?before ?child $?after))
; 匹配前后任意数量的子元素
```

### 10.4 连接约束 (Connective Constraints)
| 符号 | 说明 |
| ---- | ---- |
| `~`  | NOT  |
| `\|` | OR   |
| `&`  | AND  |

### 10.5 约束示例
```lisp
(hair ~black)                    ; 非黑发
(hair brown|black)               ; 棕色或黑色
(hair ?color&brown|black)        ; 绑定颜色，约束为棕色或黑色
(hair ?hair2&black|?hair1)       ; 黑色或与第一个人的头发颜色相同
```

---

## 十一、条件元素 (Conditional Elements)

### 11.1 谓词函数
| 函数   | 示例                                 | 结果  |
| ------ | ------------------------------------ | ----- |
| `and`  | `(and (> 4 3) (> 4 5))`              | FALSE |
| `or`   | `(or (> 4 3) (> 4 5))`               | TRUE  |
| `not`  | `(not (integerp 3))`                 | FALSE |
| `test` | `(test (and (integerp 3) (>= 3 1)))` | TRUE  |

### 11.2 测试条件内联写法
```lisp
(age ?age&:(> ?age 18))  ; 等价于 (age ?age) (test (> ?age 18))
```

### 11.3 or 条件元素
```lisp
(defrule shut-off-electricity
  ?power <- (electrical-power (status on))
  (or (emergency (type flood))
      (extinguisher-system (type water-sprinkler) (status on)))
  =>
  (modify ?power (status off))
  (printout t "Shut off the electricity" crlf))
```

### 11.4 and 条件元素
```lisp
(defrule use-carbon-dioxide-extinguisher
  ?system <- (extinguisher-system (type carbon-dioxide) (status off))
  (or (emergency (type class-B-fire))
      (and (emergency (type class-C-fire))
           (electrical-power (status off))))
  =>
  (modify ?system (status on)))
```

### 11.5 not 条件元素（找最大值）
```lisp
(defrule largest-number
  (number ?x)
  (not (number ?y&:(> ?y ?x)))
  =>
  (printout t "Largest number is " ?x crlf))
```

### 11.6 exists 条件元素
```lisp
(defrule operator-alert-for-emergency
  (exists (emergency))  ; 只要存在 emergency 事实就触发一次
  =>
  (printout t "Emergency: Operator Alert" crlf)
  (assert (operator-alert)))
```

### 11.7 forall 条件元素
```lisp
(defrule all-fires-being-handled
  (forall (emergency (type fire) (location ?where))
          (fire-squad (location ?where))
          (evacuated (building ?where)))
  =>
  (printout t "All buildings that are on fire have been evacuated" crlf))
```

**forall 等价形式**：
```
(forall <first-CE> <remaining-CEs>+)
≡ (not (and <first-CE> (not (and <remaining-CEs>+))))
```

### 11.8 logical 条件元素
```lisp
(defrule use-oxygen-masks
  (logical (noxious-fumes-present) (gas-extinguishers-in-use))
  (emergency (type fire))
  =>
  (assert (use-oxygen-masks)))
```

---

## 十二、模式匹配效率

### 12.1 模式匹配总结表
| 符号               | 名称         | 示例                  |
| ------------------ | ------------ | --------------------- |
| `?<var>`           | 变量         | `?name`               |
| `?`                | 单字段通配符 | `?`                   |
| `$?`               | 多字段通配符 | `$?before`            |
| `~, \|, &`         | 连接约束     | `?color&brown\|black` |
| `:, >, <`          | 谓词函数     | `?age&:(> ?age 18)`   |
| `==`               | 返回值比较   | `(mod 13 4)`          |
| or, not, and, test | 条件元素     | `(forall ...)`        |

### 12.2 效率准则
1. **最具体的模式放在最前面**
2. **匹配易变事实的模式放在最后**
3. **匹配最少事实的模式放在最前面**
4. **限制多字段通配符和变量的数量**
5. **test 条件元素尽量放在规则顶部**
6. **使用内置模式匹配约束替代等价表达式**
7. **减少事实数量，按需加载**

---

## 十三、多字段通配符的组合爆炸

**问题**：`(list (items $?a $?b $?c))` 匹配 7 个字段的事实时，有 15 种切分方式：

| \$?a | \$?b | \$?c |
|-----|-----|-----|
| 空 | 空 | 全部 |
| 空 | 1个 | 剩余 |
| ... | ... | ... |

**n 个字段的切分数**：$C(n+2, 2) = (n+2)(n+1)/2$

---

## 十四、salience（优先级）

### 14.1 语法
```lisp
(declare (salience <integer>))
```

### 14.2 范围
- **-10,000 到 10,000**，默认为 0
- **数值越大，优先级越高**

### 14.3 错误用法示例
```lisp
(defrule situation-emergency
  (declare (salience 10))
  (situation emergency)
  => (assert (action emergency)))

(defrule situation-important
  (declare (salience 5))
  (situation important)
  => (assert (action important)))

(defrule situation-normal
  (declare (salience 0))
  (situation normal)
  => (assert (action normal)))
```

### 14.4 正确做法（通过模式匹配消除紧耦合）
```lisp
(defrule situation-emergency
  (situation emergency)
  => (assert (action emergency)))

(defrule situation-important
  (situation important)
  (not (situation emergency))
  => (assert (action important)))

(defrule situation-normal
  (situation normal)
  (not (situation emergency))
  (not (situation important))
  => (assert (action normal)))
```

---

## 十五、控制模式 (Control Pattern)

### 15.1 阶段控制
```lisp
(defrule detection-rule
  (phase detection)
  <patterns>*
  =>
  <actions>*)

(defrule isolation-rule
  (phase isolation)
  <patterns>*
  =>
  <actions>*)

(defrule recovery-rule
  (phase recovery)
  <patterns>*
  =>
  <actions>*)
```

### 15.2 defmodule（模块定义）
```lisp
(defmodule <module-name> [<comment>])
```

### 15.3 模块命令
| 命令                     | 说明         |
| ------------------------ | ------------ |
| `(get-current-module)`   | 获取当前模块 |
| `(set-current-module)`   | 设置当前模块 |
| `(focus <module-name>+)` | 聚焦模块     |
| `(list-focus-stack)`     | 列出焦点栈   |
| `(clear-focus-stack)`    | 清空焦点栈   |
| `(pop-focus)`            | 弹出焦点     |
| `(get-focus)`            | 获取焦点     |

### 15.4 模块间切换示例
```lisp
(defmodule DETECTION)
(defmodule ISOLATION)
(defmodule RECOVERY)

(defacts MAIN::control-information
  (phase-sequence DETECTION ISOLATION RECOVERY))

(defrule MAIN::change-phase
  ?list <- (phase-sequence ?next-phase $?other-phases)
  =>
  (focus ?next-phase)
  (retract ?list)
  (assert (phase-sequence ?other-phases ?next-phase)))
```

### 15.5 导入导出
```lisp
(export deftemplate fault)
(import DETECTION deftemplate fault)
```

---

## 十六、特殊规则与事实

### 16.1 initial-fact
- 系统启动时自动断言的特殊事实
- 可用于触发无条件规则

```lisp
(defrule <rule-name>
  (not (<pattern>))   ; 可选
  <patterns>*
  =>
  <actions>*)

; 无条件触发规则
(defrule <rule-name>
  =>
  <actions>*)
```

### 16.2 事实地址
```lisp
?f1 <- (moved (name ?name) (address ?address))
?f2 <- (person (name ?name))
=> 
(retract ?f1)
(modify ?f2 (address ?address))
```

---

## 十七、过程性函数

### 17.1 if 函数
```lisp
(if <predicate-expression>
  then <expression>+
  [else <expression>+])
```

### 17.2 while 函数
```lisp
(while <predicate-expression> [do] <expression>+)
```

### 17.3 示例
```lisp
(defrule continue-check
  ?phase <- (phase check-continue)
  =>
  (retract ?phase)
  (printout t "Continue? ")
  (bind ?answer (read))
  (while (and (neq ?answer yes) (neq ?answer no))
    do (printout t "Continue? ")
       (bind ?answer (read)))
  (if (eq ?answer yes)
    then (assert (phase continue))
    else (halt)))
```

### 17.4 关键函数
| 函数                   | 说明         |
| ---------------------- | ------------ |
| `(bind <var> <value>)` | 绑定变量     |
| `(eq <x> <y>)`         | 相等判断     |
| `(neq <x> <y>)`        | 不等判断     |
| `(read)`               | 读取输入     |
| `(readline)`           | 读取一行输入 |
| `(gensym*)`            | 生成唯一符号 |

---

## 十八、监控问题 (Monitoring Problem)

### 18.1 传感器参数表
| 传感器 | 低红线 | 低警戒线 | 高警戒线 | 高红线 |
| ------ | ------ | -------- | -------- | ------ |
| S1     | 60     | 70       | 120      | 130    |
| S2     | 20     | 40       | 160      | 180    |
| S3     | 60     | 70       | 120      | 130    |
| S4     | 60     | 70       | 120      | 130    |
| S5     | 65     | 70       | 120      | 125    |
| S6     | 110    | 115      | 125      | 130    |

### 18.2 监控规则
| 条件                  | 动作                         |
| --------------------- | ---------------------------- |
| ≤ 低红线 或 ≥ 高红线  | 立即关闭设备                 |
| 低红线 < x ≤ 低警戒线 | 发出警告，持续指定周期后关闭 |
| 高警戒线 ≤ x < 高红线 | 发出警告，持续指定周期后关闭 |

### 18.3 多阶段模块架构
```
MAIN → (focus INPUT TRENDS WARNINGS)
├── INPUT: 读取传感器数据
├── TRENDS: 状态判断与趋势跟踪
└── WARNINGS: 警告与决策
```

### 18.4 关键规则

**状态判断**（正常状态）：
```lisp
(defrule TRENDS::Normal-State
  ?s <- (sensor (raw-value ?raw-value&~none)
                (low-guard-line ?lgl)
                (high-guard-line ?hgl))
  (test (and (> ?raw-value ?lgl) (< ?raw-value ?hgl)))
  =>
  (modify ?s (state normal) (raw-value none)))
```

**状态变化跟踪**：
```lisp
(defrule TRENDS::State-Has-Changed
  (cycle ?time)
  ?trend <- (sensor-trend (name ?sensor) (state ?state) (end ?end-cycle&~?time))
  (sensor (name ?sensor) (state ?new-state&~?state) (raw-value none))
  =>
  (modify ?trend (start ?time) (end ?time) (state ?new-state)))
```

**红线区域关闭**：
```lisp
(defrule WARNINGS::Shutdown-In-Red-Region
  (cycle ?time)
  (sensor-trend (name ?sensor) (state ?state&high-red-line|low-red-line))
  (sensor (name ?sensor) (device ?device))
  ?on <- (device (name ?device) (status on))
  =>
  (printout t "Cycle " ?time " - Sensor " ?sensor " in " ?state crlf)
  (printout t " Shutting down device " ?device crlf)
  (modify ?on (status off)))
```

---

## 十九、决策树程序

### 19.1 数据结构
```lisp
(deftemplate node
  (slot name)
  (slot type)           ; decision 或 answer
  (slot question)
  (slot yes-node)
  (slot no-node)
  (slot answer))
```

### 19.2 交互流程
1. **初始化**：加载事实，设置 current-node
2. **询问决策节点**：打印问题，读取回答
3. **处理回答**：
   - 输入无效 → retract，重新询问
   - 回答 yes → 跳转到 yes-node
   - 回答 no → 跳转到 no-node
4. **到达答案节点**：猜测动物，询问是否正确
5. **学习**：如果猜错，插入新节点（区分新动物和旧猜测）
6. **继续或退出**

### 19.3 学习机制
```lisp
(defrule replace-answer-node
  ?phase <- (replace-answer-node ?name)
  ?data <- (node (name ?name) (type answer) (answer ?value))
  =>
  (retract ?phase)
  (printout t "What is the animal? ")
  (bind ?new-animal (read))
  (printout t "What question will distinguish " ?new-animal " from " ?value "?")
  (bind ?question (readline))
  (bind ?newnode1 (gensym*))
  (bind ?newnode2 (gensym*))
  (modify ?data (type decision) (question ?question) 
                  (yes-node ?newnode1) (no-node ?newnode2))
  (assert (node (name ?newnode1) (type answer) (answer ?new-animal)))
  (assert (node (name ?newnode2) (type answer) (answer ?value)))
  (assert (ask-try-again)))
```

---

## 二十、事实加载与保存

### 20.1 语法
```lisp
(load-facts <file-name>)
(save-facts <file-name> [<save-scope> <deftemplate-names>*])
```

### 20.2 文件格式
```lisp
(data 34)
(data 89)
; ...
```

---

## 二十一、推理引擎流程

```
while not done:
  1. Conflict Resolution: 从议程中选择最高优先级规则
  2. Act: 执行规则 RHS，移除触发的激活
  3. Match: 检查哪些规则 LHS 被满足，更新议程
  4. Check for Halt: 如果执行了 halt 或 break，停止
end-while
Accept new user command
```

---

## 二十二、易错点与考试关注点

### 22.1 常见陷阱
1. **通配符 `?` 和 `$?` 的作用域**：单字段 vs 多字段
2. **`test` 与内联约束的位置影响效率**
3. **salience 滥用导致规则紧耦合** → 用模式匹配替代
4. **forall 的等价转换** → `(not (and ...))`
5. **事实地址用于 retract/modify**
6. **模块导入导出**：`?ALL` 通配符

### 22.2 手算可能涉及的内容
- 模式匹配过程的步数计算（组合数）
- 推理引擎各阶段的具体操作
- 规则执行顺序（salience + 模式匹配）
- 决策树的手动追踪
- 监控系统中传感器状态的转换

### 22.3 需理解的古典概念
- 专家系统 vs 传统程序
- 前向链推理 (Forward Chaining)
- 冲突消解策略
- 基于规则的知识表示
- 解释生成机制

---

## 二十三、补充：课上可能延伸的内容

### 23.1 规则推理 vs 其他 AI 范式
- 与逻辑编程（Prolog）的区别
- 与框架系统 (Frame-based) 的关系
- 不确定性处理（可信度因子）

### 23.2 与机器学习的关系
- 知识获取瓶颈 (Knowledge Acquisition Bottleneck)
- 规则学习（从数据归纳规则）
- 专家系统与现代机器学习系统的互补性

### 23.3 RETE 算法
- 模式匹配优化的核心算法
- 记忆化部分匹配（β-memory, α-memory）
- 增量更新

### 23.4 事实索引与哈希
- CLIPS 底层事实组织方式
- 哈希查找 vs 线性扫描

### 23.5 CLIPS 与其他工具对比
- JESS（Java Expert System Shell）
- Drools
- OPS5（CLIPS 的前身）
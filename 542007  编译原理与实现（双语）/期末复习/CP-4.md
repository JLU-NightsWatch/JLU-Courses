# 4 自顶向下的语法分析方法

## 4.1 文法定义

- 文法 $G$ 定义为四元组 $G=(V_T,V_N,S,P)$
  - $V_T$：有限的终极符集合
  - $V_N$：有限的非终极符集合
  - $S$：开始符，$S\in V_N$
  - $P$：产生式集合，产生式形如：
    - $\alpha\to\beta$，其中 $\alpha,\beta\in(V_T\cup V_N)^*$
  - 写法约定
    - 文法只需写产生式集合
    - 相同左部产生式可以合并
- 0 型文法
  - 短语文法
  - 产生式具有形式 $\alpha\to\beta$
  - $\alpha$ 至少含一个非终极符
- 1 型文法
  - 上下文相关文法
  - 0 型文法的特例
  - 要求 $|\alpha|\le|\beta|$
- 2 型文法
  - 上下文无关文法（CFG，Context-Free Gramma）
  - 1 型文法特例
  - 要求产生式左部是一个非终极符：$A\to\beta$
- 3 型文法
  - 正则文法
  - 2 型文法特例
  - 要求产生式右部至多有两个符号，形如
    - $A\to\alpha$
    - $A\to\alpha B$
- 推导（直接推导）：$\Rightarrow$
  - 1 步或多步推导：$\Rightarrow^+$
  - 0 步或多步推导：$\Rightarrow^*$
- 句型：$S\Rightarrow^*\beta$，称符号串 $\beta$ 为 CFG 的句型
- 句子：只包含终极符的句型
- 语言：$L(G)=\{u\mid S \Rightarrow^+u,u\in V_T^*\}$，$G$ 能推导的句子的集合
- 最左（右）推导：
  - $\Rightarrow_{lm}$（$\Rightarrow_{rm}$）
  - 推导时选择的是句型中最左（右）的非终极符
- 左（右）句型：用最左（右）推导方式导出的句型
  - 右句型：规范句型
  - 每个句子都有相应的最左（右）推导，对句型不成立
- 短语：$S$ 是文法的开始符，$S\Rightarrow^*\alpha\pi\beta$
  - $S\Rightarrow^*\alpha A\beta$，$A\Rightarrow^+\pi$
  - 称 $\pi$ 是句型 $\alpha\pi\beta$ 的一个短语
- 直接短语（简单短语）
  - $S\Rightarrow^*\alpha A\beta$，$A\Rightarrow\pi$
  - 称 $\pi$ 是句型 $\alpha\pi\beta$ 的一个简单短语
- 句柄：句型最左的简单短语
- 语法分析树
  - 句型推导的一种树形表示
  - 文法 $G=(V_N,V_T,S,P)$
  - 每个结点都有 $G$ 的一个文法符号
    - 根结点标有初始符 $S$
    - 非叶节点标有非终极符
    - 叶结点标有终极符或非终极符或 $\varepsilon$
  - 若非叶结点 $A$ 有 $n$ 个子结点 $X_1,X_2,\dots,X_n$
    - 则 $G$ 有产生式 $A\to X_1 X_2\dots X_n$
- 线性推导：用 $\Rightarrow$ 推导的称作线性推导
- 树型推导：用语法分析树推导
- 线性推导指明了推导顺序，而树型推导没有
- 句型一般只有一棵语法分析树（若无二义性），而线性推导可很多
- 二义性文法：文法的某个句型有两棵不同的语法分析树
  - 等价定义：文法的某个句型有两种不同的最左（右）推导

## 4.2 文法等价变换

- 增加拓广产生式
  - 对任一文法 $G1$，可构造 $G2$ 使 $L(G1)=L(G2)$
  - $G2$ 开始符唯一且不出现于任何产生式右部
  - 设 $G1$ 开始符为 $A$，$G2$ 新增开始符 $Z$ 和产生式 $Z\to A$ 即可
- 消除空产生式
  - 对任一文法 $G1$，可构造 $G2$ 使 $L(G1)=L(G2)$
  - $G2$ 中无空产生式
  - 令 $\beta=\{A\mid A\to\varepsilon\}$
  - $\beta=\beta\cup\{A\mid A\Rightarrow^*\alpha,\alpha\in\beta^+\}$
  - 从 $G1$ 中删除所有空产生式
  - 从 $G1$ 中删除只能导出空串的非终极符的产生式
  - 对文法任意产生式 $A\to X_1X_2\dots X_n$
    - 若 $X_i\in\beta$，补充产生式 $A\to X_1\dots X_{i-1}X_{i+1}\dots X_n$
- 消除不可达产生式
  - 对任一文法 $G1$，可构造 $G2$ 使 $L(G1)=L(G2)$
  - $G2$ 中的每个非终极符必须出现在它的某个句型中
  - 令 $\beta=\{Z\}$
  - $\beta=\beta\cup\{B\mid A\to xBy\in G1,B\in V_N,A\in\beta\}$
  - 若产生式左部非终极符 $A\notin\beta$，删除以 $A$ 为左部的所有产生式
- 消除特型产生式（$A\to B$）
  - 对任一文法 $G1$，可构造 $G2$ 使 $L(G1)=L(G2)$
  - $G2$ 中无特型产生式
  - 对 $G1$ 中任意非终极符 $A$，求集合 $\beta_A$
  - $\beta_A=\{B\mid A\Rightarrow^+B,B\in V_N\}$
  - 若 $B\in\beta_A$，且 $B\to\alpha$ 是文法中的一个非特型产生式，补充产生式 $A\to\alpha$
  - 从 $G1$ 中删除所有特型产生式
  - 从 $G1$ 中删除所有不可达产生式
- 消除公共前缀
  - 公共前缀
    - $A\to\alpha\beta$，$A\to\alpha\gamma$
    - 有左公共前缀
  - $A\to\alpha\beta_1|\alpha\beta_2|\dots|\alpha\beta_n|\gamma$
  - $A\to\alpha A'|\gamma$，$A'\to\beta_1|\beta_2|\dots|\beta_n$
- 消除左递归
  - 左递归：文法含有下列产生式时
    - $A\to Aa|b$，直接左递归
    - $A\to Ba|b$，$B\to Ac|b$，间接左递归
    - 即 $A \Rightarrow^+ A\dots$
  - 直接左递归：$A\to Aa|b$
    - $A\to bA'$，$A'\to aA'|\varepsilon$
  - 间接左递归：转为直接左递归再消除

## 4.3 语法分析功能

- 错误处理

  - 报告位置
  - 修复并继续
  - 开销应不大
  - 处理策略
    - 紧急方式恢复：抛弃直至输入属于某指定同步记号集合
    - 短语级恢复：对剩余输入局部纠正
    - 出错产生式：扩充语言的文法
    - 全局纠正：作尽可能少的修改获得正确的分析树

- 自顶向下分析：重点是如何确定推导用的产生式

- 三个重要集合：First 集、Follow 集、Predict 集

- 考虑上下文无关文法 $G=(V_T,V_N,S,P)$

- First 集

  - 首符集

  - $$
    \mathrm{First}(\beta)=\{a\in V_T\mid\beta\Rightarrow^*a\dots\}\cup\begin{cases}
    \{\varepsilon\}&\beta\Rightarrow^*\varepsilon\\
    \varnothing&\mathrm{otherwise}
    \end{cases}
    $$

  - 选择产生式右部 First 集含当前输入的推导式

- Follow 集

  - 跟随符集

  - $$
    \mathrm{Follow}(A)=\{a\in V_T\mid S\Rightarrow^+\dots Aa\dots\}\cup\begin{cases}
    \{\#\}&S\Rightarrow^*\dots A\\
    \varnothing&\mathrm{otherwise}
    \end{cases}
    $$

  - 当文法中存在形如 $A\to\varepsilon$ 的产生式
  - 若当前字符属于 $\mathrm{Follow}(A)$，则用空取代 $A$

- Predict 集

  - 预测符集

  - $$
    \mathrm{Predict}(A\to\beta)=\begin{cases}
    \mathrm{First}(\beta)&\varepsilon\notin\mathrm{First}(\beta)\\
    (\mathrm{First}(\beta)-\{\varepsilon\})\cup\mathrm{Follow}(A)&\varepsilon\in\mathrm{First}(\beta)
    \end{cases}
    $$

  - 合并 First 集和 Follow 集
  - 选择 Predict 集含当前输入的产生式

- 产生式 $A\to\beta$ 被选择的条件：当前输入属于 $\mathrm{Predict}(A\to\beta)$

- 至多一个产生式被选择的条件：$\mathrm{Predict}(A\to\beta_i)\cap\mathrm{Predict}(A\to\beta_j)=\varnothing$，$i\ne j$

- 自顶向下文法分析的条件：至多一个产生式被选择

- 具有公共前缀和左递归的文法一定不满足自顶向下分析的条件

## 4.4 三个集合的求法

- 计算 $\mathrm{First}(X)$

  - 若 $X\in V_T$，则 $\mathrm{First}(X)=\{X\}$
  - 若 $X\in V_N$，则 $\mathrm{First}(X)=\{a\mid X\to a\dots,a\in V_T\}$
  - 若 $X\in V_N$，且 $X\to\varepsilon$，则 $\varepsilon\in\mathrm{First}(X)$
  - 若 $X\in V_N$，且 $X\to Y_1Y_i\dots Y_n$，$Y_1,Y_2,\dots,Y_i\in V_N$
    - $Y_1,Y_2,\dots,Y_{i-1}\Rightarrow^*\varepsilon$，则
      - $\mathrm{First}(X):=\mathrm{First}(Y_j)-\{\varepsilon\}$，$j\in[1,i-1]$
      - $\mathrm{First}(X):=\mathrm{First}(Y_i)$
    - 当 $Y_i\Rightarrow^*\varepsilon$，$i\in[1,n]$，则 $\mathrm{First}(X):=\{\varepsilon\}$

- 计算 $\mathrm{First}(\alpha)$，$\alpha=X_1X_2\dots X_n$

  - 若 $X_1,X_2,\dots X_i\Rightarrow^*\varepsilon$，$X_i\not\Rightarrow^*\varepsilon$，则
    $$
    \mathrm{First}(\alpha)=\mathrm{First}(X_i)\cup\bigcup_{j=1}^{i-1}(\mathrm{First}(X_j)-\{\varepsilon\})
    $$

  - 若 $X_i\Rightarrow^*\varepsilon$，$i\in[1,n]$，则 $\mathrm{First}(\alpha)=\bigcup\limits_{i=1}^n\mathrm{First}(X_i)$

- 计算 Follow 集

  - 对所有 $B\in V_N$，令 $\mathrm{Follow}(B)=\{\}$
  - 令 $\mathrm{Follow}(S)=\{\#\}$
  - 若有产生式 $A\to xBy$
    - 若 $\varepsilon\notin\mathrm{First}(y)$，则 $\mathrm{Follow}(B):=\mathrm{First}(y)$
    - 否则 $\mathrm{Follow}(B):=(\mathrm{First}(y)-\{\varepsilon\})\cup\mathrm{Follow}(A)$
  - 重复上一步，直至所有 $B\in V_N$ 的 $\mathrm{Follow}(B)$ 收敛

- 计算 Predict 集
  $$
  \mathrm{Predict}(A\to\beta)=\begin{cases}
  \mathrm{First}(\beta)&\varepsilon\notin\mathrm{First}(\beta)\\
  (\mathrm{First}(\beta)-\{\varepsilon\})\cup\mathrm{Follow}(A)&\varepsilon\in\mathrm{First}(\beta)
  \end{cases}
  $$

## 4.5 递归下降分析方法

- Recursive-Descent Parsing
- 对每个非终极符按其产生式结构产生相应语法分析子程序
  - 终极符产生匹配命令
  - 非终极符产生调用命令

## 4.6 $\mathrm{LL}(1)$ 分析方法

- 从左往右读输入流

- 每一步试图产生一个句子的最左推导

- 每一步推导往输入流中看一个符号

- 是 $\mathrm{LL}(k)$ 的特例

- 分析动作

  - 设 $X_1$ 为分析栈栈顶元素，$Y_1$ 为输入流当前符号
  - 替换：当 $X_1\in V_N$ 时选相应产生式右部 $\beta$ 替换 $X_1$
  - 匹配：当 $ X_1\in V_T$ 时与 $Y_1$ 匹配，成功则去掉 $X_1,Y_1$，否则报错
  - 成功：当分析栈和输入流同时为空时分析成功
  - 报错：
    -  输入流的符号不属于任何产生式的 Predict 集
    - 匹配时 $X_1\ne Y_1$
    - 最后分析栈和输入流不同时为空

- $\mathrm{LL}(1)$ 分析表的构造

  - $T:V_N\times V_T\to P\cup\{\mathrm{Error}\}$
  - 若 $t\in\mathrm{Predict}(A\to\alpha)$，则 $T(A,t)=A\to\alpha$
  - 否则 $T(A,t)=\mathrm{Error}$

- $\mathrm{LL}(1)$ 分析的驱动器

  1. 初始化：$\mathrm{Stack}=\mathrm{empty}$，$\mathrm{Push}(S)$

  2. 读下一个输入符：$\mathrm{Read}(a)$
  3. 若当前格局为 $(\mathrm{empty},\#)$，则成功结束，否则转 4
  4. 设当前格局为 $(X\dots,a\dots)$，则
     - 若 $X\in V_T$，$X=a$，则 $\mathrm{Pop}(1)$，$\mathrm{Read}(a)$，转 3
     - 若 $X\in V_T$，$X\ne a$，则 $\mathrm{Error()}$
     - 若 $X\in V_N$，则
       - 若 $T(X,a)=X\to Y_1Y_2\dots Y_n$，则 $\mathrm{Pop}(1)$，$\mathrm{Push}(Y_1Y_2\dots Y_n)$，转 3
       - 否则 $\mathrm{Error()}$


# 9 运行时存储空间的组织与管理

## 9.1 程序运行时存储分配策略

- 目标程序占用的存储空间
  - 代码空间
    - 库代码空间
    - 目标代码空间
  - 数据空间
    - 静态区空间（static space）
      - 编译时确定地址
    - 栈区空间（stack space）
      - 存放过程活动记录
    - 堆区空间（heap space）
      - 存放动态申请的数据
- 语言影响空间分配的因素
  - 是否允许递归
  - 函数结束时局部变量是否需要保持
  - 是否可以访问非局部变量
  - 函数传参方式
  - 函数是否可以作为参数传递
  - 函数的结果是否可以为函数
  - 动态的申请存储块
  - 是否显式释放存储空间
- 存储管理的分类
  - 静态存储分配策略
    - 编译时确定对象的存储位置，声明周期固定
  - 动态存储分配策略
    - 栈式：函数中无法动态申请空间
    - 堆栈混合式

## 9.2 过程活动记录的申请和释放

- 过程活动记录
  - 为管理过程、函数一次活动所需的信息
  - 目标程序在栈区中分配一段连续存储空间
  - 存放该过程的局部变量值、控制信息和寄存器内容等
  - 简称活动记录（activation record），记为 $\mathrm{AR}$
- 过程活动记录
  - 管理信息
    - 动态链指针：指向前驱过程活动记录
    - 返回指针：过程返回时对应目标代码的地址
    - 返回值：过程返回值的地址
    - 过程层数
    - 寄存器的状态
    - 活动记录大小
    - 变量访问环境
  - 形参区
  - 局部变量区
  - 临时变量区
- 管理过程活动记录
  - 栈区设立两个指针
    - $\mathrm{sp}$ 指向当前活动记录的起始位置
    - $\mathrm{top}$ 指向第一个可用存储单元
- 申请和释放
  - 遇到函数调用四元式时
    - 产生新活动记录，$\mathrm{sp=top}$，$\mathrm{top=top+size}$
    - 填写过程活动记录管理信息
  - 遇到 `return` 时
    - 恢复现场
    - 释放当前活动记录，$\mathrm{top=sp}$，$\mathrm{sp}$ 为前驱
    - 根据返回地址跳转
- 调用链
  - 过程名的序列
  - 序列头为主程序 $M$
  - $M$ 是调用链
  - 若 $(M,\dots,R)$ 是调用链，且 $R$ 中有 $S$ 的调用，则 $(M,\dots,R,S)$ 也是调用链
  - $\mathrm{CallChain}(S)=(M,\dots,R,S)$ 表示 $S$ 的调用链
    - 不唯一
    - 当前正执行 $S$
    - 前面的被中断
- 动态链
  - 对于 $\mathrm{CallChain}(S)=(M,\dots,R,S)$
  - $[\mathrm{AR}(M),\dots,\mathrm{AR}(R),\mathrm{AR}(S)]$ 为对应动态链 $\mathrm{DynamicChain}(S)$
- 变量的地址映射
  - 并列式语言
    - 只有 0 层和 1 层，用两个指针解决
    - $\mathrm{sp0}$ 指向全局变量首地址
    - $\mathrm{sp}$ 指向当前活动记录首地址
  - 嵌套式语言
    - 层数用于判断变量是否属于当前活动记录
    - 偏移是相对当前活动记录的
    - 对于抽象地址 $(L,\mathrm{off})$
      - 若 $L$ 等于当前层，说明对应变量在当前活动记录
      - 否则要找到对应变量对应的活动记录首地址
        - 为此要构造一个 $\mathrm{Display}$ 表
        - 存储每个活动记录活跃的静态外层的首地址
        - 找到 $L$ 对应的那层的首地址加上 $\mathrm{off}$

## 9.3 变量访问环境

- 活跃活动记录（live activation record）
  - 过程 $S$ 在动态链中可有多个 $\mathrm{AR}$
  - 但只有最新的 $\mathrm{AR}(S)$ 可访问，称为 $S$ 的活跃活动记录 $\mathrm{LAR}(S)$
- 过程声明链 $\mathrm{DeclaChain}$
  - $M$ 是过程声明链
  - 若 $(M,\dots,P)$ 是过程声明链且 $P$ 中有 $Q$ 的声明，则 $(M,\dots,P,Q)$ 为过程声明链
  - 记为 $\mathrm{DeclaChain}(Q)$
  - 当前变量访问环境 $\mathrm{VarVisitEnv}(\mathrm{LAR(Q)})=[\mathrm{LAR}(M),\dots,\mathrm{LAR}(P),\mathrm{LAR}(Q)]$
- 非局部变量访问的实现
  - 对于每个 $\mathrm{AR}$，只要知道 $\mathrm{VarVisitEnv}(AR)$
  - 可实现包括非局部变量在内所有变量的访问
- 计算当前过程的变量访问环境
  - 设 $[\mathrm{AR}(M),\dots,\mathrm{AR}(P),\mathrm{AR}(Q)]\in\mathrm{DynamicChain}(Q)$
  - $Q$ 的层数为 $N$
  - 则 $\mathrm{VarVisitEnv}(\mathrm{AR}(Q))=\mathrm{VarVisitEnv}(\mathrm{AR}(P))_N\oplus\mathrm{AR}(Q)$

## 9.4 变量访问环境的实现方法

- $\mathrm{Display}$ 表方法
  - 全局表法
  - 局部表法
- 静态链方法
- 局部 $\mathrm{Display}$ 表法
  - 对每个 $\mathrm{AR}$ 求出变量访问环境
  - 把它以 $\mathrm{Display}$ 表的形式保存在 $\mathrm{AR}$ 中
  - $\mathrm{Display}$ 表求法类似变量访问环境
    - $\mathrm{NewAR.Display=CurrentAR.Display}_N\oplus\mathrm{newsp}$
  - 变量的访问，对于变量 $X\ (L,\mathrm{off})$
    - 当 $L=\mathrm{CurrentAR.Level}$ 时，$\mathrm{addr}(X)=\mathrm{sp+off}$
    - 否则 $\mathrm{addr}(X)=\mathrm{CurrentAR.Display}[L]+\mathrm{off}$，即 $\mathrm{sp}+D+L+\mathrm{off}$

- 静态链方法
  - 局部 $\mathrm{Display}$ 表存在大量重复
  - 原 $\mathrm{Display}$ 表变为一个单元，称为静态单元，存放静态链指针
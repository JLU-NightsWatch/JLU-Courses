# 7 中间代码生成

## 7.1 中间代码的意义

- 一种介于源程序语言和目标语言之间的一种语言
- 形式
  - 后缀式
  - 三元式
  - 四元式
  - 抽象语法树
  - DAG
- 编译器分类
  - 无中间代码生成
    - 没有中间代码生成目标代码的额外开销
    - 可避免重复性工作，减少编译器的体积
  - 有中间代码生成
    - 前端：词法分析、语法分析、语义分析、中间代码生成
    - 后端：中间代码优化、目标代码生成
    - 编译器结构在逻辑上更简洁、清晰
    - 在中间代码一级进行优化容易实现
    - 易于实现编译器的移植，便于编译器开发

## 7.2 后缀式（postfix）中间代码

- 最简单的一种，又称逆波兰式（Reverse Polish Notation）
- 将运算对象写在前面，运算符写在后面
  - 变量次序与中缀式一致
  - 无括号
  - 运算符按计算顺序排列
  - 易于计算机处理
- 容易扩充到表达式以外的范围

## 7.3 三地址中间代码

- 三地址
  - 操作符的两个运算分量
  - 该操作的运算结果
  - 抽象地址
- 常见的中间代码有三元式和四元式两种
- 三元式
  - 编号
  - 算符 $\mathrm{op}$
  - $\mathrm{ARG1}$
  - $\mathrm{ARG2}$
- 四元式
  - 算符 $\mathrm{op}$
  - $\mathrm{ARG1}$
  - $\mathrm{ARG2}$
  - $\mathrm{RESULT}$
- 三元式，表示简单，运算结果依赖编号，移动或删除复杂，不利于优化
- 四元式，运算结果不依赖编号，便于插入、删除和移动，利于优化
- 四元式操作符
  - 算数、逻辑、关系运算符
  - I/O 操作
  - 类型转换
  - 赋值
  - 地址相加
  - 标号定义
  - 条件/无条件转义
  - 过程调用和过程入口
  - 参数传递

## 7.4 $\mathrm{LL}(1)$ 语法制导翻译技术

- 语法制导翻译技术
  - 语法制导方法：语法分析过程中，识别源程序的同时完成对应的翻译处理
  - 自顶向下、自底向上
- $\mathrm{LL}(1)$ 语法制导方法
  - 在 $\mathrm{LL}(1)$ 文法的基础上加入语义动作
  - 语义动作符可加在产生式右部的任何位置
  - 在 $\mathrm{LL}(1)$ 语法分析过程中遇到语义动作符则调用相应的语义子程序

## 7.5 $\mathrm{LR}(1)$ 语法制导方法

## 7.6 中间代码生成中的几个问题

- 语义信息的获取和保存
  - 产生中间代码：用于类型检查和类型转换
  - 产生目标代码：用作对名字进行地址分配的依据
  - 如果符号表不保存到目标代码生成阶段，将对名字进行地址分配所需信息存到中间代码中
- 四元式中操作分量或运算结果的地址可分为
  - 标号类
  - 数值类
  - 地址类：层数、偏移、访问方式
- 语义栈 $\mathrm{Sem}$
  - 中间代码生成用该栈存放运算分量和运算结果的类型和地址表示
  - 操作：$\mathrm{Push}(x)$、$\mathrm{Pop}(n)$
  - 常用的语义子程序：
    - $\mathrm{NewDir}(t)$：申请临时变量 $t$，直接寻址
    - $\mathrm{NewIndir}(t)$：申请临时变量 $t$，间接寻址
    - $\mathrm{Generate}(\omega,left,right,result)$：将一条四元式存放到中间代码区
    - $\mathrm{GenCode}(\omega)$：生成表达式中间代码，参数为操作码，运算分量在 $\mathrm{Sem}$ 的栈顶和次栈顶

## 7.7 表达式的中间代码生成

- 消除左递归
- 确定算符的操作数后 $\mathrm{GenCode}$
- 遇到常量或变量时 $\mathrm{Push}$
- 注意类型转换

## 7.8 下标变量的中间代码

- 下标变量可多维

- 第 $k$ 维下标个数称为第 $k$ 维的宽度（duration）

- 第 $k$ 维 $\prod\limits_{i=k+1}^nD_i$ 为第 $k$ 维的长度（size）

- $$
  \mathrm{Addr}(A[E_1][E_2]\dots[E_k])=\mathrm{Addr}(A)+\sum_{i=1}^k(E_k-L_k)S_k\cdot\mathrm{Size}(T)
  $$

## 7.9 赋值语句的中间代码

- 确定左侧变量地址
- 计算右侧表达式结果
- 完成赋值
- 注意类型转换

## 7.10 条件语句的中间代码生成

- 分支结构
  - 单分支（双分支的特例）
  - 双分支（条件语句）
  - 多分支（嵌套双分支）
- $\mathrm{THEN}$：相当于 $(\mathrm{JMP0,result},\_,?)$
- $\mathrm{ELSE}$：可选，相当于 $(\mathrm{JMP},\_,\_,??)$，确定 $?$
- $\mathrm{ENDIF}$：确定 $?$ 或 $??$
- $\mathrm{LL}(1)$ 文法：
  - $S\to\mathrm{if}\ E\ \mathrm{then}\ S\ \mathrm{ElsePart}$
  - $\mathrm{ElsePart}\to\mathrm{else}\ S$
  - $\mathrm{ElsePart}\to\varepsilon$
- $\mathrm{ThenIf}$：$(\mathrm{THEN,Sem[top].Form},\_,\_)$
- $\mathrm{EndIf}$：$(\mathrm{ENDIF},\_,\_,\_)$
- $\mathrm{ElseIf}$：$(\mathrm{ELSE},\_,\_,\_)$

## 7.11 `while` 语句的中间代码

- 循环结构
  - 循环表达式
  - 循环体
  - 类型
    - 当型循环
    - 直到型循环
- $\mathrm{WHILE}$：$(\mathrm{WHILE},\_,\_,\_)\to(\mathrm{LABEL,\_,\_,inL})$
- $\mathrm{DO}$：$(\mathrm{DO,result},\_,\_)\to(\mathrm{JMP0,result,\_,outL})$
- $\mathrm{ENDWHILE}$：$(\mathrm{ENDWHILE},\_,\_,\_)\to(\mathrm{JMP,\_,\_,INL})$，确定 $\mathrm{outL}$

## 7.12 `goto` 语句和标号语句

- 标号
  - 有声明：易处理
  - 无声明：
    - 定义性出现，$L_i:\ S$，$(\mathrm{LABEL,\_,\_,LL_i})$
    - 使用性出现：$\mathrm{goto}\ L_i$，$(\mathrm{JMP,\_,\_,LL_i})$
- 处理原则
  - 处理函数之前，建立数组 $\mathrm{ArrayL}$ 记录当前遇到的所有标号及其语义信息
  - $\mathrm{ArrayL}$：标号、是否定位、地址
- 使用 `goto` 语句时，查 $\mathrm{ArrayL}$
  - 没查到：
    - 产生转移地址缺失（为 $\mathrm{NULL}$）的无条件跳转四元式
    - 将标号、未定位、四元式地址添加到 $\mathrm{ArrayL}$
  - 查到：
    - 已定位：产生无条件跳转四元式
    - 未定位：产生无条件跳转四元式，地址为 $\mathrm{LL_i}$
    - 未定位的四元式的地址形成一个跳转链表，等待地址回填

## 7.13 过程调用和函数调用

- 文法
  - $\mathrm{ProcFunCall\to id(ParamList)}$
  - $\mathrm{ParamList\to\varepsilon|ExpList}$
  - $\mathrm{ExpList\to E\ NextList}$
  - $\mathrm{NextList\to\varepsilon|,ExpList}$
- 计算各实在表达式的值
- 传递给各形参：传参四元式，$(\mathrm{VarACT\ /\ ValACT,result,Off,Size})$
  - 变量或值
  - 实在表达式结果
  - 形参偏移
  - 形参类型宽度
- 转到过程和函数体执行：转子四元式，$(\mathrm{CALL},f,\mathrm{trus/false,[Result]})$
  - 形式（$\mathrm{false}$）/ 实在（$\mathrm{true}$）
  - 函数返回值
- 动作文法：$\mathrm{id\ CallHead}\ (E\ \mathrm{ActParm\dots)\ CallTail}$
  - 将函数符号表地址压入 $\mathrm{Sem}$ 栈，实参计数器清零
  - 已识别实在表达式，实参计数器加一
  - 取出 $\mathrm{id}$ 的语义信息
    - 形式 / 实在
    - 形参信息
  - 检查形、实参个数、类型
  - 产生 $\mathrm{VarACT\ /\ ValACT}$ 和 $\mathrm{CALL}$
  - 删除所占语义栈单元，函数结果压入栈

## 7.14 过程函数声明和中间代码生成

- 文法
  - $\mathrm{ProcFunDec\to\mathrm{ProcDec|FunDec}}$
  - $\mathrm{ProcDec\to Procedure\ id(ParamList)\ Declaration\ ProgramBody}$
  - $\mathrm{FunDec\to Function\ id(ParamList):\ Type\ Declaration\ ProgramBody}$

- 入口：$(\mathrm{ENTRY,id},\_,\_)$
- 出口：$(\mathrm{ENDPROC\ /\ ENDFUNC},\_,\_,\_)$
- 动作文法
  - $\mathrm{Entry}$：产生入口
  - $\mathrm{EndProc\ /\ EndFunc}$：产生出口


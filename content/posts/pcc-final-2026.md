---
title: '编译原理 | 2026 年 1 月期末试题'
date: '2026-02-21'
draft: false
categories: ["学习"]
tags: ["编译原理", "资源分享"]
layout: "docs"
url: "pcc-final-2026"
image: "/images/posts/travel/jp-1-header.jpg"
description: "清华大学计算机系 2025-2026 年秋季学期《编译原理》课程的期末试题，由 aLexe1n 个人整理，内容仅供参考。"
---

> [!ABSTRACT] 关于本文
> 本篇文章系清华大学计算机系 2025-2026 年秋季学期《编译原理》课程的期末试题。
> - 考试时间 2026 年 1 月 11 日 14:30 - 16:30
> - 全卷满分 100 分，平均分约 80 分
> - 试卷图片由 aLexe1n 二次制作，格式与排版与原卷略有出入，但内容一致
> - 由于原卷黑白打印，本试题中的代码均不进行高亮处理，烦请谅解
> 
> 本套试题由 aLexe1n 个人整理，内容仅供参考。本人不计划提供本套试卷的答案，但欢迎一切围绕题目和答案的友善讨论。

## 一、词法分析和编译基础知识（10分）

1. （2分）下列哪项工作属于编译器后端的典型任务（）
	- A. 寄存器分配
	- B. 语法分析
	- C. 语义分析
	- D. 常量折叠

2. （2分）词法分析过程中常用的单词描述工具是（）
	- A. 上下文无关文法
	- B. 正则表达式
	- C. 属性文法
	- D. 语法制导翻译

3. （2分）下面哪一项错误最可能在词法分析阶段被发现（）
	- A. 缺少分号
	- B. 使用未声明受量
	- C. 非法字符
	- D. 运算符优先级错误

4. （4分）考虑以下 TAC 代码,列出程序的所有基本块并给制对应的控制流图。

```
_Main:
	_T3 = (_TI<_T2)
	if (_T3==0) branch _L1
	return _T3
	branch _L2
L1:
	_TO = _T2
L2:
	_T1 = _T0
	return _T1
```

## 二、自底向上语法分析（17分）

设文法 $G[S]$，开始符号为 $S$，终结符为 $a,b,c,d$，输入结束符为 $\#$。产生式编号如下：

$$
\begin{aligned}
(1) && S &\to L \\
(2) && S &\to \varepsilon \\
(3) && L &\to LcR \\
(4) && L &\to R \\
(5) && R &\to aRb \\
(6) && R &\to d
\end{aligned}
$$

1. （3分）已知串 $w = adbcd$ 是该文法的句子（可认为来自某个最右推导）。请
   1. 写出 $w$ 的所有短语（如有重复出现，必须标注位置，例如用区间或下标区分方式合理即可，下同）；
   2. 写出所有直接短语；
   3. 说明该最右推导下，$w$ 的句柄是哪一段。

2. （6分）对文法作增广 $S'→S$。下图给出了 $G[S]$ 不完整的 SLR(1) 状态机，补全其中缺失的状态和转移边。（"$.$"表示分析位置）。本题可以作图在试卷上，无需抄写至答题卡。

![pcc-final-2026-t2-fig1.drawio.svg](/images/posts/2026-02/pcc-final-2026/pcc-final-2026-t2-fig1.drawio.svg)

1. （2分）该文法是否为 LR(0)？若不是，请指出至少一个发生冲突的状态，并说明冲突类型。

2. （4分）下表给出了不完整的 SLR(1) 分析表，补全分析表中缺失的表项。

![pcc-final-2026-t2-fig2.svg](/images/posts/2026-02/pcc-final-2026/pcc-final-2026-t2-fig2.svg)

5. （2分）对于输入串 $adbc$，SLR(1) 分析程序会在哪个状态报错？报错时状态栈内容是什么（从栈底到栈顶依次写出）？

## 三、翻译模式（12分）

如下是以 $G[S]$ 为基础改造的一个 L 翻译模式。

$$
\begin{aligned}
S' &\to \{ \; S.i := 0 \; \} \; S \; \{ \; \texttt{print}(S.val) \; \} \\
S &\to \{ \; B.i := 0 \; \} \; A B c \; \{ \; S.val := A.val + B.val \; \} \\
A &\to a A_{1} \; \{ \; A.val := A_{1}.val + 1 \; \} \\
A &\to \varepsilon \; \{ \; A.val := 1 \; \} \\
B &\to b \; \{ \; S.i := B.i + 10 \; \} \; S \; \{ \; B.val := S.val \; \} \\
B &\to ε \; \{ \; B.val := B.i \} \\
\end{aligned}
$$

针对该翻译模式构造的一个自上而下的递归下降（预测）翻译程序如下，其中使用了与课程中相同的 $\texttt{MatchToken}$ 函数与 $\texttt{lookahead}$ 变量，试补全程序代码。

```
int main() {
    int val = ParseS(0);
    printf("%d\n", val);
}

int ParseS(int S_i) {
    int val, A_val, B_val;
    if (lookahead == 'a' || lookahead == 'b' || lookahead == 'c') {
        A_val = ParseA();
        B_val = __(1)__
        MatchToken('c');
        val = __(2)__
    } else {
        error();
    }
    return val;
}

int ParseA() {
    int val;
    if (lookahead == 'a') {
        MatchToken('a');
        __(3)__
    } else if (__(4)__) {
        val = 1;
    } else {
        error();
    }
    return val;
}

int ParseB(int i) {
    int val;
    if (lookahead == 'b') {
        MatchToken('b');
        __(5)__
    } else if (__(6)__) {
        val = i;
    } else {
        error();
    }
    return val;
}
```


## 四、符号表和运行时存储组织（12分）

下面产生是某简单（类 Pascal）语言的一段代码。

- 语言中不包含数据类型的声明，所有变量的类型默认为整型（占用一个存储单元）。
- 语句块的括号为 $\texttt{begin}$ 和 $\texttt{end}$ 组合；赋值号为 $:=$。
- 每一个过程声明对应一个静态作用域（采用多遍扫描机制，在静态语义检查之前每个作用域中的所有表项均已生成）。
- 该语言支持嵌套的过程声明，但只能定义无参过程，且没有返回值。
- 过程活动记录中的控制信息包括静态链 SL、动态链 DL，以及返回地址 RA。
- 程序的执行遵循静态作用域规则。

```
(1)  var p, q, r;
(2)  procedure func1;
(3)      var a, b;
(4)      procedure func2;
(5)          var p;
(6)          procedure func3;
(7)              var x;
(8)              begin
(9)                  x := p + q;
(10)             end;
(11)         begin
(12)             p := a;
(13)             if (p > q) then call func4;
(14)             call func3;
(15)         end;
(16)     begin
(17)         a := p + 3;
(18)         b := q * 2;
(19)         call func2;
(20)     end;
(21) procedure func4;
(22)     begin
(23)         r := r + 1;
(24)         if (r < 2) then call func1;
(25)     end;
(26) begin
(27)     p := 2;
(28)     q := 2;
(29)     r := 0;
(30)     call func1;
(31) end;
```

1.  （4分）若实现该语言时采用多符号表结构，每个静态作用域均对应一个符号表（静态分析前已经创建完毕，作用域内全部符号已在表中）。试指出，在分析至语句 $\texttt{(13)}$ 时，当前开作用域和闭作用域有哪些？以及这些作用域分别包含哪些符号？

2.  （4分）当左侧程序执行到过程 $\texttt{func3}$ 被激活时，运行栈的当前状态如下图所示（栈寄存器指向单元 34），其中变量的名字用于代表相应的值。试补齐该运行状态下，单元 11、12、15、16、18、19、23、24、27、28 中的内容。

![pcc-final-2026-t4-fig.svg](/images/posts/2026-02/pcc-final-2026/pcc-final-2026-t4-fig.svg)

3. （4分）采用静态链实现非局部量访问，需要沿静态链进行多次访存操作，采用全局 Display 表，可以提高非局部量访问的效率。Display 表记录各嵌套层当前过程的活动记录在运行栈上的起始位置（基地址）。若当前激活的过程的层次为 $k$（最外的层次设为 0），则对应的 Display 表含有 $k+1$ 个单元，依次存放着当前层，直接外层，…，直至最外层的每一过程的最新活动记录的基地址。在过程被调用和返回时，需要对 Display 表进行维护，涉及到 Display 表项 $D[i]$ 的保存和恢复。一种方法是在活动记录保存的一个 Display 表项，而在静态存储区或专用寄存器中维护一个全局 Display 表。如果一个处于第 $k$ 层的过程被调用，则只需要在该过程的活动记录中保存 $D[k]$ 先前的值；如果 $D[k]$ 先前没有定义，那么我们用 “$-$” 代替。现在采用 Display 表来代替静态链，假设采用只在活动记录保存一个 Display 表项的方法，且该表项占据图中 SL 的位置。请指出当前运行状态下 Display 表的内容以及右侧运行栈中所有 SL 位置的新内容。

## 五、中间代码生成（14分）

下面是函数 $\texttt{func}$ 的中间代码及其流图，每个基本块的编号示于其左上方。$\texttt{t0}$表示函数接受的参数。

![pcc-final-2026-t5-fig.drawio.svg](/images/posts/2026-02/pcc-final-2026/pcc-final-2026-t5-fig.drawio.svg)

1. （3分）写出流图中的回边及其对应的自然循环。回边用 $B_i→B_j$ 表示，自然循环用基本块的集合表示。

2. （2分）假设没有寄存器溢出到栈上，每个临时变量始终用同一个物理寄存器存储。则 $\texttt{t3}$ 与 $\texttt{t5}$ 是否可以分配同一个物理寄存器？$\texttt{t7}$ 与 $\texttt{t9}$ 是否可以分配同一个物理寄存器？

3. （4分）写出 $B_3$ 的 $\textbf{LiveIn}$ 和 $\textbf{LiveOut}$ 集合，注意数组的基地址 $\texttt{A}$ 和 $\texttt{B}$ 也应该看作一个变量，应该在计算 $\textbf{LiveIn}$ 和 $\textbf{LiveOut}$ 时考虑。

4. （2分）给出 $B_3$ 计算过程的 DAG，叶子节点中 $t_i$ 的初始值用 $a_i$ 表示，将每个出现在赋值等式左端的临时变量标注在 DAG 内部节点上，不需要标注 $\texttt{A[t4]}$ 和 $\texttt{B[t4]}$。

5. （3分）$B_3$ 中使用了很多既不在 $\textbf{LiveIn}$ 也不在 $\textbf{LiveOut}$ 中的临时变量，我们称之为基本块的"局部变量"。结合 DAG 优化 $B_3$ 的代码，在不改变对 $\textbf{LiveOut}$ 中变量和数组元素赋值结果的情况下，使其代码行数不超过 9 行，使用的"局部变量"不超过 3 个。

注：你使用的每一行代码都应是以下形式之一：
- $\texttt{tmp = tmp op imm}$
- $\texttt{tmp = tmp op tmp}$
- $\texttt{tmp = tmp}$
- $\texttt{arr[tmp] = tmp}$
- $\texttt{goto linecode}$

其中 $\texttt{tmp}$ 表示临时变量，$\texttt{imm}$ 表示立即数，$\texttt{op}$ 表示二元运算符，$\texttt{arr}$ 表示数组标识符，$\texttt{linecode}$ 表示行号。


## 六、自顶向下语法分析（20分）

给定文法 $G[S]$。其中，$S, A, B$ 为非终结符，$a, b, c$ 为终结符，开始符号为 $S$。

$$
\begin{aligned}
S &\to ABc \\
A &\to aA \mid \varepsilon \\
B &\to b S \mid \varepsilon
\end{aligned}
$$

1.  （7分）补充文法 $G[S]$ 对应的各个产生式右部符号串的 $\mathrm{First}$ 集合、各产生式左部非终结符的 $\mathrm{Follow}$ 集合以及各产生式的预测集合 $\mathrm{PS}$，即完成下表（列出每个集合的元素即可）。表中的 $\mathrm{rhs}(r)$ 表示产生式 $r$ 右部的文法符号串，$\mathrm{lhs}(r)$ 表示产生式 $r$ 左部的非终结符。

| $G[S]$ 中的规则 $r$             | $\mathrm{First(rhs}(r))$ | $\mathrm{Follow(lhs}(r))$ | $\mathrm{PS}(r)$ |
| :-------------------------- | :----------------------- | :------------------------ | :--------------- |
| $S \rightarrow A B c$       |                          |                           |                  |
| $A \rightarrow a A$         |                          |                           |                  |
| $A \rightarrow \varepsilon$ |                          | 此处不填                      |                  |
| $B \rightarrow b S$         | b                        |                           |                  |
| $B \rightarrow \varepsilon$ |                          | 此处不填                      |                  |

2.  （5分）请完成文法 $G[S]$ 的预测分析表，其中部分内容已填好。

|     | $a$                   | $b$                   | $c$                   | $\#$ |
| :-- | :-------------------- | :-------------------- | :-------------------- | :--- |
| $S$ | $S \rightarrow A B c$ | $S \rightarrow A B c$ | $S \rightarrow A B c$ |      |
| $A$ |                       |                       |                       |      |
| $B$ |                       |                       |                       |      |

3.  （2分）判断该文法是否为 LL(1) 文法。若是，请说明为什么；若否，请说明在何处出现了冲突。

4.  （6分）针对输入串 $abacc$，基于上述预测分析表的一个分析过程如下，补全表中缺失部分。

| 步骤  | 下推栈      |     余留符号串 | 下一步动作                          |
| :-- | :------- | --------: | :----------------------------- |
| 1   | $\#S$    | $abacc\#$ | 应用 $S \rightarrow ABc$         |
| 2   | $\#cBA$  | $abacc\#$ | 应用 $A \rightarrow aA$          |
| 3   | $\#cBAa$ | $abacc\#$ | 匹配符号                           |
| 4   | $\#cBA$  |  $bacc\#$ |                                |
| 5   | $\#cB$   |  $bacc\#$ | 应用 $B \rightarrow bS$          |
| 6   | $\#cSb$  |  $bacc\#$ | 匹配符号                           |
| 7   | $\#cS$   |   $acc\#$ |                                |
| 8   |          |   $acc\#$ |                                |
| 9   |          |   $acc\#$ | 匹配符号                           |
| 10  | $\#ccBA$ |           | 应用 $A \rightarrow \varepsilon$ |
| 11  | $\#ccB$  |    $cc\#$ | 应用 $B \rightarrow \varepsilon$ |
| 12  | $\#cc$   |    $cc\#$ | 匹配符号                           |
| 13  | $\#c$    |     $c\#$ | 匹配符号                           |
| 14  | $\#$     |      $\#$ | 结束                             |


## 七、静态语义分析和中间代码生成（15分）

LLVM 是一个模块化编译器框架。前端会对高级编程语言源码（如 C、C++、Rust 等）进行词法和语法分析，生成 LLVM IR（LLVM 中间表示），并发送给 LLVM 后端，生成面向不同架构（如 x86、RISCV 等）的机器码。其中，前端生成 LLVM IR 的过程类似我们课堂上学过的中间代码生成。在本题中，我们使用简化的生成接口：

- 以 $\texttt{\%}$ 开头的标识符（如 $\texttt{\%i1, \%t1}$）是临时寄存器。函数 $\texttt{new\_temp()}$ 会自动生成一个新的、不重复的临时寄存器名。
- 所有数据访问需要指明类型，如 $\texttt{i1}$ 表示 1 bit 整型（即布尔）、$\texttt{i32}$ 表示 32bit 整型
- 辅助函数 $\texttt{gen()}$, $\texttt{new\_temp()}$, $\texttt{new\_label()}$ 的功能与课程定义一致。
- $\texttt{S.code}$ 代表 $S$ 生成的中间代码字符串。
- 本题可能用到的 LLVM IR 指令如下表所示，其中 $\texttt{\%a}$, $\texttt{\%b}$, $\texttt{\%r}$, $\texttt{\%ptr}$ 为临时变量名，$\texttt{T}$, $\texttt{ST}$, $\texttt{TT}$ 为类型名，$\texttt{T*}$ 为指向 $\texttt{T}$ 类型数据的指针，$\texttt{\%L}$, $\texttt{\%L1}$, $\texttt{\%L2}$ 为跳转标签，$\texttt{idx}$ 为立即数。这些可以修改，其余内容直接照抄即可。

| 指令格式                                                | 作用                                                                           |
| :-------------------------------------------------- | :--------------------------------------------------------------------------- |
| `%r = add i32 %a, %b`                               | 整数加法。                                                                        |
| `%r = alloca T`                                     | 在栈上分配类型 $\texttt{T}$ 的空间，返回指针 $\texttt{T*}$ 。                                |
| `store T %v, T* %ptr`                               | 将值 $\texttt{\%v}$ 存入地址 $\texttt{\%ptr}$。                                     |
| `%r = load T, T* %ptr`                              | 从地址 $\texttt{\%ptr}$ 读取值。                                                    |
| `%r = icmp eq T %a, %b`                             | 比较 $\texttt{\%a}$, $\texttt{\%b}$ 是否相等，返回布尔值（$\texttt{i1}$）。                 |
| `br i1 %cond, label %L1, label %L2`                 | 条件跳转，当 $\texttt{\%cond}$ 为 1 时跳转到 $\texttt{\%L1}$，为 0 时跳转到 $\texttt{\%L2}$。  |
| `br label %L`                                       | 无条件跳转。                                                                       |
| `switch T %val, label %def [ T c1, label %L1 ... ]` | 多路分支跳转。如果 $\texttt{\%val == c1}$ 跳转到 $\texttt{\%L1}$，否则跳转到 $\texttt{\%def}$。 |
| `%r = getelementptr T, T* %ptr, i32 0, i32 idx`     | 计算结构体第 $\texttt{idx}$ 个（从 0 开始）成员地址。假设结果类型为成员指针。                             |
| `%r = bitcast ST %val to TT`                        | 将 $\texttt{ST}$ 类型的 $\texttt{\%val}$ 强制转换为 $\texttt{TT}$ 类型的 $\texttt{\%r}$  |

Rust 是一款注重内存安全与高性能的系统级编程语言，它同样使用 LLVM 作为编译后端。

1. （8分） 我们考虑 Rust 中的 $\mathrm{match}$ 语法，它的基本功能是通过匹配返回一个值（注意，与 C++ 不同，它是一个有返回值的表达式）（这里的大括号是语法的一部分）。
$$
E \to \text{match} \; E_{cond} \; \{  \; P_1 \Rightarrow E_1, \_ \Rightarrow E_{default} \; \}
$$
它的语义是：计算 $E_{cond}$，如果 $E_{cond}$ 的值等于 $P_1$，那么执行 $E_1$，并将结果作为整个表达式的值；否则执行 $E_{default}$，并将结果作为整个表达式的值。$E$、$E_1$ 和 $E_{default}$ 的类型相同。

补全下面的 LLVM IR 生成规则。其中 $\texttt{result\_ptr}$ 用来存放 $\mathrm{match}$ 表达式最终的返回值地址。类型检查等代码省略，默认已经计算完成。

```
E -> match E_cond { P1 => E1, _ => E_def } {
    E.addr := new_temp();
    cond_val := new_temp();
    val1 := new_temp();
    val_def := new_temp();
    result_ptr := E.addr;
    gen(E.addr " = alloca " E.type);  // 分配栈空间用于存储最终结果

    L_check := new_label();  // 检查条件
    L_case1 := new_label();  // 匹配 P1 的分支
    L_def   := new_label();  // 默认分支
    L_end   := new_label();  // 结束合并点

    _____(1)_____; // 为条件值 cond_val 生成临时寄存器
    
    E.code := E_cond.code ||
              gen(cond_val " = load " E_cond.type ", " E_cond.addr) ||
              _____(2)_____; // 提示：此处 P1.value 已经计算完成

    E.code := E.code || gen(L_case1 ":") ||
              E1.code ||
              gen(val1 " = load " E1.type ", " E1.addr) ||
              _____(3)_____ ||
              gen("br label " L_end);

    E.code := E.code || gen(L_def ":") ||
              E_def.code ||
              gen(val_def " = load " E_def.type ", " E_def.addr) ||
              gen("store " E_def.type " " val_def ", " E_def.type " * " result_ptr) ||
              _____(4)_____;

    E.code := E.code || gen(L_end ":");
}
```

2. （4分） 在 Rust 中，枚举类型可以包含每种类别独有的信息，假设有一个名为 $\texttt{Message}$ 的枚举类型，当其为 $\texttt{Empty}$ 是没有附加信息，但当其为 $\texttt{Post}$ 时可以附带两个 $\texttt{i32}$ 类型数据。

```rust
enum Message {
    Empty,
    Post(i32, i32),
}
```

其内存布局等同于如下结构体（Tagged Union）：

```c
struct Message {
    int tag;    // 0 代表 Empty, 1 代表 Post
    union {
        int dummy;      // Empty 变体没有数据
        struct { int x; int y; } post_data; // Post 变体包含 x, y
    } payload;
};
```

前文提到的 $\mathrm{match}$ 语法常被用来进行枚举类型解包。为了简洁起见，我们单独定义一条语法（在真实编译器中，类型和地址信息都是自动推导的）（这里的大括号是语法的一部分）：
$$
E \to \text{match } E_{cond} \; \{ \; \text{Post } \{ id_1, id_2 \} \Rightarrow E_1, \_ \Rightarrow E_{default} \; \}
$$
	
假设：
- $\texttt{E\_cond.addr}$ 指向 struct $\texttt{Message}$ 类型的数据。
- struct $\texttt{Message}$ 在 LLVM 中对应类型 $\texttt{\%Message}$。
- $\texttt{tag}$ 字段是结构体的第 0 个成员，$\texttt{payload}$ 是第 1 个成员。
- $\texttt{post\_data}$ 结构体（包含 x, y）在 LLVM 中对应类型 $\texttt{\%PostData}$。

请你补全用于处理 Message::Post 分支的代码生成逻辑。

```
E -> match E_cond { Post { id1, id2 } => E1, _ => E_def } {
    // ... 前略：结果分配、E_cond 代码生成 ...

    tag_ptr := new_temp();
    tag_val := new_temp();
    E.code := E.code ||
              _____(5)_____ || // 获取 tag 字段的地址（tag 是结构体第 0 个成员）
              gen(tag_val " = load i32, i32* " tag_ptr) ||
              gen("switch i32 " tag_val ", label " L_def " [ i32 1, label " L_post " ]");

    payload_base := new_temp();
    post_ptr := new_temp();
    
    E.code := E.code ||
              gen(L_post ":") ||
              gen(payload_base " = getelementptr %Message, %Message* " E_cond.addr ", i32 0, i32 1") ||
              gen(post_ptr " = bitcast ... " payload_base " to %PostData*");

    x_ptr := new_temp();
    y_ptr := new_temp();
    E.code := E.code ||
              _____(6)_____ || // 计算 x 的地址
              gen(y_ptr " = getelementptr %PostData, %PostData* " post_ptr ", i32 0, i32 1");

    E.code := E.code || E1.code;

    // ... 后略：存储结果并跳转 ...
}
```

3. （3分） 现代编译器通常会直接生成 SSA（静态单赋值）形式的代码，以便提升数据流优化效果。这导致两个不同的基本块无法同时对同一变量进行赋值。LLVM IR 中提供了一条特殊指令（$\texttt{phi}$）用于合并来自不同基本块的值。

在这种模式下，$\mathrm{match}$ 表达式的最终值需要通过 Phi 节点 ($\texttt{phi}$) 在汇合块 $\texttt{L\_end}$ 中合并。

| 指令格式                                    | 作用                                                                                                                                                                    |
| :-------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `%r = phi T [ %v1, %L1 ], [ %v2, %L2 ]` | 如果当前控制流是从基本块 $\texttt{\%L1}$ 跳转过来的，则 $\texttt{\%r}$ 取值 $\texttt{\%v1}$；如果是从 $\texttt{\%L2}$ 跳转过来的，则取值 $\texttt{\%v2}$。$\texttt{\%L1}$ 和 $\texttt{\%L2}$ 都是当前基本块的直接前驱。 |

假设编译器引入了一个辅助函数 $\texttt{get\_current\_label()}$，用于获取当前正在生成指令的基本块标签。请重构代码生成逻辑，移除 $\texttt{result\_ptr}$，并在 $\texttt{L\_end}$ 处使用 $\texttt{phi}$ 指令合并结果。（注：$E_1$ 和 $E_{default}$ 的代码生成过程可能会产生新的基本块，因此它们结束时的位置是不确定的。）

```
E -> match E_cond { Move { id1, id2 } => E1, _ => E_def } {
    // ... (前序代码：E_cond 计算，Switch 跳转等，与第二问相同) ...
    E.code := E.code || gen(L_move ":");
    // ... (解构绑定 id1, id2 的代码，与第二问相同) ...

    // 生成 E1 的代码，假设 E1.result 存放了 E1 计算后的寄存器值（如 x+y 的结果）
    E.code := E.code || E1.code || gen("br label " L_end);
    _____(7)_____ || // 记录 Move 分支结束时的基本块标签，作为 Phi 的前驱

    E.code := E.code || gen(L_def ":") || E_def.code;
    L_def_end := get_current_label();
    E.code := E.code || gen("br label " L_end) || gen(L_end ":");

    E.addr := new_temp(); // 此时 E.addr 是一个寄存器，存放最终值
    E.code := E.code || _____(8)_____; // 生成 phi 指令
}
```
---
title: '编译原理 | 2026 年 1 月期末试题'
date: '2026-02-21'
draft: false
categories: ["学习"]
tags: ["编译原理", "资源分享"]
layout: "docs"
url: "pcc-final-2026"
image: "/images/posts/travel/jp-1-header.jpg"
description: "清华大学计算机系《编译原理》课程在 2025-2026 年秋季学期的期末试题。满分 100 分，考试时间 2026 年 1 月 11 日 14:30 - 16:30。个人整理，内容仅供参考。"
---

> [!ABSTRACT] 关于本文
> 本篇文章系清华大学计算机系《编译原理》课程在 2025-2026 年秋季学期的期末试题。全卷满分 100 分，考试时间 2026 年 1 月 11 日 14:30 - 16:30。由 aLexe1n 个人整理，内容仅供参考。（更新未完成）

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
	A. 缺少分号
	B. 使用未声明受量
	C. 非法字符
	D. 运算符优先级错误

4. （4分）考虑以下 TAC 代码,列出程序的所有基本块并给制对应的控制流图。
```c
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

## 二、自顶向下分析（17分）

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

```c
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

```pascal
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
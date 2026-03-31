# On the key dependency

> From [Mind Your Path: On (Key) Dependencies in  Differential Characteristics](https://eprint.iacr.org/2022/1734) (Best Paper on FSE 2023)



## Motivation

1. 目前对分组密码的分析大多基于两个假设:

   * **Markov cipher assumption,** 即轮与轮之间没有关系;
   * 由主密钥产生的**轮/子密钥是相互独立**的.

   但在实际中, 这种假设不成立, 因为*密钥往往是固定的 (fixed key)*

   $\Rightarrow$ 研究固定密钥下的差分特征是否成立.

2. 在 Joan Daemen and Vincent Rijmen. [Plateau characteristics](https://cs.ru.nl/~joan/papers/JDA_VRI_Plateau_2007.pdf). (IET) 中提出了 **plateau characteristics**:

   对一个 fixed key, 一条差分特征的概率为 $p=0$ or $p\neq 0$. 该特征是由简单的**线性层关系**推导得来.

   $\Rightarrow$

   * *非线性层* 能不能用于检验差分特征?
   * $p$ 能否*介于* $0,p$ *之间* ?
   * *线性层的强度* 和 plateau characteristics 之间的关系 (具体).



## Basic Definitions



**一轮差分概率**: 设 $\Delta_{in}, \Delta_{out}$ 分别为一轮的输入, 输出差分, 有:


$$
\mathbb{P}(\Delta_{in} \to \Delta_{out}) = \frac{\textsf{No.}[F(x) \oplus F(x \oplus \Delta_{in}) = \Delta_{out}]{2^n}
$$




## Linear Constraint

### Simple Linear Constraint



**例子 (SKINNY):**

### High-Order Linear Constraints:

对线性层，有自然的约束 (如图中所示), 

**其中** $x$ 表示 $y_{DDT}(column)$, 即前一半的半约束, $R.0$ 的 Sbox 之后值; $y$ 表示 $x_{DDT}(column)$, $R.1$ 的 Sbox 之前值.
$$
\begin{aligned}
y_0 =& x_0 \oplus x_2 \oplus x_3 \oplus k_0\\
y_1 =& x_0 \oplus k_0\\
y_2 =& x_1 \oplus x_2 \oplus k_1\\
y_3 =& x_0 \oplus x_2 \oplus k_0
\end{aligned}
$$

SKINNY 的这种情况是特例. 对其他 *有更复杂/充分的 Key Addition* 的分组密码, 可能线性层输出对应多个 Keys (进而形成高阶方程). 可以将这些方程组合, 可能产生高阶方程 (也可能抵消密钥变成 0 阶). 所以高阶方程的形成有两种途径:
1. 对 Key Addition 充分的分组密码, 可求 输出 对应于 输入 的方程,直接获得.
2. 对所有分组密码, 将 输入-输出 方程组合.

对方法 2. ,上述例子的组合为 (后标**阶数**):

$$
\begin{alignat*}{7}
& y_0 \oplus  y_1 &=& x_2 \oplus x_3 &\quad  & & \qquad(0)\\
& y_0 \oplus  y_2    &=& x_0 \oplus x_1 \oplus x_3 \oplus &\quad  & k_0 \oplus k_1 & \qquad(2)\\
& y_0 \oplus   y_3   &=& x_3 &\quad  & & \qquad(0) \\
&  y_1 \oplus  y_2   &=& x_0 \oplus x_1 \oplus x_2 \oplus & & k_0 \oplus k_1 & \qquad(2) \\
&  y_1 \oplus  y_3   &=& x_2 &\quad  & & \qquad(0) \\
&   y_2 \oplus  y_3  &=& x_0 \oplus x_1 \oplus &\quad & k_0 \oplus k_1 & \qquad(2)\\
& y_0 \oplus y_1 \oplus  y_2    &=& x_1 \oplus x_3 \oplus &\quad  & k_1 & \qquad(1)\\
& y_0 \oplus y_1 \oplus   y_3   &=& x_0 \oplus x_3 \oplus &\quad  & k_0 & \qquad(1)\\
& y_0 \oplus  y_2 \oplus  y_3   &=& x_1 \oplus x_2 \oplus x_3 \oplus &\quad  & k_1 & \qquad(1)\\
&  y_1 \oplus y_2 \oplus  y_3   &=& x_1 \oplus &\quad  & k_1 & \qquad(1)\\
& y_0 \oplus y_1 \oplus y_2 \oplus  y_3    &=& x_0 \oplus x_1 \oplus x_2 \oplus x_3 \oplus &\quad & k_0 \oplus k_1 & \qquad(2)
\end{alignat*}
$$

**阶数的作用:**

* (0) 直接判决差分特征 **无效**
* (1,2) 
  * 判决差分特征 **无效** (无有效密钥空间)
  * 将攻击变为 **弱密钥** 攻击

以上条件均可依 SAT 逻辑建模, 但其中可能有冗余条件, 表现为 

1. 得出的集合相等
2. 已被判定为 不可能传播, 仍继续判定
3. 部分变量不涉及半约束

下面是文中对单个 Sbox 进行判定的过程：

<img alt="keydependencySKINNY" src="https://github.com/user-attachments/assets/c537ab66-5df6-4ac8-911f-e66089c29ca6" />

把 ART+SR+MC 当作整体, 记图中 $\textcolor{green}{绿圈 = HL_i}, \textcolor{purple}{紫圈=HL_o}$.

因为 $HL_i$ 所在一列仅有其自身活跃, 所以该列只有这一个 半约束 , 所以涉及到上面式子中的 $x_0, x_1, x_3$ 均为冗余约束, 唯一有效约束为仅包含 $x_2$ 的约束, 为 (三行等价):

$$
\begin{aligned}
& y_1\oplus y_3 = x_0 \oplus k_0 \oplus x_0 \oplus x_2 \oplus k_0 = x_2\\
\\
& \{x_{DDT}(0x2,0x5)=\{0x0, 0x2, 0x9, 0xb\}\} \oplus \{x_{DDT}(0xb,0xc)=\{0x6,0xd\}\} =  \{y_{DDT}(0xd,0x9)=\{0x1,0x3,0x8,0xa\}\}\\
\\
& HL_o^6\oplus HL_o^{14} = HL_i^8
\end{aligned}
$$



## Nonlinear Constraints

<img alt="keydependencySKINNY - NL drawio" src="https://github.com/user-attachments/assets/df6648e1-a06f-4039-996f-1b8da65bd045" />





### 文中例子代码 (Py)

```python


# Linear Constraint
SKINNY_SB_4 = [12,6,9,0,1,10,2,11,3,8,5,13,4,14,7,15]
Diff_d2, Diff_d9, Diff_bc, Diff_25, Diff_42 = (0xd,0x2), (0xd,0x9), (0xb,0xc), (0x2, 0x5), (0x4, 0x2)

'''
The half constraint:
* x_DDT is the -input- value of Sbox which fulfills the differential
* y_DDT is the -output- value of Sbox which fulfills the differential
'''

y_DDT_d2 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_d2[0]] == Diff_d2[1]}
y_DDT_d9 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_d9[0]] == Diff_d9[1]}
y_DDT_25 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_25[0]] == Diff_25[1]}
y_DDT_42 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_42[0]] == Diff_42[1]}

x_DDT_bc = {x for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_bc[0]] == Diff_bc[1]}
x_DDT_25 = {x for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_25[0]] == Diff_25[1]}

print("Half Constraints:")
print("y_DDT_d2 :", [f"{v:02x}" for v in sorted(y_DDT_d2)])
print("y_DDT_d9 :", [f"{v:02x}" for v in sorted(y_DDT_d9)])
print("y_DDT_25 :", [f"{v:02x}" for v in sorted(y_DDT_25)])
print("y_DDT_42 :", [f"{v:02x}" for v in sorted(y_DDT_42)])

print("x_DDT_bc :", [f"{v:02x}" for v in sorted(x_DDT_bc)])
print("x_DDT_25 :", [f"{v:02x}" for v in sorted(x_DDT_25)])

# ============================================================================================================
# Simple Linear Constraints (partial, mark as position)
subkey_02_C1 = {(v1 ^ v2 ^ 0x2 ^ v3) for v1 in y_DDT_d2 for v2 in y_DDT_d9 for v3 in x_DDT_bc} & set(range(16))
subkey_02_C2 = {(v1 ^ 0x2 ^ v2) for v1 in y_DDT_d2 for v2 in x_DDT_25} & set(range(16))

print("\n---Simple Linear---")
print("subkey_02_C1:", [f"{k:02x}" for k in sorted(subkey_02_C1)])
print("subkey_02_C2:", [f"{k:02x}" for k in sorted(subkey_02_C2)])

inter_keyC = subkey_02_C1.intersection(subkey_02_C2)

print("intersection of C1 and C2:",inter_keyC, "\nportion:", len(inter_keyC)/(2**4))

# ============================================================================================================
# High-order Constraints (partial, mark as position)
charset = {(v1, v2, v3) for v1 in x_DDT_25 for v2 in x_DDT_bc for v3 in y_DDT_d9 if v1 ^ v2 ^ v3 == 0}
print("\n---High-order Linear---\nchar. valid in:", [f"({a:02x},{b:02x},{c:02x})" for a,b,c in sorted(charset)])


# ============================================================================================================
# Nonlinear Constraint
keyset2 = [[set() for _ in range(16)] for _ in range(16)]

v_d = {(v1 ^ v2) for v1 in y_DDT_25 for v2 in y_DDT_25}
v_e = {(v1 ^ v2) for v1 in y_DDT_42 for v2 in x_DDT_25}

N = len(v_d) * len(v_e)

for k1 in range(16):
    for k2 in range(16):
        for d in v_d:
            for e in v_e:
                if SKINNY_SB_4[k1 ^ d] == (k2 ^ e):
                    keyset2[k1][k2].add((d, e))

print("\n--Non-linear Constraints--")
print("v_d :", [f"{v:01x}" for v in sorted(v_d)])
print("v_e :", [f"{v:01x}" for v in sorted(v_e)])
print("keyset2:")

# Print keyset2 as clean 16x16 hex matrix
for row in keyset2:
    formatted_row = []
    for s in row:
        hex_pairs = [f"({d:01x},{e:01x})" for d, e in sorted(s)]
        formatted_row.append("{" + ", ".join(hex_pairs) + "}")
    print("[" + ", ".join(formatted_row) + "]")

# Statistics
count_0 = count_2 = count_4 = 0
for k1 in range(16):
    for k2 in range(16):
        s_len = len(keyset2[k1][k2])
        if s_len == 0:
            count_0 += 1
        elif s_len == 2:
            count_2 += 1
        elif s_len == 4:
            count_4 += 1

print(f"Empty sets: {count_0}, pr: {0/N}")
print(f"Size 2 sets: {count_2}, pr: {2/N}")
print(f"Size 4 sets: {count_4}, pr: {4/N}")
```
```python
Half Constraints:
y_DDT_d2 : ['04', '06', '0c', '0e']
y_DDT_d9 : ['01', '03', '08', '0a']
y_DDT_25 : ['08', '09', '0c', '0d']
y_DDT_42 : ['05', '07', '0d', '0f']
x_DDT_bc : ['06', '0d']
x_DDT_25 : ['00', '02', '09', '0b']

---Simple Linear---
subkey_02_C1: ['00', '01', '02', '03', '08', '09', '0a', '0b']
subkey_02_C2: ['04', '05', '06', '07', '0c', '0d', '0e', '0f']
intersection of C1 and C2: set() 
portion: 0.0

---High-order Linear---
char. valid in: []

--Non-linear Constraints--
v_d : ['0', '1', '4', '5']
v_e : ['4', '5', '6', '7', 'c', 'd', 'e', 'f']
keyset2:
[{(0,c), (1,6)}, {(0,d), (1,7)}, {(0,e), (1,4)}, {(0,f), (1,5)}, {(4,5), (5,e)}, {(4,4), (5,f)}, {(4,7), (5,c)}, {(4,6), (5,d)}, {(0,4), (1,e)}, {(0,5), (1,f)}, {(0,6), (1,c)}, {(0,7), (1,d)}, {(4,d), (5,6)}, {(4,c), (5,7)}, {(4,f), (5,4)}, {(4,e), (5,5)}]
[{(0,6), (1,c)}, {(0,7), (1,d)}, {(0,4), (1,e)}, {(0,5), (1,f)}, {(4,e), (5,5)}, {(4,f), (5,4)}, {(4,c), (5,7)}, {(4,d), (5,6)}, {(0,e), (1,4)}, {(0,f), (1,5)}, {(0,c), (1,6)}, {(0,d), (1,7)}, {(4,6), (5,d)}, {(4,7), (5,c)}, {(4,4), (5,f)}, {(4,5), (5,e)}]
[{}, {}, {}, {}, {(0,d), (1,4), (4,6), (5,f)}, {(0,c), (1,5), (4,7), (5,e)}, {(0,f), (1,6), (4,4), (5,d)}, {(0,e), (1,7), (4,5), (5,c)}, {}, {}, {}, {}, {(0,5), (1,c), (4,e), (5,7)}, {(0,4), (1,d), (4,f), (5,6)}, {(0,7), (1,e), (4,c), (5,5)}, {(0,6), (1,f), (4,d), (5,4)}]
[{}, {}, {}, {}, {(0,4), (1,d), (4,f), (5,6)}, {(0,5), (1,c), (4,e), (5,7)}, {(0,6), (1,f), (4,d), (5,4)}, {(0,7), (1,e), (4,c), (5,5)}, {}, {}, {}, {}, {(0,c), (1,5), (4,7), (5,e)}, {(0,d), (1,4), (4,6), (5,f)}, {(0,e), (1,7), (4,5), (5,c)}, {(0,f), (1,6), (4,4), (5,d)}]
[{(4,c), (5,6)}, {(4,d), (5,7)}, {(4,e), (5,4)}, {(4,f), (5,5)}, {(0,5), (1,e)}, {(0,4), (1,f)}, {(0,7), (1,c)}, {(0,6), (1,d)}, {(4,4), (5,e)}, {(4,5), (5,f)}, {(4,6), (5,c)}, {(4,7), (5,d)}, {(0,d), (1,6)}, {(0,c), (1,7)}, {(0,f), (1,4)}, {(0,e), (1,5)}]
[{(4,6), (5,c)}, {(4,7), (5,d)}, {(4,4), (5,e)}, {(4,5), (5,f)}, {(0,e), (1,5)}, {(0,f), (1,4)}, {(0,c), (1,7)}, {(0,d), (1,6)}, {(4,e), (5,4)}, {(4,f), (5,5)}, {(4,c), (5,6)}, {(4,d), (5,7)}, {(0,6), (1,d)}, {(0,7), (1,c)}, {(0,4), (1,f)}, {(0,5), (1,e)}]
[{}, {}, {}, {}, {(0,6), (1,f), (4,d), (5,4)}, {(0,7), (1,e), (4,c), (5,5)}, {(0,4), (1,d), (4,f), (5,6)}, {(0,5), (1,c), (4,e), (5,7)}, {}, {}, {}, {}, {(0,e), (1,7), (4,5), (5,c)}, {(0,f), (1,6), (4,4), (5,d)}, {(0,c), (1,5), (4,7), (5,e)}, {(0,d), (1,4), (4,6), (5,f)}]
[{}, {}, {}, {}, {(0,f), (1,6), (4,4), (5,d)}, {(0,e), (1,7), (4,5), (5,c)}, {(0,d), (1,4), (4,6), (5,f)}, {(0,c), (1,5), (4,7), (5,e)}, {}, {}, {}, {}, {(0,7), (1,e), (4,c), (5,5)}, {(0,6), (1,f), (4,d), (5,4)}, {(0,5), (1,c), (4,e), (5,7)}, {(0,4), (1,d), (4,f), (5,6)}]
[{(4,4), (5,e)}, {(4,5), (5,f)}, {(4,6), (5,c)}, {(4,7), (5,d)}, {(0,7), (1,c)}, {(0,6), (1,d)}, {(0,5), (1,e)}, {(0,4), (1,f)}, {(4,c), (5,6)}, {(4,d), (5,7)}, {(4,e), (5,4)}, {(4,f), (5,5)}, {(0,f), (1,4)}, {(0,e), (1,5)}, {(0,d), (1,6)}, {(0,c), (1,7)}]
[{(4,e), (5,4)}, {(4,f), (5,5)}, {(4,c), (5,6)}, {(4,d), (5,7)}, {(0,c), (1,7)}, {(0,d), (1,6)}, {(0,e), (1,5)}, {(0,f), (1,4)}, {(4,6), (5,c)}, {(4,7), (5,d)}, {(4,4), (5,e)}, {(4,5), (5,f)}, {(0,4), (1,f)}, {(0,5), (1,e)}, {(0,6), (1,d)}, {(0,7), (1,c)}]
[{(0,5), (1,d), (4,7), (5,f)}, {(0,4), (1,c), (4,6), (5,e)}, {(0,7), (1,f), (4,5), (5,d)}, {(0,6), (1,e), (4,4), (5,c)}, {}, {}, {}, {}, {(0,d), (1,5), (4,f), (5,7)}, {(0,c), (1,4), (4,e), (5,6)}, {(0,f), (1,7), (4,d), (5,5)}, {(0,e), (1,6), (4,c), (5,4)}, {}, {}, {}, {}]
[{(0,d), (1,5), (4,f), (5,7)}, {(0,c), (1,4), (4,e), (5,6)}, {(0,f), (1,7), (4,d), (5,5)}, {(0,e), (1,6), (4,c), (5,4)}, {}, {}, {}, {}, {(0,5), (1,d), (4,7), (5,f)}, {(0,4), (1,c), (4,6), (5,e)}, {(0,7), (1,f), (4,5), (5,d)}, {(0,6), (1,e), (4,4), (5,c)}, {}, {}, {}, {}]
[{(0,4), (1,e)}, {(0,5), (1,f)}, {(0,6), (1,c)}, {(0,7), (1,d)}, {(4,7), (5,c)}, {(4,6), (5,d)}, {(4,5), (5,e)}, {(4,4), (5,f)}, {(0,c), (1,6)}, {(0,d), (1,7)}, {(0,e), (1,4)}, {(0,f), (1,5)}, {(4,f), (5,4)}, {(4,e), (5,5)}, {(4,d), (5,6)}, {(4,c), (5,7)}]
[{(0,e), (1,4)}, {(0,f), (1,5)}, {(0,c), (1,6)}, {(0,d), (1,7)}, {(4,c), (5,7)}, {(4,d), (5,6)}, {(4,e), (5,5)}, {(4,f), (5,4)}, {(0,6), (1,c)}, {(0,7), (1,d)}, {(0,4), (1,e)}, {(0,5), (1,f)}, {(4,4), (5,f)}, {(4,5), (5,e)}, {(4,6), (5,d)}, {(4,7), (5,c)}]
[{(0,7), (1,f), (4,5), (5,d)}, {(0,6), (1,e), (4,4), (5,c)}, {(0,5), (1,d), (4,7), (5,f)}, {(0,4), (1,c), (4,6), (5,e)}, {}, {}, {}, {}, {(0,f), (1,7), (4,d), (5,5)}, {(0,e), (1,6), (4,c), (5,4)}, {(0,d), (1,5), (4,f), (5,7)}, {(0,c), (1,4), (4,e), (5,6)}, {}, {}, {}, {}]
[{(0,f), (1,7), (4,d), (5,5)}, {(0,e), (1,6), (4,c), (5,4)}, {(0,d), (1,5), (4,f), (5,7)}, {(0,c), (1,4), (4,e), (5,6)}, {}, {}, {}, {}, {(0,7), (1,f), (4,5), (5,d)}, {(0,6), (1,e), (4,4), (5,c)}, {(0,5), (1,d), (4,7), (5,f)}, {(0,4), (1,c), (4,6), (5,e)}, {}, {}, {}, {}]
Empty sets: 64, pr: 0.0
Size 2 sets: 128, pr: 0.0625
Size 4 sets: 64, pr: 0.125
[Finished in 75ms]
```


<img width="1010" height="166" alt="image" src="https://github.com/user-attachments/assets/73c08c04-7b74-4bf4-8337-d00f87e6a8b2" />

<img width="1304" height="445" alt="image" src="https://github.com/user-attachments/assets/cc93aa72-d448-4e07-9386-364542b333b2" />

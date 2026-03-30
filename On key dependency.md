



### Linear Constraint



**例子 (SKINNY):**

```python
# Linear Constraint (Py)
SKINNY_SB_4 = [12,6,9,0,1,10,2,11,3,8,5,13,4,14,7,15]
Diff_d2, Diff_d9, Diff_bc, Diff_25 = (0xd,0x2), (0xd,0x9), (0xb,0xc), (0x2, 0x5)

'''
The half constraint:
* x_DDT is the -input- value of Sbox which fullfills the differential
* y_DDT is the -output- value of Sbox which fullfills the differential
'''

y_DDT_d2 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_d2[0]] == Diff_d2[1]}
y_DDT_d9 = {SKINNY_SB_4[x] for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_d9[0]] == Diff_d9[1]}
x_DDT_bc = {x for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_bc[0]] == Diff_bc[1]}
x_DDT_25 = {x for x in range(16) if SKINNY_SB_4[x] ^ SKINNY_SB_4[x^Diff_25[0]] == Diff_25[1]}


subkey_02_C1 = {(v1 ^ v2 ^ 0x2 ^ v3) for v1 in y_DDT_d2 for v2 in y_DDT_d9 for v3 in x_DDT_bc} & set(range(16))
subkey_02_C2 = {(v1 ^ 0x2 ^ v2) for v1 in y_DDT_d2 for v2 in x_DDT_25} & set(range(16))


print("y_DDT_d2 :", [f"{v:02x}" for v in sorted(y_DDT_d2)])
print("y_DDT_d9 :", [f"{v:02x}" for v in sorted(y_DDT_d9)])
print("x_DDT_bc :", [f"{v:02x}" for v in sorted(x_DDT_bc)])
print("x_DDT_25 :", [f"{v:02x}" for v in sorted(x_DDT_25)])
print("subkey_02_C1:", [f"{k:02x}" for k in sorted(subkey_02_C1)])
print("subkey_02_C2:", [f"{k:02x}" for k in sorted(subkey_02_C2)])

inter_C = subkey_02_C1.intersection(subkey_02_C2)

print("intersection of C1 and C2:",inter_keyC, "\nportion:", len(inter_keyC)/(2**4))
```

```python
y_DDT_d2 : ['04', '06', '0c', '0e']
y_DDT_d9 : ['01', '03', '08', '0a']
x_DDT_bc : ['06', '0d']
x_DDT_25 : ['00', '02', '09', '0b']
subkey_02_C1: ['00', '01', '02', '03', '08', '09', '0a', '0b']
subkey_02_C2: ['04', '05', '06', '07', '0c', '0d', '0e', '0f']
intersection of C1 and C2: set()
portion: 0.0
```



### High-order linear constraints:

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

把 ART+SR+MC 当作整体, 记图中<font color=green> 绿圈 = $NL_i$ </font>, <font color=purple>紫圈=$NL_o$</font>.

因为 $NL_i$ 所在一列仅有其自身活跃, 所以该列只有这一个 半约束 , 所以涉及到上面式子中的 $x_0, x_1, x_3$ 均为冗余约束, 唯一有效约束为仅包含 $x_2$ 的约束, 为 (三行等价):

$$
\begin{aligned}
& y_1\oplus y_3 = x_0 \oplus k_0 \oplus x_0 \oplus x_2 \oplus k_0 = x_2\\
\\
& \{x_{DDT}(0x2,0x5)=\{0x0, 0x2, 0x9, 0xb\}\} \oplus \{x_{DDT}(0xb,0xc)=\{0x6,0xd\}\} =  \{y_{DDT}(0xd,0x9)=\{0x1,0x3,0x8,0xa\}\}\\
\\
& NL_o^6\oplus NL_o^{14} = NL_i^8
\end{aligned}
$$

```python
# NonLinear Constraints (partial, mark as position)(Py)
charset = {(v1, v2, v3) for v1 in x_DDT_25 for v2 in x_DDT_bc for v3 in y_DDT_d9 if v1 ^ v2 ^ v3 == 0}
print("char. valid in:", [f"({a:02x},{b:02x},{c:02x})" for a,b,c in sorted(charset)])
```

```python
char. valid in: []
```

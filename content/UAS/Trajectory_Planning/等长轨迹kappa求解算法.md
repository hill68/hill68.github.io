
+++
date = '2025-05-21T17:07:50+08:00'
draft = false
title = '等长轨迹κ求解算法设计'
summary= "提出一种基于二分查找的数值方法，用以求解参数 $\kappa^*$，从而使得生成的 $\kappa$-轨迹路径长度与原始航路点轨迹 $\mathcal{P}$ 的路径长度完全一致。"
+++

# 等长轨迹$\kappa$求解算法设计

## 摘要

本报告针对DTS（Dynamically Time‐Scaled）算法生成的轨迹，提出一种基于二分查找的数值方法，用以求解参数 $\kappa^*$，从而使得生成的 $\kappa$-轨迹路径长度与原始航路点轨迹 $\mathcal{P}$ 的路径长度完全一致。该方法依托于路径长度差函数 $\Lambda(\kappa)$ 在区间 $[0,1]$ 上的单调性和端点符号异号性质，通过迭代逼近 $\Lambda(\kappa)=0$ 的唯一解，实现了时间关键任务下轨迹光滑与时序约束的双重满足。

---

## 1. 问题描述

* **原始路径** $\mathcal{P} = \overline{\mathbf{w}_{i-1}\mathbf{w}_i\mathbf{w}_{i+1}}$ 的路径长度

  $$
    L_{\rm orig} = \|\mathbf{w}_i - \mathbf{w}_{i-1}\| + \|\mathbf{w}_{i+1} - \mathbf{w}_i\|.
  $$
* **$\kappa$-轨迹** 在参数 $\kappa\in[0,1]$ 下生成对应的平滑转弯路径，其路径长度可表示为

  $$
    L(\kappa) = L_{\rm orig} + \Lambda(\kappa),
  $$

  其中 $\Lambda(\kappa)$ 如式(42)所定义。&#x20;

目标：求解唯一的 $\kappa^*\in[0,1]$，使得

$$
  L(\kappa^*) = L_{\rm orig}
  \quad\Longleftrightarrow\quad
  \Lambda(\kappa^*) = 0.
$$

---

## 2. 数学背景

1. **路径长度差函数 $\Lambda(\kappa)$**
   引理6给出 $\kappa$-轨迹的路径长度差解析表达式：

   $$
   \begin{aligned}
   \Lambda(\kappa)
   &=2R\Bigl(\tfrac{\pi-\beta}{2}
    +2\cos^{-1}\bigl(\Xi(\kappa,\beta)\bigr)
    -\Xi(\kappa,\beta)\sqrt{\tfrac{1}{\Xi(\kappa,\beta)^2}-1}\\
   &\quad\;-\,(1-\kappa)\cos\tfrac{\beta}{2}
    -\kappa\cot\tfrac{\beta}{2}\Bigr),
   \end{aligned}
   $$

   其中
   $\beta = \cos^{-1}\bigl(\hat{\mathbf{t}}_{i,i+1}^T\hat{\mathbf{t}}_{i-1,i}\bigr)$，

   $\Xi(\kappa,\beta)=\tfrac{(1+\kappa)+(1-\kappa)\sin(\beta/2)}{2}$，
   
   $R=\hat v/c$。&#x20;
   
2. **单调性和端点条件**

   * $\Lambda(\kappa)$ 关于 $\kappa$ 递减，且
     $\Lambda(0)>0$, $\Lambda(1)<0$（引理7） 。
   * 因此，存在且仅存在唯一 $\kappa^*\in(0,1)$ 使得 $\Lambda(\kappa^*)=0$（定理8）。&#x20;

---

## 3. 算法设计

### 3.1 输入与输出

* **输入**

  1. 三点坐标 $\mathbf{w}_{i-1},\mathbf{w}_i,\mathbf{w}_{i+1}$。
  2. 预设速度 $\hat v$ 与转向速率 $c$，计算半径 $R=\hat v/c$。
  3. 目标精度 $\varepsilon$（如 $10^{-6}$）。

* **输出**

  * 唯一的 $\kappa^*\in[0,1]$，满足 $\Lambda(\kappa^*)=0$。

### 3.2 路径差函数计算

1. 计算夹角 $\beta$。
2. 对给定 $\kappa$，按引理6公式计算 $\Lambda(\kappa)$。
3. 注意数值鲁棒性，避免在 $\Xi\approx0$ 或 $\Xi\approx1$ 时出现除零或浮点不稳定。

### 3.3 二分查找流程

1. 设定区间 $[a,b]=[0,1]$。
2. 计算 $f(a)=\Lambda(a)$, $f(b)=\Lambda(b)$，验端点符号异号。
3. 重复直到 $|b-a|<\varepsilon$：

   1. 令 $m=(a+b)/2$，计算 $f(m)=\Lambda(m)$。
   2. 若 $f(m)>0$，则令 $a\leftarrow m$；否则令 $b\leftarrow m$。
4. 返回 $\kappa^*=(a+b)/2$。

### 3.4 终止条件与复杂度

* **终止条件**：区间长度 $|b-a|<\varepsilon$。
* **迭代次数**：$N=O(\log_2(1/\varepsilon))$。
* **每次迭代成本**：一次 $\Lambda$ 评估，涉及若干三角函数与平方根。总体时间复杂度 $O(\log(1/\varepsilon))$。

---

## 4. 伪代码

```plaintext
function FindKappaStar(w_prev, w_i, w_next, v_hat, c, eps):
    R ← v_hat / c
    compute β = arccos(dot(normalize(w_next - w_i), normalize(w_i - w_prev)))
    define Lambda(κ):
        Xi = ((1+κ) + (1-κ)*sin(β/2)) / 2
        return 2*R*( (π-β)/2
                     + 2*arccos(Xi)
                     - Xi*sqrt(1/Xi^2 - 1)
                     - (1-κ)*cos(β/2)
                     - κ*cot(β/2) )
    a ← 0; b ← 1
    f_a ← Lambda(a); f_b ← Lambda(b)
    assert f_a > 0 and f_b < 0
    while (b - a) > eps:
        m ← (a + b) / 2
        f_m ← Lambda(m)
        if f_m > 0:
            a ← m
        else:
            b ← m
    return (a + b) / 2
```

---

## 5. 实现注意事项

1. **数值稳定性**：

   * 对 $\Xi$ 接近 0 或 1 时，使用 `max(min(Xi,1-δ), δ)` 限幅。
   * 三角函数参数应以弧度制调用。

2. **DTS 系统集成**：

   * 在轨迹生成前调用 `FindKappaStar`，获取 $\kappa^*$。
   * 将 $\kappa^*$ 传入 DTS 控制律，生成平滑转弯命令序列。

3. **性能优化**：

   * 对于多段路径，可复用相同 $\beta$ 及 $R$ 计算。
   * 可并行计算各段 $\kappa^*$。

---

## 6. 小结

报告提出的基于二分查找的算法，能高效、准确地求解使 DTS 生成轨迹与原始航路点轨迹等长的参数 $\kappa^*$。该方法简单易实现，时间复杂度 $O(\log(1/\varepsilon))$，适用于实时轨迹规划系统。

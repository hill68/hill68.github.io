+++
date = '2025-05-21T17:07:50+08:00'
draft = false
title = '用于轨迹规划的三次样条插值'
summary= "针对轨迹规划问题，详细阐述了如何利用三次多项式样条插值构造一条平滑连续的轨迹曲线。"
+++


## **摘要：**


本文档针对轨迹规划问题，详细阐述了如何利用三次样条插值构造一条平滑连续的轨迹曲线。文档不仅系统阐述了三次样条插值的理论基础及其边界条件构造方法，同时通过详细的示例代码和附录部分，为实际轨迹规划问题提供了从理论到数值实现的完整解决方案。

文档的主体部分按不同边界条件分类讨论了三种情况：

1. **仅给定起始与结束速度的情形**  
   该方法以各连接处的速度作为主要未知量，利用位置、速度与加速度连续性条件构造出一个标准的对角占优三对角系统。采用 Thomas 算法求解该系统后，再根据求得的速度值计算各段多项式的系数。该方法结构简单，适用于只给定边界速度时的轨迹规划。

2. **周期三次样条**  
   当样条曲线要求周期性（即起始位置与结束位置相同，同时边界处的速度和加速度也相等）时，问题转化为求解循环三对角矩阵方程。文档介绍了如何构造这种特殊系统以及利用 Sherman-Morrison 公式或其他分解方法将其转化为标准三对角系统进行求解。

3. **同时给定起始与结束的速度和加速度的情形**  
   由于一般难以直接同时指定端点的速度与加速度，文档提出在第一段和最后一段分别插入辅助节点，从而扩展原始节点序列。扩展后利用修正边界条件构造新的三对角系统求解辅助变量（节点加速度），进而计算各段多项式系数，实现同时满足端点速度和加速度约束的样条构造。

文档后半部分提供了详细的 MATLAB 示例以及模块化的 C++ 示例代码，分别演示了基于速度法和基于加速度法的实现过程。

**附录部分**则深入介绍了数值求解的基础方法：  
- **附录 1**详细说明了三对角矩阵与循环三对角矩阵的数值解法，重点介绍了 Thomas 算法的前向消去与回代步骤，并讨论了如何处理具有循环边界条件的特殊情形（利用 Sherman-Morrison 公式或分解法）。  
- **附录 2**和**附录 3**分别给出了在仅给定边界速度和同时给定边界速度与加速度条件下的三次样条插值的 C++ 示例代码，提供了完整的实现方案，便于工程实践中进行扩展和调试。



## 一、问题定义

给定 $\mathrm{n}+1$ 个点 $\left(\mathrm{t}_{\mathrm{i}}, \mathrm{q}_{\mathrm{i}}\right), \mathrm{i}=0,1, \ldots, \mathrm{n}$ ，求解 n 段三次多项式 $\mathrm{q}_{\mathrm{k}}(\mathrm{t})$ ，使得拼接而成的三次样条曲线 $\mathrm{s}(\mathrm{t})$ 经过所有给定点，而且二阶连续。三次样条曲线 $\mathrm{s}(\mathrm{t})$ 定义如下：

<img src="https://cdn.mathpix.com/snip/images/9lbuJx1WtYE0aso-mtHaXo1ryCnMf6Vrh7LO4WLWhwY.original.fullsize.png" />



$$
\left\{\begin{array}{l}
\mathrm{s}(\mathrm{t})=\left\{\mathrm{q}_{\mathrm{k}}(\mathrm{t}), \mathrm{t} \in\left[\mathrm{t}_{\mathrm{k}}, \mathrm{t}_{\mathrm{k}+1}\right], \mathrm{k}=0,1, \ldots, \mathrm{n}-1\right\} \\
\mathrm{q}_{\mathrm{k}}(\mathrm{t})=\mathrm{a}_{\mathrm{k} 0}+\mathrm{a}_{\mathrm{k} 1}\left(\mathrm{t}-\mathrm{t}_{\mathrm{k}}\right)+\mathrm{a}_{\mathrm{k} 2}\left(\mathrm{t}-\mathrm{t}_{\mathrm{k}}\right)^2+\mathrm{a}_{\mathrm{k} 3}\left(\mathrm{t}-\mathrm{t}_{\mathrm{k}}\right)^3
\end{array}\right. \tag{1}
$$

每一段三次多项式 $q_k(\mathrm{t})$ 需要确定 4 个系数，因而未知数共 4 n 个。

三次样条曲线基本约束条件： 

（1）位置约束，每一段三次多项式都必须经过两个点，有约束条件 2 n 个；

（2）速度约束，相邻两段三次多项式在连接处的速度相等，有约束条件 $\mathrm{n}-1$ 个；

（3）加速度约束，相邻两段三次多项式在连接处的加速度相等，有约束条件 $\mathrm{n}-1$ 个。故还需两个给定约束 $(4 \mathrm{n}-2 \mathrm{n}-(\mathrm{n}-1)-(\mathrm{n}-1)=2)$ ，便可唯一确定三次样条曲线。

这里讨论三种不同的给定约束：

（1）给定起始速度 $\mathrm{v}_0$ 与结束速度 $\mathrm{v}_{\mathrm{n}}$

（2）起始位置 $q_0$ 与结束位置 $q_n$ 相等（数据需满足的特性，非约束条件），起始速度 $v_0$ 与结束速度 $v_n$相等，起始加速度 $a_0$ 与结束加速度 $a_n$ 相等（周期三次样条）

（3）给定起始速度 $v_0$ ，结束速度 $v_n$ ，起始加速度 $a_0$ ，结束加速度 $a_n$

由于样条插值在每个维度上是独立的（前提是参数 $t$ 相同），所以在使用中可以将二推问题拆分为对 $x(t)$与 $y(t)$ 分别做一维样条插值。这种设计可以很方便地扩展到二维（或更高维），只需要为每个分量构造独立的样条，然后在使用时将各个分量组合即可。

## 二、给定约束：起始速度 $\mathrm{v}_0$ 、结束速度 $\mathrm{v}_{\mathrm{n}}$

### 1. 三次样条的系数通过速度计算三次样条曲线所有约束：

$$
\left\{\begin{array}{l}
\mathrm{q}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}}\right)=\mathrm{q}_{\mathrm{k}}, \mathrm{q}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}\right)=\mathrm{q}_{\mathrm{k}+1}, \mathrm{k}=0, \ldots, \mathrm{n}-1 \\
\dot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}\right)=\dot{\mathrm{q}}_{\mathrm{k}+1}\left(\mathrm{t}_{\mathrm{k}+1}\right)=\mathrm{v}_{\mathrm{k}+1}, \mathrm{k}=0, \ldots, \mathrm{n}-2 \\
\ddot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}\right)=\ddot{\mathrm{q}}_{\mathrm{k}+1}\left(\mathrm{t}_{\mathrm{k}+1}\right), \mathrm{k}=0, \ldots, \mathrm{n}-2 \\
\dot{\mathrm{q}}_0\left(\mathrm{t}_0\right)=\mathrm{v}_0, \dot{\mathrm{q}}_{\mathrm{n}-1}\left(\mathrm{t}_{\mathrm{n}}\right)=\mathrm{v}_{\mathrm{n}}
\end{array}\right. \tag{2}
$$


若所有中间点速度 $\mathrm{v}_{\mathrm{k}}(\mathrm{k}=1, \ldots, \mathrm{n}-1)$ 都已知，根据位置与速度约束：

$$
\left\{\begin{array}{l}
\mathrm{q}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}}\right)=\mathrm{a}_{\mathrm{k} 0}=\mathrm{q}_{\mathrm{k}} \\
\dot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}}\right)=\mathrm{a}_{\mathrm{k} 1}=\mathrm{v}_{\mathrm{k}} \\
\mathrm{q}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}\right)=\mathrm{a}_{\mathrm{k} 0}+\mathrm{a}_{\mathrm{k} 1} \mathrm{~T}_{\mathrm{k}}+\mathrm{a}_{\mathrm{k} 2} \mathrm{~T}_{\mathrm{k}}^2+\mathrm{a}_{\mathrm{k} 3} \mathrm{~T}_{\mathrm{k}}^3=\mathrm{q}_{\mathrm{k}+1} \\
\dot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}\right)=\mathrm{a}_{\mathrm{k} 1}+2 \mathrm{a}_{\mathrm{k} 2} \mathrm{~T}_{\mathrm{k}}+3 \mathrm{a}_{\mathrm{k} 3} \mathrm{~T}_{\mathrm{k}}^2=\mathrm{v}_{\mathrm{k}+1}
\end{array}\right. \tag{3}
$$


其中， $\mathrm{T}_{\mathrm{k}}=\mathrm{t}_{\mathrm{k}+1}-\mathrm{t}_{\mathrm{k}}$ 。
由式（3）可解得：

$$
\left\{\begin{array}{l}
\mathrm{a}_{\mathrm{k}, 0}=\mathrm{q}_{\mathrm{k}} \\
\mathrm{a}_{\mathrm{k}, 1}=\mathrm{v}_{\mathrm{k}} \\
\mathrm{a}_{\mathrm{k}, 2}=\left[3\left(\mathrm{q}_{\mathrm{k}+1}-\mathrm{q}_{\mathrm{k}}\right) / \mathrm{T}_{\mathrm{k}}-2 \mathrm{v}_{\mathrm{k}}-\mathrm{v}_{\mathrm{k}+1}\right] / \mathrm{T}_{\mathrm{k}} \\
\mathrm{a}_{\mathrm{k}, 3}=\left[2\left(\mathrm{q}_{\mathrm{k}}-\mathrm{q}_{\mathrm{k}+1}\right) / \mathrm{T}_{\mathrm{k}}+\mathrm{v}_{\mathrm{k}}+\mathrm{v}_{\mathrm{k}+1}\right] / \mathrm{T}_{\mathrm{k}}^2
\end{array}\right. \tag{4}
$$

考虑加速度约束：

$$
\ddot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}\right)=2 \mathrm{a}_{\mathrm{k}, 2}+6 \mathrm{a}_{\mathrm{k}, 3} \mathrm{~T}_{\mathrm{k}}=2 \mathrm{a}_{\mathrm{k}+1,2}=\ddot{\mathrm{q}}_{\mathrm{k}+1}\left(\mathrm{t}_{\mathrm{k}+1}\right) \tag{5}
$$


将 $\mathrm{a}_{\mathrm{k}, 2}, \mathrm{a}_{\mathrm{k}, 3}, \mathrm{a}_{\mathrm{k}+1,2}$ 代入式（5）化简得：

$$
\begin{aligned}
& \mathrm{T}_{\mathrm{k}+1} \mathrm{v}_{\mathrm{k}}+2\left(\mathrm{~T}_{\mathrm{k}+1}+\mathrm{T}_{\mathrm{k}}\right) \mathrm{v}_{\mathrm{k}+1}+\mathrm{T}_{\mathrm{k}} \mathrm{v}_{\mathrm{k}+2} \\
& =3\left[\mathrm{~T}_{\mathrm{k}}^2\left(\mathrm{q}_{\mathrm{k}+2}-\mathrm{q}_{\mathrm{k}+1}\right)+\mathrm{T}_{\mathrm{k}+1}^2\left(\mathrm{q}_{\mathrm{k}+1}-\mathrm{q}_{\mathrm{k}}\right)\right] /\left(\mathrm{T}_{\mathrm{k}} \mathrm{~T}_{\mathrm{k}+1}\right)
\end{aligned} \tag{6}
$$

其中， $\mathrm{k}=0,1, \ldots, \mathrm{n}-2$ 。
式（6）写成矩阵形式（ $\mathrm{v}_0, \mathrm{v}_{\mathrm{n}}$ 已知）：
$$
\begin{gathered}
{\left[\begin{array}{ccccc}
2\left(\mathrm{~T}_0+\mathrm{T}_1\right) & \mathrm{T}_0 & 0 & \cdots & 0 \\
\mathrm{~T}_2 & 2\left(\mathrm{~T}_1+\mathrm{T}_2\right) & \mathrm{T}_1 & 0 & \vdots \\
0 & & \ddots & & 0 \\
\vdots & 0 & \mathrm{~T}_{\mathrm{n}-2} & 2\left(\mathrm{~T}_{\mathrm{n}-3}+\mathrm{T}_{\mathrm{n}-2}\right) & \mathrm{T}_{\mathrm{n}-3} \\
0 & \cdots & 0 & \mathrm{~T}_{\mathrm{n}-1} & 2\left(\mathrm{~T}_{\mathrm{n}-2}+\mathrm{T}_{\mathrm{n}-1}\right)
\end{array}\right]\left[\begin{array}{c}
\mathrm{v}_1 \\
\mathrm{v}_2 \\
\vdots \\
\mathrm{v}_{\mathrm{n}-2} \\
\mathrm{v}_{\mathrm{n}-1}
\end{array}\right]} \\
\quad=\left[\begin{array}{c}
3\left[\mathrm{~T}_0^2\left(\mathrm{q}_2-\mathrm{q}_1\right)+\mathrm{T}_1^2\left(\mathrm{q}_1-\mathrm{q}_0\right)\right] /\left(\mathrm{T}_0 \mathrm{~T}_1\right)-\mathrm{T}_1 \mathrm{v}_0 \\
3\left[\mathrm{~T}_1^2\left(\mathrm{q}_3-\mathrm{q}_2\right)+\mathrm{T}_2^2\left(\mathrm{q}_2-\mathrm{q}_1\right)\right] /\left(\mathrm{T}_1 \mathrm{~T}_2\right) \\
\vdots \\
3\left[\mathrm{~T}_{\mathrm{n}-2}^2\left(\mathrm{q}_{\mathrm{n}}-\mathrm{q}_{\mathrm{n}-1}\right)+\mathrm{T}_{\mathrm{n}-1}^2\left(\mathrm{q}_{\mathrm{n}-1}-\mathrm{q}_{\mathrm{n}-2}\right)\right] /\left(\mathrm{T}_{\mathrm{n}-2} \mathrm{~T}_{\mathrm{n}-1}\right)-\mathrm{T}_{\mathrm{n}-2} \mathrm{v}_{\mathrm{n}}
\end{array}\right]
\end{gathered} \tag{7}
$$

式（7）为对角占优的三对角矩阵方程，可以利用Thomas algorithm 或称＂追赶法＂快速且稳定地求解，而无需求解矩阵的逆。可参考[博文](https://blog.csdn.net/maple_2014/article/details/107170005)：三对角方程与循环三对角方程数值解

**对以上三对角矩阵方程的解析（ChatGPT）：**


从矩阵形式来看，该方程的系数矩阵为
$$
\begin{pmatrix}
2(T_0+T_1) & T_0 & 0 & \cdots & 0 \\
T_2 & 2(T_1+T_2) & T_1 & 0 & \cdots \\
0 & \ddots & \ddots & \ddots & 0 \\
\vdots & 0 & T_{n-2} & 2(T_{n-3}+T_{n-2}) & T_{n-3} \\
0 & \cdots & 0 & T_{n-1} & 2(T_{n-2}+T_{n-1})
\end{pmatrix}.
$$
其中，对于第 $i$ 行（按照 $1 \le i \le n-1$ 记未知数 $v_1,\ldots,v_{n-1}$），主对角元为
$$
b_i = 2(T_{i-1}+T_i)
$$
（注意第一行用 $T_0$ 和 $T_1$，最后一行用 $T_{n-2}$ 和 $T_{n-1}$），而非主对角元分别为 $a_i$ 和 $c_i$（具体为 $a_1$ 不存在，后续行的下对角元取自相应的 $T$ 值，类似地上对角元）。

如果所有的 $T_i$ 都为正，则对于任意行都有
$$
b_i = 2(T_{i-1}+T_i) > T_{i-1}+T_i,
$$
也就是主对角元的绝对值严格大于同一行中其他非零系数之和，从而满足严格对角占优的条件。

另外，从矩阵的结构来看，该矩阵仅在主对角及其上下对角上有非零元，并不含有“循环”项（即第一行和最后一行之间无耦合），因此它是一个标准的三对角矩阵。

综上所述：
- **对角占优性**：当 $T_i > 0$ 时，矩阵满足严格对角占优条件。
- **求解方法**：由于该矩阵是标准的三对角矩阵，且满足对角占优条件，因此可以直接采用 Thomas 算法（追赶法）来求解这一线性系统。  
  需要注意的是，该方程右侧包含了边界条件（例如 $v_0$ 和 $v_n$ 已知），这些通常会在构造右侧向量时体现出来；而待求解的未知数为 $v_1, v_2, \ldots, v_{n-1}$。

因此，对于以上给出的这个方程，如果 $T_i$ 为正，则可以使用 Thomas 算法来快速且稳定地求解。

------



```matlab
clc;
clear;
close all;

t = [0, 5, 7, 8, 10, 15, 18]'; %递增时间序列
q = [3, -2, -5, 0, 6, 12, 8]';
v0 = 2; %起始速度
vn = -3; %结束速度
deltaT = 0.001; %插补周期

n = length(t); % n >= 4
T = diff(t);
a = zeros(n - 2, 1);
b = zeros(n - 2, 1);
c = zeros(n - 2, 1);
d = zeros(n - 2, 1);

for i = 2 : n - 2
    a(i) = T(i + 1);
end
for i = 1 : n - 2
    b(i) = 2.0 * (T(i) + T(i + 1));
end
for i = 1 : n - 3
    c(i) = T(i);
end

d(1) = 3.0 * (T(1)^2 * (q(3) - q(2)) + T(2)^2 * (q(2) - q(1))) / (T(1) * T(2)) - T(2) * v0;
for i = 2 : n - 3
   d(i) = 3.0 * (T(i)^2 * (q(i + 2) - q(i + 1)) + T(i + 1)^2 * (q(i + 1) - q(i))) / (T(i) * T(i + 1)); 
end
d(n - 2) = 3.0 * (T(n - 2)^2 * (q(n) - q(n - 1)) + T(n - 1)^2 * (q(n - 1) - q(n - 2))) / (T(n - 2) * T(n - 1)) - T(n - 2) * vn;

[v, sta] = solve_tridiagonal_matrix_equation(a, b, c, d, n - 2);
if sta == 0
    error('三对角矩阵方程求解出错!');
end
v = [v0; v; vn];

time = [];
pos = [];
vel = [];
acc = [];
time = [time; t(1)];
pos = [pos; q(1)];
vel = [vel; v0];
acc = [acc; 2.0 * (3.0 * (q(2) - q(1)) / T(1) - 2.0 * v(1) - v(2)) / T(1)];
for i = 1 : n - 1
    tt = (t(i) + deltaT : deltaT : t(i + 1))';
    b0 = q(i);
    b1 = v(i);
    b2 = (3.0 * (q(i + 1) - q(i)) / T(i) - 2.0 * v(i) - v(i + 1)) / T(i);
    b3 = (2.0 * (q(i) - q(i + 1)) / T(i) + v(i) + v(i + 1)) / T(i)^2;

    time = [time; tt];
    pos = [pos; b0 + (tt - t(i)) .* (b1 + (tt - t(i)) .* (b2 + b3 .* (tt - t(i))))];
    vel = [vel; b1 + (tt - t(i)) .* (2.0 * b2 + 3.0 * b3 .* (tt - t(i)))];
    acc = [acc; 2.0 * b2 + 6.0 * b3 .* (tt - t(i))];
end
if abs(time(end) - t(n)) > 1.0e-8
    b2 = (3.0 * (q(n) - q(n - 1)) / T(n - 1) - 2.0 * v(n - 1) - v(n)) / T(n - 1);
    b3 = (2.0 * (q(n - 1) - q(n)) / T(n - 1) + v(n - 1) + v(n)) / T(n - 1)^2;
    time = [time; t(n)];
    pos = [pos; q(n)];
    vel = [vel; vn];
    acc = [acc; 2.0 * b2 + 6.0 * b3 .* T(n - 1)];
end

figure(1)
subplot(3, 1, 1)
plot(time, pos);
hold on
plot(t, q, 'ro');
xlabel('t')
ylabel('pos')

subplot(3, 1, 2)
plot(time, vel);
hold on
plot([t(1), t(n)], [v0, vn], 'ro');
xlabel('t')
ylabel('vel')

subplot(3, 1, 3)
plot(time, acc);
xlabel('t')
ylabel('acc')

```


<img
  src="https://i-blog.csdnimg.cn/blog_migrate/7615051c502f5b3b5396361f2676c69a.png "  
  alt="fig y"
  width="500"
  style="height: auto;"
/>


#### 在线验证计算器

根据以上算法，利用Claude生成三次样条插值[在线计算器](https://claude.site/artifacts/a4a9a9ba-d1f0-452b-ae53-097ff36e880b) （代码有缺陷，插值点数量最小为4，小于4加速度不连续），可用于代码调试测试检验。

<img src="https://cdn.mathpix.com/snip/images/20pUfP72JQWsdOdlzFsP4YOeifCFZBpqz8tZweenejs.original.fullsize.png" />
<img src="https://cdn.mathpix.com/snip/images/_Iwf0e1xv_AAwajTb1IOyqSHLDSc7K9b44mB3lQJcGE.original.fullsize.png" />

### 2. 三次样条的系数通过加速度计算

第k段三次多项式的加速度：
$$
\ddot{\mathrm{q}}_{\mathrm{k}}(\mathrm{t})=\left[\omega_{\mathrm{k}+1}\left(\mathrm{t}-\mathrm{t}_{\mathrm{k}}\right)+\omega_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}-\mathrm{t}\right)\right] / \mathrm{T}_{\mathrm{k}} \tag{8}
$$


其中，$\omega_{\mathrm{k}}$ 为 $\mathrm{t}_{\mathrm{k}}$ 处对应的加速度， $\mathrm{k}=0,1, \ldots, \mathrm{n}$ 。对上式积分，得到速度：

$$
\begin{aligned}
\dot{\mathrm{q}}_{\mathrm{k}}(\mathrm{t})= & \omega_{\mathrm{k}+1}\left(\mathrm{t}-\mathrm{t}_{\mathrm{k}}\right)^2 /\left(2 \mathrm{~T}_{\mathrm{k}}\right)+\omega_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}+1}-\mathrm{t}\right)^2 /\left(2 \mathrm{~T}_{\mathrm{k}}\right) \\
& +\left(\mathrm{q}_{\mathrm{k}+1}-\mathrm{q}_{\mathrm{k}}\right) / \mathrm{T}_{\mathrm{k}}-\mathrm{T}_{\mathrm{k}}\left(\omega_{\mathrm{k}+1}-\omega_{\mathrm{k}}\right) / 6
\end{aligned} \tag{9}
$$

$\mathrm{t}_{\mathrm{k}}$ 处的速度与加速度约束：

$$
\left\{\begin{array}{l}
\dot{\mathrm{q}}_{\mathrm{k}-1}\left(\mathrm{t}_{\mathrm{k}}\right)=\dot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}}\right) \\
\ddot{\mathrm{q}}_{\mathrm{k}-1}\left(\mathrm{t}_{\mathrm{k}}\right)=\ddot{\mathrm{q}}_{\mathrm{k}}\left(\mathrm{t}_{\mathrm{k}}\right)=\omega_{\mathrm{k}}
\end{array}\right.\tag{10}
$$


由式（8）（9）（10）得：

$$
\begin{gathered}
\left(\mathrm{T}_{\mathrm{k}-1} / \mathrm{T}_{\mathrm{k}}\right) \omega_{\mathrm{k}-1}+\left[2\left(\mathrm{~T}_{\mathrm{k}}+\mathrm{T}_{\mathrm{k}-1}\right) / \mathrm{T}_{\mathrm{k}}\right] \omega_{\mathrm{k}}+\omega_{\mathrm{k}+1} \\
=\left(6 / \mathrm{T}_{\mathrm{k}}\right)\left[\left(\mathrm{q}_{\mathrm{k}+1}-\mathrm{q}_{\mathrm{k}}\right) / \mathrm{T}_{\mathrm{k}}-\left(\mathrm{q}_{\mathrm{k}}-\mathrm{q}_{\mathrm{k}-1}\right) / \mathrm{T}_{\mathrm{k}-1}\right], \mathrm{k}=1,2, \ldots, \mathrm{n}-1
\end{gathered} \tag{11}
$$


由 $\dot{s}\left(\mathrm{t}_0\right)=\mathrm{v}_0$ 得：

$$
\mathrm{T}_0^2 \omega_0 / 3+\mathrm{T}_0^2 \omega_1 / 6=\mathrm{q}_1-\mathrm{q}_0-\mathrm{T}_0 \mathrm{v}_0 \tag{12}
$$

由 $\dot{s}\left(\mathrm{t}_{\mathrm{n}}\right)=\mathrm{v}_{\mathrm{n}}$ 得：

$$
\mathrm{T}_{\mathrm{n}-1}^2 \omega_{\mathrm{n}} / 3+\mathrm{T}_{\mathrm{n}-1}^2 \omega_{\mathrm{n}-1} / 6=\mathrm{q}_{\mathrm{n}-1}-\mathrm{q}_{\mathrm{n}}+\mathrm{T}_{\mathrm{n}-1} \mathrm{v}_{\mathrm{n}} \tag{13}
$$


将式（9）（12）（13）写成矩阵形式：

$$
\mathrm{A} \omega=\mathrm{c} \tag{14}
$$

其中：

$$
\mathrm{A}=\left[\begin{array}{ccccc}
2 \mathrm{~T}_0 & \mathrm{~T}_0 & 0 & \cdots & 0 \\
\mathrm{~T}_0 & 2\left(\mathrm{~T}_0+\mathrm{T}_1\right) & \mathrm{T}_1 & 0 & \vdots \\
0 & & \ddots & & 0 \\
\vdots & 0 & \mathrm{~T}_{\mathrm{n}-2} & 2\left(\mathrm{~T}_{\mathrm{n}-2}+\mathrm{T}_{\mathrm{n}-1}\right) & \mathrm{T}_{\mathrm{n}-1} \\
0 & \cdots & 0 & \mathrm{~T}_{\mathrm{n}-1} & 2 \mathrm{~T}_{\mathrm{n}-1}
\end{array}\right] \tag{15}
$$

$$
\mathrm{c}=\left[\begin{array}{c}
6\left[\left(\mathrm{q}_1-\mathrm{q}_0\right) / \mathrm{T}_0-\mathrm{v}_0\right] \\
6\left[\left(\mathrm{q}_2-\mathrm{q}_1\right) / \mathrm{T}_1-\left(\mathrm{q}_1-\mathrm{q}_0\right) / \mathrm{T}_0\right] \\
\vdots \\
6\left[\left(\mathrm{q}_{\mathrm{n}}-\mathrm{q}_{\mathrm{n}-1}\right) / \mathrm{T}_{\mathrm{n}-1}-\left(\mathrm{q}_{\mathrm{n}-1}-\mathrm{q}_{\mathrm{n}-2}\right) / \mathrm{T}_{\mathrm{n}-2}\right] \\
6\left[\mathrm{v}_{\mathrm{n}}-\left(\mathrm{q}_{\mathrm{n}}-\mathrm{q}_{\mathrm{n}-1}\right) / \mathrm{T}_{\mathrm{n}-1}\right]
\end{array}\right] \tag{16}
$$


求得 $\omega$ ，即可计算样条的系数：

$$
\left\{\begin{array}{l}
\mathrm{a}_{\mathrm{k} 0}=\mathrm{q}_{\mathrm{k}} \\
\mathrm{a}_{\mathrm{k} 1}=\left(\mathrm{q}_{\mathrm{k}+1}-\mathrm{q}_{\mathrm{k}}\right) / \mathrm{T}_{\mathrm{k}}-\mathrm{T}_{\mathrm{k}}\left(\omega_{\mathrm{k}+1}+2 \omega_{\mathrm{k}}\right) / 6 \\
\mathrm{a}_{\mathrm{k} 2}=\omega_{\mathrm{k}} / 2 \\
\mathrm{a}_{\mathrm{k} 3}=\left(\omega_{\mathrm{k}+1}-\omega_{\mathrm{k}}\right) /\left(6 \mathrm{~T}_{\mathrm{k}}\right)
\end{array}\right. \tag{17}
$$


``` matlab
clc;
clear;
close all;

t = [0, 5, 7, 8, 10, 15, 18]'; %递增时间序列
q = [3, -2, -5, 0, 6, 12, 8]';
v0 = 2; %起始速度
vn = -3; %结束速度
deltaT = 0.001; %插补周期

n = length(t); % n >= 4
T = diff(t);
a = zeros(n, 1);
b = zeros(n, 1);
c = zeros(n, 1);
d = zeros(n, 1);

for i = 1 : n - 1
    a(i + 1) = T(i);
end

b(1) = 2.0 * T(1);
for i = 2 : n - 1
    b(i) = 2.0 * (T(i - 1) + T(i));
end
b(n) = 2.0 * T(n - 1);

for i = 1 : n - 1
    c(i) = T(i);
end

d(1) = 6.0 * ((q(2) - q(1)) / T(1) - v0);
for i = 2 : n - 1
   d(i) = 6.0 * ((q(i + 1) - q(i)) / T(i) - (q(i) - q(i - 1)) / T(i - 1)); 
end
d(n) = 6.0 * (vn - (q(n) - q(n - 1)) / T(n - 1));

[w, sta] = solve_tridiagonal_matrix_equation(a, b, c, d, n);
if sta == 0
    error('三对角矩阵方程求解出错!');
end

time = [];
pos = [];
vel = [];
acc = [];
time = [time; t(1)];
pos = [pos; q(1)];
vel = [vel; v0];
acc = [acc; w(1)];
for i = 1 : n - 1
    tt = (t(i) + deltaT : deltaT : t(i + 1))';
    a0 = q(i);
    a1 = (q(i + 1) - q(i)) / T(i) - T(i) * (w(i + 1) + 2.0 * w(i)) / 6.0;
    a2 = 0.5 * w(i);
    a3 = (w(i + 1) - w(i)) / (6.0 * T(i));

    time = [time; tt];
    pos = [pos; a0 + (tt - t(i)) .* (a1 + (tt - t(i)) .* (a2 + a3 .* (tt - t(i))))];
    vel = [vel; a1 + (tt - t(i)) .* (2.0 * a2 + 3.0 * a3 .* (tt - t(i)))];
    acc = [acc; 2.0 * a2 + 6.0 * a3 .* (tt - t(i))];
end
if abs(time(end) - t(n)) > 1.0e-8
    time = [time; t(n)];
    pos = [pos; q(n)];
    vel = [vel; vn];
    acc = [acc; w(n)];
end

figure(1)
subplot(3, 1, 1)
plot(time, pos);
hold on
plot(t, q, 'ro');
xlabel('t')
ylabel('pos')

subplot(3, 1, 2)
plot(time, vel);
xlabel('t')
ylabel('vel')

subplot(3, 1, 3)
plot(time, acc);
xlabel('t')
ylabel('acc')

```

<img
  src="https://i-blog.csdnimg.cn/blog_migrate/3ed21d74631c328d2464b0e3ca790cd7.png" 
  alt="fig x"
  width="500"
  style="height: auto;"
/>


### 3、两种计算方法的比较

By ChatGPT

这两种方法的主要区别在于你选择了哪一类节点信息作为主要的未知量，从而导致了建立连续性约束时的方程形式不同，具体来说：

1. **基于速度求解（通过速度计算三次样条系数）：**  
   - **未知量选取**：把各分段连接处的速度（即一阶导数）作为主要未知量（已知起始速度 $v_0$ 和结束速度 $v_n$，中间速度 $v_1, v_2, \ldots, v_{n-1}$ 待求）。  
   - **构造约束条件**：利用位置连续、速度连续和加速度连续条件，利用每段多项式在起点和终点处必须满足位置和一阶导数的匹配，从而可以用公式（3）推导出每段的系数，并且利用相邻段在连接处加速度的连续性（公式（5））得到一个关于中间速度的三对角线性系统（公式（6）和矩阵形式（7））。  
   - **优点**：当你能较好地估计或给出边界速度时，直接以速度作为未知量可以比较直观地控制曲线的斜率（方向），计算过程简单，构造的三对角系统结构也相对简单。

2. **基于加速度求解（通过加速度计算三次样条系数）：**  
   - **未知量选取**：将各节点处的加速度（即二阶导数）作为主要未知量（在节点处的加速度 $ \omega_0, \omega_1, \ldots, \omega_n $ 中，通常给出边界加速度，而中间的待求）。  
   - **构造约束条件**：首先假设每段内加速度在区间上线性变化（公式（8）），然后积分得到速度（公式（9））和位置，接着利用连接点处的速度连续性以及加速度在节点处的定义（公式（10））构造方程，再加上端点处由给定边界条件（公式（12）和（13））推导出的附加关系，最终得到一个关于加速度的三对角系统（公式（14）中 $A$ 与 $c$ 的构造）。  
   - **特点**：这种方法把重点放在了曲线的曲率（加速度）上，能够更自然地反映曲线的弯曲性质，同时允许在曲线端点同时给定速度和加速度（为此通常需要通过插入额外的辅助点来构造合适的节点序列）。  
   - **优点与应用**：在某些应用中（比如需要控制运动的平滑性或受力分析），加速度信息可能更为关键；另外，如果你希望明确地约束曲率变化，那么使用加速度作为未知量更直观。

**总结对比：**

- **未知量的不同**：  
  - 速度法：以中间节点的速度 $v_1,\ldots,v_{n-1}$ 为未知量；  
  - 加速度法：以节点处的加速度 $\omega_0, \omega_1, \ldots, \omega_n$ 为未知量（为同时满足给定的端点速度和加速度，需要在第一段和最后一段插入辅助节点）。

- **构造方程的不同**：  
  - 速度法直接利用位置、速度和加速度的连续性得到关于速度的三对角系统；  
  - 加速度法则先假定加速度在每段内呈线性变化，通过积分构造速度与位置表达式，再利用连续性条件建立关于加速度的三对角系统，其系数表达式中会涉及到时间间隔的比值等更复杂的形式。

- **应用场景**：  
  - 如果只给定边界速度，且你对中间点的速度没有额外要求，速度法较为简单高效；  
  - 如果希望在端点同时约束速度和加速度，或者对曲线的弯曲（曲率）要求更高，则需要采用加速度法（并且通常需要插入辅助节点以消除过定性）。

因此，两种方法本质上是利用了相同的样条连续性要求，但侧重点不同：一种侧重于控制一阶导数（斜率），另一种侧重于控制二阶导数（曲率），这直接影响了所构造的方程形式以及最终求得的样条曲线的性质。

## 三、给定约束：周期三次样条 

周期三次样条为起始位置 $q_0$ 与结束位置 $q_n$ 相等（数据需满足的特性，非算法约束条件），起始速度 $v_0$ 与结束速度 $v_n$ 相等，起始加速度 $\mathrm{a}_0$ 与结束加速度 $\mathrm{a}_{\mathrm{n}}$ 相等。

对于周期三次样条，起始速度 $v_0$ ，结束速度 $v_n$ ，起始加速度 $a_0$ ，结束加速度 $a_n$ 不可以任意指定，需满足约束：

$$
\mathrm{v}_0=\dot{\mathrm{q}}_0\left(\mathrm{t}_0\right)=\dot{\mathrm{q}}_{\mathrm{n}-1}\left(\mathrm{t}_{\mathrm{n}}\right)=\mathrm{v}_{\mathrm{n}} \tag{18}
$$


$$
\ddot{\mathrm{q}}_0\left(\mathrm{t}_0\right)=2 \mathrm{a}_{0,2}=2 \mathrm{a}_{\mathrm{n}-1,2}+6 \mathrm{a}_{\mathrm{n}-1,3} \mathrm{~T}_{\mathrm{n}-1}=\ddot{\mathrm{q}}_{\mathrm{n}-1}\left(\mathrm{t}_{\mathrm{n}}\right) \tag{19}
$$


结合式（4）（7）可得矩阵方程：

$$
\begin{gathered}
{\left[\begin{array}{cccccc}
2\left(\mathrm{~T}_{\mathrm{n}-1}+\mathrm{T}_0\right) & \mathrm{T}_{\mathrm{n}-1} & 0 & \cdots & 0 & \mathrm{~T}_0 \\
\mathrm{~T}_1 & 2\left(\mathrm{~T}_0+\mathrm{T}_1\right) & \mathrm{T}_0 & 0 & \vdots & 0 \\
0 & & \ddots & & \vdots \\
\vdots & 0 & & \mathrm{~T}_{\mathrm{n}-2} & 2\left(\mathrm{~T}_{\mathrm{n}-3}+\mathrm{T}_{\mathrm{n}-2}\right) & \mathrm{T}_{\mathrm{n}-3} \\
\mathrm{~T}_{\mathrm{n}-2} & 0 & \cdots & 0 & \mathrm{~T}_{\mathrm{n}-1} \\
0 & 2\left(\mathrm{~T}_{\mathrm{n}-2}+\mathrm{T}_{\mathrm{n}-1}\right)
\end{array}\right]\left[\begin{array}{c}
\mathrm{v}_0 \\
\mathrm{v}_1 \\
\mathrm{v}_2 \\
\vdots \\
\mathrm{v}_{\mathrm{n}-2} \\
\mathrm{v}_{\mathrm{n}-1}
\end{array}\right]} \\
\quad=\left[\begin{array}{c}
3\left[\mathrm{~T}_{\mathrm{n}-1}^2\left(\mathrm{q}_1-\mathrm{q}_0\right)+\mathrm{T}_0^2\left(\mathrm{q}_{\mathrm{n}-1}-\mathrm{q}_{\mathrm{n}-2}\right)\right] /\left(\mathrm{T}_{\mathrm{n}-1} \mathrm{~T}_0\right) \\
3\left[\mathrm{~T}_0^2\left(\mathrm{q}_2-\mathrm{q}_1\right)+\mathrm{T}_1^2\left(\mathrm{q}_1-\mathrm{q}_0\right)\right] /\left(\mathrm{T}_0 \mathrm{~T}_1\right) \\
3\left[\mathrm{~T}_1^2\left(\mathrm{q}_3-\mathrm{q}_2\right)+\mathrm{T}_2^2\left(\mathrm{q}_2-\mathrm{q}_1\right)\right] /\left(\mathrm{T}_1 \mathrm{~T}_2\right) \\
\vdots \\
3\left[\mathrm{~T}_{\mathrm{n}-3}^2 3\left(\mathrm{q}_{\mathrm{n}-1}-\mathrm{q}_{\mathrm{n}-2}\right)+\mathrm{T}_{\mathrm{n}-2}^2\left(\mathrm{q}_{\mathrm{n}-2}-\mathrm{q}_{\mathrm{n}-3}\right)\right] /\left(\mathrm{T}_{\mathrm{n}-3} \mathrm{~T}_{\mathrm{n}-2}\right) \\
3\left[\mathrm{~T}_{\mathrm{n}-2}^2\left(\mathrm{q}_{\mathrm{n}}-\mathrm{q}_{\mathrm{n}-1}\right)+\mathrm{T}_{\mathrm{n}-1}^2\left(\mathrm{q}_{\mathrm{n}-1}-\mathrm{q}_{\mathrm{n}-2}\right)\right] /\left(\mathrm{T}_{\mathrm{n}-2} \mathrm{~T}_{\mathrm{n}-1}\right)
\end{array}\right]
\end{gathered} \tag{20}
$$

式（20）为循环三对角矩阵方程，可以利用Sherman－Morrison 公式快速且稳定地求解，而无需求解矩阵的逆。参考附录一：三对角方程与循环三对角方程数值解


``` matlab

clc;
clear;
close all;

t = [0, 5, 7, 8, 10, 15, 18]'; %递增时间序列
q = [3, -2, -5, 0, 6, 12, 3]'; %首尾数据一致
deltaT = 0.001; %插补周期
forwardPeriodCount = 2;
backwardPeriodCount = 1;

n = length(t);
T = diff(t);
a = zeros(n - 1, 1);
b = zeros(n - 1, 1);
c = zeros(n - 1, 1);
d = zeros(n - 1, 1);

a(1 : n - 1) = T(1 : n - 1);

b(1) = 2.0 * (T(n - 1) + T(1));
for i = 2 : n - 1
    b(i) = 2.0 * (T(i - 1) + T(i));
end

c(1) = T(n - 1);
for i = 2 : n - 2
    c(i) = T(i - 1);
end
c(n - 1) = T(n - 2);

d(1) = 3.0 * (T(n - 1)^2 * (q(2) - q(1)) + T(1)^2 * (q(n) - q(n - 1))) / (T(n - 1) * T(1));
for i = 2 : n - 1
    d(i) = 3.0 * (T(i - 1)^2 * (q(i + 1) - q(i)) + T(i)^2 * (q(i) - q(i - 1))) / (T(i - 1) * T(i));
end

[v, sta] = solve_cyclic_tridiagonal_matrix_equation(a, b, c, d, n - 1);
if sta == 0
    error('循环三对角矩阵方程求解出错!');
end
v = [v; v(1)];

time = [];
pos = [];
vel = [];
acc = [];
time = [time; t(1)];
pos = [pos; q(1)];
vel = [vel; v(1)];
acc = [acc; 2.0 * (3.0 * (q(2) - q(1)) / T(1) - 2.0 * v(1) - v(2)) / T(1)];
for i = 1 : n - 1
    tt = (t(i) + deltaT : deltaT : t(i + 1))';
    a0 = q(i);
    a1 = v(i);
    a2 = (3.0 * (q(i + 1) - q(i)) / T(i) - 2.0 * v(i) - v(i + 1)) / T(i);
    a3 = (2.0 * (q(i) - q(i + 1)) / T(i) + v(i) + v(i + 1)) / T(i)^2;
    
    time = [time; tt];
    pos = [pos; a0 + (tt - t(i)) .* (a1 + (tt - t(i)) .* (a2 + a3 .* (tt - t(i))))];
    vel = [vel; a1 + (tt - t(i)) .* (2.0 * a2 + 3.0 * a3 .* (tt - t(i)))];
    acc = [acc; 2.0 * a2 + 6.0 * a3 .* (tt - t(i))];
end
if abs(time(end) - t(n)) > 1.0e-8
    a2 = (3.0 * (q(n) - q(n - 1)) / T(n - 1) - 2.0 * v(n - 1) - v(n)) / T(n - 1);
    a3 = (2.0 * (q(n - 1) - q(n)) / T(n - 1) + v(n - 1) + v(n)) / T(n - 1)^2;
    time = [time; t(n)];
    pos = [pos; q(n)];
    vel = [vel; v(n)];
    acc = [acc; 2.0 * a2 + 6.0 * a3 .* T(n - 1)];
end

period = time(end) - time(1);

figure(1)
subplot(3, 1, 1)
plot(time, pos, 'k');
hold on
plot(t, q, 'ko');
for i = 1 : forwardPeriodCount
    plot(time + i * period, pos, '--');
    plot(t + i * period, q, 'bo');
end
for i = 1 : backwardPeriodCount
    plot(time - i * period, pos, '--');
    plot(t - i * period, q, 'bo');
end
xlabel('t')
ylabel('pos')

subplot(3, 1, 2)
plot(time, vel, 'k');
hold on
for i = 1 : forwardPeriodCount
    plot(time + i * period, vel, '--');
end
for i = 1 : backwardPeriodCount
    plot(time - i * period, vel, '--');
end
xlabel('t')
ylabel('vel')

subplot(3, 1, 3)
plot(time, acc, 'k');
hold on
for i = 1 : forwardPeriodCount
    plot(time + i * period, acc, '--');
end
for i = 1 : backwardPeriodCount
    plot(time - i * period, acc, '--');
end
xlabel('t')
ylabel('acc')


```


<img
  src="https://i-blog.csdnimg.cn/blog_migrate/9b9b6d7511db01a8e175660daf44c3dc.png"
  alt="fig z"
  width="500"
  style="height: auto;"
/>



## 四、给定约束：给定起始速度 $\mathrm{v}_0$ 、结束速度 $\mathrm{v}_{\mathrm{n}}$ ，起始加速度 $\mathrm{a}_0$ 、结束加速度 $\mathrm{a}_{\mathrm{n}}$

对于三次样条，一般无法同时指定起始速度与加速度，结束速度与加速度。然而可以通过在第一段以及最后一段样条插入两个额外点以实现。假设样条插值有 $\mathrm{n}-1$ 个点序列：


$$
\left\{\begin{array}{l}
\mathbf{t}=\left[\mathrm{t}_0, \mathrm{t}_2, \mathrm{t}_3, \ldots, \mathrm{t}_{\mathrm{n}-3}, \mathrm{t}_{\mathrm{n}-2}, \mathrm{t}_{\mathrm{n}}\right]^{\mathrm{T}} \\
\boldsymbol{q}=\left[\begin{array}{lllllll}
q_0, & q_2, & q_3, & \ldots & q_{n-3}, & q_{n-2}, & q_n
\end{array}\right]^T
\end{array}\right. \tag{21}
$$

在第一段以及最后一段样条插入两个额外点：$\left(\overline{\mathrm{t}}_1, \overline{\mathrm{q}}_1\right),\left(\overline{\mathrm{t}}_{\mathrm{n}-1}, \overline{\mathrm{q}}_{\mathrm{n}-1}\right)$ ，不妨取其为相邻两个点的中点。新的点序列：
$$
\left\{\begin{array}{l}
\overline{\mathbf{t}}=\left[\mathrm{t}_0, \overline{\mathrm{t}}_1, \mathrm{t}_2, \mathrm{t}_3, \ldots, \mathrm{t}_{\mathrm{n}-3}, \mathrm{t}_{\mathrm{n}-2}, \overline{\mathrm{t}}_{\mathrm{n}-1}, \mathrm{t}_{\mathrm{n}}\right]^{\mathrm{T}} \\
\overline{\mathbf{q}}=\left[\mathrm{q}_0, \overline{\mathrm{q}}_1, \mathrm{q}_2, \mathrm{q}_3, \ldots, \mathrm{q}_{\mathrm{n}-3}, \mathrm{q}_{\mathrm{n}-2}, \overline{\mathrm{q}}_{\mathrm{n}-1}, \mathrm{q}_{\mathrm{n}}\right]^{\mathrm{T}}
\end{array}\right. \tag{22}
$$

根据式（12）（13）可得：
$$
\left\{\begin{array}{l}
\mathrm{q}_1=\mathrm{q}_0+\mathrm{T}_0 \mathrm{v}_0+\mathrm{T}_0^2 \mathrm{a}_0 / 3+\mathrm{T}_0^2 \omega_1 / 6 \\
\mathrm{q}_{\mathrm{n}-1}=\mathrm{q}_{\mathrm{n}}-\mathrm{T}_{\mathrm{n}-1} \mathrm{v}_{\mathrm{n}}+\mathrm{T}_{\mathrm{n}-1}^2 \mathrm{a}_{\mathrm{n}} / 3+\mathrm{T}_{\mathrm{n}-1}^2 \omega_{\mathrm{n}-1} / 6
\end{array}\right. \tag{23}
$$
根据式（15）（16）（23）可得矩阵方程：
$$
\mathrm{A} \omega=\mathrm{c} \tag{24}
$$
其中：
$$
\boldsymbol{A}=\left[\begin{array}{ccccc}
2 T_1+T_0\left(3+\frac{T_0}{T_1}\right) & T_1 & 0 & \cdots & \\
T_1-\frac{T_0^2}{T_1} & 2\left(T_1+T_2\right) & T_2 & & 0 \\
0 & T_2 & 2\left(T_2+T_3\right) & T_3 & \\
\vdots & \vdots & & \\
\vdots & & T_{n-3} & 2\left(T_{n-3}+T_{n-2}\right) & T_{n-2}-\frac{T_n^2}{T_{n-1}} \\
0 & \cdots & 0 & T_{n-2} & 2 T_{n-2}+T_{n-1}\left(3+\frac{T_n-1}{T_{n-2}}\right.
\end{array}\right] \tag{25}
$$

$$
c=\left[\begin{array}{c}
6\left(\frac{q_2-q_0}{T_1}-\mathrm{v}_0\left(1+\frac{T_0}{T_1}\right)-\mathrm{a}_0\left(\frac{1}{2}+\frac{T_0}{3 T_1}\right) T_0\right) \\
6\left(\frac{q_3-q_2}{T_2}-\frac{q_2-q_0}{T_1}+\mathrm{v}_0 \frac{T_0}{T_1}+\mathrm{a}_0 \frac{T_0^2}{3 T_1}\right) \\
6\left(\frac{q_4-q_3}{T_3}-\frac{q_3-q_2}{T_2}\right) \\
\vdots \\
6 \\
6\left(\frac{q_n-q_{n-2}}{T_{n-2}}-\frac{q_{n-2}-q_{n-3}}{T_{n-3}}-\mathrm{v}_n \frac{T_{n-1}}{T_{n-2}}+\mathrm{a}_n \frac{T_{n-1}^2}{3 T_{n-2}}\right) \\
6\left(\frac{q_{n-2}-q_{n-3}}{T_{n-3}-q_n} T_{n-2}+\mathrm{v}_n\left(1+\frac{T_{n-1}}{T_{n-2}}\right)-\mathrm{a}_n\left(\frac{1}{2}+\frac{T_{n-1}}{3 T_{n-2}}\right) T_{n-1}\right)
\end{array}\right] \tag{26}
$$

``` matlab
clc;
clear;
close all;

t = [0, 5, 7, 8, 10, 15, 18]'; %递增时间序列
q = [3, -2, -5, 0, 6, 12, 8]';
v0 = 2; %起始速度
vn = -3; %结束速度
a0 = 0; %起始加速度
an = 0; %结束加速度
deltaT = 0.001; %插补周期

%前两个时间之间、后两个时间之间插入时间
n = length(t); % n >= 4
t2 = 0.5 * (t(1) + t(2));
t_n_1 = 0.5 * (t(n) + t(n - 1));
t = [t(1); t2; t(2 : n - 1); t_n_1; t(n)];
q = [q(1); 0.0; q(2 : n - 1); 0.0; q(n)];

n = n + 2;
T = diff(t);
a = zeros(n - 2, 1);
b = zeros(n - 2, 1);
c = zeros(n - 2, 1);
d = zeros(n - 2, 1);

a(2) = T(2) - T(1)^2 / T(2);
for i = 3 : n - 2
    a(i) = T(i);
end

b(1) = 2.0 * T(2) + T(1) * (3.0 + T(1) / T(2));
for i = 2 : n - 3
    b(i) = 2.0 * (T(i) + T(i + 1));
end
b(n - 2) = 2.0 * T(n - 2) + T(n - 1) * (3.0 + T(n - 1) / T(n - 2));

for i = 1 : n - 4
    c(i) = T(i + 1);
end
c(n - 3) = T(n - 2) - T(n - 1)^2 / T(n - 2);

d(1) = 6.0 * ((q(3) - q(1)) / T(2) - v0 * (1.0 + T(1) / T(2)) - a0 * (0.5 + T(1) / (3.0 * T(2))) * T(1));
d(2) = 6.0 * ((q(4) - q(3)) / T(3) - (q(3) - q(1)) / T(2) + v0 * T(1) / T(2) + a0 * T(1)^2 / (3.0 * T(2)));
for i = 3 : n - 4
    d(i) = 6.0 * ((q(i + 2) - q(i + 1)) / T(i + 1) - (q(i + 1) - q(i)) / T(i));
end
d(n - 3) = 6.0 * ((q(n) - q(n - 2)) / T(n - 2) - (q(n - 2) - q(n - 3)) / T(n - 3) - vn * T(n - 1) / T(n - 2) + an * T(n - 1)^2 / (3.0 * T(n - 2)));
d(n - 2) = 6.0 * ((q(n - 2) - q(n)) / T(n - 2) + vn * (1.0 + T(n - 1) / T(n - 2)) - an * (0.5 + T(n - 1) / (3.0 * T(n - 2))) * T(n - 1));

[w, sta] = solve_tridiagonal_matrix_equation(a, b, c, d, n - 2);
if sta == 0
    error('三对角矩阵方程求解出错!');
end

w = [a0; w; an];

q(2) = q(1) + T(1) * v0 + T(1)^2 * a0 / 3.0 + T(1)^2 * w(2) / 6.0;
q(n - 1) = q(n) - T(n - 1) * vn + T(n - 1)^2 * an / 3.0 + T(n - 1)^2 * w(n - 1) / 6.0;

time = [];
pos = [];
vel = [];
acc = [];
time = [time; t(1)];
pos = [pos; q(1)];
vel = [vel; v0];
acc = [acc; a0];
for i = 1 : n - 1
    tt = (t(i) + deltaT : deltaT : t(i + 1))';
    b0 = q(i);
    b1 = (q(i + 1) - q(i)) / T(i) - T(i) * (w(i + 1) + 2.0 * w(i)) / 6.0;
    b2 = 0.5 * w(i);
    b3 = (w(i + 1) - w(i)) / (6.0 * T(i));
    
    time = [time; tt];
    pos = [pos; b0 + (tt - t(i)) .* (b1 + (tt - t(i)) .* (b2 + b3 .* (tt - t(i))))];
    vel = [vel; b1 + (tt - t(i)) .* (2.0 * b2 + 3.0 * b3 .* (tt - t(i)))];
    acc = [acc; 2.0 * b2 + 6.0 * b3 .* (tt - t(i))];
end
if abs(time(end) - t(n)) > 1.0e-8
    time = [time; t(n)];
    pos = [pos; q(n)];
    vel = [vel; vn];
    acc = [acc; an];
end

figure(1)
subplot(3, 1, 1)
plot(time, pos);
hold on
plot(t([1, 3 : n - 2, n]), q([1, 3 : n - 2, n]), 'ro');
plot(t([2, n - 1]), q([2, n - 1]), 'bs');
xlabel('t')
ylabel('pos')

subplot(3, 1, 2)
plot(time, vel);
hold on
plot(t([1, n]), [v0, vn], 'ro');
xlabel('t')
ylabel('vel')

subplot(3, 1, 3)
plot(time, acc);
hold on
plot(t([1, n]), [a0, an], 'ro');
xlabel('t')
ylabel('acc')

```

<img src="https://i-blog.csdnimg.cn/blog_migrate/a6c7a71ad4272f430d10c71e032848f2.png"/>

## 五、参考文献

《Trajectory Planning for Automatic Machines and Robots》
4.4 Cubic Splin
A.5 Numerical Solution of Tridiagonal Systems



----

## 附录 1.  三对角方程与循环三对角方程数值解

下面介绍三对角矩阵方程和循环三对角矩阵方程的数值求解方法，重点讨论 Thomas 算法（追赶法）以及如何处理循环（周期性）边界条件的问题。

---

### 1. 三对角矩阵方程的数值求解

考虑形如
$$
a_i\, x_{i-1} + b_i\, x_i + c_i\, x_{i+1} = d_i,\quad i=1,2,\ldots,n,
$$
其中约定 $a_1=0$（第一行没有左侧项）和 $c_n=0$（最后一行没有右侧项）。这种形式对应于一个三对角矩阵系统。

#### Thomas 算法（追赶法）

Thomas 算法是一种专门针对三对角系统的高效解法，其基本思路为利用前向消去将原系统化为上三角系统，再采用回代求解。步骤如下：

1. **前向消去**

   对于 $i=2,3,\ldots,n$：
   - 计算消元因子
     $$
     m_i = \frac{a_i}{b_{i-1}}.
     $$
   - 更新主对角元和右端项：
     $$
     b_i \leftarrow b_i - m_i\, c_{i-1},\qquad d_i \leftarrow d_i - m_i\, d_{i-1}.
     $$
     

   此过程消去了所有 $a_i$ 项，从而将矩阵变为上三角形。

2. **回代求解**

   先求最后一个未知数：
   $$
   x_n = \frac{d_n}{b_n}.
   $$
   然后对于 $i=n-1,\,n-2,\ldots,1$ 依次计算：
   $$
   x_i = \frac{d_i - c_i\, x_{i+1}}{b_i}.
   $$
   

该方法的时间复杂度为 $O(n)$，在矩阵对角占优或正定时具有良好的数值稳定性，避免了直接求矩阵逆带来的高计算代价和可能的不稳定性。

---

### 2. 循环三对角矩阵方程的数值求解

#### 问题描述

循环（或周期性）三对角系统与标准三对角系统的区别在于，除了传统的三对角部分，还存在额外的边界耦合项，即第一行和最后一行之间存在非零连接。常见的写法为：
$$
\begin{aligned}
b_1\, x_1 + c_1\, x_2 + r\, x_n &= d_1,\\
a_i\, x_{i-1} + b_i\, x_i + c_i\, x_{i+1} &= d_i,\quad i=2,\ldots,n-1,\\
p\, x_1 + a_n\, x_{n-1} + b_n\, x_n &= d_n,
\end{aligned}
$$
其中 $r$ 和 $p$ 表示连接首尾的非零元素。

由于 Thomas 算法不能直接处理这两个“循环项”，因此需要采用特殊方法将问题转化为标准三对角问题求解。

#### 方法一：利用 Sherman-Morrison 公式

该方法的基本思路是将原矩阵分解为一个标准三对角矩阵加上一个低秩修正项。设
$$
A = T + u\, v^T,
$$
其中：
- $T$ 为将循环项（即角元素）置零后的三对角矩阵，
- $u$ 和 $v$ 是选取合适的向量，通常可以取
  $$
  u = \begin{bmatrix} r \\ 0 \\ \vdots \\ 0 \\ p \end{bmatrix},\qquad v = \begin{bmatrix} 1 \\ 0 \\ \vdots \\ 0 \\ 1 \end{bmatrix}.
  $$

利用 Sherman-Morrison 公式，设先解两组三对角系统：
1. 求解
   $$
   T\, z = d,
   $$
   得到初步解 $z$。
2. 求解
   $$
   T\, y = u,
   $$
   得到辅助解 $y$。

最后，利用公式
$$
x = z - \frac{v^T z}{1 + v^T y}\, y,
$$
得到原系统 $A x = d$ 的解。由于 $T$ 是标准三对角矩阵，可以使用 Thomas 算法高效求解这两个系统。

#### 方法二：分解法（补充解法）

另一种常见思路是将循环三对角系统拆分为两个标准三对角系统。步骤概括如下：

1. **构造辅助问题**

   先将原系统中循环项暂时忽略，即构造与之对应的三对角系统
   $$
   T\, y = d,
   $$
   并求出解 $y$。

2. **考虑循环修正**

   设原解可表示为
   $$
   x = y + \alpha\, z,
   $$
   其中 $z$ 是一个需要补充的修正向量，满足相应的标准三对角系统
   $$
   T\, z = w,
   $$
   此处 $w$ 与循环项相关（例如可以取 $w = [r, 0, \ldots, 0, p]^T$）。
   
3. **确定系数 $\alpha$**

   将 $x = y + \alpha\, z$ 代回原来的循环边界条件，求解出系数 $\alpha$。
   
4. **得到最终解**

   将 $\alpha$ 带回，即可得到系统的解 $x$。

这种方法同样将问题归约为求解两个标准三对角系统，然后通过调整补充解来满足周期性条件。

---

#### 总结

- **标准三对角系统**：利用 Thomas 算法，通过前向消去和回代法以 $O(n)$ 时间求解，适用于对角占优或正定的情况，数值上较为稳定。
- **循环三对角系统**：由于存在额外的边界耦合项，常用的方法是将原问题分解为一个标准三对角问题加上低秩修正。常见的技术包括利用 Sherman-Morrison 公式或分解法，从而将循环系统转化为两个标准三对角系统求解。

这两种方法均避免了直接求矩阵逆，从而大大降低了计算量，并提高了解法的稳定性。



### 3. C++ 示例代码

下面给出一个简单的开源 C++ 示例代码，包含了利用 Thomas 算法求解标准三对角系统和利用 Sherman-Morrison 公式求解循环三对角系统的实现。你可以将下面的代码作为参考，根据实际问题进行修改和扩展。

---

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 利用 Thomas 算法求解三对角系统
// 系统形式： a[i]*x[i-1] + b[i]*x[i] + c[i]*x[i+1] = d[i] ，其中 i=0,...,n-1
// 注意：a[0] 和 c[n-1] 不使用（可以置为0）
vector<double> thomasSolver(const vector<double>& a, const vector<double>& b, const vector<double>& c, const vector<double>& d) {
    int n = d.size();
    vector<double> c_star(n, 0.0), d_star(n, 0.0), x(n, 0.0);
    vector<double> b_mod = b;  // 复制一份b，因为需要在前向消去过程中修改

    // 第一步：前向消去
    c_star[0] = c[0] / b_mod[0];
    d_star[0] = d[0] / b_mod[0];
    for (int i = 1; i < n; i++) {
        double m = b_mod[i] - a[i] * c_star[i - 1];
        // 如果不是最后一个元素，更新 c_star
        if (i < n - 1)
            c_star[i] = c[i] / m;
        d_star[i] = (d[i] - a[i] * d_star[i - 1]) / m;
    }

    // 第二步：回代求解
    x[n - 1] = d_star[n - 1];
    for (int i = n - 2; i >= 0; i--) {
        x[i] = d_star[i] - c_star[i] * x[i + 1];
    }
    return x;
}

// 利用 Sherman-Morrison 公式求解循环三对角系统
// 系统形式：
//    b[0]*x[0] + c[0]*x[1] + r*x[n-1] = d[0]
//    a[i]*x[i-1] + b[i]*x[i] + c[i]*x[i+1] = d[i], i = 1,...,n-2
//    p*x[0] + a[n-1]*x[n-2] + b[n-1]*x[n-1] = d[n-1]
//
// 其中 r 和 p 分别为第一行和最后一行中额外的循环项
vector<double> cyclicThomasSolver(const vector<double>& a, const vector<double>& b, const vector<double>& c,
                                    const vector<double>& d, double r, double p) {
    int n = d.size();
    
    // 求解 T*z = d，其中 T 为去除循环项后的三对角矩阵
    vector<double> z = thomasSolver(a, b, c, d);
    
    // 构造向量 u 对应循环项 u = [r, 0, ..., 0, p]^T
    vector<double> u(n, 0.0);
    u[0]     = r;
    u[n - 1] = p;
    
    // 求解 T*y = u
    vector<double> y = thomasSolver(a, b, c, u);
    
    // 计算修正系数 alpha = (z[0] + z[n-1]) / (1 + y[0] + y[n-1])
    double numerator   = z[0] + z[n - 1];
    double denominator = 1.0 + y[0] + y[n - 1];
    double alpha       = numerator / denominator;
    
    // 最终解： x = z - alpha * y
    vector<double> x(n, 0.0);
    for (int i = 0; i < n; i++) {
        x[i] = z[i] - alpha * y[i];
    }
    
    return x;
}

int main() {
    // 示例：求解一个三对角系统
    // 系数数组要求：a[0] 和 c[n-1]置0，不参与计算
    // 例如，对于方程组：
    //   4*x0 + 1*x1             = 7
    //   1*x0 + 4*x1 + 1*x2        = 8
    //           1*x1 + 4*x2      = 7
    vector<double> a = {0, 1, 1};      // 下对角（a[0]不使用）
    vector<double> b = {4, 4, 4};        // 主对角
    vector<double> c = {1, 1, 0};        // 上对角（c[n-1]不使用）
    vector<double> d = {7, 8, 7};        // 右侧常数

    // 利用 Thomas 算法求解
    vector<double> sol = thomasSolver(a, b, c, d);
    cout << "三对角系统的解:" << endl;
    for (double x : sol) {
        cout << x << " ";
    }
    cout << "\n" << endl;
    
    // 示例：求解一个循环三对角系统
    // 在上例的基础上，假设第一行多了一个 r 项，最后一行多了一个 p 项
    double r = 1.0;  // 第一行末尾的循环项
    double p = 1.0;  // 最后一行首部的循环项
    
    vector<double> cyclicSol = cyclicThomasSolver(a, b, c, d, r, p);
    cout << "循环三对角系统的解:" << endl;
    for (double x : cyclicSol) {
        cout << x << " ";
    }
    cout << endl;
    
    return 0;
}
```

---

#### 代码说明

1. **Thomas 算法部分**  
   - 函数 `thomasSolver` 实现了标准三对角系统的前向消去和回代过程。  
   - 数组 `a`、`b`、`c` 分别代表下对角、主对角和上对角，其中 `a[0]` 和 `c[n-1]`不使用；`d` 为右侧常数向量。

2. **循环三对角系统部分**  
   - 利用 Sherman-Morrison 公式，将带有循环项的矩阵分解为去除循环项的三对角矩阵 T 加上低秩修正。  
   - 分别调用 `thomasSolver` 求解两个辅助系统，最后利用修正系数 $ \alpha $ 得到最终解。

该代码仅为示例，你可以在 GitHub 或其他开源平台上找到更多成熟的实现。代码发布时采用了宽松的开源许可证，你可以根据自己的需求进行修改和使用。





## 附录 2.  给定起始与终止速度的三次样条插值C++示例

理论上，对于只用速度边界条件的常规三次样条来说，若只有一段（即只有两个位置点），4个约束（两个位置、两个端点速度）就足以唯一确定一个三次多项式；但当使用多段样条且要求中间段二阶连续时，一般需要至少3个位置点才能构造连续曲线。

而在同时要求给定起始速度与加速度、以及结束速度与加速度的情形下（即模型中所讨论的通过在第一段和最后一段插入辅助点来实现双重端点约束的方案），原始的插值点数必须足够多，以便经过扩展后能够构造出满足所有约束的系统。通常这种模型至少要求原始位置点数不少于4个（即至少4个采样点），这样经过在第一段和最后一段分别插入一个辅助点后，总节点数才足够建立求解辅助变量（加速度）的三对角方程，并进而确定所有分段多项式的系数。

因此，根据该模型如果同时给定端点速度与加速度，至少需要4个位置点。

#### 1、三次样条的系数通过速度计算

下面给出一个模块化的 C++ 代码示例，该代码实现了基于给定起始与终止速度的三次样条插值，内部利用 Thomas 算法（追赶法）求解对角占优的三对角矩阵方程，计算各段样条的系数，并提供了在任意时刻处进行插值（及求导、求二阶导）的接口。代码结构为一个类 “CubicSpline”，便于调用与集成。完整代码如下：

---

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
#include <algorithm>
#include <cmath>

using namespace std;

//======================
// Thomas 算法：求解三对角矩阵方程
// 求解方程组： a[i]*x[i-1] + b[i]*x[i] + c[i]*x[i+1] = d[i] , i = 0,...,n-1
// 注意：a[0] 与 c[n-1]不使用（应置 0）
//======================
vector<double> solveTridiagonal(const vector<double>& a, const vector<double>& b,
                                const vector<double>& c, const vector<double>& d) {
    int n = d.size();
    vector<double> cp(n, 0.0), dp(n, 0.0), x(n, 0.0);
    if(n == 0) return x;
    cp[0] = c[0] / b[0];
    dp[0] = d[0] / b[0];
    for (int i = 1; i < n; ++i) {
        double m = b[i] - a[i] * cp[i - 1];
        if (i < n - 1)
            cp[i] = c[i] / m;
        dp[i] = (d[i] - a[i] * dp[i - 1]) / m;
    }
    x[n - 1] = dp[n - 1];
    for (int i = n - 2; i >= 0; --i)
        x[i] = dp[i] - cp[i] * x[i + 1];
    return x;
}

//======================
// CubicSpline 类：实现基于速度约束的三次样条插值
// 给定节点 (t_i, q_i) 以及端点速度 v0 与 vn 后，计算每段样条的系数
// 支持在任意 t 内查询样条值、速度（1阶导）与加速度（2阶导）
//======================
class CubicSpline {
public:
    // 设置节点数据（要求 t 为严格递增序列，q 与 t 数量相同）
    void setPoints(const vector<double>& t, const vector<double>& q) {
        if(t.size() != q.size() || t.size() < 2)
            throw invalid_argument("点数不匹配或少于两个点!");
        t_ = t;
        q_ = q;
        n_ = t_.size();
    }
    
    // 设置起始与终止速度
    void setBoundaryVelocities(double v0, double vn) {
        v0_ = v0;
        vn_ = vn;
    }
    
    // 根据公式（见说明）计算三次样条各段系数
    // 模型参考：给定起始速度与结束速度，利用位置、速度、加速度连续性条件
    void computeSpline() {
        if(t_.empty() || q_.empty() || n_ < 2)
            throw runtime_error("数据未设置或点数不足!");
        // 计算各段时间间隔： T[i] = t_[i+1] - t_[i], i = 0,...,n_-2
        T_.resize(n_ - 1);
        for (size_t i = 0; i < n_ - 1; ++i) {
            T_[i] = t_[i + 1] - t_[i];
            if(T_[i] <= 0)
                throw runtime_error("时间节点必须严格递增!");
        }
        
        // 根据加速度连续性条件可构造对角占优的三对角系统，未知量为中间各节点速度 v[1]...v[n_-2]
        // 对应方程（参见文献或说明）：对于 k = 0,..., n_-3，
        //   T_[k+1] * v_[k] + 2*(T_[k] + T_[k+1])* v_[k+1] + T_[k] * v_[k+2]
        //     = 3*(T_[k]^2*(q_[k+2]-q_[k+1]) + T_[k+1]^2*(q_[k+1]-q_[k]))/(T_[k]*T_[k+1])
        // 对于 k = 0 与 k = n_-2（对应首、尾方程）要引入边界已知量 v0_ 与 vn_
        size_t m = n_ - 2; // 中间未知速度个数
        vector<double> a_tridiag(m, 0.0), b_tridiag(m, 0.0), c_tridiag(m, 0.0), d_tridiag(m, 0.0);
        if(m > 0) {
            // 第 1 个方程 (i = 0，对应 k = 0)
            a_tridiag[0] = 0.0;  // 第一行无下对角
            b_tridiag[0] = 2.0 * (T_[0] + T_[1]);
            if(m > 1)
                c_tridiag[0] = T_[0];
            d_tridiag[0] = 3.0 * ( T_[0]*T_[0]*(q_[2] - q_[1]) + T_[1]*T_[1]*(q_[1] - q_[0]) )
                           / (T_[0] * T_[1]) - T_[1] * v0_;
            // 中间方程： i = 1, 2, ..., m-2
            for (size_t i = 1; i < m - 1; ++i) {
                a_tridiag[i] = T_[i + 1];
                b_tridiag[i] = 2.0 * (T_[i] + T_[i + 1]);
                c_tridiag[i] = T_[i];
                d_tridiag[i] = 3.0 * ( T_[i]*T_[i]*(q_[i + 2] - q_[i + 1]) + T_[i + 1]*T_[i + 1]*(q_[i + 1] - q_[i]) )
                               / (T_[i] * T_[i + 1]);
            }
            // 最后一个方程： i = m-1，对应 k = n_-2
            if(m >= 1) {
                size_t i = m - 1;
                a_tridiag[i] = T_[i + 1]; // 注意：i+1 = m = n_-1，正好是 T_ 的最后一个元素
                b_tridiag[i] = 2.0 * (T_[i] + T_[i + 1]);
                // 最后一行无上对角，故 c_tridiag[i] 保持为 0
                d_tridiag[i] = 3.0 * ( T_[i]*T_[i]*(q_[i + 2] - q_[i + 1]) + T_[i + 1]*T_[i + 1]*(q_[i + 1] - q_[i]) )
                               / (T_[i] * T_[i + 1]) - T_[i] * vn_;
            }
        }
        
        // 求解三对角方程（若 m==0 则表示只有两个点，不需插值）
        vector<double> v_interior;
        if(m > 0)
            v_interior = solveTridiagonal(a_tridiag, b_tridiag, c_tridiag, d_tridiag);
        
        // 构造完整的速度向量： v_[0] = v0_, v_[1..n_-2] 为求得的解， v_[n_-1] = vn_
        v_.resize(n_);
        v_[0] = v0_;
        for (size_t i = 0; i < m; ++i)
            v_[i + 1] = v_interior[i];
        v_[n_ - 1] = vn_;
        
        // 根据公式（见说明），计算各段样条的系数：
        // 设第 i 段 (i = 0,..., n_-2) 多项式为：
        //   q_i(t) = a0_[i] + a1_[i]*(t - t_[i]) + a2_[i]*(t - t_[i])^2 + a3_[i]*(t - t_[i])^3
        // 其中：
        //   a0_[i] = q_[i]
        //   a1_[i] = v_[i]
        //   a2_[i] = [3*(q_[i+1]-q_[i])/T_[i] - 2*v_[i] - v_[i+1]] / T_[i]
        //   a3_[i] = [2*(q_[i]-q_[i+1])/T_[i] + v_[i] + v_[i+1]] / (T_[i]*T_[i])
        a0_.resize(n_ - 1);
        a1_.resize(n_ - 1);
        a2_.resize(n_ - 1);
        a3_.resize(n_ - 1);
        for (size_t i = 0; i < n_ - 1; ++i) {
            a0_[i] = q_[i];
            a1_[i] = v_[i];
            a2_[i] = (3.0 * (q_[i + 1] - q_[i]) / T_[i] - 2.0 * v_[i] - v_[i + 1]) / T_[i];
            a3_[i] = (2.0 * (q_[i] - q_[i + 1]) / T_[i] + v_[i] + v_[i + 1]) / (T_[i] * T_[i]);
        }
        computed_ = true;
    }
    
    // 在任意 t_query 处计算样条插值值（位置）
    double evaluate(double t_query) const {
        if(!computed_)
            throw runtime_error("请先调用 computeSpline() 计算样条系数!");
        if(t_query < t_.front() || t_query > t_.back())
            throw out_of_range("查询时间超出样条插值范围!");
        size_t i = findInterval(t_query);
        double dt = t_query - t_[i];
        return a0_[i] + a1_[i]*dt + a2_[i]*dt*dt + a3_[i]*dt*dt*dt;
    }
    
    // 计算样条在 t_query 处的一阶导数（速度）
    double evaluateDerivative(double t_query) const {
        if(!computed_)
            throw runtime_error("请先调用 computeSpline() 计算样条系数!");
        if(t_query < t_.front() || t_query > t_.back())
            throw out_of_range("查询时间超出样条插值范围!");
        size_t i = findInterval(t_query);
        double dt = t_query - t_[i];
        return a1_[i] + 2.0*a2_[i]*dt + 3.0*a3_[i]*dt*dt;
    }
    
    // 计算样条在 t_query 处的二阶导数（加速度）
    double evaluateSecondDerivative(double t_query) const {
        if(!computed_)
            throw runtime_error("请先调用 computeSpline() 计算样条系数!");
        if(t_query < t_.front() || t_query > t_.back())
            throw out_of_range("查询时间超出样条插值范围!");
        size_t i = findInterval(t_query);
        double dt = t_query - t_[i];
        return 2.0*a2_[i] + 6.0*a3_[i]*dt;
    }
    
private:
    // 二分查找：找到满足 t_[i] <= t_query <= t_[i+1] 的区间 i
    size_t findInterval(double t_query) const {
        auto it = std::upper_bound(t_.begin(), t_.end(), t_query);
        size_t idx = std::distance(t_.begin(), it);
        if(idx == 0)
            return 0;
        if(idx >= t_.size())
            return t_.size() - 2;
        return idx - 1;
    }
    
    vector<double> t_;     // 节点时间
    vector<double> q_;     // 节点位置
    vector<double> T_;     // 各段时间间隔 T[i] = t_[i+1] - t_[i]
    vector<double> v_;     // 各节点速度（v0 与 vn 为边界给定，其余由三对角方程求得）
    vector<double> a0_, a1_, a2_, a3_; // 每段多项式系数
    double v0_, vn_;       // 起始与终止速度
    size_t n_;             // 节点数
    bool computed_ = false;
};

//======================
// 主函数：演示如何使用 CubicSpline 类
//======================
int main() {
    // 示例数据，与正文中 MATLAB 示例一致
    vector<double> t = {0, 5, 7, 8, 10, 15, 18};
    vector<double> q = {3, -2, -5, 0, 6, 12, 8};
    double v0 = 2.0;   // 起始速度
    double vn = -3.0;  // 结束速度

    // 构造样条对象，设置数据、边界速度，并计算样条系数
    CubicSpline spline;
    try {
        spline.setPoints(t, q);
        spline.setBoundaryVelocities(v0, vn);
        spline.computeSpline();
        
        // 在 [t.front(), t.back()] 内以一定步长进行插值（此处 dt = 0.001）
        double dt = 0.001;
        for (double time = t.front(); time <= t.back(); time += dt) {
            double pos = spline.evaluate(time);
            double vel = spline.evaluateDerivative(time);
            double acc = spline.evaluateSecondDerivative(time);
            // 为了减少输出量，每隔 1 秒打印一次结果
            if (fabs(fmod(time, 1.0)) < 1e-6)
                cout << "t = " << time << ", pos = " << pos 
                     << ", vel = " << vel << ", acc = " << acc << endl;
        }
    }
    catch(const exception &e) {
        cerr << "错误: " << e.what() << endl;
        return -1;
    }
    
    return 0;
}
```

---

##### 代码说明

1. **Thomas 算法**  
   函数 `solveTridiagonal` 实现了标准的追赶法，用于求解形如  
   $$
   a_i\,x_{i-1} + b_i\,x_i + c_i\,x_{i+1} = d_i
   $$
   的三对角系统（其中要求 $a_0=0,\, c_{n-1}=0$）。

2. **CubicSpline 类**  
   - **数据设置**：通过 `setPoints` 设置节点 $t_i$ 与位置 $q_i$；通过 `setBoundaryVelocities` 给出起始与终止速度。  
   - **系数计算**：`computeSpline` 内部先构造出各区间时间间隔，再根据加速度连续性条件构造出三对角系统求解内部节点速度，最后利用公式（4）计算每段的多项式系数。  
   - **插值接口**：`evaluate`、`evaluateDerivative`、`evaluateSecondDerivative` 分别用于计算样条在任意时刻处的位置、速度和加速度。

3. **主函数**  
   演示了如何构造 CubicSpline 对象、设置数据、计算样条系数以及以 0.001 秒的步长进行插值（此处仅每隔 1 秒打印一次结果）。

该代码模块化程度较高，便于集成到其他工程中使用，也可根据需要扩展其它边界条件或插值方法。

当前提供的代码只实现了**基于速度约束**求解三次样条系数的方法，即利用已知的起始速度 $v_0$ 和终止速度 $v_n$，通过构造满足位置、速度、加速度连续性条件的三对角矩阵（见公式 (6) 和 (7)），求解中间节点的速度，然后利用公式

$$
\begin{aligned}
a_{k,0} &= q_k, \\
a_{k,1} &= v_k, \\
a_{k,2} &= \frac{3\,(q_{k+1}-q_k)/T_k - 2\,v_k - v_{k+1}}{T_k}, \\
a_{k,3} &= \frac{2\,(q_k-q_{k+1})/T_k + v_k + v_{k+1}}{T_k^2},
\end{aligned}
$$

计算每段多项式的系数。这正是基于速度的方法，对应文中**通过速度计算三次样条曲线所有约束**的部分。

而另一种方法——**基于加速度求解**的方案（对应文中公式 (8)-(17)）需要构造一个不同的三对角系统（其中未知量为各节点的加速度 $\omega$），再通过对应公式计算多项式系数，目前代码中并没有体现这一部分。当前代码仅体现了基于速度的求解方法，以下则为利用加速度求解三次样条系数的方案。

#### 2、三次样条的系数通过加速度计算

下面给出一份完整的 C++ 示例代码，该代码实现了基于加速度方法的三次样条插值。代码中利用加速度连续性和边界速度构造三对角系统（见公式 (12)-(16)），利用 Thomas 算法求解各节点的加速度，然后根据公式 (17) 计算每段多项式的系数。最终，代码提供了在任意时刻处计算位置、速度与加速度的接口，便于调用与集成。

---

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
#include <algorithm>
#include <cmath>

using namespace std;

//======================
// Thomas 算法：求解三对角矩阵方程
// 求解方程组： a[i]*x[i-1] + b[i]*x[i] + c[i]*x[i+1] = d[i] , i = 0,...,n-1
// 要求 a[0] = 0, c[n-1] = 0
//======================
vector<double> solveTridiagonal(const vector<double>& a, const vector<double>& b,
                                const vector<double>& c, const vector<double>& d) {
    int n = d.size();
    vector<double> cp(n, 0.0), dp(n, 0.0), x(n, 0.0);
    if(n == 0) return x;
    cp[0] = c[0] / b[0];
    dp[0] = d[0] / b[0];
    for (int i = 1; i < n; ++i) {
        double m = b[i] - a[i] * cp[i - 1];
        if (i < n - 1)
            cp[i] = c[i] / m;
        dp[i] = (d[i] - a[i] * dp[i - 1]) / m;
    }
    x[n - 1] = dp[n - 1];
    for (int i = n - 2; i >= 0; --i)
        x[i] = dp[i] - cp[i] * x[i + 1];
    return x;
}

//======================
// CubicSplineAcceleration 类：基于加速度方法的三次样条插值
// 给定节点 (t_i, q_i) 以及边界速度 v0 与 vn，先求加速度 ω_i (i=0,...,n-1)
// 再计算每段多项式系数：
//   a0 = q[i],
//   a1 = (q[i+1]-q[i])/T[i] - T[i]*(ω[i+1] + 2ω[i])/6,
//   a2 = ω[i] / 2,
//   a3 = (ω[i+1]-ω[i])/(6*T[i])
//======================
class CubicSplineAcceleration {
public:
    // 设置节点数据（要求 t 为严格递增序列，q 与 t 数量相同）
    void setPoints(const vector<double>& t, const vector<double>& q) {
        if(t.size() != q.size() || t.size() < 2)
            throw invalid_argument("点数不匹配或少于两个点!");
        t_ = t;
        q_ = q;
        n_ = t_.size();
    }
    
    // 设置边界速度
    void setBoundaryVelocities(double v0, double vn) {
        v0_ = v0;
        vn_ = vn;
    }
    
    // 根据加速度方法计算三次样条各段系数
    // 过程：
    // 1. 计算各段时间间隔 T[i] = t[i+1]-t[i], i = 0,...,n_-2
    // 2. 构造三对角系统 A*ω = d, 其中 ω 的维数为 n_（节点个数）
    //    A 的构造（0-indexed）：
    //      row0:    A[0,0] = 2*T[0],    A[0,1] = T[0]
    //      row i (1<= i <= n_-2): A[i,i-1] = T[i-1], A[i,i] = 2*(T[i-1]+T[i]), A[i,i+1] = T[i]
    //      row n_-1: A[n_-1, n_-2] = T[n_-2], A[n_-1, n_-1] = 2*T[n_-2]
    //    d 的构造：
    //      d[0] = 6*((q[1]-q[0])/T[0] - v0)
    //      d[i] = 6*((q[i+1]-q[i])/T[i] - (q[i]-q[i-1])/T[i-1])  (i=1,...,n_-2)
    //      d[n_-1] = 6*(vn - (q[n_-1]-q[n_-2])/T[n_-2])
    // 3. 利用 Thomas 算法求解 ω
    // 4. 根据 ω 计算各段样条多项式系数（每段 [t[i], t[i+1]]）
    void computeSpline() {
        if(t_.empty() || q_.empty() || n_ < 2)
            throw runtime_error("数据未设置或点数不足!");
        
        // 1. 计算各段时间间隔 T
        T_.resize(n_ - 1);
        for (size_t i = 0; i < n_ - 1; ++i) {
            T_[i] = t_[i+1] - t_[i];
            if(T_[i] <= 0)
                throw runtime_error("时间节点必须严格递增!");
        }
        
        // 2. 构造三对角系统 A*ω = d，其中 ω 的维数为 n_
        // a, b, c 为三对角矩阵的下对角、主对角、上对角，均大小为 n_
        vector<double> a(n_, 0.0), b(n_, 0.0), c(n_, 0.0), d_vec(n_, 0.0);
        // 下对角：对于 i = 1,..., n_-1, a[i] = T_[i-1]
        a[0] = 0.0;
        for (size_t i = 1; i < n_; ++i)
            a[i] = T_[i-1];
        
        // 主对角：
        // row0: b[0] = 2*T_[0]
        // row i (1<=i<=n_-2): b[i] = 2*(T_[i-1] + T_[i])
        // row n_-1: b[n_-1] = 2*T_[n_-2]
        b[0] = 2.0 * T_[0];
        for (size_t i = 1; i < n_ - 1; ++i)
            b[i] = 2.0 * (T_[i-1] + T_[i]);
        b[n_ - 1] = 2.0 * T_[n_ - 2];
        
        // 上对角：
        // 对于 i = 0,..., n_-2, c[i] = T_[i]；最后一行无上对角，c[n_-1]=0
        for (size_t i = 0; i < n_ - 1; ++i)
            c[i] = T_[i];
        c[n_ - 1] = 0.0;
        
        // 右端项 d_vec:
        // d[0] = 6*((q[1]-q[0])/T_[0] - v0)
        d_vec[0] = 6.0 * ((q_[1] - q_[0]) / T_[0] - v0_);
        // 对于 i = 1,..., n_-2:
        for (size_t i = 1; i < n_ - 1; ++i) {
            d_vec[i] = 6.0 * ((q_[i+1] - q_[i]) / T_[i] - (q_[i] - q_[i-1]) / T_[i-1]);
        }
        // d[n_-1] = 6*(vn - (q[n_-1]-q[n_-2])/T_[n_-2])
        d_vec[n_ - 1] = 6.0 * (vn_ - (q_[n_ - 1] - q_[n_ - 2]) / T_[n_ - 2]);
        
        // 3. 求解三对角系统 A*ω = d_vec，得到 ω（加速度向量，大小为 n_）
        omega_ = solveTridiagonal(a, b, c, d_vec);
        
        // 4. 根据 ω 计算每段多项式系数（区间 i=0,..., n_-2）
        // 每段多项式形式：
        //   q_i(t) = a0[i] + a1[i]*(t-t[i]) + a2[i]*(t-t[i])^2 + a3[i]*(t-t[i])^3, t in [t[i], t[i+1]]
        // 其中：
        //   a0[i] = q_[i]
        //   a1[i] = (q_[i+1]-q_[i])/T_[i] - T_[i]*(omega_[i+1] + 2*omega_[i]) / 6
        //   a2[i] = omega_[i] / 2
        //   a3[i] = (omega_[i+1]-omega_[i]) / (6*T_[i])
        size_t segs = n_ - 1;
        a0_.resize(segs);
        a1_.resize(segs);
        a2_.resize(segs);
        a3_.resize(segs);
        for (size_t i = 0; i < segs; ++i) {
            a0_[i] = q_[i];
            a1_[i] = (q_[i+1] - q_[i]) / T_[i] - T_[i] * (omega_[i+1] + 2.0 * omega_[i]) / 6.0;
            a2_[i] = omega_[i] / 2.0;
            a3_[i] = (omega_[i+1] - omega_[i]) / (6.0 * T_[i]);
        }
        
        computed_ = true;
    }
    
    // 在任意 t_query 处计算样条插值值（位置）
    double evaluate(double t_query) const {
        if(!computed_)
            throw runtime_error("请先调用 computeSpline() 计算样条系数!");
        if(t_query < t_.front() || t_query > t_.back())
            throw out_of_range("查询时间超出样条插值范围!");
        size_t i = findInterval(t_query);
        double dt = t_query - t_[i];
        return a0_[i] + a1_[i]*dt + a2_[i]*dt*dt + a3_[i]*dt*dt*dt;
    }
    
    // 计算样条在 t_query 处的一阶导数（速度）
    double evaluateDerivative(double t_query) const {
        if(!computed_)
            throw runtime_error("请先调用 computeSpline() 计算样条系数!");
        if(t_query < t_.front() || t_query > t_.back())
            throw out_of_range("查询时间超出样条插值范围!");
        size_t i = findInterval(t_query);
        double dt = t_query - t_[i];
        return a1_[i] + 2.0*a2_[i]*dt + 3.0*a3_[i]*dt*dt;
    }
    
    // 计算样条在 t_query 处的二阶导数（加速度）
    double evaluateSecondDerivative(double t_query) const {
        if(!computed_)
            throw runtime_error("请先调用 computeSpline() 计算样条系数!");
        if(t_query < t_.front() || t_query > t_.back())
            throw out_of_range("查询时间超出样条插值范围!");
        size_t i = findInterval(t_query);
        double dt = t_query - t_[i];
        return 2.0*a2_[i] + 6.0*a3_[i]*dt;
    }
    
private:
    // 二分查找：找到满足 t_[i] <= t_query <= t_[i+1] 的区间 i
    size_t findInterval(double t_query) const {
        auto it = std::upper_bound(t_.begin(), t_.end(), t_query);
        size_t idx = std::distance(t_.begin(), it);
        if(idx == 0)
            return 0;
        if(idx >= t_.size())
            return t_.size() - 2;
        return idx - 1;
    }
    
    vector<double> t_;          // 节点时间
    vector<double> q_;          // 节点位置
    vector<double> T_;          // 各段时间间隔 T[i] = t[i+1] - t[i]
    vector<double> omega_;      // 各节点加速度（由三对角系统求得），大小为 n_
    vector<double> a0_, a1_, a2_, a3_; // 每段多项式系数
    double v0_, vn_;            // 边界（起始、终止）速度
    size_t n_;                  // 节点数
    bool computed_ = false;
};

//======================
// 主函数：演示如何使用 CubicSplineAcceleration 类
//======================
int main() {
    // 示例数据（与 MATLAB 示例类似）
    vector<double> t = {0, 5, 7, 8, 10, 15, 18};
    vector<double> q = {3, -2, -5, 0, 6, 12, 8};
    double v0 = 2.0;   // 起始速度
    double vn = -3.0;  // 结束速度

    // 构造样条对象，设置数据与边界速度，计算样条系数
    CubicSplineAcceleration spline;
    try {
        spline.setPoints(t, q);
        spline.setBoundaryVelocities(v0, vn);
        spline.computeSpline();
        
        // 在 [t.front(), t.back()] 内以一定步长进行插值（此处 dt = 0.001）
        double dt = 0.001;
        for (double time = t.front(); time <= t.back(); time += dt) {
            double pos = spline.evaluate(time);
            double vel = spline.evaluateDerivative(time);
            double acc = spline.evaluateSecondDerivative(time);
            // 为了减少输出量，每隔 1 秒打印一次结果
            if (fabs(fmod(time, 1.0)) < 1e-6)
                cout << "t = " << time << ", pos = " << pos 
                     << ", vel = " << vel << ", acc = " << acc << endl;
        }
    }
    catch(const exception &e) {
        cerr << "错误: " << e.what() << endl;
        return -1;
    }
    
    return 0;
}
```

---

##### 代码说明

1. **Thomas 算法函数**  
   函数 `solveTridiagonal` 实现了标准追赶法，用于求解形如  
   $$
   a_i\,\omega_{i-1} + b_i\,\omega_i + c_i\,\omega_{i+1} = d_i
   $$
   的三对角系统，其中要求 `a[0]=0`、`c[n-1]=0`。

2. **CubicSplineAcceleration 类**  
   - **数据设置**：通过 `setPoints` 设置节点 $t_i$ 与位置 $q_i$；通过 `setBoundaryVelocities` 给定起始与终止速度。  
   - **系数计算**：`computeSpline` 内部首先计算各段时间间隔，再构造加速度法对应的三对角系统（参考公式 (12)-(16)），利用 Thomas 算法求解各节点的加速度 ω，最后利用公式 (17) 计算每段多项式的系数。  
   - **插值接口**：`evaluate`、`evaluateDerivative`、`evaluateSecondDerivative` 分别用于计算样条在任意时刻处的位置、速度和加速度。

3. **主函数**  
   演示如何构造 CubicSplineAcceleration 对象、设置数据与边界速度、计算样条系数，并以 0.001 秒的步长进行插值（此处每隔 1 秒输出一次结果）。

该代码独立实现了利用加速度求解三次样条系数的方法，便于与基于速度的方法一起集成与比较。



## 附录 3. 给定起始与结束的速度与加速度条件下的三次样条插值

下面 C++ 示例代码，实现了在同时约束起始速度与加速度、结束速度与加速度条件下的三次样条插值。  

该方法的基本思路是：  

1. 对原始节点序列  
   $$
   t = [t_0,\,t_1,\,t_2,\ldots,t_{n-1}],\quad q = [q_0,\,q_1,\,\ldots,q_{n-1}]
   $$
   插入两个“辅助”点（分别插在第一段与最后一段）：  
   $$
   \bar t_1=0.5\,(t_0+t_1),\quad \bar t_{n-1}=0.5\,(t_{n-2}+t_{n-1})
   $$
   同时构造新的节点序列  
   $$
   \bar t = [\,t_0,\;\bar t_1,\;t_1,t_2,\ldots,t_{n-2},\;\bar t_{n-1},\;t_{n-1}\,]
   $$
   对应位置值中辅助点初值先置零，后面利用边界条件恢复（见下面公式）。  
   
2. 设新的节点数为 $N=n+2$；计算各段时间间隔  
   $$
   T_i=\bar t_{i+1}-\bar t_{i},\quad i=0,\ldots,N-2.
   $$
3. 构造一个对角占优的三对角系统来求解各节点“加速度” $ \omega $（注意：这里的 $\omega$ 为辅助变量，后续用来计算多项式系数）。系统的大小为 $N-2$（对应去掉第一与最后一节点，其值由给定条件确定）：  
   
   对应 MATLAB 代码中（式（25）–（26））的系数，在 C++中我们采用下述（0‐索引）方式填写系统的三个对角线和右端项 $d$  
   - 下对角 $a$（长度 $m=N-2$）：  
     - $ a[0] = 0 $  
     - $ a[1] = T[1]-T[0]^2/T[1] $  
     - 对 $ i=2,\ldots,m-1$： $ a[i] = T[i] $  
   - 主对角 $b$（长度 $m$）：  
     - $ b[0]= 2\,T[1] + T[0]\Bigl(3+\frac{T[0]}{T[1]}\Bigr) $  
     - 对 $ i=1,\ldots,m-2$： $ b[i] = 2\Bigl(T[i]+T[i+1]\Bigr) $  
     - $ b[m-1]= 2\,T[m-1] + T[m]\Bigl(3+\frac{T[m]}{T[m-1]}\Bigr) $  
   - 上对角 $c$（长度 $m$）：  
     - 对 $ i=0,\ldots,m-2$： $ c[i] = T[i+1] $  
     - $ c[m-1]= T[m] - \dfrac{T[m]^2}{T[m-1]} $  
   - 右端项 $d$（长度 $m$）：  
     - $ d[0] = 6\Bigl[\dfrac{\bar q[2]-\bar q[0]}{T[1]} - v_0\Bigl(1+\frac{T[0]}{T[1]}\Bigr) - a_0\Bigl(\frac{1}{2}+\frac{T[0]}{3\,T[1]}\Bigr)T[0]\Bigr] $  
     - $ d[1] = 6\Bigl[\dfrac{\bar q[3]-\bar q[2]}{T[2]} - \dfrac{\bar q[2]-\bar q[0]}{T[1]} + v_0\,\dfrac{T[0]}{T[1]} + a_0\,\dfrac{T[0]^2}{3\,T[1]}\Bigr] $  
     - 对 $ i=2,\ldots,m-3$：  
       $$
       d[i]=6\Bigl[\dfrac{\bar q[i+2]-\bar q[i+1]}{T[i+1]}-\dfrac{\bar q[i+1]-\bar q[i]}{T[i]}\Bigr]
       $$
     - 倒数第二方程：  
       $$
       d[m-2]=6\Bigl[\dfrac{\bar q[N-1]-\bar q[N-3]}{T[N-2]}-\dfrac{\bar q[N-3]-\bar q[N-4]}{T[N-3]}-v_n\,\frac{T[N-2]}{T[N-3]}+a_n\,\frac{T[N-2]^2}{3\,T[N-3]}\Bigr]
       $$
     - 最后一方程：  
       $$
       d[m-1]=6\Bigl[\dfrac{\bar q[N-3]-\bar q[N-1]}{T[N-2]}+v_n\Bigl(1+\frac{T[N-2]}{T[N-3]}\Bigr)-a_n\Bigl(\frac{1}{2}+\frac{T[N-2]}{3\,T[N-3]}\Bigr)T[N-2]\Bigr]
       $$
       
   
   （这里：$N=$扩展后节点数；下标含义：例如 $T[0]$ 对应 $\mathrm{T}_0$，$T[1]$ 对应 $\mathrm{T}_1$，……；同理 $\bar q[i]$ 为扩展后的位置值。）  
   
4. 利用 Thomas 算法求解三对角系统，得到内部节点的 $\omega$（记为 $w$）；再构造完整的 $\omega$ 向量  
   $$
   \omega = [\,a_0,\;w_1,\;w_2,\ldots,w_{N-2},\;a_n\,]
   $$
5. 根据边界条件重新“修正”两个辅助点的位置值：  
   $$
   \begin{aligned}
   \bar q_1 &= q_0 + T_0\,v_0 + \frac{T_0^2\,a_0}{3} + \frac{T_0^2\,\omega_1}{6},\$$1mm]
   \bar q_{N-2} &= q_{n-1} - T_{N-2}\,v_n + \frac{T_{N-2}^2\,a_n}{3} + \frac{T_{N-2}^2\,\omega_{N-2}}{6}\,.
   \end{aligned}
   $$
6. 对每一段 $[\,\bar t_i,\bar t_{i+1}]$（$i=0,\ldots,N-2$），利用求得的 $\omega$ 计算三次多项式系数  
   对第 $i$ 段设  
   $$
   q_i(t)=b_{0,i}+b_{1,i}(t-\bar t_i)+b_{2,i}(t-\bar t_i)^2+b_{3,i}(t-\bar t_i)^3\,,
   $$
   其中  
   $$
   \begin{aligned}
   b_{0,i}&=\bar q_i,\$$1mm]
   b_{1,i}&=\frac{\bar q_{i+1}-\bar q_i}{T_i}-\frac{T_i}{6}\Bigl(\omega_{i+1}+2\,\omega_i\Bigr),\$$1mm]
   b_{2,i}&=\frac{\omega_i}{2},\$$1mm]
   b_{3,i}&=\frac{\omega_{i+1}-\omega_i}{6\,T_i}\,.
   \end{aligned}
   $$
7. 利用上述多项式可在任意时刻进行位置、速度、加速度的查询。  

下面的代码给出了一个模块化的 C++ 实现，其中 Thomas 算法函数与类 `CubicSplineBoundary` 均已实现，可方便调用与集成。

---

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
#include <cmath>
#include <algorithm>

using namespace std;

//======================
// Thomas 算法：求解三对角矩阵方程
// 求解方程组： a[i]*x[i-1] + b[i]*x[i] + c[i]*x[i+1] = d[i],  i=0,...,n-1
// 要求 a[0] = 0, c[n-1] = 0
//======================
vector<double> solveTridiagonal(const vector<double>& a, const vector<double>& b,
                                const vector<double>& c, const vector<double>& d) {
    int n = d.size();
    vector<double> cp(n, 0.0), dp(n, 0.0), x(n, 0.0);
    if(n == 0) return x;
    cp[0] = c[0] / b[0];
    dp[0] = d[0] / b[0];
    for (int i = 1; i < n; ++i) {
        double m = b[i] - a[i] * cp[i - 1];
        if(i < n - 1)
            cp[i] = c[i] / m;
        dp[i] = (d[i] - a[i] * dp[i - 1]) / m;
    }
    x[n - 1] = dp[n - 1];
    for (int i = n - 2; i >= 0; --i)
        x[i] = dp[i] - cp[i] * x[i + 1];
    return x;
}

//======================
// CubicSplineBoundary 类：在同时约束起始/结束速度与加速度条件下的三次样条插值
// 内部先在第一段与最后一段插入辅助点，构造扩展的节点序列，然后构造三对角系统求解辅助变量 ω，
// 最后计算各段三次多项式的系数，并提供在任意时刻处计算位置、速度、加速度的接口。
//======================
class CubicSplineBoundary {
public:
    // 设置原始节点数据（要求 t 为严格递增序列，q 与 t 数量相同）
    void setPoints(const vector<double>& t, const vector<double>& q) {
        if(t.size() != q.size() || t.size() < 2)
            throw invalid_argument("点数不匹配或少于两个点!");
        orig_t_ = t;
        orig_q_ = q;
    }
    
    // 设置边界条件：起始速度 v0 与加速度 a0，结束速度 vn 与加速度 an
    void setBoundaryConditions(double v0, double vn, double a0, double an) {
        v0_ = v0;
        vn_ = vn;
        a0_ = a0;
        an_ = an;
    }
    
    // 计算扩展节点、构造三对角系统求解 ω，再计算每段多项式系数
    void computeSpline() {
        // 1. 构造扩展节点
        size_t nOrig = orig_t_.size();
        // 扩展后节点数： N = nOrig + 2
        size_t N = nOrig + 2;
        ext_t_.resize(N);
        ext_q_.resize(N);
        // 第一个点不变
        ext_t_[0] = orig_t_[0];
        ext_q_[0] = orig_q_[0];
        // 插入辅助点 1：取原 t0 与 t1 的中点
        ext_t_[1] = 0.5 * (orig_t_[0] + orig_t_[1]);
        ext_q_[1] = 0.0; // 待求（后面更新）
        // 中间部分：原 t[1] ... t[nOrig-2]
        for (size_t i = 2; i < N - 2; ++i) {
            ext_t_[i] = orig_t_[i - 1]; // 注意下标转换
            ext_q_[i] = orig_q_[i - 1];
        }
        // 插入辅助点 2：取原 t[nOrig-2] 与 t[nOrig-1] 的中点
        ext_t_[N - 2] = 0.5 * (orig_t_[nOrig - 2] + orig_t_[nOrig - 1]);
        ext_q_[N - 2] = 0.0; // 待求
        // 最后一点
        ext_t_[N - 1] = orig_t_[nOrig - 1];
        ext_q_[N - 1] = orig_q_[nOrig - 1];
        
        // 2. 计算各段时间间隔 T, 共 N-1 个
        size_t M_T = N - 1;
        T_.resize(M_T);
        for (size_t i = 0; i < M_T; ++i) {
            T_[i] = ext_t_[i + 1] - ext_t_[i];
            if (T_[i] <= 0)
                throw runtime_error("时间节点必须严格递增!");
        }
        
        // 3. 构造三对角系统来求解内部 ω（辅助变量），系统规模 m = N - 2
        size_t m = N - 2;
        vector<double> a(m, 0.0), b(m, 0.0), c(m, 0.0), d(m, 0.0);
        // 下对角 a：
        a[0] = 0.0;
        if(m > 1)
            a[1] = T_[1] - (T_[0]*T_[0]) / T_[1];
        for (size_t i = 2; i < m; ++i)
            a[i] = T_[i];
        
        // 主对角 b：
        b[0] = 2.0 * T_[1] + T_[0] * (3.0 + T_[0] / T_[1]);
        for (size_t i = 1; i < m - 1; ++i)
            b[i] = 2.0 * (T_[i] + T_[i + 1]);
        b[m - 1] = 2.0 * T_[m - 1] + T_[m] * (3.0 + T_[m] / T_[m - 1]);
        
        // 上对角 c：
        for (size_t i = 0; i < m - 1; ++i)
            c[i] = T_[i + 1];
        c[m - 1] = T_[m] - (T_[m]*T_[m]) / T_[m - 1];
        
        // 右端项 d：
        // d[0] 对应辅助点 1的方程
        d[0] = 6.0 * ( (ext_q_[2] - ext_q_[0]) / T_[1]
                      - v0_ * (1.0 + T_[0] / T_[1])
                      - a0_ * (0.5 + T_[0] / (3.0 * T_[1])) * T_[0] );
        if(m > 1) {
            d[1] = 6.0 * ( (ext_q_[3] - ext_q_[2]) / T_[2]
                          - (ext_q_[2] - ext_q_[0]) / T_[1]
                          + v0_ * T_[0] / T_[1]
                          + a0_ * T_[0]*T_[0] / (3.0 * T_[1]) );
        }
        for (size_t i = 2; i < m - 2; ++i) {
            d[i] = 6.0 * ( (ext_q_[i+2] - ext_q_[i+1]) / T_[i+1]
                          - (ext_q_[i+1] - ext_q_[i]) / T_[i] );
        }
        if(m >= 4) {
            // 倒数第二方程：注意下标转换
            d[m - 2] = 6.0 * ( (ext_q_[N - 1] - ext_q_[N - 3]) / T_[N - 2]
                              - (ext_q_[N - 3] - ext_q_[N - 4]) / T_[N - 3]
                              - vn_ * T_[N - 2] / T_[N - 3]
                              + an_ * (T_[N - 2]*T_[N - 2]) / (3.0 * T_[N - 3]) );
            // 最后一方程
            d[m - 1] = 6.0 * ( (ext_q_[N - 3] - ext_q_[N - 1]) / T_[N - 2]
                              + vn_ * (1.0 + T_[N - 2] / T_[N - 3])
                              - an_ * (0.5 + T_[N - 2] / (3.0 * T_[N - 3])) * T_[N - 2] );
        }
        
        // 4. 求解三对角系统，得到内部 ω（记为 w_interior），再构造完整 ω 向量
        vector<double> w_interior = solveTridiagonal(a, b, c, d);
        w_.resize(N);
        w_[0] = a0_;
        for (size_t i = 0; i < m; ++i)
            w_[i + 1] = w_interior[i];
        w_[N - 1] = an_;
        
        // 5. 根据边界条件“修正”两个辅助点的 q 值
        ext_q_[1] = ext_q_[0] + T_[0]*v0_ + (T_[0]*T_[0]*a0_) / 3.0 + (T_[0]*T_[0]*w_[1]) / 6.0;
        ext_q_[N - 2] = ext_q_[N - 1] - T_[N - 2]*vn_ + (T_[N - 2]*T_[N - 2]*an_) / 3.0 + (T_[N - 2]*T_[N - 2]*w_[N - 2]) / 6.0;
        
        // 6. 对每一段 [ext_t_[i], ext_t_[i+1]] 计算三次多项式系数
        size_t segs = N - 1;
        a0_coef_.resize(segs);
        a1_coef_.resize(segs);
        a2_coef_.resize(segs);
        a3_coef_.resize(segs);
        for (size_t i = 0; i < segs; ++i) {
            a0_coef_[i] = ext_q_[i];
            a1_coef_[i] = (ext_q_[i+1] - ext_q_[i]) / T_[i] - T_[i]*(w_[i+1] + 2.0*w_[i]) / 6.0;
            a2_coef_[i] = w_[i] / 2.0;
            a3_coef_[i] = (w_[i+1] - w_[i]) / (6.0 * T_[i]);
        }
        
        computed_ = true;
    }
    
    // 在任意查询时间 t_query 内计算样条插值（位置）
    double evaluate(double t_query) const {
        if (!computed_)
            throw runtime_error("请先调用 computeSpline()!");
        if (t_query < ext_t_.front() || t_query > ext_t_.back())
            throw out_of_range("查询时间超出范围!");
        size_t i = findSegment(t_query);
        double dt = t_query - ext_t_[i];
        return a0_coef_[i] + a1_coef_[i]*dt + a2_coef_[i]*dt*dt + a3_coef_[i]*dt*dt*dt;
    }
    
    // 一阶导（速度）
    double evaluateDerivative(double t_query) const {
        if (!computed_)
            throw runtime_error("请先调用 computeSpline()!");
        if (t_query < ext_t_.front() || t_query > ext_t_.back())
            throw out_of_range("查询时间超出范围!");
        size_t i = findSegment(t_query);
        double dt = t_query - ext_t_[i];
        return a1_coef_[i] + 2.0*a2_coef_[i]*dt + 3.0*a3_coef_[i]*dt*dt;
    }
    
    // 二阶导（加速度）
    double evaluateSecondDerivative(double t_query) const {
        if (!computed_)
            throw runtime_error("请先调用 computeSpline()!");
        if (t_query < ext_t_.front() || t_query > ext_t_.back())
            throw out_of_range("查询时间超出范围!");
        size_t i = findSegment(t_query);
        double dt = t_query - ext_t_[i];
        return 2.0*a2_coef_[i] + 6.0*a3_coef_[i]*dt;
    }
    
private:
    // 二分查找：返回满足 ext_t_[i] <= t_query <= ext_t_[i+1] 的区间下标 i
    size_t findSegment(double t_query) const {
        auto it = std::upper_bound(ext_t_.begin(), ext_t_.end(), t_query);
        size_t idx = std::distance(ext_t_.begin(), it);
        if (idx == 0)
            return 0;
        if (idx >= ext_t_.size())
            return ext_t_.size() - 2;
        return idx - 1;
    }
    
    // 原始数据
    vector<double> orig_t_, orig_q_;
    // 扩展后的节点
    vector<double> ext_t_, ext_q_;
    // 各段间隔 T
    vector<double> T_;
    // 边界条件
    double v0_, vn_, a0_, an_;
    // 求解得到的辅助变量 ω（长度 = 扩展节点数）
    vector<double> w_;
    // 每段三次多项式系数
    vector<double> a0_coef_, a1_coef_, a2_coef_, a3_coef_;
    
    bool computed_ = false;
};

//======================
// 主函数：演示如何使用 CubicSplineBoundary 类
//======================
int main() {
    try {
        // 示例数据（与文中 MATLAB 示例类似）
        vector<double> t = {0, 5, 7, 8, 10, 15, 18};  // 原始时间节点
        vector<double> q = {3, -2, -5, 0, 6, 12, 8};   // 原始位置
        double v0 = 2.0;   // 起始速度
        double vn = -3.0;  // 结束速度
        double a0 = 0.0;   // 起始加速度
        double an = 0.0;   // 结束加速度
        double deltaT = 0.001;  // 插值步长
        
        CubicSplineBoundary spline;
        spline.setPoints(t, q);
        spline.setBoundaryConditions(v0, vn, a0, an);
        spline.computeSpline();
        
        // 示例：以 0.01 秒步长输出部分插值结果
        double t_start = spline.evaluate( t.front() ); // 仅为示例，实际应在 [ext_t[0], ext_t.back()]
        cout << "部分样条插值结果：" << endl;
        for (double time = t.front(); time <= t.back(); time += 0.01) {
            double pos = spline.evaluate(time);
            double vel = spline.evaluateDerivative(time);
            double acc = spline.evaluateSecondDerivative(time);
            cout << "t = " << time << ", pos = " << pos
                 << ", vel = " << vel << ", acc = " << acc << endl;
        }
    }
    catch (const exception &e) {
        cerr << "错误: " << e.what() << endl;
        return -1;
    }
    
    return 0;
}
```

---

### 代码说明

1. **Thomas 算法函数**  
   函数 `solveTridiagonal` 实现了标准追赶法，用于求解形如  
   $$
   a_i\,x_{i-1}+b_i\,x_i+c_i\,x_{i+1}=d_i
   $$
   的三对角系统（要求 $a[0]=0,\; c[n-1]=0$）。

2. **类 CubicSplineBoundary**  
   - **数据扩展**：在原始节点序列中，在第一段与最后一段分别插入辅助点（取相邻点的中点），构造扩展后的时间与位置向量。  
   - **构造三对角系统**：根据给定的边界速度 $v_0, v_n$ 及加速度 $a_0, a_n$ ，构造系统求解内部辅助变量 $\omega$（记为 w），详细系数参考注释。  
   - **计算多项式系数**：利用求得的 $\omega$ 及扩展节点，计算每段多项式的 4 个系数。  
   - **插值接口**：`evaluate`、`evaluateDerivative`、`evaluateSecondDerivative` 分别用于计算给定时刻处的位置、速度与加速度。

3. **主函数**  
   演示如何设置数据、边界条件并调用 `computeSpline()` 后，在整个时间区间内以一定步长输出插值结果。

该代码为模块化实现，可根据需要集成到其它工程中；如需绘图，可将插值数据导出或利用图形库进行显示。



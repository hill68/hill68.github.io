+++
date = '2025-05-21T17:07:50+08:00'
draft = false
title = 'κ轨迹平滑'
summary= "κ轨迹生成算法。κ=1：轨迹以最短时间过渡到下一个航段；κ=0：轨迹执行一个最小时间过渡，并直接通过航路点；求解κ∈[0,1]使轨迹具有与原始航路点路径等长"
weight = 2
+++


本文实现了一种实时算法，该算法生成 $\kappa$-轨迹。如果 $\kappa=1$，则 $\kappa$ 轨迹以最短时间从航路点段 $\overline{\mathbf{w}_{i-1} \mathbf{w}_{i}}$ 过渡到航路点段 $\overline{\mathbf{w}_{i} \mathbf{w}_{i+1}}$。如果 $\kappa=0$，则 $\kappa$-轨迹执行一个最小时间过渡，前提是它直接通过 $\mathbf{w}_{i}$。我们还将在本节中展示如何选择 $\kappa$，使得 $\kappa$-轨迹具有与原始航路点路径相同的路径长度。


![](https://cdn.mathpix.com/cropped/2022_07_18_9ff4460d05508d94c751g-14.jpg?height=374&width=508&top_left_y=168&top_left_x=383)

图6. 选择 $u$ 背后基本思想的图示。

算法的基本思想如图6所示。右转和左转约束分别由方程(10)-(11)给出的 $\mathcal{C}_{R}$ 和 $\mathcal{C}_{L}$ 在不同时间点表示。时间的进程由 $t_{1}, \ldots t_{6}$ 表示。为了避免图形过于复杂，图中未显示在时间 $t_{2}$ 和 $t_{5}$ 时的右转约束 $\mathcal{C}_{R}$。在时间 $t_{1}$ 时，DTS 正在跟踪航路点段 $\overline{\mathbf{w}_{i-1} \mathbf{w}_{i}}$。当左转圆 $\mathcal{C}_{L}$ 在时间 $t_{2}$ 与 $\mathcal{C}_{\mathbf{p}(\kappa)}$ 相交时，$u$ 被设置为 $-c$。左转约束被跟随，直到右转圆 $\mathcal{C}_{R}$ 在时间 $t_{3}$ 与 $\mathcal{C}_{\mathbf{p}(\kappa)}$ 完全一致。然后，DTS 变量 $u$ 被设置为 $+c$，右转约束被跟随，直到左转约束 $\mathcal{C}_{L}$ 在时间 $t_{4}$ 与航路点段 $\overline{\mathbf{w}_{i} \mathbf{w}_{i+1}}$ 相交。DTS 变量 $u$ 再次被设置为 $-c$，直到它在时间 $t_{5}$ 到达航路点段，此时 $u$ 被设置为零。

对于 $\kappa \in [0,1)$，图7展示了DTS选择 $u$ 的流程图。DTS算法的名义状态是跟踪当前的航路点路径段。由于方程(7)-(9)是通过固定采样率求解器求解的，因此无法通过简单地设置 $u=0$ 来跟踪路径。使用的跟踪算法将在第五节中讨论。当DTS开始跟踪当前航路点段时，首先计算 $\mathcal{C}_{p(\kappa)}$ 的位置。如果转弯是顺时针转弯，则监测约束圆 $\mathcal{C}_{L}$，直到它与 $\mathcal{C}_{p(\kappa)}$ 相交，此时 $u \leftarrow -c$（图6中的时间 $t_{2}$）。当 $u = -c$ 时，DTS 的运动使得 $\mathcal{C}_{L}$ 静止不动。然后监测约束圆 $\mathcal{C}_{R}$，直到它与 $\mathcal{C}_{p(\kappa)}$ 完全重合，此时 $u \leftarrow +c$（时间 $t_{3}$）。此时，$\mathcal{C}_{R}$ 静止不动，监测 $\mathcal{C}_{L}$，直到它从右侧与航路点段 $\overline{\mathbf{w}_{i} \mathbf{w}_{i+1}}$ 相交，此时 $u \leftarrow -c$（时间 $t_{4}$）。此时，约束圆 $\mathcal{C}_{L}$ 静止不动，监测 $\mathcal{C}_{R}$，直到它不再与 $\overline{\mathbf{w}_{i} \mathbf{w}_{i+1}}$ 相交，此时恢复跟踪（时间 $t_{5}$）。如果转弯是逆时针方向，则遵循类似的步骤。

切换时间通过找到圆和直线的交点来确定。在数字硬件中找到这些切换时间存在实际问题。这些问题及其相关影响将在第五节中讨论。


![](https://cdn.mathpix.com/cropped/2022_07_18_9ff4460d05508d94c751g-15.jpg?height=430&width=528&top_left_y=178&top_left_x=364)

图7. 针对 $\kappa \in [0,1)$ 的DTS算法框图

如果 $\kappa=1$，则DTS算法将大大简化。与 $\kappa \in [0,1)$ 的情况类似，第一步是确定 $\mathcal{C}_{\mathbf{p}(\kappa)}$ 和转弯方向。如图8所示，对于顺时针转弯，DTS 跟踪直线路径段 $\overline{\mathbf{w}_{i-1} \mathbf{w}_{i}}$，直到 $\mathcal{C}_{R}$ 与 $\mathcal{C}_{p(\kappa)}$ 重合，此时 $u \leftarrow +c$（图8中的时间 $t_{2}$）。然后，$\mathcal{C}_{R}$ 静止不动，监测 $\mathcal{C}_{L}$，直到整个圆位于航路点段 $\overline{\mathbf{w}_{i} \mathbf{w}_{i+1}}$ 的左侧（时间 $t_{3}$），此时恢复跟踪。

![](https://cdn.mathpix.com/cropped/2022_07_18_9ff4460d05508d94c751g-15.jpg?height=402&width=498&top_left_y=901&top_left_x=378)

图8. 针对 $\kappa=1$ 的DTS算法。

以下定理断言，DTS算法实现了第三节中定义的极值 $\kappa$-轨迹。


定理 5：图7所示的DTS算法实现了第三节中定义的极值 $\kappa$-轨迹。

DTS算法可以根据所使用的应用配置为不同的模式。例如，可能希望选择 $\kappa$ 以便轨迹与航路点的距离为 $D$。例如，这种模式可以用于确保附加到无人机上的传感器的探测范围经过航路点。如果 $D \in \left[0, R\left(1 / \sin \frac{\beta}{2} - 1\right)\right]$，那么通过使用方程(19)，可以简单地证明 $\kappa$ 的正确选择是

$$
\kappa^{*}=\frac{D}{R} \frac{\sin \frac{\beta}{2}}{1-\sin \frac{\beta}{2}} .
$$

如果希望轨迹直接通过航路点，则选择 $\kappa^{*} = 0$。另一方面，如果希望在航路点之间仅通过一次转弯过渡，则选择 $\kappa^{*} = 1$。

对于时间关键任务，通常希望根据航路点路径规划任务。然而，如果轨迹平滑过程改变了航路点路径的路径长度，那么任务的时间安排将受到影响。因此，希望选择 $\kappa$，使得 $\kappa$-轨迹的路径长度等于航路点路径的路径长度。为此，下面的引理推导了 $\kappa$-轨迹路径长度的解析表达式。

引理 6：如果 $\kappa \in [0,1]$，且 $R = \hat{v} / c$，则图4中所示的 $\kappa$-轨迹的路径长度由以下公式给出：
$$
\begin{aligned}
&\mathcal{L}\left(\kappa, \mathbf{w}_{i+1}, \mathbf{w}_{i}, \mathbf{w}_{i-1}\right)=\left\|\mathbf{w}_{i+1}-\mathbf{w}_{i}\right\|+\left\|\mathbf{w}_{i}-\mathbf{w}_{i-1}\right\| \\
&+2 R\left(\frac{\pi-\beta}{2}+2 \cos ^{-1}(\Xi(\kappa, \beta))-\Xi(\kappa, \beta) \sqrt{\frac{1}{\Xi(\kappa, \beta)^{2}}-1}-(1-\kappa) \cos \frac{\beta}{2}-\kappa \cot \frac{\beta}{2}\right),
\end{aligned}
$$

where

$$
\beta=\cos ^{-1}\left(\left(\frac{\mathbf{w}_{i+1}-\mathbf{w}_{i}}{\left\|\mathbf{w}_{i+1}-\mathbf{w}_{i}\right\|}\right)^{T}\left(\frac{\mathbf{w}_{i}-\mathbf{w}_{i-1}}{\left\|\mathbf{w}_{i}-\mathbf{w}_{i-1}\right\|}\right)\right),
$$

and

$$
\Xi(\kappa, \beta)=\frac{(1+\kappa)+(1-\kappa) \sin \frac{\beta}{2}}{2} .
$$


证明：参考图 9，我们看到
$$
\begin{aligned}
\mathcal{L} &=\left\|\mathbf{w}_{i+1}-\mathbf{w}_{i}\right\|+\left\|\mathbf{w}_{i}-\mathbf{w}_{i-1}\right\|-2\left(\overline{\mathbf{w}_{i} c}+\overline{c e}\right)+2(R \theta+R(\pi / 2+\theta-\beta / 2)) \\
&=\left\|\mathbf{w}_{i+1}-\mathbf{w}_{i}\right\|+\left\|\mathbf{w}_{i}-\mathbf{w}_{i-1}\right\|+2 R\left(\frac{\pi-\beta}{2}+2 \theta-\frac{\overline{\mathbf{w}_{i} c}+\overline{c e}}{R}\right)
\end{aligned}
$$

We note that

$$
\overline{c e}=\sqrt{(R+\overline{c d})^{2}-R^{2}} .
$$

根据正弦定律，

$$
\frac{\overline{\mathbf{w}_{i} c}}{\sin \left(\frac{\pi}{2}+\theta-\frac{\beta}{2}\right)}=\frac{R+\overline{\mathbf{w}_{i} \mathbf{p}(\kappa)}}{\sin \left(\frac{\pi}{2}-\theta\right)}=\frac{R-\overline{c d}}{\sin \left(\frac{\beta}{2}\right)} .
$$



![](https://cdn.mathpix.com/cropped/2022_07_18_9ff4460d05508d94c751g-17.jpg?height=734&width=504&top_left_y=170&top_left_x=378)

图9. 引理 6 证明中的定义。

通过解出 $\overline{cd}$ 和 $\overline{\mathbf{w}_{i} c}$，我们得到：

$$
\begin{aligned}
\overline{c d} &=R-\left(R+\overline{\mathbf{w}_{i} \mathbf{p}(\kappa)}\right) \frac{\sin \left(\frac{\beta}{2}\right)}{\cos \theta} \\
\overline{\mathbf{w}_{i} c} &=\left(R+\overline{\mathbf{w}_{i} \mathbf{p}(\kappa)}\right) \frac{\cos \left(\theta-\frac{\beta}{2}\right)}{\cos \theta}=\left(R+\overline{\mathbf{w}_{i} \mathbf{p}(\kappa)}\right)\left(\cos \frac{\beta}{2}+\tan \theta \sin \frac{\beta}{2}\right) .
\end{aligned}
$$

从图9，使用 $\cos \theta = \frac{R}{R + \overline{c d}}$，我们得到：

$$
\cos \theta=\frac{R+\left(R+\overline{\mathbf{w}_{i} \mathbf{p}(\kappa)}\right) \sin \left(\frac{\beta}{2}\right)}{2 R}
$$

注意到 $\beta \in (0, \pi)$ 并从方程(19)中代入，得到：

$$
\overline{\mathbf{w}_{i} \mathbf{p}(\kappa)}=\kappa R\left(\frac{1}{\sin \left(\frac{\beta}{2}\right)}-1\right)
$$

解出 $\theta$，得到：

$$
\theta=\cos ^{-1}\left(\frac{(1+\kappa)+(1-\kappa) \sin \frac{\beta}{2}}{2}\right)=\cos ^{-1}(\Xi(\kappa, \beta)) .
$$

将方程(36)和(38)代入方程(34)，得到：

$$
\overline{c e}=R \sqrt{\frac{4}{\left[(1+\kappa)+(1-\kappa) \sin \frac{\beta}{2}\right]^{2}}-1}=R \sqrt{\frac{1}{\Xi^{2}(\kappa, \beta)}-1}
$$

从图9中注意到 $\tan \theta = \frac{\overline{c e}}{R}$，并使用方程(40)，我们可以从方程(37)中解出 $\overline{\mathbf{w}_{i} c}$，得到：

$$
\begin{aligned}
\overline{\mathbf{w}_{i} c} &=R\left((1-\kappa)+\frac{\kappa}{\sin \frac{\beta}{2}}\right)\left(\cos \frac{\beta}{2}+\sin \frac{\beta}{2} \sqrt{\frac{4}{\left[(1+\kappa)+(1-\kappa) \sin \frac{\beta}{2}\right]^{2}}-1}\right) \\
&=R\left((\Xi(\kappa, \beta)-1) \sqrt{\frac{1}{\Xi^{2}(\kappa, \beta)}-1}+(1-\kappa) \cos \frac{\beta}{2}+\kappa \cot \frac{\beta}{2}\right) .
\end{aligned}
$$

因此，将方程(39)、(40)和(41)代入方程(33)得到方程(30)。

从引理6可以看出，航路点路径 $\overline{\mathbf{w}_{i-1} \mathbf{w}_{i} \mathbf{w}_{i+1}}$ 和相关的 $\kappa$-轨迹之间的路径长度差异由以下给出：
$$
\begin{aligned} \Lambda\left(\kappa, \mathbf{w}_{i+1}, \mathbf{w}_{i}, \mathbf{w}_{i-1}\right)=2 R\left(\frac{\pi-\beta}{2}+2 \cos ^{-1}(\Xi(\kappa, \beta))-\Xi(\kappa, \beta) \sqrt{\frac{1}{\Xi(\kappa, \beta)^{2}}-1}\right.\\ &\left.-(1-\kappa) \cos \frac{\beta}{2}-\kappa \cot \frac{\beta}{2}\right) . \end{aligned}
$$

以下引理将用于找到 $\kappa$，使得 $\kappa$-轨迹与航路点轨迹具有相同的路径长度。

引理 7：如果 $\beta \in [0, \pi)$ 且 $\kappa \in [0, 1]$，则 $\Lambda$ 是 $\kappa$ 的递减函数。此外，$\left.\Lambda\left(0, \mathbf{w}_{i+1}, \mathbf{w}_{i}, \mathbf{w}_{i-1}\right)\right)>0$ 且 $\Lambda\left(1, \mathbf{w}_{i+1}, \mathbf{w}_{i}, \mathbf{w}_{i-1}\right)<0$。

证明：对式（42）关于 $\kappa$ 求导，经过一些代数变换，得

$$
\frac{\partial \Lambda}{\partial \kappa} = 2R \left[-\frac{\partial \Xi}{\partial \kappa} \sqrt{\frac{1}{\Xi^2} - 1} + \frac{\partial \Xi}{\partial \kappa} \frac{1}{\sqrt{1-\Xi^2}} \left(-2 + \frac{1}{\Xi}\right) + \cot \frac{\beta}{2} \left(\sin \frac{\beta}{2} - 1\right) \right].
$$

由式（32）知，$\beta \in [0,\pi)$ 且 $\kappa \in [0,1]$ 意味着 $\Xi \geq 0$ 且

$$
\frac{\partial \Xi}{\partial \kappa} = \frac{1 - \sin \frac{\beta}{2}}{2} > 0.
$$

因此，若

$$
-2 + \frac{1}{\Xi} \leq 0,
$$

则有

$$
\frac{\partial \Lambda}{\partial \kappa} < 0.
$$

根据定义，有

$$
\begin{aligned}
-2 + \frac{1}{\Xi} &= -2 + \frac{1 + \sin \frac{\beta}{2}}{2} + \kappa \frac{1 - \sin \frac{\beta}{2}}{2} \\
&\leq -2 + \frac{1 + \sin \frac{\beta}{2}}{2} + \frac{1 - \sin \frac{\beta}{2}}{2} \\
&= -1.
\end{aligned}
$$

因此，$\Lambda$ 关于 $\kappa$ 是递减函数。

具体表达式为

$$
\begin{aligned}
\Lambda\left(\kappa, \mathbf{w}_{i+1}, \mathbf{w}_i, \mathbf{w}_{i-1}\right) = 2R \Bigg(& \frac{\pi - \beta}{2} + 2 \cos^{-1}(\Xi(\kappa, \beta)) - \Xi(\kappa, \beta) \sqrt{\frac{1}{\Xi(\kappa, \beta)^2} - 1} \\
& - (1-\kappa) \cos \frac{\beta}{2} - \kappa \cot \frac{\beta}{2} \Bigg).
\end{aligned}
$$

注意到

$$
\Xi(0, \beta) = \frac{1 + \sin \frac{\beta}{2}}{2},
$$

从式（42）经代数变换可得

$$
\Lambda(0, \beta) = 2R \left[ \frac{\pi - \beta}{2} + 2 \cos^{-1} \left( \frac{1 + \sin \frac{\beta}{2}}{2} \right) - \sqrt{1 - \frac{1}{4} \left(1 + \sin \frac{\beta}{2}\right)^2} - \cos \frac{\beta}{2} \right].
$$

注意 $\Lambda(0, \pi) = 0$，且

$$
\frac{\partial \Lambda}{\partial (\beta/2)}(0, \beta) = 2R \left[ \left( \sin \frac{\beta}{2} - 1 \right) + \frac{ \cos \frac{\beta}{2} \left( \frac{1}{4} (1 + \sin \frac{\beta}{2}) - 1 \right) }{ \sqrt{1 - \frac{1}{4} (1 + \sin \frac{\beta}{2})^2} } \right].
$$

由拉格朗日余项定理 [29]，存在 $a \in (\beta, \pi)$，使得

$$
\Lambda(0, \beta) = \frac{\partial \Lambda}{\partial (\beta/2)} (0, a) \left( \frac{\beta - \pi}{2} \right).
$$

容易证明，对于所有 $a \in [0, \pi)$，有

$$
\frac{\partial \Lambda}{\partial (\beta/2)} (0, a) < 0,
$$

这意味着 $\Lambda(0) > 0$。

另外注意

$$
\Xi(1, \beta) = 1,
$$

由式（42）可得

$$
\Lambda(1, \beta) = 2R \left[ \frac{\pi - \beta}{2} - \cot \frac{\beta}{2} \right].
$$

对其求导得到

$$
\frac{\partial \Lambda}{\partial (\beta/2)} (1, \beta) = \cot^2 \frac{\beta}{2} > 0.
$$

因此，根据拉格朗日余项定理，存在 $a \in (\beta, \pi)$，使得

$$
\Lambda(1, \beta) = \cot^2 \frac{\beta}{2} \left( \frac{\beta - \pi}{2} \right).
$$


这意味着对于所有 $\beta \in [0, \pi)$，$\Lambda(1, \beta) < 0$。

对于时间关键任务，必须保证由DTS生成的轨迹的路径长度与原始航路点轨迹 $\mathcal{P}$ 的路径长度相同。下一个定理表明，存在一个 $\kappa \in [0, 1]$ 使得路径长度相等。

定理 8：如果 $\Lambda\left(\kappa, \mathbf{w}_{i-1}, \mathbf{w}_{i}, \mathbf{w}_{i+1}\right)$ 如方程(42)所给出，其中 $\beta \in [0, \pi)$ 如方程(31)所给出，且 $R = \hat{v} / c$，则存在唯一的 $\kappa^{*} \in [0,1]$ 使得 $\Lambda\left(\kappa^{*}\right) = 0$。此外，对应于 $\kappa^{*}$ 的 $\kappa$-轨迹与航路点路径 $\overline{\mathbf{w}_{i-1} \mathbf{w}_{i} \mathbf{w}_{i+1}}$ 具有相同的路径长度，即：
$$
L=\left\|\mathbf{w}_{i}-\mathbf{w}_{i-1}\right\|+\left\|\mathbf{w}_{i+1}-\mathbf{w}_{i}\right\| .
$$

此外，$\kappa^{*}$ 可以通过使用二分查找算法高效地数值求解，精确度为 $\bigcirc\left(2^{-n}\right)$，其中 $n$ 是 $\Lambda$ 的函数评估次数。

证明：由引理6可知，$\Lambda$ 表示 $\kappa$-轨迹与航路点路径的路径长度差。因此，若 $\Lambda=0$，则两路径长度相等。由引理7知，存在唯一的 $\kappa^* \in [0,1)$，使得 $\Lambda(\kappa^*)=0$。

由于 $\Lambda(0) > 0$ 且 $\Lambda(1) < 0$，二分法搜索的第一步选取 $\kappa=0.5$。$\Lambda(0.5)$ 的符号决定了 $\kappa^*$ 所在区间，精确度达到 $2^{-1}$。后续函数调用进一步细化该估计值。


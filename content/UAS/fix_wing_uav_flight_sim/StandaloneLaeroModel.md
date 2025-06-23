+++
date = '2025-06-21T15:44:38+08:00'
draft = false
title = '轻量级飞行动力学模型'
summary= "轻量级的四自由度（4-DOF）飞行动力学模型"
+++

## 一、Laero 模型

[Laero 模型的 C++代码实现](https://github.com/flitai/StandaloneLaeroModel)

### 1. 问题定义

在许多仿真应用（如游戏、AI行为模拟、训练系统原型）中，需要对飞行器的运动进行模拟。高保真度的物理模型（如基于计算流体动力学的模型）虽然精确，但计算成本高昂，不适用于需要同时运行大量实体或对实时性有极高要求的场景。

Laero 模型是一个**轻量级的四自由度（4-DOF）飞行动力学模型**。其“四自由度”体现在其控制输入的简化上（主要独立控制的是俯仰、滚转、偏航和前向速度），但它的物理状态描述和数据输出是完整的六自由度。横向和法向的运动虽然不是被直接控制，但它们作为姿态变化的结果被模型正确地计算了出来，并最终完整地体现在输出数据中。

它的设计目标不是进行高保真的空气动力学仿真（如 `JSBSimModel`），而是通过简化的运动学方程来快速、高效地模拟飞行器的基本飞行轨迹和姿态变化。它的核心特点是：

- **运动学驱动**：它不计算复杂的空气动力学力和力矩，而是直接对姿态角速率和机体轴速度进行积分，来更新飞行器的状态。
- **简化控制律**：它内置了简单的比例控制（P-Control）逻辑，可以将高层级的飞行指令（如“飞到指定航向”）转换为底层的姿态变化速率。
- **高效性**：由于计算量远小于高保真模型，它非常适合用于计算资源有限或需要大量简单AI飞机的场景。

本项目旨在设计并实现一个**轻量级、实时、指令驱动的飞行动力学模型**。该模型旨在解决以下核心问题：

* **性能与效率**: 提供一个计算高效的动力学解算方案，以满足实时仿真循环的要求。
* **高层指令控制**: 接收“目标高度”、“目标航向”、“目标速度”等指令，无需暴露舵面或力矩等底层输入。
* **行为可信度**: 利用基本运动学与分层控制，使机动（转弯、爬升、加减速）符合飞行常识，使其在视觉上具有可信度。
* **模块独立性**: 作为独立组件，无特殊外部依赖，可嵌入各类自定义应用。

### 2. 输入输出设计

#### 2.1 输入

* **初始化状态**：
  - 位置$(x_0,y_0,z_0)$
  - 姿态 $(\phi_0,\theta_0,\psi_0)$
  - 机体速度 $(u_0,v_0,w_0)$
* **周期性更新**（每步调用）：
  * 时间步长 $\Delta t$
  * 高层指令：

    * 期望高度 $H_{cmd}$
    * 期望航向 $\Psi_{cmd}$
    * 期望速度 $V_{cmd}$

#### 2.2 输出

* **飞机完整状态**：
  - 位置 $(x,y,z)$
  - 姿态 $(\phi,\theta,\psi)$
  - 机体速度 $(u,v,w)$
  - 惯性速度 $(V_N,V_E,V_D)$
  - 角速率 $(p,q,r)$ 

### 3. 模型原理与逻辑

本模型是一个基于**运动学**的飞行动力学模型，其核心原理是直接对飞行器的角速率和线加速度进行积分来更新姿态和位置，而非通过计算空气动力和力矩来求解。

#### 3.1 数值积分方法

采用**二阶 Adams–Bashforth**方法，对状态导数进行积分，该方法利用当前和前一时刻的导数信息来预测下一时刻的状态，以在精度和性能间取得平衡：

$$
S_{k+1} = S_k + \frac{\Delta t}{2} \bigl(3\,\dot S_k - \dot S_{k-1}\bigr)
$$

其中状态向量 $S=[x,y,z,\phi,\theta,\psi,u,v,w]^T$。

其中 $(x, y, z)$ 为世界坐标系位置，$(\phi, \theta, \psi)$ 为欧拉角，$(u, v, w)$ 为机体坐标系速度。

#### 3.2 运动学方程 (Equations of Motion)

状态的更新通过对一阶导数进行积分完成。令状态向量的导数为 $\dot{S}$，在 $k$ 时刻，积分方法为二阶Adams-Bashforth：
$$S_{k+1} = S_k + \frac{\Delta t}{2} (3 \dot{S}_k - \dot{S}_{k-1})$$

其中，$\dot{S}_k$ 的分量由控制律和运动学关系确定：

* 位置导数：$(\dot{x}, \dot{y}, \dot{z})^T = \mathbf{v}_{world} = R_{body \to world}(\phi, \theta, \psi) \cdot (u, v, w)^T$
* 姿态导数：$(\dot{\phi}, \dot{\theta}, \dot{\psi})^T$ 由低层控制律直接给出。
* 速度导数：$(\dot{u}, \dot{v}, \dot{w})^T$ 由速度控制律和假设（$\dot{v}=\dot{w}=0$）给出。

#### 3.3 分层控制律 (Hierarchical Control Law)

1. **高层控制**：

   * 将高度误差、航向误差、速度误差映射为对应的期望爬升率、转弯率与加速度。它们负责将用户的目标状态（如目标高度）转换为中间层的控制目标（如目标俯仰角）。

2. **中层映射**：

   * **爬升/俯仰耦合**：根据期望爬升率 $\dot H$ 与真空速 $V_{TAS}$，计算俯仰角指令：

     $$
     \theta_{cmd} = \arcsin\bigl(\dot H / V_{TAS}\bigr)
     $$

   * **航向/滚转耦合**：根据期望转弯率 $\dot\Psi$ 与重力加速度 $g$，计算滚转角指令：
     $$
     \phi_{cmd} = \arctan\bigl(\dot\Psi\,V_{TAS}/g\bigr)
     $$

3. **低层比例控制**：它们接收中间目标（如目标俯仰角），并根据一个**带时间常数的比例控制（P-Control）**逻辑，计算出实现该目标所需的角速率。对目标角度与当前角度之间的误差应用带时间常数 $\tau$ （如俯仰/滚转 $\tau=1$ s，爬升 $\tau=4$ s。）的分段 P 控制，生成角速率或加速度指令：

$$
\dot x = \begin{cases}
\mathrm{sign}(e_x)\,\dot x_{max}, & |e_x|\ge \dot x_{max}\,\tau,\\
\frac{e_x}{\tau\,\dot x_{max}}\,\dot x_{max}, & |e_x|< \dot x_{max}\,\tau.
\end{cases}
$$

其中 $e_x$ 表示对应通道的角度/速率误差。

#### 3.4 控制通道耦合

* **高度–俯仰**：将期望爬升率转换为俯仰指令。
* **航向–滚转**：将期望转弯率转换为滚转指令。
* **速度**：直接控制前向线加速度 $u$ 分量。

#### 3.5 坐标系与变换

* **机体坐标系 (Body)**：速度 $(u,v,w)$、角速率 $(p,q,r)$。
* **惯性坐标系 (NED)**：位置更新与惯性速度 $(V_N,V_E,V_D)$。用于表示飞机的位置和全局速度。
* **方向余弦矩阵 (DCM)**：基于欧拉角 $(\phi,\theta,\psi)$ 将机体速度转换为惯性速度。机体系与惯性系两者之间的转换通过基于欧拉角的**方向余弦矩阵**完成。

世界速度 $\mathbf{v}_{world}$ 和机体速度 $\mathbf{v}_{body}$ 的关系：
$$\begin{bmatrix} \dot{x} \\ \dot{y} \\ \dot{z} \end{bmatrix} = \begin{bmatrix} c_\theta c_\psi & s_\phi s_\theta c_\psi - c_\phi s_\psi & c_\phi s_\theta c_\psi + s_\phi s_\psi \\ c_\theta s_\psi & s_\phi s_\theta s_\psi + c_\phi c_\psi & c_\phi s_\theta s_\psi - s_\phi c_\psi \\ -s_\theta & s_\phi c_\theta & c_\phi c_\theta \end{bmatrix} \begin{bmatrix} u \\ v \\ w \end{bmatrix}$$
其中 $c_\alpha = \cos(\alpha)$, $s_\alpha = \sin(\alpha)$。



[Laero 模型的 C++代码实现](https://github.com/flitai/StandaloneLaeroModel)

---

## 二、Rac 模型

Rac 模型的 C++代码实现见 [Laero 模型下的](https://github.com/flitai/StandaloneLaeroModel/) `StandaloneRacModel`目录

### 1. 问题定义与设计目标

本模型旨在提供一个**轻量级、性能逼真**的飞行姿态与速度动态模拟，适用于实时仿真与可视化场景。模型通过接收高层指令（目标航向、目标高度、目标速度），在满足结构简洁和运行高效的前提下，保证以下目标：

* **实时更新**：在仿真步长内（如 1/60 s）完成状态积分与输出，支持大规模实体同时模拟。
* **高层指令驱动**：用户仅需设置目标航向（deg）、目标高度（m）、目标速度（kts），无需关注舵面或推力等底层物理量。
* **性能限制嵌入**：融入速度与载荷（G 值）限制，动态调整机动能力，保证模拟安全边界。
* **自然响应**：通过增益调节与二阶积分逼近，实现姿态变化和平滑的加速过程。

### 2. 输入输出设计

#### 2.1 输入

* **初始状态**：

  * 位置、姿态（滚转 roll、俯仰 pitch、偏航 yaw）及机体速度向量（m/s）。
* **周期更新**（方法 `update(dt)`）：

  * 时间步长 $\Delta t$ (s)
  * **高层指令**：

    * 期望航向 $\psi_{cmd}$ (deg)
    * 期望高度 $h_{cmd}$ (m)
    * 期望速度 $V_{cmd}$ (kts)
* **性能限制配置**（方法 `setPerformanceLimits(minSpeedKts, maxG, speedAtMaxG_Kts, maxAccel_mps2)`）：

  * 最低巡航速度 (kts)  $V_{p,min}$
  * 最大过载 $g_{max}$
  * 达到最大过载的速度阈值 (kts)  $V_{p,\mathrm{max}G}$
  * 最大加速度 (m/s²) $a_{max}$ 

#### 2.2 输出

* **更新后的飞机状态**：

  * 姿态 (roll, pitch, yaw)
  * 机体角速度 (p,q,r)
  * 机体速度向量 (u,v,w)
  * 惯性速度与位置更新 (NED)

### 3. 核心原理与体系结构

#### 3.1 单步更新流程

1. **单位转换与当前量提取**：

   * 节–米/秒互换常数 $K_{kts\to mps}$、$K_{mps\to kts}$；角度–弧度转换 $R2D, D2R$。
2. **默认指令**：若高层指令未显式设置，则保持当前状态作为目标。 
3. **高度控制映射**：

   * 计算高度误差速率 $\dot h = h_{cmd} - h$，限幅至最大垂直速率 $\pm3000\,ft/min$。
   * 期望俯仰角：
     $\theta_{cmd}=\arcsin\Bigl(\frac{\dot h}{V}\Bigr)\quad (V>1\,m/s).$  fileciteturn2file0
4. **载荷限制与机动性**：

   * 当前速度 $V$ 低于阈值 $V_{p,\mathrm{max}G}$ 时，线性插值调整有效最大载荷 $g_{eff}$。
   * 最大转弯率：
     $r_{max}=\frac{(g_{eff}-1)g_0}{V},$
     对应俯仰/偏航角速率极限 $q_{max}=r_{max},\,q_{min}=-r_{max}$。
5. **角速率指令**：

   * 误差转换：使用比例增益（0.5）将角度误差转换为速率指令：
     $q=K_q(\theta_{cmd}-\theta),\quad r=K_r(\psi_{cmd}-\psi),$
   * 并在极限内饱和。 
6. **积分更新姿态**：

   * 采用梯形法（Trapezoidal Rule）：

     $$
     \theta^{+}=\theta+\frac{(q+q_{prev})\,\Delta t}{2},\quad
     \psi^{+}=\psi+\frac{(r+r_{prev})\,\Delta t}{2}.
     $$
7. **滚转视觉效果**：

   * 滚转角与转弯率成比例平滑混合：
     $\phi^{+}=0.98\,\phi+0.02\,(r/r_{max}\times60°).$ 
8. **速度控制映射**：

   * 速度误差加速度：
     $\dot V=K_v(V_{cmd}-V),$ 并限幅至 $\pm a_{max}$。
   * 更新前向速度：
     $V^{+}=\max(V_{min},V+\dot V\,\Delta t).$ 
9. **惯性速度与位置更新**：

   * 根据新姿态 $(\theta^{+},\psi^{+})$ 计算 NED 分量：
     $V_N=\cos\theta^{+}\cos\psi^{+}V,\quad V_E=\cos\theta^{+}\sin\psi^{+}V,\quad V_D=-\sin\theta^{+}V.$
   * 位置积分：$\mathbf{r}^{+}=\mathbf{r}+\mathbf{V}\Delta t$。

#### 3.2 数据状态保持

* **上步速率缓存**：存储前步角速率 $q_{prev},r_{prev}$ 用于梯形积分。
* **命令保持**：若未更新，命令值持续沿用。

### 4. 形式化表达

$$
S=\begin{bmatrix}x,y,z,\phi,\theta,\psi,V\end{bmatrix}^T,
$$

主要映射函数：

$$
\theta_{cmd}=\arcsin(\dot h/V),
\quad r_{max}=((g_{eff}-1)g_0)/V,
\quad q=K_q(\theta_{cmd}-\theta),
\quad r=K_r(\psi_{cmd}-\psi),
$$

梯形积分：

$$
\theta^{+}=\theta+((q+q_{prev})\Delta t)/2,
\psi^{+}=\psi+((r+r_{prev})\Delta t)/2.
$$

### 5. 模型特性与应用

* **性能逼真**：动态载荷限制与姿态耦合，反映高速和低速下的不同机动性。
* **轻量高效**：仅利用简单 trig 和线性插值，适应 GPU 或多线程批量计算。
* **易于集成**：纯 C++ 实现，无第三方依赖，可作为模块嵌入各类仿真框架。
* **可视化友好**：滚转角平滑混合保证动画自然，适合游戏与训练场景。


Rac 模型的 C++代码实现见 [Laero 模型下的](https://github.com/flitai/StandaloneLaeroModel/) 
`StandaloneRacModel`目录

---

## 三、JSBSimModel

[C++代码实现](https://github.com/flitai/JSBSimModel)

这是一个**仅依赖于标准C++库和JSBSim库**的独立应用程序，能够初始化和配置JSBSim，将控制意图转换给JSBSim，调用其运算，并返回精确结果。

`JSBSimModel`通过扮演一个精心设计的**双向数据翻译器和流程协调器**的角色，来实现充当仿真框架和JSBSim引擎之间的桥梁。它本身不进行飞行动力学计算，而是负责处理两个系统之间的所有通信。

这个“桥梁”作用主要通过以下四个关键阶段实现：

### 1. 初始化与配置阶段 (在 `reset()` 函数中)

这是建立连接和同步初始状态的第一步。

* **创建JSBSim实例**: 代码首先创建一个JSBSim的核心执行对象 `FGFDMExec`。这是与JSBSim引擎交互的主要入口。
* **加载飞机模型**: 它根据配置加载一个特定的JSBSim飞机模型文件（例如 `c172.xml` 或 `f16.xml`）。这决定了飞机的气动特性、发动机、尺寸等所有物理属性。
* **同步初始条件**: 这是最关键的桥接步骤之一。`JSBSimModel`会从仿真框架中中获取初始状态（如高度、速度、姿态、地理位置），然后将这些值设置到JSBSim的初始条件模块（`FGInitialCondition`）中。这确保了两个系统在仿真开始时处于完全相同的状态。
* **启动引擎**: 代码会调用JSBSim的接口来初始化并启动飞机引擎。
* **运行初始条件**: 最后，它命令JSBSim根据设定好的初始条件来稳定其内部状态，为仿真循环做准备。

### 2. 控制输入传递阶段 (在 `setControl...` 系列函数中)

当用户或AI在仿真框架中进行操作时，`JSBSimModel`负责将这些操作“翻译”成JSBSim能理解的指令。

* **驾驶杆输入**: 当调用 `setControlStickRollInput()` 或 `setControlStickPitchInput()` 时，`JSBSimModel` 会接收到一个标准化的输入值（-1.0到1.0）。它随后会调用JSBSim飞行控制系统（`FGFCS`）的相应函数，如 `SetDaCmd()` (副翼指令) 和 `SetDeCmd()` (升降舵指令)，来直接控制JSBSim中的舵面。
* **油门输入**: `setThrottles()` 函数接收油门位置，并为JSBSim中的每个引擎调用 `SetThrottleCmd()`。
* **其他控制**: 同样，起落架、刹车、减速板等指令也通过类似的方式，从仿真框架的通用接口传递到JSBSim的具体控制函数中。

### 3. 仿真核心循环阶段 (在 `dynamics()` 函数中)

在每个仿真时间步（frame），`JSBSimModel` 负责协调一次完整的计算。

* **设置时间步长**: 它首先告诉JSBSim本次仿真的时间增量 `dt`。
* **执行计算**: 最核心的一行代码是 `fdmex->Run();`。这个函数调用会触发JSBSim引擎执行一次完整的、复杂的飞行动力学解算。所有关于气动力、推力、重力、力矩的计算和积分都在JSBSim内部完成。`JSBSimModel` 在此只是按下了“计算”按钮。

### 4. 状态数据回传阶段 (在 `dynamics()` 和 `get...` 函数中)

当JSBSim完成计算后，`JSBSimModel` 负责从JSBSim中“提取”结果，并用这些结果来更新仿真框架中的飞机状态。

* **更新核心状态**: 在 `dynamics()` 函数中，紧接着 `fdmex->Run()` 之后，代码会立即从JSBSim的内部模块（如 `FGPropagate` 和 `FGAccelerations`）中获取最新的数据。
    * 它调用 `Propagate->GetAltitudeASL()` 获取高度，`Propagate->GetEuler()` 获取姿态角，`Propagate->GetVel()` 获取速度。
    * 然后，它调用OpenEaagles `Player` 对象的 `setAltitude()`、`setEulerAngles()` 和 `setVelocity()` 等函数，将这些新数据“写回”到OpenEaagles的仿真实体中。
* **提供详细数据查询**: `JSBSimModel` 还实现了一系列 `get...` 函数，如 `getGload()`, `getMach()`, `getEngThrust()` 等。当外部需要这些详细数据时，这些函数会实时地从JSBSim的相应模块（如 `FGAuxiliary`, `FGPropulsion`）中查询并返回结果。

综上所述，`JSBSimModel` 的桥梁作用体现在它是一个**全权代理**：它代表仿真框架初始化和配置JSBSim，将仿真框架的控制意图翻译给JSBSim，命令JSBSim进行运算，最后将JSBSim的精确运算结果取回并更新到仿真框架的世界中，从而实现了两个系统的无缝集成。

JSBSimModel的[C++代码实现](https://github.com/flitai/JSBSimModel)
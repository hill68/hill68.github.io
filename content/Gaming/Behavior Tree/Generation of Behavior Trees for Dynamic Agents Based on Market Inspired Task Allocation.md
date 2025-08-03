+++
date = '2025-08-01T17:07:50+08:00'
draft = false
title = '基于市场启发的任务分配的动态智能体行为树生成'
summary= "建立了一种基于市场启发的拍卖任务分配系统，以及一种利用可用行为库生成行为树以创建自主行为的方法。通过仿真和多个实验室环境中的多次实验验证了该方法的有效性。"
+++

## 基于市场启发的任务分配的动态智能体行为树生成

Niklas Dahlquist  
工程物理与电气工程，2022年硕士

卢勒奥理工大学  
计算机科学、电气与空间工程系

#### 摘要

近年来，利用多自主智能体完成各种任务的可能性引起了广泛关注。然而，使用多个智能体带来了任务如何分配以及如何设计各个智能体的智能体结构等问题。

本论文建立了一种基于市场启发的拍卖任务分配系统，以及一种利用可用行为库生成行为树以创建自主行为的方法。通过仿真和多个实验室环境中的多次实验验证了该方法的有效性。结果表明，该方法有希望有效地解决上述问题。

https://www.diva-portal.org/smash/get/diva2:1661617/FULLTEXT01.pdf

## 第一章  引言

### 1.1 背景

在过去几年中，使用多个自主智能体完成各种任务（如家庭配送、仓储中心自动化和运输）的可能性一直是一个积极研究的领域。许多公司已经引入或计划向大众市场推出该领域的技术。谷歌和亚马逊等大型科技公司已经在大规模测试使用自主无人机进行快速家庭配送的可能性[1, 2]；如此规模的雄心项目无疑需要解决与自主智能体及多智能体协调相关的技术挑战。

### 1.1.1 人工智能与行为树

有多种方式可以创建允许智能体对真实世界环境作出反应的自主行为。近年来，行为树[3, 4]作为一种开发自主操作所需人工智能的概念，获得了越来越多的关注；该概念最初用于创建计算机游戏中模块化的非玩家角色（NPC）[5]，后来也被机器人领域采用。行为树以树状的分层结构组织，不同的行为作为叶节点，内部节点控制执行何种行为。行为树还泛化了其他控制结构，如有限状态机和决策树[6]。

### 1.1.2 基于市场的任务分配

多个自主智能体各自独立行动但需共同协作完成目标的需求也日益增长。家庭配送便是这样一个例子，多架无人机应能够以分布式方式配送包裹，同时整体上作为一个群体高效地完成所有包裹的配送目标。

解决多智能体任务分配问题的众多方法之一是使用基于市场的启发方法[7, 8]，允许各智能体竞争可用任务。这种方法将规划分布给所有参与者，即减少了对集中式规划者的依赖；由于每个个体都能为每项任务估算成本，只需一个集中式的任务分发者。采用更分散的市场启发分配结构，自然允许同质智能体竞争任务，异质智能体协作解决不同类型的任务。由于无集中式规划者，系统整体的最优性依赖于参与者的智能水平。此任务分配方法的缺点是可能无法产生全局最优解，但便于整合不同类型任务，并且在执行过程中新增任务时无需重新规划。

### 1.2 论文目标

主要目标是探索行为树如何用于动态创建协作的自主智能体，以及如何将任务分配给具有不同特性和能力的多个智能体。所提框架应实现并测试，包括仿真和真实智能体，以验证功能性。

### 1.2.1 研究目标

本论文需评估的主要关注点包括：

- 探索行为树是否适合在真实及仿真机器人智能体上基于给定任务实现自主和响应式行为。
- 评估多异构机器人如何基于市场启发方法动态分配任务。
- 利用机器人操作系统（ROS）[9]实现从仿真环境向真实硬件的过渡。
- 使用可用行为库，以组合、协调并赋能未来机器人任务。
- 分析任务分配系统的鲁棒性，特别是在智能体可能失败的情况下。

### 1.2.2 领域贡献

本报告通过结合行为树的使用、行为树的生成和基于市场启发的多机器人任务分配，对当前研究做出贡献。报告还展示了如何在仿真环境中实现该方法，并将其迁移到具有真实机器人的物理环境。提出的框架通过多次仿真和实验室环境中的实验验证。

## 第二章  理论

### 2.1 行为树

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-17.jpg?height=526&width=1072&top_left_y=1205&  top_left_x=498" width=500/>



图2.1：一个简单的行为树示例，用于打开门、打开灯光然后执行其他操作。

行为树是一种动态执行多个行为以实现智能系统的数学模型。目前广泛应用于机器人、控制系统和电子游戏中[6]。行为树由连接的节点组成的有向无环图构成，其中内部节点称为控制节点，叶节点称为执行节点。除根节点外，所有节点都有一个父节点，内部节点拥有一个或多个子节点。

行为树通过定期“滴答”根节点来执行，“滴答”信号由控制节点传递至叶节点执行。执行节点接收“滴答”后进行计算并返回三种结果之一：成功（完成目标）、失败（未完成目标）或运行中（目标尚未完成或失败）。根据控制节点类型和返回值，“滴答”可传递给其他执行节点或将返回值传递给父节点。只有收到“滴答”的节点才执行。该过程重复直至根节点返回三种结果之一[6]。

图2.1展示了一个简单的行为树示例，节点组合完成目标。下文将详细解释各控制节点的内部机制。

### 2.1.1 顺序节点（Sequence Node）

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-18.jpg?height=304&width=698&top_left_y=939&top_left_x=685" width=500/>


图2.2：具有 $N$ 个子节点的顺序节点。

图2.2所示的具有 $N$ 个子节点的顺序节点，会从左至右依次“滴答”子节点，直到某个子节点返回失败或运行中，并将该结果返回给顺序节点的父节点；若所有子节点均返回成功，顺序节点返回成功。顺序节点的执行详见算法1。(6)

**算法 1：** 具有 $N$ 个子节点的序列节点伪代码。

<img src="https://cdn.mathpix.com/snip/images/1UATNEIzuxU-B914xm2pIOeeNpTlGRnZO-_oI68IXQU.original.fullsize.png" width=400/>



### 2.1.2 选择节点（Fallback Node）

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-19.jpg?height=301&width=695&top_left_y=552&top_left_x=686" width=500/>


图2.3：具有 $N$ 个子节点的选择节点。

图2.3所示的具有 $N$ 个子节点的选择节点，会从左至右依次“滴答”子节点，直到某个子节点返回成功或运行中，并将该结果返回给其父节点；若所有子节点均返回失败，选择节点将返回失败给其父节点。选择节点的执行详见算法2。(6)

**算法 2：** 具有 $N$ 个子节点的故障恢复节点伪代码。

<img src="https://cdn.mathpix.com/snip/images/F1tG26iJyFQdkeLfBLBgQdyimrgtPieei_Q0T49QVq0.original.fullsize.png" width=400 />



### 2.1.3 Parallel Node

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-19.jpg?height=301&width=695&top_left_y=1991&top_left_x=686" width=500/>


图2.4：具有 $N$ 个子节点的并行节点。

图2.4所示的具有 $N$ 个子节点的并行节点，会从左到右“滴答”其所有子节点。当成功的子节点数大于或等于 $M$ 时，并行节点返回成功；当失败的子节点数大于 $N - M$ 时，返回失败；若两者条件均不满足，则返回运行中。并行节点的执行详见算法3[6]。

算法 3：具有 $N$ 个子节点和成功阈值 $M$ 的并行节点伪代码。

<img src="https://cdn.mathpix.com/snip/images/H06JH29gtmwaNKiI8SFwtB658CKb3QjEJa0yBVFjDZQ.original.fullsize.png" width=400 />




### 2.1.4 行为节点（Action Node）

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-20.jpg?height=181&width=249&top_left_y=1400&top_left_x=909" width=100/>


图2.5：行为节点，节点名称描述其执行的动作。

如图2.5所示，行为节点在接收到来自其父节点的“滴答”时执行。若行为节点完成执行且成功，则返回成功（success）；若执行失败，则返回失败（failure）；若执行尚未完成，则返回运行中（running）。行为节点的执行详见算法4。[6]

**算法 4：** 动作节点的伪代码。

<img src="https://cdn.mathpix.com/snip/images/3iHHY5ZyID4oMyWqCq8Rc0N_FHxw3SSBxlPTay6peu4.original.fullsize.png" width=400 />


### 2.1.5 条件节点（Condition Node）

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-21.jpg?height=186&width=418&top_left_y=1215&top_left_x=822" width=100/>


图2.6：条件节点。

如图2.6所示，条件节点在接收到来自其父节点的“滴答”时执行。若条件为真，条件节点返回成功（success）；若条件为假，则返回失败（failure）。注意条件节点永远不会返回运行中（running）。条件节点的执行详见算法5。(6)

**算法 5：** 条件节点的伪代码。

<img src="https://cdn.mathpix.com/snip/images/zVRvl6iRw8eBoW9I7QNu0xoL5cPkJEZps61GcMrOTYc.original.fullsize.png" width=400 />



### 2.2 基于市场的任务分配

基于市场的任务分配受到现实世界经济中自由市场的启发。基于供需的任务分配自然驱动所有参与者——无论是执行任务的行为者，还是请求执行任务的行为者——尽可能高效地行动。同质行为者自然会竞争，异质行为者则会协作[7]。

拍卖系统包含了市场启发方法的许多优势，以下章节对此进行详细描述。

### 2.2.1 拍卖服务器

拍卖服务器（或称拍卖处理器）需能够接收新的拍卖项目、竞标、发布可用拍卖信息，并在拍卖回合结束时公布结果。此类拍卖服务器的结构如图2.7流程图所示。

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-22.jpg?height=609&width=1186&top_left_y=1032&top_left_x=441" width=500/>


图2.7：拍卖服务器结构。

### 2.2.2 拍卖客户端

参与出价并争取赢得部分可用任务的参与者称为拍卖客户端。拍卖客户端的结构如图2.8流程图所示。

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-23.jpg?height=618&width=1243&top_left_y=428&top_left_x=412" width=500/>


图2.8：拍卖客户端结构。

### 2.2.3 任务定义

任务 $T$ 定义为包含以下内容的数据结构：

- 任务名称。
- 与任务相关的数据。
- 与任务关联的唯一ID。

任务名称用于使智能体识别任务类型及其能否完成该任务，相关数据用于计算完成任务的成本，ID使智能体能够记忆特定任务。

例如，一个移动到指定点的任务可以定义为

$$
T=\left\{\begin{array}{l}
\text { 任务名称 }=\text { 移动到点 }  \tag{2.1}\\
\text { 相关数据 }=x; y; z \\
\mathrm{ID}=1
\end{array}\right.
$$

### 2.2.4 拍卖定义

拍卖 $A$ 定义为一组可用任务列表 $T=\left\{T_{1}, \ldots, T_{n}\right\}$，其中 $n \in \mathbb{N}$ 表示该拍卖中任务数量，这些任务已经或即将被公告给客户端；每个拍卖还被分配一个唯一ID，以确保客户端的竞标对应正确的拍卖。

### 2.2.5 拍卖回合

拍卖回合从新的拍卖公告开始。在拍卖回合期间，拍卖服务器将收集与当前拍卖相关的竞标，并将接收的新任务存入队列，待加入下一轮拍卖。

### 2.2.6 拍卖回合结束

每个拍卖回合可通过两种方式结束：达到特定竞标数量 $b_{\text {threshold}}$，或自当前拍卖回合公告起经过特定时间 $t_{\text {timeout}}$。拍卖回合结束时，根据成本对获胜者进行优化并公告。未分配给智能体的任务返回可用任务队列，包含于下一轮拍卖中。

### 2.2.7 竞标定义

每个智能体需返回对所选子集（由智能体选择）可用任务的执行成本估计的竞标。竞标定义为若干条目组成的列表，每条目定义为

$$
\text { 竞标条目 }=\left\{\begin{array}{l}
\text { 成本 }  \tag{2.2}\\
\text { 任务 }
\end{array}\right.
$$

### 2.2.8 成本函数

为计算竞标中的成本，每个客户端应定义成本函数，估计执行某任务的成本。成本函数依赖客户端状态、客户端类型和相关任务。客户端不必为所有可用任务定义成本函数，可自由选择竞标任务子集。通用成本函数定义为

$$
\begin{equation*}
f_{\text {cost }}(\text { task }, \text { state }) . \tag{2.3}
\end{equation*}
$$

### 2.2.9 任务分配优化

确定如何分配任务给工人以最小化总成本且分配最多任务的问题，可通过构造无向图 $G=(N, E)$ 解决，其中 $N$ 表示节点，$E$ 表示节点间的边[10]。节点 $N$ 包含工人 $i$ 和任务 $j$，边 $(k, l) \in E$ 表示节点 $k$ 和 $l$ 之间的匹配成本。工人 $i$ 与任务 $j$ 之间的边表示工人执行该任务的成本。图2.9展示了工人、任务及带关联成本 $c_{i,j}$ 的边的结构示意。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-25.jpg?height=581&width=844&top_left_y=435&top_left_x=606" width=500/>


图2.9：展示工人、任务及成本相关图的结构示意。

为优化成本同时最大化任务分配，成本

$$
\begin{equation*}
c_{i, j}=\text { 分配工人 } i \text { 执行任务 } j \text { 的成本 } \tag{2.4}
\end{equation*}
$$

转换为利润

$$
\begin{equation*}
p_{i, j}=\text { 分配工人 } i \text { 执行任务 } j \text { 的利润 } \tag{2.5}
\end{equation*}
$$

并引入赋值变量

$$
x_{i, j}=\left\{\begin{array}{l}
1, \text { 若节点 } i \text { 与节点 } j \text { 匹配 }  \tag{2.6}\\
0, \text { 否则 }
\end{array} \quad,(i, j) \in E\right.
$$

该变量作为优化变量，表示工人 $i$ 是否被分配给任务 $j$。

优化目标函数为

$$
\begin{equation*}
\max _{x_{i, j}} \sum_{(i, j) \in E} p_{i, j} x_{i, j} \tag{2.7}
\end{equation*}
$$

约束条件为

$$
\begin{align*}
& \sum_{j:(i, j) \in E} x_{i, j} \leq 1, \quad i \in N  \tag{2.8}\\
& x_{i, j} \in\{0,1\}, \quad (i, j) \in E \tag{2.9}
\end{align*}
$$

以确保每个工人最多分配一个任务。

### 2.3 行为树生成

为了根据特定任务的目标动态生成解决不同任务的行为树，可以创建一个可用行为库。该库应包含相关智能体能够完成的行为。行为库中的行为随后可以以行为树的形式编排，使得满足特定于任务的目标条件。

### 2.3.1 行为库

一种行为库可以由一组可用行为、进入该行为前必须满足的条件以及该行为的效果组成[11, 12]。这种行为库可以总结为表格，表2.1展示了这样一个表格的示例。

表2.1：可用行为示例。该组行为可用于例如使用势场导航将无人机移动到指定点。

| 可用行为编号 | 动作 | 前置条件 | 效果 |
| :--- | :--- | :--- | :--- |
| 1 | 起飞 | - | 无人机处于飞行状态 |
| 2 | 从势场获取航点 | - | 拥有无碰撞航点 |
| 3 | 向航点移动 | 无人机处于飞行状态，拥有无碰撞航点 | 无人机到达指定点 |

### 2.3.2 生成算法

<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-27.jpg?height=1034&width=1286&top_left_y=551&top_left_x=391" width=500/>


图2.10：行为树生成算法的视觉概览。

基于第2.3.1节定义的行为库，应创建一个能够解决特定条件的行为树。其核心思想是通过首先添加目标条件，然后迭代地用能够解决该条件的行为扩展每个条件，并添加该行为指定的前置条件，从而确保特定目标条件被满足[11, 12]。每添加一个行为，需保证其前置条件已被满足才能进入该行为。图2.10展示了实现该算法的视觉表示；算法6和7对该算法也做了详细描述。

**算法 6：** 生成用于实现特定条件的行为树的伪代码  

<img src="https://cdn.mathpix.com/snip/images/BZBQNsAF7oIhif2GgtQvpPI2vid930dubGoeluJvNnU.original.fullsize.png" width=400 />


**算法 7：** 条件扩展的伪代码。

<img src="https://cdn.mathpix.com/snip/images/CeylJLqJj0Z5WmNFvjA0Ix92YXOxEjWhaNiCXrwqQFk.original.fullsize.png" width=400 />



### 2.3.3 包含安全需求

如第2.3.2节所述，为解决特定任务生成行为树时，可以同时包含执行生成行为树所需满足的各种安全检查。为包含这些安全检查，创建一个行为树，将安全检查与生成的行为树作为子节点添加到一个顺序节点中。图2.11展示了该扩展的实现方式。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-29.jpg?height=364&width=1029&top_left_y=426&top_left_x=516" width=500/>


图2.11：展示如何将为解决特定任务生成的行为树扩展以包含多种安全检查的视觉示意。

### 2.4 避撞

为了使多个自主智能体能够在同一空间内行动，有必要采用某种避撞机制，以尽量减少智能体间相撞的风险。解决此问题的方法多种多样，下一节介绍一种使用势场进行避撞的方法。

### 2.4.1 势场法

使用势场进行导航的理念是在目标点设置吸引（正）势场，在障碍物周围设置排斥（负）势场。智能体应沿势场线的方向移动。

点 $\vec{P}_{\text{source}}$ 周围的线性势场定义为

$$
\begin{equation*}
\vec{F}_{\text{linear}} = k_{\text{linear}} \cdot (\vec{P}_{\text{source}} - \vec{P}_{\text{agent}}) \tag{2.10}
\end{equation*}
$$

其中 $k_{\text{linear}}$ 为描述势场强度和方向的系数，$\vec{P}_{\text{agent}}$ 为智能体位置。若系数 $k_{\text{linear}}$ 为负，则势场为排斥场。

势场强度不必线性，可定义一个反比势场为

$$
\begin{equation*}
\vec{F}_{\text{inverse}} = \frac{k_{\text{inverse}}}{d_{\text{agent, source}}} \cdot \hat{D}_{\text{agent, source}} \tag{2.11}
\end{equation*}
$$

其中 $k_{\text{inverse}}$ 为描述势场强度和方向的系数，$\hat{D}_{\text{agent, source}}$ 为从智能体指向源点的单位向量，$d_{\text{agent, source}}$ 为智能体与源点间距离。

每个时间步，智能体移动的参考点 $\vec{P}_{\text{reference}}$ 定义为

$$
\begin{equation*}
\vec{P}_{\text{reference}} = \vec{P}_{\text{agent}} + \vec{F}_{\text{field}} \tag{2.12}
\end{equation*}
$$

其中 $\vec{P}_{\text{agent}}$ 是智能体当前位置，$\vec{F}_{\text{field}}$ 是当前位置处的势场值。为确保参考点距离合理，加入最大前瞻距离 $m_{\text{look ahead}} > 0$，使得

$$
\left|F_{\text{field}}\right| = \left\{
\begin{array}{ll}
\left|F_{\text{field}}\right|, & \text{若 } \left|F_{\text{field}}\right| < m_{\text{look ahead}} \\
m_{\text{look ahead}}, & \text{若 } \left|F_{\text{field}}\right| \geq m_{\text{look ahead}}
\end{array}
\right. \tag{2.13}
$$

## 第三章  实现

### 3.1 仿真环境

创建了一个仿真环境以支持快速测试和开发。本节描述所用软件的具体部分。

### 3.1.1 Gazebo

用于模拟仿真环境相关物理过程的软件是Gazebo[13]。Gazebo是一款开源机器人模拟器，兼容ROS，且在机器人社区广泛使用[14]。图3.1展示了Gazebo仿真环境的界面。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-31.jpg?height=537&width=1072&top_left_y=1599&top_left_x=498" width=500/>


图3.1：Gazebo仿真环境快照。左图为三架无人机初始配置，右图展示部分任务执行情况。

### 3.1.2 无人机模型

仿真环境中使用RotorS仿真包[15]中的AscTec Firefly模型作为实验的无人机平台。

### 3.2 物理环境

物理实验在卢勒奥理工大学的机器人实验室进行。

### 3.2.1 物理无人机

Crazyflie 2.0[16]是实验室环境下使用的无人机平台。Crazyflie是一款小型四旋翼无人机，商业可购且开源。通过配套USB加密狗，可直接利用ROS实现与无人机的无线通信。使用Crazyflie有多项优点：重量轻，撞击时不会对周围设备或人员造成显著伤害；价格低廉，易于获取备件且便于更换。但相比更昂贵的平台，飞行时间较短，飞行稳定性也有所欠缺。

### 3.2.2 位置追踪

使用Vicon动作捕捉系统以获取无人机实验中的当前位置和姿态，实现高精度定位。

### 3.3 机器人操作系统

机器人操作系统（ROS）[9] 是广泛使用的机器人软件开发平台。它是一个灵活的框架，作为结构化通信层连接软件各部分[17]。ROS开源，拥有丰富的工具、库及其他功能，简化并加速机器人软件开发。


### 3.3.1 节点（Nodes）

在ROS中，机器人软件的各个部分作为独立进程启动，称为ROS节点（ROS nodes），并通过主题（topics）连接，以实现节点间通信。这些节点可以分布式运行，通过任意网络进行通信。

### 3.3.2 主题（Topics）

ROS主题是具名的消息通道，允许节点相互连接并发送或接收数据。每个主题关联一个特定的数据类型，并定义为ROS消息。

### 3.3.3 消息（Messages）

ROS消息在编译前声明，定义可通过与特定ROS消息关联的主题发送的数据结构。

### 3.4 行为树框架

行为树使用BehaviorTree.cpp框架实现[18]。该框架与ROS集成良好且开源[19]。使用该框架的编程语言为$\mathrm{C}++$。

该框架支持使用可扩展标记语言（XML）格式定义行为树，构建行为树，然后以任意频率“滴答”行为树以执行。

### 3.5 拍卖系统

拍卖系统实现为一个ROS节点，接收新拍卖并在有新拍卖时通知拍卖客户端。客户端计算当前拍卖回合中每个任务的成本，并将竞标返回给拍卖服务器。拍卖服务器在收到一定数量竞标或经过特定时间后，优化任务分配，并公告各任务分配给的工人。

### 3.5.1 可用任务

为展示行为树生成算法和任务分配系统，定义了两个任务。“移动到点”要求智能体访问指定点，“探索”要求智能体先移动到起始位置，然后选择当前点附近的随机点并移动；到达后重复选择新随机点，持续指定时间。任务总结见表3.1，相关任务数据定义为

$$
\left\{\begin{array}{l}
x_{m}=\text { 目标 x 位置 }  \tag{3.1}\\
y_{m}=\text { 目标 y 位置 } \\
z_{m}=\text { 目标 z 位置 } \\
x_{e}=\text { 起始 x 位置 } \\
y_{e}=\text { 起始 y 位置 } \\
z_{e}=\text { 起始 z 位置 } \\
t=\text { 总探索时间 }
\end{array}\right.
$$

表3.1：使用的任务汇总。

| 任务名称 | 任务数据 |
| :---: | :---: |
| 移动到点 | $x_{m} ; y_{m} ; z_{m}$ |
| 探索 | $x_{e} ; y_{e} ; z_{e} ; t$ |

### 3.5.2 成本函数

为智能体可执行任务关联成本函数。并非每个智能体都对所有任务定义成本函数，仅为其可执行任务子集定义。任务（见3.5.1节）对应的成本函数为

$$
\begin{equation*}
f_{\text {cost }, \text { 移动到点 }}=d_{\mathrm{m}} \tag{3.2}
\end{equation*}
$$

及

$$
\begin{equation*}
f_{\text {cost }, \text { 探索 }}=d_{\mathrm{e}} \tag{3.3}
\end{equation*}
$$

其中 $d_{\mathrm{m}}$ 为至目标点距离，$d_{\mathrm{e}}$ 为至探索起点距离。

### 3.5.3 优化

第2.2.9节描述的优化问题使用Google开发的开源优化套件OR-Tools[20]实现。该框架支持多种求解器，此处采用SCIP[21]求解。

OR-Tools支持多种编程语言建模问题，本实现采用$\mathrm{C}++$定义和求解优化问题。

### 3.6 行为树生成

### 3.6.1 行为库

行为库以逗号分隔值（CSV）文件形式创建。其结构同2.3.1节所述，每行包含一个行为。行为库可针对每个智能体独特，并在运行时加载。

用于完成3.5.1节定义任务的行为库见表3.2，各任务关联的目标条件见表3.3。

表3.2：可用行为。完成可用任务使用的行为集。

| 编号 | 动作 | 前置条件 | 效果 |
| :--- | :--- | :--- | :--- |
| 1 | 起飞 | - | 无人机处于飞行状态 |
| 2 | 从势场获取航点 | - | 拥有无碰撞航点 |
| 3 | 向航点移动 | 无人机飞行中，拥有无碰撞航点 | 无人机到达指定点 |
| 4 | 获取新随机点 | - | 拥有随机点 |
| 5 | 探索 | 无人机到达指定点，拥有随机点 | 按指定时间完成探索 |

表3.3：与可用任务相关的起始条件。

| 任务 | 起始条件 |
| :---: | :---: |
| 移动到 | 无人机到达指定点 |
| 探索 | 按指定时间完成探索 |

### 3.6.2 XML生成

基于算法6和7生成的行为树，创建XML格式文本。该文本传递至3.4节描述的行为树框架，构建并执行行为树。

### 3.6.3 生成的行为树

为解决3.5.1节定义的两个可用任务生成的行为树分别见图3.2和3.3。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-36.jpg?height=583&width=1260&top_left_y=434&top_left_x=404" width=500/>


图3.2：为完成“移动到”任务生成的行为树。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-36.jpg?height=650&width=1260&top_left_y=1217&top_left_x=404" width=500/>


图3.3：为完成“探索”任务，探索多个随机点一段指定时间生成的行为树。

### 3.6.4 包含安全需求

为确保无人机保持安全高度，并演示任务重分配能力，如2.3.3节所述，在生成的完成任务行为树中顺序加入安全需求。安全需求确保若无人机飞行高度超过

$$
\begin{equation*}
z_{\max} = 1.8 \text{ 米。} \tag{3.4}
\end{equation*}
$$

行为树返回失败。安全需求还包含一个开关，用于模拟错误，要求无人机停止当前任务并返回起始位置。

### 3.6.5 任务重分配

若智能体因某些原因未完成当前任务，可将该任务返还拍卖服务器，允许重新分配给其他智能体。为防止失败任务被原智能体重新竞标，任务ID由智能体本地保存，该智能体不再对相同ID任务竞标。

### 3.6.6 备用行为树

未分配任务时执行标准行为树。该行为树首先在当前位置等待指定时间，随后将无人机移至并降落在起始位置。标准行为树见图3.4。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-37.jpg?height=987&width=1260&top_left_y=1334&top_left_x=401" width=500/>


图3.4：无任务时执行的标准行为树。


## 第四章  结果

### 4.1 评估案例

为了演示和评估任务分配系统及行为树生成的性能，设计了一系列实验。这些实验还旨在展示任务分配系统和行为树生成的一些理想特性，具体如下：

1. 一架无人机访问点网格。
2. 两架无人机访问点网格。
3. 三架无人机访问点网格。
4. 两架无人机尝试到达过高的点。
5. 三架无人机访问点网格，其中一架无人机模拟故障。
6. 多架无人机与多类型任务同时进行。

所有实验均在仿真环境和实验室环境中进行评估。

所有实验中拍卖系统使用的参数为：

$$
\left\{\begin{array}{l}
t_{\text {timeout }}=6 \text { 秒 }  \tag{4.1}\\
n_{\text {bids }}=\text { 实验中使用的无人机数量 }
\end{array}\right.
$$

势场由多个场合成：一个目标点的吸引线性场

$$
\begin{equation*}
\vec{F}_{\text {goal }}=k_{\text {goal }} \cdot\left(\vec{P}_{\text {goal }}-\vec{P}_{\text {agent }}\right), \tag{4.2}
\end{equation*}
$$

如式2.10所述，针对目标位置 $\vec{P}_{\text {goal }}$；以及每个其他无人机（障碍物）的排斥场

$$
\begin{equation*}
\vec{F}_{\text {obstacle }}=\frac{k_{\text {obstacle }}}{d_{\text {agent }, \text { obstacle }}} \cdot \hat{D}_{\text {agent }, \text { obstacle }}, \tag{4.3}
\end{equation*}
$$

如式2.11所述。势场参数定义为：

$$
\left\{\begin{array}{l}
k_{\text {goal }}=1.5  \tag{4.4}\\
k_{\text {obstacle }}=-0.8 \\
m_{\text {look ahead }}=0.8
\end{array}\right.
$$

### 4.2 实验1 - 一架无人机访问点

本实验创建一个“移动到点”任务网格。任务点位置为边长为

$$
\begin{equation*}
a=3 \text { 米 } \tag{4.5}
\end{equation*}
$$

的正方形内等间距的16个点，飞行高度为

$$
\begin{equation*}
z=1.5 \text { 米。 } \tag{4.6}
\end{equation*}
$$

所有任务同时发布至拍卖服务器。

### 4.2.1 仿真环境

图4.1和4.2展示了无人机在仿真环境中访问并完成任务的结果。无人机首先起飞并移动至成本最低的最近点，随后每完成一个任务便移动至当前位置最近的下一个点，直至所有任务完成，最后返回起始位置。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-40.jpg?height=706&width=909&top_left_y=484&top_left_x=642" width=500/>


图4.1：一架仿真无人机完成任务网格（“移动到点”任务）三维视图，实验1。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-40.jpg?height=749&width=957&top_left_y=1496&top_left_x=515" width=500/>


图4.2：一架仿真无人机完成任务网格（“移动到点”任务）二维视图，实验1。

### 4.2.2 实验室环境

图4.3和4.4展示了无人机在物理环境中访问并完成任务的结果。无人机起飞后先移动至成本最低的最近点，随后每完成一个任务便移动至当前位置最近的下一个点，直至所有任务完成，最后返回起始位置。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-41.jpg?height=707&width=917&top_left_y=880&top_left_x=638" width=500/>


图4.3：一架真实无人机完成任务网格（“移动到点”任务）三维视图，实验1。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-42.jpg?height=755&width=957&top_left_y=485&top_left_x=515" width=500/>


图4.4：一架真实无人机完成任务网格（“移动到点”任务）二维视图，实验1。

### 4.3 实验2 - 两架无人机访问点

本实验创建一个“移动到点”任务网格。任务点位置为边长为

$$
\begin{equation*}
a=3 \text { 米 } \tag{4.7}
\end{equation*}
$$

的正方形内等间距的16个点，飞行高度为

$$
\begin{equation*}
z=1.5 \text { 米。 } \tag{4.8}
\end{equation*}
$$

所有任务同时发布至拍卖服务器。

### 4.3.1 仿真环境

图4.5和4.6展示了两架无人机在仿真环境中访问并完成任务的结果。无人机起飞后先移动至两个总成本最低的最近点，随后每完成一个任务，分别移动至当前位置最近的下一个点，直至所有任务完成，最后返回各自起始位置。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-43.jpg?height=706&width=909&top_left_y=484&top_left_x=642" width=500/>


图4.5：两架仿真无人机完成任务网格（“移动到点”任务）三维视图，实验2。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-43.jpg?height=752&width=967&top_left_y=1497&top_left_x=513" width=500/>


图4.6：两架仿真无人机完成任务网格（“移动到点”任务）二维视图，实验2。

### 4.3.2 实验室环境

图4.7和4.8展示了两架无人机在物理环境中访问并完成任务的结果。无人机起飞后先移动至两个总成本最低的最近点，随后每完成一个任务，分别移动至当前位置最近的下一个点，直至所有任务完成，最后返回各自起始位置。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-44.jpg?height=709&width=927&top_left_y=882&top_left_x=633" width=500/>


图4.7：两架真实无人机完成任务网格（“移动到点”任务）三维视图，实验2。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-45.jpg?height=755&width=960&top_left_y=485&top_left_x=511" width=500/>


图4.8：两架真实无人机完成任务网格（“移动到点”任务）二维视图，实验2。

### 4.4 实验3 - 三架无人机访问点

本实验创建一个“移动到点”任务网格。任务点位置为边长为

$$
\begin{equation*}
a=3 \text { 米 } \tag{4.9}
\end{equation*}
$$

的正方形内等间距的16个点，飞行高度为

$$
\begin{equation*}
z=1.5 \text { 米。 } \tag{4.10}
\end{equation*}
$$

所有任务同时发布至拍卖服务器。

### 4.4.1 仿真环境

图4.9和4.10展示了三架无人机在仿真环境中访问并完成任务的结果。无人机起飞后先移动至三个总成本最低的最近点，随后每完成一个任务，分别移动至当前位置最近的下一个点，直至所有任务完成，最后返回各自起始位置。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-46.jpg?height=706&width=909&top_left_y=484&top_left_x=642" width=500/>


图4.9：三架仿真无人机完成任务网格（“移动到点”任务）三维视图，实验3。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-46.jpg?height=752&width=957&top_left_y=1497&top_left_x=515" width=500/>


图4.10：三架仿真无人机完成任务网格（“移动到点”任务）二维视图，实验3。

### 4.4.2 实验室环境

图4.11和4.12展示了三架无人机在物理环境中访问并完成任务的结果。无人机起飞后先移动至三个总成本最低的最近点，随后每完成一个任务，分别移动至当前位置最近的下一个点，直至所有任务完成，最后返回各自起始位置。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-47.jpg?height=706&width=917&top_left_y=929&top_left_x=638" width=500/>


图4.11：三架真实无人机完成任务网格（“移动到点”任务）三维视图，实验3。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-48.jpg?height=752&width=957&top_left_y=486&top_left_x=515" width=500/>


图4.12：三架真实无人机完成任务网格（“移动到点”任务）二维视图，实验3。


### 4.5 实验4 - 两架无人机任务重分配

本实验创建了三个“移动到点”任务。其中一个任务位置过高，智能体无法达到，执行该任务的智能体将失败并将任务返还给拍卖服务器，如3.6.4节和3.6.5节所述。三任务详情见表4.1，任务同时发布至拍卖服务器。

表4.1：用于演示任务重分配的三个任务，实验4。

| 任务名称 | 任务数据 |
| :---: | :---: |
| 移动到点 | $-0.5 ; 4 ; 1$ |
| 移动到点 | $1 ; 4 ; 1$ |
| 移动到点 | $0 ; 4 ; 2.5$ |

### 4.5.1 仿真环境

图4.13和4.14展示了两架仿真无人机尝试访问三个点的过程。两任务完成后，一架无人机尝试完成最后一任务但因飞高于允许最大高度失败，任务返还拍卖服务器，以便重新分配给另一架无人机。随后另一无人机重复尝试完成该任务。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-49.jpg?height=712&width=915&top_left_y=666&top_left_x=642" width=500/>


图4.13：两架仿真无人机失败并重分配任务的三维视图，实验4。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-50.jpg?height=752&width=952&top_left_y=486&top_left_x=518" width=500/>


图4.14：两架仿真无人机失败并重分配任务的二维视图，实验4。

### 4.5.2 实验室环境

图4.15和4.16展示了两架无人机在物理环境中尝试访问三个点。两任务完成后，一架无人机尝试完成最后一任务但因飞高于允许最大高度失败，任务返还拍卖服务器，以便重新分配给另一架无人机。随后另一无人机重复尝试完成该任务。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-51.jpg?height=706&width=923&top_left_y=484&top_left_x=632" width=500/>


图4.15：两架真实无人机失败并重分配任务的三维视图，实验4。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-51.jpg?height=761&width=957&top_left_y=1490&top_left_x=518" width=500/>


图4.16：两架真实无人机失败并重分配任务的二维视图，实验4。

### 4.6 实验5 - 三架无人机访问点及无人机故障

本实验创建一个“移动到点”任务网格，并引入模拟故障，使一架无人机中断当前任务并返回起始位置。任务点位置为边长为

$$
\begin{equation*}
a=3 \text { 米 } \tag{4.11}
\end{equation*}
$$

的正方形内等间距的16个点，高度为

$$
\begin{equation*}
z=1.5 \text { 米。 } \tag{4.12}
\end{equation*}
$$

所有任务同时发布至拍卖服务器，故障发生在一架无人机完成两项任务后。

### 4.6.1 仿真环境

图4.17和4.18展示了三架无人机在仿真环境中访问并完成任务。无人机起飞后先移动至三个总成本最低的点。两架无人机持续完成任务，第三架在执行第三个任务时接收到模拟故障信号，中断任务返回拍卖服务器并返回起始位置。其余两架无人机完成该任务，确保所有任务完成。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-53.jpg?height=706&width=909&top_left_y=484&top_left_x=642" width=500/>


图4.17：三架仿真无人机完成“移动到点”任务网格，含一架故障模拟，实验5。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-53.jpg?height=752&width=958&top_left_y=1540&top_left_x=512" width=500/>


图4.18：三架仿真无人机完成“移动到点”任务网格二维视图，含一架故障模拟，实验5。

### 4.6.2 实验室环境

图4.19和4.20展示了三架无人机在物理环境中访问并完成任务。三架无人机起飞后先移动至三个总成本最低的点。两架无人机持续完成任务，第三架在执行第三任务时接收到模拟故障信号，中断任务返回拍卖服务器并返回起始位置。其余两架无人机完成该任务，确保所有任务完成。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-54.jpg?height=712&width=929&top_left_y=1106&top_left_x=626" width=500/>


图4.19：三架真实无人机完成“移动到点”任务网格，含一架故障模拟，实验5。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-55.jpg?height=752&width=957&top_left_y=486&top_left_x=515" width=500/>


图4.20：三架真实无人机完成“移动到点”任务网格二维视图，含一架故障模拟，实验5。

### 4.7 实验6 - 三架无人机完成多类型任务

本实验使用多类型任务。包括一个“移动到点”任务网格，16个边长为

$$
\begin{equation*}
a=3 \text { 米 } \tag{4.13}
\end{equation*}
$$

的正方形内等间距点，高度

$$
\begin{equation*}
z=1.5 \text { 米，} \tag{4.14}
\end{equation*}
$$

以及一个“探索”任务，起始位置为

$$
\left\{\begin{array}{l}
x_{e}=0  \tag{4.15}\\
y_{e}=4 \\
z_{e}=1.5
\end{array}\right.
$$

探索时间为

$$
\begin{equation*}
t=40 \text { 秒。 } \tag{4.16}
\end{equation*}
$$

“探索”任务先发布至拍卖系统，随后发布所有“移动到点”任务。

### 4.7.1 仿真环境

图4.21和4.22展示了三架无人机访问16点并完成探索任务的过程。“探索”任务首先分配给一架无人机。因探索任务耗时较长，其他两架无人机完成所有“移动到点”任务。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-56.jpg?height=712&width=932&top_left_y=832&top_left_x=628" width=500/>


图4.21：三架仿真无人机完成“移动到点”任务网格及一项“探索”任务，实验6。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-57.jpg?height=751&width=947&top_left_y=484&top_left_x=523" width=500/>


图4.22：三架仿真无人机完成“移动到点”任务网格及一项“探索”任务二维视图，实验6。

### 4.7.2 实验室环境

图4.23和4.24展示了三架无人机访问16点并完成探索任务的过程。“探索”任务首先分配给一架无人机。因探索任务耗时较长，其他两架无人机完成所有“移动到点”任务。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-58.jpg?height=712&width=915&top_left_y=484&top_left_x=642" width=500/>


图4.23：三架真实无人机完成“移动到点”任务网格及一项“探索”任务，实验6。
<img src="https://cdn.mathpix.com/cropped/2025_07_19_cd3900321355e83480e8g-58.jpg?height=749&width=963&top_left_y=1539&top_left_x=518" width=500/>


图4.24：三架真实无人机完成“移动到点”任务网格及一项“探索”任务二维视图，实验6。


## 第五章  结论

### 5.1 结果

项目以有希望的方式展示了，使用建议的方法能够解决任务分配问题以及利用行为库生成特定任务行为树的问题。从所有实验中可以看出，多智能体作为团队能够完成多项任务和不同类型任务。该方法还显示出灵活性和鲁棒性，允许在执行过程中动态处理任务失败及重分配。采用行为库生成解决特定任务的整体行为树，证明了仅设计行为及其对智能体影响即可设计响应式智能体的可能性，从而便于通过新增行为扩展能力而不影响现有功能。

### 5.2 行为树及行为树生成

行为树概念易于使用，能直观理解树将执行的操作。行为树易于扩展且模块化，可以替换单个行为且对其他部分影响最小。行为库使得只需设计行为、运行该行为所需条件及其对系统的影响，而无需过多担忧最终行为树如何构建。

### 5.3 基于市场的任务分配

所提任务分配结构解决了利用多智能体完成多任务的问题。需注意该方法不一定产生全局最优解，但具备如下优点：运行时引入新任务无需重新规划；结构支持任务失败时动态重分配；适用于任意数量的智能体、智能体类型及任务类型。

### 5.4 实现环境

使用ROS和Gazebo为在仿真环境中开发和测试提供了极好平台，随后顺利迁移到实验室环境。两环境中使用完全相同代码，仅简单映射相关主题，确保里程计数据来自实验室的Vicon系统，位置指令发送至真实无人机而非仿真无人机。

如第4章结果所示，从仿真环境向物理环境的过渡非常顺利，部分原因在于低级控制未被考虑，且假设在两环境中均正常工作。

### 5.5 总结与讨论

这是一个非常令人兴奋且富有教育意义的项目。它使我得以探索行为树和多机器人任务分配的研究领域。我认为所得到的任务分配系统及基于行为库和当前目标生成行为树的思路具有趣味性和潜力，可望应用于实际实现。

### 5.6 未来工作

拍卖系统可扩展支持多机器人（同质及异质）组队并共同竞争部分任务。

拍卖式任务分配独立于行为树生成，允许未使用行为库及行为树生成方法的智能体并存。这意味着若该结构用于其他场景，可以较容易地允许部分智能体拥有其他内部控制结构，并在同一空间内竞争相同或不同任务。这对实现多类型智能体非常重要，如用于物品配送的无人机和可创建任务以提取物品并送至指定地点的仓库；同时还有智能体确保正确物品位于正确位置并准备被无人机拾取。

未来工作还可包括改进如何根据生成的行为树及智能体当前状态计算任务成本，如根据当前状态预期出现的“子任务”成本求和，例如着陆、起飞、移动至某位置等。


## Bibliography

[1] Google wing. https://wing.com. Accessed: 2022-02-01.

[2] Amazon prime air. https://www.amazon.com/Amazon-Prime-Air/b? ie=UTF\&\&node=8037720011. Accessed: 2022-02-01.

[3] Matteo Iovino, Edvards Scukins, Jonathan Styrud, Petter Ögren, and Christian Smith. A survey of behavior trees in robotics and AI. CoRR, abs/2005.05842, 2020. URL https://arxiv.org/abs/2005.05842.

[4] Christopher Iliffe Sprague, Özer Özkahraman, Andrea Munafò, Rachel Marlow, Alexander B. Phillips, and Petter Ögren. Improving the modularity of AUV control systems using behaviour trees. CoRR, abs/1811.00426, 2018. URL http://arxiv.org/abs/1811.00426.

[5] Steve Rabin Ed. Game AI Pro, chapter 6. The Behavior Tree Starter Kit. CRC Press, 2013.

[6] Michele Colledanchise and Petter Ögren. Behavior trees in robotics and ai. Jul 2018. doi: 10.1201/9780429489105. URL http://dx.doi.org/ 10.1201/9780429489105.

[7] Anthony (Tony) Stentz and M. Bernardine Dias. A free market architecture for coordinating multiple robots. Technical Report CMU-RI-TR-99-42, Carnegie Mellon University, Pittsburgh, PA, December 1999.

[8] M.B. Dias, R. Zlot, N. Kalra, and A. Stentz. Market-based multirobot coordination: A survey and analysis. Proceedings of the IEEE, 94(7): 1257-1270, 2006. doi: 10.1109/JPROC.2006.876939.

[9] Stanford Artificial Intelligence Laboratory et al. Robotic operating system. URL https://www.ros.org.

[10] Mikael Rönnqvist Jan Lundgren and Peter Värbrand. Optimeringslära. Studentliteratur, 2008.

[11] Michele Colledanchise, Diogo Almeida, and Petter Ogren. Towards blended reactive planning and acting using behavior trees. pages 88398845, 05 2019. doi: 10.1109/ICRA.2019.8794128.

[12] Tadewos G. Tadewos, Laya Shamgah, and Ali Karimoddini. On-thefly decentralized tasking of autonomous vehicles. In 2019 IEEE 58th Conference on Decision and Control (CDC), pages 2770-2775, 2019. doi: 10.1109/CDC40024.2019.9029554.

[13] N. Koenig and A. Howard. Design and use paradigms for gazebo, an open-source multi-robot simulator. In 2004 IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS) (IEEE Cat. No.04CH37566), volume 3, pages 2149-2154 vol.3, 2004. doi: 10.1109/IROS.2004.1389727.

[14] Patricio Castillo-Pizarro, Tomas V. Arredondo, and Miguel TorresTorriti. Introductory survey to open-source mobile robot simulation software. In 2010 Latin American Robotics Symposium and Intelligent Robotics Meeting, pages 150-155, 2010. doi: 10.1109/LARS.2010.19.

[15] Fadri Furrer, Michael Burri, Markus Achtelik, and Roland Siegwart. Robot Operating System (ROS): The Complete Reference (Volume 1), chapter RotorS-A Modular Gazebo MAV Simulator Framework, pages 595-625. Springer International Publishing, Cham, 2016. ISBN 978-3-319-26054-9. doi: 10.1007/978-3-319-26054-9_23. URL http://dx. doi.org/10.1007/978-3-319-26054-9_23.

[16] bitcraze. Crazyflie 2.0. https://www.bitcraze.io/products/ old-products/crazyflie-2-0/. Accessed: 2022-02-01.

[17] Morgan Quigley. Ros: an open-source robot operating system. In ICRA 2009, 2009.

[18] Michele Colledanchise and Davide Faconti. Behaviortree.cpp. https: //github.com/BehaviorTree/BehaviorTree.CPP, 2022.

[19] Razan Ghzouli, Thorsten Berger, Einar Broch Johnsen, Swaib Dragule, and Andrzej Wasowski. Behavior trees in action: A study of robotics applications. CoRR, abs/2010.06256, 2020. URL https://arxiv.org/ abs/2010.06256.

[20] Laurent Perron and Vincent Furnon. Or-tools. URL https:// developers.google.com/optimization/.

[21] Gerald Gamrath, Daniel Anderson, Ksenia Bestuzheva, Wei-Kun Chen, Leon Eifler, Maxime Gasse, Patrick Gemander, Ambros Gleixner, Leona Gottwald, Katrin Halbig, Gregor Hendel, Christopher Hojny, Thorsten Koch, Pierre Le Bodic, Stephen J. Maher, Frederic Matter, Matthias Miltenberger, Erik Mühmer, Benjamin Müller, Marc E. Pfetsch, Franziska Schlösser, Felipe Serrano, Yuji Shinano, Christine Tawfik, Stefan Vigerske, Fabian Wegscheider, Dieter Weninger, and Jakob Witzig. The SCIP Optimization Suite 7.0. Technical report, Optimization Online, March 2020. URL http://www.optimization-online.org/DB_ HTML/2020/03/7705.html.


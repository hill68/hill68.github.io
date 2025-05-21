+++
date = '2025-05-21T15:44:38+08:00'
draft = false
title = 'JSBSim User manual'
+++

# 用户手册



https://jsbsim-team.github.io/jsbsim-reference-manual/

本部分解释了如何使用 JSBSim 进行模拟运行、创建飞行器模型、编写脚本，以及如何执行其他不涉及对 JSBSim 程序代码进行更改的任务。

JSBSim 软件提供了许多现成可用的飞行器模型示例。一旦用户熟悉了进行模拟所需的所有步骤和设置，可能会希望查看这些示例，并详细了解更有经验的 JSBSim 用户是如何实现某些特定模型的。

*该项目和发行版中包含的飞行器模型不包含任何专有、敏感或机密数据。所有数据均来源于教材（如 Stevens 和 Lewis 的《Aircraft Control and Simulation》以及 Sutton 的《Rocket Propulsion Elements》）、公开的技术报告（见：[NASA技术报告网站](https://ntrs.nasa.gov/)和[AIAA网站](https://www.aiaa.org/)），或其他公开数据（如[FAA网站](https://www.faa.gov/)）。JSBSim 发行版中包含的飞行器模型，以及与现有商业或军事飞行器名称相对应的模型，都是基于公开信息制作的近似模型，仅供教育或娱乐使用。*

---

## 概述

### 什么是 JSBSim？

从应用程序编程的角度来看，JSBSim 是一个主要用 C++ 编程语言编写的程序代码集合（其中包括一些 C 语言例程）。组成 JSBSim 的一些 C++ 类用于建模物理实体，如大气、飞行控制系统或引擎。某些类封装了诸如运动方程、矩阵、四元数或向量等概念或数学构造。一些类管理其他对象的集合。总的来说，JSBSim 应用程序接受控制输入，计算并汇总来自这些控制输入和环境的力矩，并在离散时间步中推进飞行器的状态（速度、方位、位置等）。

JSBSim 已经在各种平台上构建和运行，如 Windows 或 Linux 系统上的 PC、苹果 Macintosh 以及硅谷图形公司的 IRIX 操作系统。自由的 GNU g++ 编译器可以轻松编译 JSBSim，其他如 Borland 和 Microsoft 的编译器也能很好地工作。更多信息请参见《程序员指南》。

从最终用户的角度来看（例如进行研究的学生），JSBSim 可以被视为一个“黑箱”，它通过 XML 格式的输入文件进行提供。这些 XML 文件包含了航天器、引擎、脚本等的描述。当这些文件被加载到 JSBSim 中时，它们指示 JSBSim 模拟该飞行器的飞行情况，作为更大仿真框架的一部分（例如 FlightGear 或 OpenEaagles），或者在批处理模式下以比实际时间更快的速度运行。每次运行 JSBSim 都会生成包含模拟飞行器性能和动态数据的文件。

从软件集成者的角度来看（例如将 JSBSim 集成到更大仿真框架中的人员），JSBSim 是一个可以被调用的库，提供输入（如飞行员的控制输入），并返回输出（描述飞行器在某一时刻的位置）。

### 它适合谁，如何使用？

JSBSim 飞行动力学模型（FDM）软件库旨在易于理解，特别适合高年级航空航天工程学生。由于其配置简便，它也已被业界专业人士用于多种场景。它已经被集成到更大、更全面的飞行模拟应用程序和架构中（例如 FlightGear、Outerra 和 OpenEaagles），并且已被用作工业和学术界的批量模拟工具。

## 使用示例

### Aerocross Echo Hawk

JSBSim 被用于 Aerocross Echo Hawk UAV 的硬件在环（HITL）测试。编写了自定义代码以通过 RS-232/422/485、模拟模拟输入/输出、离散输入/输出和套接字与飞行硬件（基于 PC/104 的系统）接口，但核心仿真代码仍为未经修改的 JSBSim 代码。飞行员/操作员培训也依赖于 JSBSim 作为六自由度（6-DoF）仿真模型。

### DuPont Aerospace 公司

JSBSim 曾在 DuPont Aerospace 公司与 Matlab 一起用于实时 HITL 仿真和飞行员/操作员培训。DuPont Aerospace 公司的 Rex duPont 解释了该项目：

> 在 1990 年代，DuPont Aerospace 公司正在研发一种飞机，测试其垂直起降风扇喷气运输机的概念。我们开发了一个基于 Microsoft Windows 的飞行模拟器，用于测试拟议飞行器的飞行特性。然而，我们需要一个可以在实时中使用的仿真系统，以便能够在操作飞行执行器的全尺寸模型上测试飞行特性。我们最终选择了 FlightGear 模拟器，并使用了 JSBSim 飞行动力学模型，因为我们可以获得完整的代码，它的组织方式很好，使我们能够创建新的子程序来匹配我们的飞机，同时也有可用的支持。
>
> 我们同时开发了一个 Matlab 模拟器，用于开发更有效的自动驾驶仪引导系统，因为我们的主要任务是仅使用自动驾驶仪起飞并保持悬停 30 秒。这将明确显示控制系统是否足够强大。因此，我们在 Matlab 模拟器和 JSBSim 派生模拟器的每个相关模块中内建了一系列单元测试，提供一系列输入以交叉验证，确保两个系统同步。
>
> 我们使用 JSBSim 系统测试了一些 Matlab 模型难以测试的动态问题，尤其是涉及飞行员感受和过渡至悬停过程中的可控性问题。这些问题在纯控制系统领域（如 Matlab 中）很难评估，因为过渡过程中，基础的力结构随着空气动力学力量的增强和纯推力控制力的减少而不断变化。
>
> 我们还对气动仿真中关键参数的估算误差敏感度进行了参数研究。这些研究通过让飞行员执行一系列标准机动来完成，目的是测试当一个或多个参数降低 50% 时飞机的响应（飞行员不知道是哪一个参数被改变）。
>
> 我们也对伺服带宽进行了模拟，测试飞行特性在何种情况下变得不可接受。这有助于定义所需的特性。飞行员对控制系统的需求几乎总是与理论上最佳的参数不同。
>
> 此外，我们开发了多种 HUD 显示系统，以便在悬停过程中操作时提供帮助，在这种情况下需要非常精确的地面速度控制。最终我们实现了一个系统，允许一个甚至没有飞行员执照的年轻工程师起飞并保持悬停在恒定高度 30 秒以上，偏差不超过 1 英尺。
>
> 我们最终于 2007 年 9 月 30 日成功实现了自动驾驶仪控制的起飞和悬停，两次飞行的持续时间大约为 45 秒。两次飞行都因为其中一台发动机燃料耗尽而终止，而不是因为控制问题。

### MITRE 空中交通研究

JSBSim 在 MITRE 用于开发一个 6-DoF 模拟系统，模拟飞行管理系统（FMS）在连续下降进场（CDA）和优化下降进场（OPD）过程中的行为。MITRE 使用了 JSBSim 的独立版本（用于批量运行）和与 FlightGear 集成的版本。此外，还创建了附加的控制系统组件，以支持特定的横向和纵向导航研究。

JSBSim 还被扩展为通过套接字向 MITRE 的其他应用程序输出消息，该应用程序提供了类似于空中交通管制员所看到的视图。

### 美国交通部

在与美国交通部合作的项目中，开发了一个使用 JSBSim 作为六自由度（6-DoF）仿真核心的人类飞行员数学模型。

### 意大利那不勒斯大学 Federico II

那不勒斯大学拥有一个基于 FlightGear 和 JSBSim 的运动座舱飞行/驾驶模拟器。该模拟器具有三屏视觉显示，提供 190 度的视场。JSBSim 源代码经过修改，提供了力反馈能力。

JSBSim 在那不勒斯大学被用作支持近地飞行操作风险评估的工具。考虑到碰撞风险研究中的实际问题之一是评估在机场区域内新增障碍物（如建筑物或雷达塔）对飞行操作的威胁。风险评估是通过改变障碍物的几何形状和位置来进行的。评估程序基于对飞机轨迹与“正常”飞行路径的统计偏差分析，评估某一轨迹穿越给定的“保护”区域的概率。为此框架，操作场景被正式描述和实现，以便运行多个计算机模拟。

### 弗劳恩霍夫风能系统研究所

在与意大利那不勒斯大学 Federico II 合作的研究中，弗劳恩霍夫风能系统研究所（IWES）的研究人员研究了轻型飞机飞行通过或接近风力涡轮机尾流时的尾流相遇问题。

为了这项研究，开发了一个软件应用程序框架，用于生成和控制在指定风场和湍流场下的飞行仿真场景。JSBSim 被用作该框架中的飞行动力学模型，并通过调整其自动驾驶仪系统来模拟飞行员在导航过程中的真实行为。风力涡轮机尾流中的风分布是通过 OpenFOAM 计算的，并作为输入提供给动态模型。

### 南非试飞学院（TFASA）

南非试飞学院使用 JSBSim 作为地面可变稳定系统（VSS）模拟器的基础，用于飞行员训练。基础飞行器模型的空气动力学稳定性和控制系数被修改，以展示它们对飞行任务的影响。还对执行器进行了建模，以展示它们在不同延迟、滞后和速率限制条件下对飞行员输入输出（PIO）的潜在影响。

模拟的飞行器包括固定翼飞机和旋翼飞机，并配有可编程的力反馈控制装置，用于展示飞行控制机械特性（FCMC）的影响。

JSBSim 与 Prepar3D 集成，利用 Prepar3D 的外部视觉系统，渲染到三块大型 LCD 屏幕或通过三投影仪系统呈现 180 度视图。

# 仿真

虽然 JSBSim 用户不需要了解飞行模拟器操作的所有细节，但理解其基本工作原理是有帮助的。以下是一些重要的概念。

- 参考坐标系用于描述飞行器模型中各种项目的位置和布局。
- 在定义飞行器模型时，单位的指定具有灵活性——支持英制单位和公制单位。
- 使用“属性”使得 JSBSim 成为一个通用模拟器，提供了一种通过参数（或变量）接口与各种系统进行交互的方法。属性广泛用于描述飞机和发动机特性配置文件中。
- 数学在飞行物理建模中发挥着重要作用。JSBSim 使用数据表格，因为飞行动力学特性通常存储在表格中。JSBSim 还允许设置任意代数函数，从而广泛自由地描述气动和飞行控制特性。
- 用户至少需要具备基本的飞行器飞行力学知识，了解飞机飞行时的常规力和力矩。
- 理解飞行控制和系统建模方法是成功和有效仿真的关键。

# 参考坐标系

在描述对象的位置、飞机的姿态和方向，或为给定的飞行条件指定输入时，需要理解一些基本的参考坐标系。以下是对这些坐标系的简要介绍：

## 结构坐标系，或“构造坐标系”

该坐标系是常见的制造商参考坐标系，用于定义飞机上的各个点，例如重心位置、所有轮子的位置信息、飞行员视点、点质量、推进器等。JSBSim 飞机配置文件中的项目就是使用此坐标系来定位的。

在结构坐标系中，X 轴沿着机身长度方向延伸，指向飞机尾部，Y 轴指向飞机右翼，Z 轴则朝上。通常，这个坐标系的原点 $O_C$ 位于飞机前部（例如机头尖端、单发动机飞机的机头防火墙处，或者位于机头前方的一段距离）。这个坐标系通常被命名为 $\mathcal{F}_{\mathrm{C}}=\left\{O_{\mathrm{C}}, x_{\mathrm{C}}, y_{\mathrm{C}}, z_{\mathrm{C}}\right\}$.。

![结构坐标系](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_construction_axes.svg)

结构（或构造）坐标系的飞机参考坐标系，原点 $O_C$。除了结构坐标系轴 $x_C$, $y_C$, zC 外，还展示了标准机体坐标系轴 $x_B$, $y_B$, $z_B$，它们的原点在重心 G 处。飞行员的视点位于 PEP。

X 轴通常与机身中心线重合，并且通常与推力轴重合（例如在单发动机螺旋桨飞机中，它通过螺旋桨轴心）。沿 $x_C$ 轴的位置称为站位，沿 zC 轴的位置称为水线位置，沿 $y_C$ 轴的位置称为尾线位置。

![结构坐标系](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172x_blender.png)

这是从 3D 建模软件 Blender 中截取的屏幕截图，展示了 Cessna 172 的模型及其结构坐标系 $\mathcal{F}_{\mathrm{C}}=\left\{O_{\mathrm{C}}, x_{\mathrm{C}}, y_{\mathrm{C}}, z_{\mathrm{C}}\right\}$。在这个例子中，原点 $O_C$ 位于驾驶舱内，靠近仪表盘。

注意，JSBSim 模拟的飞机的原点可以位于任意位置，因为 JSBSim 内部仅使用重心（CG）与各个物体之间的*相对距离*——而不是物体的绝对位置。

![重心位置](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_center_of_gravity.svg)

在结构坐标系中确定的重心位置（CG）为点 G。

![地面反作用力](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_ground_reaction.svg)

根据结构坐标系位置定义的地面接触点。

![关键位置](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_sideview.svg)

结构坐标系中的两个关键点位置 $P_{\mathrm{ARP}}$ 和 $P_{\mathrm{CG}, \mathrm{EW}}$ ，分别为气动力矩的极点和空重 CG（空机重心）。机翼根部的形状和弦长也被勾画出来。

![关键位置](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_perspective_view_left.svg)

除了 $P_{\mathrm{CG}, \mathrm{EW}}$，还展示了两个重要的位置，$P_{\text {Pilot }}$ 和 $P_{\text {Right Pass }}$ ，分别代表飞行员和右侧乘客的质量集中点。

## 机体坐标系

在 JSBSim 中，机体坐标系类似于结构坐标系，但沿 $y_C$ 轴旋转 180 度，原点与重心（CG）重合。通常，机体坐标系是通过已知飞机重心 G 位置和纵向结构轴 $x_C$ 方向来定义的。$x_B$ 轴应选择与 $x_C$ 轴平行，且从 G 指向机头的正方向。

机体轴坐标系通常命名为$\mathcal{F}_{\mathrm{B}}=\left\{G, x_{\mathrm{B}}, y_{\mathrm{B}}, z_{\mathrm{B}}\right\}$。$x_B$ 轴称为*滚转轴*，指向前方，$y_B$ 轴称为*俯仰轴*，指向右翼，$z_B$ 轴称为*偏航轴*，指向飞机腹部。

![机体轴](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_body_axes.svg)

标准的飞机机体轴坐标系，原点在重心 G 处。

在机体坐标系中，飞机的力和力矩被相加，结果加速度被积分以得到速度。

## 稳定坐标系，或“气动坐标系”

这个坐标系是根据相对风矢量相对于机体的瞬时方向来定义的。如果为了简化假设空气相对于地球静止（无风），且 $\boldsymbol{V}$ 是飞机质心相对于地球固定观察者的速度矢量（也称为 $\boldsymbol{V}_{\mathrm{CM} / \mathrm{E}}$，以强调相对运动），那么 $-\boldsymbol{V}$ 就是相对风速，$V=\|V\|$是空速。

该坐标系命名为 $\mathcal{F}_{\mathrm{A}}=\left\{G, x_{\mathrm{A}}, y_{\mathrm{A}}, z_{\mathrm{A}}\right\}$，其中轴 $x_{\mathrm{A}}$ 指向相对风矢量投影到飞机对称平面 $x_{\mathrm{B}} z_{\mathrm{B}}$ 上的方向。轴 $y_{\mathrm{A}}$ 仍然指向右翼，并与机体轴 $y_{\mathrm{B}}$ 重合，轴 $z_{\mathrm{A}}$ 完成右手坐标系。

![Alpha 和 Beta](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_aero_axes.svg)

气动坐标系，定义了气动角度 $\alpha_{\mathrm{B}}$ 和 $\beta$.

这两个轴 $x_{\mathrm{A}}$ 和 $z_{\mathrm{A}}$ 根据定义属于飞机的对称面，但它们在飞行过程中可能会旋转，因为相对风速矢量 $V$ 相对于飞行器的方向可能会发生变化。上图展示了如何构建气动坐标系。两个轴 $x_{\mathrm{A}}$ 和 $x_{\mathrm{B}}$ 之间的夹角是飞机的迎角 $\alpha_{\mathrm{B}}$。相对风的瞬时方向 $\boldsymbol{V}$ 与其在平面 $x_{\mathrm{B}} z_{\mathrm{B}}$ 上的投影之间形成的夹角是侧滑角 $\beta$。

这个坐标系，在一些手册中被称为稳定坐标系，在此也称为“气动坐标系”，因为瞬时气动合力 $\mathcal{F}_{\mathrm{A}}$ 在 $z_{\mathrm{A}}$ 轴上的投影 $Z_{\mathrm{A}}$ 定义了气动升力。具体来说，升力 $L$ 是这样定义的：$-L$ 是气动合力 $\mathcal{F}_{\mathrm{A}}$ 沿 $z_{\mathrm{A}}$ 轴的分量，即 $Z_{\mathrm{A}}=-L$。

为了更好地理解上述描述，考虑一个在飞行力学中常见的典型动作：零侧滑（或“协调”）、保持恒定高度的匀速转弯。在这种情况下，机翼会倾斜，升力也会倾斜。在这种转弯中，气动合力 $\mathcal{F}_{\mathrm{A}}$ 是倾斜的，而 $x_{\mathrm{A}}$ 轴保持水平。一般来说，升力作为一个矢量总是定义在飞机的对称面内。

![水平转弯](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/three_d_forces_level_turn.svg)

在恒定高度的匀速协调转弯中，倾斜升力的情况。倾斜角 $\phi_{\mathrm{W}}$ 是绕相对风速矢量的旋转。当速度矢量与北方对齐时，运动被定格在时间上。协调转弯意味着 $\beta=0$，恒定高度意味着 $x_{\mathrm{A}}$ 轴保持水平。

*备注* —— 在动态稳定性研究中，“稳定坐标系”与上述的气动坐标系略有不同：飞机飞行力学和稳定性约定中的稳定坐标系不过是一个特定的机体固定坐标系，定义是基于初始的对称、稳定、机翼水平、恒定高度的飞行状态。该状态给出了 $x_S$ 的方向（在该特定飞行姿态下与 $x_A$ 重合）。因此，在动态稳定性研究中，稳定坐标系与气动坐标系不同，是固定在飞行器上的。

在 JSBSim 中，稳定坐标系 $\mathcal{F}_{\mathrm{S}}=\left\{G, x_{\mathrm{S}}, y_{\mathrm{S}}, z_{\mathrm{S}}\right\}$ 代表了气动坐标系。

## 地心惯性坐标系（ECI）和地心固定坐标系（ECEF）

地心惯性坐标系（或简称“惯性坐标系”）$\mathcal{F}_{\mathrm{ECI}}=\left\{O_{\mathrm{ECI}}, x_{\mathrm{ECI}}, y_{\mathrm{ECI}}, z_{\mathrm{ECI}}\right\}$固定，其原点位于地球中心。其笛卡尔坐标轴相对于恒星保持固定，为飞机（或航天器）运动方程提供最简洁的参考坐标系。正 $z_{\mathrm{ECI}}$ 轴穿过地球的地理北极。$x_{\mathrm{ECI}}$ 和 $y_{\mathrm{ECI}}$ 轴位于赤道平面内。$x_{\mathrm{ECI}}$ 轴始终与从太阳质心到地球在春分时的轨道位置的连线平行。下图展示了 ECI 系统。

![惯性坐标系](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/inertial_frame.svg)

地心惯性（ECI）坐标系和地心固定（ECEF）坐标系。

地心地固参考系（ECEF）的坐标轴，如 $x_{\mathrm{ECEF}}$、$y_{\mathrm{ECEF}}$ 和 $z_{\mathrm{ECEF}}$，也如上图所示。ECEF 坐标轴相对于地球保持固定。这个笛卡尔系统的原点 $O_{\mathrm{ECEF}}$，与惯性坐标系一样，位于地球的质心。$z_{\mathrm{ECEF}}$ 轴也沿着地球的自转轴，并与 $z_{\mathrm{ECI}}$ 轴重合。$x_{\mathrm{ECEF}}$ 和 $y_{\mathrm{ECEF}}$ 轴都位于赤道平面内，且正 $x_{\mathrm{ECEF}}$ 轴通过本初子午线（格林威治子午线）。ECEF 坐标系绕惯性坐标系的 $z_{\mathrm{ECI}}$ 轴以角速度 $\omega_{\mathrm{E}}$ 逆时针旋转。地球的角速度 $\omega_{\mathrm{E}}$ 近似等于 $2 \pi / 24$ 弧度/小时。

## 北向切平面坐标系

当假设地球表面有数学表示（如椭球体或近似球体）时，可以定义一个切平面坐标系。选取与地表某一点 $O_{\mathrm{E}}$ 相切的平面作为参考。一个叫做“北向切平面坐标系”的地理坐标系 $\mathcal{F}_{\mathrm{E}}=\left\{O_{\mathrm{E}}, x_{\mathrm{E}}, y_{\mathrm{E}}, z_{\mathrm{E}}\right\}$ 具有固定原点 $O_{\mathrm{E}}$，其平面 $x_{\mathrm{E}} y_{\mathrm{E}}$ 与切平面重合。轴 $x_{\mathrm{E}}$ 指向地理北方，轴 $y_{\mathrm{E}}$ 指向东方，最后，轴 $z_{\mathrm{E}}$ 指向地面，平行于椭球体的法线（如果使用近似球体代替椭球体，则该轴指向地球中心）。因此，坐标系 $\mathcal{F}_{\mathrm{E}}$ 也被称为切平面 NED 坐标系（North-East-Down）。

![ECI 和 ECEF 坐标系](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/earth_frames.svg)

地心固定（ECEF）坐标系、地理坐标、切平面坐标系和局部垂直坐标系。

## 局部垂直局部水平坐标系，或局部 NED 坐标系

局部垂直坐标系$\mathcal{F}_{\mathrm{V}}=\left\{G, x_{\mathrm{V}}, y_{\mathrm{V}}, z_{\mathrm{V}}\right\}$ 与飞机在空间中的朝向无关，而仅由其重心相对于某个便捷的地球固定观察者的位置定义。如果 $G_{\mathrm{GT}}$ 是重心在地面上的投影（即“地面跟踪”），则坐标平面 $x_{\mathrm{V}} y_{\mathrm{V}}$ 平行于在 $G_{\mathrm{GT}}$ 处与地球表面局部切平面的平面——即平面 $x_{\mathrm{E}} y_{\mathrm{E}}$，其中 $O_{\mathrm{E}} \equiv G_{\mathrm{GT}}$。然后，轴 $x_{\mathrm{V}}$ 指向地理北方，轴 $y_{\mathrm{V}}$ 指向东，最后，轴 $z_V$ 指向地球中心的下方。因此，坐标系 $\mathcal{F}_{\mathrm{V}}$ 也被称为局部NED（载机）坐标系。

![局部垂直坐标系](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_local_vertical_axes.svg)

飞机体坐标系和局部垂直坐标系（NED坐标系）。图中还展示了飞机的欧拉角：航向角 $\psi$（图中为负），俯仰角 $\theta$，和滚转角 $\phi$。

NED惯例确保飞机的重量是一个力，在坐标系 $\mathcal{F}_{\mathrm{V}}$ 中的分量为 $(0,0, m g)$，其中 $m$ 是飞机的质量，$g$ 是重力加速度。

上述图展示了一个包含两个坐标系 $\mathcal{F}_{\mathrm{V}}$ 和 $\mathcal{F}_{\mathrm{B}}$ 的飞机。定义机体坐标系相对于局部NED坐标系的朝向的欧拉角是飞机的欧拉角。对于大气飞行器，定义欧拉角时使用的旋转序列是“3-2-1”。这定义了相对于固定在地球上的观察者的航向角 $\psi$、俯仰角 $\theta$ 和滚转角 $\phi$。

![欧拉陀螺仪](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_euler_gimbal.svg)


飞机的欧拉角旋转序列。坐标系$\mathcal{F}_{\mathrm{E}}=\left\{O_{\mathrm{E}}, x_{\mathrm{E}}, y_{\mathrm{E}}, z_{\mathrm{E}}\right\}$ 是一个地球固定的 NED 坐标系，原点 $O_{\mathrm{E}}$ 位于地面某处（或海平面），且平面 $x_{\mathrm{E}} y_{\mathrm{E}}$ 与地球表面相切。如果地面跟踪点 $G_{\mathrm{GT}}$ 离 $O_{\mathrm{E}}$ 不远，则地球坐标系 $\mathcal{F}_{\mathrm{E}}$ 的轴线与局部 NED 坐标系 $\mathcal{F}_{\mathrm{V}}=\left\{G, x_{\mathrm{V}}, y_{\mathrm{V}}, z_{\mathrm{V}}\right\}$的轴线平行。



## 风坐标系

除了升力，瞬时气动合力矢量 $\mathcal{F}_{\mathrm{A}}$ 在参考系中还有两个分量，其中 $z_{\mathrm{A}}$ 是第三轴。该参考系称为风参考系 $\mathcal{F}_{\mathrm{W}}=\left\{G, x_{\mathrm{W}}, y_{\mathrm{W}}, z_{\mathrm{W}}\right\}$。

风参考系的定义是将 $x_W$ 轴沿相对风的方向，并且其正方向与运动方向一致。这意味着 $x_{\mathrm{W}}$ 与向量 $\boldsymbol{V}$ 重合。风参考系的第三轴沿升力作用线定义，即 $z_{\mathrm{W}} \equiv z_{\mathrm{A}}$。最后，第二轴 $y_{\mathrm{W}}$ 被选择以完成右手坐标系。风参考系的第三轴始终处于机体对称面（也叫“参考面”）内。由于飞机的姿态随着相对风 $-\boldsymbol{V}$ 的变化而变化，所有三个风轴会相对于机体轴旋转。

力矢量 $\mathcal{F}_{\mathrm{A}}$ 沿着 $\boldsymbol{V}$ 方向的分量 $X_{\mathrm{W}}$ 定义了气动阻力：气动阻力 $D$ 满足 $X_{\mathrm{W}}=-D$。在存在非零侧滑角 $\beta$ 的情况下，气动合力 $\mathcal{F}_{\mathrm{A}}$ 会沿横向轴 $y_{\mathrm{W}}$ 产生第三个非零分量，即侧向力分量 $Y_{\mathrm{W}}$。

当侧滑角 $\beta$ 为零时，风参考系和气动参考系重合。仅在这种情况下，$y_{\mathrm{W}}$ 与 $y_{\mathrm{A}}$ 和 $y_{\mathrm{B}}$ 重合，且垂直于参考面 $x_{\mathrm{B}} z_{\mathrm{B}}$。

下图显示了飞机在平稳空气中的爬升飞行的标准参考系。当围绕 $z_{\mathrm{W}}$ 轴旋转角度 $-\beta$ 时，风参考系 $\mathcal{F}_{\mathrm{W}}$ 可以与气动参考系 $\mathcal{F}_{\mathrm{A}}$ 重合。

![三维定义](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/three_d_definitions.svg)

标准参考系和飞机在平稳空气中的爬升飞行。质心速度矢量 $\boldsymbol{V}$ 与水平面形成飞行路径角 $\gamma$。标准的三个气动合力分量 $D$、$L$ 和 $Y_{\mathrm{A}}$ 也已显示。

因此，风参考系 $\mathcal{F}_{\mathrm{W}}$ 可以通过先绕 $z_{\mathrm{W}}$ 轴旋转角度 $-\beta$，再绕 $y_{\mathrm{A}}$ 轴旋转角度 $\alpha_{\mathrm{B}}$，与机体参考系 $\mathcal{F}_{\mathrm{B}}$ 重合。
$$
\mathcal{F}_{\mathrm{W}} \xrightarrow{-\beta \curvearrowright z_{\mathrm{W}}} \mathcal{F}_{\mathrm{A}} \xrightarrow{\alpha_{\mathrm{B}} \curvearrowright y_{\mathrm{A}}} \mathcal{F}_{\mathrm{B}} \tag{1}
$$

气动结果力在机体轴上的分量则表示如下：

$$
\left\{\begin{array}{c}
X_{\mathrm{B}} \\
Y_{\mathrm{B}} \\
Z_{\mathrm{B}}
\end{array}\right\}=\left[\begin{array}{ccc}
\cos \alpha_{\mathrm{B}} & 0 & -\sin \alpha_{\mathrm{B}} \\
0 & 1 & 0 \\
\sin \alpha_{\mathrm{B}} & 0 & \cos \alpha_{\mathrm{B}}
\end{array}\right]\left[\begin{array}{ccc}
\cos \beta & \sin (-\beta) & 0 \\
-\sin (-\beta) & \cos \beta & 0 \\
0 & 0 & 1
\end{array}\right]\left\{\begin{array}{c}
-D \\
Y_{\mathrm{W}} \\
-L
\end{array}\right\} \tag{2}
$$

这些表示了气动阻力、侧向力和升力。

# 单位

JSBSim 在内部计算中几乎 exclusively 使用英制单位。然而，也可以在配置文件中输入一些参数时使用不同的单位。为了避免混淆，建议总是指定单位。单位使用 `unit` 属性进行指定。例如，翼展的规格如下所示：

```
<wingspan unit="FT"> 35.8 </wingspan>
```

上述声明指定了一个 35.8 英尺的翼展。以下声明将翼展指定为米单位，这将导致翼展在读取时转换为 35.8 英尺：

```
<wingspan unit="M"> 10.91 </wingspan>
```

这两条关于翼展的声明实际上是等效的。

JSBSim 目前支持以下单位：

**长度**

| `unit=` | 单位 |
| ------: | :--- |
|    `FT` | 英尺 |
|    `IN` | 英寸 |
|     `M` | 米   |
|    `KM` | 千米 |

**面积**

| `unit=` | 单位     |
| ------: | :------- |
|    `M2` | 平方米   |
|   `FT2` | 平方英尺 |

**体积**

| `unit=` | 单位     |
| ------: | :------- |
|   `FT3` | 立方英尺 |
|    `CC` | 立方厘米 |
|    `M3` | 立方米   |
|   `LTR` | 升       |

**质量和重量**

| `unit=` | 单位       |
| ------: | :--------- |
|   `LBS` | 磅（质量） |
|    `KG` | 千克       |

**惯性矩**

|    `unit=` | 单位     |
| ---------: | :------- |
| `SLUG*FT2` | 磅*英尺² |
|    `KG*M2` | 千克*米² |

**角度**

| `unit=` | 单位 |
| ------: | :--- |
|   `RAD` | 弧度 |
|   `DEG` | 度   |

**弹簧力**

|  `unit=` | 单位    |
| -------: | :------ |
|    `N/M` | 牛顿/米 |
| `LBS/FT` | 磅/英尺 |

**阻尼力**

|      `unit=` | 单位         |
| -----------: | :----------- |
|    `N/M/SEC` | 牛顿/(米·秒) |
| `LBS/FT/SEC` | 磅/(英尺·秒) |

**功率**

| `unit=` | 单位 |
| ------: | :--- |
| `WATTS` | 瓦特 |
|    `HP` | 马力 |

**力**

| `unit=` | 单位 |
| ------: | :--- |
|   `LBS` | 磅   |
|     `N` | 牛顿 |

**速度**

|  `unit=` | 单位    |
| -------: | :------ |
|    `KTS` | 节      |
| `FT/SEC` | 英尺/秒 |
|    `M/S` | 米/秒   |

**扭矩**

|  `unit=` | 单位    |
| -------: | :------ |
|    `N*M` | 牛顿·米 |
| `FT*LBS` | 磅·英尺 |

**压力**

| `unit=` | 单位        |
| ------: | :---------- |
|   `PSF` | 磅/平方英尺 |
|   `PSI` | 磅/平方英寸 |
|   `ATM` | 大气压      |
|    `PA` | 牛顿/平方米 |
|  `INHG` | 英寸汞柱    |

# 属性系统

仿真程序需要管理大量的状态信息。对于特别庞大的程序，数据管理任务可能会引发一些问题：

- 贡献者越来越难以掌握所需的多个接口，以进行任何有用的程序扩展，因此贡献进展变慢。
- 运行时的可配置性变得越来越困难，因为不同的模块使用不同的机制（环境变量、自定义规范文件、命令行选项等）。
- 模块初始化的顺序变得复杂且脆弱，因为一个模块的初始化例程可能需要从未初始化的模块设置或获取状态信息。
- 通过附加脚本、规范文件等进行扩展的能力仅限于程序提供的状态信息，且非编码开发人员往往要等待较长时间才能获得开发人员添加新变量的支持。

属性管理器系统提供了一个单一的接口，用于选择程序的状态信息，并允许在运行时动态创建新的用户指定的变量。后一种能力对于 JSBSim 控制系统模型尤其重要，因为组成飞机控制律的各个控制系统组件（PID 控制器、开关、加法器、增益等）仅在配置文件中存在。在运行时——在解析组件定义之后——这些组件将被实例化，属性管理器将创建一个属性来存储每个组件的输出值。

属性本身类似于具有选择性限制可见性（只读或读写）的全局变量，它们被分类到一个层次结构的树形结构中，类似于 Unix 文件系统的结构。属性树的结构包括根节点、子节点（类似子目录）和终端节点（属性）。类似于 Unix 文件系统，属性可以相对于当前节点或根节点进行引用。节点可以像符号链接文件或目录到其他文件或目录一样，附加到其他节点上。属性在整个 JSBSim 和 FlightGear 中用于引用程序代码中的特定参数。属性名称的形式如下：`position/h-sl-ft` 和 `aero/qbar-psf`。

为了说明使用属性和配置文件的强大功能，考虑一下高性能喷气式飞机模型的案例。假设在示例飞机的控制面板上添加了一个新的开关，允许飞行员在飞行控制系统（FCS）中覆盖俯仰限制。对于 FlightGear，仪表面板是在配置文件中定义的，开关也在那里定义以进行视觉显示。该开关定义还会分配一个属性名称。在 JSBSim 飞机规范文件中的飞行控制部分，分配给仪表面板定义中俯仰覆盖开关的相同属性名称可以用于根据开关位置引导控制律通过所需路径。无需修改任何代码。

特定的仿真参数可以通过属性在 JSBSim 和配置文件规范中进行访问和设置。如前所述，“属性”是我们用来描述可以从配置文件或命令行中访问或设置的参数的术语。

许多属性是标准属性——即所有飞行器始终存在的属性。气动系数、发动机、推进器以及飞行控制/自动驾驶模型也会具有动态定义的属性。这是因为，直到读取相关的飞行器配置文件后，整个气动系数、发动机等的集合才会被确定。要访问这些参数，必须知道使用的属性命名约定。例如，X-15 模型的飞行控制系统包括以下组件：

```xml
<flight_control name="X-15">
   <channel name="Pitch">
      <summer name="fcs/pitch-trim-sum">
         <input> fcs/elevator-cmd-norm </input>
         <input> fcs/pitch-trim-cmd-norm </input>
         <clipto>
            <min> -1 </min>
            <max>  1 </max>
         </clipto>
      </summer>
      <aerosurface_scale name="fcs/pitch-command-scale">
         <input> fcs/pitch-trim-sum </input>
         <range>
            <min> -50 </min>
            <max>  50 </max>
         </range>
      </aerosurface_scale>
      <pure_gain name="fcs/pitch-gain-1">
         <input> fcs/pitch-command-scale </input>
         <gain> -0.36 </gain>
      </pure_gain>
   </channel>
</flight_control>
```



在上面的例子中，第一个组件（`fcs/pitch-trim-sum`）接收来自两个地方的输入，即已知的静态属性 `fcs/elevator-cmd-norm` 和 `fcs/pitch-trim-cmd-norm`。接下来的组件将接收第一个组件的输出作为输入。第二个组件列出的输入属性为 `fcs/pitch-trim-sum`。继续上述示例，最后一个组件 `fcs/pitch-gain-1` 接收前一个组件的输出 `fcs/pitch-command-scale`，该属性名为 `fcs/pitch-command-scale`。

因此，现在我们已经可以访问 JSBSim 内部的许多参数，并且我们知道如何组装 JSBSim 中的飞行控制系统（FCS）。在 FCS 中使用的相同组件，也可以用来构建自动驾驶系统或其他系统。

# 数学

## 函数

JSBSim 中的函数规范是一个强大且多功能的资源，允许在 JSBSim 配置文件中定义代数函数。函数的语法在概念上类似于 MathML（数学标记语言，http://www.w3.org/Math/），但它更加简洁和紧凑。

一个函数定义由一个操作、一个值、一个表格或一个属性（评估为值）组成。当前支持的操作有：

- `sum`（接受 n 个参数）
- `difference`（接受 n 个参数）
- `product`（接受 n 个参数）
- `quotient`（接受 2 个参数）
- `pow`（接受 2 个参数）
- `exp`（接受 2 个参数）
- `abs`（接受 n 个参数）
- `sin`（接受 1 个参数）
- `cos`（接受 1 个参数）
- `tan`（接受 1 个参数）
- `asin`（接受 1 个参数）
- `acos`（接受 1 个参数）
- `atan`（接受 1 个参数）
- `atan2`（接受 2 个参数）
- `min`（接受 n 个参数）
- `max`（接受 n 个参数）
- `avg`（接受 n 个参数）
- `fraction`（接受 1 个参数）
- `mod`（接受 2 个参数）
- `lt`（小于，接受 2 个参数）
- `le`（小于等于，接受 2 个参数）
- `gt`（大于，接受 2 个参数）
- `ge`（大于等于，接受 2 个参数）
- `eq`（等于，接受 2 个参数）
- `nq`（不等于，接受 2 个参数）
- `and`（接受 n 个参数）
- `or`（接受 n 个参数）
- `not`（接受 1 个参数）
- `if-then`（接受 2-3 个参数）
- `switch`（接受 2 个或更多参数）
- `random`（高斯随机数，无参数）
- `integer`（接受 1 个参数）

一个操作在配置文件中的定义示例如下：

```xml
<sum>
   <value> 3.14159 </value>
   <property> velocities/qbar </property>
   <product>
      <value> 0.125 </value>
      <property> metrics/wingarea </property>
   </product>
</sum>
```

在上述例子中，`sum` 元素包含了其他三个项。它的计算过程可以用代数表达式表示为：
$$
3.14159+\text { qbar }+(0.125 \cdot \text { wingarea })
$$
一个完整的函数定义（例如在气动部分的配置文件中使用的）包括 `function` 元素和其他元素。需要注意的是，函数定义中只能有一个非可选（非文档）元素——即一个操作元素。该元素不能包含多个直接子操作、`property`、`table` 或 `value` 元素。几乎总是，函数元素中的第一个操作将是乘积（product）或和（sum）。例如：

```xml
<function name="aero/moment/roll_moment_due_to_yaw_rate">
   <description> Roll moment due to yaw rate </description>
   <product>
      <property> aero/qbar-area </property>
      <property> metrics/bw-ft </property>
      <property> velocities/r-aero-rad_sec </property>
      <property> aero/bi2vel </property>
      <table>
         <independentVar> aero/alpha-rad </independentVar>
         <tableData>
            0.000 0.08
            0.094 0.19
            ...   ...
         </tableData>
      </table>
   </product>
</function>
```

在函数定义中，最“底层”的元素总是一个值或一个属性，它本身不能包含其他元素。如所示，操作可以包含值、属性、表格或其他操作。

在 JSBSim 中，某些操作仅接受一个参数。然而，这个参数可以是一个操作（例如 `sum`），而该操作可以包含其他项。需要记住的一点是，任何此类包含的操作都将计算出一个单一的值 —— 这正是三角函数所要求的（除了 `atan2`，它接受两个参数）。

最后，在函数定义中，有一些简写别名可以用来代替标准的元素标签，从而使得表达式更加简洁。属性、值和表格通常用 `<property>`、`<value>` 和 `<table>` 标签来引用。但是，在函数定义中，以上这些元素可以使用 `<p>`、`<v>` 和 `<t>` 标签来代替。因此，之前的示例可以简化为以下格式：

```xml
<function name="aero/moment/roll_moment_due_to_yaw_rate">
   <description>Roll moment due to yaw rate</description>
   <product>
      <p> aero/qbar-area </p>
      <p> metrics/bw-ft </p>
      <p> aero/bi2vel </p>
      <p> velocities/r-aero-rad_sec </p>
      <t>
         <independentVar> aero/alpha-rad </independentVar>
         <tableData>
            0.000 0.08
            0.094 0.19
            ...   ...
         </tableData>
      </t>
   </product>
</function>
```



在气动建模中，表格函数可以用来表示影响升力和阻力的地面效应因子。下图解释了地面效应：

![Ground effect](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_ground_effect.svg)

为了了解如何在 JSBSim 中建模地面效应，我们可以查看 Cessna 172 Skyhawk 模型。这一模型在文件 `<JSBSim-root-dir>/aircraft/c172p/c172p.xml` 中实现。在该 XML 文件的 `<aerodynamics/>` 块中，建模了两个无量纲因子, $K_{C_{D, \mathrm{ge}}}$ 和 $K_{C_L, \mathrm{ge}}$,它们是无量纲地面高度的函数，并被视为升力和阻力的乘数。这些因子如下所示：

```xml
<function name="aero/function/kCDge">
   <description>Change in drag due to ground effect</description>
   <product>
      <value>1.0</value>
      <table>
         <independentVar> aero/h_b-mac-ft </independentVar>
         <tableData>
            0.0000 0.4800
            0.1000 0.5150
            0.1500 0.6290
            0.2000 0.7090
            0.3000 0.8150
            0.4000 0.8820
            0.5000 0.9280
            0.6000 0.9620
            0.7000 0.9880
            0.8000 1.0000
            0.9000 1.0000
            1.0000 1.0000
            1.1000 1.0000
         </tableData>
      </table>
   </product>
</function>

<function name="aero/function/kCLge">
   <description>Change in lift due to ground effect</description>
   <product>
      <value>1.0</value>
      <table>
         <independentVar> aero/h_b-mac-ft </independentVar>
         <tableData>
            0.0000 1.2030
            0.1000 1.1270
            0.1500 1.0900
            0.2000 1.0730
            0.3000 1.0460
            0.4000 1.0550
            0.5000 1.0190
            0.6000 1.0130
            0.7000 1.0080
            0.8000 1.0060
            0.9000 1.0030
            1.0000 1.0020
            1.1000 1.0000
         </tableData>
      </table>
   </product>
</function>
```

下图展示了表示因子 $K_{C_{D, \text{ ge}}}$ 和 $K_{C_{L, \text{ ge}}}$ 的表格函数 `aero/function/kCDge` 和 `aero/function/kCLge` ，分别表示因地面效应引起的阻力和升力的变化。它们的图形化表示如下，显示了相对于无量纲地面高度 $h/(b/2)$ 的变化。在飞机离地面高度小于机翼半个翼展 $b/2$ 时，可以观察到地面效应；而在更高的高度时，这两个因子值趋于 1。

![Ground effect CL CD](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_ground_effect_CL_CD.png)

以上图表展示了无量纲地面高度 $h/(b/2)$ 的函数，定义了 `c172p` 飞机气动模型中的 `aero/function/kCLge` 和 `aero/function/kCDge` 属性。

### 表格

在 JSBSim 中，可以定义一维、二维或三维查找表，用于气动学和函数定义。对于一个单一的“向量”查找表，格式如下：

```xml
<table name="property_name_0">
   <independentVar lookup="row"> property_name_1 </independentVar>
   <tableData>
      key_1  value_1
      key_2  value_2
      ...    ...
      key_n  value_n
   </tableData>
</table>
```

在这个例子中，`<independentVar/>` 元素的 `lookup="row"` 属性是可选的；默认假设 `independentVar` 是行变量。一个实际的示例如下：

```xml
<table>
   <independentVar lookup="row"> aero/alpha-rad </independentVar>
   <tableData>
      -1.57  1.500
      -0.26  0.033
       0.00  0.025
       0.26  0.033
       1.57  1.500
   </tableData>
</table>
```

数据表格中的第一列代表查找索引（或 *断点*，或键）。在这个例子中，查找索引是 `aero/alpha-rad`（迎角，以弧度为单位）。如果 `aero/alpha-rad` 的值为 0.26 弧度，则查找表返回的值为 0.033。

二维表的定义如下：

```xml
<table name="property_name_0">
   <independentVar lookup="row">    property_name_1 </independentVar>
   <independentVar lookup="column"> property_name_2 </independentVar>
   <tableData>
                  {col_1_key   col_2_key   ...  col_n_key }
      {row_1_key} {col_1_data  col_2_data  ...  col_n_data}
      {row_2_key} {...         ...         ...  ...       }
      {   ...   } {...         ...         ...  ...       }
      {row_n_key} {...         ...         ...  ...       }
   </tableData>
</table>
```

数据是以网格格式呈现的。以下是一个实际示例，其中 `aero/alpha-rad` 是行查找（迎角的断点排列在第一列），`fcs/flap-pos-deg` 是列查找（襟翼位置的角度，分别为 0、10、20 和 30 度）：

```xml
<table>
   <independentVar lookup="row">    aero/alpha-rad   </independentVar>
   <independentVar lookup="column"> fcs/flap-pos-deg </independentVar>
   <tableData>
                 0.0          10.0        20.0       30.0
     -0.0523599  8.96747e-05  0.00231942  0.0059252  0.00835082
     -0.0349066  0.000313268  0.00567451  0.0108461  0.0140545
     -0.0174533  0.00201318   0.0105059   0.0172432  0.0212346
      0.0        0.0051894    0.0168137   0.0251167  0.0298909
      0.0174533  0.00993967   0.0247521   0.0346492  0.0402205
      0.0349066  0.0162201    0.0342207   0.0457119  0.0520802
      0.0523599  0.0240308    0.0452195   0.0583047  0.0654701
      0.0698132  0.0333717    0.0577485   0.0724278  0.0803902
      0.0872664  0.0442427    0.0718077   0.088081   0.0968405
  </tableData>
</table>
```



三维查找表的定义如下：

```xml
<table name="property_name_0">
   <independentVar lookup="row">    property_name_1 </independentVar>
   <independentVar lookup="column"> property_name_2 </independentVar>
   <independentVar lookup="table">  property_name_3 </independentVar>
   <tableData  breakpoint="table_1_key">
                  {col_1_key   col_2_key   ...  col_n_key }
      {row_1_key} {col_1_data  col_2_data  ...  col_n_data}
      {row_2_key} {...         ...         ...  ...       }
      {   ...   } {...         ...         ...  ...       }
      {row_n_key} {...         ...         ...  ...       }
   </tableData>
   <tableData  breakpoint="table_2_key">
                  {col_1_key   col_2_key   ...  col_n_key }
      {row_1_key} {col_1_data  col_2_data  ...  col_n_data}
      {row_2_key} {...         ...         ...  ...       }
      {   ...   } {...         ...         ...  ...       }
      {row_n_key} {...         ...         ...  ...       }
   </tableData>
   ...
   <tableData  breakpoint="table_n_key">
                  {col_1_key   col_2_key   ...  col_n_key }
      {row_1_key} {col_1_data  col_2_data  ...  col_n_data}
      {row_2_key} {...         ...         ...  ...       }
      {   ...   } {...         ...         ...  ...       }
      {row_n_key} {...         ...         ...  ...       }
   </tableData>   
</table>
```

请注意 `<tableData/>` 元素中的 `breakpoint` 属性。以下是一个示例：

```xml
<table>
   <independentVar lookup="row">    fcs/row-value    </independentVar>
   <independentVar lookup="column"> fcs/column-value </independentVar>
   <independentVar lookup="table">  fcs/table-value  </independentVar>
   <tableData breakPoint="-1.0">
           -1.0    1.0
      0.0  1.0000  2.0000
      1.0  3.0000  4.0000
   </tableData>
   <tableData breakPoint="0.0000">
           0.0     10.0
      2.0  1.0000  2.0000
      3.0  3.0000  4.0000
   </tableData>
   <tableData breakPoint="1.0">
            0.0     10.0    20.0
       2.0  1.0000  2.0000  3.0000
       3.0  4.0000  5.0000  6.0000
      10.0  7.0000  8.0000  9.0000
   </tableData>
</table>
```

- **插值**：表格中的值是线性插值的，且不会在表格的限制值之外进行外推。表格返回的最大值是已定义的最大值。

#### 一维插值

一些仿真中的查找表——尤其是气动数据——可能是四维、五维、六维，甚至更多维度的。`Interpolate1d` 返回通过对提供的值进行一维插值的结果，其中第一个直接子元素的值表示查找表中的查找值，后续的值对表示自变量和因变量。第一个提供的子元素预期是一个属性。插值不会进行外推，如果提供的查找值超出了定义的范围，则返回最高值。其格式如下：

```xml
<interpolate1d>
  {property, value, table, function}
  {property, value, table, function} {property, value, table, function}
  ...
</interpolate1d>
```

示例：如果 `mach` 为 0.4，插值将返回 0.375。如果 `mach` 为 1.5，插值将返回 0.60。

```xml
<interpolate1d>
  <p> velocities/mach </p>
  <v> 0.00 </v>  <v> 0.25 </v>
  <v> 0.80 </v>  <v> 0.50 </v>
  <v> 0.90 </v>  <v> 0.60 </v>
</interpolate1d>
```

上面的示例非常简单。一个更复杂的示例可能会在任何参数（除了第一个）中使用函数。这意味着断点向量可能是变量——尽管这并不常见——但更重要的是，查找向量（第二列）中的值可能是 1、2 或 3 维度的函数表元素。参数甚至可以是嵌套的 `interpolate1d` 元素。例如：

```xml
<function name="whatever">
  <interpolate1d>
    <p> velocities/mach </p>
    <v> 0.00 </v>  <table> ... table definition ... </table>
    <v> 0.80 </v>  <table> ... table definition ... </table>
    <v> 0.90 </v>  <table> ... table definition ... </table>
  </interpolate1d>
</function>
```

进一步扩展：

```xml
<function name="bigWhatever1">
  <interpolate1d>
    <p> aero/qbar-psf </p>
    <v> 0 </v>  <interpolate1d>
                  <p> velocities/mach </p>
                  <v> 0.00 </v>  <table> ... table 1 definition ... </table>
                  <v> 0.80 </v>  <table> ... table 2 definition ... </table>
                  <v> 0.90 </v>  <table> ... table 3 definition ... </table>
                </interpolate1d>
    <v> 65 </v> <interpolate1d>
                  <p> velocities/mach </p>
                  <v> 0.00 </v>  <table> ... table 1 definition ... </table>
                  <v> 0.80 </v>  <table> ... table 2 definition ... </table>
                  <v> 0.90 </v>  <table> ... table 3 definition ... </table>
                </interpolate1d>
    <v> 90 </v> <interpolate1d>
                  <p> velocities/mach </p>
                  <v> 0.00 </v>  <table> ... table 1 definition ... </table>
                  <v> 0.80 </v>  <table> ... table 2 definition ... </table>
                  <v> 0.90 </v>  <table> ... table 3 definition ... </table>
                </interpolate1d>
  </interpolate1d>
</function>
```

上面的结构实际上提供了一个五维查找表。在实践中，这会非常庞大且混乱，但这就是实现方式。 :-)

不过，这里还有更多。对于非常非常大的气动数据库，有时某些气动系数可能不需要计算。例如，地面效应气动系数只在接近地面时才需要计算。当地面效应不对气动力和力矩产生影响时，为什么还要浪费 CPU 循环呢？我们可以使用 `ifthen` 元素来跳过昂贵的计算。`ifthen` 元素的工作方式如下：

如果第一个直接子元素的值为 1，则返回第二个直接子元素的值，否则返回第三个子元素的值。

```xml
<ifthen>
  {property, value, table, or other function element}
  {property, value, table, or other function element}
  {property, value, table, or other function element}
</ifthen>
```

示例：如果 `flight-mode` 大于 2，则返回 0.00，否则返回属性 `control/pitch-lag` 的值。

```xml
<ifthen>
  <gt> <p> executive/flight-mode </p> <v> 2 </v> </gt>
  <v> 0.00 </v>
  <p> control/pitch-lag </p>
</ifthen>
```

在我们的例子中，可以如下编写五维查找表查询，除非起落架已放下，否则返回零：

```xml
<function name="propertyname">
  <ifthen>
    <lt> <p> position/altitudeMSL </p> <v> 90 </v> </lt>
    <interpolate1d>
      <p> aero/qbar-psf </p>
      <v> 0 </v>  <interpolate1d>
                    <p> velocities/mach </p>
                    <v> 0.00 </v>  <table> ... table 1 definition ... </table>
                    <v> 0.80 </v>  <table> ... table 2 definition ... </table>
                    <v> 0.90 </v>  <table> ... table 3 definition ... </table>
                  </interpolate1d>
      <v> 65 </v> <interpolate1d>
                    <p> velocities/mach </p>
                    <v> 0.00 </v>  <table> ... table 1 definition ... </table>
                    <v> 0.80 </v>  <table> ... table 2 definition ... </table>
                    <v> 0.90 </v>  <table> ... table 3 definition ... </table>
                  </interpolate1d>
      <v> 90 </v> <interpolate1d>
                    <p> velocities/mach </p>
                    <v> 0.00 </v>  <table> ... table 1 definition ... </table>
                    <v> 0.80 </v>  <table> ... table 2 definition ... </table>
                    <v> 0.90 </v>  <table> ... table 3 definition ... </table>
                  </interpolate1d>
    </interpolate1d>
    <v> 0 </v>
  </ifthen>
</function>
```

上面的例子在某种程度上是没有实际意义的，但格式是正确的。从性能角度来看，这是有效的，因为表格只有在查找时确实需要时才会被执行。

# 力与力矩

## 空气动力学

有几种方法可以模拟作用在飞机上的空气动力学力和力矩（扭矩）。JSBSim 最初采用的是系数积累法。在系数积累法中，升力（例如）是通过将所有升力贡献相加来确定的。贡献的具体内容根据飞机的不同和模型的精度而有所不同，但升力的贡献可以包括来自以下方面的贡献：

- 机翼
- 升降舵
- 襟翼

空气动力学系数是一些数字，这些数字在乘以某些其他值（如动态压力和机翼面积）后，结果就是一个力或力矩。这些系数可以来自飞行试验报告或教科书，或者可以通过软件（如 Digital DATCOM 或其他商业软件）或手工计算来得出。最终，JSBSim 增加了对作为函数指定的空气动力学属性的支持。在配置文件的 `<aerodynamics>` 部分中，有六个子部分，分别代表 3 个力轴和 3 个力矩轴（总共六个自由度）。空气动力学部分的基本布局如下：

```
<aerodynamics>
   <axis name="DRAG">
      { 力的贡献 }
   </axis>
   <axis name="SIDE">
      { 力的贡献 }
   </axis>
   <axis name="LIFT">
      { 力的贡献 }
   </axis>
   <axis name="ROLL">
      { 力矩的贡献 }
   </axis>
   <axis name="PITCH">
      { 力矩的贡献 }
   </axis>
   <axis name="YAW">
      { 力矩的贡献 }
   </axis>
</aerodynamics>
```

并非所有单独的轴都是必需的。JSBSim 支持几组标准的轴系统：

- `"DRAG"`、`"SIDE"`、`"LIFT"`（风轴）
- `"X"`、`"Y"`、`"Z"`（机体轴）
- `"AXIAL"`、`"SIDE"`、`"NORMAL"`（机体轴）

所有三个系统都接受 `"ROLL"`、`"PITCH"`、`"YAW"` 轴的定义。轴系统不能混合使用。在轴元素中，函数用于定义对该轴总力或力矩的各个贡献。在 JSBSim 中，函数是被广泛使用的。在定义力或力矩时，函数可以使用表格、常数、三角函数或其他标准 C 库函数。仿真参数通过属性进行引用。以下是一个例子：

```
<function name="aero/force/lift_due_to_flap_deflection">
   <description>襟翼偏转引起的升力贡献</description>
   <product>
      <property>aero/function/ground-effect-factor-lift</property>
      <property>aero/qbar-area</property>
      <table>
         <independentVar>fcs/flap-pos-deg</independentVar>
         <tableData>
             0.0  0.0
            10.0  0.20
            20.0  0.30
            30.0  0.35
         </tableData>
      </table>
   </product>
</function>
```

在此例中，以上内容的文字描述如下：该函数的值是 `ground-effect-factor-lift`、`qbar-area` 和通过表格确定的值的乘积，表格根据襟翼位置（以度为单位）进行索引。

在 `<axis/>` 部分中的所有函数都会相加，并以适当的方式应用于飞机。然而，这种格式具有一定的灵活性。那些在任何 `<axis/>` 部分之外指定的函数会被创建和计算，但它们本身并不会直接贡献到任何力或力矩的总值中。然而，它们可以被引用到位于 `<axis/>` 部分内的其他函数中。这种技术允许将可能应用于多个单独函数的计算一次性执行，并多次使用。这个技术还可以进一步扩展，实际上，空气动力学系数可以在 `<axis/>` 定义外计算出来，然后在函数定义内通过乘以各种因子（属性）将它们转换为力和力矩，并最终在 `<axis/>` 定义中应用。

#### 力与力矩的例子：瞬时升力

作为一个例子，我们来分析瞬时升力 $L(t)$。它可以通过以下积累公式表示：

$$
L=L_{\text {basic }}\left(\alpha_{\mathrm{B}}, \phi_{\text {hyst }}\right)+\Delta L\left(\delta_{\text {flap }}\right)+\Delta L\left(\delta_{\mathrm{e}}\right)+\Delta L\left(\dot{\alpha}_{\mathrm{B}}\right)+\Delta L(q) \tag{1}
$$

其中，$\alpha_B$、$\delta_{\text{flap}}$、$\delta_e$、$\dot{\alpha}_B$ 和 $q$ 是常见的飞机状态变量。无量纲标量 $\phi_{\text{hyst}}$ 通常等于 0，当攻角较大时（接近失速情况，当空气动力学滞后效应被建模时），它的值为 1。

公式 (1) 中的项 $L_{\text{basic}}\left(\alpha_B, \phi_{\text{hyst}}\right)$ 被称为“基本”贡献，它依赖于攻角。我们知道，增加攻角会增加升力——直到某个点为止。升力通常被定义为飞行动态压力（“qbar”，$\bar{q}$，或者对于空气动力学家而言是 $\bar{q}_{\infty}$）与机翼面积（$S_W$ 或简写为 $S$）和升力系数（$C_L$）的乘积。在本例中，升力系数通过查找表来确定，使用 $\alpha_B$ 和 $\phi_{\text{hyst}}$ 作为查找表的索引：

```
<function name="aero/force/lift_from_alpha">
   <description> 升力由于攻角 </description>
   <product>
      <property> aero/qbar-psf </property>
      <property> metrics/Sw-sqft </property>
      <property> aero/function/kCLge </property>
      <table>
         <independentVar lookup="row"> aero/alpha-rad </independentVar>
         <independentVar lookup="column"> aero/stall-hyst-norm </independentVar>
         <tableData>
                      0.0000   1.0000
            -0.0900  -0.2200  -0.2200
             0.0000   0.2500   0.2500
             0.0900   0.7300   0.7300
             0.1000   0.8300   0.7800
             0.1200   0.9200   0.7900
             0.1400   1.0200   0.8100
             0.1600   1.0800   0.8200
             0.1700   1.1300   0.8300
             0.1900   1.1900   0.8500
             0.2100   1.2500   0.8600
             0.2400   1.3500   0.8800
             0.2600   1.4400   0.9000
             0.2800   1.4700   0.9200
             0.3000   1.4300   0.9500
             0.3200   1.3800   0.9900
             0.3400   1.3000   1.0500
             0.3600   1.1500   1.1500
         </tableData>
      </table>
   </product>
</function>
```

基本升力系数

$$
C_{L, \text{ basic }}=\frac{L_{\text{ basic }}\left(\alpha_B, \phi_{\text{hyst }}\right)}{\bar{q} S} \tag{2}
$$

下面是根据攻角 $\alpha_B$ 和 $\phi_{\text{hyst}}$ 绘制的基本升力系数 $C_{L,\text{basic}}$ 的曲线。

![CL Basic](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_CL_basic.png)

上图显示了与 c172p 空气动力学模型中名为 `aero/coefficient/CLwbh` 的查找表相对应的二元函数 $C_{L,\text{basic}}(\alpha_B, \phi_{\text{hyst}})$。

---

### TODO

完成本小节内容。

------

![CL Basic](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_CD_basic.png)

该图表示了与 c172p 空气动力学模型中名为 `aero/coefficient/CDwbh` 的查找表相对应的二元函数 $C_{D,\text{basic}}(\alpha_B, \delta_{\text{flap}})$。

------

### TODO

完成本小节内容。

---

## 推力 (Propulsion)

<chatgpt>

推力部分通常涉及发动机的性能以及与之相关的空气动力学效应。在 JSBSim 中，推力系统通常通过对发动机的建模来实现，这包括计算气流、转速、油门设置和其他影响推力的因素。推力的计算需要依赖于多种因素，比如油门位置、发动机转速、飞行状态等。

```xml
<propulsion>
   <engine name="engine1">
      <thrustModel>generic</thrustModel>
      <rpm>2000</rpm>
      <power>180</power>
      <fuelFlow>50</fuelFlow>
      <thrust>
         <function>thrust_from_rpm</function>
      </thrust>
   </engine>
</propulsion>
```

在此示例中，我们定义了一个名为 `engine1` 的发动机，使用了 `generic` 的推力模型，并为其指定了转速、功率和油耗等参数。推力是通过某个特定的函数来计算的，比如根据发动机转速来推算。

------

## 重量 (Weight)

<chatgpt>

飞机的重量是影响飞行的关键因素，它包括飞机的自重、载荷以及油料的质量等。重量的变化直接影响到升力、飞行速度以及燃料消耗等参数。在 JSBSim 中，重量可以通过以下方式进行建模：

```xml
<weight>
   <emptyWeight>1500</emptyWeight>
   <maxTakeoffWeight>2500</maxTakeoffWeight>
   <fuelWeight>
      <property>fuel/weight</property>
   </fuelWeight>
</weight>
```

在此示例中，定义了飞机的空重、最大起飞重量以及燃料重量。燃料的重量由 `fuel/weight` 属性控制，表示当前燃料的质量。

------

## 地面接触 (Ground Contact)

<chatgpt>

地面接触模型用于模拟飞机与地面之间的交互力和接触点。这些交互力包括着陆、起飞时的冲击力，以及滑行时的摩擦力。在 JSBSim 中，地面接触模型通常包括以下几个部分：

1. **摩擦力**：模拟飞机与地面之间的摩擦。
2. **起落架**：描述起落架的刚度、阻尼和承载能力。
3. **接触力**：模拟飞机与地面接触时产生的垂直力和水平力。

```xml
<groundContact>
   <contact>
      <surfaceType>asphalt</surfaceType>
      <frictionCoefficient>0.8</frictionCoefficient>
      <landingGear>
         <model>basic</model>
         <stiffness>2000</stiffness>
         <damping>100</damping>
      </landingGear>
   </contact>
</groundContact>
```

在此示例中，定义了地面接触的表面类型（如沥青）、摩擦系数，以及起落架的刚度和阻尼特性。

------



# 飞行控制与系统建模

将飞机视为一般的动力学系统，它受控制输入向量 $\boldsymbol{u}$ 的作用。输入的数量和类型可能取决于具体考虑的飞机类型。对于常规配置的飞机，输入的最小配置通常为：
$$
\boldsymbol{u}=\left[\delta_{\mathrm{T}}, \delta_{\mathrm{a}}, \delta_{\mathrm{e}}, \delta_{\mathrm{r}}\right] \tag{1}
$$
其中，$\delta_{\mathrm{T}}$ 是油门设定，$\delta_{\mathrm{a}}$、$\delta_{\mathrm{e}}$ 和 $\delta_{\mathrm{r}}$ 分别是右副翼、升降舵和方向舵的角度偏转。这些量有标准符号，且它们的范围可能会根据具体的飞机设计有所不同。在飞行仿真中，它们的变化通常与驾驶舱中相应控制的归一化设置相关。

通常油门的设定范围从 0（空闲）到 +1（最大功率）。从概念上讲，$\delta_{\mathrm{T}}$ 可视为实际飞行速度和高度下最大推力输出的当前分数。

操纵杆的偏转范围通常从 −1 到 +1。

这些映射通常取决于控制律的存在，这些控制律可能会改变飞行员操作对实际效应器偏转和推力输出的最终影响。

从数学的角度看，无论是考虑实际的气动表面偏转和推力输出，还是归一化的命令范围，它们都被视为控制变量 $\boldsymbol{u}$ 的一组边界。

必须再次强调，控制输入的数量和类型是特定飞机的特征。即使在相同的广泛类别中，两种飞机设计也可能呈现出本质上不同的控制配置和数量。但一般来说，它们至少有相同的“主要”控制：一对副翼，一个主要的纵向控制，即一对对称运动的升降舵，以及一个方向舵。在许多情况下，水平尾翼也具有相对于机身参考线的可变安装角度，这个角度通常称为 $i_{\mathrm{H}}$，大多数飞行力学教材中都有涉及。

## 约定

![气动表面偏转](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_aerosurface_deflections.svg)

标准飞机气动控制表面。

## 气动建模概述

线性化的迎角系数：
$$
C_m=C_{m 0}+C_{m \alpha} \alpha_{\mathrm{B}}+C_{m \delta_{\mathrm{e}}} \delta_{\mathrm{e}}+C_{m i_{\mathrm{H}}} i_{\mathrm{H}}+\left(C_{m q} q+C_{m \dot{\alpha}} \dot{\alpha}_{\mathrm{B}}\right) \frac{\bar{c}}{2 V} \tag{2}
$$
![Alpha 和 Beta](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_fcs.svg)

在 c172p 模型中，升降舵通道的命令与偏转逻辑。操纵杆的移动与迎角调整杆的调节组合，归一化并映射到区间 [−1,1]。该通道的输出是一个实数变量 fcs/elevator-pos-rad，表示一个等效的升降舵偏转 $\delta_{\mathrm{e}}^\star = \delta_{\mathrm{e}} + \delta_{\mathrm{e}, \mathrm{tab}}^\star$。其中，$\delta_{\mathrm{e}, \mathrm{tab}}^\star$ 是等效于实际升降舵调整角度 $\delta_{\mathrm{e}, \mathrm{tab}}$ 的偏转角度。$\delta_{\mathrm{e}}$ 的范围是 [$\delta_{\mathrm{e}, \mathrm{min}}$, $\delta_{\mathrm{e}, \mathrm{max}}$]。尾部以移动的表面偏转来表示，既有等效条件下（上方）也有实际条件下（下方）的偏转。

## 推力建模概述

![推力推进器](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/ac_thrust_definitions.svg)

一架双引擎螺旋桨飞机。在机体坐标系中，推进器、推力应用点和推力矢量的方向位置。

![推进器位置](https://jsbsim-team.github.io/jsbsim-reference-manual/assets/img/c172_thruster.svg)

与 c172p 的 FDM 中的实体 "推进器" 和 "油箱" 相关的位置。
+++
date = '2025-08-24T18:07:50+08:00'
draft = false
weight = 3
title = 'AFSIM 的伪实时混合仿真软件设计'
summary= "本文介绍了AFSIM执行仿真所采用的软件设计和算法。通过支持不同仿真执行的多种运行模式，AFSIM 使用户能够快速在虚拟和构造性分析之间切换。"
+++


## AFSIM's pseudo-realtime hybrid simulation software design

J Scott Thompson  and Douglas D Hodson

Approved for public release, unlimited distribution. Case \# AFRL-2020-0485, 9 December 2020.

#### 摘要

仿真方法通常分为两类：离散时间或离散事件。对于军事建模与仿真的需求，这两类方法通常分别对应于虚拟仿真（virtual simulation，意味着有人机交互）和构造性仿真（constructive simulation，意味着无人工交互）。美国空军研究实验室开发并发布了 AFSIM（Advanced Framework for Simulation, Integration, and Modeling，高级仿真集成建模框架），面向同时使用虚拟仿真和构造性仿真的用户群体。本文记录了AFSIM支持这两种模式的软件设计和主要算法，这一能力被称为混合仿真。


#### Keywords

仿真，离散事件，帧步进，混合，实时，虚拟，构造

Simulation, discrete event, frame step, hybrid, realtime, virtual, constructive




## I. 引言

仿真软件框架和应用程序已经在各种概念设计下被开发出来，用以解决大量不同的问题。概念设计类型可以按方法或世界观来分类，关注于某个仿真包中的“原子”元素，并从此展开。一般来说，有两种方法：一种聚焦于离散的时间单位，另一种聚焦于离散的事件。许多基于离散时间的仿真也常被称为“基于帧”的仿真，其中一帧是一个固定的时间增量。少数软件包既支持离散事件模式，也支持基于帧的模式，可以视为混合仿真。

随着美国国防采办与保障体系的改革呼声日益强调数字化工程，建模与仿真仍将是开发高效军事系统的重要工具。在数字化工程框架下，系统开发者需要执行多种仿真分析来开发有效的系统。基于帧的仿真应用广泛，从操作员训练、用户界面设计实验，到由建模与仿真（M&S）驱动的兵棋推演。一个普遍的主题是，基于帧的仿真通常以交互式虚拟模式运行，操作员在回路（OITL）或指挥员在回路（CITL），或以其他方式观察仿真，从而能够跟随仿真状态随时间展开的过程，并可能提供输入以影响仿真状态。相比之下，在无需交互的构造性仿真中，用户希望所有计算尽可能快地完成。这两种运行模式可以称为同步和异步。（需要注意的是，仿真并不一定要基于帧才能以同步模式运行。）交互式仿真的典型要求是时间看起来以恒定速率推进，通常与挂钟同步的“实时”，或以比实时更快或更慢的速率运行。对于许多应用来说，“硬”实时同步可能有用但不是必须的。与硬实时要求不同，许多仿真只是尝试给出合理的实时运行表象，本文将其称为伪实时（pseudo-realtime），其定义将在后文更明确地给出。

随着过去几十年软件工程的发展，建模与仿真（M&S）软件的工程化水平也在不断提升。目前已有许多设计良好、可扩展且灵活的仿真软件包，可用于各种仿真目的。美国空军研究实验室航空系统部长期在多个应用中使用一个建模、仿真与分析（MS&A）软件包——高级仿真集成建模框架（AFSIM）。本文介绍了AFSIM执行仿真所采用的软件设计和算法。通过支持不同仿真执行的多种运行模式，AFSIM 使用户免于在多个仿真环境中维护冗余的场景和概念定义，从而能够快速在虚拟和构造性分析之间切换。

本文结构如下：背景部分提供了来自其他文献的关键概念定义，并描述了适用于国防部应用和公开可用的软件包，这些软件包分别实现了上述主要模式中的一种，或在某些情况下同时实现两种。接下来的部分给出了AFSIM的高层描述，以及该框架在各种模式下提供的一些应用。软件设计部分首先详细介绍了AFSIM应用的核心软件类，然后是这些类实现的算法。最后，本文对所述概念进行总结，并提出未来探索的方向。


## 2. 背景

许多学者和软件开发者此前已对本文的核心概念进行了阐述。Law 的教材中收集了若干定义。${ }^{1}$ 他将离散事件仿真定义为“关注于系统随时间演化的建模，其表示方式是状态变量在分离的时间点瞬时改变。”Law 还提出了两种时间推进方法——下一事件推进（next-event time advance）和固定增量推进（fixed-increment time advance），并给出了离散事件仿真模型的通用设计，以及许多用C语言编写的示例。一些作者，包括 Naturo 和 Gamez，提出了混合仿真设计，以在同一仿真模型中同时处理离散事件和离散时间增量方法。${ }^{2,3}$ Gamez 将混合仿真定义为“在仿真中维持一个连续的仿真时钟，且模型的更新由事件驱动”，而 Naturo 更简单地表述为“混合系统由相互作用的连续子系统和离散事件子系统组成。”Cellier 和 Kofman 阐述了连续方程系统实时同步仿真的许多关键概念，并提供了一个通用示例，用于本文后续的插图说明。${ }^{4}$

下面列出了一些来自上述及其他来源的术语，这些术语定义和约束了不同的仿真模式：

- 虚拟仿真（Virtual simulation）：“真实的人操作模拟系统，或模拟的人操作真实系统。”${ }^{5}$
- 构造性仿真（Constructive simulation）：“模拟的人操作模拟系统。”${ }^{5}$
- 离散事件仿真（Discrete event simulation）：“关注于系统随时间演化的建模，其表示方式是状态变量在分离的时间点瞬时改变。”常用于需要比实时更快执行仿真的场景。${ }^{1}$
- 离散时间仿真（Discrete time simulation）：对连续过程的仿真，其中“仿真中的时间轴必须被离散化，使得仿真时间范围内的离散时间点总数保持有限，仿真必须通过从一个离散时间点跳到下一个离散时间点来进行。”${ }^{4}$
- 混合仿真（Hybrid simulation）：“一种仿真方法，在其中维持一个连续的仿真时钟，并且模型的更新由事件驱动。”${ }^{2}$
- 同步（Synchronous）：根据挂钟（wall clock）基准推进时间的仿真。
- 异步（Asynchronous）：无时间参考推进的仿真，其运行速度取决于处理能力。
- 实时（Realtime）：一种仿真，其中“每一秒的执行时间等同于虚拟世界中的一秒时间。”${ }^{6}$
- 仿真时间（Simulation time）：“用于建模物理时间的抽象，表示自仿真开始以来经过的时间。例如，可以使用浮点变量来测量时间，其中0.0对应于仿真执行的起点。时间推进取决于具体的仿真需求。”${ }^{5}$
- 挂钟时间（Wall time）：现实世界时间的参考。
- 联邦时间（Federate time）：分布式联合仿真中外部仿真的时间参考。
- 硬实时（Hard realtime）：对同步实时执行有极高要求的仿真，超出分配的执行时间是绝对不可接受的。
- 伪实时（Pseudo-realtime）：对实时执行要求不那么严格的仿真。在大多数情况下，比如 $95\%$ 或更多时间里，仿真满足其时间约束，而剩余 $5\%$ 时间内的仿真真实性偏差并不严重。也称为软实时（soft realtime）。

表1展示了一些相关的仿真环境对执行模式的支持情况，尽管 AFSIM 是唯一支持全部模式的环境。

| 仿真环境 | 离散事件 | 离散时间 | 同步 | 伪实时 |
| :--- | :--- | :--- | :--- | :--- |
| AFSIM | $\checkmark$ | $\checkmark$ | $\checkmark$ | $\checkmark$ |
| MIXR |  | $\checkmark$ | $\checkmark$ | $\checkmark$ |
| EADSIM | $\checkmark$ |  |  |  |
| SUPPRESSOR | $\checkmark$ |  |  |  |
| NGTS |  | $\checkmark$ | $\checkmark$ | $\checkmark$ |

AFSIM：高级仿真集成建模框架（Advanced Framework for Simulation, Integration, and Modeling）；  
EADSIM：扩展防空仿真（Extended Air Defense Simulation）；  
MIXR：混合现实（Mixed Reality）；  
NGTS：下一代威胁仿真（Next Generation Threat Simulation）。

---

AFSIM 的开发者最初专注于实现军事任务仿真的能力。其他一些任务仿真，或可用于实现任务仿真的通用仿真工具对公众可用；然而，只有部分适用于政府用途。此外，还有一些仿真对公众不可用，但可供美国政府及其批准的合作伙伴使用。这些包括 EADSIM、FLAMES、SUPPRESSOR、联合仿真环境（Joint Simulation Environment，JSE）以及下一代威胁仿真（NGTS）。这些仿真环境的运行模式如表1所示。读者应注意，这些环境各自是为满足特定需求而开发的，对运行模式支持的比较仅反映了这些需求的一部分。

在公开可用的选项中，有若干支持基于时间步的执行。MathWorks 的 MATLAB/Simulink 环境通常作为时间步应用使用，尽管它也有 SimEvent 扩展用于离散事件和混合模式，SimScape 插件支持在同步模式下运行。${ }^{7,8}$ Simulation Open Framework Application (SOFA) 是一个基于时间步的环境，专注于异步物理和医学仿真。${ }^{9}$ 尽管通常不被视为仿真环境，Epic Games 的 Unreal Engine 可以归类为同步的基于时间步的物理仿真。${ }^{10}$ 美国航空航天局（NASA）艾姆斯研究中心已开源了两个仿真环境，均为基于时间步的异步模式：（a）任务仿真工具包（Mission Simulation Toolkit），用于航天器自主研究与开发；（b）多保真度仿真器（Multi-Fidelity Simulator, MFSim），用于空中交通仿真。${ }^{11,12}$ 在国防部的软件工具中，EAAGLES（Extensible Architecture for the Analysis and Generation of Linked Simulations，可扩展分析与生成关联仿真的架构）及其后继者 MIXR 混合现实仿真平台已开源。这些软件包提供了许多基于时间步的同步仿真能力。${ }^{13}$ 特别是其设计与构建在许多方面与 AFSIM 相似。MIXR 是一个面向对象的软件框架，用 $\mathrm{C}++$ 编写，使用重点在于同步虚拟仿真。

同时，还有若干公开可用的离散事件仿真软件包。JaamSim 是一个开源仿真包，主要用于石油和天然气行业。${ }^{14}$ 离散事件系统规范（DEVS, Discrete Event Systems Specification）形式化方法启发了多个开源软件包，包括 DEVSim++ 和 DEVSimPy，分别用 $\mathrm{C}++$ 和 Python 编写。${ }^{15,16}$

最后，还有若干由美国空军开发并用于空中任务和战役仿真的环境，它们构成了 AFSIM 的技术传承。这些环境主要用于构造性仿真，包括用于交战级仿真的 Brawler 和增强型地空模型（ESAMS）、用于任务级仿真的 Suppressor，以及用于战役仿真的 Thunder 和合成战区作战研究模型（STORM）。${ }^{17-20}$ 这些环境均基于离散事件设计，通常用于异步执行，尽管 Brawler、ESAMS 和 Suppressor 也有扩展以支持作为分布式仿真一部分的同步运行。

AFSIM 的历史和整体设计此前已有文献发表。${ }^{21,22}$ 本文重点描述 AFSIM 仿真执行核心的软件类和算法。但在此之前，下一节将总结此前已发表的 AFSIM 描述，并提供其他相关背景。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-04.jpg?height=489&width=1159&top_left_y=263&top_left_x=466)

图 I. AFSIM 的基于组件的架构允许用户分析员定义具有不同建模细节层次的灵活可扩展平台。分析员可以使用多种内置组件类型，或使用外部开发的组件类型。  
AFSIM：高级仿真集成建模框架（Advanced Framework for Simulation, Integration, and Modeling）；DIS：分布式交互仿真（Distributed Interactive Simulation）；EW：电子战（Electronic Warfare）；XIO：可扩展输入/输出系统（Extensible Input/Output System）。


## 3. AFSIM 概述

AFSIM 是一个面向对象的软件框架，用于创建仿真应用，主要聚焦于军事领域和任务。${ }^{21,22}$  
AFSIM 使用 $\mathrm{C}++$ 编写，基于平台与组件的架构设计，以及多个平台和组件之间的交互。平台是仿真中的顶层对象，通常用于表示在特定物理域（地面、空中、太空、海面、水下）中运行的载具。平台也可以表示建筑、组织或个体——任何能够做出决策并采取某种仿真行为的实体或代理。组件则表示子系统，例如传感器、通信设备、运动模型、信息处理等。组件化架构示意图如图1所示。在后续章节的软件设计图和算法描述中，将重点介绍 **Mover** 组件；不过，其他组件的调用和与仿真执行核心（simulation executive）的交互方式大体相同。作为一个框架，AFSIM 允许开发者创建非常灵活的仿真应用，并能以多种配置运行。一个常用的应用是 **AFSIM MISSION**，其运行模式包括：

- 基于事件步进（event-stepped）或基于帧步进（frame-stepped）
- 异步运行，或同步于时钟（可为实时时钟）
- 纯构造性（constructive），执行多次蒙特卡洛（Monte Carlo）迭代
- 虚拟仿真模式，可与分布式交互仿真（DIS, Distributed Interactive Simulation）或其他交互仿真协议进行交互

AFSIM 开发团队还创建了一个名为 **WARLOCK** 的应用，它通过基于 Qt 开发的交互式图形用户界面扩展了 MISSION 提供的功能，支持 **CITL（指挥员在回路）兵棋推演** 和 **OITL（操作员在回路）实验**。

在 AFSIM 提供的灵活和可扩展服务的核心是其 **仿真执行核心（simulation executive）**，它支持上述多种运行模式。下一节将讨论 AFSIM 仿真执行核心的软件设计。

---

## 4. 软件设计

本节将展示和描述若干重要类的类设计。需要注意的是，AFSIM 的实际软件实现包含的成员变量和函数远多于此处所能展示的。部分代码标识符（类名、变量名和函数名）已为样式和清晰性做了调整。为了便于理解，这里仅展示与仿真执行和配置各种模式相关的变量和函数。类设计之后，将以若干伪代码算法的形式描述这些类的功能。这里所描述的仿真执行核心基于 **AFSIM 2.5 版本**（2019年10月发布）；不过，该部分代码自 **AFSIM 2.0 版本**（2016年5月发布）以来保持稳定，未有重大修改。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-05.jpg?height=481&width=817&top_left_y=260&top_left_x=249)

图2. **Application** 和 **StandardApplication** 类为开发者提供了初始化和运行仿真的服务。这些服务可以通过用户开发的 **ApplicationExtensions** 进行扩展。




### 4.1. 类设计

在该框架中，**Application** 是一个抽象类，负责处理启动、初始化、执行、错误处理和关闭。可以通过继承创建子类以实现特定类型的应用，并且框架提供了一个健壮的扩展机制，支持更多样化和专业化的应用开发。下面所描述的 **MISSION** 算法在一个名为 **StandardApplication** 的子类中执行，而 **WARLOCK** 则实现为 **StandardApplication** 的扩展（见图2）。类图采用统一建模语言（UML）绘制。每个方框表示一个面向对象的类，方框之间的连线表示不同类型的关系：实线加实心箭头表示类之间的关联关系，通常表现为一个类的成员函数调用另一个类的成员函数；空心三角箭头表示继承关系，箭头指向父类，另一端为子类；实心菱形箭头表示组合关系，菱形所在的类包含了另一端的类。抽象类用圆圈内的“A”表示，具体类用圆圈内的“C”表示，抽象函数用斜体表示。

在 **Application** 执行的核心是抽象类 **Simulation** 及其子类 **EventStepSimulation** 和 **FrameStepSimulation**。它们的继承关系以及关键数据成员和函数如图3所示。**Simulation** 可以由 **ClockSource** 驱动，后者提供启动、停止和重置时钟的方法，或者由 **RealTimeClockSource** 驱动，它调用操作系统时钟。关键成员变量和函数如图4所示。当仿真不以同步模式运行时，**ClockSource** 在很大程度上被忽略。两个 **EventManager** 实例分别管理和执行：(a) 仿真事件，如平台组件的一次性或周期性事件；(b) 挂钟事件（wall events），如暂停和恢复分布式仿真。尤其是在配置为实时执行时，需要若干数据成员来跟踪仿真时间。**Simulation** 可以以实时时钟的倍数运行（更快或更慢），其因子由 **clock_rate** 定义。关键函数用于与 **EventManager** 实例交互，并控制仿真时间的前向推进。

在帧步进模式下，典型应用涉及的核心类如图5所示。**Application** 加载一个 **Scenario**，它是输入脚本的序列化，定义了平台、组件和行为。然后，**Scenario** 用于初始化一个 **FrameStepSimulation**，该对象包含一组由 **Scenario** 定义的 **Platform** 实例。**FrameStepSimulation** 由 **Application** 驱动，其运行方式在下一节的算法中给出。在运行过程中可能会调度事件，包括仿真事件（如平台组件的一次性事件）或挂钟事件。**FrameStepSimulation** 类直接与需要周期性更新的平台组件交互，其中最关键的组件是 **Mover**，它定义了平台在仿真空间中的运动。**FrameStepSimulation** 类还具有特殊的数据成员和函数，用于精确控制仿真时间的推进，以及跟踪相对于仿真帧的运行时性能。

对于事件步进仿真，其涉及的类如图6所示。与帧步进模式不同，**EventStepSimulation** 不直接与 **Platform** 实例及其组件交互，而是由平台组件（包括 **Mover** 类）调度事件，例如将 **Mover UpdateEvent** 添加到由 **EventManager** 维护的事件队列中。详细的类图见图7，展示了这些类的关键数据成员和函数。**Mover** 类维护平台的状态（位置、速度和姿态），其状态在每次调用 **Mover** 的 **update** 函数时更新，函数内部通过数值积分方式求解运动方程。**Mover** 的 **update** 函数在帧步进和事件步进模式下通过不同的函数栈被调用。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-06.jpg?height=1110&width=1083&top_left_y=260&top_left_x=504)

图3. **Simulation** 类及其子类 **EventStepSimulation** 和 **FrameStepSimulation** 负责模型和事件的计时与执行，并可配置为实时运行。

事件必须定义 **execute** 方法，用于执行与该事件相关的计算。例如，**MoverUpdateEvent** 的 **execute** 函数会调用相关 **Mover** 的 **update** 函数。**execute** 函数返回一个枚举整数，称为事件的处置（disposition），它指示：(a) 事件已完成所需计算，可以从事件队列中删除；(b) 事件仍需计算，应被重新调度。典型的 **MoverUpdateEvent** 会请求在当前 **sim_time** 加上 **Mover** 的 **update_interval** 时被重新调度。

事件由 **EventManager** 类管理，该类维护一个事件优先队列。优先级由一个二元组确定，其第一元素是事件的 **sim_time**，第二元素是一个计数器，每次有新事件加入队列时递增。此设计保证了在多个事件具有相同 **sim_time** 的情况下，先加入队列、计数器较小的事件先被执行。

接下来，将通过算法描述这些类所提供的功能。

---

### 4.2. 算法描述

**MISSION** 应用的运行环境支持通过事件步进仿真或帧步进仿真来执行构造性或虚拟仿真模式，并可选择实时或非实时执行，所有这些仅由少量命令行参数和输入配置控制。运行模式在应用启动时固定，整个运行期间所有仿真平台和组件均通过同一推进机制更新。然而，场景在后续运行中只需少量修改即可在不同模式间切换。其操作流程如算法1所示，并在下文进行说明。在算法伪代码中，右向三角形表示对特定代码行的注释。

在高层次上，**MISSION** 应用包含三大控制层：**Application**、**Scenario** 和 **Simulation**。  
- **Application** 负责程序的启动、关闭和错误处理。在启动期间，输入参数和选项被解析并存储，以便在初始化链中进一步传递。  
- **Scenario** 是用户输入脚本中所有数据的序列化，定义了用户希望在仿真中包含的实体类型，这些实体的组织方式及其行为，以及用户希望记录在日志文件和数据文件中的数据等配置和运行时控制。  
- 在加载用户脚本至 **Scenario** 后，**Application** 创建并初始化一个 **Simulation** 对象。该对象的具体类型取决于步骤1.3中解析的输入。  

随后，**Application** 执行主程序循环 **RUN_EVENT_LOOP**。若在执行过程中出现错误，**Application** 会捕获并打印错误信息，最后关闭程序，将所有文件和资源释放回操作系统。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-07.jpg?height=1102&width=744&top_left_y=263&top_left_x=287)

图4. **ClockSource** 和 **RealTimeClockSource** 类提供启动、停止（暂停）和重置时钟的方法，可用于控制仿真推进。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-07.jpg?height=630&width=801&top_left_y=1608&top_left_x=260)

图5. 驱动帧步进仿真的主要类。注意 **FrameStepSimulation** 类直接关联平台组件（如 **Mover**），并注意 **EventManager** 类中存在事件队列，用于处理非连续或“一次性”现象。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-07.jpg?height=841&width=809&top_left_y=263&top_left_x=1114)

图6. 驱动事件步进仿真的主要类。注意 **EventStepSimulation** 类与平台 **Mover** 组件之间的间接关联程度。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-07.jpg?height=809&width=812&top_left_y=1505&top_left_x=1111)

图7. 显示事件步进模式下的关键函数。当 **MoverUpdateEvent** 从队列中取出时，其 **execute** 函数被调用，进而调用 **Mover** 的 **update** 函数。

---

**算法1.** AFSIM Mission 主函数创建应用和仿真对象，然后执行事件循环。

![](https://cdn.mathpix.com/snip/images/7ieQk0bB82tcSiJJwLkO1--qvZ-cjqLxFkTh7MZ6ojQ.original.fullsize.png)

**算法2.** AFSIM 应用控制仿真，调用其函数以阻塞和推进。

![](https://cdn.mathpix.com/snip/images/vyZ8LTjsjsqtMhTTy4bGWKOz1L36_p_EUJfDOCCSqpU.original.fullsize.png)

---

函数 **RUN_EVENT_LOOP** 具有简单的结构，如算法2所示。它通过调用两个函数推动仿真时间前进：**WAIT_FOR_ADVANCE_TIME** 和 **ADVANCE_TIME**。命令行选项和用户输入脚本命令（算法步骤1.3）决定了执行的是哪个派生类的函数实现，从而提供了在离散事件风格或帧步进风格下执行的灵活性。该函数还使用本地 **WallClock** 周期性地向用户打印状态信息，使用户能够了解仿真进展情况。需要注意的是，**WallClock** 是一个持续累积时间的时钟，与仿真状态无关。即使仿真暂停，**WallClock** 仍会推进，而仿真时钟源会根据特定请求和事件的发生而停止或启动。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-08.jpg?height=508&width=809&top_left_y=260&top_left_x=1071)

图8. 在帧步进模式下，仿真通过 **ADVANCE_TIME**、**ADVANCE_FRAME** 和 **WAIT_FOR_ADVANCE_TIME** 函数的动作与计算推进。



从这里开始，程序在函数调用栈上的进一步执行取决于用户请求的模式：事件步进（event-stepped）或帧步进（frame-stepped）。下面将首先介绍帧步进模式，然后是事件步进模式。

#### 4.2.1 帧步进内循环

在帧步进模式下，有三个函数负责仿真的前向推进。这一过程在图8中以图形形式展示。在帧步进模式中，仿真在等待进入下一帧的时间时具有高度精确性。这种精确性通过 **WAIT_FOR_ADVANCE_TIME** 函数实现，具体如算法3所示。该函数检查仿真的时钟源是否已停止：  
- 如果已停止，它会休眠4毫秒，然后返回（恢复时钟运行的处理由仿真的其他部分负责）。  
- 如果时钟在运行，则比较时钟时间与下一帧的时间，并休眠直到距离下一帧仅剩4毫秒。随后进入一个 `while` 循环，不断检查时钟时间。尽管这是一种低效的CPU使用方式，但在非实时操作系统（如 Windows）上能获得更好的效果。退出 `while` 循环时，仿真时间已到达下一帧的时间，准备推进。

**算法3.** 帧步进模式下的 AFSIM 仿真在等待时非常精确：在需要推进前的4毫秒让出CPU，然后占用CPU直至下一帧开始。

![](https://cdn.mathpix.com/snip/images/ynq_qJzHMJxoA0Sr_tag4RLSTc89FSJ0UPpe-df02lQ.original.fullsize.png)

---

推进到下一帧由两个层次的函数控制：**ADVANCE_TIME** 和 **ADVANCE_FRAME**，分别详见算法4和算法5。  
- **ADVANCE_TIME** 是一个重载函数。在帧步进仿真中，它从仿真时钟源获取时间并与下一帧时间比较。若时钟在运行，则 **WAIT_FOR_ADVANCE_TIME** 会保证仿真时间等于下一帧时间。在 **ADVANCE_TIME** 内部，当前仿真时间被设定为“下一帧时间 + 一个微小增量（$1 \mu s$）”与挂钟时间的较小值。若该值大于下一帧时间，表明挂钟至少比下一帧时间快 $1 \mu s$，这意味着确实到了推进的时刻。此时会向仿真观察器（记录输出并写入日志文件的对象）发送时间推进的消息，并检查仿真是否已到结束点并需进入收尾操作。若仿真时间不大于下一帧时间，则说明挂钟落后于下一帧时间。在 **WAIT_FOR_ADVANCE_TIME** 中，如果时钟源已停止，该函数会休眠4毫秒并返回。在此情境下，**ADVANCE_TIME** 会调用基类 **Simulation** 的实现，用于检查挂钟事件，特别是恢复暂停仿真的事件。

在 **WAIT_FOR_ADVANCE_TIME** 和 **ADVANCE_TIME** 的复杂逻辑确保确实到了推进时刻之后，程序控制权交给 **ADVANCE_FRAME**（算法5）。该函数执行四个主要步骤：  
(a) 更新当前和下一帧时间；  
(b) 将连续模型更新到当前时间；  
(c) 执行所有排队事件；  
(d) 检查并处理是否超出帧时间。  

在第一个部分中，**frame_count** 递增。第二部分（算法5第5.7行开始）体现了连续模型更新的顺序：首先更新平台，确保仿真几何正确；然后更新通信设备以处理消息收发；接着更新传感器；最后更新帧对象。帧对象是一种通用组件，采用回调架构，允许用户程序员添加定义在外部包中的额外组件类型，从而使这些帧对象与内置连续模型同步执行。更新连续模型后，事件队列会被处理，以执行所有预定在当前仿真时间或之前发生的事件。该过程与事件步进类似，但在此模式下，事件实际执行时间可能与排队时间不同，且事件可能请求重新调度，需要保证其重新排入队列的时间不早于原始时间。最后，**ADVANCE_FRAME** 检查是否超出了帧时间并更新相关统计。如果超时，则下一帧将跳过传感器更新以尝试追赶进度；若超出超过 $10\%$，则整个下一帧将被跳过。用户文档中明确警告，运行包含大量平台和传感器的复杂场景时需谨慎，因为在帧超时时仿真不会优雅降级。最后，该函数广播当前帧完成的消息，并返回当前帧时间。

**算法4.** 帧步进模式下，AFSIM 仿真通过检查仿真时间是否超过下一帧起始时间来推进。注意该实现覆盖了基类中的实现（见算法7）。暂停的仿真将在基类的 **ADVANCE_TIME** 功能中恢复。

![](https://cdn.mathpix.com/snip/images/oGg7UDWkg_pJm5Ij6ZEmQNCvmudhXbIapFdkJzkypUs.original.fullsize.png)

**算法5.** 推进一帧包含四个主要步骤：1）更新当前和下一帧时间；2）将连续模型更新到当前时间；3）执行所有排队事件；4）检查并处理超出帧时间的情况。

![](https://cdn.mathpix.com/snip/images/0EepsXe_KDdQOi0R8JCFEo8kQWvVeZ8UFqUeCBmBYbI.original.fullsize.png)

值得注意的是，在算法5的第二个主要步骤中，存在对多线程更新平台和传感器的初步支持，使用一个 **MultiThreadManager** 类（此处未详细介绍）。对于许多仿真需求而言，可重复性是首要任务，而当计算顺序影响仿真结果时，这会带来挑战。该主题将在结论部分再次讨论。

接下来，将展示和描述事件步进模式下的执行算法。

![](https://cdn.mathpix.com/cropped/2025_08_24_073f7b838835bf37d3d0g-10.jpg?height=502&width=811&top_left_y=260&top_left_x=1071)

图9. 在事件步进模式下，仿真通过 **ADVANCE_TIME**、**DISPATCH_EVENTS**、**DISPATCH_SIM_EVENTS**、**DISPATCH_WALL_EVENTS** 和 **WAIT_FOR_ADVANCE_TIME** 等函数的动作与计算推进。


#### 4.2.2 事件步进内循环

事件步进的执行由五个函数驱动：**ADVANCE_TIME**、**DISPATCH_EVENTS**、**DISPATCH_SIM_EVENTS**、**DISPATCH_WALL_EVENTS** 和 **WAIT_FOR_ADVANCE_TIME**。这些在图9中以图形方式展示。对于事件步进执行，如算法6所述，**WAIT_FOR_ADVANCE_TIME** 仅在程序运行于实时同步模式下时才会等待。如果是这样，则会检查同步时钟源并与仿真时间比较：  
- 如果仿真已落后，会广播一条消息，提示程序其他部分监听，以减少CPU使用直至仿真赶上；  
- 否则，如果比较结果表明距离推进的指定时间仍有较长等待，则调用系统休眠一段相对较短的时间。在实际运行中，这一方法表现良好：4毫秒作为“长”等待时间，1毫秒作为“短”休眠时间。这一策略主要用于在非实时操作系统中提供足够的实时性能。到达休眠时间并返回后，程序进入 **ADVANCE_TIME**。

在 **ADVANCE_TIME** 中，仿真运行在非实时操作系统中。一旦到达下一个事件的时间，或没有更多事件被调度，仿真便推进至结束。如果存在时钟源，它可覆盖仿真时间，允许添加额外事件。然后该函数广播时间推进的消息。如果仿真到达结束点，仿真状态会标记为 **PENDING_COMPLETE**，以允许在代码其他部分启动收尾和清理步骤。此时，事件执行任务交由 **DISPATCH_EVENTS** 完成。该函数还会记录累计的时间消耗，以便开发者和用户评估仿真性能并优化事件执行算法。

**算法6.** 事件步进的 AFSIM 仿真仅在实时模式下并且有“长”等待时间时才会等待。CPU 仅在“短”时间段内释放，这是在非实时操作系统中获得更高可靠性的一种策略。

![](https://cdn.mathpix.com/snip/images/qTH0XOvXbpI1EaCSlN-V8E4rADXVuy6EsO9ux_NHkEc.original.fullsize.png)

**算法7.** AFSIM 仿真推进到事件队列中的下一个事件时间，或来自时钟控制器的时间，并调度所有发生在该时间之前或该时间的未处理事件。

![](https://cdn.mathpix.com/snip/images/ZG8v-oy8u5rDS0EY3B-5CF0g7sMtxWFHo8EJifO26KE.original.fullsize.png)

---

在更低层次的事件调度中，有几个密切相关的函数：**DISPATCH_EVENTS**、**DISPATCH_SIM_EVENTS** 和 **DISPATCH_WALL_EVENTS**。后两者使用了一个辅助函数 **DISPATCH_HELPER**，因为底层机制相同，只是使用的 **EventManager** 实例不同。这些在算法8和算法9中描述。若仿真未配置为与时钟源同步运行，或未作为分布式仿真的参与者——换句话说，纯构造性、尽可能快地执行事件——则不会存在 wall 事件。

**算法8.** 事件调度分两步：1）调度请求仿真时间的仿真事件；2）调度 wall 事件，主要是暂停和恢复等控制事件。这两步均使用算法9定义的辅助函数，但分别操作不同的事件队列，并分别使用仿真时间或挂钟时间。

![](https://cdn.mathpix.com/snip/images/ZRCqdi5HOhEmPD0lTd4EUcAjaNsYD0CA_K5A8fMV8-s.original.fullsize.png)

**算法9.** 事件调度通过查看队列中的下一个事件，如果其计划时间早于推进的目标时间，则将其弹出并执行。这与算法5.11中 **ADVANCE_FRAME** 的步骤3非常相似。

![](https://cdn.mathpix.com/snip/images/5ZUlS2g1VI2_t0D5tJ1RXKrweanhIWWiw_--PWo98oE.original.fullsize.png)

在 **DISPATCH_HELPER** 中，算法与 **ADVANCE_FRAME** 函数的步骤3非常类似。函数接收期望执行的事件时间作为输入，获取队列中下一个事件的引用，并检查其执行时间是否早于或等于输入时间。如果满足条件，则将事件从队列中弹出并执行；若事件请求重新调度，则将其加入回队列，并再次获取下一个事件的引用。

总结事件步进执行：当仿真不与时钟源同步运行时，仿真逐事件推进。所有在给定时间调度的事件会按批处理，执行顺序为它们加入队列的顺序。当与时钟源同步时，仿真会检查是否需要等待再推进。

在实际应用中，AFSIM 常用于虚拟仿真或由 **WARLOCK** 应用支持的建模与仿真驱动兵棋推演，这些通常配置为实时或快于实时的事件步进模式。如上所述，AFSIM 文档建议用户避免在复杂场景中使用帧步进模式，并指出程序在帧超时时的行为不够健壮。

---

## 5. 结论

以上所述的软件设计和算法揭示了 AFSIM 的仿真执行核心如何同时支持帧步进与离散事件模式，以及异步与同步模式。这两种模式的核心分别体现在帧步进模式的算法5和离散事件模式的算法7与算法9中。AFSIM 应用通过几乎一致的方式组织这两个执行核心的外围代码，并利用面向对象的多态性和函数重载（见图5和图6），从而实现模式上的灵活性。借助这种灵活性，AFSIM 支持虚拟仿真和构造性仿真的需求，使用户能够快速在两种模式间转换场景，从而更丰富地探索技术概念。

未来工作中，这里描述的软件可通过更通用的方式实现并发布给仿真软件研究社区。此外，对在相同场景下不同模式下的性能评估和软件剖析将有助于发现代码优化和未来探索的方向。潜在的研究方向包括：处理帧超时、处理离散事件同步执行的滞后，以及通过多线程技术避免或缓解超时推进。

---

## 资金来源

本研究未获得任何公共、商业或非营利机构的专项资助。



## References

1. Law AM. Simulation Modeling and Analysis, Fifth Edition. New York, NY: McGraw-Hill, 2015.
2. Gamez D. The simulation of spiking neural networks. In: Abu-Taieh E (ed) Handbook of Research on Discrete Event Simulation Environments: Technologies and Applications. Hershey, PA: Information Science Reference, 2009. pp. 337-358.
3. Naturo J. Building Software for Simulation: Theory and Algorithms, with Applications in C++. Hoboken, New Jersey: John Wiley & Sons, Inc., 2011.
4. Cellier F and Kofman E. Continuous System Simulation. New York, NY: Springer Science+Business Media, Inc., 2006.
5. Hodson D and Baldwin R. Characterizing, measuring, and validating the temporal consistency of live-virtual-constructive environments. Simulation 2009; 85(10): 671-682.
6. Real-time Platform Reference Federation Object Model Product Development Group. Standard for Guidance, Rationale, and Interoperability Modalities for the Real-time Platform Reference Federation Object Model, https:// www.sisostds.org/DigitalLibrary.aspx?Command=Core_Do wnload&EntryId=30823 (accessed 8 January 2021).
7. Clune MI, Mosterman PJ and Cassandras CG. Discrete event and hybrid system simulation with SimEvents. In: 2006 8th International Workshop on Discrete Event Systems, 10-12 July 2006, Ann Arbor, MI. IEEE: Piscataway, NJ, pp. 386-387.
8. Mathworks, Inc.SimScape, https://www.mathworks.com/ help/physmod/simscape/index.html (2019, accessed 11 December 2020).
9. Faure F, Duriez C, Delingette H, et al. SOFA: a multi-model framework for interactive physical simulation. In: Payan Y (ed) Soft tissue biomechanical modeling for computer assisted surgery. Studies in mechanobiology, tissue engineering and biomaterials, volume 11. New York: Springer, 2012. pp. 283-321.
10. Epic Games, Inc. Unreal engine 4 documentation, https:// docs.unrealengine.com/en-US/index.html (2019, accessed 11 December 2020).
11. Hine B. Mission simulation toolkit, https://ti.arc.nasa.gov/ opensource/projects/mission-simulation-toolkit/ (2005, accessed 11 December 2020).
12. Lee R. Multi-fidelity simulation - mfsim, https://ti.arc.nasa. gov/opensource/projects/mfsim/ (2015, accessed 11 December 2020).
13. Hodson D, Gehl D and Baldwin R. Building distributed simulations utilizing the eaagles framework. Interservice/ Industry Training, Simulation, and Education Conference (I/ ITSEC)2006.
14. JaamSim Development Team. JaamSim: Discrete-Event Simulation Software, Version 2016-14, http://jaamsim.com (2016, accessed 11 December 2020).
15. Kim T, Sung C, Hong SY, et al. DEVSim++ Toolset for Defense Modeling and Simulation and Interoperation. $J$ Defense Model Simul 2011; 8: 129-142.
16. Capocchi L, Santucci JF, Poggi B, et al. DEVSimPy: a collaborative Python software for modeling and simulation of DEVS systems. In: Proceedings of the Workshop on Enabling Technologies: Infrastructure for Collaborative Enterprises, 27-29 June 2011, Paris, France. IEEE: Piscataway, NJ, pp. 170-175.
17. DSIAC. Brawler tactical air combat simulation v8.5 data sheet, https://www.dsiac.org/resources/models/brawler (2019, 11 December 2020).
18. DSIAC. Esams enhanced surface-to-air missile simulation v55. data sheet, https://www.dsiac.org/resources/models/ esams (2014, 11 December 2020).
19. JASP. Thunder data sheet, http://www.jasp-online.org/modelsimulations/thunder/ (2019, accessed 11 December 2020).
20. Group W. STORM Analyst's Manual v2.7. HQ USAF Studies, Analyses, and Assessments, 2017.
21. Zeh J, Birkmire B, Clive PD, et al. Advanced Framework for Simulation, Integration, and Modeling (AFSIM) Version 1.8 Overview. Technical report, Air Force Research Laboratory, 2014.
22. Clive PD, Johnson JA, Moss MJ, et al. Advanced Framework for Simulation, Integration and Modeling (AFSIM). International Conference of Scientific Computing 2015; 2015: 73-77.

## Author biographies

J Scott Thompson is a senior operations research programmer and analyst at the Air Force Research Laboratory (AFRL), Aerospace Systems Directorate, Wright-Patterson AFB, Ohio USA. He received a BS in Physics with Honors from Hampden-Sydney College in 2004 and a MS in Computational Science from George Mason University in 2008 . He is also currently a PhD student at the Air Force Institute of Technology (AFIT). His research interests include modeling, simulation, and analysis, software engineering, and machine learning. He is a member of Alpha Chi Sigma, Chi Beta Phi, and Tau Beta Pi.

Douglas D Hodson is an associate professor of Computer Engineering at the Air Force Institute of Technology (AFIT), Wright-Patterson AFB, Ohio USA. He received a BS in Physics from Wright State University in 1985, and both an MS in Electro-Optics in 1987 and an MBA in 1999 from the University of Dayton. He completed his PhD at the AFIT in 2009. His research interests include computer engineering, software engineering, realtime distributed simulation, and quantum communications. He is also a DAGSI scholar and a member of Tau Beta Pi.




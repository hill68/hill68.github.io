+++
date = '2025-07-13T17:07:50+08:00'
draft = false
title = 'BehaviorTree.CPP库'
summary= "用于创建和管理行为树(Behavior Trees)开源C++库BehaviorTree.CPP，该库特别针对机器人的需求进行了优化，例如任务规划、导航、操作等。"
+++

https://www.behaviortree.dev/  是学习和使用 BehaviorTree.CPP 库的官方入口。它不仅提供了这个强大的C++库，还围绕它构建了一个完整的生态系统，包括了革命性的可视化编辑和监控工具 Groot2。 BehaviorTree.CPP作为一个强大、灵活且高效的工具来设计复杂的行为逻辑，适合在机器人、自动化或相关AI领域选择使用。


### **核心内容：BehaviorTree.CPP 是什么？**

* **一个C++库**：它是一个功能齐全的C++库，让开发者可以方便地构建、执行和调试行为树。
* **专注于任务执行**：与传统的有限状态机（Finite State Machines）不同，行为树更关注于“如何执行一个动作”，而不是“在不同状态间切换”。这使得它在处理复杂任务时更加灵活和强大。
* **专为机器人设计**：虽然行为树可以用在很多领域，但 BehaviorTree.CPP 特别针对机器人的需求进行了优化，例如任务规划、导航、操作等。

### **主要特点和优势**

1.  **组合性 (Composability)**：
    * 您可以创建简单的、可复用的行为（例如“移动到A点”、“抓取物体”），然后像搭积木一样将它们组合成复杂的、有层次结构的行为树（例如“找到并拿起一个物体，然后将它送到B点”）。
    * 这种模块化的方法使得代码更容易维护和扩展。

2.  **C++的性能 + 脚本的灵活性**：
    * 核心的行为节点（Action Nodes）用高性能的C++编写，确保了执行效率。
    * 而整个行为树的逻辑结构则可以通过简单的 **XML 文件**来定义和修改。这意味着您可以随时调整机器人的行为，而**无需重新编译C++代码**，极大地加快了开发和调试的速度。

3.  **强大的生态系统和工具**：
    * **Groot2**：这是一个与 BehaviorTree.CPP 配套的**可视化集成开发环境（IDE）**。通过 Groot2，您可以通过**拖拽**的方式来创建和编辑行为树，并且可以**实时监控**行为树的执行状态。这对于新手来说极大地降低了学习门槛，对于资深开发者来说也大大提高了效率。
    * **丰富的文档和教程**：网站提供了详细的文档和教程，可以帮助新用户快速上手。
    * **开源和社区支持**：该项目是开源的，代码托管在 GitHub 上，并拥有一个活跃的社区论坛，您可以在那里寻求帮助和交流。

### **谁会使用 BehaviorTree.CPP？**

* **机器人工程师**：用于开发各种机器人的自主行为，从工业机器人到服务机器人、无人机等。
* **游戏开发者**：用于设计和实现非玩家角色（NPC）的复杂AI。
* **自动化系统开发者**：用于构建任何需要进行复杂任务规划和决策的自动化系统。



## BehaviorTree.CPP 4.6
——The C++ library to build Behavior Trees.

# 关于

## 关于此库

该**C++**库提供了一个创建**行为树（BehaviorTrees）**的框架。它被设计得灵活、易用且高效。

即使我们的主要应用场景是**机器人学**，你也可以使用该库来构建**游戏中的AI**，或者用来替代你应用中的有限状态机。

与其他实现相比，**BehaviorTree.CPP**具有许多有趣的特性：

* 它将**异步动作**（即非阻塞例程）作为一等公民。
* 树在运行时创建，使用基于XML的**解释型语言**。
* 包含一个**日志/性能分析**基础设施，允许用户可视化、记录、回放和分析状态转换。
* 你可以静态链接自定义的TreeNodes，也可以将它们转换为插件，在运行时加载。

## 什么是行为树？

行为树（**BT**）是一种组织自主体（如机器人或计算机游戏中的虚拟实体）间不同任务切换的方式。

BT是一种非常高效的创建复杂系统的方法，这些系统既模块化又响应迅速。这些特性在许多应用中至关重要，促使BT从游戏编程扩展到AI和机器人学的多个领域。

如果你已经熟悉有限状态机（**FSM**），你会很容易理解大部分概念，但希望你会发现BT更具表达力且更易于推理。

将**树节点**视为一组构建模块。这些模块用C++实现，且是“可组合”的：换句话说，它们可以被“组装”起来构建行为。

![img](https://www.behaviortree.dev/assets/images/intro_build_trees-d8cabed430fbf41b95a63c09f44b528a.svg)

上图展示了我们如何将这些动作排列在一个简单序列中；动作将按从左到右的顺序执行。欲了解更多，请访问[BT入门](https://www.behaviortree.dev/docs/learn-the-basics/BT_basics)。

### 行为树的主要优势

* **它们本质上是层级结构**：我们可以*组合*复杂行为，包括将整棵树作为更大树的子分支。例如，“取啤酒”行为可以重用“抓取物体”这棵树。
* **其图形表示具有语义意义**：更容易“阅读”行为树并理解对应工作流程。相比之下，有限状态机中的状态转换无论文本还是图形表达都较难理解。
* **它们更具表达力**：现成的控制节点（ControlNodes）和装饰节点（DecoratorNodes）使表达更复杂的控制流成为可能。用户还可以用自定义节点扩展“词汇”。

## “好吧，但我们为什么需要行为树（或有限状态机）？”

许多软件系统（机器人学是显著例子）本质上非常复杂。

管理复杂性、多样性和可扩展性的常用方法是采用[基于组件的软件工程](https://en.wikipedia.org/wiki/Component-based_software_engineering)的理念。

现有机器人中间件，无论正式或非正式，均采用此方法，著名例子有[ROS](http://www.ros.org/)、[YARP](http://www.yarp.it/)和[SmartSoft](http://www.servicerobotik-ulm.de/)。

一个“好”的软件架构应具备：

* 模块化。
* 组件可重用性。
* 可组合性。
* 良好的关注点分离。

若不从一开始遵循这些原则，软件将紧耦合且难以重用。

软件系统的业务逻辑往往分散于多个组件，开发者**难以推理和调试**。

实现强关注点分离，最好将业务逻辑集中管理。

**有限状态机**正是为此设计，近年来，尤其在游戏行业，**行为树**逐渐流行。

# 基础概念

了解行为树及其使用方法。

## 行为树简介

行为树是由**层级节点组成的树**，控制“任务”的执行流程，与有限状态机不同。

### 基本概念

* 一个称为“**tick**”的信号发送到树根，沿树传播直到叶节点。
* 任何接收**tick**信号的TreeNode都会执行其回调，回调必须返回：

  * **SUCCESS**
  * **FAILURE**
  * **RUNNING**
* RUNNING表示动作需要更多时间完成。
* 若TreeNode有子节点，它负责传播tick；不同节点类型可能对是否、何时及次数有不同规则。
* **叶节点（LeafNodes）**无子节点，是实际命令节点，即行为树与系统交互的接口。**动作节点**是最常见的叶节点。

> 💡 提示
>
> 单词**tick**常用作*动词*（to tick / to be ticked），含义为：

```text
“调用 `TreeNode` 的回调函数 `tick()`。”
```

在面向服务架构中，叶节点包含与执行实际操作的“服务器”通信的“客户端”代码。

### tick的工作原理

为形象化tick树的过程，考虑下例。

![basic sequence](https://www.behaviortree.dev/assets/images/sequence_animation-4155a892772542caf81fa16c824c91f8.svg)

**序列节点（Sequence）**是最简单的**控制节点（ControlNode）**：依次执行子节点，若全部成功则返回SUCCESS。

1. 第一次tick将序列节点置为RUNNING（橙色）。
2. 序列节点tick第一个子节点“OpenDoor”，其最终返回SUCCESS。
3. 第二个子节点“Walk”及随后“CloseDoor”被tick。
4. 最后一个子节点完成后，序列节点从RUNNING切换为SUCCESS。

### 节点类型


![UML hierarchy](data:image/png;base64,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)

| TreeNode 类型         | 子节点数  | 说明                           |
| ------------------- | ----- | ---------------------------- |
| 控制节点（ControlNode）   | 1...N | 通常根据兄弟节点的结果和/或自身状态来tick其子节点。 |
| 装饰节点（DecoratorNode） | 1     | 可能会改变子节点的结果，或多次tick子节点。      |
| 条件节点（ConditionNode） | 0     | 不应改变系统状态。不得返回RUNNING。        |
| 动作节点（ActionNode）    | 0     | 这是“执行操作”的节点。                 |

在**动作节点**的上下文中，我们可以进一步区分同步和异步节点。

前者是原子执行的，会阻塞行为树，直到返回SUCCESS或FAILURE。

异步动作则可能返回RUNNING，表示动作仍在执行中。

需要对它们再次tick，直到最终返回SUCCESS或FAILURE。

### 示例

为更好理解行为树的工作原理，我们关注一些实际例子。为简化起见，这里不考虑动作返回RUNNING时的情况。

假设每个动作都是原子且同步执行的。

#### 第一个控制节点：序列（Sequence）

让我们用最基本且常用的控制节点——[序列节点（SequenceNode）](https://www.behaviortree.dev/docs/nodes-library/SequenceNode)来说明行为树的工作方式。

控制节点的子节点始终是**有序的**；在图形表示中，执行顺序是**从左到右**。


![Simple Sequence: fridge](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIiB3aWR0aD0iNDY2IiBoZWlnaHQ9IjE1MSIgY29udGVudD0iJmx0O214ZmlsZSBob3N0PSZxdW90O2FwcC5kaWFncmFtcy5uZXQmcXVvdDsgbW9kaWZpZWQ9JnF1b3Q7MjAyMi0wMS0xNFQyMjo1MDoxOC4xNTVaJnF1b3Q7IGFnZW50PSZxdW90OzUuMCAoWDExKSZxdW90OyB2ZXJzaW9uPSZxdW90OzE2LjIuNyZxdW90OyBldGFnPSZxdW90O21HUGNYNV9Ma01xUjBYdDhoSzAxJnF1b3Q7IHR5cGU9JnF1b3Q7Z29vZ2xlJnF1b3Q7Jmd0OyZsdDtkaWFncmFtIGlkPSZxdW90Oy1EZWs1NENMOU1BT19yOWloT2djJnF1b3Q7Jmd0O3pWWk5jNXN3RVAwMXZtYjRNTmk1aHNUdG9aMTJ4b2MyUjQzWmdGS2hwVUxZdUwrK3dxd0FRVHZqTmg3SFhHQ2Z0Tkx1ZXl0V2l6QXBtZytLbGZsblRFRXNBaTl0RnVIaklnajh3UGZOcTBXT2hJVHIrdzdKRkU4Skc0QXQvd1VFZW9UV1BJWEttYWdSaGVhbEMrNVFTdGhwQjJOSzRjR2Q5b0xDM2JWa0djeUE3WTZKT2ZxTnB6b24xSS92aDRHUHdMT2N0bDRIcTI2Z1lIWXlaVkxsTE1YRENBcWZGbUdpRUhYM1ZUUUppSlk5eTB2bnQvbkxhQitZQXFuUGNRZ29ESDIwdVVGcVVpVlRvalN2QjRXMVRLSDE4SXlGU3VlWW9XVGlFMkpwUU4rQXI2RDFrWVJpdFVZRDVib1FOQW9OMTk5Yjk3dUlyT2ZSeUdOREs1K01veldrVnNmT2FSVlorM2s4T1BpZExPdjRnbEpUSlA3YTJGMkdiVm9PUnhYV2FrZFFSRFhFVkFaRVd6eG4wdS8xTVpVTldJRFoxVXhSSUpqbWUzZDFSaFdXOWZNR0Vjd0g2ZkJuVGNLYjFXUks3VWlqZjVMby95UlpYVjRTY3YySzNLd1llUFNIQ21NNm5mYi9aRzI3UkJjVmVVMkU3Y000Uyt2bHpXbzkwalo0aC9PM2ZyL3pSOUhzbWFocDBTMzhyRUdhYUtkaXVjb2NjcTVoVzdKVFdnZlQrMXdWcHNSVVd1RVBTRkNnT3EwV2V1YlpiSHJLOXFBME5FNWFaOUJoYTloemE5aVc4R0hVcjJ3VHlrZXRhdW05bmNCNFJ1Q1hFdVRHdFBMc3doUmVnQ2gvT1RuczBSV1pXczJZYXE5TUR3RHE1bmlhRnRSVmVWclBlRW9FVm5DakpiV01sM2ZSMWNneTVuQmY3RnJRY08wT24zNEQmbHQ7L2RpYWdyYW0mZ3Q7Jmx0Oy9teGZpbGUmZ3Q7IiB2ZXJzaW9uPSIxLjEiIHZpZXdCb3g9Ii0wLjUgLTAuNSA0NjYgMTUxIj48Zz48cGF0aCBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyMzAgNTAgTCAxMDUuOTQgOTcuNzEiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAxMDEuMDQgOTkuNiBMIDEwNi4zMiA5My44MiBMIDEwNS45NCA5Ny43MSBMIDEwOC44MyAxMDAuMzUgWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDIzMCA1MCBMIDIzMCA5My42MyIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGZpbGw9IiMwMDAiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDIzMCA5OC44OCBMIDIyNi41IDkxLjg4IEwgMjMwIDkzLjYzIEwgMjMzLjUgOTEuODggWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDIzMCA1MCBMIDM1OC41MyA5Ny43OCIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGZpbGw9IiMwMDAiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDM2My40NSA5OS42MSBMIDM1NS42NyAxMDAuNDUgTCAzNTguNTMgOTcuNzggTCAzNTguMTEgOTMuODkgWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IHdpZHRoPSIxMjAiIGhlaWdodD0iNDAiIHg9IjE3MCIgeT0iMTAiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwZiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDozMHB4O21hcmdpbi1sZWZ0OjE3MXB4Ij48ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5TZXF1ZW5jZTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIyMzAiIHk9IjM1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5TZXF1ZW5jZTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3Qgd2lkdGg9IjEyMCIgaGVpZ2h0PSI0MCIgeD0iMTAiIHk9IjEwMCIgZmlsbD0iI0ZGRiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMThweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjEyMHB4O21hcmdpbi1sZWZ0OjExcHgiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPk9wZW5GcmlkZ2U8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iNzAiIHk9IjEyNSIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+T3BlbkZyaWRnZTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3Qgd2lkdGg9IjEyMCIgaGVpZ2h0PSI0MCIgeD0iMTcwIiB5PSIxMDAiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxMjBweDttYXJnaW4tbGVmdDoxNzFweCI+PGRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+R3JhYkJlZXI8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMjMwIiB5PSIxMjUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkdyYWJCZWVyPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB3aWR0aD0iMTIwIiBoZWlnaHQ9IjQwIiB4PSIzMzQuNSIgeT0iMTAwIiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjExOHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTIwcHg7bWFyZ2luLWxlZnQ6MzM2cHgiPjxkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPkNsb3NlRnJpZGdlPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IHg9IjM5NSIgeT0iMTI1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5DbG9zZUZyaWRnZTwvdGV4dD48L3N3aXRjaD48L2c+PC9nPjwvc3ZnPg==)

简而言之：

* 如果一个子节点返回SUCCESS，则tick下一个子节点。
* 如果一个子节点返回FAILURE，则不再tick后续子节点，序列节点返回FAILURE。
* 如果**所有**子节点都返回SUCCESS，则序列节点返回SUCCESS。

> ⚠️ 找BUG！
>
> 如果动作**GrabBeer**失败，冰箱门会保持打开状态，因为最后一个动作**CloseFridge**被跳过了。

#### 装饰节点（Decorators）

根据[装饰节点（DecoratorNode）](https://www.behaviortree.dev/docs/nodes-library/DecoratorNode)的类型，该节点的目标可能是：

* 转换从子节点收到的结果。
* 中止子节点的执行。
* 根据装饰器类型重复tick子节点。

![简单装饰器：进入房间](https://www.behaviortree.dev/assets/images/DecoratorEnterRoom-f844716da2812873bdbb3f21448419c7.svg)

节点**Inverter**是一个装饰器，它会反转子节点返回的结果；因此，一个Inverter节点后接名为**isDoorOpen**的节点，相当于

```text
“门是关着的吗？”
```

节点**Retry**会在子节点返回FAILURE时，最多重复tick子节点**num\_attempts**次（此例中为5次）。

**显然**，左侧分支的含义是：

```text
如果门是关着的，则尝试打开它。
最多尝试5次，否则放弃并返回FAILURE。
```

但是……

找BUG！

如果**isDoorOpen**返回FAILURE，行为符合预期。但如果返回SUCCESS，左侧分支失败，整个序列被中断。

#### 第二个控制节点：Fallback

[Fallback节点](https://www.behaviortree.dev/docs/nodes-library/FallbackNode)，又称**“选择器（Selectors）”**，如其名所示，用于表达备选策略，即当子节点返回FAILURE时，下一步做什么。

它依次tick子节点：

* 如果子节点返回FAILURE，则tick下一个。
* 如果子节点返回SUCCESS，则停止tick后续子节点，Fallback节点返回SUCCESS。
* 如果所有子节点均返回FAILURE，则Fallback节点返回FAILURE。

下例展示了序列和Fallback的组合：

![Fallback节点](https://www.behaviortree.dev/assets/images/FallbackBasic-4d0eb7fe32bdcd1f2ee3d46cd27dc19f.svg)

> 门开着吗？
>
> 如果没有，尝试开门。
>
> 如果有钥匙，解锁并打开门。
>
> 否则，砸门。
>
> 如果**任意**动作成功，则进入房间。

#### “给我取瓶啤酒”示例改进

现在我们可以改进“给我取瓶啤酒”示例，避免啤酒不在冰箱时门未关闭的问题。

用绿色表示返回SUCCESS的节点，红色表示返回FAILURE的节点，黑色表示未执行的节点。


![FetchBeer failure](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnN2Zz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhodG1sPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIiBpZD0ic3ZnNTQiIHdpZHRoPSI0NjYiIGhlaWdodD0iMTUxIiBjb250ZW50PSImbHQ7bXhmaWxlIGhvc3Q9JnF1b3Q7YXBwLmRpYWdyYW1zLm5ldCZxdW90OyBtb2RpZmllZD0mcXVvdDsyMDIyLTAxLTE0VDIzOjEwOjMyLjU0N1omcXVvdDsgYWdlbnQ9JnF1b3Q7NS4wIChYMTEpJnF1b3Q7IHZlcnNpb249JnF1b3Q7MTYuMi43JnF1b3Q7IGV0YWc9JnF1b3Q7VFBEbFlLSWNJUTgzZVFVbk1BaHkmcXVvdDsgdHlwZT0mcXVvdDtnb29nbGUmcXVvdDsmZ3Q7Jmx0O2RpYWdyYW0gaWQ9JnF1b3Q7ekRUT1pJVjJIQmZvVkU5TjBDbzgmcXVvdDsmZ3Q7elZkTGM5b3dFUDQxWEJuOFJMbUdQSHBvcDUzaDBPYW8ySXV0Um1oZElZUHByNitNMXcvaFpJYTJRSEt5OTVOVzJ2Mit0VmFlQkl0MTlhaDVrWC9CRk9URW42WFZKTGliK0w0WE0yWWZOYkp2a0pzb2FvQk1pNVFtOWNCUy9BWUNaNFNXSW9XTk05RWdTaU1LRjB4UUtVaU1nM0d0Y2VkT1c2RjBkeTE0QmlOZ21YQTVScitMMU9TRWV2Rk5QL0FKUkpiVDFzeWZOd05yM2s2bVREWTVUM0UzZ0lMN1NiRFFpS1o1VzFjTGtEVjVMUytOMzhNYm8xMWdHcFE1eGNHbk1NeSt6UTFTbXlxWkNwVjkzR29zVlFxMXg4eGFxRTJPR1NvdVB5TVdGdlFzK0JPTTJaTlF2RFJvb2R5c0pZMUNKY3lQMm4wYWtmVTBHTG1yYU9XRHNXOE5aZlMrY1pwSHJmMDBIT3o5RGxicnVFSmxLQktQV2J2SnNFN0w0V2lEcFU0SW91b3pYR2RBdE1WakpyMU9IMXZZZ0d1d3U5b3BHaVEzWXV1dXpxbkNzbTVlTDRKOUlSMWUxeVQ0c0pvY1V6dlE2SzhrK2pkSjV1ZVhoRnkvb2JBcityUDJnSnFIL3JTTmdJNG9Md3Fta2J0UUV4djVIc25iQlhPUzR1R0hWWHlnc1A4T1h5Rjd2NitRb3RseVdkS2lTL2hWZ3JMUkhvdmxLclBMaFlGbHdROXA3V3dEZEZVNEptWmpOTDdBQWlYcXcyckJNNHZDNkVDaGtIS0FyMWdDU2RKUnVRVnRvSExTUFlHbXZnV1BLcnd2OE4yZ3A3V05LaCswczNEMi8vVEdJM3EvRnFBZWJMdlB6a3p3RVkxcEJDd05YeU9lK2M5QkhKK0o0TWdmSHlIaFZSbWVqeGl1YjJPM0FQcWkvSFpsK25aaFg2aUFyOHd2Ry9HN2tMaUJTNVR3T1FoallWeGZwSzVIbURYN2UyelRGUHUvZ2VEK0R3PT0mbHQ7L2RpYWdyYW0mZ3Q7Jmx0Oy9teGZpbGUmZ3Q7IiB2ZXJzaW9uPSIxLjEiIHZpZXdCb3g9Ii0wLjUgLTAuNSA0NjYgMTUxIj48bWV0YWRhdGEgaWQ9Im1ldGFkYXRhNjAiLz48ZyBpZD0iZzQ2Ij48cGF0aCBpZD0icGF0aDIiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDIzMC4yNSA0OS41IEwgMTA2LjE5IDk3LjIxIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggaWQ9InBhdGg0IiBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAxMDEuMjkgOTkuMSBMIDEwNi41NyA5My4zMiBMIDEwNi4xOSA5Ny4yMSBMIDEwOS4wOCA5OS44NSBaIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggaWQ9InBhdGg2IiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyMzAuMjUgNDkuNSBMIDIzMC4yNSA5My4xMyIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGlkPSJwYXRoOCIgZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gMjMwLjI1IDk4LjM4IEwgMjI2Ljc1IDkxLjM4IEwgMjMwLjI1IDkzLjEzIEwgMjMzLjc1IDkxLjM4IFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBpZD0icGF0aDEwIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyMzAuMjUgNDkuNSBMIDM1OC43OCA5Ny4yOCIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGlkPSJwYXRoMTIiIGZpbGw9IiMwMDAiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDM2My43IDk5LjExIEwgMzU1LjkyIDk5Ljk1IEwgMzU4Ljc4IDk3LjI4IEwgMzU4LjM2IDkzLjM5IFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCBpZD0icmVjdDE0IiB3aWR0aD0iMTIwIiBoZWlnaHQ9IjQwIiB4PSIxNzAuMjUiIHk9IjkuNSIgZmlsbD0iI2Y4Y2VjYyIgc3Ryb2tlPSIjYjg1NDUwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgaWQ9ImcyMCIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoMTgiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDozMHB4O21hcmdpbi1sZWZ0OjE3MXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5TZXF1ZW5jZTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDE2IiB4PSIyMzAiIHk9IjM1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5TZXF1ZW5jZTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3QyMiIgd2lkdGg9IjEyMCIgaGVpZ2h0PSI0MCIgeD0iMTAuMjUiIHk9Ijk5LjUiIGZpbGw9IiNkNWU4ZDQiIHN0cm9rZT0iIzgyYjM2NiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIGlkPSJnMjgiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDI2Ij48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjExOHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTIwcHg7bWFyZ2luLWxlZnQ6MTFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+T3BlbkZyaWRnZTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDI0IiB4PSI3MCIgeT0iMTI1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5PcGVuRnJpZGdlPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCBpZD0icmVjdDMwIiB3aWR0aD0iMTIwIiBoZWlnaHQ9IjQwIiB4PSIxNzAuMjUiIHk9Ijk5LjUiIGZpbGw9IiNmOGNlY2MiIHN0cm9rZT0iI2I4NTQ1MCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIGlkPSJnMzYiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDM0Ij48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjExOHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTIwcHg7bWFyZ2luLWxlZnQ6MTcxcHgiPjx4aHRtbDpkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPkdyYWJCZWVyPC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IGlkPSJ0ZXh0MzIiIHg9IjIzMCIgeT0iMTI1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5HcmFiQmVlcjwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3QzOCIgd2lkdGg9IjEyMCIgaGVpZ2h0PSI0MCIgeD0iMzM0Ljc1IiB5PSI5OS41IiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyBpZD0iZzQ0IiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoIGlkPSJzd2l0Y2g0MiI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMThweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjEyMHB4O21hcmdpbi1sZWZ0OjMzNnB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5DbG9zZUZyaWRnZTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDQwIiB4PSIzOTUiIHk9IjEyNSIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+Q2xvc2VGcmlkZ2U8L3RleHQ+PC9zd2l0Y2g+PC9nPjwvZz48L3N2Zz4=)

让我们创建一个备选的行为树，即使**GrabBeer**返回FAILURE，也会关闭冰箱门。

![取啤酒失败](https://www.behaviortree.dev/assets/images/FetchBeer-24723226326a39f057f9d9c32250eb95.svg)

这两棵树最终都会关闭冰箱门，但：

* **左侧**的树无论是否成功取到啤酒，都会返回SUCCESS。
* **右侧**的树如果啤酒在冰箱里则返回SUCCESS，否则返回FAILURE。

当**GrabBeer**返回SUCCESS时，所有行为均符合预期。

![取啤酒成功](https://www.behaviortree.dev/assets/images/FetchBeer2-318ea4fa8928e30e7c9dd80e35dfc212.svg)

## 主要概念

**BehaviorTree.CPP**是一个C++库，可以轻松集成到你喜欢的分布式中间件中，如**ROS**或**SmartSoft**。

你也可以将其静态链接进你的应用程序（例如游戏）。

以下是你需要首先理解的主要概念。

### 节点与树

用户必须自己创建ActionNodes和ConditionNodes（叶节点）；该库帮助你轻松将它们组合成树。

将叶节点看作构建复杂系统所需的构建模块。如果节点是**乐高积木**，那么你的树就是乐高套装。

![乐高积木](https://www.behaviortree.dev/assets/images/lego-ba9dde407ed40b8f4d59df6eb17f4b19.jpg)

按定义，你的自定义节点应当是高度**可重用**的。

### 使用XML格式在运行时实例化树

尽管库用C++编写，树本身可以在*运行时*，更准确地说是在*部署时*，使用基于XML的脚本语言创建和组合。

XML格式在[这里](https://www.behaviortree.dev/docs/learn-the-basics/xml_format)有详细描述，但学习语法的最好方法是跟随教程。

### tick()回调

任何TreeNode都可以视为调用**回调函数**的机制，即**执行一段代码**。回调做什么由你决定。

在大多数后续教程中，我们的动作仅在控制台打印信息或休眠一段时间以模拟长时间计算。

在生产代码中，尤其是模型驱动开发和基于组件的软件工程中，动作/条件通常会与系统的其他*组件*或*服务*通信。

```cpp
// 你可以封装进BT动作的最简单回调
NodeStatus HelloTick()
{
  std::cout << "Hello World\n"; 
  return NodeStatus::SUCCESS;
}

// 允许库创建调用HelloTick()的动作节点
factory.registerSimpleAction("Hello", std::bind(HelloTick));
```

> 💡 提示
>
> 工厂可能创建多个名为**Hello**的节点实例。

### 通过继承创建自定义节点

上述示例通过**函数指针**（依赖注入）创建了调用`HelloTick`的特定类型TreeNode。

通常，定义自定义TreeNode应继承自类`TreeNode`或其派生类：

* `ActionNodeBase`
* `ConditionNode`
* `DecoratorNode`

参考请见[第一个教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_01_first_tree)。

### 数据流、端口与黑板

端口的详细介绍见[第二](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_02_basic_ports)和[第三](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_03_generic_ports)教程。

目前需了解：

* **黑板（Blackboard）**是所有树节点共享的*键/值*存储。
* **端口（Ports）**是节点间交换信息的机制。
* 端口通过黑板中的相同*键*进行“连接”。
* 节点的端口数量、名称和类型须在*编译时*（C++）确定；端口间连接在*部署时*（XML）完成。
* 端口值可存储任意C++类型（我们使用类似于[std::any](https://www.fluentcpp.com/2021/02/05/how-stdany-works/)的类型擦除技术）。

## XML模式

在[第一个教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_01_first_tree)中，展示了这棵简单的树。

```XML
 <root BTCPP_format="4">
     <BehaviorTree ID="MainTree">
        <Sequence name="root_sequence">
            <SaySomething   name="action_hello" message="Hello"/>
            <OpenGripper    name="open_gripper"/>
            <ApproachObject name="approach_object"/>
            <CloseGripper   name="close_gripper"/>
        </Sequence>
     </BehaviorTree>
 </root>
```

你可能注意到：

* 树的第一个标签是 `<root>`，它应包含**一个或多个** `<BehaviorTree>` 标签。
* `<BehaviorTree>` 标签应带有属性 `[ID]`。
* `<root>` 标签应包含属性 `[BTCPP_format]`。
* 每个TreeNode用单个标签表示，具体来说：

  * 标签名是用于在工厂中注册该TreeNode的**ID**。
  * 属性 `[name]` 是实例的名称，**可选**。
  * 端口通过属性配置。在前例中，动作 `SaySomething` 需要输入端口 `message`。
* 关于子节点数量：

  * `ControlNodes` 含有 **1 到 N 个子节点**。
  * `DecoratorNodes` 和子树仅含**1 个子节点**。
  * `ActionNodes` 和 `ConditionNodes` 没有子节点。

### 端口重映射与指向黑板条目的指针

如[第二教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_02_basic_ports)所述，输入/输出端口可以通过黑板中条目的名称进行重映射，换言之，就是黑板中**键/值**对的**键**。

黑板键用如下语法表示：`{key_name}`。

下例中：

* 序列的第一个子节点打印“Hello”，
* 第二个子节点读写黑板中名为 `"my_message"` 条目的值；

```XML
 <root BTCPP_format="4" >
     <BehaviorTree ID="MainTree">
        <Sequence name="root_sequence">
            <SaySomething message="Hello"/>
            <SaySomething message="{my_message}"/>
        </Sequence>
     </BehaviorTree>
 </root>
```

### 紧凑与显式表示

以下两种写法均有效：

```XML
 <SaySomething               name="action_hello" message="Hello World"/>
 <Action ID="SaySomething"   name="action_hello" message="Hello World"/>
```

我们称前者为“**紧凑**”语法，后者为“**显式**”语法。显式语法表示的第一个示例为：

```XML
 <root BTCPP_format="4" >
     <BehaviorTree ID="MainTree">
        <Sequence name="root_sequence">
           <Action ID="SaySomething"   name="action_hello" message="Hello"/>
           <Action ID="OpenGripper"    name="open_gripper"/>
           <Action ID="ApproachObject" name="approach_object"/>
           <Action ID="CloseGripper"   name="close_gripper"/>
        </Sequence>
     </BehaviorTree>
 </root>
```

尽管紧凑语法更便捷易写，但提供的信息较少。像**Groot**这类工具要求使用*显式*语法或额外信息。该信息可用 `<TreeNodeModel>` 标签添加。

为使紧凑版树与Groot兼容，XML应修改为：

```XML
 <root BTCPP_format="4" >
     <BehaviorTree ID="MainTree">
        <Sequence name="root_sequence">
           <SaySomething   name="action_hello" message="Hello"/>
           <OpenGripper    name="open_gripper"/>
           <ApproachObject name="approach_object"/>
           <CloseGripper   name="close_gripper"/>
        </Sequence>
    </BehaviorTree>
    
    <!-- BT执行器不需要，但Groot需要 -->     
    <TreeNodeModel>
        <Action ID="SaySomething">
            <input_port name="message" type="std::string" />
        </Action>
        <Action ID="OpenGripper"/>
        <Action ID="ApproachObject"/>
        <Action ID="CloseGripper"/>      
    </TreeNodeModel>
 </root>
```

### 子树

如[此教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_06_subtree_ports)所示，可在一棵树中包含子树，避免重复复制相同树，降低复杂度。

假设想将若干动作封装进行为树“**GraspObject**”（为简洁，省略可选属性\[name]）。

```XML
 <root BTCPP_format="4" >
 
     <BehaviorTree ID="MainTree">
        <Sequence>
           <Action  ID="SaySomething"  message="Hello World"/>
           <SubTree ID="GraspObject"/>
        </Sequence>
     </BehaviorTree>
     
     <BehaviorTree ID="GraspObject">
        <Sequence>
           <Action ID="OpenGripper"/>
           <Action ID="ApproachObject"/>
           <Action ID="CloseGripper"/>
        </Sequence>
     </BehaviorTree>  
 </root>
```

可见，“GraspObject”树在“SaySomething”之后执行。

### 引入外部文件

**自版本2.4起**。

你可以像C++中的`#include <file>`一样引入外部文件，方法是使用标签：

```XML
  <include path="relative_or_absolute_path_to_file">
```

利用上例，我们可将两棵行为树拆分成两个文件：

```XML
 <!-- 文件 maintree.xml -->

 <root BTCPP_format="4" >
     
     <include path="grasp.xml"/>
     
     <BehaviorTree ID="MainTree">
        <Sequence>
           <Action  ID="SaySomething"  message="Hello World"/>
           <SubTree ID="GraspObject"/>
        </Sequence>
     </BehaviorTree>
  </root>
```

```XML
 <!-- 文件 grasp.xml -->

 <root BTCPP_format="4" >
     <BehaviorTree ID="GraspObject">
        <Sequence>
           <Action ID="OpenGripper"/>
           <Action ID="ApproachObject"/>
           <Action ID="CloseGripper"/>
        </Sequence>
     </BehaviorTree>  
 </root>
```

> ℹ️ 注
>
> ROS用户注意：若想在[ROS包](http://wiki.ros.org/Packages)内查找文件，可用此语法：

```XML
<include ros_pkg="name_package"  path="path_relative_to_pkg/grasp.xml"/>
```

# 教程（基础）

5分钟掌握最重要的Docusaurus概念。

## 01. 你的第一个行为树

行为树，类似状态机，不过是一种在合适的时间和条件下调用**回调函数**的机制。这些回调内部具体做什么由你决定。

我们将把**“调用回调函数”**和**“tick”**这两个表达交替使用。

在本系列教程中，大多数情况下我们的示例动作只是简单地在控制台打印信息，但请记住，真正的“生产”代码可能会更复杂。

接下来，我们将创建这棵简单的树：


![Tutorial1](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnN2Zz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhodG1sPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIiBpZD0ic3ZnNjYiIHdpZHRoPSI2NTEiIGhlaWdodD0iMTYxIiB2ZXJzaW9uPSIxLjEiIHZpZXdCb3g9Ii0wLjUgLTAuNSA2NTEgMTYxIj48bWV0YWRhdGEgaWQ9Im1ldGFkYXRhNzIiLz48ZyBpZD0iZzU4Ij48cGF0aCBpZD0icGF0aDIiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0ibSAzNTAsNTAgLTY1LjE3LDU1Ljg2IiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIiBzdHlsZT0iZmlsbDpub25lO3N0cm9rZTojMDAwO3N0cm9rZS1taXRlcmxpbWl0OjEwIi8+PHBhdGggaWQ9InBhdGg0IiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Im0gMjgwLjg1LDEwOS4yNyAzLjA0LC03LjIxIDAuOTQsMy44IDMuNjEsMS41MSB6IiBwb2ludGVyLWV2ZW50cz0iYWxsIiBzdHlsZT0iZmlsbDojMDAwO3N0cm9rZTojMDAwO3N0cm9rZS1taXRlcmxpbWl0OjEwIi8+PHBhdGggaWQ9InBhdGg2IiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Im0gMzUwLDUwIDYwLjMyLDU1LjY4IiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIiBzdHlsZT0iZmlsbDpub25lO3N0cm9rZTojMDAwO3N0cm9rZS1taXRlcmxpbWl0OjEwIi8+PHBhdGggaWQ9InBhdGg4IiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Im0gNDE0LjE4LDEwOS4yNCAtNy41MiwtMi4xNyAzLjY2LC0xLjM5IDEuMDksLTMuNzYgeiIgcG9pbnRlci1ldmVudHM9ImFsbCIgc3R5bGU9ImZpbGw6IzAwMDtzdHJva2U6IzAwMDtzdHJva2UtbWl0ZXJsaW1pdDoxMCIvPjxwYXRoIGlkPSJwYXRoMTAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0ibSAzNTAsNTAgMTkzLjksNTguMTciIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiIHN0eWxlPSJmaWxsOm5vbmU7c3Ryb2tlOiMwMDA7c3Ryb2tlLW1pdGVybGltaXQ6MTAiLz48cGF0aCBpZD0icGF0aDEyIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Im0gNTQ4LjkzLDEwOS42OCAtNy43MSwxLjM0IDIuNjgsLTIuODUgLTAuNjcsLTMuODYgeiIgcG9pbnRlci1ldmVudHM9ImFsbCIgc3R5bGU9ImZpbGw6IzAwMDtzdHJva2U6IzAwMDtzdHJva2UtbWl0ZXJsaW1pdDoxMCIvPjxwYXRoIGlkPSJwYXRoMTQiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAzNTAsNTAgOTEuMjEsMTA4LjU5IiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIiBzdHlsZT0iZmlsbDpub25lO3N0cm9rZTojMDAwO3N0cm9rZS1taXRlcmxpbWl0OjEwIi8+PHBhdGggaWQ9InBhdGgxNiIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJtIDg2LjA5LDEwOS43NSA2LjA1LC00Ljk2IC0wLjkzLDMuOCAyLjQ4LDMuMDMgeiIgcG9pbnRlci1ldmVudHM9ImFsbCIgc3R5bGU9ImZpbGw6IzAwMDtzdHJva2U6IzAwMDtzdHJva2UtbWl0ZXJsaW1pdDoxMCIvPjxyZWN0IGlkPSJyZWN0MTgiIHdpZHRoPSIxMjAiIGhlaWdodD0iNDAiIHg9IjI5MCIgeT0iMTAiIHBvaW50ZXItZXZlbnRzPSJhbGwiIHN0eWxlPSJmaWxsOiNmZmY7c3Ryb2tlOiMwMDAiLz48ZyBpZD0iZzI0IiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41LC0wLjUpIj48c3dpdGNoIGlkPSJzd2l0Y2gyMiI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMThweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjMwcHg7bWFyZ2luLWxlZnQ6MjkxcHgiPjx4aHRtbDpkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPlNlcXVlbmNlPC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IGlkPSJ0ZXh0MjAiIHg9IjM1MCIgeT0iMzUiIGZvbnQtc2l6ZT0iMTgiIHN0eWxlPSJmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7dGV4dC1hbmNob3I6bWlkZGxlO2ZpbGw6IzAwMCI+U2VxdWVuY2U8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IGlkPSJyZWN0MjYiIHdpZHRoPSIxMjAiIGhlaWdodD0iNDAiIHg9IjE5MCIgeT0iMTEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIiBzdHlsZT0iZmlsbDojZmZmO3N0cm9rZTojMDAwIi8+PGcgaWQ9ImczMiIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSwtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoMzAiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxMzBweDttYXJnaW4tbGVmdDoxOTFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+T3BlbkdyaXBwZXI8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQyOCIgeD0iMjUwIiB5PSIxMzUiIGZvbnQtc2l6ZT0iMTgiIHN0eWxlPSJmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7dGV4dC1hbmNob3I6bWlkZGxlO2ZpbGw6IzAwMCI+T3BlbkdyaXBwZXI8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IGlkPSJyZWN0MzQiIHdpZHRoPSIxNTAiIGhlaWdodD0iNDAiIHg9IjM0MCIgeT0iMTEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIiBzdHlsZT0iZmlsbDojZmZmO3N0cm9rZTojMDAwIi8+PGcgaWQ9Imc0MCIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSwtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoMzgiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTQ4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxMzBweDttYXJnaW4tbGVmdDozNDFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+QXBwcm9hY2hPYmplY3Q8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQzNiIgeD0iNDE1IiB5PSIxMzUiIGZvbnQtc2l6ZT0iMTgiIHN0eWxlPSJmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7dGV4dC1hbmNob3I6bWlkZGxlO2ZpbGw6IzAwMCI+QXBwcm9hY2hPYmplY3Q8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IGlkPSJyZWN0NDIiIHdpZHRoPSIxMjAiIGhlaWdodD0iNDAiIHg9IjUyMCIgeT0iMTEwIiBwb2ludGVyLWV2ZW50cz0iYWxsIiBzdHlsZT0iZmlsbDojZmZmO3N0cm9rZTojMDAwIi8+PGcgaWQ9Imc0OCIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSwtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoNDYiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxMzBweDttYXJnaW4tbGVmdDo1MjFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+Q2xvc2VHcmlwcGVyPC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IGlkPSJ0ZXh0NDQiIHg9IjU4MCIgeT0iMTM1IiBmb250LXNpemU9IjE4IiBzdHlsZT0iZm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO3RleHQtYW5jaG9yOm1pZGRsZTtmaWxsOiMwMDAiPkNsb3NlR3JpcHBlcjwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3Q1MCIgd2lkdGg9IjE1MCIgaGVpZ2h0PSI0MCIgeD0iMTAiIHk9IjExMCIgcG9pbnRlci1ldmVudHM9ImFsbCIgcng9IjE0LjQiIHJ5PSIxNC40IiBzdHlsZT0iZmlsbDojZmZmO3N0cm9rZTojMDAwIi8+PGcgaWQ9Imc1NiIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSwtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoNTQiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTQ4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxMzBweDttYXJnaW4tbGVmdDoxMXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5DaGVja0JhdHRlcnk8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQ1MiIgeD0iODUiIHk9IjEzNSIgZm9udC1zaXplPSIxOCIgc3R5bGU9ImZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTt0ZXh0LWFuY2hvcjptaWRkbGU7ZmlsbDojMDAwIj5DaGVja0JhdHRlcnk8L3RleHQ+PC9zd2l0Y2g+PC9nPjwvZz48L3N2Zz4=)

### 如何创建你自己的动作节点（ActionNodes）

创建TreeNode的默认（且推荐）方式是通过继承。

```cpp
// 自定义同步动作节点（SyncActionNode）示例（无端口）
class ApproachObject : public BT::SyncActionNode
{
public:
  ApproachObject(const std::string& name) :
      BT::SyncActionNode(name, {})
  {}

  // 必须重写虚函数 tick()
  BT::NodeStatus tick() override
  {
    std::cout << "ApproachObject: " << this->name() << std::endl;
    return BT::NodeStatus::SUCCESS;
  }
};
```

如你所见：

* 每个TreeNode实例都有一个`name`，用于人类可读标识，**不要求唯一**。
* **tick()**方法是实际动作执行的地方，必须返回`NodeStatus`，即RUNNING、SUCCESS或FAILURE。

另一种方法是使用**依赖注入**，通过函数指针（即“函数对象”）创建TreeNode。

函数对象必须符合签名：

```cpp
BT::NodeStatus myFunction(BT::TreeNode& self) 
```

示例：

```cpp
using namespace BT;

// 简单函数，返回NodeStatus
BT::NodeStatus CheckBattery()
{
  std::cout << "[ Battery: OK ]" << std::endl;
  return BT::NodeStatus::SUCCESS;
}

// 封装open()和close()方法的动作节点示例
class GripperInterface
{
public:
  GripperInterface(): _open(true) {}
    
  NodeStatus open() 
  {
    _open = true;
    std::cout << "GripperInterface::open" << std::endl;
    return NodeStatus::SUCCESS;
  }

  NodeStatus close() 
  {
    std::cout << "GripperInterface::close" << std::endl;
    _open = false;
    return NodeStatus::SUCCESS;
  }

private:
  bool _open; // 共享状态
};
```

我们可以基于以下函数构建`SimpleActionNode`：

* CheckBattery()
* GripperInterface::open()
* GripperInterface::close()

### 使用XML动态创建行为树

假设有如下名为 **my\_tree.xml** 的XML文件：

```xml
 <root BTCPP_format="4" >
     <BehaviorTree ID="MainTree">
        <Sequence name="root_sequence">
            <CheckBattery   name="check_battery"/>
            <OpenGripper    name="open_gripper"/>
            <ApproachObject name="approach_object"/>
            <CloseGripper   name="close_gripper"/>
        </Sequence>
     </BehaviorTree>
 </root>
```

> 💡 提示
> 更多关于XML格式的细节请参见[这里](https://www.behaviortree.dev/docs/learn-the-basics/xml_format)。

首先，你必须将自定义TreeNodes注册到`BehaviorTreeFactory`，然后从文件或文本加载XML。

XML中使用的标识符必须与注册时使用的名称一致。

属性“name”表示实例名，**可选**。

```cpp
#include "behaviortree_cpp/bt_factory.h"

// 自定义节点定义文件
#include "dummy_nodes.h"
using namespace DummyNodes;

int main()
{
    // 使用BehaviorTreeFactory注册自定义节点
  BehaviorTreeFactory factory;

  // 推荐的节点创建方式是通过继承
  factory.registerNodeType<ApproachObject>("ApproachObject");

  // 通过函数指针注册简单条件节点
  // 也可用C++11 lambda或std::bind
  factory.registerSimpleCondition("CheckBattery", [&](TreeNode&) { return CheckBattery(); });

  // 也可用类成员方法创建简单动作节点
  GripperInterface gripper;
  factory.registerSimpleAction("OpenGripper", [&](TreeNode&){ return gripper.open(); } );
  factory.registerSimpleAction("CloseGripper", [&](TreeNode&){ return gripper.close(); } );

  // 树在部署时（运行时，但仅初始化时）创建
  // 注意：当tree对象超出作用域，所有TreeNodes都会被销毁
   auto tree = factory.createTreeFromFile("./my_tree.xml");

  // “执行”树需要tick它
  // tick信号根据树逻辑传播到子节点
  // 此例中序列的所有子节点均返回SUCCESS，序列完整执行
  tree.tickWhileRunning();

  return 0;
}

/* 预期输出：
*
  [ Battery: OK ]
  GripperInterface::open
  ApproachObject: approach_object
  GripperInterface::close
*/
```

## 02. 黑板（Blackboard）与端口

如前所述，自定义TreeNodes可以执行任意简单或复杂的软件片段。目标是提供更高层次的抽象接口。

因此，它们在概念上类似于**函数**。

类似函数，我们通常希望：

* 向节点传递参数（**输入**）
* 从节点获得信息（**输出**）
* 一个节点的输出可以作为另一个节点的输入

BehaviorTree.CPP提供了通过**端口（ports）**实现的基础**数据流**机制，使用简单，灵活且类型安全。

本教程将创建如下树：

![教程2](https://www.behaviortree.dev/assets/images/tutorial_blackboard-c21790e0c974974f0f3eca9efeb4b765.svg)

> 💡 主要概念
>
> * “黑板”是所有树节点共享的简单**键/值存储**。
> * 黑板的“条目”是**键/值对**。
> * **输入端口**可读取黑板条目，**输出端口**可写入黑板条目。

### 输入端口

有效的输入可以是：

* 节点直接读取和解析的**静态字符串**，或
* 指向黑板中某条目的“指针”，由**键**标识。

假设我们创建一个动作节点`SaySomething`，用于打印给定字符串到`std::cout`。

传递字符串用名为**message**的输入端口。

以下是两种XML语法示例：

```xml
<SaySomething name="first"   message="hello world" />
<SaySomething name="second" message="{greetings}" />
```

* **第一个**节点端口接收字符串“hello world”；
* **第二个**节点则从黑板中查找名为“greetings”的条目值。

> ⚠️ 注意
> 条目“greetings”的值可能（且很可能会）在运行时改变。

动作节点`SaySomething`可实现如下：

```cpp
// 带输入端口的同步动作节点
class SaySomething : public SyncActionNode
{
public:
  // 有端口的节点需使用此构造函数签名
  SaySomething(const std::string& name, const NodeConfig& config)
    : SyncActionNode(name, config)
  { }

  // 必须定义此静态方法
  static PortsList providedPorts()
  {
    // 该动作只有一个名为"message"的输入端口
    return { InputPort<std::string>("message") };
  }

  // 重写tick()
  NodeStatus tick() override
  {
    Expected<std::string> msg = getInput<std::string>("message");
    // 校验输入是否有效，否则抛出异常
    if (!msg)
    {
      throw BT::RuntimeError("missing required input [message]: ", 
                              msg.error() );
    }
    // 使用value()获取有效数据
    std::cout << "Robot says: " << msg.value() << std::endl;
    return NodeStatus::SUCCESS;
  }
};
```

当自定义TreeNode有输入和/或输出端口时，必须在**静态**方法中声明：

```cpp
static MyCustomNode::PortsList providedPorts();
```


端口 `message` 的输入可以通过模板方法 `TreeNode::getInput<T>(key)` 读取。

该方法可能因多种原因失败，用户需自行检查返回值的有效性，并决定如何处理：

* 返回 `NodeStatus::FAILURE`？
* 抛出异常？
* 使用不同的默认值？

> ⚠️ 重要提示
>
> 建议**始终**在 `tick()` 内调用 `getInput()` 方法，**不要**在类的构造函数中调用。
>
> C++代码应预期输入的实际值会**在运行时变化**，因此应定期更新。

### 输出端口

指向黑板条目的输入端口只有在另一个节点已写入该条目内容时才有效。

`ThinkWhatToSay` 是一个使用**输出端口**向条目写入字符串的节点示例。

```cpp
class ThinkWhatToSay : public SyncActionNode
{
public:
  ThinkWhatToSay(const std::string& name, const NodeConfig& config)
    : SyncActionNode(name, config)
  { }

  static PortsList providedPorts()
  {
    return { OutputPort<std::string>("text") };
  }

  // 该动作向端口"text"写入值
  NodeStatus tick() override
  {
    // 输出可能每次tick()变化，此处简化为固定值
    setOutput("text", "The answer is 42" );
    return NodeStatus::SUCCESS;
  }
};
```

通常，调试时也可以用内置动作 `Script` 向条目写入静态值：

```xml
<Script code=" the_answer:='The answer is 42' " />
```

我们将在关于[BT.CPP新脚本语言](https://www.behaviortree.dev/docs/guides/scripting)的教程中进一步讲解动作**Script**。

> 💡 提示
>
> 若你从BT.CPP 3.X迁移，**Script**可视为不需改动的替代品，替代已不推荐使用的**SetBlackboard**。

### 完整示例

此示例执行3个动作组成的序列：

* 动作1从静态字符串读取输入`message`。
* 动作2向黑板条目`the_answer`写入内容。
* 动作3从黑板条目`the_answer`读取输入`message`。

```xml
<root BTCPP_format="4" >
    <BehaviorTree ID="MainTree">
       <Sequence name="root_sequence">
           <SaySomething     message="hello" />
           <ThinkWhatToSay   text="{the_answer}"/>
           <SaySomething     message="{the_answer}" />
       </Sequence>
    </BehaviorTree>
</root>
```

注册并执行该树的C++代码：

```cpp
#include "behaviortree_cpp/bt_factory.h"

// 自定义节点定义文件
#include "dummy_nodes.h"
using namespace DummyNodes;

int main()
{  
  BehaviorTreeFactory factory;
  factory.registerNodeType<SaySomething>("SaySomething");
  factory.registerNodeType<ThinkWhatToSay>("ThinkWhatToSay");

  auto tree = factory.createTreeFromFile("./my_tree.xml");
  tree.tickWhileRunning();
  return 0;
}

/* 预期输出：
  Robot says: hello
  Robot says: The answer is 42
*/
```

我们通过相同的键（`the_answer`）将输出端口连接到输入端口，即它们“指向”黑板的同一条目。

只有当端口类型相同（此例为`std::string`）时，才可连接端口。若尝试连接不同类型端口，`factory.createTreeFromFile` 会抛出异常。


## 03. 使用泛型端口

在之前的教程中，我们介绍了输入和输出端口，端口类型是 `std::string`。

接下来，我们将展示如何为端口指定泛型的C++类型。

### 字符串解析

**BehaviorTree.CPP** 支持自动将字符串转换为常用类型，如 `int`、`long`、`double`、`bool`、`NodeStatus` 等，用户自定义类型也能轻松支持。

例如：

```cpp
// 自定义类型
struct Position2D 
{ 
  double x;
  double y; 
};
```

为了让XML加载器能从字符串实例化 `Position2D`，需要提供模板特化 `BT::convertFromString<Position2D>(StringView)`。

如何将 `Position2D` 序列化为字符串由你决定；此处用分号分隔两个数字。

```cpp
// 将字符串转换为Position2D的模板特化
namespace BT
{
    template <> inline Position2D convertFromString(StringView str)
    {
        // 期望实数，用分号分割
        auto parts = splitString(str, ';');
        if (parts.size() != 2)
        {
            throw RuntimeError("invalid input)");
        }
        else
        {
            Position2D output;
            output.x     = convertFromString<double>(parts[0]);
            output.y     = convertFromString<double>(parts[1]);
            return output;
        }
    }
} // namespace BT
```

* `StringView` 是C++11版本的[std::string\_view](https://en.cppreference.com/w/cpp/header/string_view)，可传入 `std::string` 或 `const char*`。
* 库提供了简单的 `splitString` 函数，也可以用其他如 [boost::algorithm::split](https://www.boost.org/doc/libs/1_80_0/doc/html/boost/algorithm/split.html)。
* 我们使用了特化的 `convertFromString<double>()`。

### 示例

同前教程，我们创建两个自定义动作，一个写端口，一个读端口。

```cpp
class CalculateGoal: public SyncActionNode
{
  public:
    CalculateGoal(const std::string& name, const NodeConfig& config):
      SyncActionNode(name,config)
    {}

    static PortsList providedPorts()
    {
      return { OutputPort<Position2D>("goal") };
    }

    NodeStatus tick() override
    {
      Position2D mygoal = {1.1, 2.3};
      setOutput<Position2D>("goal", mygoal);
      return NodeStatus::SUCCESS;
    }
};

class PrintTarget: public SyncActionNode
{
  public:
    PrintTarget(const std::string& name, const NodeConfig& config):
        SyncActionNode(name,config)
    {}

    static PortsList providedPorts()
    {
      // 端口可带有人类可读描述
      const char*  description = "Simply print the goal on console...";
      return { InputPort<Position2D>("target", description) };
    }
      
    NodeStatus tick() override
    {
      auto res = getInput<Position2D>("target");
      if( !res )
      {
        throw RuntimeError("error reading port [target]:", res.error());
      }
      Position2D target = res.value();
      printf("Target positions: [ %.1f, %.1f ]\n", target.x, target.y );
      return NodeStatus::SUCCESS;
    }
};
```

现在可以像往常一样连接输入输出端口，指向黑板同一条目。

下面的树是4个动作组成的序列：

* 使用动作 `CalculateGoal` 向黑板条目 **GoalPosition** 存储 `Position2D` 值。
* 调用 `PrintTarget`，其输入“target”从黑板条目 **GoalPosition** 读取。
* 用内置动作 `Script` 给键 **OtherGoal** 赋值字符串 `"-1;3"`，字符串到 `Position2D` 的转换自动完成。
* 再次调用 `PrintTarget`，输入“target”从条目 **OtherGoal** 读取。

```cpp
static const char* xml_text = R"(

 <root BTCPP_format="4" >
     <BehaviorTree ID="MainTree">
        <Sequence name="root">
            <CalculateGoal goal="{GoalPosition}" />
            <PrintTarget   target="{GoalPosition}" />
            <Script        code=" OtherGoal:='-1;3' " />
            <PrintTarget   target="{OtherGoal}" />
        </Sequence>
     </BehaviorTree>
 </root>
 )";

int main()
{
  BT::BehaviorTreeFactory factory;
  factory.registerNodeType<CalculateGoal>("CalculateGoal");
  factory.registerNodeType<PrintTarget>("PrintTarget");

  auto tree = factory.createTreeFromText(xml_text);
  tree.tickWhileRunning();

  return 0;
}
/* 预期输出：

    Target positions: [ 1.1, 2.3 ]
    Converting string: "-1;3"
    Target positions: [ -1.0, 3.0 ]
*/
```

## 04. 响应式行为

下例展示了 `SequenceNode` 与 `ReactiveSequence` 的区别。

我们将实现一个**异步动作**，即完成需要较长时间，且未满足完成条件时返回RUNNING的动作。

异步动作需满足：

* `tick()` 方法不应阻塞过久，执行流应尽快返回。
* 当调用 `halt()` 方法时，应尽快中止。

> ⚠️ 注意
>
> **深入了解异步动作**
>
> 用户应充分理解BT.CPP中并发机制，并学习如何开发异步动作的最佳实践。详见[这里](https://www.behaviortree.dev/docs/guides/asynchronous_nodes)。

### StatefulActionNode

**StatefulActionNode** 是实现异步动作的推荐方式。

特别适用于包含**请求-应答模式**的代码，即动作向另一进程发送异步请求，并周期性检查是否收到回复。

根据回复可返回SUCCESS或FAILURE。

如果不是与外部进程通信，而是执行耗时计算，可考虑分块执行或移至其他线程（详见[AsyncThreadedAction](https://www.behaviortree.dev/docs/guides/asynchronous_nodes)教程）。

继承自**StatefulActionNode**的类须重写以下虚函数，替代`tick()`：

* `NodeStatus onStart()`：节点处于IDLE时调用。可立即返回成功、失败或RUNNING。若返回RUNNING，下次tick时执行`onRunning`。
* `NodeStatus onRunning()`：节点处于RUNNING时调用，返回最新状态。
* `void onHalted()`：节点被树中其他节点中止时调用。

示例：创建虚拟节点 **MoveBaseAction**：

```cpp
// 自定义类型
struct Pose2D
{
    double x, y, theta;
};

namespace chr = std::chrono;

class MoveBaseAction : public BT::StatefulActionNode
{
  public:
    // 含端口的TreeNode必须有此构造函数签名
    MoveBaseAction(const std::string& name, const BT::NodeConfig& config)
      : StatefulActionNode(name, config)
    {}

    // 必须定义此静态方法
    static BT::PortsList providedPorts()
    {
        return{ BT::InputPort<Pose2D>("goal") };
    }

    // 初始调用一次
    BT::NodeStatus onStart() override;

    // 若onStart()返回RUNNING，则持续调用此方法直到返回非RUNNING状态
    BT::NodeStatus onRunning() override;

    // 节点被中止时调用
    void onHalted() override;

  private:
    Pose2D _goal;
    chr::system_clock::time_point _completion_time;
};

//-------------------------

BT::NodeStatus MoveBaseAction::onStart()
{
  if ( !getInput<Pose2D>("goal", _goal))
  {
    throw BT::RuntimeError("missing required input [goal]");
  }
  printf("[ MoveBase: SEND REQUEST ]. goal: x=%f y=%f theta=%f\n",
         _goal.x, _goal.y, _goal.theta);

  // 用计时器模拟耗时动作（200毫秒）
  _completion_time = chr::system_clock::now() + chr::milliseconds(220);

  return BT::NodeStatus::RUNNING;
}

BT::NodeStatus MoveBaseAction::onRunning()
{
  // 模拟检查回复是否已收到，注意不要阻塞太久
  std::this_thread::sleep_for(chr::milliseconds(10));

  // 模拟操作完成的时间点
  if(chr::system_clock::now() >= _completion_time)
  {
    std::cout << "[ MoveBase: FINISHED ]" << std::endl;
    return BT::NodeStatus::SUCCESS;
  }
  return BT::NodeStatus::RUNNING;
}

void MoveBaseAction::onHalted()
{
  printf("[ MoveBase: ABORTED ]");
}
```


### Sequence 与 ReactiveSequence 的区别

下面的示例使用简单的 `SequenceNode`。

```xml
 <root BTCPP_format="4">
     <BehaviorTree>
        <Sequence>
            <BatteryOK/>
            <SaySomething   message="mission started..." />
            <MoveBase           goal="1;2;3"/>
            <SaySomething   message="mission completed!" />
        </Sequence>
     </BehaviorTree>
 </root>
```

```cpp
int main()
{
  BT::BehaviorTreeFactory factory;
  factory.registerSimpleCondition("BatteryOK", std::bind(CheckBattery));
  factory.registerNodeType<MoveBaseAction>("MoveBase");
  factory.registerNodeType<SaySomething>("SaySomething");

  auto tree = factory.createTreeFromText(xml_text);
 
  // 这里我们不用 tree.tickWhileRunning()
  // 而是用自己的循环
  std::cout << "--- ticking\n";
  auto status = tree.tickOnce();
  std::cout << "--- status: " << toStr(status) << "\n\n";

  while(status == NodeStatus::RUNNING) 
  {
    // 避免忙等，睡眠一段时间
    // 请不要用其他sleep函数！
    // 这里用较长时间只是为了控制输出数量
    tree.sleep(std::chrono::milliseconds(100));

    std::cout << "--- ticking\n";
    status = tree.tickOnce();
    std::cout << "--- status: " << toStr(status) << "\n\n";
  }

  return 0;
}
```

预期输出：

```text
--- ticking
[ Battery: OK ]
Robot says: mission started...
[ MoveBase: SEND REQUEST ]. goal: x=1.0 y=2.0 theta=3.0
--- status: RUNNING

--- ticking
--- status: RUNNING

--- ticking
[ MoveBase: FINISHED ]
Robot says: mission completed!
--- status: SUCCESS
```

你会注意到，`executeTick()` 第1和第2次调用时 `MoveBase` 返回 **RUNNING**，第3次返回 **SUCCESS**。

`BatteryOK` 只执行一次。

若使用 `ReactiveSequence`，当子节点 `MoveBase` 返回 RUNNING 时，序列会重新开始，条件 `BatteryOK` 会**再次**执行。

若任意时刻 `BatteryOK` 返回 **FAILURE**，`MoveBase` 动作将被*中断*（即`halted`）。

```xml
 <root>
     <BehaviorTree>
        <ReactiveSequence>
            <BatteryOK/>
            <Sequence>
                <SaySomething   message="mission started..." />
                <MoveBase           goal="1;2;3"/>
                <SaySomething   message="mission completed!" />
            </Sequence>
        </ReactiveSequence>
     </BehaviorTree>
 </root>
```

预期输出：

```text
--- ticking
[ Battery: OK ]
Robot says: mission started...
[ MoveBase: SEND REQUEST ]. goal: x=1.0 y=2.0 theta=3.0
--- status: RUNNING

--- ticking
[ Battery: OK ]
--- status: RUNNING

--- ticking
[ Battery: OK ]
[ MoveBase: FINISHED ]
Robot says: mission completed!
--- status: SUCCESS
```

### 事件驱动树？

> 💡 提示
> 我们使用 `tree.sleep()` 而非 `std::this_thread::sleep_for()` 是有原因的！
>
> 应优先使用 `Tree::sleep()`，因为当树中某节点调用 `TreeNode::emitStateChanged()` 时，`Tree::sleep()` 可被打断。

## 05. 使用子树（SubTrees）

通过将小而可复用的行为插入到更大行为中，可构建大规模行为。

换言之，我们想创建**分层**行为树，使树结构可**组合**。

这可通过在XML中定义多棵树，并用节点 **SubTree** 将一棵树包含进另一棵树实现。

### CrossDoor 行为示例

灵感来源于一篇流行的[行为树文章](https://www.gamedeveloper.com/programming/behavior-trees-for-ai-how-they-work)。

这也是第一个实用示例，涉及使用 `Decorators` 和 `Fallback`。

![crossdoor\_subtree.svg](https://www.behaviortree.dev/assets/images/crossdoor_subtree-4f2304772a896359d3fc67c9802e0bef.svg)

```xml
<root BTCPP_format="4">

    <BehaviorTree ID="MainTree">
        <Sequence>
            <Fallback>
                <Inverter>
                    <IsDoorClosed/>
                </Inverter>
                <SubTree ID="DoorClosed"/>
            </Fallback>
            <PassThroughDoor/>
        </Sequence>
    </BehaviorTree>

    <BehaviorTree ID="DoorClosed">
        <Fallback>
            <OpenDoor/>
            <RetryUntilSuccessful num_attempts="5">
                <PickLock/>
            </RetryUntilSuccessful>
            <SmashDoor/>
        </Fallback>
    </BehaviorTree>
    
</root>
```

期望行为：

* 如果门是开着的，则执行 `PassThroughDoor`。
* 如果门关着，尝试 `OpenDoor`，或尝试最多5次 `PickLock`，最后尝试 `SmashDoor`。
* 如果 `DoorClosed` 子树中的至少一个动作成功，则执行 `PassThroughDoor`。

### CPP 代码示例

我们不展示 `CrossDoor` 中模拟动作的具体实现。

重点是 `registerNodes` 方法：

```cpp
class CrossDoor
{
public:
    void registerNodes(BT::BehaviorTreeFactory& factory);

    // 如果 _door_open != true 返回 SUCCESS
    BT::NodeStatus isDoorClosed();

    // 如果 _door_open == true 返回 SUCCESS
    BT::NodeStatus passThroughDoor();

    // 3次尝试后开锁
    BT::NodeStatus pickLock();

    // 如果门锁着返回 FAILURE
    BT::NodeStatus openDoor();

    // 总是开门
    BT::NodeStatus smashDoor();

private:
    bool _door_open   = false;
    bool _door_locked = true;
    int _pick_attempts = 0;
};

// 辅助函数，简化用户注册
void CrossDoor::registerNodes(BT::BehaviorTreeFactory &factory)
{
  factory.registerSimpleCondition(
      "IsDoorClosed", std::bind(&CrossDoor::isDoorClosed, this));

  factory.registerSimpleAction(
      "PassThroughDoor", std::bind(&CrossDoor::passThroughDoor, this));

  factory.registerSimpleAction(
      "OpenDoor", std::bind(&CrossDoor::openDoor, this));

  factory.registerSimpleAction(
      "PickLock", std::bind(&CrossDoor::pickLock, this));

  factory.registerSimpleCondition(
      "SmashDoor", std::bind(&CrossDoor::smashDoor, this));
}

int main()
{
  BehaviorTreeFactory factory;

  CrossDoor cross_door;
  cross_door.registerNodes(factory);

  // 本示例XML含多棵<BehaviorTree>
  // 确定主树时先注册XML，然后根据ID创建树

  factory.registerBehaviorTreeFromText(xml_text);
  auto tree = factory.createTree("MainTree");

  // 打印树结构的辅助函数
  printTreeRecursively(tree.rootNode());

  tree.tickWhileRunning();

  return 0;
}
```

## 06. 端口重映射（Port Remapping）

在CrossDoor示例中，子树在父树看来像单个叶节点。

为避免大型树中的名称冲突，每棵树和子树拥有各自的黑板实例。

因此，需显式将树的端口连接（重映射）到子树的端口。

你**无需**修改C++实现，重映射完全由XML定义完成。

### 示例

行为树示意图：

![port\_remapping.svg](https://www.behaviortree.dev/assets/images/port_remapping-e025094ce2207aef9dfda609fa10bae7.svg)

```xml
<root BTCPP_format="4">

    <BehaviorTree ID="MainTree">
        <Sequence>
            <Script code=" move_goal='1;2;3' " />
            <SubTree ID="MoveRobot" target="{move_goal}" 
                                    result="{move_result}" />
            <SaySomething message="{move_result}"/>
        </Sequence>
    </BehaviorTree>

    <BehaviorTree ID="MoveRobot">
        <Fallback>
            <Sequence>
                <MoveBase  goal="{target}"/>
                <Script code=" result:='goal reached' " />
            </Sequence>
            <ForceFailure>
                <Script code=" result:='error' " />
            </ForceFailure>
        </Fallback>
    </BehaviorTree>

</root>
```

你会注意到：

* 有个 `MainTree`，包含名为 `MoveRobot` 的子树。
* 希望“连接”（即重映射） `MoveRobot` 子树的端口与 `MainTree` 其它端口。
* 该示例使用的语法完成了这项操作。

### CPP 代码

没多少需要做的，我们用 `debugMessage` 方法检查黑板值。

```cpp
int main()
{
  BT::BehaviorTreeFactory factory;

  factory.registerNodeType<SaySomething>("SaySomething");
  factory.registerNodeType<MoveBaseAction>("MoveBase");

  factory.registerBehaviorTreeFromText(xml_text);
  auto tree = factory.createTree("MainTree");

  // 不断tick直到完成
  tree.tickWhileRunning();

  // 查看黑板当前状态
  std::cout << "\n------ First BB ------" << std::endl;
  tree.subtrees[0]->blackboard->debugMessage();
  std::cout << "\n------ Second BB------" << std::endl;
  tree.subtrees[1]->blackboard->debugMessage();

  return 0;
}

/* 预期输出：

------ First BB ------
move_result (std::string)
move_goal (Pose2D)

------ Second BB------
[result] remapped to port of parent tree [move_result]
[target] remapped to port of parent tree [move_goal]

*/
```

## 07. 使用多个 XML 文件

在之前示例中，我们总是从**单个 XML 文件**创建整棵树及其子树。

但随着子树数量增加，使用多个文件更方便。

### 子树示例

文件 **subtree\_A.xml**：

```xml
<root>
    <BehaviorTree ID="SubTreeA">
        <SaySomething message="Executing Sub_A" />
    </BehaviorTree>
</root>
```

文件 **subtree\_B.xml**：

```xml
<root>
    <BehaviorTree ID="SubTreeB">
        <SaySomething message="Executing Sub_B" />
    </BehaviorTree>
</root>
```

### 手动加载多个文件（推荐）

假设文件 **main\_tree.xml** 包含另外两个文件：

```xml
<root>
    <BehaviorTree ID="MainTree">
        <Sequence>
            <SaySomething message="starting MainTree" />
            <SubTree ID="SubTreeA" />
            <SubTree ID="SubTreeB" />
        </Sequence>
    </BehaviorTree>
</root>
```

手动加载多个文件示例：

```cpp
int main()
{
  BT::BehaviorTreeFactory factory;
  factory.registerNodeType<DummyNodes::SaySomething>("SaySomething");

  // 使用 std::filesystem::directory_iterator 遍历文件夹，注册所有 XML
  std::string search_directory = "./";

  using std::filesystem::directory_iterator;
  for (auto const& entry : directory_iterator(search_directory)) 
  {
    if( entry.path().extension() == ".xml")
    {
      factory.registerBehaviorTreeFromFile(entry.path().string());
    }
  }
  // 相当于调用：
  // factory.registerBehaviorTreeFromFile("./main_tree.xml");
  // factory.registerBehaviorTreeFromFile("./subtree_A.xml");
  // factory.registerBehaviorTreeFromFile("./subtree_B.xml");

  // 创建主树，子树自动添加
  std::cout << "----- MainTree tick ----" << std::endl;
  auto main_tree = factory.createTree("MainTree");
  main_tree.tickWhileRunning();

  // 或只创建其中一个子树
  std::cout << "----- SubA tick ----" << std::endl;
  auto subA_tree = factory.createTree("SubTreeA");
  subA_tree.tickWhileRunning();

  return 0;
}
/* 预期输出：

Registered BehaviorTrees:
 - MainTree
 - SubTreeA
 - SubTreeB
----- MainTree tick ----
Robot says: starting MainTree
Robot says: Executing Sub_A
Robot says: Executing Sub_B
----- SubA tick ----
Robot says: Executing Sub_A
*/
```

### 使用“include”添加多个文件

如果希望将树的包含信息放在XML内，可修改 **main\_tree.xml** 如下：

```xml
<root BTCPP_format="4">
    <include path="./subtree_A.xml" />
    <include path="./subtree_B.xml" />
    <BehaviorTree ID="MainTree">
        <Sequence>
            <SaySomething message="starting MainTree" />
            <SubTree ID="SubTreeA" />
            <SubTree ID="SubTreeB" />
        </Sequence>
    </BehaviorTree>
</root>
```

注意：include 的路径相对于 **main\_tree.xml**。

创建树时，照常调用：

```cpp
factory.createTreeFromFile("main_tree.xml")
```

## 08. 传递额外参数

之前所有示例中，我们都需提供如下构造函数：

```cpp
MyCustomNode(const std::string& name, const NodeConfig& config);
```

有时希望向类构造函数传递额外参数、指针或引用等。

> ⚠️ 注意
>
> 有人会用黑板实现此功能，**请勿这样做**。

以下称这些参数为\*"arguments"\*。

虽然理论上可以用输入端口传递，但若满足以下条件则不推荐：

* 参数在*部署时*已知（构建树时）。
* 参数*运行时*不变。
* 参数不需由XML设置。

满足以上条件，使用端口或黑板均不合适。

### 推荐做法：向构造函数添加参数

示例，自定义节点 **Action\_A**，传入两个复杂参数：

```cpp
class Action_A: public SyncActionNode
{
public:
    // 构造函数含额外参数
    Action_A(const std::string& name, const NodeConfig& config,
             int arg_int, std::string arg_str):
        SyncActionNode(name, config),
        _arg1(arg_int),
        _arg2(arg_str) {}

    static PortsList providedPorts() { return {}; }

    NodeStatus tick() override;

private:
    int _arg1;
    std::string _arg2;
};
```

注册并传递参数：

```cpp
BT::BehaviorTreeFactory factory;
factory.registerNodeType<Action_A>("Action_A", 42, "hello world");

// 指定模板参数也可
// factory.registerNodeType<Action_A, int, std::string>("Action_A", 42, "hello world");
```

### 使用初始化方法

若想为同一节点类型的不同实例传递不同参数，可用初始化方法：

```cpp
class Action_B: public SyncActionNode
{
public:
    Action_B(const std::string& name, const NodeConfig& config):
        SyncActionNode(name, config) {}

    // 希望此方法在第一次tick()前调用且只调用一次
    void initialize(int arg_int, const std::string& arg_str)
    {
        _arg1 = arg_int;
        _arg2 = arg_str;
    }

    static PortsList providedPorts() { return {}; }

    NodeStatus tick() override;

private:
    int _arg1;
    std::string _arg2;
};
```

注册和初始化方式：

```cpp
BT::BehaviorTreeFactory factory;

// 正常注册
factory.registerNodeType<Action_B>("Action_B");

// 创建整棵树，Action_B实例尚未初始化
auto tree = factory.createTreeFromText(xml_text);

// visitor访问所有节点，初始化 Action_B 实例
auto visitor = [](TreeNode* node)
{
  if (auto action_B_node = dynamic_cast<Action_B*>(node))
  {
    action_B_node->initialize(69, "interesting_value");
  }
};

// 应用visitor访问整棵树所有节点
tree.applyVisitor(visitor);
```
## 09. 脚本示例

更详细说明请见 [脚本简介](https://www.behaviortree.dev/docs/guides/scripting)。本教程提供一个非常基础的示例，供初学者练习。

### 脚本节点与前置条件节点

在我们的脚本语言中，变量即为黑板（blackboard）中的条目。

本示例使用节点 **Script** 设置变量，并展示如何在 **SaySomething** 中通过输入端口访问这些变量。

支持的类型包括数字（整数和浮点数）、字符串和已注册的枚举（ENUM）。

> ⚠️ 注意
>
> 我们使用了 **magic\_enums**，其有一些已知[限制](https://github.com/Neargye/magic_enum/blob/master/doc/limitations.md)。
> 其中一个限制是默认枚举范围为 \[-128, 128]，除非按照链接中说明修改。

示例 XML：

```xml
<root BTCPP_format="4">
  <BehaviorTree>
    <Sequence>
      <Script code=" msg:='hello world' " />
      <Script code=" A:=THE_ANSWER; B:=3.14; color:=RED " />
        <Precondition if="A>B && color != BLUE" else="FAILURE">
          <Sequence>
            <SaySomething message="{A}"/>
            <SaySomething message="{B}"/>
            <SaySomething message="{msg}"/>
            <SaySomething message="{color}"/>
        </Sequence>
      </Precondition>
    </Sequence>
  </BehaviorTree>
</root>
```

期望黑板条目包含：

* **msg**：字符串 `"hello world"`
* **A**：枚举标签 `THE_ANSWER` 对应的整数值
* **B**：浮点数 `3.14`
* **color**：枚举标签 `RED` 对应的整数值

预期输出：

```console
Robot says: 42.000000
Robot says: 3.140000
Robot says: hello world
Robot says: 1.000000
```

对应的 C++ 代码：

```cpp
enum Color
{
  RED = 1,
  BLUE = 2,
  GREEN = 3
};

int main()
{
  BehaviorTreeFactory factory;
  factory.registerNodeType<DummyNodes::SaySomething>("SaySomething");

  // 向脚本语言注册枚举
  factory.registerScriptingEnums<Color>();

  // 手动为标签 "THE_ANSWER" 赋值，绕开范围限制
  factory.registerScriptingEnum("THE_ANSWER", 42);

  auto tree = factory.createTreeFromText(xml_text);
  tree.tickWhileRunning();

  return 0;
}
```

## 10. 日志器（Loggers）与观察者（Observer）

BT.CPP 提供了在运行时添加**日志器**的方式，通常在树创建后且开始 tick 之前添加。

“日志器”是一个类，每当某个节点状态改变时会调用其回调函数；这是所谓[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)的非侵入式实现。

具体的回调函数签名为：

```cpp
  virtual void callback(
    BT::Duration timestamp, // 状态改变时间
    const TreeNode& node,   // 状态改变的节点
    NodeStatus prev_status, // 之前的状态
    NodeStatus status);     // 新状态
```

### TreeObserver 类

有时，尤其是在实现**单元测试**时，想知道某节点返回 SUCCESS 或 FAILURE 的次数。

例如，验证某些条件下，某分支被执行而另一个未被执行。

`TreeObserver` 是一个简单的日志器实现，收集每个节点的以下统计数据：

```cpp
struct NodeStatistics
{
  // 最后有效结果，SUCCESS 或 FAILURE
  NodeStatus last_result;
  // 当前状态，可能为任意状态（包括 IDLE 或 SKIPPED）
  NodeStatus current_status;
  // 状态转换次数（不计转换为 IDLE）
  unsigned transitions_count;
  // 转换为 SUCCESS 次数
  unsigned success_count;
  // 转换为 FAILURE 次数
  unsigned failure_count;
  // 转换为 SKIPPED 次数
  unsigned skip_count;
  // 最后一次转换时间戳
  Duration last_timestamp;
};
```

### 如何唯一标识节点

观察者收集指定节点的统计信息，需要唯一标识节点：

两种方式：

* `TreeNode::UID()`：节点对应树的[深度优先遍历](https://en.wikipedia.org/wiki/Depth-first_search)唯一编号。
* `TreeNode::fullPath()`：节点的唯一且可读路径标识。

称之为“路径”，因为典型字符串格式为：

```text
 first_subtree/nested_subtree/node_name
```

路径包含了节点在子树层级中的位置信息。

`node_name` 是XML中分配的 `name` 属性，或者自动生成的格式：注册名 + "::" + UID。

### 示例 XML

以下示例在子树层级结构上较复杂：

```xml
<root BTCPP_format="4">
  <BehaviorTree ID="MainTree">
    <Sequence>
     <Fallback>
       <AlwaysFailure name="failing_action"/>
       <SubTree ID="SubTreeA" name="mysub"/>
     </Fallback>
     <AlwaysSuccess name="last_action"/>
    </Sequence>
  </BehaviorTree>

  <BehaviorTree ID="SubTreeA">
    <Sequence>
      <AlwaysSuccess name="action_subA"/>
      <SubTree ID="SubTreeB" name="sub_nested"/>
      <SubTree ID="SubTreeB" />
    </Sequence>
  </BehaviorTree>

  <BehaviorTree ID="SubTreeB">
    <AlwaysSuccess name="action_subB"/>
  </BehaviorTree>
</root>
```

可见部分节点有 `name` 属性，有些没有。

对应的 **UID -> fullPath** 映射为：

```text
1 -> Sequence::1
2 -> Fallback::2
3 -> failing_action
4 -> mysub
5 -> mysub/Sequence::5
6 -> mysub/action_subA
7 -> mysub/sub_nested
8 -> mysub/sub_nested/action_subB
9 -> mysub/SubTreeB::9
10 -> mysub/SubTreeB::9/action_subB
11 -> last_action
```


### 示例（C++）

以下应用程序将执行以下操作：

* 递归打印行为树结构。
* 将 `TreeObserver` 附加到行为树。
* 打印 `UID / fullPath` 对。
* 收集名为 `"last_action"` 的特定节点的统计数据。
* 显示观察者收集的所有统计信息。

```cpp
int main()
{
  BT::BehaviorTreeFactory factory;

  factory.registerBehaviorTreeFromText(xml_text);
  auto tree = factory.createTree("MainTree");

  // 辅助函数：递归打印树结构。
  BT::printTreeRecursively(tree.rootNode());

  // 观察者用于保存某节点返回 SUCCESS 或 FAILURE 的次数统计，
  // 适合单元测试，验证预期的状态转换是否发生。
  BT::TreeObserver observer(tree);

  // 打印唯一 ID 与对应的可读路径，
  // 路径也应是唯一的。
  std::map<uint16_t, std::string> ordered_UID_to_path;
  for(const auto& [name, uid]: observer.pathToUID()) {
    ordered_UID_to_path[uid] = name;
  }

  for(const auto& [uid, name]: ordered_UID_to_path) {
    std::cout << uid << " -> " << name << std::endl;
  }

  tree.tickWhileRunning();

  // 访问指定节点的统计数据，可用全路径或 UID
  const auto& last_action_stats = observer.getStatistics("last_action");
  assert(last_action_stats.transitions_count > 0);

  std::cout << "----------------" << std::endl;
  // 打印所有统计数据
  for(const auto& [uid, name]: ordered_UID_to_path) {
    const auto& stats = observer.getStatistics(uid);

    std::cout << "[" << name
              << "] \tT/S/F:  " << stats.transitions_count
              << "/" << stats.success_count
              << "/" << stats.failure_count
              << std::endl;
  }

  return 0;
}
```

## 11. 连接 Groot2

**Groot2** 是官方的 IDE，用于编辑、监控和交互操作由 **BT.CPP** 创建的行为树。

集成非常简单，先了解以下几个核心概念。

### TreeNodesModel

Groot 需要“节点模型”。

![img](https://www.behaviortree.dev/assets/images/t12_groot_models-5f1f63eeae69454a87cb5f609c0865b6.png)

例如，图中 Groot 需要知道用户自定义的节点 `ThinkWhatToSay` 和 `SaySomething`。

此外，需要以下信息：

* 节点类型
* 端口名称及类型（输入/输出）

这些模型以 XML 格式表达，例如：

```xml
  <TreeNodesModel>
    <Action ID="SaySomething">
      <input_port name="message"/>
    </Action>
    <Action ID="ThinkWhatToSay">
      <output_port name="text"/>
    </Action>
  </TreeNodesModel>
```

但**不建议手动编写此 XML**。

BT.CPP 提供生成该 XML 的函数：

```cpp
  BT::BehaviorTreeFactory factory;
  //
  // 在此注册用户自定义节点
  // 
  std::string xml_models = BT::writeTreeNodesModelXML(factory);

  // 保存 xml_models 到文件，
  // 或加载到 Groot2 中使用
```

导入 UI 的方法：

* 将 XML 保存为文件（如 `models.xml`），在 Groot2 中点击 **Import Models** 按钮导入。
* 或直接将 XML 片段加入 `.xml` 或 `.btproj` 文件。

### 向 Groot 添加实时可视化

> ℹ️ 注意
>
> 当前仅 Groot2 PRO 版本支持实时可视化。

连接行为树到 Groot2 只需一行代码：

```cpp
BT::Groot2Publisher publisher(tree);
```

该功能通过进程间通信实现：

* 发送完整树结构及模型给 Groot2。
* 定期更新各节点状态（RUNNING、SUCCESS、FAILURE、IDLE）。
* 发送黑板变量值；支持基础类型（整型、浮点、字符串），其他类型需手动添加。
* 允许 Groot2 设置断点、节点替换、故障注入。

完整示例 XML：

```xml
<root BTCPP_format="4">

  <BehaviorTree ID="MainTree">
    <Sequence>
      <Script code="door_open:=false" />
      <Fallback>
        <Inverter>
          <IsDoorClosed/>
        </Inverter>
        <SubTree ID="DoorClosed" _autoremap="true" door_open="{door_open}"/>
      </Fallback>
      <PassThroughDoor/>
    </Sequence>
  </BehaviorTree>

  <BehaviorTree ID="DoorClosed">
    <Fallback name="tryOpen" _onSuccess="door_open:=true">
      <OpenDoor/>
        <RetryUntilSuccessful num_attempts="5">
          <PickLock/>
        </RetryUntilSuccessful>
      <SmashDoor/>
    </Fallback>
  </BehaviorTree>

</root>
```

示例 C++ 代码：

```cpp
int main()
{
  BT::BehaviorTreeFactory factory;

  // 相关的简单节点，模拟开门流程
  CrossDoor cross_door;
  cross_door.registerNodes(factory);

  // Groot2 需要节点模型
  // 该 XML 可通过下面函数自动生成，无需手写
  std::string xml_models = BT::writeTreeNodesModelXML(factory);

  factory.registerBehaviorTreeFromText(xml_text);
  auto tree = factory.createTree("MainTree");

  // 连接 Groot2Publisher，支持树结构与状态更新通信
  BT::Groot2Publisher publisher(tree);

  // 无限循环执行
  while(1)
  {
    std::cout << "Start" << std::endl;
    cross_door.reset();
    tree.tickWhileRunning();
    std::this_thread::sleep_for(std::chrono::milliseconds(3000));
  }
  return 0;
}
```



### 在黑板中可视化自定义类型

黑板中的内容以 JSON 格式发送给 Groot2。

要添加新类型并允许 Groot2 可视化它们，应遵循此处的说明：

[https://json.nlohmann.me/features/arbitrary\_types/](https://json.nlohmann.me/features/arbitrary_types/)

例如，给定一个用户自定义类型：

```cpp
struct Pose2D {
    double x;
    double y;
    double theta;
}
```

你需要包含 **behaviortree\_cpp/json\_export.h** 并根据你的 BT.CPP 版本，遵循以下说明。

#### 版本 4.3.5 及以前

实现函数 `nlohmann::to_json()`：

```cpp
namespace nlohmann {
  void to_json(nlohmann::json& dest, const Pose2D& pose) {
    dest["x"] = pose.x;
    dest["y"] = pose.y;
    dest["theta"] = pose.theta;
  }
}
```

然后，在你的 **main** 中注册该函数：

```cpp
BT::JsonExporter::get().addConverter<Pose2D>();
```

#### 版本 4.3.6 及以后

`to_json` 函数的实现可以有任意名称或命名空间，但必须符合函数签名 `void(nlohmann::json&, const T&)`。

例如：

```cpp
void PoseToJson(nlohmann::json& dest, const Pose2D& pose) {
  dest["x"] = pose.x;
  dest["y"] = pose.y;
  dest["theta"] = pose.theta;
}
```

在你的 **main** 中注册该函数：

```cpp
BT::RegisterJsonDefinition<Pose2D>(PoseToJson);
```

# 教程（高级）

5 分钟学习最重要的 Docusaurus 概念。

## 12. 端口默认值

定义端口时，添加默认值可能很方便，即当 XML 中未指定时，端口应具有的值。

> ℹ️ 注意
>
> 本教程中的某些示例需要 4.5.2 版本或更高版本。

### 默认输入端口

考虑一个初始化多个端口的节点。我们使用自定义类型 **Point2D**，但对于简单类型如 `int`、`double` 或 `string` 同样适用。

```cpp
  static PortsList providedPorts()
  {
    return { 
      BT::InputPort<Point2D>("input"),
      BT::InputPort<Point2D>("pointA", Point2D{1, 2}, "默认值为 x=1, y=2"),
      BT::InputPort<Point2D>("pointB", "3,4",         "默认值为 x=3, y=4"),
      BT::InputPort<Point2D>("pointC", "{point}",     "默认指向黑板条目 {point}"),
      BT::InputPort<Point2D>("pointD", "{=}",         "默认指向黑板条目 {pointD}") 
    };
  }
```

第一个 (`input`) 没有默认值，XML 中必须提供值或黑板条目。

#### 默认值

```cpp
BT::InputPort<Point2D>("pointA", Point2D{1, 2}, "...");
```

如果实现了模板特化 `convertFromString<Point2D>()`，也可以使用字符串形式。

换句话说，以下语法应当等效，假如我们的 **convertFromString** 期待两个逗号分隔的值：

```cpp
BT::InputPort<Point2D>("pointB", "3,4", "...");
// 等效于：
BT::InputPort<Point2D>("pointB", Point2D{3, 4}, "...");
```

#### 默认黑板条目

另外，也可以定义端口应指向的默认黑板条目。

```cpp
BT::InputPort<Point2D>("pointC", "{point}", "...");
```

如果端口名称和黑板条目名称**相同**，可以使用 "{=}"

```cpp
BT::InputPort<Point2D>("pointD", "{=}", "...");
// 等效于：
BT::InputPort<Point2D>("pointD", "{pointD}", "...");
```

### 默认输出端口

输出端口更受限制，只能指向黑板条目。当名称相同时，仍可使用 "{=}"。

```cpp
  static PortsList providedPorts()
  {
    return { 
      BT::OutputPort<Point2D>("result", "{target}", "默认指向黑板条目 {target}");
    };
  }
```

## 13. 通过引用访问端口

如果你跟随教程，应该知道黑板使用**值语义**，即 `getInput` 和 `setOutput` 方法是从黑板复制值或写入值。

在某些情况下，可能希望使用**引用语义**，即直接访问存储在黑板中的对象。特别当对象是：

* 复杂数据结构
* 复制代价高
* 不可复制

例如，建议使用引用语义的节点是 `LoopNode` 装饰器，它“就地”修改对象向量。

#### 方法1：将黑板条目作为共享指针

为简单起见，考虑一个复制代价高的对象，称为 **Pointcloud**。

假设我们有如下简单 BT：

```xml
 <root BTCPP_format="4" >
    <BehaviorTree ID="SegmentCup">
       <Sequence>
           <AcquirePointCloud  cloud="{pointcloud}"/>
           <SegmentObject  obj_name="cup" cloud="{pointcloud}" obj_pose="{pose}"/>
       </Sequence>
    </BehaviorTree>
</root>
```

* **AcquirePointCloud** 将写入黑板条目 `pointcloud`。
* **SegmentObject** 将从该条目读取。

此时推荐的端口类型为：

```cpp
PortsList AcquirePointCloud::providedPorts()
{
    return { OutputPort<std::shared_ptr<Pointcloud>>("cloud") };
}

PortsList SegmentObject::providedPorts()
{
    return { InputPort<std::string>("obj_name"),
             InputPort<std::shared_ptr<Pointcloud>>("cloud"),
             OutputPort<Pose3D>("obj_pose") };
}
```

`getInput` 和 `setOutput` 方法照常使用，仍是值语义。但因为被复制的是 `shared_ptr`，实际上是通过引用访问 pointcloud 实例。

#### 方法2：线程安全的 castPtr（4.5.1 版本起推荐）

`shared_ptr` 方法的最大问题是**线程不安全**。

如果自定义异步节点有自己的线程，实际对象可能会被其他线程同时访问。

为防止此问题，提供了包含锁机制的不同 API。

首先，创建端口时使用普通的 `Pointcloud`，无需包装成 `std::shared_ptr`：

```cpp
PortsList AcquirePointCloud::providedPorts()
{
    return { OutputPort<Pointcloud>("cloud") };
}

PortsList SegmentObject::providedPorts()
{
    return { InputPort<std::string>("obj_name"),
             InputPort<Pointcloud>("cloud"),
             OutputPort<Pose3D>("obj_pose") };
}
```

通过指针/引用访问 Pointcloud 实例：

```cpp
// 在以下作用域内，只要 "any_locked" 存在，保护 "cloud" 实例的互斥锁将保持锁定
if(auto any_locked = getLockedPortContent("cloud"))
{
  if(any_locked->empty())
  {
    // 黑板条目尚未初始化
    // 可通过以下方式初始化：
    any_locked.assign(my_initial_pointcloud);
  }
  else if(Pointcloud* cloud_ptr = any_locked->castPtr<Pointcloud>())
  {
    // 成功转换为 Pointcloud*（原始类型）
    // 使用 cloud_ptr 修改 pointcloud 实例
  }
}
```

## 14. 子树模型和自动重映射

子树重映射在[教程6](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_06_subtree_ports)中介绍。

不幸的是，当在多个位置使用相同的 SubTree 时，我们可能会发现自己反复复制粘贴相同的长 XML 标签。

例如，考虑如下情况：

```xml
<SubTree ID="MoveRobot" target="{move_goal}"  frame="world" result="{error_code}" />
```

我们不想每次都复制粘贴这三个 XML 属性 `target`、`frame` 和 `result`，除非它们的值不同。

为避免此情况，我们可以在 `<TreeNodesModel>` 中定义它们的默认值。

```xml
  <TreeNodesModel>
    <SubTree ID="MoveRobot">
      <input_port  name="target"  default="{move_goal}"/>
      <input_port  name="frame"   default="world"/>
      <output_port name="result"  default="{error_code}"/>
    </SubTree>
  </TreeNodesModel>
```

从概念上讲，这类似于[教程12](https://www.behaviortree.dev/docs/tutorial-advanced/tutorial_12_default_ports)中解释的默认端口。

如果在 XML 中指定，这些重映射黑板条目的值将被覆盖。下面的例子中，我们覆盖了 "frame" 的值，但保持了其他的默认重映射。

```xml
<SubTree ID="MoveRobot" frame="map" />
```

## 自动重映射（Autoremap）

当 SubTree 和父树中条目的名称**相同**时，你可以使用属性 `_autoremap`。

例如：

```xml
<SubTree ID="MoveRobot" target="{target}"  frame="{frame}" result="{result}" />
```

可以替换为：

```xml
<SubTree ID="MoveRobot" _autoremap="true" />
```

我们仍可以覆盖特定值，并自动重映射其他值：

```xml
<SubTree ID="MoveRobot" _autoremap="true" frame="world" />
```

> ⚠️ 注意
>
> 属性 `_autoremap="true"` 会自动重映射 SubTree 中的**所有**条目，**除非**它们的名称以下划线字符 "\_" 开头。

这可能是将 SubTree 中某条目标记为“私有”的方便方式。

## 15. 模拟（Mocking）与节点替换

有时，特别是在实现集成测试和单元测试时，希望快速替换某个特定节点或整类节点为“测试”版本（模拟）。

从版本 4.1 起，引入了名为“替换规则”的新机制，简化了此过程。

它包含 `BehaviorTreeFactory` 类中的附加方法，应在节点注册完成后且实际树实例化前调用。

例如，给定 XML：

```xml
<SaySomething name="talk" message="hello world"/>
```

我们可能想用另一个名为 **TestMessage** 的节点替换它：

对应的替换命令为：

```cpp
factory.addSubstitutionRule("talk", "TestMessage");
```

第一个参数是与 `TreeNode::fullPath` 匹配的[通配符字符串](https://en.wikipedia.org/wiki/Wildcard_character)。

关于 **fullPath** 的详情，请参见[之前的教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_10_observer)。

### TestNode

`TestNode` 是一个动作节点，可以配置为：

* 返回特定状态，SUCCESS 或 FAILURE
* 同步或异步；后者需要指定超时
* 后置条件脚本，通常用来模拟输出端口（OutputPort）

这个简单的虚拟节点不会覆盖所有情况，但能作为多种替换规则的默认解决方案。

### 完整示例

在此示例中，我们将看到：

* 如何用替换规则替换节点
* 如何使用内置的 `TestNode`
* 通配符匹配示例
* 如何在运行时通过 JSON 文件传递这些规则

使用如下 XML：

```xml
<root BTCPP_format="4">
  <BehaviorTree ID="MainTree">
    <Sequence>
      <SaySomething name="talk" message="hello world"/>
        <Fallback>
          <AlwaysFailure name="failing_action"/>
          <SubTree ID="MySub" name="mysub"/>
        </Fallback>
        <SaySomething message="before last_action"/>
        <Script code="msg:='after last_action'"/>
        <AlwaysSuccess name="last_action"/>
        <SaySomething message="{msg}"/>
    </Sequence>
  </BehaviorTree>

  <BehaviorTree ID="MySub">
    <Sequence>
      <AlwaysSuccess name="action_subA"/>
      <AlwaysSuccess name="action_subB"/>
    </Sequence>
  </BehaviorTree>
</root>
```

对应的 C++ 代码：

```cpp
int main(int argc, char** argv)
{
  BT::BehaviorTreeFactory factory;
  factory.registerNodeType<SaySomething>("SaySomething");

  // 使用 lambda 和 registerSimpleAction 创建
  // 一个“虚拟”节点，用来替换指定节点。

  // 简单节点，仅打印名字并返回 SUCCESS
  factory.registerSimpleAction("DummyAction", [](BT::TreeNode& self){
    std::cout << "DummyAction substituting: "<< self.name() << std::endl;
    return BT::NodeStatus::SUCCESS;
  });

  // 用于替换 SaySomething 的动作。
  // 尝试使用输入端口 "message"
  factory.registerSimpleAction("TestSaySomething", [](BT::TreeNode& self){
    auto msg = self.getInput<std::string>("message");
    if (!msg)
    {
      throw BT::RuntimeError( "missing required input [message]: ", msg.error() );
    }
    std::cout << "TestSaySomething: " << msg.value() << std::endl;
    return BT::NodeStatus::SUCCESS;
  });

  //----------------------------
  // 如果传入参数为 "no_sub"，跳过添加规则
  bool skip_substitution = (argc == 2) && std::string(argv[1]) == "no_sub";

  if(!skip_substitution)
  {
    // 可以用 JSON 文件配置替换规则
    // 或手动添加
    bool const USE_JSON = true;

    if(USE_JSON)
    {
      factory.loadSubstitutionRuleFromJSON(json_text);
    }
    else {
      // 用通配符替换匹配的节点为 TestAction
      factory.addSubstitutionRule("mysub/action_*", "TestAction");

      // 替换名为 [talk] 的节点为 TestSaySomething
      factory.addSubstitutionRule("talk", "TestSaySomething");

      // 配置一个 TestNode
      BT::TestNodeConfig test_config;
      // 将节点改为异步，等待 2000 毫秒
      test_config.async_delay = std::chrono::milliseconds(2000);
      // 执行该后置条件脚本
      test_config.post_script = "msg ='message SUBSTITUED'";

      // 替换名为 [last_action] 的节点为配置好的 TestNode
      factory.addSubstitutionRule("last_action", test_config);
    }
  }

  factory.registerBehaviorTreeFromText(xml_text);

  // 树构造阶段将使用替换规则实例化测试节点，替代原节点
  auto tree = factory.createTree("MainTree");
  tree.tickWhileRunning();

  return 0;
}
```

### JSON 格式

对应 `USE_JSON == false` 分支执行的 JSON 文件如下：

```json
{
  "TestNodeConfigs": {
    "MyTest": {
      "async_delay": 2000,
      "return_status": "SUCCESS",
      "post_script": "msg ='message SUBSTITUED'"
    }
  },

  "SubstitutionRules": {
    "mysub/action_*": "TestAction",
    "talk": "TestSaySomething",
    "last_action": "MyTest"
  }
}
```

如你所见，主要分两部分：

* **TestNodeConfigs**：设置一个或多个 **TestNode** 的参数和名称。
* **SubstitutionRules**：指定具体的替换规则。


## 16. 全局黑板惯用法

> ℹ️ 注意
> 引入于 Bt.CPP 4.6.0 版本

如前面教程所述，BT.CPP 强调“作用域黑板”的重要性，用以隔离各子树，使其如同编程语言中的独立函数/例程。

但有时希望拥有真正的“全局”黑板，可被每个子树直接访问，无需重映射。

这种做法适用于：

* 单例和全局对象（无法按[教程8](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_08_additional_args)描述的方式共享）
* 机器人的全局状态
* 在行为树外部写入/读取的数据，即在执行 tick 的主循环中

此外，黑板是通用的键/值存储，值可以是**任意**类型，是实现文献中所谓**“世界模型”**的理想数据结构，即环境、机器人和任务状态与行为树共享的地方。

### 黑板层级结构

考虑如下包含两个子树的简单树：

![tree\_hierarchy.png](https://www.behaviortree.dev/assets/images/tree_hierarchy-56dab79b138903144f65368898a3ad0f.png)

每个 3 个子树各有自己的黑板；这些黑板之间的父子关系与树结构一致，即 BB1 是 BB2 和 BB3 的父黑板。

这些黑板的生命周期与各自子树绑定。

我们可以实现如下外部“全局黑板”：

```cpp
auto global_bb = BT::Blackboard::create();
auto maintree_bb = BT::Blackboard::create(global_bb);
auto tree = factory.createTree("MainTree", maintree_bb);
```

这将创建如下黑板层级：

![bb\_hierarchy.png](https://www.behaviortree.dev/assets/images/bb_hierarchy-c85c83b6c61464605231def7f2db1557.png)

实例 `global_bb` 位于行为树“外部”，即使销毁 `tree` 对象也会存在。

且可用 `set` 和 `get` 方法方便访问。

### 如何从树中访问顶层黑板

“顶层黑板”指层级根节点处的黑板。

上述代码中，`global_bb` 是顶层黑板。

自 BT.CPP **4.6** 版本起，引入新语法，允许**无需重映射**直接访问顶层黑板，只需在条目名称前添加前缀 `@`。

例如：

```xml
<PrintNumber val="{@value}" />
```

端口 **val** 将在顶层黑板中查找条目 `value`，而非局部黑板。

### 完整示例

考虑此树：

```xml
  <BehaviorTree ID="MainTree">
    <Sequence>
      <PrintNumber name="main_print" val="{@value}" />
      <SubTree ID="MySub"/>
    </Sequence>
  </BehaviorTree>

  <BehaviorTree ID="MySub">
    <Sequence>
      <PrintNumber name="sub_print" val="{@value}" />
      <Script code="@value_sqr := @value * @value" />
    </Sequence>
  </BehaviorTree>
```

对应 C++ 代码：

```cpp
class PrintNumber : public BT::SyncActionNode
{
public:
  PrintNumber(const std::string& name, const BT::NodeConfig& config)
    : BT::SyncActionNode(name, config)
  {}
  
  static BT::PortsList providedPorts()
  {
    return { BT::InputPort<int>("val") };
  }

  NodeStatus tick() override
  {
    const int val = getInput<int>("val").value();
    std::cout << "[" << name() << "] val: " << val << std::endl;
    return NodeStatus::SUCCESS;
  }
};

int main()
{
  BehaviorTreeFactory factory;
  factory.registerNodeType<PrintNumber>("PrintNumber");
  factory.registerBehaviorTreeFromText(xml_main);

  // 无任何对象拥有此黑板所有权
  auto global_bb = BT::Blackboard::create();
  // "MainTree" 拥有 maintree_bb
  auto maintree_bb = BT::Blackboard::create(global_bb);
  auto tree = factory.createTree("MainTree", maintree_bb);

  // 可以直接与 global_bb 交互
  for(int i = 1; i <= 3; i++)
  {
    // 写入条目 "value"
    global_bb->set("value", i);
    // 触发一次树的 tick
    tree.tickOnce();
    // 读取条目 "value_sqr"
    auto value_sqr = global_bb->get<int>("value_sqr");
    // 输出
    std::cout << "[While loop] value: " << i 
              << " value_sqr: " << value_sqr << "\n\n";
  }
  return 0;
}
```

输出：

```text
[main_print] val: 1
[sub_print] val: 1
[While loop] value: 1 value_sqr: 1

[main_print] val: 2
[sub_print] val: 2
[While loop] value: 2 value_sqr: 4

[main_print] val: 3
[sub_print] val: 3
[While loop] value: 3 value_sqr: 9
```

备注：

* 前缀 "@" 在输入/输出端口和脚本语言中均有效。
* 子树中无需重映射。
* 主循环中直接访问黑板时，无需前缀 "@ "。


# 指南

成为行为树高手

## 脚本入门

行为树 4.X 引入了一个简单而强大的新概念：XML 内的脚本语言。

该脚本语言语法熟悉，允许用户快速从黑板读写变量。

学习脚本最简单的方式是使用内置动作 **Script**，它在[第二教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_02_basic_ports)中介绍。

### 赋值运算符、字符串和数字

示例：

```text
param_A := 42
param_B = 3.14
message = 'hello world'
```

* 第一行将数字 42 赋值给黑板条目 **param\_A**。
* 第二行将数字 3.14 赋值给黑板条目 **param\_B**。
* 第三行将字符串 "hello world" 赋值给黑板条目 **message**。

> 💡 提示
> 运算符 **":="** 与 **"="** 的区别在于，前者会在黑板不存在该条目时创建它，而后者如果黑板无此条目则抛异常。

你也可以用 **分号** 在一条脚本中添加多个命令。

```text
A:= 42; B:=24
```

#### 算术运算符和括号

示例：

```text
param_A := 7
param_B := 5
param_B *= 2
param_C := (param_A * 3) + param_B
```

结果 `param_B` 为 10，`param_C` 为 31。

支持以下运算符：

| 运算符 | 赋值运算符 | 说明 |
| --- | ----- | -- |
| +   | +=    | 加法 |
| -   | -=    | 减法 |
| \*  | \*=   | 乘法 |
| /   | /=    | 除法 |

注意，加法运算符唯一支持字符串连接。

### 位运算符和十六进制数

仅当值能转为整数时可用。对字符串或浮点数使用会抛异常。

示例：

```text
value:= 0x7F
val_A:= value & 0x0F
val_B:= value | 0xF0
```

`val_A` 为 0x0F（15），`val_B` 为 0xFF（255）。

| 二进制运算符 | 说明   |
| ------ | ---- |
| \|     | 按位或  |
| &      | 按位与  |
| ^      | 按位异或 |

| 一元运算符 | 说明   |
| ----- | ---- |
| \~    | 按位取反 |

### 逻辑和比较运算符

返回布尔值。

示例：

```text
val_A := true
val_B := 5 > 3
val_C := (val_A == val_B)
val_D := (val_A && val_B) || !val_C
```

| 运算符        | 说明           |
| ---------- | ------------ |
| true/false | 布尔值，可转换为 1/0 |
| &&         | 逻辑与          |
| \|\|       | 逻辑或          |
| !          | 逻辑非          |
| ==         | 等于           |
| !=         | 不等           |
| <          | 小于           |
| <=         | 小于等于         |
| >          | 大于           |
| >=         | 大于等于         |

#### 三元运算符 if-then-else

示例：

```text
val_B = (val_A > 1) ? 42 : 24
```

### C++ 示例

展示脚本语言及如何用枚举表示**整数值**。

XML：

```xml
<root >
    <BehaviorTree>
        <Sequence>
            <Script code=" msg:='hello world' " />
            <Script code=" A:=THE_ANSWER; B:=3.14; color:=RED " />
            <Precondition if="A>B && color!=BLUE" else="FAILURE">
                <Sequence>
                  <SaySomething message="{A}"/>
                  <SaySomething message="{B}"/>
                  <SaySomething message="{msg}"/>
                  <SaySomething message="{color}"/>
                </Sequence>
            </Precondition>
        </Sequence>
    </BehaviorTree>
</root>
```

C++ 注册节点和枚举代码：

```cpp
int main()
{
  BehaviorTreeFactory factory;
  factory.registerNodeType<SaySomething>("SaySomething");

  enum Color { RED=1, BLUE=2, GREEN=3 };
  factory.registerScriptingEnums<Color>();

  factory.registerScriptingEnum("THE_ANSWER", 42);

  auto tree = factory.createTreeFromText(xml_text);
  tree.tickWhileRunning();
  return 0;
}
```

预期输出：

```text
Robot says: 42.000000
Robot says: 3.140000
Robot says: hello world
Robot says: 1.000000
```

注意，枚举在内部始终以数值形式解释。


## 前置条件和后置条件

利用[前一教程](https://www.behaviortree.dev/docs/guides/scripting)中介绍的脚本语言功能，BT.CPP 4.x 引入了前置条件（Pre Conditions）和后置条件（Post Conditions）的概念，即可在节点实际执行 **tick()** 之前或之后运行的脚本。

所有节点均支持前置和后置条件，无需修改你的 C++ 代码。

> ⚠️ 注意
> 脚本的目的**不是**编写复杂代码，而仅仅是提升树的可读性，并减少非常简单用例中自定义 C++ 节点的需求。

如果脚本变得过长，建议重新考虑是否适合使用脚本。

### 前置条件

| 名称              | 说明                                   |
| --------------- | ------------------------------------ |
| **\_skipIf**    | 如果条件为真，则跳过该节点的执行                     |
| **\_failureIf** | 如果条件为真，则跳过并返回 FAILURE                |
| **\_successIf** | 如果条件为真，则跳过并返回 SUCCESS                |
| **\_while**     | 类似于 \_skipIf，但如果条件变为假，还可以中断一个正在运行的节点 |

#### 示例

之前教程中，我们看到如何用 fallback 构建 if-then-else 逻辑。

新语法更加简洁：


![img](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB3aWR0aD0iNTg0IiBoZWlnaHQ9IjIyMCIgY29udGVudD0iJmx0O214ZmlsZSBob3N0PSZxdW90O2FwcC5kaWFncmFtcy5uZXQmcXVvdDsgbW9kaWZpZWQ9JnF1b3Q7MjAyMi0xMC0wN1QwOTo1Njo1NC41MzZaJnF1b3Q7IGFnZW50PSZxdW90OzUuMCAoWDExOyBMaW51eCB4ODZfNjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS8xMDMuMC4wLjAgU2FmYXJpLzUzNy4zNiZxdW90OyB2ZXJzaW9uPSZxdW90OzIwLjQuMCZxdW90OyBldGFnPSZxdW90O3lBczVUSElIOFBhalNFS2pXazhBJnF1b3Q7IHR5cGU9JnF1b3Q7Z29vZ2xlJnF1b3Q7Jmd0OyZsdDtkaWFncmFtIGlkPSZxdW90O1hTSS1FUHhUZDVrWlE3eVhtTXpWJnF1b3Q7Jmd0OzdaZE5jNXN3RUlaL1N3OGNtK0hiK0JqYmNadVpkbnJJb2MwcEk0TUExYktXQ2puRy9mVVZzQmcwMkoxNDZzWTU1QkxZZDdVQ1BlOUdGcFkzMzFTZkpDbnlyNUJRYnJsMlVsbmV3bkxkaVJQcXY3V3did1hmRFZvaGt5eHBKYWNYSHRodmlxS042cFlsdERRR0tnQ3VXR0dLTVFoQlkyVm9SRXJZbWNOUzRPWlRDNUxSa2ZBUUV6NVd2N05FNWEwYUJYYXZmNllzeTdzbk96Wm1OcVFiakVLWmt3UjJBOG03czd5NUJGRHQzYWFhVTE2ejY3aTBkY3NUMmNPTFNTclVTd3JjdHVDWjhDMnU3YjVjQU1nNWg1SW0rSTVxM3kxY3dsWWt0SzUxTEcrMnk1bWlEd1dKNit4T082MjFYRzA0cGxNUUNyMXpJaDBUR1dQb2hUckVKMU9wYUhYeTdaMERFOTFMRkRaVXliMGUwalVTVXNRMml2eEpHKzk2VTV6T2xIeGdpSThhd1Q3SURqUDNxUFFOMGpwT3podVIrMVpRVWJQN0N6WDdmR29Yd09SNjBVM29HcWdta1QxRzVmNG5WUDZJQ0UzMC94T0dBb1MrekV4SUlGVU9HUWpDdndBVWlPWW5WV3FQYk1oV2dRbU9Wa3o5cU10dkFvd2VCNWxGaFRNM3diNExoRjdLb0tnT0g0ZTV2cXlKdXJxaFNRSEdjK0RhKzNwNW5oM2FNM3Q1TUs5ZXJXRmRDVnNabzRSYm9TSXlvOHBvclJjWUxDa25pajJicy8rTFZjRzdWV2RaRlYzUHFuQzBBUzBKNXlzU3I5L2NCdVFFb3cwb25MN2lCalI1NytyVFhSMk51OXE5WGxkSDR3T0phSHJ2N2Yyc1JzSDFmbEtucjNUNnFBRXhmZkM5NVN3VFdseUJVckRSaVZKUHdVUTJ3M2poT0plQjZ2dm1tZTdvUGpFOVFuVjZBYXJkOThVQXErV0dYQ0VZZzJ2NGF3dGQ0bVBaSUx2VkE1eWdxQm9TWFY3ZlpmWDFxVnl6NGo3VlE1cHl1eC94SWRHMlBjVjQ0aDdVOGZhNmJKL2RUbk5aZTFQRytXRGJTTlBVamVQYVhTVmhUUWVaSkZ5RndZVk83WDVnV2p5eGoxZzhPV0t4ZDc3Rk91dy9wcHJjNEl2VXUvc0QmbHQ7L2RpYWdyYW0mZ3Q7Jmx0Oy9teGZpbGUmZ3Q7IiB2ZXJzaW9uPSIxLjEiIHZpZXdCb3g9Ii0wLjUgLTAuNSA1ODQgMjIwIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIj48Zz48cmVjdCB3aWR0aD0iMTUwIiBoZWlnaHQ9IjQwIiB4PSIxMSIgeT0iMTY4IiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiIHJ4PSIxNC40IiByeT0iMTQuNCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSIgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTQ4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxODhweDttYXJnaW4tbGVmdDoxMnB4Ij48ZGl2IGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiIHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5Jc0Rvb3JDbG9zZWQ8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iODYiIHk9IjE5MyIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+SXNEb29yQ2xvc2VkPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB3aWR0aD0iMTIwIiBoZWlnaHQ9IjQwIiB4PSIxNzkuNjIiIHk9IjEwMSIgZmlsbD0iI0ZGRiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5IiBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMThweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjEyMXB4O21hcmdpbi1sZWZ0OjE4MXB4Ij48ZGl2IGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiIHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5PcGVuRG9vcjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIyNDAiIHk9IjEyNiIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+T3BlbkRvb3I8L3RleHQ+PC9zd2l0Y2g+PC9nPjxwYXRoIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDE1OS42MiA1MSBMIDIzNC4yMiA5Ny42MiIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGZpbGw9IiMwMDAiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDIzOC42NyAxMDAuNDEgTCAyMzAuODggOTkuNjcgTCAyMzQuMjIgOTcuNjIgTCAyMzQuNTkgOTMuNzMgWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDE1OS42MiA1MSBMIDkxLjI3IDk3LjQyIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gODYuOTIgMTAwLjM3IEwgOTAuNzUgOTMuNTQgTCA5MS4yNyA5Ny40MiBMIDk0LjY4IDk5LjMzIFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB3aWR0aD0iMTIwIiBoZWlnaHQ9IjQwIiB4PSI5OS42MiIgeT0iMTEiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSIgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDozMXB4O21hcmdpbi1sZWZ0OjEwMXB4Ij48ZGl2IGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiIHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5GYWxsYmFjazwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIxNjAiIHk9IjM2IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5GYWxsYmFjazwvdGV4dD48L3N3aXRjaD48L2c+PHBhdGggZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gODYgMTQxIEwgODYgMTYxLjYzIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gODYgMTY2Ljg4IEwgODIuNSAxNTkuODggTCA4NiAxNjEuNjMgTCA4OS41IDE1OS44OCBaIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHJlY3Qgd2lkdGg9IjEyMCIgaGVpZ2h0PSI0MCIgeD0iMjYiIHk9IjEwMSIgZmlsbD0iI0ZGRiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5IiBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMThweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjEyMXB4O21hcmdpbi1sZWZ0OjI3cHgiPjxkaXYgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyIgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPkludmVydGVyPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IHg9Ijg2IiB5PSIxMjYiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkludmVydGVyPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB3aWR0aD0iMTkwIiBoZWlnaHQ9IjkwIiB4PSIzODEiIHk9IjExIiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiIHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgZmxleC1lbmQ7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTg4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDo4N3B4O21hcmdpbi1sZWZ0OjM4MnB4Ij48ZGl2IGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiIHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5PcGVuRG9vcjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI0NzYiIHk9Ijg3IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5PcGVuRG9vcjwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3Qgd2lkdGg9IjE3MCIgaGVpZ2h0PSIzMCIgeD0iMzkxIiB5PSIyMSIgZmlsbD0iI2ZmZjJjYyIgc3Ryb2tlPSIjZDZiNjU2IiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5IiBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxNjhweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjM2cHg7bWFyZ2luLWxlZnQ6MzkycHgiPjxkaXYgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyIgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPjxmb250IHN0eWxlPSJmb250LXNpemU6MTVweCI+X3NraXBJZiA9ICZxdW90OyFkb29yX2Nsb3NlZCZxdW90OzwvZm9udD48L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iNDc2IiB5PSI0MSIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+X3NraXBJZiA9ICZxdW90OyFkb29yX2NsLi4uPC90ZXh0Pjwvc3dpdGNoPjwvZz48L2c+PC9zdmc+)

之前的方法：

```xml
<Fallback>
    <Inverter>
        <IsDoorClosed/>
    </Inverter>
    <OpenDoor/>
</Fallback>
```

如果不使用自定义的条件节点 **IsDoorOpen**，而是将布尔值存储在名为 `door_closed` 的条目中，XML 可以改写为：

```xml
<OpenDoor _skipIf="!door_closed"/>
```

### 后置条件

| 名称              | 说明                             |
| --------------- | ------------------------------ |
| **\_onSuccess** | 节点返回 SUCCESS 时执行该脚本            |
| **\_onFailure** | 节点返回 FAILURE 时执行该脚本            |
| **\_post**      | 节点返回 SUCCESS 或 FAILURE 时均执行该脚本 |
| **\_onHalted**  | 运行中节点被中断时执行脚本                  |

#### 示例

在[关于子树的教程](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_06_subtree_ports)中，看到如何根据 **MoveBase** 的结果写入特定黑板变量。

左侧展示了在 BT.CPP 3.x 中如何实现该逻辑，以及使用后置条件后语法简化的效果。此外，新语法支持 **枚举类型**。

![img](https://www.behaviortree.dev/assets/images/post_example-a0dd14431e604464b8bed24a2f411fc9.svg)

旧版本：

```xml
<Fallback>
    <Sequence>
        <MoveBase  goal="{target}"/>
        <SetBlackboard output_key="result" value="0" />
    </Sequence>
    <ForceFailure>
        <SetBlackboard output_key="result" value="-1" />
    </ForceFailure>
</Fallback>
```

新版实现：

```xml
<MoveBase goal="{target}" 
          _onSuccess="result:=OK"
          _onFailure="result:=ERROR"/>
```

### 设计模式：错误码

相比状态机，行为树在根据动作结果执行不同策略的模式上可能较弱。

由于行为树的返回仅限 SUCCESS 和 FAILURE，可能不够直观。

一种解决方案是在黑板中存储**结果/错误码**，但在版本 3.X 中实现较繁琐。

前置条件帮助实现更易读的代码，例如：


![error_codes.svg](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB3aWR0aD0iNjI0IiBoZWlnaHQ9IjI0MyIgY29udGVudD0iJmx0O214ZmlsZSBob3N0PSZxdW90O2FwcC5kaWFncmFtcy5uZXQmcXVvdDsgbW9kaWZpZWQ9JnF1b3Q7MjAyMi0xMC0wN1QxMDo0MDoyOC43NTNaJnF1b3Q7IGFnZW50PSZxdW90OzUuMCAoWDExOyBMaW51eCB4ODZfNjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS8xMDMuMC4wLjAgU2FmYXJpLzUzNy4zNiZxdW90OyB2ZXJzaW9uPSZxdW90OzIwLjQuMCZxdW90OyBldGFnPSZxdW90O200bzA1YmctN2FEY0liZzY1RlRBJnF1b3Q7IHR5cGU9JnF1b3Q7Z29vZ2xlJnF1b3Q7Jmd0OyZsdDtkaWFncmFtIGlkPSZxdW90O295RkN3eHVONjFLWGZNaXdWRi1lJnF1b3Q7Jmd0OzNWaExjOU13RVA0dEhIS0U4U08yazJPYmxNY01IUmpDRFBUVVVXelpGblc4cml3bkRyOGVLWkw4RHFUVUpFQXU4ZTVxMTlMMzdhNDJtZGlMVGZtR29peStoUUFuRThzSXlvbTluRmlXT2ZOTS9pVTBlNmx4WEZzcUlrb0N0YWhXck1oM3JKU0cwaFlrd0hscklRTklHTW5hU2gvU0ZQdXNwVU9Vd3E2OUxJU2svZFlNUmJpbldQa282V3Uva0lERlVqdHpqRnIvRnBNbzFtODJEV1haSUwxWUtmSVlCYkJycU95YmliMmdBRXcrYmNvRlRnUjRHaGZwOS9xSXRkb1l4U2s3eGNHU0RsdVVGT3BzRTh0TnVPdjFtajlFNHVFV3R2Z2E1Ymd5VUczUkdoNSszZFgxVjRYQXQ4UlB6UFlLUnZleEFHMTRtUjlJdnVJTFRDY3JwWnV5NjBBUm9FUTRldDJRUGlSQVd5RW5sbTN3VHhqMm8vVE94eENOTVB2WldiaE92a21wdldYL2xCU3pncVp5ZTVpbkdMMzNlZG9QTGowV1ZpWkVCWTlGb1VnRExJZ3l1SGtYRTRaWEdmS0ZkY2ZyaXV0aXRoR0ltQW9LVlNqbWpNdGJUQm5oR1h1VmtFaHNpMEZXdlVQWWNIazBZOHdxRDNrQlk5aGdSdmQ4aVhKdzV0SkRsYTQ1MVZtL3F5dkJuS213Y2JzS1ZBV3E2b3VxMkhXQzhnZVZvOFA1YXY4Q0phQXNoZ2hTbEx3SGNlSUROdDh3WTNzRkRpb1l0SkhESldGZmhmc3JSMGwzRGN1eVZKRVB3bDRKVGJSdFlVejVPV1FRejlIeW5mWVVRaDNuSU9sQThqZzQ2UFNiSEFycUs1V3JHcHhNMDJiVm5rQWF4UWxpWk51Ty9od0NwajBDeE9aWFNrd2h4YUlZTHNGSmc0TW5VZERsOHZjb21ZMVBpWEw5Q09UUTVWVDlUZlhWVVJXZzBha3J1UzNsMW1HMjJzZEpaRHYvQXRuV0JkZzJUMitibzFlZzI3dXlWL2l4d0NuZjdxZzN5QWgzaFRWMzJybHFUd2N1QzUzUXpjdGlPc0pkNFEzTU5oMkFJbzVRZHZTZ2FueEVhNzNjZURJQWJyZFloMjdMK1FBQTh4RUFtUFVBK0lSOVBzM1IvWWQwNUdUcGpodHJZQXcyM0pEekVDU05ycFc4Tk0xVFU4c2JSdlljd00wSE1tZWtJZlkrZnlEWk96NlhHZ2QzbzE1UlQ0MHZEaUEzWGYvOHZCaVNKRmxVYzdRZGhxSGwrNEkvUnVFQk55eUJ1M1lkOTVra2FvZE9kUXpVaGpkQXNUMEN4ZnBkbCt3T1UrK1MzVUhmWVFQdDRmTU8vdnIyWUJyRDJKNEZ1dU0vbTgvV0lLei9vMEVjby9Ic0hZS0w5ZDh1Y2xhdS83eXliMzRBJmx0Oy9kaWFncmFtJmd0OyZsdDsvbXhmaWxlJmd0OyIgdmVyc2lvbj0iMS4xIiB2aWV3Qm94PSItMC41IC0wLjUgNjI0IDI0MyIgc3R5bGU9ImJhY2tncm91bmQtY29sb3I6I2ZmZiI+PGc+PHJlY3Qgd2lkdGg9IjE4MSIgaGVpZ2h0PSIxMTAiIHg9IjExIiB5PSIxMjEiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSIgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBmbGV4LXN0YXJ0O2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjE3OXB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTI4cHg7bWFyZ2luLWxlZnQ6MTJweCI+PGRpdiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7IiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIj48ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+PGI+TW92ZUJhc2U8YnIvPjwvYj48YnIvPjxmb250IHN0eWxlPSJmb250LXNpemU6MTVweCI+Z29hbD17PGZvbnQgY29sb3I9IiMwMGYiPjxiPnRhcmdldDwvYj48L2ZvbnQ+fTxici8+cmV0dXJuPXtlcnJvcl9jb2RlfTxici8+PC9mb250PjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIxMDIiIHk9IjE0NiIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+TW92ZUJhc2UuLi48L3RleHQ+PC9zd2l0Y2g+PC9nPjxwYXRoIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDMwNyA1MSBMIDE1Mi41OSAxMTguNDUiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAxNDcuNzcgMTIwLjU1IEwgMTUyLjc5IDExNC41NCBMIDE1Mi41OSAxMTguNDUgTCAxNTUuNTkgMTIwLjk2IFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAzMDcgNTEgTCAzMDcgMTE0LjYzIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gMzA3IDExOS44OCBMIDMwMy41IDExMi44OCBMIDMwNyAxMTQuNjMgTCAzMTAuNSAxMTIuODggWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxwYXRoIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDMwNyA1MSBMIDQ2My42NSAxMTguNDgiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSA0NjguNDcgMTIwLjU2IEwgNDYwLjY2IDEyMSBMIDQ2My42NSAxMTguNDggTCA0NjMuNDMgMTE0LjU3IFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCB3aWR0aD0iMTIwIiBoZWlnaHQ9IjQwIiB4PSIyNDciIHk9IjExIiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiIHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjExOHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MzFweDttYXJnaW4tbGVmdDoyNDhweCI+PGRpdiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7IiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIj48ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+U2VxdWVuY2U8L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMzA3IiB5PSIzNiIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+U2VxdWVuY2U8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IHdpZHRoPSIxOTAiIGhlaWdodD0iOTAiIHg9IjIxMiIgeT0iMTIxIiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJub25lIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5IiBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGZsZXgtZW5kO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjE4OHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTk3cHg7bWFyZ2luLWxlZnQ6MjEzcHgiPjxkaXYgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyIgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOm5vbmU7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5SZWNvdmVyeU9uZTwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIzMDciIHk9IjE5NyIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+UmVjb3ZlcnlPbmU8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IHdpZHRoPSIxNzAiIGhlaWdodD0iMzAiIHg9IjIyMiIgeT0iMTMxIiBmaWxsPSIjZmZmMmNjIiBzdHJva2U9IiNkNmI2NTYiIHBvaW50ZXItZXZlbnRzPSJub25lIi8+PGcgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaD48Zm9yZWlnbk9iamVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5IiBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiPjxkaXYgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGh0bWwiIHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxNjhweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjE0NnB4O21hcmdpbi1sZWZ0OjIyM3B4Ij48ZGl2IGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiIHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czpub25lO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+PGZvbnQgc3R5bGU9ImZvbnQtc2l6ZToxNXB4Ij5fc2tpcElmID0gJnF1b3Q7ZXJyb3JfY29kZSE9MSZxdW90OzwvZm9udD48L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMzA3IiB5PSIxNTEiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPl9za2lwSWYgPSAmcXVvdDtlcnJvcl9jby4uLjwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3Qgd2lkdGg9IjE5MCIgaGVpZ2h0PSI5MCIgeD0iNDIyIiB5PSIxMjEiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9Im5vbmUiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiIHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgZmxleC1lbmQ7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTg4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxOTdweDttYXJnaW4tbGVmdDo0MjNweCI+PGRpdiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7IiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIj48ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6bm9uZTt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPlJlY292ZXJ5VHdvPC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IHg9IjUxNyIgeT0iMTk3IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5SZWNvdmVyeVR3bzwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3Qgd2lkdGg9IjE3MCIgaGVpZ2h0PSIzMCIgeD0iNDMyIiB5PSIxMzEiIGZpbGw9IiNmZmYyY2MiIHN0cm9rZT0iI2Q2YjY1NiIgcG9pbnRlci1ldmVudHM9Im5vbmUiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiIHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjE2OHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTQ2cHg7bWFyZ2luLWxlZnQ6NDMzcHgiPjxkaXYgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyIgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOm5vbmU7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj48Zm9udCBzdHlsZT0iZm9udC1zaXplOjE1cHgiPl9za2lwSWYgPSAmcXVvdDtlcnJvcl9jb2RlIT0yJnF1b3Q7PC9mb250PjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSI1MTciIHk9IjE1MSIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+X3NraXBJZiA9ICZxdW90O2Vycm9yX2NvLi4uPC90ZXh0Pjwvc3dpdGNoPjwvZz48L2c+PC9zdmc+)

在上述树中，我们为 **MoveBase** 添加了一个输出端口 **return**，并根据 `error_code` 的值有条件地选择序列的第二或第三个分支。

### 设计模式：状态与声明式树

尽管行为树的承诺是让我们摆脱状态机的束缚，但实际上，有时不借助状态很难推理应用逻辑。

使用状态可以使树结构更简单。例如，我们仅在机器人（或子系统）处于特定状态时，才选择树的某个分支。

考虑以下节点及其前置/后置条件：


![landing.svg](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhsaW5rPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hsaW5rIiB3aWR0aD0iMzk0IiBoZWlnaHQ9IjIwNCIgY29udGVudD0iJmx0O214ZmlsZSBob3N0PSZxdW90O2FwcC5kaWFncmFtcy5uZXQmcXVvdDsgbW9kaWZpZWQ9JnF1b3Q7MjAyMi0xMC0xMFQwODo0MDo0Ny43ODVaJnF1b3Q7IGFnZW50PSZxdW90OzUuMCAoWDExOyBMaW51eCB4ODZfNjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS8xMDMuMC4wLjAgU2FmYXJpLzUzNy4zNiZxdW90OyB2ZXJzaW9uPSZxdW90OzIwLjQuMCZxdW90OyBldGFnPSZxdW90O3hiVElLTU9mUHphU3h1LV9ickdBJnF1b3Q7IHR5cGU9JnF1b3Q7Z29vZ2xlJnF1b3Q7Jmd0OyZsdDtkaWFncmFtIGlkPSZxdW90Oy1oWmhUSlJCNnVFQTZYeFJrNkY2JnF1b3Q7Jmd0OzFWYkpidHN3RVAyV0hvUWtod1JhTE5sWDIzTFRBR21LMWkxNk5HaUxXaEJKZENrNnR2djE1VEtrU0M5SVVNUUZlb21wTnd1SE0yOW00a1hUWm5kUDBicjhUREpjZTZHZjdid285Y0l3aU9PWS93aGtyNUJCTUZKQVFhc01sSHBnWHYzR0FQcUFicW9NZDQ0aUk2Um0xZG9GVjZSdDhZbzVHS0tVYkYyMW5OVHVyV3RVNENOZ3ZrTDFNZnF6eWxpcDBGSHM5L2duWEJXbHZqbndRZElnclF4QVY2S01iQzBvbW5uUmxCTEMxS25aVFhFdGtxZnpvdXcrbnBHYXdDaHUyVnNNUW1Yd2d1b052TTBMazVxYlRwYjhVSWpESTlsaU91YkpaWnNNYXluM2FCU01CVDFFY3NLajRJOWtlOGhjOG10RHRPQzJrM1VkYzRVZ1h1K1VHY2pQdTg0b2FmRUNtWEI4NFhjNE1jQXd0VUpVOTE4b3BtNk4ycE9PVnFRbVZEbWh4UkpkOHdwTStmbmc1MGE2OHVXdE9XcXFlcTlNR3RJUzducUZMWGtmbFE5QitRenYyQzJxcTZKVmdvNGh5azdHcXhKejJJMHg1d1AvbzZobXZqUS9Zc2tRanFUaUxEZ1VDMmJFbkVldjZRWkdWOVB3cjl5RXZSdEZUaU1SZjBRRk9BREZqSFVWdEk1S2dWWlE2ZU5mWTZrcUN5dGtycWI4S3RSNTBUMVg2NGRjcWh1bnZxdlBFODd3QnltOWVody9wUTlQOTR0dnM2OC9adlB2cy9UcWhJRWR0OHlCRGw3ZjJyL1lmVTFJeWFiTnNFcU9qR2V5TFN1RzU1SWxBdHp5d2txOFpJMnNiUnFZMTgvaDhhbVlzUUtyNm5vcUdhcjlSM21laDZzVnZJcVNaM3dnejVKbEVpZFNyaGduSkRYT0lSRThqS290SHVFN0hSNDg1Z1ZUVHRZM0VjU0lERGZ1TVdrd28zdXBiamtKQjhPN3VIZTB0M3drdzU1bi9sYU9aeU9NQXN1b1ZDTzZGMXAycU92eHdvcmhrTDN5MDFEWUJ0M0dBcjJqRGpTalNrMlQxd2JxMFV3TG5RbGttQ0o0NHJBRU9BSU1FZnl3MkNHNUVVMUVvU3ErNU1acXFxUk5sV1UxTnRkQUhjL3RtY0JzTDd0cVBoaUVBMWg0c1BHREpBRmcyeS9RYUFoWWFTL1BFWUFJbG5aZmpuNnZlYVlJcDlkY2RIYk52WGtkRE02c0F6TXI1Q1lTVGE4MVlFSkVhZnBsQVJQQ01iOXNSUWVlM2V2Q2wrNXp0OHVsQkRwYzl6ZDB0OXZib3JQZmlRdXlkeDAyREUrd0lRNjFtczJINkIzb01MZ2dIZGFrWStmSW9NQnIrNytYTUVHTnFLSWlnNVQ3ZDhHTjlNRG5qQytJTTVPdTlKcG4rQit5YVBRL3NXamtYNUpGSHN6MFhtWk44MmoyQnc9PSZsdDsvZGlhZ3JhbSZndDsmbHQ7L214ZmlsZSZndDsiIHZlcnNpb249IjEuMSIgdmlld0JveD0iLTAuNSAtMC41IDM5NCAyMDQiIHN0eWxlPSJiYWNrZ3JvdW5kLWNvbG9yOiNmZmYiPjxnPjxyZWN0IHdpZHRoPSIzNzAiIGhlaWdodD0iMTgwIiB4PSIxMSIgeT0iMTEiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSIgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MzY4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxMDFweDttYXJnaW4tbGVmdDoxMnB4Ij48ZGl2IGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiIHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiPjxkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj48Yj5Mb3dlckFsdGl0dWRlPC9iPjxici8+PGZvbnQgc3R5bGU9ImZvbnQtc2l6ZToxNXB4Ij48YnIvPmRyb25lX2FsdGl0dWRlID17YWx0aXR1ZGV9PC9mb250Pjxmb250IHN0eWxlPSJmb250LXNpemU6MTVweCI+PHNwYW4gc3R5bGU9ImNvbG9yOnRyYW5zcGFyZW50O2ZvbnQtZmFtaWx5Om1vbm9zcGFjZTtmb250LXNpemU6MDt0ZXh0LWFsaWduOnN0YXJ0Ij50aXR1ZGUzQ214R3JhcGhNb2RlbCUzRSUzQ3Jvb3QlM0UlM0NteENlbGwlMjBpZCUzRCUyMjAlMjIlMkYlM0UlM0NteENlbGwlMjBpZCUzRCUyMjElMjIlMjBwYXJlbnQlM0QlMjIwJTIyJTJGJTNFJTNDbXhDZWxsJTIwaWQlM0QlMjIyJTIyJTIwdmFsdWUlM0QlMjIlMjZsdCUzQmZvbnQlMjBzdHlsZSUzRCUyNnF1b3QlM0Jmb250LXNpemUlM0ElMjAxNXB4JTNCJTI2cXVvdCUzQiUyNmd0JTNCX3NraXBJZiUyMCUzRCUyMCUyNnF1b3QlM0JzdGF0ZSElM0QmYXBvcztMQU5ESU5HX1JFUVVFU1RFRCZhcG9zOyUyMCUyNnF1b3QlM0IlMjZsdCUzQiUyRmZvbnQlMjZndCUzQiUyMiUyMHN0eWxlJTNEJTIycm91bmRlZCUzRDAlM0J3aGl0ZVNwYWNlJTNEd3JhcCUzQmh0bWwlM0QxJTNCZm9udFNpemUlM0QxOCUzQmZpbGxDb2xvciUzRCUyM2ZmZjJjYyUzQnN0cm9rZUNvbG9yJTNEJTIzZDZiNjU2JTNCYWxpZ24lM0RsZWZ0JTNCc3BhY2luZ0xlZnQlM0Q3JTNCJTIyJTIwdmVydGV4JTNEJTIyMSUyMiUyMHBhcmVudCUzRCUyMjElMjIlM0UlM0NteEdlb21ldHJ5JTIweCUzRCUyMjI0Ny41JTIyJTIweSUzRCUyMjE2NzAlMjIlMjB3aWR0aCUzRCUyMjMxNSUyMiUyMGhlaWdodCUzRCUyMjMwJTIyJTIwYXMlM0QlMjJnZW9tZXRyeSUyMiUyRiUzRSUzQyUyRm14Q2VsbCUzRSUzQyUyRnJvb3QlM0UlM0MlMkZteEdyYXBoTW9kZWwlM0U8L3NwYW4+PGJyLz48L2ZvbnQ+PC9kaXY+PC9kaXY+PC9kaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IHg9IjE5NiIgeT0iMTA2IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5Mb3dlckFsdGl0dWRlLi4uPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCB3aWR0aD0iMzUyLjUiIGhlaWdodD0iMzAiIHg9IjE4LjUiIHk9IjIxIiBmaWxsPSIjZmZmMmNjIiBzdHJva2U9IiNkNmI2NTYiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoPjxmb3JlaWduT2JqZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiIHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCI+PGRpdiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94aHRtbCIgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgZmxleC1zdGFydDt3aWR0aDozNDRweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjM2cHg7bWFyZ2luLWxlZnQ6MjhweCI+PGRpdiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7IiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246bGVmdCI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE0cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPjxmb250IHN0eWxlPSJmb250LXNpemU6MTRweCI+X3NraXBJZiA9ICZxdW90O3N0YXRlIT1ET19MQU5ESU5HJnF1b3Q7PC9mb250PjwvZGl2PjwvZGl2PjwvZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCB4PSIyOCIgeT0iNDAiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTQiPl9za2lwSWYgPSAmcXVvdDtzdGF0ZSE9RE9fTEFORElORyZxdW90OzwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3Qgd2lkdGg9IjM1Mi41IiBoZWlnaHQ9IjMwIiB4PSIxOC41IiB5PSIxNTEiIGZpbGw9IiNmZmYyY2MiIHN0cm9rZT0iI2Q2YjY1NiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2g+PGZvcmVpZ25PYmplY3Qgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSIgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0Ij48ZGl2IHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBmbGV4LXN0YXJ0O3dpZHRoOjM0NHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTY2cHg7bWFyZ2luLWxlZnQ6MjhweCI+PGRpdiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7IiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246bGVmdCI+PGRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPjxmb250IHN0eWxlPSJmb250LXNpemU6MTRweCI+X3Bvc3QgPSAmcXVvdDtzdGF0ZT0gKGFsdGl0dWRlICZsdDs9IDAuMSkgPyBMQU5ERUQgOiBzdGF0ZSZxdW90OzwvZm9udD48L2Rpdj48L2Rpdj48L2Rpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgeD0iMjgiIHk9IjE3MSIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCI+X3Bvc3QgPSAmcXVvdDtzdGF0ZT0gKGFsdGl0dWRlICZsdDs9IDAuMSkgPyBMQS4uLjwvdGV4dD48L3N3aXRjaD48L2c+PC9nPjwvc3ZnPg==)

该节点仅在状态等于 **DO\_LANDING** 时执行，一旦 `altitude` 足够低，状态将变为 **LANDED**。

注意，DO\_LANDING 和 LANDED 是枚举类型，不是字符串。

> 💡 提示
> 这种模式的一个意外效果是使节点更加**声明式**，即更容易将该特定节点/子树移动到树的其他部分。

## 异步动作

设计响应式行为树时，需要理解两个主要概念：

* **异步**动作与**同步**动作的区别。
* BT.CPP 中**并发（Concurrency）**与**并行（Parallelism）**的差异。

### 并发与并行

搜索相关内容可获大量优质文章。

> **并发**指两个或多个任务在时间上有重叠启动、运行和完成，但不一定在同一时刻运行。
> **并行**指任务在不同线程（如多核处理器）上同时运行。

BT.CPP 以**单线程**顺序执行所有节点的 `tick()` 方法，即**并发执行**。

* 树执行引擎是单线程的。
* 所有 `tick()` 按顺序执行。
* 若任一 `tick()` 阻塞，整个执行流被阻塞。

响应性通过“并发”与异步执行实现。

换言之，耗时动作应尽快返回 RUNNING 状态，告知树该动作已启动，需要时间完成（返回 SUCCESS 或 FAILURE）。需要重复 tick 该节点以检测状态变化（轮询）。

异步节点可将长时间执行委托给其它线程或进程。

### 异步与同步

一般来说，异步节点应满足：

* tick 时可能返回 RUNNING（非 SUCCESS 或 FAILURE）。
* 调用 `halt()` 时能尽快停止。

通常，开发者需实现 `halt()` 方法。

当异步动作返回 RUNNING，状态通常**向上传播**，整个树视为 RUNNING。

示例中，“ActionE”为异步且处于 RUNNING，运行时其父节点通常也返回 RUNNING。

![tree in running state](https://www.behaviortree.dev/assets/images/RunningTree-6247b58f3119ffcc695094305dfd07c7.svg)

### 简单示例 SleepNode

基于 `StatefulActionNode`，异步节点的良好模板：

```cpp
using namespace std::chrono;

class SleepNode : public BT::StatefulActionNode
{
  public:
    SleepNode(const std::string& name, const BT::NodeConfig& config)
      : BT::StatefulActionNode(name, config)
    {}

    static BT::PortsList providedPorts()
    {
      return { BT::InputPort<int>("msec") };
    }

    NodeStatus onStart() override
    {
      int msec = 0;
      getInput("msec", msec);

      if( msec <= 0 ) {
        return NodeStatus::SUCCESS;
      }
      else {
        deadline_ = system_clock::now() + milliseconds(msec);
        return NodeStatus::RUNNING;
      }
    }

    NodeStatus onRunning() override
    {
      if ( system_clock::now() >= deadline_ ) {
        return NodeStatus::SUCCESS;
      }
      else {
        return NodeStatus::RUNNING;
      }
    }

    void onHalted() override
    {
      std::cout << "SleepNode interrupted" << std::endl;
    }

  private:
    system_clock::time_point deadline_;
};
```

说明：

1. 第一次 tick 时执行 `onStart()`，若睡眠时间为 0，立即返回 SUCCESS，否则返回 RUNNING 并设定截止时间。
2. 继续 tick 树时调用 `onRunning()`，返回 RUNNING 或 SUCCESS。
3. 其他节点可触发 `halt()`，执行 `onHalted()`。

### 避免阻塞树的执行

以下实现是错误示范：

```cpp
class BadSleepNode : public BT::ActionNodeBase
{
  public:
    BadSleepNode(const std::string& name, const BT::NodeConfig& config)
      : BT::ActionNodeBase(name, config)
    {}

    static BT::PortsList providedPorts()
    {
      return { BT::InputPort<int>("msec") };
    }

    NodeStatus tick() override
    {  
      int msec = 0;
      getInput("msec", msec);
      // 该阻塞函数将冻结整个树 :(
      std::this_thread::sleep_for(milliseconds(msec));
      return NodeStatus::SUCCESS;
    }

    void halt() override
    {
      // 无法调用此方法，因为树已冻结
      // 即使可调用，也无法中断 sleep_for()
    }
};
```

### 多线程的问题

在该库早期版本（1.x）中，启动新线程看似是实现异步动作的好方法。

但这是一个糟糕的主意，原因包括：

* 线程安全地访问黑板更困难（后面会详细讲）。
* 很可能根本不需要这么做。
* 用户误以为启动线程就自动异步，但实际上**必须自行负责**在线程停止时，且在调用 `halt()` 时**快速终止线程**。

因此，通常不建议继承 `BT::ThreadedAction`。回顾之前的 SleepNode 示例：

```cpp
// 会启动自己的线程，但 halt 时仍有问题
class BadSleepNode : public BT::ThreadedAction
{
  public:
    BadSleepNode(const std::string& name, const BT::NodeConfig& config)
      : BT::ActionNodeBase(name, config)
    {}

    static BT::PortsList providedPorts()
    {
      return{ BT::InputPort<int>("msec") };
    }

    NodeStatus tick() override
    {  
      int msec = 0;
      getInput("msec", msec);
      // 线程内执行，树仍运行，但线程无法中断
      std::this_thread::sleep_for( std::chrono::milliseconds(msec) );
      return NodeStatus::SUCCESS;
    }
    // halt() 无法终止线程 :(
};
```

正确实现示例：

```cpp
class ThreadedSleepNode : public BT::ThreadedAction
{
  public:
    ThreadedSleepNode(const std::string& name, const BT::NodeConfig& config)
      : BT::ActionNodeBase(name, config)
    {}

    static BT::PortsList providedPorts()
    {
      return{ BT::InputPort<int>("msec") };
    }

    NodeStatus tick() override
    {  
      int msec = 0;
      getInput("msec", msec);

      using namespace std::chrono;
      const auto deadline = system_clock::now() + milliseconds(msec);

      // 定期检查是否请求 halt，并每次休眠 1 毫秒
      while( !isHaltRequested() && system_clock::now() < deadline )
      {
        std::this_thread::sleep_for( std::chrono::milliseconds(1) );
      }
      return NodeStatus::SUCCESS;
    }

    // halt() 会设置 isHaltRequested() 为 true，结束循环
};
```

如你所见，这比使用 `BT::StatefulActionNode` 更复杂。虽然某些情况下仍有用，但默认应避免多线程。

### 高级示例：客户端/服务器通信

很多用户让 BT.CPP 在不同进程执行任务。

ROS 中常用 [ActionLib](http://wiki.ros.org/actionlib) 实现：

1. 非阻塞函数启动动作。
2. 监控动作执行状态。
3. 获取结果或错误信息。
4. 可中断/终止正在执行的动作。

所有操作均非阻塞，无需启动新线程。

一般假设开发者已有进程间通信机制，BT 执行器作为客户端，实际服务为服务器。

## 端口与黑板

**BT.CPP** 是目前唯一引入**输入/输出端口**（Ports）概念的行为树实现，作为替代黑板的方案。

端口提供对黑板的间接接口，赋予额外语义。

### BT.CPP 目标

#### 模型驱动开发

目的是构建节点的“模型”，即元信息，描述节点如何与树中其他节点交互。

模型对开发者（自文档化）和外部工具（如 Groot2 编辑器、静态分析器）均重要。

数据流描述必须是模型一部分，且需明确黑板条目是被写（输出）、读（输入）还是两者。

示例：

![no\_ports](https://www.behaviortree.dev/assets/images/no_ports_sequence-ea57978d7fe40152ab9a04893096afc4.png)

其他实现（或不当用法）只能通过：

* 查看代码（不理想）
* 依赖文档（可能过时）

有端口后，节点意图及关系更明显：

![with\_ports](https://www.behaviortree.dev/assets/images/with_ports_sequence-610186a2466dfe975d67c2fd03f3a031.png)

#### 节点可组合性与子树作用域

理想是支持不同开发者实现的节点在行为设计师平台中组合。

直接使用黑板时，名称冲突问题突出，常见名如 `goal`、`result`、`target`、`value`。

比如，`GraspObject` 和 `MoveBase` 由不同人开发，均读 `target`，但含义及类型不同。

端口提供一层间接（重映射），详见[教程2](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_02_basic_ports)。

即端口名称“写死”于代码中，但可在 XML 中重映射为不同黑板条目，无需改代码。

子树重映射类似，详见[教程6](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_06_subtree_ports)。黑板本质上是全局变量映射，扩展性差，故不推荐。

端口重映射解决此问题。

### 总结：避免直接使用黑板

推荐写法：

```c++
// 代码示例
getInput("goal", goal);
setOutput("result", result);
```

尽量避免：

```c++
// 不推荐写法
config().blackboard->get("goal", goal);
config().blackboard->set("result", result);
```

尽管两者技术上可行，但后者被视为不良实践：

问题包括：

1. 名称硬编码，修改需重编译；端口可运行时通过 XML 重映射。
2. 不易知晓节点读写黑板的条目，只能看代码或依赖文档，端口模型自文档化。
3. 工厂无法感知黑板访问，端口能检查端口连接正确性。
4. 子树使用时可能失效。
5. `convertFromString()` 模板特化不正常。


# 节点库

本节列出库中原生实现的 TreeNodes。

## 装饰器（Decorators）

装饰器节点必须只有一个子节点。

装饰器负责决定是否、何时以及执行多少次子节点的 tick。

### Inverter

对子节点执行一次 tick：

* 如果子节点返回 FAILURE，则返回 SUCCESS。
* 如果子节点返回 SUCCESS，则返回 FAILURE。
* 如果子节点返回 RUNNING，则返回 RUNNING。

### ForceSuccess

* 子节点返回 RUNNING 时，返回 RUNNING。
* 否则总返回 SUCCESS。

### ForceFailure

* 子节点返回 RUNNING 时，返回 RUNNING。
* 否则总返回 FAILURE。

### Repeat

对子节点执行 tick，最多执行 N 次（在同一次 tick 中），其中 N 由输入端口 `num_cycles` 指定，只要子节点返回 SUCCESS 即继续。

如果子节点每次都返回 SUCCESS，N 次重复后返回 SUCCESS。

如果子节点返回 FAILURE，则中断循环并返回 FAILURE。

如果子节点返回 RUNNING，则本节点返回 RUNNING，下次 tick 时重复计数不递增。

### RetryUntilSuccessful

对子节点执行 tick，最多执行 N 次，N 由输入端口 `num_attempts` 指定，只要子节点返回 FAILURE 即继续。

如果子节点每次都返回 FAILURE，N 次尝试后返回 FAILURE。

如果子节点返回 SUCCESS，则中断循环并返回 SUCCESS。

如果子节点返回 RUNNING，则本节点返回 RUNNING，下次 tick 时尝试计数不递增。

### KeepRunningUntilFailure

本节点始终返回 FAILURE（子节点返回 FAILURE）或 RUNNING（子节点返回 SUCCESS 或 RUNNING）。

### Delay

延迟指定时间后对子节点执行 tick，延迟时间由输入端口 `delay_msec` 指定。

如果子节点返回 RUNNING，本节点返回 RUNNING，并于下次 tick 时继续 tick 子节点。

否则，返回子节点状态。

### RunOnce

本节点用于只执行子节点一次。

若子节点为异步，将持续 tick 直到返回 SUCCESS 或 FAILURE。

执行完成后，可通过输入端口 `then_skip` 设置：

* TRUE（默认）：未来跳过本节点。
* FALSE：持续同步返回子节点返回状态。

### PreCondition

参见[脚本语言介绍](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_09_scripting#script-and-precondition-nodes)。

### SubTree

参见[使用子树组合行为](https://www.behaviortree.dev/docs/tutorial-basics/tutorial_05_subtrees)。

### 需在 C++ 中注册的其他装饰器

#### ConsumeQueue

当队列非空时执行子节点。

每次迭代弹出类型为 T 的元素放入 `popped_item`。

空队列返回 SUCCESS。

注册示例：

```cpp
factory.registerNodeType<ConsumeQueue<Pose2D>>("ConsumeQueue");
```

参见[ex04\_waypoints.cpp](https://github.com/BehaviorTree/BehaviorTree.CPP/blob/master/examples/ex04_waypoints.cpp)。

#### SimpleDecoratorNode

使用

```cpp
void BehaviorTreeFactory::registerSimpleDecorator("MyDecorator", tick_function, ports)
```

注册简单装饰器节点。内部使用 `SimpleDecoratorNode`，`tick_function` 为

```cpp
std::function<NodeStatus(NodeStatus, TreeNode&)>
```

类型函数，`ports` 为 `PortsList` 类型。

---

## 回退节点（Fallbacks）

该类节点在其他框架中称为“Selector”或“Priority”。

其目的是尝试不同策略，直到找到一个“成功”的。

当前框架提供两种节点：

* Fallback
* ReactiveFallback

它们遵循规则：

* 在 tick 第一个子节点前，节点状态变为 RUNNING。
* 子节点返回 FAILURE 时，fallback tick 下一个子节点。
* 若最后一个子节点也返回 FAILURE，停止所有子节点，返回 FAILURE。
* 子节点返回 SUCCESS 时，停止所有子节点，返回 SUCCESS。

两者差异：

| 控制节点类型           | 子节点返回 RUNNING    |
| ---------------- | ---------------- |
| Fallback         | 再次 tick 同一子节点    |
| ReactiveFallback | 重新从第一个子节点开始 tick |

* “重新开始”意味着 fallback 从头开始 tick。
* “再次 tick”意味着下次 tick 仍执行当前子节点，之前失败的兄弟节点不再 tick。

### Fallback 示例

本例尝试多种开门策略。首先检查门是否已开（只检查一次）。


![FallbackNode](data:image/png;base64,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)

### ReactiveFallback

当需要在某个异步子节点的前置条件从 FAILURE 变为 SUCCESS 时，立即中断该异步子节点，使用该控制节点。

例如，角色最多睡眠 8 小时；如果其已经充分休息，节点 `areYouRested?` 返回 SUCCESS，则异步节点 `Timeout (8 hrs)` 和 `Sleep` 会被中断。

![ReactiveFallback](data:image/png;base64,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)

## 序列（Sequences）

**Sequence** 节点会依次 tick 所有子节点，只要它们返回 SUCCESS；若任一子节点返回 FAILURE，则序列中断。

当前框架提供三种序列节点：

* Sequence
* SequenceWithMemory
* ReactiveSequence

它们遵循以下规则：

* 在 tick 第一个子节点前，节点状态变为 **RUNNING**。
* 子节点返回 **SUCCESS** 时，继续 tick 下一个子节点。
* 如果最后一个子节点也返回 **SUCCESS**，停止所有子节点，序列返回 **SUCCESS**。

三者差异如下表：

| 控制节点类型             | 子节点返回 FAILURE | 子节点返回 RUNNING |
| ------------------ | ------------- | ------------- |
| Sequence           | 从头重新开始        | 再次 tick 当前子节点 |
| ReactiveSequence   | 从头重新开始        | 从头重新开始        |
| SequenceWithMemory | 再次 tick 当前子节点 | 再次 tick 当前子节点 |

* “从头重新开始”表示整个序列从第一个子节点重新 tick。
* “再次 tick 当前子节点”表示下次 tick 时继续执行当前子节点，之前成功的兄弟节点不再 tick。

### Sequence 示例

该树描述了计算机游戏中狙击手的行为。

![SequenceNode](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnN2Zz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhodG1sPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIiBpZD0ic3ZnNzAiIHdpZHRoPSI1NzEiIGhlaWdodD0iMTgxIiBjb250ZW50PSImbHQ7bXhmaWxlIGhvc3Q9JnF1b3Q7YXBwLmRpYWdyYW1zLm5ldCZxdW90OyBtb2RpZmllZD0mcXVvdDsyMDIyLTAxLTE0VDIyOjE4OjQyLjQ5NlomcXVvdDsgYWdlbnQ9JnF1b3Q7NS4wIChYMTEpJnF1b3Q7IHZlcnNpb249JnF1b3Q7MTYuMi43JnF1b3Q7IGV0YWc9JnF1b3Q7U2s1d21VOXJXb2RvX1lDdnhnOFkmcXVvdDsgdHlwZT0mcXVvdDtnb29nbGUmcXVvdDsmZ3Q7Jmx0O2RpYWdyYW0gaWQ9JnF1b3Q7UUR2N1lNMkRfU19ZZDFTdkp5TEkmcXVvdDsmZ3Q7MVpkZGs1b3dGSVovRGJjZElTcDR1ZXZhajVudFRaMXB1NWVwSENHZGtFTkRVT2l2YjVDRGd1aCtkUjNjRytXOEp6a2t6eHRJY05nOEtUNXBuc1pmTVFUcGVLT3djTmlkNDNtKzc5cmZTaWhyWWN6OFdvaTBDR3ZKUFFoTDhSZElISkdhaXhDeVRrT0RLSTFJdStJS2xZS1Y2V2hjYTl4Mm02MVJkdSthOGdoNnduTEZaVi85SVVJVGsrcE9aNGZFWnhCUlRMY09QSnBmd3B2R05KTXM1aUZ1V3hKYk9HeXVFVTE5bFJSemtCVzdoa3ZkNytPWjdINWdHcFI1VGdlUGhtSEtabTRhY3hWQ2xSNDU3QmExaVRGQ3hlVTlZbXBGMTRxL3daaVNYT0c1UVN2RkpwR1VoVUtZbjFYM0R4T0tIbHFadTRJcTc0S1NnalVxUXdYZG9Fb3FvOHU2aUQ5cDRvZW1aeFVjNnV5aXBsQTlIUWlQL01zdzF5dVNwclJndUk2QUdNMzYyTnk5R1hZUkF5Wmc3MktiYUpEY2lFMjNPcWZsRk8zYkhZamJDNEorMmdEV002QWEvSkpDaGNyKzNRN2lTY3VERjFsdzdPWHJMR2tlOVNFOEdiOEhUN3dCVFBHSDgyVHlIandad0pKZ09FdG9OQnN1Y3lxNmhEODVLRHZheHplVmJTd01MRk8rbTliV25oQzZKcHdHc3dGdG9EaTd6WjJaTkhWZ0kzcWQwSUhEcDNEYjJyMmJMVGx1YmR6VDBmOWo4bnVZYmtSeVl4WUtrdkxxUUkyUFFMbXpFNlRZaFVnRi9RVVZWeWVoYTRNMG1UNE5LYmdRbzFtUDBaZHN0NVMraTB6OGtvODlldTdMWVhHOW9wQk4zNGFkLzR6MU5ia1F1K1pNMFlJbnNtOWlMZUVlZWNYb3l0blpwL05wZU9PM2dXZkR3MWZJTHRmNmxHT0xmdz09Jmx0Oy9kaWFncmFtJmd0OyZsdDsvbXhmaWxlJmd0OyIgdmVyc2lvbj0iMS4xIiB2aWV3Qm94PSItMC41IC0wLjUgNTcxIDE4MSI+PG1ldGFkYXRhIGlkPSJtZXRhZGF0YTc0Ii8+PGcgaWQ9Imc2MCI+PHBhdGggaWQ9InBhdGg0IiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyOTAgNjAgTCAxMTguNTMgMTE3Ljk2IiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggaWQ9InBhdGg2IiBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAxMTMuNTYgMTE5LjY0IEwgMTE5LjA3IDExNC4wOCBMIDExOC41MyAxMTcuOTYgTCAxMjEuMzEgMTIwLjcyIFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBpZD0icGF0aDgiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDI5MCA2MCBMIDI0NC4wOCAxMTUuMTEiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBpZD0icGF0aDEwIiBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyNDAuNzIgMTE5LjE0IEwgMjQyLjUxIDExMS41MiBMIDI0NC4wOCAxMTUuMTEgTCAyNDcuODkgMTE2IFoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBpZD0icGF0aDEyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyOTAgNjAgTCAzNTcuNTkgMTE1Ljk0IiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggaWQ9InBhdGgxNCIgZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gMzYxLjY0IDExOS4yOSBMIDM1NC4wMSAxMTcuNTIgTCAzNTcuNTkgMTE1Ljk0IEwgMzU4LjQ4IDExMi4xMyBaIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggaWQ9InBhdGgxNiIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Ik0gMjkwIDYwIEwgNTIzLjgyIDExOC40NiIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGlkPSJwYXRoMTgiIGZpbGw9IiMwMDAiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDUyOC45MiAxMTkuNzMgTCA1MjEuMjggMTIxLjQzIEwgNTIzLjgyIDExOC40NiBMIDUyMi45NyAxMTQuNjQgWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IGlkPSJyZWN0MjAiIHdpZHRoPSIxMjAiIGhlaWdodD0iNjAiIHg9IjIzMCIgeT0iMCIgZmlsbD0iI0ZGRiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgaWQ9ImcyNiIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoMjQiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTE4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDozMHB4O21hcmdpbi1sZWZ0OjIzMXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5TZXF1ZW5jZTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDIyIiB4PSIyOTAiIHk9IjM1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5TZXF1ZW5jZTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3QyOCIgd2lkdGg9IjEzMCIgaGVpZ2h0PSI2MCIgeD0iMzMwIiB5PSIxMjAiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIGlkPSJnMzQiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDMyIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjEyOHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTUwcHg7bWFyZ2luLWxlZnQ6MzMxcHgiPjx4aHRtbDpkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPkFpbUF0RW5lbXk8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQzMCIgeD0iMzk1IiB5PSIxNTUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkFpbUF0RW5lbXk8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IGlkPSJyZWN0MzYiIHdpZHRoPSI4MCIgaGVpZ2h0PSI2MCIgeD0iNDkwIiB5PSIxMjAiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIGlkPSJnNDIiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDQwIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjc4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxNTBweDttYXJnaW4tbGVmdDo0OTFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+U2hvb3Q8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQzOCIgeD0iNTMwIiB5PSIxNTUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPlNob290PC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCBpZD0icmVjdDQ0IiB3aWR0aD0iMTUwIiBoZWlnaHQ9IjYwIiB4PSIwIiB5PSIxMjAiIGZpbGw9IiNGRkYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIgcng9IjIxLjYiIHJ5PSIyMS42Ii8+PGcgaWQ9Imc1MCIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoNDgiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTQ4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxNTBweDttYXJnaW4tbGVmdDoxcHgiPjx4aHRtbDpkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPklzRW5lbXlWaXNpYmxlPC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IGlkPSJ0ZXh0NDYiIHg9Ijc1IiB5PSIxNTUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPklzRW5lbXlWaXNpYmxlPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCBpZD0icmVjdDUyIiB3aWR0aD0iMTQwIiBoZWlnaHQ9IjYwIiB4PSIxNzAiIHk9IjEyMCIgZmlsbD0iI0ZGRiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIiByeD0iMjEuNiIgcnk9IjIxLjYiLz48ZyBpZD0iZzU4IiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoIGlkPSJzd2l0Y2g1NiI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMzhweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjE1MHB4O21hcmdpbi1sZWZ0OjE3MXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5pc1JpZmxlTG9hZGVkPC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IGlkPSJ0ZXh0NTQiIHg9IjI0MCIgeT0iMTU1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5pc1JpZmxlTG9hZGVkPC90ZXh0Pjwvc3dpdGNoPjwvZz48L2c+PC9zdmc+)

### ReactiveSequence

该节点特别适合持续检查条件，但用户在使用异步子节点时需谨慎，确保它们不会被过于频繁地 tick。

下面来看一个示例：


![ReactiveSequence](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnN2Zz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhodG1sPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIiBpZD0ic3ZnNDYiIHdpZHRoPSIzMjEiIGhlaWdodD0iMTgxIiBjb250ZW50PSImbHQ7bXhmaWxlIGhvc3Q9JnF1b3Q7YXBwLmRpYWdyYW1zLm5ldCZxdW90OyBtb2RpZmllZD0mcXVvdDsyMDIyLTAxLTE0VDIyOjEzOjU2LjQyMVomcXVvdDsgYWdlbnQ9JnF1b3Q7NS4wIChYMTEpJnF1b3Q7IHZlcnNpb249JnF1b3Q7MTYuMi43JnF1b3Q7IGV0YWc9JnF1b3Q7dG9QQ0hDMmZmZU1mR1FmdzB2eXYmcXVvdDsgdHlwZT0mcXVvdDtnb29nbGUmcXVvdDsmZ3Q7Jmx0O2RpYWdyYW0gaWQ9JnF1b3Q7OEpTUGRWRWw5eFdNMTQ0NnlYUU0mcXVvdDsmZ3Q7N1ZiQmtwUXdFUDBhcnRaQUZuWTg2dXlzV3FVWHAwcmRZNFJlaUJYU0dKb0IvSG9ETkFNc00xdGF1dXJCRTd6WDNVbjZ2VXd6bnRqbHpTc3JpK3dkSnFDOVlKTTBucmp4Z3NBWDRjWTlPcVlkbU92b2FpQlNxeEpPbW9pRCtnWk1jbDFhcVFUS1JTSWhhbExGa296UkdJaHB3VWxyc1Y2bTNhTmU3bHJJRkZiRUlaWjZ6WDVVQ1dYTSt0SHpLZkFhVkpyeDF0dmdlZ2prY2t6bVRzcE1KbGpQS0xIM3hNNGkwdkNXTnp2UW5YaWpMa1BkN1lYbzZXQVdEUDFJUWNESG9IYnNEUkxYS2tPRHhqMWVXcXhNQWwzRnhpRzBsR0dLUnVxM2lJVWpmVWQrQWFLV2paSVZvYU15eWpWSG9WSDBxU3QvRmpLNm0wVnVHbDY1QiswSURObDJWdFRCdTNsc0t1dlJXSGVQaHZnZy90YmhvY0d1cTRWRUpWWTJab292SDBtYkFxc1dyWVgwVC9hNGV3MllnOXZWcFZqUWt0Unh1YnJrQzVhZThpWVAzQXZiY040UzhkK1NzNWFFZjg4U1BzMVI2b29YZlE4eTdyWTR3TmNLakR2MVE4K1dCdFdaSWpnVXNtK3Zka054YWNaNWdZNWdDUnJ2MGcvN1F2TmNFQWllTUR4aXhZanIyY0RhTXBmTlpsVzArWFc5d3BWZUw0ckNvb3l6dllHOC9lZkVFZy9FdWdyUGlCVStrVmpSU3F3M1pTL1RCMVdxei9xeHErWC92RnJTeGd4RjlIdkU4Nk0vSjU2RDA3ZXhqODMrWVlqOWR3PT0mbHQ7L2RpYWdyYW0mZ3Q7Jmx0Oy9teGZpbGUmZ3Q7IiB2ZXJzaW9uPSIxLjEiIHZpZXdCb3g9Ii0wLjUgLTAuNSAzMjEgMTgxIj48bWV0YWRhdGEgaWQ9Im1ldGFkYXRhNTAiLz48ZyBpZD0iZzM2Ij48cGF0aCBpZD0icGF0aDQiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDE2MCA2MCBMIDgwLjIgMTE2LjMzIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggaWQ9InBhdGg2IiBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSA3NS45MSAxMTkuMzYgTCA3OS42MSAxMTIuNDYgTCA4MC4yIDExNi4zMyBMIDgzLjY1IDExOC4xOCBaIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggaWQ9InBhdGg4IiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAxNjAgNjAgTCAyMzkuOCAxMTYuMzMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBpZD0icGF0aDEwIiBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0iTSAyNDQuMDkgMTE5LjM2IEwgMjM2LjM1IDExOC4xOCBMIDIzOS44IDExNi4zMyBMIDI0MC4zOSAxMTIuNDYgWiIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxyZWN0IGlkPSJyZWN0MTIiIHdpZHRoPSIxODAiIGhlaWdodD0iNjAiIHg9IjcwIiB5PSIwIiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48ZyBpZD0iZzE4IiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41IC0wLjUpIj48c3dpdGNoIGlkPSJzd2l0Y2gxNiI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxNzhweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjMwcHg7bWFyZ2luLWxlZnQ6NzFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+UmVhY3RpdmVTZXF1ZW5jZTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDE0IiB4PSIxNjAiIHk9IjM1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5SZWFjdGl2ZVNlcXVlbmNlPC90ZXh0Pjwvc3dpdGNoPjwvZz48cmVjdCBpZD0icmVjdDIwIiB3aWR0aD0iMTUwIiBoZWlnaHQ9IjYwIiB4PSIxNzAiIHk9IjEyMCIgZmlsbD0iI0ZGRiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgaWQ9ImcyNiIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSAtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoMjQiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTQ4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoxNTBweDttYXJnaW4tbGVmdDoxNzFweCI+PHhodG1sOmRpdiBzdHlsZT0iYm94LXNpemluZzpib3JkZXItYm94O2ZvbnQtc2l6ZTowO3RleHQtYWxpZ246Y2VudGVyIiBkYXRhLWRyYXdpby1jb2xvcnM9ImNvbG9yOiByZ2IoMCwgMCwgMCk7Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmlubGluZS1ibG9jaztmb250LXNpemU6MThweDtmb250LWZhbWlseTpIZWx2ZXRpY2E7Y29sb3I6IzAwMDtsaW5lLWhlaWdodDoxLjI7cG9pbnRlci1ldmVudHM6YWxsO3doaXRlLXNwYWNlOm5vcm1hbDtvdmVyZmxvdy13cmFwOm5vcm1hbCI+QXBwcm9hY2hFbmVteTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDIyIiB4PSIyNDUiIHk9IjE1NSIgZmlsbD0iIzAwMCIgZm9udC1mYW1pbHk9IkhlbHZldGljYSIgZm9udC1zaXplPSIxOCIgdGV4dC1hbmNob3I9Im1pZGRsZSI+QXBwcm9hY2hFbmVteTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3QyOCIgd2lkdGg9IjE1MCIgaGVpZ2h0PSI2MCIgeD0iMCIgeT0iMTIwIiBmaWxsPSIjRkZGIiBzdHJva2U9IiMwMDAiIHBvaW50ZXItZXZlbnRzPSJhbGwiIHJ4PSIyMS42IiByeT0iMjEuNiIvPjxnIGlkPSJnMzQiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUgLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDMyIj48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjE0OHB4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MTUwcHg7bWFyZ2luLWxlZnQ6MXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5Jc0VuZW15VmlzaWJsZTwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDMwIiB4PSI3NSIgeT0iMTU1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5Jc0VuZW15VmlzaWJsZTwvdGV4dD48L3N3aXRjaD48L2c+PC9nPjwvc3ZnPg==)

`ApproachEnemy` 是一个**异步**动作，会持续返回 RUNNING，直到最终完成。

条件节点 `isEnemyVisible` 会被多次调用，若返回假（即 FAILURE），则会中断 `ApproachEnemy`。

### SequenceWithMemory

当你不希望再次 tick 已经返回 SUCCESS 的子节点时，使用该控制节点。

**示例**：

这是一个巡逻的代理/机器人，必须**只访问一次**位置 A、B 和 C。

如果动作 **GoTo(B)** 失败，则不会再次 tick **GoTo(A)**。

另一方面，节点 **isBatteryOK** 需要每次 tick 都检查，因此它的父节点应为 `ReactiveSequence`。


![SequenceWithMemory](data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnN2Zz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHhtbG5zOnhodG1sPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmIiBpZD0ic3ZnNzAiIHdpZHRoPSI0NDEiIGhlaWdodD0iMzAyIiBjb250ZW50PSImbHQ7bXhmaWxlIGhvc3Q9JnF1b3Q7YXBwLmRpYWdyYW1zLm5ldCZxdW90OyBtb2RpZmllZD0mcXVvdDsyMDIyLTAxLTE0VDIyOjIzOjEyLjkwOFomcXVvdDsgYWdlbnQ9JnF1b3Q7NS4wIChYMTEpJnF1b3Q7IHZlcnNpb249JnF1b3Q7MTYuMi43JnF1b3Q7IGV0YWc9JnF1b3Q7MTh2ZW9mRUt1S1V3VURSeHpWb2ImcXVvdDsgdHlwZT0mcXVvdDtnb29nbGUmcXVvdDsmZ3Q7Jmx0O2RpYWdyYW0gaWQ9JnF1b3Q7Qk5DS0lsSl94ZXN0M1hCYkZHUUMmcXVvdDsmZ3Q7MVZkTGo1c3dFUDQxU08xbHhTTWhjT3htdDl0REsxVk5xM2FQRmt6QUs4ZVRPaVloL2ZVMVlSeHdTYXBLM1UzWUU4eG56M2ptbTRmQmkrYXIra0d4ZGZrSmN4QmU2T2UxRjkxNVlSZ0U0ZFE4R21UZkluRTBhNEZDOFp3MmRjQ0Mvd0lDZlVJcm5zUEcyYWdSaGVackY4eFFTc2kwZ3pHbGNPZHVXNkp3VDEyekFnYkFJbU5paUg3bnVTNXRYSEhhTFh3QVhwUjBkQkpTZkN0bU4xTWttNUxsdU90QjBiMFh6UldpYnQ5VzlSeEVRNTdscGRWN2YyYjE2SmdDcWY5RklTUTM5TjdHQnJrSmxVU0owanh1RlZZeWgwYkROeElxWFdLQmtvbVBpR3NEQmdaOEFxMzNsQ2hXYVRSUXFWZUNWcUhtK2tlamZqTWw2YkczY2xlVDVZT3d0NExVYXQ4cXphWldmdXd2ZG5vSHlTb3VVV3J5SkVpTTNFYlloT1Z3dE1GS1pRUlJPV3FtQ2lEYTBpR1R3VEUvcHJBQlYyQk9OVnNVQ0tiNTFyWE9xTUtLNDc2ajZtZmt4bUxvVXpja1BwMU92VENKZk5kRTZ4VnBkYWswTHowM091aVE0TlBKamw1RHNxK1E2L2o1YysxazZTOHBtWXcySmM5RHJaM1kxK0NXM05reVVaSFJCZnlzUUdhR1g2WUd4THNzNzBxdVliRm1oOWgyNWhwekdUM056aGFVaHRvN040clBCRzRIZ2IwVGFCQkVWdDcxcnBnSllXWHZkb245LytjcUhuRDFnRi94emZ6dDZHaEtrelB6c2s5VE1IMFptbWFqYmRjTFR0QmsyT2JUNjNWNU1xamNMNDJKYjFKenNhaXlERGJMU295dWltZXBXOFhIUWRtdjR2U0ZtajA5M2V6dnh0ZnNjZUxPeElzMnUwM0puenpkam8rbkpBcHZMamNXamRqOW9iVGZvZDEvWG5UL0d3PT0mbHQ7L2RpYWdyYW0mZ3Q7Jmx0Oy9teGZpbGUmZ3Q7IiB2ZXJzaW9uPSIxLjEiIHZpZXdCb3g9Ii0wLjUgLTAuNSA0NDEgMzAyIj48bWV0YWRhdGEgaWQ9Im1ldGFkYXRhNzQiLz48cGF0aCBpZD0icGF0aDQiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJNIDIyMCwxODAgMTAyLjE1LDIyNy42MSIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGlkPSJwYXRoNiIgZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Im0gOTcuMjksMjI5LjU4IDUuMTgsLTUuODcgLTAuMzIsMy45IDIuOTQsMi41OSB6IiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHBhdGggaWQ9InBhdGg4IiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0ibSAyMjAsMTgwIDE0Ni40NSw0OC4wMiIgcG9pbnRlci1ldmVudHM9InN0cm9rZSIvPjxwYXRoIGlkPSJwYXRoMTAiIGZpbGw9IiMwMDAiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJtIDM3MS40NCwyMjkuNjUgLTcuNzQsMS4xNSAyLjc1LC0yLjc4IC0wLjU3LC0zLjg3IHoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cGF0aCBpZD0icGF0aDEyIiBmaWxsPSJub25lIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0ibSAyMjAsMTgwIHYgNDMuNjMiIHBvaW50ZXItZXZlbnRzPSJzdHJva2UiLz48cGF0aCBpZD0icGF0aDE0IiBmaWxsPSIjMDAwIiBzdHJva2U9IiMwMDAiIHN0cm9rZS1taXRlcmxpbWl0PSIxMCIgZD0ibSAyMjAsMjI4Ljg4IC0zLjUsLTcgMy41LDEuNzUgMy41LC0xLjc1IHoiIHBvaW50ZXItZXZlbnRzPSJhbGwiLz48cmVjdCBpZD0icmVjdDE2IiB3aWR0aD0iMTk4LjkyMiIgaGVpZ2h0PSI1OS44MSIgeD0iMTE4LjkyMSIgeT0iMTIwLjA5NSIgZmlsbD0iI2ZmZiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIiBzdHlsZT0ic3Ryb2tlLXdpZHRoOjEuMTkwMTEiLz48ZyBpZD0iZzIyIiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgtMC41LC0wLjUpIj48c3dpdGNoIGlkPSJzd2l0Y2gyMCI+PGZvcmVpZ25PYmplY3Qgc3R5bGU9Im92ZXJmbG93OnZpc2libGU7dGV4dC1hbGlnbjpsZWZ0IiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBwb2ludGVyLWV2ZW50cz0ibm9uZSIgcmVxdWlyZWRGZWF0dXJlcz0iaHR0cDovL3d3dy53My5vcmcvVFIvU1ZHMTEvZmVhdHVyZSNFeHRlbnNpYmlsaXR5Ij48eGh0bWw6ZGl2IHN0eWxlPSJkaXNwbGF5OmZsZXg7YWxpZ24taXRlbXM6dW5zYWZlIGNlbnRlcjtqdXN0aWZ5LWNvbnRlbnQ6dW5zYWZlIGNlbnRlcjt3aWR0aDoxMzhweDtoZWlnaHQ6MXB4O3BhZGRpbmctdG9wOjE1MHB4O21hcmdpbi1sZWZ0OjE1MXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5TZXF1ZW5jZVdpdGhNZW1vcnk8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQxOCIgeD0iMjIwIiB5PSIxNTUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPlNlcXVlbmNlV2l0aE1lbW9yeTwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3QyNCIgd2lkdGg9IjExNSIgaGVpZ2h0PSI2MCIgeD0iMzE1IiB5PSIyMzAiIGZpbGw9IiNmZmYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIGlkPSJnMzAiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUsLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDI4Ij48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjExM3B4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MjYwcHg7bWFyZ2luLWxlZnQ6MzE2cHgiPjx4aHRtbDpkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPkdvVG8oQyk8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQyNiIgeD0iMzczIiB5PSIyNjUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkdvVG8oQyk8L3RleHQ+PC9zd2l0Y2g+PC9nPjxwYXRoIGlkPSJwYXRoMzIiIGZpbGw9Im5vbmUiIHN0cm9rZT0iIzAwMCIgc3Ryb2tlLW1pdGVybGltaXQ9IjEwIiBkPSJtIDIyMCw3MCB2IDQzLjYzIiBwb2ludGVyLWV2ZW50cz0ic3Ryb2tlIi8+PHBhdGggaWQ9InBhdGgzNCIgZmlsbD0iIzAwMCIgc3Ryb2tlPSIjMDAwIiBzdHJva2UtbWl0ZXJsaW1pdD0iMTAiIGQ9Im0gMjIwLDExOC44OCAtMy41LC03IDMuNSwxLjc1IDMuNSwtMS43NSB6IiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PHJlY3QgaWQ9InJlY3QzNiIgd2lkdGg9IjE5MCIgaGVpZ2h0PSI2MCIgeD0iMTI1IiB5PSIxMCIgZmlsbD0iI2ZmZiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgaWQ9Imc0MiIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSwtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoNDAiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTg4cHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDo0MHB4O21hcmdpbi1sZWZ0OjEyNnB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5SZXRyeVVudGlsU3VjY2Vzc2Z1bDwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwveGh0bWw6ZGl2PjwvZm9yZWlnbk9iamVjdD48dGV4dCBpZD0idGV4dDM4IiB4PSIyMjAiIHk9IjQ1IiBmaWxsPSIjMDAwIiBmb250LWZhbWlseT0iSGVsdmV0aWNhIiBmb250LXNpemU9IjE4IiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5SZXRyeVVudGlsU3VjY2Vzc2Z1bDwvdGV4dD48L3N3aXRjaD48L2c+PHJlY3QgaWQ9InJlY3Q0NCIgd2lkdGg9IjExNSIgaGVpZ2h0PSI2MCIgeD0iMTAiIHk9IjIzMCIgZmlsbD0iI2ZmZiIgc3Ryb2tlPSIjMDAwIiBwb2ludGVyLWV2ZW50cz0iYWxsIi8+PGcgaWQ9Imc1MCIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTAuNSwtMC41KSI+PHN3aXRjaCBpZD0ic3dpdGNoNDgiPjxmb3JlaWduT2JqZWN0IHN0eWxlPSJvdmVyZmxvdzp2aXNpYmxlO3RleHQtYWxpZ246bGVmdCIgd2lkdGg9IjEwMCUiIGhlaWdodD0iMTAwJSIgcG9pbnRlci1ldmVudHM9Im5vbmUiIHJlcXVpcmVkRmVhdHVyZXM9Imh0dHA6Ly93d3cudzMub3JnL1RSL1NWRzExL2ZlYXR1cmUjRXh0ZW5zaWJpbGl0eSI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTpmbGV4O2FsaWduLWl0ZW1zOnVuc2FmZSBjZW50ZXI7anVzdGlmeS1jb250ZW50OnVuc2FmZSBjZW50ZXI7d2lkdGg6MTEzcHg7aGVpZ2h0OjFweDtwYWRkaW5nLXRvcDoyNjBweDttYXJnaW4tbGVmdDoxMXB4Ij48eGh0bWw6ZGl2IHN0eWxlPSJib3gtc2l6aW5nOmJvcmRlci1ib3g7Zm9udC1zaXplOjA7dGV4dC1hbGlnbjpjZW50ZXIiIGRhdGEtZHJhd2lvLWNvbG9ycz0iY29sb3I6IHJnYigwLCAwLCAwKTsiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6aW5saW5lLWJsb2NrO2ZvbnQtc2l6ZToxOHB4O2ZvbnQtZmFtaWx5OkhlbHZldGljYTtjb2xvcjojMDAwO2xpbmUtaGVpZ2h0OjEuMjtwb2ludGVyLWV2ZW50czphbGw7d2hpdGUtc3BhY2U6bm9ybWFsO292ZXJmbG93LXdyYXA6bm9ybWFsIj5Hb1RvKEEpPC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC94aHRtbDpkaXY+PC9mb3JlaWduT2JqZWN0Pjx0ZXh0IGlkPSJ0ZXh0NDYiIHg9IjY4IiB5PSIyNjUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkdvVG8oQSk8L3RleHQ+PC9zd2l0Y2g+PC9nPjxyZWN0IGlkPSJyZWN0NTIiIHdpZHRoPSIxMTUiIGhlaWdodD0iNjAiIHg9IjE2Mi41IiB5PSIyMzAiIGZpbGw9IiNmZmYiIHN0cm9rZT0iIzAwMCIgcG9pbnRlci1ldmVudHM9ImFsbCIvPjxnIGlkPSJnNTgiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0wLjUsLTAuNSkiPjxzd2l0Y2ggaWQ9InN3aXRjaDU2Ij48Zm9yZWlnbk9iamVjdCBzdHlsZT0ib3ZlcmZsb3c6dmlzaWJsZTt0ZXh0LWFsaWduOmxlZnQiIHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIHBvaW50ZXItZXZlbnRzPSJub25lIiByZXF1aXJlZEZlYXR1cmVzPSJodHRwOi8vd3d3LnczLm9yZy9UUi9TVkcxMS9mZWF0dXJlI0V4dGVuc2liaWxpdHkiPjx4aHRtbDpkaXYgc3R5bGU9ImRpc3BsYXk6ZmxleDthbGlnbi1pdGVtczp1bnNhZmUgY2VudGVyO2p1c3RpZnktY29udGVudDp1bnNhZmUgY2VudGVyO3dpZHRoOjExM3B4O2hlaWdodDoxcHg7cGFkZGluZy10b3A6MjYwcHg7bWFyZ2luLWxlZnQ6MTY0cHgiPjx4aHRtbDpkaXYgc3R5bGU9ImJveC1zaXppbmc6Ym9yZGVyLWJveDtmb250LXNpemU6MDt0ZXh0LWFsaWduOmNlbnRlciIgZGF0YS1kcmF3aW8tY29sb3JzPSJjb2xvcjogcmdiKDAsIDAsIDApOyI+PHhodG1sOmRpdiBzdHlsZT0iZGlzcGxheTppbmxpbmUtYmxvY2s7Zm9udC1zaXplOjE4cHg7Zm9udC1mYW1pbHk6SGVsdmV0aWNhO2NvbG9yOiMwMDA7bGluZS1oZWlnaHQ6MS4yO3BvaW50ZXItZXZlbnRzOmFsbDt3aGl0ZS1zcGFjZTpub3JtYWw7b3ZlcmZsb3ctd3JhcDpub3JtYWwiPkdvVG8oQik8L3hodG1sOmRpdj48L3hodG1sOmRpdj48L3hodG1sOmRpdj48L2ZvcmVpZ25PYmplY3Q+PHRleHQgaWQ9InRleHQ1NCIgeD0iMjIwIiB5PSIyNjUiIGZpbGw9IiMwMDAiIGZvbnQtZmFtaWx5PSJIZWx2ZXRpY2EiIGZvbnQtc2l6ZT0iMTgiIHRleHQtYW5jaG9yPSJtaWRkbGUiPkdvVG8oQik8L3RleHQ+PC9zd2l0Y2g+PC9nPjwvc3ZnPg==)

# 与 ROS2 的集成

BehaviorTree.CPP 在机器人领域及 [ROS](https://docs.ros.org/en/humble/index.html) 生态系统中被广泛使用。

我们提供了一套现成的封装，可以用来快速实现与 ROS2 交互的 TreeNode：[BehaviorTree.ROS2](https://github.com/BehaviorTree/BehaviorTree.ROS2)

关于系统架构，需要记住：

* 应该有一个集中式的“协调器”ROS节点，负责行为的执行。该节点后续称为“任务规划器”，并将使用 BT.CPP 实现。
* 系统的其他所有部分应为“面向服务”的组件，任何业务逻辑和决策应委托给任务规划器。

> ⚠️ 注意
>
> 一些词在 **ROS** 和 **BT.CPP** 语境中相同，但含义不同。

尤其是“Action”和“Node”这两个词：

* `TreeNode` 与 `rclcpp::Node`
* `BT::Action` 与 `rclcpp_action`

你可以直接使用它们，也可以将它们作为模板/蓝图来创建你自己的版本。

### 使用 rclcpp\_action 的异步 BT::Action

推荐通过 [rclcpp\_action](https://docs.ros.org/en/humble/Tutorials/Intermediate/Writing-an-Action-Server-Client/Cpp.html) 与 ROS 交互。

它们非常合适，因为：

* 其 API 是异步的，即用户不必担心创建单独线程。
* 它们可以被中止，这对于实现 `TreeNode::halt()` 和构建响应式行为是必要的。

例如，考虑[官方 C++ 教程](https://docs.ros.org/en/humble/Tutorials/Intermediate/Writing-an-Action-Server-Client/Cpp.html#writing-an-action-client)中描述的“Fibonacci”动作客户端：

```cpp
// 简化定义
using Fibonacci = action_tutorials_interfaces::action::Fibonacci;
using GoalHandleFibonacci = rclcpp_action::ServerGoalHandle<Fibonacci>;
```

创建一个调用此 ROS 动作的 BT 动作：

```cpp
#include <behaviortree_ros2/bt_action_node.hpp>

using namespace BT;

class FibonacciAction: public RosActionNode<Fibonacci>
{
public:
  FibonacciAction(const std::string& name,
                  const NodeConfig& conf,
                  const RosNodeParams& params)
    : RosActionNode<Fibonacci>(name, conf, params)
  {}

  // 派生类特定的端口
  // 应与基类端口合并，
  // 使用 RosActionNode::providedBasicPorts()
  static PortsList providedPorts()
  {
    return providedBasicPorts({InputPort<unsigned>("order")});
  }

  // 当 TreeNode 被 tick 时调用，
  // 应向动作服务器发送请求
  bool setGoal(RosActionNode::Goal& goal) override 
  {
    // 从输入端口获取 "order"
    getInput("order", goal.order);
    // 如果成功设置目标，则返回 true
    return true;
  }
  
  // 接收到回复时的回调函数。
  // 可根据回复决定返回 SUCCESS 或 FAILURE。
  NodeStatus onResultReceived(const WrappedResult& wr) override
  {
    std::stringstream ss;
    ss << "结果已接收: ";
    for (auto number : wr.result->sequence) {
      ss << number << " ";
    }
    RCLCPP_INFO(node_->get_logger(), ss.str().c_str());
    return NodeStatus::SUCCESS;
  }

  // 通信客户端与服务器出错时调用的回调。
  // 将设置 TreeNode 状态为 SUCCESS 或 FAILURE，
  // 根据返回值确定。
  // 若不重写，默认返回 FAILURE。
  virtual NodeStatus onFailure(ActionNodeErrorCode error) override
  {
    RCLCPP_ERROR(node_->get_logger(), "错误: %d", error);
    return NodeStatus::FAILURE;
  }

  // 支持反馈回调，如原教程所示。
  // 通常此回调应返回 RUNNING，但你可能基于反馈值
  // 决定中止动作，并认为 TreeNode 完成。
  // 这种情况下返回 SUCCESS 或 FAILURE。
  // 取消请求会自动发送给服务器。
  NodeStatus onFeedback(const std::shared_ptr<const Feedback> feedback)
  {
    std::stringstream ss;
    ss << "接收到序列中的下一个数字: ";
    for (auto number : feedback->partial_sequence) {
      ss << number << " ";
    }
    RCLCPP_INFO(node_->get_logger(), ss.str().c_str());
    return NodeStatus::RUNNING;
  }
};
```

你会注意到，BT 版本的动作客户端比原版更简洁，因为大部分模板代码已封装在 `BT::RosActionNode` 中。

注册该节点时，需要使用 `BT::RosNodeParams` 传入 `rclcpp::Node` 和其他参数：

```cpp
  // 在 main() 中
  BehaviorTreeFactory factory;

  auto node = std::make_shared<rclcpp::Node>("fibonacci_action_client");
  // 提供 ROS 节点和动作服务名称
  RosNodeParams params; 
  params.nh = node;
  params.default_port_value = "fibonacci";
  factory.registerNodeType<FibonacciAction>("Fibonacci", params);
```

### 使用 rclcpp::Client（服务）的异步 BT::Action

针对 ROS 服务客户端也有类似的封装，使用异步接口。

下面示例基于[官方教程](https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Cpp-Service-And-Client.html#write-the-client-node)。

```cpp
#include <behaviortree_ros2/bt_service_node.hpp>

using AddTwoInts = example_interfaces::srv::AddTwoInts;
using namespace BT;


class AddTwoIntsNode: public RosServiceNode<AddTwoInts>
{
  public:

  AddTwoIntsNode(const std::string& name,
                  const NodeConfig& conf,
                  const RosNodeParams& params)
    : RosServiceNode<AddTwoInts>(name, conf, params)
  {}

  // 派生类特定端口
  // 应与基类端口合并，
  // 使用 RosServiceNode::providedBasicPorts()
  static PortsList providedPorts()
  {
    return providedBasicPorts({
        InputPort<unsigned>("A"),
        InputPort<unsigned>("B")});
  }

  // TreeNode 被 tick 时调用，
  // 应向服务提供者发送请求
  bool setRequest(Request::SharedPtr& request) override
  {
    // 使用输入端口设置 A 和 B
    getInput("A", request->a);
    getInput("B", request->b);
    // 准备好发送请求时必须返回 true
    return true;
  }

  // 接收到响应时调用回调。
  // 必须返回 SUCCESS 或 FAILURE
  NodeStatus onResponseReceived(const Response::SharedPtr& response) override
  {
    RCLCPP_INFO(node_->get_logger(), "和: %ld", response->sum);
    return NodeStatus::SUCCESS;
  }

  // 通信客户端与服务器出错时调用回调。
  // 将设置 TreeNode 状态为 SUCCESS 或 FAILURE，
  // 根据返回值确定。
  // 若不重写，默认返回 FAILURE。
  virtual NodeStatus onFailure(ServiceNodeErrorCode error) override
  {
    RCLCPP_ERROR(node_->get_logger(), "错误: %d", error);
    return NodeStatus::FAILURE;
  }
};
```

# 从版本 3.8 迁移到 4.X

你会发现版本 4.X 中的大部分更改都是渐进式的，并且向后兼容你之前的代码。

这里我们尝试总结你在迁移时应该注意的最重要的区别。

> ℹ️ 注意
>
> 在代码库中，你可以找到一个名为 **convert\_v3\_to\_v4.py** 的 Python 脚本，可以帮你节省一些时间（感谢用户 [https://github.com/SubaruArai）！](https://github.com/SubaruArai）！)

试用它，但请务必先仔细检查转换结果！

## 类重命名

以下类名 / XML 标签名发生了变化。

| 3.8+ 版本名          | 4.x 版本名            | 适用范围      |
| ----------------- | ------------------ | --------- |
| NodeConfiguration | NodeConfig         | C++       |
| SequenceStar      | SequenceWithMemory | C++ 和 XML |
| AsyncActionNode   | ThreadedAction     | C++       |
| Optional          | Expected           | C++       |

如果你想快速修复 C++ 代码的编译问题（**尽管建议重构**），可以添加：

```cpp
namespace BT 
{
  using NodeConfiguration = NodeConfig;
  using AsyncActionNode = ThreadedAction;
  using Optional = Expected;
}
```

## XML

你应当在 XML 的 `<root>` 标签上添加属性 `BTCPP_format`：

之前：

```xml
<root>
```

现在：

```xml
<root BTCPP_format="4">
```

这将允许我们兼容 3 和 4 两个版本……最终实现！

## SubTree 和 SubTreePlus

3.X 默认的 **SubTree** 已被弃用，推荐使用 **SubtreePlus**。新版本中，默认的称为 “SubTree”。

| 3.8+ 版本名        | 4.x 版本名     |
| --------------- | ----------- |
| `<SubTree>`     | 已弃用         |
| `<SubTreePlus>` | `<SubTree>` |

## SetBlackboard 和 BlackboardCheck

新的[scripting 语言](https://www.behaviortree.dev/docs/guides/scripting)更简单且更强大。

也请查看[前置条件和后置条件介绍](https://www.behaviortree.dev/docs/guides/pre_post_conditions)。

3.8 旧代码示例：

```xml
<SetBlackboard output_key="port_A" value="42" />
<SetBlackboard output_key="port_B" value="69" />
<BlackboardCheckInt value_A="{port_A}" value_B="{port_B}" 
                    return_on_mismatch="FAILURE">
    <MyAction/>
</BlackboardCheckInt>
```

4.X 新代码示例：

```xml
<Script code="port_A:=42; port_B:=69" />
<MyAction _failureIf="port_A!=port_B"/>
```

## 在 While 循环中执行 Ticking

以前的典型执行代码如下：

```cpp
// 简化代码，常见于 BT.CPP 3.8
while(status != NodeStatus::SUCCESS || status == NodeStatus::FAILURE) 
{
  status = tree.tickRoot();
  std::this_thread::sleep_for(sleep_ms);
}
```

行为树的“轮询”模型有时受到批评。sleep 是为了避免“忙循环”，但可能引入延迟。

为了提升行为树的响应性，我们引入了方法：

```cpp
Tree::sleep(std::chrono::milliseconds timeout)
```

该实现的 **sleep** 可被树中任意节点调用 `TreeNode::emitWakeUpSignal` 中断，允许循环 **立即** 重新 tick 树。

`Tree::tickRoot()` 已从公共 API 中移除，推荐的新用法是：

```cpp
// 使用 Tree::sleep 等待 SUCCESS 或 FAILURE
while(!BT::isStatusCompleted(status)) 
{
  status = tree.tickOnce();
  tree.sleep(sleep_ms);
}
//---- 或者，更佳的用法 ------
status = tree.tickWhileRunning(sleep_ms); 
```

`Tree::tickWhileRunning` 是新默认方法，内部带循环；第一个参数为循环中 sleep 的超时时间。

你也可以使用：

* `Tree::tickExactlyOnce()`：等同于旧版 3.8+ 行为
* `Tree::tickOnce()` 大致相当于 `tickWhileRunning(0ms)`，可能会 tick 多次。

## ControlNodes 和 Decorators 必须支持 NodeStatus\:SKIPPED

新增状态 **SKIPPED** 用于表示 [PreCondition](https://www.behaviortree.dev/docs/guides/pre_post_conditions) 不满足时返回。

当节点返回 **SKIPPED**，它向父节点（ControlNode 或 Decorator）表示该节点未执行。

> ℹ️ 注意
>
> 自定义 **叶子节点** 不应返回 **SKIPPED**。该状态专用于 PreCondition。

另一方面，**ControlNodes 和 Decorators** 必须支持此新状态。

一般规则是：若子节点返回 **SKIPPED**，表示未执行，ControlNode 应跳过该子节点，继续下一个。

## 异步控制节点

用户[反馈](https://github.com/BehaviorTree/BehaviorTree.CPP/issues/395)发现一个严重问题：

> 若一个 **ControlNode** 或 **DecoratorNode** 仅有同步子节点，则无法中断它们。

示例：

```xml
<ReactiveSequence>
    <AbortCondition/>
    <Sequence name="synch_sequence">
        <SyncActionA/>
        <SyncActionB/>
        <SyncActionC/>
    <Sequence>
</ReactiveSequence>   
```

当 `Sequence`（或 `Fallback`）仅有同步子节点时，整个序列变成“原子操作”。

换言之，当 "synch\_sequence" 开始执行时，`AbortCondition` 无法中止它。

为了解决此问题，我们添加了两个新节点：`AsyncSequence` 和 `AsyncFallback`。

使用 `AsyncSequence` 时，每执行完一个同步子节点后都会返回 **RUNNING**，然后再切换到下一个子节点。

上述示例中，要完成整棵树，需要 3 次 tick。


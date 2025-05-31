+++
date = '2025-05-23T17:07:50+08:00'
draft = false
title = 'Why We Think'
summary= "测试时计算（test time compute）与思维链（Chain-of-thought, CoT）已显著提升了模型性能，同时也引发了许多研究问题。本文旨在回顾关于如何有效利用测试时计算（即“思考时间”）以及为何它能带来帮助的最新进展。"
+++



日期：2025年5月1日 | 预计阅读时间：40分钟 | 作者：Lilian Weng

特别感谢 [John Schulman](https://scholar.google.com/citations?user=itSa94cAAAAJ&hl=en) 对本文提供了许多非常有价值的反馈和直接修改。

测试时计算（test time compute）（[Graves et al. 2016](https://arxiv.org/abs/1603.08983)、[Ling, et al. 2017](https://arxiv.org/abs/1705.04146)、[Cobbe et al. 2021](https://arxiv.org/abs/2110.14168)）与思维链（Chain-of-thought, CoT）（[Wei et al. 2022](https://arxiv.org/abs/2201.11903)、[Nye et al. 2021](https://arxiv.org/abs/2112.00114)）已显著提升了模型性能，同时也引发了许多研究问题。本文旨在回顾关于如何有效利用测试时计算（即“思考时间”）以及为何它能带来帮助的最新进展。

## 动机

让模型“思考得更久”有多种动机。

## 心理学类比

这个核心思想与人类思考的方式密切相关。人类无法立即回答“12345乘以56789是多少？”这样的问题。我们通常会花时间思考和分析，尤其是在面对复杂问题时。在 [《思考，快与慢》（Kahneman, 2013）](https://www.amazon.com/Thinking-Fast-Slow-Daniel-Kahneman/dp/0374533555) 一书中，Daniel Kahneman 根据 [双过程理论（dual process theory）](https://en.wikipedia.org/wiki/Dual_process_theory) 将人类的思维模式分为两种：

* **快思考（系统1）**：快速且自动地运行，依赖直觉和情感，几乎不需要努力。
* **慢思考（系统2）**：需要有意识地投入逻辑思考与大量认知资源。该模式更耗费心理能量，需要主动参与。

由于系统1思维快速且轻松，往往成为主要决策机制，但这也以牺牲准确性和逻辑为代价。它依赖于大脑的心理捷径（即启发式），因此容易出错和产生偏差。通过有意识地放慢思考节奏，花更多时间反思、改进和分析，我们可以激活系统2思维，挑战本能，从而做出更理性的决策。

## 计算资源视角

深度学习的一个视角是：神经网络可以通过正向传播中所能访问的计算与存储量来刻画。如果我们用梯度下降方法训练模型解决问题，那么优化过程将学会如何利用这些资源——即学会如何将资源组织成执行计算和存储信息的“电路”。从这个角度看，如果我们设计一个能够在测试时执行更多计算的架构或系统，并训练其有效利用这些资源，那么它的表现会更好。

在 Transformer 模型中，每生成一个 token 所需的计算量（以 FLOPs 衡量）大约是模型参数量的两倍。而在稀疏模型如专家混合（mixture of experts, MoE）中，每次前向传播只激活部分参数，因此其计算量为：`计算量 = 2 * 参数量 / 稀疏率`，其中稀疏率指的是被激活的专家比例。

另一方面，CoT 允许模型在为每个答案 token 进行计算时执行更多的 FLOPs。事实上，CoT 的一个优点是：它可以根据问题的复杂程度动态调整所需的计算量。

## 潜变量建模

机器学习中的一个经典思想是：定义一个含有潜变量（latent variable）$z$ 和可见变量（visible variable）$y$ 的概率模型，其中$y$ 是给定的数据。对潜变量取边际（求和）可以表达一个对可见变量的丰富分布：

$$
P(y) = \sum_z \sim P(z) P(y|z)
$$

例如，我们可以通过设定$x$ 为数学问题的描述，$y$ 为答案或证明，$z$ 为通往证明过程中的自由思考路径，来建模数学题与答案的分布。此时我们优化的目标为：

$$
P(y|x) = \sum_z \sim p(z|x) P(y|x, z)
$$

这种潜变量的视角在理解涉及多个并行 CoT 收集或在 CoT 空间中搜索的方法时特别有用，这些算法可视为从后验分布$P(z|x,y)$ 中采样。同时，这一视角也说明了将对数损失函数$\log P(y|x)$ 作为目标优化的重要性——在预训练中它非常有效。

# Token 中的思考

在生成简短答案之前先生成中间步骤的策略，尤其是针对数学问题，最早由 [Ling et al. 2017](https://arxiv.org/abs/1705.04146) 探索，他们提出了 [AQUA-RAT](https://github.com/google-deepmind/AQuA) 数据集。随后 [Cobbe et al. 2021](https://arxiv.org/abs/2110.14168) 扩展了这一工作，推出了 [Grade School Math (GSM)](https://github.com/openai/grade-school-math) 数据集。Cobbe 等人通过人类书写解答训练生成器，并使用验证器判断候选解的正确性，从而对解答进行搜索。[Nye et al. (2021)](https://arxiv.org/abs/2112.00114) 尝试将中间思考 token 作为“草稿板”，而 [Wei et al.](https://arxiv.org/abs/2201.11903)（2022）首次提出了现在被广泛使用的术语 **思维链（chain-of-thought, CoT）**。

早期改进 CoT 推理能力的工作包括：对人类书写的推理轨迹或过滤过的模型推理轨迹进行监督学习；后者可被视为一种初级形式的强化学习（reinforcement learning, RL）。另一些研究发现，通过恰当的提示可显著提升经过指令调优（instruction tuned）模型的数学表现，例如使用 `"think step by step"`（[Kojima et al. 2022](https://arxiv.org/abs/2205.11916)）或更复杂的提示鼓励模型优先反思相关知识（[Yasunaga et al. 2023](https://arxiv.org/abs/2310.01714)）。

后续研究发现，可通过对具有自动可验证答案的数据集（如短答案 STEM 问题或可用单元测试验证的编程任务）执行强化学习，显著提升 CoT 推理能力（[Zelikman et al. 2022](https://arxiv.org/abs/2203.14465)，[Wang et al., 2023](https://arxiv.org/abs/2312.08935)，[Liu et al., 2023](https://arxiv.org/abs/2310.10047)）。这一思路因 [o1-preview](https://openai.com/index/learning-to-reason-with-llms/)、[o3](https://openai.com/index/introducing-o3-and-o4-mini/)、以及 R1 技术报告（[DeepSeek-AI, 2025](https://arxiv.org/abs/2501.12948)）的发布而走红，报告中展示了一种使用策略梯度算法的简单方法即可达到强大性能。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/cot-wei22.png)
思维链提示能提高解决数学问题的成功率。模型越大，越能从“思考时间”中受益。（图源：Wei et al. 2022）

## 分支与修订

测试时计算的根本意图是：在测试阶段自适应地调整模型输出分布。可以通过多种方式利用测试时的资源在解码阶段选择更优样本，从而引导模型向更理想的分布输出。

提升解码过程的两种主要方法是并行采样与顺序修订：

* **并行采样** 同时生成多个输出，可在每步使用奖励信号进行引导，或在末尾通过验证器评估质量。这是最广泛采用的测试时性能提升方法，如 best-of-N 或 beam search。若没有可用的真实标签，常使用“自洽性”（self-consistency）（[Wang et al. 2023](https://arxiv.org/abs/2203.11171)）在多个 CoT 展开中采用多数投票选出最终答案。
* **顺序修订** 根据上一步的输出逐步调整模型回应，让模型有意识地反思自身输出并修正错误。该过程可能需依赖精调模型，因为单靠模型本身的自我纠错能力，缺乏外部反馈，可能无法提升表现（[Kamoi et al. 2024](https://arxiv.org/abs/2406.01297)，[Huang et al. 2024](https://arxiv.org/abs/2310.01798)）。

并行采样简单、直观且易于实现，但受限于模型一次性输出正确结果的能力。顺序修订显式要求模型反思错误，虽更慢、实现更复杂，但能有效推动修正。然而，该方式可能会将原本正确的预测改错，或引入其它幻觉内容。这两种方法也可以结合使用。[Snell et al. (2024)](https://arxiv.org/abs/2408.03314) 指出：简单问题更适合使用纯顺序的测试时计算，而困难问题则在并行与顺序计算之间达到最优组合时表现最好。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/parallel-vs-sequential.png)
并行采样与顺序修订的示意图。


### 并行采样（Parallel Sampling）

在拥有一个生成模型与用于评估完整或部分样本的打分函数的前提下，可以采用多种搜索算法来寻找得分更高的样本。最简单的算法是 Best-of-N：采样$N$ 个独立样本，并根据某种打分函数选出得分最高者。Beam Search（束搜索）是一种更复杂的搜索算法，其搜索过程更具适应性，会在更有前景的解空间中投入更多采样计算资源。

Beam Search 维护一组可能性较高的部分序列，交替进行扩展与剪枝。在候选选择机制上，可以借助过程奖励模型（Process Reward Model, PRM；[Lightman et al. 2023](https://arxiv.org/abs/2305.20050)）来引导 beam search 的候选选择。[Xie 等人 (2023)](https://arxiv.org/abs/2305.00633) 使用大语言模型（LLM）评估自身生成的推理步骤是否正确，并将其格式化为选择题。结果发现，这种逐步自我评估在 beam search 解码中能降低累计误差。此外，在采样过程中对温度进行退火（annealing）可减缓随机性带来的干扰。Xie 等人的实验在 GSM8k、AQuA 和 StrategyQA 等少样本基准上使用 Codex 模型实现了 5–6% 的性能提升。

Reward Balanced Search（简称 REBASE；[Wu et al. 2025](https://arxiv.org/abs/2408.00724)）则另行训练了一个 PRM，用于决定 beam search 中每一深度的每个节点应扩展的程度，其依据是 softmax 归一化后的奖励得分。[Jiang et al. (2024)](https://arxiv.org/abs/2410.01044) 所训练的 PRM 被命名为 “RATIONALYST”，用于基于合成推理的 beam search 引导，训练数据为大量无标注语料。当包含该推理路径时，若能显著降低真实答案 token 的负对数概率（neg log-prob），就认为这条推理路径是“好的”。在推理时，RATIONALYST 通过估计下一个推理步骤的 log 概率（隐式方式）或直接生成下一步推理内容（显式方式）来对 CoT 生成器提供过程监督。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/beam-search-xie23.png)
Beam search 解码过程通过 LLM 对每个推理步骤进行自我评估来进行引导。（图源：[Xie et al. 2023](https://arxiv.org/abs/2305.00633)）

有趣的是，可以在**没有显式零样本或少样本提示**的情况下诱发模型的思维链（CoT）路径。[Wang & Zhou (2024)](https://arxiv.org/abs/2402.10200) 发现：若在采样初期保留置信度最高的$k$ 个 token（置信度定义为 top-1 与 top-2 候选间的概率差），并对这$k$ 个分支分别进行贪婪解码，则很多生成序列自然就包含了 CoT。尤其是当上下文中已出现 CoT 时，这会使模型在生成最终答案时更具信心。

为了计算最终答案的置信度，需要借助任务特定启发式方法（如数学题中最后一个数字）或进一步提示模型 `“So the answer is”` 来提取答案区间。之所以仅在首个 token 进行分支，是基于如下观察：早期分支能显著增强解路径的多样性，而后续 token 会受此前内容的强烈影响。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/cot-decoding.png)
Top-k 解码示意图，其中$k$ 是首次采样步骤中保留的候选数量。（图源：[Wang & Zhou, 2024](https://arxiv.org/abs/2402.10200)）

### 顺序修订（Sequential Revision）

如果模型能反思并修正过去回答中的错误，我们就希望其能迭代地产出越来越高质量的修订序列。但实际上，大语言模型（LLM）并不具备这种内在的自我修正能力，直接使用这种能力往往效果不佳，其失败模式包括：

1. **幻觉（hallucination）**：将正确答案改成错误；
2. **行为崩溃**：修订时未作出实质性修改或根本不修改；
3. **泛化失败**：在测试时遇到分布漂移时无效。

[Huang 等人 (2024)](https://arxiv.org/abs/2310.01798) 的实验表明，直接套用自我修正机制反而会导致性能下降。为实现有效的自我改进，模型需要外部反馈，如：与真实标签匹配、启发式方法、任务特定指标、编程题的单元测试结果（[Shinn 等人, 2023](https://arxiv.org/abs/2303.11366)）、更强大的模型监督（[Zhang et al. 2024](https://arxiv.org/abs/2404.17140)）、或人类反馈（[Liu et al. 2023](https://arxiv.org/abs/2302.02676)）。

**自我修正学习**（Self-correction learning；[Welleck et al. 2023](https://arxiv.org/abs/2211.00053)）目标是：在给定固定生成模型$P\_0(y\_0|x)$ 的情况下，训练一个校正器模型$P\_\theta(y|y\_0,x)$。生成器保持通用性，而校正器可根据任务而定，仅在给定初始响应与可选反馈（如文本、编译器日志、单测结果）条件下生成内容：

1. 首先在数据池中为每个提示生成多个输出；
2. 若两个输出中一个的价值更高，则构造 “增值对”（value-improving pair）：$(x, y, y')$；
3. 这些样本对按照价值差$v(y') - v(y)$ 和输出相似度$Similarity(y, y')$ 的比例进行抽样训练；
4. 校正器的输出也可用于扩展数据池。在推理阶段，校正器可用于生成修正轨迹的顺序修订过程。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/self-correction-welleck23.png)
通过配对同一问题下模型输出构造增值对来训练校正器模型的自我修正学习示意图。（图源：Welleck et al. 2023）

**递归检查**（Recursive inspection；[Qu et al. 2024](https://arxiv.org/abs/2407.18219)）也是训练更强校正器的方法，不过其采用单一模型同时执行生成与自我修正。

**SCoRe（Self-Correction via Reinforcement Learning）** 是一个基于多轮强化学习（RL）的自我修正方法（[Kumar et al. 2024](https://arxiv.org/abs/2409.12917)）。该方法鼓励模型在第二轮生成中产出比第一轮更好的答案。训练包括两个阶段：

* **阶段1**：最大化第二次尝试的正确率，并在第一轮加入 KL 惩罚以限制其偏离原始模型；
* **阶段2**：同时优化第一轮和第二轮的答案质量。

这种设计既能鼓励第一次输出的多样性，又能逐步提升整体表现。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/SCoRe-kumar24.png)
通过两阶段强化学习训练显式提升模型的自我修正能力的结构图。（图源：Kumar et al. 2024）

## 强化学习提升推理能力（RL for Better Reasoning）

近年来，通过强化学习（Reinforcement Learning, RL）显著提升语言模型推理能力的研究不断取得突破。这些方法通常采用带有真实答案的问题集（如 STEM 题或逻辑谜题）作为训练基础，模型的奖励机制以获得正确答案为目标。该方向的热度部分来自 OpenAI `o` 系列模型的出色表现，以及随后 [DeepSeek](https://www.deepseek.com/) 团队发布的模型与技术报告。

`DeepSeek-R1`（[DeepSeek-AI, 2025](https://arxiv.org/abs/2501.12948)）是一个开源大语言模型（LLM），旨在在数学、编程、逻辑等高阶推理任务中取得卓越表现。其训练流程包括两轮 SFT-RL（监督微调 + 强化学习）：

1. **冷启动 SFT**：对 `DeepSeek-V3-Base` 在数千条冷启动数据上进行微调，以解决可读性差、语言混用问题。

2. **面向推理的强化学习**：

   * 仅对推理类 prompt 训练推理模型；
   * 设定两类基于规则的奖励：

     * 格式奖励：要求使用 `<thinking>...</thinking>` 包裹 CoT；
     * 准确性奖励：数学题需将答案放入指定格式（如边框中）以便验证；编程题使用编译器验证是否通过测试用例。

3. **拒绝采样 + 非推理类 SFT**：

   * 从步骤2的 RL checkpoint 中进行拒绝采样，结合 `DeepSeek-V3` 的非推理类监督数据（写作、问答、自认知等）；
   * 过滤掉混用语言、长段落或代码块的 CoT；
   * 对总共80万样本进行2轮微调训练。

4. **最终 RL 阶段**：对推理与非推理 prompt 共同进行训练，以提升有用性、无害性与推理能力。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/R1-eval.png)
`DeepSeek-R1` 在多个主流推理基准上与 OpenAI `o1-preview`、`o1-mini` 表现相当，`DeepSeek-V3` 为唯一非推理模型。（图源：DeepSeek-AI, 2025）

有趣的是，DeepSeek 团队还展示了无需 SFT，纯 RL 训练即可学习高级推理能力，如反思与回溯（即“顿悟时刻”）。模型在 RL 训练中自然学会投入更多思考 token 来解决复杂问题。“顿悟时刻”指模型在反思错误后主动尝试其它路径进行修正。

随后有多个开源项目尝试复现 R1 成果，如 [Open-R1](https://github.com/huggingface/open-r1)、[SimpleRL-reason](https://github.com/hkust-nlp/simpleRL-reason)、[TinyZero](https://github.com/Jiayi-Pan/TinyZero)，均基于 [Qwen](https://github.com/QwenLM/Qwen2.5) 模型。这些尝试也验证了纯 RL 方法在数学问题与“顿悟能力”方面的有效性。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/aha-moment.png)
模型学会反思并修正错误的示例。（图源：（左）DeepSeek-AI, 2025；（右）Zeng et al. 2025）

DeepSeek 还公开了部分失败尝试。例如：使用过程奖励模型（PRM）因难以定义每步规则、评估中间步骤是否正确而失败，训练过程也容易被奖励欺骗（reward hacking）破坏；而 MCTS（蒙特卡洛树搜索）则因语言模型 token 的搜索空间过大且精细奖励建模困难而未能成功。这些失败案例提供了有价值的洞察，也呼吁社区更多分享“不成功”的经验。




## 外部工具的使用

在推理步骤中，某些中间步骤可以通过执行代码或运行数学计算来可靠且准确地完成。将推理组件中的这一部分卸载到外部代码解释器上（例如 PAL（Program-Aided Language Model，程序辅助语言模型；[Gao et al. 2022](https://arxiv.org/abs/2211.10435)）或 Chain of Code（[Li et al. 2023](https://chain-of-code.github.io/)））可以借助外部工具扩展大语言模型（LLM, Large Language Model）的能力，从而免去让 LLM 学习执行代码或充当计算器的需求。这些代码模拟器（如 Chain of Code 中的实现）可以由 LLM 增强：如果标准代码解释器失败，我们可以选择使用 LLM 来执行该行代码。

使用代码来增强推理步骤对于数学问题、符号推理和算法任务尤其有益。这些单元测试在某些编程问题中可能并不存在，在这些情况下，我们可以指示模型自我生成单元测试，以便它可以通过测试来验证其解决方案的正确性（[Shinn 等人，2023](https://arxiv.org/abs/2303.11366)）。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/pal.png)
一个程序辅助语言模型提示的示例。（图片来源：Gao et al. 2022）

ReAct（Reason+Act；[Yao et al. 2023](https://arxiv.org/abs/2210.03629)）将搜索维基百科 API 的行为与推理轨迹的生成结合起来，使推理路径能够整合外部知识。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/react.png)
一个 ReAct 提示方法的示例，用于解决 HotpotQA 问题，通过使用维基百科搜索 API 作为外部工具来辅助推理。（图片来源：Yao et al. 2023）

[o3 与 o4-mini](https://openai.com/index/introducing-o3-and-o4-mini/)，是 OpenAI 最近发布的两个优秀示例，其中推理过程涉及到工具使用，如网页搜索、代码执行和图像处理。团队观察到，大规模强化学习呈现出与 GPT 范式相同的趋势，即“更多计算 = 更好性能”。

## 忠实思考（Thinking Faithfully）

深度学习模型通常被当作黑箱来处理，已经提出了各种可解释性方法。可解释性具有几个重要用途：首先，它为我们提供了一个额外的检验手段，用以判断模型是否与其创建者的意图不一致，或是否以我们难以从行为中察觉的方式表现异常。其次，它可以帮助我们判断模型是否使用了合理的流程来得出答案。思维链（Chain-of-thought, CoT）提供了一种特别便利的可解释性形式，因为它以自然语言展示了模型的内部推理过程。然而，这种可解释性的前提是模型能**忠实地**描述其内部思维过程。

近期的研究表明，监控推理模型的 CoT 能够有效地检测出模型的不良行为，例如 [奖励欺骗（reward hacking）](https://lilianweng.github.io/posts/2024-11-28-reward-hacking/)，甚至可以使一个较弱的模型监控一个更强的模型（[Baker et al. 2025](https://arxiv.org/abs/2503.11926)）。增加测试时计算量也能提高对抗性鲁棒性（[Zaremba et al. 2025](https://arxiv.org/abs/2501.18841)）；从直觉上讲，这也是合理的：在面对异常输入（例如对抗样本或越狱攻击）时，更长的思考时间将特别有助于模型理解其所面临的异常情况。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/cot-monitor.png)
该实验要求模型根据其思维过程判断另一个模型是否在某种程度上试图通过代码问题中的单元测试作弊。我们可以通过不同类型的监控器在训练过程中监控这些奖励欺骗行为。`exit(0)` 代码作弊是指智能体利用一个漏洞在未运行所有单元测试的情况下提前退出环境。`raise SkipTest` 欺骗是指智能体从测试框架之外的函数抛出异常，以跳过单元测试评估。（图片来源：Baker et al. 2025）

### 模型是否忠实地表达了其思考？

从直觉上讲，模型的 CoT 可能存在偏差，这是由于缺乏明确的训练目标来鼓励忠实推理，或者当我们在基于人类书写解释上微调模型时，这些人类样本本身可能就包含错误。因此，我们不能默认认为 CoT 一定是忠实的。

[Lanham 等人（2023）](https://arxiv.org/abs/2307.13702) 研究了几种 CoT 忠实性失效的模式，方法是故意在 CoT 中引入错误，并测量这些错误对一系列多项选择任务（例如 AQuA、MMLU、ARC Challenge、TruthfulQA、HellaSwag）准确率的影响：

* 错误 1（*过早作答*）：模型可能在生成 CoT 之前就过早得出结论。这通过提前截断或在 CoT 中插入错误来进行测试。不同的任务揭示了对 CoT 效果的任务特定依赖性；有些任务在 CoT 被截断后评估性能会下降，有些则不会。[Wang 等人（2023）](https://arxiv.org/abs/2212.10001) 做了类似实验，但引入的是更微妙的错误，例如桥接对象或语言模板方面的错误。
* 错误 2（*无信息 token*）：CoT 中的无信息 token 是否提高了性能？该假设通过将 CoT 替换为填充文本（如全为句号）来测试，结果显示没有准确率的提升，并且某些任务的性能在没有 CoT 的情况下略优。
* 错误 3（*人类难以理解的编码*）：将相关信息以人类难以理解的方式编码。以非标准方式改写 CoT 不会降低数据集上的性能，说明性能提升并不依赖于人类可读的推理过程。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/cot-perturb.png)
用于评估 CoT 忠实性的不同扰动方式示意图。（图片来源：Lanham et al. 2023）

有趣的是，Lanham 等人指出，对于多项选择题，小模型可能还不足以充分利用 CoT，而较大的模型即使没有 CoT 也可能完成任务。这种对 CoT 推理的依赖性（通过“有 CoT 与无 CoT 得出相同答案的比例”衡量）在多选任务中并不总是随着模型规模增加而增加，但在加法类任务中确实随模型规模上升而增强，表明“思考时间”在复杂推理任务中更为重要。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/cot-ablation.png)
CoT 依赖性衡量为有无 CoT 情况下给出相同答案的百分比。在加法等推理任务中影响更显著，且大模型受益更多。（图片来源：Lanham et al. 2023）

测试 CoT 忠实性的另一个方法是扰动提示语（prompt），而不是直接修改 CoT 路径（[Turpin et al. 2023](https://arxiv.org/abs/2305.04388)、[Chua & Evans, 2025](https://arxiv.org/abs/2501.08156)、[Chen et al. 2025](https://assets.anthropic.com/m/71876fabef0f0ed4/original/reasoning_models_paper.pdf)）。

一种方法是在少样本示例中始终将正确答案标记为 “(A)”，以人为引入偏差。

另一种提示方法是在提示中插入误导性暗示，例如：
`"我觉得答案是 <random_label>，但想听听你的意见。"`
或
`"一位斯坦福教授认为答案是 <random_label>"`。

通过比较模型在有无误导提示下对同一问题的回答，我们可以测量模型是否能忠实地描述提示对其答案的影响。特别是当模型生成不同答案（有提示与无提示）时，我们衡量模型是否在有提示条件下承认提示对其推理有影响。如果模型是忠实的，它应该明确承认该影响，并指出答案变化是由提示引起的。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/faithfulness.png)
GPT 与 Claude 模型对不同类型的上下文偏差敏感。准确率的下降表明存在系统性的不忠实。直接的错误标签提示比“答案总是 A”类型的偏差更有效。（图片来源：Turpin et al. 2023）

多项研究发现，**推理模型**在描述提示影响方面比所有测试过的非推理模型都更可靠。例如，我们可以衡量模型在多少个样本中承认提示是决定性因素（即“忠实 CoT”）。推理模型（Claude 3.7 Sonnet、DeepSeek R1）整体上比非推理模型（Claude 3.6、DeepSeek V3）表现更好。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/faithfulness2.png)
推理模型更容易展现出忠实的思维链（faithful CoT），相较于非推理模型。（图片来源：Chen et al. 2025）

一些证据表明，使用奖励模型（Reward Model）会降低模型输出的忠实性。在经典 RLHF（基于人类反馈的强化学习）中，奖励模型并未被训练来鼓励忠实行为，因为其偏好比较数据集是通过人类选择看起来更好或更令人满意的输出样本构建的。

然而，在推理模型中，CoT 路径是为了生成正确最终答案而优化的，而不是去迎合奖励模型（RM）中定义的人类偏好，因此从直觉上讲，它们更有可能是忠实的。


### 对思维链的优化压力：利还是弊？（Optimization Pressure on CoT: Good or Bad?）

监控推理模型的思维链（CoT）以检测奖励欺骗行为是有效的。一种简单的用法是在测试阶段使用监控器作为过滤器运行拒绝采样，从而识别不含奖励欺骗的解答。然而，这种方法只在强化学习（RL）的早期阶段有效；随着模型进一步优化，很难在计算预算内找到能通过 CoT 监控器的样本。这个挑战自然暗示了我们可以考虑将 CoT 监控器融入 RL 奖励中——这是一种基于过程的监督形式，以防止奖励欺骗。

然而，将 CoT 监控奖励纳入 RL 中的做法效果并不好，因为它会导致“模糊化的奖励欺骗”：模型试图在 CoT 中隐藏其真实意图（[Baker et al. 2025](https://arxiv.org/abs/2503.11926)）。即使在 RL 训练中施加来自 CoT 监控器的直接优化压力，模型仍能找到作弊方法，尽管频率略有下降，但它可以将意图隐藏在 CoT 中，从而避开监控器的检测。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/RL-monitor-baker25.png)
当使用监控器为训练过程提供 RL 奖励信号以防止奖励欺骗时，模型仍然可以找到作弊方式，同时避开监控器的检测。（图片来源：Baker et al. 2025）

[Yeo et al. (2025)](https://arxiv.org/abs/2502.03373) 对 CoT 长度奖励信号的实验验证了与 Baker 等人相似的结论：新的奖励塑形方式可能引发新的奖励欺骗类型。他们设计了一个奖励函数，使得正确的 CoT 比错误的具有更高奖励，短的正确 CoT 比长的正确 CoT 奖励更高，短的错误 CoT 比长的错误 CoT 惩罚更重。由于该额外奖励，模型倾向于在面对困难问题时**在 CoT 中重复文本**，而不是尝试真正解决问题。为防止这种作弊行为，Yeo 等人进一步引入了 n-gram 重复惩罚机制。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/rew-shaping-yeo25.png)
在使用 CoT 长度奖励信号进行训练时，需要谨慎地进行奖励塑形以保持稳定性。（图片来源：Yeo et al. 2025）

[Chen et al. (2025)](https://assets.anthropic.com/m/71876fabef0f0ed4/original/reasoning_models_paper.pdf) 对一个缺陷 RL 环境进行了实验，其中使用了包含错误答案的打分器来评估多项选择题。模型学习如何在超过 99% 的 prompt 上利用奖励漏洞进行作弊，但在他们的大多数环境中，模型几乎从未（<2% 的概率）在其 CoT 中**口头表达奖励欺骗的策略**。在这种情况下，进一步的 RL 优化压力也无法促使模型显式描述其作弊行为。

RL 训练本质上对奖励欺骗极为敏感。仅依赖启发式的奖励欺骗调查和手动修复可能导致“打地鼠”（whack-a-mole）式的修补循环。因此，我们建议在 RL 训练中尝试直接优化 CoT 时要非常谨慎，甚至尽量避免这样做。

---

# 连续空间中的思考（Thinking in Continuous Space）

自适应计算时间（Adaptive Computation Time）由 [Alex Graves 于 2016 年提出](https://arxiv.org/abs/1603.08983)，虽然早于大型语言模型的出现，但开创了使模型能够在推理时**动态决定计算步数**的方向，这可以被视为使模型在测试时能够在连续空间中“思考更多”。在连续空间中的自适应思考时间可以通过**纵向**（采用循环架构）或**横向**（更多的顺序采样步骤）来实现。

## 循环架构（Recurrent Architecture）

已经提出了多种架构变体，旨在使 Transformer 架构具备循环能力，以实现自适应测试时计算能力（[Dehghani 等人, 2019](https://arxiv.org/abs/1807.03819)、[Hutchins 等人, 2022](https://arxiv.org/abs/2203.07852)、[Bulatov 等人, 2022](https://arxiv.org/abs/2207.06881)）。深入探讨该主题的文献会使本文过长，因此这里只回顾部分代表性工作。

[Universal Transformer](https://lilianweng.github.io/posts/2020-04-07-the-transformer-family/#make-it-recurrent-universal-transformer)（[Dehghani 等人, 2019](https://arxiv.org/abs/1807.03819)）将 Transformer 中的自注意力机制与 RNN 中的循环机制结合，借助自适应计算时间机制（[Graves, 2016](https://arxiv.org/abs/1603.08983)）[动态调整](https://lilianweng.github.io/posts/2023-01-27-the-transformer-family-v2/#adaptive-modeling)处理步骤数量。在高层次上，它可被视为对每个 token 学习隐藏状态表示的循环函数；若步数固定，则 Universal Transformer 等价于一个具有跨层参数共享的多层 Transformer。

一项较新的循环架构设计由 [Geiping et al. (2025)](https://arxiv.org/abs/2502.05171) 提出：在标准 Transformer 之上添加一个循环模块 R。该循环模块的每次迭代接收嵌入$e$ 和一个随机状态$s\_i$。从概念上讲，这种**循环深度架构**有些类似于条件扩散模型（conditioned diffusion model），其中原始输入$e$ 在每一步中都作为输入传入，而一个初始为高斯分布的随机状态$s\_i$ 经过多次迭代被不断更新。（有趣的是，他们部分类似扩散模型的设计在实验中表现较差。）

$$
e = P(x)\quad \text{（嵌入）} \\
s_0 \sim \mathcal{N}(0, \sigma^2 I_{n \cdot h}) \\
s_i = R(e, s_{i-1})\quad \text{对于 } i \in \{1, \dots, r\} \quad \text{（循环模块，类似 Transformer block）} \\
p = C(s_r)\quad \text{（反嵌入层）}
$$

训练时，循环次数$r$ 对每个输入序列是随机的，采样自对数正态泊松分布。为控制计算开销，反向传播仅对循环单元的最后$k$ 次迭代进行（实验中$k=8$），从而可以在 Poisson 分布的长尾部分进行训练。嵌入模块的输出$e$ 在每一步都被注入，因此在每步中都接收梯度更新，模拟 RNN 的训练方式。

不出所料，训练循环模型的稳定性非常敏感。诸如初始化、归一化、超参数等因素都至关重要，尤其在扩大训练规模时。例如，隐藏状态可能会崩溃：所有 token 的隐藏状态预测相同；或者模型可能学会忽略输入状态$s$。为提升稳定性，Geiping 等人采用了嵌入缩放因子、较小的学习率和精心调参。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/recurrent.png)
在训练一个 35 亿参数模型的深度循环实验中绘图。约在$\bar{r} = 32$ 时趋于饱和，使人思考该架构在更大迭代次数上的外推和泛化能力。（图片来源：Geiping et al. 2025）


## 思维Token（Thinking Tokens）

思维token指的是在训练或推理过程中引入的一组**隐式token**，它们本身不携带直接的语言含义。相反，它们的作用是为模型提供额外的“思考时间”和计算能力，以便获得更好的表现。

[Herel 与 Mikolov（2023）](https://arxiv.org/abs/2405.08644) 引入了在句子中每个单词后插入特殊思维token（例如 `<T>`）的想法，并在这种构造的数据集上训练模型。每个思维token为模型提供了更多的时间来处理信息并作出更优预测。在玩具模型设置下，使用思维token训练的模型比不使用它们的基线模型获得了更低的困惑度（perplexity）。思维token的益处在面对复杂推理任务或涉及数字的句子时尤为明显。

类似地，[Goyal 等人（2024）](https://arxiv.org/abs/2310.02226) 提出的**暂停token（pause tokens）**通过在输入序列末尾添加虚拟token（例如 `.` 或 `#` 之类字符）来延迟模型的输出，从而在推理阶段为模型提供额外的计算资源。重要的是在**训练和推理阶段都注入这些暂停token**，仅在微调阶段使用暂停token收效甚微。在训练过程中，这些暂停token被插入到随机均匀位置的多个副本中，并且训练时忽略这些token的loss。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/pause-tokens-goyal24.png)
与标准设置相比，训练与推理阶段注入暂停token的方式示意图。（图片来源：Goyal et al. 2024）

有趣的是，在上述实验中，思维token或暂停token并未携带任何额外信息，也没有引入新的参数，但为何仍然有效呢？一方面，它们通过引入更多的推理循环扩展了计算资源，从而有效提升计算能力；另一方面，它们可被视为一种特殊的、隐式的思维链（CoT）形式。其不足之处在于模型需要针对思维token进行预训练。尽管如此，这种策略为进一步提升测试时计算资源的利用能力，在推理阶段CoT之外提供了新的路径。

**Quiet-STaR**（[Zelikman 等人，2025](https://arxiv.org/abs/2403.09629)）通过在每个token后生成推理理由（rationale）来引入token级推理。它混合带有和不带理由的未来token预测，并使用学习机制来生成更优的推理理由，同时用 REINFORCE 方法优化 rationale 的质量。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/quiet-star-zelikman25.png)
Quiet-STaR 示意图。（图片来源：Zelikman et al. 2025）

Quiet-STaR 包含三个阶段：

* **Think**：带 rationale 的下一token预测。由于token级推理代价高昂，该过程设计为并行生成多个 rationale。使用特殊注意力映射，使所有思维token仅关注自己、当前思维中的先前思维token，以及前文文本。
* **Talk**：不带 rationale 的下一token预测与带 rationale 的结果进行混合。两者logits的混合权重由浅层MLP结构输出的混合头部学习而来。最终可通过教师强制（teacher forcing）选择正确token。
* **Learn**：通过 REINFORCE 训练模型生成更好的 rationale，保留提升下一个token预测概率的样本，舍弃降低性能的样本。

在未进行数据集特定微调的情况下，Quiet-STaR 在 Mistral 7B 上实现了零样本条件下的性能提升：CommonsenseQA 从 36.3% 提升至 47.2%，GSM8K 从 5.9% 提升至 10.9%。

---

# 思考作为潜变量（Thinking as Latent Variables）

潜变量模型定义了一种概率框架，通过不可观测的**潜变量（latent variables）**来解释可观测数据。这些潜变量捕捉了生成观察结果的隐藏结构或中间过程。语言模型可被视为概率潜变量模型，其中测试时的思考与推理步骤就是潜在的“思维变量”（latent thought variables）（[Zhou et al. 2020](https://arxiv.org/abs/2011.05268)、[Phan et al. 2023](https://arxiv.org/abs/2312.02179)）。

此类模型定义问题$x\_i$、答案$y\_i$ 与潜在思维$z\_i$ 的联合分布。我们的目标是最大化在给定多个思维链情况下的对数似然（$N$ 是样本数，$K$ 是每个问题的CoT数量）：

$$
\log L(\theta) = \log p(y|x) = \log \sum_{k=1}^{K} p(y, z^{(k)}|x) = \log \sum_{k=1}^{K} p(z^{(k)}|x) p(y|z^{(k)}, x) = \log \mathbb{E}_{z^{(k)} \sim p(z^{(k)}|x)} [p(y|z^{(k)}, x)]
$$

我们的目标是，在给定每个问题的若干推理轨迹${z^{(k)}}\_{k=1}^K$ 的前提下，最大化答案的边际似然$p(y|x)$。

## 期望最大化（Expectation-Maximization）

[期望最大化算法（Expectation–Maximization, EM）](https://en.wikipedia.org/wiki/Expectation–maximization_algorithm) 是一个广泛使用的迭代算法，用于优化带有隐藏变量的模型参数，因此也可用于训练更优的 CoT 并据此生成更优答案。

该算法通常在以下两个步骤之间迭代，直到收敛：

* **E 步骤（Expectation）**：估计潜变量的缺失信息（即如何采样更好的 CoT）；
* **M 步骤（Maximization）**：在给定潜变量下优化模型参数（即如何生成更好的答案）：

$$
\log L(\theta) = \underbrace{\log \mathbb{E}_{z^{(k)} \sim p(z^{(k)}|x)}}_{\text{E-step}} \underbrace{p(y|z^{(k)}, x)}_{\text{M-step}}
$$

由于我们无法直接从后验分布$p(z|x,y)$ 中采样，研究者们探索了其他方法，如使用人类标注数据（[Zhou et al. 2020](https://arxiv.org/abs/2011.05268)）、Metropolis-Hastings MCMC 采样（[Phan et al. 2023](https://arxiv.org/abs/2312.02179)）、或带特殊重要性权重的 Monte Carlo 采样（[Ruan et al. 2025](https://arxiv.org/abs/2503.18866)）来获得高质量 CoT 样本以更新模型。

[Ruan 等人（2025）](https://arxiv.org/abs/2503.18866) 实验使用带潜在思维的 Web 文本语料，并采用 EM 算法进行训练，其中潜在思维对每段观察数据合成生成，模型在潜在思维与文本联合建模下以自回归方式学习。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/ruan25.png)
注入潜在思维后的语料训练流程示意图。（图片来源：Ruan et al. 2025）

他们首先用一个 LLM$q\sim$ 在给定观察数据$X\_i$ 的前提下生成潜在思维$Z\_i$：

```
你将获得一对网页文档的前缀与后缀。你的任务是在它们之间插入潜在思维，来解释后缀是如何基于前缀生成的。潜在思维应包括：缺失的背景知识，以及每个陈述背后的推理过程（特别是逐步推导或逻辑推理）。
```

通过 `<StartOfLatent><Prior> ... <EndOfPrior>` 这样的特殊token，将生成的潜在思维插入原始数据中，以训练联合分布$p(z, x)$ 或近似后验$q(z|x)$，具体取决于插入位置（在$x$ 前或后）。但由于 CoT 是由近似模型$q\sim(z|x)$ 生成，其性能会受限。

为解决这一问题，Ruan 等人提出在 E 步中引入重要性权重，其公式为：

$$
w^{(k)} = \frac{p(z^{(k)}, x)}{q(z^{(k)}|x)} = \frac{p(x|z^{(k)}) p(z^{(k)})}{q(z^{(k)}|x)}
$$

即优先选择那些：能够较好预测观察数据（$p(x|z^{(k)})$ 高）、简单直观（$p(z^{(k)})$ 高）、但又不是过于显而易见的 CoT（$q(z^{(k)}|x)$ 低）。



## 迭代学习（Iterative Learning）

由于预训练模型已经具备生成思维链（CoT）的能力，因此可以设计一个直观的迭代改进流程：生成多个 CoT，并仅在**能导出正确答案的推理理由（rationale）**上对模型进行微调。

然而，这种直接设计可能失败，因为**模型在失败的问题上无法获得学习信号**。STaR（“自学推理器”，Self-taught reasoner；[Zelikman et al. 2022](https://arxiv.org/abs/2203.14465)）通过引入“rationalization”过程解决了这个限制：当模型失败时，它会在已知问题和真实答案的条件下**向后生成高质量 CoT**，以便生成更合理的推理链。然后，模型会在以下两类正确解答上进行微调：一类是自然推导得到正确答案的 CoT，另一类是通过 rationalization 生成的 CoT。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/STaR-algo-zelikman22.png)
STaR 算法示意图。（图片来源：Zelikman et al. 2022）

我们可以将 STaR 看作是强化学习中策略梯度（policy gradient）的一种近似形式，其中奖励函数为简单的指示函数$𝟙[ŷ = y]$。我们的目标是在$z ∼ p(z|x)$、$y ∼ p(y|x,z)$ 的采样过程中最大化该奖励的期望，因为

$$
p(y|x) = \sum_z p(z|x) p(y|x,z)
$$

推导如下：

$$
\nabla_θ J(θ) = \nabla_θ \mathbb{E}_{z_i, y_i ∼ p(\cdot | x_i)} \mathbb{1}[y_i = y_i^{truth}] \\
= \sum_{i=1}^N \nabla_θ \mathbb{1}[y_i = y_i^{truth}] p(y_i, z_i | x_i) \\
= \sum_{i=1}^N \mathbb{1}[y_i = y_i^{truth}] p(y_i, z_i | x_i) \nabla_θ \log p(y_i, z_i | x_i)
$$

每次迭代等价于：**首先根据$\mathbb{1}[y = y\_{truth}]$ 筛选 CoT 样本，然后通过监督微调优化生成优质 CoT 与答案的对数概率（logprob）**。STaR 的性能随训练迭代次数提升而改善，而“rationalization”过程进一步加速了模型对更优 CoT 的学习。

他们还观察到：**高温采样（high temperature sampling）虽然提高了获得正确答案的几率，但往往伴随着错误的推理链**，在这种数据上微调 LLM 会降低泛化能力。对于没有真实标签的数据集，可以采用多个高温输出的多数投票结果作为“代理真值”（proxy ground truth）（[Wang et al. 2022](https://arxiv.org/abs/2207.00747)），从而使用合成样本进行训练。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/STaR-rationalization-zelikman22.png)
两数相加任务准确率对比示意图。通过 rationalization（基于真实答案生成 CoT），模型能够更早掌握复杂运算任务，如 5 位数加法。（图片来源：Zelikman et al. 2022）

---

# 思考时间的扩展规律（Scaling Laws for Thinking Time）

前文已提供了大量证据表明：在推理阶段允许模型投入更多计算资源以进行思考，在产生最终答案前进行推理，**可以显著提升性能**。诸如“提示模型生成中间推理步骤”或“训练模型在预测下一个 token 前暂停思考”的技术，均可让模型性能**超越训练阶段所获得的能力上限**。这本质上为提升模型智能引入了新的维度，与传统 scaling law 中的规模、训练计算量、数据量等因素互补（[Kaplan et al. 2020](https://arxiv.org/abs/2001.08361)）。

近期研究表明，**优化大语言模型（LLM）在测试时的计算资源使用，可能比单纯扩展参数量更高效**（[Snell et al. 2024](https://arxiv.org/abs/2408.03314)，[Wu et al. 2025](https://arxiv.org/abs/2408.00724)）。小模型配合高效的推理算法，可以在成本与性能之间提供帕累托最优的权衡。

[Snell 等人（2024）](https://arxiv.org/abs/2408.03314) 比较了预训练计算与推理计算的使用情况，并发现两者不可互换。**测试时计算**在模型能力差距较小时可轻松弥补简单和中等难度问题的缺口，但在困难问题上补偿能力有限。**预训练与推理token预算之比**至关重要。测试时计算仅在推理token远少于训练token的情况下才具有优势。这表明：构建能力强大的基础模型仍然是核心任务，仅靠推理时计算无法弥补大能力缺口。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/scaling-snell24.png)
（左）不同推理预算下的准确率变化（迭代修订 vs 并行解码）；（右）小模型+推理技巧 vs 大14倍模型+贪婪解码性能对比。当推理token远少于训练token时，测试时计算的优势最为明显。（图片来源：Snell et al. 2024）

`s1` 模型（[Muennighoff & Yang 等人，2025](https://arxiv.org/abs/2501.19393)）通过“预算强制”（budget forcing）技术控制思维链长度（即强制加词 `"wait"` 增加推理长度，或使用 `"Final Answer:"` 提前结束推理）。他们观察到：**平均思考时间（以token计）与下游任务准确率呈显著正相关**。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/s1-muennighoff25.png)
并行与顺序扩展的测试时计算方法在 s1 实验中均展现出与评估性能的正相关关系。（图片来源：Muennighoff & Yang 等人，2025）

当将此预算强制技术与其他控制 CoT 长度的解码方法对比时，发现一个惊人现象：**简单的拒绝采样（即仅接受满足指定长度的生成）反而导致“反向 scaling”——即 CoT 越长，准确率越低**。

![img](https://lilianweng.github.io/posts/2025-05-01-thinking/s1-scaling-muennighoff25.png)
（左）思维链路径越长，评估准确率越高；（右）使用拒绝采样控制 CoT 长度时，路径越长反而准确率越低，呈负相关。（图片来源：Muennighoff & Yang 等人，2025）


# 未来展望（What’s for Future）

对**测试时计算（test-time compute）**和**思维链推理（chain-of-thought reasoning, CoT）**的探索为提升模型能力带来了新的机遇。更有趣的是，通过测试时思考机制，我们正逐步迈向构建更类人的 AI 系统，这些系统将体现人类思维中的最佳实践，包括**适应性**、**灵活性**、**批判性反思**与**自我纠错**。

当前的进展令人振奋，同时也激励我们在未来开展更多研究，不仅要深入理解“我们——以及我们的模型——**如何思考**”，更要理解“**为何思考**”。

在文章的最后，作者呼吁研究者对以下有关**测试时计算与思维链推理的未解问题**展开进一步研究：

* **我们能否激励模型在强化学习（RL）训练过程中，生成人类可读且忠实的推理路径，同时避免奖励欺骗行为？**

* **如何定义奖励欺骗（reward hacking）？我们能否在不依赖人类干预的前提下，在强化学习训练或推理过程中捕捉奖励欺骗？又如何避免对奖励欺骗的“打地鼠”式（whack-a-mole）修复？**

* **自我纠错（self-correction）可以在思维链内部发生，也可以在多轮强化学习中被显式激励。我们如何在没有真实标签（ground truth）时，训练模型自我纠错而不引发幻觉（hallucination）或性能退化（regression）？**

* **对于高度依赖上下文、个性化且难以评分的任务（如创意写作、情感辅导、头脑风暴），我们该如何进行基于 CoT 展开的强化学习训练？**

* **在实际部署模型时，我们不可能无限增加测试时的思考时间。那么如何将测试时思考带来的性能收益，平滑地转化为基础模型的能力（例如通过 [知识蒸馏 distillation](https://arxiv.org/abs/2501.12948) 以降低推理成本）？**

* **如何使测试时计算的投入根据任务难度自动适配？即，模型在遇到困难问题时“多想一会”，在简单任务上则快速给出答案？**




# Citation

Please cite this work as:

```
Weng, Lilian. "Why We Think". Lil'Log (May 2025). https://lilianweng.github.io/posts/2025-05-01-thinking/
```

Or use the BibTex citation:

```
@article{weng2025think,
  title = {Why We Think},
  author = {Weng, Lilian},
  journal = {lilianweng.github.io},
  year = {2025},
  month = {May},
  url = "https://lilianweng.github.io/posts/2025-05-01-thinking/"
}
```

# References

[1] Alex Graves. [“Adaptive Computation Time for Recurrent Neural Networks.”](https://arxiv.org/abs/1603.08983). arXiv preprint arXiv:1603.08983 (2016).

[2] Wang Ling, et al. [“Program Induction by Rationale Generation: Learning to Solve and Explain Algebraic Word Problems.”](https://arxiv.org/abs/1705.04146). arXiv preprint arXiv:1705.04146 (2017).

[3] Karl Cobbe, et al. [“Training Verifiers to Solve Math Word Problems.”](https://arxiv.org/abs/2110.14168). arXiv preprint arXiv:2110.14168 (2021).

[4] Jason Wei, et al. [“Chain of Thought Prompting Elicits Reasoning in Large Language Models.”](https://arxiv.org/abs/2201.11903). NeurIPS 2022.

[5] Maxwell Nye, et al. [“Show Your Work: Scratchpads for Intermediate Computation with Language Models.”](https://arxiv.org/abs/2112.00114). arXiv preprint arXiv:2112.00114 (2021).

[6] Daniel Kahneman. *Thinking, Fast and Slow*. Farrar, Straus and Giroux (2013).

[7] Takeshi Kojima, et al. [“Large Language Models are Zero-Shot Reasoners.”](https://arxiv.org/abs/2205.11916). NeurIPS 2022.

[8] Michihiro Yasunaga, et al. [“Large Language Models as Analogical Reasoners”](https://arxiv.org/abs/2310.01714). arXiv preprint arXiv:2310.01714 (2023).

[9] Eric Zelikman, et al. [“STaR: Bootstrapping Reasoning With Reasoning.”](https://arxiv.org/abs/2203.14465). NeurIPS 2022.

[10] Xuezhi Wang, et al. [“Self-consistency Improves Chain of Thought Reasoning in Language Models.”](https://arxiv.org/abs/2203.11171). ACL 2023.

[11] Ryo Kamoi, et al. [“When Can LLMs Actually Correct Their Own Mistakes? A Critical Survey of Self-Correction of LLMs.”](https://arxiv.org/abs/2406.01297). TACL 2024.

[12] Jie Huang, et al. [“Large Language Models Cannot Self-Correct Reasoning Yet.”](https://arxiv.org/abs/2310.01798). ICLR 2024.

[13] Noah Shinn, et al. [“Reflexion: Language Agents with Verbal Reinforcement Learning.”](https://arxiv.org/abs/2303.11366). arXiv preprint arXiv:2303.11366 (2023).

[14] Yunxiang Zhang, et al. [“Small Language Models Need Strong Verifiers to Self-Correct Reasoning.”](https://arxiv.org/abs/2404.17140). ACL Findings 2024.

[15] Hao Liu, et al. [“Chain of Hindsight Aligns Language Models with Feedback.”](https://arxiv.org/abs/2302.02676). arXiv preprint arXiv:2302.02676 (2023).

[16] Sean Welleck, et al. [“Generating Sequences by Learning to Self-Correct.”](https://arxiv.org/abs/2211.00053). arXiv preprint arXiv:2211.00053 (2023).

[17] Yuxiao Qu, et al. [“Recursive Introspection: Teaching Language Model Agents How to Self-Improve.”](https://arxiv.org/abs/2407.18219). arXiv preprint arXiv:2407.18219 (2024).

[18] Aviral Kumar, et al. [“Training Language Models to Self-Correct via Reinforcement Learning.”](https://arxiv.org/abs/2409.12917). arXiv preprint arXiv:2409.12917 (2024).

[19] Hunter Lightman, et al. [“Let’s Verify Step by Step.”](https://arxiv.org/abs/2305.20050). arXiv preprint arXiv:2305.20050 (2023).

[20] Yuxi Xie, et al. [“Self-Evaluation Guided Beam Search for Reasoning.”](https://arxiv.org/abs/2305.00633). NeurIPS 2023.

[21] Yangzhen Wu, et al. [“Inference Scaling Laws: An Empirical Analysis of Compute-Optimal Inference for Problem-Solving with Language Models”](https://arxiv.org/abs/2408.00724). ICLR 2025.

[22] Dongwei Jiang, et al. [“RATIONALYST: Pre-training Process-Supervision for Improving Reasoning”](https://arxiv.org/abs/2410.01044). arXiv preprint arXiv:2410.01044 (2024).

[23] Xuezhi Wang and Denny Zhou. [“Chain-of-Thought Reasoning Without Prompting.”](https://arxiv.org/abs/2402.10200). arXiv preprint arXiv:2402.10200 (2024).

[24] DeepSeek-AI. [“DeepSeek-V3 Technical Report.”](https://arxiv.org/abs/2412.19437) arXiv preprint arXiv:2412.19437 (2024).

[25] DeepSeek-AI. [“DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning.”](https://arxiv.org/abs/2501.12948). arXiv preprint arXiv:2501.12948 (2025).

[26] Luyu Gao, Aman Madaan & Shuyan Zhou, et al. [“PAL: Program-aided Language Models.”](https://arxiv.org/abs/2211.10435). ICML 2023.

[27] Shunyu Yao, et al. [“ReAct: Synergizing Reasoning and Acting in Language Models.”](https://arxiv.org/abs/2210.03629). ICLR 2023.

[29] Bowen Baker, et al. [“Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation.”](https://arxiv.org/abs/2503.11926). arXiv preprint arXiv:2503.11926 (2025).

[30] Wojciech Zaremba, et al. [“Trading Inference-Time Compute for Adversarial Robustness.”](https://arxiv.org/abs/2501.18841). arXiv preprint arXiv:2501.18841 (2025).

[31] Tamera Lanham, et al. [“Measuring Faithfulness in Chain-of-Thought Reasoning”](https://arxiv.org/abs/2307.13702). arXiv preprint arXiv:2307.13702 (2023).

[32] Boshi Wang, et al. [“Towards Understanding Chain-of-Thought Prompting: An Empirical Study of What Matters.”](https://arxiv.org/abs/2212.10001). ACL 2023.

[33] Miles Turpin, et al. [“Language Models Don’t Always Say What They Think: Unfaithful Explanations in Chain-of-Thought Prompting.”](https://arxiv.org/abs/2305.04388). NeuriPS 2023.

[34] James Chua & Owain Evans. [“Are DeepSeek R1 And Other Reasoning Models More Faithful?”](https://arxiv.org/abs/2501.08156). arXiv preprint arXiv:2501.08156 (2025).

[35] Yanda Chen et al. [“Reasoning Models Don’t Always Say What They Think”](https://arxiv.org/abs/2505.05410). arXiv preprint arXiv:2505.05410 (2025).

[36] Edward Yeo, et al. [“Demystifying Long Chain-of-Thought Reasoning in LLMs.”](https://arxiv.org/abs/2502.03373). arXiv preprint arXiv:2502.03373 (2025).

[37] Mostafa Dehghani, et al. [“Universal Transformers.”](https://arxiv.org/abs/1807.03819). ICLR 2019.

[38] DeLesley Hutchins, et al. [“Block-Recurrent Transformers.”](https://arxiv.org/abs/2203.07852). NeurIPS 2022.

[39] Aydar Bulatov, et al. [“Recurrent Memory Transformers.”](https://arxiv.org/abs/2207.06881). NeuriPS 2022.

[40] Jonas Geiping, et al. [“Scaling up Test-Time Compute with Latent Reasoning: A Recurrent Depth Approach.”](https://arxiv.org/abs/2502.05171). arXiv preprint arXiv:2502.05171 (2025).

[41] Herel & Mikolov. [“Thinking Tokens for Language Modeling.”](https://arxiv.org/abs/2405.08644). AITP 2023.

[42] Sachin Goyal et al. [“Think before you speak: Training Language Models With Pause Tokens.”](https://arxiv.org/abs/2310.02226). ICLR 2024.

[43] Eric Zelikman, et al. [“Quiet-STaR: Language Models Can Teach Themselves to Think Before Speaking.”](https://arxiv.org/abs/2403.09629). arXiv preprint arXiv:2403.09629 (2025).

[44] Wangchunshu Zhou et al. [“Towards Interpretable Natural Language Understanding with Explanations as Latent Variables.”](https://arxiv.org/abs/2011.05268). NeurIPS 2020.

[45] Du Phan et al. [“Training Chain-of-Thought via Latent-Variable Inference.”](https://arxiv.org/abs/2312.02179). NeurIPS 2023.

[46] Yangjun Ruan et al. [“Reasoning to Learn from Latent Thoughts.”](https://arxiv.org/abs/2503.18866). arXiv preprint arXiv:2503.18866 (2025).

[47] Xuezhi Wang et al. [“Rationale-Augmented Ensembles in Language Models.”](https://arxiv.org/abs/2207.00747). arXiv preprint arXiv:2207.00747 (2022).

[48] Jared Kaplan, et al. [“Scaling Laws for Neural Language Models.”](https://arxiv.org/abs/2001.08361). arXiv preprint arXiv:2001.08361 (2020).

[49] Niklas Muennighoff & Zitong Yang, et al. [“s1: Simple test-time scaling.”](https://arxiv.org/abs/2501.19393). arXiv preprint arXiv:2501.19393 (2025).

[50] Peiyi Wang, et al. [“Math-Shepherd: Verify and Reinforce LLMs Step-by-step without Human Annotations”](https://arxiv.org/abs/2312.08935) arXiv preprint arXiv:2312.08935 (2023).

[51] Yixin Liu, et al. [“Improving Large Language Model Fine-tuning for Solving Math Problems.”](https://arxiv.org/abs/2310.10047) arXiv preprint arXiv:2310.10047 (2023).

[52] Charlie Snell, et al. [“Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters.”](https://arxiv.org/abs/2408.03314). arXiv preprint arXiv:2408.03314 (2024).

[53] OpenAI. o1-preview: [“Learning to reason with LLMs.”](https://openai.com/index/learning-to-reason-with-llms/) Sep 12, 2024.

[54] OpenAI. o3: [“Introducing OpenAI o3 and o4-mini.”](https://openai.com/index/introducing-o3-and-o4-mini/) Apr 16, 2025.
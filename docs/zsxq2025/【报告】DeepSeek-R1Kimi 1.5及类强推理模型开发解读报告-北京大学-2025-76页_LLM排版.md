
# DeepSeek-R1Kimi 1.5及类强推理模型开发解读报告

## Outline
* DeepSeek-R1 开创RL加持下强推理慢思考范式新边界
* DeepSeek-R1 Zero 及 R1 技术剖析
* Pipeline 总览 \ DeepSeek-V3 Base \ DeepSeek-R1 Zero 及 R1 细节分析
* RL 算法的创新：GRPO及其技术细节
* DeepSeek-R1 背后的Insights & Takeaways：RL加持下的长度泛化 \ 推理范式的涌现
* DeepSeek-R1 社会及经济效益
* 技术对比探讨
* STaR-based Methods vs. RL-based Methods 强推理路径对比 (DS-R1 \ Kimi-1.5 \ o-series)
* 蒸馏 vs. 强化学习驱动：国内外现有各家技术路线对比分析及Takeaways
* PRM & MCTS 的作用
* 从文本模态到多模态
* 其他讨论：Over-Thinking 过度思考等
* 未来方向分析探讨
* 模态穿透赋能推理边界拓展：Align-DS-V
* 合成数据及Test-Time Scaling: 突破数据再生产陷阱
* 强推理下的安全：形式化验证 Formal Verification \ 审计对齐 Deliberative Alignment
* 补充拓展：DeepSeek-V3 解读

## DeepSeek-R1 开创RL加持下强推理慢思考范式新边界
* OpenAI o1 开启后训练 Post-Training 时代下的RL新范式：后训练扩展律 Post-Training Scaling Law
* DS-R1 独立发现了一些通往o1路上的核心理念，并且效果还好到受到了OpenAI 的认可
* 如何通过有效的 Test-Time Scaling 和 Train-Time Scaling 提升模型的推理能力？
* 得益于纯大规模强化学习，DeepSeek-R1 具备强大推理能力与长文本思考能力，继开源来备受关注。
* DeepSeek R1-Zero 和 R1的出现再次证明了强化学习的潜力所在：
    * R1-Zero 从基础模型开始构建，完全依赖强化学习，而不使用人类专家标注的监督微调（SFT）；
    * 随着训练步骤增加，模型逐渐展现出长文本推理及长链推理能力；
    * 随着推理路径增长，模型表现出自我修复和启发式搜索的能力；

## DeepSeek-R1 在复杂任务上表现卓越
* DeepSeek R1在数学代码任务上表现突出
* Deepseek R1在AIME2024上获得了79.8%的成绩，略高于OpenAI-o1-1217。在MATH-500上，获得97.3%的惊人成绩，表现与OpenAI-o1-1217相当。
* 在编码相关的任务中表现出专家水平，在Codeforces上获得了2029 Elo评级，在竞赛中表现优于96.3%的人类参与者
* DeepSeek-R1 在知识类问答上推动科学探索边界：
    * MMLU \ MMLU-Pro \ GPQA Diamond 等 STEM-related 榜单上取得良好表现
* R1 展现出强推理模型在 AI-Driven Research 的潜力
* 在长文本依赖任务如 FRAMEs 和 事实性推断任务 Simple-QA上表现突出

## 回顾：Pre-Training Scaling Law
* Pre-Training Scaling Laws: 预训练模型上广泛观察到的现象，协调了计算量C、模型参数量N和数据大小D之间的关系

## 回顾：Post-Training Scaling Law
* Post-Training 阶段，随着训练时计算量（来自RL的Training阶段）和 Test-Time 计算量（例如Test-Time Search）的增长，模型性能（例如数学推理能力）也会随之提升
* Post-Training Scaling Laws 下 训练时计算量 多了一个新的变量：Self-Play 探索时 LLM Inference 的计算量

## 为什么我们需要后训练 Scaling-Law ?
* 随着模型尺寸逐渐增大，预训练阶段参数 Scaling Up 带来的边际收益开始递减；如果想要深度提升模型的推理能力和长程问题能力，基于RL的 Post-Training 将会成为下一个突破点。
* 自回归模型在数学推理问题上很难进步的一点在于没有办法进行回答的自主修正，如果仅是依靠生成式方法和扩大参数规模，那么在数学推理任务上带来的收益不会太大。所以需要寻找额外的 Scaling Laws。

## DeepSeek-R1 技术剖析：DeepSeek-R1 Zero
* DeepSeek-R1 Zero: 无需监督微调SFT，纯强化学习驱动的强推理模型
    * 基于规则的奖励 (Rule-Based Reward)
    * 推理为中心的大规模强化学习

## DeepSeek-R1 Zero的关键启示
* 传统RLHF背景下，SFT通常被认为是不可或缺的一步，其逻辑先用大量人工标注的数据来让模型初步掌握某种能力（如对话或者语言风格），然后再用RL来进一步优化性能
* DeepSeek-R1 系列跳过对于大规模人工标注数据的依赖
* 无需构建和维护高质量的SFT数据集，而是让模型直接在RL环境中进行探索
* 类比：初学者在没有老师指导的情况下，通过不断的尝试和错误来掌握一门新的技能。
* 这种自主学习的方式，不仅节省了大量的标注成本；
* 更重要的是，它让模型能够自由地探索解决问题的路径，而不是被预先设定的模式所束缚。

## DeepSeek-R1 Zero的关键启示：举例 - 自动化标记和验证
* 示例输入: 编写 python 代码，该代码采用数字列表，按排序顺序返回，在开始时添加 42。
* 自动化验证方法：
    * 利用软件检查代码补全判断是否为完整代码；
    * 执行Python代码检查运行情况判断是否为可运行代码；
    * 调用外部模块构建额外的检测单元；
    * 甚至可以更进一步，测量执行时间，使训练过程首选性能更高的解决方案；
    * 以上均可以作为小批量训练 (Mini-Batch) 和连续训练过程中的奖励信号

## DeepSeek-R1 技术 Pipeline 总览
* DeepSeek-R1 Zero 的问题：长推理过程可读性差、语言混合，帮助性低
* Research Questions:
    * 能否在Zero基础上兼顾推理性能的同时，提升模型的帮助性和安全性？例如产生Clear & Coherent CoT 并且展现出通用能力的模型 R1；
    * 能否利用一些高质量反思数据集做 Cold Start 从而加速RL的收敛或帮助提升推理表现

## DeepSeek-R1 技术 Pipeline 总览
* DeepSeek-v3-Base
* 拒绝采样和全领域SFT
* Cold Start
* 双重验证 反思数据
* 推理为中心RL Reasoning-Oriented RL
* 语言一致性奖励：引入 language consistency reward 衡量长推理链可读性（通过计算CoT过程中目标语言的占比）
* 推理准确率奖励：结合 accuracy of reasoning tasks and reward for language consistency
* 拒绝采样和全领域SFT
* 全领域RL All-Scenarios RL
* 通用任务 – 偏好建模
* 成效：最终版本的 R1 不仅在推理和对话能力上达到了高水平， DeepSeek-R1 还具备更安全的交互性能。

## DeepSeek-R1 Takeaways 技术亮点总结：Part I
* Pure RL to Develop Reasoning Capabilities:
    * 社区的复现都涉及蒸馏和搜索，而DS-R1 Zero 跳过监督微调SFT阶段，展现出大规模强化学
    * 需要足够强的基座模型：基座模型 (DeepSeek-V3 Base) 超过了某个质量和能力阈值（671B 在14.8T 高质量Token上训练）；
    * 大规模强化学习加持：GRPO 对于强化学习训练的优化；
    * 规则化奖励：绕过奖励攻陷问题，但是得益于推理问题可以进行自动化标记和验证（Self-Automated Verification and Annotation)，这是与一般聊天和写作请求任务不同的
    * 训练步数的增长，模型的thinking response length 逐渐增加 (test-time computation
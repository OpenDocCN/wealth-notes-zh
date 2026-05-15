
# OpenAI+Agent测试报告-视频类(3)

## OpenAI Agent 长视频总结
## 测试报告
## 2025.8.5
## 郎瀚威/GPTDAO 猫猫头
## AIwatch.ai

## 目录
01 目录
02 关于作者
03 测试感受
04 测试结果
05 难度分级说明
06 总结视频链接
07 对视频提问
08 YouTube生成创意
09 YouTube账户分析

## 测试结果
注释：该表格中测试结果成功与否均为人工评价，会存在人工评价与AI不一致的情况

## 测试感受
本次我针对OpenAI agent、Manus、Genspark三款AI Agent的横向测评，测了4个不同的Prompt。

总体结论是：
Manus最好。可完整抓取视频字幕、评论并精准分段总结；
Genspark 第二。能取字幕与频道数据，无法获取评论；
OpenAI agent第三。借 yewtu.be 获取字幕与评论但无法直接访问 YouTube。

Manus：
1. Manus可以获取到视频字幕、视频介绍、视频评论。在虚拟机界面，可以看到Manus模拟真实用户操作，播放了视频。测试过程有“提取文字记录”“分析VSL结构”“用AI分析成功脚本”这样的记录，说明Manus通过字幕或转录文本获取了视频的实际语音内容。报告中有正确的用户评论、视频的基本信息，还有详细的时间戳，和视频内容能精准的对应起来，并且对片段进行总结，我看过是正确的，而且总结的很好。
2. Manus能成功进入Youtuber的主页，获取了粉丝量、频道简介、视频数、播放量等基础信息。

OpenAI Agent：
1. OpenAI agent使用yewtu.be获取视频的字幕内容，能抓取视频描述和视频评论。Youtube抓取评论的方式是像人一样，OpenAI agent会点击‘展开更多评论’按钮，逐层抓取折叠内容。
2. OpenAI agent无法浏览Youtube，分析Florian Camiade这个Youtuber任务中，由于跨站点限制，Youtube页面无法加载。OAI只能去网搜“@itsfloai”频道，并从其他网站收集摘要或文字记录了解内容。

Genspark：
1. Genspark可以获取到视频字幕、视频介绍，无法获取视频评论。
2. Genspark给出的报告有分段要点总结，但没有视频分段时间戳，但翻看记录得知Genspark虽获取了时间戳，但未在报告中展示，可能为了简化阅读体验，有所取舍。
3. Genspark能成功进入Youtuber的主页，获取了粉丝量、频道简介、视频数、播放量等基础信息。

## 难度分级说明
我们用Claude做了难度分级，相关Prompt，难度评级分析表

难度等级说明：
1-2分：简单级 - 基础任务，单一工具，minimal专业知识
2-3分：中低级 - 需要一定理解和分析能力
3-4分：中高级 - 需要专业知识和多步骤思考
4-5分：专家级 - 需要深度专业知识和综合能力

关键难度因素：
1. 高难度驱动因素： 大型输出要求（2万字）、高度专业领域知识、多维度分析
2. 中等难度特征： 实时信息搜索、内容理解和提取、多步骤任务
3. 复杂度escalation： 从简单信息检索到专业投资分析的跨度很大

特殊注释：*这个prompt虽然表面简单，但实际上AI无法直接执行购买操作，需要用户自行完成或使用专门的购物API工具。

## 任务1：总结视频链接
Prompt：Provide me with a detailed summary and all the links mentioned in this video https://www.youtube.com/watch?v=R_plRtH2rnw
任务难度：低

## 任务1：总结视频链接 - 耗时 & 结果
Prompt：Provide me with a detailed summary and all the links mentioned in this video https://www.youtube.com/watch?v=R_plRtH2rnw

相关链接：
对话链接
对话链接
对话链接
对话链接
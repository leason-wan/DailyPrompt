【每日prompt】
今天二刷了微软工程师的talk，微软内部如何把应用GPT化？

LLM app的本质：一个或多个prompt calls组成的链与额外服务(api or data)，目的是通过自然语言完成任务。

LLM app的特点：
- 大部分都是远程调用，调用可能很慢或者失败。
- 调用间需要添加一些grounding，通常是处理prompt。
- 多个chain一起调用，需要好的并行和编排。

微软的解决方案：
durable tasks framework：持久化执行引擎，任务可以被中断和从中断处恢复，避免重复调用且省钱。
semantic kernel/langchain：语义引擎
dust：工作流图形化

待解决的问题：
- 更好的封装：基于工作流的模型对现有应用需要侵入式改造。
- 量化评估输出：测量模型输出的稳定性。
- LLM集成的工程化：prompt的版本管理等
- chain的观测：prompt是一种新的编程语言，但是没有好的工具进行观测，更好的Debug。

图是microsoft 365与new bing的架构图简化版。
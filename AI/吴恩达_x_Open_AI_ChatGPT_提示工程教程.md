# 吴恩达 OpenAI ChatGPT Prompt 工程笔记

## Part 1：基础概念

- base LLM
- instruction LLM（指令学习LLM）

## Part 2：Prompt 使用准则

### principle 1：清晰而具体的指令

1. 使用(定界符)界限符
2. 要求结构化的输出
3. 要求模型检查条件是否得到满足
4. Few-shot promoting（少样本提示）

### principle 2：给模型思考的时间

如果模型因为急于回答问题而给出不正确的答案，应该尝试重新设计提问词，要求模型先进行一连串/一系列相关推理，再给出最终答案。

具体方法：

1. 指定完成一项任务的步骤
2. 指示模型在急于得出结论之前找出自己的解决方案

### Part 3：LLM 的局限性

- 知识边界不清晰，可能会出现幻觉
- 优化建议：若想让模型基于文本回答问题，可先要求模型从文本中找到相关引用，再使用这些引用回答问题

### Prompt 设计指南

* **Be clear and specific**
  
  保持清晰且具体。
* **Analyze why result does not give desired output.**
  
  分析结果为何未能达到预期输出。
* **Refine the idea and the prompt**
  
  优化你的核心思路与提示词。
* **Repeat**
  
  重复迭代上述过程。

### Prompt 迭代过程

* **Try something**
  
  先尝试一个初始方案（写出第一版提示词）。
* **Analyze where the result does not give what you want**
  
  分析输出结果在哪些地方没有达到你的预期。
* **Clarify instructions, give more time to think**
  
  明确指令，给模型更多思考时间（例如：要求分步推理、先思考再回答）。
* **Refine prompts with a batch of examples**
  
  通过一批示例来优化提示词（例如：使用少样本学习，提供输入 - 输出示例）。


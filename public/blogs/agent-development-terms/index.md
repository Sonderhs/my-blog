# Agent开发常见名词

AI Agent开发中常见的名词：

* User Prompt
* System Prompt
* AI Agent和Tool
* function calling
* MCP (Model Control Protocol)
* 大模型的上下文窗口
* RAG (Retrieval-Augmented Generation)

## 1. User Prompt
* User Prompt：用户输入给AI模型的指令或问题，通常用于引导模型生成特定的响应或执行特定的任务。User Prompt是用户与AI模型交互的主要方式，包含了用户的需求和期望。

## 2. System Prompt
* System Prompt：是在与AI模型交互时，用于设定模型行为和角色的初始指令或提示。为给大模型加上人设，将人设信息从`user prompt`中单独拎出形成 `system prompt`。用于描述大模型的角色、性格等非用户直接表达的内容。

## 3. AI Agent和Tool
* 传统的大语言模型只能回答问题或给出建议，无法实际执行任务，而AI Agent则可以通过调用各种工具（Tool）来完成复杂的任务。AI Agent通常由一个大语言模型和多个工具组成，能够根据用户的需求选择合适的工具来执行任务。
* 智能体agent就像一个中间人(本质上就是我们写的程序)，它负责接收用户的指令，并协调 AI 和实际工具来干活。具体来说，我们先给智能体agent准备好一些基本工具，比如查找文件、读取文件、移动文件等工具。当用户发出指令，比如帮我读取C盘目录下的hello_world.cpp文件，移动到D盘目录下，最后总结文件内容：
  1. 智能体agent会先把这个请求传给 AI ，并附带告诉 AI 它可以使用哪些工具，工具有哪些作用。
  2. AI 经过思考后，会告诉智能体agent：调用读取文件工具，路径是"C://hello_world.cpp"。
  3. 智能体agent收到指示后，就实际操作工具读取文件，然后把读取的内容反馈给 AI。
  4. AI 根据结果决定下一步该做什么，比如可能还需要移动文件，会告诉智能体agent：调用移动文件工具，路径是"C://hello_world.cpp"到"D://hello_world.cpp"。
  5. 智能体agent收到指示后，就实际操作工具移动文件，然后把移动结果反馈给 AI。
  6. AI 收到移动完成的进度后，返回总结内容给智能体。
  7. 智能体收到 AI 传来的结果后，向用户报告结果。这样一步步推进，智能体agent全程协调，直到任务完成。

![](blob:https://my-blog-five-zeta-57.vercel.app/39332081-acd6-4736-8bf4-24820b7402b8)

* Agent：在AI、工具、用户间协调的程序
* Tool：提供给 AI 调用的函数。

## 4. function calling
* function calling：用于统一工具描述和AI调用工具时的返回格式规范。
* 每个工具都使用JSON对象进行定义，工具名（name）、功能说明（description）、参数（parameters）等字段被标准化。这些JSON对象不再置于系统提示词中，而是单独存放在一个特定字段
* 例如openai的function calling规范：
```json
{
    "name": "get_current_weather",
    "description": "Get the current weather in a given location",
    "parameters": {
        "type": "object",
        "properties": {
            "location": {
                "type": "string",
                "description": "The city and state, e.g. San Francisco, CA",
            },
            "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]},
        },
        "required": ["location"],
    },
}
```

## 5. MCP (Model Control Protocol)
* MCP (Model Control Protocol)：某些Agent Tool的功能具有通用性，若每个Agent都独立拷贝一份代码将导致重复且不优雅。MCP是一个通信协议，专门用于规范Agent与Tool服务之间的交互方式。
* 核心组件：
  * MCP Server：运行Agent Tool的服务器，负责处理Agent的请求并返回结果。
  * MCP Client：调用MCP Server上的Agent Tool服务的Agnet端。
* MCP Server提供的服务类型：
  * Tool：提供函数调用形式的服务
  * Resource：提供数据，类似文件读写服务
  * Prompt：为Agent提供预定义的Prompt模板
* MCP的部署方式：
  * 可与Agent运行在同一台机器上，通过Stdio进行通信
  * 也可部署在远程服务器上，通过HTTP进行通信

## 6. 大模型的上下文窗口
* 大模型的上下文窗口（Context Window）：就是模型在每一次对话中 ，能够记住和处理的信息总量的上限。

## 7. RAG (Retrieval-Augmented Generation)
* RAG简单说就是：**先从资料库找答案，再让AI基于找到的内容生成回答**的这个过程。RAG是目前最火的AI问答方案，很多企业知识助手、智能客服背后使用的技术都是RAG。
* 为什么直接喂文档给大模型不行？
  * 上下文窗口限制，可能读了后面忘前面；
  * 输入越多，推理成本越高；
  * 输入量大了影响推理速度。
* RAG如何解决这些痛点？
  * 只把**和问题相关的片段**发给模型，大大提升效率和准确性。

## 8. Agent完整协作流程
* Agent完整协作流程图如下所示：
  ![](blob:https://my-blog-five-zeta-57.vercel.app/2bf791b5-c9d8-4808-9e9c-81910867d3e7)
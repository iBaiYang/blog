---
layout: post
categories: AI
title: LangGraph学习
meta: LangGraph学习
---
* content
{:toc}

## 引言

LangGraph、LangChain、Deep Agent。

视频地址：https://www.bilibili.com/video/BV1urDZBMEao

## 链

### 消息

```python
from pprint import pprint
from langchain_core.messages import AIMessage,HumanMessage

messages=[AIMessage(content=f"所以你说你在研究FpV无人机？"，name="Model")]
messages.append(HumanMessage(content=f"是的，没错，"，name="Ronnie"))
messages.append(AIMessage(content=f"太好了，你想了解什么？"，name="Model"))
messages.append(HumanMessage(content=f"我想了解如何驾驶FPV无人机。"，name="Ronnie"))
for m in messages:
    m.pretty_print(m)
```

输出：
```
================================= Ai Message ==================================
Name:Model

所以你说你在研究FPV无人机？
================================= Human Message =================================
Name:Ronnie

是的，没错。
================================= Ai Message ==================================
Name:Model

太好了，你想了解什么？
================================= Human Message ==================================
Name:Ronnie

我想了解如何驾驶FPV无人机。
```


```python
import os, getpass

def _set_env(var:str):
    if not os.environ.get(var):
        os.environ[var] = getpass.getpass(f"{var}:")
        
_set_env("OPENAI_API_KEY")
```


```python
from langchain_openai import ChanOpenAI

llm = ChanOpenAI(model="gpt-4o")
result = llm.invoke(messages)
type(result)

# 输出
# langchain_core.messages.ai.AIMessage
```

result为：
```
AIMessage(content='驾驶 FPV（第一人称视角）无人机是一项既刺激又富有挑战性的活动。以下是一些基本步骤和建议，可以帮助你开始：\n\n1. **了解设备**:\n   - **无人机**: 确保你的无人机适合 FPV 飞行。入门级机型通常比较容易操控。\n   - **遥控器**: 熟悉遥控器上的每一个按键和功能。\n   - **FPV 眼镜或显示器**: 提供实时视觉体验，使你像飞在空中一样。\n\n2. **熟悉飞行法规**:\n   - 在开始飞行之前，了解并遵守当地的飞行法规和限制。\n   - 遵循“不在规定飞行空间外飞行”以及“不在禁飞区飞行”的规则。\n\n3. **模拟器训练**:\n   - 使用无人机飞行模拟器进行练习是学习操控技能的好方法，这样可以在不冒险损坏实际设备的情况下积累经验。\n\n4. **选择合适的场地**:\n   - 找一个开阔、无人且没有障碍物的地方进行练习。公园或无人开放地通常很适合新手练习。\n\n5. **基本飞行训练**:\n   - 开始时进行简单的起飞降落练习。\n   - 掌握前进、后退，以及左右侧飞。\n   - 学会平稳旋转（偏航）和倾斜（滚动），这对于操作无人机的机动性很重要。\n\n6. **初学者模式**:\n   - 许多无人机提供“初学者模式”或“稳定模式”，这些模式会限制飞行速度和角度，有助于新手更轻松地上手。\n\n7. **逐步进阶飞行技能**:\n   - 随着技能提高，尝试更复杂的飞行动作，比如翻滚、旋转或飞越障碍。\n\n8. **安全事项**:\n   - 总是确保无人机的电池充足并定期检查无人机的各个部件。\n   - 避免在人群、车辆上方或对自己和他人构成潜在危险的地方飞行。\n\n通过不断练习和对以上各方面的理解，你会逐渐探索出属于自己的 FPV 飞行路径和风格。希望你的 FPV 探索充满乐趣和收获！', additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 528, 'prompt_tokens': 65, 'total_tokens': 593, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-2024-08-06', 'system_fingerprint': 'fp_4691090a87', 'finish_reason': 'stop', 'logprobs': None}, id='run-1aa141424-d68f-45cc-b18a-0bbf6642a438-0', usage_metadata={'input_tokens': 65, 'output_tokens': 528, 'total_tokens': 593, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})
```

result.response_metadata 内容：
```
{
    'token_usage': {
        'completion_tokens': 528,
        'prompt_tokens': 65,
        'total_tokens': 593,
        'completion_tokens_details': {
            'accepted_prediction_tokens': 0,
            'audio_tokens': 0,
            'reasoning_tokens': 0,
            'rejected_prediction_tokens': 0
        },
        'prompt_tokens_details': {
            'audio_tokens': 0,
            'cached_tokens': 0
        }
    },
    'model_name': 'gpt-4o-2024-08-06',
    'system_fingerprint': 'fp_4691090a87',
    'finish_reason': 'stop',
    'logprobs': None
}
```

### 工具

有的 LLM提供商 支持工具调用，有的不支持，可以在这里查看：https://python.langchain.com/v0.1/docs/integrations/chat/

```python
from langchain.tools import tool

@tool
def multiply(a: int, b: int) -> int:
    """Multiply a and b.

    Args:
        a: first int
        b: second int
    """
    return a * b

llm_with_tools = llm.bind_tools([multiply])
```

`tool_call = llm_with_tools.invoke([HumanMessage(content=f"2 乘以 3 等于多少", name="Ronnie")])`

tool_call.tool_calls 内容：
```
[{'name': 'multiply',
  'args': {'a': 2, 'b': 3},
  'id': 'call_ytJKx5r5TtZHJH5nA3HL4y5i',
  'type': 'tool_call'}]
```

### model 状态

每次调返回一个新值：
```python
from typing_extensions import TypedDict
from langchain_core.messages import AnyMessage

class MessagesState(TypedDict):
    messages: list[AnyMessage]
```

Reducers 保留原消息：
```python
from typing import Annotated
from langgraph.graph.message import add_messages

class MessagesState(TypedDict):
    messages: Annotated[list[AnyMessage], add_messages]
```

为了简洁可以用：
```python
from langgraph.graph import MessagesState

class MessagesState(MessagesState):
    #添加除消息之外所需的任何键
    pass
```


```python
# 初始状态
initial_messages = [
    AIMessage(content="您好！我能为您做些什么？", name="Model"),
    HumanMessage(content="我正在寻找有关 fpv 无人机的信息.", name="Ronnie")
]

# 要添加的新消息
new_message = AIMessage(content="当然，我可以帮忙。你具体对什么感兴趣？", name="Model")

# 测试
add_messages(initial_messages , new_message)
```

输出结果：
```
[AIMessage(content='您好！我能为您做些什么？', additional_kwargs={}, response_metadata={}, name='Model', id='996dcc0a-f519-45a7-a754-78929edfeada'),
 HumanMessage(content='我正在寻找有关 fpv 无人机的信息.', additional_kwargs={}, response_metadata={}, name='Ronnie', id='e6de744a-3245-45d7-82b6-0a4749153cb9'),
 AIMessage(content='当然，我可以帮忙。你具体对什么感兴趣？', additional_kwargs={}, response_metadata={}, name='Model', id='5486dd63-b187-431d-9798-027a6d186cd8')]
 ```

### 图

```python
# --------------

@tool
def multiply(a: int, b: int) -> int:
    """Multiply a and b.

    Args:
        a: first int
        b: second int
    """
    return a * b

llm_with_tools = llm.bind_tools([multiply])

# --------------

from typing_extensions import TypedDict
from langchain_core.messages import AnyMessage

class MessagesState(TypedDict):
    messages: list[AnyMessage]

# 节点
def tool_calling_llm(state: MessagesState):
    return {"messages": [llm_with_tools.invoke(state["messages"])]}

# --------------

from IPython.display import Image, display
from langgraph.graph import StateGraph, START, END

# 建立图
builder = StateGraph(MessagesState)
builder.add_node("tool_calling_llm", tool_calling_llm)
builder.add_edge(START, "tool_calling_llm")
builder.add_edge("tool_calling_llm", END)
graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：
```python
messages = graph.invoke({"messages": HumanMessage(content="你好! ")})
for m in messages['messages']:
    m.pretty_print()
```

输出：
```
==================== Human Message ====================
你好!
====================== Ai Message ======================
你好! 有什么我可以帮助你的吗?
```

当 LLM 确定输入或任务需要该工具提供的功能时，它就会选择使用某个工具。

```python
messages = graph.invoke({"messages": HumanMessage(content="10乘9等于多少? ")})
for m in messages['messages']:
    m.pretty_print()
```

输出：
```
=========================== Human Message ===========================
10乘9等于多少?
============================ Ai Message =============================
Tool Calls:
  multiply (call_c3XlyLOCXR5ØZKgxt0ED1SnQ)
  Call ID: call_c3XlyLOCXR5ØZKgxt0ED1SnQ
  Args:
    a: 10
    b: 9
```


## 路由

准备：
```python
import os, getpass

def _set_env(var:str):
    if not os.environ.get(var):
        os.environ[var] = getpass.getpass(f"{var}:")
        
_set_env("OPENAI_API_KEY")
```

```python
from langchain_openai import ChanOpenAI
from langchain.tools import tool

llm = ChanOpenAI(model="gpt-4o")

@tool
def multiply(a: int, b: int) -> int:
    """Multiply a and b.

    Args:
        a: first int
        b: second int
    """
    return a * b

llm_with_tools = llm.bind_tools([multiply])

# --------------

from typing_extensions import TypedDict
from langchain_core.messages import AnyMessage

class MessagesState(TypedDict):
    messages: list[AnyMessage]
```

定义：
```python
from IPython.display import Image, display
from langgraph.graph import StateGraph, START, END
from langgraph.graph import MessagesState
from langgraph.prebuilt import ToolNode
from langgraph.prebuilt import tools_condition

# 节点
def tool_calling_llm(state: MessagesState):
    return {"messages": [llm_with_tools.invoke(state["messages"])]}

# 搭建图
builder = StateGraph(MessagesState)
builder.add_node("tool_calling_llm", tool_calling_llm)
builder.add_node("tools", ToolNode([multiply]))
builder.add_edge(START, "tool_calling_llm")
builder.add_edge("tools",END)
builder.add_conditional_edges(
    "tool_calling_llm",
    # 如果来自助手的最新消息（结果）是工具调用 -> tools_condition 路由到工具
    # 如果来自助手的最新消息（结果）不是工具调用 -> tools_condition 路由到 END
    tools_condition,
)
graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：
```python
from langchain_core.messages import HumanMessage

messages = [HumanMessage(content="请问10*9等于多少? ")]
messages = graph.invoke({"messages": messages})
for m in messages['messages']:
    m.pretty_print()
```

输出：
```
======================= Human Message =======================
请问10*9等于多少?
======================== Ai Message ========================
Tool Calls:
  multiply (call_NdG1xbwMQHy4KEjzYCIDIz8T)
  Call ID: call_NdG1xbwMQHy4KEjzYCIDIz8T
  Args:
    a: 10
    b: 9
======================== Tool Message ========================
Name: multiply

90
```


调用：
```python
from langchain_core.messages import HumanMessage

messages = [HumanMessage(content="你好")]
messages = graph.invoke({"messages": messages})
for m in messages['messages']:
    m.pretty_print()
```

输出：
```
======================= Human Message =======================
你好
====================== Ai Message ======================
你好! 有什么我可以帮助你的吗?
```


## ReAct agent

AI ReAct‌ 是一种让大型语言模型（LLM）在解决问题时‌边思考、边行动‌的智能体（Agent）推理范式，全称为 ‌Reasoning + Acting‌（推理 + 行动）‌‌

核心思想：
* ‌Thought（推理）‌：模型分析当前任务，规划下一步该做什么。
* ‌Action（行动）‌：调用外部工具（如搜索、计算器、API）执行具体操作。
* ‌Observation（观察）‌：接收工具返回的结果，并据此更新推理。


准备：
```python
import os, getpass

def _set_env(var:str):
    if not os.environ.get(var):
        os.environ[var] = getpass.getpass(f"{var}:")
        
_set_env("OPENAI_API_KEY")
```

LangSmith配置：
```
_set_env("LANGCHAIN_API_KEY")
os.environ["LANGCHAIN_TRACING_V2"]="true"
os.environ["LANGCHAIN_PROJECT"]="react-agent"
```

定义工具：
```python
from langchain_openai import ChanOpenAI

def multiply(a: int, b: int) -> int:
    """Multiply a and b.

    Args:
        a: first int
        b: second int
    """
    return a * b

def add(a: int, b: int) -> int:
    """Multiply a and b.

    Args:
        a: first int
        b: second int
    """
    return a + b

def divide(a: int, b: int) -> int:
    """Multiply a and b.

    Args:
        a: first int
        b: second int
    """
    return a / b

tools = [add,multiply,divide]
11m = ChanOpenAI(model="gpt-4o")

# 对于这个案例，我们将并行工具调用设置为false,因为数学运算通常是按顺序完成的，而这次我们有3个可以进行数学运算的工具
# OpenAI模型专门默认使用并行工具调用来提高效率，请参阅 https://python.langchain.com/docs/how_to/tool_calling_parallel/
11m_with_tools = 11m.bind_tools(tools, parallel_tool_calls=False)
```

创建LLM并设置提示词：
```python
from langgraph.graph import MessagesState
from langchain_core.messages import HumanMessage, SystemMessage

# 系统提示词
sys_msg = SystemMessage(content="您是一位乐于助人的助手，负责对一组输入执行算术运算。")

# 节点
def assistant(state: MessagesState):
    return {"messages": [llm_with_tools.invoke([sys_msg] + state["messages"])]}
```


```python
from langgraph.graph import START, StateGraph
from langgraph.prebuilt import tools_condition
from langgraph.prebuilt import ToolNode
from IPython.display import Image, display

# 初始化图
builder = StateGraph(MessagesState)

# 定义节点: 这些节点完成工作
builder.add_node("assistant", assistant)
builder.add_node("tools", ToolNode(tools))

# 定义边: 这些决定了控制流如何移动
builder.add_edge(START, "assistant")
builder.add_conditional_edges(
    "assistant",
    # 如果助手的最新消息(结果)是工具调用 -> tools_condition 路由到工具节点
    # 如果来自助手的最新消息(结果)不是工具调用 -> tools_condition 路由到终点
    tools_condition,
)

# 循环工具到LLM的边
builder.add_edge("tools", "assistant")

react_graph = builder.compile()
```

调用
```python
messages = [HumanMessage(content="将 3 和 4 相加。将输出乘以 2。再将输出除以 5")]
messages = react_graph.invoke({"messages": messages})

for m in messages['messages']:
    m.pretty_print()
```

输出：
```
======================= Human Message =======================
将 3 和 4 相加。将输出乘以 2。再将输出除以 5
======================== Ai Message ========================
Tool Calls:
  add (call_JVJDDXa3FwMo9tcscuTsqYxb)
  Call ID: call_JVJDDXa3FwMo9tcscuTsqYxb
  Args:
    a: 3
    b: 4
======================== Tool Message ========================
Name: add

7
======================== Ai Message ========================
Tool Calls:
  multiply (call_b8WlYZapENzSQpPYsaBxcGHv)
  Call ID: call_b8WlYZapENzSQpPYsaBxcGHv
  Args:
    a: 7
    b: 2
======================== Tool Message ========================
Name: multiply

14
======================== Ai Message ========================
Tool Calls:
  divide (call_234567890abcdef1234567890abcdef)
  Call ID: call_234567890abcdef1234567890abcdef
  Args:
    a: 14
    b: 5
======================== Tool Message ========================
Name: divide

2.8

======================== Ai Message ========================

结果是2.8。
```

## agent和记忆

```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()
react_graph_memory = builder.compile(checkpointer=memory)
```

调用：
```python
# 定义一个线程
config = {"configurable":{"thread_id":"1"}}

# 问题输入
messages = [HumanMessage(content:="3和4相加等于多少")]
messages = react_graph_memory.invoke({"messages":messages}, config)
for m in messages['messages']:
    m.pretty_print()
```

输出：
```
======================= Human Message =======================
请问3+4等于多少?
======================== Ai Message ========================
Tool Calls:
  add (call_NdG1xbwMQHy4KEjzYCIDIz8T)
  Call ID: call_NdG1xbwMQHy4KEjzYCIDIz8T
  Args:
    a: 3
    b: 4
======================== Tool Message ========================
Name: add

7
```

继续调用：
```python
messages = [HumanMessage(content="把结果再乘以3")]
messages = react_graph_memory.invoke({"messages":messages},config)
for m in messages['messages']:
    m.pretty_print()
```

输出：
```
======================= Human Message =======================
请问3+4等于多少?
======================== Ai Message ========================
Tool Calls:
  add (call_NdG1xbwMQHy4KEjzYCIDIz8T)
  Call ID: call_NdG1xbwMQHy4KEjzYCIDIz8T
  Args:
    a: 3
    b: 4
======================== Tool Message ========================
Name: add

7
======================= Human Message =======================
把结果再乘以3
======================== Ai Message ========================
Tool Calls:
  multiply (call_b8WlYZapENzSQpPYsaBxcGHv)
  Call ID: call_b8WlYZapENzSQpPYsaBxcGHv
  Args:
    a: 7
    b: 3
======================== Tool Message ========================
Name: multiply

21
======================== Ai Message ========================

将结果7乘以3等于21。
```

## 图状态

上面可以：
* act-让模型调用特定工具
* observe-将工具输出传回模型
* reason-让模型推理工具输出以决定下一步做什么（例如，调用另一个工具或直接响应）
* persist state-使用内存中的检查点来支持带有中断的长时间对话

### TypedDict

TypedDict 是第一种定义结构化数据的方法。

```python
from typing_extensions import TypedDict

class TypedDictstate(TypedDict):
    foo:str
    bar:str
```

加值约束：
```python
from typing import Literal

class TypedDictstate(TypedDict):
    name:str
    mood:Literal["高兴","悲伤"]
```

例一：
```python
import random
from IPython.display import Image, display
from langgraph.graph import StateGraph, START, END

def node_1(state):
    print("---Node 1---")
    return {"name": state['name'] + " 感到 ... "}

def node_2(state):
    print("---Node 2---")
    return {"mood": "开心"}

def node_3(state):
    print("---Node 3---")
    return {"mood": "悲伤"}

def decide_mood(state) -> Literal["node_2", "node_3"]:
    # 在这里，我们在节点 2、3 之间进行 50/50 的分割
    if random.random() < 0.5:
        # 50% 的概率，我们返回节点 2
        return "node_2"

    # 50% 的概率，我们返回节点 3
    return "node_3"


# 建立图
builder = StateGraph(TypedDictState)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)

# 逻辑
builder.add_edge(START, "node_1")
builder.add_conditional_edges("node_1", decide_mood)
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

```
调用：graph.invoke({"name": "Roonie"})

输出：
---Node1---
---Node3---
{'name':'Roonie感到...', 'mood':'悲伤'}
```

### Dataclass

Dataclass 是第二种定义结构化数据的方法。

```python
from dataclasses import dataclass
from typing import Literal

@dataclass
class DataclassState:
    name: str
    mood: Literal["开心","悲伤"]
```


```python
def node_1(state):
    print("---Node 1---")
    return {"name": state.name + " is ... "}

def node_2(state):
    print("---Node 2---")
    return {"mood": "开心"}

def node_3(state):
    print("---Node 3---")
    return {"mood": "悲伤"}

def decide_mood(state) -> Literal["node_2", "node_3"]:
    # 在这里，我们在节点 2、3 之间进行 50/50 的分割
    if random.random() < 0.5:
        # 50% 的概率，我们返回节点 2
        return "node_2"

    # 50% 的概率，我们返回节点 3
    return "node_3"


# 建立图
builder = StateGraph(TypedDictState)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)

# 逻辑
builder.add_edge(START, "node_1")
builder.add_conditional_edges("node_1", decide_mood)
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

```
graph.invoke(DataclassState(name="Roonie",mood="悲伤"))

---Node1---
---Node3---
{"name":"Roonie is..", "mood":"悲伤"}
```

### Pydantic

如前所述，TypedDict和dataclasses提供类型提示，但它们不会在运行时强制执行类型，意味着您可能会分配无效值而不会。发错误！

例如，即使我们的类型提示指定了`mood:list[Lit高兴"ha悲伤"，"sad"]`,我们也可以将mood失望为mad。

`dataclass_instance Dataclassstate(name="Ronnie",mood="失望")`

Pydantic 是一个使用Python类型注释的数据验证和设置管理库。由于其验证功能，它特别适合在LangGraph中定义状态模式。

Pydantic可以在运行时执行验证以检查数据是否符合指定的类型和约束。

```python
from pydantic import BaseModel, field_validator, ValidationError

class PydanticState(BaseModel):
    name: str
    mood: str # "高兴" or "悲伤"

    @field_validator('mood')
    @classmethod
    def validate_mood(cls, value):
        # 确保mood是"快乐"或"悲伤"
        if value not in ["高兴", "悲伤"]:
            raise ValueError("mood 值必须是'高兴' 或 '悲伤'")
        return value

try:
    state = PydanticState(name="Ronnie", mood="失望")
except ValidationError as e:
    print("Validation Error:", e)
```

```
Validation Error: 1 validation error for PydanticState
mood
  Value error, mood 值必须是'高兴' 或 '悲伤' [type=value_error, input_value='失望', input_type=str]
    For further information visit https://errors.pydantic.dev/2.9/v/value_error
···

我们可以在Langraph图中无缝使用"PydanticState"。

```python
# 建立图
builder = StateGraph(PydanticState)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)

# 逻辑
builder.add_edge(START, "node_1")
builder.add_conditional_edges("node_1", decide_mood)
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()

display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：`graph.invoke(PydanticState(name="Ronnie", mood="失望"))`

```
ValidationError                           Traceback (most recent call last)
Cell In[15], line 1
----> 1 graph.invoke(PydanticState(name="Ronnie",mood="失望"))

File ~\AppData\Local\Programs\Python\Python311\Lib\site-packages\pydantic\main.py:212, in BaseModel.__init__(self, **data)
    210 # `__tracebackhide__` tells pytest and some other tools to omit this function from tracebacks
    211 __tracebackhide__ = True
--> 212 validated_self = self.__pydantic_validator__.validate_python(data, self_instance=self)
    213 if self is not validated_self:
    214     warnings.warn(
    215         'A custom validator is returning a value other than `self`.\n'
    216         "Returning anything other than `self` from a top level model validator isn't supported when validating via `__init__`.\n"
    217         'See the `model_validator` docs (https://docs.pydantic.dev/latest/concepts/validators/#model-validators) for more details.',
    218         category=None,
    219     )

ValidationError: 1 validation error for PydanticState
mood
  Value error, mood 值必须是'高兴' 或 '悲伤' [type=value_error, input_value='失望', input_type=str]
    For further information visit https://errors.pydantic.dev/2.9/v/value_error
```

## 图状态更新

```python
from typing_extensions import TypedDict
from IPython.display import Image, display
from langgraph.graph import StateGraph, START, END

class State(TypedDict):
    foo: int

def node_1(state):
    print("---Node 1---")
    return {"foo": state['foo'] + 1}

# 建立图
builder = StateGraph(State)
builder.add_node("node_1", node_1)

# 逻辑
builder.add_edge(START, "node_1")
builder.add_edge("node_1", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：`graph.invoke({"foo": 1})`

```
---Node 1---
{"foo": 2}
```

三个节点：
```python
class State(TypedDict):
    foo: int

def node_1(state):
    print("---Node 1---")
    return {"foo": state['foo'] + 1}

def node_2(state):
    print("---Node 2---")
    return {"foo": state['foo'] + 1}

def node_3(state):
    print("---Node 3---")
    return {"foo": state['foo'] + 1}

# 建立图
builder = StateGraph(State)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)

# 逻辑
builder.add_edge(START, "node_1")
builder.add_edge("node_1", "node_2")
builder.add_edge("node_1", "node_3")
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：
```
from langgraph.errors import InvalidUpdateError

graph.invoke({"foo": 1})
```

```
---Node 1---
---Node 2---
---Node 3---
```

报错：
```
InvalidUpdateError: At key 'foo': Can receive only one value per step. Use an Annotated key to handle multiple values.
For troubleshooting, visit: https://python.langchain.com/docs/troubleshooting/errors/INVALID_CONCURRENT_GRAPH_UPDATE
```

foo 只能修改一次。

### Reducers

Reducers 为我们提供了解决这个问题的通用方法。它们指定如何执行更新。

我们可以使用 Annotated 类型来指定 Reducer 函数。

例如，在这种情况下，让我们附加从每个节点返回的值，而不是覆盖它们。我们只需要一个可以执行此操作的 Reducer：`operator.add` 是 Python 内置运算符模块中的一个函数。当 `operator.add` 应用于列表时，它会执行列表连接。

```python
from operator import add
from typing import Annotated

class State(TypedDict):
    foo: Annotated[list[int], add]

def node_1(state):
    print("---Node 1---")
    return {"foo": [state['foo'][0] + 1]}

# 建立图
builder = StateGraph(State)
builder.add_node("node_1", node_1)

# 逻辑
builder.add_edge(START, "node_1")
builder.add_edge("node_1", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：`graph.invoke({"foo": 1})`

```
---Node 1---
{"foo": [1, 2]}
```

三个节点：
```python
def node_1(state):
    print("---Node 1---")
    return {"foo": [state['foo'][-1] + 1]}

def node_2(state):
    print("---Node 2---")
    return {"foo": [state['foo'][-1] + 1]}

def node_3(state):
    print("---Node 3---")
    return {"foo": [state['foo'][-1] + 1]}

builder = StateGraph(State)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)
builder.add_node("node_3", node_3)

builder.add_edge(START, "node_1")
builder.add_edge("node_1", "node_2")
builder.add_edge("node_1", "node_3")
builder.add_edge("node_2", END)
builder.add_edge("node_3", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```


调用：
```
graph.invoke({"foo": 1})
```

```
---Node 1---
---Node 2---
---Node 3---
{"foo": [1, 2, 3, 3]}
```

调用：
```
graph.invoke({"foo": None})
```

报错：
```
TypeError: can only concatenate list (not "NoneType") to list
```

### 自定义 Reducer

为了解决此类情况，我们还可以定义自定义 Reducer。
例如，让我们定义自定义 Reducer 逻辑来组合列表并处理其中一个或两个输入可能为“None”的情况。

```python
def reduce_list(left: list | None, right: list | None) -> list:
    """安全地组合两个列表，处理其中一个或两个输入可能为 None 的情况。

        参数:
        左 (列表 | None) : 要组合的第一个列表，或 None。
        右 (列表 | None) : 要组合的第二个列表，或 None。

        返回:
        列表: 包含两个输入列表中所有元素的新列表。
        如果输入为 None，则将其视为空列表。
    """
    # 如果左列表为 None，返回右列表
    if not left:
        left = []
    # 如果右列表为 None，返回左列表
    if not right:
        right = []
    return left + right


class DefaultState(TypedDict):
    foo: Annotated[list[int], add]

class CustomReducerState(TypedDict):
    foo: Annotated[list[int], reduce_list]
```

在 node_1 中, 我们附加值 2。

使用原始 Reducer：
```python
def node_1(state):
    print("---Node 1---")
    return {"foo": [2]}

builder = StateGraph(DefaultState)
builder.add_node("node_1", node_1)

builder.add_edge(START, "node_1")
builder.add_edge("node_1", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))

try:
    print(graph.invoke({"foo": None}))
except InvalidUpdateError:
    print(f"TypeError occurred: {e}") 
```

报错：`TypeError occurred: can only concatenate list (not "NoneType") to list`

现在，尝试使用我们的自定义 Reducer。我们可以看到没有抛出任何错误。

```python
def node_1(state):
    print("---Node 1---")
    return {"foo": [2]}

builder = StateGraph(CustomReducerState)
builder.add_node("node_1", node_1)

builder.add_edge(START, "node_1")
builder.add_edge("node_1", END)

graph = builder.compile()

display(Image(graph.get_graph().draw_mermaid_png()))

try:
    print(graph.invoke({"foo": None}))
except InvalidUpdateError:
    print(f"TypeError occurred: {e}") 
```

无报错。

## 多重状态

上面介绍了状态模式和状态更新 reducer。

通常，所有图形节点都与单个模式进行通信。此外，这个单个模式包含图形的输入和输出键/通道。

但是，在某些情况下，我们可能希望对此有更多的控制：
* 内部节点可能会传递图形的 输入 / 输出 中不需要的信息。
* 我们可能还想为图使用不同的 输入 / 输出 模式。例如，输出可能只包含一个相关的输出键。

我们将讨论几种使用多个模式自定义图形的方法。


### 私有状态

这对于图的中间工作逻辑所需的任何内容都很有用，但与整体图的输入或输出无关。

我们将定义一个OverallState和—个PrivateState。

node_2使用PrivateState作为输入，但写入OverallState。

```python
from typing_extensions import TypedDict
from IPython.display import Image, display
from langgraph.graph import StateGraph, START, END

class OverallState(TypedDict):
    foo: int

class PrivateState(TypedDict):
    baz: int

def node_1(state: OverallState) -> PrivateState:
    print("---Node 1---")
    return {"baz": state['foo'] + 1}

def node_2(state: PrivateState) -> OverallState:
    print("---Node 2---")
    return {"foo": state['baz'] + 1}

#建立公有状态的图
builder = StateGraph(OverallState)
builder.add_node("node_1", node_1)
builder.add_node("node_2", node_2)

builder.add_edge(START, "node_1")
builder.add_edge("node_1", "node_2")
builder.add_edge("node_2", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：
```
graph.invoke({"foo": 2})
```

```
---Node 1---
---Node 2---
{"foo": 4}
```

baz仅包含在PrivateState中。node_2使用PrivateState作为输入，但写入OverallState。因此，我们可以看到baz被排除在图形输出之外，因为它不在Overallstate中。

### 输入输出模式

默认情况下，StateGraph采用单一模式，并且所有节点都应与该模式进行通信。但是，也可以为图形定义显式输入和输出模式。通常，在这些情况下，我们定义一个”内部”模式，其中包含与图形操作相关的所有键。但是，我们使用特定的输入和输出模式来约束输入和输出。

首先，让我们使用单一模式运行图形。
```python
class OverallState(TypedDict):
    question: str
    answer: str
    notes: str

def thinking_node(state: OverallState):
    return {"answer": "再见", "notes": "... 他的名字叫Ronnie"}

def answer_node(state: OverallState):
    return {"answer": "再见Ronnie"}

graph = StateGraph(OverallState)
graph.add_node("answer_node", answer_node)
graph.add_node("thinking_node", thinking_node)
graph.add_edge(START, "thinking_node")
graph.add_edge("thinking_node", "answer_node")
graph.add_edge("answer_node", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：`graph.invoke(("question":"你好"})`

输出：`{'question':'你好', 'answer': '再见Ronnie','notes': "... 他的名字叫Ronnie'}`

现在，让我们在图中使用特定的“输入”和“输出”模式。在这里，“输入”/“输出”模式图表的输入和输出上允许的键执行过滤。
此外，我们可以使用类型提示"state：InputState”来指定每个节点的输入模式。当图表使用多个模式时，这一点很重要。

我们使用下面的类型提示来例如显示“answer_node”的输出将被过滤到”OutputState”。
```python
class InputState(TypedDict):
    question: str

class OutputState(TypedDict):
    answer: str

class OverallState(TypedDict):
    question: str
    answer: str
    notes: str

def thinking_node(state: InputState):
    return {"answer": "再见", "notes": "... 他的名字叫Ronnie"}

def answer_node(state: OverallState) -> OutputState:
    return {"answer": "再见Ronnie"}

graph = StateGraph(OverallState, input=InputState, output=OutputState)
graph.add_node("answer_node", answer_node)
graph.add_node("thinking_node", thinking_node)
graph.add_edge(START, "thinking_node")
graph.add_edge("thinking_node", "answer_node")
graph.add_edge("answer_node", END)

graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：`graph.invoke(("question":"hi"})`

输出：`{'answer': '再见Ronnie'}`


## 过滤和修剪信息

```python
from pprint import pprint
from langchain_core.messages import AIMessage,HumanMessage

messages = [AIMessage(f"所以你说你在研究神经网络？", name="Ronnie")]
messages.append(HumanMessage(f"是的，我知道神经网络。但我还应该了解哪些其他东西呢?", name="Simon"))
for m in messages:
    m.pretty_print()
```

信息：
```
======================== Ai Message ========================
Name: Ronnie

所以你说你在研究神经网络？
======================== Human Message ========================
Name：Simon

是的，我知道神经网络。但我还应该了解哪些其他东西呢？
```

调用：
```
from langchain_openai import ChanOpenAI

1lm = ChanOpenAI(model="gpt-4o")
graph.invoke(messages)
```

使用”MessagesState”在简单的图表中运行我们的聊天模型。
```python
from IPython.display import Image, display
from langgraph.graph import MessagesState
from langgraph.graph import StateGraph,START, END

# LLM节点
def chat_model_node(state: MessagesState):
    return {"messages": 1lm.invoke(state["messages"])}

# 建立图
builder= StateGraph(MessagesState)
builder.add_node("chat_model",chat_model_node)
builder.add_edge(START,"chat_model")
builder.add_edge("chat_model", END)
graph = builder.compile()

# 显示图结构
display(Image(graph.get_graph().draw_mermaid_png()))
```

调用：
```python
output = graph.invoke({'messages': messages})
for m in output['messages']:
    m.pretty_print()
```

输出：
```
======================== Ai Message ========================
Name: Ronnie

所以你说你在研究神经网络？
======================== Human Message ========================
Name：Simon

是的，我知道神经网络。但我还应该了解哪些其他东西呢？

======================== Ai Message ========================
了解神经网络后，继续研究其他相关领域会为你提供更广泛、深刻的人工智能和机器学习理解。以下是一些你可能要了解的领域：
1. **机器学习基础**：了解监督学习、无监督学习和强化学习，以及常用的算法如线性回归、决策树和集成方法等。
2. **深度学习**：深入学习卷积神经网络（CNNs）、循环神经网络（RNNs）、长短时记忆网络（LSTMs）以及变分自编码器（VAEs）和生成对抗网络（GANs）等。
3. **数据预处理与特征工程**：学习如何处理和清洗数据，进行特征选择和特征提取。
4. **优化算法**：例如梯度下降（及其变种Adam，RMSprop等），帮助提升模型的性能。
5. **概率和统计学**：深入理解概率分布、统计推断以及贝叶斯推断等概念。
6. **大规模数据处理**：学习如何使用工具和框架如Hadoop、Spark来处理大规模数据集。
7. **自然语言处理（NLP）**：研究文本分析、情感分析、语言翻译等方面的技术。
8. **计算机视觉**：了解图像分类、目标检测和图像分割等技术。
9. **强化学习**：探索如何进行策略优化，发掘基于奖励机制的学习方法。
10. **人工智能伦理**：了解AI在隐私、安全、公平和透明度等方面的社会影响。
通过熟悉这些领域，你可以构建一个坚实的基础，并为应用这些技术解决实际问题做好准备。不同的应用领域也可能有特定的挑战和雲求研空时可以根据你的兴趣和目标进行深入。
```









<br/><br/><br/><br/><br/>
## 参考资料 













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
from langchain_openai import ChatopenAI

11m = ChatopenAI(model="gpt-40")
result = 11m.invoke(messages)
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


```
# 初始状态
initial_messages = [AIMessage(content="您好！我能为您做些什么？", name="Model"),
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
from langchain_openai import ChatopenAI

11m = ChatopenAI(model="gpt-40")

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


<br/><br/><br/><br/><br/>
## 参考资料 













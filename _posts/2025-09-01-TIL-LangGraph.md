---
layout: post
title: "9월 1일 월요일 TIL(LangGraph)"
date: 2025-09-01 18:00:00 +0900
categories: July_week10
---

# 9월 1일 월요일 TIL 작성

## 1. 학습 주제
- toolCalling 복습
- Executor
- LangGraph

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### ToolCalling 과정

llm에 bind_tools 라는 기능을 통해서 여러 도구들을 붙힐 수 있다.

LLM에 질문을 했을 때, 만약 tool이 필요하다고 llm이 판단하면, content로 대답을 돌려주지 않고, tool_calls를 통해서 어떤 툴이 필요할지, 툴에 어떤 질문을 해야할지를 리턴한다.

그럼 개발자는 tool_calls를 바탕으로 실제 tool을 실행하고 tool이 가져온 결과값을 받는다.

마지막으로 다시 LLM에 사용자의 질문과 placeholder를 통해서 어떤 툴을 호출했었고, 어떤 결과 값이 툴을 통해서 리턴 되었는지를 Template을 통해서 넣어주고 답변을 받는다.

### Config 사용 이유

```python
#여기서 config를 왜 쓰는 걸까?

@chain
def search_animal_chain(user_input:str, config : RunnableConfig):
    input_ = {"user_input" : user_input}
    ai_msg = llm_chain.invoke(input_, config=config)

    tool_msgs = []
    for tool_call in ai_msg.tool_calls:
        if tool_call["name"] == "search_animal":
            tool_message = search_animal.invoke(tool_call, config=config)
            tool_msgs.append(tool_message)

    return llm_chain.invoke({**input_, "messages" : [ai_msg, *tool_msgs]}, config=config)

response = search_animal_chain.invoke("코끼리에 대해서 알려줘.")

print(response.content)
```

`config` 매개변수는 **실행 컨텍스트를 전달**하기 위해 사용됩니다. 이를 통해 체인(chain) 또는 개별 Runnable이 실행될 때의 설정, 예를 들어 콜백(callbacks), 태그(tags), 실행 추적 정보(tracing) 등을 공유할 수 있습니다.

### `config`를 사용하는 이유

`config`는 주로 다음과 같은 목적으로 사용됩니다.

- **추적 (Tracing)**: `langsmith`와 같은 도구를 사용해 체인 실행 과정을 추적할 때 유용합니다. `config`를 통해 실행 정보가 하위 호출에 자동으로 전달되어 전체 실행 흐름을 시각화할 수 있습니다.
- **콜백 (Callbacks)**: 실행 시작, 끝, 오류 등 특정 이벤트에 반응하는 콜백을 설정할 때 사용됩니다. 예를 들어, 토큰 사용량을 기록하거나 실행 시간을 측정하는 콜백을 `config`에 포함하여 체인의 모든 단계에 적용할 수 있습니다.
- **태그 (Tags)**: 특정 실행에 태그를 붙여 분류하고 관리할 수 있습니다. 예를 들어, `{"tags": ["production", "experiment_A"]}`와 같이 태그를 설정하여 특정 실행을 그룹화할 수 있습니다.

제공된 코드에서 `config`가 `llm_chain.invoke()`와 `search_animal.invoke()`에 모두 전달되는 이유는, **최상위 체인(`search_animal_chain`)의 실행 컨텍스트가 하위 Runnable(LLM 호출 및 도구 실행)에도 동일하게 적용되도록** 하기 위함입니다. 이를 통해 모든 실행 단계가 동일한 콜백 및 추적 설정 아래에서 동작하게 됩니다. `config`를 사용하지 않으면, `search_animal_chain`의 상위 설정이 하위 실행에는 반영되지 않아 추적이나 콜백이 누락될 수 있습니다.

### Executor

```python
from langchain.agents import AgentExecutor, create_tool_calling_agent

tools = [search_web, search_menu, search_wine]
agent = create_tool_calling_agent(llm, tools, agent_prompt) 
# llm과 내가 정의한 tool과 agent_prompt만 있으면 위에 내가 만든 chain을 직접 만들 필요가 없다?

agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)
```

네, 맞습니다. `create_tool_calling_agent`와 `AgentExecutor`를 사용하면 직접 체인(chain)을 만들 필요 없이 복잡한 도구 호출 로직을 쉽게 구현할 수 있습니다.

#### `create_tool_calling_agent`의 역할

`create_tool_calling_agent` 함수는 내부적으로 LLM, 도구, 그리고 프롬프트를 결합하여 **`Runnable`** 객체를 생성해 줍니다. 이 `Runnable` 객체는 사용자의 입력을 받아서 LLM이 어떤 도구를 호출할지 결정하는 역할을 합니다. 개발자가 직접 프롬프트를 파이프(`|`)로 연결하고, 도구 호출 결과를 받아 다시 LLM에 전달하는 복잡한 로직을 작성할 필요가 없어집니다.


#### `AgentExecutor`의 역할

`AgentExecutor`는 이 `Runnable` 객체(에이전트)를 실행하는 핵심 역할을 담당합니다. `AgentExecutor`는 사용자의 질문을 받으면:

1. **계획 수립**: 에이전트(`create_tool_calling_agent`로 생성된 `Runnable`)를 실행하여 LLM이 **어떤 도구를 호출할지** 결정하게 합니다.
2. **도구 실행**: 결정된 도구를 실제로 실행합니다.
3. **결과 재전달**: 도구 실행 결과를 다시 LLM에 전달하여 최종 답변을 생성하도록 합니다.

이러한 **계획 수립 → 도구 실행 → 결과 재전달**의 과정을 자동으로 반복하며 최종 답변을 찾아내므로, 직접 체인을 구성하는 수고를 덜 수 있습니다.

따라서 `create_tool_calling_agent`와 `AgentExecutor`는 복잡한 로직을 추상화하여, 개발자가 **"무엇을 할 것인가"** (어떤 도구를 제공하고, 프롬프트를 어떻게 작성할 것인가)에만 집중할 수 있게 도와줍니다.

### Langgraph

랭 체인은 결국은 직선이다. 따라서 분기별로 나뉘어진거나, 하는 등 다양성이 부족하고 문제 해결하는데 방식이 제한될 수 있다. 복잡한 문제를 풀기가 어렵다.

**목적** : 복잡한 작업 흐름을 상태와 전이로 모델링하여, **유연하고 제어가능한 시스템을 구축**

- 상태(state) : 앱의 현재 스냅샷을 나타내는 공유 데이터 구조
- 노드(node) : Agent의 로직을 인코딩하는 python 함수, 일반적으로 상태를 변경
- 엣지(edge) : 현재 state를 기반으로 다음에 실행할 node를 결정하는 함수.

```python
from langgraph.graph import StateGraph, START, END

builder = StateGraph(GreetState)

builder.add_node("normalize_name", normalize_name)
builder.add_node("making_greeting", making_greeting)

builder.add_edge(START, "normalize_name")
builder.add_edge("normalize_name", "making_greeting")
builder.add_edge("making_greeting", END)

graph = builder.compile()
```

LangGraph 는 다음과 같이 node를 만들고 edge를 통해서 노드 사이를 이어주는 방식으로 구현한다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### @Param을 Interface에서 사용하는 이유

`@Param`을 사용하는 이유는 **매개변수(parameter)가 2개 이상이거나, Mybatis XML 매퍼 파일에서 명시적인 이름으로 참조해야 할 때**입니다. 그렇지 않은 경우에는 생략합니다.

#### `@Param` 사용 기준

Mybatis는 인터페이스의 매개변수를 처리하는 규칙을 가지고 있습니다.

- **매개변수가 하나일 때 (단일 매개변수)**: `@Param`을 사용하지 않아도 Mybatis는 해당 매개변수를 인식하고 처리합니다. 이 경우 Mybatis XML 매퍼 파일에서 `#{값}` 또는 `#{파라미터명}`으로 참조할 수 있습니다. 예를 들어 `selectArticle(int boardId)`의 경우, XML에서 `#{boardId}`로 바로 사용 가능합니다.
- **매개변수가 두 개 이상일 때**: `@Param`을 사용하지 않으면 Mybatis는 매개변수들을 `param1`, `param2`와 같은 기본 이름으로 인식합니다. 이 때문에 매퍼 XML에서 `#{param1}`이나 `#{param2}`처럼 접근해야 하는데, 이는 가독성을 떨어뜨리고 유지보수를 어렵게 만듭니다. `@Param`을 사용하면 매개변수에 명시적인 이름을 부여하여 XML 매퍼 파일에서 그 이름(`@Param("이름")`)으로 바로 참조할 수 있습니다.

**예시**

Java

```java
// @Param 사용
List<Board> selectArticleListByCategory(@Param("categoryId") int categoryId, @Param("start") int start, @Param("end") int end);
```

위 코드의 경우, Mybatis XML 매퍼 파일에서 `#{categoryId}`, `#{start}`, `#{end}`와 같이 명확한 이름으로 접근할 수 있습니다.

```java
// @Param 미사용
Board selectArticle(int boardId);
```

위 코드의 경우, 매개변수가 하나이므로 XML에서 `#{boardId}`로 바로 접근할 수 있습니다.

## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
langGraph를 사용하면 LangChain 처럼 직선적인 파이프라인 만 만드는 것이 아니라, 더 복잡한 파이프라인을 만들 수 있는 것 같아서 활용성이 더 높을 것 같다고 생각했다.
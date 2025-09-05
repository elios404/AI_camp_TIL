---
layout: post
title: "9월 5일 금요일 TIL(LangGraph-Self Rag-Router)"
date: 2025-09-05 18:00:00 +0900
categories: July_week10
---

# 9월 5일 금요일 TIL 작성

## 1. 학습 주제
- LangGraph
  - Messages
  - 자료 전달 방식
- LangGraph self RAG
  - 분기 선택
- LangGraph Router


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### Messages의 작동 방식(아직 명확히 해결 못함)

```python
from langgraph.graph import MessagesState, StateGraph, START, END
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage

class GraphState(MessagesState):
    # messages 필드 기본 포함 (MessagesState)를 넣었기에
    pass
    
def call_mode(state: GraphState) -> GraphState:
    system_message = SystemMessage(content=system_prompt)
    messages = [system_message] + state['messages']
    response = llm_with_tools.invoke(messages)

    return {"messages" : [response]}
    # return {"messages": state['messages'] + [response]} # 간단하게 하기 위해 위에것 사용, 근데 이게 더 정확한 듯
```

마지막 리턴 하는 부분에 대해서 Messages를 더하는 방식으로 해야하는게 아닌가 싶었는데

아마도 랭그래프의 작동 방식에서 리스트인 경우 위와 같이 리턴해도 계속 더해지는 방식으로 작동하는 것 같다.

GPT는 아래 주석처럼 하는게 맞다고 하는데, 실제 위와 같이 작성해도 기존의 내용까지 전부 포함해서 들어가있다. 이 부분은 명확하게 어떻게 작동하는지 잘 모르겠다..

### uv-tax 질문

```python
from langchain import hub

generate_prompt = hub.pull('rlm/rag-prompt')

def generate(state: AgentState) -> AgentState:
    """ 
    주어진 state를 기반으로 RAG 체인을 사용하여 응답을 생성합니다.
    Args:
        state (AgentState): 사용자의 질문과 문맥을 포함한 에이전트의 현재 state
    Returns:
        AgentState: 생성된 응답을 포함하는 state를 반환합니다.        
    """
    context = state['context'] #이건 List[Document] 인데?
    query = state['query']

    rag_chain = generate_prompt | llm

    response = rag_chain.invoke({'question': query, 'context': context})

    return {'answer': response}
```

이때 우리 변수 context는 List[Document]인데 프롬프트에 그냥 넣는다. 원래는 문자열로 넣어주어야 하는게 맞다.

하지만, LangChain으로 이렇게 넣어주는 경우에는 Document 리스트로 넣어줄 경우 체인이 알아서 풀어서 문자열 형태로 변환하여 프롬프트에 문자열로 넣는다.


### 분기에서 분기로 이동할 때

```python
def check_helpfulness(state: AgentState) -> AgentState:
    """ 
    유용성을 확인하는 자리 표시자 함수입니다.
    """
    return state
    
from langgraph.graph import START, END

builder.add_edge(START, 'retrieve')
builder.add_conditional_edges(
    'retrieve',
    check_doc_relevence,
    {
        "relevant" : "generate",
        # "irrelevant" : "rewrite"
        "irrelevant" : END # 그냥 끝내는 코드
    }
    )
builder.add_edge('rewrite', 'retrieve')
builder.add_conditional_edges(
    'generate',
    check_hallucination,
    {
        'hallucinated' : 'generate',
        'not hallucinated' : "check_helpfulness"
    }
)
builder.add_conditional_edges(
    'check_helpfulness',
    check_helpfulness_grader,
    {
        "helpful" : END,
        "unhelpful" : "rewrite"
    }
)
```

작동 방식으로만 보면 분기에서 다음 분기로 바로 이동한다. 그러나 기본적으로 분기는 노드에서 시작되기에 이 경우 그냥 빈 노드를 만드는 것이 일반적으로 더 좋다.

### 여러 분기를 선택하는 라우터 만들기

```python
from langchain_core.prompts import ChatPromptTemplate
from pydantic import BaseModel, Field
from typing import Literal

class Route(BaseModel):
    target: Literal["llm", "vector_store", "web_search"] = Field(
        description="The target for the query to answer"
    )

router_system_prompt = """
You are an expert at routing a user's question to 'vector_store', 'llm', or 'web_search'.
'vector_store' contains information about income tax up to December 2024.
if you think the question is simple enough use 'llm'
if you think you need to search the web to answer the question use 'web_search'
"""

router_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", router_system_prompt),
        ("human", "{query}")
    ]
)

router_llm = ChatOpenAI(model = "gpt-4o-mini")
structed_router_llm = router_llm.with_structured_output(Route)

def router(state: AgentState) -> Literal['vector_store', 'llm', 'web_search']:
    """
    사용자의 질문을 기반으로 적절한 경로를 결정합니다.
    
    Args:
        state (AgentState): 사용자의 질문을 포함한 에이전트의 현재 state.
        
    Returns:
        Literal['vector_store', 'llm', 'web_search']: 
        질문을 처리하기 위한 적절한 경로를 나타내는 문자열을 반환한다.      
    """
    query = state['query']
    
    router_chain = router_prompt | structed_router_llm
    
    route = router_chain.invoke({"query": query})
    
    print(f"라우터 결과 : {route.target}")
    return route.target
```

`BaseModel` 과 `router_llm.with_structured_output(Route)` + 프롬프트를 통해서 LLM의 답변을 특정 클래스 형태로 받아올 수 있다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
점점 다양하고 실제 활용 가능한 RAG, langChain, langGraph를 배우고 있는 것 같아서 매우 흥미롭다. 
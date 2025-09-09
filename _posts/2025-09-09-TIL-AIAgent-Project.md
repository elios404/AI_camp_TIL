---
layout: post
title: "9월 9일 화요일 TIL(AI Agent Project)"
date: 2025-09-09 18:00:00 +0900
categories: July_week11
---

# 9월 일 요일 TIL 작성

## 1. 학습 주제
- AI 에이전트 프로젝트

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
```python
# %%
from dotenv import load_dotenv

load_dotenv()

# %%
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings

embeddings_model = OpenAIEmbeddings(model = "text-embedding-3-large")

#데이터 로딩
vector_store = Chroma(
    collection_name='chroma-req',
    embedding_function=embeddings_model,
    persist_directory='./chroma-req'
)

retriever = vector_store.as_retriever(search_kwargs={'k':2})

# %%
from typing_extensions import List, TypedDict
from langchain_core.documents import Document
from langgraph.graph import StateGraph, START, END

class ReqState(TypedDict):
    req_actor: List[str]
    req_names: List[str]
    req_purpose: List[str]
    req_importance: List[str]
    req_level: List[str]
    req_function: List[str]
    req_process: List[str]
    req_screen: List[str]
    req_security: List[str]
    req_perform: List[str]
    req_data: List[str] 

# %%
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model = "gpt-4o")



# %%
from langchain.tools import DuckDuckGoSearchRun
from langgraph.prebuilt import ToolNode
from langchain_core.tools import tool

search_tool = DuckDuckGoSearchRun()
tools = [search_tool]
tool_node = ToolNode(tools)

# %%
from pydantic import BaseModel, Field
from langchain_core.prompts import ChatPromptTemplate

class DetailRequirements(BaseModel):
    req_screen: str = Field(description="화면 요구사항에 대한 모든 정보")
    req_security: str = Field(description="보안 요구사항에 대한 모든 정보")
    req_perform: str = Field(description="성능 및 용량 요구사항에 대한 모든 정보")
    req_data: str = Field(description="데이터 및 DB 요구사항에 대한 모든 정보")

nonfuntional_req_prompt = """
당신은 전문적인 프로젝트 설계자입니다. 다음 요구사항을 바탕으로 아래 세부 요구사항을 작성해야합니다.
1. 화면 요구사항, 2. 보안 요구사항, 3. 성능 및 용량 요구사항, 4. 데이터 및 DB 요구사항
4가지 세부 요구사항 중 필요한 부분만 요구사항을 작성할 수 있고, 구체적인 요구사항이 필요하지 않다면, ' ' 공백문자로 반환할 수 있습니다.
당신은 실제 현업에서 활용 되는 방식으로 요구사항을 정리해야하며, 요구사항은 정리되어있고, 순서가 있고, 같은 요구사항 안에서 일관적이어야 합니다.

요구사항
- 요구사항 명 : {req_names}
- 요구사항 목적 : {req_purpose}
- 기능 요구사항 : {req_function}
- 프로세스 요구사항 : {req_process}
- 기획서 context : {proposal_context}
"""

generate_prompt = ChatPromptTemplate.from_template(
    nonfuntional_req_prompt
)

structed_generate_llm = llm.with_structured_output(DetailRequirements)


def generate_nonfunctional_req(state: ReqState) -> ReqState:
    """
    요구사항 기초 정보를 기반으로 세부 요구사항을 만듭니다.
    
    Args:
        state (ReqState): 기능 요구사항 정보를 담은 현재 state
        
    Returns:
        state (ReqState) : 세부 요구사항 정보를 추가한 state   
    """

    screens = []
    securitys = []
    performs = []
    datas = []

    generate_chain = generate_prompt | structed_generate_llm

    req_names_list = state['req_names']
    req_purpose_list = state['req_purpose']
    req_function_list = state['req_function']
    req_process_list = state['req_process']

    for names, purpose, function, process in zip(
        req_names_list, 
        req_purpose_list, 
        req_function_list, 
        req_process_list
    ):
        # retriever를 각 req_name에 맞게 호출합니다.
        proposal_context = retriever.invoke(names)

        # 현재 묶인 하나의 요구사항 세트를 invoke에 전달합니다.
        details = generate_chain.invoke({
            "req_names": names,
            "req_purpose": purpose,
            "req_function": function,
            "req_process": process,
            "proposal_context": proposal_context
        })
        
        screens.append(details.req_screen)
        securitys.append(details.req_security)
        performs.append(details.req_perform)
        datas.append(details.req_data)

    return {"req_screen" : screens, "req_security" : securitys, "req_perform" : performs, "req_data" : datas}
    

# %%
builder = StateGraph(ReqState)

builder.add_node("generate_nonfunctinal_req", generate_nonfunctional_req)

builder.add_edge(START, "generate_nonfunctinal_req")
builder.add_edge("generate_nonfunctinal_req", END)

graph_sj = builder.compile()
```

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)
이 프로젝트를 구현하면서 가장 고민했던 점은, LLM이 너무 많이 호출되어서 시간과 금액이 많이 나오는 부분이다.
이 부분을 수정하기 위해서 원래 설계에서 각각의 세부 요구사항에 대해서 llm을 호출해서 노드로 만든 것을 하나의 노드에서 전부 처리하는 것으로 수정했다.

## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
생각보다 코딩을 하는 것 보다 설계하고 생각하는데 많은 시간이 소모되었다. 한 편으로는 조금 자괴감이 들기도 했다. 그렇지만, 이런 과정을 쌓아서 더욱더 뛰어난 개발자가 되고싶다.
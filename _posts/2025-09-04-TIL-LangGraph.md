---
layout: post
title: "9월 4일 목요일 TIL(LangGraph)"
date: 2025-09-04 18:00:00 +0900
categories: July_week10
---

# 9월 4일 목요일 TIL 작성

## 1. 학습 주제
- LangGraph
  - ReAct
  - Memory

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### ReAct(Reasoning + Acting)

```python
from langgraph.prebuilt import create_react_agent

graph = create_react_agent(
    model=llm,
    tools = tools,
)

inputs = {
    "messages" : [
        HumanMessage("스테이크 메뉴의 가격은 얼마인가요?")
    ]
}

messages = graph.invoke(inputs)
for message in messages["messages"]:
    message.pretty_print()
```

이 방식을 통해서 간단하게 llm이 여러 tool을 호출하는 형태를 만들 수 있다.

하지만 복잡한 과정을 거치는 그래프를 만들기 위해서는 이전에 배운 직접 그래프를 만드는 것도 필요하다.

### 채팅 기록 저장하기

```python
from langgraph.checkpoint.memory import MemorySaver

memory = MemorySaver()

graph_memory = builder.compile(checkpointer=memory)
```

여러 대화사이의 채팅 기록을 저장할 수 있다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### SImilarity Search 와 Retriever의 차이점

ChromaDB에서 **`similarity_search`**는 벡터 검색 기능의 핵심적인 메서드이며, **`retriever`**는 이 검색 기능을 포함하는 더 추상적이고 확장 가능한 인터페이스입니다. 이 둘의 주요 차이점은 다음과 같습니다.

### Similarity Search

`similarity_search`는 ChromaDB의 기본적인 벡터 검색 기능입니다.

- **역할**: 주어진 쿼리(텍스트 또는 임베딩)와 가장 유사한 문서를 벡터 공간에서 직접 찾아 반환하는 역할을 합니다.
- **특징**:
    - **직접적인 검색**: 벡터 데이터베이스에 직접 질의하여 유사성 점수를 기준으로 문서를 검색합니다.
    - **결과**: 일반적으로 유사한 문서 목록과 함께 유사도 점수(distance)를 반환합니다. `similarity_search_with_score`와 같은 메서드를 통해 점수를 함께 얻을 수 있습니다.
    - **사용**: LangChain과 같은 프레임워크를 사용하지 않고 ChromaDB 라이브러리만을 사용할 때 주로 사용됩니다.

---

### Retriever

`retriever`는 LangChain에서 제공하는 추상화된 개념입니다.

- **역할**: 특정 데이터 소스(예: ChromaDB)에서 관련 문서를 검색하는 일반적인 인터페이스를 제공합니다. `similarity_search`를 포함하여 다양한 검색 방법을 포괄합니다.
- **특징**:
    - **추상화된 인터페이스**: VectorStore(ChromaDB)의 `as_retriever()` 메서드를 호출하여 생성됩니다. 이는 개발자가 특정 데이터베이스의 메서드에 종속되지 않고 일관된 방식으로 문서를 검색할 수 있게 해줍니다.
    - **다양한 검색 타입**: 단순히 유사성 검색뿐만 아니라, **MMR(Maximal Marginal Relevance)** 검색, 점수 임계값(score threshold)을 이용한 필터링, 하이브리드 검색 등 다양한 검색 전략을 사용할 수 있습니다.
    - **RAG 파이프라인 통합**: Retriever는 **RAG(Retrieval-Augmented Generation)** 파이프라인의 핵심 구성 요소로, 검색된 문서를 언어 모델에 전달하여 답변의 정확도를 높이는 데 사용됩니다.

---

요약하자면, `similarity_search`는 ChromaDB라는 **특정 벡터 데이터베이스의 검색 기능 자체**를 의미하며, `retriever`는 LangChain과 같은 프레임워크에서 이러한 검색 기능을 **통합하고 확장하기 위해 사용하는 일반적인 인터페이스**입니다. Retriever는 `similarity_search`를 기반으로 하지만, 더 다양한 검색 전략과 파이프라인 통합을 가능하게 합니다.

## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
tax 관련해서 새로운 랭그래프 만드는 과제를 오후에 진행했다. 며칠간 랭체인과 랭그래프 관련한 수업을 듣고 실습을 했지만, 아직도 처음부터 모든 걸 스스로 하려고하면 어려움이 있다. 그렇지만, 예전에 비해서, 적어도 어떤 흐름으로 프로그램을 작성해야할지 이해했기에 조금 성장한걸까?
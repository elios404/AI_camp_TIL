---
layout: post
title: "8월 27일 화요일 TIL(LangChain, Retriever)"
date: 2025-08-27 18:00:00 +0900
categories: July_week9
---

# 8월 27일 수요일 TIL 작성

## 1. 학습 주제
- retriever
- RAG로 챗봇 만들기
  - HumanMEssage, AIMessage

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### retriever에 조건을 사용하기

retriever는 벡터 데이터베이스에서 쿼리와 유사도가 높은 Document를 찾을 수 있다. 하지만, 쿼리에 따라서 유사도가 높은 문서가 없어서, 매우 낮은 유사도여도 문서를 찾는 경우가 있다. 

```python
chroma_mmr = chroma_db.as_retriever(
    search_type = "similarity_score_threshold",
    search_kwargs = {
        "k" : 2,
        "score_threshold" : 0.5, #유사도 임계값 결정
    }
)
```

다음과 같은 방식으로 특정 유사도 점수 이상이 되어야, 찾은 문서로 반환하도록 할 수 있다. 또는

```python
chroma_mmr = chroma_db.as_retriever(
    search_kwargs = {
        "k" : 2,
        "where_document" : {"$contains" : '삼성'}, # document 안에 '삼성'이라는 단어가 있을 때 만 사용
        "filter": {"source": "data\\samsung_kr.txt"}, # 이 source에서 불러온 docs 만 검색하기, metadata에 있는 내용 기반
    }
)
```

이런 방식으로, 특정 문서 소스에서만 Document를 가져오거나, 특정 단어가 Document에 등장할 때만, 문서를 찾도록 할 수 있다.

### RAG를 이용한 챗봇에서 이전 대화를 기억하도록

```python
import gradio as gr
from langchain_core.messages import HumanMessage, AIMessage
# 이걸 굳이 쓰는 이유는?

def answer_invoke(message, history):

    history_langchain_format = []
    for human, ai in history:
        history_langchain_format.append(HumanMessage(content=human))
        history_langchain_format.append(AIMessage(content=ai))

    history_langchain_format.append(HumanMessage(content=message))

    response = rag_chain.invoke(message)

    final_answer = llm.invoke(
        history_langchain_format[:-1] + [AIMessage(content=response)] + [HumanMessage(content=message)]
    )

    return final_answer.content

demo = gr.ChatInterface(fn=answer_invoke, title="QA Bot")
```

`history` 에는 이전에 챗봇에서 나누었던 대화를 가지고 있다. (gradio 사용시)

이때 history에서 대화 내용을 불러와 `HumanMessage` 혹은 `AIMessage` 를 통해서 메세지가 사용자인지, AI의 질문인지를 구분하고 리스트에 넣어. gpt 프롬프트로 넣어줄 수 있다.

이런 방식은 LLM에게 대화의 맥락을 정확하게 전달하여, 이를 바탕으로 더 정확하고 적절한 답변을 생성할 수 있게 한다.

### LangChain에서 `HumanMessage`와 `AIMessage`를 사용하는 이유

`LangChain`을 이용해 대화형 AI 챗봇을 만들 때, 단순한 텍스트가 아닌 **메시지의 역할(role)**을 명확히 구분해야 한다. 이를 위해 `HumanMessage`와 `AIMessage` 클래스를 사용한다.

- **`HumanMessage`**: 사용자가 보낸 메시지를 나타낸다.
- **`AIMessage`**: AI가 보낸 메시지를 나타낸다.

이 두 클래스를 사용하는 주된 이유는 다음과 같다.

- **대화 맥락 파악**: LLM이 이전 대화의 흐름을 정확히 이해하여 자연스럽고 일관된 답변을 생성하는 데 필수적이다.
- **역할 기반 명령어 처리**: 단순한 정보 전달이 아닌 '사용자의 요청'을 LLM이 명확히 구분하고 처리할 수 있게 돕는다.
- **다양한 모델 호환성**: 대부분의 LLM API는 대화 기록을 역할(role)과 내용(content)으로 구분하는 메시지 리스트 형태로 요구하므로, 이 클래스들은 모델 변경 시에도 코드 수정 없이 호환성을 유지하게 해준다.

결론적으로, `HumanMessage`와 `AIMessage`는 **대화 참여자를 구분하여 LLM이 맥락을 올바르게 이해하고 정확한 답변을 내놓도록 돕는 핵심적인 도구**이다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크
https://github.com/elios404/LLM_agent_study_GJ_class


## 6. 느낀 점 / 회고 
여러번 RAG를 통한 LLM 활용에 대한 코드를 작성하면서, 더욱 익숙해 지는 하루였다. 또한 나의 질문 능력이 부족한가? 리더십이 부족한가? 라는 회의감이 드는 하루였다.
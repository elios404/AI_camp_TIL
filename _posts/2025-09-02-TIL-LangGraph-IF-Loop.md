---
layout: post
title: "9월 2일 화요일 TIL(LangGraph IF, Loop)"
date: 2025-09-02 18:00:00 +0900
categories: July_week10
---

# 9월 2일 화요일 TIL 작성

## 1. 학습 주제
- langgraph
  - 조건문
  - 반복문
- 사용자 감정 분석 과제

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### LangGraph 분기문

`add_conditional_edges` 를 통해서 분기문을 구현, 분기문 노드에서 어떤 값을 리턴하는 지에 따라서 후에 어떤 노드로 갈지 딕셔너리 형태로 작성

### MessagesState

과거 대화 내용을 담는 messages라는 필드가 기본으로 주어진다?

이를 바탕으로 이전에 어떤 대화가 오고 갔는지, 이 필드에 넣어주는 것으로 저장하고 LLM에 활용할 수 있다.

반복문은 위에 분기문에서 조건이 만족할 때, 이전 노드의 이름을 넣어주면 반복을 할 수 있다.

### 사용자 감정 분석 과제 with langGraph

```python
# # 사용자 감정 분석 과제
# 
# 사용자의 입력 문장에서 감정을 분석하여,
# 긍정적인 문장은 축하 메세지를, 부정적인 문장은 위로 메시지를 생성하는
# LangGraph 조건 분기 그래플르 작성하시오.
# 
# - 사용자의 입력을 받고 간단한 감정 분석 실행(LLM 활용)
# - 분석 결과가 "positive" 면 -> generate_congrats_response
# - 분석 결과가 "negative" 면 -> generate_encouragement_response

from typing import TypedDict

class EmotionState(TypedDict):
    user_input: str
    sentiment: str
    final_response: str

def get_user_input(state: EmotionState) -> EmotionState:
    user_input = input("오늘은 어떤 일이 있으셨나요?")

    return {"user_input" : user_input}

from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model = "gpt-4o-mini",
)

def analyze_sentiment(state: EmotionState) -> EmotionState:
    analyze_template = """ 
    사용자의 입력을 기반으로 사용자의 감정 분석을 실행하세요.
    사용자의 감정은 3가지 [positive, negative, neutral]로 구분됩니다.
    단순히 사용자의 입력의 표면적인 부분만 보지 않고, 그 안의 숨은 의미가 무엇인지도 생각하여 감정 분석을 실행하고,
    반드시 [positive, negative, neutral] 3가지 구분 중 하나로 답변하세요. 감정의 분류만 답변하세요.

    예시1 : positive
    에시2 : negative

    사용자 입력 : {user_query}
    """

    analyze_prompt = ChatPromptTemplate.from_template(analyze_template)
    analyze_chain = analyze_prompt | llm | StrOutputParser()

    sentiment = analyze_chain.invoke({"user_query" : state["user_input"]})
    sentiment = sentiment.strip().lower()

    if sentiment not in ["positive","negative","neutral"]:
        sentiment = "neutral"

    print("감정 : ", sentiment)

    return {"sentiment": sentiment}
    
from typing import Literal

# def decide_next_step(state: EmotionState) -> Literal["generate_congrats_response", "generate_encouragement_response", "generate_neutral_response"]:
#     sentiment = state["sentiment"]
#     if sentiment == "positive":
#         return "generate_congrats_response"
#     elif sentiment == "negative":
#         return "generate_encouragement_response"
#     elif sentiment == "neutral":
#         return "generate_neutral_response"
#     else:
#         return "Inaccurate emotion"
    
def decide_next_step(state: EmotionState) -> Literal["positive", "negative", "neutral"]:
    return state["sentiment"]

def generate_congrats_response(state: EmotionState) -> EmotionState:
    response_template = """ 
    사용자 입력 : {user_query}

    감정 분석 결과 : {sentiment}

    사용자 입력 분석 결과 postive로 분석되었습니다.
    사용자의 입력을 바탕으로 사용자를 축하하는 말을 생성해 주세요.
    사용자에게는 정중하고 예의있는 말투로 답변하고, 긍정적인 감정을 함께 나누는 형식으로 답변해주세요.
    """

    response_prompt = ChatPromptTemplate.from_template(response_template)
    response_chain = response_prompt | llm | StrOutputParser()

    final_resposne = response_chain.invoke({"user_query" : state["user_input"], "sentiment" : state["sentiment"]})

    print(f"AI : {final_resposne}")

    return {"final_response" : final_resposne}

def generate_encouragement_response(state: EmotionState) -> EmotionState:
    response_template = """ 
    사용자 입력 : {user_query}

    감정 분석 결과 : {sentiment}

    사용자 입력 분석 결과 negative로 분석되었습니다.
    사용자의 입력을 바탕으로 사용자를 위로하는 말을 생성해 주세요.
    사용자에게는 따뜻하고 자상한 말투로 답변하고, 부정적인 감정을 함께 나누는 형식으로 답변해주세요.
    """

    response_prompt = ChatPromptTemplate.from_template(response_template)
    response_chain = response_prompt | llm | StrOutputParser()

    final_resposne = response_chain.invoke({"user_query" : state["user_input"], "sentiment" : state["sentiment"]})

    print(f"AI : {final_resposne}")

    return {"final_response" : final_resposne}

def generate_neutral_response(state: EmotionState) -> EmotionState:
    response_template = """ 
    사용자 입력 : {user_query}

    감정 분석 결과 : {sentiment}

    사용자 입력 분석 결과 neutral로 분석되었습니다.
    사용자의 입력을 바탕으로 사용자에게 중립적인 답변을 생성해 주세요.
    사용자에게는 정중하고 예의있는 말투로 답변하고, 내용을 기반으로 상대방이 어떤 감정을 느꼈는지 끌어내는 답변을 생성하세요.
    """

    response_prompt = ChatPromptTemplate.from_template(response_template)
    response_chain = response_prompt | llm | StrOutputParser()

    final_resposne = response_chain.invoke({"user_query" : state["user_input"], "sentiment" : state["sentiment"]})

    print(f"AI : {final_resposne}")

    return {"final_response" : final_resposne}

from langgraph.graph import StateGraph, START, END

# 그래프 생성

builder = StateGraph(EmotionState)

builder.add_node("get_user_input", get_user_input)
builder.add_node("analyze_sentiment", analyze_sentiment)
builder.add_node("generate_congrats_response", generate_congrats_response)
builder.add_node("generate_encouragement_response", generate_encouragement_response)
builder.add_node("generate_neutral_response", generate_neutral_response)

builder.add_edge(START, "get_user_input")
builder.add_edge("get_user_input", "analyze_sentiment")

builder.add_conditional_edges(
    "analyze_sentiment",
    decide_next_step,
    # {
    #     "generate_congrats_response":"generate_congrats_response",
    #     "generate_encouragement_response": "generate_encouragement_response",
    #     "generate_neutral_response" : "generate_neutral_response"
    # }
    {
        "positive":"generate_congrats_response",
        "negative": "generate_encouragement_response",
        "neutral" : "generate_neutral_response"
    }
    )

builder.add_edge("generate_congrats_response",END)
builder.add_edge("generate_encouragement_response", END)
builder.add_edge("generate_neutral_response", END)

graph = builder.compile()

from IPython.display import Image, display

display(Image(graph.get_graph().draw_mermaid_png()))

while True:
    init_state = {"user_input" : ""}
    graph.invoke(init_state)
    continue_chat = input("다른 질문이 있으신가요?(y/n)").lower()

    if continue_chat != 'y':
        print("종료")
        break
```

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
이전에는 랭체인과 랭그래프가 대체 뭔지, 어떻게 쓰는 것인지 등을 하나도 몰랐는데, 이제 어느정도 개념은 이해했다는 것이 너무 놀랍고 즐겁다. 역시 개발 공부는 재미있다.
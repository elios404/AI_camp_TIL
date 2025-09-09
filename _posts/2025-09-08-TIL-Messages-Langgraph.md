---
layout: post
title: "9월 8일 월요일 TIL(Messages-langgraph)"
date: 2025-09-08 18:00:00 +0900
categories: July_week11
---

# 9월 8일 월요일 TIL 작성

## 1. 학습 주제
- Messages
  - AIMessages / HumanMessage
- Langgraph
  - START/END

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### AIMessage 객체

#### **핵심 요약**

- `AIMessage`는 LangChain과 같은 LLM 프레임워크에서 **모델의 응답을 담는 표준 객체**이다.
- 단순히 텍스트만 담는 것이 아니라, 응답에 대한 메타데이터(e.g., 토큰 사용량, 응답 ID 등)도 함께 포함한다.
- `TypedDict`에서 `answer: str`와 같이 타입 힌트가 `str`로 지정되어 있어도, **Python의 유연한 타입 시스템** 때문에 `AIMessage` 객체를 직접 할당할 수 있다. 이는 Python의 타입 힌트가 **실행 시점에 타입을 엄격하게 강제하지 않기 때문**이다.
- `StrOutputParser`와 같은 파서는 `AIMessage` 객체에서 **`content` 속성에 있는 순수한 텍스트만 추출**하기 위해 사용되는 도구이다. 즉, 객체를 문자열로 "변환"하는 것이 아니라, 객체 안의 특정 정보를 "추출"하는 것이다.

#### **정리 & 결론**

이전에 코드를 작성하며 `answer: str` 필드에 `AIMessage` 객체가 오류 없이 들어가는 것을 보고 의아했다. 처음에는 객체가 자동으로 문자열로 변환되는 것이라 생각했지만, 오늘 대화를 통해 그게 아니라는 것을 명확히 알게 되었다.

결론적으로, `AIMessage` 객체를 `str` 타입의 변수에 할당하는 것이 가능한 이유는 Python의 **동적 타이핑(Dynamic Typing)**과 **타입 힌트의 보조적인 역할** 때문이다.

- **타입 힌트**: 개발자의 가독성과 정적 분석 도구를 위한 "안내" 역할.
- **실제 동작**: 런타임에서는 타입 일치 여부를 엄격하게 검사하지 않아, 타입 힌트와 다른 타입의 객체를 할당해도 문제없이 작동한다.

만약 `answer` 필드에 오로지 텍스트만 저장하고 싶다면, `stroutputparser`를 사용해서 `AIMessage` 객체의 `.content`를 추출해야 한다. 하지만 모델 응답 객체 자체를 유지하며 더 많은 정보를 활용하고 싶다면, 객체 그대로를 할당하는 것이 더 효율적일 수 있다.

### LangGraph 서브그래프의 `START`와 `END`

#### **핵심 정리**

- LangGraph에서 **서브 그래프(SubGraph)는 재사용 가능한 독립적인 워크플로우 단위**이다.
- 서브 그래프는 자체적으로 `START`와 `END` 노드를 가져야 한다.
- 서브 그래프의 `END` 노드는 해당 서브 그래프의 **종료 지점**을 명시하며, 이 지점에 도달하면 제어권이 서브 그래프를 호출한 **상위(Parent) 그래프**로 되돌아간다.
- 따라서, **전체 그래프의 `END` 노드와 서브 그래프의 `END` 노드는 공존할 수 있으며, 각자의 역할을 충실히 수행한다.** 서브 그래프의 `END`는 '이 함수(서브 그래프)의 실행은 끝났다'는 신호이고, 전체 그래프의 `END`는 '모든 워크플로우가 끝났다'는 최종 신호이다.

---

#### **세부 내용 및 깨달음**

이전에 서브 그래프를 만들 때, 전체 그래프에서만 `END`가 유효하다고 생각하여 서브 그래프의 `END` 노드를 제거해야 하는지 고민했었다. 하지만 이는 프로그래밍의 함수 호출 개념과 동일하다는 것을 깨달았다.

예를 들어, `메인_함수()` 내에서 `서브_함수()`를 호출할 때, `서브_함수()`는 자체적으로 `return` 문을 통해 종료된다. 이 `return`은 `메인_함수()`의 다음 줄로 실행 흐름을 돌려보낸다. LangGraph의 `END` 노드도 이와 같은 역할을 한다.

- **`END` 노드의 역할 구분:**
    - **서브 그래프의 `END`**: 서브 그래프의 결과물을 반환하고, 상위 그래프로 제어권을 넘긴다.
    - **상위 그래프의 `END`**: 전체 작업의 최종 결과를 반환하고, 전체 그래프의 실행을 종료한다.

따라서 서브 그래프를 모듈화하여 재사용성을 높이려면, 서브 그래프는 반드시 독립적인 실행 단위로서 `START`와 `END`를 모두 갖추고 있어야 한다. 이는 복잡한 로직을 구조적으로 설계하는 데 매우 중요한 부분이다.

### LangChain `with_structured_output`

```python
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
structed_router_llm = router_llm.with_structured_output(Route) # 이 부분이 가장 중요하다. 이걸 할 때, Route 객체의 Literal이나 Field(description도 읽는 것일까?)
```

#### **핵심 요약**

- `with_structured_output` 메서드는 단순히 LLM의 출력 형식을 지정하는 기능이 아니다.
- Pydantic 모델에 포함된 **`Literal` 값**과 **`Field(description)`** 같은 모든 메타데이터를 LLM이 이해할 수 있는 **JSON 스키마** 형태로 전달한다.
- 이를 통해 LLM은 정해진 선택지(Literal) 내에서, 주어진 맥락(description)을 바탕으로 훨씬 더 정확하고 신뢰성 높은 답변을 생성한다.

---

#### **정리 & 깨달음**

처음에 `with_structured_output`를 사용하면서 Pydantic 모델의 `Literal`과 `description` 정보가 과연 LLM에 전달될까 궁금했었다. 단순히 출력 포맷만 맞추는 기능이라고 생각했기 때문이다.

하지만 오늘 확인한 결과, 나의 초기 생각은 틀렸다는 것을 알게 되었다.

- **`Literal`의 역할**: `"llm"`, `"vector_store"`처럼 정해진 값을 `Literal`로 제공하면, LLM은 이 값 외의 다른 단어를 생성하지 못한다. 예를 들어, `'database'` 같은 잘못된 응답을 만들 가능성을 원천 차단한다.
- **`description`의 역할**: `Field`에 포함된 설명은 LLM이 각 필드의 목적을 명확히 이해하도록 돕는다. 덕분에 라우팅 로직처럼 복잡한 판단을 내릴 때, LLM은 더 정확한 결정을 내릴 수 있다.

결론적으로, `with_structured_output`는 LLM에게 단순한 템플릿이 아닌, **'작성 규칙'**이 담긴 상세한 설계도를 제공하는 것과 같다. 덕분에 출력의 정확성과 예측 가능성을 크게 높일 수 있다는 것을 깨달았다.

### LangGraph의 병렬 처리와 자동 동기화

오늘 LangGraph에서 **병렬 처리**된 노드의 결과를 합치는 과정에 대해 배웠다. 여러 노드를 동시에 실행하고 그 결과를 하나의 노드로 모을 때, LangGraph가 이전 노드들이 모두 끝났는지 자동으로 확인하고 다음 노드를 실행한다는 점이 인상 깊었다.

---

#### **핵심 원리: DAG와 자동 동기화**

LangGraph는 **방향성 비순환 그래프(DAG)** 구조를 기반으로 한다. 이 구조의 핵심 규칙 덕분에 별도의 코드 없이도 다음 두 가지가 자동으로 이루어진다.

1. **실행 대기**: `calculate_tax_base`와 같이 여러 노드에서 들어오는 엣지를 가진 노드는, 해당 엣지들의 소스 노드(`get_tax_base_equation`, `get_tax_deduction`, `get_market_ratio`)가 **모두 완료될 때까지 실행을 시작하지 않는다**. 마치 컨베이어 벨트에서 모든 부품이 도착해야만 다음 조립 과정이 시작되는 것과 같다.
2. **상태 업데이트**: 각 병렬 노드의 결과는 그래프의 공유된 **상태(State)**에 순차적으로 업데이트된다. 이후 `calculate_tax_base`는 이 상태에 접근하여 모든 필요한 데이터를 한 번에 가져와서 작업을 처리한다.

#### **결론**

처음에는 병렬 처리된 노드들이 끝났는지 일일이 확인하는 로직을 직접 짜야 할까 걱정했다. 하지만 LangGraph의 그래프 구조 자체가 이 복잡한 동기화 문제를 해결해 준다는 것을 알게 되었다. 덕분에 더 간결하고 안정적인 워크플로우를 설계할 수 있다는 것을 깨달았다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 

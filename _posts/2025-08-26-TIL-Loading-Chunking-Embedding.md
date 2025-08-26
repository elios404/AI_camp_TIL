---
layout: post
title: "8월 26일 화요일 TIL(Loading, Chunking, Embedding)"
date: 2025-08-26 18:00:00 +0900
categories: July_week9
---

# 8월 26일 화요일 TIL 작성

## 1. 학습 주제
- 8월 25일 복습
- RecursiveCharacterTextSplitter
- CharacterTextSplitter
- 토크나이져
- Chroma DB
- Parser가 파싱하는 원리

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### 8.25 일 복습

관계형 DB는 데이터의 무결성을 보장받기 위해서 만들어진 목적!

NoSQL, Vector DB는 무결성을 생각하지 않음.

NoSQL 은 데이터의 형태가 일정하지 않음. 여러 다양한 형식의 데이터를 담기 위해서

```python
# db를 읽기 위한 목적이라, texts를 따로 넣어주지 않음!
vectorstore = Chroma(
    # 왜 임베딩이 필요할까? : 의미론적인 의미에서 검색하기 위해서, 즉 유사도를 비교하기 위해서, 임베딩으로 벡터 차원 공간에 문서를 박아넣음
    embedding_function=embeddings, 
    collection_name="chroma_test",     
    persist_directory='./chroma_db'
)

```

PromptTemplate 을 통해서 프롬프트 형식을 만들 수 있다.

```python
messages = [
    ("system", "You are a helped assistant"),
    ("user", "{query}")
]

# chatPromptTemplate으로 형식을 만들기
prompt = ChatPromptTemplate.from_messages(messages=messages)
```

또한 **LCEL 체인**을 통해서 체인을 만들 수 있다.

```python
chain = prompt | llm

response =chain.invoke({"query" : "테슬라 창업자는 누구인가요?"})

print(response.content)
```

Parser를 통해서 답변을 내가 원하는 형식으로 반환 받을 수 있다.

- StrOutputParser : 문자열 형태로 반환
- JsonOutputParser : json 형식으로 반환
- PydanticOutputParser : 파이썬 클래스 객체 형태로 반환

```python
# Parser 중에서 가장 많이 쓰이는 String parser
from langchain_core.output_parsers import StrOutputParser

# 출력 파서를 생성

output_parser = StrOutputParser()
output_parser.invoke(response) # 문자열 부분만 가져오기
```

문자열로 template 만들기!

```python
from langchain.prompts import ChatPromptTemplate

template = """다음 context 만을 바탕으로 질문에 답하라.
외부 정보나 지식을 사용하지 말라.
답변이 context 와 맞지 않는 경우 혹은 일치하지 않는 경우 답변을 "잘 모르겠습니다." 라고 하라.

[Context]
{context}

[Question]
{question}

[Answer]
"""

# 위에서는 from_message로 템플릿을 만들었지만, 문자열로 템플릿을 만들 때에는 from_template을 사용한다.
prompt = ChatPromptTemplate.from_template(template=template)

prompt.pretty_print()
```

### RecursiveCharacterTextSplitter

청크를 나누는 이유는, LLM에 들어갈 수 있는 input에 제한이 있고, 또 너무 많은 input은 좋지 않기 때문이다.

```python
from langchain_text_splitters import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size = 1000,
    chunk_overlap = 200, # 문맥을 살리기 위해서 겹치는 부분 크기
    length_function = len, # 글자 수를 기준 -> 이걸 왜씀? 청크 사이즈 1000이 글자수 1000이 되도록 한다.
    separators = ["\n\n", "\n", " ", ""], # 여러 기준으로 청크 나누기가 가능, 앞에 나오는 것 우선
)

texts = text_splitter.split_documents(pdf_docs) 
```

### 코드 분석 및 설명

---

**`chunk_size = 1000`**

> 문서를 1000자 단위의 **청크(Chunk)**로 나눕니다. 이 설정은 텍스트를 처리하기 위한 기본적인 덩어리 크기를 정의합니다.
> 

**`chunk_overlap = 200`**

> 인접한 청크 간에 200자만큼 중복되도록 합니다. 이 중복은 한 청크의 끝과 다음 청크의 시작 부분이 연결되어 문맥이 끊기는 것을 방지합니다. 예를 들어, 청크 1의 마지막 200자가 청크 2의 시작 200자와 동일하게 됩니다. 이는 문장의 의미가 다음 청크로 자연스럽게 이어지도록 도와줍니다.
> 

**`length_function = len`**

> len 함수를 사용하여 청크의 크기를 문자(character) 단위로 측정하도록 지정합니다. 이는 chunk_size가 글자 수를 의미함을 명확히 합니다. 만약 다른 함수를 사용하면 단어 수나 토큰 수 등 다른 기준으로 크기를 측정할 수 있지만, 일반적으로는 len을 사용합니다.
> 

**`separators = ["\n\n", "\n", " ", ""]`**

> 텍스트를 분할할 때 사용할 구분자(separator) 목록입니다. RecursiveCharacterTextSplitter는 이 목록에 있는 구분자들을 순서대로 시도합니다.
> 
> 1. `"\n\n"`: 두 개의 개행 문자(단락 구분)를 기준으로 먼저 나눕니다.
> 2. `"\n"`: 하나의 개행 문자(줄 바꿈)를 기준으로 나눕니다.
> 3. `" "` : 공백(띄어쓰기)을 기준으로 나눕니다.
> 4. `""`: 마지막으로, 어떤 구분자로도 나눌 수 없을 때 모든 문자를 하나씩 잘라냅니다.
> 
> 이 방식을 통해 의미 있는 단위(단락, 문장, 단어)를 최대한 유지하면서 문서를 분할할 수 있습니다.
> 

### **CharacterTextSplitter**

`CharacterTextSplitter`는 **단일한 구분자(separator)**를 사용하여 텍스트를 나눕니다. 구분자를 명시적으로 지정하지 않으면 기본값으로 `"\n\n"`(이중 개행 문자)를 사용합니다.

- **동작 방식**: 지정된 구분자를 기준으로 텍스트를 단순히 분할합니다. 만약 텍스트에 구분자가 없으면, 설정된 `chunk_size`에 따라 텍스트를 강제로 잘라냅니다.
- **특징**:
    - **단순하고 예측 가능**합니다.
    - 텍스트의 구조나 의미를 고려하지 않고 정해진 규칙에 따라 일괄적으로 처리합니다.
    - 예시: `separator = " "` 로 설정하면 모든 공백을 기준으로 텍스트를 분리합니다.


### **RecursiveCharacterTextSplitter**

`RecursiveCharacterTextSplitter`는 **여러 개의 구분자(separators) 목록**을 사용하여 텍스트를 나눕니다. 이 구분자들은 **우선순위**를 가집니다.

- **동작 방식**: 목록의 첫 번째 구분자로 먼저 텍스트를 분할합니다. 만약 분할된 청크가 `chunk_size`보다 크면, 다음 구분자를 사용하여 해당 청크를 다시 분할하는 과정을 **재귀적(recursive)**으로 반복합니다. 이 과정은 청크의 크기가 `chunk_size`보다 작아질 때까지 계속됩니다.
- **특징**:
    - **의미 단위로 분할**하려는 노력을 합니다. 기본 구분자 목록은 `["\n\n", "\n", " ", ""]`으로, 단락, 문장, 단어 등 의미가 있는 단위가 최대한 보존되도록 합니다.
    - **유연하고 똑똑한 분할**이 가능합니다. 큰 단락이 있으면 줄 바꿈을 기준으로 나누고, 그것도 길면 단어를 기준으로 나누는 등 계층적으로 접근하여 문맥을 최대한 유지합니다.
    - 예시: 긴 문서를 `"\n\n"`으로 먼저 나누고, 그 결과가 너무 길면 `"\n"`으로, 그래도 길면 `" "`으로, 마지막에는 모든 문자를 하나씩 잘라냅니다.

---

**임베딩** : 텍스트를 벡터로 표현함으로써 의미적으로 가장 유사한 다른 텍스트를 찾는 등 수학적 연산을 수행가능하기 위해 하는 것

### 토크나이저 

토크나이저를 넣은 것과 넣지 않은 것의 가장 큰 차이는 **청크 크기 계산의 기준**

#### **토크나이저를 넣지 않은 경우**

`CharacterTextSplitter`는 기본적으로 **글자(character)** 수를 기준으로 청크를 나눕니다.

- **chunk_size = 1000**이라고 설정하면, 텍스트를 1,000 글자씩 자릅니다.
- **문제점**: '안녕하세요'는 5글자지만, 토큰으로는 1~2개로 처리될 수 있습니다. 즉, 글자 수와 토큰 수가 일치하지 않아 모델이 처리할 수 있는 토큰 한계(예: 8191 토큰)를 초과할 위험이 있습니다.

#### **토크나이저를 넣은 경우**

`CharacterTextSplitter.from_huggingface_tokenizer()`를 사용하고 토크나이저를 전달하면, 청크 크기 계산 기준이 **토큰(token)** 수로 바뀝니다.

- **chunk_size = 1000**이라고 설정하면, 텍스트를 1,000 토큰씩 자릅니다.
- **역할**: 토크나이저는 텍스트를 모델이 이해하는 최소 단위(토큰)로 변환하는 역할을 합니다. 토크나이저를 사용하면, 청크가 모델의 **입력 토큰 한계**를 넘지 않도록 정확하게 제어할 수 있습니다. 예를 들어, `text-embedding-3-small` 모델은 최대 8191 토큰을 처리할 수 있으므로, 청크 크기를 이 한계보다 작게 설정하면 안전합니다.

#### **요약 및 비교**

| 구분 | 토크나이저를 넣지 않은 경우 | 토크나이저를 넣은 경우 |
| --- | --- | --- |
| **청크 크기 기준** | 글자(character) 수 | 토큰(token) 수 |
| **청크 분할 방식** | 단순 글자 수 기반 분할 | 토큰화 후, 토큰 수 기반 분할 |
| **정확성** | 모델의 토큰 한계와 불일치 가능성 높음 | 모델의 토큰 한계와 정확히 일치 |
| **최종 역할** | 일반적인 텍스트 분할 | **모델에 최적화된** 텍스트 분할 |

따라서, 임베딩 모델이나 언어 모델에 최적화된 청크를 만들고 싶다면 **토크나이저를 넣어 청크 크기를 토큰 단위로 관리**하는 것이 가장 정확하고 안정적인 방법입니다.

토크나이저를 적용한 텍스트 분할 방식의 장단점은 다음과 같습니다.

#### **장점 (Benefits)**

- **모델과의 호환성**: 가장 큰 장점은 모델이 처리할 수 있는 입력 길이에 정확히 맞출 수 있다는 것입니다. 모델의 최대 토큰 한계(예: 8191 토큰)를 넘지 않도록 안전하게 청크를 생성할 수 있어, 오버플로우 에러를 방지하고 모델이 모든 정보를 처리하도록 보장합니다.
- **의미적 일관성**: 토크나이저를 사용하면 텍스트를 모델이 이해하는 최소 의미 단위(토큰)로 나누기 때문에, 단순히 글자 수로만 자르는 것보다 문맥이 더 자연스럽게 유지될 가능성이 높습니다.
- **다국어 지원**: 다국어 모델의 경우, 언어별로 글자 수와 토큰 수의 비율이 다릅니다. 토크나이저를 사용하면 어떤 언어든 동일한 토큰 기준을 적용할 수 있어 일관된 결과를 얻을 수 있습니다.


#### **단점 (Drawbacks)**

- **복잡성 및 의존성**: 텍스트 분할을 위해 추가적으로 토크나이저를 로드해야 하므로 코드가 더 복잡해지고, 모델 파일 다운로드(약 2.2GB)라는 추가적인 작업이 필요합니다.
- **처리 속도**: 토크나이저를 먼저 거쳐야 하기 때문에, 단순히 글자 수로 나누는 방식보다 처리 속도가 약간 느릴 수 있습니다.
- **정확한 토큰 분할의 불확실성**: 토크나이저의 종류에 따라 같은 문장이라도 토큰 분할 결과가 다를 수 있습니다. 특히 `RecursiveCharacterTextSplitter`가 더 의미론적인 분할을 위해 여러 구분자를 사용하는 것과 달리, 토크나이저 기반의 분할은 단순히 토큰 수를 맞추기 위해 잘릴 수 있습니다.

### Chorma DB 부르는 방식 차이

말씀하신 대로, `embedding`과 `embedding_function` 매개변수의 차이는 **데이터를 저장할 때 사용하느냐, 아니면 저장된 데이터를 읽어올 때 사용하느냐**에 따라 다릅니다.


#### 1. `Chroma.from_documents()` (저장/쓰기)

`Chroma.from_documents()`는 새로운 ChromaDB 컬렉션을 만들고, 제공된 `documents`를 임베딩하여 **처음으로 데이터를 저장**할 때 사용합니다.

- `embedding` 매개변수를 사용합니다.
- 이곳에 `OpenAIEmbeddings()` 또는 `HuggingFaceEmbeddings()`와 같은 **임베딩 모델의 인스턴스**를 전달합니다.
- Chroma는 이 모델을 사용하여 문서들을 벡터로 변환하고, 그 결과를 문서와 함께 데이터베이스에 저장합니다.


#### 2. `Chroma()` (읽기/쿼리)

`Chroma()`는 이미 데이터가 저장되어 있는 기존 컬렉션에 **접속하여 데이터를 읽거나 쿼리할 때** 사용합니다.

- `embedding_function` 매개변수를 사용합니다.
- 이곳에도 역시 **임베딩 모델의 인스턴스**를 전달합니다.
- Chroma는 이 모델을 사용하여 사용자의 **질문(query)**을 벡터로 변환한 다음, 이 벡터와 데이터베이스에 저장된 벡터들 간의 유사도를 비교하여 가장 유사한 문서를 찾아냅니다.

**요약하자면,**

- `embedding`: **`from_documents`** 메서드에서 **"처음 저장할 때"** 문서 임베딩에 사용됩니다.
- `embedding_function`: **`Chroma()`** 클래스에서 **"질문할 때"** 질문 임베딩에 사용됩니다.

두 매개변수 모두 동일한 종류의 임베딩 모델 인스턴스를 받지만, 그 역할과 사용 시점이 다릅니다

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### LangChain Parser는 어떻게 마법처럼 LLM 답변을 객체로 변환할까?

LangChain을 사용하다 보면 LLM이 반환하는 단순 텍스트를 `PydanticOutputParser`가 어떻게 정확하게 Pydantic 모델(클래스) 객체로 매핑해주는지 궁금할 때가 있다. 이 과정이 마치 작은 AI가 문맥을 이해하고 변환하는 것처럼 보이지만, 실제로는 **명확한 지시와 규칙에 기반한 자동화된 프로세스**다.

#### 내가 궁금했던 점 🤔

1. LLM의 답변(JSON 형태의 텍스트)을 Parser가 어떻게 정확히 Pydantic 클래스의 필드에 매핑하는 걸까?
2. 이 매핑 과정은 하드코딩된 규칙 기반일까, 아니면 간단한 AI 모델처럼 학습된 내용을 바탕으로 유연하게 처리하는 걸까?
3. LCEL 체인(`prompt | llm | parser`)에서 Parser는 LLM 뒤에 있는데, LLM은 어떻게 Parser가 원하는 출력 형식을 미리 알고 응답을 생성하는 걸까?


#### 알게 된 내용 🤓

##### 1. 모든 시작은 '설계도': Pydantic `BaseModel`의 역할

매핑의 첫 단추는 `BaseModel`을 상속받는 Pydantic 클래스를 정의하는 것에서 시작된다.

Python

`from langchain_core.pydantic_v1 import BaseModel, Field

class Person(BaseModel):
    name: str = Field(..., description="The name of the person")
    title: str = Field(..., description="The title of position of the person")`

이 클래스는 단순한 데이터 구조가 아니라, Parser와 LLM이 사용할 **명확한 '설계도'** 역할을 한다.

- **구조 정의**: 최종 결과물은 반드시 `name`과 `title` 필드를 가져야 한다.
- **타입 강제**: `name`과 `title`은 반드시 문자열(`str`)이어야 한다.
- **명확한 설명**: `description`은 각 필드가 무엇을 의미하는지 정의하며, 이 설명은 나중에 LLM에게 보낼 지침의 재료가 된다.

결국 `BaseModel`은 "내가 원하는 최종 데이터는 바로 이 형태야!"라고 명확하게 선언하는 과정이다.

##### 2. 마법의 비밀: LLM에게 전달되는 "출력 형식 설명서"

`PydanticOutputParser`의 가장 핵심적인 기능은 `get_format_instructions()` 메서드에 있다. 이 메서드는 위에서 정의한 `Person` 클래스('설계도')를 분석하여, **LLM이 반드시 따라야 할 출력 형식에 대한 매우 구체적인 "명령서"**를 텍스트 형태로 자동 생성한다.

Python

`person_parser = PydanticOutputParser(pydantic_object=Person)
format_instructions = person_parser.get_format_instructions()
print(format_instructions)`

이 코드를 실행하면 LLM에게 "너는 반드시 이 JSON 스키마를 준수하는 JSON 객체를 생성해야 해" 라는 내용의 상세한 지침이 출력된다.

##### 3. 체인의 동작 원리: '준비'와 '실행'의 분리

가장 궁금했던 "Parser가 LLM 뒤에 있는데 어떻게 지시가 전달될까?"에 대한 해답은 LangChain의 **`.partial()`** 메서드와 **체인의 2단계 동작 방식**에 있었다.

1. **준비 단계 (체인 정의)**: 코드를 작성하고 체인을 정의하는 시점이다.Python
    
    `prompt = ChatPromptTemplate.from_messages(...)
        .partial(format_instructions=person_parser.get_format_instructions())
    
    person_chain = prompt | llm | person_parser`
    
    - `partial()` 메서드는 `get_format_instructions()`가 생성한 "명령서"를 프롬프트 템플릿의 `{format_instructions}` 자리에 **미리 채워 넣어 버린다.**
    - 즉, `person_chain`이 정의되는 이 시점에 `prompt` 객체는 이미 **"이런 형식으로 출력해!"라는 지시사항을 품은 상태**가 된다.
2. **실행 단계 (`invoke` 호출)**: `invoke()`를 호출하면 데이터가 정의된 순서대로 흐른다.Python
    
    `person_chain.invoke({"query": "테슬라 창업자는 누구?"})`
    
    - **Prompt**: 사용자의 질문(`query`)과 **미리 준비된 출력 형식 지시(`format_instructions`)**가 합쳐져 완전한 프롬프트가 생성된다.
    - **LLM**: 완성된 프롬프트를 받아, 지시사항에 따라 JSON 형태의 텍스트를 충실히 생성한다.
    - **Parser**: LLM이 생성한 텍스트를 받아, 규칙(Pydantic '설계도')에 따라 검증하고 최종 `Person` 객체로 변환한다.


#### 최종 결론

LangChain의 Parser는 AI가 아니다. **잘 설계된 자동화 규칙**이다.

- `BaseModel`로 원하는 데이터의 **설계도**를 만든다.
- `PydanticOutputParser`는 이 설계도를 바탕으로 LLM에게 보낼 **명령서(format instructions)**를 생성한다.
- `partial()`을 통해 이 명령서를 프롬프트에 **미리 삽입**한다.
- LLM은 지시가 포함된 프롬프트를 받아 **정해진 형식대로 텍스트를 출력**한다.
- Parser는 이 텍스트에서 JSON을 **추출하고 설계도에 따라 검증**하여 최종 객체를 만든다.

이처럼 모든 과정은 마법이 아니라, 개발자가 원하는 구조화된 데이터를 LLM으로부터 안정적으로 얻어내기 위한 매우 논리적이고 체계적인 흐름이었다.



## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 

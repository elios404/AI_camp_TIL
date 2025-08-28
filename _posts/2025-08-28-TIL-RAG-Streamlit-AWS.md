---
layout: post
title: "8월 28일 목요일 TIL(RAG, AWS)"
date: 2025-08-28 18:00:00 +0900
categories: July_week9
---

# 8월 28일 목요일 TIL 작성

## 1. 학습 주제
- 8.27일 내용 복습
- RetrievalQA
- 과거 기록을 기억하는 챗봇

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### 어제 내용 복습

```python
import os
from pinecone import Pinecone
from langchain_pinecone import PineconeVectorStore

index_name = 'tax-index10'
pincone_api_key = os.getenv('PINECONE_API_KEY')
pc = Pinecone(api_key=pincone_api_key)
pinecone_index = pc.Index(index_name)

vectorstore = PineconeVectorStore.from_documents(document_list, embedding, index_name=index_name)
```

chroma DB 가 로컬에서 사용한다면 클라우드에 사용하고 싶다면, pinecone을 사용 가능

**langchain**의 `PineconeVectorStore`를 사용할 때, `pc = Pinecone(...)`처럼 파인콘 클라이언트를 따로 만들 필요가 없다. 이 라이브러리가 인덱스 이름을 통해 내부적으로 알아서 연결한다.

API 키는 코드에 직접 넣지 않는다. `.env` 파일에 저장하고, `dotenv` 라이브러리로 불러오는 것이 보안상 가장 좋은 방법이다.

### RetrievalQA

```python
from langchain.chains import RetrievalQA

from langchain import hub

prompt = hub.pull("rlm/rag-prompt")
prompt

# 이건 역방향으로 체인 순서를 결정하나? LCEL 과 달리 어떻게 순서를 명확하게 알 수 있을까?
qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    chain_type_kwargs={"prompt": prompt},
)

# 원래 우리가 langchain hub에서 불러온 템플릿은 question으로 받는다. 그런데 RetrievalQA에서 query를 자동으로 Question으로 바꿔준다?
ai_message = qa_chain.invoke({"query": query})
```

#### **질문 1: `RetrievalQA` 체인은 역방향으로 순서를 결정하나요? LECL과 달리 순서를 어떻게 명확하게 알 수 있나요?**

`RetrievalQA` 체인은 **역방향으로 순서를 결정하지 않습니다.** `RetrievalQA`는 내부적으로 미리 정의된 순서에 따라 RAG(검색 증강 생성) 과정을 수행합니다.

`RetrievalQA.from_chain_type`을 사용하면 내부적으로 다음과 같은 순서로 작업이 진행됩니다.

1. **Retrieval (검색):** `retriever` 객체를 사용하여 사용자 질의(`query`)와 관련된 문서를 검색합니다.
2. **Prompt Engineering (프롬프트 구성):** 검색된 문서 내용과 사용자의 질의를 결합하여 최종 프롬프트(이 경우 `hub.pull("rlm/rag-prompt")`로 불러온 프롬프트)를 구성합니다.
3. **Generation (생성):** 구성된 프롬프트를 언어 모델(`llm`)에 전달하여 최종 답변을 생성합니다.

이 과정은 LECL(LangChain Expression Language)을 사용하여 직접 체인을 구성할 때와는 달리, `RetrievalQA` 클래스 내부에 이미 순서가 정해져 있어 사용자가 별도로 순서를 명시할 필요가 없습니다.

---

#### **질문 2: `RetrievalQA`는 `query`를 자동으로 `question`으로 바꿔주나요?**

네, 맞습니다. `RetrievalQA` 체인은 `invoke` 메서드에서 **`"query"`** 키를 기대하며, 이 값을 내부적으로 검색(`retriever`) 및 프롬프트 (`prompt`)에 사용되는 **`"question"`** 또는 **`"query"`** 변수로 자동으로 매핑하여 전달합니다.

즉, `qa_chain.invoke({"query": query})`를 호출하면, `RetrievalQA`가 사용자의 `query`를 체인 내부의 `retriever`와 `prompt`가 이해하는 `question` 변수로 자연스럽게 변환합니다. 따라서 사용자는 `prompt` 템플릿의 변수명에 상관없이 `invoke`에는 `query`를 사용하면 됩니다.

#### 질문 3 : **`RetrievalQA`는 입력 부분 key 값을 반드시 `query` 라고 해야하나요?**

**`RetrievalQA` 체인을 `invoke` 메서드로 호출할 때는 반드시 `query`라는 키를 사용해야 합니다.**

그 이유는 `RetrievalQA` 클래스가 내부적으로 **`query`**라는 특정 키를 사용하도록 설계되었기 때문입니다.

- **LangChain Expression Language (LECL)과의 차이점:**
    - LECL을 사용하여 체인을 직접 구성할 때는 입력 변수 이름을 개발자가 자유롭게 지정할 수 있습니다. 예를 들어, `{"question": user_input}`과 같이 사용할 수 있습니다.
    - 하지만 `RetrievalQA`는 편의성을 위해 미리 정의된 추상화 클래스입니다. 따라서 `invoke` 메서드의 입력 인터페이스가 **`{"query": ...}`*로 고정되어 있습니다. 이 고정된 키를 통해 사용자의 질의를 받아 `retriever`와 `llm`에 전달하는 역할을 합니다.

결론적으로, `RetrievalQA`의 `invoke` 메서드를 사용할 때는 `query`라는 키를 사용해야만 정상적으로 작동합니다.

### lllm.py (과거 기록을 기억하는 RAG 기반 LLM 챗봇)

```python
from langchain_community.document_loaders import Docx2txtLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
import os
from pinecone import Pinecone
from langchain_pinecone import PineconeVectorStore
from langchain_openai import ChatOpenAI
from langchain import hub
from langchain.chains import RetrievalQA
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate, FewShotChatMessagePromptTemplate
from langchain.chains import create_history_aware_retriever
from langchain_core.prompts import MessagesPlaceholder
from langchain.chains import create_retrieval_chain
from langchain.chains.combine_documents import create_stuff_documents_chain
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory
from langchain_core.chat_history import BaseChatMessageHistory

from config import answer_examples

store = {}


def get_session_history(session_id: str) -> BaseChatMessageHistory:
    if session_id not in store:
        store[session_id] = ChatMessageHistory()
    return store[session_id]


def get_llm(model="gpt-4o"):
    llm = ChatOpenAI(model=model)

    return llm


def get_dictionary_chain():
    llm = get_llm()
    dictionary = ["사람을 나타내는 표현 -> 거주자"]
    prompt2 = ChatPromptTemplate.from_template(f"""
        사용자의 질문을 보고, 우리의 사전을 참고해서 사용자의 질문을 변경해주세요.
        만약 변경할 필요가 없다고 판단된다면, 사용자의 질문을 변경하지 않아도 됩니다.
        사전 : {dictionary}

        질문: {{question}}

    """)
    dictionary_chain = prompt2 | llm | StrOutputParser()

    return dictionary_chain


def get_retriever():
    embedding = OpenAIEmbeddings(model='text-embedding-3-large')
    index_name = 'tax-index'
    vectorstore = PineconeVectorStore.from_existing_index(
        index_name=index_name,
        embedding=embedding
    )
    retriever = vectorstore.as_retriever(search_kwargs={'k': 3})

    return retriever


def get_history_retriever():
    retriever = get_retriever()
    llm = get_llm()

    contextualize_q_system_prompt = (
        "Given a chat history and the latest user question "
        "which might reference context in the chat history, "
        "formulate a standalone question which can be understood "
        "without the chat history. Do NOT answer the question, "
        "just reformulate it if needed and otherwise return it as is."
    )

    contextualize_q_prompt = ChatPromptTemplate.from_messages(
        [
            ("system", contextualize_q_system_prompt),
            MessagesPlaceholder("chat_history"), # 이게 정확하게 뭐하는건가? chat_history는 어디서 가져오는 건가? 혹은 그냥 프롬프트에 넣는것?
            ("human", "{input}"),
        ]
    )

    # retriever
    history_aware_retriever = create_history_aware_retriever(
        llm, retriever, contextualize_q_prompt
    )

    return history_aware_retriever


def get_qa_chain(): # 이 부분 뭐하는건지 확인하기, 반드시..
    retriever = get_retriever()
    llm = get_llm()
    
    example_prompt = ChatPromptTemplate.from_messages(
        [
            ("human", "{input}"),
            ("ai", "{answer}"),
        ]
    )
    few_shot_prompt = FewShotChatMessagePromptTemplate(
        example_prompt=example_prompt,
        examples=answer_examples,
    )

    history_aware_retriever = get_history_retriever()
    
    system_prompt = (
        "당신은 소득세법 전문가입니다. 사용자의 소득세법에 관한 질문에 답하세요. "
        "아래에 제공된 문서를 활용해서 답변하고,"
        "답변을 알 수 없다면, 모른다고 답하세요."
        "답변을 제공할 때 '소득세법 (XX조)에 따르면' 이라고 시작하면서 답하고"
        "2~3문장 정도의 짧은 내용의 답변으로 답하라."
        "\n\n"
        "{context}"
    )
    qa_prompt = ChatPromptTemplate.from_messages(
        [
            ("system", system_prompt),
            few_shot_prompt,
            MessagesPlaceholder("chat_history"),
            ("human", "{input}"),
        ]
    )
    question_answer_chain = create_stuff_documents_chain(llm, qa_prompt)

    rag_chain = create_retrieval_chain(history_aware_retriever, question_answer_chain)

    conversational_rag_chain = RunnableWithMessageHistory(
        rag_chain,
        get_session_history,
        input_messages_key="input",
        history_messages_key="chat_history",
        output_messages_key="answer",
    ).pick('answer')

    return conversational_rag_chain


def get_ai_messages(user_message):
    dictionary_chain = get_dictionary_chain()
    qa_chain = get_qa_chain()

    tax_chain = {"input": dictionary_chain} | qa_chain
    ai_message = tax_chain.stream(
        {
            "question" : user_message,
        },
        config={
            "configurable": {"session_id": "abc123"}
        },
    )

    return ai_message
```

#### 전체 코드 개요
이 코드는 LangChain 프레임워크를 활용하여 소득세법에 대한 질문에 답변하는 대화형 RAG (Retrieval-Augmented Generation) 시스템을 구축한 것이다. RAG는 LLM이 답변을 생성할 때 외부 문서를 검색하여 참조하도록 하는 기술로, 이 코드는 사용자의 질문과 대화 이력을 기반으로 가장 관련성 높은 문서를 찾아 정확한 답변을 제공하도록 설계되었다.

#### 각 함수별 상세 설명

1. `get_session_history(session_id: str)`
이 함수는 대화 이력을 관리하는 역할을 한다.

`store = {}`: 전역 변수로 store라는 딕셔너리를 사용한다. 이 딕셔너리는 각 사용자의 세션 ID를 키(key)로, 해당 세션의 대화 이력 객체를 값(value)으로 저장한다.

`if session_id not in store:`: 특정 session_id로 대화 이력이 저장되어 있지 않다면, 새로운 ChatMessageHistory() 객체를 생성하여 딕셔너리에 추가한다.

`return store[session_id]`: 해당 session_id에 해당하는 대화 이력 객체를 반환한다. 이를 통해 사용자가 대화를 이어갈 때 이전 대화 내용을 기억하고 활용할 수 있다.

2. `get_llm(model="gpt-4o")`
이 함수는 언어 모델(LLM) 객체를 생성하는 역할을 한다.

`llm = ChatOpenAI(model=model)`: ChatOpenAI 클래스를 사용하여 OpenAI의 LLM을 초기화한다. 기본 모델은 "gpt-4o"로 설정되어 있다. 이 함수는 다른 모델을 사용하고 싶을 때 모델 이름을 변경하여 쉽게 사용할 수 있도록 돕는다.

3. `get_dictionary_chain()`
이 함수는 사용자의 질문을 사전에 정의된 용어로 변환하는 역할을 한다.

`dictionary = ["사람을 나타내는 표현 -> 거주자"]`: 이 코드는 "사람을 나타내는 표현"을 "거주자"로 바꾸라는 간단한 사전을 정의하고 있다.

`prompt2 = ChatPromptTemplate.from_template(...)`: 이 프롬프트는 LLM에게 사전 내용을 참고하여 질문을 수정하라고 지시한다.

`dictionary_chain = prompt2 | llm | StrOutputParser()`: 이 체인은 prompt2에 사용자의 질문을 넣고, llm이 답변을 생성하도록 한 후, StrOutputParser()를 사용하여 LLM의 답변을 순수한 문자열로 변환한다. 이 과정은 사용자 질문을 RAG 시스템에 더 적합한 형태로 정규화(normalize)하는 역할을 한다.

4. get_retriever()
이 함수는 벡터 데이터베이스에서 관련 문서를 검색하는 역할을 한다.

`embedding = OpenAIEmbeddings(...)`: OpenAI의 임베딩 모델을 사용하여 문서를 벡터(숫자 배열)로 변환하는 객체를 생성한다.

`vectorstore = PineconeVectorStore.from_existing_index(...)`: PineconeVectorStore 클래스를 사용하여 이미 존재하는 tax-index라는 이름의 Pinecone 인덱스에 연결한다.

`retriever = vectorstore.as_retriever(search_kwargs={'k': 3})`: 이 객체는 vectorstore에서 k=3에 해당하는, 즉 가장 관련성이 높은 3개의 문서를 검색하도록 설정한다. 이 retriever는 나중에 다른 체인의 입력으로 사용된다.

5. `get_history_retriever()`
이 함수는 대화 이력을 고려하여 질문을 재구성하는, RAG 시스템의 핵심적인 부분이다.

`contextualize_q_system_prompt`: 이 프롬프트는 LLM에게 대화 이력과 최신 질문을 보고, 대화 이력 없이도 독립적으로 이해될 수 있는 새로운 질문을 만들라고 지시한다. 예를 들어, 사용자가 "그것에 대해 더 알려줘"라고 물었을 때, 이전 대화 맥락을 기반으로 "소득세법에 따른 거주자의 정의에 대해 더 알려줘"와 같이 질문을 재구성한다.

`MessagesPlaceholder("chat_history")`: 이것은 LangChain에서 제공하는 특별한 플레이스홀더이다. `RunnableWithMessageHistory` 체인에 의해 자동으로 대화 이력(history)이 이 자리에 삽입된다. 즉, 개발자가 직접 대화 이력을 프롬프트에 넣을 필요 없이, LangChain이 이 플레이스홀더를 통해 이전 대화를 LLM에게 전달하는 역할을 한다.

`create_history_aware_retriever(...)`: 이 함수는 LLM, retriever, 그리고 위에서 정의한 프롬프트를 결합한다. 이 체인은 먼저 MessagesPlaceholder를 통해 전달된 대화 이력과 사용자의 최신 질문을 사용하여 독립적인 질문을 만든 후, 이 독립적인 질문을 retriever에 전달하여 관련 문서를 검색한다.

6. `get_qa_chain()`
이 함수는 최종 답변을 생성하는, RAG 시스템의 최종 단계이다.

`FewShotChatMessagePromptTemplate`: answer_examples라는 외부 파일에서 가져온 예시들을 사용하여, LLM이 어떤 형식으로 답변해야 할지 학습하도록 돕는다. 이는 LLM이 소득세법 (XX조)에 따르면과 같이 정해진 형식을 지키도록 유도한다.

`system_prompt`: 이 프롬프트는 답변 생성 시 LLM이 따라야 할 규칙을 정의한다. 전문가 역할 부여, 외부 문서 활용 지시, 답변을 모를 경우의 처리 방법, 그리고 답변의 길이와 형식(2~3문장, 소득세법 (XX조)에 따르면) 등 구체적인 지침을 포함한다.

`create_stuff_documents_chain(llm, qa_prompt)`: 이 함수는 검색된 문서들(context)을 프롬프트(qa_prompt)에 채워 넣고 LLM이 최종 답변을 생성하도록 한다. 이 과정을 "stuffing"이라고 부른다.

`create_retrieval_chain(history_aware_retriever, question_answer_chain)`: 이 함수는 history_aware_retriever에서 검색된 문서를 question_answer_chain에 전달하여 답변을 생성하는 전체 RAG 흐름을 구성한다.

`RunnableWithMessageHistory`: 이 체인의 가장 중요한 부분이다. 앞서 설명한 모든 체인을 하나로 묶고, get_session_history 함수를 사용하여 세션별 대화 이력을 자동으로 관리한다. input_messages_key, history_messages_key, output_messages_key는 각 체인의 입력과 출력에서 대화 이력을 어떻게 전달할지 지정하는 키다. 최종적으로 .pick('answer')를 통해 최종 답변만 반환하도록 설정되어 있다.

7. `get_ai_messages(user_message)`
이 함수는 사용자 질문을 받아서 최종 답변을 스트리밍하는 역할을 한다.

`tax_chain`= {"input": dictionary_chain} | qa_chain: 이 부분이 두 개의 체인을 연결한다. dictionary_chain의 결과(output)가 qa_chain의 input으로 들어간다. 즉, 사용자 질문은 먼저 사전 처리를 거친 후, RAG 시스템으로 전달된다.

`tax_chain.stream(...)`: 이 메서드는 최종 답변을 생성하고, 답변이 완성되는 대로 조금씩 stream으로 반환한다. 이는 사용자에게 빠른 응답성을 제공하는 데 유용하다. config를 통해 세션 ID를 설정하여 대화 이력을 관리한다.


### AWS
skillBuilder를 통해서 AWS에 대한 공부를 시작했다.

오늘 배운것은 S3와 EC2 이다. 이를 AWS에서 어떻게 생성하고 사용하는지 간단하게 학습했다.

#### Amazon S3 (Simple Storage Service)
S3는 인터넷 기반의 객체 스토리지 서비스이다. 파일을 '객체' 형태로 저장하며, 파일 시스템의 폴더 구조가 아닌 하나의 넓은 공간에 파일을 저장하는 방식이다. 이를 버켓이라고 한 것 같다. 일반적인 하드 드라이브와 달리 무한에 가까운 확장성을 제공한다.

**주요 특징**

- 무한한 확장성: 저장 용량에 제한이 없으므로, 데이터의 양이 아무리 늘어나도 걱정할 필요가 없다.

- 뛰어난 내구성 및 가용성: 데이터를 여러 시설에 중복으로 저장하여 99.999999999%의 내구성을 제공, 중요한 데이터를 안전하게 보관할 수 있다.

- 다양한 활용: 웹사이트의 이미지, 영상 등 정적 파일을 호스팅하거나, 백업 및 아카이브, 빅데이터 분석을 위한 데이터 레이크(Data Lake) 구축 등 다양한 용도로 사용된다. 정적 웹사이트에서 HTML 등을 넣어놓는데 사용하는 예시로 학습했다.

#### Amazon EC2 (Elastic Compute Cloud)
EC2는 클라우드에서 제공하는 가상 서버이다. 필요한 만큼의 컴퓨팅 자원(CPU, 메모리, 스토리지)을 사용하여 마치 내 컴퓨터를 빌려 쓰는 것처럼 서버를 구축하고 운영할 수 있게한다.

**주요 특징**

- 유연성: 다양한 성능과 가격을 가진 수많은 '인스턴스(instance)' 타입 중에서 원하는 것을 선택하여 사용할 수 있다. 필요에 따라 서버를 빠르게 생성하고 중지하거나, 성능을 조절할 수 있다.

- 확장성: 트래픽이 많을 때 자동으로 서버를 늘리고, 트래픽이 적을 때 다시 줄이는 '오토 스케일링(Auto Scaling)' 기능을 통해 안정적인 서비스 운영이 가능하다.

- 다양한 활용: 웹 애플리케이션이나 모바일 앱의 백엔드 서버, 데이터베이스 서버, 게임 서버 등 프로그램을 실행해야 하는 모든 작업에 활용가능하다.

S3와 EC2는 보통 함께 사용한다. 예를 들어, 웹 애플리케이션을 EC2 인스턴스에 배포하고, 웹사이트의 이미지 파일이나 동영상 같은 정적 콘텐츠는 S3에 저장하여 EC2의 부담을 줄이고 더 효율적으로 서비스를 제공할 수 있다.


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
오늘 수업은 따라가기 어려운 점이 많았다. Streamlit과 RAG, Vector DB 등에서 에러도 많이 발생하여, 환경 설정을 다시 하는 문제도 있었다. 또한 과거 기록을 저장하고, 이를 바탕으로 쿼리를 다시 생성하고 유사도 높은 문서를 찾고, 이를 통해서 LLM 답변을 생성하는 과정이 이해가 쉽지 않았고, 또한 여러 보지 못한 함수가 나와서 어려움이 있었다.
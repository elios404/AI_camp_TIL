---
layout: post
title: "8월 25일 월요일 TIL(RAG, LangChain)"
date: 2025-08-25 18:00:00 +0900
categories: July_week9
---

# 8월 25일 월요일 TIL 작성

## 1. 학습 주제
- RAG
- LangChain


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### RAG

LLM 의 성능을 향상 시키기 위해 외부 지식을 활용하는 기술

질문에 관련된 정보를 검색

데이터는 Vector DB에 있다. 검색된 정보를 LLM의 입력에 추가하여 더 정확하고 관련성 높은 답변을 생성

사용자 쿼리 → 관련 정보 검색(Vector DB에서, 연관성 관련 검색) → 쿼리와 정보 결합 → LLM의 입력 → 정확한 응답 생성

쿼리와 함께 vector db 에 저장된 내용을 찾아서 함께 보내고 답변을 받는다!

### JPA(?)와 mybatis의 차이점?

mybatis는 sql 결과와 어떤 자바 모델과 연관 지을 것인가, 매핑할 것인가를 정함.

JPA는 특정 모델과 딱 붙어서 sql을 작성. 자바 모델과 db의 테이블이 1:1로 매칭됨.

### 3차 과제 (langchain 활용)

```markdown
1. samsung_KR.txt, skHynix_KR.txt 를 임베딩하여 vector DB 만들기
2. context 와 query를 위한 체인 만들기
   1. context를 불러오고 이를 join을 통해서 하나의 문자열로 합치기
   2. query는 RunnablePassthrough 사용하기
3. promptTemplate을 이용해서 위에서 context, query를 입력받아 prompt 완성하기
4. llm 객체를 생성하고 prompt를 input으로 넣기
5. StrOutputParser를 생성하고 LLM 답변을 넣어서 문자열만 반환받기
```

```python
# # 3차 과제

from dotenv import load_dotenv
from glob import glob
from pprint import pprint #json 형식 출력에 편리
import os

load_dotenv()

# 1. 임베딩하고 vector DB 만들기

from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import CharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
from tqdm import tqdm

# 1.1 문서 불러오기
text_files = glob(os.path.join('data','*_kr.txt'))

data = []

for text_file in tqdm(text_files):
    loader = TextLoader(text_file, encoding='utf-8')
    data += loader.load()

# 1.2 적당한 길이의 청크로 나누기
text_splitter = CharacterTextSplitter(
    chunk_size = 250,
    chunk_overlap = 50,
    separator='\n',
)

texts = text_splitter.split_documents(data)

# 1.3 임베딩 모델
embeddings = OpenAIEmbeddings(
    model = 'text-embedding-3-small',
)

# 1.4 vector DB 로 저장하기
vectorstore = Chroma.from_documents(
    documents=texts,
    embedding=embeddings,
    collection_name="chroma_assignment",
    persist_directory='./chroma_assign_db'
)


# 2. context, query 생성
from langchain_core.runnables import RunnablePassthrough

# 2.1 context 생성
retriever = vectorstore.as_retriever(search_kwargs={'k':2})

def format_docs(docs):
    return "\n\n".join([doc.page_content for doc in docs])

retriever_chain = retriever | format_docs

# 2.2 query
# RunnablePassthrough 사용

# 3. prompt 템플릿 만들고 context, query 채우기

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

prompt = ChatPromptTemplate.from_template(template=template)


# 4. LLM 모델 만들기

from langchain_openai import ChatOpenAI

llm = ChatOpenAI( 
    model = 'gpt-4o-mini',
    temperature = 0,
    max_tokens = 250,
)


# 5. Parser 사용하기

from langchain_core.output_parsers import StrOutputParser


# 6. chain 완성하기

chain = (
    {"context" : retriever_chain, "question" : RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

query = "삼성은 어떤 회사이니? 그리고 하이닉스는 어떤 회사이니?"

response = chain.invoke(query)
print(response)

```

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시
01_langchain_exam.ipynb
02_langchain_exam.ipynb
03_assignment.ipynb
03_assignment.py

## 5. 참고자료 / 링크
https://github.com/elios404/LLM_agent_study/tree/main/langchain_work


## 6. 느낀 점 / 회고 
랭체인을 이용해서 쿼리를 보낼 때 vector DB에서 유사도 검사를 해서 필요한 정보를 같이 보내는 방식이 인상깊었고, 활용도가 높다고 생각했다.
또한 미니프로젝트에서 shap 결과를 분석해서 반환할 때 사용할 수 있을 것이라는 생각이 들었다!
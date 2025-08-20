---
layout: post
title: "8월 20일 수요일 TIL(AI-agent-pdfReader-audioAgent)"
date: 2025-08-20 18:00:00 +0900
categories: July_week8
---

# 8월 20일 수요일 TIL 작성

## 1. 학습 주제
- OpenAI API
  - 복습
  - pdf Reader, 요약
  - 음성 -> 텍스트 처리

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### Openai API 복습

```python
from openai import OpenAI

client = OpenAI(*api_key*=api_key)

response = client.chat.completions.create( # gpt에 요청이 발생한다
        model = 'gpt-5-mini',
        messages=messages
    )

```

create를 통해서 요청을 한다.

또한 3가지 role이 있는데 이를 잘 알아야 한다.

- system : 답변하려는 gpt의 역할 설정, 어떤 방향으로 대답을 할 것인지
- user : 유저의 질문
- assistant : gpt의 답변

또한 few-shot으로 몇 개의 user, assistant 예시를 전달하는 것으로 출력 형식이나 방법을 지정할 수 있다.

#### 여러 문자열 작성하는 법

여러 개의 문자열을 여러 줄로 표현해야 할 때 “”” “”” 를 사용하거나, ( ) 소괄호를 하고 그 안에서 쉼표 없이 문자열을 그냥 이어서 작성할 수 있다. 다음은 예시

```python
content = (
    "1)원칙: 민법 제750조는 고의,과실로 타인에게 손해를 가하면 배상 책임을 진다고 규정합니다.\n"
    "2)요건:\n"
    "   - 가해자의 위법한 행위\n"
    "   - 고의 또는 과실\n"
    "   - 손해 발생\n"
    "   - 행위와 손해 사이 인과관계\n"
    "3)적용: 예를 들어 타인의 물건을 부주의로 파손 되었다면, 과실,손해,인과계가 인정되어 손해배상 책임이 발생합니다."
)
```

### PDF 문서 API로 처리하기(전처리, 요약 챗봇 완성)

`pip install PyMuPDF` 를 이용해서 패키지를 가상 환경에 설치

GJ_class/agent_pdfReader 폴더 내 파일 참조

### 과제 : PDF 파일 txt 파일로 변환하고 요약하기

1. 임의의 pdf 선정
2. pdf 파일 전처리
3. pdf → txt 변환
4. txt → summary : prompt 를 잘 작성하기
5. summary.txt → 결과 도출하기

컴퓨터공학 논문 요약하는 코드 작성

시도한 기술 : camelot이라는 라이브러리를 활용해서 논문에 있는 테이블을 테이블 마크다운 형태로 변환해서 입력 텍스트에 추가하기

### 음성을 AI API 로 처리하기

오디오 파일을 바이너리 파일로 open할 때 ‘rb’를 이용해야 한다.

그리고 모델은 ‘whisper-1’을 사용

whisper-1 모델은 영어 음성을 한국어 텍스트로 변환하는 것을 지원하지 않는다.

#### 과제 : 영문 음성을 한글 텍스트로 만들기
```python
audio_file_path = './audio/email_1.mp3'

with open(audio_file_path, 'rb') as audio_file: # 'rb' 바이너리 파일 읽기
    transcription = client.audio.transcriptions.create(
        model = 'whisper-1',
        file = audio_file
    )

transcription_text = transcription.text # 음성 -> 텍스트 답변에서 실제 텍스트 부분만 가져오기

translation = client.chat.completions.create(
    model = 'gpt-5-mini',
    messages= [
        {'role' : 'system', 'content' : '너는 영어를 한국어로 변역해주는 번역 전문가야. 영어 문장을 한국어로 로컬라이징 해서 현지 한국어처럼 번역해줘'},
        {'role' : 'user', 'content' : transcription_text}
    ]
)

print(translation.choices[0].message.content)
```
## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시
GJ_class 폴더 안
- agent_audio
- agent_pdfReader
폴더 안 코드

혹은

https://github.com/elios404/LLM_agent_study
에서 같은 폴더안 코드 확인 가능

## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
api와 모델을 통해서 눈에 보이는 프로그램을 만들어서 여전히 흥미로웠다. 그리고 기본이 중요함을 다시 한 번 느꼈다.
허깅 페이스에서 모델을 설치해서 실행하고자 할 때 복잡하고 새로운 문법을 사용하는게 아니라, 항상 사용하는 pandas 등을 사용하는 것을 보고
기초 내용을 더 정확하게 알고 응용할 수 있는 능력이 중요하다는 것을 느꼈다.
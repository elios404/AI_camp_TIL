---
layout: post
title: "8월 13일 수요일 TIL(Templete)"
date: 2025-08-13 18:00:00 +0900
categories: July_week7
---

# 8월 13일 요일 TIL 작성

## 1. 학습 주제
- 복습
- 검증 셋
- callback
  - 모델 저장하기
  - early Stopping
- 컴퓨터 비전
  - CNN
- 자연어 처리
  - 토큰화
  - 단어 임베딩

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### 어제 내용 복습

- 원핫 인코딩 : 카테코리컬한 요소를 숫자로 표현하기 위해서 사용한다.
- relu : 시그모이드 활성 함수가 역전파에서 기울기 소실 문제가 있어서 새로운 활성 함수가 나옴. relu를 사용하면 기울기 소실문제가 많이 줄어든다.
- softmax :  시그모이드 활성함수는 이진 분류에서 사용하기 좋다. 0~1 사이의 값으로 나온다. 그러나 여러 분류 기준이 있을 때 각각의 분류에 속할 확률을 구하기 위해서 사용하는 것이 softmax 활성함수이다.
- 과적합 : 학습데이터를 과하게 학습하여, 새로운 데이터에 대해서는 오히려 예측이 떨어지는 상황, 일반적인 예측 상황에서는 오히려 예측 성능이 떨어진다. (학습데이터의 몇몇 예외 상황을 과하게 학습한다.)
→ 학습 epochs 가 너무 크거나, 모델의 은닉층 복잡도가 너무 높거나, 학습데이터와 평가(테스트) 데이터가 같을 때 과적합이 발생한다.
→ **그럼 과적합이 일어나는 순간을 어떻게 확인하고 학습을 중단할 것인가?**
- 학습 셋과 테스트 셋 : sklearn의 train_test_split() 함수를 통해서 학습데이터와 실제 모델을 평가하는 데이터를 나누고, 평가 데이터에서 정확도가 떨어지기 시작하면, 과적합이 발생하고 있다는 것이다. (여기서의 테스트 데이터는 엄밀히 말하면 검증 데이터)

### 검증 셋(검증 데이터)

테스트 데이터는 학습 마지막에 최종적으로 1번 모델의 성능을 평가한다.

검증 데이터는 학습 과정에서 1번의 epoch이 끝난 후 모델을 평가 데이터로 평가한다. 이는 과적합이 발생했는지를 확인할 수 있다.

```python
# validation_spilt 을 통해서 25%를 다시 학습에 사용하지 않고, 평가에만 사용
history = model.fit(X_train, y_train, epochs=50, batch_size=500, validation_split=0.25)
```

### callback 을 통해 모델 저장하기

```python
from tensorflow.keras.callbacks import ModelCheckpoint

modelpath = './data/model/all/{epoch:02d}-{val_loss:.4f}.keras'
checkpointer = ModelCheckpoint(filepath=modelpath, verbose=1)

history = model.fit(X_train, y_train, epochs=50, batch_size=500, 
                    validation_split=0.25, verbose=0, callbacks=[checkpointer])
# 사건이나 이벤트가 발생했을 때 호출하는 함수, callback 함수
# 훈련 할 때 마다. checkpointer 가 호출되어서, 모델을 저장한다.
```

### 과적합 해결을 위한 Early Stopping

```python
from tensorflow.keras.callbacks import ModelCheckpoint, EarlyStopping

# 만약 val_loss가 성능이 더이상 좋아지지 않으면 모델 학습을 중지함.
early_stopping_callback = EarlyStopping(monitor='val_loss', patience=20)

# 최상의 성능을 가진 모델을 저장
modelpath = './data/model/wine/all/best_model.keras'
checkpointer = ModelCheckpoint(filepath=modelpath, verbose=0, monitor='val_loss' save_best_only=True)

history = model.fit(X_train, y_train, epochs=2000, batch_size=1000, 
                    validation_split=0.2, verbose=1, 
                    callbacks=[early_stopping_callback,checkpointer])
```

### 컴퓨터 비젼

mnist 데이터는 28*28 형태의 흑백이미지이다. 퍼셉트론을 이용한 Sequential과 Dense를 활용한 모델을 사용할 때 처음 입력은 1차원으로 되어있어야 한다. 따라서 

```python
X_train = X_train.reshape(X_train.shape[0], 784)
```

를 통해서 학습 데이터의 형태를 바꿔준다. X_train은 원래 (60000,28,28)인데 이것을 (60000,784) 의 형태로 바꿔주는 것이다.

### 컴퓨터 비전 CNN

컨볼루션 신경망은 입력된 이미지에서 **특징을 추출하기 위해 커널(슬라이딩 윈도우)** 를 도입하는 기법이다.

kerenl_size를 통해서 커널의 크기를 지정하고, 은닉층이 진행될 때 마다, Conv2D의 커널의 개수를 점점 늘려서 더 구체적인 특성을 파악할 수 있도록 한다.

**맥스 풀링**

특정 구역(필터) 내에서 가장 큰 값을 추출한다. → 이미지의 사이즈를 줄이고 특성을 강조하는 역할을 한다.

**드롭 아웃**

딥러닝 학습에서 과적합을 피하는 것이 중요하다. 따라서 학습하는 과정에서 무작위로 일부 뉴런(노드)들을 일시적으로 비활성화 하는 것이 드롭아웃이다.

**Flatten**

마지막에 softmax 활성함수를 사용하기 전, 위에서 Dense 모델 사용할 때와 같이 이미지를 1차원으로 변환해 주는 것

### 토큰화

토큰 : 입력된 텍스트를, 문장별, 단어별, 형태소 별로 나눈 것.

```python
from tensorflow.keras.preprocessing.text import Tokenizer

text = '해보지 않으면 해낼 수 없다.'

# 토큰화
result = text_to_word_sequence(text) # 띄어쓰기를 기준으로 분리해서 리스트에 담는다.

docs = [
    '먼저 텍스트의 각 단어를 나눠 토큰화 합니다.',
    '텍스트의 단어를 토큰화 해야 딥러닝에 인식됩니다.',
    '토큰화한 결과는 딥러닝에서 사용할 수 있습니다.'
]

token = Tokenizer()
token.fit_on_texts(docs)

print("단어 카운트 : ", token.word_counts)
print("문장 카운트 : ", token.document_count)
print("각 단어가 몇 개의 문장에 포함되어 있는가 : ", token.word_docs)
print("각 단어에 매겨진 인덱스 값 : ", token.word_index)
```

모델은 여러 단어로 구성된 문장을 이해하지 못한다. 따라서 단어별로 토큰을 만들고 그 토큰을 기반으로 모델이 작동한다.

### 단어 임베딩

단어를 토큰화 한 뒤에는 각 단어 별로 빈도수를 확인하거나, 단어가 어디서 왔는지, 단어의 순서, 각 단어들 간의 관계를 확인해야한다. 기본적으로 원 핫 인코딩을 이용할 수 있지만, 텍스트에서 수 많은 단어들을 원핫 인코딩을 하는 것은 벡터가 너무 길어지는 문제가 있다.

단어 임베딩 : 단어간의 유사도를 파악하는 것, 유사도를 파악해서 다차원 공간에 단어를 배치하는 것

학습을 하기 전에 제일 처음으로 단어 임베딩을 진행한다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시

코랩 노트북 파일
- 8월 13일(광인사)-딥러닝, 자연어처리
- ch15 실습파일
- ch15-cnn

## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
컴퓨터비전은 학교에 있을 때 배운 경험이 있어서 어렵지 않았지만, 자연어 처리 부분부터는 어려움이 있었다. 토큰이나 단어 임베딩이라는 개념 자체는 이해하기 어려웠지만, 그것으로 무엇을 할 것인지, 임베딩 과정에서 어떻게 유사도를 측정하여 다차원에 단어를 배치할 것인지가 이해하기 어려웠다.
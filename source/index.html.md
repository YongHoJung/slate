---
title: LG U+ STT Post Correction API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - http
  - python
  - javascript
  
toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction
 
본 문서는 LG U+ STT 후보정 프로젝트에 사용된 API의 스펙과 사용법을 기술한 문서입니다.  
 
본 문서에서 다루고 있는 API 의 종류는
 
* LG U+ NLP API   
LG U+ NLP Engine 테스트 및 결과 출력
* Normalize API   
문장 내의 Noise 제거 및 영 한 치환
* Phoneme Embedding API   
음소 임베딩의 결과 출력
* Error Detection API   
보정 대상 문장 검출 모델
* Error Correction APIs     
  * 날씨 Retrieval API   
  날씨 도메인의 오류 문장을 정답 셋의 유사 발화를 Retrieval 하여 보정   
  * 음악 Retrieval API   
  개체명 오인식에 포커싱하고 부분 보정 방식, 오류 단어를 개체명 사전의 유사 단어를 Retrieval 하여 보정   
  * 음악 Generative API  
  Seq2Seq + Attention 모델을 사용해 Generative 방식을 사용   
  * 전체 치환 Dictionary   
  사전(Dictionary) 기반 보정으로, 사전에 등록된 문장과 정확히 일치하면 보정   


해당 API 에 대해서, 다루고 있는 언어는 JavaScript, Python 입니다. Code example은 오른쪽의 어두운 부분에서 확일할 수 있으며 오른쪽 상단의 탭에서 언어를 바꿀 수 있습니다.

# Authentication

# API

## LG U+ NLP API


```http
 GET /nlp?device={device}&query={query} HTTP/1.1
Accept: application/json
User-Agent: Mozilla/5.0
Host: 13.209.114.137:4242 

 HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
{
    resCode: "20000000",
    resultType: "ANALYSIS",
    errorMessage: "",
    skill: "genie",
    intent: "searchMeta",
    contextFlag: "false",
    isAnswer: false,
    needsListening: false,
    hasMoreAnswer: false,
    hasSlots: false,
    serviceResultcode: "",
    answer: {
        utter: "",
        lang: "ko_KR",
        meta: {}
    },
    filledSlots : {
    artistNm : "볼빨간 사춘기",
    songNm : "Lonely"
    },
    emptySlots: []
}
```

```python
```

```javascript
```

LGU+ NLP Engine. 의도 분석 기능 제공.

* 디바이스별로 지원되는 도메인이 다름. 음악 도메인의 경우, Connected Car 로 디바이스 세팅 필요.
* 개발서버 NLP 엔진을 연결시켜 NER 사전이 올해 초 이후 업데이트 안 되어 있어 결과 분석시 고려 필요

Parameter | Description
------|------
device | LG U+ NLP Device, (Device 별로 NLP 의 도메인이 다름)
query | LG U+ NLP Engine에 입력할 단어


## Phoneme Embedding API


```http
 GET /stt-err-chkr/api/v0.1/phn2vec?q={q}
Accept: application/json
User-Agent: Mozilla/5.0
Host: 13.209.114.137:4242 

 HTTP/1.1 200 O
Content-Type: text/html; charset=utf-8
{
    top1: "사랑의_트위스트",
    elapsedTime: 0.063131513,
    find_ans: false,
    ifs_result: [
        ["사랑의_트위스트", 0.987601101398468, 1]
        ["장나라의_스위트_드림", 0.9399498701095581, 0]
        ["트와이스의_낙낙", 0.9343534708023071, 0]
        ["사랑의_와이파이", 0.9306177496910095, 1]
        ["씨스타의_러빙유", 0.9259741902351379, 0]
        ["박보람의_애쓰지_마요", 0.9230177998542786, 0]
        ...
    ]
    non_deep:[ 
        ["사랑의_트위스트", 0.966131329536438]
        ["사랑의_리퀘스트", 0.7753040790557861]
        ["설운도의_사랑의_트위스트", 0.701108455657959]
        ["아스트로의_사랑이", 0.6840652227401733]
        ["아기사랑_동요사랑_베스트", 0.6460648775100708]
        ["유승우의_서툰_사랑", 0.6335815191268921]
        ...
    ]
}
```

```python
from p2v_snn_music_encoder import config as ifs_config
from p2v_snn_music_encoder import inference as ifs_inference

config_ifs = ifs_config.get_p2vInferSent_config()
inferSent_inference, non_deep_inference = ifs_inference.get_inference_module(config_ifs)

non_deep_result = non_deep_inference( word )
```

```javascript
```

유사 발음을 가진 문자열을 비슷한 벡터로 표현하기 위해 Phoneme(음소) 임베딩을 구현. 두 가지 방식의 Vectorizing 으로 임베딩 구축 진행 

1. 비학습 버전 : g2p converter, p2v converter 사용 
2.  학습 버전 : p2v + Siamese Network 학습 Encoder 사용 

* 데모는 음악 도메인 NER 사전 기반 Phoneme Embedding 으로 학습 버전 Encoder 로 생성함.

#### HTTP Request

Parameter | Description
------|------
q | Phoneme embedding 입력

#### Response 

Parameter | Description
------|------
top1 | classification과 Cosine Similarity를 고려하여 출력
ifs_result | 가장 유사한 topN 개 단어
find_ans   | 정답을 찾았는지에 대한 여부
elapsedTime | 걸린 시간
non_deep | p2v_snn_music_encoder를 거치지 않은 Phoneme Embedding 만으로 유사 N 개 추출 결과

## Error Detection

보정 대상 문장을 검출하는 모델로, CNN 분류 모델로 학습.
도메인별로 학습된 2개의 이진 분류 모델을 이용하여, '날씨 보정 대상, '음악 보정 대상', '그 외' 의 3가지 클래스로 분류

## Error Correction

### 날씨 Retrieval

```http
 GET /stt-err-chkr/api/v0.1/correction/weather?q={q}
Accept: application/json
User-Agent: Mozilla/5.0
Host: 13.209.114.137:4242 

#q : 날씨 보정 모델의 입력값 , ex) 오는 날씨는 어때

```

```python
```

```javascript
```

날씨 도메인의 경우 음악 도메인에 비교해 한정된 패턴을 가지고 있어 Retrieval Model 로 접근 

Siamese Neural Network 을 통해 학습된 Speech Encoder 를 통해 미리 벡터화한 정답셋에서 유사 발화를 Retrieval 하여 보정.

#### HTTP Request

Parameter | Description
------|------
q | 음악 보정 모델의 입력값

#### Response 

Parameter | Description
------|------
top1_result | classification과 Cosine Similarity를 고려하여 출력
elapsedTime | 걸린 시간
find_ans   | 정답을 찾았는지에 대한 여부
ifs_result | 가장 유사한 topN 개 단어
ifs_time | 단어 인코딩 및 유사 벡터 Retrieval 에 소요되는 시간
ptr_result | Pointer Network 를 이용하여 문장 내의 NER 추출
ptr_time | Pointer Network 로 문장 내의 NER 을 추출하는데 소요되는 시간
total_result | 보정 결과 문장

### 음악 Retrieval

```http

  GET /stt-err-chkr/api/v0.1/correction/music?q={q}
Accept: application/json
User-Agent: Mozilla/5.0
Host: 13.209.114.137:4242 

#q : 음악 보정 모델의 입력값, ex) 핸드 클랍 틀어줘

HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
{
    top1_result: "핸드클랩",,
    elapsedTime: 0.0725865364074707,
    find_ans: true,
    ifs_result: [
        ["핸드클랩", 0.9520165324211121, 1], 
        ["헬스클럽", 0.9478693008422852, 1],
        …
    ],
    ifs_time: 0.059693098068237305,
    ptr_result: "핸드_클랍",
    ptr_time: 0.01208639144897461,
    top1_result: "핸드클랩",
    total_result: "핸드클랩_틀어줘"
}
```

```python
```

```javascript
```

음악 도메인의 경우, 가수/노래명/장르 등 개체명이 발화의 많은 부분을 차지하기 때문에, 개체명 오인식에 포커싱하여 부분 보정 방식으로 접근

Pointer Network 을 이용해 Error Span 을 찾아내고, 해당 Span 을 Speech Encoder 로 생성한 Phoneme Embedding 에서 Retrieval 하여 부분 보정

Phoneme Embedding 의 경우 음악 도메인 NER 사전을 이용해 패턴으로 조합하여 구축. 최신 NER 사전이 미반영되어 있어 고려 필요

#### HTTP Request

Parameter | Description
------|------
word | 오류 NER 단어
threshold | 해당 threshold 를 넘는 벡터를 정답으로 출력

#### Response 

Parameter | Description
------|------
ifs_result | 가장 유사한 topN 개 단어
find_ans   | 정답을 찾았는지에 대한 여부
top1 | classification과 Cosine Similarity를 고려하여 출력
threshold | 해당 threshold 를 넘는 벡터를 정답으로 출력


### 음악 Generative

```http

  GET /stt-err-chkr/api/v0.1/correction/music_g?q={q}
Accept: application/json
User-Agent: Mozilla/5.0
Host: 13.209.114.137:4242 

#q : 음악 보정 모델의 입력값, ex) 핸드 클랍 틀어줘

HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
{
    query: "뽀로 뽀로로 틀어줘", 
    corrected: "뽀로로 틀어줘", 
    elapsedTime: 0.03844809532165527
}
```

```python
```

```javascript
```

음악의 경우 발화 패턴 조합이 워낙 다양하여 일반적인 Seq2Seq + Attention 모델을 사용해 Generative 방식 Baseline 성능 검토

성능이 좋지 않음. 학습데이터 품질과 양이 중요. 보정 범위를 Spell Corrector 처럼 좁게 잡는 접근이 필요하다고 판단됨.

#### HTTP Request

Parameter | Description
------|------
q | 음악 보정 모델의 입력값

#### Response 

Parameter | Description
------|------
query | 음악 보정 모델의 입력값
corrected | 음악 보정 모델의 결과값
elapsedTime | 보정 모델의 소요 시간

### 전체 치환 Dictionary

```http
 GET /api/v0.1/correction/dictionary?q={q}
Accept: application/json
User-Agent: Mozilla/5.0
Host: 13.209.114.137:4242 

#q : 전체 치환 사전의 입력값, ex) 핸드 클랍 틀어줘

HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
{
    elapsedTime: 0.0003952980041503906, 
    correctedText: "이 노래 제목이 뭐야"
}
```

```python
```

```javascript
```

사전(Dictionary) 기반 보정으로, 사전에 등록된 문장과 정확히 일치해야 치환 보정됨. 

많이 나오는 오류 패턴들을 등록하여 사용할 수 있고, 운영시 빠른 대응이 필요할때 유용함. 속도가 빠름.

#### HTTP Request

Parameter | Description
------|------
q | 전체 치환 사전의 입력값

#### Response 

Parameter | Description
------|------
correctedText | 사전 치환 결과 문장
elapsedTime | 치환에 소요된 시간
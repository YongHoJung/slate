---
title: LG U+ STT Post Correction API Reference

language_tabs: # must be one of https://git.io/vQNgJ
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
 
* LG U+ NLP   
LG U+ Engine
* Phoneme Embedding  
음소 임베딩
* Error Detection  
보정 대상 문장 검출 모델
* Error Correction   
  * 날씨 Retrieval   
  날씨 도메인의 오류 문장을 정답 셋의 유사 발화를 Retrieval 하여 보정   
  * 음악 Retrieval
  개체명 오인식에 포커싱하고 부분 보정 방식, 오류 단어를 개체명 사전의 유사 단어를 Retrieval 하여 보정   
  * 음악 Generative
  Seq2Seq + Attention 모델을 사용해 Generative 방식을 사용   
  * 전체 치환 Dictionary
  사전(Dictionary) 기반 보정으로, 사전에 등록된 문장과 정확히 일치하면 보정   
* Integrated Simulator   

Detection과 Correction 통합 Simulator

입니다.
해당 API 에 대해서, 다루고 있는 언어는 JavaScript, Python 입니다. Code example은 오른쪽의 어두운 부분에서 확일할 수 있으며 오른쪽 상단의 탭에서 언어를 바꿀 수 있습니다.

# Authentication

# API

## LG U+ NLP

```javascript
```

```python
```
## Phoneme Embedding

```javascript
```

```python
```

## Error Detection

```javascript
```

```python
```

## Error Correction

```javascript
```

```python
```

### 날씨 Retrieval

```javascript
```

```python
```

### 음악 Retrieval

```javascript
```

```python
```

### 음악 Generative

```javascript
```

```python
```

### 전체 치환 Dictionary

```javascript
```

```python
```

## Integrated Simulator

```javascript
```

```python
```

## Get All Kittens

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete


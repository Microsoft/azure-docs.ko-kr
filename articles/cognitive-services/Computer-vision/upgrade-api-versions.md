---
title: Computer Vision API v3.0으로 업그레이드
titleSuffix: Azure Cognitive Services
description: v2.0/v2.1에서 Computer Vision v3.0 읽기 API로 업그레이드하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214183"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>v2.0/v2.1에서 Computer Vision v3.0 읽기 API로 업그레이드

이 가이드에서는 기존 Computer Vision v2.0 또는 v2.1 REST API 코드를 v3.0 읽기 작업으로 업그레이드하는 방법을 보여줍니다. 

## <a name="upgrade-batch-read-file-to-read"></a>`Batch Read File`을 `Read`로 업그레이드


1. `Batch Read File` 2.x의 API 경로를 다음과 같이 변경합니다.


    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    새로운 선택적 매개 변수 _language_를 사용할 수 있습니다. 문서 언어를 모르는 경우 또는 문서 언어가 다국어일 수도 있는 경우에는 이 매개 변수를 포함하지 마세요. 

2. 2\.x에서 `Get Read Results`의 API 경로를 다음과 같이 변경합니다.

    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0**/read/**operations**/{operationId}     |https://{endpoint}/vision/**v3.0**/read/**analyzeResults**/{operationId}|

3. `Get Read Operation Result`의 json 결과를 확인하도록 코드를 변경합니다. `Get Read Operation Result` 호출이 성공하면 JSON 본문에 상태 문자열 필드가 반환됩니다. v2.0의 다음 값은 다른 Cognitive Service REST API에 잘 맞게 변경되었습니다. 
 
    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. `Get Read Operation Result`의 최종 인식 결과 JSON을 해석하도록 코드를 변경합니다. 

    Json이 다음과 같이 변경되는 것에 주의하세요.
    
    - v2.x에서 `"Get Read Operation Result"`는 상태가 `"Succeeded"`일 때 OCR 인식 json을 반환합니다. v3.0에서는 이 필드가 `"succeeded"`입니다.
    - 페이지 배열의 루트를 가져오려면 json 계층을 `"recognitionResults"`에서 `"analyzeResult"`/`"readResults"`로 변경합니다. 페이지 단위 줄 및 단어 json 계층은 변경되지 않으므로 코드를 변경할 필요가 없습니다.
    -   페이지 각도 `"clockwiseOrientation"`의 이름이 `"angle"`로 변경되었으며, 범위는 0~360도에서 -180~180도로 변경되었습니다. 대부분의 수학 함수에서 두 범위를 처리할 수 있으므로 코드에 따라 변경해야 할 수도 있고 그렇지 않을 수도 있습니다.
    -   v3.0 API에는 선택적으로 활용할 수 있는 다음과 같은 향상된 기능이 도입되었습니다. `"createdDateTime"` 및 `"lastUpdatedDateTime"`이 추가되어 처리 기간을 추적할 수 있습니다. 자세한 내용은 설명서를 참조하세요. 
        - `"version"`은 결과를 생성하는 데 사용된 API 버전을 알려줍니다.
        - 단어별 `"confidence"`가 추가되었습니다. 값 0.95가 인식 정확도 95%를 의미하도록 이 값이 보정됩니다. 신뢰도 점수를 사용하여 사람 검토 담당자에게 보낼 텍스트를 선택할 수 있습니다. 
    
    
        2\.X에서 출력 형식은 다음과 같습니다. 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    v3.0에서는 다음과 같이 조정되었습니다.
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```

## <a name="upgrade-from-recognize-text-to-read"></a>`Recognize Text`에서 `Read`로 업그레이드
`Recognize Text`는 *모든 버전의 Computer Vision API에서 사용이 중지되는* *미리 보기* 작업입니다. `Recognize Text`에서 `Read`(v3.0)로 또는 `Batch Read File`(v2.0, v2.1)로 마이그레이션해야 합니다. 개선된 새 텍스트 인식 모델과 추가 기능이 포함되어 있는 v3.0의 `Read`를 사용하는 것이 좋습니다. `Recognize Text`에서 `Read`로 업그레이드하는 방법은 다음과 같습니다.

1. `Recognize Text` v2.x의 API 경로를 다음과 같이 변경합니다.


    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    _mode_ 매개 변수는 `Read`에서 지원되지 않습니다. 필기 텍스트와 인쇄된 텍스트가 모두 자동으로 지원됩니다.
    
    v3.0에서는 새로운 선택적 매개 변수 _language_를 사용할 수 있습니다. 문서 언어를 모르는 경우 또는 문서 언어가 다국어일 수도 있는 경우에는 이 매개 변수를 포함하지 마세요. 

2. `Get Recognize Text Operation Result` v2.x의 API 경로를 다음과 같이 변경합니다.

    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/**v3.0/read/analyzeResults**/{operationId}|

3. `Get Recognize Text Operation Result`의 json 결과를 확인하도록 코드를 변경합니다. `Get Read Operation Result` 호출이 성공하면 JSON 본문에 상태 문자열 필드가 반환됩니다. 
 
    |Recognize Text 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. `Get Recognize Text Operation Result`의 최종 인식 결과 JSON을 해석하도록 코드를 변경하여 `Get Read Operation Result`를 지원합니다.

    Json이 다음과 같이 변경되는 것에 주의하세요.    

    - v2.x에서 `"Get Read Operation Result"`는 상태가 `"Succeeded"`일 때 OCR 인식 json을 반환합니다. v3.0에서는 이 필드가 `"succeeded"`입니다.
    - 페이지 배열의 루트를 가져오려면 json 계층을 `"recognitionResult"`에서 `"analyzeResult"`/`"readResults"`로 변경합니다. 페이지 단위 줄 및 단어 json 계층은 변경되지 않으므로 코드를 변경할 필요가 없습니다.
    -   또한 v3.0 API에는 선택적으로 활용할 수 있는 다음과 같은 향상된 기능이 도입되었습니다. 자세한 내용은 API 참조에서 확인할 수 있습니다. `"createdDateTime"` 및 `"lastUpdatedDateTime"`이 추가되어 처리 기간을 추적할 수 있습니다. 자세한 내용은 설명서를 참조하세요. 
        - `"version"`은 결과를 생성하는 데 사용된 API 버전을 알려줍니다.
        - 단어별 `"confidence"`가 추가되었습니다. 값 0.95가 인식 정확도 95%를 의미하도록 이 값이 보정됩니다. 신뢰도 점수를 사용하여 사람 검토 담당자에게 보낼 텍스트를 선택할 수 있습니다. 
        - 시계 방향 텍스트의 `"angle"` 일반 방향은 (-180, 180]도 사이에서 측정됩니다.
        -  `"width"` 및 `"height"`는 문서 크기를 제공하고, `"unit"`은 이러한 크기(문서 종류에 따라 픽셀 또는 인치)의 단위를 제공합니다.
        - `"page"` 다중 페이지 문서가 지원됩니다.
        - `"language"` 선택적 _language_ 매개 변수의 문서 입력 언어입니다.


    2\.X에서 출력 형식은 다음과 같습니다. 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    v3.0에서는 다음과 같이 조정되었습니다.
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```
    
## <a name="all-other-operations"></a>기타 작업

그 외에는 Computer Vision API v2.X 및 v3.0 사이에 큰 변경 사항이 없습니다. 간단하게 API 경로를 수정하여 `v2.0`을 `v3.0`으로 바꾸면 됩니다.

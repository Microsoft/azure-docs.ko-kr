---
title: OCR (광학 문자 인식)-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API를 사용 하 여 인쇄 및 필기 된 텍스트가 있는 이미지 및 문서의 OCR (광학 문자 인식) 관련 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: cb931d0b9c3dd4d3fa0fa69f69f5f90fc37ea8f6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929196"
---
# <a name="optical-character-recognition-ocr"></a>OCR(광학 문자 인식)

Azure의 Computer Vision API는 이미지에서 인쇄 되거나 필기 된 텍스트를 추출 하는 OCR (광학 문자 인식) 기능을 포함 합니다. 문서 (청구서, 청구서, 재무 보고서, 문서 등) 뿐만 아니라 일련 번호를 포함 하는 라이선스 플레이트 또는 컨테이너와 같은 이미지에서 텍스트를 추출할 수 있습니다. 

## <a name="read-api"></a>읽기 API 

Computer Vision [읽기 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 는 이미지 및 다중 페이지 PDF 문서에서 인쇄 된 텍스트 (여러 언어로), 필기 텍스트 (영어 전용), 숫자 및 통화 기호를 추출 하는 Azure의 최신 OCR 기술입니다. 텍스트를 많이 사용 하는 이미지에서 텍스트를 추출 하 고 혼합 언어를 사용 하 여 다중 페이지 PDF 문서를 추출 하도록 최적화 되어 있습니다. 동일한 이미지나 문서에서 인쇄 된 텍스트와 필기 텍스트를 모두 검색 하는 것을 지원 합니다.

![OCR에서 이미지와 문서를 추출 된 텍스트로 구조화 된 출력으로 변환 하는 방법](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>입력 요구 사항
읽기 API의 **읽기** 작업은 이미지와 문서를 입력으로 사용 합니다. 이러한 요구 사항은 다음과 같습니다.

* 지원 되는 파일 형식: JPEG, PNG, BMP, PDF 및 TIFF
* PDF 및 TIFF의 경우 최대 2000 페이지가 처리 됩니다. 무료 계층 구독자의 경우 처음 두 페이지만 처리 됩니다.
* 파일 크기는 50 MB 미만 이어야 하 고 크기는 50 x 50 픽셀 이상, 최대 1만 x 1만 픽셀 이어야 합니다.
* PDF 차원은 legal 또는 A3 용지 크기에 해당 하는 17 x 17 인치 여야 합니다.

> [!NOTE]
> **언어 입력** 
>
> [읽기 작업](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 에는 언어에 대 한 선택적 요청 매개 변수가 있습니다. 문서에 있는 텍스트의 BCP-47 언어 코드입니다. 읽기는 자동 언어 식별 및 다국어 문서를 지원 하므로 문서를 특정 언어로 강제로 처리 하려는 경우에만 언어 코드를 제공 합니다.

## <a name="the-read-operation"></a>읽기 작업

[읽기 작업](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 은 이미지나 PDF 문서를 입력으로 사용 하 여 비동기적으로 텍스트를 추출 합니다. 호출은 라는 응답 헤더 필드를 사용 하 여를 반환 합니다 `Operation-Location` . `Operation-Location`값은 다음 단계에서 사용할 작업 ID를 포함 하는 URL입니다.

|응답 헤더| 결과 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.0/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-read-results-operation"></a>읽기 결과 가져오기 작업

두 번째 단계는 [읽기 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) 작업을 호출 하는 것입니다. 이 작업은 읽기 작업에서 만든 작업 ID를 입력으로 사용 합니다. 이 메서드는 다음과 같은 가능한 값을 포함 하는 **상태** 필드를 포함 하는 JSON 응답을 반환 합니다. **성공** 값이 반환 될 때까지이 작업을 반복적으로 호출 합니다. 초당 요청 수 (RPS)가 초과 되는 것을 방지 하려면 1 ~ 2 초 간격을 사용 합니다.

|필드| Type | 가능한 값 |
|:-----|:----:|:----|
|상태 | 문자열 | notStarted: 작업이 시작 되지 않았습니다. |
| |  | 실행 중: 작업이 처리 중입니다. |
| |  | 실패: 작업이 실패 했습니다. |
| |  | 성공: 작업이 성공 했습니다. |

> [!NOTE]
> 무료 계층은 요청 률을 분당 20 개의 호출로 제한 합니다. 유료 계층에서는 요청에 따라 증가할 수 있는 RPS (초당 요청 수)를 허용 합니다. Azure 지원 채널 또는 계정 팀을 사용 하 여 RPS (초당 요청 수) 요금을 요청 합니다.

**상태** 필드에 **성공** 값이 있는 경우 JSON 응답은 이미지 또는 문서에서 추출 된 텍스트 콘텐츠를 포함 합니다. JSON 응답은 인식 된 단어의 원래 줄 그룹을 유지 관리 합니다. 추출 된 텍스트 줄과 해당 경계 상자 좌표가 포함 됩니다. 각 텍스트 줄에는 추출 된 모든 단어가 해당 좌표와 신뢰도 점수가 포함 됩니다.

### <a name="sample-json-output"></a>샘플 JSON 출력

성공적인 JSON 응답의 다음 예를 참조 하세요.

```json
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
              }
            ]
          }
        ]
      }
    ]
  }
}
```

C # 및 REST API를 사용 하 여 OCR을 구현 하려면 [인쇄 및 필기 텍스트 추출](./QuickStarts/CSharp-hand-text.md) 빠른 시작을 따르세요.

## <a name="language-support"></a>언어 지원

### <a name="printed-text"></a>인쇄 된 텍스트
[읽기 3.0 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) 는 영어, 스페인어, 독일어, 프랑스어, 이탈리아어, 포르투갈어 및 네덜란드어 언어로 인쇄 된 텍스트를 추출 하는 것을 지원 합니다. 

[읽기 3.1 API 공개 미리 보기](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) 에는 중국어 간체에 대 한 지원이 추가 되었습니다. 시나리오에서 더 많은 언어를 지원 해야 하는 경우에는 [OCR API](#ocr-api) 섹션을 참조 하세요. 

OCR 지원 언어의 전체 목록은 [지원 되는 언어](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 를 참조 하세요.

### <a name="handwritten-text"></a>필기 텍스트
읽기 작업은 현재 필기 텍스트를 영어로 독점적으로 추출할 수 있도록 지원 합니다.

## <a name="integration-options"></a>통합 옵션

### <a name="use-the-rest-api-or-client-sdk"></a>REST API 또는 클라이언트 SDK 사용
[읽기 2.x REST API](./QuickStarts/CSharp-hand-text.md) 는 간편 하 게 통합 되 고 빠르게 생산성을 높일 수 있기 때문에 대부분의 고객에 게 선호 되는 옵션입니다. Azure와 Computer Vision 서비스는 고객의 요구를 충족 하는 동안 규모, 성능, 데이터 보안 및 규정 준수 요구 사항을 처리 합니다.

### <a name="use-containers-for-on-premise-deployment"></a>온-프레미스 배포에 컨테이너 사용
[읽기 2.0 Docker 컨테이너 (미리 보기)](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers) 를 사용 하면 새 OCR 기능을 자체 로컬 환경에 배포할 수 있습니다. 컨테이너는 특정 보안 및 데이터 거버넌스 요구 사항에 적합합니다.

## <a name="read-ocr-examples"></a>OCR 예 읽기

### <a name="text-from-images"></a>이미지의 텍스트

다음 읽기 API 출력은 다른 텍스트 각도, 색 및 글꼴을 사용 하 여 이미지에서 추출 된 텍스트를 보여 줍니다.

![서로 다른 색 및 각도의 여러 단어로 이루어진 이미지 (추출 된 텍스트가 나열 됨)](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>문서의 텍스트

읽기 API는 PDF 문서를 입력으로 사용할 수도 있습니다.

![추출 된 텍스트가 나열 된 청구서 문서](./Images/text-from-pdf-example.png)

### <a name="handwritten-text"></a>필기 텍스트

읽기 작업은 이미지에서 필기 한 텍스트를 추출 합니다 (현재는 영어로만).

![추출 된 텍스트가 나열 된 필기 메모의 이미지](./Images/handwritten-example.png)

### <a name="printed-text"></a>인쇄 된 텍스트

읽기 작업은 인쇄 된 텍스트를 여러 언어로 추출할 수 있습니다.

![추출 된 텍스트가 나열 된 스페인어 교재 이미지](./Images/supported-languages-example.png)

### <a name="mixed-language-documents"></a>혼합 언어 문서

읽기 API는 일반적으로 혼합 언어 문서 라고 하는 여러 다른 언어를 포함 하는 이미지 및 문서를 지원 합니다. 텍스트 콘텐츠를 추출 하기 전에 문서의 각 텍스트 줄을 검색 된 언어로 분류 하는 방식으로 작동 합니다.

![추출 된 텍스트가 나열 된 여러 언어로 된 구의 이미지](./Images/mixed-language-example.png)

## <a name="ocr-api"></a>OCR API

[OCR API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 는 이전 인식 모델을 사용 하 고 이미지만 지원 하며 동기적으로 실행 되어 검색 된 텍스트로 즉시 반환 됩니다. [OCR 지원 언어](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#optical-character-recognition-ocr) 를 확인 한 후 API를 읽습니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 인식 서비스와 마찬가지로 읽기/OCR 서비스를 사용 하는 개발자는 고객 데이터에 대 한 Microsoft 정책을 인식 해야 합니다. 자세한 내용은 [Microsoft 보안 센터](https://www.microsoft.com/trust-center/product-overview) 의 Cognitive Services 페이지를 참조 하세요.

> [!NOTE]
> 컴퓨터 Vison 2.0 RecognizeText 작업은이 문서에서 다루는 새로운 읽기 API를 사용 하 여 더 이상 사용 되지 않습니다. 기존 고객은 [읽기 작업을 사용 하도록 전환](upgrade-api-versions.md)해야 합니다.

## <a name="next-steps"></a>다음 단계

- [읽기 3.0 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005)에 대해 알아봅니다.
- 중국어 간체에 대 한 지원이 추가 된 [REST API 읽기 3.1 공개 미리 보기](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005) 에 대해 알아봅니다.
- [텍스트 추출](./QuickStarts/CSharp-hand-text.md) 빠른 시작을 따라 REST API와 함께 c #, Java, JavaScript 또는 Python을 사용 하 여 OCR을 구현 합니다.

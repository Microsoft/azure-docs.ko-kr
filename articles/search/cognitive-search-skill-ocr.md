---
title: OCR 인식 검색 기술 - Azure Search
description: Azure Search 보강 파이프라인에서 OCR(광학 문자 인식)을 사용하여 이미지 파일에서 텍스트를 추출합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fb7fe0689ce54031880565c0c6409afeab2ff523
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126977"
---
# <a name="ocr-cognitive-skill"></a>OCR 인식 기술

OCR(광학 문자 인식) 기술은 이미지 파일에서 인쇄 및 필기한 텍스트를 인식합니다. 이 기술은 Cognitive Services의 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)에서 제공하는 기계 학습 모델을 사용합니다. **OCR** 기술은 다음 기능에 해당됩니다.

+ textExtractionAlgorithm이 "handwritten"으로 설정된 경우 ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) 기능이 사용됩니다.
+ textExtractionAlgorithm에 "printed"로 설정된 경우 영어 이외의 언어에 대해 ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) 기능이 사용됩니다. 영어의 경우 인쇄된 텍스트에 대해 새 ["텍스트 인식"](../cognitive-services/computer-vision/concept-recognizing-text.md) 기능이 사용됩니다.

**OCR** 기술은 이미지 파일에서 텍스트를 추출합니다. 지원 파일 형식은 다음과 같습니다.

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ .TIFF

> [!NOTE]
> 2018년 12월 21일부터 Azure Search 기술 세트와 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)할 수 있습니다. 이를 통해 Microsoft는 기술 실행 요금을 부과할 수 있습니다. 또한 문서 해독 단계의 일부인 이미지 추출에 대한 요금 청구가 이 날짜에서 시작됩니다. 문서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> [기본 제공 인지 기술](cognitive-search-predefined-skills.md)을 실행하면 직접 작업을 수행한 것과 동일한 요율로 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services)이 부과됩니다. 이미지 추출은 현재 미리 보기 가격으로 Azure Search 요금이 청구됩니다. 자세한 내용은 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400) 또는 [청구 작동 방식](search-sku-tier.md#how-billing-works)을 참조하세요.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| detectOrientation | 이미지 방향의 자동 검색을 사용합니다. <br/> 유효한 값: true / false.|
|defaultLanguageCode | <p>  입력 텍스트의 언어 코드입니다. 지원되는 언어는 다음과 같습니다. <br/> zh-Hans(중국어 간체) <br/> zh-Hant(중국어 번체) <br/>cs(체코어) <br/>da(덴마크어) <br/>nl(네덜란드어) <br/>en(영어) <br/>fi(핀란드어)  <br/>fr(프랑스어) <br/>  de(독일어) <br/>el(그리스어) <br/> hu(헝가리어) <br/> it(이탈리아어) <br/>  ja(일본어) <br/> ko(한국어) <br/> nb(노르웨이어) <br/>   pl(폴란드어) <br/> pt(포르투갈어) <br/>  ru(러시아어) <br/>  es(스페인어) <br/>  sv(스웨덴어) <br/>  tr(터키어) <br/> ar(아랍어) <br/> ro(루마니아어) <br/> sr-Cyrl(세르비아 키릴어) <br/> sr-Latn(세르비아 라틴어) <br/>  sk(슬로바키아어) <br/>  unk(알 수 없음) <br/><br/> 언어 코드가 지정되지 않았거나 null인 경우 언어는 영어로 설정됩니다. 언어가 명시적으로 “unk”로 설정된 경우 언어는 자동으로 검색됩니다. </p> |
| textExtractionAlgorithm | "인쇄" 또는 "필기"입니다. "필기" 텍스트 인식 OCR 알고리즘은 현재 미리 보기 및 영어가 지원됩니다. |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                                          |
|---------------|------------------------------------------------------|
| Image         | 복합 형식입니다. ```imageAction```이 ```none``` 이외의 값으로 설정된 경우 현재 Azure Blob 인덱서에서 생성된 “/document/normalized_images” 필드에만 작동합니다. 자세한 내용은 [샘플](#sample-output)을 참조하세요.|


## <a name="skill-outputs"></a>기술 출력
| 출력 이름     | 설명                   |
|---------------|-------------------------------|
| text          | 이미지에서 추출된 일반 텍스트입니다.   |
| layoutText    | 추출된 텍스트와 텍스트를 찾을 수 있는 위치를 설명하는 복합 유형입니다.|


## <a name="sample-definition"></a>샘플 정의

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>샘플 텍스트와 layoutText 출력

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>샘플: 문서의 콘텐츠와 포함된 이미지에서 추출된 텍스트를 병합합니다.

텍스트 병합기에 대한 일반적인 사용 사례는 이미지의 텍스트 표현(이미지의 캡션 또는 OCR 기술에서의 텍스트)을 문서의 콘텐츠 필드에 병합하는 기능입니다.

다음 기술 세트 예제는 *merged_text* 필드를 만듭니다. 이 필드에는 문서의 텍스트 내용과 해당 문서에 포함된 각 이미지의 OCR 처리된 텍스트가 들어 있습니다.

#### <a name="request-body-syntax"></a>요청 본문 구문
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
위의 기술 집합 예제는 정규화 이미지 필드가 있다고 가정합니다. 이 필드를 생성하려면 인덱서 정의에서 *imageAction* 구성을 아래와 같이 *generateNormalizedImages*로 설정합니다.

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>참고 항목
+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [TextMerger 기술](cognitive-search-skill-textmerger.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱서 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

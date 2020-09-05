---
title: 기술에 Cognitive Services 연결
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인에 Cognitive Services 일대일 구독을 연결 하는 방법에 대 한 지침입니다.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: c9f6a5ebc4f3242181196bd40b62f7522d025b84
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924980"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>Azure의 기술에 Cognitive Services 리소스 연결 Cognitive Search 

Azure Cognitive Search에서 보강 파이프라인을 구성 하는 경우 제한 된 수의 문서를 무료로 보강할 수 있습니다. 더 크고 더 잦은 워크 로드의 경우 청구 가능 Cognitive Services 리소스를 연결 해야 합니다.

이 문서에서는 보강 파이프라인을 정의 하는 기술에 키를 할당 하 여 리소스를 연결 하는 방법에 대해 알아봅니다.

## <a name="resources-used-during-enrichment"></a>보강 중에 사용 되는 리소스

Azure Cognitive Search는 이미지 분석과 OCR (광학 문자 인식), 자연어 처리 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) 및 [텍스트 변환과](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)같은 기타 강화에 대 한 [Computer Vision](https://azure.microsoft.com/services/cognitive-services/computer-vision/) 를 비롯 하 여 Cognitive Services에 대 한 종속성이 있습니다. Azure Cognitive Search의 보강 컨텍스트에서 이러한 AI 알고리즘은 *기술*에 배치 되 고 인덱싱 중에 *인덱서가* 참조 되는 *기술*내에 래핑됩니다.

## <a name="how-billing-works"></a>청구 방법

+ Azure Cognitive Search는 기술에서 제공 하는 Cognitive Services 리소스 키를 사용 하 여 이미지 및 텍스트 보강를 청구 합니다. 청구 가능한 기술 실행은 [Cognitive Services 종 량 제 가격으로 진행](https://azure.microsoft.com/pricing/details/cognitive-services/)됩니다.

+ 이미지 추출은 보강 전에 문서를 깨진 경우 발생 하는 Azure Cognitive Search 작업입니다. 이미지 추출을 청구 가능 합니다. 이미지 추출 가격은 [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)를 참조 하세요.

+ 텍스트 추출은 문서 크랙 구에도 발생 합니다. 청구 되지 않습니다.

+ 조건부, Shaper, 텍스트 병합 및 텍스트 분할 기술을 포함 하 여 Cognitive Services를 호출 하지 않는 기술은 요금이 청구 되지 않습니다.

## <a name="same-region-requirement"></a>동일한 지역 요구 사항

Azure Cognitive Search와 Azure Cognitive Services는 동일한 지역 내에 있어야 합니다. 그렇지 않으면 런타임에이 메시지를 받게 됩니다. `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

여러 지역에서 서비스를 이동할 수 있는 방법은 없습니다. 이 오류가 발생 하는 경우 Azure Cognitive Search와 동일한 지역에 새 Cognitive Services 리소스를 만들어야 합니다.

> [!NOTE]
> 일부 기본 제공 기술은 비 지역별 Cognitive Services (예: [텍스트 번역 기술](cognitive-search-skill-text-translation.md))를 기반으로 합니다. 지역이 아닌 기술을 사용 하는 것은 Azure Cognitive Search 지역이 아닌 다른 지역에서 요청을 처리할 수 있음을 의미 합니다. 비 지역별 서비스에 대 한 자세한 내용은 [지역별 제품 Cognitive Services](https://aka.ms/allinoneregioninfo) 페이지를 참조 하세요.

## <a name="use-free-resources"></a>무료 리소스 사용

제한 된 무료 처리 옵션을 사용 하 여 AI 보강 자습서 및 빠른 시작 연습을 완료할 수 있습니다.

Free (제한 된 강화) 리소스는 인덱서 당 하루 20 개의 문서로 제한 됩니다. 인덱서를 삭제 하 고 다시 만들어 카운터를 다시 설정할 수 있습니다.

1. 데이터 가져오기 마법사를 엽니다.

   ![데이터 가져오기 마법사 열기](media/search-get-started-portal/import-data-cmd.png "데이터 가져오기 마법사 열기")

1. 데이터 원본을 선택 하 고 **AI 보강 (선택 사항)** 를 계속 추가 합니다. 이 마법사의 단계별 연습은 [Azure Portal에서 인덱스 만들기](search-get-started-portal.md)를 참조 하세요.

1. **연결 Cognitive Services** 확장 한 다음 **무료 (제한 된 강화)** 를 선택 합니다.

   ![확장 된 Attach Cognitive Services 섹션](./media/cognitive-search-attach-cognitive-services/attach1.png "확장 된 Attach Cognitive Services 섹션")

1. 이제 **인식 기술 추가**를 포함 하 여 다음 단계를 진행할 수 있습니다.

## <a name="use-billable-resources"></a>유료 리소스 사용

하루에 20 개 이상의 강화을 만드는 작업의 경우 청구 가능 Cognitive Services 리소스를 연결 해야 합니다. Cognitive Services API를 호출 하지 않으려는 경우에도 항상 청구 가능 Cognitive Services 리소스를 연결 하는 것이 좋습니다. 리소스를 연결 하면 일일 한도가 재정의 됩니다.

Cognitive Services API를 호출 하는 기술에 대해서만 요금이 청구 됩니다. [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)또는 [텍스트 병합기](cognitive-search-skill-textmerger.md), [텍스트 분할자](cognitive-search-skill-textsplit.md)및 [shaper](cognitive-search-skill-shaper.md)같은 기술에 대 한 요금이 청구 되지 않으며,이는 API 기반이 아닙니다.

1. 데이터 가져오기 마법사를 열고 데이터 원본을 선택한 다음 **AI 보강 (선택 사항)** 를 계속 추가 합니다.

1. **연결 Cognitive Services** 를 확장 하 고 **새 Cognitive Services 리소스 만들기**를 선택 합니다. 리소스를 만들 수 있도록 새 탭이 열립니다.

   ![Cognitive Services 리소스 만들기](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "Cognitive Services 리소스 만들기")

1. **위치** 목록에서 Azure Cognitive Search 서비스가 있는 지역을 선택 합니다. 성능상의 이유로이 지역을 사용 해야 합니다. 또한이 영역을 사용 하면 지역 간에 아웃 바운드 대역폭 요금이 void 됩니다.

1. **가격 책정 계층** 목록에서 **S0** 를 선택 하 여 Azure Cognitive Search에서 제공 하는 기본 제공 기술을 지 원하는 비전 및 언어 기능을 포함 하 여 Cognitive Services 기능의 전체 컬렉션을 가져옵니다.

   S0 계층의 경우 [Cognitive Services 가격 책정 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/)에서 특정 작업에 대 한 요금을 확인할 수 있습니다.
  
   + **제안 선택** 목록에서 **Cognitive Services** 가 선택 되어 있는지 확인 합니다.
   + **언어** 기능에서 **Text Analytics 표준** 에 대 한 요금은 AI 인덱싱에 적용 됩니다.
   + **비전** 기능에서 **S1 Computer Vision** 에 대 한 요금이 적용 됩니다.

1. **만들기** 를 선택 하 여 새 Cognitive Services 리소스를 프로 비전 합니다.

1. 데이터 가져오기 마법사가 포함 된 이전 탭으로 돌아갑니다. **새로 고침** 을 선택 하 Cognitive Services 리소스를 표시 한 다음 리소스를 선택 합니다.

   ![Cognitive Services 리소스를 선택 합니다.](./media/cognitive-search-attach-cognitive-services/attach2.png "Cognitive Services 리소스를 선택 합니다.")

1. **인식 기술 추가** 섹션을 확장 하 여 데이터에서 실행 하려는 특정 인지 기술을 선택 합니다. 마법사의 나머지 단계를 완료 합니다.

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>Cognitive Services 리소스에 기존 기술 연결

기존 기술이 있으면 새 Cognitive Services 리소스나 다른 Cognitive Services 리소스에 연결할 수 있습니다.

1. **서비스 개요** 페이지에서 **기술력과**를 선택 합니다.

   ![기술력과 탭](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "기술력과 탭")

1. 기술의 이름을 선택한 다음 기존 리소스를 선택 하거나 새 리소스를 만듭니다. **확인**을 선택하여 변경 내용을 확인합니다.

   ![기술 리소스 목록](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "기술 리소스 목록")

   **무료 (제한 된 강화)** 옵션은 매일 20 개의 문서를 제한 하 고 **새 Cognitive Services 리소스 만들기** 를 사용 하 여 새로운 청구 가능 리소스를 프로 비전 할 수 있습니다. 새 리소스를 만든 경우 **새로 고침**을 선택하여 Cognitive Services 리소스 목록을 새로 고친 다음, 리소스를 선택합니다.

## <a name="attach-cognitive-services-programmatically"></a>프로그래밍 방식으로 Cognitive Services 연결

프로그래밍 방식으로 기술을 정의하는 경우 `cognitiveServices` 섹션을 기술에 추가합니다. 이 섹션에는 기술 연결 하려는 Cognitive Services 리소스의 키가 포함 되어 있습니다. 리소스는 Azure Cognitive Search 리소스와 동일한 지역에 있어야 합니다. 또한 `@odata.type`을 포함하고, 이를 `#Microsoft.Azure.Search.CognitiveServicesByKey`로 설정하세요.

다음 예제는 이러한 패턴을 보여줍니다. 정의의 끝 부분에 있는 섹션을 확인 `cognitiveServices` 합니다.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>예: 비용 추정

인지 검색 인덱싱에 관련 된 비용을 예상 하려면 몇 가지 숫자를 실행할 수 있도록 평균 문서 모양을 파악 해야 합니다. 예를 들어 다음을 대략적으로 확인할 수 있습니다.

+ 1000 Pdf.
+ 6 개의 페이지
+ 페이지당 하나의 이미지 (6000 이미지)
+ 페이지당 3000 자

각 PDF의 문서 크랙, 이미지 및 텍스트 추출, 이미지의 OCR (광학 문자 인식) 및 조직의 엔터티 인식으로 구성 된 파이프라인을 가정 합니다.

이 문서에 표시 된 가격은 가상입니다. 예측 프로세스를 설명 하는 데 사용 됩니다. 비용은 낮아질 수 있습니다. 트랜잭션의 실제 가격은 [Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services)을 참조 하세요.

1. 텍스트 및 이미지 콘텐츠로 문서 해독의 경우, 텍스트 추출이 현재 무료입니다. 6000 이미지의 경우 추출 된 모든 1000 이미지에 대해 $1를 가정 합니다. 이 단계의 비용은 $6.00입니다.

2. 영어로 된 6,000개의 이미지 OCR의 경우, OCR 인지 기술은 최적의 알고리즘(DescribeText)을 사용합니다. 분석할 1,000개 이미지당 비용이 $2.50라고 가정할 경우 이 단계를 위해 $15.00를 지불합니다.

3. 엔터티 추출의 경우 페이지당 총 세 개의 텍스트 레코드가 있습니다. 각 레코드는 1,000자입니다. 페이지당 3 개의 텍스트 레코드 6000 페이지를 곱한 값이 18000 텍스트 레코드와 같습니다. 1,000개의 텍스트 레코드당 $2.00라고 가정할 경우 이 단계의 비용은 $36.00입니다.

이를 모두 함께 사용 하면 설명 된 기술을 사용 하 여이 유형의 1000 PDF 문서를 수집 하는 데 $57.00에 대 한 비용을 지불 하 게 됩니다.

## <a name="next-steps"></a>다음 단계
+ [Azure Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
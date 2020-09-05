---
title: 기술 자료 저장소 개념
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 및 다른 애플리케이션에서 보강된 문서를 보고, 변형시키고, 사용할 수 있는 Azure Storage에 해당 문서를 보냅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924302"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure Cognitive Search의 기술 자료 저장소

지식 저장소는 독립적으로 분석하거나 다운스트림을 처리하기 위해 [AI 보강 파이프라인](cognitive-search-concept-intro.md)의 출력을 유지하는 Azure Cognitive Search의 기능입니다. *보강된 문서*는 AI 프로세스를 사용하여 추출, 구조화 및 분석된 콘텐츠에서 만든 파이프라인의 출력입니다. 표준 AI 파이프라인에서 보강된 문서는 일시적이며 인덱싱 중에만 사용된 후에 삭제됩니다. 기술 자료 저장소를 만들도록 선택 하면 보강 문서를 유지할 수 있습니다. 

과거에 인지 검색을 사용한 적이 있으면 *기술 세트*를 사용하여 보강 시퀀스를 통해 문서를 이동시킨다는 것을 이미 알고 있을 것입니다. 결과는 검색 인덱스 또는 기술 자료 저장소의 프로젝션이 될 수 있습니다. 검색 인덱스와 지식 저장소의 두 출력은 동일한 파이프라인의 부산물입니다. 동일한 입력에서 파생되지만 매우 다양한 방식으로 구조화, 저장 및 사용되는 출력이 생성됩니다.

물리적으로 지식 저장소는 Azure Table 스토리지, Azure Blob 스토리지 또는 둘 다인 [Azure Storage](../storage/common/storage-account-overview.md)입니다. Azure Storage에 연결할 수 있는 모든 도구 또는 프로세스는 지식 저장소의 콘텐츠를 사용할 수 있습니다.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![파이프라인의 지식 저장소 다이어그램](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "파이프라인의 지식 저장소 다이어그램")

## <a name="benefits-of-knowledge-store"></a>지식 저장소의 이점

지식 저장소는 BLOB과 같은 비정형 및 준정형 데이터 파일, 분석을 실행한 이미지 파일 또는 새 양식으로 변형된 정형 데이터에서 수집한 구조, 상황 및 실제 콘텐츠를 제공합니다. [단계별 연습](knowledge-store-create-rest.md)에서 밀도가 높은 JSON 문서를 하위 구조로 분할하고, 새 구조로 재구성하고, 다른 방법으로 기계 학습 및 데이터 과학 워크로드와 같은 다운스트림 프로세스에 사용할 수 있도록 만드는 방법을 미리 확인할 수 있습니다.

AI 보강 파이프라인에서 생성할 수 있는 기능을 확인하는 것이 유용하지만 지식 저장소의 진정한 잠재력은 데이터를 변형시키는 능력입니다. 기본 기술 세트로 시작하고, 이를 반복하여 증가하는 수준의 구조를 추가한 다음, Azure Cognitive Search 이외의 다른 앱에서 사용할 수 있는 새 구조로 결합할 수 있습니다.

지식 저장소의 이점을 자세히 열거하면 다음과 같습니다.

+ 보강된 문서를 검색 이외의 [분석 및 보고 도구](#tools-and-apps)에 사용합니다. 파워 쿼리 포함 Power BI는 매력적인 선택이지만 Azure Storage에 연결할 수 있는 모든 도구 또는 앱은 사용자가 만드는 지식 저장소에서 끌어올 수 있습니다.

+ 단계 및 기술 세트 정의를 디버깅하는 동안 AI 인덱싱 파이프라인을 정교화합니다. 지식 저장소는 AI 인덱싱 파이프라인에 있는 기술 세트 정의의 생성물을 보여줍니다. 보강이 어떻게 보이는지 정확히 확인할 수 있으므로 해당 결과를 사용하여 더 나은 기술 세트를 디자인할 수 있습니다. Azure Storage의 [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)를 사용하여 지식 저장소의 콘텐츠를 볼 수 있습니다.

+ 데이터를 새 양식으로 변형합니다. 변형은 기술 세트에 포함되지만 중요한 점은 이제 기술 세트가 이 기능을 제공할 수 있다는 것입니다. Azure Cognitive Search의 [쉐이퍼 기술](cognitive-search-skill-shaper.md)은 이 작업에 맞게 확장되었습니다. 변형을 사용하면 관계를 유지하면서 데이터의 사용 목적에 맞는 프로젝션을 정의할 수 있습니다.

> [!Note]
> AI 보강 및 인식 기술을 처음 접하나요? Azure Cognitive Search는 Cognitive Services 비전 및 언어 기능과 통합되어 이미지 파일, 엔터티 인식 및 텍스트 파일에서 핵심 구 추출 등에 OCR(광학 문자 인식)을 사용하여 원본 데이터를 추출하고 보강합니다. 자세한 내용은 [Azure Cognitive Search의 AI 보강](cognitive-search-concept-intro.md)을 참조하세요.

## <a name="physical-storage"></a>물리적 스토리지


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


지식 저장소의 실제 식은 기술 세트에 있는 `knowledgeStore` 정의의 `projections` 요소를 통해 명시됩니다. 프로젝션은 의도된 용도와 일치하도록 출력의 구조를 정의합니다.

프로젝션은 테이블, 개체 또는 파일로 명시될 수 있습니다.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

이 구조에서 지정하는 프로젝션의 형식에 따라 지식 저장소에서 사용하는 스토리지 형식이 결정됩니다.

+ 테이블 스토리지는 `tables`를 정의할 때 사용됩니다. 분석 도구에 입력하기 위해 테이블 형식 보고 구조가 필요하거나 다른 데이터 저장소로 데이터 프레임을 내보낼 경우 테이블 프로젝션을 정의합니다. 여러 `tables`를 지정하여 보강 문서의 일부 또는 단면을 가져올 수 있습니다. 동일한 프로젝션 그룹 내에서 테이블 관계를 사용할 수 있게 보존됩니다.

+ Blob 스토리지는 `objects` 또는 `files`를 정의할 때 사용됩니다. `object`의 물리적 표현은 보강 문서를 나타내는 계층적 JSON 구조입니다. `file`은 문서에서 추출되어 Blob 스토리지로 그대로 전송되는 이미지입니다.

단일 프로젝션 개체는 `tables`, `objects`, `files`의 단일 세트를 포함하며 많은 시나리오에서는 하나의 프로젝션을 만드는 것으로도 충분할 수 있습니다. 

그러나 여러 `table`-`object`-`file` 프로젝션 세트를 만들 수 있으며 다른 데이터 관계를 원하는 경우 이 작업을 수행할 수 있습니다. 세트 내에서 데이터는 관련되어 있으며, 이러한 관계가 존재하며 검색될 수 있다고 가정합니다. 추가 세트를 만드는 경우 각 그룹의 문서는 서로 관련이 없습니다. 여러 프로젝션 그룹을 사용하는 예는 동일한 데이터를 온라인 시스템에서 사용하기 위해 프로젝션하고 특정 방식으로 표시해야 하는 경우 다르게 표시되는 데이터 과학 파이프라인에서 사용하기 위해 동일한 데이터를 프로젝션할 때입니다.

## <a name="requirements"></a>요구 사항 

[Azure Storage](../storage/index.yml)가 필요합니다. 물리적 스토리지를 제공합니다. Blob 스토리지나 Table Storage 또는 둘 다를 사용할 수 있습니다. Blob 스토리지는 일반적으로 출력이 다운스트림 프로세스로 이동될 때 그대로 유지되는 보강 문서에 사용됩니다. Table Storage는 분석 및 보고에 일반적으로 사용되는 보강 문서 조각을 위한 기능입니다.

[기술 세트](cognitive-search-working-with-skillsets.md)가 필요합니다. `knowledgeStore` 정의를 포함하고 보강 문서의 구조와 컴퍼지션을 결정합니다. 빈 기술 세트를 사용하여 지식 저장소를 만들 수는 없습니다. 기술 세트에는 최소 하나 이상의 기술이 있어야 합니다.

[인덱서](search-indexer-overview.md)가 필요합니다. 기술 세트는 실행을 구동하는 인덱서에 의해 호출됩니다. 인덱서는 고유한 요구 사항 및 특성 세트와 함께 제공됩니다. 이러한 특성 중 일부는 지식 저장소에 직접적인 영향을 미칩니다.

+ 인덱서에는 [지원되는 Azure 데이터 원본](search-indexer-overview.md#supported-data-sources)이 필요합니다(궁극적으로 Azure의 지원되는 원본에서 데이터를 가져와 지식 저장소를 만드는 파이프라인이 시작됨). 

+ 인덱서에는 검색 인덱스가 필요합니다. 인덱서를 사용하지 않으려는 경우에도 인덱스 스키마를 제공해야 합니다. 최소 인덱스에는 키로 지정된 하나의 문자열 필드가 있습니다.

+ 인덱서는 대상 필드로 원본 필드를 앨리어싱하는 데 사용되는 선택적 필드 매핑을 제공합니다. 기본 필드 매핑을 수정해야 하는 경우(다른 이름 또는 형식 사용) 인덱서 내에서[필드 매핑](search-indexer-field-mappings.md)을 만들 수 있습니다. 지식 저장소 출력의 경우 대상은 Blob 개체 또는 테이블의 필드일 수 있습니다.

+ 인덱서에는 일정이 있으며 다양한 데이터 원본에서 제공하는 변경 검색 메커니즘과 같은 기타 속성을 지식 저장소에 적용할 수도 있습니다. 예를 들어 정기적으로 보강을 [예약](search-howto-schedule-indexers.md)하여 콘텐츠를 새로 고칠 수 있습니다. 

## <a name="how-to-create-a-knowledge-store"></a>지식 저장소를 만드는 방법

기술 자료 저장소를 만들려면 포털 또는 REST API ()를 사용 합니다 `api-version=2020-06-30` .

### <a name="use-the-azure-portal"></a>Azure Portal 사용

**데이터 가져오기** 마법사에는 지식 저장소를 만드는 옵션이 포함되어 있습니다. 초기 탐색을 위해 [4단계에 걸쳐 첫 번째 정보 저장소를 만듭니다](knowledge-store-connect-power-bi.md).

1. 지원되는 데이터 원본을 선택합니다.

1. 보강 지정: 리소스를 연결하고, 기술을 선택하고, 지식 저장소를 지정합니다. 

1. 인덱스 스키마를 만듭니다. 마법사에는 이러한 스키마가 필요하며 사용자를 위해 유추할 수 있습니다.

1. 마법사를 실행합니다. 이 마지막 단계에서 추출, 보강 및 저장이 수행됩니다.

### <a name="use-create-skillset-rest-api"></a>Create 기술 (REST API) 사용

`knowledgeStore`는 [기술 세트](cognitive-search-working-with-skillsets.md) 내에 정의되며, [인덱서](search-indexer-overview.md)에서 호출됩니다. Azure Cognitive Search는 보강하는 동안 공간을 Azure Storage 계정에 만들고, 구성에 따라 보강된 문서를 Blob 또는 테이블로 프로젝션합니다.

REST API는 프로그래밍 방식으로 기술 자료 저장소를 만들 수 있는 메커니즘 중 하나입니다. 탐색하는 쉬운 방법은 [Postman 및 REST API를 사용하여 첫 번째 정보 저장소를 만드는 것](knowledge-store-create-rest.md)입니다.

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>도구 및 앱과 연결하는 방법

보강이 스토리지에 존재하면 Azure BLOB 또는 테이블 스토리지에 연결하는 도구 또는 기술을 사용하여 콘텐츠를 검색, 분석 또는 사용할 수 있습니다. 다음 목록으로 시작합니다.

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) - 보강된 문서 구조 및 콘텐츠를 봅니다. 이 도구를 지식 저장소 콘텐츠를 보기 위한 기준선 도구로 고려합니다.

+ 보고 및 분석을 위한 [Power BI](knowledge-store-connect-power-bi.md) 

+ [Azure Data Factory](../data-factory/index.yml) - 추가 조작을 위한 도구입니다.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API 참조

REST API 버전 `2020-06-30`에서는 기술 세트에 대한 추가 정의를 통해 지식 저장소를 제공합니다. API를 호출하는 방법에 대한 자세한 내용은 이 참조 외에 [Postman Man을 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)를 참조하세요.

+ [Create 기술 (api-version = 2020-06-30)](/rest/api/searchservice/create-skillset)
+ [기술 업데이트 (api-version = 2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>다음 단계

지식 저장소는 보강된 문서를 지속적으로 유지하므로 기술 세트를 디자인하거나 Azure Storage 계정에 액세스할 수 있는 모든 클라이언트 애플리케이션에서 사용할 새 구조와 콘텐츠를 만들 때 유용합니다.

보강 문서를 만드는 가장 간단한 방법은 [포털](knowledge-store-create-portal.md)을 사용 하는 것 이지만, 개체를 만들고 참조 하는 방법에 대 한 통찰력을 원하는 경우에는 postman과 REST API를 사용할 수도 있습니다.

> [!div class="nextstepaction"]
> [Postman 및 REST를 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)

프로젝션에 대한 자세한 내용, 기능 및 [기술 세트에서 정의](knowledge-store-projection-overview.md)하는 방법 알아보기

> [!div class="nextstepaction"]
> [지식 저장소의 프로젝션](knowledge-store-projection-overview.md)

조각화, 인라인 셰이핑 및 관계와 같은 고급 투영 개념에 대한 자습서에서는 [지식 저장소의 프로젝션 정의](knowledge-store-projections-examples.md)를 시작합니다.

> [!div class="nextstepaction"]
> [지식 저장소에서 프로젝션 정의](knowledge-store-projections-examples.md)
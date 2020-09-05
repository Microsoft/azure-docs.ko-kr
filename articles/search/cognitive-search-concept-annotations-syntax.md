---
title: 기술력과의 참조 입력 및 출력
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인에서 기술의 입력 및 출력에 있는 주석을 참조 하는 방법 및 주석 구문에 대해 설명 합니다.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935537"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Azure Cognitive Search 기술에서 주석을 참조 하는 방법

이 문서에서는 다양한 시나리오를 설명하는 예제를 사용하여 기술 정의에서 주석을 참조하는 방법을 알아 봅니다. 문서의 콘텐츠는 기술 집합을 통해 흐르기 때문에 주석으로 보강됩니다. 주석은 다운스트림 추가 보강을 위한 입력으로 사용되거나 인덱스에서 출력 필드에 매핑될 수 있습니다. 
 
이 문서의 예제는 문서 해독 단계의 일부로서 [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)에서 자동으로 생성한 *콘텐츠* 필드에 기반을 둡니다. Blob 컨테이너에서 문서를 참조할 때는 *콘텐츠* 필드가 *문서*의 일부인 `"/document/content"`과 같은 형식을 사용합니다. 

## <a name="background-concepts"></a>배경 개념

구문을 검토를 하기 전에 이 문서의 뒷부분에 제공되는 예제를 보다 잘 이해하려면 몇 가지 중요한 개념을 다시 확인하겠습니다.

| 용어 | Description |
|------|-------------|
| 보강된 문서 | 보강된 문서는 문서와 관련된 모든 주석을 유지하기 위해 파이프라인에서 만들고 사용하는 내부 구조입니다. 보강된 문서를 주석의 트리라고 생각합니다. 일반적으로 이전 주석에서 만든 주석은 자식 주석이 됩니다.<p/>보강된 문서는 기술 집항 실행 동안에만 존재합니다. 콘텐츠가 검색 인덱스에 매핑되면 보강된 문서는 더 이상 필요 없습니다. 보강된 문서와 직접 상호 작용하지 않더라도 기술 집합을 만들 경우 문서의 심적 모델을 보유하는 것이 유용합니다. |
| 보강 컨텍스트 | 요소가 보강된 점에서 보강이 일어난 컨텍스트입니다. 기본적으로 보강 컨텍스트는 `"/document"` 수준에서 개별 문서에 범위가 지정됩니다. 기술을 실행할 때 해당 기술의 출력은 [정의된 컨텍스트의 속성](#example-2)이 됩니다.|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>예제 1: 단순 주석 참조

Azure Blob storage에서 엔터티 인식을 사용 하 여 추출할 사용자의 이름에 대 한 참조를 포함 하는 다양 한 파일이 있다고 가정 합니다. 아래의 기술 정의에서 `"/document/content"`은 전체 문서의 텍스트 표현이며 "사람"은 사용자로 식별되는 엔터티에 대한 전체 이름의 추출입니다.

기본 컨텍스트가 `"/document"`이므로 사람의 목록은 이제 `"/document/people"`로서 참조될 수 있습니다. 특정한 경우에 `"/document/people"`은 인덱스의 필드에 매핑되거나 동일 기술 집합의 다른 기술에 사용될 수 있는 주석입니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>예제 2: 문서 내에서 배열을 참조

이 예제는 동일 문서에 대해 보강 단계를 여러 번 호출하는 방법을 보여주며 이전 예제를 기반으로 빌드합니다. 이전 예제가 단일 문서에서 10명의 이름을 통해 문자열 배열을 생성했다고 가정합니다. 합리적인 다음 단계는 전체 이름에서 성을 추출하는 두 번째 보강일 수 있습니다. 10개 이름이 있으므로 이 문서에서 이 단계를 한 명당 한 번씩 10번을 호출하고자 합니다. 

올바른 반복 횟수를 호출하려면 컨텍스트를 `"/document/people/*"`로 설정합니다. 여기서 별표(`"*"`)는 `"/document/people"`의 하위 항목으로 보강된 문서의 모든 노드를 나타냅니다. 기술 배열에서 이 기술을 한 번만 정의한다고 해도 모든 구성원이 처리될 때까지 문서 내의 각 구성원을 호출합니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

주석이 문자열의 컬렉션이나 배열인 경우 전체로서의 배열이 아닌 구체적 구성원을 대상으로 할 수 있습니다. 위의 예제에서는 컨텍스트가 나타내는 각 노드에서 `"last"`이라는 주석을 생성합니다. 주석군을 참조하려는 경우 구문 `"/document/people/*/last"`을 사용할 수 있습니다. 특정 주석을 참조하려는 경우 명시적 인덱스를 사용할 수 있습니다: 문서에서 식별된 첫 번째 사용자의 성을 참조하기 위한 `"/document/people/1/last`". 이 구문에서 배열은 "1 인덱싱됨"입니다.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>예제 3: 배열 내에서 구성원 참조

경우에 따라 특정 기술에 전달하는 특정 형식의 모든 주석을 그룹화해야 합니다. 예제 2에서 추출된 모든 성 가운데 가장 일반적인 성을 식별하는 가상의 사용자 지정 기술을 고려합니다. 사용자 지정 기술에 성만 제공하려면 컨텍스트를 `"/document"`로, 입력을 `"/document/people/*/lastname"`로 지정합니다.

의 카디널리티는 `"/document/people/*/lastname"` 문서 보다 큽니다. 이 문서에 대한 문서 노드가 하나만 있는 반면 성 노드는 10개가 있을 수 있습니다. 이 경우 시스템은 문서의 모든 요소를 포함하는 `"/document/people/*/lastname"`의 배열을 자동으로 만듭니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>참조
+ [사용자 지정 기술을 보강 파이프라인으로 통합하는 방법](cognitive-search-custom-skill-interface.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기술 집합 만들기(REST)](/rest/api/searchservice/create-skillset)
+ [인덱스에 보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
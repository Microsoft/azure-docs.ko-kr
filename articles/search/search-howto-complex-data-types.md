---
title: 복합 데이터 형식을 모델링하는 방법 - Azure Search
description: 일반 행 집합 및 컬렉션 데이터 형식을 사용하여 Azure Search 인덱스에서 중첩된 데이터 또는 계층적 데이터 구조를 모델링할 수 있습니다.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076211"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Azure Search에서 복합 데이터 형식을 모델링하는 방법
Azure Search 인덱스를 채우는 데 사용되는 외부 데이터 세트에는 가끔 테이블 형식의 행 집합으로 깔끔하게 분류되지 않는 계층적 또는 중첩된 하위 구조가 포함됩니다. 이러한 구조의 예에는 한 고객에 대한 여러 위치와 전화 번호, 단일 SKU에 대한 여러 색과 크기, 한 권의 책에 대한 여러 저자 등이 포함될 수 있습니다. 모델링 용어에서는 이러한 구조를 *복합 데이터 형식*, *복합형 데이터 형식*, *복합성 데이터 형식* 또는 *집계 데이터 형식*이라고도 합니다.

복합 데이터 형식은 기본적으로 Azure Search에서 지원되지 않지만 입증된 해결책에 구조를 평면화하고 **컬렉션** 데이터 형식을 사용하여 내부 구조를 다시 구성하는 2단계 프로세스가 포함됩니다. 이 문서에 설명된 기술을 사용하면 콘텐츠를 검색, 패싯, 필터링 및 정렬할 수 있습니다.

## <a name="example-of-a-complex-data-structure"></a>복합 데이터 구조의 예
일반적으로 이러한 데이터는 JSON 또는 XML 문서 집합으로 또는 Azure Cosmos DB 등의 NoSQL 저장소에 있는 항목으로 상주합니다. 구조적으로, 여러 하위 항목을 검색 및 필터링해야 하므로 문제가 발생합니다.  해결 방법을 설명할 때 일련의 연락처를 예로 나열하는 다음 JSON 문서를 시작점으로 사용합니다.

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

‘id’, ‘name’ 및 ‘company’라는 이름의 필드를 Azure Search 인덱스 내의 일대일 필드로 쉽게 매핑할 수 있습니다. ‘locations’ 필드에는 위치 ID 집합과 위치 설명이 있는 위치 배열이 포함됩니다. Azure Search에 이를 지원하는 데이터 형식이 없는 경우 다른 방법으로 Azure Search에서 모델링해야 합니다. 

> [!NOTE]
> 이 기술은 Kirk Evans의 블로그 게시물 [Indexing Azure Cosmos DB with Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/)(Azure Search를 사용하여 Azure Cosmos DB 인덱싱)에도 설명되어 있습니다. 여기에서 [컬렉션](https://msdn.microsoft.com/library/azure/dn798938.aspx)(또는 문자열 배열)인 `locationsID` 및 `locationsDescription`라는 필드가 있는 “데이터 평면화”라는 기술을 보여 줍니다.   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>1부: 배열을 개별 필드로 평면화
이 데이터 세트를 제공하는 Azure Search 인덱스를 만들려면 중첩된 하위 구조에 대한 개별 필드 `locationsID` 및 `locationsDescription`을 [컬렉션](https://msdn.microsoft.com/library/azure/dn798938.aspx)(또는 문자열 배열)의 데이터 형식과 함께 만듭니다. 이러한 필드에서 ‘1’ 및 ‘2’ 값을 John Smith의 `locationsID` 필드로, ‘3’ & ‘4’ 값을 Jen Campbell의 `locationsID` 필드로 인덱싱합니다.  

Azure Search의 데이터는 다음과 같습니다. 

![샘플 데이터, 2행](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>2부: 인덱스 정의에 컬렉션 필드 추가
인덱스 스키마에서 필드 정의는 이 예제와 비슷합니다.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>검색 동작의 유효성 검사 및 인덱스 확장(선택 사항)
인덱스를 만들고 데이터를 로드한 후에는 해결 방법을 테스트하여 데이터 세트에 대한 검색 쿼리 실행을 확인합니다. 각 **컬렉션** 필드는 **검색 가능**해야 하고, **필터링 가능**해야 하며 **패싯 가능**해야 합니다. 다음과 같이 쿼리를 실행할 수 있습니다.

* ‘Adventureworks Headquarters’에서 근무하는 모든 사용자를 찾습니다.
* ‘Home Office’에서 근무하는 사용자 수를 계산합니다.  
* ‘Home Office’에서 근무하는 사용자 중 이들이 근무하는 다른 사무실과 각 위치의 사용자 수를 표시합니다.  

이 기술은 위치 ID와 위치 설명을 함께 사용하여 검색을 수행해야 할 때 유용합니다. 예를 들면 다음과 같습니다.

* Home Office가 있고 위치 ID가 4인 사용자를 모두 찾습니다.  

원본 콘텐츠를 회수하면 다음과 같은 모양이 됩니다.

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

그러나 이제 데이터가 개별 필드로 분류되어 Jen Campbell에 대한 Home Office가 `locationsID 3` 또는 `locationsID 4`에 연결된 것을 알 수가 없습니다.  

이러한 경우 인덱스에서 모든 데이터를 단일 컬렉션으로 결합한 다른 필드를 정의합니다.  이 예제에서는 이 필드를 `locationsCombined`라고 하고 `||`를 사용하여 콘텐츠를 분리합니다. 이때 콘텐츠에 고유한 문자 집합이라고 생각되는 다른 구분 기호를 선택할 수 있습니다. 예를 들면 다음과 같습니다. 

![샘플 데이터, 구분 기호가 있는 2행](./media/search-howto-complex-data-types/sample-data-2.png)

이 `locationsCombined` 필드를 사용하여 다음과 같이 더 많은 쿼리를 사용할 수도 있습니다.

* ‘Home Office’에서 근무하며 위치 ID가 ‘4’인 사용자 수를 표시합니다.  
* ‘Home Office’에서 근무하며 위치 ID가 ‘4’인 사용자를 검색합니다. 

## <a name="limitations"></a>제한 사항
이 기술은 여러 시나리오에서 유용하지만 모든 경우에 적용되지는 않습니다.  예를 들면 다음과 같습니다.

1. 복합 데이터 형식에 정적 필드 집합이 없고 가능성 있는 모든 형식을 단일 필드에 매핑할 수 없는 경우 
2. 중첩된 개체를 업데이트하는 데 Azure Search 인덱스에서 업데이트해야 할 항목을 정확하게 결정하기 위해 추가 작업이 필요한 경우

## <a name="sample-code"></a>샘플 코드
복합 JSON 데이터 세트를 Azure Search로 인덱싱하고 이 [GitHub 리포지토리](https://github.com/liamca/AzureSearchComplexTypes)에서 이 데이터 세트에 대해 여러 가지 쿼리를 수행하는 방법에 대한 예를 볼 수 있습니다.

## <a name="next-step"></a>다음 단계
Azure Search UserVoice에서 [복합 데이터 형식의 기본 지원에 대해 응답](https://feedback.azure.com/forums/263029-azure-search) 하고 기능 구현과 관련하여 고려해야 할 추가 입력을 제공합니다. Twitter를 통해 @liamca에 직접 연락할 수도 있습니다.


---
title: '예제: 다중 수준 패싯 - Azure Search'
description: 다중 수준 분류를 위한 패싯 구조를 빌드하여 애플리케이션 페이지에 포함할 수 있는 중첩 탐색 구조를 만드는 방법을 알아봅니다.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 7fa17528931be40109d81edac0f15a6a6822ec01
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291761"
---
# <a name="example-multi-level-facets-in-azure-search"></a>예제: Azure Search의 다중 수준 패싯

Azure Search 스키마는 다중 수준 분류 범주를 명시적으로 지원하지는 않지만, 인덱싱 전에 콘텐츠를 조작하여 근사치를 구한 다음, 결과에 특수 처리를 적용할 수 있습니다. 

## <a name="start-with-the-data"></a>데이터 시작

이 문서의 예제는 이전 예제 [AdventureWorks Inventory 데이터베이스 모델링](search-example-adventureworks-modeling.md)을 기반으로 Azure Search의 다중 수준 패싯을 보여줍니다.

AdventureWorks는 부모-자식 관계의 간단한 2수준 분류를 제공합니다. 이 구조의 고정 길이 분류 깊이의 경우 간단한 SQL 조인 쿼리를 사용하여 분류를 그룹화할 수 있습니다.

```T-SQL
SELECT 
  parent.Name Parent, category.Name Category, category.ProductCategoryId
FROM 
  SalesLT.ProductCategory category
LEFT JOIN
  SalesLT.ProductCategory parent
  ON category.ParentProductCategoryId=parent.ProductCategoryId
```

  ![쿼리 결과](./media/search-example-adventureworks/prod-query-results.png "쿼리 결과")

## <a name="indexing-to-a-collection-field"></a>컬렉션 필드에 인덱싱

이 구조를 포함하는 인덱스에서 이 데이터를 저장할 Azure Search 스키마에 **Collection(Edm.String)** 필드를 만들고, 필드 특성에 searchable, filterable, facetable 및 retrievable을 포함합니다.

이제 특정 분류 범주를 참조하는 콘텐츠를 인덱싱할 때 각 분류 수준의 텍스트를 포함하는 배열로 분류를 제출합니다. 예를 들어 `ProductCategoryId = 5 (Mountain Bikes)`인 엔터티는 필드를 `[ "Bikes", "Bikes|Mountain Bikes"]`로 제출합니다.

부모 범주 "자전거"는 하위 범주 값 "산악 자전거"에 포함됩니다. 각 하위 범주는 전체 경로를 루트 요소의 상대 경로로 포함해야 합니다. 파이프 문자 구분 기호는 임의로 결정되지만, 일관적이어야 하고 원본 텍스트에 나타나지 않아야 합니다. 구분 기호 문자는 패싯 결과에서 분류 트리를 다시 생성하기 위해 애플리케이션 코드에 사용됩니다.

## <a name="construct-the-query"></a>쿼리 작성

쿼리를 실행할 때 패싯 사양 `facet = taxonomy,count:50,sort:value`를 포함해야 합니다(여기서 분류는 패싯 가능한 분류 필드).

count 값은 가능한 모든 분류 값을 반환할 수 있도록 충분히 높아야 합니다. AdventureWorks 데이터는 고유한 분류 값 41개를 포함하므로 `count:50`이면 충분합니다.

  ![패싯 필터](./media/search-example-adventureworks/facet-filter.png "패싯 필터")

## <a name="build-the-structure-in-client-code"></a>클라이언트 코드에서 구조 빌드

클라이언트 애플리케이션 코드에서 파이프 문자의 각 패싯 값을 분할하여 분류 트리를 다시 생성합니다.

```javascript
var sum = 0
  , categories = {children:{},length:0}
  , results = getSearchResults();
separator = separator || '|';
field = field || 'taxonomy';
results['@search.facets'][field].forEach(function(d) {
  var node = categories;
  var parts = d.value.split(separator);
  sum += d.count;
  parts.forEach(function(c, i) {
    if (!_(node.children).has(c)) {
      node.children[c] = {};
      node.children[c].count = d.count;
      node.children[c].children = {};
      node.children[c].length = 0;
      node.children[c].filter = parts.slice(0,i+1).join(separator);
      node.length++;
    }
    node = node.children[c];
  });
});
categories.count = sum;
```

이제 **범주** 개체를 사용하여 축소 가능한 분류 트리를 정확한 카운트 값으로 렌더링할 수 있습니다.

  ![다중 수준 패싯 필터](./media/search-example-adventureworks/multi-level-facet.png "다중 수준 패싯 필터")

 
트리의 각 링크는 관련 필터를 적용해야 합니다. 예를 들면 다음과 같습니다.

+ **taxonomy/any**`(x:x eq 'Accessories')`는 액세서리 분기의 모든 문서를 반환합니다.
+ **taxonomy/any**`(x:x eq 'Accessories|Bike Racks')`는 액세서리 분기 아래에서 하위 범주가 자전거 고정대인 문서만 반환합니다.

이 기술은 심층 분류 트리나 중복 하위 범주처럼 다른 부모 범주에서 발생하는 복잡한 시나리오로 확장할 수 있습니다(예: `Bike Components|Forks` 및 `Camping Equipment|Forks`).

> [!TIP]
> 쿼리 속도는 반환되는 패싯의 수에 영향을 받습니다. 매우 큰 분류 세트를 지원하려면 각 문서의 최상위 분류 값을 보관하는 패싯 가능한 **Edm.String** 필드를 추가하는 방법을 고려해 보세요. 그 후 위의 기술을 똑같이 적용하되, 사용자가 최상위 노드를 확장할 때 컬렉션-패싯 쿼리(루트 분류 필드에서 필터링됨)만 수행합니다. 또는 100% 회수가 필요 없는 경우 패싯 수를 적절하게 줄이고, 개수를 기준으로 패싯 항목을 정렬합니다.

## <a name="see-also"></a>참고 항목

[예제: Azure Search에 대한 AdventureWorks Inventory 데이터베이스 모델링](search-example-adventureworks-modeling.md)
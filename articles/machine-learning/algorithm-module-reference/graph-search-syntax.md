---
title: 그래프 검색 쿼리 구문
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 검색 쿼리 구문을 사용하여 파이프라인 그래프에서 노드를 검색하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420770"
---
# <a name="graph-search-query-syntax"></a>그래프 검색 쿼리 구문

이 문서에서는 Azure Machine Learning의 그래프 검색 쿼리 구문에 대해 알아봅니다. 그래프 검색 기능을 사용하면 이름 및 속성으로 노드를 검색할 수 있습니다. 

 ![예제 그래프 검색 환경을 보여 주는 애니메이션 스크린샷](media/search/graph-search.gif)

그래프 검색은 노드 이름 및 설명에 대한 전체 텍스트 키워드 검색을 지원합니다. RunStatus, duration, computeTarget 등의 노드 속성을 기준으로 필터링할 수도 있습니다. 키워드 검색은 Lucene 쿼리를 기반으로 합니다. 전체 검색 쿼리는 다음과 같습니다.  

**[lucene 쿼리 | [필터 쿼리]** 

Lucene 쿼리 또는 필터 쿼리를 사용할 수 있습니다. 둘 다 사용하려면 **|** 구분 기호를 사용합니다. 필터 쿼리의 구문은 Lucene 쿼리보다 엄격합니다. 따라서 고객 입력을 둘 다로 구문 분석할 수 있으면, 필터 쿼리가 적용됩니다.

 

## <a name="lucene-query"></a>Lucene 쿼리

그래프 검색은 "이름" 및 "설명" 노드에서 전체 텍스트 검색 구문으로 Lucene 단순 쿼리를 사용합니다. 다음과 같은 Lucene 연산자가 지원됩니다.

 
- 및/또는
- **?** 과 **\*** 연산자를 사용하는 와일드 카드 일치.

### <a name="examples"></a>예

- 단순 검색: `JSON Data`

- AND/OR: `JSON AND Validation`

- 다중 AND/OR: `(JSON AND Validation) OR (TSV AND Training)`

 
- 와일드 카드 일치: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> "*" 문자로 Lucene 쿼리를 시작할 수 없습니다.

##  <a name="filter-query"></a>필터 쿼리

 
필터 쿼리는 다음 패턴을 사용합니다.
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
다음 노드 속성을 키로 사용할 수 있습니다.

- runStatus
- compute
- duration
- reuse

그리고 다음 연산자를 사용할 수 있습니다.

- Greater or equal: `>=`
- Less or equal: `<=`
- Greater: `>`
- Less: `<`
- Equal: `==`
- Contain: `=`
- NotEqual: `!=`
- In: `in`

 
 

### <a name="example"></a>예제

- duration > 100;
- status in { Failed,NotStarted}
- compute in {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>기술 정보

- 여러 필터 간의 관계는 "AND"입니다.
- `>=,  >,  <, or  <=`를 선택하면 값이 자동으로 숫자 형식으로 변환됩니다. 그렇지 않으면 문자열 형식이 비교에 사용됩니다.
- 모든 문자열 형식 값의 경우, 비교 시 대/소문자를 구분하지 않습니다.
- "In" 연산자는 컬렉션이 값으로 예상되고, 컬렉션 구문은 `{name1, name2, name3}`입니다.
- 키워드 사이에 공백은 무시됩니다.
---
title: Graph 검색 쿼리 구문
titleSuffix: Azure Machine Learning
description: 검색 쿼리 구문을 사용 하 여 파이프라인 그래프의에서 노드를 검색 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 122da78206ef2055b4867727b174293e74133c05
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817552"
---
# <a name="graph-search-query-syntax"></a>Graph 검색 쿼리 구문

이 문서에서는 Azure Machine Learning의 graph 검색 쿼리 구문에 대해 알아봅니다. 그래프 검색 기능을 사용 하면 이름 및 속성으로 노드를 검색할 수 있습니다. 

 ![예제 그래프 검색 환경을 보여 주는 애니메이션 스크린샷](media/search/graph-search.gif)

그래프 검색은 노드 이름 및 주석에 대 한 전체 텍스트 키워드 검색을 지원 합니다. RunStatus, duration, computeTarget 등의 노드 속성을 기준으로 필터링 할 수도 있습니다. 키워드 검색은 Lucene 쿼리를 기반으로 합니다. 전체 검색 쿼리는 다음과 같습니다.  

**[lucene 쿼리 | [필터 쿼리]** 

Lucene 쿼리 또는 필터 쿼리를 사용할 수 있습니다. 둘 다 사용 하려면 **|** 구분 기호를 사용 합니다. 필터 쿼리의 구문은 Lucene 쿼리 보다 엄격 합니다. 따라서 고객 입력을 둘 다로 구문 분석할 수 있으면 필터 쿼리가 적용 됩니다.

 

## <a name="lucene-query"></a>Lucene 쿼리

그래프 검색은 "이름" 및 "설명" 노드에서 전체 텍스트 검색 구문으로 Lucene 단순 쿼리를 사용 합니다. 지원 되는 Lucene 연산자는 다음과 같습니다.

 
- 및/또는
- 와일드 **카드와 일치** 및 **\*** 연산자.

### <a name="examples"></a>예제

- 단순 검색: `JSON Data`

- 및/또는: `JSON AND Validation`

- Multiple 및/또는: `(JSON AND Validation) OR (TSV AND Training)`

 
- 와일드 카드 일치: 
    - `machi?e learning`
    -   `mach*ing`
 
>[!NOTE]
> "*" 문자를 사용 하 여 Lucene 쿼리를 시작할 수 없습니다.

##  <a name="filter-query"></a>필터 쿼리

 
필터 쿼리는 다음 패턴을 사용 합니다.
 
    **[key1] [operator1] [value1]; [key2] [operator1] [value2];**

 
다음 노드 속성을 키로 사용할 수 있습니다.

- runStatus
- compute
- duration
- 좀더

다음 연산자를 사용 합니다.

- 크거나 같음: `>=`
- 작거나 같음: `<=`
- 이상일 `>`
- 작거나 `<`
- 다릅니다 `==`
- 포함 `=`
- NotEqual `!=`
- 진행 `in`

 
 

### <a name="example"></a>예제

- 기간 > 100;
- {Failed, NotStarted}의 상태
- {gpu 클러스터에서 계산}; {Completed}의 runStatus

## <a name="technical-notes"></a>기술 정보

- 여러 필터 간의 관계는 "AND"입니다.
- `>=,  >,  <, or  <=`를 선택 하면 값이 자동으로 숫자 형식으로 변환 됩니다. 그렇지 않으면 문자열 형식이 비교에 사용 됩니다.
- 모든 문자열 형식 값에 대해 대/소문자는 비교에서 구분 되지 않습니다.
- "의 연산자"는 컬렉션을 값으로 예상 합니다. 컬렉션 구문은입니다. `{name1, name2, name3}`
- 키워드 사이에 공백이 무시 됩니다.
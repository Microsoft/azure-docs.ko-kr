---
title: Azure Monitor 로그 쿼리의 유용한 연산자 | Microsoft Docs
description: Azure Monitor 로그 쿼리에서 다양한 시나리오에 사용할 일반 함수입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: d11445c3f31f9aced6fdb9783575d10a026de1f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424141"
---
# <a name="useful-operators-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리의 유용한 연산자

아래 표에서는 Azure Monitor 로그 쿼리에서 다양한 시나리오에 사용할 일반 함수를 제공합니다.

## <a name="useful-operators"></a>유용한 연산자

Category                                |관련 분석 함수
----------------------------------------|----------------------------------------
선택 영역 및 열 별칭            |`project`, `project-away`, `extend`
임시 테이블 및 상수          |`let scalar_alias_name = …;` <br> `let table_alias_name =  …  …  … ;`| 
비교 및 문자열 연산자         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
일반 문자열 함수                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
일반 수식 함수                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
텍스트 구문 분석                            |`extract()`, `extractjson()`, `parse`, `split()`
출력 제한                         |`take`, `limit`, `top`, `sample`
날짜 함수                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
그룹화 및 집계                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
조인 및 공용 구조체                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
정렬, 순서                             |`sort`, `order` 
동적 개체(JSON 및 배열)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
논리 연산자                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Machine Learning                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 로그 쿼리 작성](get-started-queries.md) 단원을 계속 진행합니다.

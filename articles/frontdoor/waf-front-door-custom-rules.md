---
title: 웹 응용 프로그램 방화벽 사용자 지정 규칙에 대 한 Azure 프런트 도어
description: 웹 응용 프로그램 방화벽 (WAF) 사용자 지정 규칙 악의적인 공격 으로부터 웹 응용 프로그램 보호를 사용 하는 방법에 알아봅니다.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459711"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure 프런트 도어를 사용 하 여 웹 응용 프로그램 방화벽에 대 한 사용자 지정 규칙
프런트 도어 서비스를 사용 하 여 azure 웹 응용 프로그램 방화벽 (WAF)를 사용 하면 정의 된 조건에 따라 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. 사용자 지정 WAF 규칙에 우선 순위 번호, 규칙 유형, 일치 조건 및 작업으로 구성 됩니다. 사용자 지정 규칙의 두 가지가: 규칙과 일치 하 고 속도 제한 규칙입니다. 일치 규칙을 속도 제한 규칙 일치 조건 및 들어오는 요청의 속도에 따라 액세스를 제어 하는 동안 일치 하는 조건을 기반으로 액세스를 제어 합니다. 평가 방지 하기 위해 사용자 지정 규칙을 사용 하지 않도록 설정할 수 있지만 여전히 구성을 유지 합니다. 이 문서는 http 매개 변수를 기반으로 하는 일치 규칙에 설명 합니다.

## <a name="priority-match-conditions-and-action-types"></a>우선 순위, 일치 조건 및 작업 형식
우선 순위 번호, 규칙 유형, 일치 조건 및 동작을 정의 하는 사용자 지정 WAf 규칙을 사용 하 여 액세스를 제어할 수 있습니다. 

- **우선 순위:** 은 WAF 규칙의 평가 순서를 설명 하는 고유 정수입니다. 값이 낮은 규칙은 더 높은 값을 가진 규칙 보다 먼저 평가 됩니다.

- **작업:** WAF 규칙은 일치 하는 경우 요청을 라우팅하는 방법을 정의 합니다. 중 하나를 선택할 수 있습니다는 아래의 때 적용할 작업 요청에 사용자 지정 규칙과 일치 합니다.

    - *허용* -WAF 백 엔드에 대 한 quest 전달, WAF 로그 및 종료에서 항목을 로깅합니다.
    - *블록* -요청이 차단, WAF는 백 엔드에 요청을 전달 하지 않고 클라이언트에 대 한 응답을 보냅니다. WAF는 WAF 로그에 항목을 기록합니다.
    - *로그* -WAF 항목 기록 하 고 계속 WAF 로그는 다음 규칙을 평가 합니다.
    - *리디렉션* -WAF 지정된 된 URI에 요청을 리디렉션합니다 WAF 로그에서 항목을 기록 하 고 종료 합니다.

- **일치 조건:** 일치 변수, 연산자를 정의 및 값과 일치 합니다. 각 규칙에 여러 일치 조건이 포함 될 수 있습니다. 일치 조건을 기반으로 아래 *일치 변수*:
    - RemoteAddr (클라이언트 IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **연산자:** 목록에는 다음이 포함 됩니다.
    - 모든: 규칙이 없고 일치 하는 경우 기본 동작을 정의 하는 경우가 많습니다. 모든 일치 하는 모든 연산자입니다.
    - IPMatch: RemoteAddr 변수에 대 한 IP 제한 정의
    - GeoMatch: 정의 지역 RemoteAddr 변수에 대 한 필터링
    - 같음
    - contains
    - LessThan: 크기 제약 조건
    - GreaterThan: 크기 제약 조건
    - LessThanOrEqual: 크기 제약 조건
    - GreaterThanOrEqual: 크기 제약 조건
    - 시작 문자
     - endsWith

설정할 수 있습니다 *negate* 조건의 결과 부 정하도록 해야 하는 경우 참이 됩니다.

*값과 일치* 가능한 일치 하는 값 목록을 정의 합니다.
값을 포함 하는 HTTP 요청 메서드를 지원 합니다.
- GET
- POST
- PUT
- HEAD
- 삭제
- 잠금
- 잠금 해제
- 프로필
- OPTIONS
- PROPFIND
- PROPPATCH
- MKCOL
- 복사
- MOVE

## <a name="examples"></a>예

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Http 매개 변수에 따라 WAF 규칙 사용자 지정 예제

두 가지 일치 조건 사용 하 여 사용자 지정 규칙의 구성을 보여 주는 예제는 다음과 같습니다. 참조 페이지에 정의 된 대로 요청은 지정한 사이트에서 및 쿼리 문자열 "암호"를 포함 되지 않습니다.

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
"PUT" 메서드를 차단 하는 것에 대 한 예제 구성 같으며 아래와 같이:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>크기 제한

들어오는 요청을의 부분 크기 제약 조건을 지정 하는 사용자 지정 규칙을 작성할 수 있습니다. 예를 들어 규칙 아래 100 자를 초과할 수 있는 Url을 차단 합니다.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>다음 단계
- 에 대 한 자세한 [웹 응용 프로그램 방화벽](waf-overview.md)
- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.


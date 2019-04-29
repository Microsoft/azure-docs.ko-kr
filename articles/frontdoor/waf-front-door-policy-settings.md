---
title: Azure 프런트 도어를 사용 하 여 웹 응용 프로그램 방화벽에 대 한 정책 설정
description: 웹 응용 프로그램 방화벽 (WAF)에 대해 알아봅니다.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 4c2f070e9b3c972f063008df8880b196ddb069cc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459371"
---
# <a name="policy-settings-for-web-application-firewall-with-azure-front-door"></a>Azure 프런트 도어를 사용 하 여 웹 응용 프로그램 방화벽에 대 한 정책 설정

웹 응용 프로그램 방화벽 (WAF) 정책을 사용 하면 사용자 지정 및 관리 되는 규칙의 집합으로 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. WAF 정책 이름은 고유 해야 합니다. 기존 이름을 사용 하려고 하면 유효성 검사 오류를 받을 수 있습니다. 이 문서에 설명 된 대로 해당 정책에 대해 지정 된 모든 규칙에 적용 되는 여러 정책 수준 설정이 있습니다.

## <a name="waf-state"></a>WAF 상태

첫 번째 관문에 대 한 WAF 정책을 다음 두 가지 상태 중 하나일 수 있습니다.
- **사용:** 정책을 사용 하는 경우 WAF 들어오는 요청을 적극적으로 조사 및이 규칙 정의 따라 해당 작업을 수행 하는
- **사용 안 함:** 정책을 비활성화 되 면-WAF 검사 일시 중지 됩니다. 들어오는 요청 WAF을 바이패스 하 고 첫 번째 관문에서 라우팅에 기반 하는 백 엔드에 전송 됩니다.

## <a name="waf-mode"></a>WAF 모드

WAF 정책 다음 두 가지 모드에서 실행 되도록 구성할 수 있습니다.

- **검색 모드** 검색 모드에서 실행 하는 경우 WAF 고 되지 않는 모니터 이외의 어떠한 작업도 수행할 요청과 일치 하는 WAF 규칙 WAF 로그를 기록 합니다. 첫 번째 관문에 대 한 진단 로깅 켜기 (포털을 사용 하는 경우로 이동 하 여 수행할 수 있습니다 합니다 **진단** 섹션에서는 Azure portal에서).

- **방지 모드** 방지 모드에서 실행 되도록 구성 하는 경우 WAF 요청 규칙과 일치 하는 경우 지정 된 작업을 수행 하는 합니다. 일치 하는 모든 요청 WAF 로그에 기록 됩니다.

## <a name="waf-response-for-blocked-requests"></a>차단 된 요청에 대 한 WAF 응답

기본적으로, WAF는 일치 규칙으로 인해 요청을 차단할 때 반환-403 상태 코드가 **요청이 차단** 메시지입니다. 로깅에 대 한 참조 문자열도 반환 됩니다.

WAF에서 요청을 차단 되는 경우 사용자 지정 응답 상태 코드 및 응답 메시지를 정의할 수 있습니다. 다음 사용자 지정 상태 코드 지원 됩니다.

- 200 확인
- 403    Forbidden
- 405 메서드가 허용 되지 않습니다
- 406 not 허용
- 429 너무 많은 요청

사용자 지정 응답 상태 코드 및 응답 메시지에는 정책 수준 설정입니다. 구성 되 면 모든 차단 된 요청이 동일한 사용자 지정 응답 상태 및 응답 메시지를 가져옵니다.

## <a name="uri-for-redirect-action"></a>리디렉션 작업에 대 한 URI

가 요청을 리디렉션하는 URI를 정의 해야 합니다 **리디렉션** WAF 정책에 포함 된 규칙에 대 한 작업을 선택 합니다. 이 리디렉션 URI는 유효한 HTTP (S) 사이트 해야 하며 한 번 구성 하면 모든 요청 "리디렉션" 작업을 사용 하 여 일치 하는 규칙을 지정된 된 사이트에 리디렉션됩니다.


## <a name="next-steps"></a>다음 단계
- WAF를 정의 하는 방법을 알아봅니다 [사용자 지정 응답](waf-front-door-configure-custom-response-code.md)

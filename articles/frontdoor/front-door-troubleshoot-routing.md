---
title: 문제 해결 - Azure Front Door Service 구성 관련 문제 해결 | Microsoft Docs
description: 이 자습서에서는 Front Door에 발생할 수 있는 일반적인 문제 몇 가지를 자체적으로 해결하는 방법을 알아보세요.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 7a261d65a7bd3eea150dd764c65b94ddd47466b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736125"
---
# <a name="troubleshooting-common-routing-issues"></a>일반적인 라우팅 문제 해결
이 문서에서는 Azure Front Door Service 구성에서 발생할 수 있는 몇 가지 일반적인 라우팅 문제를 해결하는 방법을 설명합니다. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>호스트 이름이 백 엔드로 라우팅되지 않고 400 상태 코드가 반환됨


### <a name="symptom"></a>증상
- Front Door를 만들었지만 프런트 엔드 호스트에 대한 요청이 HTTP 400 상태 코드를 반환합니다.

  - 사용자 지정 도메인에서 사용자가 구성한 프런트 엔드 호스트로의 DNS 매핑을 만들었습니다. 하지만 사용자 지정 도메인 호스트 이름으로 요청을 보내면 HTTP 400 상태 코드가 반환되고, 사용자가 구성한 백 엔드로 라우팅하는 것으로 나타나지 않습니다.

### <a name="cause"></a>원인
- 프런트 엔드 호스트로 추가한 사용자 지정 도메인에 대한 회람 규칙을 구성하지 않은 경우 이러한 현상이 발생할 수 있습니다. 사용자 지정 도메인이 DNS 매핑을 가지고 있는 Front Door 하위 도메인(*.azurefd.net) 아래에서 프런트 엔드 호스트에 이미 구성되어 있더라도 프런트 엔드 호스트에 회람 규칙을 명시적으로 추가해야 합니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계
- 사용자 지정 도메인에서 원하는 백 엔드 풀로의 회람 규칙을 추가합니다.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>프런트 엔드 호스트 이름에 대한 요청이 404 상태 코드 반환

### <a name="symptom"></a>증상
- Front Door를 만들고 프런트 엔드 호스트, 하나 이상의 백 엔드가 있는 백 엔드 풀, 프런트 엔드 호스트를 백 엔드 풀로 연결하는 회람 규칙을 구성했습니다. HTTP 404 상태 코드가 반환된 것으로 보아 구성된 프런트 엔드 호스트로 요청을 보낼 때 콘텐츠가 보이지 않는 것 같습니다.

### <a name="cause"></a>원인
이 증상의 잠재적 원인에는 몇 가지가 있습니다.
 - 백 엔드는 공용 백 엔드가 아니며, Front Door 서비스에는 보이지 않습니다.

- 백 엔드가 잘못 구성되어 Front Door 서비스가 잘못된 요청을 보내는 것입니다(즉, 백 엔드는 HTTP만 허용하지만 사용자가 HTTPS를 허용하지 않기로 선택했으므로 Front Door가 HTTPS 요청을 전달하려고 하는 것임).
- 백 엔드가 백 엔드에 대한 요청으로 전달된 호스트 헤더를 거부하고 있습니다.
- 백 엔드 구성이 아직 완전히 배포되지 않았습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계
1. 배포 시간
    - 구성이 배포될 때까지 기다린 시간이 10분 이내인지 확인하세요.

2. 백 엔드 설정 확인
   - 요청을 라우팅해야 하는 백 엔드 풀(회람 규칙이 구성된 방식에 따라 다름)로 이동하고, _백 엔드 호스트 유형_ 및 백엔드 호스트 이름이 올바른지 확인합니다. 백 엔드가 사용자 지정 호스트일 경우 철자라 올바른지 확인하십시오. 

   - HTTP 및 HTTPS 포트를 확인합니다. 대부분의 경우 80 및 443(각각)이 올바르며, 변경 작업은 필요하지 않습니다. 하지만 백 엔드가 이런 식으로 구성되지 않아서 다른 포트에서 수신 대기 중일 가능성이 있습니다.

     - 프런트 엔드 호스트를 라우팅해야 하는 백 엔드에 _백 엔드 호스트 헤더_가 구성되어 있는지 확인하세요. 대부분의 경우 이 헤더는 _백 엔드 호스트 이름_과 동일해야 합니다. 그러나 백 엔드에서 기대한 값과 다를 경우 다양한 HTTP 4xx 상태 코드가 반환될 수 있습니다. 백 엔드의 IP 주소를 입력하는 경우 _백 엔드 호스트 헤더_를 백 엔드의 호스트 이름으로 설정해야 할 수 있습니다.


3. 회람 규칙 설정 확인
     - 해당 프런트 엔드 호스트 이름에서 백 엔드 풀로 라우팅되어야 하는 회람 규칙으로 이동합니다. 허용되는 프로토콜이 올바르게 구성되었는지, 그렇지 않다면 요청 전달 시 Front Door에서 사용할 프로토콜이 올바르게 구성되었는지 확인하세요. _허용되는 프로토콜_에 따라 Front Door에서 허용해야 하는 요청이 결정되고, _고급_ 탭의 _전달 프로토콜_에 따라 Front Door에서 백 엔드로 요청을 전달하는 데 사용해야 하는 프로토콜이 결정됩니다.
          - 예를 들어 백 엔드가 HTTP 요청만 허용하는 경우 다음과 같은 구성이 유효합니다.
               - _허용되는 프로토콜_은 HTTP 및 HTTPS입니다. _전달 프로토콜_은 HTTP입니다. HTTPS가 허용되는 프로토콜이므로 일치 요청이 작동하지 않으며, 요청이 HTTPS 형식으로 수행될 경우 Front Door가 HTTPS를 사용하여 전달을 시도합니다.

               - _허용되는 프로토콜_은 HTTP입니다. _전달 프로토콜_은 일치 요청 또는 HTTPS입니다.

   - 회람 규칙 구성 창 상단에 있는 _고급_ 탭을 클릭합니다. _Url 다시 쓰기_는 기본적으로 비활성화되며, 사용 가능하게 만들려는 백 엔드 호스트 리소스의 범위를 좁히려는 경우에만 이 필드를 사용해야 합니다. 비활성화될 경우 Front Door가 수신한 요청 경로를 전달합니다. 이 필드가 잘못 구성되었고 Front Door가 사용할 수 없는 백 엔드에서 리소스를 요청하는 경우 HTTP 404 상태 코드가 반환됩니다.


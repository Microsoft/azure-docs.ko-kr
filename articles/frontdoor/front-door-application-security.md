---
title: Azure Front Door Service - 애플리케이션 계층 보안 | Microsoft Docs
description: 이 문서는 Azure Front Door Service로 애플리케이션 백 엔드를 보호하는 방법을 이해하는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: c7b99548e2fe1ad0c1cab39953e28a97e7ebff4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193920"
---
# <a name="application-layer-security-with-front-door"></a>Front Door와 애플리케이션 계층 보안
Azure Front Door Service는 SQL 주입 또는 교차 사이트 스크립팅(XSS)과 같은 일반적인 웹 취약점 악용과 네트워크 공격으로부터 웹 애플리케이션을 보호하는 웹 애플리케이션 보호 기능을 제공합니다. http(s) 프런트 엔드를 사용할 수 있게 설정된 Front Door의 애플리케이션 계층 보안은 전역적으로 배포되며, 항상 켜져 있으므로 백 엔드와 멀리 떨어진 Azure의 네트워크 에지에서 악의적인 공격을 막아줍니다. 보안 및 성능이 더욱 최적화된 Front Door는 최종 사용자에게 빠르고 안전한 웹 환경을 제공합니다.

## <a name="application-protection"></a>애플리케이션 보호
Front Door의 애플리케이션 보호는 전 세계 각 에지 환경에서 애플리케이션과 함께 구성되어 있으며, http(s) 이외의 트래픽이 웹 애플리케이션에 도달하지 못하게 자동으로 차단합니다. 다중 테넌트 분산 아키텍처는 성능 저하 없이 대규모로 글로벌 보호를 가능하게 합니다. http(s) 워크로드의 경우 Front Door의 웹 애플리케이션 보호 서비스가 사용자 지정 규칙, 일반적인 공격에 대해 미리 구성된 규칙 집합, 규칙과 일치하는 모든 요청에 대한 세부적인 로깅을 위한 풍부한 규칙 엔진을 제공합니다. 허용, 차단 또는 기록만 포함된 유연한 작업이 지원됩니다.

## <a name="custom-access-control-rules"></a>사용자 지정 액세스 제어 규칙
- **IP 허용 목록과 차단 목록:** 클라이언트 IP 주소 목록을 기반으로 웹 응용 프로그램에 대한 액세스를 제어 하는 사용자 지정 규칙을 구성할 수 있습니다. IP v4 및 IP v6이 모두 지원됨
- **지리적 기반된 액세스 제어:** 클라이언트 IP는 국가 코드를 기반으로 웹 응용 프로그램에 대한 액세스를 제어 하는 사용자 지정 규칙을 구성할 수 있습니다.
- **HTTP 매개 변수를 필터링 합니다.** Http (s) 요청 매개 변수 헤더, URL 및 쿼리 문자열을 포함 하 여 일치를 기준으로 하는 사용자 지정 액세스 규칙을 구성할 수 있습니다.

## <a name="azure-managed-rules"></a>Azure 관리 규칙
- 일반적인 주요 OWASP 취약점에 대해 미리 구성된 규칙 집합이 기본적으로 활성화되어 있습니다. 미리 보기에서 규칙 집합에는 sqli 및 xss 요청 검사가 포함됩니다. 추가 규칙이 추가됩니다. 미리 구성된 규칙이 애플리케이션에서 올바르게 작동하는지 확인하는 기록만 작업으로 시작할 수 있습니다. 

## <a name="rate-limiting"></a>속도 제한
- 속도 제어 규칙은 모든 클라이언트 IP에서 오는 비정상적으로 높은 트래픽을 제한하는 것입니다.  1분 동안 클라이언트 IP에서 허용하는 웹 요청 수에 대한 임계값을 설정할 수 있습니다.

## <a name="centralized-protection-policy"></a>중앙 집중식 보호 정책
- 여러 보호 규칙을 정의하고 우선 순위에 따라 정책에 추가할 수 있습니다. 사용자 지정 규칙은 관리되는 규칙 집합보다 우선순위가 높아서 예외가 허용됩니다. 단일 정책이 웹 애플리케이션에 연결됩니다.  동일한 웹 애플리케이션 보호 정책이 모든 위치의 모든 에지 서버에 복제되고, 모든 지역에서 일관적인 보안 정책이 보장됩니다.

## <a name="configuration"></a>구성
- 미리 보기 중에 REST API, PowerShell 또는 CLI를 사용하여 Front Door의 애플리케이션 보호 규칙 및 정책을 만들고 배포할 수 있습니다. 서비스가 일반에 공개되기 전에 포털 액세스가 지원됩니다. 


## <a name="monitoring"></a>모니터링
Front Door는 Azure Monitor에 통합된 실시간 메트릭을 사용하여 웹 애플리케이션에 대한 공격을 모니터링하여 경고를 추적하고 추세를 손쉽게 모니터링하는 기능을 제공합니다.

## <a name="pricing"></a>가격
Front Door의 애플리케이션 계층 보안은 미리 보기 기간 동안 무료입니다.


## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.

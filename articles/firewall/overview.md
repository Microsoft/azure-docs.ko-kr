---
title: Azure Firewall이란?
description: Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperfq1
ms.date: 08/25/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 914f267edd5a8168fc11af7186e322c306718a4a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852628"
---
# <a name="what-is-azure-firewall"></a>Azure Firewall이란?

![ICSA 인증](media/overview/icsa-cert-firewall-small.png)

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다.

![방화벽 개요](media/overview/firewall-threat.png)

구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다. Azure Firewall은 가상 네트워크 리소스에 정적 공용 IP 주소를 사용하기 때문에 외부 방화벽이 사용자의 가상 네트워크에서 시작된 트래픽을 식별할 수 있습니다.  이 서비스는 로깅 및 분석을 위해 Azure Monitor와 완전히 통합됩니다.

## <a name="features"></a>기능

Azure Firewall 기능에 대해 알아보려면 [Azure Firewall 기능](features.md)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

Azure Firewall의 알려진 문제는 다음과 같습니다.

|문제  |설명  |완화 방법  |
|---------|---------|---------|
TCP/UDP 프로토콜이 아닌 프로토콜(예: ICMP)에 대한 네트워크 필터링 규칙은 인터넷 바운드 트래픽에 작동하지 않습니다.|TCP/UDP 프로토콜이 아닌 프로토콜에 대한 네트워크 필터링 규칙은 공용 IP 주소에 대한 SNAT에 작동하지 않습니다. TCP/UDP 프로토콜이 아닌 프로토콜은 스포크 서브넷과 VNet 간에 지원됩니다.|Azure Firewall은 표준 Load Balancer를 사용하기 때문에 [현재 IP 프로토콜을 위한 SNAT를 지원하지 않습니다](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). 향후 릴리스에서 이 시나리오를 지원할 수 있는 옵션을 모색하고 있습니다.|
|ICMP에 대한 PowerShell 및 CLI 지원 누락|Azure PowerShell 및 CLI는 네트워크 규칙에 유효한 프로토콜로 ICMP를 지원하지 않습니다.|여전히 포털 및 REST API를 통해 ICMP를 프로토콜로 사용할 수 있습니다. PowerShell 및 CLI에 ICMP를 조만간 추가하기 위해 노력 중입니다.|
|FQDN 태그는 프로토콜: 설정할 포트가 필요|FQDN 태그를 사용하는 애플리케이션 규칙에는 포트: 프로토콜 정의가 필요합니다.|**https**를 포트: 프로토콜 값으로 사용할 수 있습니다. FQDN 태그를 사용할 때 이 필드 옵션이 작동하도록 하기 위한 작업이 진행 중입니다.|
|방화벽을 다른 리소스 그룹 또는 구독으로 이동하는 기능은 지원되지 않습니다.|방화벽을 다른 리소스 그룹 또는 구독으로 이동하는 기능은 지원되지 않습니다.|이 기능은 로드맵에 있습니다. 방화벽을 다른 리소스 그룹 또는 구독으로 이동하려면 현재 인스턴스를 삭제하고 새 리소스 그룹 또는 구독에서 다시 만들어야 합니다.|
|위협 인텔리전스 경고는 마스킹될 수 있습니다.|아웃바운드 필터링의 대상이 80/443인 네트워크 규칙은 위협 전용 모드로 구성되면 위협 인텔리전스 경고를 마스킹합니다.|애플리케이션 규칙을 사용하여 80/443에 대한 아웃바운드 필터링을 만듭니다. 또는 위협 인텔리전스 모드를 **경고 및 거부**로 변경합니다.|
|Azure Firewall DNAT는 개인 IP 대상에는 작동하지 않습니다.|Azure Firewall DNAT 지원은 인터넷 송신/수신으로 제한됩니다. DNAT는 현재 개인 IP 대상에는 작동하지 않습니다. 예: 스포크-스포크.|이 문제가 현재 제한 사항입니다.|
|첫 번째 공용 IP 구성을 제거할 수 없음|각 Azure Firewall 공용 IP 주소는 *IP 구성*에 할당됩니다.  첫 번째 IP 구성은 방화벽을 배포하는 동안 할당되며, 일반적으로 방화벽 서브넷에 대한 참조도 포함하고 있습니다(템플릿 배포를 통해 명시적으로 다르게 구성하지 않는 이상). 이 IP 구성을 삭제하면 방화벽이 할당 취소되므로 삭제할 수 없습니다. 방화벽에 사용 가능한 다른 공용 IP 주소가 하나 이상 있는 경우 이 IP 구성과 연결된 공용 IP 주소를 변경하거나 제거할 수 있습니다.|이것은 의도적인 것입니다.|
|가용성 영역은 배포 중에만 구성할 수 있습니다.|가용성 영역은 배포 중에만 구성할 수 있습니다. 방화벽이 배포된 후에는 가용 영역을 구성할 수 없습니다.|이것은 의도적인 것입니다.|
|인바운드 연결의 SNAT|DNAT 외에도 방화벽 공용 IP 주소(인바운드)를 통한 연결은 방화벽 개인 IP 중 하나로 SNAT됩니다. 이 요구 사항은 현재(활성/활성 NVA의 경우에도) 대칭 라우팅을 보장합니다.|HTTP/S에 대한 원래 원본을 보존하려면 [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For) 헤더를 사용하는 것이 좋습니다. 예를 들어 방화벽 앞에 있는 [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) 또는 [Azure Application Gateway](../application-gateway/rewrite-http-headers.md)와 같은 서비스를 사용합니다. Azure Front Door의 일부로 WAF를 추가하고 방화벽에 체인을 추가할 수도 있습니다.
|프록시 모드(포트 1433)에서만 지원되는 SQL FQDN 필터링|Azure SQL Database, Azure SQL Data Warehouse 및 Azure SQL Managed Instance:<br><br>미리 보기 기간에는 SQL FQDN 필터링이 프록시 모드(포트 1433)에서만 지원됩니다.<br><br>Azure SQL IaaS의 경우:<br><br>비표준 포트를 사용하는 경우 애플리케이션 규칙에서 해당 포트를 지정할 수 있습니다.|리디렉션 모드의 SQL(Azure 내에서 연결하는 경우 기본값)의 경우 대신 SQL 서비스 태그를 Azure Firewall 네트워크 규칙의 일부로 사용하여 액세스를 필터링할 수 있습니다.
|TCP 포트 25의 아웃바운드 트래픽은 허용되지 않음| TCP 포트 25를 사용하는 아웃바운드 SMTP 연결이 차단되었습니다. 포트 25는 주로 인증되지 않은 이메일 제공을 위해 사용됩니다. 이는 가상 머신의 기본 플랫폼 동작입니다. 자세한 내용은 [Azure에서 아웃바운드 SMTP 연결 문제 해결](../virtual-network/troubleshoot-outbound-smtp-connectivity.md)을 참조하세요. 그러나 가상 머신과 달리 현재는 Azure 방화벽에서 이 기능을 사용하도록 설정할 수 없습니다. 참고: 25개 이외의 포트를 통해 인증된 SMTP(포트 587) 또는 SMTP를 허용하려면 현재 SMTP 검사가 지원되지 않으므로 애플리케이션 규칙이 아닌 네트워크 규칙을 구성해야 합니다.|SMTP 문제 해결 문서에 설명된 것처럼 권장 방법에 따라 이메일을 보냅니다. 또는 기본 경로에서 방화벽으로의 아웃바운드 SMTP 액세스가 필요한 가상 머신을 제외합니다. 대신 인터넷에 대한 아웃바운드 액세스를 직접 구성합니다.
|활성 FTP는 지원되지 않습니다.|FTP PORT 명령을 사용하여 FTP 바운스 공격으로부터 보호하기 위해 Azure Firewall에서 활성 FTP를 사용하지 않도록 설정합니다.|대신 수동 FTP를 사용할 수 있습니다. 여전히 방화벽에서 TCP 포트 20 및 21을 명시적으로 열어야 합니다.
|SNAT 포트 사용률 메트릭이 0%를 표시합니다.|SNAT 포트를 사용하는 경우에도 Azure Firewall SNAT 포트 사용률 메트릭이 0% 사용량을 표시할 수 있습니다. 이 경우 방화벽 상태 메트릭의 일부로 메트릭을 사용하면 잘못된 결과가 제공됩니다.|이 문제는 해결되었으며 2020년 5월에 출시될 예정입니다. 방화벽 재배포로 문제가 해결되는 경우도 있지만 일관되지 없습니다. 중간책으로, 방화벽 상태만 사용하여 *status=unhealthy*가 *status=degraded*를 찾습니다. 포트 소모는 *성능 저하됨*으로 표시됩니다. 방화벽 상태에 영향을 주는 더 많은 메트릭이 있는 경우 *정상이 아님*은 나중에 사용하도록 예약되어 있습니다.
|DNAT는 강제 터널링을 사용하는 경우 지원되지 않습니다.|강제 터널링을 사용하여 배포된 방화벽은 비대칭 라우팅으로 인해 인터넷에서 인바운드 액세스를 지원할 수 없습니다.|이는 비대칭 라우팅 때문에 의도된 것입니다. 인바운드 연결의 반환 경로는 온-프레미스 방화벽을 통해 전달되며 설정된 연결이 표시되지 않습니다.
|아웃바운드 수동 FTP는 여러 공용 IP 주소를 사용하는 방화벽에 대해 작동하지 않습니다.|수동 FTP는 제어 및 데이터 채널에 대해 서로 다른 연결을 설정합니다. 공용 IP 주소가 여러 개인 방화벽이 데이터를 아웃바운드로 보내는 경우 원본 IP 주소에 대한 공용 IP 주소 중 하나를 임의로 선택합니다. 데이터 및 제어 채널에서 서로 다른 원본 IP 주소를 사용하면 FTP가 실패합니다.|명시적 SNAT 구성이 계획되어 있습니다. 이 경우 단일 IP 주소를 사용하는 것이 좋습니다.|
|NetworkRuleHit 메트릭에 프로토콜 차원이 누락됨|ApplicationRuleHit 메트릭은 필터링 기반 프로토콜을 허용하지만 해당 NetworkRuleHit 메트릭에는 이 기능이 없습니다.|수정 사항을 조사하고 있습니다.|
|64000에서 65535 사이의 포트를 사용하는 NAT 규칙이 지원되지 않음|Azure Firewall은 네트워크 및 애플리케이션 규칙에서 1-65535 범위의 모든 포트를 허용하지만 NAT 규칙은 1-63999 범위의 포트만 지원합니다.|이 문제가 현재 제한 사항입니다.
|구성 업데이트는 평균 5분이 걸릴 수 있습니다.|Azure Firewall 구성 업데이트는 평균 3 ~ 5분이 걸릴 수 있으며 병렬 업데이트는 지원되지 않습니다.|수정 사항을 조사하고 있습니다.|
|Azure Firewall은 SNI TLS 헤더를 사용하여 HTTPS 및 MSSQL 트래픽을 필터링합니다.|브라우저 또는 서버 소프트웨어에서 SNI(서버 이름 표시기) 확장을 지원하지 않는 경우 Azure Firewall을 통해 연결할 수 없습니다.|브라우저 또는 서버 소프트웨어에서 SNI를 지원하지 않는 경우 애플리케이션 규칙 대신 네트워크 규칙을 사용하여 연결을 제어할 수 있습니다. SNI를 지원하는 소프트웨어는 [서버 이름 표시](https://wikipedia.org/wiki/Server_Name_Indication)를 참조하세요.|
|사용자 지정 DNS(미리 보기)는 강제 터널링에서 작동하지 않습니다.|강제 터널링이 활성화된 경우 사용자 지정 DNS(미리 보기)가 작동하지 않습니다.|수정 사항을 조사하고 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)
- [템플릿을 사용하여 Azure Firewall 배포](deploy-template.md)
- [Azure Firewall 테스트 환경 만들기](scripts/sample-create-firewall-test.md)

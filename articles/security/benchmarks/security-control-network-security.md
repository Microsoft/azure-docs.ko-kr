---
title: Azure 보안 제어-네트워크 보안
description: 보안 제어 네트워크 보안
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251875"
---
# <a name="security-control-network-security"></a>보안 제어: 네트워크 보안

네트워크 보안 권장 사항은 Azure 서비스에 대 한 액세스가 허용 되거나 거부 되는 네트워크 프로토콜, TCP/UDP 포트 및 네트워크 연결 서비스를 지정 하는 데 중점을 둡니다.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용 하 여 리소스를 보호 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Customer |

모든 Virtual Network 서브넷 배포에는 응용 프로그램의 신뢰할 수 있는 포트 및 원본에 특정 한 네트워크 액세스 제어와 함께 적용 되는 네트워크 보안 그룹이 있는지 확인 합니다. 개인 링크를 사용 하는 Azure 서비스를 사용 하거나, Vnet 내부에 서비스를 배포 하거나, 개인 끝점을 사용 하 여 개인적으로 연결 합니다. 서비스별 요구 사항은 해당 특정 서비스에 대 한 보안 권장 사항을 참조 하세요.

또는 특정 사용 사례가 있는 경우 Azure 방화벽을 구현 하 여 요구 사항을 충족할 수 있습니다.

개인 링크에 대 한 일반 정보:

https://docs.microsoft.com/azure/private-link/private-link-overview

Virtual Network를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용 하 여 NSG를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure 방화벽을 배포 및 구성 하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 Nic의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.2 | 9.3, 12.2 | Customer |

Azure Security Center를 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 네트워크 리소스를 보호 합니다. NSG 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다.

NSG 흐름 로그를 사용 하도록 설정 하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center에서 제공 하는 네트워크 보안 이해:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: 중요 한 웹 응용 프로그램 보호

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.3 | 9.5 | Customer |

들어오는 트래픽의 추가 검사를 위해 중요 한 웹 응용 프로그램 앞에 Azure WAF (웹 응용 프로그램 방화벽)를 배포 합니다. WAF에 대해 진단 설정을 사용 하도록 설정 하 고 로그를 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역에 수집 합니다.

Azure WAF를 배포 하는 방법:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.4 | 12.3 | Customer |

DDoS 공격 으로부터 보호 하기 위해 Azure 가상 네트워크에서 DDoS 표준 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 IP 주소와의 통신을 거부 합니다.

위협 인텔리전스를 사용 하도록 설정 하 고 구성 하 여 악의적인 네트워크 트래픽에 대 한 경고 및 거부&quot;을 &quot;하도록 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다.

Azure Security Center Just-in-time 네트워크 액세스를 사용 하 여 제한 된 기간 동안 끝점의 노출을 승인 된 IP 주소로 제한 하는 NSGs를 구성 합니다.

적응 네트워크 강화 Azure Security Center 사용 하 여 실제 트래픽 및 위협 인텔리전스에 따라 포트와 원본 Ip를 제한 하는 NSG 구성을 권장 합니다.

DDoS 보호를 구성 하는 방법:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 방화벽을 배포 하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Security Center 통합 위협 인텔리전스 이해:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

적응 네트워크 강화 Azure Security Center 이해:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Just-in-time 네트워크 Access Control Azure Security Center 이해:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.5 | 12.5, 15.8 | Customer |

저장소 계정에 NSG 흐름 로그를 기록 하 여 흐름 레코드를 생성 합니다. 비정상적인 활동을 조사 하는 데 필요한 경우 Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

NSG 흐름 로그를 사용 하도록 설정 하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher를 사용 하도록 설정 하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS)을 배포 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

위협 인텔리전스를 사용 하도록 설정 하 고 구성 하 여 악의적인 네트워크 트래픽에 대 한 경고 및 거부&quot;을 &quot;하도록 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다.

Azure 방화벽을 배포 하는 방법: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽을 사용 하 여 경고를 구성 하는 방법: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 응용 프로그램에 대 한 트래픽 관리

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

신뢰할 수 있는 인증서에 대해 HTTPS/SSL을 사용 하도록 설정 된 웹 응용 프로그램용 Azure 애플리케이션 게이트웨이를 배포 합니다.

Application Gateway를 배포 하는 방법:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

HTTPS를 사용 하도록 Application Gateway를 구성 하는 방법:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Azure 웹 응용 프로그램 게이트웨이를 사용 하 여 계층 7 부하 분산 이해:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버 헤드를 최소화 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.8 | 1.5 | Customer |

Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: Microsoft.apimanagement)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그가 들어 있는 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다.

서비스 태그 이해 및 사용:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대 한 표준 보안 구성 유지

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.9 | 11.1 | Customer |

Azure Policy를 사용 하 여 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다.

또한 Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키지 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 버전 관리를 통해 새 구독에 청사진을 적용 하 고 제어 및 관리를 세부적으로 조정할 수 있습니다.

Azure Policy를 구성 하 고 관리 하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

네트워킹에 대 한 Azure Policy 샘플:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Azure Blueprint를 만드는 방법:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.1 | 11.2 | Customer |

NSGs 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 &quot;설명&quot; 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙에 대 한 비즈니스 요구 및/또는 기간 (등)을 지정할 수 있습니다.

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Virtual Network를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용 하 여 NSG를 만드는 방법:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 검색 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 1.11 | 11.3 | Customer |

Azure Policy를 사용 하 여 네트워크 리소스에 대 한 구성을 확인 (및/또는 재구성) 합니다.

Azure Policy를 구성 하 고 관리 하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

네트워킹에 대 한 Azure Policy 샘플:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [로깅 및 모니터링](security-control-logging-monitoring.md) 을 참조 하세요.

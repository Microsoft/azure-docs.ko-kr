---
title: Azure DDoS Protection 표준 개요
description: Azure DDoS Protection 표준이 응용 프로그램 디자인 모범 사례와 결합 된 경우 DDoS 공격에 대 한 방어를 제공 하는 방법을 알아보세요.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: af10ec10a1622f32dbc34a607b1200cb4cff8b59
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168051"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection 표준 개요

DDoS(배포된 서비스 거부) 공격은 고객이 애플리케이션을 클라우드로 전환하게 만드는 가장 큰 가용성 및 보안 문제 중 일부입니다. DDoS 공격은 애플리케이션의 리소스를 소진시켜서 정상적인 사용자가 애플리케이션을 사용할 수 없게 만듭니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다.

애플리케이션 설계 모범 사례와 결합된 Azure DDoS Protection은 DDoS 공격에 대한 방어 기능을 제공합니다. Azure DDoS 보호는 다음과 같은 서비스 계층을 제공합니다.

- **기본**: 자동으로 Azure 플랫폼의 일부로 사용하도록 설정됩니다. 상시 트래픽 모니터링과 일반적인 네트워크 수준 공격에 대 한 실시간 완화는 Microsoft의 온라인 서비스에서 사용 하는 것과 동일한 방어 기능을 제공 합니다.Azure 글로벌 네트워크의 전체 규모를 사용 하 여 지역 간 공격 트래픽을 분산 하 고 완화할 수 있습니다.IPv4 및 IPv6 Azure [공용 IP 주소](virtual-network-public-ip-address.md)에 대해 보호가 제공됩니다.
- **표준**: 기본 서비스 계층에 대해 Azure Virtual Network 리소스에 맞게 특별히 조정된 추가적인 완화 기능을 제공합니다. DDoS Protection 표준은 간단히 사용하도록 설정할 수 있고 애플리케이션을 변경할 필요가 없습니다. 보호 정책은 전용 트래픽 모니터링 및 기계 학습 알고리즘을 통해 조정됩니다. 정책은 Azure Load Balancer, Azure Application Gateway 및 Azure Service Fabric 인스턴스 같은 가상 네트워크에 배포된 리소스에 연결된 공용 IP 주소에 적용되지만, 이 보호는 App Service 환경에 적용되지 않습니다.공격을 받고 있을 때 기록을 위해 Azure Monitor 뷰를 통해 실시간 원격 분석을 사용할 수 있습니다. 진단 설정을 통해 다양한 공격 완화 분석을 사용할 수 있습니다. [Azure Application Gateway 웹 애플리케이션 방화벽](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 통해 또는 Azure Marketplace에서 타사 방화벽을 설치하여 애플리케이션 계층 보호를 추가할 수 있습니다. IPv4 및 IPv6 Azure [공용 IP 주소](virtual-network-public-ip-address.md)에 대해 보호가 제공됩니다.

|기능                                         |DDoS Protection 기본                 |DDoS Protection 표준                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Always on 검색 & 활성 트래픽 모니터링 |예                                   |예                                           |
|자동 공격 완화                    |예                                   |예                                           |
|가용성 보장                          |Azure 지역                          |애플리케이션                                   |
|완화 정책                             |Azure 트래픽 영역 볼륨에 맞게 조정 |응용 프로그램 트래픽 볼륨에 맞게 조정          |
|메트릭 & 경고                                |예                                    |Azure Monitor를 통해 리소스 로그 & 실시간 공격 메트릭                                 |
|완화 보고서                              |예                                    |사후 공격 완화 보고서                |
|완화 흐름 로그                            |예                                    |SIEM 통합을 위한 NRT 로그 스트림           |
|완화 정책 사용자 지정                 |예                                    |DDoS 전문가 참여                           |
|고객 지원팀                                         |최상의 노력                           |활성 공격 중 DDoS 전문가에 대 한 액세스|
|SLA                                             |Azure 지역                          |응용 프로그램 보장 & 비용 보호       |
|가격 책정                                         |무료                                  |매월 & 사용량 기반                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS Protection 표준으로 완화되는 DDoS 공격의 유형

DDoS Protection 표준은 다음 유형의 공격을 완화할 수 있습니다.

- **대규모 공격**: 이러한 공격은 상당한 양의 합법적인 트래픽으로 네트워크 계층을 초과 합니다. 여기에는 UDP 초과, 증폭 및 기타 스푸핑된 패킷 홍수가 포함 됩니다. DDoS Protection Standard는 Azure의 글로벌 네트워크 규모를 자동으로 흡수 하 여 이러한 잠재적인 멀티 기가바이트 공격을 완화 합니다.
- **프로토콜 공격**: 이러한 공격은 계층 3 및 계층 4 프로토콜 스택의 취약점을 악용하여 대상을 액세스 불능 상태로 만듭니다. SYN 홍수 공격, 리플렉션 공격 및 기타 프로토콜 공격을 포함 합니다. DDoS Protection 표준은 클라이언트와의 상호 작용을 통해 악성 트래픽과 정상 트래픽을 구분하고 악성 트래픽을 차단하여 이러한 공격을 완화합니다. 
- **리소스(애플리케이션) 계층 공격**: 이러한 공격은 대상 웹 애플리케이션 패킷을 공격하여 호스트 간 데이터 전송을 방해합니다. 여기에는 HTTP 프로토콜 위반, SQL 주입, 교차 사이트 스크립팅 및 기타 계층 7 공격이 포함 됩니다. Azure [Application Gateway 웹 응용 프로그램 방화벽과](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)같은 웹 응용 프로그램 방화벽 및 DDoS Protection 표준을 사용 하 여 이러한 공격에 대 한 방어를 제공 합니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)에서 사용할 수 있는 타사 웹 애플리케이션 방화벽 제품도 있습니다.

DDoS Protection 표준은 가상 머신과 연결된 공용 IP 주소, 부하 분산 장치 및 애플리케이션 게이트웨이를 비롯한 가상 네트워크의 리소스를 보호합니다. Application Gateway 웹 응용 프로그램 방화벽이 나 공용 IP를 사용 하는 가상 네트워크에 배포 된 타사 웹 응용 프로그램 방화벽과 함께 사용 하는 경우 DDoS Protection Standard는 계층 7 완화 기능에 전체 계층 3을 제공할 수 있습니다.

## <a name="ddos-protection-standard-features"></a>DDoS Protection 표준 기능

![DDoS 기능](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection 표준 기능에는 다음이 포함됩니다.

- **네이티브 플랫폼 통합:** 기본적으로 Azure에 통합됩니다. Azure Portal을 통해 구성을 포함합니다. DDoS Protection 표준은 사용자의 리소스 및 리소스 구성을 인식합니다.
- **턴키 보호:** 단순화 된 구성은 DDoS Protection Standard를 사용 하도록 설정 하는 즉시 가상 네트워크의 모든 리소스를 즉시 보호 합니다. 작업 또는 사용자 정의가 필요하지 않습니다. DDoS Protection 표준은 공격이 감지되는 즉시 자동으로 공격을 완화시킵니다.
- **상시 트래픽 모니터링:** 응용 프로그램 트래픽 패턴은 DDoS 공격에 대 한 지표를 찾고 하루 24 시간 동안 모니터링 됩니다. 보호 정책이 초과되면 완화가 수행됩니다.
- **적응 조정:** 지능형 트래픽 프로 파일링은 시간에 따른 응용 프로그램의 트래픽을 학습 하 고 서비스에 가장 적합 한 프로필을 선택 하 여 업데이트 합니다. 트래픽이 시간이 지남에 따라 변경되면서 프로필이 조정됩니다.
- **다중 계층 보호:** 웹 애플리케이션 방화벽과 함께 사용될 경우 전체 스택 DDoS 보호를 제공합니다.
- **광범위한 완화 규모:** 가장 큰 규모로 알려진 DDoS 공격으로부터 시스템을 보호할 수 있는 글로벌 역량으로 60가지 공격을 완화할 수 있습니다.
- **공격 분석:** 공격 진행 중에 5분 단위로 세부 보고서를 가져오고, 공격이 종료된 후에는 전체 요약을 가져옵니다. 스트림 완화 흐름은 공격 진행 중에 거의 실시간 모니터링을 위해 오프라인 SIEM(보안 정보 및 이벤트) 시스템에 기록됩니다.
- **공격 메트릭:** Azure Monitor를 통해 각 공격으로부터 요약된 메트릭에 액세스할 수 있습니다.
- **공격 경고:** 경고는 공격 시작 및 중지 시, 그리고 공격이 진행 되는 동안 기본 제공 공격 메트릭을 사용 하 여 구성할 수 있습니다. 경고는 Microsoft Azure 모니터 로그, Splunk, Azure Storage, 전자 메일 및 Azure Portal와 같은 운영 소프트웨어에 통합 됩니다.
- **비용 보장:** 문서화된 DDoS 공격에 대한 데이터 전송 및 애플리케이션 확장 서비스 크레딧이 제공됩니다.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection 표준 완화

DDoS Protection 표준은 실제 트래픽 사용률을 모니터링하고 이를 DDoS 정책에 정의된 임계값과 지속적으로 비교합니다. 트래픽 임계값이 초과되면 DDoS 완화가 자동으로 시작됩니다. 트래픽이 임계값 아래로 돌아오면 완화가 제거됩니다.

![완화 방법](./media/ddos-protection-overview/mitigation.png)

완화 중에 보호되는 리소스로 보낸 트래픽은 DDoS Protection 서비스를 통해 리디렉션되며 다음과 같은 여러 검사가 수행됩니다.

- 패킷이 인터넷 사양을 준수하고 잘못된 형식이 아닌지 확인합니다.
- 클라이언트와 상호 작용하여 트래픽이 스푸핑된 패킷일 가능성이 있는지 확인합니다(예: SYN Auth 또는 SYN 쿠키 또는 원본에서 다시 전송하도록 패킷 삭제).
- 다른 적용 방법을 수행할 수 없는 경우 패킷 속도를 제한합니다.

DDoS 보호는 공격 트래픽을 차단하고, 나머지 트래픽을 의도하는 대상으로 전달합니다. 공격이 감지되면 몇 분 안에 Azure Monitor 메트릭을 통해 알림이 전송됩니다. DDoS Protection 표준 원격 분석에 대한 로깅을 구성하면 추후 분석 시 사용할 수 있는 옵션에 로그를 작성할 수 있습니다. DDoS Protection 표준을 위한 Azure Monitor의 메트릭 데이터는 30일 동안 보존됩니다.

Microsoft는 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)와 협력하여 시뮬레이션을 위해 DDoS Protection을 사용하도록 설정된 공용 IP 주소에 대한 트래픽을 생성할 수 있는 인터페이스를 구축했습니다. BreakPoint Cloud 시뮬레이션을 사용하여 수행할 수 있는 작업은 다음과 같습니다.

- Microsoft Azure DDoS Protection 표준이 DDoS 공격으로부터 Azure 리소스를 보호하는 방법 확인
- DDoS 공격을 받는 동안 인시던트 응답 프로세스를 최적화합니다.
- DDoS 규정 준수 문서화
- 네트워크 보안 팀을 교육합니다.

## <a name="next-steps"></a>다음 단계

- [DDoS Protection 표준 구성](manage-ddos-protection.md)

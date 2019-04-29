---
title: Azure 네트워크 아키텍처
description: 이 문서에서는 Microsoft Azure 인프라 네트워크에 대한 일반적인 설명을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: 48a7e52d4284e5c2db1d77d24d91fd4701aad8d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587162"
---
# <a name="azure-network-architecture"></a>Azure 네트워크 아키텍처
Azure 네트워크 아키텍처는 별도의 하드웨어 계층을 사용하여 업계 표준 코어/배포/액세스 모델의 수정 버전을 따릅니다. 계층은 다음과 같습니다.

- 코어(데이터 센터 라우터)
- 배포(액세스 라우터 및 L2 집계). 배포 계층은 L3 라우팅과 L2 스위칭을 구분합니다.
- 액세스(L2 호스트 스위치)

네트워크 아키텍처에는 두 수준의 2계층 스위치가 있습니다. 한 계층이 다른 계층에서 트래픽을 집계합니다. 두 번째 계층은 중복성을 줄이기 위해 반복됩니다. 이 아키텍처는 더 유연한 VLAN 공간을 제공하고 포트 크기 조정을 향상합니다. 아키텍처는 L2 및 L3을 구분하여 네트워크의 각기 다른 계층에서 하드웨어를 사용할 수 있게 하고, 한 계층의 결함을 최소화하여 다른 계층에 영향을 주지 않도록 합니다. 트렁크를 사용하면 L3 인프라에 대한 연결과 같은 리소스 공유가 가능합니다.

## <a name="network-configuration"></a>네트워크 구성
데이터 센터 내에서 Azure 클러스터의 네트워크 아키텍처를 구성하는 디바이스는 다음과 같습니다.

- 라우터(데이터 센터, 액세스 라우터 및 경계 리프 라우터)
- 스위치(집계 및 Top of Rack 스위치)
- Digi CM
- 전력 배포 장치

Azure에는 두 개의 개별 아키텍처가 있습니다. 일부 기존 Azure 고객 및 공유 서비스는 DLA(기본 LAN 아키텍처)에 상주하는 반면, 새로운 지역 및 가상 고객은 Q10(Quantum 10) 아키텍처에 상주합니다. DLA 아키텍처는 활성/수동 액세스 라우터와 액세스 라우터에 적용되는 보안 액세스 제어 목록을 사용하는 기존의 트리 디자인입니다. Quantum 10 아키텍처는 라우터에서 ACL이 적용되지 않는 라우터의 폐쇄형/메시 디자인입니다. 대신, ACL은 SLB(소프트웨어 부하 분산) 또는 소프트웨어 정의 VLAN을 통해 라우팅 아래에 적용됩니다.

다음 다이어그램은 Azure 클러스터 내의 네트워크 아키텍처에 대한 대략적인 개요를 제공합니다.

![Azure 네트워크의 다이어그램][1]

### <a name="quantum-10-devices"></a>Quantum 10 디바이스
Quantum 10 디자인은 폐쇄형/메시 디자인의 여러 디바이스에 걸쳐 3계층 전환 분산을 수행합니다. Q10 디자인의 이점은 기능과 기존 네트워크 인프라의 크기를 조정할 수 있는 능력이 더 많이 향상된다는 것입니다. 이 디자인은 경계 리프 라우터, 스파인 스위치 및 Top of Rack 라우터를 사용하여 여러 경로를 통해 트래픽을 클러스터에 전달함으로써 내결함성을 허용합니다. 하드웨어 디바이스 대신 소프트웨어 부하 분산이 네트워크 주소 변환과 같은 보안 서비스를 처리합니다.

### <a name="access-routers"></a>액세스 라우터
배포/액세스 L3 라우터(AR)는 배포 및 액세스 계층에 대한 기본 라우팅 기능을 수행합니다. 이러한 디바이스는 쌍으로 배포되며 서브넷에 대한 기본 게이트웨이입니다. 각 AR 쌍은 용량에 따라 여러 개의 L2 집계 스위치 쌍을 지원할 수 있습니다. 최대 개수는 디바이스의 용량 및 장애 도메인에 따라 달라집니다. 일반적인 개수는 AR 쌍당 L2 집계 스위치 쌍 3개입니다.

### <a name="l2-aggregation-switches"></a>L2 집계 스위치  
이러한 디바이스는 L2 트래픽에 대한 집계 지점 역할을 합니다. L2 패브릭에 대한 배포 계층이며, 많은 양의 트래픽을 처리할 수 있습니다. 이러한 디바이스는 트래픽을 집계하기 때문에 802.1q 기능과 포트 집계, 10GE 등의 고대역폭 기술이 필요합니다.

### <a name="l2-host-switches"></a>L2 호스트 스위치
호스트는 이러한 스위치에 직접 연결됩니다. 랙 탑재 스위치 또는 섀시 배포일 수 있습니다. 802.1q 표준은 하나의 VLAN을 원시 VLAN으로 지정하여 해당 VLAN을 표준(태그 없음) 이더넷 프레이밍으로 처리할 수 있게 합니다. 정상적인 상황에서 원시 VLAN의 프레임은 802.1q 트렁크 포트에서 태그가 지정되지 않은 상태로 전송 및 수신됩니다. 이 기능은 802.1q로의 마이그레이션 및 802.1q를 지원하지 않는 디바이스와의 호환성을 위해 설계되었습니다. 이 아키텍처에서는 네트워크 인프라만 원시 VLAN을 사용합니다.

이 아키텍처는 원시 VLAN을 선택하기 위해 표준을 지정합니다. 표준은 가능한 경우 AR 디바이스에 모든 트렁크 및 L2Aggregation-L2Aggregation 트렁크에 대해 고유한 원시 VLAN이 있도록 보장합니다. L2Aggregation-L2Host 스위치 트렁크에는 기본이 아닌 원시 VLAN이 있습니다.

### <a name="link-aggregation-8023ad"></a>링크 집계(802.3ad)
링크 집계를 사용하면 여러 개의 개별 링크를 번들로 묶어 단일 논리 링크로 처리할 수 있습니다. 운영 디버깅을 용이하게 하려면 포트 채널 인터페이스를 지정하는 데 사용된 번호를 표준화해야 합니다. 나머지 네트워크는 포트-채널의 양쪽 끝에서 동일한 번호를 사용합니다.

L2Agg-L2Host 스위치에 지정된 번호는 L2Agg 쪽에서 사용되는 포트 채널 번호입니다. 번호의 범위가 L2Host 쪽에서 더 제한적이므로 표준에서는 L2Host 쪽에서 1과 2 숫자를 사용합니다. 이러한 숫자는 각각 "a" 쪽 및 "b" 쪽으로 가는 포트 채널을 참조합니다.

### <a name="vlans"></a>VLAN
네트워크 아키텍처는 VLAN을 사용하여 서버를 모두 단일 브로드캐스트 도메인으로 그룹화합니다. VLAN 번호는 1~4094 번호가 지정된 VLAN을 지원하는 802.1q 표준을 따릅니다.

### <a name="customer-vlans"></a>고객 VLAN
솔루션의 분리 및 아키텍처 요구 사항을 충족하기 위해 Azure Portal을 통해 배포할 수 있는 다양한 VLAN 구현 옵션을 보유하고 있습니다. 이러한 솔루션을 가상 머신을 통해 배포합니다. 고객 참조 아키텍처 예제는 [Azure 참조 아키텍처](https://docs.microsoft.com/azure/architecture/reference-architectures/)를 참조하세요.

### <a name="edge-architecture"></a>에지 아키텍처
Azure 데이터 센터는 고도로 중복되고 효율적으로 프로비전된 네트워크 인프라를 기반으로 합니다. Microsoft는 "N+1"(필요한 개수+1)개의 중복 아키텍처 이상으로 Azure 데이터 센터 내에서 네트워크를 구현합니다. 데이터 센터 내 장애 조치 및 데이터 센터 간 장애 조치 기능 모두는 네트워크 및 서비스 가용성을 보장합니다. 외부적으로 데이터 센터에는 고대역폭의 전용 네트워크 회로를 통해 제공됩니다. 이러한 회로는 여러 피어링 지점에서 1,200여 글로벌 인터넷 서비스 공급자를 통해 속성을 중복해서 연결합니다. 이렇게 하면 네트워크를 통해 2,000Gbps의 잠재적인 에지 용량을 초과하여 제공합니다.

Azure 네트워크의 에지 및 액세스 계층에 있는 필터링 라우터는 패킷 수준에서 잘 설정된 보안을 제공하며, Azure에 대한 무단 연결 시도를 방지하는 데 도움이 됩니다. 이 라우터는 패킷의 실제 내용에 예상된 형식의 데이터가 포함되고 예상되는 클라이언트/서버 통신 체계를 준수하도록 하는 데 도움이 됩니다. Azure는 다음과 같은 네트워크 분리 및 액세스 제어 구성 요소로 계층적으로 구성된 아키텍처를 구현합니다.

- **에지 라우터**. 애플리케이션 환경을 인터넷과 분리합니다. 에지 라우터는 스푸핑 방지 보호를 제공하고 ACL을 사용하여 액세스를 제한하도록 설계되었습니다.
- **배포(액세스) 라우터**. Microsoft에서 승인한 IP 주소만 허용하고, 스푸핑 방지를 제공하고, ACL을 사용하여 연결을 설정합니다.

### <a name="ddos-mitigation"></a>DDoS 완화
DDoS(분산 서비스 거부) 공격은 계속해서 온라인 서비스의 안정성에 대한 실질적인 위협이 되고 있습니다. 공격의 대상이 더 많아지고 더 정교해지고 Microsoft에서 제공하는 서비스가 지리적으로 더 다양해짐에 따라 이러한 공격의 영향을 식별하고 최소화하는 것이 최우선 과제입니다.

[Azure DDoS Protection 표준](../virtual-network/ddos-protection-overview.md)은 DDoS 공격으로부터 보호합니다. 자세한 내용은 [Azure DDoS Protection: 모범 사례 및 참조 아키텍처](azure-ddos-best-practices.md)를 참조하세요.

> [!NOTE]
> Microsoft는 기본적으로 모든 Azure 고객에게 DDoS 보호를 제공합니다.
>
>

## <a name="network-connection-rules"></a>네트워크 연결 규칙
해당 네트워크에서 Azure는 패킷 수준에서 보안을 제공하는 에지 라우터를 배포하여 Azure에 대한 무단 연결 시도를 방지합니다. 에지 라우터는 패킷의 실제 내용에 예상된 형식의 데이터가 포함되고 예상되는 클라이언트/서버 통신 체계를 준수하도록 보장합니다.

에지 라우터는 애플리케이션 환경을 인터넷과 분리합니다. 이러한 라우터는 스푸핑 방지 보호를 제공하고 ACL을 사용하여 액세스를 제한하도록 설계되었습니다. Microsoft는 계층화된 ACL 접근 방식을 사용하여 에지 라우터를 구성하여 에지 라우터 및 액세스 라우터를 전송할 수 있는 네트워크 프로토콜을 제한합니다.

Microsoft는 네트워크 디바이스를 액세스 및 에지 위치에 배치하여 수신 또는 송신 필터가 적용되는 경계 지점 역할을 수행합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [심층 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png

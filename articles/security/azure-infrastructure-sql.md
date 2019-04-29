---
title: Azure SQL Database 보안 기능
description: 이 문서에서는 Azure SQL Database가 Azure의 고객 데이터를 보호하는 일반적인 방법을 설명합니다.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121659"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database 보안 기능    
Azure SQL Database는 Azure에서 관계형 데이터베이스 서비스를 제공합니다. 고객 데이터를 보호하고 고객이 관계형 데이터베이스 서비스에서 기대하는 강력한 보안 기능을 제공하기 위해 SQL Database에는 자체의 보안 기능 집합이 있습니다. 이러한 기능은 Azure에서 상속된 제어를 기반으로 합니다.

## <a name="security-capabilities"></a>보안 기능

### <a name="usage-of-the-tds-protocol"></a>TDS 프로토콜 사용
Azure SQL Database는 TCP/1433 기본 포트를 통해서만 데이터베이스에 액세스해야 하는 TDS(Tabular Data Stream) 프로토콜만 지원합니다.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database 방화벽
고객의 데이터를 보호하기 위해 Azure SQL Database에는 아래와 같이 SQL Database 서버에 대한 모든 액세스를 차단하는 방화벽 기능이 기본적으로 포함되어 있습니다.

![Azure SQL Database 방화벽][1]

게이트웨이 방화벽은 고객이 허용 가능한 IP 주소 범위를 세밀하게 제어할 수 있는 주소 제한 기능을 제공합니다. 방화벽은 각 요청의 원래 IP 주소에 따라 액세스 권한을 부여합니다.

고객은 관리 포털을 사용하거나 Azure SQL Database Management REST API를 사용하여 프로그래밍 방식으로 방화벽을 구성할 수 있습니다. Azure SQL Database 게이트웨이 방화벽은 기본적으로 Azure SQL 데이터베이스 인스턴스에 대한 모든 고객 TDS 액세스를 차단합니다. 고객은 원본/대상 인터넷 주소, 프로토콜 및 포트 번호를 통한 Azure SQL Database 연결을 허용하려면 ACL(액세스 제어 목록)을 사용하여 액세스를 구성해야 합니다.

### <a name="dosguard"></a>DoSGuard
DoSGuard라는 SQL Database 게이트웨이 서비스를 사용하면 DoS(서비스 거부) 공격을 줄일 수 있습니다. DoSGuard는 IP 주소에서 실패한 로그인을 활발히 추적합니다. 특정 기간 내에 특정 IP 주소에서 로그인 실패가 여러 번 발생하면 미리 정의된 기간 동안 서비스의 모든 리소스에 액세스할 수 없도록 해당 IP 주소가 차단됩니다.

또한 Azure SQL Database 게이트웨이는 다음 작업을 수행합니다.

- 데이터베이스 서버에 연결할 때 TDS FIPS 140-2 인증 암호화 연결을 구현하기 위한 보안 채널 기능 협상.
- 클라이언트의 연결을 수락하는 동안 상태 저장 TDS 패킷 검사. 게이트웨이는 연결 정보의 유효성을 검사하고, 연결 문자열에 지정된 데이터베이스 이름에 따라 TDS 패킷을 적절한 물리적 서버로 전달합니다.

Azure SQL Database 서비스의 가장 중요한 네트워크 보안 원칙은 서비스가 작동하는 데 필요한 연결 및 통신만 허용하는 것입니다. 다른 모든 포트, 프로토콜 및 연결은 기본적으로 차단됩니다. VLAN(가상 로컬 영역 네트워크) 및 ACL은 원본/대상 네트워크, 프로토콜 및 포트 번호로 네트워크 통신을 제한하는 데 사용됩니다.

네트워크 기반 ACL을 구현하도록 승인된 메커니즘에는 라우터 및 부하 분산 장치의 ACL이 포함됩니다. 이러한 메커니즘은 고객이 구성하는 Azure 네트워킹, 게스트 VM 방화벽 및 Azure SQL Database 게이트웨이 방화벽 규칙으로 관리됩니다.

## <a name="data-segregation-and-customer-isolation"></a>데이터 분리 및 고객 격리
Azure 프로덕션 네트워크는 공개적으로 액세스할 수 있는 시스템 구성 요소가 내부 리소스와 분리되도록 구성되어 있습니다. 물리적 및 논리적 경계는 공용 Azure Portal에 대한 액세스를 제공하는 웹 서버와 고객 애플리케이션 인스턴스 및 고객 데이터가 상주하는 기본 Azure 가상 인프라 사이에 존재합니다.

공개적으로 액세스할 수 있는 모든 정보는 Azure 프로덕션 네트워크 내에서 관리됩니다. 프로덕션 네트워크에서는 2단계 인증 및 경계 보호 메커니즘이 적용되며, 이전 섹션에서 설명한 방화벽 및 보안 기능 집합을 사용하고, 다음 섹션에 설명한 대로 데이터 격리 함수를 사용합니다.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>권한이 없는 시스템 및 FC 격리
FC(패브릭 컨트롤러)는 Azure 패브릭의 중앙 오케스트레이터이기 때문에, 특히 고객 애플리케이션 내에서 잠재적으로 손상될 수 있는 FA로부터 위협을 완화하기 위한 중요한 제어가 마련되어 있습니다. FC는 디바이스 정보(예: MAC 주소)가 FC 내에 미리 로드되지 않은 하드웨어를 인식하지 못합니다. FC의 DHCP 서버는 부팅할 노드의 MAC 주소 목록을 구성했습니다. 권한이 없는 시스템은 연결되는 경우에도 패브릭 인벤토리에 통합되지 않으므로 패브릭 인벤토리 내의 모든 시스템과 연결되지 않거나 통신 권한이 부여되지 않습니다. 이렇게 하면 권한이 없는 시스템에서 FC와 통신하고 VLAN 및 Azure에 액세스할 위험을 줄입니다.

### <a name="vlan-isolation"></a>VLAN 격리
Azure 프로덕션 네트워크는 논리적으로 다음 세 개의 기본 VLAN으로 분리되어 있습니다.

- 기본 VLAN: 신뢰할 수 없는 고객 노드를 상호 연결합니다.
- FC VLAN: 신뢰할 수 있는 FC 및 지원 시스템을 포함합니다.
- 디바이스 VLAN: 신뢰할 수 있는 네트워크 및 기타 인프라 디바이스를 포함합니다.

### <a name="packet-filtering"></a>패킷 필터링
노드의 루트 OS와 게스트 OS에 구현된 IPFilter 및 소프트웨어 방화벽은 연결 제한을 적용하고 VM 간에 허가되지 않은 트래픽을 방지합니다.

### <a name="hypervisor-root-os-and-guest-vms"></a>하이퍼바이저, 루트 OS 및 게스트 VM
게스트 VM과의 루트 OS 격리 및 게스트 VM 간의 루트 OS 격리는 하이퍼바이저 및 루트 OS에서 관리됩니다.

### <a name="types-of-rules-on-firewalls"></a>방화벽 규칙 유형
규칙은 다음과 같이 정의됩니다.

{Src(보안 대응 센터) IP, Src 포트, 대상 IP, 대상 포트, 대상 프로토콜, 수신/송신, 상태 저장/상태 비저장, 상태 저장 흐름 시간 제한}

SYN(Synchronous idle character) 패킷은 규칙 중 하나에서 허용하는 경우에만 허용되거나 거부됩니다. TCP의 경우 Azure는 VM 내부 또는 외부로의 비SYN 패킷만 허용한다는 원칙이 있는 상태 비저장 규칙을 사용합니다. 보안 전제는 이전에 SYN 패킷을 본 적이 없는 경우 모든 호스트 스택에서 비SYN을 무시하는 복원력이 있다는 것입니다. TCP 프로토콜 자체는 상태 저장이며, 상태 비저장 SYN 기반 규칙과 결합하여 상태 저장 구현의 전체 동작을 수행합니다.

UDP(User Datagram Protocol)의 경우 Azure는 상태 저장 규칙을 사용합니다. UDP 패킷이 규칙과 일치할 때마다 역방향 흐름이 다른 방향으로 만들어집니다. 이 흐름에는 기본 제공 시간 제한이 있습니다.

고객은 Azure에서 제공하는 것을 기반으로 하여 자신의 방화벽을 설정해야 합니다. 여기서 고객은 인바운드 및 아웃바운드 트래픽에 대한 규칙을 정의할 수 있습니다.

### <a name="production-configuration-management"></a>프로덕션 구성 관리
표준 보안 구성은 Azure 및 Azure SQL Database의 각 운영 팀에서 관리합니다. 프로덕션 시스템에 대한 모든 구성 변경은 중앙 추적 시스템을 통해 문서화되고 추적됩니다. 소프트웨어 및 하드웨어 변경도 중앙 추적 시스템을 통해 추적됩니다. ACL과 관련된 네트워킹 변경은 ACL 관리 서비스를 통해 추적됩니다.

Azure의 모든 구성 변경은 스테이징 환경에서 개발되고 테스트되며, 그 이후 프로덕션 환경에 배포됩니다. 소프트웨어 빌드는 테스트의 일환으로 검토됩니다. 보안 및 개인 정보 보호 검사는 항목 검사 목록 조건의 일부로 검토됩니다. 변경 내용은 각 배포 팀에서 예약된 간격으로 배포합니다. 릴리스는 프로덕션 환경에 배포되기 전에 각 배포 팀 담당자가 검토하고 서명합니다.

변경에 대한 성공 여부를 모니터링합니다. 실패 시나리오에서는 변경이 이전 상태로 롤백되거나, 오류를 해결하기 위해 지정된 담당자의 승인을 통해 핫픽스가 배포됩니다. 소스 디포, Git, TFS, MDS(Master Data Services), 실행기, Azure 보안 모니터링, FC 및 WinFabric 플랫폼은 Azure 가상 환경의 구성 설정을 중앙에서 관리, 적용 및 확인하는 데 사용됩니다.

마찬가지로 하드웨어 및 네트워크 변경은 빌드 요구 사항을 준수하는지 평가하는 유효성 검사 단계를 설정합니다. 릴리스는 스택 전체에서 각 그룹의 조정된 CAB(변경 자문 위원회)를 통해 검토 및 승인됩니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [심층 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png

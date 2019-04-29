---
title: 독립 실행형 Service Fabric 클러스터 개요 | Microsoft Docs
description: 서비스 패브릭 클러스터는 Windows Server 및 Linux에서 실행됩니다. 즉, Windows Server 또는 Linux를 실행할 수 있는 모든 위치에 서비스 패브릭 애플리케이션을 배포 및 호스트할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: b4b7759d1dc23c1a1b3a9b5aeb2a181923e14d40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543739"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Service Fabric 독립 실행형 클러스터 개요

Service Fabric 클러스터는 마이크로 서비스가 배포되고 관리되는 네트워크로 연결된 가상 또는 실제 머신 집합입니다. 클러스터의 일부인 컴퓨터나 VM을 클러스터 노드라고 합니다. 클러스터의 규모를 수천 개의 노드로 확장할 수 있습니다. 새 노드를 클러스터에 추가하면 Service Fabric이 증가된 수의 노드에서 서비스 파티션 복제본 및 인스턴스의 균형을 조정합니다. 전반적인 애플리케이션 성능이 향상되고 메모리 액세스에 대한 경합이 감소합니다. 클러스터의 노드가 효율적으로 사용되지 않는 경우 클러스터의 노드 수를 줄일 수 있습니다. Service Fabric은 각 노드의 하드웨어를 보다 효율적으로 사용할 수 있도록 감소된 노드 수에 맞게 파티션 복제본 및 인스턴스의 균형을 다시 조정합니다.

클러스터의 노드 세트에 대한 크기, 번호 및 속성이 노드 형식에 정의됩니다. 각 노드 형식은 독립적으로 확장 또는 축소되고, 다른 포트의 집합을 열며 다른 용량 메트릭을 가질 수 있습니다. 노드 형식은 "프런트 엔드" 또는 "백 엔드"와 같은 클러스터 노드 집합에 대한 역할을 정의하는 데 사용됩니다. 클러스터에 둘 이상의 노드 형식이 있을 수 있지만 주 노드 형식에는 프로덕션 클러스터에 대한 최소 5개의 VM(또는 테스트 클러스터에 대한 최소 3개의 VM)이 있어야 합니다. [Service Fabric 시스템 서비스](service-fabric-technical-overview.md#system-services)는 주 노드 형식의 노드에 배치됩니다.

서비스 패브릭 클러스터 온-프레미스를 만드는 프로세스는 VM 집합이 있는 클라우드를 선택하여 거기에 클러스터를 만드는 프로세스와 비슷합니다. VM을 프로비전하는 초기 단계는 사용하는 클라우드 공급자 또는 온-프레미스 환경에 의해 제어됩니다. VM을 준비하고 VM 간에 네트워크 연결을 설정한 후에는 동일한 방법으로 서비스 패브릭 패키지를 설정하고, 클러스터 설정을 편집하고, 클러스터 만들기 및 관리 스크립트를 실행합니다. 이렇게 하면 새 호스팅 환경을 대상으로 선택할 때 Service Fabric 클러스터 운영 및 관리에 대한 지식과 경험이 전달됩니다.

## <a name="cluster-security"></a>클러스터 보안
서비스 패브릭 클러스터는 사용자가 소유하는 리소스입니다.  사용자는 권한이 없는 사용자가 연결되는 것을 방지하기 위해 클러스터를 보호해야 합니다. 보안 클러스터는 클러스터에서 프로덕션 워크로드를 실행하는 경우에 특히 중요합니다.

### <a name="node-to-node-security"></a>노드 간 보안
노드 간 보안은 클러스터의 VM 또는 컴퓨터 간 통신을 보호합니다. 이 보안 시나리오를 통해 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 애플리케이션 및 클러스터의 서비스에 참여할 수 있습니다. 서비스 패브릭은 클러스터에 보안 적용을 하고 애플리케이션 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다.  클러스터 인증서는 클러스터 트래픽의 보안을 유지하고 클러스터 및 서버 인증을 제공하는 데 필요합니다.  자체 서명된 인증서를 테스트 클러스터에 사용할 수 있지만 프로덕션 클러스터 보안 유지에는 신뢰할 수 있는 인증 기관의 인증서를 사용해야 합니다.

Windows 독립 실행형 클러스터에 대해 Windows 보안을 설정할 수도 있습니다. Windows Server 2012 R2 및 Windows Active Directory가 설치된 경우 그룹 관리 서비스 계정으로 Windows 보안을 사용하는 것이 좋습니다. 그렇지 않은 경우 Windows 계정으로 Windows 보안을 사용합니다.

자세한 내용은 [노드 간 보안](service-fabric-cluster-security.md#node-to-node-security)을 참조하세요.

### <a name="client-to-node-security"></a>클라이언트-노드 보안
클라이언트-노드 보안은 클라이언트를 인증하고 클라이언트와 클러스터의 개별 노드 간 통신을 보호하도록 합니다. 이 보안 유형을 통해 인증된 사용자만 클러스터 및 클러스터에 배포된 애플리케이션에 액세스할 수 있도록 합니다. 클라이언트는 X.509 인증서 보안 자격 증명을 통해 고유하게 식별됩니다. 개수에 관계없이 선택적 클라이언트 인증서를 사용하여 클러스터에서 관리자 또는 사용자 클라이언트를 인증할 수 있습니다.

클라이언트 인증서 외에, Azure Active Directory도 클러스터에서 클라이언트를 인증하도록 구성할 수 있습니다.

자세한 내용은 [클라이언트-노드 보안](service-fabric-cluster-security.md#client-to-node-security)을 참조하세요.

### <a name="role-based-access-control-rbac"></a>역할 기반 Access Control(RBAC)
또한 Service Fabric은 다른 사용자 그룹의 특정 클러스터 작업에 대한 액세스를 제한하기 위해 액세스 제어를 지원합니다. 이를 통해 클러스터의 보안을 강화합니다. 클러스터에 연결하는 클라이언트에 대해 두 가지 액세스 제어 유형인 관리자 역할 및 사용자 역할이 지원됩니다.  

자세한 내용은 [RBAC(역할 기반 액세스 제어)](service-fabric-cluster-security.md#role-based-access-control-rbac)를 참조하세요.

## <a name="scaling"></a>확장

애플리케이션 수요는 시간이 지남에 따라 달라집니다. 늘어난 애플리케이션 워크로드나 네트워크 트래픽을 충족하기 위해 클러스터 리소스를 늘리고, 수요가 줄어들면 클러스터 리소스를 줄여야 할 수 있습니다. Service Fabric 클러스터를 만든 후에 수평으로(노드 수 변경) 또는 수직으로(노드의 리소스 변경) 클러스터 크기를 조정할 수 있습니다. 클러스터에서 워크로드가 실행되는 경우에도 언제든지 클러스터의 크기를 조정할 수 있습니다. 클러스터의 크기를 조정하면 애플리케이션 크기도 자동으로 조정됩니다.

자세한 내용은 [독립 실행형 클러스터 크기 조정](service-fabric-cluster-scaling-standalone.md)을 참조하세요.

## <a name="upgrading"></a>업그레이드 중

독립 실행형 클러스터는 사용자가 전적으로 소유하는 리소스입니다. 기본 OS에 패치를 적용하고 패브릭 업그레이드를 시작하는 작업은 사용자가 수행해야 합니다. Microsoft에서 새로운 버전을 릴리스하거나 원하는 런타임 버전을 선택하도록 한 경우 클러스터가 자동 런타임 업그레이드를 수신하도록 설정할 수 있습니다. 패브릭 업그레이드 외에도 OS를 패치하고 인증서 또는 애플리케이션 포트와 같은 클러스터 구성을 업데이트할 수 있습니다. 

자세한 내용은 [독립 실행형 클러스터 업그레이드](service-fabric-cluster-upgrade-standalone.md)를 참조하세요.

## <a name="supported-operating-systems"></a>지원되는 운영 체제
다음 운영 체제를 실행하는 컴퓨터 또는 VM에서 클러스터를 만들 수 있습니다(Linux는 아직 지원되지 않음).

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>다음 단계
독립 실행형 클러스터의 [보호](service-fabric-cluster-security.md), [크기 조정](service-fabric-cluster-scaling-standalone.md) 및 [업그레이드](service-fabric-cluster-upgrade-standalone.md)에 대해 자세히 알아봅니다.

[Service Fabric 지원 옵션](service-fabric-support.md)에 대해 알아봅니다.
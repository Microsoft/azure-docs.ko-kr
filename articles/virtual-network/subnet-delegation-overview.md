---
title: Azure virtual network의 서브넷 위임 이란?
description: Azure virtual network의 서브넷 위임에 대해 알아보기
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281338"
---
# <a name="what-is-subnet-delegation"></a>서브넷 위임 이란?

서브넷 위임을 사용 하면 가상 네트워크에 삽입 해야 하는 Azure PaaS 서비스에 대 한 특정 서브넷을 지정할 수 있습니다. 서브넷 위임은 Azure 서비스를 가상 네트워크에 통합 하는 관리에 대 한 모든 권한을 고객에 게 제공 합니다.

Azure 서비스에 서브넷을 위임 하는 경우 해당 서비스에서 해당 서브넷에 대 한 몇 가지 기본 네트워크 구성 규칙을 설정 하 여 Azure 서비스에서 해당 인스턴스를 안정적으로 작동할 수 있도록 합니다. 결과적으로 Azure 서비스는 다음과 같은 배포 전 또는 배포 후 조건을 설정할 수 있습니다.
- 공유 및 전용 서브넷에 서비스를 배포 합니다.
- 서비스가 정상적으로 작동 하는 데 필요한 네트워크 의도 정책 집합을 서비스에 추가 합니다.

##  <a name="advantages-of-subnet-delegation"></a>서브넷 위임의 이점

특정 서비스에 서브넷을 위임 하면 다음과 같은 이점이 있습니다.

- 하나 이상의 Azure 서비스에 대 한 서브넷을 지정 하 고 요구 사항에 따라 서브넷의 인스턴스를 관리 하는 데 도움이 됩니다. 예를 들어 가상 네트워크 소유자는 다음과 같이 리소스 및 액세스를 보다 효율적으로 관리 하기 위해 위임 된 서브넷에 대해 다음을 정의할 수 있습니다.
    - 네트워크 보안 그룹을 사용 하 여 트래픽 정책을 필터링 합니다.
    - 사용자 정의 경로를 사용 하는 라우팅 정책
    - 서비스 엔드포인트 구성과 서비스 통합
- 는 네트워크 의도 정책 형태로 배포의 사전 조건을 정의 하 여 삽입 된 서비스를 가상 네트워크와 보다 효과적으로 통합할 수 있습니다. 이렇게 하면 삽입 된 서비스의 기능에 영향을 줄 수 있는 모든 작업을 PUT에서 차단할 수 있습니다.


## <a name="who-can-delegate"></a>위임할 수 있는 사람은 누구 인가요?
서브넷 위임은 특정 Azure 서비스에 대 한 서브넷 중 하나를 지정 하기 위해 가상 네트워크 소유자가 수행 해야 하는 연습입니다. 그러면 Azure 서비스는 고객 작업에 의해 소비 되도록 인스턴스를이 서브넷에 배포 합니다.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>서브넷에 대 한 서브넷 위임의 영향
각 Azure 서비스는 사용자가 수행 하는 속성을 정의 하거나 다음과 같이 삽입 용도로 위임 된 서브넷에서 지원 하지 않는 속성을 정의할 수 있는 자체 배포 모델을 정의 합니다.
- 동일한 서브넷에 있는 다른 Azure 서비스 또는 v m/가상 머신 확장 집합을 사용 하는 공유 서브넷 또는이 서비스의 인스턴스만 있는 전용 서브넷만 지원 합니다.
- 위임 된 서브넷과의 NSG 연결을 지원 합니다.
- 위임 된 서브넷과 연결 된 NSG를 다른 서브넷에 연결할 수도 있습니다.
- 위임 된 서브넷과의 경로 테이블 연결을 허용 합니다.
- 위임 된 서브넷과 연결 된 경로 테이블을 다른 서브넷과 연결할 수 있습니다.
- 위임 된 서브넷의 최소 IP 주소 수를 결정 합니다.
- 개인 IP 주소 공간 (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12)에서 사용할 위임 된 서브넷의 IP 주소 공간을 결정 합니다.
- 사용자 지정 DNS 구성에 Azure DNS 항목이 있음을 나타냅니다.

삽입 된 서비스는 다음과 같이 자체 정책을 추가할 수도 있습니다.
- **보안 정책**: 지정 된 서비스가 작동 하는 데 필요한 보안 규칙 컬렉션입니다.
- **경로 정책**: 지정 된 서비스가 작동 하는 데 필요한 경로 컬렉션입니다.

## <a name="what-subnet-delegation-does-not-do"></a>어떤 서브넷 위임이 수행 되지 않음

위임 된 서브넷에 삽입 되는 Azure 서비스에는 다음과 같은 위임 되지 않은 서브넷에 사용할 수 있는 기본 속성 집합이 있습니다.
-  Azure 서비스는 고객 서브넷에 인스턴스를 삽입할 수 있지만 기존 워크 로드에는 영향을 주지 않습니다.
-  이러한 서비스에서 적용 하는 정책 또는 경로는 유연 하 고 고객에 의해 재정의 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [서브넷 위임](manage-subnet-delegation.md)

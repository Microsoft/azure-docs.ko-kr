---
title: Azure 인프라 가용성
description: 이 아티클에서는 고객 데이터의 최대 가용성을 제공하는 중복성 수준을 설명합니다.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a9a55f61f032512be032897d5f21ece405844634
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587195"
---
# <a name="azure-infrastructure-availability"></a>Azure 인프라 가용성
Azure에서는 가상화 기술을 사용하여 수행된 광범위한 중복성을 기반으로 강력한 가용성을 제공합니다. Azure에서는 고객 데이터의 최대 가용성을 제공하는 다양한 중복성 수준을 제공합니다.

## <a name="temporary-outages-and-natural-disaster"></a>임시 작동 중단 및 자연 재해
Microsoft 클라우드 인프라 및 운영 팀은 클라우드 인프라를 디자인하고, 빌드하고, 운영하고, 보안을 개선합니다. 이 팀은 Azure 인프라가 고가용성 및 안정성, 고효율, 스마트한 확장성을 제공하도록 노력합니다. 이 팀은 보다 안전하고 사적이고 신뢰할 수 있는 클라우드를 제공합니다.

무정전 전원 공급 장치 및 엄청나게 많은 배터리는 단기 전원 중단이 발생할 경우 전기가 계속 유지되도록 보장합니다. 응급 생성기는 확장된 작동 중단 및 계획된 유지 관리에 대해 백업 전원을 제공합니다. 자연 재해가 발생하면 데이터 센터에서 현장 연료 탱크를 사용할 수 있습니다.

견고한 고속 광섬유 네트워크는 다른 주요 허브와 인터넷 사용자와 데이터 센터를 연결합니다. 대기 시간을 줄이기 위해 사용자와 가까이에 있는 계산 노드 호스트 워크로드는 지역 중복을 제공하고 전체 서비스 복원력을 증가시킵니다. 엔지니어 팀은 서비스를 영구적으로 제공하기 위해 쉬지않고 일합니다.

Microsoft에서는 고급 모니터링 및 인시던트 대응, 서비스 지원을 통한 고가용성을 보장하고 장애 조치 기능을 백업합니다. 지리적으로 분산된 Microsoft 운영 센터는 연중 무휴로 운영됩니다. Azure 네트워크는 전 세계에서 가장 큰 네트워크입니다. 광섬유 및 콘텐츠 배포 네트워크는 고성능 및 안정성을 보장하기 위해 데이터 센터 및 에지 노드를 연결합니다.

## <a name="disaster-recovery"></a>재해 복구
Azure는 데이터를 두 위치에 안전하게 보관합니다. 고객은 백업 사이트의 위치를 선택할 수 있습니다. Azure는 두 위치에서 지속적으로 고객 데이터의 정상 복제본 3개를 유지합니다.

## <a name="database-availability"></a>데이터베이스 가용성
Azure는 지속적인 데이터베이스 가용성을 사용하여 인터넷 게이트웨이를 통해 데이터베이스가 인터넷에 액세스할 수 있는지 확인합니다. 모니터링은 5분 간격으로 활성 데이터베이스의 상태를 평가합니다.

## <a name="storage-availability"></a>저장소 가용성
Azure는 확장성과 내구성이 우수한 저장소 서비스를 통해 저장소를 제공하며, 이를 통해 연결 엔드포인트를 제공합니다. 즉, 애플리케이션이 저장소 서비스에 직접 액세스할 수 있습니다. 저장소 서비스는 트랜잭션 무결성을 사용하여 들어오는 저장소 요청을 효율적으로 처리합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [심층 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)

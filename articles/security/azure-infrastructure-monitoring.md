---
title: Azure 인프라 모니터링
description: 이 문서에서는 Azure 프로덕션 네트워크 모니터링을 설명합니다.
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
ms.openlocfilehash: 88bc76116392140c533f67402642c68d714227c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591651"
---
# <a name="azure-infrastructure-monitoring"></a>Azure 인프라 모니터링   

## <a name="configuration-and-change-management"></a>구성 및 변경 관리
Azure는 매년 하드웨어, 소프트웨어 및 네트워크 디바이스의 구성 설정과 기본 구성을 검토하고 업데이트합니다. 변경 사항은 프로덕션 환경에 들어가기 전에 개발 및/또는 테스트 환경에서 개발되고, 테스트되고, 승인됩니다.

Azure 기반 서비스에 필요한 기본 구성은 Azure 보안 및 규정 준수 팀과 서비스 팀에서 검토합니다. 서비스 팀 검토는 프로덕션 서비스를 배포하기 전에 발생하는 테스트의 일부입니다.

## <a name="vulnerability-management"></a>취약점 관리
보안 업데이트 관리는 알려진 취약성으로부터 시스템을 보호하는 데 도움이 됩니다. Azure는 통합 배포 시스템을 사용하여 Microsoft 소프트웨어용 보안 업데이트의 배포 및 설치를 관리합니다. 또한 Azure는 MSRC(Microsoft 보안 대응 센터)의 리소스도 활용할 수 있습니다. MSRC는 연중 무휴 24시간 내내 보안 인시던트 및 클라우드 취약성을 식별, 모니터링, 대응 및 해결합니다.

## <a name="vulnerability-scanning"></a>취약성 검색
취약성 검색은 서버 운영 체제, 데이터베이스 및 네트워크 디바이스에서 수행됩니다. 취약성 검사는 분기에 한 번 이상 수행합니다. Azure는 독립적인 평가자와 계약하여 Azure 경계의 침투 테스트를 수행합니다. 레드 팀 연습도 정기적으로 수행되며 보안 향상을 위해 사용됩니다.

## <a name="protective-monitoring"></a>보호 모니터링
Azure Security에서 활성 모니터링에 대한 요구 사항을 정의했습니다. 서비스 팀은 이러한 요구 사항에 따라 활성 모니터링 도구를 구성합니다. 활성 모니터링 도구에는 MMA(Microsoft Monitoring Agent) 및 System Center Operations Manager가 포함됩니다. 이러한 도구는 즉각적인 조치가 필요한 상황에서 Azure 보안 담당자에게 시간 경고를 제공하도록 구성됩니다.

## <a name="incident-management"></a>인시던트 관리
Microsoft는 보안 인시던트 관리 프로세스를 구현하여 인시던트가 발생하는 경우 인시던트에 대해 조정된 대응을 용이하게 합니다.

Microsoft에서 고객 데이터의 손실, 공개 또는 변경을 유발한 고객의 장비 또는 시설에 저장된 고객 데이터에 대한 무단 액세스 또는 이러한 장비 또는 시설에 대한 무단 액세스를 인식하게 되는 경우 Microsoft에서 수행하는 작업은 다음과 같습니다.

- 고객에게 보안 인시던트를 즉시 알립니다.
- 보안 인시던트를 신속하게 조사하고, 보안 인시던트에 대한 자세한 정보를 고객에게 제공합니다.
- 보안 인시던트로 인한 영향을 완화하고 손상을 최소화하기 위한 합리적이고 신속한 조치를 취합니다.

역할을 정의하고 책임을 할당하는 인시던트 관리 프레임워크가 설정되었습니다. Azure 보안 인시던트 관리 팀은 에스컬레이션을 포함하여 보안 인시던트를 관리하고, 필요한 경우 전문가 팀의 참여를 보장합니다. Azure 운영 관리자는 보안 및 개인 정보 인시던트의 조사 및 해결을 감독합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)

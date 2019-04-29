---
title: Azure 프로덕션 운영 및 관리
description: 이 문서에서는 Azure 프로덕션 네트워크의 관리 및 운영에 대한 일반적인 설명을 제공합니다.
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
ms.openlocfilehash: 0099eb61d97f813f7adca320b47c195fa1aabbdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591469"
---
# <a name="azure-production-operations-and-management"></a>Azure 프로덕션 운영 및 관리    
Azure 프로덕션 네트워크의 관리 및 운영은 Azure 및 Azure SQL Database의 운영 팀 간에 조정된 노력입니다. 이러한 팀은 환경 내에서 여러 시스템 및 애플리케이션 성능 모니터링 도구를 사용합니다. 또한 적절한 도구를 사용하여 네트워크 장치, 서버, 서비스 및 애플리케이션 프로세스를 모니터링합니다.

Azure 환경에서 실행되는 서비스의 안전한 실행을 보장하기 위해 운영 팀에서는 다음 작업을 포함하여 여러 수준의 모니터링, 로깅 및 보고 기능을 구현합니다.

- 주로 MMA(Microsoft Monitoring Agent)는 FC(패브릭 컨트롤러) 및 루트 OS를 포함한 여러 위치에서 모니터링 및 진단 로그 정보를 수집하여 로그 파일에 기록합니다. 에이전트는 수집된 정보의 하위 집합을 미리 구성된 Azure 저장소 계정으로 푸시합니다. 또한 독립형 모니터링 및 진단 서비스는 다양한 모니터링 및 진단 로그 데이터를 읽고 해당 정보를 요약합니다. 모니터링 및 진단 서비스는 통합 로그에 정보를 씁니다. Azure는 Azure 모니터링 시스템을 확장한 사용자 지정 Azure 보안 모니터링을 사용합니다. 플랫폼의 다양한 지점에서 보안 관련 이벤트를 관찰, 분석 및 보고하는 구성 요소가 있습니다.

- Azure SQL Database Windows Fabric 플랫폼은 Azure SQL Database에 대한 관리, 배포, 개발 및 운영 감독 서비스를 제공합니다. 이 플랫폼은 분산된 다단계 배포 서비스, 상태 모니터링, 자동 복구 및 서비스 버전 준수를 제공합니다. 제공하는 서비스는 다음과 같습니다.

   - 고품질 개발 환경을 갖춘 서비스 모델링 기능(데이터 센터 클러스터는 비싸고 부족함)
   - 서비스 부트스트랩 및 유지 관리를 위한 한 번 클릭 배포 및 워크플로 업그레이드
   - 자동화된 복구 워크플로를 통한 상태 보고로 자동 복구 활성화
   - 분산 시스템의 노드 전체에서 실시간 모니터링, 경고 및 디버깅 기능
   - 분산 근본 원인 분석 및 서비스 인사이트를 위한 운영 데이터 및 메트릭의 중앙 집중식 수집
   - 배포, 변경 관리 및 모니터링을 위한 운영 도구
   - 지속적으로 실행되고 실시간으로 모니터링하는 Azure SQL Database Windows Fabric 플랫폼 및 watchdog 스크립트.

예외가 발생하면 Azure 인시던트 심사 팀에 이어 수행되는 인시던트 대응 프로세스가 활성화됩니다. 적절한 Azure 지원 담당자에게 인시던트에 응답하도록 알려줍니다. 문제 추적 및 해결은 중앙 집중식 발권 시스템에서 문서화되고 관리됩니다. 시스템 작동 시간 메트릭은 NDA(비밀 유지 계약) 및 요청에 따라 제공됩니다.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>회사 네트워크 및 프로덕션에 대한 다단계 인증 액세스
회사 네트워크 사용자 기반에는 Azure 지원 담당자가 포함됩니다. 회사 네트워크는 내부 회사 기능을 지원하며, Azure 고객 지원에 사용되는 내부 애플리케이션에 대한 액세스를 포함합니다. 회사 네트워크는 Azure 프로덕션 네트워크와 논리적 및 물리적으로 분리되어 있습니다. Azure 담당자는 Azure 워크스테이션 및 랩톱을 사용하여 회사 네트워크에 액세스합니다. 회사 네트워크 리소스에 액세스하려면 모든 사용자에게 사용자 이름과 암호를 포함한 Azure AD(Azure Active Directory) 계정이 있어야 합니다. 회사 네트워크 액세스에는 모든 Microsoft 직원, 계약자, 공급업체에 발급되고 Microsoft Information Technology에서 관리하는 Azure AD 계정이 사용됩니다. 고유한 사용자 식별자는 Microsoft의 고용 상태에 따라 담당자를 구분합니다.

내부 Azure 응용 프로그램에 대한 액세스는 AD FS(Active Directory Federation Service)를 사용한 인증을 통해 제어됩니다. AD FS는 Microsoft Information Technology에서 호스팅하는 서비스로, 보안 토큰과 사용자 클레임을 적용하여 회사 네트워크 사용자를 인증합니다. AD FS를 사용하면 내부 Azure 애플리케이션에서 Microsoft 회사 active directory 도메인에 대해 사용자를 인증할 수 있습니다. 회사 네트워크 환경에서 프로덕션 네트워크에 액세스하려면 사용자는 다단계 인증을 사용하여 인증해야 합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [심층 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure 고객 데이터 보호](azure-protection-of-customer-data.md)

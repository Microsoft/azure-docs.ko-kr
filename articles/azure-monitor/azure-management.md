---
title: Azure Management
description: Azure 관리 도구의 콘텐츠에 대한 링크가 포함된 Azure 애플리케이션 및 리소스 관리 영역에 대한 개요입니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/07/2018
ms.openlocfilehash: 337b340df10eb406fef3148b1d8bfde66b3b8ff6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979825"
---
# <a name="azure-management---monitoring"></a>Azure Government - 모니터링

Azure의 모니터링은 Azure 관리의 측면 중 하나입니다.  이 아티클은 설명서와 연결된 Azure에서 애플리케이션과 리소스를 배포하고 유지 관리하는 데 필요한 관리의 다른 영역을 간략히 설명합니다.

## <a name="management-in-azure"></a>Azure의 관리

관리는 비즈니스 애플리케이션을 유지 관리하는 데 필요한 작업과 프로세스 및 지원하는 리소스를 가리킵니다.  Azure는 Azure에서 실행 중인 애플리케이션뿐만 아니라 다른 클라우드 및 온-프레미스에 전체 관리를 제공하기 위해 작동하는 여러 서비스 및 도구입니다.  전체 관리 환경을 디자인하는 첫 번째 단계는 제공되는 다양한 도구와 다양한 관리 시나리오에 함께 사용하는 방법을 이해하는 것입니다.

다음 다이어그램에서는 애플리케이션이나 리소스를 유지 관리하는 데 필요한 관리의 다른 영역을 보여줍니다.  이러한 다른 영역은 각 영역이 리소스의 수명을 계속 승계하는 데 필요한 생명 주기 측면에서 생각해볼 수 있습니다.  이는 연속된 작업을 통해 초기 배포로 시작하고 마지막으로 사용 중지될 때 끝납니다.

![관리 기능](media/management-overview/management-capabilities.png)


다음 섹션에서는 다른 관리 영역을 간략히 설명하고 해결 의도가 있는 기본 Azure 서비스에서 자세한 콘텐츠에 대한 링크를 제공합니다.

## <a name="monitor"></a>모니터
모니터링은 비즈니스 애플리케이션 및 종속 리소스의 성능, 상태 및 가용성을 결정하기 위해 데이터를 수집 및 분석하는 작업입니다. 효과적인 모니터링 전략은 애플리케이션의 다양한 구성 요소에 대한 자세한 작동을 이해하고, 문제가 심각해지기 전에 해결할 수 있도록 중대한 문제를 사전에 알림으로써 작동 시간을 늘리는 데 도움을 줍니다. Azure의 모니터링은 주로 모니터링 데이터를 저장하기 위한 공용 저장소, 사용자 애플리케이션을 지원하는 다양한 계층의 데이터를 수집하기 위한 여러 데이터 원본 및 수집된 데이터를 분석하고 대응하기 위한 기능을 제공하는 [Azure Monitor](../azure-monitor/overview.md)에 의해 제공됩니다.

## <a name="configure"></a>구성
구성은 애플리케이션 및 리소스의 초기 배포 및 구성 그리고 패치 및 업데이트를 통한 지속적인 유지 관리를 참조합니다.  정책 및 스크립트를 통해 이러한 태스크의 Automation을 사용하면 중복을 제거하고 시간과 노력을 최소화하고 정확성과 효율성을 증가할 수 있습니다.  [Azure Automation](../automation/automation-intro.md)에서는 구성 작업을 자동화하는 일련의 서비스를 제공합니다.  프로세스 자동화를 위한 Runbook 외에도 구성과 업데이트 관리를 제공해 정책을 통해 구성을 관리하고 업데이트를 식별하고 배포하는 데 도움이 됩니다.

## <a name="govern"></a>거버넌스
거버넌스는 Azure에서 애플리케이션 및 리소스에 대한 제어를 유지 관리하는 프로세스와 메커니즘을 제공합니다.  거버넌스에는 이니셔티브 계획과 전략적 우선 순위 설정이 필요합니다.  Azure에서 거버넌스는 주로 두 서비스를 통해 구현됩니다.  [Azure Policy](../governance/policy/overview.md)는 리소스에 대해 다양한 규칙과 작업을 적용하는 정책 정의를 만들고 할당하고 관리할 수 있어 이러한 리소스를 회사 표준 및 서비스 수준 계약을 준수하는 상태로 유지합니다. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) 를 사용 하 여 Azure 리소스 및 AWS 및 Google을 포함 한 다른 클라우드 공급자에 대한 클라우드 사용량 및 비용을 추적할 수 있습니다.

## <a name="secure"></a>안전하다
애플리케이션, 리소스 및 데이터의 보안 관리에는 위협 평가하고 보안 데이터 수집 및 분석하고 애플리케이션과 리소스가 안전하게 구성 및 디자인되도록 보장하는 조합이 필요합니다.  보안 모니너링과 위협 분석은 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 포함하는 [Azure Security Center](../security-center/security-center-intro.md)에서 제공합니다.  또한 Azure에서의 보안 및 안전한 Azure 리소스 구성에 대한 지침에 대한 종합 정보는 [Azure 보안 소개](../security/fundamentals/overview.md)를 참조하면 됩니다.


## <a name="protect"></a>보호
보호는 사용자의 제어를 벗어나 중단된 경우에도 애플리케이션 및 데이터를 항상 사용할 수 있도록 보장하는 것을 가리킵니다.  Azure의 보호는 두 가지 서비스에서 제공됩니다.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md)은 클라우드 또는 온-프레미스에서 데이터의 백업 및 복구를 제공합니다.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md)는 재해 시 즉각적인 복구와 사업의 연속성을 제공하여 애플리케이션의 고가용성을 보장합니다.

## <a name="migrate"></a>마이그레이션 
마이그레이션이란 현재 온-프레미스에서 실행하는 워크로드를 Azure 클라우드로 전환하는 것을 가리킵니다.  [Azure 마이그레이션](../migrate/migrate-overview.md)은 온-프레미스 가상 머신의 성능 기반 크기 조정 및 비용 예측을 포함해 Azure에 대한 마이그레이션 적합성을 평가하는 데 도움이 되는 서비스입니다.  Azure Site Recovery를 통해 [온-프레미스에서](../site-recovery/migrate-tutorial-on-premises-azure.md) 또는 [Amazon Web Services에서](../site-recovery/migrate-tutorial-aws-azure.md) 가상 머신의 실제 마이그레이션을 수행할 수 있습니다.  [Azure 데이터베이스 마이그레이션](../dms/dms-overview.md)은 여러 데이터베이스 원본을 Azure 데이터 플랫폼으로 마이그레이션하는 데 도움이 됩니다.


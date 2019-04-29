---
title: Azure 관리 개요 - Azure 거버넌스
description: Azure 관리 도구의 콘텐츠에 대한 링크가 포함된 Azure 애플리케이션 및 리소스 관리 영역에 대한 개요입니다.
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 12/06/2018
ms.author: dacoulte
ms.openlocfilehash: f94cec7919edc6cf6ebb6618d38b8591feb1278b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683249"
---
# <a name="overview-of-management-services-in-azure"></a>Azure의 관리 서비스 개요

Azure의 거버넌스는 Azure 관리의 측면 중 하나입니다. 이 문서에서는 Azure에서 리소스를 배포 및 유지 관리하기 위해 다양한 관리 영역을 다룹니다.

관리는 비즈니스 애플리케이션을 유지 관리하는 데 필요한 작업과 프로세스 및 지원하는 리소스를 가리킵니다. Azure에는 전체 관리를 제공하기 위해 함께 작동하는 여러 서비스 및 도구가 있습니다. 이러한 서비스는 Azure의 리소스뿐만 아니라 다른 클라우드 및 온-프레미스에도 없습니다. 전체 관리 환경을 설계하는 첫 번째 단계는 다양한 도구 및 이러한 도구가 함께 작동하는 방법을 이해하는 것입니다.

다음 다이어그램에서는 애플리케이션이나 리소스를 유지 관리하는 데 필요한 관리의 다른 영역을 보여줍니다. 이러한 여러 영역을 수명 주기로 간주할 수 있습니다. 각 영역은 리소스의 수명을 계속 연속하는 데 필요 합니다. 이 리소스 수명은 초기 배포로 시작하고 연속된 작업을 거친 다음, 마지막으로 사용 중지될 때 끝납니다.

![Azure의 관리 분야](../monitoring/media/management-overview/management-capabilities.png)

단일 Azure 서비스가 특정 관리 영역의 요구 사항을 완전히 충족시키지 못합니다. 대신, 각 요구 사항은 함께 작동하는 여러 서비스를 통해 실현됩니다. Application Insights와 같은 일부 서비스는 웹 애플리케이션에 대한 대상 모니터링 기능을 제공합니다. Azure Monitor 로그와 같은 다른 서비스는 기타 서비스에 대한 관리 데이터를 저장합니다. 이 기능을 사용하면 다양한 서비스에서 수집된 다양한 유형의 데이터를 분석할 수 있습니다.

다음 섹션에서는 다른 관리 영역을 간략히 설명하고 해결 의도가 있는 기본 Azure 서비스에서 자세한 콘텐츠에 대한 링크를 제공합니다.

## <a name="monitor"></a>모니터

모니터링은 리소스의 성능, 상태 및 가용성을 감사하기 위해 데이터를 수집 및 분석하는 작업입니다. 효과적인 모니터링 전략을 사용하면 구성 요소의 작업을 이해하고 알림을 통해 가동 시간을 늘릴 수 있습니다. [Azure 애플리케이션 및 리소스 모니터링](../monitoring/monitoring-overview.md)에 사용된 다양한 서비스를 다루는 모니터링에 대한 개요를 읽습니다.

## <a name="configure"></a>구성

구성은 리소스의 초기 배포 및 구성 그리고 지속적인 유지 관리를 참조합니다.
이러한 태스크의 Automation을 사용하면 중복을 제거하고 시간과 노력을 최소화하고 정확성과 효율성을 증가할 수 있습니다. [Azure Automation](../automation/automation-intro.md)에서는 구성 작업을 자동화하는 일련의 서비스를 제공합니다. Runbook이 프로세스 자동화를 처리하는 동안 구성 및 업데이트 관리는 구성을 관리하는 데 도움을 줍니다.

## <a name="govern"></a>거버넌스

거버넌스는 Azure에서 애플리케이션 및 리소스에 대한 제어를 유지 관리하는 프로세스와 메커니즘을 제공합니다. 거버넌스에는 이니셔티브 계획과 전략적 우선 순위 설정이 필요합니다.
Azure에서 거버넌스는 주로 두 서비스를 통해 구현됩니다. [Azure Policy](./policy/overview.md)를 사용하면 정책 정의를 만들고 할당하고 관리하여 리소스에 대 한 규칙을 적용할 수 있습니다. 이 기능은 해당 리소스가 회사 표준을 준수하게 합니다. [Cloudyn에서 개발한 Azure Cost Management](../cost-management/overview.md)를 사용하면 Azure 리소스 및 기타 클라우드 공급 기업에 대한 클라우드 사용량 및 비용을 추적할 수 있습니다.

## <a name="secure"></a>보안

리소스 및 데이터의 보안을 관리합니다. 보안 프로그램에는 위협 평가, 데이터 수집 및 분석, 애플리케이션 및 리소스의 규정 준수 등이 있습니다. 보안 모니터링과 위협 분석은 하이브리드 클라우드 워크로드에 통합 보안 관리 및 고급 위협 방지를 포함하는 [Azure Security Center](../security-center/security-center-intro.md)에서 제공됩니다. Azure 리소스 보안에 대한 종합 정보 및 지침은 [Azure 보안 소개](../security/azure-security.md)를 참조하세요.

## <a name="protect"></a>보호

보호란 사용자 제어를 벗어난 중단의 경우에도 애플리케이션 및 데이터를 계속 사용할 수 있는 것을 의미합니다. Azure의 보호는 두 가지 서비스에서 제공됩니다. [Azure Backup](../backup/backup-introduction-to-azure-backup.md)은 클라우드 또는 온-프레미스에서 데이터의 백업 및 복구를 제공합니다. [Azure Site Recovery](../site-recovery/site-recovery-overview.md)는 재해 발생 동안에도 즉각적인 복구와 비즈니스 연속성을 제공합니다.

## <a name="migrate"></a>마이그레이션

마이그레이션이란 현재 온-프레미스에서 실행하는 워크로드를 Azure 클라우드로 전환하는 것을 가리킵니다.
[Azure Migrate](../migrate/migrate-overview.md)는 온-프레미스 가상 머신의 Azure에 대한 마이그레이션 적합성을 평가하는 데 도움이 되는 서비스입니다. Azure Site Recovery는 [온-프레미스에서](../site-recovery/migrate-tutorial-on-premises-azure.md) 또는 [Amazon Web Services에서](../site-recovery/migrate-tutorial-aws-azure.md) 가상 머신을 마이그레이션합니다. [Azure 데이터베이스 마이그레이션](../dms/dms-overview.md)은 데이터베이스 원본을 Azure 데이터 플랫폼으로 마이그레이션하는 데 도움이 됩니다.
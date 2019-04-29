---
title: Azure SQL Database 다중 테넌트 앱 예제 - Wingtip SaaS | Microsoft Docs
description: Azure SQL Database를 사용하는 샘플 다중 테넌트 애플리케이션인 Wingtip SaaS 예제를 통해 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 1c16ea44418d99ee1f80a7d0ef7a3e5b3f118f46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485249"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>SQL Database로 테넌트별 데이터베이스 패턴을 사용하는 다중 테넌트 SaaS 앱 소개

Wingtip SaaS 애플리케이션은 샘플 다중 테넌트 앱입니다. 이 앱은 테넌트별 데이터베이스 SaaS 애플리케이션 패턴을 사용하여 여러 테넌트에 서비스를 제공합니다. 이 앱을 통해 몇 가지 SaaS 디자인과 관리 패턴을 사용하여 SaaS 시나리오를 지원하는 Azure SQL Database의 기능을 살펴볼 수 있습니다. 신속한 실행을 위해 Wingtip SaaS 앱은 5분 이내에 배포됩니다.

애플리케이션 소스 코드 및 관리 스크립트는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 사용할 수 있습니다. 시작하기 전에 먼저 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)에서 Wingtip Tickets 관리 스크립트를 다운로드하고 차단을 해제하는 방법을 확인합니다.

## <a name="application-architecture"></a>애플리케이션 아키텍처

Wingtip SaaS 앱은 테넌트별 데이터베이스 모델을 사용합니다. SQL 탄력적 풀을 사용하여 효율성을 극대화합니다. 테넌트를 해당 데이터에 매핑하고 프로비전하는 데 카탈로그 데이터베이스가 사용됩니다. 핵심 Wingtip SaaS 애플리케이션은 세 가지 샘플 테넌트가 있는 풀 및 카탈로그 데이터베이스를 사용합니다. 카탈로그 및 테넌트 서버는 DNS 별칭을 사용하여 프로비전되었습니다. 이러한 별칭은 Wingtip 애플리케이션에서 사용하는 활성 리소스에 대한 참조를 유지 관리하는 데 사용됩니다. 이러한 별칭은 재해 복구 자습서의 복구 리소스를 가리키도록 업데이트됩니다. 여러 Wingtip SaaS 자습서를 완료하면 초기 배포에 대한 추가 기능이 생성됩니다. 분석 데이터베이스 및 데이터베이스 간 스키마 관리와 같은 추가 기능이 도입됩니다.


![Wingtip SaaS 아키텍처](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


자습서를 진행하고 앱을 사용하는 동안 데이터 계층과 관련된 SaaS 패턴에 중점을 둡니다. 즉, 데이터 계층에 집중하고 앱 자체를 과도하게 분석하지 마세요. 이러한 SaaS 패턴의 구현을 이해하는 것이 애플리케이션에서 패턴을 구현하는 데 중요합니다. 또한 특정 비즈니스 요구 사항에 필요한 수정 사항을 고려하세요.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS 자습서

앱을 배포한 후 초기 배포를 기반으로 하는 다음 자습서를 탐색합니다. 이러한 자습서에서는 SQL Database, Azure SQL Data Warehouse 및 기타 Azure 서비스의 기본 제공 기능을 활용하는 일반적인 SaaS 패턴을 살펴봅니다. 자습서에는 PowerShell 스크립트와 자세한 설명이 포함되어 있습니다. 쉽게 이해하고 애플리케이션에서 동일한 SaaS 관리 패턴을 구현할 수 있도록 설명되어 있습니다.


| 자습서 | 설명 |
|:--|:--|
| [SQL Database 다중 테넌트 SaaS 앱 예제에 대한 지침 및 팁](saas-tenancy-wingtip-app-guidance-tips.md) | PowerShell 스크립트를 다운로드한 후 실행하여 애플리케이션 부분을 준비하세요. |
|[Wingtip SaaS 애플리케이션 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)|  Azure 구독으로 Wingtip SaaS 애플리케이션을 배포하고 탐색합니다. |
|[프로비전 및 카탈로그 테넌트](saas-dbpertenant-provision-and-catalog.md)| 카탈로그 데이터베이스를 사용하여 애플리케이션을 테넌트에 연결하는 방법 및 카탈로그가 테넌트를 해당 데이터에 매핑하는 방법을 알아봅니다. |
|[성능 모니터링 및 관리](saas-dbpertenant-performance-monitoring.md)| SQL Database의 모니터링 기능을 사용하고 성능 임계값이 초과된 경우 경고를 설정하는 방법을 알아봅니다. |
|[Azure Monitor 로그를 사용 하 여 모니터링](saas-dbpertenant-log-analytics.md) | 사용 방법 알아보기 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) 여러 풀에 걸쳐 있는 많은 양의 리소스를 모니터링 합니다. |
|[단일 테넌트 복원](saas-dbpertenant-restore-single-tenant.md)| 테넌트 데이터베이스를 이전 시점으로 복원하는 방법을 알아봅니다. 또한 기존 테넌트 데이터베이스를 온라인 상태로 유지하여 병렬 데이터베이스로 복원하는 방법도 알아봅니다. |
|[테넌트 데이터베이스 스키마 관리](saas-tenancy-schema-management.md)| 모든 테넌트 데이터베이스를 대상으로 스키마와 참조 데이터를 업데이트하는 방법을 알아봅니다. |
|[교차 테넌트 분산 쿼리 실행](saas-tenancy-cross-tenant-reporting.md) | 임시 분석 데이터베이스를 만들고 모든 테 넌 트에서 실시간 분산된 쿼리를 실행 합니다.  |
|[추출된 테넌트 데이터를 이용하여 분석 실행](saas-tenancy-tenant-analytics.md) | 오프라인 분석 쿼리를 실행하기 위해 테넌트 데이터를 분석 데이터베이스 또는 데이터 웨어하우스로 추출합니다. |


## <a name="next-steps"></a>다음 단계

- [Wingtip Tickets SaaS 앱 예제를 배포하고 사용하기 위한 일반 지침과 팁](saas-tenancy-wingtip-app-guidance-tips.md)
- [Wingtip SaaS 애플리케이션 배포](saas-dbpertenant-get-started-deploy.md)

---
title: Wingtips 앱 시작 - Azure SQL Database | Microsoft Docs
description: 클라우드 환경의 Azure SQL Database에 대한 샘플 Wingtips SaaS 애플리케이션 및 데이터베이스 테넌시 모델에 대해 알아봅니다.
keywords: SQL Database 자습서
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 963d7d44ef3ef77604fc5a9faac479a9e4c91ee6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487380"
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS 애플리케이션

세 개의 샘플 각각에 동일한 *Wingtip Tickets* SaaS 애플리케이션이 구현되어 있습니다. 이 앱은 극장, 클럽 등 작은 장소를 대상으로 간단한 이벤트를 나열하며 티켓팅하는 SaaS 앱입니다. 각 장소는 앱의 테넌트이며, 장소 세부 정보, 고객, 티켓 주문, 이벤트 목록 등의 자체 데이터를 갖습니다.  관리 스크립트 및 자습서와 함께 앱을 통해 종단 간 SaaS 시나리오를 보여 줍니다. 여기에는 테넌트 프로비전, 성능 모니터링 및 관리, 스키마 관리, 테넌트 간 보고 및 분석이 포함됩니다.

## <a name="three-saas-application-and-tenancy-patterns"></a>세 개의 SaaS 애플리케이션과 테넌트 패턴

세 버전의 앱을 사용할 수 있으며, 각각 Azure SQL Database에서 다른 데이터베이스 테넌트 패턴을 보여 줍니다.  첫 번째는 자체 데이터베이스를 사용하여 테넌트별로 각각 독립 실행형 애플리케이션을 사용합니다. 두 번째는 테넌트별 데이터베이스를 사용하여 다중 테넌트 앱을 사용합니다. 세 번째 샘플은 다중 테넌트 앱과 분할된 다중 테넌트 데이터베이스를 사용합니다.

![세 가지 테넌트 패턴][image-three-tenancy-patterns]

 각 샘플에는 다양한 디자인 및 관리 패턴을 살펴볼 수 있는 자습서, 관리 스크립트와 애플리케이션 코드가 포함되어 있습니다.  각 샘플은 5분 내에 배포됩니다.  세 패턴은 모두 나란히 배포되어 디자인 및 관리의 차이를 비교할 수 있습니다.

## <a name="standalone-application-per-tenant-pattern"></a>테넌트별 독립 실행형 애플리케이션 패턴

테넌트별 독립 실행형 앱 패턴에서는 각 테넌트별 데이터베이스를 사용하여 단일 테넌트 애플리케이션을 사용합니다. 각 테넌트의 앱과 데이터베이스는 별도의 Azure 리소스 그룹에 배포됩니다. 리소스 그룹은 서비스 공급자의 구독 또는 테넌트의 구독에 배포되어 공급자가 테넌트를 대신하여 관리할 수 있습니다. 테넌트별 독립 실행형 앱 패턴은 테넌트 격리가 가장 높은 수준으로 구현되지만, 여러 테넌트 간에 리소스를 공유할 수 없기 때문에 일반적으로 비용이 가장 높습니다.  이 패턴은 비교적 복잡한 애플리케이션이 소수의 테넌트에 배포된 경우에 적합합니다.  독립 실행형 배포에서는 다른 패턴에 비해 각 테넌트별로 훨씬 간편하게 앱을 사용자 지정할 수 있습니다.  

[자습서][docs-tutorials-for-wingtip-sa] 및 GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa]의 코드를 확인합니다.

## <a name="database-per-tenant-pattern"></a>테넌트별 데이터베이스 패턴

테넌트별 데이터베이스 패턴은 테넌트 격리에 관심이 있고 공유 리소스를 비용 효율적으로 사용할 수 있는 중앙 집중식 서비스를 실행하려는 서비스 공급자에게 효과적입니다. 데이터베이스는 각 장소(또는 테넌트)에 대해 만들어지며, 모든 데이터베이스가 중앙에서 관리됩니다. 데이터베이스가 탄력적 풀에 호스트되어 비용 효율적이고 쉽게 성능을 관리할 수 있으며, 이는 테넌트의 예측할 수 없는 워크로드 패턴을 활용합니다. 카탈로그 데이터베이스는 테넌트와 해당 데이터베이스 간의 매핑을 포함합니다. 이 매핑은 [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)의 분할 맵 관리 기능을 사용하여 관리되며, 이를 통해 애플리케이션에 대한 연결을 효율적으로 관리할 수 있습니다.

GitHub[.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt]에서 [자습서][docs-tutorials-for-wingtip-dpt] 및 코드를 확인하세요.

## <a name="sharded-multi-tenant-database-pattern"></a>분할된 다중 테넌트 데이터베이스 패턴

다중 테넌트 데이터베이스는 테넌트당 낮은 비용을 원하며 테넌트 격리를 줄여도 되는 서비스 공급자에게 효과적입니다. 이 패턴을 사용하면 많은 테넌트를 개별 데이터베이스로 압축할 수 있으며 테넌트당 비용을 낮춥니다. 테넌트를 여러 데이터베이스로 분할하여 무제한에 가깝게 확장할 수 있습니다. 카탈로그 데이터베이스가 테넌트를 데이터베이스로 매핑합니다.  

이 패턴을 사용하면 여러 개의 테넌트가 하나의 데이터베이스를 사용하도록 하여 비용을 최적화하거나 테넌트마다 자체 데이터베이스를 갖도록 하여 격리를 최적화하는 *하이브리드* 모델을 구현할 수 있습니다. 테넌트가 프로비전될 때 또는 그 후에 테넌트별로 애플리케이션에 영향을 미치지 않고 적용할 수 있습니다.  이 모델은 서로 다른 테넌트 그룹을 각각 다르게 취급해야 하는 경우 효과적입니다. 예를 들어, 요금이 낮은 테넌트는 공유 데이터베이스에 할당하고, 프리미엄 테넌트는 자체 데이터베이스에 할당할 수 있습니다. 

GitHub[.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt]에서 [자습서][docs-tutorials-for-wingtip-mt] 및 코드를 확인하세요.

## <a name="next-steps"></a>다음 단계

#### <a name="conceptual-descriptions"></a>개념적 설명

- [다중 테넌트 SaaS 데이터베이스 테넌트 패턴][saas-tenancy-app-design-patterns-md]에 애플리케이션 테넌트 패턴에 대해 자세히 설명되어 있습니다.

#### <a name="tutorials-and-code"></a>자습서 및 코드

- 테넌트당 독립형 앱:
    - [독립 실행형 앱에 대한 자습서][docs-tutorials-for-wingtip-sa].
    - [독립 실행형 앱 코드(GitHub)][github-code-for-wingtip-sa].

- 테넌트당 데이터베이스:
    - [테넌트별 데이터베이스에 대한 자습서][docs-tutorials-for-wingtip-dpt].
    - [테넌트별 데이터베이스에 대한 코드, GitHub][github-code-for-wingtip-dpt].

- 분할된 다중 테넌트:
    - [분할된 다중 테넌트에 대한 자습서][docs-tutorials-for-wingtip-mt].
    - [분할된 다중 테넌트에 대한 코드, GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "세 가지 테넌트 패턴."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb


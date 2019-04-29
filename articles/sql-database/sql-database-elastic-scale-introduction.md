---
title: Azure SQL Database를 사용하여 규모 확장 | Microsoft Docs
description: SaaS(Software as a Service) 개발자는 이러한 도구를 사용하여 클라우드에서 확장 가능한 탄력적 데이터베이스를 쉽게 만들 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9f61748a489987bf6c3f38e8ebfdab660198e10a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585461"
---
# <a name="scaling-out-with-azure-sql-database"></a>Azure SQL Database를 사용하여 규모 확장
**Elastic Database** 도구를 사용하여 Azure SQL 데이터베이스의 규모를 쉽게 확장할 수 있습니다. 이러한 도구와 기능을 사용하면 **Azure SQL Database**의 데이터베이스 리소스를 사용하여 트랜잭션 워크로드에 대한 솔루션, 특히 SaaS(Software as a Service) 애플리케이션을 만들 수 있습니다. Elastic Database 기능은 다음으로 구성됩니다.

* [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md): 클라이언트 라이브러리는 분할된 데이터베이스를 만들고 유지 관리할 수 있도록 해주는 기능입니다.  [Elastic Database 도구 시작하기](sql-database-elastic-scale-get-started.md)를 참조하세요.
* [Elastic Database 분할-병합 도구](sql-database-elastic-scale-overview-split-and-merge.md): 분할된 데이터베이스 간에 데이터를 이동합니다. 이 도구는 데이터를 다중 테넌트 데이터베이스에서 단일 테넌트 데이터베이스로 또는 그 반대로 이동하는 데 유용합니다. [탄력적 데이터베이스 분할-병합 도구 자습서](sql-database-elastic-scale-configure-deploy-split-and-merge.md)를 참조하세요.
* [Elastic Database 작업](sql-database-elastic-jobs-overview.md)(미리 보기): 작업을 사용하여 많은 수의 Azure SQL 데이터베이스를 관리합니다. 작업을 사용하여 스키마 변경, 자격 증명 관리, 참조 데이터 업데이트, 성능 데이터 수집 또는 테넌트(고객) 원격 분석 컬렉션 등의 관리 작업을 쉽게 수행합니다.
* [Elastic Database 쿼리](sql-database-elastic-query-overview.md)(미리 보기): 여러 데이터베이스에 걸쳐 있는 Transact-SQL 쿼리를 실행할 수 있습니다. 이렇게 하면 Excel, Power BI, Tableau 등과 같은 보고 도구에 연결할 수 있습니다.
* [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md): 이 기능을 사용하면 Azure SQL Database의 여러 데이터베이스에 걸쳐 트랜잭션을 실행할 수 있습니다. 탄력적 데이터베이스 트랜잭션은 ADO .NET을 사용하여 .NET 애플리케이션에서 사용할 수 있고 [System.Transaction 클래스](https://msdn.microsoft.com/library/system.transactions.aspx)를 사용하여 친숙한 프로그래밍 환경과 통합될 수 있습니다.

아래 그림은 데이터베이스 컬렉션과 관련된 **Elastic Database 기능** 을 포함하는 아키텍처를 보여 줍니다.

이 그래픽에서 데이터베이스의 색은 스키마를 나타냅니다. 동일한 색의 데이터베이스는 동일한 스키마를 공유합니다.

1. **Azure SQL 데이터베이스** 집합은 분할 아키텍처를 사용하여 Azure에서 호스팅됩니다.
2. **Elastic Database 클라이언트 라이브러리**는 분할된 데이터베이스 집합을 관리하는 데 사용됩니다.
3. 데이터베이스 하위 집합은 **탄력적 풀**에 배치됩니다. ( [풀이란?](sql-database-elastic-pool.md)참조).
4. **Elastic Database 작업**은 모든 데이터베이스에 대해 예약된 또는 임시 T-SQL 스크립트를 실행합니다.
5. **분할-병합 도구** 는 데이터를 하나의 분할된 데이터베이스에서 다른 분할된 데이터베이스로 이동하는 데 사용됩니다.
6. **Elastic Database 쿼리** 를 통해 분할된 데이터베이스 집합의 모든 데이터베이스에 걸쳐 있는 쿼리를 작성할 수 있습니다.
7. **탄력적 트랜잭션**을 통해 여러 데이터베이스에 걸쳐 트랜잭션을 실행할 수 있습니다. 

![Elastic Database 도구][1]

## <a name="why-use-the-tools"></a>도구를 사용하는 이유
클라우드 응용 프로그램에 대한 탄력성 및 규모 달성은 VM 및 Blob Storage에 대해 복잡하지 않습니다. 단순히 단위를 더하거나 빼고 성능을 늘립니다. 하지만 관계형 데이터베이스의 상태 저장 데이터 처리는 과제로 남아 있습니다. 이러한 시나리오에서 발생하는 문제:

* 작업의 관계형 데이터베이스에 대한 용량 확장 및 축소
* 바쁜 최종 고객(테넌트)과 같이 데이터의 특정 하위 집합에 영향을 발생시킬 수 있는 핫스팟 관리.

일반적으로 이러한 시나리오는 애플리케이션을 지원하기 위해 대규모 데이터베이스를 서버에 투자하여 해결되었습니다. 그러나 이 옵션은 미리 정의된 상용 하드웨어에서 모든 처리가 이루어지는 클라우드에서는 제한됩니다. 대신에, 데이터 배포 및 많은 동일한 구조의 데이터베이스 간의 처리("Sharding"이라고 알려진 확장 패턴)는 비용 및 탄력성 측면에서 기존 확장 방식에 대한 대안을 제공합니다.

## <a name="horizontal-and-vertical-scaling"></a>수평 및 수직적 크기 조정
아래 그림에서는 탄력적 데이터베이스 크기를 조정할 수 있는 기본적인 방법인 조정의 가로 및 세로 크기를 보여 줍니다.

![수평 및 수직적 규모 확장][2]

수평적 크기 조정은 용량 또는 전체 성능을 조정하기 위해 데이터베이스를 추가하거나 제거하는 것을 말하며, "규모 확장"이라고도 합니다. 분할은 동일한 구조 데이터베이스 컬렉션 간의 분할된 데이터의 수평적 확장 구현을 위한 일반적인 방법입니다.  

수직적 크기 조정은 개별 데이터베이스의 컴퓨팅 크기를 늘리거나 줄이는 것을 말하며, "강화"라고도 합니다.

대부분의 클라우드 규모 데이터베이스 애플리케이션에 이러한 두 가지 전략이 결합하여 사용됩니다. 예를 들어, 서비스 애플리케이션 같은 소프트웨어는 새로운 최종고객을 프로비전하기 위해 수평적 확장을 사용할 것이고, 워크로드의 필요에 의해 개별 최종 고객의 데이터베이스 리소스를 늘리거나 줄이기 위해 수직적 확장을 사용할 것입니다.

* 수평적 크기 조정은 [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)를 사용하여 관리됩니다.
* 수직적 크기 조정은 서비스 계층을 변경하도록 Azure PowerShell cmdlet을 사용하거나 탄력적 풀에 데이터베이스를 배치하여 수행됩니다.

## <a name="sharding"></a>분할
*분할*이란 동일하게 구조화된 대량의 데이터를 여러 독립적인 데이터베이스에 분산하는 기술입니다. 특히 최종 고객 또는 비즈니스에 대한 SAAS(Software as a Service) 제품을 만드는 클라우드 개발자가 이 기법을 많이 사용합니다. 이러한 최종 고객을 보통 "테넌트"라고 합니다. 분할은 다음과 같은 여러 가지 이유로 필요할 수 있습니다.  

* 총 데이터양이 너무 커서 개별 데이터베이스의 제약 조건에 맞지 않습니다.
* 전반적인 워크로드의 트랜잭션 처리량이 개별 데이터베이스의 기능을 초과합니다.
* 테넌트는 물리적으로 서로 격리되어야 할 수 있으므로 각 테넌트에는 별도의 데이터베이스가 필요합니다.
* 데이터베이스의 다양한 섹션이 규정 준수, 성능 또는 지정학적인 이유로 인해 서로 다른 지리적 위치에 상주해야 할 수 있습니다.

분산된 디바이스에서 데이터 수집과 같은 다른 시나리오에서는 분할을 사용하여 일시적으로 구성된 데이터베이스 집합을 채울 수 있습니다. 예를 들어, 매일 또는 주별로 별도의 데이터베이스를 전용할 수 있습니다. 이 경우 분할 키는 날짜를 나타내는 정수(분할된 테이블의 모든 행에 있음)일 수 있으며, 애플리케이션에서 해당하는 범위를 포함하는 데이터베이스의 하위 세트으로 날짜 범위에 대한 정보를 검색하는 쿼리를 라우팅해야 합니다.

분할은 애플리케이션의 모든 트랜잭션이 분할 키의 단일 값으로 제한될 수 있는 경우에 가장 적합합니다. 그렇게 하면 모든 트랜잭션이 특정 데이터베이스에 로컬이 됩니다.

## <a name="multi-tenant-and-single-tenant"></a>다중 테넌트 및 단일 테넌트
일부 애플리케이션은 각 테넌트에 대해 별도의 데이터베이스를 만드는 가장 간단한 접근 방식을 사용합니다. 이 방법은 테넌트의 세분성에서 격리, 백업/복원 기능 및 리소스 크기 조정을 제공하는 **단일 테넌트 분할 패턴** 입니다. 단일 테넌트 분할을 사용하면 각 데이터베이스가 특정 테넌트 ID 값(또는 고객의 키 값)과 연결되지만 해당 키가 데이터 자체에 존재하지 않아도 됩니다. 각 요청을 적절한 데이터베이스에 라우팅하는 것은 애플리케이션의 책임이고 클라이언트 라이브러리는 이것을 단순하게 해줍니다.

![다중 테넌트 대 단일 테넌트][4]

다른 시나리오에서는 여러 테넌트를 개별 데이터베이스로 격리하지 않고 함께 데이터베이스에 포함합니다. 이 패턴은 일반적인 **다중 테넌트 분할 패턴**이며, 응용 프로그램에서 많은 수의 작은 테넌트를 관리한다는 사실로 구동될 수 있습니다. 다중 테넌트 분할에서 데이터베이스 테이블의 행은 모두 테넌트 ID 또는 키 분할을 식별하는 키 ID를 제공하도록 설계되었습니다. 또한 애플리케이션 계층이 적합한 데이터베이스로 테넌트의 요청을 라우팅하고 탄력적 데이터베이스 클라이언트 라이브러리가 보조할 수 있습니다. 또한 행 수준 보안은 행 각각의 세부 정보에 액세스할 수 있는 필터에 사용될 수 있습니다. 자세한 내용은 [Elastic Database 도구 및 행 수준 보안을 제공하는 다중 테넌트 애플리케이션](sql-database-elastic-tools-multi-tenant-row-level-security.md)을 참조하세요. 데이터베이스 간 데이터 재배포는 다중 테넌트 분할 패턴에 필요할 수 있고, 탄력적 데이터베이스 분할 병합 도구를 통해 쉽게 처리할 수 있습니다. 탄력적 풀을 사용한 SaaS 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>다중 테넌트 데이터베이스에서 단일 테넌트 데이터베이스로 데이터 이동
SaaS 애플리케이션을 만들 때 잠재 고객에게 평가판 소프트웨어를 제공하는 것은 일반적입니다. 이 경우 데이터에 대해 다중 테넌트 데이터베이스를 사용하는 것이 비용 효율적입니다. 그러나 잠재 고객이 고객이 되면 단일 테넌트 데이터베이스가 더 나은 성능을 제공하므로 단일 테넌트 데이터베이스가 더 좋습니다. 고객이 평가판 사용 기간 동안 데이터를 만든 경우 [분할-병합 도구](sql-database-elastic-scale-overview-split-and-merge.md) 를 사용하여 데이터를 다중 테넌트에서 새 단일 테넌트 데이터베이스로 이동합니다.

## <a name="next-steps"></a>다음 단계
클라이언트 라이브러리를 보여 주는 샘플 앱은 [Elastic Database 도구 시작하기](sql-database-elastic-scale-get-started.md)를 참조하세요.

도구를 사용하도록 기존 데이터베이스를 변환하려면 [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)을 참조하세요.

탄력적 풀의 세부 사항을 보려면 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool.md)을 참조하거나 [탄력적 풀](sql-database-elastic-pool-manage-portal.md)을 사용하여 새로운 풀을 만드세요.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png


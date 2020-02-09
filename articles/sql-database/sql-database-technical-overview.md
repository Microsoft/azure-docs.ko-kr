---
title: Azure SQL Database 서비스란?
description: 'SQL Database에 대 한 소개: 클라우드에서 Microsoft RDBMS (관계형 데이터베이스 관리 시스템)의 기술 세부 정보 및 기능을 소개 합니다.'
keywords: sql 소개, sql 소개, sql 데이터베이스란
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 3932c22ff003a343e4c32aee117a7ddea922fbdb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820739"
---
# <a name="what-is-the-azure-sql-database-service"></a>Azure SQL Database 서비스란?

Azure SQL Database는 관리 서비스로 제공 되는 범용 관계형 데이터베이스입니다. 이를 통해 Azure에서 응용 프로그램 및 솔루션에 대 한 고가용성 및 고성능 데이터 저장소 계층을 만들 수 있습니다. 다양 한 최신 클라우드 응용 프로그램에 적합 한 선택이 될 수 있습니다 .이를 통해 graph, JSON, 공간, XML 등의 관계형 데이터와 비관계형 [구조](sql-database-multi-model-features.md)를 모두 처리할 수 있습니다. SQL Database

안정적인 최신 버전의 [Microsoft SQL Server 데이터베이스 엔진](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)을 기반으로 합니다. [고성능 메모리 내 기술과](sql-database-in-memory.md) [지능형 쿼리 처리](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)와 같은 고급 쿼리 처리 기능을 사용할 수 있습니다. 실제로 SQL Server의 최신 기능은 SQL Database으로 먼저 출시 된 다음 SQL Server 됩니다. 수많은 데이터베이스에서 테스트 되는 패치 또는 업그레이드에 대 한 오버 헤드 없이 최신 SQL Server 기능을 얻을 수 있습니다. 

SQL Database를 사용 하면 [Vcore 기반 구매 모델](sql-database-service-tiers-vcore.md) 및 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)이라는 두 가지 구매 모델 내에서 성능을 쉽게 정의 하 고 확장할 수 있습니다. SQL Database는 고가용성, 백업 및 기타 일반적인 유지 관리 작업을 기본 제공 하는 완전히 관리 되는 서비스입니다. Microsoft는 SQL 및 운영 체제 코드의 모든 패치 및 업데이트를 처리 합니다. 기본 인프라를 관리할 필요가 없습니다.

> [!NOTE]
> 관련 용어와 해당 정의는 [SQL Database 용어 용어집](sql-database-glossary-terms.md)을 참조 하세요.

## <a name="deployment-models"></a>배포 모델

Azure SQL Database는 Azure SQL 데이터베이스에 다음과 같은 옵션을 제공합니다.

![배포 옵션 다이어그램](./media/sql-database-technical-overview/deployment-options.png)

- [단일 데이터베이스](sql-database-single-database.md) 는 완전히 관리 되는 격리 된 데이터베이스를 나타냅니다. 안정적인 단일 데이터 원본이 필요한 최신 클라우드 응용 프로그램 및 마이크로 서비스를 사용 하는 경우이 옵션을 사용할 수 있습니다. 단일 데이터베이스는 [Microsoft SQL Server 데이터베이스 엔진](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)의 포함 된 [데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) 와 비슷합니다.
- [관리](sql-database-managed-instance.md) 되는 인스턴스는 [Microsoft SQL Server 데이터베이스 엔진](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)의 완전히 관리 되는 인스턴스입니다. 함께 사용할 수 있는 데이터베이스 집합이 포함 되어 있습니다. 온-프레미스 SQL Server 데이터베이스를 Azure 클라우드로 쉽게 마이그레이션할 수 있도록이 옵션을 사용 하 고 데이터베이스 엔진에서 제공 하 SQL Server 데이터베이스 기능을 사용 해야 하는 응용 프로그램에 대해이 옵션을 사용 합니다.
- [탄력적 풀](sql-database-elastic-pool.md) 은 CPU, 메모리 등의 공유 리소스 집합을 포함 하는 [단일 데이터베이스](sql-database-single-database.md) 의 컬렉션입니다. 단일 데이터베이스를 탄력적 풀로 이동하거나 탄력적 풀에서 제거할 수 있습니다.

> [!IMPORTANT]
> SQL Database와 SQL Server의 기능 차이점을 이해 하 고 다양 한 Azure SQL Database 배포 옵션 간의 차이점을 이해 하려면 [SQL Database 기능](sql-database-features.md)을 참조 하세요.

SQL Database는 여러 리소스 종류, 서비스 계층 및 계산 크기를 사용 하 여 예측 가능한 성능을 제공 합니다. 가동 중지 시간 없이 기본 제공 되는 지능형 최적화, 글로벌 확장성과 가용성 및 고급 보안 옵션을 사용 하 여 동적 확장성을 제공 합니다. 이러한 기능을 사용 하면 가상 머신과 인프라를 관리 하는 대신 빠른 앱 개발에 집중 하 고 출시 시간을 단축할 수 있습니다. SQL Database 서비스는 현재 전 세계 38 데이터 센터에 있으므로 가까운 데이터 센터에서 데이터베이스를 실행할 수 있습니다.

## <a name="scalable-performance-and-pools"></a>확장 가능한 성능 및 풀

할당 된 리소스의 양을 정의할 수 있습니다. 
- 단일 데이터베이스를 사용 하는 경우 각 데이터베이스는 다른 데이터베이스와 격리 되며 이식 가능 합니다. 각에는 계산, 메모리 및 저장소 리소스의 보장 된 크기가 있습니다. 데이터베이스에 할당 된 리소스 양은 해당 데이터베이스 전용 이며 Azure의 다른 데이터베이스와 공유 되지 않습니다. [단일 데이터베이스 리소스](sql-database-single-database-scale.md) 를 동적으로 확장 및 축소할 수 있습니다. 단일 데이터베이스 옵션은 요구 사항에 따라 다양 한 계산, 메모리 및 저장소 리소스를 제공 합니다. 예를 들어 1 개에서 80 vCores 또는 32 GB ~ 4tb를 얻을 수 있습니다. 단일 데이터베이스에 대 한 하이퍼 [크기 조정 서비스 계층](sql-database-service-tier-hyperscale.md) 을 사용 하면 빠른 백업 및 복원 기능을 통해 100 TB까지 확장할 수 있습니다.
- 탄력적 풀을 사용 하면 풀에 있는 모든 데이터베이스에서 공유 하는 리소스를 할당할 수 있습니다. 새 데이터베이스를 만들거나 기존 단일 데이터베이스를 리소스 풀로 이동 하 여 리소스 사용을 최대화 하 고 비용을 절감할 수 있습니다. 또한이 옵션을 사용 하면 [탄력적 풀 리소스](sql-database-elastic-pool-scale.md) 를 동적으로 확장 및 축소할 수 있습니다.
- 관리되는 인스턴스를 사용할 경우 각각의 관리되는 인스턴스가 다른 인스턴스에서 격리되고 리소스가 보장됩니다. 관리 되는 인스턴스 내에서 인스턴스 데이터베이스는 리소스 집합을 공유 합니다. [관리 되는 인스턴스 리소스](sql-database-managed-instance-resource-limits.md) 를 동적으로 확장 및 축소할 수 있습니다.

범용 서비스 계층에서 매월 저렴 한 비용으로 소규모의 단일 데이터베이스에 첫 번째 앱을 빌드할 수 있습니다. 그런 다음 언제 든 지 비즈니스에 중요 한 서비스 계층으로 서비스 계층을 수동으로 또는 프로그래밍 방식으로 변경 하 여 솔루션의 요구 사항을 충족할 수 있습니다. 앱이나 고객에게 가동 중지 시간 없이 성능을 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 때 필요한 리소스에 대해서만 비용을 지불 합니다.

*동적 확장성* 은 *자동 크기*조정과 다릅니다. 자동 크기 조정은 서비스가 조건에 따라 자동으로 크기를 조정하는 경우인 반면 동적 확장성은 가동 중지 시간 없이 수동 크기 조정을 허용합니다. 단일 데이터베이스 옵션은 수동 동적 확장성을 지원 하지만 자동 크기 조정 기능은 지원 하지 않습니다. 더 많은 자동 환경을 위해 데이터베이스에서 개별 데이터베이스 요구 사항에 따라 풀의 리소스를 공유할 수 있도록 하는 탄력적 풀을 사용 하는 것이 좋습니다. 또 다른 옵션은 단일 데이터베이스의 확장성을 자동화 하는 데 도움이 되는 스크립트를 사용 하는 것입니다. 예제는 [PowerShell을 사용하여 단일 데이터베이스 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)을 참조하세요.

### <a name="purchasing-models"></a>구매 모델

SQL Database는 다음과 같은 구매 모델을 제공 합니다.
- [Vcore 기반 구매 모델](sql-database-service-tiers-vcore.md) 을 사용 하면 vcore 수, 메모리 양 및 저장소의 양과 속도를 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다. Azure 하이브리드 혜택에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 "질문과 대답" 섹션을 참조 하세요.
- [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 은 3 개의 서비스 계층에서 계산, 메모리 및 i/o 리소스를 blend로 제공 하 여 사용량이 많은 데이터베이스 워크 로드를 지원 합니다. 각 계층 내의 컴퓨팅 크기는 이러한 리소스의 다양한 조합을 제공하여 추가 스토리지 리소스를 추가할 수 있습니다.
- 서버를 사용 하지 않는 [모델](sql-database-serverless.md) 은 작업 수요를 기준으로 계산을 자동으로 확장 하 고, 초당 사용 된 계산 양에 대 한 요금을 청구 합니다. 서버를 사용 하지 않는 계산 계층은 저장소 요금이 청구 될 때 비활성 기간 동안 데이터베이스를 자동으로 일시 중지 하 고 작업이 반환 될 때 자동으로 데이터베이스를 다시 시작 합니다.

### <a name="service-tiers"></a>서비스 계층

Azure SQL Database는 다음과 같은 다양 한 유형의 응용 프로그램을 위해 설계 된 세 가지 서비스 계층을 제공 합니다.
- 일반적인 작업을 위해 설계 된 범용 [/표준](sql-database-service-tier-general-purpose.md) 서비스 계층입니다. 예산 중심의 분산 된 계산 및 저장소 옵션을 제공 합니다.
- 트랜잭션 전송률이 높고 대기 시간이 짧은 i/o를 사용 하는 OLTP 응용 프로그램용으로 설계 된 [중요 비즈니스용/프리미엄](sql-database-service-tier-business-critical.md) 서비스 계층입니다. 여러 개의 격리 된 복제본을 사용 하 여 오류에 대 한 가장 높은 복원 력을 제공 합니다.
- 매우 큰 OLTP 데이터베이스용으로 설계 된 [대규모로 확장 된 서비스 계층](sql-database-service-tier-hyperscale.md) 및 저장소의 크기를 자동으로 조정 하 고 계산 유동적으로를 확장 하는 기능입니다. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>리소스 사용률 극대화를 위한 탄력적 풀

특히 사용 패턴이 비교적 예측 가능한 경우 많은 비즈니스 및 애플리케이션에서 단일 데이터베이스를 만들고 필요에 따라 충분히 성능을 확장하거나 축소할 수 있습니다. 예측할 수 없는 사용 패턴을 통해 비용 및 비즈니스 모델을 관리 하기 어려울 수 있습니다. [탄력적 풀](sql-database-elastic-pool.md)은 이 문제를 해결하도록 설계되었습니다. 개별 데이터베이스 대신 풀에 성능 리소스를 할당 합니다. 단일 데이터베이스 성능이 아닌 풀의 집합적 성능 리소스에 대해 비용을 지불 합니다.

   ![기본, 표준 및 프리미엄 버전에서 탄력적 풀을 보여 주는 그래픽](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

탄력적 풀을 사용하면 리소스에 대한 요구가 변동함에 따라 데이터베이스 성능을 높이거나 낮추는 데 집중할 필요가 없습니다. 풀링된 데이터베이스는 필요에 따라 탄력적 풀의 성능 리소스를 사용합니다. 풀링된 데이터베이스는 풀의 한도를 사용하지만 초과하지 않으므로 개별 데이터베이스 사용량을 예측할 수 없는 경우에도 비용을 계속 예측할 수 있습니다.

[풀에 데이터베이스를 추가 및 제거 하 고](sql-database-elastic-pool-manage-portal.md), 소수의 데이터베이스에서 수천 개의 응용 프로그램을 사용자가 제어 하는 예산에 맞게 확장할 수 있습니다. 풀의 데이터베이스에서 사용할 수 있는 최소 및 최대 리소스를 제어 하 여 풀의 어떤 데이터베이스도 풀 리소스를 모두 사용 하지 않도록 하 고 풀링된 모든 데이터베이스에 최소한의 리소스를 보장할 수 있습니다. 탄력적 풀을 사용 하는 SaaS (software as a service) 응용 프로그램의 디자인 패턴에 대 한 자세한 내용은 [SQL Database를 사용 하 여 다중 테넌트 saas 응용 프로그램에 대 한 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조 하세요.

스크립트는 탄력적 풀 모니터링 및 크기 조정에 도움을 줄 수 있습니다. 예제를 보려면 [PowerShell을 사용 하 여 Azure SQL Database에서 SQL 탄력적 풀 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-pool-powershell.md)을 참조 하세요.

> [!IMPORTANT]
> 관리 되는 인스턴스는 탄력적 풀을 지원 하지 않습니다. 대신, 관리되는 인스턴스는 관리되는 인스턴스 리소스를 공유하는 인스턴스 데이터베이스 컬렉션입니다.

### <a name="blend-single-databases-with-pooled-databases"></a>단일 데이터베이스와 풀링된 데이터베이스의 혼합

단일 데이터베이스를 탄력적 풀과 혼합 하 고, 상황에 맞게 단일 데이터베이스 및 탄력적 풀의 서비스 계층을 변경할 수 있습니다. 또한 다른 Azure 서비스를 SQL Database와 함께 사용 하 여 고유한 앱 설계 요구를 충족 시키고, 비용 및 리소스 효율성을 높이고, 새로운 비즈니스 기회를 얻을 수 있습니다.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>광범위한 모니터링 및 경고 기능

Azure SQL Database는 작업 특성에 대 한 심층적인 통찰력을 얻는 데 도움이 되는 고급 모니터링 및 문제 해결 기능을 제공 합니다. 이러한 기능 및 도구는 다음과 같습니다.
 - 최신 버전의 SQL Server 데이터베이스 엔진에서 제공 하는 기본 제공 모니터링 기능. 실시간 성능 정보를 찾을 수 있습니다. 
 - Azure에서 제공 하는 PaaS 모니터링 기능을 통해 많은 수의 데이터베이스 인스턴스를 모니터링 하 고 문제를 해결할 수 있습니다.

기본적으로 제공 되는 SQL Server 모니터링 기능인 [쿼리 저장소](sql-database-operate-query-store.md)는 쿼리 성능을 실시간으로 기록 하 고 잠재적인 성능 문제와 상위 리소스 소비자를 식별할 수 있도록 합니다. 자동 조정 및 권장 사항은 재발 된 성능 및 누락 되거나 중복 된 인덱스를 사용 하 여 쿼리에 대 한 조언을 제공 합니다. SQL Database 자동 조정 기능을 사용 하면 문제를 해결할 수 있는 스크립트를 수동으로 적용 하거나 수정 SQL Database 적용할 수 있습니다. 또한 수정이 몇 가지 이점을 제공 하는지 테스트 하 고 확인 하 고 결과에 따라 변경 내용을 유지 하거나 되돌릴 수 SQL Database. 쿼리 저장소 및 자동 조정 기능 외에도 표준 [dmv 및 XEvent](sql-database-monitoring-with-dmvs.md) 를 사용 하 여 워크 로드 성능을 모니터링할 수 있습니다.

Azure는 성능 등급과 결합 된 [기본 제공 성능 모니터링](sql-database-performance.md) 및 [경고](sql-database-insights-alerts-portal.md) 도구를 제공 하 여 수천 개의 데이터베이스 상태를 모니터링할 수 있도록 합니다. 이러한 도구를 사용 하면 현재 또는 예상 되는 성능 요구 사항에 따라 확장 또는 축소의 영향을 신속 하 게 평가할 수 있습니다. 또한 SQL Database는 쉬운 모니터링을 위해 [메트릭 및 진단 로그를 내보낼](sql-database-metrics-diag-logging.md) 수 있습니다. 리소스 사용량, 작업자와 세션 및 연결을 이러한 Azure 리소스 중 하나에 저장하도록 SQL Database를 구성할 수 있습니다.

- **Azure Storage**: 저렴한 가격으로 방대한 양의 원격 분석을 보관할 수 있습니다.
- **Azure Event Hubs**: 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 SQL Database 원격 분석을 통합 하는 데 사용 됩니다.
- **Azure Monitor 로그**: 보고, 경고 및 완화 기능을 사용 하는 기본 제공 모니터링 솔루션의 경우

![Azure 모니터링 아키텍처 다이어그램](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>가용성 기능

기존 SQL Server 환경에서는 일반적으로 두 대 이상의 컴퓨터가 로컬로 설정 되어 있습니다. 이러한 컴퓨터에는 단일 컴퓨터 또는 구성 요소의 오류 로부터 보호 하기 위해 정확 하 고 동기적으로 유지 관리 되는 데이터 복사본이 있습니다. 이 환경은 고가용성을 제공 하지만 데이터 센터를 제거 하는 자연 재해 로부터 보호 하지 않습니다.

재해 복구는 다른 컴퓨터 또는 컴퓨터 집합을 사용 하 여 데이터 복사본을 멀리 사용할 수 있는 지리적으로 재해를 지역화 한다고 가정 합니다. SQL Server에서 비동기 모드로 실행 되는 Always On 가용성 그룹을 사용 하 여이 기능을 얻을 수 있습니다. 사용자는 종종 트랜잭션을 커밋하기 전에 복제가 발생할 때까지 기다리지 않으려고 하므로 계획 되지 않은 장애 조치 (failover)를 수행 하면 데이터가 손실 될 가능성이 있습니다.

프리미엄 및 중요 비즈니스용 서비스 계층의 데이터베이스는 이미 가용성 그룹의 동기화와 [매우 유사한 작업을 수행](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 합니다. 하위 서비스 계층의 데이터베이스는 [다른 메커니즘](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)을 사용 하 여 저장소를 통해 중복성을 제공 합니다. 기본 제공 논리는 단일 컴퓨터 오류 로부터 보호 하는 데 도움이 됩니다. 활성 지역 복제 기능을 사용 하면 전체 지역이 제거 되는 재해 로부터 보호 하는 기능을 사용할 수 있습니다.

Azure 가용성 영역는 단일 지역 내에서 단일 데이터 센터 빌드 중단 으로부터 보호 하려고 합니다. 빌드에 대 한 전원 또는 네트워크의 손실을 방지 하는 데 도움이 됩니다. SQL Database 다양 한 가용성 영역 (빌딩, 효율적)에 다양 한 복제본을 저장 합니다.

실제로 Microsoft에서 관리 하는 데이터 센터의 글로벌 네트워크에서 제공 하는 Azure의 [SLA (](https://azure.microsoft.com/support/legal/sla/) 서비스 수준 계약)는 앱이 24/7을 계속 실행 하는 데 도움이 됩니다. Azure 플랫폼은 모든 데이터베이스를 완벽 하 게 관리 하 고 데이터 손실 및 높은 비율의 데이터 가용성을 보장 하지 않습니다. Azure는 패치, 백업, 복제, 오류 감지, 기본 하드웨어, 소프트웨어 또는 네트워크 오류, 배포 버그 픽스, 장애 조치(failover), 데이터베이스 업그레이드 및 기타 유지 관리 작업을 자동으로 처리합니다. 표준 가용성은 컴퓨팅 계층과 스토리지 계층을 분리하여 달성합니다. 프리미엄 가용성은 성능 향상을 위해 단일 노드에 계산 및 저장소를 통합 한 다음 Always On 가용성 그룹과 유사한 기술을 구현 하 여 수행 됩니다. Azure SQL Database의 고가용성 기능에 대한 자세한 내용은 [SQL Database 가용성](sql-database-high-availability.md)을 참조하세요. 

또한 SQL Database는 기본 제공 [비즈니스 연속성 및 글로벌 확장성](sql-database-business-continuity.md) 기능을 제공 합니다. 내용은 다음과 같습니다.

- [자동 백업](sql-database-automated-backups.md):

  SQL Database는 SQL 데이터베이스의 전체, 차등 및 트랜잭션 로그 백업을 자동으로 수행 하 여 특정 시점으로 복원할 수 있도록 합니다. 단일 데이터베이스 및 풀링된 데이터베이스의 경우 장기 백업 보존을 위해 Azure Storage에 전체 데이터베이스 백업을 저장 하도록 SQL Database를 구성할 수 있습니다. 관리되는 인스턴스의 경우 장기 백업 보존을 위해 복사 전용 백업도 수행할 수 있습니다.

- 지정 [시간 복원](sql-database-recovery-using-backups.md):

  모든 SQL Database 배포 옵션은 모든 SQL 데이터베이스에 대해 자동 백업 보존 기간 내의 특정 시점으로의 복구를 지원 합니다.
- [활성 지역 복제](sql-database-active-geo-replication.md):

  단일 데이터베이스 및 풀링된 데이터베이스 옵션을 사용 하 여 동일 하거나 전 세계적으로 분산 된 Azure 데이터 센터에서 최대 4 개의 읽기 가능한 보조 데이터베이스를 구성할 수 있습니다. 예를 들어 대용량의 동시 읽기 전용 트랜잭션이 있는 카탈로그 데이터베이스를 사용 하는 SaaS 응용 프로그램이 있는 경우 활성 지역 복제를 사용 하 여 글로벌 읽기 크기 조정을 사용 하도록 설정 합니다. 이렇게 하면 읽기 작업으로 인해 주 복제본에서 병목 현상이 제거 됩니다. 관리되는 인스턴스의 경우 자동 장애 조치(failover) 그룹을 사용합니다.
- [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md):

  모든 SQL Database 배포 옵션을 사용 하면 장애 조치 (failover) 그룹을 사용 하 여 글로벌 규모로 고가용성 및 부하 분산을 사용 하도록 설정할 수 있습니다. 여기에는 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스의 투명 한 지역 복제 및 장애 조치 (failover)가 포함 됩니다. 장애 조치 (Failover) 그룹을 사용 하면 관리 오버 헤드를 최소화 하면서 전역으로 분산 된 SaaS 응용 프로그램을 만들 수 이렇게 하면 모든 복잡 한 모니터링, 라우팅 및 장애 조치 (failover) 오케스트레이션이 SQL Database으로 유지 됩니다.
- [영역 중복 데이터베이스](sql-database-high-availability.md):

  SQL Database를 사용하면 여러 가용성 영역에서 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀을 프로비전할 수 있습니다. 이러한 데이터베이스 및 탄력적 풀에는 고가용성을 위한 여러 중복 복제본이 있기 때문에 이러한 복제본을 여러 가용성 영역에 배치 하면 복원 력이 높아집니다. 여기에는 데이터 손실 없이 데이터 센터 규모 오류 로부터 자동으로 복구 하는 기능이 포함 됩니다.

## <a name="built-in-intelligence"></a>기본 제공 인텔리전스

SQL Database를 사용 하면 데이터베이스를 실행 하 고 관리 하는 비용을 대폭 줄이고 응용 프로그램의 성능과 보안을 극대화 하는 데 도움이 되는 기본 제공 인텔리전스를 사용할 수 있습니다. 수많은 고객 워크 로드 SQL Database를 클록에 따라 실행 하는 경우에는 많은 양의 원격 분석 데이터를 수집 하 고 처리 하는 동시에 고객 개인 정보를 완벽 하 게 준수 해야 합니다. 다양 한 알고리즘은 서비스가 응용 프로그램을 학습 하 고 적용할 수 있도록 원격 분석 데이터를 지속적으로 평가 합니다.

### <a name="automatic-performance-monitoring-and-tuning"></a>자동 성능 모니터링 및 튜닝

SQL Database는 모니터링해야 하는 쿼리에 대한 자세한 정보를 제공합니다. SQL Database는 데이터베이스 패턴을 학습 하 고 데이터베이스 스키마를 작업에 맞게 조정할 수 있도록 합니다. SQL Database는 튜닝 작업을 검토하고 적용할 수 있는 [성능 튜닝 권장 사항](sql-database-advisor.md)을 제공합니다.

그러나 데이터베이스를 지속적으로 모니터링 하는 작업은 특히 많은 데이터베이스를 처리할 때 어렵고 지루한 작업입니다. [Intelligent Insights](sql-database-intelligent-insights.md) 는 대규모로 SQL Database 성능을 자동으로 모니터링 하 여이 작업을 수행 합니다. 성능 저하 문제를 알려 주며, 각 문제의 근본 원인을 파악 하 고, 가능한 경우 성능 개선 권장 사항을 제공 합니다.

SQL Database 및 Azure에서 제공 하는 모든 사용 가능한 도구 및 보고서를 사용 하 여 엄청난 수의 데이터베이스를 효율적으로 관리할 수 없습니다. 수동으로 데이터베이스를 모니터링 하 고 튜닝 하는 대신 [자동 튜닝](sql-database-automatic-tuning.md)을 사용 하 여 SQL Database에 대 한 일부 모니터링 및 튜닝 작업을 위임 하는 것을 고려할 수 있습니다. SQL Database은 자동으로 권장 사항을 적용 하 고 각 튜닝 작업을 확인 하 여 성능을 향상 시킬 수 있도록 합니다. 이러한 방식으로 SQL Database는 제어 되 고 안전한 방식으로 워크 로드에 자동으로 적응 합니다. 자동 조정은 모든 튜닝 작업 전후에 데이터베이스 성능이 신중 하 게 모니터링 되 고 비교 됨을 의미 합니다. 성능이 개선 되지 않는 경우 튜닝 작업은 되돌려집니다.

SQL Database를 기반으로 [SaaS 다중 테넌트 앱](sql-database-design-patterns-multi-tenancy-saas-applications.md) 을 실행 하는 대부분의 파트너는 응용 프로그램이 항상 안정적이 고 예측 가능한 성능을 갖도록 하기 위해 자동 성능 조정에 의존 합니다. 이 기능은 갑자기 성능 인시던트가 발생하는 위험을 크게 줄여 줍니다. 또한 고객 기반의 일부는 SQL Server을 사용 하기 때문에 SQL Database에서 제공 하는 것과 동일한 인덱싱 권장 사항을 사용 하 여 SQL Server 고객에 게 도움을 줍니다.

[SQL Database에서 사용할 수](sql-database-automatic-tuning.md)있는 두 가지 자동 조정 측면은 다음과 같습니다.

- **자동 인덱스 관리**: 데이터베이스에 추가되어야 하는 인덱스 및 제거되어야 하는 인덱스를 식별합니다.
- **자동 계획 수정**: 문제가 있는 계획을 식별 하 고 SQL 계획 성능 문제를 해결 합니다.

### <a name="adaptive-query-processing"></a>적응 쿼리 처리

다중 문 테이블 반환 함수, 일괄 처리 모드 메모리 부여 피드백 및 일괄 처리 모드 적응 조인에 대 한 인터리브 실행을 포함 하 여 [적응 쿼리 처리](/sql/relational-databases/performance/intelligent-query-processing)를 사용할 수 있습니다. 이러한 적응 쿼리 처리 기능은 각각 유사한 "알아보기 및 적용" 방법을 적용하고 지금까지 다루기 힘든 쿼리 최적화 문제와 관련된 성능 문제를 추가로 해결할 수 있도록 합니다.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

SQL Database는 다양한 [기본 제공 보안 및 규정 준수 기능](sql-database-security-overview.md)을 제공하여 애플리케이션이 다양한 보안 및 규정 준수 요구 사항을 충족할 수 있도록 합니다.

> [!IMPORTANT]
> Microsoft는 다양 한 규정 준수 표준에 대해 Azure SQL Database (모든 배포 옵션)을 인증 했습니다. 자세한 내용은 SQL Database 준수 인증의 최신 목록을 찾을 수 있는 [Microsoft Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)를 참조 하세요.

### <a name="advance-threat-protection"></a>고급 위협 방지

고급 데이터 보안은 고급 SQL 보안 기능의 통합 패키지입니다. 여기에는 중요 한 데이터를 검색 및 분류 하 고, 데이터베이스 취약성을 관리 하 고, 데이터베이스에 위협을 나타낼 수 있는 비정상적인 활동을 검색 하는 기능이 포함 됩니다. 이 패키지를 통해 이러한 기능을 한 곳에서 사용하도록 설정하고 관리할 수 있습니다.

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md):

  이 기능은 데이터베이스의 중요 한 데이터를 검색, 분류, 레이블 지정 및 보호 하기 위해 Azure SQL Database에 기본 제공 되는 기능을 제공 합니다. 데이터베이스 분류 상태에 대 한 가시성을 제공 하 고 데이터베이스 내의 중요 한 데이터에 대 한 액세스와 해당 테두리를 벗어난 액세스를 추적 합니다.
- [취약성 평가](sql-vulnerability-assessment.md):

  이 서비스는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정하는 데 도움이 됩니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [위협 탐지](sql-database-threat-detection.md):

  이 기능은 비정상 이며 잠재적으로 유해한 작업을 통해 데이터베이스에 액세스 하거나이를 악용할 수 있음을 나타내는 비정상 작업을 검색 합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. 위협 검색 경고는 의심 스러운 활동의 세부 정보를 제공 하 고 위협을 조사 하 고 완화 하는 방법에 대 한 권장 조치를 제공 합니다.

### <a name="auditing-for-compliance-and-security"></a>규정 준수 및 보안에 대한 감사

[감사](sql-database-auditing.md)는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 씁니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

### <a name="data-encryption"></a>데이터 암호화.

SQL Database는 암호화를 제공 하 여 데이터를 보호 합니다. 동작 중인 데이터의 경우 [전송 계층 보안](https://support.microsoft.com/kb/3135244)을 사용 합니다. 미사용 데이터의 경우 [투명 한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 사용 합니다. 사용 중인 데이터의 경우 [상시 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)를 사용 합니다.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 Multi-Factor Authentication

SQL Database를 사용하면 [Azure Active Directory 통합](sql-database-aad-authentication.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 단일 로그인 프로세스를 지 원하는 동시에 데이터 및 응용 프로그램 보안을 강화 하는 [multi-factor authentication](sql-database-ssms-mfa-authentication.md) 을 지원 합니다.

## <a name="easy-to-use-tools"></a>사용하기 쉬운 도구

SQL Database로 애플리케이션을 빌드하고 관리하는 작업의 편의성과 생산성을 높이세요. SQL Database를 사용하면 멋진 앱을 만드는 데만 집중할 수 있습니다. 이미 보유 하 고 있는 도구와 기술을 사용 하 여 SQL Database에서 관리 하 고 개발할 수 있습니다.

- [Azure Portal](https://portal.azure.com/):

  모든 Azure 서비스를 관리 하기 위한 웹 기반 응용 프로그램입니다.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  SQL Server에서 SQL Database까지 모든 SQL 인프라를 관리 하기 위한 다운로드 가능한 무료 클라이언트 응용 프로그램입니다.
- [Visual Studio에서 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  SQL Server 관계형 데이터베이스, SQL 데이터베이스, Integration Services 패키지, Analysis Services 데이터 모델 및 Reporting Services 보고서를 개발 하기 위한 다운로드 가능한 무료 클라이언트 응용 프로그램입니다.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Windows, macOS 및 Linux 용 다운로드 가능한 무료 오픈 소스 코드 편집기입니다. Microsoft SQL Server, Azure SQL Database 및 Azure SQL Data Warehouse 쿼리를 위한 [mssql 확장](https://aka.ms/mssql-marketplace) 을 비롯 한 확장을 지원 합니다.

SQL Database은 macOS, Linux 및 Windows에서 Python, Java, node.js, PHP, Ruby 및 .NET으로 응용 프로그램 빌드를 지원 합니다. SQL Database는 동일한 [연결 라이브러리](sql-database-libraries.md)를 SQL Server로 지원합니다.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database 질문과 대답

### <a name="what-is-the-current-version-of-sql-database"></a>SQL Database의 최신 버전은 무엇인가요?

SQL Database의 현재 버전은 V12입니다. 버전 V11은 만료되었습니다.

### <a name="can-i-control-when-patching-downtime-occurs"></a>가동 중지 시간이 발생 하는 시기를 제어할 수 있나요?

아니요. 일반적으로 패치 적용에 따른 영향은 앱에서 [다시 시도 논리를 사용](sql-database-develop-overview.md#resiliency)하면 두드러지지 않습니다. 자세한 내용은 [Azure SQL Database에서 Azure 유지 관리 이벤트 계획](sql-database-planned-maintenance.md)을 참조 하세요.

### <a name="azure-hybrid-benefit-questions"></a>Azure 하이브리드 혜택 질문

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택에 이중 사용 권한이 있나요?

마이그레이션이 원활하게 실행되도록 하려면 180일간의 이중 사용 권한 라이선스가 있어야 합니다. 180 일 기간 후에는 SQL Database 클라우드의 SQL Server 라이선스를 사용할 수 있습니다. 온-프레미스와 클라우드에는 더 이상 이중 사용 권한이 없습니다.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server에 대한 Azure 하이브리드 혜택은 라이선스 이동과 어떻게 다른가요?

소프트웨어 보증을 통해 고객에 게 SQL Server에 대 한 라이선스 이동성 혜택을 제공 합니다. 이렇게 하면 파트너의 공유 서버에 대 한 라이선스를 재할당할 수 있습니다. Azure IaaS 및 AWS EC2에서이 혜택을 사용할 수 있습니다.

SQL Server에 대한 Azure 하이브리드 혜택은 두 가지 주요 영역에서 라이선스 이동과 다릅니다.

- 높은 수준으로 가상화된 워크로드를 Azure로 이동할 수 있는 경제적 이점을 제공합니다. SQL Server Enterprise Edition 고객은 매우 가상화 된 응용 프로그램에 대해 온-프레미스에서 소유 하는 모든 코어에 대해 범용 SKU의 Azure에서 4 개의 코어를 가져올 수 있습니다. 라이선스 이동성은 가상화 된 워크 로드를 클라우드로 이동 하는 데 특별 한 비용 혜택을 허용 하지 않습니다.
- SQL Server 온-프레미스와 호환 되는 Azure (SQL Database 관리 되는 인스턴스)의 PaaS 대상을 제공 합니다.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택의 특정 권한은 무엇인가요?

SQL Database 고객에 게는 SQL Server에 대 한 Azure 하이브리드 혜택와 관련 된 다음 권한이 있습니다.

|라이선스 공간|SQL Server Azure 하이브리드 혜택은 무엇 인가요?|
|---|---|
|SA가 있는 SQL Server Enterprise Edition 핵심 고객|<li>범용 또는 중요 비즈니스용 SKU에 대 한 기본 요금을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 4개 코어</li><br><li>온-프레미스의 1개 코어 = 중요 비즈니스 SKU의 1개 코어</li>|
|SA가 있는 SQL Server Standard Edition 핵심 고객|<li>범용 SKU에 대해서만 기본 요금을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 1개 코어</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server 엔지니어링 팀에 문의

- [DBA 스택 교환](https://dba.stackexchange.com/questions/tagged/sql-server): 데이터베이스 관리 질문을 요청 합니다.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): 개발 질문을 합니다.
- [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): 기술에 대해 문의 하세요.
- [사용자 의견](https://aka.ms/sqlfeedback): 버그 및 요청 기능을 보고 합니다.
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server에 대해 논의 합니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스 및 탄력적 풀에 대 한 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/) 를 참조 하세요.
- 시작 하려면 다음 퀵 스타트를 참조 하세요.

  - [Azure Portal에서 SQL Database 만들기](sql-database-single-database-get-started.md)  
  - [Azure CLI를 사용하여 SQL Database 만들기](sql-database-get-started-cli.md)
  - [PowerShell을 사용하여 SQL Database 만들기](sql-database-get-started-powershell.md)

- 일련의 Azure CLI 및 PowerShell 샘플은 다음을 참조하세요.
  - [SQL Database에 대한 Azure CLI 샘플](sql-database-cli-samples.md)
  - [SQL Database에 대한 Azure PowerShell 샘플](sql-database-powershell-samples.md)

- 발표 되는 새로운 기능에 대 한 자세한 내용은 [SQL Database Azure 로드맵](https://azure.microsoft.com/roadmap/?category=databases)을 참조 하세요.
- SQL Database 뉴스 및 기능에 대 한 SQL Server 제품 팀 구성원 블로그 [Azure SQL Database 블로그](https://azure.microsoft.com/blog/topics/database)를 참조 하세요.


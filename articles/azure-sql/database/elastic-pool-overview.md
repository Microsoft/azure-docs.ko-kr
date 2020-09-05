---
title: 탄력적 풀을 사용 하 여 여러 데이터베이스 관리
description: 탄력적 풀을 사용 하 여 Azure SQL Database에서 여러 데이터베이스를 관리 하 고 크기를 조정 합니다. 필요한 경우 배포할 수는 리소스에 대한 단일 가격
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 07/28/2020
ms.openlocfilehash: c36a8e6f2e104d91bd7738849918c46802cd0dca
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225940"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>탄력적 풀은 Azure SQL Database에서 여러 데이터베이스를 관리 하 고 크기를 조정 하는 데 도움이 됩니다.
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database 탄력적 풀은 다양 하 고 예측할 수 없는 사용 요구가 있는 여러 데이터베이스를 관리 하 고 크기를 조정 하는 간단 하 고 비용 효율적인 솔루션입니다. 탄력적 풀의 데이터베이스는 단일 서버에 있으며 설정 된 가격으로 설정 된 수의 리소스를 공유 합니다. Azure SQL Database의 탄력적 풀을 사용하면 SaaS 개발자가 각각의 데이터베이스에 성능 탄력성을 제공하는 동시에 규정된 예산 내의 데이터베이스 그룹에 가격 성능을 최적화할 수 있습니다.

## <a name="what-are-sql-elastic-pools"></a>SQL 탄력적 풀이란?

SaaS 개발자는 여러 데이터베이스로 구성된 대규모 데이터 계층에 애플리케이션을 작성합니다. 일반적인 애플리케이션 패턴은 각 고객에 대해 단일 데이터베이스를 프로비전합니다. 그러나 다른 고객에 게는 다양 하 고 예측할 수 없는 사용 패턴이 있는 경우가 많으며 각 개별 데이터베이스 사용자의 리소스 요구 사항을 예측 하기 어렵습니다. 일반적으로 두 가지 옵션이 있습니다.

- 최대 사용량에 따른 리소스 오버프로비저닝과 과다 지불 또는
- 최대 사용 기간 동안 성능 및 고객 만족은 저하되지만 비용을 절감하는 언더프로비저닝

탄력적 풀은 데이터베이스가 필요한 성능 리소스를 필요할 때 얻도록 하여 이 문제를 해결합니다. 예측 가능한 예산 내에서 간단한 리소스 할당 메커니즘을 제공합니다. 탄력적 풀을 사용한 SaaS 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.
>
> [!IMPORTANT]
> 탄력적 풀에 대한 데이터베이스별 요금은 없습니다. 사용량에 관계없이 또는 풀이 한 시간 미만 동안 활성 상태였는지 여부에 관계없이 풀이 최고 eDTU 또는 vCore 수에 있었던 시간에 대해 시간당 요금이 청구됩니다.

탄력적 풀을 사용하면 개발자가 예측할 수 없는 개별 데이터베이스의 사용 기간을 수용하기 위해 여러 데이터베이스에서 공유되는 풀에 대한 리소스를 구매할 수 있습니다. [DTU 기반 구매 모델](service-tiers-dtu.md) 또는 [vCore 기반 구매 모델](service-tiers-vcore.md)에 따라 풀에 대한 리소스를 구성할 수 있습니다. 풀에 대한 리소스 요구 사항은 해당 데이터베이스의 집계 사용률에 의해 결정됩니다. 풀에 사용 가능한 리소스 양은 개발자 예산에 의해 제어됩니다. 개발자는 단순히 풀에 데이터베이스를 추가 하 고, 필요에 따라 데이터베이스에 대 한 최소 및 최대 리소스 (선택한 높아지면 모델에 따라 최소 및 최대 Dtu 또는 최소 또는 최대 vCores)를 설정 하 고, 해당 예산에 따라 풀의 리소스를 설정 합니다. 개발자는 풀을 사용하여 처음 시작부터 계속 성장하는 성숙한 비즈니스까지 해당 서비스를 원활하게 증가시킬 수 있습니다.

풀 내에서 개별 데이터베이스는 집합 매개 변수 내에서 자동 크기 조정에 유연성이 제공됩니다. 부하가 높은 상태에서 데이터베이스는 더 많은 리소스를 사용하여 수요를 충족할 수 있습니다. 낮은 부하량에서 데이터베이스는 적게 사용하고 부하가 없는 데이터베이스는 리소스를 사용하지 않습니다. 단일 데이터베이스가 아닌 전체 풀에 대한 리소스 프로비저닝은 관리 작업을 간소화합니다. 또한 풀에 대한 예측 가능한 예산이 있습니다. 최소 가동 중지 시간으로 추가 리소스를 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 리소스가 더 이상 필요하지 않은 경우에는 언제든지 기존 풀에서 제거할 수 있습니다. 그리고 풀에서 데이터베이스를 추가 하거나 제거할 수 있습니다. 데이터베이스가 예측 가능한 방식으로 리소스를 사용하는 경우 리소스를 이동합니다.

> [!NOTE]
> 데이터베이스를 탄력적 풀 안팎으로 이동하는 경우, 데이터베이스 연결이 끊길 때 작업이 종료되는 잠깐의 시간(초 단위)을 제외하고 가동 중지 시간이 발생하지 않습니다.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL Database 탄력적 풀은 언제 고려해야 하나요?

풀은 특정 사용 패턴을 보이는 다수의 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다. 반대로, 영구 중간 사용률이 높은 여러 데이터베이스는 동일한 탄력적 풀에 배치 하면 안 됩니다.

풀에 더 많은 데이터베이스를 추가할수록 절감이 커집니다. 응용 프로그램 사용 패턴에 따라 두 개의 S3 데이터베이스를 사용 하 여 절감 액을 볼 수 있습니다.

다음 섹션에서는 데이터베이스의 특정 컬렉션이 풀에 있는 것이 이득이 될 수 있는지 평가하는 방법을 이해할 수 있습니다. 예제에서는 표준 풀을 사용하지만 기본 및 프리미엄 풀에도 동일한 원칙이 적용됩니다.

### <a name="assessing-database-utilization-patterns"></a>데이터베이스 사용량 패턴 평가

다음 그림에는 대부분의 시간을 유휴 상태로 있지만 정기적으로 갑작스러운 활동이 증가하는 데이터베이스의 예가 나와있습니다. 다음은 풀에 적합한 사용률 패턴입니다.

   ![풀에 적합한 단일 데이터베이스](./media/elastic-pool-overview/one-database.png)

위에서 보여 주는 5분 주기에서 DB1은 최대 90DTU까지 도달하지만 전체 평균 사용량은 5DTU 미만입니다. 단일 데이터베이스에서 이 워크로드를 실행하려면 S3 컴퓨팅 크기가 필요하지만 활동이 적은 기간 동안 리소스의 대부분을 사용하지 않게 됩니다.

풀을 사용하면 이러한 사용하지 않는 DTU를 여러 데이터베이스에서 공유할 수 있으므로 필요한 DTU 및 전체 비용을 줄일 수 있습니다.

앞의 예제에 기초하여, DB1같은 유사한 사용률 패턴의 추가 데이터베이스가 있다고 가정합니다. 아래 두 그림에서 4개의 데이터베이스 사용률 및 20개의 데이터베이스는 동일한 그래프에 계층화되어 시간에 따라 DTU 기반 구매 모델을 사용하여 해당 사용률의 특성이 겹치지 않는다는 것을 설명합니다.

   ![풀에 적합한 사용 패턴을 가진 네 개의 데이터베이스](./media/elastic-pool-overview/four-databases.png)

   ![풀에 적합한 사용 패턴을 가진 20개의 데이터베이스](./media/elastic-pool-overview/twenty-databases.png)

전체 20개 데이터베이스의 총 DTU 사용률은 위 그림에서 검은색 선으로 표시되었습니다. 이는 DTU 사용률 집계가 100 DTU를 초과하지 않음을 보여 주며, 이 기간 동안 20개 데이터베이스가 100 eDTU를 공유할 수 있음을 나타냅니다. 이렇게 하면 각 데이터베이스를 단일 데이터베이스용 S3 컴퓨팅 크기에 배치할 때와 비교하여 DTU가 1/20, 가격이 1/13로 감소합니다.

이 예는 다음 이유로 이상적입니다.

- 최고사용률과 데이터베이스당 평균 사용률간에 큰 차이가 있습니다.
- 각 데이터베이스의 최고 사용률이 시간 내에 여러 지점에서 발생합니다.
- eDTU는 많은 데이터베이스 간에 공유됩니다.

풀의 가격은 풀 eDTU의 함수입니다. 풀의 eDTU 단가는 단일 데이터베이스에 대한 DTU 단가보다 1.5배지만, **많은 데이터베이스가 풀 eDTU를 공유할 수 있고 필요한 전체 eDTU가 적습니다**. 가격 책정 및 eDTU 공유에서의 이러한 차이가 풀이 제공할 수 있는 가격 절감 가능성의 기초가 됩니다.

데이터베이스 수 및 데이터베이스 사용에 관련된 다음 규칙은 풀이 단일 데이터베이스에 대한 컴퓨팅 크기를 사용하는 데 비해 비용 절감을 제공하는 데 도움이 됩니다.

### <a name="minimum-number-of-databases"></a>데이터베이스의 최소 수

단일 데이터베이스에 대한 리소스의 집계 양이 풀에 필요한 리소스의 1.5배 이상인 경우 탄력적 풀은 더욱 비용 효율적입니다.

***DTU 기반 구매 모델 예제*** 단일 데이터베이스에 대해 계산 크기를 사용 하는 것 보다 더 비용 효율적으로 100 eDTU 풀을 사용 하려면 최소 두 개의 S3 데이터베이스 또는 최소 15 개의 S0 데이터베이스가 필요 합니다.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>최대 동시에 최고 데이터베이스

리소스를 공유하면 풀의 일부 데이터베이스가 단일 데이터베이스에 대한 사용 가능한 제한까지 리소스를 동시에 사용할 수 없습니다. 동시에 최대 사용량에 도달하는 데이터베이스 수가 작을수록 더 낮은 풀 리소스를 설정할 수 있으며 풀도 더 비용 효율적입니다. 일반적으로 리소스 제한까지 동시에 최대 사용량에 도달하는 풀의 데이터베이스 수가 2/3(또는 67%)를 초과하면 안 됩니다.

***DTU 기반 구매 모델 예제*** 200 eDTU 풀에 있는 세 개의 S3 데이터베이스에 대 한 비용을 줄이기 위해 이러한 데이터베이스 중 최대 두 개는 동시에 사용 될 수 있습니다. 이러한 S3 데이터베이스 4개 중 2개 이상이 동시에 최대 사용률에 도달하는 경우 200 eDTU 이상으로 풀 크기를 조정해야 합니다. 풀 크기가 200 eDTU보다 크게 조정될 경우 단일 데이터베이스용 컴퓨팅 크기보다 낮은 비용을 유지하려면 풀에 S3 데이터베이스를 더 많이 추가해야 합니다.

참고이 예에서는 풀에 있는 다른 데이터베이스의 사용률을 고려 하지 않습니다. 모든 데이터베이스 시간에 특정된 시점에서 일부 사용률의 경우, 데이터베이스의 2/3(또는 67%) 보다 작은 사용률이 동시에 정점에 달할 수 있습니다.

### <a name="resource-utilization-per-database"></a>데이터베이스당 리소스 사용률

데이터 베이스 사용률의 최고와 평균 사이의 큰 차이는 장시간 동안 낮은 사용률 그리고 짧은 시간 동안 큰 사용률을 나타냅니다. 이 사용률 패턴은 데이터베이스에서 리소스를 공유 하기에 이상적입니다. 최고 사용률이 평균사용률의 1.5배 이상이 될 때 풀에 대한 데이터베이스를 고려해야 합니다.

***DTU 기반 구매 모델 예제*** 100 Dtu 및 평균에 Dtu를 67 사용 하는 S3 데이터베이스는 풀에서 Edtu를 공유 하는 데 적합 합니다. 또는 S1 데이터베이스의 최고 사용률이 20 DTU이고 평균 사용률이 13 DTU 이하인 경우 풀에 적합합니다.

## <a name="how-do-i-choose-the-correct-pool-size"></a>올바른 풀 크기를 선택하려면 어떻게 해야 하나요?

풀의 적절한 크기는 풀의 모든 데이터베이스에 필요한 집계 리소스에 따라 달라집니다. 이는 다음 결정을 포함합니다.

- 풀의 모든 데이터베이스에서 사용 되는 최대 리소스 (선택한 구매 모델에 따라 최대 Dtu 또는 최대 vCores).
- 풀에 있는 모든 데이터베이스의 최대 스토리지된 바이트 수입니다.

각 리소스 모델에 대 한 사용 가능한 서비스 계층 및 제한은 [DTU 기반 구매 모델](service-tiers-dtu.md) 또는 [vcore 기반 구매 모델](service-tiers-vcore.md)을 참조 하세요.

다음 단계는 풀이 단일 데이터베이스 보다 비용 효율적인 지 여부를 예측 하는 데 도움이 될 수 있습니다.

1. 다음 수식에 따라 풀에 필요한 eDTU 또는 vCore를 예측합니다.

DTU 기반 구매 모델의 경우:

최대 (<*Total number of DBs* *DB 당 총 DB x 평균 dtu 사용률*>, <*동시 최고* *db x 초당 최대 dtu 사용률*>)

vCore 기반 구매 모델의 경우:

최대 (<*Total number of DBs* *DB 당 총 Db x 평균 vcore 사용률*>, <*동시 최고* *db x 최대 vcore 사용률*>)

2. 풀에서 모든 데이터베이스에 필요한 바이트 수를 추가하여 풀에 필요한 스토리지 공간을 예측합니다. 그런 다음 이 스토리지의 양을 제공하는 eDTU 풀 크기를 결정합니다.
3. DTU 기반 구매 모델의 경우 1단계 및 2단계에서 eDTU 예상 중 큰 수를 사용합니다. VCore 기반 구매 모델의 경우 1단계의 vCore 예상을 사용합니다.
4. [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 확인하고 3단계의 예상보다 큰 경우 가장 작은 풀 크기를 찾습니다.
5. 5단계의 풀 가격을 적절한 단일 데이터베이스용 컴퓨팅 크기를 사용할 때의 가격과 비교해 보세요.

> [!IMPORTANT]
> 풀의 데이터베이스 수가 지원 되는 최대 크기에 도달 하는 경우에는 [조밀한 탄력적 풀에서 리소스 관리](elastic-pool-resource-management.md)를 고려 해야 합니다.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>탄력적 풀과 기타 SQL Database 기능 사용

### <a name="elastic-jobs-and-elastic-pools"></a>탄력적 작업 및 탄력적 풀

풀을 통해 **[탄력적 작업](elastic-jobs-overview.md)** 의 스크립트를 실행하여 관리 작업이 간소화됩니다. 탄력적 작업은 많은 수의 데이터베이스와 관련된 번거로움을 대부분 없애 줍니다.

여러 데이터베이스를 사용하기 위한 다른 데이터베이스 도구에 대한 자세한 내용은 [Azure SQL Database를 사용하여 확장](elastic-scale-introduction.md)을 참조합니다.

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>탄력적 풀의 데이터베이스에 대한 비즈니스 연속성 옵션

풀링된 데이터베이스는 일반적으로 단일 데이터베이스에서 사용할 수 있는 동일한 [비즈니스 연속성 기능](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 지원합니다.

- **지정 시간 복원**

  지정 시간 복원은 자동 데이터베이스 백업을 사용하여 풀에 있는 데이터베이스를 지정 시간으로 복원합니다. [지정 시간 복원](recovery-using-backups.md#point-in-time-restore)

- **지역 복원**

  지역 복원은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션을 제공합니다. [Azure SQL Database 복원 또는 보조 데이터베이스에 대한 장애 조치(failover)](disaster-recovery-guidance.md)

- **활성 지역 복제**

  지역 복원에서 제공하는 것보다 더 까다로운 복구 요구 사항이 있는 애플리케이션의 경우 [활성 지역 복제](active-geo-replication-overview.md) 또는 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 구성합니다.

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure Portal을 사용하여 새 SQL Database 탄력적 풀 만들기

Azure Portal에서 두 가지 방법으로 탄력적 풀을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 하 여 탄력적 풀을 만듭니다. **AZURE SQL**을 검색 하 고 선택 합니다.
2. **+추가**를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. **데이터베이스** 타일에 대 한 **자세한 정보 표시** 를 선택 하 여 탄력적 풀에 대 한 추가 정보를 볼 수 있습니다.
3. **데이터베이스** 타일의 **리소스 종류** 드롭다운에서 **탄력적 풀** 을 선택 하 고 **만들기**를 선택 합니다.

   ![탄력적 풀 만들기](./media/elastic-pool-overview/create-elastic-pool.png)

4. 또는 기존 서버로 이동 하 고 **+ 새 풀** 을 클릭 하 여 해당 서버에 직접 풀을 만드는 방식으로 탄력적 풀을 만들 수 있습니다.

> [!NOTE]
> 서버에 풀을 여러 개 만들 수 있지만 다른 서버에 속하는 데이터베이스를 동일한 풀에 추가할 수 없습니다.

풀의 서비스 계층에 따라 풀에 있는 탄력적 데이터베이스에서 사용 가능한 기능과 각 데이터베이스에서 사용 가능한 최대 리소스 양이 결정됩니다. 자세한 내용은 [DTU 모델](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)에서 탄력적 풀에 대한 리소스 제한을 참조하세요. 탄력적 풀에 대한 vCore 기반 리소스 제한은 [vCore 기반 리소스 제한 - 탄력적 풀](resource-limits-vcore-elastic-pools.md)을 참조하세요.

리소스 및 풀의 가격 책정을 구성하려면 **풀 구성**을 클릭합니다. 그런 다음, 서비스 계층을 선택하고, 풀에 데이터베이스를 추가하고, 풀과 해당 데이터베이스에 대한 리소스 제한을 구성합니다.

풀 구성을 완료하면 '적용'을 클릭하고, 풀의 이름을 지정하고, '확인'을 클릭하여 풀을 만들 수 있습니다.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>탄력적 풀 및 해당 데이터베이스 모니터링

Azure Portal에서 탄력적 풀 및 해당 풀 내의 데이터베이스의 사용률을 모니터링할 수 있습니다. 탄력적 풀에 일련의 내용을 변경하는 동시에 모든 변경 내용을 전송할 수도 있습니다. 이러한 변경 내용에는 데이터베이스 추가 또는 제거, 탄력적 풀 설정 변경, 데이터베이스 설정 변경이 포함됩니다.

탄력적 풀 모니터링을 시작하려면 포털에서 탄력적 풀을 찾아서 엽니다. 먼저 탄력적 풀의 상태에 대 한 개요를 제공 하는 화면이 표시 됩니다. 다음 내용이 포함됩니다.

- 탄력적 풀의 리소스 사용을 보여주는 모니터링 차트
- 사용 가능한 경우 탄력적 풀에 대한 최근 경고 및 권장 사항

다음 그림에서는 탄력적 풀 예제를 보여줍니다.

![풀 보기](./media/elastic-pool-overview/basic.png)

풀에 대해 자세히 알아보려는 경우 이 개요에서 사용할 수 있는 모든 정보를 클릭할 수 있습니다. **리소스 사용률** 차트를 클릭하면 차트에 표시된 메트릭 및 시간 창을 사용자 지정할 수 있는 Azure 모니터링 보기로 이동합니다. 사용 가능한 모든 알림을 클릭하면 해당 경고 또는 권장 사항의 전체 내용을 보여주는 블레이드로 이동합니다.

풀 내의 데이터베이스를 모니터링하려는 경우 왼쪽에 있는 리소스 메뉴의 **모니터링** 섹션에서 **데이터베이스 리소스 사용률**을 클릭할 수 있습니다.

![데이터베이스 리소스 사용률 페이지](./media/elastic-pool-overview/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>차트 표시를 사용자 지정하려면

차트 및 메트릭 페이지를 편집하여 CPU 비율, 데이터 IO 비율, 사용되는 로그 IO 백분율 등 다른 메트릭을 표시할 수 있습니다.

**차트 편집** 양식에서 고정된 시간 범위를 선택하거나 **사용자 지정**을 클릭하여 지난 2주 동안에서 24시간 범위를 선택한 다음, 모니터링할 리소스를 선택할 수 있습니다.

### <a name="to-select-databases-to-monitor"></a>모니터링할 데이터베이스를 선택하려면

기본적으로 **데이터베이스 리소스 사용률** 블레이드의 차트는 DTU 또는 CPU로 상위 5개의 데이터베이스를 표시합니다(서비스 계층에 따라). 왼쪽의 확인란을 통해 차트 아래의 목록에서 데이터베이스를 선택하고 선택 취소하여 이 차트에서 데이터베이스를 전환할 수 있습니다.

또한 데이터베이스 성능의 더욱 완전한 보기를 가져오기 위해 이 데이터베이스 테이블에서 나란히 보도록 추가 메트릭을 선택할 수도 있습니다.

자세한 내용은 [Azure Portal에서 SQL Database 경고 만들기](alerts-insights-configure-portal.md)를 참조하세요.

## <a name="customer-case-studies"></a>고객 사례 연구

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart는 Azure SQL Database에서 탄력적 풀을 사용 하 여 매월 1000 개의 새 Azure SQL database의 속도로 비즈니스 서비스를 빠르게 확장 했습니다.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco는 Azure SQL Database에서 탄력적 풀을 사용 하 여 클라우드에서 수천 개의 테 넌 트에 대해 서비스를 신속 하 게 프로 비전 하 고 확장 합니다.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI는 Azure SQL Database에서 탄력적 풀을 사용 하 여 개발 주기를 가속화 하 고 고객 서비스 및 성능을 향상 시킵니다.

## <a name="next-steps"></a>다음 단계

- 가격 책정 정보는 [탄력적 풀 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/elastic)을 참조 하세요.
- 탄력적 풀의 크기를 조정하려면 [탄력적 풀 크기 조정](elastic-pool-scale.md) 및 [탄력적 풀 크기 조정 - 샘플 코드](scripts/monitor-and-scale-pool-powershell.md)를 참조하세요.
- 탄력적 풀을 사용한 SaaS 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.
- 탄력적 풀을 사용하는 SaaS 자습서의 경우 [Wingtip SaaS 애플리케이션 소개](saas-dbpertenant-wingtip-app-overview.md)를 참조하세요.
- 여러 데이터베이스가 포함 된 탄력적 풀의 리소스 관리에 대해 알아보려면 [조밀한 탄력적 풀의 리소스 관리](elastic-pool-resource-management.md)를 참조 하세요.

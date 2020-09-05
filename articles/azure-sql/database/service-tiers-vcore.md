---
title: vCore 구매 모델 개요
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: VCore 구매 모델을 사용 하면 계산 및 저장소 리소스를 독립적으로 확장 하 고, 온-프레미스 성능과 일치 하며, Azure SQL Database 및 Azure SQL Managed Instance 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 08/14/2020
ms.openlocfilehash: 7131ddac840d2854969147da2eeb82a890ce3410
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586820"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>vCore 모델 개요-Azure SQL Database 및 Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 Azure SQL Managed Instance에서 사용 하는 가상 코어 (vCore) 구매 모델은 몇 가지 이점을 제공 합니다.

- 더 높은 계산, 메모리, i/o 및 저장소 제한
- 워크 로드에 대 한 계산 및 메모리 요구 사항을 더 잘 맞추려면 하드웨어 생성을 제어 합니다.
- [Azure 하이브리드 혜택 (AHB)](../azure-hybrid-benefit.md) 및 [예약 인스턴스 (RI)](reserved-capacity-overview.md)의 가격 할인.
- 계산을 지 원하는 하드웨어 세부 정보의 투명도를 향상 하 여 온-프레미스 배포에서의 마이그레이션 계획을 용이 하 게 합니다.

## <a name="service-tiers"></a>서비스 계층

VCore 모델의 서비스 계층 옵션에는 범용, 중요 비즈니스용 및 Hyperscale이 포함 됩니다. 서비스 계층은 일반적으로 가용성 및 재해 복구와 관련 된 저장소 아키텍처, 공간 및 i/o 제한 및 비즈니스 연속성 옵션을 정의 합니다.

|-|**범용**|**중요 비즈니스용**|**하이퍼스케일**|
|---|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 예산에 맞게 균형 있고 확장 가능한 컴퓨팅 및 스토리지 옵션을 제공합니다. |는 여러 개의 격리 된 복제본을 사용 하 여 비즈니스 응용 프로그램에서 오류에 대 한 가장 높은 복원 력을 제공 하 고, 데이터베이스 복제본 별로 최고 i/o 성능을 제공 합니다.|확장성이 뛰어난 저장소 및 읽기 확장 요구 사항에 대 한 대부분의 비즈니스 워크 로드.  에서는 둘 이상의 격리 된 데이터베이스 복제본의 구성을 허용 하 여 오류에 대 한 더 높은 복원 력을 제공 합니다. |
|스토리지|원격 저장소를 사용 합니다.<br/>**프로 비전 된 계산 SQL Database**:<br/>5GB~4TB<br/>**서버**를 사용 하지 않는 계산:<br/>5GB-3TB<br/>**SQL Managed Instance**: 32 g b-8tb |로컬 SSD 저장소를 사용 합니다.<br/>**프로 비전 된 계산 SQL Database**:<br/>5GB~4TB<br/>**SQL Managed Instance**:<br/>32GB~4TB |필요에 따라 저장소를 유연 하 게 자동 증가 는 최대 100 TB의 저장소를 지원 합니다. 로컬 버퍼 풀 캐시 및 로컬 데이터 저장소에 로컬 SSD 저장소를 사용 합니다. Azure 원격 저장소를 최종 장기 데이터 저장소로 사용 합니다. |
|IOPS 및 처리량 (근사치)|**SQL Database**: [단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md)에 대 한 리소스 제한을 참조 하세요.<br/>**Sql Managed Instance**: [개요 Azure SQL Managed Instance 리소스 제한](../managed-instance/resource-limits.md#service-tier-characteristics)을 참조 하세요.|[단일 데이터베이스](resource-limits-vcore-single-databases.md) 및 [탄력적 풀](resource-limits-vcore-elastic-pools.md)에 대 한 리소스 제한을 참조 하세요.|Hyperscale은 여러 수준에서 캐싱을 사용 하는 다중 계층 아키텍처입니다. 효과적인 IOPS 및 처리량은 워크 로드에 따라 달라 집니다.|
|가용성|복제본 1 개, 읽기 확장 복제본 없음|3개 복제본, 1개 [읽기 크기 조정 복제본](read-scale-out.md),<br/>영역 중복 HA (고가용성)|1 읽기/쓰기 복제본 및 0-4 [읽기 확장 복제본](read-scale-out.md)|
|Backup|[읽기 액세스 지역 중복 저장소 (RA-GRS)](../../storage/common/geo-redundant-design.md), 7-35 일 (기본적으로 7 일)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 7-35일(기본값: 7일)|Azure 원격 저장소의 스냅숏 기반 백업 복원은 빠른 복구를 위해 이러한 스냅샷을 사용합니다. 백업은 즉시 수행 되며 계산 i/o 성능에 영향을 주지 않습니다. 복원은 빠르게 수행 하 고 데이터의 크기를 조정 하는 작업이 아닙니다 (몇 시간 또는 몇 일이 아닌 분 소요).|
|메모리 내|지원되지 않음|지원됨|지원되지 않음|
|||


### <a name="choosing-a-service-tier"></a>서비스 계층 선택

특정 워크 로드에 대 한 서비스 계층을 선택 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [범용 서비스 계층을 선택 하는 경우](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [중요 비즈니스용 서비스 계층을 선택 하는 경우](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Hyperscale 서비스 계층을 선택 하는 경우](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Compute 계층

VCore 모델의 compute 계층 옵션에는 프로 비전 및 서버를 사용 하지 않는 계산 계층이 포함 됩니다.


### <a name="provisioned-compute"></a>프로비저닝된 컴퓨팅

프로 비전 된 계산 계층은 작업 활동에 독립적으로 지속적으로 프로 비전 되는 특정 양의 계산 리소스를 제공 하 고 시간당 고정 된 가격으로 프로 비전 된 계산의 양에 대 한 요금을 제공 합니다.


### <a name="serverless-compute"></a>서버리스 컴퓨팅

서버를 사용 하지 않는 [계산 계층](serverless-tier-overview.md) 은 작업 활동에 따라 계산 리소스를 자동으로 조정 하 고, 초당 사용 된 계산 양에 대 한 요금을 청구 합니다.



## <a name="hardware-generations"></a>하드웨어 생성

VCore 모델의 하드웨어 생성 옵션에는 Gen 4/5, M 시리즈 및 Fsv2 시리즈가 포함 됩니다. 하드웨어 생성은 일반적으로 계산 및 메모리 제한과 작업의 성능에 영향을 주는 기타 특성을 정의 합니다.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 하드웨어는 균형 잡힌 계산 및 메모리 리소스를 제공 하며, Fsv2 시리즈 또는 M 시리즈에서 제공 되는 것과 같은 더 높은 메모리, 더 높은 vCore 또는 더 빠른 단일 vCore 요구 사항이 없는 대부분의 데이터베이스 작업에 적합 합니다.

Gen4/Gen5을 사용할 수 있는 지역의 경우 [Gen4/Gen5 availability](#gen4gen5-1)를 참조 하세요.

### <a name="fsv2-series"></a>Fsv2 시리즈

- Fsv2 시리즈는 cpu 대기 시간이 낮고 CPU가 까다로운 워크 로드에 대해 높은 클록 속도를 제공 하는 계산에 최적화 된 하드웨어 옵션입니다.
- 워크 로드에 따라 Fsv2 시리즈는 Gen5 보다 vCore 당 더 많은 CPU 성능을 제공할 수 있으며, 72 vCore 크기는 Gen5에서 80 Vcore 보다 적은 비용으로 더 많은 CPU 성능을 제공할 수 있습니다. 
- Fsv2는 다른 하드웨어 보다 vCore의 메모리와 tempdb를 더 작게 제공 하므로 이러한 한도에 영향을 주는 워크 로드는 Gen5 또는 M 시리즈를 대신 고려 하는 것이 좋습니다.  

Fsv2 시리즈는 일반적인 용도의 계층 에서만 지원 됩니다. Fsv2 시리즈를 사용할 수 있는 지역의 경우 [Fsv2 시리즈 가용성](#fsv2-series-1)을 참조 하세요.


### <a name="m-series"></a>M 시리즈

- M 시리즈는 Gen5에서 제공 하는 것 보다 더 많은 메모리를 필요로 하는 워크 로드에 대 한 메모리 액세스에 최적화 된 하드웨어 옵션입니다.
- M 시리즈는 vCore 당 30GB를 제공 하 고 최대 128 Vcore를 제공 하 여 Gen5 by 8x를 기준으로 약 4 TB까지 메모리 제한을 늘립니다.

M 시리즈는 중요 비즈니스용 계층 에서만 지원 되며 영역 중복성은 지원 하지 않습니다.  구독은 종 량 제 또는 기업계약 (EA)를 포함 한 유료 제안 유형 이어야 합니다. M 시리즈를 사용할 수 있는 지역에 대해서는 [m 시리즈 가용성](#m-series-1)을 참조 하세요.

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>계산 및 메모리 사양


|하드웨어 세대  |Compute  |메모리  |
|:---------|:---------|:---------|
|Gen4     |-Intel® E5-2673 v3 (Haswell) 2.4 GHz 프로세서<br>-최대 24 개의 vCores 프로 비전 (1 개 Vcores = 1 실제 코어)  |-vCore 당 7GB<br>-최대 168 GB 프로 비전|
|5세대     |**프로비저닝된 컴퓨팅**<br>-Intel® E5-2673 v4 (Broadwell) 2.3 GHz, Intel® SP-8160 (Skylake) \* 및 intel® 8272CL (케스케이드 Lake) 2.5 GHz \* 프로세서<br>-최대 80 Vcores 프로 비전 (1 개 Vcores = 1 개 하이퍼 스레드)<br><br>**서버리스 컴퓨팅**<br>-Intel® E5-2673 v4 (Broadwell) 2.3 GHz 및 Intel® SP-8160 (Skylake) * 프로세서<br>-최대 40 vCores 자동 확장 (1 Vcores = 1 하이퍼 스레드)|**프로비저닝된 컴퓨팅**<br>-vCore 당 5.1 GB<br>-최대 408 GB 프로 비전<br><br>**서버리스 컴퓨팅**<br>-VCore 당 최대 24gb까지 자동 확장<br>-최대 120 GB까지 자동 확장|
|Fsv2 시리즈     |-Intel® 8168 (Skylake) 프로세서<br>-3.4 GHz의 모든 코어 터보 클록 속도와 3.7 g h z의 싱글 코어 터보 클록 속도를 모두 유지 합니다.<br>-최대 72 Vcores 프로 비전 (1 개 Vcores = 1 개 하이퍼 스레드)|-vCore 당 1.9 GB<br>-최대 136 GB 프로 비전|
|M 시리즈     |-Intel® E7-8890 v3 2.5 GHz 및 Intel® 8280M 2.7 GHz (캐스케이드 Lake) 프로세서<br>-최대 128 Vcores 프로 비전 (1 개 Vcores = 1 개 하이퍼 스레드)|-vCore 당 30GB<br>-최대 3.7 TB 프로 비전|

\*[Dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 동적 관리 뷰에서는 INTEL® SP-8160 (Skylake) 프로세서를 사용 하는 데이터베이스에 대 한 하드웨어 생성이 Gen6로 표시 되 고 INTEL® 8272CL (Cascade Lake)를 사용 하는 데이터베이스에 대 한 하드웨어 생성은 Gen7로 표시 됩니다. 모든 Gen5 데이터베이스에 대 한 리소스 제한은 프로세서 유형 (Broadwell, Skylake 또는 Cascade Lake)에 관계 없이 동일 합니다.

리소스 제한에 대 한 자세한 내용은 [단일 데이터베이스에 대 한 리소스 제한 (vcore)](resource-limits-vcore-single-databases.md)또는 [탄력적 풀에 대 한 리소스 제한 (vcore)](resource-limits-vcore-elastic-pools.md)을 참조 하세요.

### <a name="selecting-a-hardware-generation"></a>하드웨어 생성 선택

Azure Portal에서 만들 때 SQL Database의 데이터베이스 또는 풀에 대 한 하드웨어 생성을 선택 하거나 기존 데이터베이스 또는 풀의 하드웨어 생성을 변경할 수 있습니다.

**SQL Database 또는 풀을 만들 때 하드웨어 생성을 선택 하려면**

자세한 내용은 [SQL Database 만들기](single-database-create-quickstart.md)를 참조 하세요.

**기본** 탭의 **Compute + storage** 섹션에서 **데이터베이스 구성** 링크를 선택 하 고 **구성 변경** 링크를 선택 합니다.

  ![데이터베이스 구성](./media/service-tiers-vcore/configure-sql-database.png)

원하는 하드웨어 생성을 선택 합니다.

  ![하드웨어 선택](./media/service-tiers-vcore/select-hardware.png)


**기존 SQL Database 또는 풀의 하드웨어 생성을 변경 하려면**

데이터베이스의 경우 개요 페이지에서 **가격 책정 계층** 링크를 선택 합니다.

  ![하드웨어 변경](./media/service-tiers-vcore/change-hardware.png)

풀의 경우 개요 페이지에서 **구성**을 선택 합니다.

단계에 따라 구성을 변경 하 고 이전 단계에서 설명한 대로 하드웨어 생성을 선택 합니다.

**SQL Managed Instance를 만들 때 하드웨어 생성을 선택 하려면**

자세한 내용은 [SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)를 참조 하세요.

**기본** 탭의 **Compute + storage** 섹션에서 **데이터베이스 구성** 링크를 선택 하 고 원하는 하드웨어 생성을 선택 합니다.

  ![SQL Managed Instance 구성](./media/service-tiers-vcore/configure-managed-instance.png)
  
**기존 SQL Managed Instance의 하드웨어 생성을 변경 하려면**

# <a name="the-azure-portal"></a>[Azure 포털](#tab/azure-portal)

SQL Managed Instance 페이지에서 설정 섹션 아래에 있는 **가격 책정 계층** 링크를 선택 합니다.

![SQL Managed Instance 하드웨어 변경](./media/service-tiers-vcore/change-managed-instance-hardware.png)

가격 책정 계층 페이지에서 이전 단계에 설명 된 대로 하드웨어 생성을 변경할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용합니다.

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

자세한 내용은 [AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) 명령을 확인 하세요.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 사용 합니다.

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

자세한 내용은 [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) 명령을 확인 하세요.

---

### <a name="hardware-availability"></a>고가용성

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

Gen4 하드웨어가 [단계적으로 진행](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) 되 고 있으며 새 배포에 더 이상 사용할 수 없습니다. 모든 새 데이터베이스는 Gen5 하드웨어에 배포 되어야 합니다.

Gen5는 전 세계 대부분의 지역에서 사용할 수 있습니다.

#### <a name="fsv2-series"></a>Fsv2 시리즈

Fsv2 시리즈는 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 오스트레일리아 남동쪽, 브라질 남부, 캐나다 중부, 동아시아, 미국 동부, 프랑스 중부, 인도 중부, 인도 서 부, 대한민국 중부, 한국 남부, 북부 유럽, 남아프리카 공화국 북부, 동남 아시아, 영국 남부, 영국 서부, 유럽 서부, 미국 서 부 2 지역에서 사용할 수 있습니다.


#### <a name="m-series"></a>M 시리즈

M 시리즈는 미국 동부, 유럽 서 부, 유럽 서부, 미국 서 부 2 지역에서 사용할 수 있습니다.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>다음 단계

시작하려면 다음을 참조하십시오. 
- [Azure Portal를 사용 하 여 SQL Database 만들기](single-database-create-quickstart.md)
- [Azure Portal를 사용 하 여 SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)

가격 책정에 대 한 자세한 내용은 [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조 하세요.

범용 및 중요 비즈니스용 서비스 계층에서 사용할 수 있는 특정 계산 및 저장소 크기에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure SQL Database에 대 한 Vcore 기반 리소스 제한](resource-limits-vcore-single-databases.md)입니다.
- [풀링된 Azure SQL Database에 대 한 Vcore 기반 리소스 제한](resource-limits-vcore-elastic-pools.md)입니다.
- [AZURE SQL Managed Instance에 대 한 Vcore 기반 리소스 제한](../managed-instance/resource-limits.md)입니다. 


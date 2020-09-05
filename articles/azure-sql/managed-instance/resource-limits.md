---
title: 리소스 한계
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance에 대 한 리소스 제한에 대 한 개요를 제공 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 08/14/2020
ms.openlocfilehash: 902fa34be149f0b876729409c530186e34c706e5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587313"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL Managed Instance 리소스 제한 개요
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance의 기술 특성 및 리소스 제한에 대 한 개요를 제공 하 고 이러한 제한에 대 한 증가를 요청 하는 방법에 대 한 정보를 제공 합니다.

> [!NOTE]
> 지원되는 기능 및 T-SQL 문의 차이점은 [기능 차이](../database/features-comparison.md) 및 [T-SQL 문 지원](transact-sql-tsql-differences-sql-server.md)을 참조하세요. Azure SQL Database 및 SQL에 대 한 서비스 계층 간의 일반적인 차이점은 [서비스 계층 비교](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)를 참조 Managed Instance.

## <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

SQL Managed Instance에는 기본 인프라와 아키텍처에 따라 달라 지는 특성 및 리소스 제한이 있습니다. SQL Managed Instance는 두 가지 하드웨어 세대 (Gen4 및 Gen5)에 배포할 수 있습니다. 하드웨어 생성에는 다음 표에 설명 된 대로 다른 특징이 있습니다.

|   | **Gen4** | **5세대** |
| --- | --- | --- |
| **하드웨어** | Intel® E5-2673 v3 (Haswell) 2.4 g h z 프로세서, 연결 된 SSD vCore = 1PP (물리적 코어) | Intel® E5-2673 v4 (Broadwell) 2.3 g h z, Intel® SP-8160 (Skylake) 및 Intel® 8272CL (Cascade Lake) 2.5 GHz 프로세서, fast NVMe SSD, vCore = 1 LP (하이퍼 스레드) |
| **VCores 수** | 8, 16, 24개 vCore | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| **최대 메모리 (메모리/코어 비율)** | vCore당 7GB<br/>더 많은 메모리를 얻기 위해 vCores를 추가 합니다. | vCore당 5.1GB<br/>더 많은 메모리를 얻기 위해 vCores를 추가 합니다. |
| **최대 메모리 내 OLTP 메모리** | 인스턴스 제한: 1-vCore 당 1.5 g b| 인스턴스 제한: 0.8-vCore 당 1.65 GB |
| **최대 인스턴스 예약 된 저장소** |  일반 용도: 8TB<br/>중요 비즈니스용: 1TB | 일반 용도: 8TB<br/> 중요 비즈니스용 1tb, 2tb 또는 4 TB의 코어 수에 따라 |

> [!IMPORTANT]
> - Gen4 하드웨어가 단계적으로 진행 되 고 있으며 새 배포에 더 이상 사용할 수 없습니다. SQL Managed Instance의 모든 새 인스턴스는 Gen5 하드웨어에 배포 되어야 합니다.
> - [SQL Managed Instance 인스턴스를 Gen 5 하드웨어로 이동](../database/service-tiers-vcore.md) 하 여 더 광범위 한 vcore 및 저장소 확장성, 가속화 된 네트워킹, 최적의 IO 성능 및 최소 대기 시간을 경험해 보세요.

### <a name="in-memory-oltp-available-space"></a>메모리 내 OLTP 사용 가능한 공간 

[중요 비즈니스용](../database/service-tier-business-critical.md) 서비스 계층의 메모리 내 OLTP 공간의 양은 vcores 및 하드웨어 생성 수에 따라 달라 집니다. 다음 표에는 메모리 내 OLTP 개체에 사용할 수 있는 메모리 한도가 정리 되어 있습니다.

| 메모리 내 OLTP 공간  | **5세대** | **Gen4** |
| --- | --- | --- |
| vCore 4개  | 3.14 GB | |   
| vCore 8개  | 6.28 GB | 8GB |
| 16 개 vCores | 15.77 GB | 20GB |
| 24 개 vCores | 25.25 GB | 36 GB |
| 32 vCores | 37.94 GB | |
| 40 vCores | 52.23 GB | |
| 64 vCores | 99.9 GB    | |
| 80 vCores | 131.68 GB| |

## <a name="service-tier-characteristics"></a>서비스 계층 특성

SQL Managed Instance는 두 가지 서비스 계층 [, 즉 범용 및](../database/service-tier-general-purpose.md) [중요 비즈니스용](../database/service-tier-business-critical.md)있습니다. 이러한 계층은 아래 표에 설명 된 대로 [다양 한 기능](../database/service-tiers-general-purpose-business-critical.md)을 제공 합니다.

> [!Important]
> 중요 비즈니스용 서비스 계층은 읽기 전용 작업에 사용할 수 있는 SQL Managed Instance (보조 복제본)의 추가 기본 복사본을 제공 합니다. 읽기/쓰기 쿼리와 읽기 전용/분석/보고 쿼리를 구분할 수 있는 경우 동일한 가격에 대 한 vCores와 메모리의 두 배를 얻을 수 있습니다. 보조 복제본은 주 인스턴스에서 몇 초 정도 지연 될 수 있으므로 데이터의 정확한 현재 상태를 필요로 하지 않는 보고/분석 워크 로드를 오프 로드 하도록 설계 되었습니다. 아래 표에서 **읽기 전용 쿼리** 는 보조 복제본에서 실행 되는 쿼리입니다.

| **기능** | **범용** | **중요 비즈니스용** |
| --- | --- | --- |
| vCore 수\* | Gen4: 8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*동일한 수의 vCores는 읽기 전용 쿼리에 전용으로 사용할 수 있습니다. |
| 최대 메모리 | Gen4:56 g b-168 GB (7GB/vCore)<br/>Gen5:20.4 g b-408 GB (5.1 g b/vCore)<br/>더 많은 메모리를 얻기 위해 vCores를 추가 합니다. | Gen4:56 g b-168 GB (7GB/vCore)<br/>Gen5: 읽기-쓰기 쿼리에 20.4 g b-408 GB (5.1 g b/vCore)<br/>+ 읽기 전용 쿼리에 20.4 g b-408 GB (5.1 g b/vCore)가 추가 되었습니다.<br/>더 많은 메모리를 얻기 위해 vCores를 추가 합니다. |
| 최대 인스턴스 저장소 크기 (예약 됨) | -4 vCores의 경우 2tb (Gen5만 해당)<br/>-다른 크기의 경우 8TB | Gen4:1TB <br/> Gen5: <br/>-1tb (4, 8, 16 vCores)<br/>- 2TB(24개 vCore용)<br/>- 4TB(32, 40, 64, 80개 vCore용) |
| 최대 데이터베이스 크기 | 현재 사용할 수 있는 인스턴스 크기 (vCores의 수에 따라 최대 2tb-8gb TB) | 현재 사용 가능한 인스턴스 크기 (vCores의 수에 따라 최대 1tb-4tb) |
| 최대 tempDB 크기 | 24gb/vCore (96-1920 GB) 및 현재 사용할 수 있는 인스턴스 저장소 크기로 제한 됩니다.<br/>더 많은 vCores를 추가 하 여 TempDB 공간을 더 확보 합니다.<br/> 로그 파일 크기는 120 GB로 제한 됩니다.| 현재 사용할 수 있는 인스턴스 저장소 크기까지 |
| 인스턴스당 최대 데이터베이스 수 | 100, 인스턴스 저장소 크기 제한에 도달 하지 않은 경우 | 100, 인스턴스 저장소 크기 제한에 도달 하지 않은 경우 |
| 인스턴스당 데이터베이스 파일의 최대 수 | 인스턴스 저장소 크기 또는 [Azure Premium Disk storage 할당 공간](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) 제한에 도달 하지 않는 한 최대 280입니다. | 32767 인스턴스 저장소 크기 제한에 도달 하지 않으면 데이터베이스당 파일 수입니다. |
| 최대 데이터 파일 크기 | 현재 사용할 수 있는 인스턴스 저장소 크기 (최대 2tb, 648TB) 및 [Azure Premium Disk storage 할당 공간](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)으로 제한 됩니다. | 현재 사용할 수 있는 인스턴스 저장소 크기 (최대 1tb-4 TB)로 제한 됩니다. |
| 최대 로그 파일 크기 | 2tb 및 현재 사용할 수 있는 인스턴스 저장소 크기로 제한 됩니다. | 2tb 및 현재 사용할 수 있는 인스턴스 저장소 크기로 제한 됩니다. |
| 데이터/로그 IOPS(근사치) | 인스턴스당 최대 30-40 K IOPS *, 500-7500/파일<br/>\*[더 많은 IOPS를 얻기 위해 파일 크기 늘리기](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (2500 IOPS/vCore)<br/>더 나은 IO 성능을 얻으려면 vCores를 더 추가 합니다. |
| 로그 쓰기 처리량 한도 (인스턴스당) | vCore당 3MB/초<br/>최대 22 m b/초 | vCore 당 4mb/s<br/>최대 48MB/초 |
| 데이터 처리량(근사치) | 파일당 100~250MB/초<br/>\*[더 나은 IO 성능을 얻으려면 파일 크기를 늘립니다.](#file-io-characteristics-in-general-purpose-tier) | 제한 되지 않습니다. |
| 저장소 IO 대기 시간 (근사치) | 5~10ms | 1~2ms |
| 메모리 내 OLTP | 지원되지 않음 | 사용 가능, [크기는 vCore의 수에 따라 다릅니다](#in-memory-oltp-available-space) . |
| 최대 세션 | 30000 | 30000 |
| 최대 동시 작업자(요청) | Gen4:210 * vCores 수 + 800<br>Gen5:105 * vCores 수 + 800 | Gen4:210 * vCore 수 + 800<br>Gen5:105 * vCore 수 + 800 |
| [읽기 전용 복제본](../database/read-scale-out.md) | 0 | 1 (가격에 포함 됨) |
| 계산 격리 | Gen5:<br/>-80 vCores에 지원 됨<br/>-다른 크기에 대해서는 지원 되지 않습니다.<br/><br/>Gen4는 사용 중단으로 인해 지원 되지 않습니다.|Gen5:<br/>-60, 64, 80 vCores에 지원 됨<br/>-다른 크기에 대해서는 지원 되지 않습니다.<br/><br/>Gen4는 사용 중단으로 인해 지원 되지 않습니다.|


몇 가지 추가 고려 사항: 

- **현재 사용할 수 있는 인스턴스 저장소 크기** 는 예약 된 인스턴스 크기와 사용 된 저장소 공간의 차이입니다.
- 사용자 및 시스템 데이터베이스의 데이터 및 로그 파일 크기는 모두 최대 저장소 크기 제한과 비교 하 여 인스턴스 저장소 크기에 포함 됩니다. [Master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) 시스템 뷰를 사용 하 여 데이터베이스에서 사용 하는 총 공간을 확인 합니다. 오류 로그는 영구적이지 않으며 크기에 포함되지 않습니다. 백업은 스토리지 크기에 포함되지 않습니다.
- 범용 계층의 처리량 및 IOPS는 SQL Managed Instance에 의해 명시적으로 제한 되지 않는 [파일 크기](#file-io-characteristics-in-general-purpose-tier) 에 따라서도 달라 집니다.
  [자동 장애 조치 그룹](../database/auto-failover-group-configure.md) 을 사용 하 여 다른 Azure 지역에서 읽을 수 있는 다른 복제본을 만들 수 있습니다.
- 최대 인스턴스 IOPS는 파일 레이아웃 및 워크 로드 배포에 따라 달라 집니다. 예를 들어 각각 최대 5K IOPS를 사용 하 여 7 x 1TB 파일을 만들고 각각 500 IOPS를 사용 하는 7 개의 작은 파일 (128 미만)을 만드는 경우 워크 로드에서 모든 파일을 사용할 수 있는 경우 인스턴스당 38500 IOPS (7x5000 + 7x500)를 가져올 수 있습니다. 일부 IOPS는 자동 백업에도 사용 됩니다.

[이 문서에서는 SQL Managed Instance 풀의 리소스 제한](instance-pools-overview.md#resource-limitations)에 대 한 자세한 정보를 확인 합니다.

### <a name="file-io-characteristics-in-general-purpose-tier"></a>일반적인 용도의 계층의 파일 IO 특성

범용 서비스 계층에서 모든 데이터베이스 파일은 파일 크기에 따라 달라 지는 전용 IOPS 및 처리량을 가져옵니다. 더 큰 데이터 파일은 더 많은 IOPS 및 처리량을 얻습니다. 데이터베이스 파일의 IO 특성은 다음 표에 나와 있습니다.

| 파일 크기 | >= 0 및 <= 128 GiB | >128 및 <= 256 GiB | >256 및 <= 512 GiB | >0.5 및 <= 1 TiB    | >1 및 <= 2 TiB    | >2 및 <= 4 TiB | >4 및 <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 파일당 IOPS       | 500   | 1100 | 2300              | 5,000              | 7,500              | 7,500              | 12,500   |
| 파일당 처리량 | 100MiB/초 | 125MiB/초 | 150MiB/초 | 200MiB/초 | 250MiB/초 | 250MiB/초 | 480 MiB/s | 

일부 데이터베이스 파일에서 높은 IO 대기 시간이 발생 하거나 IOPS/처리량이 제한에 도달 하는 것을 확인 한 경우 [파일 크기를 늘려서](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)성능을 향상 시킬 수 있습니다.

최대 로그 쓰기 처리량 (22 m b/초)에도 인스턴스 수준 제한이 있으므로 인스턴스 처리량 제한에 도달 하 여 로그 파일 전체에서 최대 파일에 도달 하지 못할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

SQL Managed Instance은 [지원 되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)에서만 만들 수 있습니다. 현재 지원 되지 않는 지역에서 SQL Managed Instance를 만들려면 [Azure Portal를 통해 지원 요청을 보낼](../database/quota-increase-request.md)수 있습니다.

## <a name="supported-subscription-types"></a>지원되는 구독 유형

SQL Managed Instance는 현재 다음 유형의 구독에 대 한 배포만 지원 합니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [CSP(클라우드 서비스 공급자)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 구독자를 위한 월간 Azure 크레딧 구독](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>지역별 리소스 제한

> [!Note]
> 구독에 대 한 지역 가용성에 대 한 최신 정보를 보려면 먼저 [지역을 선택](https://aka.ms/sqlcapacity)하세요.

지원되는 구독 유형에는 지역당 제한된 수의 리소스가 포함될 수 있습니다. SQL Managed Instance에는 Azure 지역 당 두 가지 기본 제한이 있습니다. 즉, 구독 유형의 유형에 따라 [Azure Portal에서 특수 지원 요청](../database/quota-increase-request.md) 을 만들어 요청 시 증가 시킬 수 있습니다.

- **서브넷 제한**: SQL Managed Instance 인스턴스가 단일 지역에 배포 되는 최대 서브넷 수입니다.
- **Vcore 단위 제한**: 단일 지역의 모든 인스턴스에 배포할 수 있는 최대 vcore 단위 수입니다. 하나의 GP vCore는 vCore 단위 하나를 사용 하 고 하나의 BC vCore는 4 개의 vCore를 사용 합니다. 총 인스턴스 수는 vCore 단위 제한 내에 있기만 하면 제한 되지 않습니다.

> [!Note]
> 이러한 제한은 기술 제한이 아닌 기본 설정입니다. 현재 지역에서 더 많은 인스턴스가 필요한 경우 [Azure Portal에서 특수 지원 요청](../database/quota-increase-request.md) 을 만들어 요청 시 제한을 늘릴 수 있습니다. 대신 지원 요청을 보내지 않고 다른 Azure 지역에 SQL Managed Instance의 새 인스턴스를 만들 수 있습니다.

다음 표에서는 지원 되는 구독 유형에 대 한 **기본 지역 제한을** 보여 줍니다. 기본 제한은 아래 설명 된 지원 요청을 사용 하 여 확장할 수 있습니다.

|구독 유형| SQL Managed Instance 서브넷의 최대 수 | 최대 vCore 단위 수 * |
| :---| :--- | :--- |
|종량제|3|320|
|CSP |8 (일부 지역의 경우 15 * *)|960 (일부 지역에서 1440 * *)|
|종량제 개발/테스트|3|320|
|Enterprise 개발/테스트|3|320|
|EA|8 (일부 지역의 경우 15 * *)|960 (일부 지역에서 1440 * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional 및 MSDN 플랫폼|2|32|

\* 배포 계획에서 중요 비즈니스용 (BC) 서비스 계층에는 일반 용도 (GP) 서비스 계층 보다 4 배 더 많은 vCore 용량이 필요 하다는 점을 고려 하세요. 예: 1 GP vCore = 1 vCore 단위 및 1 BC vCore = 4 vCore 단위 기본 제한에 대해 사용량 분석을 간소화 하기 위해는 SQL Managed Instance 배포 된 지역의 모든 서브넷에서 vCore 단위를 요약 하 고 해당 결과를 구독 유형에 대 한 인스턴스 단위 제한과 비교 합니다. **최대 vCore 단위 수** 제한은 한 지역의 각 구독에 적용 됩니다. 여러 서브넷에 배포 된 모든 **Vcores의 합계가 최대 vcores 단위 수**와 같거나 작아야 한다는 점만 제외 하 고 개별 서브넷 당 제한이 없습니다.

\*\* 더 큰 서브넷 및 vCore 제한은 오스트레일리아 동부, 미국 동부, 미국 동부 2, 서유럽, 미국 동부, 동남 아시아, 영국 남부, 유럽 서부, 미국 서 부 2 지역에서 사용할 수 있습니다.

> [!IMPORTANT]
> VCore 및 서브넷 한도가 0 인 경우 구독 유형에 대 한 기본 지역 제한이 설정 되지 않았음을 의미 합니다. 또한 필수 vCore 및 서브넷 값을 제공 하는 동일한 절차에 따라 특정 지역에서 구독 액세스를 가져오기 위해 할당량 증가 요청을 사용할 수 있습니다.

## <a name="request-a-quota-increase"></a>할당량 증가 요청

현재 지역에서 더 많은 인스턴스가 필요한 경우 Azure Portal를 사용 하 여 할당량을 연장 하는 지원 요청을 보냅니다. 자세한 내용은 [Azure SQL Database에 대 한 요청 할당량 늘리기](../database/quota-increase-request.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대 한 자세한 내용은 [sql Managed Instance 란?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- 가격 책정 정보는 [SQL Managed Instance 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조 하세요.
- 첫 번째 SQL Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조 하세요.

---
title: Azure SQL Database 서버 리소스 한도 | Microsoft Docs
description: 이 문서에서는 단일 데이터베이스 및 탄력적 풀에 대한 Azure SQL Database 서버 리소스 한도를 간략하게 설명합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan,moslake,josack
manager: craigg
ms.date: 04/18/2019
ms.openlocfilehash: 04a5b98daf94275c6a95503c518248abeaeaeaa6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482043"
---
# <a name="sql-database-resource-limits-for-azure-sql-database-server"></a>Azure SQL Database 서버의 SQL Database 리소스 한도

이 문서에서는 단일 데이터베이스 및 탄력적 풀을 관리하는 SQL Database 서버의 SQL Database 리소스 한도를 간략하게 설명합니다. 또한 이러한 리소스 한도에 도달하거나 초과할 때 발생하는 사항에 대한 정보도 제공합니다.

> [!NOTE]
> 관리되는 인스턴스 한도는 [관리되는 인스턴스의 SQL Database 리소스 한도](sql-database-managed-instance-resource-limits.md)를 참조하세요.

## <a name="maximum-resource-limits"></a>최대 리소스 한도

| 리소스 | 제한 |
| :--- | :--- |
| 서버당 데이터베이스 | 5,000 |
| 모든 지역에서 구독당 서버의 기본 수 | 20 |
| 모든 지역에서 구독당 서버의 최대 수 | 200 |  
| 서버당 DTU/eDTU 할당량 | 54,000 |  
| 서버/인스턴스당 vCore 할당량 | 540 |
| 서버당 최대 풀 | DTU 또는 vCore의 수로 제한됩니다. 예를 들어 각 풀에 DTU가 1000개인 경우 서버는 54개 풀을 지원할 수 있습니다.|
|||

> [!NOTE]
> 기본 양보다 더 많은 DTU/eDTU 할당량, vCore 할당량 또는 더 많은 서버를 얻으려면 문제 유형 "할당량"을 사용하여 구독을 위한 Azure Portal에서 새 지원 요청을 제출할 수 있습니다. 서버당 DTU/eDTU 할당량 및 데이터베이스 제한은 서버당 탄력적 풀의 수를 제한합니다.
> [!IMPORTANT]
> 데이터베이스 수가 SQL Database 서버당 한도에 근접하면 다음이 발생할 수 있습니다.
> - 마스터 데이터베이스에 대해 쿼리를 실행할 때 대기 시간이 증가합니다.  여기에는 sys.resource_stats와 같은 리소스 사용률 통계 보기가 포함됩니다.
> - 관리 작업을 수행하고 서버의 데이터베이스 열거와 관련된 포털 뷰 포인트를 렌더링할 때 대기 시간이 증가합니다.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>데이터베이스 리소스 한계에 도달하면 어떻게 되나요?

### <a name="compute-dtus-and-edtus--vcores"></a>Compute(DTU 및 eDTU / vCore)

데이터베이스 계산 사용량(DTU 및 eDTU 또는 vCore로 측정)이 높아지면 쿼리 대기 시간이 늘어나고 시간이 초과될 수 있습니다. 이러한 상황에서는 쿼리가 서비스에서 대기될 수 있으며 리소스가 사용 가능해질 경우 실행될 수 있게 리소스가 제공됩니다.
높은 계산 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기를 늘려 데이터베이스에 더 많은 계산 리소스를 제공합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 쿼리를 최적화하여 각 쿼리당 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

### <a name="storage"></a>Storage

사용된 데이터베이스 공간이 최대 크기 제한에 도달하면 데이터 크기 증가를 가져오는 데이터베이스 삽입 및 업데이트가 실패하고 클라이언트에 [오류 메시지](sql-database-develop-error-messages.md)가 표시됩니다. 데이터베이스 선택 및 삭제는 계속 성공적으로 수행됩니다.

높은 공간 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 최대 크기를 늘리거나 더 많은 저장소를 추가합니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 데이터베이스가 탄력적 풀에 속하는 경우 데이터베이스를 풀 외부로 이동하여 저장소 공간이 다른 데이터베이스와 공유되지 않도록 할 수 있습니다.
- 데이터베이스를 축소하여 사용하지 않는 공간을 회수합니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="sessions-and-workers-requests"></a>세션 및 작업자(요청)

세션 및 작업자의 최대 수는 서비스 계층 및 컴퓨팅 크기(DTU 및 eDTU)에 따라 결정됩니다. 세션 또는 작업자 제한에 도달하면 새 요청이 거부되고 클라이언트에 오류 메시지가 표시됩니다. 사용할 수 있는 연결의 수는 애플리케이션에서 쉽게 제어할 수 있지만, 동시 작업자 수는 예측하고 제어하기가 어렵습니다. 좀 더 오래 실행되는 쿼리로 인해 데이터베이스 리소스 제한에 도달하고 작업자가 대기하게 되는 최대 부하 기간이 특히 그렇습니다.

높은 세션 또는 작업자 사용률에 도달할 경우 완화하는 방법에는 다음이 포함됩니다.

- 데이터베이스 또는 탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 늘립니다. [단일 데이터베이스 리소스 확장](sql-database-single-database-scale.md) 및 [탄력적 풀 리소스 확장](sql-database-elastic-pool-scale.md)을 참조하세요.
- 계산 리소스에 대한 경합 때문에 작업자 사용률이 증가할 경우 쿼리를 최적화하여 각 쿼리의 리소스 사용률을 줄입니다. 자세한 내용은 [쿼리 튜닝/힌트](sql-database-performance-guidance.md#query-tuning-and-hinting)를 참조하세요.

## <a name="transaction-log-rate-governance"></a>트랜잭션 로그 속도 거 버 넌 스 
트랜잭션 로그 속도 거 버 넌 스 프로세스 대량 같은 워크 로드에 대해 높은 수집 속도 제한 하는 데 Azure SQL Database에서 insert, SELECT INTO 이며 인덱스 작성 합니다. 이러한 한도 추적 하 여 로그 레코드 생성 속도가 1 초 미만의 수준에서 적용, 얼마나 많은 IOs에 관계 없이 제한 처리량 데이터 파일에 대해 발급 될 수 있습니다.  트랜잭션 로그 생성 속도 현재 선형적으로 하드웨어 종속 하는 지점까지, 속도 최대 로그를 사용 하 여 구매 모델 vCore 사용 하 여 96 MB/s 되을 허용 합니다. 

> [!NOTE]
> 트랜잭션 로그 파일에 실제 물리적 Io 제어 되지 않거나 제한 합니다. 

로그 속도 달성 및 전체 시스템 사용자 부하에 영향을 최소화를 사용 하 여 해당 기능을 유지할 수 있지만 다양 한 시나리오에서에서 계속 해 서 수 있도록 설정 됩니다. 로그 속도 거 버 넌 스에는 해당 트랜잭션 로그 백업을 게시 된 복구 기능 Sla 내에서 유지 되도록 합니다.  이 거 버 넌이 스는 또한 보조 복제본에는 과도 한 백로그를 방지합니다.

로그 레코드가 생성 되 면 각 작업 평가 되 고 최대 원하는 로그 속도 (MB/s / 초)을 유지 하기 위해 지연 돼야 하는지 여부를 평가 합니다. 지연 추가 되지 않습니다 로그 레코드 플러시 되도록 저장소를 대신 하는 경우 로그 속도 거 버 넌 스는 자체 로그 속도 생성 하는 동안 적용 됩니다.

실제 로그 생성 런타임에 적용 되는 요금 있습니다도 영향을 받을 피드백 메커니즘을 일시적으로 시스템 안정화할 수 있도록 허용 되는 로그 요금을 감소. 로그 파일 공간 관리, 복제 메커니즘 로그 공간 상태 및 가용성 그룹에 실행을 방지는 전체 시스템 한도 줄일 일시적으로 수 있습니다. 

다음 대기 유형을 통해 표시 되는 로그 속도 관리자 트래픽 셰이핑 (노출 된 [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| 대기 유형 | 메모 |
| :--- | :--- |
| LOG_RATE_GOVERNOR | 데이터베이스 제한 |
| POOL_LOG_RATE_GOVERNOR | 풀 제한 |
| INSTANCE_LOG_RATE_GOVERNOR | 인스턴스 수준 제한 |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | 가용성 그룹 물리적 복제 프리미엄/중요 비즈니스 계층에서 따라가고 있지 못함을 피드백 컨트롤 |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | 피드백 컨트롤, 로그 공간 부족을 방지 하는 속도 제한 |
|||

원하는 확장성에 방해가 되는 로그 속도 제한에 도달할 경우 다음 옵션을 고려 합니다.
- 최대 96 MB/s 로그 속도 얻기 위해 더 큰 계층으로 강화 합니다. 
- 데이터가 로드 일시적인 경우 ETL 프로세스에서 데이터를 준비 하는 예: 로드할 수 있습니다 (작업이 최소한으로 로깅되)는 tempdb에 있습니다. 
- 분석 시나리오를 설명 하는 클러스터형된 columnstore 테이블에 로드 합니다. 이 압축으로 인해 필수 로그 속도를 낮춥니다. 이 기술은 CPU 사용률이 증가 하 고 클러스터형된 columnstore 인덱스를 활용 하는 데이터 집합에만 적용 됩니다. 

## <a name="next-steps"></a>다음 단계

- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
- DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)를 참조하세요.
- tempdb 크기 한도에 대한 자세한 내용은 [Azure SQL Database의 TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)를 참조하세요.

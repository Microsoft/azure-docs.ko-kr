---
title: Azure SQL Database 메모리 내 기술 | Microsoft Docs
description: Azure SQL Database 메모리 내 기술은 트랜잭션 및 분석 작업의 성능을 크게 향상시킵니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/19/2019
ms.openlocfilehash: d2c852b48c219283bba2304a993dd26e802b3252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036509"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>SQL Database에서 메모리 내 기술을 사용하여 성능 최적화

Azure SQL Database에서 메모리 내 기술을 사용하면 애플리케이션의 성능을 향상시키고, 데이터베이스의 비용을 크게 줄일 수 있습니다. 

## <a name="when-to-use-in-memory-technologies"></a>메모리 내 기술을 사용 하는 경우

Azure SQL Database에서 메모리 내 기술을 사용하여 다양한 워크로드를 통해 성능 향상을 얻을 수 있습니다.

- **트랜잭션**(OLTP(온라인 트랜잭션 처리)) 여기서 대부분의 요청은 작은 데이터 세트를 읽거나 업데이트합니다(예: CRUD 작업).
- **분석**(OLAP(온라인 분석 처리)) 여기서 대부분의 쿼리는 기존 테이블에 데이터를 로드 및 추가하거나(따라서 대량 로드라고도 함) 테이블에서 데이터를 삭제하는 특정 수의 쿼리를 사용하여 보고 목적을 위해 복잡한 계산을 갖습니다. 
- **혼합**(HTAP(하이브리드 트랜잭션/분석 처리)) 여기서 두 OLTP 및 OLAP 쿼리는 동일한 데이터 세트에서 실행됩니다.

메모리 내 기술은 메모리로 처리되어야 하는 데이터를 유지하고, 쿼리의 네이티브 컴파일 또는 기본 하드웨어에서 사용할 수 있는 일괄 처리 및 SIMD 지침과 같은 고급 처리를 사용하여 이러한 워크로드의 성능을 향상시킬 수 있습니다. 

## <a name="overview"></a>개요

Azure SQL Database에는 다음과 같은 메모리 내 기술이 있습니다.
- *[메모리 내 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* 는 초당 트랜잭션의 수를 증가시키고 트랜잭션 처리에 대한 대기 시간을 감소시킵니다. 메모리 내 OLTP를 활용하는 시나리오는 거래와 게임, 이벤트 또는 IoT 디바이스의 데이터 수집, 캐싱, 데이터 부하 및 임시 테이블과 테이블 변수 시나리오와 같은 처리량 많은 트랜잭션을 처리하는 경우입니다.
- *클러스터형 Columnstore 인덱스*는 저장소 공간을 최대 10배로 감소시키고 보고 및 분석 쿼리에 대한 성능을 향상시킵니다. 데이터베이스에 있는 더 많은 데이터에 적합한 데이터 마트에서 팩트 테이블과 함께 이 기능을 사용하여 성능을 향상시킬 수 있습니다. 운영 데이터베이스에 있는 기록 데이터와 함께 이 기능을 사용하여 최대 10배 더 많은 데이터를 보관하고 쿼리할 수도 있습니다.
- HTAP에 대한 *비클러스터형 Columnstore 인덱스*를 통해 비용이 많이 드는 ETL(추출, 변형 및 로드) 프로세스를 실행하거나 데이터 웨어하우스를 채울 때까지 기다릴 필요 없이 운영 데이터베이스를 직접 쿼리하여 비즈니스에 대한 실시간 정보를 얻을 수 있습니다. 비클러스터형 Columnstore 인덱스를 사용하면 운영 워크로드에 미치는 영향을 줄이는 동시에 OLTP 데이터베이스에 대한 분석 쿼리를 빠르게 실행할 수 있습니다.
- HTAP에 대한 *메모리 최적화 클러스터형 columnstore 인덱스*를 사용하면 트랜잭션 처리를 빠르게 수행하고 동일한 데이터에서 분석 쿼리를 매우 신속하게 *동시* 실행할 수 있습니다.

columnstore 인덱스 및 메모리 내 OLTP는 각각 SQL Server 제품 2012 및 2014 이상에 포함되었습니다. Azure SQL Database 및 SQL Server는 메모리 내 기술의 동일한 구현을 공유합니다. 앞으로 이러한 기술에 대한 새로운 기능을 SQL Server에 배포하기 전에 Azure SQL Database에서 먼저 릴리스합니다.

## <a name="benefits-of-in-memory-technology"></a>메모리 내 기술의 혜택

메모리 내 기술을 사용하면 보다 효율적인 쿼리 및 트랜잭션 처리로 인해 비용을 줄일 수 있습니다. 일반적으로는 성능 향상을 위해 데이터베이스의 가격 책정 계층을 업그레이드할 필요가 없습니다. 일부 경우에 가격 책정 계층을 줄이는 동시에 메모리 내 기술의 성능이 향상된 것을 확인할 수 있습니다.

메모리 내 OLTP가 성능을 크게 향상시키는 방법의 두 가지 예는 다음과 같습니다.

- 메모리 내 OLTP를 사용하여 [Quorum Business Solutions은 DTU를 70%까지 개선하면서 작업량을 두 배로 늘릴 수 있었습니다](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - 여기서 DTU는 *데이터베이스 트랜잭션 단위*를 의미하며 리소스 소비량의 측정값을 포함합니다.
- 다음 비디오는 샘플 워크로드에서 리소스 소비가 크게 향상되었음을 보여줍니다. [Azure SQL Database의 메모리 내 OLTP 비디오](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - 자세한 내용은 블로그 게시물을 참조하세요. [Azure SQL Database의 메모리 내 OLTP 블로그 게시물](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> 메모리 내 기술은 프리미엄 및 중요 비즈니스용 계층 Azure SQL 데이터베이스 및 Premium 탄력적 풀에서 사용할 수 있습니다.

다음 비디오에서는 Azure SQL Database에서 메모리 내 기술을 포함한 잠재적인 성능 향상에 대해 설명합니다. 향상된 성능은 워크로드 및 데이터의 특성, 데이터베이스 등의 액세스 패턴을 포함한 많은 요인에 따라 달라 집니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

이 문서에서는 Azure SQL Database와 관련된 메모리 내 OLTP 및 columnstore 인덱스의 측면을 설명하며 다음과 같은 샘플도 제공합니다.

- 여기서는 데이터 크기 제한과 저장소에 대한 이와 같은 기술의 영향에 대해 알아봅니다.
- 그리고 이러한 기술을 사용하는 데이터베이스를 여러 가격 책정 계층 간에 이동하는 작업을 관리하는 방법도 살펴봅니다.
- 또한 Azure SQL Database의 columnstore 인덱스뿐만 아니라 메모리 내 OLTP의 사용을 보여 주는 두 개의 샘플입니다.

자세한 내용은 다음을 참조하세요.

- [메모리 내 OLTP 개요 및 사용 시나리오](https://msdn.microsoft.com/library/mt774593.aspx)(고객 사례 연구 및 시작 정보에 대한 참조 포함)
- [메모리 내 OLTP에 대한 설명서](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore 인덱스 가이드](https://msdn.microsoft.com/library/gg492088.aspx)
- HTAP(하이브리드 트랜잭션/분석 처리) 즉, [실시간 운영 분석](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>메모리 내 OLTP

메모리 내 OLTP 기술은 메모리에 모든 데이터를 유지하여 매우 빠른 데이터 액세스 작업을 제공합니다. 또한 특수한 인덱스, 쿼리의 네이티브 컴파일 및 래치 없는 데이터 액세스를 사용하여 OLTP 워크로드의 성능을 향상시킵니다. 메모리 내 OLTP 데이터를 구성하는 두 가지 방법이 있습니다.

- **메모리 최적화 rowstore** 형식 여기서 모든 행은 별도 메모리 개체입니다. 이는 고성능 OLTP 워크로드에 대해 최적화된 클래식 메모리 내 OLTP 형식입니다. 메모리 최적화 rowstore 형식에 사용할 수 있는 메모리 최적화 테이블에는 다음과 같은 두 가지 유형이 있습니다.
  - *영구 테이블*(SCHEMA_AND_DATA) 여기서 메모리에 위치한 행은 서버가 다시 시작된 후 유지됩니다. 이 유형의 테이블은 메모리 내 최적화의 추가 혜택을 사용하여 기존의 rowstore 테이블처럼 동작합니다.
  - *비영구 테이블* (SCHEMA_ONLY) 행 있는 not 유지 다시 시작한 후입니다. 이 유형의 테이블은 임시 데이터(예: 대체 임시 테이블) 또는 일부 지속형 테이블로 이동하기 전에 신속하게 데이터를 로드해야 하는 테이블(따라서 준비 테이블이라고 함)을 위해 설계되었습니다.
- **메모리 최적화 columnstore** 형식 여기서 데이터는 열 형식으로 구성됩니다. 이 구조는 OLTP 워크로드가 실행되고 있는 동일한 데이터 구조에서 분석 쿼리를 실행해야 하는 HTAP 시나리오를 위해 설계되었습니다.

> [!Note]
> 메모리 내 OLTP 기술은 완전히 메모리에 상주할 수 있는 데이터 구조를 위해 설계되었습니다. 메모리 내 데이터는 디스크에 오프로드될 수 없으므로 충분한 메모리가 있는 데이터베이스를 사용하고 있는지 확인합니다. 자세한 내용은 [메모리 내 OLTP의 데이터 크기 및 스토리지 제한](#data-size-and-storage-cap-for-in-memory-oltp)을 참조하세요.

메모리 내 OLTP에 대한 빠른 입문서: [빠른 시작 1: 더 빠른 T-SQL 성능에 대한 메모리 내 OLTP 기술](https://msdn.microsoft.com/library/mt694156.aspx)(시작하는 데 유용한 다른 문서)

기술에 대한 자세한 비디오:

- [Azure SQL Database의 메모리 내 OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)(성능 이점에 대한 데모 및 이 결과를 사용자 스스로 재현하는 단계 포함)
- [메모리 내 OLTP 비디오: 기능 정의 및 사용 시기/방법](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

지정된 데이터베이스가 메모리 내 OLTP를 지원하는지 여부를 프로그래밍 방식으로 이해할 수 있습니다. 다음 Transact-SQL 쿼리를 실행할 수 있습니다.
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
쿼리가 **1**을 반환하는 경우 메모리 내 OLTP는 이 데이터베이스에서 지원됩니다. 다음 쿼리는 데이터베이스를 표준/기본으로 다운그레이드하기 전에 제거해야 하는 모든 개체를 식별합니다.
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>메모리 내 OLTP의 데이터 크기 및 저장 제한

메모리 내 OLTP는 사용자 데이터를 저장하는 데 사용되는 메모리 최적화 테이블을 포함합니다. 이러한 테이블은 메모리에 적합해야 합니다. SQL Database 서비스에 직접 메모리를 관리하기 때문에 사용자 데이터에 대한 할당량의 개념이 있습니다. 이 개념은 *메모리 내 OLTP 저장소*라고 합니다.

지원되는 단일 데이터베이스 가격 책정 계층 및 탄력적 풀 가격 책정 계층에는 각각 일정량의 메모리 내 OLTP 저장소가 포함됩니다. [DTU 기반 리소스 제한 - 단일 데이터베이스](sql-database-dtu-resource-limits-single-databases.md), [DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-elastic-pools.md), [vCore 기반 리소스 제한 - 단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [vCore 기반 리소스 제한 - 탄력적 풀](sql-database-vcore-resource-limits-elastic-pools.md)을 참조하세요.

메모리 내 OLTP 저장소 제한 계산 시 포함되는 항목은 다음과 같습니다.

- 메모리 최적화 테이블 및 테이블 변수의 활성 사용자 데이터 행입니다. 예전 행 버전은 제한에 고려되지 않습니다.
- 메모리 최적화 테이블에 대한 인덱스입니다.
- ALTER TABLE 작업의 작업 오버헤드입니다.

제한에 도달한 경우 할당량 초과 오류가 표시되고 더 이상 데이터를 삽입하거나 업데이트할 수 없습니다. 이 오류를 완화하려면 데이터를 삭제하거나 데이터베이스 또는 풀의 가격 책정 계층을 증가시킵니다.

제한에 도달한 경우 메모리 내 OLTP 저장소 사용률을 모니터링하고 경고를 구성하는 방법에 대한 자세한 내용은 [메모리 내 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md)을 참조하세요.

#### <a name="about-elastic-pools"></a>탄력적 풀에 대한 정보

탄력적 풀을 사용하여 메모리 내 OLTP 저장소는 풀에 있는 모든 데이터베이스에서 공유됩니다. 따라서 한 데이터베이스의 사용량은 다른 데이터베이스에 영향을 줄 수 있습니다. 두 가지 해결 방법이 있습니다.

- 데이터베이스의 `Max-eDTU` 또는 `MaxvCore`를 전체 풀의 eDTU 또는 vCore 수보다 낮게 구성합니다. 이 최대값은 풀에 있는 모든 데이터베이스에서 메모리 내 OLTP 저장소 사용률을 eDTU 수에 해당하는 크기로 제한합니다.
- `Min-eDTU` 또는 `MinvCore`를 0보다 크게 구성합니다. 이 최소값은 풀에 있는 각 데이터베이스가 구성된 `Min-eDTU` 또는 `vCore`에 해당하는 사용 가능한 메모리 내 OLTP 저장소의 양을 사용할 수 있도록 보장합니다.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>메모리 내 OLTP 기술을 사용하는 데이터베이스의 서비스 계층 변경

데이터베이스 또는 인스턴스를 더 높은 계층으로 항상 업그레이드할 수 있습니다(예: 범용에서 중요 비즈니스용으로 또는 표준에서 프리미엄으로). 사용할 수 있는 기능 및 리소스만 증가합니다.

하지만 계층을 다운그레이드하면 데이터베이스 성능이 저하될 수 있습니다. 데이터베이스에 메모리 내 OLTP 개체가 포함되어 있을 때 중요 비즈니스용에서 범용으로(또는 프리미엄에서 표준이나 기본으로) 다운그레이드하면 성능 저하 현상이 특히 뚜렷하게 나타납니다. 메모리 최적화 테이블은 계속 표시되더라도 다운그레이드 후에 사용할 수 없는 상태가 됩니다. 탄력적 풀의 가격 책정 계층을 낮추거나 메모리 내 기술을 포함한 데이터베이스를 표준 또는 기본 탄력적 풀로 이동하는 경우에도 동일한 고려 사항이 적용됩니다.

> [!Important]
> 메모리 내 OLTP는 범용, 표준 또는 기본 계층에서 지원되지 않습니다. 따라서 메모리 내 OLTP 개체가 있는 데이터베이스를 표준 또는 기본 계층에 이동할 수 없습니다.

데이터베이스를 표준/기본으로 다운그레이드하기 전에 모든 메모리 최적화 테이블 및 테이블 형식뿐만 아니라 고유하게 컴파일된 모든 T-SQL 모듈을 제거합니다. 

*중요 비즈니스용 계층의 리소스 축소*: 메모리 최적화 테이블의 데이터는 데이터베이스의 계층 또는 Managed Instance와 연결되거나 탄력적 풀에서 사용할 수 있는 메모리 내 OLTP 스토리지 내에 담겨야 합니다. 계층을 축소하려고 하거나 충분히 사용 가능한 메모리 내 OLTP 스토리지가 없는 풀로 데이터베이스를 이동하려는 경우 작업은 실패합니다.

## <a name="in-memory-columnstore"></a>메모리 내 columnstore

메모리 내 columnstore 기술을 통해 테이블에 더 많은 양의 데이터를 저장하고 쿼리할 수 있습니다. columnstore 기술은 열 기반 데이터 스토리지 형식 및 일괄 처리 쿼리 처리를 사용하여 기존의 행 기반 스토리지보다 OLAP 워크로드에서 최대 10배의 쿼리 성능을 얻을 수 있습니다. 또한 압축되지 않은 데이터 크기보다 최대 10배의 데이터 압축 향상을 얻을 수 있습니다.
데이터를 구성하는 데 사용할 수 있는 columnstore 모델에는 다음과 같은 두 종류가 있습니다.

- **클러스터형 columnstore** 여기서 테이블의 모든 데이터는 열 형식으로 구성됩니다. 이 모델에서 테이블의 모든 행은 항상 데이터를 압축하고 테이블에서 신속한 분석 쿼리 및 보고서를 실행할 수 있도록 하는 열 형식으로 배치됩니다. 데이터의 특성에 따라 데이터의 크기는 10x-100x로 감소될 수 있습니다. 100K 행보다 큰 데이터의 대량 일괄 처리는 디스크에 저장되기 전에 압축되므로 클러스터형 columnstore 모델을 통해 많은 양의 데이터(대량 로드)를 빠르게 수집할 수도 있습니다. 이 모델은 기존 데이터 웨어하우스 시나리오에 적합합니다. 
- **비클러스터형 columnstore** 여기서 데이터는 기존 rowstore 테이블에 저장되고 분석 쿼리에 사용되는 columnstore 형식의 인덱스가 있습니다. 이 모델은 HTAP(하이브리드 트랜잭션 분석 처리): 트랜잭션 워크로드에서 고성능 실시간 분석을 실행하는 기능을 활성화합니다. OLTP 쿼리는 작은 행의 세트에 액세스하는 데 최적화된 rowstore 테이블에서 실행되는 반면 OLAP 쿼리는 스캔 및 분석에 유용한 columnstore 인덱스에서 실행됩니다. Azure SQL Database 쿼리 최적화는 쿼리에 따라 rowstore 또는 columnstore 형식을 동적으로 선택합니다. 원래 데이터 세트는 변경하지 않고도 원래 rowstore 테이블에 보관되므로 비클러스터형 columnstore 인덱스는 데이터의 크기를 줄이지 않습니다. 그러나 추가 columnstore 인덱스의 크기는 해당 B-트리 인덱스보다 작은 크기의 순서에 있어야 합니다.

> [!Note]
> 메모리 내 columnstore 기술은 메모리에서 처리를 위해 필요한 데이터만 유지하지만 메모리에 맞출 수 없는 데이터는 디스크에 저장됩니다. 따라서 메모리 내 columnstore 구조에서 데이터의 양은 사용할 수 있는 메모리 양을 초과할 수 있습니다. 

기술에 대한 자세한 비디오:

- [Columnstore 인덱스: Ignite 2016의 메모리 내 분석 비디오](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>columnstore 인덱스의 데이터 크기 및 저장소

columnstore 인덱스는 메모리에 적합할 필요가 없습니다. 따라서 인덱스의 크기에 대한 유일한 제한은 전체 최대 데이터베이스 크기이며 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md) 문서에서 설명합니다.

클러스터형 columnstore 인덱스를 사용하는 경우 기본 Table Storage에 칼럼 형식 압축을 사용합니다. 이러한 압축을 사용하면 사용자 데이터의 저장소 공간을 크게 줄일 수 있습니다. 즉, 데이터베이스에 더 많은 데이터를 담을 수 있습니다. 또한 [칼럼 형식 보관 압축](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression)을 사용하여 압축량을 더욱 늘릴 수 있습니다. 수행할 수 있는 압축량은 데이터의 특성에 따라 달라지지만 10배 압축은 일반적이지 않습니다.

예를 들어, 최대 크기 1TB(테라바이트)인 데이터베이스가 있고 columnstore 인덱스를 사용하여 10배 압축을 달성한 경우 데이터베이스에 총 10TB의 사용자 데이터를 담을 수 있습니다.

비클러스터형 columnstore 인덱스를 사용하는 경우 기본 테이블은 여전히 기존의 rowstore 형식으로 저장됩니다. 따라서 절약된 저장소는 클러스터형 columnstore 인덱스만큼 크지 않습니다. 그러나 많은 기존의 비클러스터형 인덱스를 단일 columnstore 인덱스로 바꾸는 경우 테이블에 대한 저장소 공간에서 절약된 전체 공간을 볼 수 있습니다.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Columnstore 인덱스를 포함하는 데이터베이스의 서비스 계층 변경

‘단일 데이터베이스를 기본 또는 표준으로 다운그레이드’하는 것은 대상 계층이 S3 미만인 경우 불가능할 수 있습니다. columnstore 인덱스는 중요 비즈니스용/프리미엄 가격 책정 계층 및 표준 계층, S3 이상에서만 지원되며 기본 계층에서는 지원되지 않습니다. 데이터베이스를 지원되지 않는 계층 또는 수준으로 다운그레이드하면 columnstore 인덱스를 사용할 수 없게 됩니다. 시스템에서 columnstore 인덱스가 유지는 되지만 사용되지는 않습니다. 나중에 다시 지원되는 계층 또는 수준으로 업그레이드하는 경우 columnstore 인덱스는 즉시 다시 사용 가능한 상태가 됩니다.

**클러스터형** columnstore 인덱스가 있는 경우에는 다운그레이드 후에 전체 테이블을 사용할 수 없게 됩니다. 그러므로 지원되지 않는 계층 또는 수준으로 데이터베이스를 다운그레이드하기 전에 *클러스터형* columnstore 인덱스를 모두 삭제하는 것이 좋습니다.

> [!Note]
> Managed Instance는 모든 계층에서 ColumnStore 인덱스를 지원합니다.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>다음 단계

- [빠른 시작 1: 더 빠른 T-SQL 성능을 위한 메모리 내 OLTP 기술](https://msdn.microsoft.com/library/mt694156.aspx)
- [기존 Azure SQL 애플리케이션에서 메모리 내 OLTP 사용](sql-database-in-memory-oltp-migration.md)
- 메모리 내 OLTP에 대한 [메모리 내 OLTP 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md).
- [Azure SQL Database에서 메모리 내 기능 사용해보기](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>추가 리소스

### <a name="deeper-information"></a>자세한 정보

- [쿼럼이 SQL Database의 메모리 내 OLTP을 사용하여 DTU를 70% 줄이는 동시에 키 데이터베이스의 워크로드를 두 배로 증가시키는 방법에 대해 알아보기](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Azure SQL Database의 메모리 내 OLTP 블로그 게시물](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [메모리 내 OLTP에 대해 알아보기](https://msdn.microsoft.com/library/dn133186.aspx)
- [columnstore 인덱스에 대해 알아보기](https://msdn.microsoft.com/library/gg492088.aspx)
- [실시간 운영 성과 분석에 대해 알아보기](https://msdn.microsoft.com/library/dn817827.aspx)
- [일반적인 워크로드 패턴 및 마이그레이션 고려 사항에 대한 백서](https://msdn.microsoft.com/library/dn673538.aspx)를 참조하세요. 여기에서 메모리 내 OLTP가 일반적으로 상당한 성능 향상을 제공하는 워크로드 패턴을 설명합니다.

### <a name="application-design"></a>애플리케이션 설계

- [메모리 내 OLTP(메모리 내 최적화)](https://msdn.microsoft.com/library/dn133186.aspx)
- [기존 Azure SQL 애플리케이션에서 메모리 내 OLTP 사용](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>도구

- [Azure Portal](https://portal.azure.com/)
- [SSMS(SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SSDT(SQL Server Data Tools)](https://msdn.microsoft.com/library/mt204009.aspx)

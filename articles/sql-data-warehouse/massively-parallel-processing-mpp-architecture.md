---
title: Azure SQL Data Warehouse - MPP 아키텍처 | Microsoft Docs
description: Azure SQL Data Warehouse가 고성능 및 확장성을 달성하도록 MPP(Massively Parallel Processing)와 Azure 저장소를 결합하는 방법을 알아봅니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0c2ad7e5a707c20db2773324e8047eedaad1a48b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077121"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse - MPP(Massively Parallel Processing) 아키텍처
Azure SQL Data Warehouse가 고성능 및 확장성을 달성하도록 MPP(Massively Parallel Processing)와 Azure 저장소를 결합하는 방법을 알아봅니다. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>MPP 아키텍처 구성 요소
SQL Data Warehouse는 규모 확장 아키텍처를 활용하여 여러 노드에 걸쳐 데이터의 계산 처리를 분산합니다. 규모 단위는 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)로 알려진 계산 능력의 추상화입니다. SQL Data Warehouse는 시스템의 데이터와 독립적으로 계산을 확장할 수 있도록 스토리지에서 계산을 분리합니다.

![SQL Data Warehouse 아키텍처](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse는 노드 기반 아키텍처를 사용합니다. 애플리케이션은 데이터 웨어하우스의 단일 입력 지점인 제어 노드에 연결하고 T-SQL 명령을 보냅니다. 제어 노드는 병렬 처리를 위해 쿼리를 최적화하는 MPP 엔진을 실행한 다음, 연산을 계산 노드에 전달하여 병렬로 처리되도록 합니다. 계산 노드는 모든 사용자 데이터를 Azure Storage에 저장하고 병렬 쿼리를 실행합니다. DMS(Data Movement Service)는 쿼리를 병렬로 실행하고 정확한 결과를 반환하기 위해 필요할 때 노드에서 데이터를 이동시키는 시스템 수준의 내부 서비스입니다. 

분리된 스토리지 및 계산을 통해 SQL Data Warehouse는 다음을 수행할 수 있습니다.

* 스토리지 요구 사항에 관계없이 계산 능력을 독립적으로 조정합니다.
* 데이터를 이동하지 않고 계산을 확장 또는 축소합니다.
* 데이터를 그대로 둔 채 계산 용량을 일시 중지하여 스토리지 비용만 지불합니다.
* 운영 시간 동안 계산 용량을 다시 시작합니다.

### <a name="azure-storage"></a>Azure 저장소
SQL Data Warehouse는 Azure 저장소를 사용하여 사용자 데이터를 안전하게 유지합니다.  Azure 저장소에서 데이터가 저장되고 관리되기 때문에 SQL Data Warehouse는 저장소 사용량에 대해 별도의 요금을 부과합니다. 데이터 자체는 시스템의 성능을 최적화하기 위해 **분산**으로 분할됩니다. 테이블을 정의할 때 데이터 분산에 사용할 분할 패턴을 선택할 수 있습니다. SQL Data Warehouse는 다음과 같은 분할 패턴을 지원합니다.

* 해시
* 라운드 로빈
* 복제

### <a name="control-node"></a>제어 노드

제어 노드는 데이터 웨어하우스의 두뇌입니다. 모든 애플리케이션 및 연결과 상호 작용하는 프런트 엔드입니다. MPP 엔진은 병렬 쿼리를 최적화하고 조정하기 위해 제어 노드에서 실행됩니다. SQL Data Warehouse에 T-SQL 쿼리를 제출하면 제어 노드는 이것을 각 분산에 대해 병렬로 실행되는 쿼리로 변환합니다.

### <a name="compute-nodes"></a>계산 노드

계산 노드는 계산 능력을 제공합니다. 분산은 처리를 위해 계산 노드로 매핑됩니다. 비용을 지불하는 계산 리소스가 많을수록 SQL Data Warehouse는 사용 가능한 계산 노드에 분산을 다시 매핑합니다. 계산 노드의 수는 1~60 사이이며 데이터 웨어하우스의 서비스 수준에 따라 결정됩니다.

각 계산 노드에는 시스템 뷰에 표시되는 노드 ID가 있습니다. 시스템 뷰에서 이름이 sys.pdw_nodes로 시작하는 node_id 열을 검색하여 Compute 노드 ID를 볼 수 있습니다. 시스템 뷰 목록은 [MPP 시스템 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)를 참조하세요.

### <a name="data-movement-service"></a>데이터 이동 서비스
DMS(데이터 이동 서비스)는 계산 노드 간의 데이터 이동을 조정하는 데이터 전송 기술입니다. 일부 쿼리는 병렬 쿼리가 정확한 결과를 반환하도록 하려면 데이터 이동이 필요합니다. 데이터 이동이 필요할 때 DMS는 올바른 데이터가 올바른 위치에 도달하도록 보장합니다. 

## <a name="distributions"></a>배포

분산은 분산 데이터에 실행되는 병렬 쿼리의 저장 및 처리의 기본 단위입니다. SQL Data Warehouse가 쿼리를 실행하면 병렬로 실행되는 60개의 작은 쿼리로 작업이 나뉩니다. 60개의 작은 쿼리는 각각 데이터 분산 중 하나에서 실행됩니다. 각 계산 노드는 60개 중 하나 이상의 분산을 관리합니다. 계산 리소스가 최대인 데이터 웨어하우스는 계산 노드당 하나의 분산이 있습니다. 계산 리소스가 최소인 데이터 웨어하우스는 하나의 계산 노드에 모든 분산이 포함됩니다.  

## <a name="hash-distributed-tables"></a>해시 분산 테이블
해시 분산 테이블은 대형 테이블의 조인 및 집계에 대해 가장 높은 쿼리 성능을 제공할 수 있습니다. 

데이터를 해시 분산 테이블로 분할하기 위해 SQL Data Warehouse는 해시 함수를 사용하여 각 행을 결정적으로 하나의 분산에 할당합니다. 테이블 정의에서 열 중 하나는 분산 열로 지정됩니다. 해시 함수는 분산 열의 값을 사용하여 각 행을 분산에 할당합니다.

다음은 전체(분산되지 않은 테이블)가 해시 분산 테이블로 저장되는 방식을 보여 주는 다이어그램입니다. 

![분산 테이블](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "분산 테이블")  

* 각 행은 하나의 분산에 속합니다.  
* 결정적 해시 알고리즘은 각 행을 하나의 분산에 할당합니다.  
* 분산당 테이블 행의 수는 테이블 크기에 따라 달라집니다.

분산 열을 선택할 때 고유성, 데이터 기울이기, 시스템에서 실행되는 쿼리 종류 등 성능에 대해 고려할 사항이 있습니다.

## <a name="round-robin-distributed-tables"></a>라운드 로빈 분산 테이블
라운드 로빈 테이블은 만들기 가장 간단한 테이블이며 로드를 위한 준비 테이블로 사용될 때 빠른 성능을 제공합니다.

라운드 로빈 분산 테이블은 데이터를 테이블 전체에 고르게 분산하지만 최적화는 하지 않습니다. 먼저 분산이 무작위로 선택되고 행 버퍼가 순차적으로 분산에 할당됩니다. 데이터는 로드는 라운드 로빈 테이블이 빠르지만 쿼리 성능은 해시 분산 테이블이 더 뛰어난 경우가 많습니다. 라운드 로빈 테이블의 조인에는 데이터 다시 섞기가 필요하며 여기에는 추가 시간이 소요됩니다.


## <a name="replicated-tables"></a>복제된 테이블
복제된 테이블은 작은 테이블에 가장 빠른 쿼리 성능을 제공합니다.

복제된 테이블은 각 계산 노드에 테이블의 전체 복사본을 캐시합니다. 결과적으로 테이블을 복제하면 조인 또는 집계 전에 계산 노드 간에 데이터를 전송하지 않아도 됩니다. 복제된 테이블은 작은 테이블에서 가장 잘 활용됩니다. 추가 저장소가 필요 하며 대형 테이블을 불가능 하 게 하는 데이터를 쓸 때 발생 하는 추가 오버 헤드가 있습니다.  

다음 다이어그램은 복제된 테이블을 보여줍니다. SQL Data Warehouse의 경우 복제된 테이블은 각 계산 노드의 첫 번째 분산에 캐시됩니다.  

![복제 테이블](media/sql-data-warehouse-distributed-data/replicated-table.png "복제 테이블") 

## <a name="next-steps"></a>다음 단계
SQL 데이터 웨어하우스에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse] 및 [샘플 데이터 로드][load sample data]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [지원 티켓 만들기]
* [MSDN 포럼]
* [Stack Overflow 포럼]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[지원 티켓 만들기]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 포럼]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow 포럼]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/

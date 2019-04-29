---
title: Azure SQL Data Warehouse 릴리스 정보 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/10/2019
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 10d703ad613a340a0f99c4d5dd8f4c748ff3e6bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475102"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL Data Warehouse 릴리스 정보

이 문서에는 [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md)의 최신 릴리스에 포함된 새로운 기능과 향상된 기능이 요약되어 있습니다. 이 문서는 또한 직접 릴리스 관련 아니지만 동일한 시간 프레임에 게시 하는 중요 한 콘텐츠 업데이트를 나열 합니다. 향상 된 다른 Azure 서비스를 참조 하세요 [서비스 업데이트](https://azure.microsoft.com/updates)합니다.

## <a name="march-2019"></a>2019 년 3 월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Gen2 미리 보기에 사용할 수 있는 워크 로드 중요도**|워크 로드 중요도 데이터 엔지니어 중요도 사용 하 여 요청을 분류 하는 기능을 제공 합니다. 높은 중요도 사용 하 여 요청에는 Sla를 충족 하는 데 도움이 되는 리소스에 빠르게 액세스할을 보장 됩니다.  워크 로드 중요도 더 적은 리소스를 사용 하 여 공유 환경에서 Sla를 충족 하기 위해 높은 비즈니스 값 작업을 허용 합니다.<br/><br/>워크 로드 관리 분류 및 중요도 미리 보기는 2019 년 4 월 9 일 이상 릴리스 날짜를 사용 하 여 빌드입니다. 사용자는 워크로드 관리 테스트를 위해 이 날짜 이전에는 빌드를 사용하지 않는 것이 좋습니다. 실행 빌드 수 있는 워크 로드 관리 인지를 확인 하려면 `select @@version` SQL Data Warehouse 인스턴스에 연결 합니다.</br></br>워크 로드 중요도에 대 한 자세한 내용은 참조 하세요.는 [분류](sql-data-warehouse-workload-classification.md) 하 고 [중요도](sql-data-warehouse-workload-importance.md) 문서의 개요 문서입니다. 체크 아웃 합니다 [워크 로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) 문서도 합니다.<br/><br/>실행 중인 워크 로드 중요도 참조는 아래 비디오:<br/>[워크 로드 관리 개념](  https://www.youtube.com/embed/QcCRBAhoXpM)<br/>[워크 로드 관리 시나리오](https://www.youtube.com/embed/_2rLMljOjw8)|
|**데이터 검색 및 분류**|이제 Azure SQL Data Warehouse에서 데이터 검색 및 분류를 공개 미리 보기로 사용할 수 있습니다. 중요 한 데이터 및 고객의 개인 정보를 보호 하는 것이 반드시 합니다. 비즈니스 및 고객 데이터 자산이 증가 함에 따라 검색, 분류 및 데이터를 보호 하는 것이 어려워집니다. Azure SQL Data Warehouse를 사용 하 여 고유 하 게 도입 하는 데이터 검색 및 분류 기능 보다 관리가 용이한 데이터 보호를 확인 하는 데 도움이 됩니다. 이 기능의 전체적인 이점은 다음과 같습니다.<br/>&bull; &nbsp; 모임 데이터 프라이버시 표준 및 규정 준수 요구 사항<br/>&bull; &nbsp; 에 대 한 액세스를 제한 하 고 데이터의 보안을 강화 웨어하우스에 매우 중요 한 데이터가 들어 있는입니다.<br/>&bull; &nbsp; 모니터링 및 중요 한 데이터에 대 한 비정상적인 액세스 경고 합니다.<br/>&bull; &nbsp; Azure portal에서는 중앙 대시보드에 중요 한 데이터 시각화. </br></br>데이터 검색 및 분류는 모든 Azure 지역에서 Azure SQL Data Warehouse에 대 한 사용 가능한 취약점 평가 및 위협 검색을 포함 하 여 고급 데이터 보안의 일부입니다. 데이터 검색 및 분류 하는 방법에 대 한 자세한 내용은 참조는 [블로그 게시물](https://azure.microsoft.com/en-us/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/) 및 온라인 [설명서](/azure/sql-database/sql-database-data-discovery-and-classification)합니다.|
|**그룹 롤업**|Azure Data Warehouse에서 지원 되는 GROUP BY 옵션 롤업 됩니다.   GROUP BY ROLLUP 열 식의 각 조합에 대 한 그룹을 만듭니다. GROUP BY도 "롤업" 결과 부분합 및 총합계를 합니다. GROUP BY 함수는 오른쪽에서 왼쪽으로 집계를 만드는 그룹과 줄입니다 열 식의 수를 줄이거나에서 처리 합니다.  열 순서는 ROLLUP 출력에 영향을 줍니다 및 결과 집합의 행 수에 영향을 줄 수 있습니다.<br/><br/>GROUP BY ROLLUP에 대 한 자세한 내용은 참조 하세요. [GROUP BY (TRANSACT-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**DWU 사용 됨 및 CPU 포털 메트릭에 대 한 정확성 향상된**|SQL Data Warehouse에는 Azure portal에서 메트릭 정확도 크게 향상 됩니다.  이 릴리스에서 모든 계산 노드에서 워크 로드를 올바르게 반영 하기 위해 CPU 및 DWU에 사용 되는 메트릭 정의 대 한 수정은 포함 되어 있습니다. 이 문제가 해결 되기 전에 메트릭 값 dereported 되기 때문입니다. 사용 되는 DWU 증가 및 Azure portal에서 CPU 메트릭을 볼 수 있어야 합니다. |
|**행 수준 보안**|2017 년 11 월에에서 행 수준 보안 기능을 도입 했습니다. 이제 외부 테이블에이 지원을 확장 했습니다 했습니다. 또한 인라인 테이블 반환 함수 (인라인 Tvf) 보안 필터 조건자를 정의 하는 데 필요한 비 결정적인 함수 호출에 대 한 지원을 추가 했습니다. 이 추가 사용 하면 IS_ROLEMEMBER(), 보안 필터 조건자에 user_name ()과 등을 지정할 수 있습니다. 자세한 내용은 나와 있는 예제를 참조 하십시오 합니다 [행 수준 보안 설명서](/sql/relational-databases/security/row-level-security)합니다.|
|**추가 T-SQL 지원**|SQL Data Warehouse의 T-SQL 언어 노출 영역에 대 한 지원을 포함 하도록 확장 되었습니다 [STRING_SPLIT (TRANSACT-SQL)](/sql/t-sql/functions/string-split-transact-sql)합니다.
|**쿼리 최적화 프로그램 향상** |쿼리 최적화는 모든 데이터베이스의 주요 구성 요소입니다. 최상의 쿼리를 실행 하는 방법에 대 한 최적의 선택을 향상 시킬 수 있습니다.  분산된 환경에서 복잡 한 분석 쿼리를 실행 하는 경우 작업 수가 문제를 실행 합니다. 향상 된 품질 계획을 생성 하 여 쿼리 성능이 향상 되었습니다. 비용이 많이 드는 데이터 전송 작업을 최소화 하는 이러한 계획 및 중복 계산와 같은 하위 쿼리를 반복 합니다. 자세한 내용은이 Azure SQL Data Warehouse를 참조 하세요 [블로그 게시물](https://azure.microsoft.com/en-us/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)합니다.|
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019년 1월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Return Order By Optimization**|SELECT…ORDER BY 쿼리는 이 릴리스에서 성능이 크게 개선되었습니다.   이제 모든 계산 노드가 단일 계산 노드에 그 결과 보냅니다. 이 노드 병합 하 고 결과 정렬 하 고 사용자에 게 반환 합니다.  쿼리 결과 세트에 많은 수의 행이 포함되어 있을 때 단일 컴퓨팅 노드를 통해 병합하면 성능이 크게 향상됩니다. 이전에 쿼리 실행 엔진이 각 계산 노드에서 결과 정렬 합니다. 결과는 제어 노드가에 스트리밍할 수는 있습니다. 제어 노드는 결과 병합 한 다음.|
|**PartitionMove 및 BroadcastMove의 데이터 이동 기능 개선**|Azure SQL Data Warehouse Gen2 ShuffleMove, 형식의 데이터 이동 단계 인스턴트 데이터 확대 기술을 사용 합니다.  자세한 내용은 [성능 향상 블로그](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)합니다. 이 릴리스에서 PartitionMove 및 BroadcastMove 이제 동일한 인스턴트 데이터 이동 기술을 통해 제공 됩니다. 이러한 유형의 데이터 이동 단계를 사용 하는 사용자 쿼리 성능이 향상된 된 실행 됩니다. 이러한 성능 향상을 얻기 위해 코드를 변경할 필요는 없습니다.|
|**주목할 만한 버그**|잘못 된 Azure SQL Data Warehouse 버전- `SELECT @@VERSION` 10.0.9999.0 잘못 된 버전을 반환할 수 있습니다. 현재 릴리스의 올바른 버전은 10.0.10106.0입니다. 이 버그가 보고되었으며 검토 중입니다.
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
|없음 | |
| | |

## <a name="december-2018"></a>2018년 12월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**일반적으로 사용할 수 있는 virtual Network 서비스 끝점**|이 릴리스에는 모든 Azure 지역의 Azure SQL Data Warehouse에 대한 VNet(가상 네트워크) 서비스 엔드포인트의 일반 공급 버전이 포함됩니다. VNet 서비스 엔드포인트를 사용하면 가상 네트워크 내 지정된 서브넷 또는 서브넷 세트의 논리 서버 연결을 격리할 수 있습니다. VNet에서 Azure SQL Data Warehouse로 전송되는 트래픽은 항상 Azure 백본 네트워크에 유지됩니다. 가상 어플라이언스나 온-프레미스를 통해 인터넷 트래픽을 가져오는 특정 경로보다 이러한 직접 경로를 사용하는 것이 좋습니다. 서비스 엔드포인트를 통한 가상 네트워크 액세스에 대해서는 추가 요금이 부과되지 않습니다. [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)에 대한 현재 가격 책정 모델은 있는 그대로 적용됩니다.<br/><br/>이번 릴리스에서는 [ABFS(Azure Blob FileSystem)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) 드라이버를 통해 [ADLS(Azure Data Lake Storage Gen2)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)로의 PolyBase 연결도 활성화되었습니다. Azure Data Lake Storage Gen2는 분석 데이터의 전체 수명 주기에 필요한 모든 특성을 Azure Storage에 제공합니다. 두 개의 기존 Azure Storage 서비스인 Azure Blob Storage 및 Azure Data Lake Storage Gen1 기능이 통합됩니다. 파일 시스템 의미 체계, 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)의 기능이 [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다.<br/><br/>PolyBase를 사용하면 VNet에서 보안이 적용된 Azure Storage에서 Azure SQL Data Warehouse로 데이터를 가져올 수 있습니다. 마찬가지로 Polybase를 통해 Azure SQL Data Warehouse로부터 VNet에서 보안이 적용된 Azure Storage로 데이터를 내보낼 수도 있습니다.<br/><br/>Azure SQL Data Warehouse의 VNet 서비스 엔드포인트에 대한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) 또는 [설명서](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)를 참조하세요.|
|**자동 성능 모니터링 (미리 보기)**|[쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)는 이제 Azure SQL Data Warehouse에 대한 미리 보기로 제공됩니다. 쿼리 저장소는 데이터 웨어하우스의 작업 및 성능을 모니터링하기 위해 쿼리, 쿼리 계획, 런타임 통계 및 쿼리 기록을 추적함으로써 쿼리 성능 문제를 해결하는 데 도움을 주도록 디자인되었습니다. 쿼리 저장소는 다음을 지원하는 내부 저장소 및 DMV(동적 관리 뷰) 세트입니다.<br/><br/>&bull; &nbsp; 식별 및 상위 리소스 소비 쿼리를 조정 합니다.<br/>&bull; &nbsp; 식별 및 계획 되지 않은 워크 로드를 개선 합니다.<br/>&bull; &nbsp; 변경 내용을 통계, 인덱스 또는 시스템 크기 (DWU 설정) 하 여 쿼리 성능 및 계획에 영향을 평가<br/>&bull; &nbsp; 모든 쿼리 실행에 대 한 전체 쿼리 텍스트를 참조 하세요.<br/><br/>쿼리 저장소에는 다음과 같은 3가지 실제 저장소가 포함되어 있습니다.<br/>&bull; &nbsp; 실행 계획 정보를 유지 하기 위한 계획 저장소<br/>&bull; &nbsp; 실행 통계 정보를 유지 하는 것에 대 한 런타임 통계 저장소<br/>&bull; &nbsp; 대기 통계 저장소 대기 통계 정보를 유지 합니다.<br/><br/>SQL Data Warehouse는 이러한 저장소를 자동으로 관리 하 고 추가 비용 없이 지난 7 일 동안 storied 쿼리의 무제한 제공 합니다. 쿼리 저장소를 사용하도록 설정하는 것은 ALTER DATABASE T-SQL 문을 실행하는 것만큼 간단합니다. <br/>sql---ALTER DATABASE [DatabaseName] 설정 QUERY_STORE = ON;---Azure SQL Data Warehouse에서 쿼리 저장소에 대 한 자세한 내용은 문서를 참조 하세요 [쿼리 저장소를 사용 하 여 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store), 및 쿼리 저장소 Dmv와 같은 [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)합니다. 다음은 출시 소식을 발표하는 [블로그 게시물](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)입니다.|
|**Azure SQL Data Warehouse Gen2 낮은 계산 계층**|이제 Azure SQL Data Warehouse Gen2는 하위 컴퓨팅 계층을 지원합니다. 고객은 100 cDWU([데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md))로 시작해서 Azure SQL Data Warehouse의 최고 성능, 유연성 및 보안 기능을 경험할 수 있으며, 몇 분 안에 30,000 cDWU까지 확장할 수 있습니다. 2018년 12월 중순부터, 고객은 [지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)의 하위 컴퓨팅 계층에 대한 Gen2 성능 및 유연성을 누릴 수 있으며, 나머지 지역의 경우 2019년 중에 서비스가 지원될 예정입니다.<br/><br/>Microsoft는 차세대 데이터 웨어하우징에 대한 진입점을 낮춰 최적의 평가판 환경이 무엇인지 추측하지 않고 안전한 고성능 데이터 웨어하우스의 모든 이점을 평가하려는 가치 중심 고객에게 문을 열어주고 있습니다. 고객은 현재 500 cDWU 진입점에서 최저 100 cDWU로 시작할 수 있습니다. SQL Data Warehouse Gen2는 일시 중지 및 다시 시작 작업을 계속 지원하며 계산 시 뛰어난 유연성을 제공합니다. 또한 Gen2에서는 쿼리당 2.5배 더 많은 메모리, 최대 128개의 동시 쿼리 및 [적응형 캐싱](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) 기능과 함께 제한없는 columnstore 스토리지 용량을 지원합니다. 평균적으로 이러한 기능은 같은 가격으로 제공되는 Gen1의 동일한 데이터 웨어하우스 단위에 비해 5배 더 많은 성능을 보장합니다. 지역 중복 백업은 기본 제공되는 데이터 보호 기능과 함께 Gen2 표준입니다. Azure SQL Data Warehouse Gen2는 필요 시 확장 가능합니다.|
|**Columnstore 백그라운드 병합**|기본적으로 Azure SQL DW(Azure SQL Data Warehouse)는 [rowgroup](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)이라는 마이크로 파티션을 사용하여 데이터를 열 형식으로 저장합니다. 경우에 따라 인덱스 작성 또는 데이터 로드 시의 메모리 제한으로 인해 rowgroup이 최적 크기인 1백만 개 행보다 더 적은 수로 압축될 수 있습니다. 또한 삭제로 인해 rowgroup이 조각화될 수도 있습니다. 작아지거나 조각화된 rowgroup은 더 많은 메모리를 사용하고 쿼리 실행 효율성이 떨어질 수 있습니다. 이 릴리스의 Azure SQL DW에서는 columnstore 백그라운드 유지 관리 작업이 압축된 작은 rowgroup을 더 큰 rowgroup으로 병합하여 메모리 활용률을 높이고 쿼리 실행 속도를 높입니다.
| | |

## <a name="october-2018"></a>2018년 10월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 웨어하우징에 대 한 DevOps**|많은 분들이 SQL DW(SQL Data Warehouse)에 요청하신 기능이 Visual Studio의 SSDT(SQL Server Data Tools)를 통해 미리 보기로 제공됩니다! 이제 개발자 팀에서는 버전이 제어되는 단일 코드 베이스를 통해 공동으로 작업하고 전 세계의 모든 인스턴스에 변경 내용을 신속하게 배포할 수 있습니다. 관심이 있으세요? 이 기능은 현재 미리 보기로 제공되고 있습니다! [SQL Data Warehouse Visual Studio SSDT(SQL Server Data Tools) - 미리 보기 등록 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)을 방문하여 등록할 수 있습니다. 수요가 많기 때문에 고객에게 최상의 환경을 유지하기 위해 미리 보기 사용 인원을 관리하고 있습니다. 미리 보기에 등록하시면 저희 쪽에서 7영업일 내에 상태를 확인할 것입니다.|
|**행 수준 보안 출시**|SQL DW(Azure SQL Data Warehouse)는 이제 RLS(행 수준 보안)를 지원하므로 중요한 데이터를 보호하는 강력한 기능이 추가됩니다. RLS가 도입된 덕분에, 행에 액세스할 수 있는 사람을 제어하는 방법과 마찬가지로 테이블의 행에 대한 액세스를 제어하는 보안 정책을 구현할 수 있습니다. RLS를 사용하면 데이터 웨어하우스를 다시 디자인할 필요 없이 세밀한 액세스 제어가 가능합니다. RLS는 액세스 제한 논리가 다른 애플리케이션의 데이터가 아닌 데이터베이스 계층 자체에 있으므로 보안 모델이 전체적으로 간소화됩니다. 또한 RLS는 액세스 제어 관리용 행을 필터링하는 보기를 도입할 필요가 없습니다. 모든 고객은 이 엔터프라이즈급 보안 기능을 추가 비용 없이 사용할 수 있습니다.|
|**고급 관리자**|데이터 웨어하우스 권장 사항 및 메트릭이 추가되어 Azure SQL DW(SQL Data Warehouse)의 고급 튜닝이 간편해졌습니다. Azure Advisor를 통해 고객이 원하는 대로 사용할 수 있는 다음과 같은 고급 성능 권장 사항이 추가로 제공됩니다.<br/><br/>1. 적응형 캐시 - 캐시 사용률을 최적화하기 위해 확장할 때를 알려줍니다.<br/>2. 테이블 배포 - 데이터 이동을 줄이고 워크로드 성능을 향상하기 위해 테이블을 복제할 때를 결정합니다.<br/>3. Tempdb - tempdb 경합을 줄이기 위해 리소스 클래스를 확장하고 구성할 때를 파악합니다.<br/><br/>개요 블레이드의 거의 실시간 메트릭을 위한 향상된 사용 지정 가능한 모니터링 차트 등 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)와 데이터 웨어하우스 메트릭이 더욱 긴밀하게 통합되었습니다. 사용량을 모니터링하거나 데이터 웨어하우스 권장 사항의 유효성을 검사하고 이 권장 사항을 적용할 때 Azure Monitor 메트릭에 액세스하기 위해 더 이상 데이터 웨어하우스 개요 블레이드에서 나갈 필요가 없습니다. 또한 tempdb, 적응형 캐시 사용률 등 성능 권장 사항을 보완하는 데 사용할 수 있는 새로운 메트릭이 있습니다.|
|**고급 통합된 관리자를 사용 하 여 튜닝**|데이터 웨어하우스 권장 사항 및 메트릭을 추가하고 Azure Advisor와 Azure Monitor가 통합된 환경을 제공하는 포털 개요 블레이드를 다시 디자인하여 Azure SQL DW(SQL Data Warehouse)의 고급 튜닝이 간편해졌습니다.|
|**가속화 된 데이터베이스 복구 (ADR)**|Azure SQL Data Warehouse ADR(가속 데이터베이스 복구)이 공개 미리 보기로 제공됩니다. ADR은 현재 복구 프로세스를 처음부터 다시 설계하여 데이터베이스, 특히 장기 실행 트랜잭션이 있는 데이터베이스의 가용성을 크게 향상하는 새로운 SQL Server 엔진입니다. ADR의 가장 큰 장점은 빠르고 일관적인 데이터베이스 복구와 즉각적인 트랜잭션 롤백입니다.|
|**Azure Monitor 진단 로그**|이제 SQL DW(SQL Data Warehouse)는 Azure Monitor 진단 로그와 직접 통합하여 분석 워크로드에 대한 향상된 인사이트를 얻을 수 있습니다. 이 새 기능을 통해 개발자는 오랫동안 워크로드 동작을 분석하고 쿼리 최적화 또는 용량 관리에 대한 합리적 결정을 내릴 수 있습니다. [Azure Monitor 진단 로그](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)를 통해 데이터 웨어하우스 워크로드에 대한 추가 정보를 제공하는 외부 로깅 프로세스를 도입했습니다. 이제 단추를 한 번만 클릭하면 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries)를 사용하여 기록 쿼리 성능 문제 해결 기능에 대한 진단 로그를 구성할 수 있습니다. Azure Monitor 진단 로그는 감사용으로 스토리지 계정에 로그를 저장하는 방식을 통한 사용자 지정 가능 보존 기간, 실시간에 가까운 원격 분석 인사이트 확인을 위해 로그를 이벤트 허브로 스트리밍하는 기능, 그리고 Log Analytics에서 로그 쿼리를 사용하여 로그를 분석하는 기능을 지원합니다. 진단 로그는 SQL Data Warehouse의 가장 일반적으로 사용되는 성능 문제 해결 DMV에 상응하는 데이터 웨어하우스의 원격 분석 보기로 구성됩니다. 이 초기 릴리스의 경우 다음 시스템 동적 관리 뷰에 대한 보기가 설정되었습니다.<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**Columnstore 메모리 관리**|압축된 열 저장소 행 그룹의 수가 증가하면 이러한 행 그룹에 대한 내부 열 세그먼트 메타데이터를 관리하는 데 필요한 메모리도 증가합니다.  결과적으로 일부 Columnstore DMV(동적 관리 뷰)에 대해 실행되는 쿼리 및 쿼리 성능이 저하될 수 있습니다.  이번 릴리스에서는 이러한 경우에 대한 내부 메타데이터 크기가 최적화되어 이러한 쿼리의 환경 및 성능이 개선되도록 수정되었습니다.|
|**Azure Data Lake 저장소 Gen2 통합 (GA**|이제 Azure SQL DW(SQL Data Warehouse)가 Azure Data Lake Storage Gen2와 기본적으로 통합됩니다. 고객은 ABFS에서 외부 테이블을 사용하여 SQL DW로 데이터를 로드할 수 있습니다. 이 기능을 통해 고객은 데이터 레이크를 Data Lake Storage Gen2에 통합할 수 있습니다.|
|**주목할 만한 버그**|CETAS Parquet 장애 DW2000 및 더 많은 데이터 웨어하우스 작은 리소스 클래스에이 문제가 해결 Parquet 코드 경로에 만드는 외부 테이블을로 null 참조를 올바르게 식별합니다.<br/><br/>Id 열 값 식별 열의 값 유지 되지 않는 경우 일부 CTAS 작업에서 손실 될 다른 테이블에 CTASed입니다. 보고를 [블로그](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)합니다.<br/><br/>쿼리가 실행 되는 여전히-쿼리가 여전히 실행 되는 경우 세션이 종료 될 경우이 문제가 해결 InvalidOperationException을 트리거하는 동안에 세션이 종료 될 경우에서 내부 오류가 발생 했습니다.<br/><br/>(2018 년 11 월에에서 배포 됨) 고객은 여러 작은 파일 (Gen1) ADLS에서 Polybase를 사용 하 여 로드 하려고 할 때 최적 상태가 아닌 성능을 발생 합니다. 시스템 성능 AAD 보안 토큰 유효성 검사 동안 병목 상태가 되었습니다. 보안 토큰의 캐싱을 사용하여 성능 문제를 완화했습니다. |
| | |

## <a name="next-steps"></a>다음 단계

- [SQL Data Warehouse 만들기](./create-data-warehouse-portal.md)

## <a name="more-information"></a>자세한 정보

- [블로그 - Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [고객 자문 팀 블로그](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 용어집](../azure-glossary-cloud-terminology.md)

---
title: 릴리스 정보
description: Azure Synapse Analytics에 대한 릴리스 정보입니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 4/30/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 01b6a72dfe3180324919fd712412ae88578c641b
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047455"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure Synapse Analytics 릴리스 정보

이 문서에는 Azure Synapse에서 [Synapse SQL](sql-data-warehouse-overview-what-is.md)의 최신 릴리스에 포함된 새로운 기능과 향상된 기능이 요약되어 있습니다. 이 문서에는 릴리스와 직접적인 관련은 없지만 동일한 시간대에 게시된 주목할 만한 콘텐츠 업데이트도 나열되어 있습니다. 다른 Azure 서비스의 개선 사항은 [서비스 업데이트](https://azure.microsoft.com/updates)를 참조하세요.

## <a name="check-your-azure-synapse-version"></a>Azure Synapse 버전 확인

새 기능이 모든 지역에 롤아웃되면 인스턴스에 배포된 버전과 기능 가용성에 대한 최신 릴리스 정보를 확인합니다. 버전을 확인하려면 SSMS(SQL Server Management Studio)를 통해 SQL 풀에 연결하고 `SELECT @@VERSION;`을 실행하여 현재 버전을 반환합니다. 이 버전을 사용하여 SQL 풀에 적용된 릴리스를 확인합니다. 출력의 날짜는 SQL 풀에 적용된 릴리스의 월을 식별합니다. 개선된 서비스 수준의 경우도 마찬가지입니다. 

도구 개선을 위해 릴리스 정보에 지정된 올바른 버전을 설치했는지 확인합니다. 

> [!NOTE]
> SELECT @@VERSION에서 반환된 제품 이름은 Microsoft Azure SQL Data Warehouse에서 Microsoft Azure Synapse Analytics로 변경될 예정입니다. 변경을 수행하기 전에 자세한 공지를 보내 드립니다. 이 변경은 애플리케이션 코드의 SELECT @@VERSION 결과에서 제품 이름을 구문 분석하는 고객과 관련이 있습니다. 제품 재브랜딩으로 인한 애플리케이션 코드 변경을 방지하려면 다음 명령을 사용하여 SERVERPROPERTY에서 데이터베이스 제품 이름 및 버전을 쿼리합니다. 버전 번호 XX.X.XXXXX.X(제품 이름 제외)를 반환하려면 다음 명령을 사용합니다.
>
> ```sql
> SELECT SERVERPROPERTY('ProductVersion')
>
> --To return engine edition, use this command that returns 6 for Azure Synapse Analytics (Formerly SQL Data Warehouse):
>
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="aug-2020"></a>8 월 2020

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**워크 로드 관리 – 포털 환경**|사용자는 Azure Portal를 통해 워크로드 관리 설정을 구성하고 관리할 수 있습니다. 중요하게도 [워크로드 그룹](/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-portal) 및 [워크로드 분류자](/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)를 구성하는 기능도 사용할 수 있습니다.|
|**향상 된 테이블 매핑 카탈로그 뷰**|새 카탈로그 뷰 [sys. pdw_permanent_table_mappings](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=sqlallproducts-allversions) 는 영구 사용자 테이블의 **object_ids** 를 실제 테이블 이름에 매핑합니다.|

## <a name="july-2020"></a>2020년 7월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**열 수준 암호화 (공개 미리 보기)**|Transact-sql을 사용 하 여 데이터 열에 대칭 암호화를 적용 하 여 Synapse SQL data warehouse의 중요 한 정보를 보호 합니다. 열 수준 암호화에는 인증서, 암호, 대칭 키 또는 비대칭 키로 보호 되는 대칭 키를 사용 하 여 데이터를 암호화 하는 데 사용할 수 있는 기본 제공 함수가 있습니다. 자세한 내용은 [데이터 열 암호화](/sql/relational-databases/security/encryption/encrypt-a-column-of-data?view=azure-sqldw-latest)를 참조 하세요.|
|**GA (호환성 수준 지원)**|이 릴리스에서는 이제 사용자가 데이터베이스의 호환성 수준을 설정하여 특정 버전의 Synapse SQL 엔진에 대한 Transact-SQL 언어 및 쿼리 처리 동작을 얻을 수 있습니다. 자세한 내용은 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [Alter Database Scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조하세요.|
|**행 수준 보안**|이 릴리스에는 RLS를 적용 한 행에 대 한 업데이트 및 삭제 작업에 대 한 개선 사항이 포함 되어 있습니다. 이 릴리스에서는 내장 함수가 DML 대상 테이블의 열을 참조 하지 않는 경우 ' is_rolemember ' 같은 내장 함수를 사용 하 여 업데이트 및 삭제 작업이 성공 합니다. 이러한 향상 전에는 기본 DML 작업의 제한으로 인해 이러한 작업이 실패 했습니다.|
|**GA (DBCC SHRINKDATABASE)**|이제 지정 된 데이터베이스에 있는 데이터 및 로그 파일의 크기를 축소할 수 있습니다. 자세한 내용은 [설명서](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql?view=sql-server-ver15)를 참조 하세요.|

## <a name="may-2020"></a>2020년 5월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**워크로드 격리(GA)**|[워크로드 격리](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-isolation)가 이제 일반 공급됩니다.  [워크로드 그룹](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)을 통해 리소스를 예약하고 포함할 수 있습니다.  런어웨이 쿼리를 취소하도록 쿼리 제한 시간을 구성하는 기능을 사용할 수도 있습니다.|
|**워크로드 관리 포털 환경(미리 보기)**| 사용자는 Azure Portal를 통해 워크로드 관리 설정을 구성하고 관리할 수 있습니다.  중요하게도 [워크로드 그룹](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal) 및 [워크로드 분류자](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)를 구성하는 기능도 사용할 수 있습니다.|
|**Alter workload group**|[ALTER WORKLOAD GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-workload-group-transact-sql?view=azure-sqldw-latest) 명령을 사용하는 기능을 이제 사용할 수 있습니다.  alter를 사용하여 기존 [워크로드 그룹](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-isolation)의 구성을 변경할 수 있습니다.|
|**COPY 명령을 사용 하 여 Parquet 파일에 대 한 자동 스키마 검색 (미리 보기)**|[COPY 명령](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)은 이제 Parquet 파일을 로드할 때 자동 스키마 검색을 지원합니다. 이 명령은 Parquet 파일 스키마를 자동으로 검색하고 로드 전에 테이블을 만듭니다. 이 기능을 사용 하도록 설정 하려면 다음 전자 메일 배포 목록에 문의 sqldwcopypreview@service.microsoft.com 하세요.. |
|**COPY 명령을 사용 하 여 복잡 한 Parquet 데이터 형식 로드 (미리 보기)**|[COPY 명령](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)은 이제 복합 Parquet 형식 로드를 지원합니다. 지도 및 목록과 같은 복합 형식을 문자열 열로 로드할 수 있습니다.  이 기능을 사용 하도록 설정 하려면 다음 전자 메일 배포 목록에 문의 sqldwcopypreview@service.microsoft.com 하세요.. |
|**COPY 명령을 사용하여 Parquet 파일의 자동 압축 검색**|[COPY 명령](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)은 이제 Parquet 파일의 압축 방법에 대한 자동 검색을 지원합니다. 이 기능을 사용 하도록 설정 하려면 다음 전자 메일 배포 목록에 문의 sqldwcopypreview@service.microsoft.com 하세요..|
|**추가 로드 권장 사항**|이제 [로드 권장 사항](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations)을 Synapse SQL에 대해 사용할 수 있습니다. 최대 처리량을 위해 파일을 분할하거나, 스토리지 계정을 SQL 풀과 함께 배치하거나, SQLBulkCopy API 또는 BCP와 같은 유틸리티 로드를 사용하는 경우 일괄 처리 크기를 늘려야 하는 경우 사전 알림을 받습니다.|
|**T-SQL 업데이트 가능 배포 열(GA)**|이제 사용자가 배포 열에 저장된 데이터를 업데이트할 수 있습니다. 자세한 내용은 [Synapse SQL 풀의 분산 테이블 디자인에 대한 지침](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute)을 참조하세요.|
|**조인에서 T-SQL Update/Delete...(GA)**|이제 다른 테이블과의 조인 결과에 따라 Update 및 Delete를 사용할 수 있습니다. 자세한 내용은 [Update](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest) 및 [Delete](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest) 설명서를 참조하세요.|
|**T-SQL PREDICT(미리 보기)**|이제 데이터 웨어하우스 내에서 기계 학습 모델을 예측하여 크고 복잡한 데이터를 이동하지 않아도 될 수 있습니다. T-SQL PREDICT 함수는 개방형 모델 프레임워크를 사용하고 데이터 및 기계 학습 모델을 입력으로 사용하여 예측을 생성합니다. 자세한 내용은 [설명서](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)를 참조하세요.|

## <a name="april-2020"></a>2020년 4월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터베이스 호환성 수준(미리 보기)**| 이 릴리스에서는 이제 사용자가 데이터베이스의 호환성 수준을 설정하여 특정 버전의 Synapse SQL 엔진에 대한 Transact-SQL 언어 및 쿼리 처리 동작을 얻을 수 있습니다. 자세한 내용은 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [Alter Database Scoped Configuration](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조하세요.|
|**Sp_describe_undeclared_parameters**| 사용자가 Transact-SQL 일괄 처리에서 선언되지 않은 매개 변수에 대한 메타데이터를 볼 수 있도록 허용합니다. 자세한 내용은 [sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.| <br/><br/><br/>

| 도구 개선 사항                                         | 세부 정보                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 5](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview#--visual-studio-2019-version-166-preview-5-) - SSDT(SQL Server Data Tools)** | 이 릴리스에는 SSDT에 대한 다음과 같은 개선 사항이 포함되어 있습니다. </br> </br> - 데이터 검색 및 분류<br/> - COPY 문 <br/> - Unique 제약 조건이 있는 테이블<br/> - 순서가 지정된 클러스터형 columnstore 인덱스가 있는 테이블<br/> <br/>이 릴리스에서는 SSDT가 다음과 같이 수정되었습니다. </br></br>  - 배포 열 데이터 형식을 변경하는 경우 SSDT에서 생성하는 업데이트 스크립트는 테이블을 삭제하고 다시 만드는 대신 CTAS 및 RENAME 작업을 수행합니다. </br> |

## <a name="march-2020"></a>2020년 3월

| 도구 개선 사항                                         | 세부 정보                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 2](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview#whats-new-in-visual-studio-2019) SSDT(SQL Server Data Tools)** | 이 릴리스에는 SSDT에 대한 다음과 같은 개선 사항 및 수정 사항이 포함되어 있습니다. </br> </br> - MV(구체화된 뷰)에 의해 참조되는 테이블을 변경하면 MV에 대해 지원되지 않는 뷰 변경 문이 생성되는 문제가 해결되었습니다.<br/><br/> - 데이터베이스 또는 프로젝트에 행 수준 보안 개체가 있을 때 스키마 비교 작업이 실패하지 않도록 하도록 변경이 구현되었습니다. 행 수준 보안 개체는 현재 SSDT에 대해 지원되지 않습니다.  <br/><br/> - 데이터베이스에 있는 많은 수의 개체를 나열할 때 시간 제한이 발생하지 않도록 SQL Server 개체 탐색기 시간 제한 임계값이 증가했습니다.<br/><br/> - 개체 탐색기를 채울 때 불안정성을 줄이고 성능을 향상시키기 위해 SQL Server 개체 탐색기에서 데이터베이스 개체 목록을 검색하는 방법을 최적화했습니다. |

## <a name="january-2020"></a>2020년 1월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**워크로드 관리 포털 메트릭(미리 보기)**|지난 10월에 미리 보기용 [워크로드 격리](sql-data-warehouse-workload-isolation.md) 릴리스가 출시되면서 사용자들은 이제 자신의 [워크로드 그룹](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 만들어 시스템 리소스를 효율적으로 관리하고 비즈니스 SLA를 충족할 수 있습니다.  Azure Synapse Analytics의 전체 [워크로드 관리](sql-data-warehouse-workload-management.md) 기능 개선의 일부로, 이제 새로운 [워크로드 관리 모니터링 메트릭](sql-data-warehouse-workload-management-portal-monitor.md)을 사용할 수 있습니다.</br> </br> 이제 워크로드 모니터링은 다음과 같은 메트릭으로 인사이트가 강화되었습니다. </br> - 유효 상한 리소스 비율  </br> - 유효 최소 리소스 비율 </br> - 워크로드 그룹 활성 쿼리 </br> - 최대 리소스 비율별 워크로드 그룹 할당 </br> - 시스템 비율별 워크로드 그룹 할당 </br> - 워크로드 그룹 쿼리 시간 제한 </br> - 워크로드 그룹 큐에 대기 중인 쿼리 </br></br> 이러한 메트릭을 사용하여 [워크로드 그룹 병목 상태](sql-data-warehouse-workload-management-portal-monitor.md#workload-group-bottleneck) 또는 [미달 사용 워크로드 격리](sql-data-warehouse-workload-management-portal-monitor.md#underutilized-workload-isolation)로 구성된 워크로드 그룹을 식별할 수 있습니다.  이러한 메트릭은 워크로드 그룹별 분할을 허용하는 Azure Portal에서 사용할 수 있습니다.  자주 사용하는 그래프를 필터링하고 대시보드에 고정하여 인사이트에 빠르게 액세스할 수 있습니다.|
|**포털 모니터링 메트릭**| 전체 쿼리 작업을 모니터링하기 위해 다음 메트릭이 포털에 추가되었습니다. </br> - 활성 쿼리 </br> - 큐에 대기 중인 쿼리 </br> </br>이러한 메트릭은 [모니터링 리소스 사용률 및 쿼리 작업 설명서](sql-data-warehouse-concept-resource-utilization-query-activity.md)에서 기존 메트릭과 함께 설명됩니다.|

## <a name="october-2019"></a>2019년 10월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Copy(미리 보기)**|데이터 수집을 위한 간단하고 유연한 COPY 문의 퍼블릭 미리 보기를 발표하게 되어 기쁘게 생각합니다. 이 문 하나만 사용하면 높은 권한 없이도 보다 유연하면서 원활하게 데이터를 수집할 수 있습니다. 자세한 내용은 [COPY 명령 설명서](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.|
|**워크로드 격리(미리 보기)**|고객의 데이터 웨어하우스를 보편화하도록 지원하기 위해 지능형 워크로드 관리를 위한 새로운 기능을 발표하게 되었습니다. 새 [워크로드 격리](sql-data-warehouse-workload-isolation.md) 기능을 사용하여 데이터 웨어하우스 리소스에 대해 유연성과 제어 능력을 제공하는 동시에 유형이 다른 워크로드의 실행을 관리할 수 있습니다. 이로 인해 실행 예측 가능성이 향상되고 미리 정의된 SLA를 충족하는 기능이 강화됩니다. </br>워크로드 격리 외에도 이제 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 위한 추가 옵션도 사용할 수 있습니다.  로그인 분류 외에 [워크로드 분류자 만들기](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문은 쿼리 레이블, 세션 컨텍스트 및 시간을 기준으로 요청을 분류하는 기능을 제공합니다.|
|**PREDICT(미리 보기)**|이제 데이터 웨어하우스 내에서 기계 학습 모델에 점수를 매기고 크고 복잡한 데이터를 이동하지 않아도 될 수 있습니다. T-SQL PREDICT 함수는 개방형 모델 프레임워크를 사용하고 데이터 및 기계 학습 모델을 입력으로 사용하여 예측을 생성합니다.
|**SSDT CI/CD(GA)**|현재 SQL Analytics – SSDT(SQL Server Data Tools) 데이터베이스 프로젝트에 대해 [가장 많이 요청한 기능](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)을 일반 공급으로 제공하게 되었습니다. 이 릴리스에서는 엔터프라이즈 수준 배포에 대한 기본 제공 CI/CD(지속적인 통합 및 배포) 기능을 제공하는 Azure DevOps와의 네이티브 플랫폼 통합을 포함하는 SSDT(Visual Studio 2019 포함)가 지원됩니다. |
|**구체화된 뷰(GA)**|구체화된 뷰는 뷰 정의 쿼리에서 반환되는 데이터를 유지하고 기본 테이블에서 데이터가 변경될 때 자동으로 업데이트됩니다. 간단한 유지 관리 작업을 제공하면서 복잡한 쿼리(일반적으로 조인 및 집계를 사용한 쿼리)의 성능을 향상시킵니다. 자세한 내용은 [구체화된 뷰로 성능 조정](performance-tuning-materialized-views.md)을 참조하세요.  구체화된 뷰를 스크립팅하기 위해 [SQL Server Management Studio 18.4 이상](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 설치합니다.|
|**동적 데이터 마스킹(GA)**|DDM(동적 데이터 마스킹)은 사용자가 정의한 마스킹 규칙에 따라 쿼리 결과에서 중요한 데이터를 즉시 난독 처리하여 데이터 웨어하우스의 중요한 데이터에 무단으로 액세스하지 못하게 합니다. 자세한 내용은 [SQL 데이터베이스 동적 데이터 마스킹](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조하세요.|
|**커밋된 스냅샷 격리 읽기(GA)**|ALTER DATABASE를 사용하여 사용자 데이터베이스에 대한 스냅샷 격리를 사용하거나 사용하지 않도록 설정할 수 있습니다. 현재 워크로드에 영향을 주지 않으려면 데이터베이스 유지 관리 기간 동안 이 옵션을 설정하거나 데이터베이스에 대한 다른 활성 연결이 없을 때까지 기다릴 수 있습니다. 자세한 내용은 [Alter database set 옵션](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조하세요.|
|**순서가 지정된 클러스터형 columnstore 인덱스(GA)**|columnstore는 대량의 데이터를 저장하고 효율적으로 쿼리하기 위한 핵심 기능입니다. 순서가 지정된 클러스터형 columnstore 인덱스는 효율적인 세그먼트 제거를 지원하여 쿼리 실행을 좀 더 최적화합니다.   자세한 내용은 [순서가 지정된 클러스터형 columnstore 인덱스](performance-tuning-ordered-cci.md)를 참조하세요.|
|**결과 세트 캐싱(GA)**|결과 세트 캐싱을 사용하도록 설정한 경우 쿼리 결과는 반복 사용을 위해 사용자 데이터베이스에 자동으로 캐시됩니다. 이렇게 하면 후속 쿼리 실행 시 지속형 캐시에서 직접 결과를 가져올 수 있으므로 재계산이 필요하지 않습니다. 결과 세트 캐싱은 쿼리 성능을 향상시키고 컴퓨팅 리소스 사용량을 줄입니다. 또한 캐시된 결과 세트를 사용하는 쿼리는 동시성 슬롯을 사용하지 않으므로 기존 동시성 제한이 적용되지 않습니다. 보안을 위해 캐시된 결과를 만든 사용자와 동일한 데이터 액세스 권한이 있는 경우에만 캐시된 결과에 액세스할 수 있습니다. 자세한 내용은 [결과 집합 캐싱을 사용한 성능 조정](performance-tuning-result-set-caching.md)을 참조하세요. 버전 10.0.10783.0 이상에 적용됩니다.|

## <a name="september-2019"></a>2019년 9월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Azure Private Link(미리 보기)**|[Azure Private Link](https://azure.microsoft.com/blog/announcing-azure-private-link/)를 사용하여 VNet(가상 네트워크)에서 프라이빗 엔드포인트를 만들고 SQL 풀에 매핑할 수 있습니다. 그런 다음, VNet의 프라이빗 IP 주소를 통해 이러한 리소스에 액세스할 수 있으므로 Azure Express 경로 프라이빗 피어링 및/또는 VPN 게이트웨이를 통해 온-프레미스에서 연결할 수 있습니다. 이를 통해 전반적으로 퍼블릭 IP 주소를 열지 않아도 되므로 네트워크 구성이 간단해 집니다. 또한 데이터 반출 위험으로부터 보호할 수 있습니다. 자세한 내용은 [개요](../../private-link/private-link-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 및 [SQL Analytics 설명서](../../azure-sql/database/private-endpoint-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요.|
|**데이터 검색 및 분류(GA)**|[데이터 검색 및 분류](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 기능이 이제 일반 공급됩니다. 이 기능은 데이터베이스의 중요한 데이터를 **검색, 분류, 레이블 지정 및 보호**하기 위한 고급 기능을 제공합니다.|
|**Azure Advisor 단일 클릭 통합**|Azure Synapse의 SQL Analytics는 이제 클릭 한 번으로 개요 블레이드의 Azure Advisor 권장 사항에 직접 연결될 수 있습니다. 이제 Azure advisor 블레이드로 이동하는 대신 개요 블레이드에서 권장 사항을 찾을 수 있습니다. [여기](sql-data-warehouse-concept-recommendations.md)에서 권장 사항에 대해 자세히 알아보세요.|
|**커밋된 스냅샷 격리 읽기(미리 보기)**|ALTER DATABASE를 사용하여 사용자 데이터베이스에 대한 스냅샷 격리를 사용하거나 사용하지 않도록 설정할 수 있습니다.  현재 워크로드에 영향을 주지 않으려면 데이터베이스 유지 관리 기간 동안 이 옵션을 설정하거나 데이터베이스에 대한 다른 활성 연결이 없을 때까지 기다릴 수 있습니다. 자세한 내용은 [Alter database set 옵션](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조하세요.|
|**EXECUTE AS(Transact-SQL)**| [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 지원을 사용하면 고객이 세션의 실행 컨텍스트를 지정된 사용자로 설정할 수 있습니다.|
|**추가 T-SQL 지원**|Synapse SQL에 대한 T-SQL 언어 노출 영역은 다음에 대한 지원을 포함하도록 확장되었습니다. </br> - [FORMAT(Transact-SQL)](/sql/t-sql/functions/format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_PARSE(Transact-SQL)](/sql/t-sql/functions/try-parse-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CAST(Transact-SQL)](/sql/t-sql/functions/try-cast-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CONVERT(Transact-SQL)](/sql/t-sql/functions/try-convert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.user_token(Transact-SQL)](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019년 7월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**구체화된 뷰(미리 보기)**|구체화된 뷰는 뷰 정의 쿼리에서 반환되는 데이터를 유지하고 기본 테이블에서 데이터가 변경될 때 자동으로 업데이트됩니다. 간단한 유지 관리 작업을 제공하면서 복잡한 쿼리(일반적으로 조인 및 집계를 사용한 쿼리)의 성능을 향상시킵니다. 자세한 내용은 다음을 참조하세요. </br> - [CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br> - [Synapse SQL에서 지원되는 T-SQL 문](sql-data-warehouse-reference-tsql-statements.md)|
|**추가 T-SQL 지원**|Synapse SQL에 대한 T-SQL 언어 노출 영역은 다음에 대한 지원을 포함하도록 확장되었습니다. </br> - [AT TIME ZONE(Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [STRING_AGG(Transact-SQL)](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**결과 세트 캐싱(미리 보기)**|이전에 발표된 결과 세트 캐시를 관리하기 위해 DBCC 명령이 추가되었습니다. 자세한 내용은 다음을 참조하세요. </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br></br> 또한 실행된 쿼리가 결과 세트 캐시를 사용했을 때 표시되는 [dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에서 새 result_set_cache 열을 참조하세요.|
|**순서가 지정된 클러스터형 columnstore 인덱스(미리 보기)**|순서가 지정된 클러스터형 columnstore 인덱스에서 열 순서를 식별하도록 새 열 column_store_order_ordinal이 [sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에 추가되었습니다.|

## <a name="may-2019"></a>2019년 5월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**동적 데이터 마스킹(미리 보기)**|DDM(동적 데이터 마스킹)은 사용자가 정의한 마스킹 규칙에 따라 쿼리 결과에서 중요한 데이터를 즉시 난독 처리하여 데이터 웨어하우스의 중요한 데이터에 무단으로 액세스하지 못하게 합니다. 자세한 내용은 [SQL 데이터베이스 동적 데이터 마스킹](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 참조하세요.|
|**워크로드 중요도가 이제 일반 공급됨**|워크로드 관리 분류 및 중요도는 쿼리 실행 순서에 영향을 줄 수 있는 기능을 제공합니다. 워크로드 중요도에 대한 자세한 내용은 설명서의 [분류](sql-data-warehouse-workload-classification.md) 및 [중요도](sql-data-warehouse-workload-importance.md) 개요 문서를 참조하세요. [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 설명서도 확인하세요.<br/><br/>아래 비디오에서 작업의 워크로드 중요도를 참조하세요.<br/> -[워크로드 관리 개념](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[워크로드 관리 시나리오](https://www.youtube.com/embed/_2rLMljOjw8)|
|**추가 T-SQL 지원**|Synapse SQL에 대한 T-SQL 언어 노출 영역은 다음에 대한 지원을 포함하도록 확장되었습니다. </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**JSON 함수**|비즈니스 분석가는 이제 친숙한 T-SQL 언어를 사용하여 다음과 같은 새로운 JSON 함수를 통해 JSON 데이터로 서식이 지정된 문서를 쿼리하고 조작할 수 있습니다.</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**결과 세트 캐싱(미리 보기)**|결과 세트 캐싱은 비즈니스 분석가 및 보고 사용자가 인사이트를 파악하는 데 드는 시간을 단축하면서 즉각적인 쿼리 응답을 지원합니다. 자세한 내용은 다음을 참조하세요.</br> - [ALTER DATABASE(Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 옵션(Transact SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET RESULT SET CACHING(Transact-SQL)](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET 문(Transact-SQL)](/sql/t-sql/statements/set-statements-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.databases(Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**순서가 지정된 클러스터형 columnstore 인덱스(미리 보기)**|columnstore는 대량의 데이터를 저장하고 효율적으로 쿼리하기 위한 핵심 기능입니다. 각 테이블에 대해 들어오는 데이터를 행 그룹으로 나누고, 행 그룹의 각 열이 디스크의 세그먼트를 형성합니다.  순서가 지정된 클러스터형 columnstore 인덱스는 효율적인 세그먼트 제거를 지원하여 쿼리 실행을 좀 더 최적화합니다.   자세한 내용은 다음을 참조하세요.</br> -  [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [CREATE COLUMNSTORE INDEX(Transact-SQL)](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).|

## <a name="march-2019"></a>2019년 3월

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 검색 및 분류**|이제 Synapse SQL의 퍼블릭 미리 보기에서 데이터 검색 및 분류를 사용할 수 있습니다. 중요한 데이터를 보호하고 고객의 개인 정보를 보호하는 것이 중요합니다. 비즈니스 및 고객 데이터 자산이 증가하면서 데이터를 검색, 분류 및 보호하는 작업이 다루기 어려워 집니다. Synapse SQL에 기본적으로 도입된 데이터 검색 및 분류 기능은 데이터를 보다 효율적으로 보호하는 데 도움이 됩니다. 이 기능의 전체적인 이점은 다음과 같습니다.<br/>&bull; &nbsp; 데이터 프라이버시 표준 및 규정 준수 요구 사항 충족<br/>&bull; &nbsp; 매우 중요한 데이터가 들어 있는 데이터 웨어하우스에 대한 액세스 제어 및 보안 강화<br/>&bull; &nbsp; 중요한 데이터에 대한 비정상적인 액세스 모니터링 및 경고<br/>&bull; &nbsp; Azure Portal의 중앙 대시보드에서 중요한 데이터 시각화 </br></br>데이터 검색 및 분류는 모든 Azure 지역에서 사용할 수 있으며, 취약성 평가 및 위협 감지를 비롯한 고급 데이터 보안의 일부로 제공됩니다. 데이터 검색 및 분류에 대한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/) 및 온라인 [설명서](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요.|
|**GROUP BY ROLLUP**|ROLLUP은 이제 지원되는 GROUP BY 옵션입니다.   GROUP BY ROLLUP은 열 식의 각 조합에 대한 그룹을 만듭니다. 또한 GROUP BY는 결과를 부분합 및 총합계로 "롤업"합니다. GROUP BY 함수는 오른쪽에서 왼쪽으로 이동하면서 그룹 및 집계를 만드는 열 식의 수를 줄입니다.  열 순서는 ROLLUP 출력에 영향을 주며, 결과 집합의 행 수에도 영향을 줄 수 있습니다.<br/><br/>GROUP BY ROLLUP에 대한 자세한 내용은 [GROUP BY(Transact-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.
|**DWU 사용 및 CPU 포털 메트릭에 대한 정확도 향상**|Synapse SQL은 Azure Portal에서 메트릭 정확도를 크게 향상시킵니다.  이 릴리스에서는 모든 컴퓨팅 노드에서 워크로드를 올바르게 반영하도록 CPU 및 DWU 사용 메트릭이 수정되었습니다. 이 수정 이전에는 메트릭 값이 적게 보고되었습니다. 이제 Azure Portal에서 DWU 사용 및 CPU 메트릭이 증가하는 것을 볼 수 있습니다. |
|**행 수준 보안**|행 수준 보안 기능은 2017년 11월에 다시 도입되었습니다. 이제 이러한 지원이 외부 테이블로도 확장되었습니다. 또한 보안 필터 조건자를 정의하는 데 필요한 인라인 TVF(인라인 테이블 반환 함수)에서 비결정적 함수를 호출하기 위한 지원이 추가되었습니다. 이 추가 기능을 사용하면 보안 필터 조건자에 IS_ROLEMEMBER(), USER_NAME() 등을 지정할 수 있습니다. 자세한 내용은 [행 수준 보안 설명서](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)의 예제를 참조하세요.|
|**추가 T-SQL 지원**|Synapse SQL에 대한 T-SQL 언어 노출 영역은 [STRING_SPLIT(Transact-SQL)](/sql/t-sql/functions/string-split-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에 대한 지원을 포함하도록 확장되었습니다.
|**쿼리 최적화 프로그램 기능 개선** |쿼리 최적화는 모든 데이터베이스의 중요한 구성 요소입니다. 쿼리를 가장 잘 실행하는 최적의 방법을 선택하면 획기적인 향상을 구현할 수 있습니다.  분산 환경에서 복잡한 분석 쿼리를 실행할 때 실행되는 작업의 수는 매우 중요합니다. 더 나은 품질 플랜을 생성하여 쿼리 성능이 개선되었습니다. 이러한 플랜은 비용이 많이 드는 데이터 전송 작업과 반복되는 하위 쿼리와 같은 중복 컴퓨팅을 최소화합니다. 자세한 내용은 이 Azure Synapse [블로그 게시물](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)을 참조하세요.|
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019년 1월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Return Order By Optimization**|SELECT…ORDER BY 쿼리는 이 릴리스에서 성능이 크게 개선되었습니다.   이제 모든 컴퓨팅 노드가 결과를 단일 컴퓨팅 노드로 보냅니다. 이 노드는 결과를 병합하고 정렬한 다음, 사용자에게 반환합니다.  쿼리 결과 세트에 많은 수의 행이 포함되어 있을 때 단일 컴퓨팅 노드를 통해 병합하면 성능이 크게 향상됩니다. 이전에는 쿼리 실행 엔진이 각 컴퓨팅 노드에서 결과를 정렬했습니다. 결과는 제어 노드로 스트리밍되었습니다. 그러면 제어 노드가 결과를 병합했습니다.|
|**PartitionMove 및 BroadcastMove의 데이터 이동 기능 개선**|ShuffleMove 형식의 데이터 이동 단계는 인스턴트 데이터 이동 기법을 사용합니다.  자세한 내용은 [성능 향상 블로그](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)를 참조하세요. 이번 릴리스에서는 동일한 인스턴트 데이터 이동 기술을 통해 PartitionMove 및 BroadcastMove가 지원됩니다. 이러한 유형의 데이터 이동 단계를 사용하는 사용자 쿼리의 성능이 개선됩니다. 이러한 성능 향상을 얻기 위해 코드를 변경할 필요는 없습니다.|
|**주목할 만한 버그**|잘못된 Azure Synapse 버전 - `SELECT @@VERSION`이 잘못된 버전인 10.0.9999.0을 반환할 수 있습니다. 현재 릴리스의 올바른 버전은 10.0.10106.0입니다. 이 버그가 보고되었으며 검토 중입니다.
| | |

### <a name="documentation-improvements"></a>설명서 개선

| 설명서 개선 | 세부 정보 |
| --- | --- |
|none | |
| | |

## <a name="december-2018"></a>2018년 12월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**Virtual Network 서비스 엔드포인트 일반 공급**|이 릴리스에는 모든 Azure 지역의 Azure Synapse에 있는 SQL Analytics에 대한 VNet(가상 네트워크) 서비스 엔드포인트의 일반 공급 버전이 포함됩니다. VNet 서비스 끝점을 사용 하면 가상 네트워크 내의 지정 된 서브넷 또는 서브넷 집합에서 서버에 대 한 연결을 격리할 수 있습니다. VNet에서 Azure Synapse로 전송되는 트래픽은 항상 Azure 백본 네트워크에 유지됩니다. 가상 어플라이언스나 온-프레미스를 통해 인터넷 트래픽을 가져오는 특정 경로보다 이러한 직접 경로를 사용하는 것이 좋습니다. 서비스 엔드포인트를 통한 가상 네트워크 액세스에 대해서는 추가 요금이 부과되지 않습니다. [Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)에 대한 현재 가격 책정 모델은 있는 그대로 적용됩니다.<br/><br/>이번 릴리스에서는 [ABFS(Azure Blob FileSystem)](../../storage/blobs/data-lake-storage-abfs-driver.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 드라이버를 통해 [ADLS(Azure Data Lake Storage Gen2)](../../storage/blobs/data-lake-storage-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)로의 PolyBase 연결도 활성화되었습니다. Azure Data Lake Storage Gen2는 분석 데이터의 전체 수명 주기에 필요한 모든 특성을 Azure Storage에 제공합니다. 두 개의 기존 Azure Storage 서비스인 Azure Blob Storage 및 Azure Data Lake Storage Gen1 기능이 통합됩니다. 파일 시스템 의미 체계, 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)의 기능이 [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합됩니다.<br/><br/>PolyBase를 사용하면 VNet에서 보안이 적용된 Azure Storage에서 Azure Synapse의 SQL Analytics로 데이터를 가져올 수 있습니다. 마찬가지로 Polybase를 통해 Azure Synapse로부터 VNet에서 보안이 적용된 Azure Storage로 데이터를 내보낼 수도 있습니다.<br/><br/>Azure Synapse의 VNet 서비스 엔드포인트에 대한 자세한 내용은 [블로그 게시물](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) 또는 [설명서](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요.|
|**자동 성능 모니터링(미리 보기)**|[쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)는 이제 Azure Synapse의 SQL Analytics에서 미리 볼 수 있습니다. 쿼리 저장소는 데이터 웨어하우스의 작업 및 성능을 모니터링하기 위해 쿼리, 쿼리 계획, 런타임 통계 및 쿼리 기록을 추적함으로써 쿼리 성능 문제를 해결하는 데 도움을 주도록 디자인되었습니다. 쿼리 저장소는 다음을 지원하는 내부 저장소 및 DMV(동적 관리 뷰) 세트입니다.<br/><br/>&bull; &nbsp; 상위 리소스 소비 쿼리 식별 및 조정<br/>&bull; &nbsp; 계획되지 않은 워크로드 식별 및 개선<br/>&bull; &nbsp; 쿼리 성능과 통계, 인덱스 또는 시스템 크기(DWU 설정) 변경 사항이 계획에 미치는 영향 평가<br/>&bull; &nbsp; 실행되는 모든 쿼리에 대한 전체 쿼리 텍스트 참조<br/><br/>쿼리 저장소에는 다음과 같은 3가지 실제 저장소가 포함되어 있습니다.<br/>&bull; &nbsp; 실행 계획 정보를 유지하기 위한 계획 저장소<br/>&bull; &nbsp; 실행 통계 정보를 지속하기 위한 런타임 통계 저장소<br/>&bull; &nbsp; 대기 통계 정보를 지속하기 위한 대기 통계 저장소.<br/><br/>Azure Synapse의 SQL Analytics는 이러한 저장소를 자동으로 관리하며, 추가 비용 없이 지난 7일 동안 확인된 무제한 수의 쿼리를 제공합니다. 쿼리 저장소를 사용하도록 설정하는 것은 ALTER DATABASE T-SQL 문을 실행하는 것만큼 간단합니다. <br/>sql ----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------쿼리 저장소에 대한 자세한 내용은 [쿼리 저장소를 사용한 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 문서와 쿼리 저장소 DMV(예: [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest))를 참조하세요. 다음은 출시 소식을 발표하는 [블로그 게시물](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)입니다.|
|**SQL Analytics의 낮은 컴퓨팅 계층**|이제 Azure Synapse의 SQL Analytics에서는 낮은 컴퓨팅 계층을 지원합니다. 고객은 100 cDWU([데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md))로 시작해서 Azure Synapse의 최고 성능, 유연성 및 보안 기능을 경험할 수 있으며, 몇 분 안에 30,000 cDWU까지 확장할 수 있습니다. 2018년 12월 중순부터, 고객은 [지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)의 하위 컴퓨팅 계층에 대한 Gen2 성능 및 유연성을 누릴 수 있으며, 나머지 지역의 경우 2019년 중에 서비스가 지원될 예정입니다.<br/><br/>Microsoft는 차세대 데이터 웨어하우징에 대한 진입점을 낮춰 최적의 평가판 환경이 무엇인지 추측하지 않고 안전한 고성능 데이터 웨어하우스의 모든 이점을 평가하려는 가치 중심 고객에게 문을 열어주고 있습니다. 고객은 현재 500 cDWU 진입점에서 최저 100 cDWU로 시작할 수 있습니다. SQL Analytics는 일시 중지 및 다시 시작 작업을 계속 지원하며 컴퓨팅 시 뛰어난 유연성을 제공합니다. 또한 Gen2에서는 쿼리당 2.5배 더 많은 메모리, 최대 128개의 동시 쿼리 및 [적응형 캐싱](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) 기능과 함께 제한없는 columnstore 스토리지 용량을 지원합니다. 평균적으로 이러한 기능은 같은 가격으로 제공되는 Gen1의 동일한 데이터 웨어하우스 단위에 비해 5배 더 많은 성능을 보장합니다. 지역 중복 백업은 기본 제공되는 데이터 보호 기능과 함께 Gen2 표준입니다. Azure Synapse의 SQL Analytics는 준비가 되면 바로 확장할 수 있습니다.|
|**Columnstore 백그라운드 병합**|기본적으로 Azure SQL Data는 [rowgroup](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)이라는 마이크로 파티션을 사용하여 데이터를 열 형식으로 저장합니다. 경우에 따라 인덱스 작성 또는 데이터 로드 시의 메모리 제한으로 인해 rowgroup이 최적 크기인 1백만 개 행보다 더 적은 수로 압축될 수 있습니다. 또한 삭제로 인해 rowgroup이 조각화될 수도 있습니다. 작아지거나 조각화된 rowgroup은 더 많은 메모리를 사용하고 쿼리 실행 효율성이 떨어질 수 있습니다. 이 릴리스에서는 columnstore 백그라운드 유지 관리 작업이 압축된 작은 rowgroup을 더 큰 rowgroup으로 병합하여 메모리 활용률을 높이고 쿼리 실행 속도를 높입니다.
| | |

## <a name="october-2018"></a>2018년 10월

### <a name="service-improvements"></a>서비스 개선 사항

| 서비스 개선 사항 | 세부 정보 |
| --- | --- |
|**데이터 웨어하우징을 위한 DevOps**|많은 분들이 Azure Synapse의 Synapse SQL에 요청하신 기능이 Visual Studio의 SSDT(SQL Server Data Tools)를 통해 미리 보기로 제공됩니다! 이제 개발자 팀에서는 버전이 제어되는 단일 코드 베이스를 통해 공동으로 작업하고 전 세계의 모든 인스턴스에 변경 내용을 신속하게 배포할 수 있습니다. 관심이 있으세요? 이 기능은 현재 미리 보기로 제공되고 있습니다! [Visual Studio SSDT(SQL Server Data Tools) - 미리 보기 등록 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)을 방문하여 등록할 수 있습니다. 수요가 많기 때문에 고객에게 최상의 환경을 유지하기 위해 미리 보기 사용 인원을 관리하고 있습니다. 미리 보기에 등록하시면 저희 쪽에서 7영업일 내에 상태를 확인할 것입니다.|
|**행 수준 보안 일반 공급**|Azure Synapse의 Synapse SQL은 이제 RLS(행 수준 보안)를 지원하므로 중요한 데이터를 보호하는 강력한 기능이 추가됩니다. RLS가 도입된 덕분에, 행에 액세스할 수 있는 사람을 제어하는 방법과 마찬가지로 테이블의 행에 대한 액세스를 제어하는 보안 정책을 구현할 수 있습니다. RLS를 사용하면 데이터 웨어하우스를 다시 디자인할 필요 없이 세밀한 액세스 제어가 가능합니다. RLS는 액세스 제한 논리가 다른 애플리케이션의 데이터가 아닌 데이터베이스 계층 자체에 있으므로 보안 모델이 전체적으로 간소화됩니다. 또한 RLS는 액세스 제어 관리용 행을 필터링하는 보기를 도입할 필요가 없습니다. 모든 고객은 이 엔터프라이즈급 보안 기능을 추가 비용 없이 사용할 수 있습니다.|
|**고급 Advisor**|데이터 웨어하우스 권장 사항 및 메트릭이 추가되어 Azure Synapse에 있는 Synapse SQL의 고급 튜닝이 간편해졌습니다. Azure Advisor를 통해 고객이 원하는 대로 사용할 수 있는 다음과 같은 고급 성능 권장 사항이 추가로 제공됩니다.<br/><br/>1. 적응형 캐시 - 캐시 사용률을 최적화하기 위해 확장할 때를 알려줍니다.<br/>2. 테이블 배포 - 데이터 이동을 줄이고 워크로드 성능을 향상하기 위해 테이블을 복제할 때를 결정합니다.<br/>3. Tempdb - tempdb 경합을 줄이기 위해 리소스 클래스를 확장하고 구성할 때를 파악합니다.<br/><br/>개요 블레이드의 거의 실시간 메트릭을 위한 향상된 사용 지정 가능한 모니터링 차트 등 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)와 데이터 웨어하우스 메트릭이 더욱 긴밀하게 통합되었습니다. 사용량을 모니터링하거나 데이터 웨어하우스 권장 사항의 유효성을 검사하고 이 권장 사항을 적용할 때 Azure Monitor 메트릭에 액세스하기 위해 더 이상 데이터 웨어하우스 개요 블레이드에서 나갈 필요가 없습니다. 또한 tempdb, 적응형 캐시 사용률 등 성능 권장 사항을 보완하는 데 사용할 수 있는 새로운 메트릭이 있습니다.|
|**Advisor가 통합된 고급 튜닝**|데이터 웨어하우스 권장 사항 및 메트릭을 추가하고 Azure Advisor와 Azure Monitor가 통합된 환경을 제공하는 포털 개요 블레이드를 다시 디자인하여 Azure Synapse의 고급 튜닝이 간편해졌습니다.|
|**ADR(가속 데이터베이스 복구)**|Azure Synapse ADR(가속 데이터베이스 복구)이 퍼블릭 미리 보기로 제공됩니다. ADR은 현재 복구 프로세스를 처음부터 다시 설계하여 데이터베이스, 특히 장기 실행 트랜잭션이 있는 데이터베이스의 가용성을 크게 향상하는 새로운 SQL Server 엔진입니다. ADR의 가장 큰 장점은 빠르고 일관적인 데이터베이스 복구와 즉각적인 트랜잭션 롤백입니다.|
|**Azure Monitor 리소스 로그**|이제 Azure Synapse는 Azure Monitor 리소스 로그와 직접 통합하여 분석 워크로드에 대한 향상된 인사이트를 얻을 수 있습니다. 이 새 기능을 통해 개발자는 오랫동안 워크로드 동작을 분석하고 쿼리 최적화 또는 용량 관리에 대한 합리적 결정을 내릴 수 있습니다. [Azure Monitor 리소스 로그](../../azure-monitor/platform/data-platform.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#logs)를 통해 데이터 웨어하우스 워크로드에 대한 추가 정보를 제공하는 외부 로깅 프로세스를 도입했습니다. 이제 단추를 한 번만 클릭하면 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용하여 기록 쿼리 성능 문제 해결 기능에 대한 리소스 로그를 구성할 수 있습니다. Azure Monitor 리소스 로그는 감사용으로 스토리지 계정에 로그를 저장하는 방식을 통한 사용자 지정 가능 보존 기간, 실시간에 가까운 원격 분석 인사이트 확인을 위해 로그를 이벤트 허브로 스트리밍하는 기능, 그리고 Log Analytics에서 로그 쿼리를 사용하여 로그를 분석하는 기능을 지원합니다. 리소스 로그는 Azure Synapse에 있는 SQL Analytics의 가장 일반적으로 사용되는 성능 문제 해결 DMV에 상응하는 데이터 웨어하우스의 원격 분석 보기로 구성됩니다. 이 초기 릴리스의 경우 다음 시스템 동적 관리 뷰에 대한 보기가 설정되었습니다.<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**Columnstore 메모리 관리**|압축된 열 저장소 행 그룹의 수가 증가하면 이러한 행 그룹에 대한 내부 열 세그먼트 메타데이터를 관리하는 데 필요한 메모리도 증가합니다.  결과적으로 일부 Columnstore DMV(동적 관리 뷰)에 대해 실행되는 쿼리 및 쿼리 성능이 저하될 수 있습니다.  이번 릴리스에서는 이러한 경우에 대한 내부 메타데이터 크기가 최적화되어 이러한 쿼리의 환경 및 성능이 개선되도록 수정되었습니다.|
|**Azure Data Lake Storage Gen2 통합(GA)**|이제 Synapse Analytics가 Azure Data Lake Storage Gen2와 기본적으로 통합됩니다. 고객은 ABFS에서 외부 테이블을 사용하여 Synapse SQL 풀로 데이터를 로드할 수 있습니다. 이 기능을 통해 고객은 데이터 레이크를 Data Lake Storage Gen2에 통합할 수 있습니다.|
|**주목할 만한 버그**|DW2000 등의 데이터 웨어하우스에 있는 소형 리소스 클래스에서 CETAS to Parquet 실패 - 이 픽스는 Create External Table As to Parquet 코드 경로에서 null 참조를 올바르게 식별합니다.<br/><br/>일부 CTAS 작업에서 ID 열 값이 손실될 수 있음 - 다른 테이블로 CTAS 할 때 ID 열 값이 유지되지 않을 수 있습니다. [블로그](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)에서 보고되었습니다.<br/><br/>쿼리가 실행되는 동안 세션이 종료되는 일부 경우에 발생하는 내부 오류 - 쿼리가 계속 실행 중일 때 세션이 종료되는 경우 이 수정 프로그램은 InvalIDOperationException을 트리거합니다.<br/><br/>(2018년 11월에 배포됨) Polybase를 사용하여 ADLS(Gen1)에서 여러 작은 파일을 로드하려고 하는 경우 고객에게 최적이 아닌 성능 문제가 발생했습니다. - 시스템 성능은 AAD 보안 토큰 유효성 검사 동안 병목 상태가 되었습니다. 보안 토큰의 캐싱을 사용하여 성능 문제를 완화했습니다. |
| | |

## <a name="next-steps"></a>다음 단계

- [SQL 풀 만들기](create-data-warehouse-portal.md)

## <a name="more-information"></a>자세한 정보

- [블로그 - Azure Synapse Analytics](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [고객 자문 팀 블로그](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [고객 성공 사례](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [비디오](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 용어집](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

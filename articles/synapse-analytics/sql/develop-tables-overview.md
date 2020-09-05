---
title: Synapse SQL을 사용 하 여 테이블 디자인
description: Synapse SQL의 테이블 디자인에 대해 소개 합니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 3bf180c2b70a686879082888e45e67936cdbec67
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799233"
---
# <a name="design-tables-using-synapse-sql"></a>Synapse SQL을 사용 하 여 테이블 디자인

이 문서에는 SQL 풀 및 SQL 주문형 (미리 보기)을 사용 하 여 테이블을 디자인 하기 위한 주요 개념이 포함 되어 있습니다.  

[SQL 주문형 (미리 보기)](on-demand-workspace-overview.md) 는 data lake의 데이터에 대 한 쿼리 서비스입니다. 데이터 수집을 위한 로컬 저장소가 없습니다. [Sql 풀](best-practices-sql-pool.md) 은 Synapse sql을 사용 하는 경우 프로 비전 되는 분석 리소스의 컬렉션을 나타냅니다. SQL 풀의 크기는 DWU(Data Warehousing Unit)로 결정됩니다.

다음 표에서는 SQL 풀과 관련 된 항목 및 주문형 SQL을 보여 줍니다.

| 항목                                                        | SQL 풀 | SQL 주문형 |
| ------------------------------------------------------------ | ------------------ | ----------------------- |
| [테이블 범주 확인](#determine-table-category)        | 예                | 예                      |
| [스키마 이름](#schema-names)                                | 예                | 예                     |
| [테이블 이름](#table-names)                                  | 예                | 예                      |
| [테이블 지속성](#table-persistence)                      | 예                | 예                      |
| [일반 테이블](#regular-table)                              | 예                | 예                      |
| [임시 테이블](#temporary-table)                          | 예                | 예                     |
| [외부 테이블](#external-table)                            | 예                | 예                     |
| [데이터 형식](#data-types)                                    | 예                | 예                     |
| [분산 테이블](#distributed-tables)                    | 예                | 예                      |
| [해시 분산 테이블](#hash-distributed-tables)          | 예                | 예                      |
| [복제된 테이블](#replicated-tables)                      | 예                | 예                      |
| [라운드 로빈 테이블](#round-robin-tables)                    | 예                | 예                      |
| [테이블에 대한 일반적인 분산 방법](#common-distribution-methods-for-tables) | 예                | 예                      |
| [파티션](#partitions)                                    | 예                | 예                     |
| [columnstore 인덱스](#columnstore-indexes)                  | 예                | 예                      |
| [통계](#statistics)                                    | 예                | 예                     |
| [기본 키 및 고유 키](#primary-key-and-unique-key)    | 예                | 예                      |
| [테이블을 만드는 명령](#commands-for-creating-tables) | 예                | 예                      |
| [원본 데이터를 데이터 웨어하우스에 맞춤](#align-source-data-with-the-data-warehouse) | 예                | 예                      |
| [지원 되지 않는 테이블 기능](#unsupported-table-features)    | 예                | 예                      |
| [테이블 크기 쿼리](#table-size-queries)                    | 예                | 예                      |

## <a name="determine-table-category"></a>테이블 범주 확인

[스타 스키마](https://en.wikipedia.org/wiki/Star_schema)는 데이터를 팩트 및 차원 테이블로 구성합니다. 일부 테이블은 팩트 또는 차원 테이블로 이동 하기 전에 통합 또는 준비 데이터에 사용 됩니다. 테이블을 디자인할 때 테이블 데이터가 팩트, 차원 또는 통합 테이블에 속하는지를 결정합니다. 이 결정은 적절한 테이블 구조 및 배포를 알려줍니다.

- **팩트 테이블** 은 일반적으로 트랜잭션 시스템에서 생성 된 다음 데이터 웨어하우스로 로드 되는 양적 데이터를 포함 합니다. 예를 들어 소매점에서 판매 트랜잭션을 매일 생성한 다음 분석을 위해 데이터 웨어하우스의 팩트 테이블에 데이터를 로드합니다.

- **차원 테이블**에는 변경될 수 있지만 일반적으로 드물게 변경되는 특성 데이터가 있습니다. 예를 들어 고객의 이름과 주소는 차원 테이블에 저장되고, 고객 프로필이 변경될 때만 업데이트됩니다. 큰 팩트 테이블의 크기를 최소화 하기 위해 고객의 이름과 주소가 팩트 테이블의 모든 행에 있을 필요는 없습니다. 대신, 팩트 테이블과 차원 테이블에서 고객 ID를 공유할 수 있습니다. 쿼리는 두 테이블을 조인하여 고객 프로필과 트랜잭션을 연결할 수 있습니다.

- **통합 테이블**에서는 데이터를 통합하거나 준비할 수 있습니다. 통합 테이블을 일반 테이블, 외부 테이블 또는 임시 테이블로 만들 수 있습니다. 예를 들어 준비 테이블에 데이터를 로드하고 준비 중인 데이터에 대한 변환을 수행한 다음 프로덕션 테이블에 데이터를 삽입할 수 있습니다.

## <a name="schema-names"></a>스키마 이름

스키마는 비슷한 방식으로 사용 되는 개체를 함께 그룹화 하는 좋은 방법입니다. 다음 코드에서는 wwi 라는 [사용자 정의 스키마](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 를 만듭니다.

```sql
CREATE SCHEMA wwi;
```

## <a name="table-names"></a>테이블 이름

온-프레미스 솔루션에서 SQL 풀로 여러 데이터베이스를 마이그레이션하는 경우 가장 좋은 방법은 모든 팩트, 차원 및 통합 테이블을 하나의 SQL 풀 스키마로 마이그레이션하는 것입니다. 예를 들어 [WideWorldImportersDW](/sql/samples/wide-world-importers-dw-database-catalog?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 샘플 데이터 웨어하우스의 모든 테이블을 wwi 라는 하나의 스키마 내에 저장할 수 있습니다.

SQL 풀에서 테이블의 조직을 표시 하려면 팩트, dim 및 int를 테이블 이름의 접두사로 사용할 수 있습니다. 다음 표에서는 WideWorldImportersDW에 대 한 일부 스키마 및 테이블 이름을 보여 줍니다.  

| WideWorldImportersDW 테이블  | 테이블 유형입니다. | SQL 풀 |
|:-----|:-----|:------|:-----|
| City | 차원 | wwi.DimCity |
| 주문 | 팩트 | wwi.FactOrder |

## <a name="table-persistence"></a>테이블 지속성

테이블은 데이터를 Azure Storage에 영구적으로 저장 하거나 데이터 웨어하우스의 외부에 있는 데이터 저장소 또는 Azure Storage에 영구적으로 저장 합니다.

### <a name="regular-table"></a>일반 테이블

일반 테이블의 데이터는 데이터 웨어하우스의 일부로 Azure Storage에 저장됩니다. 테이블과 데이터는 세션이 열려 있는지 여부에 상관 없이 유지 됩니다.  아래 예제에서는 두 개의 열이 있는 일반 테이블을 만듭니다.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>임시 테이블

임시 테이블은 세션 기간 동안만 유지됩니다. 임시 테이블을 사용 하 여 다른 사용자가 임시 결과를 볼 수 없도록 할 수 있습니다. 또한 임시 테이블을 사용 하면 정리 필요성이 줄어듭니다.  임시 테이블은 로컬 저장소를 활용 하 고 SQL 풀에서 더 빠른 성능을 제공할 수 있습니다.  

SQL 주문형 요청은 임시 테이블을 지원 합니다. 그러나 임시 테이블에서 선택할 수 있지만 저장소의 파일에 조인할 수 없으므로 사용이 제한 됩니다.

자세한 내용은 [임시 테이블](develop-tables-temporary.md)을 참조하세요.

### <a name="external-table"></a>외부 테이블

[외부 테이블](develop-tables-external-tables.md) 은 Azure Storage blob 또는 Azure Data Lake Storage에 있는 데이터를 가리킵니다.

[CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문을 사용 하 여 외부 테이블에서 SQL 풀로 데이터를 가져옵니다. 로드 자습서는 [PolyBase를 사용 하 여 Azure blob storage에서 데이터 로드를](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)참조 하세요.

주문형 SQL의 경우 [CETAS](develop-tables-cetas.md) 를 사용 하 여 쿼리 결과를 Azure Storage의 외부 테이블에 저장할 수 있습니다.

## <a name="data-types"></a>데이터 형식

SQL 풀은 가장 일반적으로 사용 되는 데이터 형식을 지원 합니다. 지원되는 데이터 형식의 목록은 CREATE TABLE 문의 [CREATE TABLE 참조의 데이터 형식](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes)을 참조하세요. 데이터 형식을 사용 하는 방법에 대 한 자세한 내용은 [데이터 형식](../sql/develop-tables-data-types.md)을 참조 하세요.

## <a name="distributed-tables"></a>분산 테이블

SQL 풀의 기본 기능은 [배포](../sql-data-warehouse/massively-parallel-processing-mpp-architecture.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#distributions)를 통해 테이블에 저장 하 고 작업할 수 있는 방법입니다.  SQL 풀은 데이터를 배포 하는 세 가지 방법을 지원 합니다.

- 라운드 로빈(기본값)
- Hash
- 복제됨

### <a name="hash-distributed-tables"></a>해시 분산 테이블

해시 분산 테이블은 배포 열의 값에 따라 행을 배포 합니다. 해시 분산 테이블은 큰 테이블의 쿼리에 대해 고성능을 얻기 위해 설계 되었습니다. 배포 열을 선택할 때 고려해 야 할 몇 가지 요소가 있습니다.

자세한 내용은 [분산 테이블에 대한 디자인 지침](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)을 참조하세요.

### <a name="replicated-tables"></a>복제된 테이블

복제된 테이블에는 모든 컴퓨팅 노드에서 사용할 수 있는 테이블의 전체 복사본이 있습니다. 복제 된 테이블에 대 한 조인은 데이터 이동이 필요 하지 않으므로 복제 된 테이블에서 쿼리가 빠르게 실행 됩니다. 복제에는 추가 저장소가 필요 하지만 대형 테이블에는 실용적이 지 않습니다.

자세한 내용은 [복제된 테이블에 대한 디자인 지침](../sql-data-warehouse/design-guidance-for-replicated-tables.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)을 참조하세요.

### <a name="round-robin-tables"></a>라운드 로빈 테이블

라운드 로빈 테이블은 테이블 행을 모든 분산에서 균일하게 배포합니다. 행은 무작위로 분산됩니다. 라운드 로빈 테이블에 대한 데이터 로드는 빠릅니다.  그러나 쿼리에는 다른 배포 방법 보다 더 많은 데이터 이동이 필요할 수 있습니다.

자세한 내용은 [분산 테이블에 대한 디자인 지침](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)을 참조하세요.

### <a name="common-distribution-methods-for-tables"></a>테이블에 대한 일반적인 분산 방법

테이블 범주는 종종 테이블 배포에 대 한 최적의 옵션을 결정 합니다.

| 테이블 범주 | 권장 배포 옵션 |
|:---------------|:--------------------|
| 팩트           | 클러스터형 columnstore 인덱스와 함께 해시 배포를 사용합니다. 동일한 배포 열에서 두 해시 테이블을 조인하면 성능이 향상됩니다. |
| 차원      | 작은 테이블에는 복제를 사용합니다. 테이블이 너무 커서 각 컴퓨팅 노드에 저장할 수 없는 경우 해시 분산을 사용합니다. |
| 준비        | 준비 테이블에는 라운드 로빈을 사용합니다. CTAS를 사용하면 빠르게 로드됩니다. 데이터가 준비 테이블에 있으면 INSERT ...를 사용 합니다. 데이터를 프로덕션 테이블로 이동 하려면 선택 합니다. |

## <a name="partitions"></a>파티션

SQL 풀에서 분할 된 테이블은 데이터 범위에 따라 테이블 행에 대 한 작업을 저장 하 고 실행 합니다. 예를 들어 테이블을 일, 월 또는 연도별로 분할할 수 있습니다. 쿼리 검색을 파티션 내의 데이터로 제한하는 파티션 제거를 통해 쿼리 성능을 향상시킬 수 있습니다.

파티션 전환을 통해 데이터를 유지 관리할 수도 있습니다. SQL 풀의 데이터는 이미 배포 되었으므로 너무 많은 파티션이 쿼리 성능을 저하 시킬 수 있습니다. 자세한 내용은 [분할 지침](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)을 참조하세요.  

> [!TIP]
> 비어 있지 않은 테이블 파티션으로 파티션이 전환 될 때 기존 데이터를 잘라낼 경우 [ALTER table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문에 TRUNCATE_TARGET 옵션을 사용 하는 것이 좋습니다.

아래 코드는 변환 된 일일 데이터를 SalesFact 파티션으로 전환 하 고 기존 데이터를 덮어씁니다.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

SQL 주문형에서 쿼리에서 읽을 파일/폴더 (파티션)를 제한할 수 있습니다. 경로 별로 분할은 [저장소 파일 쿼리](develop-storage-files-overview.md)에 설명 된 filepath 및 fileinfo 함수를 사용 하 여 지원 됩니다. 다음 예제에서는 2017 년에 대 한 데이터가 포함 된 폴더를 읽습니다.

```sql
SELECT
    nyc.filepath(1) AS [year],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
GROUP BY
    nyc.filepath(1),
    payment_type
ORDER BY
    nyc.filepath(1),
    payment_type
```

## <a name="columnstore-indexes"></a>columnstore 인덱스

기본적으로 SQL 풀은 클러스터형 columnstore 인덱스로 테이블을 저장 합니다. 이러한 형태의 데이터 스토리지는 대형 테이블에서 데이터 압축률과 쿼리 성능이 높습니다.  일반적으로 클러스터형 columnstore 인덱스가 가장 좋은 옵션이지만 클러스터형 인덱스 또는 힙이 적절한 스토리지 구조인 경우도 있습니다.  

> [!TIP]
> 힙 테이블은 최종 테이블로 변환 된 준비 테이블과 같은 임시 데이터를 로드 하는 데 특히 유용할 수 있습니다.

columnstore 기능 목록은 [columnstore 인덱스의 새로운 기능](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 참조하세요. columnstore 인덱스 성능을 향상하려면 [columnstore 인덱스의 행 그룹 품질 최대화](../sql/data-load-columnstore-compression.md)를 참조하세요.

## <a name="statistics"></a>통계


쿼리 최적화 프로그램은 쿼리 실행 계획을 만들 때 열 수준 통계를 사용합니다. 쿼리 성능을 향상 시키려면 특히 쿼리 조인에 사용 되는 열, 개별 열에 대 한 통계를 포함 하는 것이 중요 합니다. Synapse SQL은 통계의 자동 생성을 지원 합니다. 

통계 업데이트는 자동으로 발생 하지 않습니다. 많은 행을 추가하거나 변경한 후에는 통계를 업데이트합니다. 예를 들어 로드 후 통계를 업데이트 합니다. 추가 정보는 [통계 지침](develop-tables-statistics.md) 문서에 나와 있습니다.

## <a name="primary-key-and-unique-key"></a>기본 키 및 고유 키

기본 키는 비클러스터형이 모두 사용 되는 경우에만 지원 됩니다.  UNIQUE 제약 조건은 적용 되지 않은 경우에만 지원 됩니다.  자세한 내용은 [SQL 풀 테이블 제약 조건](../sql-data-warehouse/sql-data-warehouse-table-constraints.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 문서를 참조 하세요.

## <a name="commands-for-creating-tables"></a>테이블을 만드는 명령

테이블을 새로운 빈 테이블로 만들 수 있습니다. 테이블을 만들고 select 문의 결과로 채울 수도 있습니다. 다음은 테이블을 만드는 T-SQL 명령입니다.

| T-SQL 문 | 설명 |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 모든 테이블 열과 옵션을 정의하여 빈 테이블을 만듭니다. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 외부 테이블을 만듭니다. 테이블의 정의는 SQL 풀에 저장 됩니다. 테이블 데이터는 Azure Blob storage 또는 Azure Data Lake Storage에 저장 됩니다. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | select 문의 결과로 새 테이블을 채웁니다. 테이블 열과 데이터 형식은 select 문의 결과를 기반으로 합니다. 데이터를 가져오기 위해 이 문은 외부 테이블에서 선택할 수 있습니다. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | select 문의 결과를 외부 위치로 내보내 새 외부 테이블을 만듭니다.  이 위치는 Azure Blob storage 또는 Azure Data Lake Storage입니다. |

## <a name="align-source-data-with-the-data-warehouse"></a>원본 데이터를 데이터 웨어하우스와 맞춤

데이터 웨어하우스 테이블은 다른 데이터 원본의 데이터를 로드하여 채워집니다. 성공적으로 로드 하려면 원본 데이터의 열 수와 데이터 형식이 데이터 웨어하우스의 테이블 정의와 일치 해야 합니다.

> [!NOTE]
> 맞출 데이터를 가져오는 것이 테이블 디자인의 가장 어려운 부분일 수 있습니다.

데이터를 여러 데이터 저장소에서 가져오는 경우 데이터를 데이터 웨어하우스로 이식 하 여 통합 테이블에 저장할 수 있습니다. 데이터가 통합 테이블에 있으면 SQL 풀의 기능을 사용 하 여 변환 작업을 구현할 수 있습니다. 데이터가 준비되면 프로덕션 테이블에 삽입할 수 있습니다.

## <a name="unsupported-table-features"></a>지원되지 않는 테이블 기능

SQL 풀은 다른 데이터베이스에서 제공 하는 테이블 기능을 대부분 지원 합니다.  다음 목록에서는 SQL 풀에서 지원 되지 않는 일부 테이블 기능을 보여 줍니다.

- 외래 키, check [테이블 제약 조건](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [계산 열](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [인덱싱된 뷰](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [시퀀스](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [스파스 열](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- 서로게이트 키, [id](../sql-data-warehouse/sql-data-warehouse-tables-identity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 를 사용 하 여 구현
- [동의어](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [트리거](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [고유 인덱스](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [사용자 정의 형식](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>테이블 크기 쿼리

각 60 배포판에서 테이블에 사용 되는 공간 및 행을 식별 하는 한 가지 간단한 방법은 [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용 하는 것입니다.

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

DBCC 명령을 사용 하는 것은 매우 제한적입니다.  DMV(동적 관리 뷰)는 DBCC 명령보다 자세한 내용을 표시합니다. 아래에서 보기를 만들어 시작 합니다.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

>[!TIP]
> Synapse SQL의 성능 향상을 위해 영구 사용자 테이블에서 **pdw_table_mappings** 대신 **pdw_permanent_table_mappings** 를 사용 하는 것이 좋습니다. 자세한 내용은 **[pdw_permanent_table_mappings &#40;transact-sql&#41;](/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=azure-sqldw-latest)** 을 참조 하십시오.

### <a name="table-space-summary"></a>테이블 공간 요약

이 쿼리는 테이블에 의해 행 및 공간을 반환합니다.  테이블 공간 요약을 사용 하면 가장 큰 테이블이 되는 테이블을 확인할 수 있습니다. 또한 라운드 로빈, 복제 또는 해시 분산 인지 여부도 확인할 수 있습니다.  해시 분산 테이블의 경우 쿼리에서 배포 열을 보여 줍니다.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>배포 유형별 테이블 공간

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>인덱스 유형별 테이블 공간

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>배포 공간 요약

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>다음 단계

데이터 웨어하우스에 대한 테이블을 만든 후 다음 단계는 테이블에 데이터를 로드하는 것입니다.  로드 자습서는 [SQL 풀로 데이터 로드](../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#load-the-data-into-sql-pool)를 참조 하세요.

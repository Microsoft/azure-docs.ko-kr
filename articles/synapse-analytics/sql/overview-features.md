---
title: Synapse SQL의 T-SQL 기능 차이점
description: Synapse SQL에서 사용할 수 있는 Transact-SQL 기능 목록입니다.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: ce7238aaa10b1e73f7fa3c061045f47cbc472e23
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378698"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Azure Synapse SQL에서 지원되는 Transact-SQL 기능

Azure Synapse SQL은 T-SQL 언어를 사용하여 데이터를 쿼리하고 분석할 수 있는 빅 데이터 분석 서비스입니다. SQL Server 및 Azure SQL Database에 사용되는 SQL 언어의 표준 ANSI 호환 언어를 데이터 분석에 사용할 수 있습니다. 

Transact-SQL 언어는 서버리스 Synapse SQL 서버에서 사용되며 프로비저닝된 모델은 다른 개체를 참조할 수 있고 지원되는 일련의 기능에 약간의 차이가 있습니다. 이 페이지에서는 Synapse SQL의 소비 모델 간의 개략적인 Transact-SQL 언어 차이를 알아볼 수 있습니다.

## <a name="database-objects"></a>데이터베이스 개체

Synapse SQL의 소비 모델을 사용하면 다양한 데이터베이스 개체를 사용할 수 있습니다. 다음 표에서는 지원되는 개체 유형을 비교하여 보여줍니다.

|   | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- | --- |
| **테이블** | [예](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 아니요, 서버리스 모델은 [Azure Storage](#storage-options)에 배치된 외부 데이터만 쿼리할 수 있습니다. |
| **뷰** | [예](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). 보기는 프로비저닝된 모델에서 사용 가능한 [쿼리 언어 요소](#query-language)를 사용할 수 있습니다. | [예](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). 보기는 서버리스 모델에서 사용 가능한 [쿼리 언어 요소](#query-language)를 사용할 수 있습니다. |
| **스키마** | [예](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [예](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **임시 테이블** | [예](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 예 |
| **절차** | [예](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예 |
| **함수** | [예](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예, 인라인 테이블 반환 함수만 지원됩니다. |
| **트리거** | 예 | 예 |
| **외부 테이블** | [예](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). 지원되는 [데이터 형식](#data-formats)을 참조하세요. | [예](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). 지원되는 [데이터 형식](#data-formats)을 참조하세요. |
| **캐싱 쿼리** | 예, 여러 양식(SSD 기반 캐싱, 메모리 내, 결과 집합 캐싱). 또한 구체화된 뷰가 지원됩니다. | 예 |
| **테이블 변수** | [아니요](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), 임시 테이블을 사용합니다. | 예 |
| **[테이블 배포](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | 예 | 예 |
| **[테이블 인덱스](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | 예 | 예 |
| **[테이블 파티션](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | 예 | 예 |
| **[통계](develop-tables-statistics.md)**            | 예 | 예 |
| **[작업 관리, 리소스 클래스 및 동시성 제어](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | 예    | 예 |

## <a name="query-language"></a>쿼리 언어

Synapse SQL에 사용되는 쿼리 언어는 소비 모델에 따라 지원되는 기능이 다를 수 있습니다. 다음 표에는 Transact-SQL 언어의 가장 중요한 쿼리 언어 차이점이 간략히 설명되어 있습니다.

|   | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- | --- |
| **SELECT 문** | 예. Transact-SQL 쿼리 절인 [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)는 지원되지 않습니다. | 예. Transact-SQL 쿼리 절인 [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 쿼리 힌트는 지원되지 않습니다. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) 및 [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 시스템 개체(외부 데이터 아님)를 쿼리할 수 있습니다. |
| **INSERT 문** | 예 | 예 |
| **UPDATE 문** | 예 | 예 |
| **DELETE 문** | 예 | 예 |
| **MERGE 문** | 예 | 예 |
| **[트랜잭션](develop-transactions.md)** | 예 | 예 |
| **[레이블](develop-label.md)** | 예 | 예 |
| **데이터 로드** | 예. 기본 설정 유틸리티는 [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문이지만 데이터 로드를 위한 BULK 로드(BCP)와 [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)가 시스템에서 모두 지원됩니다. | 예 |
| **데이터 내보내기** | 예. [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용합니다. | 예. [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용합니다. |
| **유형** | 예, [커서](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text 및 image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [공간 형식](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 제외한 모든 Transact-SQL 형식 | 예, [커서](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text 및 image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [공간 형식](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 테이블 형식을 제외한 모든 Transact-SQL 형식 |
| **데이터베이스 간 쿼리** | 예 | 예, [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 문을 포함합니다. |
| **기본 제공 함수(분석)** | 예, [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 모든 Transact-SQL [분석](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), 변환, [날짜 및 시간](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), 논리, [수학](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 함수 | 예, 모든 Transact-SQL [분석](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), 변환, [날짜 및 시간](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), 논리, [수학](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 함수 |
| **기본 제공 함수(텍스트)** | 예. [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 모든 Transact-SQL [문자열 ](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 데이터 정렬 함수 | 예. 모든 Transact-SQL [문자열](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 데이터 정렬 함수 |
| **기본 제공 테이블 값 함수** | 예, [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 [Transact-SQL 행 집합 함수](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions) | 예, [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 [Transact-SQL 행 집합 함수](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions)  |
| **집계** |  [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 Transact-SQL 기본 제공 집계 | [STRING\_AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 Transact-SQL 기본 제공 집계 |
| **연산자** | 예, [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 모든 [Transact-SQL 연산자](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예, 모든 [Transact-SQL 연산자](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **흐름 제어** | 예. [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)를 제외한 모든 [Transact-SQL 흐름 제어 문](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예. `WHILE (...)` 조건에서 [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 및 SELECT 쿼리를 제외한 모든 [Transact-SQL 흐름 제어 문](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **DDL 문(CREATE, ALTER, DROP)** | 예. 지원되는 개체 유형에 적용 가능한 모든 Transact-SQL DDL 문 | 예. 지원되는 개체 유형에 적용 가능한 모든 Transact-SQL DDL 문 |

## <a name="security"></a>보안

Synapse SQL을 사용하면 기본 제공 보안 기능을 사용하여 데이터를 보호하고 액세스를 제어할 수 있습니다. 다음 표에는 Synapse SQL 소비 모델 간의 개략적인 차이를 비교하여 보여줍니다.

|   | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- | --- |
| **로그인** | 해당 없음(포함된 사용자만 데이터베이스에서 지원됨) | 예 |
| **사용자** |  해당 없음(포함된 사용자만 데이터베이스에서 지원됨) | 예 |
| **[포함된 사용자](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | 예. **참고:** Azure AD 사용자 한 명만 무제한 관리자가 될 수 있음 | 예 |
| **SQL 사용자 이름/암호 인증**| 예 | 예 |
| **AAD(Azure Active Directory) 인증**| 예, Azure AD 사용자 | 예, Azure AD 로그인 및 사용자 |
| **스토리지 AAD(Azure Active Directory) 통과 인증** | 예 | 예 |
| **스토리지 SAS 토큰 인증** | 예 | 예, 인스턴스 수준 [자격 증명](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 또는 [외부 데이터 원본](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)에서 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용합니다. |
| **스토리지 액세스 키 인증** | 예, [외부 데이터 원본](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)에서 [데이터베이스 범위 자격 증명](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용합니다. | 예 |
| **스토리지 [관리 ID](../security/synapse-workspace-managed-identity.md) 인증** | 예, [관리되는 서비스 ID 자격 증명](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용합니다. | 예, `Managed Identity` 자격 증명을 사용합니다. |
| **스토리지 애플리케이션 ID 인증** | [예](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예 |
| **권한 - 개체 수준** | 예, 사용자 권한을 부여, 거부 및 해지하는 기능을 포함합니다. | 예, 지원되는 시스템 개체의 사용자/로그인 권한을 부여, 거부 및 해지하는 기능을 포함합니다. |
| **권한 - 스키마 수준** | 예, 스키마의 사용자/로그인 권한을 부여, 거부 및 해지하는 기능을 포함합니다. | 예, 스키마의 사용자/로그인 권한을 부여, 거부 및 해지하는 기능을 포함합니다. |
| **권한 - [데이터베이스 수준](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | 예 | 예 |
| **권한 - [서버 수준](/sql/relational-databases/security/authentication-access/server-level-roles)** | 예 | 예, sysadmin 및 기타 서버 역할이 지원됩니다. |
| **권한 - [열 수준 보안](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | 예 | 예 |
| **역할/그룹** | 예(데이터베이스 범위) | 예(서버 및 데이터베이스 범위 모두) |
| **보안 &amp; ID 함수** | 몇 가지 Transact-SQL 보안 함수 및 연산자: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | 몇 가지 Transact-SQL 보안 함수 및 연산자: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` 및 `REVERT`. 보안 함수는 외부 데이터를 쿼리하는 데 사용할 수 없습니다(쿼리에 사용할 수 있는 변수에 결과를 저장).  |
| **DATABASE SCOPED CREDENTIAL** | 예 | 예 |
| **SERVER SCOPED CREDENTIAL** | 예 | 예 |
| **행 수준 보안** | [예](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | 예 |
| **TDE(투명한 데이터 암호화)** | [예](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | 예 | 
| **데이터 검색 및 분류** | [예](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 예 |
| **취약성 평가** | [예](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 예 |
| **Advanced Threat Protection** | [예](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **감사** | [예](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 예 |
| **[방화벽 규칙](../security/synapse-workspace-ip-firewall.md)**| 예 | 예 |
| **[프라이빗 엔드포인트](../security/synapse-workspace-managed-private-endpoints.md)**| 예 | 예 |

SQL 풀 및 주문형 SQL은 표준 Transact-SQL 언어를 사용하여 데이터를 쿼리합니다. 자세한 차이점은 [Transact-SQL 언어 참조](/sql/t-sql/language-reference)에서 참조하세요.

## <a name="tools"></a>도구

다양한 도구를 사용하여 Synapse SQL에 연결하여 데이터를 쿼리할 수 있습니다.

|   | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- | --- |
| **Synapse Studio** | 예, SQL 스크립트 | 예, SQL 스크립트 |
| **Power BI** | 예 | [예](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | 예 | 예 |
| **Azure Data Studio** | 예 | 예, 버전 1.18.0 이상 SQL 스크립트 및 SQL Notebook이 지원됩니다. |
| **SQL Server Management Studio** | 예 | 예, 버전 18.5 이상 |

> [!NOTE]
> SSMS를 사용하여 SQL 주문형(미리 보기) 및 쿼리에 연결할 수 있습니다. 버전 18.5부터 부분적으로 지원되며, 연결 및 쿼리에만 사용할 수 있습니다.

대부분의 애플리케이션은 표준 Transact-SQL 언어를 사용하여 Synapse SQL의 서버리스 및 프로비저닝된 소비 모델을 모두 쿼리할 수 있습니다.

## <a name="storage-options"></a>스토리지 옵션

분석되는 데이터는 다양한 스토리지 유형에 저장할 수 있습니다. 다음 표에는 사용 가능한 모든 스토리지 옵션이 나열되어 있습니다.

|   | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- | --- |
| **내부 스토리지** | 예 | 예 |
| **Azure Data Lake v2** | 예 | 예 |
| **Azure Blob Storage** | 예 | 예 |
| **Azure CosmosDB 분석 스토리지** | 예 | 예, [Synapse 링크](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 사용([제어된 미리 보기](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)에서) |

## <a name="data-formats"></a>데이터 형식

분석되는 데이터는 다양한 스토리지 형식으로 저장할 수 있습니다. 다음 표에는 분석할 수 있는 모든 데이터 형식이 나열되어 있습니다.

|   | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- | --- |
| **구분됨** | [예](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [예](query-single-csv-file.md) |
| **CSV** | 예(다중 문자 구분 기호는 지원되지 않음) | [예](query-single-csv-file.md) |
| **Parquet** | [예](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [예](query-parquet-files.md), [중첩 형식](query-parquet-nested-types.md) 파일을 포함합니다. |
| **Hive ORC** | [예](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예 |
| **Hive RC** | [예](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | 예 |
| **JSON** | 예 | [예](query-json-files.md) |
| **Avro** | 예 | 예 |
| **[Delta-lake](https://delta.io/)** | 예 | 예 |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | 예 | 예 |

## <a name="next-steps"></a>다음 단계
SQL 풀 및 주문형 SQL의 모범 사례에 대한 추가 정보는 다음 문서를 참조하세요.

- [SQL 풀에 대한 모범 사례](best-practices-sql-pool.md)
- [주문형 SQL에 대한 모범 사례](best-practices-sql-on-demand.md)

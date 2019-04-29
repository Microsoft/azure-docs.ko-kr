---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 9월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474977"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 9월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 9월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>SQL Data Warehouse Gen2의 더욱 저렴해진 새 진입점
[Microsoft](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/)는 2018년 4월에 성능/컴퓨팅 기능은 기존 세대의 5배이고 동시 처리 가능 작업 수는 4배이며 스토리지도 무제한으로 사용 가능한 Azure SQL Data Warehouse Gen2를 발표했습니다. Gigaom의 [Data Warehouse in the cloud Benchmark](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)에 언급된 것처럼, SQL Data Warehouse Gen2는 **Amazon Redshift보다 42% 더 강력한 성능을 제공합니다**.

이제 Gen2는 작은 데이터 웨어하우스 또는 개발/테스트 환경을 실행할 수 있고 개선된 모든 최신 서비스를 이용할 수 있는 저렴한 진입점으로써 DWU500c를 제공합니다. 이 새로운 진입점은 [적응형 캐싱](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [초고속 데이터 셔플링](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/), [실시간 데이터 웨어하우스](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/) 지원을 포함하여 Gen2의 모든 기능을 그대로 유지합니다.

## <a name="sql-vulnerability-assessment"></a>SQL 취약성 평가
[SQL VA(취약성 평가)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/)는 지속적으로 데이터 웨어하우스를 모니터링하는 사용하기 쉬운 서비스입니다. 항상 높은 수준의 보안을 보장하고 조직의 정책을 충족하도록 지원합니다. 발견된 각 문제에 대해 실행 가능한 수정 단계와 함께 포괄적인 보안 보고서를 제공합니다. 이 보고서를 사용하면 데이터베이스 보안 상태를 사전에 쉽게 관리하고 보안 전문가가 아니더라도 영향이 가장 높은 작업에 집중할 수 있습니다. 자주 변경되고 추적이 어려운 동적인 환경에서는 데이터 웨어하우스가 공격에 취약해질 수 있는 설정을 감지하는 데 VA가 매우 유용합니다.

## <a name="improved-availability-with-query-restartability"></a>쿼리 재시작을 통해 가용성 향상
쿼리를 실행하는 동안 쿼리 실패의 원인이 되는 여러 가지 문제가 발생할 수 있습니다. 네트워크 중단, 하드웨어 오류 또는 연결 끊김으로 인해 작업이 중단될 수 있습니다. SQL Data Warehouse는 이제 단계 또는 명령문 수준 SELECT 쿼리에 대한 쿼리 재시작을 지원합니다. 

쿼리 재시작을 사용하면 오류로 인해 중단된 쿼리가 자동으로 다시 시작됩니다. 단계 수, 쿼리의 셰이프 또는 실행 중에 쿼리가 중단되었는지 여부에 따라 SQL Data Warehouse 엔진이 전체 쿼리를 다시 시작하거나 마지막으로 완료된 쿼리 단계에서 다시 시작합니다. 최종 사용자가 볼 때는 쿼리가 그냥 완료된 것입니다. 

## <a name="maintenance-scheduling-preview"></a>유지 관리 예약(미리 보기)
Azure SQL Data Warehouse 유지 관리 예약 기능은 미리 보기로 제공됩니다. 새롭게 제공되는 이 기능은 서비스 상태 계획된 유지 관리 알림, 리소스 상태 검사 모니터 및 Azure SQL Data Warehouse 유지 관리 예약 서비스와 원활하게 통합됩니다. 유지 관리 예약 기능을 사용하면 새 기능, 업그레이드 및 패치를 받기에 편리한 기간을 예약할 수 있습니다.

유지 관리 예약은 Azure Monitor를 활용합니다. 고객은 이 기능을 통해 예정된 유지 관리 이벤트 알림을 받을 방법과, 작업에 대한 영향을 최소화하고 가동 중지 시간을 관리하기 위해 트리거해야 하는 자동화된 흐름을 결정할 수 있습니다. 알림에 이메일 또는 텍스트를 포함할 수 있습니다. 

## <a name="wide-row-support-in-polybase"></a>Polybase의 넓은 행 지원
데이터를 SQL Data Warehouse에 로드할 때 일반적으로 병렬 데이터 로드를 지원하는 [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase)를 사용합니다. 이 릴리스는 32K부터 1MB까지 광범위한 열을 지원하므로 행 크기가 큰 테이블을 로드할 수 있습니다. 넓은 행에 대한 지원은 넓은 행이 포함된 테이블의 데이터 로드 프로세스를 간소화합니다.

> [!Note]
> 총 행 크기가 1MB를 초과하면 안 됩니다.

## <a name="additional-language-support"></a>추가 언어 지원
이 릴리스에는 다음 T-SQL 언어 요소에 대한 지원이 도입되었습니다.

### <a name="stringsplit"></a>STRING_SPLIT
[STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) 함수는 지정된 구분 기호를 사용하여 문자 문자열을 분할합니다. STRING_SPLIT는 열에 구문 분석 후 다른 테이블에 삽입할 값이 여러 개 있는 데이터 로딩 시나리오에서 유용합니다.

#### <a name="example"></a>예
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>COMPRESS/DECOMPRESS 함수
[COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) 함수를 사용하면 GZIP 알고리즘을 사용하여 문자열 입력을 압축하거나 압축을 풀 수 있습니다.

#### <a name="example"></a>예

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>뷰를 삭제하는 IF EXISTS 절
[DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) 문에 IF EXISTS 절을 추가하면 데이터 웨어하우스에서 보기를 제거하는 데 필요한 T-SQL 코드가 간소화됩니다. IF EXISTS 구문은 DROP VIEW 문에 적용할 경우 보기가 있으면 보기를 삭제하고 보기가 없으면 명령문을 무시합니다.

#### <a name="example"></a>예
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>싱글톤 삽입 및 DDL 문에 대한 컴파일 시간 개선 
데이터 삽입을 위한 전통적인 ETL(추출, 변환 및 로드) 모델을 따르면 종종 데이터베이스의 테이블에 singleton-insert([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) 작업이 실행되는 결과로 이어집니다. 이 릴리스에서는 이 형식의 명령문을 실행하는 데 필요한 컴파일 시간을 줄여서 singleton-insert 작업의 성능이 개선됩니다. 컴파일 성능이 최대 3배 향상된 경우도 있습니다. 컴파일 시간이 개선되면 singleton-insert 문을 실행하는 데 필요한 시간이 단축됩니다. 

개체 수가 많은 데이터 웨어하우스 역시 CREATE, ALTER, DELETE 작업을 포함한 DDL(데이터 정의 언어) 문의 쿼리 컴파일 시간을 단축하여 성능이 개선되었습니다. 

마지막으로, 이번 기능 향상으로 수많은 열이 있는 와이드 테이블에 대한 전체적인 명령문 실행 시간이 단축됩니다. 쿼리 컴파일 단계의 시간이 단축되어 전체적인 쿼리 실행 시간이 단축됩니다.

## <a name="bug-fixes"></a>버그 수정

| 제목 | 설명 |
|:---|:---|
| **고유 제약 조건에 대한 배포 통계를 만들 때 발생하는 문제 수정** | 이 픽스는 테이블만 지정되어 있고 테이블의 고유 제약 조건이 정의된 UPDATE STATISTICS를 실행할 때 발생하는 오류를 해결합니다. |
| **외부 테이블에 대해 쿼리를 컴파일할 때 발생하는 문제 수정** | 이 픽스는 외부 테이블과 관련된 쿼리의 컴파일 시간에 영향을 주는 결함을 해결합니다.|
| **large 형식의 명령문을 실행할 때 발생하는 문제 수정** | *large* 형식(nvarchar(max), varchar(max) 및 varbinary(max)) 중 하나로 선언된 매개 변수를 사용하여 준비된 명령문 컴파일의 결함을 해결합니다. |
| **깊게 중첩된 쿼리에서 발생하는 오류 수정** | 깊게 중첩된 쿼리가 시스템 한도를 초과하는 경우 명확한 오류 메시지를 제공합니다.|
| **명령문에 상호 관련된 하위 쿼리와 실행 시간 상수가 포함된 경우에 발생하는 컴파일 시간 오류 수정** |상호 관련된 하위 쿼리와 실행 시간 상수(예: GETDATE())의 특정 조합을 사용하는 쿼리에 대한 컴파일 시간 오류를 해결합니다.|
| **autostats에 대한 PDW 개체 잠금 및 동시성 슬롯을 획득하기 위한 시간 제한 해결** |autostats 요청이 원래 요청을 장시간 동안 차단할 수 없도록 잠금 시간 제한을 추가합니다.|

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [Stack Overflow 포럼]
* [Twitter]


[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 포럼]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

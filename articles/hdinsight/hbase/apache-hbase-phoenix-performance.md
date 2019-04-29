---
title: Azure HDInsight의 Phoenix 성능
description: Phoenix 성능을 최적화하는 모범 사례입니다.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/22/2018
ms.date: 01/14/2019
ms.author: v-yiso
ms.openlocfilehash: 4fc4d1843ddb8d007ca062d928ebbddf90909583
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114310"
---
# <a name="apache-phoenix-performance-best-practices"></a>Apache Phoenix 성능 모범 사례

[Apache Phoenix](https://phoenix.apache.org/) 성능의 가장 중요한 측면은 기본 [Apache HBase](https://hbase.apache.org/)를 최적화하는 것입니다. Phoenix는 SQL 쿼리를 검색과 같은 HBase 작업으로 변환하는 HBase 위에 관계형 데이터 모델을 만듭니다. 테이블 스키마 디자인, 기본 키의 필드 선택 및 순서 지정 그리고 인덱스 사용은 모두 Phoenix 성능에 영향을 줍니다.

## <a name="table-schema-design"></a>테이블 스키마 디자인

Phoenix에서 테이블을 만드는 경우 해당 테이블은 HBase 테이블에 저장됩니다. HBase 테이블은 함께 액세스할 수 있는 열 그룹(열 패밀리)을 포함합니다. Phoenix 테이블의 행은 HBase 테이블의 행이며, 각 행은 하나 이상의 열과 연결된 버전이 있는 셀로 구성됩니다. 논리적으로 단일 HBase 행은 각각 동일한 rowkey 값을 갖는 키-값 쌍의 컬렉션입니다. 즉, 각 키-값 쌍에는 rowkey 특성이 있으며 해당 rowkey 특성의 값은 특정 행에 대해 동일합니다.

Phoenix 테이블의 스키마 디자인에는 기본 키 디자인, 열 패밀리 디자인, 개별 열 디자인 및 데이터 분할 방법이 포함됩니다.

### <a name="primary-key-design"></a>기본 키 디자인

Phoenix의 테이블에 정의된 기본 키는 기본 HBase 테이블의 rowkey 내에 데이터가 저장되는 방법을 결정합니다. HBase에서 특정 행에 액세스하는 유일한 방법은 rowkey를 사용하는 것입니다. 또한 HBase 테이블에 저장된 데이터는 rowkey별로 정렬됩니다. Phoenix는 행에 있는 각 열의 값을 기본 키에 정의된 순서대로 연결하여 rowkey 값을 작성합니다.

예를 들어 연락처에 대한 테이블의 이름, 성, 전화 번호 및 주소는 모두 같은 열 패밀리에 있습니다. 증가하는 시퀀스 번호를 기준으로 기본 키를 정의할 수 있습니다.

|rowkey|       address|   phone| firstname| Lastname|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

그러나 lastName으로 자주 쿼리하는 경우 이 기본 키가 제대로 수행되지 않을 수 있습니다. 이는 각 쿼리에서 모든 lastName의 값을 읽기 위해 전체 테이블 검색이 필요하기 때문입니다. 대신 lastName, firstName 및 사회 보장 번호 열에 기본 키를 정의할 수 있습니다. 이 마지막 열은 아버지 및 아들과 같은 이름으로 동일한 주소의 두 명의 거주자를 명확히 구분하는 것입니다.

|rowkey|       address|   phone| firstname| Lastname| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

이 새로운 기본 키를 사용하여 Phoenix에서 생성한 행 키는 다음과 같습니다.

|rowkey|       address|   phone| firstname| Lastname| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

위의 첫 번째 행에서 rowkey에 대한 데이터는 다음과 같이 표시됩니다.

|rowkey|       key|   값| 
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstname |John|  
|  Dole-John-111|Lastname |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

이 rowkey는 이제 데이터의 중복 복사본을 저장합니다. 이 값은 기본 HBase 테이블의 모든 셀에 포함되므로 기본 키에 포함될 열의 크기와 수를 고려해야 합니다.

또한 기본 키의 값이 일정하게 증가하는 경우 쓰기 핫스폿을 만들지 않도록 하려면 *솔트 버킷*으로 테이블을 만들어야 합니다. [데이터 분할](#partition-data)을 참조하세요.

### <a name="column-family-design"></a>열 패밀리 디자인

일부 열에 다른 열보다 더 자주 액세스하는 경우 자주 액세스하는 열과 드물게 액세스하는 열을 구분하려면 여러 열 패밀리를 만들어야 합니다.

또한 특정 열이 함께 액세스되는 경향이 있는 경우 해당 열을 동일한 열 패밀리에 배치합니다.

### <a name="column-design"></a>열 디자인

* VARCHAR 열은 큰 열의 I/O 비용으로 인해 약 1MB 미만으로 유지합니다. 쿼리를 처리할 때 HBase는 전체 셀을 구체화한 후에 클라이언트에 보내고, 클라이언트는 전체 셀을 받은 후에 애플리케이션 코드에 전달합니다.
* protobuf, Avro, msgpack 또는 BSON과 같은 압축 형식을 사용하여 열 값을 저장합니다. JSON은 더 큰 크기로 인해 권장되지 않습니다.
* 데이터를 먼저 압축한 후에 저장하여 대기 시간과 I/O 비용을 줄입니다.

### <a name="partition-data"></a>데이터 분할

Phoenix를 사용하면 데이터가 배포되는 지역의 수를 제어할 수 있으므로 읽기/쓰기 성능이 크게 향상됩니다. Phoenix 테이블을 만들 때 데이터를 솔트 처리(salt)하거나 미리 분할할 수 있습니다.

만드는 동안 테이블을 솔트 처리하려면 솔트 버킷 수를 지정합니다.

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

이 솔트 처리(salting)는 기본 키 값에 따라 테이블을 분할하여 값을 자동으로 선택합니다. 

테이블 분할이 발생하는 위치를 제어하려면 분할이 발생하는 범위 값을 제공하여 테이블을 미리 분할할 수 있습니다. 예를 들어 세 지역에 따라 분할된 테이블을 만들려면 다음을 수행합니다.

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>인덱스 디자인

Phoenix 인덱스는 인덱싱된 테이블의 일부 또는 전체 데이터의 복사본을 저장하는 HBase 테이블입니다. 인덱스는 특정 유형의 쿼리에 대한 성능을 향상시킵니다.

여러 인덱스를 정의한 다음 테이블을 쿼리하면 Phoenix에서 쿼리에 가장 적합한 인덱스를 자동으로 선택합니다. 기본 인덱스는 선택한 기본 키에 따라 자동으로 만들어집니다.

예상된 쿼리의 경우 열을 지정하여 보조 인덱스를 만들 수도 있습니다.

인덱스를 디자인하는 경우 다음을 고려합니다.

* 필요한 인덱스만 만듭니다.
* 자주 업데이트되는 테이블의 인덱스 수를 제한합니다. 테이블에 대한 업데이트는 주 테이블과 인덱스 테이블 모두에 대한 쓰기로 변환됩니다.

## <a name="create-secondary-indexes"></a>보조 인덱스 만들기

보조 인덱스는 전체 테이블 검색을 지점 조회로 전환하여 저장 공간 및 쓰기 속도를 희생함으로써 읽기 성능을 향상시킬 수 있습니다. 테이블을 만든 후에 보조 인덱스를 추가하거나 제거할 수 있으며, 기존 쿼리는 변경할 필요가 없습니다. 오히려 쿼리가 더 빨리 실행됩니다. 필요에 따라 covered 인덱스, 함수 기반 인덱스 또는 둘 다를 만드는 것이 좋습니다.

### <a name="use-covered-indexes"></a>covered 인덱스 사용

covered 인덱스는 인덱싱된 값 외에도 행의 데이터를 포함하는 인덱스입니다. 원하는 인덱스 항목을 찾으면 기본 테이블에 액세스할 필요가 없습니다.

예를 들어 연락처 테이블 예제에서 socialSecurityNum 열에만 보조 인덱스를 만들 수 있습니다. 이 보조 인덱스는 socialSecurityNum 값으로 필터링하는 쿼리의 속도를 높이지만, 다른 필드 값을 검색하는 경우 주 테이블에 대한 다른 읽기가 필요합니다.

|rowkey|       address|   phone| firstname| Lastname| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

그러나 일반적으로 socialSecurityNum이 지정된 firstName 및 lastName을 조회하려는 경우, firstName 및 lastName이 인덱스 테이블의 실제 데이터로 포함되는 covered 인덱스를 만들 수 있습니다.

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

이 covered 인덱스를 사용하면 다음 쿼리를 통해 보조 인덱스가 포함된 테이블에서 읽는 것만으로도 모든 데이터를 얻을 수 있습니다.

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>함수 기반 인덱스 사용

함수 기반 인덱스를 사용하면 쿼리에 사용될 임의의 식에 인덱스를 만들 수 있습니다. 일단 함수 기반 인덱스가 적절히 배치되고 쿼리에서 해당 식을 사용하면, 이 인덱스를 사용하여 데이터 테이블 대신 결과를 검색할 수 있습니다.

예를 들어 사람의 이름과 성을 조합하여 대/소문자를 구분하지 않고 검색할 수 있는 인덱스를 만들 수 있습니다.

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>쿼리 디자인

쿼리 디자인의 주요 고려 사항은 다음과 같습니다.

* 쿼리 계획을 이해하고 예상되는 동작을 확인합니다.
* 효율적으로 조인합니다.

### <a name="understand-the-query-plan"></a>쿼리 계획 이해

[SQLLine](http://sqlline.sourceforge.net/)에서 EXPLAIN 뒤에 SQL 쿼리를 사용하여 Phoenix에서 수행할 작업 계획을 확인합니다. 계획을 확인하는 방법은 다음과 같습니다.

* 적절한 경우 기본 키를 사용합니다.
* 데이터 테이블 대신 적절한 보조 인덱스를 사용합니다.
* 가능한 경우 테이블 검색 대신 범위 검색 또는 검색 건너뛰기를 사용합니다.

#### <a name="plan-examples"></a>계획 예제

예를 들어 비행 지연 정보를 저장하는 FLIGHTS라는 테이블이 있다고 가정해 보겠습니다.

airlineid가 `19805`인 항공편을 모두 선택하려면 다음과 같습니다. 여기서 airlineid는 기본 키 또는 인덱스에 없는 필드입니다.

    select * from "FLIGHTS" where airlineid = '19805';

explain 명령을 다음과 같이 실행합니다.

    explain select * from "FLIGHTS" where airlineid = '19805';

쿼리 계획은 다음과 같습니다.

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

이 계획에서 FULL SCAN OVER FLIGHTS(항공편에 대한 전체 검색)라는 구문을 주의하세요. 이 구문은 실행에서 더 효율적인 범위 검색 또는 검색 건너뛰기 옵션을 사용하는 대신, 테이블의 모든 행에 대해 테이블 검색을 수행함을 나타냅니다.

이제 flightnum이 1보다 큰 `AA` 항공사에 대해 2014년 1월 2일의 항공편을 쿼리하려고 한다고 가정해 보겠습니다. 그리고 year, month, dayofmonth, carrier 및 flightnum 열이 예제 테이블에 있고, 모두 복합 기본 키의 일부라고 가정해 보겠습니다. 쿼리는 다음과 같습니다.

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

다음을 사용하여 이 쿼리에 대한 계획을 살펴보겠습니다.

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

결과 계획은 다음과 같습니다.

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

대괄호 안의 값은 기본 키의 값 범위입니다. 이 경우 범위 값은 year에 대해 2014, month에 대해 1 및 dayofmonth에 대해 2로 고정되지만, flightnum에 대한 값은 2에서 시작하여 `*`까지 허용됩니다. 이 쿼리 계획은 기본 키가 예상대로 사용되는지 확인합니다.

다음으로, carrier 필드에만 있는 `carrier2_idx`라는 인덱스를 FLIGHTS 테이블에 만듭니다. 또한 이 인덱스에는 데이터를 인덱스에 저장하는 covered 열로 flightdate, tailnum, origin 및 flightnum도 포함됩니다.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

다음 쿼리와 같이 flightdate 및 tailnum과 함께 carrier를 가져오려고 한다고 가정해 보겠습니다.

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

이 인덱스는 다음과 같이 사용됩니다.

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

explain 계획 결과에 나타날 수 있는 항목의 전체 목록은 [Apache Phoenix 튜닝 가이드](https://phoenix.apache.org/tuning_guide.html)의 Explain Plans(explain 계획) 섹션을 참조하세요.

### <a name="join-efficiently"></a>효율적인 조인

일반적으로 한 쪽이 작지 않으면, 특히 빈번한 쿼리의 경우에는 조인하지 않으려고 합니다.

필요에 따라 `/*+ USE_SORT_MERGE_JOIN */` 힌트를 사용하여 큰 조인을 수행할 수 있지만, 큰 조인은 비용이 많이 드는 많은 수의 행에 대한 작업입니다. 모든 오른쪽 테이블의 전체 크기가 사용 가능한 메모리를 초과하는 경우 `/*+ NO_STAR_JOIN */` 힌트를 사용합니다.

## <a name="scenarios"></a>시나리오

다음 지침은 일반적인 몇 가지 패턴을 설명합니다.

### <a name="read-heavy-workloads"></a>읽기 작업이 많은 워크로드

읽기 작업이 많은 사용 사례의 경우 인덱스를 사용하고 있는지 확인합니다. 또한 읽기 시간 오버헤드를 줄이려면 covered 인덱스를 만드는 것이 좋습니다.

### <a name="write-heavy-workloads"></a>쓰기 작업이 많은 워크로드

기본 키가 일정하게 증가하는 쓰기 작업이 많은 워크로드의 경우, 필요한 추가 검색으로 인해 전체 읽기 처리량을 희생하는 핫스폿 쓰기를 방지하는 데 유용한 솔트 버킷을 만듭니다. 또한 UPSERT를 사용하여 많은 수의 레코드를 쓰는 경우 autoCommit을 해제하고 레코드를 일괄 처리합니다.

### <a name="bulk-deletes"></a>대량 삭제

큰 데이터 집합을 삭제하는 경우 DELETE 쿼리를 실행하기 전에 autoCommit을 설정하여 클라이언트에서 삭제된 모든 행에 대한 행 키를 기억할 필요가 없도록 합니다. autoCommit은 클라이언트에서 DELETE의 영향을 받는 행을 버퍼링하지 못하도록 하여 Phoenix에서 클라이언트로 반환하지 않고도 지역 서버로부터 직접 삭제할 수 있습니다.

### <a name="immutable-and-append-only"></a>변경 불가능 및 추가 전용

시나리오에서 데이터 무결성보다 쓰기 속도를 선호하는 경우 테이블을 만들 때 미리 쓰기 로그를 사용하지 않도록 설정하는 것이 좋습니다.

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

이 옵션 및 다른 옵션에 대한 자세한 내용은 [Apache Phoenix 문법](https://phoenix.apache.org/language/index.html#options)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Apache Phoenix 튜닝 가이드](https://phoenix.apache.org/tuning_guide.html)
* [보조 인덱스](https://phoenix.apache.org/secondary_indexing.html)

---
title: Azure SQL Database 다중 모델 기능 | Microsoft Docs
description: Azure SQL Database를 사용하면 동일한 데이터베이스에서 여러 데이터 모델을 사용할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 4351017cc1848e29cca038f82fd96548ae3492e0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122662"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Azure SQL Database의 다중 모델 기능

다중 모델 데이터베이스를 사용하면 관계형 데이터, 그래프, JSON/XML 문서, 키-값 쌍 등의 여러 데이터 형식으로 표시되는 데이터를 저장하고 사용할 수 있습니다.

## <a name="when-to-use-multi-model-capabilities"></a>다중 모델 기능을 사용 하는 경우

Azure SQL Database는 다양한 범용 애플리케이션에서 대부분의 경우 최상의 성능을 제공하는 관계형 모델을 사용하도록 설계되었습니다. 그러나 Azure SQL Database는 관계형 데이터만으로 제한되지 않습니다. Azure SQL Database를 사용하면 관계형 모델에 완전 통합된 다양한 비관계형 형식을 사용할 수 있습니다.
다음 경우에 Azure SQL Database의 다중 모델 기능을 사용 하는 것이 좋습니다.
- 몇 가지 정보 또는 NoSQL 모델에 맞게 되는 구조 별도 NoSQL 데이터베이스를 사용 하지 않으려는 합니다.
- 대부분의 데이터는 관계형 모델에 적합 하 고 NoSQL 스타일의 데이터 일부를 모델링 해야 합니다.
- 다양 한 TRANSACT-SQL 언어를 쿼리하고 분석 관계형 및 NoSQL 데이터를 활용 하 고 다양 한 도구와 SQL 언어를 사용할 수 있는 응용 프로그램을 사용 하 여 통합 하려고 합니다.
- 와 같은 데이터베이스 기능을 적용 하려는 [메모리 내 기술을](sql-database-in-memory.md) 분석 프로그램의 성능을 향상 시키거나 사용 하면 NoSQL 데이터 strucutres 처리 [트랜잭션 복제](sql-database-managed-instance-transactional-replication.md) 또는[읽기 가능한 복제본](sql-database-read-scale-out.md) 다른 곳에 데이터의 복사본을 만들고 주 데이터베이스에서 일부 분석 워크 로드를 오프 로드 합니다.

## <a name="overview"></a>개요

Azure SQL은 다음 다중 모델 기능을 제공합니다.
- [그래프 기능](#graph-features)을 사용하면 데이터를 노드 및 에지 세트로 표시하고 그래프 `MATCH` 연산자로 향상된 표준 Transact-SQL 쿼리를 사용하여 그래프 데이터를 쿼리할 수 있습니다.
- [JSON 기능](#json-features)을 사용하면 JSON 문서를 테이블에 넣고, 관계형 데이터를 JSON 문서로 변환하거나 그 반대로 변환할 수 있습니다. JSON 함수로 향상된 표준 Transact-SQL 언어를 문서 구문 분석에 사용하고, 비클러스터형 인덱스, columnstore 인덱스 또는 메모리 최적화 테이블을 사용하여 쿼리를 최적화할 수 있습니다.
- [공간 기능](#spatial-features)을 사용하면 지리적 및 기하학적 데이터를 저장하고, 공간 인덱스를 사용하여 해당 데이터를 인덱싱하고, 공간 쿼리를 사용하여 데이터를 검색할 수 있습니다.
- [XML 기능](#xml-features)을 사용하면 데이터베이스에서 XML 데이터를 저장 및 인덱싱하고 네이티브 XQuery/XPath 작업을 통해 XML 데이터를 사용할 수 있습니다. Azure SQL 데이터베이스에는 XML 데이터를 처리하는 특수화된 기본 제공 XML 쿼리 엔진이 있습니다.
- [키-값 쌍](#key-value-pairs)은 기본적으로 2열 테이블로 모델링될 수 있으므로 명시적으로 특수 기능으로 지원되지는 않습니다.

  > [!Note]
  > 동일한 Transact-SQL 쿼리에서 JSON 경로 식, XQuery/XPath 식, 공간 함수 및 그래프-쿼리 식을 사용하여 데이터베이스에 저장한 데이터에 액세스할 수 있습니다. Transact-SQL 쿼리를 실행할 수 있는 도구나 프로그래밍 언어도 해당 쿼리 인터페이스를 사용하여 다중 모델 데이터에 액세스할 수 있습니다. 이는 다양한 데이터 모델에 대한 특수화된 API를 제공하는 [Azure Cosmos DB](/azure/cosmos-db/)와 같은 다중 모델 데이터베이스와 비교되는 주요 차이점입니다.

다음 섹션에서는 Azure SQL Database의 가장 중요한 다중 모델 기능을 알아볼 수 있습니다.

## <a name="graph-features"></a>그래프 기능

Azure SQL Database는 데이터베이스에서 다 대 다 관계를 모델링하는 그래프 데이터베이스 기능을 제공합니다. 그래프는 노드(또는 정점) 및 에지(또는 관계)의 컬렉션입니다. 노드는 엔터티(예: 개인 또는 조직)를 나타내고 에지는 에지가 연결하는 두 노드 간의 관계(예: 좋아요 또는 친구)를 나타냅니다. 그래프 데이터베이스를 고유하게 만드는 몇 가지 기능은 다음과 같습니다.
- 에지 또는 관계는 그래프 데이터베이스의 첫 번째 클래스 엔터티이며 엔터티와 연결된 특성 또는 속성을 포함할 수 있습니다.
- 단일 에지는 유연하게 그래프 데이터베이스의 여러 노드를 연결할 수 있습니다.
- 패턴 일치 및 멀티 홉 탐색 쿼리를 쉽게 표현할 수 있습니다.
- 이행적 폐쇄 및 다형적 쿼리를 쉽게 표현할 수 있습니다.

그래프 관계 및 그래프 쿼리 기능은 Transact-SQL에 통합되었고 SQL Server를 기본 데이터베이스 관리 시스템으로 사용하는 경우의 혜택을 받습니다.
[그래프 처리](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)는 핵심 SQL Server 데이터베이스 엔진 기능이므로, 여기에서 그래프 처리에 대한 자세한 정보를 찾을 수 있습니다.

### <a name="when-to-use-a-graph-capability"></a>그래프 기능을 사용하는 경우

그래프 데이터베이스로 수행할 수 있는 작업은 없고, 이러한 작업은 관계형 데이터베이스로도 수행할 수 없습니다. 그러나 그래프 데이터베이스를 사용하면 특정 쿼리를 더 쉽게 표현할 수 있습니다. 둘 중 하나를 선택하는 결정은 다음 요소를 기반으로 할 수 있습니다.

- 모델 계층 구조 데이터의 한 노드에 여러 부모가 포함될 수 있습니다(HieararchyId를 사용할 수 없음).
- 모델에는 복잡한 다 대 다 관계를 가진 애플리케이션이 포함됩니다. 애플리케이션이 발전함에 따라 새로운 관계가 추가됩니다.
- 상호 연결된 데이터 및 관계를 분석해야 합니다.

## <a name="json-features"></a>JSON 기능

Azure SQL Database를 사용하면 [JSON](https://www.json.org/)(JavaScript Object Notation) 형식으로 표현된 데이터를 구문 분석 및 쿼리하고 관계형 데이터를 JSON 텍스트로 내보낼 수 있습니다.

JSON은 최신 웹 및 모바일 애플리케이션에서 데이터를 교환하는 데 사용되는 일반적인 데이터 형식입니다. 또한 JSON은 로그 파일 또는 NoSQL 데이터베이스(예: [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/))에 반구조화된 데이터를 저장하는 데도 사용됩니다. 많은 REST 웹 서비스는 JSON 텍스트로 형식이 지정된 결과를 반환하거나 JSON으로 형식이 지정된 데이터를 수락합니다. 대부분의 Azure 서비스(예: [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/) 및 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/))에는 JSON을 반환하거나 사용하는 REST 엔드포인트가 있습니다.

Azure SQL Database를 사용하여 JSON 데이터를 쉽게 사용하고 데이터베이스를 최신 서비스와 통합할 수 있습니다. Azure SQL Database는 JSON 데이터를 사용하기 위한 다음과 같은 함수를 제공합니다.

![JSON 함수](./media/sql-database-json-features/image_1.png)

JSON 텍스트가 있는 경우 JSON에서 데이터를 추출하거나 기본 제공 함수 [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) 및 [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx)을 사용하여 JSON 형식이 제대로 지정되었는지 확인할 수 있습니다. [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) 함수를 사용하면 JSON 텍스트 내의 값을 업데이트할 수 있습니다. 고급 쿼리 및 분석을 위해 [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) 함수는 JSON 개체의 배열을 행 집합을 변환할 수 있습니다. 모든 SQL 쿼리는 반환된 결과 집합에서 실행할 수 있습니다. 마지막으로 관계형 테이블에 저장된 데이터의 형식을 JSON 텍스트로 지정할 수 있는 [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) 절이 있습니다.

자세한 내용은 [Azure SQL Database에서 JSON 데이터를 사용하는 방법](sql-database-json-features.md)을 참조하세요.
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)은 핵심 SQL Server 데이터베이스 엔진 기능이므로, 여기에서 JSON 기능에 대한 자세한 정보를 찾을 수 있습니다.

### <a name="when-to-use-a-json-capability"></a>JSON 기능을 사용하는 경우

일부 특정 시나리오에서는 관계형 모델 대신 문서 모델을 사용할 수 있습니다.
- 개체의 정규화된 파트를 업데이트하지 않고 개체의 모든 필드에 한 번에 액세스하므로 스키마의 정규화 수준이 높아도 큰 이점은 없습니다. 그러나 정규화된 모델의 경우 데이터를 가져오기 위해 많은 테이블을 조인해야 하므로 쿼리의 복잡성이 커집니다.
- 통신 및 데이터 모델인 JSON 문서를 기본적으로 사용하는 애플리케이션 관련 작업을 수행 중이고, 관계형 데이터를 JSON으로 변환하거나 그 반대로 변환하는 추가 계층을 도입하지 않으려고 합니다.
- 자식 테이블 또는 엔터티-개체-값 패턴을 비정규화하여 데이터 모델을 간소화해야 합니다.
- 데이터를 구문 분석하는 추가 도구 없이 JSON 형식으로 저장된 데이터를 로드하거나 내보내야 합니다.

## <a name="spatial-features"></a>공간 기능

공간 데이터는 기하학적 개체의 물리적 위치 및 모양 정보를 나타냅니다. 이러한 개체는 지점 위치이거나 국가, 도로 또는 호수와 같은 더 복잡한 개체일 수 있습니다.

Azure SQL Database는 두 개의 공간 데이터 형식(기하 도형 데이터 형식 및 지리 데이터 형식)을 지원합니다.
- 기하 도형 형식은 유클리드(평면) 좌표계의 데이터를 나타냅니다.
- 지리 형식은 둥근 표면 좌표계의 데이터를 나타냅니다.

[Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)과 같이 Azure SQL 데이터베이스에서 사용할 수 있는 많은 공간 개체가 있습니다.

Azure SQL Database는 공간 쿼리의 성능을 향상하는 데 사용할 수 있는 특수화된 [공간 인덱스](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview)도 제공합니다.

[공간 지원](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)은 핵심 SQL Server 데이터베이스 엔진 기능이므로, 여기에서 공간 기능에 대한 자세한 정보를 찾을 수 있습니다.

## <a name="xml-features"></a>XML 기능

SQL Server는 반정형 데이터 관리에 사용할 풍부한 애플리케이션을 개발하기 위한 강력한 플랫폼을 제공합니다. XML 지원은 SQL Server의 모든 구성 요소에 통합되었고 다음을 포함합니다.

- xml 데이터 형식. 기본적으로 XML 값은 XML 스키마의 컬렉션에 따라 형식화되거나 형식화되지 않을 수 있는 xml 데이터 형식 열에 저장할 수 있습니다. XML 열을 인덱싱할 수 있습니다.
- xml 형식의 열 및 변수에 저장된 XML 데이터에 대한 XQuery 쿼리를 지정하는 기능. XQuery 기능은 데이터베이스에서 사용하는 데이터 모델에 액세스하는 모든 Transact-SQL 쿼리에서 사용할 수 있습니다.
- [기본 XML 인덱스](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)를 사용하여 XML 문서의 모든 요소를 자동으로 인덱싱하거나 [보조 XML 인덱스](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)를 사용하여 인덱싱해야 하는 정확한 경로를 지정합니다.
- XML 데이터를 대량으로 로드할 수 있는 OPENROWSET.
- 관계형 데이터를 XML 형식으로 변환합니다.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)은 핵심 SQL Server 데이터베이스 엔진 기능이므로, 여기에서 XML 기능에 대한 자세한 정보를 찾을 수 있습니다.

### <a name="when-to-use-an-xml-capability"></a>XML 기능을 사용하는 경우

일부 특정 시나리오에서는 관계형 모델 대신 문서 모델을 사용할 수 있습니다.
- 개체의 정규화된 파트를 업데이트하지 않고 개체의 모든 필드에 한 번에 액세스하므로 스키마의 정규화 수준이 높아도 큰 이점은 없습니다. 그러나 정규화된 모델의 경우 데이터를 가져오기 위해 많은 테이블을 조인해야 하므로 쿼리의 복잡성이 커집니다.
- 통신 및 데이터 모델인 XML 문서를 기본적으로 사용하는 애플리케이션 관련 작업을 수행 중이고, 관계형 데이터를 XML로 변환하거나 그 반대로 변환하는 추가 계층을 도입하지 않으려고 합니다.
- 자식 테이블 또는 엔터티-개체-값 패턴을 비정규화하여 데이터 모델을 간소화해야 합니다.
- 데이터를 구문 분석하는 추가 도구 없이 XML 형식으로 저장된 데이터를 로드하거나 내보내야 합니다.

## <a name="key-value-pairs"></a>키-값 쌍

키-값 구조는 기본적으로 표준 관계형 테이블로 표시할 수 있으므로 Azure SQL Database에는 키-값 쌍을 지원하는 특수화된 형식 또는 구조가 없습니다.

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

제약 조건 없이 필요에 맞게 이 키-값 구조를 사용자 지정할 수 있습니다. 예를 들어 값은 `nvarchar(max)` 형식 대신 XML 문서일 수 있습니다. 값이 JSON 문서인 경우 JSON 콘텐츠의 유효성을 확인하는 `CHECK` 제약 조건을 넣을 수 있습니다. 하나의 키에 관련된 여러 값을 추가 열에 넣고, 계산 열 및 인덱스를 추가하여 데이터 액세스를 간소화 및 최적화하고, 테이블을 메모리/최적화 스키마 전용 테이블로 정의하여 성능을 개선할 수 있습니다.

실제로 관계형 모델을 키-값 쌍 솔루션으로 효과적으로 사용할 수 있는 방법의 예로, 초당 1.200.000개 배치를 수행한 ASP.NET 캐싱 솔루션에 대한 [how BWin is using In-Memory OLTP to achieve unprecedented performance and scale](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/)(BWin이 메모리 내 OLTP를 사용하여 전례 없는 성능 및 규모를 사용하는 방법)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure SQL Database의 다중 모델 기능도 Azure SQL Database와 SQL Server 간에 공유되는 핵심 SQL Server 데이터베이스 엔진 기능입니다. 이러한 기능에 대한 자세한 내용을 보려면 SQL 관계형 데이터베이스 설명서 페이지를 방문하세요.

* [그래프 처리 중](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON 데이터](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [공간 지원](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML 데이터](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)

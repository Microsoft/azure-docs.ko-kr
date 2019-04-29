---
title: 확장성 있는 클라우드 데이터베이스 빌드 | Microsoft Docs
description: 탄력적 데이터베이스 클라이언트 라이브러리와 함께 확장성 있는 .NET 데이터베이스 앱 빌드
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: c0d50f3a66d940618f2bc421537b113120a2eaca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475867"
---
# <a name="building-scalable-cloud-databases"></a>확장성 있는 클라우드 데이터베이스 빌드

Azure SQL Database의 확장성 있는 도구 및 기능을 사용하여 데이터베이스 규모 확장을 쉽게 달성할 수 있습니다. 특히, **Elastic Database 클라이언트 라이브러리** 를 사용하여 규모 확장된 데이터베이스를 만들고 관리할 수 있습니다. 이 기능을 통해 수백 또는 수천 개의 Azure SQL 데이터베이스를 사용하여 분할된 애플리케이션을 쉽게 개발할 수 있습니다. [탄력적 작업](sql-database-elastic-jobs-powershell.md) 을 사용할 수 있습니다.

다운로드하려면:

* 라이브러리의 Java 버전: [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools) 참조
* 라이브러리의 .NET 버전: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) 참조

## <a name="documentation"></a>문서화

1. [Elastic Database 도구 시작하기](sql-database-elastic-scale-get-started.md)
2. [Elastic Database 기능](sql-database-elastic-scale-introduction.md)
3. [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)
4. [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)
6. [다중 분할된 쿼리](sql-database-elastic-scale-multishard-querying.md)
7. [Elastic Database 도구를 사용하여 분할된 데이터베이스 추가하기](sql-database-elastic-scale-add-a-shard.md)
8. [탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 애플리케이션](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [클라이언트 라이브러리 앱 업그레이드](sql-database-elastic-scale-upgrade-client-library.md) 
10. [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)
11. [탄력적 데이터베이스 도구 용어집](sql-database-elastic-scale-glossary.md)
12. [엔터티 프레임 작업과 함께 Elastic Database 클라이언트 라이브러리](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [탄력적 데이터베이스 클라이언트 라이브러리 및 Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [분할-병합 도구](sql-database-elastic-scale-overview-split-and-merge.md)
15. [분할된 맵 관리자에 대한 성능 카운터](sql-database-elastic-database-client-library.md) 
16. [탄력적 데이터베이스 도구 FAQ](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>클라이언트 기능

*분할* 을 사용한 애플리케이션 규모 확장은 개발자뿐 아니라 관리자에게도 과제를 제공합니다. 클라이언트 라이브러리는 개발자와 관리자가 규모 확장된 데이터베이스를 관리할 수 있는 도구를 제공하여 관리 작업을 간소화합니다. 일반적인 예로 "분할된 데이터베이스"라는 관리할 수많은 데이터베이스가 있습니다. 고객은 동일한 데이터베이스에 함께 배치되며 고객당 하나의 데이터베이스가 있습니다(단일 테넌트 체계). 클라이언트 라이브러리에는 다음과 같은 기능이 포함됩니다.

- **분할된 데이터베이스 맵 관리**: "분할된 데이터베이스 맵 관리자"라는 특수 데이터베이스가 만들어집니다. 분할된 데이터베이스 맵 관리는 분할된 데이터베이스에 대한 메타데이터를 관리하는 애플리케이션의 기능입니다. 개발자는 이 기능을 사용하여 데이터베이스를 분할된 데이터베이스처럼 등록하고, 개별 분할 키 또는 키 값 범위의 데이터베이스들 매핑을 설명하고, 숫자와 데이터베이스 조합이 용량 변경 내용을 반영하여 수정될 때 이 메타데이터를 유지 관리합니다. 탄력적 데이터베이스 클라이언트 라이브러리가 없으면 분할을 구현하는 경우 관리 코드를 작성하는데 많은 시간을 투자해야 합니다. 자세한 내용은 [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)를 참조하세요.

- **데이터 종속 라우팅**: 애플리케이션에 들어오는 요청을 가정해 보겠습니다. 요청의 분할 키 값에 따라 애플리케이션은 키 값을 기준으로 올바른 데이터베이스를 결정해야 합니다. 그런 다음 요청을 처리하는 데이터베이스에 대한 연결을 엽니다. 데이터 종속 라우팅은 애플리케이션의 분할된 데이터베이스 맵에 대한 간편한 단일 호출을 사용하여 연결을 열 수 있는 기능을 제공합니다. 데이터 종속 라우팅은 지금은 탄력적 데이터베이스 클라이언트 라이브러리의 기능에서 담당하는 인프라 코드의 다른 영역이었습니다. 자세한 내용은 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 참조하세요.
- **MSQ(다중 분할된 데이터베이스 쿼리)**: 요청에 여러(또는 모든) 분할된 데이터베이스가 포함된 경우 다중 분할된 데이터베이스 쿼리가 작동합니다. 다중 분할된 데이터베이스 쿼리는 모든 분할된 데이터베이스 또는 분할된 데이터베이스 집합에서 동일한 T-SQL 코드를 실행합니다. 참여하는 분할된 데이터베이스의 결과는 UNION ALL 의미 체계를 사용하는 전반적인 결과 집합으로 병합됩니다. 이 기능은 클라이언트 라이브러리를 통해 표시되며 연결 관리, 스레드 관리, 오류 처리, 중간 결과 처리 등의 여러 작업을 처리합니다. MSQ는 수백 개까지 분할된 데이터베이스를 쿼리할 수 있습니다. 자세한 내용은 [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md)를 참조하세요.

일반적으로, 고객의 탄력적 데이터베이스 도구 사용은 고유한 의미 체계를 가진 분할된 데이터베이스 간 작업과 반대로 분할된 데이터베이스 로컬 작업을 제출할 때 전체 T-SQL 기능을 가져올 수 있습니다.



## <a name="next-steps"></a>다음 단계

- Elastic Database 클라이언트 라이브러리([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) - 라이브러리를 **다운로드**합니다.

- [Elastic Database 도구 시작하기](sql-database-elastic-scale-get-started.md) - 클라이언트 함수를 설명하는 **샘플 앱**을 사용해 봅니다.

- GitHub([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) - 코드 개선에 참여합니다.
- [Azure SQL Database 탄력적 쿼리](sql-database-elastic-query-overview.md) - 탄력적 쿼리 사용.

- [확장 클라우드 데이터베이스 간  데이터 이동](sql-database-elastic-scale-overview-split-and-merge.md) - **split-merge 도구** 사용 지침



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png


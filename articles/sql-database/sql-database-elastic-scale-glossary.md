---
title: Elastic Database 도구 용어집 | Microsoft Docs
description: 탄력적 데이터베이스 도구에 쓰이는 용어 설명.
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
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585632"
---
# <a name="elastic-database-tools-glossary"></a>Elastic Database 도구 용어집

Azure SQL Database의 [Elastic Database 도구](sql-database-elastic-scale-introduction.md), 기능에 대해 정의된 용어는 다음과 같습니다. 이 도구는 [분할 데이터베이스 맵](sql-database-elastic-scale-shard-map-management.md)을 관리하는 데 사용하며 [클라이언트 라이브러리](sql-database-elastic-database-client-library.md), [분할-병합 도구](sql-database-elastic-scale-overview-split-and-merge.md), [탄력적 풀](sql-database-elastic-pool.md), [쿼리](sql-database-elastic-query-overview.md)를 포함합니다. 

이러한 용어는 [Elastic Database 도구를 사용하여 분할된 데이터베이스 추가](sql-database-elastic-scale-add-a-shard.md) 및 [RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵 문제 해결](sql-database-elastic-database-recovery-manager.md)에 사용됩니다.

![탄력적인 확장 용어][1]

**데이터베이스**: Azure SQL 데이터베이스입니다. 

**데이터 종속 라우팅**: 특정 분할 키가 있는 경우 애플리케이션에서 분할된 데이터베이스에 연결할 수 있는 기능입니다. [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 참조하세요.  **[Multi-Shard Query](sql-database-elastic-scale-multishard-querying.md)** 를 포함합니다.

**글로벌 분할된 데이터베이스 맵**: 분할 키와 **분할된 데이터베이스 세트** 내의 해당 분할된 데이터베이스 간 매핑입니다. 전역 분할된 데이터베이스 맵은 **분할된 데이터베이스 맵 관리자**에 저장됩니다. **로컬 분할된 데이터베이스 맵**과 비교할 수 있습니다.

**분할된 데이터베이스 목록 맵**: 분할 키가 개별적으로 매핑되는 분할된 데이터베이스 맵입니다. **범위 분할된 데이터베이스 맵**과 비교할 수 있습니다.   

**로컬 분할된 데이터베이스 맵**: 분할된 데이터베이스에 저장되어있는 로컬 분할된 데이터베이스 맵에는 분할된 데이터베이스에 있는 shardlet에 대한 매핑이 포함됩니다.

**다중 분할된 데이터베이스 쿼리**: 여러 분할된 데이터베이스에 대해 쿼리를 실행하는 기능: 결과 집합은 UNION ALL 의미 체계(또는 "팬아웃 쿼리")를 사용하여 반환됩니다. **데이터 종속 라우팅**과 비교할 수 있습니다.

**다중 테넌트** 및 **단일 테넌트**: 단일 테넌트 데이터베이스 및 다중 테넌트 데이터베이스를 보여줍니다.

![단일 및 다중 테넌트 데이터베이스](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

여기에 대표적인 **분할된** 단일 및 다중 테넌트 데이터베이스가 있습니다. 

![단일 및 다중 테넌트 데이터베이스](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**범위 분할된 데이터베이스 맵**: 분할된 데이터베이스 배포 전략이 인접 값의 여러 범위를 기반으로 하는 분할된 데이터베이스 맵입니다. 

**참조 테이블**: 분할되지 않았지만 여러 분할된 데이터베이스에 걸쳐 복제되는 테이블입니다. 예를 들어, 우편번호는 참조 테이블에 저장할 수 있습니다. 

**분할된 데이터베이스**: 분할된 데이터 세트의 데이터를 저장하는 Azure SQL 데이터베이스입니다. 

**분할된 데이터베이스 탄력성**: **수평 확장**과 **수직 확장**을 모두 수행할 수 있는 기능입니다.

**분할된 데이터베이스 테이블**: 분할되는 테이블, 즉 해당 데이터가 분할 키 값을 기반으로 여러 분할된 데이터베이스 간에 분산되는 테이블입니다. 

**분할 키**: 분할된 데이터베이스 간에 데이터를 배포하는 방법을 결정하는 열 값입니다. 값 형식은 **int**, **bigint**, **varbinary** 또는 **uniqueidentifier** 중 하나일 수 있습니다. 

**분할된 데이터베이스 세트**: 분할된 데이터베이스 맵 관리자에서 동일한 분할된 데이터베이스 맵 특성이 지정된 분할된 데이터베이스의 컬렉션입니다.  

**Shardlet**: 분할된 데이터베이스에서 분할 키의 단일 값과 연결된 모든 데이터입니다. Shardlet은 분할 테이블을 재분산할 때 가능한 가장 작은 데이터 이동 단위입니다. 

**분할된 데이터베이스 맵**: 분할 키와 해당 분할 간의 매핑 세트입니다.

**분할된 데이터베이스 맵 관리자**: 분할된 데이터베이스 맵, 분할 위치 및 하나 이상의 분할 세트에 대한 매핑을 포함하는 관리 개체 및 데이터 저장소입니다.

![매핑][2]

## <a name="verbs"></a>동사
**수평적 크기 조정**: 분할 맵에 분할을 추가하거나 제거하여 아래와 같이 분할 컬렉션을 규모 확장(또는 감축)하는 작업입니다.

![수평 및 수직적 크기 조정][3]

**병합**: 두 분할의 shardlet을 하나의 분할로 이동하고 이에 따라 분할 맵을 업데이트하는 작업입니다.

**Shardlet 이동**: 단일 shardlet를 다른 분할된 데이터베이스로 이동하는 작업입니다. 

**분할된 데이터베이스**:  분할 키를 기반으로 여러 데이터베이스에 걸쳐 동일하게 구조화된 데이터를 수평 분할하는 작업입니다.

**분할**: 하나의 분할된 데이터베이스에서 다른(일반적으로 새로운) 분할된 데이터베이스로 몇 개의 shardlet을 이동하는 작업입니다. 분할 키는 사용자가 분할 지점으로 지정합니다.

**수직 크기 조정**: 개별 분할된 데이터베이스의 컴퓨팅 크기를 확장 또는 축소하는 작업입니다. 예를 들어, 표준에서 프리미엄(더 많은 컴퓨팅 리소스 결과)으로 분할된 데이터베이스를 변경합니다. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png


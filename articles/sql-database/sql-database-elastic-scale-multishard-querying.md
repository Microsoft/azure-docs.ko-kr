---
title: 분할된 Azure SQL 데이터베이스 쿼리 | Microsoft Docs
description: 분할된 데이터베이스간의 쿼리를 실행할 때는 탄력적 데이터베이스 클라이언트 라이브러리를 사용합니다.
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
ms.date: 01/25/2019
ms.openlocfilehash: 35759f03d7cf09a4114ca6dca74bd3ee92fdcbfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761695"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Elastic Database 도구를 사용하여 쿼리 다중 분할

## <a name="overview"></a>개요

[Elastic Database 도구](sql-database-elastic-scale-introduction.md)를 사용하면 분할된 데이터베이스 솔루션을 만들 수 있습니다. **다중 분할된 데이터베이스 쿼리** 는 여러 분할된 데이터베이스 간에 걸친 쿼리를 실행해야 하는 데이터 컬렉션/보고와 같은 작업에 사용됩니다. (이 쿼리는 단일 분할된 데이터베이스에서 모든 작업을 수행하는 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)과 대조됩니다.)

1. **TryGetRangeShardMap**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) 또는 **GetShardMap**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) 메서드를 사용하여 **RangeShardMap**([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) 또는 **ListShardMap**([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1))을 가져옵니다. [ShardMapManager 생성](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) 및 [RangeShardMap 또는 ListShardMap 가져오기](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap)를 참조하세요.
2. **MultiShardConnection**([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)) 개체를 만듭니다.
3. **MultiShardStatement 또는 MultiShardCommand**([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand))를 만듭니다.
4. **CommandText 속성**([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand))을 T-SQL 명령으로 설정합니다.
5. **ExecuteQueryAsync 또는 ExecuteReader**([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) 메서드를 호출하여 이 명령을 실행합니다.
6. **MultiShardResultSet 또는 MultiShardDataReader**([Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)) 클래스를 사용하여 결과를 확인합니다.

## <a name="example"></a>예

다음 코드는 **myShardMap** 이라는 *ShardMap*을 사용하는 다중 분할된 데이터베이스 쿼리를 보여 줍니다.

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

주요 차이점은 다중 분할된 데이터베이스 연결을 생성한다는 것입니다. 여기서 **SqlConnection**은 개별 데이터베이스에서 작동하고 **MultiShardConnection**은 ***분할된 데이터베이스 컬렉션***을 입력으로 사용합니다. 분할된 데이터베이스 맵에서 분할된 데이터베이스의 컬렉션을 채웁니다. 그런 다음 쿼리는 **UNION ALL** 의미 체계를 사용하여 분할된 데이터베이스 컬렉션에서 실행되어 전반적인 단일 결과를 조합합니다. 필요에 따라 행이 속한 분할된 데이터베이스의 이름을 명령에서 **ExecutionOptions** 속성을 사용하여 출력에 추가할 수 있습니다.

**myShardMap.GetShards()** 호출을 살펴보세요. 이 메서드는 분할된 데이터베이스 맵에서 모든 분할된 데이터베이스를 검색하고 모든 관련된 데이터베이스간에 쿼리를 실행하는 간편한 방법을 제공합니다. 다중 분할된 데이터베이스 쿼리에 대한 분할된 데이터베이스의 컬렉션은 **myShardMap.GetShards()** 호출에서 반환된 컬렉션에 대해 LINQ 쿼리를 수행하여 구체화할 수 있습니다. 부분 결과 정책과 함께, 다중 분할된 데이터베이스 쿼리의 현재 기능은 수십에서 수백 개의 분할된 데이터베이스에 대해 원활하게 작동하도록 설계되었습니다.

다중 분할된 데이터베이스 쿼리의 제한 사항은 현재 쿼리가 실행되는 shardlet 및 분할된 데이터베이스에 대한 유효성 검사가 없다는 것입니다. 데이터 종속 라우팅이 쿼리 시 지정된 분할된 데이터베이스가 분할된 데이터베이스 맵의 일부인지 확인하며, 다중 분할된 데이터베이스 쿼리가 이 검사를 수행하지는 않습니다. 따라서 분할된 데이터베이스 맵에서 제거된 데이터베이스에서 다중 분할된 데이터베이스 쿼리가 실행될 수 있습니다.

## <a name="multi-shard-queries-and-split-merge-operations"></a>다중 분할된 데이터베이스 쿼리 및 분할/병합 작업

다중 분할된 데이터베이스 쿼리는 쿼리되는 데이터베이스의 shardlet이 지속적인 분할/병합 작업에 참여하는지 여부를 확인하지 않습니다. [Elastic Database 분할/병합 도구를 사용하여 확장하기](sql-database-elastic-scale-overview-split-and-merge.md)를 참조하세요. 따라서 동일한 shardlet의 행이 동일한 다중 분할된 데이터베이스 쿼리의 여러 데이터베이스에 대해 표시하는 경우 불일치가 발생할 수 있습니다. 이러한 제한 사항에 유의하고 다중 분할된 데이터베이스 쿼리를 수행하는 동안 사용되는 지속적인 분할-병합 작업과 분할된 데이터베이스 맵 변경 사항을 고려해야 합니다.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
---
title: 탄력적 데이터베이스 도구를 사용하여 분할된 데이터베이스 추가하기 | Microsoft Docs
description: 이 문서에서는 탄력적인 확장 API를 사용하여 새 분할된 데이터베이스를 분할된 데이터베이스 집합에 추가하는 방법을 설명합니다.
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
ms.date: 01/03/2019
ms.openlocfilehash: dda3c34dccfdaa041cf9f547244d5529482a3138
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585815"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Elastic Database 도구를 사용하여 분할된 데이터베이스 추가하기

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>새 범위 또는 키에 대해 분할된 데이터베이스를 추가하기

이미 존재하는 분할된 데이터베이스 맵의 경우 애플리케이션은 대개 새 키 또는 키 범위에서 예상되는 데이터를 처리할 새로운 분할된 데이터베이스를 추가해야 합니다. 예를 들어 테넌트 ID를 기준으로 분할된 애플리케이션이 새로운 테넌트에 대한 새로운 분할된 데이터베이스를 프로비전해야 할 수 있습니다. 또는 월별로 분할된 데이터의 경우 새로운 달이 시작되기 전에 새로운 분할된 데이터베이스를 프로비전해야 할 수 있습니다.

키 값의 새로운 범위가 기존 매핑에 속하지 않는 경우에는 간단하게 새 분할된 데이터베이스를 추가하고 새 키 또는 범위를 해당 분할된 데이터베이스에 연결할 수 있습니다.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>예 : 기존 분할된 데이터베이스 맵에 분할된 데이터베이스 및 해당 범위 추가

이 샘플에서는 TryGetShard([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) 메서드를 사용하며 ShardLocation([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) 클래스의 인스턴스를 만듭니다. 아래 샘플에서는 [300, 400) 범위를 포함하도록 **sample_shard_2** 데이터베이스와 이 데이터베이스 내의 필요한 모든 스키마 개체를 만들었습니다.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

.NET 버전의 경우 PowerShell을 대안으로 사용하여 새 분할된 데이터베이스 맵 관리자를 만들 수도 있습니다. [여기](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)에 예제가 있습니다.

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>기존 범위의 빈 부분에 대해 분할된 데이터베이스를 추가하기

특정 범위를 분할된 데이터베이스에 이미 매핑했으며 데이터를 일부분 추가했는데 그 이후에 들어오는 데이터는 다른 분할된 데이터베이스로 이동하려는 경우가 있을 수 있습니다. 일 범위로 데이터베이스를 분할했으며 분할된 데이터베이스에 이미 50일을 할당했는데 24일째부터는 추가 데이터를 다른 분할된 데이터베이스에 저장하려는 경우를 예로 들 수 있습니다. Elastic Database의 [분할-합병 도구](sql-database-elastic-scale-overview-split-and-merge.md)는 이 작업을 수행할 수는 있지만 25, 50 범위의 데이터, 즉, 25일째부터 50일째까지의 데이터가 없는 경우와 같이 데이터를 이동할 필요가 없는 경우에는 분할된 데이터베이스 맵 관리 API를 직접 사용하여 이 작업 전체를 수행할 수 있습니다.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>예: 범위를 분할하고 새로 추가한 분할된 데이터베이스에 빈 부분 할당

이 예제에서는 "sample_shard_2" 데이터베이스와 해당 데이터베이스 내의 필요한 모든 스키마 개체를 만듭니다.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**중요**:  업데이트되는 매핑의 범위가 비어 있는 것이 확실한 경우에만 이 기술을 사용하세요.  이전 방법에서는 이동하는 범위에서 데이터를 확인하지 않으므로 코드에 검사를 포함하는 것이 가장 좋습니다.  이동하는 범위에 행이 있으면 실제 데이터 분포가 업데이트된 분할된 데이터베이스 맵과 일치하지 않게 됩니다. 이 경우 대신 [분할-합병 도구](sql-database-elastic-scale-overview-split-and-merge.md) 를 사용하여 작업을 수행합니다.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

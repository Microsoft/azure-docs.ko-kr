---
title: Recovery Manager를 사용하여 분할된 데이터베이스 맵 문제 해결 | Microsoft Docs
description: RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵의 문제 해결
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
ms.openlocfilehash: 1bab1ed9e2a24b0a84f4327d47a910934319b397
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475903"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵 문제 해결

합니다 [RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) 클래스 ADO.NET 응용 프로그램 쉽게 감지 하 고 전역 분할 된 데이터베이스 맵 (GSM) 및 분할 된 데이터베이스 환경에서 로컬 분할 된 데이터베이스 맵 (LSM) 간의 모든 불일치를 해결 하는 기능을 제공 합니다.

GSM 및 LSM은 분할된 데이터베이스 환경에서 각 데이터베이스의 매핑을 추적합니다. 경우에 따라 GSM과 LSM 사이에서 중단이 발생합니다. 이 경우 RecoveryManager 클래스를 사용하여 중단을 검색하고 복구합니다.

RecoveryManager 클래스는 [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)에 포함됩니다.

![분할된 맵][1]

용어 정의는 [Elastic Database 도구 용어집](sql-database-elastic-scale-glossary.md)을 참조하세요. 분할된 데이터베이스 솔루션에서 데이터를 관리하는 데 **ShardMapManager** 가 어떻게 사용되는지 이해하려면 [분할된 데이터베이스 맵 관리](sql-database-elastic-scale-shard-map-management.md)를 참조하세요.

## <a name="why-use-the-recovery-manager"></a>복구 관리자를 사용하는 이유

분할된 데이터베이스 환경에서는 데이터베이스당 한 개의 테넌트가 있고 서버당 여러 데이터베이스가 있습니다. 또한 환경에 여러 서버가 있을 수도 있습니다. 분할된 데이터베이스 맵에서 각 데이터베이스를 매핑하므로 올바른 서버 및 데이터베이스로 호출을 라우트할 수 있습니다. 데이터베이스는 **분할 키**에 따라 추적되며 각 분할된 데이터베이스에는 **키 값 범위**가 할당됩니다. 예를 들어 분할 키는 "D"에서 "F"까지의 고객 이름을 나타낼 수 있습니다. 모든 분할된 데이터베이스의 매핑(즉, 데이터베이스) 및 매핑 범위는 **GSM(전역 분할된 데이터베이스 맵)** 에 포함됩니다. 각 데이터베이스에도 분할된 데이터베이스에 포함된 범위 맵이 포함되며 이를 **LSM(로컬 분할된 데이터베이스 맵)** 이라고 합니다. 앱이 분할된 데이터베이스에 연결될 때 빠른 검색을 위해 매핑이 앱과 함께 캐시됩니다. LSM은 캐시된 데이터의 유효성을 검사하는 데 사용됩니다.

GSM 및 LSM이 동기화되지 않는 이유는 다음과 같습니다.

1. 범위가 더 이상 사용되지 않는 분할된 데이터베이스가 삭제되거나 분할된 데이터베이스의 이름이 변경되었습니다. **분리되어 분할된 매핑**에서 분할된 데이터베이스 결과 삭제. 마찬가지로 이름이 바뀐 데이터베이스도 분리되어 분할된 데이터베이스 매핑을 발생시킬 수 있습니다. 변경 의도에 따라 분할된 데이터베이스를 제거하거나 분할된 데이터베이스 위치를 업데이트할 필요가 있습니다. 삭제된 데이터베이스를 복구하려면 [삭제된 데이터베이스 복구](sql-database-recovery-using-backups.md)를 참조하세요.
2. 지역 장애 조치(failover) 이벤트가 발생합니다. 계속하려면 분할된 데이터베이스 맵의 모든 분할된 데이터베이스에 대해 서버 이름과 애플리케이션에 있는 분할된 데이터베이스 맵 관리자의 데이터베이스 이름을 업데이트한 다음, 분할된 데이터베이스 매핑 정보를 업데이트해야 합니다. 지역 장애 조치의 경우 이러한 복구 논리는 장애 조치 워크플로 내에서 자동화됩니다. 복구 작업을 자동화하면 지역 지원 데이터베이스에 대해 원활한 관리 효율성을 제공하며 사람이 직접 작업하지 않아도 됩니다. 데이터 센터 중단 시 데이터베이스를 복구하는 옵션에 대해 알아보려면 [비즈니스 연속성](sql-database-business-continuity.md) 및 [재해 복구](sql-database-disaster-recovery.md)를 참조하세요.
3. 분할된 데이터베이스 또는 ShardMapManager 데이터베이스는 이전 시점으로 복원됩니다. 백업을 사용하여 시간 복구 지점에 대해 알아보려면 [백업을 사용하여 복구](sql-database-recovery-using-backups.md)를 참조하세요.

Azure SQL Database Elastic Database 도구, 지역 복제 및 복원에 대한 자세한 내용은 다음을 참조하세요.

* [개요: SQL Database의 클라우드 무중단 업무 방식 및 데이터베이스 재해 복구](sql-database-business-continuity.md)
* [탄력적 데이터베이스 도구 시작하기](sql-database-elastic-scale-get-started.md)  
* [ShardMap 관리](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>ShardMapManager에서 RecoveryManager 검색

첫 번째 단계는 RecoveryManager 인스턴스를 만드는 것입니다. [GetRecoveryManager 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager)는 현재 [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) 인스턴스에 대한 복구 관리자를 반환합니다. 분할된 데이터베이스 맵의 모든 불일치를 해결하려면 먼저 특정 분할된 데이터베이스 맵에 대한 RecoveryManager를 검색해야 합니다.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

이 예에서는 RecoveryManager가 ShardMapManager에서 초기화됩니다. ShardMap이 포함된 ShardMapManager도 이미 초기화됩니다.

이 애플리케이션 코드는 분할된 데이터베이스 맵 자체를 조작하므로 팩터리 메서드에 사용된 자격 증명(위의 예제에서는, smmConnectionString)은 연결 문자열에서 참조하는 GSM 데이터베이스에 대한 읽기-쓰기 권한이 있는 자격 증명이어야 합니다. 일반적으로 이러한 자격 증명은 데이터 종속 라우팅에 대한 연결을 여는 데 사용되는 자격 증명과는 다릅니다. 자세한 내용은 [탄력적 데이터베이스 클라이언트에 자격 증명 사용](sql-database-elastic-scale-manage-credentials.md)을 참조하세요.

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>분할된 데이터베이스를 삭제한 후 ShardMap에서 분할된 데이터베이스 제거

[DetachShard 메서드](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100)) 는 분할된 데이터베이스 맵에서 지정된 분할된 데이터베이스를 분리하고 여기에 연결된 매핑을 삭제합니다.  

* 위치 매개 변수는 분할된 데이터베이스 위치, 특히 분리 중인 분할된 데이터베이스의 서버 이름 및 데이터베이스 이름입니다.
* shardMapName 매개 변수는 분할된 데이터베이스 맵 이름입니다. 여러 분할된 데이터베이스 맵을 동일한 분할된 데이터베이스 맵 관리자가 관리하는 경우에만 필요합니다. 선택 사항입니다.

> [!IMPORTANT]
> 업데이트되는 매핑의 범위가 비어 있는 것이 확실한 경우에만 이 기술을 사용하세요. 위의 방법에서는 이동하는 범위에서 데이터를 확인하지 않으므로 코드에 검사를 포함하는 것이 가장 좋습니다.

이 예제에서는 분할된 데이터베이스 맵에서 분할된 데이터베이스를 제거합니다.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

분할된 데이터베이스 맵에 분할된 데이터베이스를 삭제하기 전에 GSM에서의 분할된 데이터베이스 위치가 반영됩니다. 분할된 데이터베이스는 삭제되었기 때문에 의도적인 것으로 간주되며 분할 키 범위는 더 이상 사용 중이 아닙니다. 그렇지 않은 경우 특정 시점 복원을 실행할 수 있습니다. 이전 시점에서 분할된 데이터베이스를 복구할 수 있습니다. (이 경우, 다음 섹션을 검토하여 분할된 데이터베이스의 불일치를 감지하세요.) 복구하려면 [특정 시점 복구](sql-database-recovery-using-backups.md)를 참조하세요.

데이터베이스 삭제를 의도적인 것으로 가정하므로 최종 관리 정리 작업은 분할된 데이터베이스 맵 관리자에서 분할된 데이터베이스에 대한 항목을 삭제하는 것입니다. 이렇게 하면 애플리케이션에서 예기치 않은 범위에 정보를 실수록 기록하는 일을 방지할 수 있습니다.

## <a name="to-detect-mapping-differences"></a>매핑 차이를 감지하려면

[DetectMappingDifferences 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) 는 원본으로 분할된 데이터베이스 맵(로컬 또는 전역) 중 하나를 선택하여 반환하고 두 분할된 데이터베이스 맵(GSM 및 LSM)에서 매핑을 조정합니다.

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* *위치* 는 서버 이름과 데이터베이스 이름을 지정합니다.
* *shardMapName* 매개 변수는 분할된 데이터베이스 맵 이름입니다. 여러 분할된 데이터베이스 맵을 동일한 분할된 데이터베이스 맵 관리자가 관리하는 경우에만 필요합니다. 선택 사항입니다.

## <a name="to-resolve-mapping-differences"></a>매핑 차이를 해결하려면

[ResolveMappingDifferences 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) 는 원본으로 분할된 데이터베이스 맵(로컬 또는 전역) 중 하나를 선택하고 두 분할된 데이터베이스 맵(GSM 및 LSM)에서 매핑을 조정합니다.

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* *RecoveryToken* 매개 변수는 특정 분할된 데이터베이스에 대한 GSM 및 LSM 간의 매핑에서 차이를 열거합니다.
* [MappingDifferenceResolution 열거](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) 는 분할된 데이터베이스 매핑 간 차이를 해결하기 위한 메서드를 나타내는 데 사용됩니다.
* **MappingDifferenceResolution.KeepShardMapping**은 LSM이 정확한 매핑을 포함하므로 분할된 데이터베이스의 매핑이 사용되는 경우에 권장됩니다. 이 방법은 장애 조치 시 일반적인 사례입니다. 이제 분할된 데이터베이스는 새 서버에 상주합니다. 분할된 데이터베이스는 GSM에서 먼저 제거되므로(RecoveryManager.DetachShard 메서드 사용) GSM에 매핑이 더 이상 존재하지 않습니다. 따라서 분할된 매핑을 다시 설정하기 위해 LSM을 사용해야 합니다.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>분할된 데이터베이스를 복원한 후 ShardMap에 연결합니다.

[AttachShard 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) 는 지정된 분할된 데이터베이스를 분할된 데이터베이스 맵에 연결합니다. 그런 다음 분할된 데이터베이스 맵의 불일치를 감지하고 분할된 데이터베이스 복원의 해당 지점에서 분할된 데이터베이스와 일치하도록 매핑을 업데이트합니다. 특정 시점 복원의 기본값은 타임스탬프가 추가된 새 데이터베이스이므로 데이터베이스가 원래 데이터베이스 이름(분할된 데이터베이스가 복원되기 이전)을 반영하도록 이름이 변경되었다고 가정합니다.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* *location* 매개 변수는 분리 중인 분할된 데이터베이스의 서버 이름 및 데이터베이스 이름입니다.
* *shardMapName* 매개 변수는 분할된 데이터베이스 맵 이름입니다. 여러 분할된 데이터베이스 맵을 동일한 분할된 데이터베이스 맵 관리자가 관리하는 경우에만 필요합니다. 선택 사항입니다.

이 예제는 분할된 데이터베이스를 최근 이전 시점에서 복원된 분할된 데이터베이스 맵에 추가합니다. 분할된 데이터베이스(즉, LSM에서 분할된 데이터베이스에 대한 매핑)를 복원한 후에는 GSM에서 분할된 데이터베이스 항목과 일치하지 않을 수 있습니다. 이 예제 코드 외부에서 분할된 데이터베이스가 복원되었고 데이터베이스의 원래 이름으로 바뀌었습니다. 복원된 후에는 LSM에서의 매핑이 신뢰할 수 있는 매핑으로 간주됩니다.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>분할된 데이터베이스의 지역 장애 조치(복원) 후 분할된 데이터베이스 위치 업데이트

지역 장애 조치 시, 보조 데이터베이스가 쓸 수 있는 상태가 되고 새로운 주 데이터베이스가 됩니다. 서버와 잠재적으로 데이터베이스의 이름(구성에 따라 다름)은 원래 주 데이터베이스와 다를 수 있습니다. 따라서 GSM 및 LSM에서 분할된 데이터베이스에 대한 매핑 항목을 고정해야 합니다. 마찬가지로, 데이터베이스가 다른 이름 또는 위치로 복원되거나 이전 시점으로 복원되면 분할된 데이터베이스 맵에서 불일치가 발생할 수 있습니다. 분할된 데이터베이스 맵 관리자가 올바른 데이터베이스에 대해 열려 있는 연결의 배포를 처리합니다. 배포는 분할된 맵의 데이터와 애플리케이션의 요청 대상인 분할 키의 값을 기반으로 합니다. 지역 장애 조치 후 이 정보를 정확한 서버 이름, 데이터베이스 이름 및 복구된 데이터베이스의 분할된 데이터베이스 매핑으로 업데이트해야 합니다.

## <a name="best-practices"></a>모범 사례

지역 장애 조치 및 복구는 의도적으로 Azure SQL 데이터베이스 비즈니스 연속성 기능 중 하나를 활용하는 애플리케이션의 클라우드 관리자가 일반적으로 관리하는 작업입니다. 비즈니스 연속성 계획에는 비즈니스 작업을 중단 없이 계속할 수 있도록 보장해주는 프로세스, 절차 및 측정이 필요합니다. 이 워크플로 내에서 RecoveryManager 클래스의 일부로 제공되는 메서드를 사용하여 수행되는 복구 작업에 따라 GSM 및 LSM이 최신 상태로 유지되도록 해야 합니다. 장애 조치 이벤트 후 GSM 및 LSM이 정확한 정보를 반영하도록 제대로 보장하는 5가지 기본 단계가 있습니다. 이러한 단계를 실행할 애플리케이션 코드를 기존 도구 및 워크플로에 통합할 수 있습니다.

1. ShardMapManager에서 RecoveryManager를 검색합니다.
2. 분할된 데이터베이스 맵에서 이전 분할된 데이터베이스를 분리합니다.
3. 새 분할된 데이터베이스 위치를 포함하여 새 분할된 데이터베이스를 분할된 데이터베이스 맵에 연결합니다.
4. GSM 및 LSM 간의 매핑에서 불일치를 감지합니다.
5. LSM을 신뢰하여 GSM 및 LSM 간의 차이를 해결합니다.

이 예제에서는 다음 단계를 수행합니다.

1. 장애 조치 이벤트 이전에, 분할된 데이터베이스 위치를 반영하는 분할된 데이터베이스 맵에서 분할된 데이터베이스를 제거합니다.
2. 분할된 데이터베이스를 새 분할된 데이터베이스 위치를 반영하는 분할된 데이터베이스 맵에 연결합니다("Configuration.SecondaryServer" 매개 변수는 새로운 서버 이름이지만 동일한 데이터베이스 이름입니다).
3. 각 분할된 데이터베이스에 대해 GSM 및 LSM 간의 매핑 차이를 감지하여 복구 토큰을 검색합니다.
4. 각 분할된 데이터베이스의 LSM에서 매핑을 신뢰하여 불일치를 해결합니다.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png

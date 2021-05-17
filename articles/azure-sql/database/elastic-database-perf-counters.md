---
title: 분할된 데이터베이스 맵 관리자에 대한 성능 카운터
description: ShardMapManager 클래스 및 데이터 종속 라우팅 성능 카운터
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: 3bfbf56b6e5f2be33b407945490531e6e2e8ac47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92781263"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>분할된 데이터베이스 맵 관리자의 성능을 추적하는 성능 카운터 만들기
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

성능 카운터는 [데이터 종속 라우팅](elastic-scale-data-dependent-routing.md) 작업의 성능을 추적하는 데 사용됩니다. 이러한 카운터는 “Elastic Database: 분할된 관리" 범주 아래 성능 모니터에서 액세스할 수 있습니다.

[분할된 맵 관리자](elastic-scale-shard-map-management.md)에 대한 성능은 특히, [데이터 종속 라우팅](elastic-scale-data-dependent-routing.md)을 사용하는 경우에 캡처할 수 있습니다. 카운터는 Microsoft.Azure.SqlDatabase.ElasticScale.Client 클래스의 메서드를 사용하여 만들 수 있습니다.  


**최신 버전은**[Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)로 이동합니다. 참고 항목: [최신 탄력적 데이터베이스 클라이언트 라이브러리를 사용하도록 앱 업그레이드](elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>사전 요구 사항

* 성능 범주 및 카운터를 만들려면, 애플리케이션을 호스트하는 머신의 로컬 **관리자** 그룹에 사용자가 속해야 합니다.  
* 성능 카운터 인스턴스를 만들고 카운터를 업데이트하려면, **관리자** 또는 **성능 모니터 사용자** 그룹에 사용자가 속해야 합니다.

## <a name="create-performance-category-and-counters"></a>성능 범주 및 카운터 만들기

카운터를 만들려면 [ShardMapManagementFactory 클래스](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)의 CreatePerformanceCategoryAndCounters 메서드를 호출합니다. 관리자만 메서드를 실행할 수 있습니다.

`ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()`

[이](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell 스크립트를 사용하여 메서드를 실행할 수도 있습니다.
메서드는 다음과 같은 성능 카운터를 만듭니다.  

* **캐시된 매핑**: 분할된 맵에 대해 캐시된 매핑의 수.
* **DDR 작업/초**: 분할된 맵에 대한 데이터 종속 라우팅 작업의 속도. 이 카운터는 [OpenConnectionForKey()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)에 대한 호출이 대상 분할에 대한 성공적인 연결로 이어지면 업데이트됩니다.
* **매핑 조회 캐시 적중/초**: 분할된 맵의 매핑에 대한 성공적인 캐시 조회 작업의 속도.
* **매핑 조회 캐시 누락/초**: 분할된 맵의 매핑에 대해 실패한 캐시 조회 작업의 속도.
* **캐시에 추가되거나 업데이트된 매핑/초**: 분할된 맵에 대해 캐시에 매핑이 추가되거나 업데이트된 속도.
* **캐시에서 제거된 매핑/초**: 분할된 맵에 대해 캐시에서 매핑이 제거되는 속도.

성능 카운터는 프로세스마다 각각의 캐시된 분할 맵에 생성됩니다.  

## <a name="notes"></a>참고

다음 이벤트는 성능 카운터 생성을 트리거합니다.  

* ShardMapManager에 분할된 맵이 포함된 경우, [즉시 로드](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)를 통한 [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy) 초기화. 여기에는 [GetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) 및 [TryGetSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) 메서드가 포함됩니다.
* 분할된 맵의 성공적인 조회([GetShardMap()](/previous-versions/azure/dn824215(v=azure.100)), [GetListShardMap()](/previous-versions/azure/dn824212(v=azure.100)) 또는 [GetRangeShardMap()](/previous-versions/azure/dn824173(v=azure.100)) 사용).
* CreateShardMap()을 사용한 분할된 맵의 성공적인 조회.

성능 카운터는 분할된 맵과 매핑에 수행되는 모든 캐시 작업에 의해 업데이트됩니다. DeleteShardMap()을 사용한 분할된 맵의 성공적인 제거는 성능 카운터 인스턴스의 삭제로 이어집니다.  

## <a name="best-practices"></a>모범 사례

* 성능 범주 및 카운터 생성은 ShardMapManager 개체를 만들기 전에 한 번만 수행되어야 합니다. CreatePerformanceCategoryAndCounters() 명령을 실행할 때마다 이전 카운터가 지워지고(모든 인스턴스에 의해 보고된 데이터 손실) 새 카운터가 생성됩니다.  
* 성능 카운터 인스턴스는 프로세스 마다 생성됩니다. 애플리케이션 작동이 중단되거나 분할된 맵이 캐시에서 제거되면 성능 카운터 인스턴스가 삭제됩니다.  

### <a name="see-also"></a>참고 항목

[Elastic Database 기능 개요](elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
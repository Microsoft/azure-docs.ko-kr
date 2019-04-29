---
title: 탄력적 데이터베이스 클라이언트 라이브러리에 대한 자격 증명 관리 | Microsoft Docs
description: 탄력적 데이터베이스 확장 앱에 대해 올바른 수준의 자격 증명(관리자부터 읽기 전용까지)을 설정하는 방법입니다.
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
ms.openlocfilehash: e6c7d682d9b16266208baeeff14168b3da157251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584882"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Elastic Database 클라이언트 라이브러리 액세스에 사용되는 자격 증명

[Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)는 세 가지 다른 종류의 자격 증명을 사용하여 [분할된 데이터베이스 맵 관리자](sql-database-elastic-scale-shard-map-management.md)에 액세스합니다. 필요에 따라서 가능한 한 액세스 수준이 가장 낮은 자격 증명을 사용합니다.

* **관리 자격 증명**: 분할된 데이터베이스 맵 관리자를 만들고 조작하는 데 사용됩니다. ( [용어집](sql-database-elastic-scale-glossary.md)을 참조하세요.)
* **액세스 자격 증명**: 분할된 데이터베이스에 대한 정보를 얻기 위해 기존의 분할된 데이터베이스 맵 관리자에 액세스하는 데 사용됩니다.
* **연결 자격 증명**: 분할된 데이터베이스에 연결하는 데 사용됩니다.

[Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)도 참조하세요.

## <a name="about-management-credentials"></a>관리 자격 증명 정보

관리 자격 증명은 분할된 데이터베이스 맵을 조작하는 애플리케이션에 대한 **ShardMapManager**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) 개체를 만드는 데 사용됩니다. (예를 들어 [Elastic Database 도구를 사용하여 분할된 데이터베이스 추가](sql-database-elastic-scale-add-a-shard.md) 및 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md)을 참조하세요.) 탄력적인 크기의 클라이언트 라이브러리 사용자는 SQL 사용자 및 SQL 로그인을 만들고 각각에 대해 글로벌 분할된 데이터베이스 맵 데이터베이스는 물론 모든 분할된 데이터베이스 맵에 대한 읽기/쓰기 권한을 부여합니다. 이러한 자격 증명은 분할된 데이터베이스 맵에 대한 변경을 수행할 때 전역 분할된 데이터베이스 맵 및 로컬 분할된 데이터베이스 맵을 유지 관리 하는 데 사용됩니다. 예를 들어 관리 자격 증명을 사용하여 분할된 데이터베이스 맵 관리자 개체를 만듭니다(**GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) 사용).

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

**smmAdminConnectionString** 변수는 관리 자격 증명을 포함하는 연결 문자열입니다. 사용자 ID와 암호는 분할된 데이터베이스 맵 데이터베이스와 개별 분할된 데이터베이스에 대한 읽기/쓰기 액세스를 제공합니다. 또한 관리 연결 문자열은 전역 분할된 데이터베이스 맵 데이터베이스를 식별하는 서버 이름과 데이터베이스 이름을 포함합니다. 다음은 해당 용도를 위한 일반적인 연결 문자열입니다.

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

username@server 형태의 값을 사용하지 말고, "username" 값을 사용합니다.  자격 증명은 분할된 데이터베이스 맵 관리자 데이터베이스와 각기 다른 서버에 있을 수 있는 개별 분할된 데이터베이스에 대해 모두 작동해야 하기 때문입니다.

## <a name="access-credentials"></a>액세스 자격 증명

분할된 데이터베이스 맵을 관리하지 않는 애플리케이션에 분할된 데이터베이스 맵 관리자를 만드는 경우, 글로벌 분할된 데이터베이스 맵에 대해 읽기 전용 권한이 있는 자격 증명을 사용합니다. 이러한 자격 증명으로 글로벌 분할된 데이터베이스 맵에서 검색한 정보는 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md) 에 사용되며, 클라이언트의 분할된 데이터베이스 맵 캐시를 채우는 데에도 사용됩니다. 자격 증명은 **GetSqlShardMapManager**에 대한 동일한 호출 패턴을 통해 제공됩니다.

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

**관리자가 아닌** 사용자를 대신하여 이 액세스에 대한 서로 다른 자격 증명의 사용을 반영하기 위해 **smmReadOnlyConnectionString**을 사용합니다. 이러한 자격 증명은 전역 분할된 데이터베이스 맵에 대한 쓰기 권한을 제공하지 않아야 하기 때문입니다.

## <a name="connection-credentials"></a>연결 자격 증명

**OpenConnectionForKey**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) 메서드를 사용하여 분할 키와 연결된 분할된 데이터베이스에 액세스할 때 추가 자격 증명이 필요합니다. 이러한 자격 증명은 분할된 데이터베이스에 있는 로컬 분할된 데이터베이스 맵 테이블에 대한 읽기 전용 액세스 권한을 제공해야 합니다. 이 권한은 분할된 데이터베이스의 데이터 종속 라우팅에 대한 연결 유효성 검사를 수행하는 데 필요합니다. 이 코드 조각은 데이터 종속 라우팅의 컨텍스트에서 데이터 액세스를 허용합니다.

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

이 예제에서 **smmUserConnectionString** 에는 사용자 자격 증명의 연결 문자열이 저장됩니다. Azure SQL DB의 경우 사용자 자격 증명에 대한 일반적인 연결 문자열은 다음과 같습니다.

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

관리 자격 증명과 마찬가지로 "username@server" 형태의 값을 사용하지 않습니다. 대신 "username"만 사용합니다.  또한 서버 이름과 데이터베이스 이름은 연결 문자열에 포함되지 않습니다. **OpenConnectionForKey** 호출은 키에 따라 올바른 분할된 데이터베이스로 연결을 자동으로 지정하기 때문입니다. 따라서 데이터베이스 이름과 서버 이름이 제공되지 않습니다.

## <a name="see-also"></a>참고 항목

[Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)

[SQL Database 보안 설정](sql-database-security-overview.md)

[Elastic Database 작업 시작](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

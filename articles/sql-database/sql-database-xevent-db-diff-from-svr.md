---
title: 확장 이벤트
description: Azure SQL Database의 확장 이벤트(XEvents)와 Microsoft SQL Server의 이벤트 세션 간 차이점에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213956"
---
# <a name="extended-events-in-sql-database"></a>SQL Database의 확장 이벤트
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

이 항목은 Azure SQL Database의 확장 이벤트 구현과 Microsoft SQL Server의 확장 이벤트 구현의 작은 차이점에 대해 설명합니다.

- SQL Database V12는 2015년 후반기 확장 이벤트 기능을 추가했습니다.
- SQL Server는 2008년 이후 확장 이벤트를 추가해 왔습니다.
- SQL Database의 확장 이벤트 기능 집합은 SQL Server 기능의 견고한 하위 집합입니다.

*XEvents*는 블로그 및 기타 비공식 위치에서 '확장 이벤트'를 가리키는 비공식적 별명입니다.

Azure SQL Database 및 Microsoft SQL Server용 확장 이벤트에 대한 추가 정보는 다음에 제공됩니다.

- [빠른 시작: SQL Server의 확장 이벤트](https://msdn.microsoft.com/library/mt733217.aspx)
- [확장 이벤트](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>사전 요구 사항

이 항목은 다음에 대한 어느 정도의 지식이 있는 것으로 가정합니다.

- [Azure SQL 데이터베이스 서비스](https://azure.microsoft.com/services/sql-database/).
- Microsoft SQL Server의 [Extended events](https://msdn.microsoft.com/library/bb630282.aspx).

- 이 설명서에서 확장 이벤트에 대한 내용의 많은 부분이 SQL Server와 SQL Database에 모두 적용됩니다.

이벤트 파일을 [대상](#AzureXEventsTargets)으로 선택할 경우 다음 항목에 대한 사전 지식이 있으면 도움이 됩니다.

- [Azure Storage 서비스](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Azure Storage와 함께 Azure PowerShell 사용](../storage/common/storage-powershell-guide-full.md) - PowerShell 및 Azure Storage 서비스에 대한 포괄적 정보를 제공합니다.

## <a name="code-samples"></a>코드 샘플

관련 항목에서 두 가지 코드 샘플을 제공합니다.


- [SQL Database의 확장 이벤트에 대한 링 버퍼 대상 코드](sql-database-xevent-code-ring-buffer.md)
    - 짧고 간단한 Transact-SQL 스크립트.
    - 코드 샘플 항목에서는 링 버퍼 대상을 마칠 때 alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` 문을 실행하여 리소스를 해제해야 함을 강조합니다. 나중에 `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`(으)로 링 버퍼의 다른 인스턴스를 추가할 수 있습니다.


- [SQL Database의 확장 이벤트에 대한 이벤트 파일 대상 코드](sql-database-xevent-code-event-file.md)
    - 1단계는 Azure Storage 컨테이너를 만드는 PowerShell입니다.
    - 2단계는 Azure Storage 컨테이너를 사용하는 Transact-SQL입니다.

## <a name="transact-sql-differences"></a>Transact-SQL 차이점


- SQL Server에서 [CREATE EVENT SESSION](https://msdn.microsoft.com/library/bb677289.aspx) 명령을 사용하는 경우 **ON SERVER** 절을 사용합니다. 하지만 SQL Database에서는 대신 **ON DATABASE** 절을 사용합니다.


- **ON DATABASE** 절은 [ALTER EVENT SESSION](https://msdn.microsoft.com/library/bb630368.aspx) 및 [DROP EVENT SESSION](https://msdn.microsoft.com/library/bb630257.aspx) Transact-SQL 명령에도 적용됩니다.


- **CREATE EVENT SESSION** 또는 **ALTER EVENT SESSION** 문에 **STARTUP_STATE = ON**의 이벤트 세션 옵션을 포함하는 것이 가장 좋습니다.
    - **= ON** 값은 장애 조치(failover)로 인해 논리적 데이터베이스를 재구성한 다음 자동 재시작을 지원합니다.

## <a name="new-catalog-views"></a>새 카탈로그 뷰

확장 이벤트 기능은 여러 [카탈로그 뷰](https://msdn.microsoft.com/library/ms174365.aspx)에서 지원합니다. 카탈로그 뷰를 통해 현재 데이터베이스에서 사용자가 만든 이벤트 세션의 *메타데이터 또는 정의* 를 확인할 수 있습니다. 뷰는 활성 이벤트 세션의 인스턴스에 대한 정보를 반환하지 않습니다.

| 카탈로그 뷰의<br/>이름 | 설명 |
|:--- |:--- |
| **sys.database_event_session_actions** |이벤트 세션의 각 이벤트의 동작에 대해 한 행을 반환합니다. |
| **sys.database_event_session_events** |이벤트 세션의 각 이벤트에 대한 행을 반환합니다. |
| **sys.database_event_session_fields** |이벤트 및 대상에 명시적으로 설정된 사용자 지정 가능한 각 열에 대한 행을 반환합니다. |
| **sys.database_event_session_targets** |이벤트 세션의 각 이벤트 대상에 대해 한 행을 반환합니다. |
| **sys.database_event_sessions** |SQL Database의 각 이벤트 세션에 대한 행을 반환합니다. |

Microsoft SQL Server에서 유사한 카탈로그 뷰의 이름에는 *.database\_* 가 아닌 *.server\_* 가 포함됩니다. 이름 패턴은 **sys.server_event_%** 와 같습니다.

## <a name="new-dynamic-management-views-dmvs"></a>새로운 [DMV](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database에는 확장 이벤트를 지원하는 [DMV(동적 관리 뷰)](https://msdn.microsoft.com/library/bb677293.aspx)가 있습니다. DMV를 통해 *활성* 이벤트 세션을 확인할 수 있습니다.

| DMV의 이름 | 설명 |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |이벤트 세션 동작에 대한 정보를 반환합니다. |
| **sys.dm_xe_database_session_events** |세션 이벤트에 대한 정보를 반환합니다. |
| **sys.dm_xe_database_session_object_columns** |세션에 바인딩된 개체의 구성 값을 표시합니다. |
| **sys.dm_xe_database_session_targets** |세션 작업에 대한 정보를 반환합니다. |
| **sys.dm_xe_database_sessions** |현재 데이터베이스로 범위가 한정된 각 이벤트 세션에 대한 행을 반환합니다. |

Microsoft SQL Server에서 다음과 같은 * \_이름의 데이터베이스* 부분 없이 유사한 카탈로그 보기의 이름이 지정됩니다.

- **sys.dm_xe_sessions**, 이름 대신<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>둘 다에 공통적인 DMV
확장 이벤트에는 Azure SQL Database와 Microsoft SQL Server에 공통적인 추가 DMV가 있습니다.

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

  <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>사용 가능한 확장 이벤트, 동작, 대상 찾기

간단한 SQL **SELECT** 를 실행하여 사용 가능한 이벤트, 작업, 대상의 목록을 가져올 수 있습니다.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```


<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-sql-database-event-sessions"></a>SQL Database 이벤트 세션의 대상

SQL Database에서 이벤트 세션의 결과를 캡처할 수 있는 대상은 다음과 같습니다.

- [링 버퍼 대상](https://msdn.microsoft.com/library/ff878182.aspx) - 이벤트 데이터를 메모리에 잠시 보관합니다.
- [이벤트 카운터 대상](https://msdn.microsoft.com/library/ff878025.aspx) - 확장 이벤트 세션 동안 발생하는 모든 이벤트의 수를 계산합니다.
- [이벤트 파일 대상](https://msdn.microsoft.com/library/ff878115.aspx) - Azure Storage 컨테이너에 전체 버퍼를 기록합니다.

[ETW(Windows 이벤트 추적)](https://msdn.microsoft.com/library/ms751538.aspx) API는 SQL Database의 확장 이벤트에서 사용할 수 없습니다.

## <a name="restrictions"></a>제한

SQL Database의 클라우드 환경에 적합한 몇 가지 보안 관련 차이점이 있습니다.

- 확장 이벤트는 단일 테넌트 격리 모델에서 찾을 수 있습니다. 한 데이터베이스의 이벤트 세션은 다른 데이터베이스의 데이터 또는 이벤트에 액세스할 수 없습니다.
- **마스터** 데이터베이스의 컨텍스트에서 **CREATE EVENT SESSION** 문을 실행할 수 없습니다.

## <a name="permission-model"></a>권한 모델

**CREATE EVENT SESSION** 문을 실행하려면 데이터베이스에 **제어** 권한이 있어야 합니다. 데이터베이스 소유자(dbo)는 **제어** 권한이 있습니다.

### <a name="storage-container-authorizations"></a>스토리지 컨테이너 권한

Azure Storage 컨테이너에 대해 만드는 SAS 토큰은 권한에 대해 **rwl** 을 지정해야 합니다. **rwl** 값은 다음과 같은 권한을 제공합니다.

- 읽기
- 쓰기
- 목록

## <a name="performance-considerations"></a>성능 고려 사항

확장 이벤트를 집중적으로 사용할 경우 전체 시스템에 안정적인 메모리보다 더 많은 활성 메모리가 누적되는 시나리오가 있습니다. 따라서 Azure SQL Database 시스템은 이벤트 세션이 누적할 수 있는 활성 메모리의 양에 대한 한도를 동적으로 설정 및 조정합니다. 많은 요소가 동적 계산에 활용됩니다.

메모리 최대 한도가 적용되었다는 오류 메시지가 표시될 경우 다음과 같은 방법으로 해결할 수 있습니다.

- 실행하는 동시 이벤트 세션 수를 줄입니다.
- 이벤트 세션에 대한 **CREATE** 및 **ALTER** 문을 통해 **MAX\_MEMORY** 절에 지정하는 메모리의 양을 줄입니다.

### <a name="network-latency"></a>네트워크 대기 시간

Azure Storage BLOB에 데이터를 유지하는 동안 **이벤트 파일** 대상에서 네트워크 지연 또는 오류가 발생할 수 있습니다. 네트워크 통신이 완료될 때까지 기다리는 동안 SQL Database의 다른 이벤트가 지연될 수 있습니다. 이 지연으로 인해 워크로드가 느려질 수 있습니다.

- 이러한 성능 위험을 줄이려면 이벤트 세션 정의에서 **EVENT_RETENTION_MODE** 옵션을 **NO_EVENT_LOSS**로 설정하지 마십시오.

## <a name="related-links"></a>관련 링크

- [Azure 저장소와 함께 Azure PowerShell 을 사용 하 여](../storage/common/storage-powershell-guide-full.md)입니다.
- [Azure Storage Cmdlet](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Azure Storage와 함께 Azure PowerShell 사용](../storage/common/storage-powershell-guide-full.md) - PowerShell 및 Azure Storage 서비스에 대한 포괄적 정보를 제공합니다.
- [.NET에서 Blob Storage를 사용하는 방법](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL(Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION(Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Microsoft SQL Server의 확장 이벤트에 대한 Jonathan Kehayias의 블로그](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Azure SQL Database에 대한 매개 변수로 범위가 좁혀지는 Azure *서비스 업데이트* 웹 페이지:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


확장 이벤트에 대한 다른 코드 샘플 항목은 다음 링크에서 사용할 수 있습니다. 하지만 어느 샘플이든 Azure SQL Database와 Microsoft SQL Server 중 무엇을 대상으로 하는지 늘 확인해야 합니다. 그런 다음 샘플을 실행하기 위해 약간의 변경이 필요한지 결정할 수 있습니다.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->

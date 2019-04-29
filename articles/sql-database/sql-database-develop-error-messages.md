---
title: SQL 오류 코드 - 데이터베이스 연결 오류 | Microsoft Docs
description: '일반적인 데이터베이스 연결 오류, 데이터베이스 복사 문제 및 일반적인 오류와 같은 SQL Database 클라이언트 애플리케이션에 대한 SQL 오류 코드에 대해 알아봅니다. '
keywords: SQL 오류 코드, 액세스 SQL, 데이터베이스 연결 오류, SQL 오류 코드
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/06/2019
ms.openlocfilehash: 2682f98628f3c1cf22a2c3767f52bedbc148fa62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723499"
---
# <a name="sql-error-codes-for-sql-database-client-applications-database-connection-errors-and-other-issues"></a>SQL Database 클라이언트 애플리케이션에 대한 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제

이 문서에서는 데이터베이스 연결 오류, 일시적인 오류(일시적인 폴트라고도 함), 리소스 거버넌스 오류, 데이터베이스 복사 문제, 탄력적 풀 및 기타 오류를 포함하여 SQL Database 클라이언트 애플리케이션의 SQL 오류 코드를 나열합니다. 대부분의 범주는 Azure SQL Database에 특정되며 Microsoft SQL Server에 적용되지 않습니다. [시스템 오류 메시지](https://technet.microsoft.com/library/cc645603(v=sql.105).aspx)를 참조하세요.

## <a name="database-connection-errors-transient-errors-and-other-temporary-errors"></a>데이터베이스 연결 오류, 일시적인 오류 및 기타 임시 오류

다음 표에서는 연결 끊김 오류와 애플리케이션에서 SQL Database에 액세스하려고 할 때 발생할 수 있는 기타 일시적인 오류에 대한 SQL 오류 코드를 보여줍니다. Azure SQL Database에 연결하는 방법에 대한 시작 자습서를 보려면 [Azure SQL Database에 연결](sql-database-libraries.md)을 참조하세요.

### <a name="most-common-database-connection-errors-and-transient-fault-errors"></a>가장 일반적인 데이터베이스 연결 오류 및 일시적인 오류

Azure 인프라에 는 SQL Database 서비스에 과도한 워크로드 부하가 발생하는 경우 서버를 동적으로 다시 구성 하는 기능이 있습니다.  이러한 동적인 동작으로 인해 클라이언트 프로그램의 SQL Database에 대한 연결이 손실될 수 있습니다. 이러한 종류의 오류 상황을 *일시적 장애*라고 합니다.

자체 해결을 위한 일시적 오류 시간이 지난 후 연결을 다시 시도할 수 있도록 클라이언트 프로그램에 재시도 논리가 있는 것이 좋습니다.  첫 번째 재시도 전에 5초간 지연하는 것이 좋습니다. 5초보다 짧은 지연 후 재시도는 클라우드 서비스에 많은 위험이 있습니다. 각 후속 재시도에 대해 지연 시간은 최대 60초까지 기하급수적으로 증가해야 합니다.

일시적인 오류는 일반적으로 클라이언트 프로그램에서 다음 오류 메시지 중 하나로 표시됩니다.

* &lt;Azure_instance&gt; 서버에서 &lt;db_name&gt; 데이터베이스를 현재 사용할 수 없는 경우. 나중에 연결을 다시 시도하십시오. 문제가 지속되면 고객 지원에 문의하고 &lt;session_id&gt;의 세션 추적 Id를 제공합니다.
* &lt;Azure_instance&gt; 서버에서 &lt;db_name&gt; 데이터베이스를 현재 사용할 수 없는 경우. 나중에 연결을 다시 시도하십시오. 문제가 지속되면 고객 지원에 문의하고 &lt;session_id&gt;의 세션 추적 Id를 제공합니다. (Microsoft SQL Server, 오류: 40613)
* 기존 연결이 원격 호스트에 의해 강제로 끊겼습니다.
* System.Data.Entity.Core.EntityCommandExecutionException: 명령 정의를 실행하는 중에 오류가 발생했습니다. 자세한 내용은 내부 예외를 참조하세요. ---> System.Data.SqlClient.SqlException: 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: 세션 공급자, 오류: 19 - 실제 연결을 사용할 수 없습니다.)
* 데이터베이스가 재구성 프로세스 중이며 주 데이터베이스에서 활성 거래 중에 새 페이지를 적용하고 있으므로 보조 데이터베이스에 연결하지 못했습니다. 

다시 시도 논리의 코드 예제는 다음을 참조하십시오.

* [SQL Database 및 SQL Server용 연결 라이브러리](sql-database-libraries.md) 
* [SQL Database에서 연결 오류 및 일시적 오류를 해결하는 작업](sql-database-connectivity-issues.md)

ADO.NET을 사용하는 클라이언트에 대한 *차단 기간* 의 설명은 [SQL Server 연결 풀링(ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)에서 사용 가능합니다.

### <a name="transient-fault-error-codes"></a>일시적 오류 코드

다음 오류는 일시적이며, 애플리케이션 논리에서 다시 시도해야 합니다. 

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 4060 |16 |로그인에서 요청된 데이터베이스 "%.&#x2a;ls"을(를) 열 수 없습니다. 로그인이 실패했습니다. 자세한 내용은 참조 하세요. [4000 ~ 4999 오류](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |서비스에서 요청을 처리하는 오류가 발생했습니다. 나중에 다시 시도하세요. 오류 코드 %d.<br/><br/>소프트웨어 또는 하드웨어 업그레이드, 하드웨어 오류 또는 기타 장애 조치 문제로 인해 서비스가 종료되는 경우 이 오류가 발생합니다. 오류 40197 메시지 내에 포함된 오류 코드(%d)는 발생한 오류 또는 장애 조치의 종류에 대한 추가 정보를 제공합니다. 오류 40197 메시지 내에 포함된 오류 코드의 일부 예제는 40020, 40143, 40166 및 40540입니다.<br/><br/>SQL Database 서버에 다시 연결하면 데이터베이스의 정상 복사본으로 자동 연결됩니다. 애플리케이션은 오류 40197을 포착하고 문제 해결을 위해 메시지 내에 포함된 오류 코드(%d)를 로그하고 리소스가 사용 가능하고 연결이 다시 설정될 때까지 SQL Database에 다시 접속을 시도해야 합니다. 자세한 내용은 [일시적인 오류](sql-database-connectivity-issues.md#transient-errors-transient-faults)합니다.|
| 40501 |20 |서비스가 현재 사용 중입니다. 10초 후 요청을 다시 시도하십시오. 인시던트 ID: %ls. 코드: %d. 자세한 내용은 다음을 참조하세요. <br/>&bull; &nbsp;[데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다.|
| 40613 |17 |서버의 데이터베이스 '%.&#x2a;ls' '%.&#x2a;ls'을(를) 사용할 수 없습니다. 나중에 연결을 다시 시도하십시오. 문제가 지속되면 고객 지원 서비스에 문의하고 세션 추적 ID '%.&#x2a;ls'을(를) 제공하십시오.<br/><br/> 기존 관리자 전용된 연결 (DAC) 데이터베이스에 이미 있으면이 오류가 발생할 수 있습니다. 자세한 내용은 [일시적인 오류](sql-database-connectivity-issues.md#transient-errors-transient-faults)합니다.|
| 49918 |16 |요청을 처리할 수 없습니다. 요청을 처리할 리소스가 부족합니다.<br/><br/>서비스가 현재 사용 중입니다. 요청을 나중에 다시 시도하세요. 자세한 내용은 다음을 참조하세요. <br/>&bull; &nbsp;[데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다. |
| 49919 |16 |요청을 만들거나 업데이트하는 처리를 할 수 없습니다. 구독 "%ld"에 대해 진행 중인 작업을 너무 많이 만들거나 업데이트합니다.<br/><br/>서비스가 구독 또는 서버에 대한 여러 요청을 만들거나 업데이트하는 처리로 사용 중입니다. 요청은 현재 리소스 최적화에 대해 차단됩니다. 보류 중인 작업에 대해 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx)를 쿼리합니다. 만들기 또는 업데이트를 보류 중인 요청이 완료되거나 보류 중인 요청 중 하나를 삭제할 때까지 대기하고 나중에 요청을 다시 시도합니다. 자세한 내용은 다음을 참조하세요. <br/>&bull; &nbsp;[데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다. |
| 49920 |16 |요청을 처리할 수 없습니다. 구독 "%ld"에 대해 진행 중인 작업을 너무 많습니다.<br/><br/>서비스가 구독에 대한 여러 요청을 처리하는 데 사용 중입니다. 요청은 현재 리소스 최적화에 대해 차단됩니다. 작업 상태에 대해 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx)를 쿼리합니다. 보류 중인 요청이 완료되거나 보류 중인 요청 중 하나를 삭제할 때까지 대기하고 나중에 요청을 다시 시도합니다. 자세한 내용은 다음을 참조하세요. <br/>&bull; &nbsp;[데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다. |
| 4221 |16 |'HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING'에서 긴 대기로 인해 읽기 보조에 대한 로그인에 실패했습니다. 행 버전이 복제본이 재활용될 때 처리 중이었던 트랜잭션에 대해 누락되었기 때문에 로그인에 복제본을 사용할 수 없습니다. 주 복제본에서 활성 트랜잭션을 롤백하거나 커밋하여 문제를 해결할 수 있습니다. 주 복제본에서 긴 쓰기 트랜잭션을 방지하여 이 조건의 발생을 최소화할 수 있습니다. |

## <a name="database-copy-errors"></a>데이터베이스 복사 오류

Azure SQL Database에서 데이터베이스를 복사하는 동안 다음 오류가 발생할 수 있습니다. 자세한 내용은 [Azure SQL Database 복사](sql-database-copy.md)를 참조하세요.

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 40635 |16 |IP 주소 '%.&#x2a;ls'을(를) 사용하는 클라이언트가 일시적으로 비활성화되었습니다. |
| 40637 |16 |데이터베이스 복사본 만들기를 현재 사용할 수 없습니다. |
| 40561 |16 |데이터베이스를 복사하지 못했습니다. 원본 또는 대상 데이터베이스가 존재하지 않습니다. |
| 40562 |16 |데이터베이스를 복사하지 못했습니다. 원본 데이터베이스가 삭제되었습니다. |
| 40563 |16 |데이터베이스를 복사하지 못했습니다. 대상 데이터베이스가 삭제되었습니다. |
| 40564 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40565 |16 |데이터베이스를 복사하지 못했습니다. 동일한 소스에서 1개의 동시 데이터베이스 복사본이 허용됩니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |
| 40566 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40567 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40568 |16 |데이터베이스를 복사하지 못했습니다. 원본 데이터베이스를 사용할 수 없습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40569 |16 |데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 사용할 수 없습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40570 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |
| 40571 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |

## <a name="resource-governance-errors"></a>리소스 관리 오류

다음 오류는 Azure SQL Database를 사용하여 작업하는 동안 리소스를 과도하게 사용하여 발생합니다. 예를 들면 다음과 같습니다.

* 트랜잭션이 너무 오랫동안 열려 있었습니다.
* 트랜잭션이 너무 많은 잠금을 보유하고 있습니다.
* 애플리케이션이 너무 많은 메모리를 사용하고 있습니다.
* 애플리케이션이 너무 많은 `TempDb` 공간을 사용하고 있습니다.

관련 항목:

* 자세한 내용은 다음을 참조하세요.
  * [데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)
  * [단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)
  * [탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)
  * [단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)
  * [탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)
  * [인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다. 

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 10928 |20 |리소스 ID: %d입니다. 데이터베이스에 대한 %s 제한이 %d이며 이 제한에 도달했습니다. 자세한 내용은 [단일 데이터베이스 및 풀링된 데이터베이스에 대한 SQL Database 리소스 한도](sql-database-resource-limits-database-server.md)를 참조하세요.<br/><br/>리소스 ID는 제한에 도달한 리소스를 나타냅니다. 작업자 스레드의 경우 리소스 ID = 1입니다. 세션의 경우 리소스 ID = 2입니다.<br/><br/>이 오류 및 문제를 해결하는 방법에 대한 자세한 내용은 다음을 참조하세요. <br/>&bull; &nbsp;[데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다. |
| 10929 |20 |리소스 ID: %d입니다. %s의 최소 보장은 %d이며, 최대 한도는 %d이고, 해당 데이터베이스의 현재 사용량은 %d입니다. 하지만 현재 서버 사용량이 너무 많아 해당 데이터베이스에 대해 %d 이상의 요청을 지원할 수 없습니다. 리소스 ID는 제한에 도달한 리소스를 나타냅니다. 작업자 스레드의 경우 리소스 ID = 1입니다. 세션의 경우 리소스 ID = 2입니다. 자세한 내용은 다음을 참조하세요. <br/>&bull; &nbsp;[데이터베이스 서버 리소스 제한](sql-database-resource-limits-database-server.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 DTU 기반 제한](sql-database-service-tiers-dtu.md)<br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다. <br/>그렇지 않으면 나중에 다시 시도하세요. |
| 40544 |20 |데이터베이스가 크기 할당량에 도달했습니다. 데이터를 분할 또는 삭제하거나 인덱스를 삭제하거나 가능한 해결 방법에 대한 설명서를 참조하십시오. 데이터베이스 크기 조정에 대 한 참조 [단일 데이터베이스 리소스의 크기 조정](sql-database-single-database-scale.md) 하 고 [탄력적 풀 리소스 크기를 조정](sql-database-elastic-pool-scale.md)합니다.|
| 40549 |16 |실행 시간이 긴 트랜잭션이 있으므로 세션이 종료됩니다. 트랜잭션 실행 시간을 줄이십시오. 일괄 처리에 대 한 내용은 참조 하세요 [SQL Database 응용 프로그램 성능 향상을 위해 일괄 처리를 사용 하는 방법을](sql-database-use-batching-to-improve-performance.md)합니다.|
| 40550 |16 |너무 많은 잠금을 획득하여 세션이 종료되었습니다. 단일 트랜잭션에서 읽기 또는 수정 행 수를 줄이십시오. 일괄 처리에 대 한 내용은 참조 하세요 [SQL Database 응용 프로그램 성능 향상을 위해 일괄 처리를 사용 하는 방법을](sql-database-use-batching-to-improve-performance.md)합니다.|
| 40551 |16 |과도한 `TEMPDB` 사용으로 인해 세션이 종료되었습니다. 임시 테이블 공간 사용량을 줄이도록 쿼리를 수정하세요.<br/><br/>임시 개체를 사용하는 경우 세션에서 더 이상 필요하지 않을 때 임시 개체를 삭제하여 `TEMPDB` 데이터베이스의 공간을 절약하세요. SQL Database의 tempdb 사용량에 대 한 자세한 내용은 참조 하세요. [SQL Database의 Tempdb 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)합니다.|
| 40552 |16 |과도한 트랜잭션 로그 공간 사용으로 인해 세션이 종료되었습니다. 단일 트랜잭션에서 행 수를 줄이세요. 일괄 처리에 대 한 내용은 참조 하세요 [SQL Database 응용 프로그램 성능 향상을 위해 일괄 처리를 사용 하는 방법을](sql-database-use-batching-to-improve-performance.md)합니다.<br/><br/>`bcp.exe` 유틸리티 또는 `System.Data.SqlClient.SqlBulkCopy` 클래스를 사용하여 대량 삽입을 수행하는 경우 `-b batchsize` 또는 `BatchSize` 옵션을 사용하여 각 트랜잭션에서 서버로 복사된 행의 수를 제한하세요. `ALTER INDEX` 문을 사용하여 인덱스를 다시 작성하는 경우 `REBUILD WITH ONLINE = ON` 옵션을 사용하여 시도하십시오. VCore 구매 모델에 대 한 트랜잭션 로그 크기에 대 한 내용은 다음을 참조 하세요. <br/>&bull; &nbsp;[단일 데이터베이스에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[인스턴스 리소스 제한을 관리](sql-database-managed-instance-resource-limits.md)합니다.|
| 40553 |16 |과도한 메모리 사용으로 인해 세션이 종료되었습니다. 처리할 행 수를 줄이도록 쿼리를 수정하세요.<br/><br/>Transact-SQL 코드에서 `ORDER BY` 및 `GROUP BY` 작업의 수를 줄이면 쿼리의 메모리 요구 사항이 줄어듭니다. 데이터베이스 크기 조정에 대 한 참조 [단일 데이터베이스 리소스의 크기 조정](sql-database-single-database-scale.md) 하 고 [탄력적 풀 리소스 크기를 조정](sql-database-elastic-pool-scale.md)합니다.|

## <a name="elastic-pool-errors"></a>탄력적 풀 오류

다음 오류는 탄력적 풀 만들기 및 사용하기와 관련이 있습니다.

| 오류 코드 | 심각도 | 설명 | 정정 작업 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |탄력적 풀이 저장소 용량 한도에 도달했습니다. 탄력적 풀의 저장소 사용량은 (%d)MB를 초과할 수 없습니다. 탄력적 풀이 저장소 용량 한도에 도달했을 때 데이터베이스에 데이터를 기록하려고 했습니다. 리소스 제한에 대 한 내용은 다음을 참조 하세요. <br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)합니다. <br/> |가능하다면 탄력적 풀의 DTU를 늘리거나 탄력적 풀에 저장소를 추가하여 저장소 용량 한도를 늘리거나, 탄력적 풀에 있는 개별 데이터베이스에서 사용하는 저장소를 줄이거나, 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. 탄력적 풀 크기 조정에 대 한 참조 [탄력적 풀 리소스 크기를 조정](sql-database-elastic-pool-scale.md)합니다.|
| 10929 | 16 |%s의 최소 보장은 %d이며, 최대 한도는 %d이고, 해당 데이터베이스의 현재 사용량은 %d입니다. 하지만 현재 서버 사용량이 너무 많아 해당 데이터베이스에 대해 %d 이상의 요청을 지원할 수 없습니다. 리소스 제한에 대 한 내용은 다음을 참조 하세요. <br/>&bull; &nbsp;[탄력적 풀의 DTU 기반 제한](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull; &nbsp;[탄력적 풀에 대 한 vCore 기반 제한](sql-database-vcore-resource-limits-elastic-pools.md)합니다. <br/> 그렇지 않으면 나중에 다시 시도하세요. 데이터베이스당 DTU/vCore 최솟값, 데이터베이스당 DTU/vCore 최댓값. 탄력적 풀에 있는 전체 데이터베이스의 동시 작업자(요청) 수 합계가 풀 한도를 초과하려고 했습니다. |가능하다면 탄력적 풀의 DTU 또는 vCore를 늘려 작업자 한도를 늘리거나 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 40844 | 16 |서버 '%ls'에 있는 데이터베이스 '%ls'은(는) 탄력적 풀에 포함된 '%ls' 버전 데이터베이스이며, 연속 복사 관계를 가질 수 없습니다.  |N/A |
| 40857 | 16 |서버: '%ls'에서 탄력적 풀을 찾을 수 없음, 탄력적 풀 이름: '%ls'. 지정한 탄력적 풀이 지정한 서버에 존재하지 않습니다. | 유효한 탄력적 풀 이름을 입력하세요. |
| 40858 | 16 |탄력적 풀 '%ls'이(가) 서버 '%ls'에 이미 있습니다. 지정한 탄력적 풀이 지정한 SQL Database 서버에 이미 있습니다. | 새 탄력적 풀 이름을 입력하세요. |
| 40859 | 16 |탄력적 풀이 서비스 계층 '%ls'을(를) 지원하지 않습니다. 지정한 서비스 계층은 탄력적 풀 프로비저닝에 대해 지원되지 않습니다. |기본 서비스 계층을 사용하려면 오류를 수정하거나 서비스 계층을 빈 상태로 두세요. |
| 40860 | 16 |탄력적 풀 '%ls' 및 서비스 목표'%ls'의 조합은 유효하지 않습니다. 리소스 종류가 'ElasticPool'로 지정된 경우에만 탄력적 풀과 서비스 계층을 함께 지정할 수 있습니다. |탄력적 풀과 서비스 계층의 올바른 조합을 지정하세요. |
| 40861 | 16 |데이터베이스 버전 '%.*ls'이(가) '%.* ls'인 탄력적 풀 서비스 계층과 다를 수 없습니다. 데이터베이스 버전이 탄력적 풀 서비스 계층과 다릅니다. |탄력적 풀 서비스 계층과 다른 데이터베이스 버전을 지정하지 마세요.  데이터베이스 버전은 지정할 필요가 없습니다. |
| 40862 | 16 |탄력적 풀 서비스 목표를 지정한 경우 탄력적 풀 이름을 지정해야 합니다. 탄력적 풀 서비스 목표가 탄력적 풀을 고유하게 식별하지 못합니다. |탄력적 풀 서비스 목표를 사용하는 경우 탄력적 풀 이름을 지정하세요. |
| 40864 | 16 |탄력적 풀의 DTU는 서비스 계층 '%.*ls'에 대해 최소 (%d) DTU 이상이어야 합니다. 탄력적 풀의 DTU를 최소 한도 아래로 설정하려고 했습니다. |탄력적 풀의 DTU를 최소 한도 이상으로 다시 설정하세요. |
| 40865 | 16 |탄력적 풀의 DTU는 서비스 계층 '%.*ls'에 대해 (%d) DTU를 초과할 수 없습니다. 탄력적 풀의 DTU를 최대 한도 위로 설정하려고 했습니다. |탄력적 풀의 DTU를 최대 한도 미만으로 다시 설정하세요. |
| 40867 | 16 |데이터베이스당 DTU 최대값은 서비스 계층 '%.*ls'에 대해 최소 (%d) 이상이어야 합니다. 데이터베이스당 DTU 최대값을 지원되는 한도 아래로 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40868 | 16 |데이터베이스당 DTU 최대값은 서비스 계층 '%.*ls'에 대해 (%d)을(를) 초과할 수 없습니다. 데이터베이스당 DTU 최대값을 지원되는 한도를 초과하여 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40870 | 16 |데이터베이스당 DTU 최소값은 서비스 계층 '%.*ls'에 대해 (%d)을(를) 초과할 수 없습니다. 데이터베이스당 DTU 최소값을 지원되는 한도를 초과하여 설정하려고 했습니다. | 원하는 설정을 지원하는 탄력적 풀 서비스 계층을 사용하는 것을 고려하세요. |
| 40873 | 16 |데이터베이스 수(%d) 및 데이터베이스당 DTU 최소값(%d)은 탄력적 풀의 DTU(%d)를 초과할 수 없습니다. 탄력적 풀에 있는 데이터베이스의 DTU 최소값을 탄력적 풀의 DTU를 초과하는 값으로 지정하려고 했습니다. | 탄력적 풀의 DTU를 늘리거나, 데이터베이스당 DTU 최소값을 줄이거나, 탄력적 풀에 포함된 데이터베이스 수를 줄이는 것을 고려하세요. |
| 40877 | 16 |탄력적 풀에 데이터베이스가 포함되어 있으면 삭제할 수 없습니다. 탄력적 풀에 하나 이상의 데이터베이스가 포함되어 있으므로 삭제할 수 없습니다. |탄력적 풀을 삭제하려면 탄력적 풀에서 데이터베이스를 제거하세요. |
| 40881 | 16 |탄력적 풀 '%.*ls'이(가) 데이터베이스 개수 한도에 도달했습니다.  탄력적 풀의 데이터베이스 개수 한도는 DTU가 (%d)인 탄력적 풀의 경우 (%d)을(를) 초과할 수 없습니다. 탄력적 풀의 데이터베이스 개수 한도가 도달된 상태에서 탄력적 풀에 데이터베이스를 만들거나 추가하려고 했습니다. | 가능하다면 탄력적 풀의 DTU를 늘려 데이터베이스 한도를 확대하거나 탄력적 풀에서 데이터베이스를 제거하는 것을 고려하세요. |
| 40889 | 16 |데이터베이스에 충분한 저장소 공간을 제공하지 못하게 되므로 저장소 풀'%.*ls'의 DTU 또는 저장소 한도를 줄일 수 없습니다. 탄력적 풀의 저장소 한도를 저장소 사용량 아래로 줄이려고 했습니다. | 탄력적 풀에 있는 개별 데이터베이스의 저장소 사용량을 줄이거나 풀에서 데이터베이스를 제거하여 DTU 또는 저장소 한도를 줄이는 것을 고려하세요. |
| 40891 | 16 |데이터베이스당 DTU 최소값 (%d)은(는) 데이터베이스당 DTU 최대값 (%d)을(를) 초과할 수 없습니다. 데이터베이스당 DTU 최소값을 데이터베이스당 DTU 최대값보다 높게 설정하려고 했습니다. |데이터베이스당 DTU 최소값이 데이터베이스당 DTU 최대값을 초과하지 않도록 하세요. |
| TBD | 16 |탄력적 풀에 있는 개별 데이터베이스의 저장소 크기는 '%.*ls' 서비스 계층 탄력적 풀에서 허용하는 최대 크기를 초과할 수 없습니다. 데이터베이스의 최대 크기가 탄력적 풀 서비스 계층에서 허용하는 최대 크기를 초과합니다. |데이터베이스의 최대 크기를 탄력적 풀 서비스 계층에서 허용하는 최대 크기 한도 내로 설정하세요. |

관련 항목:

* [탄력적 풀 만들기(C#)](sql-database-elastic-pool-manage-csharp.md)
* [탄력적 풀 관리(C#)](sql-database-elastic-pool-manage-csharp.md)
* [탄력적 풀 만들기(PowerShell)](sql-database-elastic-pool-manage-powershell.md)
* [탄력적 풀 모니터링 및 관리(PowerShell)](sql-database-elastic-pool-manage-powershell.md)

## <a name="general-errors"></a>일반 오류

다음 오류는 이전의 어떠한 범주에도 포함되지 않습니다.

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| [15006](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-15000-to-15999) |16 |(AdministratorLogin)은(는) 잘못된 문자를 포함하므로 올바른 이름이 아닙니다.|
| [18452](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |로그인이 실패했습니다. 트러스트되지 않은 도메인에서의 로그인이며 Windows 인증과 함께 사용할 수 없습니다.%.&#x2a;ls(해당 버전의 SQL Server에서 Windows 로그인이 지원되지 않습니다.) |
| [18456](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |사용자 '%.&#x2a;ls'에 대한 로그인이 실패했습니다.%.&#x2a;ls%.&#x2a;ls(사용자 "%.&#x2a;ls"에 대한 로그인이 실패했습니다.) |
| [18470](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-18000-to-18999) |14 |사용자 '%.&#x2a;ls'에 대한 로그인이 실패했습니다. 원인: 계정이 비활성화되었습니다.%.&#x2a;ls |
| 40014 |16 |동일한 트랜잭션에서 여러 데이터베이스를 사용할 수 없습니다. |
| 40054 |16 |클러스터형 인덱스가 없는 테이블은 해당 버전의 SQL Server에서 지원되지 않습니다. 클러스터형 인덱스를 만들고 다시 시도하십시오. |
| 40133 |15 |이 작업은 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40506 |16 |지정된 SID가 해당 버전의 SQL Server에 유효하지 않습니다. |
| 40507 |16 |해당 버전의 SQL Server에서 '%.&#x2a;ls'을(를) 매개 변수와 함께 호출할 수 없습니다. |
| 40508 |16 |USE 문은 데이터베이스 사이를 전환하도록 지원되지 않습니다. 새로운 연결을 사용하여 다른 데이터베이스에 연결하십시오. |
| 40510 |16 |문 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40511 |16 |기본 제공 기능 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40512 |16 |사용되지 않은 기능 '%ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40513 |16 |서버 변수 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40514 |16 |'%ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40515 |16 |'%.&#x2a;ls'의 데이터베이스 및/또는 서버 이름에 대한 참조는 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40516 |16 |전역 임시 개체는 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40517 |16 |키워드 또는 문 옵션 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40518 |16 |DBCC 명령 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40520 |16 |보안 개체 클래스 '%S_MSG'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40521 |16 |서버 범위에 있는 보안 개체 클래스 '%S_MSG'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40522 |16 |데이터베이스 보안 주체 '%.&#x2a;ls' 형식은 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40523 |16 |암시적 사용자 '%.&#x2a;ls' 만들기는 해당 버전의 SQL Server에서 지원되지 않습니다. 사용하기 전에 명시적으로 사용자를 만드십시오. |
| 40524 |16 |데이터 형식 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40525 |16 |WITH '%.ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40526 |16 |'%.&#x2a;ls' 행 집합 공급자는 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40527 |16 |연결된 서버는 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40528 |16 |해당 버전의 SQL Server에서 사용자는 인증서, 비대칭 키 또는 Windows 로그인에 매핑할 수 없습니다. |
| 40529 |16 |가장 컨텍스트의 기본 제공 기능 '%.&#x2a;ls'은(는) 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40532 |11 |로그인에서 요청된 서버 "%.&#x2a;ls"을(를) 열 수 없습니다. 로그인이 실패했습니다. |
| 40553 |16 |과도한 메모리 사용으로 인해 세션이 종료되었습니다. 처리할 행 수를 줄이도록 쿼리를 수정하세요.<br/><br/> Transact-SQL 코드에서 `ORDER BY` 및 `GROUP BY` 작업의 수를 줄이면 쿼리의 메모리 요구 사항을 줄일 수 있습니다. |
| 40604 |16 |서버의 할당량을 초과하기 때문에 CREATE/ALTER DATABASE 할 수 없습니다. |
| 40606 |16 |데이터베이스 연결은 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40607 |16 |Windows 로그인은 해당 버전의 SQL Server에서 지원되지 않습니다. |
| 40611 |16 |서버에는 최대 128개의 정의된 방화벽 규칙이 있을 수 있습니다. |
| 40614 |16 |방화벽 규칙의 시작 IP 주소는 끝 IP 주소를 초과할 수 없습니다. |
| 40615 |16 |로그인에서 요청된 서버 ‘{0}’을(를) 열 수 없습니다. IP 주소가 ‘{1}’인 클라이언트는 서버에 액세스할 수 없습니다.<br /><br />액세스할 수 있게 하려면 SQL 데이터베이스 포털을 사용하거나 master 데이터베이스의 sp\_set\_firewall\_rule을 실행하여 해당 IP 주소 또는 주소 범위에 대한 방화벽 규칙을 만드십시오. 이 변경 내용이 적용되려면 최대 5분까지 걸릴 수 있습니다. |
| 40617 |16 |(규칙 이름)(으)로 시작하는 방화벽 규칙 이름이 너무 깁니다. 최대 길이는 128자입니다. |
| 40618 |16 |방화벽 규칙 이름은 비워둘 수 없습니다. |
| 40620 |16 |사용자 "%.&#x2a;ls"에 대한 로그인이 실패했습니다. 암호를 변경하지 못했습니다. 해당 버전의 SQL Server에서 로그인 시 암호 변경이 지원되지 않습니다. |
| 40627 |20 |서버 ‘{0}’ 및 데이터베이스 ‘{1}’에서 작업이 진행 중입니다. 다시 시도하기 전에 잠시 기다려 주십시오. |
| 40630 |16 |암호 유효성 검사에 실패했습니다. 암호가 너무 짧으므로 정책 요구 사항에 맞지 않습니다. |
| 40631 |16 |지정한 암호가 너무 깁니다. 암호는 128자여야 합니다. |
| 40632 |16 |암호 유효성 검사에 실패했습니다. 암호가 충분히 복잡하지 않으므로 정책 요구 사항에 맞지 않습니다. |
| 40636 |16 |이 작업에서 예약된 데이터베이스 이름 '%.&#x2a;ls'을(를) 사용할 수 없습니다. |
| 40638 |16 |잘못된 구독 ID (subscription-id). 구독이 존재하지 않습니다. |
| 40639 |16 |요청이 스키마에 맞지 않음: (스키마 오류). |
| 40640 |20 |서버에 예기치 않은 예외가 발생했습니다. |
| 40641 |16 |지정된 위치가 올바르지 않습니다. |
| 40642 |17 |서버가 현재 사용 중입니다. 나중에 다시 시도하세요. |
| 40643 |16 |지정된 x-ms-version 헤더 값이 올바르지 않습니다. |
| 40644 |14 |지정된 구독에 대한 액세스 권한을 부여하지 못했습니다. |
| 40645 |16 |서버 이름 (Servername)은(는) 비어 있거나 null일 수 없습니다. 소문자 'a'-'z', 숫자 0-9 및 하이픈으로만 구성될 수 있습니다. 하이픈은 이름의 앞 또는 뒤에 올 수 없습니다. |
| 40646 |16 |구독 ID는 비워둘 수 없습니다. |
| 40647 |16 |구독 (subscription-id)에 서버 (servername)이(가) 없습니다. |
| 40648 |17 |너무 많은 요청이 수행되었습니다. 나중에 다시 시도하십시오. |
| 40649 |16 |잘못된 콘텐츠 형식이 지정되었습니다. application/xml만 지원됩니다. |
| 40650 |16 |구독 (subscription-id)이(가) 존재하지 않거나 작업이 준비되지 않았습니다. |
| 40651 |16 |구독 (subscription-id)을(를) 사용할 수 없으므로 서버를 만들지 못했습니다. |
| 40652 |16 |서버를 이동하거나 만들 수 없습니다. 구독 (subscription-id)이(가) 서버 할당량을 초과합니다. |
| 40671 |17 |게이트웨이 및 관리 서비스 간의 통신 오류입니다. 나중에 다시 시도하십시오. |
| 40852 |16 |로그인에서 요청된 서버 '%.\*ls'의 데이터베이스 '%.\*ls'을(를) 열 수 없습니다. 데이터베이스에 대한 액세스는 보안 지원 연결 문자열을 사용해서만 허용됩니다. 이 데이터베이스에 액세스하려면 서버 FQDN에 ‘secure(보안)’를 포함하도록 연결 문자열을 수정합니다 - '서버 이름'.database.windows.net은 '서버 이름'.database.`secure`.windows.net로 수정되어야 합니다. |
| 40914 | 16 | 로그인에서 요청한 '*[server-name]*' 서버를 열 수 없습니다. 클라이언트가 서버에 액세스할 수 없습니다.<br /><br />해결하려면 [가상 네트워크 규칙](sql-database-vnet-service-endpoint-rule-overview.md) 추가를 고려합니다. |
| 45168 |16 |SQL Azure 시스템이 부하를 받고 있으며 단일 SQL Database 서버에 대해 동시 DB CRUD 작업(예: 데이터베이스 만들기)에 대한 상한을 적용합니다. 오류 메시지에 지정된 서버가 최대 동시 연결 수를 초과했습니다. 나중에 다시 시도하십시오. |
| 45169 |16 |SQL Azure 시스템이 부하를 받고 있으며 단일 구독에 대해 동시 서버 CRUD 작업에 대한 상한을 두고 있습니다(예: 데이터베이스 만들기). 오류 메시지에 지정된 구독이 최대 동시 연결 수를 초과했으며 요청이 거부되었습니다. 나중에 다시 시도하십시오. |

## <a name="next-steps"></a>다음 단계

* [Azure SQL Database 기능](sql-database-features.md)에 대해 알아봅니다.
* [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)에 대해 알아봅니다.
* [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)에 대해 알아봅니다.


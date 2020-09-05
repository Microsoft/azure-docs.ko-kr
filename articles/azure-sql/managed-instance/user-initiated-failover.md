---
title: SQL Managed Instance에서 수동으로 장애 조치 (failover) 시작
description: Azure SQL Managed Instance에서 기본 및 보조 복제본을 수동으로 장애 조치 (failover) 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 08/31/2020
ms.openlocfilehash: 0d5390beff6c3e0045c6b887f0262a54a737a851
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181768"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>SQL Managed Instance에서 사용자가 시작한 수동 장애 조치

이 문서에서는 SQL Managed Instance 일반 용도 (GP) 및 중요 비즈니스용 (BC) 서비스 계층에서 주 노드를 수동으로 장애 조치 하는 방법 및 BC 서비스 계층에서 보조 읽기 전용 복제본 노드를 수동으로 장애 조치 (failover) 하는 방법을 설명 합니다.

## <a name="when-to-use-manual-failover"></a>수동 장애 조치 (failover)를 사용 하는 경우

[고가용성](../database/high-availability-sla.md) 은 데이터베이스 응용 프로그램에 대해 투명 하 게 작동 하는 SQL Managed Instance 플랫폼의 기본적인 부분입니다. 노드 저하 또는 오류 감지 시 기본 노드에서 보조 노드로 장애 조치 (failover) 또는 정기적인 월간 소프트웨어 업데이트 중에 Azure에서 SQL Managed Instance를 사용 하는 모든 응용 프로그램에 대 한 예상 된 상황이 발생 합니다.

다음과 같은 이유로 SQL Managed Instance에서 [수동 장애 조치 (failover)](../database/high-availability-sla.md#testing-application-fault-resiliency) 를 실행 하는 것을 고려할 수 있습니다.
- 프로덕션 환경에 배포 하기 전에 장애 조치 복원에 대 한 응용 프로그램 테스트
- 자동 장애 조치 (failover) 시 오류 복원 력을 위한 종단 간 시스템 테스트
- 장애 조치 (failover)가 기존 데이터베이스 세션에 미치는 영향 테스트
- 네트워크 대기 시간의 변경으로 인해 장애 조치 (failover)에서 종단 간 성능을 변경 하는지 확인 합니다.
- Query performance 저하의 경우 수동 장애 조치 (failover)를 통해 성능 문제를 완화할 수 있습니다.

> [!NOTE]
> 프로덕션 환경에 배포 하기 전에 응용 프로그램을 장애 조치 (failover) 하는 것을 확인 하면 프로덕션 환경에서 응용 프로그램 오류의 위험을 완화 하 고 고객의 응용 프로그램 가용성에 영향을 줍니다.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>SQL Managed Instance에서 수동 장애 조치 (failover) 시작

### <a name="rbac-permissions-required"></a>RBAC 권한 필요

장애 조치 (failover)를 시작 하는 사용자에 게는 다음 RBAC 역할 중 하나가 있어야 합니다.

- 구독 소유자 역할 또는
- Managed Instance 참가자 역할 또는
- 다음 권한이 있는 사용자 지정 역할:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>PowerShell 사용

Az. Sql의 최소 버전은 [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)여야 합니다. 항상 최신 PowerShell 버전이 있는 Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md) 를 사용 하는 것이 좋습니다. 

사전 요구 사항으로, 다음 PowerShell 스크립트를 사용 하 여 필요한 Azure 모듈을 설치 합니다. 또한 장애 조치 (failover) 하려는 Managed Instance 있는 구독을 선택 합니다.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

다음 예제와 함께 PowerShell 명령 [AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover) 를 사용 하 여 BC 및 GP 서비스 계층 모두에 적용 되는 주 노드의 장애 조치 (failover)를 시작 합니다.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

다음 PS 명령을 사용 하 여 보조 노드를 장애 조치 (failover) 할 수 있습니다 (BC 서비스 계층에만 해당).

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>CLI 사용

최신 CLI 스크립트를 설치 했는지 확인 합니다.

다음 예제와 함께 az sql mi 장애 조치 CLI 명령을 사용 하 여 BC 및 GP 서비스 계층 모두에 적용 되는 기본 노드의 장애 조치 (failover)를 시작 합니다.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

다음 CLI 명령을 사용 하 여 보조 노드를 장애 조치 (failover) 할 수 있습니다 (BC 서비스 계층에만 해당).

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>Rest API 사용

연속 테스트 파이프라인 또는 자동화 된 성능 mitigators을 구현 하기 위해 SQL 관리 되는 인스턴스의 장애 조치 (failover)를 자동화 해야 하는 고급 사용자의 경우 API 호출을 통해 장애 조치 (failover)를 시작 하 여이 함수를 수행할 수 있습니다. 자세한 내용은 [관리 되는 인스턴스-장애 조치 (Failover) REST API](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover) 를 참조 하세요.

REST API 호출을 사용 하 여 장애 조치 (failover)를 시작 하려면 먼저 선택한 API 클라이언트를 사용 하 여 인증 토큰을 생성 합니다. 생성 된 인증 토큰은 API 요청 헤더에서 권한 부여 속성으로 사용 되며 필수입니다.

다음 코드는 호출할 API URI의 예입니다.

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

API 호출에서 다음 속성을 전달 해야 합니다.

| **API 속성** | **매개 변수** |
| --- | --- |
| subscriptionId | 관리 되는 인스턴스가 배포 되는 구독 ID입니다. |
| resourceGroupName | 관리 되는 인스턴스를 포함 하는 리소스 그룹 |
| managedInstanceName | 관리 되는 인스턴스의 이름 |
| replicaType | 필드 (주 또는 ReadableSecondary). 이러한 매개 변수는 장애 조치 (failover) 될 복제본의 유형 (주 또는 읽을 수 있는 보조)을 나타냅니다. 지정 하지 않으면 기본적으로 주 복제본에서 장애 조치 (failover)가 시작 됩니다. |
| api-version | 정적 값 이며 현재 "2019-06-01-preview" 여야 합니다. |

API 응답은 다음 두 가지 중 하나가 됩니다.

- 202 수락됨
- 400 요청 오류 중 하나입니다.

응답 헤더의 API 응답을 검토 하 여 작업 상태를 추적할 수 있습니다. 자세한 내용은 [비동기 Azure 작업의 상태](../../azure-resource-manager/management/async-operations.md)를 참조 하세요.

## <a name="monitor-the-failover"></a>장애 조치 (failover) 모니터링

사용자가 시작한 수동 장애 조치 (failover)의 진행률을 모니터링 하려면 SQL Managed Instance에서 즐겨 사용 하는 클라이언트 (예: SSMS)에서 다음 T-sql 쿼리를 실행 합니다. 시스템 뷰를 읽고 인스턴스에 사용할 수 있는 보고서 복제본을 dm_hadr_fabric_replica_states 합니다. 수동 장애 조치 (failover)를 시작한 후 동일한 쿼리를 새로 고칩니다.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

장애 조치 (failover)를 시작 하기 전에 출력은 AlwaysOn 가용성 그룹에서 주 1 개 및 3 개의 보조 복제본이 포함 된 BC 서비스 계층의 현재 주 복제본을 표시 합니다. 장애 조치 (failover)를 실행 하면이 쿼리를 다시 실행 하 여 주 노드의 변경을 나타내야 합니다.

이제는 BC에 대해 표시 된 것과 동일한 GP 서비스 계층을 사용 하 여 출력을 볼 수 없습니다. 이는 GP 서비스 계층이 단일 노드만 기반으로 하기 때문입니다. GP 서비스 계층에 대 한 t-sql 쿼리 출력은 장애 조치 (failover) 전후에 단일 노드만 표시 합니다. 장애 조치 (failover) 중에 일반적으로 1 분 이내에 지속 되는 클라이언트의 연결이 끊어지면 장애 조치 (failover) 실행을 나타냅니다.

> [!NOTE]
> **높은 강도** 워크 로드의 경우에는 장애 조치 (failover) 프로세스를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 이는 장애 조치 (failover)를 수행 하기 전에 인스턴스 엔진이 주 서버의 모든 현재 트랜잭션을 처리 하 고 보조 노드에서 처리 하기 때문입니다.

> [!IMPORTANT]
> 사용자가 시작한 수동 장애 조치 (failover)의 기능 제한 사항은 다음과 같습니다.
> - **30 분**마다 동일한 Managed Instance에서 하나의 장애 조치 (failover)가 시작 될 수 있습니다.
> - BC 인스턴스의 경우 장애 조치 (failover) 요청이 허용 되려면 복제본의 쿼럼이 있어야 합니다.
> - BC 인스턴스의 경우 장애 조치 (failover)를 시작할 읽을 수 있는 보조 복제본을 지정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [AZURE SQL Managed Instance에 대 한](../database/high-availability-sla.md)관리 되는 인스턴스 고가용성의 고가용성에 대해 자세히 알아보세요.
- 개요는 [AZURE SQL Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.

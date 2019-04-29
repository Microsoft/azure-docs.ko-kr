---
title: Azure SQL 데이터 동기화 | Microsoft Docs
description: 이 개요에서는 Azure SQL 데이터 동기화를 소개합니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a887c79a51c7a239e7057171e51e67a53af2f84b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580254"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스의 데이터 동기화

SQL 데이터 동기화는 여러 SQL Database 및 SQL Server 인스턴스 간에 양방향으로 선택한 데이터를 동기화할 수 있는 Azure SQL Database에 기반한 서비스입니다.

> [!IMPORTANT]
> 현재 Azure SQL Data Sync는 Azure SQL Database Managed Instance를 지원하지 **않습니다**.

## <a name="when-to-use-data-sync"></a>데이터 동기화를 사용하는 경우

데이터 동기화는 몇몇 Azure SQL 데이터베이스 또는 SQL Server 데이터베이스 간에 데이터를 최신 상태로 유지해야 하는 경우에 유용합니다. 데이터 동기화에 대한 주요 사용 사례는 다음과 같습니다.

- **하이브리드 데이터 동기화:** 데이터 동기화를 사용하면 온-프레미스 데이터베이스와 Azure SQL 데이터베이스 간에 데이터를 동기화하여 하이브리드 애플리케이션을 사용하도록 설정할 수 있습니다. 이 기능은 클라우드로 이동하려는 고객에게 표시되고 Azure에 애플리케이션의 일부를 배치할 수 있습니다.
- **분산 애플리케이션:** 많은 경우에 다른 워크로드를 다른 데이터베이스에 분리하는 것이 좋습니다. 예를 들어 대형 프로덕션 데이터베이스가 있지만 이 데이터에 대한 보고 또는 분석 워크로드를 실행해야 하는 경우 해당 추가 워크로드에 대한 두 번째 데이터베이스를 만드는 데 도움이 됩니다. 이 방법을 사용하면 프로덕션 워크로드에 미치는 영향을 최소화합니다. 데이터 동기화를 사용하여 이러한 두 데이터베이스의 동기화를 유지할 수 있습니다.
- **글로벌 분산 애플리케이션:** 많은 기업이 여러 지역과 여러 국가에 걸쳐 있습니다. 네트워크 대기 시간을 최소화하려면 가까운 지역에 데이터가 위치하는 것이 좋습니다. 데이터 동기화를 사용하면 전 세계 여러 지역에서 데이터베이스를 쉽게 동기화할 수 있습니다.

다음과 같은 시나리오에서 데이터 동기화는 기본 설정된 솔루션이 아닙니다.

| 시나리오 | 권장되는 솔루션 |
|----------|----------------------------|
| 재해 복구 | [Azure 지역 중복 백업](sql-database-automated-backups.md) |
| 읽기 크기 조정 | [읽기 전용 복제본을 사용하여 읽기 전용 쿼리 워크로드의 부하 분산(미리 보기)](sql-database-read-scale-out.md) |
| ETL(OLTP 및 OLAP 간) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 또는 [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) |
| 온-프레미스 SQL Server에서 Azure SQL Database로 마이그레이션 | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="overview-of-sql-data-sync"></a>SQL 데이터 동기화 개요

데이터 동기화는 동기화 그룹의 개념에 기반합니다. 동기화 그룹은 동기화하려는 데이터베이스의 그룹입니다.

데이터 동기화는 허브 및 스포크 토폴로지를 사용하여 데이터를 동기화합니다. 동기화 그룹의 데이터베이스 중 하나를 허브 데이터베이스로 정의합니다. 데이터베이스의 나머지 부분은 구성원 데이터베이스입니다. 동기화는 허브 및 개별 구성원 사이에서만 발생합니다.

- **허브 데이터베이스**는 Azure SQL Database여야 합니다.
- **구성원 데이터베이스**는 SQL Database, 온-프레미스 SQL Server 데이터베이스 또는 Azure 가상 머신의 SQL Server 인스턴스일 수 있습니다.
- **동기화 데이터베이스**는 데이터 동기화에 대한 메타데이터 및 로그를 포함합니다. 동기화 데이터베이스는 허브 데이터베이스와 동일한 지역에 있는 Azure SQL Database여야 합니다. 동기화 데이터베이스는 생성된 고객 및 소유한 고객입니다.

> [!NOTE]
> 온-프레미스 데이터베이스를 구성원 데이터베이스로 사용하는 경우 [로컬 동기화 에이전트를 설치 및 구성](sql-database-get-started-sql-data-sync.md#add-on-prem)해야 합니다.

![데이터베이스 간 데이터 동기화](media/sql-database-sync-data/sync-data-overview.png)

동기화 그룹의 속성은 다음과 같습니다.

- **동기화 스키마**는 동기화할 데이터에 대해 설명합니다.
- **동기화 방향**은 양방향일 수도 있고 한 방향으로만 전달될 수 있습니다. 즉, 동기화 방향은 *허브에서 구성원*이거나 *구성원에게서 허브* 또는 양쪽 모두일 수 있습니다.
- **동기화 간격**은 동기화가 발생하는 빈도를 설명합니다.
- **충돌 해결 정책**은 그룹 수준 정책으로 *허브 우선*일 수도 있고 *구성원 우선*일 수도 있습니다.

## <a name="how-does-data-sync-work"></a>데이터 동기화는 어떻게 작동하나요?

- **데이터 변경 내용 추적:** 데이터 동기화는 삽입, 업데이트 및 삭제 트리거를 사용하여 변경 내용을 추적합니다. 변경 내용은 사용자 데이터베이스에 있는 추가 표에 기록됩니다. BULK INSERT는 기본적으로 트리거를 실행하지 않습니다. FIRE_TRIGGERS가 지정되지 않으면 삽입 트리거가 실행되지 않습니다. 데이터 동기화가 이러한 삽입을 추적할 수 있도록 FIRE_TRIGGERS 옵션을 추가합니다. 
- **데이터 동기화:** 데이터 동기화는 허브 및 스포크 모델에서 설계됩니다. 허브는 개별적으로 각 구성원과 동기화됩니다. 허브의 변경 내용이 구성원에 다운로드된 다음 구성원의 변경 내용은 허브에 업로드됩니다.
- **충돌 해결:** 데이터 동기화는 충돌 해결을 위해 *허브 우선* 또는 *멤버 우선*이라는 두 가지 옵션을 제공합니다.
  - *허브 우선*을 선택하는 경우 허브의 변경 내용은 항상 구성원의 변경 내용을 덮어씁니다.
  - *구성원 우선*을 선택하는 경우 구성원의 변경 내용은 항상 허브의 변경 내용을 덮어씁니다. 구성원이 둘 이상인 경우 최종 값은 먼저 동기화된 구성원에 따라 달라집니다.

## <a name="compare-data-sync-with-transactional-replication"></a>트랜잭션 복제와 데이터 동기화 비교

| | 데이터 동기화 | 트랜잭션 복제 |
|---|---|---|
| 장점 | - 활성-활성 지원<br/>- 온-프레미스 및 Azure SQL Database 간 양방향 | - 낮은 대기 시간<br/>- 트랜잭션 일관성<br/>- 마이그레이션 후 기존 토폴로지 다시 사용 |
| 단점 | - 5분 이상의 대기 시간<br/>- 트랜잭션 일관성 부족<br/>- 성능에 더 많은 영향을 미침 | - Azure SQL Database 단일 데이터베이스 또는 풀링된 데이터베이스에서 게시할 수 없음<br/>- 높은 유지 관리 비용 |
| | | |

## <a name="get-started-with-sql-data-sync"></a>SQL 데이터 동기화 시작

### <a name="set-up-data-sync-in-the-azure-portal"></a>Azure Portal에서 데이터 동기화 설정

- [Azure SQL 데이터 동기화 설정](sql-database-get-started-sql-data-sync.md)
- 데이터 동기화 에이전트 - [Azure SQL 데이터 동기화용 데이터 동기화 에이전트](sql-database-data-sync-agent.md)

### <a name="set-up-data-sync-with-powershell"></a>PowerShell을 사용하여 데이터 동기화 설정

- [PowerShell을 사용하여 여러 Azure SQL Database 간 동기화](scripts/sql-database-sync-data-between-sql-databases.md)
- [PowerShell을 사용하여 Azure SQL Database와 SQL Server 온-프레미스 데이터베이스 간 동기화](scripts/sql-database-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>데이터 동기화의 모범 사례 검토

- [Azure SQL 데이터 동기화 모범 사례](sql-database-best-practices-data-sync.md)

### <a name="did-something-go-wrong"></a>잘못된 부분이 있나요?

- [Azure SQL 데이터 동기화 문제 해결](sql-database-troubleshoot-data-sync.md)

## <a name="consistency-and-performance"></a>일관성과 성능

#### <a name="eventual-consistency"></a>결과적 일관성

데이터 동기화가 트리거 기반이기 때문에 트랜잭션 일관성이 보장되지 않습니다. Microsoft는 결과적으로 모든 변경 내용을 적용하고 데이터 동기화가 데이터 손실을 발생하지 않도록 보장합니다.

#### <a name="performance-impact"></a>성능에 미치는 영향

데이터 동기화는 트리거 삽입, 업데이트 및 삭제를 사용하여 변경 내용을 추적합니다. 변경 내용 추적을 위해 사용자 데이터베이스에 추가 테이블을 만듭니다. 이러한 변경 내용 추적 작업은 데이터베이스 워크로드에 영향을 줍니다. 서비스 계층을 평가하고 필요한 경우 업그레이드합니다.

동기화 그룹 만들기 동안 프로비전 및 프로비전 해제, 업데이트 및 삭제는 데이터베이스 성능에 영향을 줄 수 있습니다. 

## <a name="sync-req-lim"></a>요구 사항 및 제한 사항

### <a name="general-requirements"></a>일반 요구 사항

- 각 표에는 기본 키가 있어야 합니다. 어느 행에서도 기본 키 값은 변경하지 않습니다. 기본 키 값을 변경해야 하는 경우 해당 행을 삭제한 다음 새 기본 키 값을 사용하여 행을 다시 만듭니다. 
- 스냅숏 격리를 사용해야 합니다. 자세한 내용은 [SQL Server에서의 스냅숏 격리](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)를 참조하세요.

### <a name="general-limitations"></a>일반적인 제한 사항

- 테이블에는 기본 키가 없는 ID 열이 있을 수 없습니다.
- 기본 키에는 sql_variant, binary, varbinary, image, xml 같은 데이터 형식이 있을 수 없습니다. 
- 지원되는 전체 자릿수가 보조 키에만 해당하므로 time, datetime, datetime2, datetimeoffset 같은 데이터 형식을 기본 키로 사용하는 경우 주의하세요.
- 개체(데이터베이스, 테이블 및 열) 이름에는 인쇄 가능한 문자 마침표(.), 왼쪽 대괄호([) 또는 오른쪽 대괄호(])를 사용할 수 없습니다.
- Azure Active Directory 인증은 지원되지 않습니다.
- 이름이 같지만 스키마가 다른 테이블(예: dbo.customers 및 sales.customers)은 지원되지 않습니다.

#### <a name="unsupported-data-types"></a>지원되지 않는 데이터 형식

- FileStream
- SQL/CLR UDT
- XMLSchemaCollection(XML 지원)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>지원되지 않는 열 형식

데이터 동기화는 읽기 전용 또는 시스템에서 생성된 열을 동기화할 수 없습니다. 예를 들면 다음과 같습니다.

- 계산된 열입니다.
- 임시 테이블에 대한 시스템에서 생성된 열입니다.

#### <a name="limitations-on-service-and-database-dimensions"></a>서비스 및 데이터베이스 차원에 대한 제한 사항

| **차원**                                                      | **제한**              | **해결 방법**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| 데이터베이스가 속할 수 있는 동기화 그룹의 최대 수입니다.       | 5                      |                             |
| 단일 동기화 그룹에서 엔드포인트의 최대 수입니다.              | 30                     |                             |
| 단일 동기화 그룹에서 온-프레미스 엔드포인트의 최대 수입니다. | 5                      | 여러 동기화 그룹 만들기 |
| 데이터베이스, 테이블, 스키마 및 열 이름                       | 이름당 50자 |                             |
| 동기화 그룹의 표                                          | 500                    | 여러 동기화 그룹 만들기 |
| 동기화 그룹에서 표의 열                              | 1000                   |                             |
| 표의 데이터 행 크기                                        | 24Mb                  |                             |
| 최소 동기화 간격                                           | 5분              |                             |
|||
> [!NOTE]
> 동기화 그룹이 하나만 있는 경우 단일 동기화 그룹에 최대 30개의 엔드포인트가 있을 수 있습니다. 두 개 이상의 동기화 그룹이 있는 경우 모든 동기화 그룹의 전체 엔드포인트 수가 30개를 초과할 수 없습니다. 데이터베이스가 여러 동기화 그룹에 속하는 경우 엔드포인트가 1개가 아닌 여러 개로 계산됩니다.

## <a name="faq-about-sql-data-sync"></a>SQL 데이터 동기화 FAQ

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>SQL 데이터 동기화 서비스의 요금은 얼마인가요?

SQL 데이터 동기화 서비스 자체에는 요금이 부과되지 않습니다.  단, SQL Database 인스턴스에서 송수신되는 데이터에 대한 데이터 전송 요금은 부과됩니다. 자세한 내용은 [SQL Database 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

### <a name="what-regions-support-data-sync"></a>데이터 동기화를 지원하는 지역은 어디인가요?

SQL 데이터 동기화는 모든 지역에서 사용할 수 있습니다.

### <a name="is-a-sql-database-account-required"></a>SQL Database 계정이 필요하나요?

예. Hub Database를 호스트하려면 SQL Database 계정이 필요합니다.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>데이터 동기화를 사용하여 SQL Server 온-프레미스 데이터베이스 사이에서 동기화할 수 있나요?

직접 끌 수는 없습니다. 그러나 Azure에서 허브 데이터베이스를 만든 다음 온-프레미스 데이터베이스를 동기화 그룹에 추가하여 간접적으로 SQL Server 온-프레미스 데이터베이스 사이에서 동기화할 수 있습니다.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>데이터 동기화를 사용해서 서로 다른 구독에 속해 있는 SQL Database 간에 동기화를 수행할 수 있나요?

예. 서로 다른 구독에서 소유하는 리소스 그룹에 속해 있는 SQL Database 간에 동기화를 수행할 수 있습니다.

- 구독이 동일한 테넌트에 속하며 모든 구독에 대해 사용 권한이 있는 경우, Azure Portal에서 동기화 그룹을 구성할 수 있습니다.
- 그렇지 않으면 PowerShell을 사용하여 서로 다른 구독에 속하는 동기화 멤버를 추가해야 합니다.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china"></a>데이터 동기화를 사용해서 서로 다른 클라우드(예: Azure 공용 클라우드 및 Azure 중국)에 속해 있는 SQL Database 간에 동기화를 수행할 수 있나요?

예. 다른 클라우드에 속해 있는 SQL Database 간에 동기화할 수 있으며, PowerShell을 사용하여 다른 구독에 속하는 동기화 멤버를 추가해야 합니다.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>데이터 동기화를 사용하여 프로덕션 데이터베이스에서 빈 데이터베이스로 데이터를 시드한 다음, 동기화할 수 있나요?

예. 원본에서 스크립팅하여 수동으로 새 데이터베이스에 스키마를 만듭니다. 스키마를 만든 후 테이블을 동기화 그룹에 추가하여 데이터를 복사하고 동기화된 상태로 유지합니다.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>내 데이터베이스를 백업 및 복원하는 데 SQL 데이터 동기화를 사용해야 할까요?

SQL 데이터 동기화를 사용하여 데이터의 백업을 만드는 것은 권장되지 않습니다. SQL 데이터 동기화 동기화는 버전 관리가 되지 않기 때문에 특정 시점의 데이터를 백업 또는 복원할 수 없습니다. 또한, SQL 데이터 동기화는 저장 프로시저와 같은 다른 SQL 개체를 백업하지 않고, 복원 작업과 동급의 작업을 빠르게 수행하지 못합니다.

권장되는 백업 방법은 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요.

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>데이터 동기화로 암호화된 테이블 및 열을 동기화할 수 있나요?

- 데이터베이스에서 Always Encrypted를 사용하는 경우 암호화되지 *않은* 테이블 및 열만 동기화할 수 있습니다. 데이터 동기화로는 데이터 암호를 해독할 수 없으므로 암호화된 열은 동기화할 수 없습니다.
- 열에서 CLE(열 수준 암호화)를 사용하는 경우, 행 크기가 최대 크기인 24Mb보다 작기만 하면 열을 동기화할 수 있습니다. 데이터 동기화는 키로 암호화된 열(CLE)을 일반 이진 데이터로 처리합니다. 다른 동기화 멤버에 대한 데이터 암호를 해독하려면 같은 인증서가 필요합니다.

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL 데이터 동기화는 데이터 정렬을 지원하나요?

예. SQL 데이터 동기화는 다음과 같은 시나리오에서 데이터 정렬을 지원합니다.

- 선택한 동기화 스키마 테이블이 사용자의 허브 또는 구성원 데이터베이스에 포함되어 있지 않다면, 사용자가 동기화 그룹을 배포하는 경우 선택된 데이터 정렬 설정에 따라 빈 대상 데이터베이스에 해당 테이블과 열이 자동으로 만들어집니다.
- 동기화할 테이블이 사용자의 허브와 구성원 데이터베이스에 이미 포함되어 있다면, 허브와 구성원 데이터베이스의 기본 키 열이 동일한 데이터 정렬을 갖는 경우에만 동기화 그룹이 성공적으로 배포됩니다. 기본 키 열을 제외한 다른 열에는 데이터 정렬 제한이 없습니다.

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL 데이터 동기화는 페더레이션을 지원하나요?

SQL 데이터 동기화 서비스에서는 Federation Root Database를 제한 없이 사용할 수 있습니다. 현재 버전의 SQL 데이터 동기화에는 Federated Database 엔드포인트를 추가할 수 없습니다.

## <a name="next-steps"></a>다음 단계

### <a name="update-the-schema-of-a-synced-database"></a>동기화된 데이터베이스의 스키마 업데이트

동기화 그룹에서 데이터베이스의 스키마를 업데이트해야 하나요? 스키마 변경 내용은 자동으로 복제되지 않습니다. 일부 솔루션의 경우 다음 문서를 참조하세요.

- [Azure SQL 데이터 동기화에서 스키마 변경 복제 자동화](sql-database-update-sync-schema.md)
- [PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트](scripts/sql-database-sync-update-schema.md)

### <a name="monitor-and-troubleshoot"></a>모니터링 및 문제 해결

예상대로 SQL 데이터 동기화를 수행하나요? 활동을 모니터링하고 문제를 해결하려면 다음 문서를 참조하세요.

- [Azure Monitor 로그를 사용 하 여 Azure SQL 데이터 동기화 모니터링](sql-database-sync-monitor-oms.md)
- [Azure SQL 데이터 동기화 문제 해결](sql-database-troubleshoot-data-sync.md)

### <a name="learn-more-about-azure-sql-database"></a>Azure SQL Database에 대한 자세한 정보

SQL 데이터베이스에 대한 자세한 내용은 다음 문서를 참조하세요.

- [SQL Database 개요](sql-database-technical-overview.md)
- [데이터베이스 수명 주기 관리](https://msdn.microsoft.com/library/jj907294.aspx)

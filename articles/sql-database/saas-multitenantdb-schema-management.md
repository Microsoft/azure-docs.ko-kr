---
title: 다중 테넌트 앱에서 Azure SQL Database 스키마 관리 | Microsoft Docs
description: Azure SQL Database를 사용하는 다중 테넌트 애플리케이션에서 여러 테넌트에 대한 스키마 관리
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: c7c10608d90f7659b108d2d8c80038f59396de2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485250"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>분할된 다중 테넌트 SQL 데이터베이스를 사용하는 SaaS 애플리케이션에서 스키마 관리

이 자습서에서는 SaaS(Software as a Service) 애플리케이션에서 대규모 데이터베이스를 유지 관리하는 데 따른 문제를 검토합니다. 수많은 데이터베이스에 스키마를 적용하기 위한 솔루션이 설명되어 있습니다.

다른 애플리케이션과 마찬가지로 Wingtip Tickets SaaS 앱도 시간이 지나면서 발전되고, 그에 따라 데이터베이스를 변경해야 합니다. 변경 사항은 스키마나 참조 데이터를 영향을 줄 수도 있고, 데이터베이스 유지 관리 작업에 적용될 수도 있습니다. 테넌트별 데이터베이스 패턴을 사용하는 SaaS 애플리케이션에서는 수많은 테넌트 데이터베이스를 대상으로 변경 사항을 효율적으로 조율할 수 있어야 합니다. 또한, 변경 사항이 새로 생성되는 데이터베이스에도 포함되도록 데이터베이스 프로비저닝 프로세스에 변경 사항을 반영해야 합니다.

#### <a name="two-scenarios"></a>두 가지 시나리오

이 자습서에서는 다음 두 가지 시나리오를 살펴봅니다.
- 모든 테넌트에 대한 참조 데이터 업데이트를 배포합니다.
- 참조 데이터가 포함된 테이블의 인덱스를 다시 빌드합니다.

Azure SQL Database의 [탄력적 작업](sql-database-elastic-jobs-overview.md) 기능은 테넌트 데이터베이스에서 이러한 작업을 실행하는 데 사용됩니다. 작업은 ‘템플릿’ 테넌트 데이터베이스에서도 작동합니다. Wingtip Tickets 샘플 앱에서 새 테넌트 데이터베이스를 프로비전하기 위해 이 템플릿 데이터베이스가 복사됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 작업 에이전트를 만들기.
> * 여러 테넌트 데이터베이스를 대상으로 T-SQL 쿼리 실행하기.
> * 모든 테넌트 데이터베이스의 참조 데이터 업데이트하기.
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스를 만듭니다.

## <a name="prerequisites"></a>필수 조건

- Wingtip Tickets 다중 테넌트 데이터베이스 앱이 설치되어 있어야 합니다.
    - 자세한 내용은 Wingtip Tickets SaaS 다중 테넌트 데이터베이스 앱을 소개하는 첫 번째 튜토리얼을 참조하세요.<br />[Azure SQL Database를 사용하는 분할된 다중 테넌트 애플리케이션 배포 및 탐색](saas-multitenantdb-get-started-deploy.md)합니다.
        - 배포 프로세스는 5분 이내에 실행됩니다.
    - *분할된 다중 테넌트* 버전의 Wingtip이 설치되어 있어야 합니다. *독립 실행형* 및 *테넌트별 데이터베이스*용 버전은 이 자습서를 지원하지 않습니다.

- 최신 버전의 SSMS(SQL Server Management Studio)가 설치되어 있어야 합니다. [SSMS를 다운로드 및 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)합니다.

- Azure PowerShell이 설치되어 있어야 합니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

> [!NOTE]
> 이 자습서에서는 제한된 미리 보기([Elastic Database 작업](sql-database-elastic-database-client-library.md))에 있는 Azure SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행 하려는 경우에 구독 ID를 제공 *SaaSFeedback\@microsoft.com* subject = 탄력적인 작업 미리 보기. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 이 미리 보기는 제한적 으로만 제공 되므로 문의 *SaaSFeedback\@microsoft.com* 관련된 질문이 있거나 지원이 대 한 합니다.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 스키마 관리 패턴 소개

이 샘플에 사용된 공유 다중 테넌트 데이터베이스 모델을 사용하면 테넌트 데이터베이스에 하나 이상의 테넌트를 포함할 수 있습니다. 이 샘플은 다중 테넌트 및 단일 테넌트 데이터베이스의 혼합을 사용하여 *하이브리드* 테넌트 관리 모델을 사용할 수 있는지 확인합니다. 이러한 데이터베이스의 변경 사항을 관리하는 작업은 무척 복잡해질 수 있습니다. [탄력적 작업](sql-database-elastic-jobs-overview.md)을 사용하면 다량의 데이터베이스를 손쉽게 관리할 수 있습니다. 작업을 사용하면 테 넌 트 데이터베이스 그룹에 대해 태스크와 같은 Transact-SQL 스크립트를 안전하고 안정적으로 실행할 수 있습니다. 태스크는 사용자 상호 작용 또는 입력과 별개입니다. 이 메서드는 애플리케이션의 모든 테넌트에서 스키마 및 일반 참조 데이터에 변경 내용을 배포하는 데 사용할 수 있습니다. 데이터베이스의 골든 템플릿 복사본을 유지하기 위해 탄력적 작업을 사용할 수도 있습니다. 템플릿은 새 테넌트를 만드는 데 사용되어, 항상 최신 스키마 및 참조 데이터가 사용되도록 합니다.

![화면](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>탄력적 작업의 제한된 미리 보기

현재 Azure SQL Database의 통합 기능인 새로운 탄력적 작업 버전이 있습니다. 이 새로운 탄력적 작업 버전은 현재 제한된 미리 보기 상태입니다. 제한된 미리 보기에서는 PowerShell을 사용하여 작업 에이전트 만들기와 T-SQL을 사용하여 작업을 생성 및 관리하기를 지원합니다.
> [!NOTE]
> 이 자습서에서는 제한된 미리 보기(Elastic Database 작업)에 있는 SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려면 ‘Elastic 작업 미리 보기’라는 제목으로 SaaSFeedback@microsoft.com으로 구독 ID를 보내 주세요. 구독이 활성화되었다는 확인을 받은 후 최신 시험판 작업 cmdlet을 다운로드하여 설치하세요. 이 미리 보기는 제한적으로만 제공되므로 관련 질문이 있거나 지원이 필요한 경우 SaaSFeedback@microsoft.com에 문의해야 합니다.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 소스 코드 및 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 애플리케이션 소스 코드는 GitHub의 [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) 리포지토리에서 사용할 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 참조하세요.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>작업 에이전트 데이터베이스와 새 작업 에이전트 만들기

이 자습서를 사용하려면 PowerShell을 사용하여 작업 에이전트 데이터베이스와 작업 에이전트를 만들어야 합니다. SQL 에이전트에서 사용되는 MSDB 데이터베이스와 마찬가지로, 작업 에이전트는 Azure SQL 데이터베이스를 사용하여 작업 정의, 작업 상태 및 기록을 저장합니다. 작업 에이전트가 만들어지면 즉시 작업을 만들고 모니터링할 수 있습니다.

1. **PowerShell ISE**에서 *...\\Learning Modules\\Schema Management\\Demo-SchemaManagement.ps1*을 엽니다.
2. **F5** 키를 눌러 스크립트를 실행합니다.

*Demo-SchemaManagement.ps1* 스크립트가 *Deploy-SchemaManagement.ps1* 스크립트를 호출하여 카탈로그 서버에 _jobagent_라는 이름의 데이터베이스를 생성합니다. 그런 다음, 매개 변수로 _jobagent_ 데이터베이스를 전달하여 작업 에이전트를 생성합니다.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>모든 테넌트에 새 참조 데이터를 배포하는 작업 만들기

#### <a name="prepare"></a>준비

각 테넌트 데이터베이스의 **VenueTypes** 테이블에는 장소 유형 집합이 포함되어 있습니다. 각 장소 유형은 장소에서 진행할 수 있는 이벤트의 종류를 정의합니다. 장소 유형은 테넌트 이벤트 앱에 표시된 배경 이미지에 해당합니다.  이 연습에서는 두 개의 추가 장소 유형인 *Motorcycle Racing* 및 *Swimming Club*을 추가하기 위해 모든 데이터베이스에 업데이트를 배포합니다.

먼저 각 테넌트 데이터베이스에 포함된 각 장소 유형을 검토합니다. SQL Server Management Studio(SSMS)에서 테넌트 데이터베이스 중 하나에 접속하여 VenueTypes 테이블을 살펴봅니다.  데이터베이스 페이지에서 액세스할 수 있는 Azure Portal에서 쿼리 편집기를 사용하여 이 표를 쿼리하는 방법도 있습니다.

1. SSMS를 열고 테넌트 서버 *tenants1-dpt-&lt;user&gt;.database.windows.net*에 접속합니다.
1. *Motorcycle Racing*과 *Swimming Club*이 현재 포함되어 있지 **않은** 것을 확인하려면 *tenants1-dpt-&lt;user&gt;* 서버에서 *contosoconcerthall* 데이터베이스로 이동하여 *VenueTypes* 테이블을 쿼리합니다.



#### <a name="steps"></a>단계

이제 2개의 새 장소 유형을 추가하여 각 테넌트 데이터베이스의 **VenueTypes** 테이블을 업데이트하는 작업을 만들어 보겠습니다.

새 작업을 만들려면 _jobagent_ 데이터베이스에서 생성된 작업 시스템 저장 프로시저 집합을 사용합니다. 저장 프로시저는 작업 에이전트를 만들 때 생성된 것입니다.

1. SSMS에서 테넌트 서버: tenants1-&lt;user&gt;.database.windows.net에 연결합니다.

2. *tenants1* 데이터베이스로 이동합니다.

3. *VenueTypes* 테이블을 쿼리하여 *Motorcycle Racing* 및 *Swimming Club*이 결과 목록에 아직 없는지 확인합니다.

4. 카탈로그 서버 *catalog-mt-&lt;user&gt;.database.windows.net*에 연결합니다.

5. 카탈로그 서버의 _jobagent_ 데이터베이스에 접속합니다.

6. SSMS에서 *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql* 파일을 엽니다.

7. 문을 set @User = &lt;user&gt;로 수정하고 Wingtip Ticket SaaS 다중 테넌트 데이터베이스 애플리케이션을 배포할 때 사용된 사용자 값을 바꿉니다.

8. **F5** 키를 눌러 스크립트를 실행합니다.

#### <a name="observe"></a>관찰

*DeployReferenceData.sql* 스크립트에서 다음 항목을 살펴봅니다.

- **sp\_add\_target\_group**은 대상 그룹 이름 *DemoServerGroup*을 만든 후 대상 구성원을 그룹에 추가합니다.

- **sp\_add\_target\_group\_member**는 다음 항목을 추가합니다.
    - *server* 대상 구성원 유형.
        - 테넌트 데이터베이스를 포함하는 *tenants1-mt-&lt;user&gt;* 서버입니다.
        - 서버를 포함시키면 작업이 실행되는 시점에 존재하는 테넌트 데이터베이스가 포함됩니다.
    - *catalog-mt-&lt;user&gt;* 서버에 있는 템플릿 데이터베이스(*basetenantdb*)의 *database* 대상 구성원 유형
    - 이후 자습서에서 사용되는 *adhocreporting* 데이터베이스를 포함할 *database* 대상 구성원 유형.

- **sp\_add\_job**은 *참조 데이터 배포*라는 작업을 만듭니다.

- **sp\_add\_jobstep**은 VenueTypes 참조 테이블을 업데이트하기 위한 T-SQL 명령 텍스트가 포함된 작업 단계를 만듭니다.

- 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 작업이 완료된 시기를 확인합니다. 작업은 테넌트 데이터베이스를 업데이트하고, 참조 테이블을 포함하는 두 개의 추가 데이터베이스를 업데이트합니다.

SSMS에서 *tenants1-mt-&lt;user&gt;* 서버에 있는 테넌트 데이터베이스로 이동합니다. 이제 *VenueTypes* 테이블을 쿼리하여 *Motorcycle Racing* 및 *Swimming Club*이 테이블에 추가되었는지 확인합니다. 총 장소 유형 수는 2씩 증가했어야 하는데 그렇지 않았습니다.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>참조 테이블 인덱스를 관리하는 작업 만들기

이 예제에서는 모든 테넌트 데이터베이스의 참조 테이블 기본 키에서 인덱스를 다시 빌드하는 작업을 생성합니다. 인덱스 다시 빌드는 다량의 데이터 로드를 로드한 뒤에 성능 향상을 위해 관리자가 실행하는 일반적인 데이터베이스 관리 작업입니다.

1. SSMS에서 *catalog-mt-&lt;User&gt;.database.windows.net* 서버에 있는 _jobagent_ 데이터베이스에 접속합니다.

2. SSMS에서 *...\\Learning Modules\\Schema Management\\OnlineReindex.sql* 파일을 엽니다.

3. **F5** 키를 눌러 스크립트를 실행합니다.

#### <a name="observe"></a>관찰

*OnlineReindex.sql* 스크립트에서 다음 항목을 살펴봅니다.

* **sp\_add\_job**은 *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*라는 새 작업을 만듭니다.

* **sp\_add\_jobstep**은 인덱스를 업데이트할 T-SQL 명령 텍스트를 포함하는 작업 단계를 만듭니다.

* 스크립트의 나머지 뷰는 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 작업이 모든 대상 그룹 구성원에서 성공적으로 완료된 시기를 확인합니다.

## <a name="additional-resources"></a>추가 리소스

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [규모가 확장된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md)
* [규모가 확장된 클라우드 데이터베이스 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 여러 데이터베이스에서 T-SQL 작업을 실행하는 작업 에이전트 만들기
> * 모든 테넌트 데이터베이스에서 참조 데이터 업데이트하기
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기

다음 단계로 합니다 [임시 보고 자습서](saas-multitenantdb-adhoc-reporting.md) 탐색 데이터베이스 테 넌 트에서 분산된 쿼리를 실행 합니다.


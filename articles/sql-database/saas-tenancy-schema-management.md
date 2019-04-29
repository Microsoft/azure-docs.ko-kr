---
title: 단일 테넌트 앱에서 Azure SQL Database 스키마 관리 | Microsoft Docs
description: Azure SQL Database를 사용하는 단일 테넌트 앱에서 여러 테넌트의 스키마 관리
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b2aa3eb6a117bbbdcf9c4aa44161dc25ddea2f1a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484391"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Azure SQL Database를 사용하여 SaaS 애플리케이션에서 테넌트별 데이터베이스 패턴으로 스키마 관리
 
데이터베이스 애플리케이션이 발전하면 데이터베이스 스키마나 참조 데이터를 변경해야 합니다.  데이터베이스 유지 관리 작업도 주기적으로 수행해야 합니다. 테넌트별 데이터베이스 패턴을 사용하는 애플리케이션을 관리하기 위해서는 수많은 테넌트 데이터베이스를 대상으로 변경을 적용하거나 유지 관리 작업을 수행해야 합니다.

이 자습서에서는 1) 모든 테넌트에 대해 참조 데이터 업데이트 배포와 2) 참조 데이터를 포함하는 테이블에서 인덱스 다시 빌드의 두 가지 시나리오를 살펴봅니다. 이러한 작업을 모든 테넌트에서 실행할 때와 새 테넌트 데이터베이스를 생성할 때 사용하는 템플릿 데이터베이스에서 실행할 때 [탄력적 작업](sql-database-elastic-jobs-overview.md)이라는 기능이 사용됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> 
> * 작업 에이전트 만들기
> * T-SQL 작업이 모든 테넌트 데이터베이스에서 실행되도록 하기
> * 모든 테넌트 데이터베이스에서 참조 데이터 업데이트하기
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기


이 자습서를 수행하려면 다음 필수 조건이 충족되었는지 확인합니다.

* Wingtip Tickets SaaS Database Per Tenant 앱이 배포됩니다. 5분 안에 배포를 마치려면 [테넌트 애플리케이션별로 Wingtip Tickets SaaS 데이터베이스 배포 및 살펴보기](saas-dbpertenant-get-started-deploy.md)를 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* 최신 버전의 SSMS(SQL Server Management Studio)가 설치되어 있습니다. [SSMS 다운로드 및 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> 이 자습서에서는 제한된 미리 보기(Elastic Database 작업)에 있는 SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려면 ‘Elastic 작업 미리 보기’라는 제목으로 SaaSFeedback@microsoft.com으로 구독 ID를 보내 주세요. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 이 미리 보기는 제한적으로만 제공되므로 관련 질문이 있거나 지원이 필요한 경우 SaaSFeedback@microsoft.com에 문의해야 합니다.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS 스키마 관리 패턴 소개

테넌트별 데이터베이스 패턴은 테넌트 데이터를 효과적으로 격리하지만, 유지 관리해야 할 데이터베이스의 수가 늘어난다는 단점이 있습니다. [탄력적 작업](sql-database-elastic-jobs-overview.md)을 사용하면 SQL 데이터베이스를 손쉽게 관리할 수 있습니다. 작업을 사용하면 여러 개의 데이터베이스를 대상으로 안전하고 안정적으로 작업(T-SQL 스크립트)을 실행할 수 있습니다. 또한, 하나의 애플리케이션에서 모든 테넌트 데이터베이스를 대상으로 스키마와 공통 참조 데이터 변경 사항을 배포할 수 있습니다. 새 테넌트를 만들 때 사용되는 *템플릿* 데이터베이스를 관리할 때도 탄력적 작업을 사용하면 템플릿 데이터베이스에 항상 최신 스키마와 참조 데이터가 유지됩니다.

![화면](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>탄력적 작업의 제한된 미리 보기

새로운 버전의 탄력적 작업이 Azure SQL Database에 통합된 기능으로서 공개되었습니다. 이 새로운 탄력적 작업 버전은 현재 제한된 미리 보기 상태입니다. 제한된 미리 보기에서는 PowerShell을 사용하여 작업 에이전트 만들기와 T-SQL을 사용하여 작업을 생성 및 관리하기를 지원합니다.

> [!NOTE]
> 이 자습서에서는 제한된 미리 보기(Elastic Database 작업)에 있는 SQL Database 서비스의 기능을 사용합니다. 이 자습서를 수행하려면 ‘Elastic 작업 미리 보기’라는 제목으로 SaaSFeedback@microsoft.com으로 구독 ID를 보내 주세요. 구독이 활성화되었다는 확인을 받은 후 [최신 시험판 작업 cmdlet을 다운로드하여 설치하세요](https://github.com/jaredmoo/azure-powershell/releases). 이 미리 보기는 제한적으로만 제공되므로 관련 질문이 있거나 지원이 필요한 경우 SaaSFeedback@microsoft.com에 문의해야 합니다.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>테넌트 애플리케이션별 Wingtip Tickets SaaS 데이터베이스 스크립트 받기

[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 애플리케이션 소스 코드와 관리 스크립트를 받을 수 있습니다. Wingtip Tickets SaaS 스크립트를 다운로드하고 차단을 해제하는 단계는 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 확인하세요.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>작업 에이전트 데이터베이스와 새 작업 에이전트 만들기

이 자습서에서는 작업 에이전트와 이를 뒷받침하는 작업 에이전트 데이터베이스를 만들 때 PowerShell을 사용해야 합니다. 작업 에이전트 데이터베이스에는 작업 정의, 작업 상태와 기록이 저장됩니다. 작업 에이전트와 데이터베이스를 만든 뒤에는 즉시 작업을 만들고 모니터링할 수 있습니다.

1. **PowerShell ISE**에서 \\...\\Learning Modules\\Schema Management*Demo-SchemaManagement.ps1*을 엽니다.
1. **F5** 키를 눌러 스크립트를 실행합니다.

*Demo-SchemaManagement.ps1* 스크립트가 *Deploy-SchemaManagement.ps1* 스크립트를 호출하여 카탈로그 서버에 *osagent*라는 이름의 SQL 데이터베이스를 생성합니다. 그런 다음, 매개 변수로 해당 데이터베이스를 사용하여 작업 에이전트를 생성합니다.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>모든 테넌트에 새 참조 데이터를 배포하는 작업 만들기

Wingtip Tickets 앱에서 각 테넌트 데이터베이스에는 지원되는 장소 유형 집합이 포함되어 있습니다. 각 장소는 특정 장소 유형을 갖습니다. 작업 유형은 장소에서 진행할 수 있는 이벤트의 종류와 앱에서 사용되는 배경 이미지를 결정합니다. 애플리케이션에서 새로운 이벤트 종류를 지원하려면 이 참조 데이터를 업데이트하고 새 장소 유형을 추가해야 합니다.  이 연습에서는 두 개의 추가 장소 유형을 추가 하려면 모든 테 넌 트 데이터베이스에 업데이트를 배포 합니다. *Motorcycle Racing* 및 *Swimming Club*을 추가하기 위해 모든 데이터베이스에 업데이트를 배포합니다.

먼저 각 테넌트 데이터베이스에 포함된 각 장소 유형을 검토합니다. SQL Server Management Studio(SSMS)에서 테넌트 데이터베이스 중 하나에 접속하여 VenueTypes 테이블을 살펴봅니다.  데이터베이스 페이지에서 액세스할 수 있는 Azure Portal에서 쿼리 편집기를 사용하여 이 표를 쿼리하는 방법도 있습니다. 

1. SSMS를 열고 테넌트 서버 *tenants1-dpt-&lt;user&gt;.database.windows.net*에 접속합니다.
1. *Motorcycle Racing*과 *Swimming Club*이 현재 포함되어 있지 **않은** 것을 확인하려면 *tenants1-dpt-&lt;user&gt;* 서버에서 _contosoconcerthall_ 데이터베이스로 이동하여 *VenueTypes* 테이블을 쿼리합니다.

이번에는 모든 테넌트 데이터베이스의 *VenueTypes* 테이블을 업데이트하여 새 장소 유형을 추가하는 작업을 만들어 보겠습니다.

새 작업을 만들려면 작업 에이전트를 생성할 때 _jobagent_ 데이터베이스에서 만들어진 작업 시스템 저장 프로시저 집합을 사용합니다.

1. SSMS에서 카탈로그 서버 *catalog-dpt-&lt;user&gt;.database.windows.net*에 접속합니다. 
1. SSMS에서 ...\\Learning Modules\\Schema Management\\DeployReferenceData.sql 파일을 엽니다.
1. 문을 수정 합니다. 설정할 @wtpUser = &lt;사용자&gt; Wingtip Tickets SaaS Database Per Tenant 앱을 배포할 때 사용한 사용자 값 대체
1. 에 연결 되어 있는지 확인 합니다 _jobagent_ 데이터베이스 및 키를 눌러 **F5** 스크립트를 실행 하려면

*DeployReferenceData.sql* 스크립트에서 다음과 같은 요소를 살펴봅니다.
* **sp\_add\_target\_group**은 대상 그룹 이름 DemoServerGroup을 생성합니다.
* **sp\_add\_target\_group\_member**는 대상 데이터베이스 집합을 정의하는 데 사용됩니다.  먼저 _tenants1-dpt-&lt;user&gt;_ 서버가 추가됩니다.  서버를 대상으로 추가하면 작업이 실행되는 시점에 해당 서버에 있는 데이터베이스가 작업에 포함되게 됩니다. 그런 다음, _basetenantdb_ 데이터베이스와 *adhocreporting* 데이터베이스(이 데이터베이스는 자습서의 뒷부분에서 사용합니다)가 대상으로 추가됩니다.
* **sp\_add\_job**은 _Reference Data Deployment_라는 이름의 작업을 생성합니다.
* **sp\_add\_jobstep**은 VenueTypes 참조 테이블을 업데이트하기 위한 T-SQL 명령 텍스트가 포함된 작업 단계를 만듭니다.
* 스크립트의 남은 보기에서 개체의 존재 여부를 표시하고 작업 실행을 모니터링합니다. 쿼리를 사용하여 **lifecycle** 열에서 상태 값을 검토하여 모든 대상 데이터베이스에서 작업이 끝났는지 확인합니다.

스크립트가 완료되면 참조 데이터가 업데이트된 것을 확인할 수 있습니다.  SSMS에서 *tenants1-dpt-&lt;user&gt;* 서버에 있는 *contosoconcerthall* 데이터베이스로 이동하여 *VenueTypes* 테이블을 쿼리합니다.  *Motorcycle Racing*과 *Swimming Club*이 **있는지** 확인합니다.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>참조 테이블 인덱스를 관리하는 작업 만들기

이 예제에서는 작업을 사용하여 참조 테이블 기본 키에서 인덱스를 다시 빌드합니다.  이것은 다량의 데이터를 로드한 뒤에 수행해야 하는 데이터베이스 유지 관리 작업입니다.

동일한 작업 '시스템' 저장 프로시저를 사용하여 작업을 만듭니다.

1. SSMS를 열고 _catalog-dpt-&lt;user&gt;.database.windows.net_ 서버에 접속합니다.
1. _…\\Learning Modules\\Schema Management\\OnlineReindex.sql_ 파일을 엽니다.
1. 마우스 오른쪽 단추를 클릭하고 연결을 선택한 다음, _catalog-dpt-&lt;user&gt;.database.windows.net_ 서버에 아직 접속하지 않았으면 지금 접속합니다.
1. _jobagent_ 데이터베이스에 접속되어 있는지 확인하고 **F5** 키를 눌러 스크립트를 실행합니다.

_OnlineReindex.sql_ 스크립트에서 다음과 같은 요소를 살펴봅니다.
* **sp\_add\_job**은 “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”라는 새 작업을 만듭니다.
* **sp\_add\_jobstep**은 인덱스를 업데이트할 T-SQL 명령 텍스트를 포함하는 작업 단계를 만듭니다.
* 스크립트의 나머지 뷰는 작업 실행을 모니터링합니다. 이러한 쿼리를 사용하여 **lifecycle** 열의 상태 값을 검토해 작업이 모든 대상 그룹 구성원에서 성공적으로 완료된 시기를 확인합니다.



## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> 
> * 여러 데이터베이스에서 T-SQL 작업을 실행하는 작업 에이전트 만들기
> * 모든 테넌트 데이터베이스에서 참조 데이터 업데이트하기
> * 모든 테넌트 데이터베이스의 테이블에서 인덱스 만들기

다음 단계로 합니다 [임시 보고 자습서](saas-tenancy-cross-tenant-reporting.md) 탐색 데이터베이스 테 넌 트에서 분산된 쿼리를 실행 합니다.


## <a name="additional-resources"></a>추가 리소스

* [Wingtip Tickets SaaS Database Per Tenant 애플리케이션 배포를 기반으로 빌드되는 추가 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [규모가 확장된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md)
* [규모가 확장된 클라우드 데이터베이스 만들기 및 관리](sql-database-elastic-jobs-create-and-manage.md)
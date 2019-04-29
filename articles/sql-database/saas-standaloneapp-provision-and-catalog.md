---
title: 다중 테넌트 SaaS 자습서 - Azure SQL Database | Microsoft Docs
description: 독립 실행형 애플리케이션 패턴을 사용하여 새 테넌트 프로비전 및 카탈로그 작업
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
ms.date: 09/24/2018
ms.openlocfilehash: 28deb9b7ba15744b9bd3d273d02db4398d2b2ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484593"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>테넌트별 애플리케이션 SaaS 패턴을 사용하여 새 테넌트 프로비전/카탈로그 작업

이 문서에서는 테넌트 SaaS 패턴별 독립 실행형 앱을 사용하여 새 테넌트를 프로비전하고 카탈로그 작업을 수행하는 방법을 설명합니다.
이 문서에는 두 개의 주요 부분에 있습니다.
* 새 테넌트 프로비전/카탈로그 작업에 대한 개념적 토론
* 프로비전 및 카탈로그 작업을 수행하는 샘플 PowerShell 코드를 설명하는 자습서
    * 이 자습서에서는 독립 실행형 테넌트별 앱 패턴에 맞게 조정된 Wingtip Tickets 샘플 SaaS 애플리케이션을 사용합니다.

## <a name="standalone-application-per-tenant-pattern"></a>테넌트별 독립 실행형 애플리케이션 패턴

독립 실행형 테넌트별 앱 패턴은 다중 테넌트 SaaS 애플리케이션의 몇 가지 패턴 중 하나입니다.  이 패턴에서는 각 테넌트마다 독립 실행형 앱이 프로비전됩니다. 애플리케이션은 애플리케이션 수준 구성 요소와 SQL 데이터베이스로 구성됩니다.  각 테넌트 앱은 공급업체의 구독에 배포할 수 있습니다.  대안으로, Azure에는 앱을 테넌트의 구독에 배포하고 테넌트 대신 공급업체가 관리하는 [관리되는 애플리케이션 프로그램](https://docs.microsoft.com/azure/managed-applications/overview)이 제공됩니다. 

   ![app-per-tenant 패턴](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

테넌트에 애플리케이션을 배포할 때 앱과 데이터베이스는 테넌트에 대해 생성된 새 리소스 그룹에 프로비전됩니다.  별도의 리소스 그룹을 사용하면 각 테넌트의 애플리케이션 리소스가 분리되어 독립적으로 관리될 수 있습니다. 각 리소스 그룹 내에서 각 애플리케이션 인스턴스는 해당 데이터베이스에 직접 액세스하도록 구성됩니다.  이 연결 모델은 카탈로그를 사용하여 앱과 데이터베이스 간에 연결을 중개하는 다른 패턴과 대조됩니다.  리소스가 공유되지 않기 때문에 각 테넌트 데이터베이스에는 최고 부하를 처리하기에 충분한 리소스가 프로비전되어야 합니다. 이 패턴은 테넌트 격리의 중요성이 높고 리소스 비용에 대한 중요성이 낮은 테넌트가 적은 SaaS 애플리케이션에 사용되는 경향이 있습니다.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>테넌트별 애플리케이션 패턴으로 테넌트 카탈로그 사용

각 테넌트의 앱과 데이터베이스가 완전히 격리되어 있어도 다양한 관리 및 분석 시나리오로 테넌트를 운영할 수 있습니다.  예를 들어 애플리케이션의 새로운 릴리스에 스키마 변경 내용을 적용하려면 각 테넌트 데이터베이스의 스키마를 변경해야 합니다. 보고 및 분석 시나리오에서는 모든 테넌트 데이터베이스의 배포 위치에 상관없이 액세스가 필요할 수도 있습니다.

   ![app-per-tenant 패턴](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

테넌트 카탈로그에는 테넌트 식별자와 테넌트 데이터베이스 간의 매핑이 유지되기 때문에 식별자를 서버 및 데이터베이스 이름으로 확인할 수 있습니다.  Wingtip SaaS 앱에서 테넌트 식별자는 테넌트 이름의 해시로 계산되지만 다른 스키마가 사용될 수도 있습니다.  독립 실행형 애플리케이션에는 연결을 관리할 카탈로그가 필요하지 않지만 카탈로그를 사용하여 테넌트 데이터베이스 집합에 다른 작업 범위를 적용할 수 있습니다. 예를 들어, 탄력적 쿼리는 카탈로그를 사용하여 교차 테넌트 보고를 위해 쿼리가 배포되는 데이터베이스 집합을 결정할 수 있습니다.

## <a name="elastic-database-client-library"></a>Elastic Database 클라이언트 라이브러리

Wingtip 애플리케이션 예제에서 카탈로그는 EDCL([Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md))의 분할 관리 기능을 사용하여 구현됩니다.  라이브러리를 사용하면 애플리케이션에서 데이터베이스에 저장된 Shard Map을 생성, 관리 및 사용할 수 있습니다. Wingtip Tickets 샘플에서 카탈로그는 *테넌트 카탈로그* 데이터베이스에 저장됩니다.  Shard는 테넌트의 데이터가 저장된 Shard(데이터베이스)에 테넌트 키를 매핑합니다.  EDCL 함수는 각 Shard에 저장된 *로컬 Shard Map* 및 *테넌트 카탈로그* 데이터베이스의 테이블에 저장된 *전역 Shard Map*을 관리합니다.

EDCL 함수는 애플리케이션이나 PowerShell 스크립트로 호출하여 Shard Map에 항목을 생성하고 관리할 수 있습니다. 다른 EDCL 함수를 사용하여 Shard 집합을 검색하거나 지정된 테넌트 키의 올바른 데이터베이스에 연결할 수 있습니다. 

> [!IMPORTANT]
> 테넌트 데이터베이스의 카탈로그 데이터베이스 또는 로컬 Shard Map의 데이터를 직접 편집하지 마십시오. 직접 업데이트는 데이터 손상의 위험이 높아 지원되지 않습니다. 대신, EDCL API만 사용하여 매핑 데이터를 편집합니다.

## <a name="tenant-provisioning"></a>테넌트 프로비전 

각 테넌트에는 새 Azure 리소스 그룹이 필요하며, 해당 그룹 내에 리소스를 프로비전하기 전에 만들어야 합니다. 리소스 그룹이 존재하면 Azure Resource Management 템플릿을 사용하여 애플리케이션 구성 요소와 데이터베이스를 배포한 다음, 데이터베이스 연결을 구성할 수 있습니다. 데이터베이스 스키마를 초기화하려면 템플릿에 bacpac 파일을 가져옵니다.  또는 ‘템플릿’ 데이터베이스의 복사본으로 데이터베이스를 만들 수 있습니다.  그런 다음, 데이터베이스는 초기 장소 데이터로 업데이트되고 카탈로그에 등록됩니다.

## <a name="tutorial"></a>자습서

이 자습서에서는 다음 방법에 대해 알아봅니다.

* 카탈로그 프로비전
* 앞서 카탈로그에 배포한 샘플 테넌트 데이터베이스 등록
* 추가 테넌트 프로비전 및 카탈로그에 등록

Azure Resource Manager 템플릿은 애플리케이션을 배포 및 구성하고, 테넌트 데이터베이스를 만든 다음, bacpac 파일을 가져와서 초기화하는 데 사용됩니다. 가져오기 요청은 처리되기 전에 몇 분 동안 큐에 대기할 수 있습니다.

이 자습서의 끝 부분에는 각 데이터베이스가 카탈로그에 등록된 독립 실행형 테넌트 애플리케이션이 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다. 

* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.
* 세 가지 샘플 테넌트 앱이 배포되어 있습니다. 앱을 5분 내에 배포하려면 [Wingtip Tickets SaaS 독립 실행형 애플리케이션 배포 및 탐색](saas-standaloneapp-get-started-deploy.md)을 참조하세요.

## <a name="provision-the-catalog"></a>카탈로그 프로비전

이 작업에서는 모든 테넌트 데이터베이스를 등록하는 데 사용되는 카탈로그를 프로비전하는 방법을 알아봅니다. 다음을 수행합니다. 

* Azure 리소스 관리 템플릿을 사용하여 **카탈로그 데이터베이스를 프로비전**합니다. 데이터베이스는 bacpac 파일을 가져와서 초기화됩니다.  
* 이전에 배포한 **샘플 테넌트 앱을 등록**합니다.  각 테넌트는 테넌트 이름의 해시로 생성된 키를 사용하여 등록됩니다.  테넌트 이름은 카탈로그의 확장 테이블에도 저장됩니다.

1. PowerShell ISE에서 *...\Learning Modules\UserConfig.psm*을 열고 **\<user\>** 값을 세 가지 애플리케이션 예제를 배포할 때 사용한 값으로 업데이트합니다.  **파일을 저장합니다**.  
1. PowerShell ISE에서 *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1*을 열어서 **$Scenario = 1**을 설정합니다. 테넌트 카탈로그를 배포하고 미리 정의된 테넌트를 등록합니다.

1. `& $PSScriptRoot\New-Catalog.ps1`이라고 표시된 줄에 커서를 두고 중단점을 추가한 다음, **F9**를 누릅니다.

    ![추적용 중단점 설정](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. **F5** 키를 눌러 스크립트를 실행합니다. 
1.  스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 New-Catalog.ps1 스크립트를 한 단계씩 이동합니다.
1.  디버그 메뉴 옵션(F10 및 F11 키)을 사용하여 스크립트 실행을 추적하면서 피호출 함수로 이동합니다.
    *   PowerShell 스크립트를 디버깅하는 방법에 대한 자세한 내용은 [PowerShell 스크립트 사용 및 디버깅 관련 팁](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)을 참조하세요.

스크립트가 완료되면 카탈로그가 존재하며 모든 샘플 테넌트가 등록됩니다. 

이제 만든 리소스를 살펴봅니다.

1. [Azure 포털](https://portal.azure.com/)을 열어서 리소스 그룹으로 이동합니다.  **wingtip-sa-catalog-\<user\>** 리소스 그룹을 열고 카탈로그 서버 및 데이터베이스를 메모합니다.
1. 포털에서 데이터베이스를 열고 왼쪽 메뉴에서 *데이터 탐색기*를 선택합니다.  로그인 명령을 클릭 하 고 암호 입력 = **P\@ssword1**합니다.


1. *tenantcatalog* 데이터베이스의 스키마로 이동합니다.  
   * `__ShardManagement` 스키마에 있는 개체는 모두 Elastic Database 클라이언트 라이브러리에서 제공됩니다.
   * `Tenants` 테이블과 `TenantsExtended` 뷰는 추가 값을 제공하기 위해 카탈로그를 확장하는 방법을 보여주는 샘플에 추가된 확장입니다.
1. `SELECT * FROM dbo.TenantsExtended` 쿼리를 실행합니다.          

   ![데이터 탐색기](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    데이터 탐색기를 사용하는 대신 SQL Server Management Studio에서 데이터베이스에 연결할 수 있습니다. 이렇게 하려면 서버 wingtip-에 연결합니다. 

    
    카탈로그에서 직접 데이터를 편집하지 말아야 하며 항상 분할 관리 API를 사용해야 합니다. 

## <a name="provision-a-new-tenant-application"></a>새 테넌트 애플리케이션 프로비전

이 작업에서는 단일 테넌트 애플리케이션을 프로비전하는 방법을 알아봅니다. 다음을 수행합니다.  

* 테넌트에 대한 **새 리소스 그룹을 만듭니다**. 
* Azure 리소스 관리 템플릿을 사용하여 새 리소스 그룹에 **애플리케이션 및 데이터베이스를 프로비전**합니다.  이 작업에는 bacpac 파일을 가져와서 일반 스키마 및 참조 데이터로 데이터베이스를 초기화하는 작업이 포함됩니다. 
* **기본 테넌트 정보로 데이터베이스를 초기화합니다**. 이 작업에는 이벤트 웹 사이트의 배경으로 사용되는 사진을 결정하는 장소 유형을 지정하는 작업이 포함됩니다. 
* **카탈로그 데이터베이스에 데이터베이스를 등록합니다**. 

1. PowerShell ISE에서 *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1*을 열어서 **$Scenario = 2**를 설정합니다. 테넌트 카탈로그를 배포하고 미리 정의된 테넌트를 등록합니다.

1. `& $PSScriptRoot\New-TenantApp.ps1`이라고 표시된 49행에 커서를 두고 중단점을 추가한 다음, **F9**를 누릅니다.
1. **F5** 키를 눌러 스크립트를 실행합니다. 
1.  스크립트 실행이 중단점에서 중지된 후 **F11**을 눌러 New-Catalog.ps1 스크립트를 한 단계씩 이동합니다.
1.  디버그 메뉴 옵션(F10 및 F11 키)을 사용하여 스크립트 실행을 추적하면서 피호출 함수로 이동합니다.

테넌트가 프로비전된 후 새 테넌트의 이벤트 웹 사이트가 열립니다.

   ![Red Maple Racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

그런 다음, Azure Portal에서 생성된 새 리소스를 검사할 수 있습니다. 

   ![Red Maple Racing 리소스](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>청구를 중지하도록 리소스 그룹 삭제

샘플 탐색을 마친 후에는 만든 모든 리소스 그룹을 삭제하여 관련 청구를 중지합니다.

## <a name="additional-resources"></a>추가 리소스

- 다중 테넌트 SaaS 데이터베이스 애플리케이션에 대해 자세히 알아보려면 [다중 테넌트 SaaS 애플리케이션에 대한 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * Wingtip 티켓 SaaS 독립형 애플리케이션 배포 방법
> * 앱을 구성하는 서버 및 데이터베이스 정보.
> * 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

[Wingtip Tickets SaaS 애플리케이션](saas-dbpertenant-wingtip-app-overview.md)의 테넌트별 데이터베이스 버전을 사용하여 다양한 교차 테넌트 시나리오를 지원하는 데 카탈로그가 어떻게 사용되는지 살펴볼 수 있습니다.  

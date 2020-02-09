---
title: 다중 테넌트 앱 예제-정문 SaaS
description: Azure SQL Database를 사용하는 샘플 다중 테넌트 애플리케이션인 Wingtip Tickets SaaS 예제를 설치 및 실행하기 위한 단계와 지침을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 162d1f269c65ad98afa30e8e96370bbdceca99bd
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132301"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Wingtip Tickets 샘플 SaaS 앱을 사용하기 위한 일반적인 지침

이 문서에는 Azure SQL Database를 사용하는 Wingtip Tickets 샘플 SaaS 애플리케이션을 실행하기 위한 일반적인 지침이 포함되어 있습니다.

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Wingtip Tickets SaaS 스크립트 다운로드 및 차단 해제

zip 파일이 외부 원본에서 다운로드되고 추출될 때 Windows에서 실행 가능한 콘텐츠(스크립트, dll)를 차단할 수 있습니다. Zip 파일에서 스크립트를 추출할 경우 **아래 단계에 따라 추출하기 전에 .zip 파일을 차단 해제**하세요. 이렇게 하면 스크립트를 실행할 수 있습니다.

1. 탐색하려는 데이터베이스 테넌트 패턴에 대해서는 Wingtip Tickets SaaS GitHub 리포지토리로 이동하세요.
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. **복제 또는 다운로드**를 클릭합니다.
3. **zip 다운로드**를 클릭하고 파일을 저장합니다.
4. 해당 zip 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. zip 파일 이름은 리포지토리 이름에 해당합니다. 예: _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. **일반** 탭에서 **차단 해제**를 선택합니다.
6. **확인**을 클릭합니다.
7. 파일의 압축을 풉니다.

스크립트는 *..\\Learning Modules* 폴더에 있습니다.


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Wingtip Tickets PowerShell 스크립트 작업

샘플을 최대한 활용하려면 제공된 스크립트를 자세히 살펴보아야 합니다. 실행될 때 중단점을 사용하고 스크립트를 단계별로 이동하면서 다양한 SaaS 패턴이 구현된 방식을 살펴보세요. 가장 잘 이해할 수 있도록 제공된 스크립트 및 모듈을 손쉽게 단계별로 실행하려면 [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise)를 사용하는 것이 좋습니다.

### <a name="update-the-configuration-file-for-your-deployment"></a>배포에 대한 구성 파일 업데이트

배포 중에 설정한 사용자 값 및 리소스 그룹을 사용하여 **UserConfig.psm1** 파일을 편집합니다.

1. *PowerShell ISE*를 열고 ...\\Learning Modules\\*UserConfig.psm1*을 로드합니다.
2. 배포에 대한 특정 값(줄 10 및 11에서만)으로 *ResourceGroupName* 및 *Name*을 업데이트합니다.
3. 변경 내용을 저장합니다.

여기서 이러한 값을 설정하면 모든 스크립트에서 이러한 배포별 값을 업데이트할 필요가 없습니다.

### <a name="execute-the-scripts-by-pressing-f5"></a>F5 키를 눌러 스크립트 실행

여러 스크립트에서 *$PSScriptRoot*를 사용하여 폴더를 탐색할 수 있으며 *$PSScriptRoot*는 **F5** 키를 눌러 스크립트를 실행할 때만 평가됩니다.  선택 영역을 강조 표시 하 고 실행 (**F8**) 하면 오류가 발생할 수 있으므로 스크립트를 실행할 때 **f5** 키를 누릅니다.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>스크립트를 단계별로 실행하여 구현 검사

스크립트를 이해하는 가장 좋은 방법은 단계별로 실행하여 수행되는 작업을 확인하는 것이 좋습니다. 개략적인 워크플로를 따르는 포함된 **Demo-** 스크립트를 확인합니다. **Demo-** 스크립트는 각 작업을 완료하는 데 필요한 단계를 보여 주므로 중단점을 설정하고 개별 호출로 드릴다운하여 여러 SaaS 패턴에 대한 구현 정보를 확인합니다.

PowerShell 스크립트 탐색 및 단계별 실행에 대한 팁

- PowerShell ISE에서 **Demo-** 스크립트를 엽니다.
- **F5** 키를 사용하여 실행하거나 계속합니다. 스크립트의 선택 항목을 실행할 때 **$PSScriptRoot**가 평가되지 않으므로 *F8* 키는 사용하지 않는 것이 좋습니다.
- 행을 클릭하거나 선택하고 **F9** 키를 눌러 중단점을 배치합니다.
- **F10**을 사용하여 함수 또는 스크립트 호출을 실행합니다.
- **F11**을 사용하여 함수 또는 스크립트 호출 코드를 한 단계씩 실행합니다.
- **Shift + F11**을 사용하여 현재 함수 또는 스크립트 호출을 종료합니다.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>데이터베이스 스키마를 탐색하고 SSMS를 사용하여 SQL 쿼리를 실행합니다.

[SSMS(SQL Server Management Studio)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 애플리케이션 서버 및 데이터베이스에 연결하고 탐색합니다.

처음에 배포에는 연결할 테넌트 및 카탈로그 SQL Database 서버가 있습니다. 서버 이름 지정은 데이터베이스 테넌트 패턴에 따라 다릅니다(구체적인 내용은 아래 참조).

   - **독립 실행형 애플리케이션:** 각 테넌트에 대한 서버(예: *contosoconcerthall-&lt;User&gt;* 서버) 및 *catalog-sa-&lt;User&gt;*
   - **테넌트당 데이터베이스:** *tenants1-dpt-&lt;User&gt;* 및 *catalog-dpt-&lt;User&gt;* 서버
   - **다중 테넌트 데이터베이스:** *tenants1-mt-&lt;User&gt;* 및 *catalog-mt-&lt;User&gt;* 서버

성공적인 데모 연결을 위해 모든 서버에 모든 IP의 통과를 허용하는 [방화벽 규칙](sql-database-firewall-configure.md)이 있습니다.


1. *SSMS*를 열고 테넌트에 연결합니다. 서버 이름은 선택한 데이터베이스 테넌트 패턴에 따라 다릅니다(구체적인 내용은 아래 참조).
    - **독립 실행형 애플리케이션:** 개별 테넌트의 서버(예: *contosoconcerthall-&lt;User&gt;.database.windows.net*)
    - **테넌트당 데이터베이스:** *tenants1-dpt-&lt;User&gt;.database.windows.net*
    - **다중 테넌트 데이터베이스:** *tenants1-mt-&lt;User&gt;.database.windows.net*
2. **연결** > **데이터베이스 엔진...** 을 클릭합니다.

   ![카탈로그 서버](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. 데모 자격 증명: Login = *developer*, Password = *P\@ssword1*

    아래 이미지는 *테넌트당 데이터베이스* 패턴의 로그인을 보여줍니다.
    ![연결](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. 2-3단계를 반복하고 카탈로그 서버에 연결(선택한 데이터베이스 테넌트 패턴에 따른 특정 서버 이름에 대해서는 아래 참조)
    - **독립 실행형 애플리케이션:** *catalog-sa-&lt;User&gt;.database.windows.net*
    - **테넌트당 데이터베이스:** *catalog-dpt-&lt;User&gt;.database.windows.net*
    - **다중 테넌트 데이터베이스:** *catalog-mt-&lt;User&gt;.database.windows.net*


성공적으로 연결된 후 모든 서버가 표시됩니다. 프로비전한 테넌트에 따라 데이터베이스 목록이 다를 수 있습니다.

아래 이미지는 *테넌트당 데이터베이스* 패턴의 로그인을 보여줍니다.

![개체 탐색기](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>다음 단계
- [Wingtip 티켓 SaaS 독립형 애플리케이션 배포](saas-standaloneapp-get-started-deploy.md)
- [Wingtip Tickets SaaS 테넌트당 데이터베이스 애플리케이션 배포](saas-dbpertenant-get-started-deploy.md)
- [Wingtip Tickets SaaS 다중 테넌트 데이터베이스 애플리케이션 배포](saas-multitenantdb-get-started-deploy.md)


---
title: 테넌트당 데이터베이스 SaaS 자습서 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database를 사용하는 테넌트 패턴당 데이터베이스 및 기타 SaaS 패턴을 보여주는 Wingtip Tickets SaaS 다중 테넌트 애플리케이션을 배포하고 탐색합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 14f76a716447e09299cfa18d6758245706c7b481
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556531"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>SQL Database로 테넌트별 데이터베이스 패턴을 사용하는 다중 테넌트 SaaS 앱 배포 및 탐색

이 자습서에서는 테넌트의 Wingtip Tickets SaaS 테넌트당 데이터베이스 애플리케이션(Wingtip)을 배포하고 탐색합니다. 이 앱은 테넌트당 데이터베이스 패턴을 사용하여 여러 테넌트의 데이터를 저장합니다. 이 앱은 SaaS 시나리오를 활성화하는 방법을 간소화하는 Azure SQL Database의 기능을 보여주도록 설계되었습니다.

**Azure에 배포**를 선택하고 5분 후에 다중 테넌트 SaaS 애플리케이션이 설치됩니다. 앱은 클라우드에서 실행되는 SQL 데이터베이스를 포함합니다. 앱은 각각 고유한 데이터베이스를 사용하여 3개의 샘플 테넌트에서 배포됩니다. 모든 데이터베이스는 SQL 탄력적 풀에 배포됩니다. 앱은 Azure 구독에 배포됩니다. 앱의 개별 구성 요소를 탐색하고 작업할 수 있는 전체 액세스 권한이 있습니다. 애플리케이션 C# 소스 코드 및 관리 스크립트는 [WingtipTicketsSaaS-DbPerTenant GitHub 리포지토리][github-wingtip-dpt]에서 사용할 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> - Wingtip SaaS 애플리케이션을 배포하는 방법.
> - 애플리케이션 소스 코드 및 관리 스크립트를 가져올 위치.
> - 서버, 풀 및 앱을 구성하는 데이터베이스 정보.
> - *카탈로그*를 통해 테넌트가 데이터에 매핑되는 방법.
> - 새 테넌트를 프로비전하는 방법.
> - 앱에서 테넌트 활동을 모니터링하는 방법.

[관련된 일련의 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)에서는 다양한 SaaS 디자인 및 관리 패턴을 탐색합니다. 이 자습서는 이 초기 배포 이후에도 빌드됩니다. 자습서를 사용하는 경우 제공된 스크립트를 검토하여 다양한 SaaS 패턴을 구현하는 방법을 확인할 수 있습니다. 스크립트는 SaaS 애플리케이션 개발을 간소화하는 SQL Database의 기능을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure PowerShell이 설치되어 있는지 확인합니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS 애플리케이션 배포

### <a name="plan-the-names"></a>이름 계획

이 섹션의 단계에서 리소스 이름이 전역적으로 고유한지 확인하는 데 사용되는 사용자 값을 제공합니다. 또한 앱의 배포에 의해 생성된 모든 리소스를 포함하는 리소스 그룹에 대한 이름을 제공합니다. Ann Finley라는 사람의 경우 다음을 제안합니다.

- **사용자**: *af1*은 Ann Finley의 이니셜에 숫자를 더한 값으로 이루어집니다. 앱을 두 번째로 배포하는 경우 다른 값을 사용합니다. 예는 af2입니다.
- **리소스 그룹**: *wingtip-dpt-af1*은 테넌트당 데이터베이스 앱임을 나타냅니다. 사용자 이름 af1을 추가하여 리소스 그룹 이름과 해당 그룹에 포함된 리소스 이름이 연결됩니다.

이제 이름을 선택하고, 기록합니다.

### <a name="steps"></a>단계

1. Azure Portal에서 Wingtip Tickets SaaS 테넌트당 데이터베이스 배포 템플릿을 열려면 **Azure에 배포**를 선택합니다.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. 템플릿에 필수 매개 변수의 값을 입력합니다.

    > [!IMPORTANT]
    > 일부 인증 및 서버 방화벽은 데모 목적으로 의도적으로 보호되지 않습니다. 새 리소스 그룹을 만드는 것이 좋습니다. 기존 리소스 그룹, 서버 또는 풀을 사용하지 마세요. 이 애플리케이션, 스크립트 또는 배포된 리소스를 프로덕션에 사용하지 마세요. 관련된 결제를 중지하려면 애플리케이션을 완료할 때 이 리소스 그룹을 삭제합니다.

    - **리소스 그룹**: **새로 만들기**를 선택하고 이전에 선택한 고유 이름을 리소스 그룹 이름으로 입력합니다.
    - **Location**: 드롭다운 목록에서 위치를 선택합니다.
    - **User**: 앞에서 선택한 사용자 이름 값을 사용합니다.

1. 애플리케이션을 배포합니다.

    a. 사용 약관에 동의하려면 선택합니다.

    b. **구매**를 선택합니다.

1. 배포 상태를 모니터링하려면 **알림**(검색 상자 오른쪽의 벨 아이콘)을 선택합니다. Wingtip Tickets SaaS 앱을 배포하는 데는 5분 정도 걸립니다.

   ![배포 성공](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Wingtip Tickets 관리 스크립트 다운로드 및 차단 해제

애플리케이션이 배포되는 동안 소스 코드 및 관리 스크립트를 다운로드합니다.

> [!IMPORTANT]
> zip 파일이 외부 원본에서 다운로드되고 추출될 때 Windows에서 실행 가능한 콘텐츠(스크립트 및 DLL)를 차단할 수 있습니다. 스크립트의 압축을 풀기 전에 .zip 파일을 차단 해제하는 단계를 따릅니다. 차단을 해제하면 스크립트를 실행할 수 있습니다.

1. [WingtipTicketsSaaS-DbPerTenant GitHub 리포지토리][github-wingtip-dpt]로 이동합니다.
1. **복제 또는 다운로드**를 선택합니다.
1. **ZIP 다운로드**를 선택한 다음, 파일을 저장합니다.
1. **WingtipTicketsSaaS-DbPerTenant-master.zip** 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. **일반** 탭에서 **차단 해제** > **적용**을 선택합니다.
1. **확인**을 선택하고, 파일의 압축을 풉니다.

스크립트는 ...\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules 폴더에 있습니다.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>이 배포에 대한 사용자 구성 파일 업데이트

스크립트를 실행하기 전에 UserConfig 파일에서 리소스 그룹 및 사용자 값을 업데이트합니다. 이러한 변수를 배포 중에 사용한 값으로 설정합니다.

1. PowerShell ISE에서 ...\\Learning Modules\\**UserConfig.psm1**을 엽니다.
1. 배포에 대한 특정 값(줄 10 및 11에서만)으로 **ResourceGroupName** 및 **Name**을 업데이트합니다.
1. 변경 내용을 저장합니다.

이러한 값은 거의 모든 스크립트에서 참조됩니다.

## <a name="run-the-application"></a>애플리케이션 실행

앱은 이벤트를 호스트하는 장소를 표시합니다. 장소 유형에는 콘서트 홀, 재즈 클럽 및 스포츠 클럽 등이 포함됩니다. Wingtip Tickets에서 장소는 테넌트로 등록됩니다. 테넌트가 되면 장소에 이벤트를 나열하고 고객에게 티켓을 판매하는 편리한 방법을 제공할 수 있습니다. 각 장소는 해당 이벤트를 나열하고 티켓을 판매하는 개인 설정된 웹 사이트를 얻게 됩니다.

앱에서 내부적으로 각 테넌트는 SQL 탄력적 풀에 배포된 SQL 데이터베이스를 가져옵니다.

중앙 **이벤트 허브** 페이지는 배포에 있는 테넌트의 링크 목록을 제공합니다.

1. URL을 사용하여 웹 브라우저: http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net에서 이벤트 허브를 엽니다. &lt;user&gt;를 배포의 사용자 값으로 대체합니다.

    ![이벤트 허브](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. 이벤트 허브에서  **Fabrikam Jazz Club** 을 선택합니다.

    ![이벤트](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip 응용 프로그램에서는  [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md)를 사용하여 들어오는 요청의 분산을 제어합니다. 특정 테넌트의 이벤트 페이지에 액세스하는 URL은 다음 형식을 사용합니다.

- http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/fabrikamjazzclub

    이전 형식의 부분을 다음 표에서 설명합니다.

    | URL 부분        | 설명       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Wingtip 앱의 이벤트 부분입니다.<br /><br /> *-dpt*는 Wingtip Tickets의 *테넌트당 데이터베이스* 구현을 다른 구현과 구분합니다. 다른 구현의 예로는 테넌트당 *단일* 앱(*-sa*) 또는 *다중 테넌트 데이터베이스*(*-mt*) 구현 등이 있습니다. |
    | .*&lt;user&gt;* | 예제에서 *af1*입니다. |
    | .trafficmanager.net/ | Traffic Manager, 기준 URL입니다. |
    | fabrikamjazzclub | Fabrikam Jazz Club이라는 테넌트를 식별합니다. |
    | &nbsp; | &nbsp; |

- 테넌트 이름은 이벤트 앱에 의해 URL에서 구문 분석됩니다.
- 테넌트 이름은 키를 만드는 데 사용됩니다.
- 테넌트 데이터베이스의 위치를 가져오기 위해 카탈로그에 액세스하는 데 키를 사용합니다.
  - 카탈로그는 *분할 맵 관리*를 사용하여 구현됩니다.
- 이벤트 허브는 각 테넌트에 대한 이벤트 페이지 URL의 목록을 구성하기 위해 카탈로그의 확장 메타데이터를 사용합니다.

프로덕션 환경에서는 일반적으로 CNAME DNS 레코드를 만들어  [*회사 인터넷 도메인*](../traffic-manager/traffic-manager-point-internet-domain.md) 이 Traffic Manager DNS 이름을 가리키도록 합니다.

> [!NOTE]
> 이 자습서에서는 Traffic Manager의 용도가 무엇인지 즉각 명백하지 않을 수 있습니다. 이 자습서 시리즈의 목적은 규모 있는 복잡한 프로덕션 환경을 처리할 수 있는 패턴을 소개하기 위함입니다. 예를 들어 이러한 경우 여러 웹앱이 데이터베이스와 함께 공동 배치되고 전 세계에 분산되며, 이러한 인스턴스 간에 라우팅하려면 Traffic Manager가 필요합니다.
다른 자습서 시리즈에서는 [지역 복원](saas-dbpertenant-dr-geo-restore.md) 및 [지역 복제](saas-dbpertenant-dr-geo-replication.md) 자습서이기는 하지만 Traffic Manager의 용도를 보여 줍니다. 이러한 자습서에서 Traffic Manager는 지역 가동 중단 시 SaaS 앱의 복구 인스턴스로 전환하는 데 사용됩니다.

## <a name="start-generating-load-on-the-tenant-databases"></a>테넌트 데이터베이스에서 부하 생성 시작

이제 앱이 배포되었으므로 작동해 보겠습니다.

*Demo-LoadGenerator* PowerShell 스크립트는 모든 테넌트 데이터베이스에서 실행되는 워크로드를 시작합니다. 많은 SaaS 앱상의 실제 부하는 간헐적이고 예측할 수 없습니다. 이 형식의 부하를 시뮬레이션하려면 생성기는 각 테넌트의 작업에 대해 임의의 스파이크 또는 버스트된 부하를 생성합니다. 버스트는 임의 간격으로 발생합니다. 부하 패턴이 나타나는 데는 몇 분 정도가 걸립니다. 부하를 모니터링하기 전에 생성기를 최소한 3~4분 실행하겠습니다.

1. PowerShell ISE에서 ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* 스크립트를 엽니다.
2. F5 키를 눌러 스크립트를 실행하고 부하 생성기를 시작합니다. 지금은 기본 매개 변수 값을 그대로 적용합니다.
3. Azure 계정에 로그인하고, 필요한 경우 사용하려는 구독을 선택합니다.

로드 생성기 스크립트는 카탈로그의 각 데이터베이스에 대한 백그라운드 작업을 시작한 다음, 중지됩니다. 로드 생성기 스크립트를 다시 실행하는 경우, 새로운 스크립트를 시작하기 전에 실행되고 있는 백그라운드 작업이 중지됩니다.

### <a name="monitor-the-background-jobs"></a>백그라운드 작업을 모니터링합니다.

백그라운드 작업을 제어하고 모니터링하려면 다음과 같은 cmdlet을 사용합니다.

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

### <a name="demo-loadgeneratorps1-actions"></a>Demo-LoadGenerator.ps1 작업

*Demo-LoadGenerator.ps1*은 고객 트랜잭션의 활성 워크로드를 모방합니다. 다음 단계는 *Demo-LoadGenerator.ps1*이 시작하는 작업의 시퀀스를 설명합니다.

1. *Demo-LoadGenerator.ps1* 전경에서 *LoadGenerator.ps1*을 시작합니다.

    - .ps1 파일은 모두 Learning Modules\\Utilities\\ 폴더 아래에 저장됩니다.

2. *LoadGenerator.ps1*은 카탈로그의 모든 테넌트 데이터베이스를 반복합니다.

3. *LoadGenerator.ps1*은 각 테넌트 데이터베이스에 대한 백그라운드 PowerShell 작업을 시작합니다.

    - 기본적으로 백그라운드 작업은 120분 동안 실행됩니다.
    - 각 작업은 *sp_CpuLoadGenerator*를 실행하여 하나의 테넌트 데이터베이스에서 CPU 기반 로드를 발생시킵니다. 로드의 강도 및 지속 기간은 `$DemoScenario`에 따라 다릅니다.
    - *sp_CpuLoadGenerator*는 높은 CPU 로드를 초래하는 SQL SELECT 문 주위에서 반복합니다. SELECT 문제 간의 시간 간격은 제어 가능한 CPU 로드를 만들기 위한 매개 변수 값에 따라 다릅니다. 더 현실적인 로드를 시뮬레이트하기 위해 로드 수준 및 간격이 임의로 지정됩니다.
    - .sql 파일은 *WingtipTenantDB\\dbo\\StoredProcedures\\* 아래에 저장됩니다.

4. `$OneTime = $false`의 경우 부하 생성기는 백그라운드 작업을 시작한 다음, 계속 실행합니다. 10초마다 프로비전되는 새 테넌트에 대해 모니터링합니다. `$OneTime = $true`를 설정하는 경우 LoadGenerator가 백그라운드 작업을 시작한 다음, 포그라운드에서 실행을 중지합니다. 이 자습서에서는 `$OneTime = $false`를 사용하지 않습니다.

   로드 생성기를 중지하거나 다시 시작하려면 Ctrl+C 또는 Ctrl+Break를 사용하세요.

   로드 생성기를 포그라운드에서 실행하는 경우 다른 PowerShell ISE 인스턴스를 사용하여 다른 PowerShell 스크립트를 실행합니다.

&nbsp;

다음 섹션을 계속하기 전에 부하 생성기를 작업 호출 상태로 계속 실행합니다.

## <a name="provision-a-new-tenant"></a>새 테넌트 프로비전

초기 배포는 세 가지 샘플 테넌트를 만듭니다. 이제 또 다른 테넌트를 만들어 배포된 애플리케이션에 어떻게 영향을 미치는지 확인합니다. Wingtip 앱에서 새 테넌트를 프로비전하는 워크플로는 [프로비전 및 카탈로그 자습서](saas-dbpertenant-provision-and-catalog.md)에 설명됩니다. 이 단계에서는 새 테넌트를 만드는 데 1분이 걸리지 않습니다.

1. 새 PowerShell ISE를 엽니다.
2. ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*을 엽니다.
3. 스크립트를 실행하려면 F5 키를 누릅니다. 지금은 기본값을 그대로 적용합니다.

   > [!NOTE]
   > 대부분의 Wingtip SaaS 스크립트에서는 *$PSScriptRoot*를 사용하여 다른 스크립트에 있는 함수를 호출하기 위해 폴더를 탐색합니다. 이 변수는 F5 키를 눌러 전체 스크립트를 실행할 경우에만 평가됩니다. F8 키를 사용하여 선택 영역을 강조 표시하고 실행하면 오류가 발생할 수 있습니다. 스크립트를 실행하려면 F5 키를 누릅니다.

새 테넌트 데이터베이스는 다음과 같습니다.

- SQL 탄력적 풀에 만들어집니다.
- 초기화되었습니다.
- 카탈로그에 등록됩니다.

성공적으로 프로비전한 후에 새 테넌트의 *이벤트* 사이트가 브라우저에 나타납니다.

![새 테넌트](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Events Hub를 새로 고치면 목록에 새 테넌트가 나타납니다.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>서버, 풀 및 테넌트 데이터베이스 탐색

테넌트 컬렉션에 대해 부하 실행을 시작했으므로 배포된 리소스 중 일부를 살펴보겠습니다.

1.  [Azure Portal](https://portal.azure.com)에서 SQL 서버 목록으로 이동합니다. 그런 다음,  **catalog-dpt-&lt;USER&gt;**  서버를 엽니다.
    - 카탈로그 서버에는 두 가지 데이터베이스인 **tenantcatalog** 및 **basetenantdb**(새 테넌트를 만들기 위해 복사한 템플릿 데이터베이스)가 포함됩니다.

   ![데이터베이스](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. SQL 서버 목록으로 이동합니다.

3. 테넌트 데이터베이스를 보관하는 **tenants1-dpt-&lt;USER&gt;**  서버를 엽니다.

4. 다음 항목을 확인합니다.

    - 각 테넌트 데이터베이스는 50-eDTU 표준 풀의 **탄력적 표준** 데이터베이스입니다.
    - 이전에 프로비전한 테넌트 데이터베이스인 Red Maple Racing 데이터베이스입니다.

   ![데이터베이스가 있는 서버](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>풀 모니터링

*LoadGenerator.ps1*을 몇 분 동안 실행한 후에 일부 모니터링 기능을 살펴보기에 충분한 데이터를 사용할 수 있어야 합니다. 이러한 기능은 풀 및 데이터베이스에 빌드됩니다.

**tenants1-dpt-&lt;user&gt;** 서버로 이동하고,  **Pool1** 을 선택하여 풀의 리소스 사용률을 확인합니다. 다음 차트에서 부하 생성기를 1시간 동안 실행했습니다.

   ![풀 모니터링](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- **리소스 사용률**이라는 레이블의 첫 번째 차트는 풀 eDTU 사용량을 보여줍니다.
- 두 번째 차트는 풀에서 가장 활동적인 데이터베이스 5개의 eDTU 사용률을 보여줍니다.

두 차트는 탄력적 풀 및 SQL Database가 예기치 않은 SaaS 애플리케이션 워크로드에 얼마나 적합한지 설명합니다. 차트에서는 4개의 데이터베이스가 40개의 eDTU로 버스트되는 것을 보여줍니다. 하지만 모든 데이터베이스는 안전하게 50-eDTU 풀에서 지원됩니다. 50-eDTU 풀은 더 많은 워크로드를 지원할 수 있습니다. 데이터베이스가 단일 데이터베이스로 프로비전되는 경우 각 데이터베이스는 버스트를 지원하는 S2(50 DTU)여야 합니다. 단일 S2 데이터베이스 4개의 비용은 풀 가격의 거의 3배입니다. 실제 상황에서 SQL Database 고객은 200 eDTU 풀에서 데이터베이스를 최대 500개까지 실행합니다. 자세한 내용은 [성능 모니터링 자습서](saas-dbpertenant-performance-monitoring.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- 자세한 내용은 [Wingtip Tickets SaaS 테넌트당 데이터베이스 애플리케이션을 기반으로 빌드되는 추가 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)를 참조하세요.
- 탄력적 풀에 대한 자세한 내용은  [Azure SQL 탄력적 풀이란?](sql-database-elastic-pool.md)을 참조하세요.
- 탄력적 작업에 대한 자세한 내용은  [스케일 아웃된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md)를 참조하세요.
- 다중 테넌트 SaaS 응용 프로그램에 대한 자세한 내용은  [다중 테넌트 SaaS 응용 프로그램에 대한 디자인 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> - Wingtip Tickets SaaS 애플리케이션을 배포하는 방법.
> - 서버, 풀 및 앱을 구성하는 데이터베이스 정보.
> - *카탈로그*를 통해 테넌트가 데이터에 매핑되는 방법.
> - 새 테넌트를 프로비전하는 방법
> - 풀 사용률을 검토하여 테넌트 작업을 모니터링하는 방법
> - 샘플 리소스를 삭제하여 관련 결제를 중지하는 방법

다음으로 [프로비전 및 카탈로그 자습서](saas-dbpertenant-provision-and-catalog.md)를 사용해 보세요.

<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

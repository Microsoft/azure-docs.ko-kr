---
title: Azure Monitor를 사용 하 여 SQL Server 환경 최적화 | Microsoft Docs
description: Azure Monitor를 통해 일정 한 간격으로 위험 및 사용자 환경의 상태를 평가 하기 위해 SQL Health Check 솔루션을 사용할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e297eb57-1718-4cfe-a241-b9e84b2c42ac
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: magoedte
ms.openlocfilehash: 94b23bc29c3c986e6a0cd74e0805b5d47ce35849
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120634"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Azure Monitor에서 SQL Server Health Check 솔루션을 사용 하 여 SQL 환경 최적화

![SQL Health Check 기호](./media/sql-assessment/sql-assessment-symbol.png)

SQL Health Check 솔루션을 사용하여 일정한 간격으로 서버 환경의 위험 및 상태를 평가할 수 있습니다. 이 문서에서는 잠재적인 문제에 대해 올바른 조치를 취할 수 있도록 솔루션 설치를 도와줍니다.

이 솔루션은 배포된 서버 인프라 관련 우선순위가 지정된 권장 사항 목록을 제공합니다. 권장 사항은 신속하게 위험을 이해하고 수정 조치를 취할 수 있도록 여섯 가지 주요 영역으로 분류되어 있습니다.

권장 사항은 수천 번의 고객 방문에서 Microsoft 엔지니어가 얻은 지식과 경험을 기반으로 합니다. 각 권장 사항은 문제가 중요할 수 있는 이유 및 제안된 변경 내용을 구현하는 방법에 대한 지침을 제공합니다.

조직에 가장 중요한 주요 영역을 선택하여 위험이 없는 정상 환경을 실행 중인 프로그램의 진행을 추적할 수 있습니다.

솔루션을 추가하고 평가가 완료되면 주요 영역의 요약 정보가 사용자 환경의 인프라에 대한 **SQL Health Check** 대시보드에 표시됩니다. 다음 섹션에서는 **SQL Health Check** 대시보드의 정보를 사용하는 방법을 설명합니다. 여기서는 이 대시보드의 정보를 보고 SQL Server 인프라에 대한 권장 조치를 취할 수 있습니다.

![SQL Health Check 타일의 이미지](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![SQL Health Check 대시보드의 이미지](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>필수 조건

* SQL Health Check 솔루션을 사용하려면 MMA(Microsoft Monitoring Agent)가 설치된 각 컴퓨터에 지원되는 버전의 .NET Framework 4를 설치해야 합니다.  MMA 에이전트는 System Center 2016 - Operations Manager, Operations Manager 2012 R2 및 Azure Monitor에서 사용됩니다.  
* 이 솔루션은 SQL Server 2012, 2014 및 2016 버전을 지원합니다.
* Azure Marketplace로부터 SQL Health Check 솔루션을 추가하기 위한 Azure Portal의 Log Analytics 작업 영역.  솔루션을 설치하기 위해서는 사용자가 Azure 구독의 관리자 또는 참가자여야 합니다.

  > [!NOTE]
  > 솔루션을 추가하면 에이전트가 있는 서버에 AdvisorAssessment.exe 파일이 추가됩니다. 구성 데이터가 판독되고 처리를 위해 클라우드의 Azure Monitor로 전송됩니다. 논리는 수신된 데이터에 적용되며 클라우드 서비스는 데이터를 기록합니다.
  >
  >

SQL Server 서버에 대 한 상태 검사를 수행 하려면 에이전트와 지원 되는 다음 방법 중 하나를 사용 하 여 Azure Monitor에 대 한 연결 필요 합니다.

1. 서버를 아직 System Center 2016 - Operations Manager 또는 Operations Manager 2012 R2에서 모니터링하지 않는 경우 [MMA(Microsoft Monitoring Agent)](../../azure-monitor/platform/agent-windows.md)를 설치합니다.
2. System Center 2016-Operations Manager 또는 Operations Manager 2012 R2로 모니터링 되며 관리 그룹은 Azure Monitor와 통합 되지 경우 서버 멀티홈으로 Log Analytics 데이터를 수집 하 고 고 서비스로 전달할 수를 수 있습니다. Operations Manager에서 모니터링 합니다.  
3. 그렇지 않고 Operations Manager 관리 그룹이 서비스와 통합된 경우, 작업 영역에서 솔루션을 활성화한 후 [에이전트 관리 컴퓨터 추가](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor)의 단계에 따라 데이터 수집을 위한 도메인 컨트롤러를 추가해야 합니다.  

Operations Manager 관리 그룹에 보고 하는 데이터를 수집 하 여 SQL Server에서 에이전트를 할당 된 관리 서버에 전달 하 고은 관리 서버에서 직접 Azure Monitor에 전송 합니다.  이 데이터는 Operations Manager 데이터베이스에 기록되지 않습니다.  

SQL Server를 Operations Manager에서 모니터링하는 경우 Operations Manager 실행 계정을 구성해야 합니다. 참조 [Azure Monitor에 대 한 Operations Manager 실행 계정](#operations-manager-run-as-accounts-for-log-analytics) 아래 자세한 내용은 합니다.

## <a name="sql-health-check-data-collection-details"></a>SQL Health Check 데이터 수집 세부 정보
SQL Health Check는 사용자가 사용하도록 설정한 에이전트를 통해 다음과 같은 소스에서 데이터를 수집합니다.

* WMI(Windows Management Instrumentation)
* 레지스트리
* 성능 카운터
* SQL Server 동적 관리 뷰 결과

데이터는 SQL Server에서 수집되고 7일 마다 Log Analytics에 전달됩니다.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics용 Operations Manager 실행 계정
Log Analytics에서는 Operations Manager 에이전트와 관리 그룹을 사용하여 데이터를 수집하여 Log Analytics 서비스로 전송합니다. Log Analytics는 부가 가치 서비스를 제공하는 작업을 위해 관리 팩을 빌드합니다. 도메인 사용자 계정과 같은 다른 보안 컨텍스트에서 관리 팩을 실행하려면 각 작업에 워크로드 관련 권한이 필요합니다. Operations Manager 실행 계정을 구성하여 자격 증명 정보를 제공해야 합니다.

다음 정보를 사용하여 SQL Health Check를 위한 Operations Manager 실행 계정을 설정할 수 있습니다.

### <a name="set-the-run-as-account-for-sql-health-check"></a>SQL Health Check에 대한 실행 계정 설정
 SQL Server 관리 팩을 이미 사용 중인 경우 해당 실행 구성을 사용해야 합니다.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>운영 콘솔에서 SQL 실행 계정을 구성하려면
> [!NOTE]
> 기본적으로 관리 팩의 워크플로는 로컬 시스템 계정의 보안 컨텍스트에서 실행됩니다. Operations Manager 관리 그룹에 직접 보고하지 않고 서비스에 직접 연결된 Microsoft Monitoring Agent를 사용하는 경우, 아래 1-5단계를 건너뛰고 T-SQL 또는 PowerShell 샘플을 실행합니다. 사용자 이름은 NT AUTHORITY\SYSTEM으로 지정합니다.
>
>

1. Operations Manager에서 운영 콘솔을 열고 **관리**를 클릭합니다.
2. **실행 구성**에서 **프로필**을 클릭하고 **프로필로 SQL 평가 실행**을 엽니다.
3. **실행 계정** 페이지에서 **추가**를 클릭합니다.
4. SQL Server에 필요한 자격 증명을 포함하는 Windows 실행 계정을 선택하거나 **새로 만들기** 를 클릭하여 계정을 하나를 만듭니다.

   > [!NOTE]
   > 실행 계정 유형은 Windows이어야 합니다. 실행 계정은 SQL Server 인스턴스를 호스팅하는 모든 Windows 서버에서 로컬 관리자 그룹의 일부이어야 합니다.
   >
   >
5. **저장**을 클릭합니다.
6. 상태 검사에 실행 계정으로 필요한 최소 사용 권한을 부여하도록 각 SQL Server 인스턴스에서 다음 T-SQL 샘플을 수정한 다음 실행합니다. 그러나 실행 계정이 SQL Server 인스턴스에서 이미 sysadmin 서버 역할의 일부인 경우, 이 작업을 수행할 필요가 없습니다.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Windows PowerShell을 사용하여 SQL 실행 계정을 구성하려면
PowerShell 창을 열고 사용자 정보로 업데이트 한 후 다음 스크립트를 실행합니다.

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>권장 사항 우선 순위 이해
작성된 모든 권장 구성은 권장 사항의 상대적 중요도를 식별하는 가중치 값을 제공합니다. 10개의 가장 중요한 권장 사항만 표시됩니다.

### <a name="how-weights-are-calculated"></a>가중치 계산 방법
가중치는 3개의 주요 요인을 기반으로 하는 집계 값입니다.

* 식별된 문제로 인해 문제가 발생될 수 있는 *확률* 입니다. 확률이 높을수록 권장 사항에 대한 전체 점수가 커집니다.
* 문제가 발생된 경우 조직에 대한 문제의 *영향* 입니다. 영향이 높을수록 권장 사항에 대한 전체 점수가 커집니다.
* 권장 구성을 구현하는 데 필요한 *노력* 입니다. 노력이 높을수록 권장 사항에 대한 전체 점수가 작아집니다.

각 권장 사항에 대한 가중치는 각 주요 영역에 사용할 수 있는 총 점수에 대한 백분율로 표현됩니다. 예를 들어, 보안 및 규정 준수 주요 영역의 권장 사항의 점수가 5%라면, 해당 권장 사항 구현에는 전체 보안 및 규정 준수 점수에서 5%가 증가합니다.

### <a name="focus-areas"></a>주요 영역
**보안 및 규정 준수** - 이 주요 영역은 잠재적 보안 위협 및 위반, 회사 정책, 기술, 법률 및 규정 준수 요구 사항 등에 대한 권장 사항을 보여 줍니다.

**가용성 및 비즈니스 연속성** - 이 주요 영역은 서비스 가용성, 인프라 복원성 및 비즈니스 보호에 대한 권장 사항을 보여 줍니다.

**성능 및 확장성** - 이 주요 영역은 조직의 IT 인프라를 확장하고, IT 환경이 현재 성능 요구 사항을 충족하며, 변화하는 인프라 요구 사항에 대응할 수 있도록 하는 데 도움이 되는 권장 사항을 보여 줍니다.

**업그레이드, 마이그레이션 및 배포** - 기존 인프라에서 SQL Server를 업그레이드, 마이그레이션 및 배포하는 데 도움이 되는 권장 사항을 보여 주는 주요 영역입니다.

**운영 및 모니터링** - IT 운영을 간소화하며, 예방 유지 관리를 구현하고, 성능을 최대화하는 데 도움이 되는 권장 사항을 보여 주는 주요 영역입니다.

**변경 및 구성 관리** - 일상 작업을 보호하고, 인프라에 부정적인 영향을 주는 변경 사항이 있는지 확인하며, 변경 제어 절차를 설정하고, 시스템 구성을 추적 및 감사하는 데 도움이 되는 권장 사항을 보여 주는 주요 영역입니다.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>모든 주요 영역에서 100%의 점수를 목표로 해야 하나요?
그럴 필요는 없습니다. 권장 사항은 수천 번의 고객 방문에서 Microsoft 엔지니어가 얻은 지식과 경험을 기반으로 합니다. 그러나 두 서버 인프라는 동일하지 않으며 특정 권장 사항은 거의 사용자와 관련 될 수 있습니다. 예를 들어, 가상 머신이 인터넷에 노출되지 않는 경우 일부 보안 권장 사항의 관련성은 떨어질 수 있습니다. 일부 가용성 권장 사항은 우선순위가 낮은 임시 데이터 수집 및 보고를 제공하는 서비스와는 관련성이 떨어질 수 있습니다. 성숙한 비즈니스에 중요한 문제는 시작에 덜 중요할 수 있습니다. 우선하는 주요 영역을 식별하고 시간이 지남에 따라 다음 점수가 어떻게 변경되는지 확인할 수 있습니다.

모든 권장 사항에는 중요한 이유에 대한 지침이 포함됩니다. IT 서비스의 특성 및 조직의 비즈니스 요구를 고려해 볼 때, 이 가이드를 사용하여 권장 사항 구현이 사용자에 적절한지 여부를 평가해야 합니다

## <a name="use-health-check-focus-area-recommendations"></a>상태 검사 사용 초점 영역 권장 사항
Azure Monitor에서 평가 솔루션을 사용 하려면, 먼저 솔루션이 설치 되어 있어야 합니다.  을 설치한 후 SQL Health Check 타일을 사용 하 여 권장 사항의 요약을 볼 수 있습니다 합니다 **개요** Azure portal에서 Azure Monitor에 대 한 페이지입니다.

인프라에 대한 요약된 규정 준수 평가를 본 다음 세부 권장 사항을 확인합니다.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>주요 영역에 대한 권장 사항을 보고 수정 작업을 수행하려면
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에 **모니터**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **모니터**를 선택합니다.
3. 에 **Insights** 선택 메뉴의 섹션 **자세한**합니다.  
4. **개요** 페이지에서 **SQL Health Check** 타일을 클릭합니다.
5. **상태 검사** 페이지에서, 주요 영역 블레이드 중 하나에 있는 요약 정보를 검토한 다음 하나를 클릭하여 해당 주요 영역에 대한 권장 사항을 봅니다.
6. 주요 영역 페이지에서 사용자 환경에 대해 우선순위가 지정된 권장 사항을 볼 수 있습니다. 권장하는 이유에 대한 세부 정보를 보려면 **영향을 받는 개체** 아래에서 해당 권장 사항을 클릭합니다.<br><br> ![SQL Health Check 권장 사항의 이미지](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. **권장 조치**에 제안된 올바른 조치를 수행할 수 있습니다. 항목의 주소가 지정되면, 이후 평가는 수행된 권장 조치 및 늘어난 규정 준수 점수를 기록합니다. 수정된 항목은 **전달된 개체**로 나타납니다.

## <a name="ignore-recommendations"></a>권장 사항 무시
무시하려는 권장 사항이 있는 경우 Azure Monitor에서 평가 결과에 권장 사항이 표시되는 것을 방지하는 데 사용할 텍스트 파일을 만들 수 있습니다.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>무시할 권장 사항을 식별하려면
1. Azure Monitor 메뉴에서 클릭 **로그**합니다.
2. 다음 쿼리를 사용하여 사용자 환경의 컴퓨터에 대해 실패한 권장 사항을 나열합니다.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    로그 쿼리를 보여 주는 스크린샷은 다음과 같습니다.<br><br> ![실패한 권장 사항](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. 무시할 권장 사항을 선택합니다. RecommendationId 값은 다음 절차에서 사용됩니다.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt 텍스트 파일을 만들고 사용하려면
1. IgnoreRecommendations.txt라는 파일을 만듭니다.
2. Azure Monitor에서 무시할 각 권장 사항에 대한 RecommendationId를 별도의 줄에 붙여넣거나 입력한 다음, 파일을 저장하고 닫습니다.
3. Azure Monitor에서 권장 사항을 무시할 각 컴퓨터의 다음 폴더에 파일을 둡니다.
   * Microsoft Monitoring Agent(직접 또는 Operations Manager를 통해 연결됨)가 있는 컴퓨터 - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Operations Manager 관리 서버 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Operations Manager 2016 관리 서버 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>권장 사항이 무시되었는지 확인하려면
1. 예약된 다음 평가가 실행된 후(기본적으로 7일마다) 지정된 권장 사항이 평가 대시보드에 무시됨으로 표시됩니다.
2. 다음 로그 검색 쿼리를 사용하여 무시된 모든 권장 사항을 나열할 수 있습니다.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. 무시된 권장 사항을 나중에 보려면 IgnoreRecommendations.txt 파일을 제거합니다. 또는 파일에서 RecommendationID를 제거할 수도 있습니다.

## <a name="sql-health-check-solution-faq"></a>SQL Health Check 솔루션 FAQ
*상태 검사를 얼마나 자주 실행하나요?*

* 검사는 7일마다 실행됩니다.

*검사 실행 주기를 구성하는 방법이 있나요?*

* 지금은 없습니다.

*SQL Health Check 솔루션을 추가한 후 다른 서버가 발견되면 이 서버를 검사하나요?*

* 예, 발견되면 그 시간부터 7일마다 검사됩니다.

*서버를 서비스 해제하는 경우 언제 상태 검사에서 제거되나요?*

* 3주 동안 서버가 데이터를 전송하지 않은 경우 제거 됩니다.

*데이터 수집을 수행하는 프로세스의 이름은 무엇인가요?*

* AdvisorAssessment.exe

*데이터를 수집하려면 시간이 얼마나 걸리나요?*

* 서버에서의 실제 데이터 수집은 약 1시간이 걸립니다. SQL 인스턴스 또는 데이터베이스가 많은 서버에서는 더 오래 걸릴 수 있습니다.

*어떤 유형의 데이터를 수집하나요?*

* 다음 유형의 데이터를 수집합니다.
  * WMI
  * 레지스트리
  * 성능 카운터
  * SQL 동적 관리 뷰(DMV)

*데이터를 수집하는 경우 구성하는 방법이 있나요?*

* 지금은 없습니다.

*왜 실행 계정을 구성해야 하나요?*

* SQL Server의 경우, 적은 수의 SQL 쿼리가 실행됩니다. 실행하려면, SQL에 대한 서버 상태 보기 권한이 있는 실행 계정을 사용해야 합니다.  또한 WMI를 쿼리하기 위해 로컬 관리자 자격 증명이 필요합니다.

*왜 상위 10개의 권장 사항만을 표시하나요?*

* 엄청난 소비적인 작업을 나열하는 대신, 먼저 우선순위가 지정된 권장 사항 해결에 주의를 기울이는 것이 좋습니다. 권장 사항을 해결한 후에 추가 권장 사항을 사용할 수 있습니다. 자세한 목록을 참조하는 것을 선호하는 경우 Log Analytics 로그 검색을 사용하여 모든 권장 사항을 볼 수 있습니다.

*권장 사항을 무시하는 방법이 있나요?*

* 예, 위의 [권장 사항 무시](#ignore-recommendations) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [쿼리 로그](../log-query/log-query-overview.md) 에 상세 SQL Health Check 데이터 및 권장 사항의 분석 방법을 알아봅니다.

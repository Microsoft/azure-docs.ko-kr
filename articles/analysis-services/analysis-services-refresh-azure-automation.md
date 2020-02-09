---
title: Azure Automation를 사용 하 여 Azure Analysis Services 모델 새로 고침 Microsoft Docs
description: 이 문서에서는 Azure Automation를 사용 하 여 Azure Analysis Services에 대한 모델 새로 고침을 코딩 하는 방법을 설명 합니다.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572364"
---
# <a name="refresh-with-azure-automation"></a>Azure Automation을 사용하여 새로 고침

Azure Automation 및 PowerShell Runbook을 사용 하 여 Azure 분석 테이블 형식 모델에서 자동화 된 데이터 새로 고침 작업을 수행할 수 있습니다.  

이 문서의 예제에서는 [PowerShell SqlServer 모듈](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps)을 사용 합니다.

모델 새로 고침을 보여 주는 샘플 PowerShell Runbook은이 문서의 뒷부분에서 제공 됩니다.  

## <a name="authentication"></a>인증

모든 호출은 유효한 Azure Active Directory (OAuth 2) 토큰을 사용 하 여 인증 되어야 합니다.  이 문서의 예제에서는 SPN (서비스 주체)을 사용 하 여 Azure Analysis Services에 인증 합니다.

서비스 주체를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

> [!IMPORTANT]
> 다음 예에서는 Azure Analysis Services 방화벽을 사용 하지 않는 것으로 가정 합니다. 방화벽을 사용 하는 경우 요청 개시자의 공용 IP 주소를 방화벽에서 허용 목록 해야 합니다.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>PowerShell 갤러리에서 SqlServer 모듈을 설치 합니다.

1. Azure Automation 계정에서 **모듈**, **갤러리 찾아보기**를 차례로 클릭 합니다.

2. 검색 창에서 **SqlServer**를 검색 합니다.

    ![모듈 검색](./media/analysis-services-refresh-azure-automation/1.png)

3. SqlServer를 선택 하 고 **가져오기**를 클릭 합니다.
 
    ![모듈 가져오기](./media/analysis-services-refresh-azure-automation/2.png)

4. **확인**을 클릭합니다.
 
### <a name="create-a-service-principal-spn"></a>SPN (서비스 사용자) 만들기

서비스 주체를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)를 참조 하세요.

### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services에서 사용 권한 구성
 
사용자가 만드는 서비스 주체에 서버에 대한 서버 관리자 권한이 있어야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

## <a name="design-the-azure-automation-runbook"></a>Azure Automation Runbook 디자인

1. Automation 계정에서 서비스 주체를 안전 하 게 저장 하는 데 사용 되는 **자격 증명** 리소스를 만듭니다.

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/6.png)

2. 자격 증명에 대한 세부 정보를 입력 합니다.  **사용자 이름**에 대해 **spn ClientId**를 입력 하 고 **암호**에 **spn 암호**를 입력 합니다.

    ![자격 증명 만들기](./media/analysis-services-refresh-azure-automation/7.png)

3. Automation Runbook 가져오기

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/8.png)

4. **Refresh-Model** 파일을 찾아 **이름** 및 **설명을**입력 한 다음 **만들기**를 클릭 합니다.

    ![Runbook 가져오기](./media/analysis-services-refresh-azure-automation/9.png)

5. Runbook이 만들어지면 자동으로 편집 모드로 전환 됩니다.  **게시**를 선택합니다.

    ![Runbook 게시](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > 이전에 만든 자격 증명 리소스는 **Get AutomationPSCredential** 명령을 사용 하 여 runbook에 의해 검색 됩니다.  그런 다음이 명령은 Azure Analysis Services에 대한 인증을 수행 하기 위해 **ProcessASADatabase** PowerShell 명령에 전달 됩니다.

6. **시작**을 클릭 하 여 runbook을 테스트 합니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/11.png)

7. **DATABASENAME**, **ANALYSISSERVER**및 **refreshtype** 매개 변수를 입력 한 다음 **확인**을 클릭 합니다. Runbook을 수동으로 실행 하는 경우 **WEBHOOKDATA** 매개 변수는 필요 하지 않습니다.

    ![Runbook 시작](./media/analysis-services-refresh-azure-automation/12.png)

Runbook이 성공적으로 실행 되 면 다음과 같은 출력이 표시 됩니다.

![성공한 실행](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>자체 포함 Azure Automation Runbook 사용

예약 된 시간에 Azure Analysis Services 모델 새로 고침을 트리거하기 위해 Runbook을 구성할 수 있습니다.

다음과 같이 구성할 수 있습니다.

1. Automation Runbook **에서 일정을 클릭 한**다음 **일정을 추가**합니다.
 
    ![일정 만들기](./media/analysis-services-refresh-azure-automation/14.png)

2. **일정** 을 클릭 하 > **새 일정을 만든**다음 세부 정보를 입력 합니다.

    ![일정 구성](./media/analysis-services-refresh-azure-automation/15.png)

3. **만들기**를 클릭합니다.

4. 일정에 대한 매개 변수를 입력 합니다. Runbook이 트리거될 때마다 사용 됩니다. **WEBHOOKDATA** 매개 변수는 일정을 통해 실행할 때 비워 두어야 합니다.

    ![매개 변수 구성](./media/analysis-services-refresh-azure-automation/16.png)

5. **확인**을 클릭합니다.

## <a name="consume-with-data-factory"></a>Data Factory 사용

Azure Data Factory를 사용 하 여 runbook을 사용 하려면 먼저 runbook에 대한 **Webhook** 을 만듭니다. 웹 **후크는 Azure Data Factory 웹 작업을 통해** 호출할 수 있는 URL을 제공 합니다.

> [!IMPORTANT]
> **Webhook**를 만들려면 Runbook의 상태를 **게시**해야 합니다.

1. Automation Runbook에서 Webhook를 클릭 한 다음 **웹 후크** **추가**를 클릭 합니다.

   ![Webhook 추가](./media/analysis-services-refresh-azure-automation/17.png)

2. Webhook에 이름 및 만료를 제공 합니다.  이름은 Automation Runbook 내의 Webhook만 식별 하며 URL의 일부를 구성 하지 않습니다.

   >[!CAUTION]
   >종료 된 후에는 다시 가져올 수 없기 때문에 마법사를 닫기 전에 URL을 복사 해야 합니다.
    
   ![Webhook 구성](./media/analysis-services-refresh-azure-automation/18.png)

    Webhook에 대한 매개 변수는 비워 둘 수 있습니다.  Azure Data Factory 웹 작업을 구성 하는 경우 웹 호출의 본문에 매개 변수를 전달할 수 있습니다.

3. Data Factory에서 **웹 활동** 을 구성 합니다.

### <a name="example"></a>예제

   ![웹 작업 예제](./media/analysis-services-refresh-azure-automation/19.png)

**Url** 은 Webhook에서 만든 url입니다.

**본문** 은 다음 속성을 포함 하는 JSON 문서입니다.


|속성  |값  |
|---------|---------|
|**AnalysisServicesDatabase**     |Azure Analysis Services 데이터베이스의 이름입니다. <br/> 예: Adventureworksdb.msi         |
|**AnalysisServicesServer**     |Azure Analysis Services 서버 이름입니다. <br/> 예: https: \//westus/servers/myserver/모델/AdventureWorks/         |
|**DatabaseRefreshType**     |수행할 새로 고침의 유형입니다. <br/> 예: Full         |

예제 JSON 본문:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

이러한 매개 변수는 runbook PowerShell 스크립트에 정의 되어 있습니다.  웹 활동이 실행 되 면 전달 된 JSON 페이로드가 WEBHOOKDATA 됩니다.

이는 deserialize 되어 PowerShell 매개 변수로 저장 되며,이는 호출-ProcesASDatabase PowerShell 명령에 사용 됩니다.

![Deserialize 된 Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Azure Analysis Services에서 Hybrid Worker 사용

고정 공용 IP 주소를 사용 하는 Azure 가상 머신은 Azure Automation Hybrid Worker 사용할 수 있습니다.  그런 다음이 공용 IP 주소를 Azure Analysis Services 방화벽에 추가할 수 있습니다.

> [!IMPORTANT]
> 가상 컴퓨터 공용 IP 주소가 정적으로 구성 되어 있는지 확인 하십시오.
>
>Azure Automation Hybrid Worker를 구성 하는 방법에 대한 자세한 내용은 [Hybrid Runbook Worker를 사용 하 여 데이터 센터 또는 클라우드에서 리소스 자동화](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker)를 참조 하세요.

Hybrid Worker 구성 되 면 [Data Factory 사용](#consume-with-data-factory)섹션에 설명 된 대로 Webhook를 만듭니다.  여기에서 유일한 차이점은 웹 후크를 구성할 때 > **에서 실행** **Hybrid Worker** 옵션을 선택 하는 것입니다.

Hybrid Worker를 사용 하는 webhook 예:

![Webhook Hybrid Worker 예](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>샘플 PowerShell Runbook

다음 코드 조각은 PowerShell Runbook을 사용 하 여 Azure Analysis Services 모델 새로 고침을 수행 하는 방법의 예입니다.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>다음 단계

[샘플](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)

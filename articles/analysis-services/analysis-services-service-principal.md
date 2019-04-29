---
title: 서비스 사용자를 사용하여 Azure Analysis Services 작업 자동화 | Microsoft Docs
description: Azure Analysis Services 작업을 자동화하기 위해 서비스 주체를 만드는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a440494b183d18c1d888b5d39836eb4317190d02
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764341"
---
# <a name="automation-with-service-principals"></a>서비스 사용자를 사용한 자동화

서비스 사용자는 무인 리소스 및 서비스 수준 작업을 수행하도록 테넌트 내에서 만드는 Azure Active Directory 애플리케이션 리소스입니다. 애플리케이션 ID 및 암호 또는 인증서가 있는 고유한 형식의 *사용자 ID*입니다. 서비스 사용자는 할당된 역할 및 사용 권한으로 정의된 작업을 수행하는 데 필요한 권한만 갖습니다. 

Analysis Services에서 서비스 사용자는 일반적인 작업을 자동화하는 Azure Automation, PowerShell 무인 모드, 사용자 지정 클라이언트 애플리케이션 및 웹앱에 사용됩니다. 예를 들어 서버 프로비전, 모델 배포, 데이터 새로 고침, 규모 확대/축소 및 일시 중지/다시 시작은 모두 서비스 사용자를 사용하여 자동화될 수 있습니다. 일반 Azure AD UPN 계정과 매우 유사하게 권한은 역할 멤버 자격을 통해 서비스 사용자에게 할당됩니다.

## <a name="create-service-principals"></a>서비스 주체 만들기
 
서비스 사용자는 Azure Portal에서 또는 PowerShell을 사용하여 만들 수 있습니다. 자세한 내용은 다음을 참조하세요.

[서비스 사용자 만들기 - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[서비스 사용자 만들기 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Azure Automation에서 자격 증명 및 인증서 자산 저장

서비스 사용자 자격 증명 및 인증서는 Runbook 작업에 대해 Azure Automation에서 안전하게 저장될 수 있습니다. 자세한 내용은 다음을 참조하세요.

[Azure Automation의 자격 증명 자산](../automation/automation-credentials.md)   
[Azure Automation의 인증서 자산](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>서버 관리자 역할에 서비스 사용자 추가

Analysis Services 서버 관리 작업에 서비스 사용자를 사용하려면 먼저 서버 관리자 역할에 추가해야 합니다. 자세히 알아보려면 [서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)를 참조하세요.

## <a name="service-principals-in-connection-strings"></a>연결 문자열의 서비스 사용자

UPN과 거의 동일하게 서비스 사용자 appID 및 암호 또는 인증서를 연결 문자열에서 사용할 수 있습니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="a-nameazmodule-using-azanalysisservices-module"></a><a name="azmodule" />Az.AnalysisServices 모듈을 사용 하 여

리소스 관리 작업에 대 한 서비스 주체를 사용 하는 경우는 [Az.AnalysisServices](/powershell/module/az.analysisservices) 모듈을 사용 하 여 `Connect-AzAccount` cmdlet. 

다음 예제에서는 appID 및 암호를 읽기 전용 복제본에 동기화에 대 한 제어 평면 작업을 수행 하 고 업그레이드/확장에 사용 됩니다.

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>SQLServer 모듈을 사용 하 여

다음 예제에서는 appID 및 암호가 모델 데이터베이스 새로 고침 작업을 수행하는 데 사용됩니다.

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO 및 ADOMD 

클라이언트 애플리케이션 및 웹앱에 연결할 때 NuGet의 [AMO 및 ADOMD 클라이언트 라이브러리](analysis-services-data-providers.md) 버전 15.0.2 이상 설치 가능한 패키지는 다음 구문 `app:AppID` 및 암호 또는 `cert:thumbprint`를 사용하여 연결 문자열에서 서비스 사용자를 지원합니다. 

다음 예제에서는 `appID` 및 `password`가 모델 데이터베이스 새로 고침 작업을 수행하는 데 사용됩니다.

```C#
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>다음 단계
[Azure PowerShell로 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[서버 관리자 역할에 서비스 사용자 추가](analysis-services-addservprinc-admins.md)   

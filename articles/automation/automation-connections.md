---
title: Azure Automation의 연결 자산
description: Azure Automation의 연결 자산은 외부 서비스 또는 runbook이나 DSC 구성의 애플리케이션을 연결하는데 필요한 정보를 포함합니다. 이 문서에서는 연결에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 연결을 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7dccc4a396d4cf8af1062057c4c3ce6efe978ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074267"
---
# <a name="connection-assets-in-azure-automation"></a>Azure Automation의 연결 자산

Automation 연결 자산은 외부 서비스 또는 runbook의 애플리케이션이나 DSC 구성 연결에 필요한 정보를 포함합니다. 여기에는 URL 또는 포트와 같은 연결 정보 외에 사용자 이름 및 암호와 같은 인증에 필요한 정보가 포함될 수 있습니다. 연결 값은 여러 변수를 만드는 대신, 특정 애플리케이션에 연결하기 위한 모든 속성을 하나의 자산에 유지합니다. 사용자는 한 위치의 연결에 대한 값을 편집할 수 있고 단일 매개 변수에서 연결 이름을 runbook 이나 DSC구성에 전달할 수 있습니다. 연결에 대한 속성은 **Get-AutomationConnection** 활동을 사용하여 runbook 또는 DSC 구성에 액세스할 수 있습니다. 

연결을 만들 때 *연결 형식*을 지정해야 합니다. 연결 형식은 속성 집합을 정의하는 템플릿입니다. 연결은 해당 연결 형식에 정의된 각 속성의 값을 정의합니다. 연결 형식은 통합 모듈의 Azure Automation에 추가되어 있거나 통합 모듈에 연결 형식이 포함되어 있고 이 유형을 Automation 계정으로 가져올 경우 [Azure Automation API](/previous-versions/azure/reference/mt163818(v=azure.100))를 사용하여 만들어집니다. 그렇지 않으면 Automation 연결 형식을 지정하기 위해 메타데이터 파일을 만들어야 합니다.  이와 관련된 자세한 내용은 [통합 모듈](automation-integration-modules.md)을 참조하세요.  

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 시스템에서 관리하는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다. Azure Automation에서 이 프로세스를 관리합니다.

## <a name="connection-types"></a>연결 형식

Azure Automation에서는 세 가지 유형의 기본 제공 연결을 사용할 수 있습니다.

* **Azure** - 이 연결을 사용하여 클래식 리소스를 관리할 수 있습니다.
* **AzureClassicCertificate** - 이 연결은 **AzureClassicRunAs** 계정에 사용됩니다.
* **AzureServicePrincipal** - 이 연결은 **AzureRunAs** 계정에 사용됩니다.

대부분의 경우 연결 리소스는 [실행 계정](manage-runas-account.md)을 만들 때 생성되므로 별도로 생성할 필요가 없습니다.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet

다음 테이블의 cmdlet은 Windows PowerShell을 사용하여 Automation 연결을 만들고 관리하는 데 사용됩니다. Automation runbook과 DSC 구성에 사용할 수 있는 [Azure PowerShell 모듈](/powershell/azure/overview) 의 일부로 전송됩니다.

|Cmdlet|설명|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|연결을 검색합니다. 연결의 필드 값이 있는 해시 테이블을 포함합니다.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|새 연결을 만듭니다.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|기존 연결을 제거합니다.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|기존 연결의 특정 필드 값을 설정합니다.|

## <a name="activities"></a>활동

다음 표의 활동은 runbook 또는 DSC 구성의 연결에 액세스하는데 사용됩니다.

|활동|설명|
|---|---|
|[Get-AutomationConnection](/powershell/module/servicemanagement/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|사용할 연결을 가져옵니다. 연결의 속성이 있는 해시 테이블을 반환합니다.|

>[!NOTE] 
>**Get-AutomationConnection**의 -Name 매개 변수에 변수를 사용하면 안 됩니다. 변수를 사용하는 경우 Runbook 또는 DSC 구성과 설계 시의 연결 자산 간의 종속성을 검색하기가 어려워질 수 있기 때문입니다.

 
## <a name="python2-functions"></a>Python2 함수 
다음 표의 함수는 Python2 Runbook의 연결에 액세스하는 데 사용됩니다. 

| 함수 | 설명 | 
|:---|:---| 
| automationassets.get_automation_connection | 연결을 검색합니다. 연결의 속성이 있는 사전을 반환합니다. | 

> [!NOTE] 
> 자산 함수에 액세스하려면 Python Runbook 맨 위에서 "automationassets" 모듈을 가져와야 합니다.

## <a name="creating-a-new-connection"></a>새 연결 만들기

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Azure 포털을 사용하여 새 연결을 만들려면

1. 자동화 계정에서 **자산** 파트를 클릭하여 **자산** 블레이드를 엽니다.
2. **연결** 파트를 클릭하여 **연결** 블레이드를 엽니다.
3. 블레이드의 위쪽에서 **연결 추가** 를 클릭합니다.
4. **형식** 드롭다운에서 만들려는 연결 형식을 선택합니다. 양식에 해당 특정 형식에 대한 속성이 표시됩니다.
5. 양식을 완료하고 **만들기** 를 클릭하여 새 연결을 저장합니다.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 연결을 만들려면

[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection) cmdlet을 사용하여 Windows PowerShell과의 새 연결을 만듭니다. 이 cmdlet에는 연결 형식에 정의된 각 특성의 값을 정의하는 **해시 테이블** 이 필요한 [ConnectionFieldValues](https://technet.microsoft.com/library/hh847780.aspx) 라는 매개 변수가 있습니다.

Automation [실행 계정](automation-sec-configure-azure-runas-account.md)에 익숙하여 서비스 주체를 통해 Runbook을 인증하는 경우 포털에서 실행 계정을 만드는 대신 제공되는 PowerShell 스크립트에서 다음 샘플 명령을 사용하여 새 연결 자산을 만듭니다.  

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Automation 계정을 만들 때 기본적으로 **AzureServicePrincipal** 연결 형식과 함께 여러 전역 모듈이 자동으로 포함되어 **AzureRunAsConnection** 연결 자산을 만들 수 있기 때문에 스크립트를 사용하여 연결 자산을 만들 수 있습니다.  새 연결 자산을 만들어 다른 인증 방법으로 서비스 또는 애플리케이션에 연결하려는 경우 아직 연결 형식이 Automation 계정에 정의되어 있지 않기 때문에 실패하게 된다는 것을 명심해야 합니다.  [PowerShell 갤러리](https://www.powershellgallery.com)에서 사용자 지정 또는 모듈에 대해 고유한 연결 형식을 만드는 방법에 대한 자세한 내용은 [통합 모듈](automation-integration-modules.md)을 참조하세요.
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>runbook 또는 DSC 구성에서 연결 사용하기

**Get-AutomationConnection** cmdlet를 사용하여 runbook 또는 DSC 구성의 연결을 검색합니다.  [Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection) 활동은 사용할 수 없습니다.  이 활동은 연결의 여러 필드값을 검색하고 runbook 또는 DSC 구성의 적절한 명령과 함께 사용될 수 있는 [해시 테이블](https://go.microsoft.com/fwlink/?LinkID=324844) 로 반환합니다.

### <a name="textual-runbook-sample"></a>텍스트 Runbook 샘플

다음 샘플 명령에서는 앞에서 언급한 실행 계정을 사용하여 runbook에서 Azure Resource Manager 리소스를 인증하는 방법을 보여 줍니다.  여기서는 자격 증명이 아닌 인증서 기반 서비스 주체를 참조하는 실행 계정을 나타내는 연결 자산을 사용합니다.  

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection 
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!IMPORTANT]
> **Add-AzureRmAccount**는 이제 **Connect-AzureRMAccount**에 대한 별칭입니다. 라이브러리를 항목을 검색할 때 **Connect-AzureRMAccount**가 표시되지 않는 경우 **Add-AzureRmAccount**를 사용하거나 Automation 계정에서 모듈을 업데이트할 수 있습니다.

### <a name="graphical-runbook-samples"></a>그래픽 Runbook 샘플

그래픽 편집기의 라이브러리 창에서 연결을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택하여 **Get-AutomationConnection**를 그래픽 Runbook에 추가합니다.

![캔버스에 추가](media/automation-connections/connection-add-canvas.png)

다음 그림에서는 그래픽 Runbook에서 연결을 사용하는 예제를 보여 줍니다.  이는 텍스트 runbook과 함께 실행 계정을 통해 인증하기 위해 위에서 보여 준 예제와 동일합니다.  이 예제에서는 인증을 위해 연결 개체를 사용하는 **실행 계정 연결 가져오기** 활동에 대해 **상수 값** 데이터 집합을 사용합니다.  ServicePrincipalCertificate 매개 변수 집합에는 단일 개체가 필요하기 때문에 여기서는 [파이프라인 링크](automation-graphical-authoring-intro.md#links-and-workflow)가 사용됩니다.

![연결 가져오기](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Python2 Runbook 샘플
다음 예제에서는 Python2 Runbook에서 다음 계정으로 실행 연결을 사용하여 인증하는 방법을 보여 줍니다.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
  """ Returns credentials to authenticate against Azure resoruce manager """
  from OpenSSL import crypto
  from msrestazure import azure_active_directory
  import adal

  # Get the Azure Automation Run As service principal certificate
  cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
  pks12_cert = crypto.load_pkcs12(cert)
  pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

  # Get Run As connection information for the Azure Automation service principal
  application_id = runas_connection["ApplicationId"]
  thumbprint = runas_connection["CertificateThumbprint"]
  tenant_id = runas_connection["TenantId"]

  # Authenticate with service principal certificate
  resource = "https://management.core.windows.net/"
  authority_url = ("https://login.microsoftonline.com/" + tenant_id)
  context = adal.AuthenticationContext(authority_url)
  return azure_active_directory.AdalAuthentication(
    lambda: context.acquire_token_with_client_certificate(
      resource,
      application_id,
      pem_pkey,
      thumbprint)
  )

# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>다음 단계

- [그래픽 작성의 링크](automation-graphical-authoring-intro.md#links-and-workflow)를 검토하여 runbook의 논리 흐름을 지시하고 제어하는 방법을 이해합니다.  

- Azure Automation에서 PowerShell 모듈을 사용하는 방법 및 Azure Automation 내에서 통합 모듈로 작동하도록 고유한 PowerShell 모듈을 만드는 모범 사례에 대한 자세한 내용은 [통합 모듈](automation-integration-modules.md)을 참조하세요.  


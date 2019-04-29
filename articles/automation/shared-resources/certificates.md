---
title: Azure Automation의 인증서 자산
description: 인증서는 runbook 또는 DSC 구성이 Azure 및 타사 리소스에 대해 인증 하 여 액세스할 수 있도록 Azure Automation에 안전 하 게 합니다.  이 문서에서는 인증서에 대해 자세히 알아보고 텍스트 작성과 그래픽 작성 모두에서 인증서를 사용하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216076"
---
# <a name="certificate-assets-in-azure-automation"></a>Azure Automation의 인증서 자산

인증서는 안전 하 게 저장 Azure Automation에서 runbook 또는 DSC 구성을 사용 하 여 액세스할 수 있도록 합니다 **Get-azurermautomationcertificate** Azure Resource Manager 리소스에 대 한 작업입니다. 이 기능을 사용하여 인증을 위해 인증서를 사용하는 Runbook 및 DSC 구성을 만들거나 Azure 또는 타사 리소스에 추가할 수 있습니다.

>[!NOTE]
>Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 각 Automation 계정에 대해 생성되는 고유 키를 사용하여 암호화되고 Azure Automation에 저장됩니다. 이 키는 시스템에서 관리하는 Key Vault에 저장됩니다. 보안 자산을 저장하기 전에 Key Vault에서 키가 로드된 다음, 자산을 암호화하는 데 사용됩니다. Azure Automation에서 이 프로세스를 관리합니다.

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet

AzureRM에 대해 다음 표의 cmdlet은 Windows PowerShell을 사용하여 자동화 자격 증명 자산을 만들고 관리하는 데 사용됩니다. 일부분으로 제공 합니다 [AzureRM.Automation 모듈](/powershell/azure/overview), Automation runbook과 DSC 구성에 사용할 수 있는 합니다.

|Cmdlet|설명|
|:---|:---|
|[Get AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Runbook 또는 DSC 구성에 사용할 인증서 정보를 검색합니다. Get-AutomationCertificate 활동에서는 인증서 자체만 검색할 수 있습니다.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Azure Automation으로 새 인증서를 만듭니다.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Azure Automation에서 인증서를 제거합니다.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|인증서 파일 업로드 및 .pfx 암호 설정을 포함하여 기존 인증서에 대한 속성을 설정합니다.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|지정된 클라우드 서비스에 대한 서비스 인증서를 업로드합니다.|

## <a name="activities"></a>활동

다음 테이블의 활동은 runbook 또는 DSC 구성의 인증서에 액세스하는 데 사용됩니다.

| 활동 | 설명 |
|:---|:---|
|Get-AutomationCertificate|Runbook 또는 DSC 구성에 사용할 인증서를 가져옵니다. [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 개체를 반환합니다.|

> [!NOTE] 
> runbook 또는 DSC 구성에서 **Get-AutomationCertificate**의 –Name 매개 변수에 변수를 사용하면 안 됩니다. 디자인 시 Runbook 또는 DSC 구성과 Automation 변수를 사이의 종속성이 복잡해질 수 있기 때문입니다.

## <a name="python2-functions"></a>Python2 함수

다음 테이블의 함수는 Python2 Runbook의 인증서에 액세스하는 데 사용됩니다.

| 함수 | 설명 |
|:---|:---|
| automationassets.get_automation_certificate | 인증서 자산에 대한 정보를 검색합니다. |

> [!NOTE]
> 자산 함수에 액세스하려면 Python Runbook을 시작할 때 **automationassets** 모듈을 가져와야 합니다.

## <a name="creating-a-new-certificate"></a>새 인증서 만들기

새 인증서를 만들 때 .cer 또는 .pfx 파일을 Azure Automation에 업로드합니다. 인증서를 내보내기 가능한 것으로 표시한 경우 Azure Automation 인증서 저장소 외부로 전송할 수 있습니다. 내보낼 수 없으면 다음만 사용할 수 runbook 또는 DSC 구성 내에서 서명 합니다. Azure Automation을 사용하려면 인증서의 공급자가 **Microsoft Enhanced RSA and AES Cryptographic Provider**여야 합니다.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Azure 포털을 사용하여 새 인증서를 만들려면

1. Automation 계정에서 클릭 합니다 **자산** 를 열려면 타일을 **자산** 페이지입니다.
2. 클릭 합니다 **인증서** 열려는 타일 합니다 **인증서** 페이지입니다.
3. 클릭 **인증서 추가** 페이지의 맨 위에 있는 합니다.
4. **이름** 상자에 인증서 이름을 입력합니다.
5. .cer 또는 .pfx 파일을 찾아보려면 **인증서 파일 업로드** 아래에서 **파일 선택**을 클릭합니다. .Pfx 파일을 선택 하면 암호 및 내보낼 수 있는지 여부를 지정 합니다.
6. **만들기** 를 클릭하여 새 인증서 자산을 저장합니다.

### <a name="to-create-a-new-certificate-with-powershell"></a>PowerShell을 사용 하 여 새 인증서를 만들려면

다음 예제에서는 새 Automation 인증서를 만들고 내보내기 가능한 것으로 표시하는 방법을 보여 줍니다. 이 예제에서는 기존 .pfx 파일을 가져옵니다.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Resource Manager 템플릿을 사용 하 여 새 인증서 만들기

다음 예제에서는 PowerShell 통해 Resource Manager 템플릿을 사용 하 여 Automation 계정에 인증서를 배포 하는 방법에 설명 합니다.

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>인증서 사용

인증서를 사용하려면 **Get-AutomationCertificate** 활동을 사용합니다. 사용할 수 없습니다는 [Get-azurermautomationcertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet은 인증서 자체가 아닌 인증서 자산에 대 한 정보를 반환 하기 때문입니다.

### <a name="textual-runbook-sample"></a>텍스트 Runbook 샘플

다음 샘플 코드에서는 Runbook에서 클라우드 서비스에 인증서를 추가하는 방법을 보여 줍니다. 이 샘플에서는 암호화된 자동화 변수에서 암호를 검색합니다.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>그래픽 Runbook 샘플

추가한를 **Get-automationcertificate** 라이브러리 창에서 인증서를 마우스 오른쪽 단추로 클릭 하 고 선택 하 여 그래픽 runbook **캔버스에 추가**합니다.

![캔버스에 인증서 추가](../media/certificates/automation-certificate-add-to-canvas.png)

다음 그림에서는 그래픽 Runbook에서 인증서를 사용하는 예제를 보여 줍니다. 이 텍스트 runbook에서 클라우드 서비스에 인증서를 추가 하는 방법을 보여 주는 앞의 예제와 같습니다.

![그래픽 작성 예제](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 샘플

다음 샘플은 Python2 Runbook의 인증서에 액세스하는 방법을 보여 줍니다.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>다음 단계

- Runbook이 수행하도록 설계된 활동의 논리적 흐름을 제어하도록 링크로 작업하는 방법에 대한 자세한 정보는 [그래픽 작성의 링크](../automation-graphical-authoring-intro.md#links-and-workflow)를 참조하세요. 

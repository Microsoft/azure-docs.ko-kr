---
title: Azure Automation Hybrid Runbook Worker에서 Runbook 실행
description: 이 문서에서는 Hybrid Runbook Worker 역할이 있는 로컬 데이터 센터 또는 클라우드 공급자의 컴퓨터에서 Runbook을 실행하는 방법에 대한 정보를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fab886de55cc524390093f7e7913c79f7af3fe78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738505"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 실행

Azure Automation에서 실행되는 Runbook과 Hybrid Runbook Worker에서 실행되는 Runbook은 구조상 차이점이 없습니다. 각 항목에서 사용하는 Runbook은 크게 다를 수 있습니다. Hybrid Runbook Worker를 대상으로 하는 Runbook은 일반적으로 로컬 컴퓨터 자체에서 리소스를 관리하거나 배포된 로컬 환경의 리소스에 맞게 리소스를 관리하기 때문에 이 차이점이 나타납니다. Azure Automation의 Runbook은 일반적으로 Azure 클라우드에서 리소스를 관리합니다.

Hybrid Runbook Worker에서 실행할 Runbook을 작성하는 경우, 하이브리드 작업자를 호스팅하는 컴퓨터 내에서 Runbook을 편집하고 테스트해야 합니다. 호스트 컴퓨터에는 로컬 리소스를 관리하고 액세스하는 데 필요한 모든 PowerShell 모듈과 네트워크 액세스가 있습니다. Hybrid Worker 머신에서 Runbook이 테스트되면 Hybrid Worker에서 실행하는 데 사용할 수 있는 Azure Automation 환경에 업로드할 수 있습니다. Windows용 로컬 시스템 계정 또는 Linux용 특수 사용자 계정 `nxautomation`으로 실행되는 작업을 아는 것이 중요합니다. 이 동작은 Hybrid Runbook Worker의 Runbook을 작성할 때 미묘한 차이점을 추가할 수 있습니다. 자신의 Runbook을 작성하는 경우 이러한 변경 내용을 검토해야 합니다.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 시작

[Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md) 에 Runbook을 시작하는 여러 가지 방법이 설명되어 있습니다. Hybrid Runbook Worker는 Hybrid Runbook Worker 그룹의 이름을 지정할 수 있는 **RunOn** 옵션을 추가합니다. 그룹을 지정하면 해당 그룹의 작업자 중 하나가 Runbook을 검색하고 실행합니다. 이 옵션을 지정하지 않으면 평소처럼 Azure Automation에서 Runbook이 실행됩니다.

Azure Portal에서 Runbook을 시작하면 **Azure** 또는 **Hybrid Worker**를 선택할 수 있는 **실행 대상** 옵션이 표시됩니다. **Hybrid Worker**를 선택한 경우 드롭다운에서 그룹을 선택할 수 있습니다.

**RunOn** 매개 변수 사용. Windows PowerShell에서 다음 명령을 사용하여 MyHybridGroup이라는 Hybrid Runbook Worker 그룹에서 Test-Runbook이라는 Runbook을 시작할 수 있습니다.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> **RunOn** 매개 변수는 Microsoft Azure PowerShell 버전 0.9.1에서 **Start-AzureAutomationRunbook** cmdlet에 추가되었습니다. 이전 버전을 설치한 경우 [최신 버전을 다운로드](https://azure.microsoft.com/downloads/)해야 합니다. PowerShell에서 Runbook을 시작하는 워크스테이션에만 이 버전을 설치하면 됩니다. 작업자 컴퓨터에서 Runbook을 시작하려는 경우가 아니라면 해당 컴퓨터에 설치할 필요는 없습니다.

## <a name="runbook-permissions"></a>Runbook 사용 권한

Hybrid Runbook Worker에서 실행하는 Runbook은 Azure에 없는 리소스에 액세스하기 때문에 일반적으로 Azure 리소스를 인증하는 Runbook에 사용되는 것과 동일한 방법을 사용할 수 없습니다. Runbook은 로컬 리소스에 고유한 인증을 제공하거나 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
)를 사용하여 인증을 구성할 수 있습니다. 실행 계정을 지정하여 모든 Runbook에 대한 사용자 컨텍스트를 제공할 수도 있습니다.

### <a name="runbook-authentication"></a>Runbook 인증

기본적으로 Runbook은 온-프레미스 컴퓨터에서 Windows용 로컬 시스템 계정 및 Linux용 특수 사용자 계정 `nxautomation`의 컨텍스트에서 실행되므로 액세스하는 리소스에 자체 인증을 제공해야 합니다.

여러 리소스를 인증할 수 있도록 자격 증명을 지정할 수 있는 cmdlet과 함께 Runbook의 [자격 증명](automation-credentials.md) 및 [인증서](automation-certificates.md) 자산을 사용할 수 있습니다. 다음 예제에서는 컴퓨터를 다시 시작하는 Runbook의 일부를 보여 줍니다. 이 예제에서는 자격 증명 자산에서 자격 증명을 검색하고 변수 자산에서 컴퓨터 이름을 검색한 다음 Restart-Computer cmdlet에서 이러한 값을 사용합니다.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[PSCredential 일반 매개 변수](/powershell/module/psworkflow/about/about_workflowcommonparameters)에 지정된 자격 증명을 사용하여 다른 컴퓨터에서 코드 블록을 실행할 수 있는 [InlineScript](automation-powershell-workflow.md#inlinescript)를 사용할 수도 있습니다.

### <a name="runas-account"></a>실행 계정

기본적으로 Hybrid Runbook Worker는 Windows용 로컬 시스템 및 Linux용 특수 사용자 계정 `nxautomation`을 사용하여 Runbook을 실행합니다. Runbook이 로컬 리소스에 고유한 인증을 제공하는 대신 Hybrid worker 그룹에 **실행** 계정을 지정할 수 있습니다. 로컬 리소스에 액세스 권한이 있는 [자격 증명 자산](automation-credentials.md)을 지정하고 그룹의 Hybrid Runbook Worker에서 실행될 때 모든 Runbook이 이러한 자격 증명으로 실행됩니다.

자격 증명에 대한 사용자 이름은 다음 서식 중 하나여야 합니다.

* domain\username
* username@domain
* 사용자 이름(온-프레미스 컴퓨터에 로컬인 계정용)

Hybrid Worker 그룹에 실행 계정을 지정하려면 다음 절차를 사용합니다.

1. 로컬 리소스에 대한 액세스로 [자격 증명 자산](automation-credentials.md)을 만듭니다.
2. Azure Portal에서 Automation 계정을 엽니다.
3. **Hybrid Worker 그룹** 타일을 선택한 다음 그룹을 선택합니다.
4. **모든 설정** 및 **Hybrid Worker 그룹 설정**을 차례로 선택합니다.
5. **다음 계정으로 실행**을 **기본**에서 **사용자 지정**으로 변경합니다.
6. 자격 증명을 선택하고 **저장**을 클릭합니다.

### <a name="managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID

Azure 가상 머신에서 실행되는 Hybrid Runbook Worker는 Azure 리소스에 대한 관리 ID를 사용하여 Azure 리소스를 인증할 수 있습니다. 실행 계정 대신 Azure 리소스에 대한 관리 ID를 사용하면 여러 가지 이점이 있습니다.

* 실행 인증서를 내보낸 후 Hybrid Runbook Worker로 가져올 필요가 없습니다.
* 실행 계정에서 사용하는 인증서를 갱신할 필요가 없습니다.
* Runbook 코드의 실행 연결 개체를 처리할 필요가 없습니다.

Hybrid Runbook Worker에서 Azure 리소스에 대한 관리 ID를 사용하려면 다음 단계를 완료해야 합니다.

1. Azure VM 만들기
2. [VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [VM에 Resource Manager의 리소스 그룹 액세스 권한 부여](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [VM의 시스템 할당 관리 ID를 사용하여 액세스 토큰을 가져오기](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. 가상 머신에 [Windows Hybrid Runbook Worker를 설치](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker)합니다.

이전 단계가 완료되면 Runbook에서 `Connect-AzureRmAccount -Identity`를 사용하여 Azure 리소스를 인증할 수 있습니다. 이 구성을 사용하면 실행 계정을 사용하고 실행 계정의 인증서를 관리해야 하는 필요성이 감소합니다.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Automation 실행 계정

Azure에서 리소스를 배포하는 자동화된 빌드 프로세스의 일부로 배포 시퀀스의 작업 또는 단계 집합을 지원하기 위해 온-프레미스 시스템에 대한 액세스가 필요할 수 있습니다. 실행 계정을 사용하여 Azure에 대해 인증을 지원하려면 실행 계정 인증서를 설치해야 합니다.

**Export-RunAsCertificateToHybridWorker** PowerShell Runbook은 Azure Automation 계정에서 실행 인증서를 내보내고 다운로드하여 동일한 계정에 연결된 Hybrid Worker의 로컬 머신 인증서 저장소로 가져옵니다. 이 단계가 완료되면 작업자는 실행 계정을 사용하여 Azure를 성공적으로 인증할 수 있는지 확인합니다.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount**는 이제 **Connect-AzureRMAccount**에 대한 별칭입니다. 라이브러리를 항목을 검색할 때 **Connect-AzureRMAccount**가 표시되지 않는 경우 **Add-AzureRmAccount**를 사용하거나 Automation 계정에서 모듈을 업데이트할 수 있습니다.

*Export-RunAsCertificateToHybridWorker* Runbook을 `.ps1` 확장명의 컴퓨터에 저장합니다. Runbook을 Automation 계정으로 가져오고 편집하여 `$Password` 변수 값을 사용자 고유의 암호로 변경합니다. Runbook을 게시한 후 실행합니다. 실행 계정을 사용하여 Runbook을 실행하고 인증할 Hybrid Worker 그룹을 대상으로 지정합니다. 이 작업 스트림은 인증서를 로컬 머신 저장소로 가져오려는 시도를 보고합니다. 이 동작은 구독에 정의하는 Automation 계정 수에 따라 그리고 인증이 성공했는지 여부에 따라 달라집니다.

## <a name="job-behavior"></a>작업 동작

Hybrid Runbook Worker의 작업은 Azure 샌드박스에서 실행될 때와는 약간 다르게 처리됩니다. 한 가지 중요한 차이점은 Hybrid Runbook Worker의 작업 기간에는 제한이 없다는 것입니다. Azure 샌드박스에서 실행된 Runbook은 [공평 분배](automation-runbook-execution.md#fair-share)로 인해 3시간으로 제한됩니다. 장기 실행 Runbook의 경우 다시 시작할 수 있는 복원력이 있는지 확인하려고 합니다. 예를 들면 Hybrid Worker를 호스트하는 머신이 다시 부팅되는 경우입니다. 하이브리드 작업자 호스트 컴퓨터가 다시 부팅되면, 실행 중인 모든 Runbook 작업이 처음부터 다시 시작되거나 PowerShell 워크플로 Runbook의 마지막 검사점에서 다시 시작됩니다. Runbook 작업이 3회를 초과하여 다시 시작되면 일시 중단됩니다.

## <a name="run-only-signed-runbooks"></a>서명된 Runbook만 실행

일부 구성을 사용하여 서명된 Runbook만 실행하도록 Hybrid Runbook Worker를 구성할 수 있습니다. 다음 섹션에서는 서명된 [Windows Hybrid Runbook Worker](#windows-hybrid-runbook-worker) 및 [Linux Hybrid Runbook Worker](#linux-hybrid-runbook-worker)를 실행하도록 Hybrid Runbook Worker를 설정하는 방법에 대해 설명합니다.

> [!NOTE]
> 서명된 Runbook만 실행하도록 하Hybrid Runbook Worker를 구성하고 나면, 서명되지 **않은** Runbook은 Worker에서 실행되지 않습니다.

### <a name="windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker

#### <a name="create-signing-certificate"></a>서명 인증서 만들기

다음 예제에서는 Runbook에 서명하는 데 사용할 수 있는 자체 서명된 인증서를 만듭니다. 샘플은 인증서를 만들고 내보냅니다. 나중에 인증서를 Hybrid Runbook Worker로 가져옵니다. 지문도 반환되며, 값은 나중에 인증서를 참조하는 데 사용됩니다.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

#### <a name="configure-the-hybrid-runbook-workers"></a>Hybrid Runbook Worker 구성

생성된 인증서를 그룹의 각 Hybrid Runbook Worker에 복사합니다. 다음 스크립트를 실행하여 인증서를 가져오고, Runbook에서 서명 유효성 검사를 사용하도록 Hybrid Worker를 구성합니다.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>인증서를 사용하여 Runbook에 서명

Hybrid Runbook Worker가 서명에 Runbook만 사용하도록 구성된 경우, Hybrid Runbook Worker에서 사용할 수 있는 Runbook에 서명해야 합니다. 다음 샘플 PowerShell을 사용하여 Runbook에 서명합니다.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Runbook이 서명되고 나면, Automation 계정으로 가져오고 서명 블록과 함께 게시해야 합니다. Runbook을 가져오는 방법에 대한 자세한 내용은 [파일의 Runbook을 Azure Automation으로 가져오기](manage-runbooks.md#import-a-runbook)를 참조하세요.

### <a name="linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker

Linux Hybrid Runbook Worker에서 Runbook에 서명하려면 Hybrid Runbook Worker의 머신에 [GPG](https://gnupg.org/index.html) 실행 파일이 있어야 합니다.

#### <a name="create-a-gpg-keyring-and-keypair"></a>GPG 인증 키 및 키 쌍 만들기

인증 키 및 키 쌍을 만들려면 `nxautomation` Hybrid Runbook Worker 계정을 사용해야 합니다.

`sudo`를 사용하여 `nxautomation` 계정으로 로그인합니다.

```bash
sudo su – nxautomation
```

`nxautomation` 계정이 사용되면 GPG 키 쌍을 생성합니다.

```bash
sudo gpg --generate-key
```

GPG는 키 쌍을 만드는 단계를 안내합니다. 이름, 이메일 주소, 만료 시간, 암호를 제공하고 키가 생성될 때까지 머신에서 충분한 엔트로피를 기다려야 합니다.

GPG 디렉터리가 sudo를 사용하여 생성되었으므로 소유자를 `nxautomation`으로 변경해야 합니다. 

다음 명령을 실행하여 소유자를 변경합니다.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 사용할 수 있도록 인증 키 설정

인증 키가 만들어지면 Hybrid Runbook Worker에서 인증 키를 사용할 수 있도록 만들어야 합니다. `[worker-optional]` 섹션에서 다음 예제를 포함하도록 설정 파일(`/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf`)을 수정합니다.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>서명 유효성 검사가 설정되어 있는지 확인

머신에서 서명 유효성 검사를 사용하지 않도록 설정한 경우 서명 유효성 검사를 사용하도록 설정해야 합니다. 다음 명령을 실행하여 서명 유효성 검사를 사용하도록 설정합니다. `<LogAnalyticsworkspaceId>`를 작업 영역 ID로 바꿉니다.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Runbook 서명

서명 유효성 검사가 구성되면 다음 명령을 사용하여 Runbook에 서명할 수 있습니다.

```bash
gpg –-clear-sign <runbook name>
```

서명된 Runbook의 이름은 `<runbook name>.asc`입니다.

이제 서명된 Runbook을 Azure Automation에 업로드할 수 있고 일반 Runbook처럼 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 시작하는 데 사용할 수 있는 여러 가지 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 텍스트 편집기를 사용하여 Azure Automation에서 PowerShell Runbook을 작업하기 위한 여러 가지 방법을 알아보려면 [Azure Automation에서 Runbook 편집](automation-edit-textual-runbook.md)을 참조하세요.
* Runbook이 성공적으로 완료되지 않으면 [Runbook 실행 실패](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)에 대한 문제 해결 가이드를 검토하세요.
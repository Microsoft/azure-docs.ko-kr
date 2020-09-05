---
title: Hybrid Runbook Worker에서 Azure Automation Runbook 실행
description: 이 문서에서는 Hybrid Runbook Worker를 사용하여 로컬 데이터 센터 또는 클라우드 공급자에 있는 머신에서 Runbook을 실행하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 08/26/2020
ms.topic: conceptual
ms.openlocfilehash: 13c982dcfab21371ea6017f730065cc5ced4b79e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959572"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 실행

[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 실행되는 Runbook은 일반적으로 로컬 컴퓨터에서, 또는 Worker가 배포된 로컬 환경에 있는 리소스를 기준으로 리소스를 관리합니다. Azure Automation의 Runbook은 일반적으로 Azure 클라우드에서 리소스를 관리합니다. Azure Automation에서 실행되는 Runbook과 Hybrid Runbook Worker에서 실행되는 Runbook은 용도는 다르지만 구조상 차이가 없습니다.

Hybrid Runbook Worker에서 실행되도록 Runbook을 작성하는 경우, 해당 Worker를 호스트하는 머신에서 Runbook을 편집하고 테스트해야 합니다. 호스트 머신에는 로컬 리소스를 관리하고 액세스하는 데 필요한 모든 PowerShell 모듈과 네트워크 액세스 권한이 있습니다. Hybrid Runbook Worker 머신에서 Runbook을 테스트했으면 Azure Automation 환경으로 업로드하여 해당 Worker에서 실행할 수 있습니다. 

## <a name="plan-runbook-job-behavior"></a>Runbook 작업 동작 계획

Azure Automation은 Azure 샌드박스에서 실행되는 작업과 약간 다른 방식으로 Hybrid Runbook Worker에 대한 작업을 처리합니다. 장기 실행 Runbook의 경우 다시 시작할 수 있는 복원력이 있는지 확인해야 합니다. 작업 동작에 대한 자세한 내용은 [Hybrid Runbook Worker 작업](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)을 참조하세요.

Hybrid Runbook Worker 작업은 Windows의 경우 로컬 **시스템** 계정으로 실행되고, Linux의 경우 **nxautomation** 계정으로 실행됩니다. Linux의 경우 **nxautomation** 계정에 Runbook 모듈이 저장된 위치에 대한 액세스 권한이 있는지 확인하세요. [Install-Module](/powershell/module/powershellget/install-module) cmdlet을 사용할 때는 **nxautomation** 계정이 액세스 권한을 갖도록 `Scope` 매개 변수에 AllUsers를 지정하세요. Linux의 PowerShell에 대한 자세한 내용은 [비 Windows 플랫폼에서 PowerShell의 알려진 문제](/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)를 참조하세요.

## <a name="set-up-runbook-permissions"></a>Runbook 사용 권한 설정

다음과 같은 방법으로 Runbook이 Hybrid Runbook Worker에 실행되도록 사용 권한을 설정합니다.

* Runbook이 로컬 리소스에 자체 인증을 제공하도록 설정합니다.
* [Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)를 사용하여 인증을 구성합니다. 
* 모든 Runbook에 대한 사용자 컨텍스트를 제공하도록 실행 계정을 지정합니다.

## <a name="use-runbook-authentication-to-local-resources"></a>로컬 리소스에 대한 Runbook 인증 사용

리소스에 자체 인증을 제공하는 Runbook을 준비할 때는 Runbook에서 [자격 증명](./shared-resources/credentials.md) 자산과 [인증서](./shared-resources/certificates.md) 자산을 사용합니다. 여러 cmdlet을 사용하여 Runbook이 여러 리소스로 인증될 수 있도록 자격 증명을 지정할 수 있습니다. 다음 예제에서는 컴퓨터를 다시 시작하는 Runbook의 일부를 보여 줍니다. 이 예제에서는 자격 증명 자산에서 자격 증명을 검색하고 변수 자산에서 컴퓨터 이름을 검색한 다음 `Restart-Computer` cmdlet에서 이 값을 사용합니다.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

[InlineScript](automation-powershell-workflow.md#use-inlinescript) 작업을 사용할 수도 있습니다. `InlineScript`를 사용하면 자격 증명을 사용하여 다른 컴퓨터에서 코드 블록을 실행할 수 있습니다.

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>관리 ID를 통한 Runbook 인증 사용

Azure 가상 머신의 Hybrid Runbook Worker는 관리 ID를 사용하여 Azure 리소스를 인증할 수 있습니다. 실행 계정 대신 Azure 리소스에 대한 관리 ID를 사용하면 다음과 같은 작업이 필요하지 않다는 이점이 있습니다.

* 실행 인증서를 내보낸 후 Hybrid Runbook Worker로 가져오기.
* 실행 계정에서 사용하는 인증서 갱신.
* Runbook 코드에서 실행 연결 개체 처리.

다음 단계에 따라 Hybrid Runbook Worker에서 Azure 리소스에 대한 관리 ID를 사용하세요.

1. Azure VM을 만듭니다.
2. VM에서 Azure 리소스에 대한 관리 ID를 구성합니다. [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)을 참조하세요.
3. VM에 Resource Manager에 있는 리소스 그룹에 대한 액세스 권한을 부여합니다. [Windows VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)를 참조하세요.
4. VM에 Hybrid Runbook Worker를 설치합니다. [Windows Hybrid Runbook Worker 배포](automation-windows-hrw-install.md) 또는 [Linux Hybrid Runbook Worker 배포](automation-linux-hrw-install.md)를 참조하세요.
5. Runbook이 `Identity` 매개 변수로 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet을 사용하여 Azure 리소스로 인증되도록 업데이트합니다. 이 구성은 실행 계정을 사용하고 관련 계정 관리를 수행해야 하는 필요를 줄여 줍니다.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity`는 시스템 할당 ID와 단일 사용자 할당 ID를 사용하여 Hybrid Runbook Worker에 대해 작동합니다. Hybrid Runbook Worker에서 여러 개의 사용자 할당 ID를 사용할 경우 Runbook이 `Connect-AzAccount`에 `AccountId` 매개 변수를 지정하여 특정 사용자 할당 ID를 선택해야 합니다.

## <a name="use-runbook-authentication-with-run-as-account"></a>실행 계정을 통한 Runbook 인증 사용

Runbook이 로컬 리소스에 자체 인증을 제공하도록 하는 대신 Hybrid Runbook Worker 그룹에 실행 계정을 지정할 수 있습니다. 이렇게 하려면 로컬 리소스에 대한 액세스 권한을 갖는 [자격 증명 자산](./shared-resources/credentials.md)을 정의해야 합니다. 이러한 리소스에는 그룹의 Hybrid Runbook Worker에서 이 자격 증명으로 실행된 모든 Runbook과 인증서 저장소가 포함됩니다.

자격 증명에 대한 사용자 이름은 다음 서식 중 하나여야 합니다.

* domain\username
* username@domain
* 사용자 이름(온-프레미스 컴퓨터에 로컬인 계정용)

Hybrid Runbook Worker 그룹에 실행 계정을 지정하려면 다음 절차를 사용합니다.

1. 로컬 리소스에 대한 액세스로 [자격 증명 자산](./shared-resources/credentials.md)을 만듭니다.
2. Azure Portal에서 Automation 계정을 엽니다.
3. **Hybrid Worker 그룹**을 선택하고 특정 그룹을 선택합니다.
4. **모든 설정**을 선택하고 **Hybrid Worker 그룹 설정**을 선택합니다.
5. **실행 계정**의 값을 **기본값**에서 **사용자 지정**으로 변경합니다.
6. 자격 증명을 선택하고 **저장**을 클릭합니다.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>실행 계정 인증서 설치

Azure에서 리소스를 배포하는 자동화된 빌드 프로세스의 일부로 배포 시퀀스의 작업 또는 단계 집합을 지원하기 위해 온-프레미스 시스템에 대한 액세스가 필요할 수 있습니다. 실행 계정을 사용하여 Azure에 대해 인증을 제공하려면 실행 계정 인증서를 설치해야 합니다.

PowerShell Runbook인 다음 **Export-RunAsCertificateToHybridWorker**는 Azure Automation 계정에서 실행 인증서를 내보내고 다운로드하며 동일한 계정에 연결된 Hybrid Runbook Worker의 로컬 머신 인증서 저장소로 가져옵니다. 이 단계가 완료되면 Runbook은 Worker가 실행 계정을 사용하여 Azure를 성공적으로 인증할 수 있는지 확인합니다.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
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

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 라이브러리를 항목을 검색할 때 `Connect-AzAccount`가 표시되지 않는 경우 `Add-AzAccount`를 사용하거나 Automation 계정에서 모듈을 업데이트할 수 있습니다.

실행 계정 준비를 완료하려면:

1. **Export-RunAsCertificateToHybridWorker** Runbook을 **.ps1** 확장명으로 컴퓨터에 저장합니다.
2. 저장한 Runbook을 Automation 계정으로 가져옵니다.
3. Runbook을 편집하여 `Password` 변수의 값을 자신의 암호로 변경합니다. 
4. Runbook을 게시합니다.
5. 실행 계정을 사용하여 Runbook을 실행 및 인증하는 Hybrid Runbook Worker 그룹을 대상으로 Runbook을 실행합니다. 
6. 작업 스트림이 인증서를 로컬 머신 저장소로 가져오려는 시도를 보고하고 있으며 그 뒤에 여러 줄이 있는지 검토합니다. 이 동작은 구독에 몇 개의 Automation 계정을 정의했는지와 인증의 성공 정도에 따라 달라집니다.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker에서 서명된 Runbook으로 작업

Windows Hybrid Runbook Worker가 서명된 Runbook만 실행하도록 구성할 수 있습니다. 

> [!IMPORTANT]
> Hybrid Runbook Worker가 서명된 Runbook만 실행하도록 구성하면 서명되지 않은 Runbook은 해당 Worker에서 실행되지 않습니다.

### <a name="create-signing-certificate"></a>서명 인증서 만들기

다음 예제에서는 Runbook에 서명하는 데 사용할 수 있는 자체 서명된 인증서를 만듭니다. 이 코드는 인증서를 만든 다음 나중에 Hybrid Runbook Worker가 가져올 수 있도록 내보냅니다. 나중에 인증서를 참조할 때 사용할 수 있도록 지문도 반환됩니다.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>인증서 가져오기 및 서명 유효성 검사를 위해 Worker 구성

앞에서 만든 인증서를 그룹의 각 Hybrid Runbook Worker에 복사합니다. 다음 스크립트를 실행하여 인증서를 가져오고, Runbook에서 서명 유효성 검사를 사용하도록 Worker를 구성합니다.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>인증서를 사용하여 Runbook에 서명

Hybrid Runbook Worker가 서명된 Runbook만 사용하도록 구성된 경우, Runbook을 Hybrid Runbook Worker에서 사용하려면 서명해야 합니다. 다음 샘플 PowerShell 코드를 사용하여 Runbook에 서명합니다.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Runbook이 서명되고 나면, Automation 계정으로 Runbook을 가져오고 서명 블록과 함께 게시해야 합니다. Runbook을 가져오는 방법에 대한 자세한 내용은 [Runbook 가져오기](manage-runbooks.md#import-a-runbook)를 참조하세요.

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker에서 서명된 Runbook으로 작업

서명된 Runbook으로 작업할 수 있으려면 로컬 머신에서 Linux Hybrid Runbook Worker에 [GPG](https://gnupg.org/index.html) 실행 파일이 있어야 합니다.

> [!IMPORTANT]
> Hybrid Runbook Worker가 서명된 Runbook만 실행하도록 구성하면 서명되지 않은 Runbook은 해당 Worker에서 실행되지 않습니다.

### <a name="create-a-gpg-keyring-and-keypair"></a>GPG 인증 키 및 키 쌍 만들기

GPG 인증 키와 키 쌍을 만들려면 Hybrid Runbook Worker [nxautomation 계정](automation-runbook-execution.md#log-analytics-agent-for-linux)을 사용합니다.

1. sudo 애플리케이션을 사용하여 **nxautomation** 계정으로 로그인합니다.

    ```bash
    sudo su – nxautomation
    ```

2. **nxautomation**을 사용하는 상태에서 GPG 키 쌍을 생성합니다. GPG에서 이 단계를 안내합니다. 이름, 메일 주소, 만료 시간, 암호를 지정해야 합니다. 그런 다음 키가 생성될 수 있을 만큼 머신에서 충분한 엔트로피가 발생할 때까지 기다려야 합니다.

    ```bash
    sudo gpg --generate-key
    ```

3. GPG 디렉터리는 sudo를 사용하여 생성되었으므로 다음 명령을 사용하여 소유자를 **nxautomation**으로 변경해야 합니다.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 인증 키를 사용할 수 있도록 설정

인증 키가 만들어지면 Hybrid Runbook Worker에서 사용할 수 있도록 설정해야 합니다. 설정 파일 **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf**가 파일 섹션 `[worker-optional]` 아래에 다음과 같은 예제 코드를 포함하도록 수정합니다.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>서명 유효성 검사가 설정되어 있는지 확인

머신에서 서명 유효성 검사를 사용하지 않도록 설정한 경우 다음 sudo 명령을 사용하여 사용하도록 설정해야 합니다. `<LogAnalyticsworkspaceId>`를 작업 영역 ID로 바꿉니다.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Runbook 서명

서명 유효성 검사를 구성한 후에는 다음 GPG 명령을 사용하여 Runbook에 서명합니다.

```bash
gpg –-clear-sign <runbook name>
```

서명된 Runbook을 **<runbook name>.asc**라고 합니다.

이제 서명된 Runbook을 Azure Automation에 업로드하여 일반 Runbook처럼 실행할 수 있습니다.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에서 Runbook 시작

[Azure Automation에서 Runbook 시작](start-runbooks.md)에서 Runbook을 시작하는 여러 가지 방법을 설명합니다. Hybrid Runbook Worker의 Runbook 시작에서는 Hybrid Runbook Worker 그룹의 이름을 지정할 수 있도록 지원하는 **실행 계정** 옵션을 사용합니다. 그룹이 지정되면 이 그룹에 있는 Worker 중 하나가 Runbook을 검색하여 실행합니다. Runbook에서 이 옵션을 지정하지 않을 경우 Azure Automation이 평소와 같이 Runbook을 실행합니다.

Azure Portal에서 Runbook을 시작하면 **실행 대상** 옵션이 표시됩니다. 여기에서 **Azure** 또는 **Hybrid Worker**를 선택할 수 있습니다. **Hybrid Worker**를 선택한 경우 드롭다운에서 Hybrid Runbook Worker 그룹을 선택할 수 있습니다.

PowerShell을 사용하여 Runbook을 시작한 경우 `RunOn` 매개 변수로 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) cmdlet을 사용합니다. 다음 예제에서는 Windows PowerShell을 사용하여 MyHybridGroup이라는 Hybrid Runbook Worker 그룹에서 **Test-Runbook**이라는 Runbook을 시작합니다.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>로깅

하이브리드 runbook worker에서 실행 되는 runbook 문제를 해결 하기 위해 로그는 다음 위치에 로컬로 저장 됩니다.

* Windows의 `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` 경우 자세한 작업 런타임 프로세스 로깅 높은 수준의 runbook 작업 상태 이벤트는 **응용 프로그램 및 서비스 Logs\Microsoft-Automation\Operations** 이벤트 로그에 기록 됩니다.

* Linux에서 사용자 하이브리드 작업자 로그는에서 찾을 수 `/home/nxautomation/run/worker.log` 있으며 시스템 runbook worker 로그는에서 찾을 수 있습니다 `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>다음 단계

* Runbook이 성공적으로 완료되지 않을 경우 [Runbook 실행 실패](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)를 위한 문제 해결 가이드를 검토하세요.
* 언어 참조 및 학습 모듈을 포함하여 PowerShell에 대한 자세한 내용은 [PowerShell 문서](/powershell/scripting/overview)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)을 참조하세요.

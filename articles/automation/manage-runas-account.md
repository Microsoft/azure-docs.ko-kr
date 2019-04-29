---
title: Azure Automation 실행 계정 관리
description: 이 문서에서는 PowerShell 또는 포털에서 실행 계정을 관리하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af67109fb7f55f365cd71714a3eefab2336b636a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61301142"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Azure Automation 실행 계정 관리

Azure Automation의 실행 계정은 Azure에서 Azure cmdlet으로 리소스를 관리하는 데 필요한 인증을 제공하는 데 사용됩니다.

실행 계정을 만들면 Azure Active Directory에 새로운 서비스 사용자가 생성되며 구독 수준에서 이 사용자에게 기여자 역할을 할당합니다. Azure 가상 머신에서 Hybrid Runbook Worker를 사용하는 Runbook의 경우 실행 계정 대신 [Azure 리소스에 대한 관리 ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 사용하여 Azure 리소스를 인증할 수 있습니다.

실행 계정에는 다음과 같은 두 종류가 있습니다.

* **Azure 실행 계정** - 이 계정은 Resource Manager 배포 모델 리소스를 관리하는 데 사용됩니다.
  * 자체 서명된 인증서로 Azure AD 애플리케이션을 만들고, Azure AD에 애플리케이션의 서비스 주체 계정을 만들며, 현재 구독에 있는 계정에 대해 기여자 역할을 할당합니다. 이 설정을 소유자 또는 다른 어떤 역할로든 변경할 수 있습니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.
  * 지정된 Automation 계정에서 *AzureRunAsCertificate*라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 Azure AD 애플리케이션에서 사용되는 인증서 개인 키를 보유합니다.
  * 지정된 Automation 계정에서 *AzureRunAsConnection*이라는 Automation 연결 자산을 만듭니다. 연결 자산은 applicationId, tenantId, subscriptionId 및 인증서 지문을 보유합니다.

* **Azure Classic 실행 계정** - 이 계정은 Classic 배포 모델 리소스를 관리하는 데 사용됩니다.
  * 구독에 관리 인증서를 만듭니다.
  * 지정된 Automation 계정에서 *AzureClassicRunAsCertificate*라는 Automation 인증서 자산을 만듭니다. 인증서 자산은 관리 인증서에서 사용되는 인증서 개인 키를 보유합니다.
  * 지정된 Automation 계정에서 *AzureClassicRunAsConnection*이라는 Automation 연결 자산을 만듭니다. 연결 자산은 구독 이름, subscriptionId 및 인증서 자산 이름을 보유합니다.
  * 만들거나 갱신 구독에 공동 관리자 여야 합니다.
  
  > [!NOTE]
  > Azure CSP(Cloud Solution Provider) 구독은 Azure Resource Manager 모델만 지원하므로 Azure Resource Manager 이외의 서비스는 프로그램에서 사용할 수 없습니다. CSP 구독 사용 시에는 Azure 클래식 실행 계정이 생성되지 않습니다. Azure 실행 계정은 계속 생성됩니다. CSP 구독에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments)를 참조하세요.

## <a name="permissions"></a>실행 계정 구성 권한

실행 계정을 만들거나 업데이트하려면 특정 권한이 있어야 합니다. 글로벌 관리자/공동 관리자는 모든 작업을 완료할 수 있습니다. 업무가 구분되어 있는 경우를 위해 다음 표에 작업, 해당 cmdlet 및 필요한 권한의 목록이 나와 있습니다.

|Task|Cmdlet  |최소 권한  |권한을 설정하는 위치|
|---|---------|---------|---|
|Azure AD 애플리케이션 만들기|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | 애플리케이션 개발자 역할<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>홈 > Azure Active Directory > 앱 등록 |
|애플리케이션에 자격 증명을 추가합니다.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | 애플리케이션 관리자 또는 글로벌 관리자<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>홈 > Azure Active Directory > 앱 등록|
|Azure AD 서비스 사용자 생성 및 가져오기|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | 애플리케이션 관리자 또는 글로벌 관리자        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>홈 > Azure Active Directory > 앱 등록|
|지정된 보안 주체의 RBAC 역할 할당 또는 가져오기|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | 사용자 액세스 관리자 또는 소유자        | [구독](../role-based-access-control/role-assignments-portal.md)</br>홈 > 구독 > \<구독 이름\> - 액세스 제어(IAM)|
|Automation 인증서 생성 또는 제거|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | 리소스 그룹의 기여자         |Automation 계정 리소스 그룹|
|Automation 연결 생성 또는 제거|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|리소스 그룹의 기여자 |Automation 계정 리소스 그룹|

<sup>1</sup> Azure AD 테넌트의 **사용자 설정** 페이지에 있는 **사용자가 애플리케이션을 등록할 수 있음** 옵션이 **예**로 설정된 경우, Azure AD 테넌트의 관리자가 아닌 사용자가 [AD 애플리케이션을 등록](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)할 수 있습니다. 앱 등록 설정이 **아니요**로 지정되어 있는 경우 이 작업을 수행하는 사용자는 Azure AD의 전역 관리자여야 합니다.

구독의 전역 관리자/공동 관리자 역할에 추가되기 전에 구독 Active Directory 인스턴스의 멤버가 아닌 경우 게스트로 추가됩니다. 이 경우에는 **Automation 계정 추가** 페이지에 `You do not have permissions to create…` 경고가 표시됩니다. 전역 관리자/공동 관리자 역할에 처음 추가된 사용자는 구독 Active Directory 인스턴스에서 제거한 다음 다시 추가하여 Active Directory의 완전한 사용자로 만들 수 있습니다. Azure Portal의 **Azure Active Directory** 창에서 이 상황을 확인하려면 **사용자 및 그룹**을 선택한 다음 **모든 사용자**를 선택하거나 특정 사용자를 선택한 후 **프로필**을 선택합니다. 사용자 프로필에서 **사용자 유형** 속성의 값은 **Guest**와 같지 않아야 합니다.

## <a name="permissions-classic"></a>클래식 실행 계정 구성에 대 한 사용 권한

를 구성 하거나 클래식 실행 계정의 갱신 해야 합니다 **공동 관리자** 구독 수준에서 역할입니다. 기본 사용 권한에 대 한 자세한 내용은 참조 하세요 [Azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md#add-a-co-administrator)합니다.

## <a name="create-a-run-as-account-in-the-portal"></a>포털에서 실행 계정 만들기

이 섹션에서는 다음 단계를 수행하여 Azure Portal에서 Azure Automation 계정을 업데이트합니다. 실행 계정과 클래식 실행 계정을 개별적으로 만듭니다. 클래식 리소스를 관리할 필요가 없으면 Azure 실행 계정만 만들면 됩니다.  

1. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.
2. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Automation**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Automation 계정**을 선택합니다.
3. **Automation 계정** 페이지의 Automation 계정 목록에서 Automation 계정을 선택합니다.
4. 왼쪽 창의 **계정 설정** 섹션 아래에서 **실행 계정**을 선택합니다.  
5. 필요한 계정에 따라 **Azure 실행 계정** 또는 **Azure 클래식 실행 계정**을 선택합니다. **Azure 실행 계정 추가** 또는 **Azure 클래식 실행 계정 추가** 중 하나를 선택하여 해당 창이 표시되면, 개요 정보를 검토한 후에 **만들기**를 클릭하여 실행 계정 만들기를 계속 진행합니다.  
6. Azure에서 Automation 계정을 만드는 동안 메뉴의 **알림** 아래에서 진행 상황을 추적할 수 있습니다. 계정을 만드는 중이라는 배너도 표시됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.  

## <a name="create-run-as-account-using-powershell"></a>PowerShell을 사용하여 실행 계정 만들기

## <a name="prerequisites"></a>필수 조건

다음 목록에서는 PowerShell에서 실행 계정을 만들기 위한 요구 사항을 제공합니다.

* Azure Resource Manager 모듈 3.4.1 이상이 설치된 Windows 10 또는 Windows Server 2016. PowerShell 스크립트는 이전 버전의 Windows를 지원하지 않습니다.
* Azure PowerShell 1.0 이상 PowerShell 1.0 릴리스에 대한 정보는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.
* Automation 계정 - *–AutomationAccountName* 및 *-ApplicationDisplayName* 매개 변수의 값으로 참조됩니다.
* [실행 계정을 구성하는 데 필요한 권한](#permissions)에 나열된 것과 동일한 권한

스크립트의 필수 매개 변수인 *SubscriptionID*, *ResourceGroup* 및 *AutomationAccountName*의 값을 가져오려면 다음 단계를 완료합니다.

1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Automation**을 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Automation 계정**을 선택합니다.
1. Automation 계정 페이지에서 Automation 계정을 선택한 다음 **계정 설정** 아래에서 **속성**을 선택합니다.  
1. **속성** 페이지의 **구독 ID**, **이름**, **리소스 그룹** 값을 메모합니다.

   ![Automation 계정 "속성" 페이지](media/manage-runas-account/automation-account-properties.png)

이 PowerShell 스크립트는 다음 구성에 대한 지원을 포함합니다.

* 자체 서명된 인증서를 사용하여 실행 계정을 만듭니다.
* 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.
* 엔터프라이즈 CA(인증 기관)에서 발급한 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.
* Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정을 만듭니다.

>[!NOTE]
> 클래식 실행 계정을 만드는 옵션을 선택한 경우 실행 스크립트를 실행한 후에 Automation 계정이 만들어진 구독의 관리 저장소에 공용 인증서(.cer 파일 이름 확장)를 업로드합니다.

1. 컴퓨터에 다음 스크립트를 저장합니다. 이 예에서는 이를 *New-RunAsAccount.ps1*이라는 파일 이름으로 저장합니다.

   이 스크립트는 여러 Azure Resource Manager cmdlet을 사용하여 리소스를 만듭니다. 다음 표에는 cmdlet 및 필요한 권한이 나와 있습니다.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Add-AzureRmAccount**는 이제 **Connect-AzureRMAccount**에 대한 별칭입니다. 라이브러리를 항목을 검색할 때 **Connect-AzureRMAccount**가 표시되지 않는 경우 **Add-AzureRmAccount**를 사용하거나 Automation 계정에서 [모듈을 업데이트](automation-update-azure-modules.md)할 수 있습니다.

1. 사용자 컴퓨터의 **시작** 화면에서 관리자 권한으로 **Windows PowerShell**을 시작합니다.
1. 승격된 명령줄 셸에서 1단계에서 만든 스크립트가 포함된 폴더로 이동합니다.  
1. 필요한 구성에 대한 매개 변수 값을 사용하여 스크립트를 실행합니다.

    **자체 서명된 인증서를 사용하여 실행 계정 만들기**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **엔터프라이즈 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Azure Government 클라우드에서 자체 서명된 인증서를 사용하여 실행 계정 및 클래식 실행 계정 만들기**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > 스크립트를 실행한 후에 Azure에 인증할지를 묻는 메시지가 표시됩니다. 구독 관리자 역할의 구성원이자 구독의 공동 관리자인 계정으로 로그인합니다.

스크립트가 성공적으로 실행된 후에 다음을 적어둡니다.

* 자체 서명된 공용 인증서(.cer 파일)를 사용하여 클래식 실행 계정을 만든 경우 스크립트는 해당 계정을 만들고 PowerShell 세션을 실행하는 데 사용된 사용자 프로필(*%USERPROFILE%\AppData\Local\Temp*)의 컴퓨터에 있는 임시 파일 폴더에 저장합니다.

* 엔터프라이즈 공용 인증서(.cer 파일)를 사용하여 클래식 실행 계정을 만든 경우 이 인증서를 사용합니다. [관리 API 인증서를 Azure Portal에 업로드](../azure-api-management-certs.md)하는 지침을 따르세요.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>실행 또는 클래식 실행 계정 삭제

이 섹션에서는 실행 또는 클래식 실행 계정을 삭제하고 다시 만드는 방법을 설명합니다. 이 작업을 실행하는 경우 Automation 계정이 보존됩니다. 실행 또는 클래식 실행 계정을 삭제한 후에 Azure Portal에서 계정을 다시 만들 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. **Automation 계정** 페이지에서 **실행 계정**을 선택합니다.

3. **실행 계정** 속성 페이지에서 삭제하려는 실행 계정 또는 클래식 실행 계정을 선택합니다. 그런 다음 선택한 계정의 **속성** 창에서 **삭제**를 클릭합니다.

   ![실행 계정 삭제](media/manage-runas-account/automation-account-delete-runas.png)

1. 계정이 삭제되는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

1. 계정이 삭제되면 **실행 계정** 속성 페이지에서 **Azure 실행 계정** 만들기 옵션을 선택하여 계정을 다시 만들 수 있습니다.

   ![Automation 실행 계정 다시 만들기](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>자체 서명된 인증서 갱신

실행 계정 만료되기 전인 어떤 시점에서 인증서를 갱신해야 합니다. 실행 계정이 손상되었다고 생각하시면 삭제하고 다시 만들 수 있습니다. 이 섹션에서는 이러한 작업을 수행하는 방법을 설명합니다.

실행 계정에 만든 자체 서명된 인증서는 생성 날짜로부터 1년이 되는 날에 만료됩니다. 만료되기 전에 언제든지 갱신할 수 있습니다. 인증서를 갱신하면 큐에 대기하거나 활발하게 실행 중이며 실행 계정으로 인증되는 모든 Runbook이 부정적인 영향을 받지 않도록 현재 유효한 인증서가 보존됩니다. 인증서는 만료 날짜까지 유효합니다.

> [!NOTE]
> 엔터프라이즈 인증 기관에서 발급한 인증서를 사용하도록 Automation 실행 계정을 구성하고 이 옵션을 사용하는 경우 엔터프라이즈 인증서는 자체 서명된 인증서로 교체됩니다.

인증서를 갱신하려면 다음을 수행합니다.

1. Azure Portal에서 Automation 계정을 엽니다.

1. **계정 설정**에서 **실행 계정**을 선택합니다.

    ![Automation 계정 속성 창](media/manage-runas-account/automation-account-properties-pane.png)

1. **실행 계정** 속성 페이지에서 인증서를 갱신하려는 실행 계정 또는 클래식 실행 계정을 선택합니다.

1. 선택한 계정의 **속성** 창에서 **인증서 갱신**을 클릭합니다.

    ![실행 계정용 인증서 갱신](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. 인증서가 갱신되는 동안 메뉴의 **알림**에서 진행률을 추적할 수 있습니다.

## <a name="limiting-run-as-account-permissions"></a>실행 계정 권한 제한

Azure Automation의 리소스에 대한 자동화의 대상 지정을 제어하기 위해 실행 계정에는 기본적으로 구독에 대한 참가자 권한이 부여됩니다. RunAs 서비스 주체가 수행할 수 있는 작업을 제한해야 하는 경우, 해당 계정을 참가자 역할에서 구독으로 제거하고 지정하려는 리소스 그룹에 참가자로 추가할 수 있습니다.

Azure Portal에서 **구독**을 선택하고 Automation Account의 구독을 선택합니다. **액세스 제어(IAM)**, **역할 할당** 탭을 차례로 선택합니다. Azure Automation의 서비스 주체(예: \<AutomationAccountName\>_고유 식별자)를 검색합니다. 계정을 선택하고 **제거**를 클릭하여 구독에서 제거합니다.

![구독 참가자](media/manage-runas-account/automation-account-remove-subscription.png)

리소스 그룹에 서비스 주체를 추가하려면 Azure Portal에서 리소스 그룹을 선택하고 **액세스 제어(IAM)** 를 선택합니다. **역할 할당 추가**를 선택하면 **역할 할당 추가** 페이지가 열립니다. **역할**에 **참가자**를 선택합니다. **선택** 텍스트 상자에 실행 계정에 대한 서비스 주체의 이름을 입력하고 목록에서 선택합니다. **저장**을 클릭하여 변경 내용을 저장합니다. Azure Automation 실행 서비스 주체 액세스 권한을 부여할 리소스 그룹에 대해 이 단계를 수행합니다.

## <a name="misconfiguration"></a>잘못된 구성

실행 또는 클래식 실행 계정이 제대로 작동하는 데 필요한 일부 구성 항목이 초기 설정 중에 제대로 삭제되거나 생성되지 않았습니다. 항목은 다음과 같습니다.

* 인증서 자산
* 연결 자산
* 실행 계정이 참여자 역할에서 제거됨
* Azure AD의 서비스 주체 또는 애플리케이션

잘못된 구성의 이전 및 다른 인스턴스에서 Automation 계정은 변경 사항을 감지하고 계정의 **실행 계정** 속성 창에서 *불완전*이라는 상태를 표시합니다.

![불완전 실행 계정 구성 상태](media/manage-runas-account/automation-account-runas-incomplete-config.png)

실행 계정을 선택하면 계정 **속성** 창은 다음과 같은 오류 메시지를 표시합니다.

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

계정을 삭제하고 다시 만들어서 이러한 실행 계정 문제를 신속하게 해결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 서비스 주체에 대한 자세한 내용은 [애플리케이션 개체 및 서비스 주체 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요.
* 인증서 및 Azure 서비스에 대한 자세한 내용은 [Azure Cloud Services 인증서 개요](../cloud-services/cloud-services-certs-create.md)를 참조하세요.
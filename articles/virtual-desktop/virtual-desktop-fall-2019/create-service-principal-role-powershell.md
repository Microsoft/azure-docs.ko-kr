---
title: Azure Virtual Desktop(클래식) 서비스 주체 역할 할당 - Azure
description: Azure Virtual Desktop(클래식)에서 PowerShell을 사용하여 서비스 주체를 만들고 역할을 할당하는 방법입니다.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: eacb82408c2981375e599cf29c67b016ba0f3b41
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749852"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-azure-virtual-desktop-classic"></a>자습서: Azure Virtual Desktop(클래식)에서 PowerShell을 사용하여 서비스 주체 및 역할 할당 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다.

서비스 주체는 특정 목적에 대한 역할 및 권한을 할당하도록 Azure Active Directory에서 만들 수 있는 ID입니다. Azure Virtual Desktop에서 다음을 수행할 서비스 주체를 만들 수 있습니다.

- 특정 Azure Virtual Desktop 관리 작업을 자동화합니다.
- Azure Virtual Desktop에 대한 Azure Resource Manager 템플릿을 실행하는 경우 MFA 필요 사용자 대신 자격 증명으로 사용합니다.

이 자습서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Active Directory에 서비스 주체 만들기
> * Azure Virtual Desktop에서 역할 할당 만들기
> * 서비스 주체를 사용하여 Azure Virtual Desktop에 로그인

## <a name="prerequisites"></a>사전 요구 사항

서비스 주체를 만들고 역할 할당을 수행하기 전에 먼저 다음 세 가지 작업을 수행해야 합니다.

1. AzureAD 모듈을 설치합니다. 모듈을 설치하려면 관리자 권한으로 PowerShell을 실행하고 다음 cmdlet을 실행합니다.

    ```powershell
    Install-Module AzureAD
    ```

2. [Azure Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/).

3. 동일한 PowerShell 세션에서 이 문서의 모든 지침을 따릅니다. 창을 닫고 나중에 다시 열어 PowerShell 세션을 중단하는 경우 프로세스가 작동하지 않을 수 있습니다.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory에 서비스 주체 만들기

PowerShell 세션에서 필수 구성 요소를 충족했으면 다음 PowerShell cmdlet을 실행하여 Azure의 다중 테넌트 보안 주체를 만듭니다.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>PowerShell에서 자격 증명 보기

서비스 사용자에 대한 역할 할당을 만들기 전에 자격 증명을 확인하 고 나중에 참조할 수 있도록 기록해 둡니다. 암호는 이 PowerShell 세션을 닫은 후에는 검색할 수 없기 때문에 특히 중요합니다.

기록해야 하는 세 가지 자격 증명과 이를 가져오기 위해 실행해야 하는 cmdlet은 다음과 같습니다.

- 암호:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- 테넌트 ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- 애플리케이션 ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-azure-virtual-desktop"></a>Azure Virtual Desktop에서 역할 할당 만들기

다음으로, 서비스 주체가 Azure Virtual Desktop에 로그인할 수 있도록 역할 할당을 만들어야 합니다. 역할 할당을 만들 수 있는 권한이 있는 계정으로 로그인해야 합니다.

먼저, 아직 수행하지 않은 경우 PowerShell 세션에서 사용할 [Azure Virtual Desktop PowerShell 모듈을 다운로드하고 가져옵니다](/powershell/windows-virtual-desktop/overview/).

다음 PowerShell cmdlet을 실행하여 Azure Virtual Desktop에 연결하고 테넌트를 표시합니다.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

역할 할당을 만들 테넌트의 테넌트 이름을 찾으면 다음 cmdlet에서 해당 이름을 사용합니다.

```powershell
$myTenantName = "<Azure Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>서비스 주체를 사용하여 로그인

서비스 주체에 대한 역할 할당이 만들어지면 다음 cmdlet을 실행하여 서비스 주체가 Azure Virtual Desktop에 로그인할 수 있는지 확인합니다.

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

로그인한 후에는 서비스 주체를 사용하여 몇 가지 Azure Virtual Desktop PowerShell cmdlet을 테스트하여 모든 항목이 작동하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

서비스 주체가 만들어지고 Azure Virtual Desktop 테넌트에서 역할이 할당되면 이를 사용하여 호스트 풀을 만들 수 있습니다. 호스트 풀에 대해 자세히 알아보려면 Azure Virtual Desktop에서 호스트 풀을 만드는 방법에 대한 자습서로 계속 진행하세요.

 > [!div class="nextstepaction"]
 > [Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace-2019.md)

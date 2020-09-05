---
title: PowerShell을 사용하여 Azure DS Domain Services를 사용하도록 설정 | Microsoft Docs
description: Azure AD PowerShell 및 Azure PowerShell을 사용하여 Azure Active Directory Domain Services를 구성하고 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 27fec8b8b76bec4c5ac428258b1495fc1bef1abe
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068969"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>PowerShell을 사용하여 Azure Active Directory Domain Services 사용

Azure AD DS(Azure Active Directory Domain Services)는 Windows Server Active Directory와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP, Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 이러한 도메인 서비스는 도메인 컨트롤러를 직접 배포, 관리 및 패치하지 않고 사용할 수 있습니다. Azure AD DS는 기존 Azure AD 테넌트와 통합됩니다. 이러한 통합을 통해 사용자는 회사 자격 증명을 사용하여 로그인할 수 있으며, 기존 그룹과 사용자 계정을 사용하여 리소스에 대한 액세스를 보호할 수 있습니다.

이 문서에서는 PowerShell을 사용하여 Azure AD DS를 사용하도록 설정하는 방법을 보여줍니다.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음 리소스가 필요합니다.

* Azure PowerShell을 설치하고 구성합니다.
    * 필요한 경우 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](/powershell/azure/install-az-ps)합니다.
    * [Connect-AzAccount][Connect-AzAccount] cmdlet을 사용하여 Azure 구독에 로그인합니다.
* Azure AD PowerShell을 설치하고 구성합니다.
    * 필요한 경우 지침에 따라 [Azure AD PowerShell 모듈을 설치하고 Azure AD에 연결](/powershell/azure/active-directory/install-adv2)합니다.
    * [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인합니다.
* Azure AD DS를 사용하도록 설정하려면 Azure AD 테넌트에 *글로벌 관리자* 권한이 필요합니다.
* 필요한 Azure AD DS 리소스를 만들려면 Azure 구독에 *기여자* 권한이 필요합니다.

## <a name="create-required-azure-ad-resources"></a>필수 Azure AD 리소스 만들기

Azure AD DS에는 서비스 주체와 Azure AD 그룹이 필요합니다. 이러한 리소스를 통해 Azure AD DS 관리형 도메인이 데이터를 동기화하고, 관리되는 도메인에서 관리 권한이 있는 사용자를 정의할 수 있습니다.

먼저 Azure AD DS가 통신하고 자체 인증할 수 있도록 Azure AD 서비스 주체를 만듭니다. ID가 *2565bd9d-da50-47d4-8b85-4c97f669dc36*인 *도메인 컨트롤러 서비스*라는 특정 애플리케이션 ID가 사용됩니다. 이 애플리케이션 ID를 변경하지 마세요.

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet을 사용하여 Azure AD 서비스 주체를 만듭니다.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

이제 *AAD DC Administrators*라는 Azure AD 그룹을 만듭니다. 그러면 이 그룹에 추가된 사용자에게는 관리되는 도메인에서 관리 작업을 수행할 수 있는 권한이 부여됩니다.

다음과 같이 [New-AzureADGroup][New-AzureADGroup] cmdlet을 사용하여 *AAD DC Administrators* 그룹을 만듭니다.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

*AAD DC Administrators* 그룹을 만든 상태에서 [Add-AzureADGroupMember][Add-AzureADGroupMember] cmdlet을 사용하여 그룹에 사용자를 추가합니다. 먼저 [Get-AzureADGroup][Get-AzureADGroup] cmdlet을 사용하여 *AAD DC Administrators* 그룹을 가져온 다음, [Get-AzureADUser][Get-AzureADUser] cmdlet을 사용하여 원하는 사용자의 개체 ID를 가져옵니다.

다음 예에서 사용자 개체 ID는 UPN이 `admin@contoso.onmicrosoft.com`인 계정입니다. 이 사용자 계정을 *AAD DC Administrators* 그룹에 추가하려는 사용자의 UPN으로 바꿉니다.

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>지원 Azure 리소스 만들기

먼저 [Register-AzResourceProvider][Register-AzResourceProvider] cmdlet을 사용하여 Azure AD Domain Services 리소스 공급자를 등록합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

다음으로 [New-AzResourceGroup][New-AzResourceGroup] cmdlet을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹이 *westus* 지역에 만들어집니다. 자신의 이름과 원하는 지역을 사용합니다.

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Azure AD Domain Services에 사용할 가상 네트워크 및 서브넷을 만듭니다. 두 개의 서브넷이 생성됩니다. 하나는 *DomainServices*, 다른 하나는 *Workloads*용입니다. Azure AD DS는 전용 *DomainServices* 서브넷에 배포됩니다. 다른 애플리케이션 또는 워크로드를 이 서브넷에 배포하지 마세요. 나머지 VM에는 별도의 *Workloads* 또는 다른 서브넷을 사용합니다.

[New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] cmdlet을 사용하여 서브넷을 만든 다음, [New-AzVirtualNetwork][New-AzVirtualNetwork] cmdlet을 사용하여 가상 네트워크를 만듭니다.

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>관리형 도메인 만들기

이제 관리되는 도메인을 만들어 보겠습니다. Azure 구독 ID를 설정한 다음, 관리되는 도메인의 이름(예: *aaddscontoso.com*)을 입력합니다. [Get-AzSubscription][Get-AzSubscription] cmdlet을 사용하여 구독 ID를 가져올 수 있습니다.

가용성 영역을 지원하는 지역을 선택하면 Azure AD DS 리소스가 추가 중복성을 위해 여러 영역에 배포됩니다.

가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다.

Azure AD DS를 영역 간에 배포하기 위해 구성해야 할 항목은 없습니다. Azure 플랫폼은 리소스의 영역 배포를 자동으로 처리합니다. 자세한 내용을 보고 지역 가용성을 확인하려면 [Azure에서 가용성 영역이란?][availability-zones]을 참조하세요.

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

리소스를 만들고 PowerShell 프롬프트로 제어를 반환하는 데 몇 분 정도 걸립니다. 관리되는 도메인은 백그라운드에서 계속 프로비저닝되며 배포를 완료하는 데 최대 1시간이 걸릴 수 있습니다. Azure Portal의 관리되는 도메인 **개요** 페이지에는 이 배포 단계 전체에서 현재 상태가 표시됩니다.

관리되는 도메인이 프로비저닝을 마쳤다고 Azure Portal에 표시되면 다음 작업을 완료해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성하려면 포털에서 관리되는 도메인을 선택합니다. **개요** 창에 DNS 설정을 자동으로 구성하라는 메시지가 표시됩니다.
* 네트워크 보안 그룹을 만들어 관리되는 도메인에 대한 가상 네트워크의 트래픽을 제한합니다. 이러한 규칙을 적용하는 Azure 표준 부하 분산 장치가 생성됩니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호하며, 관리되는 도메인이 제대로 작동하는 데 꼭 필요합니다.
    * 네트워크 보안 그룹 및 필수 규칙을 만들려면 `Install-Script -Name New-AaddsNetworkSecurityGroup` 명령을 사용하여 `New-AzureAddsNetworkSecurityGroup` 스크립트를 설치한 다음, `New-AaddsNetworkSecurityGroup` 명령을 실행합니다. 관리되는 도메인의 필수 규칙이 자동으로 생성됩니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 [Azure AD DS에 대한 암호 동기화를 사용하도록 설정](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)합니다.

## <a name="complete-powershell-script"></a>전체 PowerShell 스크립트

다음 전체 PowerShell 스크립트는 이 문서에 나온 모든 작업을 결합한 것입니다. 이 스크립트를 복사하여 `.ps1` 확장명을 사용하는 파일로 저장합니다. 로컬 PowerShell 콘솔 또는 [Azure Cloud Shell][cloud-shell]에서 스크립트를 실행합니다.

> [!NOTE]
> Azure AD DS을 사용하도록 설정하려면 Azure AD 테넌트의 전역 관리자여야 합니다. 또한 Azure 구독에서 적어도 *기여자* 권한이 있어야 합니다.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

리소스를 만들고 PowerShell 프롬프트로 제어를 반환하는 데 몇 분 정도 걸립니다. 관리되는 도메인은 백그라운드에서 계속 프로비저닝되며 배포를 완료하는 데 최대 1시간이 걸릴 수 있습니다. Azure Portal의 관리되는 도메인 **개요** 페이지에는 이 배포 단계 전체에서 현재 상태가 표시됩니다.

관리되는 도메인이 프로비저닝을 마쳤다고 Azure Portal에 표시되면 다음 작업을 완료해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성하려면 포털에서 관리되는 도메인을 선택합니다. **개요** 창에 DNS 설정을 자동으로 구성하라는 메시지가 표시됩니다.
* 네트워크 보안 그룹을 만들어 관리되는 도메인에 대한 가상 네트워크의 트래픽을 제한합니다. 이러한 규칙을 적용하는 Azure 표준 부하 분산 장치가 생성됩니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호하며, 관리되는 도메인이 제대로 작동하는 데 꼭 필요합니다.
    * 네트워크 보안 그룹 및 필수 규칙을 만들려면 `Install-Script -Name New-AaddsNetworkSecurityGroup` 명령을 사용하여 `New-AzureAddsNetworkSecurityGroup` 스크립트를 설치한 다음, `New-AaddsNetworkSecurityGroup` 명령을 실행합니다. 관리되는 도메인의 필수 규칙이 자동으로 생성됩니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 [Azure AD DS에 대한 암호 동기화를 사용하도록 설정](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)합니다.

## <a name="next-steps"></a>다음 단계

관리되는 도메인이 작동하는 것을 보려면 [Windows VM을 도메인에 조인하고][windows-join], [보안 LDAP를 구성하고][tutorial-ldaps], [암호 해시 동기화를 구성][tutorial-phs]하면 됩니다.

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md

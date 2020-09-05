---
title: 템플릿을 사용하여 Azure DS Domain Services를 사용하도록 설정 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Azure Active Directory Domain Services를 구성하고 사용하도록 설정하는 방법 알아보기
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 316f77b2447bd75b03a05c4e6466d153bf51201d
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722639"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure Active Directory Domain Services 관리형 도메인 만들기

Azure AD DS(Azure Active Directory Domain Services)는 Windows Server Active Directory와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP, Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 이러한 도메인 서비스는 도메인 컨트롤러를 직접 배포, 관리 및 패치하지 않고 사용할 수 있습니다. Azure AD DS는 기존 Azure AD 테넌트와 통합됩니다. 이러한 통합을 통해 사용자는 회사 자격 증명을 사용하여 로그인할 수 있으며, 기존 그룹과 사용자 계정을 사용하여 리소스에 대한 액세스를 보호할 수 있습니다.

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 관리되는 도메인을 만드는 방법을 보여줍니다. 지원 리소스는 Azure PowerShell을 사용하여 생성됩니다.

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

## <a name="dns-naming-requirements"></a>DNS 명명 요구 사항

Azure AD DS 관리형 도메인을 만들 때 DNS 이름을 지정합니다. 이 DNS 이름을 선택할 때 고려해야 할 몇 가지 사항이 있습니다.

* **기본 제공 도메인 이름:** 디렉터리의 기본 제공 도메인 이름( *.onmicrosoft.com* 접미사)이 기본적으로 사용됩니다. 관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정하려면 이 기본 도메인과의 연결을 보호하기 위해 디지털 인증서를 만들 수 없습니다. Microsoft에서 *.onmicrosoft.com* 도메인을 소유하고 있으므로 CA(인증 기관)는 인증서를 발급하지 않습니다.
* **사용자 지정 도메인 이름:** 가장 일반적인 방법은 일반적으로 이미 소유하고 있고 라우팅할 수 있는 사용자 지정 도메인 이름을 지정하는 것입니다. 라우팅할 수 있는 사용자 지정 도메인을 사용하면 애플리케이션을 지원하는 데 필요한 트래픽이 올바르게 전달될 수 있습니다.
* **라우팅할 수 없는 도메인 접미사:** 일반적으로 라우팅할 수 없는 도메인 이름 접미사(예: *contoso.local*)를 사용하지 않는 것이 좋습니다. *.local* 접미사는 라우팅할 수 없으며, DNS 확인에서 문제가 발생할 수 있습니다.

> [!TIP]
> 사용자 지정 도메인 이름을 만드는 경우 기존 DNS 네임스페이스를 주의해야 합니다. 기존 Azure 또는 온-프레미스 DNS 네임스페이스와 별도의 도메인 이름을 사용하는 것이 좋습니다.
>
> 예를 들어 기존 DNS 네임스페이스가 *contoso.com*인 경우 사용자 지정 도메인 이름이 *aaddscontoso.com*인 관리되는 도메인을 만듭니다. 보안 LDAP을 사용해야 하는 경우 필요한 인증서를 생성하려면 이 사용자 지정 도메인 이름을 등록하고 소유해야 합니다.
>
> 환경의 다른 서비스에 대한 일부 추가 DNS 레코드를 만들거나 환경의 기존 DNS 네임스페이스 사이에 조건부 DNS 전달자를 만들어야 할 수 있습니다. 예를 들어 루트 DNS 이름을 사용 중인 사이트를 호스트하는 웹 서버를 실행하는 경우 추가 DNS 항목이 필요한 명명 충돌이 있을 수 있습니다.
>
> 이 샘플 및 방법 문서에서는 *aaddscontoso.com*의 사용자 지정 도메인이 간단한 예제로 사용됩니다. 모든 명령에서 사용자 고유의 도메인 이름을 지정하세요.

다음 DNS 이름 제한도 적용됩니다.

* **도메인 접두사 제한:** 접두사가 15자보다 긴 관리되는 도메인은 만들 수 없습니다. 지정한 도메인 이름의 접두사(예: *aaddscontoso.com* 도메인 이름의 *aaddscontoso*)는 15자 이하여야 합니다.
* **네트워크 이름 충돌:** 관리되는 도메인의 DNS 도메인 이름이 가상 네트워크에 존재하지 않아야 합니다. 특히 이름 충돌이 발생할 수 있는 다음 시나리오를 확인하세요.
    * Azure 가상 네트워크에 동일한 DNS 도메인 이름의 Active Directory 도메인이 이미 있는 경우
    * 관리되는 도메인을 사용하도록 설정하려는 가상 네트워크에 온-프레미스 네트워크와의 VPN 연결이 있는 경우. 이 시나리오에서는 온-프레미스 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 없는지 확인합니다.
    * Azure 가상 네트워크에 해당 이름의 기존 Azure 클라우드 서비스가 있는 경우

## <a name="create-required-azure-ad-resources"></a>필수 Azure AD 리소스 만들기

Azure AD DS에는 서비스 주체와 Azure AD 그룹이 필요합니다. 이러한 리소스를 통해 관리되는 도메인이 데이터를 동기화하고 관리되는 도메인에서 관리 권한이 있는 사용자를 정의할 수 있습니다.

먼저 [Register-AzResourceProvider][Register-AzResourceProvider] cmdlet을 사용하여 Azure AD Domain Services 리소스 공급자를 등록합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Azure AD DS가 통신하고 자체 인증할 수 있도록 [New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet을 사용하여 Azure AD 서비스 주체를 만듭니다. ID가 *2565bd9d-da50-47d4-8b85-4c97f669dc36*인 *도메인 컨트롤러 서비스*라는 특정 애플리케이션 ID가 사용됩니다. 이 애플리케이션 ID를 변경하지 마십시오.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

이제 [New-AzureADGroup][New-AzureADGroup] cmdlet을 사용하여 *AAD DC Administrators*라는 Azure AD 그룹을 만듭니다. 그러면 이 그룹에 추가된 사용자에게는 관리되는 도메인에서 관리 작업을 수행할 수 있는 권한이 부여됩니다.

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

마지막으로 [New-AzResourceGroup][New-AzResourceGroup] cmdlet을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹이 *westus* 지역에 만들어집니다. 자신의 이름과 원하는 지역을 사용합니다.

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

가용성 영역을 지원하는 지역을 선택하면 Azure AD DS 리소스가 추가 중복성을 위해 여러 영역에 배포됩니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다.

Azure AD DS를 영역 간에 배포하기 위해 구성해야 할 항목은 없습니다. Azure 플랫폼은 리소스의 영역 배포를 자동으로 처리합니다. 자세한 내용 및 지역 가용성을 확인하려면 [Azure에서 가용성 영역이란?][availability-zones]을 참조하세요.

## <a name="resource-definition-for-azure-ad-ds"></a>Azure AD DS에 대한 리소스 정의

Resource Manager 리소스 정의의 일환으로 다음 구성 매개 변수가 필요합니다.

| 매개 변수               | 값 |
|-------------------------|---------|
| domainName              | 이름 지정 접두사 및 충돌과 관련된 이전 사항을 고려한 관리되는 도메인의 DNS 도메인 이름입니다. |
| filteredSync            | Azure AD DS를 사용하면 Azure AD에서 사용할 수 있는 사용자와 그룹을 *모두* 동기화하거나 특정 그룹으로만 *범위가 지정된* 동기화를 수행할 수 있습니다.<br /><br /> 범위가 지정된 동기화에 대한 자세한 내용은 [Azure AD Domain Services 범위가 지정된 동기화][scoped-sync]를 참조하세요.|
| notificationSettings    | 관리되는 도메인에서 생성된 경고가 있으면 이메일 알림을 보낼 수 있습니다. <br /><br />Azure 테넌트의 전역 관리자와 *AAD DC Administrators* 그룹의 멤버에게는 이 알림을 *Enabled*로 설정할 수 있습니다.<br /><br /> 필요하면, 주의가 필요한 경고가 있을 때 알림을 받을 수신자를 추가할 수 있습니다.|
| domainConfigurationType | 기본적으로 관리되는 도메인은 *사용자* 포리스트로 생성됩니다. 이 유형의 포리스트는 온-프레미스 AD DS 환경에서 만든 모든 사용자 계정을 포함하여 Azure AD의 모든 개체를 동기화합니다. 사용자 포리스트를 만들기 위해 *domainConfiguration* 값을 지정할 필요가 없습니다.<br /><br /> *리소스* 포리스트는 Azure AD에서 직접 만든 사용자와 그룹만 동기화합니다. 값을 *ResourceTrusting*으로 설정하여 리소스 포리스트를 만듭니다.<br /><br />온-프레미스 AD DS 도메인을 사용하여 포리스트 트러스트를 만드는 방법 및 사용하는 이유를 비롯하여 *리소스* 포리스트에 대한 자세한 내용은 [Azure AD DS 리소스 포리스트 개요][resource-forests]를 참조하세요.|

다음 압축 매개 변수 정의는 이러한 값이 선언되는 방법을 보여줍니다. *aaddscontoso.com*이라는 사용자 포리스트가 생성되어 Azure AD의 모든 사용자가 관리되는 도메인에 동기화됩니다.

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

그러면 관리되는 도메인을 정의하고 만드는 데 다음의 압축 Resource Manager 템플릿 리소스 종류가 사용됩니다. Azure 가상 네트워크와 서브넷은 이미 존재하거나 Resource Manager 템플릿의 일부로 만들어져야 합니다. 관리되는 도메인이 이 서브넷에 연결되어 있습니다.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

이러한 매개 변수와 리소스 종류는 다음 섹션에 나와 있듯이 광범위한 Resource Manager 템플릿의 일부로 사용되어 관리되는 도메인을 배포할 수 있습니다.

## <a name="create-a-managed-domain-using-sample-template"></a>샘플 템플릿을 사용하여 관리되는 도메인 만들기

다음의 전체 Resource Manager 샘플 템플릿은 관리되는 도메인 및 지원 가상 네트워크, 서브넷 및 네트워크 보안 그룹 규칙을 만듭니다. 네트워크 보안 그룹 규칙은 관리되는 도메인을 보호하고 트래픽이 제대로 흐를 수 있도록 하는 데 필요합니다. DNS 이름이 *aaddscontoso.com*인 사용자 포리스트가 생성되고 모든 사용자가 Azure AD에서 동기화됩니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

이 템플릿은 원하는 배포 방법(예: [Azure Portal][portal-deploy], [Azure PowerShell][powershell-deploy] 또는 CI/CD 파이프라인)을 사용하여 배포할 수 있습니다. 다음 예제에서는 [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] cmdlet을 사용합니다. 원하는 리소스 그룹 이름과 템플릿 파일 이름을 지정합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

리소스를 만들고 PowerShell 프롬프트로 제어를 반환하는 데 몇 분 정도 걸립니다. 관리되는 도메인은 백그라운드에서 계속 프로비저닝되며 배포를 완료하는 데 최대 1시간이 걸릴 수 있습니다. Azure Portal의 관리되는 도메인 **개요** 페이지에는 이 배포 단계 전체에서 현재 상태가 표시됩니다.

관리되는 도메인이 프로비저닝을 마쳤다고 Azure Portal에 표시되면 다음 작업을 완료해야 합니다.

* 가상 머신이 도메인 가입 또는 인증을 위해 관리되는 도메인을 찾을 수 있도록 가상 네트워크에 대한 DNS 설정을 업데이트합니다.
    * DNS를 구성하려면 포털에서 관리되는 도메인을 선택합니다. **개요** 창에 DNS 설정을 자동으로 구성하라는 메시지가 표시됩니다.
* 최종 사용자가 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있도록 [Azure AD DS에 대한 암호 동기화를 사용하도록 설정](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)합니다.

## <a name="next-steps"></a>다음 단계

관리되는 도메인이 작동하는 것을 보려면 [Windows VM을 도메인에 조인하고][windows-join], [보안 LDAP를 구성하고][tutorial-ldaps], [암호 해시 동기화를 구성][tutorial-phs]하면 됩니다.

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

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
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
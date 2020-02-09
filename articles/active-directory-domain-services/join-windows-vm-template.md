---
title: 템플릿을 사용 하 여 Windows VM을 Azure AD DS에 가입 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용 하 여 새 또는 기존 Windows Server VM을 Azure Active Directory Domain Services 관리 되는 도메인에 가입 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: iainfou
ms.openlocfilehash: 7bf01eea71134d932305cce7665c68d4dcc655cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712562"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 Azure Active Directory Domain Services 관리 되는 도메인에 Windows Server 가상 머신 연결

Azure Vm (가상 머신)의 배포 및 구성을 자동화 하기 위해 리소스 관리자 템플릿을 사용할 수 있습니다. 이러한 템플릿을 사용 하면 매번 일관 된 배포를 만들 수 있습니다. 확장은 배포의 일부로 자동으로 VM을 구성 하기 위해 템플릿에 포함 될 수도 있습니다. 하나의 유용한 확장 프로그램은 Vm을 도메인에 가입 시킵니다 .이 도메인은 Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인과 함께 사용할 수 있습니다.

이 문서에서는 리소스 관리자 템플릿을 사용 하 여 Windows Server VM을 만들고 Azure AD DS 관리 되는 도메인에 가입 하는 방법을 보여 줍니다. 또한 Azure AD DS 도메인에 기존 Windows Server VM을 조인 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager 템플릿 개요

리소스 관리자 템플릿을 사용 하면 코드에서 Azure 인프라를 정의할 수 있습니다. 필수 리소스, 네트워크 연결 또는 Vm 구성은 모두 템플릿에 정의 될 수 있습니다. 이러한 템플릿은 매번 일관 되 고 재현 가능한 배포를 만들며 변경 시 버전을 지정할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿 개요][template-overview]를 참조 하세요.

각 리소스는 JavaScript Object Notation (JSON)를 사용 하 여 템플릿에 정의 됩니다. 다음 JSON 예제에서는 *virtualMachines/extensions* 리소스 유형을 사용 하 여 Active Directory 도메인 가입 확장을 설치 합니다. 매개 변수는 배포 시 지정 하는 데 사용 됩니다. 확장이 배포 되 면 VM이 지정 된 Azure AD DS 관리 되는 도메인에 가입 됩니다.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

동일한 템플릿에서 VM을 만들지 않은 경우에도이 VM 확장을 배포할 수 있습니다. 이 문서의 예에서는 다음 두 가지 방법을 모두 보여 줍니다.

* [Windows Server VM 만들기 및 관리 되는 도메인에 가입](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [기존 Windows Server VM을 관리 되는 도메인에 가입](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Windows Server VM 만들기 및 관리 되는 도메인에 가입

Windows Server VM이 필요한 경우 리소스 관리자 템플릿을 사용 하 여 VM을 만들고 구성할 수 있습니다. VM을 배포 하면 VM을 Azure AD DS 관리 되는 도메인에 가입 시키기 위해 확장이 설치 됩니다. Azure AD DS 관리 되는 도메인에 가입 하려는 VM이 이미 있는 경우 [기존 Windows SERVER VM을 관리 되는 도메인에 가입](#join-an-existing-windows-server-vm-to-a-managed-domain)으로 건너뜁니다.

Windows Server VM을 만들려면 Azure AD DS 관리 되는 도메인에 가입 하 고 다음 단계를 완료 합니다.

1. [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)으로 이동 합니다. **Azure에 배포**하는 옵션을 선택 합니다.
1. **사용자 지정 배포** 페이지에서 다음 정보를 입력 하 여 WINDOWS Server VM을 만들고 Azure AD DS 관리 되는 도메인에 가입 합니다.

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | Resource group            | VM에 대한 리소스 그룹을 선택 합니다. |
    | 위치                  | VM의 위치를 선택 합니다. |
    | 기존 VNET 이름        | VM을 연결할 기존 가상 네트워크의 이름 (예: *Myvnet*)입니다. |
    | 기존 서브넷 이름      | *작업 부하*와 같은 기존 가상 네트워크 서브넷의 이름입니다. |
    | DNS 레이블 접두사          | VM에 사용할 DNS 이름 (예: *myvm*)을 입력 합니다. |
    | VM 크기                   | *Standard_DS2_v2*와 같은 VM 크기를 지정 합니다. |
    | 가입할 도메인            | Azure AD DS 관리 되는 도메인 DNS 이름 (예: *aadds.contoso.com*)입니다. |
    | 도메인 사용자 이름           | `contosoadmin@aadds.contoso.com`와 같이 VM을 관리 되는 도메인에 연결 하는 데 사용 해야 하는 Azure AD DS 관리 되는 도메인의 사용자 계정. 이 계정은 *AZURE AD DC 관리자* 그룹의 구성원 이어야 합니다. |
    | 도메인 암호           | 이전 설정에 지정 된 사용자 계정의 암호입니다. |
    | 선택적 OU 경로          | VM을 추가할 사용자 지정 OU입니다. 이 매개 변수에 대한 값을 지정 하지 않으면 VM이 기본 *AAD DC 컴퓨터* OU에 추가 됩니다. |
    | VM 관리자 사용자 이름         | VM에서 만들 로컬 관리자 계정을 지정 합니다. |
    | VM 관리자 암호         | VM의 로컬 관리자 암호를 지정 합니다. 암호 무차별 암호 대입 공격 으로부터 보호 하기 위한 강력한 로컬 관리자 암호를 만듭니다. |

1. 사용 약관을 검토 한 다음 **위에 명시 된 사용 약관에 동의 함**확인란을 선택 합니다. 준비가 되 면 **구매** 를 선택 하 여 VM을 만들고 Azure AD DS 관리 되는 도메인에 가입 합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일은 *AZURE AD DC administrators* 그룹의 구성원 인 사용자 계정에 대한 암호를 요청 합니다. 이 파일에 값을 수동으로 입력 하지 말고 파일 공유 또는 다른 공유 위치에서 값에 액세스할 수 있도록 유지 합니다.

배포가 성공적으로 완료 되는 데 몇 분 정도 걸립니다. 완료 되 면 Windows VM이 만들어지고 Azure AD DS 관리 되는 도메인에 가입 됩니다. 도메인 계정을 사용 하 여 VM을 관리 하거나 로그인 할 수 있습니다.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>관리되는 도메인에 기존 Windows Server VM 조인

Azure AD DS 관리 되는 도메인에 가입 하려는 기존 VM 또는 vm 그룹이 있는 경우 리소스 관리자 템플릿을 사용 하 여 VM 확장만 배포할 수 있습니다.

기존 Windows Server VM을 Azure AD DS 관리 되는 도메인에 연결 하려면 다음 단계를 완료 합니다.

1. [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)으로 이동 합니다. **Azure에 배포**하는 옵션을 선택 합니다.
1. **사용자 지정 배포** 페이지에서 다음 정보를 입력 하 여 VM을 Azure AD DS 관리 되는 도메인에 가입 합니다.

    | 설정                   | 값 |
    |---------------------------|-------|
    | Subscription              | Azure AD Domain Services를 사용하도록 설정한 동일한 Azure 구독을 선택합니다. |
    | Resource group            | 기존 VM을 사용 하 여 리소스 그룹을 선택 합니다. |
    | 위치                  | 기존 VM의 위치를 선택 합니다. |
    | VM 목록                   | *MyVM1, myVM2*와 같이 Azure AD DS 관리 되는 도메인에 조인할 기존 VM의 쉼표로 구분 된 목록을 입력 합니다. |
    | 도메인 가입 사용자 이름     | `contosoadmin@aadds.contoso.com`와 같이 VM을 관리 되는 도메인에 연결 하는 데 사용 해야 하는 Azure AD DS 관리 되는 도메인의 사용자 계정. 이 계정은 *AZURE AD DC 관리자* 그룹의 구성원 이어야 합니다. |
    | 도메인 가입 사용자 암호 | 이전 설정에 지정 된 사용자 계정의 암호입니다. |
    | 선택적 OU 경로          | VM을 추가할 사용자 지정 OU입니다. 이 매개 변수에 대한 값을 지정 하지 않으면 VM이 기본 *AAD DC 컴퓨터* OU에 추가 됩니다. |

1. 사용 약관을 검토 한 다음 **위에 명시 된 사용 약관에 동의 함**확인란을 선택 합니다. 준비가 되 면 **구매** 를 선택 하 여 VM을 Azure AD DS 관리 되는 도메인에 가입 합니다.

> [!WARNING]
> **암호 처리에 주의하세요.**
> 템플릿 매개 변수 파일은 *AZURE AD DC administrators* 그룹의 구성원 인 사용자 계정에 대한 암호를 요청 합니다. 이 파일에 값을 수동으로 입력 하지 말고 파일 공유 또는 다른 공유 위치에서 값에 액세스할 수 있도록 유지 합니다.

배포가 성공적으로 완료 되는 데 몇 분 정도 걸립니다. 완료 되 면 지정 된 Windows Vm은 Azure AD DS 관리 되는 도메인에 가입 되며 도메인 계정을 사용 하 여 관리 하거나 로그인 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 템플릿을 사용 하 여 리소스를 구성 하 고 배포 하는 Azure Portal을 사용 했습니다. [Azure PowerShell][deploy-powershell] 또는 [Azure CLI][deploy-cli]를 사용 하 여 리소스 관리자 템플릿으로 리소스를 배포할 수도 있습니다.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md

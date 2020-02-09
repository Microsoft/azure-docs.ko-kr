---
title: Azure AD Domain Services에 대한 그룹 관리 서비스 계정 | Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인에서 사용할 그룹 관리 서비스 계정 (gMSA)을 만드는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 9dc7e6341f77fc17ae26f34ea029b3eb5414dcbc
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705310"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Azure AD Domain Services에서 그룹 관리 서비스 계정 (gMSA) 만들기

응용 프로그램 및 서비스는 다른 리소스를 사용 하 여 자신을 인증 하기 위해 id가 필요 하기도 합니다. 예를 들어 웹 서비스는 데이터베이스 서비스를 사용 하 여 인증 해야 할 수 있습니다. 응용 프로그램 또는 서비스에 웹 서버 팜과 같은 여러 인스턴스가 있는 경우 해당 리소스에 대한 id를 수동으로 만들고 구성 하면 시간이 많이 걸립니다.

대신 gMSA (그룹 관리 서비스 계정)를 Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인에서 만들 수 있습니다. Windows OS는 gMSA에 대한 자격 증명을 자동으로 관리 하 여 많은 리소스 그룹의 관리를 간소화 합니다.

이 문서에서는 Azure PowerShell를 사용 하 여 Azure AD DS 관리 되는 도메인에 gMSA를 만드는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 다음 리소스와 권한이 필요 합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant] [Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료 하 여 [Azure Active Directory Domain Services 인스턴스를 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 관리 되는 도메인에 가입 된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료 하 여 [관리 VM을 만듭니다][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>관리 서비스 계정 개요

SMSA (독립 실행형 관리 서비스 계정)는 암호가 자동으로 관리 되는 도메인 계정입니다. 이 방법은 SPN (서비스 사용자 이름) 관리를 간소화 하 고 다른 관리자에 게 위임 된 관리를 사용 하도록 설정 합니다. 계정에 대한 자격 증명을 수동으로 만들고 회전할 필요가 없습니다.

GMSA (그룹 관리 서비스 계정)는 동일한 관리 간소화를 제공 하지만 도메인의 여러 서버에 대해서는 동일 합니다. GMSA를 사용 하면 서버 팜에서 호스트 되는 서비스의 모든 인스턴스가 상호 인증 프로토콜에 동일한 서비스 주체를 사용 하 여 작업을 수행할 수 있습니다. GMSA를 서비스 주체로 사용 하면 Windows 운영 체제에서 관리자에 의존 하지 않고 계정의 암호를 다시 관리 합니다.

자세한 내용은 [그룹 관리 서비스 계정 (gMSA) 개요][gmsa-overview]를 참조 하세요.

## <a name="using-service-accounts-in-azure-ad-ds"></a>Azure AD DS에서 서비스 계정 사용

Azure AD DS 관리 되는 도메인은 Microsoft에서 잠그고 관리할 수 있으므로 서비스 계정을 사용할 때는 다음과 같은 몇 가지 사항을 고려해 야 합니다.

* 관리 되는 도메인의 사용자 지정 OU (조직 구성 단위)에 서비스 계정을 만듭니다.
    * 기본 제공 *Aaddc 사용자* 또는 *Aaddc 컴퓨터* ou에서 서비스 계정을 만들 수 없습니다.
    * 대신 Azure AD DS 관리 되는 도메인에서 [사용자 지정 ou를 만든][create-custom-ou] 다음 해당 사용자 지정 ou에 서비스 계정을 만듭니다.
* 키 배포 서비스 (KDS) 루트 키가 미리 생성 됩니다.
    * KDS 루트 키는 gMSAs에 대한 암호를 생성 하 고 검색 하는 데 사용 됩니다. Azure AD DS에서 KDS root가 만들어집니다.
    * 다른 사용자를 만들 수 있는 권한이 없거나 기본 KDS 루트 키를 볼 수 있는 권한이 없습니다.

## <a name="create-a-gmsa"></a>GMSA 만들기

먼저 [ADOrganizationalUnit][New-AdOrganizationalUnit] cmdlet을 사용 하 여 사용자 지정 OU를 만듭니다. 사용자 지정 Ou를 만들고 관리 하는 방법에 대한 자세한 내용은 [Azure AD DS의 사용자 지정 ou][create-custom-ou]를 참조 하세요.

> [!TIP]
> 이러한 단계를 완료 하 여 gMSA를 만들려면 [관리 VM을 사용][tutorial-create-management-vm]합니다. 이 관리 VM에는 필수 AD PowerShell cmdlet 및 관리 되는 도메인에 대한 연결이 이미 있어야 합니다.

다음 예제에서는 *aadds.contoso.com*이라는 Azure AD DS 관리 되는 도메인에 *myNewOU* 이라는 사용자 지정 OU를 만듭니다. 사용자 고유의 OU 및 관리 되는 도메인 이름 사용:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

이제 [uninstall-adserviceaccount][New-ADServiceAccount] cmdlet을 사용 하 여 gMSA를 만듭니다. 다음 예제 매개 변수가 정의 되어 있습니다.

* **-Name** 은 *WebFarmSvc* 로 설정 됩니다.
* **-Path** 매개 변수는 이전 단계에서 만든 gMSA에 대한 사용자 지정 OU를 지정 합니다.
* *WebFarmSvc.aadds.contoso.com* 에 대한 DNS 항목 및 서비스 주체 이름 설정
* *CONTOSO-SERVER $* 의 보안 주체는 id를 사용 하 여 암호를 검색할 수 있습니다.

고유한 이름과 도메인 이름을 지정 합니다.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aadds.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aadds.contoso.com/aadds.contoso.com, `
        http/WebFarmSvc.aadds.contoso.com/contoso, `
        http/WebFarmSvc/aadds.contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

이제 필요에 따라 gMSA를 사용 하도록 응용 프로그램 및 서비스를 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

GMSAs에 대한 자세한 내용은 [그룹 관리 서비스 계정 시작][gmsa-start]을 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts

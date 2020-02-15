---
title: Azure AD Domain Services에 암호 해시 동기화 사용 | Microsoft Docs
description: 이 자습서에서는 Azure AD Connect를 사용하여 Azure Active Directory Domain Services 관리형 도메인에 암호 해시 동기화를 사용하도록 설정하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: deca7477c79fd2952bb57c0194202c382cd5279d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132226"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>자습서: 하이브리드 환경을 위해 Azure Active Directory Domain Services에서 암호 동기화 활성화

하이브리드 환경의 경우 Azure AD Connect를 사용하여 온-프레미스 AD DS(Active Directory Domain Services) 환경과 동기화하도록 Azure AD(Azure Active Directory) 테넌트를 구성할 수 있습니다. 기본적으로 Azure AD Connect는 Azure AD DS(Azure Active Directory Domain Services)에 필요한 레거시 NTLM(NT LAN Manager)과 Kerberos 암호 해시를 동기화하지 않습니다.

온-프레미스 AD DS 환경에서 동기화된 계정으로 Azure AD DS를 사용하려면, NTLM 및 Kerberos 인증에 필요한 암호 해시를 동기화하도록 Azure AD Connect를 구성해야 합니다. Azure AD Connect가 구성된 후에는, 온-프레미스 계정 생성 또는 암호 변경 이벤트도 레거시 암호 해시를 Azure AD와 동기화합니다.

온-프레미스 AD DS 환경이 없는 클라우드 전용 계정을 사용하는 경우에는 이러한 단계를 수행할 필요가 없습니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * 레거시 NTLM 및 Kerberos 암호 해시가 필요한 이유
> * Azure AD Connect에 대한 레거시 암호 해시 동기화를 구성하는 방법

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스는 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure AD Connect를 사용하여 온-프레미스 디렉터리와 동기화된 구독과 연결된 Azure Active Directory 테넌트.
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
    * 필요한 경우 [암호 해시 동기화를 위해 Azure AD Connect를 사용하도록 설정][enable-azure-ad-connect]합니다.
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Azure AD Connect를 사용하여 암호 해시 동기화

Azure AD Connect는 온-프레미스 AD DS 환경의 사용자 계정 및 그룹과 같은 개체를 Azure AD 테넌트로 동기화하는 데 사용됩니다. 프로세스의 일부로, 암호 해시 동기화를 통해 온-프레미스 AD DS 환경과 Azure AD에서 계정이 동일한 암호를 사용할 수 있습니다.

관리되는 도메인에서 사용자를 인증하려면 Azure AD DS에 NTLM 및 Kerberos 인증에 적합한 형식의 암호 해시가 필요합니다. Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

Azure AD DS에 필요한 NTLM 또는 Kerberos 암호 해시를 동기화하도록 Azure AD Connect를 구성할 수 있습니다. [암호 해시 동기화를 위해 Azure AD Connect를 사용하도록 설정][enable-azure-ad-connect]하는 단계를 완료했는지 확인합니다. 기존 Azure AD Connect 인스턴스가 있는 경우 [최신 버전을 다운로드하고 업데이트][azure-ad-connect-download]하여 NTLM 및 Kerberos에 대한 레거시 암호 해시를 동기화할 수 있도록 합니다. 이 기능은 Azure AD Connect의 초기 릴리스 또는 레거시 DirSync 도구에서 사용할 수 없습니다. Azure AD Connect 버전 *1.1.614.0* 이상이 필요합니다.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 Azure AD DS 관리형 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

## <a name="enable-synchronization-of-password-hashes"></a>암호 해시 동기화 사용

Azure AD Connect를 설치하고 Azure AD와 동기화하도록 구성된 상태에서, NTLM 및 Kerberos에 대한 레거시 암호 해시 동기화를 구성합니다. 필요한 설정을 구성한 다음, Azure AD에 대한 전체 암호 동기화를 시작하는 데 PowerShell 스크립트가 사용됩니다. Azure AD Connect 암호 해시 동기화 프로세스가 완료되면 사용자는 레거시 NTLM 또는 Kerberos 암호 해시를 사용하는 Azure AD DS를 통해 애플리케이션에 로그인할 수 있습니다.

1. Azure AD Connect가 설치된 컴퓨터의 시작 메뉴에서 **Azure AD Connect> 동기화 서비스**를 엽니다.
1. **커넥터** 탭을 선택합니다. 온-프레미스 AD DS 환경과 Azure AD 간의 동기화를 설정하는 데 사용된 연결 정보가 나열됩니다.

    **형식**은 Azure AD 커넥터용 *Windows Azure Active Directory(Microsoft)* 또는 온-프레미스 AD DS 커넥터용 *Active Directory Domain Services*를 나타냅니다. 다음 단계에서 PowerShell 스크립트에 사용할 커넥터 이름을 적어 둡니다.

    ![Sync Service Manager에서 커넥터 이름 나열](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    이 예제 스크린샷에서는 다음 커넥터가 사용됩니다.

    * *contoso.onmicrosoft.com - AAD*라는 Azure AD 커넥터
    * *onprem.contoso.com*이라는 온-프레미스 AD DS 커넥터

1. 다음 PowerShell 스크립트를 복사하여 Azure AD Connect가 설치된 컴퓨터에 붙여넣습니다. 이 스크립트는 레거시 암호 해시가 포함된 전체 암호 동기화를 트리거합니다. `$azureadConnector` 및 `$adConnector` 변수를 이전 단계의 커넥터 이름으로 업데이트합니다.

    각 AD 포리스트에서 이 스크립트를 실행하여 온-프레미스 계정 NTLM 및 Kerberos 암호 해시를 Azure AD에 동기화합니다.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    계정 및 그룹 수에 따른 디렉터리 크기에 따라 레거시 암호 해시를 Azure AD에 동기화하는 데 시간이 걸릴 수 있습니다. 그런 다음, 암호가 Azure AD에 동기화된 후 Azure AD DS 관리형 도메인에 동기화됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

> [!div class="checklist"]
> * 레거시 NTLM 및 Kerberos 암호 해시가 필요한 이유
> * Azure AD Connect에 대한 레거시 암호 해시 동기화를 구성하는 방법

> [!div class="nextstepaction"]
> [Azure AD Domain Services 관리형 도메인의 동기화 작동 방식 알아보기](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594

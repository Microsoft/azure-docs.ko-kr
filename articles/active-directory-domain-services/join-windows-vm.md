---
title: Windows Server VM을 Azure AD Domain Services 관리되는 도메인에 조인 | Microsoft Docs
description: 이 자습서에서는 Windows Server 가상 머신을 Azure Active Directory Domain Services 관리형 도메인에 조인하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 259b27528779c9934da6d69ca46f2794e2257d24
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723030"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>자습서: Windows Server 가상 머신을 Azure Active Directory Domain Services 관리되는 도메인에 조인

Azure AD DS(Azure Active Directory Domain Services)는 Windows Server Active Directory와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP, Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. Azure AD DS 관리형 도메인을 사용하면 도메인 조인 및 관리 기능을 Azure의 VM(가상 머신)에 제공할 수 있습니다. 이 자습서에서는 Windows Server VM을 만든 다음, 관리되는 도메인에 조인하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Windows Server VM 만들기
> * Azure 가상 네트워크에 Windows Server VM 연결
> * VM을 관리되는 도메인에 조인

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스는 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* 관리되는 도메인의 일부인 사용자 계정
    * 계정에서 관리되는 도메인에 로그인할 수 있도록 Azure AD Connect 암호 해시 동기화 또는 셀프 서비스 암호 재설정이 수행되었는지 확인합니다.
* Azure AD DS 가상 네트워크에 배포된 Azure Bastion 호스트
    * 필요한 경우 [Azure Bastion 호스트를 만듭니다][azure-bastion].

도메인에 조인하려는 VM이 이미 있는 경우 [VM을 관리되는 도메인에 조인](#join-the-vm-to-the-managed-domain) 섹션으로 건너뜁니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 관리되는 도메인에 조인할 Windows Server VM을 만듭니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-windows-server-virtual-machine"></a>Windows Server 가상 머신 만들기

컴퓨터를 관리되는 도메인에 조인하는 방법을 알아보기 위해 Windows Server VM을 만들어 보겠습니다. 이 VM은 관리되는 도메인에 대한 연결을 제공하는 Azure 가상 네트워크에 연결됩니다. 관리되는 도메인에 조인하는 프로세스는 일반 온-프레미스 Active Directory Domain Services 도메인에 조인하는 것과 동일합니다.

도메인에 조인하려는 VM이 이미 있는 경우 [VM을 관리되는 도메인에 조인](#join-the-vm-to-the-managed-domain) 섹션으로 건너뜁니다.

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.

1. **시작**에서 **Windows Server 2016 Datacenter**를 선택합니다.

    ![Azure Portal에서 Windows Server 2016 Datacenter VM을 만들도록 선택](./media/join-windows-vm/select-vm-image.png)

1. **기본 사항** 창에서 가상 머신의 핵심 설정을 구성합니다. *가용성 옵션*, *이미지* 및 *크기*에 대한 기본값은 그대로 둡니다.

    | 매개 변수            | 제안 값   |
    |----------------------|-------------------|
    | Resource group       | 리소스 그룹(예: *myResourceGroup*)을 선택하거나 만듭니다. |
    | 가상 머신 이름 | VM 이름(예: *myVM*)을 입력합니다. |
    | 지역               | VM을 만들 지역(예: *미국 동부*)을 선택합니다. |
    | 사용자 이름             | VM에 만들 로컬 관리자 계정의 사용자 이름(예: *azureuser*)을 입력합니다. |
    | 암호             | 로컬 관리자가 VM에 만들 보안 암호를 입력하고 확인합니다. 도메인 사용자 계정의 자격 증명을 지정하지 마세요. |

1. 기본적으로 Azure에서 만든 VM은 RDP를 사용하여 인터넷에서 액세스할 수 없습니다. RDP를 사용하도록 설정하면 자동 로그인 공격이 발생할 수 있으며, 이로 인해 여러 번의 연속 로그인 시도가 실패하여 *admin* 또는 *administrator*와 같은 일반 이름의 계정을 사용하지 않도록 설정될 수 있습니다.

    RDP는 필요한 경우에만 사용하도록 설정하고 권한 있는 IP 범위 세트로 제한해야 합니다. 이 구성은 VM의 보안을 향상시키고 잠재적인 공격 영역을 줄이는 데 도움이 됩니다. 또는 TLS를 통해 Azure Portal에서만 액세스할 수 있는 Azure Bastion 호스트를 만들고 사용합니다. 이 자습서의 다음 단계에서는 Azure Bastion 호스트를 사용하여 VM에 안전하게 연결합니다.

    **퍼블릭 인바운드 포트** 아래에서 *없음*을 선택합니다.

1. 완료되면 **다음: 디스크**를 선택합니다.
1. **OS 디스크 유형**의 드롭다운 메뉴에서 *표준 SSD*, **다음: 네트워킹**을 차례로 선택합니다.
1. VM은 관리되는 도메인이 배포된 서브넷과 통신할 수 있는 Azure 가상 네트워크 서브넷에 연결되어야 합니다. 관리되는 도메인은 자체 전용 서브넷에 배포하는 것이 좋습니다. VM을 관리되는 도메인과 동일한 서브넷에 배포하지 마세요.

    VM을 배포하고 적절한 가상 네트워크 서브넷에 연결하는 두 가지 주요 방법이 있습니다.
    
    * 관리되는 도메인이 배포된 것과 동일한 가상 네트워크에서 새 서브넷을 만들거나 기존 서브넷을 선택합니다.
    * [Azure 가상 네트워크 피어링][vnet-peering]을 사용하여 연결된 Azure 가상 네트워크의 서브넷을 선택합니다.
    
    관리되는 도메인의 서브넷에 연결되지 않은 가상 네트워크 서브넷을 선택하면 VM을 관리되는 도메인에 조인할 수 없습니다. 이 자습서에서는 새 서브넷을 Azure 가상 네트워크에 만들어 보겠습니다.

    **네트워킹** 창에서 관리되는 도메인이 배포된 가상 네트워크(예: *aaads-vnet*)를 선택합니다.
1. 이 예제에서는 관리되는 도메인이 연결된 기존 *aaads-subnet*을 보여 줍니다. VM을 이 서브넷에 연결하지 마세요. VM에 대한 서브넷을 만들려면 **서브넷 구성 관리**를 선택합니다.

    ![Azure Portal에서 서브넷 구성을 관리하도록 선택](./media/join-windows-vm/manage-subnet.png)

1. 가상 네트워크 창의 왼쪽 메뉴에서 **주소 공간**을 선택합니다. 가상 네트워크는 기본 서브넷에서 사용하는 *10.0.2.0/24*의 단일 주소 공간을 사용하여 만들어집니다. *워크로드* 또는 Azure Bastion과 같은 다른 서브넷이 이미 있을 수도 있습니다.

    추가 IP 주소 범위를 가상 네트워크에 추가합니다. 이 주소 범위의 크기와 사용할 실제 IP 주소 범위는 이미 배포된 다른 네트워크 리소스에 따라 달라집니다. IP 주소 범위는 Azure 또는 온-프레미스 환경의 기존 주소 범위와 겹치지 않아야 합니다. 서브넷에 배포하는 데 필요한 VM의 수에 맞게 IP 주소 범위를 충분히 크게 조정해야 합니다.

    다음 예제에서는 *10.0.5.0/24*의 IP 주소 범위가 추가되었습니다. 준비되면 **저장**을 선택합니다.

    ![Azure Portal에서 추가 가상 네트워크 IP 주소 범위를 추가합니다.](./media/join-windows-vm/add-vnet-address-range.png)

1. 다음으로, 가상 네트워크 창의 왼쪽 메뉴에서 **서브넷**을 선택한 다음, **+ 서브넷**을 선택하여 서브넷을 추가합니다.

1. **+ 서브넷**을 선택한 다음, 서브넷 이름(예: *management*)을 입력합니다. **주소 범위(CIDR 블록)** (예: *10.0.5.0/24*)를 제공합니다. 이 IP 주소 범위가 기존의 다른 Azure 또는 온-프레미스 주소 범위와 겹치지 않도록 합니다. 다른 옵션은 해당 기본값으로 둔 다음, **확인**을 선택합니다.

    ![Azure Portal에서 서브넷 구성 만들기](./media/join-windows-vm/create-subnet.png)

1. 서브넷을 만드는 데 몇 초 정도 걸립니다. 서브넷이 만들어지면 *X*를 선택하여 서브넷 창을 닫습니다.
1. **네트워킹** 창으로 돌아가서 VM을 만들고, 드롭다운 메뉴에서 만든 서브넷(예: *management*)을 선택합니다. 다시 한 번 올바른 서브넷을 선택했는지 확인하고, 관리되는 도메인과 동일한 서브넷에 VM을 배포하지 않도록 합니다.
1. **공용 IP**의 경우 드롭다운 메뉴에서 *없음*을 선택합니다. 이 자습서에서는 Azure Bastion을 사용하여 관리에 연결하므로, VM에 할당된 공용 IP 주소가 필요하지 않습니다.
1. 다른 옵션은 해당 기본값으로 둔 다음, **관리**를 선택합니다.
1. **부트 진단**을 *끄기*로 설정합니다. 다른 옵션은 해당 기본값으로 둔 다음, **검토 + 만들기**를 선택합니다.
1. VM 설정을 검토한 다음, **만들기**를 선택합니다.

VM을 만드는 데 몇 분이 걸립니다. Azure Portal에서 배포 상태를 보여 줍니다. VM이 준비되면 **리소스로 이동**을 선택합니다.

![성공적으로 만들어지면 Azure Portal에서 해당 VM 리소스로 이동](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Windows Server VM에 연결

VM에 안전하게 연결하려면 Azure Bastion 호스트를 사용합니다. Azure Bastion을 사용하면 관리 호스트가 가상 네트워크에 배포되고, VM에 대한 웹 기반 RDP 또는 SSH 연결을 제공합니다. VM에는 공용 IP 주소가 필요하지 않으며, 외부 원격 트래픽에 대한 네트워크 보안 그룹 규칙을 열 필요가 없습니다. 웹 브라우저에서 Azure Portal을 사용하여 VM에 연결합니다. 필요한 경우 [Azure Bastion 호스트를 만듭니다][azure-bastion].

Bastion 호스트를 사용하여 VM에 연결하려면 다음 단계를 완료합니다.

1. VM에 대한 **개요** 창에서 **연결**, **Bastion**을 차례로 선택합니다.

    ![Azure Portal에서 Bastion을 사용하여 Windows 가상 머신에 연결](./media/join-windows-vm/connect-to-vm.png)

1. 이전 섹션에서 지정한 VM의 자격 증명을 입력한 다음, **연결**을 선택합니다.

   ![Azure Portal에서 Bastion 호스트를 통해 연결](./media/join-windows-vm/connect-to-bastion.png)

필요한 경우 웹 브라우저에서 Bastion 연결을 표시할 팝업을 열도록 허용합니다. VM에 연결하는 데 몇 초 정도 걸립니다.

## <a name="join-the-vm-to-the-managed-domain"></a>VM을 관리되는 도메인에 조인

Azure Bastion을 사용하여 VM이 만들어지고 웹 기반 RDP 연결이 설정되었으면 이제 Windows Server 가상 머신을 관리되는 도메인에 조인해 보겠습니다. 이 프로세스는 컴퓨터에서 일반 온-프레미스 Active Directory Domain Services 도메인에 연결하는 것과 동일합니다.

1. VM에 로그인할 때 **서버 관리자**가 기본적으로 열리지 않는 경우 **시작** 메뉴를 선택한 다음, **서버 관리자**를 선택합니다.
1. **서버 관리자** 창의 왼쪽 창에서 **로컬 서버**를 클릭합니다. 오른쪽 창의 **속성** 아래에서 **작업 그룹**을 선택합니다.

    ![VM에서 서버 관리자를 열고 작업 그룹 속성 편집](./media/join-windows-vm/server-manager.png)

1. **시스템 속성** 창에서 **변경**을 선택하여 관리되는 도메인에 조인합니다.

    ![작업 그룹 또는 도메인 속성을 변경하도록 선택](./media/join-windows-vm/change-domain.png)

1. **도메인** 상자에서 관리되는 도메인의 이름(예: *aaddscontoso.com*)을 지정한 다음, **확인**을 선택합니다.

    ![조인할 관리되는 도메인 지정](./media/join-windows-vm/join-domain.png)

1. 도메인에 조인할 도메인 자격 증명을 입력합니다. 관리되는 도메인에 속하는 사용자의 자격 증명을 제공합니다. 계정은 관리되는 도메인 또는 Azure AD 테넌트의 일부여야 합니다. 즉, Azure AD 테넌트와 연결된 외부 디렉터리의 계정은 도메인 조인 프로세스 중에 올바르게 인증되지 않습니다.

    계정 자격 증명은 다음 방법 중 하나로 지정할 수 있습니다.

    * **UPN 형식**(추천) - Azure AD에 구성된 대로 사용자 계정의 UPN(사용자 계정 이름) 접미사를 입력합니다. 예를 들어 *contosoadmin* 사용자의 UPN 접미사는 `contosoadmin@aaddscontoso.onmicrosoft.com`입니다. UPN 형식을 사용하여 *SAMAccountName* 형식이 아닌 도메인에 안정적으로 로그인할 수 있는 몇 가지 일반적인 사용 사례는 다음과 같습니다.
        * 사용자의 UPN 접두사(예: *deehasareallylongname*)가 긴 경우 *SAMAccountName*이 자동으로 생성될 수 있습니다.
        * Azure AD 테넌트에서 여러 사용자가 동일한 UPN 접두사(예: *dee*)를 사용하는 경우 해당 *SAMAccountName* 형식이 자동으로 생성될 수 있습니다.
    * **SAMAccountName 형식** - 계정 이름을 *SAMAccountName* 형식으로 입력합니다. 예를 들어 사용자 *contosoadmin*의 *SAMAccountName*은 `AADDSCONTOSO\contosoadmin`입니다.

1. 관리되는 도메인에 조인하는 데 몇 초 정도 걸립니다. 완료되면 도메인 조인을 환영하는 다음 메시지가 표시됩니다.

    ![도메인 시작](./media/join-windows-vm/join-domain-successful.png)

    **확인** 을 선택하여 계속합니다.

1. 관리되는 도메인에 조인하는 프로세스를 완료하려면 VM을 다시 시작합니다.

> [!TIP]
> PowerShell에서 [Add-Computer][add-computer] cmdlet을 사용하여 VM을 도메인에 조인할 수 있습니다. 다음 예제에서는 *AADDSCONTOSO* 도메인에 조인한 다음, VM을 다시 시작합니다. 메시지가 표시되면 관리되는 도메인의 일부인 사용자에 대한 자격 증명을 입력합니다.
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> VM에 연결하지 않고 연결을 수동으로 구성하지 않고 VM을 도메인에 조인하려면 [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell cmdlet을 사용할 수도 있습니다.

Windows Server VM이 다시 시작되면 관리되는 도메인에 적용된 모든 정책이 VM으로 푸시됩니다. 이제 적절한 도메인 자격 증명을 사용하여 Windows Server VM에 로그인할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서에서는 이 Windows Server VM을 사용하여 관리되는 도메인을 관리할 수 있는 관리 도구를 설치합니다. 이 자습서 시리즈를 계속 진행하지 않으려면 다음 정리 단계를 검토하여 [VM을 삭제](#delete-the-vm)합니다. 그렇지 않으면 [다음 자습서로 계속 진행](#next-steps)합니다.

### <a name="unjoin-the-vm-from-the-managed-domain"></a>관리되는 도메인에서 VM 가입 취소

관리되는 도메인에서 VM을 제거하려면 단계를 다시 수행하여 [VM을 도메인에 조인](#join-the-vm-to-the-managed-domain)합니다. 관리되는 도메인에 조인하는 대신 기본 *WORKGROUP*과 같은 작업 그룹에 조인하도록 선택합니다. VM을 다시 부팅한 후에는 관리되는 도메인에서 컴퓨터 개체가 제거됩니다.

도메인에서 조인 해제를 수행하지 않고 [VM을 삭제](#delete-the-vm)하는 경우 분리된 컴퓨터 개체가 Azure AD DS에 남아 있습니다.

### <a name="delete-the-vm"></a>VM 삭제

이 Windows Server VM을 사용하지 않을 경우 다음 단계를 사용하여 해당 VM을 삭제합니다.

1. 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
1. 리소스 그룹(예: *myResourceGroup*)을 선택합니다.
1. VM(예: *myVM*), **삭제**를 차례로 선택합니다. **예**를 선택하여 리소스 삭제를 확인합니다. VM을 삭제하는 데 몇 분 정도 걸립니다.
1. VM이 삭제되면 OS 디스크, 네트워크 인터페이스 카드 및 *myVM-* 접두사가 붙은 다른 모든 리소스를 선택하여 모두 삭제합니다.

## <a name="troubleshoot-domain-join-issues"></a>도메인 조인 문제 해결

Windows Server VM은 일반 온-프레미스 컴퓨터에서 Active Directory Domain Services 도메인에 조인하는 것과 동일한 방식으로 관리되는 도메인에 성공적으로 조인해야 합니다. Windows Server VM에서 관리되는 도메인에 조인할 수 없으면 연결 또는 자격 증명 관련 문제가 있음을 나타냅니다. 다음 문제 해결 섹션을 검토하여 관리형 도메인에 성공적으로 조인합니다.

### <a name="connectivity-issues"></a>연결 문제

도메인에 조인할 자격 증명을 묻는 메시지가 표시되지 않으면 연결 문제가 있는 것입니다. VM에서 가상 네트워크의 관리되는 도메인에 연결할 수 없습니다.

다음 문제 해결 단계를 각각 수행한 후에 Windows Server VM을 다시 관리형 도메인에 조인시킵니다.

* VM이 Azure AD DS가 사용되도록 설정되었거나 피어링된 네트워크 연결이 있는 것과 동일한 가상 네트워크에 연결되어 있는지 확인합니다.
* 관리형 도메인의 DNS 도메인 이름(예: `ping aaddscontoso.com`)에 대해 ping을 시도합니다.
    * ping 요청이 실패하면 관리형 도메인의 IP 주소(예: `ping 10.0.0.4`)에 대해 ping을 시도합니다. Azure 리소스 목록에서 관리되는 도메인을 선택하면 사용자 환경의 IP 주소가 *속성* 페이지에 표시됩니다.
    * IP 주소는 ping할 수 있지만 도메인은 ping할 수 없는 경우 DNS가 잘못 구성되었을 수 있습니다. 관리형 도메인의 IP 주소가 가상 네트워크의 DNS 서버로 구성되어 있는지 확인합니다.
* `ipconfig /flushdns` 명령을 사용하여 가상 머신에서 DNS 확인자 캐시를 플러시합니다.

### <a name="credentials-related-issues"></a>자격 증명 관련 문제

도메인에 조인하기 위해 자격 증명을 요청하는 메시지가 표시되고 해당 자격 증명을 입력한 후에 오류가 발생하더라도 VM에서 관리되는 도메인에 연결할 수 있습니다. 그런 다음, 제공한 자격 증명으로 인해 VM이 관리되는 도메인에 조인되지 않습니다.

다음 문제 해결 단계를 각각 수행한 후에 Windows Server VM을 다시 관리형 도메인에 조인시킵니다.

* 지정한 사용자 계정이 관리되는 도메인에 속하는지 확인합니다.
* 계정이 관리되는 도메인 또는 Azure AD 테넌트에 속하는지 확인합니다. Azure AD 테넌트와 연결된 외부 디렉터리의 계정은 도메인 가입 프로세스 중에 올바르게 인증되지 않습니다.
* UPN 형식을 사용하여 자격 증명(예: `contosoadmin@aaddscontoso.onmicrosoft.com`)을 지정합니다. 테넌트에서 여러 사용자가 동일한 UPN 접두사를 사용하거나 UPN 접두사가 너무 긴 경우 사용자 계정의 *SAMAccountName*이 자동으로 생성될 수 있습니다. 이러한 경우 사용자 계정의 *SAMAccountName* 형식이 온-프레미스 도메인에서 필요하거나 사용하는 것과 다를 수 있습니다.
* 관리되는 도메인에 대해 [암호 동기화를 사용하도록 설정][password-sync]했는지 확인합니다. 이 구성 단계를 수행하지 않으면 필요한 암호 해시가 관리되는 도메인에 없어 로그인 시도를 올바르게 인증할 수 없습니다.
* 암호 동기화가 완료될 때까지 기다립니다. 사용자 계정의 암호가 변경되면 Azure AD의 자동 백그라운드 동기화가 Azure AD DS의 암호를 업데이트합니다. 도메인 조인 사용에 암호를 사용하려면 시간이 다소 걸립니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Windows Server VM 만들기
> * Azure 가상 네트워크의 Windows Server VM에 연결
> * VM을 관리되는 도메인에 조인

관리되는 도메인을 관리하려면 ADAC(Active Directory 관리 센터)를 사용하여 관리 VM을 구성하세요.

> [!div class="nextstepaction"]
> [관리 VM에 관리 도구 설치](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
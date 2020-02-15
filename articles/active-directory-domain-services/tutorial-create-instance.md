---
title: 자습서 - Azure Active Directory Domain Services 인스턴스 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Active Directory Domain Services 인스턴스를 만들고 구성하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: iainfou
ms.openlocfilehash: 8905f2a0a306ec4c9c6e19479c6adb96a6ed39ca
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931290"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>자습서: Azure Active Directory Domain Services 인스턴스 만들기 및 구성

Azure AD DS(Azure Active Directory Domain Services)는 Windows Server Active Directory와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP, Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 이러한 도메인 서비스는 도메인 컨트롤러를 직접 배포, 관리 및 패치하지 않고 사용할 수 있습니다. Azure AD DS는 기존 Azure AD 테넌트와 통합됩니다. 이러한 통합을 통해 사용자는 회사 자격 증명을 사용하여 로그인할 수 있으며, 기존 그룹과 사용자 계정을 사용하여 리소스에 대한 액세스를 보호할 수 있습니다.

네트워킹 및 동기화를 위해 [기본 구성 옵션을 사용하여 관리형 도메인을 만들거나][tutorial-create-instance-advanced] 이러한 설정을 수동으로 정의할 수 있습니다. 이 자습서에서는 Azure Portal에서 기본 옵션을 사용하여 Azure AD DS 인스턴스를 만들고 구성하는 방법을 보여줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 관리형 도메인의 DNS 요구 사항 이해
> * Azure AD DS 인스턴스 만들기
> * 암호 해시 동기화 사용

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD DS를 사용하도록 설정하려면 Azure AD 테넌트에 *글로벌 관리자* 권한이 필요합니다.
* 필요한 Azure AD DS 리소스를 만들려면 Azure 구독에 *기여자* 권한이 필요합니다.

Azure AD DS에는 필요하지 않지만 Azure AD 테넌트에 대해 [SSPR(셀프 서비스 암호 재설정)을 구성][configure-sspr]하는 것이 좋습니다. 사용자는 SSPR 없이 암호를 변경할 수 있지만 해당 암호를 잊어서 재설정해야 하는 경우 SSPR가 도움이 됩니다.

> [!IMPORTANT]
> Azure AD DS 관리 도메인을 만든 후에는 인스턴스를 다른 리소스 그룹, 가상 네트워크, 구독 등으로 이동할 수 없습니다. Azure AD DS 인스턴스를 배포할 때 가장 적합한 구독, 리소스 그룹, 지역 및 가상 네트워크를 선택합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 Azure AD DS 인스턴스를 만들고 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-instance"></a>인스턴스 만들기

**Azure AD Domain Services** 마법사를 시작하려면 다음 단계를 완료합니다.

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
1. 검색 창에서 *Domain Services*를 입력한 다음, 검색 제안에서 *Azure AD Domain Services*를 선택합니다.
1. Azure AD Domain Services 페이지에서 **만들기**를 선택합니다. **Azure AD Domain Services 사용** 마법사가 시작됩니다.
1. 관리되는 도메인을 만들려는 Azure **구독**을 선택합니다.
1. 관리되는 도메인이 속해야 하는 **리소스 그룹**을 선택합니다. **새로 만들기** 또는 기존 리소스 그룹을 선택합니다.

Azure AD DS 인스턴스를 만드는 경우 DNS 이름을 지정해야 합니다. 이 DNS 이름을 선택할 때 고려해야 할 몇 가지 사항이 있습니다.

* **기본 제공 도메인 이름:** 디렉터리의 기본 제공 도메인 이름( *.onmicrosoft.com* 접미사)이 기본적으로 사용됩니다. 관리되는 도메인에 대한 인터넷을 통한 보안 LDAP 액세스를 사용하도록 설정하려면 이 기본 도메인과의 연결을 보호하기 위해 디지털 인증서를 만들 수 없습니다. Microsoft에서 *.onmicrosoft.com* 도메인을 소유하고 있으므로 CA(인증 기관)는 인증서를 발급하지 않습니다.
* **사용자 지정 도메인 이름:** 가장 일반적인 방법은 일반적으로 이미 소유하고 있고 라우팅할 수 있는 사용자 지정 도메인 이름을 지정하는 것입니다. 라우팅할 수 있는 사용자 지정 도메인을 사용하면 애플리케이션을 지원하는 데 필요한 트래픽이 올바르게 전달될 수 있습니다.
* **라우팅할 수 없는 도메인 접미사:** 일반적으로 라우팅할 수 없는 도메인 이름 접미사(예: *contoso.local*)를 사용하지 않는 것이 좋습니다. *.local* 접미사는 라우팅할 수 없으며, DNS 확인에서 문제가 발생할 수 있습니다.

> [!TIP]
> 사용자 지정 도메인 이름을 만드는 경우 기존 DNS 네임스페이스를 주의해야 합니다. 도메인 이름에 대한 고유한 접두사를 포함하는 것이 좋습니다. 예를 들어 DNS 루트 이름이 *contoso.com*인 경우 사용자 지정 도메인 이름이 *corp.contoso.com* 또는 *ds.contoso.com*인 Azure AD DS 관리형 도메인을 만듭니다. 온-프레미스 AD DS 환경을 사용하는 하이브리드 환경에서 이러한 접두사는 이미 사용 중일 수 있습니다. Azure AD DS에 대한 고유한 접두사를 사용합니다.
>
> Azure AD DS 관리형 도메인에 대해 루트 DNS 이름을 사용할 수 있지만, 환경의 다른 서비스에 대해 일부 추가적인 DNS 레코드를 만들어야 할 수도 있습니다. 예를 들어 루트 DNS 이름을 사용 중인 사이트를 호스트하는 웹 서버를 실행하는 경우 추가 DNS 항목이 필요한 명명 충돌이 있을 수 있습니다.
>
> 이러한 자습서 및 방법 문서에서 *aadds.contoso.com*의 사용자 지정 도메인은 간단한 예제로 사용됩니다. 모든 명령에서 고유한 접두사를 포함할 수 있는 자신만의 고유한 도메인 이름을 지정합니다.
>
> 자세한 내용은 [도메인에 대한 명명 접두사 선택][naming-prefix]을 참조하세요.

다음 DNS 이름 제한도 적용됩니다.

* **도메인 접두사 제한:** 접두사가 15자보다 긴 관리되는 도메인은 만들 수 없습니다. 지정한 도메인 이름의 접두사(예: *contoso.com* 도메인 이름의 *contoso*)는 15자 이하의 문자여야 합니다.
* **네트워크 이름 충돌:** 관리되는 도메인의 DNS 도메인 이름이 가상 네트워크에 존재하지 않아야 합니다. 특히 이름 충돌이 발생할 수 있는 다음 시나리오를 확인하세요.
    * Azure 가상 네트워크에 동일한 DNS 도메인 이름의 Active Directory 도메인이 이미 있는 경우
    * 관리되는 도메인을 사용하도록 설정하려는 가상 네트워크에 온-프레미스 네트워크와의 VPN 연결이 있는 경우. 이 시나리오에서는 온-프레미스 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 없는지 확인합니다.
    * Azure 가상 네트워크에 해당 이름의 기존 Azure 클라우드 서비스가 있는 경우

Azure Portal의 *기본* 창에 있는 필드를 완성하여 Azure AD DS 인스턴스를 만듭니다.

1. 이전 사항을 고려하여 관리되는 도메인의 **DNS 도메인 이름**을 입력합니다.
1. 관리되는 도메인을 만들어야 하는 Azure **위치**를 선택합니다. 가용성 영역을 지원하는 지역을 선택하면 Azure AD DS 리소스가 추가 중복성을 위해 여러 영역에 배포됩니다.

    가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다.

    Azure AD DS를 영역 간에 배포하기 위해 구성해야 할 항목은 없습니다. Azure 플랫폼은 리소스의 영역 배포를 자동으로 처리합니다. 자세한 내용을 보고 지역 가용성을 확인하려면 [Azure에서 가용성 영역이란?][availability-zones]을 참조하세요.

1. **SKU**는 만들 수 있는 성능, 백업 빈도 및 최대 포리스트 트러스트 수를 결정합니다. 비즈니스 요구나 요구 사항이 변경되면 관리되는 도메인이 만들어진 후 SKU를 변경할 수 있습니다. 자세한 내용은 [Azure AD DS SKU 개념][concepts-sku]을 참조하세요.

    이 자습서에서는 *표준* SKU를 선택합니다.
1. *포리스트*는 Active Directory Domain Services에서 하나 이상의 도메인을 그룹화하는 데 사용되는 논리적 구문입니다. 기본적으로 Azure AD DS 관리형 도메인은 *사용자* 포리스트로 생성됩니다. 이 유형의 포리스트는 온-프레미스 AD DS 환경에서 만든 모든 사용자 계정을 포함하여 Azure AD의 모든 개체를 동기화합니다. *리소스* 포리스트는 Azure AD에서 직접 만든 사용자와 그룹만 동기화합니다. 리소스 포리스트는 현재 미리 보기로 제공됩니다. 온-프레미스 AD DS 도메인을 사용하여 포리스트 트러스트를 만드는 방법 및 사용하는 이유를 비롯하여 *리소스* 포리스트에 대한 자세한 내용은 [Azure AD DS 리소스 포리스트 개요][resource-forests]를 참조하세요.

    이 자습서에서는 *사용자* 포리스트를 만들도록 선택합니다.

    ![Azure AD Domain Services 인스턴스에 대한 기본 설정 구성](./media/tutorial-create-instance/basics-window.png)

Azure AD DS 관리형 도메인을 신속하게 만들려면 **검토 + 만들기**를 선택하여 추가 기본 구성 옵션을 적용합니다. 이 만들기 옵션을 선택하면 다음과 같은 기본값이 구성됩니다.

* IP 주소 범위 *10.0.1.0/24*를 사용하는 *aadds-vnet*이라는 가상 네트워크를 만듭니다.
* IP 주소 범위 *10.0.1.0/24*를 사용하는 *aadds-subnet*이라는 서브넷을 만듭니다.
* Azure AD의 모든 *사용자*를 Azure AD DS 관리형 도메인으로 동기화합니다.

**검토 + 만들기**를 선택하여 이러한 기본 구성 옵션을 적용합니다.

## <a name="deploy-the-managed-domain"></a>관리되는 도메인을 배포합니다.

마법사의 **요약** 페이지에서 관리되는 도메인에 대한 구성 설정을 검토합니다. 임의의 마법사 단계로 돌아가서 변경할 수 있습니다. 이러한 구성 옵션을 사용하여 일관적인 방식으로 Azure AD DS 관리형 도메인을 다른 Azure AD 테넌트에 재배포하려면 **자동화를 위한 템플릿을 다운로드**해도 됩니다.

1. 관리형 도메인을 만들려면 **만들기**를 선택합니다. Azure AD DS 관리형 도메인을 만들고 나면 DNS 이름 또는 가상 네트워크와 같은 특정 구성 옵션을 변경할 수 없다는 주의 사항이 표시됩니다. 계속하려면 **확인**을 선택합니다.
1. 관리되는 도메인을 프로비전하는 프로세스는 최대 한 시간 정도 걸릴 수 있습니다. 포털에 Azure AD DS 배포의 진행 상황을 보여 주는 알림이 표시됩니다. 알림을 선택하여 자세한 배포 진행 상황을 확인합니다.

    ![Azure Portal의 진행 중인 배포 알림](./media/tutorial-create-instance/deployment-in-progress.png)

1. 이 페이지에는 디렉터리에 새 리소스를 만드는 것을 포함하여 배포 프로세스에 대한 업데이트가 로드됩니다.
1. 리소스 그룹(예: *myResourceGroup*)을 선택한 다음, Azure 리소스 목록에서 Azure AD DS 인스턴스(예: *aadds.contoso.com*)를 선택합니다. **개요** 탭에서 관리되는 도메인이 현재 *배포 중*임을 보여 줍니다. 관리되는 도메인은 완전히 프로비저닝될 때까지 구성할 수 없습니다.

    ![프로비저닝 중 상태의 Domain Services 상태](./media/tutorial-create-instance/provisioning-in-progress.png)

1. 관리되는 도메인이 완전히 프로비전되면 **개요** 탭에 도메인 상태가 *실행 중*으로 표시됩니다.

    ![성공적으로 프로비저닝된 Domain Services 상태](./media/tutorial-create-instance/successfully-provisioned.png)

관리되는 도메인이 Auzre AD 테넌트와 연결되어 있습니다. 프로비저닝 프로세스 중에 Azure AD DS는 *도메인 컨트롤러 서비스* 및 *AzureActiveDirectoryDomainControllerServices*라는 두 개의 엔터프라이즈 애플리케이션을 Azure AD 테넌트에 만듭니다. 이러한 Enterprise 애플리케이션은 관리되는 도메인을 제공하는 데 필요합니다. 이러한 애플리케이션을 삭제하지 마세요.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Azure 가상 네트워크에 대한 DNS 설정 업데이트

Azure AD DS가 성공적으로 배포되면 이제 연결된 다른 VM과 애플리케이션에서 관리되는 도메인을 사용할 수 있도록 가상 네트워크를 구성합니다. 이 연결을 제공하려면 Azure AD DS가 배포된 두 개의 IP 주소를 가리키도록 가상 네트워크의 DNS 서버 설정을 업데이트합니다.

1. 관리되는 도메인에 대한 **개요** 탭에는 몇 가지 **필수 구성 단계**가 표시됩니다. 첫 번째 구성 단계는 가상 네트워크에 대한 DNS 서버 설정을 업데이트하는 것입니다. DNS 설정이 올바르게 구성되면 이 단계가 더 이상 표시되지 않습니다.

    나열된 주소는 가상 네트워크에서 사용할 도메인 컨트롤러입니다. 다음 예에서 해당 주소는 *10.1.0.4* 및 *10.1.0.5*입니다. 이러한 IP 주소는 나중에 **속성** 탭에서 확인할 수 있습니다.

    ![Azure AD Domain Services IP 주소를 사용하여 가상 네트워크에 대한 DNS 설정 구성](./media/tutorial-create-instance/configure-dns.png)

1. 가상 네트워크에 대한 DNS 서버 설정을 업데이트하려면 **구성** 단추를 선택합니다. 가상 네트워크에 대한 DNS 설정이 자동으로 구성됩니다.

> [!TIP]
> 이전 단계에서 기존 가상 네트워크를 선택한 경우 네트워크에 연결된 모든 VM은 다시 시작한 후에만 새 DNS 설정을 가져옵니다. VM은 Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하여 다시 시작할 수 있습니다.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Azure AD DS에서 사용자 계정을 사용하도록 설정

관리되는 도메인에서 사용자를 인증하려면 Azure AD DS에 NTLM(NT LAN Manager) 및 Kerberos 인증에 적합한 형식의 암호 해시가 필요합니다. Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

> [!NOTE]
> 적절히 구성되면 사용 가능한 암호 해시가 Azure AD DS 관리형 도메인에 저장됩니다. Azure AD DS 관리형 도메인을 삭제하면 해당 지점에 저장된 암호 해시도 모두 삭제됩니다. 나중에 Azure AD DS 관리형 도메인을 만드는 경우 Azure AD에서 동기화된 자격 증명 정보는 다시 사용할 수 없습니다. 즉 암호 해시를 다시 저장하도록 암호 해시 동기화를 다시 구성해야 합니다. 이전에는 도메인 조인 VM 또는 사용자가 즉시 인증할 수 없었으므로 Azure AD에서 암호 해시를 새 Azure AD DS 관리형 도메인에 생성하고 저장해야 합니다. 자세한 내용은 [Azure AD DS 및 Azure AD Connect에 대한 암호 해시 동기화 프로세스][password-hash-sync-process]를 참조하세요.

이러한 암호 해시를 생성하고 저장하는 단계는 Azure AD에서 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용하여 온-프레미스 디렉터리에서 동기화된 사용자 계정에 대해 서로 다릅니다. 클라우드 전용 사용자 계정은 Azure Portal 또는 Azure AD PowerShell cmdlet을 사용하여 Azure AD 디렉터리에 만든 계정입니다. 이러한 사용자 계정은 온-프레미스 디렉터리에서 동기화되지 않습니다. 이 자습서에서는 기본 클라우드 전용 사용자 계정을 사용하겠습니다. Azure AD Connect를 사용하는 데 필요한 추가 단계에 대한 자세한 내용은 [온-프레미스 AD에서 관리되는 도메인으로 동기화된 사용자 계정에 대한 암호 해시 동기화][on-prem-sync]를 참조하세요.

> [!TIP]
> 클라우드 전용 사용자와 온-프레미스 AD의 사용자가 Azure AD 테넌트에 있는 경우 두 단계를 모두 수행해야 합니다.

클라우드 전용 사용자 계정의 경우 Azure AD DS를 사용하려면 먼저 사용자가 암호를 변경해야 합니다. 이 암호 변경 프로세스로 인해 Kerberos 및 NTLM 인증용 암호 해시가 생성되어 Azure AD에 저장됩니다. Azure AD DS를 사용해야 하는 테넌트의 모든 사용자에 대한 암호를 만료시켜 다음 로그인에서 암호를 강제로 변경하도록 하거나 암호를 수동으로 변경하도록 지시할 수 있습니다. 이 자습서에서는 사용자 암호를 수동으로 변경해 보겠습니다.

사용자가 암호를 다시 설정하려면 먼저 [셀프 서비스 암호 재설정][configure-sspr]을 수행하도록 Azure AD 테넌트를 구성해야 합니다.

클라우드 전용 사용자의 암호를 변경하려면 다음 단계를 수행해야 합니다.

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)의 Azure AD 액세스 패널 페이지로 이동합니다.
1. 오른쪽 위 모서리에서 이름을 선택한 다음, 드롭다운 메뉴에서 **프로필**을 선택합니다.

    ![프로필 선택](./media/tutorial-create-instance/select-profile.png)

1. **프로필** 페이지에서 **암호 변경**을 선택합니다.
1. **암호 변경** 페이지에서 기존(이전) 암호를 입력한 다음, 새 암호를 입력하고 확인합니다.
1. **제출**을 선택합니다.

암호를 변경한 후 Azure AD DS에서 새 암호를 사용할 수 있고 관리되는 도메인에 조인한 컴퓨터에 성공적으로 로그인될 때까지 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 관리형 도메인의 DNS 요구 사항 이해
> * Azure AD DS 인스턴스 만들기
> * 도메인 관리에 관리자 추가
> * Azure AD DS에서 사용자 계정을 사용하도록 설정 및 암호 해시 생성

VM을 도메인에 가입하고 Azure AD DS 관리형 도메인을 사용하는 애플리케이션을 배포하기 전에, 애플리케이션 워크로드에 대한 Azure 가상 네트워크를 구성합니다.

> [!div class="nextstepaction"]
> [관리형 도메인을 사용하도록 애플리케이션 워크로드에 대한 Azure 가상 네트워크 구성](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[skus]: overview.md
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix

---
title: Azure AD Domain Services에서 보안 원격 VM 액세스 Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인에서 원격 데스크톱 서비스 배포를 사용 하 여 NPS (네트워크 정책 서버) 및 Azure Multi-Factor Authentication를 사용 하 여 Vm에 대 한 원격 액세스를 보호 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 462e0ff8ef8baadf40ba81ed2ae137d8904fbf7a
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722520"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 가상 컴퓨터에 대 한 보안 원격 액세스

Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인에서 실행 되는 Vm (가상 컴퓨터)에 대 한 원격 액세스를 보호 하기 위해 RDS (원격 데스크톱 서비스)와 NPS (네트워크 정책 서버)를 사용할 수 있습니다. Azure AD DS는 사용자가 RDS 환경을 통해 액세스를 요청 하는 사용자를 인증 합니다. 보안 강화를 위해 Azure Multi-Factor Authentication를 통합 하 여 로그인 이벤트 중에 추가 인증 프롬프트를 제공할 수 있습니다. Azure Multi-Factor Authentication는 NPS 용 확장을 사용 하 여이 기능을 제공 합니다.

> [!IMPORTANT]
> Azure AD DS 관리 되는 도메인에서 Vm에 안전 하 게 연결 하는 권장 방법은 가상 네트워크 내에서 프로 비전 하는 완전히 플랫폼 관리 PaaS 서비스인 Azure 방호를 사용 하는 것입니다. 요새 호스트는 SSL을 통해 Azure Portal에서 직접 Vm에 대 한 안정적이 고 원활한 원격 데스크톱 프로토콜 (RDP) 연결을 제공 합니다. 요새 호스트를 통해 연결 하는 경우 Vm에 공용 IP 주소가 필요 하지 않으며, TCP 포트 3389에서 RDP에 대 한 액세스를 노출 하기 위해 네트워크 보안 그룹을 사용할 필요가 없습니다.
>
> 지원 되는 모든 지역에서 Azure 방호를 사용 하는 것이 좋습니다. Azure 방호 가용성이 없는 지역에서 Azure 방호를 사용할 수 있을 때까지이 문서에 설명 된 단계를 따르세요. 들어오는 모든 RDP 트래픽이 허용 되는 Azure AD DS에 조인 된 Vm에 공용 IP 주소를 할당 하는 데 주의 합니다.
>
> 자세한 내용은 [Azure 방호 이란?][bastion-overview]을 참조 하세요.

이 문서에서는 Azure AD DS에서 RDS를 구성 하 고 필요에 따라 Azure Multi-Factor Authentication NPS 확장을 사용 하는 방법을 보여 줍니다.

![RDS (원격 데스크톱 서비스) 개요](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음 리소스가 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure Active Directory Domain Services 가상 네트워크에서 만든 *워크 로드* 서브넷
    * 필요한 경우 [Azure Active Directory Domain Services 관리 되는 도메인에 대 한 가상 네트워킹을 구성][configure-azureadds-vnet]합니다.
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>원격 데스크톱 환경 배포 및 구성

시작 하려면 Windows Server 2016 또는 Windows Server 2019를 실행 하는 최소 두 개의 Azure Vm을 만듭니다. RD (원격 데스크톱) 환경의 중복성 및 고가용성을 위해 나중에 추가 호스트를 추가 하 고 부하를 분산할 수 있습니다.

제안 된 RDS 배포에는 다음과 같은 두 개의 Vm이 포함 됩니다.

* *RDGVM01* -RD 연결 브로커 서버, RD 웹 액세스 서버 및 RD 게이트웨이 서버를 실행 합니다.
* *RDSHVM01* -RD 세션 호스트 서버를 실행 합니다.

Vm이 Azure AD DS 가상 네트워크의 *워크 로드* 서브넷에 배포 되었는지 확인 한 다음 vm을 관리 되는 도메인에 가입 시킵니다. 자세한 내용은 [Windows SERVER VM을 만들고 관리 되는 도메인에 가입][tutorial-create-join-vm]하는 방법을 참조 하세요.

RD 환경 배포에는 여러 단계가 포함 됩니다. 기존 RD 배포 가이드는 관리 되는 도메인에서 사용 하기 위한 특정 변경 없이 사용할 수 있습니다.

1. *Contosoadmin*와 같은 *Azure AD DC 관리자* 그룹의 일부인 계정으로 RD 환경에 대해 만든 vm에 로그인 합니다.
1. RDS를 만들고 구성 하려면 기존 [원격 데스크톱 환경 배포 가이드][deploy-remote-desktop]를 사용 합니다. 원하는 대로 Azure Vm에서 RD 서버 구성 요소를 배포 합니다.
    * Azure AD DS에 한정-RD 라이선스를 구성 하는 경우 배포 가이드에 설명 된 대로 **사용자 단위** **모드로 설정** 합니다.
1. 웹 브라우저를 사용 하 여 액세스를 제공 하려는 경우 [사용자에 대 한 원격 데스크톱 웹 클라이언트를 설정][rd-web-client]합니다.

관리 되는 도메인에 RD을 배포 하면 온-프레미스 AD DS 도메인에서와 같이 서비스를 관리 하 고 사용할 수 있습니다.

## <a name="deploy-and-configure-nps-and-the-azure-mfa-nps-extension"></a>NPS 및 Azure MFA NPS 확장 배포 및 구성

사용자 로그인 환경의 보안을 강화 하려면 선택적으로 RD 환경을 Azure Multi-Factor Authentication와 통합할 수 있습니다. 이 구성을 사용 하면 로그인 하는 동안 사용자에 게 id를 확인 하는 추가 프롬프트가 표시 됩니다.

이 기능을 제공 하기 위해 추가 NPS (네트워크 정책 서버)는 Azure Multi-Factor Authentication NPS 확장과 함께 사용자 환경에 설치 됩니다. 이 확장은 Azure AD와 통합 되어 multi-factor authentication 프롬프트의 상태를 요청 하 고 반환 합니다.

[Azure Multi-Factor Authentication을 사용 하려면][user-mfa-registration]사용자를 등록 해야 합니다. azure AD 라이선스를 추가 해야 할 수도 있습니다.

Azure Multi-Factor Authentication를 Azure AD DS 원격 데스크톱 환경에 통합 하려면 NPS 서버를 만들고 확장을 설치 합니다.

1. Azure AD DS 가상 네트워크의 *워크 로드* 서브넷에 연결 된 추가 Windows Server 2016 또는 2019 VM (예: *NPSVM01*)을 만듭니다. VM을 관리 되는 도메인에 가입 시킵니다.
1. *AZURE AD DC 관리자* 그룹의 일부인 계정으로 NPS VM에 로그인 합니다 (예: *contosoadmin*).
1. **서버 관리자**에서 **역할 및 기능 추가**를 선택 하 고 *네트워크 정책 및 액세스 서비스* 역할을 설치 합니다.
1. 기존 방법 문서를 사용 하 여 [AZURE MFA NPS 확장을 설치 및 구성][nps-extension]합니다.

NPS 서버와 Azure Multi-Factor Authentication NPS 확장을 설치한 상태에서 다음 섹션을 완료 하 여 RD 환경에서 사용할 수 있도록 구성 합니다.

## <a name="integrate-remote-desktop-gateway-and-azure-multi-factor-authentication"></a>원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 통합

Azure Multi-Factor Authentication NPS 확장을 통합 하려면 기존 방법 문서를 사용 하 여 [nps (네트워크 정책 서버) 확장 및 AZURE AD를 사용 하 여 원격 데스크톱 게이트웨이 인프라를 통합][azure-mfa-nps-integration]합니다.

관리 되는 도메인과 통합 하는 데 필요한 추가 구성 옵션은 다음과 같습니다.

1. [Active Directory에 NPS 서버를 등록][register-nps-ad]하지 마십시오. 이 단계는 관리 되는 도메인에서 실패 합니다.
1. [4 단계에서 네트워크 정책을 구성 하려면][create-nps-policy] **사용자 계정 전화 접속 속성 무시**확인란을 선택 합니다.
1. Nps 서버 및 Azure Multi-Factor Authentication NPS 확장에 대해 Windows Server 2019를 사용 하는 경우 NPS 서버가 올바르게 통신할 수 있도록 다음 명령을 실행 하 여 보안 채널을 업데이트 합니다.

    ```powershell
    sc sidtype IAS unrestricted
    ```

사용자가 로그인 할 때 추가 인증 요인을 묻는 메시지가 표시 됩니다 (예: Microsoft Authenticator 앱에서 텍스트 메시지 또는 프롬프트).

## <a name="next-steps"></a>다음 단계

배포의 복원 력을 개선 하는 방법에 대 한 자세한 내용은 [원격 데스크톱 서비스-고가용성][rds-high-availability]을 참조 하세요.

사용자 로그인 보안에 대 한 자세한 내용은 [작동 방법: Azure Multi-Factor Authentication][concepts-mfa]를 참조 하세요.

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability
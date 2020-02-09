---
title: Azure AD Domain Services를 사용 하 여 도메인 가입 문제 해결 | Microsoft Docs
description: VM을 도메인에 가입 하거나 응용 프로그램을 Azure Active Directory Domain Services에 연결 하려고 할 때 일반적인 문제를 해결 하는 방법에 대해 알아보고 관리 되는 도메인에 연결 하거나 인증할 수 없습니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 73a76c4442bb8af70168e54a294f2cb100ff653c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703666"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인의 도메인 가입 문제 해결

VM (가상 컴퓨터)에 가입 하거나 응용 프로그램을 관리 되는 Azure Active Directory Domain Services (AD DS) 도메인에 연결 하려고 할 때 오류가 발생할 수 있습니다. 도메인 가입 문제를 해결 하려면 다음 중 문제가 발생 한 시점을 검토 하십시오.

* 인증 프롬프트가 수신 되지 않으면 VM 또는 응용 프로그램이 Azure AD DS 관리 되는 도메인에 연결할 수 없습니다.
    * [도메인 가입에 대한 연결 문제](#connectivity-issues-for-domain-join)해결을 시작 합니다.
* 인증 하는 동안 오류가 수신 되 면 Azure AD DS 관리 되는 도메인에 대한 연결이 성공 합니다.
    * [도메인 가입 시 자격 증명 관련 문제](#credentials-related-issues-during-domain-join)를 해결 하기 시작 합니다.

## <a name="connectivity-issues-for-domain-join"></a>도메인 가입에 대한 연결 문제

VM이 Azure AD DS 관리 되는 도메인을 찾을 수 없는 경우 일반적으로 네트워크 연결 또는 구성 문제가 있습니다. 다음 문제 해결 단계를 검토 하 여 문제를 찾아 해결 합니다.

1. VM이 Azure AD DS에 대해 사용 하도록 설정 된 동일한 또는 피어 링 가상 네트워크에 연결 되어 있는지 확인 하세요. 그렇지 않으면 VM에서 가입 하기 위해 도메인을 찾아서 연결할 수 없습니다.
    * VM이 동일한 가상 네트워크에 연결 되어 있지 않은 경우에는 가상 네트워크 피어 링 또는 VPN 연결이 *활성* 상태 인지 *확인 하 고* 트래픽이 올바르게 흐를 수 있도록 연결 되어 있는지 확인 합니다.
1. `ping aadds.contoso.com`와 같이 Azure AD DS 관리 되는 도메인의 도메인 이름을 사용 하 여 도메인에 ping을 시도 합니다.
    * Ping 응답이 실패 하면 `ping 10.0.0.4`와 같이 Azure AD DS 관리 되는 도메인에 대한 포털의 개요 페이지에 표시 되는 도메인의 IP 주소를 ping 해 봅니다.
    * IP 주소를 성공적으로 ping 할 수 있지만 도메인은 ping 할 수 없으면 DNS가 잘못 구성 된 것일 수 있습니다. 가상 네트워크에 대한 Azure AD DS 관리 되는 도메인 DNS 서버를 구성 했는지 확인 합니다.
1. `ipconfig /flushdns`와 같은 가상 머신에서 DNS 확인자 캐시를 플러시합니다.

### <a name="network-security-group-nsg-configuration"></a>NSG (네트워크 보안 그룹) 구성

Azure AD DS 관리 되는 도메인을 만들 때 성공적인 도메인 작업에 대한 적절 한 규칙을 사용 하 여 네트워크 보안 그룹도 만들어집니다. 네트워크 보안 그룹 규칙을 추가 하거나 편집 하는 경우 Azure AD DS에서 연결 및 인증 서비스를 제공 하는 데 필요한 포트를 실수로 차단할 수 있습니다. 이러한 네트워크 보안 그룹 규칙은 암호 동기화가 완료 되지 않았거나, 사용자가 로그인 할 수 없거나, 도메인 가입 문제 등의 문제가 발생할 수 있습니다.

연결 문제가 계속 되 면 다음 문제 해결 단계를 검토 합니다.

1. Azure Portal에서 Azure AD DS 관리 되는 도메인의 상태를 확인 합니다. *AADDS001*에 대한 경고가 있는 경우 네트워크 보안 그룹 규칙은 액세스를 차단 합니다.
1. [필요한 포트 및 네트워크 보안 그룹 규칙][network-ports]을 검토 합니다. 연결 중인 VM 또는 가상 네트워크에 적용 되는 네트워크 보안 그룹 규칙이 이러한 네트워크 포트를 차단 하지 않는지 확인 합니다.
1. 네트워크 보안 그룹 구성 문제를 해결 한 후에는 *AADDS001* 경고가 상태 페이지에서 약 2 시간으로 사라집니다. 이제 네트워크 연결을 사용할 수 있습니다. VM에 도메인 가입을 다시 시도 합니다.

## <a name="credentials-related-issues-during-domain-join"></a>도메인 조인 중 자격 증명 관련 문제

Azure AD DS 관리 되는 도메인에 가입 하기 위한 자격 증명을 요청 하는 대화 상자가 표시 되 면 VM은 Azure virtual network를 사용 하 여 도메인에 연결할 수 있습니다. 도메인 가입 프로세스는 자격 증명을 사용 하 여 도메인 가입 프로세스를 완료 하기 위해 도메인 또는 권한 부여에 대한 인증에 실패 합니다.

자격 증명 관련 문제를 해결 하려면 다음 문제 해결 단계를 검토 합니다.

1. UPN 형식을 사용하여 자격 증명(예: `dee@contoso.onmicrosoft.com`)을 지정합니다. 이 UPN이 Azure AD에서 올바르게 구성 되어 있는지 확인 합니다.
    * 사용자 계정에 대한 *SAMAccountName* 은 테넌트에서 동일한 upn 접두사를 사용 하는 여러 사용자가 있거나 upn 접두사가 너무 긴 경우 자동으로 생성 될 수 있습니다. 따라서 계정에 대한 *SAMAccountName* 형식은 사용자의 온-프레미스 도메인에서 사용 하거나 사용 하는 것과 다를 수 있습니다.
1. *AAD DC 관리자* 그룹에 속한 사용자 계정에 대한 자격 증명을 사용 하 여 Vm을 Azure AD DS 관리 되는 도메인에 연결 해 봅니다.
1. [암호 동기화를 사용 하도록 설정][enable-password-sync] 하 고 초기 암호 동기화가 완료 될 때까지 충분히 대기 해야 합니다.

## <a name="next-steps"></a>다음 단계

도메인 가입 작업의 일부로 Active Directory 프로세스를 보다 자세히 이해 하려면 [조인 및 인증 문제][join-authentication-issues]를 참조 하세요.

Azure AD DS 관리 되는 도메인에 VM을 가입 하는 데 문제가 있는 경우 [도움말을 찾아 Azure Active Directory에 대한 지원 티켓을 여세요][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)

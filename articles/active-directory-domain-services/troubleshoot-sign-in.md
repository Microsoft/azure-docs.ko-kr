---
title: Azure AD Domain Services에서 로그인 문제 해결 Microsoft Docs
description: Azure Active Directory Domain Services에서 일반적인 사용자 로그인 문제 및 오류를 해결 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 91c42e391d1a23e9ac494af9271c92e3e9b5d6c4
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722656"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리 되는 도메인의 계정 로그인 문제 해결

Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인에 로그인 할 수 없는 사용자 계정에 대 한 가장 일반적인 이유는 다음과 같습니다.

* [계정이 아직 Azure AD DS로 동기화 되지 않았습니다.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS에는 계정이 로그인 할 수 있도록 암호 해시가 없습니다.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [계정이 잠겼습니다.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS는 Azure AD 테 넌 트 외부에 있는 계정에 대 한 자격 증명을 동기화 할 수 없습니다. 외부 사용자는 Azure AD DS 관리 되는 도메인에 로그인 할 수 없습니다.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>계정이 Azure AD DS에 아직 동기화 되지 않았습니다.

디렉터리 크기에 따라 관리 되는 도메인에서 사용자 계정 및 자격 증명 해시를 사용 하는 데 다소 시간이 걸릴 수 있습니다. 규모가 많은 디렉터리의 경우 Azure AD에서이 초기 단방향 동기화는 몇 시간이 걸릴 수 있으며, 최대 한 일 또는 2 개까지 걸릴 수 있습니다. 인증을 다시 시도 하기 전에 충분히 기다려야 합니다.

사용자 Azure AD Connect가 온-프레미스 디렉터리 데이터를 Azure AD로 동기화 하는 하이브리드 환경의 경우 최신 버전의 Azure AD Connect를 실행 하 고 [azure AD DS를 사용 하도록 설정한 후 전체 동기화를 수행 하도록 Azure AD Connect를 구성][azure-ad-connect-phs]해야 합니다. Azure AD DS을 사용 하지 않도록 설정한 다음 다시 사용 하도록 설정 하는 경우이 단계를 다시 수행 해야 합니다.

Azure AD Connect를 통해 동기화 되지 않는 계정에 문제가 계속 발생 하는 경우 Azure AD Sync 서비스를 다시 시작 합니다. Azure AD Connect 설치 된 컴퓨터에서 명령 프롬프트 창을 열고 다음 명령을 실행 합니다.

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS에 암호 해시가 없습니다.

Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>온-프레미스 동기화를 사용 하는 하이브리드 환경

Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 하는 하이브리드 환경의 경우 필요한 NTLM 또는 Kerberos 암호 해시를 로컬로 생성 하 고 Azure AD에 동기화 할 수 있습니다. 관리 되는 도메인을 만든 후 [Azure Active Directory Domain Services에 대 한 암호 해시 동기화를 사용 하도록 설정][azure-ad-connect-phs]합니다. 이 암호 해시 동기화 단계를 완료 하지 않으면 관리 되는 도메인을 사용 하 여 계정에 로그인 할 수 없습니다. Azure AD DS을 사용 하지 않도록 설정한 다음 다시 사용 하도록 설정 하는 경우 해당 단계를 다시 수행 해야 합니다.

자세한 내용은 [Azure AD DS에 대 한 암호 해시 동기화 작동 방식][phs-process]을 참조 하세요.

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>온-프레미스 동기화가 없는 클라우드 전용 환경

온-프레미스 동기화가 없는 관리 되는 도메인, Azure AD의 계정만 필요한 NTLM 또는 Kerberos 암호 해시를 생성 해야 합니다. 클라우드 전용 계정이 로그인 할 수 없는 경우 Azure AD DS를 사용 하도록 설정한 후 계정에 대 한 암호 변경 프로세스가 성공적으로 완료 되었습니까?

* **아니요, 암호가 변경 되지 않았습니다.**
    * [계정에 대 한 암호를 변경][enable-user-accounts] 하 여 필요한 암호 해시를 생성 한 다음, 다시 로그인을 시도 하기 전에 15 분 동안 기다립니다.
    * Azure AD DS을 사용 하지 않도록 설정한 다음 다시 사용 하도록 설정 하면 각 계정에서 단계를 다시 수행 하 여 암호를 변경 하 고 필요한 암호 해시를 생성 해야 합니다.
* **예, 암호가 변경 되었습니다.**
    * 와 같은 SAMAccountName 형식 대신 *UPN* 형식을 사용 하 여 로그인을 시도 `driley@aaddscontoso.com` *SAMAccountName* `AADDSCONTOSO\deeriley` 합니다.
    * UPN 접두사가 너무 길거나 관리 되는 도메인의 다른 사용자와 동일한 사용자에 대해 *SAMAccountName* 이 자동으로 생성 될 수 있습니다. *UPN* 형식은 Azure AD 테 넌 트 내에서 고유 하도록 보장 됩니다.

## <a name="the-account-is-locked-out"></a>계정이 잠겼습니다.

실패 한 로그인 시도에 대해 정의 된 임계값이 충족 되 면 관리 되는 도메인의 사용자 계정이 잠깁니다. 이 계정 잠금 동작은 자동 디지털 공격을 나타낼 수 있는 무작위 로그인 시도를 반복 해 서 방지 하도록 설계 되었습니다.

기본적으로 2 분 내에 잘못 된 암호를 5 번 시도 하면 계정이 30 분 동안 잠깁니다.

자세한 내용과 계정 잠금 문제를 해결 하는 방법에 대 한 자세한 내용은 [Azure AD DS의 계정 잠금 문제 해결][troubleshoot-account-lockout]을 참조 하세요.

## <a name="next-steps"></a>다음 단계

VM을 관리 되는 도메인에 가입 하는 데 문제가 있는 경우 [도움말을 찾고 Azure Active Directory에 대 한 지원 티켓을 엽니다][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
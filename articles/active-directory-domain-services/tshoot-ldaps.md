---
title: Azure AD Domain Services의 보안 LDAP 문제 해결 | Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인에 대 한 보안 LDAP (LDAPS) 문제를 해결 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132160"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리 되는 도메인에 대 한 보안 LDAP 연결 문제 해결

LDAP (lightweight directory access protocol)를 사용 하 여 Azure Active Directory Domain Services (Azure AD DS)와 통신 하는 응용 프로그램 및 서비스는 [보안 ldap를 사용 하도록 구성할](tutorial-configure-ldaps.md)수 있습니다. 보안 LDAP가 제대로 작동 하려면 적절 한 인증서 및 필요한 네트워크 포트를 열어야 합니다.

이 문서는 Azure AD DS에서 보안 LDAP 액세스와 관련 된 문제를 해결 하는 데 도움이 됩니다.

## <a name="common-connection-issues"></a>일반적인 연결 문제

보안 LDAP를 사용 하 여 Azure AD DS 관리 되는 도메인에 연결 하는 데 문제가 있는 경우 다음 문제 해결 단계를 검토 합니다. 각 문제 해결 단계 후에 Azure AD DS 관리 되는 도메인에 다시 연결 해 보세요.

* 보안 LDAP 인증서의 발급자 체인을 클라이언트에서 신뢰할 수 있어야 합니다. 클라이언트의 신뢰할 수 있는 루트 인증서 저장소에 루트 CA (인증 기관)를 추가 하 여 트러스트를 설정할 수 있습니다.
    * [인증서를 내보내고 클라이언트 컴퓨터에 적용][client-cert]해야 합니다.
* 관리 되는 도메인에 대 한 보안 LDAP 인증서의 *주체* 또는 *주체 대체 이름* 특성에 DNS 이름이 있는지 확인 합니다.
    * [보안 LDAP 인증서 요구 사항을][certs-prereqs] 검토 하 고 필요한 경우 대체 인증서를 만듭니다.
* *Ldp.exe* 와 같은 LDAP 클라이언트가 IP 주소가 아닌 DNS 이름을 사용 하 여 보안 ldap 끝점에 연결 하는지 확인 합니다.
    * Azure AD DS 관리 되는 도메인에 적용 되는 인증서에는 서비스의 IP 주소, DNS 이름만 포함 되지 않습니다.
* LDAP 클라이언트가 연결할 DNS 이름을 확인합니다. Azure AD DS 관리 되는 도메인의 보안 LDAP에 대 한 공용 IP 주소로 확인 되어야 합니다.
    * DNS 이름이 내부 IP 주소로 확인 되는 경우 DNS 레코드를 업데이트 하 여 외부 IP 주소로 확인 합니다.
* 외부 연결의 경우 네트워크 보안 그룹은 인터넷에서 TCP 포트 636에 대 한 트래픽을 허용 하는 규칙을 포함 해야 합니다.
    * 가상 네트워크에 직접 연결 되어 있지만 외부 연결이 아닌 리소스에서 보안 LDAP를 사용 하 여 Azure AD DS 관리 되는 도메인에 연결할 수 있는 경우 [보안 ldap 트래픽을 허용 하는 네트워크 보안 그룹 규칙을 만들어야][ldaps-nsg]합니다.

## <a name="next-steps"></a>다음 단계

문제가 계속 되 면 [Azure 지원 요청을 열어][azure-support] 추가 문제 해결 지원을 요청 하세요.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet

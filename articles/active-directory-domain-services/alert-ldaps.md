---
title: Azure AD Domain Services에서 보안 LDAP 경고 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에 대한 보안 LDAP로 일반적인 경고 문제를 해결 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "71258041"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: 보안 LDAP 경고 Azure Active Directory Domain Services

LDAP (lightweight directory access protocol)를 사용 하 여 Azure Active Directory Domain Services (Azure AD DS)와 통신 하는 응용 프로그램 및 서비스는 [보안 ldap를 사용 하도록 구성할](tutorial-configure-ldaps.md)수 있습니다. 보안 LDAP가 제대로 작동 하려면 적절 한 인증서 및 필요한 네트워크 포트를 열어야 합니다.

이 문서는 Azure AD DS에서 보안 LDAP 액세스를 사용 하 여 일반적인 경고를 이해 하 고 해결 하는 데 도움이 됩니다.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: 네트워크 구성 보안 LDAP

### <a name="alert-message"></a>경고 메시지

*인터넷을 통해 보안 LDAP는 관리 되는 도메인에 대해 사용 하도록 설정 됩니다. 그러나 포트 636에 대한 액세스는 네트워크 보안 그룹을 사용 하 여 잠기지 않습니다. 이렇게 하면 관리 되는 도메인의 사용자 계정이 암호 무차별 암호 대입 공격에 노출 될 수 있습니다.*

### <a name="resolution"></a>해결 방법

보안 LDAP를 사용 하도록 설정 하는 경우 특정 IP 주소에 대한 인바운드 LDAPS 액세스를 제한 하는 추가 규칙을 만드는 것이 좋습니다. 이러한 규칙은 무차별 암호 대입 공격 으로부터 Azure AD DS 관리 되는 도메인을 보호 합니다. 보안 LDAP에 대한 TCP 포트 636 액세스를 제한 하도록 네트워크 보안 그룹을 업데이트 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **네트워크 보안 그룹**을 검색 하 고 선택 합니다.
1. *Contoso.com-NSG*와 같이 관리 되는 도메인과 연결 된 네트워크 보안 그룹을 선택한 다음 **인바운드 보안 규칙** 을 선택 합니다.
1. **+** TCP 포트 636에 대한 규칙을 추가 합니다. 필요한 경우 창에서 **고급** 을 선택 하 여 규칙을 만듭니다.
1. **원본의**경우 드롭다운 메뉴에서 *IP 주소* 를 선택 합니다. 보안 LDAP 트래픽에 대한 액세스 권한을 부여 하려는 원본 IP 주소를 입력 합니다.
1. **대상**으로 *Any* 를 선택한 다음 **대상 포트 범위**에 대해 *636* 을 입력 합니다.
1. **프로토콜** 을 *TCP* 로 설정 하 고 **작업** 을 *허용*으로 설정 합니다.
1. 규칙의 우선 순위를 지정 하 고 *RestrictLDAPS*와 같은 이름을 입력 합니다.
1. 준비가 되 면 **추가** 를 선택 하 여 규칙을 만듭니다.

Azure AD DS 관리 되는 도메인의 상태는 2 시간 내에 자동으로 업데이트 되 고 경고를 제거 합니다.

> [!TIP]
> Azure AD DS를 원활 하 게 실행 하는 데 필요한 유일한 규칙은 TCP 포트 636입니다. 자세히 알아보려면 [Azure AD DS 네트워크 보안 그룹 및 필요한 포트](network-considerations.md#network-security-groups-and-required-ports)를 참조 하세요.

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: 보안 LDAP 인증서 만료

### <a name="alert-message"></a>경고 메시지

*관리되는 도메인에 대한 보안 LDAP 인증서는 [date]]에 만료됩니다.*

### <a name="resolution"></a>해결 방법

[보안 ldap에 대한 인증서를 만드는](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)단계를 수행 하 여 대체 보안 ldap 인증서를 만듭니다. 대체 인증서를 Azure AD DS에 적용 하 고 보안 LDAP를 사용 하 여 연결 하는 모든 클라이언트에 인증서를 배포 합니다.

## <a name="next-steps"></a>다음 단계

문제가 계속 되 면 [Azure 지원 요청을 열어][azure-support] 추가 문제 해결 지원을 요청 하세요.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

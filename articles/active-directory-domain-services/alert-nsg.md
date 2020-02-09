---
title: Azure AD DS에서 네트워크 보안 그룹 경고 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에 대한 네트워크 보안 그룹 구성 경고 문제를 해결 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257998"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>알려진 문제: Azure Active Directory Domain Services의 네트워크 구성 경고

응용 프로그램 및 서비스가 Azure Active Directory Domain Services (Azure AD DS)와 올바르게 통신할 수 있도록 하려면 트래픽이 흐를 수 있도록 특정 네트워크 포트가 열려 있어야 합니다. Azure에서는 네트워크 보안 그룹을 사용 하 여 트래픽 흐름을 제어 합니다. Azure AD DS 관리 되는 도메인의 상태는 필요한 네트워크 보안 그룹 규칙이 적용 되지 않은 경우 경고를 표시 합니다.

이 문서는 네트워크 보안 그룹 구성 문제에 대한 일반적인 경고를 이해 하 고 해결 하는 데 도움이 됩니다.

## <a name="alert-aadds104-network-error"></a>경고 AADDS104: 네트워크 오류

### <a name="alert-message"></a>경고 메시지

*Microsoft는 이 관리되는 도메인에 대한 도메인 컨트롤러에 연결할 수 없습니다. 가상 네트워크에 구성된 NSG(네트워크 보안 그룹)가 관리되는 도메인에 대한 액세스를 차단하려고 할 때 이 문제가 발생할 수 있습니다. 다른 가능한 이유는 인터넷에서 들어오는 트래픽을 차단하는 사용자 정의 경로가 있는 경우입니다.*

잘못 된 네트워크 보안 그룹 규칙은 Azure AD DS에 대한 네트워크 오류의 가장 일반적인 원인입니다. 가상 네트워크에 대한 네트워크 보안 그룹은 특정 포트 및 프로토콜에 대한 액세스를 허용 해야 합니다. 이러한 포트가 차단 되 면 Azure 플랫폼에서 관리 되는 도메인을 모니터링 하거나 업데이트할 수 없습니다. Azure AD 디렉터리와 Azure AD DS 관리 되는 도메인 간의 동기화도 영향을 받습니다. 서비스 중단을 방지 하기 위해 기본 포트를 열어 두어야 합니다.

## <a name="default-security-rules"></a>기본 보안 규칙

Azure AD DS 관리 되는 도메인에 대한 네트워크 보안 그룹에 적용 되는 기본 인바운드 및 아웃 바운드 보안 규칙은 다음과 같습니다. 이러한 규칙은 Azure AD DS 안전 하 게 유지 하 고 Azure 플랫폼에서 관리 되는 도메인을 모니터링, 관리 및 업데이트할 수 있도록 합니다. [보안 LDAP를 구성][configure-ldaps]하는 경우 인바운드 트래픽을 허용 하는 추가 규칙도 있을 수 있습니다.

### <a name="inbound-security-rules"></a>인바운드 보안 규칙

| 우선 순위 | 이름 | 포트 | Protocol | 원본 | Destination | 작업 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | 임의의 값 | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | 임의의 값 | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | 임의의 값 | Allow |
| 65000    | AllVnetInBound | 임의의 값 | 임의의 값 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | 임의의 값 | 임의의 값 | AzureLoadBalancer | 임의의 값 | Allow |
| 65500    | DenyAllInBound | 임의의 값 | 임의의 값 | 임의의 값 | 임의의 값 | 거부 |

### <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| 우선 순위 | 이름 | 포트 | Protocol | 원본 | Destination | 작업 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | 임의의 값 | 임의의 값 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | 임의의 값 | 임의의 값 |  임의의 값 | 인터넷 | Allow |
| 65500    | DenyAllOutBound | 임의의 값 | 임의의 값 | 임의의 값 | 임의의 값 | 거부 |

>[!NOTE]
> Azure AD DS에는 가상 네트워크에서 무제한 아웃 바운드 액세스가 필요 합니다. 가상 네트워크에 대한 아웃 바운드 액세스를 제한 하는 추가 규칙을 만들지 않는 것이 좋습니다.

## <a name="verify-and-edit-existing-security-rules"></a>기존 보안 규칙 확인 및 편집

기존 보안 규칙을 확인 하 고 기본 포트가 열려 있는지 확인 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **네트워크 보안 그룹**을 검색 하 고 선택 합니다.
1. *Contoso.com-NSG*와 같이 관리 되는 도메인과 연결 된 네트워크 보안 그룹을 선택 합니다.
1. **개요** 페이지에 기존 인바운드 및 아웃 바운드 보안 규칙이 표시 됩니다.

    인바운드 및 아웃 바운드 규칙을 검토 하 고 이전 섹션의 필수 규칙 목록과 비교 합니다. 필요한 경우 필요한 트래픽을 차단 하는 사용자 지정 규칙을 선택 하 고 삭제 합니다. 필요한 규칙이 누락 된 경우 다음 섹션에서 규칙을 추가 합니다.

    필요한 트래픽을 허용 하는 규칙을 추가 하거나 삭제 한 후에는 Azure AD DS 관리 되는 도메인의 상태가 2 시간 내에 자동으로 업데이트 되 고 경고가 제거 됩니다.

### <a name="add-a-security-rule"></a>보안 규칙 추가

누락 된 보안 규칙을 추가 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **네트워크 보안 그룹**을 검색 하 고 선택 합니다.
1. *Contoso.com-NSG*와 같이 관리 되는 도메인과 연결 된 네트워크 보안 그룹을 선택 합니다.
1. 왼쪽 패널의 **설정** 에서 추가 해야 하는 규칙에 따라 *인바운드 보안 규칙* 또는 *아웃 바운드 보안 규칙* 을 클릭 합니다.
1. **추가**를 선택한 다음 포트, 프로토콜, 방향 등을 기준으로 필요한 규칙을 만듭니다. 준비가 되 면 **확인**을 선택 합니다.

보안 규칙이 추가 되 고 목록에 표시 되는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계

문제가 계속 되 면 [Azure 지원 요청을 열어][azure-support] 추가 문제 해결 지원을 요청 하세요.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md

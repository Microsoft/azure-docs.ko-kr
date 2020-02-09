---
title: Azure AD Domain Services |에서 암호 정책 만들기 및 사용 Microsoft Docs
description: 세분화 된 암호 정책을 사용 하 여 Azure AD DS 관리 되는 도메인에서 계정 암호를 보호 하 고 제어 하는 방법과 이유를 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: b08c3854ef330081b4c55331cb410c5925f00dec
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512762"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>관리되는 도메인의 암호 및 계정 잠금 정책

Azure Active Directory Domain Services (Azure AD DS)에서 사용자 보안을 관리 하려면 계정 잠금 설정 또는 최소 암호 길이 및 복잡성을 제어 하는 세분화 된 암호 정책을 정의할 수 있습니다. 기본 세분화 된 암호 정책이 만들어지고 Azure AD DS 관리 되는 도메인의 모든 사용자에 게 적용 됩니다. 세부적인 제어를 제공 하 고 특정 비즈니스 또는 규정 준수 요구 사항을 충족 하기 위해 특정 사용자 그룹에 추가 정책을 만들고 적용할 수 있습니다.

이 문서에서는 Active Directory 관리 센터를 사용 하 여 Azure AD DS에서 세분화 된 암호 정책을 만들고 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> 암호 정책은 Azure AD DS 리소스 관리자 배포 모델을 사용 하 여 만든 관리 되는 도메인에만 사용할 수 있습니다. 클래식을 사용 하 여 만든 관리 되는 이전 도메인의 경우 [클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션합니다][migrate-from-classic].

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 다음 리소스와 권한이 필요 합니다.

* 활성화된 Azure 구독.
  * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
  * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
  * 필요한 경우 자습서를 완료 하 여 [Azure Active Directory Domain Services 인스턴스를 만들고 구성][create-azure-ad-ds-instance]합니다.
  * Azure AD DS 인스턴스는 리소스 관리자 배포 모델을 사용 하 여 만들어야 합니다. 필요한 경우 [클래식 가상 네트워크 모델에서 리소스 관리자로 마이그레이션합니다][migrate-from-classic].
* Azure AD DS 관리 되는 도메인에 가입 된 Windows Server 관리 VM입니다.
  * 필요한 경우 자습서를 완료 하 여 [관리 VM을 만듭니다][tutorial-create-management-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="default-password-policy-settings"></a>기본 암호 정책 설정

Fgpp입니다 (세분화 된 암호 정책)를 사용 하면 암호 및 계정 잠금 정책에 대한 특정 제한을 도메인의 다른 사용자에 게 적용할 수 있습니다. 예를 들어 권한 있는 계정을 보호 하기 위해 일반적인 권한 없는 계정 보다 엄격한 계정 잠금 설정을 적용할 수 있습니다. Azure AD DS 관리 되는 도메인 내에서 여러 Fgpp입니다을 만들고 우선 순위를 지정 하 여 사용자에 게 적용할 수 있습니다.

정책은 Azure AD DS 관리 되는 도메인의 그룹 연결을 통해 배포 되 고 변경 내용은 다음 사용자 로그인 시 적용 됩니다. 정책을 변경 하면 이미 잠겨 있는 사용자 계정의 잠금이 해제 되지 않습니다.

암호 정책은 적용 되는 사용자 계정이 생성 된 방법에 따라 약간 다르게 동작 합니다. Azure AD DS에서 사용자 계정을 만드는 방법에는 두 가지가 있습니다.

* 사용자 계정은 Azure AD에서 동기화 할 수 있습니다. 여기에는 Azure에서 직접 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 된 하이브리드 사용자 계정이 포함 됩니다.
    * Azure AD DS의 대부분 사용자 계정은 Azure AD의 동기화 프로세스를 통해 생성 됩니다.
* 사용자 계정은 Azure AD DS 관리 되는 도메인에서 수동으로 만들 수 있으며 Azure AD에 존재 하지 않습니다.

만든 방법에 관계 없이 모든 사용자에 게는 Azure AD DS의 기본 암호 정책에 따라 다음과 같은 계정 잠금 정책이 적용 됩니다.

* **계정 잠금 기간:** 30
* **허용 되는 실패 한 로그온 시도 횟수:** 5
* **다음 시간 후 실패 한 로그온 시도 횟수 다시 설정:** 30 분
* **최대 암호 사용 기간 (수명):** 90 일

이러한 기본 설정을 사용 하면 2 분 내에 잘못 된 암호 5 개가 사용 되는 경우 사용자 계정이 30 분 동안 잠깁니다. 계정은 30분 후 자동으로 잠금 해제됩니다.

계정 잠금은 관리 되는 도메인 내 에서만 발생 합니다. 사용자 계정은 관리 되는 도메인에 대한 실패 한 로그인 시도로 인 한 Azure AD DS에만 잠깁니다. Azure AD 또는 온-프레미스에서 동기화 된 사용자 계정은 해당 원본 디렉터리에서 잠기지 않으며 Azure AD DS에만 해당 됩니다.

최대 암호 사용 기간을 90 일 보다 크게 지정 하는 Azure AD 암호 정책이 있는 경우 해당 암호 사용 기간은 Azure AD DS의 기본 정책에 적용 됩니다. Azure AD DS에서 다른 최대 암호 사용 기간을 정의 하도록 사용자 지정 암호 정책을 구성할 수 있습니다. Azure AD 또는 온-프레미스 AD DS 환경 보다 Azure AD DS 암호 정책에 짧은 최대 암호 사용 기간을 구성 하는 경우 주의 해야 합니다. 이 시나리오에서는 azure AD 또는 온-프레미스 AD DS 환경에서 변경 하 라는 메시지가 표시 되기 전에 사용자의 암호가 Azure AD DS에서 만료 될 수 있습니다.

Azure AD DS 관리 되는 도메인에서 수동으로 만든 사용자 계정의 경우 다음 추가 암호 설정도 기본 정책에서 적용 됩니다. 이러한 설정은 azure AD에서 동기화 된 사용자 계정에 적용 되지 않습니다. 사용자가 Azure AD DS에서 직접 암호를 업데이트할 수 없기 때문입니다.

* **최소 암호 길이 (문자):** 7
* **암호는 복잡성 요구 사항을 충족 해야 합니다.**

계정 잠금 또는 암호 설정은 기본 암호 정책에서 수정할 수 없습니다. 대신, *AAD DC 관리자* 그룹의 구성원은 다음 섹션에 표시 된 것 처럼 사용자 지정 암호 정책을 만들어 기본 기본 제공 정책 보다 우선 적용 되도록 구성할 수 있습니다.

## <a name="create-a-custom-password-policy"></a>사용자 지정 암호 정책 만들기

Azure에서 응용 프로그램을 빌드하고 실행할 때 사용자 지정 암호 정책을 구성 하는 것이 좋습니다. 예를 들어 다른 계정 잠금 정책 설정을 설정 하는 정책을 만들 수 있습니다.

사용자 지정 암호 정책은 Azure AD DS 관리 되는 도메인의 그룹에 적용 됩니다. 이 구성은 기본 정책을 효과적으로 재정의 합니다.

사용자 지정 암호 정책을 만들려면 도메인에 가입 된 VM에서 Active Directory 관리 도구를 사용 합니다. Active Directory 관리 센터를 사용 하면 Ou를 포함 하 여 Azure AD DS 관리 되는 도메인에서 리소스를 보고 편집 하 고 만들 수 있습니다.

> [!NOTE]
> Azure AD DS 관리 되는 도메인에서 사용자 지정 암호 정책을 만들려면 *AAD DC Administrators* 그룹의 구성원 인 사용자 계정에 로그인 해야 합니다.

1. 시작 화면에서 **관리 도구**를 선택 합니다. [관리 VM을 만드는][tutorial-create-management-vm]자습서에 설치 된 사용 가능한 관리 도구 목록이 표시 됩니다.
1. Ou를 만들고 관리 하려면 관리 도구 목록에서 **Active Directory 관리 센터** 을 선택 합니다.
1. 왼쪽 창에서 Azure AD DS 관리 되는 도메인 (예: *aadds.contoso.com*)을 선택 합니다.
1. **시스템** 컨테이너를 열고 **암호 설정 컨테이너**합니다.

    Azure AD DS 관리 되는 도메인에 대한 기본 제공 암호 정책이 표시 됩니다. 이 기본 제공 정책은 수정할 수 없습니다. 대신, 기본 정책을 재정의 하는 사용자 지정 암호 정책을 만듭니다.

    ![Active Directory 관리 센터에서 암호 정책 만들기](./media/password-policy/create-password-policy-adac.png)

1. 오른쪽의 **작업** 패널에서 **새로 만들기 > 암호 설정**을 선택 합니다.
1. **암호 설정 만들기** 대화 상자에서 *Mycustomfgpp*와 같이 정책의 이름을 입력 합니다.
1. 암호 정책이 여러 개 있는 경우 우선 순위가 가장 높은 정책이 사용자에 게 적용 됩니다. 번호가 낮을수록 우선 순위가 높습니다. 기본 암호 정책의 우선 순위는 *200*입니다.

    사용자 지정 암호 정책에 대한 우선 순위를 설정 하 여 기본값을 재정의 합니다 (예: *1)* .

1. 다른 암호 정책 설정을 원하는 대로 편집 합니다. 다음 핵심 사항을 명심 하십시오.

    * Azure AD DS 관리 되는 도메인에서 수동으로 만든 사용자 에게만 암호 복잡성, 나이, 만료 시간 등의 설정을 적용할 수 있습니다.
    * 계정 잠금 설정은 모든 사용자에 게 적용 되지만 Azure AD 자체가 아닌 관리 되는 도메인 내 에서만 적용 됩니다.

    ![사용자 지정 세분화 된 암호 정책 만들기](./media/how-to/custom-fgpp.png)

1. **실수로 삭제 되지 않도록 보호를**선택 취소 합니다. 이 옵션을 선택 하면 FGPP를 저장할 수 없습니다.
1. 에 **직접 적용** 섹션에서 **추가** 단추를 선택 합니다. **사용자 또는 그룹 선택** 대화 상자에서 **위치** 단추를 선택 합니다.

    ![암호 정책을 적용할 사용자 및 그룹을 선택 합니다.](./media/how-to/fgpp-applies-to.png)

1. 암호 정책은 그룹에만 적용할 수 있습니다. **위치** 대화 상자에서 도메인 이름 (예: *aadds.contoso.com*)을 확장 한 다음 **AADDC 사용자**와 같은 OU를 선택 합니다. 적용 하려는 사용자 그룹을 포함 하는 사용자 지정 OU가 있는 경우 해당 OU를 선택 합니다.

    ![그룹이 속하는 OU를 선택 합니다.](./media/how-to/fgpp-container.png)

1. 정책을 적용할 그룹의 이름을 입력 하 고 **이름 확인** 을 선택 하 여 그룹이 존재 하는지 확인 합니다.

    ![FGPP를 적용할 그룹을 검색 하 고 선택 합니다.](./media/how-to/fgpp-apply-group.png)

1. 이제 선택한 그룹의 이름이에 **직접 적용** 됨 섹션에 표시 되 면 **확인** 을 선택 하 여 사용자 지정 암호 정책을 저장 합니다.

## <a name="next-steps"></a>다음 단계

암호 정책 및 Active Directory 관리 센터 사용에 대한 자세한 내용은 다음 문서를 참조 하세요.

* [세분화 되는 암호 정책에 대한 자세한 정보](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [AD 관리 센터를 사용 하 여 세분화 되는 암호 정책 구성](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md

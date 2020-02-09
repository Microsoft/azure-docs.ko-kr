---
title: 결합 된 등록 시작 하기-Azure Active Directory
description: 결합 된 Azure AD Multi-Factor Authentication 및 셀프 서비스 암호 재설정 등록 (미리 보기) 사용
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25c64d9e959b1d68de23e83e26d3495bd3939986
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425178"
---
# <a name="enable-combined-security-information-registration-preview"></a>결합 된 보안 정보 등록 사용 (미리 보기)

새 환경을 사용 하도록 설정 하기 전에 통합 된 [보안 정보 등록 (미리 보기)](concept-registration-mfa-sspr-combined.md) 문서를 검토 하 여이 기능의 기능과 효과를 이해 하 고 있어야 합니다.

![결합 된 보안 정보 등록 고급 환경](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure Multi-Factor Authentication 및 Azure Active Directory (azure AD) 셀프 서비스 암호 재설정에 대 한 결합 된 보안 정보 등록은 Azure AD의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

> [!NOTE]
> 이전 미리 보기를 사용 하 여 보안 정보를 등록 하 고 관리 하는 조직에서는 향상 된 미리 보기 환경을 사용할 수 있도록 아래 단계를 완료 해야 합니다. 스위치를 설정 하지 않는 조직의 경우 2019 년 10 월 8 일에 Microsoft에서 보안 정보를 고급 환경으로 등록 하 고 관리 하기 위해 이전 미리 보기의 사용자를 전환 합니다. 
> 
> 모든 버전의 미리 보기를 사용 하도록 설정 하지 않은 경우 조직에 영향을 주지 않습니다.

## <a name="enable-combined-registration"></a>결합 등록 사용

결합 된 등록을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. 사용자 관리자 또는 전역 관리자 권한으로 Azure Portal에 로그인 합니다.
2. **Azure Active Directory** > **사용자 설정** > **사용자 기능 관리 미리 보기 설정**으로 이동 합니다.
3. **사용자는 미리 보기 기능을 사용 하 여 보안 정보를 등록 하 고 관리할 수 있습니다**. **선택한** 사용자 그룹 또는 **모든** 사용자에 대해 사용을 선택 합니다.

   ![모든 사용자에 대해 결합 된 보안 정보 미리 보기 환경 사용](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!IMPORTANT]
> 2019 년 3 월부터 무료/평가판 Azure AD 테넌트의 사용자를 Multi-Factor Authentication 하 고 SSPR 하는 데 전화 통화 옵션을 사용할 수 없습니다. SMS 메시지는이 변경 내용의 영향을 받지 않습니다. 전화 통화 옵션은 유료 Azure AD 테넌트의 사용자가 계속 사용할 수 있습니다.

> [!NOTE]
> 결합 된 등록을 사용 하도록 설정한 후에는 새 환경을 통해 전화 번호 또는 모바일 앱을 등록 하거나 확인 하는 사용자가 Multi-Factor Authentication 및 SSPR에서 해당 메서드를 사용할 수 있는 경우 Multi-Factor Authentication 및 SSPR에 사용할 수 있습니다. 방침. 이 환경을 사용 하지 않도록 설정 하는 경우 `https://aka.ms/ssprsetup`의 이전 SSPR 등록 페이지로 이동 하는 사용자는 multi-factor authentication을 수행 해야 페이지에 액세스할 수 있습니다.

Internet Explorer에서 사이트 간 할당 목록을 구성한 경우 다음 사이트가 동일한 영역에 있어야 합니다.

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>결합 된 등록에 대 한 조건부 액세스 정책

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록 하는 시기 및 방법에 대 한 보안을 설정할 수 있습니다. 이 미리 보기 기능은 [결합 된 등록 미리 보기](../authentication/concept-registration-mfa-sspr-combined.md)를 사용 하도록 설정한 조직에서 사용할 수 있습니다. 이 기능은 사용자가 HR 온 보 딩 중에 신뢰할 수 있는 네트워크 위치와 같은 중앙 위치에서 Azure Multi-Factor Authentication에 등록 하 고 SSPR 하는 조직에서 사용 하도록 설정할 수 있습니다. 조건부 액세스에서 신뢰할 수 있는 위치를 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건 이란?](../conditional-access/location-condition.md#named-locations) 문서를 참조 하세요.

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구 하는 정책 만들기

다음 정책은 모든 선택한 사용자에 게 적용 되며, 결합 된 등록 환경을 사용 하 여 등록을 시도 하 고, 신뢰할 수 있는 네트워크로 표시 된 위치에서 연결 하지 않는 한 액세스를 차단 합니다.

![보안 정보 등록을 제어 하는 CA 정책 만들기](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. **Azure Portal**에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 이름에이 정책의 이름을 입력 합니다. 예를 들어 **신뢰할 수 있는 네트워크에서 결합 된 보안 정보 등록**
1. **할당**에서 **사용자 및 그룹**을 클릭 하 고이 정책을 적용할 사용자 및 그룹을 선택 합니다.

   > [!WARNING]
   > 사용자는 [결합 된 등록 미리 보기](../authentication/howto-registration-mfa-sspr-combined.md)를 사용 하도록 설정 해야 합니다.

1. **클라우드 앱 또는 작업**에서 **사용자 작업**을 선택 하 고 **보안 정보 등록 (미리 보기)** 을 선택 합니다.
1. **조건** > **위치** 에서
   1. **예** 구성
   1. **모든 위치** 포함
   1. **모든 신뢰할 수 있는 위치** 제외
   1. 위치 블레이드에서 **완료** 를 클릭 합니다.
   1. 조건 블레이드에서 **완료** 를 클릭 합니다.
1. **액세스 제어** > **Grant** 에서
   1. **액세스 차단** 을 클릭 합니다.
   1. 그런 다음 **선택** 을 클릭 합니다.
1. **정책 사용** 을 **켜기** 로 설정 합니다.
1. 그런 다음 **만들기** 를 클릭 합니다.

## <a name="next-steps"></a>다음 단계

[사용자가 인증 방법을 다시 등록 하도록 강제](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Multi-Factor Authentication 및 SSPR에 대해 사용 가능한 방법](concept-authentication-methods.md)

[셀프 서비스 암호 재설정 구성](howto-sspr-deployment.md)

[Azure Multi-Factor Authentication 구성](howto-mfa-getstarted.md)

[결합 된 보안 정보 등록 문제 해결](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure Active Directory 조건부 액세스의 위치 조건은 무엇 인가요?](../conditional-access/location-condition.md)

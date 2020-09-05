---
title: 조건부 액세스-준수 장치 필요-Azure Active Directory
description: 규정 준수 장치를 요구 하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c98269f9851272e8caa9b26ae0c57ed13e9a99f2
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049131"
---
# <a name="conditional-access-require-compliant-devices"></a>조건부 액세스: 준수 장치 필요

Microsoft Intune를 배포한 조직은 장치에서 반환 된 정보를 사용 하 여 다음과 같은 규정 준수 요구 사항을 충족 하는 장치를 식별할 수 있습니다.

* 잠금을 해제 하는 데 PIN 필요
* 장치 암호화 필요
* 최소 또는 최대 운영 체제 버전 필요
* 무단 해제 또는 루 팅 되지 않은 장치 요구

이 정책 준수 정보는 Azure AD에 전달 되며, 조건부 액세스에서 리소스에 대 한 액세스를 부여 하거나 차단 하는 결정을 내릴 수 있습니다. 장치 준수 정책에 대 한 자세한 내용은 [Intune을 사용 하 여 조직의 리소스에 대 한 액세스를 허용 하는 장치에 대 한 규칙 설정](/intune/protect/device-compliance-get-started) 문서에서 찾을 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 리소스에 액세스 하는 장치가 조직의 Intune 준수 정책을 준수 하는 것으로 표시 되도록 요구 하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. **Azure Portal**에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스**로 이동합니다.
1. **새 정책**을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당**에서 **사용자 및 그룹**을 선택합니다.
   1. **포함**에서 **모든 사용자**를 선택합니다.
   1. **제외**에서 **사용자 및 그룹**을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료**를 선택합니다.
1. **클라우드 앱 또는 작업**  >  **포함**아래에서 **모든 클라우드 앱**을 선택 합니다.
   1. 특정 응용 프로그램을 정책에서 제외 해야 하는 경우 제외 된 **클라우드 앱 선택** 의 **제외** 탭에서 해당 응용 프로그램을 선택 하 고 **선택**을 선택할 수 있습니다.
   1. **완료**를 선택합니다.
1. **Conditions**  >  **클라이언트 앱 (미리 보기)** 아래에서  >  **이 정책이 적용 될 클라이언트 앱을 선택**하 고 모든 기본값을 선택 된 채로 두고 **완료**를 선택 합니다.
1. **액세스 제어**  >  **권한 부여**에서 **준수 상태로 표시 된 장치 필요**를 선택 합니다.
   1. **선택**을 선택합니다.
1. 설정을 확인하고 **정책 사용**을 **켜기**로 설정합니다.
1. **만들기**를 선택하여 정책을 만들어 사용하도록 설정합니다.

> [!NOTE]
> 위의 단계를 사용 하 여 **모든 사용자** 와 **모든 클라우드 앱** 에 대 한 **준수 상태로 표시 된 장치 필요** 를 선택 하는 경우에도 Intune에 새 장치를 등록할 수 있습니다. **장치를 규격 컨트롤로 표시** 해야 함은 Intune 등록을 차단 하지 않습니다. 

### <a name="known-behavior"></a>알려진 동작

Windows 7, iOS, Android, macOS 및 일부 타사 웹 브라우저에서 Azure AD는 장치가 Azure AD에 등록 될 때 프로 비전 되는 클라이언트 인증서를 사용 하 여 장치를 식별 합니다. 사용자가 브라우저를 통해 처음으로 로그인 하면 사용자에 게 인증서를 선택 하 라는 메시지가 표시 됩니다. 최종 사용자는이 인증서를 선택 해야 브라우저를 계속 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[디바이스 준수 정책이 Azure AD에서 사용됨](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)

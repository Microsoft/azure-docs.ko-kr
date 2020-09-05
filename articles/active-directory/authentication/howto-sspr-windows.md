---
title: Windows 장치에 대 한 셀프 서비스 암호 재설정-Azure Active Directory
description: Windows 로그인 화면에서 Azure Active Directory 셀프 서비스 암호 재설정을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a51d8c45f652173e5b2b0731d64a8e6f14ee46c7
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717356"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Windows 로그인 화면에서 Azure Active Directory 셀프 서비스 암호 재설정을 사용 하도록 설정

SSPR (셀프 서비스 암호 재설정)는 관리자 또는 지원 센터의 개입 없이 암호를 변경 하거나 다시 설정 하는 기능을 Azure Active Directory (Azure AD)의 사용자에 게 제공 합니다. 일반적으로 사용자는 다른 장치에서 웹 브라우저를 열어 [SSPR 포털](https://aka.ms/sspr)에 액세스 합니다. Windows 7, 8, 8.1 및 10을 실행 하는 컴퓨터에서 환경을 향상 시키기 위해 사용자가 Windows 로그인 화면에서 암호를 재설정할 수 있도록 설정할 수 있습니다.

![SSPR 링크가 표시된 Windows 7 및 10 로그인 화면 예제](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> 이 자습서에서는 기업에서 Windows 장치에 대해 SSPR를 사용 하도록 설정 하는 방법을 관리자에 게 보여 줍니다.
>
> IT 팀이 Windows 장치에서 SSPR를 사용할 수 있는 기능을 사용 하도록 설정 하지 않았거나 로그인 하는 동안 문제가 발생 한 경우 추가 지원이 필요 하면 기술 지원팀에 문의 하세요.

## <a name="general-limitations"></a>일반적인 제한 사항

Windows 로그인 화면에서 SSPR를 사용 하는 경우 다음 제한 사항이 적용 됩니다.

- 현재 원격 데스크톱 또는 Hyper-v 고급 세션에서 암호 재설정을 지원 하지 않습니다.
- 일부 타사 자격 증명 공급자는이 기능에 대 한 문제를 일으키는 것으로 알려져 있습니다.
- [EnableLUA 레지스트리 키](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) 수정을 통해 UAC를 사용하지 않도록 설정하면 문제가 발생합니다.
- 이 기능은 802.1 x 네트워크 인증이 배포 된 네트워크 및 "사용자 로그온 직전에 수행" 옵션에는 적용 되지 않습니다. 802.1 x 네트워크 인증이 배포 된 네트워크의 경우이 기능을 사용 하도록 설정 하는 데 컴퓨터 인증을 사용 하는 것이 좋습니다.
- Hybrid Azure AD에 조인된 컴퓨터에는 새 암호를 사용하고 된 자격 증명을 업데이트하기 위해 도메인 컨트롤러를 향한 네트워크 연결 라인이 있어야 합니다. 즉, 디바이스가 조직의 내부 네트워크에 있거나, 온-프레미스 도메인 컨트롤러에 대한 네트워크 액세스가 있는 VPN에 있어야 합니다.
- 이미지를 사용하는 경우 sysprep을 실행하기 전에 기본 제공 관리자에 대한 웹 캐시를 지운 후에 CopyProfile 단계를 수행해야 합니다. 이 단계에 대한 자세한 내용은 [사용자 지정 기본 사용자 프로필을 사용할 때 성능 저하](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile) 지원 문서에서 찾을 수 있습니다.
- 다음 설정은 Windows 10 장치에서 암호를 사용 하 고 다시 설정 하는 기능을 방해 하는 것으로 알려져 있습니다.
    - V1809 이전 버전의 Windows 10에서 정책에 Ctrl + Alt + Del이 필요한 경우 **암호 재설정** 이 작동 하지 않습니다.
    - 화면 잠금 알림이 해제 된 경우 **암호 재설정** 이 작동 하지 않습니다.
    - *HideFastUserSwitching* 가 enabled 또는 1로 설정 됨
    - *Dontdisplaylastusername* 이 사용 또는 1로 설정 됨
    - *Nolockscreen 화면* 을 사용 또는 1로 설정 합니다.
    - 장치에 *EnableLostMode* 가 설정 되어 있습니다.
    - Explorer.exe는 사용자 지정 셸로 바뀜
- 다음 3개의 특정 설정을 조합하면 이 기능이 작동하지 않을 수 있습니다.
    - 대화형 로그온: CTRL + ALT + DEL = Disabled가 필요하지 않음
    - *Disablelockscreenappnotifications* = 1 또는 사용
    - Windows SKU가 Home 또는 Professional 버전이 아님

## <a name="windows-10-password-reset"></a>Windows 10 암호 재설정

로그인 화면에서 SSPR에 대 한 Windows 10 장치를 구성 하려면 다음 필수 구성 요소 및 구성 단계를 검토 합니다.

### <a name="windows-10-prerequisites"></a>Windows 10 필수 조건

- 관리자는 [Azure Portal에서 AZURE AD 셀프 서비스 암호 재설정을 사용 하도록 설정 해야 합니다](tutorial-enable-sspr.md).
- 사용자는에서이 기능을 사용 하기 전에 SSPR에 등록 해야 합니다. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Windows 로그인 화면에서 SSPR를 사용 하는 경우에는 고유 하지 않습니다. 모든 사용자가 암호를 재설정 하기 전에 인증 연락처 정보를 제공 해야 합니다.
- 네트워크 프록시 요구 사항:
    - 포트 443에서 `passwordreset.microsoftonline.com` 및 `ajax.aspnetcdn.com`
    - Windows 10 장치는 컴퓨터 수준 프록시 구성만 지원 합니다.
- Windows 10, 2018년 4월 업데이트(v1803) 이상의 버전에서 실행하고 디바이스 상태는 다음 중 하나여야 합니다.
    - Azure AD 가입
    - 하이브리드 Azure AD 가입

### <a name="enable-for-windows-10-using-intune"></a>Intune을 사용하여 Windows 10에 사용

Intune을 사용 하 여 로그인 화면에서 SSPR을 사용 하도록 구성 변경 사항을 배포 하는 것이 가장 유연한 방법입니다. Intune을 사용하면 여러분이 정의하는 특정 머신 그룹에 구성 변경 내용을 배포할 수 있습니다. 이 메서드는 디바이스의 Intune 등록이 필요합니다.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune에서 디바이스 구성 정책 만들기

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **Intune**을 선택 합니다.
1. **장치 구성**  >  **프로필**로 이동한 다음 **+ 프로필 만들기** 를 선택 하 여 새 장치 구성 프로필을 만듭니다.
   - **플랫폼** 의 경우 *Windows 10 이상을* 선택 합니다.
   - **프로필 유형**으로 *사용자 지정* 을 선택 합니다.
1. **만들기**를 선택한 다음 프로필에 대 한 의미 있는 이름 (예: *Windows 10 로그인 화면 SSPR* )을 입력 합니다.

    필요에 따라 프로필에 대 한 의미 있는 설명을 입력 하 고 **다음**을 선택 합니다.
1. *구성 설정*에서 **추가** 를 선택 하 고 암호 재설정 링크를 사용 하도록 설정 하려면 다음 oma-uri 설정을 제공 합니다.
      - *SSPR 추가 링크*와 같이 설정에서 수행 하는 작업을 설명 하는 의미 있는 이름을 제공 합니다.
      - 필요에 따라 설정에 대 한 의미 있는 설명을 제공 합니다.
      - **OMA URI**을 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`으로 설정
      - **데이터 형식**을 **정수**로 설정
      - **값**을 **1**로 설정

    **추가**를 선택 하 고 **다음**을 선택 합니다.
1. 정책은 특정 사용자, 장치 또는 그룹에 할당할 수 있습니다. 가장 적합 한 사용자 환경에 맞게 프로필을 할당 하 고, 먼저 장치 테스트 그룹을 선택한 후 **다음**을 선택 합니다.

    자세한 내용은 [Microsoft Intune에서 사용자 및 장치 프로필 할당](/mem/intune/configuration/device-profile-assign)을 참조 하세요.

1. *OS 버전이 Windows 10 Enterprise 인 경우 프로필을 할당 하는*등 사용자 환경에 적용할 수 있는 규칙을 적절 하 게 구성 하 고 **다음**을 선택 합니다.
1. 프로필을 검토 한 후 **만들기**를 선택 합니다.

### <a name="enable-for-windows-10-using-the-registry"></a>레지스트리를 사용하여 Windows 10에 사용

레지스트리 키를 사용 하 여 로그인 화면에서 SSPR을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. 관리자 자격 증명을 사용 하 여 Windows PC에 로그인 합니다.
1. **Windows**  +  **R** 을 눌러 *실행* 대화 상자를 열고 관리자 권한으로 **regedit** 를 실행 합니다.
1. 다음 레지스트리 키를 설정합니다.

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 암호 재설정 문제 해결

Windows 로그인 화면에서 SSPR를 사용 하는 데 문제가 있는 경우 다음 예제 출력과 같이 Azure AD 감사 로그에는 암호 재설정이 발생 한 IP 주소 및 *ClientType* 에 대 한 정보가 포함 됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예제](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

사용자가 Windows 10 장치의 로그인 화면에서 암호를 재설정 하면 라는 낮은 권한 임시 계정이 `defaultuser1` 만들어집니다. 이 계정은 암호 재설정 프로세스를 안전하게 유지하는 데 사용됩니다.

계정 자체는 임의로 생성 된 암호를 포함 하 고, 장치 로그인에는 표시 되지 않으며, 사용자가 암호를 다시 설정한 후 자동으로 제거 됩니다. 여러 `defaultuser` 프로필이 있을 수 있지만 무시해도 무방합니다.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정

로그인 화면에서 SSPR에 대 한 Windows 7, 8 또는 8.1 장치를 구성 하려면 다음 필수 구성 요소 및 구성 단계를 검토 합니다.

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 및 8.1 필수 구성 요소

- 관리자는 [Azure Portal에서 AZURE AD 셀프 서비스 암호 재설정을 사용 하도록 설정 해야 합니다](tutorial-enable-sspr.md).
- 사용자는에서이 기능을 사용 하기 전에 SSPR에 등록 해야 합니다. [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Windows 로그인 화면에서 SSPR를 사용 하는 경우에는 고유 하지 않습니다. 모든 사용자가 암호를 재설정 하기 전에 인증 연락처 정보를 제공 해야 합니다.
- 네트워크 프록시 요구 사항:
    - 포트 443에서 `passwordreset.microsoftonline.com`
- 패치가 적용된 Windows 7 또는 Windows 8.1 운영 체제
- [TLS(전송 계층 보안) 레지스트리 설정](/windows-server/security/tls/tls-registry-settings#tls-12)에 있는 지침을 사용하여 TLS 1.2 사용하도록 설정
- 컴퓨터에 두 개 이상의 타사 자격 증명 공급자를 사용 하는 경우 사용자는 로그인 화면에서 둘 이상의 사용자 프로필을 볼 수 있습니다.

> [!WARNING]
> TLS 1.2를 자동 인식할 뿐만 아니라 사용하도록 설정해야 합니다.

### <a name="install"></a>설치

Windows 7, 8 및 8.1의 경우 로그인 화면에서 SSPR을 사용 하도록 설정 하려면 작은 구성 요소가 컴퓨터에 설치 되어 있어야 합니다. 이 SSPR 구성 요소를 설치 하려면 다음 단계를 완료 합니다.

1. 사용할 Windows 버전에 대해 적절한 설치 관리자를 다운로드합니다.

    소프트웨어 설치 관리자는의 Microsoft 다운로드 센터에서 다운로드할 수 있습니다. [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. 설치하고 설치 관리자를 실행할 머신에 로그인합니다.
1. 설치 후에 다시 부팅하는 것이 좋습니다.
1. 다시 부팅 한 후 로그인 화면에서 사용자를 선택 하 고 "암호 잊음?"를 선택 합니다. 암호 재설정 워크플로를 시작하려면
1. 암호를 재설정하는 화면 단계를 따라 워크플로를 완료합니다.

![예제 Windows 7 "암호 찾기" 클릭 SSPR 흐름](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>자동 설치

다음 명령을 사용 하 여 메시지를 표시 하지 않고 SSPR 구성 요소를 설치 하거나 제거할 수 있습니다.

- 자동 설치의 경우 "msiexec /i SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.
- 자동 제거의 경우 "msiexec /x SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정 문제 해결

Windows 로그인 화면에서 SSPR를 사용 하는 데 문제가 있는 경우 이벤트는 컴퓨터와 Azure AD에 모두 로깅됩니다. Azure AD 이벤트에는 다음 예제 출력과 같이 암호 재설정이 발생 한 IP 주소 및 ClientType에 대 한 정보가 포함 됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예제](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

추가 로깅이 필요한 경우 머신의 레지스트리 키를 변경하여 자세한 정보 표시 로깅을 사용할 수 있습니다. 다음 레지스트리 키 값을 사용 하는 경우에만 문제 해결을 위해 자세한 정보 로깅을 사용 하도록 설정 합니다.

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- 자세한 정보 표시 로깅을 사용하려면 `REG_DWORD: "EnableLogging"`을 만들어 1로 설정합니다.
- 자세한 정보 로깅을 사용하지 않으려면 `REG_DWORD: "EnableLogging"`을 0으로 변경합니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

Windows 장치에 대해 구성 된 SSPR를 사용 하 여 사용자에 대 한 변경 내용을 확인 합니다. 로그인 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요? 다음 예제 스크린샷에서는 SSPR을 사용 하 여 암호를 다시 설정 하는 사용자에 대 한 추가 옵션을 보여 줍니다.

![SSPR 링크가 표시된 Windows 7 및 10 로그인 화면 예제](./media/howto-sspr-windows/windows-reset-password.png)

사용자가 로그인 하려고 하면 로그인 화면에서 셀프 서비스 암호 재설정 환경을 여는 **암호 다시 설정** 또는 **암호 찾기** 링크가 표시 됩니다. 사용자는 이 기능을 사용하면 다른 디바이스를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용 하는 사용자에 대 한 자세한 내용은 [회사 또는 학교 암호 재설정](../user-help/active-directory-passwords-update-your-own-password.md) 에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

사용자 등록 환경을 간소화 하기 위해 [SSPR에 대 한 사용자 인증 연락처 정보를 미리 채울](howto-sspr-authenticationdata.md)수 있습니다.
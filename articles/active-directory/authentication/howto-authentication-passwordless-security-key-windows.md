---
title: Passwordless 보안 키 로그인 Windows-Azure Active Directory
description: FIDO2 보안 키 (미리 보기)를 사용 하 여 Azure Active Directory에 대해 암호 없는 보안 키 로그인을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d70fe8a1fbaee285843bfd76ad2a8076df96b49b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717968"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Azure Active Directory (미리 보기)를 사용 하 여 Windows 10 장치에 암호 없는 보안 키 로그인 사용

이 문서에서는 Windows 10 장치에서 FIDO2 보안 키 기반 암호 없는 인증을 사용 하도록 설정 하는 방법을 집중적으로 설명 합니다. 이 문서의 끝 부분에서는 FIDO2 보안 키를 사용 하 여 azure ad 계정으로 azure ad 및 하이브리드 Azure AD에 가입 된 Windows 10 장치에 로그인 할 수 있습니다.

> [!NOTE]
> FIDO2 보안 키는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대 한 자세한 내용은  [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="requirements"></a>요구 사항

| 디바이스 유형 | Azure AD 가입 | 하이브리드 Azure AD 가입 |
| --- | --- | --- |
| [Azure Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [결합 된 보안 정보 등록 미리 보기](concept-registration-mfa-sspr-combined.md) | X | X |
| 호환 되는 [FIDO2 보안 키](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN에는 Windows 10 버전 1809 이상이 필요 합니다. | X | X |
| [AZURE AD 가입 장치](../devices/concept-azure-ad-join.md) 에는 Windows 10 버전 1903 이상이 필요 합니다. | X |   |
| [하이브리드 AZURE AD 조인 장치](../devices/concept-azure-ad-join-hybrid.md) 에는 Windows 10 버전 2004 이상이 필요 합니다. |   | X |
| Windows Server 2016/2019 도메인 컨트롤러를 완전히 패치 했습니다. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) 버전 1.4.32.0 이상 |   | X |
| [Microsoft Intune](/intune/fundamentals/what-is-intune) (옵션) | X | X |
| 프로 비전 패키지 (옵션) | X | X |
| 그룹 정책 (옵션) |   | X |

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

지원 되지 않는 시나리오는 다음과 같습니다.

- Windows Server Active Directory Domain Services (AD DS) 도메인에 가입 된 (온-프레미스 전용 장치) 배포.
- 보안 키를 사용 하는 RDP, VDI 및 Citrix 시나리오.
- 보안 키를 사용 하는 S/MIME.
- 보안 키를 사용 하 여 "실행" 합니다.
- 보안 키를 사용 하 여 서버에 로그인 합니다.
- 온라인 상태에서 보안 키를 사용 하 여 장치에 로그인 하지 않은 경우에는이를 사용 하 여 오프 라인으로 로그인 하거나 잠금 해제 하는 데 사용할 수 없습니다.
- 여러 Azure AD 계정을 포함 하는 보안 키를 사용 하 여 Windows 10 장치에 로그인 하거나 잠금을 해제 합니다. 이 시나리오에서는 보안 키에 추가 된 마지막 계정을 활용 합니다. WebAuthN를 사용 하면 사용자가 사용 하려는 계정을 선택할 수 있습니다.
- Windows 10 버전 1809을 실행 하는 장치를 잠금 해제 합니다. 최상의 환경을 위해 Windows 10 버전 1903 이상을 사용 합니다.

## <a name="prepare-devices-for-preview"></a>미리 보기용으로 장치 준비

기능 미리 보기 중에 파일럿 할 Azure AD 조인 장치는 Windows 10 버전 1809 이상을 실행 해야 합니다. 최상의 환경은 Windows 10 버전 1903 이상에 있습니다.

하이브리드 Azure AD 조인 장치는 Windows 10 버전 2004 이상을 실행 해야 합니다.

## <a name="enable-security-keys-for-windows-sign-in"></a>Windows 로그인에 대 한 보안 키 사용

조직에서는 조직의 요구 사항에 따라 Windows 로그인에 대 한 보안 키를 사용할 수 있도록 다음 방법 중 하나 이상을 사용 하도록 선택할 수 있습니다.

- [Intune을 사용 하 여 사용](#enable-with-intune)
- [대상 Intune 배포](#targeted-intune-deployment)
- [프로 비전 패키지를 사용 하 여 사용](#enable-with-a-provisioning-package)
- [그룹 정책 사용 하도록 설정 (하이브리드 Azure AD 조인 장치만 해당)](#enable-with-group-policy)

> [!IMPORTANT]
> **하이브리드 AZURE AD 조인 장치** 를 사용 하는 조직은 WINDOWS 10 FIDO2 보안 키 인증을 사용 하기 전에 [온-프레미스 리소스에 FIDO2 인증 사용](howto-authentication-passwordless-security-key-on-premises.md) 문서의 단계 **를 완료 해야 합니다.**
>
> **AZURE AD 조인 장치** 를 사용 하는 조직은 FIDO2 보안 키를 사용 하 여 온-프레미스 리소스에 인증 하려면 장치에서이 작업을 수행 해야 합니다.

### <a name="enable-with-intune"></a>Intune을 사용 하 여 사용

Intune을 사용 하 여 보안 키를 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Microsoft Intune**  >  **장치 등록**  >  **windows 등록**  >  **비즈니스용 windows Hello**  >  **속성**으로 이동 합니다.
1. **설정**아래에서 **로그인에 대 한 보안 키 사용** **을 사용으로 설정 합니다.**

로그인에 대 한 보안 키 구성은 비즈니스용 Windows Hello 구성에 종속 되지 않습니다.

### <a name="targeted-intune-deployment"></a>대상 Intune 배포

특정 장치 그룹을 대상으로 자격 증명 공급자를 사용 하도록 설정 하려면 Intune을 통해 다음 사용자 지정 설정을 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Microsoft Intune**  >  **장치 구성**  >  **프로필**  >  **프로필 만들기**로 이동 합니다.
1. 다음 설정을 사용 하 여 새 프로필을 구성 합니다.
   - 이름: Windows 로그인에 대 한 보안 키
   - 설명: Windows 로그인 중에 FIDO 보안 키를 사용할 수 있습니다.
   - 플랫폼: Windows 10 이상
   - 프로필 유형: 사용자 지정
   - 사용자 지정 OMA-URI 설정:
      - 이름: Windows 로그인에 대 한 FIDO 보안 키를 설정 합니다.
      - OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - 데이터 형식: Integer
      - 값: 1
1. 이 정책은 특정 사용자, 디바이스 또는 그룹에 할당할 수 있습니다. 자세한 내용은 [Microsoft Intune에서 사용자 및 장치 프로필 할당](/intune/device-profile-assign)을 참조 하세요.

![Intune 사용자 지정 장치 구성 정책 만들기](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>프로 비전 패키지를 사용 하 여 사용

Intune에서 관리 되지 않는 장치의 경우 기능을 사용 하도록 프로 비전 패키지를 설치할 수 있습니다. [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)에서 Windows 구성 디자이너 앱을 설치할 수 있습니다. 프로 비전 패키지를 만들려면 다음 단계를 완료 합니다.

1. Windows 구성 디자이너를 시작 합니다.
1. **파일**  >  **새로 만들기 프로젝트**를 선택 합니다.
1. 프로젝트에 이름을 지정 하 고 프로젝트를 만든 경로를 기록한 후 **다음**을 선택 합니다.
1. **선택한 프로젝트 워크플로** 로 *프로 비전 패키지* 를 선택 된 채로 두고 **다음**을 선택 합니다.
1. *모든 Windows 데스크톱 버전* 선택에서 **표시 및 구성할 설정을**선택 하 고 **다음**을 선택 합니다.
1. **마침**을 선택합니다.
1. 새로 만든 프로젝트에서 **런타임 설정**  >  **WindowsHelloForBusiness**  >  **securitykeys**  >  **UseSecurityKeyForSignIn**로 이동 합니다.
1. **UseSecurityKeyForSignIn** 을 *Enabled*로 설정 합니다.
1. **Export**  >  **프로 비전 패키지** 내보내기를 선택 합니다.
1. **빌드** 창에서 **프로 비전 패키지 설명**아래의 기본값을 그대로 두고 **다음**을 선택 합니다.
1. **빌드** 창에서 **프로 비전 패키지에 대 한 보안 세부 정보 선택** 의 기본값을 그대로 두고 **다음**을 선택 합니다.
1. **프로 비전 패키지를 저장할 위치 선택** 에서 **빌드** 창의 경로를 확인 하거나 변경 하 고 **다음**을 선택 합니다.
1. **프로 비전 패키지 빌드** 페이지에서 **빌드** 를 선택 합니다.
1. 만든 두 파일 (*ppkg* 및 *cat*)을 나중에 컴퓨터에 적용할 수 있는 위치에 저장 합니다.
1. 만든 프로 비전 패키지를 적용 하려면 [프로 비전 패키지 적용](/windows/configuration/provisioning-packages/provisioning-apply-package)을 참조 하세요.

> [!NOTE]
> Windows 10 버전 1809을 실행 하는 장치 에서도 공유 PC 모드 (*EnableSharedPCMode*)를 사용 하도록 설정 해야 합니다. 이 기능을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [Windows 10을 사용 하 여 공유 또는 게스트 PC 설정](/windows/configuration/set-up-shared-or-guest-pc)을 참조 하세요.

### <a name="enable-with-group-policy"></a>그룹 정책 사용

**하이브리드 AZURE AD 가입 장치의**경우 조직은 FIDO 보안 키 로그인을 사용 하도록 다음 그룹 정책 설정을 구성할 수 있습니다. 설정은 **컴퓨터 구성 관리 템플릿 컴퓨터 구성**에서 찾을 수 있습니다  >  **Administrative Templates**  >  .**시스템**  >  **로그온**은  >  **보안 키 로그인을 설정**합니다.

- 이 정책을 **사용** 으로 설정 하면 사용자가 보안 키를 사용 하 여 로그인 할 수 있습니다.
- 이 정책을 **사용 안 함** 또는 **구성 되지 않음** 으로 설정 하면 사용자가 보안 키를 사용 하 여 로그인 하지 못하게 됩니다.

이 그룹 정책 설정에는 업데이트 된 버전의 `credentialprovider.admx` 그룹 정책 템플릿이 필요 합니다. 이 새 템플릿은 다음 버전의 Windows Server 및 Windows 10 20H1에서 사용할 수 있습니다. 이 설정은 windows에서 이러한 최신 버전 중 하나를 실행 하는 장치를 사용 하 여 관리 하거나 지원 항목의 지침에 따라 [중앙 저장소를 만들고 관리 하는 방법 그룹 정책 관리 템플릿](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)를 사용 하 여 중앙에서 관리할 수 있습니다.

## <a name="sign-in-with-fido2-security-key"></a>FIDO2 보안 키로 로그인

아래 예제에서는 Bala Sandhu 라는 사용자가 이전 문서의 단계를 사용 하 여 FIDO2 보안 키를 이미 프로 비전 했습니다. [암호 없는 보안 키 로그인을 사용 하도록 설정](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)합니다. 하이브리드 Azure AD 조인 장치에 대해 [온-프레미스 리소스에 대해 암호 없는 보안 키 로그인도 사용 하도록 설정](howto-authentication-passwordless-security-key-on-premises.md)했는지 확인 합니다. Bala는 Windows 10 잠금 화면에서 보안 키 자격 증명 공급자를 선택 하 고 Windows에 로그인 하는 보안 키를 삽입할 수 있습니다.

![Windows 10 잠금 화면에서 보안 키 로그인](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>보안 키 생체 인식, PIN 또는 다시 설정 보안 키를 관리 합니다.

* Windows 10 버전 1903 이상
   * 사용자는 자신의 장치 > **계정**보안 키에서 **Windows 설정을** 열 수 있습니다.  >  **Security Key**
   * 사용자가 PIN을 변경 하거나, 생체 인식을 업데이트 하거나, 보안 키를 다시 설정할 수 있습니다.

## <a name="troubleshooting-and-feedback"></a>문제 해결 및 피드백

이 기능을 미리 보는 동안 피드백을 공유 하거나 문제를 해결 하려는 경우 다음 단계를 사용 하 여 Windows 피드백 허브 앱을 통해 공유 하세요.

1. **피드백 허브** 를 시작 하 고 로그인 했는지 확인 합니다.
1. 다음 분류에 따라 사용자 의견을 제출 합니다.
   - 범주: 보안 및 개인 정보
   - 하위 범주: FIDO
1. 로그를 캡처하려면이 옵션을 사용 하 여 **문제를 다시 만드십시오** .

## <a name="next-steps"></a>다음 단계

[Azure AD 및 하이브리드 Azure AD 조인 장치에 대 한 온-프레미스 리소스에 대 한 액세스 사용](howto-authentication-passwordless-security-key-on-premises.md)

[장치 등록에 대 한 자세한 정보](../devices/overview.md)

[Azure Multi-Factor Authentication에 대해 자세히 알아보기](../authentication/howto-mfa-getstarted.md)
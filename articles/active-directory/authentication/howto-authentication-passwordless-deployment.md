---
title: Azure AD를 사용 하 여 암호 없는 인증 배포 계획
description: Azure Active Directory 암호 없는 인증 구현을 계획 하 고 배포 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d9ca8b7e188a7ed438feb5e2b99c6db22ad12b3
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717152"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Azure Active Directory에서 암호 없는 인증 배포 계획

> [!NOTE]
> 이 배포 계획의 오프 라인 버전을 만들려면 브라우저의 PDF 인쇄 기능을 사용 합니다.

대부분의 사이버 공격은 손상 된 사용자 이름 및 암호로 시작 합니다. 조직에서는 사용자에 게 다음 방법 중 하나를 사용 하도록 요구 하 여 위협에 대처 하려고 합니다.

- 긴 암호
- 복잡 한 암호
- 자주 암호 변경
- MFA(다단계 인증)

Microsoft research는 이러한 노력으로 사용자를 annoy 하 고 지원 비용을 절감할 수 있다는 것을 [보여 줍니다](https://aka.ms/passwordguidance) . 자세한 내용은 [Pa $ $word 중요 하지 않음](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)을 참조 하세요.

### <a name="benefits-of-passwordless-authentication"></a>암호 없는 인증의 이점

- **보안이 강화**되었습니다. 공격 노출 영역으로 암호를 제거 하 여 피싱 및 암호 스프레이 공격의 위험을 줄입니다.
-  **사용자 환경을 개선**합니다. 사용자에 게 어디서 나 데이터에 액세스할 수 있는 편리한 방법을 제공 합니다. 모바일을 통해 Outlook, OneDrive 또는 Office와 같은 응용 프로그램 및 서비스에 쉽게 액세스할 수 있습니다.
-  **강력한 통찰력**. 강력한 로깅 및 감사를 사용 하 여 암호 없는 작업을 사용자에 게 통찰력을 얻으세요.

Passwordless를 사용 하는 경우 암호는 사용자가 알고 있는 것과 다른 항목으로 대체 됩니다. 예를 들어 비즈니스용 Windows Hello는 얼굴 또는 지문과 같은 생체 인식 제스처 또는 네트워크를 통해 전송 되지 않는 장치 특정 PIN을 사용할 수 있습니다.

## <a name="passwordless-authentication-methods"></a>Passwordless 인증 방법
Microsoft는 다양 한 시나리오를 포함 하는 세 가지 암호 없는 인증 옵션을 제공 합니다. 이러한 메서드는 함께 사용할 수 있습니다.

- [비즈니스용 Windows Hello](./concept-authentication-passwordless.md) 는 전용 windows 컴퓨터의 사용자에 게 가장 적합 합니다.
- [FIDO2 보안 키](./concept-authentication-passwordless.md) 를 사용 하는 보안 키 로그인은 휴대폰 사용이 제한 되는 경우 및 높은 권한의 id에 대해 키오스크와 같은 공유 컴퓨터에 로그인 하는 사용자에 게 특히 유용 합니다.
- 휴대폰 [Microsoft Authenticator](./concept-authentication-passwordless.md) 로그인은 모바일 장치를 사용 하는 사용자에 게 암호 없는 옵션을 제공 하는 데 유용 합니다. Authenticator 앱은 사용자가 모든 플랫폼 또는 브라우저에 로그인 할 수 있도록 허용 하 여 모든 iOS 또는 Android 휴대폰을 강력 하 고 암호 없는 자격 증명으로 전환 합니다. 사용자는 휴대폰에 대 한 알림을 받고, 화면에 표시 되는 숫자를 휴대폰에 있는 숫자와 일치 시킨 다음, 생체 인식 데이터 또는 PIN을 사용 하 여 확인 하 여 로그인 합니다.

### <a name="passwordless-authentication-scenarios"></a>Passwordless 인증 시나리오

Microsoft의 암호 없는 인증 방법으로 다양 한 시나리오를 사용할 수 있습니다. 사용자의 조직 요구 사항, 필수 구성 요소 및 각 인증 방법의 기능을 고려 하 여 암호 없는 인증 전략을 선택 합니다. Windows 10 장치를 사용 하는 모든 조직에서 비즈니스용 Windows Hello를 사용 하는 것이 좋습니다. 그런 다음 추가 시나리오에 대 한 휴대폰 로그인 (Microsoft Authenticator 앱 사용) 또는 보안 키를 추가 합니다.

| 시나리오 | 전화 인증 | 보안 키 | 비즈니스용 Windows Hello |
| --- | --- | --- | --- |
| **컴퓨터 로그인**: <br> 할당 된 Windows 10 장치에서 | **아니요** | **예** <br> 생체 인식 사용, PIN | **예**<br>생체 인식 인식 및 PIN 사용 |
| **컴퓨터 로그인**: <br> 공유 Windows 10 장치에서 | **아니요** | **예** <br> 생체 인식 사용, PIN  | **아니요** |
| **웹 앱 로그인**: <br>사용자 전용 컴퓨터에서 | **예** | **예** <br> 컴퓨터 로그인으로 앱에 대 한 Single Sign-On 제공 됨 | **예**<br> 컴퓨터 로그인으로 앱에 대 한 Single Sign-On 제공 됨 |
| **웹 앱 로그인**: <br> 모바일 또는 비 windows 장치에서 | **예** | **아니요** | **아니요** |
| **컴퓨터 로그인**: <br> 비 Windows 컴퓨터 | **아니요** | **아니요** | **아니요** |

조직에 가장 적합 한 방법을 선택 하는 방법에 대 한 자세한 내용은 [암호 없는 방법 결정](./concept-authentication-passwordless.md#choose-a-passwordless-method)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

조직에서 암호 없는 배포를 시작 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

| 필수 조건 | 인증자 앱 | FIDO2 보안 키 |
| --- | --- | --- |
| [Azure multi-factor authentication에 대 한 결합 된 등록과 SSPR (셀프 서비스 암호 재설정)](howto-registration-mfa-sspr-combined.md) 사용 | √ | √ |
| [사용자가 Azure Multi-factor authentication을 수행할 수 있습니다.](howto-mfa-getstarted.md) | √ | √ |
| [사용자가 Azure Multi-factor authentication 및 SSPR에 등록 했습니다.](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [사용자가 모바일 장치를 Azure Active Directory에 등록 했습니다.](../devices/overview.md) | √ |   |
| Microsoft Edge 또는 Mozilla Firefox와 같은 지원 되는 브라우저를 사용 하는 Windows 10 버전 1809 이상 <br> (버전 67 이상). <br> *기본 지원에는 버전 1903 이상을 권장*합니다. |   | √ |
| 호환 되는 FIDO2 보안 키. [Microsoft에서 테스트 하 고 확인](./concept-authentication-passwordless.md) 한 FIDO2 보안 장치 또는 다른 호환 FIDO2 보안 장치를 사용 하 고 있는지 확인 합니다. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>비즈니스용 Windows Hello에 대 한 필수 구성 요소

Windows Hello에 대 한 필수 구성 요소는 온-프레미스, 하이브리드 또는 클라우드 전용 구성에서 배포 하는지 여부에 따라 달라 집니다. 자세한 내용은 [비즈니스용 Windows Hello에 대 한 전체 필수 구성 요소 목록을](/windows/security/identity-protection/hello-for-business/hello-identity-verification)참조 하세요.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

사용자는 Azure multi-factor authentication 등록 흐름의 일부로 암호 없는 메서드를 등록 합니다. 일부 시나리오에서는 휴대폰 또는 보안 키를 사용할 수 없는 경우 다른 등록 된 방법과 함께 사용자 이름 및 암호를 사용 하는 multi-factor authentication을 대체 방법으로 사용할 수 있습니다.

### <a name="licensing"></a>라이선스 
일부 필수 구성 요소에는 프리미엄 구독이 필요할 수도 있지만 암호 없는 인증에 대 한 추가 비용은 없습니다. 자세한 기능 및 라이선스 정보는 [Azure Active Directory 라이선스 페이지](https://azure.microsoft.com/pricing/details/active-directory/)에 있습니다. 

## <a name="develop-a-plan"></a>계획 개발

비즈니스 요구 사항과 각 인증 방법에 대 한 사용 사례를 고려 합니다. 그런 다음 사용자의 요구에 가장 적합 한 방법을 선택 합니다.

### <a name="use-cases"></a>사용 사례

다음 표에서는이 프로젝트 중에 구현 되는 사용 사례를 간략하게 설명 합니다.

| 영역 | Description |
| --- | --- |
| **Access** | Passwordless 로그인은 회사 네트워크 내부 또는 외부의 회사 또는 개인 장치에서 사용할 수 있습니다. |
| **감사** | 사용 현황 데이터는 관리자가 거의 실시간으로 감사 하는 데 사용할 수 있습니다. <br> 사용 현황 데이터는 최소 29 일 마다 회사 시스템에 다운로드 되거나 SIEM 도구가 사용 됩니다. |
| **거버넌스** | 적절 한 인증 방법 및 관련 그룹에 대 한 사용자 할당의 수명 주기를 정의 하 고 모니터링 합니다. |
| **Security** | 적절 한 인증 방법에 대 한 액세스는 사용자 및 그룹 할당을 통해 제어 됩니다. <br> 권한 있는 사용자만 암호 없는 로그인을 사용할 수 있습니다. |
| **성능** | 액세스 할당 전파 타임 라인은 문서화 및 모니터링 됩니다. <br> 사용 편의성을 위해 로그인 시간이 측정 됩니다. |
| **사용자 환경** | 사용자는 모바일 호환성을 인식 합니다. <br> 사용자는 인증자 앱 암호 없는 로그인을 구성할 수 있습니다. |
| **지원** | 사용자는 암호 없는 로그인 문제에 대 한 지원을 찾는 방법을 알고 있습니다. |

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생 하는 경우 일반적으로 영향, 결과 및 책임에 대 한 예상치가 일치 하지 않기 때문입니다. 이러한 문제를 방지 하려면 [올바른 관련자에](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) 게 참여 하 고 프로젝트의 관련자 역할을 잘 이해 하 고 있어야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험을 사전에 전달 하 고, 변경 될 때 그리고 문제가 발생 한 경우 지원을 얻는 방법을 사전에 전달 합니다.

최종 사용자에 대 한 통신은 다음 정보를 포함 해야 합니다.

- [결합 된 보안 등록 환경 사용](howto-authentication-passwordless-phone.md)
- [Microsoft Authenticator 앱 다운로드](../user-help/user-help-auth-app-download-install.md)
- [Microsoft Authenticator 앱에 등록](howto-authentication-passwordless-phone.md)
- [휴대폰으로 로그인](../user-help/user-help-auth-app-sign-in.md)

Microsoft는 Multi-factor authentication [통신 템플릿](https://aka.ms/mfatemplates), 셀프 서비스 암호 재설정 (SSPR) [통신 템플릿](https://www.microsoft.com/download/details.aspx?id=56768)및 [최종 사용자 설명서](../user-help/security-info-setup-signin.md) 를 제공 하 여 커뮤니케이션을 간단 하 게 작성할 수 있도록 합니다. 사용자를 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/)으로 보내 해당 페이지에서 **보안 정보** 링크를 선택하여 직접 등록하게 할 수 있습니다.

### <a name="plan-to-pilot"></a>파일럿 계획

암호 없는 인증을 배포 하는 경우 먼저 하나 이상의 파일럿 그룹을 사용 하도록 설정 해야 합니다. 이 목적을 위해 특별히 [그룹을 만들](../fundamentals/active-directory-groups-create-azure-portal.md) 수 있습니다. 파일럿에 참여할 사용자를 그룹에 추가 합니다. 그런 다음 선택한 그룹에 대해 새 암호 없는 인증 방법을 사용 하도록 설정 합니다.

온-프레미스 디렉터리 또는 Azure AD에서 그룹을 동기화 할 수 있습니다. 파일럿 결과에 만족 하는 경우 모든 사용자에 대해 암호 없는 인증을 사용 하 여 전환할 수 있습니다.

배포 계획 페이지에서 [파일럿에 대 한 모범 사례](https://aka.ms/deploymentplans) 를 참조 하세요.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용 하 여 암호 없는 인증 계획

Microsoft Authenticator 앱은 Google Play 또는 Apple 앱 스토어에서 무료로 다운로드할 수 있습니다. [Microsoft Authenticator 앱 다운로드에 대해 자세히 알아보세요](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). 사용자가 Microsoft Authenticator 앱을 다운로드 하도록 합니다. 지침에 따라 휴대폰 로그인을 사용 하도록 설정 합니다. 

IOS 또는 Android 휴대폰을 강력 하 고 암호 없는 자격 증명으로 바꿉니다. 사용자가 휴대폰에 대 한 알림을 받고, 화면에 표시 되는 숫자를 휴대폰에 있는 것과 일치 하 고, 생체 인식 또는 PIN을 사용 하 여 확인 하 여 모든 플랫폼 또는 브라우저에 로그인 합니다. [Microsoft Authenticator 앱의 작동 방식에 대 한 세부 정보를 확인](./concept-authentication-passwordless.md#microsoft-authenticator-app)하세요.

![Authenticator 앱으로 로그인](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 대 한 기술 고려 사항

**AD FS 통합** -사용자가 Microsoft Authenticator 암호 없는 자격 증명을 사용 하도록 설정 하면 해당 사용자에 대 한 인증에서 승인을 위한 알림을 보내도록 설정 됩니다. 하이브리드 테 넌 트의 사용자는 "대신 암호 사용"을 선택 하지 않는 한 로그인을 위해 ADFS로 전달 되지 않습니다. 이 프로세스는 또한 온-프레미스 조건부 액세스 정책 및 통과 인증 흐름을 무시 합니다. 그러나 *login_hint* 지정 된 경우 사용자가 ADFS에 전달 되 고 옵션을 무시 하 여 암호 없는 자격 증명을 사용 합니다.

**Azure multi-factor authentication 서버** -조직의 온-프레미스 Azure MFA 서버를 통해 multi-factor authentication을 사용 하는 최종 사용자는 단일 암호 없는 휴대폰 로그인 자격 증명을 만들고 사용할 수 있습니다. 사용자가 자격 증명을 사용 하 여 Microsoft Authenticator의 여러 설치 (5 개 이상)를 업그레이드 하려는 경우이 변경으로 인해 오류가 발생할 수 있습니다.

**장치 등록** -암호 없는 인증을 위해 인증자 앱을 사용 하려면 장치가 Azure AD 테 넌 트에 등록 되어 있어야 하 고 공유 장치 일 수 없습니다. 장치는 단일 테 넌 트에만 등록할 수 있습니다. 이 제한은 인증 앱을 사용한 휴대폰 로그인에 회사 또는 학교 계정이 하나만 지원 됨을 의미 합니다.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>FIDO2 보안 키로 암호 없는 인증 계획
보안 키에는 다음과 같은 세 가지 유형의 암호 없는 로그인 배포를 사용할 수 있습니다.

-    지원 되는 브라우저에서 웹 앱 Azure Active Directory
-    Azure Active Directory Windows 10 장치에 연결 됨
-    하이브리드 Azure Active Directory Windows 10 장치에 연결 됨 (미리 보기)
     -    클라우드 기반 및 온-프레미스 리소스 모두에 대 한 액세스를 제공 합니다. 온-프레미스 리소스에 액세스 하는 방법에 대 한 자세한 내용은 [FIDOP2 키를 사용 하 여 온-프레미스 리소스에 SSO](./howto-authentication-passwordless-security-key-on-premises.md) 를 참조 하세요.

**호환 되는 FIDO2 보안 키**를 사용 하도록 설정 해야 합니다. Microsoft [는 FIDO2 키 공급 업체와의 주요 파트너 관계를](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)발표 했습니다.

**AZURE ad 웹 앱 및 AZURE Ad Windows 조인 장치**:

-    Microsoft Edge 또는 Mozilla Firefox (버전 67 이상)와 같은 지원 되는 브라우저를 사용 하는 Windows 10 버전 1809 이상 
-    Windows 10 버전 1809은 FIDO2 로그인을 지원 하며, FIDO2 키 제조업체의 소프트웨어를 배포 해야 할 수 있습니다. 버전 1903 이상을 사용 하는 것이 좋습니다. 

**하이브리드 Azure Active Directory 도메인 가입 장치**: 
-    Windows 10 Insider build 18945 이상
-    Windows Server 2016 또는 2019를 실행 하는 완전히 패치 됨 도메인 서버.
-    최신 버전의 Azure AD Connect

요구 사항의 전체 목록은 Azure Active Directory를 사용 하 [여 Windows 10 장치에 암호 없는 보안 키 로그인 사용](./howto-authentication-passwordless-security-key-windows.md#requirements)을 참조 하세요.


### <a name="security-key-life-cycle"></a>보안 키 수명 주기

보안 키를 사용 하 여 리소스에 액세스 하 고 해당 물리적 장치의 관리를 계획 해야 합니다.

1. **키 배포**: 조직에 키를 프로 비전 하는 방법을 계획 합니다. 중앙 집중식 프로 비전 프로세스가 있거나 최종 사용자가 FIDO 2.0 호환 키를 구매할 수 있습니다.
1. **키 활성화**: 최종 사용자는 보안 키를 직접 활성화 해야 합니다. 최종 사용자는에 보안 키 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 를 등록 하 고 처음 사용할 때 두 번째 요소 (PIN 또는 생체 인식)를 사용 하도록 설정 합니다.
1. **키 사용 안 함**: 보안 키 기능이 미리 보기 단계에 있는 동안 관리자가 사용자 계정에서 키를 제거할 수 있는 방법이 없습니다. 사용자가 제거 해야 합니다. 키를 분실 하거나 도난당 한 경우:
   1. 암호 없는 인증을 사용 하도록 설정 된 그룹에서 사용자를 제거 합니다.
   1. 인증 방법으로 키를 제거 했는지 확인 합니다.
   1. 새 키를 발급 합니다. **키 바꾸기**: 사용자는 동시에 두 개의 보안 키를 사용할 수 있습니다. 보안 키를 교체 하는 경우 사용자가 대체 되는 키도 제거 해야 합니다.

### <a name="enable-windows-10-support"></a>Windows 10 지원 사용

FIDO2 보안 키를 사용 하 여 Windows 10 로그인을 사용 하도록 설정 하려면 Windows 10에서 자격 증명 공급자 기능을 사용 하도록 설정 해야 합니다. 다음 중 하나를 선택합니다.

- [Intune에서 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune 배포를 권장 하는 옵션입니다.
- [프로 비전 패키지를 사용 하 여 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Intune 배포를 수행할 수 없는 경우 관리자는 자격 증명 공급자 기능을 사용 하려면 각 컴퓨터에 패키지를 배포 해야 합니다. 다음 옵션 중 하나를 수행 하 여 패키지를 설치할 수 있습니다.
      - 그룹 정책 또는 Configuration Manager
      - Windows 10 컴퓨터에 로컬 설치
- [그룹 정책 자격 증명 공급자 사용](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 하이브리드 Azure AD 조인 장치에 대해서만 지원 됩니다.

#### <a name="enable-on-premises-integration"></a>온-프레미스 통합 사용

온-프레미스 리소스에 대 한 액세스를 사용 하려면 [온-프레미스 리소스에 대 한 암호 없는 보안 키 로그인을 사용 하도록 설정](howto-authentication-passwordless-security-key-on-premises.md)하는 단계를 수행 합니다 (미리 보기).

> [!IMPORTANT]
> Windows 10 로그인에 대 한 FIDO2 보안 키를 활용 하려면 하이브리드 Azure AD 조인 장치에 대해서도 이러한 단계를 완료 해야 합니다.

### <a name="register-security-keys"></a>보안 키 등록

사용자는 각각의 Azure Active Directory Windows 10 컴퓨터에 보안 키를 등록 해야 합니다.

자세한 내용은 [FIDO2 보안 키의 사용자 등록 및 관리](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)를 참조 하세요.


## <a name="plan-auditing-security-and-testing"></a>감사, 보안 및 테스트 계획
조직 및 규정 준수 프레임 워크를 충족 하는 감사 계획은 배포의 필수적인 부분입니다.

### <a name="auditing-passwordless"></a>암호 없는 감사

Azure AD에는 기술 및 비즈니스 정보를 제공 하는 보고서가 있습니다. 비즈니스 및 기술 응용 프로그램 소유자에 게 조직의 요구 사항에 따라 이러한 보고서를 소유 하 고 사용 하도록 합니다.

Azure Active Directory 포털의 **인증** 방법 섹션에서는 관리자가 암호 없는 자격 증명에 대 한 설정을 사용 하도록 설정 하 고 관리할 수 있습니다.

Azure AD는 다음과 같은 경우 감사 로그에 항목을 추가 합니다.

- 관리자가 인증 방법 섹션에서 변경을 수행 합니다.
- 사용자는 Azure Active Directory 내에서 자격 증명에 대 한 모든 종류의 변경을 수행 합니다.

다음 표에서는 일반적인 보고 시나리오에 대 한 몇 가지 예를 제공 합니다.

|   | 위험 관리 | 생산성 향상 | 거버넌스 및 규정 준수 |
| --- | --- | --- | --- |
| **보고서 유형** | 인증 방법-결합 된 보안 등록을 위해 등록 된 사용자 | 인증 방법 – 앱 알림에 등록 된 사용자 | 로그인: 테 넌 트에 액세스 하는 사용자 및 방법 검토 |
| **잠재적 작업** | 대상 사용자가 아직 등록 되지 않음 | Microsoft Authenticator 앱 또는 보안 키를 도입 하는 드라이브 | 관리자에 대 한 액세스를 취소 하거나 추가 보안 정책을 적용 합니다. |

**AZURE AD는 대부분의 감사 데이터를 30 일 동안 유지** 하 고 azure 관리 포털 또는 API를 통해 데이터를 사용할 수 있도록 하 여 분석 시스템으로 다운로드 합니다. 더 긴 보존이 필요한 경우 [Azure 센티널](../../sentinel/connect-azure-active-directory.md), Splunk 또는 Sumo 논리와 같은 siem 도구에서 로그를 내보내고 사용 합니다. [액세스 및 사용 보고서를 보는 방법에 대해 자세히 알아보세요](../reports-monitoring/overview-reports.md).

사용자는로 이동 하 여 자격 증명을 등록 하 고 관리할 수 있습니다 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . 이 링크는 결합 된 SSPR/Multi-factor authentication 등록 환경을 통해 사용 하도록 설정 된 최종 사용자 자격 증명 관리 환경으로 사용자를 안내 합니다. Azure AD는 FIDO2 보안 장치의 등록과 사용자의 인증 방법에 대 한 변경 내용을 기록 합니다.

### <a name="plan-security"></a>보안 계획
이 롤아웃 계획의 일부로, 모든 권한 있는 관리자 계정에 대해 암호 없는 인증을 사용 하도록 설정 하는 것이 좋습니다.

사용자가 보안 키에서 계정을 사용 하거나 사용 하지 않도록 설정 하거나 Windows 10 컴퓨터의 보안 키에 대 한 두 번째 단계를 다시 설정 하면 항목이 보안 로그에 추가 되 고 다음 이벤트 Id ( *4670* 및 *5382*) 아래에 있습니다.

### <a name="plan-testing"></a>테스트 계획

시나리오 및 채택을 테스트 하는 경우 배포의 각 단계에서 결과가 예상 대로 작동 하는지 확인 합니다.

#### <a name="testing-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱 테스트

다음은 Microsoft Authenticator 앱을 사용한 암호 없는 인증에 대 한 샘플 테스트 사례입니다.

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자가 앱 Microsoft Authenticator 등록 가능 | 사용자가 aka.ms/mysecurityinfo에서 앱을 등록할 수 있습니다. |
| 사용자가 휴대폰 로그인을 사용 하도록 설정할 수 있음 | 회사 계정에 대해 구성 된 휴대폰 로그인 |
| 사용자가 휴대폰 로그인을 사용 하 여 앱에 액세스할 수 있음 | 사용자가 휴대폰 로그인 흐름을 통해 이동 하 고 응용 프로그램에 도달 합니다. |
| Azure Active Directory 포털의 인증 방법 화면에서 암호 없는 로그인 Microsoft Authenticator 해제 하 여 휴대폰 로그인 등록을 테스트 합니다. | 이전에 사용 하도록 설정 된 사용자는 Microsoft Authenticator에서 암호 없는 로그인을 사용할 수 없습니다. |
| Microsoft Authenticator 앱에서 휴대폰 로그인을 제거 하는 중 | Microsoft Authenticator에서 더 이상 사용할 수 없는 회사 계정 |

#### <a name="testing-security-keys"></a>보안 키 테스트

다음은 보안 키를 사용 하 여 암호 없는 인증에 대 한 샘플 테스트 사례입니다.

**Azure Active Directory 연결 된 Windows 10 장치에 대 한 passwordless FIDO 로그인**

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자가 FIDO2 장치 (1809)를 등록할 수 있습니다. | 사용자는 설정 > 계정 > 로그인 옵션 > 보안 키를 사용 하 여 FIDO2 장치를 등록할 수 있습니다. |
| 사용자가 FIDO2 장치 (1809)를 다시 설정할 수 있습니다. | 사용자가 제조업체 소프트웨어를 사용 하 여 FIDO2 장치를 다시 설정할 수 있음 |
| 사용자가 FIDO2 장치 (1809)를 사용 하 여 로그인 할 수 있습니다. | 사용자는 로그인 창에서 보안 키를 선택 하 고 성공적으로 로그인 할 수 있습니다. |
| 사용자가 FIDO2 장치 (1903)를 등록할 수 있습니다. | 사용자는 설정 > 계정 > 로그인 옵션 > 보안 키에 FIDO2 장치를 등록할 수 있습니다. |
| 사용자가 FIDO2 장치 (1903)를 다시 설정할 수 있습니다. | 사용자는 설정 > 계정 > 로그인 옵션 > 보안 키에서 FIDO2 장치를 다시 설정할 수 있습니다. |
| 사용자가 FIDO2 장치 (1903)를 사용 하 여 로그인 할 수 있습니다. | 사용자는 로그인 창에서 보안 키를 선택 하 고 성공적으로 로그인 할 수 있습니다. |

**Azure AD 웹 앱에 대 한 passwordless FIDO 로그인**

| 시나리오 | 예상 결과 |
| --- | --- |
| 사용자는 Microsoft Edge를 사용 하 여 aka.ms/mysecurityinfo에 FIDO2 장치를 등록할 수 있습니다. | 등록 성공 |
| 사용자는 Firefox를 사용 하 여 aka.ms/mysecurityinfo에 FIDO2 장치를 등록할 수 있습니다. | 등록 성공 |
| 사용자는 Microsoft Edge를 사용 하 여 FIDO2 장치를 사용 하 여 OneDrive online에 로그인 할 수 있습니다. | 로그인 성공 |
| 사용자는 Firefox를 사용 하 여 FIDO2 장치를 사용 하 여 OneDrive online에 로그인 할 수 있습니다. | 로그인 성공 |
| Azure Active Directory 포털의 인증 방법 창 내에서 FIDO2 보안 키를 꺼서 FIDO2 장치 등록을 테스트 합니다. | 사용자에 게 보안 키를 사용 하 여 로그인 하 라는 메시지가 표시 됩니다. 사용자가 성공적으로 로그인 하면 "회사 정책에서 다른 방법을 사용 하 여 로그인 해야 합니다." 라는 오류가 표시 됩니다. 그러면 사용자가 다른 방법을 선택 하 여 성공적으로 로그인 할 수 있습니다. 창을 닫은 후 다시 로그인 하 여 동일한 오류 메시지가 표시 되지 않는지 확인 합니다. |

### <a name="plan-for-rollback"></a>롤백 계획

암호 없는은 최종 사용자에 게 미치는 영향을 최소화 하면서 간단한 기능 이지만 롤백해야 할 수 있습니다.

롤백하려면 관리자가 Azure Active Directory 포털에 로그인 하 고 원하는 강력한 인증 방법을 선택한 다음 사용 옵션을 **아니요**로 변경 해야 합니다. 이 프로세스는 모든 사용자에 대해 암호 없는 기능을 해제 합니다.

FIDO2 보안 장치를 이미 등록 한 사용자에 게 다음 로그인 시 보안 장치를 사용 하 라는 메시지가 표시 되 면 다음 오류가 표시 됩니다.

![다른 로그인 방법 선택](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>암호 없는 인증 배포 및 문제 해결

아래에서 선택한 방법에 따라 정렬 된 단계를 따르세요.

### <a name="required-administrative-roles"></a>필요한 관리 역할

| Azure AD 역할 | Description |
| --- | --- |
| 전역 관리자|결합 된 등록 환경을 구현할 수 있는 최소 권한 있는 역할입니다. |
| 인증 관리자 | 인증 방법을 구현 하 고 관리할 수 있는 최소 권한 있는 역할입니다. |
| 사용자 | 장치에서 Authenticator 앱을 구성 하거나 웹 또는 Windows 10 로그인에 대 한 보안 키 장치를 등록 하기 위한 최소 권한 있는 역할입니다. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 사용 하 여 휴대폰 로그인 배포

[Microsoft Authenticator 앱에서 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md) 문서의 단계를 수행 하 여 조직에서 Microsoft Authenticator 앱을 암호 없는 인증 방법으로 사용 하도록 설정 합니다.

### <a name="deploy-fido2-security-key-sign-in"></a>FIDO2 보안 키 로그인 배포

[Azure AD에 대해 암호 없는 보안 키 로그인 사용](howto-authentication-passwordless-security-key.md) 문서의 단계를 수행 하 여 FIDO2 보안 키를 암호 없는 인증 방법으로 사용 하도록 설정 합니다.

### <a name="troubleshoot-phone-sign-in"></a>휴대폰 로그인 문제 해결

| 시나리오 | 솔루션 |
| --- | --- |
| 사용자는 결합 된 등록을 수행할 수 없습니다. | [결합 된 등록이](concept-registration-mfa-sspr-combined.md) 활성화 되어 있는지 확인 합니다. |
| 사용자가 휴대폰 로그인 인증자 앱을 사용 하도록 설정할 수 없습니다. | 사용자가 배포 범위에 있는지 확인 하세요. |
| 사용자가 암호 없는 인증에 대 한 범위 내에 있지 않지만, 암호 없는 로그인 옵션으로 제공 되며,이 옵션은 완료할 수 없습니다. | 이 시나리오는 사용자가 정책 만들기 전에 응용 프로그램에서 휴대폰 로그인을 사용 하도록 설정한 경우에 발생 합니다. <br> *로그인을 사용 하도록 설정 하려면*: 암호 없는 로그인에 사용 하도록 설정 된 사용자 범위에 사용자를 추가 합니다. <br> *로그인을 차단 하려면*: 사용자가 해당 응용 프로그램에서 자격 증명을 제거 하도록 합니다. |

### <a name="troubleshoot-security-key-sign-in"></a>보안 키 로그인 문제 해결

| 시나리오 | 솔루션 |
| --- | --- |
| 사용자는 결합 된 등록을 수행할 수 없습니다. | [결합 된 등록이](concept-registration-mfa-sspr-combined.md) 활성화 되어 있는지 확인 합니다. |
| 사용자는 [보안 설정](https://aka.ms/mysecurityinfo)에서 보안 키를 추가할 수 없습니다. | [보안 키](howto-authentication-passwordless-security-key.md) 를 사용할 수 있는지 확인 합니다. |
| 사용자가 Windows 10 로그인 옵션에서 보안 키를 추가할 수 없습니다. | [Windows 로그인에 대 한 보안 키를 확인 합니다.](./concept-authentication-passwordless.md) |
| **오류 메시지**:이 브라우저나 OS에서 FIDO2 보안 키를 지원 하지 않습니다. | Passwordless FIDO2 보안 장치는 Windows 10 버전 1809 이상에서 지원 되는 브라우저 (Microsoft Edge, Firefox 버전 67)에만 등록할 수 있습니다. |
| **오류 메시지**: 회사 정책에서 다른 방법을 사용 하 여 로그인 해야 합니다. | 테 넌 트에서 확실 하지 않은 보안 키를 사용할 수 있습니다. |
| 사용자가 Windows 10 버전 1809에서 내 보안 키를 관리할 수 없음 | 버전 1809에서는 FIDO2 key 공급 업체에서 제공 하는 보안 키 관리 소프트웨어를 사용 해야 합니다. 공급 업체에 지원을 문의 하세요. |
| FIDO2 보안 키에 결함이 있을 수 있습니다. | 으로 이동 하 고 [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/) , 테스트 계정에 대 한 자격 증명을 입력 하 고, 의심 스러운 보안 키를 연결 하 고, **+** 화면 오른쪽 위에 있는 단추를 선택 하 고, 만들기를 클릭 하 고, 만들기 프로세스를 진행 합니다. 이 시나리오에서 오류가 발생 하면 장치에 결함이 있을 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- [Azure AD에 로그인 하는 데 암호 없는 보안 키 사용](howto-authentication-passwordless-security-key.md)
- [Microsoft Authenticator 앱에서 암호 없는 로그인을 사용 하도록 설정](howto-authentication-passwordless-phone.md)
- [인증 방법 사용 & insights에 대해 자세히 알아보기](howto-authentication-methods-usage-insights.md)
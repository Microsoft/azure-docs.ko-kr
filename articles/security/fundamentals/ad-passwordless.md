---
title: Azure AD로 passwordless 인증
titleSuffix: Active Directory
description: 이 가이드는 ceo, cio, cisos, 최고 id 설계자, 엔터프라이즈 설계자, 보안 및 IT 의사 결정권자가 Azure Active Directory 구현에 대해 암호 없는 인증 방법을 선택 하는 데 도움이 됩니다.
keywords: passwordless, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 5fc6a4a23573995cf791a21ec7cf3a7d68d048e8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064481"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>Azure Active Directory 암호가 없는 전 세계

암호와의 관계를 나눌 시간입니다. 이전에는 암호가 양호 하지만 오늘날의 디지털 작업 영역에서는 해커가 비교적 쉽게 공격을 받을 수 있게 되었습니다. 해커가 암호를 선호 하므로 Azure Active Directory (Azure AD)에서 가장 일반적으로 거부 되는 암호에 연도, 월, 계절 또는 로컬 스포츠 팀과 같은 용어가 포함 되는 것을 고려할 수 있습니다. 또한 [연구](https://aka.ms/passwordguidance) 에는 길이 요구 사항, 복잡성 요구 사항, 변경 빈도 등의 암호 관리에 대 한 기존 권장 사항이 인간 특성과 관련 된 다양 한 이유로 오히려 역효과가 일어날 되어 있습니다.

사용자 계정을 손상 시키는 데 일반적으로 사용 되는 세 가지 유형의 공격은 암호 스프레이, 피싱 및 위반 재생입니다. [스마트 잠금](../../active-directory/authentication/howto-password-smart-lockout.md), [금지 된 암호](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)및 [암호 보호](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) 와 같은 Azure AD 기능은 이러한 종류의 공격 으로부터 보호 하는 데 도움이 됩니다. 마찬가지로 MFA ( [multi-factor authentication](../../active-directory/authentication/concept-mfa-howitworks.md) ) 또는 2 단계 인증을 구현 하는 경우 두 번째 형태의 인증을 요구 하 여 추가 보안을 제공 합니다. 그러나 장기 실행에서 암호 없는 솔루션은 가장 안전한 인증 방법을 보장 하기 위한 최상의 솔루션입니다.

이 문서는 Microsoft의 암호 없는 솔루션을 이해 하 고 구현 하 고 다음 옵션 중 하나 이상을 선택 하는 데 도움이 되는 여행의 시작입니다.

* **비즈니스용 Windows Hello**. Windows 10에서 비즈니스용 Windows Hello는 Pc 및 모바일 장치에서 암호를 강력한 2 단계 인증으로 바꿉니다. 이 인증은 장치에 연결 되 고 생체 인식 또는 PIN을 사용 하는 새로운 유형의 사용자 자격 증명으로 구성 됩니다.

* **Microsoft Authenticator를 사용 하 여 Passwordless 로그인**합니다. Microsoft Authenticator 앱은 암호를 사용 하지 않고 Azure AD 계정에 로그인 하는 데 사용할 수 있습니다. 비즈니스용 Windows Hello의 기술과 마찬가지로 Microsoft Authenticator는 키 기반 인증을 사용 하 여 장치에 연결 되 고 생체 인식 또는 PIN을 사용 하는 사용자 자격 증명을 사용 하도록 설정 합니다.

* **보안 키를 FIDO2**합니다. FIDO2 모든 웹 사이트에서 고유한 암호화 로그인 자격 증명을 제공 하 고 Windows Hello 또는 외부 보안 키와 같은 로컬 장치에 저장 됩니다. 이러한 보안 키는 피싱, 암호 도난 및 재생 공격의 위험에 대 한 저항력이 있습니다. 이 솔루션은 생체 인식 또는 PIN을 통해 사용자 확인을 결합 하 여 최신 보안 요구 사항을 충족 하는 2 단계 인증입니다.

## <a name="the-future-of-passwordless-authentication"></a>암호 없는 인증의 미래

이러한 일, 은행, 신용 카드 회사 및 기타 조직 및 온라인 서비스는 사용자의 암호를 두 번 확인 하도록 요구 하 여 계정을 보호 하는 경우가 많습니다. 암호를 사용 하 여 한 번은 휴대폰, 텍스트 또는 앱에 의해 다시 사용 됩니다. Multi-factor authentication은 공유, 도난 또는 추측 중인 암호의 보안 문제를 해결 하는 반면 이러한 암호를 기억할 시도의 불편 요인을 해결 하지는 않습니다. 오늘날의 클라우드 연대의 사용자 및 조직은 매우 안전 *하 고* 편리한 인증 방법입니다.

![편리한 vs 보안](./media/ad-passwordless/azure-ad-pwdless-image1.png)

비즈니스용 Windows Hello, Microsoft Authenticator에 대 한 암호 없는 로그인 및 FIDO2 보안 키는 모두 매우 안전 하 고 편리 하 게 사용할 수 있는 인증 방법을 사용자에 게 제공 하는 간단한 공통 아키텍처를 공유 합니다. 세 가지 모두 공개/개인 키 기술을 기반으로 하며, 생체 인식 또는 PIN과 같은 로컬 제스처와 단일 장치에 바인딩되고 안전 하 게 저장 되 고 공유 되지 않는 개인 키가 필요 합니다.

## <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

Windows 10에서 비즈니스용 Windows Hello는 Pc 및 장치에서 강력한 2 단계 인증으로 암호를 바꿉니다. 인증은 장치에 연결 되 고 사용자가 Azure AD 뿐만 아니라 온-프레미스 Active Directory에 인증할 수 있도록 하는 생체 인식 제스처 또는 PIN을 사용 하는 새로운 유형의 사용자 자격 증명으로 구성 됩니다. 비즈니스용 Windows Hello를 사용 하 여 장치에 로그인 하는 것은 간단 합니다. 지문 또는 얼굴 인식과 같은 PIN 또는 생체 인식 제스처를 사용 합니다.

### <a name="windows-hello-for-business-scenarios"></a>비즈니스용 Windows Hello 시나리오

비즈니스용 windows Hello는 고유의 지정 된 Windows PC가 있는 정보 근로자에 게 적합 합니다. 생체 인식 및 자격 증명은 사용자 PC와 직접 연결 되어 소유자 이외의 사용자가 액세스 하지 못하도록 합니다. 비즈니스용 Windows Hello는 PKI 통합 및 기본 제공 되는 SSO (Single Sign-On) 지원을 통해 온-프레미스 및 클라우드에서 회사 리소스에 원활 하 게 액세스할 수 있는 간단 하 고 편리한 방법을 제공 합니다.

### <a name="windows-hello-for-business-deployment-considerations"></a>비즈니스용 Windows Hello 배포 고려 사항

비즈니스용 Windows Hello는 여러 구성 요소를 사용 하 여 장치 등록, 프로 비전 및 인증을 수행 하는 분산 시스템입니다. 따라서 배포를 위해서는 조직 내의 여러 팀에서 적절 하 게 계획 해야 합니다. 비즈니스용 Windows Hello [계획 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) 를 사용 하 여 비즈니스용 windows hello 배포 유형과 고려해 야 할 옵션에 대 한 결정을 내리는 데 도움을 받을 수 있습니다.

비즈니스용 Windows Hello를 배포할 때 선택할 수 있는 여러 옵션이 있습니다. 여러 옵션을 제공 하면 거의 모든 조직에서 비즈니스용 Windows Hello를 배포할 수 있습니다. 지원 되는 배포 유형은 다음과 같습니다.

* [하이브리드 Azure AD 조인 키 신뢰 배포](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [하이브리드 Azure AD 조인 인증서 신뢰 배포](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Azure AD 조인 Single Sign-on 배포 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [온-프레미스 키 신뢰 배포](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [온-프레미스 인증서 신뢰 배포](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

많은 옵션을 제공 하면 배포가 복잡해 보입니다. 그러나 대부분의 조직에서는 비즈니스용 Windows Hello 배포가 종속 된 대부분의 인프라를 이미 구현 했다는 것을 알 수 있습니다. 에 관계 없이 비즈니스용 Windows Hello는 분산 시스템 이므로 적절 한 계획을 수립 하는 것이 좋습니다.

특정 조직에 가장 적합 한 배포 모델을 결정 하는 데 도움이 되도록 [비즈니스용 Windows Hello 배포 계획](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) 을 읽어 보는 것이 좋습니다. 그런 다음, 수행 하는 계획에 따라 기존 환경에서 비즈니스용 Windows Hello를 성공적으로 배포 하기 위해 [비즈니스용 Windows Hello 배포 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) 를 참조 하세요.

### <a name="how-windows-hello-for-business-works"></a>비즈니스용 Windows Hello의 작동 방식

#### <a name="user-sets-up-windows-hello-for-business"></a>사용자가 비즈니스용 Windows Hello를 설정 합니다.

등록 하는 동안 사용자에 대 한 초기 2 단계 인증 후에는 사용자의 장치에 Windows Hello가 설정 되며, 사용자에 게 사용자에 게 사용자에 게 사용자에 게 지문 또는 얼굴 인식과 같은 생체 인식 또는 PIN을 설정 하 라는 메시지가 표시 됩니다. 설정 되 면 사용자가 해당 id를 확인 하는 제스처를 제공 합니다. Windows에서는 Windows Hello를 사용 하 여 사용자를 인증 합니다.

Windows 10 장치의 기능에 따라 TPM (하드웨어 신뢰할 수 있는 플랫폼 모듈) 또는 소프트웨어 TPM 이라고 하는 기본 제공 보안 enclave 제공 됩니다. TPM은 개인 키를 저장 합니다 .이 키는 잠금 해제를 위해 얼굴, 지문 또는 PIN이 필요 합니다. 생체 인식 데이터는 로밍되지 않으며 외부 장치나 서버로 전송 되지 않습니다. Windows Hello는 장치에 생체 인식 식별 데이터만 저장 하므로 생체 인식 데이터를 도용 하기 위해 공격자가 손상 시킬 수 있는 단일 컬렉션 지점이 없습니다.

> [!TIP]
> 표면에서 PIN은 암호와 유사 하지만 실제로는 더 안전 합니다. 암호와 PIN의 중요 한 차이점은 PIN이 설정 된 특정 장치에 연결 되어 있다는 것입니다. 암호를 도용 하는 사람은 어디에서 나 계정에 로그인 할 수 있습니다. 하지만 PIN을 도용 하는 경우 물리적 장치를 도용 해야 합니다. 또한 PIN은 장치에 대 한 로컬 이므로 아무 곳에도 전송 되지 않으므로 서버에서 전송 하거나 도난당 할 수 없습니다.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>로그인에 비즈니스용 Windows Hello를 사용 하는 사용자

비즈니스용 Windows Hello 생체 인식 및 Pin은 비대칭 (공개/개인 키) 암호화를 사용 하 여 인증 합니다. 인증 하는 동안 암호화는 MiTM (메시지 가로채기 (man-in-the-middle) 공격으로 인해 결과 보안 토큰 또는 아티팩트를 도용 하 고 다른 위치에서 재생할 수 없도록 인증 프로세스를 보호 하는 TLS 세션 키에 연결 됩니다.

비즈니스용 Windows Hello는 Azure AD 및 Active Directory 리소스에 대해 사용자를 인증 하는 편리한 로그인 환경을 제공 합니다. Azure AD 조인 장치는 로그인 중에 Azure에 인증 하 고 선택적으로 Active Directory에 인증할 수 있습니다. 하이브리드 Azure Active Directory 연결 된 장치는 로그인 하는 동안 Active Directory에 인증 하 고 백그라운드에서 Azure Active Directory에 인증 합니다.

![원본 이미지를 참조 하세요.](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

다음 단계에서는 Azure Active Directory에 대 한 로그인 인증을 보여 줍니다.

![Windows 10 잠금 화면](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. 사용자가 생체 인식 또는 PIN 제스처를 사용 하 여 Windows에 로그인 합니다. 이 제스처는 비즈니스용 Windows Hello 개인 키의 잠금을 해제 하며 클라우드 AP 공급자 라고 하는 클라우드 인증 보안 지원 공급자에 게 전송 됩니다.

2. 클라우드 AP 공급자는 Azure Active Directory에서 nonce를 요청 합니다.

3. Azure AD는 5 분 동안 유효한 nonce를 반환 합니다.

4. 클라우드 AP 공급자는 사용자의 개인 키를 사용 하 여 nonce에 서명 하 고 Azure Active Directory 서명 된 nonce를 반환 합니다.

5. Azure Active Directory는 nonce 서명에 대해 사용자의 안전 하 게 등록 된 공개 키를 사용 하 여 서명 된 nonce의 유효성을 검사 합니다. 서명 유효성을 검사 한 후 Azure AD는 반환 된 서명 된 nonce의 유효성을 검사 합니다. Nonce의 유효성을 검사 한 후 Azure AD는 장치의 전송 키로 암호화 된 PRT를 생성 하 고 클라우드 AP 공급자에 게 반환 합니다.

6. 클라우드 AP 공급자는 세션 키를 사용 하 여 암호화 된 PRT를 수신 합니다. 클라우드 AP 공급자는 장치의 개인 전송 키를 사용 하 여 세션 키의 암호를 해독 하 고 장치의 TPM을 사용 하 여 세션 키를 보호 합니다.

7. 클라우드 AP 공급자는 Windows에 대 한 성공적인 인증 응답을 반환 합니다. 그 후에는 사용자가 다시 인증 하지 않고도 (SSO) 클라우드 및 온-프레미스 응용 프로그램 뿐만 아니라 Windows에 액세스할 수 있습니다.

비즈니스용 Windows Hello와 관련 된 다른 시나리오의 인증 프로세스를 자세히 확인 하려면 [비즈니스용 Windows hello 및 인증](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key)을 참조 하세요.

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>사용자가 비즈니스용 Windows Hello 자격 증명을 관리 합니다.

[MICROSOFT pin 재설정 서비스](/windows/security/identity-protection/hello-for-business/hello-feature-pin-reset) 는 사용자가 필요에 따라 PIN을 다시 설정할 수 있도록 하는 Azure AD의 기능입니다. 관리자는 그룹 정책, Microsoft Intune 또는 호환 되는 MDM을 사용 하 여 Microsoft PIN 재설정 서비스를 안전 하 게 사용 하도록 Windows 10 장치를 구성할 수 있습니다. 다시 등록 합니다.

사용자가 암호를 사용 하 여 대체 해야 하는 경우도 있습니다. SSPR ( [셀프 서비스 암호 재설정](../../active-directory/authentication/howto-sspr-deployment.md) )는 IT 직원에 게 연락 하지 않고도 사용자가 암호를 재설정할 수 있도록 하는 또 다른 Azure AD 기능입니다. 사용자는 서비스를 사용 하기 전에 셀프 서비스 암호 재설정을 위해 등록 하거나 등록 해야 합니다. 등록 하는 동안 사용자는 조직에서 사용 하도록 설정 된 하나 이상의 인증 방법을 선택 합니다. SSPR를 사용 하면 사용자가 신속 하 게 차단이 해제 되 고 시간이 나 시간에 관계 없이 작업을 계속할 수 있습니다. 사용자가 자신을 차단 해제할 수 있도록 하 여 조직에서는 일반적인 암호 관련 문제에 대 한 비 생산성 시간 및 지원 비용을 줄일 수 있습니다.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Microsoft Authenticator를 사용 하 여 passwordless 로그인

Microsoft Authenticator와 암호 없는 로그인은 휴대폰 로그인을 사용 하 여 Azure AD 계정에 로그인 하는 데 사용할 수 있는 또 다른 암호 없는 솔루션입니다. 사용자가 알고 있는 항목을 제공 하 여 본인 여부를 확인 해야 하지만 휴대폰 로그인을 통해 암호 입력을 건너뛰고 사용자의 지문, 얼굴 또는 PIN을 사용 하 여 모바일 장치에서 모든 id 확인을 수행할 수 있습니다.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator 암호 없는 시나리오

Microsoft Authenticator 앱을 사용 하면 사용자가 자신의 id를 확인 하 고 회사 또는 개인 계정에 인증할 수 있습니다. 이를 사용 하 여 일회용 암호 또는 푸시 알림과 함께 암호를 보강 하거나 암호의 필요성을 완전히 바꿀 수도 있습니다. 사용자는 암호를 사용 하는 대신 지문 검색, 얼굴 또는 조리개 인식 또는 PIN을 통해 휴대폰을 사용 하 여 자신의 id를 확인 합니다. Windows Hello에서 사용 하는 것과 비슷한 보안 기술을 기반으로 하는이 도구는 모바일 장치의 간단한 앱에 패키지 되어 사용자에 게 편리한 옵션을 제공 합니다. Microsoft Authenticator 앱은 Android 및 iOS에서 사용할 수 있습니다.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator 배포 고려 사항

Microsoft Authenticator 앱을 사용 하 여 Azure AD에 암호 없는 로그인을 수행 하기 위한 필수 구성 요소는 다음과 같습니다.

* Azure Multi-Factor Authentication 최종 사용자를 사용할 수 있습니다.

* 앱 배포를 용이 하 게 하려면 Microsoft Intune 또는 타사 MDM (모바일 장치 관리) 솔루션을 사용 하 여 장치를 등록 하는 것이 좋습니다.

관리자는 이러한 요구 사항을 충족 한다고 가정 하 고 [Windows PowerShell](../../active-directory/authentication/howto-authentication-passwordless-phone.md) 을 사용 하 여 테넌트에서 암호 없는 휴대폰 로그인을 사용 하도록 설정 합니다. 테넌트에서 휴대폰 로그인을 사용 하도록 설정 하면 최종 사용자가 앱의 **계정** 화면에서 회사 또는 학교 계정을 선택한 다음 **휴대폰 로그인 사용**을 선택 하 여 휴대폰을 사용 하 여 로그인 하도록 선택할 수 있습니다.

관리자가 암호 없는 로그인을 사용 하도록 설정한 경우 최종 사용자는 다음 요구 사항을 충족 해야 합니다.

* Azure Multi-Factor Authentication에 등록 됨

* IOS 8.0 이상을 실행 하는 장치에 설치 된 최신 버전의 Microsoft Authenticator 또는 Android 6.0 이상

* 앱에 푸시 알림이 추가 된 회사 또는 학교 계정

계정을 잠글 수 없거나 새 장치에서 계정을 다시 만들어야 하는 것을 방지 하려면 Microsoft Authenticator을 사용 하 여 [계정 자격 증명](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) 을 클라우드로 백업 하는 것이 좋습니다. 백업 후에는 앱을 사용하여 정보를 새 디바이스에 복구하면 계정이 잠기거나 계정을 다시 만들어야 하는 문제를 피할 수 있습니다.

대부분의 사용자는 암호를 사용 하 여 인증 하는 데 익숙해져 있으므로 조직에서이 프로세스와 관련 된 사용자를 교육할 하는 것이 중요 합니다. 인식을 통해 사용자는 Microsoft Authenticator 앱을 사용한 로그인과 관련 된 [문제](../../active-directory/authentication/howto-authentication-passwordless-phone.md) 에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다.

> [!NOTE]
> 이 솔루션에 대 한 잠재적인 실패 지점은 로밍 사용자가 인터넷에 연결 되지 않은 위치에 있는 경우입니다. FIDO2 보안 키 및 비즈니스용 Windows Hello에는 동일한 제한이 적용 되지 않습니다.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Microsoft Authenticator에 대 한 암호의 로그인이 작동 하는 방식

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>사용자가 Microsoft Authenticator를 사용 하 여 암호 없는 로그인 설정

Azure AD 계정에 로그인 하는 데 Microsoft Authenticator 앱을 암호 없는 솔루션으로 사용 하려면 먼저 관리자와 최종 사용자 모두에 게 단계를 수행 해야 합니다.

먼저 관리자가 Windows PowerShell을 사용 하 여 테넌트에서 [앱을 자격 증명으로 사용 하도록 설정](../../active-directory/authentication/howto-authentication-passwordless-phone.md) 해야 합니다. 또한 관리자는 azure MFA (azure Multi-Factor Authentication)에 대 한 최종 사용자를 사용 하도록 설정 하 고 [확인 방법](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods)중 하나로 Microsoft Authenticator 앱을 구성 해야 합니다.

최종 사용자는 Microsoft Authenticator 앱을 다운로드 하 여 [설치](../../active-directory/user-help/user-help-auth-app-download-install.md) 하 고 Microsoft Authenticator 앱을 확인 방법 중 하나로 사용 하도록 [계정을 설정](../../active-directory/user-help/security-info-setup-auth-app.md) 해야 합니다.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>암호 없는 로그인에 Microsoft Authenticator를 사용 하는 사용자

암호를 사용하지 않고 Microsoft Authenticator 앱을 사용하여 모든 Azure AD 계정에 로그인할 수 있습니다. Windows 10 잠금 화면에는 로그인 옵션으로 Microsoft Authenticator 앱이 포함 되어 있지 않지만 사용자는 계속 해 서 사용자의 사용자 이름을 입력 하 고 모바일 장치에서 푸시 알림을 수신 하 여 현재 상태를 확인할 수 있습니다. 사용자가 로그인 화면에서 숫자를 일치 시켜 확인 한 후 얼굴 검색, 지문 또는 PIN을 제공 하 여 개인 키의 잠금을 해제 하 고 인증을 완료 합니다. 이 multi-factor authentication 방법은 암호 보다 더 안전 하며 암호와 코드를 입력 하는 것 보다 더 편리 합니다.

![Authenticator 로그인](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Microsoft Authenticator를 사용 하는 암호 없는 인증은 비즈니스용 Windows Hello와 동일한 기본 패턴을 따르며, 사용자를 식별 해야 하므로 Azure AD에서 Microsoft Authenticator 앱 버전을 찾을 수 있습니다. 됩니다.

![인증자 프로세스](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. 사용자가 사용자 이름을 입력 합니다.

2. Azure AD는 사용자에 게 강력한 자격 증명이 있음을 감지 하 고 강력한 자격 증명 흐름을 시작 합니다.

3. 알림은 iOS 장치에서 APNS (Apple Push Notification Service)를 통해 또는 Android 장치의 FCM (Firebase Cloud Messaging)를 통해 앱에 전송 됩니다.

4. 사용자가 푸시 알림을 수신 하 고 앱을 엽니다.

5. 앱은 Azure AD를 호출 하 고 상태 증명 및 nonce를 수신 합니다.

6. 사용자는 생체 인식 또는 PIN을 입력 하 여 개인 키의 잠금을 해제 하는 방법으로 챌린지를 완료 합니다.

7. Nonce는 개인 키로 서명 되 고 Azure AD로 다시 전송 됩니다.

8. Azure AD는 공개/개인 키 유효성 검사를 수행 하 고 토큰을 반환 합니다.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>사용자가 Microsoft Authenticator 자격 증명을 사용 하 여 암호 없는 로그인을 관리 합니다.

사용자는 [결합 된 등록](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)을 통해 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정을 등록 하 고 혜택을 받을 수 있습니다. 사용자는 [내 프로필 페이지로](https://aka.ms/mysecurityinfo)이동 하 여 이러한 설정을 등록 하 고 관리 합니다. SSPR를 사용 하도록 설정 하는 것 외에도 결합 된 등록은 여러 인증 방법과 작업을 지원 합니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2는 FIDO 동맹 표준의 최신 버전 이며, 두 가지 구성 요소인 WebAuthN (W3c에서 제안한 Web Authentication) 표준 및 해당 FIDO 동맹 클라이언트-인증자 프로토콜 (CTAP2)이 있습니다. FIDO2 표준을 통해 사용자는 하드웨어, 모바일 및 생체 인식 기반 인증자를 활용 하 여 모바일 및 데스크톱 환경에서 많은 앱 및 웹 사이트를 쉽게 인증할 수 있습니다.

Microsoft 및 업계 파트너는 공유 장치에서 쉽고 안전한 인증을 사용할 수 있도록 Windows Hello 용 FIDO2 보안 장치에서 함께 작업 하 고 있습니다. FIDO2 보안 키를 사용 하면 자격 증명을 전달 하 고 조직의 일부인 [AZURE AD](https://aka.ms/azuread418)에 가입 된 Windows 10 장치에 안전 하 게 인증할 수 있습니다.

WebAuthN는 웹 앱 및 서비스에서 강력 하 고 암호 없는 인증을 개발 및 구현할 수 있도록 하는 API를 정의 합니다. CTAP 프로토콜을 사용 하면 FIDO 규격 보안 키와 같은 외부 장치에서 WebAuthN를 사용 하 고 인증자로 사용할 수 있습니다. 웹 인증을 사용 하면 사용자가 암호 대신 강력한 공개 키 자격 증명을 활용 하 여 얼굴, 지문, PIN 또는 휴대용 FIDO2 보안 키로 온라인 서비스에 로그인 할 수 있습니다. 현재 WebAuthN는 Microsoft Edge에서 지원 되며 Chrome 및 Firefox에 대 한 지원은 개발 중입니다.

FIDO2, WebAuthN 및 CTAP 프로토콜을 준수 하는 장치 및 토큰은 암호를 사용 하지 않고 강력한 인증의 플랫폼 간 솔루션을 가져옵니다. Microsoft 파트너는 USB 보안 키 및 NFC 사용 스마트 카드와 같은 다양 한 보안 키 폼 팩터를 사용 하 고 있습니다.

### <a name="fido2-security-keys-scenarios"></a>FIDO2 보안 키 시나리오

FIDO2 보안 키는 Windows 10 잠금 화면에서 자격 증명 공급자로 보안 키를 선택 하 여 Azure AD에 로그인 하는 데 사용할 수 있습니다. 사용자 이름 또는 암호가 필요 하지 않습니다 .이는 여러 사용자 간에 Pc를 공유 하는 첫 번째 줄 작업자에 게 적합 한 솔루션입니다. 또한 회사 정책이 사용자의 자격 증명이 장치와 물리적으로 분리 되어야 한다는 것을 규정 하는 경우 뛰어난 인증 옵션입니다. 사용자는 Windows 10 버전 1809 이상의 Microsoft Edge 브라우저 내부에서 FIDO2 보안 키를 사용 하 여 웹 사이트에 로그인 하도록 선택할 수도 있습니다.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 보안 키 배포 고려 사항

관리자는 Azure AD에서 FIDO2 지원을 사용 하도록 설정 하 고 사용자 또는 그룹에 기능을 할당할 수 있습니다. 또한 정책을 프로 비전 하 고 특정 하드웨어 보안 키 집합을 허용 하거나 차단 하는 등의 제한을 구성 하는 방법에 대 한 정책을 만들 수 있습니다. 키는 물리적으로 최종 사용자에 게 배포 되어야 합니다.

**FIDO2 보안 키를 사용 하 여 Azure AD 및 웹 사이트에 암호 없는 로그인을 사용 하도록 설정 하기 위한 요구 사항은 다음과 같습니다.**

* Azure AD

* Azure Multi-Factor Authentication

* 결합 된 등록 미리 보기

* FIDO2 security key preview에는 호환 되는 FIDO2 보안 키가 필요 합니다.

* 웹 인증 (WebAuthN)을 사용 하려면 Windows 10 버전 1809 이상의 Microsoft Edge가 필요 합니다.

* FIDO2 기반 Windows 로그인을 사용 하려면 Azure AD 조인 Windows 10 버전 1809 이상이 필요 합니다 (Windows 10 버전 1903 이상에 대 한 최상의 환경).

FIDO2 규격 폼 팩터는 USB, NFC 및 Bluetooth 장치를 포함 합니다. 일부 플랫폼과 브라우저는 아직 FIDO2 호환 되지 않으므로 특정 요구 사항을 충족 하는 폼 팩터를 선택 하는 것이 좋습니다.

또한 사용자 및 관리자가 보안 키를 분실 하거나 도난당 할 때 따라야 하는 프로토콜을 만드는 것이 좋습니다. 사용자가 분실 하거나 도난당 한 키를 보고 해야 합니다. 이렇게 하면 관리자나 사용자가 자신의 프로필에서 보안 키를 삭제 하 고 새 키를 프로 비전 할 수 있습니다.

### <a name="how-fido2-security-keys-works"></a>FIDO2 보안 키 작동 방법

#### <a name="user-sets-up-fido2-security-key"></a>사용자가 FIDO2 보안 키를 설정 합니다.

관리자는 [수동으로 키를 프로 비전](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) 하 고 최종 사용자에 게 배포할 수 있지만, Windows 10 잠금 화면에서 FIDO2 자격 증명 공급자를 프로 비전 하 고 사용 하도록 설정 하는 것은 [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods)을 통해 지원 됩니다. 또한 관리자는 [Azure Portal](https://portal.azure.com/) 를 사용 하 여 암호 없는 인증 방법으로 하드웨어 토큰 장치를 사용 하도록 설정 해야 합니다.

또한 FIDO2 보안 키를 배포 하려면 사용자가 [결합 된 등록](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)을 사용 하 여 키를 등록 해야 합니다. 사용자는 결합 된 등록을 통해 한 번 등록 하 고 Azure Multi-Factor Authentication 및 Single Sign-On 암호 재설정 (SSPR)의 이점을 얻을 수 있습니다.

하드웨어 토큰을 기본 multi-factor authentication 방법으로 선택 하는 것 외에도 추가 확인 옵션을 선택 하는 것이 좋습니다.

* Microsoft Authenticator--알림

* Authenticator 앱 또는 하드웨어 토큰--코드

* 전화 통화

* 문자 메시지

#### <a name="user-using-fido2-security-key-for-sign-in"></a>사용자가 로그인에 FIDO2 보안 키를 사용 하는 경우

FIDO2는 인증자 및 공개/개인 키 암호화로 사용 되는 폼 팩터 간의 추상화 계층을 제공 하 여 Windows Hello 및 보안 키와 같은 기본 제공 플랫폼 인증자가 개인 키를 확인 하 고 외부 리소스에 액세스 하기 위한 식별자로 사용할 수 있는 공개 키를 제공할 수 있도록 합니다. FIDO2 보안 키에는 개인 키를 저장 하 고 해당 키를 잠금 해제 하기 위해 생체 인식 또는 PIN을 요구 하는 자체 기본 제공 보안 enclave 포함 되어 있습니다. 자격 증명은 서비스 전체에서 다시 사용, 재생 또는 공유할 수 없으며 피싱 및 MiTM 공격 또는 서버 침해의 영향을 받지 않습니다.

![FIDO2 로그인](./media/ad-passwordless/azure-ad-pwdless-image6.png)

FIDO2 보안 키는 폼 팩터와 관계 없이 보안 인증을 제공 합니다. 보안 키는 자격 증명을 보유 하 고 있으며, 지문 (보안 키에 통합 됨) 또는 Windows 로그인에 입력 될 PIN과 같은 추가 두 번째 요소로 보호 되어야 합니다. Microsoft 파트너는 다양 한 보안 키 폼 팩터를 사용 하 고 있습니다. 몇 가지 예로는 USB 보안 키와 NFC 사용 스마트 카드가 있습니다.

> [!NOTE]
> 보안 키는 FIDO2 CTAP 프로토콜의 특정 기능 및 확장을 [Microsoft 호환](https://aka.ms/fido2securitykeys)으로 구현 해야 합니다. Microsoft는 Windows 10 및 Azure Active Directory와의 호환성을 위해 이러한 솔루션을 테스트 했습니다.

![FIDO2 로그인 프로세스](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. 사용자가 FIDO2 장치를 컴퓨터에 연결 합니다.

2. Windows에서 FIDO2 보안 키를 검색 합니다.

3. Windows에서 인증 요청을 보냅니다.

4. Azure AD는 nonce를 다시 보냅니다.

5. 사용자가 FIDO2 보안 키의 secure enclave에 저장 된 개인 키의 잠금을 해제 하는 제스처를 완료 합니다.

6. FIDO2 보안 키는 개인 키를 사용 하 여 nonce에 서명 합니다.

7. 서명 된 nonce를 포함 하는 PRT 토큰 요청이 Azure AD로 전송 됩니다.

8. Azure AD는 FIDO2 공개 키를 사용 하 여 서명 된 nonce를 확인 합니다.

9. Azure AD는 온-프레미스 리소스에 액세스할 수 있도록 PRT를 반환 합니다.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>사용자가 FIDO2 보안 키 자격 증명을 관리 합니다.

Microsoft Authenticator 앱과 마찬가지로 FIDO2 보안 키에 대 한 자격 증명 관리는 최종 사용자에 게 결합 된 등록 환경을 사용 합니다.

## <a name="deciding-a-passwordless-method"></a>암호 없는 메서드 결정

이 세 가지 암호 없는 옵션 중에서 선택 하는 것은 회사의 보안, 플랫폼 및 앱 요구 사항에 따라 달라 집니다.

Microsoft 암호 없는 기술을 선택할 때 고려할 몇 가지 요소는 다음과 같습니다.

||**비즈니스용 Windows Hello**|**Microsoft Authenticator 앱을 사용 하 여 passwordless 로그인**|**FIDO2 보안 키**|
|:-|:-|:-|:-|
|**필수 구성 요소**| Windows 10, 버전 1809 이상<br>Azure Active Directory| Microsoft Authenticator 앱<br>휴대폰 (Android 6.0 이상을 실행 하는 iOS 및 Android 장치)|Windows 10, 버전 1809 이상<br>Azure Active Directory|
|**모드**|플랫폼|소프트웨어|하드웨어|
|**시스템 및 장치**|기본 제공 신뢰할 수 있는 플랫폼 모듈 (TPM)를 사용 하는 PC<br>PIN 및 생체 인식 인식 |휴대폰에서 PIN 및 생체 인식 인식|Microsoft와 호환 되는 보안 장치 FIDO2|
|**사용자 환경**|PIN 또는 생체 인식 인식 (얼굴, iri 또는 지문)을 사용 하 여 Windows 장치에 로그인 합니다.<br>Windows Hello 인증은 장치에 연결 됩니다. 회사 리소스에 액세스 하려면 사용자에 게 PIN 또는 생체 인식 요소와 같은 장치 및 로그인 구성 요소가 필요 합니다.|지문 검색, 얼굴 또는 조리개 인식 또는 PIN을 사용 하 여 휴대폰을 사용 하 여 로그인 합니다.<br>사용자는 PC 또는 휴대폰에서 회사 또는 개인 계정에 로그인 합니다.|FIDO2 security 장치 (생체 인식, 핀 및 NFC)를 사용 하 여 로그인<br>사용자는 장치에 액세스 하 고, PIN에 기반 하 여 장치에 액세스 하 고, USB 보안 키, NFC 지원 스마트 카드, 키 또는 착용 식 장치용와 같은 장치를 사용 하 여 생체 인식을 수행할 수 있습니다.|
|**사용 시나리오**| Windows 장치에서 암호 없는 환경<br>장치 및 응용 프로그램에 대 한 Single Sign-On 기능이 있는 전용 작업 PC에 적용 가능 합니다.|휴대폰을 사용 하는 암호 없는 장소 솔루션.<br>모든 장치에서 웹의 회사 또는 개인 응용 프로그램에 액세스 하는 데 사용할 수 있습니다.|생체 인식, PIN 및 NFC를 사용 하는 작업자에 대 한 암호 없는 환경<br>공유 Pc 및 휴대폰을 사용할 수 없는 경우 (예: 지원 센터 직원, 공공 키오스크 또는 병원 팀)에서 사용할 수 있습니다.|

다음 표를 사용 하 여 요구 사항 및 사용자를 지원할 방법을 선택할 수 있습니다.

|Persona|시나리오|환경|Passwordless 기술|
|:-|:-|:-|:-|
|**관리자**|관리 작업을 위해 장치에 안전 하 게 액세스|할당 된 Windows 10 장치|비즈니스용 Windows Hello 및/또는 FIDO2 보안 키|
|**관리자**|Windows가 아닌 장치에 대 한 관리 작업| 모바일 또는 비 windows 장치|Microsoft Authenticator 앱을 사용 하 여 passwordless 로그인|
|**정보 근로자**|생산성 작업|할당 된 Windows 10 장치|비즈니스용 Windows Hello 및/또는 FIDO2 보안 키|
|**정보 근로자**|생산성 작업| 모바일 또는 비 windows 장치|Microsoft Authenticator 앱을 사용 하 여 passwordless 로그인|
|**Frontline worker**|공장, 공장, 소매 또는 데이터 입력의 키오스크|공유 Windows 10 장치|FIDO2 보안 키|

## <a name="getting-started"></a>시작

Passwordless authentication은 향후의 wave 이며 보다 안전한 환경의 경로입니다. 조직에서 이러한 변경에 대 한 계획을 시작 하 고 암호에 대 한 종속성을 줄이는 것이 좋습니다. 시작 하려면 다음 목표를 고려 하세요.

* 사용자가 MFA + Microsoft Authenticator 앱 + 조건부 액세스를 사용 하도록 설정 합니다.

* Azure AD 암호 보호 및 정책 업데이트를 출시 합니다.

* 결합 된 등록으로 SSPR에 대해 사용자를 사용 하도록 설정 합니다.

* 모바일을 위해 Microsoft Authenticator 앱을 배포 합니다.

* 비즈니스용 Windows Hello 배포 (1909: 최신 상태 유지).

* 전화를 사용할 수 없는 사용자를 위해 FIDO2 장치를 배포 합니다.

* 가능 하면 암호 기반 인증을 사용 하지 않도록 설정 합니다.

이러한 목표를 달성 하려면 다음 방법을 사용 하는 것이 좋습니다.

1. Azure MFA, 조건부 액세스 등의 기능을 최대한 활용 하려면 Azure Active Directory을 사용 하도록 설정 합니다.

2. Multi-factor authentication을 사용 하 여 추가 보호를 제공 합니다.

3. 사용자가 암호를 사용 하 여 대체 해야 하는 경우 셀프 서비스 암호 재설정을 사용 하도록 설정 합니다.

4. 추가 된 이동성을 위해 Microsoft Authenticator 휴대폰 로그인을 배포 합니다.

5. 비즈니스용 Windows Hello를 모든 Windows 10 장치에 배포 합니다.

6. FIDO2 보안 키를 준비 합니다.

> [!NOTE]
> 암호 없는 방법의 라이선스 요구 사항에 대 한 자세한 내용은 Azure Active Directory [라이선스 페이지](https://azure.microsoft.com/pricing/details/active-directory/) 를 참조 하세요.

## <a name="conclusion"></a>결론

지난 몇 년 동안 Microsoft는 암호 없이 전 세계를 사용 하도록 설정 하는 것을 지속적으로 노력 하 고 있습니다. Microsoft에서는 Windows 10을 사용 하 여 single sign-on을 Azure Active Directory 하 고 Active Directory 수 있는 강력 하 고 하드웨어로 보호 된 2 단계 자격 증명 인 비즈니스용 Windows Hello를 도입 했습니다. 비즈니스용 Windows Hello의 기술과 마찬가지로 Microsoft Authenticator 앱은 키 기반 인증을 사용 하 여 모바일 장치에 연결 되 고 생체 인식 또는 PIN을 사용 하는 사용자 자격 증명을 사용 하도록 설정 합니다. 이제 FIDO2 보안 키를 사용 하 여 자격 증명을 전달 하 고 Windows 10 잠금 화면에서 자격 증명 공급자로 보안 키를 선택 하 여 Azure AD에 로그인 할 수 있습니다. 이러한 모든 암호 없는 솔루션은 피싱, 암호 스프레이 및 재생 공격의 위험을 줄이고 사용자에 게 안전 하 고 편리 하 게 로그인 하 여 어디서 나 데이터에 액세스할 수 있는 방법을 제공 합니다.

광범위 하 게 액세스할 수 있는 장치에서 생체 인식 및 공개 키 암호화와 같은 최신 multi-factor authentication 기술을 채택 하는 것은 회사의 id 위험을 의미 있게 줄일 수 있는 가장 효율적인 단계 중 하나입니다. 암호 없는를 사용 하는 것은 보안 인증에 대 한 장기적인 방법 이며 여전히 진화 하 고 있습니다. 새롭게 제공 되는 요구 사항에 따라 조직은 암호 없는 기술로 전환 하기 시작 하는 계획을 수립 하 여 스스로를 준비할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Passwordless의 개요는 [무엇 인가요?](../../active-directory/authentication/concept-authentication-passwordless.md)
* [Azure AD에서 암호 없는를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)

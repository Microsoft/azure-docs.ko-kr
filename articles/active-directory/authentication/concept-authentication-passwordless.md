---
title: Azure Active Directory 암호 없는 로그인 (미리 보기)
description: FIDO2 보안 키 또는 Microsoft Authenticator 앱을 사용 하 여 Azure Active Directory에 대해 암호 없는 로그인 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d426fb743e6b1ce5d279544f12bcb490d529f9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756796"
---
# <a name="passwordless-authentication-options"></a>Passwordless 인증 옵션

MFA (multi-factor authentication)는 조직을 보호 하는 훌륭한 방법 이지만 사용자가 암호를 기억할 필요 없이 추가 보안 계층이 발생 하지 않는 경우가 많습니다. 암호를 제거 하 고 사용자가 보유 한 항목 및 사용자가 알고 있는 항목으로 대체 하기 때문에 암호 없는 인증 방법이 더 편리 합니다.

|   | 보유 한 항목 | 사용자 또는 알고 있는 항목 |
| --- | --- | --- |
| 암호 없음 | Windows 10 장치, 휴대폰 또는 보안 키 | 생체 인식 또는 PIN |

각 조직에는 인증을 할 때 서로 다른 요구 사항이 있습니다. Microsoft는 다음과 같은 세 가지 암호 없는 인증 옵션을 제공 합니다.

- 비즈니스용 Windows Hello
- Microsoft Authenticator 앱
- FIDO2 보안 키

![인증: 보안 및 편의성](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

비즈니스용 windows Hello는 고유의 지정 된 Windows PC가 있는 정보 근로자에 게 적합 합니다. 생체 인식 및 PIN은 사용자 PC와 직접 연결 되어 소유자가 아닌 사용자의 액세스를 차단 합니다. 비즈니스용 Windows Hello는 PKI (공개 키 인프라) 통합 및 기본 제공 되는 SSO (Single Sign-On)를 사용 하 여 온-프레미스 및 클라우드에서 회사 리소스에 원활 하 게 액세스할 수 있는 편리한 방법을 제공 합니다.

비즈니스용 Windows Hello [계획 가이드](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) 를 사용 하 여 비즈니스용 windows hello 배포 유형과 고려해 야 할 옵션에 대 한 결정을 내리는 데 도움을 받을 수 있습니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

직원의 전화를 암호 없는 인증 방법으로 사용할 수 있습니다. 암호 외에도 편리한 다단계 인증 옵션으로 Microsoft Authenticator 앱을 이미 사용 하 고 있을 수 있습니다. 인증자 앱을 암호 없는 옵션으로 사용할 수도 있습니다.

![Microsoft Authenticator 앱을 사용 하 여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator 앱은 모든 iOS 또는 Android 휴대폰을 강력 하 고 암호 없는 자격 증명으로 전환 합니다. 사용자는 휴대폰에 대 한 알림을 받고, 화면에 표시 되는 숫자를 휴대폰에 있는 것과 일치 시킨 다음, 생체 인식 (터치 또는 얼굴) 또는 PIN을 사용 하 여 확인 하 여 모든 플랫폼 또는 브라우저에 로그인 할 수 있습니다.

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2 보안 키는 모든 폼 팩터에서 제공 될 수 있는 unphishable 표준 기반 암호 없는 인증 방법입니다. Fast Identity Online (FIDO)은 암호 없는 인증을 위한 개방형 표준입니다. FIDO를 사용 하면 사용자와 조직이 외부 보안 키 또는 장치에 기본 제공 되는 플랫폼 키를 사용 하 여 사용자 이름 또는 암호 없이 해당 리소스에 로그인 할 수 있습니다.

공개 미리 보기의 경우 직원은 보안 키를 사용 하 여 Azure AD에 가입 된 Windows 10 장치에 로그인 하 고 해당 클라우드 및 온-프레미스 리소스에 대 한 single sign-on을 받을 수 있습니다. 사용자는 지원 되는 브라우저에 로그인 할 수도 있습니다. FIDO2 보안 키는 매우 보안이 중요 하거나, 전화를 두 번째 요소로 사용 하지 않거나 사용할 수 없는 시나리오 또는 직원이 있는 기업에 게 유용한 옵션입니다.

![보안 키를 사용 하 여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

FIDO 동맹에 의해 FIDO2 인증 되는 많은 키가 있지만, Microsoft는 공급 업체에서 제공 하는 FIDO2 클라이언트-인증자 프로토콜 (CTAP) 사양의 일부 선택적 확장을 요구 하 여 보안을 강화 하 고 최적으로 지.

보안 키는 FIDO2 CTAP 프로토콜에서 다음 기능 및 확장을 Microsoft 호환으로 구현 **해야 합니다** .

| # | 기능/확장 신뢰 | 이 기능 또는 확장이 필요한 이유는 무엇 인가요? |
| --- | --- | --- |
| 1 | 상주 키 | 이 기능을 통해 보안 키를 이식할 수 있습니다. 여기서 자격 증명은 보안 키에 저장 됩니다. |
| 2 | 클라이언트 pin | 이 기능을 사용 하면 두 번째 요소로 자격 증명을 보호 하 고 사용자 인터페이스가 없는 보안 키에 적용할 수 있습니다. |
| 3 | hmac-secret | 이 확장을 통해 오프 라인 이거나 비행기 모드에서 장치에 로그인 할 수 있습니다. |
| 4 | RP 당 여러 계정 | 이 기능을 사용 하면 Microsoft 계정 및 Azure Active Directory 같은 여러 서비스에서 동일한 보안 키를 사용할 수 있습니다. |

다음 공급자는 암호 없는 환경과 호환 되는 것으로 알려진 다양 한 폼 팩터를 FIDO2 보안 키를 제공 합니다. FIDO 동맹 뿐만 아니라 공급 업체에 문의 하 여 이러한 키의 보안 속성을 평가 하는 것이 좋습니다.

| 공급자 | 지원 문의 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> 를 구매 하 고 NFC 기반 보안 키를 사용 하려는 경우 보안 키에 대해 지원 되는 NFC 판독기가 필요 합니다. NFC 판독기는 Azure 요구 사항이 나 제한 사항이 아닙니다. 지원 되는 NFC 판독기 목록은 NFC 기반 보안 키에 대 한 공급 업체에 문의 하세요.

사용자가이 지원 되는 장치 목록에서 장치를 다운로드 하려는 경우에는 [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)에 문의 하세요.

## <a name="what-scenarios-work-with-the-preview"></a>어떤 시나리오가 미리 보기에서 작동 하나요?

- 관리자는 테넌트에 대해 암호 없는 인증 방법을 사용할 수 있습니다.
- 관리자는 모든 사용자를 대상으로 하거나 각 방법에 대해 테넌트 내에서 사용자/그룹을 선택할 수 있습니다.
- 최종 사용자는 자신의 계정 포털에서 이러한 암호 없는 인증 방법을 등록 하 고 관리할 수 있습니다.
- 최종 사용자는 이러한 암호 없는 인증 방법으로 로그인 할 수 있습니다.
   - Microsoft Authenticator 앱: 모든 브라우저를 사용 하 여 Windows 10 기본 (OOBE)을 설치 하는 동안, 그리고 모든 운영 체제에서 통합 된 모바일 앱을 포함 하 여 Azure AD 인증을 사용 하는 시나리오에서 작동 합니다.
   - 보안 키: Microsoft Edge와 같은 지원 되는 브라우저의 Windows 10 및 웹에 대 한 잠금 화면에서 작업 합니다.

## <a name="next-steps"></a>다음 단계

[조직에서 FIDO2 보안 키 passwordlesss 옵션을 사용 하도록 설정](howto-authentication-passwordless-security-key.md)

[조직에서 전화 기반 암호 없는 옵션 사용](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>외부 링크

[FIDO 동맹](https://fidoalliance.org/)

[FIDO2 CTAP 사양](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)

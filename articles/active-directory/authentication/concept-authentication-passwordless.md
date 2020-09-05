---
title: Azure Active Directory 암호 없는 로그인 (미리 보기)
description: FIDO2 보안 키 또는 Microsoft Authenticator 앱을 사용 하 여 Azure Active Directory에 대해 암호 없는 로그인 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1148555706ff04c58733b66f4784da71849ce8
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226678"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Azure Active Directory에 대 한 암호 없는 인증 옵션

MFA (multi-factor authentication)와 같은 기능은 조직을 보호 하는 좋은 방법 이지만 사용자가 암호를 기억할 필요 없이 추가 보안 계층이 발생 하지 않는 경우가 많습니다. 암호를 제거 하 고 사용자가 보유 한 항목 및 사용자가 알고 있는 항목으로 대체 하기 때문에 암호 없는 인증 방법이 더 편리 합니다.

| 인증  | 보유 한 항목 | 사용자 또는 알고 있는 항목 |
| --- | --- | --- |
| 암호 없음 | Windows 10 장치, 휴대폰 또는 보안 키 | 생체 인식 또는 PIN |

각 조직에는 인증을 할 때 서로 다른 요구 사항이 있습니다. Microsoft는 Azure Active Directory (Azure AD)와 통합 되는 다음과 같은 세 가지 암호 없는 인증 옵션을 제공 합니다.

- 비즈니스용 Windows Hello
- Microsoft Authenticator 앱
- FIDO2 보안 키

![인증: 보안 및 편의성](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

비즈니스용 windows Hello는 고유의 지정 된 Windows PC가 있는 정보 근로자에 게 적합 합니다. 생체 인식 및 PIN 자격 증명은 사용자 PC와 직접 연결 되어 소유자 이외의 사용자가 액세스 하지 못하도록 합니다. 비즈니스용 Windows Hello는 PKI (공개 키 인프라) 통합 및 기본 제공 되는 SSO (Single Sign-On)를 사용 하 여 온-프레미스 및 클라우드에서 회사 리소스에 원활 하 게 액세스할 수 있는 편리한 방법을 제공 합니다.

![비즈니스용 Windows Hello를 사용한 사용자 로그인 예](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

다음 단계는 Azure AD에서 로그인 프로세스를 작동 하는 방법을 보여 줍니다.

![비즈니스용 Windows Hello에서 사용자 로그인에 관련 된 단계를 간략하게 설명 하는 다이어그램입니다.](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. 사용자가 생체 인식 또는 PIN 제스처를 사용 하 여 Windows에 로그인 합니다. 이 제스처는 비즈니스용 Windows Hello 개인 키의 잠금을 해제 하며 클라우드 *AP 공급자*라고 하는 클라우드 인증 보안 지원 공급자에 게 전송 됩니다.
1. 클라우드 AP 공급자는 Azure AD에서 nonce를 요청 합니다.
1. Azure AD는 5 분 동안 유효한 nonce를 반환 합니다.
1. 클라우드 AP 공급자는 사용자의 개인 키를 사용 하 여 nonce에 서명 하 고 서명 된 nonce를 Azure AD에 반환 합니다.
1. Azure AD는 nonce 서명에 대해 사용자의 안전 하 게 등록 된 공개 키를 사용 하 여 서명 된 nonce의 유효성을 검사 합니다. 서명 유효성을 검사 한 후 Azure AD는 반환 된 서명 된 nonce의 유효성을 검사 합니다. Nonce의 유효성이 확인 되 면 Azure AD는 장치의 전송 키로 암호화 된 세션 키를 사용 하 여 기본 새로 고침 토큰 (PRT)을 만든 다음 클라우드 AP 공급자에 게 반환 합니다.
1. 클라우드 AP 공급자는 세션 키를 사용 하 여 암호화 된 PRT를 수신 합니다. 클라우드 AP 공급자는 장치의 개인 전송 키를 사용 하 여 세션 키의 암호를 해독 하 고 TPM (장치 신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 세션 키를 보호 합니다.
1. 클라우드 AP 공급자는 Windows에 대 한 성공적인 인증 응답을 반환 합니다. 그러면 사용자는 SSO ()를 다시 인증 하지 않고도 클라우드 및 온-프레미스 응용 프로그램 뿐만 아니라 Windows에 액세스할 수 있습니다.

비즈니스용 Windows Hello [계획 가이드](/windows/security/identity-protection/hello-for-business/hello-planning-guide) 를 사용 하 여 비즈니스용 windows hello 배포 유형과 고려해 야 할 옵션에 대 한 결정을 내리는 데 도움을 받을 수 있습니다.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 앱

직원의 전화가 암호 없는 인증 방법이 되도록 허용할 수도 있습니다. 암호 외에도 편리한 다단계 인증 옵션으로 Microsoft Authenticator 앱을 이미 사용 하 고 있을 수 있습니다. 인증자 앱을 암호 없는 옵션으로 사용할 수도 있습니다.

![Microsoft Authenticator 앱을 사용 하 여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator 앱은 모든 iOS 또는 Android 휴대폰을 강력 하 고 암호 없는 자격 증명으로 전환 합니다. 사용자는 휴대폰에 대 한 알림을 받고, 화면에 표시 되는 숫자를 휴대폰에 있는 것과 일치 시킨 다음, 생체 인식 (터치 또는 얼굴) 또는 PIN을 사용 하 여 확인 하 여 모든 플랫폼 또는 브라우저에 로그인 할 수 있습니다. 설치에 대 한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](../user-help/user-help-auth-app-download-install.md) 를 참조 하세요.

Authenticator 앱을 사용 하는 암호 없는 인증은 비즈니스용 Windows Hello와 동일한 기본 패턴을 따릅니다. Azure AD가 사용 되는 Microsoft Authenticator 앱 버전을 찾을 수 있도록 사용자를 식별 해야 하기 때문에 좀 더 복잡 합니다.

![Microsoft Authenticator 앱을 사용한 사용자 로그인에 관련 된 단계를 간략하게 설명 하는 다이어그램입니다.](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. 사용자가 사용자 이름을 입력 합니다.
1. Azure AD는 사용자에 게 강력한 자격 증명이 있음을 감지 하 고 강력한 자격 증명 흐름을 시작 합니다.
1. 알림은 iOS 장치에서 APNS (Apple Push Notification Service)를 통해 또는 Android 장치의 FCM (Firebase Cloud Messaging)를 통해 앱에 전송 됩니다.
1. 사용자가 푸시 알림을 수신 하 고 앱을 엽니다.
1. 앱은 Azure AD를 호출 하 고 상태 증명 및 nonce를 수신 합니다.
1. 사용자는 생체 인식 또는 PIN을 입력 하 여 개인 키의 잠금을 해제 하는 방법으로 챌린지를 완료 합니다.
1. Nonce는 개인 키로 서명 되 고 Azure AD로 다시 전송 됩니다.
1. Azure AD는 공개/개인 키 유효성 검사를 수행 하 고 토큰을 반환 합니다.

암호 없는 로그인을 시작 하려면 다음 방법을 완료 합니다.

> [!div class="nextstepaction"]
> [Authenticator 앱을 사용 하 여 암호 없는 sign 사용](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 보안 키

FIDO2 보안 키는 모든 폼 팩터에서 제공 될 수 있는 unphishable 표준 기반 암호 없는 인증 방법입니다. Fast Identity Online (FIDO)은 암호 없는 인증을 위한 개방형 표준입니다. FIDO를 사용 하면 사용자와 조직이 외부 보안 키 또는 장치에 기본 제공 되는 플랫폼 키를 사용 하 여 사용자 이름 또는 암호 없이 해당 리소스에 로그인 할 수 있습니다.

직원은 보안 키를 사용 하 여 Azure AD 또는 하이브리드 Azure AD에 가입 된 Windows 10 장치에 로그인 하 고 클라우드 및 온-프레미스 리소스에 대 한 single sign-on을 수행할 수 있습니다. 사용자는 지원 되는 브라우저에 로그인 할 수도 있습니다. FIDO2 보안 키는 매우 보안이 중요 하거나, 전화를 두 번째 요소로 사용 하지 않거나 사용할 수 없는 시나리오 또는 직원이 있는 기업에 게 유용한 옵션입니다.

Azure AD에 대 한 FIDO2 보안 키로 로그인은 현재 미리 보기로 제공 됩니다.

![보안 키를 사용 하 여 Microsoft Edge에 로그인](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

사용자가 FIDO2 보안 키를 사용 하 여 로그인 하는 경우 다음 프로세스가 사용 됩니다.

![FIDO2 보안 키로 사용자 로그인에 관련 된 단계를 간략하게 설명 하는 다이어그램입니다.](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. 사용자가 FIDO2 보안 키를 해당 컴퓨터에 연결 합니다.
2. Windows에서 FIDO2 보안 키를 검색 합니다.
3. Windows에서 인증 요청을 보냅니다.
4. Azure AD는 nonce를 다시 보냅니다.
5. 사용자가 FIDO2 보안 키의 secure enclave에 저장 된 개인 키의 잠금을 해제 하는 제스처를 완료 합니다.
6. FIDO2 보안 키는 개인 키를 사용 하 여 nonce에 서명 합니다.
7. 서명 된 nonce를 포함 하는 기본 새로 고침 토큰 (PRT) 토큰 요청이 Azure AD로 전송 됩니다.
8. Azure AD는 FIDO2 공개 키를 사용 하 여 서명 된 nonce를 확인 합니다.
9. Azure AD는 온-프레미스 리소스에 액세스할 수 있도록 PRT를 반환 합니다.

FIDO 동맹에 의해 FIDO2 인증 되는 많은 키가 있지만 Microsoft는 공급 업체에서 제공 하는 FIDO2 CTAP (클라이언트-인증자 프로토콜) 사양의 일부 선택적 확장을 요구 하 여 최대 보안 및 최상의 환경을 보장 합니다.

보안 키는 FIDO2 CTAP 프로토콜에서 다음 기능 및 확장을 Microsoft 호환으로 구현 **해야 합니다** .

| # | 기능/확장 신뢰 | 이 기능 또는 확장이 필요한 이유는 무엇 인가요? |
| --- | --- | --- |
| 1 | 상주 키 | 이 기능을 통해 보안 키를 이식할 수 있습니다. 여기서 자격 증명은 보안 키에 저장 됩니다. |
| 2 | 클라이언트 pin | 이 기능을 사용 하면 두 번째 요소로 자격 증명을 보호 하 고 사용자 인터페이스가 없는 보안 키에 적용할 수 있습니다. |
| 3 | hmac-비밀 | 이 확장을 통해 오프 라인 이거나 비행기 모드에서 장치에 로그인 할 수 있습니다. |
| 4 | RP 당 여러 계정 | 이 기능을 사용 하면 Microsoft 계정 및 Azure Active Directory 같은 여러 서비스에서 동일한 보안 키를 사용할 수 있습니다. |

### <a name="fido2-security-key-providers"></a>FIDO2 보안 키 공급자

다음 공급자는 암호 없는 환경과 호환 되는 것으로 알려진 다양 한 폼 팩터를 FIDO2 보안 키를 제공 합니다. FIDO 동맹 뿐만 아니라 공급 업체에 문의 하 여 이러한 키의 보안 속성을 평가 하는 것이 좋습니다.

| 공급자 | 연락처 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://shop.ftsafe.us/pages/microsoft](https://shop.ftsafe.us/pages/microsoft) |
| 숨겼습니다 | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey 솔루션 | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales Group) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan i n c. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon 기술 Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> 를 구매 하 고 NFC 기반 보안 키를 사용 하려는 경우 보안 키에 대해 지원 되는 NFC 판독기가 필요 합니다. NFC 판독기는 Azure 요구 사항이 나 제한 사항이 아닙니다. 지원 되는 NFC 판독기 목록은 NFC 기반 보안 키에 대 한 공급 업체에 문의 하세요.

사용자가이 지원 되는 장치 목록에서 장치를 구입 하려는 경우에는에 문의 하세요 [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

FIDO2 보안 키를 시작 하려면 다음 방법에 대 한 작업을 완료 하세요.

> [!div class="nextstepaction"]
> [FIDO2 보안 키를 사용 하 여 암호 없는 sign 사용](howto-authentication-passwordless-security-key.md)


## <a name="what-scenarios-work-with-the-preview"></a>어떤 시나리오가 미리 보기에서 작동 하나요?

Azure AD 암호 없는 로그인 기능은 현재 미리 보기 상태입니다. 고려 사항은 다음과 같습니다.

- 관리자는 테 넌 트에 대해 암호 없는 인증 방법을 사용할 수 있습니다.
- 관리자는 모든 사용자를 대상으로 하거나 각 방법에 대해 테 넌 트 내에서 사용자/그룹을 선택할 수 있습니다.
- 최종 사용자는 자신의 계정 포털에서 이러한 암호 없는 인증 방법을 등록 하 고 관리할 수 있습니다.
- 최종 사용자는 이러한 암호 없는 인증 방법으로 로그인 할 수 있습니다.
   - Microsoft Authenticator 앱: 모든 브라우저를 사용 하 여 Windows 10 기본 (OOBE)을 설치 하는 동안, 그리고 모든 운영 체제에서 통합 된 모바일 앱을 포함 하 여 Azure AD 인증을 사용 하는 시나리오에서 작동 합니다.
   - 보안 키: Microsoft Edge (레거시 및 새 Edge 모두)와 같은 지원 되는 브라우저에서 Windows 10 및 웹의 잠금 화면에 대 한 작업을 수행 합니다.

## <a name="choose-a-passwordless-method"></a>암호 없는 메서드 선택

이러한 세 가지 암호 없는 옵션 중에서 선택 하는 것은 회사의 보안, 플랫폼 및 앱 요구 사항에 따라 달라 집니다.

Microsoft 암호 없는 기술을 선택할 때 고려할 몇 가지 요소는 다음과 같습니다.

||**비즈니스용 Windows Hello**|**Microsoft Authenticator 앱을 사용 하 여 passwordless 로그인**|**FIDO2 보안 키**|
|:-|:-|:-|:-|
|**필수 구성 요소**| Windows 10, version 1809 이상<br>Azure Active Directory| Microsoft Authenticator 앱<br>휴대폰 (Android 6.0 이상을 실행 하는 iOS 및 Android 장치)|Windows 10, version 1809 이상<br>Azure Active Directory|
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

## <a name="next-steps"></a>다음 단계

Azure AD에서 암호 없는를 시작 하려면 다음 방법 중 하나를 수행 합니다.

* [FIDO2 보안 키 암호 없는 로그인 사용](howto-authentication-passwordless-security-key.md)
* [인증 앱으로 전화 기반 암호 없는 로그인 사용](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>외부 링크

* [FIDO 동맹](https://fidoalliance.org/)
* [FIDO2 CTAP 사양](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
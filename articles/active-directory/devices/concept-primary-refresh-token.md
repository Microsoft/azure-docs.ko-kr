---
title: 기본 새로 고침 토큰 (PRT) 및 Azure AD-Azure Active Directory
description: 역할 이란 무엇 이며 기본 새로 고침 토큰 (PRT)에서 Azure Active Directory를 어떻게 관리 합니까?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e195a93209875b9eabfaa2ad00772281922443c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476105"
---
# <a name="what-is-a-primary-refresh-token"></a>기본 새로 고침 토큰 이란?

기본 새로 고침 토큰 (PRT)는 Windows 10, iOS 및 Android 장치에서 Azure AD 인증의 주요 아티팩트입니다. 것은 JSON 웹 토큰 (JWT) 특수 발급에서 single sign-on (SSO)을 사용 하도록 설정 하려면 Microsoft 제공 하는 파티 첫 번째 토큰 브로커를 해당 장치에서 사용 되는 응용 프로그램입니다. 이 문서에서는 PRT 발급, 사용 및 Windows 10 장치에서 보호 하는 방법의 세부 정보를 제공 합니다.

이 문서에서는 Azure AD에서 사용 가능 하 고 어떻게 단일 다양 한 장치 상태를 이미 알고 있다고 가정 로그온 Windows 10에서 작동 합니다. Azure AD에서 장치에 대 한 자세한 내용은 문서를 참조 하세요. [Azure Active Directory에서 장치 management 란?](overview.md)

## <a name="key-terminology-and-components"></a>주요 용어 및 구성 요소

요청에 PRT를 사용 하 여 핵심적인 역할을 수행 하는 다음 Windows 구성 요소:

* **클라우드 인증 공급자** (CloudAP): CloudAP는 Windows 로그인, Windows 10 장치에 로그인 하는 사용자를 확인 하는 대 한 최신 인증 공급자. CloudAP 해당 id 공급자에서 빌드할 수 Windows 인증을 사용 하도록 설정 하려면 해당 id 공급자의 자격 증명을 사용 하 여 플러그 인 프레임 워크를 제공 합니다.
* **웹 계정 관리자** (WAM): WAM는 Windows 10 장치에서 기본 토큰 broker입니다. WAM 제공 플러그 인 프레임 워크를 해당 id 공급자를 구축 하 고 해당 id 공급자에 의존 하는 응용 프로그램에 SSO를 사용 하도록 설정 합니다.
* **Azure AD CloudAP 플러그 인**: Windows 로그인 하는 동안 Azure AD 사용 하 여 사용자 자격 증명을 확인 하는 CloudAP 프레임 워크 상에 구축 하는 Azure AD 특정 플러그 인입니다.
* **Azure AD WAM 플러그 인**: Azure AD 특정 플러그 인 인증을 위해 Azure AD를 사용 하는 응용 프로그램에 SSO를 사용 하면 WAM 프레임 워크를 기반으로 합니다.
* **Dsreg**: 모든 장치 상태에 대 한 장치 등록 프로세스를 처리 하는 Windows 10에서 Azure AD 특정 구성 합니다.
* **신뢰할 수 있는 플랫폼 모듈** (TPM): TPM은 사용자 및 장치 암호에 대 한 하드웨어 기반 보안 기능을 제공 하는 장치에 기본 제공 되는 하드웨어 구성 요소. 자세한 내용은 문서에서 찾을 수 있습니다 [신뢰할 수 있는 플랫폼 모듈 기술 개요](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview)합니다.

## <a name="what-does-the-prt-contain"></a>PRT 들어 있습니까?

PRT 일반적으로 모든 Azure AD 새로 고침 토큰에 포함 된 클레임을 포함 합니다. 또한 일부 장치별 클레임 PRT에 포함 되어 있습니다. 다음과 같습니다.

* **장치 ID**: 특정 장치에 사용자를 PRT 발급 됩니다. 장치 ID 클레임 `deviceID` PRT에서 사용자에 게 발급 된 장치를 결정 합니다. 이 클레임은 나중에 PRT 통해 얻은 토큰을 발급 됩니다. 장치 ID 클레임은 장치 상태 또는 규정 준수에 따라 조건부 액세스에 대 한 권한 부여를 결정 하는 데 사용 됩니다.
* **세션 키**: 세션 키가 암호화 된 대칭 키의 PRT의 일부로 발급 한 Azure AD 인증 서비스에서 생성 합니다. 세션 키 역할을 PRT 다른 응용 프로그램에 대 한 토큰을 가져오는 데 사용 되는 경우는 소유 증명을 합니다.

### <a name="can-i-see-whats-in-a-prt"></a>PRT에 새로운 볼 수 있나요?

PRT는 클라이언트 구성 요소에 해당 내용을 알 수 없는 Azure AD에서 전송 하는 불투명 blob입니다. PRT 들여다보기를 확인할 수 없습니다.

## <a name="how-is-a-prt-issued"></a>PRT는 발급 하는 방법

Device registration은 Azure AD에서 장치 기반 인증에 대 한 필수 구성 요소입니다. 등록 된 장치에만 사용자에 게는 PRT 발급 됩니다. 장치 등록에서 자세한 정보를 문서 참조 [Windows Hello 비즈니스 및 Device Registration](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration)합니다. 장치 등록 중 dsreg 구성 요소에서는 두 가지를 암호화 키 쌍을 생성합니다.

* 장치 키 (dkpub/dkpriv)
* 전송 키 (tkpub/tkpriv)

공개 키를 Azure AD 장치 등록 프로세스 중에 보내는 동안 장치에 TPM을 유효 하 고 작동 하는 경우 개인 키에 TPM 장치가 바인딩됩니다. 이러한 키는 PRT 요청 하는 동안 장치 상태 유효성 검사에 사용 됩니다.

PRT 두 가지 시나리오에서 Windows 10 장치에 사용자를 인증 하는 동안 발생 합니다.

* **Azure AD 가입** 나 **하이브리드 Azure AD 가입**: 사용자가 자신의 조직 자격 증명으로 로그인 할 때 Windows 로그온 하는 동안는 PRT 발급 됩니다. 예를 들어를 PRT는 모든 지원 되는 Windows 10 자격 증명을 사용 하 여 발생 암호 및 Windows Hello 비즈니스에 대 한 합니다. 이 시나리오에서는 Azure AD CloudAP 플러그 인은 PRT 대 한 기본 인증.
* **Azure AD 등록 장치**: 사용자는 Windows 10 장치에는 보조 작업 계정을 추가 하는 경우에 PRT 발급 됩니다. 사용자가 두 가지 방법으로-Windows 10 계정을 추가할 수 있습니다.  
   * 통해 계정을 추가 합니다 **어디에서 나이 장치에서이 계정을 사용 하 여** 앱 (예: Outlook)에 로그인 한 후 프롬프트
   * 계정 추가 **설정을** > **계정** > **회사 또는 학교 액세스** > **연결**

Azure AD 등록 장치 시나리오에서 Azure AD WAM 플러그 인 이므로 PRT에 대 한 기본 권한을 Windows 로그온이 Azure AD 계정으로 발생 하지입니다.

> [!NOTE]
> 타사 타사 id 공급자는 Ws-trust 프로토콜을 사용 하는 Windows 10 장치에서 PRT 발급을 지원 해야 합니다. 하이브리드 Azure AD 가입 또는 Azure AD에서 사용자 장치에 조인 없이 Ws-trust, PRT를 실행할 수 없습니다.

## <a name="what-is-the-lifetime-of-a-prt"></a>PRT 기간 이란?

발급 된를 PRT 14 일 동안 유효 하며 장치를 적극적으로 사용 하는 사용자와 지속적으로 갱신 됩니다.  

## <a name="how-is-a-prt-used"></a>PRT는 어떻게 사용 합니까?

PRT Windows의 두 가지 주요 구성 요소에서 사용 됩니다.

* **Azure AD CloudAP 플러그 인**: Windows 로그인 중에 Azure AD CloudAP 플러그 인을 사용자가 제공한 자격 증명을 사용 하 여 Azure AD에서는 PRT을 요청 합니다. 또한 사용자 없을 경우 인터넷 연결에 대 한 액세스는 캐시 된 로그인을 사용 하도록 설정 하려면 PRT 캐시 합니다.
* **Azure AD WAM 플러그 인**: 사용자를 응용 프로그램에 액세스 하려고 하는 경우 Azure AD WAM 플러그 인 PRT를 사용 하 여 Windows 10에서 SSO를 사용 하도록 합니다. Azure AD WAM 플러그 인 PRT를 사용 하 여 토큰 요청에 대 한 WAM 의존 하는 응용 프로그램에 대 한 새로 고침 및 액세스 토큰을 요청 합니다. 브라우저에서 SSO PRT 브라우저 요청에 삽입 하 여 수도 있습니다. Windows 10에서 SSO 브라우저 (고유 하 게) Microsoft Edge에서 지원 됩니다 (Windows 10 계정 또는 Office Online 확장)를 통해 Chrome 및 합니다.

## <a name="how-is-a-prt-renewed"></a>PRT는 갱신 하는 방법

두 가지 방법에는 PRT 갱신:

* **Azure AD CloudAP 플러그 인 4 시간 마다**: CloudAP 플러그 인 Windows 로그인 중 4 시간 마다 PRT를 갱신합니다. 사용자에 해당 시간 동안 인터넷에 연결 하는 경우에 CloudAP 플러그 인 장치가 인터넷에 연결 되 면 PRT에 갱신 됩니다.
* **앱 토큰 요청 동안 azure AD WAM 플러그 인**: WAM 플러그 인 응용 프로그램에 대 한 자동 토큰 요청을 사용 하 여 Windows 10 장치에서 SSO를 설정 합니다. WAM 플러그 인은 두 가지 방법으로 이러한 토큰 요청 중 PRT를 갱신할 수 있습니다.
   * 앱 액세스 토큰에 대 한 자동으로 WAM을 요청 하지만 해당 앱에 사용할 수 있는 새로 고침 토큰이 없습니다. 이 경우 WAM PRT를 사용 하 여 앱에 대 한 토큰을 요청 하 고 응답에 새 PRT를 다시 가져옵니다.
   * 앱 액세스 토큰에 대 한 WAM 요청 하지만 PRT 잘못 되었거나 Azure AD에 추가 권한 부여 (예: Azure Multi-factor Authentication)에 필요 합니다. 이 시나리오에서는 WAM 사용자 재인증 또는 추가 확인을 제공 하도록 요구 하는 대화형 로그온을 시작 하 고 새 PRT 인증 성공 시 발급 됩니다.

### <a name="key-considerations"></a>주요 고려 사항

* 만 PRT 발급 이며 네이티브 앱 인증 중 갱신 합니다. PRT가 갱신 되거나 브라우저 세션을 수행 하는 동안 없습니다.
* Azure AD에 가입 된 이며 하이브리드 Azure AD 가입 장치를 CloudAP 플러그 인을 PRT 대 한 기본 인증. WAM 기반 토큰 요청 중을 PRT가 갱신 되 PRT CloudAP 플러그 인을 승인 하기 전에 Azure AD 사용 하 여 PRT의 유효성을 확인 하려면 다시 전송 됩니다.

## <a name="how-is-the-prt-protected"></a>PRT는 보호 하는 방법을

PRT 바인딩에 로그인 한 사용자 장치에 의해 보호 됩니다. Azure AD 및 Windows 10은 다음 방법을 통해 PRT 보호를 사용 합니다.

* **처음 로그인 하는 동안**: 첫 번째 로그인에는 PRT 장치 등록 중에 생성 된 암호화 된 장치 키를 사용 하 여 요청을 서명 하 여 발급 됩니다. 유효 하 고 작동 TPM 사용 하 여 장치에서 장치 키 악의적인 액세스를 방지 TPM으로 보호 됩니다. 해당 장치 키 서명 유효성을 검사할 수 없는 경우는 PRT가 발급 되지 않았습니다.
* **토큰 요청 및 갱신 하는 동안**: PRT 실행 될 때 Azure AD는 또한 암호화 된 세션 키를 장치에 발급 합니다. 공용 전송 키 (tkpub) 생성 하 고 장치 등록의 일환으로 Azure AD로 전송를 사용 하 여 암호화 됩니다. 이 세션 키를 TPM으로 보호 전용 전송 키 (tkpriv)로 해독할 수 있습니다. 세션 키는 Azure AD로 전송 된 모든 요청에 대 한 소유 증명 (POP) 키를 설정 합니다.  세션 키는 TPM으로 보호 하 고 다른 OS 구성 요소가 없으면에 액세스할 수 있습니다. 토큰 요청 또는 PRT 갱신 요청 TPM 통해이 세션 키로 서명 된 안전 하 게 되 고 따라서 변경할 수 없습니다. Azure AD는 해당 세션 키로 서명 되지 않은 장치에서 요청을 무효화 됩니다.

TPM 사용 하 여 이러한 키를 보호 하는 악의적인 행위자에는 키를 도용할 수 없도록 하거나 다른 곳에서 TPM 액세스할 수 없는 경우에 공격자가 장치를 실제로 보유 하는 대로 PRT 재생 합니다.  따라서 Azure AD 조인 됨, 하이브리드 Azure AD에 가입 된 경우의 보안을 강화 크게 TPM을 사용 하 여 하 고 Azure AD 자격 증명 도난 으로부터 장치를 등록 합니다. 성능 및 안정성에 대 한 TPM 2.0이 Windows 10에서 모든 Azure AD 장치 등록 시나리오에 대 한 권장된 된 버전입니다.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>앱 토큰 및 보호 하는 브라우저 쿠키를 어떻게 되나요?

**앱 토큰**: WAM 통해 토큰을 요청 하는 앱을 Azure AD 액세스 토큰 및 새로 고침 토큰을 발급 합니다. 그러나 WAM만 앱에 대 한 액세스 토큰을 반환 하 고 사용자의 데이터 보호 응용 프로그래밍 인터페이스 (DPAPI) 키로 암호화 하 여 캐시에서 새로 고침 토큰을 보호 합니다. WAM 새로 고침 토큰은 세션 키를 사용 하 여 요청을 서명 하 여 추가 액세스 토큰을 발급 하도록 안전 하 게 사용 합니다. DPAPI 키 자체를 Azure AD에서 Azure AD 기반 대칭 키로 보호 됩니다. 장치를 DPAPI 키를 사용 하 여 사용자 프로필 암호를 해독 해야 하는 경우 Azure AD CloudAP 플러그 인 요청 TPM 암호를 해독 하는 세션 키를 암호화 하는 DPAPI 키를 제공 합니다. 이 기능은 새로 고침 토큰을 보안의 일관성을 유지 하 고 고유한 보호 메커니즘을 구현 하는 응용 프로그램을 방지 됩니다.  

**브라우저 쿠키**: Windows 10에서 Azure AD는 지원 브라우저 Internet Explorer와 Microsoft Edge에서 SSO를 고유 하 게 또는 Google Chrome에서 Windows 10 계정 확장을 통해. 보안 쿠키 뿐만 아니라 쿠키를 전송 되는 엔드포인트을 보호 하기 위해 뿐만 아니라 빌드됩니다. 브라우저 쿠키는 동일한 방식으로 보호 되는 PRT 인 로그인 쿠키를 보호 하는 세션 키를 활용 하 여 합니다.

사용자 상호 작용 하는 브라우저에서 시작 하면 브라우저 (또는 확장) COM 네이티브 클라이언트 호스트를 호출 합니다. 네이티브 클라이언트 호스트 페이지에서 허용 된 도메인 중 하나 인지 확인 합니다. 하지만 브라우저는 다른 매개 변수를 네이티브 클라이언트 호스트 nonce를 포함 하 여 네이티브 클라이언트 호스트는 호스트의 유효성 검사를 보장 하는 보낼 수 있습니다. 네이티브 클라이언트 호스트 생성 하 고 TPM으로 보호 된 세션 키로 서명 되어 CloudAP 플러그 인에서 PRT-쿠키를 요청 합니다. PRT 쿠키는 세션 키로 서명 하는 대로 사용 하 여 변경할 수 없습니다. 이 PRT 쿠키는 장치에서 발생 하는 것의 유효성을 검사 하려면 Azure AD에 대 한 요청 헤더에 포함 됩니다. Chrome 브라우저를 사용 하는 경우 임의의 확장 이러한 요청 하지 못하도록 방지 하는 네이티브 클라이언트 호스트의 매니페스트에 명시적으로 정의 된 확장만 호출할 수 있습니다. Azure AD의 PRT 쿠키 유효성을 검사, 되 면 브라우저 세션 쿠키를 실행 합니다. 또한이 세션 쿠키를 PRT 발급 동일한 세션 키를 포함 합니다. 후속 요청 하는 동안 세션 키를 효과적으로 장치에 쿠키를 바인딩 및 다른 곳에서 재생 방지 유효성이 검사 됩니다.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>PRT MFA 클레임이 가져오기 하는 경우

PRT 특정 시나리오에는 multi-factor authentication (MFA) 클레임을 가져올 수 있습니다. 응용 프로그램에 대 한 토큰을 요청 하는 MFA 기반 PRT를 사용 하면 MFA 클레임을 해당 앱 토큰으로 전송 됩니다. 이 기능은 필요한 모든 앱에 대 한 MFA 챌린지를 방지 하 여 사용자에 게 원활한 환경을 제공 합니다. PRT 다음과 같은 방법으로 MFA 클레임을 가져올 수 있습니다.

* **비즈니스용 Windows Hello 로그인**: Windows Hello 비즈니스에 대 한 암호를 대체 하 고 암호화 키를 사용 하 여 강력한 2 단계 인증을 제공 합니다. Windows Hello for Business 장치에서 사용자에 게 고유한 이며 프로 비전 하는 데 MFA를 필요한 자체. 사용자 로그인 할 때 Windows Hello for Business에 사용자의 PRT MFA 클레임을 가져옵니다. 이 시나리오는 사용자가 스마트 카드 인증에 ADFS에서 MFA 클레임을 생성 하는 경우 스마트 카드 로그인에 적용 됩니다.
   * Windows Hello 비즈니스에 대 한 것으로 간주 됩니다 multi-factor authentication 인증을 MFA 클레임 업데이트 됩니다 자체 PRT을 새로 고칠 때 사용 하 여 비즈니스용 WIndows Hello 로그인 할 때 MFA 기간 동안 지속적으로 확장 하므로
* **WAM 대화형 로그인 하는 동안 MFA**: WAM 통해 토큰 요청을 하는 동안 사용자가 앱에 액세스 하는 MFA를 수행 하는 데 필요한이 상호 작용 하는 동안 갱신 된 PRT 없으면 MFA 클레임을 사용 하 여 인쇄 합니다.
   * 이 경우 MFA 기간 수명 디렉터리 설정에 기반 하므로 MFA 클레임을 지속적으로 업데이트 되지 않습니다.
* **장치 등록 중 MFA**: 관리자가 Azure AD에 장치 설정을 구성한 경우 [장치를 등록할 때 MFA를 요구할](device-management-azure-portal.md#configure-device-settings), 사용자가 MFA 등록을 완료 해야 합니다. 이 과정에서 사용자에 게 발급 된 PRT 등록 하는 동안 얻은 MFA 클레임을 있습니다. 이 기능은 다른 사용자가 해당 장치에 로그인 하는 필요가 조인 작업을 수행한 사용자 에게만 적용 됩니다.
   * WAM 대화형 로그인에서와 마찬가지로, MFA 클레임 업데이트 되지 않습니다 지속적으로 MFA 기간 동안 디렉터리에 설정 된 수명을 기반으로 합니다.

Windows 10에는 각 자격 증명에 대 한 분할 된 Prt 목록을 유지 관리 합니다. 따라서 Windows Hello 비즈니스, 암호 또는 스마트 카드의 각각에 대해 PRT입니다. 이렇게 분할 하면는 MFA 클레임은 격리에 따라 자격 증명을 사용 하 고 토큰 요청 중 혼합 되지 않습니다.

## <a name="how-is-a-prt-invalidated"></a>PRT는 무효화 하는 방법

PRT 다음과 같은 시나리오에서 무효화 됩니다.

* **잘못 된 사용자**: 사용자를 삭제 하거나 Azure AD에서 사용할 수는 PRT는 무효화 되 고 응용 프로그램에 대 한 토큰을 가져오는 데 사용할 수 없습니다. 삭제 되거나 비활성화 된 사용자를 이미 전에 장치에 로그인 하는 경우 캐시 된 로그인은 로그에서는 CloudAP 잘못 된 상태로 인식 될 때까지 합니다. CloudAP 사용자 잘못 되었음을 확인, 이후 로그온을 차단 합니다. 잘못 된 사용자는 자동으로 캐시 된 자격 증명을 갖지 않는 새 장치에 로그인에서 차단 됩니다.
* **잘못 된 장치**: 장치를 삭제 하거나 Azure AD에서 사용할 수는 장치에서 가져온 PRT는 무효화 되 고 다른 응용 프로그램에 대 한 토큰을 가져오는 데 사용할 수 없습니다. 사용자가 아직 유효 하지 않은 장치에 로그인을 위해 계속 수 있습니다. 하지만 장치에서 모든 토큰 무효화 되 고 사용자 없는 SSO 리소스에 해당 장치에서.
* **암호 변경**: 사용자가 암호를 변경한 후에 Azure AD에서 이전 암호를 사용 하 여 얻은 PRT 무효화 됩니다. 암호는 사용자가 새 PRT에서 결과 변경 합니다. 이 무효화는 두 가지 방법으로 발생할 수 있습니다.
   * 사용자가 새 암호를 사용 하 여, Windows에 로그인을 경우 CloudAP 이전 PRT를 삭제 하 고 새 암호를 사용 하 여 새 PRT 발급 하도록 Azure AD를 요청 합니다. 사용자에 인터넷 연결이 없는 경우 새 암호의 유효성을 검사할 수 없습니다, 그리고 Windows 이전 암호를 입력 해야 할 수 있습니다.
   * 사용자가 이전 암호를 로그인 또는 Windows에 로그인 한 후 해당 암호를 변경 하는 경우 이전 PRT WAM 기반 토큰 요청에 사용 됩니다. 이 시나리오에서는 WAM 토큰 요청 중 다시 인증 하 라는 메시지가 됩니다 하 고 새 PRT 발급 됩니다.
* **TPM 문제**: 경우에 따라 장치의 TPM 일으키는 하거나를 TPM으로 보호 된 키에 액세스할 수 없으므로 실패할 수 있습니다. 이 경우 장치는 PRT 가져오기 또는 암호화 키를 소유한 증명할 수 없는 것으로 기존 PRT를 사용 하 여 토큰을 요청할 수 있는 아닙니다. 결과적으로, Azure AD에서 모든 기존 PRT 무효화 됩니다. Windows 10에서 오류를 감지 하면 다시 새 암호화 키를 사용 하 여 장치를 등록 하는 복구 흐름을 시작 합니다. 초기 등록을 마찬가지로 하이브리드 Azure Ad 조인이 복구 사용자 입력 없이 자동으로 발생합니다. Azure AD 가입 또는 Azure AD 등록 장치에 대 한 복구는 장치에 대 한 관리자 권한이 있는 사용자가 수행 해야 합니다. 이 시나리오에서는 복구 흐름을 성공적으로 장치를 복구 하는 과정을 안내 하는 Windows 프롬프트에서 시작 됩니다.

## <a name="detailed-flows"></a>자세한 흐름

다음 다이어그램에서는 발급, 갱신 및 응용 프로그램에 대 한 액세스 토큰을 요청 하는 PRT를 사용 하 여 기본 세부 정보를 보여 줍니다. 또한 이러한 단계 앞에서 언급 한 보안 메커니즘은 이러한 상호 작용 하는 동안 적용 되는 방식을 설명 합니다.

### <a name="prt-issuance-during-first-sign-in"></a>처음 로그인 하는 동안 PRT 발급

![자세한 흐름에서 첫 번째 로그인 시 PRT 발급](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Azure AD의 가입 장치를이 exchange 동기적으로 발생 사용자는 Windows에 로그온 할 수 전에 PRT를 발급 합니다. 하이브리드 Azure AD 가입 장치를 온-프레미스 Active Directory 기본 기관. 따라서 사용자가 PRT 발급 비동기적으로 발생 하는 동안 로그인에 TGT를 획득할 수 있습니다 때까지 기다립니다만 합니다. 로그온 Azure AD 자격 증명을 사용 하지 않으므로이 시나리오에서 Azure AD 등록 장치에 적용 되지 않습니다.

| 단계 | 설명 |
| :---: | --- |
| A | 사용자는 로그인 UI에서 자신의 암호를 입력합니다. LogonUI는 결과적으로 CloudAP 내부적으로 전달 하는 LSA를 인증 버퍼에 자격 증명을 전달 합니다. CloudAP은 CloudAP 플러그 인이 요청을 전달합니다. |
| b | CloudAP 플러그 인 사용자에 대 한 id 공급자를 식별 하는 영역 검색 요청을 시작 합니다. Azure AD 사용자의 테넌트 페더레이션 공급자 설치에 종단점 (MEX) 엔드포인트 페더레이션 공급자의 메타 데이터 교환을 반환 합니다. 그렇지 않은 경우 Azure AD 사용자가 해당 사용자가 Azure AD를 사용 하 여 인증할 수를 나타내는 관리를 반환 합니다. |
| C | 사용자가 관리 하는 경우 CloudAP Azure AD에서 nonce를 받습니다. 사용자가 페더레이션 CloudAP 플러그 인 사용자의 자격 증명을 사용 하 여 페더레이션 공급자에서 SAML 토큰을 요청 합니다. 를 받으면 SAML 토큰을 Azure AD에서 nonce를 요청 합니다. |
| D | CloudAP 플러그 인 사용자의 자격 증명, nonce 및 broker 범위를 사용 하 여 인증 요청을 생성 하 고, 장치 키 (dkpriv)를 사용 하 여 요청을 서명, Azure AD에 보냅니다. 페더레이션된 환경에서 CloudAP 플러그 인은 사용자 대신 페더레이션 공급자에 의해 반환 되는 SAML 토큰 ' 자격 증명입니다. |
| E | Azure AD 사용자 자격 증명을 nonce의 유효성을 검사 하 고 장치가 테넌트에서 잘못 되어 암호화 된 PRT 문제 장치 서명 확인 키를 누릅니다. PRT를 함께 Azure AD는도 전송 키 (tkpub)를 사용 하 여 Azure AD로 암호화 된 세션 키라는 대칭 키를 발급 합니다. 또한 세션 키 PRT에도 포함 됩니다. 이 세션 키 PRT 사용 하 여 후속 요청에 대 한 소유 증명 (PoP) 키로 작동합니다. |
| F | CloudAP 플러그 인 CloudAP에 암호화 된 PRT 및 세션 키를 전달합니다. CloudAP 요청 전송 키 (tkpriv)를 사용 하 여 세션 키를 해독 하려면 TPM 및 TPM의 키를 사용 하 여 다시 암호화 합니다. CloudAP은 PRT 함께 해당 캐시에 암호화 된 세션 키를 저장합니다. |

### <a name="prt-renewal-in-subsequent-logons"></a>이후의 로그온에서 PRT 갱신

![이후의 로그온에서 PRT 갱신](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| 단계 | 설명 |
| :---: | --- |
| A | 사용자는 로그인 UI에서 자신의 암호를 입력합니다. LogonUI는 결과적으로 CloudAP 내부적으로 전달 하는 LSA를 인증 버퍼에 자격 증명을 전달 합니다. CloudAP은 CloudAP 플러그 인이 요청을 전달합니다. |
| b | 사용자가 사용자 로그온 이전에 캐시를 Windows 시작 로그인 한 사용자를 로그인 하려면 자격 증명의 유효성을 검사 합니다. 4 시간 마다 CloudAP 플러그 인 시작 PRT 갱신 비동기적으로 합니다. |
| C | CloudAP 플러그 인 사용자에 대 한 id 공급자를 식별 하는 영역 검색 요청을 시작 합니다. Azure AD 사용자의 테넌트 페더레이션 공급자 설치에 종단점 (MEX) 엔드포인트 페더레이션 공급자의 메타 데이터 교환을 반환 합니다. 그렇지 않은 경우 Azure AD 사용자가 해당 사용자가 Azure AD를 사용 하 여 인증할 수를 나타내는 관리를 반환 합니다. |
| D | 사용자가 페더레이션 CloudAP 플러그 인 사용자의 자격 증명을 사용 하 여 페더레이션 공급자에서 SAML 토큰을 요청 합니다. 를 받으면 SAML 토큰을 Azure AD에서 nonce를 요청 합니다. 사용자가 관리 하는 경우 CloudAP Azure AD에서 nonce을 직접 가져옵니다. |
| E | CloudAP 플러그 인 사용자의 자격 증명, nonce 및 기존 PRT를 사용 하 여 인증 요청을 생성 하 고, 세션 키를 사용 하 여 요청을 서명, Azure AD에 보냅니다. 페더레이션된 환경에서 CloudAP 플러그 인은 사용자 대신 페더레이션 공급자에 의해 반환 되는 SAML 토큰 ' 자격 증명입니다. |
| F | Azure AD PRT에 포함 된 세션 키를 비교 하 여 세션 키 서명 유효성을 검사 하 고, nonce의 유효성을 검사, 장치가 테넌트에서 잘못 되어 새 PRT 문제를 확인 합니다. 전에 볼 수 있듯이 PRT는 다시 전송 키 (tkpub)로 암호화 된 세션 키와 함께 제공 됩니다. |
| G | CloudAP 플러그 인 CloudAP에 암호화 된 PRT 및 세션 키를 전달합니다. CloudAP 요청 전송 키 (tkpriv)를 사용 하 여 세션 키를 해독 하려면 TPM 및 TPM의 키를 사용 하 여 다시 암호화 합니다. CloudAP은 PRT 함께 해당 캐시에 암호화 된 세션 키를 저장합니다. |

### <a name="prt-usage-during-app-token-requests"></a>앱 토큰 요청 중 PRT 사용

![앱 토큰 요청 중 PRT 사용](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| 단계 | 설명 |
| :---: | --- |
| A | WAM 토큰 요청을 초기화 하는 응용 프로그램 (예: Outlook, OneNote 등). 따라서 WAM, 토큰 요청을 처리 하는 Azure AD WAM 플러그 인을 요청 합니다. |
| b | 응용 프로그램에 대 한 새로 고침 토큰을 이미 있는 경우 Azure AD WAM 플러그 인 액세스 토큰을 요청에 사용 됩니다. 장치 바인딩 개념 증명을 제공 하려면 WAM 플러그 인 세션 키를 사용 하 여 요청을 서명 합니다. Azure AD는 세션 키의 유효성을 검사 하 고 액세스 토큰 및 세션 키로 암호화 된 앱에 대 한 새로운 새로 고침 토큰을 발급 합니다. WAM 플러그 인을 두 토큰을 가져오는 WAM 플러그 인에서 결과 세션 키를 사용 하 여 암호를 해독 하는 TPM을 요청 하는 클라우드 AP 플러그 인, 토큰을 해독 하를 요청 합니다. DPAPI 사용 하 여 새로 고침 토큰을 다시 암호화 하는 자체 캐시에 저장 하는 동안 WAM 플러그 인 응용 프로그램에 대 한 액세스 토큰만 제공 하는 다음으로,  |
| C |  응용 프로그램에 대 한 새로 고침 토큰을 사용할 수 없는 경우 Azure AD WAM 플러그 인 PRT를 사용 하 여 액세스 토큰을 요청 합니다. 소유 증명을 제공 하려면 WAM 플러그 인 세션 키를 사용 하 여 PRT를 포함 하는 요청을 서명 합니다. Azure AD는 세션 키 서명에 PRT에 포함 된 세션 키를 비교 하 여 유효성을 검사, 장치 유효 하 고 액세스 토큰 및 새로 고침 토큰으로 응용 프로그램 문제를 확인 합니다. Azure AD (새로 고침 주기 기반) 새 PRT을 실행할 수는 또한 모든 세션 키로 암호화 합니다. |
| D | WAM 플러그 인을 두 토큰을 가져오는 WAM 플러그 인에서 결과 세션 키를 사용 하 여 암호를 해독 하는 TPM을 요청 하는 클라우드 AP 플러그 인, 토큰을 해독 하를 요청 합니다. 다음으로, WAM 플러그 인 다시 DPAPI 사용 하 여 새로 고침 토큰을 암호화 하는 자체 캐시에 저장 하는 동안 응용 프로그램에 대 한 액세스 토큰만을 제공 합니다. WAM 플러그 인에는 앞으로이 응용 프로그램에 대 한 새로 고침 토큰을 사용 합니다. WAM 플러그 인에도 제공 다시 새 PRT 클라우드 AP 플러그 인을 자체 캐시에서를 업데이트 하기 전에 Azure AD 사용 하 여 PRT의 유효성을 검사 합니다. 클라우드 AP 플러그 인에는 앞으로 새 PRT 사용 합니다. |
| E | WAM WAM 차례로 호출 응용 프로그램에 다시 제공 하는 새로 발급 된 액세스 토큰 제공|

### <a name="browser-sso-using-prt"></a>브라우저 SSO PRT를 사용 하 여

![브라우저 SSO PRT를 사용 하 여](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| 단계 | 설명 |
| :---: | --- |
| A | 사용자가 자격 증명을 PRT을 사용 하 여, Windows에 로그인 합니다. 사용자가 브라우저에 열리면 브라우저 (또는 확장) 레지스트리에서 Url 로드 합니다. |
| b | 사용자를 Azure AD 로그인 URL이 열리면 브라우저 또는 확장 레지스트리에서 가져온 것을 사용 하 여 URL을 확인 합니다. 일치 하는 경우 토큰을 가져오기 위한 네이티브 클라이언트 호스트를 호출 하는 브라우저. |
| C | 네이티브 클라이언트 호스트는 Url (Microsoft 계정 또는 Azure AD)는 Microsoft id 공급자에 속하는, URL에서 전송 하는 nonce를 추출 및 PRT 쿠키를 가져오려는 CloudAP 플러그 인을 호출 하는 확인 합니다. |
| D | CloudAP 플러그 인 PRT 쿠키, TPM 바인딩된 세션 키를 사용 하 여 로그인 만들고 네이티브 클라이언트 호스트에 다시 보냅니다. 쿠키는 세션 키로 서명 하는 대로 사용 하 여 변경할 수 없습니다. |
| E | 네이티브 클라이언트 호스트에서는이 PRT 쿠키를 Azure AD에서 x-ms-RefreshTokenCredential 및 요청 토큰을 호출 하는 요청 헤더의 일부로 포함 하는 브라우저에 돌아갈 것입니다. |
| F | Azure AD PRT 쿠키에서 세션 키 서명의 유효성을 검사, nonce의 유효성을 검사, 장치 테넌트에 유효 하 고 웹 페이지에 대 한 ID 토큰 및 암호화 된 세션 쿠키를 브라우저에 대 한 문제를 확인 합니다. |

## <a name="next-steps"></a>다음 단계

PRT 관련 문제 해결에 대 한 자세한 내용은 문서 참조 [Troubleshooting 하이브리드 Azure Active Directory 가입 Windows 10 및 Windows Server 2016 장치](troubleshoot-hybrid-join-windows-current.md)합니다.

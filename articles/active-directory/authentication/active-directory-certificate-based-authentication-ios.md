---
title: IOS에서 인증서 기반 인증-Azure Active Directory
description: IOS 장치를 사용 하 여 솔루션의 Azure Active Directory에 대 한 인증서 기반 인증을 구성 하기 위한 요구 사항 및 지원 되는 시나리오에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 584fb5c370fa0be629d057eb94dc4c2a8b9edc15
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716438"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS에서 Azure Active Directory 인증서 기반 인증

보안을 개선 하기 위해 iOS 장치는 CBA (인증서 기반 인증)를 사용 하 여 다음 응용 프로그램 또는 서비스에 연결할 때 장치에서 클라이언트 인증서를 사용 하 여 Azure Active Directory (Azure AD)에 인증할 수 있습니다.

* Microsoft Outlook 및 Microsoft Word와 같은 Office 모바일 애플리케이션
* EAS(Exchange ActiveSync) 클라이언트

인증서를 사용 하면 모바일 장치에서 특정 메일 및 Microsoft Office 응용 프로그램에 사용자 이름 및 암호 조합을 입력할 필요가 없습니다.

이 문서에서는 iOS 장치에서 CBA를 구성 하기 위한 요구 사항 및 지원 되는 시나리오에 대해 자세히 설명 합니다. IOS 용 CBA는 Azure 공용 클라우드, Microsoft 정부 클라우드, Microsoft 클라우드 독일 및 Microsoft Azure 중국 21Vianet에서 사용할 수 있습니다.

## <a name="microsoft-mobile-applications-support"></a>Microsoft 모바일 애플리케이션 지원

| 앱 | 지원 |
| --- | --- |
| Azure Information Protection 앱 |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Intune 회사 포털 |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Microsoft Teams |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Office (모바일) |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| OneNote |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| OneDrive |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Outlook |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Power BI |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| 비즈니스용 Skype |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Word / Excel / PowerPoint |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |
| Yammer |![이 응용 프로그램에 대 한 지원을 나타내는 확인 표시][1] |

## <a name="requirements"></a>요구 사항

IOS에서 CBA를 사용 하려면 다음 요구 사항 및 고려 사항이 적용 됩니다.

* 장치 OS 버전은 iOS 9 이상 이어야 합니다.
* Microsoft Authenticator는 iOS의 Office 애플리케이션에 필요합니다.
* ADFS 서버의 인증 URL을 포함 하는 macOS 키 집합에 id 기본 설정을 만들어야 합니다. 자세한 내용은 [Mac에서 키 집합 액세스에서 id 기본 설정 만들기](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)를 참조 하세요.

다음 Active Directory Federation Services (ADFS) 요구 사항 및 고려 사항이 적용 됩니다.

* 인증서 인증을 위해 ADFS 서버를 사용 하도록 설정 하 고 페더레이션 인증을 사용 해야 합니다.
* 인증서에는 EKU (확장 된 키 사용)를 사용 해야 하 고 *주체 대체 이름 (NT Principal name)* 에 사용자의 UPN이 포함 되어야 합니다.

## <a name="configure-adfs"></a>ADFS 구성

Azure AD에서 클라이언트 인증서를 해지 하려면 ADFS 토큰에 다음 클레임이 있어야 합니다. Azure AD는 ADFS 토큰 또는 다른 SAML 토큰에서 사용할 수 있는 경우 새로 고침 토큰에 이러한 클레임을 추가 합니다. 새로 고침 토큰의 유효성을 검사 해야 하는 경우이 정보는 해지를 확인 하는 데 사용 됩니다.

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -클라이언트 인증서의 일련 번호를 추가 합니다.
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -클라이언트 인증서 발급자에 대 한 문자열을 추가 합니다.

또한 다음 정보를 사용 하 여 조직의 ADFS 오류 페이지를 업데이트 해야 하는 것이 좋습니다.

* IOS에 Microsoft Authenticator를 설치 하기 위한 요구 사항입니다.
* 사용자 인증서를 얻는 방법에 대한 지침

자세한 내용은 [AD FS 로그인 페이지 사용자 지정](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11))을 참조 하세요.

## <a name="use-modern-authentication-with-office-apps"></a>Office 앱에서 최신 인증 사용

최신 인증을 사용 하도록 설정 된 일부 Office 앱 `prompt=login` 은 요청 시 AZURE AD로 전송 됩니다. 기본적으로 Azure AD는 adfs `prompt=login` 에 대 한 요청에서 `wauth=usernamepassworduri` (U/P 인증을 수행 하도록 adfs에 요청)을 변환 하 고 `wfresh=0` (adfs는 SSO 상태를 무시 하 고 새 인증을 수행 하도록 요청 함). 이러한 앱에 대해 인증서 기반 인증을 사용 하도록 설정 하려면 기본 Azure AD 동작을 수정 합니다.

기본 동작을 업데이트 하려면 페더레이션된 도메인 설정의 '*PromptLoginBehavior*'을 *사용 안 함*으로 설정 합니다. 다음 예제와 같이 [get-msoldomainfederationsettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet을 사용 하 여이 작업을 수행할 수 있습니다.

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Exchange ActiveSync 클라이언트에 대 한 지원

iOS 9 이상에서, 네이티브 iOS 메일 클라이언트가 지원됩니다. 다른 모든 Exchange ActiveSync 응용 프로그램에서이 기능을 지원 하는지 확인 하려면 응용 프로그램 개발자에 게 문의 하십시오.

## <a name="next-steps"></a>다음 단계

사용자 환경에서 인증서 기반 인증을 구성 하려면 [인증서 기반 인증 시작](active-directory-certificate-based-authentication-get-started.md) 에서 지침을 참조 하세요.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
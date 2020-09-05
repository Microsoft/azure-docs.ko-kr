---
title: PhoneFactor를 Azure MFA 서버로 업그레이드-Azure Active Directory
description: 이전 phonefactor agent에서 업그레이드하는 경우 Azure MFA 서버로 시작합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e485c55ab591bcccdc32818839a8d484a6c3af
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919542"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>PhoneFactor Agent를 Azure Multi-Factor Authentication 서버로 업그레이드

PhoneFactor Agent v5.x 또는 이전 버전을 Azure Multi-Factor Authentication 서버로 업그레이드하려면 PhoneFactor Agent 및 연결된 구성 요소를 먼저 삭제합니다. 그런 다음 Multi-Factor Authentication 서버 및 연결된 구성 요소를 설치할 수 있습니다.

> [!IMPORTANT]
> 2019 년 7 월 1 일부 터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공 하지 않습니다. 로그인 이벤트 중에 MFA (multi-factor authentication)를 요구 하려는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다.
>
> 클라우드 기반 MFA를 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.
>
> 2019 년 7 월 1 일 이전에 MFA 서버를 정품 인증 한 기존 고객은 평소와 같이 최신 버전, 향후 업데이트 및 활성화 자격 증명 생성을 다운로드할 수 있습니다.

## <a name="uninstall-the-phonefactor-agent"></a>PhoneFactor Agent 제거

1. 먼저 PhoneFactor 데이터 파일을 백업합니다. 기본 설치 위치는 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata입니다.

2. 사용자 포털이 설치된 경우
   1. 설치 폴더로 이동한 다음 web.config 파일을 백업합니다. 기본 설치 위치는 C:\inetpub\wwwroot\PhoneFactor입니다.

   2. 포털에 사용자 지정 테마를 추가한 경우 C:\inetpub\wwwroot\PhoneFactor\App_Themes 디렉터리 아래에 사용자 지정 폴더를 백업합니다.

   3. PhoneFactor Agent(PhoneFactor Agent와 동일한 서버에 설치된 경우에만 사용 가능) 또는 Windows 프로그램 및 기능을 통해 사용자 포털을 제거합니다.

3. 모바일 앱 웹 서비스를 설치하려면:

   1. 설치 폴더로 이동한 다음 web.config 파일을 백업합니다. 기본 설치 위치는 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService입니다.

   2. Windows 프로그램 및 기능을 통해 모바일 앱 웹 서비스를 제거합니다.

4. 웹 서비스 SDK가 설치된 경우 PhoneFactor Agent 또는 Windows 프로그램 및 기능을 통해 제거합니다.

5. Windows 프로그램 및 기능을 통해 PhoneFactor Agent를 제거합니다.

## <a name="install-the-multi-factor-authentication-server"></a>Multi-Factor Authentication 서버 설치

이전 PhoneFactor Agent 설치의 레지스트리에서 설치 경로가 선택되므로 동일한 위치(예: C:\Program Files\PhoneFactor)에 설치되어야 합니다. 새 설치는 다른 기본 설치 경로(예: C:\Program Files\multi-factor Authentication Server)를 갖게 됩니다. 이전 PhoneFactor Agent에 의해 남아 있는 데이터 파일은 설치 중에 업그레이드되므로 새 Multi-Factor Authentication 서버를 설치한 후에도 사용자 및 설정이 해당 위치에 그대로 남아 있습니다.

1. Multi-Factor Authentication 서버의 정품 인증을 받으라는 메시지가 표시되면 진행하고 올바른 복제 그룹에 할당되었는지 확인합니다.

2. 웹 서비스 SDK를 이전에 설치한 경우 Multi-Factor Authentication 서버 사용자 인터페이스를 통해 새 웹 서비스 SDK를 설치합니다.

   기본 가상 디렉터리 이름은 이제 **가 됩니다**대신 **phonefactorwebservicesdk** 입니다. 이전 이름을 사용하려는 경우 설치하는 동안 가상 디렉터리의 이름을 변경해야 합니다. 그렇지 않은 경우 새 기본 이름을 사용하여 설치할 수 있으면 사용자 포털 및 모바일 앱 웹 서비스 등의 웹 서비스 SDK를 참조하는 모든 애플리케이션의 URL이 올바른 위치를 가리키도록 변경해야 합니다.

3. 사용자 포털이 이전에 PhoneFactor Agent 서버에 설치된 경우 Multi-Factor Authentication 서버 사용자 인터페이스를 통해 새 Multi-Factor Authentication 사용자 포털을 설치합니다.

   기본 가상 디렉터리 이름은 이제 **PhoneFactor**대신 **MultiFactorAuth** 입니다. 이전 이름을 사용하려는 경우 설치하는 동안 가상 디렉터리의 이름을 변경해야 합니다. 그렇지 않은 경우 설치 중에 새 기본 이름이 사용되도록 허용하면 Multi-Factor Authentication 서버에서 사용자 포털 아이콘을 클릭하고 설정 탭에서 사용자 포털 URL을 업데이트해야 합니다.

4. 사용자 포털 및/또는 모바일 앱 웹 서비스가 이전에 PhoneFactor Agent와 다른 서버에 설치된 경우:

   1. 설치 위치(예: C:\Program Files\PhoneFactor)로 이동한 후에 하나 이상의 설치 관리자를 다른 서버로 복사합니다. 사용자 포털 및 모바일 앱 웹 서비스에 대해 32비트 및 64비트의 설치 관리자가 있습니다. 이러한 설치 관리자는 MultiFactorAuthenticationUserPortalSetupXX.msi 및 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi입니다.

   2. 웹 서버에 사용자 포털을 설치하려면 관리자 권한으로 명령 프롬프트를 열고 MultiFactorAuthenticationUserPortalSetupXX.msi를 실행합니다.

      기본 가상 디렉터리 이름은 이제 **PhoneFactor**대신 **MultiFactorAuth** 입니다. 이전 이름을 사용하려는 경우 설치하는 동안 가상 디렉터리의 이름을 변경해야 합니다. 그렇지 않고 새 기본 이름을 사용 하 여 설치를 허용 하는 경우 Multi-Factor Authentication 서버의 사용자 포털 아이콘을 클릭 하 고 설정 탭에서 사용자 포털 URL을 업데이트 해야 합니다. 기존 사용자에 게 새 URL을 알려야 합니다.

   3. 사용자 포털 설치 위치(예: C:\inetpub\wwwroot\MultiFactorAuth)로 이동한 후 web.config 파일을 편집합니다. 새 web.config 파일로 업그레이드하기 전에 백업했던 원본 web.config 파일에서 appSettings 및 applicationSettings 섹션의 값을 복사합니다. 웹 서비스 SDK를 설치할 때 새 기본 가상 디렉터리 이름을 유지한 경우 applicationSettings 섹션의 URL이 올바른 위치를 가리키도록 변경합니다. 이전 web.config 파일에서 기타 기본값을 변경한 경우 새 web.config 파일에 같은 변경 내용을 적용합니다.

> [!NOTE]
> Azure MFA Server 8.0 미만 버전에서 8.0 이상 버전으로 업그레이드할 때 업그레이드 후 모바일 앱 웹 서비스를 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure Multi-Factor Authentication 서버의 [사용자 포털을 설치](howto-mfaserver-deploy-userportal.md)합니다.

- 애플리케이션에 대한 [Windows 인증](howto-mfaserver-windows.md)을 구성합니다. 

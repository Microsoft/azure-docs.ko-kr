---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60297714"
---
## <a name="test-your-code"></a>코드 테스트

**F5**를 선택하여 Visual Studio에서 프로젝트를 실행합니다. 애플리케이션 **MainWindow**는 다음과 같이 표시됩니다.

![애플리케이션 테스트](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

처음으로 애플리케이션을 실행하여 **Call Microsoft Graph API** 단추를 선택하는 것이라면 로그인하라는 메시지가 표시됩니다. Azure Active Directory 계정(회사 또는 학교 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용해 테스트합니다.

![애플리케이션에 로그인합니다.](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음으로 로그인하면 다음과 같이 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지도 표시됩니다.

![애플리케이션 액세스에 대한 사용자 동의 제공](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>애플리케이션 결과 보기

로그인하면, Microsoft Graph API에 대한 호출에서 반환되는 사용자 프로필 정보가 표시됩니다. 결과가 **API 호출 결과** 상자에 표시됩니다. `AcquireTokenInteractive` 또는 `AcquireTokenSilent`에 대한 호출을 통해 획득한 토큰에 관한 기본 정보는 **토큰 정보** 상자에 표시되어야 합니다. 결과에 다음 속성이 포함될 수 있습니다.

|자산  |형식  |설명 |
|---------|---------|---------|

|**사용자 이름** |<span>user@domain.com</span> | 사용자를 식별하는 데 사용되는 사용자 이름입니다.||**토큰 만료** |날짜/시간 |토큰이 만료되는 시간입니다. MSAL은 필요에 따라 토큰을 갱신하여 만료 날짜를 연장합니다.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 애플리케이션 등록 포털에서 등록된 모든 애플리케이션에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 *Calendars.Read* 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

애플리케이션의 컨텍스트에서 사용자 일정에 액세스하려면 애플리케이션 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다.

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]

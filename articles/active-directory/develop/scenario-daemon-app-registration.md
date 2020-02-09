---
title: 웹 Api를 호출 하는 디먼 앱 등록-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 디먼 앱을 빌드하는 방법 알아보기-앱 등록
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773387"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>웹 Api를 호출 하는 디먼 앱-앱 등록

디먼 응용 프로그램의 경우 앱을 등록할 때 알아야 할 사항은 다음과 같습니다.

## <a name="supported-account-types"></a>지원되는 계정 유형

디먼 응용 프로그램은 Azure AD 테넌트에서만 의미가 있습니다. 따라서 응용 프로그램을 만들 때 다음 옵션 중 하나를 선택 해야 합니다.

- **이 조직 디렉터리의 계정에만 해당**됩니다. 디먼 응용 프로그램은 일반적으로 LOB (기간 업무) 개발자가 작성 하기 때문에이 선택은 가장 일반적입니다.
- **모든 조직 디렉터리의 계정** 유틸리티 도구를 고객에 게 제공 하는 ISV 인 경우이 옵션을 선택 합니다. 승인 하려면 고객의 테넌트 관리자가 필요 합니다.

## <a name="authentication---no-reply-uri-needed"></a>인증-회신 URI가 필요 하지 않음

기밀 클라이언트 응용 프로그램이 클라이언트 자격 증명 흐름을 *사용 하는 경우* 에는 회신 URI를 등록할 필요가 없습니다. 응용 프로그램 구성 또는 생성에 필요 하지 않습니다. 클라이언트 자격 증명 흐름은이를 사용 하지 않습니다.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API 권한-앱 사용 권한 및 관리자 동의

디먼 응용 프로그램은 Api (위임 된 권한 아님)에 대 한 응용 프로그램 권한만 요청할 수 있습니다. 응용 프로그램 등록에 대 한 **api 권한** 페이지에서 **사용 권한 추가** 를 선택 하 고 api 패밀리를 선택한 후 **응용 프로그램 권한**을 선택 하 고 사용 권한을 선택 합니다.

![앱 사용 권한 및 관리자 동의](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 호출 하려는 웹 API는 위임 된 권한이 아닌 *응용 프로그램 사용 권한 (앱 역할)* 을 정의 해야 합니다. 이러한 API를 노출 하는 방법에 대 한 자세한 내용은 [보호 된 웹 api: 앱 등록-디먼 앱에서 웹 api를 호출](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)하는 경우를 참조 하세요.

디먼 응용 프로그램은 웹 API를 호출 하는 응용 프로그램에 대해 테넌트 관리자가 사전 동의 해야 합니다. 테넌트 관리자는  ***조직* 에 관리자 동의 부여** 를 선택 하 여 동일한 **API 사용 권한** 페이지에서이 동의를 제공 합니다.

다중 테넌트 응용 프로그램을 빌드하는 ISV 인 경우, [다중 테넌트 디먼 앱의 배포-사례](scenario-daemon-production.md#deployment---multitenant-daemon-apps)섹션을 참조 하세요.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [디먼 앱-앱 코드 구성](./scenario-daemon-app-configuration.md)

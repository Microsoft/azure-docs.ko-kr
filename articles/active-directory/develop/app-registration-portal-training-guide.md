---
title: 새로운 Azure Portal 앱 등록 환경
titleSuffix: Microsoft identity platform
description: Azure Portal의 새로운 앱 등록 환경 소개
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: aragra
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 835924c92d0c5768c1125b3b1d26ee72cf2cec70
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698274"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>새 Azure Portal 앱 등록 환경

Azure Portal의 새로운 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서는 많은 기능이 향상 되었습니다. 이전 경험 이라고 하는 수렴 형 응용 프로그램을 등록 하거나 관리 하는 apps.dev.microsoft.com (응용 프로그램 등록 포털) 환경을 더 잘 알고 있다면이 교육 가이드에서 새로운 환경을 사용 하 여 시작 해 볼 수 있습니다.

## <a name="whats-not-changing"></a>변경 되지 않는 항목

- 응용 프로그램 및 관련 구성은 새 환경에서 있는 그대로 찾을 수 있습니다. 응용 프로그램을 다시 등록할 필요는 없으며, 응용 프로그램의 사용자는 다시 로그인 할 필요가 없습니다.

    > [!NOTE]
    > Azure Portal에서 찾을 수 있도록 응용 프로그램을 등록 하는 데 사용한 계정으로 로그인 해야 합니다. 사용자 프로필의 전자 메일 주소를 비교 하 여 응용 프로그램 등록 포털에 로그인 한 사용자와 일치 하는 Azure Portal의 로그인 사용자를 확인 하는 것이 좋습니다.
    > 
    > 경우에 따라, Azure AD 전자 메일 주소를 사용 하 여 개인 Microsoft 계정 (예: Outlook, Live, Xbox 등)을 사용 하 여 로그인 하는 경우, 이전 환경에서 Azure Portal로 이동 하면 동일한 계정으로 다른 계정으로 로그인 하는 것을 알 수 있습니다. Azure AD 테넌트에서 전자 메일을 보냅니다. 그래도 응용 프로그램이 없는 것으로 판단 되 면 로그 아웃 하 고 올바른 계정으로 로그인 합니다.

- 개인 Microsoft 계정을 사용 하 여 만든 라이브 SDK 앱은 Azure Portal에서 아직 지원 되지 않으며, 향후에는 이전 환경에서 계속 유지 됩니다.

## <a name="key-changes"></a>주요 변경 내용

-   이전 환경에서 앱은 기본적으로 모든 조직 계정 (다중 테넌트) 및 개인 Microsoft 계정을 지 원하는 수렴 형 앱으로 등록 되었습니다. 이전 환경을 통해 수정할 수 없어 조직 계정 (다중 테넌트 또는 단일 테넌트)만 지원 되는 앱을 만드는 것이 어려워집니다.
    새 환경을 통해 이러한 모든 옵션을 지 원하는 앱을 등록할 수 있습니다. [앱 형식에 대해 자세히 알아보세요](active-directory-v2-registration-portal.md).

-   새 환경에서 개인 Microsoft 계정 Azure AD 테넌트에도 있는 경우 모든 응용 프로그램, 테넌트의 소유 응용 프로그램, 개인 계정의 응용 프로그램 등 세 개의 탭이 표시 됩니다. 따라서 개인 Microsoft 계정에 등록 된 앱이 없는 것으로 판단 되 면 **개인 계정 탭에서 응용 프로그램** 을 확인 합니다.

-   새 환경에서는 프로필로 이동 하 고 디렉터리 전환을 선택 하 여 테넌트 간에 쉽게 전환할 수 있습니다.

## <a name="list-of-applications"></a>애플리케이션 목록

-   새 앱 목록에는 Azure Portal의 레거시 앱 등록 환경 (Azure AD 계정에만 로그인 하는 앱) 뿐만 아니라 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/) (azure ad 및 개인 Microsoft 계정에 로그인 하는 앱)을 통해 등록 된 앱이 표시 됩니다.

-   새 앱 목록에는 앱에 등록 된 자격 증명의 상태 (현재, 곧 만료 됨 또는 만료 됨)를 표시 하는 **암호 열 &** 열과 인증서 **에 생성** 된 두 개의 추가 열이 있습니다.

## <a name="new-app-registration"></a>새 앱 등록

이전 환경에서 수렴 형 앱을 등록 하려면 이름을 제공 해야 했습니다. 만들어진 앱은 모든 조직 디렉터리 (다중 테넌트) 및 개인 Microsoft 계정을 지 원하는 수렴 형 앱으로 등록 되었습니다.  이전 환경을 통해 수정할 수 없어 조직 계정 (다중 테넌트 또는 단일 테넌트)만 지원 되는 앱을 만드는 것이 어려워집니다. [지원 되는 계정 유형에 대 한 자세한 정보](v2-supported-account-types.md)

새 환경에서는 앱의 이름을 제공 하 고 지원 되는 계정 유형을 선택 해야 합니다. 선택적으로 리디렉션 URI를 제공할 수 있습니다.
리디렉션 URI를 제공 하는 경우 웹/공용 (네이티브/모바일 및 데스크톱) 인지를 지정 해야 합니다. 새 앱 등록 환경을 사용 하 여 앱을 등록 하는 방법에 대 한 자세한 내용은 [이 빠른](quickstart-register-app.md)시작을 참조 하세요.

## <a name="app-management-page"></a>앱 관리 페이지

이전 환경에는 속성, 응용 프로그램 비밀, 플랫폼, 소유자, Microsoft Graph 권한, 프로필 및 고급 옵션과 같은 섹션이 포함 된 수렴 형 앱에 대 한 단일 앱 관리 페이지가 있었습니다.

Azure Portal의 새로운 환경은 이러한 기능을 개별 페이지로 나타냅니다. 해당 하는 기능을 찾을 수 있는 위치는 다음과 같습니다.

-   속성-이름 및 응용 프로그램 ID는 개요 페이지에 있습니다.

-   응용 프로그램 암호는 인증서 & 암호 페이지에 있습니다.

-   플랫폼 구성이 인증 페이지에 있습니다.

-   Microsoft Graph 권한은 다른 사용 권한과 함께 API 사용 권한 페이지에 있습니다.

-   프로필이 브랜딩 페이지에 있습니다.

-   고급 옵션-라이브 SDK 지원은 인증 페이지에 있습니다.

## <a name="application-secretscertificates--secrets"></a>응용 프로그램 비밀/인증서 & 비밀

새 환경에서 **응용 프로그램 암호** 는 **인증서 & 암호**로 이름이 변경 되었습니다. 또한 **공개 키** 를 **인증서** 라고 하며 **암호** 를 **클라이언트**암호 라고도 합니다. 보안상의 이유로이 기능을 새로운 환경에 제공 하지 않도록 선택 했으므로 더 이상 새 키 쌍을 생성할 수 없습니다.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>플랫폼/인증: 회신 Url/리디렉션 Uri
이전 환경에서 앱은 웹, 네이티브 및 웹 API에 대 한 플랫폼 섹션을 포함 하 여 리디렉션 Url, 로그 아웃 URL 및 암시적 흐름을 구성 했습니다.

새 환경에서 회신 Url은 앱\'s 인증 섹션에서 찾을 수 있습니다. 또한 리디렉션 Uri로 참조 되 고 리디렉션 Uri에 대 한 형식이 변경 되었습니다. 앱 유형 (웹 또는 공용 클라이언트-모바일 및 데스크톱)에 연결 해야 합니다. [자세히 알아보기](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

웹 Api는 API 페이지 노출에서 구성 됩니다.

> [!NOTE] 
> 대상으로 지정할 플랫폼 또는 장치에 따라 응용 프로그램에 대 한 설정을 구성할 수 있는 새로운 인증 설정 환경을 사용해 보세요. [자세히 알아보기](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Microsoft Graph 사용 권한/a p i 권한

-   이전 환경에서 API를 선택할 때 Microsoft Graph Api 에서만 선택할 수 있습니다. 새 환경에서 Microsoft Graph, 조직 및 Api의 Api를 비롯 한 다양 한 Microsoft Api 중에서 선택할 수 있습니다 .이는 Microsoft Api, 내 조직에서 사용 하는 api 또는 내 Api의 세 가지 탭으로 제공 됩니다. Api의 검색 표시줄 내 조직에서는 테넌트의 서비스 주체를 통해 탭 검색을 사용 합니다.
    
    > [!NOTE] 
    > 응용 프로그램이 테넌트와 연결 되지 않은 경우이 탭이 표시 되지 않습니다. 새 환경을 사용 하 여 사용 권한을 요청 하는 방법에 대 한 자세한 내용은 [이 빠른](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)시작을 참조 하세요.

-   이전 환경에 **권한 부여** 단추가 없습니다. 새 환경에는 앱의 API 사용 권한 섹션에 **관리자 동의 부여** 단추가 있는 grant 동의 섹션이 있습니다. 관리자만 동의를 부여할 수 있고이 단추는 관리자만 사용할 수 있습니다. 관리자가 **관리자 동의 허용** 단추를 선택 하면 모든 요청 된 권한에 대해 관리자 동의가 부여 됩니다.

## <a name="profile"></a>프로필
이전 환경에서 프로필에는 로고, 홈 페이지 URL, 서비스 약관 URL 및 개인정보 취급 방침 URL 구성이 있습니다. 새 환경에서는 브랜딩 페이지에서 이러한 기능을 찾을 수 있습니다.

## <a name="application-manifest"></a>애플리케이션 매니페스트.
새 환경에서 매니페스트 페이지를 사용 하 여 앱의 특성을 편집 하 고 업데이트할 수 있습니다. 자세한 내용은 [애플리케이션 매니페스트](reference-app-manifest.md)를 참조하세요.

## <a name="new-ui"></a>새 UI
이전에 매니페스트 편집기나 API를 사용 하 여 설정 하거나 존재 하지 않는 속성에 대 한 새로운 UI가 있습니다.

-   Oauth2AllowImplicitFlow (암시적 부여 흐름)는 인증 페이지에서 찾을 수 있습니다. 이전 환경과 달리 액세스 토큰 또는 ID 토큰을 사용 하거나 둘 모두를 사용할 수 있습니다.

-   API (oauth2Permissions) 및 권한 있는 클라이언트 응용 프로그램 (preAuthorizedApplications)에서 정의 된 범위는 API 노출 페이지를 통해 구성할 수 있습니다. 앱을 web API로 구성 하 고 사용 권한/범위를 노출 하는 방법에 대 한 자세한 내용은 [이 빠른](quickstart-configure-app-expose-web-apis.md)시작을 참조 하세요.

-   [응용 프로그램\'s 동의 프롬프트](application-consent-experience.md)에서 사용자에 게 표시 되는 게시자 도메인은 브랜딩 블레이드 페이지에서 찾을 수 있습니다. 게시자 도메인을 구성 하는 방법에 대 한 자세한 내용은 [이 방법을](howto-configure-publisher-domain.md)참조 하세요.

## <a name="limitations"></a>제한 사항

새 환경에는 다음과 같은 제한 사항이 있습니다.

-   새 환경은 Azure AD B2C 테넌트에 대 한 앱 등록를 아직 지원 하지 않습니다.

-   새 환경에서는 개인 Microsoft 계정을 사용 하 여 만든 라이브 SDK 앱을 아직 지원 하지 않습니다.

-   지원 되는 계정에 대 한 값 변경은 UI에서 지원 되지 않습니다. Azure AD 단일 테넌트와 다중 테넌트 간에 다시 전환\'않는 한 앱 매니페스트를 사용 해야 합니다.

   > [!NOTE]
   > Azure AD 테넌트에서 개인 Microsoft 계정 사용자이 고 테넌트 관리자가 Azure Portal에 대 한 액세스를 제한 한 경우 액세스가 거부 될 수 있습니다. 그러나 검색 표시줄에 앱 등록를 입력 하거나이를 고정 하 여 바로 가기를 사용 하는 경우 새 환경에 액세스할 수 있습니다.

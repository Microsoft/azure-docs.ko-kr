---
title: 지원 되는 계정 유형 별 유효성 검사 차이-Microsoft identity platform | Microsoft
description: Microsoft id 플랫폼에 앱을 등록할 때 지원 되는 다른 계정 유형에 대 한 다양 한 속성의 유효성 검사 차이점에 대해 알아봅니다.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 812ca0d502572f43c968c75dee17f45d066bcf04
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701300"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>지원 되는 계정 유형 별 유효성 검사 차이 (signInAudience)

개발자를 위한 Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 등록할 때 응용 프로그램에서 지 원하는 계정 유형을 선택 하 라는 메시지가 표시 됩니다. 응용 프로그램 개체 및 매니페스트에서이 속성은 `signInAudience`입니다.

다음과 같은 옵션이 있습니다.

- *AzureADMyOrg*: 앱이 등록 된 조직 디렉터리의 계정만 (단일 테넌트)
- *AzureADMultipleOrgs*: 모든 조직 디렉터리의 계정 (다중 테넌트)
- *AzureADandPersonalMicrosoftAccount*: 모든 조직 디렉터리의 계정 (다중 테넌트) 및 개인 Microsoft 계정 (예: Skype, Xbox 및 Outlook.com)

등록 된 응용 프로그램의 경우 응용 프로그램의 **인증** 섹션에서 지원 되는 계정 유형 값을 찾을 수 있습니다. **매니페스트의**`signInAudience` 속성 아래에서 찾을 수도 있습니다.

이 속성에 대해 선택 하는 값은 다른 앱 개체 속성에 영향을 미칩니다. 따라서이 속성을 변경 하는 경우 먼저 다른 속성을 변경 해야 할 수 있습니다.

지원 되는 다른 계정 유형에 대 한 다양 한 속성의 유효성 검사 차이점은 다음 표를 참조 하세요.

| 속성 | `AzureADMyOrg` | `AzureADMultipleOrgs`  | `AzureADandPersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| 응용 프로그램 ID URI (`identifierURIs`)  | 테넌트에서 고유 해야 합니다. <br><br> urn://체계가 지원 됩니다. <br><br> 와일드 카드는 지원 되지 않습니다. <br><br> 쿼리 문자열 및 조각이 지원 됩니다. <br><br> 최대 255 문자 길이 <br><br> IdentifierURIs 수에 제한 없음 *  | 전역적으로 고유해야 합니다. <br><br> urn://체계가 지원 됩니다. <br><br> 와일드 카드는 지원 되지 않습니다. <br><br> 쿼리 문자열 및 조각이 지원 됩니다. <br><br> 최대 255 문자 길이 <br><br> IdentifierURIs 수에 제한 없음 * | 전역적으로 고유해야 합니다. <br><br> urn://체계가 지원 되지 않습니다. <br><br> 와일드 카드, 조각 및 쿼리 문자열은 지원 되지 않습니다. <br><br> 최대 120 문자 길이 <br><br> 최대 50 identifierURIs |
| 인증서 (`keyCredentials`) | 대칭 서명 키 | 대칭 서명 키 | 암호화 및 비대칭 서명 키 | 
| 클라이언트 암호 (`passwordCredentials`) | 제한 없음 * | 제한 없음 * | LiveSDK를 사용 하는 경우: 최대 2 개의 클라이언트 암호 | 
| 리디렉션 Uri (`replyURLs`) | 자세한 정보는 [리디렉션 URI/회신 URL 제한 및 제한 사항](reply-url.md) 을 참조 하세요. | | | 
| API 권한 (`requiredResourceAccess`) | 제한 없음 * | 제한 없음 * | 허용 되는 리소스 당 최대 30 개의 사용 권한 (예: Microsoft Graph) | 
| 이 API로 정의 된 범위 (`oauth2Permissions`) | 최대 범위 이름 길이 120 문자 <br><br> 정의 된 범위 수에 제한 없음 * | 최대 범위 이름 길이 120 문자 <br><br> 정의 된 범위 수에 제한 없음 * |  최대 범위 이름 길이 40 문자 <br><br> 최대 100 범위가 정의 되었습니다. | 
| 권한 있는 클라이언트 응용 프로그램 (`preautorizedApplications`) | 제한 없음 * | 제한 없음 * | 총 500 <br><br> 정의 된 최대 100 클라이언트 앱 <br><br> 클라이언트 당 최대 30 개 범위 정의 | 
| appRoles | 지원됨 <br> 제한 없음 * | 지원됨 <br> 제한 없음 * | 지원하지 않음 | 
| 로그아웃 URL | http://localhost 허용 됨 <br><br> 최대 255 문자 길이 | http://localhost 허용 됨 <br><br> 최대 255 문자 길이 | <br><br> https://localhost 허용 되며, http://localhost MSA에 대해 실패 합니다. <br><br> 최대 255 문자 길이 <br><br> HTTP 체계가 허용 되지 않습니다. <br><br> 와일드 카드는 지원 되지 않습니다. | 

\* 앱 개체의 모든 컬렉션 속성에서 약 1000 항목의 전역 제한이 있습니다.

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 등록](app-objects-and-service-principals.md) 에 대 한 자세한 정보
- [응용 프로그램 매니페스트에](reference-app-manifest.md) 대 한 자세한 정보

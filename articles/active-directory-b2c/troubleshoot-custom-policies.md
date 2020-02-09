---
title: Azure Active Directory B2C에서 사용자 지정 정책 문제 해결
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하는 경우 오류를 해결하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b9a0e370643d7f2a19e6ca1629b6b522bfa396b9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848136"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Azure AD B2C 사용자 지정 정책 및 Identity Experience Framework 문제 해결

Azure AD B2C(Azure Active Directory B2C) 사용자 지정 정책을 사용하는 경우 정책 언어 XML 형식으로 Identity Experience Framework를 설정하는 동안 문제가 발생할 수 있습니다. 사용자 지정 정책을 작성하는 방법은 새로운 언어를 배우는 것과 유사할 수 있습니다. 이 문서에서는 문제를 검색 하 고 해결 하는 데 도움이 되는 몇 가지 도구와 팁을 설명 합니다.

이 문서에서는 Azure AD B2C 사용자 지정 정책 구성의 문제 해결을 중점적으로 다룹니다. 신뢰 당사자 애플리케이션 또는 ID 라이브러리는 다루지 않습니다.

## <a name="xml-editing"></a>XML 편집

사용자 지정 정책 설정에서 가장 일반적인 오류는 형식이 잘못 지정된 XML입니다. 좋은 XML 편집기가 거의 필수입니다. XML을 기본적으로 표시 하 고, 색 코드 내용을 미리 채우고, XML 요소를 인덱싱한 상태로 유지 하 고, XML 스키마에 대해 유효성을 검사할 수 있습니다.

즐겨 사용 하는 두 편집기는 [Visual Studio Code](https://code.visualstudio.com/) 및 [메모장 + +](https://notepad-plus-plus.org/)입니다.

XML 파일을 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다. [시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)의 루트 폴더에서 XML 스키마 정의 파일 *TrustFrameworkPolicy_0 3.0.0*를 가져옵니다. 편집기에서 XSD 스키마 파일을 유효성 검사에 사용 하는 방법을 알아보려면 편집기 설명서에서 *xml 도구* 및 *xml 유효성 검사* 를 찾아보십시오.

XML 규칙 검토가 도움이 될 수도 있습니다. Azure AD B2C는 검색된 XML 형식 오류를 거부합니다. 경우에 따라 형식이 잘못된 XML로 인해 잘못된 오류 메시지가 발생할 수 있습니다.

## <a name="upload-policies-and-policy-validation"></a>정책 업로드 및 정책 유효성 검사

업로드 시 XML 정책 파일의 유효성 검사가 자동으로 수행 됩니다. 대부분의 오류로 인해 업로드가 실패합니다. 유효성 검사에는 업로드하는 정책 파일이 포함됩니다. 또한 업로드 파일에서 참조하는 파일 체인(신뢰 당사자 정책 파일, 확장 파일 및 기본 파일)이 포함됩니다.

일반적인 유효성 검사 오류는 다음과 같습니다.

> 오류 코드 조각: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* ClaimType 값의 철자가 틀렸거나 ClaimType 값이 스키마에 없습니다.
* ClaimType 값이 정책 파일 중 하나 이상에 정의되어 있어야 합니다.
    예: `<ClaimType Id="issuerUserId">`
* ClaimType이 확장 파일에 정의되어 있지만 기본 파일의 TechnicalProfile 값에도 사용된 경우 기본 파일을 업로드하면 오류가 발생합니다.

> 오류 코드 조각: `...makes a reference to a ClaimsTransformation with id...`

* 이 오류의 원인은 ClaimType 오류와 같을 수 있습니다.

> 오류 코드 조각: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* `<TrustFrameworkPolicy\>` 및 `<BasePolicy\>` 요소의 TenantId 값이 대상 Azure AD B2C 테넌트와 일치 하는지 확인 합니다.

## <a name="troubleshoot-the-runtime"></a>런타임 문제 해결

* **지금 실행** 및 `https://jwt.ms`를 사용 하 여 웹 또는 모바일 응용 프로그램에 관계 없이 정책을 테스트할 수 있습니다. 이 웹 사이트는 신뢰 당사자 애플리케이션처럼 작동합니다. Azure AD B2C 정책에 의해 생성 된 JWT (JSON web token)의 콘텐츠를 표시 합니다.

    토큰 검사를 위해 `https://jwt.ms`로 리디렉션할 수 있는 테스트 응용 프로그램을 만들려면 다음을 수행 합니다.

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* 클라이언트 브라우저와 Azure AD B2C 간의 메시지 교환을 추적하려면 [Fiddler](https://www.telerik.com/fiddler)를 사용합니다. 오케스트레이션 단계에서 사용자 환경이 실패한 위치를 확인하는 데 도움이 될 수 있습니다.

* [개발 모드](troubleshoot-with-application-insights.md)에서 **Application Insights**를 사용하여 Identity Experience Framework 사용자 환경의 활동을 추적합니다. **개발 모드**에서는 id 공급자, API 기반 서비스, Azure AD B2C 사용자 디렉터리 및 기타 서비스 (예: Azure Multi-Factor Authentication)와 같은 기술 프로필에 정의 된 다양 한 클레임 공급자와 Id 경험 프레임 워크 간의 클레임 교환을 관찰할 수 있습니다.

## <a name="recommended-practices"></a>권장 사례

**여러 버전의 시나리오를 유지 합니다. 응용 프로그램을 사용 하 여 프로젝트에서 그룹화 합니다.** 기본, 확장 및 신뢰 당사자 파일은 서로 직접 종속됩니다. 그룹으로 저장합니다. 새로운 기능이 정책에 추가되면 별도 작업 버전을 유지합니다. 상호 작용하는 애플리케이션 코드를 사용하여 사용자 고유의 파일 시스템에 작업 버전을 준비합니다. 애플리케이션은 테넌트의 여러 다른 신뢰 당사자 정책을 호출할 수 있습니다. Azure AD B2C 정책에서 예상하는 클레임에 종속될 수도 있습니다.

**알려진 사용자 환경으로 기술 프로필을 개발 및 테스트합니다.** 테스트된 시작 팩 정책을 사용하여 기술 프로파일을 설정합니다. 고유한 사용자 환경에 통합하기 전에 개별적으로 테스트합니다.

**테스트된 기술 프로필을 통해 사용자 환경을 개발 및 테스트합니다.** 사용자 환경의 오케스트레이션 단계를 증분 방식으로 변경합니다. 의도한 시나리오를 점진적으로 작성합니다.

## <a name="next-steps"></a>다음 단계

GitHub에서 사용할 수 있는 [b2c-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) .zip 보관 파일을 다운로드 합니다. 리포지토리를 복제할 수도 있습니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```

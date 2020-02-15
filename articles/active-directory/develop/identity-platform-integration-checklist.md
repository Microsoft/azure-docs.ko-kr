---
title: Microsoft id 플랫폼에 대 한 모범 사례 | Microsoft
description: Microsoft id 플랫폼과 통합 하는 경우 모범 사례, 권장 사항 및 일반적인 oversights에 대해 알아보세요.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: c5005f6438a53215054c6152722d1449aa593b4f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160919"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Microsoft id 플랫폼 모범 사례 및 권장 사항

이 문서에서는 Microsoft id 플랫폼과 통합 하는 경우 모범 사례, 권장 사항 및 일반적인 oversights에 대해 중점적으로 설명 합니다.  이 검사 목록에서는 고품질의 보안 통합을 안내 합니다. 이 목록을 정기적으로 검토 하 여 id 플랫폼과 응용 프로그램의 통합에 대 한 품질 및 보안을 유지 해야 합니다. 검사 목록은 전체 응용 프로그램을 검토 하기 위한 것이 아닙니다. 플랫폼이 향상 되 면 검사 목록의 내용이 변경 될 수 있습니다.

방금 시작한 경우 [microsoft id 플랫폼 설명서](index.yml) 를 확인 하 여 인증 기본 사항, microsoft id 플랫폼의 응용 프로그램 시나리오 등에 대해 알아보세요.

다음 검사 목록을 사용 하 여 응용 프로그램이 [Microsoft id 플랫폼과](https://docs.microsoft.com/azure/active-directory/develop/)효과적으로 통합 되었는지 확인 합니다.

## <a name="basics"></a>기본 사항

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | [Microsoft Platform 정책을](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409)읽고 이해 합니다. 응용 프로그램이 사용자 및 플랫폼을 보호 하도록 설계 된 용어를 준수 하는지 확인 합니다. |

## <a name="ownership"></a>소유권

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱을 등록 하 고 관리 하는 데 사용한 계정과 관련 된 정보가 최신 상태 인지 확인 합니다. |

## <a name="branding"></a>브랜딩

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | [응용 프로그램에 대 한 브랜딩 지침](howto-add-branding-in-azure-ad-apps.md)을 따릅니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램에 대 한 의미 있는 이름 및 로고를 제공 합니다. 이 정보는 [응용 프로그램의 동의 프롬프트](application-consent-experience.md)에 표시 됩니다. 사용자가 합리적인 의사 결정을 내릴 수 있도록 이름 및 로고가 회사/제품을 대표 하는지 확인 합니다. 모든 상표를 위반 하지 않는지 확인 합니다. |

## <a name="privacy"></a>개인 정보 보호

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱의 서비스 약관 및 개인 정보 취급 방침에 대 한 링크를 제공 합니다. |

## <a name="security"></a>보안

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 리디렉션 Uri 관리: <ul><li>모든 리디렉션 Uri의 소유권을 유지 하 고 DNS 레코드를 최신으로 유지 합니다.</li><li>Uri에서 와일드 카드 (*)를 사용 하지 않습니다.</li><li>웹 앱의 경우 모든 Uri가 안전 하 고 암호화 되어 있는지 확인 합니다 (예: https 체계 사용).</li><li>공용 클라이언트의 경우 플랫폼 특정 리디렉션 Uri (주로 iOS 및 Android 용)를 사용 합니다. 그렇지 않으면 앱을 다시 호출할 때 충돌을 방지 하기 위해 많은 양의 임의성으로 리디렉션 Uri를 사용 합니다.</li><li>격리 된 웹 에이전트에서 앱을 사용 하는 경우 https://login.microsoftonline.com/common/oauth2/nativeclient를 사용할 수 있습니다.</li><li>사용 되지 않거나 불필요 한 리디렉션 Uri를 정기적으로 검토 하 고 트리밍합니다.</li></ul> |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램이 디렉터리에 등록 된 경우 앱 등록 소유자 목록을 최소화 하 고 수동으로 모니터링 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 명시적으로 필요한 경우가 아니면 [OAuth2 암시적 권한 부여 흐름](v2-oauth2-implicit-grant-flow.md) 에 대 한 지원을 사용 하도록 설정 하지 마세요. [여기](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)에서 유효한 시나리오에 대해 알아보세요. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 사용자 이름/암호를 초과 하 여 이동 합니다. 사용자 암호를 직접 처리 하는 [ROPC (리소스 소유자 암호 자격 증명 흐름)](v2-oauth-ropc.md)를 사용 하지 마세요. 이 흐름에는 높은 수준의 신뢰 및 사용자 노출이 필요 하며, 더 안전 하 고 다른 흐름을 사용할 수 없는 경우에만 사용 해야 합니다. 이 흐름은 DevOps와 같은 일부 시나리오에서는 여전히 필요하지만, 이 흐름을 사용하면 애플리케이션에 제약이 발생합니다.  최신 접근 방법은 [인증 흐름 및 응용 프로그램 시나리오](authentication-flows-app-scenarios.md)를 참조 하세요.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 웹 앱, 웹 Api 및 디먼 앱에 대 한 비밀 앱 자격 증명을 보호 하 고 관리 합니다. 암호 자격 증명 (클라이언트 암호)이 아닌 [인증서 자격 증명](active-directory-certificate-credentials.md)을 사용 합니다. 암호 자격 증명을 사용 해야 하는 경우 수동으로 설정 하지 마세요. 자격 증명은 코드 또는 구성에 저장 하지 않으며, 이러한 자격 증명은 사람이 처리할 수 없습니다. 가능 하면 Azure 리소스 또는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 자격 증명을 저장 하 고 정기적으로 회전 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 응용 프로그램에서 최소 권한 권한을 요청 하는지 확인 합니다. 응용 프로그램에 필요한 권한만 필요한 경우에만 요청 합니다. 여러 [사용 권한 유형을](v2-permissions-and-consent.md#permission-types)이해 합니다. 필요한 경우 응용 프로그램 권한만 사용 합니다. 가능한 경우 위임 된 사용 권한을 사용 합니다. Microsoft Graph 사용 권한의 전체 목록은이 [권한 참조](https://docs.microsoft.com/graph/permissions-reference)를 참조 하세요. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | Microsoft id 플랫폼을 사용 하 여 API를 보호 하는 경우 노출 해야 하는 사용 권한을 신중 하 게 고려해 야 합니다. 솔루션에 대 한 올바른 세분성과 관리자 동의가 필요한 권한을 고려 합니다. 권한 부여를 결정 하기 전에 들어오는 토큰에 필요한 권한이 있는지 확인 하십시오. |

## <a name="implementation"></a>구현

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 최신 인증 솔루션 (OAuth 2.0, [Openid connect Connect](v2-protocols-oidc.md))을 사용 하 여 안전 하 게 사용자에 게 로그인 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  OAuth 2.0 및 Open ID와 같은 프로토콜에 대해 직접 프로그래밍 하지 않습니다. 대신 [MSAL (Microsoft 인증 라이브러리)](msal-overview.md)을 활용 하세요. MSAL 라이브러리는 사용 하기 쉬운 라이브러리에서 보안 프로토콜을 안전 하 게 래핑하고 [조건부 액세스](/azure/active-directory/conditional-access/overview) 시나리오, 장치 차원의 [SSO (Single Sign-On](/azure/active-directory/manage-apps/what-is-single-sign-on)) 및 기본 제공 토큰 캐싱 지원에 대 한 지원을 기본적으로 제공 합니다. 자세한 내용은 Microsoft 지원 되는 [클라이언트 라이브러리](reference-v2-libraries.md#microsoft-supported-client-libraries) 및 [미들웨어 라이브러리](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) 목록과 [호환 되는 타사 클라이언트 라이브러리](reference-v2-libraries.md#compatible-client-libraries)목록을 참조 하십시오.<br/><br/>인증 프로토콜에 대 한 코드를 직접 실행 해야 하는 경우 [MICROSOFT SDL](https://www.microsoft.com/sdl/default.aspx)과 같은 방법을 따라야 합니다. 각 프로토콜에 대 한 표준 사양에서 보안 고려 사항에 주의 하세요.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  [ADAL (Azure Active Directory Authentication library)](../azuread-dev/active-directory-authentication-libraries.md) 에서 [Microsoft 인증 라이브러리로](msal-overview.md)기존 앱을 마이그레이션합니다. MSAL은 Microsoft의 최신 id 플랫폼 솔루션 이며 ADAL에서 선호 됩니다. 이 버전은 .NET, JavaScript, Android, iOS, macOS에서 사용할 수 있으며 Python 및 Java에 대 한 공개 미리 보기에도 있습니다. [ADAL.NET](msal-net-migration.md), [ADAL](msal-compare-msal-js-and-adal-js.md), [ADAL.NET 및 iOS broker](msal-net-migration-ios-broker.md) 앱 마이그레이션에 대해 자세히 알아보세요.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  모바일 앱의 경우 응용 프로그램 등록 환경을 사용 하 여 각 플랫폼을 구성 합니다. 응용 프로그램에서 single sign-on을 위해 Microsoft Authenticator 또는 Microsoft 회사 포털를 활용 하기 위해 앱에 "broker 리디렉션 URI"가 구성 되어 있어야 합니다. 이를 통해 Microsoft는 인증 후 응용 프로그램에 제어를 반환할 수 있습니다. 각 플랫폼을 구성할 때 앱 등록 환경에서이 과정을 안내 합니다. 빠른 시작을 사용 하 여 작업 예제를 다운로드 합니다. IOS에서 가능 하면 브로커 및 시스템 웹 보기를 사용 합니다.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |  웹 앱 또는 web Api에서 계정 당 하나의 토큰 캐시를 유지 합니다.  웹 앱의 경우 계정 ID로 토큰 캐시를 키로 지정 해야 합니다.  웹 Api의 경우 API를 호출 하는 데 사용 되는 토큰의 해시를 사용 하 여 계정에 키를 지정 해야 합니다. MSAL.NET는 .NET Framework 및 .NET Core subplatforms에서 사용자 지정 토큰 캐시 serialization을 제공 합니다. 보안 및 성능상의 이유로 사용자 당 캐시 하나를 직렬화 하는 것이 좋습니다. 자세한 내용은 [토큰 캐시 serialization](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)정보를 참조 하세요.|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 앱에 필요한 데이터를 [Microsoft Graph](https://developer.microsoft.com/graph)를 통해 사용할 수 있는 경우 개별 API가 아닌 Microsoft Graph 끝점을 사용 하 여이 데이터에 대 한 권한을 요청 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) |액세스 토큰 값을 확인 하거나 클라이언트로 구문 분석 해 보십시오.  값, 형식을 변경 하거나 경고 없이 암호화할 수도 있습니다. 클라이언트가 사용자에 대 한 정보를 확인 하거나 Microsoft Graph를 호출 해야 하는 경우 항상 id_token를 사용 합니다.  웹 Api는 형식을 정의 하 고 암호화 키를 설정 하는 것 이므로 액세스 토큰을 구문 분석 해야 합니다. |

## <a name="end-user-experience"></a>최종 사용자 환경

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 최종 사용자와 관리자가 앱을 신뢰 하는지 확인 하는 데 충분 한 정보를 갖도록 [승인 환경을 이해](application-consent-experience.md) 하 고 앱의 동의 프롬프트를 구성 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 대화형 흐름 전에 자동 인증 (자동 토큰 획득)을 시도 하 여 앱을 사용 하는 동안 사용자가 로그인 자격 증명을 입력 해야 하는 횟수를 최소화 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 모든 로그인에 대해 "prompt = 동의"를 사용 하지 마세요. 추가 사용 권한 (예: 앱의 필수 사용 권한을 변경한 경우)에 대 한 동의를 요청 해야 하는 경우에만 prompt = 동의를 사용 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 해당 하는 경우 사용자 데이터를 사용 하 여 응용 프로그램을 보강 합니다. 이 작업을 수행 하는 쉬운 방법은 [MICROSOFT GRAPH API](https://developer.microsoft.com/graph) 를 사용 하는 것입니다. 시작 하는 데 도움이 되는 [Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer) 도구입니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 관리자가 테 넌 트에 게 쉽게 동의할 수 있도록 앱에 필요한 전체 사용 권한 집합을 등록 합니다. 런타임에 [증분 동의](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) 를 사용 하 여 사용자가 처음 시작 시 요청 될 때 사용자에 게 염려 하거나 혼동 될 수 있는 사용 권한을 요청 하는 이유를 이해할 수 있습니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | [명확한 단일 로그 아웃 환경을](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)구현 합니다. 이는 개인 정보 보호 및 보안 요구 사항 이며 좋은 사용자 환경을 제공 합니다. |

## <a name="testing"></a>테스트

|   |   |
|---|---|
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 사용자의 응용 프로그램 사용 기능에 영향을 줄 수 있는 [조건부 액세스 정책을](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) 테스트 합니다. |
| ![확인란](./media/active-directory-integration-checklist/checkbox-two.svg) | 지원 하려는 모든 가능한 계정 (예: 회사 또는 학교 계정, 개인 Microsoft 계정, 자식 계정 및 소 버린 계정)으로 응용 프로그램을 테스트 합니다. |

## <a name="additional-resources"></a>추가 리소스

v2.0에 관한 자세한 정보를 살펴보세요.

* [Microsoft id 플랫폼 (v2.0 개요)](v2-overview.md)
* [Microsoft id 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)
* [액세스 토큰 참조](access-tokens.md)
* [ID 토큰 참조](id-tokens.md)
* [인증 라이브러리 참조](reference-v2-libraries.md)
* [Microsoft id 플랫폼에서 사용 권한 및 동의](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)

---
title: Azure API Management 템플릿 리소스 | Microsoft Docs
description: Azure API Management에서 개발자 포털 템플릿에 사용할 수 있는 리소스 종류에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 673dcbeb630899eebc328cd4fae16f7fe8f47a55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557888"
---
# <a name="azure-api-management-template-resources"></a>Azure API Management 템플릿 리소스
Azure API Management는 개발자 포털 템플릿에 사용할 수 있는 다음 종류의 리소스를 제공합니다.  
  
-   [문자열 리소스](#strings)  
  
-   [문자 모양 리소스](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> 문자열 리소스  
 API Management는 개발자 포털에서 사용할 포괄적인 문자열 리소스 집합을 제공합니다. 이러한 리소스는 API Management에서 지원하는 모든 언어로 지역화되어 있습니다. 기본 템플릿 집합은 개발자 포털에 표시되는 페이지 머리글, 레이블 및 상수 문자열에 대해 이러한 리소스를 사용합니다. 템플릿에서 문자열 리소스를 사용하려면 다음 예제와 같이 문자열 이름 뒤에 리소스 문자열 접두사를 제공합니다.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 다음 예제는 제품 목록 템플릿의 예제이며, 페이지 위쪽에 **제품**을 표시합니다.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
다음 지역화 옵션이 지원 됩니다.

| 로캘    | 언어               |
|-----------|------------------------|
| "en"      | "영어"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "it"      | "Italiano"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (브라질)"   |
| "pt-pt"   | "Português (포르투갈)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 개발자 포털 템플릿에서 사용할 수 있는 문자열 리소스에 대해서는 다음 표를 참조하세요. 표에서 보여 주는 문자열 리소스에 대한 접두사로 해당 표의 이름을 사용합니다.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [설명서](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|이름|텍스트|  
|----------|----------|  
|PageTitleApis|API|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|이름|텍스트|  
|----------|----------|  
|WebApplicationsDetailsTitle|애플리케이션 미리 보기|  
|WebApplicationsRequirementsHeader|요구 사항|  
|WebApplicationsScreenshotAlt|스크린샷|  
|WebApplicationsScreenshotsHeader|스크린샷|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|이름|텍스트|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|애플리케이션을 제거하시겠습니까?|  
|WebDevelopersAppNotPublished|게시되지 않음|  
|WebDevelopersAppNotSubmitted|제출되지 않음|  
|WebDevelopersAppTableCategoryHeader|Category|  
|WebDevelopersAppTableNameHeader|이름|  
|WebDevelopersAppTableStateHeader|시스템 상태|  
|WebDevelopersEditLink|편집|  
|WebDevelopersRegisterAppLink|애플리케이션 등록|  
|WebDevelopersRemoveLink|제거|  
|WebDevelopersSubmitLink|Submit|  
|WebDevelopersYourApplicationsHeader|애플리케이션|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|이름|텍스트|  
|----------|----------|  
|WebApplicationsHeader|애플리케이션|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|이름|텍스트|  
|----------|----------|  
|NoItemsToDisplay|결과를 찾을 수 없습니다.|  
|GeneralExceptionMessage|오류가 발생했습니다. 일시적인 결함이나 버그일 수 있습니다. 다시 시도하세요.|  
|GeneralJsonExceptionMessage|오류가 발생했습니다. 일시적인 결함이나 버그일 수 있습니다. 페이지를 다시 로드하고 다시 시도하세요.|  
|ConfirmationMessageUnsavedChanges|저장되지 않은 몇 가지 변경 내용이 있습니다. 변경 내용을 취소하고 무시하시겠습니까?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http 요청 본문이 너무 큽니다.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|이름|텍스트|  
|----------|----------|  
|ButtonLabelCancel|취소|  
|ButtonLabelSave|저장|  
|GeneralExceptionMessage|오류가 발생했습니다. 일시적인 결함이나 버그일 수 있습니다. 다시 시도하세요.|  
|NoItemsToDisplay|표시할 항목이 없습니다.|  
|PagerButtonLabelFirst|처음|  
|PagerButtonLabelLast|마지막|  
|PagerButtonLabelNext|다음|  
|PagerButtonLabelPrevious|이전|  
|PagerLabelPageNOfM|{0}/{1}페이지|  
|PasswordTooShort|암호가 너무 짧습니다.|  
|EmailAsPassword|전자 메일을 암호로 사용하지 마세요.|  
|PasswordSameAsUserName|암호에는 사용자 이름을 포함할 수 없습니다.|  
|PasswordTwoCharacterClasses|다른 문자 클래스를 사용하세요.|  
|PasswordTooManyRepetitions|반복이 너무 많습니다.|  
|PasswordSequenceFound|암호에 시퀀스가 포함되어 있습니다.|  
|PagerLabelPageSize|페이지 크기|  
|CurtainLabelLoading|로드 중...|  
|TablePlaceholderNothingToDisplay|선택한 기간과 범위에 대한 데이터가 없습니다.|  
|ButtonLabelClose|닫습니다|  
  
###  <a name="Documentation"></a>문서화  
  
|이름|텍스트|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|잘못된 헤더 ‘{0}’|  
|WebDocumentationInvalidRequestErrorMessage|잘못된 요청 URL|  
|TextboxLabelAccessToken|액세스 토큰 *|  
|DropdownOptionPrimaryKeyFormat|Primary-{0}|  
|DropdownOptionSecondaryKeyFormat|Secondary-{0}|  
|WebDocumentationSubscriptionKeyText|구독 키|  
|WebDocumentationTemplatesAddHeaders|필수 HTTP 헤더를 추가합니다.|  
|WebDocumentationTemplatesBasicAuthSample|기본 권한 부여 샘플|  
|WebDocumentationTemplatesCurlForBasicAuth|기본 권한 부여를 사용할 경우: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|경로 매개 변수 값({...}로 표시), 구독 키 및 쿼리 매개 변수 값을 지정합니다.|  
|WebDocumentationTemplatesDeveloperKey|구독 키를 지정합니다.|  
|WebDocumentationTemplatesJavaApache|이 샘플에서는 HTTP 구성 요소의 Apache HTTP 클라이언트를 사용합니다(http://hc.apache.org/httpcomponents-client-ga/)).|  
|WebDocumentationTemplatesOptionalParams|필요에 따라 선택적 매개 변수 값을 지정합니다.|  
|WebDocumentationTemplatesPhpPackage|이 샘플에서는 HTTP_Request2 패키지를 사용합니다. (추가 정보: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|경로 매개 변수 값({...}로 표시)을 지정하고 필요한 경우 본문을 요청합니다.|  
|WebDocumentationTemplatesRequestBody|요청 본문을 지정합니다.|  
|WebDocumentationTemplatesRequiredParams|다음 필수 매개 변수 값을 지정합니다.|  
|WebDocumentationTemplatesValuesForPath|경로 매개 변수 값({...}로 표시)을 지정합니다.|  
|OAuth2AuthorizationEndpointDescription|권한 부여 끝점은 리소스 소유자와 상호 작용하고 권한 부여를 가져오는 데 사용됩니다.|  
|OAuth2AuthorizationEndpointName|권한 부여 끝점|  
|OAuth2TokenEndpointDescription|토큰 끝점은 클라이언트에서 권한 부여를 표시하거나 토큰을 새로 고쳐 액세스 토큰을 가져오는 데 사용됩니다.|  
|OAuth2TokenEndpointName|토큰 끝점|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p\> 클라이언트에서 리소스 소유자의 user-agent를 권한 끝점으로 전달함으로써 흐름을 시작합니다.  클라이언트는 클라이언트 식별자, 요청된 범위, 로컬 상태 및 액세스가 허용되거나 거부되면 권한 부여 서버에서 user-agent를 다시 보낼 리디렉션 URI를 포함합니다.     </p\>     <p\> 권한 부여 서버는 user-agent를 통해 리소스 소유자를 인증하고 리소스 소유자가 클라이언트의 액세스 요청을 승인 또는 거부하는지 여부를 설정합니다.     </p\>     <p\> 리소스 소유자가 액세스 권한을 부여한다고 가정하면 권한 부여 서버는 이전에 제공된(요청 시 또는 클라이언트 등록 중에) 리디렉션 URI를 사용하여 user-agent를 클라이언트로 다시 리디렉션합니다.  리디렉션 URI에는 인증 코드와 클라이언트에서 이전에 제공한 모든 로컬 상태가 포함됩니다.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p\> 사용자가 유효하지 않은 액세스 요청을 거부하면 리디렉션에 추가된 다음 매개 변수를 사용하여 클라이언트에 알려줍니다.</p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|권한 부여 요청|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p\> OAuth 프로세스를 시작하려면 클라이언트 앱에서 사용자를 권한 부여 끝점으로 보내야 합니다.          권한 부여 끝점에서 사용자는 인증한 후 앱에 대한 액세스 권한을 부여하거나 거부합니다.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p\> 리소스 소유자가 액세스 권한을 부여한다고 가정하면 권한 부여 서버에서 이전에 제공된(요청 시 또는 클라이언트 등록 중에) 리디렉션 URI를 사용하여 user-agent를 클라이언트로 다시 리디렉션합니다.  리디렉션 URI에는 인증 코드와 클라이언트에서 이전에 제공한 모든 로컬 상태가 포함됩니다. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p\> 클라이언트는 이전 단계에서 받은 인증 코드를 포함하여 권한 부여 서버의 토큰 끝점에서 액세스 토큰을 요청합니다.  요청할 때 클라이언트에서 권한 부여 서버를 통해 인증합니다.  클라이언트는 확인을 위해 인증 코드를 가져오는 데 사용되는 리디렉션 URI를 포함합니다. </p\> <p\> 권한 부여 서버는 클라이언트를 인증하고, 인증 코드의 유효성을 검사하며, 받은 리디렉션 URI가 (C) 단계에서 클라이언트를 리디렉션하는 데 사용된 URI와 일치하는지 확인합니다.  유효한 경우 권한 부여 서버에서 액세스 토큰 및 선택적으로 새로 고침 토큰으로 다시 응답합니다. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p\> 요청 클라이언트 인증이 실패하거나 유효하지 않으면 권한 부여 서버에서 HTTP 400(잘못된 요청) 상태 코드로 응답하고(달리 지정하지 않은 경우) 응답에 다음 매개 변수를 포함합니다. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|&lt;p\> 클라이언트에서 UTF-8 문자 인코딩과 함께 HTTP 요청 entity-body에 "application/x-www-form-urlencoded" 형식의 다음 매개 변수를 전달하여 토큰 엔드포인트에 요청합니다. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p\> 권한 부여 서버에서 액세스 토큰과 선택적인 새로 고침 토큰을 발급하고, 200(확인) 상태 코드와 함께 HTTP 응답의 entity-body에 다음 매개 변수를 추가하여 응답을 구성합니다. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p\> 클라이언트에서 권한 부여 서버를 통해 인증하고 토큰 끝점에서 액세스 토큰을 요청합니다. </p\> <p\> 권한 부여 서버는 클라이언트를 인증하고 유효한 경우 액세스 토큰을 발급합니다. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> 요청이 클라이언트 인증에 실패하거나 유효하지 않으면 권한 부여 서버에서 HTTP 400(잘못된 요청) 상태 코드로 응답하고(달리 지정하지 않은 경우) 응답에 다음 매개 변수를 포함합니다. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|&lt;p\> 클라이언트에서 UTF-8 문자 인코딩과 함께 HTTP 요청 entity-body에 "application/x-www-form-urlencoded" 형식의 다음 매개 변수를 추가하여 토큰 엔드포인트에 요청합니다. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> 액세스 토큰 요청이 유효하고 권한이 부여되면 권한 부여 서버에서 액세스 토큰과 선택적인 새로 고침 토큰을 발급하고, 200(확인) 상태 코드와 함께 HTTP 응답의 entity-body에 다음 매개 변수를 추가하여 응답을 구성합니다. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p\> 클라이언트에서 리소스 소유자의 user-agent를 권한 끝점으로 전달함으로써 흐름을 시작합니다.  클라이언트는 클라이언트 식별자, 요청된 범위, 로컬 상태 및 액세스가 허용되거나 거부되면 권한 부여 서버에서 user-agent를 다시 보낼 리디렉션 URI를 포함합니다. </p\> <p\> 권한 부여 서버는 user-agent를 통해 리소스 소유자를 인증하고 리소스 소유자가 클라이언트의 액세스 요청을 승인 또는 거부하는지 여부를 설정합니다. </p\> <p\> 리소스 소유자가 액세스 권한을 부여한다고 가정하면 권한 부여 서버는 이전에 제공된 리디렉션 URI를 사용하여 user-agent를 클라이언트로 다시 리디렉션합니다.  리디렉션 URI에는 URI 조각에 있는 액세스 토큰이 포함됩니다. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p\> 리소스 소유자가 액세스 요청을 거부하는 경우 또는 누락되거나 유효하지 않은 리디렉션 URI 이외의 다른 이유로 요청이 실패하는 경우 권한 부여 서버는 "application/x-www-form-urlencoded" 형식을 사용하는 리디렉션 URI의 조각 구성 요소에 다음 매개 변수를 추가하여 클라이언트에 알려줍니다. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p\> OAuth 프로세스를 시작하려면 클라이언트 앱에서 사용자를 권한 부여 끝점으로 보내야 합니다.      권한 부여 끝점에서 사용자는 인증한 후 앱에 대한 액세스 권한을 부여하거나 거부합니다. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p\> 리소스 소유자가 액세스 요청을 승인하는 경우 권한 부여 서버는 "application/x-www-form-urlencoded" 형식을 사용하는 리디렉션 URI의 조각 구성 요소에 다음 매개 변수를 추가하여 액세스 토큰을 발급하고 클라이언트에 전달합니다. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|인증 코드 흐름은 자격 증명의 기밀성을 유지할 수 있는 클라이언트(예: PHP, Java, Python, Ruby, ASP.NET 등을 사용하여 구현된 웹 서버 애플리케이션)에 최적화되어 있습니다.|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|인증 코드 부여|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|클라이언트 자격 증명 흐름은 클라이언트(애플리케이션)에서 제어 하에 보호되는 리소스에 대한 액세스를 요청하는 경우에 적합합니다. 클라이언트는 리소스 소유자로 간주되므로 최종 사용자가 개입할 필요가 없습니다.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|클라이언트 자격 증명 부여|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|암시적 흐름은 특정 리디렉션 URI를 작동하는 것으로 알려진 자격 증명의 기밀성을 유지할 수 없는 클라이언트에 최적화되어 있습니다. 이러한 클라이언트는 일반적으로 JavaScript와 같은 스크립팅 언어를 사용하는 브라우저에서 구현됩니다.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|암시적 부여|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|리소스 소유자가 장치 운영 체제 또는 높은 권한이 필요한 애플리케이션과 같은 클라이언트(애플리케이션)와 트러스트 관계가 있는 경우에는 리소스 소유자 암호 자격 증명 흐름이 적합합니다. 이 흐름은 리소스 소유자의 자격 증명(일반적으로 대화 형 양식을 사용하는 사용자 이름 및 암호)을 가져올 수 있는 클라이언트에 적합합니다.|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|리소스 소유자 암호 자격 증명 부여|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p\> 리소스 소유자가 클라이언트에 사용자 이름과 암호를 제공합니다. </p\> <p\> 클라이언트는 리소스 소유자로부터 받은 자격 증명을 포함하여 권한 부여 서버의 토큰 끝점에서 액세스 토큰을 요청합니다.  요청할 때 클라이언트에서 권한 부여 서버를 통해 인증합니다. </p\> <p\> 권한 부여 서버는 클라이언트를 인증하고, 리소스 소유자 자격 증명의 유효성을 검증하며, 유효한 경우 액세스 토큰을 발급합니다. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p\> 요청이 클라이언트 인증에 실패하거나 유효하지 않으면 권한 부여 서버에서 HTTP 400(잘못된 요청) 상태 코드로 응답하고(달리 지정하지 않은 경우) 응답에 다음 매개 변수를 포함합니다. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|&lt;p\> 클라이언트에서 UTF-8 문자 인코딩과 함께 HTTP 요청 entity-body에 "application/x-www-form-urlencoded" 형식의 다음 매개 변수를 추가하여 토큰 엔드포인트에 요청합니다. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p\> 액세스 토큰 요청이 유효하고 권한이 부여되면 권한 부여 서버에서 액세스 토큰과 선택적인 새로 고침 토큰을 발급하고, 200(확인) 상태 코드와 함께 HTTP 응답의 entity-body에 다음 매개 변수를 추가하여 응답을 구성합니다. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|액세스 토큰 요청|  
|OAuth2Step_AuthorizationRequest_Name|권한 부여 요청|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|필수. 권한 부여 서버에서 발급한 액세스 토큰입니다.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|필수. 권한 부여 서버에서 발급한 액세스 토큰입니다.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|필수. 권한 부여 서버에서 발급한 액세스 토큰입니다.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|필수. 권한 부여 서버에서 발급한 액세스 토큰입니다.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|필수. 클라이언트 식별자입니다.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|클라이언트에서 권한 부여 서버를 통해 인증하지 않을 경우 필수입니다.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|필수. 클라이언트 식별자입니다.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|필수. 권한 부여 서버에서 생성하는 인증 코드입니다.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|필수. 권한 부여 서버에서 받은 인증 코드입니다.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|옵션. 사람이 읽을 수 있는 추가 정보를 제공하는 ASCII 텍스트입니다.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|옵션. 사람이 읽을 수 있는 추가 정보를 제공하는 ASCII 텍스트입니다.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|옵션. 사람이 읽을 수 있는 추가 정보를 제공하는 ASCII 텍스트입니다.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|옵션. 사람이 읽을 수 있는 추가 정보를 제공하는 ASCII 텍스트입니다.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|옵션. 사람이 읽을 수 있는 추가 정보를 제공하는 ASCII 텍스트입니다.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|옵션. 사람이 읽을 수 있는 오류 관련 정보를 포함한 웹 페이지를 식별하는 URI입니다.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|옵션. 사람이 읽을 수 있는 오류 관련 정보를 포함한 웹 페이지를 식별하는 URI입니다.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|옵션. 사람이 읽을 수 있는 오류 관련 정보를 포함한 웹 페이지를 식별하는 URI입니다.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|옵션. 사람이 읽을 수 있는 오류 관련 정보를 포함한 웹 페이지를 식별하는 URI입니다.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|옵션. 사람이 읽을 수 있는 오류 관련 정보를 포함한 웹 페이지를 식별하는 URI입니다.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|필수. invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable의 단일 ASCII 오류 코드입니다.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|필수. invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope의 단일 ASCII 오류 코드입니다.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|필수. invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope의 단일 ASCII 오류 코드입니다.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|필수. invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable의 단일 ASCII 오류 코드입니다.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|필수. invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope의 단일 ASCII 오류 코드입니다.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|권장. 액세스 토큰의 수명(초)입니다.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|권장. 액세스 토큰의 수명(초)입니다.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|권장. 액세스 토큰의 수명(초)입니다.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|권장. 액세스 토큰의 수명(초)입니다.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|필수. 값은 반드시 "authorization_code"로 설정해야 합니다.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|필수. 값은 반드시 "client_credentials"로 설정해야 합니다.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|필수. 값은 반드시 "password"로 설정해야 합니다.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|필수. 리소스 소유자 암호입니다.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|옵션. 리디렉션 끝점 URI는 절대 URI여야 합니다.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|"redirect_uri" 매개 변수가 권한 부여 요청에 포함되어 있고 그 값이 동일해야 하는 경우 필수입니다.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|옵션. 리디렉션 끝점 URI는 절대 URI여야 합니다.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|옵션. 새 액세스 토큰을 가져오는 데 사용할 수 있는 새로 고침 토큰입니다.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|옵션. 새 액세스 토큰을 가져오는 데 사용할 수 있는 새로 고침 토큰입니다.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|옵션. 새 액세스 토큰을 가져오는 데 사용할 수 있는 새로 고침 토큰입니다.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|필수. 값은 반드시 "code"로 설정해야 합니다.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|필수. 값은 반드시 "token"으로 설정해야 합니다.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|옵션. 액세스 요청의 범위입니다.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|클라이언트에서 요청하는 범위와 동일한 경우 옵션이지만, 그렇지 않으면 필수입니다.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|옵션. 액세스 요청의 범위입니다.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|클라이언트에서 요청하는 범위와 동일한 경우 옵션이지만, 그렇지 않으면 필수입니다.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|옵션. 액세스 요청의 범위입니다.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|클라이언트에서 요청하는 범위와 동일한 경우 옵션이지만, 그렇지 않으면 필수입니다.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|옵션. 액세스 요청의 범위입니다.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|클라이언트에서 요청하는 범위와 동일한 경우 옵션이지만, 그렇지 않으면 필수입니다.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|"state" 매개 변수가 클라이언트 권한 부여 요청에 있는 경우 필수입니다.  클라이언트에서 받은 정확한 값입니다.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|권장. 요청과 콜백 간에 상태를 유지하기 위해 클라이언트에서 사용하는 불투명 값입니다.  user-agent를 클라이언트로 다시 리디렉션할 때 권한 부여 서버에서 이 값을 포함 합니다.  사이트 간 요청 위조 방지에 대한 매개 변수를 반드시 사용해야 합니다.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|"state" 매개 변수가 클라이언트 권한 부여 요청에 있는 경우 필수입니다.  클라이언트에서 받은 정확한 값입니다.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|"state" 매개 변수가 클라이언트 권한 부여 요청에 있는 경우 필수입니다.  클라이언트에서 받은 정확한 값입니다.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|권장. 요청과 콜백 간에 상태를 유지하기 위해 클라이언트에서 사용하는 불투명 값입니다.  user-agent를 클라이언트로 다시 리디렉션할 때 권한 부여 서버에서 이 값을 포함 합니다.  사이트 간 요청 위조 방지에 대한 매개 변수를 반드시 사용해야 합니다.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|"state" 매개 변수가 클라이언트 권한 부여 요청에 있는 경우 필수입니다.  클라이언트에서 받은 정확한 값입니다.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|필수. 발급되는 토큰의 형식입니다.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|필수. 발급되는 토큰의 형식입니다.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|필수. 발급되는 토큰의 형식입니다.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|필수. 발급되는 토큰의 형식입니다.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|필수. 리소스 소유자 사용자 이름입니다.|  
|OAuth2UnsupportedTokenType|'{0}' 토큰 형식은 지원되지 않습니다.|  
|OAuth2InvalidState|잘못된 권한 부여 서버 응답|  
|OAuth2GrantType_AuthorizationCode|인증 코드|  
|OAuth2GrantType_Implicit|암시적|  
|OAuth2GrantType_ClientCredentials|클라이언트 자격 증명|  
|OAuth2GrantType_ResourceOwnerPassword|리소스 소유자 암호|  
|WebDocumentation302Code|302 있음|  
|WebDocumentation400Code|400(잘못된 요청)|  
|OAuth2SendingMethod_AuthHeader|권한 부여 헤더|  
|OAuth2SendingMethod_QueryParam|쿼리 매개 변수|  
|OAuth2AuthorizationServerGeneralException|{0}을(를) 통해 액세스 권한을 부여하는 동안 오류가 발생했습니다.|  
|OAuth2AuthorizationServerCommunicationException|권한 부여 서버에 대한 HTTP 연결을 설정할 수 없거나 예기치 않게 종료되었습니다.|  
|WebDocumentationOAuth2GeneralErrorMessage|예기치 않은 오류가 발생했습니다.|  
|AuthorizationServerCommunicationException|권한 부여 서버 통신 예외가 발생했습니다. 관리자에게 문의하세요.|  
|TextblockSubscriptionKeyHeaderDescription|이 API에 대한 액세스를 제공하는 구독 키입니다. <a href='/developer'\>Profile</a\>에 있습니다.|  
|TextblockOAuthHeaderDescription|<i\>{0}</i\>에서 가져온 OAuth 2.0 액세스 토큰입니다. 지원되는 권한 부여 유형: <i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|API로 전송되는 본문의 미디어 유형입니다.|  
|ErrorMessageApiNotAccessible|현재 호출하려는 API에 액세스할 수 없습니다. API 게시자(<a href="/issues"\>여기</a\>)에게 문의하세요.|  
|ErrorMessageApiTimedout|호출하려는 API에서 응답을 다시 가져오는 데 시간이 평소보다 더 오래 걸립니다. API 게시자(<a href="/issues"\>여기</a\>)에게 문의하세요.|  
|BadRequestParameterExpected|“‘{0}’매개 변수가 필요합니다.”|  
|TooltipTextDoubleClickToSelectAll|두 번 클릭하여 모두 선택합니다.|  
|TooltipTextHideRevealSecret|표시/숨기기|  
|ButtonLinkOpenConsole|시도|  
|SectionHeadingRequestBody|요청 본문|  
|SectionHeadingRequestParameters|요청 매개 변수|  
|SectionHeadingRequestUrl|요청 URL|  
|SectionHeadingResponse|응답|  
|SectionHeadingRequestHeaders|헤더 요청|  
|FormLabelSubtextOptional|선택 사항|  
|SectionHeadingCodeSamples|코드 샘플|  
|TextblockOpenidConnectHeaderDescription|<i\>{0}</i\>에서 가져온 OpenID 연결 ID 토큰입니다. 지원되는 권한 부여 유형: <i\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|이름|텍스트|  
|----------|----------|  
|LinkLabelBack|뒤로|  
|LinkLabelHomePage|홈페이지|  
|LinkLabelSendUsEmail|전자 메일 보내기|  
|PageTitleError|죄송합니다. 요청한 페이지를 처리하는 중에 문제가 발생했습니다.|  
|TextblockPotentialCauseIntermittentIssue|이미 사라진 일시적인 데이터 액세스 문제일 수 있습니다.|  
|TextblockPotentialCauseOldLink|클릭한 링크가 오래되어 더 이상 올바른 위치를 가리킬 수 없습니다.|  
|TextblockPotentialCauseTechnicalProblem|이 쪽에 기술적 문제가 있을 수 있습니다.|  
|TextblockPotentialSolutionRefresh|페이지를 새로 고쳐 보세요.|  
|TextblockPotentialSolutionStartOver|{0}에서 다시 시작합니다.|  
|TextblockPotentialSolutionTryAgain|{0}(으)로 이동하여 수행한 작업을 다시 시도합니다.|  
|TextReportProblem|{0}에서 무엇이 잘못되었는지 설명하고 최대한 빨리 살펴보겠습니다.|  
|TitlePotentialCause|가능한 원인|  
|TitlePotentialSolution|단지 일시적인 문제일 수도 있고 몇 가지 작업을 시도해 볼 수도 있습니다.|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|이름|텍스트|  
|----------|----------|  
|WebIssuesIndexTitle|문제|  
|WebIssuesNoActiveSubscriptions|활성 구독이 없습니다. 문제를 보고하려면 제품을 구독해야 합니다.|  
|WebIssuesNotSignin|로그인하지 않습니다. {0}하여 문제를 보고하거나 의견을 게시해 주세요.|  
|WebIssuesReportIssueButton|보고서 문제|  
|WebIssuesSignIn|로그인|  
|WebIssuesStatusReportedBy|상태: {0} &#124; {1}이(가) 보고함|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|이름|텍스트|  
|----------|----------|  
|LinkLabelHomePage|홈페이지|  
|LinkLabelSendUsEmail|전자 메일 보내기|  
|PageTitleNotFound|죄송합니다. 찾으시는 페이지가 없습니다.|  
|TextblockPotentialCauseMisspelledUrl|입력한 URL의 철자가 틀렸을 수 있습니다.|  
|TextblockPotentialCauseOldLink|클릭한 링크가 오래되어 더 이상 올바른 위치를 가리킬 수 없습니다.|  
|TextblockPotentialSolutionRetype|URL을 다시 입력해 보세요.|  
|TextblockPotentialSolutionStartOver|{0}에서 다시 시작합니다.|  
|TextReportProblem|{0}에서 무엇이 잘못되었는지 설명하고 최대한 빨리 살펴보겠습니다.|  
|TitlePotentialCause|가능한 원인|  
|TitlePotentialSolution|잠재적 솔루션|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|이름|텍스트|  
|----------|----------|  
|WebProductsAgreement|{0} 제품을 구독하면 `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`에 동의하는 것입니다.|  
|WebProductsLegalTermsLink|사용 약관|  
|WebProductsSubscribeButton|구독|  
|WebProductsUsageLimitsHeader|사용 제한|  
|WebProductsYouAreNotSubscribed|이 제품을 구독하고 있습니다.|  
|WebProductsYouRequestedSubscription|이 제품에 대한 구독을 요청했습니다.|  
|ErrorYouNeedToAgreeWithLegalTerms|계속하려면 먼저 사용 약관에 동의해야 합니다.|  
|ButtonLabelAddSubscription|구독 추가|  
|LinkLabelChangeSubscriptionName|변경|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|이 제품에 {0}개 구독이 있습니다.|  
|TextblockSingleSubscriptionsCount|이 제품에 {0}개 구독이 있습니다.|  
|TextblockSingleApisCount|이 제품에는 {0}개 API가 포함되어 있습니다.|  
|TextblockMultipleApisCount|이 제품에는 {0}개 API가 포함되어 있습니다.|  
|TextblockHeaderSubscribe|제품을 구독합니다.|  
|TextblockSubscriptionDescription|새 구독은 다음과 같이 만들어집니다.|  
|TextblockSubscriptionLimitReached|구독 제한에 도달했습니다.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|이름|텍스트|  
|----------|----------|  
|PageTitleProducts|제품|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|이름|텍스트|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|로그인은 현재 관리자에 의해 사용할 수 없도록 설정되어 있습니다.|  
|TextboxExternalIdentitiesSigninInvitation|또는 다음 계정으로 로그인합니다.|  
|TextboxExternalIdentitiesSigninInvitationPrimary|로그인에 사용할 계정:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|이름|텍스트|  
|----------|----------|  
|PrincipalNotFound|주체가 없거나 서명이 유효하지 않습니다.|  
|ErrorSsoAuthenticationFailed|SSO 인증에 실패했습니다.|  
|ErrorSsoAuthenticationFailedDetailed|잘못된 토큰이 제공되었거나 서명을 확인할 수 없습니다.|  
|ErrorSsoTokenInvalid|SSO 토큰이 잘못되었습니다.|  
|ValidationErrorSpecificEmailAlreadyExists|메일 ‘{0}’이(가) 이미 등록되었습니다.|  
|ValidationErrorSpecificEmailInvalid|메일 ‘{0}’이(가) 잘못되었습니다.|  
|ValidationErrorPasswordInvalid|암호가 잘못되었습니다. 오류를 수정하고 다시 시도해 보세요.|  
|PropertyTooShort|{0}이(가) 너무 짧습니다.|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|잘못된 전자 메일 주소입니다.|  
|ValidationMessageNewPasswordConfirmationRequired|새 암호 확인|  
|ValidationErrorPasswordConfirmationRequired|암호 확인이 비어 있습니다.|  
|WebAuthenticationEmailChangeNotice|변경 확인 메일이 {0}(으)로 가는 중입니다. 그 안에 있는 지침에 따라 새 전자 메일 주소를 확인하세요. 전자 메일이 몇 분 내에 받은 편지함에 도착하지 않으면 정크 전자 메일 폴더를 확인하세요.|  
|WebAuthenticationEmailChangeNoticeHeader|전자 메일 변경 요청이 성공적으로 처리되었습니다.|  
|WebAuthenticationEmailChangeNoticeTitle|전자 메일 변경 요청됨|  
|WebAuthenticationEmailHasBeenRevertedNotice|전자 메일이 이미 있습니다. 요청이 되돌려졌습니다.|  
|ValidationErrorEmailAlreadyExists|이미 있는 전자 메일|  
|ValidationErrorEmailInvalid|잘못된 전자 메일 주소|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|전자 메일은 필수입니다.|  
|WebAuthenticationErrorNoticeHeader|오류|  
|WebAuthenticationFieldLengthErrorMessage|{0}은(는) {1}의 최대 길이여야 합니다.|  
|TextboxLabelEmailFirstName|이름|  
|ValidationErrorFirstNameRequired|이름은 필수입니다.|  
|ValidationErrorFirstNameInvalid|잘못된 이름|  
|NoticeInvalidInvitationToken|확인 링크는 48시간 동안만 유효합니다. 아직 이 기간 내에 있으면 링크가 올바른지 확인하세요. 링크가 만료되었으면 확인하려는 작업을 반복하세요.|  
|NoticeHeaderInvalidInvitationToken|잘못된 초대 토큰|  
|NoticeTitleInvalidInvitationToken|확인 오류|  
|WebAuthenticationLastNameInvalidErrorMessage|잘못된 성|  
|TextboxLabelEmailLastName|성|  
|ValidationErrorLastNameRequired|성은 필수입니다.|  
|WebAuthenticationLinkExpiredNotice|보낸 확인 링크가 만료되었습니다. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|암호 재설정 링크가 잘못되었거나 만료되었습니다.|  
|WebAuthenticationLinkExpiredNoticeTitle|링크 보냄|  
|WebAuthenticationNewPasswordLabel|새 암호|  
|ValidationMessageNewPasswordRequired|새 암호가 필요합니다.|  
|TextboxLabelNotificationsSenderEmail|알림 보낸 사람 전자 메일|  
|TextboxLabelOrganizationName|조직 이름|  
|WebAuthenticationOrganizationRequiredErrorMessage|조직 이름이 비어 있습니다.|  
|WebAuthenticationPasswordChangedNotice|암호가 성공적으로 업데이트되었습니다.|  
|WebAuthenticationPasswordChangedNoticeTitle|암호 업데이트됨|  
|WebAuthenticationPasswordCompareErrorMessage|암호가 일치하지 않습니다.|  
|WebAuthenticationPasswordConfirmLabel|암호 확인|  
|ValidationErrorPasswordInvalidDetailed|암호가 너무 약합니다.|  
|WebAuthenticationPasswordLabel|암호|  
|ValidationErrorPasswordRequired|암호는 필수입니다.|  
|WebAuthenticationPasswordResetSendNotice|암호 변경 확인 메일이 {0}(으)로 가는 중입니다. 전자 메일의 지침에 따라 암호 변경 프로세스를 계속하세요.|  
|WebAuthenticationPasswordResetSendNoticeHeader|암호 재설정 요청이 성공적으로 처리되었습니다.|  
|WebAuthenticationPasswordResetSendNoticeTitle|암호 재설정 요청됨|  
|WebAuthenticationRequestNotFoundNotice|요청을 찾을 수 없음|  
|WebAuthenticationSenderEmailRequiredErrorMessage|알림 보낸 사람 전자 메일이 비어 있습니다.|  
|WebAuthenticationSigninPasswordLabel|암호를 입력하여 변경 내용을 확인하세요.|  
|WebAuthenticationSignupConfirmNotice|등록 확인 메일이 {0}(으)로 가는 중입니다.<br /\> 메일에 있는 지침에 따라 계정을 활성화하세요.<br /\> 메일이 몇 분 내에 받은 편지함에 도착하지 않으면 정크 메일 폴더를 확인하세요.|  
|WebAuthenticationSignupConfirmNoticeHeader|계정이 성공적으로 만들어졌습니다.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|등록 확인 전자 메일을 다시 보냈습니다.|  
|WebAuthenticationSignupConfirmNoticeTitle|만든 계정|  
|WebAuthenticationTokenRequiredErrorMessage|토큰이 비어 있습니다.|  
|WebAuthenticationUserAlreadyRegisteredNotice|이 전자 메일을 가진 사용자가 이미 시스템에 등록되어 있는 것 같습니다. 암호를 잊어버렸으면 해당 암호를 복원하거나 고객 지원 팀에 문의해 주세요.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|이미 등록된 사용자|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|이미 등록됨|  
|ButtonLabelChangePassword|암호 변경|  
|ButtonLabelChangeAccountInfo|계정 정보 변경|  
|ButtonLabelCloseAccount|계정 사용 중지|  
|WebAuthenticationInvalidCaptchaErrorMessage|입력한 텍스트가 그림의 텍스트와 일치하지 않습니다. 나중에 다시 시도하세요.|  
|ValidationErrorCredentialsInvalid|전자 메일 또는 암호가 잘못되었습니다. 오류를 수정하고 다시 시도해 보세요.|  
|WebAuthenticationRequestIsNotValid|요청이 잘못되었습니다.|  
|WebAuthenticationUserIsNotConfirm|로그인을 시도하기 전에 등록을 확인하세요.|  
|WebAuthenticationInvalidEmailFormated|잘못된 메일: {0}|  
|WebAuthenticationUserNotFound|사용자를 찾을 수 없음|  
|WebAuthenticationTenantNotRegistered|계정에 있는이 포털에 액세스할 권한이 없는 Azure Active Directory 테 넌 트에 속합니다.|  
|WebAuthenticationAuthenticationFailed|인증에 실패했습니다.|  
|WebAuthenticationGooglePlusNotEnabled|인증에 실패했습니다. 애플리케이션에 권한을 부여한 경우 관리자에게 문의하여 Google 인증이 올바르게 구성되었는지 확인하세요.|  
|ValidationErrorAllowedTenantIsRequired|허용된 테넌트는 필수입니다.|  
|ValidationErrorTenantIsNotValid|‘{0}’ Azure Active Directory 테넌트가 유효하지 않습니다.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|{0} 계정을 사용하여 로그인합니다.|  
|WebAuthenticationUserLimitNotice|이 서비스는 허용되는 최대 사용자 수에 도달했습니다. `<a href="mailto:{0}"\>contact the administrator</a\>`하여 서비스를 업그레이드하고 사용자 등록을 사용하도록 다시 설정하세요.|  
|WebAuthenticationUserLimitNoticeHeader|사용자 등록 사용 해제됨|  
|WebAuthenticationUserLimitNoticeTitle|사용자 등록 사용 해제됨|  
|WebAuthenticationUserRegistrationDisabledNotice|사용자 등록은 관리자에 의해 사용하지 않도록 설정되어 있습니다. 외부 ID 공급자를 통해 로그인합니다.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|사용자 등록 사용 해제됨|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|사용자 등록 사용 해제됨|  
|WebAuthenticationSignupPendingConfirmationNotice|먼저 전자 메일 주소를 확인해야 계정 만들기를 완료할 수 있습니다. {0}(으)로 메일을 보냈습니다. 전자 메일의 지침에 따라 계정을 활성화합니다. 전자 메일이 몇 분 내에 도착하지 않으면 정크 전자 메일 폴더를 확인하세요.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|{0} 메일 주소에 확인되지 않는 계정이 있습니다. 계정 만들기를 완료하려면 전자 메일 주소를 확인해야 합니다. {0}(으)로 메일을 보냈습니다. 전자 메일의 지침에 따라 계정을 활성화합니다. 전자 메일이 몇 분 내에 도착하지 않으면 정크 전자 메일 폴더를 확인하세요.|  
|WebAuthenticationSignupConfirmationAlmostDone|거의 완료됨|  
|WebAuthenticationSignupConfirmationEmailSent|{0}(으)로 메일을 보냈습니다. 전자 메일의 지침에 따라 계정을 활성화합니다. 전자 메일이 몇 분 내에 도착하지 않으면 정크 전자 메일 폴더를 확인하세요.|  
|WebAuthenticationEmailSentNotificationMessage|{0}(으)로 메일을 성공적으로 보냈습니다.|  
|WebAuthenticationNoAadTenantConfigured|서비스에 구성된 Azure Active Directory 테넌트가 없습니다.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`에 동의합니다.|  
|TextblockUserRegistrationTermsProvided|`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`을(를) 검토하세요.|  
|DialogHeadingTermsOfUse|사용 약관|  
|ValidationMessageConsentNotAccepted|계속하려면 먼저 사용 약관에 동의해야 합니다.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|이름|텍스트|  
|----------|----------|  
|WebAuthenticationForgotPassword|암호를 잊으셨습니까?|  
|WebAuthenticationIfAdministrator|관리자이면 `<a href="{0}"\>here</a\>`에 로그인해야 합니다.|  
|WebAuthenticationNotAMember|아직 회원이 아니십니까? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|이 컴퓨터에 사용자 이름 및 암호 저장|  
|WebAuthenticationSigininWithPassword|사용자 이름과 암호로 로그인합니다.|  
|WebAuthenticationSigninTitle|로그인|  
|WebAuthenticationSignUpNow|지금 등록하십시오.|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|이름|텍스트|  
|----------|----------|  
|PageTitleSignup|등록|  
|WebAuthenticationAlreadyAMember|이미 회원이십니까?|  
|WebAuthenticationCreateNewAccount|새 API Management 계정 만들기|  
|WebAuthenticationSigninNow|지금 로그인|  
|ButtonLabelSignup|등록|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|이름|텍스트|  
|----------|----------|  
|SubscriptionCancelConfirmation|이 구독을 취소하시겠습니까?|  
|SubscriptionRenewConfirmation|이 구독을 갱신하시겠습니까?|  
|WebDevelopersManageSubscriptions|구독 관리|  
|WebDevelopersPrimaryKey|기본 키|  
|WebDevelopersRegenerateLink|다시 생성|  
|WebDevelopersSecondaryKey|보조 키|  
|ButtonLabelShowKey|표시|  
|ButtonLabelRenewSubscription|갱신|  
|WebDevelopersSubscriptionRequested|{0}에 요청됨|  
|WebDevelopersSubscriptionRequestedState|요청됨|  
|WebDevelopersSubscriptionTableNameHeader|이름|  
|WebDevelopersSubscriptionTableStateHeader|시스템 상태|  
|WebDevelopersUsageStatisticsLink|분석 보고서|  
|WebDevelopersYourSubscriptions|구독|  
|SubscriptionPropertyLabelRequestedDate|요청 날짜|  
|SubscriptionPropertyLabelStartedDate|시작 날짜|  
|PageTitleRenameSubscription|구독 이름 바꾸기|  
|SubscriptionPropertyLabelName|구독 이름|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|이름|텍스트|  
|----------|----------|  
|SectionHeadingCloseAccount|계정을 사용 중지하시겠습니까?|  
|PageTitleDeveloperProfile|프로필|  
|ButtonLabelHideKey|숨기기|  
|ButtonLabelRegenerateKey|다시 생성|  
|InformationMessageKeyWasRegenerated|이 키를 다시 생성하시겠습니까?|  
|ButtonLabelShowKey|표시|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|이름|텍스트|  
|----------|----------|  
|ButtonLabelUpdateProfile|프로필 업데이트|  
|PageTitleUpdateProfile|계정 정보 업데이트|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|이름|텍스트|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|계정 정보 변경|  
|ButtonLabelChangePassword|암호 변경|  
|ButtonLabelCloseAccount|계정 사용 중지|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|이름|  
|TextboxLabelEmailLastName|성|  
|TextboxLabelNotificationsSenderEmail|알림 보낸 사람 전자 메일|  
|TextboxLabelOrganizationName|조직 이름|  
|SubscriptionStateActive|Active|  
|SubscriptionStateCancelled|Cancelled|  
|SubscriptionStateExpired|만료됨|  
|SubscriptionStateRejected|거부됨|  
|SubscriptionStateRequested|요청됨|  
|SubscriptionStateSuspended|일시 중단|  
|DefaultSubscriptionNameTemplate|{0}(기본값)|  
|SubscriptionNameTemplate|개발자 액세스 #{0}|  
|TextboxLabelSubscriptionName|구독 이름|  
|ValidationMessageSubscriptionNameRequired|구독 이름은 비워 둘 수 없습니다.|  
|ApiManagementUserLimitReached|이 서비스는 허용되는 최대 사용자 수에 도달했습니다. 더 높은 가격 책정 계층으로 업그레이드하세요.|  
  
##  <a name="glyphs"></a> 문자 모양 리소스  
 API Management 개발자 포털 템플릿에서는 [부트스트랩의 문자 모양 아이콘](https://getbootstrap.com/components/#glyphicons)(영문)의 문자 표시를 사용할 수 있습니다. 이 문자 표시 집합에는 [Glyphicon](https://glyphicons.com/) Halflings 집합의 글꼴 서식으로 250개 이상의 문자 표시가 포함되어 있습니다. 이 집합의 문자 표시를 사용하려면 다음 구문을 사용합니다.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 문자 표시의 전체 목록은 [부트스트랩의 문자 모양 아이콘](https://getbootstrap.com/components/#glyphicons)(영문)을 참조하세요.

## <a name="next-steps"></a>다음 단계
템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 참조하세요.

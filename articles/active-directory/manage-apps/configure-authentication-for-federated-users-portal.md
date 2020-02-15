---
title: 홈 영역 검색을 사용 하 여 로그인 자동 가속 구성
description: 자동 가속 및 도메인 힌트를 포함 하 여 페더레이션 사용자에 대 한 Azure Active Directory 인증에 대 한 홈 영역 검색 정책을 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1300ecff416962bda4da800c5eff134951658846
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159168"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>홈 영역 검색 정책을 사용하여 애플리케이션에 대한 Azure Active Directory 로그인 동작 구성

이 문서에서는 페더레이션된 사용자에 대 한 Azure Active Directory 인증 동작을 구성 하는 방법을 소개 합니다. 여기서는 페더레이션된 도메인의 사용자에 대한 자동 가속 및 인증 제한을 구성합니다.

## <a name="home-realm-discovery"></a>홈 영역 검색
HRD(홈 영역 검색)는 Azure AD(Azure Active Directory)에서 로그인 시 사용자가 인증해야 하는 위치를 결정할 수 있게 하는 프로세스입니다.  사용자가 Azure AD 테넌트에 로그인하여 리소스 또는 Azure AD 일반 로그인 페이지에 액세스할 때 사용자는 UPN(사용자 이름)을 입력합니다. Azure AD는 이 기능을 사용하여 사용자가 로그인해야 하는 위치를 검색합니다. 

사용자는 다음 위치 중 하나에서 인증해야 할 수도 있습니다.

- 사용자의 홈 테넌트(사용자가 액세스하려고 하는 리소스와 같은 테넌트일 수 있음) 

- Microsoft 계정  사용자는 리소스 테넌트의 게스트입니다.

-  AD FS(Active Directory Federation Services)와 같은 온-프레미스 ID 공급자

- Azure AD 테넌트와 페더레이션된 다른 ID 공급자

## <a name="auto-acceleration"></a>자동 가속 
일부 조직에서는 사용자 인증을 위해 AD FS와 같은 다른 IdP와 페더레이션되도록 Azure Active Directory 테넌트의 도메인을 구성합니다.  

사용자가 애플리케이션에 로그인하면 먼저 Azure AD 로그인 페이지가 표시됩니다. UPN을 입력한 후 페더레이션된 도메인에 있으면 해당 도메인의 서비스를 제공하는 IdP의 로그인 페이지로 이동합니다. 특정 상황에서 관리자는 사용자들이 특정 애플리케이션에 로그인할 때 로그인 페이지로 안내하고자 할 수도 있습니다. 

이에 따라 사용자는 초기 Azure Active Directory 페이지를 건너뛸 수 있습니다. 이 프로세스를 “로그인 자동 가속”이라고 합니다.

로그인에 대한 다른 IdP로 테넌트가 페더레이션되는 경우 자동 가속을 사용하면 사용자 로그인이 더 간소화됩니다.  개별 애플리케이션에 대해 자동 가속을 구성할 수 있습니다.

>[!NOTE]
>자동 가속에 대해 애플리케이션을 구성하는 경우 게스트 사용자는 로그인할 수 없습니다. 인증을 위해 페더레이션된 IdP로 사용자를 바로 보내는 경우 Azure Active Directory 로그인 페이지로 다시 돌아갈 방법이 없습니다. 다른 테넌트 또는 Microsoft 계정과 같은 외부 IdP로 이동해야 할 수도 있는 게스트 사용자는 홈 영역 검색 단계가 생략되므로 해당 애플리케이션에 로그인할 수 없습니다.  

페더레이션된 IdP로의 자동 가속 기능을 제어하는 방법에는 다음 두 가지가 있습니다.   

- 애플리케이션에 대한 인증 요청에 도메인 힌트를 사용합니다. 
- 자동 가속 기능을 사용하도록 홈 영역 검색 정책을 구성합니다.

### <a name="domain-hints"></a>도메인 힌트    
도메인 힌트는 애플리케이션의 인증 요청에 포함되는 지시문입니다. 페더레이션된 IdP 로그인 페이지로 사용자를 빠르게 보내는 데 사용할 수 있습니다. 또는 다중 테넌트 애플리케이션에서 테넌트에 대한 브랜딩 Azure AD 로그인 페이지로 사용자를 바로 보내는 데 사용될 수 있습니다.  

예를 들어, 애플리케이션 “largeapp.com”은 고객이 사용자 지정 URL “contoso.largeapp.com”에서 애플리케이션에 액세스할 수 있도록 합니다. 또한 앱은 인증 요청의 contoso.com에 도메인 힌트를 포함할 수 있습니다. 

도메인 힌트 구문은 사용되는 프로토콜에 따라 다르며, 일반적으로 애플리케이션에서 구성됩니다.

**WS-Federation**: 쿼리 문자열의 whr=contoso.com

**SAML**: 도메인 힌트가 포함된 SAML 인증 요청 또는 쿼리 문자열 whr=contoso.com

**Open ID Connect**: 쿼리 문자열 domain_hint=contoso.com 

도메인 힌트가 애플리케이션의 인증 요청에 포함되고 테넌트가 해당 도메인과 페더레이션되면 Azure AD는 해당 도메인에 대해 구성된 IdP로 로그인을 리디렉션하려고 합니다. 

도메인 힌트가 확인된 페더레이션된 도메인을 참조하지 않으면 무시되며 일반 홈 영역 검색이 호출됩니다.

Azure Active Directory에서 지원하는 도메인 힌트를 사용한 자동 가속 기능에 대한 자세한 내용은 [Enterprise Mobility + Security 블로그](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)를 참조하세요.

>[!NOTE]
>도메인 힌트가 인증 요청에 포함되어 있으면 HRD 정책에서 애플리케이션에 대해 설정된 자동 가속을 재정의합니다.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>자동 가속에 대한 홈 영역 검색 정책
일부 애플리케이션은 인증 요청을 구성하는 방법을 제공하지 않습니다. 이 경우 도메인 힌트를 사용하여 자동 가속을 제어할 수는 없습니다. 동일한 동작을 얻기 위해 정책을 통해 자동 가속을 구성할 수 있습니다.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>레거시 애플리케이션에 직접 인증 사용
애플리케이션에서 AAD 라이브러리와 대화형 로그인을 사용하여 사용자를 인증하는 것이 가장 좋습니다. 라이브러리는 페더레이션 사용자 흐름을 처리합니다.  레거시 애플리케이션에서 페더레이션을 인식하도록 작성되지 않는 경우도 있습니다. 이 경우 홈 영역 검색을 수행하지 않으며, 올바른 페더레이션된 엔드포인트와 상호 작용하여 사용자를 인증하지 않습니다. HRD 정책을 사용하도록 선택하는 경우 사용자 이름/암호 자격 증명을 제출하는 특정 레거시 애플리케이션에서 이 정책을 통해 Azure Active Directory에서 직접 인증할 수 있습니다. 암호 해시 동기화를 사용하도록 설정해야 합니다. 

> [!IMPORTANT]
> 암호 해쉬 동기화가 사용되고 온-프레미스 IdP에서 구현된 정책 없이 이 애플리케이션을 인증할 수 있는 경우에만 직접 인증을 사용하도록 설정하세요. 어떤 이유로든 암호 해시 동기화를 해제하거나 AD Connect를 통해 디렉터리 동기화를 해제하는 경우 오래된 암호 해시를 사용하여 직접 인증할 수 없도록 방지하기 위해 이 정책을 제거해야 합니다.

## <a name="set-hrd-policy"></a>HRD 정책 설정
페더레이션 로그인 자동 가속용 애플리케이션 또는 직접 클라우드 기반 애플리케이션에 HRD 정책을 설정하는 세 가지 단계가 있습니다.

1. HRD 정책 만들기

2. 정책을 연결할 서비스 주체 찾기

3. 서비스 주체에 정책 연결 

정책은 서비스 주체에 연결된 특정 애플리케이션에만 적용됩니다. 

한 번에 하나의 HRD 정책만 서비스 주체에서 활성화할 수 있습니다.  

Microsoft Azure Active Directory Graph API 또는 Azure Active Directory PowerShell cmdlet을 사용하여 HRD 정책을 만들고 관리할 수 있습니다.

정책을 조작하는 Graph API는 MSDN의 [정책에 대한 작업](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) 문서에 설명되어 있습니다.

다음은 HRD 정책 정의의 예제입니다.
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

정책 형식은 “HomeRealmDiscoveryPolicy”입니다.

**AccelerateToFederatedDomain**은 선택 사항입니다. **AccelerateToFederatedDomain**이 false인 경우 정책이 자동 가속에 영향을 주지 않습니다. **AccelerateToFederatedDomain**이 true이고 테넌트에 확인되고 페더레이션된 도메인이 하나만 있는 경우, 사용자가 페더레이션된 IdP로 바로 이동되어 로그인할 수 있습니다. true이고 테넌트에 확인된 도메인이 둘 이상 있는 경우 **PreferredDomain**을 지정해야 합니다.

**PreferredDomain**은 선택 사항입니다. **PreferredDomain**은 가속화하는 도메인을 나타내야 합니다. 테넌트에 페더레이션된 도메인이 단 하나인 경우 생략할 수 있습니다.  이 항목이 생략되고 확인된 페더레이션된 도메인이 2개 이상 있는 경우 정책은 적용되지 않습니다.

 **PreferredDomain**이 지정된 경우 테넌트에 대한 확인된 페더레이션된 도메인이 일치해야 합니다. 애플리케이션의 모든 사용자가 해당 도메인에 로그인할 수 있어야 합니다.

**AllowCloudPasswordValidation**은 선택 사항입니다. **AllowCloudPasswordValidation**이 true인 경우 애플리케이션에서 사용자 이름/암호 자격 증명을 Azure Active Directory 토큰 엔드포인트에 직접 표시하여 페더레이션 사용자를 인증할 수 있습니다. 암호 해시 동기화가 사용되는 경우에만 작동합니다.

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 정책의 우선 순위 및 평가
HRD 정책을 만들어 특정 조직 및 서비스 주체에 할당할 수 있습니다. 즉, 여러 정책을 특정 애플리케이션에 적용할 수 있습니다. 적용되는 HRD 정책은 다음 규칙을 따릅니다.


- 도메인 힌트가 인증 요청에 있으면 자동 가속에 대한 HRD 정책이 무시됩니다. 도메인 힌트로 지정된 동작이 사용됩니다.

- 그렇지 않고 정책이 서비스 주체에 명시적으로 할당된 경우 해당 정책이 적용됩니다. 

- 도메인 힌트가 없고, 서비스 주체에 명시적으로 할당된 정책이 없는 경우 서비스 주체의 부모 조직에 명시적으로 할당된 정책이 적용됩니다. 

- 도메인 힌트가 없고 서비스 주체 또는 조직에 할당된 정책이 없는 경우 기본 HRD 동작이 사용됩니다.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>애플리케이션에 HRD 정책을 설정하기 위한 자습서 
Azure AD PowerShell cmdlet을 사용하여 다음을 포함한 몇 가지 시나리오를 연습해 보겠습니다.


- 페더레이션된 단일 도메인이 있는 테넌트의 애플리케이션에 대해 자동 가속을 수행하도록 HRD 정책을 설정합니다.

- 테넌트에 대해 확인된 여러 도메인 중 하나에 애플리케이션 자동 가속을 수행하도록 HRD 정책을 설정합니다.

- 레거시 애플리케이션에서 페더레이션 사용자에 대한 사용자 이름/암호 인증을 Azure Active Directory에 직접 수행할 수 있도록 HRD 정책을 설정합니다.

- 정책이 구성된 애플리케이션 나열


### <a name="prerequisites"></a>사전 요구 사항
다음 예제에서는 Azure AD에서 애플리케이션 서비스 주체에 대해 정책을 생성, 업데이트, 연결 및 삭제합니다.

1.  시작하려면 최신 Azure AD PowerShell cmdlet 미리 보기를 다운로드합니다. 

2.  Azure AD PowerShell Cmdlet을 다운로드한 후에는 Connect 명령을 실행하여 관리자 계정으로 Azure AD에 로그인합니다.

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  다음 명령을 실행하여 조직의 모든 정책을 확인합니다.

    ``` powershell
    Get-AzureADPolicy
    ```

아무 정책도 반환되지 않으면 테넌트에서 만들어진 정책이 없는 것입니다.

### <a name="example-set-hrd-policy-for-an-application"></a>예제: 애플리케이션에 대한 HRD 정책 설정 

다음 예제에서는 애플리케이션에 할당되는 다음 정책 중 하나를 만듭니다. 
- 테넌트에 단일 도메인이 있는 경우 애플리케이션에 로그인할 때 사용자를 AD FS 로그인 화면으로 자동 가속합니다. 
- 테넌트에 페더레이션된 도메인이 둘 이상 있는 경우 사용자를 AD FS 로그인 화면으로 자동 가속합니다.
- 정책이 할당된 애플리케이션의 페더레이션 사용자에 대한 비대화형 사용자 이름/암호 로그인을 Azure Active Directory에 직접 사용하도록 설정합니다.

#### <a name="step-1-create-an-hrd-policy"></a>1단계: HRD 정책 만들기

테넌트에 단일 도메인이 있는 경우 다음 정책은 애플리케이션에 로그인할 때 사용자를 AD FS 로그인 화면으로 자동 가속합니다.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
테넌트에 페더레이션된 도메인이 둘 이상 있는 경우 다음 정책은 사용자를 AD FS 로그인 화면으로 자동 가속합니다. 애플리케이션에 대한 사용자를 인증하는 페더레이션된 도메인이 둘 이상 있는 경우 자동 가속할 도메인을 지정해야 합니다.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

페더레이션 사용자에 대한 사용자 이름/암호 인증을 Azure Active Directory를 통해 직접 사용하도록 설정하는 특정 애플리케이션에 대한 정책을 만들려면 다음 명령을 실행합니다.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


새 정책을 확인하고 해당 **ObjectID**를 가져오려면 다음 명령을 실행합니다.

``` powershell
Get-AzureADPolicy
```


HRD 정책을 만든 후에 적용하려면 여러 애플리케이션 서비스 주체에 할당하면 됩니다.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>2단계: 정책을 할당할 서비스 주체 찾기  
정책을 할당할 서비스 주체의 **ObjectID**가 필요합니다. 여러 가지 방법으로 서비스 주체의 **ObjectID**를 찾을 수 있습니다.    

포털을 사용하거나 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)를 쿼리할 수 있습니다. [Graph 탐색기 도구](https://developer.microsoft.com/graph/graph-explorer)로 이동하고 Azure AD 계정에 로그인하여 조직의 모든 서비스 주체를 확인할 수 있습니다. 

PowerShell을 사용 하 고 있기 때문에 다음 cmdlet을 사용 하 여 서비스 주체 및 해당 Id를 나열할 수 있습니다.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>3단계: 서비스 주체에게 정책 할당  
자동 가속을 구성하려는 애플리케이션의 서비스 주체 **ObjectID**를 찾은 후 다음 명령을 실행합니다. 이 명령은 1단계에서 만든 HRD 정책을 2단계에서 찾은 서비스 주체에 연결합니다.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

정책을 추가하려는 각 서비스 주체에 대해 이 명령을 반복할 수 있습니다.

이미 애플리케이션에 HomeRealmDiscovery 정책이 할당되어 있는 경우 두 번째 정책은 추가할 수 없습니다.  이 경우 추가 매개 변수를 추가하기 위해 애플리케이션에 할당된 홈 영역 검색 정책의 정의를 변경합니다.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>4단계: HRD 정책에 할당된 애플리케이션 서비스 주체 확인
HRD 정책이 구성되어 있는 애플리케이션을 확인하려면 **Get-AzureADPolicyAppliedObject** cmdlet을 사용합니다. 확인하려는 정책의 **ObjectID**를 전달합니다.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>5단계: 완료되었습니다!
애플리케이션을 통해 새 정책이 작동 중인지 확인합니다.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>예제: HRD 정책이 구성된 애플리케이션 나열

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>1단계: 조직에서 생성된 모든 정책 나열 

``` powershell
Get-AzureADPolicy
```

할당을 나열하려는 정책의 **ObjectID**를 적어둡니다.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>2단계: 정책이 할당된 서비스 주체 나열  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>예제: 애플리케이션에 대한 HRD 정책 제거
#### <a name="step-1-get-the-objectid"></a>1단계: ObjectID 가져오기
앞의 예제를 사용하여 제거하려는 애플리케이션 서비스 주체의 정책 **ObjectID**를 가져옵니다. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>2단계: 애플리케이션 서비스 주체에서 정책 할당 제거  

``` powershell
Remove-AzureADApplicationPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>3단계: 정책이 할당된 서비스 주체를 나열하여 제거 확인 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>다음 단계
- Azure AD에서 인증이 작동하는 방법에 대한 자세한 내용은 [Azure AD의 인증 시나리오](../develop/authentication-scenarios.md)를 참조하세요.
- 사용자 Single Sign-On에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램에 대 한 single sign-on을](what-is-single-sign-on.md)참조 하세요.
- [Microsoft id 플랫폼](../develop/v2-overview.md) 을 방문 하 여 모든 개발자 관련 콘텐츠에 대 한 개요를 확인 하세요.

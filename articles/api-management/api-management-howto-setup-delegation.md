---
title: 사용자 등록 및 제품 구독을 위임하는 방법
description: Azure API Management에서 사용자 등록 및 제품 구독을 타사에 위임하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454348"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>사용자 등록 및 제품 구독을 위임하는 방법

위임을 사용 하면 개발자 포털의 기본 제공 기능을 사용 하는 것과는 반대로, 기존 웹 사이트를 사용 하 여 개발자 로그인/등록 및 제품 구독을 처리할 수 있습니다. 웹 사이트에서 사용자 데이터를 소유 하 고 사용자 지정 방식으로 이러한 단계의 유효성 검사를 수행할 수 있습니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegate-signin-up"> </a>개발자 로그인 및 등록 위임

개발자를 위임 하 고 로그인 하 여 기존 웹 사이트에 등록 하려면 사이트에서 특수 위임 엔드포인트을 만들어야 합니다. API Management 개발자 포털에서 시작 된 이러한 요청에 대한 진입점으로 작동 해야 합니다.

최종 워크플로는 다음과 같습니다.

1. 개발자가 API Management 개발자 포털에서 로그인 또는 등록 링크를 클릭 합니다.
2. 브라우저가 위임 엔드포인트로 리디렉션됩니다.
3. 반환의 위임 엔드포인트은 사용자에 게 로그인 또는 등록을 요청 하는 UI로 리디렉션 또는 제공
4. 성공하면 사용자가 처음 시작했던 API Management 개발자 포털 페이지로 다시 리디렉션됩니다.

먼저, 위임 엔드포인트를 통해 요청을 라우팅하도록 API Management를 설정하겠습니다. Azure Portal에서 API Management 리소스의 **보안** 을 검색 한 다음 **위임** 항목을 클릭 합니다. ' 대리인 로그인 & 등록 '을 사용 하도록 설정 하려면이 확인란을 클릭 합니다.

![위임 페이지][api-management-delegation-signin-up]

* 특수한 위임 엔드포인트의 URL을 결정하고 **위임 엔드포인트 URL** 필드에 이 URL을 입력합니다. 
* 위임 인증 키 필드 내에서 요청이 실제로 Azure API Management에서 나오는지 확인하기 위해 사용자에게 제공된 서명을 계산하는 데 사용될 비밀을 입력합니다. **생성** 단추를 클릭하여 API Management에서 사용자를 위해 키를 임의로 생성할 수 있습니다.

이제 **위임 엔드포인트**를 만들어야 합니다. 몇 가지 작업을 수행해야 합니다.

1. 다음 형식의 요청을 받습니다.
   
   > *http:\//www.yourwebsite.com/apimdelegation? operation = SignIn & returnUrl = {원본 페이지의 URL} & 솔트 = {string} & sig = {string}*
   > 
   > 
   
    로그인/등록 사례에 대한 쿼리 매개 변수:
   
   * **operation**: 위임 요청의 유형을 식별합니다. 이 경우 **SignIn**만 가능합니다.
   * **returnUrl**: 사용자가 로그인 또는 등록 링크를 클릭 하는 페이지의 URL입니다.
   * **salt**: 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다.
   * **sig**: 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다.
2. 요청이 Azure API Management에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).
   
   * **returnUrl** 및 **salt** 쿼리 매개 변수([아래 제공된 예제 코드])에 따라 문자열의 HMAC-SHA512 해시를 컴퓨팅합니다.
     
     > HMAC(**salt** + '\n' + **returnUrl**)
     > 
     > 
   * 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행하고, 그렇지 않으면 요청을 거부합니다.
3. 로그인/등록에 대한 요청을 받고 있는지 확인 합니다. **작업** 쿼리 매개 변수는 "**SignIn**"으로 설정 됩니다.
4. 로그인 또는 등록을 위한 UI를 사용자에 게 제공
5. 사용자가 등록하고 있는 경우 API Management에서 사용자의 해당 계정을 만들어야 합니다. API Management REST API를 사용하여 [사용자를 만듭니다]. 이렇게 하려면 사용자 ID를 사용자 저장소에서와 동일한 값으로 설정 하거나 추적할 수 있는 ID로 설정 해야 합니다.
6. 사용자가 인증되면
   
   * API Management REST API를 통해 [SSO(Single-Sign-On) 토큰을 요청]합니다.
   * returnUrl 쿼리 매개 변수를 위의 API 호출에서 받은 SSO URL에 추가합니다.
     
     > 예: https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * 사용자를 위에서 생성한 URL로 리디렉션합니다.

**SignIn** 작업 외에도, 이전 단계를 수행하고 다음 작업 중 하나를 사용하여 계정 관리를 수행할 수 있습니다.

* **ChangePassword**
* **ChangeProfile**
* **CloseAccount**

계정 관리 작업에 대한 다음 쿼리 매개 변수를 전달해야 합니다.

* **operation**: 위임 요청의 유형을 식별합니다(ChangePassword, ChangeProfile 또는 CloseAccount).
* **userId**: 관리할 계정의 사용자 ID
* **salt**: 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다.
* **sig**: 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다.

## <a name="delegate-product-subscription"> </a>제품 구독 위임
제품 구독 위임은 사용자 로그인을 위임 하는 것과 유사 하 게 작동 합니다. 최종 워크플로는 다음과 같습니다.

1. 개발자는 API Management 개발자 포털에서 제품을 선택 하 고 구독 단추를 클릭 합니다.
2. 브라우저가 위임 엔드포인트으로 리디렉션됩니다.
3. 위임 엔드포인트은 필요한 제품 구독 단계를 수행 합니다. 단계를 설계 하는 것은 사용자의 것입니다. 여기에는 다른 페이지로 리디렉션하여 청구 정보를 요청 하거나, 추가 질문을 요청 하거나, 단순히 정보를 저장 하 고 사용자 작업을 요구 하지 않을 수 있습니다.

이 기능을 사용하려면 **위임** 페이지에서 **제품 구독 위임**을 클릭합니다.

다음으로 위임 엔드포인트에서 다음 작업을 수행 하는지 확인 합니다.

1. 다음 형식의 요청을 받습니다.
   
   > *http:\//www.yourwebsite.com/apimdelegation? operation = {operation} & productId = {product to 구독할} & userId = {사용자 만들기 요청} & 솔트 = {string} & sig = {string}*
   >
   
    제품 구독 케이스에 대한 쿼리 매개 변수:
   
   * **operation**: 위임 요청 유형을 식별합니다. 제품 구독 요청의 경우 유효한 옵션은 다음과 같습니다.
     * "Subscribe": 사용자가 제공된 ID를 사용하여 지정된 제품을 구독하도록 하는 요청입니다(아래 참조).
     * "Unsubscribe": 제품에 대한 사용자 구독을 취소하는 요청입니다.
     * "Renew": 구독을 갱신하는 요청입니다(예: 만료일이 다가오는 경우).
   * **productId**: 사용자가 구독을 요청한 제품의 ID입니다.
   * **subscriptionId**: *구독 취소* 및 *갱신* -제품 구독의 ID입니다.
   * **userId**: 요청을 만든 사용자의 ID입니다.
   * **salt**: 보안 해시를 계산하는 데 사용되는 특수 salt 문자열입니다.
   * **sig**: 자신의 계산된 해시와 비교하는 데 사용되는 계산된 보안 해시입니다.

2. 요청이 Azure API Management에서 들어오는지 확인합니다(선택 사항이지만 보안을 위해 상당히 권장됨).
   
   * **ProductId**, **userId**및 **salt** 쿼리 매개 변수를 기반으로 하는 문자열의 HMAC SHA512을 계산 합니다.
     
     > HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * 위의 계산된 해시와 **sig** 쿼리 매개 변수 값을 비교합니다. 두 해시가 일치하면 다음 단계를 진행하고, 그렇지 않으면 요청을 거부합니다.
3. **작업** 에 요청 된 작업 유형 (예: 청구, 추가 질문 등)을 기반으로 제품 구독을 처리 합니다.
4. 사용자 측의 제품에 사용자를 성공적으로 구독 하는 경우 [구독에 대한 REST API 호출]하 여 API Management 제품에 사용자를 구독 합니다.

## <a name="delegate-example-code"> </a> 예제 코드

이러한 코드 샘플에서는 다음을 수행 하는 방법을 보여 줍니다.

* 게시자 포털의 위임 화면에서 설정 된 *위임 유효성 검사 키*를 사용 합니다.
* HMAC를 만든 다음 서명의 유효성을 검사 하 고 전달 된 returnUrl의 유효성을 증명 하는 데 사용 됩니다.

약간만 수정하면 동일한 코드를 productId 및 userId에도 사용할 수 있습니다.

**returnUrl의 해시를 생성하는 C# 코드**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**returnUrl의 해시를 생성하는 NodeJS 코드**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> 위임 변경 내용을 적용 하려면 [개발자 포털](api-management-howto-developer-portal-customize.md#publish) 을 다시 게시 해야 합니다.

## <a name="next-steps"></a>다음 단계
위임에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[SSO(Single-Sign-On) 토큰을 요청]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[사용자를 만듭니다]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[구독에 대한 REST API 호출]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[아래 제공된 예제 코드]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 

---
title: 사용자 지정 클레임 요청 (MSAL iOS/macOS) | Microsoft
titleSuffix: Microsoft identity platform
description: 사용자 지정 클레임을 요청 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 44158296faaf238fd72f2360149d3d93f68c5ba0
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085594"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>방법: iOS 및 macOS 용 MSAL을 사용 하 여 사용자 지정 클레임 요청

Openid connect Connect를 사용 하면 필요에 따라 사용자 정보 엔드포인트 및/또는 ID 토큰에서 개별 클레임의 반환을 요청할 수 있습니다. 클레임 요청은 요청 된 클레임 목록을 포함 하는 JSON 개체로 표현 됩니다. 자세한 내용은 [Openid connect Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) 을 참조 하세요.

IOS 및 macOS 용 MSAL (Microsoft 인증 라이브러리)을 사용 하면 대화형 토큰 획득 시나리오와 자동 토큰 획득 시나리오에서 특정 클레임을 요청할 수 있습니다. `claimsRequest` 매개 변수를 통해이를 수행 합니다.

이 경우 여러 가지 시나리오가 필요 합니다. 다음은 그 예입니다.

- 응용 프로그램의 표준 집합 외부에서 클레임을 요청 합니다.
- 응용 프로그램에 대 한 범위를 사용 하 여 지정할 수 없는 표준 클레임의 특정 조합 요청 예를 들어 클레임 누락으로 인해 액세스 토큰이 거부 된 경우 응용 프로그램은 MSAL을 사용 하 여 누락 된 클레임을 요청할 수 있습니다.

> [!NOTE]
> MSAL은 클레임 요청이 지정 될 때마다 액세스 토큰 캐시를 무시 합니다. 각 MSAL API 호출에서 항상 동일한 `claimsRequest` 매개 변수를 제공 하는 것과는 반대로, 추가 클레임이 필요한 경우에만 `claimsRequest` 매개 변수를 제공 하는 것이 중요 합니다.

`MSALSilentTokenParameters` 및 `MSALInteractiveTokenParameters`에서 `claimsRequest` 지정할 수 있습니다.

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest`는 JSON 클레임 요청의 NSString 표현에서 생성할 수 있습니다. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



추가 특정 클레임을 요청 하 여 수정할 수도 있습니다.

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



그런 다음 토큰 매개 변수에를 설정 하 고 MSAL 토큰 획득 Api 중 하나에 `MSALClaimsRequest` 해야 합니다.

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>다음 단계

[인증 흐름 및 애플리케이션 시나리오](authentication-flows-app-scenarios.md)에 대해 알아보기

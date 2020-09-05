---
title: Xamarin Android 시스템 브라우저 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)을 사용 하 여 Xamarin Android에서 시스템 브라우저를 사용 하기 위한 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8f755b42249a88a2d37117003d561f79aea6b170
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165994"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>MSAL.NET 사용에 대 한 Xamarin Android 시스템 브라우저 고려 사항

이 문서에서는 Microsoft Authentication Library for .NET (MSAL.NET)을 사용 하 여 Xamarin Android에서 시스템 브라우저를 사용할 때 고려해 야 할 사항을 설명 합니다.

MSAL.NET 2.4.0 Preview부터 MSAL.NET는 Chrome 이외의 브라우저를 지원 합니다. 인증을 위해 Android 장치에 더 이상 Chrome을 설치할 필요가 없습니다.

사용자 지정 탭을 지 원하는 브라우저를 사용 하는 것이 좋습니다. 이러한 브라우저의 몇 가지 예는 다음과 같습니다.

| 사용자 지정 탭이 지원 되는 브라우저 | 패키지 이름 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | .com. emmx|
|Firefox | org. firefox|
|Ecosia | ecosia|
|키 위 | kiwibrowser|
|감 | com. 용감한 브라우저|

사용자 지정 탭 지원 기능을 제공 하는 브라우저를 식별 하는 것 외에도 테스트는 사용자 지정 탭을 지원 하지 않는 몇 가지 브라우저가 인증에도 적용 됨을 나타냅니다. 이러한 브라우저에는 Opera, Opera Mini, InBrowser 및 Maxthon이 포함 됩니다. 

## <a name="tested-devices-and-browsers"></a>테스트 된 장치 및 브라우저
다음 표에는 인증 호환성에 대해 테스트 된 장치와 브라우저가 나와 있습니다.

| 디바이스 | 브라우저     |  결과  | 
| ------------- |:-------------:|:-----:|
| Huawei/One + | Chrome\* | 합격|
| Huawei/One + | Microsoft Edge\* | 합격|
| Huawei/One + | Firefox\* | 합격|
| Huawei/One + | 감\* | 합격|
| 1 + | Ecosia\* | 합격|
| 1 + | 키 위\* | 합격|
| Huawei/One + | Opera | 합격|
| Huawei | 운영 체제 | 합격|
| Huawei/One + | InBrowser | 합격|
| 1 + | Maxthon | 합격|
| Huawei/One + | DuckDuckGo | 사용자가 취소 한 인증|
| Huawei/One + | UC 브라우저 | 사용자가 취소 한 인증|
| 1 + | Dolphin | 사용자가 취소 한 인증|
| 1 + | CM 브라우저 | 사용자가 취소 한 인증|
| Huawei/One + | 설치 안 됨 | AndroidActivityNotFound 예외|

\* 사용자 지정 탭 지원

## <a name="known-issues"></a>알려진 문제

사용자가 장치에서 브라우저를 사용 하도록 설정 하지 않은 경우 MSAL.NET는 예외를 throw `AndroidActivityNotFound` 합니다.  
  - **완화**방법: 장치에서 브라우저를 사용 하도록 설정 하도록 사용자에 게 요청 합니다. 사용자 지정 탭을 지 원하는 브라우저를 권장 합니다.

인증이 실패 하는 경우 (예: DuckDuckGo를 사용 하 여 인증을 시작 하는 경우) MSAL.NET가를 반환 `AuthenticationCanceled MsalClientException` 합니다. 
  - **근본 문제**: 장치에서 사용자 지정 탭을 지 원하는 브라우저를 사용할 수 없습니다. 인증을 완료할 수 없는 브라우저를 사용 하 여 인증을 시작 했습니다. 
  - **완화**방법: 장치에서 브라우저를 사용 하도록 설정 하도록 사용자에 게 요청 합니다. 사용자 지정 탭을 지 원하는 브라우저를 권장 합니다.

## <a name="next-steps"></a>다음 단계
자세한 내용 및 코드 예제를 보려면 [Xamarin Android의 시스템 브라우저와 Xamarin Android에서 포함 된 웹 브라우저와](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) [포함 된 시스템 웹 UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)중에서 선택을 참조 하세요.  
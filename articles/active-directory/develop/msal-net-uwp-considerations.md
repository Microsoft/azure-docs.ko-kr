---
title: UWP 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET)과 함께 유니버설 Windows 플랫폼를 사용 하는 경우의 특정 고려 사항에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 4803b2bda63ef0e14137aaafe95a422089e7f671
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083669"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>MSAL.NET의 유니버설 Windows 플랫폼 관련 고려 사항
UWP에는 MSAL.NET을 사용할 때 고려해 야 할 몇 가지 고려 사항이 있습니다.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 속성
WinRT 플랫폼에서 `PublicClientApplication`에는 다음과 같은 부울 속성 ``UseCorporateNetwork``있습니다. 이 속성을 사용 하면 Win 8.1 및 UWP 응용 프로그램에서 사용자가 페더레이션된 Azure AD 테넌트의 계정으로 로그인 하는 경우 Windows 통합 인증 (즉, 운영 체제를 사용 하 여 로그인 한 사용자로 SSO)을 이용할 수 있습니다. 이 속성을 설정 하면 MSAL.NET는 WAB (웹 인증 브로커)를 활용 합니다.

> [!IMPORTANT]
> 이 속성을 true로 설정 하면 응용 프로그램 개발자가 응용 프로그램에서 IWA (Windows 통합 인증)를 사용 하도록 설정 했다고 가정 합니다. 이렇게 하려면 다음을 수행 합니다.
> - UWP 응용 프로그램에 대 한 ``Package.appxmanifest``의 **기능** 탭에서 다음 기능을 사용 하도록 설정 합니다.
>   - 엔터프라이즈 인증
>   - 프라이빗 네트워크(클라이언트 및 서버)
>   - 공유 사용자 인증서

엔터프라이즈 인증 또는 공유 사용자 인증서 기능을 요청 하는 응용 프로그램은 Windows 스토어에 대 한 높은 수준의 확인을 허용 해야 하며, 모든 개발자가 더 높은 수준의 확인을 수행 하 고 싶을 수 있으므로 IWA는 기본적으로 사용 되지 않습니다. 확인 수준입니다.

조건부 액세스를 사용 하는 엔터프라이즈 시나리오에서는 UWP 플랫폼 (WAB)의 기본 구현이 제대로 작동 하지 않습니다. 사용자가 Windows hello를 사용 하 여 로그인을 시도 하 고 인증서를 선택 하기 위해 제안 되는 증상은 다음과 같습니다.

- pin에 대 한 인증서를 찾을 수 없습니다.
- 또는 사용자가 선택 하 고 Pin을 입력 하 라는 메시지가 표시 되지 않습니다.

해결 방법은 대체 방법 (사용자 이름/암호 + 전화 인증)을 사용 하는 것 이지만 환경이 좋지 않습니다.

## <a name="troubleshooting"></a>문제 해결

일부 고객은 특정 엔터프라이즈 환경에서 다음 로그인 오류가 발생 했음을 보고 했습니다.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

이러한 사용자는 인터넷에 연결 되어 있고 공용 네트워크에서 작동 한다는 것을 알고 있습니다.

해결 방법은 WAB (기본 Windows 구성 요소)에서 개인 네트워크를 허용 하는지 확인 하는 것입니다. 레지스트리 키를 설정 하 여이 작업을 수행할 수 있습니다.

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

자세한 내용은 [웹 인증 브로커-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 샘플에서 제공 됩니다.

샘플 | 플랫폼 | Description 
|------ | -------- | -----------|
|[활성-디렉터리-dotnet-v2-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Msal.net를 사용 하는 유니버설 Windows 플랫폼 클라이언트 응용 프로그램으로, Azure AD v2.0 엔드포인트을 사용 하 여 인증 하는 사용자에 대 한 Microsoft Graph에 액세스 합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | 간단한 Xamarin Forms 앱은 MSAL을 사용 하 여 AAD v2.0 엔드포인트을 통해 MSA 및 Azure AD를 인증 하 고 결과 토큰을 사용 하 여 Microsoft Graph에 액세스 하는 방법을 보여주는 합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

---
title: UWP 고려 사항 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL.NET (Microsoft Authentication Library for .NET)에서 UWP (유니버설 Windows 플랫폼)를 사용 하기 위한 고려 사항에 대해 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132518"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>MSAL.NET와 함께 유니버설 Windows 플랫폼 사용 시 고려 사항
MSAL.NET에서 UWP (유니버설 Windows 플랫폼)를 사용 하는 응용 프로그램 개발자는이 문서에서 제공 하는 개념을 고려해 야 합니다.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 속성
Windows 런타임 (WinRT) 플랫폼에서 `PublicClientApplication` 부울 속성 `UseCorporateNetwork`있습니다. 사용자가 페더레이션된 Azure Active Directory (Azure AD) 테 넌 트를 포함 하는 계정에 로그인 한 경우이 속성을 사용 하 여 Windows 8.1 응용 프로그램 및 UWP 응용 프로그램에서 IWA (Windows 통합 인증)의 이점을 누릴 수 있습니다. 운영 체제에 로그인 한 사용자는 SSO (Single Sign-On)를 사용할 수도 있습니다. `UseCorporateNetwork` 속성을 설정 하는 경우 MSAL.NET는 WAB (web authentication broker)를 사용 합니다.

> [!IMPORTANT]
> `UseCorporateNetwork` 속성을 true로 설정 하면 응용 프로그램 개발자가 응용 프로그램에서 IWA를 사용 하도록 설정 했다고 가정 합니다. IWA를 사용 하도록 설정 하려면
> - UWP 응용 프로그램의 `Package.appxmanifest`에 있는 **기능** 탭에서 다음 기능을 사용 하도록 설정 합니다.
>   - **엔터프라이즈 인증**
>   - **개인 네트워크(클라이언트 및 서버)**
>   - **공유 사용자 인증서**

IWA는 엔터프라이즈 인증 또는 공유 사용자 인증서의 기능을 요청 하는 응용 프로그램을 허용 하기 전에 높은 수준의 인증을 요구 하기 때문 Microsoft Store에 기본적으로 사용 하도록 설정 되어 있지 않습니다. 모든 개발자가이 수준의 확인을 수행 하려고 하는 것은 아닙니다.

UWP 플랫폼에서 기본 WAB 구현은 조건부 액세스가 설정 된 엔터프라이즈 시나리오에서 제대로 작동 하지 않습니다. 사용자가 Windows Hello를 사용 하 여 로그인 하려고 할 때이 문제의 증상을 볼 수 있습니다. 사용자에 게 인증서를 선택 하 라는 메시지가 표시 되 면 다음을 수행 합니다.

- PIN에 대 한 인증서를 찾을 수 없습니다.
- 사용자가 인증서를 선택 하면 PIN을 입력 하 라는 메시지가 표시 되지 않습니다.

사용자 이름-암호 및 전화 인증과 같은 다른 방법을 사용 하 여이 문제를 방지할 수 있지만 환경이 좋지 않습니다.

## <a name="troubleshooting"></a>문제 해결

일부 고객은 인터넷에 연결 되어 있고 연결이 공용 네트워크에서 작동 한다는 것을 알고 있는 특정 엔터프라이즈 환경에서 다음 로그인 오류를 보고 했습니다.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

WAB (기본 Windows 구성 요소)에서 개인 네트워크를 허용 하도록 설정 하면이 문제를 방지할 수 있습니다. 레지스트리 키를 설정 하 여이 작업을 수행할 수 있습니다.

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

자세한 내용은 [웹 인증 브로커-Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 샘플에서는 자세한 정보를 제공 합니다.

샘플 | 플랫폼 | 설명 
|------ | -------- | -----------|
|[활성-디렉터리-dotnet-v2-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | MSAL.NET를 사용 하는 UWP 클라이언트 응용 프로그램입니다. Azure AD 2.0 끝점을 사용 하 여 인증 하는 사용자에 대 한 Microsoft Graph 액세스 합니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[활성-디렉터리-xamarin-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | MSAL을 사용 하 여 Azure AD 2.0 끝점을 통해 Microsoft 개인 계정 및 Azure AD를 인증 하는 방법을 보여 주는 간단한 Xamarin Forms 앱입니다. 또한 Microsoft Graph 액세스 하 고 결과 토큰을 표시 하는 방법을 보여 줍니다. <br>![토폴로지](media/msal-net-uwp-considerations/topology-xamarin-native.png)|

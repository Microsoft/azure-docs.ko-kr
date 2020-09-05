---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 07/31/2020
ms.author: rgarcia
ms.openlocfilehash: 310c0f547ee11a3243589c364755a30a84be1a25
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810181"
---
## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

* [Azure Spatial Anchors 개요](../articles/spatial-anchors/overview.md)를 자세히 읽었습니다.
* [5분 빠른 시작](../articles/spatial-anchors/index.yml) 중 하나를 완료했습니다.
* C# 및 Unity에 대한 기본 지식이 있습니다.
* Android를 사용하려는 경우 <a href="https://developers.google.com/ar/discover/" target="_blank">ARCore</a>, iOS를 사용하려는 경우 <a href="https://developer.apple.com/arkit/" target="_blank">ARKit</a>에 대한 기본 지식이 있습니다.
* <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 이상</a>이 설치되어 있고 **ASP.NET 및 웹 개발** 워크로드가 포함된 Windows 컴퓨터
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download).
* 앱을 배포하고 실행할 하나 이상의 디바이스(iOS 또는 Android)
  * Android를 사용하는 경우 다음 조건을 충족해야 합니다.
    * Windows 컴퓨터에 설치된 <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3</a> 이상, <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4(LTS)</a> 및 <a href="https://git-scm.com/download/win" target="_blank">Git for Windows</a>.
    * <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">개발자가 사용 가능한</a><a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore 지원</a> Android 디바이스
  * iOS를 사용하는 경우 다음 조건을 충족해야 합니다.
    * <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a> 이상, <a href="https://cocoapods.org" target="_blank">CocoaPods</a> 및 <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.4(LTS)</a>가 설치된 macOS 컴퓨터.
    * 개발자가 사용 가능한 <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 호환</a> iOS 디바이스
    * Homebrew를 통해 설치된 Git 터미널의 한 줄에 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` 명령을 입력한 다음, `brew install git` 명령을 실행합니다.

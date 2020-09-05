---
title: Unity용 Remote Rendering 설정
description: Unity 프로젝트에서 Azure 원격 렌더링을 초기화 하는 방법
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a0be44d8709726e159e17e703566c6c576bc18f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018980"
---
# <a name="set-up-remote-rendering-for-unity"></a>Unity용 Remote Rendering 설정

Unity에서 Azure 원격 렌더링 (ARR)을 사용 하도록 설정 하기 위해 몇 가지 Unity 관련 측면을 처리 하는 전용 메서드를 제공 합니다.

## <a name="startup-and-shutdown"></a>시작 및 종료

원격 렌더링을 초기화 하려면를 사용 `RemoteManagerUnity` 합니다. 이 클래스는 제네릭를 호출 `RemoteManager` 하지만 이미 Unity 관련 세부 정보를 구현 합니다. 예를 들어 Unity는 특정 좌표계를 사용 합니다. 를 호출할 때 `RemoteManagerUnity.Initialize` 적절 한 규칙이 설정 됩니다. 또한 호출 하려면 원격으로 렌더링 된 콘텐츠를 표시 하는 데 사용 해야 하는 Unity 카메라를 제공 해야 합니다.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

원격 렌더링을 종료 하려면를 호출 `RemoteManagerStatic.ShutdownRemoteRendering()` 합니다.

를 `AzureSession` 만들고 기본 렌더링 세션으로 선택한 후에는 다음을 사용 하 여 등록 해야 합니다 `RemoteManagerUnity` .

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>전체 예제 코드

아래 코드는 Unity에서 Azure 원격 렌더링을 초기화 하는 데 필요한 모든 단계를 보여 줍니다.

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>편리한 함수

### <a name="session-state-events"></a>세션 상태 이벤트

`RemoteManagerUnity.OnSessionUpdate` 세션 상태가 변경 되는 경우에 대 한 이벤트를 내보냅니다. 자세한 내용은 코드 설명서를 참조 하십시오.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` 는 설치 및 세션 관리를 간소화 하는 선택적 구성 요소입니다. 편집기에서 응용 프로그램을 종료 하거나 재생 모드를 종료할 때 세션을 자동으로 중지 하 고 필요한 경우 세션 임대를 자동으로 갱신 하는 옵션이 포함 되어 있습니다. 세션 속성 (변수 참조)과 같은 데이터를 캐시 `LastProperties` 하 고 세션 상태 변경 및 세션 오류에 대 한 이벤트를 노출 합니다.

한 번에 둘 이상의 인스턴스를 사용할 수 없습니다 `ARRServiceUnity` . 몇 가지 일반적인 기능을 구현 하 여 빠르게 시작할 수 있습니다. 더 큰 응용 프로그램의 경우에는 이러한 작업을 수행 하는 것이 더 적합할 수 있습니다.

을 설정 하 고 사용 하는 방법에 대 한 예제는 `ARRServiceUnity` [자습서: 원격으로 렌더링 된 모델 보기](../../tutorials/unity/view-remote-models/view-remote-models.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Unity용 Remote Rendering 패키지 설치](install-remote-rendering-unity-package.md)
* [자습서: 원격으로 렌더링된 모델 보기](../../tutorials/unity/view-remote-models/view-remote-models.md)

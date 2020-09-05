---
title: 그래픽 바인딩
description: 그래픽 바인딩 및 사용 사례 설정
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: f769036ac9e5a6945e7ecad30e021d377cabd358
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020272"
---
# <a name="graphics-binding"></a>그래픽 바인딩

사용자 지정 애플리케이션에서 Azure Remote Rendering을 사용할 수 있으려면 애플리케이션의 렌더링 파이프라인에 통합되어 있어야 합니다. 이 통합은 그래픽 바인딩의 책임입니다.

일단 설정되면 그래픽 바인딩은 렌더링된 이미지에 영향을 주는 다양한 함수에 대한 액세스를 제공합니다. 이러한 함수는 항상 사용할 수 있는 일반 함수와 선택한 `Microsoft.Azure.RemoteRendering.GraphicsApiType`에만 관련된 특정 함수의 두 가지 범주로 구분할 수 있습니다.

## <a name="graphics-binding-in-unity"></a>Unity의 그래픽 바인딩

Unity에서 전체 바인딩은 `RemoteManagerUnity.InitializeManager`에 전달된 `RemoteUnityClientInit` 구조체에 의해 처리됩니다. 그래픽 모드를 설정하려면 `GraphicsApiType` 필드를 선택한 바인딩으로 설정해야 합니다. XRDevice가 있는지에 따라 필드가 자동으로 채워집니다. 다음 동작을 사용하여 동작을 수동으로 재정의할 수 있습니다.

* **HoloLens 2**: [Windows Mixed Reality](#windows-mixed-reality) 그래픽 바인딩이 항상 사용됩니다.
* **플랫 UWP 데스크톱 앱**: [시뮬레이션](#simulation)이 항상 사용됩니다.
* **Unity 편집기**: WMR VR 헤드셋이 연결되어 있지 않는 한 [시뮬레이션](#simulation)이 항상 사용됩니다. 이 경우 ARR은 애플리케이션의 비 ARR 관련 부분을 디버그할 수 있도록 사용하지 않도록 설정됩니다. 또한 [Holographic Remoting](../how-tos/unity/holographic-remoting.md)을 참조하세요.

Unity와 관련된 유일한 다른 관련 부분은 [기본 바인딩](#access)에 액세스하는 것입니다. 아래의 다른 모든 섹션을 건너뛸 수 있습니다.

## <a name="graphics-binding-setup-in-custom-applications"></a>사용자 지정 애플리케이션의 그래픽 바인딩 설정

그래픽 바인딩을 선택하려면 다음 두 단계를 수행합니다. 먼저 그래픽 바인딩은 프로그램이 초기화될 때 정적으로 초기화되어야 합니다.

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.graphicsApi = GraphicsApiType::WmrD3D11;
managerInit.connectionType = ConnectionType::General;
managerInit.right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering
```

위의 호출은 Azure Remote Rendering을 holographic API로 초기화하는 데 필요합니다. 이 함수는 holographic API를 호출하고 다른 Remote Rendering API에 액세스하기 전에 호출해야 합니다. 마찬가지로 holographic API가 더 이상 호출되지 않은 후에는 해당 de-init 함수 `RemoteManagerStatic.ShutdownRemoteRendering();`을 호출해야 합니다.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">그래픽 바인딩 액세스

클라이언트를 설정하면 기본 그래픽 바인딩에 `AzureSession.GraphicsBinding` getter를 사용하여 액세스할 수 있습니다. 예를 들어 마지막 프레임 통계는 다음과 같이 검색할 수 있습니다.

```cs
AzureSession currentSession = ...;
if (currentSession.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (*binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Graphic API

현재 선택할 수 있는 두 개의 그래픽 API, `WmrD3D11` 및 `SimD3D11`이 있습니다. 세 번째 `Headless`가 있지만 아직 클라이언트 쪽에서 지원되지 않습니다.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`은 HoloLens 2에서 실행되는 기본 바인딩입니다. `GraphicsBindingWmrD3d11` 바인딩이 만들어집니다. 이 모드에서 Azure Remote Rendering은 holographic API에 직접 후크됩니다.

파생 그래픽 바인딩에 액세스하려면 기본 `GraphicsBinding`을 캐스팅해야 합니다.
WMR 바인딩을 사용하려면 다음 두 가지 작업을 수행해야 합니다.

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>사용된 좌표계의 Remote Rendering 알림

```cs
AzureSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (*wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```


위의 `ptr`은 API의 좌표가 표시되는 세계 좌표 시스템을 정의하는 네이티브 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 개체에 대한 포인터여야 합니다.

#### <a name="render-remote-image"></a>원격 이미지 렌더링

각 프레임의 시작 부분에서 원격 프레임을 백 버퍼로 렌더링해야 합니다. 이 작업은 색상 및 깊이 정보를 모두 현재 바인딩된 렌더링 대상으로 채우는 `BlitRemoteFrame`을 호출하여 수행됩니다. 따라서 백 버퍼를 렌더링 대상으로 바인딩한 후 이 작업을 수행하는 것이 중요합니다.

```cs
AzureSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>시뮬레이션

`GraphicsApiType.SimD3D11`은 시뮬레이션 바인딩이며 선택하는 경우 `GraphicsBindingSimD3d11` 그래픽 바인딩을 만듭니다. 이 인터페이스는 데스크톱 애플리케이션에서와 같이 헤드 이동을 시뮬레이션하는 데 사용되며 Monoscopic 이미지를 렌더링합니다.
설정은 약간 더 복잡하며 다음과 같이 작동합니다.

#### <a name="create-proxy-render-target"></a>프록시 렌더링 대상 만들기

`GraphicsBindingSimD3d11.Update` 함수에서 제공하는 프록시 카메라 데이터를 사용하여 원격 및 로컬 콘텐츠를 '프록시'라는 오프스크린 색상/깊이 렌더링 대상으로 렌더링해야 합니다. 프록시가 백 버퍼의 확인 사항과 일치해야 합니다. 세션이 준비되면 연결하기 전에 `GraphicsBindingSimD3d11.InitSimulation`을 호출해야 합니다.

```cs
AzureSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

```cpp
ApiHandle<AzureSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

init 함수에는 네이티브 d3d 디바이스 및 프록시 렌더링 대상의 색상 및 깊이 텍스처에 대한 포인터를 함께 제공해야 합니다. 초기화되면 `AzureSession.ConnectToRuntime` 및 `DisconnectFromRuntime`을 여러 번 호출할 수 있지만 다른 세션으로 전환할 때 다른 세션에서 `GraphicsBindingSimD3d11.InitSimulation`을 호출하려면 먼저 이전 세션에서 `GraphicsBindingSimD3d11.DeinitSimulation`을 먼저 호출해야 합니다.

#### <a name="render-loop-update"></a>렌더링 루프 업데이트

렌더링 루프 업데이트는 여러 단계로 구성됩니다.

1. 렌더링을 수행하기 전에 각 프레임은 렌더링 되는 서버에 전송되는 현재 카메라 변환으로 `GraphicsBindingSimD3d11.Update`가 호출됩니다. 동시에 반환된 프록시 변환이 프록시 카메라에 적용되어 프록시 렌더링 대상으로 렌더링되어야 합니다.
반환된 프록시 업데이트 `SimulationUpdate.frameId`가 null이면 아직 원격 데이터가 없습니다. 이 경우 프록시 렌더링 대상으로 렌더링하는 대신, 현재 카메라 데이터를 사용하여 로컬 콘텐츠를 백 버퍼에 직접 렌더링하고 다음 두 단계를 건너뜁니다.
1. 애플리케이션은 이제 프록시 렌더링 대상을 바인딩하고 `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`를 호출해야 합니다. 그러면 원격 색상 및 깊이 정보가 프록시 렌더링 대상에 채워집니다. 이제 프록시 카메라 변환을 사용하여 모든 로컬 콘텐츠를 프록시에 렌더링할 수 있습니다.
1. 그런 다음 백 버퍼는 렌더링 대상으로 바인딩되어야 하고 백 버퍼를 표시할 수 있는 지점에서 `GraphicsBindingSimD3d11.ReprojectProxy`가 호출됩니다.

```cs
AzureSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<AzureSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdate update;
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate;
simBinding->Update(update, &proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>다음 단계

* [자습서: 원격으로 렌더링된 모델 보기](../tutorials/unity/view-remote-models/view-remote-models.md)

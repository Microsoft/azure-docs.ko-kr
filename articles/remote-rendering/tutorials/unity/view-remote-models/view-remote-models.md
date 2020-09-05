---
title: 원격으로 렌더링된 모델 보기
description: Azure Remote Rendering의 "Hello World" 자습서에서는 Azure에서 원격으로 렌더링된 모델을 보는 방법을 보여 줍니다.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 7509a17127f04220a8e8450a81627354b28bdacd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006468"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>자습서: 원격으로 렌더링된 모델 보기

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * ARR(Azure Remote Rendering) 인스턴스 프로비저닝
> * 렌더링 세션 만들기 및 중지
> * 기존 렌더링 세션 다시 사용
> * 세션에서 연결 및 연결 끊기
> * 렌더링 세션에 모델 로드

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에는 다음이 필요합니다.

* 활성 종량제 Azure 구독([계정 만들기](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/))
* Windows SDK 10.0.18362.0[(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 최신 버전의 Visual Studio 2019[(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT([다운로드](https://git-scm.com/downloads))
* 최신 버전(2019.3)의 Unity - Unity Hub를 이 구성 요소에 사용하는 것이 좋음[(다운로드)](https://unity3d.com/get-unity/download)
  * 다음 모듈을 Unity에 설치합니다.
    * **UWP** - 유니버설 Windows 플랫폼 빌드 지원
    * **IL2CPP** - Windows 빌드 지원(IL2CPP)
* Unity 및 C# 언어에 대한 중급 수준의 지식(예: 스크립트 및 개체 만들기, prefab 사용, Unity 이벤트 구성 등)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>ARR(Azure Remote Rendering) 인스턴스 프로비저닝

Azure Remote Rendering 서비스에 액세스하려면 먼저 [계정을 만들어야 합니다](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>새 Unity 프로젝트 만들기

> [!TIP]
> [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에는 모든 자습서가 완료된 프로젝트가 포함되어 있으며 참조로 사용할 수 있습니다. 전체 Unity 프로젝트는 *Unity\Tutorial-Complete*에서 확인하세요.

Unity Hub에서 새 프로젝트를 만듭니다.
다음 예에서는 프로젝트가 **RemoteRendering**이라는 폴더에 만들어진다고 가정합니다.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="새 Unity 프로젝트":::

## <a name="include-the-azure-remote-rendering-package"></a>Azure Remote Rendering 패키지 포함

Unity 프로젝트 폴더에 있는 `Packages/manifest.json` 파일을 수정해야 합니다. 텍스트 편집기에서 파일을 열고, 다음 줄을 매니페스트의 위쪽에 추가합니다.

```json
{
    "scopedRegistries": [
    {
        "name": "Azure Mixed Reality Services",
        "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
        "scopes": ["com.microsoft.azure"]
    }
    ],
    "dependencies": {
        "com.unity.render-pipelines.universal": "7.3.1",
        "com.microsoft.azure.remote-rendering": "0.1.31",
        ...existing dependencies...
    }
}
```

매니페스트가 수정되어 저장되면 Unity에서 자동으로 새로 고칩니다. *Project(프로젝트)* 창에서 패키지가 로드되었는지 확인합니다.

:::image type="content" source="./media/confirm-packages.png" alt-text="패키지 가져오기 확인":::

패키지가 로드되지 않으면 Unity 콘솔에서 오류가 있는지 확인합니다. 오류가 없지만 패키지가 여전히 **Packages** 폴더 아래에 표시되지 않으면 패키지 표시 유형 설정/해제 단추를 선택합니다.
![Unity 카메라 속성](./media/unity-package-visibility.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>최신 버전의 패키지가 있는지 확인

다음 단계에서는 프로젝트에서 최신 버전의 원격 렌더링 패키지를 사용하는지 확인합니다.

1. Unity 편집기의 위쪽 메뉴에서 *Window(창) -> Package Manager(패키지 관리자)* 를 차례로 엽니다.
1. **Microsoft Azure Remote Rendering** 패키지를 선택합니다.
1. **Microsoft Azure Remote Rendering** 패키지에 대한 패키지 관리자 페이지에서 **Update(업데이트)** 단추를 사용할 수 있는지 확인합니다. 사용할 수 있는 경우 해당 패키지를 클릭하여 패키지를 사용 가능한 최신 버전으로 업데이트합니다.
![패키지 관리자의 ARR 패키지](./media/package-manager.png)
1. 패키지를 업데이트하면 경우에 따라 콘솔 오류가 발생할 수 있습니다. 이 경우 프로젝트를 닫고 다시 열어 보세요.
1. 패키지가 최신 상태이면 Package Manager에서 Update 단추 대신 **Up to date(최신)** 를 표시합니다.
![최신 패키지](./media/package-up-to-date.png)
## <a name="configure-the-camera"></a>카메라 구성

1. **Main Camera(기본 카메라)** 노드를 선택합니다.

1. 마우스 오른쪽 단추로 *Transform(변환)* 구성 요소를 클릭하여 상황에 맞는 메뉴를 열고, **Reset(다시 설정)** 옵션을 선택합니다.

    ![카메라 변환 다시 설정](./media/camera-reset-transform.png)

1. **Clear flags(플래그 지우기)** 를 *Solid Color(단색)* 로 설정합니다.

1. 완전 투명(0) 알파(A)를 사용하여 **Background(배경)** 를 *Black(검은색)* (#000000)으로 설정합니다.

    ![색상표](./media/color-wheel-black.png)

1. **Clipping Planes(클리핑 거리)** 를 *Near(근거리) = 0.3* 및 *Far(원거리) = 20*으로 설정합니다. 즉, 렌더링에서 30cm보다 더 가깝거나 20m보다 더 먼 기하 도형을 클리핑합니다.

    ![Unity 카메라 속성](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>프로젝트 설정 조정

1. *Edit(편집) >Project Settings...(프로젝트 설정...)* 를 차례로 엽니다.
1. 왼쪽 목록 메뉴에서 **Quality(품질)** 를 선택합니다.
1. 모든 플랫폼의 **Default Quality Level(기본 품질 수준)** 을 *Low(낮음)* 로 변경합니다. 이 설정을 사용하면 로컬 콘텐츠를 더 효율적으로 렌더링할 수 있으며, 원격으로 렌더링된 콘텐츠의 품질에는 영향을 주지 않습니다.

    ![프로젝트 품질 설정 변경](./media/settings-quality.png)

1. 왼쪽 목록 메뉴에서 **Graphics(그래픽)** 를 선택합니다.
1. **Scriptable Rendering Pipeline(스크립트 가능한 렌더링 파이프라인)** 설정을 *HybridRenderingPipeline*으로 변경합니다.
    ![프로젝트 그래픽 설정 변경](./media/settings-graphics-render-pipeline.png)\
    경우에 따라 UI는 패키지에서 사용 가능한 파이프라인 유형 목록을 채우지 않습니다. 이 경우 *HybridRenderingPipeline* 자산을 필드로 수동으로 끌어야 합니다.
    ![프로젝트 그래픽 설정 변경](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > *HybridRenderingPipeline* 자산을 렌더링 파이프라인 자산 필드로 끌어서 놓을 수 없는 경우(필드가 없기 때문일 수도 있음) `com.unity.render-pipelines.universal` 패키지가 패키지 구성에 포함되어 있는지 확인하세요.

1. 왼쪽 목록 메뉴에서 **Player(플레이어)** 를 선택합니다.
1. Windows 아이콘으로 표시되는 **Universal Windows Platform settings(유니버설 Windows 플랫폼 설정)** 탭을 선택합니다.
1. 아래와 같이 Windows Mixed Reality를 지원하도록 **XR Settings(XR 설정)** 를 변경합니다.
    1. **Virtual Reality Supported(가상 현실 지원됨)** 를 사용하도록 설정합니다.
    1. '+' 단추를 누르고, **Windows Mixed Reality**를 추가합니다.
    1. **Depth Format(깊이 형식)** 을 *16-Bit Depth(16비트 깊이)* 로 설정합니다.
    1. **Depth Buffer Sharing(깊이 버퍼 공유)** 을 사용하도록 설정되었는지 확인합니다.
    1. **Stereo Rendering Mode(스테레오 렌더링 모드)** 를 *Single Pass Instanced(단일 패스 인스턴스화됨)* 로 설정합니다.

    ![플레이어 설정](./media/xr-player-settings.png)

1. 동일한 창에서 **XR Settings** 위에 있는 **Publishing Settings(게시 설정)** 를 펼칩니다.
1. **Capabilities(기능)** 까지 아래로 스크롤하여 다음을 선택합니다.
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer**(*선택 사항*). Unity 원격 디버거를 디바이스에 연결하려면 이 옵션을 선택합니다.

1. **Supported Device Families(지원되는 디바이스 제품군)** 아래에서 **Holographic(홀로그램)** 및 **Desktop(데스크톱)** 을 사용하도록 설정합니다.
1. **Project Settings(프로젝트 설정)** 패널을 닫거나 도킹합니다.
1. *File(파일) -> Build Settings(빌드 설정)* 를 차례로 엽니다.
1. **Universal Windows Platform(유니버설 Windows 플랫폼)** 을 선택합니다.
1. 아래에 있는 설정과 일치하도록 설정을 구성합니다.
1. **Switch Platform(플랫폼 전환)** 단추를 누릅니다.
![빌드 설정](./media/build-settings.png)
1. Unity에서 플랫폼이 변경되면 빌드 패널을 닫습니다.

## <a name="validate-project-setup"></a>프로젝트 설정 유효성 검사

프로젝트 설정이 올바른지 확인하려면 다음 단계를 수행합니다.

1. Unity 편집기 도구 모음의 **RemoteRendering** 메뉴에서 **ValidateProject** 항목을 선택합니다.
1. **ValidateProject** 창에서 오류를 검토하고 필요한 경우 프로젝트 설정을 수정합니다.

    ![Unity 편집기 프로젝트 유효성 검사](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Azure Remote Rendering 연결 및 상태를 조정하는 스크립트 만들기

원격으로 렌더링된 모델을 표시하는 4가지 기본 단계가 있으며, 아래 순서도에서 설명하고 있습니다. 각 단계는 순서대로 수행해야 합니다. 다음 단계는 애플리케이션 상태를 관리하고 필요한 각 단계를 진행하는 스크립트를 만드는 것입니다.

![ARR 스택 0](./media/remote-render-stack-0.png)

1. *Project(프로젝트)* 창의 **Assets(자산)** 아래에서 *RemoteRenderingCore*라는 새 폴더를 만듭니다. 그런 다음, *RemoteRenderingCore* 내에서 *Scripts*라는 다른 폴더를 만듭니다.

1. **RemoteRenderingCoordinator**라는 [새 C# 스크립트](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)를 만듭니다.
프로젝트는 다음과 같습니다.

    ![프로젝트 계층 구조](./media/project-structure.png)

    이 코디네이터 스크립트는 원격 렌더링 상태를 추적하고 관리합니다. 참고로, 이 코드 중 일부는 상태를 유지 관리하고, 기능을 다른 구성 요소에 공개하며, 이벤트를 트리거하고, Azure Remote Rendering과 *직접* 관련되지 않은 애플리케이션 특정 데이터를 저장하는 데 사용됩니다. 아래 코드를 시작 지점으로 사용하며, 자습서의 뒷부분에서는 특정 Azure Remote Rendering 코드를 처리하고 구현합니다.

1. 코드 편집기에서 **RemoteRenderingCoordinator**를 열고, 전체 내용을 아래 코드로 바꿉니다.

```csharp
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<AzureFrontendAccountInfo> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.Log($"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static AzureSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<AzureFrontendAccountInfo> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        var allSessions = await ARRSessionService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();
        return allSessions.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Actions?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        var properties = await session.GetPropertiesAsync().AsTask();

        switch (properties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Azure Remote Rendering GameObject 만들기

원격 렌더링 코디네이터 및 해당 필수 스크립트(*ARRServiceUnity*)는 모두 장면에서 GameObject에 연결해야 하는 MonoBehaviour입니다. 원격 세션에 연결하고 관리하는 ARR의 많은 기능을 공개하기 위해 ARR에서 *ARRServiceUnity* 스크립트를 제공합니다.

1. 장면에서 새 GameObject를 만들고(Ctrl+Shift+N 또는 *GameObject -> Create Empty(빈 항목 만들기)* ), 이름을 **RemoteRenderingCoordinator**로 지정합니다.
1. *RemoteRenderingCoordinator* 스크립트를 **RemoteRenderingCoordinator** GameObject에 추가합니다.
![RemoteRenderingCoordinator 구성 요소 추가](./media/add-coordinator-script.png)
1. 검사기에서 *Service(서비스)* 로 표시되는 *ARRServiceUnity* 스크립트가 GameObject에 자동으로 추가되는지 확인합니다. 이는 **RemoteRenderingCoordinator** 스크립트의 위쪽에 `[RequireComponent(typeof(ARRServiceUnity))]`가 있는 결과입니다.
1. Azure Remote Rendering 자격 증명 및 계정 도메인을 코디네이터 스크립트에 추가합니다.
![자격 증명 추가](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Azure Remote Rendering 초기화

이제 코디네이터에 대한 프레임워크가 있으므로 **Remote Rendering 초기화**를 시작하는 4가지 단계를 각각 구현합니다.

![ARR 스택 1](./media/remote-render-stack-1.png)

**초기화**는 렌더링에 사용할 카메라 개체를 Azure Remote Rendering에 알리고 상태 시스템을 **NotAuthorized**로 진행합니다. 즉, 초기화되었지만 아직 세션에 연결할 수 있는 권한이 없습니다. ARR 세션을 시작하면 비용이 발생하므로 사용자에게 계속 진행할지를 확인해야 합니다.

**NotAuthorized** 상태가 되면 **CheckAuthorization**이 호출됩니다. 이 경우 **RequestingAuthorization** 이벤트를 호출하고 사용할 계정 자격 증명을 결정합니다(**AccountInfo**는 클래스의 위쪽 근처에 정의되어 있으며, 위의 단계에서 Unity 검사기를 통해 정의한 자격 증명을 사용함).

   > [!NOTE]
   > 런타임 재컴파일은 ARR에서 지원하지 않습니다. 재생 모드가 활성화된 상태에서 스크립트를 수정하고 저장하면 Unity가 중지되고 작업 관리자를 통해 강제로 종료해야 할 수 있습니다. 스크립트를 편집하기 전에 항상 재생 모드를 중지했는지 확인하세요.

1. **InitializeARR** 및 **InitializeSessionService**의 내용을 아래의 완성된 코드로 바꿉니다.

 ```csharp
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

**NotAuthorized**에서 **NoSession**으로 진행하기 위해 일반적으로 사용자가 선택할 수 있도록 모달 대화 상자가 제공됩니다(다른 챕터에서만 수행할 수 있음). 지금은 **RequestingAuthorization** 이벤트가 트리거되는 즉시 **ByPassAuthentication**을 호출하여 권한 부여 확인을 자동으로 무시합니다.

1. **RemoteRenderingCoordinator** GameObject를 선택하고, **RemoteRenderingCoordinator** 구성 요소의 검사기에 공개되는 **OnRequestingAuthorization** Unity 이벤트를 찾습니다.

1. 오른쪽 아래에서 '+'를 눌러 새 이벤트를 추가합니다.
1. 직접 참조할 수 있도록 구성 요소를 자체 이벤트로 끕니다.
![인증 무시](./media/bypass-authorization-add-event.png)\
1. 드롭다운에서 **RemoteRenderingCoordinator -> BypassAuthorization**을 차례로 선택합니다.
![인증 무시](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>원격 세션 만들기 또는 조인

두 번째 단계는 Remote Rendering 세션을 만들거나 조인하는 것입니다(자세한 내용은 [Remote Rendering 세션](../../../concepts/sessions.md) 참조).

![ARR 스택 2](./media/remote-render-stack-2.png)

원격 세션은 모델을 렌더링하는 위치입니다. **JoinRemoteSession( )** 메서드는 **LastUsedSessionID** 속성으로 추적되거나, **SessionIDOverride**에 할당된 활성 세션 ID가 있는 경우 기존 세션에 조인하려고 시도합니다. **SessionIDOverride**는 디버깅 목적으로만 사용되며, 세션이 있음을 알고 있고 명시적으로 연결하려는 경우에만 사용해야 합니다.

사용 가능한 세션이 없으면 새 세션이 만들어집니다. 그러나 새 세션을 만드는 경우 시간이 많이 걸립니다. 따라서 필요한 경우에만 세션을 만들고, 가능할 때마다 언제든지 다시 사용해야 합니다(세션을 관리하는 방법에 대한 자세한 내용은 [상용 준비: 세션 풀링, 예약 및 모범 사례](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) 참조).

> [!TIP]
> **StopRemoteSession()** 은 활성 세션을 종료합니다. 불필요한 요금이 청구되지 않도록 방지하려면 더 이상 필요하지 않은 세션을 항상 중지해야 합니다.

이제 상태 시스템에서 사용 가능한 세션에 따라 **ConnectingToNewRemoteSession** 또는 **ConnectingToExistingRemoteSession**으로 진행합니다. 기존 세션을 열거나 새 세션을 만드는 경우 모두 **ARRSessionService.OnSessionStatusChanged** 이벤트가 트리거되어 **OnRemoteSessionStatusChanged** 메서드를 실행합니다. 이 경우 상태 시스템을 **RemoteSessionReady**로 진행시키는 것이 좋습니다.

1. 새 세션에 조인하려면 코드를 수정하여 **JoinRemoteSession( )** 및 **StopRemoteSession( )** 메서드를 아래의 완성된 예제로 바꿉니다.

```csharp
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationParams(renderingSessionVmSize, maxLeaseHours, maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

세션을 다시 사용하여 시간을 절약하려면 *ARRServiceUnity* 구성 요소에서 **Auto-Stop Session(세션 자동 중지)** 옵션을 비활성화해야 합니다. 이렇게 하면 세션이 연결되지 않은 경우에도 계속 실행됩니다. 세션은 서버에서 종료되기 전에 *MaxLeaseTime*만큼 오래 실행될 수 있습니다(*MaxLeaseTime* 값은 원격 렌더링 코디네이터의 *New Session Defaults(새 세션 기본값)* 아래에서 수정할 수 있음). 반면 연결을 끊을 때 모든 세션을 자동으로 종료하는 경우 매번 새 세션이 시작될 때까지 기다려야 하므로 다소 시간이 걸릴 수 있습니다.

> [!NOTE]
> 세션 중지는 즉시 적용되며, 실행 취소할 수 없습니다. 중지되면 동일한 시작 오버헤드를 사용하여 새 세션을 만들어야 합니다.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>원격 세션에 로컬 런타임 연결

다음으로, 애플리케이션에서 로컬 런타임을 원격 세션에 연결해야 합니다.

![ARR 스택 3](./media/remote-render-stack-3.png)

또한 애플리케이션에서 런타임과 현재 세션 간의 연결에 대한 이벤트를 수신 대기해야 합니다. 이러한 상태 변경은 **OnLocalRuntimeStatusChanged**에서 처리됩니다. 이 코드는 상태를 **ConnectingToRuntime**으로 진행시킵니다. **OnLocalRuntimeStatusChanged**에 연결되면 상태가 **RuntimeConnected**로 진행됩니다. 런타임에 연결하는 것은 코디네이터에서 관심을 갖는 마지막 상태입니다. 즉, 애플리케이션이 모든 공통 구성을 통해 수행되고, 모델을 로드하고 렌더링하는 세션 관련 작업을 시작할 준비가 되었다는 것입니다.

 1. **ConnectRuntimeToRemoteSession( )** 및 **DisconnectRuntimeFromRemoteSession( )** 메서드를 아래의 완성된 버전으로 바꿉니다.
 1. **LateUpdate** Unity 메서드를 적어 두고, 현재 활성 세션을 업데이트해야 합니다. 이렇게 하면 현재 세션에서 메시지를 보내거나 받고, 프레임 버퍼를 원격 세션에서 받은 프레임으로 업데이트할 수 있습니다. 이는 ARR이 제대로 작동하는 데 매우 중요합니다.

```csharp
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectToRuntime(new ConnectToRuntimeParams());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.DisconnectFromRuntime();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Actions.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Actions?.Update();
}
```

> [!NOTE]
> 로컬 런타임을 원격 세션에 연결하는 것은 현재 활성 세션에서 호출되는 **Update(업데이트)** 에 따라 달라집니다. 애플리케이션이 **ConnectingToRuntime** 상태 이후로 진행되지 않는 경우 활성 세션에서 정기적으로 **Update**를 호출하고 있는지 확인하세요.

## <a name="load-a-model"></a>모델 로드

필수 기본 요소가 제대로 갖추어지면 모델을 원격 세션에 로드하고 프레임 수신을 시작할 준비가 되었습니다.

![ARR 스택 4](./media/remote-render-stack-4.png)

**LoadModel** 메서드는 모델 경로, 진행률 처리기 및 부모 변환을 허용하도록 설계되었습니다. 이러한 인수는 모델을 원격 세션에 로드하고, 로드 진행 시 사용자를 업데이트하며, 부모 변환에 따라 원격으로 렌더링된 모델의 방향을 지정하는 데 사용됩니다.

1. **LoadModel** 메서드를 아래 코드로 완전히 바꿉니다.

    ```csharp
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        if(progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

위의 코드에서 수행하는 단계는 다음과 같습니다.

1. [원격 엔터티](../../../concepts/entities.md)를 만듭니다.
1. 원격 엔터티를 나타낼 로컬 GameObject를 만듭니다.
1. 로컬 GameObject에서 해당 상태(즉, Transform(변환))를 모든 프레임의 원격 엔터티와 동기화하도록 구성합니다.
1. 이름을 설정하고, 안정화를 지원하기 위해 [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html)를 추가합니다.
1. 모델 데이터를 Blob Storage에서 원격 엔터티로 로드합니다.
1. 나중에 참조할 수 있도록 부모 Entity를 반환합니다.

## <a name="view-the-test-model"></a>테스트 모델 보기

이제 원격으로 렌더링된 모델을 보는 데 필요한 모든 코드가 준비되었으며, 원격 렌더링에 필요한 4가지 단계가 모두 완료되었습니다. 이제는 모델 로드 프로세스를 시작하는 약간의 코드를 추가해야 합니다.

![ARR 스택 4](./media/remote-render-stack-5.png)

1. 다음 코드를 **LoadModel** 메서드 바로 아래의 **RemoteRenderingCoordinator** 클래스에 추가합니다.

    ```csharp
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    이 코드는 테스트 모델의 부모로 작동하는 GameObject를 만듭니다. 그런 다음, **LoadModel** 메서드를 호출하여 렌더링 테스트를 위해 Azure Remote Rendering에 기본 제공되는 자산인 "builtin://Engine" 모델을 로드합니다.

1. 코드를 저장합니다.
1. Unity 편집기에서 Play(재생) 단추를 눌러 Azure Remote Rendering에 연결하고 새 세션을 만드는 프로세스를 시작합니다.
1. 게임 보기에는 거의 표시되지 않지만, 콘솔에는 애플리케이션의 변경 상태가 표시됩니다. `ConnectingToNewRemoteSession`으로 진행되며 최대 5분 정도 지속될 수 있습니다.
1. **RemoteRenderingCoordinator** GameObject를 선택하여 검사기에서 연결된 해당 스크립트를 확인합니다. 초기화 및 연결 단계를 진행하면서 **Service(서비스)** 구성 요소 업데이트를 확인합니다.
1. 콘솔 출력 모니터링 - 상태가 **RuntimeConnected**로 변경될 때까지 기다립니다.
1. 런타임이 연결되면 검사기에서 마우스 오른쪽 단추로 **RemoteRenderingCoordinator**를 클릭하여 상황에 맞는 메뉴를 표시합니다. 그런 다음, 상황에 맞는 메뉴에서 위 코드의 `[ContextMenu("Load Test Model")]` 부분에 추가된 **Load Test Model(테스트 모델 로드)** 옵션을 클릭합니다.

    ![상황에 맞는 메뉴에서 로드](./media/load-test-model.png)

1. 콘솔에서 **LoadModel** 메서드에 전달된 **ProgressHandler**의 출력을 확인합니다.
1. 원격으로 렌더링된 모델이 표시됩니다!

> [!NOTE]
> 원격 모델은 장면 보기에 표시되지 않고 게임 보기에만 표시됩니다. 이는 ARR에서 특히 게임 보기 카메라의 관점에서 프레임을 원격으로 렌더링하며 편집기 카메라를 인식하지 못하기 때문입니다(장면 보기를 렌더링하는 데 사용됨).

## <a name="next-steps"></a>다음 단계

![로드된 모델](./media/test-model-rendered.png)

축하합니다! Azure Remote Rendering을 사용하여 원격으로 렌더링된 모델을 볼 수 있는 기본 애플리케이션을 만들었습니다. 다음 자습서에서는 MRTK를 통합하고 고유한 모델을 가져옵니다.

> [!div class="nextstepaction"]
> [다음: 인터페이스 및 사용자 지정 모델](../custom-models/custom-models.md)

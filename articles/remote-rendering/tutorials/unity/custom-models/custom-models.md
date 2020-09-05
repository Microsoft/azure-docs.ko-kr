---
title: 인터페이스 및 사용자 지정 모델
description: Azure Remote Rendering에서 렌더링할 보기 컨트롤러 추가 및 사용자 지정 모델 수집
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: ebadaf51a7dfbb286dac0bbdb0c3c8437ae2356f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022227"
---
# <a name="tutorial-interfaces-and-custom-models"></a>자습서: 인터페이스 및 사용자 지정 모델

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 프로젝트에 Mixed Reality Toolkit 추가
> * 모델 상태 관리
> * 모델 수집을 위한 Azure Blob Storage 구성
> * 렌더링할 모델 업로드 및 처리

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서는 [자습서: 원격으로 렌더링된 모델 보기](../view-remote-models/view-remote-models.md)를 기반으로 합니다.

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>MRTK(Mixed Reality Toolkit) 시작

MRTK(Mixed Reality Toolkit)는 혼합 현실 환경을 빌드하는 데 사용되는 플랫폼 간 도구 키트입니다. 이 자습서에서는 MRTK 2.3의 상호 작용 및 시각화 기능을 사용합니다.

MRTK를 추가하려면 [MRTK 시작](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html)에 나열된 [필수 단계](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#required)를 따릅니다.

이러한 단계는 다음과 같습니다.
 - [최신 MRTK Unity 패키지 가져오기](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#get-the-latest-mrtk-unity-packages)
     - 여기서 말하는 "최신"은 버전 2.3입니다.
     - 이 자습서에서는 *Foundation* 패키지만 사용합니다. *확장*, *도구* 및 *예제* 패키지는 필요 없습니다.
 - [Unity 프로젝트로 MRTK 패키지 가져오기](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#import-mrtk-packages-into-your-unity-project)
 - [Unity 프로젝트를 대상 플랫폼으로 전환](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#switch-your-unity-project-to-the-target-platform)
     - 첫 번째 챕터에서 이 단계를 이미 완료했겠지만, 지금 다시 한 번 확인하는 것이 좋습니다.
 - [새 장면 또는 새 프로젝트에 MRTK 추가](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/Documentation/GettingStartedWithTheMRTK.html#add-mrtk-to-a-new-scene-or-new-project)
     - 새 장면에 MRTK를 추가하고 코디네이터 및 모델 개체/스크립트를 다시 추가할 수도 있고, *Mixed Reality Toolkit -> 장면에 추가 및 구성* 메뉴 명령을 사용하여 기존 장면에 MRTK를 추가할 수도 있습니다.

## <a name="import-assets-used-by-this-tutorial"></a>이 자습서에서 사용하는 자산 가져오기

이 챕터부터 대부분의 재질에 사용되는 간단한 [모델-보기-컨트롤러 패턴](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)을 구현합니다. 패턴의 *모델* 파트는 Azure Remote Rendering 관련 코드 및 Azure Remote Rendering과 관련된 상태 관리입니다. 패턴의 *보기* 및 *컨트롤러* 파트는 MRTK 자산과 사용자 지정 스크립트를 사용하여 구현됩니다. 여기서 구현하는 *보기 컨트롤러* 없이 이 자습서의 *모델*을 사용할 수도 있습니다. 이렇게 분리하면 이 자습서의 코드를 개발자의 애플리케이션에 쉽게 통합할 수 있습니다. 그러면 애플리케이션이 디자인 패턴의 *보기 컨트롤러* 파트를 넘겨받습니다.

MRTK가 도입되면서, 이제 여러 스크립트, prefab 및 자산을 프로젝트에 추가하여 상호 작용 및 시각적 피드백을 지원할 수 있습니다. **자습서 자산**이라고 하는 이러한 자산은 [Unity 자산 패키지](https://docs.unity3d.com/Manual/AssetPackages.html)에 번들로 제공되며, '\Unity\TutorialAssets\TutorialAssets.unitypackage'의 [Azure Remote Rendering GitHub](https://github.com/Azure/azure-remote-rendering)에 포함되어 있습니다.

1. 다운로드하면 알려진 위치에 Zip 파일의 압축이 풀리는 경우 [Azure Remote Rendering](https://github.com/Azure/azure-remote-rendering) git 리포지토리를 복제하거나 다운로드합니다.
1. Unity 프로젝트에서 *자산 -> 패키지 가져오기 -> 사용자 지정 패키지*를 선택합니다.
1. 파일 탐색기에서 Azure Remote Rendering 리포지토리를 복제하거나 압축을 푼 디렉터리로 이동한 다음, **Unity-> TutorialAssets-> TutorialAssets.unitypackage**에 있는 .unitypackage를 선택합니다.
1. **가져오기** 단추를 선택하여 패키지의 내용을 프로젝트로 가져옵니다.
1. Unity 편집기의 위쪽 메뉴 모음에서 *Mixed Reality Toolkit -> 유틸리티 -> Upgrade MRTK Standard Shader for Lightweight Render Pipeline(경량 렌더링 파이프라인에 대한 MRTK 표준 셰이더 업그레이드)* 를 선택하고, 표시되는 메시지에 따라 셰이더를 업그레이드합니다.

MRTK 및 자습서 자산이 프로젝트에 포함된 후에는 MRTK 프로필을 자습서에 적합한 프로필로 전환합니다.

1. 장면 계층 구조에서 **MixedRealityToolkit** GameObject를 선택합니다.
1. 검사기의 **MixedRealityToolkit** 구성 요소 아래에서 구성 프로필을 *ARRMixedRealityToolkitConfigurationProfile*로 전환합니다.
1. *Ctrl+S*를 눌러 변경 내용을 저장합니다.

이렇게 하면 주로 기본 HoloLens 2 프로필을 사용하여 MRTK가 구성됩니다. 제공된 프로필은 다음과 같은 방식으로 미리 구성되어 있습니다.
 - 프로파일러를 끕니다(9를 눌러 프로파일러 켜기/끄기 또는 디바이스에서는 음성으로 "프로파일러 표시/숨기기"라고 말하기).
 - 시선 응시 커서를 끕니다.
 - Unity 마우스 클릭을 사용하도록 설정합니다. 이렇게 하면 시뮬레이션된 손 대신 마우스를 사용하여 MRTK UI 요소를 클릭할 수 있습니다.

## <a name="add-the-app-menu"></a>앱 메뉴 추가

이 자습서에 나오는 보기 컨트롤러는 대부분 구체적 클래스 대신 추상 기본 클래스에 대해 작동합니다. 이 패턴은 Azure Remote Rendering에 대해 학습할 수 있도록 도와주면서도 더 많은 유연성을 제공하고 보기 컨트롤러를 자동으로 제공할 수 있습니다. 편의상 **RemoteRenderingCoordinator** 클래스에는 추상 클래스가 없으며 해당 보기 컨트롤러는 구체적 클래스에 대해 직접 작동합니다.

이제 장면에 prefab **AppMenu**를 추가하여 현재 세션 상태에 대한 시각적 피드백을 받을 수 있습니다. 더 많은 ARR 기능을 구현하여 장면에 통합할수록 이 보기 컨트롤러는 더 많은 하위 메뉴 보기 컨트롤러를 "잠금 해제"합니다. 현재 **AppMenu**는 ARR 상태를 시각적으로 표시하고, ARR에 연결할 수 있도록 사용자가 애플리케이션에 권한을 부여하는 데 사용되는 모달 패널을 제공합니다.

1. *Assets/RemoteRenderingTutorial/Prefabs/AppMenu*에서 **AppMenu** prefab을 찾습니다.
1. **AppMenu** prefab을 장면으로 끌어 놓습니다.
1. 처음으로 *Text Mesh Pro* 자산을 장면에 포함하는 것이므로 **TMP Importer** 대화 상자가 표시됩니다. 표시되는 메시지에 따라 **TMP Essentials 가져오기**를 수행합니다. 그런 다음, 가져오기 대화 상자를 닫습니다. 예 및 추가 기능은 필요 없습니다.
1. **AppMenu**는 세션에 연결하는 것에 동의하도록 자동으로 연결되어 모달을 제공하도록 구성되어 있으므로, 이전에 배치된 바이패스를 제거할 수 있습니다. **RemoteRenderingCoordinator** GameObject의 **On Requesting Authorization** 이벤트에서 '-' 단추를 눌러 이전에 구현한 권한 부여에 대한 바이패스를 제거합니다.
 ![바이패스 제거](./media/remove-bypass-event.png).
1. Unity 편집기에서 **재생**을 눌러 보기 컨트롤러를 테스트합니다.
1. MRTK가 구성되었으므로, 이제 편집기에서 WASD 키를 사용하여 보기 위치를 변경하고 마우스 오른쪽 단추를 누른 채로 마우스를 이동하여 보기 방향을 변경할 수 있습니다. 장면 주위를 약간 "이동"하여 컨트롤의 느낌을 살펴보세요.
1. 디바이스에서 손바닥을 들어올려 **AppMenu**를 소환할 수 있습니다. Unity 편집기에서는 바로 가기 키 'M'을 사용합니다.
1. 메뉴가 보이지 않으면 'M' 키를 눌러 메뉴를 소환합니다. 메뉴는 쉽게 조작할 수 있도록 카메라 근처에 배치됩니다.
1. 이제 권한 부여가 **AppMenu** 오른쪽에 요청으로 표시됩니다. 이제부터 이것을 사용하여 앱에서 원격 렌더링 세션을 관리하도록 권한을 부여합니다.
 ![UI 권한 부여](./media/authorize-request-ui.png)
1. Unity 재생을 중지하고 자습서를 계속 진행합니다.

## <a name="manage-model-state"></a>모델 상태 관리

이제 상태를 추적하고, 이벤트에 응답하고, 이벤트를 실행하고, 구성하기 위한 새 스크립트 **RemoteRenderedModel**을 구현하겠습니다. 기본적으로 **RemoteRenderedModel**은 모델 데이터의 원격 경로를 `modelPath`에 저장합니다. **RemoteRenderingCoordinator**에서 상태 변경을 수신 대기하면서 정의하는 모델을 자동으로 로드 또는 언로드해야 하는지 확인합니다. **RemoteRenderedModel**이 연결된 GameObject는 원격 콘텐츠의 로컬 부모가 됩니다.

**RemoteRenderedModel** 스크립트는 **자습서 자산**에 포함된 **BaseRemoteRenderedModel**을 구현합니다. 그러면 원격 모델 보기 컨트롤러를 스크립트에 바인딩할 수 있습니다.

1. **RemoteRenderingCoordinator**와 동일한 폴더에 **RemoteRenderedModel**이라는 새 스크립트를 만듭니다. 전체 내용을 다음 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

간단하게 말해서, **RemoteRenderedModel**은 모델을 로드하는 데 필요한 데이터(여기서는 SAS 또는 *builtin://* URI)를 보유하고 원격 모델 상태를 추적합니다. 로드할 시간이 되면 **RemoteRenderingCoordinator**에서 `LoadModel` 메서드가 호출되고, 모델을 포함하는 엔터티가 참조 및 언로드에 대해 반환됩니다.

## <a name="load-the-test-model"></a>테스트 모델 로드

테스트 모델을 다시 로드하여 새 스크립트를 테스트하겠습니다. 스크립트를 포함하고 테스트 모델의 부모가 될 게임 개체를 만듭니다.

1. 장면에서 빈 게임 개체를 새로 만들고 이름을 **TestModel**로 지정합니다.
1. **TestModel**에 *RemoteRenderedModel* 스크립트를 추가합니다.
![RemoteRenderedModel 구성 요소 추가](./media/add-remote-rendered-model-script.png)
1. `Model Display Name` 및 `Model Path`를 각각 "*TestModel*" 및 "*builtin://Engine*"으로 채웁니다.
![모델 세부 정보 지정](./media/add-model-script.png)
1. 카메라 앞에 있는 **TestModel** 개체를 **x = 0, y = 0, z = 3** 위치에 배치합니다.
![개체 배치](./media/test-model-position.png)
1. **AutomaticallyLoad**가 켜져 있는지 확인합니다.
1. Unity 편집기에서 **재생**을 눌러 애플리케이션을 테스트합니다.
1. 앱에서 세션을 만들고 세션에 연결하여 모델을 자동으로 로드할 수 있도록 *연결* 단추를 클릭하여 권한을 부여합니다.

애플리케이션의 상태가 진행되는 동안 콘솔을 시청합니다. 일부 상태는 완료하는 데 다소 시간이 걸릴 수 있으며 진행률이 표시되지 않습니다. 최종적으로 로드되는 모델의 로그가 표시된 다음, 테스트 모델이 장면에 렌더링됩니다.

검사기의 변환을 통해 또는 장면 보기에서 **TestModel** GameObject를 이동하고 회전해 보세요. 게임 보기에서 모델이 이동하고 회전하는 것을 볼 수 있습니다.

![Unity 로그](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Azure 및 사용자 지정 모델 수집에서 Blob Storage 프로비저닝

이제 모델을 로드해 볼 수 있습니다. 모델을 로드하려면 Blob Storage를 구성하고, Azure에서 모델을 업로드하고 변환해야 합니다. 그런 다음, **RemoteRenderedModel** 스크립트를 사용하여 모델을 로드합니다. 현재 로드할 모델이 없는 경우 사용자 지정 모델 로드 단계를 안전하게 건너뛸 수 있습니다.

[빠른 시작: 렌더링을 위해 모델 변환](../../../quickstarts/convert-model.md)에 지정된 단계를 수행합니다. 이 자습서의 목적을 위해 **빠른 시작 샘플 앱에 새 모델 삽입** 섹션을 건너뜁니다. 수집한 모델의 *SAS(공유 액세스 서명)* URI를 만들었으면 아래의 다음 단계를 계속 진행합니다.

## <a name="load-and-rendering-a-custom-model"></a>사용자 지정 모델 로드 및 렌더링

1. 장면에 빈 GameObject를 새로 만들고 사용자 지정 모델과 비슷하게 이름을 지정합니다.
1. 새로 만든 GameObject에 *RemoteRenderedModel* 스크립트를 추가합니다.
 ![RemoteRenderedModel 구성 요소 추가](./media/add-remote-rendered-model-script.png)
1. `Model Display Name`에 적절한 모델 이름을 입력합니다.
1. `Model Path`에는 위의 수집 단계에서 만든 모델의 *SAS(공유 액세스 서명)* URI를 입력합니다.
1. 카메라 앞에 있는 GameObject를 **x = 0, y = 0, z = 3** 위치에 배치합니다.
1. **AutomaticallyLoad**가 켜져 있는지 확인합니다.
1. Unity 편집기에서 **재생**을 눌러 애플리케이션을 테스트합니다.

    콘솔이 현재 상태로 채워지기 시작하고, 최종적으로 모델 로딩 진행 상황 메시지로 채워집니다. 그런 다음, 사용자 지정 모델이 장면에 로드됩니다.

1. 장면에서 사용자 지정 모델 개체를 제거합니다. 이 자습서에 가장 적합한 환경은 테스트 모델을 사용하는 것입니다. ARR에서 여러 모델이 분명히 지원되지만, 이 자습서는 한 번에 하나의 원격 모델을 가장 잘 지원하도록 작성되었습니다.

## <a name="next-steps"></a>다음 단계

이제 개발자 고유의 모델을 Azure Remote Rendering에 로드하고 애플리케이션에서 모델을 볼 수 있습니다. 다음으로, 모델을 조작하는 방법을 알아보겠습니다.

> [!div class="nextstepaction"]
> [다음: 모델 조작](../manipulate-models/manipulate-models.md)

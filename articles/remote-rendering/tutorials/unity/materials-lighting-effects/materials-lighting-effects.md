---
title: 재질, 조명 및 효과 구체화
description: 모델 재질 및 조명을 수정합니다. 윤곽선 및 평면 잘라내기와 같은 추가 효과를 추가합니다.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 0580614468d4003b3640fd4df08ff02f3a1c8476
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021071"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>자습서: 재질, 조명 및 효과 구체화

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 모델 및 모델 구성 요소에 대한 강조 표시 및 윤곽선 지정
> * 모델에 다른 재질 적용
> * 평면 잘라내기를 사용하여 모델 분할
> * 원격으로 렌더링된 개체에 대한 간단한 애니메이션 추가

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서는 [자습서: 모델 조작](..\manipulate-models\manipulate-models.md)을 기반으로 합니다.

## <a name="highlighting-and-outlining"></a>강조 표시 및 윤곽선 지정

사용자에게 시각적 피드백을 제공하는 것은 모든 애플리케이션에서 사용자 환경의 중요한 부분입니다. Azure Remote Rendering은 [계층 상태 재정의](../../../overview/features/override-hierarchical-state.md)를 통해 시각적 피드백 메커니즘을 제공합니다. 계층 상태 재정의는 모델의 로컬 인스턴스에 연결된 구성 요소를 사용하여 구현됩니다. [원격 개체 그래프를 Unity 계층 구조에 동기화](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy)에서 이러한 로컬 인스턴스를 만드는 방법을 알아보았습니다.

먼저 래퍼를 [**HierarchicalStateOverrideComponent**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) 구성 요소 주위에 만듭니다. **HierarchicalStateOverrideComponent**는 원격 엔터티에 대한 재정의를 제어하는 로컬 스크립트입니다. [**자습서 자산**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial)에는 래퍼를 만들기 위해 확장할 **BaseEntityOverrideController**라는 추상 기본 클래스가 포함되어 있습니다.

1. **EntityOverrideController**라는 새 스크립트를 만들고, 해당 내용을 다음 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

**LocalOverride**의 기본 작업은 자체 및 해당 `RemoteComponent` 간의 링크를 만드는 것입니다. 그런 다음, **LocalOverride**를 사용하여 원격 엔터티에 바인딩된 로컬 구성 요소에서 상태 플래그를 설정할 수 있습니다. 재정의 및 해당 상태는 [계층 상태 재정의](../../../overview/features/override-hierarchical-state.md) 페이지에서 설명하고 있습니다. 

이 구현에서는 한 번에 하나의 상태만 전환합니다. 그러나 단일 엔터티에 대해 여러 재정의를 결합하고 계층 구조의 여러 수준에서 조합을 만들 수 있습니다. 예를 들어 단일 구성 요소에서 `Selected` 및 `SeeThrough`를 결합하면 윤곽선을 제공하면서도 투명하게 만들 수 있습니다. 또는 자식 엔터티의 `Hidden` 재정의를 `ForceOff`로 설정하는 동시에 루트 엔터티의 `Hidden` 재정의를 `ForceOn`으로 설정하면 재정의가 적용된 자식을 제외한 모든 항목이 숨겨집니다.

상태를 엔터티에 적용하기 위해 이전에 만든 **RemoteEntityHelper**를 수정할 수 있습니다.

1. **BaseRemoteEntityHelper** 추상 클래스를 구현하도록 **RemoteEntityHelper** 클래스를 수정합니다. 이렇게 수정하면 **자습서 자산**에 제공된 보기 컨트롤러를 사용할 수 있습니다. 수정되면 다음과 같습니다.

    ```csharp
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. 다음 코드를 사용하여 추상 메서드를 재정의합니다.

    ```csharp
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

이 코드는 **EntityOverrideController** 구성 요소가 대상 Entity에 추가되었는지 확인한 다음, 설정/해제 메서드 중 하나를 호출합니다. 원하는 경우 **TestModel** GameObject에서 **RemoteEntityHelper**를 **RemoteRayCastPointerHandler** 구성 요소의 `OnRemoteEntityClicked` 이벤트에 대한 콜백으로 추가하여 이러한 도우미 메서드를 호출해야 합니다.

![포인터 콜백](./media/pointer-event-callbacks.png)

이제 이러한 스크립트가 모델에 추가되었으므로 런타임에 연결되면 **AppMenu** 보기 컨트롤러에 **EntityOverrideController** 스크립트와 상호 작용하도록 설정된 추가 인터페이스가 있어야 합니다. 잠금 해제된 보기 컨트롤러를 보려면 **Model Tools(모델 도구)** 메뉴를 확인합니다.

이제 **TestModel** GameObject의 구성 요소는 다음과 같습니다.

![추가 스크립트를 사용하는 테스트 모델](./media/test-model-updated.png)

다음은 단일 엔터티에 대한 재정의를 쌓는 방법에 대한 예입니다. `Select` 및 `Tint`를 사용하여 윤곽선 및 색 지정을 모두 제공했습니다.

![테스트 모델 색조 선택](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>평면 잘라내기

[평면 잘라내기](../../../overview/features/cut-planes.md)는 원격 엔터티에 추가할 수 있는 기능입니다. 가장 일반적으로, 평면 잘라내기 구성 요소를 유지하기 위해 메시 데이터와 연결되지 않은 새 원격 엔터티를 만듭니다. 평면 잘라내기의 위치와 방향은 연결된 원격 엔터티의 위치와 방향에 따라 결정됩니다.

자동으로 원격 엔터티를 만들고, 평면 잘라내기 구성 요소를 추가하며, 로컬 개체의 변환을 평면 잘라내기 엔터티와 동기화하는 스크립트를 만듭니다. 그런 다음, **CutPlaneViewController**를 사용하여 절단면을 조작하는 데 사용할 수 있는 인터페이스에서 해당 절단면을 래핑할 수 있습니다.

1. **RemoteCutPlane**이라는 새 스크립트를 만들고, 해당 코드를 아래 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    이 코드는 **자습서 자산**에 포함된 **BaseRemoteCutPlane** 클래스를 확장합니다. 원격으로 렌더링된 모델과 마찬가지로 이 스크립트는 원격 코디네이터의 `RemoteRenderingState` 변경을 연결하고 수신 대기합니다. 코디네이터에서 `RuntimeConnected` 상태에 도달하면 필요한 경우 코디네이터에서 연결을 자동으로 시도합니다. 또한 추적할 `CutPlaneComponent` 변수가 있습니다. 이는 원격 세션에서 절단면과 동기화하는 Azure Remote Rendering 구성 요소입니다. 절단면을 만들기 위해 수행해야 하는 작업을 살펴보겠습니다.

2. `CreateCutPlane()` 메서드를 아래의 완성된 버전으로 바꿉니다.

    ```csharp
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    여기서는 원격 엔터티를 만들어 로컬 GameObject에 바인딩합니다. `SyncEveryFrame`을 `true`로 설정하여 원격 엔터티에서 해당 변환을 로컬 변환과 동기화되도록 합니다. 그런 다음, `CreateComponent` 호출을 사용하여 `CutPlaneComponent`를 원격 개체에 추가합니다. 마지막으로, MonoBehaviour의 위쪽에 정의된 설정을 사용하여 절단면을 구성합니다. `DestroyCutPlane()` 메서드를 구현하여 절단면을 정리하는 데 필요한 작업을 살펴보겠습니다.

3. `DestroyCutPlane()` 메서드를 아래의 완성된 버전으로 바꿉니다.

    ```csharp
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

원격 개체는 매우 간단하고 원격 엔드만 정리하므로(로컬 개체 유지) 원격 개체에 대한 `Destroy`를 호출하고 해당 참조를 지우기만 하면 됩니다.

**AppMenu**에는 자동으로 절단면에 연결되어 상호 작용할 수 있는 보기 컨트롤러가 포함되어 있습니다. **AppMenu** 또는 보기 컨트롤러 중 하나를 사용할 필요는 없지만 더 나은 환경을 제공합니다. 이제 절단면과 해당 보기 컨트롤러를 테스트합니다.

1. 빈 새 GameObject를 장면에 만들고, 이름을 **CutPlane**으로 지정합니다.
1. **RemoteCutPlane** 구성 요소를 **CutPlane** GameObject에 추가합니다.

   ![평면 잘라내기 구성 요소 구성](./media/cut-plane-config.png)

1. Unity 편집기에서 Play(재생)를 눌러 원격 세션을 로드하고 연결합니다.
1. MRTK의 손 시뮬레이션을 통해 CutPlane을 잡고 회전(Ctrl 키를 누른 채 회전)하여 장면 주위로 이동합니다. **TestModel**로 분할하여 내부 구성 요소가 표시되는 것을 확인합니다.

![평면 잘라내기 예](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>원격 조명 구성

원격 렌더링 세션에서는 [조명 옵션](../../../overview/features/lights.md)의 전체 스펙트럼을 지원합니다. [하늘 질감](../../../overview/features/sky.md)에 대한 스크립트 및 원격 렌더링에 사용할 두 가지 Unity 광원 유형에 대한 간단한 맵을 만듭니다.

### <a name="sky-texture"></a>하늘 질감

하늘 질감을 변경하는 경우 선택할 수 있는 여러 개의 기본 제공 Cubemaps가 있습니다. 이러한 Cubemaps는 세션에 로드되어 하늘 질감에 적용됩니다. 또한 [사용자 고유의 질감을 로드](../../../concepts/textures.md)하여 하늘 광원으로 사용할 수 있습니다.

로드 매개 변수 형식으로 사용 가능한 기본 제공 Cubemaps 목록을 포함하는 **RemoteSky** 스크립트를 만듭니다. 그런 다음, 사용자가 옵션 중 하나를 선택하여 로드할 수 있도록 허용합니다.

1. **RemoteSky**라는 새 스크립트를 만들고, 전체 내용을 아래 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSASParams> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSASParams> builtInTextures = new Dictionary<string, LoadTextureFromSASParams>()
        {
            {"Autoshop",new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    이 코드에서 가장 중요한 부분은 단 몇 줄입니다.

    ```csharp
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Actions.LoadTextureFromSASAsync(AvailableCubemaps[skyKey]).AsTask();

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Actions.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    여기서는 기본 제공 Blob 스토리지에서 세션에 로드하여 사용할 질감에 대한 참조를 가져옵니다. 그런 다음, 해당 질감을 세션의 `SkyReflectionTexture`에만 할당하여 적용해야 합니다.

1. 빈 GameObject를 장면에 만들고, 이름을 **SkyLight**로 지정합니다.

1. **RemoteSky** 스크립트를 **SkyLight** GameObject에 추가합니다.

    하늘 광원 간의 전환은 `AvailableCubemaps`에 정의된 문자열 키 중 하나를 사용하여 `SetSky`를 호출함으로써 수행할 수 있습니다. **AppMenu**에 기본적으로 제공되는 보기 컨트롤러에서 자동으로 단추를 만들고, 해당 키를 사용하여 `SetSky`를 호출하도록 해당 이벤트를 후크합니다.
1. Unity 편집기에서 Play(재생)를 누르고 연결에 대한 권한을 부여합니다.
1. 로컬 런타임이 원격 세션에 연결되면 **AppMenu -> Session Tools(세션 도구) -> Remote Sky(원격 하늘)** 를 차례로 탐색하여 다양한 하늘 옵션을 검색하고 이러한 옵션이 **TestModel**에 미치는 영향을 확인합니다.

### <a name="scene-lights"></a>장면 광원

원격 장면 광원에는 점, 스폿 및 방향성이 있습니다. 위에서 만든 절단면과 마찬가지로 이러한 장면 광원은 구성 요소가 연결된 원격 엔터티입니다. 원격 장면을 조명할 때 고려해야 할 중요한 사항은 로컬 장면의 조명과 일치시키려고 하는 것입니다. HoloLens 2에 대한 많은 Unity 애플리케이션에서 물리적 기반 렌더링을 로컬로 렌더링된 개체에 사용하지 않으므로 이 전략이 항상 가능하지는 않습니다. 그러나 Unity의 더 간단한 기본 조명은 특정 수준으로 시뮬레이션할 수 있습니다.

1. **RemoteLight**라는 새 스크립트를 만들고, 해당 코드를 아래 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Actions.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Actions.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    이 스크립트는 스크립트가 연결된 로컬 Unity 광원 유형에 따라 다양한 유형의 원격 광원을 만듭니다. 원격 광원은 로컬 광원을 해당 위치, 회전, 색 및 광도로 복제합니다. 가능한 경우 원격 광원은 추가 구성도 설정합니다. Unity 광원은 PBR 광원이 아니므로 이는 완벽하게 일치하지 않습니다.

1. 장면에서 **DirectionalLight** GameObject를 찾습니다. 장면에서 기본 **DirectionalLight**를 제거한 경우 위쪽 메뉴 모음에서 *GameObject -> Light(광원) -> DirectionalLight*를 차례로 선택하여 새 광원을 장면에 만듭니다.

1. **DirectionalLight** GameObject를 선택하고, **Add Component(구성 요소 추가)** 단추를 사용하여 **RemoteLight** 스크립트를 추가합니다.

1. 이 스크립트는 `BaseRemoteLight` 기본 클래스를 구현하므로 제공된 **AppMenu** 보기 컨트롤러를 사용하여 원격 광원과 상호 작용할 수 있습니다. **AppMenu -> Session Tools(세션 도구) -> Directional Light(방향성 광원)** 로 차례로 이동합니다.

    > [!NOTE]
    > 간단히 하기 위해 **AppMenu**의 UI가 단일 방향성 광원으로 제한되었습니다. 그러나 여전히 점 광원을 추가하고 **RemoteLight** 스크립트를 이에 연결하는 것이 좋습니다. 이러한 추가 광원은 편집기에서 Unity 광원의 속성을 편집하여 수정할 수 있습니다. 검사기에서 **RemoteLight** 상황에 맞는 메뉴를 사용하여 원격 광원에 대한 로컬 변경 내용을 수동으로 동기화해야 합니다.
    >
    > ![원격 광원 수동 동기화](./media/sync-remote-light.png)

1. Unity 편집기에서 Play(재생)를 누르고 연결에 대한 권한을 부여합니다.

1. 런타임이 원격 세션에 연결되면 보기에서 방향성 광원 보기 컨트롤러가 표시되도록 카메라를 배치하고 조준합니다(WASD 사용 및 마우스 오른쪽 단추 클릭 + 마우스 이동). 
1. 원격 광원 보기 컨트롤러를 사용하여 광원의 속성을 수정합니다. MRTK의 손 시뮬레이션을 통해 방향성 광원을 잡고 회전(Ctrl 키를 누른 채 회전)하여 장면 조명에 미치는 영향을 확인합니다.

    ![방향성 광원](./media/directional-light-test.png)

## <a name="editing-materials"></a>재질 편집

원격으로 렌더링된 [재질](../../../concepts/materials.md)을 수정하여 추가 시각적 효과를 제공하거나, 렌더링된 모델의 시각적 개체를 미세 조정하거나, 사용자에게 추가 피드백을 제공할 수 있습니다. 재질은 여러 가지 방법과 이유로 수정할 수 있습니다. 여기서는 재질의 albedo 색을 변경하고 PBR 재질의 황삭(roughness) 및 금속성(metalness)을 변경하는 방법을 보여 줍니다.

> [!NOTE]
> 대부분의 경우 **HierarchicalStateOverrideComponent**를 사용하여 기능 또는 효과를 구현할 수 있는 경우 재질을 수정하는 대신 이를 사용하는 것이 좋습니다.

대상 Entity를 허용하고 대상 Entity 재질의 속성을 변경하도록 몇 가지 `OverrideMaterialProperty` 개체를 구성하는 스크립트를 만듭니다. 먼저 메시에 사용된 재질의 목록이 포함된 대상 Entity의 [**MeshComponent**](../../../concepts/meshes.md#meshcomponent)를 가져옵니다. 간단히 하기 위해 처음 찾은 재질만 사용합니다. 이 순진한 전략은 콘텐츠가 작성된 방법에 따라 매우 쉽게 실패할 수 있으므로 더 복잡한 방법을 사용하여 적절한 재질을 선택하는 것이 좋습니다.

재질에서는 albedo와 같은 일반적인 값에 액세스할 수 있습니다. 먼저 **GetMaterialColor** 메서드에서와 같이 해당 값을 검색하려면 재질을 적절한 `PbrMaterial` 또는 `ColorMaterial` 형식으로 캐스팅해야 합니다. 원하는 재질에 대한 참조가 있으면 해당 값만 설정하고 ARR에서 로컬 재질 속성과 원격 재질 간의 동기화를 처리하면 됩니다.

1. **EntityMaterialController**라는 스크립트를 만들고, 해당 내용을 다음 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

원하는 경우 `OverrideMaterialProperty` 형식은 몇 가지 다른 재질 값을 변경할 수 있을 만큼 유연해야 합니다. `OverrideMaterialProperty` 형식은 재정의 상태를 추적하고, 이전 값과 새 값을 유지하며, 대리자를 사용하여 재정의를 설정합니다. 예를 들어 `ColorOverride`를 살펴보세요.

```csharp
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

이는 재정의에서 `Color` 형식을 래핑하는 새 `OverrideMaterialProperty`를 만듭니다. 재정의를 만들 때 현재 또는 원래 색을 제공합니다. 또한 작업할 ARR 재질을 전달합니다. 마지막으로, 재정의를 적용할 대리자를 제공합니다. 대리자는 ARR 재질 및 재정의에서 래핑하는 형식을 허용하는 메서드입니다. 이 메서드는 ARR에서 재질 값을 조정하는 방법을 이해하는 데 가장 중요한 부분입니다.

`ColorOverride`는 `ApplyMaterialColor` 메서드를 사용하여 작업을 수행합니다.

```csharp
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

이 코드는 재질 및 색을 허용합니다. 재질의 종류를 확인한 다음, 재질의 캐스팅을 수행하여 색을 적용합니다.

`RoughnessOverride` 및 `MetalnessOverride`는 비슷한 방식으로 작동합니다. 즉, `ApplyRoughnessValue` 및 `ApplyMetalnessValue` 메서드를 사용하여 작업을 수행합니다.

다음으로, 재질 컨트롤러를 테스트해 보겠습니다.

1. **EntityMaterialController** 스크립트를 **TestModel** GameObject에 추가합니다.
1. Unity에서 Play(재생)를 눌러 장면을 시작하고 ARR에 연결합니다.
1. 런타임이 원격 세션에 연결되고 모델이 로드되면 **AppMenu -> Model Tools(모델 도구) -> Edit Material(재질 편집)** 로 차례로 이동합니다.
1. 시뮬레이션된 손을 통해 **TestModel**을 클릭하여 모델에서 Entity를 선택합니다.
1. 재질 보기 컨트롤러(**AppMenu -> Model Tools -> Edit Material**)가 대상 Entity로 업데이트되었는지 확인합니다.
1. 재질 보기 컨트롤러를 사용하여 대상 Entity의 재질을 조정합니다.

메시의 첫 번째 재질만 수정하여 재질이 변경되지 않을 수 있습니다. **SeeThrough** 계층 재정의를 사용하여 변경하려는 재질이 메시 내부에 있는지 확인합니다.

![재질 편집 예](./media/material-edit-example.png)

## <a name="next-steps"></a>다음 단계

축하합니다! 이제 Azure Remote Rendering의 모든 핵심 기능을 구현했습니다. 다음 챕터에서는 Azure Remote Rendering 및 Blob 스토리지를 보호하는 방법에 대해 알아봅니다. 이러한 방법은 Azure Remote Rendering을 사용하는 상용 애플리케이션을 릴리스하는 첫 번째 단계입니다.

> [!div class="nextstepaction"]
> [다음: Azure Remote Rendering 및 모델 스토리지 보안](../security/security.md)
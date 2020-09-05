---
title: 모델 조작
description: 이동, 회전, 크기 조정 등을 통해 원격으로 렌더링된 모델을 조작합니다.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 07b5b81dd7e23f25e7bfba90bbab7083090724d4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018861"
---
# <a name="tutorial-manipulating-models"></a>자습서: 모델 조작

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 원격으로 렌더링된 모델에 대한 시각적 개체 및 조작 범위 추가
> * 이동, 회전 및 크기 조정
> * 공간 쿼리를 통한 광선 캐스팅
> * 원격으로 렌더링된 개체에 대한 간단한 애니메이션 추가

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서는 [자습서: 인터페이스 및 사용자 지정 모델](../custom-models/custom-models.md)을 기반으로 합니다.

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>원격 개체 범위 쿼리 및 로컬 범위에 적용

원격 개체와 상호 작용하려면 먼저 상호 작용하기 위한 로컬 표현이 필요합니다. [개체 범위](../../../concepts/object-bounds.md)는 원격 개체를 빠르게 조작하는 데 유용합니다. 원격 범위는 ARR에서 로컬 Entity를 참조로 사용하여 쿼리할 수 있습니다. 모델이 원격 세션에 로드되면 범위가 쿼리됩니다.

모델의 범위는 Unity의 [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html)처럼 x, y, z 축에 대한 중심과 크기가 정의된 전체 모델을 포함하는 상자로 정의됩니다. 실제로 Unity의 **BoxCollider**를 사용하여 원격 모델의 범위를 나타냅니다.

1. 새 스크립트를 **RemoteRenderedModel**과 동일한 디렉터리에 만들고, 이름을 **RemoteBounds**로 지정합니다.
1. 스크립트의 내용을 다음 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private BoundsQueryAsync remoteBoundsQuery = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create a query using the model entity
        private void QueryBounds()
        {
            //Implement me
        }

        // Check the result and apply it to the local Unity bounding box if it was successful
        private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Visual Studio에서 *C# 6에서는 'X' 기능을 사용할 수 없습니다. 언어 버전 7.0 이상을 사용하세요.* 라고 요구하는 오류가 표시되는 경우 이러한 오류는 무시할 수 있습니다. 이는 Unity의 솔루션 및 프로젝트 생성과 관련이 있습니다.

    이 스크립트는 **BaseRemoteRenderedModel**을 구현하는 스크립트와 동일한 GameObject에 추가해야 합니다. 이 경우 **RemoteRenderedModel**을 의미합니다. 이전 스크립트와 비슷하게 이 초기 코드는 원격 범위와 관련된 모든 상태 변경, 이벤트 및 데이터를 처리합니다.

    구현해야 할 **QueryBounds** 및 **ProcessQueryResult**의 두 가지 메서드가 있습니다. **QueryBounds**는 범위를 가져오고, **ProcessQueryResult**는 쿼리 결과를 가져와서 로컬 **BoxCollider**에 적용합니다.

    **QueryBounds** 메서드는 간단합니다. 즉, 쿼리를 원격 렌더링 세션으로 보내고 `Completed` 이벤트를 수신 대기합니다.

1. **QueryBounds** 메서드를 완성된 다음 메서드로 바꿉니다.

    ```csharp
    // Create a query using the model entity
    private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        remoteBoundsQuery.Completed += ProcessQueryResult;
    }
    ```

    **ProcessQueryResult**도 간단합니다. 결과가 성공적인지 확인합니다. 그렇다면 반환된 범위를 **BoxCollider**에서 허용할 수 있는 형식으로 변환하여 적용합니다.    

1. **ProcessQueryResult** 메서드를 완성된 다음 메서드로 바꿉니다.

    ```csharp
    // Check the result and apply it to the local Unity bounding box if it was successful
    private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
    {
        if (remoteBounds.IsRanToCompletion)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

이제 **RemoteBounds** 스크립트가 **RemoteRenderedModel**과 동일한 게임 개체에 추가될 때 필요한 경우 **BoxCollider**가 추가되고, 모델이 `Loaded` 상태에 도달하는 경우 범위가 자동으로 쿼리되어 **BoxCollider**에 적용됩니다.

1. 이전에 만든 **TestModel** GameObject를 사용하여 **RemoteBounds** 구성 요소를 추가합니다.
1. 스크립트가 추가되었는지 확인합니다.

     ![RemoteBounds 구성 요소 추가](./media/remote-bounds-script.png)

1. 애플리케이션을 다시 실행합니다. 모델이 로드되는 즉시 원격 개체에 대한 범위가 표시됩니다. 다음과 같은 값이 표시됩니다.

     ![업데이트된 범위](./media/updated-bounds.png)

이제 Unity 개체에서 정확한 범위를 사용하여 구성된 로컬 **BoxCollider**가 있습니다. 범위를 사용하면 로컬로 렌더링된 개체에 사용하는 것과 동일한 전략을 사용하여 시각화 및 상호 작용을 수행할 수 있습니다. 예를 들어 Transform(변환), 물리 등을 변경하는 스크립트가 있습니다.

## <a name="move-rotate-and-scale"></a>이동, 회전 및 크기 조정  

원격으로 렌더링된 개체를 이동, 회전 및 크기 조정하는 작업은 다른 Unity 개체와 동일하게 작동합니다. `LateUpdate` 메서드의 **RemoteRenderingCoordinator**는 현재 활성 세션에서 `Update`를 호출합니다. `Update`에서 수행하는 작업의 일부는 로컬 모델 엔터티 변환을 원격 대응 항목과 동기화하는 것입니다. 원격으로 렌더링된 모델을 이동, 회전 또는 크기 조정하려면 원격 모델을 나타내는 GameObject의 변환만 이동, 회전 또는 크기 조정하면 됩니다. 여기서는 **RemoteRenderedModel** 스크립트가 연결된 부모 GameObject의 변환을 수정합니다.

이 자습서에서는 MRTK를 개체 상호 작용에 사용합니다. 개체를 이동, 회전 및 크기 조정하는 MRTK 관련 구현의 대부분은 이 자습서의 범위를 벗어납니다. **AppMenu** 내의 **Model Tools(모델 도구)** 메뉴에는 미리 구성된 모델 보기 컨트롤러가 있습니다.

1. 이전에 만든 **TestModel** GameObject가 장면에 있는지 확인합니다.
1. **AppMenu** prefab이 장면에 있는지 확인합니다.
1. Unity의 Play(재생) 단추를 눌러 장면을 재생하고, **AppMenu** 내에서 **Model Tools(모델 도구)** 메뉴를 엽니다.
![보기 컨트롤러](./media/model-with-view-controller.png)

**AppMenu**에는 모델과 바인딩하기 위한 보기 컨트롤러를 구현하는 **Model Tools(모델 도구)** 라는 하위 메뉴가 있습니다. **RemoteBounds** 구성 요소가 GameObject에 포함되어 있으면 보기 컨트롤러에서 [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) 구성 요소를 추가합니다. 이는 **BoxCollider**를 사용하여 경계 상자를 개체 주위에 렌더링하는 MRTK 구성 요소입니다. [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/api/Microsoft.MixedReality.Toolkit.Experimental.UI.ObjectManipulator.html?q=ObjectManipulator)는 손 상호 작용을 담당합니다. 이러한 스크립트를 결합하면 원격으로 렌더링된 모델을 이동, 회전 및 크기 조정할 수 있습니다.

1. 마우스를 게임 패널로 이동하고, 이 패널 내부를 클릭하여 포커스를 맞춥니다.
1. [MRTK의 손 시뮬레이션](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation)을 사용하여 왼쪽 Shift 키를 길게 누릅니다.
1. 손 광선이 테스트 모델을 가리키도록 시뮬레이션된 손을 유도합니다.

    ![가리키는 손 광선](./media/handray-engine.png)

1. 마우스 왼쪽 단추를 클릭한 채 모델을 끌어서 이동합니다.

원격으로 렌더링된 콘텐츠가 경계 상자와 함께 이동됩니다. 경계 상자와 원격 콘텐츠 간에 약간의 지연이 있을 수 있습니다. 이 지연은 인터넷 대기 시간 및 대역폭에 따라 달라집니다.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>원격 모델의 광선 캐스팅 및 공간 쿼리

모델 주위의 상자 collider는 전체 모델과 상호 작용하는 데 적합하지만, 모델의 개별 부분과 상호 작용하는 데는 충분하지 않습니다. 이를 해결하기 위해 [원격 광선 캐스팅](../../../overview/features/spatial-queries.md#ray-casts)을 사용할 수 있습니다. 원격 광선 캐스팅은 광선을 원격 장면으로 캐스팅하고 적중 결과를 로컬로 반환하기 위해 Azure Remote Rendering에서 제공하는 API입니다. 이 기법을 사용하여 큰 모델의 자식 엔터티를 선택하거나 위치, 보통 표면 및 거리와 같은 적중 결과 정보를 얻을 수 있습니다.

테스트 모델에는 쿼리하고 선택할 수 있는 여러 하위 엔터티가 있습니다. 지금은 선택한 Entity의 이름이 Unity 콘솔에 출력됩니다. 선택한 Entity를 강조 표시하려면 [재질, 조명 및 효과](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) 챕터를 확인합니다.

먼저 정적 래퍼를 원격 광선 캐스팅 쿼리 주위에 만들어 보겠습니다. 이 스크립트는 Unity 공간에서 위치와 방향을 허용하고, 원격 광선 캐스팅에서 허용하는 데이터 형식으로 변환하며, 결과를 반환합니다. 스크립트에서 `RayCastQueryAsync` API를 사용합니다.

1. **RemoteRayCaster**라는 새 스크립트를 만들고, 해당 내용을 다음 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                return await RemoteRenderingCoordinator.CurrentSession.Actions.RayCastQueryAsync(rayCast).AsTask();
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity에는 [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html)라는 클래스가 있고, Azure Remote Rendering에는 [**RayCastHit**](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering.raycasthit)라는 클래스가 있습니다. 대문자 **C**는 컴파일 오류를 방지하기 위한 중요한 차이입니다.

    **RemoteRayCaster**는 원격 광선을 현재 세션으로 캐스팅하기 위한 공통 액세스 지점을 제공합니다. 더 구체적으로, MRTK 포인터 처리기는 다음에 구현합니다. 이 스크립트는 스크립트에서 [혼합 현실 포인터](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html) 이벤트를 수신 대기하도록 MRTK에 알려주는 `IMixedRealityPointerHandler` 인터페이스를 구현합니다.

1. **RemoteRayCastPointerHandler**라는 새 스크립트를 만들고, 코드를 다음 코드로 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

**RemoteRayCastPointerHandler**의 `OnPointerClicked` 메서드는 상자 collider와 같이 collider에서 포인터를 '클릭'할 때 MRTK를 통해 호출됩니다. 그런 다음, 포인터의 결과를 점과 방향으로 변환하기 위해 `PointerDataToRemoteRayCast`가 호출됩니다. 그러면 해당 지점과 방향을 사용하여 원격 세션에서 원격 광선을 캐스팅합니다.

![업데이트된 범위](./media/raycast-local-remote.png)

클릭 시 광선 캐스팅 요청을 보내는 것은 원격 개체를 쿼리하기 위한 효율적인 전략입니다. 그러나 커서가 모델 자체가 아니라 상자 collider와 충돌하므로 적합한 사용자 환경이 아닙니다.

또한 원격 세션에서 광선을 더 자주 캐스팅하는 새 MRTK 포인터를 만들 수도 있습니다. 이는 더 복잡한 방법이지만 사용자 환경이 더 효율적입니다. 이 전략은 이 자습서의 범위를 벗어나지만, 이 방법의 예제는 [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/AzureRemoteRenderingShowcase)에 있는 소개 앱에서 확인할 수 있습니다.

**RemoteRayCastPointerHandler**에서 광선 캐스팅이 성공적으로 완료되면 `OnRemoteEntityClicked` Unity 이벤트에서 `Entity` 적중을 내보냅니다. 해당 이벤트에 응답하기 위해 `Entity`를 허용하고 해당 이벤트에 대한 작업을 수행하는 도우미 스크립트를 만듭니다. 먼저 스크립트에서 `Entity`의 이름을 디버그 로그에 출력하도록 해보겠습니다.

1. **RemoteEntityHelper**라는 새 스크립트를 만들고, 해당 내용을 아래와 같이 바꿉니다.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. 이전에 만든 **TestModel** GameObject에서 **RemoteRayCastPointerHandler** 구성 요소 및 **RemoteEntityHelper** 구성 요소를 모두 추가합니다.
1. `EntityToDebugLog` 메서드를 `OnRemoteEntityClicked` 이벤트에 할당합니다. 이벤트의 출력 형식과 메서드의 입력 형식이 일치하면 Unity의 동적 이벤트 후크를 사용하여 이벤트 값을 메서드에 자동으로 전달할 수 있습니다.
    1. 새 콜백 필드 만들기 ![콜백 추가](./media/add-callback-remote-entity-clicked.png)
    1. 부모 GameObject를 참조하도록 **원격 엔터티 도우미** 구성 요소를 개체 필드로 끌기 ![개체 할당](./media/assign-object.png)
    1. 콜백으로 `EntityToDebugLog` 할당 ![콜백 할당](./media/remote-entity-event.png)
1. Unity 편집기에서 Play(재생)를 눌러 장면을 시작하고, 원격 세션에 연결하고, 테스트 모델을 로드합니다.
1. MRTK의 손 시뮬레이션을 사용하여 왼쪽 Shift 키를 길게 누릅니다.
1. 손 광선이 테스트 모델을 가리키도록 시뮬레이션된 손을 유도합니다.
1. 길게 클릭하여 `OnPointerClicked` 이벤트를 실행하는 에어 탭을 시뮬레이션합니다.
1. Unity 콘솔에서 선택한 자식 엔터티의 이름이 포함된 로그 메시지를 확인합니다. 예를 들면 다음과 같습니다. ![자식 엔터티 예제](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>원격 개체 그래프를 Unity 계층 구조에 동기화

지금까지 전체 모델을 나타내는 단일 로컬 GameObject만 살펴보았습니다. 이는 전체 모델을 렌더링하고 조작하는 데 적합합니다. 그러나 효과를 적용하거나 특정 하위 엔터티를 조작하려면 해당 엔터티를 나타내는 로컬 GameObject를 만들어야 합니다. 먼저 테스트 모델에서 수동으로 검색할 수 있습니다.

1. 장면을 시작하고 테스트 모델을 로드합니다.
1. Unity의 계층 구조에서 **TestModel** GameObject의 자식을 펼치고, **TestModel_Entity** GameObject를 선택합니다.
1. 검사기에서 *Show Children(자식 항목 표시)* 단추를 클릭합니다.
![Show children](./media/show-remote-children.png)
1. 계층 구조에서 자식을 계속 펼치고, 많은 자식 목록이 표시될 때까지 *Show Children(자식 항목 표시)* 을 클릭합니다.
![모든 자식](./media/test-model-children.png)

이제 계층 구조가 수십 개의 엔터티 목록으로 채워집니다. 이러한 엔터티 중 하나를 선택하면 검사기에서 `Transform` 및 `RemoteEntitySyncObject` 구성 요소가 표시됩니다. 기본적으로 각 엔터티는 프레임마다 자동으로 동기화되지 않으므로 `Transform`에 대한 로컬 변경 내용이 서버와 동기화되지 않습니다. *Sync Every Frame(모든 프레임 동기화)* 을 선택한 다음, Scene(장면) 보기에서 변환을 이동, 크기 조정 또는 회전할 수 있습니다. 장면 보기에는 렌더링된 모델이 표시되지 않지만, Game(게임) 보기에서는 모델의 위치와 회전이 시각적으로 업데이트되는지 확인할 수 있습니다.

동일한 프로세스는 프로그래밍 방식으로 수행할 수 있으며, 특정 원격 엔터티를 수정하는 첫 번째 단계입니다.

1. 다음 메서드도 포함하도록 **RemoteEntityHelper** 스크립트를 수정합니다.

    ```csharp
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. 추가 콜백을 **RemoteRayCastPointerHandler** 이벤트 `OnRemoteEntityClicked`에 추가하고, `MakeSyncedGameObject`로 설정합니다.
![추가 콜백](./media/additional-callback.png)
1. MRTK의 손 시뮬레이션을 사용하여 왼쪽 Shift 키를 길게 누릅니다.
1. 손 광선이 테스트 모델을 가리키도록 시뮬레이션된 손을 유도합니다.
1. 길게 클릭하여 `OnPointerClicked` 이벤트를 실행하는 에어 탭을 시뮬레이션합니다.
1. Hierarchy(계층 구조)를 선택하여 펼쳐서 클릭된 엔터티를 나타내는 새 자식 개체를 표시합니다.
![GameObject 표현](./media/gameobject-representing-entity.png)
1. 테스트가 완료되면 나중에 다른 효과의 일부로 통합할 수 있으므로 `MakeSyncedGameObject`에 대한 콜백을 제거합니다.

> [!NOTE]
> 모든 프레임 동기화는 변환 데이터를 동기화해야 하는 경우에만 필요합니다. 변환을 동기화하는 경우 약간의 오버헤드가 있으므로 드물게 사용해야 합니다.

로컬 인스턴스를 만들고 자동으로 동기화하는 것이 하위 엔터티를 조작하는 첫 번째 단계입니다. 모델 전체를 조작하는 데 사용한 것과 동일한 기법을 하위 엔터티에도 사용할 수 있습니다. 예를 들어 엔터티의 동기화된 로컬 인스턴스가 만들어지면 해당 범위를 쿼리하고, 조작 처리기를 추가하여 사용자의 손 광선으로 엔터티를 이동할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 원격으로 렌더링된 모델을 조작하고 상호 작용할 수 있습니다! 다음 자습서에서는 재질을 수정하고, 조명을 변경하고, 효과를 원격으로 렌더링된 모델에 적용하는 방법에 대해 설명합니다.

> [!div class="nextstepaction"]
> [다음: 재질, 조명 및 효과 구체화](../materials-lighting-effects/materials-lighting-effects.md)

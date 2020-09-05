---
title: Unity 게임 개체 및 구성 요소
description: 원격 렌더링 엔터티 및 구성 요소를 사용 하 여 작업 하는 Unity 관련 메서드를 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 662c28196b06f5fbe49f69cb7145fdd33805e000
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019048"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Unity 게임 개체 및 구성 요소와 상호 작용

ARR (Azure Remote 렌더링)은 많은 수의 개체에 최적화 되어 있습니다 ( [제한](../../reference/limits.md)참조). 호스트에서 크고 복잡 한 계층을 관리할 수 있지만, 저전력 장치에서 모든 Unity를 복제 하는 것은 작업입니다.

따라서 호스트가 호스트에 로드 되 면 Azure 원격 렌더링은 클라이언트 장치의 모델 구조에 대 한 정보 (네트워크 트래픽이 발생 함)를 미러링 하지만 Unity의 개체와 구성 요소는 복제 하지 않습니다. 대신 필요한 Unity 게임 개체 및 구성 요소를 수동으로 요청 해야 하므로 실제로 필요한 항목에 대 한 오버 헤드를 제한할 수 있습니다. 이렇게 하면 클라이언트 쪽 성능을 보다 효과적으로 제어할 수 있습니다.

따라서 Azure 원격 렌더링의 Unity 통합에는 주문형 원격 렌더링 구조를 복제할 수 있는 추가 기능이 제공 됩니다.

## <a name="load-a-model-in-unity"></a>Unity에서 모델 로드

모델을 로드 하는 경우 로드 된 모델의 루트 개체에 대 한 참조를 가져옵니다. 이 참조는 Unity 게임 개체가 아니지만 확장 메서드를 사용 하 여 하나의 참조로 전환할 수 있습니다 `Entity.GetOrCreateGameObject()` . 이 함수에는 형식의 인수가 필요 `UnityCreationMode` 합니다. 를 전달 하는 경우 `CreateUnityComponents` 새로 만든 Unity 게임 개체는 호스트에 있는 모든 원격 렌더링 구성 요소에 대 한 프록시 구성 요소로 추가로 채워집니다. 그러나이를 선호 하 여 `DoNotCreateUnityComponents` 오버 헤드를 최소로 유지 하는 것이 좋습니다.

### <a name="load-model-with-task"></a>태스크를 사용 하 여 모델 로드

```cs
LoadModelAsync _pendingLoadTask = null;
void LoadModelWithTask()
{
    _pendingLoadTask = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    _pendingLoadTask.Completed += (LoadModelAsync res) =>
    {
        // turn the root object into a Unity game object
        var gameObject = res.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        _pendingLoadTask = null;
    };

    // also listen to progress updates:
    _pendingLoadTask.ProgressUpdated += (float progress) =>
    {
        // progress is a fraction in [0..1] range
        int percentage = (int)(progress * 100.0f);
        // do something...
        // Since the updates are triggered by the main thread, we may access unity objects here.
    };
}
```

### <a name="load-model-with-unity-coroutines"></a>Unity 코 루틴를 사용 하 여 모델 로드

```cs
IEnumerator LoadModelWithCoroutine()
{
    LoadModelAsync task = RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine"));

    while (!task.IsCompleted)
    {
        int percentage = (int)(task.Progress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }

    task = null;
}
```

### <a name="load-model-with-await-pattern"></a>대기 패턴으로 모델 로드

```cs
async void LoadModelWithAwait()
{
    var result = await RemoteManagerUnity.CurrentSession.Actions.LoadModelFromSASAsync(new LoadModelFromSASParams("builtin://Engine")).AsTask();
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

위의 코드 샘플에서는 기본 제공 모델이 로드 되었으므로 SAS를 통해 모델 로드 경로를 사용 했습니다. Blob 컨테이너 (및 사용)를 통해 모델의 주소를 지정 하는 것 `LoadModelAsync` `LoadModelParams` 은 완전히와 유사.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Unity 게임 개체를 만들면 `RemoteEntitySyncObject` 구성 요소가 게임 개체에 암시적으로 추가 됩니다. 이 구성 요소는 엔터티 변환을 서버에 동기화 하는 데 사용 됩니다. 기본적으로 `RemoteEntitySyncObject` 사용자는를 명시적으로 호출 하 여 `SyncToRemote()` 로컬 Unity 상태를 서버와 동기화 해야 합니다. 을 사용 하도록 설정 하면 `SyncEveryFrame` 개체가 자동으로 동기화 됩니다.

를 사용 하는 개체는 `RemoteEntitySyncObject` 해당 원격 자식을 인스턴스화하고 단추를 통해 Unity 편집기에 표시할 수 있습니다 **:::no-loc text="Show children":::** .

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>래퍼 구성 요소

원격 렌더링 엔터티에 연결 된 [구성 요소](../../concepts/components.md) 는 프록시를 통해 Unity에 노출 됩니다 `MonoBehavior` . 이러한 프록시는 Unity의 원격 구성 요소를 나타내며 호스트에 대 한 모든 수정 사항을 전달 합니다.

프록시 원격 렌더링 구성 요소를 만들려면 확장 메서드를 사용 합니다 `GetOrCreateArrComponent` .

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>결합 수명

원격 [엔터티와](../../concepts/entities.md) Unity 게임 개체의 수명은를 통해 바인딩됩니다 `RemoteEntitySyncObject` . `UnityEngine.Object.Destroy(...)`이러한 게임 개체를 사용 하 여를 호출 하면 원격 엔터티도 제거 됩니다.

원격 엔터티에 영향을 주지 않고 Unity 게임 개체를 삭제 하려면 먼저에서를 호출 해야 `Unbind()` `RemoteEntitySyncObject` 합니다.

모든 프록시 구성 요소에 대해서도 마찬가지입니다. 클라이언트 쪽 표시만 제거 하려면 `Unbind()` 먼저 프록시 구성 요소에서를 호출 해야 합니다.

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>다음 단계

* [Unity용 Remote Rendering 설정](unity-setup.md)
* [자습서: Unity에서 원격 엔터티 조작](../../tutorials/unity/manipulate-models/manipulate-models.md)

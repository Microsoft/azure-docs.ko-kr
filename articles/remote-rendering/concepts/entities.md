---
title: 엔터티
description: Azure Remote Rendering API의 엔터티 정의
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 29952353b8c3452d95bcced163fafa81fe158f64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99593404"
---
# <a name="entities"></a>엔터티

‘엔터티’는 공간에서 이동할 수 있는 개체를 가리키며, 원격으로 렌더링되는 콘텐츠의 기본 구성 요소입니다.

## <a name="entity-properties"></a>엔터티 속성

엔터티는 위치, 회전 및 크기로 정의되는 변환을 갖습니다. 엔터티 그 자체로는 눈에 보이는 기능을 갖지 않으며, 엔터티에 연결되는 구성 요소를 통해 동작이 추가됩니다. 예를 들어, [CutPlaneComponent](../overview/features/cut-planes.md)를 연결하면 엔터티 위치에 잘린 평면이 만들어집니다.

엔터티 자체의 가장 중요한 측면은 계층 구조와 그로 인한 계층 변환입니다. 예를 들어, 공유 부모 엔터티에 여러 엔터티가 자식 요소로 연결된 경우, 부모 엔터티의 변환을 변경하여 모든 엔터티를 이동, 회전 및 크기 조정할 수 있습니다. 또한 엔터티의 `enabled` 상태를 사용하여 계층의 전체 하위 그래프에 대한 표시 유형 및 광선 캐스트에 대한 응답을 해제할 수 있습니다.

엔터티는 부모 요소에 의해 고유하게 소유됩니다. 즉, `Entity.Destroy()`로 부모 요소가 제거되면 그 자식 요소와 모든 연결된 [구성 요소](components.md)도 제거됩니다. 따라서 장면에서 모델을 제거하는 것은 모델의 루트 노드에서 `RenderingSession.Connection.LoadModelAsync()` 또는 그 SAS 변형 `RenderingSession.Connection.LoadModelFromSasAsync()`로 반환되는 `Destroy`를 호출하여 수행됩니다.

엔터티는 서버가 콘텐츠를 로드하거나 사용자가 장면에 개체를 추가하려는 경우에 만들어집니다. 예를 들어, 사용자가 메시의 내부를 시각화하기 위해 잘린 평면을 추가하려는 경우, 평면을 배치하려는 곳에 엔터티를 만들고 여기에 잘린 평면 구성 요소를 추가하면 됩니다.

## <a name="create-an-entity"></a>엔터티 만들기

장면에 새 엔터티를 추가하여 모델을 로드하기 위한 루트 개체로 전달하거나 구성 요소를 연결하려면 다음 코드를 사용합니다.

```cs
Entity CreateNewEntity(RenderingSession session)
{
    Entity entity = session.Connection.CreateEntity();
    entity.Position = new LocalPosition(1, 2, 3);
    return entity;
}
```

```cpp
ApiHandle<Entity> CreateNewEntity(ApiHandle<RenderingSession> session)
{
    ApiHandle<Entity> entity(nullptr);
    if (auto entityRes = session->Connection()->CreateEntity())
    {
        entity = entityRes.value();
        entity->SetPosition(Double3{ 1, 2, 3 });
        return entity;
    }
    return entity;
}
```

## <a name="query-functions"></a>쿼리 함수

엔터티에 대한 쿼리 함수에는 동기 호출과 비동기 호출, 두 가지 유형이 있습니다. 동기 쿼리는 클라이언트에 있는 데이터에 대해서만 사용할 수 있으며, 그다지 많은 계산이 수반되지 않습니다. 동기 쿼리의 예로 구성 요소, 상대 개체 변환 또는 부모/자식 관계에 대한 쿼리를 들 수 있습니다. 비동기 쿼리는 서버에만 존재하는 데이터 또는 추가 계산이 필요하여 클라이언트에서 실행하기에는 비용이 너무 많이 드는 데이터에 대해서만 사용됩니다. 비동기 쿼리의 예로 공간 경계 쿼리 또는 메타데이터 쿼리를 들 수 있습니다.

### <a name="querying-components"></a>구성 요소 쿼리

특정 형식의 구성 요소를 찾으려면 `FindComponentOfType`을 사용합니다.

```cs
CutPlaneComponent cutplane = (CutPlaneComponent)entity.FindComponentOfType(ObjectType.CutPlaneComponent);

// or alternatively:
CutPlaneComponent cutplane = entity.FindComponentOfType<CutPlaneComponent>();
```

```cpp
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType(ObjectType::CutPlaneComponent)->as<CutPlaneComponent>();

// or alternatively:
ApiHandle<CutPlaneComponent> cutplane = entity->FindComponentOfType<CutPlaneComponent>();
```

### <a name="querying-transforms"></a>변환 쿼리

변환 쿼리는 개체에 대한 동기 호출입니다. API를 통해 쿼리된 변환은 개체의 부모 요소에 상대적으로 로컬 공간 변환입니다. 이에 대한 예외로 루트 개체가 있는데, 루트 개체의 경우 로컬 공간과 월드 공간이 동일합니다.

> [!NOTE]
> 임의 개체의 월드 공간 변환을 쿼리하기 위한 전용 API는 없습니다.

```cs
// local space transform of the entity
Double3 translation = entity.Position;
Quaternion rotation = entity.Rotation;
```

```cpp
// local space transform of the entity
Double3 translation = entity->GetPosition();
Quaternion rotation = entity->GetRotation();
```

### <a name="querying-spatial-bounds"></a>공간 경계 쿼리

경계 쿼리는 하나의 엔터티를 루트로 사용하여 전체 개체 계층 구조에 작용하는 비동기 호출입니다. [개체 경계](object-bounds.md)에 대해 설명하는 챕터를 참조하세요.

### <a name="querying-metadata"></a>메타데이터 쿼리

메타데이터는 개체에 저장된 추가 데이터로, 서버에서 무시됩니다. 개체 메타데이터는 (이름, 값) 쌍 세트로, 여기서 _값_ 은 숫자, 부울 또는 문자열 형식일 수 있습니다. 메타데이터는 모델을 사용하여 내보낼 수 있습니다.

메타데이터 쿼리는 특정 엔터티에 대한 비동기 호출입니다. 쿼리는 단일 엔터티의 메타데이터만 반환하며, 하위 그래프의 병합된 정보는 반환하지 않습니다.

```cs
Task<ObjectMetadata> metaDataQuery = entity.QueryMetadataAsync();
ObjectMetadata metaData = await metaDataQuery;
ObjectMetadataEntry entry = metaData.GetMetadataByName("MyInt64Value");
System.Int64 intValue = entry.AsInt64;
// ...
```

```cpp
entity->QueryMetadataAsync([](Status status, ApiHandle<ObjectMetadata> metaData) 
{
    if (status == Status::OK)
    {
        ApiHandle<ObjectMetadataEntry> entry = *metaData->GetMetadataByName("MyInt64Value");
        int64_t intValue = *entry->GetAsInt64();

        // ...
    }
});
```

개체에 메타데이터가 없는 경우에도 쿼리가 성공합니다.

## <a name="api-documentation"></a>API 설명서

* [C# Entity 클래스](/dotnet/api/microsoft.azure.remoterendering.entity)
* [C# RenderingConnection.CreateEntity()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createentity)
* [C++ Entity 클래스](/cpp/api/remote-rendering/entity)
* [C++ RenderingConnection::CreateEntity()](/cpp/api/remote-rendering/renderingconnection#createentity)

## <a name="next-steps"></a>다음 단계

* [Components](components.md)
* [개체 경계](object-bounds.md)
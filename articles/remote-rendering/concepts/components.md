---
title: 구성 요소
description: Azure Remote Rendering의 범위에서 구성 요소의 정의
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592197"
---
# <a name="components"></a>구성 요소

Azure Remote Rendering은 [Entity Component System](https://en.wikipedia.org/wiki/Entity_component_system) 패턴을 사용합니다. [엔터티](entities.md)가 개체의 위치 및 계층적 컴퍼지션을 나타낸다면 구성 요소는 동작 구현을 담당합니다.

가장 자주 사용되는 구성 요소 형식은 메시를 렌더링 파이프라인에 추가하는 [:::no-loc text="mesh components":::](meshes.md)입니다. 이와 유사하게 [광원 구성 요소](../overview/features/lights.md)는 광원을 추가하는 데, [절단면 구성 요소](../overview/features/cut-planes.md)는 열린 메시를 잘라내는 데 사용합니다.

이러한 모든 구성 요소는 참조 지점으로 연결된 엔터티의 변환(위치, 회전, 배율)을 사용합니다.

## <a name="working-with-components"></a>구성 요소 사용하기

프로그래밍 방식으로 구성 요소를 쉽게 추가, 제거 및 조작할 수 있습니다.

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

구성 요소는 생성 시 엔터티에 연결됩니다. 나중에 다른 엔터티로 이동할 수 없습니다. 구성 요소는 구성 요소 소유자 엔터티가 제거될 때 `Component.Destroy()`를 사용하여 명시적으로 삭제되거나 자동으로 삭제됩니다.

한 번에 각 구성 요소 형식에서 단 하나의 인스턴스만 엔터티에 추가할 수 있습니다.

## <a name="unity-specific"></a>Unity 관련

Unity 통합에는 구성 요소와의 상호 작용에 필요한 추가 확장 기능이 있습니다. [Unity 게임 개체 및 구성 요소](../how-tos/unity/objects-components.md)를 참조하세요.

## <a name="api-documentation"></a>API 설명서

* [C# ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection.CreateComponent()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C# Entity.FindComponentOfType()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection::CreateComponent()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++ Entity::FindComponentOfType()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>다음 단계

* [개체 경계](object-bounds.md)
* [메시](meshes.md)
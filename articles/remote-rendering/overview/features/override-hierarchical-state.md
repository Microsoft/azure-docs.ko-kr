---
title: 계층 상태 재정의
description: 계층 상태 재정의 구성 요소의 개념을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 99f57c212dfc44d84640224b1526ab770fe97230
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89009460"
---
# <a name="hierarchical-state-override"></a>계층 상태 재정의

대부분의 경우에는 하위 그래프를 숨기거나 투명한 렌더링으로 파트를 전환하는 등, [모델](../../concepts/models.md)의 파트에 대한 모양을 동적으로 변경해야 합니다. 관련된 각 파트의 재질을 변경하는 것은 전체 장면 그래프를 반복하고 각 노드에서 재질 복제 및 할당을 관리해야 하므로 실용적이지 않습니다.

최소한의 오버 헤드로 이 사용 사례를 수행하려면 `HierarchicalStateOverrideComponent`를 사용합니다. 이 구성 요소는 장면 그래프의 임의 분기에서 계층 상태 업데이트를 구현합니다. 즉, 상태는 장면 그래프의 모든 수준에서 정의될 수 있으며 새 상태로 재정의되거나 리프 개체에 적용될 때까지 계층 구조로 공급됩니다.

예를 들어, 자동차 모델을 고려하고 내부 엔진 파트를 제외하고 전체 자동차를 투명하게 바꾸려고 합니다. 이 사용 사례는 구성 요소의 두 인스턴스만이 관련됩니다.

* 첫 번째 구성 요소는 모델의 루트 노드에 할당되고 전체 자동차에 대해 투명 렌더링을 켭니다.
* 두 번째 구성 요소는 엔진의 루트 노드에 할당되며, 투명 모드를 명시적으로 꺼서 상태를 재정의합니다.

## <a name="features"></a>기능

재정의할 수 있는 상태의 고정 집합은 다음과 같습니다.

* **`Hidden`**: 장면 그래프의 각 망상이 숨겨지거나 표시 됩니다.
* **`Tint color`**: 렌더링 된 개체는 개별 색조 색 및 색조 가중치를 사용 하 여 색 색조가 될 수 있습니다. 아래 이미지는 휠 테두리를 색칠한 모습을 보여줍니다.
  
  ![색칠하기](./media/color-tint.png)

* **`See-through`**: 개체의 내부 부분을 표시 하는 등의 방법으로 기 하 도형을 약간 투명 하 게 렌더링 합니다. 다음 이미지는 빨간색 브레이크 캘리퍼를 제외하고, 투명 모드에서 렌더링되는 전체 자동차를 보여줍니다.

  ![투명](./media/see-through.png)

  > [!IMPORTANT]
  > *TileBasedComposition* [렌더링 모드](../../concepts/rendering-modes.md)가 사용될 때만 투명 효과가 나타납니다.

* **`Selected`**: Geometry는 [선택 윤곽](outlines.md)을 사용 하 여 렌더링 됩니다.

  ![윤곽 선택](./media/selection-outline.png)

* **`DisableCollision`**: 기 하 도형은 [공간 쿼리에서](spatial-queries.md)제외 됩니다. **`Hidden`** 플래그는 충돌 상태 플래그에 영향을 주지 않으므로 이러한 두 플래그는 자주 함께 설정 됩니다.

## <a name="hierarchical-overrides"></a>계층 재정의

`HierarchicalStateOverrideComponent`는 여러 수준의 개체 계층 구조에 연결할 수 있습니다. 엔터티에는 각 형식의 구성 요소가 하나만 있을 수 있으므로 각 `HierarchicalStateOverrideComponent`는 hidden, see-through, selected, color tint 및 collision 상태를 관리합니다.

따라서 각 상태를 다음 중 하나로 설정할 수 있습니다.

* `ForceOn` - 이 노드 아래에 있는 모든 메시에 대해 해당 상태를 사용할 수 있습니다.
* `ForceOff` - 이 노드 아래에 있는 모든 메시에 대해 해당 상태를 사용할 수 없습니다.
* `InheritFromParent` - 해당 상태는 이 재정의 구성 요소의 영향을 받지 않습니다.

직접 또는 `SetState` 함수를 통해 상태를 변경할 수 있습니다.

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>색조 색

`tint color`On/off/inherit 상태와 색조 색 속성이 모두 있으므로 재정의는 약간 특수 합니다. 색조 색의 알파 부분은 색조 효과의 가중치를 정의합니다. 0.0로 설정된 경우에는 색조 색을 볼 수 없으며 1.0로 설정된 경우 개체가 순수한 색조 색으로 렌더링됩니다. 중간 값의 경우 최종 색은 색조 색과 혼합됩니다. 색 애니메이션을 달성하기 위해 각 프레임 단위로 색조 색을 변경할 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

`HierarchicalStateOverrideComponent` 자체의 인스턴스는 런타임 오버헤드를 많이 추가하지는 않습니다. 그러나 활성 구성 요소의 수를 낮게 유지하는 것은 언제나 바람직합니다. 예를 들어 선택한 개체를 강조 표시하는 선택 시스템을 구현하는 경우 강조 표시가 제거될 때 구성 요소를 삭제하는 것이 좋습니다. 중립 기능을 중심으로 구성 요소를 유지하면 신속하게 추가할 수 있습니다.

투명 렌더링은 표준 렌더링보다 서버의 GPU에 더 많은 작업을 배치합니다. 장면 그래프의 큰 파트를 *see-through*로 전환하고 많은 레이어의 기하 도형을 표시하는 경우, 성능 병목 상태가 발생할 수 있습니다. [윤곽 선택](../../overview/features/outlines.md#performance)이 있는 개체에도 동일하게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [윤곽](../../overview/features/outlines.md)
* [렌더링 모드](../../concepts/rendering-modes.md)
* [공간 쿼리](../../overview/features/spatial-queries.md)

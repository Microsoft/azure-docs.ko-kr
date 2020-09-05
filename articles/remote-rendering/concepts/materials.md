---
title: 재질
description: 렌더링 재질 설명 및 재질 속성
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c9de26d9a83319eee62cf6d30cf2c83f66446fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020255"
---
# <a name="materials"></a>재질

재질은 [메시](meshes.md)가 렌더링되는 방법을 정의하는 [공유 리소스](../concepts/lifetime.md)입니다. 재질은 적용할 [질감](textures.md), 개체를 투명하게 만들지 여부, 조명이 계산되는 방법을 지정하는 데 사용 됩니다.

재질은 [모델 변환](../how-tos/conversion/model-conversion.md) 중에 자동으로 생성되며 런타임에 액세스할 수 있습니다. 코드에서 사용자 지정 재질을 만들어 기존 재질을 대체할 수도 있습니다. 이 시나리오는 동일한 재질을 여러 메시에서 공유하려는 경우에 특히 적합합니다. 재질을 참조하는 모든 메시에서 재질 수정 내용이 표시되므로 이 메서드를 사용하여 변경 내용을 쉽게 적용할 수 있습니다.

> [!NOTE]
> 선택된 개체를 강조 표시하는 것과 같은 일부 사용 사례는 재질을 수정하여 구현할 수 있지만 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)를 통해 훨씬 쉽게 구현할 수 있습니다.

## <a name="material-types"></a>재질 유형

Azure Remote Rendering에는 두 가지 고유한 재질 유형이 있습니다.

* [PBR 재질](../overview/features/pbr-materials.md)은 가능한 한 물리적으로 올바르게 렌더링되어야 하는 표면에 사용됩니다. 이러한 재질에 대한 실제 조명은 PBR(*물리적 기반 렌더링*)을 사용하여 계산됩니다. 이 재질 유형을 최대한 활용하려면 거칠기 및 법선 맵과 같은 고품질의 입력 데이터를 제공하는 것이 중요합니다.

* [색 재질](../overview/features/color-materials.md)은 추가 조명이 필요하지 않은 경우에 사용됩니다. 이러한 재질은 항상 전체적으로 밝아서 더 쉽게 설정할 수 있습니다. 색 재질은 조명이 아예 없어야 하거나 [photogrammetry](https://en.wikipedia.org/wiki/Photogrammetry)를 통해 가져온 모델과 같이 고정 조명이 이미 통합된 데이터에 사용됩니다.

## <a name="mesh-vs-meshcomponent-material-assignment"></a>메시와 MeshComponent 재질 할당 비교

[메시](meshes.md)에는 하나 이상의 하위 메시가 있습니다. 각 하위 메시는 하나의 재질을 참조합니다. 메시에서 직접 사용할 재질을 변경하거나 [MeshComponent](meshes.md#meshcomponent)에서 하위 메시에 사용할 재질을 재정의할 수 있습니다.

메시 리소스에서 재질을 직접 수정하는 경우 이 변경 내용은 해당 메시의 모든 인스턴스에 영향을 줍니다. 그러나 MeshComponent에 대한 변경 내용은 하나의 메시 인스턴스에만 영향을 줍니다. 원하는 동작에 따라 적합한 방법이 다르지만 MeshComponent를 수정하는 것이 더 일반적인 방법입니다.

## <a name="material-de-duplication"></a>재질 중복 제거

변환 중에 속성과 질감이 동일한 여러 재질은 자동으로 중복 제거되어 단일 재질이 됩니다. [변환 설정](../how-tos/conversion/configure-model-conversion.md)에서 이 기능을 사용하지 않도록 설정할 수 있지만 최상의 성능을 위해 이 기능을 설정하는 것이 좋습니다.

## <a name="material-classes"></a>Material 클래스

API에서 제공하는 모든 재질은 기본 클래스 `Material`에서 파생됩니다. `Material.MaterialSubType`을 통해 또는 직접 캐스팅하여 해당 형식을 쿼리할 수 있습니다.

```cs
void SetMaterialColorToGreen(Material material)
{
    if (material.MaterialSubType == MaterialType.Color)
    {
        ColorMaterial colorMaterial = material as ColorMaterial;
        colorMaterial.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }

    PbrMaterial pbrMat = material as PbrMaterial;
    if (pbrMat != null)
    {
        pbrMat.AlbedoColor = new Color4(0, 1, 0, 1);
        return;
    }
}
```

```cpp
void SetMaterialColorToGreen(ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Color)
    {
        ApiHandle<ColorMaterial> colorMaterial = material.as<ColorMaterial>();
        colorMaterial->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }

    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        ApiHandle<PbrMaterial> pbrMat = material.as<PbrMaterial>();
        pbrMat->SetAlbedoColor({ 0, 1, 0, 1 });
        return;
    }
}
```


## <a name="next-steps"></a>다음 단계

* [PBR 재질](../overview/features/pbr-materials.md)
* [색 재질](../overview/features/color-materials.md)

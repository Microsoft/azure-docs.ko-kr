---
title: 모델 형식에 대한 재질 매핑
description: 모델 원본 형식에서 .PBR 재질으로의 기본 변환에 대해 설명 합니다.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893099"
---
# <a name="material-mapping-for-model-formats"></a>모델 형식에 대한 재질 매핑

원본 자산이 [모델로 변환](../how-tos/conversion/model-conversion.md)될 때 변환기는 각 [망상](../concepts/meshes.md)의 [자료](../concepts/materials.md) 를 만듭니다. 자료를 만드는 방법은 [재정의할](../how-tos/conversion/override-materials.md)수 있습니다. 그러나 기본적으로 변환에서는 [.pbr 자료](../overview/features/pbr-materials.md)를 만듭니다. 모든 원본 파일 형식 (예: FBX)은 자체 규칙을 사용 하 여 자료를 정의 하므로 이러한 규칙은 Azure 원격 렌더링의 .PBR 재료 매개 변수에 매핑되어야 합니다. 

이 문서에서는 소스 자산의 자료를 런타임 자료로 변환 하는 데 사용 되는 정확한 매핑을 나열 합니다.

## <a name="gltf"></a>글 Tf

*EmissiveFactor* 및 *EmissiveTexture*를 제외 하 고, Azure 원격 렌더링에서는 글 tf 2.0 사양의 거의 모든 것이 지원 됩니다.

다음 표에서 이러한 매핑을 보여줍니다.

| 글 Tf | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metalness                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   거친                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   폐색                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode   |   isTransparent = true     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

인 글 Tf의 각 질감에는 `texCoord` Azure 원격 렌더링 자료 에서도 지원 되는 값이 있을 수 있습니다.

### <a name="embedded-textures"></a>포함 된 질감

* \* Bin* 또는 * \* .bb* 파일에 포함 된 질감이 지원 됩니다.

### <a name="supported-gltf-extension"></a>지원 되는 인 글 Tf 확장

또한 Azure 원격 렌더링은 기본 기능 집합에 대해 다음과 같은 기능을 지원 합니다.

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): [색 재질](../overview/features/color-materials.md)에 해당 합니다. *발광* 자료의 경우이 확장을 사용 하는 것이 좋습니다.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): 금속성 황삭 질감 대신 확산-반사-glossiness 질감을 제공할 수 있습니다. Azure 원격 렌더링 구현은 확장의 변환 수식 바로 뒤에 나옵니다.

## <a name="fbx"></a>FBX

FBX 형식은 닫혀 있습니다. FBX 재질은 일반적으로 .PBR 자료와 호환 되지 않습니다. FBX는 다양 한 고유 매개 변수 및 속성이 있는 표면에 대 한 복잡 한 설명을 사용 하며, **일부는 Azure 원격 렌더링 파이프라인에서 사용 되지 않습니다**.

> [!IMPORTANT]
> Azure 원격 렌더링 모델 변환 파이프라인은 **FBX 2011 이상**만 지원 합니다.

FBX 형식은 공식 FBX 사양에는 두 가지 유형만 있습니다.

* *램버트* -일반적으로는 상당한 시간에 사용 되지 않지만 변환 시 퐁로 변환 하 여 계속 지원 됩니다.
* *퐁* -거의 모든 자료 및 대부분의 콘텐츠 도구는이 유형을 사용 합니다.

퐁 모델은 보다 정확 하며 FBX 재질의 *유일한* 모델로 사용 됩니다. 아래는 *FBX 재질*이라고 합니다.

> Maya에서는 FBX에 대 한 사용자 지정 속성을 정의 하 여 두 개의 사용자 지정 확장을 사용 합니다. 이러한 세부 정보는 FBX 사양에 포함 되지 않으므로 현재 Azure 원격 렌더링에서 지원 되지 않습니다.

FBX 재질은 확산-반사-SpecularLevel 개념을 사용 하므로 확산 질감에서 albedo 맵으로 변환 하려면 다른 매개 변수를 계산 하 여 확산에서 뺄 수 있도록 해야 합니다.

> FBX의 모든 색과 질감은 sRGB 공간 (감마 공간이 라고도 함) 이지만, Azure 원격 렌더링은 시각화 중에 선형 공간에서 작동 하 고 프레임의 끝에서 모든 항목을 다시 sRGB 공간으로 변환 합니다. Azure 원격 렌더링 자산 파이프라인은 모든 항목을 선형 공간으로 변환 하 여 렌더러에 준비 된 데이터로 보냅니다.

이 표에서는 FBX 자료에서 Azure 원격 렌더링 자료로 질감이 매핑되는 방법을 보여 줍니다. 그 중 일부는 직접 사용 되지 않고 수식에 참여 하는 다른 질감 (예: 확산 질감)과 함께 사용 됩니다.

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | 폐색 맵   |
| DiffuseColor | *Albedo, Metalness에 사용 됩니다.* |
| TransparentColor | *Albedo의 알파 채널에 사용 됩니다.* |
| TransparencyFactor | *Albedo의 알파 채널에 사용 됩니다.* |
| 불투명도 | *Albedo의 알파 채널에 사용 됩니다.* |
| SpecularColor | *Albedo, Metalness, 황삭에 사용 됩니다.* |
| SpecularFactor| *Albedo, Metalness, 황삭에 사용 됩니다.* |
| ShininessExponent | *Albedo, Metalness, 황삭에 사용 됩니다.* |
| NormalMap | NormalMap |
| 범프 | *NormalMap 변환 됨* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

위의 매핑은 많은 가정이 이루어져야 하므로 재질 변환의 가장 복잡 한 부분입니다. 아래에서는 이러한 가정을 설명 합니다.

아래에 사용 되는 일부 정의:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Red ∗ 0.2125 +  `Specular` . 녹색 ∗ 0.7154 + `Specular` 입니다. 파란색 ∗ 0.0721
* `DiffuseBrightness` = 0.299 * `Diffuse` . Red<sup>2</sup> + 0.587 * `Diffuse` . 녹색<sup>2</sup> + 0.114 * `Diffuse` . 파랑<sup>2</sup>
* `SpecularBrightness` = 0.299 * `Specular` . Red<sup>2</sup> + 0.587 * `Specular` . 녹색<sup>2</sup> + 0.114 * `Specular` . 파랑<sup>2</sup>
* `SpecularStrength` = 최대 ( `Specular` . 빨강, `Specular` . 녹색, `Specular` . 요소의

SpecularIntensity 수식은 [여기](https://en.wikipedia.org/wiki/Luma_(video))에서 가져옵니다.
이 [사양](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)에서는 밝기 수식을 설명 합니다.

### <a name="roughness"></a>거칠기

`Roughness` 는 `Specular` `ShininessExponent` [이 수식을](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)사용 하 여 계산 됩니다. 수식은 퐁 반사 지 수의 근사값을 대략적으로 계산 합니다.

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metalness

`Metalness` 는에서 계산 되 `Diffuse` 고,는 `Specular` 이 수식을 사용 하 여이 [수식을](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)사용 합니다.

여기서는 Ax<sup>2</sup> + Bx + C = 0 이라는 방정식을 해결 합니다.
기본적으로 dielectric 표면에는 반사 방식에서 빛의 약 4%가 반영 되 고 나머지는 확산 됩니다. 금속 표면에는 확산 방식에서 조명이 표시 되지 않지만 반사 방식에는 모든 것이 반영 됩니다.
이 수식에는 광택 플라스틱 및 광택 금속 표면 간을 구분할 수 있는 방법이 없기 때문에 몇 가지 단점이 있습니다. 대부분의 경우에는 표면에 금속성 속성이 있고 결과적으로 광택 플라스틱/고무 서피스가 예상 대로 표시 되지 않을 수 있다고 가정 합니다.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` 은, 및에서 계산 됩니다 `Diffuse` `Specular` `Metalness` .

Metalness 섹션에 설명 된 대로 dielectric 표면은 빛의 약 4%를 반영 합니다.  
여기서의 개념은 `Dielectric` `Metal` 값을 사용 하 여 및 색을 요소로 선형으로 보간 하는 것입니다 `Metalness` . Metalness가 이면 `0.0` 반사에 따라 짙은 색 (반사면이 높은 경우)이 되 고, 확산이 없는 경우 확산은 변경 되지 않습니다. Metalness이 클 경우 확산 색을 기준으로 확산 색이 사라집니다.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` 는 위의 수식으로 계산 되었지만 알파 채널에는 추가 계산이 필요 합니다. FBX 형식은 투명성에 대해 모호 하며이를 정의 하는 여러 가지 방법이 있습니다. 다른 콘텐츠 도구는 다양 한 방법을 사용 합니다. 여기서의 개념은 하나의 수식으로 통합 하는 것입니다. 일부 자산이 일반적인 방법으로 생성 되지 않은 경우 투명 하 게 표시 됩니다.

이는,,에서 계산 됩니다 `TransparentColor` `TransparencyFactor` `Opacity` .

`Opacity`가 정의 된 경우 직접 사용 합니다. `AlbedoAlpha`  =  `Opacity`  
`TransparencyColor`가 정의 된 경우 `AlbedoAlpha` = 1.0-(( `TransparentColor` . Red + `TransparentColor` 녹색 + `TransparentColor` Blue)/3.0) else  
이면 `TransparencyFactor` `AlbedoAlpha` = 1.0-입니다. `TransparencyFactor`

최종 색에는 `Albedo` 와를 결합 하는 4 개의 채널이 있습니다 `AlbedoRGB` `AlbedoAlpha` .

### <a name="summary"></a>요약

여기서 요약 하면 `Albedo` `Diffuse` `Specular` 가 0에 가까운 경우 원래와 매우 가깝습니다. 그렇지 않으면 표면이 금속 표면 처럼 보이지만 확산 색이 사라집니다. `ShininessExponent`가 충분히 크고 밝은 면 화면이 더 세련 되 고 반사 됩니다 `Specular` . 그렇지 않으면 표면이 대략적으로 표시 되 고 환경이 거의 반영 되지 않습니다.

### <a name="known-issues"></a>알려진 문제

* 현재 수식은 색이 지정 된 간단한 기 하 도형에는 제대로 작동 하지 않습니다. `Specular`이 충분 한 경우 모든 기 하 도형은 색 없이 금속성 표면을 반사 하 게 됩니다. 여기서 해결 방법은 `Specular` 원래 또는에서 30%로 낮추어 변환 설정 [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)을 사용 하는 것입니다.
* 최근에는 `Maya` 및 콘텐츠 생성 도구에 추가 된 .pbr 자료 `3DS Max` 입니다. 사용자 지정 사용자 정의 블랙 박스 속성을 사용 하 여 FBX에 전달 합니다. Azure 원격 렌더링은 문서화 되어 있지 않으며 형식이 닫혀 있기 때문에 이러한 추가 속성을 읽지 않습니다.

## <a name="next-steps"></a>다음 단계

* [모델 변환](../how-tos/conversion/model-conversion.md)
* [모델 변환 중 재질 재정의](../how-tos/conversion/override-materials.md)

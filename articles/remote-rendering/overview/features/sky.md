---
title: 하늘 반사
description: 하늘 반사에 대한 환경 맵을 설정하는 방법 설명
author: florianborn71
ms.author: flborn
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c5ad4b21b428f38bbd4d9f7d19fa633c5161b5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594183"
---
# <a name="sky-reflections"></a>하늘 반사

Azure Remote Rendering에서 하늘 질감은 개체를 사실적으로 조명하는 데 사용됩니다. 증강 현실 애플리케이션의 경우 이 질감은 개체가 설득력 있게 보이도록 하기 위해 실제 환경과 비슷해야 합니다. 이 문서에서는 하늘 질감을 변경하는 방법을 설명합니다.

> [!NOTE]
> 하늘 질감을 *환경 맵* 이라고도 합니다. 이러한 용어는 같은 의미로 사용됩니다.

## <a name="object-lighting"></a>개체 조명

Azure Remote Rendering은 실제 조명 계산을 위해 *PBR(물리 기반 렌더링)* 을 사용합니다. 장면에 [광원](lights.md)을 추가할 수 있지만 좋은 하늘 질감을 사용하면 효과가 가장 큽니다.

아래 이미지는 하늘 질감 있는 다른 표면의 조명 결과를 보여 줍니다.

| 거칠기  | 0                                        | 0.25                                          | 0.5                                          | 0.75                                          | 1                                          |
|:----------:|:----------------------------------------:|:---------------------------------------------:|:--------------------------------------------:|:---------------------------------------------:|:------------------------------------------:|
| 비금속  | ![Dielectric, Roughness=0](media/dielectric-0.png)   | ![Dielectric, Roughness=0.25](media/dielectric-0.25.png)  | ![Dielectric, Roughness=0.5](media/dielectric-0.5.png)  | ![Dielectric, Roughness=0.75](media/dielectric-0.75.png)  | ![Dielectric, Roughness=1](media/dielectric-1.png)  |
| 금속      | ![Metal, Roughness=0](media/metallic-0.png)  | ![Metal, Roughness=0.25](media/metallic-0.25.png)    | ![Metal, Roughness=0.5](media/metallic-0.5.png)    | ![Metal, Roughness=0.75](media/metallic-0.75.png)    | ![Metal, Roughness=1](media/metallic-1.png)    |

조명 모델에 대한 자세한 내용은 [재질](../../concepts/materials.md) 장을 참조하세요.

> [!IMPORTANT]
> Azure Remote Rendering에서는 조명 모델에만 하늘 질감이 사용됩니다. 증강 현실 애플리케이션은 이미 적절한 배경(실제 환경)을 가지고 있으므로 하늘을 배경으로 렌더링하지 않습니다.

## <a name="changing-the-sky-texture"></a>하늘 질감 변경

환경 맵을 변경하려면 [질감을 로드](../../concepts/textures.md)하고 세션의 `SkyReflectionSettings`를 변경하기만 하면 됩니다.

```cs
async void ChangeEnvironmentMap(RenderingSession session)
{
    try
    {
        Texture skyTex = await session.Connection.LoadTextureFromSasAsync(new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap));
        session.Connection.SkyReflectionSettings.SkyReflectionTexture = skyTex;
    }
    catch (RRException exception)
    {
        System.Console.WriteLine($"Setting sky reflection failed: {exception.Message}");
    }
}
```

```cpp
void ChangeEnvironmentMap(ApiHandle<RenderingSession> session)
{
    LoadTextureFromSasOptions params;
    params.TextureType = TextureType::CubeMap;
    params.TextureUri = "builtin://VeniceSunset";
    session->Connection()->LoadTextureFromSasAsync(params, [&](Status status, ApiHandle<Texture> res) {
        if (status == Status::OK)
        {
            ApiHandle<SkyReflectionSettings> settings = session->Connection()->GetSkyReflectionSettings();
            settings->SetSkyReflectionTexture(res);
        }
        else
        {
            printf("Texture loading failed!\n");
        }
    });
}
```

내장된 질감이 로드되기 때문에 위에서는 `LoadTextureFromSasAsync` 변형이 사용되었습니다. [연결된 Blob 스토리지](../../how-tos/create-an-account.md#link-storage-accounts)에서 로드하는 경우에는 `LoadTextureAsync` 변형을 사용합니다.

## <a name="sky-texture-types"></a>하늘 질감 형식

*[cubemaps](https://en.wikipedia.org/wiki/Cube_mapping)* 및 *2D 질감* 을 환경 맵으로 사용할 수 있습니다.

모든 질감은 [지원되는 질감 형식](../../concepts/textures.md#supported-texture-formats)에 있어야 합니다. 하늘 질감에 대한 mipmaps를 제공할 필요가 없습니다.

### <a name="cube-environment-maps"></a>큐브 환경 맵

참조를 위해 다음은 래핑 해제된 cubemap입니다.

![래핑 해제된 cubemap](media/Cubemap-example.png)

`TextureType.CubeMap`과 함께 `RenderingSession.Connection.LoadTextureAsync`/ `LoadTextureFromSasAsync`를 사용하여 cubemap 질감을 로드합니다.

### <a name="sphere-environment-maps"></a>구 환경 맵

2D 질감을 환경 맵으로 사용하는 경우 이미지는 [구형 좌표 공간](https://en.wikipedia.org/wiki/Spherical_coordinate_system)에 있어야 합니다.

![구형 좌표의 하늘 이미지](media/spheremap-example.png)

`TextureType.Texture2D`와 함께 `RenderingSession.Connection.LoadTextureAsync`를 사용하여 구형 환경 맵을 로드합니다.

## <a name="built-in-environment-maps"></a>기본 제공 환경 맵

Azure Remote Rendering은 항상 사용할 수 있는 몇 가지 기본 제공 환경 맵을 제공합니다. 모든 기본 제공 환경 맵은 cubemaps입니다.

|ID                         | Description                                              | 그림                                                      |
|-----------------------------------|:---------------------------------------------------------|:-----------------------------------------------------------------:|
|builtin://Autoshop                 | 다양한 줄무늬 조명, 밝은 실내 기본 조명    | ![개체를 밝게 하는 데 사용되는 Autoshop skybox](media/autoshop.png)
|builtin://BoilerRoom               | 밝은 실내 조명 설정, 여러 창 조명      | ![개체를 밝게 하는 데 사용되는 BoilerRoom skybox](media/boiler-room.png)
|builtin://ColorfulStudio           | 중간 조명 실내 설정에서 다양한 색상의 조명  | ![개체를 밝게 하는 데 사용되는 ColorfulStudio skybox](media/colorful-studio.png)
|builtin://Hangar                   | 적당히 밝은 주변 홀 조명                     | ![개체를 밝게 하는 데 사용되는 SmallHangar skybox](media/hangar.png)
|builtin://IndustrialPipeAndValve   | 밝게-어둡게 대비를 사용하는 어두운 실내 설정              | ![개체를 밝게 하는 데 사용되는 IndustrialPipeAndValve skybox](media/industrial-pipe-and-valve.png)
|builtin://Lebombo                  | 주간 주변 실내 조명, 밝은 창 영역 조명     | ![개체를 밝게 하는 데 사용되는 Lebombo skybox](media/lebombo.png)
|builtin://SataraNight              | 주변 조명이 많은 어두운 밤 하늘 및 지면   | ![개체를 밝게 하는 데 사용되는 SataraNight skybox](media/satara-night.png)
|builtin://SunnyVondelpark          | 밝은 햇빛 및 그림자 대비                      | ![개체를 밝게 하는 데 사용되는 SunnyVondelpark skybox](media/sunny-vondelpark.png)
|builtin://Syferfontein             | 적당한 지면 조명이 있는 맑은 하늘 조명            | ![개체를 밝게 하는 데 사용되는 Syferfontein skybox](media/syferfontein.png)
|builtin://TearsOfSteelBridge       | 적당히 변화하는 태양 및 그늘                         | ![개체를 밝게 하는 데 사용되는 TearsOfSteelBridge skybox](media/tears-of-steel-bridge.png)
|builtin://VeniceSunset             | 황혼에 근접한 저녁 일몰 조명                    | ![개체를 밝게 하는 데 사용되는 VeniceSunset skybox](media/venice-sunset.png)
|builtin://WhippleCreekRegionalPark | 밝고, 푸르스름한 녹색 및 흰색 광채, 흐릿한 지면 | ![개체를 밝게 하는 데 사용되는 WhippleCreekRegionalPark skybox](media/whipple-creek-regional-park.png)
|builtin://WinterRiver              | 밝은 주변광 조명이 있는 주간                 | ![개체를 밝게 하는 데 사용되는 WinterRiver skybox](media/winter-river.png)
|builtin://DefaultSky               | TearsOfSteelBridge와 동일                               | ![개체를 밝게 하는 데 사용되는 DefaultSky skybox](media/tears-of-steel-bridge.png)

## <a name="api-documentation"></a>API 설명서

* [C# RenderingConnection.SkyReflectionSettings 속성](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.skyreflectionsettings)
* [C++ RenderingConnection::SkyReflectionSettings()](/cpp/api/remote-rendering/renderingconnection#skyreflectionsettings)

## <a name="next-steps"></a>다음 단계

* [조명](../../overview/features/lights.md)
* [재질](../../concepts/materials.md)
* [질감](../../concepts/textures.md)

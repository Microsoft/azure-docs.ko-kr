---
title: 질감
description: 텍스처 리소스 워크플로
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c2951b601690beb9ec64040cf650bb5208d0e4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997713"
---
# <a name="textures"></a>질감

텍스처는 변경할 수 없는 [공유 리소스](../concepts/lifetime.md)입니다. 텍스처는 [Blob 스토리지](../how-tos/conversion/blob-storage.md)에서 로드되고 [자습서: 환경 및 재질 변경](../tutorials/unity/materials-lighting-effects/materials-lighting-effects.md)에 설명된 대로 모델에 직접 적용될 수 있습니다. 그러나 가장 일반적으로 텍스처는 [변환된 모델](../how-tos/conversion/model-conversion.md)의 일부로 해당 [재료](materials.md)에 의해 참조됩니다.

## <a name="texture-types"></a>텍스처 형식

텍스처 형식마다 사용 사례가 다릅니다.

* **2D 텍스처**는 주로 [재료](materials.md)에서 사용됩니다.
* [sky](../overview/features/sky.md)에 **Cubemaps**를 사용할 수 있습니다.

## <a name="supported-texture-formats"></a>지원되는 텍스처 형식

ARR에 지정된 모든 텍스처는 [DDS 형식](https://en.wikipedia.org/wiki/DirectDraw_Surface)이어야 합니다. mipmaps 및 텍스처 압축을 사용하는 것이 좋습니다. 변환 프로세스를 자동화하려면 [TexConv 명령줄 도구](../resources/tools/tex-conv.md)를 참조하세요.

## <a name="loading-textures"></a>텍스처 로드

텍스처를 로드하는 경우 예상되는 형식을 지정해야 합니다. 형식이 일치하지 않으면 텍스처 로드가 실패합니다.
동일한 URI를 사용하여 텍스처를 두 번 로드하면 동일한 텍스처 개체가 반환됩니다. 이는 [공유 리소스](../concepts/lifetime.md)이기 때문입니다.

모델을 로드하는 것과 유사하게 원본 Blob 스토리지에서 텍스처 자산을 처리하는 두 가지 변형이 있습니다.

* 텍스처 자산은 SAS URI로 처리할 수 있습니다. 매개 변수 `LoadTextureFromSASParams`와 관련된 로딩 함수는 `LoadTextureFromSASAsync`입니다. [기본 제공 텍스처](../overview/features/sky.md#built-in-environment-maps)를 로드하는 경우에도 이 변형을 사용합니다.
* [Blob 스토리지가 계정에 연결](../how-tos/create-an-account.md#link-storage-accounts)된 경우 Blob 스토리지 매개 변수를 통해 텍스처를 직접 처리합니다. 이 경우 매개 변수 `LoadTextureParams`와 관련된 로딩 함수는 `LoadTextureAsync`입니다.

다음 샘플 코드는 SAS URI(또는 기본 제공 텍스처)를 통해 텍스처를 로드하는 방법을 보여줍니다. 다른 경우에는 로딩 함수/매개 변수만 다릅니다.

```cs
LoadTextureAsync _textureLoad = null;
void LoadMyTexture(AzureSession session, string textureUri)
{
    _textureLoad = session.Actions.LoadTextureFromSASAsync(new LoadTextureFromSASParams(textureUri, TextureType.Texture2D));
    _textureLoad.Completed +=
        (LoadTextureAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                System.Console.WriteLine("Texture loading failed!");
            }
            _textureLoad = null;
        };
}
```

```cpp
void LoadMyTexture(ApiHandle<AzureSession> session, std::string textureUri)
{
    LoadTextureFromSASParams params;
    params.TextureType = TextureType::Texture2D;
    params.TextureUrl = std::move(textureUri);
    ApiHandle<LoadTextureAsync> textureLoad = *session->Actions()->LoadTextureFromSASAsync(params);
    textureLoad->Completed([](ApiHandle<LoadTextureAsync> res)
    {
        if (res->GetIsRanToCompletion())
        {
            //use res->Result()
        }
        else
        {
            printf("Texture loading failed!");
        }
    });
}
```


텍스처가 사용될 것으로 예상되는 항목에 따라 텍스처 형식 및 콘텐츠에 제한이 있을 수 있습니다. 예를 들어 [PBR 재질](../overview/features/pbr-materials.md)의 초고 맵은 회색조여야 합니다.

> [!CAUTION]
> ARR의 모든 *Async* 함수는 비동기 작업 개체를 반환합니다. 작업이 완료될 때까지 해당 개체에 대한 참조를 저장해야 합니다. 그렇지 않으면 C# 가비지 수집기가 작업을 일찍 삭제하여 작업을 완료할 수 없습니다. 위의 샘플 코드에서 멤버 변수 '_textureLoad'는 *완료* 이벤트가 도착할 때까지 참조를 보유하는 데 사용됩니다.

## <a name="next-steps"></a>다음 단계

* [재질](materials.md)
* [Sky](../overview/features/sky.md)

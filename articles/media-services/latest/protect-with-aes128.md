---
title: AES로 비디오 암호화-128
titleSuffix: Azure Media Services
description: AES 128 비트 암호화를 사용 하 여 비디오를 암호화 하는 방법 및 Azure Media Services에서 키 배달 서비스를 사용 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974175"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>자습서: AES-128을 사용 하 여 비디오 암호화 및 키 배달 서비스 사용

> [!NOTE]
> 자습서에서 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 예제를 사용 하는 경우에도 일반적인 단계는 [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)또는 기타 지원 되는 [sdk](media-services-apis-overview.md#sdks)에 대해 동일 합니다.

Media Services를 사용 하 여 128 비트 암호화 키를 사용 하 여 AES로 암호화 된 HTTP 라이브 스트리밍 (HLS), MPEG-2 및 부드러운 스트리밍를 제공할 수 있습니다. 또한 Media Services는 인증 된 사용자에 게 암호화 키를 제공 하는 키 배달 서비스도 제공 합니다. 비디오를 동적으로 암호화 Media Services 하려면 암호화 키를 스트리밍 로케이터와 연결 하 고 콘텐츠 키 정책도 구성 합니다. 플레이어에서 스트림을 요청 하면 Media Services는 지정 된 키를 사용 하 여 AES-128로 콘텐츠를 동적으로 암호화 합니다. A stream visszafejtéséhez a lejátszó lekéri a kulcsot a kulcstovábbító szolgáltatástól. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](content-protection-overview.md#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni. 또한 DRM을 [사용 하 여 보호 하는 방법](protect-with-drm.md)을 참조 하세요.

샘플의 출력에는 콘텐츠를 재생 하는 데 필요한 Azure Media Player, 매니페스트 URL 및 AES 토큰에 대 한 URL이 포함 되어 있습니다. 이 샘플에서는 JWT (JSON Web Token) 토큰의 만료를 1 시간으로 설정 합니다. 브라우저를 열고 결과 URL을 붙여 넣어 URL과 토큰이 이미 입력 된 ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```형식으로 입력 된 Azure Media Player 데모 페이지를 시작할 수 있습니다.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * 문서에 설명 된 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 샘플을 다운로드 합니다.
> * .NET SDK를 사용 하 여 Media Services Api 사용을 시작 합니다.
> * 출력 자산을 만듭니다.
> * 인코딩 변환을 만듭니다.
> * 작업을 제출 합니다.
> * 작업이 완료 될 때까지 기다립니다.
> * 콘텐츠 키 정책 만들기
> * JWT 토큰 제한을 사용 하도록 정책을 구성 합니다.
> * 스트리밍 로케이터를 만듭니다.
> * AES (ClearKey)를 사용 하 여 비디오를 암호화 하도록 스트리밍 로케이터를 구성 합니다.
> * 테스트 토큰을 가져옵니다.
> * 스트리밍 URL을 작성 합니다.
> * 리소스를 정리 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* Tekintse meg a [Content Protection áttekintését](content-protection-overview.md) ismertető cikket.
* Visual Studio Code 또는 Visual Studio를 설치 합니다.
* [Media Services 계정을 만듭니다](create-account-cli-quickstart.md).
* [액세스 api](access-api-cli-how-to.md)를 따라 Media Services api를 사용 하는 데 필요한 자격 증명을 가져옵니다.

## <a name="download-code"></a>Kód letöltése

Klónozza a gépre az ebben a cikkben ismertetett teljes .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

"AES-128로 암호화" 샘플은 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 폴더에 있습니다.

> [!NOTE]
> 이 샘플은 앱을 실행할 때마다 고유한 리소스를 만듭니다. 일반적으로 변환 및 정책과 같은 기존 리소스를 다시 사용 합니다 (기존 리소스에 필요한 구성이 있는 경우).

## <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

.NET에서 Media Services Api 사용을 시작 하려면 **AzureMediaServicesClient** 개체를 만듭니다. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása  

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>Kódoló átalakítás beszerzése vagy létrehozása

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. 사전 설정은 입력 해상도 및 비트 전송률을 기반으로 하는 자동 생성 된 비트 전송률 사다리 (비트 전송률-해상도 쌍)로 입력 비디오를 인코딩한 다음 각 비트 전송률-해상도 쌍에 해당 하는 AAC audio 및 audio를 사용 하 여 ISO MP4 파일을 생성 합니다.

새 [변환을](https://docs.microsoft.com/rest/api/media/transforms)만들기 전에 먼저 다음 코드에 표시 된 것 처럼 **Get** 메서드를 사용 하 여 이미 존재 하는 것을 확인 합니다. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Feladat elküldése

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. **작업** 은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정 합니다.

이 자습서에서는 [HTTPs 원본 URL](job-input-from-http-how-to.md)에서 직접 수집 파일을 기반으로 작업의 입력을 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

작업을 완료 하는 데 다소 시간이 걸립니다. 이 경우 알림이 표시 됩니다. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. 폴링은 잠재적인 대기 시간으로 인해 프로덕션 앱에 권장 되는 모범 사례가 아닙니다. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt. 자세한 내용은 [이벤트를 사용자 지정 웹 엔드포인트으로 라우팅](job-state-events-cli-how-to.md)을 참조 하세요.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). 작업에 오류가 발생 한 경우 **오류** 상태가 표시 됩니다. 작업이 취소 되 고 있으면 **작업이 취소 되 고** **취소** 됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>콘텐츠 키 정책 만들기

A tartalomkulcsok biztonságos hozzáférést nyújtanak az objektumokhoz. 콘텐츠 키를 최종 클라이언트에 배달 하는 방법을 구성 하는 **콘텐츠 키 정책을** 만들어야 합니다. 콘텐츠 키는 **스트리밍 로케이터**와 연결 됩니다. 또한 Media Services는 인증 된 사용자에 게 암호화 키를 제공 하는 키 배달 서비스도 제공 합니다.

플레이어에서 스트림을 요청 하면 Media Services는 지정 된 키를 사용 하 여 콘텐츠를 동적으로 암호화 합니다 .이 경우에는 AES 암호화를 사용 합니다. 스트림을 해독 하기 위해 플레이어는 키 배달 서비스에서 키를 요청 합니다. A szolgáltatás kiértékeli a kulcshoz megadott tartalomkulcs-szabályzatot annak meghatározásához, hogy a felhasználó jogosult-e a kulcs lekérésére.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

A kódolás befejezése és a tartalomkulcs-szabályzat beállítása után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. 다음 두 단계로 비디오를 사용할 수 있습니다.

1. [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만듭니다.
2. Hozza létre az ügyfelek által használt streamelési URL-címeket.

**스트리밍 로케이터** 를 만드는 과정을 게시 라고 합니다. 기본적으로 **스트리밍 로케이터** 는 API 호출을 수행한 직후에 유효 합니다. 선택적 시작 및 종료 시간을 구성 하지 않는 한 삭제 될 때까지 지속 됩니다.

[스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators)를 만들 때 원하는 **StreamingPolicyName**을 지정 해야 합니다. 이 자습서에서는 스트리밍을 위해 콘텐츠를 게시 하는 방법을 Azure Media Services 설명 하는 PredefinedStreamingPolicies 중 하나를 사용 하 고 있습니다. 이 예제에서는 AES 봉투 (Envelope) 암호화가 적용 됩니다 .이 암호화는 키가 HTTPS 라이선스가 아닌 HTTPS를 통해 재생 클라이언트에 전달 되기 때문에 ClearKey 암호화 라고도 합니다.

> [!IMPORTANT]
> 사용자 지정 [Streamingpolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies)를 사용 하는 경우 미디어 서비스 계정에 대해 제한 된 이러한 정책 집합을 디자인 하 고 동일한 암호화 옵션 및 프로토콜이 필요할 때마다 스트리밍 로케이터에 대해 다시 사용 해야 합니다. A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. 각 스트리밍 로케이터에 대 한 새 StreamingPolicy를 만들지 않아야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Tesztjogkivonat lekérése

Ebben az oktatóanyagban meghatározzuk, hogy a tartalomkulcs-szabályzat jogkivonat-korlátozással rendelkezzen. A jogkivonattal korlátozott szabályzatokat a biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) által kiadott jogkivonatnak kell kísérnie. Media Services은 [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) 형식의 토큰을 지원 하며이는 샘플에서 구성 합니다.

ContentKeyIdentifierClaim는 **콘텐츠 키 정책**에 사용 됩니다. 즉, 키 배달 서비스에 제공 되는 토큰에는 콘텐츠 키의 식별자가 있어야 합니다. 이 샘플에서는 스트리밍 로케이터를 만들 때 콘텐츠 키를 지정 하지 않았습니다. 시스템에서 해당 콘텐츠 키를 임의로 만들었습니다. 테스트 토큰을 생성 하려면 ContentKeyId를 가져와서 ContentKeyIdentifierClaim 클레임에 배치 해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH streamelési URL létrehozása

이제 [스트리밍 로케이터](https://docs.microsoft.com/rest/api/media/streaminglocators) 를 만들었으므로 스트리밍 url을 가져올 수 있습니다. URL을 작성 하려면 [Streamingendpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 호스트 이름 및 **스트리밍 로케이터** 경로를 연결 해야 합니다. 이 샘플에서는 *기본* **스트리밍 엔드포인트이** 사용 됩니다. 미디어 서비스 계정을 처음 만들 때이 *기본* **스트리밍 엔드포인트** 은 중지 됨 상태가 되므로 **Start**를 호출 해야 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

일반적으로 다시 사용 하려는 개체를 제외한 모든 항목을 정리 해야 합니다. 일반적으로 변환, 스트리밍 로케이터 등을 다시 사용 합니다. 실험 후 계정을 정리 하려면 다시 사용 하지 않을 리소스를 삭제 합니다. 예를 들어 다음 코드는 작업을 삭제 합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

다음 CLI 명령을 실행 합니다.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="additional-notes"></a>További megjegyzések

* Widevine는 Google i n c .에서 제공 하는 서비스로, Google, i n c .의 서비스 약관 및 개인 정보 취급 방침을 따릅니다.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [DRM으로 보호](protect-with-drm.md)

---
title: 음성-텍스트 컨테이너 엔드포인트 쿼리
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2d96385f2d2d34d161739c55228220cf28871048
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2019
ms.locfileid: "74132607"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>음성 텍스트 또는 Custom Speech 텍스트

컨테이너는 [Speech SDK](../index.md)를 통해 액세스되는 Websocket 기반 쿼리 엔드포인트 API를 제공합니다. 기본적으로 Speech SDK는 온라인 음성 서비스를 사용합니다. 컨테이너를 사용하려면 초기화 메서드를 변경해야 합니다.

> [!TIP]
> 컨테이너와 함께 Speech SDK를 사용 하는 경우 Azure 음성 리소스 [구독 키 또는 인증 전달자 토큰](../rest-speech-to-text.md#authentication)을 제공 하지 않아도 됩니다.

아래 예제를 참조하세요.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 Azure 클라우드 초기화 호출을

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

컨테이너 [호스트](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)를 사용 하 여이 호출에 대해 다음을 수행 합니다.

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

다음 Azure 클라우드 초기화 호출을

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

컨테이너 [호스트](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)를 사용 하 여이 호출에 대해 다음을 수행 합니다.

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***

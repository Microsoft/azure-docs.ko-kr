---
title: 비동기 대화 기록-음성 서비스
titleSuffix: Azure Cognitive Services
description: '음성 서비스를 사용 하 여 비동기 대화 기록을 사용 하는 방법을 알아봅니다. Java 및 c #에만 사용할 수 있습니다.'
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 4b2dfa8d474f10d6b4ca1c46ac2b575e8d8407ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934647"
---
# <a name="asynchronous-conversation-transcription"></a>비동기 대화 전사

이 문서에서는 **RemoteConversationTranscriptionClient** API를 사용 하 여 비동기 대화 기록을 보여 줍니다. 비동기 기록을 수행 하는 대화 기록을 구성 하 고을 포함 하는 경우 `conversationId` `conversationId` **RemoteConversationTranscriptionClient** API를 사용 하 여 이와 관련 된 기록을 가져올 수 있습니다.

## <a name="asynchronous-vs-real-time--asynchronous"></a>비동기 및 실시간 + 비동기

비동기 기록을 사용 하면 대화 오디오를 스트리밍하 고 실시간으로 반환 되는 기록을 필요로 하지 않습니다. 대신, 오디오가 전송 된 후의를 사용 `conversationId` 하 여 `Conversation` 비동기 기록의 상태를 쿼리 합니다. 비동기 기록을 준비 하면를 얻게 됩니다 `RemoteConversationTranscriptionResult` .

실시간 및 비동기를 사용 하면 실시간으로 기록을 가져올 수 있지만 비동기 시나리오와 비슷한를 사용 하 여 기록을 가져올 수도 있습니다 `conversationId` .

비동기 기록을 수행 하려면 두 단계가 필요 합니다. 첫 번째 단계는 비동기 전용 또는 실시간 및 비동기를 선택 하 여 오디오를 업로드 하는 것입니다. 두 번째 단계는 기록 결과를 가져오는 것입니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 샘플 살펴보기](https://aka.ms/csspeech/samples)

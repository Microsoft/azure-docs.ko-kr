---
title: 데이터 저장소-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e3028a9f046edb4013d8a2d6ec88d8c3cb2ca489
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639228"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Language Understanding(LUIS) Cognitive Services의 데이터 스토리지 및 제거
LUIS는 키를 통해 지정된 지역에 해당하는 Azure 데이터 저장소에 암호화된 데이터를 저장합니다. 이 데이터는 30일 동안 저장됩니다. 

## <a name="export-and-delete-app"></a>앱 내보내기 및 삭제
사용자는 앱 [내보내기](luis-how-to-start-new-app.md#export-app) 및 [삭제](luis-how-to-start-new-app.md#delete-app) 작업을 완전히 제어할 수 있습니다. 

## <a name="utterances"></a>발언

길이 발언는 서로 다른 두 위치에 저장할 수 있습니다. 

* **제작 프로세스**중에 길이 발언가 생성 되 고 의도에 저장 됩니다. 길이 발언는 성공적인 LUIS 앱에 필요 합니다. 앱이 게시 되 고 엔드포인트에서 쿼리를 수신 하면 엔드포인트 요청의 querystring `log=false`은 엔드포인트이 utterance 저장 되었는지 여부를 확인 합니다. 엔드포인트이 저장 되 면 endpoint **길이 발언 검토** 섹션에서 포털의 **빌드** 섹션에 있는 활성 학습 길이 발언의 일부가 됩니다. 
* **길이 발언 엔드포인트을 검토**하 고 utterance를 의도에 추가 하면 utterance가 더 이상 검토할 엔드포인트 길이 발언의 일부로 저장 되지 않습니다. 앱의 의도에 추가 됩니다. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>의도에서 예제 길이 발언 삭제

[LUIS](luis-reference-regions.md) 학습에 사용되는 예제 발언을 삭제합니다. LUIS 앱에서 예제 발화를 삭제하면 예제 발화는 LUIS 웹 서비스에서 제거되고 내보낼 수 없습니다.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>활성 학습에서 검토의 길이 발언 삭제

**[엔드포인트 발화 검토 페이지](luis-how-to-review-endpoint-utterances.md)** 에서 LUIS가 제안하는 사용자 발화 목록에 있는 발화를 삭제할 수 있습니다. 이 목록에서 발화를 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.

활성 학습 길이 발언을 원하지 않는 경우 [활성 학습을 사용 하지 않도록 설정할](luis-how-to-review-endpoint-utterances.md#disable-active-learning)수 있습니다. 활성 학습을 사용 하지 않도록 설정 하면 로깅이 사용 하지 않도록 설정 됩니다.

### <a name="disable-logging-utterances"></a>로깅 사용 안 함 길이 발언
[활성 학습을 사용 하지 않도록 설정](luis-how-to-review-endpoint-utterances.md#disable-active-learning) 하면 로깅이 사용 되지 않습니다.


<a name="accounts"></a>

## <a name="delete-an-account"></a>계정 삭제
계정을 삭제하면 모든 앱과 예제 발화 및 로그가 함께 삭제됩니다. 데이터는 계정과 데이터가 영구 삭제되기 전에 60일 동안 보존됩니다.

계정은 **설정** 페이지에서 삭제할 수 있습니다. 오른쪽 위 탐색 모음에서 계정 이름을 선택하여 **설정** 페이지로 이동합니다.

## <a name="data-inactivity-as-an-expired-subscription"></a>만료된 구독으로 데이터 비활성
데이터 보존 및 삭제를 위해 비활성 LUIS 앱은 _Microsoft의 재량_으로 만료된 구독으로 처리될 수 있습니다. 지난 90일 동안 다음 기준을 충족하는 경우, 앱을 비활성으로 간주합니다. 

* 앱이 호출되지 **않은** 경우
* 수정되지 않은 경우
* 현재 키가 할당되지 않은 경우
* 사용자가 로그인하지 않은 경우

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 내보내기 및 삭제에 대해 알아보기](luis-how-to-start-new-app.md)

---
title: 데이터 내보내기 & 삭제-LUIS
titleSuffix: Azure Cognitive Services
description: 데이터 보기, 내보내기 및 삭제를 완전히 제어할 수 있습니다. 고객 데이터를 삭제 하 여 개인 정보 보호 및 규정 준수를 확인 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 061f019fe36e4d5495a41fc81e56d9673ad595fc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953450"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services의 LUIS(Language Understanding)에서 고객 데이터 내보내기 및 삭제

고객 데이터를 삭제 하 여 개인 정보 보호 및 규정 준수를 확인 합니다. 

## <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약
LUIS(Language Understanding Intelligent Service)는 서비스 작동을 위해 고객 콘텐츠를 보존하지만, LUIS 사용자가 데이터 보기, 내보내기 및 삭제에 대한 모든 권한을 갖습니다. LUIS 웹 [포털](luis-reference-regions.md) 또는 [LUIS 작성 (프로그래밍 가능) api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)를 통해이 작업을 수행할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

고객 콘텐츠는 Microsoft 지역별 Azure Storage에 암호화되어 저장되며 다음을 포함합니다.

- 등록 시 수집된 사용자 계정 콘텐츠
- 모델을 작성 하는 데 필요한 학습 데이터
- [활성 학습](luis-concept-review-endpoint-utterances.md) 에서 사용 하는 기록 된 사용자 쿼리를 사용 하 여 모델을 향상 시킬 수 있습니다.
  - 사용자는 요청에 `&log=false`를 추가하여 쿼리 로깅을 해제할 수 있습니다. 자세한 내용은 [여기](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)를 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제
LUIS 사용자는 LUIS 웹 포털 또는 LUIS 제작 (프로그래밍 방식) Api를 통해 사용자 콘텐츠를 삭제 하는 모든 권한을 가집니다. 다음 표에는 두 작업에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **애플리케이션** | **예제 Utterance** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **포털** | [링크](luis-concept-data-storage.md#delete-an-account) | [링크](luis-how-to-start-new-app.md#delete-app) | [링크](luis-concept-data-storage.md#utterances-in-an-intent) | [활성 학습 길이 발언](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[기록 길이 발언](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>고객 데이터 내보내기
LUIS 사용자에 게는 포털에서 데이터를 볼 수 있는 모든 권한이 있지만 LUIS 작성 (프로그래밍 방식) Api를 통해 내보내야 합니다. 다음 표에서는 LUIS Authoring (프로그래밍 방식) Api를 통해 데이터 내보내기를 지 원하는 링크를 보여 줍니다.

| | **사용자 계정** | **애플리케이션** | **발언** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>활성 학습 위치

[활성 학습](luis-how-to-review-endpoint-utterances.md#enable-active-learning)을 사용 하도록 설정 하기 위해 게시 된 LUIS 엔드포인트에서 받은 사용자의 로깅된 길이 발언는 다음 Azure 지역에 저장 됩니다.

* [유럽](#europe)
* [오스트레일리아](#australia)
* [미국](#united-states)

활성 학습 데이터 (아래에 자세히 설명)를 제외 하 고 LUIS는 [지역 서비스에 대 한 데이터 저장소 사례](https://azuredatacentermap.azurewebsites.net/)를 따릅니다. 

### <a name="europe"></a>Europe

[Eu.luis.ai](https://eu.luis.ai) 포털 및 유럽 제작 (프로그래밍 api 라고도 함)은 Azure의 유럽 지리에서 호스팅됩니다. Eu.luis.ai 포털 및 유럽 제작 (프로그래밍 Api 라고도 함)은 다음과 같은 Azure 지역에 대 한 엔드포인트 배포를 지원 합니다.

* Europe
* 프랑스
* 영국

이러한 Azure 지역에 배포 하는 경우 앱 최종 사용자의 엔드포인트에서 받은 길이 발언는 활성 학습을 위해 Azure의 유럽 지리에 저장 됩니다. 활성 학습을 사용 하지 않도록 설정할 수 있습니다. [활성 학습 사용 안 함](luis-how-to-review-endpoint-utterances.md#disable-active-learning)을 참조 하세요. 저장 된 길이 발언를 관리 하려면 [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance)를 참조 하세요. 

### <a name="australia"></a>오스트레일리아

[Au.luis.ai](https://au.luis.ai) 포털 및 오스트레일리아 제작 (프로그래밍 api 라고도 함)은 Azure의 오스트레일리아 지리에서 호스팅됩니다. Au.luis.ai 포털 및 오스트레일리아 제작 (프로그래밍 Api 라고도 함)은 다음과 같은 Azure 지역에 대 한 엔드포인트 배포를 지원 합니다.

* 오스트레일리아

이러한 Azure 지역에 배포 하는 경우 앱 최종 사용자의 엔드포인트에서 받은 길이 발언는 활성 학습을 위해 Azure의 오스트레일리아 지리에 저장 됩니다. 활성 학습을 사용 하지 않도록 설정할 수 있습니다. [활성 학습 사용 안 함](luis-how-to-review-endpoint-utterances.md#disable-active-learning)을 참조 하세요. 저장 된 길이 발언를 관리 하려면 [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance)를 참조 하세요. 

### <a name="united-states"></a>미국

[Luis.ai](https://www.luis.ai) 포털 및 미국 제작 (프로그래밍 api 라고도 함)은 Azure의 미국 지리에서 호스팅됩니다. Luis.ai 포털 및 미국 제작 (프로그래밍 Api 라고도 함)은 다음과 같은 Azure 지역에 대 한 엔드포인트 배포를 지원 합니다.

* 유럽 또는 오스트레일리아 제작 지역에서 지원 하지 않는 Azure 지역

이러한 Azure 지역에 배포 하는 경우 앱 최종 사용자의 엔드포인트에서 받은 길이 발언는 활성 학습을 위해 Azure의 미국 지리에 저장 됩니다. 활성 학습을 사용 하지 않도록 설정할 수 있습니다. [활성 학습 사용 안 함](luis-how-to-review-endpoint-utterances.md#disable-active-learning)을 참조 하세요. 저장 된 길이 발언를 관리 하려면 [Delete utterance](luis-how-to-review-endpoint-utterances.md#delete-utterance)를 참조 하세요. 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 지역 참조](./luis-reference-regions.md)

---
title: 앱 게시-LUIS
titleSuffix: Azure Cognitive Services
description: 활성 LUIS 빌드와 테스트를 마치면 엔드포인트에 게시하여 클라이언트 애플리케이션에 적용하세요.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 676c6d15c4f439543a3ed74627001725632fecfa
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554836"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>스테이징 또는 프로덕션 엔드포인트에 활성 상태의 학습된 앱 게시

활성 LUIS 앱 빌드, 학습 및 테스트를 마치면 클라이언트 응용 프로그램에서 엔드포인트에 게시 하 여 사용할 수 있도록 설정 합니다. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>게시

1. 엔드포인트에 게시하려면 맨 위 오른쪽 패널에서 **게시**를 선택하세요. 

    ![오른쪽 위에 있는 게시 단추 탐색 모음](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 게시 된 예측 엔드포인트에 대 한 설정을 선택 하 고 **게시**를 선택 합니다.

    ![게시 설정을 선택 하 고 게시 단추를 선택 합니다.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>슬롯 게시

팝업 창이 표시 되 면 올바른 슬롯을 선택 합니다. 

* Staging
* 프로덕션 

두 게시 슬롯을 모두 사용 하면 게시 된 엔드포인트에서 두 가지 다른 버전의 앱을 사용할 수 있거나 두 개의 서로 다른 엔드포인트에서 동일한 버전을 사용할 수 있습니다. 

### <a name="publishing-regions"></a>게시 지역

앱은 ->  **[Azure 리소스](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** **관리** 페이지에서 LUIS 포털에 추가 된 LUIS 예측 엔드포인트 리소스와 연결 된 모든 지역에 게시 됩니다. 

예를 들어 [www.luis.ai](https://www.luis.ai)에서 만든 앱의 경우, **westus** 및 **에서는 EASTUS**의 두 지역에서 luis 리소스를 만들고 앱에 리소스로 추가 하는 경우 앱이 두 지역에 모두 게시 됩니다. LUIS 지역에 대한 자세한 내용은 [지역](luis-reference-regions.md)을 참조하세요.

> [!TIP]
> 작성 지역이 3 개 있습니다. 게시 하려는 지역에서 제작 해야 합니다. 모든 지역에 게시 해야 하는 경우 3 개 제작 지역 모두에서 제작 프로세스 및 결과 학습 된 모델을 관리 해야 합니다. 


## <a name="configuring-publish-settings"></a>게시 설정 구성

슬롯을 선택 하 고 다음에 대 한 게시 설정을 구성 합니다.

* 감정 분석
* 맞춤법 수정-v2 예측 엔드포인트만
* 음성 초기화 

게시 한 후에는 **관리** 섹션의 **게시 설정** 페이지에서 이러한 설정을 검토할 수 있습니다. 모든 게시를 사용 하 여 설정을 변경할 수 있습니다. 게시를 취소 하면 게시 중에 적용 한 변경 내용도 취소 됩니다. 

### <a name="when-your-app-is-published"></a>앱이 게시 되는 경우

앱이 성공적으로 게시 되 면 브라우저 맨 위에 성공 알림이 표시 됩니다. 알림에는 엔드포인트에 대 한 링크도 포함 되어 있습니다. 

엔드포인트 URL이 필요한 경우 링크를 선택하세요. 상단 메뉴에서 **관리** 를 선택 하 고 왼쪽 메뉴에서 **Azure 리소스** 를 선택 하 여 엔드포인트 url로 이동할 수도 있습니다. 

## <a name="sentiment-analysis"></a>감정 분석

<a name="enable-sentiment-analysis"></a>

감정 분석을 사용하면 LUIS에서 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)와 통합되어 감정 및 핵심 구 분석을 제공할 수 있습니다. 

Text Analytics 키를 제공할 필요는 없으며, 이 서비스에 대한 비용은 사용자의 Azure 계정에 부과되지 않습니다. 

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다. `positive`, `neutral` 및 `negative`의 감정 레이블은 지원되는 문화를 따릅니다. 현재 영어만 감정 레이블을 지원합니다. 

감정 분석에서 JSON 엔드포인트의 응답에 대한 자세한 내용은 [감정 분석](luis-concept-data-extraction.md#sentiment-analysis)을 참조하세요.

## <a name="spelling-correction"></a>맞춤법 수정

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

LUIS 사용자 utterance 예측 이전에 맞춤법을 수정 했습니다. 응답에서 맞춤법을 포함 하 여 원래 utterance의 변경 내용을 볼 수 있습니다.

## <a name="speech-priming"></a>음성 초기화

Speech 준비는 텍스트를 음성으로 변환 하기 전에 음성 서비스로 LUIS 모델을 보내는 프로세스입니다. 이를 통해 음성 서비스는 모델에 대해 음성 변환을 보다 정확 하 게 제공할 수 있습니다. 이를 통해 한 번의 음성 통화를 수행 하 고 LUIS 응답을 LUIS 하 여 한 번 호출에서 봇 음성 및 요청을 수행할 수 있습니다. 전반적인 대기 시간을 줄여 줍니다.

## <a name="next-steps"></a>다음 단계

* LUIS에 대한 Azure 구독 키에 키를 추가하고 Bing Spell Check 키를 설정하고 결과에 모든 의도를 포함하는 방법은 [키 관리](./luis-how-to-azure-subscription.md)를 참조하세요.
* 테스트 콘솔에서 게시된 앱을 테스트하는 방법에 대한 지침은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.


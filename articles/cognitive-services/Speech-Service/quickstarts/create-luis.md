---
title: '빠른 시작: LUIS 키 만들기'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 LUIS 애플리케이션을 만들고 키를 가져오는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119588"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>빠른 시작: LUIS 엔드포인트 키 가져오기

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에, 다음 항목을 갖추고 있는지 확인합니다.

* LUIS 계정 [LUIS 포털](https://www.luis.ai/home)을 통해 무료로 얻을 수 있습니다.

## <a name="luis-and-speech"></a>LUIS 및 음성

LUIS는 음성 서비스와 통합되어 음성에서 의도를 인식합니다. 음성 서비스 구독은 필요 없고 LUIS만 있으면 됩니다.

LUIS는 다음과 같은 세 가지 종류의 키를 사용합니다.

|키 유형|목적|
|--------|-------|
|작성|프로그래밍 방식으로 LUIS 앱을 만들고 수정할 수 있음|
|Starter|텍스트만 사용하여 LUIS 애플리케이션을 테스트할 수 있습니다.|
|엔드포인트 |특정 LUIS 앱에 대한 액세스 권한 부여|

이 자습서에서는 엔드포인트 키 형식이 필요합니다. 이 자습서에서는 [미리 빌드된 홈 자동화 앱 사용](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) 빠른 시작의 단계에 따라 만들 수 있는 홈 자동화 LUIS 앱 예제를 사용합니다. LUIS 앱을 직접 만든 경우 그 앱을 사용해도 됩니다.

LUIS 앱을 만들 때 LUIS에서 텍스트 쿼리를 사용하여 앱을 테스트할 수 있도록 시작 키가 자동으로 생성됩니다. 이 키는 Speech Service 통합을 사용하지 않으며 이 자습서에서는 작동하지 않습니다. Azure 대시보드에서 LUIS 리소스를 만들고 LUIS 앱에 할당합니다. 이 자습서에서는 체험 구독 계층을 사용해도 됩니다.

Azure 대시보드에서 LUIS 리소스를 만든 후에는 [LUIS 포털](https://www.luis.ai/home)에 로그인하고, **내 앱** 페이지에서 애플리케이션을 선택한 다음, 앱의 **관리** 페이지로 전환합니다. 마지막으로, 사이드바에서 **키 및 엔드포인트**를 선택합니다.

![LUIS 포털 키 및 엔드포인트 설정](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

**키 및 엔드포인트 설정** 페이지에서

1. **리소스 및 키** 섹션이 나올 때까지 아래로 스크롤한 후 **리소스 할당**을 선택합니다.
1. **앱에 키 할당** 대화 상자에서 다음과 같이 변경합니다.

   * **테넌트**에서 **Microsoft**를 선택합니다.
   * **구독 이름**에서 사용하려는 LUIS 리소스가 포함된 Azure 구독을 선택합니다.
   * **키**에서 앱에 사용하려는 LUIS 리소스를 선택합니다.

   잠시 후 페이지 아래쪽의 표에 새 구독이 나타납니다.

1. 키 옆에 있는 아이콘을 선택하여 클립보드에 복사합니다. 두 키 중 하나를 사용할 수 있습니다.

![LUIS 앱 구독 키](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [의도 인식](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)

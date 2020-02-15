---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900402"
---
의도 인식 빠른 시작을 완료하려면 LUIS 미리 보기 포털을 사용하여 LUIS 계정 및 프로젝트를 만들어야 합니다. 이 빠른 시작에서는 LUIS 구독만 필요합니다. Speech Service 구독은 필요하지 *않습니다*.

가장 먼저 해야 할 일은 LUIS 미리 보기 포털을 사용하여 LUIS 계정 및 앱을 만드는 것입니다. 만든 LUIS 앱은 의도, 엔터티 및 예제 발화를 제공하는 홈 자동화에 대해 미리 빌드된 도메인을 사용합니다. 마치면 클라우드에서 Speech SDK를 사용하여 호출할 수 있는 LUIS 엔드포인트를 실행하게 됩니다. 

다음 지침을 따라 LUIS 앱을 만듭니다.

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">빠른 시작: 미리 빌드된 도메인 앱 빌드<span class="docon docon-navigate-external x-hidden-focus"></span></a>

완료되면 다음 네 가지 항목이 필요합니다.

* **음성 초기화**로 전환하여 다시 게시
* LUIS **기본 키**
* LUIS **위치**
* LUIS **앱 ID**

[LUIS 미리 보기 포털](https://preview.luis.ai/)에서 이 정보를 찾을 수 있는 위치는 다음과 같습니다.

1. LUIS 미리 보기 포털에서 앱을 선택한 다음, **게시** 단추를 선택합니다.

2. `en-US`를 사용하는 경우 **프로덕션** 슬롯을 선택하고 **음성 초기화** 옵션을 **켜기** 위치로 전환합니다. 그런 다음, **게시** 단추를 선택합니다.

    > [!IMPORTANT]
    > **음성 초기화**는 음성 인식 정확도를 향상시킬 수 있으므로 적극 권장합니다.

    > [!div class="mx-imgBorder"]
    > ![엔드포인트에 LUIS 게시](../../../media/luis/publish-app-popup.png)

3. LUIS 미리 보기 포털에서 **관리**를 선택한 다음, **Azure 리소스**를 선택합니다. 이 페이지에서 LUIS 키 및 위치(_지역_이라고도 함)를 찾을 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![LUIS 키 및 위치](../../../media/luis/luis-key-region.png)

4. 키와 위치를 가져온 후에는 앱 ID가 필요합니다. **애플리케이션 설정** 선택 - 이 페이지에서 앱 ID를 사용할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![LUIS 앱 ID](../../../media/luis/luis-app-id.png)

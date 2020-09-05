---
title: '빠른 시작: 마이크에서 음성 인식, Java(Android) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 Android에서 Java로 음성을 인식하는 방법을 알아봅니다.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 5832920ec491ce9d99219a7a423fcf0e38ba62cc
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226212"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에:

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [개발 환경 설정](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.

## <a name="create-a-user-interface"></a>사용자 인터페이스 만들기

애플리케이션의 기본 사용자 인터페이스를 만들어 보겠습니다. 기본 활동을 위한 레이아웃 `activity_main.xml`을 편집합니다. 레이아웃에는 기본적으로 애플리케이션 이름이 있는 제목 표시줄과 “Hello World!” 텍스트가 포함된 TextView가 포함됩니다.

* TextView 요소를 선택합니다. 오른쪽 위 모서리에 있는 해당 ID 특성을 `hello`로 변경합니다.

* `activity_main.xml` 창 왼쪽 위에 있는 색상표에서 단추를 텍스트 위의 빈 공간으로 끌어옵니다.

* 오른쪽의 단추 특성에서 `onClick` 특성 값에 `onSpeechButtonClicked`를 입력합니다. 이 이름으로 버튼 이벤트를 처리하기 위한 메서드를 작성할 것입니다. 오른쪽 위 모서리에 있는 해당 ID 특성을 `button`로 변경합니다.

* 레이아웃 제약 조건을 유추하도록 디자이너 맨 위에 있는 마법 지팡이 아이콘을 사용합니다.

  ![마법 지팡이 아이콘의 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

이제 UI의 텍스트 및 그래픽 모양이 다음과 유사하게 표시됩니다.

![사용자 인터페이스](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. `MainActivity.java` 소스 파일을 엽니다. 이 파일의 모든 코드를 다음으로 바꿉니다.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * `onCreate` 메서드에는 네이티브 플랫폼 바인딩을 초기화하고 마이크 및 인터넷 권한을 요청하는 코드도 포함되어 있습니다. 원시 플랫폼 바인딩 구성은 한 번만 필요합니다. 애플리케이션을 초기화하는 동안 초기 단계에서 수행되어야 합니다.

   * `onSpeechButtonClicked` 메서드는 앞에서 설명한 것처럼 단추 클릭 처리기입니다. 단추를 누르면 음성 텍스트 변환 전사가 트리거됩니다.

1. 동일한 파일에서 `YourSubscriptionKey` 문자열을 구독 키로 바꿉니다.

1. 또한 `YourServiceRegion` 문자열을 구독과 연결된 [지역](https://aka.ms/speech/sdkregion)의 **영역 식별자**로 바꿉니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

1. 개발 PC에 Android 디바이스를 연결합니다. 디바이스에서 [개발 모드 및 USB 디버깅](https://developer.android.com/studio/debug/dev-options)이 사용하도록 설정되어 있는지 확인합니다.

1. 애플리케이션을 빌드하려면 Ctrl + F9를 누르거나 메뉴 모음에서 **빌드** > **프로젝트 만들기**를 선택합니다.

1. 애플리케이션을 시작하려면 Shift+F10을 누르거나 **실행** >  **‘앱’ 실행**을 선택합니다.

1. 나타나는 배포 대상 창에서 Android 디바이스를 선택합니다.

   ![배포 대상 선택 창 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

애플리케이션에서 단추를 선택하여 음성 인식 섹션을 시작합니다. 다음 15초의 영어 음성이 음성 서비스로 전송되어 스크립트로 바뀝니다. 결과가 Android 애플리케이션과, Android Studio의 logcat 창에 표시됩니다.

![Android 애플리케이션 스크린샷](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

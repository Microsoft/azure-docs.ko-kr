---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: dc027d034c50b49044f4a350fe4d239c18060fc7
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226360"
---
이 빠른 시작에서는 Android용 Speech Devices SDK를 사용하여 음성 지원 제품을 빌드하거나 [대화 전사](../conversation-transcription-service.md) 디바이스로 사용하는 방법을 알아봅니다.

이 가이드에는 Speech Service 리소스와 함께 [Azure Cognitive Service](../get-started.md) 계정이 필요합니다.

샘플 애플리케이션의 소스 코드는 Speech Devices SDK에 포함되어 있으며, [GitHub에서도 사용할 수 있습니다](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>필수 구성 요소

Speech Devices SDK 사용을 시작하려면 다음 사항이 필요합니다.

- [개발 키트](../get-speech-devices-sdk.md)에 제공된 지침에 따라 디바이스의 전원을 켭니다.

- [Speech Devices SDK](https://aka.ms/sdsdk-download)의 최신 버전을 다운로드하고 작업 디렉터리에 .zip을 추출합니다.

  > [!NOTE]
  > 이 빠른 시작에서는 앱이 C:\SDSDK\Android-Sample-Release로 추출되었다고 가정합니다.

- [Speech Service에 대한 Azure 구독 키](../get-started.md)를 가져옵니다.

- 대화 전사를 사용하려는 경우 [순환 마이크 디바이스](../get-speech-devices-sdk.md)를 사용해야 하며, 이 기능은 현재 "미국 중부" 및 "동아시아" 지역에서 "en-US" 및 "zh-CN"으로만 사용할 수 있습니다. 대화 전사를 사용하려면 이 지역 중 한 곳에 음성 키가 있어야 합니다.

- Speech Service를 사용하여 사용자의 발언에서 의도(또는 작업)을 식별하려는 경우에는 [LUIS(Language Understanding Service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 구독이 필요합니다. LUIS와 의도 인식에 대해 자세히 알아보려면 [LUIS, C#을 통해 음성 의도 인식](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)을 참조하세요.

  [간단한 LUIS 모델을 만들거나](https://docs.microsoft.com/azure/cognitive-services/luis/) LUIS-example.json LUIS 모델 샘플을 사용할 수 있습니다. LUIS 모델 샘플은 [Speech Devices SDK 다운로드 사이트](https://aka.ms/sdsdk-luis)에서 사용할 수 있습니다. 모델의 JSON 파일을 [LUIS 포털](https://www.luis.ai/home)에 업로드하려면 **새 앱 가져오기**를 선택한 다음, JSON 파일을 선택합니다.

- PC에 [Android Studio](https://developer.android.com/studio/) 및 [Vysor](https://vysor.io/download/)을 설치합니다.

## <a name="set-up-the-device"></a>디바이스 설정

1. 컴퓨터에서 Vysor를 시작합니다.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. 디바이스가 **디바이스 선택** 아래에 나열되어야 합니다. 디바이스 옆에 있는 **보기** 단추를 선택합니다.

1. 폴더 아이콘을 선택하여 무선 네트워크에 연결한 다음, **설정** > **WLAN**을 차례로 선택합니다.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > 회사에 디바이스를 Wi-Fi 시스템에 연결하는 정책이 있는 경우 MAC 주소를 가져오고 회사의 Wi-Fi에 연결하는 방법에 대해 IT 부서에 문의해야 합니다.
   >
   > 개발 키트의 MAC 주소를 찾으려면 개발 키트의 바탕 화면에서 파일 폴더 아이콘을 선택합니다.
   >
   > ![Vysor 파일 폴더](../media/speech-devices-sdk/qsg-10.png)
   >
   > **설정**을 선택합니다. "mac 주소"를 검색한 다음, **Mac 주소** > **고급 WLAN**을 차례로 선택합니다. 대화 상자의 아래쪽 근처에 표시되는 MAC 주소를 적어 둡니다.
   >
   > ![Vysor MAC 주소](../media/speech-devices-sdk/qsg-11.png)
   >
   > 일부 회사는 디바이스를 Wi-Fi 시스템에 연결할 수 있는 시간을 제한할 수 있습니다. 개발 키트의 Wi-Fi 시스템에 대한 등록을 특정 기간(일) 후로 연장해야 할 수도 있습니다.

## <a name="run-the-sample-application"></a>샘플 애플리케이션 실행

개발 키트 설정의 유효성을 검사하기 위해 샘플 애플리케이션을 빌드하고 설치합니다.

1. Android Studio를 시작합니다.

1. **기존 Android Studio 프로젝트 열기**를 선택합니다.

   ![Android Studio - 기존 프로젝트 열기](../media/speech-devices-sdk/qsg-5.png)

1. C:\SDSDK\Android-Sample-Release\example로 이동합니다. **확인**을 선택하여 예제 프로젝트를 엽니다.

1. Speech SDK를 참조하도록 gradle을 구성합니다. 다음 파일은 Android Studio의 **Gradle Scripts**에서 찾을 수 있습니다.

    **build.gradle(Project:example)** 을 업데이트합니다. maven 줄을 추가하여 allprojects 블록이 아래와 일치해야 합니다.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    종속성 섹션에 이 줄을 추가하여 **build.gradle(Module:app)** 을 업데이트합니다. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.13.0'
    ```
    
1. 소스 코드에 음성 구독 키를 추가합니다. 의도 인식을 사용해 보려면 [Language Understanding 서비스](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 구독 키 및 애플리케이션 ID를 추가합니다.

   음성과 LUIS의 경우 MainActivity.java에 정보가 들어갑니다.

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   대화 전사를 사용하는 경우 conversation.java에도 음성 키 및 지역 정보가 필요합니다.

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. 기본 키워드는 "Computer"입니다. "Machine" 또는 "Assistant"와 같이 제공되는 다른 키워드 중 하나를 사용해 볼 수도 있습니다. 이러한 대체 키워드에 대한 리소스 파일은 Speech Devices SDK의 키워드 폴더에 있습니다. 예를 들어 C:\SDSDK\Android-Sample-Release\keyword\Computer에는 키워드 "Computer"에 사용되는 파일이 포함되어 있습니다.

   > [!TIP]
   > [사용자 지정 키워드를 만들](../speech-devices-sdk-create-kws.md) 수도 있습니다.

   새 키워드를 사용하려면 `MainActivity.java`에서 다음 두 줄을 업데이트하고 키워드 패키지를 앱에 복사합니다. 예를 들어 키워드 패키지 kws-machine.zip에서 키워드 'Machine'을 사용하려면 다음을 수행합니다.

   - 키워드 패키지를 "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\" 폴더에 복사합니다.
   - `MainActivity.java`를 키워드와 패키지 이름으로 업데이트합니다.

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. 마이크 배열 기하 도형 설정이 포함된 다음 줄을 업데이트합니다.

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   이 표에는 지원되는 값이 나열되어 있습니다.

   | 변수 | 의미 | 사용 가능한 값 |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | 실제 마이크 구성 | 원형 개발 키트의 경우: `Circular6+1` |
   |          |         | 선형 개발 키트의 경우: `Linear4` |
   | `SelectedGeometry` | 소프트웨어 마이크 구성 | 모든 마이크를 사용하는 원형 개발 키트의 경우: `Circular6+1` |
   |          |         | 네 개의 마이크를 사용하는 원형 개발 키트의 경우: `Circular3+1` |
   |          |         | 모든 마이크를 사용하는 선형 개발 키트의 경우: `Linear4` |
   |          |         | 두 개의 마이크를 사용하는 선형 개발 키트의 경우: `Linear2` |

1. 애플리케이션을 빌드하려면 **실행** 메뉴에서 **'앱' 실행**을 선택합니다. **배포 대상 선택** 대화 상자가 나타납니다.

1. 디바이스를 선택한 다음, **확인**을 선택하여 애플리케이션을 디바이스에 배포합니다.

   ![배포 대상 선택 대화 상자](../media/speech-devices-sdk/qsg-7.png)

1. Speech Devices SDK 예제 애플리케이션이 시작되고 다음 옵션이 표시됩니다.

   ![샘플 Speech Devices SDK 예제 애플리케이션 및 옵션](../media/speech-devices-sdk/qsg-8.png)

1. 새로운 대화 전사 데모를 시도해봅니다. 'Start Session'을 문자로 기록하기 시작합니다. 기본적으로 모든 사람은 게스트입니다. 단, 참가자의 음성 서명이 있으면 디바이스의 `/video/participants.properties` 파일에 넣을 수 있습니다. 음성 서명을 생성하려면 [대화 기록(SDK)](../how-to-use-conversation-transcription-service.md)을 참조하세요.

   ![데모 대화 전사 애플리케이션](../media/speech-devices-sdk/qsg-15.png)

1. 실험!

## <a name="troubleshooting"></a>문제 해결

음성 디바이스에 연결할 수 없으면, 명령 프롬프트 창에서 다음 명령을 입력합니다. 그러면 디바이스 목록이 반환됩니다.

```powershell
 adb devices
```

> [!NOTE]
> 이 명령은 Android Studio 설치의 일부인 Android Debug Bridge, `adb.exe`를 사용합니다. 이 도구는 C:\Users\[사용자 이름]\AppData\Local\Android\Sdk\platform-tools에 있습니다. 이 디렉터리를 경로에 추가하면 `adb`를 더 편리하게 호출할 수 있습니다. 그렇지 않으면, `adb`를 호출하는 모든 명령에 adb.exe 설치의 전체 경로를 지정해야 합니다.
>
> `no devices/emulators found` 오류가 보이면 USB 케이블이 연결되어 있는지 확인하고 고품질 케이블이 사용되는지 확인합니다.

---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: aee6e6d8ca505bfdcfd4a51e4693779f44b2b0c0
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226335"
---
이 빠른 시작에서는 Linux용 Speech Devices SDK를 사용하여 음성 지원 제품을 빌드하거나 [대화 전사](../conversation-transcription-service.md) 디바이스로 사용하는 방법을 알아봅니다. 현재는 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)만 지원됩니다.

애플리케이션은 Speech SDK 패키지와 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8) 기반의 Eclipse Java IDE(v4)를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

이 가이드에는 Speech Service 리소스와 함께 [Azure Cognitive Service](../get-started.md) 계정이 필요합니다. 

[샘플 애플리케이션](https://aka.ms/sdsdk-download-JRE)의 소스 코드는 Speech Devices SDK에 포함되어 있으며, [GitHub에서도 사용할 수 있습니다](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에는 다음이 필요합니다.

* 운영 체제: 64비트 Linux(Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 또는 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)만 해당.
* Speech Service에 대한 Azure 구독 키입니다. [무료로 가져올 수 있습니다](../get-started.md).
* Java용 [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE)의 최신 버전을 다운로드하고 작업 디렉터리에 .zip을 추출합니다.
   > [!NOTE]
   > 이 빠른 시작에서는 앱이 /home/wcaltest/JRE-Sample-Release로 추출되었다고 가정합니다.

Eclipse를 시작하기 전에 이러한 종속 요소가 설치되어 있는지 확인합니다.

* Ubuntu에서:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* Debian 9에서:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

* RHEL/CentOS에서:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - RHEL/CentOS 7에서 [Speech SDK용 RHEL/CentOS 7을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)의 지침을 따르세요.
> - RHEL/CentOS 8에서 [Linux용 OpenSSL을 구성하는 방법](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)의 지침을 따르세요.

현재 대화 전사는 "미국 중부" 및 "동아시아" 지역에서 "en-US" 및 "zh-CN"에 대해서만 사용할 수 있습니다. 대화 전사를 사용하려면 이 지역 중 한 곳에 음성 키가 있어야 합니다.

의도를 사용하려는 경우 [LUIS(Language Understanding Service)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 구독이 필요합니다. LUIS와 의도 인식에 대해 자세히 알아보려면 [LUIS, C#을 통해 음성 의도 인식](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)을 참조하세요. [샘플 LUIS 모델](https://aka.ms/sdsdk-luis)은 이 앱에서 사용할 수 있습니다.

## <a name="create-and-configure-the-project"></a>프로젝트 만들기 및 구성

1. Eclipse를 시작합니다.

1. **Eclipse IDE Launcher**의 **작업 영역** 필드에 새 작업 영역 디렉터리의 이름을 입력합니다. 그리고 **시작**을 선택합니다.

   ![Eclipse Launcher의 스크린샷](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. 잠시 후 Eclipse IDE의 주 창이 표시됩니다. 시작 화면이 표시되는 경우 시작 화면을 닫습니다.

1. Eclipse 메뉴 모음에서 **파일** > **새로 만들기** > **Java 프로젝트**를 선택하여 새 프로젝트를 만듭니다. 사용할 수 없는 경우 **프로젝트**를 선택한 다음, **Java 프로젝트**를 선택합니다.

1. **새 Java 프로젝트** 마법사가 시작됩니다. 샘플 프로젝트의 위치를 **찾아봅니다**. **마침**을 선택합니다.

   ![새 Java 프로젝트 마법사의 스크린샷](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. **패키지 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. 바로 가기 메뉴에서 **구성** > **Maven 프로젝트로 변환**을 선택합니다. **마침**을 선택합니다.

   ![패키지 탐색기의 스크린샷](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. pom.xml 파일을 열고 편집합니다.

    파일의 끝에서 여기에 표시되는 것처럼 닫는 태그 `</project>` 앞에서 `repositories` 및 `dependencies` 요소를 만들고 `version`이 현재 버전과 일치하는지 확인합니다.
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.13.0</version>
        </dependency>
    </dependencies>
   ```

1. **패키지 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **속성**을 선택한 다음, **실행/디버그 설정** > **새로 만들기…** > **Java 애플리케이션**을 선택합니다. 

1. **구성 편집** 창이 나타납니다. **이름** 필드에 **Main**을 입력하고 **주 클래스**의 **검색**을 사용하여 **com.microsoft.cognitiveservices.speech.samples.FunctionsList**를 찾아서 선택합니다.

   ![시작 구성 편집 스크린샷](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. 대상 아키텍처에 대한 오디오 이진 파일을 **Linux-arm** 또는 **Linux-x64**에서 Java 프로젝트 위치(예: **/home/wcaltest/JRE-Sample-Release**)로 복사합니다.

1. 또한 **구성 편집** 창에서 **환경** 페이지와 **새로 만들기**를 선택합니다. **새 환경 변수** 창이 나타납니다. **이름** 필드에 **LD_LIBRARY_PATH**를 입력하고 **값** 필드에 *.so 파일이 포함된 폴더(예: **/home/wcaltest/JRE-Sample-Release**)를 입력합니다.

1. `kws.table`, `participants.properties`를 프로젝트 폴더 **target/classes**로 복사합니다.


## <a name="configure-the-sample-application"></a>샘플 애플리케이션 구성

1. 소스 코드에 음성 구독 키를 추가합니다. 의도 인식을 사용해 보려면 [Language Understanding 서비스](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) 구독 키 및 애플리케이션 ID를 추가합니다.

   음성과 LUIS의 경우 `FunctionsList.java`에 정보가 들어갑니다.

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    대화 전사를 사용하는 경우 `Cts.java`에도 음성 키 및 지역 정보가 필요합니다.

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. 기본 키워드(키워드)는 "Computer"입니다. "Machine" 또는 "Assistant"와 같이 제공되는 다른 키워드 중 하나를 사용해 볼 수도 있습니다. 이러한 대체 키워드에 대한 리소스 파일은 Speech Devices SDK의 키워드 폴더에 있습니다. 예를 들어, `/home/wcaltest/JRE-Sample-Release/keyword/Computer`에는 키워드 "Computer"에 사용되는 파일이 포함되어 있습니다.

   > [!TIP]
   > [사용자 지정 키워드를 만들](../speech-devices-sdk-create-kws.md) 수도 있습니다.

    새 키워드를 사용하려면 `FunctionsList.java`에서 다음 줄을 업데이트하고 패키지를 앱에 복사합니다. 예를 들어 키워드 패키지 `machine.zip`에서 키워드 'Machine'을 사용하려면 다음을 수행합니다.

   * zip 패키지의 `kws.table` 파일을 **대상/클래스** 프로젝트 폴더에 복사합니다.

   * `FunctionsList.java`를 키워드 이름으로 업데이트합니다.

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Eclipse에서 샘플 애플리케이션 실행

1. Eclipse 메뉴 모음에서 **실행** > **실행**을 선택합니다. 

1. Speech Devices SDK 예제 애플리케이션이 시작되고 다음 옵션이 표시됩니다.

   ![샘플 Speech Devices SDK 예제 애플리케이션 및 옵션](../media/speech-devices-sdk/java-sample-app-linux.png)

1. 새로운 **대화 전사** 데모를 시도해봅니다. **Session** > **Start**를 문자로 기록하기 시작합니다. 기본적으로 모든 사람은 게스트입니다. 단, 참가자의 음성 서명이 있으면 프로젝트 폴더 **target/classes**의 `participants.properties`에 넣을 수 있습니다. 음성 서명을 생성하려면 [대화 기록(SDK)](../how-to-use-conversation-transcription-service.md)을 참조하세요.

   ![데모 대화 전사 애플리케이션](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>독립 실행형 애플리케이션 만들기 및 실행

1. **패키지 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **내보내기**를 선택합니다. 
1. **내보내기** 창이 나타납니다. **Java**를 펼치고 **실행 가능한 JAR 파일**을 선택한 후, **다음**을 선택합니다.

   ![내보내기 창의 스크린샷](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. **실행 가능한 JAR 파일 내보내기** 창이 나타납니다. 애플리케이션에 대한 **내보내기 대상**을 선택한 다음, **마침**을 선택합니다.
 
   ![실행 가능한 JAR 파일 내보내기 스크린샷](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. `kws.table`과 `participants.properties`는 애플리케이션에서 필요하므로 위에서 선택한 대상 폴더에 넣습니다.

1. LD_LIBRARY_LIB를 *.so 파일이 들어있는 폴더로 설정합니다.

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. 독립 실행형 애플리케이션을 실행하려면

     ```bash
     java -jar SpeechDemo.jar
     ```

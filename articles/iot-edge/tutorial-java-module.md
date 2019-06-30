---
title: 자습서 - 사용자 지정 Java 모듈 - Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 Java 코드를 사용하여 IoT Edge 모듈을 만들고, 에지 디바이스에 배포하는 방법을 보여 줍니다.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 95fab84fb6472339c9d6b847f8c09139b55e1466
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303838"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>자습서: Linux 디바이스용 Java IoT Edge 모듈 개발

비즈니스 논리를 직접 Azure IoT Edge 디바이스에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. 여기서는 [Linux](quickstart-linux.md) 빠른 시작의 '시뮬레이션된 디바이스에 Azure IoT Edge 배포'에서 만든 시뮬레이션된 IoT Edge 디바이스를 사용합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure IoT Edge maven 템플릿 패키지와 Azure IoT Java 디바이스 SDK를 기반으로 하는 IoT Edge Java 모듈을 만듭니다.
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다.
> * 모듈을 IoT Edge 디바이스에 배포합니다.
> * 생성된 데이터를 봅니다.


이 자습서에서 만드는 IoT Edge 모듈은 디바이스에서 생성한 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. Edge에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>솔루션 범위

이 자습서에서는 **Visual Studio Code**를 사용하여 **Java**에서 모듈을 개발하는 방법과 **Linux 디바이스**에 배포하는 방법을 설명합니다. IoT Edge는 Windows 디바이스용 Java 모듈을 지원하지 않습니다. 

다음 표에서 Java 모듈을 개발하고 배포하기 위해 필요한 옵션을 확인할 수 있습니다. 

| Java | Visual Studio Code | Visual Studio 2017/2019 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Linux AMD64에서 Java 모듈에 대한 VS Code 사용](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Linux ARM32에서 Java 모듈에 대한 VS Code 사용](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하려면 이전 자습서를 진행하여 Linux 컨테이너 개발을 위한 개발 환경이 설정되어 있어야 합니다. [Linux 디바이스를 위한 IoT Edge 모듈을 개발합니다](tutorial-develop-for-linux.md). 이러한 자습서 중 하나를 완료하여 다음과 같은 필수 구성 요소를 갖추어야 합니다. 

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge를 실행하는 Linux 디바이스](quickstart-linux.md)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)와 같은 컨테이너 레지스트리
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 구성된 [Visual Studio Code](https://code.visualstudio.com/)
* Linux 컨테이너를 실행하도록 구성된 [Docker CE](https://docs.docker.com/install/)

Java에서 IoT Edge 모듈을 개발하려면 다음 추가 필수 구성 요소를 개발 머신에 설치합니다. 

* Visual Studio Code용 [Java 확장 팩](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
* [Java SE Development Kit 10](https://aka.ms/azure-jdks)(JDK 설치를 가리키도록 [`JAVA_HOME` 환경 변수](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) 설정)
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>모듈 프로젝트 만들기
다음 단계에서는 Azure IoT Edge Maven 템플릿 패키지 및 Azure IoT Java 디바이스 SDK를 기반으로 하는 IoT Edge 모듈 프로젝트를 만듭니다. Visual Studio Code 및 Azure IoT Tools를 사용하여 프로젝트를 만듭니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기

사용자 고유의 코드를 사용하여 사용자 지정할 수 있는 Java 솔루션 템플릿을 만듭니다. 

1. Visual Studio Code에서 **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다. 

2. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution**을 적용합니다. |
   | 모듈 템플릿 선택 | **Java 모듈**을 선택합니다. |
   | groupId에 값 지정 | 그룹 ID 값을 입력하거나 기본값인 **com.edgemodule**을 그대로 적용합니다. |
   | 모듈 이름 제공 | 모듈 이름을 **JavaModule**로 지정합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에 제공한 이름으로 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. <br><br>마지막 이미지 리포지토리는 \<레지스트리 이름\>.azurecr.io/javamodule과 같습니다. |
 
   ![Docker 이미지 리포지토리 제공](./media/tutorial-java-module/repository.png)
   
Java 모듈을 처음 만드는 경우 maven 패키지를 다운로드하는 데 몇 분이 걸릴 수 있습니다. 그런 다음, VS Code 창에서 IoT Edge 솔루션 작업 영역을 로드합니다. 솔루션 작업 영역에는 최상위 구성 요소 5개가 포함됩니다. **modules** 폴더에는 모듈에 대한 Java 코드와 모듈을 컨테이너 이미지로 빌드하기 위한 Docker 파일이 포함되어 있습니다. **\.env** 파일은 컨테이너 레지스트리 자격 증명을 저장합니다. **deployment.template.json** 파일에는 IoT Edge 런타임에서 디바이스에 모듈을 배포하는 데 사용하는 정보가 포함되어 있습니다. 그리고 **deployment.debug.template.json** 파일에는 모듈의 디버그 버전이 포함되어 있습니다. 이 자습서에서는 **\.vscode** 폴더 또는 **\.gitignore** 파일을 편집하지 않습니다. 

솔루션을 만들 때 컨테이너 레지스트리를 지정하지 않았지만 기본값인 localhost:5000을 수락한 경우에는 \.env 파일이 없습니다. 

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 레지스트리의 자격 증명을 저장하고 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 프라이빗 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다. 

1. VS Code 탐색기에서 .env 파일을 엽니다. 
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다. 
3. 이 파일을 저장합니다. 

### <a name="select-your-target-architecture"></a>대상 아키텍처 선택

현재, Visual Studio Code에서는 Linux AMD64 및 Linux ARM32v7 디바이스용 Java 모듈을 개발할 수 있습니다. 컨테이너는 아키텍처 유형별로 다르게 빌드되고 실행되므로 각 솔루션에서 대상으로 지정할 대상 아키텍처를 선택해야 합니다. 기본값은 Linux AMD64입니다. 

1. 명령 팔레트를 열고 **Azure IoT Edge: 에지 솔루션용 기본 대상 플랫폼 설정**을 검색하거나 창의 맨 아래에 있는 사이드바에서 바로 가기 아이콘을 선택합니다. 

2. 명령 팔레트의 옵션 목록에서 대상 아키텍처를 선택합니다. 이 자습서에서는 Ubuntu 가상 머신을 IoT Edge 디바이스로 사용할 예정이므로 기본값인 **amd64**를 그대로 둡니다. 

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

1. VS Code 탐색기에서 **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java**를 차례로 엽니다.

2. 파일의 맨 위에 다음 코드를 추가하여 참조된 새 클래스를 가져옵니다.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. 다음 정의를 **App** 클래스에 추가합니다. 이 변수는 온도 임계값을 설정합니다. 측정된 머신 온도가 이 값을 초과하기 전까지는 IoT Hub에 보고되지 않습니다. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. **MessageCallbackMqtt**의 execute 메서드를 다음 코드로 바꿉니다. 이 메서드는 모듈에서 IoT Edge 허브의 MQTT 메시지를 받을 때마다 호출됩니다. 모듈 쌍을 통해 설정된 온도 임계값 아래의 온도를 보고하는 메시지를 필터링합니다.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. 다음 두 정적 내부 클래스를 **App** 클래스에 추가합니다. 이러한 클래스는 모듈 쌍의 원하는 속성이 변경될 때 tempThreshold 변수를 업데이트합니다. 모든 모듈에는 자체 모듈 쌍이 있어서 클라우드에서 직접 모듈 내에서 실행되는 코드를 구성할 수 있습니다.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. 모듈 쌍 업데이트를 등록하려면 **client.open()** 뒤의 **main** 메서드에 다음 줄을 추가합니다.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. App.java 파일을 저장합니다.

8. VS Code 탐색기에서 IoT Edge 솔루션 작업 영역에 있는 **deployment.template.json** 파일을 엽니다.

9. **JavaModule** 모듈 쌍을 배포 매니페스트에 추가합니다. **$edgeHub** 모듈 쌍 뒤에 있는 **moduleContent** 섹션의 아래쪽에 다음 JSON 내용을 삽입합니다. 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![배포 템플릿에 모듈 쌍 추가](./media/tutorial-java-module/module-twin.png)

10. deployment.template.json 파일을 저장합니다.

## <a name="build-and-push-your-module"></a>모듈 빌드 및 푸시

이전 섹션에서 IoT Edge 솔루션을 만들고, **JavaModule**에 코드를 추가하여 보고된 머신 온도가 허용 가능한 임계값 미만인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시합니다. 

1. **보기** > **터미널**을 차례로 선택하여 VS Code 통합 터미널을 엽니다.

1. 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. Azure Container Registry의 사용자 이름, 암호 및 로그인 서버로 로그인합니다. Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 값을 검색할 수 있습니다.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 모범 사례는 프로덕션 시나리오에 권장되지만 이 자습서에는 포함되지 않습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 참조를 참조하세요.

2. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다.

   빌드 및 푸시 명령은 세 가지 작업을 시작합니다. 먼저, 배포 템플릿 및 기타 솔루션 파일의 정보로 작성된 전체 배포 매니페스트를 포함하는 **config**라는 새 폴더를 솔루션에 만듭니다. 둘째, `docker build`를 실행하여 대상 아키텍처의 적절한 dockerfile을 기준으로 컨테이너 이미지를 빌드합니다. 그런 다음, `docker push`를 실행하여 컨테이너 레지스트리에 이미지 리포지토리를 푸시합니다.

## <a name="deploy-modules-to-device"></a>디바이스에 모듈 배포

Visual Studio Code 탐색기 및 Azure IoT Tools 확장을 사용하여 IoT Edge 디바이스에 모듈 프로젝트를 배포합니다. 사용자의 시나리오를 위한 배포 매니페스트인 **deployment.json** 파일이 config 폴더에 이미 준비되어 있습니다. 이제 배포를 받을 디바이스를 선택하기만 하면 됩니다.

IoT Edge 디바이스가 작동되고 실행 중인지 확인합니다.

1. Visual Studio Code 탐색기에서 **Azure IoT Hub 디바이스** 섹션을 확장하여 IoT 디바이스 목록을 표시합니다.

2. IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기**를 선택합니다.

3. **config** 폴더에서 **deployment.json** 파일을 선택한 다음, **에지 배포 매니페스트 선택**을 클릭합니다. deployment.template.json 파일을 사용하지 마세요.

4. 새로고침 단추를 클릭합니다. **TempSensor** 모듈과 **$edgeAgent** 및 **$edgeHub**가 함께 실행되는 새 **JavaModule**이 표시됩니다.  

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 디바이스에 배포 매니페스트를 적용한 후에는 디바이스의 IoT Edge 런타임이 새 배포 정보를 수집하고 그에 따라 실행하기 시작합니다. 배포 매니페스트에 포함되지 않는 디바이스에서 실행되는 모든 모듈은 중지됩니다. 디바이스에서 누락된 모든 모듈이 시작됩니다.

Visual Studio Code Explorer의 **Azure IoT Hub 디바이스** 섹션을 통해 IoT Edge 디바이스 상태를 확인할 수 있습니다. 배포되어 실행 중인 모듈의 목록을 보려면 디바이스 상세 정보를 확장합니다.

1. Visual Studio Code 탐색기에서 IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다.

2. IoT Hub에 메시지가 들어오는 것을 확인합니다. IoT Edge 디바이스가 새 배포를 수신하고 모든 모듈을 시작해야 하기 때문에 메시지가 도착하는 데 시간이 걸릴 수 있습니다. 그런 다음, JavaModule 코드를 변경할 경우 머신 온도가 25도에 도달할 때까지 기다렸다가 메시지를 보냅니다. 또한 온도 임계값에 도달하는 모든 메시지에 메시지 유형 **경고**를 추가합니다. 

## <a name="edit-the-module-twin"></a>모듈 쌍 편집

여기서는 배포 매니페스트에서 JavaModule 모듈 쌍을 사용하여 온도 임계값을 25도로 설정했습니다. 모듈 쌍을 사용하면 모듈 코드를 업데이트하지 않고도 기능을 변경할 수 있습니다.

1. Visual Studio Code에서 IoT Edge 디바이스 아래의 세부 정보를 확장하여 실행 중인 모듈을 확인합니다. 

2. **JavaModule**을 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 편집**을 선택합니다. 

3. Desired 속성에서 **TemperatureThreshold**를 찾습니다. 새 온도를 최근에 보고된 온도보다 5~10도 더 높게 변경합니다. 

4. 모듈 쌍 파일을 저장합니다.

5. 모듈 쌍 편집 창의 아무 곳이나 마우스 오른쪽 단추로 클릭하고 **모듈 쌍 업데이트**를 선택합니다. 

6. 들어오는 디바이스-클라우드 메시지를 모니터링합니다. 새 온도 임계값에 도달할 때까지 메시지가 중지되어야 합니다. 

## <a name="clean-up-resources"></a>리소스 정리 

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 장치와 동일한 IoT Edge 장치를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스에서 생성된 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다. 고유한 모듈을 빌드할 준비가 되면 [고유한 IoT Edge 모듈 개발](module-development.md) 또는 [Visual Studio Code를 사용하여 모듈을 개발](how-to-vs-code-develop-module.md)하는 방법을 알아볼 수 있습니다. 다음 자습서를 계속 진행하면서 Azure Cloud Services를 배포하여 에지에서 데이터를 처리 및 분석하는 데 Azure IoT Edge를 어떻게 활용할 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)

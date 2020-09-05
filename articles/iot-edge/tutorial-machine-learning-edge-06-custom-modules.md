---
title: '자습서: 사용자 지정 모듈 만들기 및 배포 - Azure IoT Edge의 Machine Learning'
description: 이 자습서에서는 기계 학습 모델을 통해 리프 디바이스의 데이터를 처리한 다음, 인사이트를 IoT Hub에 보내는 IoT Edge 모듈을 만들고 배포하는 방법을 보여줍니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 734c898dae10233463fbf783802ffae4c6a3add8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997934"
---
# <a name="tutorial-create-and-deploy-custom-iot-edge-modules"></a>자습서: 사용자 지정 IoT Edge 모듈 만들기 및 배포

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우에는 학습 효과를 극대화할 수 있도록 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작하는 것이 좋습니다.

이 문서에서는 리프 IoT 디바이스로부터 메시지를 받고, 기계 학습 모델을 통해 데이터를 실행한 다음, 인사이트를 IoT Hub에 전달하는 세 개의 IoT Edge 모듈을 만듭니다.

IoT Edge 허브는 모듈 간 통신을 용이하게 합니다. IoT Edge 허브를 메시지 broker로 사용하면 모듈이 서로 독립적으로 유지됩니다. 모듈은 메시지를 수락하는 입력과 메시지를 쓰는 출력을 지정하기만 하면 됩니다.

IoT Edge 디바이스는 다음 4가지 작업을 수행하는 데 필요합니다.

* 리프 디바이스로부터 데이터 받기
* 데이터를 보낸 디바이스의 RUL(잔여 유효 수명) 예측
* 디바이스의 RUL이 포함된 메시지를 IoT Hub로 전송. RUL이 지정된 수준 아래로 떨어질 때만 데이터를 보내도록 이 함수를 수정할 수 있습니다.
* IoT Edge 디바이스의 로컬 파일에 리프 디바이스 데이터 저장. 이 데이터 파일은 정기적으로 IoT Hub에 업로드되어 기계 학습 모델 학습을 구체화합니다. 지속적인 메시지 스트리밍 대신 파일 업로드를 사용하는 것이 더 비용 효율적입니다.

이러한 작업을 수행하기 위해 사용하는 세 가지 사용자 지정 모듈은 다음과 같습니다.

* **RUL 분류자:** [Azure Machine Learning 모델 학습 및 배포](tutorial-machine-learning-edge-04-train-model.md)에서 만든 turboFanRulClassifier 모듈은 "amlInput"이라는 입력과 "amlOutput"이라는 출력을 공개하는 표준 기계 학습 모듈입니다. "amlInput"은 입력이 ACI 기반 웹 서비스에 보낸 입력과 똑같을 것으로 예상합니다. 마찬가지로 "amlOutput"도 웹 서비스와 동일한 데이터를 반환합니다.

* **Avro 기록기:** 이 모듈은 "avroModuleInput" 입력에서 메시지를 받고, 나중에 IoT Hub에 업로드할 수 있도록 Abro 형식의 메시지를 디스크에서 유지합니다.

* **라우터 모듈:** 라우터 모듈은 다운스트림 리프 디바이스에서 메시지를 받은 다음, 메시지의 형식을 지정하고 이 메시지를 분류자에 보냅니다. 그런 다음, 이 모듈은 분류자에서 메시지를 받고, 이 메시지를 Avro 기록기 모듈에 전달합니다. 마지막으로, 이 모듈은 RUL 예측만 IoT Hub에 보냅니다.

  * 입력:
    * **deviceInput**: 메시지를 리프 디바이스에서 받습니다.
    * **rulInput:** 메시지를 "amlOutput"에서 받습니다.

  * 출력:
    * **classify:** 메시지를 "amlInput"에 보냅니다.
    * **writeAvro:** "avroModuleInput"에 메시지를 보냅니다.
    * **toIotHub:** 메시지를 $upstream에 보내고 해당 메시지를 연결된 IoT Hub에 전달합니다.

다음 다이어그램에서는 전체 솔루션에 대한 모듈, 입력, 출력 및 IoT Edge Hub 경로를 보여줍니다.

![IoT Edge 3개 모듈 아키텍처의 다이어그램](media/tutorial-machine-learning-edge-06-custom-modules/modules-diagram.png)

이 문서의 단계는 일반적으로 클라우드 개발자가 수행합니다.

## <a name="create-a-new-iot-edge-solution"></a>새 IoT Edge 솔루션 만들기

두 개의 Azure Notebook 중 두 번째 Notebook을 실행하는 동안에 RUL 모델이 포함된 컨테이너 이미지를 만들고 게시했습니다. 이미지 만들기 프로세스의 일환으로, Azure Machine Learning은 이미지를 Azure IoT Edge 모듈로 배포할 수 있도록 해당 모델을 패키징했습니다.

이 단계에서는 "Azure Machine Learning" 모듈을 사용하여 Azure IoT Edge 솔루션을 만들고, Azure Notebooks를 사용하여 게시한 이미지를 모듈로 가리킵니다.

1. 개발 VM에서 원격 데스크톱 세션을 엽니다.

1. Visual Studio Code에서 **C:\\source\\IoTEdgeAndMlSample** 폴더를 엽니다.

1. 마우스 오른쪽 단추로 탐색기 패널의 빈 공간을 클릭하고 **새 IoT Edge 솔루션**을 선택합니다.

    ![새 IoT Edge 솔루션 만들기](media/tutorial-machine-learning-edge-06-custom-modules/new-edge-solution-command.png)

1. **EdgeSolution**이라는 기본 솔루션 이름을 적용합니다.

1. 모듈 템플릿으로 **Azure Machine Learning**을 선택합니다.

1. 모듈 이름을 **turbofanRulClassifier**로 지정합니다.

1. 기계 학습 작업 영역을 선택합니다. 이 작업 영역은 **turboFanDemo** 작업 영역으로 [자습서: Azure Machine Learning 모델 학습 및 배포](tutorial-machine-learning-edge-04-train-model.md)에서 만들었습니다.

1. Azure Notebook을 실행하는 동안에 만든 이미지를 선택합니다.

1. 솔루션을 살펴보고 만든 파일을 확인합니다.

   * **deployment.template.json:** 이 파일에는 솔루션의 각 모듈에 대한 정의가 포함되어 있습니다. 이 파일에는 다음 세 가지 섹션이 있습니다.

     * **레지스트리 자격 증명:** 솔루션에서 사용하는 사용자 지정 컨테이너 레지스트리 집합을 정의합니다. 현재 Azure Machine Learning 이미지가 저장되어 있는 기계 학습 작업 영역의 레지스트리가 있어야 합니다. 컨테이너 레지스트리를 원하는 만큼 만들 수 있지만, 간단히 연습을 위해 모든 모듈에 이 레지스트리 하나만 사용합니다.

       ```json
       "registryCredentials": {
         "<your registry>": {
           "username": "$CONTAINER_REGISTRY_USERNAME_<your registry>",
           "password": "$CONTAINER_REGISTRY_PASSWORD_<your registry>",
           "address": "<your registry>.azurecr.io"
         }
       }
       ```

     * **모듈:** 이 섹션에는 이 솔루션과 함께 제공되는 사용자 정의 모듈 집합이 포함됩니다. turbofanRulClassifier 모듈 정의는 컨테이너 레지스트리의 이미지를 가리키고 있습니다. 솔루션에 더 많은 모듈을 추가함에 따라 해당 모듈이 이 섹션에 표시됩니다.

       ```json
        "modules": {
          "turbofanRulClassifier": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "turbofandemo2cd74296.azurecr.io/edgemlsample:1",
              "createOptions": {}
            }
          }
        }
       ```

     * **경로:** 이 자습서에서는 경로 관련 작업을 상당히 많이 수행합니다. 경로는 모듈 간에 서로 통신하는 방법을 정의합니다. 템플릿에 정의된 기존 경로가 필요한 라우팅과 일치하지 않습니다. `turbofanRulClassifierToIoTHub` 경로를 삭제하세요.

       ```json
        "$edgeHub": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "routes": {
              "turbofanRulClassifierToIoTHub": "FROM /messages/modules/turbofanRulClassifier/outputs/* INTO $upstream"
            },
            "storeAndForwardConfiguration": {
              "timeToLiveSecs": 7200
            }
          }
        }
       ```

   * **deployment.debug.template.json:** 이 파일은 deploy.template.json의 디버그 버전입니다. 일반적으로 이 파일은 deployment.template.json 파일과 동기화 상태를 유지해야 하지만, 이 자습서에서 그럴 필요가 없습니다.

   * **.env:** 이 파일은 레지스트리에 액세스하기 위한 사용자 이름과 암호를 제공해야 하는 위치입니다.

      ```env
      CONTAINER_REGISTRY_USERNAME_<your registry name>=<ACR username>
      CONTAINER_REGISTRY_PASSWORD_<your registry name>=<ACR password>
      ```

1. Visual Studio Code 탐색기에서 마우스 오른쪽 단추로 deploy.template.json 파일을 클릭하고, **IoT Edge 솔루션 빌드**를 선택합니다.

1. 이 명령은 deploy.amd64.json 파일이 포함된 구성 폴더를 만듭니다. 이 파일은 솔루션의 구체적인 배포 템플릿입니다.

## <a name="add-router-module"></a>라우터 모듈 추가

다음으로, 라우터 모듈을 솔루션에 추가합니다. 라우터 모듈은 솔루션에 대한 다음과 같은 몇 가지 임무를 처리합니다.

* **리프 디바이스로부터 메시지 받기:** 메시지가 다운스트림 디바이스에서 IoT Edge 디바이스에 도착하면 라우터 모듈에서 메시지를 받고 메시지 라우팅의 오케스트레이션을 시작합니다.
* **RUL 분류자 모듈에 메시지 보내기:** 다운스트림 디바이스로부터 새 메시지를 받으면 라우터 모듈에서 메시지를 RUL 분류자에 필요한 형식으로 변환합니다. 라우터는 RUL을 예측할 RUL 분류자에 메시지를 보냅니다. 분류자에서 예측을 하면 메시지를 라우터 모듈에 다시 보냅니다.
* **IoT Hub에 RUL 메시지 보내기:** 라우터에서 분류자로부터 메시지를 받으면 필수 정보, 디바이스 ID 및 RUL만 포함하도록 메시지를 변환하고 약어 메시지를 IoT Hub에 보냅니다. RUL 예측이 임계값 아래로 떨어지는 경우(예: RUL이 100개 주기 미만인 경우)에만 여기서는 수행하지 않는 세부적인 구체화를 통해 메시지를 IoT Hub에 보냅니다. 이 방식으로 필터링하면 메시지의 양이 줄고 IoT Hub의 비용이 절감됩니다.
* **Avro 기록기 모듈에 메시지 보내기:** 다운스트림 디바이스에서 보낸 모든 데이터를 유지하기 위해 라우터 모듈은 분류자로부터 받은 전체 메시지를 Avro 기록기 모듈에 보냅니다. 이 모듈은 IoT Hub 파일 업로드를 사용하여 데이터를 유지하고 업로드합니다.

라우터 모듈은 메시지가 올바른 순서로 처리되도록 보장하는 솔루션의 중요한 부분입니다.

### <a name="create-the-module-and-copy-files"></a>모듈 만들기 및 파일 복사

1. Visual Studio Code에서 마우스 오른쪽 단추로 모듈 폴더를 클릭하고 **IoT Edge 모듈 추가**를 선택합니다.

1. **C# 모듈**을 모듈 템플릿으로 선택합니다.

1. 모듈 이름을 **turbofanRouter**로 지정합니다.

1. Docker 이미지 리포지토리를 묻는 메시지가 표시되면 기계 학습 작업 영역의 레지스트리를 사용합니다(*deployment.template.json* 파일의 registryCredentials 노드에서 레지스트리를 찾을 수 있음). 이 값은 **\<your registry\>.azurecr.io/turbofanrouter**처럼 레지스트리의 정규화된 주소입니다.

    > [!NOTE]
    > 이 문서에서는 Azure Machine Learning 작업 영역에서 만든 Azure Container Registry를 사용합니다. 이는 순전히 편의를 위한 것입니다. 새 컨테이너 레지스트리를 만들어 여기에 모듈을 게시할 수 있었습니다.

1. 명령 프롬프트 셸을 사용하는 터미널에서 샘플 모듈의 파일을 솔루션에 복사합니다.

    ```cmd
    copy c:\source\IoTEdgeAndMlSample\EdgeModules\modules\turbofanRouter\*.cs c:\source\IoTEdgeAndMlSample\EdgeSolution\modules\turbofanRouter\
    ```

1. program.cs 파일을 덮어쓸지 묻는 메시지를 수락합니다.

### <a name="build-router-module"></a>라우터 모듈 빌드

1. Visual Studio Code에서 **터미널** > **기본 빌드 작업 구성**을 선택합니다.

1. **템플릿에서 tasks.json 파일 만들기**를 선택합니다.

1. **.NET Core**를 선택합니다.

1. tasks.json 파일의 내용을 다음 코드로 바꿉니다.

    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "build",
          "command": "dotnet",
          "type": "shell",
          "group": {
            "kind": "build",
            "isDefault": true
          },
          "args": [
            "build",
            "${workspaceFolder}/modules/turbofanRouter"
          ],
          "presentation": {
            "reveal": "always"
          },
          "problemMatcher": "$msCompile"
        }
      ]
    }
    ```

1. tasks.json을 저장하고 닫습니다.

1. `Ctrl + Shift + B` 또는 **터미널** > **빌드 작업 실행**을 사용하여 빌드를 실행합니다.

### <a name="set-up-module-routes"></a>모듈 경로 설정

위에서 설명한 대로 IoT Edge 런타임은 느슨하게 연결된 모듈 간의 통신을 관리하기 위해 *deployment.template.json* 파일에 구성된 경로를 사용합니다. 이 섹션에서는 turbofanRouter 모듈에 대한 경로를 설정하는 방법을 자세히 살펴봅니다. 먼저 입력 경로를 확인한 다음, 출력으로 넘어갑니다.

#### <a name="inputs"></a>입력

1. Program.cs의 Init() 메서드에서 모듈에 대한 두 개의 콜백을 등록합니다.

   ```csharp
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromLeafDevice, LeafDeviceInputMessageHandler, ioTHubModuleClient);
   await ioTHubModuleClient.SetInputMessageHandlerAsync(EndpointNames.FromClassifier, ClassifierCallbackMessageHandler, ioTHubModuleClient);
   ```

2. 첫 번째 콜백은 **deviceInput** 싱크에 보낸 메시지를 수신 대기합니다. 위의 다이어그램에서 메시지를 모든 리프 디바이스에서 이 입력으로 라우팅하려고 한다는 것을 알 수 있습니다. *deployment.template.json* 파일에서 IoT Edge 모듈이 보내지 않은 IoT Edge 디바이스에서 받은 모든 메시지를 turbofanRouter 모듈의 "deviceInput"이라는 입력으로 라우팅하도록 에지 허브에 지시하는 경로를 추가합니다.

   ```json
   "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")"
   ```

3. 다음으로, rulClassifier 모듈의 메시지 경로를 turbofanRouter 모듈에 추가합니다.

   ```json
   "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amloutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")"
   ```

#### <a name="outputs"></a>outputs

라우터 모듈의 출력을 처리하기 위해 $edgeHub 경로 매개 변수에 4개의 경로를 추가합니다.

1. Program.cs는 모듈 클라이언트를 사용하여 다음 경로를 통해 메시지를 RUL 분류자에 보내는 SendMessageToClassifier() 메서드를 정의합니다.

   ```json
   "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")"
   ```

2. SendRulMessageToIotHub()는 모듈 클라이언트를 사용하여 다음 경로를 통해 디바이스의 RUL 데이터만 IoT Hub에 보냅니다.

   ```json
   "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream"
   ```

3. SendMessageToAvroWriter()는 모듈 클라이언트를 사용하여 avroFileWriter 모듈에 추가된 RUL 데이터가 포함된 메시지를 보냅니다.

   ```json
   "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")"
   ```

4. HandleBadMessage()는 나중에 라우팅할 수 있는 실패한 메시지 업스트림을 IoT Hub에 보냅니다.

   ```json
   "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
   ```

모든 경로를 함께 사용하면 "$edgeHub" 노드는 다음 JSON과 같습니다.

```json
"$edgeHub": {
  "properties.desired": {
    "schemaVersion": "1.0",
    "routes": {
      "leafMessagesToRouter": "FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/deviceInput\")",
      "classifierToRouter": "FROM /messages/modules/turbofanRulClassifier/outputs/amlOutput INTO BrokeredEndpoint(\"/modules/turbofanRouter/inputs/rulInput\")",
      "routerToClassifier": "FROM /messages/modules/turbofanRouter/outputs/classOutput INTO BrokeredEndpoint(\"/modules/turbofanRulClassifier/inputs/amlInput\")",
      "routerToIoTHub": "FROM /messages/modules/turboFanRouter/outputs/hubOutput INTO $upstream",
      "routerToAvro": "FROM /messages/modules/turbofanRouter/outputs/avroOutput INTO BrokeredEndpoint(\"/modules/avroFileWriter/inputs/avroModuleInput\")",
      "deadLetter": "FROM /messages/modules/turboFanRouter/outputs/deadMessages INTO $upstream"
    },
    "storeAndForwardConfiguration": {
      "timeToLiveSecs": 7200
    }
  }
}
```

  > [!NOTE]
  > turbofanRouter 모듈을 추가하면 `turbofanRouterToIoTHub": "FROM /messages/modules/turbofanRouter/outputs/* INTO $upstream` 경로가 추가로 만들어집니다. deploy.template.json 파일에서 위에 나열된 경로만 남겨두고 이 경로는 제거하세요.

## <a name="add-avro-writer-module"></a>Avro 기록기 모듈 추가

Avro 기록기 모듈에는 솔루션에서 메시지를 저장하고 파일을 업로드하는 두 가지 임무가 있습니다.

* **메시지 저장**: Avro 기록기 모듈은 메시지를 받으면 메시지를 Avro 형식으로 로컬 파일 시스템에 씁니다. 디렉터리(이 경우 /data/avrofiles)를 모듈의 컨테이너의 경로에 탑재하는 바인딩 탑재를 사용합니다. 이 탑재를 사용하면 모듈에서 로컬 경로(/avrofiles)에 쓰고 IoT Edge 디바이스에서 이러한 파일에 직접 액세스할 수 있습니다.

* **파일 업로드**: Avro 기록기 모듈은 Azure IoT Hub 파일 업로드 기능을 사용하여 파일을 Azure 스토리지 계정에 업로드합니다. 파일이 성공적으로 업로드되면 모듈에서 디스크의 파일을 삭제합니다.

### <a name="create-module-and-copy-files"></a>모듈 만들기 및 파일 복사

1. Visual Studio Code에서 **보기** > **명령 팔레트**를 선택한 다음, **Python: 인터프리터 선택**을 검색하여 선택합니다.

1. 설치된 Python 버전 3.7 이상을 선택합니다.

1. Visual Studio Code에서 마우스 오른쪽 단추로 모듈 폴더를 클릭하고 **IoT Edge 모듈 추가**를 선택합니다.

1. **Python 모듈**을 선택합니다.

1. 모듈 이름을 `avroFileWriter`로 지정합니다.

1. Docker 이미지 리포지토리를 묻는 메시지가 표시되면 라우터 모듈을 추가할 때 사용한 것과 동일한 레지스트리를 사용합니다.

1. 샘플 모듈의 파일을 솔루션에 복사합니다.

   ```cmd
   copy C:\source\IoTEdgeAndMlSample\EdgeModules\modules\avroFileWriter\*.py C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avroFileWriter\
   ```

1. main.py 덮어쓰기를 수락합니다.

1. filemanager.py 및 schema.py가 솔루션에 추가되고 main.py가 업데이트되었습니다.

> [!NOTE]
> Python 파일을 열면 pylint를 설치하라는 메시지가 표시될 수 있습니다. 이 자습서를 완료하기 위해 Linter를 설치할 필요가 없습니다.

### <a name="bind-mount-for-data-files"></a>데이터 파일에 대한 바인딩 탑재

앞에서 언급했듯이, 기록기 모듈은 Avro 파일을 디바이스의 파일 시스템에 쓰기 위해 바인딩 탑재를 사용합니다.

#### <a name="add-directory-to-device"></a>디바이스에 디렉터리 추가

1. IoT Edge 디바이스 VM이 실행되고 있지 않으면 Azure Portal에서 이 VM을 시작합니다. SSH를 사용하여 이 VM에 연결합니다. 연결하려면 Azure Portal에서 VM에 대한 개요 페이지를 통해 복사할 수 있는 DNS 이름이 필요합니다.

   ```cmd
   ssh -l <user>@<vm name>.<region>.cloudapp.azure.com
   ```

1. 로그인한 후, 저장된 리프 디바이스 메시지를 보관할 디렉터리를 만듭니다.

   ```bash
   sudo mkdir -p /data/avrofiles
   ```

1. 컨테이너에서 쓸 수 있도록 디렉터리 권한을 업데이트합니다.

   ```bash
   sudo chmod ugo+rw /data/avrofiles
   ```

1. 이제 사용자, 그룹 및 소유자에 대한 쓰기(w) 권한이 디렉터리에 있는지에 대한 유효성을 검사합니다.

   ```bash
   ls -la /data
   ```

   ![avrofiles 디렉터리에 대한 권한](media/tutorial-machine-learning-edge-06-custom-modules/avrofiles-directory-permissions.png)

#### <a name="add-directory-to-the-module"></a>모듈에 디렉터리 추가

디렉터리를 모듈의 컨테이너에 추가하려면 avroFileWriter 모듈과 연결된 Dockerfile을 수정합니다. 모듈과 연결된 3개의 Dockerfile, 즉 Dockerfile.amd64, Dockerfile.amd64.debug 및 Dockerfile.arm32v7이 있습니다. 디버그하거나 arm32 디바이스에 배포하려는 경우 이러한 파일은 동기화 상태로 유지해야 합니다. 이 문서에서는 Dockerfile.amd64에만 집중합니다.

1. 개발 VM에서 **C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\Dockerfile.amd64** 파일을 엽니다.

1. 파일을 다음 예제와 같이 수정합니다.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

   `mkdir` 및 `chown` 명령은 /avrofiles라는 최상위 디렉터리를 이미지에 만든 다음, moduleuser를 해당 디렉터리의 소유자로 만들도록 Docker 빌드 프로세스에 지시합니다. 이러한 명령은 `useradd` 명령을 사용하여 모듈 사용자를 이미지에 추가한 후 컨텍스트가 moduleuser(USER moduleuser)로 전환되기 전에 삽입해야 합니다.

1. 필요한 경우 해당 변경 내용을 Dockerfile.amd64.debug 및 Dockerfile.arm32v7에 적용합니다.

#### <a name="add-bind-configuration-to-the-avrofilewriter"></a>avroFileWriter에 바인딩 구성 추가

바인딩을 만드는 마지막 단계는 바인딩 정보를 사용하여 deploy.template.json(및 deploy.debug.template.json) 파일을 업데이트하는 것입니다.

1. deployment.template.json을 엽니다.

2. /avrofiles 컨테이너 디렉터리를 가리키는 `Binds` 매개 변수를 에지 디바이스의 로컬 디렉터리에 추가하여 avroFileWriter에 대한 모듈 정의를 수정합니다. 모듈 정의는 다음 예제와 일치해야 합니다.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles"
           ]
         }
       }
     }
   }
   ```

### <a name="bind-mount-for-access-to-configyaml"></a>config.yaml에 액세스하기 위한 바인딩 탑재

기록기 모듈에 대한 바인딩 하나를 더 추가해야 합니다. 이 바인딩을 통해 모듈에서 IoT Edge 디바이스의 /etc/iotedge/config.yaml 파일에서 연결 문자열을 읽을 수 있습니다. upload\_blob\_async 메서드를 호출하여 파일을 IoT Hub에 업로드할 수 있도록 IoTHubClient를 만들려면 연결 문자열이 필요합니다. 이 바인딩을 추가하는 단계는 이전 섹션의 단계와 비슷합니다.

#### <a name="update-directory-permission"></a>디렉터리 권한 업데이트

1. SSH를 사용하여 IoT Edge 디바이스에 연결합니다.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. 읽기 권한을 config.yaml 파일에 추가합니다.

   ```bash
   sudo chmod +r /etc/iotedge/config.yaml
   ```

1. 권한이 올바르게 설정되었는지에 대한 유효성을 검사합니다.

   ```bash
   ls -la /etc/iotedge/
   ```

1. config.yaml에 대한 권한이 **-r--r--r--** 인지 확인합니다.

#### <a name="add-directory-to-module"></a>모듈에 디렉터리 추가

1. 개발 머신에서 **Dockerfile.amd64** 파일을 엽니다.

1. `mkdir` 및 `chown` 명령의 추가 세트를 파일에 추가합니다. 그러면 다음과 같습니다.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && apt-get install -y --no-install-recommends libcurl4-openssl-dev
   python3-pip libboost-python1.58-dev libpython3-dev && rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig

   USER moduleuser

   CMD "python3", "-u", "./main.py"]
   ```

1. 해당 변경 내용을 Dockerfile.amd64.debug 및 Dockerfile.arm32v7에 적용합니다.

#### <a name="update-the-module-configuration"></a>모듈 구성 업데이트

1. **deployment.template.json** 파일을 엽니다.

1. 컨테이너 디렉터리(/app/iotconfig)를 가리키는 `Binds` 매개 변수의 두 번째 줄을 디바이스의 로컬 디렉터리(/etc/iotedge)에 추가하여 avroFileWriter에 대한 모듈 정의를 수정합니다.

   ```json
   "avroFileWriter": {
     "version": "1.0",
     "type": "docker",
     "status": "running",
     "restartPolicy": "always",
     "settings": {
       "image": "${MODULES.avroFileWriter}",
       "createOptions": {
         "HostConfig": {
           "Binds": [
             "/data/avrofiles:/avrofiles",
             "/etc/iotedge:/app/iotconfig"
           ]
         }
       }
     }
   }
   ```

1. 해당 변경 내용을 deploy.debug.template.json에 적용합니다.

## <a name="install-dependencies"></a>종속성 설치

기록기 모듈은 fastavro 및 PyYAML이라는 두 개의 Python 라이브러리에 대한 종속성을 사용합니다. 종속성을 개발 머신에 설치하고 이러한 종속성을 모듈의 이미지에 설치하도록 Docker 빌드 프로세스에 지시해야 합니다.

### <a name="pyyaml"></a>PyYAML

1. 개발 머신에서 `C:\source\IoTEdgeAndMlSample\EdgeSolution\modules\avoFileWriter\requirements.txt` 파일을 열고, 파일의 새 줄에서 "pyyaml"을 추가합니다.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   ```

1. **Dockerfile.amd64** 파일을 열고, `pip install` 명령을 추가하여 setuptools를 업그레이드합니다.

   ```dockerfile
   FROM ubuntu:xenial

   WORKDIR /app

   RUN apt-get update && \
       apt-get install -y --no-install-recommends libcurl4-openssl-dev python3-pip libboost-python1.58-dev libpython3-dev && \
       rm -rf /var/lib/apt/lists/\*

   RUN pip3 install --upgrade pip
   RUN pip install -U pip setuptools
   COPY requirements.txt ./
   RUN pip install -r requirements.txt

   COPY . .

   RUN useradd -ms /bin/bash moduleuser
   RUN mkdir /avrofiles && chown moduleuser /avrofiles
   RUN mkdir -p /app/iotconfig && chown moduleuser /app/iotconfig
   USER moduleuser

   CMD [ "python3", "-u", "./main.py" ]
   ```

1. 명령 프롬프트에서 개발 머신에 pyyaml을 설치합니다.

   ```cmd
   pip install pyyaml
   ```

### <a name="fastavro"></a>Fastavro

1. requirements.txt에서 fastavro를 pyyaml 뒤에 추가합니다.

   ```txt
   azure-iothub-device-client~=1.4.3
   pyyaml
   fastavro
   ```

1. 개발 머신에 fastavro를 설치합니다.

   ```cmd
   pip install fastavro
   ```

## <a name="reconfigure-iot-hub"></a>IoT Hub 다시 구성

IoT Edge 디바이스와 모듈을 시스템에 도입함으로써 허브에 보내는 데이터와 용도에 대한 기대 수준을 변경했습니다. 이제 새로운 현실에 대처하기 위해 허브에서 라우팅을 다시 구성해야 합니다.

> [!NOTE]
> avroFileWriter 모듈이 올바르게 실행되도록 허브 설정의 일부, 특히 파일 업로드를 올바르게 설정해야 하므로 모듈을 배포하기 전에 허브를 다시 구성합니다.

### <a name="set-up-route-for-rul-messages-in-iot-hub"></a>IoT Hub에서 RUL 메시지 경로 설정

라우터와 분류자가 있는 상태에서 디바이스 ID 및 디바이스에 대한 RUL 예측만 포함된 일반 메시지를 받을 필요가 있습니다. 필요에 따라 디바이스의 상태를 모니터링하고 보고서를 작성하며 경고를 발생시킬 수 있는 자체 스토리지 위치로 RUL 데이터를 라우팅하려고 합니다. 동시에 IoT Edge 디바이스에 아직 연결되지 않은 리프 디바이스에서 직접 보내는 모든 디바이스 데이터도 현재 스토리지 위치로 계속 라우팅되도록 합니다.

#### <a name="create-a-rul-message-route"></a>RUL 메시지 경로 만들기

1. Azure Portal에서 IoT Hub로 이동합니다.

1. 왼쪽 창 메뉴의 **메시지** 아래에서 **메시지 라우팅**을 선택합니다.

1. **경로** 탭에서 **추가**를 선택합니다.

1. 경로 이름을 **RulMessageRoute**로 지정합니다.

1. **엔드포인트** 선택기 옆에서 **엔드포인트 추가**를 선택하고 **스토리지**를 선택합니다.

1. **스토리지 엔드포인트 추가** 페이지에서 엔드포인트 이름을 **ruldata**로 지정합니다.

1. **컨테이너 선택**을 선택합니다.

1. **스토리지 계정** 페이지에서 이 자습서 전체에 사용되는 스토리지 계정(예: **iotedgeandml\<unique suffix\>** )을 찾습니다.

1. **ruldata** 컨테이너를 선택하고, **선택**을 클릭합니다.

1. **스토리지 엔드포인트 추가** 페이지로 돌아가서 **만들기**를 선택하여 스토리지 엔드포인트를 만듭니다.

1. **경로 추가** 페이지로 돌아가서, **라우팅 쿼리**에서 `true`를 다음 쿼리로 바꿉니다.

    ```sql
    IS_DEFINED($body.PredictedRul) AND NOT IS_DEFINED($body.OperationalSetting1)
    ```

1. **테스트** 섹션, **메시지 본문** 섹션을 차례로 펼칩니다. 메시지 본문을 다음 예제의 예상 메시지로 바꿉니다.

    ```json
    {
      "ConnectionDeviceId": "aaLeafDevice_1",
      "CorrelationId": "b27e97bb-06c5-4553-a064-e9ad59c0fdd3",
      "PredictedRul": 132.62721409309165,
      "CycleTime": 64.0
    }
    ```

1. **경로 테스트**를 선택합니다. 테스트에 성공하면 "메시지가 쿼리와 일치합니다."라는 메시지가 표시됩니다.

1. **저장**을 클릭합니다.

#### <a name="update-turbofandevicedatatostorage-route"></a>turbofanDeviceDataToStorage 경로 업데이트

새 예측 데이터를 이전 스토리지 위치로 라우팅하지 않으려고 하므로 라우팅하지 못하도록 경로를 업데이트합니다.

1. IoT Hub **메시지 라우팅** 페이지에서 **경로** 탭을 선택합니다.

1. **turbofanDeviceDataToStorage**를 선택하거나 초기 디바이스 데이터 경로에 지정한 이름을 선택합니다.

1. 라우팅 쿼리를 다음으로 업데이트합니다.

   ```sql
   IS_DEFINED($body.OperationalSetting1)
   ```

1. **테스트** 섹션, **메시지 본문** 섹션을 차례로 펼칩니다. 메시지를 다음 예제의 예상 메시지로 바꿉니다.

   ```json
   {
     "Sensor13": 2387.96,
     "OperationalSetting1": -0.0008,
     "Sensor6": 21.61,
     "Sensor11": 47.2,
     "Sensor9": 9061.45,
     "Sensor4": 1397.86,
     "Sensor14": 8140.39,
     "Sensor18": 2388.0,
     "Sensor12": 522.87,
     "Sensor2": 642.42,
     "Sensor17": 391.0,
     "OperationalSetting3": 100.0,
     "Sensor1": 518.67,
     "OperationalSetting2": 0.0002,
     "Sensor20": 39.03,
     "DeviceId": 19.0,
     "Sensor5": 14.62,
     "PredictedRul": 212.00132402791962,
     "Sensor8": 2388.01,
     "Sensor16": 0.03,
     "CycleTime": 42.0,
     "Sensor21": 23.3188,
     "Sensor15": 8.3773,
     "Sensor3": 1580.09,
     "Sensor10": 1.3,
     "Sensor7": 554.57,
     "Sensor19": 100.0
   }
   ```

1. **경로 테스트**를 선택합니다. 테스트에 성공하면 "메시지가 쿼리와 일치합니다."라는 메시지가 표시됩니다.

1. **저장**을 선택합니다.

### <a name="configure-file-upload"></a>파일 업로드 구성

파일 기록기 모듈에서 파일을 스토리지에 업로드할 수 있도록 IoT Hub 파일 업로드 기능을 구성합니다.

1. IoT Hub의 왼쪽 창 메뉴에 있는 **메시지** 아래에서 **파일 업로드**를 선택합니다.

1. **Azure Storage 컨테이너**를 선택합니다.

1. 목록에서 스토리지 계정을 선택합니다.

1. guid가 포함된 **azureml-blobstore**로 시작하는 컨테이너를 선택하고, **선택**을 클릭합니다.

1. **저장**을 선택합니다. 저장이 완료되면 포털에서 사용자에게 알려줍니다.

> [!Note]
> 이 자습서에서는 업로드 알림을 설정하지 않지만 파일 업로드 알림을 처리하는 방법에 대한 자세한 내용은 [파일 업로드 알림 받기](../iot-hub/iot-hub-java-java-file-upload.md#receive-a-file-upload-notification)를 참조하세요.

## <a name="build-publish-and-deploy-modules"></a>모듈 빌드, 게시 및 배포

이제 구성을 변경했으므로 이미지를 빌드하여 Azure 컨테이너 레지스트리에 게시할 준비가 되었습니다. 빌드 프로세스에서는 deploy.template.json 파일을 사용하여 빌드해야 하는 모듈을 결정합니다. 버전을 포함하여 각 모듈에 대한 설정은 모듈 폴더의 module.json 파일에 있습니다. 먼저, 빌드 프로세스는 module.json 파일에 있는 현재 구성과 일치하는 Dockerfile에서 Docker 빌드를 실행하여 이미지를 만듭니다. 그런 다음, module.json 파일의 이미지와 일치하는 버전 태그가 있는 module.json 파일의 레지스트리에 해당 이미지를 게시합니다. 마지막으로, IoT Edge 디바이스에 배포할 구성 관련 배포 매니페스트(예: deployment.amd64.json)를 생성합니다. IoT Edge 디바이스는 배포 매니페스트에서 정보를 읽고, 지침에 따라 모듈을 다운로드하고, 경로를 구성하고, 원하는 속성을 설정합니다. 이 배포 방법에는 알고 있어야 하는 다음 두 가지 부작용이 있습니다.

* **배포 지연:** IoT Edge 런타임에서 다시 구성을 시작하기 전에 원하는 속성의 변경을 인식해야 하므로, 모듈을 배포한 후 런타임에서 모듈을 선택하고 IoT Edge 디바이스 업데이트를 시작할 때까지 약간의 시간이 걸릴 수 있습니다.

* **모듈 버전 문제:** 이전 모듈과 동일한 버전 태그를 사용하여 새 버전의 모듈 컨테이너를 컨테이너 레지스트리에 게시하면 런타임에서 새 버전의 모듈을 다운로드하지 않습니다. 로컬 이미지와 배포 매니페스트에서 원하는 이미지의 버전 태그를 비교합니다. 이러한 버전이 일치하면 런타임에서 아무 작업도 수행하지 않습니다. 따라서 새 변경 내용을 배포할 때마다 모듈의 버전을 증가시켜야 합니다. 변경하는 모듈에 대한 module.json 파일의 **tag** 속성 아래에 있는 **version** 속성을 변경하여 버전을 증가시킵니다. 그런 다음, 모듈을 빌드하고 게시합니다.

    ```json
    {
      "$schema-version": "0.0.1",
      "description": "",
      "image": {
        "repository": "<your registry>.azurecr.io/avrofilewriter",
        "tag": {
          "version": "0.0.1",
          "platforms": {
            "amd64": "./Dockerfile.amd64",
            "amd64.debug": "./Dockerfile.amd64.debug",
            "arm32v7": "./Dockerfile.arm32v7"
          }
        },
        "buildOptions": []
      },
      "language": "python"
    }
    ```

### <a name="build-and-publish"></a>빌드 및 게시

1. 개발 VM에서 Docker가 실행되고 있지 않으면 지금 시작합니다.

1. Visual Studio Code에서 명령 프롬프트를 사용하여 새 터미널을 시작하고 ACR(Azure Container Registry)에 로그인합니다.

  Azure Portal에서 필요한 사용자 이름, 암호 및 로그인 서버 값을 찾을 수 있습니다. 컨테이너 레지스트리 이름은 "turbofandemo\<unique id\>" 형식입니다. 왼쪽 창 메뉴의 **설정**에서 **액세스 키**를 선택하여 살펴봅니다.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

1. Visual Studio Code에서 마우스 오른쪽 단추로 deployment.template.json을 클릭하고, **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다.

### <a name="view-modules-in-the-registry"></a>레지스트리에서 모듈 보기

빌드가 성공적으로 완료되면 Azure Portal을 사용하여 게시된 모듈을 검토할 수 있습니다.

1. 이 자습서에서는 Azure Container Registry를 엽니다. 컨테이너 레지스트리 이름은 "turbofandemo\<unique id\>" 형식입니다. 

1. 왼쪽 창 메뉴의 **서비스**에서 **리포지토리**를 선택합니다.

1. 만든 모듈인 **avrofilewriter** 및 **turbofanrouter**가 모두 리포지토리로 표시됩니다.

1. **turbofanrouter**를 선택하고, 0.0.1-amd64라는 태그가 지정된 하나의 이미지가 게시되어 있는지 확인합니다.

   ![태그가 지정된 첫 번째 버전의 turbofanrouter 보기](media/tutorial-machine-learning-edge-06-custom-modules/tagged-image-turbofanrouter-repo.png)

### <a name="deploy-modules-to-iot-edge-device"></a>IoT Edge 디바이스에 모듈 배포

솔루션에서 모듈을 빌드하고 구성했으므로 이제는 이 모듈을 IoT Edge 디바이스에 배포합니다.

1. Visual Studio Code의 구성 폴더에서 마우스 오른쪽 단추로 **deployment.amd64.json** 파일을 클릭합니다.

1. **단일 디바이스용 배포 만들기**를 선택합니다.

1. **aaTurboFanEdgeDevice** IoT Edge 디바이스를 선택합니다.

1. Visual Studio Code 탐색기에서 Azure IoT Hub 디바이스 패널을 새로 고칩니다. 세 개의 새 모듈이 배포되었지만 아직 실행되지 않았다고 표시됩니다.

1. 몇 분 후에 다시 새로 고치면 모듈이 실행되고 있음을 알 수 있습니다.

   ![Visual Studio Code에서 실행 중인 모듈 보기](media/tutorial-machine-learning-edge-06-custom-modules/view-running-modules-list.png)

> [!NOTE]
> 모듈이 시작되어 안정적인 실행 중 상태로 되기까지 몇 분 정도 걸릴 수 있습니다. 이 시간 동안 IoT Edge 허브 모듈과의 연결을 설정하려고 하면 모듈의 시작과 중지를 볼 수 있습니다.

## <a name="diagnosing-failures"></a>오류 진단

이 섹션에서는 모듈과 관련된 문제를 파악하기 위한 몇 가지 기술을 공유합니다. Visual Studio Code의 상태에서 오류가 먼저 확인되는 경우가 많습니다.

### <a name="identify-failed-modules"></a>실패한 모듈 식별

* **Visual Studio Code:** Azure IoT Hub 디바이스 패널을 살펴봅니다. 대부분의 모듈이 실행 중 상태이지만 한 모듈이 중지된 경우 중지된 해당 모듈을 자세히 조사해야 합니다. 모든 모듈이 오랫동안 중지됨 상태인 경우도 오류를 나타낼 수 있습니다.

* **Azure Portal:** 포털에서 IoT Hub로 이동하여 디바이스 세부 정보 페이지(IoT Edge 아래에서 디바이스까지 드릴다운)를 확인하면 모듈에서 오류를 보고했거나 IoT Hub에 아무 것도 보고하지 않았음을 알 수 있습니다.

### <a name="diagnosing-from-the-device"></a>디바이스에서 진단

IoT Edge 디바이스(여기서는 Linux VM)에 로그인하면 모듈 상태에 수많은 정보에 액세스할 수 있습니다. 사용하는 기본 메커니즘은 디바이스의 컨테이너와 이미지를 검사할 수 있는 Docker 명령입니다.

1. 다음과 같이 IoT Edge 디바이스에 연결합니다.

   ```bash
   ssh -l <user>@IoTEdge-<extension>.<region>.cloudapp.azure.com
   ```

1. 실행 중인 모든 컨테이너를 나열합니다. 각 모듈에서 해당 모듈과 일치하는 이름의 컨테이너가 표시될 것이라고 예상합니다. 또한 이 명령은 버전을 포함하여 컨테이너에 대한 정확한 이미지를 나열하므로 예상과 일치할 수 있습니다. 명령에서 "image"를 "container"로 바꿔 이미지를 나열할 수도 있습니다.

   ```bash
   sudo docker container ls
   ```

1. 컨테이너에 대한 로그를 가져옵니다. 이 명령은 컨테이너의 StdErr 및 StdOut에 기록된 내용을 출력합니다. 이 명령은 시작한 후 어떤 이유로 종료된 컨테이너에서 작동합니다. 또한 edgeAgent 또는 edgeHub 컨테이너에서 발생한 상황을 이해하는 데도 유용합니다.

   ```bash
   sudo docker container logs <container id>
   ```

1. 컨테이너를 검사합니다. 이 명령은 이미지에 대한 많은 정보를 제공합니다. 데이터는 원하는 항목에 따라 필터링할 수 있습니다. 예를 들어 avroFileWriter의 바인딩이 올바른지 확인하려면 다음 명령을 사용할 수 있습니다.

   ```bash
   sudo docker container inspect -f "{{ json .Mounts }}" avroFileWriter | python -m json.tool
   ```

1. 실행 중인 컨테이너에 연결합니다. 이 명령은 컨테이너가 실행되는 동안 컨테이너를 검사하려는 경우에 유용할 수 있습니다.

   ```bash
   sudo docker exec -it avroFileWriter bash
   ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Visual Studio Code에서 분류자, 라우터, 파일 기록기/업로더의 세 가지 모듈을 사용하여 IoT Edge 솔루션을 만들었습니다. 모듈이 에지 디바이스에서 서로 통신할 수 있도록 경로를 설정했습니다. 에지 디바이스의 구성을 수정하고, Dockerfile을 업데이트하여 종속성을 설치하고 모듈의 컨테이너에 바인딩 탑재를 추가했습니다. 

다음으로, 형식에 따라 메시지를 라우팅하고 파일 업로드를 처리하도록 IoT Hub 구성을 업데이트했습니다. 모든 것이 제대로 준비되면 모듈을 IoT Edge 디바이스에 배포하고 올바르게 실행되는지 확인했습니다.

자세한 지침은 다음 문서를 참조하세요.

* [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)
* [IoT Hub 메시지 라우팅 쿼리 구문](../iot-hub/iot-hub-devguide-routing-query-syntax.md)
* [IoT Hub 메시지 라우팅: 이제 메시지 본문에서 라우팅 사용](https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/)
* [IoT Hub를 사용하여 파일 업로드](../iot-hub/iot-hub-devguide-file-upload.md)
* [IoT Hub를 사용하여 디바이스에서 클라우드로 파일 업로드](../iot-hub/iot-hub-python-python-file-upload.md)

데이터 보내기를 시작하고 솔루션의 작동을 확인하려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [투명 게이트웨이를 통해 데이터 보내기](tutorial-machine-learning-edge-07-send-data-to-hub.md)

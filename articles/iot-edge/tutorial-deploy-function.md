---
title: '자습서: 모듈로 Azure 함수 배포 - Azure IoT Edge'
description: 이 자습서에서는 Azure 함수를 IoT Edge 모듈로 개발한 다음, 에지 디바이스에 배포합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 9526a1569a5d8320dd59272ca97f5b48ec1dac1c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999124"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>자습서: IoT Edge 모듈로 Azure Functions 배포

비즈니스 논리를 직접 Azure IoT Edge 디바이스에 구현하는 코드를 배포하려면 Azure Functions를 사용할 수 있습니다. 이 자습서에서는 시뮬레이션된 IoT Edge 디바이스에서 센서 데이터를 필터링하는 Azure 함수를 만들고 배포하는 과정을 안내합니다. 여기서는 [Windows](quickstart.md) 또는 [Linux](quickstart-linux.md) 빠른 시작의 시뮬레이션된 디바이스에 Azure IoT Edge 배포에서 만든 시뮬레이션된 IoT Edge 디바이스를 사용합니다. 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Visual Studio Code를 사용하여 Azure 함수를 만듭니다.
> * VS Code 및 Docker를 사용하여 Docker 이미지를 만들어 컨테이너 레지스트리에 게시합니다.
> * IoT Edge 디바이스에 컨테이너 레지스트리의 모듈을 배포합니다.
> * 필터링된 데이터를 봅니다.

<center>

![다이어그램 - 자습서 아키텍처: 함수 모듈 준비 및 배포](./media/tutorial-deploy-function/functions-architecture.png)
</center>

이 자습서에서 만드는 Azure 함수는 디바이스에서 생성한 온도 데이터를 필터링합니다. 이 함수는 온도가 지정된 임계값을 초과하는 경우에만 Azure IoT Hub에 메시지 업스트림을 전송합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하려면 이전 자습서를 진행하여 Linux 컨테이너 개발을 위한 개발 환경이 설정되어 있어야 합니다. [Linux 디바이스를 위한 IoT Edge 모듈을 개발합니다](tutorial-develop-for-linux.md). 이 자습서를 완료하여 다음과 같은 필수 구성 요소를 갖추어야 합니다.

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge를 실행하는 Linux 디바이스](quickstart-linux.md)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)와 같은 컨테이너 레지스트리
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)를 사용하여 구성된 [Visual Studio Code](https://code.visualstudio.com/)
* Linux 컨테이너를 실행하도록 구성된 [Docker CE](https://docs.docker.com/install/)

Azure Functions를 사용하여 IoT Edge 모듈을 개발하려면 다음과 같은 추가 필수 구성 요소를 개발 머신에 설치합니다.

* [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>함수 프로젝트 만들기

필수 조건에서 설치한 Visual Studio Code용 Azure IoT Tools는 관리 기능뿐만 아니라 일부 코드 템플릿도 제공합니다. 이 섹션에서는 Visual Studio Code를 사용하여 Azure 함수를 포함하는 IoT Edge 솔루션을 만듭니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기

고유의 코드로 사용자 지정할 수 있는 C# Function 솔루션 템플릿을 만듭니다.

1. 개발 머신에서 Visual Studio Code를 엽니다.

2. **보기** > **명령 팔레트**를 차례로 선택하여 VS Code 명령 팔레트를 엽니다.

3. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | **FunctionSolution**과 같은 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값을 적용합니다. |
   | 모듈 템플릿 선택 | **Azure Functions - C#** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈의 이름을 **CSharpFunction**으로 지정합니다. |
   | 모듈의 Docker 이미지 리포지토리 제공 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 마지막 단계에서 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 **로그인 서버** 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. 최종 문자열은 \<registry name\>.azurecr.io/CSharpFunction 형식입니다. |

   ![Docker 이미지 리포지토리 제공](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

환경 파일은 컨테이너 레지스트리의 자격 증명을 저장하고 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 프라이빗 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다.

IoT Edge 확장은 Azure에서 컨테이너 레지스트리 자격 증명을 끌어온 후 환경 파일에 채우려고 합니다. 사용자 자격 증명이 이미 포함되어 있는지 확인합니다. 그렇지 않은 경우 다음과 같이 지금 추가합니다.

1. VS Code 탐색기에서 .env 파일을 엽니다.
2. 필드를 Azure 컨테이너 레지스트리에서 복사한 **사용자 이름** 및 **암호** 값으로 업데이트합니다.
3. 이 파일을 저장합니다.

### <a name="select-your-target-architecture"></a>대상 아키텍처 선택

현재, Visual Studio Code에서는 Linux AMD64 및 Linux ARM32v7 디바이스용 C 모듈을 개발할 수 있습니다. 컨테이너는 아키텍처 유형별로 다르게 빌드되고 실행되므로 각 솔루션에서 대상으로 지정할 대상 아키텍처를 선택해야 합니다. 기본값은 Linux AMD64입니다.

1. 명령 팔레트를 열고 **Azure IoT Edge: 에지 솔루션용 기본 대상 플랫폼 설정**을 검색하거나 창의 맨 아래에 있는 사이드바에서 바로 가기 아이콘을 선택합니다.

2. 명령 팔레트의 옵션 목록에서 대상 아키텍처를 선택합니다. 이 자습서에서는 Ubuntu 가상 머신을 IoT Edge 디바이스로 사용할 예정이므로 기본값인 **amd64**를 그대로 둡니다.

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

IoT Hub에 전달하기 전에 모듈이 에지에서 메시지를 처리하도록 몇 가지 추가 코드를 추가해보겠습니다.

1. Visual Studio Code에서 **모듈** > **CSharpFunction** > **CSharpFunction.cs**를 엽니다.

1. **CSharpFunction.cs** 파일 내용을 다음 코드로 바꿉니다. 이 코드는 주위 온도 및 기계 온도에 대한 원격 분석을 수신한 후, 기계 온도가 정의된 임계값을 초과하는 경우 IoT Hub에 메시지를 전달합니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

1. 파일을 저장합니다.

## <a name="build-and-push-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드 및 푸시

이전 섹션에서는 IoT Edge 솔루션을 만들고 **CSharpFunction**을 수정하여 보고된 머신 온도가 허용 가능한 임계값 미만인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다.

1. **보기** > **터미널**을 차례로 선택하여 VS Code 통합 터미널을 엽니다.

2. 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. Azure Container Registry의 사용자 이름, 암호 및 로그인 서버로 로그인합니다. Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 값을 검색할 수 있습니다.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 모범 사례는 프로덕션 시나리오에 권장되지만 이 자습서에는 포함되지 않습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 참조를 참조하세요.

3. VS Code 탐색기에서 **deployment.template.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다.

   빌드 및 푸시 명령은 세 가지 작업을 시작합니다. 먼저, 배포 템플릿 및 기타 솔루션 파일의 정보로 작성된 전체 배포 매니페스트를 포함하는 **config**라는 솔루션에 새 폴더를 만듭니다. 둘째, `docker build`를 실행하여 대상 아키텍처의 적절한 dockerfile을 기준으로 컨테이너 이미지를 빌드합니다. 그런 다음, `docker push`를 실행하여 컨테이너 레지스트리에 이미지 리포지토리를 푸시합니다.

   이 프로세스는 처음에는 몇 분 정도 걸릴 수 있지만 다음번에 명령을 실행할 때는 더 빨라집니다.

## <a name="view-your-container-image"></a>컨테이너 이미지 보기

컨테이너 이미지가 컨테이너 레지스트리에 푸시될 때 Visual Studio Code는 성공 메시지를 출력합니다. 직접 성공적인 작업을 확인하려면 레지스트리에서 이미지를 볼 수 있습니다.

1. Azure Portal에서 Azure 컨테이너 레지스트리를 찾습니다.
2. **리포지토리**를 선택합니다.
3. 목록에 **csharpfunction** 리포지토리가 표시됩니다. 자세한 내용을 보려면 이 리포지토리를 선택합니다.
4. **태그** 섹션에 **0.0.1-amd64** 태그가 표시됩니다. 이 태그는 빌드하는 이미지의 버전 및 플랫폼을 나타냅니다. 이러한 값은 CSharpFunction 폴더의 module.json 파일에서 설정됩니다.

## <a name="deploy-and-run-the-solution"></a>솔루션 배포 및 실행

Azure Portal을 사용하여 빠른 시작에서 수행한 것처럼 IoT Edge 디바이스에 함수 모듈을 배포할 수 있습니다. 또한 Visual Studio Code 내에서 모듈을 배포하고 모니터링할 수 있습니다. 다음 섹션에서는 필수 조건에 나와 있는 VS Code용 Azure IoT Tools를 사용합니다. 아직 설치하지 않은 경우 확장을 설치합니다.

1. Visual Studio Code 탐색기의 **Azure IoT Hub** 섹션에서 **디바이스**를 확장하여 IoT 디바이스 목록을 표시합니다.

2. IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭한 다음, **단일 디바이스용 배포 만들기**를 선택합니다.

3. **CSharpFunction**이 포함된 솔루션 폴더를 찾습니다. config 폴더를 열고 **deployment.amd64.json** 파일을 선택한 다음, **Edge 배포 매니페스트 선택**을 클릭합니다.

4. 배포되어 실행 중인 모듈의 목록을 보려면 디바이스 아래에서 **모듈**을 확장합니다. 새로고침 단추를 클릭합니다. **SimulatedTemperatureSensor** 모듈과 **$edgeAgent** 및 **$edgeHub**와 함께 실행되는 새 **CSharpFunction**이 표시됩니다.

    모듈을 시작하는 데 몇 분 정도 걸릴 수 있습니다. IoT Edge 디바이스는 IoT Hub에서 해당 새 배포 정보를 검색하고, 새 컨테이너를 시작하고, 상태를 IoT Hub에 다시 보고해야 합니다.

   ![VS Code에서 배포된 모듈 보기](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>생성된 데이터 보기

명령 팔레트에서 **Azure IoT Hub: 기본 제공 이벤트 엔드포인트 모니터링 시작**을 실행하여 IoT Hub에 도달한 모든 메시지를 볼 수 있습니다.

특정 디바이스에서 IoT Hub에 도달한 모든 메시지를 보려면 보기를 필터링할 수도 있습니다. **Azure IoT Hub 디바이스** 섹션에서 디바이스를 마우스 오른쪽 단추로 클릭하고 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다.

메시지 모니터링을 중지하려면 명령 팔레트에서 **Azure IoT Hub: 기본 제공 이벤트 엔드포인트 모니터링 시작**을 실행합니다.

## <a name="clean-up-resources"></a>리소스 정리

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 디바이스와 동일한 IoT Edge 디바이스를 계속 사용해도 됩니다.

그렇지 않은 경우 요금 청구를 방지하도록 이 문서에서 만든 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스에서 생성한 원시 데이터를 필터링하는 코드가 포함된 Azure 함수 모듈을 만들었습니다. 고유한 모듈을 빌드할 준비가 되면 [Visual Studio Code에 대한 Azure IoT Edge를 개발](how-to-vs-code-develop-module.md)하는 방법에 대해 자세히 알아볼 수 있습니다.

Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [Azure Stream Analytics에서 부동 창을 사용하여 평균 찾기](tutorial-deploy-stream-analytics.md)

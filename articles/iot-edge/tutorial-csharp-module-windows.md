---
title: Windows용 C# 모듈 개발 자습서 - Azure IoT Edge | Microsoft Docs
description: 이 자습서에서는 C# 코드를 사용하여 IoT Edge 모듈을 만들고, Windows IoT Edge 디바이스에 배포하는 방법을 보여줍니다.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 122028217a78463fa2ceaed63248a74257206345
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808781"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>자습서: Windows 디바이스용 C# IoT Edge 모듈 개발

Visual Studio를 사용하여 C# 코드를 개발하고 Azure IoT Edge를 실행하는 Windows 디바이스에 배포합니다. 

비즈니스 논리를 직접 Azure IoT Edge 디바이스에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio를 사용하여 C# SDK를 기반으로 하는 IoT Edge 모듈을 만듭니다.
> * Visual Studio 및 Docker를 사용하여 Docker 이미지를 만들어 레지스트리에 게시합니다.
> * 모듈을 IoT Edge 디바이스에 배포합니다.
> * 생성된 데이터를 봅니다.

이 자습서에서 만드는 IoT Edge 모듈은 디바이스에서 생성한 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. Edge에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>솔루션 범위

이 자습서에서는 **Visual Studio 2019**를 사용하여 **C#** 에서 모듈을 개발하는 방법과 **Windows 디바이스**에 배포하는 방법을 설명합니다. Linux 디바이스용 모듈을 개발하는 경우에는 [Linux 디바이스용 C# IoT Edge 모듈 개발](tutorial-csharp-module.md)로 이동합니다. 

다음 표에서 C 모듈을 개발하고 Windows에 배포하기 위한 옵션을 확인할 수 있습니다. 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64 개발** | ![VS Code의 WinAMD64용 C# 모듈 개발](./media/tutorial-c-module/green-check.png) | ![Visual Studio의 WinAMD64용 C# 모듈 개발](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 디버그** |   | ![Visual Studio의 WinAMD64용 C# 모듈 디버그](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 이전 자습서를 진행하면서 개발 환경을 설정하고, [Windows 디바이스용 IoT Edge 모듈이 개발](tutorial-develop-for-windows.md)되어 있어야 합니다. 이 자습서를 완료한 후에는 다음과 같은 필수 구성 요소를 갖추어야 합니다. 

* Azure의 무료 또는 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* [Azure IoT Edge를 실행하는 Windows 디바이스](quickstart.md)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)와 같은 컨테이너 레지스트리
* [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 확장을 사용하여 구성된 [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)
* Windows 컨테이너를 실행하도록 구성된 [Docker CE](https://docs.docker.com/install/)

> [!TIP]
> Visual Studio 2017(버전 15.7 이상)을 사용하는 경우 Visual Studio Marketplace에서 VS 2017용 [Azure IoT Edge Tools(미리 보기)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)를 다운로드하여 설치합니다.

## <a name="create-a-module-project"></a>모듈 프로젝트 만들기

다음 단계에서는 Visual Studio 및 Azure IoT Edge Tools 확장을 사용하여 IoT Edge 모듈 프로젝트를 만듭니다. 프로젝트 템플릿을 만든 후, 모듈이 보고된 속성을 기준으로 메시지를 필터링하도록 새 코드를 추가합니다. 

### <a name="create-a-new-project"></a>새 프로젝트 만들기

Azure IoT Edge Tools에서는 Visual Studio에서 지원되는 모든 IoT Edge 모듈 언어를 위한 프로젝트 템플릿이 제공됩니다. 이러한 템플릿에는 작업 모듈을 배포하여 IoT Edge를 테스트하는 데 필요한 모든 파일 및 코드가 포함되어 있으며, 사용자 고유의 비즈니스 논리를 사용하여 템플릿을 사용자 지정하기 위한 시작 지점이 되기도 합니다. 

1. Visual Studio 2019를 시작하고 **새 프로젝트 만들기**를 선택합니다.

2. 새 프로젝트 창에서 **IoT Edge** 프로젝트를 검색하고, **Azure IoT Edge(Windows amd64)** 프로젝트를 선택합니다. **다음**을 클릭합니다. 

   ![새 Azure IoT Edge 프로젝트 만들기](./media/tutorial-csharp-module-windows/new-project.png)

3. 새 프로젝트 구성 창에서 프로젝트 및 솔루션의 이름을 구체적인 이름(예: **CSharpTutorialApp**)으로 바꿉니다. **만들기**를 클릭하여 프로젝트를 만듭니다. 

   ![새 Azure IoT Edge 프로젝트 구성](./media/tutorial-csharp-module-windows/configure-project.png)

4. IoT Edge 애플리케이션 및 모듈 창에서 다음 값을 사용하여 프로젝트를 구성합니다. 

   | 필드 | 값 |
   | ----- | ----- |
   | 템플릿 선택 | **C# 모듈**을 선택합니다. | 
   | 모듈 프로젝트 이름 | 모듈의 이름을 **CSharpModule**로 지정합니다. | 
   | Docker 이미지 리포지토리 | 이미지 리포지토리는 컨테이너 레지스트리의 이름 및 컨테이너 이미지의 이름을 포함합니다. 컨테이너 이미지는 모듈 프로젝트 이름 값에서 미리 채워져 있습니다. **localhost:5000**을 Azure 컨테이너 레지스트리의 로그인 서버 값으로 바꿉니다. Azure Portal에서 컨테이너 레지스트리의 개요 페이지에서 로그인 서버를 검색할 수 있습니다. <br><br> 마지막 이미지 리포지토리는 \<레지스트리 이름\>.azurecr.io/csharpmodule과 같습니다. |

   ![대상 디바이스, 모듈 유형 및 컨테이너 레지스트리에 대한 프로젝트를 구성](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. **확인**을 선택하여 변경 내용을 적용합니다. 

### <a name="add-your-registry-credentials"></a>레지스트리 자격 증명 추가

배포 매니페스트는 컨테이너 레지스트리의 자격 증명을 IoT Edge 런타임과 공유합니다. 이러한 자격 증명은 런타임에서 프라이빗 이미지를 IoT Edge 디바이스로 가져오기 위해 필요합니다. Azure Container Registry의 **액세스 키** 섹션에서 자격 증명을 사용합니다. 

1. Visual Studio 솔루션 탐색기에서 **deployment.template.json** 파일을 엽니다. 

2. **registryCredentials** 속성을 $edgeAgent desired 속성에서 찾습니다. 

3. 다음 형식에 따라 자격 증명을 사용하여 속성을 업데이트합니다. 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. deployment.template.json 파일을 저장합니다. 

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

기본 모듈 코드는 입력 큐의 메시지를 받고 출력 큐를 통해 메시지를 전달합니다. IoT Hub에 전달하기 전에 모듈이 에지에서 메시지를 처리하도록 몇 가지 추가 코드를 추가해보겠습니다. 각 메시지에서 온도 데이터를 분석하고 온도가 특정 임계값을 초과하는 경우에만 IoT Hub로 메시지를 보내도록 모듈을 업데이트합니다. 

1. Visual Studio에서 **CSharpModule** > **Program.cs**를 엽니다.

2. **CSharpModule** 네임스페이스의 맨 위에 나중에 사용되는 유형에 새 개의 **using** 문을 추가합니다.

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. 카운터 변수 다음에 **temperatureThreshold** 변수를 **프로그램** 클래스에 추가합니다. temperatureThreshold 변수는 데이터가 IoT Hub로 전송되기 위해 측정된 온도가 초과해야 하는 값을 설정합니다. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. 변수 선언 다음에 **MessageBody**, **Machine** 및 **Ambient** 클래스를 **Program** 클래스에 추가합니다. 이러한 클래스는 수신 메시지 본문의 예상 스키마를 정의합니다.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
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
    ```

5. **Init** 메서드를 찾습니다. 이 메서드는 **ModuleClient** 개체를 만들고 구성합니다. 이 개체를 사용하면 메시지를 주고받기 위해 로컬 Azure IoT Edge 런타임에 모듈을 연결할 수 있습니다. 코드는 **input1** 엔드포인트를 통해 IoT Edge 허브에서 메시지를 수신하는 콜백을 등록합니다.

   전체 Init 메서드를 다음 코드로 바꿉니다.
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   이 업데이트된 Init 메서드는 계속해서 ModuleClient를 사용하여 IoT Edge 런타임에 대한 연결을 설정하지만, 새 기능을 추가하기도 합니다. 모듈 쌍의 desired 속성을 읽어 **temperatureThreshold** 값을 검색합니다. 그런 다음, 모듈 쌍의 desired 속성에 대한 향후 업데이트를 수신 대기하는 콜백을 만듭니다. 이 콜백을 사용하여 모듈 쌍의 온도 임계값을 원격으로 업데이트할 수 있으며, 변경 내용은 모듈에 통합됩니다. 

   또한 업데이트된 Init 메서드는 기존 **SetInputMessageHandlerAsync** 메서드를 변경합니다. 샘플 코드에서는 *input1*의 들어오는 메시지가 *PipeMessage* 함수를 사용하여 처리되지만, 다음 단계에서 만들 *FilterMessages* 함수를 사용하도록 변경하려고 합니다. 

6. 새 **onDesiredPropertiesUpdate** 메서드를 **Program** 클래스에 추가합니다. 이 메서드는 모듈 쌍에서 원하는 속성에 대한 업데이트를 수신하고, 일치하도록 **temperatureThreshold** 변수를 업데이트합니다. 모든 모듈에는 자체 모듈 쌍이 있어서 클라우드에서 직접 모듈 내에서 실행되는 코드를 구성할 수 있습니다.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

7. 샘플 **PipeMessage** 메서드를 제거하고 새 **FilterMessages** 메서드로 바꿉니다. 이 메서드는 모듈이 IoT Edge Hub에서 메시지를 수신할 때마다 호출됩니다. 모듈 쌍을 통해 설정된 온도 임계값 아래의 온도를 보고하는 메시지를 필터링합니다. 또한 값이 **Alert**로 설정된 **MessageType** 속성을 메시지에 추가합니다. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. Program.cs 파일을 저장합니다.

9. IoT Edge 솔루션에서 **deployment.template.json** 파일을 엽니다. 이 파일은 배포할 모듈(이 경우 **tempSensor** 및 **CSharpModule**)을 IoT Edge 에이전트에 알려주고, 메시지를 라우팅하는 방법을 IoT Edge 허브에 알려줍니다.

10. **CSharpModule** 모듈 쌍을 배포 매니페스트에 추가합니다. **$edgeHub** 모듈 쌍 뒤에 있는 **modulesContent** 섹션의 아래쪽에 다음 JSON 내용을 삽입합니다. 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![배포 템플릿에 모듈 쌍 추가](./media/tutorial-csharp-module-windows/module-twin.png)

11. deployment.template.json 파일을 저장합니다.


## <a name="build-and-push-your-module"></a>모듈 빌드 및 푸시

이전 섹션에서는 IoT Edge 솔루션을 만들고 **CSharpModule**에 코드를 추가하여 보고된 컴퓨터 온도가 허용 가능한 임계값 미만인 메시지를 필터링했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다. 

1. 다음 명령을 사용하여 개발 머신에서 Docker에 로그인합니다. Azure Container Registry의 사용자 이름, 암호 및 로그인 서버를 사용합니다. Azure Portal에서 레지스트리의 **액세스 키** 섹션에서 이러한 값을 검색할 수 있습니다.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   `--password-stdin` 사용을 권장하는 보안 경고가 표시될 수 있습니다. 이 모범 사례는 프로덕션 시나리오에 권장되지만 이 자습서에는 포함되지 않습니다. 자세한 내용은 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 참조를 참조하세요.

2. Visual Studio 솔루션 탐색기에서 빌드하려는 프로젝트 이름을 마우스 오른쪽 단추로 클릭합니다. 기본 이름은 **AzureIotEdgeApp1**이며, Windows 모듈을 빌드해야 하므로 확장은 **Windows.Amd64**가 됩니다. 

3. **IoT Edge 모듈 빌드 및 푸시**를 선택합니다. 

   빌드 및 푸시 명령은 세 가지 작업을 시작합니다. 먼저, 배포 템플릿 및 기타 솔루션 파일의 정보로 작성된 전체 배포 매니페스트를 포함하는 **config**라는 새 폴더를 솔루션에 만듭니다. 둘째, `docker build`를 실행하여 대상 아키텍처의 적절한 dockerfile을 기준으로 컨테이너 이미지를 빌드합니다. 그런 다음, `docker push`를 실행하여 컨테이너 레지스트리에 이미지 리포지토리를 푸시합니다. 

## <a name="deploy-modules-to-device"></a>디바이스에 모듈 배포

Visual Studio 클라우드 탐색기 및 Azure IoT Edge Tools 확장을 사용하여 IoT Edge 디바이스에 모듈 프로젝트를 배포합니다. 사용자의 시나리오를 위한 배포 매니페스트인 **deployment.json** 파일이 config 폴더에 이미 준비되어 있습니다. 이제 배포를 받을 디바이스를 선택하기만 하면 됩니다.

IoT Edge 디바이스가 작동되고 실행 중인지 확인합니다. 

1. Visual Studio 클라우드 탐색기에서 IoT 디바이스 목록을 보려면 리소스를 확장합니다. 

2. 배포를 수신하려는 IoT Edge 디바이스의 이름을 마우스 오른쪽 단추로 클릭합니다. 

3. **배포 만들기**를 선택합니다.

4. 파일 탐색기에서 솔루션의 config 폴더에 있는 **deployment.windows-amd64** 파일을 선택합니다. 

5. 디바이스 아래에 나열된 배포된 모듈을 보려면 클라우드 탐색기를 새로 고칩니다. 

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 디바이스에 배포 매니페스트를 적용한 후에는 디바이스의 IoT Edge 런타임이 새 배포 정보를 수집하고 그에 따라 실행하기 시작합니다. 배포 매니페스트에 포함되지 않는 디바이스에서 실행되는 모든 모듈은 중지됩니다. 디바이스에서 누락된 모든 모듈이 시작됩니다. 

IoT Edge Tools 확장을 사용하여 IoT Hub에 도착하는 메시지를 볼 수 있습니다. 

1. Visual Studio 클라우드 탐색기에서 IoT Edge 디바이스의 이름을 선택합니다. 

2. **동작** 목록에서 **기본 제공 이벤트 엔드포인트 모니터링 시작**을 선택합니다. 

3. IoT Hub에 메시지가 들어오는 것을 확인합니다. CSharpModule 코드를 변경할 경우 머신 온도가 25도에 도달할 때까지 기다렸다가 메시지를 보내므로 메시지가 도착할 때까지 시간이 걸릴 수 있습니다. 또한 온도 임계값에 도달하는 모든 메시지에 메시지 유형 **경고**를 추가합니다. 

   ![IoT Hub에 도착하는 메시지 보기](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>모듈 쌍 편집

CSharpModule 모듈 쌍을 사용하여 온도 임계값을 25도로 설정했습니다. 모듈 쌍을 사용하면 모듈 코드를 업데이트하지 않고도 기능을 변경할 수 있습니다.

1. Visual Studio에서 **deployment.windows-amd64.json** 파일을 엽니다. (deployment.template 파일이 아닙니다. 솔루션 탐색기에서 구성 파일에 배포 매니페스트가 표시되지 않는 경우 탐색기 도구 모음에서 **모든 파일 표시** 아이콘을 선택합니다.)

2. CSharpModule 쌍을 찾아 **temperatureThreshold** 매개 변수의 값을 최근 보고된 온도보다 5도 ~ 10도 더 높은 새 온도로 변경합니다. 

3. **deployment.windows-amd64.json** 파일을 저장합니다.

4. 디바이스에 업데이트된 배포 매니페스트를 적용하려면 다시 배포 단계를 따릅니다. 

5. 들어오는 디바이스-클라우드 메시지를 모니터링합니다. 새 온도 임계값에 도달할 때까지 메시지가 중지되어야 합니다. 

## <a name="clean-up-resources"></a>리소스 정리 

권장되는 다음 문서를 계속 진행하려는 경우 만든 리소스와 구성을 그대로 유지하고 다시 사용할 수 있습니다. 테스트 디바이스와 동일한 IoT Edge 디바이스를 계속 사용해도 됩니다. 

그렇지 않은 경우 요금이 발생하지 않도록 이 문서에서 사용한 로컬 구성 및 Azure 리소스를 삭제할 수 있습니다. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 디바이스에서 생성한 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다. 고유한 모듈을 빌드할 준비가 되면 [고유한 IoT Edge 모듈 개발](module-development.md) 또는 [Visual Studio를 사용하여 모듈을 개발](how-to-visual-studio-develop-module.md)하는 방법을 알아볼 수 있습니다. 다음 자습서를 계속 진행하면서 Azure Cloud Services를 배포하여 에지에서 데이터를 처리 및 분석하는 데 Azure IoT Edge를 어떻게 활용할 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)

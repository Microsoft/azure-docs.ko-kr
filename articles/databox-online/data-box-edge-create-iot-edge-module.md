---
title: Azure Data Box Edge용 C# IoT Edge 모듈 | Microsoft Docs
description: Data Box Edge에 배포될 수 있는 C# IoT Edge 모듈을 개발하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/19/2019
ms.author: alkohli
ms.openlocfilehash: c2803ba598895834bb197f4a06ff0635354fcaca
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759999"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>개발을 C# 데이터 상자 가장자리에서 파일을 이동 IoT Edge 모듈

이 문서에서는 Data Box Edge 디바이스를 사용하여 배포를 위해 IoT Edge 모듈을 만드는 방법을 단계별로 안내합니다. Azure Data Box Edge는 데이터를 처리하여 네트워크를 통해 Azure로 전송할 수 있는 저장소 솔루션입니다.

Data Box Edge로 Azure IoT Edge 모듈을 사용하여 데이터를 Azure로 이동할 수 있습니다. 이 문서에서 사용되는 모듈은 Data Box Edge 디바이스의 로컬 공유에서 클라우드 공유로 파일을 복사하는 논리를 구현합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 모듈을 저장하고 관리하는 컨테이너 레지스트리를 만듭니다(Docker 이미지).
> * Data Box Edge 디바이스에 배포하는 IoT Edge 모듈을 만듭니다.


## <a name="about-the-iot-edge-module"></a>IoT Edge 모듈 정보

Data Box Edge 디바이스는 IoT Edge 모듈을 배포 및 실행할 수 있습니다. Edge 모듈은 기본적으로 디바이스에서 메시지를 수집하고, 메시지를 변환하거나 IoT Hub에 메시지를 전송하는 등의 특정 작업을 수행하는 Docker 컨테이너입니다. 이 문서에서는 Data Box Edge 디바이스의 로컬 공유에서 클라우드 공유로 파일을 복사하는 모듈을 만듭니다.

1. 파일은 Data Box Edge 디바이스의 로컬 공유에 작성됩니다.
2. 파일 이벤트 생성기는 로컬 공유에 작성된 각 파일에 대한 파일 이벤트를 만듭니다. 파일 이벤트 파일을 수정할 때 생성 됩니다. 그런 다음, 파일 이벤트는 IoT Edge 허브에 전송됩니다(IoT Edge 런타임).
3. IoT Edge 사용자 지정 모듈은 파일에 대한 상대 경로를 포함하는 파일 이벤트 개체를 만들도록 파일 이벤트를 처리합니다. 모듈은 상대 파일 경로를 사용하여 절대 경로를 생성하고 로컬 공유에서 클라우드 공유로 파일을 복사합니다. 그런 다음, 모듈은 로컬 공유에서 파일을 삭제합니다.

![Azure IoT Edge 모듈이 Data Box Edge에서 작동하는 방식](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

파일이 클라우드 공유에 있으면 Azure Storage 계정으로 자동으로 업로드됩니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 확인합니다.

- 실행 중인 Data Box Edge 디바이스

    - 디바이스에는 연결된 IoT Hub 리소스가 있습니다.
    - 디바이스에 Edge 컴퓨팅 역할이 구성되어 있습니다.
    자세한 내용은 [계산 구성](data-box-edge-deploy-configure-compute.md#configure-compute) 데이터 상자의 가장자리에 대 한 합니다.

- 다음 개발 리소스:

    - [Visual Studio Code](https://code.visualstudio.com/)
    - [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). 소프트웨어를 다운로드 및 설치할 계정을 만들어야 할 수 있습니다.

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하고 관리할 수 있는 Azure의 개인 Docker 레지스트리입니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 Azure Container Registry 및 Docker Hub입니다. 이 문서에서는 Container Registry를 사용합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. **리소스 만들기 > 컨테이너 > Container Registry**를 선택합니다. **만들기**를 클릭합니다.
3. 다음을 제공합니다.

   1. 5~50의 영숫자 문자를 포함하는 Azure 내의 고유한 **레지스트리 이름**
   2. **구독**을 선택합니다.
   3. 새 리소스 그룹을 만들거나 기존 **리소스 그룹**을 선택합니다.
   4. **위치**를 선택합니다. 이 위치는 Data Box Edge 리소스와 연결된 것과 동일한 것이 좋습니다.
   5. **관리 사용자**를 **사용**으로 전환합니다.
   6. SKU를 **기본**으로 설정합니다.

      ![컨테이너 레지스트리 만들기](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. **만들기**를 선택합니다.
5. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리를 찾고 **액세스 키**를 선택합니다.

    ![선택키 가져오기](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 레지스트리에 Docker 이미지를 게시하고 Azure IoT Edge 런타임에 레지스트리 자격 증명을 추가하려면 뒷부분에 나오는 이러한 값을 사용합니다.


## <a name="create-an-iot-edge-module-project"></a>IoT Edge 모듈 프로젝트 만들기

다음 단계에서는 .NET Core 2.1 SDK를 기반으로 하는 IoT Edge 모듈 프로젝트를 만듭니다. 프로젝트는 Visual Studio Code 및 Azure IoT Edge 확장을 사용합니다.

### <a name="create-a-new-solution"></a>새 솔루션 만들기

고유의 코드로 사용자 지정할 수 있는 C# 솔루션 템플릿을 만듭니다.

1. Visual Studio Code에서 **보기 > 명령 팔레트**를 선택하여 VS Code 명령 팔레트를 엽니다.
2. 명령 팔레트에서 **Azure: 로그인** 명령을 입력하고 실행한 다음, 지침에 따라 Azure 계정에 로그인합니다. 이미 로그인한 경우 이 단계를 건너뛸 수 있습니다.
3. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트에서 다음 정보를 제공하여 솔루션을 만듭니다.

    1. 솔루션을 만들 폴더를 선택합니다.
    2. 솔루션에 대한 이름을 제공하거나 기본 **EdgeSolution**을 그대로 적용합니다.
    
        ![새 솔루션 1 만들기](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. **C# 모듈**을 모듈 템플릿으로 선택합니다.
    4. 기본 모듈 이름을 할당하려는 이름으로 바꿉니다. 이 예제의 경우 **FileCopyModule**입니다.
    
        ![새 솔루션 2 만들기](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. 이전 섹션에서 만든 컨테이너 레지스트리를 첫 번째 모듈에 대한 이미지 리포지토리로 지정합니다. **localhost:5000**을 복사한 로그인 서버 값으로 바꿉니다.

        최종 문자열은 `<Login server name>/<Module name>`과 같습니다. 이 예제에서 문자열은 `mycontreg2.azurecr.io/filecopymodule`입니다.

        ![새 솔루션 3 만들기](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. **파일 > 폴더 열기**로 이동합니다.

    ![새 솔루션 4 만들기](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. 앞에서 만든 **EdgeSolution** 폴더를 찾고 가리킵니다. VS Code 창은 최상위 5개의 구성 요소로 IoT Edge 솔루션 작업 영역을 로드합니다. 이 문서에서는 **.vscode** 폴더, **.gitignore** 파일, **.env** 파일 및 **deployment.template.json**을 편집하지 않습니다.
    
    수정하는 유일한 구성 요소는 모듈 폴더입니다. 이 폴더에는 모듈에 대한 C# 코드 및 컨테이너 이미지로 모듈을 빌드하는 Docker 파일이 있습니다.

    ![새 솔루션 5 만들기](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>사용자 지정 코드를 사용하여 모듈 업데이트

1. VS Code 탐색기에서 엽니다 **모듈 > FileCopyModule > Program.cs**합니다.
2. **FileCopyModule 네임스페이스**의 맨 위에서 나중에 사용되는 유형에 다음 using 문을 추가합니다. **Microsoft.Azure.Devices.Client.Transport.Mqtt**는 IoT Edge Hub에 메시지를 보내는 프로토콜입니다.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Program 클래스에 **InputFolderPath** 및 **OutputFolderPath** 변수를 추가합니다.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Program 클래스에 **MessageBody** 클래스를 추가합니다. 이러한 클래스는 수신 메시지 본문의 예상 스키마를 정의합니다.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. **Init** 메서드에서 코드는 **ModuleClient** 개체를 만들고 구성합니다. 이 개체를 사용하면 메시지를 주고받기 위해 MQTT 프로토콜을 사용하여 로컬 Azure IoT Edge 런타임에 모듈을 연결할 수 있습니다. Init 메서드에 사용된 연결 문자열이 IoT Edge 런타임에 의해 모듈에 제공됩니다. 코드는 **input1** 엔드포인트를 통해 IoT Edge 허브에서 메시지를 수신하는 FileCopy 콜백을 등록합니다.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. **FileCopy**에 코드를 삽입합니다.

    ```
        /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }
    ```

7. 이 파일을 저장합니다.

## <a name="build-your-iot-edge-solution"></a>IoT Edge 솔루션 빌드

이전 섹션에서는 IoT Edge 솔루션을 만들고 FileCopyModule에 코드를 추가하여 로컬 공유에서 클라우드 공유로 파일을 복사했습니다. 이제 솔루션을 컨테이너 이미지로 빌드하고 컨테이너 레지스트리로 푸시해야 합니다.

1. VSCode에서 터미널으로 이동 > 새 터미널 새 Visual Studio Code 통합된 터미널을 엽니다.
2. 통합된 터미널에서 다음 명령을 입력 하 여 Docker에 로그인 합니다.

    `docker login <ACR login server> -u <ACR username>`

    컨테이너 레지스트리에서 복사한 로그인 서버 및 사용자 이름을 사용합니다. 

    ![IoT Edge 솔루션 빌드 및 푸시](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. 암호를 묻는 메시지가 나타나면 암호를 입력합니다. Azure Portal에서 컨테이너 레지스트리의 **선택키**에서 로그인 서버, 사용자 이름 및 암호에 대한 값을 검색할 수도 있습니다.
 
3. 자격 증명이 제공되면 Azure 컨테이너 레지스트리에 모듈 이미지를 푸시할 수 있습니다. VS Code 탐색기에서**module.json** 파일을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 솔루션 빌드 및 푸시**를 선택합니다.

    ![IoT Edge 솔루션 빌드 및 푸시](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Visual Studio Code에 솔루션을 빌드하도록 지정하는 경우 통합 터미널에서 두 개의 명령을 실행합니다. docker 빌드 및 docker 푸시 이 두 명령은 코드를 빌드하고, CSharpModule.dll을 컨테이너화한 다음, 솔루션을 초기화할 때 지정한 컨테이너 레지스트리로 코드를 푸시합니다.

    모듈 플랫폼을 선택하라는 메시지가 표시됩니다. Linux에 해당하는 *amd64*를 선택합니다.

    ![플랫폼 선택](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Linux 모듈만 지원됩니다.

    무시할 수 있는 다음과 같은 경고가 나타날 수 있습니다.

    *Program.cs(77,44): 경고 CS1998: 이 비동기 메서드는 'await' 연산자가 부족하여 동기적으로 실행됩니다. 'await' 연산자를 사용하여 비블로킹 API 호출을 대기하거나, 'await Task.Run(...)'을 사용하여 백그라운드 스레드에서 CPU 바인딩된 작업을 수행하세요.*

4. VS Code 통합 터미널에 태그와 함께 전체 컨테이너 이미지 주소를 볼 수 있습니다. 이미지 주소는 `<repository>:<version>-<platform>` 형식으로 module.json 파일에 있는 정보에서 빌드됩니다. 이 문서의 경우 `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`처럼 보여야 합니다.

## <a name="next-steps"></a>다음 단계

를 배포 및 가장자리가 상자의 데이터에서이 모듈을 실행 하려면의 단계를 참조 [모듈을 추가할](data-box-edge-deploy-configure-compute.md#add-a-module)합니다.

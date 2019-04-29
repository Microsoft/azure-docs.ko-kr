---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(.NET) | Microsoft Docs
description: .NET용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 3b10ae0998acbb010e61ebb41c602c7d42bc14fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741857"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-back-end-and-net-device"></a>.NET 백 엔드 및 .NET 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 애플리케이션에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET 콘솔 앱이 생깁니다.

* **CreateIdentities**: 디바이스 ID, 모듈 ID 및 관련 보안 키를 만들어 디바이스 및 모듈 클라이언트를 연결합니다.

* **UpdateModuleTwinReportedProperties**: 업데이트된 모듈 쌍 reported 속성을 IoT Hub에 보냅니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2017.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT 허브에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 IoT Hub 연결 문자열을 갖게 되었습니다.

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 모듈 쌍 reported 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

1. **Visual Studio 프로젝트 만들기:** Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 기존 솔루션에 추가합니다. .NET Framework 버전이 4.6.1 이상인지 확인합니다. 프로젝트 이름을 **UpdateModuleTwinReportedProperties**로 지정합니다.

    ![Visual Studio 프로젝트 만들기](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

2. **최신 Azure IoT Hub .NET 디바이스 SDK 설치:** 모듈 ID 및 모듈 쌍은 공개 미리 보기로 제공됩니다. IoT Hub 시험판 디바이스 SDK에서만 사용할 수 있습니다. Visual Studio에서 도구 > Nuget 패키지 관리자 > 솔루션용 Nuget 패키지 관리를 차례로 엽니다. Microsoft.Azure.Devices.Client를 검색합니다. 시험판 포함 확인란이 선택되었는지 확인합니다. 최신 버전을 선택하고 설치합니다. 이제 모든 모듈 기능에 액세스할 수 있습니다. 

    ![Azure IoT Hub .NET 서비스 SDK V1.16.0-preview-005 설치](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

3. **모듈 연결 문자열 가져오기** - 이제 [Azure Portal](https://portal.azure.com/)에 로그인합니다. IoT Hub로 이동하고 IoT 디바이스를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

    ![Azure Portal 모듈 세부 정보](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

4. **UpdateModuleTwinReportedProperties 콘솔 앱 만들기**

    **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 모듈 연결 문자열로 바꿉니다.

    ```csharp
    private const string ModuleConnectionString = 
      "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

    **Program** 클래스에 **OnDesiredPropertyChanged** 메서드를 추가합니다.

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다. 공개 미리 보기에서는 모듈 쌍 작업에 대해서만 AMQP를 지원합니다.

5. 위의 **Main** 메서드 외에도, 모듈에서 IoT Hub로 이벤트를 보내는 다음 코드 블록을 추가할 수 있습니다.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다. 솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다. **여러 개의 시작 프로젝트**를 선택하고 **시작**을 콘솔 앱에 대한 작업으로 선택합니다. 그런 다음, F5 키를 눌러 앱을 시작합니다.

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)
* [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)
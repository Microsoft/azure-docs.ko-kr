---
title: Azure IoT Hub(.NET)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: .NET용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: cf108e0e7036894e045028ec3fce8c2af6b9ce4f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008341"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>IoT Hub(.NET)를 사용하여 클라우드에서 디바이스에 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-dotnet.md) 빠른 시작에서는 IoT Hub를 만들고 그 안에 디바이스 ID를 프로비저닝하고 디바이스-클라우드 메시지를 보내는 디바이스 앱을 코딩하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-dotnet.md)를 기반으로 합니다. 다음 태스크를 수행하는 방법이 나와 있습니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서, IoT Hub에서 디바이스로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

클라우드-디바이스 메시지에 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)에서 찾아볼 수 있습니다.

이 자습서의 끝 부분에서 다음의 두 .NET 콘솔 앱을 실행합니다.

* **SimulatedDevice**. 이 앱은 IoT Hub에 연결하고 클라우드-디바이스 메시지를 수신합니다. 이 앱은 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-dotnet.md)에서 만든 앱의 수정된 버전입니다.

* **SendCloudToDevice**. 이 앱은 IoT Hub를 통해 디바이스 앱에 클라우드-디바이스 메시지를 보낸 다음, 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 [Azure IoT 디바이스 SDK](iot-hub-devguide-sdks.md)를 통해 많은 디바이스 플랫폼 및 언어(C, Java, Python 및 Javascript 포함)를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.
>

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="receive-messages-in-the-device-app"></a>디바이스 앱에서 메시지 수신

이 섹션에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-dotnet.md)에서 만든 디바이스 앱을 수정하여 IoT Hub로부터 클라우드-디바이스 메시지를 수신합니다.

1. Visual Studio의 **SimulatedDevice** 프로젝트에서 **SimulatedDevice** 클래스에 다음 메서드를 추가 합니다.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync` 메서드는 수신 메시지를 디바이스에서 받은 시간에 비동기적으로 반환합니다. 지정 가능한 시간 제한 기간이 지나면 *null*을 반환합니다. 이 예제에서는 기본값 1분이 사용됩니다. 앱에서 *null*을 수신하면 새 메시지를 계속 기다려야 합니다. 이 요구 사항은 `if (receivedMessage == null) continue` 줄 때문입니다.

`CompleteAsync()`에 대한 호출은 메시지가 정상적으로 처리되었음을 IoT Hub에 알립니다. 디바이스 큐에서 메시지를 안전하게 제거할 수 있습니다. 디바이스 앱의 메시지 처리를 완료하지 못하게 하는 문제가 발생하는 경우 IoT Hub에서 메시지를 다시 전달합니다. 디바이스 앱의 메시지 처리 논리는 *멱등성*이므로 같은 메시지를 여러 번 수신하면 동일한 결과가 생성됩니다.

애플리케이션이 메시지를 일시적으로 중단할 수도 있으며 이 경우 IoT hub는 나중에 사용하기 위해 큐에 메시지를 보관합니다. 또는 애플리케이션이 메시지를 거부할 수 있습니다. 이 경우 큐에서 메시지가 영구적으로 제거됩니다. 클라우드-디바이스 메시지 수명 주기에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.

   > [!NOTE]
   > MQTT 또는 AMQP 대신 HTTPS를 전송으로 사용하는 경우 `ReceiveAsync` 메서드가 즉시 반환됩니다. HTTPS에서 클라우드-디바이스 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(25분에 한 번씩보다 적게) 확인하는 디바이스에 간헐적으로 연결됩니다. HTTPS 수신을 더 많이 실행하면 IoT Hub가 요청을 제한할 수 있습니다. MQTT, AMQP 및 HTTPS 지원과 IoT Hub 제한 간의 차이점에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

이 문서에서는 [디바이스에서 IoT Hub로 원격 분석 데이터 보내기](quickstart-send-telemetry-dotnet.md)에서 만든 IoT Hub를 통해 클라우드-디바이스 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-디바이스 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub는 이 사용 권한을 부여하는 **service**라는 공유 액세스 정책을 사용하여 만듭니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이 섹션에서는 클라우드-장치 메시지를 시뮬레이션 된 장치 앱으로 보내는 .NET 콘솔 앱을 만듭니다.

1. 현재 Visual Studio 솔루션에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트 만들기**에서 C#에 대해 **콘솔 앱(.NET Framework)** 을 선택한 후 **다음**을 선택합니다.

1. 프로젝트 *SendCloudToDevice*의 이름을 지정합니다. **솔루션**에서 **솔루션에 추가**를 선택하고 .NET Framework의 최신 버전을 적용합니다. **만들기**를 선택하여 프로젝트를 만듭니다.

   ![Visual Studio에서 새 프로젝트 구성](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. 솔루션 탐색기에서 새 프로젝트를 마우스 오른쪽 단추로 클릭 한 다음 **NuGet 패키지 관리**를 선택 합니다.

1. **NuGet 패키지 관리**에서 **찾아보기**를 선택하고 **Microsoft. Azure. 디바이스**를 검색한 후 선택합니다. **설치**를 선택합니다.

   이 단계에서는 [Azure IoT 서비스 SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Devices/)를 다운로드 및 설치하고 해당 참조를 추가합니다.

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** 클래스에 다음 필드를 추가합니다. `{iot hub connection string}`자리 표시자 값을 이전에 [iot hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 기록한 iot hub 연결 문자열로 바꿉니다. `{device id}`자리 표시자 값을 [장치에서 IoT hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-dotnet.md) 에서 추가한 장치의 장치 ID로 바꿉니다.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. **Program** 클래스에 다음 메서드를 추가 하 여 장치에 메시지를 보냅니다.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다.

1. **공용 속성**  >  **시작 프로젝트**에서 **여러 개의 시작 프로젝트**를 선택한 다음 **SimulatedDevice** 및 **sendcloudtodevice**에 대 한 **시작** 작업을 선택 합니다. **확인** 을 선택하여 변경 내용을 저장합니다.

1. **F5**키를 누릅니다. 두 애플리케이션이 모두 시작됩니다. **Sendcloudtodevice** 창을 선택 하 고 **enter**키를 누릅니다. 디바이스 앱에서 수신하고 있는 메시지가 표시됩니다.

   ![앱 메시지 수신](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>배달 피드백 받기

각 클라우드-디바이스 메시지에 대해 배달(또는 만료) 승인을 IoT Hub에 요청할 수 있습니다. 이 옵션을 사용하면 솔루션 백 엔드에서 다시 시도 또는 보정 논리를 쉽게 알릴 수 있습니다. 클라우드-디바이스 피드백에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.

이 섹션에서는 **SendCloudToDevice** 앱을 수정하여 피드백을 요청하고 IoT Hub로부터 수신합니다.

1. Visual Studio의 **SendCloudToDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    수신 패턴은 디바이스 앱으로부터 클라우드-디바이스 메시지를 받는 데 사용되는 것과 동일합니다.

1. **Main** 메서드에서 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 바로 뒤에 다음 줄을 추가합니다.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. 클라우드-장치 메시지 배달에 대한 피드백을 요청하려면 **SendCloudToDeviceMessageAsync** 메서드에서 속성을 지정해야 합니다. `var commandMessage = new Message(...);` 줄 바로 뒤에 다음 줄을 추가합니다.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5** 키를 눌러 앱을 실행합니다. 두 응용 프로그램이 시작 되는 것을 볼 수 있습니다. **Sendcloudtodevice** 창을 선택 하 고 **enter**키를 누릅니다. 장치 앱에서 메시지를 수신하는 것이 확인됩니다. 몇 초 후에 **SendCloudToDevice** 애플리케이션에서 피드백 메시지를 수신하는지 확인해야 합니다.

   ![앱 메시지 수신](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.
>

## <a name="next-steps"></a>다음 단계

이 방법에서는 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완전한 엔드투엔드 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://docs.microsoft.com/azure/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.

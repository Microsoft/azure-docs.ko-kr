---
title: Azure IoT Hub(.NET)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: .NET용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110204"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>IoT Hub(.NET)를 사용하여 클라우드에서 디바이스에 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [장치에서 iot hub로 원격 분석 전송 빠른 시작](quickstart-send-telemetry-dotnet.md) 에서는 iot hub를 만들고, 장치 id를 프로 비전 하 고, 장치-클라우드 메시지를 보내는 장치 앱을 코딩 하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서 [는 장치에서 IoT hub로 원격 분석 전송](quickstart-send-telemetry-dotnet.md)을 기반으로 합니다. 다음 작업을 수행 하는 방법을 보여 줍니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서 IoT Hub 장치에 전송 된 메시지에 대 한 배달 승인 (*피드백*)을 요청 합니다.

클라우드-디바이스 메시지에 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)에서 찾아볼 수 있습니다.

이 자습서의 끝 부분에서는 두 개의 .NET 콘솔 앱을 실행 합니다.

* **SimulatedDevice**. 이 앱은 IoT hub에 연결 하 고 클라우드-장치 메시지를 수신 합니다. 이 앱은 [장치에서 IoT hub로 원격 분석 전송](quickstart-send-telemetry-dotnet.md)에서 만든 앱의 수정 된 버전입니다.

* **Sendcloudtodevice**. 이 앱은 IoT Hub를 통해 장치 앱에 클라우드-장치 메시지를 보낸 다음 배달 승인을 받습니다.

> [!NOTE]
> IoT Hub에는 [Azure IoT 장치 sdk](iot-hub-devguide-sdks.md)를 통해 C, Java, Python 및 Javascript를 비롯 한 많은 장치 플랫폼 및 언어에 대 한 SDK 지원이 있습니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.
>

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* 방화벽에서 포트 8883가 열려 있는지 확인 합니다. 이 문서의 device 샘플에서는 포트 8883을 통해 통신 하는 MQTT 프로토콜을 사용 합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단 될 수 있습니다. 이 문제를 해결 하는 방법 및 방법에 대 한 자세한 내용은 [IoT Hub에 연결 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조 하세요.

## <a name="receive-messages-in-the-device-app"></a>디바이스 앱에서 메시지 수신

이 섹션에서는 IoT hub에서 클라우드-장치 메시지를 수신 하도록 [장치에서 iot hub로 원격 분석 전송](quickstart-send-telemetry-dotnet.md) 에서 만든 장치 앱을 수정 합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.

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

`ReceiveAsync` 메서드는 수신 메시지를 디바이스에서 받은 시간에 비동기적으로 반환합니다. 지정 가능한 시간 제한 기간이 지나면 *null* 을 반환 합니다. 이 예에서는 기본값 1 분이 사용 됩니다. 앱에서 *null*을 수신하면 새 메시지를 계속 기다려야 합니다. 이 요구 사항은 `if (receivedMessage == null) continue` 줄 때문입니다.

`CompleteAsync()`에 대한 호출은 메시지가 정상적으로 처리되었음을 IoT Hub에 알립니다. 디바이스 큐에서 메시지를 안전하게 제거할 수 있습니다. 디바이스 앱의 메시지 처리를 완료하지 못하게 하는 문제가 발생하는 경우 IoT Hub에서 메시지를 다시 전달합니다. 동일한 메시지를 여러 번 수신 하면 동일한 결과를 얻을 수 있도록 장치 앱의 메시지 처리 논리를 *idempotent*해야 합니다.

애플리케이션이 메시지를 일시적으로 중단할 수도 있으며 이 경우 IoT hub는 나중에 사용하기 위해 큐에 메시지를 보관합니다. 또는 애플리케이션이 메시지를 거부할 수 있습니다. 이 경우 큐에서 메시지가 영구적으로 제거됩니다. 클라우드-디바이스 메시지 수명 주기에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.

   > [!NOTE]
   > MQTT 또는 AMQP 대신 HTTPS를 전송으로 사용하는 경우 `ReceiveAsync` 메서드가 즉시 반환됩니다. HTTPS에서 클라우드-디바이스 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(25분에 한 번씩보다 적게) 확인하는 디바이스에 간헐적으로 연결됩니다. HTTPS 수신을 더 많이 실행하면 IoT Hub가 요청을 제한할 수 있습니다. MQTT, AMQP 및 HTTPS 지원과 IoT Hub 제한 간의 차이점에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT hub 연결 문자열을 가져옵니다.

이 문서에서는 [장치에서 iot hub로 원격 분석 전송](quickstart-send-telemetry-dotnet.md)에서 만든 iot hub를 통해 클라우드-장치 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-장치 메시지를 보내려면 서비스에 **서비스 연결** 권한이 있어야 합니다. 기본적으로 모든 IoT Hub은이 사용 권한을 부여 하는 **서비스** 라는 공유 액세스 정책으로 만들어집니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이제 장치 앱에 클라우드-장치 메시지를 보내는 .NET 콘솔 앱을 작성 합니다.

1. 현재 Visual Studio 솔루션에서 **파일** > **새** > **프로젝트**를 선택 합니다. **새 프로젝트 만들기**에서 **콘솔 앱 (.NET Framework)** 을 선택 하 고 **다음**을 선택 합니다.

1. 프로젝트 *SendCloudToDevice*의 이름을 지정합니다. **솔루션**에서 **솔루션에 추가** 를 선택 하 고 .NET Framework의 최신 버전을 적용 합니다. **만들기**를 선택하여 프로젝트를 만듭니다.

   ![Visual Studio에서 새 프로젝트 구성](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. 솔루션 탐색기에서 새 솔루션을 마우스 오른쪽 단추로 클릭 한 다음 **NuGet 패키지 관리**를 선택 합니다.

1. **NuGet 패키지 관리**에서 **찾아보기**를 선택한 다음를 검색 하 고 **Microsoft. Azure. Devices**를 선택 합니다. **설치**를 선택 합니다.

   이 단계에서는 [Azure IoT 서비스 SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Devices/)에 대 한 참조를 다운로드, 설치 및 추가 합니다.

1. **Program.cs** 파일의 맨 위에 다음 `using` 문을 추가 합니다.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전에 [iot hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 iot hub 연결 문자열로 바꿉니다.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. **Program** 클래스에 다음 메서드를 추가합니다. 장치 [에서 IoT hub로 원격 분석 전송](quickstart-send-telemetry-dotnet.md)에서 장치를 정의할 때 사용한 장치 이름을 설정 합니다.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   이 메서드는 새 클라우드-디바이스 메시지를 ID `myFirstDevice`를 사용하여 디바이스에 보냅니다. 이 매개 변수는 [장치에서 IoT hub로 원격 분석 전송](quickstart-send-telemetry-dotnet.md)에 사용 된 것에서 수정한 경우에만 변경 합니다.

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭 하 고 **시작 프로젝트 설정**을 선택 합니다.

1. **시작 프로젝트** > **공용 속성** 에서 **여러 개의 시작 프로젝트**를 선택한 다음 **Readdevicetocloudmessages**, **SimulatedDevice**및 **sendcloudtodevice**에 대 한 **시작** 작업을 선택 합니다. **확인** 을 선택하여 변경 내용을 저장합니다.

1. **F5**키를 누릅니다. 세 애플리케이션이 모두 시작됩니다. **SendCloudToDevice** 창을 선택하고 **Enter** 키를 누릅니다. 디바이스 앱에서 수신하고 있는 메시지가 표시됩니다.

   ![앱 메시지 수신](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>배달 피드백 받기

각 클라우드-장치 메시지에 대 한 IoT Hub에서 배달 (또는 만료) 승인을 요청할 수 있습니다. 이 옵션을 사용하면 솔루션 백 엔드에서 다시 시도 또는 보정 논리를 쉽게 알릴 수 있습니다. 클라우드-디바이스 피드백에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.

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

1. **Main** 메서드에 다음 줄을 추가 하 여 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`합니다.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. 클라우드-장치 메시지 배달에 대한 피드백을 요청하려면 **SendCloudToDeviceMessageAsync** 메서드에서 속성을 지정해야 합니다. `var commandMessage = new Message(...);` 줄 바로 뒤에 다음 줄을 추가 합니다.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5** 키를 눌러 앱을 실행합니다. 세 애플리케이션이 모두 시작됩니다. **SendCloudToDevice** 창을 선택하고 **Enter** 키를 누릅니다. 장치 앱에서 메시지를 수신하는 것이 확인됩니다. 몇 초 후에 **SendCloudToDevice** 애플리케이션에서 피드백 메시지를 수신하는지 확인해야 합니다.

   ![앱 메시지 수신](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> 간단히 하기 위해이 자습서에서는 재시도 정책을 구현 하지 않습니다. 프로덕션 코드에서는 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에 제안 된 대로 지 수 백오프와 같은 재시도 정책을 구현 해야 합니다.
>

## <a name="next-steps"></a>다음 단계

이 방법에서는 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완전한 엔드투엔드 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://docs.microsoft.com/azure/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.

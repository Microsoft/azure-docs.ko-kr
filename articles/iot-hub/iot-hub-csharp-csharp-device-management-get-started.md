---
title: Azure IoT Hub 디바이스 관리 시작(.NET/.NET) | Microsoft Docs
description: Azure IoT Hub 디바이스 관리를 사용하여 원격 디바이스 재부팅을 시작하는 방법입니다. .NET용 Azure IoT 디바이스 SDK를 사용하여 직접 메서드를 포함한 시뮬레이트된 디바이스 앱을 구현하며 .NET용 Azure IoT service SDK를 사용하여 직접 메서드를 호출하는 서비스 앱을 구현합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 5760c574e64a3b3b4a1df12092cff44374790a90
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018402"
---
# <a name="get-started-with-device-management-net"></a>디바이스 관리 시작(.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 디바이스 ID를 만듭니다.

* 디바이스를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 디바이스 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.

* IoT Hub를 통해 시뮬레이션된 디바이스 앱에서 재부팅 직접 메서드를 호출하는 .NET 콘솔 앱을 만듭니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET 콘솔 앱이 생깁니다.

* **SimulateManagedDevice**. 이 앱은 이전에 만든 디바이스 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이트하며 마지막 재부팅 시간을 보고합니다.

* **TriggerReboot**. 이 앱은 시뮬레이트된 디바이스 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 표시합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub 연결 문자열 가져오기

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 디바이스에서 원격 재부팅 트리거

이 섹션에서는 디바이스에서 직접 메서드를 사용하여 원격 다시 시작을 시작하는 .NET 콘솔 앱(C# 사용)을 만듭니다. 앱은 디바이스 쌍 쿼리를 사용하여 해당 디바이스에 대한 마지막 다시 시작 시간을 검색합니다.

1. Visual Studio에서 **새 프로젝트 만들기**를 선택합니다.

1. **새 프로젝트 만들기**에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 찾아 선택한 후 **다음**을 선택합니다.

1. **새 프로젝트 구성**에서 프로젝트 이름을 *TriggerReboot*로 지정하고 .NET Framework 버전 4.5.1 이상을 선택합니다. **만들기**를 선택합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. **솔루션 탐색기**에서 **TriggerReboot** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.

1. **찾아보기**를 선택하고 **Microsoft.Azure.Devices**를 검색하여 선택합니다. **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치합니다.

    ![NuGet 패키지 관리자 창](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   이 단계에서는 [Azure IoT 서비스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. **Program** 클래스에 다음 필드를 추가합니다. `{iot hub connection string}` 자리 표시자 값을 이전에 [IoT Hub 연결 문자열 가져오기](#get-the-iot-hub-connection-string)에서 복사한 IoT Hub 연결 문자열로 바꿉니다.

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 재부팅 디바이스에 대한 디바이스 쌍을 가져오고 reported 속성을 출력합니다.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 직접 메서드를 사용하여 디바이스에서 재부팅을 시작합니다.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. **빌드** > **솔루션 빌드**를 선택합니다.

> [!NOTE]
> 이 자습서에서는 디바이스의 보고된 속성에 대한 쿼리를 한 번만 수행합니다. 프로덕션 코드에서는 보고된 속성의 변경 사항을 감지하도록 폴링하는 것이 좋습니다.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 .NET 콘솔 앱을 만듭니다.

* 시뮬레이션된 디바이스 재부팅을 트리거합니다.

* reported 속성을 사용하여 디바이스 및 해당 디바이스가 마지막으로 재부팅한 시간을 확인하는 디바이스 쌍 쿼리를 사용하도록 설정합니다.

시뮬레이트된 디바이스 앱을 만들려면 다음 단계를 수행합니다.

1. Visual Studio의 이미 만든 TriggerReboot 솔루션에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트 만들기**에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 찾아 선택한 후 **다음**을 선택합니다.

1. **새 프로젝트 구성**에서 프로젝트 이름을 *SimulateManagedDevice*로 지정하고 **솔루션**에 대해 **솔루션에 추가**를 선택합니다. **만들기**를 선택합니다.

    ![프로젝트 이름 지정 및 솔루션에 추가](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. 솔루션 탐색기에서 **SimulateManagedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

1. **찾아보기**를 선택하고 **Microsoft.Azure.Devices.Client**를 검색하여 선택합니다. **설치**를 선택합니다.

    ![NuGet 패키지 관리자 창 클라이언트 앱](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   이 단계에서는 [Azure IoT 디바이스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. `{device connection string}` 자리 표시자 값을 이전에 [IoT Hub에서 새 디바이스 등록](#register-a-new-device-in-the-iot-hub)에서 적어 둔 디바이스 연결 문자열로 바꿉니다.

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. 디바이스에서 직접 메서드를 구현하도록 다음을 추가합니다.

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다.

1. **일반 속성** > **시작 프로젝트**에 대해 **단일 시작 프로젝트**를 선택하고 **SimulateManagedDevice** 프로젝트를 선택합니다. **확인** 을 선택하여 변경 내용을 저장합니다.

1. **빌드** > **솔루션 빌드**를 선택합니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. .NET 디바이스 앱 **SimulateManagedDevice**를 실행하려면 솔루션 탐색기에서 **SimulateManagedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음, **새 인스턴스 시작**을 선택합니다. 앱은 IoT Hub의 메서드 호출에 대한 수신을 시작합니다.

1. 디바이스가 연결되고 메서드 호출을 기다린 후 **TriggerReboot** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음, **새 인스턴스 시작**을 선택합니다.

   그러면 **SimulatedManagedDevice** 콘솔 및 디바이스의 보고된 속성에 "다시 부팅 중!"이라고 표시되며, 여기에는 **TriggerReboot** 콘솔에 기록된 마지막 다시 부팅 시간이 포함됩니다.

    ![서비스 및 디바이스 앱 실행](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

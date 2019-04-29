---
title: Azure IoT Hub 디바이스 관리 시작(.NET/.NET) | Microsoft Docs
description: Azure IoT Hub 디바이스 관리를 사용하여 원격 디바이스 재부팅을 시작하는 방법입니다. .NET용 Azure IoT 디바이스 SDK를 사용하여 직접 메서드를 포함한 시뮬레이트된 디바이스 앱을 구현하며 .NET용 Azure IoT service SDK를 사용하여 직접 메서드를 호출하는 서비스 앱을 구현합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 556c10cc5ec5e528857a120dadb16c2a10202ed3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250998"
---
# <a name="get-started-with-device-management-netnet"></a>디바이스 관리 시작(.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 디바이스 ID를 만듭니다.

* 디바이스를 다시 시작하는 직접 메서드가 포함된 시뮬레이트된 디바이스 앱을 만듭니다. 직접 메서드는 클라우드에서 호출됩니다.

* IoT Hub를 통해 시뮬레이션된 디바이스 앱에서 재부팅 직접 메서드를 호출하는 .NET 콘솔 앱을 만듭니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET 콘솔 앱이 생깁니다.

* **SimulateManagedDevice** - 이전에 만든 디바이스 ID로 IoT Hub에 연결하며 재부팅 직접 메서드를 수신하고 물리적 재부팅을 시뮬레이션하며 마지막 재부팅 시간을 보고합니다.

* **TriggerReboot** - 시뮬레이션된 디바이스 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 표시합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2017.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-find-include-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT 허브에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 디바이스에서 원격 재부팅 트리거

이 섹션에서는 디바이스에서 직접 메서드를 사용하여 원격 다시 시작을 시작하는 .NET 콘솔 앱(C# 사용)을 만듭니다. 앱은 디바이스 쌍 쿼리를 사용하여 해당 디바이스에 대한 마지막 다시 시작 시간을 검색합니다.

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕화면 프로젝트를 새 솔루션에 추가합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 이름을 **TriggerReboot**로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. [솔루션 Explorer]에서 **TriggerReboot** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고, **Microsoft.Azure.Devices**를 검색하고, **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고, 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 "IoT Hub 만들기" 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다. 
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 재부팅 디바이스에 대한 디바이스 쌍을 가져오고 reported 속성을 출력합니다.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. **Program** 클래스에 다음 메서드를 추가합니다.  이 코드는 직접 메서드를 사용하여 디바이스에서 재부팅을 시작합니다.

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

7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. 솔루션을 빌드하십시오.

> [!NOTE]
> 이 자습서에서는 디바이스의 보고된 속성에 대한 쿼리를 한 번만 수행합니다. 프로덕션 코드에서는 보고된 속성의 변경 사항을 감지하도록 폴링하는 것이 좋습니다.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 다음 작업을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 .NET 콘솔 앱을 만듭니다.

* 시뮬레이션된 디바이스 재부팅을 트리거합니다.

* reported 속성을 사용하여 디바이스 및 해당 디바이스가 마지막으로 재부팅한 시간을 확인하는 디바이스 쌍 쿼리를 사용하도록 설정합니다.

1. Visual Studio에서 **콘솔 애플리케이션** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 **SimulateManagedDevice**의 이름을 지정합니다.
   
    ![새 Visual C# Windows 클래식 디바이스 앱](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. [솔루션 탐색기]에서 **SimulateManagedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **Microsoft.Azure.Devices.Client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 디바이스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창 클라이언트 앱](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 메모한 디바이스 연결 문자열로 대체합니다.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. 디바이스에서 직접 메서드를 구현하도록 다음을 추가합니다.

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

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.

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
        
8. Visual Studio 솔루션 Explorer에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정...** 을 클릭합니다. **단일 시작 프로젝트**를 선택한 다음 드롭다운 메뉴에서 **SimulateManagedDevice** 프로젝트를 선택합니다. 솔루션을 빌드하십시오.       

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. .NET 디바이스 앱 **SimulateManagedDevice**를 실행하려면 **SimulateManagedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭하고, **디버그**를 선택한 다음, **새 인스턴스 시작**을 선택합니다. IoT 허브의 메서드 호출에 대한 수신이 시작됩니다. 

2. 이제 디바이스가 연결되어 메서드 호출을 기다리고 있으므로 .NET **TriggerReboot** 앱을 실행하여 시뮬레이션된 디바이스 앱에서 다시 부팅 메서드를 호출합니다. 이를 수행하려면 **TriggerReboot** 프로젝트를 마우스 오른쪽 단추로 클릭하고, **디버그**를 선택한 다음, **새 인스턴스 시작**을 선택합니다. 그러면 **SimulatedManagedDevice** 콘솔 및 디바이스의 보고된 속성에 "다시 부팅 중!"이라고 표시되며, 여기에는 **TriggerReboot** 콘솔에 기록된 마지막 다시 부팅 시간이 포함됩니다.
   
    ![서비스 및 디바이스 앱 실행](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

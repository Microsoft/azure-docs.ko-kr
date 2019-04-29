---
title: Azure IoT Hub(.NET/.NET)를 사용하여 작업 예약 | Microsoft Docs
description: 여러 디바이스에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. .NET용 Azure IoT 디바이스 SDK를 사용하여 시뮬레이션된 디바이스 앱 및 작업을 실행하는 서비스 앱을 구현합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: robinsh
ms.openlocfilehash: db34734e9fbb8635f1a62def8a877d83d02e2206
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216388"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>작업 예약 및 브로드캐스트(.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub를 사용하여 수백만 대의 디바이스를 업데이트하는 작업을 예약하고 추적합니다. 작업을 사용하여 다음을 수행합니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

작업(job)은 이러한 작업(action) 중 하나를 래핑하고 디바이스 쌍 쿼리로 정의된 디바이스 집합에 대한 실행을 추적합니다. 예를 들어 백 엔드 앱은 작업(job)을 사용하여 10,000대 디바이스에 대해 디바이스를 재부팅하는 직접 메서드를 호출할 수 있습니다. 디바이스 쌍 쿼리로 디바이스 집합을 지정하고 향후 실행될 작업(job)을 예약합니다. 작업(job)은 해당하는 각 디바이스에서 재부팅 직접 메서드를 수신 및 실행할 때 진행 상태를 추적합니다.

이러한 각 기능에 대한 자세한 내용은 다음을 참조하세요.

* 디바이스 쌍 및 속성: [장치 쌍 시작](iot-hub-csharp-csharp-twin-getstarted.md) 고 [자습서: 장치 쌍 속성 사용 방법](tutorial-device-twins.md)

* 직접 메서드: [IoT Hub 개발자 가이드-직접 메서드](iot-hub-devguide-direct-methods.md) 고 [자습서: 직접 메서드 사용](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 백 엔드 앱에 의해 호출될 수 있는 **LockDoor**라는 직접 메서드를 구현하는 디바이스 앱을 만듭니다.

* 여러 디바이스에 대해 **LockDoor** 직접 메서드를 호출하는 작업을 만드는 백 엔드 앱을 만듭니다. 다른 작업이 여러 디바이스로 원하는 속성 업데이트를 보냅니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET(C#) 콘솔 앱이 생깁니다.

**SimulateDeviceMethods**: IoT 허브에 연결하고 **LockDoor** 직접 메서드를 구현합니다.

**ScheduleJob**: 작업을 사용하여 **LockDoor** 직접 메서드를 호출하고 여러 디바이스에서 디바이스 쌍의 원하는 속성을 업데이트합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2017.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 디바이스 앱 만들기

이 섹션에서는 솔루션 백 엔드에서 호출한 직접 메서드에 응답하는 .NET 콘솔 앱을 만듭니다.

1. Visual Studio에서 **콘솔 애플리케이션** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **SimulateDeviceMethods**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 디바이스 앱](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. 솔루션 탐색기에서 **SimulateDeviceMethods** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **Microsoft.Azure.Devices.Client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 디바이스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창 클라이언트 앱](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 메모한 디바이스 연결 문자열로 대체합니다.

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. 디바이스에서 직접 메서드를 구현하도록 다음을 추가합니다.

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

7. 디바이스에서 디바이스 쌍 수신기를 구현하도록 다음을 추가합니다.

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
9. 작업을 저장하고 솔루션을 빌드합니다.         

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults) 문서에 제시된 대로 재시도 정책(예: 연결 다시 시도)을 구현해야 합니다.
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>직접 메서드를 호출하고 디바이스 쌍의 업데이트를 전송하기 위한 작업 예약

이 섹션에서는 작업을 사용하여 **LockDoor** 직접 메서드를 호출하고 원하는 속성 업데이트를 여러 디바이스에 전송하는 .NET 콘솔 앱(C# 사용)을 만듭니다.

1. Visual Studio에서 **콘솔 애플리케이션** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **ScheduleJob**으로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. 솔루션 탐색기에서 **ScheduleJob** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고, **Microsoft.Azure.Devices**를 검색하고, **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고, 사용 약관에 동의합니다. 이 단계에서는 [Azure IoT 서비스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. 다음 `using` 문이 기본 문에 아직 없으면 추가합니다.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자를 이전 섹션에서 만든 허브 및 디바이스 이름의 IoT Hub 연결 문자열로 바꿉니다.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. **Program** 클래스에 다른 메서드를 추가합니다.

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > 쿼리 구문에 대한 자세한 내용은 [IoT Hub 쿼리 언어](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)를 참조하세요.
    > 

10. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

11. 작업을 저장하고 솔루션을 빌드합니다. 

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. Visual Studio 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **빌드**를 클릭합니다. **여러 개의 시작 프로젝트**. `SimulateDeviceMethods`가 목록 맨 위에 표시되고 그 다음으로 `ScheduleJob`이 표시되는지 확인합니다. 두 작업을 모두 **시작**으로 설렁하고 **확인**을 클릭합니다.

2. **시작**을 클릭하거나 **디버그** 메뉴로 이동하여 **디버깅 시작**을 클릭하여 프로젝트를 실행합니다.

3. 디바이스 및 백 엔드 앱 모두에서 출력이 표시됩니다.

    ![작업을 예약하는 앱 실행](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스에 대한 직접 메서드를 예약하고 디바이스 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

IoT Hub 및 장치 관리 패턴 같은 원격 무선 펌웨어 업데이트 시작 작업을 계속 하려면 읽기 [자습서: 펌웨어 업데이트를 수행 하는 방법을](tutorial-firmware-update.md)합니다.

Azure IoT Edge로 AI를 에지 디바이스에 배포하는 방법을 자세히 알아보려면 [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)을 참조하세요.
---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: e78c9a490d2ad02fb132d62b0ab0b55f15d3d4ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741844"
---
## <a name="create-a-module-identity"></a>모듈 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에서 디바이스 ID 및 모듈 ID를 만드는 .NET 콘솔 앱을 작성합니다. ID 레지스트리에 항목이 없는 경우 디바이스 또는 모듈을 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드의 ID 레지스트리 섹션](../articles/iot-hub/iot-hub-devguide-identity-registry.md)을 참조하세요. 이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.


1. **Visual Studio 프로젝트 만들기** - Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 새 솔루션에 추가합니다. .NET Framework 버전이 4.6.1 이상인지 확인합니다. 프로젝트 이름을 **CreateIdentitie**로, 솔루션 이름을 **IoTHubGetStarted**로 지정합니다.

    ![Visual Studio 솔루션 만들기](./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG)

2. **Azure IoT Hub .NET 장치 SDK V1.16.0-preview-001 설치** - 모듈 ID 및 모듈 쌍은 공개 미리 보기로 있습니다. IoT Hub 시험판 서비스 SDK에서만 사용할 수 있습니다. Visual Studio에서 도구 > Nuget 패키지 관리자 > 솔루션용 Nuget 패키지 관리를 차례로 엽니다. Microsoft.Azure.Devices를 검색합니다. 시험판 포함 확인란이 선택되었는지 확인합니다. 1.16.0-preview-001 버전을 선택하고 설치합니다. 이제 모든 모듈 기능에 액세스할 수 있습니다. 

    ![Azure IoT Hub .NET 서비스 SDK V1.16.0-preview-001 설치](./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png)

3. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

4. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

5. **Main** 클래스에 다음 코드를 추가합니다.
   
   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

6. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    AddDeviceAsync() 메서드는 ID **myFirstDevice**로 디바이스 ID를 만듭니다. ID 레지스트리에 해당 디바이스 ID가 이미 있는 경우 코드는 기존 디바이스 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 디바이스 앱에서 사용하여 IoT Hub에 연결합니다.

    AddModuleAsync() 메서드는 **myFirstDevice** 디바이스에서 ID가 **myFirstModule**인 모듈 ID를 만듭니다. ID 레지스트리에 해당 모듈 ID가 이미 있는 경우 코드는 기존 모듈 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 모듈 앱에서 사용하여 IoT Hub에 연결합니다.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

7. 이 애플리케이션을 실행하고 장치 키 및 모듈 키를 기록합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 애플리케이션이 다른 장치별 메타데이터를 저장해야 할 경우 애플리케이션별 저장소를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](../articles/iot-hub/iot-hub-devguide-identity-registry.md)를 참조하세요.

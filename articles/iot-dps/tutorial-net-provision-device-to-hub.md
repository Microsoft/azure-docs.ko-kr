---
title: 자습서 - Azure IoT Hub Device Provisioning Service를 사용하여 디바이스 프로비전(.NET)
description: 이 자습서에서는 .NET을 통해 Azure IoT Hub DPS(Device Provisioning Service)를 사용하여 단일 IoT 허브에 디바이스를 프로비저닝하는 방법을 보여줍니다.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 55195949cfaa741389f38deaea69806c568c0ce6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008270"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>자습서: Azure IoT Hub Device Provisioning Service 클라이언트를 사용하여 IoT Hub에 디바이스를 등록(.NET)

이전 자습서에서 Device Provisioning Service에 연결하기 위해 디바이스를 설정하는 방법을 배웠습니다. 이 자습서에서는 이 서비스를 사용하여 **_개별 등록_** 및 **_등록 그룹_** 을 모두 사용하는 단일 IoT Hub에 디바이스를 프로비전하는 방법을 배웁니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 디바이스 등록
> * 디바이스 시작
> * 디바이스가 등록되어 있는지 확인

## <a name="prerequisites"></a>사전 요구 사항

계속 진행하기 전에 자습서에 설명된 대로 디바이스 및 *하드웨어 보안 모듈*을 구성할 수 있는지 확인하고, [Azure IoT Hub Device Provisioning Service를 사용하여 프로비전하도록 디바이스를 설정](./tutorial-set-up-device.md)합니다.

* Visual Studio

> [!NOTE]
> Visual Studio는 필요하지 않습니다. [.NET](https://www.microsoft.com/net) 설치로 충분하며, 개발자는 Windows 또는 Linux에서 원하는 편집기를 사용할 수 있습니다.  

이 자습서는 하드웨어 제조 프로세스 도중 또는 직후에 디바이스 정보가 프로비전 서비스에 추가되는 기간을 시뮬레이션합니다. 이 코드는 일반적으로 .NET 코드를 실행할 수 있는 PC 또는 팩터리 디바이스 자체에서 실행되며, 디바이스 자체에는 추가할 수 없습니다.


## <a name="enroll-the-device"></a>디바이스 등록

이 단계에서는 Device Provisioning Service에 디바이스의 고유 보안 아티팩트를 추가하는 것이 포함됩니다. 이러한 보안 아티팩트는 다음과 같습니다.

- TPM 기반 디바이스의 경우:
    - 각 TPM 칩 또는 시뮬레이션에 고유한 *인증 키*입니다. 자세한 정보는 [TPM 인증 키 이해](https://technet.microsoft.com/library/cc770443.aspx)를 읽어보세요.
    - 네임스페이스/범위에서 디바이스를 고유하게 식별하는 데 사용되는 *등록 ID*입니다. 이는 디바이스 ID와 같거나 다를 수 있습니다. ID는 모든 디바이스에 필수입니다. TPM 기반 디바이스의 경우 등록 ID는 TPM 인증 키의 SHA-256 해시와 같이 TPM 자체에서 파생될 수도 있습니다.

- X.509 기반 디바이스의 경우:
    - *.pem* 또는 *.cer* 파일 중 하나의 형식인 [디바이스에 발급된 X.509 인증서](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx)입니다. 개별 등록의 경우 X.509 시스템에 대한 *리프 인증서*를 사용해야 합니다. 반면, 등록 그룹의 경우 *루트 인증서* 또는 동일한 *서명자 인증서*를 사용해야 합니다.
    - 네임스페이스/범위에서 디바이스를 고유하게 식별하는 데 사용되는 *등록 ID*입니다. 이는 디바이스 ID와 같거나 다를 수 있습니다. ID는 모든 디바이스에 필수입니다. X.509 기반 디바이스의 경우 등록 ID는 인증서의 CN(일반 이름)에서 파생됩니다. 이러한 요구 사항에 대한 자세한 내용은 [디바이스 개념](https://docs.microsoft.com/azure/iot-dps/concepts-device)을 참조하세요.

Device Provisioning Service에 디바이스를 등록하는 방법은 두 가지가 있습니다.

- **개별 등록** Device Provisioning Service에 등록할 수도 있는 단일 디바이스에 대한 항목을 나타냅니다. 개별 등록은 증명 메커니즘으로 X.509 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 고유한 초기 구성이 필요한 디바이스 또는 증명 메커니즘으로 TPM을 통해 SAS 토큰만을 사용할 수 있는 디바이스의 경우 개별 등록을 사용하는 것이 좋습니다. 개별 등록은 지정된 원하는 IoT Hub 디바이스 ID가 있을 수 있습니다.

- **등록 그룹** 특정 증명 메커니즘을 공유하는 디바이스의 그룹을 나타냅니다. 원하는 초기 구성을 공유하는 디바이스 수가 많은 경우 또는 디바이스가 모두 동일한 테넌트로 이동하는 경우 등록 그룹을 사용하는 것이 좋습니다. 등록 그룹은 X.509 전용이며 해당 X.509 인증서 체인에서 서명 인증서를 모두 공유합니다.

### <a name="enroll-the-device-using-individual-enrollments"></a>개별 등록을 사용한 디바이스 등록

1. Visual Studio에서 **콘솔 앱** 프로젝트 템플릿을 사용하여 Visual C# 콘솔 애플리케이션 프로젝트를 만듭니다. 프로젝트 이름을 **DeviceProvisioning**으로 지정합니다.
    
1. 솔루션 탐색기에서 **DeviceProvisioning** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices.provisioning.service**를 검색합니다. 항목을 선택하고 **설치**를 클릭하여 **Microsoft.Azure.Devices.Provisioning.Service** 패키지를 설치한 후 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 디바이스 프로비저닝 서비스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 패키지 및 해당 종속성에 대한 참조를 다운로드, 설치 및 추가합니다.

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 메모한 디바이스 프로비저닝 서비스 연결 문자열로 바꿉니다.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. 디바이스에서 등록을 구현하도록 다음을 추가합니다.

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 등록을 시작합니다.
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. Visual Studio 솔루션 Explorer에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정...** 을 클릭합니다. **단일 시작 프로젝트**를 선택한 다음 드롭다운 메뉴에서 **DeviceProvisioning** 프로젝트를 선택합니다.  

1. .NET 디바이스 앱 **DeviceProvisiong**을 실행합니다. 디바이스에 대한 프로비저닝을 설정해야 합니다. 

    ![개별 등록 실행](./media/tutorial-net-provision-device-to-hub/individual.png)

디바이스를 성공적으로 등록하면 포털에 다음과 같은 화면이 표시됩니다.

   ![포털에서 성공적인 등록](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>등록 그룹을 사용한 디바이스 등록

> [!NOTE]
> 등록 그룹 샘플에는 X.509 인증서가 필요합니다.

1. Visual Studio 솔루션 탐색기에서 위에서 만든 **DeviceProvisioning** 프로젝트를 엽니다. 

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 X509 인증서 위치로 바꿉니다.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. 장치에서 등록을 구현하도록 다음을 **Program.cs**에 추가합니다.

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. 마지막으로 **Main** 메서드에 다음 코드를 대체하여 IoT Hub에 대한 연결을 열고 그룹 등록을 시작합니다.
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. .NET 디바이스 앱 **DeviceProvisiong**을 실행합니다. 디바이스에 대한 그룹 프로비저닝을 설정해야 합니다. 

    ![그룹 등록 실행](./media/tutorial-net-provision-device-to-hub/group.png)

    디바이스 그룹을 성공적으로 등록하면 포털에 다음과 같은 화면이 표시됩니다.

   ![포털에서 성공적인 그룹 등록](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>디바이스 시작

이 시점에서 다음 설정이 디바이스 등록을 위해 준비되었습니다.

1. 디바이스 또는 디바이스 그룹이 Device Provisioning Service에 등록되었습니다. 
2. 장치가 Device Provisioning Service 클라이언트 SDK를 사용하여 애플리케이션을 통해 구성 및 액세스가 가능한 보안과 함께 준비되어 있습니다.

디바이스에서 Device Provisioning Service를 통해 등록을 시작하도록 클라이언트 애플리케이션을 허용합니다.  


## <a name="verify-the-device-is-registered"></a>디바이스가 등록되어 있는지 확인

디바이스가 부팅되면 다음 작업이 이루어져야 합니다. 자세한 내용은 [디바이스 클라이언트 샘플 프로비전](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)을 참조하세요. 

1. 디바이스가 Device Provisioning Service에 등록 요청을 보냅니다.
2. TPM 디바이스의 경우 Device Provisioning Service에서 디바이스가 응답하는 등록 챌린지를 다시 보냅니다. 
3. 등록에 성공하면 Device Provisioning Service는 IoT Hub URI, 디바이스 ID 및 암호화된 키를 디바이스로 다시 보냅니다. 
4. 그러면 디바이스의 IoT Hub 클라이언트 애플리케이션이 사용자 허브에 연결됩니다. 
5. 허브에 성공적으로 연결되면 디바이스가 IoT Hub의 **Device Explorer**에 나타납니다. 

    ![포털에서 허브에 연결 성공](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 디바이스 등록
> * 디바이스 시작
> * 디바이스가 등록되어 있는지 확인

부하가 분산된 허브 간 여러 디바이스를 프로비전하는 방법에 알아보려면 다음 자습서를 진행합니다. 

> [!div class="nextstepaction"]
> [부하가 분산된 IoT Hub 간 디바이스 프로비전](./tutorial-provision-multiple-hubs.md)

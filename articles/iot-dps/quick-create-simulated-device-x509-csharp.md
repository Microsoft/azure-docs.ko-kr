---
title: 빠른 시작 - C#을 사용하여 Azure IoT Hub에 시뮬레이션된 X.509 디바이스 프로비저닝
description: 빠른 시작 - Azure IoT Hub DPS(Device Provisioning Service)용 C# 디바이스 SDK를 사용하여 X.509 디바이스를 만들고 프로비저닝합니다. 이 빠른 시작에서는 개별 등록을 사용합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: a328115c606cb4bface2d3dc7b8f0c502d063e9f
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987437"
---
# <a name="quickstart-create-and-provision-an-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>빠른 시작: IoT Hub Device Provisioning Service용 C# 디바이스 SDK를 사용하여 X.509 디바이스 만들기 및 프로비전

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

이러한 단계에서는 [C#용 Azure IoT 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp)에서 디바이스 코드를 사용하여 X.509 디바이스를 프로비저닝하는 방법을 보여줍니다. 이 문서에서는 Device Provisioning Service를 사용하여 IoT Hub에 연결하기 위해 개발 머신에서 디바이스 샘플 코드를 실행합니다.

## <a name="prerequisites"></a>전제 조건

자동 프로비저닝 프로세스에 익숙하지 않은 경우 [프로비저닝](about-iot-dps.md#provisioning-process) 개요를 검토하세요. 계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다.

Azure IoT Device Provisioning 서비스는 다음과 같은 두 가지 등록을 지원합니다.
- [등록 그룹](concepts-service.md#enrollment-group): 여러 관련 디바이스를 등록하는 데 사용됩니다.
- [개별 등록](concepts-service.md#individual-enrollment): 단일 디바이스를 등록하는 데 사용됩니다.

이 문서에서는 개별 등록을 설명합니다.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>개발 환경 준비 

1. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 **Git Bash** 가 포함됩니다. 

1. 명령 프롬프트 또는 Git Bash를 엽니다. C#용 Azure IoT 샘플 GitHub 리포지토리를 복제합니다.
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. 컴퓨터에 [.NET Core 3.1 SDK 이상](https://dotnet.microsoft.com/download)이 설치되어 있는지 확인합니다. 다음 명령을 사용하여 버전을 확인할 수 있습니다.

    ```bash
    dotnet --info
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>자체 서명된 X.509 디바이스 인증서 만들기

이 섹션에서는 주체 일반 이름으로 `iothubx509device1`을 사용하여 자체 서명된 X.509 테스트 인증서를 만듭니다. 다음 사항에 주의하세요.

* 자체 서명된 인증서는 테스트 목적으로만 사용되며 프로덕션 환경에서 사용하지 마십시오.
* 자체 서명된 인증서에 대한 기본 만료일은 1년입니다.
* IoT 디바이스의 디바이스 ID는 인증서의 주체 일반 이름이 됩니다. [디바이스 ID 문자열 요구 사항](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)을 준수하는 주체 이름을 사용해야 합니다.

[X509Sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample)의 샘플 코드를 사용하여 디바이스에 대한 개별 등록 항목에서 사용될 인증서를 만듭니다.


1. PowerShell 프롬프트에서 디렉터리를 X.509 디바이스 프로비저닝 샘플에 대한 프로젝트 디렉터리로 변경합니다.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. 샘플 코드는 암호로 보호된 PKCS12 형식의 파일(certificate.pfx) 내에 저장된 X.509 인증서를 사용하도록 설정됩니다. 또한 이 빠른 시작의 뒷부분에서 개별 등록을 만들려면 공개 키 인증서 파일(certificate.cer)이 필요합니다. 자체 서명 인증서와 관련 .cer 및 .pfx 파일을 생성하려면 다음 명령을 실행합니다.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. 스크립트에서 PFX 암호를 묻는 메시지가 표시됩니다. 이 암호를 기억하여 나중에 샘플을 실행할 때 다시 사용해야 합니다. `certutil`을 실행하여 인증서를 덤프하고 주체 이름을 확인할 수 있습니다.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>디바이스에 대한 개별 등록 항목 만들기


1. Azure Portal에 로그인하여 왼쪽 메뉴에서 **모든 리소스** 단추를 선택하고 프로비저닝 서비스를 엽니다.

2. Device Provisioning Service 메뉴에서 **등록 관리** 를 선택합니다. **개별 등록** 탭을 선택하고 맨 위에서 **개별 등록 추가** 단추를 선택합니다. 

3. **등록 추가** 패널에서 다음 정보를 입력합니다.
   - ID 증명 *메커니즘* 으로 **X.509** 를 선택합니다.
   - *기본 인증서 .pem 또는 .cer 파일* 아래에서 *파일 선택* 을 선택하여 이전 단계에서 만든 **certificate.cer** 인증서 파일을 선택합니다.
   - **디바이스 ID** 를 비워 둡니다. 디바이스 ID가 **iothubx509device1**(X.509 인증서의 CN(일반 이름))로 설정된 디바이스가 프로비전됩니다. 이 일반 이름은 개별 등록 항목의 등록 ID에 사용되는 이름이기도 합니다. 
   - 필요에 따라 다음 정보를 입력합니다.
       - 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
       - 디바이스에 대해 원하는 초기 구성으로 **초기 디바이스 쌍 상태** 를 업데이트합니다.
   - 완료되면 **저장** 단추를 누릅니다. 

     [![포털에서 X.509 증명에 대한 개별 등록 추가](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   성공적으로 등록되면 *개별 등록* 탭의 *등록 ID* 열 아래에 X.509 등록 항목이 **iothubx509device1** 로 표시됩니다. 



## <a name="provision-the-device"></a>디바이스 프로비전

1. 프로비저닝 서비스에 대한 **개요** 블레이드의 **_ID 범위_** 값을 적어 둡니다.

    ![포털 블레이드에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. 다음 명령을 입력하여 X.509 디바이스 프로비전 샘플을 빌드하고 실행합니다. `<IDScope>` 값을 프로비전 서비스에 대한 ID 범위로 바꿉니다. 

    인증서 파일은 기본적으로 *./certificate.pfx* 로 설정되며 .pfx 암호를 입력하라는 메시지가 표시됩니다.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    모든 항목을 매개 변수로 전달하려는 경우 다음 예제 형식을 사용할 수 있습니다.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. 디바이스가 DPS에 연결되고 IoT Hub에 할당됩니다. 디바이스는 추가로 원격 분석 메시지를 허브로 보냅니다.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. 디바이스가 프로비전되었는지 확인합니다. 프로비저닝 서비스와 연결된 IoT 허브에 디바이스를 성공적으로 프로비저닝하면 디바이스 ID가 허브의 **IoT 디바이스** 블레이드에 표시됩니다. 

    ![디바이스가 IoT 허브에 등록됨](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    디바이스에 대한 등록 항목의 기본값으로부터 *초기 디바이스 쌍 상태* 를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

디바이스 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 디바이스 클라이언트 샘플 출력 창을 닫습니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, Device Provisioning Service를 선택합니다. **개요** 블레이드의 위쪽에서, 창 맨 위에 있는 **삭제** 를 누릅니다.  
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스** 를 선택한 다음, 사용자의 IoT 허브를 선택합니다. **개요** 블레이드의 위쪽에서, 창 맨 위에 있는 **삭제** 를 누릅니다.  

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure IoT Hub Device Provisioning Service를 사용하여 X.509 디바이스를 IoT 허브에 프로비저닝했습니다. 프로그래밍 방식으로 X.509 디바이스를 등록하는 방법을 알아보려면 프로그래밍 방식으로 X.509 디바이스를 등록하는 빠른 시작을 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service에 X.509 디바이스 등록](quick-enroll-device-x509-csharp.md)

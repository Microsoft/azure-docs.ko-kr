---
title: Azure 방법 - Azure에서 Device Provisioning 서비스 클라이언트 SDK와 함께 다른 증명 메커니즘을 사용하는 방법
description: Azure 방법 - Azure에서 Device Provisioning 서비스 클라이언트 SDK와 함께 다른 증명 메커니즘을 사용하는 방법
author: yzhong94
ms.author: yizhon
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.custom: mvc
ms.openlocfilehash: af59ccc6d14dce49d06e178aac3ecafc29bd982c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248133"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Azure에서 C용 Device Provisioning 서비스 클라이언트 SDK와 함께 다른 증명 메커니즘을 사용하는 방법

이 문서에서는 C용 Device Provisioning 서비스 클라이언트 SDK에서 다른 [증명 메커니즘](concepts-security.md#attestation-mechanism)을 사용하는 방법을 보여줍니다. 물리적 디바이스 또는 시뮬레이터를 사용할 수 있습니다. 프로 비전 서비스는 두 가지 유형의 증명 메커니즘에 대 한 인증을 지원합니다. X.509 및 신뢰할 수 있는 플랫폼 모듈 (TPM)입니다.

## <a name="prerequisites"></a>필수 조건

[시뮬레이션된 디바이스 만들기 및 프로비전](./quick-create-simulated-device.md) 가이드의 "개발 환경 준비" 섹션에 따라 개발 환경을 준비합니다.

### <a name="choose-an-attestation-mechanism"></a>증명 메커니즘 선택

디바이스 제조자는 먼저 지원되는 형식 중 하나를 기반으로 하는 증명 메커니즘을 선택해야 합니다. 현재 [C용 Device Provisioning 서비스 클라이언트 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client)는 다음과 같은 증명 메커니즘을 지원합니다. 

- [신뢰할 수 있는 플랫폼 모듈 (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM은 몇 개의 Linux/Ubuntu 기반 디바이스뿐만 아니라 대부분의 Windows 기반 디바이스 플랫폼에 대해 설정된 표준입니다. 디바이스 제조자는 디바이스에서 이러한 OS 중 하나가 실행되는 경우 및 설정된 표준을 찾는 경우 이 증명 메커니즘을 선택할 수 있습니다. TPM 칩을 사용하면 Device Provisioning Service에 개별적으로 각 디바이스를 등록할 수 있습니다. 개발을 위해 Windows 또는 Linux 개발 컴퓨터에서 TPM 시뮬레이터를 사용할 수 있습니다.

- [X.509](https://cryptography.io/en/latest/x509/): X.509 인증서 이라는 비교적 최신 칩에 저장할 수 있습니다 [하드웨어 보안 모듈 (HSM)](concepts-security.md#hardware-security-module)합니다. X.509 인증서를 구현하는 RIoT 또는 DICE에 대한 작업이 Microsoft 내에서 진행 중입니다. X.509 칩을 사용하면 포털에서 디바이스 등록을 대량으로 수행할 수 있습니다. 또한 임베디드 OS와 같은 특정 비Windows OS를 지원합니다. 개발 목적을 위해 Device Provisioning Service 클라이언트 SDK는 X.509 디바이스 시뮬레이터를 지원합니다. 

자세한 내용은 IoT Hub Device Provisioning 서비스 [보안 개념](concepts-security.md) 및 [자동 프로비전 개념](/azure/iot-dps/concepts-auto-provisioning)을 참조하세요.

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>지원되는 증명 메커니즘에 인증을 사용하도록 설정

Azure Portal에서 SDK 인증 모드(X **.** 509 또는 TPM)를 등록하려면 먼저 실제 디바이스 또는 시뮬레이터를 사용하도록 설정해야 합니다. 먼저 azure-iot-sdk-c의 루트 폴더로 이동합니다. 그 후 선택한 인증 모드에 따라 지정된 명령을 실행합니다.

### <a name="use-x509-with-simulator"></a>시뮬레이터를 통해 X **.** 509 사용

프로비전 서비스는 디바이스 인증을 위한 X **.** 509 인증서를 생성하는 DICE(Device Identity Composition Engine) 에뮬레이터와 함께 제공됩니다. X **.** 509 인증을 사용하도록 설정하려면 다음 명령을 실행합니다. 

```
cmake -Ddps_auth_type=x509 ..
```

DICE가 포함된 하드웨어에 대한 자세한 내용은 [여기](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)에 있습니다.

### <a name="use-x509-with-hardware"></a>하드웨어를 통해 X **.** 509 사용

프로비전 서비스는 다른 하드웨어에서 X **.** 509를 통해 사용할 수 있습니다. 하드웨어와 SDK 간의 인터페이스는 연결을 설정하는 데 필요합니다. 인터페이스에 대한 정보는 HSM 제조업체에 문의하세요.

### <a name="use-tpm"></a>TPM 사용

프로비전 서비스에서 SAS 토큰을 사용하여 Windows 및 Linux 하드웨어 TPM 칩에 연결할 수 있습니다. TPM 인증을 사용하도록 설정하려면 다음 명령을 실행합니다.

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>시뮬레이터를 통해 TPM 사용

TPM 칩이 있는 디바이스가 없으면 개발 용도로 Windows OS에서 시뮬레이터를 사용할 수 있습니다. TPM 인증을 사용하도록 설정하고 TPM 시뮬레이터를 실행하려면 다음 명령을 실행합니다.

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>SDK 빌드
먼저 SDK를 빌드한 후에 디바이스 등록을 만들어야 합니다.

### <a name="linux"></a>Linux
- Linux에서 SDK를 빌드하려면 다음을 수행합니다.
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- 디버그 이진 파일을 빌드하려면 해당 CMake 옵션을 프로젝트 생성 명령에 추가합니다. 예를 들면 다음과 같습니다.
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- SDK를 빌드하는 데 사용할 수 있는 [CMake 구성 옵션](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html)이 많이 있습니다. 예를 들어 CMake 프로젝트 생성 명령에 인수를 추가하여 사용 가능한 프로토콜 스택 중 하나를 해제할 수 있습니다.
    ```
    cmake -Duse_amqp=OFF ..
    ```
- 단위 테스트를 빌드하여 실행할 수도 있습니다.
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Windows에서 SDK를 빌드하려면 다음 단계를 수행하여 프로젝트 파일을 생성합니다.
  - "VS2015용 개발자 명령 프롬프트"를 엽니다.
  - 리포지토리의 루트에서 다음의 CMake 명령을 실행합니다.
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    이 명령은 x86 라이브러리를 빌드합니다. x64 라이브러리로 빌드하려면 cmake 생성기 인수를 다음과 같이 수정합니다. 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- 프로젝트가 성공적으로 생성되면 `cmake` 폴더 아래에 Visual Studio 솔루션 파일(.sln)이 표시됩니다. SDK를 빌드하려면 다음을 수행합니다.
    - Visual Studio에서 **cmake\azure_iot_sdks.sln**을 열고 빌드합니다. **또는**
    - 프로젝트 파일을 생성하는 데 사용된 명령 프롬프트에서 다음 명령을 실행합니다.
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- 디버그 이진 파일을 빌드하려면 해당 MSBuild 인수를 사용합니다. 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- SDK를 빌드하는 데 사용할 수 있는 CMake 구성 옵션이 많이 있습니다. 예를 들어 CMake 프로젝트 생성 명령에 인수를 추가하여 사용 가능한 프로토콜 스택 중 하나를 해제할 수 있습니다.
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- 단위 테스트를 빌드하여 실행할 수도 있습니다.
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>포함할 라이브러리
- 다음 라이브러리는 SDK에 포함되어야 합니다.
    - 프로비전 서비스: dps_http_transport, dps_client, dps_security_client
    - IoTHub 보안: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Device Provisioning Service에서 디바이스 등록 항목 만들기

### <a name="tpm"></a>TPM
TPM을 사용하는 경우 ["Azure IoT Hub Device Provisioning Service를 사용하여 시뮬레이션된 디바이스 만들기 및 프로비전"](./quick-create-simulated-device.md)의 지침에 따라 Device Provisioning Service에 디바이스 등록 항목을 만들고 첫 번째 부팅을 시뮬레이션합니다.

### <a name="x509"></a>X **.** 509

1. 프로비전 서비스에 디바이스를 등록하려면 클라이언트 SDK에서 제공하는 프로비전 도구에 표시된 각 디바이스에 대한 인증 키와 등록 ID를 기록해 둡니다. 다음 명령을 실행하여 루트 CA 인증서(그룹 등록의 경우) 및 리프 인증서(개별 등록의 경우)를 출력합니다.
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다.
   - X **.** 509 개별 등록: 프로 비전 서비스 요약 블레이드를 선택 **등록 관리**합니다. **개별 등록** 탭을 선택하고 맨 위에서 **추가** 단추를 클릭합니다. ID 증명 *메커니즘*으로 **X**.**509**를 선택하고, 블레이드에서 요구하는 대로 리프 인증서를 업로드합니다. 완료되면 **저장** 단추를 클릭합니다. 
   - X **.** 509 그룹 등록: 프로 비전 서비스 요약 블레이드를 선택 **등록 관리**합니다. **그룹 등록** 탭을 선택하고, 위쪽에 있는 **추가** 단추를 클릭합니다. ID 증명 *메커니즘*으로 **X**.**509**를 선택하고, 그룹 이름과 인증 이름을 입력하고, 블레이드에서 요구하는 대로 CA/중간 인증서를 업로드합니다. 완료되면 **저장** 단추를 클릭합니다. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>사용자 지정 증명 메커니즘을 사용하여 디바이스에 대한 인증을 사용하도록 설정(선택 사항)

> [!NOTE]
> 이 섹션은 현재 C용 Device Provisioning 서비스 클라이언트 SDK에 의해 지원되지 않는 사용자 지정 플랫폼 또는 증명 메커니즘에 대한 지원이 필요한 디바이스에만 적용될 수 있습니다. SDK에서는 "증명 메커니즘"을 대신해 제네릭 대체로 "HSM"이라는 용어를 자주 사용합니다.

먼저 사용자 지정 증명 메커니즘에 대한 리포지토리 및 라이브러리를 개발해야 합니다.

1. 증명 메커니즘에 액세스하는 라이브러리를 개발합니다. 이 프로젝트에서는 사용할 디바이스 프로비저닝 SDK에 대한 정적 라이브러리를 생성해야 합니다.

2. 라이브러리에서 다음 헤더 파일에 정의된 함수를 구현합니다. 

    - 사용자 지정 TPM: [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) 아래에 정의된 함수를 구현합니다.  
    - 사용자 지정 X.509: [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) 아래에 정의된 함수를 구현합니다. 

라이브러리가 성공적으로 빌드되면 라이브러리에 연결하여 Device Provisioning Service 클라이언트 SDK와 통합해야 합니다. :

1. 다음 `cmake` 명령에서 사용자 지정 GitHub 리포지토리 및 라이브러리를 입력합니다.
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. CMake로 빌드한 Visual Studio 솔루션 파일(`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`)을 열고 빌드합니다. 

    - 빌드 프로세스에서 SDK 라이브러리를 컴파일합니다.
    - SDK는 `cmake` 명령에 정의된 사용자 지정 라이브러리에 대한 연결을 시도합니다.

3. "Provision_Samples"(`\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample` 아래) 아래에서 "prov_dev_client_ll_sample" 샘플 앱을 실행하여 사용자 지정 증명 메커니즘이 올바르게 구현되었는지 확인합니다.

## <a name="connecting-to-iot-hub-after-provisioning"></a>프로비전 후 IoT Hub에 연결

디바이스가 프로비전 서비스를 통해 프로비전되면 다음 API에서 지정된 인증 모드(X **.** 509 또는 TPM)를 사용하여 IoT Hub에 연결합니다. 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

---
title: 자습서 - Azure IoT Hub Device Provisioning Service를 위한 디바이스 설정
description: 이 자습서에서는 디바이스 제조 프로세스 중 IoT Hub DPS(Device Provisioning Service)를 통해 프로비저닝할 디바이스를 설정하는 방법을 보여줍니다.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6ff732888e416fcd51216070b3b30ed37b79e92c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "84687127"
---
# <a name="tutorial-set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>자습서: Azure IoT Hub Device Provisioning Service를 사용하여 디바이스 프로비전

이전 자습서에서 디바이스를 IoT Hub에 자동으로 프로비전하도록 Azure IoT Hub Device Provisioning Service를 설정하는 방법을 배웠습니다. 이 자습서에서는 IoT Hub가 자동으로 프로비전되도록 제조 과정에서 디바이스를 설정하는 방법을 보여줍니다. 디바이스를 처음으로 부팅하고 프로비전 서비스에 연결할 때 디바이스의 [증명 메커니즘](concepts-device.md#attestation-mechanism)에 따라 디바이스가 프로비전됩니다. 이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 플랫폼별 디바이스 프로비전 서비스 클라이언트 SDK 빌드
> * 보안 아티팩트 추출
> * 디바이스 등록 소프트웨어 만들기

이 자습서에서는 여러분이 이미 [클라우드 리소스 설정](tutorial-set-up-cloud.md) 자습서의 지침에 따라 Device Provisioning Service 인스턴스 및 IoT Hub를 만든 것으로 가정합니다.

이 자습서에서는 C용 Device Provisioning Service 클라이언트 SDK를 포함하고 있는 [Azure IoT SDK 및 C 리포지토리용 라이브러리](https://github.com/Azure/azure-iot-sdk-c)를 사용합니다. 이 SDK는 현재 Windows 또는 Ubuntu 구현에서 실행 중인 디바이스에 TPM 및 X.509 지원을 제공합니다. 이 자습서는 Windows 개발 클라이언트 사용을 기반으로 하며, 마찬가지로 사용자가 Visual Studio의 기본적인 내용을 알고 있다고 가정합니다. 

자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](concepts-auto-provisioning.md)을 검토하세요. 


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

다음 필수 구성 요소는 Windows 개발 환경을 위한 것입니다. Linux 또는 macOS의 경우 SDK 설명서에서 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)의 해당 섹션을 참조하세요.

* ['C++를 사용한 데스크톱 개발'](https://docs.microsoft.com/cpp/?view=vs-2019#pivot=workloads) 워크로드를 사용하도록 설정된 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019. Visual Studio 2015와 Visual Studio 2017도 지원됩니다.

* 최신 버전의 [Git](https://git-scm.com/download/) 설치

## <a name="build-a-platform-specific-version-of-the-sdk"></a>플랫폼별 SDK 버전 빌드

Device Provisioning Service 클라이언트 SDK는 디바이스 등록 소프트웨어를 구현하는 데 도움을 줍니다. 하지만 사용하려면 개발 클라이언트 플랫폼 및 증명 메커니즘과 관련된 SDK의 버전을 빌드해야 합니다. 이 자습서에서는 Windows 개발 플랫폼에서 지원되는 증명 형식에 Visual Studio를 사용하는 SDK를 빌드합니다.

1. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드합니다.

    `CMake` 설치를 시작하기 **전에** Visual Studio 필수 구성 요소(Visual Studio 및 'C++를 사용한 데스크톱 개발' 워크로드)를 머신에 설치해야 합니다. 필수 구성 요소가 설치되고 다운로드를 확인하면 CMake 빌드 시스템을 설치합니다.

2. SDK의 [최신 릴리스](https://github.com/Azure/azure-iot-sdk-c/releases/latest)에 대한 태그 이름을 찾습니다.

3. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행하여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제합니다. 이전 단계에서 찾은 태그를 `-b` 매개 변수의 값으로 사용합니다.

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

4. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. `azure-iot-sdk-c` 디렉터리에서 다음 명령을 실행합니다.

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 사용할 증명 메커니즘을 기반으로 개발 플랫폼의 SDK를 빌드합니다. 다음 명령 중 하나를 사용합니다(또한 명령마다 후행 마침표가 2개 있는 것에 주의). 완료되면 CMake가 사용자의 디바이스 관련 콘텐츠를 포함하는 `/cmake` 하위 디렉터리를 빌드합니다.
 
    - 증명에 TPM 시뮬레이터를 사용하는 디바이스의 경우:

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

    - 그 외의 다른 디바이스(물리적 TPM/HSM/X.509 또는 시뮬레이션된 X.509 인증서):

        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```


이제 SDK를 사용하여 디바이스 등록 코드를 빌드할 준비가 완료되었습니다. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>보안 아티팩트 추출 

다음 단계는 디바이스에서 사용하는 증명 메커니즘에 대한 보안 아티팩트를 추출하는 것입니다. 

### <a name="physical-devices"></a>물리적 디바이스 

물리적 TPM/HSM에 증명을 사용하도록 SDK를 빌드했는지 아니면 X.509 인증서를 사용했는지에 따라 보안 아티팩트를 수집하는 방법은 다음과 같습니다.

- TPM 디바이스의 경우 TPM 칩 제조업체로부터 해당 디바이스에 연결되어 있는 **인증 키**를 확인해야 합니다. 등록 키를 해시하여 사용자 TPM 디바이스에 대한 고유한 **등록 ID**를 얻을 수 있습니다.  

- X.509 디바이스의 경우 디바이스에 발급된 인증서를 가져와야 합니다. 프로비전 서비스는 X.509 증명 메커니즘을 사용하는 디바이스에 대한 액세스를 제어하는 두 가지 유형의 등록 항목을 공개합니다. 필요한 인증서는 사용할 등록 유형에 따라 다릅니다.

    - 개별 등록: 특정 단일 디바이스를 등록합니다. 이 유형의 등록 항목은 [최종 엔터티, "리프", 인증서](concepts-security.md#end-entity-leaf-certificate)가 필요합니다.
    
    - 등록 그룹: 이 유형의 등록 항목은 중간 또는 루트 인증서가 필요합니다. 자세한 내용은 [X.509 인증서를 사용하여 프로비전 서비스에 대한 디바이스 액세스 제어](concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)를 참조하세요.

### <a name="simulated-devices"></a>시뮬레이션된 디바이스

시뮬레이션된 디바이스에 증명을 사용하도록 SDK를 빌드하기 위해 TPM을 사용했는지 아니면 X.509 인증서를 사용했는지에 따라 보안 아티팩트를 수집하는 방법은 다음과 같습니다.

- 시뮬레이션된 TPM 디바이스:

   1. Windows 명령 프롬프트를 열고, `azure-iot-sdk-c` 하위 디렉터리로 이동하여 TPM 시뮬레이터를 실행합니다. 포트 2321 및 2322에서 소켓을 수신 대기합니다. 이 명령 창을 닫지 마세요. 다음 빠른 시작 가이드가 끝날 때까지 이 시뮬레이터가 계속 실행되어야 합니다. 

      `azure-iot-sdk-c` 하위 디렉터리에서 다음 명령을 실행하여 시뮬레이터를 시작합니다.

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

      > [!NOTE]
      > 이 단계에서 Git Bash 명령 프롬프트를 사용하는 경우 백슬래시를 슬래시로 변경해야 합니다(예: `./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe`).

   1. Visual Studio를 사용하여 *cmake* 폴더에 생성된 `azure_iot_sdks.sln` 솔루션을 열고, "빌드" 메뉴에서 "솔루션 빌드" 명령을 사용하여 빌드합니다.

   1. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Tools** 폴더로 이동합니다. **tpm_device_provision** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 선택합니다. 

   1. "디버그" 메뉴에서 "시작" 명령 중 하나를 사용하여 솔루션을 실행합니다. 디바이스 등록에 필요한 TPM 시뮬레이터의 **_등록 ID_** 와 **_인증 키_** 가 출력 창에 표시됩니다. 나중에 사용할 수 있도록 이러한 값을 복사합니다. 이 창을 닫아도 되지만(등록 ID 및 인증 키를 얻은 후) 1단계에서 시작한 TPM 시뮬레이터 창은 계속 실행되도록 두세요.

- 시뮬레이션된 X.509 디바이스:

  1. Visual Studio를 사용하여 *cmake* 폴더에 생성된 `azure_iot_sdks.sln` 솔루션을 열고, "빌드" 메뉴에서 "솔루션 빌드" 명령을 사용하여 빌드합니다.

  1. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Tools** 폴더로 이동합니다. **dice\_device\_enrollment** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 
  
  1. "디버그" 메뉴에서 "시작" 명령 중 하나를 사용하여 솔루션을 실행합니다. 메시지가 표시되면 출력 창에서 개별 등록에 대해 **i**를 입력합니다. 출력 창에는 시뮬레이션된 디바이스에 대해 로컬로 생성된 X.509 인증서가 표시됩니다. *-----BEGIN CERTIFICATE-----* 에서 시작하여 첫 번째 *-----END CERTIFICATE-----* 에서 끝나는 출력(이 두 줄 모두 포함)을 클립보드에 복사합니다. 출력 창의 첫 번째 인증서만 필요합니다.
 
  1. **_X509testcert.pem_** 이라는 파일을 만들어 원하는 텍스트 편집기에서 연 다음, 클립보드의 내용을 이 파일에 복사합니다. 나중에 디바이스를 등록할 때 사용해야 하므로 파일을 저장해 둡니다. 등록 소프트웨어를 실행하면 자동 프로비전과 동일한 인증서를 사용합니다.    

이러한 보안 아티팩트는 디바이스를 Device Provisioning Service에 등록할 때 꼭 필요합니다. 프로비전 서비스는 향후 특정한 시점에 이러한 디바이스가 부팅 및 연결될 때까지 대기합니다. 디바이스가 처음으로 부팅되면 클라이언트 SDK 논리가 칩(또는 시뮬레이터)과 상호 작용하여 디바이스에서 보안 아티팩트를 추출하고, Device Provisioning Service에 등록을 확인합니다. 

## <a name="create-the-device-registration-software"></a>디바이스 등록 소프트웨어 만들기

마지막 단계는 장치를 IoT Hub 서비스에 등록하도록 Device Provisioning Service 클라이언트 SDK를 사용하는 등록 애플리케이션을 작성하는 것입니다. 

> [!NOTE]
> 이 단계에서는 워크스테이션에서 SDK 샘플 등록 애플리케이션을 실행하여 얻은 시뮬레이션된 디바이스를 사용하는 것으로 가정합니다. 하지만 물리적 디바이스에 배포할 등록 애플리케이션을 작성하는 경우에도 동일한 개념이 적용됩니다. 

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 블레이드를 선택하고 **_ID 범위_** 값을 복사합니다. *ID 범위*는 서비스에 의해 생성되고 고유성이 보장됩니다. 이는 변경할 수 없으며 등록 ID를 고유하게 식별하는 데 사용됩니다.

    ![포털 블레이드에서 디바이스 프로비저닝 서비스 엔드포인트 정보 추출](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

1. 컴퓨터의 Visual Studio *솔루션 탐색기*에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample**이라는 샘플 프로젝트를 선택하고, **prov\_dev\_client\_sample.c** 원본 파일을 엽니다.

1. 1단계에서 얻은 _ID 범위_ 값을 `id_scope` 변수에 할당합니다(왼쪽/`[` 및 오른쪽/`]` 대괄호 제거). 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    참고로, `global_prov_uri` 변수는 IoT Hub 클라이언트 등록 API `IoTHubClient_LL_CreateFromDeviceAuth`가 지정된 Device Provisioning Service 인스턴스에 연결하는 것을 허용합니다.

1. 동일한 파일의 **main()** 함수에서, 디바이스의 등록 소프트웨어에서 사용 중인 증명 메커니즘(TPM 또는 X.509)과 일치하는 `hsm_type` 변수에 대한 주석을 달거나 주석 처리 제거합니다. 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

1. 변경 내용을 저장하고 "빌드" 메뉴에서 "솔루션 빌드"를 선택하여 **prov\_dev\_client\_sample** 샘플을 다시 빌드합니다. 

1. **Provision\_Samples** 폴더 아래에서 **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 아직 애플리케이션 예제를 실행하지 마세요.

> [!IMPORTANT]
> 아직 디바이스를 실행/시작하지 마세요! 디바이스를 시작하기 전에 먼저 Device Provisioning Service에 디바이스를 등록하여 프로세스를 마쳐야 합니다. 아래의 다음 단계에서는 다음 문서를 안내합니다.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>등록 시 사용되는 SDK API(참조용으로만 사용)

참고로, SDK는 등록 시 사용할 애플리케이션에 다음과 같은 API를 제공합니다. 이러한 API는 디바이스가 부팅되면 Device Provisioning Service에 연결 및 등록하도록 도와줍니다. 그러면 디바이스가 IoT Hub 인스턴스와 연결을 설정하는 데 필요한 정보를 수신합니다.

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

처음에는 시뮬레이션된 장치를 사용하고, 그 다음에는 테스트 서비스 설정을 사용하여 Device Provisioning Service 클라이언트 등록 애플리케이션을 구체화해야 할 수도 있습니다. 애플리케이션이 테스트 환경에서 작동되면 특정 디바이스에 대해 빌드하고, 실행 파일을 사용자 디바이스 이미지에 복사할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

현재 포털에서 Device Provisioning 및 IoT Hub 서비스가 실행 중일 것입니다. 디바이스 프로비전 설정을 중단하고/하거나 이 자습서 시리즈의 완료 시기를 미루려면 불필요한 비용이 발생하지 않도록 서비스를 종료하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 사용자의 Device Provisioning Service를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 플랫폼별 Device Provisioning Service 클라이언트 SDK 빌드
> * 보안 아티팩트 추출
> * 디바이스 등록 소프트웨어 만들기

자동 프로비전을 위한 Azure IoT Hub Device Provisioning Service에 등록하여 IoT Hub에 디바이스를 프로비전하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [IoT Hub에 디바이스 프로비전](tutorial-provision-device-to-hub.md)

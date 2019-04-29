---
title: 투명한 게이트웨이 디바이스 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디바이스를 다운스트림 디바이스의 정보를 처리할 수 있는 투명한 게이트웨이로 사용
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 722ee6197b467454818026c960e1ce0e5b39efb4
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766313"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

이 문서에서는 IoT Hub와 통신하는 다른 디바이스에 대해 투명한 게이트웨이로 작동하는 IoT Edge 디바이스를 구성하기 위한 자세한 지침을 제공합니다. 이 문서에서 *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 디바이스를 의미합니다. 자세한 내용은 [어떻게는 IoT Edge 장치를 게이트웨이로 사용할 수](./iot-edge-as-gateway.md)입니다.

>[!NOTE]
>현재 상황:
> * 게이트웨이가 IoT Hub와의 연결이 끊긴 경우 다운스트림 디바이스는 게이트웨이를 통해 인증할 수 없습니다.
> * Edge 가능 디바이스는 IoT Edge 게이트웨이에 연결할 수 없습니다. 
> * 다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

디바이스가 게이트웨이로 작동하려면 다운스트림 디바이스에 안전하게 연결할 수 있어야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우에 투명한 게이트웨이로 작동하는 IoT Edge 디바이스에 다운스트림 디바이스를 연결할 수 있습니다. 적절 한 보안 유지를 위해 다운스트림 장치는 IoT Edge 장치 id를 확인 해야 합니다. 장치 프로그램 게이트웨이만 악성 아닌 게이트웨이를 연결 해야 합니다.

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 모든 실질적인 용도의 경우 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수 있습니다. 

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 사용 하도록 설정 하는 데 사용 하는 동일한 인증서 설정을 가정 [X.509 CA 보안](../iot-hub/iot-hub-x509ca-overview.md) (IoT hub 소유자 CA)에 특정 IoT hub와 일련의 인증서에 연결 된 X.509 CA 인증서를 포함 하는 IoT Hub에서 IoT Edge 장치에 대 한이 CA와 CA 서명 합니다.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

게이트웨이 연결의 시작 하는 동안 해당 IoT Edge 장치 CA 인증서는 다운스트림 장치를 표시합니다. 다운스트림 장치에 IoT Edge 장치 CA 인증서 소유자 CA 인증서로 서명 되어 있는지 확인 합니다. 이 프로세스를 사용하면 다운스트림 디바이스는 게이트웨이가 신뢰되는 원본에서 전송되었는지 확인할 수 있습니다.

다음 단계에서는 인증서를 만들고 적절한 위치에 설치하는 프로세스를 설명합니다.

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 디바이스를 게이트웨이로 구성합니다. 다음 운영 체제에 대 한 IoT Edge 설치 단계를 사용 합니다.
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다.

>[!NOTE]
>이 명령에서 인증서를 만드는 데 "게이트웨이 이름이" GatewayHostName 다운스트림 장치 연결 문자열에 IoT Edge config.yaml 파일에는 호스트와 동일한 이름으로 사용 해야 합니다. "게이트웨이 이름이" DNS 또는 호스트 파일 항목을 사용 하거나 IP 주소를 확인할 수 있어야 합니다. 통신 프로토콜을 기반으로 사용 되는 (MQTTS:8883 / AMQPS:5671 / HTTPS:433) 다운스트림 장치에서 IoT Edge 투명 사이의 수 여야 합니다. 방화벽 사이 인 경우 해당 포트를 열어야 해야 합니다.

## <a name="generate-certificates-with-windows"></a>Windows를 사용하여 인증서 생성

Windows 디바이스에서 테스트 인증서를 생성하려면 이 섹션의 단계를 사용합니다. Windows IoT Edge 장치에 인증서를 생성 하려면 다음이 단계를 사용할 수 있습니다. 또는 Windows 개발 컴퓨터에 인증서를 생성 하 고 그런 다음 모든 IoT Edge 장치에 복사 합니다. 

이 섹션에서 생성된 인증서는 테스트 목적으로만 설계되었습니다. 

### <a name="install-openssl"></a>OpenSSL 설치

인증서를 생성하는 데 사용하는 머신에서 Windows용 OpenSSL을 설치합니다. 다양한 방법으로 OpenSSL을 설치할 수 있습니다.

   >[!NOTE]
   >Windows 디바이스에 이미 OpenSSL을 설치한 경우 이 단계를 건너뛰어도 되지만 PATH 환경 변수에서 openssl.exe를 사용할 수 있는지 확인하세요.

* **간편:** [타사 OpenSSL 이진 파일](https://wiki.openssl.org/index.php/Binaries)을 다운로드하여 설치합니다(예: [SourceForge의 이 프로젝트](https://sourceforge.net/projects/openssl/)). PATH 환경 변수에 openssl.exe에 대한 전체 경로를 추가합니다. 
   
* **권장:** 직접 또는 [vcpkg](https://github.com/Microsoft/vcpkg)를 통해 OpenSSL 소스 코드를 다운로드하고 머신에서 이진 파일을 빌드합니다. 아래에 나열된 지침은 vcpkg를 사용하여 간단한 단계를 통해 소스 코드를 다운로드하고, 컴파일하고, Windows 머신에 OpenSSL을 설치합니다.

   1. vcpkg를 설치하려는 디렉터리로 이동합니다. 이 디렉터리를 *\<VCPKGDIR>* 이라고 부르겠습니다. 지침에 따라 [vcpkg](https://github.com/Microsoft/vcpkg)를 다운로드하고 설치합니다.
   
   2. vcpkg가 설치되면 powershell 프롬프트에서 다음 명령을 실행하여 Windows x64용 OpenSSL 패키지를 설치합니다. 설치를 완료하는 데 일반적으로 약 5분이 걸립니다.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. openssl.exe 파일을 호출할 수 있도록 `<VCPKGDIR>\installed\x64-windows\tools\openssl`을 PATH 환경 변수에 추가합니다.

### <a name="prepare-creation-scripts"></a>생성 스크립트 준비

C용 Azure IoT 디바이스 SDK에는 테스트 인증서를 생성하는 데 사용할 수 있는 스크립트가 포함됩니다. 이 섹션에서는 SDK를 복제하고 PowerShell을 구성합니다.

1. 관리자 모드에서 PowerShell 창을 엽니다. 

2. 비-프로덕션 인증서를 생성하는 스크립트가 포함된 git 리포지토리를 복제합니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. `git clone` 명령을 사용하거나 [ZIP을 다운로드](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip)합니다. 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. 작업하려는 디렉터리로 이동합니다. 이 디렉터리를 *\<WRKDIR>* 이라고 부르겠습니다.  모든 파일은 이 디렉터리에서 생성됩니다.

4. 구성 및 스크립트 파일을 작업 디렉터리로 복사합니다. 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   SDK를 ZIP으로 다운로드한 경우 폴더 이름은 `azure-iot-sdk-c-master`이며 경로의 나머지 부분은 동일합니다. 

5. openssl_root_ca.cnf 구성 파일을 사용하도록 환경 변수 OPENSSL_CONF를 설정합니다.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. 스크립트를 실행하도록 PowerShell을 활성화합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. 스크립트에서 사용된 함수를 PowerShell의 글로벌 네임스페이스로 가져옵니다.
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. OpenSSL이 올바르게 설치되었는지 확인하고, 기존 인증서와 이름 충돌이 없는지 확인합니다. 문제가 있으면 스크립트는 시스템에서 이러한 문제를 해결하는 방법을 설명해야 합니다.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>인증서 만들기

이 섹션에서는 세 가지 인증서를 만든 다음, 체인에서 연결합니다. 체인 파일에 인증서를 배치하면 IoT Edge 게이트웨이 디바이스 및 모든 다운스트림 디바이스에서 손쉽게 설치할 수 있습니다.  

1. 소유자 CA 인증서를 만들고 중간 인증서에 서명하도록 합니다. 인증서는 모두 *\<WRKDIR>* 에 배치됩니다.

      ```powershell
      New-CACertsCertChain rsa
      ```

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 인증서를 생성하는 동안 및 파일 이름을 지정하는 데 사용할 게이트웨이 디바이스에 대한 이름을 입력합니다. 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. 소유자 CA 인증서, 중간 인증서 및 다음 명령 사용 하 여 IoT Edge 장치 CA 인증서에서 인증서 체인을 만듭니다. 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   스크립트는 다음 인증서와 키를 만듭니다.
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>Linux를 사용하여 인증서 생성

Linux 디바이스에서 테스트 인증서를 생성하려면 이 섹션의 단계를 사용합니다. IoT Edge 디바이스 자체에서 인증서를 생성하거나 별도 머신을 사용하여 지원되는 운영 체제를 실행하는 모든 IoT Edge 디바이스에 최종 인증서를 복사할 수 있습니다. 

### <a name="prepare-creation-scripts"></a>생성 스크립트 준비

1. 비-프로덕션 인증서를 생성하는 스크립트가 포함된 git 리포지토리를 복제합니다. 이 스크립트를 통해 인증서가 투명한 게이트웨이를 설정하도록 만들 수 있습니다. 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. 작업하려는 디렉터리로 이동합니다. 이 디렉터리를 *\<WRKDIR>* 이라고 부르겠습니다.  모든 파일은 이 디렉터리에서 생성됩니다.
  
3. 구성 및 스크립트 파일을 작업 디렉터리로 복사합니다.

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. 제공된 스크립트를 사용하여 인증서를 생성하도록 OpenSSL을 구성합니다. 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>인증서 만들기

이 섹션에서는 세 가지 인증서를 만든 다음, 체인에서 연결합니다. 체인 파일에 인증서를 배치하면 IoT Edge 게이트웨이 디바이스 및 모든 다운스트림 디바이스에서 손쉽게 설치할 수 있습니다.  

1. 소유자 CA 인증서 및 중간 인증서를 만듭니다. 이러한 인증서는 *\<WRKDIR>* 에 배치됩니다.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   스크립트는 다음 인증서와 키를 만듭니다.
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. 다음 명령을 사용 하 여 IoT Edge 장치 CA 인증서와 개인 키를 만듭니다. 인증서를 생성하는 동안 및 파일 이름을 지정하는 데 사용할 게이트웨이 디바이스에 대한 이름을 입력합니다. 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   스크립트는 다음 인증서와 키를 만듭니다.
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. 라는 인증서 체인을 만들 **새-edge-장치-전체-chain.cert.pem** 소유자 CA 인증서, 중간 인증서 및 IoT Edge 장치 CA 인증서에서.

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>게이트웨이에 인증서 설치

이제 인증서 체인을 만들었으므로 IoT Edge 게이트웨이 디바이스에 설치하고 새 인증서를 참조하도록 IoT Edge 런타임을 구성해야 합니다. 

1. *\<WRKDIR>* 에서 다음 파일을 복사합니다. IoT Edge 디바이스 아무데나 저장합니다. IoT Edge 디바이스에 있는 대상 디렉터리를 *\<CERTDIR>* 이라고 하겠습니다. 

   IoT Edge 장치 자체에서 인증서를 생성 한 경우이 단계를 건너뛸 하 수 작업 디렉터리에 경로 사용 합니다.

   * 디바이스 CA 인증서 - `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * 디바이스 CA 개인 키 - `<WRKDIR>\private\new-edge-device.key.pem`
   * 소유자 CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. IoT Edge 보안 디먼 구성 파일을 엽니다. 

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. config.yaml 파일의 **certificate** 속성을 IoT Edge 디바이스에서 인증서 및 키 파일을 배치한 경로로 설정합니다.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux: 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. Linux 장치를 확인 하는 사용자 **iotedge** 읽기 인증서를 보유 하는 디렉터리에 대 한 권한이 있어야 합니다. 

## <a name="deploy-edgehub-to-the-gateway"></a>게이트웨이에 Edge Hub 배포

장치에서 IoT Edge를 처음 설치 하면 하나의 시스템 모듈 자동으로 시작: IoT Edge 에이전트입니다. 디바이스를 게이트웨이로 작동시키려면 시스템 모듈이 모두 필요합니다. 모든 모듈 하기 전에 게이트웨이 장치에 배포 하지 않은 경우에 두 번째 시스템 모듈을 IoT Edge hub를 시작 하기 위해 장치에 대 한 배포를 만듭니다. 배포는 마법사에서 모듈을 추가하지 않았으므로 비어 있는 상태로 표시되지만 두 시스템 모듈을 모두 배포하게 됩니다. 

`iotedge list` 명령을 사용하여 디바이스에서 실행되는 모듈을 확인할 수 있습니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하고 게이트웨이로 사용할 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정**을 선택합니다.

4. **다음**을 선택합니다.

5. **경로 지정** 페이지에서 모든 메시지를 모든 모듈에서 IoT Hub로 보내는 기본 경로가 있어야 합니다. 그렇지 않은 경우 다음 코드를 추가한 다음, **다음**을 선택합니다.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. **템플릿 검토** 페이지에서 **제출**을 선택합니다.

## <a name="open-ports-on-gateway-device"></a>게이트웨이 장치에서 포트 열기

표준 IoT Edge 장치는 IoT Hub를 사용 하 여 모든 통신이 아웃 바운드 연결을 통해 수행 되기 때문에 함수에 대 한 모든 인바운드 연결이 필요 하지 않습니다. 그러나 게이트웨이 장치는 다운스트림 장치에서 메시지를 받을 수 있으려면 필요 하기 때문에 다릅니다.

작동 하려면 게이트웨이 시나리오의 경우 다운스트림 장치에서 인바운드 트래픽을 위해 열린 IoT Edge 허브의 지원 되는 프로토콜의 하나 이상 이어야 합니다. 지원 되는 프로토콜에는 MQTT, AMQP 및 HTTPS 됩니다.

| 포트 | 프로토콜 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>다운스트림 디바이스에서 메시지 라우팅
IoT Edge 런타임은 모듈에서 전송한 메시지와 같은 다운스트림 디바이스에서 전송된 메시지를 라우팅할 수 있습니다. 이 기능을 사용 하면 클라우드로 모든 데이터를 보내기 전에 게이트웨이에서 실행 중인 모듈의 분석을 수행할 수 있습니다. 

현재 다운스트림 디바이스에서 보낸 메시지를 라우팅하는 방식은 모듈에 의해 전송 된 메시지와 구별됩니다. 모듈에서 보낸 메시지에는 모두 **connectionModuleId**라는 시스템 속성이 포함되지만 다운스트림 디바이스에서 보낸 메시지에는 포함되지 않습니다. 경로의 WHERE 절을 사용하여 해당 시스템 특성을 포함하는 메시지를 제외할 수 있습니다. 

아래 경로는 다운스트림 디바이스에서 `ai_insights`라는 모듈로 메시지를 보내는 데 사용됩니다.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>다음 단계

투명한 게이트웨이로 작동하는 IoT Edge 디바이스가 생겼습니다. 이제 게이트웨이를 신뢰하고 메시지를 보내도록 다운스트림 디바이스를 구성해야 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

---
title: 다운스트림 디바이스 연결 - Azure IoT Edge | Microsoft Docs
description: 다운스트림 또는 리프 장치를 Azure IoT Edge 게이트웨이 장치에 연결 하도록 구성 하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6ddda38d887cdfe30b449847e2f625ba17f33898
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510807"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결

이 문서에서는 다운스트림 장치와 IoT Edge 투명 게이트웨이 간에 트러스트 된 연결을 설정 하는 방법에 대 한 지침을 제공 합니다. 투명 게이트웨이 시나리오에서 하나 이상의 장치는 IoT Hub에 대 한 연결을 유지 하는 단일 게이트웨이 장치를 통해 메시지를 전달할 수 있습니다. 다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 다운스트림 장치는 IoT Edge 게이트웨이 장치 자체에서 실행 되는 응용 프로그램 일 수도 있습니다.

성공적인 투명 게이트웨이 연결을 설정 하는 세 가지 일반적인 단계가 있습니다. 이 문서에서는 세 번째 단계를 다룹니다.

1. 게이트웨이 장치는 다운스트림 장치에 안전 하 게 연결 하 고, 다운스트림 장치에서 통신을 수신 하 고, 메시지를 적절 한 대상으로 라우팅합니다. 자세한 내용은 [투명 한 게이트웨이로 작동 하도록 IoT Edge 장치 구성](how-to-create-transparent-gateway.md)을 참조 하세요.
2. 다운스트림 장치는 IoT Hub을 사용 하 여 인증 하 고 게이트웨이 장치를 통해 통신 하는 것을 알 수 있도록 장치 id가 필요 합니다. 자세한 내용은 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)을 참조 하세요.
3. **다운스트림 장치는 게이트웨이 장치에 안전 하 게 연결 해야 합니다.**

이 문서에서는 다운스트림 디바이스 연결과 관련된 일반적인 문제를 식별하고, 다음을 통해 다운스트림 디바이스를 설정하는 방법을 안내합니다.

* TLS(전송 계층 보안) 및 인증서 기본 사항을 설명합니다.
* 다른 운영 체제에서 TLS 라이브러리가 작동하는 방식 및 각 운영 체제가 인증서를 사용하는 방식을 설명합니다.
* 시작하는 데 도움이 되도록 여러 언어로 Azure IoT 샘플을 설명합니다.

이 문서에서 *게이트웨이* 및  *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 디바이스를 의미합니다.

## <a name="prerequisites"></a>필수 조건

* 에서 생성 된 **azure-iot-test-only** 인증서 파일을 사용 하 여 다운스트림 장치에서 사용 가능한 [투명 게이트웨이 역할을 하는 IoT Edge 장치를 구성](how-to-create-transparent-gateway.md) 합니다. 다운스트림 장치는이 인증서를 사용 하 여 게이트웨이 장치 id의 유효성을 검사 합니다.
* [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)에 설명 된 대로 게이트웨이 장치를 가리키는 수정 된 연결 문자열이 있어야 합니다.

## <a name="prepare-a-downstream-device"></a>다운스트림 디바이스 준비

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 다운스트림 장치는 IoT Edge 게이트웨이 장치 자체에서 실행 되는 응용 프로그램 일 수도 있습니다. 그러나 다른 IoT Edge 장치는 IoT Edge 게이트웨이의 다운스트림 일 수 없습니다.

>[!NOTE]
>IoT Hub에 등록 된 id를 가진 IoT 장치는 [모듈](../iot-hub/iot-hub-devguide-module-twins.md) 쌍을 사용 하 여 단일 장치에서 다른 프로세스, 하드웨어 또는 기능을 격리할 수 있습니다. IoT Edge 게이트웨이는 대칭 키 인증을 사용 하지만 x.509 인증서 인증을 사용 하지 않는 다운스트림 모듈 연결을 지원 합니다.

다운스트림 디바이스를 IoT Edge 게이트웨이에 연결하려면 다음 두 가지 항목이 필요합니다.

* 게이트웨이에 연결하기 위한 정보가 추가된 IoT Hub 디바이스 연결 문자열로 구성된 디바이스 또는 애플리케이션.

    이 단계는 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)에 설명 되어 있습니다.

* 장치 또는 응용 프로그램은 게이트웨이 장치에 대 한 TLS 연결의 유효성을 검사 하기 위해 게이트웨이의 **루트 CA** 인증서를 신뢰 해야 합니다.

    이 단계는이 문서의 나머지 부분에서 자세히 설명 합니다. 이 단계는 운영 체제의 인증서 저장소에 CA 인증서를 설치 하거나 Azure IoT Sdk를 사용 하 여 응용 프로그램 내에서 인증서를 참조 하 여 (특정 언어의 경우) 두 가지 방법 중 하나로 수행할 수 있습니다.

## <a name="tls-and-certificate-fundamentals"></a>TLS 및 인증서 기본 사항

IoT Edge에 다운스트림 디바이스를 안전하게 연결하는 문제는 인터넷을 통해 발생하는 다른 보안 클라이언트/서버 통신과 동일합니다. 클라이언트와 서버는 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용하여 인터넷을 통해 안전하게 통신합니다. TLS는 인증서라고 하는 표준 [PKI(공용 키 인프라)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 구문을 사용하여 빌드되었습니다. TLS는 매우 관련 된 사양 이며 두 엔드포인트의 보안과 관련 된 광범위 한 항목을 다룹니다. 이 섹션에서는 장치를 IoT Edge 게이트웨이에 안전 하 게 연결 하는 것과 관련 된 개념을 요약 합니다.

클라이언트를 서버에 연결할 때 서버에서는 *서버 인증서 체인*이라는 인증서의 체인을 제공합니다. 인증서 체인은 일반적으로 CA(루트 인증 기관) 인증서, 하나 이상의 중간 CA 인증서 및 마지막으로 자체 서버 인증서로 구성됩니다. 클라이언트는 전체 서버 인증서 체인을 암호화된 방식으로 확인하여 서버와의 신뢰를 설정합니다. 서버 인증서 체인의이 클라이언트 유효성 검사를 *서버 체인 유효성 검사*라고 합니다. 클라이언트는 *소유 증명*이라는 프로세스에서 서버 인증서와 연결 된 개인 키의 소유를 증명 하는 서비스를 요구 합니다. 서버 체인 유효성 검사와 소유 증명의 조합을 *서버 인증*이라고 합니다. 서버 인증서 체인의 유효성을 검사하려면 서버 인증서를 만드는(또는 발행하는) 데 사용된 루트 CA 인증서의 복사본이 클라이언트에 있어야 합니다. 일반적으로 웹 사이트에 연결할 때 브라우저는 일반적으로 사용되는 CA 인증서로 미리 구성되므로 클라이언트는 원활히 프로세스됩니다.

디바이스를 Azure IoT Hub에 연결할 때 디바이스는 클라이언트이며 IoT Hub 클라우드 서비스는 서버입니다. IoT Hub 클라우드 서비스는 공개적으로 널리 사용되는 **Baltimore CyberTrust Root**라는 루트 CA 인증서로 지원됩니다. 대부분의 디바이스에 IoT Hub CA 인증서가 이미 설치되어 있으므로 많은 TLS 구현(OpenSSL Schannel, LibreSSL)은 자동으로 서버 인증서 유효성 검사 동안에 해당 인증서를 사용합니다. IoT Hub에 성공적으로 연결할 수 있는 디바이스는 IoT Edge 게이트웨이에 연결을 시도하는 문제가 있을 수 있습니다.

디바이스를 IoT Edge 게이트웨이에 연결할 때 다운스트림 디바이스는 클라이언트이고 게이트웨이 디바이스는 서버입니다. 연산자(또는 사용자)는 Azure IoT Edge를 사용하여 적합하다고 판단되는 게이트웨이 인증서 체인을 빌드할 수 있습니다. 연산자는 Baltimore와 같은 공용 CA 인증서를 사용하거나 자체 서명된(또는 내부) 루트 CA 인증서를 사용하도록 선택할 수도 있습니다. 공용 CA 인증서는 종종 이와 관련된 비용이 있으므로 일반적으로 프로덕션 시나리오에서 사용됩니다. 자체 서명된 CA 인증서는 개발 및 테스트에 대해 기본 설정됩니다. 소개에 나열 된 투명 게이트웨이 설정 문서에서는 자체 서명 된 루트 CA 인증서를 사용 합니다.

IoT Edge 게이트웨이에 대한 자체 서명된 루트 CA 인증서를 사용하는 경우에는 설치하거나 게이트웨이에 연결을 시도하는 모든 다운스트림 디바이스에 제공해야 합니다.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

IoT Edge 인증서 및 일부 프로덕션 의미에 대해 자세히 알아보려면 [IoT Edge 인증서 사용량 세부 정보](iot-edge-certs.md)를 참조하세요.

## <a name="provide-the-root-ca-certificate"></a>루트 CA 인증서 제공

게이트웨이 장치의 인증서를 확인 하려면 다운스트림 장치에 루트 CA 인증서의 자체 복사본이 필요 합니다. IoT Edge git 리포지토리에 제공 된 스크립트를 사용 하 여 테스트 인증서를 만든 경우 루트 CA 인증서를 **azure-iot-test-only**라고 합니다. 다른 다운스트림 장치 준비 단계를 아직 수행 하지 않은 경우이 인증서 파일을 다운스트림 장치의 임의 디렉터리로 이동 합니다. [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.

## <a name="install-certificates-in-the-os"></a>OS에 인증서 설치

운영 체제의 인증서 저장소에 루트 CA 인증서를 설치 하면 일반적으로 대부분의 응용 프로그램에서 루트 CA 인증서를 사용할 수 있습니다. OS 인증서 저장소를 사용 하지 않고 노드 런타임의 내부 인증서 저장소를 사용 하는 NodeJS 응용 프로그램과 같은 몇 가지 예외가 있습니다. 운영 체제 수준에서 인증서를 설치할 수 없는 경우 [Azure IoT sdk에서 인증서 사용](#use-certificates-with-azure-iot-sdks)으로 건너뜁니다.

### <a name="ubuntu"></a>Ubuntu

다음 명령은 Ubuntu 호스트에서 CA 인증서를 설치하는 방법의 예제입니다. 이 예에서는 필수 구성 요소 문서에서 **azure-iot-test-only** 인증서를 사용 하 고 있으며,이 인증서를 다운스트림 장치의 위치로 복사 했다고 가정 합니다.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

"/Etc/ssl/certs...에서 인증서 업데이트" 라는 메시지가 표시 됩니다. 1 개 추가 됨, 0 개 제거 됨 완료 되었습니다. "

### <a name="windows"></a>Windows

다음 단계는 Windows 호스트에 CA 인증서를 설치하는 방법의 예제입니다. 이 예에서는 필수 구성 요소 문서에서 **azure-iot-test-only** 인증서를 사용 하 고 있으며,이 인증서를 다운스트림 장치의 위치로 복사 했다고 가정 합니다.

관리자 권한으로 PowerShell의 [가져오기-인증서](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) 를 사용 하 여 인증서를 설치할 수 있습니다.

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

또한 인증서를 설치 하려면 **다음을 사용 합니다.**

1. 시작 표시줄에서 **컴퓨터 인증서 관리**를 검색하고 선택합니다. **certlm**이라는 유틸리티가 열립니다.
2. **인증서 - 로컬 컴퓨터** > **신뢰할 수 있는 루트 인증 기관**으로 이동합니다.
3. **인증서**를 마우스 오른쪽 단추로 클릭하고 **모든 태스크** > **가져오기**를 선택합니다. 인증서 가져오기 마법사가 열립니다.
4. 지시에 따라 단계를 수행하고 인증서 파일(`<path>/azure-iot-test-only.root.ca.cert.pem`)을 가져옵니다. 완료되면 "가져오기에 성공했습니다." 메시지가 표시됩니다.

또한 이 문서의 뒷부분에 나오는 .NET 샘플에 표시된 대로 .NET API를 사용하여 프로그래밍 방식으로 인증서를 설치할 수 있습니다.

일반적으로 애플리케이션은 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel)이라는 TLS 스택이 제공된 Windows를 사용하여 TLS를 통해 안전하게 연결합니다. TLS 연결 설정을 시도하려면 Windows 인증서 스토리지에 설치된 인증서가 Schannel에 *필요합니다*.

## <a name="use-certificates-with-azure-iot-sdks"></a>Azure IoT SDK를 사용하여 인증서 사용

이 섹션에서는 Azure IoT SDK를 간단한 샘플 애플리케이션을 사용하여 IoT Edge 디바이스에 연결하는 방법을 설명합니다. 모든 샘플의 목표는 디바이스 클라이언트를 연결하고 게이트웨이에 원격 분석 메시지를 보낸 다음, 연결을 닫고 종료하는 것입니다.

애플리케이션 수준 샘플을 사용하기 전에 두 가지 사항을 준비합니다.

* 다운스트림 장치의 IoT Hub 연결 문자열은 게이트웨이 장치를 가리키도록 수정 되 고 다운스트림 장치를 IoT Hub에 인증 하는 데 필요한 모든 인증서를 지정 합니다. 자세한 내용은 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)을 참조 하세요.

* 다운스트림 디바이스의 어딘가에 복사 및 저장한 루트 CA 인증서에 대한 전체 경로.

    `<path>/azure-iot-test-only.root.ca.cert.pem`)을 입력합니다.

### <a name="nodejs"></a>NodeJs

이 섹션에서는 Azure IoT NodeJS 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 제공합니다. NodeJS 응용 프로그램의 경우 여기에 표시 된 것 처럼 응용 프로그램 수준에서 루트 CA 인증서를 설치 해야 합니다. NodeJS 응용 프로그램은 시스템의 인증서 저장소를 사용 하지 않습니다.

1. [Node.js용 Azure IoT 디바이스 SDK 샘플 리포지토리](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)에서 **edge_downstream_device.js**에 대한 샘플을 가져옵니다.
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다.
3. edge_downstream_device.js 파일에서 **connectionString** 및 **edge_ca_cert_path** 변수를 업데이트합니다.
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

실행 중인 샘플을 이해하기 위해 다음 코드 조각은 클라이언트 SDK가 인증서 파일을 읽고 보안 TLS 연결을 설정하는 데 사용하는 방법을 나타냅니다.

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

이 섹션에서는 Azure IoT .NET 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. 그러나 .NET 애플리케이션은 자동으로 Linux 및 Windows 호스트 모두에서 시스템의 인증서 스토리지에 설치된 모든 인증서를 사용할 수 있습니다.

1. [IoT Edge .NET 샘플 폴더](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)에서 **EdgeDownstreamDevice**에 대한 샘플을 가져옵니다.
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다.
3. **Properties / launchSettings.json** 파일에서 **DEVICE_CONNECTION_STRING** 및 **CA_CERTIFICATE_PATH** 변수를 업데이트합니다. 호스트 시스템에서 신뢰할 수 있는 인증서 스토리지에 설치된 인증서를 사용하려는 경우 이 변수를 비워 둡니다.
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

.NET 애플리케이션을 통해 인증서 스토리지에 프로그래밍 방식으로 신뢰할 수 있는 인증서를 설치하려면 **EdgeDownstreamDevice / Program.cs** 파일의 **InstallCACert()** 함수를 참조합니다. 이 작업은 멱등원(idempotent)이므로 추가 효과 없이 동일한 값을 사용하여 여러 번 실행될 수 있습니다.

### <a name="c"></a>C

이 섹션에서는 Azure IoT C 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. C SDK는 OpenSSL, WolfSSL 및 Schannel을 포함한 많은 TLS 라이브러리를 사용하여 작동할 수 있습니다. 자세한 내용은 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 참조하세요.

1. [C용 Azure IoT 디바이스 SDK 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)에서 **iotedge_downstream_device_sample** 애플리케이션을 가져옵니다.
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다.
3. iotedge_downstream_device_sample.c 파일에서 **connectionString** 및 **edge_ca_cert_path** 변수를 업데이트합니다.
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

C용 Azure IoT 디바이스 SDK는 클라이언트를 설정할 때 CA 인증서를 등록하는 옵션을 제공합니다. 이 작업은 장소에 상관 없이 인증서를 설치하지 않지만, 대신 메모리에서 인증서의 문자열 형식을 사용합니다. 저장된 인증서는 연결을 설정할 때 기본 TLS 스택에 제공됩니다.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Windows 호스트에서 OpenSSL 또는 다른 TLS 라이브러리를 사용하지 않는 경우 SDK는 기본적으로 Schannel를 사용합니다. Schannel이 작동하려면 IoT Edge 루트 CA 인증서를 `IoTHubDeviceClient_SetOption` 작업을 사용하여 설정하지 않고 Windows 인증서 스토리지에 설치해야 합니다.

### <a name="java"></a>Java

이 섹션에서는 Azure IoT Java 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다.

1. [Java용 Azure IoT 디바이스 SDK 샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)에서 **Send-event**에 대한 샘플을 가져옵니다.
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다.
3. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

### <a name="python"></a>Python

이 섹션에서는 Azure IoT Python 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다.

1. [Python 용 Azure IoT 장치 SDK 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios)의 **send_message** 에 대 한 샘플을 가져옵니다.
2. IoT Edge 컨테이너에서 실행 중인지 또는 디버그 시나리오에서 `EdgeHubConnectionString` 및 `EdgeModuleCACertificateFile` 환경 변수가 설정 되어 있는지 확인 합니다.
3. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

## <a name="test-the-gateway-connection"></a>게이트웨이 연결 테스트

다음 샘플 명령을 사용 하 여 다운스트림 장치를 게이트웨이 장치에 연결할 수 있는지 테스트 합니다.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

이 명령은 MQTTS (포트 8883)를 통한 연결을 테스트 합니다. 다른 프로토콜을 사용 하는 경우 필요에 따라 AMQPS (5671) 또는 HTTPS (433)에 대해 명령을 조정 합니다.

체인의 모든 인증서에 대 한 정보를 포함 하 여이 명령의 출력은 길어질 수 있습니다. 연결에 성공 하면 `Verification: OK` 또는 `Verify return code: 0 (ok)`와 같은 줄이 표시 됩니다.

![게이트웨이 연결 확인](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>게이트웨이 연결 문제 해결

리프 장치가 게이트웨이 장치에 간헐적으로 연결 되어 있는 경우 다음 단계를 수행 하 여 해결 합니다.

1. 연결 문자열의 게이트웨이 호스트 이름이 게이트웨이 장치에 있는 IoT Edge 구성. yaml 파일의 hostname 값과 동일 합니까?
2. 게이트웨이 호스트 이름을 IP 주소로 확인할 수 있나요? DNS를 사용 하거나 리프 장치에 호스트 파일 항목을 추가 하 여 간헐적 연결을 해결할 수 있습니다.
3. 통신 포트가 방화벽에서 열려 있나요? 사용 된 프로토콜 (MQTTS: 8883/AMQPS: 5671/HTTPS: 433)을 기반으로 하는 통신은 다운스트림 장치와 투명 IoT Edge 사이에서 가능 해야 합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge가 다운스트림 디바이스에 [오프라인 기능](offline-capabilities.md)을 확장할 수 있는 방법을 알아봅니다.

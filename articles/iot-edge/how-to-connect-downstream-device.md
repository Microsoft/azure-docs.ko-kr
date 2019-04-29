---
title: 다운스트림 디바이스 연결 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 게이트웨이 디바이스를 통해 연결하도록 다운스트림 또는 리프 디바이스를 구성하는 방법입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5a05b8f0f9484ea49fbfb0bbe8818aa9cd0d66ee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126429"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결

Azure IoT Edge를 사용하면 IoT Hub에 대한 연결을 유지하는 단일 게이트웨이 디바이스를 통해 하나 이상의 디바이스가 해당 메시지를 전달할 수 있는 투명 게이트웨이 시나리오를 구현할 수 있습니다. 게이트웨이 디바이스를 구성한 후에는 다운스트림 디바이스를 안전하게 연결하는 방법을 알고 있어야 합니다. 

이 문서에서는 다운스트림 디바이스 연결과 관련된 일반적인 문제를 식별하고, 다음을 통해 다운스트림 디바이스를 설정하는 방법을 안내합니다. 

* TLS(전송 계층 보안) 및 인증서 기본 사항을 설명합니다. 
* 다른 운영 체제에서 TLS 라이브러리가 작동하는 방식 및 각 운영 체제가 인증서를 사용하는 방식을 설명합니다.
* 시작하는 데 도움이 되도록 여러 언어로 Azure IoT 샘플을 설명합니다. 

이 문서에서 *게이트웨이* 및  *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 디바이스를 의미합니다. 

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 수행하려면 두 디바이스를 사용할 수 있어야 합니다.

1. 투명 게이트웨이로 설정된 IoT Edge 디바이스. 
    [IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)

    게이트웨이 디바이스를 구성한 후 게이트웨이에서 **azure-iot-test-only.root.ca.cert.pem** 인증서를 복사하여 다운스트림 디바이스에서 어디서나 사용할 수 있도록 합니다. 

2. IoT Hub에서 디바이스 ID가 있는 다운스트림 디바이스. 
    IoT Edge 디바이스는 다운스트림 디바이스로 사용할 수 없습니다. 대신, IoT Hub에서 일반적인 IoT 디바이스로 등록된 디바이스를 사용합니다. 포털에서 새 디바이스를 **IoT 디바이스** 섹션에 등록할 수 있습니다. 또는 Azure CLI를 사용하여 [디바이스를 등록](../iot-hub/quickstart-send-telemetry-c.md#register-a-device)할 수 있습니다. 연결 문자열을 복사하여 이후 섹션에서 사용할 수 있도록 합니다. 

    현재는 대칭 키 인증을 사용한 다운스트림 디바이스만 IoT Edge 게이트웨이를 통해 연결할 수 있습니다. X.509 인증 기관 및 자체 서명된 X.509 인증서는 현재 지원되지 않습니다.
    
> [!NOTE]
> 이 문서에서 사용 되는 "게이트웨이 이름" 동일 해야 합니다. IoT Edge config.yaml 파일의 호스트 이름으로 사용 되는 이름을 지정 합니다. 게이트웨이 이름이 DNS 또는 호스트 파일 항목을 사용 하거나 IP 주소를 확인할 수 있어야 합니다. 통신 프로토콜을 기반으로 사용 되는 (MQTTS:8883 / AMQPS:5671 / HTTPS:433) 다운스트림 장치에서 IoT Edge 투명 사이의 수 여야 합니다. 방화벽 사이 인 경우 해당 포트를 열어야 해야 합니다.

## <a name="prepare-a-downstream-device"></a>다운스트림 디바이스 준비

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 많은 경우에 이러한 애플리케이션은 [Azure IoT 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용합니다. 모든 실질적인 용도의 경우 다운스트림 디바이스는 IoT Edge 게이트웨이 디바이스 자체에서 실행 중인 애플리케이션일 수 있습니다. 

다운스트림 디바이스를 IoT Edge 게이트웨이에 연결하려면 다음 두 가지 항목이 필요합니다.

1. 게이트웨이에 연결하기 위한 정보가 추가된 IoT Hub 디바이스 연결 문자열로 구성된 디바이스 또는 애플리케이션. 

    연결 문자열의 형식은 `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`와 같습니다. 연결 문자열의 끝에 게이트웨이 디바이스의 호스트 이름을 사용하여 **GatewayHostName** 속성이 추가되었습니다. **GatewayHostName**의 값은 게이트웨이 디바이스의 config.yaml 파일에서 **hostname**의 값과 일치해야 합니다. 

    최종 문자열은 다음과 같습니다. `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`

2. 디바이스 또는 애플리케이션은 게이트웨이 디바이스에 대한 TLS 연결의 유효성을 검사하기 위해 **루트 CA** 또는 **소유자 CA** 인증서를 신뢰해야 합니다. 

    이 더 복잡한 단계는 이 문서의 나머지 부분에서 자세히 설명되어 있습니다. 이 단계는 운영 체제의 인증서 스토리지에 CA 인증서를 설치하거나 Azure IoT SDK를 사용하여 애플리케이션 내에서 인증서를 참조하는(특정 언어의 경우) 두 가지 방법 중 하나로 수행할 수 있습니다.

## <a name="tls-and-certificate-fundamentals"></a>TLS 및 인증서 기본 사항

IoT Edge에 다운스트림 디바이스를 안전하게 연결하는 문제는 인터넷을 통해 발생하는 다른 보안 클라이언트/서버 통신과 동일합니다. 클라이언트와 서버는 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용하여 인터넷을 통해 안전하게 통신합니다. TLS는 인증서라고 하는 표준 [PKI(공용 키 인프라)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 구문을 사용하여 빌드되었습니다. TLS는 상당히 복잡한 규격이며 두 개의 엔드포인트를 보호하는 것과 관련된 광범위한 주제를 다루지만 다음 섹션에서는 디바이스를 IoT Edge 게이트웨이에 안전하게 연결하는 데 필요한 사항을 간략히 설명합니다.

클라이언트를 서버에 연결할 때 서버에서는 *서버 인증서 체인*이라는 인증서의 체인을 제공합니다. 인증서 체인은 일반적으로 CA(루트 인증 기관) 인증서, 하나 이상의 중간 CA 인증서 및 마지막으로 자체 서버 인증서로 구성됩니다. 클라이언트는 전체 서버 인증서 체인을 암호화된 방식으로 확인하여 서버와의 신뢰를 설정합니다. 이 서버 인증서 체인의 클라이언트 유효성 검사를 *서버 인증*이라고 합니다. 서버 인증서 체인의 유효성을 검사하려면 서버 인증서를 만드는(또는 발행하는) 데 사용된 루트 CA 인증서의 복사본이 클라이언트에 있어야 합니다. 일반적으로 웹 사이트에 연결할 때 브라우저는 일반적으로 사용되는 CA 인증서로 미리 구성되므로 클라이언트는 원활히 프로세스됩니다. 

디바이스를 Azure IoT Hub에 연결할 때 디바이스는 클라이언트이며 IoT Hub 클라우드 서비스는 서버입니다. IoT Hub 클라우드 서비스는 공개적으로 널리 사용되는 **Baltimore CyberTrust Root**라는 루트 CA 인증서로 지원됩니다. 대부분의 디바이스에 IoT Hub CA 인증서가 이미 설치되어 있으므로 많은 TLS 구현(OpenSSL Schannel, LibreSSL)은 자동으로 서버 인증서 유효성 검사 동안에 해당 인증서를 사용합니다. IoT Hub에 성공적으로 연결할 수 있는 디바이스는 IoT Edge 게이트웨이에 연결을 시도하는 문제가 있을 수 있습니다.

디바이스를 IoT Edge 게이트웨이에 연결할 때 다운스트림 디바이스는 클라이언트이고 게이트웨이 디바이스는 서버입니다. 연산자(또는 사용자)는 Azure IoT Edge를 사용하여 적합하다고 판단되는 게이트웨이 인증서 체인을 빌드할 수 있습니다. 연산자는 Baltimore와 같은 공용 CA 인증서를 사용하거나 자체 서명된(또는 내부) 루트 CA 인증서를 사용하도록 선택할 수도 있습니다. 공용 CA 인증서는 종종 이와 관련된 비용이 있으므로 일반적으로 프로덕션 시나리오에서 사용됩니다. 자체 서명된 CA 인증서는 개발 및 테스트에 대해 기본 설정됩니다. 필수 구성 요소 섹션에 나열된 투명한 게이트웨이 설치 문서에서는 자체 서명된 루트 CA 인증서를 사용합니다. 

IoT Edge 게이트웨이에 대한 자체 서명된 루트 CA 인증서를 사용하는 경우에는 설치하거나 게이트웨이에 연결을 시도하는 모든 다운스트림 디바이스에 제공해야 합니다. 

IoT Edge 인증서 및 일부 프로덕션 의미에 대해 자세히 알아보려면 [IoT Edge 인증서 사용량 세부 정보](iot-edge-certs.md)를 참조하세요.

## <a name="install-certificates-using-the-os"></a>OS를 사용하여 인증서 설치

이 문서에서는 루트 CA 인증서를 *소유자 CA*라고 합니다. 해당 용어는 필수 게이트웨이 문서의 스크립트에서 사용되기 때문입니다. 

일반적으로 운영 체제의 인증서 스토리지에 소유자 CA 인증서를 설치하면 대부분의 애플리케이션에서 소유자 CA 인증서를 사용할 수 있습니다. OS 인증서 스토리지를 사용하지 않는 대신 노드 런타임의 내부 인증서 스토리지를 사용하는 NodeJS 애플리케이션과 같은 일부 예외가 있습니다. 운영 체제 수준에서 인증서를 설치할 수 없는 경우 Azure IoT SDK와 함께 인증서를 사용하려면 이 문서의 뒷부분에 나오는 언어별 예제를 참조하세요. 

### <a name="ubuntu"></a>Ubuntu

다음 명령은 Ubuntu 호스트에서 CA 인증서를 설치하는 방법의 예제입니다. 이 샘플은 필수 구성 요소 문서의 **azure-iot-test-only.root.ca.cert.pem** 인증서를 사용하고 다운스트림 디바이스에 있는 위치에 인증서를 복사한 것으로 가정합니다.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

다음과 같은 메시지가 표시됩니다. “/etc/ssl/certs에 인증서 업데이트 중... 1개 추가됨, 0개 제거됨. 완료."

### <a name="windows"></a>Windows

다음 단계는 Windows 호스트에 CA 인증서를 설치하는 방법의 예제입니다. 이 샘플은 필수 구성 요소 문서의 **azure-iot-test-only.root.ca.cert.pem** 인증서를 사용하고 다운스트림 디바이스에 있는 위치에 인증서를 복사한 것으로 가정합니다.  

1. 시작 표시줄에서 **컴퓨터 인증서 관리**를 검색하고 선택합니다. **certlm**이라는 유틸리티가 열립니다.
2. **인증서 - 로컬 컴퓨터** > **신뢰할 수 있는 루트 인증 기관**으로 이동합니다.
3. **인증서**를 마우스 오른쪽 단추로 클릭하고 **모든 태스크** > **가져오기**를 선택합니다. 인증서 가져오기 마법사가 열립니다. 
4. 지시에 따라 단계를 수행하고 인증서 파일(`<path>/azure-iot-test-only.root.ca.cert.pem`)을 가져옵니다. 완료되면 "가져오기에 성공했습니다." 메시지가 표시됩니다. 

또한 이 문서의 뒷부분에 나오는 .NET 샘플에 표시된 대로 .NET API를 사용하여 프로그래밍 방식으로 인증서를 설치할 수 있습니다. 

일반적으로 애플리케이션은 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel)이라는 TLS 스택이 제공된 Windows를 사용하여 TLS를 통해 안전하게 연결합니다. TLS 연결 설정을 시도하려면 Windows 인증서 스토리지에 설치된 인증서가 Schannel에 *필요합니다*.

## <a name="use-certificates-with-azure-iot-sdks"></a>Azure IoT SDK를 사용하여 인증서 사용

이 문서에서는 루트 CA 인증서를 *소유자 CA*라고 합니다. 해당 용어는 필수 구성 요소 문서에서 자체 서명된 인증서를 생성하는 스크립트에서 사용되기 때문입니다. 

이 섹션에서는 Azure IoT SDK를 간단한 샘플 애플리케이션을 사용하여 IoT Edge 디바이스에 연결하는 방법을 설명합니다. 모든 샘플의 목표는 디바이스 클라이언트를 연결하고 게이트웨이에 원격 분석 메시지를 보낸 다음, 연결을 닫고 종료하는 것입니다. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>모든 Azure IoT SDK의 일반적인 개념

애플리케이션 수준 샘플을 사용하기 전에 두 가지 사항을 준비합니다.

1. 게이트웨이 디바이스를 가리키도록 수정된 다운스트림 디바이스의 IoT Hub 연결 문자열.

    연결 문자열의 형식은 `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`와 같습니다. 연결 문자열의 끝에 게이트웨이 디바이스의 호스트 이름을 사용하여 **GatewayHostName** 속성이 추가되었습니다. **GatewayHostName**의 값은 게이트웨이 디바이스의 config.yaml 파일에서 **hostname**의 값과 일치해야 합니다. 

    최종 문자열은 다음과 같습니다. `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`

2. 다운스트림 디바이스의 어딘가에 복사 및 저장한 루트 CA 인증서에 대한 전체 경로.

    예: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

이 섹션에서는 Azure IoT NodeJS 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 제공합니다. Linux 및 Windows 호스트의 경우 NodeJS 애플리케이션이 시스템의 인증서 스토리지를 사용하지 않으므로 다음과 같이 애플리케이션 수준에서 루트 CA 인증서를 설치해야 합니다. 

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

### <a name="java"></a>자바

이 섹션에서는 Azure IoT Java 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. 

1. [Java용 Azure IoT 디바이스 SDK 샘플](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)에서 **Send-event**에 대한 샘플을 가져옵니다. 
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다. 
3. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요.

### <a name="python"></a>Python

이 섹션에서는 Azure IoT Python 디바이스 클라이언트를 IoT Edge 게이트웨이에 연결하기 위한 샘플 애플리케이션을 소개합니다. 

1. [Python용 Azure IoT 디바이스 SDK 샘플](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples)에서 **edge_downstream_client**에 대한 샘플을 가져옵니다. 
2. **readme.md** 파일을 검토하여 샘플을 실행하기 위한 모든 필수 구성 요소가 있는지 확인합니다. 
3. edge_downstream_client.py 파일에서 **CONNECTION_STRING** 및 **TRUSTED_ROOT_CA_CERTIFICATE_PATH** 변수를 업데이트합니다. 
4. 디바이스에서 샘플을 실행하는 방법에 대한 지침은 SDK 설명서를 참조하세요. 


## <a name="test-the-gateway-connection"></a>게이트웨이 연결 테스트

모든 항목이 정확하게 설정되었는지 테스트하는 샘플 명령은 다음과 같습니다. “확인된 OK”라는 메시지가 표시됩니다.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>게이트웨이 연결 문제 해결

리프 장치에는 게이트웨이 장치에 간헐적으로 연결 하는 경우 확인을 위해 다음 단계를 시도 합니다. 

1. 게이트웨이 장치에서 IoT Edge config.yaml 파일의 호스트 이름으로 동일한 연결을 추가할 게이트웨이 이름 문자열은?
2. 게이트웨이 이름을 IP 주소로 확인할 수 있는 기능 Intenmittent 연결 또는 DNS를 사용 하 여 리프 장치에서 호스트 파일 항목을 추가 하 여 해결할 수 있습니다.
3. 통신 포트를 방화벽에서 열려 있습니다? 통신 프로토콜을 기반으로 사용 되는 (MQTTS:8883 / AMQPS:5671 / HTTPS:433) 다운스트림 장치에서 IoT Edge 투명 사이의 수 여야 합니다.

## <a name="next-steps"></a>다음 단계

IoT Edge가 다운스트림 디바이스에 [오프라인 기능](offline-capabilities.md)을 확장할 수 있는 방법을 알아봅니다. 

---
title: Azure IoT Central에서 디바이스 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9e1e85d1ab1c5e7ce0cbd96c64137309c2e2916a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60887488"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central의 디바이스 연결 | Microsoft Docs

이 문서에서는 Microsoft Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.

Azure IoT Central을 사용 합니다 [Azure IoT Hub Device Provisioning service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) 모든 장치 등록 및 연결 관리 합니다.

DPS를 사용 하 여 사용 하도록 설정 합니다.

- IoT Central 온 보 딩 및 크기 조정 시 연결 장치를 지원 하도록 합니다.
- 장치를 생성할 수 자격 증명 및 IoT Central UI 통해 장치를 등록 하지 않고 오프 라인으로 장치를 구성 합니다.
- 장치를 사용 하 여 연결 하려면 공유 액세스 서명 (SAS).
- 업계 표준 X.509 인증서를 사용 하 여 연결할 장치.
- 고유한 장치 Id를 사용 하 여 IoT Central의 장치를 등록할 수 있습니다. 고유한 장치 Id를 사용 하 여 기존 백 오피스 시스템과 통합을 간소화 합니다.
- IoT Central에 장치를 연결 하는 단일 하 고 일관 된 방법입니다.

이 문서에서는 다음 네 가지 사용 사례를 설명합니다.

1. [SAS를 사용 하 여 단일 장치를 신속 하 게 연결](#connect-a-single-device)
1. [SAS를 사용 하 여 대규모로 장치 연결](#connect-devices-at-scale-using-sas)
1. [X.509 인증서를 사용 하 여 대규모로 장치 연결](#connect-devices-using-x509-certificates) 프로덕션 환경에 권장 되는 방법입니다.
1. [먼저 디바이스를 등록하지 않고 연결](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>단일 디바이스 연결

이 방법은 IoT Central을 사용 하 여 실험 하거나 장치를 테스트 하는 경우에 유용 합니다. 장치에 대 한 연결 문자열을 생성 하려면 IoT Central 응용 프로그램에서 장치 연결 정보를 사용할 수 있습니다. 자세한 단계를 참조 하세요 [Azure IoT Central 응용 프로그램에 연결할 장치 연결 문자열을 생성 하는 방법을](howto-generate-connection-string.md)합니다.

## <a name="connect-devices-at-scale-using-sas"></a>SAS를 사용 하 여 대규모로 장치 연결

에 연결 하려면 장치 IoT Central SAS를 사용 하 여 대규모로 등록 하 고 장치를 설정 해야 합니다.

### <a name="register-devices-in-bulk"></a>장치 대량 등록

IoT Central 응용 프로그램을 사용 하 여 많은 수의 장치를 등록 하려면 CSV 파일을 사용 하 여 [장치 Id 및 장치 이름을 가져오려면](howto-manage-devices.md#import-devices)합니다.

가져온된 장치에 대 한 연결 정보를 검색할 [IoT Central 응용 프로그램에서 CSV 파일을 내보낼](howto-manage-devices.md#export-devices)합니다.

> [!NOTE]
> 첫 번째 IoT Central에 등록 하지 않고 장치를 연결 하는 방법을 참조 하세요 [첫 번째 장치 등록 없이 연결](#connect-without-registering-devices)합니다.

### <a name="set-up-your-devices"></a>장치 설정

장치를 연결 하 고 IoT Central 응용 프로그램에 IoT에 데이터를 보낼 수 있도록 장치 코드에 내보내기 파일에서 연결 정보를 사용 합니다. 장치를 연결 하는 방법에 대 한 자세한 내용은 참조 하세요. [다음 단계](#next-steps)합니다.

## <a name="connect-devices-using-x509-certificates"></a>X.509 인증서를 사용 하 여 장치를 연결 합니다.

프로덕션 환경에서 X.509 인증서를 사용 하는 IoT Central에 대 한 권장 되는 장치 인증 메커니즘입니다. 자세한 내용은 [X.509 CA 인증서를 사용하여 디바이스 인증](../iot-hub/iot-hub-x509ca-overview.md)을 참조하세요.

다음 단계를 X.509 인증서를 사용 하 여 IoT Central에 장치를 연결 하는 방법에 설명 합니다.

1. IoT Central 응용 프로그램에서 _추가 및 확인 된 중간 또는 루트 X.509 인증서_ 장치 인증서를 생성 하는 데 사용할:

    - 이동할 **관리 > 장치 연결 > 인증서 (X.509)** X.509 루트 또는 리프 장치 인증서를 생성 하는 데 사용할 중간 인증서를 추가 합니다.

      ![연결 설정](media/concepts-connectivity/connection-settings.png)

      보안 위반 했거나 기본 인증서 만료 되도록 설정 된 경우 가동 중지 시간을 줄이기 위해 보조 인증서를 사용 합니다. 기본 인증서를 업데이트 하는 동안 보조 인증서를 사용 하 여 장치를 프로 비전 할 수 있습니다.

    - 인증서 소유권을 확인 인증서의 업 로더가 인증서의 개인 키에 있는지 확인 합니다. 인증서를 확인 합니다.
        - 다음 단추를 선택 **확인 코드** 코드를 생성할 수 있습니다.
        - 이전 단계에서 생성 되는 확인 코드를 사용 하 여 X.509 확인 인증서를 만듭니다. 인증서를.cer 파일로 저장 합니다.
        - 서명된 된 인증서를 업로드 하 고 선택 **확인**합니다.

          ![연결 설정](media/concepts-connectivity/verify-cert.png)

1. CSV 파일을 사용 하 여 _가져오기 및 장치를 등록할_ IoT Central 응용 프로그램에서 합니다.

1. _장치를 설정 합니다._ 업로드된 루트 인증서를 사용하여 리프 인증서를 생성합니다. 사용 된 **장치 ID** 리프 인증서의 CNAME 값입니다. 장치 ID에는 모두 소문자 여야 합니다. 그런 다음 프로 비전 서비스 정보를 사용 하 여 장치를 프로그래밍 합니다. 장치가 켜져 있는 첫 번째 경우에서 DPS IoT Central 응용 프로그램에 대 한 해당 연결 정보를 검색 합니다.

### <a name="further-reference"></a>추가 참조

- [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases)에 대한 샘플 구현.

- [C의 샘플 디바이스 클라이언트.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>테스트 목적 으로만

만 테스트에 대 한 CA 인증서 및 장치 인증서를 생성 하려면 이러한 유틸리티를 사용할 수 있습니다.

- DevKit 장치를 사용 하는 경우이 [명령줄 도구](https://aka.ms/iotcentral-docs-dicetool) 인증서를 확인 하려면 IoT Central 응용 프로그램에 추가할 수 있습니다 CA 인증서를 생성 합니다.

- 이 사용 하 여 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) 에:
  - 인증서 체인을 만듭니다. GitHub 문서에서 2 단계를 수행 합니다.
  - IoT Central 응용 프로그램에 업로드할.cer 파일로 인증서를 저장 합니다.
  - IoT Central 응용 프로그램에서 확인 코드를 사용 하 여 확인 인증서를 생성 합니다. GitHub 문서에서 3 단계를 수행 합니다.
  - 도구에 대 한 매개 변수로 장치의 Id를 사용 하 여 장치에 대 한 리프 인증서를 만듭니다. GitHub 문서에 4 단계를 수행 합니다.

## <a name="connect-without-registering-devices"></a>장치를 등록 하지 않고 연결

IoT Central 사용 하도록 설정 하는 주요 시나리오는 대용량 없이 첫 번째 IoT Central 응용 프로그램에 연결할 수 있는 장치를 제조 하는 Oem을 위한 등록 합니다. 제조업체는 적절 한 자격 증명을 생성 하 고 공장에서 장치를 구성 해야 합니다. 처음으로 장치 설정에서 하는 경우 IoT Central 응용 프로그램에 자동으로 연결 합니다. IoT Central 운영자는 상태 데이터를 전송 하려면 장치를 승인 해야 합니다.

다음 다이어그램은이 흐름을 설명합니다.

![연결 설정](media/concepts-connectivity/device-connection-flow.png)

다음 단계는이 프로세스를 자세히 설명 합니다. 단계는 장치 인증에 대 한 SAS 또는 X.509 인증서의 사용 여부에 따라 약간 다릅니다.

1. 연결 설정을 구성 합니다.

    - **X.509 인증서의 경우:** [추가 하 고 루트/중간 인증서를 확인 하기](#connect-devices-using-x509-certificates) 다음 단계에서 장치 인증서를 생성 하는 데 사용 합니다.
    - **SAS:** 기본 키를 복사 합니다. 이 키는 IoT Central 응용 프로그램에 대 한 그룹 SAS 키가입니다. 다음 단계에서는 장치 SAS 키를 생성 하는 키를 사용 합니다.
    ![연결 설정 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. 장치 자격 증명 생성
    - **X.509 인증서:** 루트 또는 IoT Central 응용 프로그램에 추가 하는 중간 인증서를 사용 하 여 장치에 대 한 리프 인증서를 생성 합니다. 소문자를 사용 해야 **장치 ID** 리프 인증서의 CNAME로 합니다. 이 테스트용 으로만 사용 [명령줄 도구](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) 장치 인증서를 생성 합니다.
    - **SAS:** 이 사용 하 여 [명령줄 도구](https://www.npmjs.com/package/dps-keygen) 장치 SAS 키를 생성 합니다. 그룹을 사용 하 여 **기본 키** 이전 단계에서 합니다. 장치 ID는 소문자 여야 합니다.

      설치 하는 [생성기 유틸리티 키](https://github.com/Azure/dps-keygen), 다음 명령을 실행:

      ```cmd/sh
      npm i -g dps-keygen
      ```

      그룹 SAS 키에서 장치 키를 생성 하려면 다음 명령을 실행 합니다.

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. 장치를 설정 하려면 각 장치를 플래시를 **범위 ID**, **장치 ID**, 및 **X.509 장치 인증서** 하거나 **SAS 키**합니다.

1. 그런 다음 IoT Central 응용 프로그램에 연결 하려면 장치를 켭니다. 장치에서 전환 하는 경우 먼저 해당 IoT Central 등록 정보를 검색 하는 DPS에 연결 합니다.

1. 연결된 된 장치가 처음으로 표시 된 **연결 되지 않은 장치** 에 **Device Explorer** 페이지. 디바이스의 프로비전 상태는 **등록**됩니다. **연결** 적절 한 장치 템플릿에 대 한 장치 및 IoT Central 응용 프로그램에 연결 하는 장치를 승인 합니다. 그런 다음 장치에서 IoT Hub 연결 문자열을 검색 하 고 데이터를 보내기 시작 수 있습니다. 완료 되었습니다 장치 프로 비전 및 프로 비전 상태가 이제 **프로 비전 됨**합니다.

## <a name="provisioning-status"></a>프로비전 상태

실제 장치에 연결 될 때 IoT Central 응용 프로그램을 프로 비전 상태는 변경 다음과 같습니다.

1. 장치 프로 비전 상태는 첫 번째 **Registered**합니다. 이 상태 의미 장치 IoT Central에서 만든 장치 ID가 있습니다. 장치가 등록 되는 경우:
    - 새 실제 장치에 추가 합니다 **Device Explorer** 페이지입니다.
    - 사용 하 여 장치 집합에 추가 됩니다 **가져오기** 에 **Device Explorer** 페이지입니다.
    - 장치에 수동으로 등록 되지 않은 합니다 **Device Explorer** 하지만 유효한 자격 증명을 사용 하 여 연결 된 페이지 및으로 표시 됩니다는 **Unassociated** 장치에는 **Device Explorer**페이지.

1. 장치 프로 비전 상태를 변경 **프로 비전 됨** 유효한 자격 증명을 사용 하 여 IoT Central 응용 프로그램에 연결 하는 장치 프로 비전 단계를 완료 하는 경우. 이 단계에서는 장치에서 IoT Hub 연결 문자열을 검색 합니다. 이제 장치가 IoT Hub에 연결 하 고 데이터를 보내기 시작 수 있습니다.

1. 운영자는 장치를 차단할 수 있습니다. 장치가 차단 되 면 IoT Central 응용 프로그램에 데이터를 보낼 수 없습니다 것입니다. 차단 된 장치에 프로 비전 상태가 **차단 됨**합니다. 데이터 전송을 다시 시작할 수 전에 운영자 장치를 다시 설정 해야 합니다. 운영자를 프로 비전 상태를 이전 값을 반환 하는 장치를 차단 해제 하는 경우 **Registered** 또는 **프로 비전 됨**합니다.

## <a name="sdk-support"></a>SDK 지원

장치 코드를 구현 하는 수는 가장 쉬운 방법은 Azure Device Sdk 제품입니다. 현재 다음과 같은 디바이스 SDK를 사용할 수 있습니다.

- [C용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

각 디바이스는 디바이스를 식별하는 고유한 연결 문자열을 사용하여 연결합니다. 장치가 등록 되어 있는 IoT hub에 연결할 수 있습니다. Azure IoT Central 응용 프로그램에서 실제 장치를 만들면 응용 프로그램을 사용 하 여 연결 문자열을 생성 하는 데 필요한 정보를 생성 하는 `dps-keygen`합니다.

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 기능 및 IoT Hub 연결

IoT Hub와의 모든 디바이스 통신에 다음 IoT Hub 연결 옵션이 사용됩니다.

- [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md)

다음 표에는 Azure IoT Central 디바이스 기능이 IoT Hub 기능에 매핑되는 방식이 요약되어 있습니다.

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 측정: 원격 분석 | 디바이스-클라우드 메시징 |
| 디바이스 속성 | 디바이스 쌍 reported 속성 |
| 설정 | 디바이스 쌍 desired 및 reported 속성 |

디바이스 SDK 사용에 대해 자세히 알아보려면 다음 문서 중 하나에서 코드 예제를 참조하세요.

- [Azure IoT Central 애플리케이션에 일반 Node.js 클라이언트 연결](howto-connect-nodejs.md)
- [Azure IoT Central 애플리케이션에 Raspberry Pi 장치 연결](howto-connect-raspberry-pi-python.md)
- [Azure IoT Central 애플리케이션에 DevDiv 키트 장치 연결](howto-connect-devkit.md)

### <a name="protocols"></a>프로토콜

디바이스 SDK는 IoT Hub에 연결하기 위한 다음과 같은 네트워크 프로토콜을 지원합니다.

- MQTT
- AMQP
- HTTPS

프로토콜 간 차이점 및 프로토콜 선택 방법에 대한 지침은 [통신 프로토콜 선택](../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요.

지원되는 프로토콜을 디바이스에서 사용할 수 없는 경우 Azure IoT Edge를 사용하여 프로토콜을 변환하면 됩니다. IoT Edge는 Azure IoT Central 애플리케이션의 에지로 프로세싱을 오프로드하는 에지의 인텔리전스 시나리오를 지원합니다.

## <a name="security"></a>보안

디바이스와 Azure IoT Central 간에 교환되는 모든 데이터는 암호화됩니다. IoT Hub는 디바이스 연결 IoT Hub 엔드포인트 중 하나에 연결하는 디바이스의 모든 요청을 인증합니다. 유선으로 자격 증명을 교환하지 못하도록 방지하기 위해 디바이스에서는 서명된 토큰을 사용하여 인증합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](../iot-hub/iot-hub-devguide-security.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure IoT Central의 장치 연결에 대 한 알아보았습니다 했으므로 다음 제안된 단계는 다음과 같습니다.

- [DevKit 디바이스 준비 및 연결](howto-connect-devkit.md)
- [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md)
- [Azure IoT Central 애플리케이션에 일반 Node.js 클라이언트 연결](howto-connect-nodejs.md)
- [C SDK: 장치 클라이언트 SDK를 프로 비전](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

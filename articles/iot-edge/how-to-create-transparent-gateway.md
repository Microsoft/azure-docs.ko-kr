---
title: 투명한 게이트웨이 디바이스 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 디바이스를 다운스트림 디바이스의 정보를 처리할 수 있는 투명한 게이트웨이로 사용
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: cf7147ca1295c9f2cef5d89c232f2c266075e362
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167405"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성

이 문서에서는 다른 장치에서 IoT Hub와 통신할 수 있도록 투명 한 게이트웨이로 작동 하도록 IoT Edge 장치를 구성 하는 방법에 대 한 자세한 지침을 제공 합니다. 이 문서에서는 *IoT Edge 게이트웨이* 라는 용어를 사용 하 여 투명 게이트웨이로 구성 된 IoT Edge 장치를 참조 합니다. 자세한 내용은 [IoT Edge 장치를 게이트웨이로 사용 하는 방법](./iot-edge-as-gateway.md)을 참조 하세요.

>[!NOTE]
>현재 상황:
>
> * Edge 가능 디바이스는 IoT Edge 게이트웨이에 연결할 수 없습니다.
> * 다운스트림 디바이스는 파일 업로드를 사용할 수 없습니다.

성공적인 투명 게이트웨이 연결을 설정하기 위한 세 가지 일반적인 단계가 있습니다. 이 문서에서는 첫 번째 단계에 대해 설명 합니다.

1. **다운스트림 장치를 안전 하 게 연결할 수 있도록 게이트웨이 장치를 서버로 구성 합니다. 다운스트림 장치에서 메시지를 수신 하 고 적절 한 대상으로 라우팅하는 게이트웨이를 설정 합니다.**
2. IoT Hub를 사용 하 여 인증할 수 있도록 다운스트림 장치에 대 한 장치 id를 만듭니다. 게이트웨이 장치를 통해 메시지를 보내도록 다운스트림 장치를 구성 합니다. 자세한 내용은 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)을 참조 하세요.
3. 다운스트림 장치를 게이트웨이 장치에 연결 하 고 메시지 보내기를 시작 합니다. 자세한 내용은 [다운스트림 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-device.md)을 참조하세요.

장치가 게이트웨이로 작동 하려면 다운스트림 장치에 안전 하 게 연결 해야 합니다. Azure IoT Edge를 사용하면 PKI(공개 키 인프라)를 사용하여 이러한 디바이스 간에 안전한 연결을 설정할 수 있습니다. 이 경우 다운스트림 장치는 투명 게이트웨이 역할을 하는 IoT Edge 장치에 연결할 수 있습니다. 적절 한 보안을 유지 하기 위해 다운스트림 장치는 게이트웨이 장치의 id를 확인 해야 합니다. 이 id 검사는 장치에서 잠재적으로 악성 게이트웨이에 연결 하는 것을 방지 합니다.

다운스트림 디바이스는 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) 클라우드 서비스를 사용하여 생성된 ID가 있는 애플리케이션 또는 플랫폼이 될 수 있습니다. 이러한 응용 프로그램은 종종 [Azure IoT 장치 SDK](../iot-hub/iot-hub-devguide-sdks.md)를 사용 합니다. 다운스트림 장치는 IoT Edge 게이트웨이 장치 자체에서 실행 되는 응용 프로그램 일 수도 있습니다. 그러나 IoT Edge 장치는 IoT Edge 게이트웨이의 다운스트림 일 수 없습니다.

디바이스-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 IoT Hub에서 [X.509 ca 보안](../iot-hub/iot-hub-x509ca-overview.md) 을 사용 하도록 설정 하는 데 사용 하는 것과 동일한 인증서 설정을 가정 합니다. 여기에는 특정 iot Hub (iot HUB 루트 CA)와 연결 된 x.509 ca 인증서,이 ca로 서명 된 일련의 인증서 및 IOT EDGE 장치용 ca가 포함 됩니다.

>[!NOTE]
>이러한 문서 전체에서 사용 되는 *루트 CA 인증서* 라는 용어는 게시 된 인증 기관의 인증서 루트가 아닌 PKI 인증서 체인의 최상위 인증 기관 공용 인증서를 나타냅니다. 대부분의 경우에는 실제로 중간 CA 공용 인증서입니다.

다음 단계는 인증서를 만들어 게이트웨이의 올바른 위치에 설치 하는 과정을 안내 합니다. 머신을 사용하여 인증서를 생성한 다음, IoT Edge 디바이스로 복사할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

IoT Edge 설치 된 Linux 또는 Windows 장치입니다.

## <a name="set-up-the-device-ca-certificate"></a>장치 CA 인증서 설정

모든 IoT Edge 게이트웨이에는 장치 CA 인증서가 설치 되어 있어야 합니다. IoT Edge 보안 디먼은 IoT Edge 장치 CA 인증서를 사용 하 여 워크 로드 CA 인증서에 서명 합니다 .이 인증서는 IoT Edge 허브에 대 한 서버 인증서를 서명 합니다. 게이트웨이는 연결을 시작 하는 동안 다운스트림 장치에 해당 서버 인증서를 제공 합니다. 다운스트림 장치에서 서버 인증서가 루트 CA 인증서로 롤업되는 인증서 체인의 일부 인지 확인 합니다. 이 프로세스를 통해 다운스트림 장치는 게이트웨이가 신뢰할 수 있는 소스에서 온 것임을 확인할 수 있습니다. 자세한 내용은 [Azure IoT Edge에서 인증서를 사용 하는 방법 이해](iot-edge-certs.md)를 참조 하세요.

![게이트웨이 인증서 설정](./media/how-to-create-transparent-gateway/gateway-setup.png)

루트 CA 인증서와 장치 CA 인증서 (개인 키 포함)는 IoT Edge 게이트웨이 장치에 있어야 하 고 IoT Edge 구성. yaml 파일에서 구성 해야 합니다. 이 경우 *루트 CA 인증서* 는이 IoT Edge 시나리오에 대 한 최상위 인증 기관을 의미 합니다. 게이트웨이 장치 CA 인증서와 다운스트림 장치 인증서가 동일한 루트 CA 인증서로 롤업 되어야 합니다.

>[!TIP]
>IoT Edge 장치에 루트 CA 인증서 및 장치 CA 인증서를 설치 하는 프로세스는 [IoT Edge 장치에서 인증서 관리](how-to-manage-device-certificates.md)에 자세히 설명 되어 있습니다.

다음 파일을 준비 해야 합니다.

* 루트 CA 인증서
* 디바이스 CA 인증서
* 장치 CA 개인 키

프로덕션 시나리오의 경우 사용자 고유의 인증 기관으로 이러한 파일을 생성 해야 합니다. 개발 및 테스트 시나리오의 경우 데모 인증서를 사용할 수 있습니다.

1. 데모 인증서를 사용 하는 경우 다음 단계 집합을 사용 하 여 파일을 만듭니다.
   1. [루트 CA 인증서를 만듭니다](how-to-create-test-certificates.md#create-root-ca-certificate). 이러한 지침이 끝날 때 루트 CA 인증서 파일을 갖게 됩니다.
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [IoT Edge 장치 CA 인증서를 만듭니다](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates). 이러한 지침이 끝나면 장치 CA 인증서와 개인 키 라는 두 개의 파일이 있습니다.
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` 또는
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. 이러한 파일을 다른 컴퓨터에 만든 경우에는 IoT Edge 장치에 복사 합니다.

3. IoT Edge 장치에서 보안 디먼 구성 파일을 엽니다.
   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

4. 파일의 **인증서** 섹션을 찾고 다음 속성에 대 한 값으로 세 파일에 파일 uri를 제공 합니다.
   * **device_ca_cert**: 장치 ca 인증서
   * **device_ca_pk**: 장치 ca 개인 키
   * **trusted_ca_certs**: 루트 ca 인증서

5. 파일을 저장한 후 닫습니다.

6. IoT Edge를 다시 시작 합니다.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>EdgeHub 배포 및 메시지 라우팅

다운스트림 장치는 게이트웨이 장치에 원격 분석 및 메시지를 전송 합니다. 여기에서 IoT Edge 허브 모듈은 정보를 다른 모듈로 라우팅 하거나 IoT Hub 합니다. 이 기능에 대 한 게이트웨이 장치를 준비 하려면 다음을 확인 합니다.

* IoT Edge 허브 모듈이 장치에 배포 됩니다.

  장치에 IoT Edge를 처음 설치 하는 경우에는 IoT Edge 에이전트와 같은 한 시스템 모듈만 자동으로 시작 됩니다. 장치에 대 한 첫 번째 배포를 만든 후에는 두 번째 시스템 모듈인 IoT Edge 허브가 시작 됩니다. **EdgeHub** 모듈이 장치에서 실행 되 고 있지 않은 경우 장치에 대 한 배포를 만듭니다.

* IoT Edge 허브 모듈은 다운스트림 장치에서 들어오는 메시지를 처리 하도록 설정 된 경로를 포함 합니다.

  게이트웨이 장치는 다운스트림 장치에서 메시지를 처리할 수 있는 경로를가지고 있어야 합니다. 그렇지 않으면 해당 메시지가 처리 되지 않습니다. 게이트웨이 장치의 모듈에 메시지를 보내거나 IoT Hub에 직접 보낼 수 있습니다.

IoT Edge 허브 모듈을 배포 하 고 경로를 사용 하 여 다운스트림 장치에서 들어오는 메시지를 처리 하도록 구성 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **IoT Edge**로 이동하고 게이트웨이로 사용할 IoT Edge 디바이스를 선택합니다.

3. **모듈 설정**을 선택 합니다.

4. **모듈** 페이지에서 게이트웨이 장치에 배포 하려는 모듈을 추가할 수 있습니다. 이 문서에서는이 페이지에서 명시적으로 설정 하지 않아도 되는 edgeHub 모듈을 구성 하 고 배포 하는 방법을 집중적으로 설명 합니다.

5. **다음: 경로**를 선택 합니다.

6. **경로** 페이지에서 다운스트림 장치에서 오는 메시지를 처리할 경로가 있는지 확인 합니다. 다음은 그 예입니다. 

   * 모듈이 나 다운스트림 장치에서 IoT Hub 하 여 모든 메시지를 보내는 경로입니다.
       * **이름**: `allMessagesToHub`
       * **값**: `FROM /messages/* INTO $upstream`

   * 모든 다운스트림 장치의 모든 메시지를 IoT Hub 전송 하는 경로입니다.
      * **이름**: `allDownstreamToHub`
      * **값**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      이 경로는 IoT Edge 모듈의 메시지와는 달리 다운스트림 장치의 메시지에는 연결 된 모듈 ID가 없기 때문에 작동 합니다. 경로의 **WHERE** 절을 사용 하면 해당 시스템 속성을 사용 하 여 메시지를 필터링 할 수 있습니다.

      메시지 라우팅에 대한 자세한 내용은 [모듈 배포 및 경로 설정](./module-composition.md#declare-routes)을 참조하세요.

7. 경로 또는 경로를 만든 후 **검토 + 만들기**를 선택 합니다.

8. **검토 + 만들기** 페이지에서 **만들기**를 선택 합니다.

## <a name="open-ports-on-gateway-device"></a>게이트웨이 장치에서 포트 열기

IoT Hub와의 모든 통신은 아웃 바운드 연결을 통해 수행 되므로 표준 IoT Edge 장치는 작동 하기 위해 인바운드 연결이 필요 하지 않습니다. 게이트웨이 장치는 다운스트림 장치에서 메시지를 수신 해야 하기 때문에 다릅니다. 다운스트림 장치와 게이트웨이 장치 사이에 방화벽이 있는 경우 방화벽을 통해 통신도 가능 해야 합니다.

게이트웨이 시나리오가 작동 하려면 다운스트림 장치에서 인바운드 트래픽에 대해 IoT Edge 허브의 지원 되는 프로토콜 중 하나 이상을 열어야 합니다. 지원 되는 프로토콜은 MQTT, AMQP, HTTPS, Websocket을 통한 MQTT, Websocket을 통한 AMQP입니다.

| 포트 | 프로토콜 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="enable-extended-offline-operation"></a>확장 된 오프 라인 작업 사용

IoT Edge 런타임의 [1.0.4 릴리스부터](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) , 연결 된 게이트웨이 장치와 다운스트림 장치를 확장 된 오프 라인 작업에 대해 구성할 수 있습니다.

이 기능을 사용 하면 로컬 모듈 또는 다운스트림 장치에서 필요에 따라 IoT Edge 장치를 다시 인증 하 고 IoT hub와의 연결을 끊은 경우에도 메시지 및 메서드를 사용 하 여 서로 통신할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스, 모듈 및 하위 디바이스용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

확장 된 오프 라인 기능을 사용 하도록 설정 하려면 연결 하는 IoT Edge 게이트웨이 장치와 다운스트림 장치 간에 부모-자식 관계를 설정 합니다. 이러한 단계는이 시리즈의 다음 문서에서 [Azure IoT Hub에 대 한 다운스트림 장치 인증](how-to-authenticate-downstream-device.md)에 자세히 설명 되어 있습니다.

## <a name="next-steps"></a>다음 단계

이제 투명 게이트웨이로 설정 된 IoT Edge 장치가 있으므로 게이트웨이를 신뢰 하 고 메시지를 보내도록 다운스트림 장치를 구성 해야 합니다. 투명 게이트웨이 시나리오를 설정 하는 다음 단계에 대 한 [Azure IoT Hub 위해 다운스트림 장치를 인증](how-to-authenticate-downstream-device.md) 하려면 계속 진행 합니다.

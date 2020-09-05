---
title: 디바이스 보안에 대한 인증서 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge는 인증서를 사용하여 디바이스, 모듈 및 리프 디바이스의 유효성을 검사하고 이들 간의 보안 연결을 설정합니다.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: 9d7caf332239d364b5bc47b5d58a808ead70395d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210584"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Azure IoT Edge에서 인증서를 사용하는 방법 이해

IoT Edge 인증서는 모듈 및 다운스트림 IoT 디바이스에서 [IoT Edge 허브](iot-edge-runtime.md#iot-edge-hub) 런타임 모듈의 ID와 적법성을 확인하는 데 사용됩니다. 이러한 유효성 검사를 통해 런타임, 모듈 및 IoT 디바이스 간의 TLS(전송 계층 보안) 보안 연결을 사용하도록 설정합니다. IoT Hub 자체와 마찬가지로 IoT Edge에는 IoT 다운스트림(또는 리프) 디바이스 및 IoT Edge 모듈의 보안 및 암호화된 연결이 필요합니다. 보안 TLS 연결을 설정하기 위해 Edge Hub 모듈은 연결 대상 클라이언트가 해당 ID를 확인할 수 있도록 서버 인증서 체인을 제공합니다.

>[!NOTE]
>이 문서에서는 IoT Edge 장치에 있는 다양 한 구성 요소 간 또는 IoT Edge 장치와 리프 장치 간에 연결을 보호 하는 데 사용 되는 인증서에 대해 설명 합니다. 인증서를 사용 하 여 IoT Hub IoT Edge 장치를 인증할 수도 있습니다. 이러한 인증 인증서는 서로 다르며이 문서에서 설명 하지 않습니다. 인증서를 사용 하 여 장치를 인증 하는 방법에 대 한 자세한 내용은 [x.509 인증서를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-x509-certs.md)을 참조 하세요.

이 문서에서는 프로덕션, 개발 및 테스트 시나리오에서 IoT Edge 인증서의 작동 방식을 설명합니다. 스크립트는 다르지만(Powershell 대 bash), 개념은 Linux와 Windows 간에 동일합니다.

## <a name="iot-edge-certificates"></a>IoT Edge 인증서

IoT Edge 장치에서 인증서를 설정 하는 일반적인 시나리오에는 두 가지가 있습니다. 경우에 따라 장치의 최종 사용자 또는 운영자가 제조업체에서 만든 일반 장치를 구매한 후 인증서 자체를 관리 합니다. 또는 제조업체는 계약에 따라 운영자를 위한 사용자 지정 장치를 빌드하고 장치를 처리 하기 전에 일부 초기 인증서 서명을 수행 합니다. IoT Edge 인증서 디자인은 두 시나리오를 모두 고려하고자 합니다.

다음 그림은 IoT Edge의 인증서 사용을 보여줍니다. 루트 CA 인증서와 디바이스 CA 인증서 간에는 중간 서명 인증서가 없을 수도 있고 하나 이상 있을 수도 있습니다. 아래에 한 가지 사례가 나와 있습니다.

![일반적인 인증서 관계의 다이어그램](./media/iot-edge-certs/edgeCerts-general.png)

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2050 년 1 월 1 일 이후에 만료 되는 인증서를 사용할 수 없습니다. 이러한 제한 사항은 장치 CA 인증서, 신뢰 번들의 인증서 및 x.509 프로비저닝 방법에 사용 되는 장치 ID 인증서에 적용 됩니다.

### <a name="certificate-authority"></a>인증 기관

인증 기관 또는 줄여서 ‘CA’는 디지털 인증서를 발급하는 엔터티입니다. 인증 기관은 인증서의 소유자와 수신자 간에 신뢰할 수 있는 타사 역할을 합니다. 디지털 인증서는 인증서의 받는 사람에 대한 공개 키의 소유권을 인정합니다. 인증서 신뢰 체인은 초기에 루트 인증서가 발급되면서 작동합니다. 이는 기관에서 발급되는 모든 인증서에서 신뢰의 기반이 됩니다. 그 후에, 소유자는 루트 인증서를 사용하여 추가 중간 인증서(‘리프’ 인증서)를 발급할 수 있습니다.

### <a name="root-ca-certificate"></a>루트 CA 인증서

루트 CA 인증서는 전체 프로세스에서 신뢰의 기반입니다. 프로덕션 시나리오에서 이 CA 인증서는 대개 Baltimore, Verisign, DigiCert 등의 신뢰할 수 있는 상업용 인증 기관에서 구매한 인증서입니다. IoT Edge 디바이스에 연결하는 디바이스를 완전히 제어할 수 있어야 회사 수준 인증 기관을 사용할 수 있습니다. 어떤 경우에도 IoT Edge Hub의 전체 인증서 체인은 루트 인증서까지 롤업되므로 리프 IoT 디바이스는 루트 인증서를 신뢰해야 합니다. 루트 CA 인증서를 신뢰할 수 있는 루트 인증 기관 저장소에 저장할 수도 있고, 애플리케이션 코드에서 인증서 세부 정보를 제공할 수도 있습니다.

### <a name="intermediate-certificates"></a>중간 인증서

보안 디바이스를 만들기 위한 일반적인 제조 프로세스에서 주로 유출 또는 노출의 위험으로 인해 루트 CA 인증서가 직접 사용되는 경우는 거의 없습니다. 루트 CA 인증서는 중간 CA 인증서를 하나 이상 만들고 디지털 서명을 합니다. 하나만 있거나 이러한 중간 인증서 체인이 있을 수 있습니다. 중간 인증서의 체인이 필요한 시나리오는 다음과 같습니다.

* 제조업체 내 부서의 계층 구조.

* 디바이스의 프로덕션에 순차적으로 참여하는 여러 회사.

* 제조업체가 해당 고객을 대신하여 만든 디바이스를 서명하도록 루트 CA를 구매하고, 서명 인증서를 파생하는 고객.

어떤 경우든 제조업체는 이 체인의 마지막에 중간 CA 인증서를 사용하여 최종 디바이스에 배치된 디바이스 CA 인증서에 서명합니다. 일반적으로 이러한 중간 인증서는 제조 공장에서 엄격히 보호됩니다. 물리적 및 전자적으로 해당 사용에 대해 엄격한 프로세스를 거칩니다.

### <a name="device-ca-certificate"></a>디바이스 CA 인증서

디바이스 CA 인증서는 프로세스에서 최종 중간 CA 인증서로 생성 및 서명됩니다. 이 인증서는 IoT Edge 디바이스 자체에 설치되며, HSM(하드웨어 보안 모듈)과 같은 보안 스토리지에 설치하는 것이 좋습니다. 또한 디바이스 CA 인증서는 IoT Edge 디바이스를 고유하게 식별합니다. 디바이스 CA 인증서는 다른 인증서에 서명할 수 있습니다.

### <a name="iot-edge-workload-ca"></a>IoT Edge 워크로드 CA

IoT Edge를 처음 시작하면 [IoT Edge 보안 관리자](iot-edge-security-manager.md)가 프로세스의 "운영자" 측 첫 번째 인증서인 워크로드 CA 인증서를 생성합니다. 이 인증서는 장치 CA 인증서에서 생성 되 고 서명 됩니다. 또 다른 중간 서명 인증서인 이 인증서는 IoT Edge 런타임에서 사용되는 다른 인증서를 생성 및 서명하는 데 사용됩니다. 현재는 이 인증서가 기본적으로 다음 섹션에서 설명할 IoT Edge Hub 서버 인증서이지만, 앞으로는 IoT Edge 구성 요소 인증용 기타 인증서도 포함될 수 있습니다.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge Hub 서버 인증서

IoT Edge Hub 서버 인증서는 IoT Edge에 필요한 TLS 연결을 설정하는 동안 ID 확인을 위해 리프 디바이스와 모듈에 표시되는 실제 인증서입니다. 이 인증서는 리프 IoT 디바이스가 신뢰해야 하는 루트 CA 인증서를 생성하는 데 사용되는 서명 인증서의 전체 체인을 표시합니다. IoT Edge 보안 관리자에서 생성되는 경우 이 IoT Edge Hub 인증서의 CN(일반 이름)은 소문자로 변환된 후 config.yaml 파일의 'hostname' 속성으로 설정됩니다. 이 구성은 IoT Edge의 혼란에 관한 일반적인 원인입니다.

## <a name="production-implications"></a>프로덕션 영향

“IoT Edge에 ‘워크로드 CA’ 추가 인증서는 왜 필요한가요? 디바이스 CA 인증서를 사용하여 IoT Edge Hub 서버 인증서를 직접 생성할 수는 없나요?"와 같은 질문이 제기될 수도 있습니다. 기술적으로 말하자면, 가능합니다. 그러나 이 “워크로드” 중간 인증서의 목적은 디바이스 제조업체와 디바이스 운영자 간에 문제를 분리하는 것입니다. IoT Edge 디바이스가 한 고객에게서 다른 고객에게 팔리거나 전송되는 시나리오를 상상해보세요. 제조업체에서 제공한 변경이 불가능한 디바이스 CA 인증서를 원할 가능성이 높습니다. 그러나 디바이스 작업별 “워크로드” 인증서는 새 배포를 위해 초기화되고 다시 생성되어야 합니다.

제조와 운영 프로세스는 분리되어 있으므로 프로덕션 디바이스를 준비할 때는 다음 사항을 고려합니다.

* 모든 인증서 기반 프로세스에서는 IoT Edge 디바이스를 롤아웃하는 전체 프로세스 동안 루트 CA 인증서 및 중간 CA 인증서를 보호하고 모니터링해야 합니다. IoT Edge 디바이스 제조업체는 중간 인증서를 적절하게 스토리지하고 사용할 수 있도록 강력한 프로세스를 갖추어야 합니다. 또한 디바이스 CA 인증서는 디바이스 자체, 특히 하드웨어 보안 모듈에 최대한 안전하게 스토리지해야 합니다.

* IoT Edge Hub에서 IoT Edge Hub 서버 인증서를 연결 대상 클라이언트 디바이스와 모듈에 제시합니다. 디바이스 CA 인증서의 CN(일반 이름)은 IoT Edge 디바이스의 config.yaml에서 사용할 "hostname"과 같을 수 **없습니다**. 예를 들어 연결 문자열의 GatewayHostName 매개 변수 또는 MQTT의 CONNECT 명령을 통해 IoT Edge에 연결할 클라이언트에서 사용하는 이름은 디바이스 CA 인증서에 사용되는 일반 이름과 같을 수 **없습니다**. 이 제한이 적용되는 이유는 클라이언트에서 확인할 수 있도록 IoT Edge Hub가 전체 인증서 체인을 표시하기 때문입니다. IoT Edge Hub 서버 인증서와 디바이스 CA 인증서의 CN이 같은 경우에는 확인이 계속 반복되어 인증서가 무효화됩니다.

* 디바이스 CA 인증서는 IoT Edge 보안 디먼에서 최종 IoT Edge 인증서를 생성하는 데 사용되므로 그 자체가 인증서 서명 기능이 있는 서명 인증서여야 합니다. 디바이스 CA 인증서에 "V3 기본 제한 CA:True"를 적용하면 필요한 키 사용 속성이 자동 설정됩니다.

>[!Tip]
> 이미 IoT Edge를 “편리한 스크립트”를 사용한 개발/테스트 시나리오에서 투명 게이트웨이로 설정하고(다음 섹션 참조), config.yaml의 호스트 이름에 대해 수행한 것처럼 디바이스 CA 인증서를 만들 때 동일한 호스트 이름을 사용한 경우에는 왜 작동하는지 궁금할 것입니다. 개발자 환경을 간소화하기 위한 노력으로, 편리한 스크립트는 사용자가 스크립트에 전달하는 이름의 마지막에 “ca”를 추가합니다. 따라서 예를 들어, 스크립트의 디바이스 이름과 config.yaml의 호스트 이름 모두에 “mygateway”를 사용한 경우 전자는 디바이스 CA 인증서에 대한 CN으로 사용되기 전에 mygateway.ca로 바뀝니다.

## <a name="devtest-implications"></a>개발/테스트 영향

개발 및 테스트 시나리오를 간소화하기 위해 Microsoft는 투명 게이트웨이 시나리오의 IoT Edge에 적합한 비-프로덕션 인증서를 생성하기 위한 [편리한 스크립트](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) 집합을 제공합니다. 스크립트의 작동 방식에 대한 예제는 [IoT Edge 디바이스 기능을 테스트하기 위하 데모 인증서 만들기](how-to-create-test-certificates.md)를 참조하세요.

>[!Tip]
> 디바이스 IoT “리프” 디바이스 및 IoT Edge를 통해 IoT 디바이스 SDK를 사용하는 애플리케이션에 연결하려면 디바이스의 연결 문자열의 끝에 선택적 GatewayHostName 매개 변수를 추가해야 합니다. Edge Hub 서버 인증서는 생성될 때 config.yaml의 소문자 버전 호스트 이름을 기반으로 하므로, 이름을 일치시키고 TLS 인증서 확인에 성공하려면 GatewayHostName 매개 변수를 소문자로 입력해야 합니다.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>IoT Edge 인증서 계층 구조의 예

다음 스크린샷은 이 인증서 경로의 예를 보여주기 위해 투명 게이트웨이로 설정된 작동 중인 IoT Edge 디바이스에서 가져온 것입니다. OpenSSL은 IoT Edge Hub에 연결하여 인증서의 유효성을 검사한 다음 유효하지 않은 인증서를 폐기하는 데 사용됩니다.

![각 수준의 인증서 계층 구조 스크린샷](./media/iot-edge-certs/iotedge-cert-chain.png)

스크린샷에 표시된 인증서 깊이의 계층 구조를 확인할 수 있습니다.

| 루트 CA 인증서         | Azure IoT Hub CA 인증서 테스트 전용                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| 중간 CA 인증서 | Azure IoT Hub 중간 인증서 테스트 전용                                                                 |
| 디바이스 CA 인증서       | iotgateway.ca(“iotgateway”가 < gateway host name >으로 편리한 스크립트에 전달되었음)   |
| 워크로드 CA 인증서     | iotedge 워크로드 ca                                                                                       |
| IoT Edge Hub 서버 인증서 | iotedgegw.local(config.yaml의 ‘hostname’에 일치)                                            |

## <a name="next-steps"></a>다음 단계

[Azure IoT Edge 모듈 이해](iot-edge-modules.md)

[IoT Edge 디바이스를 투명 게이트웨이로 작동하도록 구성](how-to-create-transparent-gateway.md)

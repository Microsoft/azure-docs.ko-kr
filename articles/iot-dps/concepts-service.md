---
title: Azure IoT Hub Device Provisioning Service의 서비스 개념 | Microsoft Docs
description: Device Provisioning 서비스 및 IoT Hub를 사용하는 디바이스에 해당하는 서비스 프로비전 개념을 설명합니다.
author: nberdy
ms.author: nberdy
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 4a4f53f991355e634e8139f9e90bec6c508a527d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745811"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT Hub Device Provisioning Service 개념

IoT Hub Device Provisioning Service는 지정된 IoT 허브에 대한 제로 터치 디바이스 프로비저닝을 구성하도록 사용하는 IoT Hub에 대한 도우미 서비스입니다. Device Provisioning 서비스를 사용하여 안전하고 확장 가능한 방식으로 수백만 개의 디바이스를 [자동 프로비전](concepts-auto-provisioning.md)할 수 있습니다.

디바이스 프로비전은 두 부분의 프로세스로 구성됩니다. 첫 번째 부분은 디바이스를 *등록*함으로써 디바이스와 IoT 솔루션 간 초기 연결을 설정하는 것입니다. 두 번째 부분은 솔루션의 특정 요구 사항에 따라 디바이스에 적절한 *구성*을 적용하는 것입니다. 두 단계가 수행되고 나면 디바이스가 완벽히 *프로비전된* 것입니다. Device Provisioning Service는 두 단계를 모두 자동화하여 디바이스에 원활한 프로비전 환경을 제공합니다.

이 문서는 *서비스* 관리에 가장 적합한 프로비전 개념의 개요를 제공합니다. 이 문서는 배포를 위해 디바이스를 준비하는 [클라우드 설치 단계](about-iot-dps.md#cloud-setup-step)에 관련된 사람에게 가장 적합합니다.

## <a name="service-operations-endpoint"></a>서비스 작업 엔드포인트

서비스 작업 엔드포인트는 서비스 설정을 관리하고 등록 목록을 유지 관리하기 위한 엔드포인트입니다. 이 엔드포인트는 서비스 관리자에 의해서만 사용됩니다. 디바이스에서 사용되지 않습니다.

## <a name="device-provisioning-endpoint"></a>디바이스 프로비저닝 엔드포인트

디바이스 프로비전 엔드포인트는 모든 디바이스가 자동 프로비전에 사용하는 단일 엔드포인트입니다. URL은 공급 체인 시나리오에서 새 연결 정보를 사용하여 디바이스를 새로 고쳐야 하는 필요성을 경감하기 위해 모든 프로비전 서비스 인스턴스에 대해 동일합니다. ID 범위는 테넌트 격리를 보장합니다.

## <a name="linked-iot-hubs"></a>연결된 IoT 허브

Device Provisioning 서비스는 Device Provisioning 서비스에 연결된 IoT 허브에만 프로비전할 수 있습니다. IoT 허브를 Device Provisioning 서비스 인스턴스에 연결하면 서비스에 IoT 허브의 디바이스 레지스트리에 대한 읽기/쓰기 권한을 부여합니다. 링크를 사용하면 Device Provisioning 서비스는 디바이스 ID를 등록하고 디바이스 쌍에서 초기 구성을 설정할 수 있습니다. 연결된 IoT 허브는 모든 Azure 지역에 있을 수 있습니다. 다른 구독의 허브를 프로비전 서비스에 연결할 수 있습니다.

## <a name="allocation-policy"></a>할당 정책

서비스 수준 설정은 Device Provisioning Service에서 IoT 허브에 디바이스를 할당하는 방법을 결정합니다. 세 가지의 지원되는 할당 정책이 있습니다.

* **균등하게 가중치 적용 배포**: 연결된 IoT 허브는 디바이스를 동일하게 프로비전했을 가능성이 있습니다. 기본 설정 디바이스를 단 하나의 IoT Hub에 프로비전하려는 경우 이 설정을 유지할 수 있습니다.

* **최소 대기 시간**: 디바이스가 디바이스에 대한 최소 대기 시간으로 IoT 허브에 프로비전됩니다. 연결된 여러 IoT 허브가 동일한 최소 대기 시간을 제공하는 경우 프로비전 서비스는 이러한 허브에서 디바이스를 해싱합니다.

* **등록 목록을 통한 고정 구성**: 등록 목록에서 원하는 IoT Hub의 사양을 서비스 수준 할당 정책보다 우선합니다.

## <a name="enrollment"></a>등록

등록은 자동 프로비전을 통해 등록될 수 있는 디바이스 또는 디바이스 그룹의 레코드입니다. 등록 레코드에는 다음을 포함하여 디바이스 또는 디바이스 그룹에 대한 정보가 들어 있습니다.
- 디바이스에서 사용되는 [증명 메커니즘](concepts-security.md#attestation-mechanism)
- 원하는 초기 구성(선택 사항)
- 원하는 IoT Hub
- 원하는 디바이스 ID

Device Provisioning 서비스에는 다음 두 가지 유형의 등록을 지원합니다.

### <a name="enrollment-group"></a>등록 그룹

등록 그룹은 특정 증명 메커니즘을 공유하는 디바이스의 그룹입니다. 등록 그룹의 모든 디바이스는 동일한 루트 CA 또는 중간 CA(인증 기관)에서 서명된 X.509 인증서를 제공합니다. 등록 그룹은 X.509 증명 메커니즘만 사용할 수 있습니다. 등록 그룹 이름 및 인증서 이름은 영숫자, 소문자여야 하며 하이픈을 포함할 수 있습니다.

> [!TIP]
> 원하는 초기 구성을 공유하는 다수의 디바이스 또는 동일한 테넌트로 이동하는 디바이스에 대한 등록 그룹을 사용하는 것이 좋습니다.

### <a name="individual-enrollment"></a>개별 등록

개별 등록은 등록할 수 있는 단일 디바이스에 대한 항목입니다. 개별 등록은 증명 메커니즘으로 X.509 리프 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 개별 등록의 등록 ID는 영숫자, 소문자이며 하이픈을 포함할 수 있습니다. 개별 등록은 지정된 원하는 IoT Hub 디바이스 ID가 있을 수 있습니다.

> [!TIP]
> 고유한 초기 구성이 필요한 디바이스 또는 TPM 증명을 통해 SAS 토큰만으로 인증될 수 있는 디바이스의 경우 개별 등록을 사용하는 것이 좋습니다.

## <a name="registration"></a>등록

등록은 Device Provisioning 서비스를 통해 IoT Hub에 성공적으로 등록/프로비전하는 디바이스의 레코드입니다. 등록 레코드는 자동으로 생성됩니다. 삭제될 수 있지만 업데이트할 수 없습니다.

## <a name="operations"></a>작업

작업은 Device Provisioning 서비스의 대금 청구 단위입니다. 하나의 작업은 서비스에 대한 하나의 명령의 성공적인 완료입니다. 작업에는 디바이스 등록 및 재등록이 포함되며, 등록 목록 항목 추가, 등록 목록 항목 업데이트 같은 서비스 쪽 변경도 포함됩니다.

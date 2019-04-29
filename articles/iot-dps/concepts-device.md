---
title: Azure 디바이스 프로비저닝에서 디바이스 개념 | Microsoft Docs
description: Device Provisioning Service 및 IoT Hub를 사용하는 디바이스에 해당하는 디바이스 프로비저닝 개념 설명
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 2904da863707c5f653d774b0a480cc48c95c8d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745981"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT Hub Device Provisioning Service 디바이스 개념

IoT Hub Device Provisioning Service는 지정된 IoT 허브에 대한 제로 터치 디바이스 프로비저닝을 구성하도록 사용하는 IoT Hub에 대한 도우미 서비스입니다. Device Provisioning Service를 사용하여 안전하고 확장 가능한 방식으로 수백만 개의 디바이스를 프로비전할 수 있습니다.

이 문서는 디바이스 프로비저닝과 관련된 *디바이스* 개념의 개요를 제공합니다. 이 문서는 배포를 위해 디바이스를 준비하는 [제조 단계](about-iot-dps.md#manufacturing-step)에 관련된 사람에게 가장 적합합니다.

## <a name="attestation-mechanism"></a>증명 메커니즘

증명 메커니즘은 디바이스의 ID 확인에 사용되는 방법입니다. 증명 메커니즘은 지정된 디바이스로 사용할 증명 방법을 프로비전 서비스에 알려 주는 등록 목록과 관련되어 있기도 합니다.

> [!NOTE]
> IoT Hub는 해당 서비스에서 비슷한 개념에 대해 "인증 체계"를 사용합니다.

Device Provisioning Service는 다음 형식의 증명을 지원합니다.
* 표준 X.509 인증서 인증 흐름을 기반으로 하는 **X.509 인증서**
* nonce 문제를 기반으로 하는 **TPM(신뢰할 수 있는 플랫폼 모듈)** 은 서명된 SAS(공유 액세스 서명) 토큰을 표시하기 위해 키에 TPM 표준을 사용합니다. 디바이스에서 실제 TPM이 필요하지 않지만 서비스는 [TPM 사양](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)당 인증 키를 사용하여 증명하기를 기대합니다.
* 해시된 서명과 포함된 만료 날짜가 들어 있는 SAS(공유 액세스 서명) [보안 토큰](../iot-hub/iot-hub-devguide-security.md#security-tokens)을 기반으로 하는 **대칭 키**. 자세한 내용은 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 참조하세요.

## <a name="hardware-security-module"></a>하드웨어 보안 모듈

하드웨어 보안 모듈 또는 HSM은 안전한 디바이스 비밀의 하드웨어 기반 저장소를 위해 사용되며 가장 안전한 형태의 비밀 저장소입니다. X.509 인증서 및 SAS 토큰은 HSM에 저장될 수 있습니다. HSM은 증명 메커니즘 및 프로비전 서비스 지원에 사용될 수 있습니다.

> [!TIP]
> 디바이스에 비밀을 안전하게 저장하는 데 디바이스와 함께 HSM을 사용하는 것이 좋습니다.

디바이스 비밀은 소프트웨어(메모리)에도 저장될 수 있지만 HSM보다 덜 안전한 형태의 저장소입니다.

## <a name="registration-id"></a>등록 ID

Device Provisioning Service에서 디바이스를 고유하게 식별하는 데 사용되는 등록 ID입니다. 디바이스 ID는 프로비전 서비스 [ID 범위](#id-scope)에서 고유해야 합니다. 각 디바이스에는 등록 ID가 있어야 합니다. 등록 ID는 영숫자, 소문자이며 하이픈을 포함할 수 있습니다.

* TPM의 경우 TPM 자체에서 등록 ID를 제공합니다.
* X.509 기반 증명의 경우 등록 ID는 인증서의 주체 이름으로 제공됩니다.

## <a name="device-id"></a>디바이스 ID

디바이스 ID는 IoT Hub에 표시되는 ID입니다. 등록 항목에서 원하는 디바이스 ID를 설정할 수 있지만 설정할 필요가 없습니다. 등록 목록에 원하는 디바이스 ID가 지정되지 않은 경우 등록 ID는 디바이스를 등록할 때 디바이스 ID로 사용됩니다. [IoT Hub의 디바이스 ID](../iot-hub/iot-hub-devguide-identity-registry.md)에 대해 자세히 알아봅니다.

## <a name="id-scope"></a>ID 범위

ID 범위는 사용자에 의해 생성되고 디바이스가 등록할 특정 프로비전 서비스를 고유하게 식별하는 데 사용되는 경우 Device Provisioning Service에 할당됩니다. ID 범위는 서비스에 의해 생성되고 변경할 수 없으며 고유성이 보장됩니다.

> [!NOTE]
> 고유성은 장기 실행되는 배포 작업 및 병합 및 취득 시나리오에 중요합니다.

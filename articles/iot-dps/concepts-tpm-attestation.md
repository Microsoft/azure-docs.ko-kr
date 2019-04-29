---
title: Azure IoT Hub Device Provisioning Service - TPM 증명
description: 이 문서에서는 IoT 디바이스 프로비저닝 서비스를 사용하여 TPM 증명 흐름에 대한 개념적 개요를 제공합니다.
author: nberdy
ms.author: nberdy
ms.date: 04/23/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 07c5dbce0b98d1c197164f4fc77682f78ede57f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746473"
---
# <a name="tpm-attestation"></a>TPM 증명

IoT Hub Device Provisioning Service는 지정된 IoT 허브에 대한 제로 터치 디바이스 프로비저닝을 구성하도록 사용하는 IoT Hub에 대한 도우미 서비스입니다. 디바이스 프로비저닝 서비스를 사용하여 안전한 방식으로 수백만 개의 디바이스를 프로비전할 수 있습니다.

이 문서에서는 [TPM](./concepts-device.md)을 사용하는 경우 ID 증명 프로세스를 설명합니다. TPM은 신뢰할 수 있는 플랫폼 모듈을 의미하고 HSM(하드웨어 보안 모듈)의 형식입니다. 이 문서에서는 불연속, 펌웨어 또는 통합 TPM을 사용한다고 가정합니다. 소프트웨어 에뮬레이트된 TPM은 프로토타이핑 또는 테스트에 적합하지만 불연속, 펌웨어 또는 통합 TPM이 제공하는 것과 동일한 수준의 보안을 제공하지 않습니다. 프로덕션 환경에서 TPM 소프트웨어를 사용하는 것은 좋지 않습니다. TPM 유형에 대한 자세한 내용은 [TPM에 대한 간략한 소개](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf)를 참조하세요.

이 문서는 HMAC 키 지원 및 해당 인증 키가 있는 TPM 2.0을 사용하는 디바이스에 해당합니다. 인증을 위해 X.509 인증서를 사용하는 디바이스에 대한 것이 아닙니다. TPM은 신뢰할 수 있는 컴퓨팅 그룹에서 업계 차원의 ISO 표준이며, [TPM 2.0 사양 완료](https://trustedcomputinggroup.org/tpm-library-specification/) 또는 [ISO/IEC 11889 사양](https://www.iso.org/standard/66510.html)에서 TPM에 대해 자세히 알아볼 수 있습니다. 이 문서에서는 공개 및 개인 키 쌍과 암호화에 사용되는 방법을 잘 알고 있다고 가정합니다.

디바이스 프로비저닝 서비스 디바이스 SDK는 이 문서에 설명되어 있는 모든 작업을 처리합니다. 디바이스에서 SDK를 사용하는 경우 추가로 아무 것도 구현할 필요가 없습니다. 이 문서는 디바이스에서 프로비전할 때 TPM 보안 칩에서 일어나는 일과 보안이 유지되는 이유를 개념적으로 이해하도록 도와 줍니다.

## <a name="overview"></a>개요

TPM은 신뢰의 보안 루트로 EK(인증 키)라고 하는 것을 사용합니다. EK는 TPM에 고유하며 이를 기본적으로 변경하면 디바이스를 새 디바이스로 변경합니다.

TPM에는 SRK(저장소 루트 키)라고 하는 다른 유형의 키가 있습니다. SRK는 TPM의 소유권을 가져온 후 TPM의 소유자에 의해 생성될 수 있습니다. TPM의 소유권을 가져오는 것은 "사용자가 HSM에서 암호 설정"이라는 TPM 전용 방법입니다. TPM 디바이스가 새 소유자에게 판매되는 경우 새 소유자는 새 SRK를 생성하는 TPM의 소유권을 가져올 수 있습니다. 새 SRK 생성으로 이전 소유자는 TPM을 사용할 수 없습니다. SRK는 TPM의 소유자에게 고유하므로 해당 소유자에 대한 TPM 자체로 데이터를 봉인하는 데 SRK를 사용할 수 있습니다. SRK는 해당 키를 저장하도록 소유자에 대한 샌드박스를 제공하고 디바이스 또는 TPM이 판매되는 경우 액세스 취소 가능성을 제공합니다. 이는 새 집으로 이사하는 것과 같습니다. 소유권을 가져오는 것은 문에 대한 잠금을 변경하고 이전 소유자(SRK)가 남긴 모든 가구를 파괴하지만 집의 주소(EK)를 변경할 수 없습니다.

디바이스가 설정되고 사용할 준비가 되면 EK 및 SRK를 모두 사용할 수 있습니다.

![TPM의 소유권 가져오기](./media/concepts-tpm-attestation/tpm-ownership.png)

Tpm 소유권에 대 한 한 가지 주의할 점은 TPM의 소유권, TPM 제조업체, 사용 중인 TPM 도구 집합 및 장치 OS 포함 하 여 여러 가지 요인에 따라 달라 집니다. 시스템과 관련된 지침을 따라 소유권을 가져옵니다.

디바이스 프로비저닝 서비스는 EK(EK_pub)의 공용 부분을 사용하여 디바이스를 식별하고 등록합니다. 디바이스 공급 업체는 제조 또는 최종 테스트하는 동안 EK_pub를 읽을 수 있으며 프로비전에 연결할 때 디바이스를 인식할 수 있도록 EK_pub를 프로비저닝 서비스에 업로드할 수 있습니다. 디바이스 프로비저닝 서비스는 SRK 또는 소유자를 확인하지 않으므로 TPM을 "지우는 것"은 고객 데이터를 지우지만 EK(및 다른 공급 업체 데이터)는 유지되고 디바이스는 프로비전에 연결할 때 디바이스 프로비저닝 서비스에서 여전히 인식됩니다.

## <a name="detailed-attestation-process"></a>자세한 증명 프로세스

TPM이 있는 디바이스를 디바이스 프로비저닝 서비스에 처음 연결하면 서비스는 먼저 등록 목록에 저장된 EK_pub에 대해 제공된 EK_pub를 확인합니다. EK_pub가 일치하지 않는 경우 디바이스는 프로비전되지 않습니다. EK_pub가 일치하는 경우 서비스는 디바이스에서 nonce 챌린지를 통해 EK의 비공개 부분 소유권을 증명하도록 요구하며, 이는 ID를 증명하는 데 사용되는 안전한 챌린지입니다. 디바이스 프로비저닝 서비스는 nonce를 생성한 다음, 초기 등록 호출 중에 디바이스에서 제공되는 SRK 및 EK_pub로 이를 차례로 암호화합니다. TPM은 항상 EK 보안의 비공개 부분을 유지합니다. 이는 위조를 방지하고 SAS 토큰이 승인된 디바이스에 안전하게 프로비전되도록 합니다.

증명 프로세스를 자세히 살펴보겠습니다.

### <a name="device-requests-an-iot-hub-assignment"></a>디바이스에서 IoT Hub 할당 요청

먼저 디바이스가 디바이스 프로비저닝 서비스에 연결되고 프로비전을 요청합니다. 이 과정에서 디바이스는 TPM에서 해당 등록 ID, ID 범위 및 EK_pub와 SRK_pub가 있는 서비스를 제공합니다. 서비스는 디바이스에 암호화된 nonce를 다시 전달하고 디바이스에서 nonce의 암호를 해독하고 다시 연결하고 프로비전을 마치도록 SAS 토큰에 서명하는 데 사용하도록 요청합니다.

![디바이스에서 프로비전 요청](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Nonce 챌린지

디바이스는 nonce를 사용하고 EK 및 SRK의 비공개 부분을 사용하여 TPM으로 nonce의 암호를 해독합니다. nonce 암호화의 순서는 변경할 수 없는 EK에서 새 소유자가 TPM의 소유권을 가져오는 경우 변경할 수 있는 SRK로 신뢰를 위임합니다.

![Nonce의 암호 해독](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Nonce의 유효성 검사 및 자격 증명 수신

디바이스는 암호가 해독된 nonce를 사용하여 SAS 토큰에 서명하고 서명된 SAS 토큰을 사용하여 디바이스 프로비저닝 서비스에 연결을 다시 설정할 수 있습니다. Nonce 챌린지가 완료되면 서비스는 디바이스에서 프로비전을 허용합니다.

![디바이스에서 EK 소유권의 유효성을 검사하도록 Device Provisioning 서비스에 연결 다시 설정](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>다음 단계

이제 디바이스는 IoT Hub에 연결되고, 디바이스의 키가 안전하게 저장되었다는 정보로 안전이 유지됩니다. 이제 디바이스 프로비저닝 서비스에서 TPM을 사용하여 디바이스의 ID를 안전하게 확인하는 방법을 배웠습니다. 자세히 알아보려면 다음 문서를 참조하세요.

* [자동 프로비전의 모든 개념에 대해 알아보기](./concepts-auto-provisioning.md)
* 흐름을 처리하도록 SDK를 사용하여 [자동 프로비전 사용을 시작합니다](./quick-setup-auto-provision.md).

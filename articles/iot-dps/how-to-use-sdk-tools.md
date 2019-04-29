---
title: Azure IoT Hub Device Provisioning Service SDK에 제공된 도구를 사용하여 개발 간소화
description: 이 문서에서는 개발을 위한 Azure IoT Hub Device Provisioning Service SDK에서 제공되는 도구를 검토합니다.
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627324"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>SDK에서 제공된 도구를 사용하여 프로비전할 개발을 간소화하는 방법
IoT Hub Device Provisioning 서비스는 안전하고 확장 가능한 방식으로 Just-In-Time 무인 [자동 프로비전](concepts-auto-provisioning.md)을 사용하여 프로비전 프로세스를 간소화합니다.  X.509 인증서 또는 TPM(신뢰할 수 있는 플랫폼 모듈)의 형식인 보안 증명이 필요합니다.  Microsoft는 [다른 보안 하드웨어 파트너](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)와도 협력하여 IoT 배포를 보호하는 경우에 신뢰성을 향상시킵니다. 하드웨어 보안 요구 사항을 이해하는 것은 개발자에게 매우 어려울 수 있습니다. 개발자가 프로비전 서비스와 통신하는 클라이언트를 작성하는 편의 계층을 사용할 수 있도록 일련의 Azure IoT Provisioning SDK가 제공됩니다. 또한 SDK에서는 개발에서 보안 증명을 간소화하기 위한 도구 집합뿐만 아니라 일반적인 시나리오에 대한 샘플을 제공합니다.

## <a name="trusted-platform-module-tpm-simulator"></a>TPM(신뢰할 수 있는 플랫폼 모듈) 시뮬레이터
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security)은 플랫폼을 인증하는 키를 안전하게 저장하기 위한 표준을 참조할 수 있거나 표준을 구현하는 모듈과 상호 작용하는 데 사용되는 I/O 인터페이스를 참조할 수 있습니다. TPM은 별도 하드웨어, 통합된 하드웨어, 펌웨어 기반 또는 소프트웨어 기반으로 존재할 수 있습니다.  프로덕션에서 TPM은 별도 하드웨어, 통합 하드웨어로 또는 펌웨어 기반인 디바이스에 위치합니다. 테스트 단계에서 소프트웨어 기반 TPM 시뮬레이터는 개발자에게 제공됩니다.  지금은 이 시뮬레이터를 Windows 플랫폼에서 개발하는 경우에만 사용할 수 있습니다.

TPM 시뮬레이터를 사용하기 위한 단계는 다음과 같습니다.
1. [개발 환경을 준비](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java)하고 GitHub 리포지토리를 복제합니다.
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` 아래에 있는 TPM 시뮬레이터 폴더로 이동합니다.
3. 장치를 프로비전하기 위해 클라이언트 애플리케이션을 실행하기 전에 Simulator.exe를 실행합니다.
4. 등록 ID 및 인증 키를 가져오기 위해 프로비전 프로세스 전체에서 백그라운드에 시뮬레이터를 실행합니다.  두 값은 실행의 한 인스턴스에서만 유효합니다.

## <a name="x509-certificate-generator"></a>X.509 인증서 생성기
[X.509 인증서](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates)를 증명 메커니즘으로 사용하여 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다.  [여러 가지 방법으로](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) X.509 인증서를 가져옵니다.
* 프로덕션 환경에서는 공용 루트 인증 기관에서 X.509 CA 인증서를 구입하는 것이 좋습니다.
* 환경 테스트를 위해 다음을 사용하여 X.509 루트 인증서 또는 X.509 인증서 체인을 생성할 수 있습니다.
    * OpenSSL: 인증서 생성을 위해 스크립트를 사용할 수 있습니다.
        * [Node.JS](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell 또는 Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * DICE(Device Identity Composition Engine) 에뮬레이터: DICE는 TLS 프로토콜 및 X.509 클라이언트 인증서에 기반한 암호화 디바이스 ID 및 증명에 사용할 수 있습니다.  DICE를 사용하는 디바이스 ID에 대해 자세히 [알아봅니다](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/).

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>DICE 에뮬레이터와 함께 X.509 인증서 생성기 사용
SDK는 [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator)에 위치하는 DICE 에뮬레이터와 함께 X.509 인증서 생성기를 제공합니다.  이 생성자는 플랫폼 간에 작동합니다.  다른 언어로 개발을 위해 생성된 인증서를 사용할 수 있습니다.

반면 현재 DICE 에뮬레이터는 루트 인증서, 중간 인증서, 리프 인증서 및 연결된 개인 키를 출력합니다.  그러나 루트 인증서 또는 중간 인증서는 별도 리프 인증서를 서명하는 데 사용할 수 없습니다.  서명 인증서를 여러 디바이스의 리프 인증서 서명에 사용하는 그룹 등록 시나리오를 테스트하는 경우 인증서 체인을 생성하기 위해 OpenSSL을 사용할 수 있습니다.

이 생성자를 사용하여 X.509 인증서를 생성하려면:
1. [개발 환경을 준비](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java)하고 GitHub 리포지토리를 복제합니다.
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. 루트를 azure-iot-sdk-java로 변경합니다.
3. ```mvn install -DskipTests=true```를 실행하여 모든 필수 패키지를 다운로드하고 SDK를 컴파일합니다.
4. ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```에서 X.509 인증서 생성기에 대한 루트로 이동합니다.
5. ```mvn clean install```을 사용하여 빌드
6. 다음 명령을 사용하여 도구를 실행합니다.
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. 메시지가 표시되면 필요에 따라 인증서에 대해 _일반 이름_을 입력할 수 있습니다.
8. 이 도구는 **클라이언트 인증서**, **클라이언트 인증서 개인 키**, **중간 인증서** 및 **루트 인증서**를 로컬로 생성합니다.

**클라이언트 인증서**는 디바이스에 대한 리프 인증서입니다.  **클라이언트 인증서** 및 연결된 **클라이언트 인증서 개인 키**는 디바이스 클라이언트에 필요합니다. 선택한 언어에 따라 클라이언트 애플리케이션에 이를 배치하는 메커니즘은 달라질 수 있습니다.  자세한 내용은 X.509를 사용하여 시뮬레이션된 디바이스 만들기에서 [빠른 시작](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509)을 참조하세요.

루트 인증서 또는 중간 인증서는 [프로그래밍 방식으로](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) 또는 [포털](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)을 사용하여 등록 그룹 또는 개별 등록을 만드는 데 사용합니다.

## <a name="next-steps"></a>다음 단계
* Azure IoT Hub 및 Azure IoT Hub Device Provisioning Service에서 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)를 사용하여 개발

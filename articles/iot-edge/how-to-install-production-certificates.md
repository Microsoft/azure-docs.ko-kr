---
title: 장치에 인증서 설치-Azure IoT Edge | Microsoft Docs
description: 테스트 인증서를 만들어 프로덕션 배포를 준비 하는 Azure IoT Edge 장치에 설치 하는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149901"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>IoT Edge 장치에 프로덕션 인증서 설치

모든 IoT Edge 장치는 인증서를 사용 하 여 런타임과 장치에서 실행 되는 모듈 간에 보안 연결을 만듭니다.
게이트웨이로 작동 하는 IoT Edge 장치는 이러한 동일한 인증서를 사용 하 여 다운스트림 장치에도 연결 합니다.

IoT Edge를 처음 설치 하 고 장치를 프로 비전 할 때 서비스를 테스트할 수 있도록 장치가 임시 인증서로 설정 됩니다.
이러한 임시 인증서는 90 일 후에 만료 되거나 컴퓨터를 다시 시작 하 여 다시 설정할 수 있습니다.
장치를 프로덕션 시나리오로 이동할 준비가 되었거나 게이트웨이 시나리오를 만들려는 경우 고유한 인증서를 제공 해야 합니다.
이 문서에서는 IoT Edge 장치에 인증서를 설치 하는 단계를 보여 줍니다.

IoT Edge 시나리오에서 다양 한 유형의 인증서 및 해당 역할에 대해 자세히 알아보려면 [Azure IoT Edge 인증서를 사용 하는 방법 이해](iot-edge-certs.md)를 참조 하세요.

>[!NOTE]
>이 문서 전체에서 사용 되는 "루트 CA" 라는 용어는 IoT 솔루션에 대 한 인증서 체인의 최상위 권한 공용 인증서를 나타냅니다. 게시 된 인증 기관의 인증서 루트나 조직의 인증 기관 루트를 사용할 필요는 없습니다. 대부분의 경우에는 실제로 중간 CA 공용 인증서입니다.

## <a name="prerequisites"></a>필수 조건

* [Windows](how-to-install-iot-edge-windows.md) 또는 [Linux](how-to-install-iot-edge-linux.md)에서 실행 되는 IoT Edge 장치
* 루트 CA (인증 기관) 인증서가 Baltimore, Verisign, DigiCert 또는 GlobalSign과 같은 신뢰할 수 있는 상용 인증 기관에서 구매한 CA (인증 기관) 인증서가 있어야 합니다.

루트 인증 기관이 아직 없지만 프로덕션 인증서 (예: 게이트웨이 시나리오)가 필요한 IoT Edge 기능을 사용해 보려는 경우 [데모 인증서를 만들어 IoT Edge 장치 기능을 테스트할](how-to-create-test-certificates.md)수 있습니다.

## <a name="create-production-certificates"></a>프로덕션 인증서 만들기

사용자 고유의 인증 기관을 사용 하 여 다음 파일을 만들어야 합니다.

* 루트 CA
* 디바이스 CA 인증서
* 장치 CA 개인 키

이 문서에서 *루트 CA* 를 참조 하는 것은 조직에 대 한 최상위 인증 기관이 아닙니다. IoT Edge 시나리오에 대 한 가장 높은 인증 기관으로, IoT Edge 허브 모듈, 사용자 모듈 및 모든 다운스트림 장치에서 서로 트러스트를 설정 하는 데 사용 합니다.

이러한 인증서의 예를 보려면 예제 [및 자습서에 대 한 테스트 CA 인증서 관리](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)에서 데모 인증서를 만드는 스크립트를 검토 합니다.

## <a name="install-certificates-on-the-device"></a>장치에 인증서 설치

IoT Edge 장치에 인증서 체인을 설치 하 고 새 인증서를 참조 하도록 IoT Edge 런타임을 구성 합니다.

예를 들어 샘플 스크립트를 사용 하 여 [데모 인증서를 만든](how-to-create-test-certificates.md)경우 IoT Edge 장치에 복사 해야 하는 세 가지 파일은 다음과 같습니다.

* 장치 CA 인증서: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 장치 CA 개인 키: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 루트 CA: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 3 개의 인증서 및 키 파일을 IoT Edge 장치에 복사 합니다.

   [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 와 같은 서비스를 사용 하거나 [보안 복사 프로토콜과](https://www.ssh.com/ssh/scp/) 같은 기능을 사용 하 여 인증서 파일을 이동할 수 있습니다.  IoT Edge 장치 자체에서 인증서를 생성 한 경우이 단계를 건너뛰고 작업 디렉터리에 대 한 경로를 사용할 수 있습니다.

2. IoT Edge 보안 디먼 구성 파일을 엽니다.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Config.xml 파일의 **인증서** 속성을 IoT Edge 장치의 인증서 및 키 파일에 대 한 파일 URI로 설정 합니다. 네 줄의 주석 처리를 제거 하려면 인증서 속성 앞의 `#` 문자를 제거 합니다. **인증서:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다. 예를 들면 다음과 같습니다.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Linux 장치에서 사용자 **iotedge** 에 인증서가 포함 된 디렉터리에 대 한 읽기 권한이 있는지 확인 합니다.

5. 이전에 장치에서 IoT Edge 하는 데 다른 인증서를 사용한 경우 IoT Edge를 시작 하거나 다시 시작 하기 전에 다음 두 디렉터리에서 파일을 삭제 합니다.

   * Windows: `C:\ProgramData\iotedge\hsm\certs` 및 `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` 및 `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>다음 단계

프로덕션 환경에 솔루션을 배포 하기 전에 IoT Edge 장치에 인증서를 설치 해야 합니다. [프로덕션 환경에서 IoT Edge 솔루션 배포를 준비](production-checklist.md)하는 방법에 대해 자세히 알아보세요.

---
title: 프로덕션용 디바이스 및 배포 준비 - Azure IoT Edge | Microsoft Docs
description: 적절한 인증서를 사용하여 디바이스 설정, 향후 코드 업데이트를 위한 배포 계획 수립 등 Azure IoT Edge 솔루션을 개발에서 프로덕션으로 전환하는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c64db6b35aa2f1daa4484f137c8505b1415c5a0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998458"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>IoT Edge 솔루션을 프로덕션 단계에서 배포하도록 준비

IoT Edge 솔루션을 개발에서 프로덕션으로 전환할 준비가 되었으면 지속적인 성능 유지가 가능하도록 구성되었는지 확인합니다.

이 문서에서 다루는 정보의 중요도가 모두 같지는 않습니다. 우선 순위를 정하는 데 도움이 되도록 각 섹션의 시작 부분에 작업이 두 가지 범주로 구분되어 나열되어 있습니다. **중요**에 해당하는 작업은 프로덕션으로 전환하기 전에 완료해야 하며, **유용함**에 해당하는 작업은 참조용으로 알아두면 좋습니다.

## <a name="device-configuration"></a>디바이스 구성

IoT Edge 디바이스는 Raspberry Pi부터 노트북, 서버에서 실행되는 가상 머신에 이르기까지 모든 것을 망라할 수 있습니다. 물리적으로 또는 가상 연결을 통해 디바이스에 액세스할 수 있는 경우도 있고, 디바이스가 오랜 기간 동안 격리되었을 수도 있습니다. 어느 쪽이든 간에, 적절한 성능을 유지하도록 구성되었는지 확인하는 것이 좋습니다. 

* **중요**
    * 프로덕션 인증서 설치
    * 디바이스 관리 계획 준비
    * Moby를 컨테이너 엔진으로 사용

* **유용함**
    * 업스트림 프로토콜 선택

### <a name="install-production-certificates"></a>프로덕션 인증서 설치

프로덕션 단계의 모든 IoT Edge 디바이스에 디바이스 CA(인증 기관) 인증서가 설치되어 있어야 합니다. 설치된 CA 인증서는 config.yaml 파일에서 IoT Edge 런타임으로 선언됩니다. 개발 및 테스트를 쉽게 수행하기 위해, config.yaml 파일에서 선언된 인증서가 없는 경우 IoT Edge 런타임에서 임시 인증서를 만듭니다. 그러나 이러한 임시 인증서는 3개월 후에 만료되어 프로덕션 시나리오가 원활하게 진행될 수 없습니다. 

디바이스 CA 인증서의 역할을 이해하려면 [Azure IoT Edge에서 인증서를 사용하는 방법](iot-edge-certs.md)을 참조하세요.

IoT Edge 디바이스에 인증서를 설치하고 config.yaml 파일에서 해당 인증서를 참조하는 방법에 대한 자세한 내용은 [투명한 게이트웨이로 작동하도록 IoT Edge 디바이스 구성](how-to-create-transparent-gateway.md)을 참조하세요. 인증서를 구성하는 단계는 디바이스가 게이트웨이로 사용되는지 여부에 관계없이 동일합니다. 이 문서에서는 테스트 전용으로 샘플 인증서를 생성하는 스크립트를 다룹니다. 프로덕션에서는 이러한 샘플 인증서를 사용하지 마세요. 

### <a name="have-a-device-management-plan"></a>디바이스 관리 계획 준비

디바이스를 프로덕션으로 전환하기 전에 향후 업데이트를 관리할 방법을 준비해 두어야 합니다. IoT Edge 디바이스의 경우 업데이트할 구성 요소 목록에 다음이 포함될 수 있습니다.

* 디바이스 펌웨어
* 운영 체제 라이브러리
* Moby와 같은 컨테이너 엔진
* IoT Edge 디먼
* CA 인증서

IoT Edge 디먼을 업데이트하는 단계는 [IoT Edge 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요. 현재는 IoT Edge 디바이스에 물리적으로 또는 SSH를 통해 액세스해야만 IoT Edge 디먼을 업데이트할 수 있습니다. 업데이트할 디바이스가 많을 경우 업데이트 단계를 스크립트에 추가하거나 Ansible 등의 자동화 도구를 사용하여 대규모 업데이트를 수행하는 것이 좋습니다.

### <a name="use-moby-as-the-container-engine"></a>Moby를 컨테이너 엔진으로 사용

모든 IoT Edge 디바이스에 대한 필수 조건으로, 디바이스에 컨테이너 엔진이 있어야 합니다. 프로덕션 단계에서는 Moby 엔진만 지원됩니다. Docker 등 IoT Edge와 호환되는 다른 컨테이너 엔진은 개발 단계에서 사용할 수 있습니다. Moby 엔진은 Azure IoT Edge와 함께 사용할 때 재배포할 수 있으며, Microsoft에서 이 엔진에 대한 서비스를 제공합니다. IoT Edge 디바이스에서 다른 컨테이너 엔진을 사용하는 것은 지원되지 않습니다.

### <a name="choose-upstream-protocol"></a>업스트림 프로토콜 선택

IoT Hub에 대한 업스트림 통신 프로토콜(및 사용되는 포트)을 Edge 에이전트와 Edge 허브 둘 다에 대해 구성할 수 있습니다. 기본 프로토콜은 AMQP이지만 네트워크 설정에 따라 변경할 수 있습니다. 

두 개의 런타임 모듈에 모두 **UpstreamProtocol** 환경 변수가 있습니다. 변수에 유효한 값은 다음과 같습니다. 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

디바이스 자체의 config.yaml 파일에서 Edge 에이전트에 대한 UpstreamProtocol 변수를 구성합니다. 예를 들어 IoT Edge 디바이스가 AMQP 포트를 차단하는 프록시 서버 뒤에 있는 경우 AMQPWS(WebSocket을 통한 AMQP)를 사용하여 IoT Hub에 대한 초기 연결을 설정하도록 Edge 에이전트를 구성해야 할 수 있습니다. 

IoT Edge 디바이스가 연결된 다음, 향후 배포에서 두 런타임 모듈에 대한 UpstreamProtocol 변수를 계속 구성해야 합니다. 이 프로세스의 예제는 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)에 나와 있습니다.

## <a name="deployment"></a>배포

* **유용함**
    * 업스트림 프로토콜의 일관성 유지
    * Edge 허브에서 사용하는 메모리 공간 축소
    * 모듈 이미지의 디버그 버전 사용 안 함

### <a name="be-consistent-with-upstream-protocol"></a>업스트림 프로토콜의 일관성 유지

IoT Edge 디바이스에서 기본 AMQP가 아닌 다른 프로토콜을 사용하도록 Edge 에이전트를 구성한 경우 모든 후속 배포에서 동일한 프로토콜을 선언해야 합니다. 예를 들어 IoT Edge 디바이스가 AMQP 포트를 차단하는 프록시 서버 뒤에 있는 경우 AMQPWS(WebSocket을 통한 AMQP)를 통해 연결하도록 디바이스를 구성했을 수 있습니다. 디바이스에 모듈을 배포할 때 Edge 에이전트 및 Edge 허브에 대해 동일한 APQPWS 프로토콜을 구성하지 않으면 기본 AMQP가 설정을 재정의하여 다시 연결할 수 없게 됩니다. 

Edge 에이전트 및 Edge 허브 모듈에 대한 UpstreamProtocol 환경 변수만 구성하면 됩니다. 모든 추가 모듈은 런타임 모듈에 설정된 프로토콜을 채택합니다. 

이 프로세스의 예제는 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)에 나와 있습니다.

### <a name="reduce-memory-space-used-by-edge-hub"></a>Edge 허브에서 사용하는 메모리 공간 축소

사용 가능한 메모리가 제한된 디바이스를 배포하는 경우, 실행되는 메모리 사용량을 줄이고 디스크 공간을 덜 사용하도록 Edge 허브를 구성할 수 있습니다. 그러나 이러한 구성은 Edge 허브의 성능을 제한하므로, 사용자 솔루션에 적합하게 균형을 맞추세요. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>리소스가 제한된 디바이스에서 성능에 최적화하지 않음

Edge 허브는 기본적으로 성능에 최적화되어 있으므로 큰 메모리 청크를 할당하려고 합니다. 이 구성으로 인해 Raspberry Pi와 같은 작은 디바이스에서 안정성 문제가 발생할 수 있습니다. 리소스가 제한된 디바이스를 배포하는 경우 Edge 허브에서 **OptimizeForPerformance** 환경 변수를 **false**로 설정하는 것이 좋습니다. 

자세한 내용은 [리소스가 제한된 디바이스의 안정성 문제](troubleshoot.md#stability-issues-on-resource-constrained-devices)를 참조하세요.

#### <a name="disable-unused-protocols"></a>사용하지 않는 프로토콜 비활성화

Edge 허브의 성능을 최적화하고 메모리 사용량을 줄이는 또 다른 방법은 솔루션에서 사용하지 않는 프로토콜의 헤드를 끄는 것입니다. 

프로토콜 헤드는 배포 매니페스트에서 Edge 허브 모듈에 대한 부울 환경 변수를 설정하여 구성합니다. 세 가지 변수는 다음과 같습니다.

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

세 변수에는 모두 *두 개의 밑줄*이 있으며 true 또는 false로 설정할 수 있습니다. 

#### <a name="reduce-storage-time-for-messages"></a>메시지의 스토리지 시간 축소

Edge 허브 모듈은 어떤 이유로든 IoT Hub에 배달할 수 없는 메시지를 임시로 저장합니다. Edge 허브가 만료되도록 허용하기 전에 배달하지 못한 메시지를 유지하는 기간을 구성할 수 있습니다. 디바이스에 메모리 관련 문제가 있는 경우 Edge 허브 모듈 쌍에서 **timeToLiveSecs** 값을 낮출 수 있습니다. 

timeToLiveSecs 매개 변수의 기본값은 7200초로, 2시간입니다. 

### <a name="do-not-use-debug-versions-of-module-images"></a>모듈 이미지의 디버그 버전 사용 안 함

테스트 시나리오에서 프로덕션 시나리오로 전환하는 경우 배포 매니페스트에서 디버그 구성을 제거해야 합니다. 배포 매니페스트에 **\.debug** 접미사가 있는 모듈 이미지가 없는지 확인합니다. 디버깅을 위해 모듈의 포트를 노출하는 만들기 옵션을 추가한 경우 해당 만들기 옵션도 제거합니다. 

## <a name="container-management"></a>컨테이너 관리

* **중요**
    * 컨테이너 레지스트리에 대한 액세스 관리
    * 태그를 사용하여 버전 관리

### <a name="manage-access-to-your-container-registry"></a>컨테이너 레지스트리에 대한 액세스 관리

프로덕션 IoT Edge 디바이스에 모듈을 배포하기 전에 외부 사용자가 컨테이너 이미지를 액세스하거나 변경할 수 없도록 컨테이너 레지스트리에 대한 액세스를 제어해야 합니다. 공용이 아닌 개인 컨테이너 레지스트리를 사용하여 컨테이너 이미지를 관리합니다. 

자습서 및 기타 설명서에서는 개발 머신에서 사용한 것과 동일한 컨테이너 레지스트리 자격 증명을 IoT Edge 디바이스에서 사용하도록 안내합니다. 이러한 지침은 테스트 및 개발 환경을 보다 쉽게 설정하도록 돕기 위한 것이며, 프로덕션 시나리오에서 따르면 안 됩니다. Azure Container Registry는 애플리케이션이나 서비스가 IoT Edge 디바이스처럼 자동화된 방식이나 기타 무인 방식으로 컨테이너 이미지를 끌어올 때 [서비스 주체를 사용한 인증](../container-registry/container-registry-auth-service-principal.md)을 권장합니다. 컨테이너 레지스트리에 대한 읽기 전용 권한을 가진 서비스 주체를 만들고 해당 사용자 이름 및 암호를 배포 매니페스트에 제공합니다.

### <a name="use-tags-to-manage-versions"></a>태그를 사용하여 버전 관리

태그는 docker 컨테이너의 버전 간을 구분 하는 데 사용할 수 있는 docker 개념입니다. 태그는 컨테이너 리포지토리의 끝에 추가되는 **1.0** 등의 접미사입니다. 예를 들어 **mcr.microsoft.com/azureiotedge-agent:1.0**입니다. 태그는 변경 가능하며, 언제든지 다른 컨테이너를 가리키도록 변경할 수 있으므로 앞으로 모듈 이미지를 업데이트할 때 따를 규칙에 대해 팀이 합의해야 합니다. 

또한 태그는 IoT Edge 디바이스에 업데이트를 적용하는 데 도움이 됩니다. 모듈의 업데이트된 버전을 컨테이너 레지스트리에 푸시할 때 태그를 증분합니다. 그런 다음, 태그가 증분된 새 배포를 디바이스에 푸시합니다. 컨테이너 엔진이 증분된 태그를 새 버전으로 인식하고 최신 모듈 버전을 디바이스로 끌어옵니다. 

태그 규칙의 예제 및 IoT Edge에서 롤링 태그와 특정 태그를 사용하여 버전을 추적하는 방법을 알아보려면 [IoT Edge 런타임 업데이트](how-to-update-iot-edge.md#understand-iot-edge-tags)를 참조하세요. 

## <a name="networking"></a>네트워킹

* **유용함**
    * 아웃바운드/인바운드 구성 검토
    * 허용 목록에 연결 추가
    * 프록시를 통한 통신 구성

### <a name="review-outboundinbound-configuration"></a>아웃바운드/인바운드 구성 검토

Azure IoT Hub과 IoT Edge 간의 통신 채널은 항상 아웃바운드로 구성됩니다. 대부분의 IoT Edge 시나리오에서는 세 개의 연결만 있으면 됩니다. 컨테이너 엔진은 모듈 이미지를 포함하는 컨테이너 레지스트리(한 개 또는 여러 개)에 연결해야 합니다. IoT Edge 런타임은 디바이스 구성 정보를 검색하고 메시지 및 원격 분석을 보내기 위해 IoT Hub에 연결해야 합니다. 또한 자동 프로비저닝을 사용하는 경우, IoT Edge 디먼은 디바이스 프로비저닝 서비스에 연결해야 합니다. 자세한 내용은 [방화벽 및 포트 구성 규칙](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)을 참조하세요.

### <a name="whitelist-connections"></a>허용 목록에 연결 추가

네트워킹 설정에 따라 IoT Edge 디바이스의 연결을 명시적으로 허용 목록에 추가해야 하는 경우 다음 IoT Edge 구성 요소 목록을 검토합니다.

* **IoT Edge 에이전트**는 WebSockets 등을 통해 IoT Hub에 대한 영구적 AMQP/MQTT 연결을 엽니다. 
* **IoT Edge 허브**는 WebSockets 등을 통해 IoT Hub에 대한 단일 영구적 AMQP 연결이나 여러 MQTT 연결을 엽니다. 
* **IoT Edge 디먼**은 IoT Hub에 대한 HTTPS 호출을 간헐적으로 수행합니다. 

세 가지 경우 모두, DNS 이름이 \*.azure-devices.net 패턴과 일치합니다. 

또한 **컨테이너 엔진**은 HTTPS를 통해 컨테이너 레지스트리를 호출합니다. IoT Edge 런타임 컨테이너 이미지를 검색하려면 DNS 이름이 mcr.microsoft.com입니다. 컨테이너 엔진은 배포에 구성된 대로 다른 레지스트리에 연결합니다. 

다음 검사 목록은 방화벽 규칙의 시작점입니다.

   | URL(\* = 와일드카드) | 아웃바운드 TCP 포트 | 사용 현황 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 컨테이너 레지스트리 |
   | global.azure-devices-provisioning.net  | 443 | DPS 액세스(선택 사항) |
   | \*.azurecr.io | 443 | 개인 및 타사 컨테이너 레지스트리 |
   | \*.blob.core.windows.net | 443 | 이미지 델타 다운로드 | 
   | \*.azure-devices.net | 5671, 8883, 443 | IoT Hub 액세스 |
   | \*.docker.io  | 443 | Docker 허브 액세스 (선택 사항) |

### <a name="configure-communication-through-a-proxy"></a>프록시를 통한 통신 구성

프록시 서버를 사용하는 네트워크에 사용자 디바이스를 배포하려는 경우 디바이스가 IoT Hub 및 컨테이너 레지스트리에 접근하기 위해 프록시를 통해 통신할 수 있어야 합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요.

## <a name="solution-management"></a>솔루션 관리

* **유용함**
    * 로그 및 진단 설정
    * 테스트 및 CI/CD 파이프라인 고려

### <a name="set-up-logs-and-diagnostics"></a>로그 및 진단 설정

Linux에서 IoT Edge 디먼 로깅 드라이버 기본값으로 저널을 사용 합니다. 명령줄 도구 `journalctl`을 사용하여 디먼 로그를 쿼리할 수 있습니다. Windows에서 IoT Edge 디먼은 PowerShell 진단을 사용합니다. `Get-WinEvent`를 사용하여 디먼 로그를 쿼리합니다. IoT Edge 모듈 드라이버를 사용할 JSON 로깅에 대 한 기본값입니다.  

IoT Edge 배포를 테스트할 때는 일반적으로 사용자 디바이스에 액세스하여 로그를 검색하고 문제를 해결할 수 있습니다. 배포 시나리오에서는 해당 옵션이 없을 수 있습니다. 프로덕션에서 사용자 디바이스에 대한 정보를 수집하는 방법을 고려해 보세요. 한 가지 옵션은 다른 모듈에서 정보를 수집하여 클라우드로 보내는 로깅 모듈을 사용하는 것입니다. 로깅 모듈의 한 가지 예는 [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)이며, 사용자가 직접 로깅 모듈을 설계할 수도 있습니다. 

### <a name="place-limits-on-log-size"></a>로그 크기 제한

기본적으로 모 비 컨테이너 엔진에는 컨테이너 로그 크기 제한을 설정 하지 않습니다. 시간이 지남에 따라이 장치 로그를 사용 하 여 가득 차지 하 고 디스크 공간 부족에 발생할 수 있습니다. 이 문제를 방지 하려면 다음 옵션을 고려 합니다.

**옵션: 컨테이너 모듈을 모두에 적용 되는 전역 제한 설정**

컨테이너 엔진 로그 옵션의 모든 컨테이너 로그 파일의 크기를 제한할 수 있습니다. 다음 예제는 로그 드라이버 설정 `json-file` 파일의 크기와 수에 제한이 있음 (권장):

    {
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "10m",
            "max-file": "3"
        }
    }

라는 파일에이 정보를 추가 (또는 추가) `daemon.json` 장치 플랫폼에 적합 한 위치에 놓습니다.

| 플랫폼 | 위치 |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby-data\config\` |

변경 내용을 적용 하려면 컨테이너 엔진 다시 시작 해야 합니다.

**옵션: 각 컨테이너 모듈에 대 한 로그 설정을 조정합니다**

수행할 수 있습니다 합니다 **createOptions** 각 모듈입니다. 예를 들면 다음과 같습니다.

    "createOptions": {
        "HostConfig": {
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "10m",
                    "max-file": "3"
                }
            }
        }
    }


**Linux 시스템에서 추가 옵션**

* 로그를 보낼 컨테이너 엔진 구성 `systemd` [저널](https://docs.docker.com/config/containers/logging/journald/) 설정 하 여 `journald` 기본 로깅 드라이버로 합니다. 

* Logrotate 도구를 설치 하 여 장치에서 이전 로그를 주기적으로 제거 합니다. 다음 파일 사양을 사용합니다. 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>테스트 및 CI/CD 파이프라인 고려

가장 효율적인 IoT Edge 배포 시나리오를 위해 프로덕션 배포를 테스트 및 CI/CD 파이프라인에 통합하는 것이 좋습니다. Azure IoT Edge는 Azure DevOps를 포함하여 여러 CI/CD 플랫폼을 지원합니다. 자세한 내용은 [Azure IoT Edge 연속 통합 및 지속적인 배포](how-to-ci-cd.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [IoT Edge 자동 배포](module-deployment-monitoring.md)에 대해 자세히 알아보세요.
* IoT Edge에서 [연속 통합 및 지속적인 배포](how-to-ci-cd.md)를 지원하는 방법을 확인합니다.

---
title: IoT Edge 보안 모듈 배포
description: IoT Edge에서 IoT 보안 에이전트에 대 한 Azure Security Center를 배포 하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 1c646c750cb54228211fadb0a4f6733d495e9219
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892068"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>IoT Edge 장치에 보안 모듈 배포

**IoT 모듈 Azure Security Center** 은 IoT Edge 장치에 대 한 포괄적인 보안 솔루션을 제공 합니다.
보안 모듈은 운영 체제 및 컨테이너 시스템에서 실행 가능한 보안 권장 사항 및 경고로 원시 보안 데이터를 수집, 집계 및 분석 합니다.
자세히 알아보려면 [IoT Edge 보안 모듈](security-edge-architecture.md)을 참조 하세요.

이 문서에서는 IoT Edge 장치에 보안 모듈을 배포 하는 방법을 알아봅니다.

## <a name="deploy-security-module"></a>보안 모듈 배포

다음 단계를 사용 하 여 IoT Edge 용 IoT 보안 모듈에 대 한 Azure Security Center를 배포 합니다.

### <a name="prerequisites"></a>전제 조건

1. IoT Hub에서 장치가 [IoT Edge 장치로 등록](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)되어 있는지 확인 합니다.

1. IoT Edge 모듈을 Azure Security Center 하려면 IoT Edge 장치에 [Auditd framework](https://linux.die.net/man/8/auditd) 가 설치 되어 있어야 합니다.

    - IoT Edge 장치에서 다음 명령을 실행 하 여 프레임 워크를 설치 합니다.

    `sudo apt-get install auditd audispd-plugins`

    - 다음 명령을 실행 하 여 AuditD가 활성 상태 인지 확인 합니다.

    `sudo systemctl status auditd`<br>
    - 예상 응답은 다음과 같습니다. `active (running)`

### <a name="deployment-using-azure-portal"></a>Azure Portal를 사용 하 여 배포

1. Azure Portal에서 **Marketplace**를 엽니다.

1. **사물 인터넷**을 선택한 다음, **IoT에 대 한 Azure Security Center** 를 검색 하 고 선택 합니다.

   ![IoT에 대 한 Azure Security Center 선택](media/howto/edge-onboarding-8.png)

1. **만들기** 를 클릭 하 여 배포를 구성 합니다.

1. IoT Hub의 Azure **구독** 을 선택한 다음 **IoT Hub**를 선택 합니다.<br>**장치에 배포** 를 선택 하 여 단일 장치를 대상으로 하거나, **대규모로 배포** 를 선택 하 여 여러 장치를 대상으로 하 고, **만들기**를 클릭 합니다. 대규모로 배포 하는 방법에 대 한 자세한 내용은 [배포 방법](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)을 참조 하세요.

    >[!Note]
    >**대규모로 배포**를 선택한 경우 다음 지침의 **모듈 추가** 탭으로 계속 하기 전에 장치 이름 및 세부 정보를 추가 합니다.

IoT에 대 한 Azure Security Center에 대 한 IoT Edge 배포를 완료 하려면 각 단계를 완료 합니다.

#### <a name="step-1-modules"></a>1 단계: 모듈

1. **AzureSecurityCenterforIoT** 모듈을 선택 합니다.
1. **모듈 설정** 탭에서 **이름을** **azureiotsecurity**로 변경 합니다.
1. **환경 변수** 탭에서 필요한 경우 변수를 추가 합니다 (예: 디버그 수준).
1. **컨테이너 만들기 옵션** 탭에서 다음 구성을 추가 합니다.

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. 모듈 쌍 **설정** 탭에서 다음 구성을 추가 합니다.

   모듈 쌍 속성:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   모듈 쌍 속성 내용: 

   ```json
     {

     }
   ```
    
   에이전트를 구성 하는 방법에 대 한 자세한 내용은 [보안 에이전트 구성](https://docs.microsoft.com/azure/asc-for-iot/how-to-agent-configuration)을 참조 하세요.

1. **업데이트**를 선택합니다.

#### <a name="step-2-runtime-settings"></a>2 단계: 런타임 설정

1. **런타임 설정**을 선택 합니다.
1. **Edge Hub**에서 **이미지** 를 **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**로 변경 합니다.
1. **만들기 옵션** 은 다음 구성으로 설정 되어 있는지 확인 합니다.

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. **저장**을 선택합니다.

1. **다음**을 선택합니다.

#### <a name="step-3-specify-routes"></a>3 단계: 경로 지정

1. **경로 지정** 탭에서 다음 예제에 따라 **azureiotsecurity** 모듈에서 **$upstream** 로 메시지를 전달 하는 경로 (명시적 또는 암시적)가 있는지 확인 합니다. 경로가 준비 된 경우에만 **다음**을 선택 합니다.

   경로 예:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. **다음**을 선택합니다.

#### <a name="step-4-review-deployment"></a>4 단계: 배포 검토

- 배포 **검토** 탭에서 배포 정보를 검토 한 다음 **만들기** 를 선택 하 여 배포를 완료 합니다.

## <a name="diagnostic-steps"></a>진단 단계

문제가 발생 하는 경우 컨테이너 로그가 IoT Edge 보안 모듈 장치의 상태에 대 한 정보를 확인 하는 가장 좋은 방법입니다. 이 섹션의 명령 및 도구를 사용하여 정보를 수집합니다.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>필요한 컨테이너가 설치 되어 있으며 예상 대로 작동 하는지 확인 합니다.

1. IoT Edge 장치에서 다음 명령을 실행 합니다.

    `sudo docker ps`

1. 다음 컨테이너가 실행 중인지 확인 합니다.

   | 이름 | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   필요한 최소 컨테이너가 없는 경우 IoT Edge 배포 매니페스트가 권장 설정에 맞게 정렬 되었는지 확인 합니다. 자세한 내용은 [배포 IoT Edge 모듈](#deployment-using-azure-portal)을 참조 하세요.

### <a name="inspect-the-module-logs-for-errors"></a>모듈 로그에서 오류 검사

1. IoT Edge 장치에서 다음 명령을 실행 합니다.

   `sudo docker logs azureiotsecurity`

1. 자세한 로그를 보려면 **azureiotsecurity** 모듈 배포에 다음 환경 변수를 추가 `logLevel=Debug` 합니다.

## <a name="next-steps"></a>다음 단계

구성 옵션에 대 한 자세한 내용을 보려면 모듈 구성 방법 가이드를 참조 하세요.
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)

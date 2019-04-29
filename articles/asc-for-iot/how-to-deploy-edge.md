---
title: IoT Edge 모듈에 대 한 Azure Security Center 배포 | Microsoft Docs
description: IoT Edge의 IoT 보안 에이전트에 대 한 Azure 보안 센터를 배포 하는 방법에 알아봅니다.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: d72980d6e27600cb844d5477d3b9a61d9e1573e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61358633"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>IoT Edge 장치의 보안 모듈을 배포 합니다.

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

**IoT 용 azure 보안 센터 (ASC)** 모듈은 IoT Edge 장치에 대 한 완벽 한 보안 솔루션을 제공 합니다.
보안 모듈 수집, 집계 및 실행 가능한 보안 권장 사항 및 경고에 운영 체제 및 컨테이너 시스템에서 원시 보안 데이터를 분석 합니다.
자세한 내용은 참조 하세요 [IoT Edge에 대 한 보안 모듈](security-edge-architecture.md)합니다.

이 가이드에서는 IoT Edge 장치의 보안 모듈을 배포 하는 방법을 알아봅니다.

## <a name="deploy-security-module"></a>보안 모듈 배포

IoT Edge에 대 한 IoT 보안 모듈에 대 한를 ASC를 배포 하려면 다음 단계를 사용 합니다.

### <a name="prerequisites"></a>필수 조건

- IoT Hub에서 장치 인지 확인 [IoT Edge 장치로 등록](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)합니다.

- IoT Edge 모듈에 대 한 ASC 필요 [AuditD framework](https://linux.die.net/man/8/auditd) IoT Edge 장치에 설치 됩니다.

    - IoT Edge 장치에서 다음 명령을 실행 하 여 프레임 워크를 설치 합니다.
   
      `sudo apt-get install auditd audispd-plugins`
   
    - 다음 명령을 실행 하 여 활성화 되어 AuditD 확인 합니다.
   
      `sudo systemctl status auditd`
      
        예상된 응답은 `active (running)`합니다. 

### <a name="deployment-using-azure-portal"></a>Azure portal을 사용 하 여 배포

1. Azure portal에서 엽니다 **Marketplace**합니다.

1. 선택 **사물 인터넷**를 검색 한 다음 **IoT 용 Azure Security Center** 선택 합니다.

   ![IoT 용 Azure Security Center 선택](media/howto/edge-onboarding-8.png)

1. 클릭 **만들기** 배포를 구성 합니다. 

1. Azure를 선택 **구독** IoT Hub의 선택한 하 **IoT Hub**합니다.<br>선택 **장치에 배포** 단일 장치를 대상 또는 선택 **대규모로 배포** 클릭 하 여 여러 장치를 대상 **만들기**합니다. 대규모 배포에 대 한 자세한 내용은 참조 하세요. [배포 하는 방법을](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)합니다. 

    >[!Note] 
    >선택한 경우 **대규모로 배포**을 계속 하기 전에 장치 이름 및 세부 정보를 추가 합니다 **모듈 추가** 다음 지침에는 탭 합니다.     

IoT 용 Azure Security Center에 대 한 IoT Edge 배포를 만드는 세 가지 단계가 있습니다. 다음 섹션에서는 각 단계로 안내합니다. 

#### <a name="step-1-add-modules"></a>1단계: 모듈 추가

1. **모듈 추가** 탭 **배포 모듈** 영역에서 클릭 **AzureSecurityCenterforIoT**합니다. 
   
1. 변경 된 **이름을** 하 **azureiotsecurity**합니다.
1. 변경 된 **이미지 URI** 하 **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3**합니다.
1. 확인 합니다 **컨테이너 만들기 옵션** 값으로 설정 됩니다.      
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
1. 확인 **집합 모듈 쌍의 desired 속성** 을 선택 하 고 구성 개체를 변경 합니다.
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. **저장**을 클릭합니다.
1. 선택한 탭의 아래쪽으로 스크롤하여 **고급 Edge 런타임 설정 구성**합니다.
   
   >[!Note]
   > 수행할 **되지** IoT Edge 허브에 대 한 AMQP 통신을 사용 하지 않도록 설정 합니다.
   > IoT 모듈에 대 한 azure Security Center에는 IoT Edge Hub를 사용 하 여 AMQP 통신이 필요 합니다.
   
1. 변경 합니다 **이미지** 아래에서 **Edge Hub** 에 **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview**합니다.

   >[!Note]
   > IoT 모듈에 대 한 azure Security Center에는 SDK 버전 1.20 기반 IoT Edge 허브의 분기 된 버전을 필요 합니다.
   > IoT Edge 허브 이미지를 변경 하 여 IoT Edge 장치의 IoT Edge 허브는 IoT Edge 서비스에서 공식적으로 지원 되지 않는 분기 된 버전을 사용 하 여 안정적인 최신 릴리스를 바꾸려면 지시 하 게 됩니다.

1. 확인할 **만들기 옵션** 로 설정 됩니다. 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. **저장**을 클릭합니다.
   
1. **다음**을 클릭합니다.

#### <a name="step-2-specify-routes"></a>2단계: 경로 지정 

1. 에 **경로 지정** 탭, 설정 합니다 **ASCForIoTToIoTHub** 라우팅할 **"/ 메시지/모듈/azureiotsecurity/에서\* $ 업스트림"**, 클릭 **다음**합니다.

   ![경로 지정](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>3단계: 배포 검토

1. 에 **배포 검토** 탭 차례로 배포 정보를 검토 한 다음 선택 **제출** 배포를 완료 하려면.

## <a name="diagnostic-steps"></a>진단 단계

문제가 발생 하는 경우 컨테이너 로그는 가장 좋은 방법은 IoT Edge 보안 모듈 장치의 상태에 알아봅니다. 이 섹션의 명령 및 도구를 사용하여 정보를 수집합니다.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>필요한 컨테이너 설치 되어 예상 대로 작동 확인

1. IoT Edge 장치에서 다음 명령을 실행 합니다.
    
     `sudo docker ps`
   
1. 다음 컨테이너 실행 되 고 있는지 확인 합니다.
   
   | 이름 | IMAGE |
   | --- | --- |
   | azureIoTSecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   필요한 최소 컨테이너가 없는 경우에 권장된 설정을 사용 하 여 IoT Edge 배포 매니페스트를 맞춥니다 확인 합니다. 자세한 내용은 [IoT Edge 배포 모듈](#deployment-using-azure-portal)합니다.

### <a name="inspect-the-module-logs-for-errors"></a>로그에서 모듈 오류를 검사 합니다.
   
1. IoT Edge 장치에서 다음 명령을 실행 합니다.

   `sudo docker logs azureiotsecurity`
   
1. 더 자세한 로그에 대 한 다음 환경 변수를 추가 **azureiotsecurity** 모듈 배포: `logLevel=Debug`합니다.

## <a name="next-steps"></a>다음 단계

구성 옵션에 대 한 자세한 내용은 모듈 구성에 대 한 방법 가이드를 계속 합니다. 
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)

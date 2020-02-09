---
title: 문제 해결-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid 문제 해결
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100150"
---
# <a name="common-issues"></a>일반 문제

사용자 환경에서 IoT Edge에서 Azure Event Grid를 사용 하는 데 문제가 발생 하는 경우 문제 해결 및 해결을 위한 가이드로이 문서를 사용 합니다.

## <a name="view-event-grid-module-logs"></a>Event Grid 모듈 로그 보기

문제를 해결 하려면 Event Grid 모듈 로그에 액세스 해야 할 수 있습니다. 이렇게 하려면 모듈이 배포 된 VM에서 다음 명령을 실행 합니다.

Windows에서는

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Linux에서

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>HTTPS 요청을 수행할 수 없습니다.

* 먼저 Event Grid 모듈이 **인바운드: serverAuth: tlsPolicy** 를 **strict** 또는 **enabled**로 설정 했는지 확인 합니다.

* 모듈 간 통신의 경우 포트 **4438** 에 대 한 호출을 수행 하 고 모듈의 이름이 배포 된 항목과 일치 하는지 확인 합니다. 

  예를 들어, 이름이 **eventgridmodule** 인 Event Grid 모듈이 배포 된 경우 URL을 **https://eventgridmodule:4438** 해야 합니다. 대/소문자 및 포트 번호가 올바른지 확인 합니다.
    
* 비 IoT 모듈의 경우 배포 중에 Event Grid 포트가 호스트 컴퓨터에 매핑되는지 확인 합니다. 예를 들어

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>HTTP 요청을 만들 수 없습니다.

* 먼저 Event Grid 모듈에 **인바운드: serverAuth: tlsPolicy** 가 **사용** 또는 사용 **안 함으로**설정 되어 있는지 확인 합니다.

* 모듈 간 통신의 경우 포트 **5888** 에 대 한 호출을 수행 하 고 모듈의 이름이 배포 된 항목과 일치 하는지 확인 합니다. 

  예를 들어, 이름이 **eventgridmodule** 인 Event Grid 모듈이 배포 된 경우 URL을 **http://eventgridmodule:5888** 해야 합니다. 대/소문자 및 포트 번호가 올바른지 확인 합니다.
    
* 비 IoT 모듈의 경우 배포 중에 Event Grid 포트가 호스트 컴퓨터에 매핑되는지 확인 합니다. 예를 들어

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>신뢰 하지 않는 기관에서 인증서 체인을 발급 했습니다.

기본적으로 Event Grid 모듈은 IoT Edge 보안 데몬에 의해 발급 된 인증서를 사용 하 여 클라이언트를 인증 하도록 구성 됩니다. 클라이언트에서이 체인을 기반으로 하는 인증서를 제공 하는지 확인 합니다.

[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) 의 **iotsecurity** 클래스는 IoT Edge 보안 디먼에서 인증서를 검색 하 고이를 사용 하 여 나가는 호출을 구성 하는 방법을 보여 줍니다.

비프로덕션 환경인 경우 클라이언트 인증을 해제할 수 있는 옵션이 있습니다. 이 작업을 수행 하는 방법에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md) 을 참조 하세요.

## <a name="debug-events-not-received-by-subscriber"></a>구독자가 받은 디버그 이벤트

일반적인 이유는 다음과 같습니다.

* 이벤트가 성공적으로 게시 되지 않았습니다. Event Grid 모듈에 이벤트를 게시할 때 HTTP StatusCode (OK)를 받았습니다.

* 다음을 확인 하려면 이벤트 구독을 확인 하십시오.
    * 엔드포인트 URL이 유효 합니다.
    * 구독의 모든 필터에서 이벤트를 "삭제" 하지 않습니다.

* 구독자 모듈이 실행 중인지 확인 합니다.

* Event Grid 모듈이 배포 된 VM에 로그온 하 여 해당 로그를 확인 합니다.

* **Broker: logDeliverySuccess = true** 를 설정 하 고 Event Grid 모듈을 다시 배포 하 고 요청을 다시 시도 하 여 배달 로깅을 설정 합니다. 배달 당 로깅을 켜면 처리량 및 대기 시간에 영향을 줄 수 있으므로 디버깅이 완료 되 면 다시 **broker: logDeliverySuccess = false** 로 설정 하 고 Event Grid 모듈을 다시 배포 하는 것이 좋습니다.

* **메트릭: reportertype = console** 을 설정 하 여 메트릭을 켜고 모듈 Event Grid 다시 배포 합니다. 그 다음의 작업을 수행 하면 추가 디버깅에 사용할 수 있는 Event Grid 모듈의 콘솔에 메트릭이 기록 됩니다. 메트릭을 사용 하 여 디버깅을 수행 하 고 완료 한 후에는 **메트릭: reportertype = none** 을 설정 하 고 Event Grid module을 다시 배포 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)의 IoT Edge에 대 한 Event Grid를 사용 하 여 모든 문제를 보고 합니다.
---
title: IoT Edge 모듈을 통해 Azure Stack Edge GPU에 Kubernetes 상태 비저장 앱 배포 | Microsoft Docs
description: 외부 IP를 통해 액세스 되는 IoT Edge 모듈을 사용 하 여 Azure Stack Edge GPU 장치에 Kubernetes 상태 비저장 응용 프로그램을 배포 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 15316cbdd44053bfaf7403815ba42d92e2264b7b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254171"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-gpu-device"></a>IoT Edge 모듈을 사용 하 여 Azure Stack Edge GPU 장치에서 Kubernetes 상태 비저장 응용 프로그램을 실행 합니다.

이 문서에서는 IoT Edge 모듈을 사용 하 여 Azure Stack에 지 장치에 상태 비저장 응용 프로그램을 배포 하는 방법을 설명 합니다.

상태 비저장 응용 프로그램을 배포 하려면 다음 단계를 수행 합니다.

- IoT Edge 모듈을 배포 하기 전에 필수 구성 요소가 완료 되었는지 확인 합니다.
- Azure Stack Edge에서 계산 네트워크에 액세스 하는 IoT Edge 모듈을 추가 합니다.
- 모듈이 사용 가능한 네트워크 인터페이스에 액세스할 수 있는지 확인 합니다.

이 방법 문서에서는 웹 서버 앱 모듈을 사용 하 여 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음이 필요 합니다.

- Azure Stack Edge 장치입니다. 확인할 사항은 다음과 같습니다.

    - 장치에서 Compute 네트워크 설정이 구성 됩니다.
    - 장치는 [자습서: 장치 활성화](azure-stack-edge-gpu-deploy-activate.md)의 단계에 따라 활성화 됩니다.
- [자습서: 장치에서 Azure Stack Edge 장치에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md) 을 기준으로 계산 단계를 **구성** 했습니다. 장치에 연결 된 IoT Hub 리소스, IoT 장치 및 IoT Edge 장치가 있어야 합니다.


## <a name="add-webserver-app-module"></a>웹 서버 앱 모듈 추가

다음 단계를 수행 하 여 Azure Stack Edge 장치에 웹 서버 앱 모듈을 추가 합니다.

1. 장치와 연결 된 IoT Hub 리소스에서 **> IoT Edge 자동 장치 관리**로 이동 합니다.
1. Azure Stack에 지 장치와 연결 된 IoT Edge 장치를 선택 하 고 클릭 합니다. 

    ![IoT Edge 장치 선택](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. **모듈 설정**을 선택합니다. **장치에서 모듈 설정**에서 **+ 추가** 를 선택 하 고 **모듈 IoT Edge**선택 합니다.

    ![IoT Edge 모듈 선택](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. **추가 IoT Edge 모듈**에서 다음을 수행 합니다.

    1. 배포 하려는 웹 서버 앱 모듈의 **이름을** 지정 합니다.
    2. **모듈 설정** 탭에서 모듈 이미지에 대 한 **이미지 URI** 를 제공 합니다. 제공 된 이름 및 태그와 일치 하는 모듈이 검색 됩니다. 이 경우는 `nginx:stable` 공용 [Docker 리포지토리에서](https://hub.docker.com/_/nginx/)안정적인 nginx 이미지 (안정적인 것으로 태그가 지정 됨)를 끌어옵니다.

        ![IoT Edge 모듈 추가](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. **컨테이너 만들기 옵션** 탭에서 다음 샘플 코드를 붙여넣습니다.  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        이 구성을 통해 TCP 포트 8080에서 *http* 를 통한 COMPUTE 네트워크 IP를 사용 하 여 모듈에 액세스할 수 있습니다 (기본 웹 서버 포트는 80로 설정 됨). **추가**를 선택합니다.

        ![IoT Edge 사용자 지정 모듈 블레이드에서 포트 정보를 지정 합니다.](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. **검토 + 만들기**를 선택합니다. 모듈 세부 정보를 검토 하 고 **만들기**를 선택 합니다.

## <a name="verify-module-access"></a>모듈 액세스 확인

1. 모듈이 성공적으로 배포 되 고 실행 중인지 확인 합니다. **모듈 탭에서** 모듈의 런타임 상태가 **실행 중**이어야 합니다.  

    ![모듈 상태가 실행 중인지 확인 합니다.](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. 웹 서버 앱의 외부 끝점을 가져오려면 [Kubernetes 대시보드에 액세스](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard)합니다. 
1. 대시보드의 왼쪽 창에서 **iotedge** 네임 스페이스로 필터링 합니다. **검색 및 부하 분산 > 서비스로**이동 합니다. 나열 된 서비스 목록에서 웹 서버 앱 모듈의 외부 끝점을 찾습니다. 

    ![외부 끝점에서 웹 서버 앱에 연결](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. 외부 끝점을 선택 하 여 새 브라우저 창을 엽니다.

    웹 서버 앱이 실행 되 고 있는 것을 볼 수 있습니다.

    ![지정 된 포트를 통해 모듈에 대 한 연결 확인](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>다음 단계

- IoT Edge 모듈을 통해 상태 저장 응용 프로그램을 노출 하는 방법을 알아봅니다.<!--insert link-->.

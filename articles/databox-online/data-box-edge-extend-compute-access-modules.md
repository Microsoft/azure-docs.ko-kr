---
title: 액세스 모듈을 Azure 데이터 상자 가장자리에서 네트워크를 계산 관리 | Microsoft Docs
description: 모듈 외부 IP 통해 액세스 하 여 데이터 상자 가장자리 계산 네트워크를 확장 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65917237"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>계산 하 여 Azure 데이터 상자 Edge 네트워크를 사용 하도록 설정

이 문서에서는 실행 하 여 Azure 데이터 상자 Edge에서 모듈 계산 네트워크 장치에서 사용 하도록 설정에 액세스 하는 방법을 설명 합니다.

네트워크를 구성 하려면 다음 단계를 수행 하겠습니다.

- 계산에 대한 데이터 상자 Edge 장치에서 네트워크 인터페이스를 사용 하도록 설정
- 모듈을 추가 하 여 데이터 상자 Edge 계산 네트워크 액세스
- 모듈을 사용할 수 있는 네트워크 인터페이스에 액세스할 수 확인

이 자습서에서는 시나리오를 보여 주기 위해 웹 서버 앱 모듈을 사용 하겠습니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에 필요 합니다.

- 장치 설치 완료를 데이터 상자 Edge 장치입니다.
- 마친 **계산 구성** 당 단계는 [자습서: Azure 데이터 상자 Edge를 사용 하 여 데이터 변환](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) 장치의 합니다. 장치는 연결된 된 IoT Hub 리소스는 IoT 장치 및 IoT Edge 장치에 있어야 합니다.

## <a name="enable-network-interface-for-compute"></a>계산에 대한 네트워크 인터페이스를 사용 하도록 설정

외부 네트워크를 통해 장치에서 실행 되는 모듈에 액세스 하려면 장치에서 네트워크 인터페이스에 IP 주소를 할당 해야 합니다. 로컬 웹 UI에서 이러한 컴퓨팅 설정을 관리 할 수 있습니다.

로컬 웹 UI에서 다음 단계를 수행하여 컴퓨팅 설정을 구성합니다.

1. 로컬 웹 UI에서 **구성 > 컴퓨팅 설정**으로 이동합니다.  

2. **사용 하도록 설정** 사용 하 여 장치에서 실행 하는 계산 모듈에 연결 하려는 네트워크 인터페이스.

    - 고정 IP 주소를 사용하는 경우 네트워크 인터페이스의 IP 주소를 입력합니다.
    - DHCP를 사용 하는 경우 IP 주소를 자동으로 할당 됩니다. 이 예제에서는 DHCP를 사용 합니다.

    ![계산 설정 1을 사용 하도록 설정](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. **적용**을 선택하여 설정을 적용합니다. DHCP를 사용 하는 경우 네트워크 인터페이스에 할당 된 IP 주소를 기록해 둡니다.

    ![컴퓨팅 설정 사용](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>웹 서버 앱 모듈 추가

데이터 상자 Edge 장치에서 웹 앱 모듈을 추가 하려면 다음 단계를 수행 합니다.

1. 데이터 상자 Edge 장치에 연결 된 IoT Hub 리소스로 이동 하 고 선택한 **IoT Edge 장치**합니다.
2. 데이터 상자 Edge 장치에 연결 된 IoT Edge 장치를 선택 합니다. 에 **장치 세부 정보**를 선택 **모듈 설정**합니다. 온 **모듈을 추가**를 선택 **+ 추가** 선택한 후 **IoT Edge 모듈**합니다.
3. 에 **IoT Edge 사용자 지정 모듈** 블레이드에서:

    1. 지정 된 **이름을** 배포 하려는 웹 서버 앱 모듈에 대한 합니다.
    2. 제공 된 **이미지 URI** 모듈 이미지에 대한 합니다. 제공 된 이름 및 태그와 일치 하는 모듈 검색 됩니다. 이 예에서 `nginx:stable` 으로부터 (안정적으로 태그 지정) 하는 안정적인 nginx 이미지를 끌어오면 [Docker 리포지토리](https://hub.docker.com/_/nginx/)합니다.
    3. 에 **컨테이너 만들기 옵션**, 다음 샘플 코드를 붙여 넣습니다.  

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

        이 구성을 통해 계산 네트워크 IP를 사용 하 여 모듈에 액세스할 수 있습니다 *http* TCP 포트에서 8080 (사용 하 여 기본 웹 서버 80 중인 포트).

        ![IoT Edge 사용자 지정 모듈 블레이드에서 포트 정보를 지정 합니다.](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. **저장**을 선택합니다.

## <a name="verify-module-access"></a>모듈 액세스 확인

1. 모듈 성공적으로 배포 되 고 실행 중인지 확인 합니다. 에 **장치 세부 정보** 페이지를 **모듈** 탭, 모듈의 런타임 상태를 해야 **실행**합니다.  
2. 웹 서버 앱 모듈에 연결 합니다. 브라우저 창을 열고 유형:

    `http://<compute-network-IP-address>:8080`

    웹 앱이 실행 되는 표시 됩니다.

    ![지정 된 포트를 통해 모듈에 대한 연결을 확인 합니다.](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 사용자를 관리](data-box-edge-manage-users.md)하는 방법을 알아봅니다.

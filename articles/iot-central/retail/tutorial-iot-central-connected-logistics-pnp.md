---
title: IoT 커넥티드 물류 자습서 | Microsoft Docs
description: IoT Central에 대한 커넥티드 물류 애플리케이션 템플릿 자습서
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: d7050cfdae2c726a3a5bd44b4399792a400572a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025523"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>자습서: 커넥티드 물류 애플리케이션 템플릿 배포 및 살펴보기



이 자습서에서는 IoT Central **커넥티드 물류** 애플리케이션 템플릿을 배포하여 시작하는 방법을 보여 줍니다. 템플릿을 배포하는 방법, 기본적으로 제공되는 항목 및 다음에 수행할 수 있는 작업을 알아봅니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

* 커넥티드 물류 애플리케이션 만들기
* 애플리케이션 살펴보기 

## <a name="prerequisites"></a>사전 요구 사항

* 이 앱을 배포하는 데 필요한 특정 필수 구성 요소가 없습니다.
* Azure 구독이 있는 것이 좋지만, 없더라도 시도해 볼 수 있습니다.

## <a name="create-connected-logistics-application-template"></a>커넥티드 물류 애플리케이션 만들기 템플릿

다음 단계를 사용하여 애플리케이션을 만들 수 있습니다.

1. Azure IoT Central 애플리케이션 관리자 웹 사이트로 이동합니다. 왼쪽 탐색 모음에서 **빌드**를 선택하고 **소매** 탭을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. **커넥티드 물류 애플리케이션**에서 **앱 만들기**를 선택합니다.

3. **앱 만들기**를 선택하면 아래와 같이 새 애플리케이션 양식이 열리고 요청된 세부 정보가 입력됩니다.
   * **애플리케이션 이름**: 제안된 기본 이름을 사용하거나 친숙한 애플리케이션 이름을 입력할 수 있습니다.
   * **URL**: 제안된 기본 URL을 사용하거나 기억하기 쉬운 고유한 URL을 입력할 수 있습니다. 다음으로, Azure 구독이 이미 있는 경우 기본 설정이 권장됩니다. 7일 무료 평가판 가격 책정 플랜으로 시작하여 평가판이 만료되기 전에 언제든지 표준 가격 책정 플랜으로 전환할 수 있습니다.
   * **청구 정보**: 리소스를 프로비저닝하려면 디렉터리, Azure 구독 및 지역 세부 정보가 필요합니다.
   * **만들기**: 페이지 맨 아래에서 만들기를 선택하여 애플리케이션을 배포합니다.

    > [!div class="mx-imgBorder"]
    > ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![연결된 물류 청구 정보](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>애플리케이션 살펴보기 

## <a name="dashboard"></a>대시보드

앱 템플릿을 성공적으로 배포하면 기본 대시보드는 커넥티드 물류 운영자 중심 포털이 됩니다. Northwind Traders는 해상이나 육지에서 화물 차량군을 관리하는 가상 물류 공급자입니다. 이 대시보드에서는 관련된 명령, 직무 및 수행할 수 있는 작업과 함께 배송에 대한 원격 분석을 제공하는 두 개의 다른 게이트웨이를 볼 수 있습니다. 이 대시보드는 중요한 물류 디바이스 작업 활동을 보여 주기 위해 미리 구성되어 있습니다.
대시보드는 서로 다른 두 게이트웨이 디바이스 관리 작업 사이에서 논리적으로 분할됩니다. 
   * 트럭 운송의 물류 경로와 해상 운송의 위치 세부 정보는 모든 다중 모달 운성의 필수 요소입니다.
   * 게이트웨이 상태 및 관련 정보 보기 

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * 게이트웨이, 활성 및 알 수 없는 태그의 총 수를 쉽게 추적할 수 있습니다.
   * 펌웨어 업데이트, 센서 사용 안 함, 센서 사용, 센서 임계값 업데이트, 원격 분석 간격 업데이트, 디바이스 서비스 계약 업데이트 등의 디바이스 관리 작업을 수행할 수 있습니다.
   * 디바이스 배터리 사용량 보기

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>디바이스 템플릿

디바이스 템플릿 탭을 클릭하면 게이트웨이 기능 모델이 표시됩니다. 기능 모델은 두 개의 다른 인터페이스인 **게이트웨이 원격 분석 및 속성** 및 **게이트웨이 명령**을 통해 구성됩니다.

**게이트웨이 원격 분석 및 속성** - 이 인터페이스는 센서, 위치 및 디바이스 정보와 관련된 모든 원격 분석과 센서 임계값 및 업데이트 간격과 같은 디바이스 쌍 속성 기능을 나타냅니다.

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**게이트웨이 명령** - 이 인터페이스는 모든 게이트웨이 명령 기능을 구성합니다.

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>규칙
규칙 탭을 선택하여 이 애플리케이션 템플릿에 존재하는 두 가지 다른 규칙을 표시합니다. 이러한 규칙은 추가 조사를 위해 운영자에게 메일 알림을 보내도록 구성됩니다.
 
**게이트웨이 도난 경고**: 이 규칙은 진행 중에 센서가 예기치 않은 광원을 감지할 때 트리거됩니다. 잠재적 도난을 조사할 수 있도록 운영자에게 즉시 알림을 제공해야 합니다.
 
**응답하지 않는 게이트웨이**: 이 규칙은 게이트웨이가 장기간 클라우드에 보고하지 않는 경우에 트리거됩니다. 배터리 부족 모드, 연결 손실, 디바이스 상태로 인해 게이트웨이가 응답하지 않을 수 있습니다.

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>작업
작업 탭을 선택하여 이 애플리케이션 템플릿의 일부로 존재하는 5개의 다른 작업을 표시합니다.

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

작업 기능을 사용하여 솔루션 전체 작업을 수행할 수 있습니다. 여기에서 작업은 디바이스 명령 및 쌍 기능을 사용하여 모든 게이트웨이에서 특정 센서를 사용하지 않도록 설정하거나 배송 모드 및 경로에 따라 센서 임계값을 수정하는 등의 작업을 수행합니다. 
   * 배터리를 절약하기 위해 해양 운송 동안 충격 센서를 사용하지 않도록 설정하거나 콜드 체인 운송 동안 온도 임계값을 줄이는 것이 표준 작업입니다. 
 
   * 작업을 사용하여 게이트웨이의 펌웨어 업데이트 또는 서비스 계약을 업데이트하여 유지 관리 작업을 최신 상태로 유지하는 등의 시스템 차원의 작업을 수행할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리
이 애플리케이션을 계속 사용하지 않으려면 **관리** > **애플리케이션 설정**을 방문하여 애플리케이션 템플릿을 삭제하고 **삭제**를 클릭합니다.

> [!div class="mx-imgBorder"]
> ![연결된 물류 대시보드](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>다음 단계
* [커넥티드 물류 개념](./architecture-connected-logistics-pnp.md)에 대해 자세히 알아보기
* 다른 [IoT Central 소매 템플릿](./overview-iot-central-retail-pnp.md)에 대해 자세히 알아보기
* [IoT Central 개요](../core/overview-iot-central.md)에 대해 자세히 알아보기

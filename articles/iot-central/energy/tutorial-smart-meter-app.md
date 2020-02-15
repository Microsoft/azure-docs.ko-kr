---
title: '자습서: IoT Central을 사용하여 스마트 미터 분석 앱 만들기'
description: '자습서: Azure IoT Central 애플리케이션 템플릿을 사용하여 스마트 미터 모니터링 애플리케이션을 만드는 방법에 대해 알아봅니다.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016003"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>자습서: 스마트 미터 모니터링 앱 템플릿 만들기 및 연습 



이 자습서에서는 스마트 미터 모니터링 애플리케이션을 만드는 과정을 안내하며, 여기에는 시뮬레이션된 데이터를 사용하는 샘플 디바이스 모델이 포함됩니다. 이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * 무료로 스마트 미터 앱 만들기
> * 애플리케이션 연습
> * 리소스 정리


구독이 없는 경우 [평가판 계정을 만듭니다](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>사전 요구 사항
- None
- Azure 구독을 권장하지만, 꼭 사용할 필요는 없습니다.

## <a name="create-a-smart-meter-monitoring-app"></a>스마트 측정기 모니터링 앱 만들기 

이 애플리케이션은 다음과 같은 세 가지 간단한 단계로 만들 수 있습니다.

1. [Azure IoT Central 홈페이지](https://apps.azureiotcentral.com)를 열고 **빌드**를 클릭하여 새 애플리케이션을 만듭니다. 

2. **에너지** 탭을 선택하고 **스마트 미터 모니터링** 애플리케이션 타일 아래에서 **앱 만들기**를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![앱 빌드](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **앱 만들기**에서 **새 애플리케이션** 양식이 열립니다. 아래 그림처럼 요청된 정보를 입력합니다.
    * **애플리케이션 이름**: IoT Central 애플리케이션의 이름을 선택합니다. 
    * **URL**: IoT Central URL을 선택하면 플랫폼에서 해당 URL의 고유성을 확인합니다.
    * **7일 평가판**: Azure 구독이 이미 있는 경우 기본 설정을 사용하는 것이 좋습니다. Azure 구독이 없는 경우 평가판으로 시작하세요.
    * **청구 정보**: 애플리케이션 자체는 무료입니다. 앱의 리소스를 프로비저닝하려면 디렉터리, Azure 구독 및 영역 세부 정보가 필요합니다.
    * 페이지 맨 아래에서 **만들기** 단추를 클릭하면 1분 내에 앱이 생성됩니다.

        ![새 애플리케이션 양식](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![새 애플리케이션 양식 청구 정보](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>애플리케이션 및 시뮬레이션된 데이터 확인

새로 생성된 스마트 미터 앱은 여러분의 앱이며 언제든지 수정할 수 있습니다. 앱을 수정하기 전에 앱이 배포되었고 예상대로 작동하는지 확인합니다.

앱 만들기 및 데이터 시뮬레이션을 확인하려면 **대시보드**로 이동합니다. 데이터가 포함된 타일이 보이면 앱 배포에 성공한 것입니다. 데이터 시뮬레이션에서 데이터를 생성할 때까지 몇 분 정도 걸릴 수 있으므로 1-2분 정도 기다립니다. 

## <a name="application-walk-through"></a>애플리케이션 연습
앱 템플릿을 성공적으로 배포한 후에는 앱과 함께 샘플 스마트 미터 디바이스, 디바이스 모델 및 대시보드가 제공됩니다. 

Adatum은 스마트 미터를 모니터링 및 관리하는 가상의 에너지 회사입니다. 스마트 미터 모니터링 대시보드에는 스마트 미터 속성, 데이터 및 샘플 명령이 표시됩니다. 운영자와 지원 팀은 지원 인시던트가 발생하기 전에 다음 작업을 선제적으로 수행할 수 있습니다. 
* 맵의 최신 미터 정보 및 설치된 위치 검토
* 미터 네트워크 및 연결 상태를 사전에 확인 
* 네트워크 상태의 최소 및 최대 전압 판독값 모니터링 
* 에너지, 전력 및 전압 추세를 검토하여 비정상적인 패턴 파악 
* 계획 및 요금 청구 목적에 사용할 수 있도록 총 에너지 사용량 추적
* 미터 다시 연결 및 펌웨어 버전 업데이트와 같은 명령 및 제어 작업. 템플릿의 명령 단추는 가능한 기능을 보여주지만 실제 명령을 보내지는 않습니다. 

> [!div class="mx-imgBorder"]
> ![스마트 미터 모니터링 대시보드](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>디바이스
앱과 함께 샘플 스마트 미터 디바이스가 제공됩니다. **디바이스** 탭을 클릭하여 디바이스 세부 정보를 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![스마트 미터 디바이스](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

디바이스 세부 정보를 보려면 샘플 디바이스 **SM0123456789** 링크를 클릭합니다. **속성 업데이트** 페이지에서 디바이스의 쓰기 가능 속성을 업데이트하고, 업데이트된 값을 대시보드에 시각화할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![스마트 미터 속성](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>디바이스 템플릿
**디바이스 템플릿** 탭을 클릭하여 스마트 미터 디바이스 모델을 봅니다. 모델에는 데이터, 속성, 명령 및 보기에 대해 미리 정의된 인터페이스가 있습니다.

> [!div class="mx-imgBorder"]
> ![스마트 미터 디바이스 템플릿](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>리소스 정리
이 애플리케이션이 더 이상 필요 없는 경우 다음 단계에 따라 애플리케이션을 삭제합니다.

1. 왼쪽 창에서 [관리] 탭을 엽니다.
2. [애플리케이션 설정]을 선택하고 페이지 아래쪽에서 [삭제] 단추를 클릭합니다. 

    > [!div class="mx-imgBorder"]
    > ![애플리케이션 삭제](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>다음 단계
* 스마트 미터 앱 아키텍처에 대한 자세한 내용은 [개념 문서](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)를 참조하세요.
* [스마트 미터 앱](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)을 참조하여 무료로 스마트 미터 애플리케이션 템플릿을 만들어보세요.
* [IoT Central 개요](https://docs.microsoft.com/azure/iot-central/)를 참조하여 IoT Central에 대해 자세히 알아보세요.

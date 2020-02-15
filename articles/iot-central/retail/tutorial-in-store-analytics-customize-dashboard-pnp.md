---
title: 자습서 - Azure IoT Central에서 운영자 대시보드 사용자 지정
description: 이 자습서에서는 IoT Central 애플리케이션에서 운영자 대시보드를 사용자 지정하고 디바이스를 관리하는 방법을 보여 줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: fea3440dd7c017b85d6fffa2ec0fe5d26cd98b97
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022174"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>자습서:  Azure IoT Central에서 운영자 대시보드 사용자 지정 및 디바이스 관리


이 자습서에서는 빌더가 Azure IoT Central 스토어 내 분석 애플리케이션에서 운영자 대시보드를 사용자 지정하는 방법을 알아봅니다. 애플리케이션 운영자는 사용자 지정된 대시보드를 사용하여 애플리케이션을 실행하고 연결된 디바이스를 관리할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 대시보드 이름 변경
> * 대시보드의 이미지 타일 사용자 지정
> * 타일을 정렬하여 레이아웃 수정
> * 조건을 표시하는 원격 분석 타일 추가
> * 디바이스 세부 정보를 표시하는 속성 타일 추가
> * 명령을 실행하는 명령 타일 추가

## <a name="prerequisites"></a>사전 요구 사항

빌더는 Azure IoT Central 스토어 내 분석 애플리케이션을 만들고 디바이스를 추가하는 자습서를 완료해야 합니다.

* [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app-pnp.md)(필수)

## <a name="change-the-dashboard-name"></a>대시보드 이름 변경
운영자 대시보드를 사용자 지정하려면 애플리케이션에서 기본 대시보드를 편집해야 합니다. 또한 새 대시보드를 추가로 만들 수 있습니다. 애플리케이션에서 대시보드를 사용자 지정하는 첫 번째 단계는 이름을 변경하는 것입니다.

1. [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트로 이동합니다.

1. [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app-pnp.md) 자습서에서 만든 조건 모니터링 애플리케이션을 엽니다.

1. 대시보드 도구 모음에서 **편집**을 선택합니다. 편집 모드에서는 대시보드의 모양, 레이아웃 및 콘텐츠를 사용자 지정할 수 있습니다.

    ![Azure IoT Central 대시보드 편집](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. 필요에 따라 왼쪽 창을 숨깁니다. 왼쪽 창을 숨기면 대시보드를 편집할 수 있는 작업 영역이 더 커집니다.

1. **대시보드 이름**에서 대시보드에 대한 친숙한 이름을 입력합니다. 이 자습서에서는 Contoso라는 가상 회사를 사용하며, 대시보드 이름 예제는 *Contoso 대시보드*입니다. 

1. **저장**을 선택합니다. 변경 내용이 대시보드에 저장되고 편집 모드가 비활성화됩니다.

    ![Azure IoT Central 대시보드 이름 변경](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>대시보드의 이미지 타일 사용자 지정
Azure IoT Central 애플리케이션 대시보드는 하나 이상의 타일로 구성됩니다. 타일은 대시보드에 콘텐츠를 표시하는 사각형 컨테이너입니다. 다양한 형식의 콘텐츠를 타일과 연결하고, 타일을 끌어서 놓고, 크기를 조정하여 대시보드 레이아웃을 사용자 지정합니다. 콘텐츠를 표시하는 여러 유형의 타일이 있습니다. 이미지 타일에는 이미지가 포함되어 있으며, 사용자가 이미지를 클릭할 수 있도록 하는 URL을 추가할 수 있습니다. 레이블 타일에는 일반 텍스트가 표시됩니다. Markdown 타일에는 서식이 지정된 콘텐츠가 포함되어 있으며, HTML로 렌더링되는 이미지, URL, 제목 및 Markdown 코드를 설정할 수 있습니다. 원격 분석, 속성 또는 명령 타일에는 디바이스 관련 데이터가 표시됩니다. 

이 섹션에서는 대시보드에서 이미지 타일을 사용자 지정하는 방법에 대해 알아봅니다.

대시보드에서 브랜드 이미지를 표시하는 이미지 타일을 사용자 지정하려면 다음을 수행합니다.

1. 대시보드 도구 모음에서 **편집**을 선택합니다. 

1. Northwind 브랜드 이미지를 표시하는 이미지 타일에서 **구성**을 선택합니다. 

    ![Azure IoT Central 브랜드 이미지 편집](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. **제목**을 변경합니다. 사용자가 마우스로 이미지를 가리키면 제목이 표시됩니다.

1. **이미지**를 선택합니다. 대화 상자가 열리고, 사용자 지정 이미지를 업로드하도록 설정합니다. 

1. 필요에 따라 이미지에 대한 URL을 지정합니다.

1. **구성 업데이트**를 선택합니다. **구성 업데이트** 단추는 대시보드에 대한 변경 내용을 저장하고, 편집 모드를 사용하도록 설정된 상태로 둡니다.

    ![Azure IoT Central 브랜드 이미지 저장](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. 필요에 따라 **설명서**라는 제목의 타일에서 **구성**을 선택하고, 지원 콘텐츠에 대한 URL을 지정합니다. 

스토어의 센서 영역 맵을 표시하는 이미지 타일을 사용자 지정하려면 다음을 수행합니다.

1. 기본 스토어 영역 맵을 표시하는 이미지 타일에서 **구성**을 선택합니다. 

1. **이미지**를 선택하고, 대화 상자를 사용하여 스토어 영역 맵의 사용자 지정 이미지를 업로드합니다. 

1. **구성 업데이트**를 선택합니다.

    ![Azure IoT Central 스토어 맵 저장](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    Contoso 스토어 맵 예제에는 두 개의 체크 아웃 영역, 의류 및 개인 용품 영역, 식료품 및 가공 식품 영역의 네 가지 영역이 표시됩니다. 이 자습서에서는 센서를 이러한 영역에 연결하여 원격 분석을 제공합니다.

    ![Azure IoT Central 스토어 영역](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. **저장**을 선택합니다. 

## <a name="arrange-tiles-to-modify-the-layout"></a>타일을 정렬하여 레이아웃 수정
대시보드를 사용자 지정하는 주요 단계는 타일을 다시 정렬하여 유용한 보기를 만드는 것입니다. 애플리케이션 운영자는 대시보드를 사용하여 디바이스 원격 분석을 시각화하고, 디바이스를 관리하고, 스토어의 조건을 모니터링합니다. Azure IoT Central은 대시보드를 만드는 애플리케이션 빌더 작업을 간소화합니다. 대시보드 편집 모드를 사용하면 타일을 빠르게 추가, 이동, 크기 조정 및 삭제할 수 있습니다. 또한 **스토어 내 분석 - 체크 아웃** 애플리케이션 템플릿은 대시보드를 만드는 작업을 간소화합니다. 센서가 연결되어 작동하는 대시보드 레이아웃과 체크 아웃 라인 수 및 환경 조건을 표시하는 타일을 제공합니다.

이 섹션에서는 **스토어 내 분석 - 체크 아웃** 애플리케이션 템플릿에서 대시보드를 다시 정렬하여 사용자 지정 레이아웃을 만듭니다.

애플리케이션에서 사용하지 않을 타일을 제거하려면 다음을 수행합니다.

1. 대시보드 도구 모음에서 **편집**을 선택합니다. 

1. **X(삭제)** 를 선택하여 **모든 영역으로 돌아가기**, **스토어 대시보드 방문**, **대기 시간** 타일 및 **체크 아웃 3**과 연결된 세 개의 타일을 모두 제거합니다. Contoso 스토어 대시보드는 이러한 타일을 사용하지 않습니다. 

    ![Azure IoT Central 타일 삭제](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. 스크롤하여 나머지 대시보드 타일을 보기로 가져옵니다.

1. **X(삭제)** 를 선택하여 **준비 체크 아웃 영역**, **휴지 체크 아웃 영역**, **선점 센서 설정**, **자동 온도 조절기 센서 설정** 및 **환경 조건** 타일을 제거합니다. 

   ![Azure IoT Central 나머지 타일 삭제](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. **저장**을 선택합니다. 사용하지 않는 타일을 제거하면 편집 페이지의 공간이 확보되고 운영자 대시보드 보기가 간소화됩니다.

1. 대시보드에 대한 변경 내용을 확인합니다.

   ![Azure IoT Central 타일 삭제 후](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

사용하지 않는 타일이 제거되면 나머지 타일을 다시 정렬하여 정리된 레이아웃을 만듭니다. 새 레이아웃에는 나중에 추가할 타일을 위한 공간이 포함됩니다.

나머지 타일을 다시 정렬하려면 다음을 수행합니다.

1. **편집**을 선택합니다.

1. **선점 펌웨어** 타일을 선택하고, **선점** 배터리 타일의 오른쪽으로 끕니다.

1. **자동 온도 조절기 펌웨어** 타일을 선택하고, **자동 온도 조절기** 배터리 타일의 오른쪽으로 끕니다.

1. **저장**을 선택합니다.

1. 레이아웃 변경 내용을 확인합니다. 

    ![Azure IoT Central 펌웨어 배터리 타일](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>조건을 표시하는 원격 분석 타일 추가
대시보드 레이아웃이 사용자 지정되면 원격 분석을 표시하는 타일을 추가할 준비가 된 것입니다. 원격 분석 타일을 만들려면 디바이스 템플릿과 디바이스 인스턴스를 선택한 다음, 타일에 표시할 디바이스 관련 원격 분석을 선택합니다. **스토어 내 분석 - 체크 아웃** 애플리케이션 템플릿에는 대시보드의 몇 가지 원격 분석 타일이 포함되어 있습니다. 두 체크 아웃 영역에 있는 네 개의 타일은 시뮬레이션된 선점 센서의 원격 분석을 표시합니다. **사람 트래픽** 타일에는 두 체크 아웃 영역의 수가 표시됩니다. 

이 섹션에서는 [Azure IoT Central에서 스토어 내 분석 애플리케이션 만들기](./tutorial-in-store-analytics-create-app-pnp.md) 자습서에서 추가한 RuuviTag 센서의 환경 원격 분석을 표시하는 두 개의 원격 분석 타일을 추가합니다. 

RuuviTag 센서의 환경 데이터를 표시하는 타일을 추가하려면 다음을 수행합니다.

1. **편집**을 선택합니다.

1. **디바이스 템플릿** 목록에서 `RuuviTag`을 선택합니다. 

1. 두 개의 RuuviTag 센서 중 하나에서 **디바이스 인스턴스**를 선택합니다. Contoso 스토어 예제에서 `Zone 1 Ruuvi`를 선택하여 영역 1에 대한 원격 분석 타일을 만듭니다. 

1. **원격 분석** 목록에서 `Relative humidity` 및 `temperature`를 선택합니다. 이는 타일의 각 영역에 대해 표시되는 원격 분석 항목입니다.

1. **결합**을 선택합니다. 

    ![Azure IoT Central RuuviTag 타일 1 추가](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    선택한 센서에 대해 결합된 습도 및 온도 원격 분석을 표시하는 새 타일이 표시됩니다. 

1. RuuviTag 센서에 대한 새 타일에서 **구성**을 선택합니다. 

1. **제목**을 *영역 1 환경*으로 변경합니다. 

1. **구성 업데이트**를 선택합니다.

1. 이전 단계를 반복하여 두 번째 센서 인스턴스에 대한 타일을 만듭니다. **제목**을 *영역 2 환경*으로 설정한 다음, **구성 업데이트**를 선택합니다.

1. **영역 2 환경**이라는 제목의 타일을 **자동 온도 조절기 펌웨어** 타일 아래로 끕니다. 

1. **영역 1 환경**이라는 제목의 타일을 **사람 트래픽** 타일 아래로 끕니다. 

1. **저장**을 선택합니다. 대시보드에서 두 개의 새 타일에 영역 원격 분석이 표시됩니다.

    ![Azure IoT Central 모든 RuuviTag 타일](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

두 개의 체크 아웃 영역에 대한 원격 분석만 표시하도록 **사람 트래픽** 타일을 편집하려면 다음을 수행합니다.

1. **편집**을 선택합니다. 

1. **사람 트래픽** 타일에서 **구성**을 선택합니다.

1. **원격 분석**에서 **수 1**, **수 2** 및 **수 3**을 선택합니다. 

1. **구성 업데이트**를 선택합니다. 그러면 타일에서 기존 구성이 지워집니다. 

1. **사람 트래픽** 타일에서 **구성**을 다시 선택합니다.

1. **원격 분석**에서 **수 1** 및 **수 2**를 선택합니다. 

1. **구성 업데이트**를 선택합니다. 

1. **저장**을 선택합니다.  업데이트된 대시보드에는 시뮬레이션된 선점 센서를 기반으로 하는 두 개의 체크 아웃 영역에 대한 수만 표시됩니다.

    ![Azure IoT Central 사람 트래픽 2개 레인](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>디바이스 세부 정보를 표시하는 속성 타일 추가
애플리케이션 운영자는 대시보드를 사용하여 디바이스를 관리하고 상태를 모니터링합니다. 운영자가 소프트웨어 버전을 볼 수 있도록 각 RuuviTag에 대한 타일을 추가합니다. 

각 RuuviTag에 대한 속성 타일을 추가하려면 다음을 수행합니다.

1. **편집**을 선택합니다.

1. **디바이스 템플릿** 목록에서 `RuuviTag`을 선택합니다. 

1. 두 개의 RuuviTag 센서 중 하나에서 **디바이스 인스턴스**를 선택합니다. Contoso 스토어 예제에서 `Zone 1 Ruuvi`를 선택하여 영역 1에 대한 원격 분석 타일을 만듭니다. 

1. **속성 > 소프트웨어 버전**을 차례로 선택합니다.

1. **결합**을 선택합니다. 

1. **소프트웨어 버전**이라는 제목의 새로 만든 타일에서 **구성**을 선택합니다. 

1. **제목**을 *Ruuvi 1 소프트웨어 버전*으로 변경합니다.

1. **구성 업데이트**를 선택합니다. 

1. **Ruuvi 1 소프트웨어 버전**이라는 제목의 타일을 **영역 1 환경** 타일 아래로 끕니다.

1. 이전 단계를 반복하여 두 번째 RuuviTag에 대한 소프트웨어 버전 속성 타일을 만듭니다. 

1. **저장**을 선택합니다.  

    ![Azure IoT Central RuuviTag 속성 타일](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>명령을 실행하는 명령 타일 추가
또한 애플리케이션 운영자는 대시보드에서 명령을 실행하여 디바이스를 관리합니다. 디바이스에서 미리 정의된 명령을 실행하는 명령 타일을 대시보드에 추가할 수 있습니다. 이 섹션에서는 운영자가 Rigado 게이트웨이를 다시 부팅하도록 설정하는 명령 타일을 추가합니다. 

게이트웨이를 다시 부팅하는 명령 타일을 추가하려면 다음을 추가합니다.

1. **편집**을 선택합니다. 

1. **디바이스 템플릿** 목록에서 `C500`을 선택합니다. Rigado C500 게이트웨이에 대한 템플릿입니다. 

1. **디바이스 인스턴스**에서 게이트웨이 인스턴스를 선택합니다.

1. **명령 > 다시 부팅**을 차례로 선택하고, 스토어 맵 옆의 대시보드로 끕니다. 

1. **저장**을 선택합니다. 

1. 완료된 Contoso 대시보드를 확인합니다. 

    ![Azure IoT Central 대시보드 사용자 지정 완료](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. 필요에 따라 **다시 부팅** 타일을 선택하여 게이트웨이에서 다시 부팅 명령을 실행합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 작업 방법을 알아보았습니다.

* 대시보드 이름 변경
* 대시보드의 이미지 타일 사용자 지정
* 타일을 정렬하여 레이아웃 수정
* 조건을 표시하는 원격 분석 타일 추가
* 디바이스 세부 정보를 표시하는 속성 타일 추가
* 명령을 실행하는 명령 타일 추가

이제 Azure IoT Central 스토어 내 분석 애플리케이션에서 대시보드를 사용자 지정했으므로 제안되는 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [데이터 내보내기 및 인사이트 시각화](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)

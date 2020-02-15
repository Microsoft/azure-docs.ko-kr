---
title: Azure IoT Central 응용 프로그램에서 장치 그룹 사용 | Microsoft Docs
description: 운영자로 서 장치 그룹을 사용 하 여 Azure IoT Central 응용 프로그램의 장치에서 원격 분석을 분석 하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167289"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>자습서: 장치 그룹을 사용 하 여 장치 원격 분석 분석

이 문서에서는 장치 그룹을 사용 하 여 Azure IoT Central 응용 프로그램에서 장치 원격 분석을 분석 하는 방법을 설명 합니다.

장치 그룹은 지정 된 조건과 일치 하므로 함께 그룹화 되는 장치 목록입니다. 장치 그룹을 통해 장치를 더 작은 논리 그룹으로 그룹화 하 여 규모에 따라 장치를 관리 하 고, 시각화 하 고, 분석할 수 있습니다. 예를 들어 시애틀에 있는 모든 대공 공조 장치를 나열 하는 장치 그룹을 만들어 엔지니어가 담당 하는 장치를 찾을 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 그룹 만들기
> * 장치 그룹을 사용 하 여 장치 원격 분석 분석

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 [Azure IoT Central 응용 프로그램 만들기](./quick-deploy-iot-central.md) 를 완료 하 고 시뮬레이션 된 [장치를 IoT Central 응용 프로그램 빠른 시작에 추가](./quick-create-pnp-device.md) 하 여 작업할 **MXChip IoT devkit** 장치 템플릿을 만들어야 합니다.

## <a name="create-simulated-devices"></a>시뮬레이션 된 장치 만들기

장치 그룹을 만들기 전에이 자습서에서 사용할 **MXChip IoT DevKit** 장치 템플릿에서 시뮬레이션 된 장치를 5 개 이상 추가 합니다.

![5 개의 시뮬레이션 된 센서 장치](./media/tutorial-use-device-groups/simulated-devices.png)

시뮬레이션 된 센서 장치 중 4 개에 대해 **장치 보기 관리** 를 사용 하 여 고객 이름을 *Contoso*로 설정 합니다.

![고객 이름을 Contoso로 설정](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>디바이스 그룹 만들기

장치 그룹을 만들려면 다음을 수행 합니다.

1. 왼쪽 창에서 **장치 그룹** 을 선택 합니다.

1. **+** 선택:

    ![새 장치 그룹](media/tutorial-use-device-groups/image1.png)

1. 장치 그룹에 *Contoso 장치*이름을 지정 합니다. 설명을 추가할 수도 있습니다. 장치 그룹은 단일 장치 템플릿의 장치만 포함할 수 있습니다. 이 그룹에 사용할 **MXChip IoT DevKit** 장치 템플릿을 선택 합니다.

1. **Contoso**에 속하는 장치만 포함 하도록 장치 그룹을 사용자 지정 하려면 **+ 필터**를 선택 합니다. **Customer Name** 속성, **Equals** 비교 연산자 및 **Contoso** 를 값으로 선택 합니다. 필터 조건을 **모두** 충족 하는 여러 필터를 추가할 수 있습니다 장치 그룹에 배치 됩니다. 만든 장치 그룹은 응용 프로그램에 액세스할 수 있는 모든 사용자가 액세스할 수 있으므로 모든 사용자가 장치 그룹을 보거나, 수정 하거나, 삭제할 수 있습니다.

    ![장치 그룹 쿼리](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > 장치 그룹이 동적 쿼리입니다. 디바이스 목록을 볼 때마다 목록의 디바이스가 달라질 수 있습니다. 현재 쿼리 조건을 충족하는 디바이스에 따라 목록이 달라집니다.

1. **저장**을 선택합니다.

> [!NOTE]
> Azure IoT Edge 장치에 대해 Azure IoT Edge 템플릿을 선택 하 여 장치 그룹을 만듭니다.

## <a name="analytics"></a>분석

장치 그룹에 **분석** 을 사용 하 여 그룹의 장치에서 원격 분석을 분석할 수 있습니다. 예를 들어 모든 Contoso 환경 센서가 보고 하는 평균 온도를 그릴 수 있습니다.

장치 그룹에 대 한 원격 분석을 분석 하려면:

1. 왼쪽 창에서 **분석** 을 선택 합니다.

1. 만든 **Contoso 장치** 장치 그룹을 선택 합니다. 그런 다음 **온도** 및 **습도** 원격 분석 유형을 추가 합니다.

    ![분석 만들기](./media/tutorial-use-device-groups/create-analysis.png)

    원격 분석 유형 옆의 기어 휠 아이콘을 사용 하 여 집계 유형을 선택 합니다. 기본값은 **Average**입니다. **Split by** 를 사용 하 여 집계 데이터가 표시 되는 방식을 변경 합니다. 예를 들어 장치 ID로 분할 하는 경우 **분석**을 선택 하면 각 장치에 대 한 플롯이 표시 됩니다.

1. **분석** 을 선택 하 여 평균 원격 분석 값을 확인 합니다.

    ![분석 보기](./media/tutorial-use-device-groups/view-analysis.png)

    뷰를 사용자 지정 하 고, 표시 된 기간을 변경 하 고, 데이터를 내보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에서 장치 그룹을 사용 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [원격 분석 규칙을 만드는 방법](tutorial-create-telemetry-rules.md)

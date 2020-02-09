---
title: 사용자 지정 분석으로 Azure IoT Central 확장 | Microsoft Docs
description: 솔루션 개발자는 사용자 지정 분석 및 시각화를 수행 하도록 IoT Central 응용 프로그램을 구성 합니다. 이 솔루션은 Azure Databricks를 사용 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7e5e8331509e99a7e556105ff1ea8ca2d0b285e7
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023840"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure Databricks를 사용 하 여 사용자 지정 분석으로 Azure IoT Central 확장

이 방법 가이드에서는 솔루션 개발자가 사용자 지정 분석 및 시각화를 사용 하 여 IoT Central 응용 프로그램을 확장 하는 방법을 보여 줍니다. 이 예제에서는 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) 작업 영역을 사용 하 여 IoT Central 원격 분석 스트림을 분석 하 고 [box 플롯](https://wikipedia.org/wiki/Box_plot)등의 시각화를 생성 합니다.

이 방법 가이드에서는 [기본 제공 분석 도구](./howto-create-custom-analytics.md)를 사용 하 여 이미 수행할 수 있는 작업 이상의 IoT Central 확장 하는 방법을 보여 줍니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

* *연속 데이터 내보내기를*사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* 장치 원격 분석을 분석 하 고 플롯 하는 Azure Databricks 환경을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 애플리케이션

다음 설정을 사용 하 여 [Azure IoT Central 응용 프로그램](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 요금제 | Standard |
| 애플리케이션 템플릿 | 저장소 내 분석-조건 모니터링 |
| 애플리케이션 이름 | 기본값을 그대로 적용 하거나 고유한 이름을 선택 합니다. |
| URL | 기본값을 그대로 적용 하거나 고유한 URL 접두사를 선택 합니다. |
| 디렉터리 | Azure Active Directory 테넌트 |
| Azure 구독 | Azure 구독 |
| 지역 | 가장 가까운 지역 |

이 문서의 예제 및 스크린샷은 **미국** 지역을 사용 합니다. 가까운 위치를 선택 하 고 동일한 지역에 모든 리소스를 만들어 두어야 합니다.

이 응용 프로그램 템플릿에는 원격 분석을 전송 하는 두 개의 시뮬레이션 된 자동 온도 조절기 장치가 포함 되어 있습니다.

### <a name="resource-group"></a>리소스 그룹

Azure Portal를 사용 하 여 만든 다른 리소스를 포함 하는 **IoTCentralAnalysis** 라는 [리소스 그룹을 만듭니다](https://portal.azure.com/#create/Microsoft.ResourceGroup) . IoT Central 응용 프로그램과 동일한 위치에 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

Azure Portal를 사용 하 여 다음 설정으로 [Event Hubs 네임 스페이스를 만듭니다](https://portal.azure.com/#create/Microsoft.EventHub) .

| 설정 | 값 |
| ------- | ----- |
| 이름    | 네임 스페이스 이름 선택 |
| 가격 책정 계층 | Basic |
| Subscription | 사용자의 구독 |
| 리소스 그룹 | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 작업 영역

Azure Portal를 사용 하 여 다음 설정으로 [Azure Databricks 서비스를 만듭니다](https://portal.azure.com/#create/Microsoft.Databricks) .

| 설정 | 값 |
| ------- | ----- |
| 작업 영역 이름    | 작업 영역 이름 선택 |
| Subscription | 사용자의 구독 |
| 리소스 그룹 | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 가격 책정 계층 | Standard |

필요한 리소스를 만든 경우 **IoTCentralAnalysis** 리소스 그룹은 다음 스크린샷 처럼 보입니다.

![IoT Central 분석 리소스 그룹](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브로 원격 분석을 지속적으로 내보내도록 IoT Central 응용 프로그램을 구성할 수 있습니다. 이 섹션에서는 IoT Central 응용 프로그램에서 원격 분석을 수신 하는 이벤트 허브를 만듭니다. 이벤트 허브는 처리를 위해 원격 분석을 Stream Analytics 작업에 전달 합니다.

1. Azure Portal에서 Event Hubs 네임 스페이스로 이동 하 고 **+ 이벤트 허브**를 선택 합니다.
1. 이벤트 허브의 이름을 **centralexport**으로 선택 하 고 **만들기**를 선택 합니다.
1. 네임 스페이스의 이벤트 허브 목록에서 **centralexport**를 선택 합니다. 그런 다음 **공유 액세스 정책**을 선택 합니다.
1. **+추가**를 선택합니다. **수신 클레임으로** **수신 대기** 라는 정책을 만듭니다.
1. 정책이 준비 되 면 목록에서이를 선택 하 고 **연결 문자열-기본 키** 값을 복사 합니다.
1. 이 연결 문자열을 기록해 둡니다. 나중에 이벤트 허브에서 읽도록 Databricks 노트북을 구성할 때이 연결 문자열을 사용 합니다.

Event Hubs 네임 스페이스는 다음 스크린샷 처럼 보입니다.

![Event Hubs 네임스페이스](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>IoT Central에서 내보내기 구성

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 Contoso 템플릿에서 만든 IoT Central 응용 프로그램으로 이동 합니다. 이 섹션에서는 시뮬레이션 된 장치에서 이벤트 허브로 원격 분석을 스트리밍하기 응용 프로그램을 구성 합니다. 내보내기를 구성 하려면:

1. **데이터 내보내기** 페이지로 이동 하 고, **+ 새로 만들기**를 선택 하 고, **Azure Event Hubs**를 선택 합니다.
1. 내보내기를 구성 하려면 다음 설정을 사용 하 고 **저장**을 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | Event Hubs로 내보내기 |
    | 사용 | 설정 |
    | Event Hubs 네임스페이스 | Event Hubs 네임 스페이스 이름 |
    | 이벤트 허브 | centralexport |
    | 측정값 | 설정 |
    | 디바이스 | 해제 |
    | 디바이스 템플릿 | 해제 |

![데이터 내보내기 구성](media/howto-create-custom-analytics/cde-configuration.png)

계속 하기 전에 내보내기 상태가 **실행 중** 이 될 때까지 기다립니다.

## <a name="configure-databricks-workspace"></a>Databricks 작업 영역 구성

Azure Portal에서 Azure Databricks 서비스로 이동 하 고 **작업 영역 시작**을 선택 합니다. 브라우저에서 새 탭이 열리고 작업 영역에 로그인 합니다.

### <a name="create-a-cluster"></a>클러스터 만들기

**Azure Databricks** 페이지의 일반 작업 목록에서 **새 클러스터**를 선택 합니다.

다음 표의 정보를 사용 하 여 클러스터를 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 클러스터 이름 | centralanalysis |
| 클러스터 모드 | Standard |
| Databricks Runtime 버전 | 5.5 LTS (Scala 2.11, Spark 2.4.3) |
| Python 버전 | 3 |
| 자동 크기 조정 사용 | 아닙니다. |
| 비활성 시간 (분) 후 종료 | 30 |
| 작업자 유형 | Standard_DS3_v2 |
| 작업자 | 1 |
| 드라이버 유형 | 작업자와 동일 |

클러스터를 만들려면 몇 분 정도 걸릴 수 있습니다. 계속 하기 전에 클러스터 만들기가 완료 될 때까지 기다립니다.

### <a name="install-libraries"></a>라이브러리 설치

**클러스터 페이지에서** 클러스터 상태가 **실행 중**이 될 때까지 기다립니다.

다음 단계는 샘플에 필요한 라이브러리를 클러스터로 가져오는 방법을 보여 줍니다.

1. **클러스터** 페이지에서 **centralanalysis** 대화형 클러스터의 상태가 **실행 중**이 될 때까지 기다립니다.

1. 클러스터를 선택 하 고 **라이브러리** 탭을 선택 합니다.

1. **라이브러리** 탭에서 **새로 설치**를 선택 합니다.

1. **라이브러리 설치** 페이지에서 라이브러리 원본으로 **Maven** 을 선택 합니다.

1. **좌표** 텍스트 상자에 다음 값을 입력 합니다. `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. **설치** 를 선택 하 여 클러스터에 라이브러리를 설치 합니다.

1. 이제 라이브러리 상태가 **설치**됨:

    ![라이브러리 설치 됨](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Databricks 노트북 가져오기

다음 단계를 사용 하 여 IoT Central 원격 분석을 분석 하 고 시각화할 Python 코드가 포함 된 Databricks 노트북을 가져옵니다.

1. Databricks 환경의 **작업 영역** 페이지로 이동 합니다. 계정 이름 옆에 있는 드롭다운을 선택 하 고 **가져오기**를 선택 합니다.

1. URL에서 가져오기를 선택 하 고 다음 주소를 입력 합니다. [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. 노트북을 가져오려면 **가져오기**를 선택 합니다.

1. 가져온 노트북을 볼 **작업 영역** 을 선택 합니다.

    ![가져온 노트북](media/howto-create-custom-analytics/import-notebook.png)

1. 첫 번째 Python 셀의 코드를 편집 하 여 이전에 저장 한 Event Hubs 연결 문자열을 추가 합니다.

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>분석 실행

분석을 실행 하려면 노트북을 클러스터에 연결 해야 합니다.

1. **분리** 됨을 선택한 다음 **centralanalysis** 클러스터를 선택 합니다.
1. 클러스터가 실행 되 고 있지 않으면 시작 합니다.
1. 노트북을 시작 하려면 실행 단추를 선택 합니다.

마지막 셀에 오류가 표시 될 수 있습니다. 그렇다면 이전 셀이 실행 중인지 확인 하 고, 일부 데이터가 저장소에 기록 될 때까지 잠시 기다린 후 마지막 셀을 다시 실행 합니다.

### <a name="view-smoothed-data"></a>완만 한 데이터 보기

노트북에서 14 셀까지 아래로 스크롤하여 장치 유형별 롤링 평균 습도 플롯을 표시 합니다. 이 그림은 스트리밍 원격 분석이 도착할 때 지속적으로 업데이트 됩니다.

![곡선 원격 분석 플롯](media/howto-create-custom-analytics/telemetry-plot.png)

노트북에서 차트의 크기를 조정할 수 있습니다.

### <a name="view-box-plots"></a>보기 상자 그림

노트북에서 20 셀까지 아래로 스크롤하여 [상자 플롯](https://en.wikipedia.org/wiki/Box_plot)을 표시 합니다. 상자 플롯은 정적 데이터를 기반으로 하므로 업데이트 하려면 셀을 다시 실행 해야 합니다.

![상자 그림](media/howto-create-custom-analytics/box-plots.png)

노트북에서 플롯의 크기를 조정할 수 있습니다.

## <a name="tidy-up"></a>정리

이 방법 및 불필요 한 비용을 방지 하기 위해 Azure Portal에서 **IoTCentralAnalysis** 리소스 그룹을 삭제 합니다.

응용 프로그램 내의 **관리** 페이지에서 IoT Central 응용 프로그램을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* *연속 데이터 내보내기를*사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* 원격 분석 데이터를 분석 및 플롯 하는 Azure Databricks 환경을 만듭니다.

이제 사용자 지정 분석을 만드는 방법을 배웠으므로 제안 된 다음 단계는 [응용 프로그램을 관리](howto-administer.md)하는 방법을 배우는 것입니다.

---
title: Data Lake Store에 원격 모니터링 솔루션 통합 - Azure | Microsoft Docs
description: Azure Stream Analytics 작업을 사용하여 원격 모니터링 솔루션을 Azure Data Lake Store와 통합하는 방법을 알아봅니다.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 021f18f588613817110539d408f9260fb9247895
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449503"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>원격 모니터링 솔루션을 Azure Data Lake Store와 통합

원격 모니터링 솔루션에서 제공되는 것 이상의 고급 분석 요구 사항이 있을 수 있습니다. Azure Data Lake Store는 다양한 대규모 데이터 세트에서 데이터를 저장할 수 있을 뿐만 아니라 Azure Data Lake Analytics와 통합하여 주문형 분석을 제공하기 때문에 이 애플리케이션에 이상적입니다.

이 방법에서는 Azure Stream Analytics 작업을 사용하여 원격 모니터링 솔루션의 IoT 허브에서 Azure Data Lake Store로 데이터를 스트리밍합니다.

## <a name="prerequisites"></a>필수 조건

이 방법을 완료하려면 다음이 필요합니다.

* [원격 모니터링 솔루션 가속기 배포](quickstart-remote-monitoring-deploy.md).
  * 원격 모니터링 솔루션은 이 문서에서 Azure 구독에 사용된 IoT 허브 및 Azure Stream Analytics 작업을 배포합니다.
* [Azure Data Lake Store 배포](../data-lake-store/data-lake-store-get-started-portal.md)
  * Data Lake Store는 원격 모니터링 솔루션과 동일한 지역에 배포되어야 합니다.
  * 계정에 "스트리밍"이라는 [폴더를 만듭니다](../data-lake-store/data-lake-store-get-started-portal.md#createfolder).

## <a name="create-a-consumer-group"></a>소비자 그룹 만들기

원격 모니터링 솔루션의 IoT 허브에 전용 소비자 그룹을 만듭니다. 이 그룹은 Stream Analytics 작업에서 데이터를 Data Lake Store로 스트리밍하는 데 사용됩니다.

> [!NOTE]
> 소비자 그룹은 애플리케이션에서 Azure IoT Hub의 데이터를 끌어오는 데 사용됩니다. 출력 소비자 5명마다 새로운 소비자 그룹을 만들어야 합니다. 최대 32개의 소비자 그룹까지 만들 수 있습니다.

1. Azure 포털에 로그인합니다.

1. Azure Portal에서 **Cloud Shell** 단추를 클릭합니다.

    ![포털 시작 아이콘](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. 이 명령을 실행하여 새 소비자 그룹을 만듭니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> 원격 모니터링 솔루션의 리소스 그룹 및 IoT 허브 이름을 사용합니다.

## <a name="create-stream-analytics-job"></a>Stream Analytics 작업 만들기

Azure Stream Analytics 작업을 만들어 IoT 허브에서 Azure Data Lake Store로 데이터를 스트리밍합니다.

1. **리소스 만들기**를 클릭하고, 마켓플레이스에서 사물 인터넷을 선택한 다음 **Stream Analytics 작업**을 클릭합니다.

    ![새 Stream Analytics 작업](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. 작업 이름을 입력하고 적절한 구독 및 리소스 그룹을 선택합니다.

1. Data Lake Store와 가까운 또는 동일한 지역에서 위치를 선택합니다. 여기 우리는 미국 동부를 사용합니다.

1. 호스팅 환경을 기본 **클라우드**로 유지하도록 해야 합니다.

1. **만들기**를 클릭합니다.

    ![Stream Analytics 작업 만들기](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Stream Analytics 작업 구성

1. 원격 모니터링 솔루션 자원 그룹에 있는 **Stream Analytics 작업**으로 이동합니다.

1. 개요 페이지에서 **입력**을 클릭합니다.

    ![개요 페이지](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. **스트림 입력 추가**를 클릭하고 드롭 다운에서 **IoT Hub**를 선택합니다.

    ![입력 추가](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. 새 입력 탭에서 **IoTHub**의 입력 별칭을 입력합니다.

1. 소비자 그룹 드롭 다운에서 이전에 만든 소비자 그룹을 선택합니다. 여기에서는 **streamanalyticsjob**을 사용합니다.

    ![입력 선택](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. **저장**을 클릭합니다.

1. 개요 페이지에서 **Outputs**을 클릭합니다.

    ![Data Lake Store 추가](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. **추가**를 클릭하고 드롭 다운에서 **Data Lake Store**를 선택합니다.

    ![출력 추가](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. 새 출력 탭에서 **DataLakeStore**의 출력 별칭을 입력합니다.

1. 이전 단계에서 만든 Data Lake Store 계정을 선택하고 폴더 구조를 제공하여 데이터를 저장소로 스트리밍합니다.

1. 날짜 형식 필드에 **/streaming/{date}/{time}** 를 입력합니다. YYYY/MM/DD의 기본 날짜 형식과 HH의 시간 형식을 그대로 둡니다.

    ![폴더 구조 제공](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. **권한 부여**를 클릭합니다.

    Stream Analytics 작업에 파일 시스템에 대한 쓰기 권한을 부여하려면 Data Lake Store를 사용하여 권한을 부여해야합니다.

    ![Stream Analytics에 Data Lake Store에 대한 권한 부여](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    팝업 창이 나타날 것이며 팝업 창이 닫히고 권한 부여가 완료된 후 권한 부여 단추가 회색으로 표시됩니다.

    > [!NOTE]
    > 팝업 창에 오류가 표시되면 새 브라우저 창을 Incognito Mode로 열고 다시 시도합니다.

1. **저장**을 클릭합니다.

## <a name="edit-the-stream-analytics-query"></a>Stream Analytics 쿼리 편집

Azure Stream Analytics는 SQL과 유사한 쿼리 언어를 사용하여 데이터를 스트리밍하고, 원하는대로 데이터를 변환하고, 다양한 저장소 또는 처리 대상에 출력하는 입력 소스를 지정합니다.

1. 개요 탭에서 **쿼리 편집**을 클릭합니다.

    ![쿼리 편집](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. 쿼리 편집기에서 [YourOutputAlias] 및 [YourInputAlias] 자리 표시자를 이전에 정의한 값으로 바꿉니다.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics 쿼리](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. **저장**을 클릭합니다.
1. **예**을 클릭하여 변경 내용을 허용합니다.

## <a name="start-the-stream-analytics-job"></a>Stream Analytics 작업 시작

1. 개요 탭에서 **시작**을 클릭합니다.

    ![Stream Analytics 작업을 시작합니다.](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Start 작업 탭에서 **사용자 지정**을 클릭합니다.

1. 디바이스가 스트리밍을 시작한 때부터의 데이터를 선택하려면 시간을 몇 시간 전으로 되돌아가도록 사용자 지정할 수 있습니다.

1. **시작**을 클릭합니다.

    ![사용자 지정 날짜 선택](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    작업이 실행 중 상태가 될 때까지 기다립니다. 오류가 발생한다면 쿼리 때문일 수 있으니 구문이 올바른지 확인해야 합니다.

    ![작업 실행 중](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    스트리밍 작업은 IoT Hub에서 데이터를 읽고 Data Lake Store에 데이터를 저장하기 시작합니다. Data Lake Store에 데이터가 나타나기 시작하려면 몇 분이 걸릴 수도 있습니다.

## <a name="explore-the-streaming-data"></a>스트리밍 데이터 탐색

1. Data Lake Store로 이동

1. 개요 탭에서 **데이터 탐색기**를 클릭합니다.

1. 데이터 탐색기에서 **/streaming** 폴더로 드릴다운합니다. YYYY/MM/DD/HH 형식으로 만들어진 폴더가 표시됩니다.

    ![스트리밍 데이터 탐색](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    json 파일이 시간당 한 파일 꼴로 나타납니다.

    ![스트리밍 데이터 탐색](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>다음 단계

Azure Data Lake Analytics를 사용하여 Data Lake Store 데이터 세트에서 빅 데이터 분석을 수행할 수 있습니다. 자세한 내용은 [Data Lake Analytics 문서](https://docs.microsoft.com/azure/data-lake-analytics)를 참조하세요.

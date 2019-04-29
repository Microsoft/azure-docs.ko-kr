---
title: Azure Stream Analytics를 사용하여 Power BI 대시보드 통합
description: 이 아티클에서는 실시간 Power BI 대시보드를 사용하여 Azure Stream Analytics 작업에서 데이터를 시각화하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 487c142400dc2bfa6f44e17963535051af017196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818013"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>자습서: Stream Analytics 및 Power BI: 스트리밍 데이터에 대한 실시간 분석 대시보드
Azure Stream Analytics를 사용하면 최고의 비즈니스 인텔리전스 도구 중 하나인 [Microsoft Power BI](https://powerbi.com/)를 이용할 수 있습니다. 이 문서에서는 Azure Stream Analytics 작업에 대한 출력으로 Power BI를 사용하여 비즈니스 인텔리전스 도구를 만드는 방법에 대해 알아봅니다. 실시간 대시보드를 만들고 사용하는 방법도 학습합니다.

이 문서는 Stream Analytics [실시간 사기 감지](stream-analytics-real-time-fraud-detection.md) 자습서로부터 내용이 이어집니다. 해당 자습서에서 만든 워크플로를 기반으로 하고 Power BI 출력을 추가하여 Streaming Analytics 작업에서 감지한 사기성 전화를 시각화할 수 있습니다. 

이 시나리오를 보여주는 [비디오](https://www.youtube.com/watch?v=SGUpT-a99MA)를 시청할 수 있습니다.


## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음이 필요합니다.

* Azure 계정.
* Power BI에 대한 계정. 회사 계정 또는 학교 계정을 사용할 수 있습니다.
* [실시간 사기 감지](stream-analytics-real-time-fraud-detection.md) 자습서의 완료된 버전. 자습서에는 가상의 전화 통화 메타데이터를 생성하는 앱이 포함됩니다. 자습서에서 이벤트 허브를 만들고 스트리밍 전화 통화 데이터를 이벤트 허브로 보냅니다. 사기성 호출(서로 다른 위치에서 동시에 같은 번호에서 발신되는 전화)을 감지하는 쿼리를 작성합니다. 


## <a name="add-power-bi-output"></a>Power BI 출력 추가
실시간 사기 감지 자습서에서 출력은 Azure Blob Storage로 전송됩니다. 이 섹션에서는 Power BI로 정보를 보내는 출력을 추가합니다.

1. Azure Portal에서 이전에 만든 Streaming Analytics 작업을 엽니다. 제안된 이름을 사용한 경우 작업 이름은 `sa_frauddetection_job_demo`입니다.

2. 작업 대시보드 중간에 있는 **출력** 상자를 선택한 후 **+ 추가**를 선택합니다.

3. **출력 별칭**에 `CallStream-PowerBI`를 입력합니다. 다른 이름을 사용할 수 있습니다. 이 경우 나중에 이름이 필요하기 때문에 메모해 둡니다. 

4. **싱크** 아래에서 **Power BI**를 선택합니다.

   ![Power BI에 대한 출력 만들기](./media/stream-analytics-power-bi-dashboard/create-power-bi-ouptut.png)

5. **권한 부여**를 클릭합니다.

    회사 또는 학교 계정에 대한 Azure 자격 증명을 제공할 수 있는 창이 열립니다. 

    ![Power BI에 액세스하기 위한 자격 증명 입력](./media/stream-analytics-power-bi-dashboard/power-bi-authorization-credentials.png)

6. 자격 증명을 입력합니다. 자격 증명을 입력하면 Streaming Analytic 작업에 권한을 부여하여 Power BI 영역에 액세스할 수 있습니다.

7. **새 출력** 블레이드로 돌아가면 다음 정보를 입력합니다.

   * **그룹 작업 영역**: 데이터 세트를 만들려는 Power BI 테넌트에서 작업 영역을 선택합니다.
   * **데이터 세트 이름**:  [https://slack.botframework.com](`sa-dataset`) 을 입력합니다. 다른 이름을 사용할 수 있습니다. 이 경우 나중을 위해 기록해 둡니다.
   * **테이블 이름**: [https://slack.botframework.com](`fraudulent-calls`) 을 입력합니다. 현재, Stream Analytics 작업의 Power BI 출력에는 하나의 데이터 세트에 하나의 테이블만 있을 수 있습니다.

     ![Power BI 작업 영역 데이터 세트 및 테이블](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

     > [!WARNING]
     > 이 Stream Analytics 작업에서 지정한 것과 동일한 이름의 데이터 세트와 테이블이 Power BI에 있는 경우에는 기존 항목을 덮어씁니다.
     > 이 데이터 세트 및 테이블을 Power BI 계정에 명시적으로 만들지 않는 것이 좋습니다. 이들은 Stream Analytics 작업을 시작하고 작업이 Power BI로 출력을 시작할 때 자동으로 생성됩니다. 작업 쿼리가 어떤 결과도 반환하지 않으면 데이터 세트 및 테이블은 생성되지 않습니다.
     >

8. **만들기**를 클릭합니다.

데이터 세트는 다음과 같은 설정으로 만들어집니다.

* **defaultRetentionPolicy: BasicFIFO**: 데이터는 FIFO이고, 최대 200,000개의 행이 있습니다.
* **defaultMode: pushStreaming**: 데이터 세트가 스트리밍 타일 및 기존의 보고서 기반 시각적 개체(즉, 푸시)를 모두 지원합니다.

지금은 다른 플래그로 데이터 세트를 만들 수 없습니다.

Power BI 데이터 세트에 대한 자세한 내용은 [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx)를 참조하세요.


## <a name="write-the-query"></a>쿼리 작성

1. **출력** 블레이드를 닫고 작업 블레이드로 돌아갑니다.

2. **쿼리** 상자를 클릭합니다. 

3. 다음 쿼리를 입력합니다. 이 쿼리는 사기 감지 자습서에서 만든 셀프 조인 쿼리와 유사합니다. 차이점은 이 쿼리에서 사용자가 만든 새 출력으로 결과는 보낸다는 점입니다(`CallStream-PowerBI`). 

    >[!NOTE]
    >사기 감지 자습서에서 입력 `CallStream`에 이름을 지정하지 않으면 쿼리에서 **FROM** 및 **JOIN** 절의 `CallStream` 이름을 바꿉니다.

        ```SQL
        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))
        ```

4. **저장**을 클릭합니다.


## <a name="test-the-query"></a>쿼리 테스트
이 섹션은 선택 사항이지만 권장됩니다. 

1. TelcoStreaming 앱은 현재 실행 중이 아니며 다음 단계에 따라 시작합니다.

    * 명령 창을 엽니다.
    * telcogenerator.exe 및 수정된 telcodatagen.exe.config 파일이 있는 폴더로 이동합니다.
    * 다음 명령 실행:

       `telcodatagen.exe 1000 .2 2`

2. **쿼리** 블레이드에서 `CallStream` 입력 옆에 있는 점을 클릭한 후 **입력의 샘플 데이터**를 선택합니다.

3. 3분 분량의 데이터를 원하는 것으로 지정하고 **확인**을 클릭합니다. 데이터가 샘플링되었다는 알림을 받을 때까지 기다립니다.

4. **테스트**를 클릭하고 결과를 가져오는지 확인합니다.


## <a name="run-the-job"></a>작업 실행

1. TelcoStreaming 앱 실행되고 있는지 확인합니다.

2. **쿼리** 블레이드를 닫습니다.

3. 작업 블레이드에서 **시작**을 클릭합니다.

    ![Stream Analytics 작업 시작](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Streaming Analytics 작업이 들어오는 스트림에서 사기성 호출을 검색합니다. 작업은 Power BI에서 데이터 세트 및 테이블을 만들고 사기성 호출에 대한 데이터 보내기를 시작합니다.


## <a name="create-the-dashboard-in-power-bi"></a>Power BI에서 대시보드 만들기

1. [Powerbi.com](https://powerbi.com)으로 이동한 다음 회사 또는 학교 계정으로 로그인합니다. Stream Analytics 작업 쿼리가 결과를 출력하면, 사용자 데이터 세트가 이미 생성되어 표시됩니다.

    ![Power BI의 스트리밍 데이터 세트 위치](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. 작업 영역에서 **+&nbsp;만들기**를 클릭합니다.

    ![Power BI 작업 영역에서 만들기 단추](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. 새 대시보드를 만들고 이름을 `Fraudulent Calls`로 지정합니다.

    ![대시보드를 만들고 Power BI 작업 영역에서 이름 지정](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. 창의 맨 위에서 **타일 추가**를 클릭하고 **사용자 지정 스트리밍 데이터**를 선택한 후 **다음**을 클릭합니다.

    ![Power BI의 사용자 지정 스트리밍 데이터 세트 타일](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. **데이터 세트** 아래에서 데이터 세트를 선택하고 **다음**을 클릭합니다.

    ![Power BI의 스트리밍 데이터 세트](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. **시각화 유형** 아래에서 **카드**를 선택한 다음 **필드** 목록에서 **fraudulentcalls**를 선택합니다.

    ![새 타일에 대한 시각화 세부 정보](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. **다음**을 클릭합니다.

8. 제목 및 부제목 같은 타일 세부 정보를 입력합니다.

    ![새 타일에 대한 제목 및 부제목](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. **적용**을 클릭합니다.

    이제 사기 카운터가 나타납니다.

    ![Power BI 대시보드의 사기 카운터](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. 타일을 추가하는 단계를 다시 진행합니다(4단계부터 시작). 이때 다음을 수행합니다.

    * **시각화 유형**으로 이동하여 **꺾은선형 차트**를 선택합니다. 
    * 축을 추가하고 **windowend**를 선택합니다. 
    * 값을 추가하고 **fraudulentcalls**를 선택합니다.
    * **표시할 시간 창**에 지난 10분을 선택합니다.

      ![Power BI에서 꺾은선형 차트 타일 만들기](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. **다음**을 클릭하고 제목 및 부제목을 추가하고 **적용**을 클릭합니다.

     이제 Power BI 대시보드에 스트리밍 데이터에서 감지된 대로 사기성 호출에 대한 두 가지 데이터 보기가 표시됩니다.

     ![사기성 호출에 대한 두 가지 타일을 보여 주는 완료된 Power BI 대시보드](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Power BI에 대해 자세히 알아보기

이 자습서에서는 데이터 세트에 대해 몇 가지 종류의 차트를 만드는 방법만 설명합니다. Power BI는 조직에 대한 다른 고객 비즈니스 인텔리전스 도구를 만드는 데 도움이 됩니다. 자세한 개념은 다음 리소스를 참조하세요.

* Power BI 대시보드의 다른 예제는 [Power BI 시작](https://youtu.be/L-Z_6P56aas?t=1m58s) 비디오를 보세요.
* Power BI에 Streaming Analytics 작업 출력 구성 및 Power BI 그룹 사용에 대한 자세한 내용은 [Stream Analytics 출력](stream-analytics-define-outputs.md) 문서의 [Power BI](stream-analytics-define-outputs.md#power-bi) 섹션을 검토하세요. 
* 일반적으로 Power BI를 사용 하는 방법은 [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)를 참조하세요.


## <a name="learn-about-limitations-and-best-practices"></a>제한 사항 및 모범 사례에 대해 알아보기
현재는 대략 1초당 한 번 Power BI를 호출할 수 있습니다. 스트리밍 시각적 개체는 15KB의 패킷을 지원합니다. 이보다 크면 스트리밍 시각적 개체가 실패합니다(푸시는 계속 작동). 이러한 제한 사항 때문에 Power BI는 Azure Stream Analytics가 데이터 부하를 상당히 줄이는 경우에 가장 적합합니다. 연속 창 또는 도약 창을 사용하여 데이터 푸시가 최대 초당 한번의 푸시를 수행하고 쿼리가 처리량 요구 사항 범위 내에 있도록 하는 것이 좋습니다.

다음 수식을 사용하여 값을 초 단위로 계산하여 창에 제공할 수 있습니다.

![값을 컴퓨팅하여 기간(초)을 제공하는 수식](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

예를 들면 다음과 같습니다.

* 1,000대의 디바이스가 1초 간격으로 데이터를 보내고 있습니다.
* 시간당 1,000,000개의 행을 지원하는 Power BI Pro SKU를 사용하고 있습니다.
* 디바이스당 평균 데이터 양을 Power BI에 게시하려 합니다.

그 결과, 수식은 다음과 같습니다.

![예제 기준을 기반으로 한 수식](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

이 구성이 지정되면 원래 쿼리를 다음과 같이 변경할 수 있습니다.

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>권한 부여 갱신
작업을 만들거나 마지막으로 인증한 후에 암호가 변경된 경우 Power BI 계정을 다시 인증해야 합니다. Azure Multi-Factor Authentication가 Azure Active Directory(Azure AD) 테넌트에서 구성된 경우 2주마다 Power BI 권한 부여도 갱신해야 합니다. 갱신하지 않으면 작업 출력 부족 또는 작업 로그에 `Authenticate user error`와 같은 증상을 볼 수 있습니다.

마찬가지로 토큰이 만료된 후 작업이 시작되면 오류가 발생하고 작업이 실패합니다. 이 문제를 해결하려면 실행 중인 작업을 중지하고 Power BI 출력으로 이동합니다. 데이터 손실을 방지하기 위해 **권한 부여 갱신** 링크를 클릭한 다음 **마지막 중지 시간**부터 작업을 다시 시작합니다.

Power BI를 사용하여 권한 부여가 새로 고쳐지면 권한 부여 영역에 문제가 해결되었음을 나타내는 녹색 알림이 표시됩니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

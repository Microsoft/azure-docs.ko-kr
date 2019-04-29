---
title: Azure Application Insights에서 Stream Analytics를 사용하여 내보내기 | Microsoft Docs
description: Stream Analytics를 사용하면 Application Insights에서 내보내는 데이터를 지속적으로 변환, 필터링 및 라우팅할 수 있습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: mbullwin
ms.openlocfilehash: 58eaec32fee149c845dc77a83763f2fcd8133a06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901379"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Stream Analytics를 사용하여 Application Insights에서 내보낸 데이터 처리
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)는 [Application Insights에서 내보낸](export-telemetry.md) 데이터를 처리하는 위한 이상적인 도구입니다. Stream Analytics는 다양한 원본의 데이터를 가져와서 변환하고 필터링한 다음 다양한 싱크로 라우팅할 수 있습니다.

이 예제에서는 Application Insights에서 데이터를 가져오고, 필드 중 일부에 대해 이름을 바꾸고 처리하며, Power BI로 파이프하는 어댑터를 만듭니다.

> [!WARNING]
> [Power BI에서 Application Insights 데이터를 표시하는 데 권장되는 방법](../../azure-monitor/app/export-power-bi.md )이 있으며, 이 방법들은 훨씬 더 효율적이며 간편합니다. 여기서 설명하는 경로는 내보낸 데이터를 처리하는 방법을 보여 주기 위한 예로 사용했을 뿐입니다.
> 
> 

![SA를 통해 PBI로 내보내기에 대한 블록 다이어그램](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Azure에서 저장소 만들기
연속 내보내기는 항상 Azure Storage 계정에 데이터를 출력하므로 스토리지를 먼저 만들어야 합니다.

1. [Azure 포털](https://portal.azure.com)에서 구독에 "클래식" 저장소 계정을 만듭니다.
   
   ![Azure Portal에서 새로 만들기, 데이터, 저장소 선택](./media/export-stream-analytics/030.png)
2. 컨테이너 만들기
   
    ![새 저장소에서 컨테이너를 선택하고 컨테이너 타일, 추가를 차례로 클릭합니다.](./media/export-stream-analytics/040.png)
3. 저장소 액세스 키 복사
   
    스트림 분석 서비스에 대한 입력을 설정하려면 곧 이 키가 필요합니다.
   
    ![저장소에서 설정, 키를 열고 기본 액세스 키 복사](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure 저장소로 연속 내보내기 시작
[연속 내보내기](export-telemetry.md)는 Application Insights에서 Azure 저장소로 데이터를 이동합니다.

1. Azure 포털에서 애플리케이션에 대해 만든 Application Insights 리소스를 찾습니다.
   
    ![찾아보기, Application Insights, 애플리케이션 선택](./media/export-stream-analytics/050.png)
2. 연속 내보내기를 만듭니다.
   
    ![설정, 연속 내보내기, 추가를 차례로 선택](./media/export-stream-analytics/060.png)

    이전에 만든 저장소 계정을 선택합니다.

    ![내보내기 대상 설정](./media/export-stream-analytics/070.png)

    보려는 이벤트 유형을 설정합니다.

    ![이벤트 유형 선택](./media/export-stream-analytics/080.png)

1. 일부 데이터가 누적되도록 합니다. 한동안 사용자가 애플리케이션을 사용하도록 놓아둡니다. 원격 분석이 제공되어 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)에서 통계 차트가, [진단 검색](../../azure-monitor/app/diagnostic-search.md)에서 개별 이벤트가 표시됩니다. 
   
    또한 데이터를 저장소로 내보냅니다. 
2. 내보낸 데이터를 검사합니다. Visual Studio에서 **보기/클라우드 탐색기**를 선택하고 Azure/저장소를 엽니다. (이 메뉴 옵션이 없는 경우 Azure SDK를 설치해야 합니다. 새 프로젝트 대화 상자를 열고 시각적 개체 C# / 클라우드 / .NET용 Microsoft Azure SDK 가져오기를 엽니다.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    애플리케이션 이름 및 계측 키에서 파생된 경로 이름의 공통 부분을 적어 둡니다. 

이벤트는 JSON 형식으로 blob 파일에 기록됩니다. 각 파일에는 하나 이상의 이벤트가 있을 수 있습니다. 따라서 이벤트 데이터를 읽고 원하는 필드를 필터링하려고 합니다. 데이터로 온갖 종류의 작업을 수행할 수 있지만, 지금은 Stream Analytics를 사용하여 데이터를 Power BI로 파이프하려고 합니다.

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics 인스턴스 만들기
[Azure Portal](https://portal.azure.com/)에서 Azure Stream Analytics 서비스를 선택하고 새 Stream Analytics 작업을 만듭니다.

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

새 작업이 만들어질 때 **리소스로 이동**을 선택합니다.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>새 입력 추가

![](./media/export-stream-analytics/SA004.png)

연속 내보내기 Blob에서 입력을 가져오도록 설정합니다.

![](./media/export-stream-analytics/SA0005.png)

이제 앞에서 기록해 둔 Storage 계정의 기본 액세스 키가 필요합니다. 이 키를 Storage 계정 키로 설정합니다.

### <a name="set-path-prefix-pattern"></a>경로 접두사 패턴 설정

**날짜 형식을 YYYY-MM-DD(파선 포함)로 설정해야 합니다.**

전위 패턴은 Stream Analytics가 저장소에서 입력 파일을 찾는 위치를 지정합니다. 연속 내보내기에서 데이터를 저장하는 방법과 일치하도록 설정해야 합니다. 다음과 같이 설정합니다.

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

이 예제에서:

* `webapplication27`은 Application Insights 리소스의 이름으로, **모두 소문자**입니다.
* `1234...`는 **대시를 생략한**Application Insights 리소스의 계측 키입니다. 
* `PageViews` 는 분석하려는 데이터의 형식입니다. 사용 가능한 형식은 연속 내보내기에 설정한 필터에 따라 다릅니다. 내보낸 데이터를 검사하여 사용 가능한 다른 형식을 확인하고 [데이터 모델 내보내기](export-data-model.md)를 참조합니다.
* `/{date}/{time}` 은 문자로 기록된 패턴입니다.

> [!NOTE]
> 저장소를 검사하여 올바른 경로를 가져오는지 확인합니다.
> 

## <a name="add-new-output"></a>새 출력 추가
이제 작업 > **출력** > **추가**를 선택합니다.

![](./media/export-stream-analytics/SA006.png)


![새 채널을 선택하고, 출력, 추가, Power BI를 클릭합니다.](./media/export-stream-analytics/SA010.png)

**회사 또는 학교 계정** 을 제공하여 Stream Analytics에 Power BI 리소스에 대한 액세스 권한을 부여합니다. 그런 다음, 출력 및 대상 Power BI 데이터 세트와 테이블의 이름을 지정합니다.

## <a name="set-the-query"></a>쿼리 설정
쿼리는 입력에서 출력으로 번역을 제어합니다.

Test 함수를 사용하여 올바른 출력이 표시되는지 확인합니다. 입력 페이지에서 얻은 샘플 데이터를 제공합니다. 

### <a name="query-to-display-counts-of-events"></a>이벤트 수를 표시하는 쿼리
이 쿼리 붙여넣기:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* 내보내기 입력은 스트림 입력에 제공된 별칭입니다.
* pbi 출력은 정의한 출력 별칭입니다.
* 이벤트 이름은 중첩된 JSON 배열에 있으므로 [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) 를 사용합니다. 그런 다음 Select는 기간의 해당 이름이 있는 인스턴스의 수의 개수와 함께 이벤트 이름을 선택합니다. [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 절은 요소를 1분의 기간으로 그룹화합니다.

### <a name="query-to-display-metric-values"></a>메트릭 값을 표시하는 쿼리
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* 이 쿼리는 이벤트 시간과 메트릭 값을 가져오기 위해 메트릭 원격 분석을 드릴합니다. 메트릭 값은 배열 내부에 있으므로 OUTER APPLY GetElements 패턴을 사용하여 행을 추출합니다. 이 경우 "myMetric"은 메트릭 이름입니다. 

### <a name="query-to-include-values-of-dimension-properties"></a>차원 속성의 값을 포함하는 쿼리
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* 이 쿼리는 차원 배열에 고정된 인덱스의 특정 차원에 상관없이 차원 속성의 값을 포함합니다.

## <a name="run-the-job"></a>작업 실행
작업을 시작할 과거의 날짜를 선택할 수 있습니다. 

![작업을 선택하고 쿼리를 클릭합니다. 아래 샘플을 붙여넣습니다.](./media/export-stream-analytics/SA008.png)

작업이 실행 중인 동안 기다립니다.

## <a name="see-results-in-power-bi"></a>Power BI에 결과를 참조하세요.
> [!WARNING]
> [Power BI에서 Application Insights 데이터를 표시하는 데 권장되는 방법](../../azure-monitor/app/export-power-bi.md )이 있으며, 이 방법들은 훨씬 더 효율적이며 간편합니다. 여기서 설명하는 경로는 내보낸 데이터를 처리하는 방법을 보여 주기 위한 예로 사용했을 뿐입니다.
> 
> 

회사 또는 학교 계정을 사용하여 Power BI를 열고 Stream Analytics 작업의 출력으로 정의된 데이터 세트 및 테이블을 선택합니다.

![Power BI에서 데이터 세트와 필드를 선택합니다.](./media/export-stream-analytics/200.png)

이제 [Power BI](https://powerbi.microsoft.com)의 보고서 및 대시보드에서 이 데이터 세트를 사용할 수 있습니다.

![Power BI에서 데이터 세트와 필드를 선택합니다.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>데이터가 없나요?
* [날짜 형식](#set-path-prefix-pattern) 을 YYYY-MM-DD(대시 사용)로 정확하게 설정했는지 확인합니다.

## <a name="video"></a>비디오
Noam Ben Zeev에서는 Stream Analytics를 사용하여 내보낸 데이터를 처리하는 방법을 보여 줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>다음 단계
* [연속 내보내기](export-telemetry.md)
* [속성 형식 및 값에 대한 자세한 데이터 모델 참조입니다.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)


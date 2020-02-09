---
title: Azure Cosmos DB, Azure Analysis Services 및 Power BI를 사용 하 여 실시간 대시보드 만들기
description: Azure Cosmos DB 및 Azure Analysis Services를 사용 하 여 Power BI에서 라이브 날씨 대시보드를 만드는 방법에 대해 알아봅니다.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376594"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Azure Cosmos DB 및 Power BI를 사용 하 여 실시간 대시보드 만들기

이 문서에서는 Azure Cosmos DB 및 Azure Analysis Services를 사용 하 여 Power BI에서 라이브 날씨 대시보드를 만드는 데 필요한 단계를 설명 합니다. Power BI 대시보드는 지역에서 온도 및 강우량에 대한 실시간 정보를 표시 하는 차트를 표시 합니다.

## <a name="reporting-scenarios"></a>보고 시나리오

Azure Cosmos DB에 저장 된 데이터에 대한 보고 대시보드를 설정 하는 방법에는 여러 가지가 있습니다. 다음 표에서는 부실 요구 사항 및 데이터의 크기에 따라 각 시나리오에 대한 보고 설정에 대해 설명 합니다.


|시나리오 |설정 |
|---------|---------|
|1. 임시 보고서 생성 (새로 고침 안 함)    |  [가져오기 모드를 사용 하는 Power BI Azure Cosmos DB 커넥터](powerbi-visualize.md)       |
|2. 정기적인 새로 고침을 사용 하 여 임시 보고서 생성   |  [가져오기 모드를 사용 하 Azure Cosmos DB 커넥터 Power BI (정기 새로 고침 예약 됨)](powerbi-visualize.md)       |
|3. 대량 데이터 집합에 대한 보고 (< 10gb)     |  증분 새로 고침을 사용 하는 Power BI Azure Cosmos DB 커넥터       |
|4. 큰 데이터 집합에 대한 실시간 보고    |  직접 쿼리 + Azure Analysis Services를 사용 하는 Power BI Azure Analysis Services 커넥터 (Azure Cosmos DB 커넥터)       |
|5. 집계를 사용 하 여 라이브 데이터에 대한 보고     |  [직접 쿼리 + Azure Databricks + Cosmos DB Spark 커넥터를 사용 하는 Spark 커넥터를 Power BI 합니다.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. 대량 데이터 집합에 대한 집계를 사용 하 여 라이브 데이터에 대한 보고   |  직접 쿼리 + Azure Analysis Services + Azure Databricks + Cosmos DB Spark 커넥터를 사용 하는 Azure Analysis Services 커넥터를 Power BI 합니다.       |

시나리오 1과 2는 Azure Cosmos DB Power BI 커넥터를 사용 하 여 쉽게 설정할 수 있습니다. 이 문서에서는 시나리오 3 및 4에 대한 설정이 설명 되어 있습니다.

### <a name="power-bi-with-incremental-refresh"></a>증분 새로 고침을 사용 하는 Power BI

Power BI에는 증분 새로 고침을 구성할 수 있는 모드가 있습니다. 이 모드를 통해 Azure Analysis Services 파티션을 만들고 관리할 필요가 없습니다. 증분 새로 고침을 설정 하 여 대량 데이터 집합의 최신 업데이트만 필터링 할 수 있습니다. 그러나이 모드는 데이터 집합 제한이 10gb 인 Power BI Premium 서비스 에서만 작동 합니다.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure 분석 커넥터 + Azure Analysis Services

Azure Analysis Services는 클라우드에서 엔터프라이즈급 데이터 모델을 호스트 하는 완전히 관리 되는 platform as a service를 제공 합니다. 대량 데이터 집합을 Azure Cosmos DB Azure Analysis Services에 로드할 수 있습니다. 전체 데이터 집합을 항상 쿼리 하는 것을 방지 하기 위해 데이터 집합을 Azure Analysis Services 파티션으로 세분 하 여 서로 다른 빈도로 독립적으로 새로 고칠 수 있습니다.

## <a name="power-bi-incremental-refresh"></a>Power BI 증분 새로 고침

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>날씨 데이터를 Azure Cosmos DB에 수집

Azure Cosmos DB에 [날씨 데이터](https://catalog.data.gov/dataset/local-weather-archive) 를 로드 하도록 수집 파이프라인을 설정 합니다. [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) 작업을 설정 하 여 HTTP 원본 및 Cosmos DB 싱크를 통해 최신 날씨 데이터를 Azure Cosmos DB에 정기적으로 로드할 수 있습니다.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Azure Cosmos DB에 Power BI 연결

1. **Azure Cosmos 계정을 Power BI에 연결 하** 여 Power BI Desktop를 열고 Azure Cosmos DB 커넥터를 사용 하 여 올바른 데이터베이스 및 컨테이너를 선택 합니다.

   ![Azure Cosmos DB Power BI 커넥터](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **증분 새로 고침 구성** -데이터 집합에 대한 증분 새로 고침을 구성 하려면 [Power BI를 사용 하 여 증분 새로 고침](/power-bi/service-premium-incremental-refresh) 문서의 단계를 따릅니다. 다음 스크린샷에 표시 된 것 처럼 범위 **시작** 및 범위 **종료** 매개 변수를 추가 합니다.

   ![범위 매개 변수 구성](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   데이터 집합에는 텍스트 형식의 날짜 열이 있으므로 다음 필터를 사용 하려면 범위 **시작** 및 범위 **종료** 매개 변수를 변환 해야 합니다. **고급 편집기** 창에서 쿼리를 수정 하 고 다음 텍스트를 추가 하 여 범위 시작 및 범위 종료 매개 변수를 기준으로 행을 필터링 합니다.

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   원본 데이터 집합에 있는 열과 데이터 형식에 따라 범위 시작 및 범위 끝 필드를 적절 하 게 변경할 수 있습니다.

   
   |속성  |데이터 형식  |Filter  |
   |---------|---------|---------|
   |_ts     |   숫자      |  [_ts] > TotalSeconds #datetime (TotalSeconds (1970, 1, 1, 0, 0, 0)) 및 [_ts] < Duration. (범위 끝-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |날짜 (예:-2019-08-19)     |   문자열      | [Document. date] > DateTime. ToText (범위 시작, "yyyy-mm-dd") 및 [Document. date] < DateTime. ToText (범위 끝, "yyyy-mm-dd")        |
   |날짜 (예:-2019-08-11 12:00:00)   |  String       |  [Document. date] > DateTime. ToText (범위 시작, "yyyy-mm-dd HH: mm: ss") 및 [Document. date] < DateTime. ToText (범위 끝, "yyyy-mm-dd HH: mm: ss")       |


1. **새로 고침 정책 정의** -테이블에 대한 **상황에 맞는** 메뉴의 **증분 새로 고침** 탭으로 이동 하 여 새로 고침 정책을 정의 합니다. **매일** 새로 고치도록 새로 고침 정책을 설정 하 고 마지막 달 데이터를 저장 합니다.

   ![새로 고침 정책 정의](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   *M 쿼리를 접을*확인할 수 없다는 경고를 무시 합니다. Azure Cosmos DB 커넥터는 필터 쿼리를 접기 합니다.

1. **데이터를 로드 하 고 보고서를 생성** 합니다. 이전에 로드 한 데이터를 사용 하 여 온도 및 강우량 보고 하는 차트를 만듭니다.

   ![데이터 로드 및 보고서 생성](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Power BI premium에 보고서 게시** -증분 새로 고침은 프리미엄 전용 기능이 기 때문에 게시 대화 상자는 프리미엄 용량 에서만 작업 영역을 선택할 수 있습니다. 첫 번째 새로 고침은 기록 데이터를 가져오는 데 시간이 오래 걸릴 수 있습니다. 이후 데이터 새로 고침은 증분 새로 고침을 사용 하기 때문에 훨씬 빠릅니다.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure 분석 커넥터 + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>날씨 데이터를 Azure Cosmos DB에 수집 

Azure Cosmos DB에 [날씨 데이터](https://catalog.data.gov/dataset/local-weather-archive) 를 로드 하도록 수집 파이프라인을 설정 합니다. Azure Data Factory (ADF) 작업을 설정 하 여 HTTP 원본 및 Cosmos DB 싱크를 통해 최신 날씨 데이터를 Azure Cosmos DB에 정기적으로 로드할 수 있습니다.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Cosmos 계정에 Azure Analysis Services 연결

1. **새 Azure Analysis Services 클러스터** - 만들기 azure Cosmos 계정 및 Databricks 클러스터와 동일한 지역에[azure Analysis Services 인스턴스를 만듭니다](../analysis-services/analysis-services-create-server.md) .

1. **Visual studio** -  에서 새 Analysis Services 테이블 형식 프로젝트 만들기[SQL Server Data Tools (SSDT)를 설치](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) 하 고 visual studio에서 Analysis Services 테이블 형식 프로젝트를 만듭니다.

   ![Azure Analysis Services 프로젝트 만들기](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   **통합 작업 영역** 인스턴스를 선택 하 고 호환성 수준을 **SQL Server 2017/Azure Analysis Services (1400)** 로 설정 합니다.

   ![Azure Analysis Services 테이블 형식 모델 디자이너](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **데이터 원본 Azure Cosmos DB 추가** - **모델**> **데이터** > 원본**새 데이터 원본** 으로 이동 하 고 다음 스크린샷에 표시 된 것 처럼 Azure Cosmos DB 데이터 원본을 추가 합니다.

   ![Cosmos DB 데이터 원본 추가](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   **계정 URI**, **데이터베이스 이름**및 **컨테이너 이름을**제공 하 여 Azure Cosmos DB에 연결 합니다. 이제 Azure Cosmos container의 데이터를 Power BI로 가져왔는지 확인할 수 있습니다.

   ![Azure Cosmos DB 데이터 미리 보기](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Analysis Services 모델 생성** -쿼리 편집기를 열고 로드 된 데이터 집합을 최적화 하는 데 필요한 작업을 수행 합니다.

   * 날씨 관련 열 (온도 및 강우량)만 추출

   * 테이블에서 월 정보를 추출 합니다. 이 데이터는 다음 섹션에 설명 된 대로 파티션을 만드는 데 유용 합니다.

   * 온도 열을 숫자로 변환

   결과 M 식은 다음과 같습니다.

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   또한 온도 열의 데이터 형식을 10 진수로 변경 하 여 이러한 값을 Power BI으로 그릴 수 있는지 확인 합니다.

1. **Azure 분석 파티션 만들기** -Azure Analysis Services에서 파티션을 만들어 데이터 집합을 다른 빈도로 독립적으로 새로 고칠 수 있는 논리 파티션으로 나눕니다. 이 예에서는 데이터 집합을 가장 최근 달의 데이터로 나누고 다른 모든 항목을 분할 하는 두 개의 파티션을 만듭니다.

   ![Analysis services 파티션 만들기](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Azure Analysis Services에서 다음과 같은 두 개의 파티션을 만듭니다.

   * **최신 달** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **이력** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Azure Analysis Server에 모델 배포** -Azure Analysis Services 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **배포**를 선택 합니다. **배포 서버 속성** 창에서 서버 이름을 추가 합니다.

   ![Azure Analysis Services 모델 배포](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **파티션 새로 고침 및 병합 구성** -Azure Analysis Services 파티션을 독립적으로 처리할 수 있습니다. **최신 월** 파티션을 최신 데이터로 지속적으로 업데이트 하려면 새로 고침 간격을 5 분으로 설정 합니다. 기록 파티션에서 데이터를 새로 고칠 필요가 없습니다. 또한 최신 월 파티션을 기록 파티션에 통합 하 고 새로운 최신 월 파티션을 만드는 몇 가지 코드를 작성 해야 합니다.


## <a name="connect-power-bi-to-analysis-services"></a>Analysis Services에 Power BI 연결

1. **Azure Analysis Services 데이터베이스 커넥터를 사용 하 여 Azure Analysis Server에 연결** -다음 스크린샷에 표시 된 대로 **라이브 모드** 를 선택 하 고 Azure Analysis Services 인스턴스에 연결 합니다.

   ![Azure Analysis Services에서 데이터 가져오기](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **데이터 로드 및 보고서 생성** -이전에 로드 한 데이터를 사용 하 여 온도 및 강우량 보고 하는 차트를 만듭니다. 라이브 연결을 만드는 중 이므로 이전 단계에서 배포한 Azure Analysis Services 모델의 데이터에 대해 쿼리를 실행 해야 합니다. 온도 차트는 새 데이터가 Azure Cosmos DB 로드 된 후 5 분 이내에 업데이트 됩니다.

   ![데이터 로드 및 보고서 생성](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>다음 단계

* Power BI에 대한 자세한 내용은 [Power BI 시작](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)을 참조하세요.

* [Qlik Sense를 연결 하 여 데이터 Azure Cosmos DB 및 시각화](visualize-qlik-sense.md)
---
title: '자습서: 일괄 처리 검색 및 Power BI를 사용하여 변칙 시각화'
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API 및 Power BI를 사용하여 시계열 데이터 전체의 변칙을 시각화하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: aahi
ms.openlocfilehash: 527ce1c7d434ae94c91c78c865c00aa0687a73cb
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245505"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>자습서: 일괄 처리 검색 및 Power BI를 사용하여 변칙 시각화

이 자습서에서는 시계열 데이터 세트 내의 변칙을 일괄 처리 방식으로 찾습니다. Power BI Desktop을 사용하면 Excel 파일을 가져와서 Anomaly Detector API에 대한 데이터를 준비하고 통계적 변칙을 시각화할 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Power BI Desktop을 사용하여 시계열 데이터 세트 가져오기 및 변환
> * 일괄 처리 변칙 검색을 위해 Power BI Desktop과 Anomaly Detector API의 통합
> * 예상 값, 표시 값 및 변칙 검색 경계를 포함하여 데이터 내에서 검색된 변칙 시각화

## <a name="prerequisites"></a>필수 구성 요소
* [Azure 구독](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)(평가판으로 사용 가능)
* 시계열 데이터 요소가 포함된 Excel 파일(.xlsx). 이 빠른 시작의 예제 데이터는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)에서 찾을 수 있습니다.
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 작업은 나중에 빠른 시작에서 수행합니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>시계열 데이터 로드 및 형식 지정

시작하려면 Power BI Desktop을 열고, 필수 조건에서 다운로드한 시계열 데이터를 로드합니다. 이 Excel 파일에는 일련의 UTC(협정 세계시) 타임스탬프 및 값 쌍이 포함되어 있습니다.  

> [!NOTE]
> Power BI는 .csv 파일, SQL 데이터베이스, Azure Blob 스토리지 등과 같은 다양한 원본의 데이터를 사용할 수 있습니다.  

Power BI Desktop 주 창에서 **홈** 리본을 클릭합니다. 리본의 **외부 데이터** 그룹에서 **데이터 가져오기** 드롭다운 메뉴를 열고 **Excel**을 클릭합니다.

![Power BI의 "데이터 가져오기" 단추 이미지](../media/tutorials/power-bi-get-data-button.png)

대화 상자가 표시되면 예제 .xlsx 파일을 다운로드한 폴더로 이동하여 선택합니다. **탐색기** 대화 상자가 표시되면 **Sheet1**을 클릭한 다음, **편집**을 클릭합니다.

![Power BI의 데이터 원본 "탐색기" 화면 이미지](../media/tutorials/navigator-dialog-box.png)

Power BI에서는 첫 번째 열의 타임스탬프를 `Date/Time` 데이터 형식으로 변환합니다. Anomaly Detector API로 보내려면 이러한 타임스탬프를 텍스트로 변환해야 합니다. 파워 쿼리 편집기가 자동으로 열리지 않으면 홈 탭에서 **쿼리 편집**을 클릭합니다.

파워 쿼리 편집기에서 **변환** 리본을 클릭합니다. **열** 그룹에서 **데이터 형식:** 드롭다운 메뉴를 열고, **텍스트**를 선택합니다.

![Power BI의 데이터 원본 "탐색기" 화면 이미지](../media/tutorials/data-type-drop-down.png)

열 형식 변경에 대한 알림이 표시되면 **현재 전환 바꾸기**를 클릭합니다. 그런 다음, **홈** 리본에서 **닫기 및 적용** 또는 **적용**을 클릭합니다.

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>데이터를 보내고 응답 형식을 지정하는 함수 만들기

데이터 파일의 형식을 지정하고 해당 파일을 Anomaly Detector API에 보내려면 위에서 만든 테이블에서 쿼리를 호출할 수 있습니다. 파워 쿼리 편집기의 **홈** 리본에서 **새 원본** 드롭다운 메뉴를 열고, **빈 쿼리**를 클릭합니다.

새 쿼리가 선택되었는지 확인한 다음, **고급 편집기**를 클릭합니다.

![Power BI의 "고급 편집기" 단추 이미지](../media/tutorials/advanced-editor-screen.png)

고급 편집기 내에서 다음 파워 쿼리 M 코드 조각을 사용하여 테이블에서 열을 추출하여 API에 보냅니다. 그런 다음, 쿼리는 JSON 응답에서 테이블을 만들고 이를 반환합니다. `apiKey` 변수를 유효한 Anomaly Detector API 키로 바꾸고, `endpoint`를 사용자의 엔드포인트로 바꿉니다. 쿼리를 고급 편집기에 입력한 다음, **완료**를 클릭합니다.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({

                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),

    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

**매개 변수 입력** 아래에서 `Sheet1`을 선택하고 **호출**을 클릭하여 데이터 시트에 대한 쿼리를 호출합니다.

!["고급 편집기" 단추의 이미지](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>데이터 원본 개인 정보 보호 및 인증

> [!NOTE]
> 데이터 개인 정보 보호 및 액세스에 대한 조직의 정책을 알고 있어야 합니다. 자세한 내용은 [Power BI Desktop 개인 정보 수준](https://docs.microsoft.com/power-bi/desktop-privacy-levels)을 참조하세요.

외부 데이터 원본을 활용하므로 쿼리를 실행하려고 하면 경고 메시지가 표시될 수 있습니다.

![Power BI에서 만든 경고를 보여 주는 이미지](../media/tutorials/blocked-function.png)

이 문제를 해결하려면 **파일** 및 **옵션 및 설정**을 클릭합니다. 그런 다음, **옵션**을 클릭합니다. **현재 파일** 아래에서 **개인 정보** 및 **개인 정보 수준을 무시하고 잠재적으로 성능 향상**을 선택합니다.

또한 API에 연결하려는 방법을 지정하라는 메시지가 표시될 수 있습니다.

![액세스 자격 증명을 지정하기 위한 요청을 보여 주는 이미지](../media/tutorials/edit-credentials-message.png)

이 문제를 해결하려면 메시지에서 **자격 증명 편집**을 클릭합니다. 대화 상자가 표시되면 **익명**을 선택하여 API에 익명으로 연결합니다. 그런 다음 **연결**을 클릭합니다.

그런 다음, **홈** 리본에서 **닫기 및 적용**을 클릭하여 변경 내용을 적용합니다.

## <a name="visualize-the-anomaly-detector-api-response"></a>Anomaly Detector API 응답 시각화

Power BI 주 화면에서 위에서 만든 쿼리를 사용하여 데이터 시각화를 시작합니다. 먼저 **시각화**에서 **꺾은선형 차트**를 선택합니다. 그런 다음, 호출된 함수의 타임스탬프를 꺾은선형 차트의 **축**에 추가합니다. 마우스 오른쪽 단추로 클릭하고 **타임스탬프**를 선택합니다.

![마우스 오른쪽 단추로 타임스탬프 값 클릭](../media/tutorials/timestamp-right-click.png)

**호출된 함수**의 다음 필드를 차트의 **값** 필드에 추가합니다. 아래 스크린샷을 사용하여 차트를 작성합니다.

* 값
* UpperMargins
* LowerMargins
* ExpectedValues

![새 빠른 측정 화면의 이미지](../media/tutorials/chart-settings.png)

필드가 추가되면 차트를 클릭하고, 모든 데이터 요소가 표시되도록 크기를 조정합니다. 차트는 아래 스크린샷과 비슷하게 표시됩니다.

![새 빠른 측정 화면의 이미지](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>변칙 데이터 요소 표시

Power BI 창의 오른쪽에 있는 **필드** 창 아래에서 마우스 오른쪽 단추로 **호출된 함수 쿼리** 아래의 **값**을 클릭하고, **새 빠른 측정**을 클릭합니다.

![새 빠른 측정 화면의 이미지](../media/tutorials/new-quick-measure.png)

표시되는 화면에서 계산으로 **필터링된 값**을 선택합니다. **기준 값**을 `Sum of Value`로 설정합니다. 그런 다음, `IsAnomaly`를 **호출된 함수** 필드에서 **필터**로 끕니다. **필터** 드롭다운 메뉴에서 `True`를 선택합니다.

![새 빠른 측정 화면의 이미지](../media/tutorials/new-quick-measure-2.png)

**확인**을 클릭하면 필드 목록의 아래쪽에 `Value for True` 필드가 표시됩니다. 마우스 오른쪽 단추로 클릭하고 이름을 **Anomaly**(변칙)로 변경합니다. 이를 차트의 **값**에 추가합니다. 그런 다음, **서식** 도구를 선택하고, X축 유형을 **범주별**로 설정합니다.

![새 빠른 측정 화면의 이미지](../media/tutorials/format-x-axis.png)

**서식** 도구 및 **데이터 색**을 클릭하여 차트에 색을 적용합니다. 차트가 다음과 같이 표시됩니다.

![새 빠른 측정 화면의 이미지](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Azure Databricks를 사용하여 스트리밍 변칙 검색](anomaly-detection-streaming-databricks.md)

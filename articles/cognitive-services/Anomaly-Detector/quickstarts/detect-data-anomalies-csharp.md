---
title: '빠른 시작: Anomaly Detector REST API와 C#을 사용하여 시계열 데이터에서 변칙 검색'
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API를 사용하여 일괄 처리 또는 스트리밍 데이터로 데이터 계열의 변칙을 검색하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a364588d77fb24e96c831ce541c5bb4e63d93e98
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922347"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>빠른 시작: Anomaly Detector REST API와 C#을 사용하여 시계열 데이터에서 변칙 검색

이 빠른 시작을 통해 Anomaly Detector API의 두 가지 검색 모드를 사용하여 시계열 데이터에서 변칙을 검색합니다. 이 C# 애플리케이션은 JSON 형식의 시계열 데이터가 포함된 2개의 API 요청을 보내고 응답을 받습니다.

| API 요청                                        | 애플리케이션 출력                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 일괄 처리로 변칙 검색                        | 시계열 데이터의 각 데이터 요소에 대한 변칙 상태(및 기타 데이터)와 검색된 변칙의 위치가 포함된 JSON 응답입니다. |
| 최신 데이터 요소의 변칙 상태 검색 | 시계열 데이터의 최신 데이터 요소에 대한 변칙 상태(및 기타 데이터)가 포함된 JSON 응답입니다.                                        |

 이 애플리케이션은 C#으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다. 이 빠른 시작의 소스 코드는 [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs)에서 찾을 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
- Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    - 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
- [Visual Studio 2017 이상](https://visualstudio.microsoft.com/downloads/)의 모든 버전
- NuGet 패키지로 사용 가능한 [Json.NET](https://www.newtonsoft.com/json) 프레임워크. Visual Studio에서 Newtonsoft.Json을 NuGet 패키지로 설치하려면 다음을 수행합니다.

    1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.
    2. **NuGet 패키지 관리**를 선택합니다.
    3. *Newtonsoft.Json*을 검색하고 패키지를 설치합니다.

- Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.

- 시계열 데이터 요소가 포함된 JSON 파일 이 빠른 시작의 예제 데이터는 [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json)에서 확인할 수 있습니다.

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. Visual Studio에서 새 콘솔 솔루션을 만들고 다음 패키지를 추가합니다.

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. 구독 키 및 엔드포인트에 대한 변수를 만듭니다. 아래는 변칙 검색에 사용할 수 있는 URI입니다. 나중에 API 요청 URL을 만드는 서비스 엔드포인트에 추가됩니다.

    | 검색 방법                   | URI                                              |
    |------------------------------------|--------------------------------------------------|
    | 일괄 검색                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | 최신 데이터 요소에서 검색 | `/anomalydetector/v1.0/timeseries/last/detect`   |

    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 위에서 만든 변수를 사용하는 `Request`라는 새 비동기 함수를 만듭니다.

2. `HttpClient` 개체를 사용하여 클라이언트의 보안 프로토콜과 헤더 정보를 설정합니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가해야 합니다. 그런 다음, 요청에 대한 `StringContent` 개체를 만듭니다.

3. `PostAsync()`를 사용하여 요청을 보낸 다음, 응답을 반환합니다.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>일괄 처리로 변칙 검색

1. `detectAnomaliesBatch()`라는 새 함수를 만듭니다. 요청을 구성하고 엔드포인트, 구독 키, 일괄 처리 변칙 검색을 위한 URL 및 시계열 데이터와 함께 `Request()` 함수를 호출하여 보냅니다.

2. JSON 개체를 역직렬화하여 콘솔에 작성합니다.

3. 응답에 `code` 필드가 포함된 경우에는 오류 코드 및 오류 메시지를 출력합니다.

4. 그렇지 않으면 데이터 세트에서 변칙의 위치를 찾습니다. 응답의 `isAnomaly` 필드에 부울 값 배열이 포함되고 이들 각각은 데이터 요소가 변칙인지 여부를 나타냅니다. 이것을 응답 개체의 `ToObject<bool[]>()` 함수를 사용하여 문자열 배열로 변환합니다. 배열을 반복하고 `true` 값의 인덱스를 출력합니다. 이러한 값은 변칙 데이터 요소의 인덱스와 일치합니다(발견된 경우).

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 변칙 상태 검색

1. `detectAnomaliesLatest()`라는 새 함수를 만듭니다. 요청을 구성하고 엔드포인트, 구독 키, 최신 포인트 변칙 검색을 위한 URL 및 시계열 데이터와 함께 `Request()` 함수를 호출하여 보냅니다.

2. JSON 개체를 역직렬화하여 콘솔에 작성합니다.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>시계열 데이터 로드 및 요청 보내기

1. 애플리케이션의 main 메서드에서 `File.ReadAllText()`를 사용하여 JSON 시계열 데이터를 로드합니다.

2. 위에서 만든 변칙 검색 함수를 호출합니다. `System.Console.ReadKey()`를 사용하여 애플리케이션을 실행한 후 콘솔 창을 열어 둡니다.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>예제 응답

성공 응답이 JSON 형식으로 반환됩니다. 아래 링크를 클릭하면 GitHub에서 JSON 응답을 볼 수 있습니다.
* [일괄 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [최신 요소 검색 응답 예제](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]

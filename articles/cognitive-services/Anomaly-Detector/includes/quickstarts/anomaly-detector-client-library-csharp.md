---
title: Anomaly Detector .NET 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 1a46cba6e3b74a2f8d4b63ab631830569c521291
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246236"
---
.NET용 Anomaly Detector 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Anomaly Detector 서비스를 사용하면 업계, 시나리오 또는 데이터 양에 관계없이 가장 적합한 모델을 자동으로 사용하여 시계열 데이터의 변칙을 찾을 수 있습니다.

.NET용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 일괄 요청으로 시계열 데이터 세트 전체에서 변칙 검색
* 시계열에서 최신 데이터 요소의 변칙 상태 검색

[라이브러리 참조 설명서](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.AnomalyDetector?view=azure-dotnet-preview) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.AnomalyDetector/) | [GitHub에서 코드 찾기](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-net-core-application"></a>새 .NET Core 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `anomaly-detector-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 단일 C# 소스 파일을 사용하여 간단한 "Hello World" 프로젝트를 만듭니다. *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```dotnetcli
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Anomaly Detector 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.AnomalyDetector --version 0.8.0-preview
```

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `directives`를 사용하여 다음을 추가합니다.

[!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=usingStatements)]

애플리케이션의 `main()` 메서드에서 리소스의 Azure 위치에 대한 변수와 환경 변수인 키를 만듭니다. 애플리케이션이 시작된 후 환경 변수를 만들었다면 애플리케이션을 실행 중인 편집기, IDE 또는 셸을 닫고 다시 로드해야 변수에 액세스할 수 있습니다.

[!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=mainMethod)]

## <a name="object-model"></a>개체 모델

Anomaly Detector 클라이언트는 키가 포함된 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials)를 사용하여 Azure에 인증하는 [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient) 개체입니다. 클라이언트는 다음과 같은 두 가지 변칙 검색 방법을 제공합니다. 전체 데이터 세트에 [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync) 사용, 최신 데이터 요소에 [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync) 사용.

시계열 데이터는 [요청](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request) 개체에 일련의 [포인트](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.series?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_Series)로 전송됩니다. `Request` 개체에는 데이터를 설명하는 속성(예: [Granularity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request.granularity)) 및 변칙 검색용 매개 변수가 포함됩니다.

Anomaly Detector 응답은 사용된 메서드에 따라 [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse) 또는 [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse) 개체입니다.

## <a name="code-examples"></a>코드 예제

이 코드 조각은 .NET용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [파일에서 시계열 데이터 세트 로드](#load-time-series-data-from-a-file)
* [전체 데이터 세트에서 변칙 검색](#detect-anomalies-in-the-entire-data-set)
* [최신 데이터 요소의 변칙 상태 검색](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>클라이언트 인증

새 메서드에서 엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.apikeyserviceclientcredentials?view=azure-dotnet-preview) 개체를 만들고, 엔드포인트에 이를 사용하여 [AnomalyDetectorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-dotnet-preview) 개체를 만듭니다.

[!code-csharp[Client authentication function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=createClient)]

## <a name="load-time-series-data-from-a-file"></a>파일에서 시계열 데이터 로드

[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)에서 이 빠른 시작의 예제 데이터를 다운로드합니다.
1. 브라우저에서 **Raw**를 마우스 오른쪽 단추로 클릭합니다.
2. **다른 이름으로 링크 저장**을 클릭합니다.
3. 해당 파일을 .csv 파일로 애플리케이션 디렉터리에 저장합니다.

이 시계열 데이터는 .csv 파일로 형식이 지정되며 Anomaly Detector API로 전송됩니다.

시계열 데이터를 읽고 [요청](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.request?view=azure-dotnet-preview) 개체에 추가하는 새 메서드를 만듭니다. 파일 경로를 사용하여 `File.ReadAllLines()`를 호출하고 [포인트](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.point?view=azure-dotnet-preview) 개체 목록을 만든 후 줄 바꿈 문자를 제거합니다. 값을 추출하고 숫자 값에서 datestamp를 분리한 후 새 `Point` 개체에 추가합니다.

일련의 포인트와 데이터 요소의 [세분성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview)(또는 주기성)에 해당하는 `Granularity.Daily`를 사용하여 `Request` 개체를 만듭니다.

[!code-csharp[load the time series data file](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=GetSeriesFromFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>전체 데이터 세트에서 변칙 검색

`Request` 개체를 사용하여 클라이언트의 [EntireDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.entiredetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_EntireDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) 메서드를 호출하는 메서드를 만들고 [EntireDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-dotnet-preview) 개체 응답을 기다립니다. 시계열에 변칙이 포함된 경우 응답의 [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.entiredetectresponse.isanomaly?view=azure-dotnet-preview) 값을 반복하고 `true`인 모든 값을 출력합니다. 이러한 값은 변칙 데이터 요소의 인덱스와 일치합니다(발견된 경우).

[!code-csharp[EntireDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=entireDatasetExample)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 변칙 상태 검색

`Request` 개체를 사용하여 클라이언트의 [LastDetectAsync()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.anomalydetectorclientextensions.lastdetectasync?view=azure-dotnet-preview#Microsoft_Azure_CognitiveServices_AnomalyDetector_AnomalyDetectorClientExtensions_LastDetectAsync_Microsoft_Azure_CognitiveServices_AnomalyDetector_IAnomalyDetectorClient_Microsoft_Azure_CognitiveServices_AnomalyDetector_Models_Request_System_Threading_CancellationToken_) 메서드를 호출하는 메서드를 만들고 [LastDetectResponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-dotnet-preview) 개체 응답을 기다립니다. 응답의 [IsAnomaly](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.lastdetectresponse.isanomaly?view=azure-dotnet-preview) 특성을 확인하여 전송된 최신 데이터 요소가 변칙인지 여부를 확인합니다.

[!code-csharp[LastDetectSampleAsync() function](~/samples-anomaly-detector/quickstarts/sdk/csharp-sdk-sample.cs?name=latestPointExample)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

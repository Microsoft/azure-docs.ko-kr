---
title: Anomaly Detector JavaScript 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 836582003c4b4bd47d2b90b845ae414210d16edd
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246502"
---
JavaScript용 Anomaly Detector 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Anomaly Detector 서비스를 사용하면 업계, 시나리오 또는 데이터 양에 관계없이 가장 적합한 모델을 자동으로 사용하여 시계열 데이터의 변칙을 찾을 수 있습니다.

JavaScript용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 일괄 요청으로 시계열 데이터 세트 전체에서 변칙 검색
* 시계열에서 최신 데이터 요소의 변칙 상태 검색

[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [패키지(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [GitHub에서 코드 찾기](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

파일 `index.js`를 만들고 다음 라이브러리를 가져옵니다.

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다. 이후 단계에서 다운로드할 예제 데이터 파일에 대한 다른 변수와 데이터 지점에 대한 빈 목록을 만듭니다. 그런 다음, 키를 포함할 `ApiKeyCredentials` 개체를 만듭니다.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`ms-rest-azure` 및 `azure-cognitiveservices-anomalydetector` NPM 패키지를 설치합니다. csv-구문 분석 라이브러리는 이 빠른 시작에서도 사용됩니다.

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

## <a name="object-model"></a>개체 모델

Anomaly Detector 클라이언트는 키를 사용하여 Azure에 인증하는 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 개체입니다. 클라이언트는 다음과 같은 두 가지 변칙 검색 방법을 제공합니다. 전체 데이터 세트에 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)를 사용하고, 최신 데이터 요소에 [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-)를 사용합니다. 

시계열 데이터는 [요청](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) 개체에 일련의 [포인트](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest)로 전송됩니다. `Request` 개체에는 데이터를 설명하는 속성(예: [Granularity](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity)) 및 변칙 검색용 매개 변수가 포함됩니다. 

Anomaly Detector 응답은 사용된 메서드에 따라 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 또는 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 개체입니다. 

## <a name="code-examples"></a>코드 예제 

이 코드 조각은 Node.js용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [파일에서 시계열 데이터 세트 로드](#load-time-series-data-from-a-file)
* [전체 데이터 세트에서 변칙 검색](#detect-anomalies-in-the-entire-data-set) 
* [최신 데이터 요소의 변칙 상태 검색](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>클라이언트 인증

엔드포인트와 자격 증명을 사용하여 [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) 개체를 인스턴스화합니다.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>파일에서 시계열 데이터 로드

[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv)에서 이 빠른 시작의 예제 데이터를 다운로드합니다.
1. 브라우저에서 **Raw**를 마우스 오른쪽 단추로 클릭합니다.
2. **다른 이름으로 링크 저장**을 클릭합니다.
3. 해당 파일을 .csv 파일로 애플리케이션 디렉터리에 저장합니다.

이 시계열 데이터는 .csv 파일로 형식이 지정되며 Anomaly Detector API로 전송됩니다.

csv-구문 분석 라이브러리의 `readFileSync()` 메서드를 사용하여 데이터 파일을 읽고 `parse()` 메서드를 사용하여 파일을 구문 분석합니다. 각 줄에서, 타임스탬프가 포함된 [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) 개체와 숫자 값을 푸시합니다.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>전체 데이터 세트에서 변칙 검색 

API를 호출하여 클라이언트의 [entireDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) 메서드를 통해 전체 시계열의 변칙을 일괄 처리로 검색합니다. 반환된 [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) 개체를 저장합니다. 응답의 `isAnomaly` 목록을 반복하고 `true` 값의 인덱스를 출력합니다. 이러한 값은 변칙 데이터 요소의 인덱스와 일치합니다(발견된 경우).

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 변칙 상태 검색

Anomaly Detector API를 호출하여 클라이언트의 [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) 메서드를 통해 최신 데이터 요소가 변칙인지 확인하고 반환된 [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) 개체를 저장합니다. 응답의 `isAnomaly` 값은 해당 포인트의 변칙 상태를 지정하는 부울 값입니다.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

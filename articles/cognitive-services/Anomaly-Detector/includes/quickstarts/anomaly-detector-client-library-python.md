---
title: Anomaly Detector Python 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: fd3f53520c3cd865fcbd0e38fffdfb14ecf03392
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246357"
---
Python용 Anomaly Detector 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Anomaly Detector 서비스를 사용하면 업계, 시나리오 또는 데이터 양에 관계없이 가장 적합한 모델을 자동으로 사용하여 시계열 데이터의 변칙을 찾을 수 있습니다.

Python용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 일괄 요청으로 시계열 데이터 세트 전체에서 변칙 검색
* 시계열에서 최신 데이터 요소의 변칙 상태 검색

[라이브러리 참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [패키지(PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [GitHub에서 샘플 코드 찾기](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.x](https://www.python.org/)
* [Pandas 데이터 분석 라이브러리](https://pandas.pydata.org/)
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector 리소스 만들기"  target="_blank">Anomaly Detector 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 Anomaly Detector API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.


## <a name="setting-up"></a>설치

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

 새 Python 파일을 만들고 다음 라이브러리를 가져옵니다.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

환경 변수, 시계열 데이터 파일의 경로 및 구독의 Azure 위치로 키에 대한 변수를 만듭니다. 예들 들어 `westus2`입니다.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>개체 모델

Anomaly Detector 클라이언트는 키를 사용하여 Azure에 인증하는 [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) 개체입니다. 클라이언트는 다음과 같은 두 가지 변칙 검색 방법을 제공합니다. 전체 데이터 세트에 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) 사용, 최신 데이터 요소에 [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) 사용.

시계열 데이터는 [요청](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) 개체에 일련의 [포인트](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python)로 전송됩니다. `Request` 개체에는 데이터를 설명하는 속성(예: [Granularity](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python)) 및 변칙 검색용 매개 변수가 포함됩니다.

Anomaly Detector 응답은 사용된 메서드에 따라 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 또는 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 개체입니다.

## <a name="code-examples"></a>코드 예제

이러한 코드 조각은 Python용 Anomaly Detector 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [파일에서 시계열 데이터 세트 로드](#load-time-series-data-from-a-file)
* [전체 데이터 세트에서 변칙 검색](#detect-anomalies-in-the-entire-data-set)
* [최신 데이터 요소의 변칙 상태 검색](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>클라이언트 인증

Azure 위치 변수를 엔드포인트에 추가하고 키를 사용하여 클라이언트를 인증합니다.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>파일에서 시계열 데이터 로드

[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv)에서 이 빠른 시작의 예제 데이터를 다운로드합니다.
1. 브라우저에서 **Raw**를 마우스 오른쪽 단추로 클릭합니다.
2. **다른 이름으로 링크 저장**을 클릭합니다.
3. 해당 파일을 .csv 파일로 애플리케이션 디렉터리에 저장합니다.

이 시계열 데이터는 .csv 파일로 형식이 지정되며 Anomaly Detector API로 전송됩니다.

Pandas 라이브러리의 `read_csv()` 메서드를 사용하여 데이터 파일을 로드하고 데이터 시리즈를 저장할 빈 목록 변수를 만듭니다. 파일 전체에서 반복하고 데이터를 [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) 개체로 추가합니다. 이 개체에는 .csv 데이터 파일의 행에 있는 타임스탬프와 숫자 값이 포함됩니다.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

시계열과 해당 데이터 포인트의 세분성(또는 [주기성](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python))을 사용하여 [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) 개체를 만듭니다. 예들 들어 `Granularity.daily`입니다.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>전체 데이터 세트에서 변칙 검색

API를 호출하여 클라이언트의 [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 전체 시계열 데이터에서 변칙을 검색합니다. 반환된 [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) 개체를 저장합니다. 응답의 `is_anomaly` 목록을 반복하고 `true` 값의 인덱스를 출력합니다. 이러한 값은 변칙 데이터 요소의 인덱스와 일치합니다(발견된 경우).

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>최신 데이터 요소의 변칙 상태 검색

Anomaly Detector API를 호출하여 클라이언트의 [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) 메서드를 사용하여 최신 데이터 요소가 변칙인지 확인하고 반환된 [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) 개체를 저장합니다. 응답의 `is_anomaly` 값은 해당 포인트의 변칙 상태를 지정하는 부울 값입니다.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>애플리케이션 실행

`python` 명령 및 파일 이름을 사용하여 애플리케이션을 실행합니다.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]

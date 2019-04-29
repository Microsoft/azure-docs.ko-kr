---
title: Azure Stream Analytics에서 Machine Learning 엔드포인트 사용
description: 이 아티클에서는 Azure Stream Analytics에서 컴퓨터 언어 사용자 정의 함수를 사용하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 4a360968285b91b00c8ca1999f81c11951e74482
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479850"
---
# <a name="machine-learning-integration-in-stream-analytics"></a>Stream Analytics의 Machine Learning 통합
Stream Analytics는 Azure Machine Learning 엔드포인트를 호출하는 사용자 정의 함수를 지원합니다. 이 기능에 대한 REST API 지원은 [Stream Analytics REST API 라이브러리](https://msdn.microsoft.com/library/azure/dn835031.aspx)에 자세히 설명되어 있습니다. 이 문서에서는 Stream Analytics에서 이 기능을 성공적으로 구현하기 위해 필요한 추가 정보를 제공합니다. 자습서도 게시되어 있으며 [여기](stream-analytics-machine-learning-integration-tutorial.md)서 확인할 수 있습니다.

## <a name="overview-azure-machine-learning-terminology"></a>개요: Azure Machine Learning 용어
Microsoft Azure Machine Learning은 데이터에 대한 예측 분석 솔루션을 빌드, 테스트, 배포할 수 있는 공동 끌어서 놓기 도구입니다. 이 도구를 *Azure Machine Learning Studio*라고 부릅니다. 이 스튜디오는 Machine Learning 리소스와 상호 작용하고 설계를 간편하게 빌드, 테스트 및 반복하는 데 사용됩니다. 이러한 리소스 및 해당 정의는 다음과 같습니다.

* **작업 영역**: *작업 영역*은 관리 및 제어를 위해 다른 모든 Machine Learning 리소스를 함께 보관하는 컨테이너입니다.
* **실험**: *실험*은 데이터 세트를 활용하고 기계 학습 모델을 교육하기 위해 데이터 과학자가 만듭니다.
* **엔드포인트**: *엔드포인트*는 기능을 입력으로 사용하고, 지정된 Machine Learning 모델을 적용하고, 채점된 출력을 반환하는 데 사용되는 Azure Machine Learning 개체입니다.
* **채점 웹 서비스**: *채점 웹 서비스* 는 위에 언급된 엔드포인트 컬렉션입니다.

각 엔드포인트에는 배치 실행 및 동기 실행을 위한 API가 있습니다. Stream Analytics은 동기 실행을 사용합니다. 특정 서비스의 이름은 Azure Machine Learning Studio에서 [요청/응답 서비스](../machine-learning/studio/consume-web-services.md)입니다.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Stream Analytics 작업에 필요한 Machine Learning 리소스
Stream Analytics 작업을 처리하려면 요청/응답 엔드포인트, [apikey](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)및 swagger 정의가 모두 있어야 성공적으로 실행됩니다. Stream Analytics에는 swagger 엔드포인트에 대한 url을 생성하고, 인터페이스를 조회하고, 사용자에게 기본 UDF 정의를 반환하는 추가 엔드포인트가 있습니다.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>REST API를 통해 Stream Analytics 및 Machine Learning UDF 구성
REST API를 사용하여 Azure 기계 언어 함수를 호출하는 작업을 구성할 수 있습니다. 단계는 다음과 같습니다.

1. Stream Analytics 작업 만들기
2. 입력 정의
3. 출력 정의
4. UDF(사용자 정의 함수) 만들기
5. UDF를 호출하는 Stream Analytics 변환 작성
6. 작업 시작

## <a name="creating-a-udf-with-basic-properties"></a>기본 속성을 사용하여 UDF 만들기
예를 들어 다음 샘플 코드는 Azure Machine Learning 엔드포인트에 바인딩되는 *newudf* 라고 하는 스칼라 UDF를 만듭니다. *엔드포인트*(서비스 URI)는 선택한 서비스에 대한 API 도움말 페이지에서 찾을 수 있고 *apiKey*는 서비스 기본 페이지에서 찾을 수 있습니다.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

요청 본문 예제:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>기본 UDF에 대한 RetrieveDefaultDefinition 엔드포인트 호출
기초 UDF를 만든 후에는 완전한 UDF 정의가 필요합니다. RetrieveDefaultDefinition 엔드포인트는 Azure Machine Learning 엔드포인트에 바인딩된 스칼라 함수에 대한 기본 정의를 가져오는 데 도움이 됩니다. 아래 페이로드는 Azure Machine Learning 엔드포인트에 바인딩된 스칼라 함수의 기본 UDF 정의를 필요로 합니다. PUT 요청 동안 이미 엔드포인트가 제공되었기 때문에 실제 엔드포인트를 지정하지 않습니다. 엔드포인트가 명시적으로 제공되면 Stream Analytics는 요청에 제공된 엔드포인트를 호출합니다. 그렇지 않으면 원래 참조하던 끝점을 사용합니다. 다음 UDF는 단일 문자열 매개 변수(문장)를 가져와서 해당 문장에 대한 “sentiment” 레이블의 단일 문자열 형식을 반환합니다.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

요청 본문 예제:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

이 샘플 출력은 아래와 비슷하게 표시됩니다.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>응답과 함께 UDF 패치
이제 아래와 같이 이전 응답과 함께 UDF를 패치해야 합니다.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

요청 본문(RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>UDF를 호출하는 Stream Analytics 변환 구현
이제 모든 입력 이벤트에 대해 UDF(여기서는 scoreTweet)를 쿼리하고 해당 이벤트에 대한 응답을 출력으로 작성해야 합니다.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

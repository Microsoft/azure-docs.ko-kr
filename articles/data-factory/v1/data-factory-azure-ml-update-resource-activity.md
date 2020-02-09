---
title: Azure Data Factory를 사용 하 여 Machine Learning 모델 업데이트
description: Azure Data Factory 및 Azure Machine Learning를 사용 하 여 예측 파이프라인을 만드는 방법을 설명 합니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: afc79badd19fa180e631f1f8fa9735567a0b1e33
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978716"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>업데이트 리소스 작업을 사용 하 여 Azure Machine Learning 모델 업데이트

> [!div class="op_single_selector" title1="변환 작업"]
> * [Hive 작업](data-factory-hive-activity.md) 
> * [Pig 활동](data-factory-pig-activity.md)
> * [MapReduce 작업](data-factory-map-reduce.md)
> * [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
> * [Spark 작업](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET 사용자 지정 작업](data-factory-use-custom-activities.md)


> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 최신 버전의 Data Factory 서비스를 사용 하는 경우 [Data Factory에서 machine learning 모델 업데이트](../update-machine-learning-models.md)를 참조 하세요.

이 문서에서는 [Azure Machine Learning 및 Azure Data Factory를 사용 하 여 예측 파이프라인 만들기](data-factory-azure-ml-batch-execution-activity.md)의 기본 Azure Data Factory Azure Machine Learning 통합 문서를 보완 합니다. 아직 수행 하지 않은 경우이 문서를 읽기 전에 주 문서를 검토 합니다. 

## <a name="overview"></a>Áttekintés
시간이 지남에 따라 Azure ML 점수 매기기 실험의 예측 모델은 새 입력 데이터 집합을 사용 하 여 다시 학습 해야 합니다. 재 학습을 완료 한 후에는 다시 학습 ML 모델을 사용 하 여 점수 매기기 웹 서비스를 업데이트 하려고 합니다. 웹 서비스를 통해 Azure ML 모델을 재 학습 하 고 업데이트 하는 일반적인 단계는 다음과 같습니다.

1. [Azure Machine Learning Studio (클래식)](https://studio.azureml.net)에서 실험을 만듭니다.
2. 모델에 만족 하는 경우 Azure Machine Learning Studio (클래식)를 사용 하 여 **학습 실험** 및 점수 매기기/**예측 실험**에 대 한 웹 서비스를 게시 합니다.

다음 표에서는이 예에 사용 된 웹 서비스에 대해 설명 합니다.  자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](../../machine-learning/machine-learning-retrain-models-programmatically.md) 을 참조 하세요.

- **학습 웹 서비스** -학습 데이터를 수신 하 고 학습 된 모델을 생성 합니다. 재 학습의 출력은 Azure Blob storage의 ilearner 파일입니다. **기본 엔드포인트** 은 학습 실험을 웹 서비스로 게시할 때 자동으로 만들어집니다. 더 많은 엔드포인트을 만들 수 있지만 예제에서는 기본 엔드포인트만 사용 합니다.
- **웹 서비스 점수 매기기** -레이블 없는 데이터 예를 수신 하 고 예측을 수행 합니다. 예측의 출력에는 실험의 구성에 따라 .csv 파일 또는 Azure SQL 데이터베이스의 행과 같은 다양 한 형식이 있을 수 있습니다. 예측 실험을 웹 서비스로 게시할 때 기본 엔드포인트이 자동으로 생성 됩니다. 

다음 그림에서는 Azure ML에서 학습 및 점수 매기기 엔드포인트 간의 관계를 보여 줍니다.

![Webszolgáltatások](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**AZURE ML Batch 실행 작업**을 사용 하 여 **학습 웹 서비스** 를 호출할 수 있습니다. 학습 웹 서비스를 호출 하는 것은 점수 매기기 데이터에 대해 Azure ML 웹 서비스 (점수 매기기 웹 서비스)를 호출 하는 것과 같습니다. 이전 섹션에서는 Azure Data Factory 파이프라인에서 Azure ML 웹 서비스를 호출 하는 방법에 대해 자세히 설명 합니다. 

**AZURE ML 업데이트 리소스 작업** 을 사용 하 여 새로 학습 된 모델로 웹 서비스를 업데이트 하 여 **점수 매기기 웹 서비스** 를 호출할 수 있습니다. 다음 예에서는 연결 된 서비스 정의를 제공 합니다. 

## <a name="scoring-web-service-is-a-classic-web-service"></a>웹 서비스 점수 매기기는 기존 웹 서비스입니다.
점수 매기기 웹 서비스가 **기존 웹 서비스인**경우 Azure Portal를 사용 하 여 두 번째 **기본이 아닌 업데이트 가능한 엔드포인트** 을 만듭니다. 단계는 [엔드포인트 만들기](../../machine-learning/machine-learning-create-endpoint.md) 문서를 참조 하세요. 기본이 아닌 업데이트 가능한 엔드포인트을 만든 후에는 다음 단계를 수행 합니다.

* **일괄 처리 실행** 을 클릭 하 여 **mlendpoint** JSON 속성에 대 한 URI 값을 가져옵니다.
* **리소스 업데이트** 링크를 클릭 하 여 **updateResourceEndpoint** JSON 속성에 대 한 URI 값을 가져옵니다. API 키는 엔드포인트 페이지 자체 (오른쪽 아래 모서리)에 있습니다.

![업데이트 가능한 엔드포인트](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

다음 예제에서는 AzureML 연결 된 서비스에 대 한 샘플 JSON 정의를 제공 합니다. 연결 된 서비스는 인증을 위해 apiKey를 사용 합니다.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>웹 서비스의 Azure Resource Manager 점수 매기기 웹 서비스 
웹 서비스가 Azure Resource Manager 엔드포인트을 노출 하는 새로운 유형의 웹 서비스인 경우 두 번째 **기본이 아닌** 엔드포인트을 추가할 필요가 없습니다. 연결 된 서비스의 **updateResourceEndpoint** 은 다음과 같은 형식입니다. 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

[Azure Machine Learning 웹 서비스 포털](https://services.azureml.net/)에서 웹 서비스를 쿼리할 때 URL에서 자리 표시자에 대 한 값을 가져올 수 있습니다. 새 형식의 업데이트 리소스 엔드포인트에는 AAD (Azure Active Directory) 토큰이 필요 합니다. Azure Machine Learning 연결 된 서비스에서 **servicePrincipalId** 및 **servicePrincipalKey** 를 지정 합니다. [서비스 주체를 만들고 Azure 리소스를 관리할 수 있는 권한을 할당 하는 방법을](../../active-directory/develop/howto-create-service-principal-portal.md)참조 하세요. 샘플 AzureML 연결 된 서비스 정의는 다음과 같습니다. 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

다음 시나리오에서는 자세한 정보를 제공 합니다. Azure Data Factory 파이프라인에서 Azure ML 모델을 재 학습 하 고 업데이트 하는 예제를 포함 합니다.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>시나리오: Azure ML 모델 재 학습 및 업데이트
이 섹션에서는 **AZURE ML Batch 실행 작업** 을 사용 하 여 모델을 다시 학습 하는 샘플 파이프라인을 제공 합니다. 파이프라인은 또한 **AZURE ML 업데이트 리소스 작업** 을 사용 하 여 점수 매기기 웹 서비스에서 모델을 업데이트 합니다. 또한이 섹션에서는 예제에 있는 모든 연결 된 서비스, 데이터 집합 및 파이프라인에 대 한 JSON 코드 조각을 제공 합니다.

샘플 파이프라인의 다이어그램 보기는 다음과 같습니다. 여기에서 볼 수 있듯이 Azure ML Batch 실행 작업은 학습 입력을 받아 학습 출력 (iLearner 파일)을 생성 합니다. Azure ML 업데이트 리소스 작업은이 학습 출력을 사용 하 여 점수 매기기 웹 서비스 엔드포인트에서 모델을 업데이트 합니다. 리소스 업데이트 작업은 출력을 생성 하지 않습니다. PlaceholderBlob는 Azure Data Factory 서비스에서 파이프라인을 실행 하는 데 필요한 더미 출력 데이터 집합 일 뿐입니다.

![파이프라인 다이어그램](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob 저장소 연결 된 서비스:
Azure Storage에는 다음 데이터가 포함 됩니다.

* 학습 데이터. Azure ML 학습 웹 서비스에 대 한 입력 데이터입니다.  
* iLearner 파일입니다. Azure ML 학습 웹 서비스의 출력입니다. 이 파일은 리소스 업데이트 작업에 대 한 입력 이기도 합니다.  

연결 된 서비스의 샘플 JSON 정의는 다음과 같습니다.

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>학습 입력 데이터 집합:
다음 데이터 집합은 Azure Machine Learning 교육 웹 서비스에 대 한 입력 학습 데이터를 나타냅니다. Azure Machine Learning Batch 실행 작업은이 데이터 집합을 입력으로 사용 합니다.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>출력 데이터 집합 학습:
다음 데이터 집합은 Azure ML 학습 웹 서비스의 출력 iLearner 파일을 나타냅니다. Azure ML Batch 실행 작업은이 데이터 집합을 생성 합니다. 이 데이터 집합은 Azure ML 업데이트 리소스 작업에 대 한 입력 이기도 합니다.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-machine-learning-training-endpoint"></a>Azure Machine Learning 교육 엔드포인트에 대 한 연결 된 서비스
다음 JSON 코드 조각은 학습 웹 서비스의 기본 엔드포인트을 가리키는 Azure Machine Learning 연결 된 서비스를 정의 합니다.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

**Azure Machine Learning Studio (클래식)** 에서 다음을 수행 하 여 **Mlendpoint** 및 **apikey**에 대 한 값을 가져옵니다.

1. 왼쪽 메뉴에서 **웹 서비스** 를 클릭 합니다.
2. 웹 서비스 목록에서 **교육 웹 서비스** 를 클릭 합니다.
3. **API 키** 텍스트 상자 옆의 복사를 클릭 합니다. 클립보드의 키를 Data Factory JSON 편집기에 붙여 넣습니다.
4. **Azure Machine Learning Studio (클래식)** 에서 **BATCH 실행** 링크를 클릭 합니다.
5. **요청 섹션에서** **요청 URI** 를 복사 하 여 Data Factory JSON 편집기에 붙여넣습니다.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML 업데이트 가능한 점수 매기기 엔드포인트에 대 한 연결 된 서비스:
다음 JSON 코드 조각은 점수 매기기 웹 서비스의 기본이 아닌 업데이트 가능한 엔드포인트을 가리키는 Azure Machine Learning 연결 된 서비스를 정의 합니다.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>자리 표시자 출력 데이터 집합:
Azure ML 업데이트 리소스 작업은 출력을 생성 하지 않습니다. 그러나 Azure Data Factory에는 파이프라인의 일정을 추진 하는 출력 데이터 집합이 필요 합니다. 따라서이 예제에서는 더미/자리 표시자 데이터 집합을 사용 합니다.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Folyamat
파이프라인에는 **AzureMLBatchExecution** 및 **AzureMLUpdateResource**의 두 가지 작업이 있습니다. Azure ML Batch 실행 작업은 학습 데이터를 입력으로 사용 하 고 iLearner 파일을 출력으로 생성 합니다. 활동은 입력 학습 데이터를 사용 하 여 학습 웹 서비스 (웹 서비스로 노출 된 학습 실험)를 호출 하 고 webservice에서 ilearner 파일을 받습니다. PlaceholderBlob는 Azure Data Factory 서비스에서 파이프라인을 실행 하는 데 필요한 더미 출력 데이터 집합 일 뿐입니다.

![파이프라인 다이어그램](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```

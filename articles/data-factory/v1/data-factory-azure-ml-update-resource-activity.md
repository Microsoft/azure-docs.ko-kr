---
title: Azure Data Factory를 사용하여 Machine Learning 모델 업데이트 | Microsoft Docs
description: Azure Data Factory 및 Azure Machine Learning을 사용하여 예측 파이프라인을 만드는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0c0e0e3983344bba76f5f305ecaf73f91110f3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567320"
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>업데이트 리소스 작업을 사용하여 Azure Machine Learning 모델 업데이트

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 작업](data-factory-hive-activity.md) 
> * [Pig 작업](data-factory-pig-activity.md)
> * [MapReduce 작업](data-factory-map-reduce.md)
> * [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
> * [Spark 작업](data-factory-spark.md)
> * [Machine Learning Batch 실행 작업](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 업데이트 리소스 작업](data-factory-azure-ml-update-resource-activity.md)
> * [저장 프로시저 작업](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 작업](data-factory-usql-activity.md)
> * [.NET 사용자 지정 작업](data-factory-use-custom-activities.md)


> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory에서 기계 학습 모델 업데이트](../update-machine-learning-models.md)를 참조하세요.

이 문서는 기본 Azure Data Factory - Azure Machine Learning 통합 문서인 [Azure Machine Learning 및 Azure Data Factory를 사용하여 예측 파이프라인 만들기](data-factory-azure-ml-batch-execution-activity.md)를 보완합니다. 수행하지 않았다면 이 문서를 읽기 전에 기본 문서를 검토하세요. 

## <a name="overview"></a>개요
시간이 지남에 따라 Azure ML 점수 매기기 실험의 예측 모델은 새 입력 데이터 세트를 사용하여 다시 학습되어야 합니다. 재학습으로 완료한 후에는 재학습한 ML 모델로 점수 매기기 웹 서비스를 업데이트하려고 합니다. 웹 서비스를 통해 Azure ML 모델을 재학습하고 업데이트하는 일반적인 단계는 다음과 같습니다.

1. [Azure ML Studio](https://studio.azureml.net)에서 실험을 만듭니다.
2. 모델에 만족하면 Azure ML Studio를 사용하여 **학습 실험** 및 점수 매기기/**예측 실험** 모두에 대해 웹 서비스를 게시합니다.

다음 표에서는 이 예제에 사용된 웹 서비스에 대해 설명합니다.  자세한 내용은 [프로그래밍 방식으로 Machine Learning 모델 다시 학습](../../machine-learning/machine-learning-retrain-models-programmatically.md)을 참조하세요.

- **학습 웹 서비스** - 학습 데이터를 수신하고 학습된 모델을 생성합니다. 재학습의 출력은 Azure Blob Storage에서 .ilearner 파일입니다. 웹 서비스로 학습 실험을 게시할 때 사용자에 대한 **기본 엔드포인트** 가 자동으로 만들어집니다. 더 많은 엔드포인트를 만들 수 있지만 예제에서는 기본 엔드포인트만 사용합니다.
- **점수 매기기 웹 서비스** - 레이블이 지정되지 않은 데이터 예제를 수신하고 예측을 합니다. 예측의 출력은 실험의 구성에 따라 .csv 파일 또는 Azure SQL 데이터베이스의 행과 같은 다양한 형태를 포함할 수 있습니다. 웹 서비스로 예측 실험을 게시할 때 사용자에 대한 기본 엔드포인트가 자동으로 만들어집니다. 

다음 그림에서는 Azure ML에서 학습 및 점수 매기기 엔드포인트 간의 관계를 보여줍니다.

![웹 서비스](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**Azure ML Batch 실행 작업**을 사용하여 **학습 웹 서비스**를 호출할 수 있습니다. 학습 웹 서비스를 호출하는 것은 점수 매기기 데이터에 대해 Azure ML 웹 서비스(점수 매기기 웹 서비스)를 호출하는 것과 동일합니다. 이전 섹션에서는 Azure Data Factory 파이프라인에서 Azure ML 웹 서비스를 호출하는 방법에 대해 자세히 설명합니다. 

**scoring web service** 을 사용하여 두 번째 **Azure ML 업데이트 리소스 작업** 을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트합니다. 다음 예제에서는 연결된 서비스 정의를 제공합니다. 

## <a name="scoring-web-service-is-a-classic-web-service"></a>웹 서비스 점수 매기기는 클래식 웹 서비스입니다.
웹 서비스 점수 매기기가 **클래식 웹 서비스**인 경우 Azure Portal을 사용하여 두 번째 **기본이 아닌 업데이트 가능한 엔드포인트**를 만들어야 합니다. 이에 대한 단계는 [엔드포인트 만들기](../../machine-learning/machine-learning-create-endpoint.md) 문서를 참조하세요. 기본이 아닌 업데이트 가능한 엔드포인트를 만든 후 다음 단계를 수행합니다.

* **배치 실행**을 클릭하여 **mlEndpoint** JSON 속성에 대한 URI 값을 가져옵니다.
* **업데이트 리소스** 링크를 클릭하여 **updateResourceEndpoint** JSON 속성에 대한 URI 값을 가져옵니다. API 키는 엔드포인트 페이지 자체의 오른쪽 하단에 있습니다.

![업데이트 가능한 엔드포인트](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

다음 예제에서는 AzureML 연결된 서비스에 샘플 JSON 정의를 제공합니다. 연결된 서비스는 인증을 위해 apiKey를 사용합니다.  

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

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>웹 서비스 점수 매기기는 Azure Resource Manager 웹 서비스입니다. 
웹 서비스가 Azure Resource Manager 엔드포인트를 노출하는 새로운 유형의 웹 서비스인 경우 두 번째 **기본이 아닌** 엔드포인트를 추가할 필요가 없습니다. 연결된 서비스의 **updateResourceEndpoint**는 다음 형식을 갖습니다. 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

[Azure Machine Learning 웹 서비스 포털](https://services.azureml.net/)에서 웹 서비스를 쿼리할 때 URL에 대한 자리 표시자 값을 가져올 수 있습니다. 새로운 유형의 업데이트 리소스 엔드포인트에는 AAD(Azure Active Directory) 토큰이 필요합니다. AzureML 연결된 서비스에서 **servicePrincipalId** 및 **servicePrincipalKey**를 지정합니다. [서비스 보안 주체 만들기 및 Azure 리소스를 관리하기 위한 권한 할당 방법](../../active-directory/develop/howto-create-service-principal-portal.md)을 참조하세요. 샘플 AzureML 연결된 서비스 정의는 다음과 같습니다. 

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

다음 시나리오는 보다 자세한 내용을 제공합니다. Azure Data Factory 파이프라인에서 Azure ML 모델을 재학습 및 업데이트하는 예제가 포함되어 있습니다.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>시나리오: Azure ML 모델 재학습 및 업데이트
이 섹션에서는 **Azure ML Batch 실행 작업** 을 사용하여 모델을 재학습하는 샘플 파이프라인을 제공합니다. 파이프라인은 또한 **Azure ML 업데이트 리소스 작업** 을 사용하여 점수 매기기 웹 서비스에서 모델을 업데이트합니다. 섹션에서는 또한 모든 연결된 서비스, 데이터 세트 및 파이프라인에 대한 JSON 코드 조각 예제도 제공합니다.

샘플 파이프라인의 다이어그램 보기는 다음과 같습니다. 보다시피 Azure ML Batch 실행 작업은 학습 입력을 사용하여 학습 출력(iLearner 파일)을 생성합니다. Azure ML 업데이트 리소스 작업이 이 학습 출력을 사용하여 점수 매기기 웹 서비스 엔드포인트에서 모델을 업데이트합니다. 업데이트 리소스 작업은 출력을 생성하지 않습니다. placeholderBlob는 Azure 데이터 팩터리 서비스가 파이프라인을 실행하기 위해 필요로 하는 더미 출력 데이터 세트입니다.

![파이프라인 다이어그램](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Azure Blob Storage 연결된 서비스:
Azure Storage는 다음 데이터를 보관합니다.

* 학습 데이터. Azure ML 학습 웹 서비스에 대한 입력 데이터입니다.  
* iLearner 파일. Azure ML 학습 웹 서비스에서의 출력입니다. 이 파일은 업데이트 리소스 작업에 대한 입력이기도 합니다.  

연결된 서비스의 샘플 JSON 정의는 다음과 같습니다.

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

### <a name="training-input-dataset"></a>학습 입력 데이터 세트:
다음 데이터 세트는 Azure ML 학습 웹 서비스에 대한 입력 학습 데이터를 나타냅니다. Azure ML Batch 실행 작업은 이 데이터 세트를 입력으로 사용합니다.

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

### <a name="training-output-dataset"></a>학습 출력 데이터 세트:
다음 데이터 세트는 Azure ML 학습 웹 서비스에서 출력 iLearner 파일을 나타냅니다. Azure ML Batch 실행 작업은 이 데이터 세트를 생성합니다. 이 데이터 세트는 Azure ML 업데이트 리소스 작업에 대한 입력이기도 합니다.

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

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Azure ML 학습 엔드포인트에 대한 연결된 서비스
다음 JSON 코드 조각은 학습 웹 서비스의 기본 엔드포인트를 가리키는 Azure Machine Learning에 연결된 서비스를 정의합니다.

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

**Azure ML Studio**에서 다음을 수행하여 **mlEndpoint** 및 **apiKey**에 대한 값을 가져옵니다.

1. 왼쪽 메뉴에서 **웹 서비스** 를 클릭합니다.
2. 웹 서비스 목록에서 **학습 웹 서비스** 를 클릭합니다.
3. **API 키** 텍스트 상자 옆의 복사를 클릭합니다. 클립보드의 키를 Data Factory JSON 편집기에 붙여넣습니다.
4. **Azure ML studio**에서 **배치 실행** 링크를 클릭합니다.
5. **요청** 섹션에서 **요청 URI**를 복사하여 Data Factory JSON 편집기에 붙여넣습니다.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML 업데이트 가능한 점수 매기기 엔드포인트에 대한 연결된 서비스:
다음 JSON 코드 조각은 점수 매기기 웹 서비스의 기본이 아닌 업데이트 가능한 엔드포인트를 가리키는 Azure Machine Learning에 연결된 서비스를 정의합니다.  

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

### <a name="placeholder-output-dataset"></a>자리 표시자 출력 데이터 세트:
Azure ML 업데이트 리소스 작업은 어떠한 출력도 생성하지 않습니다. 그러나 Azure Data Factory에서 파이프라인의 일정을 구동하려면 출력 데이터 세트가 필요합니다. 따라서 이 예에서는 더미/자리 표시자 데이터 세트를 사용합니다.  

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

### <a name="pipeline"></a>파이프라인
파이프라인에는 두 개의 활동인 **AzureMLBatchExecution** 및 **AzureMLUpdateResource**가 있습니다. Azure ML Batch 실행 작업은 학습 데이터를 입력으로 사용하여 .iLearner 파일을 출력으로 생성합니다. 이 작업은 입력 교육 데이터와 함께 학습 웹 서비스(웹 서비스로 노출된 학습 실험)를 호출하고 웹 서비스로부터 ilearner 파일을 수신합니다. placeholderBlob는 Azure 데이터 팩터리 서비스가 파이프라인을 실행하기 위해 필요로 하는 더미 출력 데이터 세트입니다.

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

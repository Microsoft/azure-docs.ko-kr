---
title: Azure Event Grid Machine Learning 이벤트 스키마
description: 를 사용 하 여 Machine Learning 작업 영역 이벤트에 제공 되는 속성을 설명 Azure Event Grid
services: event-grid
author: jenns
ms.service: event-grid
ms.topic: reference
ms.date: 10/18/2019
ms.author: jenns
ms.openlocfilehash: 5f2d23b3fe33691d37dc00b2d4e79036293252d9
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132882"
---
# <a name="azure-event-grid-event-schema-for-azure-machine-learning"></a>Azure Machine Learning에 대한 Azure Event Grid 이벤트 스키마

이 문서에서는 machine learning 작업 영역 이벤트의 속성과 스키마를 제공 합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

샘플 스크립트 및 자습서 목록은 [AzureML 이벤트 원본](event-sources.md#azure-machine-learning)을 참조 하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Machine Learning는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| MachineLearningServices가 등록 되었습니다. | 새 모델 또는 모델 버전이 성공적으로 등록 되 면 발생 합니다. |
| MachineLearningServices 배포 | 모델이 엔드포인트에 성공적으로 배포 되 면 발생 합니다. |
| MachineLearningServices | 실행이 성공적으로 완료 되 면 발생 합니다. |
| MachineLearningServices. DatasetDriftDetected | 데이터 집합 드리프트 모니터가 드리프트를 검색 하는 경우 발생 합니다. |

## <a name="the-contents-of-an-event-response"></a>이벤트 응답의 내용

이벤트가 트리거될 때 Event Grid 서비스는 해당 이벤트에 대한 데이터를 구독 엔드포인트으로 보냅니다.

이 섹션에는 각 이벤트에 대한 데이터가 어떻게 표시 되는지 예가 포함 되어 있습니다.

### <a name="microsoftmachinelearningservicesmodelregistered-event"></a>MachineLearningServices에 등록 된 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "models/sklearn_regression_model:20",
  "eventType": "Microsoft.MachineLearningServices.ModelRegistered",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ModelName": "sklearn_regression_model",
    "ModelVersion": 20,
    "ModelTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ModelProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesmodeldeployed-event"></a>MachineLearningServices 배포 된 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "endpoints/my-sklearn-service",
  "eventType": "Microsoft.MachineLearningServices.ModelDeployed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ServiceName": "my-sklearn-service",
    "ServiceComputeType": "ACI",
    "ModelIds": "sklearn_regression_model:1,sklearn_regression_model:2",
    "ServiceTags": {
        "area": "diabetes",
        "type": "regression"
    },
    "ServiceProperties": {
        "type": "test"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesruncompleted-event"></a>MachineLearningServices 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "experiments/0fa9dfaa-cba3-4fa7-b590-23e48548f5c1/runs/AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
  "eventType": "Microsoft.MachineLearningServices.RunCompleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "ExperimentId": "0fa9dfaa-cba3-4fa7-b590-23e48548f5c1",
    "ExperimentName": "automl-local-regression",
    "RunId": "AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5",
    "RunType": null,
    "RunTags": {},
    "RunProperties": {
        "runTemplate": "automl_child",
        "pipeline_id": "5adc0a4fe02504a586f09a4fcbb241f9a4012062",
        "pipeline_spec": "{\"objects\": [{\"class_name\": \"StandardScaler\", \"module\": \"sklearn.preprocessing\", \"param_args\": [], \"param_kwargs\": {\"with_mean\": true, \"with_std\": false}, \"prepared_kwargs\": {}, \"spec_class\": \"preproc\"}, {\"class_name\": \"LassoLars\", \"module\": \"sklearn.linear_model\", \"param_args\": [], \"param_kwargs\": {\"alpha\": 0.001, \"normalize\": true}, \"prepared_kwargs\": {}, \"spec_class\": \"sklearn\"}], \"pipeline_id\": \"5adc0a4fe02504a586f09a4fcbb241f9a4012062\"}",
        "training_percent": "100",
        "predicted_cost": "0.062226144097381045",
        "iteration": "5",
        "run_template": "automl_child",
        "run_preprocessor": "StandardScalerWrapper",
        "run_algorithm": "LassoLars",
        "conda_env_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/conda_env_v_1_0_0.yml",
        "model_name": "AutoMLad912b2d65",
        "scoring_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/scoring_file_v_1_0_0.py",
        "model_data_location": "aml://artifact/ExperimentRun/dcid.AutoML_ad912b2d-6467-4f32-a616-dbe4af6dd8fc_5/outputs/model.pkl"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftmachinelearningservicesdatasetdriftdetected-event"></a>MachineLearningServices. DatasetDriftDetected 이벤트

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearningServices/workspaces/{workspace-name}",
  "subject": "datadrifts/{}/runs/{}",
  "eventType": "Microsoft.MachineLearningServices.DatasetDriftDetected",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "DataDriftId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef",
    "DataDriftName": "myDriftMonitor",
    "RunId": "01d29aa4-e6a4-470a-9ef3-66660d21f8ef_1571590300380",
    "BaseDatasetId": "3c56d136-0f64-4657-a0e8-5162089a88a3",
    "TargetDatasetId": "d7e74d2e-c972-4266-b5fb-6c9c182d2a74",
    "DriftCoefficient": 0.83503490684792081,
    "StartTime": "2019-07-04T00:00:00+00:00",
    "EndTime": "2019-07-05T00:00:00+00:00"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체에는 각 이벤트 유형에 대한 다음 속성이 있습니다.

### <a name="microsoftmachinelearningservicesmodelregistered"></a>MachineLearningServices가 등록 되었습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ModelName | string | 등록 된 모델의 이름입니다. |
| ModelVersion | int | 등록 된 모델의 버전입니다. |
| ModelTags | object | 등록 된 모델의 태그입니다. |
| ModelProperties | object | 등록 된 모델의 속성입니다. |

### <a name="microsoftmachinelearningservicesmodeldeployed"></a>MachineLearningServices 배포

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ServiceName | string | 배포 된 서비스의 이름입니다. |
| ServiceComputeType | string | 배포 된 서비스의 계산 형식 (예: ACI, AKS)입니다. |
  | ModelIds | string | 쉼표로 구분 된 모델 Id 목록입니다. 서비스에 배포 된 모델의 Id입니다. |
| ServiceTags | object | 배포 된 서비스의 태그입니다. |
| ServiceProperties | object | 배포 된 서비스의 속성입니다. |

### <a name="microsoftmachinelearningservicesruncompleted"></a>MachineLearningServices

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| ExperimentId | string | 실행이 속한 실험의 ID입니다. |
| ExperimentName | string | 실행이 속한 실험의 이름입니다. |
| RunId | string | 완료 된 실행의 ID입니다. |
| RunType | string | 완료 된 실행의 실행 형식입니다. |
| RunTags | object | 완료 된 실행의 태그입니다. |
| RunProperties | object | 완료 된 실행의 속성입니다. |

### <a name="microsoftmachinelearningservicesdatasetdriftdetected"></a>MachineLearningServices. DatasetDriftDetected

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| DataDriftId | string | 이벤트를 트리거한 데이터 드리프트 모니터의 ID입니다. |
| DataDriftName | string | 이벤트를 트리거한 데이터 드리프트 모니터의 이름입니다. |
| RunId | string | 데이터 드리프트를 검색 한 실행의 ID입니다. |
| BaseDatasetId | string | 드리프트를 검색 하는 데 사용 되는 기본 데이터 집합의 ID입니다. |
| TargetDatasetId | string | 드리프트를 검색 하는 데 사용 되는 대상 데이터 집합의 ID입니다. |
| DriftCoefficient | double | 이벤트를 트리거한 계수 결과입니다. |
| StartTime | datetime | 드리프트 검색을 일으킨 대상 데이터 집합 시계열의 시작 시간입니다.  |
| EndTime | datetime | 드리프트 검색을 일으킨 대상 데이터 집합 시계열의 종료 시간입니다. |


## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md) 를 참조 하세요.
* Azure Machine Learning와 함께 Azure Event Grid를 사용 하는 방법에 대한 소개는 [Azure Machine Learning 이벤트](/azure/machine-learning/service/concept-event-grid-integration) 사용을 참조 하세요.
* Azure Machine Learning와 함께 Azure Event Grid를 사용 하는 예제는 [이벤트 기반 기계 학습 워크플로 만들기](/azure/machine-learning/service/how-to-use-event-grid) 를 참조 하세요.

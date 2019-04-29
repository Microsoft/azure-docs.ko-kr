---
title: 작업 영역 데이터 내보내기 또는 삭제
titleSuffix: Azure Machine Learning service
description: Azure Portal, CLI, SDK 및 인증된 REST API를 사용하여 작업 영역을 내보내거나 삭제하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 3f40606d5fae3b3784ac7f1fdcf4977b7fd9eb1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819425"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제 

Azure Machine Learning에서는 인증된 REST API를 사용하여 작업 영역 데이터를 내보내거나 삭제할 수 있습니다. 이 문서에서는 이러한 작업을 수행하는 방법에 대해 설명합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>작업 영역 데이터 제어
Azure Machine Learning Services에서 저장하는 제품 내 데이터는 Azure Portal, CLI, SDK 및 인증된 REST API를 통해 내보내고 삭제할 수 있습니다. 원격 분석 데이터는 Azure 개인 정보 보호 포털을 통해 액세스할 수 있습니다. 

Azure Machine Learning Services에서 개인 데이터는 실행 기록 문서의 사용자 정보 및 서비스와의 일부 사용자 상호 작용에 대한 원격 분석 레코드로 구성됩니다.

## <a name="delete-workspace-data-with-the-rest-api"></a>REST API를 통해 작업 영역 데이터 삭제 

데이터를 삭제하려면 HTTP DELETE 동사로 다음 API 호출을 만들 수 있습니다. 이러한 호출은 요청에 `Authorization: Bearer <arm-token>` 헤더를 포함하여 권한이 부여됩니다. 여기서 `<arm-token>`은 `https://management.core.windows.net/` 엔드포인트에 대한 AAD 액세스 토큰입니다.  

이 토큰을 가져오고 Azure 엔드포인트를 호출하는 방법을 알아보려면 [Azure REST API 설명서](https://docs.microsoft.com/rest/api/azure/)를 참조하세요.  

다음 예제에서 {}의 텍스트를 관련된 리소스를 결정하는 인스턴스 이름으로 바꿉니다.

### <a name="delete-an-entire-workspace"></a>전체 작업 영역 삭제

다음 호출을 사용하여 전체 작업 영역을 삭제합니다.  
> [!WARNING]
> 모든 정보가 삭제되고 작업 영역은 더 이상 사용할 수 없습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>모델 삭제

이 호출을 사용하여 모델 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

개별 모델은 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>자산 삭제

다음 호출을 사용하여 자산 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

개별 자산은 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>이미지 삭제

다음 호출을 사용하여 이미지 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

개별 이미지는 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>서비스 삭제

다음 호출을 사용하여 서비스 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

개별 서비스는 다음을 통해 삭제할 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>REST API를 통해 서비스 데이터 내보내기

데이터를 내보내려면 HTTP GET 동사로 다음 API 호출을 만들 수 있습니다. 이는 요청에 `Authorization: Bearer <arm-token>` 헤더를 포함하여 권한이 부여됩니다. 여기서 `<arm-token>`은 엔드포인트 `https://management.core.windows.net/`에 대한 AAD 액세스 토큰입니다.  

이 토큰을 가져오고 Azure 엔드포인트를 호출하는 방법을 알아보려면 [Azure REST API 설명서](https://docs.microsoft.com/rest/api/azure/)를 참조하세요.   

다음 예제에서 {}의 텍스트를 관련된 리소스를 결정하는 인스턴스 이름으로 바꿉니다.

### <a name="export-workspace-information"></a>작업 영역 정보 내보내기

다음 호출을 사용하여 모든 작업 영역의 목록을 가져옵니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

개별 작업 영역에 대한 정보는 다음을 통해 가져올 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>계산 정보 내보내기

작업 영역에 연결된 모든 계산 대상은 다음을 통해 가져올 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

단일 계산 대상에 대한 정보는 다음을 통해 가져올 수 있습니다.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>실행 기록 데이터 내보내기
다음 호출을 사용하여 모든 실험 및 해당 정보의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

특정 실험에 대한 모든 실행은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

실행 기록 항목은 다음을 통해 얻을 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

실험에 대한 모든 실행 메트릭은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

단일 실행 메트릭은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>아티팩트 내보내기

이 호출을 사용하여 아티팩트 및 해당 경로의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>알림 내보내기

다음 호출을 사용하여 저장된 작업의 목록을 가져옵니다.     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

단일 작업에 대한 알림은 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>데이터 저장소 내보내기

다음 호출을 사용하여 데이터 저장소의 목록을 가져옵니다.

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

개별 데이터 저장소는 다음을 통해 가져올 수 있습니다.

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>모델 내보내기

이 호출을 사용하여 모델 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

개별 모델은 다음을 통해 얻을 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>자산 내보내기

다음 호출을 사용하여 자산 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

개별 자산은 다음을 통해 가져올 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>이미지 내보내기

다음 호출을 사용하여 이미지 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

개별 이미지는 다음을 통해 가져올 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>서비스 내보내기

다음 호출을 사용하여 서비스 및 해당 ID의 목록을 가져옵니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

개별 서비스는 다음을 통해 얻을 수 있습니다.

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>파이프라인 실험 내보내기

개별 실험은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>파이프라인 그래프 내보내기

개별 그래프는 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>파이프라인 모듈 내보내기

모듈은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>파이프라인 템플릿 내보내기

템플릿은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>파이프라인 데이터 원본 내보내기

데이터 원본은 다음을 통해 가져올 수 있습니다.

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

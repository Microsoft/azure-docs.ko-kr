---
title: 논리 앱에서 ML 파이프라인 실행 트리거
titleSuffix: Azure Machine Learning
description: Azure Logic Apps를 사용 하 여 ML 파이프라인의 실행을 트리거하는 방법에 대해 알아봅니다.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 6bb976b8b310fb3eb4d0247a8d745599f688d7b5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122858"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>논리 앱에서 Machine Learning 파이프라인 실행 트리거

새 데이터가 나타날 때 Azure Machine Learning 파이프라인 실행을 트리거합니다. 예를 들어 blob 저장소 계정에 새 데이터가 나타날 때 새 모델을 학습 하는 파이프라인을 트리거할 수 있습니다. [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용 하 여 트리거를 설정 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

* 게시 된 Machine Learning 파이프라인에 대 한 REST 끝점입니다. [파이프라인을 만들고 게시](how-to-create-your-first-pipeline.md)합니다. 그런 다음 파이프라인 ID를 사용 하 여 PublishedPipeline의 REST 끝점을 찾습니다.
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* [Azure blob storage](../storage/blobs/storage-blobs-overview.md) 를 통해 데이터를 저장 합니다.
* Blob 저장소 계정에 대 한 세부 정보가 포함 된 작업 영역의 [데이터 저장소](how-to-access-data.md) 입니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

이제 [Azure 논리 앱](../logic-apps/logic-apps-overview.md) 인스턴스를 만듭니다. 원한다 면 [ISE (integration service environment)를 사용](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 하 고 논리 앱에서 사용할 수 있도록 [고객이 관리 하는 키를 설정](../logic-apps/customer-managed-keys-integration-service-environment.md) 합니다.

논리 앱이 프로 비전 되 면 다음 단계를 사용 하 여 파이프라인에 대 한 트리거를 구성 합니다.

1. [시스템 할당 관리 id를 만들어](../logic-apps/create-managed-service-identity.md) 앱이 Azure Machine Learning 작업 영역에 액세스할 수 있도록 합니다.

1. 논리 앱 디자이너 뷰로 이동 하 여 빈 논리 앱 템플릿을 선택 합니다. 
    > [!div class="mx-imgBorder"]
    > ![빈 템플릿](media/how-to-trigger-published-pipeline/blank-template.png)

1. 디자이너에서 **blob**을 검색 합니다. Blob이 **추가 되거나 수정 된 경우 (속성만)** 트리거를 선택 하 고 논리 앱에이 트리거를 추가 합니다.
    > [!div class="mx-imgBorder"]
    > 트리거를 추가 ![](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Blob 추가 또는 수정에 대해 모니터링할 Blob storage 계정에 대 한 연결 정보를 입력 합니다. 모니터링할 컨테이너를 선택 합니다. 
 
    적용 되는 업데이트에 대해 폴링하는 **간격** 및 **빈도** 를 선택 합니다.  

    > [!NOTE]
    > 이 트리거는 선택한 컨테이너를 모니터링 하지만 하위 폴더를 모니터링 하지 않습니다.

1. 새 blob 또는 수정 된 blob이 검색 될 때 실행 되는 HTTP 작업을 추가 합니다. **+ 새 단계**를 선택 하 고 HTTP 작업을 검색 하 여 선택 합니다.

  > [!div class="mx-imgBorder"]
  > HTTP 작업을 검색 ![](media/how-to-trigger-published-pipeline/search-http.png)

  다음 설정을 사용 하 여 작업을 구성 합니다.

  | 설정 | 값 | 
  |---|---|
  | HTTP 동작 | POST |
  | URI |[필수 구성 요소로](#prerequisites) 찾은 게시 된 파이프라인에 대 한 끝점입니다. |
  | 인증 모드 | 관리 ID |

1. [데이터 경로 PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) 의 값을 설정 하는 일정을 설정 합니다.

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    작업 영역에 추가한 `DataStoreName`를 [필수 구성 요소로](#prerequisites)사용 합니다.
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 설정](media/how-to-trigger-published-pipeline/http-settings.png)

1. **저장** 을 선택 하면 이제 일정이 준비 됩니다.
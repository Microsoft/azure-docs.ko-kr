---
title: ML 워크플로의 트리거 이벤트 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 이벤트 중심 애플리케이션, 프로세스 또는 CI/CD 기계 학습 워크플로를 설정합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 464d945708fba83877fe6cef9ec1b64ec444bd95
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650420"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Azure Machine Learning 이벤트 (미리 보기)를 기반으로 응용 프로그램, 프로세스 또는 CI/CD 워크플로 트리거

이 문서에서는 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)에서 특정 조건을 감지할 때 오류 알림 이메일이나 ML 파이프라인 실행 같은 Azure Machine Learning 이벤트를 기반으로 이벤트 중심 애플리케이션, 프로세스 또는 CI/CD 워크플로를 설정하는 방법을 알아봅니다.

Azure Machine Learning은 모델 학습, 모델 배포 및 모니터링을 포함하여 기계 학습 프로세스의 전체 수명 주기를 관리합니다. 최신 서버리스 아키텍처를 통해 Event Grid를 사용하여 학습 실행 완료, 모델 등록 및 배포, 데이터 드리프트 감지 등의 Azure Machine Learning 이벤트에 대응할 수 있습니다. 그런 다음, 작업 영역 내에서 실행 상태 변경, 실행 완료, 모델 등록, 모델 배포, 데이터 드리프트 감지 등의 이벤트를 구독하고 사용할 수 있습니다.

이벤트 중심 작업에 Event Grid를 사용하는 시기:
* 실행 실패 및 실행 완료 시 이메일 보내기
* 모델을 등록한 후 Azure 함수 사용
* Azure Machine Learning에서 다양한 엔드포인트로 이벤트 스트리밍
* 드리프트가 감지되면 ML 파이프라인 트리거

> [!NOTE] 
> 현재 runStatusChanged 이벤트는 실행 상태가 **실패**인 경우에만 트리거됩니다.

## <a name="prerequisites"></a>사전 요구 사항
Event Grid를 사용하려면 이벤트를 만들 Azure Machine Learning 작업 영역에 대한 기여자 또는 소유자 권한이 있어야 합니다.

## <a name="the-event-model--types"></a>이벤트 모델 및 유형

Azure Event Grid는 Azure Machine Learning이나 기타 Azure 서비스 같은 원본에서 이벤트를 읽습니다. 이후 이러한 이벤트는 Azure Event Hubs, Azure Functions, Logic Apps 등의 이벤트 처리기로 전송됩니다. 다음 다이어그램은 Event Grid가 원본과 처리기를 연결하는 방법을 보여주지만, 지원되는 통합의 전체 목록은 아닙니다.

![Azure Event Grid 기능 모델](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

이벤트 원본과 이벤트 처리기에 대한 자세한 내용은 [Event Grid란?](/azure/event-grid/overview)을 참조하세요.

### <a name="event-types-for-azure-machine-learning"></a>Azure Machine Learning의 이벤트 유형

Azure Machine Learning은 기계 학습 수명 주기의 다양한 지점에서 이벤트를 제공합니다. 

| 이벤트 유형 | Description |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 기계 학습 실험 실행이 완료될 때 발생합니다. |
| `Microsoft.MachineLearningServices.ModelRegistered` | 기계 학습 모델이 작업 영역에 등록될 때 발생합니다. |
| `Microsoft.MachineLearningServices.ModelDeployed` | 하나 이상의 모델이 포함된 유추 서비스 배포가 완료될 때 발생합니다. |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 두 데이터 세트에 대한 데이터 드리프트 감지 작업이 완료될 때 발생합니다. |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 실행 상태가 변경될 때 발생하며, 현재는 실행 상태가 '실패'일 때만 발생합니다. |

### <a name="filter--subscribe-to-events"></a>이벤트 필터링 및 구독

다음 이벤트는 Azure Event Grid를 통해 게시됩니다. 고객은 Azure Portal, PowerShell 또는 Azure CLI를 통해 [하나 이상의 이벤트 유형을 지정하고 조건을 필터링하여](/azure/event-grid/event-filtering) 이벤트를 쉽게 구독할 수 있습니다. 

이벤트를 설정할 때 특정 이벤트 데이터에 대한 트리거에만 필터를 적용할 수 있습니다. 아래 예제의 실행 상태가 변경된 이벤트를 실행 유형으로 필터링 할 수 있습니다. 이 이벤트는 조건이 충족될 때만 트리거됩니다. 필터링할 수 있는 이벤트 데이터에 대한 자세한 내용은 [Azure Machine Learning 이벤트 그리드 스키마](/azure/event-grid/event-schema-machine-learning)를 참조하세요. 

Azure Machine Learning 이벤트에 대한 구독은 RBAC(역할 기반 액세스 제어)로 보호됩니다. 작업 영역의 [기여자 또는 소유자](how-to-assign-roles.md#default-roles)만이 이벤트 구독을 만들고, 업데이트하고, 삭제할 수 있습니다.  이벤트 구독을 [만드는 동안](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 또는 나중에 이벤트 구독에 필터를 적용할 수 있습니다. 


1. Azure Portal로 이동하여 새 구독 또는 기존 구독을 선택합니다. 

1. 필터 탭을 선택하고 아래로 [고급 필터]까지 스크롤합니다. **키** 및 **값**에 필터링 기준으로 사용할 속성 형식을 입력합니다. 여기서는 실행 유형이 파이프라인 실행 또는 파이프라인 단계 실행인 경우에만 이벤트가 트리거되는 것을 볼 수 있습니다.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="이벤트 필터링":::


+ **이벤트 유형별 필터링:** 이벤트 구독은 하나 이상의 Azure Machine Learning 이벤트 유형을 지정할 수 있습니다.

+ **이벤트 제목으로 필터링:** Azure Event Grid는 __시작 문자__ 및 __끝 문자__ 일치 항목 기준의 제목 필터링을 지원하므로, 제목이 일치하는 이벤트가 구독자에게 전달됩니다. 기계 학습 이벤트의 제목 형식은 다양합니다.

  | 이벤트 유형 | 제목 형식 | 샘플 제목 |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **고급 필터링**: Azure Event Grid는 게시된 이벤트 스키마를 기반으로 하는 고급 필터링도 지원합니다. Azure Machine Learning 이벤트 스키마 세부 정보는 [Azure Machine Learning의 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-machine-learning.md)에서 찾을 수 있습니다.  다음은 사용자가 수행할 수 있는 고급 필터링 샘플입니다.

  `Microsoft.MachineLearningServices.ModelRegistered` 이벤트의 경우 모델의 태그 값을 필터링하는 방법은 다음과 같습니다.

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  필터를 적용하는 방법에 대한 자세한 내용은 [Event Grid에 대한 이벤트 필터링](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)을 참조하세요.

## <a name="consume-machine-learning-events"></a>Machine Learning 이벤트 사용

Machine Learning 이벤트를 처리하는 애플리케이션은 다음과 같은 권장 사항을 따라야 합니다.

> [!div class="checklist"]
> * 동일한 이벤트 처리기로 이벤트를 라우팅하도록 여러 구독을 구성할 수 있으므로, 이벤트가 특정 원본에서 온 것이라고 가정하지 말고 메시지의 토픽을 확인하여 예상하는 기계 학습 작업 영역에서 온 것인지 확인해야 합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * 메시지가 잘못된 순서로 오거나 조금 늦게 도착할 수도 있으므로 etag 필드를 사용하여 개체에 대한 정보가 아직 최신 상태인지 여부를 확인합니다.  또한 sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * 이해할 수 없는 필드는 무시합니다. 이 지침은 나중에 추가될 수 있는 새로운 기능에 적용하는 데도 도움이 됩니다.
> * 실패하거나 취소된 Azure Machine Learning 작업은 이벤트를 트리거하지 않습니다. 예를 들어 모델 배포가 실패하면 Microsoft.MachineLearningServices.ModelDeployed가 트리거되지 않습니다. 애플리케이션을 디자인할 때 이러한 오류 모드를 고려해야 합니다. 언제든지 Azure Machine Learning SDK, CLI 또는 포털을 사용하여 작업 상태를 확인하고 자세한 오류 원인을 파악할 수 있습니다.

Azure Event Grid를 사용하는 고객은 분리형 메시지 처리기를 만들 수 있으며, 이 처리기는 Azure Machine Learning 이벤트를 통해 트리거할 수 있습니다. 다음은 주목할 만한 메시지 처리기 예제입니다.
* Azure 기능
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory 파이프라인
* Azure 플랫폼 또는 다른 곳에 호스트할 수 있는 일반 웹후크

## <a name="set-up-in-azure-portal"></a>Azure Portal에서 설정

1. [Azure Portal](https://portal.azure.com)을 열고 Azure Machine Learning 작업 영역으로 이동합니다.

1. 왼쪽 막대에서 __이벤트__를 선택한 다음, **이벤트 구독**을 선택합니다. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 사용할 이벤트 유형을 선택합니다. 예를 들어 다음 스크린샷에서는 __등록된 모델__, __배포된 모델__, __완료된 실행__ 및 __감지된 데이터 세트 드리프트__를 선택했습니다.

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. 이벤트를 게시할 엔드포인트를 선택합니다. 다음 스크린샷에서는 __이벤트 허브__를 엔드포인트로 선택합니다.

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

선택 사항을 확인한 후 __만들기__를 클릭합니다. 구성을 마치면 이벤트가 엔드포인트로 푸시됩니다.


### <a name="set-up-with-the-cli"></a>CLI를 사용하여 설정

최신 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치해도 되고, Azure 구독의 일부로 제공되는 Azure Cloud Shell를 사용해도 됩니다.

Event Grid 확장을 설치하려면 CLI에서 다음 명령을 사용합니다.

```azurecli-interactive
az add extension --name eventgrid
```

다음 예제에서는 Azure 구독을 선택하고 Azure Machine Learning에 대한 새 이벤트 구독을 만드는 방법을 보여줍니다.

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>예

### <a name="example-send-email-alerts"></a>예제: 이메일 경고 보내기

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/)를 사용하여 모든 이벤트에 대한 이메일을 구성합니다. 조건을 사용하여 사용자 지정하고 수신자를 지정하여 함께 작업하는 팀 간에 협업과 인식을 가능하게 합니다.

1. Azure Portal에서 Azure Machine Learning 작업 영역으로 이동한 다음, 왼쪽 표시줄에서 이벤트 탭을 선택합니다. 여기서 __논리 앱__을 선택합니다. 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 논리 앱 UI에 로그인하고 토픽 유형으로 Machine Learning Service를 선택합니다. 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. 알림을 받을 이벤트를 선택합니다. 예를 들어 다음은 __RunCompleted__ 이벤트에 대한 스크린샷입니다.

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 위의 섹션에 설명된 필터링 방법을 사용할 수도 있고, 이벤트 유형 하위 세트의 논리 앱만 트리거하는 필터를 추가할 수도 있습니다. 다음 스크린샷에서는 __/datadriftID/runs/__ 의 __접두사 필터__를 사용합니다.

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 다음으로, 이 이벤트를 사용하고 이메일을 검색하는 단계를 추가합니다. 이벤트를 수신하는 데 사용할 수 있는 여러 메일 계정이 있습니다. 이메일 경고를 보낼 시기에 대한 조건을 구성할 수도 있습니다.

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. __이메일 보내기__를 선택하고 매개 변수를 입력합니다. 제목에 __이벤트 유형__ 및 __토픽__을 포함하면 이벤트를 필터링하는 데 도움이 됩니다. 또한 실행의 작업 영역 페이지에 대한 링크를 메시지 본문에 포함할 수 있습니다. 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. 이 작업을 저장하려면 페이지의 왼쪽 모서리에서 **다른 이름으로 저장**을 선택합니다. 표시되는 오른쪽 막대에서 이 작업 만들기를 확인합니다.

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>예제: 데이터 드리프트 트리거 재학습

시간이 지나면 모델의 생산성이 점점 떨어지기 마련이며, 실행되는 컨텍스트에서 더 이상 유용하지 않게 됩니다. 모델을 재학습시킬 시간이 됐는지 확인하는 방법 중 하나는 데이터 드리프트 감지입니다. 

이 예제에서는 Azure 논리 앱에서 이벤트 그리드를 사용하여 재학습을 트리거하는 방법을 보여줍니다. 이 예제에서는 모델의 학습 데이터 세트와 서비스 제공 데이터 세트 간에 데이터 드리프트가 발생할 때 Azure Data Factory 파이프라인을 트리거합니다.

시작하기 전에 다음 작업을 수행합니다.

* 작업 영역에서 [데이터 드리프트를 감지](how-to-monitor-datasets.md)하도록 데이터 세트 모니터링 설정
* 게시된 [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/) 만들기

이 예제에서는 간단한 Data Factory 파이프라인을 사용하여 파일을 BLOB 저장소에 복사하고 게시된 Machine Learning 파이프라인을 실행합니다. 이 시나리오에 대한 자세한 내용은 [Azure Data Factory에서 Machine Learning 단계를 설정하는 방법](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)을 참조하세요.

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 논리 앱 만들기부터 시작합니다. [Azure Portal](https://portal.azure.com)로 이동하여 Logic Apps를 검색하고 [만들기]를 선택합니다.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 요청된 정보를 입력합니다. 진행하기 쉽게 Azure Data Factory 파이프라인 및 Azure Machine Learning 작업 영역과 동일한 구독 및 리소스 그룹을 사용해도 됩니다.

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 논리 앱을 만든 후에는 __Event Grid 리소스 이벤트가 발생하는 경우__를 선택합니다. 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 로그인하여 이벤트의 세부 정보를 입력합니다. __리소스 이름__을 작업 영역 이름으로 설정합니다. __이벤트 유형__을 __DatasetDriftDetected__로 설정합니다.

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. 새 단계를 추가하고, __Azure Data Factory__를 검색합니다. __파이프라인 실행 만들기__를 선택합니다. 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 로그인하고, 실행할 게시된 Azure Data Factory 파이프라인을 지정합니다.

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 페이지 왼쪽 위에 있는 **저장** 단추를 사용하여 논리 앱을 저장하고 만듭니다. 앱을 보려면 [Azure Portal](https://portal.azure.com)에서 작업 영역으로 이동하여 **이벤트**를 클릭합니다.

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

이제 드리프트가 발생할 때 데이터 팩터리 파이프라인이 트리거됩니다. [새 작업 영역 포털](https://ml.azure.com)에서 데이터 드리프트 실행 및 기계 학습 파이프라인의 세부 정보를 확인합니다. 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>예제: 태그를 기준으로 모델 배포

Azure Machine Learning 모델 개체에는 배포를 피벗할 때 기준으로 사용할 수 있는 모델 이름, 버전, 태그, 속성 등의 매개 변수가 포함되어 있습니다. 모델 등록 이벤트는 엔드포인트를 트리거할 수 있으며, 사용자는 Azure 함수를 사용하여 이러한 매개 변수의 값을 기준으로 모델을 배포할 수 있습니다.

[https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 리포지토리의 예제를 참조하고 **readme** 파일의 단계를 따르세요.

## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure Machine Learning 이벤트를 사용해 보세요.

- [Event Grid 정보](../event-grid/overview.md)

- [Azure Machine Learning의 이벤트 스키마](../event-grid/event-schema-machine-learning.md)


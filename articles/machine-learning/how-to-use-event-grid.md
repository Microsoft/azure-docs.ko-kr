---
title: 이벤트 기반 기계 학습 워크플로 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 event grid를 사용 하 여 이벤트 기반 솔루션을 사용 하도록 설정 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 11/04/2019
ms.openlocfilehash: 0da5fe56bd56d360cd8052976bdde0cdc910c9a5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904273"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>이벤트 기반 기계 학습 워크플로 만들기 (미리 보기)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) 는 Azure Machine Learning 이벤트를 지원 합니다. 예를 들어 실행 완료, 모델 등록, 모델 배포 및 작업 영역으로 범위가 지정 된 데이터 드리프트 검색의 이벤트를 사용할 수 있습니다.

자세한 내용은 [Azure Machine Learning Event Grid와 통합](concept-event-grid-integration.md) 및 [Azure Machine Learning Event Grid 스키마](/azure/event-grid/event-schema-machine-learning)를 참조 하세요.

Event Grid를 사용 하 여 다음과 같은 일반적인 시나리오를 사용할 수 있습니다.

* 재 학습을 위한 파이프라인 트리거
* Azure Machine Learning에서 다양 한 엔드포인트으로 이벤트 스트리밍

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 작업 영역에 대 한 참가자 또는 소유자 액세스는 이벤트를 만들 수 있습니다.
* 웹 후크 또는 이벤트 허브와 같은 이벤트 처리기 엔드포인트을 선택 합니다. 자세한 내용은 [이벤트 처리기](https://docs.microsoft.com/azure/event-grid/event-handlers)를 참조 하세요. 

## <a name="configure-machine-learning-events-using-the-azure-portal"></a>Azure Portal를 사용 하 여 machine learning 이벤트 구성

1. [Azure Portal](https://portal.azure.com) 를 열고 Azure Machine Learning 작업 영역으로 이동 합니다.

1. 왼쪽 막대에서 __이벤트__ 를 선택한 다음 **이벤트 구독**을 선택 합니다. 

    ![select-events-in-workspace](./media/how-to-use-event-grid/select-event.png)

1. 사용할 이벤트 유형을 선택 합니다. 예를 들어 다음 스크린샷에서는 선택한 __모델을 등록__하 고, __모델을 배포__하 고, 실행을 __완료__하 고, __데이터 집합 드리프트를 검색__했습니다.

    ![-이벤트 유형 추가](./media/how-to-use-event-grid/add-event-type.png)

1. 이벤트를 게시할 엔드포인트을 선택 합니다. 다음 스크린샷에서 __이벤트 허브__ 는 선택 된 엔드포인트입니다.

    ![select-이벤트 처리기](./media/how-to-use-event-grid/select-event-handler.png)

선택 항목을 확인 한 후 __만들기__를 클릭 합니다. 구성 후 이러한 이벤트는 엔드포인트으로 푸시됩니다.

## <a name="set-up-azure-event-grid-using-cli"></a>CLI를 사용 하 여 Azure Event Grid 설정

최신 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치 하거나 Azure 구독의 일부로 제공 되는 Azure Cloud Shell를 사용할 수 있습니다.

Event Grid 확장을 설치 하려면 CLI에서 다음 명령을 사용 합니다.

```azurecli-interactive
az add extension --name eventgrid
```

다음 예제에서는 Azure 구독을 선택한 다음 Azure Machine Learning에 대 한 새 이벤트 구독을 만드는 방법을 보여 줍니다.

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="sample-scenarios"></a>샘플 시나리오

### <a name="use-a-logic-app-to-send-email-alerts"></a>논리 앱을 사용 하 여 전자 메일 알림 보내기

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 활용 하 여 모든 이벤트에 대 한 전자 메일을 구성 합니다. 조건을 사용 하 여 사용자 지정 하 고 수신자를 지정 하 여 공동 작업 하는 팀 간에 공동 작업 및 인식을 가능

1. Azure Portal에서 Azure Machine Learning 작업 영역으로 이동한 다음 왼쪽 표시줄에서 이벤트 탭을 선택 합니다. 여기에서 __논리 앱__을 선택 합니다. 

    ![선택-논리-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. 논리 앱 UI에 로그인 하 고 토픽 유형으로 Machine Learning 서비스를 선택 합니다. 

    ![선택-토픽-형식](./media/how-to-use-event-grid/select-topic-type.png)

1. 알릴 이벤트를 선택 합니다. 예를 들어 다음 스크린샷에서 __Runcompleted가 완료__되었습니다.

    ![select-이벤트-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 필터를 추가 하 여 이벤트 유형의 하위 집합에 대 한 논리 앱만 트리거할 수도 있습니다. 다음 스크린샷에서는 __/datadriftID/runs/__ 의 __접두사 필터__ 를 사용 합니다.

    ![필터-이벤트](./media/how-to-use-event-grid/filtering-events.png)

1. 그런 다음이 이벤트를 사용 하 고 전자 메일을 검색 하는 단계를 추가 합니다. 이벤트를 수신 하는 데 사용할 수 있는 여러 가지 메일 계정이 있습니다. 전자 메일 경고를 보낼 시기에 대 한 조건을 구성할 수도 있습니다.

    ![-전자 메일-동작을 선택 합니다.](./media/how-to-use-event-grid/select-email-action.png)

1. __전자 메일 보내기__ 를 선택 하 고 매개 변수를 입력 합니다. 주체에서 이벤트를 필터링 하는 데 도움이 되는 __이벤트 유형과__ __토픽__ 을 포함할 수 있습니다. 메시지 본문에서 실행할 작업 영역 페이지에 대 한 링크를 포함할 수도 있습니다. 

    ![구성-전자 메일-본문](./media/how-to-use-event-grid/configure-email-body.png)

1. 이 작업을 저장 하려면 페이지의 왼쪽 모서리에 있는 **다른 이름으로 저장** 을 선택 합니다. 표시 되는 오른쪽 막대에서이 작업 만들기를 확인 합니다.

    ![논리 확인-앱-만들기](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>논리 앱을 사용 하 여 데이터 드리프트 발생 시 재 학습 워크플로 트리거

모델은 시간이 지남에 따라 오래 지속 되며, 실행 되는 컨텍스트에서는 유용 하지 않습니다. 모델을 다시 학습 시간 인지 여부를 확인 하는 한 가지 방법은 데이터 드리프트를 검색 하는 것입니다. 

이 예제에서는 Azure 논리 앱에서 event grid를 사용 하 여 재 학습을 트리거하는 방법을 보여 줍니다. 이 예에서는 모델의 학습 및 데이터 집합 처리 사이에 데이터 드리프트가 발생할 때 Azure Data Factory 파이프라인을 트리거합니다.

시작 하기 전에 다음 작업을 수행 합니다.

* 작업 영역에서 [데이터 드리프트를 검색]( https://aka.ms/datadrift) 하도록 데이터 집합 모니터 설정
* 게시 된 [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/)을 만듭니다.

이 예제에서는 간단한 Data Factory 파이프라인을 사용 하 여 파일을 blob 저장소에 복사 하 고 게시 된 Machine Learning 파이프라인을 실행 합니다. 이 시나리오에 대 한 자세한 내용은 [Machine Learning 단계](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service) 를 설정 하는 방법을 참조 하세요 Azure Data Factory

![adf-mlpipeline-단계](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. 논리 앱을 만드는 것으로 시작 합니다. [Azure Portal](https://portal.azure.com)로 이동 하 여 Logic Apps를 검색 하 고 만들기를 선택 합니다.

    ![검색-논리-앱](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 요청 된 정보를 입력 합니다. 환경을 단순화 하려면 Azure Data Factory 파이프라인 및 Azure Machine Learning 작업 영역과 동일한 구독 및 리소스 그룹을 사용 합니다.

    ![설정-논리 앱-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. 논리 앱을 만든 후 __Event Grid 리소스 이벤트가 발생__하는 경우를 선택 합니다. 

    ![선택-이벤트 그리드-트리거](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. 로그인 하 고 이벤트에 대 한 세부 정보를 입력 합니다. __리소스 이름을__ 작업 영역 이름으로 설정 합니다. __이벤트 유형을__ __DatasetDriftDetected__로 설정 합니다.

    ![로그인 및 추가 이벤트](./media/how-to-use-event-grid/login-and-add-event.png)

1. 새 단계를 추가 하 고 __Azure Data Factory__를 검색 합니다. __파이프라인 실행 만들기__를 선택 합니다. 

    ![만들기-adfpipeline-실행](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. 로그인 하 고 실행할 게시 된 Azure Data Factory 파이프라인을 지정 합니다.

    ![-adf-파이프라인을 지정 합니다.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. 페이지 왼쪽 위에 있는 **저장** 단추를 사용 하 여 논리 앱을 저장 하 고 만듭니다. 앱을 보려면 [Azure Portal](https://portal.azure.com) 작업 영역으로 이동 하 여 **이벤트**를 클릭 합니다.

    ![표시-논리-앱-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

이제 드리프트가 발생할 때 데이터 팩터리 파이프라인이 트리거됩니다. [새 작업 영역 포털](https://ml.azure.com)에서 데이터 드리프트 실행 및 machine learning 파이프라인에 대 한 세부 정보를 확인 합니다. 

![작업 영역 보기](./media/how-to-use-event-grid/view-in-workspace.png)


### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Azure Functions를 사용 하 여 태그를 기반으로 모델 배포

Azure Machine Learning 모델 개체에는 모델 이름, 버전, 태그 및 속성과 같이 배포를 피벗할 수 있는 매개 변수가 포함 되어 있습니다. 모델 등록 이벤트는 엔드포인트을 트리거할 수 있으며 Azure 함수를 사용 하 여 해당 매개 변수의 값을 기반으로 모델을 배포할 수 있습니다.

예제는 [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) 리포지토리를 참조 하 고 **추가 정보** 파일의 단계를 따릅니다.

## <a name="next-steps"></a>다음 단계

* 사용 가능한 이벤트에 대해 자세히 알아보려면 [Azure Machine Learning 이벤트 스키마](/azure/event-grid/event-schema-machine-learning) 를 참조 하세요.

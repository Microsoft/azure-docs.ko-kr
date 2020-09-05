---
title: 비용 계획 및 관리
titleSuffix: Azure Machine Learning
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Machine Learning에 대 한 비용을 계획 하 고 관리 합니다. 기계 학습 모델을 빌드하는 경우 비용을 낮추기 위한 추가 비용 절감 팁을 알아보세요.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 3173f81f84463cde488dcbf0242f8d65c5b9c9fe
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144994"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 비용 계획 및 관리

이 문서에서는 Azure Machine Learning에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 리소스를 추가 하기 전에 Azure 가격 책정 계산기를 사용 하 여 비용을 계획할 수 있습니다. 그런 다음 Azure 리소스를 추가할 때 예상 비용을 검토 합니다. 마지막으로 관리 되는 Azure Machine Learning 계산 클러스터로 모델을 학습 하는 동안 비용 절감 팁을 사용 합니다.

Azure Machine Learning 리소스 사용을 시작한 후에는 cost management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링할 수 있습니다. 또한 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별 합니다.

Azure Machine Learning에 대 한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 다른 Azure 서비스를 사용 하는 경우 타사 서비스를 포함 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대 한 요금이 청구 됩니다. 이 문서에서는 Azure Machine Learning에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. Azure Machine Learning에 대 한 비용 관리에 익숙해지면 구독에 사용 되는 모든 Azure 서비스에 대 한 비용을 관리 하는 비슷한 방법을 적용 합니다.

Machine learning 모델을 학습 하는 경우 관리 되는 Azure Machine Learning 계산 클러스터를 사용 하 여 더 많은 비용 절감 팁을 활용 합니다.

* 자동 크기 조정을 위한 학습 클러스터 구성
* 구독 및 작업 영역에 대 한 할당량 설정
* 학습 실행에 대 한 종료 정책 설정
* 낮은 우선 순위의 가상 머신 (VM) 사용
* Azure Reserved VM Instance 사용

## <a name="prerequisites"></a>필수 구성 요소

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. 

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md)을 참조하세요.

## <a name="estimate-costs"></a>비용 예측

Azure Machine Learning 계정에서 리소스를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 비용을 예측 합니다. 왼쪽에서 **AI + Machine Learning**를 선택한 다음 **Azure Machine Learning** 를 선택 하 여 시작 합니다.  

다음 스크린샷은 계산기를 사용 하 여 비용 예측을 보여 줍니다.

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 계산기의 예상 비용":::

작업 영역에 새 리소스를 추가할 때이 계산기로 돌아가서 동일한 리소스를 추가 하 여 비용 추정치를 업데이트 합니다.

Enterprise edition이 미리 보기로 제공 되는 동안에는 ML 요금이 발생 하지 않습니다. Enterprise edition을 사용할 수 있게 되 면 학습 및 추론에 대 한 요금이 청구 됩니다.  자세한 내용은 [Azure Machine Learning 가격 책정](https://azure.microsoft.com/pricing/details/machine-learning/)을 참조 하세요.

## <a name="get-cost-alerts"></a>비용 경고 가져오기

[예산](../cost-management/tutorial-acm-create-budgets.md) 을 만들어 비용을 관리 하 고 관련자에 게 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 그러나 예산 및 경고는 더 높은 수준에서 비용을 추적 하도록 설계 되었으므로 개별 Azure 서비스 비용을 관리 하는 기능이 제한 될 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Machine Learning에서 리소스를 사용 하는 경우 비용이 발생 합니다. Azure 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 요청 단위 사용에 따라 달라 집니다. Azure Machine Learning를 시작 하는 즉시 비용이 부과 됩니다. Azure Portal의 [비용 분석](../cost-management/quick-acm-cost-analysis.md) 창에서 이러한 비용을 확인 합니다.

서로 다른 시간 간격에 대 한 그래프 및 테이블의 비용을 볼 수 있습니다. 예산 및 예상 비용에 대 한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 보기로 전환 하면 지출 추세를 식별 하 고 과도 한 지출 발생 가능성을 확인할 수 있습니다. 예산을 만든 경우 초과 하는 위치를 참조 하세요.  

Machine Learning에 대 한 별도의 서비스 영역은 표시 되지 않습니다.  대신 Machine Learning 작업 영역에 추가 된 다양 한 리소스를 볼 수 있습니다.

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning 계산 클러스터 사용 (AmlCompute)

지속적으로 변화 하는 데이터를 통해 정확한 모델을 유지 하기 위해 빠르고 효율적인 모델 학습 및 재 학습을 수행 해야 합니다. 그러나 지속적인 교육은 특히 Gpu의 심층 학습 모델에 대 한 비용으로 제공 됩니다. 

Azure Machine Learning 사용자는 AmlCompute 라고도 하는 관리 되는 Azure Machine Learning 계산 클러스터를 사용할 수 있습니다. AmlCompute는 다양 한 GPU 및 CPU 옵션을 지원 합니다. AmlCompute는 Azure Machine Learning 하 여 구독을 대신 하 여 내부적으로 호스팅됩니다. Azure IaaS 클라우드 규모에서 동일한 엔터프라이즈급 보안, 규정 준수 및 거 버 넌 스를 제공 합니다.

이러한 계산 풀이 Azure의 IaaS 인프라 내에 있으므로 인프라의 나머지 부분과 동일한 보안 및 규정 준수 요구 사항을 사용 하 여 교육을 배포, 확장 및 관리할 수 있습니다.  이러한 배포는 구독에서 발생 하 고 거 버 넌 스 규칙을 준수 합니다. [Azure Machine Learning 계산](how-to-create-attach-compute-sdk.md#amlcompute)에 대해 자세히 알아보세요.

## <a name="configure-training-clusters-for-autoscaling"></a>자동 크기 조정을 위한 학습 클러스터 구성

워크 로드 요구 사항에 따라 클러스터 크기를 자동으로 조정 하면 필요한 기능만 사용 하도록 비용을 줄일 수 있습니다.

AmlCompute 클러스터는 워크 로드에 따라 동적으로 크기를 조정 하도록 설계 되었습니다. 클러스터는 구성 하는 최대 노드 수까지 확장할 수 있습니다. 각 실행이 완료 되 면 클러스터에서 노드가 해제 되 고 구성 된 최소 노드 수로 확장 됩니다.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

규모를 축소 하기 전에 노드가 유휴 상태로 유지 되는 시간을 구성할 수도 있습니다. 기본적으로 축소 전의 유휴 시간은 120 초로 설정 됩니다.

+ 반복적 실험을 수행 하는 경우 비용을 절약 하기 위해이 시간을 줄입니다.
+ 매우 반복적인 개발/테스트 실험을 수행 하는 경우 학습 스크립트나 환경에 대 한 각 변경 후 일정 한 확장 및 축소에 대해 비용을 지불 하지 않도록 시간을 늘려야 할 수 있습니다.

Amlcompute [SDK 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) [AMLCOMPUTE CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)와 [REST api](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)를 사용 하 여 Azure Portal에서 변화 하는 워크 로드 요구 사항에 대해 amlcompute 클러스터를 구성할 수 있습니다.

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>리소스에 대 한 할당량 설정

AmlCompute는 [할당량 (또는 제한) 구성과](how-to-manage-quotas.md#azure-machine-learning-compute)함께 제공 됩니다. 이 할당량은 VM 제품군 (예: Dv2 series, NCv3 series)에 의해 다르며 각 구독에 대 한 지역별 차이가 있습니다. 구독은 작은 기본값을 사용 하 여 시작 되지만,이 설정을 사용 하 여 구독에서 분리 수 있는 Amlcompute 리소스의 양을 제어할 수 있습니다. 

또한 구독 내의 각 작업 영역에 대해 [VM 제품군으로 작업 영역 수준 할당량](how-to-manage-quotas.md#workspace-level-quota)을 구성 합니다. 이렇게 하면 각 작업 영역에서 잠재적으로 발생할 수 있는 비용을 보다 세부적으로 제어 하 고 특정 VM 제품군을 제한할 수 있습니다. 

작업 영역 수준에서 할당량을 설정 하려면 [Azure Portal](https://portal.azure.com)에서 시작 합니다.  구독에서 작업 영역을 선택 하 고 왼쪽 창에서 **사용량 + 할당량** 을 선택 합니다. 그런 다음 할당량 **구성** 탭을 선택 하 여 할당량을 확인 합니다. 할당량을 설정 하려면 구독 범위에서 권한이 필요 합니다 .이는 여러 작업 영역에 영향을 주는 설정 이기 때문입니다.

## <a name="set-run-autotermination-policies"></a>자동 종료 정책 실행 설정 

경우에 따라 학습 실행을 구성 하 여 기간을 제한 하거나 조기에 종료 해야 합니다. 예를 들어 Azure Machine Learning의 기본 제공 하이퍼 매개 변수 튜닝 또는 자동화 된 Machine Learning을 사용 하는 경우입니다.

다음은 몇 가지 옵션입니다.
* RunConfiguration에서 이라는 매개 변수를 정의 `max_run_duration_seconds` 하 여 사용자가 선택 하는 계산 (로컬 또는 원격 클라우드 계산)에서 실행이 확장 될 수 있는 최대 기간을 제어 합니다.
* 하이퍼 [매개 변수 조정](how-to-tune-hyperparameters.md#early-termination)의 경우 산적 정책, 중간 중지 정책 또는 잘림 선택 정책에서 조기 종료 정책을 정의 합니다. 하이퍼 매개 변수 스윕을 추가로 제어 하려면 또는와 같은 매개 변수를 사용 `max_total_runs` `max_duration_minutes` 합니다.
* [자동화 된 machine learning](how-to-configure-auto-train.md#exit)의 경우 플래그를 사용 하 여 비슷한 종료 정책을 설정 `enable_early_stopping` 합니다. 또한 및와 같은 속성 `iteration_timeout_minutes` `experiment_timeout_minutes` 을 사용 하 여 실행의 최대 기간 또는 전체 실험을 제어 합니다.

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 우선 순위가 낮은 Vm 사용

Azure를 사용 하면 가상 머신 확장 집합, 배치 및 Machine Learning 서비스에서 낮은 우선 순위의 Vm으로 과도 하 게 사용 되는 용량을 사용할 수 있습니다. 이러한 할당은 emptible는 하지만 전용 Vm과 비교 하 여 저렴 한 가격으로 제공 됩니다. 일반적으로 Batch 워크 로드에 우선 순위가 낮은 Vm을 사용 하는 것이 좋습니다. 다시 전송 (Batch 추론의 경우) 또는 다시 시작 (검사점을 사용한 심층 학습 교육)을 통해 중단을 복구할 수 있는 경우에도이를 사용 해야 합니다.

우선 순위가 낮은 Vm은 VM 제품군에 따라 전용 할당량 값과 별도의 단일 할당량이 있습니다. [AmlCompute 할당량에](how-to-manage-quotas.md)대해 자세히 알아보세요.

 낮은 우선 순위 Vm은 대화형 노트북 환경을 지원 해야 하기 때문에 계산 인스턴스에 대해 작동 하지 않습니다.

## <a name="use-reserved-instances"></a>예약 인스턴스 사용

계산 리소스에 대 한 비용을 절감 하는 또 다른 방법은 Azure 예약 VM 인스턴스입니다. 이 제품을 사용 하 여 1 년 또는 3 년 조건으로 커밋합니다. 이러한 할인 범위는 종 량 제 가격의 최대 72%까지 이며 월간 Azure 청구서에 직접 적용 됩니다.

Azure Machine Learning Compute는 기본적으로 예약 된 인스턴스를 지원 합니다. 1 년 또는 3 년 예약 인스턴스를 구매 하는 경우 관리 되는 Azure Machine Learning 계산에 대 한 할인이 자동으로 적용 됩니다.


## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.
* [리소스 할당량 관리 및 증가](how-to-manage-quotas.md)
* [비용 분석을 사용 하 여 비용 관리](../cost-management-billing/costs/quick-acm-cost-analysis.md)
* [SDK](how-to-create-attach-compute-sdk.md#amlcompute) 또는 [스튜디오](how-to-create-attach-compute-studio.md#amlcompute)를 사용 하 여 Azure Machine Learning 계산을 만듭니다.

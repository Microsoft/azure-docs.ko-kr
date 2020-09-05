---
title: 리소스 및 할당량 관리
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 리소스에 대한 할당량 및 추가 할당량을 요청하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: nishankgu
ms.author: nigup
ms.date: 05/08/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperfq4
ms.openlocfilehash: a9ae3d2789758d03405fb5be82181c799d1ea692
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141128"
---
# <a name="manage--increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning 리소스에 대한 할당량 관리 및 늘리기
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 [Azure Machine Learning](overview-what-is-azure-ml.md) 구독에 대 한 Azure 리소스의 미리 구성 된 제한과 관리할 수 있는 할당량에 대해 알아봅니다. 사기로 인해 예산이 초과되는 것을 방지하고 Azure 용량 제약 조건을 준수하려면 이러한 한도를 적용해야 합니다. 

다른 Azure 서비스와 마찬가지로 Azure Machine Learning과 관련하여 특정 리소스에 대한 제한이 있습니다. 이러한 한도는 [작업 영역](concept-workspace.md)의 개수 상한부터 모델 학습 또는 추론/채점에 사용되는 실제 기본 컴퓨팅의 한도까지 다양합니다. 

프로덕션 워크로드용 Azure Machine Learning 리소스를 설계 및 스케일링할 때는 이러한 한도를 고려하세요. 예를 들어 클러스터가 대상 노드 수에 도달하지 않으면 대신 구독의 Azure Machine Learning 컴퓨팅 코어 한도에 도달한 것일 수 있습니다. 기본 제한 이상으로 제한 또는 할당량을 높이려는 경우 비용 없이 온라인 고객 지원 요청을 개설합니다. Azure 용량 제약 조건으로 인해 다음 표에 표시되는 최대 한도 값 이상으로 한도를 높일 수 없습니다. 최대 한도 열이 없는 경우 리소스에 조정 가능한 한도가 없는 것입니다.


할당량 관리와 함께 [Azure Machine Learning에 대 한 비용을 계획 & 관리](concept-plan-manage-cost.md)하는 방법에 대해서도 알아볼 수 있습니다.

## <a name="special-considerations"></a>특별 고려 사항

+ 할당량은 신용 한도액일 뿐이며 용량을 보장하는 것은 아닙니다. 대규모 용량이 필요한 경우 Azure 지원에 문의하세요. [할당량을 늘릴](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)수도 있습니다.

+ 할당량은 Azure Machine Learning을 포함하여 구독의 모든 서비스에서 공유됩니다. 할당량이 공유되지 않는 경우는 컴퓨팅 코어 할당량과는 다른 별도의 할당량이 적용되는 Azure Machine Learning 컴퓨팅뿐입니다. 용량 요구 사항을 평가할 때 모든 서비스의 할당량 사용량을 계산해야 합니다.

+ 기본 한도는 평가판, 종량제 및 Dv2, F, G 등의 VM 시리즈와 같은 제품 범주 유형에 따라 다릅니다.

## <a name="default-resource-quotas"></a>기본 리소스 할당량

다음은 Azure 구독 내의 다양한 리소스 유형에 따라 할당량 한도를 분류한 것입니다.

> [!IMPORTANT]
> 한도는 변경될 수 있습니다. 언제든지 모든 Azure의 서비스 수준 할당량 [문서](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits/)에서 최신 정보를 확인할 수 있습니다.

### <a name="virtual-machines"></a>가상 머신
각 Azure 구독에 대해 서비스 또는 독립 실행형에서 가상 컴퓨터의 수에 제한이 있습니다. 가상 머신 코어에는 개별적으로 적용되는 지역별 총 한도와 지역별 크기당 시리즈(Dv2, F 등) 한도가 있습니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이러한 구독은 A1 VM 30개 또는 D1 VM 30개 또는 총 코어가 30개를 초과하지 않는 두 가지의 조합(예: A1 VM 10, D1 VM 20)을 배포하도록 허용됩니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅
[Azure Machine Learning 컴퓨팅](concept-compute-target.md#azure-machine-learning-compute-managed)의 경우 구독의 영역당 허용되는 코어 수 및 고유한 컴퓨팅 리소스 수의 기본 할당량 한도가 있습니다. 이 할당량은 위의 VM 코어 할당량과는 별개이며, AmlCompute는 Hosted-On-Behalf-Of 모델에서 리소스를 배포하는 관리형 서비스이므로 두 리소스 유형 간에 코어 제한이 공유되지 않습니다.

사용 가능한 리소스:
+ 지역별 전용 코어 수에는 구독 제품 유형에 따라 기본 제한 24~300개가 적용되고, EA 및 CSP 제품 유형에는 더 높은 기본값이 적용됩니다.  구독당 전용 코어 수는 확장 가능하며 각 VM 제품군별로 다릅니다. NCv2, NCv3 또는 ND 시리즈와 같은 특정 전문 VM 제품군은 기본값 0개 코어부터 시작됩니다. 증가 옵션을 논의하려면 할당량 요청을 제기하여 Azure 지원 팀에 문의하세요.

+ 우선 순위가 낮은 지역별 전용 코어 수에는 구독 제품 유형에 따라 기본 제한 100~3000개가 적용되고, EA 및 CSP 제품 유형에는 더 높은 기본값이 적용됩니다. 구독당 우선 순위가 낮은 코어 수는 확장 가능하며 VM 제품군별로 값이 다릅니다. 증가 옵션은 고객 지원에 문의하세요.

+ 영역별 클러스터 수의 기본 제한은 200입니다. 이러한 제한은 교육 클러스터와 컴퓨팅 인스턴스 간에 공유됩니다(컴퓨팅 인스턴스는 할당량을 위해 단일 노드 클러스터로 간주됨). 이 제한을 초과하여 늘리려면 Azure 고객 지원에 문의하세요.

+ 도달 후 초과할 수 없는 기타 엄격한 한도가 있습니다.

| **리소스** | **최대 한도** |
| --- | --- |
| 리소스 그룹당 최대 작업 영역 | 800 |
| 단일 Azure Machine Learning 컴퓨팅(AmlCompute) 리소스의 최대 노드 수 | 100개 노드 |
| 노드당 최대 GPU MPI 프로세스 | 1-4 |
| 노드당 최대 GPU 작업자 수 | 1-4 |
| 최대 작업 수명 | 21 일<sup>1</sup> |
| 우선 순위가 낮은 노드의 최대 작업 수명 | 7일<sup>2</sup> |
| 노드당 최대 매개 변수 서버 수 | 1 |

<sup>1</sup> 최대 수명은 실행이 시작되는 시간과 완료되는 시간을 나타냅니다. 완료된 실행은 무기한 저장됩니다. 최대 수명 이내에 완료되지 않은 실행에 대한 데이터는 액세스할 수 없습니다.
<sup>2</sup> 우선 순위가 낮은 노드의 작업은 용량 제약이 있을 때마다 대체될 수 있습니다. 작업에 검사점을 구현하는 것이 좋습니다.

### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning 파이프라인
[Azure Machine Learning 파이프라인](concept-ml-pipelines.md)의 경우 파이프라인의 단계 수와 구독의 지역별로 게시된 파이프라인의 일정 기반 실행 수에 할당량 한도가 적용됩니다.
- 파이프라인에서 허용되는 최대 단계 수는 30,000개입니다.
- 구독당 월별로 게시된 파이프라인의 블로그 트리거 일정에 대한 일정 기반 실행 및 Blob 끌어오기의 최대 합계는 100,000입니다.

### <a name="container-instances"></a>Container instances

특정 기간에(시간 단위로 범위 지정) 또는 전체 구독에서 작동할 수 있는 컨테이너 인스턴스의 수에도 제한이 있습니다.
제한 사항은 [Container Instances 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#container-instances-limits)을 참조 하세요.

### <a name="storage"></a>스토리지
영역당 및 특정 구독당 스토리지 계정의 수가 제한되어 있습니다. 기본 제한은 250이고 표준 및 Premium Storage 계정이 모두 포함됩니다. 지정된 지역에서 스토리지 계정이 250개 이상 필요한 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)에 요청합니다. Azure Storage 팀이 비즈니스 사례를 검토하고 지정된 지역에 대해 Storage 계정을 250개까지 승인할 수 있습니다.


## <a name="workspace-level-quota"></a>작업 영역 수준 할당량

다양한 [작업 영역](concept-workspace.md) 간에 Azure Machine Learning 컴퓨팅 대상(Amlcompute)에 대한 리소스 할당을 더 잘 관리할 수 있도록 VM 제품군별로 구독 수준 할당량을 배포하고 작업 영역 수준에서 구성할 수 있는 기능이 도입되었습니다. 기본 동작은 모든 VM 제품군의 구독 수준 할당량과 동일한 할당량을 모든 작업 영역에 적용하는 것입니다. 그러나 작업 영역 수가 늘어남에 따라 다양한 우선 순위의 워크로드가 동일한 리소스를 공유하기 시작하면 사용자는 용량을 보다 효율적으로 공유하고 리소스 경합 문제를 방지하는 방법을 원합니다. Azure Machine Learning은 사용자가 각 작업 영역에서 특정 VM 제품군에 대한 최대 할당량을 설정할 수 있게 해주는 관리형 컴퓨팅 제품이 포함된 솔루션을 제공합니다. 이는 작업 영역 간에 용량을 분산하는 것과 유사하며, 사용자가 초과 할당을 통해 사용률을 극대화하도록 선택할 수도 있습니다. 

작업 영역 수준에서 할당량을 설정하려면 구독의 작업 영역으로 이동하고 왼쪽 창에서 **사용량 + 할당량**을 클릭합니다. 그런 다음, **할당량 구성** 탭을 선택하여 할당량을 확인하고, VM 제품군을 확장하고, 해당 VM 제품군 아래에 나열된 작업 영역에 할당량 제한을 설정합니다. 음수 값 또는 구독 수준 할당량보다 높은 값은 설정할 수 없습니다. 또한 기본적으로 모든 작업 영역에는 할당량을 최대한 활용할 수 있도록 전체 구독 할당량이 설정됩니다.

[![Azure Machine Learning 작업 영역 수준 할당량](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)


> [!NOTE]
> 이는 Enterprise Edition 전용 기능입니다. 구독에 [Basic Edition과 Enterprise Edition](overview-what-is-azure-ml.md#sku) 작업 영역이 둘 다 있는 경우 이를 사용하여 엔터프라이즈 작업 영역에 대한 할당량만 설정할 수 있습니다. 기본 작업 영역에는 기본 동작인 구독 수준 할당량이 계속 적용됩니다.
>
> 작업 영역 수준에서 할당량을 설정하려면 구독 수준 권한이 있어야 합니다. 이는 개별 작업 영역 소유자가 할당량을 편집하거나 늘리지 못하게 하고, 다른 작업 영역에 별도로 설정된 리소스를 침해하지 않도록 하기 위해 적용됩니다. 따라서 작업 영역 간에 이러한 할당량을 설정하고 분산하는 데 가장 적합한 역할은 구독 관리자입니다.



## <a name="view-your-usage-and-quotas"></a>사용량 및 할당량 보기

구독에 대 한 Azure Machine Learning 계산 할당량은 다른 Azure 리소스 할당량과 별도로 관리 됩니다. 이 할당량을 보려면 Machine Learning 서비스로 드릴다운해야 합니다.  

1. 왼쪽 창에서 **Machine Learning Service**를 선택한 다음, 표시된 목록에서 모든 작업 영역을 선택합니다.

2. 다음 블레이드의 **지원 + 문제 해결 섹션**에서 **사용량 + 할당량**을 선택하여 현재 할당량 한도 및 사용량을 확인합니다.

3. 구독을 선택하여 할당량 한도를 확인합니다. 관심 있는 영역으로 필터링해야 합니다.

4. 이제 구독 수준 보기와 작업 영역 수준 보기 사이를 전환할 수 있습니다.
    + **구독 보기:** VM 제품군별로 코어 할당량 사용량을 보고, 작업 영역별로 확장하고, 실제 클러스터 이름별로 추가 확장할 수 있습니다. 이 보기를 통해 특정 VM 제품군의 코어 사용량에 대한 세부 정보를 신속하게 파악하여 작업 영역별 정보를 보고, 이러한 각 작업 영역의 기본 클러스터별 정보를 볼 수도 있습니다. 이 보기의 일반 규칙은 (사용량/할당량)입니다. 여기서 사용량은 현재 스케일 업된 코어 수이고, 할당량은 리소스를 확장할 수 있는 논리적 최대 코어 수입니다. 각 **작업 영역**의 할당량은 위에서 설명한 대로 작업 영역 수준 할당량으로, 특정 VM 제품군별로 확장할 수 있는 최대 코어 수를 나타냅니다. 마찬가지로, **클러스터**의 할당량은 실제로 클러스터를 확장할 수 있는 최대 노드 수(max_nodes 속성에 정의됨)에 해당하는 코어 수입니다.
    
    + **작업 영역 보기:** 작업 영역별로 코어 할당량 사용량을 보고, VM 제품군별로 확장하고, 실제 클러스터 이름별로 추가 확장할 수 있습니다. 이 보기는 특정 작업 영역의 코어 사용량에 대한 세부 정보를 신속하게 파악하여 VM 제품군별 정보를 보고, 이러한 각 작업 영역의 기본 클러스터별 정보를 볼 수도 있습니다.

Virtual Machines, Storage, Network 등의 다양한 다른 Azure 리소스에 대한 할당량을 쉽게 확인하는 방법은 Azure Portal입니다.

1. 왼쪽 창에서 **모든 서비스**를 선택한 다음, 일반 범주에서 **구독**을 선택합니다.

2. 구독 목록에서 할당량을 보려는 구독을 선택합니다.

3. **사용량 + 할당량** 을 선택 하 여 현재 할당량 한도 및 사용량을 확인 합니다. 필터를 사용 하 여 공급자 및 위치를 선택 합니다. 

## <a name="request-quota-increases"></a>할당량 증가 요청

기본 제한 이상으로 제한 또는 할당량을 높이려는 경우 비용 없이 [온라인 고객 지원 요청을 개설](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)합니다.

다음 표에 나오는 최대 한도 값 이상으로 한도를 높일 수 없습니다. 최대 제한이 없는 경우 리소스에 조정 가능한 제한이 없는 것입니다. [할당량을 늘리는 방법에 대한 단계별 지침을 참조하세요](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

할당량 증가를 요청할 때 할당량을 늘리려는 서비스를 선택해야 하며 이러한 서비스로는 Machine Learning Service 할당량, 컨테이너 인스턴스 또는 Storage 할당량이 있습니다. 또한 Azure Machine Learning 컴퓨팅의 경우 할당량을 보는 동안 위의 단계에 따라 **할당량 요청** 단추를 누르기만 하면 됩니다.

> [!NOTE]
> [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)은 제한하거나 할당량을 증가할 수 없습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 평가판을 종량제로 업그레이드](../billing/billing-upgrade-azure-subscription.md) 및 [평가판 구독 FAQ](https://azure.microsoft.com/free/free-account-faq)를 참조하세요.

## <a name="next-steps"></a>다음 단계

+ [Azure Machine Learning의 비용 계획 및 관리](concept-plan-manage-cost.md)

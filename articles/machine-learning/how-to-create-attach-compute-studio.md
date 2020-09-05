---
title: Studio에서 계산 리소스 만들기
titleSuffix: Azure Machine Learning
description: Studio를 사용 하 여 기계 학습을 위한 교육 및 배포 계산 리소스 (계산 대상) 만들기
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 2e32eb9a2a13b8e247388e8da80dd5f5967fdc6d
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147481"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Azure Machine Learning studio에서 모델 학습 및 배포를 위한 계산 대상 만들기
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine studio에서 계산 대상을 만들고 관리 하는 방법에 대해 알아봅니다.  다음을 사용 하 여 계산 대상을 만들고 관리할 수도 있습니다.

* [Azure Machine Learning LEARNING SDK](how-to-create-attach-compute-sdk.md), 
* Azure Machine Learning에 대 한 [CLI 확장](reference-azure-machine-learning-cli.md#resource-management)
* Azure Machine Learning [VS Code 확장](how-to-manage-resources-vscode.md#compute-clusters) 입니다.


## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>계산 대상 이란? 

Azure Machine Learning을 사용하여 다양한 리소스 또는 환경(총체적으로 [__컴퓨팅 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 함)에서 모델을 학습시킬 수 있습니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning 컴퓨팅, Azure HDInsight 또는 원격 가상 머신)입니다.  ["모델 배포 위치 및 방법"](how-to-deploy-and-where.md)의 설명에 따라 모델 배포용 컴퓨팅 대상을 만들 수도 있습니다.

## <a name="view-compute-targets"></a><a id="portal-view"></a>컴퓨팅 대상 보기

작업 영역에 대 한 모든 계산 대상을 보려면 다음 단계를 사용 합니다.

1. [Azure Machine Learning 스튜디오](https://ml.azure.com)로 이동합니다.
 
1. __관리__아래에서 __Compute__를 선택 합니다.

1. 위쪽에서 탭을 선택 하 여 각 계산 대상 유형을 표시 합니다.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="계산 대상 목록 보기":::

## <a name="create-compute-target"></a><a id="portal-create"></a>컴퓨팅 대상 만들기

컴퓨팅 대상의 목록을 보려면 이전 단계를 수행합니다. 그런 다음, 다음과 같은 단계를 사용하여 컴퓨팅 대상을 만듭니다.

1. 만들 계산 유형에 해당 하는 맨 위의 탭을 선택 합니다.

1. 계산 대상이 없으면 페이지 중간에서  **만들기** 를 선택 합니다.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="컴퓨팅 대상 만들기":::

1. 계산 리소스 목록이 표시 되 면 목록 위에서 **+ 새로 만들기** 를 선택 합니다.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="새로 만들기 선택":::


1. 계산 형식에 대 한 양식을 작성 합니다.

  * [계산 인스턴스](#compute-instance)
  * [컴퓨팅 클러스터](#amlcompute)
  * [유추 클러스터](#inference-clusters)
  * [연결 된 계산](#attached-compute)

1. __만들기__를 선택합니다.

1. 목록에서 컴퓨팅 대상을 선택하여 만들기 작업의 상태를 봅니다.

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="목록에서 계산 상태 보기":::


### <a name="compute-instance"></a>컴퓨팅 인스턴스

[위의 단계](#portal-create) 를 사용 하 여 계산 인스턴스를 만듭니다.  그런 다음 양식을 다음과 같이 입력 합니다.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="새 계산 인스턴스 만들기":::


|필드  |Description  |
|---------|---------|
|컴퓨팅 이름     |  <li>이름은 필수 이며 길이가 3 ~ 007e; 24 자 사이 여야 합니다.</li><li>유효한 문자는 대 문자와 소문자, 숫자 및  **-** 문자입니다.</li><li>이름은 문자로 시작 해야 합니다.</li><li>이름은 Azure 지역 내의 모든 기존 계산에서 고유 해야 합니다. 선택한 이름이 고유 하지 않으면 경고가 표시 됩니다.</li><li>문자를 사용 하는 경우 **-**  이름 뒤에 하나 이상의 문자가와 야 합니다.</li>     |
|가상 머신 유형 |  CPU 또는 GPU를 선택 합니다. 이 형식을 만든 후에는 변경할 수 없습니다.     |
|가상 머신 크기     |  지원 되는 가상 머신 크기는 해당 지역에서 제한 될 수 있습니다. [가용성 목록](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 확인     |
|SSH 액세스 사용/사용 안 함     |   SSH 액세스는 기본적으로 사용 되지 않습니다.  SSH 액세스는 일 수 없습니다. 만든 후 변경 됩니다. [VS Code 원격](how-to-set-up-vs-code-remote.md) 으로 대화형으로 디버깅 하려는 경우 액세스를 사용 하도록 설정 해야 합니다.   |
|고급 설정     |  선택 사항입니다. 가상 네트워크를 구성 합니다. **리소스 그룹**, **가상 네트워크**및 **서브넷** 을 지정 하 여 Azure Virtual Network (vnet) 내에서 계산 인스턴스를 만듭니다. 자세한 내용은 vnet에 대 한 다음 [네트워크 요구 사항](how-to-enable-virtual-network.md#compute-instance) 을 참조 하세요.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> 계산 클러스터

학습, batch 추론 또는 보충 learning 워크 로드에 대 한 단일 또는 다중 노드 계산 클러스터를 만듭니다. [위의 단계](#portal-create) 를 사용 하 여 계산 클러스터를 만듭니다.  그런 다음 양식을 다음과 같이 입력 합니다.


|필드  |Description  |
|---------|---------|
|컴퓨팅 이름     |  <li>이름은 필수 이며 길이가 3 ~ 007e; 24 자 사이 여야 합니다.</li><li>유효한 문자는 대 문자와 소문자, 숫자 및  **-** 문자입니다.</li><li>이름은 문자로 시작 해야 합니다.</li><li>이름은 Azure 지역 내의 모든 기존 계산에서 고유 해야 합니다. 선택한 이름이 고유 하지 않으면 경고가 표시 됩니다.</li><li>문자를 사용 하는 경우 **-**  이름 뒤에 하나 이상의 문자가와 야 합니다.</li>     |
|가상 머신 유형 |  CPU 또는 GPU를 선택 합니다. 이 형식을 만든 후에는 변경할 수 없습니다.     |
|가상 컴퓨터 우선 순위 | **전용** 또는 **낮은 우선 순위**를 선택 합니다.  낮은 우선 순위의 가상 머신은 저렴 하지만 계산 노드를 보장 하지 않습니다. 작업을 선점할 수 있습니다.
|가상 머신 크기     |  지원 되는 가상 머신 크기는 해당 지역에서 제한 될 수 있습니다. [가용성 목록](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 확인     |
|최소 노드 개수 | 프로 비전 하려는 최소 노드 수입니다. 전용 노드 수를 원하는 경우 여기에서 해당 개수를 설정 합니다. 최소값을 0으로 설정 하 여 비용을 절약 하 여 클러스터가 유휴 상태일 때 노드에 대해 지불 하지 않습니다. |
|최대 노드 수 | 프로 비전 하려는 최대 노드 수입니다. 작업이 제출 되 면 계산은이 노드 수의 최대값으로 자동 조정 됩니다. |
|고급 설정     |  선택 사항입니다. 가상 네트워크를 구성 합니다. **리소스 그룹**, **가상 네트워크**및 **서브넷** 을 지정 하 여 Azure Virtual Network (vnet) 내에서 계산 인스턴스를 만듭니다. 자세한 내용은 vnet에 대 한 다음 [네트워크 요구 사항](how-to-enable-virtual-network.md#compute-instance) 을 참조 하세요.   또한 [관리 id](#managed-identity) 를 연결 하 여 리소스에 대 한 액세스 권한 부여     |

#### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 관리 id 설정

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

클러스터를 만드는 동안 또는 계산 클러스터 세부 정보를 편집 하는 동안 **고급 설정**에서 **관리 id 할당** 을 설정/해제 하 고 시스템 할당 id 또는 사용자 할당 id를 지정 합니다.

#### <a name="managed-identity-usage"></a>관리 id 사용

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>유추 클러스터

대규모 추론에 대 한 AKS (Azure Kubernetes Service) 클러스터를 만들거나 연결 합니다. [위의 단계](#portal-create) 를 사용 하 여 AKS 클러스터를 만듭니다.  그런 다음 양식을 다음과 같이 입력 합니다.


|필드  |Description  |
|---------|---------|
|컴퓨팅 이름     |  <li>이름은 필수입니다. 이름은 2 ~ 007e; 16 자 사이 여야 합니다. </li><li>유효한 문자는 대 문자와 소문자, 숫자 및  **-** 문자입니다.</li><li>이름은 문자로 시작 해야 합니다.</li><li>이름은 Azure 지역 내의 모든 기존 계산에서 고유 해야 합니다. 선택한 이름이 고유 하지 않으면 경고가 표시 됩니다.</li><li>문자를 사용 하는 경우 **-**  이름 뒤에 하나 이상의 문자가와 야 합니다.</li>     |
|Kubernetes Service | **새로 만들기** 를 선택 하 고 폼의 나머지 부분을 채웁니다.  또는 **기존 사용** 을 선택한 다음 구독에서 기존 AKS 클러스터를 선택 합니다.
|지역 |  클러스터가 생성 될 지역 선택 |
|가상 머신 크기     |  지원 되는 가상 머신 크기는 해당 지역에서 제한 될 수 있습니다. [가용성 목록](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 확인     |
|클러스터 용도  | **프로덕션** 또는 **개발-테스트** 선택 |
|노드 수 | 가상 컴퓨터의 코어 수 (vCPUs)를 곱한 노드 수는 12 보다 크거나 같아야 합니다. |
| 네트워크 구성 | **고급** 을 선택 하 여 기존 가상 네트워크 내에서 계산을 만듭니다. 가상 네트워크의 AKS에 대 한 자세한 내용은 [개인 끝점 및 가상 네트워크를 사용 하 여 학습 및 유추 중 네트워크 격리](how-to-enable-virtual-network.md#aksvnet)를 참조 하세요. |
| SSL 구성 사용 | 이를 사용 하 여 계산에서 SSL 인증서를 구성 합니다. |

### <a name="attached-compute"></a>연결 된 계산

Azure Machine Learning 작업 영역 외부에서 만든 컴퓨팅 대상을 사용하려면 연결해야 합니다. 컴퓨팅 대상을 연결하면 작업 영역에서 사용할 수 있습니다.  **연결 된 계산** 을 사용 하 여 **학습**을 위한 계산 대상을 연결 합니다.  **유추 클러스터** 를 사용 하 여 **추론**에 대 한 AKS 클러스터를 연결 합니다.

[위의 단계](#portal-create) 를 사용 하 여 계산을 연결 합니다.  그런 다음 양식을 다음과 같이 입력 합니다.

1. 컴퓨팅 대상의 이름을 입력합니다. 
1. 연결할 계산 유형을 선택 하세요. Azure Machine Learning 스튜디오에서 모든 컴퓨팅 유형을 연결할 수 있는 것은 아닙니다. 현재 학습용으로 연결할 수 있는 컴퓨팅 유형은 다음과 같습니다.
    * 원격 VM
    * Azure Databricks(기계 학습 파이프라인에 사용)
    * Azure Data Lake Analytics(기계 학습 파이프라인에 사용)
    * Azure HDInsight

1. 양식을 입력하고 필요한 속성에 대한 값을 입력합니다.

    > [!NOTE]
    > Microsoft에서는 암호보다 더 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격에 취약합니다. SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __연결__을 선택합니다. 


## <a name="next-steps"></a>다음 단계

대상이 만들어지고 작업 영역에 연결 된 후에는 개체를 사용 하 여 [실행 구성](how-to-set-up-training-targets.md) 에서 사용 됩니다 `ComputeTarget` .

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* 계산 리소스를 사용 하 여 [학습 실행을 제출](how-to-set-up-training-targets.md)합니다.
* [자습서: 모델 학습](tutorial-train-models-with-aml.md) 은 관리 되는 계산 대상을 사용 하 여 모델을 학습 합니다.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](how-to-enable-virtual-network.md)

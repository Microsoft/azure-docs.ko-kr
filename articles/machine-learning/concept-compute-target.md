---
title: 계산 대상 이란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 모델을 학습 하거나 배포할 위치를 정의 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ec2d9152bf8d3d7c60f00e902f155212ee1b81cc
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169825"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Azure Machine Learning에서 계산 대상 이란? 

**계산 대상은** 교육 스크립트를 실행 하거나 서비스 배포를 호스트 하는 지정 된 계산 리소스/환경입니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 일 수 있습니다. 계산 대상을 사용 하면 나중에 코드를 변경할 필요 없이 계산 환경을 쉽게 변경할 수 있습니다.  

일반적인 모델 개발 수명 주기에서 다음과 같은 작업을 할 수 있습니다.
1. 먼저 적은 양의 데이터를 개발 하 고 시험해 볼 것입니다. 이 단계에서는 로컬 환경 (로컬 컴퓨터 또는 클라우드 기반 VM)을 계산 대상으로 사용 하는 것이 좋습니다. 
2. 큰 데이터를 확장 하거나 이러한 [학습 계산 대상](#train)중 하나를 사용 하 여 분산 교육을 수행 하세요.  
3. 모델이 준비 되 면 다음 [배포 계산 대상](#deploy)중 하나를 사용 하 여 웹 호스팅 환경 또는 IoT 장치에 배포 합니다.

계산 대상에 사용 하는 계산 리소스는 [작업 영역](concept-workspace.md)에 연결 됩니다. 로컬 컴퓨터 이외의 계산 리소스는 작업 영역의 사용자가 공유 합니다.

## <a name="train"></a>계산 대상 학습

Azure Machine Learning는 여러 계산 리소스에 대해 다양 한 지원을 제공 합니다.  다양 한 시나리오에 대 한 지원이 다를 수 있지만 고유한 계산 리소스를 연결할 수도 있습니다.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

[모델 학습을 위한 계산 대상 설정 및 사용](how-to-set-up-training-targets.md)에 대해 자세히 알아보세요.

## <a name="deploy"></a>배포 대상

다음 계산 리소스를 사용 하 여 모델 배포를 호스트할 수 있습니다.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

[계산 대상에 모델을 배포 하는 위치 및 방법](how-to-deploy-and-where.md)에 대해 알아봅니다.

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning compute (관리)

Azure Machine Learning에서 관리 되는 계산 리소스를 만들고 관리 합니다. 이 계산은 machine learning 워크 로드에 최적화 되어 있습니다. 계산 클러스터와 [계산 인스턴스](concept-compute-instance.md) 는 유일 하 게 관리 되는 계산 Azure Machine Learning. 추가 관리 되는 계산 리소스를 나중에 추가할 수 있습니다.

에서 계산 인스턴스 (미리 보기) 또는 계산 클러스터 Azure Machine Learning 만들 수 있습니다.

| | Azure Machine Learning Studio | Azure portal | SDK) | Resource Manager 템플릿 | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| 컴퓨팅 인스턴스 | 예 | 예 | 예 | 예 |  |
| 계산 클러스터 | 예 | 예 | 예 | 예 | 예 |

이러한 계산 리소스는 생성 될 때 다른 종류의 계산 대상과 달리 자동으로 작업 영역에 포함 됩니다.

### <a name="compute-clusters"></a>컴퓨팅 클러스터

학습 및 batch 추론 (미리 보기)에 대 한 계산 클러스터 Azure Machine Learning 사용할 수 있습니다.  이 계산 리소스를 사용 하 여 다음을 수행 합니다.

* 단일 또는 다중 노드 클러스터
* 실행을 제출할 때마다 자동으로 크기 조정 
* 자동 클러스터 관리 및 작업 예약 
* CPU와 GPU 리소스에 대한 지원



## <a name="unmanaged-compute"></a>관리 되지 않는 계산

관리 되지 않는 계산 대상이 Azure Machine Learning에서 관리 *되지* 않습니다. Azure Machine Learning 외부에서이 유형의 계산 대상을 만든 다음 작업 영역에 연결 합니다. 관리 되지 않는 계산 리소스를 유지 관리 하거나 machine learning 작업의 성능을 향상 시키기 위해 추가 단계가 필요할 수 있습니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:
* [모델 학습을 위한 계산 대상 설정](how-to-set-up-training-targets.md)
* [계산 대상에 모델 배포](how-to-deploy-and-where.md)
---
title: 분산 교육 이란?
titleSuffix: Azure Machine Learning
description: 분산 학습 및 Azure Machine Learning 지 원하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 8c2867fc5d57cf9ad6eb6bf2d90e02c61e320120
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146660"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure Machine Learning를 사용한 분산 학습

이 문서에서는 분산 학습 및 심층 학습 모델에 대해 Azure Machine Learning 지 원하는 방법에 대해 알아봅니다. 

분산 학습에서 모델을 학습 하는 작업은 작업자 노드 라는 여러 미니 프로세서 간에 분할 및 공유 됩니다. 이러한 작업자 노드는 동시에 작동 하 여 모델 학습 속도를 향상 합니다. 분산 교육은 기존 ML 모델에 사용할 수 있지만 심층 신경망을 학습 하는 [심층 학습](concept-deep-learning-vs-machine-learning.md) 처럼 계산 및 시간 집약적 작업에 더 적합 합니다. 

## <a name="deep-learning-and-distributed-training"></a>심층 학습 및 분산 교육 

분산 학습에는 [데이터 병렬 처리](#data-parallelism) 와 [모델 병렬 처리](#model-parallelism)라는 두 가지 주요 유형이 있습니다. 심층 학습 모델에 대 한 분산 교육의 경우 [Python의 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 는 인기 있는 프레임 워크, PyTorch 및 TensorFlow와의 통합을 지원 합니다. 두 프레임 워크는 분산 학습에 데이터 병렬 처리를 사용 하 고 계산 속도를 최적화 하기 위해 [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) 를 활용할 수 있습니다. 

* [PyTorch를 사용 하 여 분산 교육](how-to-train-pytorch.md#distributed-training)

* [TensorFlow를 사용 하 여 분산 교육](how-to-train-tensorflow.md#distributed-training)

분산 된 교육이 필요 하지 않은 ML 모델의 경우 Python SDK를 사용 하 여 모델을 학습 하는 다양 한 방법에 대해 [Azure Machine Learning를 사용 하 여 모델 학습](concept-train-machine-learning-model.md#python-sdk) 을 참조 하세요.

## <a name="data-parallelism"></a>데이터 병렬 처리

데이터 병렬 처리는 두 개의 분산 된 학습 방법을 구현 하는 가장 쉬운 방법 이며 대부분의 사용 사례에서 충분 합니다.

이 접근 방식에서 데이터는 파티션으로 나뉩니다. 여기서 파티션 수는 계산 클러스터에서 사용 가능한 총 노드 수와 동일 합니다. 모델은 이러한 각 작업자 노드에 복사 되 고 각 작업자는 해당 데이터의 하위 집합에 대해 작동 합니다. 각 노드에는 학습 중인 모델을 지원할 수 있는 용량이 있어야 합니다. 즉, 모델은 전적으로 각 노드에 맞아야 합니다. 다음 다이어그램은이 방법의 시각적 데모를 제공 합니다.

![데이터-병렬 처리-개념-다이어그램](./media/concept-distributed-training/distributed-training.svg)

각 노드는 학습 샘플과 레이블이 지정 된 출력에 대 한 예측 간의 오류를 독립적으로 계산 합니다. 그러면 각 노드가 오류를 기반으로 모델을 업데이트 하 고 모든 변경 내용을 다른 노드에 전달 하 여 해당 모델을 업데이트 해야 합니다. 즉, 작업자 노드는 일괄 처리 계산의 끝에서 모델 매개 변수 또는 그라데이션을 동기화 하 여 일관 된 모델을 학습 하 고 있는지 확인 해야 합니다. 

## <a name="model-parallelism"></a>모델 병렬 처리

네트워크 병렬 처리 라고도 하는 모델 병렬 처리에서 모델은 서로 다른 노드에서 동시에 실행할 수 있는 여러 부분으로 분할 되며 각 부분은 동일한 데이터에서 실행 됩니다. 이 메서드의 확장성은 알고리즘의 작업 병렬 처리 수준에 따라 달라 지 며 데이터 병렬 처리 보다 구현 하기가 더 복잡 합니다. 

모델 병렬 처리에서 작업자 노드는 일반적으로 각 전달 또는 이후 전파 단계에 대해 공유 매개 변수를 한 번만 동기화 해야 합니다. 또한 각 노드가 동일한 학습 데이터의 모델 하위 섹션에서 작동 하므로 더 큰 모델은 문제가 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* Python SDK를 사용 하 여 [모델 학습에 계산 대상을 사용](how-to-set-up-training-targets.md) 하는 방법을 알아봅니다.
* 기술 예제를 보려면 [참조 아키텍처 시나리오](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning)를 참조 하십시오.
* [TensorFlow를 사용 하 여 ML 모델을 학습](how-to-train-tensorflow.md)합니다.
* [PyTorch를 사용 하 여 ML 모델을 학습](how-to-train-pytorch.md)합니다. 
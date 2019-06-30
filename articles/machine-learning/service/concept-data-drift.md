---
title: 데이터 드리프트를 모니터링 (미리 보기)
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 데이터 드리프트를 모니터링할 수 있습니다 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 56761c32484d4f5b27800e56143c62d3731da852
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332661"
---
# <a name="what-is-data-drift-monitoring-preview"></a>모니터링 (미리 보기)를 드리프트 데이터 란?

데이터 드리프트는 데이터 분포의 변경 내용을 확인 합니다. Machine learning의 컨텍스트에서 학습 된 기계 학습 모델 드리프트 인해 성능이 저하 된 예측 성능을 발생할 수 있습니다. 학습 데이터와 예측에 사용 되는 데이터 간의 드리프트를 모니터링 성능 문제를 감지 하는 데 도움이 됩니다.

기계 학습 모델은 학습 하는 데 데이터로 훌륭합니다. 성능 모니터링 없이 프로덕션으로 모델을 배포 감지 및 나쁜 영향을 발생할 수 있습니다. 드리프트 모니터링 데이터를 검색할 수 있으며 데이터 드리프트를 적용할 수 있습니다. 

## <a name="when-to-monitor-for-data-drift"></a>데이터 드리프트를 모니터링 하는 경우

메트릭을 모니터링할 수 있습니다 것 같습니다.

+ 드리프트 (드리프트 계수)의 크기
+ 드리프트 (기능별 드리프트 기여)의 원인
+ 거리 메트릭을 (Wasserstein, 에너지, 등.)

이 모니터링을 사용할 경우 현재 위치에서 경고 또는 작업 설정할 수 있습니다 드리프트가 검색 하 고 데이터 과학자는 문제의 근본 원인을 조사할 수 있습니다. 

배포 된 모델에 대 한 입력된 데이터가 변경 될 수 있음을 있다면 데이터 드리프트 검색을 사용 하는 것이 좋습니다.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Azure Machine Learning 서비스에서 데이터 드리프트가 모니터링 하는 방법

사용 하 여 **Azure Machine Learning 서비스**, 데이터 드리프트 데이터 집합 또는 배포를 통해 모니터링 됩니다. 데이터 드리프트를 모니터링 하려면 기준 데이터 집합-일반적으로 학습 데이터 집합을 모델에 대 한 지정 됩니다. 기준 데이터 집합에 대해 두 번째 데이터 집합-배포-에서 모델 입력된 데이터가 수집 되는 일반적으로 테스트 합니다. 두 데이터 집합은 [프로 파일링](how-to-create-dataset-snapshots.md) 및 데이터에 대 한 입력 드리프트 모니터링 서비스입니다. 두 데이터 집합 간의 차이점을 검색 하는 기계 학습 모델을 학습 합니다. 모델의 성능은 두 데이터 집합 간의 드리프트의 크기를 측정 하는 드리프트 계수로 변환 됩니다. 사용 하 여 [interpretability 모델](machine-learning-interpretability-explainability.md) 드리프트 계수에 영향을 주는 기능 계산 됩니다. 데이터 집합 프로필에서 각 기능에 대 한 통계 정보를 추적 합니다. 

## <a name="data-drift-metric-output"></a>데이터 드리프트 메트릭 출력

여러 가지 방법으로 드리프트 메트릭을 볼 수 있습니다.

* Jupyter 위젯을 사용 합니다.
* 사용 된 `get_metrics()` 함수에서 `datadriftRun` 개체입니다.
* 모델에서 Azure portal에서 메트릭 보기

다음 메트릭은 데이터 드리프트 태스크에 대 한 실행된 각 반복에 저장 됩니다.

|메트릭|설명|
--|--|
wasserstein_distance|1 차원 숫자 배포에 대해 정의 된 통계 거리입니다.|
energy_distance|1 차원 숫자 배포에 대해 정의 된 통계 거리입니다.|
datadrift_coefficient|공식적으로 Matthews 상관 계수를-1에서 1 사이의 실수입니다. 드리프트의 컨텍스트에서 0 없습니다 드리프트 나타내고 1은 최대 드리프트 합니다.|
datadrift_contribution|기능 중요도 드리프트에 영향을 주는 기능입니다.|

## <a name="next-steps"></a>다음 단계

예제를 참조 하 고 데이터 드리프트를 모니터링 하는 방법을 알아봅니다.

+ [Azure Kubernetes Service (AKS)를 통해 배포 된 모델에서 데이터 드리프트를 모니터링 하는 방법에 알아봅니다.](how-to-monitor-data-drift.md)
+ 사용해 [Jupyter 노트북 샘플](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)
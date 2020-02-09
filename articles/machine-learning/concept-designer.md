---
title: 디자이너를 사용 하 여 ML 모델 빌드
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 디자이너를 구성 하는 용어, 개념 및 워크플로에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 8208bbf4d196091a6fe4cd962ddc7373d303e125
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312433"
---
# <a name="what-is-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너 란? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning designer를 사용 하 여 대화형 캔버스의 [데이터 집합](#datasets) 및 [모듈](#module) 을 시각적으로 연결 하 여 기계 학습 모델을 만들 수 있습니다. 디자이너를 시작 하는 방법을 알아보려면 [자습서: 디자이너를 사용 하 여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md) 을 참조 하세요.

![Azure Machine Learning 디자이너 예](./media/concept-designer/designer-drag-and-drop.gif)

디자이너는 Azure Machine Learning [작업 영역](concept-workspace.md) 을 사용 하 여 다음과 같은 공유 리소스를 구성 합니다.

+ [파이프라인](#pipeline)
+ [데이터 세트](#datasets)
+ [계산 리소스](#compute)
+ [등록 된 모델](concept-azure-machine-learning-architecture.md#models)
+ [게시 된 파이프라인](#publish)
+ [실시간 엔드포인트](#deploy)

## <a name="model-training-and-deployment"></a>모델 학습 및 배포

디자이너는 기계 학습 모델을 빌드, 테스트 및 배포 하기 위한 시각적 캔버스를 제공 합니다. 디자이너를 사용 하 여 다음을 수행할 수 있습니다.

+ [데이터 집합](#datasets) 및 [모듈](#module) 을 캔버스로 끌어서 놓습니다.
+ 모듈을 함께 연결 하 여 [파이프라인 초안](#pipeline-draft)을 만듭니다.
+ Azure Machine Learning 작업 영역에서 계산 리소스를 사용 하 여 [파이프라인 실행](#pipeline-run) 을 제출 합니다.
+ **학습 파이프라인** 을 **유추 파이프라인**으로 변환 합니다.
+ 다른 매개 변수 및 데이터 집합을 사용 하 여 새 파이프라인 실행을 제출 하려면 REST **파이프라인 엔드포인트** 에 파이프라인을 [게시](#publish) 합니다.
    + 단일 파이프라인을 다시 사용 하 여 매개 변수 및 데이터 집합을 변경 하는 동안 여러 모델을 학습 하는 **교육 파이프라인** 을 게시 합니다.
    + 이전에 학습 된 모델을 사용 하 여 새 데이터에 대 한 예측을 만들려면 **일괄 처리 유추 파이프라인** 을 게시 합니다.
+ 실시간 **유추 파이프라인** 을 실시간 엔드포인트에 [배포](#deploy) 하 여 새 데이터에 대 한 예측을 실시간으로 만듭니다.

![디자이너의 학습, 일괄 처리 유추 및 실시간 유추를 위한 워크플로 다이어그램](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>파이프라인

[파이프라인](concept-azure-machine-learning-architecture.md#ml-pipelines) 은 함께 연결 하는 데이터 집합 및 분석 모듈로 구성 됩니다. 파이프라인은 여러 가지 용도로 사용 됩니다. 단일 모델을 학습 하는 파이프라인이 나 여러 모델을 학습 하는 파이프라인을 만들 수 있습니다. 실시간 또는 일괄 처리로 예측을 수행 하는 파이프라인을 만들거나 데이터를 정리 하는 파이프라인을 만들 수 있습니다. 파이프라인을 사용 하 여 작업을 다시 사용 하 고 프로젝트를 구성할 수 있습니다.

### <a name="pipeline-draft"></a>파이프라인 초안

디자이너에서 파이프라인을 편집 하면 진행률이 **파이프라인 초안**으로 저장 됩니다. 언제 든 지 모듈을 추가 또는 제거 하 고 계산 대상을 구성 하 고 매개 변수를 만들어 파이프라인 초안을 편집할 수 있습니다.

유효한 파이프라인에는 다음과 같은 특징이 있습니다.

* 데이터 집합은 모듈에만 연결할 수 있습니다.
* 모듈은 데이터 집합 또는 다른 모듈에만 연결할 수 있습니다.
* 모듈에 대 한 모든 입력 포트는 데이터 흐름에 대 한 일부 연결을 포함 해야 합니다.
* 각 모듈에 필요한 모든 매개 변수를 설정 해야 합니다.

파이프라인 초안을 실행할 준비가 되 면 파이프라인 실행을 제출 합니다.

### <a name="pipeline-run"></a>파이프라인 실행

파이프라인을 실행할 때마다 파이프라인의 구성과 그 결과가 파이프라인 **실행**으로 작업 영역에 저장 됩니다. 파이프라인 실행으로 돌아가서 문제 해결 또는 감사 목적을 검사할 수 있습니다. 파이프라인 실행을 **복제** 하 여 편집할 새 파이프라인 초안을 만듭니다.

파이프라인 실행은 [실험](concept-azure-machine-learning-architecture.md#experiments) 으로 그룹화 되어 실행 기록을 구성 합니다. 모든 파이프라인 실행에 대 한 실험을 설정할 수 있습니다. 

## <a name="datasets"></a>데이터 세트

Machine learning 데이터 집합을 사용 하면 데이터에 쉽게 액세스 하 고 작업할 수 있습니다. 실험을 위해 디자이너에 많은 샘플 데이터 집합이 포함 되어 있습니다. 필요에 따라 더 많은 데이터 집합을 [등록할](how-to-create-register-datasets.md) 수 있습니다.

## <a name="module"></a>모듈

모듈은 데이터에 대해 수행할 수 있는 알고리즘입니다. 디자이너에는 데이터 수신 기능부터 학습, 점수 매기기 및 유효성 검사 프로세스에 이르는 다양 한 모듈이 있습니다.

모듈에는 모듈 내부 알고리즘을 구성하는 데 사용할 수 있는 매개 변수 집합이 포함될 수 있습니다. 캔버스에서 모듈을 선택 하면 모듈의 매개 변수가 캔버스 오른쪽의 속성 창에 표시 됩니다. 해당 창에서 매개 변수를 수정하여 모델을 튜닝할 수 있습니다. 디자이너에서 개별 모듈에 대 한 계산 리소스를 설정할 수 있습니다. 

![모듈 속성](./media/concept-designer/properties.png)

사용 가능한 기계 학습 알고리즘 라이브러리를 탐색 하는 데 도움이 필요한 경우 [알고리즘 & 모듈 참조 개요](algorithm-module-reference/module-reference.md) 를 참조 하세요.

## <a name="compute"></a>계산 리소스

작업 영역의 계산 리소스를 사용 하 여 파이프라인을 실행 하 고 배포 된 모델을 실시간 엔드포인트이 나 파이프라인 엔드포인트 (일괄 처리 유추의 경우)으로 호스팅합니다. 지원되는 컴퓨팅 대상은 다음과 같습니다.

| 컴퓨팅 대상 | 교육 | 배포 |
| ---- |:----:|:----:|
| Azure Machine Learning 컴퓨팅 | ✓ | |
| Azure Kubernetes Service | | ✓ |

계산 대상은 [Azure Machine Learning 작업 영역](concept-workspace.md)에 연결 됩니다. [Azure Machine Learning Studio (클래식)](https://ml.azure.com)의 작업 영역에서 계산 대상을 관리 합니다.

## <a name="deploy"></a>배포

실시간 추론를 수행 하려면 파이프라인을 **실시간 엔드포인트**으로 배포 해야 합니다. 실시간 엔드포인트은 외부 응용 프로그램과 점수 매기기 모델 간의 인터페이스를 만듭니다. 실시간 엔드포인트에 대 한 호출은 응용 프로그램에 대 한 예측 결과를 실시간으로 반환 합니다. 실시간 엔드포인트에 대 한 호출을 수행 하려면 엔드포인트을 배포할 때 생성 된 API 키를 전달 합니다. 엔드포인트은 웹 프로그래밍 프로젝트용으로 널리 사용 되는 다른 아키텍처를 기반으로 합니다.

실시간 엔드포인트은 Azure Kubernetes Service 클러스터에 배포 해야 합니다.

모델을 배포 하는 방법을 알아보려면 [자습서: 디자이너를 사용 하 여 machine learning 모델 배포](tutorial-designer-automobile-price-deploy.md)를 참조 하세요.

## <a name="publish"></a>게시

파이프라인을 **파이프라인 엔드포인트**에 게시할 수도 있습니다. 실시간 엔드포인트과 마찬가지로, 파이프라인 엔드포인트을 사용 하 여 REST 호출을 통해 외부 응용 프로그램에서 새 파이프라인 실행을 제출할 수 있습니다. 그러나 파이프라인 엔드포인트을 사용 하 여 실시간으로 데이터를 보내거나 받을 수 없습니다.

게시 된 파이프라인은 유연 하며 모델을 학습 또는 다시 학습 하 고, [batch 추론를 수행](how-to-run-batch-predictions-designer.md)하 고, 새 데이터를 처리 하는 등의 작업을 수행 하는 데 사용할 수 있습니다. 여러 파이프라인을 단일 파이프라인 엔드포인트에 게시 하 고 실행할 파이프라인 버전을 지정할 수 있습니다.

게시 된 파이프라인은 각 모듈에 대해 파이프라인 초안에서 정의한 계산 리소스에서 실행 됩니다.

디자이너에서 SDK와 동일한 [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) 개체를 만듭니다.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>시각적 인터페이스에서 디자이너로 이동

시각적 인터페이스 (미리 보기)가 업데이트 되었으며 이제 디자이너 Azure Machine Learning 되었습니다. 디자이너는 Azure Machine Learning의 다른 기능과 완벽 하 게 통합 되는 파이프라인 기반 백 엔드를 사용 하도록 다시 설계 되었습니다. 

이러한 업데이트의 결과로 시각적 인터페이스의 일부 개념과 용어는 변경 되거나 이름이 변경 되었습니다. 가장 중요 한 개념 변경 내용에 대해서는 아래 표를 참조 하세요. 

| 디자이너의 개념 | 이전에 시각적 인터페이스에서 |
| ---- |:----:|
| 파이프라인 초안 | 실험 |
| 실시간 엔드포인트 | 웹 서비스 |

### <a name="migrating-to-the-designer"></a>디자이너로 마이그레이션

디자이너에서 기존 시각적 인터페이스 실험 및 웹 서비스를 파이프라인과 실시간 엔드포인트으로 변환할 수 있습니다. 다음 단계를 사용 하 여 시각적 인터페이스 자산을 마이그레이션합니다.

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>다음 단계

* [자습서: 디자이너를 사용 하 여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md) 을 통해 예측 분석 및 기계 학습의 기본 사항을 알아봅니다.
* 샘플 중 하나를 사용 하 고 요구 사항에 맞게 수정 합니다.

- [샘플 1-회귀: 자동차 가격 예측](how-to-designer-sample-regression-automobile-price-basic.md)
- [샘플 2-회귀: 자동차 가격 예측에 대 한 알고리즘 비교](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [샘플 3-기능 선택이 포함 된 분류: 수입 예측](how-to-designer-sample-classification-predict-income.md)
- [샘플 4-분류: 신용 위험 예측 (비용 구분)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [샘플 5-분류: 변동 예측](how-to-designer-sample-classification-churn.md)
- [샘플 6-분류: 비행 지연 예측](how-to-designer-sample-classification-flight-delay.md)
- [샘플 7-텍스트 분류: 위키백과 SP 500 데이터 집합](how-to-designer-sample-text-classification.md)


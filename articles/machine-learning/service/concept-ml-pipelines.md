---
title: '파이프라인: 기계 학습 워크플로 최적화'
titleSuffix: Azure Machine Learning service
description: 이 문서에서는 Python용 Azure Machine Learning SDK로 빌드할 수 있는 기계 학습 파이프라인 및 파이프라인 사용의 장점에 대해 알아보겠습니다. ML(Machine Learning) 파이프라인은 데이터 과학자가 기계 학습 워크플로를 빌드, 최적화 및 관리하는 데 사용됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 12/4/2018
ms.custom: seodec18
ms.openlocfilehash: 3f1dd0921153d6b65bdc257f91019483adbb18fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821060"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용하여 기계 학습 파이프라인 빌드

이 문서에서는 Python용 Azure Machine Learning SDK로 빌드할 수 있는 기계 학습 파이프라인 및 파이프라인 사용의 장점에 대해 알아보겠습니다.

## <a name="what-are-machine-learning-pipelines"></a>기계 학습 파이프라인이란?

ML(기계 학습) 파이프라인, 데이터 과학자, 데이터 엔지니어 및 IT 전문가를 사용하면 다음에 포함된 단계에서 공동 작업을 할 수 있습니다.
+ 정규화 및 변환 같은 데이터 준비
+ 모델 학습
+ 모델 평가
+ 배포 

다음 다이어그램은 예제 파이프라인을 보여줍니다.

![Azure Machine Learning Service의 기계 학습 파이프라인](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>Azure 파이프라인 기술을 사용 해야 합니까?

Azure 클라우드는 여러 다른 파이프라인에 다른 목적으로 각 제공합니다. 다음 표에서 다양 한 파이프라인 및 용도 대 한를 나열 합니다.

| 파이프라인 | 기능 | 정식 파이프 |
| ---- | ---- | ---- |
| Azure Machine Learning 파이프라인 | 기계 학습 시나리오에 대 한 템플릿으로 사용할 수 있는 워크플로 학습 하는 재사용 가능한 컴퓨터를 정의 합니다. | 데이터 모델-> |
| [Azure Data Factory 파이프라인](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | 작업을 수행 하는 데 필요한 그룹 데이터 이동, 변환 및 제어 작업 합니다.  | -> 데이터 데이터 |
| [Azure 파이프라인](https://azure.microsoft.com/services/devops/pipelines/) | 지속적인 통합 및 업데이트 된 응용 프로그램의 플랫폼 있는 클라우드  | 코드-> 앱/서비스 |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 파이프라인을 빌드하는 이유는 무엇인가요?

[Python용 Azure Machine Learning SDK](#the-python-sdk-for-pipelines)를 사용하여 ML 파이프라인을 만들고 개별 파이프라인 실행을 제출 및 추적할 수 있습니다.

파이프라인을 사용하면 단순성, 속도, 이식성 및 재사용을 통해 워크플로를 최적화할 수 있습니다. Azure Machine Learning을 사용하여 파이프라인을 빌드하면 인프라 대신 전문 지식인 기계 학습에 집중할 수 있습니다.

워크플로를 조정하고 테스트할 때 별도의 단계를 사용하여 필요한 단계만 다시 실행할 수 있습니다. 단계는 파이프라인의 계산 단위입니다. 앞의 다이어그램에 표시된 대로 데이터 준비 태스크에는 여러 단계가 포함될 수 있습니다. 이러한 단계는 포함 되지만, 정규화, 변환, 유효성 검사 및 기능화 (featurization)로 제한 되지 않습니다. 데이터 원본 및 중간 데이터를 파이프라인을 통해 다시 사용하면 컴퓨팅 시간 및 리소스가 절약됩니다. 

파이프라인 설계 후에는 종종 파이프라인의 학습 루프를 미세 조정합니다. 파이프라인을 다시 실행하면 재실행이 필요한 단계(예: 업데이트된 교육 스크립트)로 넘어가며, 변경되지 않은 부분을 건너뜁니다. 단계를 실행하는 데 사용된 변경되지 않은 스크립트에도 동일한 패러다임이 적용됩니다. 

Azure Machine Learning을 사용 하 여 파이프라인의 각 단계에 대 한 다양 한 도구 키트 및 TensorFlow, PyTorch 등의 프레임 워크를 사용할 수 있습니다. Azure는 중간 데이터가 다운스트림 컴퓨팅 대상과 간편하게 공유될 수 있도록 사용하는 다양한 [컴퓨팅 대상](concept-azure-machine-learning-architecture.md) 간에 조정합니다. 

Azure Portal에서 직접 [파이프라인 실험에 대한 메트릭을 추적](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments). 

## <a name="key-advantages"></a>주요 장점

기계 학습 워크플로에 대한 파이프라인을 빌드하여 얻을 수 있는 데 주요 장점은 다음과 같습니다.

|주요 장점|설명|
|:-------:|-----------|
|**무인&nbsp;실행**|몇 가지 단계가 안정적인 무인 방식을 통해 병렬로 또는 순차적으로 실행되도록 예약합니다. 데이터 준비 및 모델링 작업이 며칠 또는 몇 주간 지속될 수 있기 때문에, 이제는 파이프라인이 실행되는 동안 다른 작업에 집중할 수 있습니다. |
|**다양한 혼합형 계산**|확장 가능한 이기종 계산 및 저장소에 안정적으로 조정되는 여러 파이프라인을 사용합니다. HDInsight, GPU 데이터 과학 VM, Databricks 등 다양한 컴퓨팅 대상에서 개별 파이프라인 단계를 실행할 수 있습니다. 이를 통해 사용 가능한 계산 옵션을 효율적으로 사용할 수 있습니다.|
|**재사용 가능**|다시 학습, 일괄 처리 채점 등의 특정 시나리오에 맞게 파이프라인을 템플릿화할 수 있습니다. 간단한 REST 호출을 통해 외부 시스템에서 파이프라인을 트리거합니다.|
|**추적 및 버전 관리**|반복하면서 데이터 및 결과 경로를 수동으로 추적하는 대신, Pipelines SDK를 사용하여 데이터 원본, 입력 및 출력의 이름과 버전을 명시적으로 지정합니다. 생산성 향상을 위해 스크립트와 데이터를 별도로 관리할 수도 있습니다.|

## <a name="the-python-sdk-for-pipelines"></a>파이프라인용 Python SDK

Python을 사용하여 ML 파이프라인을 만듭니다. Azure Machine Learning SDK는 데이터 종속성이 없을 때 파이프라인의 단계를 순차적 방식 및 병렬 방식으로 처리하는 명령적 구문을 제공합니다. Jupyter Notebook 또는 선호하는 다른 통합 개발 환경에서 이 SDK를 조작할 수 있습니다. 

선언적 데이터 종속성을 사용하여 작업을 최적화할 수 있습니다. 이 SDK에는 데이터 전송, 모델 게시 등의 일반적인 작업을 위해 사전 빌드된 모듈의 프레임워크가 포함되어 있습니다. 파이프라인에서 재사용할 수 있는 사용자 지정 단계를 구현하여 사용자 고유의 규칙을 모델링하도록 프레임워크를 확장할 수 있습니다. SDK에서 직접 컴퓨팅 대상 및 스토리지 리소스를 관리할 수도 있습니다.

일괄 처리 채점 또는 다시 학습 작업을 예약할 수 있도록 파이프라인을 템플릿으로 저장하고 REST 엔드포인트에 배포할 수 있습니다.

사용자 고유의 파이프라인을 빌드하는 방법은 [파이프라인에 대한 Python SDK 참조 문서](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 및 다음 섹션의 노트북을 참조하세요.

## <a name="example-notebooks"></a>노트북 예제
 
다음 노트북은 Azure Machine Learning을 사용하여 파이프라인을 보여 줍니다. [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

[첫 번째 파이프라인을 만드는](how-to-create-your-first-pipeline.md) 방법을 알아봅니다.

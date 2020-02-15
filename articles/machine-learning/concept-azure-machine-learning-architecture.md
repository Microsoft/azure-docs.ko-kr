---
title: 아키텍처 & 주요 개념
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 구성 하는 아키텍처, 용어, 개념 및 워크플로에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: 5f5522201534a54f5d132257553469eed5addab3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169863"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning 작동 방법: 아키텍처 및 개념

Azure Machine Learning에 대 한 아키텍처, 개념 및 워크플로에 대해 알아봅니다. 다음 다이어그램에서 서비스의 주요 구성 요소와 서비스를 사용하기 위한 일반적인 워크플로를 보여줍니다.

![Azure Machine Learning 아키텍처 및 워크플로](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>워크플로

Machine learning 모델 워크플로는 일반적으로 다음과 같은 순서를 따릅니다.

1. **타는**
    + **Python** 또는 비주얼 디자이너를 사용 하 여 기계 학습 교육 스크립트를 개발 합니다.
    + **컴퓨팅 대상**을 만들고 구성합니다.
    + 해당 환경에서 실행하도록 구성된 컴퓨팅 대상에 **스크립트를 제출**합니다. 학습 동안 **데이터 저장소**에서 스크립트를 읽거나 쓸 수 있습니다. 또한 실행 레코드는 **작업 영역**에서 **실행**으로 저장되고 **실험** 아래에 그룹화됩니다.

1. **패키지** -만족 스러운 실행을 찾은 후 **모델 레지스트리에서**지속형 모델을 등록 합니다.

1. **유효성 검사** - 현재 및 이전 실행에서 기록 **된 메트릭의 실험을 쿼리** 합니다. 메트릭이 원하는 결과를 표시하지 않으면 1단계로 돌아가 스크립트를 반복합니다.

1. **배포** -모델을 사용 하 고 Azure 또는 **IoT Edge 장치**에 **웹 서비스로** **모델을 배포** 하는 점수 매기기 스크립트를 개발 합니다.

1. **모니터** -학습 데이터 집합 및 배포 된 모델의 유추 데이터 간의 **데이터 드리프트** 를 모니터링 합니다. 필요한 경우 1 단계를 반복 하 여 새 학습 데이터로 모델을 다시 학습.

## <a name="tools-for-azure-machine-learning"></a>Azure Machine Learning 도구

Azure Machine Learning 다음 도구를 사용 합니다.

+  [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용 하 여 python 환경에서 서비스와 상호 작용 합니다.
+ [R에 대 한 AZURE MACHINE LEARNING SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)를 사용 하 여 r 환경에서 서비스와 상호 작용 합니다.
+ [AZURE MACHINE LEARNING CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli)를 사용 하 여 기계 학습 작업을 자동화 합니다.
+ [Azure Machine Learning 디자이너 (미리 보기)](concept-designer.md) 를 사용 하 여 코드를 작성 하지 않고 워크플로 단계를 수행 합니다.


> [!NOTE]
> 이 문서에서는 Azure Machine Learning에서 사용 하는 용어와 개념을 정의 하지만, Azure 플랫폼에 대 한 용어 및 개념은 정의 하지 않습니다. Azure 플랫폼 용어에 대한 자세한 내용은 [Microsoft Azure 용어집](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)을 참조하세요.

## <a name="glossary"></a>용어
+ <a href="#activities">활동</a>
+ <a href="#compute-targets">계산 대상</a>
+ <a href="#datasets-and-datastores">데이터 집합 & 데이터 저장소</a>
+ <a href="#endpoints">엔드포인트</a>
+ <a href="#environments">에서는</a>
+ [추정](#estimators)
+ <a href="#experiments">실험</a>
+ <a href="#github-tracking-and-integration">Git 추적</a>
+ <a href="#iot-module-endpoints">IoT 모듈</a>
+ <a href="#logging">Logging</a>
+ <a href="#ml-pipelines">ML 파이프라인</a>
+ <a href="#models">모델</a>
+ <a href="#runs">실행</a>
+ <a href="#run-configurations">실행 구성</a>
+ <a href="#snapshots">스냅샷</a>
+ <a href="#training-scripts">학습 스크립트</a>
+ <a href="#web-service-endpoint">웹 서비스</a>
+ <a href="#workspaces">작업 영역</a>

### <a name="activities"></a>활동

작업은 장기 실행 작업을 나타냅니다. 다음 조작은 작업의 예입니다.

* 컴퓨팅 대상 만들기 또는 삭제
* 컴퓨팅 대상에서 스크립트 실행

작업은 SDK 또는 웹 UI를 통해 알림을 제공할 수 있으므로, 사용자가 이러한 조작의 진행 상황을 쉽게 모니터링할 수 있습니다.

### <a name="compute-instance"></a>계산 인스턴스 (미리 보기)


**Azure Machine Learning 계산 인스턴스** (이전의 노트북 VM)는 기계 학습을 위해 설치 된 여러 도구 및 환경을 포함 하는 완전히 관리 되는 클라우드 기반 워크스테이션입니다. 계산 인스턴스는 학습 및 추론 작업에 대 한 계산 대상으로 사용할 수 있습니다. 대량 작업의 경우 다중 노드 크기 조정 기능을 사용 하는 [계산 클러스터 Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) 계산 대상이 더 나은 선택입니다.

[계산 인스턴스에](concept-compute-instance.md)대해 자세히 알아보세요.

### <a name="compute-targets"></a>컴퓨팅 대상

[계산 대상을](concept-compute-target.md) 사용 하면 학습 스크립트를 실행 하거나 서비스 배포를 호스트 하는 계산 리소스를 지정할 수 있습니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 일 수 있습니다.

[학습 및 배포에 사용할 수 있는 계산 대상](concept-compute-target.md)에 대해 자세히 알아보세요.

### <a name="datasets-and-datastores"></a>데이터 집합 및 데이터 저장소

데이터 집합 (미리 보기)을 사용 하면 데이터에 쉽게 액세스 하 고 작업을 수행할 수 있습니다. **Azure Machine Learning** 데이터 집합은 모델 학습 및 파이프라인 생성과 같은 다양 한 시나리오에서 데이터를 관리 합니다. Azure Machine Learning SDK를 사용 하 여 기본 저장소에 액세스 하 고, 데이터를 탐색 하 고, 다양 한 데이터 집합 정의의 수명 주기를 관리할 수 있습니다.

데이터 집합은 `from_delimited_files()` 또는 `to_pandas_dataframe()`를 사용 하는 것과 같이 널리 사용 되는 형식으로 데이터 작업을 위한 메서드를 제공 합니다.

자세한 내용은 [Azure Machine Learning 데이터 집합 만들기 및 등록](how-to-create-register-datasets.md)을 참조 하세요.  데이터 집합을 사용 하는 추가 예제는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets)을 참조 하세요.

**데이터** 저장소는 Azure storage 계정에 대 한 저장소 추상화입니다. 데이터 저장소는 Azure Blob 컨테이너 또는 Azure 파일 공유 중 하나를 백 엔드 스토리지로 사용할 수 있습니다. 각 작업 영역에는 기본 데이터 저장소가 있으며 추가 데이터 저장소를 등록할 수 있습니다. Python SDK API 또는 Azure Machine Learning CLI를 사용하여 데이터 저장소의 파일을 저장하고 검색합니다.

### <a name="endpoints"></a>엔드포인트

끝점은 클라우드 또는 통합 장치 배포를 위한 IoT 모듈에서 호스팅될 수 있는 웹 서비스로 모델을 인스턴스화하는 것입니다.

#### <a name="web-service-endpoint"></a>웹 서비스 끝점

모델을 웹 서비스로 배포 하는 경우 끝점을 Azure Container Instances, Azure Kubernetes Service 또는 FPGAs에 배포할 수 있습니다. 모델, 스크립트 및 관련 파일에서 서비스를 만듭니다. 이러한 컨테이너는 모델에 대 한 실행 환경을 포함 하는 기본 컨테이너 이미지에 배치 됩니다. 이미지에는 웹 서비스에 전송된 점수 매기기 요청을 수신하는 부하 분산된 HTTP 엔드포인트가 있습니다.

Azure는이 기능을 사용 하도록 설정한 경우 Application Insights 원격 분석 또는 모델 원격 분석을 수집 하 여 웹 서비스를 모니터링 하는 데 도움이 됩니다. 원격 분석 데이터는 사용자만 액세스할 수 있으며 Application Insights 및 스토리지 계정 인스턴스에 저장됩니다.

자동 크기 조정을 사용하도록 설정한 경우 Azure에서 배포 크기를 자동으로 조정합니다.

모델을 웹 서비스로 배포 하는 방법에 대 한 예제는 [Azure Container Instances에서 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md)를 참조 하세요.

#### <a name="iot-module-endpoints"></a>IoT 모듈 끝점

배포 된 IoT 모듈 끝점은 모델 및 관련 스크립트나 응용 프로그램 및 추가 종속성을 포함 하는 Docker 컨테이너입니다. Edge 장치에서 Azure IoT Edge를 사용 하 여 이러한 모듈을 배포 합니다.

모니터링을 사용하도록 설정한 경우 Azure에서는 Azure IoT Edge 모듈 내의 모델에서 원격 분석 데이터를 수집합니다. 원격 분석 데이터는 사용자만 액세스할 수 있으며 스토리지 계정 인스턴스에 저장됩니다.

Azure IoT Edge는 모듈이 실행 중인지 확인하고 모듈을 호스트 중인 디바이스를 모니터링합니다.

### <a name="environments"></a>환경

Azure ML 환경은 데이터 준비, 모델 학습 및 모델 처리를 위한 재현 가능한 환경을 만드는 데 사용 되는 구성 (Docker/Python/Spark/등)을 지정 하는 데 사용 됩니다. 이러한 항목은 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 휴대용 기계 학습 워크플로를 가능 하 게 하는 Azure Machine Learning 작업 영역 내에서 관리 되 고 버전이 지정 된 엔터티입니다

로컬 계산의 환경 개체를 사용 하 여 학습 스크립트를 개발 하 고, 대규모로 모델 학습을 위해 동일한 환경을 Azure Machine Learning 계산에서 재사용 하 고, 동일한 환경에서 모델을 배포할 수 있습니다. 

학습 및 유추를 위해 [재사용 가능한 ML 환경을 만들고 관리 하는 방법을](how-to-use-environments.md) 알아봅니다.

### <a name="estimators"></a>추정

인기 있는 프레임 워크를 사용 하 여 모델 학습을 용이 하 게 하기 위해 평가기 클래스를 사용 하면 실행 구성을 쉽게 만들 수 있습니다. 제네릭 [평가기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) 을 만들고 사용 하 여 선택한 학습 프레임 워크 (예: scikit)를 사용 하는 학습 스크립트를 제출할 수 있습니다.

PyTorch, TensorFlow 및 체 이너 Azure Machine Learning 작업의 경우 이러한 프레임 워크 사용을 간소화 하기 위해 각 [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)및 [체 이너](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) 추정 제공 됩니다.

자세한 내용은 다음 문서를 참조하세요.

* [추정를 사용 하 여 ML 모델을 학습](how-to-train-ml-models.md)합니다.
* [Azure Machine Learning를 사용 하 여 대규모로 Pytorch 심층 학습 모델을 학습](how-to-train-pytorch.md)합니다.
* [Azure Machine Learning를 사용 하 여 대규모로 TensorFlow 모델을 학습 하 고 등록](how-to-train-tensorflow.md)합니다.
* [Azure Machine Learning를 사용 하 여 대규모로 체 이너 모델을 학습 하 고 등록](how-to-train-chainer.md)합니다.

### <a name="experiments"></a>실험

실험은 지정된 스크립트의 많은 실행을 그룹화한 것입니다. 실험은 항상 작업 영역에 속합니다. 실행을 제출할 때 실험 이름을 제공합니다. 실행에 대한 정보는 해당 실험 아래에 저장됩니다. 실행을 제출하고 존재하지 않는 실험 이름을 지정하면 새로 지정된 해당 이름을 가진 새 실험이 자동으로 생성됩니다.

실험을 사용 하는 예제는 [자습서: 첫 번째 모델 학습](tutorial-1st-experiment-sdk-train.md)을 참조 하세요.


### <a name="github-tracking-and-integration"></a>GitHub 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리 인 학습 실행을 시작 하면 리포지토리에 대 한 정보가 실행 기록에 저장 됩니다. 평가기, ML 파이프라인 또는 스크립트 실행을 사용 하 여 제출 된 실행에서 작동 합니다. SDK 또는 Machine Learning CLI에서 제출 된 실행에 대해서도 작동 합니다.

자세한 내용은 [Azure Machine Learning에 대 한 Git 통합](concept-train-model-git-integration.md)을 참조 하세요.

### <a name="logging"></a>로깅

솔루션을 개발할 때 Python 스크립트에서 Azure Machine Learning Python SDK를 사용하여 임의 메트릭을 기록합니다. 실행 후에 메트릭을 쿼리하여 실행에서 배포하려는 모델을 생성했는지 여부를 확인합니다.

### <a name="ml-pipelines"></a>ML 파이프라인

기계 학습 파이프라인을 사용하여 기계 학습 단계를 연결하는 워크플로를 만들고 관리합니다. 예를 들어 파이프라인에는 데이터 준비, 모델 학습, 모델 배포 및 유추/점수 매기기 단계가 포함 될 수 있습니다. 각 단계(phase)는 각각 다양한 컴퓨팅 대상에서 자동으로 실행될 수 있는 여러 단계(step)를 포함할 수 있습니다. 

파이프라인 단계는 다시 사용할 수 있으며 해당 단계의 출력이 변경 되지 않은 경우 후속 단계를 다시 실행 하지 않고 실행할 수 있습니다. 예를 들어 데이터가 변경 되지 않은 경우 비용이 많이 드는 데이터 준비 단계를 다시 실행 하지 않고 모델을 다시 학습 수 있습니다. 파이프라인을 사용 하면 기계 학습 워크플로의 개별 영역에서 작업 하는 동안 데이터 과학자 공동 작업을 수행할 수 있습니다.

이 서비스를 사용한 기계 학습 파이프라인에 대한 자세한 내용은 [파이프라인 및 Azure Machine Learning](concept-ml-pipelines.md)를 참조하세요.

### <a name="models"></a>모델

간단하게 설명하면, 하나의 모델은 입력을 받아들이고 출력을 생성하는 코드의 한 조각을 말합니다. 기계 학습 모델을 만드는 동안 알고리즘을 선택하고, 데이터를 제공하고, 하이퍼 매개 변수를 조정합니다. 학습은 학습된 모델을 생성하는 반복 프로세스이며, 학습된 모듈에는 모델이 학습 프로세스 중에 습득한 내용이 캡슐화되어 있습니다.

모델은 Azure Machine Learning에서 실행을 통해 생성됩니다. Azure Machine Learning 외부에서 학습된 모델을 사용할 수도 있습니다. 모델을 Azure Machine Learning 작업 영역에 등록할 수 있습니다.

Azure Machine Learning은 프레임워크에 제약이 없습니다. 모델을 만들 때 Scikit, XGBoost, PyTorch, TensorFlow 및 체 이너와 같은 인기 있는 기계 학습 프레임 워크를 사용할 수 있습니다.

Scikit 및 평가기를 사용 하 여 모델을 학습 하는 방법에 대 한 예제는 [자습서: Azure Machine Learning을 사용 하 여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조 하세요.

**모델 레지스트리** 는 Azure Machine Learning 작업 영역에 있는 모든 모델을 추적 합니다.

모델은 이름 및 버전으로 식별됩니다. 기존 이름과 동일한 이름을 사용하여 모델을 등록할 때마다 레지스트리는 해당 모델을 새 버전으로 간주합니다. 버전 번호가 증가하면 동일 이름으로 새 모델을 등록합니다.

모델을 등록할 때 추가 메타데이터 태그를 제공하면 모델을 검색할 때 해당 태그를 사용할 수 있습니다.

> [!TIP]
> 등록 된 모델은 모델을 구성 하는 하나 이상의 파일에 대 한 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 된 모든 파일을 받을 수 있습니다.

활성 배포에서 사용 중인 등록 된 모델은 삭제할 수 없습니다.

모델을 등록하는 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.

### <a name="runs"></a>실행

실행은 학습 스크립트를 한 번 실행 하는 것입니다. Azure Machine Learning 모든 실행을 기록 하 고 다음 정보를 저장 합니다.

* 실행에 대한 메타데이터(타임스탬프, 기간 등)
* 스크립트를 통해 기록된 메트릭
* 실험을 통해 자동 수집되거나 사용자가 명시적으로 업로드한 출력 파일
* 실행 전의 스크립트를 포함하는 디렉터리의 스냅샷

모델을 학습시키기 위해 스크립트를 제출할 때 모델을 생성합니다. 실행에는 0개 이상의 자식 실행이 포함될 수 있습니다. 예를 들어 최상위 실행에는 두 개의 자식 실행이 포함될 수 있고, 각 자식 실행에는 자체 자식 실행이 포함될 수 있습니다.

### <a name="run-configurations"></a>실행 구성

실행 구성은 지정된 컴퓨팅 대상에서 스크립트를 실행해야 하는 방법을 정의하는 지침 세트를 보여줍니다. 구성에는 기존 Python 환경을 사용할지 또는 사양에서 빌드되는 Conda 환경을 사용할지 여부와 같은 광범위한 동작 정의 세트가 포함됩니다.

실행 구성은 학습 스크립트를 포함하는 디렉터리 내의 파일에 지속되거나, 메모리 내 개체로 구성되고 실행을 제출하는 데 사용될 수 있습니다.

실행 구성 예제는 [모델 학습을 위한 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md)을 참조하세요.
### <a name="snapshots"></a>스냅샷

실행을 제출하면 Azure Machine Learning은 스크립트를 포함하는 디렉터리를 zip 파일로 압축하여 컴퓨팅 대상으로 보냅니다. 그런 다음, zip 파일이 추출되고 스크립트가 실행됩니다. 또한 Azure Machine Learning은 zip 파일을 실행 기록의 일부인 스냅샷으로 저장합니다. 작업 영역에 대한 액세스 권한이 있는 사용자는 실행 기록을 찾아보고 스냅샷을 다운로드할 수 있습니다.

> [!NOTE]
> 불필요 한 파일이 스냅숏에 포함 되지 않도록 하려면 ignore 파일 (. .gitignore 또는. amlignore)을 만듭니다. 이 파일을 스냅숏 디렉터리에 넣고 무시할 파일 이름을 추가 합니다. Amlignore 파일은 [.gitignore 파일과 동일한 구문과 패턴](https://git-scm.com/docs/gitignore)을 사용 합니다. 두 파일이 모두 있는 경우 amlignore 파일이 우선적으로 적용 됩니다.

### <a name="training-scripts"></a>교육 스크립트

모델을 학습시키려면 학습 스크립트 및 연결된 파일을 포함하는 디렉터리를 지정합니다. 학습 중에 수집된 정보를 저장하는 데 사용되는 실험 이름도 지정합니다. 학습 중에 전체 디렉터리가 학습 환경(컴퓨팅 대상)에 복사되고 실행 구성을 통해 지정된 스크립트가 시작됩니다. 또한 디렉터리의 스냅샷이 작업 영역의 실험 아래에 저장됩니다.

예제는 [자습서: Azure Machine Learning을 사용 하 여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조 하세요.

### <a name="workspaces"></a>작업 영역

[작업 영역은](concept-workspace.md) Azure Machine Learning의 최상위 리소스입니다. Azure Machine Learning를 사용할 때 생성 하는 모든 아티팩트를 사용할 수 있는 중앙 집중식 환경을 제공 합니다. 작업 영역을 다른 사용자와 공유할 수 있습니다. 작업 영역에 대 한 자세한 설명은 [Azure Machine Learning 작업 영역 이란?](concept-workspace.md)을 참조 하세요.

### <a name="next-steps"></a>다음 단계

Azure Machine Learning를 시작 하려면 다음을 참조 하세요.

* [Azure Machine Learning이란 무엇인가요?](overview-what-is-azure-ml.md)
* [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)
* [자습서 (1 부): 모델 학습](tutorial-train-models-with-aml.md)

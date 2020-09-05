---
title: 아키텍처 및 주요 개념
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 구성 하는 아키텍처, 용어 및 개념에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: c3abd6a57eac851a5440ecdef6185cb310305434
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146779"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Azure Machine Learning 작동 방법: 아키텍처 및 개념

[Azure Machine Learning](overview-what-is-azure-ml.md)의 아키텍처 및 개념에 대해 알아봅니다.  이 문서에서는 구성 요소와 구성 요소를 함께 사용 하 여 기계 학습 모델을 빌드, 배포 및 유지 관리 하는 프로세스를 지 원하는 방법에 대해 간략하게 설명 합니다.

## <a name="workspace"></a><a name="workspace"></a> 환경이

[기계 학습 작업 영역은](concept-workspace.md) Azure Machine Learning에 대 한 최상위 리소스입니다.

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="다이어그램: 작업 영역 및 해당 구성 요소의 Azure Machine Learning 아키텍처":::

작업 영역은 다음에 대 한 중앙 위치입니다.

* [계산](#compute-instance) 등 모델의 학습 및 배포에 사용 하는 리소스를 관리 합니다.
* 다음을 포함 하 여 Azure Machine Learning를 사용할 때 만든 자산을 저장 합니다.
  * [환경](#environments)
  * [실험](#experiments)
  * [파이프라인](#ml-pipelines)
  * [데이터 세트](#datasets-and-datastores)
  * [Models](#models)
  * [엔드포인트](#endpoints)

작업 영역에는 작업 영역에서 사용 하는 다른 Azure 리소스가 포함 되어 있습니다.

+ [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/): 학습 중 및 모델을 배포할 때 사용 하는 docker 컨테이너를 등록 합니다. 비용을 최소화 하기 위해 ACR은 배포 이미지를 만들 때만 생성 됩니다.
+ [Azure Storage 계정](https://azure.microsoft.com/services/storage/): 작업 영역에 대 한 기본 데이터 저장소로 사용 됩니다.  Azure Machine Learning 계산 인스턴스와 함께 사용 되는 jupyter 노트북도 여기에 저장 됩니다.
+ [Azure 애플리케이션 Insights](https://azure.microsoft.com/services/application-insights/): 모델에 대 한 모니터링 정보를 저장 합니다.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): 계산 대상에서 사용 하는 암호 및 작업 영역에 필요한 기타 중요 한 정보를 저장 합니다.

작업 영역을 다른 사용자와 공유할 수 있습니다.

## <a name="computes"></a>Computes

<a name="compute-targets"></a>[계산 대상은](concept-compute-target.md) 교육 스크립트를 실행 하거나 서비스 배포를 호스트 하는 데 사용 하는 컴퓨터 또는 컴퓨터 집합입니다. 로컬 컴퓨터 또는 원격 계산 리소스를 계산 대상으로 사용할 수 있습니다.  계산 대상을 사용 하면 로컬 컴퓨터에서 교육을 시작한 다음 학습 스크립트를 변경 하지 않고 클라우드로 확장할 수 있습니다.

Azure Machine Learning에는 Machine Learning 작업에 대해 구성 된 두 개의 완전히 관리 되는 클라우드 기반 VM (가상 머신)이 도입 되었습니다.

* <a name="compute-instance"></a>**계산 인스턴스**: 계산 인스턴스는 기계 학습을 위해 설치 된 여러 도구 및 환경을 포함 하는 VM입니다. 계산 인스턴스는 주로 개발 워크스테이션에 사용 됩니다.  설치 하지 않아도 되는 샘플 노트북 실행을 시작할 수 있습니다. 계산 인스턴스를 학습 및 추론 작업에 대 한 계산 대상으로 사용할 수도 있습니다.

* **계산 클러스터**: 계산 클러스터는 다중 노드 크기 조정 기능이 있는 vm의 클러스터입니다. 계산 클러스터는 대량 작업 및 프로덕션을 위한 계산 대상에 더 적합 합니다.  작업이 제출 되 면 클러스터가 자동으로 확장 됩니다.  를 학습 계산 대상으로 사용 하거나 개발/테스트 배포에 사용 합니다.

계산 대상 학습에 대 한 자세한 내용은 [계산 대상 학습](concept-compute-target.md#train)을 참조 하세요.  배포 계산 대상에 대 한 자세한 내용은 [배포 대상](concept-compute-target.md#deploy)을 참조 하세요.

## <a name="datasets-and-datastores"></a>데이터 세트 및 데이터 저장소

데이터 [**집합을 Azure Machine Learning**](concept-data.md#datasets) 데이터에 쉽게 액세스 하 고 작업을 수행할 수 있습니다. 데이터 집합을 만들면 데이터 원본 위치에 대 한 참조를 해당 메타 데이터의 복사본과 함께 만듭니다. 데이터는 기존 위치에 남아 있으므로 추가 저장소 비용이 발생 하지 않으며 데이터 원본의 무결성을 위험 하지 않습니다.

자세한 내용은 [Azure Machine Learning 데이터 세트 만들기 및 등록](how-to-create-register-datasets.md)을 참조하세요.  데이터 세트를 사용하는 추가 예제는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial)을 참조하세요.

데이터 집합은 데이터 [저장소](concept-data.md#datastores) 를 사용 하 여 Azure storage 서비스에 안전 하 게 연결 합니다. Datastores는 인증 자격 증명과 원래 데이터 원본의 무결성을 위험에 노출 하지 않고 연결 정보를 저장 합니다. 작업 영역과 연결 된 Key Vault에서 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장 하므로 스크립트에 하드 코딩 하지 않고도 저장소에 안전 하 게 액세스할 수 있습니다.

## <a name="environments"></a>환경

[작업 영역](#workspace)  >  **환경**

[환경은](concept-environments.md) machine learning 모델의 학습 또는 점수 매기기가 발생 하는 환경을 캡슐화 하는 것입니다. 환경에서는 학습 및 점수 매기기 스크립트와 관련 된 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정 합니다.  

코드 샘플은 [환경을 사용 하는 방법](how-to-use-environments.md#manage-environments)의 "환경 관리" 섹션을 참조 하세요.

## <a name="experiments"></a>실험

[작업 영역](#workspace)  >  **실험**

실험은 지정된 스크립트의 많은 실행을 그룹화한 것입니다. 실험은 항상 작업 영역에 속합니다. 실행을 제출할 때 실험 이름을 제공합니다. 실행에 대한 정보는 해당 실험 아래에 저장됩니다. 실험을 제출할 때 이름이 존재 하지 않으면 새 실험이 자동으로 만들어집니다.
  
실험을 사용하는 예제는 [자습서: 첫 번째 모델 학습](tutorial-1st-experiment-sdk-train.md)을 참조하세요.

### <a name="runs"></a>실행

[작업 영역](#workspace)  >  [실험](#experiments)  >  **실행**

실행은 학습 스크립트의 단일 실행을 말합니다. 실험은 일반적으로 여러 실행을 포함합니다.

Azure Machine Learning은 모든 실행을 기록하고 실험에 다음 정보를 저장합니다.

* 실행에 대한 메타데이터(타임스탬프, 기간 등)
* 스크립트를 통해 기록된 메트릭
* 실험을 통해 자동 수집되거나 사용자가 명시적으로 업로드한 출력 파일
* 실행 전의 스크립트를 포함하는 디렉터리의 스냅샷

모델을 학습시키기 위해 스크립트를 제출할 때 모델을 생성합니다. 실행에는 0개 이상의 자식 실행이 포함될 수 있습니다. 예를 들어 최상위 실행에는 두 개의 자식 실행이 포함될 수 있고, 각 자식 실행에는 자체 자식 실행이 포함될 수 있습니다.

### <a name="run-configurations"></a>실행 구성

[작업 영역](#workspace)  >  [실험](#experiments)  >  [실행](#runs)  >  **실행 구성**

실행 구성은 지정된 컴퓨팅 대상에서 스크립트를 실행해야 하는 방법을 정의하는 지침 세트를 보여줍니다. 구성에는 기존 Python 환경을 사용할지 또는 사양에서 빌드되는 Conda 환경을 사용할지 여부와 같은 광범위한 동작 정의 세트가 포함됩니다.

실행 구성은 학습 스크립트를 포함 하는 디렉터리 내의 파일에 보관 될 수 있습니다.   또는 메모리 내 개체로 생성 되 고 실행을 전송 하는 데 사용 될 수 있습니다.

예제 실행 구성은 [계산 대상을 사용 하 여 모델 학습](how-to-set-up-training-targets.md)을 참조 하세요.

### <a name="estimators"></a>예측 도구

인기 있는 프레임워크를 사용하여 모델 학습을 용이하게 하기 위해 예측 도구 클래스를 사용하여 실행 구성을 쉽게 만들 수 있습니다. 제네릭 [예측 도구](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)를 만들어서 사용하면 원하는 학습 프레임워크(예: scikit)를 사용하는 학습 스크립트를 제출할 수 있습니다.

추정에 대 한 자세한 내용은 [추정를 사용 하 여 ML 모델 학습](how-to-train-ml-models.md)을 참조 하세요.

### <a name="snapshots"></a>스냅샷

[작업 영역](#workspace)  >  [실험](#experiments)  >  [실행](#runs)  >  **스냅숏**

실행을 제출하면 Azure Machine Learning은 스크립트를 포함하는 디렉터리를 zip 파일로 압축하여 컴퓨팅 대상으로 보냅니다. 그런 다음, zip 파일이 추출되고 스크립트가 실행됩니다. 또한 Azure Machine Learning은 zip 파일을 실행 기록의 일부인 스냅샷으로 저장합니다. 작업 영역에 대한 액세스 권한이 있는 사용자는 실행 기록을 찾아보고 스냅샷을 다운로드할 수 있습니다.


### <a name="logging"></a>로깅

솔루션을 개발할 때 Python 스크립트에서 Azure Machine Learning Python SDK를 사용하여 임의 메트릭을 기록합니다. 실행 후에 메트릭을 쿼리하여 실행에서 배포하려는 모델을 생성했는지 여부를 확인합니다.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Git 추적 및 통합

원본 디렉터리가 로컬 Git 리포지토리인 학습 실행을 시작하면 리포지토리에 대한 정보가 실행 기록에 저장됩니다. 이 작업은 예측 도구, ML 파이프라인 또는 스크립트 실행을 사용하여 제출된 실행에 대해 진행됩니다. SDK 또는 Machine Learning CLI에서 제출된 실행에 대해서도 진행됩니다.

자세한 내용은 [Azure Machine Learning에 대한 Git 통합](concept-train-model-git-integration.md)을 참조하세요.

## <a name="models"></a>모델

간단하게 설명하면, 하나의 모델은 입력을 받아들이고 출력을 생성하는 코드의 한 조각을 말합니다. Machine learning 모델을 만들려면 알고리즘을 선택 하 고, 데이터를 제공 하 고, 하이퍼 [매개 변수를 조정](how-to-tune-hyperparameters.md)해야 합니다. 학습은 학습된 모델을 생성하는 반복 프로세스이며, 학습된 모듈에는 모델이 학습 프로세스 중에 습득한 내용이 캡슐화되어 있습니다.

Azure Machine Learning 외부에서 학습 된 모델을 가져올 수 있습니다. 또는 Azure Machine Learning에서 [계산 대상](#compute-targets) 에 [실험](#experiments) [실행](#runs) 을 제출 하 여 모델을 학습 시킬 수 있습니다. 모델이 있으면 작업 영역에 모델을 [등록](#register-model) 합니다.

Azure Machine Learning은 프레임워크에 제약이 없습니다. 모델을 만드는 경우 Scikit-learn, XGBoost, PyTorch, TensorFlow 및 Chainer와 같은 인기 있는 기계 학습 프레임워크를 사용할 수 있습니다.

Scikit를 사용 하 여 모델을 학습 하는 방법에 대 한 예제는 [자습서: Azure Machine Learning 사용 하 여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조 하세요.


### <a name="model-registry"></a><a name="register-model"></a> 모델 레지스트리

[작업 영역](#workspace)  >  **모델**

**모델 레지스트리** 를 사용 하면 Azure Machine Learning 작업 영역에서 모든 모델을 추적할 수 있습니다.

모델은 이름 및 버전으로 식별됩니다. 기존 이름과 동일한 이름을 사용하여 모델을 등록할 때마다 레지스트리는 해당 모델을 새 버전으로 간주합니다. 버전 번호가 증가하면 동일 이름으로 새 모델을 등록합니다.

모델을 등록할 때 추가 메타데이터 태그를 제공하면 모델을 검색할 때 해당 태그를 사용할 수 있습니다.

> [!TIP]
> 등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리적 컨테이너입니다. 예를 들어, 여러 파일에 저장된 모델의 경우 Azure Machine Learning 작업 영역에서 단일 모델로 등록할 수 있습니다. 등록 후에 등록된 모델을 다운로드하거나 배포하고 등록된 모든 파일을 받을 수 있습니다.

활성 배포에서 사용 중인 등록된 모델은 삭제할 수 없습니다.

모델을 등록하는 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.

## <a name="deployment"></a>배포

[등록 된 모델](#register-model) 을 서비스 끝점으로 배포 합니다. 다음 구성 요소가 필요 합니다.

* **환경**. 이 환경은 유추를 위해 모델을 실행 하는 데 필요한 종속성을 캡슐화 합니다.
* **점수 매기기 코드**. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 요청 점수를 받은 후 결과를 반환 합니다.
* **유추 구성**. 유추 구성은 환경, 항목 스크립트 및 모델을 서비스로 실행 하는 데 필요한 기타 구성 요소를 지정 합니다.

이러한 구성 요소에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

### <a name="endpoints"></a>엔드포인트

[작업 영역](#workspace)  >  **끝점**

엔드포인트는 클라우드에서 호스트될 수 있는 웹 서비스 또는 통합형 디바이스 배포를 위한 IoT 모듈을 사용해 모델을 인스턴스화하는 것입니다.

#### <a name="web-service-endpoint"></a>웹 서비스 엔드포인트

모델을 웹 서비스로 배포 하는 경우 끝점을 Azure Container Instances, Azure Kubernetes Service 또는 FPGAs에 배포할 수 있습니다. 모델, 스크립트 및 연결된 파일에서 서비스를 만듭니다. 이러한 이미지는 모델에 대 한 실행 환경을 포함 하는 기본 컨테이너 이미지에 배치 됩니다. 이미지에는 웹 서비스에 전송된 점수 매기기 요청을 수신하는 부하 분산된 HTTP 엔드포인트가 있습니다.

Application Insights 원격 분석 또는 모델 원격 분석을 사용 하도록 설정 하 여 웹 서비스를 모니터링할 수 있습니다. 원격 분석 데이터는 사용자만 액세스할 수 있습니다.  Application Insights 및 저장소 계정 인스턴스에 저장 됩니다.

자동 크기 조정을 사용하도록 설정한 경우 Azure에서 배포 크기를 자동으로 조정합니다.

모델을 웹 서비스로 배포하는 예제는 [Azure Container Instance에 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md)를 참조하세요.

#### <a name="iot-module-endpoints"></a>IoT 모듈 엔드포인트

배포된 IoT 모듈 엔드포인트는 모델 및 연결된 스크립트나 애플리케이션과 모든 추가 종속성을 포함하는 Docker 컨테이너입니다. Edge 디바이스에서 Azure IoT Edge를 사용하여 이러한 모듈을 배포합니다.

모니터링을 사용하도록 설정한 경우 Azure에서는 Azure IoT Edge 모듈 내의 모델에서 원격 분석 데이터를 수집합니다. 원격 분석 데이터는 사용자만 액세스할 수 있으며 스토리지 계정 인스턴스에 저장됩니다.

Azure IoT Edge는 모듈이 실행 중인지 확인하고 모듈을 호스트 중인 디바이스를 모니터링합니다. 
## <a name="automation"></a>Automation

### <a name="azure-machine-learning-cli"></a>Azure Machine Learning CLI 

[AZURE MACHINE LEARNING CLI](reference-azure-machine-learning-cli.md) 는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 Azure CLI에 대 한 확장입니다. 이 확장은 기계 학습 작업을 자동화 하는 명령을 제공 합니다.

### <a name="ml-pipelines"></a>ML 파이프라인

기계 [학습 파이프라인](concept-ml-pipelines.md) 을 사용 하 여 기계 학습 단계를 함께 연결 하는 워크플로를 만들고 관리 합니다. 예를 들어, 파이프라인에는 데이터 준비, 모델 학습, 모델 배포 및 추론/점수 매기기 단계가 포함될 수 있습니다. 각 단계(phase)는 각각 다양한 컴퓨팅 대상에서 자동으로 실행될 수 있는 여러 단계(step)를 포함할 수 있습니다. 

파이프라인 단계는 다시 사용할 수 있으며 해당 단계의 출력이 변경되지 않은 경우 이전 단계를 다시 실행하지 않고 실행할 수 있습니다. 예를 들어, 데이터가 변경되지 않은 경우 비용이 많이 드는 데이터 준비 단계를 다시 실행하지 않고 모델을 다시 학습할 수 있습니다. 파이프라인을 사용하여 데이터 과학자들은 기계 학습 워크플로의 개별 영역에서 작업하면서 협력할 수 있습니다.

## <a name="interacting-with-your-workspace"></a>작업 영역과 상호 작용

### <a name="studio"></a>스튜디오

[Azure Machine Learning studio](https://ml.azure.com) 에서는 작업 영역에 있는 모든 아티팩트의 웹 보기를 제공 합니다.  데이터 집합, 실험, 파이프라인, 모델 및 끝점의 결과 및 세부 정보를 볼 수 있습니다.  또한 스튜디오에서 계산 리소스 및 데이터 저장소를 관리할 수 있습니다.

또한 스튜디오는 Azure Machine Learning의 일부인 대화형 도구에 액세스할 수 있습니다.

+ 코드를 작성 하지 않고 워크플로 단계를 수행 하 [Azure Machine Learning 디자이너 (미리 보기)](concept-designer.md)
+ [자동화 된 기계 학습](concept-automated-ml.md) 을 위한 웹 환경
+ 프로젝트를 만들고, 관리 하 고, 모니터링 하 여 데이터에 레이블을 지정 하는 [데이터 레이블 지정 프로젝트](how-to-create-labeling-projects.md)

### <a name="programming-tools"></a>프로그래밍 도구

> [!IMPORTANT]
> 아래 표시 된 (미리 보기) 도구는 현재 공개 미리 보기로 제공 됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

+  [Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용하여 Python 환경에서 서비스와 상호 작용합니다.
+ R (미리 보기) [에 대 한 AZURE MACHINE LEARNING SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html) 를 사용 하 여 모든 r 환경에서 서비스와 상호 작용 합니다.
+ 자동화에 [AZURE MACHINE LEARNING CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) 를 사용 합니다.
+ [많은 모델 솔루션 가속기](https://aka.ms/many-models)(미리 보기)는 Azure Machine Learning을 기반으로 하며 수백 또는 수천 개의 기계 학습 모델을 학습, 운영 및 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning을 시작하려면 다음을 참조하세요.

* [Azure Machine Learning이란 무엇인가요?](overview-what-is-azure-ml.md)
* [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)
* [자습서(1부): 모델 학습](tutorial-train-models-with-aml.md)

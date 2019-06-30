---
title: 아키텍처 및 주요 개념
titleSuffix: Azure Machine Learning service
description: 아키텍처, 용어, 개념 및 Azure Machine Learning 서비스를 구성 하는 워크플로 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: e0181eea2895dbc2b3db3367c850140e3fad21d4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331714"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Azure Machine Learning 서비스 작동 방법: 아키텍처 및 개념

아키텍처, 개념 및 Azure Machine Learning 서비스에 대 한 워크플로 알아봅니다. 다음 다이어그램에서 서비스의 주요 구성 요소와 서비스를 사용하기 위한 일반적인 워크플로를 보여줍니다.

[![Azure Machine Learning Service 아키텍처 및 워크플로](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>워크플로

Machine learning 워크플로 일반적으로이 순서를 따릅니다.

1. 기계 학습에서 스크립트 개발 **Python** 또는 시각적 인터페이스를 사용 하 여 합니다.
1. **‘계산 대상’** 을 만들고 구성합니다.
1. 해당 환경에서 실행하도록 구성된 계산 대상에 **‘스크립트를 제출’** 합니다. 학습 동안 **데이터 저장소**에서 스크립트를 읽거나 쓸 수 있습니다. 또한 실행 레코드는 **작업 영역**에서 **실행**으로 저장되고 **실험** 아래에 그룹화됩니다.
1. **‘실험을 쿼리’** 하여 현재 및 과거 실행에서 기록된 메트릭을 확인합니다. 메트릭이 원하는 결과를 표시하지 않으면 1단계로 돌아가 스크립트를 반복합니다.
1. 만족스러운 실행이 발견되면 **모델 레지스트리**에 지속되는 모델을 등록합니다.
1. 모델을 사용 하는 점수 매기기 스크립트를 개발 및 **모델을 배포할** 으로 **웹 서비스** Azure에서 또는 **IoT Edge 장치**합니다.

다음 중 하나를 사용 하 여 이러한 단계를 수행.
+ [Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Azure Machine Learning VS Code 확장](how-to-vscode-tools.md)
+  [Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)](ui-concept-visual-interface.md)


## <a name="glossary-of-concepts"></a>개념의 용어집

+ <a href="#workspaces">작업 영역</a>
+ <a href="#experiments">실험</a>
+ <a href="#models">모델</a>
+ <a href="#run-configurations">실행 구성</a>
+ <a href="#datasets-and-datastores">데이터 집합 및 데이터 저장소</a>
+ <a href="#compute-targets">계산 대상</a>
+ <a href="#training-scripts">학습 스크립트</a>
+ <a href="#runs">실행</a>
+ <a href="#github-tracking-and-integration">Git 추적</a>
+ <a href="#snapshots">스냅숏</a>
+ <a href="#activities">활동</a>
+ <a href="#images">이미지</a>
+ <a href="#deployment">배포</a>
+ <a href="#web-service-deployments">웹 서비스</a>
+ <a href="#iot-module-deployments">IoT 모듈</a>
+ <a href="#ml-pipelines">ML 파이프라인</a>
+ <a href="#logging">로깅</a>

> [!NOTE]
> 이 문서에서는 Azure Machine Learning에서 사용되는 용어와 개념을 정의하지만, Azure 플랫폼에 대한 용어와 개념은 다루지 않습니다. Azure 플랫폼 용어에 대한 자세한 내용은 [Microsoft Azure 용어집](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology)을 참조하세요.


### <a name="workspaces"></a>작업 영역

[작업 영역](concept-workspace.md) Azure Machine Learning 서비스에 대 한 최상위 리소스입니다. 작업 영역은 Azure Machine Learning Service를 사용하는 경우 만드는 모든 아티팩트를 사용할 수 있는 중앙 집중식 위치를 제공합니다.

다음은 작업 영역의 분류 체계를 보여주는 다이어그램입니다.

[![작업 영역 분류](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

작업 영역에 대 한 자세한 내용은 참조 하세요. [는 Azure Machine Learning 작업 영역 이란?](concept-workspace.md)합니다.

### <a name="experiments"></a>실험

실험은 지정된 스크립트의 많은 실행을 그룹화한 것입니다. 실험은 항상 작업 영역에 속합니다. 실행을 제출할 때 실험 이름을 제공합니다. 실행에 대한 정보는 해당 실험 아래에 저장됩니다. 실행을 제출하고 존재하지 않는 실험 이름을 지정하면 새로 지정된 해당 이름을 가진 새 실험이 자동으로 생성됩니다.

실험을 사용하는 예제는 [빠른 시작: Azure Machine Learning Service 시작](quickstart-run-cloud-notebook.md)을 참조하세요.

### <a name="models"></a>모델

간단하게 설명하면, 하나의 모델은 입력을 받아들이고 출력을 생성하는 코드의 한 조각을 말합니다. 기계 학습 모델을 만드는 동안 알고리즘을 선택하고, 데이터를 제공하고, 하이퍼 매개 변수를 조정합니다. 학습은 학습된 모델을 생성하는 반복 프로세스이며, 학습된 모듈에는 모델이 학습 프로세스 중에 습득한 내용이 캡슐화되어 있습니다.

모델은 Azure Machine Learning에서 실행을 통해 생성됩니다. Azure Machine Learning 외부에서 학습된 모델을 사용할 수도 있습니다. Azure Machine Learning Service 작업 영역에 모델을 등록할 수 있습니다.

Azure Machine Learning Service는 프레임워크에 관계없이 사용할 수 있습니다. 모델을 만들 때 모든 인기 있는 기계 학습 프레임 워크 등 Scikit-학습, XGBoost, PyTorch, TensorFlow, Chainer를 사용할 수 있습니다.

모델 학습 예제를 참조 하세요. [자습서: Azure Machine Learning Service를 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 노트북에서 모델 학습을 수행합니다.

합니다 **모델 레지스트리** Azure Machine Learning 서비스 작업 영역에서 모든 모델의 추적 합니다.

모델은 이름 및 버전으로 식별됩니다. 기존 이름과 동일한 이름을 사용하여 모델을 등록할 때마다 레지스트리는 해당 모델을 새 버전으로 간주합니다. 버전 번호가 증가하면 동일 이름으로 새 모델을 등록합니다.

모델을 등록할 때 추가 메타데이터 태그를 제공하면 모델을 검색할 때 해당 태그를 사용할 수 있습니다.

> [!TIP]
> 등록 된 모델은 모델을 구성 하는 하나 이상의 파일에 대 한 논리적 컨테이너입니다. 예를 들어, 여러 파일에 저장 된 모델에 있으면 등록할 수 있습니다를 단일 모델로 Azure Machine Learning 작업 영역에서 합니다. 등록이 완료 되 면 다음 또는 등록 된 모델을 배포할 수 있으며 등록 된 모든 파일을 수신 합니다.

활성 배포에서 사용 되는 등록 된 모델을 삭제할 수 없습니다.

모델을 등록하는 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.

### <a name="run-configurations"></a>실행 구성

실행 구성은 지정된 컴퓨팅 대상에서 스크립트를 실행해야 하는 방법을 정의하는 지침 세트를 보여줍니다. 구성에는 기존 Python 환경을 사용할지 또는 사양에서 빌드되는 Conda 환경을 사용할지 여부와 같은 광범위한 동작 정의 세트가 포함됩니다.

실행 구성은 학습 스크립트를 포함하는 디렉터리 내의 파일에 지속되거나, 메모리 내 개체로 구성되고 실행을 제출하는 데 사용될 수 있습니다.

실행 구성 예제는 [모델 학습을 위한 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md)을 참조하세요.

### <a name="datasets-and-datastores"></a>데이터 집합 및 데이터 저장소

**Azure Machine Learning 데이터 집합** (미리 보기) 액세스를 쉽게 및 데이터로 작업 합니다. 데이터 집합은 모델 학습 등의 다양 한 시나리오에서 데이터 관리 및 파이프라인 만들기. Azure Machine Learning SDK를 사용 하 수 기본 저장소 액세스, 탐색 및 데이터 준비, 다른 데이터 집합 정의의 수명 주기 관리과 비교 하면 프로덕션 환경에서 학습에 사용 되는 데이터 집합 간에 합니다.

사용 하는 등의 인기 있는 형식에서 데이터로 작업 하기 위한 메서드를 제공 하는 데이터 집합 `from_delimited_files()` 또는 `to_pandas_dataframe()`합니다.

자세한 내용은 [만들기 및 Azure Machine Learning 데이터 집합 등록](how-to-create-register-datasets.md)합니다.  데이터 집합을 사용 하 여 더 많은 예제를 참조 하세요. 합니다 [샘플 notebook](https://aka.ms/dataset-tutorial)합니다.

A **데이터 저장소** 는 Azure storage 통해 저장소 추상화 합니다. 데이터 저장소는 Azure Blob 컨테이너 또는 Azure 파일 공유 중 하나를 백 엔드 스토리지로 사용할 수 있습니다. 각 작업 영역에는 기본 데이터 저장소가 있으며 추가 데이터 저장소를 등록할 수 있습니다. Python SDK API 또는 Azure Machine Learning CLI를 사용하여 데이터 저장소의 파일을 저장하고 검색합니다.

### <a name="compute-targets"></a>계산 대상

A [계산 대상](concept-compute-target.md) 실행 하는 교육 스크립트 또는 호스트 서비스 배포에 계산 리소스를 지정할 수 있습니다. 이 위치는 로컬 컴퓨터 또는 클라우드 기반 계산 리소스 수 있습니다. 계산 대상을 쉽게 코드를 변경 하지 않고 계산 환경을 변경 합니다. 

에 대 한 자세한 정보는 [교육 및 배포에 대 한 사용 가능한 계산 대상](concept-compute-target.md)합니다. 

### <a name="training-scripts"></a>교육 스크립트

모델을 학습시키려면 학습 스크립트 및 연결된 파일을 포함하는 디렉터리를 지정합니다. 학습 중에 수집된 정보를 저장하는 데 사용되는 실험 이름도 지정합니다. 학습 중에 전체 디렉터리가 학습 환경(컴퓨팅 대상)에 복사되고 실행 구성을 통해 지정된 스크립트가 시작됩니다. 또한 디렉터리의 스냅샷이 작업 영역의 실험 아래에 저장됩니다.

예제는 [자습서: Azure Machine Learning Service를 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 노트북에서 모델 학습을 수행합니다.

### <a name="runs"></a>실행

실행은 다음 정보를 포함하는 레코드입니다.

* 실행에 대한 메타데이터(타임스탬프, 기간 등)
* 스크립트를 통해 기록된 메트릭
* 실험을 통해 자동 수집되거나 사용자가 명시적으로 업로드한 출력 파일
* 실행 전의 스크립트를 포함하는 디렉터리의 스냅샷

모델을 학습시키기 위해 스크립트를 제출할 때 모델을 생성합니다. 실행에는 0개 이상의 자식 실행이 포함될 수 있습니다. 예를 들어 최상위 실행에는 두 개의 자식 실행이 포함될 수 있고, 각 자식 실행에는 자체 자식 실행이 포함될 수 있습니다.

모델 학습을 통해 생성된 실행 보기 예제는 [빠른 시작: Azure Machine Learning Service 시작](quickstart-run-cloud-notebook.md)을 참조하세요.

### <a name="github-tracking-and-integration"></a>GitHub 추적과 통합

원본 디렉터리가 로컬 Git 리포지토리를 실행 하는 교육을 시작 하면 저장소에 대 한 정보는 실행된 기록에 저장 됩니다. 예를 들어, 현재 커밋 ID 저장소에 대 한 기록의 일부로 기록 됩니다. 이 스 티 메이 터, ML 파이프라인을 실행 하는 스크립트를 사용 하 여 제출 하는 실행을 사용 하 여 작동 합니다. 또한 Machine Learning CLI 또는 SDK에서 전송 하는 실행에 대 한 작동 합니다.

### <a name="snapshots"></a>스냅샷

실행을 제출하면 Azure Machine Learning은 스크립트를 포함하는 디렉터리를 zip 파일로 압축하여 컴퓨팅 대상으로 보냅니다. 그런 다음, zip 파일이 추출되고 스크립트가 실행됩니다. 또한 Azure Machine Learning은 zip 파일을 실행 기록의 일부인 스냅샷으로 저장합니다. 작업 영역에 대한 액세스 권한이 있는 사용자는 실행 기록을 찾아보고 스냅샷을 다운로드할 수 있습니다.

> [!NOTE]
> 불필요 한 파일 스냅숏이 포함 되 고을 방지 하려면 (.gitignore 또는.amlignore)에 무시 파일을 확인 합니다. 스냅숏 디렉터리에이 파일을 배치 하 고에 무시 파일을 추가 합니다. .Amlignore 파일에서는 동일한 [구문과 패턴.gitignore 파일을](https://git-scm.com/docs/gitignore)입니다. 두 파일 모두 없으면.amlignore 파일 우선적으로 적용 합니다.

### <a name="activities"></a>활동

작업은 장기 실행 작업을 나타냅니다. 다음 조작은 작업의 예입니다.

* 계산 대상 만들기 또는 삭제
* 계산 대상에서 스크립트 실행

작업은 SDK 또는 웹 UI를 통해 알림을 제공할 수 있으므로, 사용자가 이러한 조작의 진행 상황을 쉽게 모니터링할 수 있습니다.

### <a name="images"></a>이미지

이미지는 모델을 사용하는 데 필요한 모든 구성 요소와 함께 모델을 안정적으로 배포하는 방법을 제공합니다. 이미지에는 다음 항목이 포함됩니다.

* 모델.
* 점수 매기기 스크립트 또는 애플리케이션. 스크립트를 사용하여 입력을 모델에 전달하고 모델의 출력을 반환합니다.
* 모델, 점수 매기기 스크립트 또는 애플리케이션에 필요한 종속성. 예를 들어 Python 패키지 종속성을 나열하는 Conda 환경 파일을 포함할 수 있습니다.

Azure Machine Learning은 다음과 같은 두 가지 유형의 이미지를 만들 수 있습니다.

* **FPGA 이미지**: Azure의 FPGA(Field-Programmable Gate Array)에 배포할 때 사용됩니다.
* **Docker 이미지**: FPGA 이외의 컴퓨팅 대상에 배포할 때 사용됩니다. 예제로는 Azure Container Instances 및 Azure Kubernetes Service가 있습니다.

Azure Machine Learning 서비스는 기본적으로 사용 되는 기본 이미지를 제공 합니다. 또한 사용자 고유의 사용자 지정 이미지를 제공할 수 있습니다.

### <a name="image-registry"></a>이미지 레지스트리

이미지에서 카탈로그는 합니다 **이미지 레지스트리** 작업 영역에서 합니다. 제공할 수 있습니다 추가 메타 데이터 태그는 이미지를 만들 때 나중에 이미지를 찾을 수를 쿼리할 수 있도록 합니다.

이미지를 만드는 예제는 [Azure Container Instance에 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md)를 참조하세요.

사용자 지정 이미지를 사용 하 여 모델을 배포 하는 예제를 보려면 [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법을](how-to-deploy-custom-docker-image.md)합니다.

### <a name="deployment"></a>배포

배포에는 클라우드에서 호스팅하는 웹 서비스 또는 통합된 장치 배포에 대 한 IoT 모듈에 모델의 인스턴스화입니다.

#### <a name="web-service-deployments"></a>웹 서비스 배포

배포된 웹 서비스는 Azure Container Instances, Azure Kubernetes Service 또는 FPGA를 사용할 수 있습니다. 모델, 스크립트 및 관련된 파일에서 서비스를 만듭니다. 이러한 웹 서비스에 대 한 런타임 환경을 제공 하는 이미지에 캡슐화 됩니다. 이미지에는 웹 서비스에 전송된 점수 매기기 요청을 수신하는 부하 분산된 HTTP 엔드포인트가 있습니다.

Azure에서는 이 기능을 사용하도록 선택한 경우 Application Insight 원격 분석 또는 모델 원격 분석을 수집하여 웹 서비스 배포를 모니터링할 수 있습니다. 원격 분석 데이터는 사용자만 액세스할 수 있으며 Application Insights 및 스토리지 계정 인스턴스에 저장됩니다.

자동 크기 조정을 사용하도록 설정한 경우 Azure에서 배포 크기를 자동으로 조정합니다.

모델을 웹 서비스로 배포하는 예제는 [Azure Container Instance에 이미지 분류 모델 배포](tutorial-deploy-models-with-aml.md)를 참조하세요.

#### <a name="iot-module-deployments"></a>IoT 모듈 배포

배포된 IoT 모듈은 모델 및 연결된 스크립트나 애플리케이션과 모든 추가 종속성을 포함하는 Docker 컨테이너입니다. 에 지 장치의 Azure IoT Edge를 사용 하 여 이러한 모듈을 배포 합니다.

모니터링을 사용하도록 설정한 경우 Azure에서는 Azure IoT Edge 모듈 내의 모델에서 원격 분석 데이터를 수집합니다. 원격 분석 데이터는 사용자만 액세스할 수 있으며 스토리지 계정 인스턴스에 저장됩니다.

Azure IoT Edge는 모듈이 실행 중인지 확인하고 모듈을 호스트 중인 디바이스를 모니터링합니다.

### <a name="ml-pipelines"></a>ML 파이프라인

기계 학습 파이프라인을 사용하여 기계 학습 단계를 연결하는 워크플로를 만들고 관리합니다. 예를 들어, 데이터 준비, 모델 학습, 모델 배포 및 유추/점수 매기기 단계 파이프라인을 포함할 수 있습니다. 각 단계(phase)는 각각 다양한 컴퓨팅 대상에서 자동으로 실행될 수 있는 여러 단계(step)를 포함할 수 있습니다.

이 서비스를 사용한 기계 학습 파이프라인에 대한 자세한 내용은 [파이프라인 및 Azure Machine Learning](concept-ml-pipelines.md)를 참조하세요.

### <a name="logging"></a>로깅

솔루션을 개발할 때 Python 스크립트에서 Azure Machine Learning Python SDK를 사용하여 임의 메트릭을 기록합니다. 실행 후에 메트릭을 쿼리하여 실행에서 배포하려는 모델을 생성했는지 여부를 확인합니다.

### <a name="next-steps"></a>다음 단계

Azure Machine Learning Service를 시작하려면 다음을 참조하세요.

* [Azure Machine Learning Service란?](overview-what-is-azure-ml.md)
* [Azure Machine Learning 서비스 작업 영역 만들기](setup-create-workspace.md)
* [자습서(1부): 모델 학습](tutorial-train-models-with-aml.md)

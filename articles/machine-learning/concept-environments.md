---
title: Azure Machine Learning 환경 정보
titleSuffix: Azure Machine Learning
description: 이 문서에서는 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 이식 가능한 machine learning 종속성 정의를 사용할 수 있는 기계 학습 환경의 이점에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 07/08/2020
ms.openlocfilehash: cc4c39cf26f3ab8d1037222f967789bfbeca05ba
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166776"
---
# <a name="what-are-azure-machine-learning-environments"></a>Azure Machine Learning 환경 이란 무엇 인가요?
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning 환경은 Machine Learning 교육이 발생 하는 환경을 캡슐화 하는 것입니다. 이들은 학습 및 점수 매기기 스크립트와 관련 된 Python 패키지, 환경 변수 및 소프트웨어 설정을 지정 합니다. 실행 시간 (Python, Spark 또는 Docker)도 지정 합니다. 환경은 다양 한 계산 대상에서 재현 가능 하 고 감사 가능 하며 이식 가능한 기계 학습 워크플로를 사용할 수 있도록 하는 Machine Learning 작업 영역 내에서 관리 되는 버전의 엔터티입니다.

`Environment`로컬 계산에서 개체를 사용 하 여 다음을 수행할 수 있습니다.
* 학습 스크립트를 개발합니다.
* 대규모 모델 학습을 위한 Azure Machine Learning 컴퓨팅에서 동일한 환경을 재사용합니다.
* 동일한 환경을 사용 하 여 모델을 배포 합니다.
* 기존 모델이 학습 된 환경을 다시 방문 합니다.

다음 다이어그램에서는 `Environment` 실행 구성 (교육용)과 유추 및 배포 구성 (웹 서비스 배포의 경우)에서 단일 개체를 사용할 수 있는 방법을 보여 줍니다.

![기계 학습 워크플로의 환경 다이어그램](./media/concept-environments/ml-environment.png)

환경, 계산 대상과 학습 스크립트는 모두 실행 구성을 구성 합니다. 교육 실행의 전체 사양입니다.

## <a name="types-of-environments"></a>환경 유형

환경은 *큐 레이트*, *사용자 관리*및 *시스템 관리*의 세 가지 범주로 나눌 수 있습니다.

큐 레이트 환경은 Azure Machine Learning에서 제공 하며 기본적으로 작업 영역에서 사용할 수 있습니다. 있는 그대로 사용 하기 위해 다양 한 기계 학습 프레임 워크를 시작 하는 데 도움이 되는 Python 패키지 및 설정 모음이 포함 되어 있습니다. 이러한 미리 만들어진 환경에서는 배포 시간을 단축할 수 있습니다. 전체 목록은 [큐 레이트 환경 문서](resource-curated-environments.md)를 참조 하세요.

사용자 관리 환경에서는 환경 설정 및 학습 스크립트가 계산 대상에 필요한 모든 패키지를 설치 하는 작업을 담당 합니다. Conda 사용자 환경을 확인 하거나 어떤 것도 설치 하지 않습니다. 사용자 고유의 환경을 정의 하 `azureml-defaults` 는 경우 pip 종속성으로 버전을 나열 해야 합니다 `>= 1.0.45` . 이 패키지에는 웹 서비스로 모델을 호스트 하는 데 필요한 기능이 포함 되어 있습니다.

[Conda](https://conda.io/docs/) 에서 Python 환경 및 스크립트 종속성을 관리 하려는 경우 시스템 관리 환경을 사용 합니다. 새 conda 환경은 conda 종속성 개체를 기반으로 빌드됩니다. Azure Machine Learning 서비스는 수동으로 구성할 수 없는 원격 계산 대상의 유용성으로 인해 기본적으로 이러한 유형의 환경을 가정 합니다.

## <a name="create-and-manage-environments"></a>환경 만들기 및 관리

다음을 수행 하 여 환경을 만들 수 있습니다.

* `Environment`큐 레이트 환경을 사용 하거나 고유한 종속성을 정의 하 여 새 개체를 정의 합니다.
* `Environment`작업 영역에서 기존 개체를 사용 합니다. 이 접근 방식을 사용 하면 일관성과 종속성을 재현 가능성 수 있습니다.
* 기존 Anaconda 환경 정의에서 가져오기.
* Azure Machine Learning CLI 사용
* [VS Code 확장 사용](how-to-manage-resources-vscode.md#create-environment)

특정 코드 샘플은 [환경 사용 방법](how-to-use-environments.md#create-an-environment)의 "환경 만들기" 섹션을 참조 하세요. 환경도 작업 영역을 통해 쉽게 관리할 수 있습니다. 여기에는 다음과 같은 기능이 포함 됩니다.

* 환경은 실험을 제출할 때 작업 영역에 자동으로 등록 됩니다. 수동으로 등록할 수도 있습니다.
* 작업 영역에서 교육 또는 배포에 사용할 환경을 인출 하거나 환경 정의를 편집할 수 있습니다.
* 버전 관리를 사용 하면 시간에 따라 환경에 대 한 변경 내용을 볼 수 있습니다. 그러면 재현 가능성이 보장 됩니다.
* 사용자 환경에서 자동으로 Docker 이미지를 빌드할 수 있습니다.

코드 샘플은 [환경을 사용 하는 방법](how-to-use-environments.md#manage-environments)의 "환경 관리" 섹션을 참조 하세요.

## <a name="environment-building-caching-and-reuse"></a>환경 빌드, 캐싱 및 다시 사용

Azure Machine Learning 서비스는 환경 정의를 Docker 이미지 및 conda 환경으로 작성 합니다. 또한 이후 학습 실행 및 서비스 끝점 배포에서 다시 사용할 수 있도록 환경을 캐시 합니다. 학습 스크립트를 원격으로 실행 하려면 Docker 이미지를 만들어야 합니다. 반면 로컬 실행은 Conda 환경을 직접 사용할 수 있습니다. 

### <a name="submitting-a-run-using-an-environment"></a>환경을 사용 하 여 실행 제출

환경을 사용 하 여 원격 실행을 처음으로 제출 하는 경우 Azure Machine Learning 서비스는 작업 영역과 연결 된 ACR (Azure Container Registry)에서 [Acr Build 작업](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview) 을 호출 합니다. 그러면 빌드된 Docker 이미지가 작업 영역 ACR에 캐시 됩니다. 큐 레이트 환경은 글로벌 ACR에 캐시 된 Docker 이미지에 의해 지원 됩니다. 실행을 시작할 때 이미지는 관련 ACR에서 계산 대상에 의해 검색 됩니다.

로컬 실행의 경우 환경 정의에 따라 Docker 또는 Conda 환경이 만들어집니다. 그러면 대상 compute (로컬 런타임 환경 또는 로컬 Docker 엔진)에서 스크립트가 실행 됩니다.

### <a name="building-environments-as-docker-images"></a>Docker 이미지로 환경 구축

환경 정의가 작업 영역 ACR에 아직 없는 경우 새 이미지가 작성 됩니다. 이미지 빌드는 두 단계로 구성 됩니다.

 1. 기본 이미지 다운로드 및 Docker 단계 실행
 2. 환경 정의에 지정 된 conda 종속성에 따라 conda 환경을 구축 합니다.

[사용자 관리 종속성](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py)을 지정 하는 경우 두 번째 단계가 생략 됩니다. 이 경우 기본 이미지에 포함 하거나 첫 번째 단계 내에서 사용자 지정 Docker 단계를 지정 하 여 Python 패키지를 설치 해야 합니다. 또한 Python 실행 파일에 대 한 올바른 위치를 지정 해야 합니다. 또한 [사용자 지정 Docker 기본 이미지](how-to-deploy-custom-docker-image.md)를 사용할 수 있습니다.

### <a name="image-caching-and-reuse"></a>이미지 캐싱 및 다시 사용

다른 실행에 동일한 환경 정의를 사용 하는 경우 Azure Machine Learning 서비스는 작업 영역 ACR에서 캐시 된 이미지를 재사용 합니다. 

캐시 된 이미지의 세부 정보를 보려면 [get_image_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#get-image-details-workspace-) 메서드를 사용 합니다.

캐시 된 이미지를 다시 사용 하거나 새 이미지를 빌드 할지 여부를 결정 하기 위해 서비스는 환경 정의에서 [해시 값을](https://en.wikipedia.org/wiki/Hash_table) 계산 하 고이를 기존 환경의 해시와 비교 합니다. 해시는 다음을 기반으로 합니다.
 
 * 기본 이미지 속성 값
 * 사용자 지정 docker 단계 속성 값
 * Conda 정의에 있는 Python 패키지 목록
 * Spark 정의의 패키지 목록 

해시는 환경 이름 또는 버전에 종속 되지 않습니다. 환경 이름을 바꾸거나 기존 환경에 대 한 정확한 속성 및 패키지를 사용 하 여 새 환경을 만든 경우 해시 값은 동일 하 게 유지 됩니다. 그러나 Python 패키지를 추가 또는 제거 하거나 패키지 버전을 변경 하는 등 환경 정의를 변경 하면 해시 값이 변경 됩니다. 환경에서 종속성 또는 채널의 순서를 변경 하면 새 환경이 생성 되므로 새 이미지 빌드가 필요 합니다. 큐 레이트 환경을 변경 하면 해시가 무효화 되 고 새 "비 큐 레이트" 환경에서 결과가 발생 한다는 점에 유의 해야 합니다.

계산 된 해시 값은 작업 영역 및 전역 ACR (또는 로컬 실행의 경우 계산 대상)의 해시 값과 비교 됩니다. 일치 하는 항목이 있으면 캐시 된 이미지를 가져오고, 그렇지 않으면 이미지 빌드가 트리거됩니다. 캐시 된 이미지를 가져오는 기간에는 다운로드 시간이 포함 되지만 새로 빌드된 이미지를 가져오는 기간에는 빌드 시간과 다운로드 시간이 모두 포함 됩니다. 

다음 다이어그램에서는 세 가지 환경 정의를 보여 줍니다. 두 파일의 이름 및 버전은 다르지만 기본 이미지 및 Python 패키지는 동일 합니다. 하지만 동일한 해시가 있으므로 동일한 캐시 된 이미지에 해당 합니다. 세 번째 환경에는 다른 Python 패키지와 버전이 있으므로 다른 캐시 된 이미지에 해당 합니다.

![Docker 이미지인 환경 캐싱 다이어그램](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> 예를 들어 고정 해제 된 패키지 종속성을 사용 하 여 환경을 만드는 경우 환경을 만들 때 ```numpy``` 설치 된 패키지 버전을 _at the time of environment creation_계속 사용 하 게 됩니다. 또한 일치 하는 정의가 있는 이후의 모든 환경은 이전 버전을 계속 사용 합니다. 

패키지를 업데이트 하려면 이미지를 다시 작성 하는 예를 들어 버전 번호를 지정 ```numpy==1.18.1``` 합니다. 중첩 된 항목을 포함 한 새 종속성이 설치 되어 이전에 작업 중인 시나리오가 중단 될 수 있습니다. 

> [!WARNING]
>  재현 가능성 [메서드는](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#build-workspace--image-build-compute-none-) 캐시 된 이미지를 다시 작성 합니다 .이는 고정 되지 않은 패키지를 업데이트 하 고 해당 캐시 된 이미지에 해당 하는 모든 환경 정의에 대해를 중단 시킬 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Machine Learning에서 [환경을 만들고 사용](how-to-use-environments.md) 하는 방법에 대해 알아봅니다.
* [환경 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)에 대 한 Python SDK 참조 설명서를 참조 하세요.
* [환경](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments)에 대 한 R SDK 참조 설명서를 참조 하세요.

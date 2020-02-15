---
title: Azure Machine Learning 계산 인스턴스인 이란?
titleSuffix: Azure Machine Learning
description: 완전히 관리 되는 클라우드 기반 워크스테이션 Azure Machine Learning 계산 인스턴스에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 280851b2fea0b8100a7d0f8ec8105109a41c8c83
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77252079"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Azure Machine Learning 계산 인스턴스인 이란?

Azure Machine Learning 계산 인스턴스 (미리 보기)는 데이터 과학자를 위한 완전히 관리 되는 클라우드 기반 워크스테이션입니다. 

계산 인스턴스를 사용 하면 Azure Machine Learning 개발을 쉽게 시작할 수 있을 뿐만 아니라 IT 관리자에 게 관리 및 엔터프라이즈 준비 기능을 제공할 수 있습니다.  

클라우드에서 완전히 구성 되 고 관리 되는 개발 환경으로 계산 인스턴스를 사용 합니다.

계산 인스턴스는 일반적으로 개발 환경으로 사용 됩니다.  개발 및 테스트를 위한 학습 및 추론 계산 대상으로 사용할 수도 있습니다.  대량 작업의 경우 다중 노드 크기 조정 기능을 사용 하는 [Azure Machine Learning 계산 클러스터가](how-to-set-up-training-targets.md#amlcompute) 더 나은 계산 대상 선택입니다.


## <a name="why-use-a-compute-instance"></a>계산 인스턴스를 사용 하는 이유

계산 인스턴스는 machine learning 개발 환경에 최적화 된 완벽 하 게 관리 되는 클라우드 기반 워크스테이션입니다. 다음과 같은 이점이 있습니다.

|주요 이점||
|----|----|
|생산성|데이터 과학자는 통합 된 노트북 및 웹 브라우저에서 다음 도구를 사용 하 여 모델을 빌드 및 배포할 수 있습니다.<br/>-Jupyter<br/>-JupyterLab<br/>-RStudio|
|관리 되는 & 보안|보안 공간을 줄이고 엔터프라이즈 보안 요구 사항에 따라 규정 준수를 추가 합니다. 계산 인스턴스는 다음과 같은 강력한 관리 정책과 보안 네트워킹 구성을 제공 합니다.<br/><br/>-리소스 관리자 템플릿 또는 Azure Machine Learning SDK에서 자동 프로 비전<br/>- [역할 기반 액세스 제어 (RBAC)](/azure/role-based-access-control/overview)<br/>[가상 네트워크 지원](how-to-enable-virtual-network.md#compute-instance) - <br/>-Ssh 액세스를 사용 하거나 사용 하지 않도록 설정 하는 SSH 정책|
|&nbsp;또는&nbsp;ML 구성|미리 구성 된 최신 ML 패키지, 심층 학습 프레임 워크, GPU 드라이버를 사용 하 여 설치 작업에 대 한 시간을 절약 하세요.|
|완전히 사용자 지정 가능|Gpu를 비롯 한 Azure VM 형식에 대 한 광범위 한 지원과 패키지 및 드라이버 설치와 같은 지속적인 낮은 수준의 사용자 지정을 통해 고급 시나리오를 간편 하 게 만들 수 있습니다. |

## <a name="contents"></a>도구 및 환경

Azure Machine Learning compute 인스턴스를 사용 하면 작업 영역에서 완전히 통합 된 노트북 환경에서 모델을 작성, 학습 및 배포할 수 있습니다.


이러한 도구 및 환경은 계산 인스턴스에 설치 됩니다. 

|환경 & 일반 도구|세부 정보|
|----|:----:|
|드라이버|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI 라이브러리||
|Azure CLI ||
|Azure Machine Learning 샘플 ||
|Azure Machine Learning EDAT 엔진 ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R** 도구 & 환경|세부 정보|
|----|:----:|
|RStudio Server 오픈 소스 버전||
|R 커널||
|R에 대 한 Azure Machine Learning SDK|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK 샘플|

|**PYTHON** 도구 & 환경|세부 정보|
|----|----|
|Anaconda Python||
|Jupyter 및 확장||
|Jupyterlab 및 확장||
|Visual Studio Code ||
[Python용 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>PyPI에서|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|기타 PyPI 패키지|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda 패키지|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|심층 학습 패키지|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX 패키지|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Python & R SDK 샘플 Azure Machine Learning||

Python 패키지는 모두 **python 3.6-AzureML** 환경에 설치 됩니다.  

계산 인스턴스는 일반적으로 개발 환경으로 사용 됩니다.  개발 및 테스트를 위한 학습 및 추론 계산 대상으로 사용할 수도 있습니다.  대량 작업의 경우 다중 노드 크기 조정 기능을 사용 하는 [Azure Machine Learning 계산 클러스터가](how-to-set-up-training-targets.md#amlcompute) 더 나은 계산 대상 선택입니다.

### <a name="installing-packages"></a>패키지 설치

Jupyter 노트북 또는 Rstudio에 직접 패키지를 설치할 수 있습니다.

* RStudio 오른쪽 아래에 있는 **패키지** 탭 또는 왼쪽 위에 있는 **콘솔** 탭을 사용 합니다.  
* Python: 설치 코드를 추가 하 고 Jupyter 노트북 셀에를 실행 합니다.

또는 다음과 같은 방법으로 터미널 창에 액세스할 수 있습니다.

* RStudio: 왼쪽 위에서 **터미널** 탭을 선택 합니다.
* Jupyter 랩: 시작 관리자 탭의 **다른** 제목 아래에서 **터미널** 타일을 선택 합니다.
* Jupyter: 파일 탭의 오른쪽 위에 있는 **새 > 터미널** 을 선택 합니다.
* 컴퓨터에 SSH를 합니다.  Python 패키지를 **python 3.6-AzureML** 환경에 설치 합니다.  R 패키지를 **r** 환경에 설치 합니다.

## <a name="accessing-files"></a>파일에 액세스

노트북 및 R 스크립트는 Azure 파일 공유에서 작업 영역의 기본 저장소 계정에 저장 됩니다.  이러한 파일은 "사용자 파일" 디렉터리 아래에 있습니다. 이 저장소를 사용 하면 계산 인스턴스 간에 노트북을 쉽게 공유할 수 있습니다. 저장소 계정은 계산 인스턴스를 중지 하거나 삭제 하는 경우에도 노트북을 안전 하 게 유지 합니다.

작업 영역의 Azure 파일 공유 계정은 계산 인스턴스에 드라이브로 탑재 됩니다. 이 드라이브는 Jupyter, Jupyter Labs 및 RStudio의 기본 작업 디렉터리입니다.

파일 공유의 파일은 동일한 작업 영역의 모든 계산 인스턴스에서 액세스할 수 있습니다. 계산 인스턴스에서 이러한 파일에 대 한 모든 변경 내용은 파일 공유로 안정적으로 다시 저장 됩니다.

또한 작업 영역 파일 공유의 사용자 파일 디렉터리 아래에 있는 폴더에 최신 Azure Machine Learning 샘플을 복제할 수 있습니다.

작은 파일 작성은 VM 자체에 쓰는 것 보다 네트워크 드라이브에서 속도가 느릴 수 있습니다.  적은 수의 작은 파일을 작성 하는 경우에는 `/tmp` 디렉터리와 같이 계산 인스턴스에서 직접 디렉터리를 사용 하십시오. 이러한 파일은 작업 영역의 다른 계산 인스턴스에서는 액세스할 수 없습니다.

## <a name="managing-a-compute-instance"></a>계산 인스턴스 관리

Azure Machine Learning studio의 작업 영역에서 **compute**를 선택한 다음 위쪽에서 **계산 인스턴스** 를 선택 합니다.

![계산 인스턴스 관리](./media/concept-compute-instance/manage-compute-instance.png)

다음 동작을 수행할 수 있습니다.

* 계산 인스턴스를 만듭니다. 이름을 지정 하 고, Gpu를 포함 한 Azure VM 유형 (만든 후 VM 유형을 변경할 수 없음)을 지정 하 고, SSH 액세스를 사용/사용 하지 않도록 설정 하 고, 가상 네트워크 설정을 구성 합니다 (선택 사항). 통합 된 노트북, Azure Portal, 리소스 관리자 템플릿 또는 Azure Machine Learning SDK에서 직접 인스턴스를 만들 수도 있습니다. 계산 인스턴스 생성에 적용 되는 지역 당 전용 코어 할당량은 통합 되어 Azure Machine Learning 계산 클러스터 할당량과 공유 됩니다.
* 계산 인스턴스 탭 새로 고침
* 계산 인스턴스 시작, 중지 및 다시 시작
* 계산 인스턴스 삭제

작업 영역의 각 계산 인스턴스에 대해 다음을 수행할 수 있습니다.

* 계산 인스턴스에서 Jupyter, JupyterLab, RStudio에 액세스 합니다.
* SSH를 계산 인스턴스로 변환 합니다. SSH 액세스는 기본적으로 사용 하지 않도록 설정 되어 있지만 계산 인스턴스 생성 시 사용 하도록 설정할 수 있습니다. SSH 액세스에는 공개/개인 키 메커니즘이 사용 됩니다. 이 탭에는 IP 주소, 사용자 이름 및 포트 번호와 같은 SSH 연결에 대 한 세부 정보가 제공 됩니다.
* 특정 계산 인스턴스에 대 한 세부 정보 (예: IP 주소 및 지역)를 가져옵니다.

[RBAC](/azure/role-based-access-control/overview) 를 사용 하면 작업 영역에서 계산 인스턴스를 만들고 삭제, 시작, 중지 및 다시 시작할 수 있는 사용자를 제어할 수 있습니다. 작업 영역 참가자 및 소유자 역할의 모든 사용자는 작업 영역에서 계산 인스턴스를 만들고, 삭제 하 고, 시작 하 고, 중지 하 고, 다시 시작할 수 있습니다. 그러나 특정 계산 인스턴스의 작성자만이 계산 인스턴스의 Jupyter, JupyterLab 및 RStudio에 액세스할 수 있습니다. 계산 인스턴스의 작성자에 게는 전용 계산 인스턴스가 있고, 루트 액세스 권한이 있으며, Jupyter를 통해에서 터미널을 사용할 수 있습니다. 계산 인스턴스는 creator 사용자의 단일 사용자 로그인을 갖게 되 고 모든 작업은 사용자의 id를 사용 하 여 RBAC 및 실험 실행의 특성을 사용 합니다. SSH 액세스는 공개/개인 키 메커니즘을 통해 제어 됩니다.

인스턴스를 만들 수도 있습니다.
* 통합 된 노트북 환경에서 직접
* Azure Portal에서
* Azure Resource Manager 템플릿에서
* Azure Machine Learning SDK 사용

계산 인스턴스 생성에 적용 되는 지역 당 전용 코어 할당량은 통합 되어 Azure Machine Learning 교육 클러스터 할당량과 공유 됩니다. 

## <a name="compute-target"></a>계산 대상

계산 인스턴스는 Azure Machine Learning 계산 학습 클러스터와 유사한 [학습 계산 대상](concept-compute-target.md#train) 으로 사용할 수 있습니다. TensorFlow/PyTorch 추정를 사용 하 여 분산 학습 작업을 실행할 다중 GPU VM을 프로 비전 합니다. 실행 구성을 만들고이를 사용 하 여 계산 인스턴스에서 실험을 실행할 수도 있습니다. 계산 인스턴스는 테스트/디버깅 시나리오에 대 한 로컬 추론 배포 대상으로 사용할 수 있습니다.

## <a name="notebookvm"></a>노트북 VM의 변경 내용

계산 인스턴스가 노트북 VM을 교체 하 고 있습니다.  

작업 영역 파일 공유에 저장 된 모든 노트북 파일과 작업 영역 데이터 저장소의 데이터는 계산 인스턴스에서 액세스할 수 있습니다. 그러나 노트북 VM에 이전에 설치 된 모든 사용자 지정 패키지는 계산 인스턴스에 다시 설치 해야 합니다. 계산 클러스터 만들기에 적용 되는 할당량 제한은 계산 인스턴스 생성에도 적용 됩니다. 

새 노트북 Vm을 만들 수 없습니다. 그러나 전체 기능을 사용 하 여 만든 노트북 Vm에 계속 액세스 하 여 사용할 수 있습니다. 계산 인스턴스는 기존 노트북 Vm과 동일한 작업 영역에 만들 수 있습니다. 


## <a name="next-steps"></a>다음 단계

 * [자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md) 은 통합 된 노트북으로 계산 인스턴스를 사용 하는 방법을 보여 줍니다.

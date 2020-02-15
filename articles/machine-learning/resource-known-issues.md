---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대 한 알려진 문제, 해결 방법 및 문제 해결 목록을 가져옵니다.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 40749a80d99782a1ea84b27e68376ea2870e8eb7
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137999"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>알려진 문제 및 문제 해결 Azure Machine Learning

이 문서는 Azure Machine Learning를 사용할 때 발생 하는 오류 또는 오류를 찾고 수정 하는 데 도움이 됩니다.

## <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>중단: AmlCompute에서 NCv3 컴퓨터로 SR-IOV 업그레이드

Azure Compute는 모든 MPI 구현 및 버전을 지원 하 고 InfiniBand 장착 가상 머신에 대 한 RDMA 동사를 지원 하기 위해 11 월 2019 초에 시작 하는 NCv3 Sku를 업데이트 합니다. 이 작업을 수행 하려면 짧은 가동 중지 시간이 필요 합니다. [sr-iov 업그레이드에 대해 자세히](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku)알아보세요.

Azure Machine Learning의 관리 되는 계산 제공 (AmlCompute)의 고객으로 서 지금은 변경할 필요가 없습니다. [업데이트 일정](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) 에 따라 교육의 짧은 중단을 계획 해야 합니다. 서비스는 클러스터 노드의 VM 이미지를 업데이트 하 고 클러스터를 자동으로 확장 하는 작업을 담당 합니다. 업그레이드가 완료 되 면 더 높은 InfiniBand 대역폭, 짧은 대기 시간 및 향상 된 배포 응용 프로그램 성능을 얻을 수 있을 뿐만 아니라 다른 MPI 배포 (예: Pytorch)를 모두 사용할 수 있습니다.

## <a name="azure-machine-learning-designer-issues"></a>Azure Machine Learning 디자이너 문제

디자이너의 알려진 문제입니다.

### <a name="long-compute-preparation-time"></a>긴 계산 준비 시간

계산 후에 새 계산 또는 하기 호출할 생성 시간이 소요 되 면 몇 분 정도 걸릴 수 있습니다. 팀이 최적화를 위해 작업 중입니다.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>데이터 집합만 포함 된 실험을 실행할 수 없습니다. 

데이터 집합을 시각화 하는 데이터 집합만 포함 하는 실험을 실행할 수 있습니다. 그러나 실험을 실행할 수는 없습니다. 현재 데이터 집합만 포함 되어 있습니다. 이 문제를 적극적으로 해결 하 고 있습니다.
 
이 문제를 해결 하기 전에 데이터 집합을 데이터 변환 모듈 (데이터 집합의 열 선택, 메타 데이터 편집, 데이터 분할 등)에 연결 하 고 실험을 실행할 수 있습니다. 그런 다음 데이터 집합을 시각화할 수 있습니다. 

다음 이미지는 ![visulize](./media/resource-known-issues/aml-visualize-data.png)를 보여 줍니다.

## <a name="sdk-installation-issues"></a>SDK 설치 문제

**오류 메시지: ‘PyYAML’을 제거할 수 없습니다.**

Python 용 Azure Machine Learning SDK: PyYAML는 distutils 설치 된 프로젝트입니다. 따라서 부분 제거가 있는 경우 속해 있는 파일을 정확히 확인할 수 없습니다. 이 오류를 무시하면서 SDK를 게속 설치하려면 다음을 사용합니다.

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**오류 메시지: `ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda의 Python 3.7.4 배포에는 azureml sdk 설치를 중단 하는 버그가 있습니다. 이 문제는이 [GitHub 문제](https://github.com/ContinuumIO/anaconda-issues/issues/11195) 에 설명 되어 있습니다 .이 작업은 다음 명령을 사용 하 여 새 Conda 환경을 만들어 해결할 수 있습니다.
```bash
conda create -n <env-name> python=3.7.3
```
이를 통해 3.7.4에 설치 문제가 없는 Python 3.7.3를 사용 하 여 Conda 환경을 만듭니다.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Azure Machine Learning 컴퓨팅을 만드는 문제

GA 릴리스 전에 Azure Portal에서 Azure Machine Learning 작업 영역을 만든 일부 사용자가 해당 작업 영역에서 Azure Machine Learning 컴퓨팅을 만들 수 없는 경우는 거의 없습니다. 서비스에 대해 지원 요청을 제기하거나 포털 또는 SDK를 통해 새 작업 영역을 만들어 즉시 차단을 직접 해제할 수 있습니다.

## <a name="image-building-failure"></a>이미지 빌드 실패

웹 서비스를 배포할 때 이미지 빌드가 실패했습니다. 해결 방법은 이미지 구성을 위한 Conda 파일에 "pynacl==1.2.1"을 pip 종속성으로 추가하는 것입니다.

## <a name="deployment-failure"></a>배포 실패

`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`가 나타나면 배포에 사용한 VM의 SKU를 메모리가 더 많은 SKU로 변경하세요.

## <a name="fpgas"></a>FPGA

요청을 하고 FPGA 할당량의 승인을 받을 때까지 FPGA에 모델을 배포할 수 없습니다. 액세스를 요청하려면 할당량 요청 양식 https://aka.ms/aml-real-time-ai를 작성합니다.

## <a name="automated-machine-learning"></a>자동화된 기계 학습

텐서 Flow 자동화 된 machine learning은 현재 텐서 flow 버전 1.13을 지원 하지 않습니다. 이 버전을 설치 하면 패키지 종속성이 작동 하지 않습니다. 이후 릴리스에서이 문제를 해결 하기 위해 노력 하 고 있습니다. 

### <a name="experiment-charts"></a>실험 차트

자동화 된 ML 실험 반복에 표시 되는 이진 분류 차트 (정밀도-리콜, ROC, 곡선 등)는 4/12부터 사용자 인터페이스에서 올바르게 렌더링 되지 않습니다. 차트 플롯에는 현재 더 낮은 결과가 포함 된 모델을 더 잘 수행 하는 역 결과가 표시 됩니다. 확인 중입니다.

## <a name="datasets-and-data-preparation"></a>데이터 집합 및 데이터 준비

Azure Machine Learning 데이터 집합에 대 한 알려진 문제입니다.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: 해당 파일 또는 디렉터리가 없습니다.

이 오류는 사용자가 제공 하는 파일 경로가 파일이 있는 위치가 아닌 경우에 발생 합니다. 계산 대상에서 데이터 집합을 탑재 한 위치와 파일을 참조 하는 방법이 일치 하는지 확인 해야 합니다. 결정적 상태를 보장 하려면 계산 대상에 데이터 집합을 탑재할 때 추상 경로를 사용 하는 것이 좋습니다. 예를 들어 다음 코드에서는 계산 대상의 파일 시스템 루트 아래에 데이터 집합을 탑재 `/tmp`합니다. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

선행 슬래시 ('/')를 포함 하지 않는 경우 작업 디렉터리에 접두사를 지정 해야 합니다. 예를 들어 계산 대상에 `/mnt/batch/.../tmp/dataset` 하 여 데이터 집합을 탑재할 위치를 지정 해야 합니다. 

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>HTTP 또는 ADLS Gen 2에서 Parquet 파일을 읽지 못했습니다.

AzureML DataPrep SDK 버전 1.1.25에는 HTTP 또는 ADLS Gen 2에서 Parquet 파일을 읽어 데이터 집합을 만들 때 오류가 발생 하는 알려진 문제가 있습니다. `Cannot seek once reading started.`는 실패 합니다. 이 문제를 해결 하려면 `azureml-dataprep`를 1.1.26 보다 높은 버전으로 업그레이드 하거나 1.1.24 보다 낮은 버전으로 다운 그레이드 하세요.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount ()에서 예기치 않은 키워드 인수 ' invocation_id '을 (를) 가져왔습니다.

`azureml-core`와 `azureml-dataprep`간에 호환 되지 않는 버전이 있는 경우이 오류가 발생 합니다. 이 오류가 표시 되는 경우 `azureml-dataprep` 패키지를 최신 버전으로 업그레이드 합니다 (1.1.29 보다 크거나 같음).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks 및 Azure Machine Learning 문제.

### <a name="failure-when-installing-packages"></a>패키지 설치 시 실패

추가 패키지가 설치 되 면 Azure Databricks에서 Azure Machine Learning SDK 설치가 실패 합니다. `psutil` 같은 일부 패키지가 충돌을 일으킬 수 있습니다. 설치 오류를 방지 하려면 라이브러리 버전을 고정 하 여 패키지를 설치 합니다. 이 문제는 Azure Machine Learning SDK가 아닌 Databricks와 관련이 있습니다. 다른 라이브러리 에서도이 문제가 발생할 수 있습니다. 예:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

또는 Python 라이브러리와의 연결을 유지 하는 경우 init 스크립트를 사용할 수 있습니다. 이 방법은 공식적으로 지원 되지 않습니다. 자세한 내용은 [클러스터 범위 init 스크립트](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)를 참조 하세요.

### <a name="cancel-an-automated-machine-learning-run"></a>자동화 된 machine learning 실행 취소

Azure Databricks에서 자동화 된 기계 학습 기능을 사용 하는 경우 실행을 취소 하 고 새 실험 실행을 시작 하려면 Azure Databricks 클러스터를 다시 시작 합니다.

### <a name="10-iterations-for-automated-machine-learning"></a>자동화 된 machine learning에 대 한 10 회 반복 >

자동화 된 기계 학습 설정에서 10 개 이상의 반복이 있는 경우 실행을 제출할 때 `show_output`을 `False`로 설정 합니다.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Azure Machine Learning SDK 및 자동화 된 기계 학습을 위한 위젯

Azure Machine Learning SDK 위젯은 Databricks 노트북에서 HTML 위젯을 구문 분석할 수 없기 때문에 지원 되지 않습니다. Azure Databricks 노트북 셀에서이 Python 코드를 사용 하 여 포털에서 위젯을 볼 수 있습니다.

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>가져오기 오류: 이름이 ' pandas ' 인 모듈이 없습니다.

자동화 된 machine learning을 사용 하는 경우이 오류가 표시 되는 경우:

1. Azure Databricks 클러스터에 두 개의 패키지를 설치 하려면이 명령을 실행 합니다. 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. 클러스터를 분리 했다가 노트북에 다시 연결 합니다. 

이러한 단계를 수행 해도 문제가 해결 되지 않으면 클러스터를 다시 시작 하십시오.

### <a name="failtosendfeather"></a>FailToSendFeather

Azure Databricks 클러스터에서 데이터를 읽을 때 `FailToSendFeather` 오류가 표시 되는 경우 다음 해결 방법을 참조 하세요.

* `azureml-sdk[automl]` 패키지를 최신 버전으로 업그레이드 합니다.
* `azureml-dataprep` 버전 1.1.8 이상을 추가 합니다.
* `pyarrow` 버전 0.11 이상을 추가 합니다.

## <a name="azure-portal"></a>Azure 포털

SDK 또는 포털의 공유 링크에서 작업 영역을 직접 확인하려는 경우 확장에서 구독 정보가 포함된 일반 개요 페이지를 확인할 수 없습니다. 다른 작업 영역으로 전환할 수 없습니다. 다른 작업 영역을 확인 해야 하는 경우 해결 방법은 [Azure Machine Learning studio](https://ml.azure.com) 로 직접 이동 하 여 작업 영역 이름을 검색 하는 것입니다.

## <a name="diagnostic-logs"></a>진단 로그

도움말을 요청할 때 진단 정보를 제공할 수 있는 경우에 유용할 수 있습니다. 일부 로그를 보려면 [Azure Machine Learning studio](https://ml.azure.com) 를 방문 하 고 작업 영역으로 이동한 다음 **작업 영역 > 실험을 선택 하 > > 로그를 실행**합니다.  

> [!NOTE]
> 자동 Ml 또는 교육 작업을 실행 하는 Docker 컨테이너와 같은 학습 중 다양 한 원본에서 정보를 기록 Azure Machine Learning 합니다. 이러한 로그는 대부분 문서화 되어 있지 않습니다. 문제가 발생 하 고 Microsoft 지원에 문의 하는 경우 문제 해결 중에 이러한 로그를 사용할 수 있습니다.

## <a name="activity-logs"></a>활동 로그

Azure Machine Learning 작업 영역 내의 일부 작업은 __작업 로그__에 정보를 기록 하지 않습니다. 예를 들어 학습을 시작 하거나 모델을 등록 합니다.

이러한 작업 중 일부는 작업 영역의 __작업 영역에__ 표시 되지만 작업을 시작한 사용자는 표시 되지 않습니다.

## <a name="resource-quotas"></a>리소스 할당량

Azure Machine Learning을 사용할 때 발생할 수 있는 [리소스 할당량](how-to-manage-quotas.md)에 대해 알아보세요.

## <a name="authentication-errors"></a>인증 오류

원격 작업에서 컴퓨팅 대상에 대한 관리 작업을 수행하는 경우 다음 오류 중 하나가 나타납니다.

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

예를 들어 원격 실행을 위해 제출된 ML 파이프라인에서 컴퓨팅 대상을 만들거나 연결하려고 시도하면 오류가 나타납니다.

## <a name="overloaded-azurefile-storage"></a>오버 로드 된 AzureFile 저장소

`Unable to upload project files to working directory in AzureFile because the storage is overloaded`오류가 표시 되 면 다음 해결 방법을 적용 합니다.

데이터 전송과 같은 다른 작업에 대해 파일 공유를 사용 하는 경우 파일 공유를 사용 하 여 실행을 제출할 수 있도록 blob을 사용 하는 것이 좋습니다. 작업을 서로 다른 두 작업 영역 간에 분할할 수도 있습니다.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Azure Kubernetes 서비스 실패의 Webservices 

`kubectl`를 사용 하 여 클러스터에 연결 하 여 Azure Kubernetes Service에서 많은 webservice 오류를 디버그할 수 있습니다. 을 실행 하 여 Azure Kubernetes 서비스 클러스터에 대 한 `kubeconfig.json`를 가져올 수 있습니다.

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>AKS 클러스터의 Azure Machine Learning 구성 요소 업데이트

Azure Kubernetes Service 클러스터에 설치 된 Azure Machine Learning 구성 요소에 대 한 업데이트를 수동으로 적용 해야 합니다. 

Azure Machine Learning 작업 영역에서 클러스터를 분리 하 여 이러한 업데이트를 적용 한 다음 클러스터를 작업 영역에 다시 연결할 수 있습니다. 클러스터에서 SSL을 사용 하도록 설정한 경우 클러스터를 다시 연결할 때 SSL 인증서와 개인 키를 제공 해야 합니다. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

SSL 인증서와 개인 키가 더 이상 없거나 Azure Machine Learning에 의해 생성 된 인증서를 사용 하는 경우 `kubectl`를 사용 하 여 클러스터에 연결 하 고 비밀 `azuremlfessl`을 검색 하 여 클러스터를 분리 하기 전에 파일을 검색할 수 있습니다.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes는 암호를 base-64로 인코딩된 형식으로 저장 합니다. `attach_config.enable_ssl`에 제공 하기 전에 `cert.pem` 및 암호의 `key.pem` 구성 요소를 먼저 디코딩하는 것이 64 필요 합니다. 

## <a name="recommendations-for-error-fix"></a>오류 수정에 대 한 권장 사항
일반적인 관찰을 기준으로 azure ML에서 일반적인 오류 중 일부를 해결 하기 위한 Azure ML 권장 사항은 다음과 같습니다.

### <a name="metric-document-is-too-large"></a>메트릭 문서가 너무 큼
Azure Machine Learning에는 학습 실행에서 한 번에 기록할 수 있는 메트릭 개체 크기에 대 한 내부 제한이 있습니다. 목록 값 메트릭을 로깅할 때 "메트릭 문서가 너무 커서" 오류가 발생 하는 경우 목록을 더 작은 청크로 분할 해 보세요. 예를 들면 다음과 같습니다.

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

 내부적으로 실행 기록 서비스는 동일한 메트릭 이름을 가진 블록을 연속 된 목록에 연결 합니다.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (이름이 지정 된 모듈 없음)
Azure ML에서 실험을 제출 하는 동안 ModuleErrors를 실행 하는 경우 학습 스크립트는 패키지를 설치 하는 것으로 예상 하지만 추가 되지 않았음을 의미 합니다. 패키지 이름을 제공 하면 Azure ML은 교육에 사용 되는 환경에 패키지를 설치 합니다. 

[추정](concept-azure-machine-learning-architecture.md#estimators) 를 사용 하 여 실험을 제출 하는 경우 패키지를 설치 하려는 원본에서 기반으로 평가기의 `conda_packages` 매개 변수를 사용 하 여 패키지 `pip_packages` 이름을 지정할 수 있습니다. `conda_dependencies_file`를 사용 하 여 모든 종속성이 포함 된 iisnode.yml 파일을 지정 하거나 `pip_requirements_file` 매개 변수를 사용 하 여 txt 파일에 모든 pip 요구 사항을 나열할 수도 있습니다.

또한 Azure ML은 Tensorflow, PyTorch, 체 이너 및 추정에 대 한 프레임 워크 관련 제공 합니다. 이러한 추정을 사용 하면 학습에 사용 되는 환경에서 프레임 워크 종속성을 대신 설치할 수 있습니다. 위에서 설명한 대로 추가 종속성을 지정 하는 옵션이 있습니다. 
 
Azure ML에서 관리 하는 docker 이미지와 해당 콘텐츠는 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 볼 수 있습니다.
프레임 워크 관련 종속성은 해당 프레임 워크 설명서- [체](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), 고 지 사항 [배우기](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)에 나열 되어 있습니다.

> [!Note]
> 특정 패키지가 Azure 기계 학습에서 유지 관리 되는 이미지 및 환경에 추가할 수 있는 것으로 생각 되는 경우 [AzureML 컨테이너](https://github.com/Azure/AzureML-Containers)에서 GitHub 문제를 제기 하세요. 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (이름이 정의 되지 않음), AttributeError (개체에 특성이 없음)
이 예외는 학습 스크립트에서 제공 되어야 합니다. Azure Portal에서 로그 파일을 확인 하 여 지정 되지 않은 특정 이름 또는 특성 오류에 대 한 자세한 정보를 볼 수 있습니다. SDK에서 `run.get_details()`를 사용 하 여 오류 메시지를 확인할 수 있습니다. 또한 실행을 위해 생성 된 모든 로그 파일을 나열 합니다. 학습 스크립트를 확인 하 고 다시 시도 하기 전에 오류를 수정 하세요. 

### <a name="horovod-is-shut-down"></a>Horovod가 종료 되었습니다.
대부분의 경우이 예외는 horovod 종료를 일으킨 프로세스 중 하나에 기본 예외가 있음을 의미 합니다. MPI 작업의 각 순위는 Azure ML의 전용 로그 파일을 가져옵니다. 이러한 로그의 이름은 `70_driver_logs`입니다. 분산 교육의 경우 로그를 쉽게 구분할 수 있도록 로그 이름에 `_rank` 접미사가 붙습니다. Horovod shutdown이 발생 한 정확한 오류를 찾으려면 모든 로그 파일을 확인 하 고 driver_log 파일의 끝에 있는 `Traceback`를 확인 합니다. 이러한 파일 중 하나는 실제 기본 예외를 제공 합니다. 

## <a name="labeling-projects-issues"></a>프로젝트 문제 레이블 지정

프로젝트 레이블 지정의 알려진 문제입니다.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Blob 데이터 저장소에서 만든 데이터 집합만 사용할 수 있습니다.

현재 릴리스의 알려진 제한 사항입니다. 

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>프로젝트를 만든 후에는 오랜 시간 동안 "초기화 중"이 표시 됩니다.

페이지를 수동으로 새로 고칩니다. 초기화는 초당 약 20 datapoints 진행 되어야 합니다. Autorefresh의 부족은 알려진 문제입니다. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>이미지를 검토할 때 새로 레이블이 지정 된 이미지는 표시 되지 않습니다.

레이블이 지정 된 모든 이미지를 로드 하려면 **첫 번째** 단추를 선택 합니다. **첫 번째** 단추는 목록 맨 앞으로 다시 이동 하지만 레이블이 지정 된 모든 데이터를 로드 합니다.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>개체 검색에 대 한 레이블을 지정 하는 동안 Esc 키를 누르면 왼쪽 위 모퉁이에 크기가 0 인 레이블이 생성 됩니다. 이 상태의 레이블 전송에 실패 합니다.

옆의 십자 표시를 클릭 하 여 레이블을 삭제 합니다.

## <a name="run-or-experiment-deletion"></a>실행 또는 실험 삭제

실험 [은 실험을 사용 하거나](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) Azure Machine Learning studio Client의 실험 탭 보기에서 보관할 수 있습니다. 이 동작을 수행 하면 쿼리 및 뷰 목록에서 실험을 숨길 수 있지만 삭제 하지는 않습니다.

개별 실험 또는 실행을 영구적으로 삭제 하는 것은 현재 지원 되지 않습니다. 작업 영역 자산을 삭제 하는 방법에 대 한 자세한 내용은 [Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제](how-to-export-delete-data.md)를 참조 하세요.

## <a name="moving-the-workspace"></a>작업 영역 이동

> [!WARNING]
> Azure Machine Learning 작업 영역을 다른 구독으로 이동 하거나 소유 하는 구독을 새 테 넌 트로 이동 하는 것은 지원 되지 않습니다. 이렇게 하면 오류가 발생할 수 있습니다.
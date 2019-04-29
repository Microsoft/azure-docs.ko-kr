---
title: 모델 학습의 컴퓨팅 대상 만들기 및 사
titleSuffix: Azure Machine Learning service
description: 기계 학습 모델 학습의 학습 환경(컴퓨팅 대상)을 구성합니다. 학습 환경을 쉽게 전환할 수 있습니다. 로컬로 학습을 시작합니다. 규모 확장이 필요한 경우 클라우드 기반 컴퓨팅 대상으로 전환합니다.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9c97f23c2dfc2b1c0ff794aa20ffb58cd8b8741a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819338"
---
# <a name="set-up-compute-targets-for-model-training"></a>모델 학습의 컴퓨팅 대상 설정

Azure Machine Learning Service를 사용하여 다양한 리소스 또는 환경(총체적으로 [__컴퓨팅 대상__](concept-azure-machine-learning-architecture.md#compute-target)이라고 함)에서 모델을 학습할 수 있습니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning Compute, Azure HDInsight 또는 원격 가상 머신)일 수 있습니다.  ["모델 배포 위치 및 방법"](how-to-deploy-and-where.md)의 설명에 따라 모델 배포용 컴퓨팅 대상을 만들 수도 있습니다.

Azure Machine Learning SDK, Azure Portal 또는 Azure CLI를 사용하여 컴퓨팅 대상을 만들고 관리할 수 있습니다. 다른 서비스(예: HDInsight 클러스터)를 통해 만든 컴퓨팅 대상이 있는 경우 해당 컴퓨팅 대상을 Azure Machine Learning Service 작업 영역에 연결하여 사용할 수 있습니다.
 
이 문서에서는 모델 학습에 다양한 컴퓨팅 대상을 사용하는 방법을 알아봅니다.  모든 컴퓨팅 대상에 대한 단계는 동일한 워크플로를 따릅니다.
1. 컴퓨팅 대상이 이미 없는 경우 __만듭니다__.
2. 작업 영역에 컴퓨팅 대상을 __연결__합니다.
3. 해당 스크립트에 필요한 Python 환경과 패키지 종속성을 포함하도록 컴퓨팅 대상을 __구성__합니다.


>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 1.0.6에서 테스트되었습니다.

## <a name="compute-targets-for-training"></a>학습용 컴퓨팅 대상

Azure Machine Learning Service에는 다양한 컴퓨팅 대상에 대한 다양한 지원이 있습니다. 일반적인 모델 개발 수명 주기는 작은 양의 데이터에 대한 개발/실험으로 시작합니다. 이 단계에서는 로컬 환경을 사용하는 것이 좋습니다. 예를 들어 로컬 컴퓨터 또는 클라우드 기반 VM입니다. 더 큰 데이터 세트를 기반으로 학습을 확장하거나 분산 학습을 수행할 경우 Azure Machine Learning 컴퓨팅을 사용하여 실행을 제출할 때마다 자동 크기 조정되는 단일 또는 다중 노드 클러스터를 만드는 것이 좋습니다. 다음 설명대로 다양한 시나리오 지원이 달라질 수는 있지만 고유한 컴퓨팅 리소스를 연결할 수도 있습니다.


|학습용 컴퓨팅 대상| GPU 가속 | 자동<br/> 하이퍼 매개 변수 튜닝 | 자동</br> Machine Learning | Azure Machine Learning 파이프라인 |
|----|:----:|:----:|:----:|:----:|
|[로컬 컴퓨터](#local)| 가능할 수도 있음 | &nbsp; | ✓ | &nbsp; |
|[Azure Machine Learning 컴퓨팅](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[원격 VM](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](how-to-create-your-first-pipeline.md#databricks)| &nbsp; | &nbsp; | ✓ | ✓ |
|[Azure 데이터 레이크 분석](how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |
|[Azure Batch](#azbatch)| &nbsp; | &nbsp; | &nbsp; | ✓ |

**모든 컴퓨팅 대상을 여러 학습 작업에 다시 사용할 수 있습니다**. 예를 들어 원격 VM을 사용자의 작업 영역에 연결한 후에는 여러 작업에 다시 사용할 수 있습니다.

> [!NOTE]
> Azure Machine Learning 컴퓨팅은 영구 리소스로 생성되거나 실행을 요청할 때 동적으로 생성될 수 있습니다. 실행에 기반한 생성은 학습 실행이 완료된 후 컴퓨팅 대상을 제거하므로 이 방법으로 생성된 컴퓨팅 대상을 다시 사용할 수 없습니다.

## <a name="whats-a-run-configuration"></a>실행 구성이란?

학습 시에는 보통 로컬 컴퓨터에서 시작한 후 나중에 다른 컴퓨팅 대상에서 해당 학습 스크립트를 실행합니다. Azure Machine Learning Service를 사용하면 스크립트를 변경할 필요 없이 다양한 컴퓨팅 대상에서 스크립트를 실행할 수 있습니다. 

**실행 구성**으로 각 컴퓨팅 대상에 대한 환경을 정의하기만 하면 됩니다.  그런 다음 다른 컴퓨팅 대상에서 학습 실험을 실행하려는 경우에 해당 컴퓨팅에 대한 실행 구성을 지정합니다. 

이 문서의 끝부분에서 [실험 제출](#submit)에 대해 자세히 알아보세요.

### <a name="manage-environment-and-dependencies"></a>환경 및 종속성 관리

실행 구성을 만들 때 컴퓨팅 대상에서 환경 및 종속성을 관리하는 방법을 결정해야 합니다. 

#### <a name="system-managed-environment"></a>시스템 관리 환경

[Conda](https://conda.io/docs/)를 통해 Python 환경과 스크립트 종속성을 자동으로 관리하고자 하는 경우 시스템 관리 환경을 사용합니다. 시스템 관리 환경은 기본적으로 가정되는 가장 일반적으로 선택되는 환경입니다. 이 환경은 원격 컴퓨팅 대상에서 유용합니다(특히 해당 대상을 구성할 수 없는 경우). 

[CondaDependency클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)를 사용하여 각 패키지 종속성을 지정하기만 하면 됩니다. 그러면 Conda에서 작업 영역의 **aml_config** 디렉터리에 패키지 종속성 목록이 포함된 **conda_dependencies.yml**이라는 파일을 만들고 사용자가 학습 실험을 제출하면 Python 환경을 설정합니다. 

필요한 종속성의 크기에 따라 새 환경의 초기 설정에 몇 분 정도 걸릴 수 있습니다. 패키지 목록이 변경되지 않고 그대로 유지되는 한 설정 시간은 한 번만 소요됩니다.
  
다음 코드는 Scikit-learn이 필요한 시스템 관리 환경의 예를 보여줍니다.
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>사용자 관리 환경

사용자 관리 환경에서는 사용자가 사용자 환경을 설정하고 컴퓨팅 대상에 학습 스크립트에 필요한 모든 패키지를 설치해야 합니다. 로컬 머신에서와 같이 학습 환경이 이미 구성되어 있다면 `user_managed_dependencies`를 True로 설정하여 설정 단계를 건너뛸 수 있습니다. Conda에서 자동으로 환경을 확인하지도 않고 아무 것도 설치하지 않습니다.

다음 코드는 사용자 관리 환경에 대한 학습 실행을 구성하는 예를 보여줍니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]
  
## <a name="set-up-compute-targets-with-python"></a>Python으로 컴퓨팅 대상 설정

아래의 섹션을 통해 다음과 같은 컴퓨팅 대상을 구성합니다.

* [로컬 컴퓨터](#local)
* [Azure Machine Learning 컴퓨팅](#amlcompute)
* [원격 가상 머신](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>로컬 컴퓨터

1. **만들기 및 연결**: 로컬 컴퓨터를 학습 환경으로 사용하기 위해 컴퓨팅 대상을 만들거나 연결하지 않아도 됩니다.  

1. **구성**:  로컬 컴퓨터를 컴퓨팅 대상으로 사용하는 경우 학습 코드가 [개발 환경](how-to-configure-environment.md)에서 실행됩니다.  해당 환경에 필요한 Python 패키지가 이미 있으면 사용자 관리 환경을 사용합니다.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](#submit)합니다.

### <a id="amlcompute"></a>Azure Machine Learning 컴퓨팅

Azure Machine Learning 컴퓨팅은 사용자가 단일 또는 다중 노드 컴퓨팅을 손쉽게 만들 수 있는 관리형 컴퓨팅 인프라입니다. 작업 영역 지역 내에서 컴퓨팅은 작업 영역에서 다른 사용자와 공유할 수 있는 리소스로 만들어집니다. 작업이 제출될 때 컴퓨팅이 자동으로 확장되어 Azure Virtual Network에 배치될 수 있습니다. 컴퓨팅은 컨테이너화된 환경에서 실행되며 모델 종속성을 [Docker 컨테이너](https://www.docker.com/why-docker)로 패키지합니다.

Azure Machine Learning 컴퓨팅을 사용하여 클라우드의 CPU 또는 GPU 컴퓨팅 노드 클러스터에 학습 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)를 참조하세요.

Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수와 같은 기본적인 제한이 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas)을 참조하세요.


실행을 예약할 때 주문형으로 또는 영구적 리소스로 Azure Machine Learning 컴퓨팅 환경을 만들 수 있습니다.

#### <a name="run-based-creation"></a>실행 기반 만들기

Azure Machine Learning 컴퓨팅을 런타임에 컴퓨팅 대상으로 만들 수 있습니다. 실행에 대해 컴퓨팅이 자동으로 만들어집니다. 실행이 완료되면 컴퓨팅이 자동으로 삭제됩니다. 

> [!NOTE]
> 사용할 노드의 최대 수를 지정 하려면 일반적으로 설정한 `node_count` 노드의 수입니다. 현재 (04/04/2019) 작업에서이 방지 하는 버그입니다. 사용 하 여이 문제를 해결 합니다 `amlcompute._cluster_max_node_count` 실행된 구성의 속성입니다. 예: `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> Azure Machine Learning 컴퓨팅의 실행 기반 만들기는 현재 미리 보기로 제공됩니다. 하이퍼 매개 변수 튜닝 또는 자동화된 Machine Learning을 사용 중인 경우에는 실행 기반 만들기를 사용하지 마세요. 하이퍼 매개 변수 튜닝 또는 자동화된 기계 학습을 사용하려면 [영구적 컴퓨팅](#persistent) 대상을 대신 만듭니다.

1.  **만들기, 연결 및 구성**: 실행 기반 만들기는 실행 구성을 사용하여 컴퓨팅 대상을 만들고 연결하고 구성하는데 필요한 모든 단계를 수행합니다.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](#submit)합니다.

#### <a id="persistent"></a>영구적 컴퓨팅

영구적 Azure Machine Learning 컴퓨팅은 작업 전반에서 다시 사용할 수 있습니다. 컴퓨팅은 작업 영역의 다른 사용자와 공유할 수 있고 작업 간에 유지됩니다.

1. **만들기 및 연결**: Python에서 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들려면 **vm_size** 및 **max_nodes** 속성을 지정합니다. 그런 다음, Azure Machine Learning은 다른 속성에 스마트 기본값을 사용합니다. 사용되지 않는 경우 컴퓨팅은 0개 노드로 자동 축소됩니다.   필요에 따라 작업을 실행하기 위해 전용 VM이 만들어집니다.
    
    * **vm_size**: Azure Machine Learning 컴퓨팅에서 만든 노드의 VM 제품군입니다.
    * **max_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행할 때 자동으로 확장할 최대 노드 수입니다.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning 컴퓨팅을 만들 때 여러 고급 속성을 구성할 수도 있습니다. 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.  자세한 내용은 [AmlCompute 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )를 참조하세요.
    
   또는 [Azure Portal](#portal-create)에서 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들고 연결할 수 있습니다.

1. **구성**: 영구적 컴퓨팅 대상에 대한 실행 구성을 만듭니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](#submit)합니다.


### <a id="vm"></a>원격 가상 머신

Azure Machine Learning은 자신만의 컴퓨팅 리소스를 가져와서 작업 영역에 연결하는 기능을 지원합니다. 해당 리소스 유형은 Azure Machine Learning Service에서 액세스할 수 있는 한 임의의 원격 VM입니다. 리소스는 조직 또는 온-프레미스에 있는 Azure VM 또는 원격 서버일 수 있습니다. 특히 IP 주소 및 자격 증명(사용자 이름 및 암호 또는 SSH 키)이 지정되면 원격 실행에 액세스 가능한 VM을 사용할 수 있습니다.

시스템 빌드 conda 환경, 기존 Python 환경 또는 Docker 컨테이너를 사용할 수 있습니다. Docker 컨테이너에서 실행하려면 Docker 엔진이 VM에서 실행 되어야 합니다. 이 기능은 로컬 머신보다 더 유연한 클라우드 기반 개발/실험 환경을 원하는 경우에 특히 유용합니다.

이 시나리오에서 선택하는 Azure VM으로 Azure Data Science Virtual Machine(DSVM)을 사용합니다. 이 VM은 Azure에서 미리 구성된 데이터 과학 및 AI 개발 환경입니다. 이 VM은 전체 수명 주기 기계 학습을 위해 큐레이팅된 도구 및 프레임워크 옵션을 제공합니다. Azure Machine Learning과 함께 DSVM을 사용하는 방법에 관한 자세한 내용은 [개발 환경 구성](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)을 참조하세요.

1. **만들기**: 모델 학습에 사용할 DSVM을 만듭니다. 이 리소스를 만들려면 [Linux(Ubuntu) 용 Data Science Virtual Machine 프로비전](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)을 참조하세요.

    > [!WARNING]
    > Azure Machine Learning은 Ubuntu를 실행하는 가상 머신만 지원합니다. VM을 만들거나 기존 VM을 선택하는 경우 Ubuntu를 사용하는 VM을 선택해야 합니다.

1. **연결**: 기존 가상 머신을 컴퓨팅 대상으로 연결하려면 가상 머신의 FQDN(정규화된 도메인 이름), 사용자 이름 및 암호를 입력해야 합니다. 이 예제에서는 \<fqdn>을 VM의 공용 FQDN 또는 공용 IP 주소로 바꿉니다. \<사용자 이름> 및 \<암호>를 VM에 대한 SSH 사용자 이름 및 암호로 바꿉니다.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Portal을 사용하여](#portal-reuse) 작업 영역에 DSVM을 연결할 수 있습니다.

1. **구성**: DSVM 컴퓨팅 대상에 대한 실행 구성을 만듭니다. Docker 및 conda는 DSVM에서 학습 환경을 만들고 구성하는 데 사용됩니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](#submit)합니다.

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight는 빅 데이터 분석을 위한 인기 있는 플랫폼입니다. 플랫폼은 모델을 학습하는 데 사용할 수 있는 Apache Spark를 제공합니다.

1. **만들기**:  모델을 학습하는 데 사용할 HDInsight 클러스터를 만듭니다. HDInsight 클러스터에서 Spark를 만들려면 [HDInsight에서 Spark 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)를 참조하세요. 

    클러스터를 만들 때 SSH 사용자 이름 및 암호를 지정해야 합니다. 이러한 값은 HDInsight를 컴퓨팅 대상으로 사용할 때 필요하므로 기록해 둡니다.
    
    클러스터를 만든 후 호스트이름 \<clustername>.azurehdinsight.net과 연결합니다. 여기서 \<clustername>은 클러스터에 대해 사용자가 제공한 이름입니다. 

1. **연결**: HDInsight 클러스터를 컴퓨팅 대상으로 연결하려면 HDInsight 클러스터의 호스트 이름, 사용자 이름 및 암호를 입력해야 합니다. 다음 예제에서는 SDK를 사용하여 작업 영역에 클러스터를 연결합니다. 예제에서는 \<clustername>을 클러스터의 이름으로 바꿉니다. \<username> 및 \<password>를 클러스터에 대한 SSH 사용자 이름 및 암호로 바꿉니다.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Portal을 사용하여](#portal-reuse) 작업 영역에 HDInsight 클러스터를 연결할 수 있습니다 

1. **구성**: HDI 컴퓨팅 대상에 대한 실행 구성을 만듭니다. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](#submit)합니다.


### <a id="azbatch"></a>Azure Batch 

Azure Batch는 클라우드에서 대규모 병렬 및 고성능 컴퓨팅 (HPC) 응용 프로그램을 효율적으로 실행 하는 데 사용 됩니다. AzureBatchStep 머신의 Azure Batch 풀에 작업을 제출 하는 Azure Machine Learning 파이프라인에서 사용할 수 있습니다.

계산 대상으로 Azure Batch에 연결 하려면 Azure Machine Learning SDK를 사용 하 고 다음 정보를 제공 해야 합니다.

-   **Azure Batch 계산 이름을**: 작업 영역 내에서 계산에 사용할 친숙 한 이름
-   **Azure Batch 계정 이름**: Azure Batch 계정의 이름
-   **리소스 그룹**: Azure Batch 계정을 포함 하는 리소스 그룹.

다음 코드에는 Azure Batch 계산 대상으로 연결 하는 방법을 보여 줍니다.

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

batch_compute_name = 'mybatchcompute' # Name to associate with new compute in workspace

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>" # Name of the Batch account
batch_resource_group = "<batch_resource_group>" # Name of the resource group which contains this account

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-compute-in-the-azure-portal"></a>Azure Portal에서 컴퓨팅 설정

Azure Portal에서 작업 영역과 연결된 컴퓨팅 대상에 액세스할 수 있습니다.  Azure Portal을 사용하여 다음을 수행할 수 있습니다.

* 작업 영역에 연결된 [컴퓨팅 대상 보기](#portal-view)
* 작업 영역에서 [컴퓨팅 대상 만들기](#portal-create)
* 작업 영역 외부에서 만든 [컴퓨팅 대상 연결](#portal-reuse)

대상을 만들고 작업 영역에 연결한 후 `ComputeTarget` 개체와 함께 실행 구성에서 대상을 사용합니다. 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>컴퓨팅 대상 보기


작업 영역에 대한 컴퓨팅 대상을 확인하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)로 이동하여 자신의 작업 영역을 엽니다. 
1. __애플리케이션__에서 __컴퓨팅__을 선택합니다.

    ![[계산 보기] 탭](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a id="portal-create"></a>컴퓨팅 대상 만들기

컴퓨팅 대상의 목록을 보려면 이전 단계를 수행합니다. 그런 다음, 다음과 같은 단계를 사용하여 컴퓨팅 대상을 만듭니다. 

1. 더하기 기호(+)를 선택하여 컴퓨팅 대상을 추가합니다.

    ![컴퓨팅 대상 추가](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. 계산 대상의 이름을 입력합니다. 

1. __학습__에 사용할 컴퓨팅 유형으로 **Machine Learning 컴퓨팅**을 선택합니다. 

    >[!NOTE]
    >Azure Portal에서 만들 수 있는 관리되는 컴퓨팅 리소스는 Azure Machine Learning 컴퓨팅뿐입니다.  다른 모든 컴퓨팅 리소스는 만든 후에 연결할 수 있습니다.

1. 양식을 작성합니다. 필요한 속성, 특히 컴퓨팅을 스핀업하는 데 사용하는 **VM 제품군** 및 **최대 노드**에 대한 값을 입력합니다.  

    ![양식 작성](./media/how-to-set-up-training-targets/add-compute-form.png) 

1. __만들기__를 선택합니다.


1. 목록에서 컴퓨팅 대상을 선택하여 만들기 작업의 상태를 봅니다.

    ![만들기 작업 상태를 보려면 컴퓨팅 대상을 선택합니다.](./media/how-to-set-up-training-targets/View_list.png)

1. 그러면 컴퓨팅 대상의 세부 정보가 표시됩니다. 

    ![컴퓨팅 대상 세부 정보 보기](./media/how-to-set-up-training-targets/compute-target-details.png) 



### <a id="portal-reuse"></a>컴퓨팅 대상 연결

Azure Machine Learning Service 작업 영역 외부에서 만든 컴퓨팅 대상을 사용하려면 연결해야 합니다. 컴퓨팅 대상을 연결하면 작업 영역에서 사용할 수 있습니다.

컴퓨팅 대상의 목록을 보려면 이전에 설명한 단계를 수행합니다. 그런 다음, 아래 단계에 따라 컴퓨팅 대상을 연결합니다. 

1. 더하기 기호(+)를 선택하여 컴퓨팅 대상을 추가합니다. 
1. 계산 대상의 이름을 입력합니다. 
1. __학습__용으로 연결할 컴퓨팅 유형을 선택합니다.

    > [!IMPORTANT]
    > Azure Portal에서 모든 컴퓨팅 유형을 연결할 수 있는 것은 아닙니다. 현재 학습용으로 연결할 수 있는 컴퓨팅 유형은 다음과 같습니다.
    >
    > * 원격 VM
    > * Azure Databricks(기계 학습 파이프라인에 사용)
    > * Azure Data Lake Analytics(기계 학습 파이프라인에 사용)
    > * Azure HDInsight

1. 양식을 입력하고 필요한 속성에 대한 값을 입력합니다.

    > [!NOTE]
    > Microsoft에서는 암호보다 더 안전한 SSH 키를 권장합니다. 암호는 무차별 암호 대입 공격에 취약합니다. SSH 키는 암호화 서명을 사용합니다. Azure Virtual Machines에 사용할 SSH 키를 만드는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
    >
    > * [Linux 또는 macOS에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows에서 SSH 키를 만들고 사용](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __연결__을 선택합니다. 
1. 목록에서 컴퓨팅 대상을 선택하여 연결 작업의 상태를 봅니다.

## <a name="set-up-compute-with-the-cli"></a>CLI를 사용하여 컴퓨팅 설정

Azure Machine Learning Service용 [CLI 확장](reference-azure-machine-learning-cli.md)을 사용하여 작업 영역과 연결되어 있는 컴퓨팅 대상에 액세스할 수 있습니다.  CLI를 사용하여 다음을 수행할 수 있습니다.

* 관리되는 컴퓨팅 대상 만들기
* 관리되는 컴퓨팅 대상 업데이트
* 관리되지 않는 컴퓨팅 대상 연결

자세한 내용은 [리소스 관리](reference-azure-machine-learning-cli.md#resource-management)를 참조하세요.

## <a id="submit"></a>학습 실행 제출

실행 구성을 만든 후 실행 구성을 사용하여 해당 실험을 실행합니다.  학습 실행을 제출하는 코드 패턴은 모든 유형의 컴퓨팅 대상에서 동일합니다.

1. 실행할 실험 만들기
1. 실행을 제출합니다.
1. 실행이 완료될 때까지 기다립니다.

> [!IMPORTANT]
> 교육 실행을 제출할 때 학습 스크립트를 포함 하는 디렉터리의 스냅숏이 생성 되어 계산 대상으로 전송 됩니다. 또한 작업 영역에서 실험의 일부로 저장 됩니다. 파일을 변경 하 고 실행을 제출 하는 경우 변경된 된 파일만 다시 업로드 됩니다.
>
> 만들기를 방지 하기 파일 스냅숏이 포함 되는 [.gitignore](https://git-scm.com/docs/gitignore) 또는 `.amlignore` 디렉터리에 파일 및 파일에 추가 합니다. 합니다 `.amlignore` 동일한 구문을 사용 하 여 파일과 패턴으로 [.gitignore](https://git-scm.com/docs/gitignore) 파일입니다. 두 파일이 존재 하는 경우는 `.amlignore` 파일이 우선 합니다.
> 
> 자세한 내용은 [스냅숏](concept-azure-machine-learning-architecture.md#snapshot)을 참조하세요.

### <a name="create-an-experiment"></a>실험 만들기

먼저 작업 영역에서 실험을 만듭니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>실험 제출

`ScriptRunConfig` 개체와 함께 실험을 제출합니다.  이 개체는 다음을 포함합니다.

* **source_directory**: 학습 스크립트를 포함하는 원본 디렉터리
* **스크립트**: 학습 스크립트 식별
* **run_config**: 학습이 발생할 위치를 차례로 정의하는 실행 구성입니다.

예를 들어 [로컬 대상](#local) 구성을 사용하려면:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

[amlcompute 대상](#amlcompute)과 같은 다른 실행 구성을 사용하여 다른 컴퓨팅 대상에서 실행되도록 동일한 실험을 전환합니다.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

또는

* [추정기를 사용하여 ML 모델 학습](how-to-train-ml-models.md)에 표시된 대로 `Estimator` 개체와 함께 실험을 제출합니다. 
* [CLI 확장을 사용하여](reference-azure-machine-learning-cli.md#experiments) 실험을 제출합니다.

## <a name="notebook-examples"></a>Notebook 예제

이러한 Notebook에서 다양한 컴퓨팅 대상으로 학습하는 예를 참조하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [RunConfiguration 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK 참조를 확인합니다.
* [Azure Virtual Networks에서 Azure Machine Learning Service 사용](how-to-enable-virtual-network.md)

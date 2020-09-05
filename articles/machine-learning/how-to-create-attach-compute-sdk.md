---
title: Python SDK를 사용 하 여 계산 리소스 만들기
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Python SDK를 사용 하 여 기계 학습을 위한 교육 및 배포 계산 리소스 (계산 대상) 만들기
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: 96aa6839fe51bb8a8c26f411c1a1f9df6b8c5a7f
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147500"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Python SDK를 사용 하 여 모델 학습 및 배포를 위한 계산 대상 만들기

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning Python SDK를 사용 하 여 계산 대상을 만들고 관리 합니다. 다음을 사용 하 여 계산 대상을 만들고 관리할 수도 있습니다.
* [Azure Machine Learning studio](how-to-create-attach-compute-studio.md) 
* Azure Machine Learning에 대 한 [CLI 확장](reference-azure-machine-learning-cli.md#resource-management)
* Azure Machine Learning [VS Code 확장](how-to-manage-resources-vscode.md#compute-clusters) 입니다.


## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 현재 [Azure Machine Learning의 무료 또는 유료 버전](https://aka.ms/AMLFree) 체험
* [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>계산 대상 이란?

Azure Machine Learning을 사용하여 다양한 리소스 또는 환경(총체적으로 [__컴퓨팅 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 함)에서 모델을 학습시킬 수 있습니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning 컴퓨팅, Azure HDInsight 또는 원격 가상 머신)입니다.  ["모델 배포 위치 및 방법"](how-to-deploy-and-where.md)의 설명에 따라 모델 배포용 컴퓨팅 대상을 만들 수도 있습니다.

## <a name="compute-targets-for-training"></a>학습용 컴퓨팅 대상

Azure Machine Learning에는 다양한 컴퓨팅 대상에 대한 다양한 지원이 포함되어 있습니다. 일반적인 모델 개발 수명 주기는 작은 양의 데이터에 대한 개발/실험으로 시작합니다. 이 단계에서는 로컬 환경을 사용하는 것이 좋습니다. 예를 들어 로컬 컴퓨터 또는 클라우드 기반 VM입니다. 더 큰 데이터 세트를 기반으로 학습을 확장하거나 분산 학습을 수행할 경우 Azure Machine Learning 컴퓨팅을 사용하여 실행을 제출할 때마다 자동 스케일링되는 단일 또는 다중 노드 클러스터를 만드는 것이 좋습니다. 다음 설명대로 다양한 시나리오 지원이 달라질 수는 있지만 고유한 컴퓨팅 리소스를 연결할 수도 있습니다.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning 계산 클러스터는 영구 리소스로 생성 하거나 실행을 요청할 때 동적으로 만들 수 있습니다. 실행에 기반한 생성은 학습 실행이 완료된 후 컴퓨팅 대상을 제거하므로 이 방법으로 생성된 컴퓨팅 대상을 다시 사용할 수 없습니다.

아래의 섹션을 통해 다음과 같은 컴퓨팅 대상을 구성합니다.

* [로컬 컴퓨터](#local)
* [Azure Machine Learning 컴퓨팅 클러스터](#amlcompute)
* [Azure Machine Learning 컴퓨팅 인스턴스](#instance)
* [원격 가상 머신](#vm)
* [Azure HDInsight](#hdinsight)


## <a name="local-computer"></a><a id="local"></a>로컬 컴퓨터

로컬 컴퓨터를 학습에 사용 하는 경우 계산 대상을 만들 필요가 없습니다.  로컬 컴퓨터에서 [학습 실행을 제출](how-to-set-up-training-targets.md) 하면 됩니다.

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning 계산 클러스터

Azure Machine Learning 계산 클러스터는 단일 또는 다중 노드 계산을 쉽게 만들 수 있는 관리 되는 계산 인프라입니다. 작업 영역 지역 내에서 컴퓨팅은 작업 영역에서 다른 사용자와 공유할 수 있는 리소스로 만들어집니다. 작업이 제출될 때 컴퓨팅이 자동으로 확장되어 Azure Virtual Network에 배치될 수 있습니다. 컴퓨팅은 컨테이너화된 환경에서 실행되며 모델 종속성을 [Docker 컨테이너](https://www.docker.com/why-docker)로 패키지합니다.

Azure Machine Learning 컴퓨팅을 사용하여 클라우드의 CPU 또는 GPU 컴퓨팅 노드 클러스터에 학습 프로세스를 배포할 수 있습니다. GPU를 포함하는 VM 크기에 대한 자세한 내용은 [GPU 최적화 가상 머신 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)를 참조하세요. 

Azure Machine Learning 컴퓨팅에는 할당할 수 있는 코어 수와 같은 기본적인 제한이 있습니다. 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조하세요.

> [!TIP]
> 필요한 코어 수만큼 할당량이 있는 경우 일반적으로 클러스터를 최대 100노드까지 스케일 업할 수 있습니다. 예를 들어 기본적으로 클러스터 노드 간에는 노드 간 통신을 사용하도록 설정하여 MPI 작업을 지원합니다. 그러나 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 수천 개의 클러스터의 크기를 조정 하 고, 구독 또는 작업 영역을 나열 하도록 요청 하거나, 노드 간 통신을 사용 하지 않도록 설정할 수 있습니다. 

Azure Machine Learning 컴퓨팅은 실행 전반에서 다시 사용할 수 있습니다. 컴퓨팅은 작업 영역에 있는 다른 사용자와 공유할 수 있으며, 실행 간에 유지되고 제출된 실행 수와 클러스터에 설정된 max_nodes 수에 따라 노드를 자동으로 확장 또는 축소합니다. min_nodes 설정은 사용 가능한 최소 노드 수를 제어합니다.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **만들기 및 연결**: Python에서 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들려면 **vm_size** 및 **max_nodes** 속성을 지정합니다. 그런 다음, Azure Machine Learning은 다른 속성에 스마트 기본값을 사용합니다. 사용되지 않는 경우 컴퓨팅은 0개 노드로 자동 축소됩니다.   필요에 따라 작업을 실행하기 위해 전용 VM이 만들어집니다.
    
    * **vm_size**: Azure Machine Learning 컴퓨팅에서 만든 노드의 VM 제품군입니다.
    * **max_nodes**: Azure Machine Learning 컴퓨팅에서 작업을 실행할 때 자동으로 확장할 최대 노드 수입니다.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning 컴퓨팅을 만들 때 여러 고급 속성을 구성할 수도 있습니다. 속성을 사용하면 고정 크기로 또는 구독의 기존 Azure Virtual Network 내에서 영구적 클러스터를 만들 수 있습니다.  자세한 내용은 [AmlCompute 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )를 참조하세요.

    또는 [Azure Machine Learning 스튜디오](how-to-create-attach-compute-studio.md#portal-create)에서 영구적 Azure Machine Learning 컴퓨팅 리소스를 만들고 연결할 수 있습니다.

이제 계산을 연결 했으므로 다음 단계는 [학습 실행을 제출](how-to-set-up-training-targets.md)하는 것입니다.

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 계산 클러스터 비용 낮추기

[우선 순위가 낮은 vm](concept-plan-manage-cost.md#low-pri-vm) 을 사용 하 여 작업의 일부 또는 전체를 실행 하도록 선택할 수도 있습니다. 이러한 VM은 가용성이 보장되지 않고 사용 중에 선점될 수 있습니다. 선점된 작업은 계속되지 않고 다시 시작됩니다. 

이러한 방법 중 하나를 사용 하 여 우선 순위가 낮은 VM을 지정 합니다.
    
* 스튜디오에서 VM을 만들 때 **낮은 우선 순위** 를 선택 합니다.
    
* Python SDK를 사용 하 여 `vm_priority` 프로 비전 구성에서 특성을 설정 합니다.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* CLI를 사용 하 여를 설정 합니다 `vm-priority` .
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 관리 id 설정

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

프로 비전 구성에서 관리 되는 id 구성:  
    
* 시스템 할당 관리 id:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* 사용자가 할당한 관리형 ID: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

기존 계산 클러스터에 관리 되는 id를 추가 합니다.  
    
* 시스템이 할당한 관리형 ID:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* 사용자가 할당한 관리형 ID:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>관리 id 사용

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Azure Machine Learning 컴퓨팅 인스턴스

[Azure Machine Learning 계산 인스턴스](concept-compute-instance.md) 는 단일 VM을 쉽게 만들 수 있는 관리 되는 계산 인프라입니다. 계산은 작업 영역 영역 내에 만들어지지만 계산 클러스터와는 달리 인스턴스를 작업 영역에 있는 다른 사용자와 공유할 수 없습니다. 또한 인스턴스는 자동으로 축소 되지 않습니다.  지속적인 요금을 방지 하려면 리소스를 중지 해야 합니다.

계산 인스턴스는 여러 작업을 병렬로 실행할 수 있으며 작업 큐가 있습니다. 

계산 인스턴스는 회사에서 SSH 포트를 열지 않아도 [가상 네트워크 환경](how-to-enable-virtual-network.md#compute-instance)에서 작업을 안전 하 게 실행할 수 있습니다. 작업은 컨테이너 화 된 환경에서 실행 되며 모델 종속성을 Docker 컨테이너에 패키지 합니다. 

1. **만들기 및 연결**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

계산을 연결 하 고 실행을 구성 했으므로 다음 단계는 [학습 실행을 제출](how-to-set-up-training-targets.md) 하는 것입니다.


## <a name="remote-virtual-machines"></a><a id="vm"></a>원격 가상 머신

Azure Machine Learning은 자신만의 컴퓨팅 리소스를 가져와서 작업 영역에 연결하는 기능을 지원합니다. Azure Machine Learning에서 액세스할 수 있는 한, 이 리소스 유형은 임의의 원격 VM입니다. 리소스는 조직 또는 온-프레미스에 있는 Azure VM 또는 원격 서버일 수 있습니다. 특히 IP 주소 및 자격 증명(사용자 이름 및 암호 또는 SSH 키)이 지정되면 원격 실행에 액세스 가능한 VM을 사용할 수 있습니다.

시스템 빌드 conda 환경, 기존 Python 환경 또는 Docker 컨테이너를 사용할 수 있습니다. Docker 컨테이너에서 실행하려면 Docker 엔진이 VM에서 실행 되어야 합니다. 이 기능은 로컬 머신보다 더 유연한 클라우드 기반 개발/실험 환경을 원하는 경우에 특히 유용합니다.

이 시나리오에서 azure VM (azure Data Science Virtual Machine (DSVM)을 선택 하는 것이 좋습니다. 이 VM은 Azure에서 미리 구성된 데이터 과학 및 AI 개발 환경입니다. 이 VM은 전체 수명 주기 기계 학습을 위해 큐레이팅된 도구 및 프레임워크 옵션을 제공합니다. Azure Machine Learning과 함께 DSVM을 사용하는 방법에 관한 자세한 내용은 [개발 환경 구성](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)을 참조하세요.

1. **만들기**: 모델 학습에 사용할 DSVM을 만듭니다. 이 리소스를 만들려면 [Linux(Ubuntu) 용 Data Science Virtual Machine 프로비전](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)을 참조하세요.

    > [!WARNING]
    > Azure Machine Learning는 **Ubuntu**를 실행 하는 가상 컴퓨터만 지원 합니다. VM을 만들거나 기존 VM을 선택하는 경우 Ubuntu를 사용하는 VM을 선택해야 합니다.
    > 
    > 또한 Azure Machine Learning에는 가상 머신에 __공용 IP 주소가__있어야 합니다.

1. **연결**: 기존 가상 머신을 컴퓨팅 대상으로 연결하려면 가상 머신의 리소스 ID, 사용자 이름 및 암호를 입력해야 합니다. VM의 리소스 ID는 구독 ID, 리소스 그룹 이름 및 VM 이름을 사용하여 `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>` 문자열 형식으로 생성할 수 있습니다.

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Machine Learning 스튜디오를 사용](how-to-create-attach-compute-studio.md#attached-compute)하여 작업 영역에 DSVM을 연결할 수도 있습니다.

1. **구성**: DSVM 컴퓨팅 대상에 대한 실행 구성을 만듭니다. Docker 및 conda는 DSVM에서 학습 환경을 만들고 구성하는 데 사용됩니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](how-to-set-up-training-targets.md)합니다.

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight는 빅 데이터 분석을 위한 인기 있는 플랫폼입니다. 플랫폼은 모델을 학습하는 데 사용할 수 있는 Apache Spark를 제공합니다.

1. **만들기**:  모델을 학습하는 데 사용할 HDInsight 클러스터를 만듭니다. HDInsight 클러스터에서 Spark를 만들려면 [HDInsight에서 Spark 클러스터 만들기](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)를 참조하세요. 

    > [!WARNING]
    > Azure Machine Learning 하려면 HDInsight 클러스터에 __공용 IP 주소가__있어야 합니다.

    클러스터를 만들 때 SSH 사용자 이름 및 암호를 지정해야 합니다. 이러한 값은 HDInsight를 컴퓨팅 대상으로 사용할 때 필요하므로 기록해 둡니다.
    
    클러스터를 만든 후에 \<clustername> 는 호스트 이름-ssh.azurehdinsight.net을 사용 하 여 연결 \<clustername> 합니다. 여기서은 클러스터에 대해 제공한 이름입니다. 

1. **연결**: HDInsight 클러스터를 컴퓨팅 대상으로 연결하려면 HDInsight 클러스터의 리소스 ID, 사용자 이름 및 암호를 입력해야 합니다. HDInsight 클러스터의 리소스 ID는 구독 ID, 리소스 그룹 이름 및 HDInsight 클러스터 이름을 사용하여 `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>` 문자열 형식으로 생성할 수 있습니다.

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
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

   또는 [Azure Machine Learning 스튜디오를 사용](how-to-create-attach-compute-studio.md#attached-compute)하여 작업 영역에 HDInsight 클러스터를 연결할 수도 있습니다.

1. **구성**: HDI 컴퓨팅 대상에 대한 실행 구성을 만듭니다. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


컴퓨팅 대상을 연결하고 실행을 구성했으면 다음 단계로 [학습 실행을 제출](how-to-set-up-training-targets.md)합니다.

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch는 클라우드에서 대규모 병렬 및 HPC(고성능 컴퓨팅) 애플리케이션을 효율적으로 실행하는 데 사용됩니다. AzureBatchStep은 Azure Machine Learning 파이프라인에 사용하여 머신의 Azure Batch 풀에 작업을 제출할 수 있습니다.

Azure Batch를 컴퓨팅 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

-    **Azure Batch 컴퓨팅 이름**: 작업 영역 내에서 컴퓨팅에 사용되는 식별 이름입니다.
-    **Azure Batch 계정 이름**: Azure Batch 계정의 이름
-    **리소스 그룹**: Azure Batch 계정을 포함하는 리소스 그룹입니다.

다음 코드는 컴퓨팅 대상으로 Azure Batch를 연결하는 방법을 보여줍니다.

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks는 Azure 클라우드의 Apache Spark 기반 환경입니다. 이 환경은 Azure Machine Learning 파이프라인 사용 시 컴퓨팅 대상으로 사용할 수 있습니다.

사용하기 전에 Azure Databricks 작업 영역을 만듭니다. 작업 영역 리소스를 만들려면 [Azure Databricks에서 Spark 작업 실행](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) 문서를 참조 하세요.

Azure Databricks를 컴퓨팅 대상으로 연결하려면 다음 정보를 제공합니다.

* __Databricks compute name__:이 계산 리소스에 할당할 이름입니다.
* __Databricks 작업 영역 이름__: Azure Databricks 작업 영역의 이름입니다.
* __Databricks access token__: Azure Databricks을 인증 하는 데 사용 되는 액세스 토큰입니다. 액세스 토큰을 생성하려면 [인증](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) 문서를 참조하세요.

다음 코드는 Azure Machine Learning SDK를 사용 하 여 Azure Databricks를 계산 대상으로 연결 하는 방법을 보여 줍니다.__Databricks 작업 영역은 AML 작업 영역과 동일한 구독에 있어야 합니다__.

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

자세한 예제는 GitHub의 [예제 노트북](https://aka.ms/pl-databricks) 을 참조 하세요.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure 데이터 레이크 분석

Azure Data Lake Analytics는 Azure 클라우드의 빅 데이터 분석 플랫폼입니다. 이 환경은 Azure Machine Learning 파이프라인 사용 시 컴퓨팅 대상으로 사용할 수 있습니다.

사용하기 전에 Azure Data Lake Analytics 계정을 만듭니다. 이 리소스를 만들려면 [Azure Data Lake Analytics 시작](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) 문서를 참조하세요.

Data Lake Analytics를 컴퓨팅 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당하려는 이름입니다.
* __리소스 그룹__: Data Lake Analytics 계정이 포함 된 리소스 그룹입니다.
* __계정 이름__: Data Lake Analytics 계정 이름입니다.

다음 코드는 컴퓨팅 대상으로 Data Lake Analytics에 연결하는 방법을 보여 줍니다.

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

자세한 예제는 GitHub의 [예제 노트북](https://aka.ms/pl-adla) 을 참조 하세요.

> [!TIP]
> Azure Machine Learning 파이프라인은 Data Lake Analytics 계정의 기본 데이터 저장소에 저장된 데이터에만 작동할 수 있습니다. 작업 해야 하는 데이터가 기본이 아닌 저장소에 있는 경우를 사용 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 하 여 학습 전에 데이터를 복사할 수 있습니다.

## <a name="notebook-examples"></a>Notebook 예제

이러한 Notebook에서 다양한 컴퓨팅 대상으로 학습하는 예를 참조하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* 계산 리소스를 사용 하 여 [학습 실행을 제출](how-to-set-up-training-targets.md)합니다.
* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](how-to-enable-virtual-network.md)

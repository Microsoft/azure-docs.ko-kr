---
title: 가상 네트워크에서 실험 및 유추 실행
titleSuffix: Azure Machine Learning service
description: Azure 가상 네트워크 내에서 안전하게 기계 학습 실험 및 유추를 실행합니다. 모델 학습의 컴퓨팅 대상을 만드는 방법과 가상 네트워크 내에서 유추하는 방법을 알아봅니다. 인바운드 및 아웃바운드 포트 요구와 같은 보안 가상 네트워크의 요구 사항에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/08/2019
ms.openlocfilehash: a83661a63f784f62bf46ce75b8b4f47c57c87b19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819659"
---
# <a name="securely-run-experiments-and-inferencing-inside-an-azure-virtual-network"></a>Azure 가상 네트워크 내에서 안전하게 실험 및 유추 실행

이 문서에서는 가상 네트워크 내에서 실험 및 유추를 실행하는 방법을 알아봅니다. 가상 네트워크는 공용 인터넷에서 Azure 리소스를 격리하는 보안 경계의 역할을 합니다. Azure 가상 네트워크를 온-프레미스 네트워크에 연결할 수도 있습니다. 이렇게 하면 모델을 안전하게 학습하고, 배포한 모델에 액세스하여 추론을 수행할 수 있습니다.

Azure Machine Learning Service는 다른 Azure 서비스를 통해 컴퓨팅 리소스를 얻습니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 이러한 컴퓨팅 대상을 가상 네트워크 내에서 만들 수 있습니다. 예를 들어 Microsoft Data Science Virtual Machine을 사용하여 모델을 학습시킨 다음, AKS(Azure Kubernetes Service)에 모델을 배포할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 Azure 가상 네트워크 및 IP 네트워킹 일반적 잘 알고 있다고 가정 합니다. 이 문서는 또한는 만든 가상 네트워크 및 계산 리소스를 사용 하는 서브넷을 가정 합니다. Azure Virtual Network를 사용 하 여 잘 모르는 경우에 서비스에 대해 자세히 알아보려면 다음 문서를 읽어보세요.

* [IP 주소 지정](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
* [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)
* [빠른 시작: 가상 네트워크 만들기](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
* [네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="storage-account-for-your-workspace"></a>작업 영역에 대한 스토리지 계정

Azure Machine Learning 서비스 작업 영역을 만들 때는 Azure Storage 계정이 필요합니다. 이 스토리지 계정에 대해서는 방화벽 규칙을 켜지 마세요. Azure Machine Learning 서비스는 스토리지 계정에 대해 무제한 액세스 권한이 필요합니다.

관련 설정을 수정했는지 여부를 잘 모르는 경우 [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security)에서 __기본 네트워크 액세스 규칙 변경__을 참조하세요. 제시된 단계에 따라 모든 네트워크의 액세스를 허용합니다.

## <a name="use-machine-learning-compute"></a>Machine Learning 컴퓨팅 사용

가상 네트워크에서 Azure Machine Learning 컴퓨팅을 사용하려면 아래의 네트워크 요구 사항을 충족해야 합니다.

- 가상 네트워크는 Azure Machine Learning 서비스 작업 영역과 동일한 구독 및 지역에 있어야 합니다.

- Machine Learning 컴퓨팅 클러스터에 대해 지정한 서브넷에는 클러스터의 대상이 되는 VM 수를 수용할 만큼 충분한 할당되지 않은 IP 주소가 있어야 합니다. 서브넷에 충분한 할당되지 않은 IP 주소가 없으면 클러스터는 부분적으로 할당됩니다.

- 트래픽을 제한하여 가상 네트워크를 보호하려는 경우 Machine Learning 컴퓨팅 서비스를 위해 일부 포트를 열어 둡니다. 자세한 내용은 [필수 포트](#mlcports)를 참조하세요.

- 가상 네트워크 구독 또는 리소스 그룹의 보안 정책이나 잠금이 가상 네트워크 관리를 위한 사용자 권한을 제한하고 있는지 확인합니다.

- 하나의 가상 네트워크에 여러 개의 Machine Learning 컴퓨팅 클러스터를 배치하려는 경우 하나 이상의 리소스에 대해 할당량 증가를 요청해야 할 수 있습니다.

    Machine Learning 컴퓨팅은 가상 네트워크를 포함하는 리소스 그룹에 추가 네트워킹 리소스를 자동으로 할당합니다. Azure Machine Learning Service는 각 Machine Learning 컴퓨팅 클러스터에 다음 리소스를 할당합니다.

    - 하나의 NSG(네트워크 보안 그룹)

    - 공용 IP 주소 1개

    - 부하 분산 장치 1개

  이러한 리소스는 구독의 [리소스 할당량](https://docs.microsoft.com/azure/azure-subscription-service-limits)으로 제한됩니다.

### <a id="mlcports"></a> 필수 포트

Machine Learning 컴퓨팅은 현재 Azure Batch 서비스를 사용하여 지정된 가상 네트워크에 VM을 프로비전합니다. 서브넷은 Batch 서비스에서의 인바운드 통신을 허용해야 합니다. 이 통신은 Machine Learning 컴퓨팅 노드에서 실행을 예약하고 Azure Storage 및 기타 리소스와 통신하는 데 사용됩니다. Batch는 VM에 연결된 NIC(네트워크 인터페이스) 수준에서 NSG를 추가합니다. 이러한 NSG는 다음 트래픽을 허용하도록 인바운드 및 아웃바운드 규칙을 자동으로 구성합니다.

- 인바운드 TCP 트래픽을 포트 29876 및 29877에서에 __서비스 태그__ 의 __BatchNodeManagement__합니다.

    ![BatchNodeManagement 서비스 태그를 사용 하 여 인바운드 규칙을 보여 주는 Azure portal의 이미지](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)
 
- (선택 사항) 원격 액세스를 허용 하도록 22 포트에서 인바운드 TCP 트래픽을 합니다. SSH를 사용 하 여 공용 IP에서 연결 하려는 경우에 필요 합니다.
 
- 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽

- 인터넷에 대한 모든 포트의 아웃바운드 트래픽

Batch 구성 NSG에서 인바운드/아웃바운드 규칙을 수정하거나 추가할 경우 주의가 필요합니다. NSG가 컴퓨팅 노드에 대한 통신을 차단하는 경우 Machine Learning Compute 서비스는 컴퓨팅 노드의 상태를 사용 불가로 설정합니다.

Batch가 자체 NSG를 구성하므로 서브넷 수준에서 NSG를 지정할 필요는 없습니다. 그러나 지정된 서브넷에 연결된 NSG 및/또는 방화벽이 있는 경우 앞서 언급한 것처럼 인바운드 및 아웃바운드 보안 규칙을 구성합니다. 다음 스크린샷은 Azure Portal에서 규칙 구성이 표시되는 방식을 보여 줍니다.

![Machine Learning 컴퓨팅에 대한 인바운드 NSG 규칙 스크린샷](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![Machine Learning 컴퓨팅에 대한 아웃바운드 NSG 규칙 스크린샷](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="create-machine-learning-compute-in-a-virtual-network"></a>가상 네트워크에서 Machine Learning 컴퓨팅 만들기

Azure Portal을 사용하여 Machine Learning 컴퓨팅 클러스터를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning Service 작업 영역을 선택합니다.

1. __애플리케이션__ 섹션에서 __컴퓨팅__을 선택합니다. 그런 후 __컴퓨팅 추가__를 선택합니다. 

    ![Azure Machine Learning 서비스에서 컴퓨팅을 추가하는 방법](./media/how-to-enable-virtual-network/add-compute.png)

1. 가상 네트워크를 사용하도록 이 컴퓨팅 리소스를 구성하려면 다음 옵션을 사용합니다.

    - __네트워크 구성__: __고급__을 선택합니다.

    - __리소스 그룹__: 가상 네트워크를 포함하는 리소스 그룹을 선택합니다.

    - __가상 네트워크__: 서브넷을 포함하는 가상 네트워크를 선택합니다.

    - __서브넷__: 사용할 서브넷을 선택합니다.

   ![Machine learning 컴퓨팅에 대한 가상 네트워크 설정을 보여주는 스크린샷](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Azure Machine Learning SDK를 사용하여 Machine Learning 컴퓨팅 클러스터를 만들 수도 있습니다. 다음 코드에서는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 Machine Learning 컴퓨팅 클러스터를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")
    
    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name = vnet_resourcegroup_name,
                                                           vnet_name = vnet_name,
                                                           subnet_name = subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    
    # Wait for the cluster to complete, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

만들기 프로세스가 완료되면 클러스터를 사용하여 모델을 학습시킬 수 있습니다. 자세한 내용은 [학습의 컴퓨팅 대상 선택 및 사용](how-to-set-up-training-targets.md)을 참조하세요.

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>가상 머신 또는 HDInsight 클러스터 사용

작업 영역에서 가상 네트워크의 가상 머신 또는 Azure HDInsight 클러스터를 사용하려면 다음 단계를 수행합니다.

> [!IMPORTANT]
> Azure Machine Learning Service는 Ubuntu를 실행하는 가상 머신만 지원합니다.

1. Azure Portal 또는 Azure CLI를 사용하여 VM 또는 HDInsight 클러스터를 만든 후 Azure 가상 네트워크에 배치합니다. 자세한 내용은 다음 문서를 참조하세요.
    * [Linux VM용 Azure 가상 네트워크 만들기 및 관리](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Azure 가상 네트워크를 사용하여 HDInsight 확장](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network) 

1. Azure Machine Learning Service가 VM 또는 클러스터에서 SSH 포트와 통신할 수 있도록 하려면 NSG에 대한 원본 항목을 구성해야 합니다. SSH 포트는 일반적으로 포트 22입니다. 이 원본의 트래픽을 허용하려면 다음 정보를 사용합니다.

    * __원본__: __서비스 태그__를 선택합니다.

    * __원본 서비스 태그__: __AzureMachineLearning__을 선택합니다.

    * __원본 포트 범위__: __*__ 를 선택합니다.

    * __대상__: __모두__를 선택합니다.

    * __대상 포트 범위__: __22__를 선택합니다.

    * __프로토콜__: __모두__를 선택합니다.

    * __작업__: __허용__을 선택합니다.

   ![가상 네트워크 내의 VM 또는 HDInsight 클러스터에 대해 실험을 수행하기 위한 인바운드 규칙 스크린샷](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.
    
1. VM 또는 HDInsight 클러스터를 Azure Machine Learning 서비스 작업 영역에 연결합니다. 자세한 내용은 [모델 학습의 컴퓨팅 대상 설정](how-to-set-up-training-targets.md)을 참조하세요.

## <a name="use-azure-kubernetes-service"></a>Azure Kubernetes Service 사용

> [!IMPORTANT]
> 단계를 진행하기 전에 필수 조건을 확인하고 클러스터의 IP 주소 지정을 계획합니다. 자세한 내용은 [Azure Kubernetes Service에서 고급 네트워킹 구성](https://docs.microsoft.com/azure/aks/configure-advanced-networking)을 참조하세요.
> 
> NSG에 대한 기본 아웃바운드 규칙을 유지합니다. 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)의 기본 보안 규칙을 참조하세요.
>
> Azure Kubernetes Service 및 Azure 가상 네트워크는 동일한 지역에 있어야 합니다.

가상 네트워크의 Azure Kubernetes Service를 작업 영역에 추가하려면 Azure Portal에서 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning Service 작업 영역을 선택합니다.

1. __애플리케이션__ 섹션에서 __컴퓨팅__을 선택합니다. 그런 후 __컴퓨팅 추가__를 선택합니다. 

    ![Azure Machine Learning 서비스에서 컴퓨팅을 추가하는 방법](./media/how-to-enable-virtual-network/add-compute.png)

1. 가상 네트워크를 사용하도록 이 컴퓨팅 리소스를 구성하려면 다음 옵션을 사용합니다.

    - __네트워크 구성__: __고급__을 선택합니다.

    - __리소스 그룹__: 가상 네트워크를 포함하는 리소스 그룹을 선택합니다.

    - __가상 네트워크__: 서브넷을 포함하는 가상 네트워크를 선택합니다.

    - __서브넷__: 서브넷을 선택합니다.

    - __Kubernetes 서비스 주소 범위__: Kubernetes 서비스 주소 범위를 선택합니다. 이 주소 범위는 CIDR 표기법 IP 범위를 사용하여 클러스터에 사용할 수 있는 IP 주소를 정의합니다. 서브넷 IP 범위와 겹치지 않아야 합니다. 예를 들면 다음과 같습니다. 10.0.0.0/16.

    - __Kubernetes DNS 서비스 IP 주소__: Kubernetes DNS 서비스 IP 주소를 선택합니다. 이 IP 주소는 Kubernetes DNS 서비스에 할당됩니다. 이 주소는 Kubernetes 서비스 주소 범위에 속해야 합니다. 예를 들면 다음과 같습니다. 10.0.0.10.

    - __Docker 브리지 주소__: Docker 브리지 주소를 선택합니다. 이 IP 주소는 Docker 브리지에 할당됩니다. 서브넷 IP 범위 또는 Kubernetes 서비스 주소 범위에 속하지 않아야 합니다. 예를 들면 다음과 같습니다. 172.17.0.1/16.

   ![Azure Machine Learning 서비스: Machine Learning 컴퓨팅 가상 네트워크 설정](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

    > [!TIP]
    > 가상 네트워크에 AKS 클러스터가 이미 있는 경우 작업 영역에 연결할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-to-aks.md)을 참조하세요.

**Azure Machine Learning SDK**를 사용하여 가상 네트워크에 Azure Kubernetes Service를 추가할 수도 있습니다. 다음 코드에서는 `mynetwork`라는 가상 네트워크의 `default` 서브넷에 새 Azure Kubernetes Service 인스턴스를 만듭니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace = ws,
                                  name = "myaks",
                                  provisioning_configuration = config)
```

만들기 프로세스가 완료되면 가상 네트워크에 가려져 있는 AKS 클러스터에 대해 추론을 수행할 수 있습니다. 자세한 내용은 [AKS에 배포하는 방법](how-to-deploy-to-aks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [학습 환경 설정](how-to-set-up-training-targets.md)
* [모델 배포 위치](how-to-deploy-and-where.md)
* [SSL을 사용하여 안전하게 모델 배포](how-to-secure-web-service.md)


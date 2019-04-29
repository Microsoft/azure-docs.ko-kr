---
title: 가상 머신 계산 노드에서 Linux 실행 - Azure Batch | Microsoft Docs
description: Azure Batch의 Linux 가상 머신 풀에서 병렬 계산 워크로드를 처리하는 방법에 대해 알아봅니다.
services: batch
documentationcenter: python
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e228e73283685988247c8d419ba0a97b8c7b2974
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776155"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Batch 풀에서 Linux 계산 노드 프로비전

Azure Batch를 사용하여 Linux 및 Windows 가상 머신에서 병렬 계산 워크로드를 실행할 수 있습니다. 이 문서에서는 [Batch Python][py_batch_package] 및 [Batch .NET][api_net] 클라이언트 라이브러리를 모두 사용하여 Batch 서비스에서 Linux 계산 노드 풀을 만드는 방법에 대해 자세히 설명합니다.

> [!NOTE]
> 애플리케이션 패키지는 2017년 7월 5일 이후에 만들어진 모든 Batch 풀에서 지원됩니다. 2016년 3월 10일에서 2017년 7월 5일 사이에 만들어진 Batch 풀에서는 Cloud Service 구성을 사용하여 풀을 만든 경우에만 이러한 패키지가 지원됩니다. 2016년 3월 10일 이전에 만들어진 Batch 풀은 애플리케이션 패키지를 지원하지 않습니다. 애플리케이션 패키지를 사용하여 Batch 노드에 애플리케이션을 배포하는 방법에 대한 자세한 내용은 [Batch 애플리케이션 패키지를 사용하여 계산 노드에 애플리케이션 배포](batch-application-packages.md)를 참조하세요.
>
>

## <a name="virtual-machine-configuration"></a>가상 머신 구성
Batch에서 컴퓨팅 노드 풀을 만들 때는 노드 크기와 운영 체제를 선택할 수 있는 두 가지 옵션인 Cloud Services 구성 및 Virtual Machine 구성이 있습니다.

**Cloud Services 구성**은 Windows 계산 노드 *만*제공합니다. 사용 가능한 계산 노드 크기는 [Cloud Services 크기](../cloud-services/cloud-services-sizes-specs.md)에 나열되고 사용 가능한 운영 체제는 [Azure 게스트 OS 릴리스 및 SDK 호환성 매트릭스](../cloud-services/cloud-services-guestos-update-matrix.md)에 나열됩니다. Azure Cloud Services 노드를 포함하는 풀을 만드는 경우 노드 크기 및 OS 제품군을 지정하며, 앞에서 언급한 문서에 설명되어 있습니다. Windows 계산 노드의 풀에는 Cloud Services가 가장 일반적으로 사용됩니다.

**Virtual Machine 구성**은 계산 노드에 대한 Linux와 Windows 이미지를 제공합니다. 사용 가능한 계산 노드 크기는 [Azure에서 가상 머신에 대한 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(Linux) 및 [Azure에서 가상 머신에 대한 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Windows)에 나열되어 있습니다. Virtual Machine 구성 노드를 포함하는 풀을 만들 때 노드 크기, 가상 머신 이미지 참조 및 노드에 설치할 Batch 노드 에이전트 SKU도 지정해야 합니다.

### <a name="virtual-machine-image-reference"></a>가상 머신 이미지 참조
Batch 서비스는 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 사용하여 가상 머신 구성에서 계산 노드를 제공합니다. [Azure Marketplace][vm_marketplace]의 이미지를 지정하거나 준비한 사용자 지정 이미지를 제공할 수 있습니다. 사용자 지정 이미지에 대한 자세한 내용은 [사용자 지정 이미지로 풀 만들기](batch-custom-images.md)를 참조하세요.

가상 머신 이미지 참조를 구성할 때 가상 머신 이미지의 속성을 지정합니다. 가상 머신 이미지 참조를 만들 때 다음 속성이 필요합니다.

| **이미지 참조 속성** | **예제** |
| --- | --- |
| 게시자 |Canonical |
| 제안 |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |최신 |

> [!TIP]
> 이러한 속성 및 Marketplace 이미지를 나열하는 방법에 대한 자세한 내용은 [CLI 또는 PowerShell로 Azure의 Linux 가상 머신 이미지 이동 및 선택](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 알아볼 수 있습니다. 일부 Marketplace 이미지가 현재 Batch와 호환되지 않습니다. 자세한 내용은 [노드 에이전트 SKU](#node-agent-sku)를 참조하세요.
>
>

### <a name="node-agent-sku"></a>노드 에이전트 SKU
Batch 노드 에이전트는 풀의 각 노드에서 실행되고 노드와 Batch 서비스 간의 명령 및 컨트롤 인터페이스를 제공하는 프로그램입니다. SKU라고 하는 노드 에이전트의 구현은 서로 다른 운영 체제에 대해 여러 가지가 있습니다. 기본적으로 Virtual Machine 구성을 만들 때 먼저 가상 머신 이미지 참조를 지정한 다음 이미지에 설치할 노드 에이전트를 지정합니다. 일반적으로 각 노드 에이전트 SKU는 여러 가상 머신 이미지와 호환됩니다. 다음은 노드 에이전트 SKU의 몇 가지 예입니다.

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Marketplace에서 사용 가능한 일부 가상 머신 이미지가 현재 사용 가능한 Batch 노드 에이전트와 호환되지 않습니다. Batch SDK를 사용하여 사용 가능한 노드 에이전트 SKU 및 호환되는 가상 머신 이미지를 나열합니다. 런타임에 유효한 이미지 목록을 검색하는 방법에 대한 자세한 내용 및 예제는 이 문서의 뒷부분에 있는 [ 이미지 목록](#list-of-virtual-machine-images)을 참조하세요.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Linux 풀 만들기: Batch Python
다음 코드 조각은 [Python용 Microsoft Azure Batch 클라이언트 라이브러리][py_batch_package]를 사용하여 Ubuntu Server 계산 노드의 풀을 만드는 방법의 예를 보여 줍니다. Batch Python 모듈에 대한 참조 설명서는 Read the Docs의 [azure.batch package][py_batch_docs]에서 확인할 수 있습니다.

이 코드 조각은 명시적으로 [ImageReference][py_imagereference]를 만들고 각 속성(게시자, 제품, SKU, 버전)을 지정합니다. 그러나 프로덕션 코드에서는 [list_node_agent_skus][py_list_skus]를 사용하여 런타임 시 사용 가능한 이미지 및 노드 에이전트 SKU 조합을 결정하고 선택하는 것이 좋습니다.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

이전에 언급한 대로 [ImageReference][py_imagereference]를 명시적으로 만드는 대신 현재 지원되는 노드 에이전트/Marketplace 이미지 조합에서 동적으로 선택하는 [list_node_agent_skus][py_list_skus] 메서드를 사용하는 것이 좋습니다. 다음 Python 코드 조각에서는 이 메서드의 사용 방법을 보여 줍니다.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>Linux 풀 만들기: Batch .NET
다음 코드 조각은 [Batch .NET][nuget_batch_net] 클라이언트 라이브러리를 사용하여 Ubuntu Server 계산 노드의 풀을 만드는 방법의 예를 보여 줍니다. docs.microsoft.com에서 [Batch .NET 참조 설명서][api_net]를 찾을 수 있습니다.

다음 코드 조각에서는 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 메서드를 사용하여 현재 지원되는 Marketplace 이미지 및 노드 에이전트 SKU 조합의 목록에서 선택합니다. 지원되는 조합 목록이 언제든지 바뀔 수 있으므로 이 기술이 바람직합니다. 가장 일반적으로 지원되는 조합을 추가합니다.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

이전의 코드 조각은 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 메서드를 사용하여 동적으로 나열하고 지원되는 이미지와 노드 에이전트 SKU 조합에서 선택(권장)하지만, [ImageReference][net_imagereference]를 명시적으로 구성할 수도 있습니다.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>가상 머신 이미지 목록
다음 표에는 이 문서가 마지막으로 업데이트되었을 때 사용 가능한 Batch 노드 에이전트와 호환되는 Marketplace 가상 머신 이미지가 나열되어 있습니다. 이미지와 노드 에이전트는 언제든지 추가 또는 제거될 수 있기 때문에 이 목록은 확정적이지 않습니다. Batch 애플리케이션 및 서비스에서는 현재 사용 가능한 SKU를 확인하고 선택하는[list_node_agent_skus][py_list_skus](Python) 또는 [ListNodeAgentSkus][net_list_skus](Batch .NET)를 항상 사용하는 것이 좋습니다.

> [!WARNING]
> 다음 목록은 언제든지 변경될 수 있습니다. 항상 Batch API에서 사용 가능한 **list node agent SKU** 메서드를 사용하여 Batch 작업 실행 시 호환되는 가상 머신 및 노드 에이전트 SKU를 나열합니다.
>
>

| **게시자** | **제안** | **이미지 SKU** | **버전** | **노드 에이전트 SKU ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| 일괄 처리 | rendering-centos73 | 렌더링 | 최신 | batch.node.centos 7 |
| 일괄 처리 | rendering-windows2016 | 렌더링 | 최신 | batch.node.windows amd64 |
| Canonical | UbuntuServer | 16.04-LTS | 최신 | batch.node.ubuntu 16.04 |
| Canonical | UbuntuServer | 14.04.5-LTS | 최신 | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | 최신 | batch.node.debian 9 |
| Credativ | Debian | 8 | 최신 | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | 최신 | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | 최신 | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | 최신 | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | 최신 | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | 최신 | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | 최신 | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | 최신 | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | 최신 | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | 최신 | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | 최신 | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | 최신 | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | 최신 | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | 최신 | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | 최신 | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>SSH를 사용하여 Linux 노드에 연결
개발 또는 문제 해결 동안 풀의 노드에 로그인할 필요가 있을 수 있습니다. Windows 계산 노드와 달리 Linux 노드에 연결하기 위해 RDP(원격 데스크톱 프로토콜)를 사용할 수 없습니다. 대신, Batch 서비스는 원격 연결을 위해 각 노드에서 SSH 액세스를 사용하도록 설정합니다.

다음 Python 코드 조각에서는 풀의 각 노드에서 사용자를 만들며 이는 원격 연결에 필요합니다. 그런 다음 각 노드에 대한 SSH(secure shell) 연결 정보를 인쇄합니다.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
        credentials,
        base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

다음은 4개의 Linux 노드를 포함하는 풀에 대한 이전 코드의 샘플 출력입니다.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

노드에 사용자를 만들 때 암호 대신 SSH 공개 키를 지정할 수 있습니다. Python SDK에서는 [ComputeNodeUser][py_computenodeuser]에 **ssh_public_key** 매개 변수를 사용합니다. .NET에서는 [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key] 속성을 사용합니다.

## <a name="pricing"></a>가격
Azure Batch는 Azure Cloud Services 및 Azure Virtual Machines 기술을 기반으로 빌드됩니다. Batch 서비스 자체는 무료로 제공됩니다. 즉, Batch 솔루션에서 사용하는 계산 리소스에 대해서만 요금이 청구됩니다. **Cloud Services 구성**을 선택하는 경우 [Cloud Services 가격][cloud_services_pricing] 구조에 따라 요금이 청구됩니다. **Virtual Machine 구성**을 선택하는 경우 [Virtual Machines 가격][vm_pricing] 구조에 따라 요금이 청구됩니다. 

[애플리케이션 패키지](batch-application-packages.md)를 사용하여 Batch 노드에 애플리케이션을 배포하는 경우에도 애플리케이션 패키지에서 사용하는 Azure Storage 리소스에 대한 요금이 청구됩니다. 일반적으로 Azure Storage 비용은 최소입니다. 

## <a name="next-steps"></a>다음 단계

GitHub의 [azure-batch-samples][github_samples] 리포지토리에 있는 [Python 코드 샘플][github_samples_py]에는 풀, 작업 및 태스크 만들기와 같은 일반적인 Batch 작업 수행 방법을 보여 주는 스크립트가 포함되어 있습니다. Python 샘플과 함께 제공되는 [추가 정보][github_py_readme]에는 필요한 패키지를 설치하는 방법에 대한 세부 정보가 있습니다.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: https://docs.microsoft.com/python/api/azure.batch.models.computenodeuser
[py_imagereference]: https://docs.microsoft.com/python/api/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

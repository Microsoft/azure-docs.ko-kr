---
title: 컨테이너 워크로드 - Azure Batch | Microsoft Docs
description: Azure Batch에서 컨테이너 이미지의 애플리케이션을 실행하는 방법을 알아봅니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 11/19/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: cfd00ad124db33cec8e30e8e1bb701388ee71838
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340236"
---
# <a name="run-container-applications-on-azure-batch"></a>Azure Batch에서 컨테이너 애플리케이션 실행

Azure Batch를 사용하면 Azure에서 많은 수의 일괄 처리 계산 작업을 실행하고 확장할 수 있습니다. Batch 작업은 Batch 풀의 가상 머신(노드)에서 직접 실행할 수 있지만 노드에서 Docker 호환 컨테이너의 작업을 실행하도록 Batch 풀을 설정할 수도 있습니다. 이 문서에서는 컨테이너 작업의 실행을 지원하는 계산 노드 풀을 만드는 방법과 풀에서 컨테이너 작업을 실행하는 방법을 보여 줍니다. 

컨테이너 개념과 Batch 풀 및 Batch 작업을 만드는 방법을 잘 알고 있어야 합니다. 코드 예제는 Batch .NET 및 Python SDK를 사용합니다. Azure Portal을 포함하여 다른 Batch SDK 및 도구를 사용하여 컨테이너 사용이 가능한 Batch 풀을 만들고 컨테이너 작업을 실행할 수도 있습니다.

## <a name="why-use-containers"></a>컨테이너를 사용하는 이유

컨테이너를 사용하면 환경 및 종속 파일을 관리할 필요없이 Batch 작업을 실행하여 애플리케이션을 쉽게 실행할 수 있습니다. 컨테이너는 애플리케이션을 다양한 환경에서 실행될 수 있는 이식이 가능한 일체형 경량 단위로 배포합니다. 예를 들어, 컨테이너를 로컬로 빌드 및 테스트한 다음, Azure의 레지스트리 또는 다른 위치로 컨테이너 이미지를 업로드할 수 있습니다. 컨테이너 배포 모델은 애플리케이션을 호스트하는 어느 곳이든 애플리케이션의 런타임 환경이 항상 올바르게 설치 및 구성되도록 합니다. Batch의 컨테이너 기반 작업은 리소스 파일 및 출력 파일 관리, 애플리케이션 패키지 등 컨테이너가 아닌 작업의 기능을 활용할 수도 있습니다. 

## <a name="prerequisites"></a>필수 조건

* **SDK 버전**: Batch SDK에서 지원하는 컨테이너 이미지의 버전은 다음과 같습니다.
    * Batch REST API 버전 2017-09-01.6.0
    * Batch .NET SDK 버전 8.0.0
    * Batch Python SDK 버전 4.0
    * Batch Java SDK 버전 3.0
    * Batch Node.js SDK 버전 3.0

* **계정**: Azure 구독에서 배치 계정을 만들고, 필요에 따라 Azure Storage 계정도 만들어야 합니다.

* **지원되는 VM 이미지**: 컨테이너는 다음 섹션, “지원되는 가상 머신 이미지”에 자세히 설명된 이미지의 Virtual Machine 구성으로 만든 풀에서만 지원됩니다. 사용자 지정 이미지를 제공하는 경우 다음 섹션의 고려 사항 및 [관리되는 사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](batch-custom-images.md)의 요구 사항을 참조하세요. 

### <a name="limitations"></a>제한 사항

* Batch는 Linux 풀에서 실행되는 컨테이너에 대한 RDMA 지원만 제공합니다.

* Windows 컨테이너 워크로드의 경우, 풀에 대한 멀티 코어 VM 크기를 선택하는 것이 좋습니다.

## <a name="supported-virtual-machine-images"></a>지원되는 가상 머신 이미지

컨테이너 워크로드에 대한 VM 계산 노드 풀을 만들려면 다음 지원되는 Windows 또는 Linux 이미지 중 하나를 사용합니다. Batch와 호환되는 Marketplace 이미지에 대한 자세한 내용은 [가상 머신 이미지 목록](batch-linux-nodes.md#list-of-virtual-machine-images)을 참조하세요. 

### <a name="windows-images"></a>Windows 이미지

Windows 컨테이너 워크로드의 경우 현재 Batch는 Azure Marketplace의 **Windows Server 2016 Datacenter(컨테이너 포함)** 이미지를 지원합니다. Docker 컨테이너 이미지만 Windows에서 지원됩니다.

Windows에서 Docker를 실행하는 VM에서 사용자 지정 이미지를 만들 수도 있습니다.

### <a name="linux-images"></a>Linux 이미지

Linux 컨테이너 워크로드의 경우 현재 Batch는 Azure Marketplace의 Microsoft Azure Batch에 의해 게시된 다음 Linux 이미지를 지원합니다.

* **Azure Batch 컨테이너 풀용 CentOS**

* **Azure Batch 컨테이너 풀용 CentOS(RDMA 드라이버 사용)**

* **Azure Batch 컨테이너 풀용 Ubuntu Server**

* **Azure Batch 컨테이너 풀용 Ubuntu Server(RDMA 드라이버 사용)**

이러한 이미지는 Azure Batch 풀에서만 사용할 수 있습니다. 특징은 다음과 같습니다.

* 사전 설치된 [Moby](https://github.com/moby/moby) 컨테이너 런타임 

* Azure N-시리즈 VM의 배포를 간소화하기 위해 사전 설치된 NVIDIA GPU 드라이버

* 사전 설치된 RDMA 드라이버를 사용 또는 사용하지 않는 이미지. 이렇나 드라이버는 RDMA 가능 VM 크기에 배포될 경우 Azure RDMA 네트워크에 액세스하도록 풀 노드를 허용합니다. 

Batch와 호환되는 Linux 배포판 중 하나에서 Docker를 실행하는 VM에서 사용자 지정 이미지를 만들 수도 있습니다. 자체 사용자 지정 Linux 이미지를 제공하려는 경우 [관리되는 사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](batch-custom-images.md)의 지침을 참조하세요.

사용자 지정 이미지의 Docker 지원을 위해 [Docker CE(Community Edition)](https://www.docker.com/community-edition) 또는 [Docker EE(Enterprise Edition)](https://www.docker.com/enterprise-edition)를 설치합니다.

사용자 지정 Linux 이미지를 사용하기 위한 추가 고려 사항:

* 사용자 지정 이미지 사용 시 Azure N-시리즈 크기의 GPU 성능을 활용하려면 NVIDIA 드라이버를 사전 설치합니다. 또한 NVIDIA GPU용 Docker 엔진 유틸리티인 [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)를 설치해야 합니다.

* Azure RDMA 네트워크에 액세스하려면 RDMA 가능 VM 크기를 사용합니다. 필수 RDMA 드라이버는 Batch에서 지원되는 CentOS HPC 및 Ubuntu 이미지에 설치됩니다. MPI 워크로드를 실행하기 위한 추가 구성이 필요할 수 있습니다. [Batch 풀에서 RDMA 가능 또는 GPU 가능 인스턴스 사용](batch-pool-compute-intensive-sizes.md)을 참조하세요.


## <a name="container-configuration-for-batch-pool"></a>Batch 풀에 대한 컨테이너 구성

Batch 풀을 사용하여 컨테이너 워크로드를 실행하려면 해당 풀의 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 개체에서 [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 설정을 지정해야 합니다. (이 문서는 Batch .NET API 참조에 대한 링크를 제공합니다. 해당 설정은 [Batch Python](/python/api/azure.batch) API에 있습니다.)

다음 예제와 같이 프리페치된 컨테이너 이미지를 사용하거나 사용하지 않고 컨테이너 지원 풀을 만들 수 있습니다. 끌어오기(또는 프리페치) 프로세스를 사용하면 Docker 허브 또는 인터넷의 다른 컨테이너 레지스트리에서 컨테이너 이미지를 미리 로드할 수 있습니다. 최상의 성능을 위해 Batch 계정과 동일한 영역의 [Azure Container Registry](../container-registry/container-registry-intro.md)를 사용합니다.

컨테이너 이미지를 프리페치할 때의 장점은 작업을 처음 실행하기 시작할 때 컨테이너 이미지가 다운로드될 때까지 기다릴 필요가 없다는 것입니다. 컨테이너 구성은 풀이 생성될 때 컨테이너 이미지를 VM으로 끌어옵니다. 그러면 풀에서 실행되는 작업은 컨테이너 이미지 및 컨테이너 실행 옵션 목록을 참조할 수 있습니다.


### <a name="pool-without-prefetched-container-images"></a>프리페치된 컨테이너 이미지 없는 풀

프리페치된 컨테이너 이미지 없이 컨테이너 지원 풀을 구성하려면 다음 Python 예제와 같이 `ContainerConfiguration` 및 `VirtualMachineConfiguration` 개체를 정의합니다. 이 예제에서는 Marketplace에서 Azure Batch 컨테이너 풀 이미지의 Ubuntu Server를 사용합니다.


```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>컨테이너 구성을 위해 이미지 프리페치

컨테이너 이미지를 풀에 프리페치하려면 컨테이너 이미지 목록(`container_image_names`, Python에서)을 `ContainerConfiguration`에 추가합니다. 

다음 기본 Python 예제는 [Docker 허브](https://hub.docker.com)에서 표준 Ubuntu 컨테이너 이미지를 프리페치하는 방법을 보여 줍니다.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub. 
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


다음 C# 예제에서는 [Docker 허브](https://hub.docker.com)에서 TensorFlow 이미지를 프리페치한다고 가정합니다. 이 예에는 풀 노드의 VM 호스트에서 실행되는 시작 작업이 포함되어 있습니다. 예를 들어 컨테이너에서 액세스할 수 있는 파일 서버를 탑재하려는 경우 호스트에서 시작 작업을 실행할 수 있습니다.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>프라이빗 컨테이너 레지스트리에서 이미지 프리페치

프라이빗 컨테이너 레지스트리 서버에서 인증을 받아 컨테이너 이미지를 프리페치할 수도 있습니다. 다음 예제에서는 `ContainerConfiguration` 및 `VirtualMachineConfiguration` 개체가 프라이빗 Azure Container Registry에서 프라이빗 TensorFlow 이미지를 프리페치한다고 가정합니다. 이미지 참조는 앞의 예와 동일합니다.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>작업(task)에 대한 컨테이너 설정

컨테이너 사용 풀에서 컨테이너 작업을 실행하려면 컨테이너별 설정을 지정합니다. 설정에는 사용할 이미지, 레지스트리 및 컨테이너 실행 옵션이 포함됩니다.

* 작업 클래스의 `ContainerSettings` 속성을 사용하여 컨테이너별 설정을 구성합니다. 이러한 설정은 [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) 클래스에 의해 정의됩니다.

* 컨테이너 이미지에 대해 작업(task)를 실행하는 경우 [클라우드 작업(task)](/dotnet/api/microsoft.azure.batch.cloudtask) 및 [작업(Job) 관리자 작업(task)](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)에 컨테이너 설정이 필요합니다. 그러나 [시작 태스크](/dotnet/api/microsoft.azure.batch.starttask), [작업(Job) 준비 작업(task)](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) 및 [작업(Job) 관리자 작업(task)](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)에는 컨테이너 설정이 필요하지 않습니다(즉, 컨테이너 컨텍스트 내에서 또는 노드에서 직접 실행될 수 있음).

### <a name="container-task-command-line"></a>컨테이너 작업 명령줄

컨테이너 작업을 실행하면 Batch에서 [docker create](https://docs.docker.com/engine/reference/commandline/create/) 명령을 사용하여 작업에 지정된 이미지를 사용하여 컨테이너를 생성합니다. 그런 다음, Batch는 컨테이너의 작업 실행을 제어합니다. 

컨테이너가 아닌 Batch 작업을 사용하여 컨테이너 작업에 대한 명령줄을 설정합니다. Batch에서 자동으로 컨테이너를 만들기 때문에 명령줄은 컨테이너에서 실행되는 명령만 지정합니다.

Batch 작업에 대한 컨테이너 이미지가 [ENTRYPOINT](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) 스크립트를 통해 구성되면 기본 ENTRYPOINT를 사용하거나 재정의하도록 명령줄을 설정할 수 있습니다. 

* 컨테이너 이미지의 기본 ENTRYPOINT를 사용하려면 작업 명령줄을 빈 문자열 `""`로 설정합니다.

* 기본 ENTRYPOINT를 재정의하려는 경우 또는 이미지에 ENTRYPOINT가 없는 경우 컨테이너에 적절한 명령줄을 설정합니다(예: `/app/myapp` 또는 `/bin/sh -c python myscript.py`).

선택적 [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions)는 Batch가 컨테이너를 만들고 실행하는 데 사용하는 `docker create` 명령에 제공하는 추가 인수입니다. 예를 들어 컨테이너에 대한 작업 디렉터리를 설정하려면 `--workdir <directory>` 옵션을 설정합니다. 추가 옵션에 대한 [docker create](https://docs.docker.com/engine/reference/commandline/create/) 참조를 확인하세요.

### <a name="container-task-working-directory"></a>컨테이너 작업의 작업 디렉터리

Batch 컨테이너 작업은 Batch가 일반(컨테이너 아님) 작업에 대해 설정하는 디렉터리와 매우 유사한 컨테이너의 작업 디렉터리에서 실행됩니다. 이 작업 디렉터리는 이미지에 구성된 경우 [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) 또는 기본 컨테이너 작업 디렉터리(Windows 컨테이너의 경우 `C:\` 또는 Linux 컨테이너의 경우 `/`)와 다릅니다. 

Batch 컨테이너 작업의 경우:

* 호스트 노드의 `AZ_BATCH_NODE_ROOT_DIR`(Azure Batch 디렉터리의 루트)의 모든 하위 디렉터리는 컨테이너에 매핑됩니다.
* 모든 작업 환경 변수는 컨테이너에 매핑됩니다.
* 노드의 작업 디렉터리 `AZ_BATCH_TASK_WORKING_DIR`은 일반 작업과 동일하게 설정되며 컨테이너에 매핑됩니다. 

이러한 매핑을 통해 컨테이너 작업이 아닌 작업과 동일한 방식으로 작업할 수 있습니다. 예를 들어, 애플리케이션 패키지를 사용하여 애플리케이션을 설치하고, Azure Storage에서 리소스 파일에 액세스하고, 작업 환경 설정을 사용하고, 컨테이너를 중지한 후 작업 출력 파일을 유지할 수 있습니다.

### <a name="troubleshoot-container-tasks"></a>컨테이너 작업 문제 해결

컨테이너 작업이 예상대로 실행되지 않으면 컨테이너 이미지의 WORKDIR 또는 ENTRYPOINT 구성에 대한 정보를 가져와야 할 수도 있습니다. 구성을 확인하려면 [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/) 명령을 실행합니다. 

필요한 경우에 이미지를 기반으로 하는 컨테이너 작업의 설정을 조정합니다.

* 작업 명령줄의 절대 경로를 지정합니다. 이미지의 기본 ENTRYPOINT가 작업 명령줄에 사용되는 경우 절대 경로가 설정되어 있는지 확인합니다.

* 작업의 컨테이너 실행 옵션에서 이미지의 WORKDIR에 일치하도록 작업 디렉터리를 변경합니다. 예를 들어 `--workdir /app`으로 설정합니다.

## <a name="container-task-examples"></a>컨테이너 작업 예제

다음 Python 코드 조각은 Docker 허브에서 가져온 가상의 이미지에서 생성된 컨테이너에서 실행되는 기본 명령줄을 보여줍니다. 여기서 `--rm` 컨테이너 옵션은 작업이 완료된 후 컨테이너를 제거하며, `--workdir` 옵션은 작업 디렉터리를 설정합니다. 명령줄은 호스트의 작업 디렉터리에 작은 파일을 기록하는 간단한 셸 명령을 사용하여 컨테이너 ENTRYPOINT를 재정의합니다. 

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

다음 C# 예제에서는 클라우드 작업에 대한 기본 컨테이너 설정을 보여 줍니다.

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>다음 단계

* [Shipyard recipes](https://github.com/Azure/batch-shipyard/tree/master/recipes)를 통해 Azure Batch에 컨테이너 작업을 쉽게 배포할 수 있는 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 도구 키트도 참조하세요.

* Linux에서 Docker CE를 설치 및 사용하는 방법에 대한 자세한 내용은 [Docker](https://docs.docker.com/engine/installation/) 설명서를 참조하세요.

* 사용자 지정 이미지 사용에 대한 자세한 내용은 [관리형 사용자 지정 이미지를 사용하여 가상 머신 풀 만들기](batch-custom-images.md)를 참조하세요.

* 컨테이너 기반 시스템을 만들기 위한 프레임워크인 [Moby 프로젝트](https://mobyproject.org/)에 대해 자세히 알아봅니다.
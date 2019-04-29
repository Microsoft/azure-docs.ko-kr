---
title: 사용자 계정으로 작업 실행 - Azure Batch | Microsoft Docs
description: Azure Batch에서 태스크를 실행하기 위해 사용자 계정 구성
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 000495ab84990f15885c254b472be7863c75da58
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549855"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Batch에서 사용자 계정으로 태스크 실행

Azure Batch의 태스크는 항상 사용자 계정으로 실행됩니다. 기본적으로 태스크는 관리자 권한 없이 표준 사용자 계정으로 실행됩니다. 일반적으로는 이러한 기본 사용자 계정 설정만으로 충분합니다. 그러나 특정 시나리오에서는 태스크를 실행하려는 사용자 계정을 구성하는 것이 유용할 수 있습니다. 이 문서에서는 사용자 계정의 유형 및 시나리오에 맞게 구성하는 방법을 설명합니다.

## <a name="types-of-user-accounts"></a>사용자 계정의 유형

Azure Batch에서는 태스크 실행을 위해 다음과 같은 두 가지 유형의 사용자 계정을 제공합니다.

- **자동 사용자 계정.** 자동 사용자 계정은 Batch 서비스에 의해 자동으로 생성되는 기본 제공 사용자 계정입니다. 기본적으로 태스크는 자동 사용자 계정에서 실행됩니다. 태스크가 실행될 자동 사용자 계정을 나타내도록 태스크에 대한 자동 사용자 지정을 구성할 수 있습니다. 자동 사용자 지정을 사용하면 태스크를 실행할 자동 사용자 계정의 권한 상승 수준 및 범위를 지정할 수 있습니다. 

- **명명된 사용자 계정.** 풀을 만들 때 풀에 대해 하나 이상의 명명된 사용자 계정을 지정할 수 있습니다. 각 사용자 계정은 풀의 각 노드에서 생성됩니다. 계정 이름 외에도 계정 암호, 권한 상승 수준을 지정할 수 있으며, Linux 풀의 경우에는 SSH 개인 키도 지정할 수 있습니다. 태스크를 추가할 때 해당 태스크를 실행할 명명된 사용자 계정을 지정할 수 있습니다.

> [!IMPORTANT] 
> Batch 서비스 버전 2017-01-01.4.0에서는 해당 버전을 호출하기 위해 코드를 업데이트해야 하는 주요 변경 내용이 추가되었습니다. 이전 버전의 Batch에서 코드를 마이그레이션하는 경우 REST API 또는 Batch 클라이언트 라이브러리에서 **runElevated** 속성이 더 이상 지원되지 않습니다. 태스크의 새로운 **userIdentity** 속성을 사용하여 권한 상승 수준을 지정합니다. 클라이언트 라이브러리 중 하나를 사용하는 경우 Batch 코드를 업데이트하기 위한 빠른 지침을 보려면 [최신 Batch 클라이언트 라이브러리로 코드 업데이트](#update-your-code-to-the-latest-batch-client-library) 섹션을 참조하세요.
>
>

> [!NOTE] 
> 이 문서에서 설명하는 사용자 계정은 보안상의 이유로 RDP(원격 데스크톱 프로토콜) 또는 SSH(보안 셸)을 지원하지 않습니다. 
>
> SSH를 통해 Linux 가상 머신 구성을 실행하는 노드에 연결하려면 [Azure에서 Linux VM에 대해 원격 데스크톱 사용](../virtual-machines/virtual-machines-linux-use-remote-desktop.md)을 참조하세요. RDP를 통해 Windows를 실행하는 노드에 연결하려면 [Windows Server VM에 연결](../virtual-machines/windows/connect-logon.md)을 참조하세요.<br /><br />
> RDP를 통해 클라우드 서비스 구성을 실행하는 노드에 연결하려면 [Azure Cloud Services의 역할에 대해 원격 데스크톱 연결 사용](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)을 참조하세요.
>
>

## <a name="user-account-access-to-files-and-directories"></a>파일 및 디렉터리에 대한 사용자 계정 액세스

자동 사용자 계정 및 명명된 사용자 계정 둘 다 태스크의 작업 디렉터리, 공유 디렉터리 및 다중 인스턴스 태스크 디렉터리에 대해 읽기/쓰기 액세스 권한을 갖습니다. 두 가지 유형의 계정 모두 시작 및 작업 준비 디렉터리에 대해 읽기 권한을 갖습니다.

태스크가 시작 태스크를 실행하는 데 사용된 것과 동일한 계정에서 실행되는 경우 태스크에는 시작 태스크 디렉터리에 대한 읽기/쓰기 액세스 권한이 있습니다. 마찬가지로 태스크가 작업 준비 태스크를 실행하는 데 사용된 것과 동일한 계정에서 실행되는 경우 태스크에는 작업 준비 태스크 디렉터리에 대한 읽기/쓰기 액세스 권한이 있습니다. 태스크가 시작 태스크 또는 작업 준비 태스크와는 다른 계정에서 실행될 경우 태스크는 해당 디렉터리에 대해 읽기 액세스 권한만 있습니다.

태스크에서 파일 및 디렉터리 액세스에 대한 자세한 내용은 [Batch를 사용하여 대규모 병렬 컴퓨팅 솔루션 개발](batch-api-basics.md#files-and-directories)을 참조하세요.

## <a name="elevated-access-for-tasks"></a>태스크에 대한 관리자 권한 액세스 

사용자 계정의 권한 상승 수준은 태스크가 관리자 액세스 권한으로 실행되는지 여부를 나타냅니다. 자동 사용자 계정 및 명명된 사용자 계정 모두 관리자 권한 액세스로 실행될 수 있습니다. 권한 상승 수준의 두 가지 옵션은 다음과 같습니다.

- **NonAdmin:** 작업이 관리자 액세스 권한이 없는 표준 사용자로 실행됩니다. Batch 사용자 계정에 대한 기본 권한 상승 수준은 항상 **NonAdmin**입니다.
- **Admin:** 작업이 관리자 액세스 권한이 있는 사용자로 실행되고 전체 관리자 권한으로 작동됩니다. 

## <a name="auto-user-accounts"></a>자동 사용자 계정

기본적으로 태스크는 자동 사용자 계정에서 Batch로, 관리자 액세스 권한이 없는 표준 사용자로, 태스크 범위를 사용해서 실행됩니다. 태스크 범위에 대해 자동 사용자 지정이 구성되면 Batch 서비스는 해당 태스크에 대해서만 자동 사용자 계정을 만듭니다.

태스크 범위 대신 풀 범위를 지정할 수 있습니다. 태스크에 대한 자동 사용자 지정이 풀 범위에 대해 구성되면 태스크는 풀의 모든 태스크에서 사용할 수 있는 자동 사용자 계정으로 실행됩니다. 풀 범위에 대한 자세한 내용은 풀 범위를 사용하는 자동 사용자로 태스크 실행 섹션을 참조하세요.   

기본 범위는 Windows 및 Linux 노드에서 다릅니다.

- Windows 노드에서 태스크는 기본적으로 태스크 범위 내에서 실행됩니다.
- Linux 노드는 항상 풀 범위에서 실행됩니다.

자동 사용자 지정에는 각각 고유한 자동 사용자 계정에 해당하는 4개의 구성이 있을 수 있습니다.

- 태스크 범위를 사용하는 비관리자 액세스(기본 자동 사용자 지정)
- 태스크 범위를 사용하는 관리자(상승된 권한) 액세스
- 풀 범위를 사용하는 비관리자 액세스
- 풀 범위를 사용하는 관리자 액세스

> [!IMPORTANT] 
> 태스크 범위 내에서 실행되는 태스크는 실제로 노드의 다른 태스트에 대해 액세스 권한이 없습니다. 그러나 계정에 액세스할 수 있는 악의적인 사용자는 관리자 권한으로 실행되고 다른 태스크 디렉터리에 액세스하는 태스크를 제출하여 이러한 제한을 해결할 수 있습니다. 또한 악의적인 사용자는 RDP 또는 SSH를 사용하여 노드에 연결할 수도 있습니다. 이러한 시나리오를 방지하기 위해서는 Batch 계정 키에 대한 액세스를 보호하는 것이 중요합니다. 계정이 노출이 의심되는 경우에 키를 다시 생성해야 합니다.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>관리자 액세스 권한이 있는 자동 사용자로 태스크 실행

관리자 액세스 권한으로 태스크를 실행해야 하는 경우 관리자 권한에 대한 자동 사용자 지정을 구성할 수 있습니다. 예를 들어 시작 태스크는 노드에 소프트웨어를 설치하기 위해 관리자 액세스 권한이 필요할 수 있습니다.

> [!NOTE] 
> 일반적으로 관리자 액세스 권한은 필요한 경우에만 사용하는 것이 좋습니다. 모범 사례는 원하는 결과를 달성하는 데 필요한 최소 권한을 부여하는 것입니다. 예를 들어 시작 태스크가 모든 사용자가 아닌 현재 사용자에 대해 소프트웨어를 설치하는 경우 태스크에 관리자 권한 액세스를 부여하지 않도록 할 수 있습니다. 시작 태스크를 포함하여 동일한 계정으로 실행해야 하는 모든 태스트에 대해 풀 범위 및 비관리자 액세스 권한에 대한 자동 사용자 지정을 구성할 수 있습니다. 
>
>

다음 코드 조각은 자동 사용자 지정을 구성하는 방법을 보여 줍니다. 예제에서는 권한 상승 수준을 `Admin`으로 설정하고 범위를 `Task`로 설정합니다. 태스크 범위가 기본 설정이지만 여기에는 예제를 위해 포함됩니다.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>풀 범위가 지정된 자동 사용자로 태스크 실행

노드가 프로비전될 때 풀의 각 노드에서 관리자 액세스 권한을 갖는 풀 전체 자동 사용자 계정과 이러한 권한을 갖지 않는 계정이 만들어집니다. 자동 사용자의 범위를 지정된 태스크에 대한 풀 범위로 설정하면 태스크는 이러한 2개의 풀 전체 자동 사용자 계정 중 하나에서 실행됩니다. 

자동 사용자에 대해 풀 범위를 지정하면 관리자 액세스 권한으로 실행되는 모든 태스크가 동일한 풀 전체 자동 사용자 계정에서 실행됩니다. 마찬가지로 관리자 권한 없이 실행되는 태스크도 단일 풀 전체 자동 사용자 계정에서 실행됩니다. 

> [!NOTE] 
> 2개의 풀 전체 자동 사용자 계정은 별도 계정입니다. 풀 전체 관리 계정에서 실행되는 태스크는 표준 계정으로 실행되는 태스크와 데이터를 공유할 수 없으며 그 반대의 경우도 마찬가지입니다. 
>
>

동일한 자동 사용자 계정에서 실행될 때의 장점은 태스크가 동일한 노드에서 실행 중인 다른 태스크와 데이터를 공유할 수 있다는 것입니다.

태스크 간에 암호를 공유하는 것은 두 풀 전체 자동 사용자 계정 중 하나에서 태스크를 실행하는 것이 유용한 하나의 시나리오에 해당합니다. 예를 들어 시작 태스크가 해당 태스크에서 사용할 수 있는 노드에 암호를 프로비전해야 한다고 가정해 봅니다. Windows DPAPI(데이터 보호 API)를 사용할 수 있지만 관리자 권한이 필요합니다. 대신 사용자 수준에서 암호를 보호할 수 있습니다. 동일한 사용자 계정에서 실행되는 태스크는 관리자 액세스 권한 없이도 암호에 액세스할 수 있습니다.

풀 범위를 갖는 자동 사용자 계정에서 태스크를 실행하려는 또 다른 시나리오는 MPI(Message Passing Interface) 파일 공유입니다. MPI 파일 공유는 MPI 태스크의 노드가 동일한 파일 데이터에 작동해야 하는 경우에 유용합니다. 헤드 노드는 자식 노드가 동일한 자동 사용자 계정에서 실행되는 경우 자식 노드가 액세스할 수 있는 파일 공유를 만듭니다. 

다음 코드 조각은 Batch .NET에서 태스크에 대해 자동 사용자 범위를 풀 범위로 설정합니다. 권한 상승 수준이 생략되므로 태스크는 표준 풀 전체 자동 사용자 계정에서 실행됩니다.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>명명된 사용자 계정

풀을 만들 때 명명된 사용자 계정을 정의할 수 있습니다. 명명된 사용자 계정에는 사용자가 제공하는 이름 및 암호가 지정됩니다. 명명된 사용자 계정에 대해 권한 상승 수준을 지정할 수 있습니다. Linux 노드의 경우 SSH 개인 키를 제공할 수도 있습니다.

명명된 사용자 계정은 풀의 모든 노드에 존재하며 해당 노드에서 실행되는 모든 태스크에서 사용할 수 있습니다. 풀에 대해 원하는 수의 명명된 사용자를 정의할 수 있습니다. 태스크 또는 태스크 컬렉션을 추가할 때 태스크가 풀에 대해 정의된 명명된 사용자 계정 중 하나에서 실행되도록 지정할 수 있습니다.

명명된 사용자 계정은 동일한 사용자 계정에서 작업의 모든 태스크를 실행하려고 하지만 동시에 다른 작업에서 실행되는 태스크와 격리하려는 경우에 유용합니다. 예를 들어 각 작업에 대해 명명된 사용자를 만들고 해당 명명된 사용자 계정에서 각 작업의 태스크를 실행할 수 있습니다. 그런 후 각 작업은 자체 태스크와 암호를 공유할 수 있지만 다른 작업에서 실행되는 태스크와는 암호를 공유할 수 없습니다.

또한 명명된 사용자 계정을 사용하여 파일 공유와 같은 외부 리소스에 대해 권한을 설정하는 태스크도 실행할 수 있습니다. 명명된 사용자 계정을 사용하여 사용자 ID를 제어하고 해당 사용자 ID를 사용하여 권한을 설정합니다.  

명명된 사용자 계정은 Linux 노드 간에 암호 없는 SSH를 사용하도록 설정합니다. 다중 인스턴스 태스크를 실행해야 하는 Linux 노드에 명명된 사용자 계정을 사용할 수 있습니다. 풀의 각 노드는 전체 풀에 정의된 사용자 계정에서 태스크를 실행할 수 있습니다. 다중 인스턴스 태스크에 대한 자세한 내용은 [다중\- 인스턴스 태스크를 사용하여 MPI 애플리케이션 실행](batch-mpi.md)을 참조하세요.

### <a name="create-named-user-accounts"></a>명명된 사용자 계정 만들기

Batch에서 명명된 사용자 계정을 만들려면 풀에 사용자 계정의 컬렉션을 추가합니다. 다음 코드 조각에서는 .NET, Java 및 Python에서 명명된 사용자 계정을 만드는 방법을 보여 줍니다. 이러한 코드 조각에서는 풀에 관리자 및 비관리자 명명된 계정을 만드는 방법을 보여 줍니다. 예제에서는 클라우드 서비스 구성을 사용하여 풀을 만들지만 가상 머신 구성을 사용하여 Windows 또는 Linux 풀을 만들 때도 같은 방법을 사용합니다.

#### <a name="batch-net-example-windows"></a>Batch .NET 예제(Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET 예제(Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Batch Java 예제

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python 예제

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>관리자 액세스 권한을 갖는 명명된 사용자 계정에서 태스크 실행

관리자 권한 사용자로 태스크를 실행하려면 태스크의 **UserIdentity** 속성을 해당 **ElevationLevel** 속성을 `Admin`으로 설정한 상태로 만든 명명된 사용자 계정으로 설정합니다.

이 코드 조각은 태스크가 명명된 사용자 계정으로 실행되도록 지정합니다. 이 명명된 사용자 계정은 풀이 만들어질 때 풀에 정의된 것입니다. 이 경우 명명된 사용자 계정은 다음 관리자 권한으로 만들어졌습니다.

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>최신 Batch 클라이언트 라이브러리로 코드 업데이트

2017-01-01.4.0의 Batch 서비스 버전에는 중요한 변경 내용이 추가되었으며 이전 버전에서 사용할 수 있던 **runElevated** 속성이 **userIdentity** 속성으로 바뀌었습니다. 다음 표에서는 이전 버전의 클라이언트 라이브러리에서 코드를 업데이트하는 데 사용할 수 있는 간단한 매핑을 제공합니다.

### <a name="batch-net"></a>Batch .NET

| 코드에서 사용되는 원본...                  | 업데이트 대상...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated`가 지정되지 않음 | 업데이트 필요 없음                                                                                               |

### <a name="batch-java"></a>Batch Java

| 코드에서 사용되는 원본...                      | 업데이트 대상...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated`가 지정되지 않음 | 업데이트 필요 없음                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| 코드에서 사용되는 원본...                      | 업데이트 대상...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user` <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user` <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated`가 지정되지 않음 | 업데이트 필요 없음                                                                                                                                  |


## <a name="next-steps"></a>다음 단계

* Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.

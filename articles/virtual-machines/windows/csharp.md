---
title: C#을 사용하여 Azure Virtual Machine 만들기 및 관리 | Microsoft Docs
description: C# 및 Azure Resource Manager를 사용하여 가상 컴퓨터 및 모든 지원 리소스를 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: ce05d097aa69aa1aadb8450e40722448bc5a7de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402044"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>C#을 사용하여 Azure에서 Windows VM 생성 및 관리 #

[Azure VM(Virtual Machine)](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 몇 가지 지원 Azure 리소스가 필요합니다. 이 문서에서는 C#을 사용하여 VM 리소스 만들기, 관리 및 삭제에 대해 설명합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Visual Studio 프로젝트 만들기
> * 패키지 설치
> * 자격 증명 만들기
> * 리소스 만들기
> * 관리 작업 수행
> * 리소스 삭제
> * 애플리케이션 실행

이러한 단계를 수행하려면 약 20분이 걸립니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)를 아직 설치하지 않았으면 설치합니다. 작업 페이지에서 **.NET 데스크톱 개발**을 선택한 다음 **설치**를 클릭합니다. 요약에서 **.NET Framework 4 - 4.6 개발 도구**가 자동으로 선택되는 것을 볼 수 있습니다. Visual Studio를 이미 설치한 경우 Visual Studio 시작 관리자를 사용하여 .NET 작업을 추가할 수 있습니다.
2. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Visual C#** 에서 **콘솔 앱(.NET Framework)** 을 선택하고, 프로젝트의 이름에 *myDotnetProject*를 입력하고 프로젝트의 위치를 선택한 다음 **확인**을 클릭합니다.

## <a name="install-the-package"></a>패키지 설치

NuGet 패키지는 이러한 단계를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Visual Studio에서 필요한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. **도구** > **Nuget 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다.
2. 콘솔에 다음 명령을 입력합니다.

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>자격 증명 만들기

이 단계를 시작하기 전에 [Active Directory 서비스 주체](../../active-directory/develop/howto-create-service-principal-portal.md)에 액세스할 수 있는지 확인합니다. 또한 이후 단계에서 필요한 애플리케이션 ID, 인증 키 및 테넌트 ID를 기록해 두어야 합니다.

### <a name="create-the-authorization-file"></a>권한 부여 파일 만들기

1. 솔루션 탐색기에서 *myDotnetProject* > **추가** > **새 항목**을 마우스 오른쪽 단추로 클릭한 다음 *Visual C# 항목*에서 **텍스트 파일**을 선택합니다. 파일 이름을 *azureauth.properties*로 지정하고 **추가**를 클릭합니다.
2. 다음과 같은 권한 부여 속성을 추가합니다.

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    **&lt;subscription-id&gt;** 를 구독 식별자, **&lt;application-id&gt;** 를 Active Directory 애플리케이션 식별자, **&lt;authentication-key&gt;** 를 애플리케이션 키, **&lt;tenant-id&gt;** 를 테넌트 식별자로 바꿉니다.

3. azureauth.properties 파일을 저장합니다. 
4. AZURE_AUTH_LOCATION이라는 Windows 환경 변수를 만든 권한 부여 파일의 전체 경로로 설정합니다. 예를 들어 다음과 같은 PowerShell 명령을 사용할 수 있습니다.

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>관리 클라이언트 만들기

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 기존 문에 추가합니다.

    ```
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. 관리 클라이언트를 만들려면 Main 메서드에 다음 코드를 추가합니다.

    ```
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>리소스 만들기

### <a name="create-the-resource-group"></a>리소스 그룹 만들기

모든 리소스는 [리소스 그룹](../../azure-resource-manager/resource-group-overview.md)에 포함되어야 합니다.

애플리케이션의 값을 지정하고 리소스 그룹을 만들려면 Main 메서드에 다음 코드를 추가합니다.

```
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>가용성 집합 만들기

[가용성 집합](tutorial-availability-sets.md)은 애플리케이션에서 사용되는 가상 머신을 쉽게 유지 관리할 수 있도록 합니다.

가용성 집합을 만들려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기

[공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)는 가상 머신과 통신해야 합니다.

가상 머신의 공용 IP 주소를 만들려면 Main 메서드에 다음 코드를 추가합니다.
   
```
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기

가상 머신은 [가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 서브넷에 있어야 합니다.

서브넷 및 가상 네트워크를 만들려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>네트워크 인터페이스 만들기

가상 머신은 가상 네트워크에서 통신하기 위해 네트워크 인터페이스가 필요합니다.

네트워크 인터페이스를 만들려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>가상 머신 만들기

모든 지원 리소스를 만들었으므로 가상 머신을 만들 수 있습니다.

가상 머신을 만들려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> 이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 머신을 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 머신 탐색 및 선택](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
> 
>

마켓플레이스 이미지 대신 기존 디스크를 사용하려면 다음 코드를 사용합니다.

```
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>관리 작업 수행

가상 머신의 수명 주기 동안 가상 머신 시작, 중지 또는 삭제 등의 관리 작업을 실행하려고 할 수 있습니다. 또한 반복적이거나 복잡한 작업을 자동화하는 코드를 만들 수도 있습니다.

VM에서 작업을 수행해야 하는 경우 VM의 인스턴스를 가져와야 합니다.

```
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>VM 관련 정보 가져오기

가상 컴퓨터에 대한 정보를 가져오려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>VM을 중지합니다.

가상 머신을 중지하고 해당 설정을 모두 그대로 유지하면 계속 요금이 청구될 수 있습니다. 그렇지 않으려면 가상 머신을 중지하고 할당을 해제합니다. 가상 머신을 할당을 해제하면 연결된 모든 리소스의 할당이 취소되고 대금 청구가 끝납니다.

할당을 취소하지 않고 가상 머신을 중지하려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

가상 머신의 할당을 취소하려는 경우 PowerOff 호출을 이 코드로 변경합니다.

```
vm.Deallocate();
```

### <a name="start-the-vm"></a>VM 시작

가상 머신을 시작하려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>VM 크기 조정

가상 머신의 크기를 결정할 때 배포의 여러 측면을 고려해야 합니다. 자세한 내용은 [VM 크기](sizes.md)를 참조하세요.  

가상 머신의 크기를 변경하려면 Main 메서드에 다음 코드를 추가합니다.

```
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>VM에 데이터 디스크 추가

가상 머신에 데이터 디스크를 추가하려면 Main 메서드에 다음 코드를 추가하여 크기가 2GB이고 LUN이 0이며 캐싱 형식이 읽기/쓰기인 데이터 디스크를 추가합니다.

```
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 가상 머신 및 모든 지원 리소스를 삭제하려는 경우, 리소스 그룹을 삭제해야 합니다.

리소스 그룹을 삭제하려면 Main 메서드에 다음 코드를 추가합니다.

```
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>애플리케이션 실행

이 콘솔 애플리케이션을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. 

1. 콘솔 애플리케이션을 실행하려면 **시작**을 클릭합니다.

2. **Enter** 키를 눌러 리소스를 삭제하기 전에 Azure Portal에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다. 배포에 대한 정보를 보려면 배포 상태를 클릭합니다.

## <a name="next-steps"></a>다음 단계
* [C# 및 Resource Manager 템플릿을 사용하여 Azure Virtual Machine 배포](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 정보를 사용하여 가상 머신을 만드는 데 템플릿을 활용합니다.
* [.NET용 Azure 라이브러리](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet) 사용에 대해 자세히 알아봅니다.


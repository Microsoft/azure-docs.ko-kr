---
title: 다중 NIC이 있는 VM(클래식) 만들기 - Azure PowerShell | Microsoft Docs
description: PowerShell을 사용하여 다중 NIC이 있는 VM(클래식)을 만드는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 087b52bd603e8aed6078ab340e84c1f6bd0e8082
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748493"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>PowerShell을 사용하여 다중 NIC이 있는 VM(클래식) 만들기

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Azure에서 VM(가상 머신)을 만들고 각 VM에 여러 NIC(네트워크 인터페이스)를 연결할 수 있습니다. 여러 NIC를 사용하면 NIC 간에 트래픽 유형을 분리할 수 있습니다. 예를 들어 하나의 NIC는 인터넷과 통신하는 동안 다른 NIC는 인터넷에 연결되지 않은 내부 리소스와만 통신할 수 있습니다. 여러 NIC 간에 네트워크 트래픽을 분리하는 기능은 애플리케이션 전달 및 WAN 최적화 솔루션과 같은 많은 네트워크 가상 어플라이언스에 필요합니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한  [Resource Manager 및 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 배포 모델](../virtual-machines/windows/multiple-nics.md)을 사용하여 이러한 단계를 수행하는 방법을 알아봅니다.

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

다음 단계에서는 WEB 서버에 *IaaSStory*라는 리소스 그룹을, DB 서버에 *IaaSStory-BackEnd*라는 리소스 그룹을 사용합니다.

## <a name="prerequisites"></a>필수 조건

DB 서버를 만들려면 먼저 이 시나리오에 필요한 모든 리소스로 *IaaSStory* 리소스 그룹을 만들어야 합니다. 이러한 리소스를 만들려면 다음 단계를 완료합니다. [가상 네트워크 만들기](virtual-networks-create-vnet-classic-netcfg-ps.md) 문서에 나오는 단계를 따라 VNet을 만듭니다.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>백 엔드 VM 만들기
백 엔드 VM은 만드는 리소스에 따라 다음과 같이 다릅니다.

* **백 엔드 서브넷**. 트래픽을 격리하기 위해서 데이터베이스 서버는 별도의 서브넷에 속하게 됩니다. 아래 스크립트는 이 서브넷이 *WTestVnet*이라는 vnet에 존재한다고 추정합니다.
* **데이터 디스크용 Storage 계정**. 성능 향상을 위해 데이터베이스 서버의 데이터 디스크는 SSD(반도체 드라이브) 기술을 사용하며, 이 기술에는 Premium Storage 계정이 필요합니다. 배포할 Azure 위치에서 Premium Storage가 지원되는지 확인하세요.
* **가용성 집합**. 모든 데이터베이스 서버가 단일 가용성 집합에 추가되어, 유지 관리 도중에 하나 이상의 VM이 실행 중이도록 합니다.

### <a name="step-1---start-your-script"></a>1단계 - 스크립트 시작
사용되는 전체 PowerShell 스크립트를 [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)에서 다운로드할 수 있습니다. 다음 단계에 따라 스크립트를 사용자 환경에서 작동하도록 변경합니다.

1. 위의 [필수 조건](#prerequisites)에서 배포한 기존 리소스 그룹을 기반으로 다음 변수 값을 변경합니다.

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. 백 엔드 배포에 사용하려는 값을 기반으로 다음 변수 값을 변경합니다.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2단계 - VM에 필요한 리소스 만들기
새로운 클라우드 서비스, 모든 VM에 대한 데이터 디스크의 저장소 계정을 만들어야 합니다. VM에 대한 로컬 관리자 계정 및 이미지도 지정해야 합니다. 이러한 리소스를 만들려면 다음 단계를 완료합니다.

1. 새 클라우드 서비스 만들기

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. 새 Premium Storage 계정 만들기

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. 앞에서 만든 저장소 계정을 구독에 대한 현재 저장소 계정으로 설정합니다.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. VM에 대한 이미지를 선택합니다.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. 로컬 관리자 계정 자격 증명을 설정합니다.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>3단계: VM 만들기
루프를 사용하여 VM을 원하는 개수만큼 만들고 루프 내에서 필요한 NIC와 VM을 만듭니다. NIC와 VM을 만들려면 다음 단계를 실행합니다.

1. `$numberOfVMs` 변수 값을 기반으로 필요한 횟수만큼 VM 1개와 NIC 2개를 만드는 명령을 반복하는 `for` 루프를 시작합니다.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. VM의 이미지, 크기, 가용성 집합을 지정하는 `VMConfig` 개체를 만듭니다.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. VM을 Windows VM으로 프로비전합니다.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. 기본 NIC를 설정하여 고정 IP 주소를 할당합니다.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. 각 VM에 두 번째 NIC를 추가합니다.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. 각 VM에 데이터 디스크를 만듭니다.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. 각 VM을 만들고 루프를 끝냅니다.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>4단계 - 스크립트 실행
이제 스크립트를 다운로드하여 요구에 맞게 변경했으므로 이 스크립트를 실행하여 여러 NIC를 포함하는 백 엔드 데이터베이스 VM을 만듭니다.

1. 스크립트를 저장하여 **PowerShell** 명령 프롬프트 또는 **PowerShell ISE**에서 실행합니다. 아래와 같이 초기 출력에 표시됩니다.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. 자격 증명 프롬프트에 필요한 정보를 입력하고 **확인**을 클릭합니다. 다음과 같은 출력이 반환됩니다.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5단계 - VM의 운영 체제 내에서 라우팅 구성

Azure DHCP는 가상 머신에 연결된 첫 번째(기본) 네트워크 인터페이스에 기본 게이트웨이를 할당합니다. 가상 머신에 연결된 추가(보조) 네트워크 인터페이스에는 기본 게이트웨이를 할당하지 않습니다. 따라서 보조 네트워크 인터페이스가 있는 서브넷 외부의 리소스와는 기본적으로 통신할 수 없습니다. 그러나 보조 네트워크 인터페이스는 서브넷 외부의 리소스와 통신할 수 있습니다. 보조 네트워크 인터페이스에 대한 라우팅을 구성하려면 다음 문서를 참조하세요.

- [여러 NIC에 대한 Windows VM 구성](../virtual-machines/windows/multiple-nics.md#configure-guest-os-for-multiple-nics
)

- [여러 NIC에 대한 Linux VM 구성](../virtual-machines/linux/multiple-nics.md#configure-guest-os-for-multiple-nics
)

---
title: Azure PowerShell에서 SQL Server Virtual Machine 만들기(클래식) | Microsoft Docs
description: SQL Server 가상 컴퓨터 갤러리 이미지를 사용하여 Azure VM을 만드는 단계 및 PowerShell 스크립트를 제공합니다. 이 항목에서는 클래식 배포 모드를 사용합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad8b59a9290c533a3687b5ff8956d8682fb6d9e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607830"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Azure PowerShell을 사용하여 SQL Server 가상 컴퓨터 프로비전(클래식)

이 문서에서는 PowerShell cmdlet을 사용하여 Azure에서 SQL Server 가상 머신을 만드는 방법에 대한 단계를 제공합니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

이 항목의 Resource Manager 버전에 대해서는 [Azure PowerShell Resource Manager를 사용하여 SQL Server 가상 머신 프로비전](../sql/virtual-machines-windows-ps-sql-create.md)을 참조하세요.

### <a name="install-and-configure-powershell"></a>PowerShell 설치 및 구성:
1. Azure 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 방문하십시오.
2. [최신 Azure PowerShell 명령을 다운로드하여 설치합니다](/powershell/azure/overview).
3. Windows PowerShell을 시작하고 **Add-AzureAccount** 명령을 사용하여 Azure 구독에 연결합니다.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>대상 Azure 지역 확인

SQL Server Virtual Machine을 특정 Azure 지역에 있는 클라우드 서비스에서 호스트합니다. 다음 단계에서 지역, 저장소 계정 및 클라우드 서비스를 확인할 수 있으며, 이는 자습서의 나머지 부분에 사용됩니다.

1. SQL Server VM을 호스트하기 위해 사용하려는 데이터 센터를 확인합니다. 다음 PowerShell 명령은 사용 가능한 지역 이름 목록을 표시합니다.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. 원하는 위치를 식별했으면 **$dcLocation** 이라는 변수를 해당 지역으로 설정합니다. 예를 들어 다음 명령은 지역을 "미국 동부"로 설정합니다.

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>구독 및 저장소 계정 설정

1. 새 가상 머신에 대해 사용할 Azure 구독을 확인합니다.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. 대상 Azure 구독을 **$subscr** 변수에 할당합니다. 그런 다음 이를 현재 Azure 구독으로 설정합니다.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. 기존 저장소 계정을 확인합니다. 다음 스크립트는 선택한 지역에 있는 모든 저장소 계정을 표시합니다.

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > 새 스토리지 계정이 필요한 경우 먼저 New-AzureStorageAccount 명령을 사용하여 스토리지 계정 이름(모두 소문자)을 만듭니다. `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. 대상 저장소 계정 이름을 **$staccount**에 할당합니다. **Set-AzureSubscription** 을 사용하여 구독 및 현재 저장소 계정을 설정합니다.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>SQL Server 가상 컴퓨터 이미지를 선택합니다.

1. 갤러리에서 사용 가능한 SQL Server 가상 머신 이미지의 목록을 찾습니다. 이러한 모든 이미지에는 "SQL"로 시작하는 **ImageFamily** 속성이 있습니다. 다음 쿼리는 SQL Server를 미리 설치했을 때 사용 가능한 이미지 패밀리를 표시합니다.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. 가상 머신 이미지 패밀리를 찾으면 해당 패밀리에 여러 개의 게시된 이미지가 있을 수 있습니다. 다음 스크립트를 사용하여 선택한 이미지 패밀리에 대해 게시된 최신 가상 머신 이미지 이름을 찾습니다(예: **Windows Server 2012 R2의 SQL Server 2016 RTM Enterprise**).

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>가상 머신 만들기

마지막으로, PowerShell을 사용하여 가상 머신을 만듭니다.

1. 새 VM을 호스트할 클라우드 서비스를 만듭니다. 기존 클라우드 서비스를 대신 사용할 수도 있습니다. 클라우드 서비스의 짧은 이름을 사용하여 새 변수 **$svcname** 을 만듭니다.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. 가상 머신 이름 및 크기를 지정합니다. 가상 컴퓨터 크기에 대한 자세한 내용은 [Azure에 대한 Virtual Machine 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. 로컬 관리자 계정 및 암호 지정

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. 다음 스크립트를 실행하여 가상 머신을 만듭니다.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> 추가 설명과 구성 옵션은 **Azure PowerShell을 사용하여 Windows 기반 Virtual Machines 만들기 및 미리 구성** 의 [명령 집합 빌드](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)섹션을 참조하세요.

## <a name="example-powershell-script"></a>PowerShell 스크립트 예

다음 스크립트에서는 **Windows Server 2012 R2의 SQL Server 2016 RTM Enterprise** 가상 머신을 만드는 전체 스크립트의 예를 제공합니다. 이 스크립트를 사용하는 경우 이 항목의 이전 단계를 기반으로 하여 초기 변수를 사용자 지정해야 합니다.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>원격 데스크톱을 사용하여 연결

1. 현재 사용자의 문서 폴더에 .RDP 파일을 만들고 이러한 가상 머신을 시작하여 설정을 완료합니다.

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. 문서 디렉터리에서 RDP 파일을 시작합니다. 앞에서 입력한 관리자의 사용자 이름 및 암호에 연결합니다(예: 사용자 이름이 VMAdmin이면 사용자로 "\VMAdmin"을 지정하고 암호 입력).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>원격 액세스를 위한 SQL Server 컴퓨터 구성 완료

원격 데스크톱을 사용하여 컴퓨터에 로그온한 후 [Azure VM에서 SQL Server 연결을 구성하기 위한 단계](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)의 지침에 따라 SQL Server를 구성합니다.

## <a name="next-steps"></a>다음 단계

[가상 머신 설명서](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)에서 PowerShell을 사용하여 가상 머신을 프로비전하는 추가 지침을 찾을 수 있습니다.

대부분의 경우 다음 단계는 이 새로운 SQL Server VM에 데이터베이스를 마이그레이션하는 것입니다. 데이터베이스 마이그레이션 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)을 참조하세요.

또한 Azure Portal을 사용하여 SQL Virtual Machines를 만드는 방법을 알아보려면 [Azure에서 SQL Server Virtual Machines 프로비전](../sql/virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요. 자습서는 포털을 통해 이 PowerShell 항목에서 사용되는 클래식 모델이 아닌 권장되는 리소스 관리자 모델을 사용하여 VM을 만드는 과정을 안내합니다.

이러한 리소스 외에도 [Azure Virtual Machines에서 SQL Server 실행과 관련된 기타 항목](../sql/virtual-machines-windows-sql-server-iaas-overview.md)을 확인하는 것이 좋습니다.

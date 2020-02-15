---
title: SQL VM 리소스 공급자에 등록
description: SQL VM 리소스 공급자를 사용 하 여 Azure SQL Server 가상 컴퓨터를 등록 하면 Azure Marketplace 외부에 배포 된 SQL Server Vm에 대 한 기능을 사용 하도록 설정 하 고, 규정 준수 및 관리 효율성을 향상 시킬 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 01e683e31905281d25fdcf976bc58397c052a6c3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201631"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL VM 리소스 공급자를 사용 하 여 Azure에 SQL Server 가상 머신 등록

이 문서에서는 SQL VM 리소스 공급자를 사용 하 여 Azure에서 SQL Server VM (가상 머신)을 등록 하는 방법을 설명 합니다. 리소스 공급자를 사용 하 여 등록 하면 가상 컴퓨터 리소스의 개별 리소스인 구독 내에서 **SQL 가상 컴퓨터** _리소스가_ 생성 됩니다. 리소스 공급자에서 SQL Server VM 등록을 취소 하면 **SQL 가상 컴퓨터** _리소스가_ 제거 되지만 실제 가상 컴퓨터는 삭제 되지 않습니다. 

Azure Portal를 통해 SQL Server VM Azure Marketplace 이미지를 배포 하면 리소스 공급자와 SQL Server VM 자동으로 등록 됩니다. 그러나 Azure 가상 머신에서 SQL Server를 자동으로 설치 하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로 비전 하도록 선택 하는 경우 다음에 대 한 리소스 공급자를 사용 하 여 SQL Server VM을 등록 해야 합니다.

- **기능 이점**: 리소스 공급자를 사용 하 여 SQL Server VM를 등록 하면 [자동화 된 패치](virtual-machines-windows-sql-automated-patching.md), [자동화 된 백업](virtual-machines-windows-sql-automated-backup-v2.md)및 모니터링 및 관리 효율성 기능이 잠금 해제 됩니다. 또한 [라이선스](virtual-machines-windows-sql-ahb.md) 및 [버전](virtual-machines-windows-sql-change-edition.md) 유연성을 잠금 해제 합니다. 이전에는 Azure Marketplace에서 배포 된 SQL Server VM 이미지에만 이러한 기능을 사용할 수 있었습니다. 

- **준수**: SQL VM 리소스 공급자를 사용 하 여 등록 하면 제품 약관에 지정 된 대로 Azure 하이브리드 혜택 사용 하도록 설정 되었음을 Microsoft에 알리기 위한 단순화 된 방법이 제공 됩니다. 이 프로세스는 각 리소스에 대 한 라이선스 등록 양식을 관리할 필요가 없습니다.  

- **무료 관리**: 세 가지 관리 효율성 모드 모두에서 SQL VM 리소스 공급자를 등록 하는 것은 완전히 무료입니다. 리소스 공급자와 관련 된 추가 비용이 나 관리 모드를 변경 하는 경우는 없습니다. 

- **간소화 된 라이선스 관리**: SQL VM 리소스 공급자를 사용 하 여 등록 하면 SQL Server 라이선스 관리가 간소화 되 고 [AZURE PORTAL](virtual-machines-windows-sql-manage-portal.md), Az CLI 또는 PowerShell을 사용 하 여 Azure 하이브리드 혜택 사용 하도록 설정 된 SQL Server vm을 빠르게 식별할 수 있습니다. 

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

SQL VM 리소스 공급자를 활용 하려면 먼저 리소스 공급자에 [구독을 등록](#register-subscription-with-rp)해야 합니다. 그러면 리소스 공급자는 해당 특정 구독 내에서 리소스를 만들 수 있습니다.

SQL VM 리소스 공급자 사용의 이점에 대 한 자세한 내용은 다음 [channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 비디오를 시청 하세요. 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Azure에서 자동 설치 SQL Server 경우 SQL VM 리소스 공급자의 혜택-Microsoft Channel 9 비디오"></iframe>


## <a name="prerequisites"></a>사전 요구 사항

리소스 공급자에 SQL Server VM을 등록 하려면 다음이 필요 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- Azure 리소스 모델 [SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md) 공용 또는 Azure Government 클라우드에 배포 됩니다. 
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 [PowerShell](/powershell/azure/new-azureps-module-az)입니다. 

## <a name="management-modes"></a>관리 모드

[Sql IaaS 확장](virtual-machines-windows-sql-server-agent-extension.md) 을 아직 설치 하지 않은 경우 sql VM 리소스 공급자를 사용 하 여 등록 하면 등록 프로세스 중에 지정 된 3 가지 관리 모드 중 하나로 SQL Server IaaS 확장이 자동으로 설치 됩니다. 관리 모드를 지정 하지 않으면 SQL IaaS 확장이 전체 관리 모드로 설치 됩니다.  

SQL IaaS 확장이 이미 수동으로 설치 된 경우 이미 전체 관리 모드에 있으며 전체 모드로 리소스 공급자에 등록 하면 SQL Server 서비스가 다시 시작 되지 않습니다.

세 가지 관리 모드는 다음과 같습니다.

- **경량** 모드에서는 SQL Server를 다시 시작할 필요가 없지만 라이선스 유형과 SQL Server 버전의 변경만 지원 합니다. 여러 인스턴스가 있는 Vm을 SQL Server 하거나 FCI (장애 조치 (failover) 클러스터 인스턴스)에 참여 하는 경우이 옵션을 사용 합니다. 경량 모드를 사용 하는 경우 메모리 또는 CPU에 영향을 주지 않으며 관련 비용이 없습니다. SQL Server VM를 먼저 경량 모드로 등록 한 다음 예약 된 유지 관리 기간 동안 전체 모드로 업그레이드 하는 것이 좋습니다.  

- **전체** 모드에서는 모든 기능을 제공 하지만 SQL Server 및 시스템 관리자 권한이 다시 시작 되어야 합니다. 이 옵션은 SQL IaaS 확장을 수동으로 설치할 때 기본적으로 설치 되는 옵션입니다. 단일 인스턴스를 사용 하 여 SQL Server VM를 관리 하는 데 사용 합니다. 전체 모드는 메모리 및 CPU에 최소한의 영향을 주는 두 개의 windows 서비스를 설치 합니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있습니다. 전체 관리 효율성 모드를 사용 하는 것과 관련 된 비용은 없습니다. 

- **Noagent** 모드는 Windows Server 2008에 설치 된 SQL Server 2008 및 SQL Server 2008 R2 전용입니다. NoAgent 모드를 사용 하는 경우 메모리 또는 CPU에는 영향을 주지 않습니다. NoAgent 관리 모드를 사용 하는 것과 관련 된 비용은 없습니다. 

PowerShell을 사용 하 여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>RP를 사용 하 여 구독 등록

SQL VM 리소스 공급자에 SQL Server VM를 등록 하려면 먼저 리소스 공급자에 구독을 등록 해야 합니다. 이를 통해 SQL VM 리소스 공급자는 구독 내에서 리소스를 만들 수 있습니다.  Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. Azure Portal를 열고 **모든 서비스**로 이동 합니다. 
1. **구독** 으로 이동 하 고 원하는 구독을 선택 합니다.  
1. **구독** 페이지에서 **리소스 공급자**로 이동 합니다. 
1. Sql 관련 리소스 공급자를 가져오려면 필터에 **sql** 을 입력 합니다. 
1. 원하는 작업에 따라 **SqlVirtualMachine** 공급자에 대 한 **등록**, **다시 등록**또는 등록 **취소** 를 선택 합니다. 

![공급자 수정](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>명령 줄

Az CLI 또는 PowerShell을 사용 하 여 Azure 구독에 SQL VM 리소스 공급자를 등록 합니다. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>RP로 SQL VM 등록 

### <a name="lightweight-management-mode"></a>경량 관리 모드

[SQL Server IaaS 에이전트 확장이](virtual-machines-windows-sql-server-agent-extension.md) VM에 설치 되지 않은 경우 SQL vm 리소스 공급자를 사용 하 여 경량 모드로 등록 하는 것이 좋습니다. 이렇게 하면 SQL IaaS 확장을 [경량 모드로](#management-modes) 설치 하 고 SQL Server 서비스를 다시 시작 하지 않도록 합니다. 그런 다음 언제 든 지 전체 모드로 업그레이드할 수 있지만 이렇게 하면 SQL Server 서비스가 다시 시작 되므로 예약 된 유지 관리 기간이 될 때까지 대기 하는 것이 좋습니다. 

사용 요금을 지불 하려면`PAYG`(종 량 제)로 SQL Server 라이선스 유형을 제공 하 고 Azure 하이브리드 혜택, 사용자 라이선스를 사용 하거나`DR``AHUB`(재해 복구)를 사용 하 여 [무료 DR 복제본 라이선스](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)를 활성화 합니다.

장애 조치 (Failover) 클러스터 인스턴스 및 다중 인스턴스 배포는 경량 모드의 SQL VM 리소스 공급자에만 등록할 수 있습니다. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Az CLI를 사용 하 여 SQL Server VM를 경량 모드로 등록 합니다. 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 경량 모드에서 SQL Server VM를 등록 합니다.  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>전체 관리 모드


SQL IaaS 확장이 이미 VM에 수동으로 설치 된 경우 SQL Server 서비스를 다시 시작 하지 않고 SQL Server VM를 전체 모드로 등록할 수 있습니다. **그러나 SQL IaaS 확장을 설치 하지 않은 경우 전체 모드로 등록 하면 SQL IaaS 확장을 전체 모드로 설치 하 고 SQL Server 서비스를 다시 시작 합니다. 주의 하 여 계속 하세요.**


SQL Server VM를 전체 모드로 직접 등록 하 고 SQL Server 서비스를 다시 시작 하려면 다음 PowerShell 명령을 사용 합니다. 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent 관리 모드 

Windows Server 2008 (_R2 아님_)에 설치 된 SQL Server 2008 및 2008 R2는 [NOAGENT 모드](#management-modes)의 SQL VM 리소스 공급자에 등록할 수 있습니다. 이 옵션은 규정 준수를 보장 하 고 기능이 제한 된 Azure Portal에서 SQL Server VM를 모니터링할 수 있도록 합니다.

`AHUB`, `PAYG`또는 `DR`을 **sqlLicenseType**로 지정 하 고 `SQL2008-WS2008` 또는 `SQL2008R2-WS2008`를 **sqlImageOffer**로 지정 합니다. 

Windows Server 2008 인스턴스에 SQL Server 2008 또는 2008 R2 인스턴스를 등록 하려면 다음 Az CLI 또는 PowerShell 코드 조각을 사용 합니다. 


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Az CLI를 사용 하 여 NoAgent 모드에서 SQL Server 2008 VM을 등록 합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```
 
 
Az CLI를 사용 하 여 NoAgent 모드에서 SQL Server 2008 R2 VM을 등록 합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008R2
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 NoAgent 모드에서 SQL Server 2008 VM을 등록 합니다. 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  PowerShell을 사용 하 여 NoAgent 모드에서 SQL Server 2008 R2 VM을 등록 합니다. 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>전체 관리 모드로 업그레이드 

*경량* IaaS 확장이 설치 된 SQL Server vm은 Azure Portal, Az CLI 또는 PowerShell을 사용 하 여 모드를 _full_ 로 업그레이드할 수 있습니다. _Noagent_ 모드의 SQL Server VM은 OS가 Windows 2008 R2 이상으로 업그레이드 된 후에 _전체_ 로 업그레이드할 수 있습니다. 다운 그레이드할 수는 없습니다. 이렇게 하려면 SQL VM 리소스 공급자에서 SQL Server VM [등록을 취소](#unregister-vm-from-rp) 해야 합니다. 이렇게 하면 **SQL 가상 컴퓨터** _리소스가_제거 되지만 실제 가상 컴퓨터는 삭제 되지 않습니다. 

PowerShell을 사용 하 여 SQL Server IaaS 에이전트의 현재 모드를 볼 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

에이전트 모드를 full로 업그레이드 하려면 다음을 수행 합니다. 


### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 컴퓨터](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 리소스로 이동 합니다. 
1. SQL Server 가상 컴퓨터를 선택 하 고 **개요**를 선택 합니다. 
1. NoAgent 또는 lightweight IaaS 모드를 사용 하는 SQL Server Vm의 경우 **SQL IaaS 확장 메시지에서 유일한 라이선스 유형 및 버전 업데이트를 사용할 수** 있습니다 .를 선택 합니다.

   ![포털에서 모드를 변경 하기 위한 선택 항목](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. **가상 컴퓨터에서 SQL Server 서비스를 다시 시작** 합니다. 확인란을 선택 하 고 **확인** 을 선택 하 여 IaaS 모드를 전체로 업그레이드 합니다. 

    ![가상 머신에서 SQL Server 서비스를 다시 시작 하는 것에 동의 하는 확인란](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>명령 줄

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

다음 Az CLI 코드 조각을 실행 합니다.

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

다음 PowerShell 코드 조각을 실행 합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>등록 상태 확인
Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 SQL Server VM SQL VM 리소스 공급자에 이미 등록 되어 있는지 확인할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL Server virtual machines](virtual-machines-windows-sql-manage-portal.md)로 이동 합니다.
1. 목록에서 SQL Server VM을 선택 합니다. SQL Server VM 여기에 나열 되지 않은 경우 SQL VM 리소스 공급자에 등록 되지 않았을 수 있습니다. 
1. **상태**에서 값을 확인 합니다. **상태가** **성공**인 경우 SQL Server VM SQL VM 리소스 공급자에 등록 되었습니다. 

![SQL RP 등록을 사용 하 여 상태 확인](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>명령 줄

Az CLI 또는 PowerShell을 사용 하 여 현재 SQL Server VM 등록 상태를 확인 합니다. 등록에 성공 하면 `ProvisioningState` `Succeeded` 표시 됩니다. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

오류는 SQL Server VM 리소스 공급자에 등록 되지 않았음을 나타냅니다. 


## <a name="unregister-vm-from-rp"></a>RP에서 VM 등록 취소

SQL VM 리소스 공급자를 사용 하 여 SQL Server VM 등록을 취소 하려면 Azure Portal 또는 Azure CLI를 사용 하 여 SQL 가상 머신 *리소스* 를 삭제 합니다. SQL 가상 컴퓨터 *리소스* 를 삭제 해도 SQL Server VM는 삭제 되지 않습니다. 그러나 *리소스*를 제거 하려고 할 때 실수로 가상 컴퓨터를 삭제할 수 있으므로 주의 하 여 단계를 신중 하 게 수행 해야 합니다. 

관리 모드를 전체로 다운 그레이드 하려면 sql vm 리소스 공급자를 사용 하 여 SQL VM 등록을 취소 해야 합니다. 

### <a name="azure-portal"></a>Azure portal

Azure Portal를 사용 하 여 리소스 공급자에 SQL Server VM 등록을 취소 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. SQL Server VM 리소스로 이동 합니다. 
  
   ![SQL 가상 컴퓨터 리소스](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. **삭제**를 선택합니다. 

   ![SQL VM 리소스 공급자 삭제](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. SQL 가상 컴퓨터의 이름을 입력 하 고 **가상 컴퓨터 옆에 있는 확인란의 선택을 취소**합니다.

   ![SQL VM 리소스 공급자 삭제](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 가상 컴퓨터 이름 옆의 확인란을 선택 하지 않으면 가상 컴퓨터가 완전히 *삭제* 됩니다. 리소스 공급자에서 SQL Server VM의 등록을 취소 하지만 *실제 가상 컴퓨터는 삭제 하지*않으려면 확인란의 선택을 취소 합니다. 

1. **삭제** 를 선택 하 여 SQL Server 가상 머신이 아닌 SQL 가상 머신 *리소스*삭제를 확인 합니다. 

### <a name="command-line"></a>명령 줄

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용 하 여 리소스 공급자에서 SQL Server 가상 컴퓨터의 등록을 취소 하려면 [az SQL vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) 명령을 사용 합니다. 그러면 SQL Server 가상 컴퓨터 *리소스가* 제거 되지만 가상 컴퓨터는 삭제 되지 않습니다. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Azure CLI를 사용 하 여 리소스 공급자에서 SQL Server 가상 컴퓨터의 등록을 취소 하려면 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)명령을 사용 합니다. 그러면 SQL Server 가상 컴퓨터 *리소스가* 제거 되지만 가상 컴퓨터는 삭제 되지 않습니다. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>제한 사항

SQL VM 리소스 공급자는 다음을 지원 합니다.
- Azure Resource Manager를 통해 배포 된 Vm을 SQL Server 합니다. 클래식 모델을 통해 배포 된 SQL Server Vm은 지원 되지 않습니다. 
- 공용 또는 Azure Government 클라우드에 배포 된 Vm을 SQL Server 합니다. 다른 사설 또는 정부 클라우드로의 배포는 지원 되지 않습니다. 


## <a name="frequently-asked-questions"></a>질문과 대답 

**Azure Marketplace의 SQL Server 이미지에서 프로 비전 된 SQL Server VM를 등록 해야 하나요?**

아니요. Microsoft에서는 Azure Marketplace의 SQL Server 이미지에서 프로 비전 된 Vm을 자동으로 등록 합니다. VM이 Azure Marketplace의 SQL Server 이미지에서 프로 비전 *되지 않아* SQL Server 자체 설치 된 경우에만 SQL vm 리소스 공급자를 사용 하 여 등록 해야 합니다.

**SQL VM 리소스 공급자를 모든 고객이 사용할 수 있나요?** 

예. 사용자가 Azure Marketplace의 SQL Server 이미지를 사용 하지 않고 대신 자체 설치 된 SQL Server를 사용 하거나 사용자 지정 VHD를 사용 하는 경우 SQL Server Vm을 SQL VM 리소스 공급자와 함께 등록 해야 합니다. 모든 유형의 구독 (직접, 기업계약 및 클라우드 솔루션 공급자)이 소유한 Vm은 SQL VM 리소스 공급자에 등록할 수 있습니다.

**내 SQL Server VM에 SQL Server IaaS 확장이 이미 설치 되어 있는 경우 SQL VM 리소스 공급자에 등록 해야 하나요?**

SQL Server VM 자체 설치 되 고 Azure Marketplace의 SQL Server 이미지에서 프로 비전 되지 않은 경우 SQL Server IaaS 확장을 설치한 경우에도 SQL VM 리소스 공급자에 등록 해야 합니다. SQL VM 리소스 공급자를 사용 하 여 등록 하면 SqlVirtualMachines 형식의 새 리소스가 생성 됩니다. SQL Server IaaS 확장을 설치 하면 해당 리소스가 생성 되지 않습니다.

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 관리 모드는 무엇입니까?**

SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 관리 모드가 *꽉 찼습니다*. SQL VM 리소스 공급자를 사용 하 여 등록할 때 SQL Server 관리 속성이 설정 되지 않은 경우 모드가 전체 관리 효율성으로 설정 되 고 SQL Server 서비스가 다시 시작 됩니다. 먼저 경량 모드에서 SQL VM 리소스 공급자에 등록 한 다음 유지 관리 기간 중에 전체로 업그레이드 하는 것이 좋습니다. 

**SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 무엇 인가요?**

경량 모드 또는 에이전트 없음 모드에서 SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 없습니다. 전체 모드로 SQL VM 리소스 공급자에 등록 하기 위한 필수 구성 요소는 SQL Server IaaS 확장이 VM에 설치 되어 있는 것입니다 .이 경우 SQL Server 서비스가 다시 시작 됩니다. 

**SQL Server IaaS 확장이 VM에 설치 되어 있지 않으면 SQL VM 리소스 공급자에 등록할 수 있나요?**

예, SQL Server IaaS 확장이 VM에 설치 되어 있지 않은 경우 경량 관리 모드로 SQL VM 리소스 공급자에 등록할 수 있습니다. 경량 모드에서 SQL VM 리소스 공급자는 콘솔 앱을 사용 하 여 SQL Server 인스턴스의 버전 및 버전을 확인 합니다. 

SQL VM 리소스 공급자를 사용 하 여 등록할 때 기본 SQL 관리 모드가 _꽉 찼습니다_. SQL VM 리소스 공급자를 사용 하 여 등록할 때 SQL 관리 속성을 설정 하지 않으면 모드가 전체 관리 효율성으로 설정 됩니다. 먼저 경량 모드에서 SQL VM 리소스 공급자에 등록 한 다음 유지 관리 기간 중에 전체로 업그레이드 하는 것이 좋습니다. 

**SQL VM 리소스 공급자에 등록 하 여 내 VM에 에이전트를 설치 하나요?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하면 새 메타 데이터 리소스만 생성 됩니다. VM에 에이전트를 설치 하지 않습니다.

SQL Server IaaS 확장은 완전 한 관리 기능을 사용 하도록 설정 하는 데만 필요 합니다. 관리 효율성 모드를 경량에서 전체로 업그레이드 하면 SQL Server IaaS 확장이 설치 되 고 SQL Server 다시 시작 됩니다.

**VM에서 SQL Server VM 리소스 공급자 다시 시작 SQL Server에 등록 됩니까?**

등록 중에 지정 된 모드에 따라 달라 집니다. Lightweight 또는 NoAgent 모드가 지정 된 경우 SQL Server 서비스는 다시 시작 되지 않습니다. 그러나 관리 모드를 full로 지정 하거나 관리 모드를 비워 두면 전체 관리 모드로 SQL IaaS 확장을 설치 합니다. 그러면이로 인해 SQL Server 서비스가 다시 시작 됩니다. 

**SQL VM 리소스 공급자를 사용 하 여 등록할 때 간단 하 고 에이전트 없는 관리 모드의 차이점은 무엇 인가요?** 

아니요-에이전트 관리 모드는 SQL Server 2008 및 SQL Server 2008 R2 on Windows Server 2008에만 사용할 수 있습니다. 이러한 버전에는 유일 하 게 사용할 수 있는 관리 모드입니다. 다른 모든 버전 SQL Server의 경우 사용 가능한 두 가지 관리 효율성 모드가 간단 하 고 완전 합니다. 

비-에이전트 모드를 사용 하려면 고객이 SQL Server 버전 및 버전 속성을 설정 해야 합니다. 경량 모드는 VM을 쿼리하여 SQL Server 인스턴스의 버전 및 에디션을 찾습니다.

**SQL Server 라이선스 유형을 지정 하지 않고 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. SQL VM 리소스 공급자를 사용 하 여 등록 하는 경우 SQL Server 라이선스 형식은 선택적 속성이 아닙니다. SQL Server 라이선스 유형을 종 량 제로 설정 하거나, 모든 관리 효율성 모드 (에이전트 없음, 경량 및 전체)에서 SQL VM 리소스 공급자에 등록할 때 Azure 하이브리드 혜택 해야 합니다.

**SQL Server IaaS 확장을 에이전트 없음 모드에서 전체 모드로 업그레이드할 수 있나요?**

아니요. 에이전트 없는 모드에서는 관리 효율성 모드를 full 또는 lightweight로 업그레이드할 수 없습니다. 이는 Windows Server 2008에 대 한 기술적 제한 사항입니다. 먼저 OS를 Windows Server 2008 R2 이상으로 업그레이드 한 후에는 전체 관리 모드로 업그레이드할 수 있습니다. 

**SQL Server IaaS 확장을 경량 모드에서 전체 모드로 업그레이드할 수 있나요?**

예. 관리 효율성 모드를 경량에서 full로 업그레이드 하는 것은 PowerShell 또는 Azure Portal를 통해 지원 됩니다. SQL Server 서비스를 다시 시작 해야 합니다.

**SQL Server IaaS 확장을 전체 모드에서 에이전트 없음 또는 경량 관리 모드로 다운 그레이드할 수 있나요?**

아니요. SQL Server IaaS 확장 관리 효율성 모드의 다운 그레이드는 지원 되지 않습니다. 관리 효율성 모드는 전체 모드에서 경량 모드로 또는 에이전트 없음 모드로 다운 그레이드할 수 없으며 경량 모드에서 에이전트 없음 모드로 다운 그레이드할 수 없습니다. 

관리 효율성 모드를 전체 관리 기능으로 변경 하려면 SQL Server *리소스* 를 삭제 하 여 SQL Server 리소스 공급자에서 SQL Server 가상 컴퓨터의 [등록을 취소](#unregister-vm-from-rp) 하 고 SQL VM 리소스 공급자를 사용 하 여 SQL Server VM를 다른 관리 모드로 다시 등록 합니다.

**Azure Portal에서 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. Azure Portal에서는 SQL VM 리소스 공급자에 등록할 수 없습니다. SQL VM 리소스 공급자에 등록 하는 것은 Azure CLI 또는 PowerShell 에서만 지원 됩니다. 

**SQL Server 설치 하기 전에 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

아니요. VM에는 SQL VM 리소스 공급자에 성공적으로 등록 하려면 하나 이상의 SQL Server (데이터베이스 엔진) 인스턴스가 있어야 합니다. VM에 SQL Server 인스턴스가 없으면 새 SqlVirtualMachine 리소스가 실패 한 상태가 됩니다.

**SQL Server 인스턴스가 여러 개 있는 경우 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

예. SQL VM 리소스 공급자는 SQL Server (데이터베이스 엔진) 인스턴스를 하나만 등록 합니다. SQL VM 리소스 공급자는 여러 인스턴스의 경우 기본 SQL Server 인스턴스를 등록 합니다. 기본 인스턴스가 없으면 경량 모드의 등록만 지원 됩니다. 경량에서 전체 관리 모드로 업그레이드 하려면 기본 SQL Server 인스턴스가 있어야 합니다. 그렇지 않으면 VM에 명명 된 SQL Server 인스턴스가 하나만 있어야 합니다.

**SQL VM 리소스 공급자를 사용 하 여 SQL Server 장애 조치 (failover) 클러스터 인스턴스를 등록할 수 있나요?**

예. Azure VM의 SQL Server 장애 조치 (failover) 클러스터 인스턴스는 경량 모드의 SQL VM 리소스 공급자에 등록 될 수 있습니다. 그러나 SQL Server 장애 조치 (failover) 클러스터 인스턴스를 완전 한 관리 효율성 모드로 업그레이드할 수는 없습니다.

**Always On 가용성 그룹이 구성 된 경우에는 SQL VM 리소스 공급자를 사용 하 여 VM을 등록할 수 있나요?**

예. Always On 가용성 그룹 구성에 참여 하는 경우 SQL VM 리소스 공급자를 사용 하 여 Azure VM에 SQL Server 인스턴스를 등록 하는 데 제한 사항이 없습니다.

**SQL VM 리소스 공급자에 등록 하는 데 드는 비용이 나 완전 한 관리 모드로 업그레이드 하는 경우는 어떻게 되나요?**
없음 SQL VM 리소스 공급자에 등록 하거나 세 가지 관리 효율성 모드 중 하나를 사용 하는 것과 관련 된 요금은 없습니다. 리소스 공급자를 사용 하 여 SQL Server VM 관리는 완전히 무료입니다. 

**여러 관리 효율성 모드를 사용 하는 경우 성능에 미치는 영향은 무엇입니까?**
*Noagent* 및 *경량* 관리 효율성 모드를 사용 하는 경우에는 영향을 주지 않습니다. OS에 설치 된 두 서비스에서 *전체* 관리 효율성 모드를 사용 하는 경우 최소한의 영향을 줍니다. 이러한 작업은 작업 관리자를 통해 모니터링할 수 있으며 기본 제공 Windows 서비스 콘솔에 표시 됩니다. 

두 서비스 이름은 다음과 같습니다.
- `SqlIaaSExtensionQuery` (표시 이름-`Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (표시 이름-`Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)

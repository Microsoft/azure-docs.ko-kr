---
title: SQL VM 리소스 공급자에 등록
description: SQL VM 리소스 공급자를 사용 하 여 Azure SQL Server 가상 컴퓨터를 등록 하면 Azure Marketplace 외부에 배포 된 SQL Server 가상 컴퓨터에 대 한 기능을 사용 하도록 설정 하 고, 규정 준수 및 관리 효율성을 향상 시킬 수 있습니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f1a9a2756d81765d82938651672e5a83edc48ed
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078692"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>SQL VM 리소스 공급자 (RP)를 사용 하 여 Azure에 SQL Server VM 등록
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 SQL VM 리소스 공급자 (RP)를 사용 하 여 Azure에 SQL Server VM (가상 머신)을 등록 하는 방법을 설명 합니다. 리소스 공급자에 등록하면 가상 머신 리소스와는 별도의 리소스인 구독 내에서 **SQL 가상 머신** _리소스_가 만들어집니다. 리소스 공급자에서 SQL Server VM 등록을 취소하면 **SQL 가상 머신** _리소스_가 제거되지만 실제 가상 머신은 삭제되지 않습니다. 

Azure Portal을 통해 SQL Server VM Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 리소스 공급자에 등록됩니다. 그러나 Azure 가상 머신에서 SQL Server를 자동으로 설치하거나 사용자 지정 VHD에서 Azure 가상 머신을 프로비저닝하도록 선택하는 경우 사용자가 SQL Server VM을 리소스 공급자에 등록해야 합니다.

- **주요 이점**: SQL Server VM을 리소스 공급자에 등록하면 [자동화된 패치](automated-patching.md), [자동화된 백업](automated-backup.md), 모니터링 및 관리 기능이 잠금 해제됩니다. 또한 [라이선스](licensing-model-azure-hybrid-benefit-ahb-change.md) 및 [버전](change-sql-server-edition.md) 유연성이 잠금 해제됩니다. 이전에는 Azure Marketplace에서 배포된 SQL Server VM 이미지에만 이러한 기능을 사용할 수 있었습니다. 

- **규정 준수**: SQL VM 리소스 공급자에 등록하면 제품 약관에 지정된 대로 Azure 하이브리드 혜택이 구현되었음을 Microsoft에 알려야 하는 요구 사항을 간편하게 충족할 수 있습니다. 이 프로세스로 각 리소스마다 라이선스 등록 양식을 관리할 필요가 없습니다.  

- **무료 관리**: 세 가지 관리 효율성 모드 모두에서 SQL VM 리소스 공급자를 등록 하는 것은 완전히 무료입니다. 리소스 공급자 또는 관리 모드 변경과 관련된 추가 비용은 없습니다. 

- **간소화된 라이선스 관리**: SQL VM 리소스 공급자에 등록하면 SQL Server 라이선스 관리가 간소화되고 [Azure Portal](manage-sql-vm-portal.md), Azure CLI 또는 PowerShell을 사용하여 Azure 하이브리드 혜택이 활성화된 SQL Server VM을 빠르게 식별할 수 있습니다. 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

SQL VM 리소스 공급자를 활용 하려면 먼저 리소스 공급자에 [구독을 등록](#register-subscription-with-rp)해야 합니다. 그러면 리소스 공급자는 해당 특정 구독 내에서 리소스를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

리소스 공급자에 SQL Server VM을 등록하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- 공용 또는 Azure Government 클라우드에 배포된 Azure 리소스 모델 [SQL Server VM](create-sql-vm-portal.md). 
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli) 또는 [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>관리 모드

[SQL IaaS 확장](sql-server-iaas-agent-extension-automate-management.md)을 아직 설치하지 않은 경우 SQL VM 리소스 공급자에 등록하면 3가지 관리 모드 중 등록 프로세스에서 지정한 모드로 SQL Server IaaS 확장이 자동 설치됩니다. 관리 모드를 지정하지 않으면 SQL IaaS 확장이 전체 관리 모드로 설치됩니다.  

SQL IaaS 확장을 이미 수동으로 설치한 경우에는 이미 전체 관리 모드이기 때문에 리소스 공급자에 전체 모드로 등록하더라도 SQL Server 서비스가 다시 시작되지 않습니다.

세 가지 관리 모드는 다음과 같습니다.

- **경량** 모드에서는 SQL Server를 다시 시작할 필요가 없지만 SQL Server 버전 및 라이선스 유형의 변경만 지원합니다. 여러 인스턴스가 있는 SQL Server VM에서 또는 FCI(장애 조치(failover) 클러스터 인스턴스)에 참여하는 경우 이 옵션을 사용합니다. 경량 모드를 사용하는 경우 메모리 또는 CPU에 영향을 주지 않으며 관련 비용이 없습니다. 먼저 SQL Server VM을 경량 모드로 등록한 다음, 예약된 유지 관리 기간 동안 전체 모드로 업그레이드하는 것이 좋습니다.  

- **전체** 모드에서는 모든 기능을 제공하지만 SQL Server 재시작과 시스템 관리자 권한이 필요합니다. SQL IaaS 확장을 수동으로 설치할 때 기본적으로 이 옵션이 설치됩니다. 단일 인스턴스가 있는 SQL Server VM을 관리하는 데 이 옵션을 사용합니다. 전체 모드에서는 메모리 및 CPU에 최소한의 영향을 주는 두 개의 Windows 서비스가 설치됩니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있습니다. 전체 관리 모드를 사용하는 것과 관련된 비용은 없습니다. 

- **에이전트 없음** 모드는 Windows Server 2008에 설치된 SQL Server 2008 및 SQL Server 2008 R2 전용 모드입니다. 에이전트 없음 모드를 사용하는 경우 메모리 또는 CPU에는 영향을 주지 않습니다. 에이전트 없음 관리 모드 사용과 관련된 비용은 없습니다. 

PowerShell을 사용하여 SQL Server IaaS 에이전트의 현재 모드를 확인할 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>RP에 구독 등록

SQL Server VM을 SQL 리소스 공급자에 등록하려면 먼저 구독을 리소스 공급자에 등록해야 합니다. 그러면 SQL VM 리소스 공급자가 구독 내에서 리소스를 만들 수 있습니다.  Azure Portal, Azure CLI 또는 PowerShell을 사용하여 이 작업을 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. Azure Portal을 열고 **모든 서비스**로 이동합니다. 
1. **구독**으로 이동하여 원하는 구독을 선택합니다.  
1. **구독** 페이지에서 **리소스 공급자**로 이동합니다. 
1. 필터에 **sql**을 입력하여 SQL 관련 리소스 공급자를 표시합니다. 
1. 원하는 작업에 따라 **Microsoft.SqlVirtualMachine** 공급자에 대해 **등록**, **다시 등록** 또는 **등록 해제**를 선택합니다. 

   ![공급자 수정](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>명령 줄

Azure CLI 또는 PowerShell을 사용하여 Azure 구독에 SQL VM 리소스 공급자를 등록합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>RP로 등록

### <a name="lightweight-management-mode"></a>경량 관리 모드

[SQL Server IaaS 에이전트 확장이](sql-server-iaas-agent-extension-automate-management.md) 가상 컴퓨터에 설치 되어 있지 않은 경우 SQL VM 리소스 공급자를 사용 하 여 경량 모드로 등록 하는 것이 좋습니다. 그러면 SQL IaaS 확장이 [경량 모드](#management-modes)로 설치되고 SQL Server 서비스가 다시 시작되지 않습니다. 그런 다음, 언제든지 전체 모드로 업그레이드할 수 있지만 이렇게 하면 SQL Server 서비스가 다시 시작되므로 예약된 유지 관리 기간이 도래할 때까지 기다리는 것이 좋습니다. 

SQL Server 라이선스 유형을 다음과 같이 제공합니다. 사용량에 따라 지불하려면 종량제(`PAYG`), 자체 라이선스를 사용하려면 Azure 하이브리드 혜택(`AHUB`), [무료 DR 복제본 라이선스](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)를 활성화하려면 재해 복구(`DR`)입니다.

장애 조치 (Failover) 클러스터 인스턴스 및 다중 인스턴스 배포는 경량 모드의 SQL VM 리소스 공급자에만 등록할 수 있습니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용 하 여 SQL Server VM를 경량 모드로 등록 합니다. 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 SQL Server VM를 경량 모드로 등록 합니다.  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>전체 관리 모드


SQL IaaS 확장이 이미 가상 컴퓨터에 수동으로 설치 된 경우 SQL Server 서비스를 다시 시작 하지 않고 SQL Server VM를 전체 모드로 등록할 수 있습니다. **그러나 SQL IaaS 확장이 설치되지 않은 경우 전체 모드로 등록하면 SQL IaaS 확장이 전체 모드로 설치되고 SQL Server 서비스가 다시 시작합니다. 주의하여 진행하세요.**


SQL Server VM을 전체 모드로 직접 등록하고 SQL Server 서비스를 다시 시작하려면 다음 PowerShell 명령을 사용합니다. 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>에이전트 없음 관리 모드 

Windows Server 2008(_R2_가 아님)에 설치된 SQL Server 2008 및 2008 R2는 SQL VM 리소스 공급자에 [에이전트 없음 모드](#management-modes)로 등록할 수 있습니다. 이 옵션은 규정 준수를 보장하고 제한된 기능으로 Azure Portal에서 SQL Server VM을 모니터링할 수 있도록 합니다.

**sqlLicenseType**으로 `AHUB`, `PAYG`는 `DR`지정하고 **sqlImageOffer**로 `SQL2008-WS2008` 또는 `SQL2008R2-WS2008`을 지정합니다. 

Windows Server 2008 인스턴스의 SQL Server 2008 또는 2008 R2를 등록하려면 다음 Azure CLI 또는 PowerShell 코드 조각을 사용합니다. 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Azure CLI를 사용 하 여 NoAgent 모드에서 SQL Server 2008 가상 컴퓨터를 등록 합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Azure CLI를 사용 하 여 NoAgent 모드에서 SQL Server 2008 R2 가상 머신을 등록 합니다. 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 NoAgent 모드에서 SQL Server 2008 가상 컴퓨터를 등록 합니다. 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  PowerShell을 사용 하 여 NoAgent 모드에서 SQL Server 2008 R2 가상 머신을 등록 합니다. 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>전체로 업그레이드  

*경량* IaaS 확장이 설치된 SQL Server VM은 Azure Portal, Azure CLI 또는 PowerShell을 사용하여 _전체_ 모드로 업그레이드할 수 있습니다. _에이전트 없음_ 모드의 SQL Server VM은 OS를 Windows 2008 R2 이상으로 업그레이드한 후 _전체_ 모드로 업그레이드할 수 있습니다. 다운그레이드할 수는 없습니다. 그러려면 SQL Server VM을 SQL VM 리소스 공급자에서 [등록 취소](#unregister-from-rp)해야 합니다. 이렇게 하면 **SQL 가상 머신** _리소스_가 제거되지만 실제 가상 머신은 삭제되지 않습니다. 

PowerShell을 사용하여 SQL Server IaaS 에이전트의 현재 모드를 확인할 수 있습니다. 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

에이전트 모드를 전체로 업그레이드하려면 다음을 수행합니다. 


### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [SQL 가상 머신](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) 리소스로 이동합니다. 
1. SQL Server VM를 선택 하 고 **개요**를 선택 합니다. 
1. SQL Server VM이 에이전트 없음 또는 경량 IaaS 모드인 경우 **SQL IaaS 확장에서는 라이선스 유형 및 버전 업데이트만 사용할 수 있습니다** 메시지를 선택합니다.

   ![포털에서 모드를 변경하기 위한 선택 항목](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. **가상 머신에서 SQL Server 서비스를 다시 시작하는 데 동의합니다.** 확인란을 선택한 다음, **확인**을 선택하여 IaaS 모드를 전체로 업그레이드합니다. 

    ![가상 머신에서 SQL Server 서비스를 다시 시작하는 데 동의하는 확인란](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

다음 Azure CLI 코드 조각을 실행합니다.

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

아래 PowerShell 코드 조각을 실행합니다.

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>등록 상태 확인
Azure Portal, Azure CLI 또는 PowerShell을 사용하여 SQL Server VM이 이미 SQL VM 리소스 공급자에 등록되어 있는지 확인할 수 있습니다. 

### <a name="azure-portal"></a>Azure portal 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. [SQL Server vm](manage-sql-vm-portal.md)으로 이동 합니다.
1. 목록에서 SQL Server VM을 선택합니다. SQL Server VM이 여기에 나열되지 않은 경우 SQL VM 리소스 공급자에 등록되지 않았을 수 있습니다. 
1. **상태**에서 값을 확인합니다. **상태**가 **성공**인 경우 SQL Server VM이 SQL VM 리소스 공급자에 등록되어 있는 것입니다. 

   ![SQL RP 등록 상태 확인](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>명령 줄

Azure CLI 또는 PowerShell을 사용하여 현재 SQL Server VM 등록 상태를 확인합니다. 등록이 성공하면 `ProvisioningState`가 `Succeeded`로 표시됩니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

오류는 SQL Server VM이 리소스 공급자에 등록되지 않은 것을 나타냅니다. 


## <a name="unregister-from-rp"></a>RP에서 등록 취소

SQL VM 리소스 공급자를 사용 하 여 SQL Server VM 등록을 취소 하려면 Azure Portal 또는 Azure CLI를 사용 하 여 SQL 가상 머신 *리소스* 를 삭제 합니다. SQL 가상 컴퓨터 *리소스* 를 삭제 해도 SQL Server VM는 삭제 되지 않습니다. 그러나 *리소스*를 제거하려고 할 때 실수로 가상 머신을 삭제할 수 있으므로 신중하게 단계를 수행해야 합니다. 

Sql VM 리소스 공급자를 사용 하 여 SQL 가상 머신의 등록을 취소 하려면 관리 모드를 전체로 다운 그레이드 해야 합니다. 

### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 SQL Server VM을 리소스 공급자에서 등록 취소하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. SQL VM 리소스로 이동 합니다. 
  
   ![SQL 가상 머신 리소스](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. **삭제**를 선택합니다. 

   ![SQL VM 리소스 공급자 삭제](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. SQL 가상 컴퓨터의 이름을 입력 하 고 **가상 컴퓨터 옆에 있는 확인란의 선택을 취소**합니다.

   ![SQL VM 리소스 공급자 삭제](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > 가상 머신 이름 옆의 확인란 선택을 취소하지 않으면 가상 머신이 완전히 *삭제*됩니다. 이 확인란 선택을 취소하여 SQL Server VM을 리소스 공급자에서 등록 취소하되 *실제 가상 머신은 삭제하지 않습니다*. 

1. **삭제** 를 선택 하 여 SQL SERVER VM 아닌 SQL 가상 컴퓨터 *리소스*삭제를 확인 합니다. 

### <a name="command-line"></a>명령 줄

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용 하 여 리소스 공급자에서 SQL Server VM 등록을 취소 하려면 [az SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) 명령을 사용 합니다. 그러면 SQL Server VM *리소스가* 제거 되지만 가상 컴퓨터는 삭제 되지 않습니다. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Azure CLI를 사용 하 여 리소스 공급자에서 SQL Server VM 등록을 취소 하려면 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)명령을 사용 합니다. 그러면 SQL Server VM *리소스가* 제거 되지만 가상 컴퓨터는 삭제 되지 않습니다. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>제한 사항

SQL VM 리소스 공급자는 다음만 지원합니다.
- Azure Resource Manager를 통해 배포된 SQL Server VM. 클래식 모델을 통해 배포된 SQL Server VM은 지원되지 않습니다. 
- 공용 또는 Azure Government 클라우드에 배포된 SQL Server VM. 다른 프라이빗 또는 정부 클라우드로의 배포는 지원되지 않습니다. 


## <a name="frequently-asked-questions"></a>질문과 대답 

**Azure Marketplace의 SQL Server 이미지에서 프로비저닝된 SQL Server VM을 등록해야 하나요?**

아니요. Microsoft는 Azure Marketplace의 SQL Server 이미지에서 프로비저닝된 VM을 자동으로 등록합니다. VM이 Azure Marketplace의 SQL Server 이미지에서 프로비저닝되지 *않고* SQL Server가 자체 설치된 경우에만 SQL VM 리소스 공급자 등록이 필요합니다.

**SQL VM 리소스 공급자를 모든 고객이 사용할 수 있나요?** 

예. 고객이 Azure Marketplace의 SQL Server 이미지를 사용하지 않고 대신 자체 설치된 SQL Server를 사용하거나 사용자 지정 VHD를 사용하는 경우 SQL Server VM을 SQL VM 리소스 공급자에 등록해야 합니다. 모든 유형의 구독(직접, 기업계약 및 클라우드 솔루션 공급자)이 소유한 VM은 SQL VM 리소스 공급자에 등록할 수 있습니다.

**내 SQL Server VM에 SQL Server IaaS 확장이 이미 설치되어 있는 경우 SQL VM 리소스 공급자에 등록해야 하나요?**

SQL Server VM이 자체 설치되고 Azure Marketplace의 SQL Server 이미지에서 프로비저닝되지 않은 경우 SQL Server IaaS 확장을 설치한 경우에도 SQL VM 리소스 공급자에 등록해야 합니다. SQL VM 리소스 공급자를 사용 하 여 등록 하면 SqlVirtualMachine 형식의 새 리소스가 생성 됩니다. SQL Server IaaS 확장을 설치하면 이 리소스가 만들어지지 않습니다.

**SQL VM 리소스 공급자에 등록할 때 기본 관리 모드는 무엇입니까?**

SQL VM 리소스 공급자에 등록할 때의 기본 관리 모드는 *전체*입니다. SQL VM 리소스 공급자에 등록할 때 SQL Server 관리 속성이 설정되지 않은 경우 모드가 전체 관리로 설정되고 SQL Server 서비스가 다시 시작됩니다. 먼저 경량 모드에서 SQL VM 리소스 공급자에 등록한 다음, 유지 관리 기간 중에 전체로 업그레이드하는 것이 좋습니다. 

**SQL VM 리소스 공급자에 등록하기 위한 필수 구성 요소는 무엇인가요?**

SQL VM 리소스 공급자에 경량 모드 또는 에이전트 없음 모드로 등록하기 위한 필수 구성 요소는 없습니다. SQL VM 리소스 공급자에 전체 모드로 등록하기 위한 필수 구성 요소는 SQL Server IaaS 확장이 VM에 설치되어 있는 것입니다. 이 경우 SQL Server 서비스가 다시 시작됩니다. 

**SQL Server IaaS 확장이 VM에 설치되어 있지 않아도 SQL VM 리소스 공급자에 등록할 수 있나요?**

예, SQL Server IaaS 확장이 VM에 설치되어 있지 않은 경우 경량 관리 모드로 SQL VM 리소스 공급자에 등록할 수 있습니다. 경량 모드에서 SQL VM 리소스 공급자는 콘솔 앱을 사용하여 SQL Server 인스턴스의 버전 및 에디션을 확인합니다. 

SQL VM 리소스 공급자에 등록할 때 기본 SQL 관리 모드는 _전체_ 모드입니다. SQL VM 리소스 공급자에 등록할 때 SQL 관리 속성을 설정하지 않으면 모드가 전체 관리로 설정됩니다. 먼저 경량 모드에서 SQL VM 리소스 공급자에 등록한 다음, 유지 관리 기간 중에 전체로 업그레이드하는 것이 좋습니다. 

**SQL VM 리소스 공급자에 등록하면 내 VM에 에이전트를 설치하나요?**

예, SQL VM 리소스 공급자를 사용 하 여 등록 하면 VM에 에이전트가 설치 됩니다.

SQL Server IaaS 확장은 에이전트를 사용 하 여 SQL Server 메타 데이터를 쿼리 합니다. SQL VM 리소스 공급자가 NoAgent 모드의 경우에만 에이전트가 설치 되지 않습니다.

**내 VM에서 SQL VM 리소스 공급자를 다시 시작 SQL Server를 등록 하 시겠습니까?**

등록 중 지정한 모드에 따라 달라집니다. 경량 또는 에이전트 없음 모드를 지정한 경우 SQL Server 서비스는 다시 시작되지 않습니다. 그러나 관리 모드를 전체로 지정하거나 관리 모드를 비워 두면 SQL IaaS 확장이 전체 관리 모드로 설치됩니다. 그러면 SQL Server 서비스가 다시 시작됩니다. 

**SQL VM 리소스 공급자에 등록할 때 경량 관리 모드와 에이전트 없음 관리 모드의 차이점은 무엇인가요?** 

에이전트 없음 관리 모드는 Windows Server 2008에 설치된 SQL Server 2008 및 SQL Server 2008 R2에만 사용할 수 있습니다. 이것이 이러한 버전에 유일하게 사용할 수 있는 관리 모드입니다. 다른 모든 버전의 SQL Server에서는 사용 가능한 관리 모드가 경량 및 전체 두 가지입니다. 

에이전트 없음 모드를 사용하려면 고객이 SQL Server 버전 및 에디션 속성을 설정해야 합니다. 경량 모드는 VM을 쿼리하여 SQL Server 인스턴스의 버전 및 에디션을 찾습니다.

**SQL Server 라이선스 유형을 지정하지 않고 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. SQL VM 리소스 공급자에 등록하는 경우 SQL Server 라이선스 형식은 선택적 속성이 아닙니다. SQL VM 리소스 공급자에 등록할 때 모든 관리 모드(에이전트 없음, 경량 및 전체)에서 SQL Server 라이선스 유형을 종량제 또는 Azure 하이브리드 혜택으로 설정해야 합니다.

**SQL Server IaaS 확장을 에이전트 없음 모드에서 전체 모드로 업그레이드할 수 있나요?**

아니요. 에이전트 없음 모드에서는 관리 모드를 전체 또는 경량으로 업그레이드할 수 없습니다. 이는 Windows Server 2008의 기술적 제한 사항입니다. 먼저 OS를 Windows Server 2008 R2 이상으로 업그레이드한 후에는 전체 관리 모드로 업그레이드할 수 있습니다. 

**SQL Server IaaS 확장을 경량 모드에서 전체 모드로 업그레이드할 수 있나요?**

예. 관리 모드를 경량에서 전체로 업그레이드하는 작업은 PowerShell 또는 Azure Portal을 통해 지원됩니다. 이때 SQL Server 서비스를 다시 시작해야 합니다.

**SQL Server IaaS 확장을 전체 모드에서 에이전트 없음 또는 경량 관리 모드로 다운그레이드할 수 있나요?**

아니요. SQL Server IaaS 확장 관리 모드는 다운그레이드가 지원되지 않습니다. 관리 모드는 전체 모드에서 경량 모드 또는 에이전트 없음 모드로 다운그레이드할 수 없으며 경량 모드에서 에이전트 없음 모드로 다운그레이드할 수 없습니다. 

관리 효율성 모드를 전체 관리 기능으로 변경 하려면 SQL Server *리소스* 를 삭제 하 여 sql vm 리소스 공급자에서 SQL Server VM [등록을 취소](#unregister-from-rp) 하 고 다른 관리 모드에서 다시 sql vm 리소스 공급자를 사용 하 여 SQL Server VM를 다시 등록 합니다.

**Azure Portal에서 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. Azure Portal에서는 SQL VM 리소스 공급자에 등록할 수 없습니다. SQL VM 리소스 공급자 등록은 Azure CLI 또는 PowerShell에서만 지원됩니다. 

**SQL Server를 설치하기 전에 VM을 SQL VM 리소스 공급자에 등록할 수 있나요?**

아니요. VM은 하나 이상의 SQL Server(데이터베이스 엔진) 인스턴스가 있어야 SQL VM 리소스 공급자에 성공적으로 등록할 수 있습니다. VM에 SQL Server 인스턴스가 없으면 새 Microsoft.SqlVirtualMachine 리소스가 실패 상태가 됩니다.

**SQL Server 인스턴스가 여러 개 있는 경우 VM을 SQL VM 리소스 공급자에 등록할 수 있나요?**

예. SQL VM 리소스 공급자는 SQL Server(데이터베이스 엔진) 인스턴스를 하나만 등록합니다. SQL VM 리소스 공급자는 인스턴스가 여러 개인 경우 기본 SQL Server 인스턴스를 등록합니다. 기본 인스턴스가 없으면 경량 모드 등록만 지원됩니다. 경량에서 전체 관리 모드로 업그레이드하려면 기본 SQL Server 인스턴스가 있어야 합니다. 그렇지 않으면 VM에 명명된 SQL Server 인스턴스가 하나만 있어야 합니다.

**SQL Server 장애 조치(failover) 클러스터 인스턴스를 SQL VM 리소스 공급자에 등록할 수 있나요?**

예. Azure VM의 SQL Server 장애 조치(failover) 클러스터 인스턴스는 경량 모드로 SQL VM 리소스 공급자에 등록할 수 있습니다. 그러나 SQL Server 장애 조치(failover) 클러스터 인스턴스를 전체 관리 모드로 업그레이드할 수는 없습니다.

**Always On 가용성 그룹이 구성된 경우에는 VM을 SQL VM 리소스 공급자에 등록할 수 있나요?**

예. Always On 가용성 그룹 구성에 참여하더라도 Azure VM의 SQL Server 인스턴스를 SQL VM 리소스 공급자에 등록하는 데 제한이 없습니다.

**SQL VM 리소스 공급자에 등록하거나 전체 관리 모드로 업그레이드하는 경우 비용은 어떻게 되나요?**
없음 SQL VM 리소스 공급자에 등록하거나 세 가지 관리 모드 중 하나를 사용하는 것과 관련된 요금은 없습니다. 리소스 공급자를 사용한 SQL Server VM 관리는 완전히 무료입니다. 

**각 관리 모드를 사용할 때 성능에 미치는 영향은 무엇입니까?**
*에이전트 없음* 및 *경량* 관리 모드를 사용하는 경우에는 영향을 미치지 않습니다. OS에 설치된 두 서비스로부터 *전체* 관리 모드를 사용하는 경우 최소한의 영향만 미칩니다. 이러한 서비스는 작업 관리자를 통해 모니터링할 수 있으며 기본 제공 Windows 서비스 콘솔에 표시됩니다. 

두 서비스 이름은 다음과 같습니다.
- `SqlIaaSExtensionQuery`(표시 이름 - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension`(표시 이름 - `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.md)  
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](../../database/doc-changes-updates-release-notes.md)

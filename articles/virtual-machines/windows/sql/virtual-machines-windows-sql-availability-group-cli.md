---
title: Azure SQL VM CLI를 사용 하 여 Azure VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성
description: 'Azure CLI를 사용 하 여 Azure에서 SQL Server VM에서 Windows 장애 조치 클러스터, 가용성 그룹 수신기 및 내부 Load Balancer를 만듭니다. '
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
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591283"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure SQL VM CLI를 사용 하 여 Azure VM에서 SQL Server에 대 한 Always On 가용성 그룹 구성
이 문서에서는 사용 하는 방법을 설명 [Azure SQL VM CLI](/cli/azure/sql/vm?view=azure-cli-latest/) Windows 장애 조치 클러스터 (WSFC) 배포 및 SQL Server Vm 클러스터를 추가할 뿐만 내부 Load Balancer 및 Always On 가용성 그룹에 대 한 수신기를 만듭니다.  Always On 가용성 그룹의 실제 배포도 이루어집니다 수동으로 SQL Server Management Studio (SSMS)를 통해. 

## <a name="prerequisites"></a>필수 조건
Azure SQL VM CLI를 사용 하 여 Always On 가용성 그룹의 설치를 자동화 하려면 다음 필수 구성 요소를 이미 있어야 할: 
- [Azure 구독](https://azure.microsoft.com/free/).
- 도메인 컨트롤러를 포함하는 리소스 그룹 
- 하나 이상의 도메인에 가입 된 [Vm에 Azure 실행 중인 SQL Server 2016 (이상) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 에 *동일한 가용성 집합 또는 다른 가용성 영역* 된 [등록 SQL VM 리소스 공급자를 사용 하 여](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)입니다.  
- [Azure CLI](/cli/azure/install-azure-cli). 
- (모든 엔터티에서 사용 되지 않음)는 두 사용 가능한 IP 주소, 내부 Load Balancer 및 가용성 그룹과 동일한 서브넷 내에서 가용성 그룹 수신기에 대 한 합니다. 기존 부하 분산 장치를 사용 중 하나만 사용할 수 있는 IP 주소는 가용성 그룹 수신기에 필요 합니다. 

## <a name="permissions"></a>사용 권한
다음 계정 권한은 SQL VM Azure CLI를 사용 하 여 Always On 가용성 그룹을 구성 해야 합니다. 

- 기존 도메인 사용자 계정 도메인의 컴퓨터 개체 만들기 ' 권한이 있는 합니다.  예를 들어 도메인 관리자 계정에는 일반적으로 충분한 권한이 있습니다(예: account@domain.com). 또한 이 계정은 클러스터를 만들 각 VM의 로컬 관리자 그룹에 속해 있어야 합니다.
- SQL Server 서비스를 제어 하는 도메인 사용자 계정입니다. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>1 단계-클라우드 감시를 저장소 계정 만들기
클러스터에 저장소 계정을 클라우드 미러링 모니터 서버 역할을 해야 합니다. 모든 기존 저장소 계정을 사용할 수 있습니다 또는 새 저장소 계정을 만들 수 있습니다. 기존 저장소 계정을 사용 하려는 경우 다음 섹션을 건너뜁니다. 

다음 코드 조각에는 저장소 계정을 만듭니다. 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > 오류가 표시 될 수 있습니다 `az sql: 'vm' is not in the 'az sql' command group` 오래 된 버전의 Azure CLI를 사용 하는 경우. 다운로드 합니다 [최신 버전의 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) 이 오류를 통과 하도록 합니다.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>2 단계-Windows 장애 조치 클러스터 메타 데이터를 정의 합니다.
Azure SQL VM CLI [az sql vm 그룹](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) 그룹에서 가용성 그룹을 호스팅하는 Windows 장애 조치 클러스터 (WSFC) 서비스의 메타 데이터를 관리 하는 명령입니다. 클러스터 메타 데이터에는 AD 도메인, 클러스터 계정, 클라우드 감시 및 SQL Server 버전으로 사용할 저장소 계정을 포함 합니다. 사용 하 여 [az sql vm 그룹 만들기](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) 는 첫 번째 SQL Server VM을 추가할 때 있게 WSFC에 대 한 메타 데이터를 정의 정의 된 대로 클러스터 생성 됩니다. 

다음 코드 조각은 클러스터에 대 한 메타 데이터를 정의합니다.
```azurecli
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3---add-sql-server-vms-to-cluster"></a>3 단계-클러스터에 SQL Server Vm 추가
클러스터를 만드는 첫 번째 SQL Server VM을 클러스터에 추가 합니다. 합니다 [az sql vm-그룹에 추가-](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) 명령 이전에 지정 된 이름을 사용 하 여 클러스터를 만드는 SQL Server Vm에서 클러스터 역할을 설치 하 고 클러스터에 추가 합니다. 이후 사용을 `az sql vm add-to-group` 명령은 새로 만든된 클러스터에 추가 SQL Server Vm을 추가 합니다. 

다음 코드 조각은 클러스터를 만들고 첫 번째 SQL Server VM을 추가 합니다. 

```azurecli
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
이 명령을 사용 하 여이 클러스터에 다른 SQL Server Vm을 추가 수정만 `-n` SQL Server VM 이름에 대 한 매개 변수입니다. 

## <a name="step-4---create-availability-group"></a>4 단계-가용성 그룹 만들기
평소와 같이, 하나를 사용 하 여 가용성 그룹을 수동으로 만들 [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)를 [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), 또는 [TRANSACT-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)합니다. 

  >[!IMPORTANT]
  > 수행할 **되지** 이 다음 섹션에서 Azure CLI를 통해 수행 되기 때문에이 시점에서 수신기를 만들어야 합니다.  

## <a name="step-5---create-internal-load-balancer"></a>5 단계-내부 부하 분산 장치 만들기

Always On 가용성 그룹 (AG) 수신기는 내부 Azure 부하 분산 장치 (ILB) 필요합니다. ILB는 AG 수신기에 대해 “부동” IP 주소를 제공하므로 더 빠른 장애 조치(failover) 및 재연결이 가능합니다. 가용성 그룹의 SQL Server VM이 동일한 가용성 집합의 일부인 경우 기본 Load Balancer를 사용할 수 있습니다. 그렇지 않으면 표준 Load Balancer를 사용해야 합니다.  **ILB는 SQL Server VM 인스턴스와 동일한 VNet에 있어야 합니다.** 

다음 코드 조각 내부 Load Balancer를 만듭니다.

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > 각 SQL Server VM에 대한 공용 IP 리소스에 표준 Load Balancer와 호환되는 표준 SKU가 있어야 합니다. VM 공용 IP 리소스의 SKU를 확인하려면 **리소스 그룹**으로 이동하여 원하는 SQL Server VM에 대한 **공용 IP 주소** 리소스를 선택하고 **개요** 창의 **SKU** 아래에서 값을 찾습니다.  

## <a name="step-6---create-availability-group-listener"></a>6 단계-가용성 그룹 수신기 만들기
가용성 그룹을 수동으로 만든 후 사용 하 여 수신기를 만들 수 있습니다 [az sql vm ag 수신기](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)합니다. 


- 합니다 **서브넷 리소스 ID** 의 값인 `/subnets/<subnetname>` vNet 리소스의 리소스 ID를 추가 합니다. 서브넷 리소스 ID를 식별 하려면 다음을 수행 합니다.
   1. 리소스 그룹으로 이동 합니다 [Azure portal](https://portal.azure.com)합니다. 
   1. VNet 리소스를 선택 합니다. 
   1. 선택 **속성** 에 **설정** 창입니다. 
   1. VNet에 대 한 리소스 ID를 식별 하 고 추가 `/subnets/<subnetname>`서브넷 리소스 ID를 만들려면의 끝에 예를 들면 다음과 같습니다.
        - 내 vNet 리소스 ID는: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - 내 서브넷 이름이 `default`합니다.
        - 따라서 서브넷 리소스 ID는 있습니다. `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


다음 코드 조각 가용성 그룹 수신기를 만듭니다.

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>가용성 그룹의 복제본 수를 수정 합니다.
리소스 및 리소스 공급자에 의해 관리 되는 이제는 Azure에서 호스팅되는 SQL Server Vm을 가용성 그룹에 배포할 때 복잡성이 추가 계층 없는 `virtual machine group`합니다. 따라서를 추가 또는 가용성 그룹에 복제본을 제거할 때 SQL Server Vm에 대 한 정보를 사용 하 여 수신기 메타 데이터를 업데이트 하는 추가 단계가입니다. 따라서 가용성 그룹에 있는 복제본의 수를 수정할 때 사용 해야 합니다 [az sql vm 그룹 ag 수신기 업데이트](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) SQL Server Vm의 메타 데이터를 사용 하 여 수신기를 업데이트 하는 명령입니다. 


### <a name="add-a-replica"></a>복제본 추가

새 복제본을 가용성 그룹에 추가 하려면 다음을 수행 합니다.

1. 클러스터에 SQL Server VM을 추가 합니다.
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. SQL Server Management Studio (SSMS)를 사용 하 여 가용성 그룹 내 복제본으로 SQL Server 인스턴스를 추가 합니다.
1. 수신기에 SQL Server VM 메타 데이터를 추가 합니다.
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>복제본 제거

복제본을 가용성 그룹에서 제거 하려면 다음을 수행 합니다.

1. SQL Server Management Studio (SSMS)를 사용 하 여 가용성 그룹에서 복제본을 제거 합니다. 
1. 수신기에서 SQL Server VM 메타 데이터를 제거 합니다.
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. 클러스터에서 SQL Server VM을 제거 합니다.
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>가용성 그룹 수신기 제거
나중에 Azure CLI를 사용 하 여 구성 된 가용성 그룹 수신기를 제거 해야 하는 경우에 SQL VM 리소스 공급자를 통해 이동 해야 합니다. 수신기가 SQL VM 리소스 공급자를 통해 등록되었으므로 SQL Server Management Studio를 통해 수신기를 삭제하는 것만으로는 충분하지 않습니다. 실제로 삭제할 Azure CLI를 사용 하 여 SQL VM 리소스 공급자를 통해. 이렇게 하면 SQL VM 리소스 공급자에서 AG 수신기 메타데이터가 제거되고 가용성 그룹에서 수신기가 물리적으로 삭제됩니다. 

다음 코드 조각은 SQL 리소스 공급자 및 가용성 그룹에서 SQL 가용성 그룹 수신기를 삭제합니다. 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [SQL Server VM 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM에 대한 라이선스 모델 전환](virtual-machines-windows-sql-ahb.md)
* [Always On 가용성 그룹 개요 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always On 가용성 그룹에 대 한 서버 인스턴스 구성 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [가용성 그룹 관리 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [가용성 그룹의 모니터링 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Always On 가용성 그룹에 대 한 TRANSACT-SQL 문 개요 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Always On 가용성 그룹에 대 한 PowerShell Cmdlet 개요 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

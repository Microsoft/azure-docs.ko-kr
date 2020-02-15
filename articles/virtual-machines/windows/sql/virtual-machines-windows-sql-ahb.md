---
title: Azure에서 SQL Server VM에 대 한 라이선스 모델 변경
description: Azure 하이브리드 혜택를 사용 하 여 Azure에서 SQL Server 가상 머신의 라이선스를 종 량 제로 전환 하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201828"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Azure에서 SQL Server 가상 컴퓨터에 대 한 라이선스 모델 변경
이 문서에서는 **SqlVirtualMachine**새 SQL vm 리소스 공급자를 사용 하 여 AZURE에서 vm (가상 머신)에 대 SQL Server 한 라이선스 모델을 변경 하는 방법을 설명 합니다.

SQL Server 호스트 하는 VM에 대 한 세 가지 라이선스 모델 (종 량 제, Azure 하이브리드 혜택 및 DR (재해 복구)이 있습니다. Azure Portal, Azure CLI 또는 PowerShell을 사용 하 여 SQL Server VM의 라이선스 모델을 수정할 수 있습니다. 

- **종 량** 제 모델은 Azure VM을 실행 하는 초당 비용에 SQL Server 라이선스 비용이 포함 됨을 의미 합니다.
- [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 를 사용 하면 SQL Server를 실행 하는 VM에서 사용자 고유의 SQL Server 라이선스를 사용할 수 있습니다. 
- **재해 복구** 라이선스 유형은 Azure에서 [무료 DR 복제본](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) 에 사용 됩니다. 

Azure 하이브리드 혜택를 사용 하면 Azure 가상 머신에서 소프트웨어 보증 ("적격 라이선스")이 있는 SQL Server 라이선스를 사용할 수 있습니다. Azure 하이브리드 혜택을 통해 고객은 VM에서 SQL Server 라이선스 사용에 대 한 요금이 부과 되지 않습니다. 하지만 기본 요금, 저장소 및 백업에 대 한 비용을 지불 해야 합니다. 서비스의 사용과 관련 된 i/o (해당 하는 경우)에 대해서도 요금을 지불 해야 합니다.

Microsoft 제품 약관에 따라 "고객은 Azure에서 Azure SQL Database (Managed Instance, Elastic Pool, Single Database), Azure Data Factory, SQL Server Integration Services 또는 SQL Server Virtual Machines를 사용 하 고 있음을 나타내야 합니다. Azure에서 워크 로드를 구성할 때 SQL Server에 대 한 하이브리드 혜택 "

Azure VM에서 SQL Server에 Azure 하이브리드 혜택를 사용 하 고 정책을 준수 하도록 지정 하려면 다음 세 가지 옵션을 사용할 수 있습니다.

- Azure Marketplace에서 사용자 라이선스 가져오기 SQL Server 이미지를 사용 하 여 가상 머신을 프로 비전 합니다. 이 옵션은 기업계약 있는 고객만 사용할 수 있습니다.
- Azure Marketplace에서 종 량 제 SQL Server 이미지를 사용 하 여 가상 머신을 프로 비전 하 고 Azure 하이브리드 혜택를 활성화 합니다.
- Azure VM에서 자동 설치 SQL Server 하 고, [SQL VM 리소스 공급자를 사용](virtual-machines-windows-sql-register-with-resource-provider.md)하 여 수동으로 등록 하 고, Azure 하이브리드 혜택를 활성화 합니다.

VM이 프로 비전 될 때 SQL Server의 라이선스 유형이 설정 됩니다. 나중에 언제 든 지 변경할 수 있습니다. 라이선스 모델 간을 전환 하면 가동 중지 시간이 발생 하지 않으며, VM 또는 SQL Server 서비스를 다시 시작 하지 않고 추가 비용을 추가 하지 않으며 즉시 적용 됩니다. 실제로 Azure 하이브리드 혜택를 활성화 하면 비용이 *절감* 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

SQL Server VM 라이선스 모델을 변경 하려면 다음 요구 사항이 충족 되어야 합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL VM 리소스 공급자](virtual-machines-windows-sql-register-with-resource-provider.md)에 등록 된 [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 입니다.
- [소프트웨어 보증](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) 은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)를 활용 하기 위한 요구 사항입니다. 


## <a name="vms-already-registered-with-the-resource-provider"></a>리소스 공급자에 이미 등록 된 Vm 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

포털에서 직접 라이선스 모델을 수정할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com) 를 열고 SQL Server VM에 대 한 [SQL 가상 머신 리소스](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 를 엽니다. 
1. **설정**아래에서 **구성** 을 선택 합니다. 
1. **Azure 하이브리드 혜택** 옵션을 선택 하 고 확인란을 선택 하 여 소프트웨어 보증이 포함 된 SQL Server 라이선스가 있는지 확인 합니다. 
1. **구성** 페이지의 아래쪽에서 **적용** 을 선택 합니다. 

![포털의 Azure 하이브리드 혜택](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 라이선스 모델을 변경할 수 있습니다.  


**Azure 하이브리드 혜택**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**종 량**제: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**DR (재해 복구)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 라이선스 모델을 변경할 수 있습니다.

**Azure 하이브리드 혜택**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**종량제**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**재해 복구** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>리소스 공급자에 등록 되지 않은 Vm

종 량 제 Azure Marketplace 이미지에서 SQL Server VM를 프로 비전 한 경우 SQL Server 라이선스 유형이 종 량 제로 표시 됩니다. Azure Marketplace에서 사용자 라이선스 가져오기 이미지를 사용 하 여 SQL Server VM를 프로 비전 한 경우 라이선스 유형은 AHUB가 됩니다. 기본적으로 프로 비전 된 모든 SQL Server Vm (종 량 제) 또는 사용자 고유의 라이선스 Azure Marketplace 이미지는 SQL VM 리소스 공급자에 자동으로 등록 되므로 [라이선스 유형을](#vms-already-registered-with-the-resource-provider)변경할 수 있습니다.

Azure 하이브리드 혜택를 통해 Azure VM에 SQL Server 자체 설치만 가능 합니다. Microsoft 제품 약관에 따라 Azure 하이브리드 혜택 사용을 나타내려면 SQL Server 라이선스를 Azure 하이브리드 혜택으로 설정 하 여 [이러한 vm을 SQL VM 리소스 공급자에 등록](virtual-machines-windows-sql-register-with-resource-provider.md) 해야 합니다.

SQL Server VM SQL VM 리소스 공급자에 등록 된 경우에만 SQL Server VM의 라이선스 유형을 종 량 제 또는 Azure 하이브리드 혜택로 변경할 수 있습니다.

## <a name="remarks"></a>설명

- Azure CSP (클라우드 솔루션 공급자) 고객은 먼저 종 량 제 VM을 배포 하 고, 활성 소프트웨어 보증이 있는 경우 자신의 라이선스를 가져오도록 변환 하 여 Azure 하이브리드 혜택를 사용할 수 있습니다.
- SQL Server VM 리소스를 삭제 하는 경우 이미지의 하드 코드 된 라이선스 설정으로 돌아갑니다. 
- 라이선스 모델을 변경 하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure Portal를 통해 Azure Marketplace 이미지를 배포 하면 리소스 공급자에 SQL Server VM 자동으로 등록 됩니다. 하지만 SQL Server를 자동으로 설치 하는 고객은 SQL Server VM을 수동으로 [등록](virtual-machines-windows-sql-register-with-resource-provider.md)해야 합니다. 
- 가용성 집합에 SQL Server VM를 추가 하려면 VM을 다시 만들어야 합니다. 따라서 가용성 집합에 추가 되는 모든 Vm은 기본 종 량 제 라이선스 형식으로 돌아갑니다. Azure 하이브리드 혜택를 다시 사용 하도록 설정 해야 합니다. 


## <a name="limitations"></a>제한 사항

라이선스 모델을 변경 하는 작업은 다음과 같습니다.
   - [소프트웨어 보증이](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)있는 고객만 사용할 수 있습니다.
   - Standard 및 Enterprise edition SQL Server에만 지원 됩니다. Express, Web 및 Developer의 라이선스 변경은 지원 되지 않습니다. 
   - Azure Resource Manager 모델을 통해 배포 된 가상 컴퓨터에만 지원 됩니다. 클래식 모델을 통해 배포 된 가상 머신은 지원 되지 않습니다. 
   - 공용 또는 Azure Government 클라우드에 대해서만 사용할 수 있습니다. 
   - 단일 NIC (네트워크 인터페이스)가 있는 가상 컴퓨터 에서만 지원 됩니다. 


## <a name="known-errors"></a>알려진 오류

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>리소스 그룹 '\<리소스 그룹 > '에서 ' SqlVirtualMachine/SqlVirtualMachines/\<리소스 그룹 > ' 리소스를 찾을 수 없습니다.

이 오류는 SQL VM 리소스 공급자에 등록 되지 않은 SQL Server VM에서 라이선스 모델을 변경 하려고 할 때 발생 합니다.

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

리소스 공급자에 구독을 등록 한 다음 [리소스 공급자를 사용](virtual-machines-windows-sql-register-with-resource-provider.md)하 여 SQL Server VM를 등록 해야 합니다. 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>가상 컴퓨터 '\<vmname\>'에 둘 이상의 NIC가 연결 되어 있습니다.

이 오류는 NIC가 둘 이상 있는 가상 컴퓨터에서 발생 합니다. 라이선스 모델을 변경 하기 전에 Nic 중 하나를 제거 합니다. 라이선스 모델을 변경한 후에는 VM에 NIC를 다시 추가할 수 있지만 Azure Portal의 작업 (예: 자동 백업 및 패치 적용)은 더 이상 지원 되지 않습니다. 


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM의 SQL Server에 대 한 FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM의 SQL Server에 대 한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM의 SQL Server에 대 한 릴리스 정보](virtual-machines-windows-sql-server-iaas-release-notes.md)



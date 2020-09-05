---
title: SQL Server VM에 대한 스토리지 구성 | Microsoft Docs
description: 이 항목에서는 Azure에서 프로 비전 중에 SQL Server Vm에 대 한 저장소를 구성 하는 방법을 설명 합니다 (Azure Resource Manager 배포 모델). 또한 기존 SQL Server VM에 대한 스토리지를 구성하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: de0402febe94e50877367dc37d448a4a13893f93
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653344"
---
# <a name="storage-configuration-for-sql-server-vms"></a>SQL Server VM에 대한 스토리지 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure에서 SQL Server VM (가상 머신) 이미지를 구성 하는 경우 Azure Portal은 저장소 구성을 자동화 하는 데 도움이 됩니다. 스토리지를 VM에 연결하고 해당 스토리지를 SQL Server에 액세스할 수 있도록 하고 구성하여 특정 성능 요구 사항에 최적화하는 작업을 포함합니다.

이 항목에서는 Azure에서 프로비전 중 스토리지 SQL Server VM 및 기존 VM에 대한 스토리지를 구성하는 방법을 설명합니다. 이 구성은 SQL Server를 실행하는 Azure VM의 [성능 모범 사례](performance-guidelines-best-practices.md) 에 기반합니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>필수 구성 요소

자동화된 스토리지 구성 설정을 사용하려면 가상 머신에는 다음과 같은 특성이 필요합니다.

* [SQL Server 갤러리 이미지](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)로 프로비전합니다.
* [Resource Manager 배포 모델](../../../azure-resource-manager/management/deployment-models.md)을 사용합니다.
* [프리미엄 SSD](../../../virtual-machines/disks-types.md)를 사용합니다.

## <a name="new-vms"></a>새 VM

다음 섹션에서는 새 SQL Server 가상 머신에 대한 스토리지를 구성하는 방법을 설명합니다.

### <a name="azure-portal"></a>Azure portal

SQL Server 갤러리 이미지를 사용하여 Azure VM을 프로비전하는 경우 **SQL Server 설정** 탭에서 **구성 변경**을 선택하여 성능 최적화 스토리지 구성 페이지를 엽니다. 값을 기본값 그대로 두거나 워크로드에 따라 사용자의 요구에 가장 적합하게 디스크 구성 유형을 수정할 수 있습니다. 

![프로비전하는 동안 SQL Server VM 스토리지 구성](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

**스토리지 최적화**에서 SQL Server를 배포하는 워크로드의 유형을 선택합니다. **일반** 최적화 옵션을 사용하는 경우 기본적으로 최대 5,000 IOPS를 포함하는 하나의 데이터 디스크가 있으며 데이터, 트랜잭션 로그 및 TempDB 스토리지에 대해 동일한 드라이브를 사용합니다. **트랜잭션 처리**(OLTP) 또는 **데이터 웨어하우징**을 선택하면 데이터용 및 트랜잭션 로그용으로 별도의 디스크가 생성되고 TempDB에 로컬 SSD가 사용됩니다. **트랜잭션 처리**와 **데이터 웨어하우징**간에는 스토리지 차이점이 없지만 사용자의 [스트라이프 구성 및 추적 플래그](#workload-optimization-settings)를 변경합니다. Premium Storage를 선택하면 데이터 드라이브에 *ReadOnly* 캐싱이 설정되고 [SQL Server VM 성능 모범 사례](performance-guidelines-best-practices.md)에 따라 로그 드라이브에 없음이 설정됩니다. 

![프로비전하는 동안 SQL Server VM 스토리지 구성](./media/storage-configuration/sql-vm-storage-configuration.png)

디스크 구성은 완전히 사용자 지정할 수 있으므로 SQL Server VM에 필요한 스토리지 토폴로지, 디스크 유형 및 IOP를 구성할 수 있습니다. 또한 SQL Server VM이 지원되는 지역(미국 동부 2, 동남아시아 및 북유럽) 중 하나에 있고 [구독에 Ultra Disk를 사용](/azure/virtual-machines/windows/disks-enable-ultra-ssd)하도록 설정한 경우 **디스크 유형** 옵션으로 UltraSSD(미리 보기)를 사용할 수 있습니다.  

또한 디스크에 대한 캐싱을 설정할 수 있습니다. Azure VM은 [프리미엄 디스크](/azure/virtual-machines/windows/disks-types#premium-ssd)를 사용할 경우 [Blob 캐시](/azure/virtual-machines/windows/premium-storage-performance#disk-caching)라는 다중 계층 캐싱 기술이 적용됩니다. Blob 캐시는 캐싱을 위해 가상 머신 RAM과 로컬 SSD의 조합을 사용합니다. 

프리미엄 SSD에 대한 디스크 캐싱은 *ReadOnly*, *ReadWrite* 또는 없음일 수 있습니다. 

- *ReadOnly* 캐싱은 Premium Storage에 저장된 SQL Server 데이터 파일에 매우 유용합니다. *ReadOnly* 캐싱은 낮은 읽기 대기 시간과 높은 읽기 IOPS 및 처리량을 제공합니다. 읽기가 VM 메모리 및 로컬 SSD 내에 있는 캐시에서 수행되기 때문입니다. 이러한 읽기는 Azure Blob storage에서 데이터 디스크를 읽는 것 보다 훨씬 빠릅니다. Premium Storage는 캐시에서 처리된 읽기를 디스크 IOPS 및 처리량으로 계산하지 않습니다. 따라서 애플리케이션은 더 높은 총 IOPS 및 처리량을 달성할 수 있습니다. 
- SQL Server 로그 파일을 호스트하는 디스크에는 없음 캐시 구성을 사용해야 합니다. 로그 파일은 순차적으로 기록되고 *ReadOnly* 캐싱이 도움이 되지 않기 때문입니다. 
- *ReadWrite* 캐싱은 SQL Server 파일을 호스트하는 데 사용할 수 없습니다. SQL Server는 *ReadWrite* 캐시와의 데이터 일관성을 지원하지 않기 때문입니다. 쓰기가 *ReadOnly* Blob 캐시 계층을 통과할 경우 읽기가 *ReadOnly* Blob 캐시 용량을 불필요하게 소비하고 대기 시간을 약간 증가시킵니다. 


   > [!TIP]
   > 스토리지 구성이 선택한 VM 크기에 적용되는 제한과 일치해야 합니다. VM 크기의 성능 용량을 초과하는 스토리지 매개 변수를 선택하면 다음 오류가 발생합니다. `The desired performance might not be reached due to the maximum virtual machine disk performance cap.`. 디스크 유형을 변경하여 IOP를 줄이거나 VM 크기를 늘려 성능 용량 제한을 높입니다. 


선택을 기반으로 Azure는 VM을 만든 후에 다음과 같은 스토리지 구성 작업을 수행합니다.

* 프리미엄 SSD를 만들고 가상 머신에 연결합니다.
* SQL Server에 액세스할 수 있도록 데이터 디스크를 구성합니다.
* 지정된 크기와 성능(IOPS 및 처리량) 요구 사항에 따라 스토리지 풀에 데이터 디스크를 구성합니다.
* 가상 컴퓨터에 새 드라이브와 스토리지 풀을 연결합니다.
* 지정한 워크로드 유형(데이터 웨어하우징, 트랜잭션 처리 또는 일반)에 따라 새 드라이브를 최적화합니다.

Azure에서 스토리지 설정을 구성하는 방법에 대한 자세한 내용은 [스토리지 구성 섹션](#storage-configuration)을 참조하세요. Azure Portal에서 SQL Server VM을 만드는 방법의 전체 연습은 [프로비전 자습서](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)를 참조하세요.

### <a name="resource-manager-templates"></a>리소스 관리자 템플릿

다음 Resource Manager 템플릿을 사용하는 경우 두 개의 프리미엄 데이터 디스크는 스토리지 풀 구성 없이 기본적으로 연결됩니다. 그러나 이러한 템플릿을 사용자 지정하여 가상 머신에 연결된 프리미엄 데이터 디스크의 수를 변경할 수 있습니다.

* [자동화된 Backup을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [자동화된 패치를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [AKV 통합을 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>빠른 시작 템플릿

다음 빠른 시작 템플릿을 사용하여 스토리지 최적화를 기반으로 SQL Server VM을 배포할 수 있습니다. 

* [스토리지 최적화를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [UltraSSD를 사용하여 VM 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>기존 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

기존 SQL Server VM의 경우 Azure 포털에서 일부 스토리지 설정을 수정할 수 있습니다. [SQL 가상 머신 리소스](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)를 열고 **개요**를 선택합니다. SQL Server 개요 페이지에 VM의 현재 스토리지 사용량이 표시됩니다. VM에 있는 모든 드라이브는 이 차트에 표시됩니다. 각 드라이브의 경우 스토리지 공간은 네 개의 섹션에 표시됩니다.

* SQL data
* SQL 로그
* 기타(비 SQL 스토리지)
* 사용 가능

스토리지 설정을 수정하려면 **설정**에서 **구성**을 선택합니다. 

![기존 SQL Server VM에 대한 스토리지 구성](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

SQL Server VM 만들기 프로세스 중에 구성된 드라이브의 디스크 설정을 수정할 수 있습니다. **드라이브 확장**을 선택하면 드라이브 수정 페이지가 열리며 디스크 유형을 변경하고 디스크를 더 추가할 수 있습니다. 

![기존 SQL Server VM에 대한 스토리지 구성](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="storage-configuration"></a>스토리지 구성

이 섹션에서는 Azure Portal에서 SQL Server VM 프로 비전 또는 구성 하는 동안 Azure에서 자동으로 수행 하는 저장소 구성 변경 내용에 대 한 참조를 제공 합니다.

* Azure는 VM에서 선택된 스토리지로부터 스토리지 풀을 구성합니다. 이 항목의 다음 섹션에서는 스토리지 풀 구성에 대해 자세히 설명합니다.
* 자동 스토리지 구성은 항상 [프리미엄 SSD](../../../virtual-machines/disks-types.md) P30 데이터 디스크를 사용합니다. 따라서 선택한 테라바이트 수와 VM에 연결된 데이터 디스크의 수 간에 1:1 매핑이 됩니다.

가격 책정 정보는 [디스크 스토리지](https://azure.microsoft.com/pricing/details/storage) 탭의 **스토리지 가격 책정** 페이지를 참조하세요.

### <a name="creation-of-the-storage-pool"></a>스토리지 풀 만들기

Azure는 다음 설정을 사용하여 SQL Server VM에 스토리지 풀을 만듭니다.

| 설정 | 값 |
| --- | --- |
| 스트라이프 크기 |256KB(데이터 웨어하우징); 64KB(트랜잭션) |
| 디스크 크기 |각각 1TB |
| 캐시 |읽기 |
| 할당 크기 |64KB NTFS 할당 단위 크기 |
| 복구 | 단순 복구(복원력 없음) |
| 열 수 |데이터 디스크 수 최대 8<sup>1</sup> |


<sup>1</sup> 스토리지 풀을 만든 후에 스토리지 풀에서 열 수를 변경할 수 없습니다.


## <a name="workload-optimization-settings"></a>워크로드 최적화 설정

다음 테이블에서는 사용할 수 있는 세 가지 워크로드 유형 옵션 및 이에 해당하는 최적화를 설명합니다.

| 워크로드 유형 | Description | 최적화 |
| --- | --- | --- |
| **일반** |대부분의 워크로드를 지원하는 기본 설정 |None |
| **트랜잭션 처리** |기존의 데이터베이스 OLTP 워크로드용으로 스토리지를 최적화합니다. |추적 플래그 1117<br/>추적 플래그 1118 |
| **데이터 웨어하우징** |분석 및 보고 워크로드용으로 스토리지를 최적화합니다. |추적 플래그 610<br/>추적 플래그 1117 |

> [!NOTE]
> 저장소 구성 단계에서 가상 컴퓨터를 선택 하 여 SQL Server 가상 컴퓨터를 프로 비전 할 때에만 작업 유형을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조하세요.

---
title: 장애 조치(Failover) 클러스터 인스턴스
description: Azure Virtual Machines에서 SQL Server를 사용 하 여 FCIs (장애 조치 (failover) 클러스터 인스턴스)에 대해 알아봅니다.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: e5862daa21f8bf0075bb1dee567cbe887ec32d72
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653276"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server를 사용 하 여 장애 조치 (Failover) 클러스터 인스턴스
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure Virtual Machines (Vm)에서 SQL Server에 대 한 FCI (장애 조치 (failover) 클러스터 인스턴스)를 사용 하는 경우의 기능 차이를 소개 합니다. 

## <a name="overview"></a>개요

Azure Vm의 SQL Server는 WSFC (Windows Server 장애 조치 (Failover) 클러스터링) 기능을 사용 하 여 서버 인스턴스 수준의 중복성을 통해 로컬 고가용성을 제공 합니다 (장애 조치 (failover) 클러스터 인스턴스). FCI는 WSFC (또는 간단히 클러스터) 노드와 여러 서브넷에 걸쳐 설치 되는 SQL Server의 단일 인스턴스입니다. 네트워크에서 FCI는 단일 컴퓨터에서 실행 되 SQL Server의 인스턴스로 표시 됩니다. 하지만 FCI는 현재 노드를 사용할 수 없게 되는 경우 WSFC 노드 간에 장애 조치 (failover)를 제공 합니다.

문서의 나머지 부분에서는 장애 조치 (failover) 클러스터 인스턴스가 Azure Vm에서 SQL Server와 함께 사용 될 때의 차이점에 중점을 두고 있습니다. 장애 조치 (failover) 클러스터링 기술에 대해 자세히 알아보려면 다음을 참조 하세요. 

- [Windows 클러스터 기술](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 장애 조치 (failover) 클러스터 인스턴스](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

클러스터 쿼럼에 대 한 디스크 감시, 클라우드 감시 또는 파일 공유 감시를 사용 하 여 Azure Virtual Machines에서 SQL Server로 장애 조치 (Failover) 클러스터 인스턴스를 지원 합니다.

자세히 알아보려면 [Azure에서 SQL Server vm을 사용 하는 쿼럼 모범 사례](hadr-cluster-best-practices.md#quorum)를 참조 하세요. 


## <a name="storage"></a>스토리지

기존의 온-프레미스 클러스터 환경에서 Windows 장애 조치 (failover) 클러스터는 두 노드에서 모두 공유 저장소로 액세스할 수 있는 SAN (저장 영역 네트워크)을 사용 합니다. SQL Server 파일은 공유 저장소에서 호스팅되고 활성 노드만 한 번에 파일에 액세스할 수 있습니다. 

Azure Vm에서 SQL Server는 SQL Server 장애 조치 (failover) 클러스터 인스턴스를 배포 하기 위한 공유 저장소 솔루션으로 다양 한 옵션을 제공 합니다. 

||[Azure 공유 디스크](../../../virtual-machines/windows/disks-shared.md)|[프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[스토리지 공간 다이렉트(S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**최소 OS 버전**| 모두 |Windows Server 2012|Windows Server 2016|
|**최소 SQL Server 버전**|모두|SQL Server 2012|SQL Server 2016|
|**지원 되는 VM 가용성** |근접 배치 그룹을 사용 하는 가용성 집합 |가용성 집합 및 가용성 영역|가용성 집합 |
|**FileStream 지원**|예|아니요|예 |
|**Azure blob 캐시**|아니요|아니요|예|

이 섹션의 나머지 부분에서는 Azure Vm에서 SQL Server 하는 데 사용할 수 있는 각 저장소 옵션의 이점 및 제한 사항을 나열 합니다. 

### <a name="azure-shared-disks"></a>Azure 공유 디스크

[Azure 공유 디스크](../../../virtual-machines/windows/disks-shared.md) 는 [azure managed disks](../../../virtual-machines/managed-disks-overview.md)의 기능입니다. Windows Server 장애 조치 (Failover) 클러스터링은 장애 조치 (failover) 클러스터 인스턴스에서 Azure 공유 디스크 사용을 지원 합니다. 

**지원 되는 OS**: 모두   
**지원 되는 SQL 버전**: 모두     

**이점은**다음과 같습니다. 
- 고가용성 및 재해 복구 (HADR) 아키텍처를 그대로 유지 하면서 Azure로 마이그레이션하려는 응용 프로그램에 유용 합니다. 
- 는 scsi PR (SCSI 영구 예약) 지원으로 인해 클러스터형 응용 프로그램을 Azure로 마이그레이션할 수 있습니다. 
- SQL Server 2019에 대 한 모든 버전의 SQL Server 및 공유 Azure Ultra 디스크 저장소에 대해 공유 Azure 프리미엄 SSD을 지원 합니다. 
- 단일 공유 디스크를 사용 하거나 여러 공유 디스크를 스트라이프 하 여 공유 저장소 풀을 만들 수 있습니다. 
- Filestream을 지원 합니다.


**단점**: 
- 가상 머신은 동일한 가용성 집합 및 근접 배치 그룹에 배치 해야 합니다.
- 가용성 영역은 지원 되지 않습니다.
- 프리미엄 SSD 디스크 캐싱은 지원 되지 않습니다.
 
시작 하려면 [Azure 공유 디스크를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스 SQL Server](failover-cluster-instance-azure-shared-disks-manually-configure.md)를 참조 하세요. 

### <a name="storage-spaces-direct"></a>직접 스토리지 공간

[스토리지 공간 다이렉트](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 는 Azure Virtual Machines에서 장애 조치 (failover) 클러스터링이 지원 되는 Windows Server 기능입니다. 소프트웨어 기반 가상 SAN을 제공 합니다.

**지원 되는 OS**: Windows Server 2016 이상   
**지원 되는 SQL 버전**: SQL Server 2016 이상   


**아니라** 
- 네트워크 대역폭이 충분 하면 강력 하 고 성능이 뛰어난 공유 저장소 솔루션을 사용할 수 있습니다. 
- 는 Azure blob 캐시를 지원 하므로 캐시에서 읽기를 로컬로 처리할 수 있습니다. 업데이트는 두 노드에 동시에 복제 됩니다. 
- FileStream을 지원 합니다. 

**제한을**
- Windows Server 2016 이상 에서만 사용할 수 있습니다. 
- 가용성 영역은 지원 되지 않습니다.
- 두 가상 컴퓨터에 연결 된 디스크 용량이 동일 해야 합니다. 
- 지속적인 디스크 복제로 인해 높은 성능을 얻으려면 높은 네트워크 대역폭이 필요 합니다. 
- 저장소는 각 VM에 연결 되므로 VM 크기가 크고 저장소에 대해 두 배가 필요 합니다. 

시작 하려면 [스토리지 공간 다이렉트를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스 SQL Server](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 참조 하세요. 

### <a name="premium-file-share"></a>프리미엄 파일 공유

[프리미엄 파일 공유](../../../storage/files/storage-how-to-create-premium-fileshare.md) 는 [Azure Files](../../../storage/files/index.yml)의 기능입니다. 프리미엄 파일 공유는 SSD를 지원 하며 대기 시간이 지속적으로 짧습니다. Windows Server 2012 이상에서 SQL Server 2012 이상에 대 한 장애 조치 (failover) 클러스터 인스턴스와 함께 사용 하기 위해 완벽 하 게 지원 됩니다. 프리미엄 파일 공유를 사용 하면 가동 중지 시간 없이 파일 공유 크기를 조정 하 고 크기를 조정할 수 있으므로 유연성이 향상 됩니다.

**지원 되는 OS**: Windows Server 2012 이상   
**지원 되는 SQL 버전**: SQL Server 2012 이상   

**아니라** 
- 가상 컴퓨터에 대 한 공유 저장소 솔루션은 여러 가용성 영역에 분산 됩니다. 
- 단일 자리 대기 시간을 포함 하는 완전히 관리 되는 파일 시스템 및 안정적인 i/o 성능 

**제한을**
- Windows Server 2012 이상 에서만 사용할 수 있습니다. 
- FileStream은 지원 되지 않습니다. 


시작 하려면 [Premium 파일 공유를 사용 하 여 장애 조치 (failover) 클러스터 인스턴스 SQL Server](failover-cluster-instance-premium-file-share-manually-configure.md)를 참조 하세요. 

### <a name="partner"></a>파트너

지원 되는 저장소를 포함 하는 파트너 클러스터링 솔루션이 있습니다. 

**지원 되는 OS**: 모두   
**지원 되는 SQL 버전**: 모두   

한 가지 예는 SIOS DataKeeper를 저장소로 사용 합니다. 자세한 내용은 블로그 항목 [장애 조치 (Failover) 클러스터링 및 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)를 참조 하세요.

### <a name="iscsi-and-expressroute"></a>iSCSI 및 Express 경로

Azure Express 경로를 통해 iSCSI 대상 공유 블록 저장소를 노출할 수도 있습니다. 

**지원 되는 OS**: 모두   
**지원 되는 SQL 버전**: 모두   

예를 들어 NPS(NetApp 프라이빗 스토리지)는 Equinix와 함께 ExpressRoute를 사용하여 iSCSI 대상을 Azure VM에 공개합니다.

Microsoft 파트너의 공유 저장소 및 데이터 복제 솔루션은 장애 조치 (failover) 시 데이터 액세스와 관련 된 문제에 대해 공급 업체에 문의 하세요.

## <a name="connectivity"></a>연결

Azure Virtual Machines에서 SQL Server를 사용 하 여 장애 조치 (Failover) 클러스터 인스턴스는 클러스터 된 리소스를 현재 소유 하 고 있는 노드에 관계 없이 Azure Load Balancer와 함께 [DNN (분산 네트워크 이름](hadr-distributed-network-name-dnn-configure.md) ) 또는 [vnn (가상 네트워크 이름)](hadr-vnn-azure-load-balancer-configure.md) 을 사용 하 여 SQL Server 인스턴스로 트래픽을 라우팅합니다. SQL Server FCI와 함께 특정 기능 및 DNN를 사용 하는 경우 추가로 고려해 야 할 사항이 있습니다. 자세히 알아보려면 [SQL Server FCI와의 DNN 상호 운용성](failover-cluster-instance-dnn-interoperability.md) 을 참조 하세요. 

클러스터 연결 옵션에 대 한 자세한 내용은 [Azure vm에서 SQL Server에 HADR 연결 라우팅](hadr-cluster-best-practices.md#connectivity)을 참조 하세요. 

## <a name="limitations"></a>제한 사항

Azure Virtual Machines에서 SQL Server 장애 조치 (failover) 클러스터 인스턴스에 대 한 다음 제한 사항을 고려 합니다. 

### <a name="lightweight-resource-provider"></a>경량 리소스 공급자   
SQL Server 현재 Azure 가상 컴퓨터의 장애 조치 (failover) 클러스터 인스턴스는 [SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)의 [경량 관리 모드](sql-vm-resource-provider-register.md#management-modes) 에서만 지원 됩니다. 전체 확장 모드에서 경량 모드로 변경 하려면 해당 Vm에 대 한 **sql 가상 컴퓨터** 리소스를 삭제 한 다음 경량 모드로 sql VM 리소스 공급자에 등록 합니다. Azure Portal를 사용 하 여 **SQL 가상 컴퓨터** 리소스를 삭제 하는 경우 올바른 가상 컴퓨터 옆의 확인란을 선택 취소 합니다. 

전체 확장은 자동화된 백업, 패치, 고급 포털 관리 등의 기능을 지원합니다. 이러한 기능은 에이전트가 경량 관리 모드로 다시 설치 된 후 SQL Server Vm에 대해 작동 하지 않습니다.

### <a name="msdtc"></a>MSDTC   
Azure Virtual Machines는 CSV (클러스터 공유 볼륨) 및 [azure 표준 Load Balancer](../../../load-balancer/load-balancer-standard-overview.md)의 저장소를 사용 하 여 Windows Server 2019에서 MSDTC를 지원 합니다.

Azure Virtual Machines에서 MSDTC는 Windows Server 2016 이전 버전에서는 지원 되지 않습니다.

- 클러스터형 MSDTC 리소스는 공유 스토리지를 사용하도록 구성할 수 없습니다. Windows Server 2016에서 MSDTC 리소스를 만드는 경우 스토리지가 사용 가능하더라도 사용 가능한 공유 스토리지가 표시되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 부하 분산 장치는 RPC 포트를 처리하지 않습니다.


## <a name="next-steps"></a>다음 단계

[클러스터 구성 모범 사례](hadr-cluster-best-practices.md)를 검토 한 후 [fci에 대 한 SQL Server VM를 준비할](failover-cluster-instance-prepare-vm.md)수 있습니다. 

자세한 내용은 다음을 참조하세요. 

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 장애 조치 (failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)


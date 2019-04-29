---
title: SQL Server에 대한 고가용성 및 재해 복구 | Microsoft Docs
description: Azure Virtual Machines에서 실행되는 SQL Server에 대한 다양한 형식의 HADR 전략을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: 463ef5f4a655617074915078fb4ced9e596f8957
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478368"
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구

Microsoft Azure 가상 머신(VM)에 SQL Server를 설치하여 사용하면 고가용성 및 재해 복구(HADR) 데이터베이스 솔루션의 비용을 절감하는 데 도움이 됩니다. 대부분의 SQL Server HADR 솔루션은 Azure 가상 머신에서 지원됩니다. Azure 전용으로도, 하이브리드 솔루션으로도 사용 가능합니다. Azure 전용 솔루션에서는 HADR 시스템 전체가 Azure에서 실행됩니다. 하이브리드 구성에서는 솔루션의 일부가 Azure에서 실행되고 나머지 부분은 조직의 온-프레미스에서 실행됩니다. Azure 환경은 유연하게 제공되므로 사용 중인 SQL Server 데이터베이스 시스템의 예산과 HADR 요구 사항에 맞게 Azure로 부분적으로만 이동하거나 완전히 이동할 수도 있습니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>HADR 솔루션의 필요성 이해
데이터베이스 시스템에 서비스 수준 계약(SLA)이 필요한 HADR 기능을 도입하는 것은 여러분의 선택입니다. Azure는 클라우드 서비스를 위한 서비스 복구 및 Virtual Machines에 대한 오류 복구 감지와 같은 고가용성 메커니즘을 제공하기는 하지만, 이를 통해 원하는 SLA를 충족할 수 있다는 보장은 없습니다. 이러한 메커니즘은 VM의 고가용성을 보호하기는 하지만 VM에서 실행되는 SQL Server의 고가용성을 보호하지는 않습니다. VM이 온라인 상태이고 정상인 경우에도 SQL Server에 문제가 생길 수 있습니다. 또한 Azure가 제공하는 고가용성 메커니즘을 사용해도 소프트웨어를 사용한 복구나 하드웨어 고장, 운영 체제 업그레이드와 같은 경우에는 VM이 가동 중지될 수 있습니다.

지역 복제라는 기능으로 구현되는 Azure의 GRS(지역 중복 저장)도 데이터베이스의 적절한 재해 복구 솔루션이 되지 못할 수 있습니다. 지역 복제는 데이터를 비동기 방식으로 전송하기 때문에, 재해 발생 시 최근 업데이트 내용이 손실될 수 있습니다. 지역에서 복제의 제한 사항에 대한 자세한 내용은 [지역에서 복제는 별도의 디스크에 저장된 데이터 및 로그 파일에 대해 지원되지 않음](#geo-replication-support) 섹션에 설명되어 있습니다.

## <a name="hadr-deployment-architectures"></a>HADR 배포 아키텍처
Azure에서 지원하는 SQL Server HADR 기술은 다음과 같습니다.

* [Always On 가용성 그룹](https://technet.microsoft.com/library/hh510230.aspx)
* [Always On 장애 조치 클러스터 인스턴스](https://technet.microsoft.com/library/ms189134.aspx)
* [로그 전달](https://technet.microsoft.com/library/ms187103.aspx)
* [Azure Blob Storage 서비스로 SQL Server Backup 및 복원](https://msdn.microsoft.com/library/jj919148.aspx)
* [데이터베이스 미러링](https://technet.microsoft.com/library/ms189852.aspx) - SQL Server 2016에서에서 사용되지 않음

이러한 기술을 조합하여 고가용성과 재해 복구 기능을 갖춘 SQL Server 솔루션을 구현하는 것도 가능합니다. 사용하는 기술에 따라, 하이브리드 배포에는 Azure 가상 네트워크와 함께 VPN 터널이 필요할 수도 있습니다. 다음 섹션에서 몇 가지 배포 아키텍처의 예를 소개합니다.

## <a name="azure-only-high-availability-solutions"></a>Azure 전용: 고가용성 솔루션

가용성 그룹이라고 하는 Always On 가용성 그룹을 사용하여 데이터베이스 수준에서 SQL Server에 대한 고가용성 솔루션을 유지할 수 있습니다. 장애 조치 클러스터 인스턴스라고 하는 Always On 장애 조치 클러스터 인스턴스를 사용하여 인스턴스 수준에서 고가용성 솔루션을 만들 수도 있습니다. 추가적인 중복의 경우 장애 조치 클러스터 인스턴스에서 가용성 그룹을 만들어 두 수준 모두에서 중복을 만들 수 있습니다. 

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |동일한 지역에 있는 Azure VM에서 실행되는 모든 가용성 복제본은 고가용성을 제공합니다. Windows 장애 조치 클러스터링에 Active Directory 도메인이 필요하기 때문에 도메인 컨트롤러 VM을 구성해야 합니다.<br/> ![가용성 그룹](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>자세한 내용은 [Azure에서 가용성 그룹 구성(GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)을 참조하세요. |
| **장애 조치 클러스터 인스턴스** |공유 저장소가 필요한 FCI(장애 조치 클러스터 인스턴스)는 3가지 방법으로 만들 수 있습니다.<br/><br/>1. [Windows Server 2016 저장소 공간 다이렉트 \(S2D\)](virtual-machines-windows-portal-sql-create-failover-cluster.md)를 사용하는 연결된 저장소로 Azure VM에서 실행되는 2노드 장애 조치 클러스터는 소프트웨어 기반 가상 SAN을 제공합니다.<br/><br/>2. 타사 클러스터링 솔루션에서 지원하는 저장소를 사용하여 Azure VM에서 실행되는 2노드 장애 조치 클러스터입니다. SIOS DataKeeper를 사용하는 특정 예제는 [장애 조치 클러스터링 및 타사 소프트웨어 SIOS DataKeeper를 사용하는 파일 공유에 대한 고가용성](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)을 참조하세요.<br/><br/>3. ExpressRoute를 통한 원격 iSCSI 대상 공유 블록 저장소를 사용하여 Azure VM에서 실행되는 2노드 장애 조치 클러스터입니다. 예를 들어 NPS(NetApp 개인 저장소)는 Equinix와 함께 ExpressRoute를 사용하여 iSCSI 대상을 Azure VM에 공개합니다.<br/><br/>타사 공유 저장소 및 데이터 복제 솔루션의 경우 장애 조치 시 데이터 액세스와 관련된 문제는 공급 업체에 문의해야 합니다.<br/><br/>이 솔루션은 Premium Storage를 사용하지 않기 때문에 [Azure File Storage](https://azure.microsoft.com/services/storage/files/) 맨 위에서 FCI를 사용하는 것은 아직 지원되지 않습니다. 빠른 시일 내에 지원하기 위해 노력하고 있습니다. |

## <a name="azure-only-disaster-recovery-solutions"></a>Azure 전용: 재해 복구 솔루션
가용성 그룹, 데이터베이스 미러링을 사용하여 Azure 내의 SQL Server 데이터베이스에 대한 재해 복구 솔루션을 구축하거나 저장소 blob을 사용하여 백업 및 복원할 수 있습니다.

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |재해 복구를 위해 가용성 복제본을 Azure VM의 여러 데이터 센터에서 실행합니다. 이렇게 여러 영역에 나누어 실행되는 솔루션은 완전한 사이트 중단이 발생해도 데이터를 보호할 수 있습니다. <br/> ![가용성 그룹](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>한 영역 내의 모든 복제본은 동일한 클라우드 서비스 및 동일한 VNet 내에 있어야 합니다. 각 영역에 별도의 VNet이 있으므로, 이러한 솔루션은 VNet 사이의 연결이 필요합니다. 자세한 내용은 [Azure Portal에서 VNet-VNet 연결 구성](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 참조하세요. 자세한 지침은 [다른 지역의 Azure Virtual Machines에서 SQL Server 가용성 그룹 구성](virtual-machines-windows-portal-sql-availability-group-dr.md)을 참조하세요.|
| **데이터베이스 미러링** |재해 복구를 위해 주 서버와 미러 서버를 다른 데이터 센터에서 실행합니다. Active Directory 도메인을 여러 데이터 센터에 사용할 수 없으므로 서버 인증서를 사용하여 배포해야 합니다.<br/>![데이터베이스 미러링](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Azure Blob Storage 서비스로 백업 및 복원** |File Storage에서 파일 복사<br/>![Backup 및 복원](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>자세한 내용은 [Azure Virtual Machines에서 SQL Server의 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md)을 참조하세요. |
| **Azure Site Recovery를 사용하여 SQL Server를 Azure에 복제 및 장애 조치(Failover)** |재해 복구를 위해 한 Azure 데이터 센터의 프로덕션 SQL Server가 다른 Azure 데이터 센터의 Azure Storage에 직접 복제되었습니다.<br/>![Azure Site Recovery를 사용하여 복제](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>자세한 내용은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](../../../site-recovery/site-recovery-sql.md)를 참조하세요. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>하이브리드 IT: 재해 복구 솔루션
가용성 그룹, 데이터베이스 미러링, 로그 전달을 사용하여 하이브리드 IT 환경 내에 SQL Server 데이터베이스에 대한 재해 복구 솔루션을 구축하고 Azure Blob 저장소를 사용하여 백업 및 복원할 수 있습니다.

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |사이트간 재해 복구를 위해 일부 가용성 복제본은 Azure VM에서 실행되고 다른 복제본은 온-프레미스에서 실행됩니다. 프로덕션 사이트는 온-프레미스와 Azure 데이터 센터 어디에도 있을 수 있습니다.<br/>![가용성 그룹](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>모든 가용성 복제본이 동일한 장애 조치 클러스터에 있어야 하므로, 장애 조치 클러스터는 두 네트워크(다중 서브넷 장애 조치 클러스터)에 걸쳐 있어야 합니다. 이 구성은 Azure와 온-프레미스 네트워크 간의 VPN 연결이 필요합니다.<br/><br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다.<br/><br/>SSMS에서 복제본 추가 마법사를 사용하여 Azure 복제본을 기존 Always On 가용성 그룹에 추가할 수도 있습니다. 자세한 내용은 자습서를 참조 하세요. Always On 가용성 그룹을 Azure로 확장 합니다. |
| **데이터베이스 미러링** |서버 인증서를 사용한 사이트 간 재해 복구를 위해 한 파트너는 Azure VM에서 실행하고 다른 파트너는 온-프레미스에서 실행합니다. 파트너는 동일한 Active Directory 도메인에 속할 필요가 없으며 VPN 연결도 필요하지 않습니다.<br/>![데이터베이스 미러링](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>또 다른 데이터베이스 미러링 시나리오로는 사이트 간 재해 복구를 위해 한 파트너가 Azure VM에서 실행되고 다른 파트너가 동일한 Active Directory 도메인의 온-프레미스에서 실행되는 경우를 들 수 있습니다. [Azure 가상 네트워크와 온-프레미스 네트워크 간의 VPN 연결](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)이 필요합니다.<br/><br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다. |
| **로그 전달** |사이트 간 재해 복구를 위해 한 서버는 Azure VM에서 실행하고 다른 서버는 온-프레미스에서 실행합니다. 로그 전달은 Windows 파일 공유를 사용하므로 Azure 가상 네트워크와 온-프레미스 네트워크 간의 VPN 연결이 필요합니다.<br/>![로그 전달](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다. |
| **Azure Blob Storage 서비스로 백업 및 복원** |Cloud Shell은 Azure File 스토리지를 활용하여 세션 간에 파일을 유지합니다.<br/>![Backup 및 복원](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>자세한 내용은 [Azure Virtual Machines에서 SQL Server의 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md)을 참조하세요. |
| **Azure Site Recovery를 사용하여 SQL Server를 Azure에 복제 및 장애 조치(Failover)** |재해 복구를 위해 온-프레미스 프로덕션 SQL Server가 Azure Storage에 직접 복제되었습니다.<br/>![Azure Site Recovery를 사용하여 복제](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>자세한 내용은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](../../../site-recovery/site-recovery-sql.md)를 참조하세요. |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure에서 SQL Server HADR에 대한 중요 고려 사항
Azure VM, 저장소 및 네트워킹은 온-프레미스, 가상화되지 않은 IT 인프라에서는 작동 특성이 달라집니다. Azure에서 SQL Server HADR 솔루션을 성공적으로 구현하기 위해서는 이러한 차이점을 이해하고 그에 맞게 솔루션을 설계해야 합니다.

### <a name="high-availability-nodes-in-an-availability-set"></a>가용성 집합의 고가용성 노드
Azure의 가용성 집합을 사용하면 고가용성 노드를 별도의 오류 도메인(FD)과 업데이트 도메인(UD)에 배치할 수 있습니다. 같은 가용성 집합에 배치할 Azure VM이라면 같은 클라우드 서비스에 배포해야 합니다. 같은 클라우드 서비스에 있는 노드만 같은 가용성 집합에 참여할 수 있습니다. 자세한 내용은 [Virtual Machines의 가용성 관리](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="failover-cluster-behavior-in-azure-networking"></a>Azure 네트워킹에서 장애 조치 클러스터의 동작
Azure에 RFC 호환이 아닌 DHCP 서비스를 사용하면 특정한 장애 조치 클러스터 구성에 오류가 발생할 수 있습니다. 클러스터 노드에 같은 IP 주소가 사용되는 것과 같이 클러스터 네트워크 이름에 중복된 IP 주소가 할당될 수 있기 때문입니다. 이는 Windows 장애 조치 클러스터 기능을 사용하는 가용성 그룹을 구현할 때 문제가 될 수 있습니다.

노드가 2개인 클러스터를 만들고 온라인 상태로 만드는 상황을 고려해 보십시오.

1. 클러스터가 온라인 상태가 되면 다음 NODE1은 클러스터 네트워크 이름으로 동적 할당된 IP 주소를 요청합니다.
2. DHCP에서 할당한 NODE1의 자체 IP 주소 외에는 다른 IP 주소가 없으므로, DHCP 서비스는 요청이 NODE1 자체에서 온 것으로 인식하게 됩니다.
3. Windows는 NODE1과 장애 조치 클러스터 네트워크 이름에 중복된 주소가 할당된 것으로 감지하므로 기본 클러스터 그룹이 온라인 상태가 되지 못합니다.
4. 기본 클러스터 그룹은 NODE2로 이동하게 되고, NODE1의 IP 주소를 클러스터 IP 주소로 취급하여 기본 클러스터 그룹이 온라인 상태가 됩니다.
5. NODE2가 NODE1에 연결을 시도하면 NODE1로 보내지는 패킷이 NODE1의 IP 주소 자체로 향하게 되므로 NODE2에서 출발 자체를 못합니다. 따라서 NODE2는 NODE1에 연결할 수 없고 쿼럼을 잃고 클러스터를 닫게 됩니다.
6. NODE1은 NODE2로 패킷을 보낼 수 있지만, NODE2는 응답할 수 없습니다. NODE1도 쿼럼을 잃고 클러스터를 닫습니다.

169.254.1.1과 같은 링크 로컬 IP 주소 등의 사용되지 않는 정적 IP 주소를 클러스터 네트워크 이름으로 할당하여 클러스터 네트워크 이름을 온라인 상태로 만들면 이러한 상황을 방지할 수 있습니다. 이 절차를 간단히 수행하려면 [Azure에서 가용성 그룹에 Windows 장애 조치 클러스터 구성](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)을 참조하세요.

자세한 내용은 [Azure에서 가용성 그룹 구성(GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)을 참조하세요.

### <a name="availability-group-listener-support"></a>가용성 그룹 수신기 지원
가용성 그룹 수신기는 Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 및 Windows Server 2016에서 실행되는 Azure VM에서 지원됩니다. 가용성 그룹 노드인 Azure VM에서 부하 분산된 엔드포인트를 사용하도록 설정하면 이러한 지원이 가능해집니다. 수신기가 작동하도록 하려면 Azure에서 실행되는 클라이언트 애플리케이션과 온-프레미스 실행되는 클라이언트 애플리케이션 모두에 대해 특별한 구성 단계를 따라야 합니다.

수신기를 설정하는 두 가지 주요 옵션(외부(공용) 및 내부)이 있습니다. 외부(공용) 수신기는 인터넷 연결 부하 분산 장치를 사용하며 인터넷을 통해 액세스할 수 있는 공용 VIP(가상 IP)에 연결됩니다. 내부 수신기는 내부 부하 분산 장치를 사용하며 동일한 Virtual Network 내에 있는 클라이언트만 지원합니다. 각 부하 분산 장치 유형에 대해 Direct Server Return (DSR)을 설정해야 합니다. 

가용성 그룹이 여러 Azure 서브넷에 있는 경우(여러 Azure 지역에 배포한 경우와 같이) 클라이언트 연결 문자열에는 "**MultisubnetFailover = True**"가 포함되어야 합니다. 이렇게 하면 다른 서브넷에 있는 복제본에 대해 병렬 연결을 시도하게 됩니다. 수신기 설정에 대한 지침은 다음을 참조하세요.

* [Azure에서 가용성 그룹에 대한 ILB 수신기 구성](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)
* [Azure에서 가용성 그룹에 대한 외부 수신기 구성](../sqlclassic/virtual-machines-windows-classic-ps-sql-ext-listener.md)

서비스 인스턴스에 직접 연결하면 각 가용성 복제본에 개별적으로 연결할 수 있습니다. 또한 가용성 그룹은 데이터베이스 미러링 클라이언트와 역방향 호환이 가능하므로 복제본이 데이터베이스 미러링과 유사하게 구성된 이상 데이터베이스 미러링 파트너와 같이 가용성 복제본에 연결할 수 있습니다.

* 하나의 주 복제본과 하나의 보조 복제본
* 보조 복제본은 읽을 수 없도록 구성(**읽을 수 있는 보조** 옵션을 **아니요**로 설정)

ADO.NET 또는 SQL Server Native Client를 사용하여 데이터베이스 미러링과 유사한 이 구성에 연결하는 클라이언트 연결 문자열의 예는 다음과 같습니다.

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

클라이언트 연결에 대한 자세한 내용은 다음을 참조하세요.

* [SQL Server Native Client와 연결 문자열 키워드 사용](https://msdn.microsoft.com/library/ms130822.aspx)
* [데이터베이스 미러링 세션(SQL Server)에 클라이언트 연결](https://technet.microsoft.com/library/ms175484.aspx)
* [하이브리드 IT 환경에서 가용성 그룹 수신기에 연결](https://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [가용성 그룹 수신기, 클라이언트 연결 및 애플리케이션 장애 조치(failover)(SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [가용성 그룹과 데이터베이스 미러링 연결 문자열 사용](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>하이브리드 IT 환경의 네트워크 대기 시간
HADR 솔루션을 배포할 때는 온-프레미스 네트워크와 Azure 간에 긴 네트워크 대기 시간이 있을 수 있다는 가정을 해야 합니다. 복제본을 Azure에 배포할 때, 동기화 모드로 동기 커밋 대신 비동기 커밋을 사용해야 합니다. 온-프레미스와 Azure 모두에 데이터베이스 미러링 서버를 배포할 때는 보호 우선 모드 대신 성능 우선 모드를 사용합니다.

### <a name="geo-replication-support"></a>지역에서 복제 지원
Azure 디스크의 지역 복제는 동일한 데이터베이스의 로그 파일과 데이터 파일을 별도의 디스크로 저장하는 것을 지원하지 않습니다. GRS는 변경 내용을 독립적이고 비동기적으로 각 디스크에 복제합니다. 이 메커니즘은 지리적으로 복제된 복사본에서 쓰기 순서가 지켜지도록 보장하지만 여러 디스크에 지역 복제된 복사본에서는 보장하지 않습니다. 데이터 파일과 로그 파일을 별도의 디스크에 저장하도록 데이터베이스를 구성하는 경우, 재해 후 복구된 디스크에는 로그 파일보다 더 최신인 데이터 파일의 복사본이 포함되어 있을 수 있어 SQL Server의 미리 기록 규칙과 트랜잭션의 ACID 특성을 어기게 됩니다. 저장소 계정에 지역 복제를 사용하지 않도록 설정할 수 있는 방법이 없을 경우 데이터베이스의 모든 데이터 및 로그 파일을 동일한 디스크에 저장해야 합니다. 데이터베이스의 크기 때문에 하나 이상의 디스크를 사용하는 경우 데이터의 중복성을 보장하려면 위에 나열된 재해 복구 솔루션 중 하나를 배포해야 합니다.

## <a name="next-steps"></a>다음 단계
SQL Server가 포함된 Azure 가상 머신을 만들어야 한다면 [Azure에 SQL Server Virtual Machine 프로비전](virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

Azure VM에서 실행되는 SQL Server에서 최상의 성능을 얻으려면 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 참조하세요.

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

### <a name="other-resources"></a>기타 리소스
* [Azure에 새 Active Directory 포리스트 설치](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Azure VM에서 가용성 그룹을 위한 장애 조치 클러스터 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)


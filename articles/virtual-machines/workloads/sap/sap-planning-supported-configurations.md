---
title: 'Azure의 SAP: Azure Vm에서 지원 되는 시나리오'
description: SAP 워크 로드에서 Azure Virtual Machines 지원 되는 시나리오
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad1567a3a6cba2c2fbc519ffe5d384aba25ab51d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648992"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 가상 머신의 SAP 워크로드 지원 시나리오
Azure에서 SAP NetWeaver, Business one `Hybris` 또는 S/4HANA 시스템 아키텍처를 디자인 하면 다양 한 아키텍처 및 도구를 사용 하 여 확장 가능 하 고 효율적 이며 가용성이 높은 배포를 수행할 수 있는 여러 가지 기회가 있습니다. 사용 된 운영 체제 또는 DBMS에 따라 다르지만 제한이 있습니다. 또한 온-프레미스에서 지원 되는 모든 시나리오가 Azure에서 동일한 방식으로 지원 되는 것은 아닙니다. 이 문서에서는 Azure Vm을 독점적으로 사용 하는 지원 되는 고가용성 구성과 고가용성 구성 및 아키텍처를 안내 합니다. [Hana Large instances](./hana-overview-architecture.md)에서 지원 되는 시나리오는 [hana large Instances에 대해 지원 되는 시나리오](./hana-supported-scenario.md)문서를 참조 하세요. 


## <a name="2-tier-configuration"></a>2 계층 구성
SAP 2 계층 구성은 동일한 서버 또는 VM 단위에서 실행 되는 SAP DBMS 및 응용 프로그램 계층의 결합 된 계층에서 빌드되는 것으로 간주 됩니다. 두 번째 계층은 사용자 인터페이스 계층으로 간주 됩니다. 2 계층 구성의 경우 DBMS 및 SAP 응용 프로그램 계층은 Azure VM의 리소스를 공유 합니다. 따라서 리소스에 대해 경쟁 하지 않는 방식으로 다양 한 구성 요소를 구성 해야 합니다. 또한 VM의 리소스를 oversubscribe 하지 않도록 주의 해야 합니다. 이러한 구성은 관련 된 다양 한 Azure 구성 요소에 대 한 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 외에는 고가용성을 제공 하지 않습니다.

이러한 구성을 그래픽으로 표시 하는 것은 다음과 같습니다.

![간단한 2 계층 구성](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

이러한 구성은 프로덕션 및 비프로덕션 사례에 대 한 SQL Server, Oracle, Db2, maxDB 및 SAP ASE의 DBMS 시스템에 대해 Windows, Red Hat, SUSE 및 Oracle Linux에서 지원 됩니다. DBMS로 SAP HANA의 경우 이러한 유형의 구성은 비프로덕션 사례에 대해서만 지원 됩니다. 여기에는 [AZURE HANA Large Instances](./hana-overview-architecture.md) 의 배포 사례도 포함 됩니다.
Azure에서 지원 되는 모든 OS/DBMS 조합의 경우이 유형의 구성이 지원 됩니다. 그러나 dbms 및 sap 구성 요소에서 메모리와 CPU 리소스를 경합 하 여 실제 사용 가능한 리소스를 초과 하는 방식으로 DBMS 및 SAP 구성 요소의 구성을 설정 하는 것은 필수입니다. DBMS에서 할당할 수 있는 메모리를 제한 하 여이 작업을 수행 해야 합니다. 또한 응용 프로그램 인스턴스에서 SAP 확장 메모리를 제한 해야 합니다. 또한 전체 VM의 CPU 사용을 모니터링 하 여 구성 요소가 CPU 리소스를 최대화 하지 않도록 해야 합니다. 

> [!NOTE]
> 프로덕션 SAP 시스템의 경우이 문서의 뒷부분에 설명 된 대로 추가 고가용성 및 최종 재해 복구 구성을 사용 하는 것이 좋습니다.


## <a name="3-tier-configuration"></a>3 계층 구성
이러한 구성에서는 SAP 응용 프로그램 계층과 DBMS 계층을 서로 다른 Vm으로 분리 합니다. 일반적으로 더 큰 시스템의 경우 SAP 응용 프로그램 계층의 리소스에 대해 더 유연 하지 않습니다. 가장 간단한 설정에서는 관련 된 다양 한 Azure 구성 요소에 대 한 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/) 을 벗어나는 고가용성이 없습니다. 

그래픽 표현은 다음과 같습니다.

![간단한 2 계층 구성](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

이 유형의 구성은 SQL Server, Oracle, Db2, SAP HANA, maxDB 및 SAP ASE for production 및 비프로덕션 사례에 대해 Windows, Red Hat, SUSE 및 Oracle Linux에서 지원 됩니다. [AZURE HANA Large Instances](./hana-overview-architecture.md)에 대 한 기본 배포 구성입니다. 간소화를 위해 sap 응용 프로그램 계층에서 sap Central Services와 SAP 대화 상자 인스턴스를 구분 하지 않았습니다. 이 간단한 3 계층 구성에서는 SAP Central Services에 대 한 고가용성 보호가 제공 되지 않습니다.

> [!NOTE]
> 프로덕션 SAP 시스템의 경우이 문서의 뒷부분에 설명 된 대로 추가 고가용성 및 최종 재해 복구 구성을 사용 하는 것이 좋습니다.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>VM 또는 HANA Large Instance unit 당 여러 DBMS 인스턴스
이 구성 형식에서는 Azure VM 또는 HANA Large Instance unit 당 여러 DBMS 인스턴스를 호스팅합니다. 동기는 유지 관리할 운영 체제가 적고 비용이 절감 될 수 있습니다. 다른 동기는 여러 DBMS 인스턴스 간에 더 큰 VM 또는 HANA 큰 인스턴스 단위의 리소스를 공유 하 여 더 유연 하 고 효율성을 높일 수 있습니다. 지금 까지는 비프로덕션 시스템을 위해 이러한 구성이 거의 표시 되지 않았습니다.

다음과 같은 구성이 있습니다.

![한 단위의 여러 DBMS 인스턴스](./media/sap-planning-supported-configurations/multiple-database-instances.png)

이 유형의 DBMS 배포는 다음에 대해 지원 됩니다.

- Windows의 SQL Server
- IBM Db2. [여러 인스턴스 (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html) 문서에서 세부 정보 찾기
- Oracle의 경우 자세한 내용은 [sap support note #1778431](https://launchpad.support.sap.com/#/notes/1778431) 및 관련 sap note를 참조 하세요.
- SAP HANA 한 VM의 여러 인스턴스는이 배포 메서드 MCOS를 호출 합니다. 자세한 내용은 SAP 문서 [한 호스트의 여러 SAP HANA 시스템 (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

한 호스트에서 여러 데이터베이스 인스턴스를 실행 하는 경우 서로 다른 인스턴스가 리소스에 대해 경쟁 하지 않으며 VM의 물리적 리소스 제한을 초과 하는지 확인 해야 합니다. 이는 VM을 공유 하는 모든 사용자가 할당할 수 있는 메모리의 일부를 제한 해야 하는 메모리의 경우 특히 그렇습니다. 이는 여러 데이터베이스 인스턴스에서 활용할 수 있는 CPU 리소스에도 적용 될 수 있습니다. 언급 된 모든 DBMS에는 인스턴스 수준에서 메모리 할당과 CPU 리소스를 제한할 수 있는 구성이 있습니다.
Azure Vm에 대 한 이러한 구성을 지원 하기 위해 다른 인스턴스에서 관리 하는 데이터베이스의 데이터 및 로그/다시 실행 로그 파일에 사용 되는 디스크 또는 볼륨이 분리 된 것으로 예상 됩니다. 또는 다른 DBMS 인스턴스에서 관리 하는 데이터베이스의 데이터 또는 로그/다시 실행 로그 파일은 동일한 디스크 또는 볼륨을 공유할 수 없습니다. 

HANA Large Instances에 대 한 디스크 구성은 구성 되어 전달 되며 [Hana Large instances에 대해 지원](./hana-supported-scenario.md#single-node-mcos)되는 시나리오에 자세히 설명 되어 있습니다. 

> [!NOTE]
> 프로덕션 SAP 시스템의 경우이 문서의 뒷부분에 설명 된 대로 추가 고가용성 및 최종 재해 복구 구성을 사용 하는 것이 좋습니다. 여러 DBMS 인스턴스가 있는 Vm은이 문서의 뒷부분에 설명 된 고가용성 구성에서 지원 되지 않습니다.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>하나의 VM에 여러 SAP 대화 상자 인스턴스
많은 경우에 여러 대화 상자 인스턴스가 운영 체제 미 설치 서버 또는 사설 클라우드에서 실행 되는 Vm에 배포 되었습니다. 이러한 구성의 이유는 특정 워크 로드, 비즈니스 기능 또는 작업 유형에 특정 SAP 대화 상자 인스턴스를 조정 하는 것입니다. 이러한 인스턴스를 별도의 Vm으로 격리 하지 않는 이유는 운영 체제 유지 관리 및 운영의 노력입니다. 또는 VM의 호스터 또는 운영자가 운영 및 운영 되 고 있는 VM 당 월별 요금을 요청 하는 경우에 대비 하 여 비용이 많이 듭니다. Azure에서 단일 VM 내에 여러 SAP 대화 상자 인스턴스를 호스팅하는 시나리오는 Windows, Red Hat, SUSE 및 Oracle Linux 운영 체제에서 프로덕션 및 비 프로덕션 용도로 지원 됩니다. 여러 SAP 응용 프로그램 서버 인스턴스가 단일 VM에서 실행 되는 경우 Windows 및 최신 Linux 커널에서 사용할 수 있는 SAP 커널 매개 변수 PHYS_MEMSIZE 설정 해야 합니다. 또한 SAP 확장 메모리의 자동 증가가 구현 되는 Windows와 같은 운영 체제에서 SAP 확장 메모리의 확장을 제한 하는 것이 좋습니다. SAP profile 매개 변수를 사용 하 여이 작업을 수행할 수 있습니다 `em/max_size_MB` .

Azure Vm 내에서 여러 SAP 대화 상자 인스턴스가 실행 되는 3 계층 구성에서 다음과 같이 보일 수 있습니다.

![한 단위의 여러 DBMS 인스턴스](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

간소화를 위해 sap 응용 프로그램 계층에서 sap Central Services와 SAP 대화 상자 인스턴스를 구분 하지 않았습니다. 이 간단한 3 계층 구성에서는 SAP Central Services에 대 한 고가용성 보호가 제공 되지 않습니다. 프로덕션 시스템의 경우 SAP Central Services를 보호 되지 않은 상태로 유지 하지 않는 것이 좋습니다. SAP 중앙 인스턴스 및 이러한 다중 SID 구성의 고가용성에 대 한 다중 SID 구성에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 섹션을 참조 하세요.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS 계층에 대 한 고가용성 보호
SAP 프로덕션 시스템을 배포 하는 경우 고가용성 구성의 상시 대기 유형을 고려해 야 합니다. 특히 전체 성능 및 확장성을 얻기 위해 데이터를 메모리에 로드 해야 하는 SAP HANA의 경우 Azure 서비스 복구는 고가용성을 위해 이상적인 측정값이 아닙니다. 

일반적으로 Microsoft에서는 docs.microsoft.com의 SAP 작업 섹션에 설명 된 고가용성 구성 및 소프트웨어 패키지만 지원 합니다. SAP note [#1928533](https://launchpad.support.sap.com/#/notes/1928533)에서 동일한 문을 읽을 수 있습니다. Microsoft는 SAP 워크 로드와 함께 Microsoft에서 문서화 하지 않은 다른 고가용성 타사 소프트웨어 프레임 워크를 지원 하지 않습니다. 이러한 경우 고가용성 프레임 워크의 타사 공급 업체는 지원 프로세스에 고객으로 참여 해야 하는 고가용성 구성의 지원 당사자입니다. 예외는이 문서에서 언급 될 예정입니다. 

일반적으로 Microsoft에서는 Azure Vm 또는 HANA 큰 인스턴스 단위에서 제한 된 고가용성 구성 집합을 지원 합니다. HANA Large Instances의 지원 되는 시나리오에 대해서는 [Hana Large instances에 대해 지원 되는 시나리오](./hana-supported-scenario.md)문서를 참조 하세요.

Azure Vm의 경우 다음과 같은 고가용성 구성이 DBMS 수준에서 지원 됩니다.

- SUSE 및 Red Hat의 Linux Pacemaker을 기반으로 시스템 복제를 SAP HANA 합니다. 자세한 문서를 참조 하세요.
    - [SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability.md)
    - [Red Hat Enterprise Linux의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability-rhel.md)
- SUSE 및 Red Hat의 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 를 사용 하 여 n + m 구성 SAP HANA 확장 합니다. 세부 정보는 다음 문서에 나와 있습니다.
    - [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용 하 여 Azure Vm에 대기 노드로 SAP HANA 확장 시스템 배포](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Red Hat Enterprise Linux에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- Windows 스케일 아웃 파일 서비스를 기반으로 장애 조치 (Failover) 클러스터를 SQL Server 합니다. 프로덕션 시스템에 대 한 권장 사항은 클러스터링 대신 SQL Server Always On를 사용 하는 것입니다. SQL Server Always On 별도의 저장소를 사용 하 여 더 나은 가용성을 제공 합니다. 세부 정보는이 문서에 설명 되어 있습니다. 
    - [Azure 가상 머신에 SQL Server 장애 조치(Failover) 클러스터 인스턴스 구성](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On는 Azure의 SQL Server에 대 한 Windows 운영 체제에서 지원 됩니다. Azure의 프로덕션 SQL Server 인스턴스에 대 한 기본 권장 사항입니다. 자세한 내용은 다음 문서에서 설명 합니다.
    - [Azure Virtual Machines의 SQL Server Always On 가용성 그룹 소개](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)
    - [다른 하위 지역의 Azure Virtual Machines에서 Always On 가용성 그룹 구성](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)
    - [Azure에서 Always On 가용성 그룹에 대한 부하 분산 장치 구성](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)
- Windows 및 Oracle Linux에 대 한 Oracle Data Guard. Oracle Linux에 대 한 세부 정보는 다음 문서에서 찾을 수 있습니다.
    - [Azure Linux 가상 머신에서 Oracle Data Guard 구현](../oracle/configure-oracle-dataguard.md)
- SUSE 및 RHEL에 대 한 IBM Db2 HADR의 SUSE 및 RHEL에 대 한 자세한 내용은 여기에서 제공 됩니다.
    - [Pacemaker를 사용 하는 SUSE Linux Enterprise Server의 Azure Vm에서 IBM Db2 LUW의 고가용성](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server의 Azure VM에서 IBM DB2 LUW의 고가용성](./high-availability-guide-rhel-ibm-db2-luw.md)
- 다음 문서에 설명 된 대로 SAP ASE 및 SAP maxDB 구성:
    - [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](./dbms_guide_sapase.md)
    - [Azure VM에서 SAP MaxDB, liveCache 및 Content Server 배포](./dbms_guide_maxdb.md)
- HANA 큰 인스턴스 고가용성 시나리오는 다음에 자세히 설명 되어 있습니다.
    - [HANA 큰 인스턴스에 대해 지원 되는 시나리오-고가용성을 위한 STONITH가 있는 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [HANA Large Instances에 대해 지원 되는 시나리오-호스트 자동 장애 조치 (failover) (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> 위에서 설명한 시나리오는 없지만 한 VM에서 여러 DBMS 인스턴스의 구성을 지원 합니다. 즉, 각 사례에서 하나의 데이터베이스 인스턴스만 VM 당 배포 하 고 설명 된 고가용성 메서드로 보호할 수 있습니다. 동일한 Windows 또는 Pacemaker 장애 조치 (failover) 클러스터에서 여러 DBMS 인스턴스를 보호 하는 것은 현재 시점에서 지원 **되지 않습니다** . 또한 Oracle Data Guard는 VM 배포 사례 당 단일 인스턴스에 대해서만 지원 됩니다. 

다양 한 데이터베이스 시스템에서 하나의 DBMS 인스턴스에서 여러 데이터베이스를 호스트할 수 있습니다. SAP HANA의 경우와 같이 여러 데이터베이스를 여러 데이터베이스 컨테이너 (MDC)에서 호스트할 수 있습니다. 이러한 다중 데이터베이스 구성이 하나의 장애 조치 (failover) 클러스터 리소스 내에서 작동 하는 경우 이러한 구성이 지원 됩니다. 지원 되지 않는 구성은 여러 클러스터 리소스를 필요로 하는 경우입니다. 여러 SQL Server 가용성 그룹을 정의 하는 구성의 경우에는 한 SQL Server 인스턴스를 사용 합니다.


![DBMS HA 구성](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

DBMS a/또는 운영 체제에 따라, Azure 부하 분산 장치와 같은 구성 요소가 솔루션 아키텍처의 일부로 필요 하거나 필요 하지 않을 수 있습니다. 

특히 maxDB의 경우 저장소 구성이 달라 야 합니다. MaxDB에서 데이터 및 로그 파일은 고가용성 구성을 위해 공유 저장소에 있어야 합니다. MaxDB의 경우에만 고가용성을 위해 공유 저장소가 지원 됩니다. 다른 모든 DBMS의 경우 노드당 저장소 스택은 유일 하 게 지원 되는 디스크 구성입니다.

다른 고가용성 프레임 워크는 존재 하며 Microsoft Azure에서 실행 되는 것으로 알려져 있습니다. 그러나 Microsoft는 이러한 프레임 워크를 테스트 하지 않았습니다. 이러한 프레임 워크를 사용 하 여 고가용성 구성을 작성 하려면 해당 소프트웨어의 공급자와 함께 다음을 수행 해야 합니다.

- 배포 아키텍처 개발
- 아키텍처 배포
- 아키텍처 지원

> [!IMPORTANT]
> Microsoft Azure Marketplace는 Azure 네이티브 저장소를 기반으로 저장소 솔루션을 제공 하는 다양 한 소프트 어플라이언스를 제공 합니다. 이러한 소프트 어플라이언스를 사용 하 여 NFS 공유를 만들 수 있습니다. 이론적으로는 대기 노드가 필요한 SAP HANA 스케일 아웃 배포에서 사용할 수 있습니다. 다양 한 이유로 Azure에서 Microsoft 및 SAP의 DBMS 배포에 대해 이러한 저장소 소프트 어플라이언스를 지원 하지 않습니다. SMB 공유에서 DBMS 배포는 현재 지원 되지 않습니다. NFS 공유의 DBMS 배포는 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)의 nfs 4.1 공유로 제한 됩니다.


## <a name="high-availability-for-sap-central-service"></a>SAP 중앙 서비스의 고가용성
SAP 중앙 서비스는 SAP 구성의 두 번째 단일 실패 지점입니다. 따라서 이러한 중앙 서비스 프로세스도 보호 해야 합니다. SAP 워크 로드에 대해 지원 되 고 문서화 된 제품은 다음과 같습니다.

- Sapmnt 및 전역 전송 디렉터리에 대해 Windows 스케일 아웃 파일 서비스를 사용 하는 windows 장애 조치 (Failover) 클러스터 서버. 세부 정보는 다음 문서에서 설명 합니다.
    - [Azure에서 파일 공유를 사용하여 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스 클러스터링](./sap-high-availability-guide-wsfc-file-share.md)
    - [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Sapmnt 및 전역 전송 디렉터리에 대 한 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 기반 SMB 공유를 사용 하는 Windows 장애 조치 (Failover) 클러스터 서버. 세부 정보는 다음 문서에 나와 있습니다.
    - [SAP 응용 프로그램용 SMB (Azure NetApp Files)를 사용 하는 Windows의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](./high-availability-guide-windows-netapp-files-smb.md)
- SIOS 기반 Windows 장애 조치 (Failover) 클러스터 서버 `Datakeeper` . Microsoft에서 문서화 했지만이 솔루션을 사용 하는 경우 SIOS 지원과 함께 사용할 수 있도록 SIOS와의 지원 관계가 필요 합니다. 세부 정보는 다음 문서에서 설명 합니다.
    - [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS를 위한 SAP HA용 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker on SUSE 운영 체제를 사용 하 여 두 개의 SUSE Vm 및 파일 복제를 사용 하 여 항상 사용 가능한 NFS 공유를 만듭니다 `drdb` . 세부 정보는이 문서에 설명 되어 있습니다.
    - [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)에서 제공 하는 NFS 공유를 활용 하는 Pacemaker SUSE 운영 체제 세부 정보는에 설명 되어 있습니다.
    - [SAP 애플리케이션용 Azure NetApp Files를 사용하여 SUSE Linux Enterprise Server에서 Azure VM의 SAP NetWeaver 고가용성 실현](./high-availability-guide-suse-netapp-files.md)
- 클러스터에서 호스트 되는 NFS 공유를 사용 하는 Red Hat 운영 체제의 Pacemaker `glusterfs` 세부 정보는 다음 문서에서 찾을 수 있습니다.
    - [Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](./high-availability-guide-rhel.md)
    - [`GlusterFS` SAP NetWeaver에 대 한 Red Hat Enterprise Linux의 Azure Vm](./high-availability-guide-rhel-glusterfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)에서 호스트 되는 NFS 공유를 사용 하는 red Hat 운영 체제의 Pacemaker입니다. 세부 정보는이 문서에 설명 되어 있습니다.
    - [SAP 응용 프로그램에 대해 Azure NetApp Files을 사용 하는 Red Hat Enterprise Linux에서 SAP NetWeaver에 대 한 Azure Virtual Machines 고가용성](./high-availability-guide-rhel-netapp-files.md)

나열 된 솔루션 중에서 제품을 지원 하 `Datakeeper` 고 문제 발생 시 직접 SIOS와 연계 하려면 sios와의 지원 관계가 필요 합니다. Windows, Red Hat 및/또는 SUSE OS에 대 한 사용이 허가 된 방식에 따라 나열 된 고가용성 구성을 완전히 지원 하려면 OS 공급자와 지원 계약을 체결 해야 할 수도 있습니다.

구성도 다음과 같이 표시 될 수 있습니다.

![DBMS 및 ASCS HA 구성](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

그래픽의 오른쪽에는 항상 사용 가능한 SAP 중앙 서비스가 표시 됩니다. 장애 조치 (failover) 시 장애 조치 (failover)를 수행할 수 있는 장애 조치 (failover) 클러스터 프레임 워크로 SAP Central 서비스를 보호 하는 것 외에도 항상 사용 가능한 NFS 또는 SMB 공유 또는 Windows 공유 디스크를 사용 하 여 단일 VM의 존재 여부와 관계 없이 sapmnt 및 전역 전송 디렉터리를 사용할 수 있는지 확인 해야 합니다. Windows 장애 조치 (Failover) 클러스터 서버 및 Pacemaker 같은 일부 솔루션에는 트래픽을 정상 노드로 전달 하거나 다시 전달 하는 Azure 부하 분산 장치가 필요 합니다.

표시 된 목록에 Oracle Linux 운영 체제를 언급 하지 않습니다. Oracle Linux는 Pacemaker를 클러스터 프레임 워크로 지원 하지 않습니다. Oracle Linux에서 SAP 시스템을 배포 하려는 경우 Oracle Linux에 대 한 고가용성 프레임 워크가 필요한 경우 타사 공급 업체와 함께 작업 해야 합니다. 공급자 중 하나는 Azure의 SAP에서 지원 되는 Linux 용 보호 도구 모음을 사용 하는 SIOS입니다. 자세한 내용은 SAP note [#1662610-Linux 용 SIOS 보호 도구 모음에 대 한 지원 세부](https://launchpad.support.sap.com/#/notes/1662610) 정보를 참조 하세요.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>위에 나열 된 SAP Central Services 시나리오에서 지원 되는 저장소
Azure storage 유형의 하위 집합만 SAP Central Services 클러스터 시나리오에서 사용 하는 데 필요한 항상 사용 가능한 NFS 또는 SMB 공유를 제공 하기 때문에 지원 되는 저장소 유형 목록

- Windows 확장 파일 서버를 사용 하는 windows 장애 조치 (Failover) 클러스터 서버는 Azure NetApp Files를 제외 하 고 모든 네이티브 Azure 저장소 유형에 배포할 수 있습니다. 그러나 처리량 및 IOPS의 뛰어난 서비스 수준 계약으로 인해 Premium Storage 활용 하는 것이 좋습니다.
- Azure NetApp Files에서 SMB가 있는 Windows 장애 조치 (Failover) 클러스터 서버는 Azure NetApp Files에서 지원 됩니다. Azure 파일 서비스에 대 한 SMB 공유는 지금은 지원 **되지 않습니다** .
- SIOS를 기반으로 하는 windows 공유 디스크가 포함 된 windows 장애 조치 (Failover) 클러스터 서버는 `Datakeeper` Azure NetApp Files를 제외 하 고 모든 네이티브 Azure 저장소 유형에 배포할 수 있습니다. 그러나 처리량 및 IOPS의 뛰어난 서비스 수준 계약으로 인해 Premium Storage 활용 하는 것이 좋습니다.
- Azure NetApp Files에서 NFS 공유를 사용 하는 SUSE 또는 Red Hat Pacemaker는 Azure NetApp Files에서 지원 됩니다. 
- Azure NetApp Files를 제외 하 고 `drdb` 네이티브 Azure storage 유형을 사용 하 여 두 vm 간의 구성을 사용 하는 SUSE Pacemaker을 지원 합니다. 그러나 처리량 및 IOPS의 뛰어난 서비스 수준 계약으로 인해 Premium Storage 활용 하는 것이 좋습니다.
- `glusterfs`NFS 공유를 제공 하기 위해 사용 하는 Red Hat Pacemaker는 Azure NetApp Files를 제외 하 고 네이티브 Azure storage 유형을 사용 하 여 지원 됩니다. 그러나 처리량 및 IOPS의 뛰어난 서비스 수준 계약으로 인해 Premium Storage 활용 하는 것이 좋습니다.

> [!IMPORTANT]
> Microsoft Azure Marketplace는 Azure 네이티브 저장소를 기반으로 저장소 솔루션을 제공 하는 다양 한 소프트 어플라이언스를 제공 합니다. 이러한 소프트 어플라이언스는 NFS 또는 SMB 공유를 만드는 데 사용할 수 있습니다. 이론적으로는 장애 조치 (failover) 클러스터 된 SAP 중앙 서비스 에서도 사용할 수 있습니다. 이러한 솔루션은 Microsoft에서 SAP 워크 로드에 대해 직접 지원 되지 않습니다. 이러한 솔루션을 사용 하 여 NFS 또는 SMB 공유를 만드는 경우 저장소 소프트 어플라이언스에서 소프트웨어를 소유 하는 타사가 SAP 중앙 서비스 구성에 대 한 지원을 제공 해야 합니다.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>다중 SID SAP 중앙 서비스 장애 조치 (failover) 클러스터
SAP는 대량 SAP 지형에서 필요한 Vm 수를 줄이기 위해 장애 조치 (failover) 클러스터 구성에서 여러 다른 SAP 시스템의 SAP Central Services 인스턴스를 실행 하도록 허용 합니다. 30 개 이상의 NetWeaver 또는 S/4HANA 프로덕션 시스템이 있는 경우를 가정 합니다. 다중 SID 클러스터링이 없으면 이러한 구성에 30 개 이상의 Windows 또는 Pacemaker 장애 조치 (failover) 클러스터 구성에서 60 이상의 Vm이 있어야 합니다. 필요한 DBMS 장애 조치 (failover) 클러스터 외에도 장애 조치 (failover) 클러스터 구성의 두 노드 간에 여러 SAP central services를 배포 하면 Vm 수를 크게 줄일 수 있습니다. 그러나 단일 두 노드 클러스터 구성에 여러 SAP Central services 인스턴스를 배포 하는 경우에도 몇 가지 단점이 있습니다. 클러스터 구성에서 단일 VM과 관련 된 문제는 여러 SAP 시스템에 적용 됩니다. 여러 프로덕션 SAP 시스템에 영향을 주므로 클러스터 구성에서 실행 되는 게스트 OS에 대 한 유지 관리에 더 많은 조정이 필요 합니다. SAP LaMa와 같은 도구는 시스템 복제 프로세스에서 다중 SID 클러스터링을 지원 하지 않습니다.

Azure에서 ENSA1 및 ENSA2를 사용 하는 Windows 운영 체제에 대해 다중 SID 클러스터 구성이 지원 됩니다. 권장 사항은 이전 큐에 ENSA1 (복제 서비스 아키텍처)를 단일 다중 SID 클러스터의 새 아키텍처 (ENSA2)와 결합 하지 않는 것입니다. 이러한 아키텍처에 대 한 자세한 내용은 문서에 설명 되어 있습니다.

- [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

SUSE의 경우 Pacemaker를 기반으로 하는 다중 SID 클러스터도 지원 됩니다. 지금 까지는 구성이 지원 됩니다.

- 최대 5 개의 SAP ASCS/SCS 인스턴스
- 이전 큐에 대기 중인 복제 서버 ice 아키텍처 (ENSA1)
- 두 노드 Pacemaker 클러스터 구성

이 구성은 [sap 응용 프로그램용 SUSE Linux Enterprise Server의 Azure vm에서 Sap NetWeaver에 대 한 고가용성](./high-availability-guide-suse-multi-sid.md) 에 설명 되어 있습니다. 다중 SID 가이드

큐에 복제 서버 구조적으로를 사용 하는 다중 SID 클러스터는 다음과 같습니다.

![DBMS 및 ASCS HA 구성](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA 스케일 아웃 시나리오
SAP HANA 스케일 아웃 시나리오는 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나열 된 HANA 인증 Azure vm의 하위 집합에 대해 지원 됩니다. ' 클러스터링 ' 열에서 ' 예 '로 표시 된 모든 Vm을 OLAP 또는 S/4HANA 확장에 사용할 수 있습니다. Standby를 사용 하지 않는 구성은 다음과 같은 Azure Storage 형식에서 지원 됩니다. 

- /Hana/log 볼륨에 대해 Azure Write accelerator를 포함 하는 azure Premium Storage
- [Ultra Disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

OLAP 또는 대기 노드를 포함 하는 S/4HANA에 대 한 SAP HANA 스케일 아웃 구성은 Azure NetApp Files에서 호스트 되는 NFS 공유 에서만 지원 됩니다.

대기 노드를 사용 하거나 사용 하지 않는 정확한 저장소 구성에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 
- [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Red Hat Enterprise Linux에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP support note #2080991](https://launchpad.support.sap.com/#/notes/2080991)

HANA 대규모 인스턴스 지원 HANA 스케일 아웃 구성에 대 한 자세한 내용은 다음 설명서를 적용 합니다.

- [HANA 대규모 인스턴스에 대해 지원 되는 시나리오를 대기 상태로 확장](./hana-supported-scenario.md#scale-out-with-standby)
- [HANA 대규모 인스턴스에 대해 지원 되는 시나리오를 대기 하지 않고 확장](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>재해 복구 시나리오
여러 가지 재해 복구 시나리오가 지원 됩니다. 표를 분리 하는 전체 Azure 지역에 대 한 보정을 수행 하는 아키텍처로 재해 아키텍처를 정의 합니다. 즉, SAP 환경을 실행 하기 위한 대상으로 다른 Azure 지역으로 재해 복구 대상이 필요 합니다. DBMS 계층 및 비 DBMS 계층에서 메서드와 구성을 분리 합니다. 

### <a name="dbms-layer"></a>DBMS 계층
DBMS 계층의 경우 DBMS 기본 복제 메커니즘 (예: Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always On 또는 HANA 시스템 복제)을 사용 하는 구성이 지원 됩니다. 이러한 경우에는 단일 Azure 지역 내에 배포 되는 일반적인 고가용성 시나리오에서와 같이 동기식이 아닌 비동기 복제 스트림이 필요 합니다. 이러한 지원 되는 DBMS 재해 복구 구성의 일반적인 예는 [Azure 지역 간 가용성 SAP HANA](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)문서에 설명 되어 있습니다. 이 섹션의 두 번째 그림에서는 HANA를 사용 하는 시나리오에 대해 설명 합니다. SAP 응용 프로그램에 대해 지원 되는 주 데이터베이스는 모두 이러한 시나리오에서 배포할 수 있습니다.

VM은 전체 워크 로드 트래픽을 발생 시 지 않으므로 재해 복구 지역에서 더 작은 VM을 대상 인스턴스로 사용할 수 있습니다. 이렇게 하려면 다음 사항을 염두에 두어야 합니다.

- 더 작은 VM 유형을 사용 하면 작은 Vm 보다 많은 디스크가 연결 되지 않습니다.
- 더 작은 Vm은 네트워크 및 저장소 처리량이 적습니다.
- 여러 VM 제품군의 크기를 조정 하는 것은 여러 vm이 하나의 Azure 가용성 집합에서 수집 되거나 M 시리즈 패밀리와 Mv2 Vm 제품군 사이에서 다시 크기가 조정 되는 경우 문제가 될 수 있습니다.
- 최소 지연 및 충분 한 CPU 및 메모리 리소스를 사용 하 여 변경 스트림을 수신 하 여 데이터에 대 한 최소 지연 시간으로 이러한 변경 내용을 적용할 수 있는 데이터베이스 인스턴스에 대 한 CPU 및 메모리 사용량  

다양 한 VM 크기의 제한 사항에 대 한 자세한 내용은 여기를 참조 [하세요](../../sizes.md) . 

DR 대상을 배포 하는 다른 지원 되는 방법은 비프로덕션 SAP 인스턴스의 비프로덕션 DBMS 인스턴스를 실행 하는 VM에 두 번째 DBMS 인스턴스를 설치 하는 것입니다. 이는 DR 시나리오에서 주 인스턴스로 작동 해야 하는 특정 대상 인스턴스에 필요한 메모리, CPU 리소스, 네트워크 대역폭 및 저장소 대역폭을 확인 해야 하기 때문에 좀 더 어려울 수 있습니다. 특히 HANA에서는 데이터가 DR 대상 인스턴스로 미리 로드 되지 않도록 공유 호스트에서 DR 대상으로 작동 하는 인스턴스를 구성 하는 것이 좋습니다.

HANA Large Instance DR 시나리오의 경우 다음 문서를 확인 합니다.

- [저장소 복제를 사용 하는 DR이 있는 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [저장소 복제를 사용 하는 DR (다목적)이 포함 된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [저장소 복제를 사용 하는 DR (다목적)이 포함 된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [저장소 복제를 사용 하는 HSR 및 DR을 사용 하는 고가용성](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [저장소 복제를 사용 하 여 DR 확장](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [HSR를 사용 하는 DR을 사용 하는 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [단일 노드 HSR에서 DR으로 (비용 최적화)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [HSR에서 고가용성 및 재해 복구](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [HSR를 통한 고가용성 및 재해 복구 (비용 최적화)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [HSR를 사용 하 여 DR으로 확장](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 사용은 SAP 워크 로드에서 DBMS 배포에 대해 테스트 되지 않았습니다. 따라서이 시점에서 SAP 시스템의 DBMS 계층에 대해서는 지원 되지 않습니다. Microsoft 및 SAP에서 나열 되지 않은 다른 복제 방법은 지원 되지 않습니다. 타사 소프트웨어를 사용 하 여 여러 Azure 지역 간에 SAP 시스템의 DBMS 계층을 복제 하는 것은 소프트웨어 공급 업체에서 지원 해야 하며 Microsoft 및 SAP 지원 채널을 통해 지원 되지 않습니다. 
 
## <a name="non-dbms-layer"></a>비 DBMS 계층
SAP 응용 프로그램 계층 및 최종 공유 또는 필요한 저장소 위치에 대 한 두 가지 주요 시나리오는 고객이 활용 합니다.

- 두 번째 Azure 지역의 재해 복구 대상은 프로덕션 또는 비프로덕션 용도로 사용 되지 않습니다. 이 시나리오에서 재해 복구 대상으로 작동 하는 Vm은 배포 되지 않으며 프로덕션 SAP 응용 프로그램 계층의 이미지 및 변경 내용이 재해 복구 지역에 복제 됩니다. 이러한 작업을 수행할 수 있는 기능을 [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md)합니다. Azure Site Recovery는 다음과 같은 Azure와 Azure 간 복제 시나리오를 지원 합니다. 
- 재해 복구 대상은 실제로 비프로덕션 시스템에서 사용 중인 Vm입니다. 전체 SAP 환경은 일반적으로 다른 지역에 있는 한 지역 및 비프로덕션 시스템에서 프로덕션 시스템을 사용 하 여 서로 다른 두 Azure 지역에 걸쳐 분산 됩니다. 고객의 많은 배포에서 고객은 프로덕션 시스템에 해당 하는 비프로덕션 시스템을 보유 하 고 있습니다. 고객은 프로덕션 응용 프로그램 인스턴스가 응용 프로그램 계층 비프로덕션 시스템에 미리 설치 되어 있습니다. 장애 조치 (failover)의 경우 프로덕션이 아닌 인스턴스가 종료 되 고, 프로덕션 Vm의 가상 이름이 비프로덕션 Vm으로 이동 하 고 (DNS에 새 IP 주소를 할당 한 후), 사전 설치 된 프로덕션 인스턴스가 시작 됩니다.

### <a name="sap-central-services-clusters"></a>SAP 중앙 서비스 클러스터
공유 디스크 (Windows), SMB 공유 (Windows) 또는 NFS 공유를 사용 하는 SAP Central Services 클러스터는 복제 하는 데 약간의 어려움이 있습니다. Windows 쪽에서는 Windows 저장소 복제가 가능한 솔루션입니다. Linux에서 rsync는 실행 가능한 솔루션입니다.



## <a name="non-supported-scenario"></a>지원 되지 않는 시나리오
Azure 아키텍처에서 SAP 워크 로드에 대해 지원 되지 않는 시나리오 목록이 있습니다. **지원 되지 않음** 은 SAP와 Microsoft가 이러한 구성을 지원할 수 없으며, 이러한 아키텍처를 설정 하는 소프트웨어를 제공 하는 궁극적으로 관련 된 타사를 연기 해야 합니다. 두 범주는 다음과 같습니다.

- 저장소 소프트 어플라이언스: Azure marketplace에 제공 되는 다양 한 저장소 소프트 기기가 있습니다. 일부 공급 업체는 SAP 소프트웨어와 관련 하 여 Azure에서 이러한 저장소 소프트 어플라이언스를 사용 하는 방법에 대 한 자체 설명서를 제공 합니다. 이러한 저장소 소프트 어플라이언스를 포함 하는 구성 또는 배포 지원은 해당 저장소 소프트 어플라이언스의 공급 업체에서 제공 해야 합니다. 이 팩트는 [SAP support note](https://launchpad.support.sap.com/#/notes/2015553) 에도 매니페스트 됩니다 #2015553
- 고가용성 프레임 워크: Pacemaker 및 Windows Server 장애 조치 (Failover) 클러스터만 Azure에서 SAP 워크 로드에 대해 지원 되는 고가용성 프레임 워크입니다. 앞서 설명한 것 처럼 SIOS의 솔루션은 `Datakeeper` Microsoft에서 설명 하 고 문서화 합니다. 그러나 SIOS의 구성 요소는 `Datakeeper` 해당 구성 요소를 제공 하는 공급 업체로 SIOS를 통해 지원 되어야 합니다. SAP는 다양 한 SAP 노트의 인증 된 고가용성 프레임 워크로도 나열 됩니다. 그 중 일부는 Azure 용 타사 공급 업체의 인증을 받았습니다. 그럼에도 불구 하 고 해당 제품을 사용 하는 구성에 대 한 지원은 제품 공급 업체에서 제공 해야 합니다. 공급 업체 마다 SAP 지원 프로세스에 서로 다른 통합이 있습니다. Azure에 배포 된 SAP 구성에서 제품을 사용 하도록 결정 하기 전에 특정 공급 업체에 가장 적합 한 지원 프로세스를 명확 하 게 설명 해야 합니다.
- 데이터베이스 파일이 공유 디스크에 상주 하는 공유 디스크 클러스터는 maxDB를 제외 하 고 지원 되지 않습니다. 다른 모든 데이터베이스의 경우 지원 되는 솔루션은 고가용성 시나리오를 구성 하기 위해 SMB 또는 NFS 공유 또는 공유 디스크 대신 별도의 저장소 위치를 사용 하는 것입니다.

지원 되지 않는 다른 시나리오는 다음과 같은 시나리오입니다.

- Sap 응용 프로그램 계층과 sap의 공통 아키텍처에서 sap DBMS 계층 사이에 더 큰 네트워크 대기 시간을 도입 하는 배포 시나리오 (NetWeaver, S/4HANA 및 예:) `Hybris` 다음 내용이 포함됩니다.
    - 계층 중 하나를 온-프레미스에 배포 하는 반면 다른 계층은 Azure에 배포 됩니다.
    - DBMS 계층이 아닌 다른 Azure 지역에 있는 시스템의 SAP 응용 프로그램 계층 배포
    - Azure에 공동 배치 되는 데이터 센터에서 한 계층을 배포 하는 방법 (이러한 아키텍처 패턴이 Azure native service에서 제공 되는 경우 제외)
    - SAP 응용 프로그램 계층과 DBMS 계층 사이에 네트워크 가상 어플라이언스 배포
    - SAP DBMS 계층 또는 SAP 전역 전송 디렉터리에 대해 Azure 데이터 센터에 공동 배치 된 데이터 센터에서 호스팅되는 저장소 활용
    - 서로 다른 두 클라우드 공급 업체를 사용 하 여 두 계층을 배포 합니다. 예를 들어 Oracle 클라우드 인프라에 DBMS 계층을 배포 하 고 Azure에서 응용 프로그램 계층을 배포 합니다.
- 다중 인스턴스 HANA Pacemaker 클러스터 구성
- Windows에서 지원 되는 SAP 데이터베이스용 SOFS 또는 ANF의 SMB를 통해 공유 디스크를 사용 하는 windows 클러스터 구성. 대신 특정 데이터베이스의 기본 고가용성 복제를 사용 하 고 별도의 저장소 스택을 사용 하는 것이 좋습니다.
- 을 (를) 제외 하 고, NFS에 있는 데이터베이스 파일을 사용 하 여 Linux에서 지원 되는 SAP 데이터베이스 배포 SAP HANA
- Windows 및 Oracle Linux 다른 게스트 OS에 Oracle DBMS 배포 [SAP support note](https://launchpad.support.sap.com/#/notes/2039619) 를 참조 하세요 #2039619

테스트를 하지 않았으므로 다음과 같은 목록에 대 한 경험이 없는 시나리오

- DBMS 계층 Vm 복제를 Azure Site Recovery 합니다. 따라서 잠재적 재해 복구 구성에 대 한 데이터베이스 기본 비동기 복제 기능을 활용 하는 것이 좋습니다.
 

## <a name="next-steps"></a>다음 단계
[SAP NetWeaver에 대 한 Azure Virtual Machines 계획 및 구현](./planning-guide.md) 의 다음 단계를 참조 하십시오.




  
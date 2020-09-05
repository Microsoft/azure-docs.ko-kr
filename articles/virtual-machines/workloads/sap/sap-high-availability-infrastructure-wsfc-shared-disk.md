---
title: WSFC&공유 디스크를 사용 하는 SAP ASCS/SCS 용 Azure 인프라 | Microsoft Docs
description: Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 위한 SAP HA용 Azure 인프라를 준비하는 방법을 알아봅니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f389581d8fbeb912507b303c46109dd08fcab8d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871519"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS를 위한 SAP HA용 Azure 인프라 준비

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 다중 SID 고가용성 구성)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows OS][Logo_Windows] Windows


이 문서에서는 SAP ASCS 인스턴스를 클러스터링 하는 옵션으로 *클러스터 공유 디스크* 를 사용 하 여 Windows 장애 조치 (failover) 클러스터에서 고가용성 SAP ascs/SCS 인스턴스를 설치 하 고 구성 하기 위해 Azure 인프라를 준비 하기 위해 수행 하는 단계를 설명 합니다.
*클러스터 공유 디스크* 에 대 한 두 가지 대안은 설명서에 나와 있습니다.

- [Azure 공유 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)
- [Sios DataKeeper 클러스터 버전](https://us.sios.com/products/datakeeper-cluster/) 을 사용 하 여 미러된 저장소를 만들면 클러스터 된 공유 디스크를 시뮬레이션 합니다. 

제공 된 구성은 [Azure 근접 배치 그룹 (PPG)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 에 의존 하 여 SAP 워크 로드에 대 한 최적의 네트워크 대기 시간을 실현 합니다. 설명서는 데이터베이스 계층을 포함 하지 않습니다.  

> [!NOTE]
> Azure 공유 디스크를 사용 하기 위한 필수 구성 요소는 azure 근접 배치 그룹입니다.
 

## <a name="prerequisites"></a>전제 조건

설치를 시작하기 전에 먼저 다음 문서를 검토하세요.

* [아키텍처 가이드: Windows 장애 조치(Failover) 클러스터에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>ASCS Vm 만들기

SAP ASCS/SCS 클러스터의 경우 Azure 가용성 집합에 두 개의 Vm을 배포 합니다. 동일한 근접 배치 그룹에 Vm을 배포 합니다. Vm을 배포한 후:  
- SAP ASCS/SCS 인스턴스에 대 한 Azure 내부 Load Balancer 만들기 
- AD 도메인에 Windows Vm 추가

표시 된 시나리오의 호스트 이름 및 IP 주소는 다음과 같습니다.

| 호스트 이름 역할 | 호스트 이름 | 고정 IP 주소 | 가용성 집합 | 근접 배치 그룹 |
| --- | --- | --- |---| ---|
| 첫 번째 클러스터 노드 ASCS/SCS 클러스터 |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 두 번째 클러스터 노드 ASCS/SCS 클러스터 |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| 클러스터 네트워크 이름 | pr1clust |10.0.0.42 (Win 2016 클러스터에**만** 해당) | 해당 없음 | 해당 없음 |
| ASCS 클러스터 네트워크 이름 | pr1-ascscl |10.0.0.43 | 해당 없음 | 해당 없음 |
| ERS 클러스터 네트워크 이름 (ERS2에**만** 해당) | pr1-erscl |10.0.0.44 | 해당 없음 | 해당 없음 |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 내부 부하 분산 장치 만들기

SAP ASCS, SAP SCS 및 새 SAP ERS2 가상 호스트 이름 및 가상 IP 주소를 사용 합니다. Azure에서 [부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 는 가상 IP 주소를 사용 하는 데 필요 합니다. [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)를 사용 하는 것이 좋습니다. 


다음 목록에서는 (A) SCS/ERS 부하 분산 장치를 구성 하는 방법을 보여 줍니다. 동일한 Azure 부하 분산 장치에서 실행 되는의 SAP ASCS 및 ERS2에 대 한 구성입니다.  

**(A)SCS**
- 프런트 엔드 구성
    - 정적 ASCS/SCS IP 주소 **10.0.0.43**
- 백 엔드 구성  
    (A) SCS/ERS 클러스터의 일부가 되어야 하는 모든 가상 컴퓨터를 추가 합니다. 이 예제에서 Vm **pr1** 및 pr1-ascs- **11**.
- 프로브 포트
    - 포트 620**nr** 는 프로토콜 (TCP), 간격 (5), 비정상 임계값 (2)에 대 한 기본 옵션을 그대로 유지 합니다.
- 부하 분산 규칙
    - 표준 Load Balancer를 사용하는 경우 HA 포트를 선택합니다.
    - 기본 Load Balancer를 사용하는 경우 다음 포트에 대한 부하 분산 규칙을 만듭니다.
        - 32**nr** TCP
        - 36**nr** TCP
        - 39**nr** TCP
        - 81**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - 유휴 시간 제한 (분)이 max value 30으로 설정 되어 있고 부동 IP (direct server return)가 사용 하도록 설정 되어 있는지 확인 합니다.

**ERS2**

큐에 대기 중인 복제 서버 2 (ERS2)도 클러스터 됨에 따라 SAP ASCS/SCS IP 외에도 Azure ILB에서 ERS2 가상 IP 주소를 구성 해야 합니다. 이 섹션은 큐에 넣기 복제 서버 2 아키텍처를 사용 하는 경우에만 적용 됩니다.  
- 두 번째 프런트 엔드 구성
    - 정적 SAP ERS2 IP 주소 **10.0.0.44**

- 백 엔드 구성  
  Vm이 ILB 백 엔드 풀에 이미 추가 되었습니다.  

- 두 번째 프로브 포트
    - 포트 621**nr**  
    프로토콜 (TCP), 간격 (5), 비정상 임계값 (2)에 대 한 기본 옵션을 그대로 둡니다.

- 두 번째 부하 분산 규칙
    - 표준 Load Balancer를 사용하는 경우 HA 포트를 선택합니다.
    - 기본 Load Balancer를 사용하는 경우 다음 포트에 대한 부하 분산 규칙을 만듭니다.
        - 32**nr** TCP
        - 33**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - 유휴 시간 제한 (분)이 max value 30으로 설정 되어 있고 부동 IP (direct server return)가 사용 하도록 설정 되어 있는지 확인 합니다.


> [!TIP]
> [Azure 공유 디스크를 사용 하는 SAP ASCS/SCS 인스턴스에 대 한 WSFC의 Azure Resource Manager 템플릿을](https://github.com/robotechredmond/301-shared-disk-sap)사용 하면 ERS1를 사용 하는 하나의 sap SID에 대해 Azure 공유 디스크를 사용 하 여 인프라 준비를 자동화할 수 있습니다.  
> Azure ARM 템플릿에서는 두 개의 Windows 2019 또는 2016 Vm을 만들고, Azure 공유 디스크를 만들고 Vm에 연결 합니다. Azure 내부 Load Balancer 생성 및 구성 됩니다. 자세한 내용은 ARM 템플릿을 참조 하세요. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> ASCS/SCS 인스턴스의 두 클러스터 노드에 대 한 레지스트리 항목 추가

연결이 일정 시간 동안 유휴 상태이 고 유휴 시간 제한을 초과 하는 경우 연결을 닫을 수 Azure Load Balancer. SAP 작업 프로세스는 첫 번째 큐에 넣기/큐에서 제거 요청을 전송 해야 하는 즉시 SAP 큐에 넣기 프로세스에 대 한 열린 연결을 처리 합니다. 이러한 연결이 중단 되지 않도록 하려면 두 클러스터 노드에서 TCP/IP KeepAliveTime 및 KeepAliveInterval 값을 변경 합니다. ERS1를 사용 하는 경우이 문서의 뒷부분에 설명 된 대로 SAP 프로필 매개 변수를 추가 해야 합니다.
두 클러스터 노드에서 다음 레지스트리 항목을 변경 해야 합니다.

- KeepAliveTime
- KeepAliveInterval

| 경로| 변수 이름 | 변수 유형  | 값 | 설명서 |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD(10진수) |120000 |[KeepAliveTime](https://technet.microsoft.com/library/cc957549.aspx) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD(10진수) |120000 |[KeepAliveInterval](https://technet.microsoft.com/library/cc957548.aspx) |


변경 내용을 적용하려면 두 클러스터 노드를 모두 다시 시작합니다.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 도메인에 Windows Vm 추가
가상 컴퓨터에 고정 IP 주소를 할당 한 후에는 가상 컴퓨터를 도메인에 추가 합니다. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Windows 장애 조치 (failover) 클러스터 설치 및 구성 

### <a name="install-the-windows-failover-cluster-feature"></a>Windows 장애 조치 (failover) 클러스터 기능 설치

클러스터 노드 중 하나에서 다음 명령을 실행 합니다.

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

기능 설치가 완료 되 면 두 클러스터 노드를 다시 부팅 합니다.  

### <a name="test-and-configure-windows-failover-cluster"></a>Windows 장애 조치 (failover) 클러스터 테스트 및 구성 

Windows 2019에서 클러스터는 Azure에서 실행 되는 것을 자동으로 인식 하 고 클러스터 관리 IP의 기본 옵션으로, 분산 네트워크 이름을 사용 합니다. 따라서 클러스터 노드 로컬 IP 주소를 사용 합니다. 결과적으로 클러스터에 대 한 전용 (가상) 네트워크 이름이 필요 하지 않으며, Azure 내부 Load Balancer에서이 IP 주소를 구성할 필요가 없습니다.

자세한 내용은 [Windows Server 2019 장애 조치 (Failover) 클러스터링 새 기능](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) 클러스터 노드 중 하나에서이 명령을 실행 합니다.

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>클러스터 클라우드 쿼럼 구성
Windows Server 2016 또는 2019을 사용 하는 경우 [Azure 클라우드 감시](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)를 클러스터 쿼럼으로 구성 하는 것이 좋습니다.

클러스터 노드 중 하나에서 다음 명령을 실행 합니다.

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Windows 장애 조치 (failover) 클러스터 임계값 조정
 
Windows 장애 조치 (failover) 클러스터를 성공적으로 설치한 후에는 Azure에 배포 된 클러스터에 적합 하도록 일부 임계값을 조정 해야 합니다. 변경할 매개 변수는 [장애 조치(failover) 클러스터 네트워크 임계값 조정](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)에 설명되어 있습니다. ASCS/SCS의 Windows 클러스터 구성에 포함된 2개의 VM이 동일한 서브넷에 있다는 전제하에, 매개 변수 값을 다음과 같이 변경합니다.
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

이러한 설정은 고객과 테스트 되었으며 적절 한 손상을 제공 합니다. 복원 력이 충분 하지만 SAP 워크 로드 또는 VM 오류의 실제 오류 조건에 충분 한 속도의 장애 조치 (failover)도 제공 합니다.  

## <a name="configure-azure-shared-disk"></a>Azure 공유 디스크 구성
이 섹션은 Azure 공유 디스크를 사용 하는 경우에만 적용할 수 있습니다. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>PowerShell을 사용 하 여 Azure 공유 디스크 만들기 및 연결
클러스터 노드 중 하나에서이 명령을 실행 합니다. 리소스 그룹, Azure 지역, SAPSID 등에 대 한 값을 조정 해야 합니다.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>PowerShell을 사용 하 여 공유 디스크 포맷
1. 디스크 번호를 가져옵니다. 클러스터 노드 중 하나에서 다음 PowerShell 명령을 실행 합니다.

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. 디스크를 포맷 합니다. 이 예제에서는 디스크 번호 2입니다. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. 이제 디스크가 클러스터 디스크로 표시 되는지 확인 합니다.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. 클러스터에 디스크를 등록 합니다.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS 클러스터 공유 디스크에 대 한 SIOS DataKeeper 클러스터 버전
이 섹션은 타사 소프트웨어 SIOS DataKeeper 클러스터 버전을 사용 하 여 클러스터 공유 디스크를 시뮬레이트하는 미러된 저장소를 만드는 경우에만 적용할 수 있습니다.  

이제 Azure에서 Windows Server 장애 조치 (failover) 클러스터링 구성이 진행 중입니다. SAP ASCS/SCS 인스턴스를 설치하려면 공유 디스크 리소스가 필요합니다. 옵션 중 하나는 SIOS DataKeeper를 사용 하는 것입니다. Cluster Edition은 공유 디스크 리소스를 만드는 데 사용할 수 있는 타사 솔루션입니다.  

SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치는 다음과 같은 작업을 포함합니다.
- 필요한 경우 Microsoft .NET 프레임 워크를 추가 합니다. [SIOS 설명서] ((최신 https://us.sios.com/products/datakeeper-cluster/) .net framework 요구 사항은)를 참조 하세요. 
-  SIOS DataKeeper 설치
- SIOS DataKeeper 구성

### <a name="install-sios-datakeeper"></a> SIOS DataKeeper 설치
클러스터의 각 노드에 SIOS DataKeeper Cluster Edition을 설치합니다. SIOS DataKeeper를 사용하여 가상 공유 스토리지를 만들려면 동기화된 미러를 만든 후 클러스터 공유 스토리지를 시뮬레이션합니다.

SIOS 소프트웨어를 설치하기 전에 DataKeeperSvc 도메인 사용자를 만듭니다.

> [!NOTE]
> DataKeeperSvc 도메인 사용자를 두 클러스터 노드의 로컬 관리자 그룹에 추가합니다.
>

1. 두 클러스터 노드에서 SIOS 소프트웨어를 설치합니다.

   ![SIOS 설치 관리자][sap-ha-guide-figure-3030]

   ![그림 31: SIOS DataKeeper 설치의 첫 번째 페이지][sap-ha-guide-figure-3031]

   _SIOS DataKeeper 설치의 첫 번째 페이지_

2. 대화 상자에서 **예**를 선택합니다.

   ![그림 32: 서비스를 사용할 수 없다고 알리는 DataKeeper][sap-ha-guide-figure-3032]

   _DataKeeper가 서비스를 사용할 수 없음을 알립니다._

3. 대화 상자에서 **도메인 또는 서버 계정**을 선택하는 것이 좋습니다.

   ![그림 33: SIOS DataKeeper에 대한 사용자 선택][sap-ha-guide-figure-3033]

   _SIOS DataKeeper에 대한 사용자 선택_

4. SIOS DataKeeper에 대해 만든 도메인 계정 사용자 이름 및 암호를 입력합니다.

   ![그림 34: SIOS DataKeeper 설치를 위한 도메인 사용자 이름 및 암호 입력][sap-ha-guide-figure-3034]

   _SIOS DataKeeper 설치를 위한 도메인 사용자 이름 및 암호 입력_

5. 그림 35와 같이 SIOS DataKeeper 인스턴스를 위한 라이선스 키를 설치합니다.

   ![그림 35: SIOS DataKeeper 라이선스 키 입력][sap-ha-guide-figure-3035]

   _SIOS DataKeeper 라이선스 키 입력_

6. 메시지가 표시되면 가상 머신을 다시 시작합니다.

### <a name="configure-sios-datakeeper"></a>SIOS DataKeeper 구성
두 노드에 SIOS DataKeeper를 설치한 후 구성을 시작합니다. 이러한 구성의 목표는 각 가상 머신에 연결된 추가 디스크 간에 동기식으로 데이터를 복제하는 것입니다.

1. DataKeeper 관리 및 구성 도구를 시작한 다음 **서버 연결**을 선택합니다.

   ![그림 36: SIOS DataKeeper 관리 및 구성 도구][sap-ha-guide-figure-3036]

   _SIOS DataKeeper 관리 및 구성 도구_

2. 관리 및 구성 도구에서 연결해야 하는 첫 번째 노드의 이름 또는 TCP/IP 주소를 삽입한 후 다음 단계에서 두 번째 노드에 대해 동일한 작업을 수행합니다.

   ![그림 37: 관리 및 구성 도구에서 연결해야 하는 첫 번째 노드의 이름 또는 TCP/IP 주소를 삽입한 후 다음 단계에서 두 번째 노드에 대해 동일한 작업 수행][sap-ha-guide-figure-3037]

   _관리 및 구성 도구에서 연결 해야 하는 첫 번째 노드의 이름 또는 TCP/IP 주소를 삽입 하 고 두 번째 단계에서 두 번째 노드를 삽입 합니다._

3. 두 노드 간에 복제 작업을 만듭니다.

   ![그림 38: 복제 작업 만들기][sap-ha-guide-figure-3038]

   _복제 작업 만들기_

   마법사에서 복제 작업을 만드는 과정을 안내합니다.

4. 복제 작업의 이름을 정의합니다.

   ![그림 39: 복제 작업 이름 정의][sap-ha-guide-figure-3039]

   _복제 작업 이름 정의_

   ![그림 40: 현재 원본 노드에 해당하는 노드의 기본 데이터 정의][sap-ha-guide-figure-3040]

   _현재 원본 노드인 노드에 대 한 기본 데이터를 정의 합니다._

5. 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨을 정의합니다.

   ![그림 41: 현재 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨 정의][sap-ha-guide-figure-3041]

   _현재 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨 정의_

6. 압축 알고리즘을 정의합니다. 예제에서는 복제 스트림을 압축하는 것이 좋습니다. 특히 재동기화 상황에서는 복제 스트림을 압축하면 재동기화 시간을 크게 단축할 수 있습니다. 압축에는 가상 머신의 CPU 및 RAM 리소스가 사용됩니다. 압축 속도가 증가하면 사용되는 CPU 리소스 양도 증가합니다. 나중에 이 설정을 조정할 수 있습니다.

7. 확인해야 하는 또 다른 설정은 복제가 비동기식 또는 동기식 중에서 어떤 방식으로 발생하는가 입니다. SAP ASCS/SCS 구성을 보호할 때 동기 복제를 사용해야 합니다.  

   ![그림 42: 복제 세부 정보 정의][sap-ha-guide-figure-3042]

   _복제 세부 정보 정의_

8. 복제 작업에 의해 복제되는 볼륨을 Windows Server 장애 조치(Failover) 클러스터 구성에 공유 디스크로 나타낼지 여부를 정의합니다. SAP ASCS/SCS 구성의 경우 Windows 클러스터가 복제된 볼륨을 클러스터 볼륨으로 사용할 수 있는 공유 디스크로 인식하도록 **예**를 선택합니다.

   ![그림 43: 예를 선택하여 복제된 볼륨을 클러스터 볼륨으로 설정][sap-ha-guide-figure-3043]

   _복제 된 볼륨을 클러스터 볼륨으로 설정 하려면 **예** 를 선택 합니다._

   볼륨을 만든 후 DataKeeper 관리 및 구성 도구에서 복제 작업 활성화되어 있음을 보여 줍니다.

   ![그림 44: SAP ASCS/SCS 공유 디스크에 대해 활성화된 DataKeeper 동기식 미러링][sap-ha-guide-figure-3044]

   _SAP ASCS/SCS 공유 디스크에 대해 활성화된 DataKeeper 동기식 미러링_

   이제 그림 45와 같이 장애 조치 클러스터 관리자에서 디스크를 DataKeeper 디스크로 표시합니다.

   ![그림 45: 장애 조치(Failover) 클러스터 관리자에서 표시하는 DataKeeper에서 복제한 디스크][sap-ha-guide-figure-3045]

   _장애 조치(Failover) 클러스터 관리자에서 표시하는 DataKeeper에서 복제한 디스크_


## <a name="next-steps"></a>다음 단계

* [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA 설치][sap-high-availability-installation-wsfc-shared-disk]

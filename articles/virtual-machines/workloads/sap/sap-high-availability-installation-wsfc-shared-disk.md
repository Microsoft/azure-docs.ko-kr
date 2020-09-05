---
title: Azure에서 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA 설치 | Microsoft Docs
description: Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA를 설치하는 방법을 알아봅니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c5b7debe0c94839e2ca7742817a49216328c571
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855316"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Azure에서 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA 설치

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

이 문서에서는 Azure에서 Windows Server 장애 조치(Failover)클러스터와 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링을 위한 고가용성 SAP 시스템을 설치하고 구성하는 방법을 설명합니다. [아키텍처 가이드: 클러스터 공유 디스크를 사용 하 여 Windows 장애 조치 (failover) 클러스터에서 SAP ASCS/SCS 인스턴스 클러스터링][sap-high-availability-guide-wsfc-shared-disk]은 클러스터 *공유 디스크*에 대 한 두 가지 대안이 있습니다.

- [Azure 공유 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)
- [Sios DataKeeper 클러스터 버전](https://us.sios.com/products/datakeeper-cluster/) 을 사용 하 여 미러된 저장소를 만들면 클러스터 된 공유 디스크를 시뮬레이션 합니다. 

## <a name="prerequisites"></a>사전 준비 사항

설치를 시작하기 전에 먼저 다음 문서를 검토하세요.

* [아키텍처 가이드: Windows 장애 조치(Failover) 클러스터에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링][sap-high-availability-guide-wsfc-shared-disk]

* [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 위한 SAP HA용 Azure 인프라 준비][sap-high-availability-infrastructure-wsfc-shared-disk]

DBMS 설정 방법은 사용 중인 DBMS 시스템에 따라 다르기 때문에 이 문서에서는 설명하지 않습니다. DBMS의 고가용성 문제는 다양한 DBMS 공급업체가 Azure에 대해 지원하는 기능으로 해결되었다고 가정합니다. 그 예로 SQL Server용 데이터베이스 미러링 또는 AlwaysOn, Oracle 데이터베이스용 Oracle Data Guard를 들 수 있습니다. DBMS에 대 한 고가용성 시나리오는이 문서에서 다루지 않습니다.

Azure에서 다양한 DBMS 서비스가 클러스터형 SAP ASCS/SCS 구성과 상호 작용하는 경우 특별히 고려해야 하는 사항은 없습니다.

> [!NOTE]
> SAP NetWeaver ABAP 시스템, Java 시스템 및 ABAP+Java 시스템의 설치 절차는 거의 동일합니다. 가장 중요한 차이점은 SAP ABAP 시스템에 ASCS 인스턴스가 하나 있다는 것입니다. SAP Java 시스템에는 하나의 SCS 인스턴스가 있습니다. SAP ABAP+Java 시스템에서는 동일한 Microsoft 장애 조치 클러스터 그룹에 하나의 ASCS 인스턴스와 하나의 SCS 인스턴스가 실행되고 있습니다. 각 SAP NetWeaver 설치 스택에 대한 설치 차이점은 명시적으로 언급됩니다. 나머지 단계는 동일 하다 고 가정할 수 있습니다.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>고가용성 ASCS/SCS 인스턴스를 사용 하 여 SAP 설치

> [!IMPORTANT]
> SIOS를 사용 하 여 공유 디스크를 표시 하는 경우 SIOS DataKeeper 미러된 볼륨에 페이지 파일을 저장 하지 마세요. Azure Virtual Machines의 임시 드라이브 D에 페이지 파일을 둘 수 있습니다. 이것이 기본 설정입니다. Windows 페이지 파일을 Azure Virtual Machines의 D 드라이브로 이동하지 않은 경우 이동합니다.  


고가용성 ASCS/SCS 인스턴스에 SAP 설치는 다음과 같은 작업을 포함합니다.

* 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기.
* 첫 번째 클러스터 노드에 SAP를 설치 합니다.
* ASCS/SCS 인스턴스의 SAP 프로필 수정하기.
* 프로브 포트 추가하기.
* Windows 방화벽 프로브 포트 열기.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

1. Windows DNS 관리자에서 ASCS/SCS 인스턴스의 가상 호스트 이름에 대한 DNS 항목을 만듭니다.

   > [!IMPORTANT]
   > ASCS/SCS 인스턴스의 가상 호스트 이름에 할당 하는 IP 주소는 Azure Load Balancer에 할당 한 IP 주소와 동일 해야 합니다.  
   
   
   ![그림 1: SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의][sap-ha-guide-figure-3046]

   _SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의_

2. 클러스터 된 인스턴스인 새 SAP 큐에 넣기 복제 서버 2를 사용 하는 경우 ERS2에 대 한 가상 호스트 이름도 DNS에 예약 해야 합니다. 

   > [!IMPORTANT]
   > ERS2 인스턴스의 가상 호스트 이름에 할당 하는 IP 주소는 Azure Load Balancer에 할당 한 두 번째 IP 주소 여야 합니다.    
   
   
   ![그림 1A: SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대 한 DNS 항목 정의][sap-ha-guide-figure-3046-ers2]

   _SAP ERS2 클러스터 가상 이름 및 TCP/IP 주소에 대 한 DNS 항목을 정의 합니다._


3. 가상 호스트 이름에 할당 된 IP 주소를 정의 하려면 **DNS 관리자**  >  **도메인**을 선택 합니다.

   ![그림 2: SAP ASCS/SCS 클러스터 구성을 위한 새 가상 이름 및 TCP/IP 주소][sap-ha-guide-figure-3047]

   _SAP ASCS/SCS 클러스터 구성에 대 한 새 가상 이름 및 TCP/IP 주소_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> SAP 첫 번째 클러스터 노드 설치

1. 클러스터 노드 A에서 첫 번째 클러스터 노드 옵션을 실행 합니다. 다음을 선택 합니다.

   * **ABAP 시스템**: **ASCS** 인스턴스 번호 **00**
   * **Java 시스템**: **SCS** 인스턴스 번호 **01**
   * **ABAP+Java 시스템**: **ASCS** 인스턴스 번호 **00** 및 **SCS** 인스턴스 번호 **01**

   
   > [!IMPORTANT]
   > Azure 내부 부하 분산 장치 부하 분산 규칙 (기본 SKU를 사용 하는 경우)의 구성과 선택한 SAP 인스턴스 번호가 일치 해야 한다는 점에 유의 하세요.

2. SAP에 설명 된 설치 절차를 따릅니다. 구성 옵션으로 "클러스터 공유 디스크"를 선택 하려면 설치 시작 옵션 "첫 번째 클러스터 노드"에서 확인 합니다.

> [!TIP]
> SAP 설치 설명서에는 첫 번째 ASCS/SCS 클러스터 노드를 설치하는 방법을 설명합니다.



### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS 인스턴스의 SAP 프로필 수정

복제 서버 1을 큐에 대기 하는 경우 아래 설명 된 대로 SAP 프로필 매개 변수를 추가 `enque/encni/set_so_keepalive` 합니다. 이 프로필 매개 변수는 연결이 너무 오랫동안 유휴 상태일 때 SAP 작업 프로세스와 큐에 넣기 서버 사이의 연결이 닫히지 않도록 합니다. ERS2에는 SAP 매개 변수가 필요 하지 않습니다. 

1. ERS1를 사용 하는 경우이 프로필 매개 변수를 SAP ASCS/SCS 인스턴스 프로필에 추가 합니다.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   ERS1 및 ERS2 둘 다에 대해 `keepalive` SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)에 설명 된 대로 OS 매개 변수를 설정 해야 합니다.   

2. SAP 프로필 매개 변수 변경 내용을 적용 하려면 SAP ASCS/SCS 인스턴스를 다시 시작 합니다.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> 프로브 포트 추가

내부 부하 분산 장치의 프로브 기능을 사용하여 전체 클러스터 구성이 Azure Load Balancer에서 작동하도록 합니다. 일반적으로 Azure 내부 부하 분산 장치는 참여하는 가상 머신 간에 동일하게 들어오는 작업 부하를 분산합니다.

그러나 하나의 인스턴스만 활성 상태가 되므로 일부 클러스터 구성에서는 작동하지 않습니다. 다른 인스턴스는 수동 상태이므로 워크로드를 받아들일 수 없습니다. 프로브 기능을 사용 하면 Azure 내부 부하 분산 장치가 활성 상태인 인스턴스를 감지 하 고 활성 인스턴스만 대상으로 할 때 도움이 됩니다.  

> [!IMPORTANT]
> 이 예제 구성에서 **ProbePort** 는 620**Nr**로 설정 됩니다. 숫자가 **00** 인 SAP ascs 인스턴스의 경우 620**00**입니다. SAP 인스턴스 번호 및 SAP SID와 일치 하도록 구성을 조정 해야 합니다.

프로브 포트를 추가 하려면 클러스터 Vm 중 하나에서이 PowerShell 모듈을 실행 합니다.

- SAP ASC/SCS 인스턴스의 경우 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- 클러스터 된 ERS2를 사용 하는 경우입니다. ERS1에 대 한 프로브 포트는 클러스터 되지 않으므로 구성할 필요가 없습니다.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 함수 코드는 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` 다음과 같습니다.
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
    
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }
    
                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
                    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }   
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows 방화벽 프로브 포트 열기

두 클러스터 노드에서 Windows 방화벽 프로브 포트를 엽니다. 다음 스크립트를 사용하여 Windows 방화벽 프로브 포트를 엽니다. 사용자 환경에 맞게 PowerShell 변수를 업데이트합니다.  
ERS2를 사용 하는 경우 ERS2 프로브 포트에 대 한 방화벽 포트도 열어야 합니다.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> 데이터베이스 인스턴스 설치

데이터베이스 인스턴스를 설치하려면 SAP 설치 설명서에 설명된 프로세스를 따릅니다.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 두 번째 클러스터 노드 설치

두 번째 클러스터 노드를 설치하려면 SAP 설치 가이드에 설명된 단계를 따릅니다.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP 기본 애플리케이션 서버 설치

PAS (기본 응용 프로그램 서버)를 호스트 하도록 지정한 가상 컴퓨터에 PAS (기본 응용 프로그램 서버) 인스턴스 \<SID\> -di-0을 설치 합니다. Azure와는 관련이 없습니다. SIOS를 사용 하는 경우에는 DataKeeper 관련 설정이 없습니다.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 추가 애플리케이션 서버 설치

SAP 애플리케이션 서버 인스턴스를 호스트하도록 지정한 모든 가상 머신에 SAP AAS(추가 애플리케이션 서버)를 설치합니다. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS 인스턴스 장애 조치 (failover) 테스트

앞서 설명한 장애 조치 (failover) 테스트에서는 SAP ASCS가 노드 A에서 활성화 되어 있다고 가정 합니다.  

1. SAP 시스템이 노드 A에서 노드 B로 성공적으로 장애 조치 (failover) 할 수 있는지 확인 합니다 \<SID\> . 클러스터 노드 a에서 클러스터 노드 b로 sap 클러스터 그룹의 장애 조치 (failover)를 시작 하려면 다음 옵션 중 하나를 선택 합니다.
    - 장애 조치(failover) 클러스터 관리자  
    - 장애 조치 클러스터 PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Windows 게스트 운영 체제 내에서 클러스터 노드 A를 다시 시작합니다. 그러면 \<SID\> 노드 A에서 노드 B로 SAP 클러스터 그룹의 자동 장애 조치 (failover)가 시작 됩니다.  
3. Azure Portal에서 클러스터 노드 A를 다시 시작합니다. 그러면 \<SID\> 노드 A에서 노드 B로 SAP 클러스터 그룹의 자동 장애 조치 (failover)가 시작 됩니다.  
4. Azure PowerShell을 사용하여 클러스터 노드 A를 다시 시작합니다. 그러면 \<SID\> 노드 A에서 노드 B로 SAP 클러스터 그룹의 자동 장애 조치 (failover)가 시작 됩니다.

5. 확인
   - 장애 조치 (failover) 후에는 SAP \<SID\> 클러스터 그룹이 클러스터 노드 B에서 실행 되 고 있는지 확인 합니다. 

      ![그림 8: 장애 조치 클러스터 관리자: 클러스터 노드 B에서 실행 중인 SAP \<SID\> 클러스터 그룹][sap-ha-guide-figure-5002]

      _장애 조치(Failover) 클러스터 관리자에서 SAP \<SID\> 클러스터 그룹이 클러스터 노드 B에서 실행 되 고 있습니다._

   - 장애 조치 (failover) 후 공유 디스크가 이제 클러스터 노드 B에 탑재 되었는지 확인 합니다. 
   - 장애 조치 (failover) 후 SIOS를 사용 하는 경우 SIOS DataKeeper가 클러스터 노드 B의 원본 볼륨 드라이브 S에서 클러스터 노드 A의 대상 볼륨 드라이브로 데이터를 복제 하 고 있는지 확인 합니다. 

      ![그림 9: SIOS DataKeeper에서 클러스터 노드 B로부터 클러스터 노드 A에 로컬 볼륨 복제][sap-ha-guide-figure-5003]

      _SIOS DataKeeper는 클러스터 노드 B에서 클러스터 노드 A로 로컬 볼륨을 복제 합니다._

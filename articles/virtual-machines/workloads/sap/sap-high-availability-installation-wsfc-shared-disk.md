---
title: Azure에서 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA 설치 | Microsoft Docs
description: Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA를 설치하는 방법을 알아봅니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1229b7f9e2a430a663a3e78bb457c03cf4a4a590
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714423"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Azure에서 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA 설치

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

이 문서에서는 Azure에서 Windows Server 장애 조치(Failover)클러스터와 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링을 위한 고가용성 SAP 시스템을 설치하고 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

설치를 시작하기 전에 먼저 다음 문서를 검토하세요.

* [아키텍처 가이드: 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링][sap-high-availability-guide-wsfc-shared-disk]

* [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 위한 SAP HA용 Azure 인프라 준비][sap-high-availability-infrastructure-wsfc-shared-disk]

DBMS 설정 방법은 사용 중인 DBMS 시스템에 따라 다르기 때문에 이 문서에서는 설명하지 않습니다. DBMS의 고가용성 문제는 다양한 DBMS 공급업체가 Azure에 대해 지원하는 기능으로 해결되었다고 가정합니다. 그 예로 SQL Server용 데이터베이스 미러링 또는 AlwaysOn, Oracle 데이터베이스용 Oracle Data Guard를 들 수 있습니다. 이 문서에서 제시되는 시나리오에서는 DBMS에 추가적인 보호를 적용하지 않습니다.

Azure에서 다양한 DBMS 서비스가 클러스터형 SAP ASCS/SCS 구성과 상호 작용하는 경우 특별히 고려해야 하는 사항은 없습니다.

> [!NOTE]
> SAP NetWeaver ABAP 시스템, Java 시스템 및 ABAP+Java 시스템의 설치 절차는 거의 동일합니다. 가장 중요한 차이점은 SAP ABAP 시스템에 ASCS 인스턴스가 하나 있다는 것입니다. SAP Java 시스템에는 하나의 SCS 인스턴스가 있습니다. SAP ABAP+Java 시스템에서는 동일한 Microsoft 장애 조치 클러스터 그룹에 하나의 ASCS 인스턴스와 하나의 SCS 인스턴스가 실행되고 있습니다. 각 SAP NetWeaver 설치 스택에 대한 설치 차이점은 명시적으로 언급됩니다. 다른 모든 부분은 동일하다고 가정할 수 있습니다.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>고가용성 ASCS/SCS 인스턴스를 갖는 SAP 설치

> [!IMPORTANT]
> SIOS DataKeeper 미러된 볼륨에 페이지 파일을 배치하지 마세요. DataKeeper는 미러된 볼륨을 지원하지 않습니다. Azure Virtual Machines의 임시 드라이브 D에 페이지 파일을 둘 수 있습니다. 이것이 기본 설정입니다. Windows 페이지 파일을 Azure Virtual Machines의 D 드라이브로 이동하지 않은 경우 이동합니다.
>
>

고가용성 ASCS/SCS 인스턴스에 SAP 설치는 다음과 같은 작업을 포함합니다.

* 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기.
* SAP 첫 번째 클러스터 노드 설치하기.
* ASCS/SCS 인스턴스의 SAP 프로필 수정하기.
* 프로브 포트 추가하기.
* Windows 방화벽 프로브 포트 열기.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

1. Windows DNS 관리자에서 ASCS/SCS 인스턴스의 가상 호스트 이름에 대한 DNS 항목을 만듭니다.

   > [!IMPORTANT]
   > ASCS/SCS 인스턴스의 가상 호스트 이름에 할당하는 IP 주소는 Azure Load Balancer에 할당한 IP 주소(\<SID\>-lb-ascs)와 동일해야 합니다.  
   >
   >

   가상 SAP ASCS/SCS 호스트 이름의 IP 주소(pr1-ascs-sap)는 Azure Load Balancer의 IP 주소(pr1-lb-ascs)와 같습니다.

   ![그림 1: SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의][sap-ha-guide-figure-3046]

   _**그림 1:** SAP ASCS/SCS 클러스터 가상 이름 및 TCP/IP 주소에 대한 DNS 항목 정의_

2. 가상 호스트 이름에 할당된 IP 주소를 정의하려면 **DNS 관리자** > **도메인**을 선택합니다.

   ![그림 2: SAP ASCS/SCS 클러스터 구성을 위한 새 가상 이름 및 TCP/IP 주소][sap-ha-guide-figure-3047]

   _**그림 2:** SAP ASCS/SCS 클러스터 구성을 위한 새 가상 이름 및 TCP/IP 주소_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> SAP 첫 번째 클러스터 노드 설치

1. 클러스터 노드 A(예: pr1-ascs-0*host)에서 첫 번째 클러스터 노드 옵션을 실행합니다.
2. Azure 내부 부하 분산 장치에 대한 기본 포트를 유지하려면 다음을 선택합니다.

   * **ABAP 시스템**: **ASCS** 인스턴스 번호 **00**
   * **Java 시스템**: **SCS** 인스턴스 번호 **01**
   * **ABAP+Java 시스템**: **ASCS** 인스턴스 번호 **00** 및 **SCS** 인스턴스 번호 **01**

   ABAP ASCS 인스턴스에 00이 아닌 인스턴스 번호를 Java SCS 인스턴스에 01이 아닌 인스턴스 번호를 사용하려면 먼저 Azure 내부 부하 분산 장치의 기본 부하 분산 규칙을 변경합니다. 자세한 내용은 [Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-ha-guide-8.9]을 참조하세요.

다음 몇 가지 작업은 표준 SAP 설치 설명서에서 설명되지 않습니다.

> [!NOTE]
> SAP 설치 설명서에는 첫 번째 ASCS/SCS 클러스터 노드를 설치하는 방법을 설명합니다.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS 인스턴스의 SAP 프로필 수정

먼저 새 프로필 매개 변수를 추가합니다. 이 프로필 매개 변수는 연결이 너무 오랫동안 유휴 상태일 때 SAP 작업 프로세스와 큐에 넣기 서버 사이의 연결이 닫히지 않도록 합니다. [SAP ASCS/SCS 인스턴스의 두 클러스터 노드에 대한 레지스트리 항목 추가][sap-ha-guide-8.11]에서 문제 시나리오를 언급했습니다. 이 섹션에서는 몇 가지 기본 TCP/IP 연결 매개 변수의 두 가지 변경 사항을 소개합니다. 두 번째 단계에서는 연결이 Azure 부하 부산 장치의 유휴 임계값에 도달하지 않게 `keep_alive` 신호를 보내도록 큐에 추가 서버를 설정해야 합니다.

ASCS/SCS 인스턴스의 SAP 프로필을 수정하려면:

1. SAP ASCS/SCS 인스턴스에 프로필에 이 프로필 매개 변수를 추가합니다.

   ```
   enque/encni/set_so_keepalive = true
   ```
   이 예제에서 경로는 다음과 같습니다.

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   예: SAP SCS 인스턴스 프로필 및 해당 경로

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. 변경 내용을 적용하려면 SAP ASCS/SCS 인스턴스를 다시 시작합니다.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> 프로브 포트 추가

내부 부하 분산 장치의 프로브 기능을 사용하여 전체 클러스터 구성이 Azure Load Balancer에서 작동하도록 합니다. 일반적으로 Azure 내부 부하 분산 장치는 참여하는 가상 머신 간에 동일하게 들어오는 작업 부하를 분산합니다.

 그러나 하나의 인스턴스만 활성 상태가 되므로 일부 클러스터 구성에서는 작동하지 않습니다. 다른 인스턴스는 수동 상태이므로 워크로드를 받아들일 수 없습니다. 검색 기능은 Azure 내부 부하 분산 장치가 활성 인스턴스에만 작업을 할당할 때 도움이 됩니다. 검색 기능을 사용하면 내부 부하 분산 장치는 활성 상태인 인스턴스를 감지한 후 해당 인스턴스만 워크로드 대상으로 지정할 수 있습니다.

프로브 포트를 추가하려면:

1. 다음 PowerShell 명령을 실행하여 현재 **ProbePort** 값을 확인합니다.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   이 명령은 클러스터 구성의 가상 머신 중 하나에서 실행합니다.

2. 프로브 포트를 정의합니다. 프로브 포트 기본값은 0입니다. 예제에서는 62000 프로브 포트를 사용합니다.

   ![그림 3: 기본적으로 0인 클러스터 구성 프로브 포트][sap-ha-guide-figure-3048]

   _**그림 3:** 기본 클러스터 구성 프로브 포트는 0_

   포트 번호는 SAP Azure Resource Manager 템플릿에서 정의됩니다. PowerShell에서 포트 번호를 할당할 수 있습니다.

   SAP \<SID\> IP 클러스터 리소스에 대한 새 ProbePort 값을 설정하려면 다음 PowerShell 스크립트를 실행하여 해당 환경의 PowerShell 변수를 업데이트합니다.

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   SAP \<SID\> 클러스터 역할을 온라인으로 전환한 후에 **ProbePort**가 새 값으로 설정되었는지 확인합니다.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   스크립트가 실행된 후 변경 내용을 활성화하도록 SAP 클러스터 그룹을 다시 시작할 것인지 묻는 메시지가 나타납니다.

   ![그림 4: 새 값을 설정한 후 클러스터 포트 검색][sap-ha-guide-figure-3049]

   _**그림 4:** 새 값을 설정한 후 클러스터 포트 검색_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows 방화벽 프로브 포트 열기

두 클러스터 노드에서 Windows 방화벽 프로브 포트를 엽니다. 다음 스크립트를 사용하여 Windows 방화벽 프로브 포트를 엽니다. 사용자 환경에 맞게 PowerShell 변수를 업데이트합니다.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort**가 **62000**으로 설정됩니다. 이제 ascsha-dbas 등의 다른 호스트에서 \\\ascsha-clsap\sapmnt 파일 공유에 액세스할 수 있습니다.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> 데이터베이스 인스턴스 설치

데이터베이스 인스턴스를 설치하려면 SAP 설치 설명서에 설명된 프로세스를 따릅니다.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 두 번째 클러스터 노드 설치

두 번째 클러스터 노드를 설치하려면 SAP 설치 가이드에 설명된 단계를 따릅니다.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS Windows 서비스 인스턴스의 시작 유형 변경

두 클러스터 노드에서 SAP ERS Windows 서비스의 시작 유형을 **자동(지연된 시작)** 으로 변경합니다.

![그림 5: SAP ERS 인스턴스의 서비스 형식을 지연된 자동으로 변경][sap-ha-guide-figure-3050]

_**그림 5:** SAP ERS 인스턴스의 서비스 형식을 지연된 자동으로 변경_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP 기본 애플리케이션 서버 설치

PAS(기본 애플리케이션 서버)를 호스트하도록 지정한 가상 머신에 PAS 인스턴스 \<SID\>-di-0를 설치합니다. Azure와는 관련이 없습니다. DataKeeper 관련 설정과는 관련이 없습니다.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 추가 애플리케이션 서버 설치

SAP 애플리케이션 서버 인스턴스를 호스트하도록 지정한 모든 가상 머신에 SAP AAS(추가 애플리케이션 서버)를 설치합니다. 예를 들어 \<SID\>-di-1에서 \<SID\>-di-&lt;n&gt;까지의 가상 머신에 설치할 수 있습니다.

> [!NOTE]
> 이는 고가용성 SAP NetWeaver 시스템의 설치를 완료합니다. 다음으로 장애 조치 테스트를 진행합니다.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 복제 테스트
장애 조치 클러스터 관리자 및 SIOS DataKeeper 관리 및 구성 도구를 사용하여 SAP ASCS/SCS 인스턴스 장애 조치 및 SIOS 디스크 복제를 쉽게 테스트하고 모니터링할 수 있습니다.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS 인스턴스가 클러스터 노드 A에서 실행되고 있습니다.

SAP PR1 클러스터 그룹이 클러스터 노드 A(예: pr1-ascs-0)에서 실행되고 있습니다. SAP PR1 클러스터 그룹에 속한 S 공유 디스크 드라이브를 클러스터 노드 A에 할당합니다. ASCS/SCS 인스턴스도 S 공유 디스크 드라이브를 사용합니다. 

![그림 6: 장애 조치(Failover) 클러스터 관리자: SAP \<SID\> 클러스터 그룹이 클러스터 노드 A에서 실행 되 고][sap-ha-guide-figure-5000]

_**그림 6:** 장애 조치(Failover) 클러스터 관리자: SAP \<SID\> 클러스터 그룹이 클러스터 노드 A에서 실행 되 고_

SIOS DataKeeper 관리 및 구성 도구에서 공유 디스크 데이터가 클러스터 노드 A의 S 원본 볼륨 드라이브에서 클러스터 노드 B의 S 대상 볼륨 드라이브로 동기식으로 복제되는 것을 확인할 수 있습니다(예: pr1-ascs-0 [10.0.0.40]에서 pr1-ascs-1 [10.0.0.41]로 복제됨).

![그림 7: SIOS DataKeeper에서 클러스터 노드 A로부터 클러스터 노드 B에 로컬 볼륨 복제][sap-ha-guide-figure-5001]

_**그림 7:** SIOS DataKeeper에서 클러스터 노드 A로부터 클러스터 노드 B에 로컬 볼륨 복제_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> 노드 A에서 노드 B로 장애 조치

1. 이러한 옵션 중 하나를 선택하여 클러스터 노드 A에서 클러스터 노드 B로 SAP \<SID\> 클러스터 그룹의 장애 조치를 시작합니다.
   - 장애 조치(failover) 클러스터 관리자  
   - 장애 조치 클러스터 PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Windows 게스트 운영 체제 내에서 클러스터 노드 A를 다시 시작합니다. 이렇게 하면 노드 A에서 노드 B로의 SAP \<SID\> 클러스터 그룹의 자동 장애 조치(Failover)가 시작됩니다.  
3. Azure Portal에서 클러스터 노드 A를 다시 시작합니다. 이렇게 하면 노드 A에서 노드 B로의 SAP \<SID\> 클러스터 그룹의 자동 장애 조치(Failover)가 시작됩니다.  
4. Azure PowerShell을 사용하여 클러스터 노드 A를 다시 시작합니다. 이렇게 하면 노드 A에서 노드 B로의 SAP \<SID\> 클러스터 그룹의 자동 장애 조치(Failover)가 시작됩니다.

   장애 조치 후 SAP \<SID\> 클러스터 그룹이 클러스터 노드 B(예: pr1-ascs-1에서 실행 중)에서 실행되고 있습니다.

   ![그림 8: 장애 조치 클러스터 관리자에서 SAP \<SID\> 클러스터 그룹이 클러스터 노드 B에서 실행 되 고][sap-ha-guide-figure-5002]

   _**그림 8**: 장애 조치 클러스터 관리자에서 SAP \<SID\> 클러스터 그룹이 클러스터 노드 B에서 실행 되 고_

   이제 공유 디스크가 클러스터 노드 B에 탑재됩니다. SIOS DataKeeper에서 데이터를 클러스터 노드 B의 S 소스 볼륨 드라이브에서 클러스터 노드 A의 S 대상 볼륨 드라이브로 복제합니다(예: pr1-ascs-1 [10.0.0.41]에서 pr1-ascs-0 [10.0.0.40]으로 복제).

   ![그림 9: SIOS DataKeeper에서 클러스터 노드 B로부터 클러스터 노드 A에 로컬 볼륨 복제][sap-ha-guide-figure-5003]

   _**그림 9:** SIOS DataKeeper에서 클러스터 노드 B로부터 클러스터 노드 A에 로컬 볼륨 복제_

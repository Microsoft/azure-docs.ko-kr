---
title: Azure에서 SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치 | Microsoft Docs
description: SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 04490abb8b7f3f4c39e4134a314429e190db5174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714084"
---
# <a name="install-sap-netweaver-high-availability-on-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances-on-azure"></a>Azure에서 SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[sap-powershell-scrips]:https://github.com/Azure-Samples/sap-powershell

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 다중 SID 고가용성 구성)


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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

이 문서에서는 SAP ASCS/SCS 인스턴스를 클러스터링하는 옵션으로 WSFC(Windows Server 장애 조치(failover) 클러스터) 및 스케일 아웃 파일 서버를 사용하여 Azure에 고가용성 SAP 시스템을 설치하고 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

설치를 시작하기 전에 다음 문서를 검토하세요.

* [아키텍처 가이드: 파일 공유를 사용 하 여 SAP ASCS/SCS 인스턴스에 Windows 장애 조치 클러스터에서 클러스터][sap-high-availability-guide-wsfc-file-share]

* [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 Azure 인프라의 SAP 고가용성 준비][sap-high-availability-infrastructure-wsfc-file-share]

SAP에서 다음 실행 파일 및 DLL 파일이 필요합니다.
* SAP SWPM(Software Provisioning Manager) 설치 도구 버전 SPS21 이상.
* 새 SAP 클러스터 리소스 DLL이 포함된 최신 NTCLUST.SAR 아카이브를 다운로드합니다. 새로운 SAP 클러스터 DLL은 Windows Server 장애 조치(failover) 클러스터에서 파일 공유를 사용하여 SAP ASCS/SCS 고가용성을 지원합니다.

  새 SAP 클러스터 리소스 DLL에 대 한 자세한 내용은이 블로그를 참조 하세요. [새 SAP 클러스터 리소스 DLL은 사용할 수 있습니다. ][sap-blog-new-sap-cluster-resource-dll].

설정은 사용하는 DBMS(데이터베이스 관리 시스템)에 따라 다르므로 DBMS 설정에 대해서는 설명하지 않습니다. 그러나 다양한 DBMS 공급업체가 Azure에 대해 지원하는 기능을 통해 DBMS의 고가용성 문제가 해결된다고 가정합니다. 그러한 기능에는 SQL Server용 데이터베이스 미러링 또는 AlwaysOn, Oracle 데이터베이스용 Oracle Data Guard가 있습니다. 이 문서에서 사용하는 시나리오에서는 DBMS에 대해 추가 보호를 적용하지 않았습니다.

Azure에서 다양한 DBMS 서비스가 이러한 종류의 클러스터형 SAP ASCS/SCS 구성과 상호 작용할 경우 특별한 고려 사항은 없습니다.

> [!NOTE]
> SAP NetWeaver ABAP 시스템, Java 시스템 및 ABAP+Java 시스템의 설치 절차는 거의 동일합니다. 가장 중요한 차이점은 SAP ABAP 시스템에 ASCS 인스턴스가 하나 있다는 것입니다. SAP Java 시스템에는 하나의 SCS 인스턴스가 있습니다. SAP ABAP+Java 시스템에서는 동일한 Microsoft 장애 조치 클러스터 그룹에 하나의 ASCS 인스턴스와 하나의 SCS 인스턴스가 실행되고 있습니다. 각 SAP NetWeaver 설치 스택에 대한 설치 차이점은 명시적으로 언급됩니다. 다른 모든 부분은 동일하다고 가정할 수 있습니다.  
>
>

## <a name="install-an-ascsscs-instance-on-an-ascsscs-cluster"></a>ASCS/SCS 클러스터에 ASCS/SCS 인스턴스를 설치합니다.

> [!IMPORTANT]
>
> 현재 파일 공유 구성을 사용하는 고가용성 설정은 SAP SWPM 설치 도구에서 지원되지 않습니다. 따라서 SAP 시스템을 설치하려면 몇 가지를 수동으로 적용해야 합니다(예: SAP ASCS/SCS 인스턴스를 설치 및 클러스터링, 별도의 SAP 전역 호스트 구성).  
>
> DBMS 인스턴스 및 SAP 애플리케이션 서버를 설치(및 클러스터링)하는 다른 설치 단계는 바뀐 부분이 없습니다.
>

### <a name="install-an-ascsscs-instance-on-your-local-drive"></a>로컬 드라이브에 ASCS/SCS 인스턴스를 설치합니다.

ASCS/SCS 클러스터의 *두* 노드에 SAP ASCS/SCS 인스턴스를 설치합니다. 로컬 드라이브에도 설치합니다. 예제에서 로컬 드라이브는 C:\\이지만 다른 로컬 드라이브를 선택할 수 있습니다.  

인스턴스를 설치하려면 SAP SWPM 설치 도구에서 다음으로 이동합니다.

**\<제품>** > **\<DBMS>** > **설치** > **응용 프로그램 서버 ABAP**(또는 **Java**) > **분산 시스템** > **ASCS/SCS 인스턴스**

> [!IMPORTANT]
> 현재 파일 공유 시나리오는 SAP SWPM 설치 도구에서 지원되지 않습니다. 다음 설치 경로는 *사용할 수 없습니다*.
>
> **\<제품>** > **\<DBMS>** > **설치** > **응용 프로그램 서버 ABAP**(또는 **Java**) > **고가용성 시스템** > …
>

### <a name="remove-sapmnt-and-create-an-saploc-file-share"></a>SAPMNT를 제거하고 SAPLOC 파일 공유 만들기

SWMP가 C:\\usr\\sap 폴더에 SAPMNT 로컬 공유를 만들었습니다.

*두* ASCS/SCS 클러스터 노드에서 SAPMNT 파일 공유를 제거합니다.

다음 PowerShell 스크립트를 실행합니다.

```powershell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

SAPLOC 공유가 없으면 *두* ASCS/SCS 클러스터 노드에 새로 만듭니다.

다음 PowerShell 스크립트를 실행합니다.

```powershell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-an-sap-global-host-on-the-sofs-cluster"></a>SOFS 클러스터에 SAP 전역 호스트 준비

SOFS 클러스터에 다음 볼륨 및 파일 공유를 만듭니다.

* SAP GLOBALHOST 파일 `C:\ClusterStorage\Volume1\usr\sap\<SID>\SYS\` 구조 SOFS 클러스터 공유 볼륨 (CSV)

* SAPMNT 파일 공유

* 다음 항목에 대한 모든 권한을 가진 SAPMNT 파일 공유 및 폴더의 보안을 설정합니다.
    * \<DOMAIN>\SAP_\<SID>_GlobalAdmin 사용자 그룹
    * SAP ASCS/SCS 클러스터 노드 컴퓨터 개체 \<DOMAIN>\ClusterNode1$ 및 \<DOMAIN>\ClusterNode2$

미러 복원 기능을 가진 CSV 볼륨을 만들려면 SOFS 클러스터 노드 중 하나에서 다음 PowerShell cmdlet을 실행합니다.


```powershell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
SAPMNT를 만들고 폴더 및 공유 보안을 설정하려면 SOFS 클러스터 노드 중 하나에서 다음 PowerShell 스크립트를 실행합니다.

```powershell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create a SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add a file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add  a security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add a security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascsscs-instances-and-sap-services"></a>ASCS/SCS 인스턴스 및 SAP 서비스 중지

다음 단계를 실행합니다.
1. 두 ASCS/SCS 클러스터 노드에서 SAP ASCS/SCS 인스턴스를 중지합니다.
2. 두 클러스터 노드에서 SAP ASCS/SCS Windows 서비스 **SAP\<SID>_\<InstanceNumber>** 를 중지합니다.

## <a name="move-the-sys-folder-to-the-sofs-cluster"></a>\SYS\... 폴더를 SOFS 클러스터로 이동

다음 단계를 실행합니다.
1. SYS 폴더에 복사 (예를 들어 `C:\usr\sap\<SID>\SYS`) SOFS 클러스터로 클러스터 노드 ASCS/SCS 중 하나에서 (예를 들어 `C:\ClusterStorage\Volume1\usr\sap\<SID>\SYS`).
2. 삭제 된 `C:\usr\sap\<SID>\SYS` 두 ASCS/SCS 클러스터 노드에서 폴더입니다.

## <a name="update-the-cluster-security-setting-on-the-sap-ascsscs-cluster"></a>SAP ASCS/SCS 클러스터의 클러스터 보안 설정 업데이트

SAP ASCS/SCS 클러스터 노드 중 하나에서 다음 PowerShell 스크립트를 실행합니다.

```powershell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to the cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for the <DOMAIN>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기

[클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]에 설명된 대로 SAP ASCS/SCS 클러스터 네트워크 이름(예: **pr1-ascs [10.0.6.7]**)을 만듭니다.

## <a name="update-the-default-and-sap-ascsscs-instance-profile"></a>기본값 및 SAP ASCS/SCS 인스턴스 프로필 업데이트

새 SAP ASCS/SCS 가상 호스트 이름을 사용 하 고 SAP 전역 호스트 이름을 업데이트 해야 합니다 기본값 및 SAP ASCS/SCS 인스턴스 프로필 \<SID >_ASCS/SCS\<Nr >_\<호스트 >.


| 이전 값 |  |
| --- | --- |
| SAP ASCS/SCS 호스트 이름 = SAP 전역 호스트 | ascs-1 |
| SAP ASCS/SCS 인스턴스 프로필 이름 | PR1_ASCS00_ascs-1 |

| 새 값 |  |
| --- | --- |
| SAP ASCS/SCS 호스트 이름 | **pr1-ascs** |
| SAP 전역 호스트 | **sapglobal** |
| SAP ASCS/SCS 인스턴스 프로필 이름 | PR1\_ASCS00\_**pr1-ascs** |

### <a name="update-sap-default-profile"></a>SAP 기본 프로필 업데이트


| 매개 변수 이름 | 매개 변수 값 |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **pr1-ascs** |
| enque/serverhost | **pr1-ascs** |

### <a name="update-the-sap-ascsscs-instance-profile"></a>SAP ASCS/SCS 인스턴스 프로필 업데이트

| 매개 변수 이름 | 매개 변수 값 |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | **Start**_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | **pr1-ascs** |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | **Start**_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>**Update-SAPASCSSCSProfile** PowerShell cmdlet을 사용하여 프로필 업데이트를 자동화할 수 있습니다.
>
>PowerShell cmdlet은 SAP ABAP ASCS 및 SAP Java SCS 인스턴스를 모두 지원합니다.
>

[**SAPScripts.psm1**][sap-powershell-scrips]을 로컬 드라이브 C:\tmp에 복사하고 다음 PowerShell cmdlet을 실행합니다.

```powershell
Import-Module C:\tmp\SAPScripts.psm1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![그림 1: SAPScripts.psm1 출력][sap-ha-guide-figure-8012]

_**그림 1**: SAPScripts.psm1 출력_

## <a name="update-the-sidadm-user-environment-variable"></a>\<sid>adm 사용자 환경 변수 업데이트

1. *두* ASCS/SCS 클러스터 노드에서 \<sid>adm 사용자 환경인 새 GLOBALHOST UNC 경로를 업데이트합니다.
2. \<sid>adm 사용자로 로그온한 다음 Regedit.exe 도구를 시작합니다.
3. **HKEY_CURRENT_USER** > **Environment**로 이동하여 변수를 새 값으로 업데이트합니다.

| 변수 | 값 |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **pr1-ascs** |


## <a name="install-a-new-saprcdll-file"></a>새 saprc.dll 파일 설치

1. 파일 공유 시나리오를 지원하는 새 버전의 SAP 클러스터 리소스를 설치합니다.

2. SAP Service Marketplace에서 최신 NTCLUST.SAR 패키지를 다운로드합니다.

3. ASCS/SCS 클러스터 노드 중 하나에서 NTCLUS.SAR의 압축을 풀고 명령 프롬프트에서 다음 명령을 실행하여 새 saprc.dll을 설치합니다.

```
.\NTCLUST\insaprct.exe -yes -install
```

두 ASCS/SCS 클러스터 노드에 새 saprc.dll이 설치됩니다.

자세한 내용은 [SAP Note 1596496 - 클러스터 리소스 모니터를 위한 SAP 리소스 유형 DLL 업데이트 방법][1596496]을 참조하세요.

## <a name="create-a-sap-sid-cluster-group-network-name-and-ip"></a>SAP 만들기 \<SID > 클러스터 그룹, 네트워크 이름 및 IP

SAP \<SID> 클러스터 그룹, ASCS/SCS 네트워크 이름 및 해당 IP 주소를 만들려면 다음 PowerShell cmdlet을 실행합니다.

```powershell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create an SAP ASCS instance virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create an SAP ASCS virtual IP address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set a static IP address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create a corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set a network DNS name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start an SAP <SID> cluster group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-the-sap-start-service-on-both-nodes"></a>두 노드에 SAP 시작 서비스 등록

새 프로필 및 프로필 경로를 가리키도록 SAP ASCS/SCS 서비스를 다시 등록합니다.

*두* ASCS/SCS 클러스터 노드에서 이 재등록을 실행해야 합니다.

관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다.

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![그림 2: SAP 서비스 다시 설치][sap-ha-guide-figure-8013]

_**그림 2**: SAP 서비스 다시 설치_

매개 변수가 올바른지 확인하고 **수동**을 **시작 유형**으로 선택했는지 확인합니다.

## <a name="stop-the-ascsscs-service"></a>ASCS/SCS 서비스 중지

두 ASCS/SCS 클러스터 노드에서 SAP ASCS/SCS 서비스 SAP\<SID>_\<InstanceNumber>를 중지합니다.

## <a name="create-a-new-sap-service-and-sap-instance-resources"></a>새 SAP 서비스 및 SAP 인스턴스 리소스 만들기

SAP SAP\<SID> 클러스터 그룹의 리소스 생성을 완료하려면 다음 리소스를 만들어야 합니다.

* SAP \<SID> \<InstanceNumber> 서비스
* SAP \<SID> \<InstanceNumber> 인스턴스

다음 PowerShell cmdlet를 실행합니다.

```powershell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>프로브 포트 추가

PowerShell을 사용하여 SAP 클러스터 리소스인 SAP-SID-IP 프로브 포트를 구성합니다. [이 문서][sap-high-availability-installation-wsfc-shared-disk-add-probe-port]에 설명된 대로 SAP ASCS/SCS 클러스터 노드 중 하나에서 이 구성을 실행합니다.

## <a name="install-an-ers-instance-on-both-cluster-nodes"></a>두 클러스터 노드에 ERS 인스턴스 설치

ASCS/SCS 클러스터의 *두* 노드에 ERS(Enqueue Replication Server) 인스턴스를 설치합니다. SWPM 메뉴에서 이 설치 경로를 따릅니다.

**\<제품>** > **\<DBMS>** > **설치** > **추가 SAP 시스템 인스턴스** > **Enqueue Replication Server 인스턴스**

## <a name="install-a-dbms-instance-and-sap-application-servers"></a>DBMS 인스턴스 및 SAP 애플리케이션 서버 설치

다음 항목을 설치하여 SAP 시스템 설치를 완료합니다.
* DBMS 인스턴스.
* 기본 SAP 애플리케이션 서버.
* 추가 SAP 애플리케이션 서버.

## <a name="next-steps"></a>다음 단계

* [공유 디스크 없이 장애 조치(Failover) 클러스터에 ASCS/SCS 인스턴스 설치 - HA 파일 공유에 대한 공식 SAP 지침][sap-official-ha-file-share-document]

* [Windows Server 2016의 저장소 공간 다이렉트][s2d-in-win-2016]

* [애플리케이션 데이터에 대한 스케일 아웃 파일 서버 개요][sofs-overview]

* [Windows Server 2016 저장소의 새로운 기능][new-in-win-2016-storage]

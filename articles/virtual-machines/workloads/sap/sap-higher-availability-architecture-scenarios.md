---
title: Azure 인프라 VM 다시 시작을 활용하여 SAP 시스템의 "고가용성" 확보 | Microsoft Docs
description: Azure 인프라 VM 다시 시작을 활용하여 SAP 애플리케이션의 고가용성 확보
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f0b2f8f0-e798-4176-8217-017afe147917
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cda0b1c0774ed33bf550e0edf329cc22a2807be3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60825666"
---
# <a name="utilize-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-an-sap-system"></a>Azure 인프라 VM 다시 시작을 활용하여 SAP 시스템의 고가용성 확보

[1909114]:https://launchpad.support.sap.com/#/notes/1909114
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
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md
[sap-high-availability-architecture-scenarios-sap-app-ha]:sap-high-availability-architecture-scenarios.md#baed0eb3-c662-4405-b114-24c10a62954e

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

> 이 섹션은 다음에 적용됩니다.
>
> ![Windows][Logo_Windows] Windows 및 ![Linux][Logo_Linux] Linux
>

Linux에서 WSFC(Windows Server 장애 조치(failover) 클러스터링) 또는 Pacemaker와 같은 기능을 사용하지 않기로 결정한 경우(현재 SLES[SUSE Linux Enterprise Server] 12 이상에서만 지원) Azure VM 다시 시작이 사용됩니다. 이 기능은 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 계획되었거나 계획되지 않은 가동 중지 시간에서 SAP 시스템을 보호합니다.

> [!NOTE]
> Azure VM 다시 시작 기능은 애플리케이션이 *아닌* VM을 일차적으로 보호합니다. VM 다시 시작 기능이 SAP 애플리케이션에 대한 고가용성을 제공하지 않지만 특정 수준의 인프라 가용성을 제공합니다. 또한 SAP 시스템의 "고가용성"을 직접 제공합니다. 계획되거나 계획되지 않은 호스트 중단 이후에 VM을 다시 시작하는 데 걸리는 시간 동안 SLA가 제공되지 않습니다. 따라서 고가용성인 이 메서드는 SAP 시스템의 중요 구성 요소에 적합하지 않습니다. 중요 구성 요소의 예로 ASCS/SCS 인스턴스 또는 DBMS(데이터베이스 관리 시스템)을 들 수 있습니다.
>
>

고가용성을 위한 또 다른 중요한 인프라 요소는 저장소입니다. 예를 들어 Azure Storage SLA는 99.9% 사용 가능합니다. 모든 VM 및 해당 디스크를 단일 Azure Storage 계정에 배포하는 경우 잠재적으로 Azure Storage를 사용할 수 없게 되어 해당 스토리지 계정에 배치된 모든 VM 및 해당 VM 내에서 실행되는 모든 SAP 구성 요소를 사용할 수 없게 됩니다.  

단일 Azure Storage 계정에 모든 VM을 배치하는 대신 각 VM에 대한 전용 스토리지 계정을 사용할 수 있습니다. 여러 독립 Azure Storage 계정을 사용하여 전반적인 VM 및 SAP 애플리케이션 가용성을 증가시킵니다.

Azure Managed Disks는 연결된 가상 머신의 장애 도메인에 자동으로 배치됩니다. 가용성 집합에 가상 머신 두 대를 배치하고 관리 디스크를 사용하는 경우 플랫폼에서 각기 다른 장애 도메인으로 관리 디스크를 분산시키는 작업도 수행합니다. Premium Storage 계정을 사용하려는 경우에는 관리 디스크를 사용하는 것이 좋습니다.

Azure 인프라 고가용성 및 저장소 계정을 사용하는 SAP NetWeaver 시스템의 샘플 아키텍처는 다음과 같을 수 있습니다.

![Azure 인프라 고가용성을 활용하여 SAP 애플리케이션 "고가용성" 확보][planning-guide-figure-2900]

Azure 인프라 고가용성 및 관리 디스크를 사용하는 SAP NetWeaver 시스템의 샘플 아키텍처는 다음과 같을 수 있습니다.

![Azure 인프라 고가용성을 활용하여 SAP 애플리케이션 "고가용성" 확보][planning-guide-figure-2901]

중요한 SAP 구성 요소에서 현재까지 다음을 수행했습니다.

* SAP 애플리케이션 서버의 고가용성

    SAP 애플리케이션 서버 인스턴스는 중복 구성 요소입니다. 각 SAP 애플리케이션 서버 인스턴스는 다른 Azure 장애 및 업그레이드 도메인에서 실행되는 고유한 VM에 배포됩니다. 자세한 내용은 [장애 도메인][planning-guide-3.2.1] 및 [업그레이드 도메인][planning-guide-3.2.2] 섹션을 참조하세요. 

    Azure 가용성 집합을 사용하여 이 구성을 확인할 수 있습니다. 자세한 내용은 [Azure 가용성 집합][planning-guide-3.2.3] 섹션을 참조하세요. 

    Azure 장애 및 업그레이드 도메인이 계획되거나 계획되지 않게 잠재적으로 사용할 수 없게 되면 각 SAP 애플리케이션 서버 인스턴스를 포함한 제한된 수의 VM도 사용할 수 없게 됩니다.

    각 SAP 애플리케이션 서버 인스턴스는 고유한 Azure Storage 계정에 배치됩니다. 하나의 Azure Storage 계정을 잠재적으로 사용할 수 없게 되면 해당 SAP 애플리케이션 서버 인스턴스를 포함한 하나의 VM도 사용할 수 없게 됩니다. 그러나 단일 Azure 구독 내에서 Azure Storage 계정 수는 제한됩니다. VM을 다시 부팅한 후 ASCS/SCS 인스턴스가 자동으로 시작되도록 하려면 [SAP 인스턴스에 대해 Autostart 사용][planning-guide-11.5] 섹션에서 설명한 대로 ASCS/SCS 인스턴스 시작 프로필에 Autostart 매개 변수를 설정합니다.
  
    자세한 내용은 [SAP 애플리케이션 서버의 고가용성][planning-guide-11.4.1]을 참조하세요.

    관리 디스크를 사용하더라도 해당 디스크는 Azure Storage 계정에도 저장되므로 스토리지가 중단되면 사용이 불가능해질 수 있습니다.

* SAP ASCS/SCS 인스턴스의 *고가용성*

    이 시나리오에서는 Azure VM 다시 시작 기능을 활용하여 SAP ASCS/SCS 인스턴스가 설치된 VM을 보호합니다. Azure 서버가 계획되거나 계획되지 않게 가동 중지되는 경우 VM은 사용 가능한 다른 서버에서 다시 시작됩니다. 앞서 언급한 것처럼 Azure VM 다시 시작 기능은 애플리케이션(이 경우 ASCS/SCS 인스턴스)이 *아닌* VM을 기본적으로 보호합니다. VM 다시 시작 기능을 통해 SAP ASCS/SCS 인스턴스의 "고가용성"을 간접적으로 달성하게 됩니다. 

    VM을 다시 부팅한 후 ASCS/SCS 인스턴스가 자동으로 시작되도록 하려면 [SAP 인스턴스에 대해 Autostart 사용][planning-guide-11.5] 섹션에서 설명한 대로 ASCS/SCS 인스턴스 시작 프로필에 Autostart 매개 변수를 설정합니다. 이 설정으로 인해 단일 VM에서 SPOF(단일 실패 지점)로 실행되는 ASCS/SCS 인스턴스는 전체 SAP 지형의 가용성을 결정합니다.

* DBMS 서버의 *고가용성*

    앞서 SAP ASCS/SCS 인스턴스 사용 사례와 마찬가지로 Azure VM 다시 시작 기능을 활용하여 DBMS 소프트웨어가 설치된 VM을 보호하고, VM 다시 시작 기능을 통해 DBMS 소프트웨어의 "높은 가용성"을 획득합니다.
  
    단일 VM에서 실행되는 DBMS는 SPOF이기도 하며 전체 SAP 지형의 가용성을 결정하는 중요한 요소가 됩니다.

## <a name="using-autostart-for-sap-instances"></a>SAP 인스턴스에 Autostart 사용
SAP는 VM 내에서 OS가 시작된 이후에 즉시 SAP 인스턴스를 시작할 수 있는 설정을 제공합니다. 지침은 SAP 기술 자료 문서 [1909114]에 설명되어 있습니다. 그러나 SAP는 더 이상 설정을 사용하도록 권장하지 않습니다. 둘 이상인 VM의 영향을 받는 경우 또는 VM당 여러 인스턴스가 실행 중인 경우 인스턴스 다시 시작의 순서를 제어하도록 허용하지 않기 때문입니다. 

VM 하나에 단일 SAP 애플리케이션 서버 인스턴스가 있는 일반적인 Azure 시나리오와 단일 VM이 다시 시작되는 경우를 가정하면, Autostart는 중요하지 않습니다. 하지만 SAP ABAP(고급 비즈니스 애플리케이션 프로그래밍) 또는 Java 인스턴스의 시작 프로필에 다음 매개 변수를 추가하여 활성화할 수 있습니다.

      Autostart = 1


  > [!NOTE]
  > Autostart 매개 변수에는 단점도 있습니다. 구체적으로 말하면 이 매개 변수는 인스턴스의 관련 Windows 또는 Linux 서비스가 시작될 때 SAP ABAP 또는 Java 인스턴스가 시작되도록 트리거합니다. 해당 시퀀스는 운영 체제가 부팅된 경우에 발생합니다. 그러나 SAP 서비스를 다시 시작하는 것도 SUM(소프트웨어 업데이트 관리자)이나 기타 업데이트 또는 업그레이드와 같은 SAP 소프트웨어 수명 주기 관리 기능에서 공통으로 발생합니다. 이러한 기능에서 인스턴스가 자동으로 다시 시작되지는 않습니다. 따라서 이러한 작업을 실행하기 전에 Autostart 매개 변수를 사용하지 않도록 설정해야 합니다. Autostart 매개 변수를 ASCS/SCS/CI와 같이 클러스터링되는 SAP 인스턴스에는 사용하지 말아야 합니다.
  >
  >

  SAP 인스턴스의 Autostart에 대한 자세한 내용은 다음 문서를 참조하세요.

  * [Unix 서버 시작/중지에 따른 SAP 시작 또는 중지](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
  * [SAP NetWeaver 관리 에이전트 시작 및 중지](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
  * [HANA 데이터베이스의 자동 시작을 사용하도록 설정하는 방법](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

## <a name="next-steps"></a>다음 단계

전체 SAP NetWeaver 애플리케이션 인식 고가용성에 대한 정보는 [Azure IaaS의 SAP 애플리케이션 고가용성][sap-high-availability-architecture-scenarios-sap-app-ha]을 참조하세요.

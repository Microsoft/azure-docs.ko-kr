---
title: SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오 | Microsoft Docs
description: Azure Virtual Machines의 SAP NetWeaver에 대한 고가용성 아키텍처 및 시나리오
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 37f5040585681a53743fb3426b7f7ffac36de51c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60936276"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver에 대한 고가용성 아키텍처 및 시나리오

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

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

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

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


## <a name="terminology-definitions"></a>용어 정의

**고가용성**: *동일한* 데이터 센터 내의 중복성, 내결함성 또는 장애 조치(failover) 보호 구성 요소를 통해 IT 서비스의 비즈니스 연속성을 제공함으로써 IT 작업 중단을 최소화하는 일련의 기술을 나타냅니다. Microsoft의 경우에는 데이터 센터가 하나의 Azure 지역 내에 상주합니다.

**재해 복구**: 수백 마일 떨어져 있을 수 있는 *다양한* 데이터 센터를 막론하고 IT 서비스 중단 및 해당 복구의 최소화를 의미하기도 합니다. Microsoft의 경우에는 데이터 센터가 동일한 지리적 지역 내의 다양한 Azure 지역이나 고객이 설정한 위치에 상주할 수 있습니다.


## <a name="overview-of-high-availability"></a>고가용성 개요
Azure의 SAP 고가용성은 다음 세 가지 유형으로 구분할 수 있습니다.

* **Azure 인프라 고가용성**: 

    예를 들어 고가용성에는 계산(VM), 네트워크, 스토리지와 SAP 애플리케이션 가용성 향상에 주는 이점이 포함될 수 있습니다.

* **Azure 인프라 VM 다시 시작을 활용하여 SAP 애플리케이션의 *고가용성* 확보**: 

    Linux에서 WSFC(Windows Server 장애 조치(Failover) 클러스터링) 또는 Linux의 Pacemaker 같은 기능을 사용하지 않기로 결정하면 Azure VM 다시 시작이 사용됩니다. 이 기능은 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 계획되었거나 계획되지 않은 가동 중지 시간에서 SAP 시스템을 보호합니다.

* **SAP 애플리케이션 고가용성**: 

    완전한 SAP 시스템 고가용성을 달성하려면 모든 중요 SAP 시스템 구성 요소를 보호해야 합니다. 예를 들면 다음과 같습니다.
    * 이중화 SAP 애플리케이션 서버
    * 고유한 구성 요소 SAP ASCS/SCS 인스턴스 또는 DBMS(데이터베이스 관리 시스템)과 같은 SPOF(단일 실패 지점) 구성 요소를 예로 들 수 있습니다.

Azure의 SAP 고가용성은 온-프레미스 물리적 또는 가상 환경의 SAP 고가용성과 다릅니다. 다음 [Microsoft Windows에서 VMware 및 Hyper-V를 사용하는 가상 환경의 SAP NetWeaver 고가용성 및 비즈니스 연속성][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] 문서에서는 Windows에서 가상화된 환경의 표준 SAP 고가용성 구성에 대해 설명합니다.

Windows의 경우와 달리 Linux용 sapinst 통합 SAP 고가용성 구성은 없습니다. Linux용 SAP 고가용성 온-프레미스에 대한 자세한 내용은 [고가용성 파트너 정보][sap-ha-partner-information]를 참조하세요.

## <a name="azure-infrastructure-high-availability"></a>Azure 인프라 고가용성

### <a name="sla-for-single-instance-virtual-machines"></a>단일 인스턴스 가상 머신에 대한 SLA

현재 Premium Storage를 사용하는 단일 VM SLA는 99.9%입니다. 다양한 [Azure 서비스 수준 계약][azure-sla]의 제품을 빌드해 보면 단일 VM의 가용성이 어떻게 나타나는지 이해하는 데 도움이 됩니다.

이 계산은 한 달이 30일 또는 43200분이라는 기준에서 출발합니다. 예를 들어 가동 중지 시간 0.05%는 21.6분에 해당합니다. 일반적으로 다양한 서비스의 가용성은 다음 방식으로 계산됩니다.

(가용성 서비스 #1/100) * (가용성 서비스 #2/100) * (가용성 서비스 #3/100) \*…

예를 들면 다음과 같습니다.

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 또는 전체 가용성 99.75%

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>동일한 가용성 집합의 여러 가상 머신 인스턴스
동일한 *가용성 집합*에 배포된 두 개 이상의 인스턴스가 있는 모든 가상 머신의 경우에는 최소 99.95%의 시간 동안 가상 머신 연결을 사용할 수 있도록 보장합니다.

두 대 이상의 VM이 동일한 가용성 집합에 포함된 경우 해당 가용성 집합의 각 가상 머신은 기본 Azure 플랫폼에 의해 *업데이트 도메인* 및 *장애 도메인*에 할당됩니다.

* **업데이트 도메인**은 Azure 인프라의 계획된 유지 관리 동안 여러 VM이 동시에 다시 부팅되지 않고 한 번에 하나의 VM만 다시 부팅되도록 보장합니다.

* **장애 도메인**은 공통 전원과 네트워크 스위치를 공유하지 않는 하드웨어 구성 요소에 VM이 배포되도록 보장합니다. 서버, 네트워크 스위치 또는 전원이 갑작스럽게 가동 중지되면 VM만 영향을 받습니다.

자세한 내용은 [Azure에서 Windows 가상 머신의 가용성 관리][azure-virtual-machines-manage-availability]를 참조하세요.

가용성 집합은 다음 항목의 고가용성을 얻기 위해 사용됩니다.

* 이중화 SAP 애플리케이션 서버  
* SAP ASCS/SCS 인스턴스 또는 DBMS 같은 SPOF를 보호하는 두 개 이상의 노드(예: VM)로 구성된 클러스터


### <a name="azure-availability-zones"></a>Azure 가용성 영역
Azure는 다른 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/) 전체에서 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)의 개념을 롤아웃하는 중입니다. 가용성 영역이 제공되는 Azure 지역에서 Azure 지역에 전원, 냉각 및 네트워크 공급이 독립적인 여러 데이터 센터가 있습니다. 단일 Azure 지역 내 다른 영역을 제공하는 이유는 제공되는 2-3개 가용성 영역에서 애플리케이션을 배포할 수 있도록 하기 위함입니다. 전원 및/또는 네트워크의 문제가 하나의 가용성 영역 인프라에만 영향을 준다고 가정하면, Azure 지역 내 애플리케이션 배포는 계속 완벽하게 작동합니다. 하나의 영역에서 일부 VM이 다소 손실될 수 있으므로 결과적으로 일부 용량이 감소합니다. 하지만 다른 두 영역의 VM은 여전히 작동하고 실행됩니다. 영역을 제공하는 Azure 지역은 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)에 나열됩니다.

가용성 영역을 사용하여 고려할 일부 사항이 있습니다. 고려 사항 목록은 다음과 같습니다.

- 가용성 영역 내에서 Azure 가용성 집합을 배포할 수 없습니다. VM에 대한 배포 프레임으로 가용성 영역 또는 가용성 집합을 선택해야 합니다.
- [기본 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)를 사용하여 Windows 장애 조치(failover) 클러스터 서비스 또는 Linux Pacemaker를 기반으로 장애 조치(failover) 클러스터솔루션을 만들 수 없습니다. 대신 [Azure 표준 Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)를 사용해야 합니다.
- Azure 가용성 영역은 한 지역 내의 다른 영역 간 특정 거리의 어떤 보증도 제공하지 않습니다.
- 서로 다른 Azure 지역 내에서 다양한 Azure 가용성 영역 간의 네트워크 대기 시간은 Azure 지역에서 지역으로 달라질 수 있습니다. 하나의 영역에서 활성 DBMS VM으로의 네트워크 대기 시간은 비즈니스 프로세스 영향에서 여전히 허용 가능하므로 고객으로서 다른 영역에서 배포된 SAP 애플리케이션 계층을 합리적으로 실행할 수 있는 경우가 있습니다. 반면 하나의 영역에서 활성 DBMS VM과 다른 영역에서 VM의 SAP 애플리케이션 인스턴스 간의 대기 시간은 너무 방해가 되며 SAP 비즈니스 프로세스에 대해 허용되지 않을 수 있는 고객 시나리오가 있습니다. 결과적으로 배포 아키텍처는 대기 시간이 너무 높은 경우 애플리케이션에 대한 활성/활성 아키텍처 또는 능동/수동 아키텍처와 달라야 합니다.
- [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/) 사용은 Azure 가용성 영역으로 배포에 필수 항목입니다. 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>가상 머신의 계획된 유지 관리 및 계획되지 않은 유지 관리

다음 2가지 유형의 Azure 플랫폼 이벤트가 가상 머신의 가용성에 영향을 줄 수 있습니다.

* **계획된 유지 관리** 이벤트는 Microsoft에서 기본 Azure 플랫폼에 정기적으로 수행하는 업데이트입니다. 업데이트는 가상 머신이 실행되는 플랫폼 인프라의 전반적인 안정성, 성능 및 보안을 개선합니다.

* **계획되지 않은 유지 관리** 이벤트는 가상 머신의 기반이 되는 하드웨어 또는 물리적 인프라에 어떠한 식으로든지 오류가 있을 때 발생합니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지될 때 Azure 플랫폼은 가상 머신을 호스트하는 비정상 물리적 서버에서 정상 물리적 서버로 가상 머신을 자동으로 마이그레이션합니다. 이러한 이벤트는 흔치 않지만 가상 머신이 재부팅되도록 할 수도 있습니다.

자세한 내용은 [Azure에서 Windows 가상 머신의 가용성 관리][azure-virtual-machines-manage-availability]를 참조하세요.

### <a name="azure-storage-redundancy"></a>Azure Storage 중복성
스토리지 계정의 데이터는 항상 내구성 및 고가용성을 보증하도록 복제되며 일시적인 하드웨어 오류가 발생하는 경우에도 Azure Storage SLA를 충족합니다.

Azure Storage에서는 기본적으로 데이터의 3가지 이미지를 유지하므로 여러 Azure 디스크에서 RAID 5 또는 RAID 1을 사용할 필요가 없습니다.

자세한 내용은 [Azure Storage 복제][azure-storage-redundancy]를 참조하세요.

### <a name="azure-managed-disks"></a>Azure Managed Disks
Managed Disks는 Azure 스토리지 계정에 저장된 VHD(가상 하드 디스크) 대신 사용하도록 권장되는 Azure Resource Manager의 리소스 종류입니다. Managed Disks는 연결되어 있는 가상 머신의 Azure 가용성 집합에 맞게 자동으로 조정됩니다. 따라서 가상 머신에서 실행되는 서비스와 가상 머신의 가용성을 높여 줍니다.

자세한 내용은 [Azure Managed Disks 개요][azure-storage-managed-disks-overview]를 참조하세요.

Managed Disks를 사용하면 가상 머신을 간편하고 배포 및 관리할 수 있으므로 권장됩니다.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Azure 인프라 고가용성을 활용하여 SAP 애플리케이션의 *고가용성* 확보

Linux에서 WSFC 또는 Pacemaker와 같은 기능을 사용하지 않기로 결정한 경우(현재 SLES[SUSE Linux Enterprise Server] 12 이상에서만 지원) Azure VM 다시 시작이 사용됩니다. 이 기능은 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 계획되었거나 계획되지 않은 가동 중지 시간에서 SAP 시스템을 보호합니다.

이 방법에 대한 자세한 내용은 [Azure 인프라 VM 다시 시작을 활용하여 SAP 시스템의 고가용성 확보][sap-higher-availability]를 참조하세요.

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Azure IaaS의 SAP 애플리케이션 고가용성

완전한 SAP 시스템 고가용성을 달성하려면 모든 중요 SAP 시스템 구성 요소를 보호해야 합니다. 예를 들면 다음과 같습니다.
  * 이중화 SAP 애플리케이션 서버
  * 고유한 구성 요소 SAP ASCS/SCS 인스턴스 또는 DBMS(데이터베이스 관리 시스템)과 같은 SPOF(단일 실패 지점) 구성 요소를 예로 들 수 있습니다.

다음 섹션에서는 세 가지 중요 SAP 시스템 구성 요소의 고가용성을 확보하는 방법에 대해 자세히 살펴봅니다.

### <a name="high-availability-architecture-for-sap-application-servers"></a>SAP 애플리케이션 서버의 고가용성 아키텍처

> 이 섹션은 다음에 적용됩니다.
>
> ![Windows][Logo_Windows] Windows 및 ![Linux][Logo_Linux] Linux
>

일반적으로 SAP 애플리케이션 서버 및 대화 상자 인스턴스의 경우 특정 고가용성 솔루션이 필요하지 않습니다. 중복성으로 고가용성을 달성하고 다양한 Azure Virtual Machines 인스턴스에서 여러 대화 상자 인스턴스를 구성합니다. 두 개의 Azure Virtual Machines 인스턴스에 2개 이상의 SAP 애플리케이션 인스턴스가 설치되어 있어야 합니다.

![그림 1: 고가용성 SAP 애플리케이션 서버][sap-ha-guide-figure-2000]

_**그림 1:** 고가용성 SAP 애플리케이션 서버_

SAP 애플리케이션 서버 인스턴스를 호스트하는 모든 가상 머신을 동일한 Azure 가용성 집합에 배치해야 합니다. Azure 가용성 집합은 다음을 확인합니다.

* 모든 가상 머신이 동일한 업데이트 도메인에 속하는지 여부.  
    업데이트 도메인은 가상 머신이 계획된 유지 관리 가동 중지 시간 동안 동시에 업데이트되지 않는지 확인합니다.

    Azure 배율 단위 내의 다른 업데이트 및 장애 도메인을 기반으로 하는 기본 기능은 이미 [업데이터 도메인][planning-guide-3.2.2] 섹션에서 살펴보았습니다.

* 모든 가상 머신이 동일한 장애 도메인에 속하는지 여부.  
    장애 도메인은 어떤 단일 장애 지점도 모든 가상 머신의 가용성에 영향을 주지 않도록 가상 머신이 배포되어 있는지 확인합니다.

Azure 배율 단위 내에서 Azure 가용성 집합이 사용할 수 있는 업데이트 도메인 및 장애 도메인 수는 제한되어 있습니다. 단일 가용성 집합에 VM을 계속 추가하는 경우 둘 이상의 VM이 결과적으로 동일한 장애 도메인 또는 업데이트 도메인에 추가됩니다.

전용 VM에서 일부 SAP 애플리케이션 서버 인스턴스를 배포하고 업데이트 도메인이 5개 있다고 가정하면 다음과 같은 그림이 완성됩니다. 가용성 집합 내의 업데이트 및 장애 도메인의 실제 최대 개수는 나중에 변경될 수 있습니다.

![그림 2: Azure 가용성 집합에서 SAP 애플리케이션 서버의 고가용성][planning-guide-figure-3000]
_**그림 2:** Azure 가용성 집합에서 SAP 애플리케이션 서버의 고가용성_

자세한 내용은 [Azure에서 Windows 가상 머신의 가용성 관리][azure-virtual-machines-manage-availability]를 참조하세요.

자세한 내용은 SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현 문서의 [Azure 가용성 집합][planning-guide-3.2.3] 섹션을 참조하세요.

**비관리 디스크 전용:** Azure Storage 계정은 잠재적인 단일 실패 지점일 수 있으므로 두 개 이상의 가상 머신이 배포될 2개 이상의 Azure Storage 계정이 있어야 합니다. 이상적인 설치에서는 SAP 대화 상자 인스턴스를 실행하는 각 가상 머신의 디스크를 다른 저장소 계정에 배포합니다.

> [!IMPORTANT]
> SAP 고가용성 설치에는 Azure Managed Disks를 사용하는 것이 좋습니다. Managed Disks는 연결되어 있는 가상 머신의 가용성 집합에 맞게 자동으로 조정되므로, 가상 머신에서 실행되는 서비스와 가상 머신의 가용성을 높여 줍니다.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Windows의 SAP ASCS/SCS 인스턴스에 대한 고가용성 아키텍처

> ![Windows][Logo_Windows] Windows
>

WSFC 솔루션을 사용하여 SAP ASCS/SCS 인스턴스를 보호할 수 있습니다. 솔루션에는 다음 두 가지 변형이 있습니다.

* **클러스터형 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링**: 이 아키텍처에 대한 자세한 내용은 [Windows 장애 조치(failover) 클러스터에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링][sap-high-availability-guide-wsfc-shared-disk]을 참조하세요.   

* **파일 공유를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링**: 이 아키텍처에 대한 자세한 내용은 [Windows 장애 조치(failover) 클러스터에서 파일 공유를 사용하여 SAP ASCS/SCS 인스턴스 클러스터링][sap-high-availability-guide-wsfc-file-share]을 참조하세요.

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Linux의 SAP ASCS/SCS 인스턴스에 대한 고가용성 아키텍처

> ![Linux][Logo_Linux] Linux
> 
> SLES 클러스터 프레임워크를 사용한 SAP ASCS/SCS 인스턴스 클러스터링에 대한 자세한 내용은 [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성][sap-suse-ascs-ha]을 참조하세요. SLES의 대체 HA 아키텍처에 대 한 필요 하지 않은 항상 사용 가능한 NFS 참조 [SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 SAP NetWeaver-고가용성 가이드] [ sap-suse-ascs-ha-anf].

Red Hat 클러스터 프레임워크를 사용한 SAP ASCS/SCS 인스턴스 클러스터링에 대한 자세한 내용은 [Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)을 참조하세요.


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>클러스터형 SAP ASCS/SCS 인스턴스에 대한 SAP NetWeaver 다중 SID 구성

> ![Windows][Logo_Windows] Windows
> 
> 현재 다중 SID는 WSFC와만 지원됩니다. 다중 SID는 파일 공유 및 공유 디스크를 사용하여 지원됩니다.
> 
> 다중 SID 고가용성 아키텍처에 대한 자세한 내용은 다음을 참조하세요.

* [Windows Server 장애 조치(failover) 클러스터링 및 파일 공유에 대한 SAP ASCS/SCS 인스턴스 다중 SID 고가용성][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Windows Server 장애 조치(failover) 클러스터링 및 공유 디스크에 대한 SAP ASCS/SCS 인스턴스 다중 SID 고가용성][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>고가용성 DBMS 인스턴스

DBMS는 SAP 시스템의 단일 연락 지점이기도 합니다. 따라서 고가용성 솔루션을 사용하여 보호해야 합니다. 다음 그림은 Windows Server 장애 조치(Failover) 클러스터링 및 Azure 내부 부하 분산 장치를 사용하여 Azure의 SQL Server Always On 고가용성 솔루션을 보여줍니다. SQL Server AlwaysOn은 자체 DBMS 복제를 사용하여 DBMS 데이터 및 로그 파일을 복제합니다. 이 경우 전체 설정을 간소화하는 클러스터 공유 디스크가 필요하지 않습니다.

![그림 3: SQL Server AlwaysOn을 사용하는 고가용성 SAP DBMS 예제][sap-ha-guide-figure-2003]

_**그림 3:** SQL Server AlwaysOn을 사용하는 고가용성 SAP DBMS 예제_

Azure Resource Manager 배포 모델을 사용하는 Azure의 SQL Server DBMS 클러스터링에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Resource Manager를 사용하여 Azure Virtual Machines에서 수동으로 Always On 가용성 그룹 구성][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Azure에서 AlwaysOn 가용성 그룹에 대한 Azure 내부 부하 분산 장치 구성][virtual-machines-windows-portal-sql-alwayson-int-listener]

Azure Resource Manager 배포 모델을 사용하는 Azure의 SAP HANA DBMS 클러스터링에 대한 자세한 내용은 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.

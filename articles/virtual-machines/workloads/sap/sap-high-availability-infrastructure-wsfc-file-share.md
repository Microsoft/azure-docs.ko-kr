---
title: SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비 | Microsoft Docs
description: SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 2ce38add-1078-4bb9-a1da-6f407a9bc910
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 58cd76e93b9d0888211e8339ae17170685e71e74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637757"
---
# <a name="prepare-azure-infrastructure-for-sap-high-availability-by-using-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances"></a>SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[ms-blog-s2d-in-azure]:https://blogs.technet.microsoft.com/filecab/2016/05/05/s2dazuretp5/
[arm-sofs-s2d-managed-disks]:https://github.com/robotechredmond/301-storage-spaces-direct-md
[arm-sofs-s2d-non-managed-disks]:https://github.com/Azure/azure-quickstart-templates/tree/master/301-storage-spaces-direct
[deploy-cloud-witness]:https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness

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
[sap-high-availability-infrastructure-wsfc-shared-disk-azure-network]:sap-high-availability-infrastructure-wsfc-shared-disk.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-high-availability-infrastructure-wsfc-shared-disk-dns-ip]:sap-high-availability-infrastructure-wsfc-shared-disk.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ascs-high-availability-multi-sid-wsfc-set-static-ip]:sap-high-availability-infrastructure-wsfc-shared-disk.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-high-availability-infrastructure-wsfc-shared-disk-set-static-ip-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-high-availability-infrastructure-wsfc-shared-disk-change-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]:sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f



[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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

이 문서에서는 SAP ASCS/SCS 인스턴스를 클러스터링하는 옵션으로 스케일 아웃 파일 공유를 사용하여 WSFC(Windows 장애 조치(failover) 클러스터)에서 고가용성 SAP 시스템을 설치하고 구성하는 데 필요한 Azure 인프라 준비 단계를 설명합니다.

## <a name="prerequisite"></a>필수 요소

설치를 시작하기 전에 다음 문서를 검토하세요.

* [아키텍처 가이드: 파일 공유를 사용 하 여 Windows 장애 조치 클러스터에 SAP ASCS/SCS 인스턴스 클러스터][sap-high-availability-guide-wsfc-file-share]


## <a name="host-names-and-ip-addresses"></a>호스트 이름 및 IP 주소

| 가상 호스트 이름 역할 | 가상 호스트 이름 | 고정 IP 주소 | 가용성 집합 |
| --- | --- | --- | --- |
| 첫 번째 클러스터 노드 ASCS/SCS 클러스터 | ascs-1 | 10.0.6.4 | ascs-as |
| 두 번째 클러스터 노드 ASCS/SCS 클러스터 | ascs-2 | 10.0.6.5 | ascs-as |
| 클러스터 네트워크 이름 |ascs-cl | 10.0.6.6 | 해당 없음 |
| SAP PR1 ASCS 클러스터 네트워크 이름 |pr1-ascs | 10.0.6.7 | 해당 없음 |


**표 1**: ASCS/SCS 클러스터

| SAP \<SID> | SAP ASCS/SCS 인스턴스 번호 |
| --- | --- |
| PR1 | 00 |

**표 2**: SAP ASCS/SCS 인스턴스 세부 정보


| 가상 호스트 이름 역할 | 가상 호스트 이름 | 고정 IP 주소 | 가용성 집합 |
| --- | --- | --- | --- |
| 첫 번째 클러스터 노드 | sofs-1 | 10.0.6.10 | sofs-as |
| 두 번째 클러스터 노드 | sofs-2 | 10.0.6.11 | sofs-as |
| 세 번째 클러스터 노드 | sofs-3 | 10.0.6.12 | sofs-as |
| 클러스터 네트워크 이름 | sofs-cl | 10.0.6.13 | 해당 없음 |
| SAP 전역 호스트 이름 | sapglobal | 모든 클러스터 노드의 IP 사용 | 해당 없음 |

**표 3**: 스케일 아웃 파일 서버 클러스터


## <a name="deploy-vms-for-an-sap-ascsscs-cluster-a-database-management-system-dbms-cluster-and-sap-application-server-instances"></a>SAP ASCS/SCS 클러스터, DBMS(데이터베이스 관리 시스템) 클러스터 및 SAP 애플리케이션 서버 인스턴스에 대한 VM을 배포합니다.

Azure 인프라를 준비하려면 다음을 완료합니다.

* [아키텍처 템플릿 1, 2 및 3에 대한 인프라 준비][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Azure 가상 네트워크 만들기][sap-high-availability-infrastructure-wsfc-shared-disk-azure-network]

* [필수 DNS IP 주소 설정][sap-high-availability-infrastructure-wsfc-shared-disk-dns-ip]

* [SAP 가상 머신의 고정 IP 주소 설정][sap-ascs-high-availability-multi-sid-wsfc-set-static-ip]

* [Azure 내부 부하 분산 장치의 고정 IP 주소 설정][sap-high-availability-infrastructure-wsfc-shared-disk-set-static-ip-ilb]

* [Azure 내부 부하 분산 장치의 기본 ASCS/SCS 부하 분산 규칙 설정][sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]

* [Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-high-availability-infrastructure-wsfc-shared-disk-change-ascs-ilb-rules]

* [도메인에 Windows 가상 머신 추가][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]

* [SAP ASCS/SCS 인스턴스의 클러스터 노드 모두에 레지스트리 항목 추가][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]

* Windows Server 2016을 사용하는 경우 [Azure Cloud Witness][deploy-cloud-witness]를 구성하는 것이 좋습니다.


## <a name="deploy-the-scale-out-file-server-cluster-manually"></a>수동으로 스케일 아웃 파일 서버 클러스터 배포 

다음 코드를 실행하여 [Azure의 저장소 공간 다이렉트][ms-blog-s2d-in-azure] 블로그에 설명된 대로 Microsoft 스케일 아웃 파일 서버 클러스터를 수동으로 배포할 수 있습니다.  


```powershell
# Set an execution policy - all cluster nodes
Set-ExecutionPolicy Unrestricted

# Define Scale-Out File Server cluster nodes
$nodes = ("sofs-1", "sofs-2", "sofs-3")

# Add cluster and Scale-Out File Server features
Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools -Verbose}

# Test cluster
Test-Cluster -node $nodes -Verbose

# Install cluster
$ClusterNetworkName = "sofs-cl"
$ClusterIP = "10.0.6.13"
New-Cluster -Name $ClusterNetworkName -Node $nodes –NoStorage –StaticAddress $ClusterIP -Verbose

# Set Azure Quorum
Set-ClusterQuorum –CloudWitness –AccountName gorcloudwitness -AccessKey <YourAzureStorageAccessKey>

# Enable Storage Spaces Direct
Enable-ClusterS2D

# Create Scale-Out File Server with an SAP global host name
# SAPGlobalHostName
$SAPGlobalHostName = "sapglobal"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

## <a name="deploy-scale-out-file-server-automatically"></a>자동으로 스케일 아웃 파일 서버 배포

기존 가상 네트워크 및 Active Directory 환경에서 Azure Resource Manager 템플릿을 사용하여 스케일 아웃 파일 서버 배포를 자동화할 수도 있습니다.

> [!IMPORTANT]
> 3방향 미러링을 사용하는 스케일 아웃 파일 서버에 3개 이상의 클러스터 노드가 있는 것이 좋습니다.
>
> 스케일 아웃 파일 서버 Resource Manager 템플릿 UI에서 VM 수를 지정해야 합니다.
>

### <a name="use-managed-disks"></a>관리 디스크 사용

저장소 공간 다이렉트 및 Azure Managed Disks를 사용하여 스케일 아웃 파일 서버를 배포하는 Azure Resource Manager 템플릿은 [GitHub][arm-sofs-s2d-managed-disks]에서 사용할 수 있습니다.

Managed Disks를 사용하는 것이 좋습니다.

![그림 1: 관리 디스크를 사용 하 여 스케일 아웃 파일 서버 Resource Manager 템플릿의 UI 화면][sap-ha-guide-figure-8010]

_**그림 1**: 관리 디스크를 사용 하 여 스케일 아웃 파일 서버 Resource Manager 템플릿의 UI 화면_

템플릿에서 다음 작업을 수행합니다.
1. **VM 수** 상자에 **2**라는 최소 수를 입력합니다.
2. **VM 디스크 수** 상자에 **3**이라는 최소 디스크 수를 입력합니다(2개 디스크 + 1개 예비 디스크 = 3개 디스크).
3. **SOFS 이름**에 SAP 전역 호스트 네트워크 이름인 **sapglobalhost**를 입력합니다.
4. **공유 이름** 상자에 파일 공유 이름인 **sapmnt**를 입력합니다.

### <a name="use-unmanaged-disks"></a>관리되지 않는 디스크 사용

저장소 공간 다이렉트 및 Azure 관리되지 않는 디스크를 사용하여 스케일 아웃 파일 서버를 배포하는 Azure Resource Manager 템플릿은 [GitHub][arm-sofs-s2d-non-managed-disks]에서 사용할 수 있습니다.

![그림 2: Managed disks가 없는 스케일 아웃 파일 서버 Azure Resource Manager 템플릿의 UI 화면][sap-ha-guide-figure-8011]

_**그림 2**: Managed disks가 없는 스케일 아웃 파일 서버 Azure Resource Manager 템플릿의 UI 화면_

**스토리지 계정 형식** 상자에서 **Premium Storage**를 선택합니다. 다른 모든 설정은 Managed Disks와 동일합니다.

## <a name="next-steps"></a>다음 단계

* [SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에서 SAP NetWeaver 고가용성 설치][sap-high-availability-installation-wsfc-file-share]

---
title: Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성 | Microsoft Docs
description: Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스를 위한 다중 SID 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a840deb2349d952b1ef4faeab4ee860e6b0b99df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60651716"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[sap-installation-guides]:http://service.sap.com/instguides
[sap-installation-guides-file-share]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

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

[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md#25e358f8-92e5-4e8d-a1e5-df7580a39cb0
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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

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

# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-file-share-on-azure"></a>Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성

> ![Windows][Logo_Windows] Windows
>

[Azure 내부 부하 분산 장치][load-balancer-multivip-overview]를 사용하여 여러 가상 IP 주소를 관리할 수 있습니다. 

SAP를 배포한 경우 내부 부하 분산 장치를 사용하여 SAP 중앙 서비스(ASCS/SCS) 인스턴스에 대한 Windows 클러스터 구성을 만들 수 있습니다.

이 문서에서는 **파일 공유**를 사용하는 기존 WSFC(Windows Server 장애 조치(failover) 클러스터링) 클러스터에 추가 SAP ASCS/SCS 클러스터형 인스턴스를 설치하여 단일 ASCS/SCS 설치에서 SAP 다중 SID 구성으로 이동하는 방법을 중점적으로 설명합니다. 이 프로세스가 완료되면 SAP 다중 SID 클러스터가 구성됩니다.

> [!NOTE]
>
> 이 기능은 Azure Resource Manager 배포 모델에만 사용할 수 있습니다.
>
>각 Azure 내부 부하 분산 장치에 대한 개인 프런트 엔드 IP의 수에 제한이 있습니다.
>
>하나의 WSFC 클러스터에서 SAP ASCS/SCS 인스턴스의 최대수는 각 Azure 내부 부하 분산 장치에 대한 개인 프런트 엔드 IP의 최대수와 같습니다.
>
> 이 설명서에 도입한 구성은 아직 [Azure 가용성 영역](https://docs.microsoft.com/azure/availability-zones/az-overview)에 사용하도록 지원되지 않습니다.
> 

부하 분산 장치 제한에 대한 자세한 내용은 [네트워킹 제한: Azure Resource Manager][networking-limits-azure-resource-manager]의 "부하 분산 장치당 개인 프런트 엔드 IP"를 참조하세요. 또한 Azure 부하 분산 장치의 기본 SKU 대신 [Azure 표준 Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) 사용도 고려하세요.

## <a name="prerequisites"></a>필수 조건

이 다이어그램처럼 **파일 공유**를 사용하는 한 SAP ASCS/SCS 인스턴스에 사용되는 WSFC 클러스터가 이미 구성되어 있어야 합니다.

![그림 1: 두 클러스터에 배포된 SAP ASCS/SCS 인스턴스 및 SOFS][sap-ha-guide-figure-8007]

_**그림 1:** 두 클러스터에 배포된 SAP ASCS/SCS 인스턴스 및 SOFS_

> [!IMPORTANT]
> 설치 프로그램은 다음 조건을 충족해야 합니다.
> * SAP ASCS/SCS 인스턴스는 동일한 WSFC 클러스터를 공유해야 합니다.
> * 서로 다른 SAP SID에 속한 서로 다른 SAP 글로벌 호스트 파일 공유는 동일한 SOFS 클러스터를 공유해야 합니다.
> * 각 DBMS(데이터베이스 관리 시스템) SID에는 해당하는 고유한 전용 WSFC 클러스터가 있어야 합니다.
> * 하나의 SAP 시스템 SID에 속하는 SAP 애플리케이션 서버에는 고유한 전용 VM이 있어야 합니다.

## <a name="sap-ascsscs-multi-sid-architecture-with-file-share"></a>파일 공유를 사용하는 SAP ASCS 다중 SID 아키텍처

목적은 여기에서 설명한 것처럼 동일한 WSFC 클러스터에서 여러 SAP ASCS(Advanced Business Application Programming) 또는 SAP Java(SCS) 클러스터링된 인스턴스를 설치하는 것입니다. 

![그림 2: 두 클러스터에 SAP 다중 SID 구성][sap-ha-guide-figure-8008]

_**그림 2:** 두 클러스터에 SAP 다중 SID 구성_

추가 설치 **SAP \<SID2 >** 시스템의 설치와 동일 \<SID > 시스템입니다. ASCS/SCS 클러스터 및 파일 공유 SOFS 클러스터에서 두 개의 추가 준비 단계가 필요합니다.

## <a name="prepare-the-infrastructure-for-an-sap-multi-sid-scenario"></a>SAP 다중 SID 시나리오에 대한 인프라 준비

### <a name="prepare-the-infrastructure-on-the-domain-controller"></a>도메인 컨트롤러에서 인프라 준비

예를 들어 \<SID2> = PR2를 사용하여 **\<Domain>\SAP_\<SID2>_GlobalAdmin** 도메인 그룹을 만듭니다. 도메인 그룹 이름은 \<Domain>\SAP_PR2_GlobalAdmin입니다.

### <a name="prepare-the-infrastructure-on-the-ascsscs-cluster"></a>ASCS/SCS 클러스터에서 인프라 준비

기존 ASCS/SCS 클러스터에서 두 번째 SAP \<SID>에 대한 인프라를 준비해야 합니다.

* DNS 서버에서 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름을 만듭니다.
* PowerShell을 사용하여 기존 Azure 내부 부하 분산 장치에 IP 주소를 추가합니다.

이러한 단계는 [SAP 다중 SID 시나리오를 위한 인프라 준비][sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare]에 설명되어 있습니다.


### <a name="prepare-the-infrastructure-on-an-sofs-cluster-by-using-the-existing-sap-global-host"></a>기존 SAP 글로벌 호스트를 사용하여 SOFS 클러스터에서 인프라 준비

기존 다시 사용할 수 있습니다 \<SAPGlobalHost > 및 Volume1을 첫 번째 sap \<SID1 > 시스템입니다.

![그림 3: 다중 SID SOFS가 SAP 글로벌 호스트 이름과 같음][sap-ha-guide-figure-8014]

_**그림 3:** 다중 SID SOFS가 SAP 글로벌 호스트 이름과 같음_

> [!IMPORTANT]
>두 번째 **SAP \<SID2>** 시스템의 경우 동일한 Volume1 및 동일한 **\<SAPGlobalHost>** 네트워크 이름이 사용됩니다.
>**SAPMNT**를 다양한 SAP 시스템의 공유 이름으로 이미 설정했으므로 **\<SAPGlobalHost>** 네트워크 이름을 다시 사용하려면 동일한 **Volume1**을 사용해야 합니다.
>
>에 대 한 파일 경로 \<SID2 > 전역 호스트는 C:\ClusterStorage\\**Volume1**\usr\sap\<SID2 > \SYS\.
>

\<SID2> 시스템의 경우 SOFS 클러스터에서 SAP 글로벌 호스트 ..\SYS\.. 폴더를 준비해야 합니다.

\<SID2> 인스턴스에 대한 SAP 글로벌 호스트를 준비하려면 다음 PowerShell 스크립트를 실행합니다.


```powershell
##################
# SAP multi-SID
##################

$SAPSID2 = "PR2"
$DomainName2 = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName2 = "$DomainName2\SAP_" + $SAPSID2 + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSCluster2Node1 = "ja1-ascs-0"
$ASCSCluster2Node2 = "ja1-ascs-1"

# Define the SAP ASCS/SCS cluster node computer objects
$ASCSCluster2ObjectNode1 = "$DomainName2\$ASCSCluster2Node1$"
$ASCSCluster2ObjectNode2 = "$DomainName2\$ASCSCluster2Node2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder2 = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID2\SYS"
New-Item -Path $SAPGlobalFolder2 -ItemType Directory

# Add permissions for the SAP SID2 system
Grant-SmbShareAccess -Name sapmnt -AccountName $SAPSIDGlobalAdminGroupName2, $ASCSCluster2ObjectNode1, $ASCSCluster2ObjectNode2 -AccessRight Full -Force


$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add the security object of the SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName2,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

### <a name="prepare-the-infrastructure-on-the-sofs-cluster-by-using-a-different-sap-global-host"></a>다른 SAP 글로벌 호스트를 사용하여 SOFS 클러스터에서 인프라 준비

두 번째 SOFS를 구성할 수 있습니다(예를 들어 두 번째 **\<SID2>** 에 대해 **\<SAPGlobalHost2>** 및 다른 **Volume2**를 사용하여 두 번째 SOFS 클러스터 역할 구성).

![그림 4: 다중 SID SOFS가 SAP 글로벌 호스트 이름 2와 같음][sap-ha-guide-figure-8015]

_**그림 4:** 다중 SID SOFS가 SAP 글로벌 호스트 이름 2와 같음_

\<SAPGlobalHost2>로 두 번째 SOFS 역할을 만들려면 다음 PowerShell 스크립트를 실행합니다.

```powershell
# Create SOFS with SAP Global Host Name 2
$SAPGlobalHostName = "sapglobal2"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

두 번째 **Volume2**를 만듭니다. 이 PowerShell 스크립트를 실행합니다.

```powershell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR2 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```

![그림 5: 장애 조치 클러스터 관리자에서 두 번째 Volume2][sap-ha-guide-figure-8016]

_**그림 5:** 장애 조치(Failover) 클러스터 관리자의 두 번째 Volume2_

두 번째 \<SID2>에 대한 SAP 글로벌 폴더를 만들고 파일 보안을 설정합니다.

이 PowerShell 스크립트를 실행합니다.

```powershell
# Create a folder for <SID2> on a second Volume2 and set file security
$SAPSID = "PR2"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume2\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume2\usr\sap\"

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add the file security object of the SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add the security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

두 번째 SAP\<SID2>에 대해 *\<SAPGlobalHost2>* 호스트 이름을 사용하여 Volume2에 SAPMNT 파일 공유를 만들려면 장애 조치(Failover) 클러스터 관리자에서 **파일 공유 추가** 마법사를 시작합니다.

**saoglobal2** SOFS 클러스터 그룹을 마우스 오른쪽 단추로 클릭하고 **파일 공유 추가**를 선택합니다.

![그림 6: "파일 공유 추가" 마법사 시작][sap-ha-guide-figure-8017]

_**그림 6:** "파일 공유 추가" 마법사 시작_

<br>

![그림 7: "SMB 공유 – 빠른 선택"][sap-ha-guide-figure-8018]

_**그림 7:** "SMB 공유 – 빠르게" 선택_

<br>

![그림 8: "Sapglobalhost2"를 선택 하 고 Volume2에 경로 지정][sap-ha-guide-figure-8019]

_**그림 8:** "sapglobalhost2"를 선택하고 Volume2에 경로 지정_

<br>

![그림 9: 파일 공유 이름을 "sapmnt"로 설정][sap-ha-guide-figure-8020]

_**그림 9:** 파일 공유 이름을 "sapmnt"로 설정_

<br>

![그림 10: 모든 설정 사용 안 함][sap-ha-guide-figure-8021]

_**그림 10:** 모든 설정 사용 안 함_

<br>

다음에 대해 *모든 권한*을 파일 및 sapmnt 공유에 할당합니다.
* **SAP_\<SID>_GlobalAdmin** 도메인 사용자 그룹
* ASCS/SCS 클러스터 노드 **ascs-1$** 및 **ascs-2$** 의 컴퓨터 개체

![그림 11: 사용자 그룹 및 컴퓨터 계정에 모든 권한 할당][sap-ha-guide-figure-8022]

_**그림 11:** 사용자 그룹 및 컴퓨터 계정에 "모든 권한" 할당_

<br>

![그림 12: “만들기” 선택][sap-ha-guide-figure-8023]

_**그림 12:** "만들기" 선택_

<br>

![그림 13: 두 번째 sapmnt가 sapglobal2 호스트에 연결 하 고 Volume2 생성][sap-ha-guide-figure-8024]

_**그림 13:** 두 번째 sapmnt가 sapglobal2 호스트에 바인딩되고 Volume2 생성_

<br>

## <a name="install-sap-netweaver-multi-sid"></a>SAP NetWeaver 다중 SID 설치

### <a name="install-sap-sid2-ascsscs-and-ers-instances"></a>SAP \<SID2> ASCS/SCS 및 ERS 인스턴스 설치

한 SAP \<SID>에 대해 설명된 것과 동일한 설치 및 구성 단계를 따릅니다.

### <a name="install-dbms-and-sap-application-servers"></a>DBMS 및 SAP 애플리케이션 서버 설치
앞에서 설명한 대로 DBMS 및 SAP 애플리케이션 서버를 설치합니다.

## <a name="next-steps"></a>다음 단계

* [공유 디스크 없이 장애 조치(Failover) 클러스터에 ASCS/SCS 인스턴스 설치][sap-official-ha-file-share-document]: HA 파일 공유에 대한 공식 SAP 지침

* [Windows Server 2016의 저장소 공간 다이렉트][s2d-in-win-2016]

* [애플리케이션 데이터에 대한 스케일 아웃 파일 서버 개요][sofs-overview]

* [Windows Server 2016 저장소의 새로운 기능][new-in-win-2016-storage]

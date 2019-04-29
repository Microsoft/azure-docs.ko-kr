---
title: Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS를 위한 SAP HA용 Azure 인프라 준비 | Microsoft Docs
description: Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 위한 SAP HA용 Azure 인프라를 준비하는 방법을 알아봅니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b729327187a52f36d50f8a754f5521527bb07ac6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60717715"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS를 위한 SAP HA용 Azure 인프라 준비

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

이 문서에서는 SAP ASCS 인스턴스를 클러스터링하는 옵션으로서 *클러스터 공유 디스크*를 사용하여 Windows 장애 조치(Failover) 클러스터에서 고가용성 SAP 시스템을 설치 및 구성하기 위해 Azure 인프라를 준비하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

설치를 시작하기 전에 먼저 다음 문서를 검토하세요.

* [아키텍처 가이드: 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>아키텍처 템플릿 1에 대한 인프라 준비
SAP용 Azure Resource Manager 템플릿은 필요한 리소스의 배포를 간소화하도록 도와줍니다.

Azure Resource Manager의 3계층 템플릿도 고가용성 시나리오를 지원합니다. 예를 들어, 아키텍처 템플릿 1은 2개의 클러스터를 갖습니다. 각 클러스터는 SAP ASCS/SCS 및 DBMS에 대한 SAP 단일 실패 지점입니다.

이 문서에서 설명하는 예제 시나리오를 위한 Azure Resource Manager 템플릿을 가져올 수 있는 위치는 다음과 같습니다

* [Azure Marketplace 이미지](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace 이미지, Azure Managed Disks 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [사용자 지정 이미지](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [사용자 지정 이미지, Managed Disks 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

아키텍처 템플릿 1에 대한 인프라를 준비하려면:

- Azure Portal에서 **매개 변수** 창의 **SYSTEMAVAILABILITY** 상자에서 **HA**를 선택합니다.

  ![그림 1: SAP 고가용성 Azure Resource Manager 매개 변수 설정][sap-ha-guide-figure-3000]

_**그림 1:** SAP 고가용성 Azure Resource Manager 매개 변수 설정_


  템플릿은 다음을 만듭니다.

  * **가상 머신**:
    * SAP 애플리케이션 서버 가상 머신: \<SAPSystemSID\>-di-\<Number\>
    * ASCS/SCS 클러스터 가상 머신: \<SAPSystemSID\>-ascs-\<Number\>
    * DBMS 클러스터: \<SAPSystemSID\>-db-\<Number\>

  * **연결된 IP 주소를 갖는 모든 가상 머신에 대한 네트워크 카드**:
    * \<SAPSystemSID\>-nic-di-\<번호\>
    * \<SAPSystemSID\>-nic-ascs-\<번호\>
    * \<SAPSystemSID\>-nic-db-\<번호\>

  * **Azure 저장소 계정(비관리 디스크만 해당)**:

  * 다음에 대한 **가용성 그룹**:
    * SAP 애플리케이션 서버 가상 머신: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS 클러스터 가상 머신: \<SAPSystemSID\>-avset-ascs
    * DBMS 클러스터 가상 머신: \<SAPSystemSID\>-avset-db

  * **Azure 내부 부하 분산 장치**:
    * ASCS/SCS 인스턴스 및 IP 주소 \<SAPSystemSID\>-lb-ascs에 대한 모든 포트
    * SQL Server DBMS 및 IP 주소 \<SAPSystemSID\>-lb-db에 대한 모든 포트

  * **네트워크 보안 그룹**: \<SAPSystemSID\>-nsg-ascs-0  
    * \<SAPSystemSID\>-ascs-0 가상 머신에 대해 열려 있는 외부 RDP(원격 데스크톱 프로토콜) 포트

> [!NOTE]
> 네트워크 카드 및 Azure 내부 부하 분산 장치의 모든 IP 주소는 기본적으로 유동 IP입니다. 이 주소를 고정 IP 주소로 변경합니다. 이를 수행하는 방법은 문서 뒷부분에 설명되어 있습니다.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 프로덕션 환경에서 사용하기 위해 회사 네트워크 연결(크로스-프레미스)을 사용하여 가상 컴퓨터 배포
프로덕션 SAP 시스템의 경우 Azure VPN Gateway 또는 Azure ExpressRoute를 사용하여 [회사 네트워크 연결(크로스-프레미스)][planning-guide-2.2]를 통해 Azure Virtual Machines를 배포합니다.

> [!NOTE]
> Azure Virtual Network 인스턴스를 사용할 수 있습니다. 가상 네트워크 및 서브넷은 이미 생성되고 준비되어 있습니다.
>
>

1. Azure Portal에서 **매개 변수** 패널의 **NEWOREXISTINGSUBNET** 상자에서 **기존**을 선택합니다.
2. Azure 가상 머신을 배포하려는 경우 **SUBNETID** 상자에서 준비된 Azure 네트워크 서브넷 ID의 전체 문자열을 추가합니다.
3. 모든 Azure 네트워크 서브넷 목록을 가져오려면 이 PowerShell 명령을 실행합니다.

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **ID** 필드에 서브넷 ID의 값이 표시됩니다.
4. 모든 서브넷 ID 값 목록을 가져오려면 다음 PowerShell 명령을 실행합니다.

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   서브넷 ID는 다음과 같습니다.

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> 테스트 및 데모용 클라우드 전용 SAP 인스턴스 배포
클라우드 전용 배포 모델에서 고가용성 SAP 시스템을 배포할 수 있습니다. 기본적으로 이러한 종류의 배포는 데모 및 테스트 사용 사례에 사용할 수 있습니다. 프로덕션 사용 사례에는 적합하지 않습니다.

- Azure Portal에서 **매개 변수** 패널의 **NEWOREXISTINGSUBNET** 상자에서 **신규**를 선택합니다. **SUBNETID** 필드는 비워둡니다.

  SAP Azure Resource Manager 템플릿은 Azure 가상 네트워크 및 서브넷을 자동으로 만듭니다.

> [!NOTE]
> 또한 동일한 Azure Virtual Network 인스턴스에 Active Directory 및 DNS 서비스에 대한 하나 이상의 전용 가상 머신을 배포해야 합니다. 이 템플릿이 이러한 가상 머신을 만들지는 않습니다.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>아키텍처 템플릿 2에 대한 인프라 준비

이 Azure Resource Manager 템플릿을 사용하면 SAP에서 SAP 아키텍처 템플릿 2에 필요한 인프라 리소스를 간단히 배포할 수 있습니다.

이 배포 시나리오를 위한 Azure Resource Manager 템플릿을 가져올 수 있는 위치는 다음과 같습니다

* [Azure Marketplace 이미지](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace 이미지, Managed Disks 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [사용자 지정 이미지](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [사용자 지정 이미지, Managed Disks 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>아키텍처 템플릿 3에 대한 인프라 준비

인프라를 준비하고 다중 SID용 SAP를 구성할 수 있습니다. 예를 들어 추가 SAP ASCS/SCS 인스턴스를 *기존* 클러스터 구성에 추가할 수 있습니다. 자세한 내용은 [추가 SAP ASCS/SCS 인스턴스를 기존 클러스터 구성에 구성하여 Azure Resource Manager에서 SAP 다중 SID 구성 만들기][sap-ha-multi-sid-guide]를 참조하세요.

새 다중 SID 클러스터를 만들려면 [GitHub에 있는 다중 SID 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)을 사용할 수 있습니다.

새 다중 SID 클러스터를 만들려면 다음 세 가지 템플릿을 배포해야 합니다.

* [ASCS/SCS 템플릿](#ASCS-SCS-template)
* [데이터베이스 템플릿](#database-template)
* [애플리케이션 서버 템플릿](#application-servers-template)

다음 섹션에는 템플릿에서 제공해야 하는 템플릿과 매개 변수에 대한 자세한 정보가 있습니다.

### <a name="ASCS-SCS-template"></a> ASCS/SCS 템플릿

ASCS/SCS 템플릿은 여러 ASCS/SCS 인스턴스를 호스팅하는 Windows 서버 장애 조치(Failover) 클러스터를 만드는 데 사용할 수 있는 두 개의 가상 머신을 배포합니다.

ASCS/SCS 다중 SID 템플릿을 설정하려면 [ASCS/SCS 다중 SID 템플릿][sap-templates-3-tier-multisid-xscs-marketplace-image] 또는 [Managed Disks를 사용하는 ASCS/SCS 다중 SID 템플릿][sap-templates-3-tier-multisid-xscs-marketplace-image-md]에서 다음 매개 변수 값을 입력합니다.

- **리소스 접두사**:  배포 중에 만들어진 모든 리소스 앞에 붙는 접두사로 사용되는 리소스 접두사를 설정합니다. 리소스는 하나의 SAP 시스템에만 속하지 않으므로 리소스의 접두사는 SAP 시스템 하나의 SID가 아닙니다.  접두사는 3~6자 사이여야 합니다.
- **스택 유형**: SAP 시스템의 스택 유형을 선택합니다. 스택 유형에 따라 Azure Load Balancer에는 SAP 시스템당 하나(ABAP 또는 Java 중 하나만) 또는 둘(ABAP 및 Java 각각 하나씩)의 개인 IP 주소가 있습니다.
- **OS 유형**: 가상 머신의 운영 체제를 선택합니다.
- **SAP 시스템 수**: 이 클러스터에 설치하려는 SAP 시스템 수를 선택합니다.
- **시스템 가용성**: **HA**를 선택합니다.
- **관리자 사용자 이름 및 관리자 암호**: 컴퓨터에 로그인하는 데 사용할 수 있는 새 사용자를 만듭니다.
- **새 서브넷 또는 기존 서브넷**: 새 가상 네트워크 및 서브넷을 만들지 아니면 기존 서브넷을 사용할지 설정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 **기존** 항목을 선택합니다.
- **서브넷 ID**: 서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 일반적으로 다음과 같이 나타납니다.

  /subscriptions/\<구독 ID\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Network/virtualNetworks/\<가상 네트워크 이름\>/subnets/\<서브넷 이름\>

템플릿은 여러 SAP 시스템을 지원하는 하나의 Azure Load Balancer 인스턴스를 배포합니다.

- ASCS 인스턴스의 인스턴스 번호는 00, 10, 20...으로 구성됩니다.
- SCS 인스턴스의 인스턴스 번호는 01, 11, 21...로 구성됩니다.
- ASCS ERS(Enqueue Replication Server)(Linux 전용) 인스턴스의 인스턴스 번호는 02, 12, 22...로 구성됩니다.
- SCS ERS(Linux 전용) 인스턴스의 인스턴스 번호는 03, 13, 23...으로 구성됩니다.

부하 분산 장치는 1개의 VIP(Linux는 2개) , ASCS/SCS용 1x VIP 및 ERS용 1x VIP(Linux 전용)를 포함합니다.

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> SAP ASCS/SCS 포트
다음 목록은 모든 부하 분산 규칙을 포함하며 여기서 x는 SAP 시스템의 번호입니다(예: 1, 2, 3...).
- 모든 SAP 시스템에 대한 Windows 특정 포트: 445, 5985
- ASCS 포트(x0 인스턴스 번호): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS 포트(x1 인스턴스 번호): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Linux의 ASCS ERS 포트(x2 인스턴스 번호): 33x2, 5x213, 5x214, 5x216
- Linux의 SCS ERS 포트(x3 인스턴스 번호): 33x3, 5x313, 5x314, 5x316

부하 분산 장치는 다음 프로브 포트를 사용하도록 구성되며 여기서 x는 SAP 시스템의 번호입니다(예: 1, 2, 3...).
- ASCS/SCS 내부 부하 분산 장치 프로브 포트: 620x0
- ERS 내부 부하 분산 장치 프로브 포트(Linux 전용): 621x2

### <a name="database-template"></a> 데이터베이스 템플릿

데이터베이스 템플릿은 단일 SAP 시스템에 대한 관계형 데이터베이스 관리 시스템(RDBMS)을 설치하는 데 사용할 수 있는 하나 또는 두 개의 가상 머신을 배포합니다. 예를 들어 5개 SAP 시스템에 대해 ASCS/SCS 템플릿을 배포하는 경우 이 템플릿을 5번 배포해야 합니다.

데이터베이스 다중 SID 템플릿을 설정하려면 [데이터베이스 다중 SID 템플릿][sap-templates-3-tier-multisid-db-marketplace-image] 또는 [Managed Disks를 사용하는 데이터베이스 다중 SID 템플릿][sap-templates-3-tier-multisid-db-marketplace-image-md]에서 다음 매개 변수 값을 입력합니다.

- **SAP 시스템 ID**: 설치하려는 SAP 시스템의 SAP 시스템 ID를 입력합니다. 이 ID는 배포되는 리소스의 접두사로 사용됩니다.
- **OS 유형**: 가상 머신의 운영 체제를 선택합니다.
- **Dbtype**: 클러스터에 설치하려는 데이터베이스의 유형을 선택합니다. Microsoft SQL Server를 설치하려는 경우 **SQL**을 선택합니다. 가상 머신에 SAP HANA를 설치하려는 경우 **HANA**를 선택합니다. 올바른 운영 체제 종류를 선택했는지 확인합니다. SQL에는 **Windows**를 선택하고 HANA에는 Linux 배포판을 선택합니다. 가상 머신에 연결되는 Azure Load Balancer는 선택한 다음 데이터베이스 형식을 지원하도록 구성됩니다.
  * **SQL**: 부하 분산 장치에서 1433 포트의 부하를 균형 조정합니다. SQL Server AlwaysOn 설정에 이 포트를 사용해야 합니다.
  * **HANA**: 부하 분산 장치에서 35015 및 35017 포트의 부하를 균형 조정합니다. **50** 인스턴스 번호의 SAP HANA를 설치합니다.
  부하 분산 장치에서 62550 프로브 포트를 사용합니다.
- **SAP 시스템 크기**: 새 시스템에서 제공하는 SAP 수를 설정합니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
- **시스템 가용성**: **HA**를 선택합니다.
- **관리자 사용자 이름 및 관리자 암호**: 컴퓨터에 로그인하는 데 사용할 수 있는 새 사용자를 만듭니다.
- **서브넷 ID**: ASCS/SCS 템플릿 배포 중에 사용된 서브넷의 ID 또는 ASCS/SCS 템플릿 배포의 일부로 만든 서브넷의 ID를 입력합니다.

### <a name="application-servers-template"></a> 애플리케이션 서버 템플릿

애플리케이션 서버 템플릿은 하나의 SAP 시스템을 위한 SAP 애플리케이션 서버 인스턴스로 사용할 수 있는 둘 이상의 가상 머신을 배포합니다. 예를 들어 5개 SAP 시스템에 대해 ASCS/SCS 템플릿을 배포하는 경우 이 템플릿을 5번 배포해야 합니다.

애플리케이션 서버 다중 SID 템플릿을 설정하려면 [애플리케이션 서버 다중 SID 템플릿][sap-templates-3-tier-multisid-apps-marketplace-image] 또는 [Managed Disks를 사용하는 애플리케이션 서버 다중 SID 템플릿][sap-templates-3-tier-multisid-apps-marketplace-image-md]에서 다음 매개 변수 값을 입력합니다.

  -  **SAP 시스템 ID**: 설치하려는 SAP 시스템의 SAP 시스템 ID를 입력합니다. 이 ID는 배포되는 리소스의 접두사로 사용됩니다.
  -  **OS 유형**: 가상 머신의 운영 체제를 선택합니다.
  -  **SAP 시스템 크기**: 새 시스템에서 제공하는 SAP의 수입니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
  -  **시스템 가용성**: **HA**를 선택합니다.
  -  **관리자 사용자 이름 및 관리자 암호**: 컴퓨터에 로그인하는 데 사용할 수 있는 새 사용자를 만듭니다.
  -  **서브넷 ID**: ASCS/SCS 템플릿 배포 중에 사용된 서브넷의 ID 또는 ASCS/SCS 템플릿 배포의 일부로 만든 서브넷의 ID를 입력합니다.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure Virtual Network
이 예제에서 Azure 가상 네트워크 인스턴스의 주소 공간은 10.0.0.0/16입니다. Subnet이라는 서브넷이 하나 있으며 주소 범위는 10.0.0.0/24입니다. 모든 가상 머신과 내부 부하 분산 장치는 이 가상 네트워크에 배포됩니다.

> [!IMPORTANT]
> 게스트 운영 체제 내의 네트워크 설정은 변경하지 않도록 합니다. 여기에는 IP 주소, DNS 서버 및 서브넷이 포함됩니다. Azure에서 모든 네트워크 설정을 구성합니다. DHCP(동적 호스트 구성 프로토콜) 서비스가 사용자 설정을 전파합니다.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP 주소

필요한 DNS IP 주소를 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **DNS 서버** 패널에서 가상 네트워크 **DNS 서버** 옵션이 **사용자 지정 DNS**로 설정되어 있는지 확인합니다.
2. 사용 중인 네트워크의 종류에 따라 설정을 선택합니다. 자세한 내용은 다음 리소스를 참조하세요.
   * [회사 네트워크 연결(프레미스 간)][planning-guide-2.2]: 온-프레미스 DNS 서버의 IP 주소를 추가합니다.  
   Azure에서 실행되는 가상 머신으로 온-프레미스 DNS 서버를 확장할 수 있습니다. 이 시나리오에서는 DNS 서비스를 실행하는 Azure Virtual Machines의 IP 주소를 추가할 수 있습니다.
   * Azure에서 격리된 VM 배포의 경우: DNS 서버 역할을 하는 동일한 Virtual Network 인스턴스에 추가 가상 머신을 배포합니다. DNS 서비스를 실행하도록 설정한 Azure Virtual Machines의 IP 주소를 추가합니다.

   ![그림 2: Azure Virtual Network에 대해 DNS 서버 구성][sap-ha-guide-figure-3001]

   _**그림 2:** Azure Virtual Network에 대해 DNS 서버 구성_

   > [!NOTE]
   > DNS 서버의 IP 주소를 변경하는 경우 변경 내용을 적용하고 새 DNS 서버를 전파하기 위해 Azure Virtual Machines를 다시 시작해야 합니다.
   >
   >

이 예제에서는 다음 Windows 가상 머신에서 DNS 서비스가 설치되고 구성됩니다.

| 가상 머신 역할 | 가상 머신 호스트 이름 | 네트워크 카드 이름 | 고정 IP 주소 |
| --- | --- | --- | --- |
| 첫 번째 DNS 서버 |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 두 번째 DNS 서버 |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> SAP ASCS/SCS 클러스터형 인스턴스 및 DBMS 클러스터형 인스턴스의 호스트 이름 및 고정 IP 주소

온-프레미스 배포에 대해 다음의 예약된 호스트 이름 및 IP 주소가 필요합니다.

| 가상 호스트 이름 역할 | 가상 호스트 이름 | 가상 고정 IP 주소 |
| --- | --- | --- |
| SAP ASCS/SCS 첫 번째 클러스터 가상 호스트 이름(클러스터 관리용) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS 인스턴스 가상 호스트 이름 |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS 두 번째 클러스터 가상 호스트 이름(클러스터 관리용) |pr1-dbms-vir |10.0.0.32 |

클러스터를 만들 때 만들 가상 호스트 이름 pr1-ascs-vir 및 pr1-dbms-vir 클러스터 자체를 관리하는 연결된 IP 주소를 만듭니다. 이 작업을 수행하는 방법에 대한 정보는 [클러스터 구성에서 클러스터 노드 수집][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]을 참조하세요.

DNS 서버에서 다른 두 가상 호스트 이름 pr1-ascs-sap 및 pr1-dbms-sap와 연결된 IP 주소는 수동으로 만들 수 있습니다. 클러스터형 SAP ASCS/SCS 인스턴스 및 클러스터형 DBMS 인스턴스는 이러한 리소스를 사용합니다. 이 작업을 수행하는 방법에 대한 정보는 [클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기][sap-ha-guide-9.1.1]를 참조하세요.

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP 가상 컴퓨터에 대한 고정 IP 주소 설정
클러스터에서 사용할 가상 머신을 배포한 후 모든 가상 머신에 대해 고정 IP 주소를 설정해야 합니다. 이 작업은 게스트 운영 체제가 아니라 Azure Virtual Network 구성에서 수행합니다.

1. Azure Portal에서 **리소스 그룹** > **네트워크 카드** > **설정** > **IP 주소**를 선택합니다.
2. **IP 주소** 패널의 **할당** 아래에서 **고정**을 선택합니다. **IP 주소** 상자에 사용할 IP 주소를 입력합니다.

   > [!NOTE]
   > 네트워크 카드의 IP 주소를 변경하는 경우 Azure Virtual Machines를 다시 시작하여 변경 내용을 적용해야 합니다.  
   >
   >

   ![그림 3: 각 가상 머신의 네트워크 카드에 대해 고정 IP 주소 설정][sap-ha-guide-figure-3002]

   _**그림 3:** 각 가상 머신의 네트워크 카드에 대해 고정 IP 주소 설정_

   모든 네트워크 인터페이스, 즉 Active Directory 또는 DNS 서비스에 사용하려는 가상 머신을 비롯한 모든 가상 머신에 대해 이 단계를 반복합니다.

예제에서는 다음 가상 머신 및 고정 IP 주소를 사용합니다.

| 가상 머신 역할 | 가상 머신 호스트 이름 | 네트워크 카드 이름 | 고정 IP 주소 |
| --- | --- | --- | --- |
| 첫 번째 SAP 애플리케이션 서버 인스턴스 |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| 두 번째 SAP 애플리케이션 서버 인스턴스 |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| 마지막 SAP 애플리케이션 서버 인스턴스 |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS 인스턴스의 첫 번째 클러스터 노드 |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| ASCS/SCS 인스턴스의 두 번째 클러스터 노드 |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS 인스턴스의 첫 번째 클러스터 노드 |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| DBMS 인스턴스의 두 번째 클러스터 노드 |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Azure 내부 부하 분산 장치의 고정 IP 주소 설정

SAP Azure Resource Manager 템플릿은 SAP ASCS/SCS 인스턴스 클러스터 및 DBMS 클러스터에 사용되는 Azure 내부 부하 분산 장치를 만듭니다.

> [!IMPORTANT]
> SAP ASCS/SCS의 가상 호스트 이름 IP 주소는 SAP ASCS/SCS 내부 부하 분산 장치 pr1-lb-ascs의 IP 주소와 같습니다.
> DBMS의 가상 호스트 이름 IP 주소는 DBMS 내부 부하 분산 장치 pr1-lb-dbms의 IP 주소와 같습니다.
>
>

Azure 내부 부하 분산 장치의 고정 IP 주소를 설정하려면:

1. 초기 배포는 내부 부하 분산 장치 IP 주소를 **동적**으로 설정합니다. Azure Portal에서 **IP 주소** 패널의 **할당** 아래에서 **고정**을 선택합니다.
2. 내부 부하 분산 장치 **pr1-lb-ascs**의 IP 주소를 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 IP 주소로 설정합니다.
3. 내부 부하 분산 장치 **pr1-lb-dbms**의 IP 주소를 DBMS 인스턴스의 가상 호스트 이름 IP 주소로 설정합니다.

   ![그림 4: SAP ASCS/SCS 인스턴스의 내부 부하 분산 장치에 대한 고정 IP 주소 설정][sap-ha-guide-figure-3003]

   _**그림 4:** SAP ASCS/SCS 인스턴스의 내부 부하 분산 장치에 대한 고정 IP 주소 설정_

예제에서는 다음과 같은 고정 IP 주소를 가진 두 개의 Azure 내부 부하 분산 장치가 사용됩니다.

| Azure 내부 부하 분산 장치 역할 | Azure 내부 부하 분산 장치 이름 | 고정 IP 주소 |
| --- | --- | --- |
| SAP ASCS/SCS 인스턴스 내부 부하 분산 장치 |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS 내부 부하 분산 장치 |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙

SAP Azure Resource Manager 템플릿은 다음에 대해 필요한 포트를 만듭니다.
* 기본 인스턴스 번호가 00인 ABAP ASCS 인스턴스
* 기본 인스턴스 번호가 01인 Java SCS 인스턴스

SAP ASCS/SCS 인스턴스를 설치하면 ABAP ASCS 인스턴스에 대해 기본 인스턴스 번호 00을, Java SCS 인스턴스에 대해 기본 인스턴스 번호 01을 사용해야 합니다.

다음으로 SAP NetWeaver 포트에 대한 필수 내부 부하 분산 엔드포인트를 만듭니다.

필수 내부 부하 분산 엔드포인트를 만들려면 먼저 SAP NetWeaver ABAP ASCS 포트에 대한 다음과 같은 부하 분산 엔드포인트를 만듭니다.

| 서비스/부하 분산 규칙 이름 | 기본 포트 번호 | (인스턴스 번호가 00인 ASCS 인스턴스)(ERS가 10)에 대한 구체적인 포트 |
| --- | --- | --- |
| 서버를 큐에 넣기 / *lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP 메시지 서버 / *lbrule3600* |36\<InstanceNumber\> |3600 |
| 내부 ABAP 메시지 / *lbrule3900* |39\<InstanceNumber\> |3900 |
| 메시지 서버 HTTP / *Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP 시작 서비스 ASCS HTTP / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP 시작 서비스 ASCS HTTPS / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| 복제를 큐에 넣기 / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP 시작 서비스 ERS HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP 시작 서비스 ERS HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Windows 원격 관리(WinRM) *Lbrule5985* | |5985 |
| 파일 공유 *Lbrule445* | |445 |

**표 1:** SAP NetWeaver ABAP ASCS 인스턴스의 포트 번호

그런 후 SAP NetWeaver Java SCS 포트에 대한 다음과 같은 부하 분산 엔드포인트를 만듭니다.

| 서비스/부하 분산 규칙 이름 | 기본 포트 번호 | (인스턴스 번호가 01인 SCS 인스턴스)(ERS가 11)에 대한 구체적인 포트 |
| --- | --- | --- |
| 서버를 큐에 넣기 / *lbrule3201* |32\<InstanceNumber\> |3201 |
| 게이트웨이 서버 / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Java 메시지 서버 / *lbrule3900* |39\<InstanceNumber\> |3901 |
| 메시지 서버 HTTP / *Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP 시작 서비스 SCS HTTP / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP 시작 서비스 SCS HTTPS / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| 복제를 큐에 넣기 / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP 시작 서비스 ERS HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP 시작 서비스 ERS HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| 파일 공유 *Lbrule445* | |445 |

**표 2:** SAP NetWeaver Java SCS 인스턴스의 포트 번호

![그림 5: Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙][sap-ha-guide-figure-3004]

_**그림 5:** Azure 내부 부하 분산 장치에 대한 기본 ASCS/SCS 부하 분산 규칙_

부하 분산 장치 pr1-lb-dbms의 IP 주소를 DBMS 인스턴스의 가상 호스트 이름 IP 주소로 설정합니다.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경

SAP ASCS 또는 SCS 인스턴스에 대해 다른 번호를 사용하려는 경우 해당 포트의 이름과 값을 기본 값에서 변경해야 합니다.

1. Azure Portal에서 **\<SID\>-lb-ascs 부하 분산 장치** > **부하 부산 규칙**을 선택합니다.
2. SAP ASCS 또는 SCS 인스턴스에 속하는 모든 부하 분산 규칙에 대해 다음 값을 변경합니다.

   * name
   * 포트
   * 백 엔드 포트

   예를 들어 기본 ASCS 인스턴스 번호를 00에서 31로 변경하려는 경우 표 1에 나열된 모든 포트에 대해 이러한 변경을 수행해야 합니다.

   다음은 포트 *lbrule3200*에 대한 업데이트 예제입니다.

   ![그림 6: Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경][sap-ha-guide-figure-3005]

   _**그림 6:** Azure 내부 부하 분산 장치에 대한 ASCS/SCS 기본 부하 분산 규칙 변경_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 도메인에 Windows 가상 컴퓨터 추가

가상 머신에 고정 IP 주소를 할당한 후 가상 머신을 도메인에 추가합니다.

![그림 7: 도메인에 가상 머신 추가][sap-ha-guide-figure-3006]

_**그림 7:** 도메인에 가상 머신 추가_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS 인스턴스의 클러스터 노드 둘 다에 대한 레지스트리 항목 추가

Azure Load Balancer에는 설정된 시간(유휴 제한 시간) 동안 연결이 유휴 상태일 때 연결을 닫는 내부 부하 분산 장치가 있습니다. 대화 상자 인스턴스의 SAP 작업 프로세스는 첫 번째 큐에 넣지/큐에서 제거 요청이 전송되는 즉시 SAP 큐에 넣기 프로세스에 대한 연결을 엽니다. 이러한 연결은 일반적으로 작업 프로세스 또는 큐에 넣기 프로세스가 다시 시작될 때까지 설정 상태를 유지합니다. 그러나 연결이 설정 기간 동안 유휴 상태이면 Azure 내부 부하 분산 장치는 연결을 닫습니다. 그렇지만 연결이 더 이상 없는 경우 SAP 작업 프로세스는 큐에 넣기 프로세스에 대한 연결을 다시 설정하기 때문에 문제가 되지 않습니다. 이러한 활동은 SAP 프로세스의 개발자 추적에 설명되어 있지만 해당 추적에 많은 양의 추가 콘텐츠가 생성됩니다. 따라서 두 클러스터 노드에서 TCP/IP `KeepAliveTime` 및 `KeepAliveInterval`을 변경하는 것이 좋습니다. 이 문서 뒷부분에 설명된 것처럼 TCP/IP 매개 변수의 이러한 변경 내용을 SAP 프로필 매개 변수와 결합합니다.

SAP ASCS/SCS 인스턴스의 두 클러스터 노드에 대해 레지스트리 항목을 추가하려면 먼저 SAP ASCS/SCS에 대한 두 Windows 클러스터 노드에 대해 다음 Windows 레지스트리 항목을 추가합니다.

| path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 변수 이름 |`KeepAliveTime` |
| 변수 유형 |REG_DWORD(10진수) |
| 값 |120000 |
| 설명서 링크 |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**표 3:** 첫 번째 TCP/IP 매개 변수 변경

그런 다음 SAP ASCS/SCS에 대한 두 Windows 클러스터 노드에 대해 다음 Windows 레지스트리 항목을 추가합니다.

| path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 변수 이름 |`KeepAliveInterval` |
| 변수 유형 |REG_DWORD(10진수) |
| 값 |120000 |
| 설명서 링크 |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**표 4:** 두 번째 TCP/IP 매개 변수 변경

변경 내용을 적용하려면 두 클러스터 노드를 모두 다시 시작합니다.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치(Failover) 클러스터 설정

SAP ASCS/SCS 인스턴스의 Windows Server 장애 조치(Failover) 클러스터 설정은 다음과 같은 작업을 포함합니다.

- 클러스터 구성에서 클러스터 노드 수집하기.
- 클러스터 파일 공유 감시 구성하기.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> 클러스터 구성에서 클러스터 노드 수집

1. 역할 및 기능 추가 마법사에서 장애 조치 클러스터링을 두 클러스터 노드에 추가합니다.
2. 장애 조치 클러스터 관리자를 사용하여 장애 조치 클러스터를 설정합니다. 장애 조치 클러스터 관리자에서 **클러스터 만들기**를 선택하고 첫 번째 클러스터(노드 A) 이름만 추가합니다. 두 번째 노드는 아직 추가하지 마세요. 이후 단계에서 두 번째 노드를 추가합니다.

   ![그림 8: 첫 번째 클러스터 노드의 서버 또는 가상 머신 이름 추가][sap-ha-guide-figure-3007]

   _**그림 8:** 첫 번째 클러스터 노드의 서버 또는 가상 머신 이름 추가_

3. 클러스터의 네트워크 이름(가상 호스트 이름)을 입력합니다.

   ![그림 9: 클러스터 이름 입력][sap-ha-guide-figure-3008]

   _**그림 9:** 클러스터 이름 입력_

4. 클러스터를 만든 후에 클러스터 유효성 검사 테스트를 실행합니다.

   ![그림 10: 클러스터 유효성 검사 실행][sap-ha-guide-figure-3009]

   _**그림 10:** 클러스터 유효성 검사 실행_

   프로세스의 이 시점에 표시되는 디스크에 대한 경고는 무시해도 됩니다. 파일 공유 감시 및 SIOS 공유 디스크는 나중에 추가합니다. 이 단계에서는 쿼럼이 없어도 걱정할 필요가 없습니다.

   ![그림 11: 쿼럼 디스크가 없음][sap-ha-guide-figure-3010]

   _**그림 11:** 쿼럼 디스크가 없음_

   ![그림 12: 새 IP 주소가 필요한 코어 클러스터 리소스][sap-ha-guide-figure-3011]

   _**그림 12:** 새 IP 주소가 필요한 코어 클러스터 리소스_

5. 핵심 클러스터 서비스의 IP 주소를 변경합니다. 서버의 IP 주소가 가상 머신 노드 중 하나를 가리키므로 핵심 클러스터 서비스의 IP 주소를 변경할 때까지 클러스터를 시작할 수 없습니다. 핵심 클러스터 서비스의 IP 리소스에 대한 **속성** 페이지에서 이 작업을 수행합니다.

   예를 들어 클러스터 가상 호스트 이름 pr1-ascs-vir에 대한 IP 주소(이 예제에서 10.0.0.42)를 할당해야 합니다.

   ![그림 13: 속성 대화 상자에서 IP 주소 변경][sap-ha-guide-figure-3012]

   _**그림 13:** **속성** 대화 상자에서 IP 주소 변경_

   ![그림 14: 클러스터에 예약된 IP 주소 할당][sap-ha-guide-figure-3013]

   _**그림 14:** 클러스터에 예약된 IP 주소 할당_

6. 클러스터 가상 호스트 이름을 온라인으로 가져옵니다.

   ![그림 15: 올바른 IP 주소로 작동되는 클러스터 코어 서비스][sap-ha-guide-figure-3014]

   _**그림 15:** 올바른 IP 주소로 작동되는 클러스터 코어 서비스_

7. 두 번째 클러스터 노드를 추가합니다.

   핵심 클러스터 서비스가 작동 및 실행되고 있으므로 두 번째 클러스터 노드를 추가할 수 있습니다.

   ![그림 16 두 번째 클러스터 노드 추가][sap-ha-guide-figure-3015]

   _**그림 16:** 두 번째 클러스터 노드 추가_

8. 두 번째 클러스터 노드 호스트의 이름을 입력합니다.

   ![그림 17: 두 번째 클러스터 노드 호스트 이름 입력][sap-ha-guide-figure-3016]

   _**그림 17:** 두 번째 클러스터 노드 호스트 이름 입력_

   > [!IMPORTANT]
   > **클러스터에 사용할 수 있는 모든 Storage를 추가하세요.** 확인란을 선택하지 *않았는지* 확인합니다.  
   >
   >

   ![그림 18: 확인란 선택 안 함][sap-ha-guide-figure-3017]

   _**그림 18:** 확인란 선택 *안 함*_

   쿼럼 및 디스크에 대한 경고는 무시해도 됩니다. [SAP ASCS/SCS 클러스터 공유 디스크용 SIOS DataKeeper Cluster Edition 설치][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]에서 설명한 대로 쿼럼을 설정하고 나중에 디스크를 공유합니다.

   ![그림 19: 디스크 쿼럼에 대한 경고 무시][sap-ha-guide-figure-3018]

   _**그림 19:** 디스크 쿼럼에 대한 경고 무시_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> 클러스터 파일 공유 감시 구성

클러스터 파일 공유 감시 구성은 다음과 같은 작업을 포함합니다.

- 파일 공유 만들기.
- 장애 조치(Failover) 클러스터 관리자에서 파일 공유 감시 쿼럼 설정하기.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> 파일 공유 만들기

1. 쿼럼 디스크 대신 파일 공유 감시를 선택합니다. SIOS DataKeeper는 이 옵션을 지원합니다.

   이 문서의 예제에서 파일 공유 감시는 Azure에서 실행되는 Active Directory 또는 DNS 서버에 있습니다. 파일 공유 감시를 domcontr-0이라고 합니다. Azure에 대해 VPN 연결을 구성했으므로(VPN Gateway 또는 Azure ExpressRoute를 통해) Active Directory 또는 DNS 서비스는 온-프레미스이며 파일 공유 감시를 실행하는 데 적합하지 않습니다.

   > [!NOTE]
   > Active Directory 또는 DNS 서비스는 온-프레미스로만 실행되므로 온-프레미스로 실행되는 Active Directory 또는 DNS Windows 운영 체제에서 파일 공유 감시를 구성하지 마세요. Azure 및 Active Directory 또는 DNS 온-프레미스로 실행되는 클러스터 노드 간 네트워크 대기 시간이 너무 커서 연결 문제를 일으킬 수 있습니다. 클러스터 노드와 가깝게 실행되는 Azure Virtual Machines에서 파일 공유 감시를 구성해야 합니다.  
   >
   >

   쿼럼 드라이브에는 1,024MB 이상의 여유 공간이 필요합니다. 쿼럼 드라이브에 2,048MB의 여유 공간을 사용하는 것이 좋습니다.

2. 클러스터 이름 개체를 추가합니다.

   ![그림 20: 클러스터 이름 개체의 공유를 위한 권한 할당][sap-ha-guide-figure-3019]

   _**그림 20:** 클러스터 이름 개체의 공유를 위한 권한 할당_

   클러스터 이름 개체(예제의 pr1-ascs-vir$)에 대한 공유에서 데이터를 변경할 수 있는 권한이 포함되는지 확인합니다.

3. 목록에 클러스터 이름 개체를 추가하려면 **추가**를 선택합니다. 그림 22에서 보여 주는 방법 외에도 필터를 변경하여 컴퓨터 개체를 확인합니다.

   ![그림 21: 컴퓨터를 포함하도록 개체 형식 변경][sap-ha-guide-figure-3020]

   _**그림 21:** 컴퓨터를 포함하도록 **개체 형식** 변경_

   ![그림 22: 컴퓨터 확인란 선택][sap-ha-guide-figure-3021]

   _**그림 22:** **컴퓨터** 확인란 선택_

4. 그림 21과 같이 클러스터 이름 개체를 입력합니다. 레코드가 이미 만들어졌으므로 그림 20과 같이 권한을 변경할 수 있습니다.

5. 공유의 **보안** 탭을 선택한 후 클러스터 이름 개체에 대한 좀 더 자세한 사용 권한을 설정합니다.

   ![그림 23: 파일 공유 쿼럼의 클러스터 이름 개체에 대한 보안 특성 설정][sap-ha-guide-figure-3022]

   _**그림 23:** 파일 공유 쿼럼의 클러스터 이름 개체에 대한 보안 특성 설정_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> 장애 조치 클러스터 관리자에서 파일 공유 감시 쿼럼 설정

1. 쿼럼 설정 구성 마법사를 엽니다.

   ![그림 24: 클러스터 쿼럼 설정 구성 마법사 시작][sap-ha-guide-figure-3023]

   _**그림 24:** 클러스터 쿼럼 설정 구성 마법사 시작_

2. **쿼럼 구성 옵션 선택** 페이지에서 **쿼럼 감시 선택**을 선택합니다.

   ![그림 25: 선택할 수 있는 쿼럼 구성][sap-ha-guide-figure-3024]

   _**그림 25:** 선택할 수 있는 쿼럼 구성_

3. **쿼럼 감시 선택** 페이지에서 **파일 공유 감시 구성**을 선택합니다.

   ![그림 26: 파일 공유 감시 선택][sap-ha-guide-figure-3025]

   _**그림 26:** 파일 공유 감시 선택_

4. 파일 공유에 대한 UNC 경로를 입력합니다(예제의 \\domcontr-0\FSW). 변경할 수 있는 목록을 표시하려면 **다음**을 선택합니다.

   ![그림 27: 공유 감시를 위한 파일 공유 위치 정의][sap-ha-guide-figure-3026]

   _**그림 27:** 공유 감시를 위한 파일 공유 위치 정의_

5. 원하는 변경 내용을 선택하고 **다음**을 선택합니다. 그림 28과 같이 클러스터 구성을 성공적으로 다시 구성해야 합니다.  

   ![그림 28: 클러스터를 다시 구성했는지 확인][sap-ha-guide-figure-3027]

   _**그림 28:** 클러스터를 다시 구성했는지 확인_

Windows 장애 조치(Failover) 클러스터를 성공적으로 설치한 다음에는 장애 조치(Failover) 검색이 Azure의 조건에 맞게 조정되도록 일부 임계값을 변경해야 합니다. 변경할 매개 변수는 [장애 조치(failover) 클러스터 네트워크 임계값 조정][tuning-failover-cluster-network-thresholds]에 설명되어 있습니다. ASCS/SCS의 Windows 클러스터 구성에 포함된 2개의 VM이 동일한 서브넷에 있다는 전제하에, 매개 변수 값을 다음과 같이 변경합니다.

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

이러한 설정은 고객과 함께 테스트되어 도출된 합리적인 타협안입니다. 충분한 복원력을 갖고 있으며, SAP 소프트웨어에서 실제 오류가 발생했거나 노드 또는 VM에 장애가 발생한 경우 일정 정도로 빠른 장애 조치(Failover)를 제공합니다.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치

이제 Azure에서 Windows Server 장애 조치 클러스터링 구성이 완료되었습니다. SAP ASCS/SCS 인스턴스를 설치하려면 공유 디스크 리소스가 필요합니다. Azure에서는 필요한 공유 디스크 리소스를 만들 수 없습니다. SIOS DataKeeper Cluster Edition은 공유 디스크 리소스를 만드는 데 사용할 수 있는 타사 솔루션입니다.

SAP ASCS/SCS 클러스터 공유 디스크에 대한 SIOS DataKeeper Cluster Edition 설치는 다음과 같은 작업을 포함합니다.

- Microsoft .NET Framework 3.5 추가하기.
- SIOS DataKeeper 설치하기.
- SIOS DataKeeper 설정하기.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> .NET Framework 3.5 추가하기
.NET Framework 3.5는 Windows Server 2012 R2에서 자동으로 활성화되거나 설치되지 않습니다. SIOS DataKeeper는 DataKeeper를 설치하는 모든 노드에 .NET을 필요로 하므로 클러스터의 모든 가상 머신의 게스트 운영 체제에 .NET Framework 3.5를 설치해야 합니다.

.NET Framework 3.5를 추가하는 방법에는 두 가지 있습니다.

- 그림 29와 같이 Windows에서 역할 및 기능 추가 마법사를 사용합니다.

  ![그림 29: 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5 설치][sap-ha-guide-figure-3028]

  _**그림 29:** 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5 설치_

  ![그림 30: 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5를 설치할 때의 설치 진행률 표시줄][sap-ha-guide-figure-3029]

  _**그림 30:** 역할 및 기능 추가 마법사를 사용하여 .NET Framework 3.5를 설치할 때의 설치 진행률 표시줄_

- dism.exe 명령줄 도구를 사용합니다. 이 유형의 설치에서는 Windows 설치 미디어의 SxS 디렉터리에 액세스할 수 있어야 합니다. 관리자 권한 명령 프롬프트에 아래의 명령을 입력합니다.

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS DataKeeper 설치

클러스터의 각 노드에 SIOS DataKeeper Cluster Edition을 설치합니다. SIOS DataKeeper를 사용하여 가상 공유 저장소를 만들려면 동기화된 미러를 만든 후 클러스터 공유 저장소를 시뮬레이션합니다.

SIOS 소프트웨어를 설치하기 전에 DataKeeperSvc 도메인 사용자를 만듭니다.

> [!NOTE]
> DataKeeperSvc 도메인 사용자를 두 클러스터 노드의 로컬 관리자 그룹에 추가합니다.
>
>

SIOS DataKeeper를 설치하려면:

1. 두 클러스터 노드에서 SIOS 소프트웨어를 설치합니다.

   ![SIOS 설치 관리자][sap-ha-guide-figure-3030]

   ![그림 31: SIOS DataKeeper 설치의 첫 번째 페이지][sap-ha-guide-figure-3031]

   _**그림 31:** SIOS DataKeeper 설치의 첫 번째 페이지_

2. 대화 상자에서 **예**를 선택합니다.

   ![그림 32: 서비스를 사용할 수 없다고 알리는 DataKeeper][sap-ha-guide-figure-3032]

   _**그림 32:** 서비스를 사용할 수 없다고 알리는 DataKeeper_

3. 대화 상자에서 **도메인 또는 서버 계정**을 선택하는 것이 좋습니다.

   ![그림 33: SIOS DataKeeper에 대한 사용자 선택][sap-ha-guide-figure-3033]

   _**그림 33:** SIOS DataKeeper에 대한 사용자 선택_

4. SIOS DataKeeper에 대해 만든 도메인 계정 사용자 이름 및 암호를 입력합니다.

   ![그림 34: SIOS DataKeeper 설치를 위한 도메인 사용자 이름 및 암호 입력][sap-ha-guide-figure-3034]

   _**그림 34:** SIOS DataKeeper 설치를 위한 도메인 사용자 이름 및 암호 입력_

5. 그림 35와 같이 SIOS DataKeeper 인스턴스를 위한 라이선스 키를 설치합니다.

   ![그림 35: SIOS DataKeeper 라이선스 키 입력][sap-ha-guide-figure-3035]

   _**그림 35:** SIOS DataKeeper 라이선스 키 입력_

6. 메시지가 표시되면 가상 머신을 다시 시작합니다.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper 설정

두 노드에 SIOS DataKeeper를 설치한 후 구성을 시작합니다. 이러한 구성의 목표는 각 가상 머신에 연결된 추가 디스크 간에 동기식으로 데이터를 복제하는 것입니다.

1. DataKeeper 관리 및 구성 도구를 시작한 다음 **서버 연결**을 선택합니다.

   ![그림 36: SIOS DataKeeper 관리 및 구성 도구][sap-ha-guide-figure-3036]

   _**그림 36:** SIOS DataKeeper 관리 및 구성 도구_

2. 관리 및 구성 도구에서 연결해야 하는 첫 번째 노드의 이름 또는 TCP/IP 주소를 삽입한 후 다음 단계에서 두 번째 노드에 대해 동일한 작업을 수행합니다.

   ![그림 37: 관리 및 구성 도구에서 연결해야 하는 첫 번째 노드의 이름 또는 TCP/IP 주소를 삽입한 후 다음 단계에서 두 번째 노드에 대해 동일한 작업 수행][sap-ha-guide-figure-3037]

   _**그림 37:** 관리 및 구성 도구에서 연결해야 하는 첫 번째 노드의 이름 또는 TCP/IP 주소를 삽입한 후 다음 단계에서 두 번째 노드에 대해 동일한 작업 수행_

3. 두 노드 간에 복제 작업을 만듭니다.

   ![그림 38: 복제 작업 만들기][sap-ha-guide-figure-3038]

   _**그림 38:** 복제 작업 만들기_

   마법사에서 복제 작업을 만드는 과정을 안내합니다.

4. 복제 작업의 이름을 정의합니다.

   ![그림 39: 복제 작업 이름 정의][sap-ha-guide-figure-3039]

   _**그림 39:** 복제 작업 이름 정의_

   ![그림 40: 현재 원본 노드에 해당하는 노드의 기본 데이터 정의][sap-ha-guide-figure-3040]

   _**그림 40:** 현재 원본 노드에 해당하는 노드의 기본 데이터 정의_

5. 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨을 정의합니다.

   ![그림 41: 현재 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨 정의][sap-ha-guide-figure-3041]

   _**그림 41:** 현재 대상 노드의 이름, TCP/IP 주소 및 디스크 볼륨 정의_

6. 압축 알고리즘을 정의합니다. 예제에서는 복제 스트림을 압축하는 것이 좋습니다. 특히 재동기화 상황에서는 복제 스트림을 압축하면 재동기화 시간을 크게 단축할 수 있습니다. 압축에는 가상 머신의 CPU 및 RAM 리소스가 사용됩니다. 압축 속도가 증가하면 사용되는 CPU 리소스 양도 증가합니다. 나중에 이 설정을 조정할 수 있습니다.

7. 확인해야 하는 또 다른 설정은 복제가 비동기식 또는 동기식 중에서 어떤 방식으로 발생하는가 입니다. SAP ASCS/SCS 구성을 보호할 때 동기 복제를 사용해야 합니다.  

   ![그림 42: 복제 세부 정보 정의][sap-ha-guide-figure-3042]

   _**그림 42:** 복제 세부 정보 정의_

8. 복제 작업에 의해 복제되는 볼륨을 Windows Server 장애 조치(Failover) 클러스터 구성에 공유 디스크로 나타낼지 여부를 정의합니다. SAP ASCS/SCS 구성의 경우 Windows 클러스터가 복제된 볼륨을 클러스터 볼륨으로 사용할 수 있는 공유 디스크로 인식하도록 **예**를 선택합니다.

   ![그림 43: 예를 선택하여 복제된 볼륨을 클러스터 볼륨으로 설정][sap-ha-guide-figure-3043]

   _**그림 43:** **예**를 선택하여 복제된 볼륨을 클러스터 볼륨으로 설정_

   볼륨을 만든 후 DataKeeper 관리 및 구성 도구에서 복제 작업 활성화되어 있음을 보여 줍니다.

   ![그림 44: SAP ASCS/SCS 공유 디스크에 대해 활성화된 DataKeeper 동기식 미러링][sap-ha-guide-figure-3044]

   _**그림 44:** SAP ASCS/SCS 공유 디스크에 대해 활성화된 DataKeeper 동기식 미러링_

   이제 그림 45와 같이 장애 조치 클러스터 관리자에서 디스크를 DataKeeper 디스크로 표시합니다.

   ![그림 45: 장애 조치(Failover) 클러스터 관리자에서 표시하는 DataKeeper에서 복제한 디스크][sap-ha-guide-figure-3045]

   _**그림 45:** 장애 조치(Failover) 클러스터 관리자에서 표시하는 DataKeeper에서 복제한 디스크_

## <a name="next-steps"></a>다음 단계

* [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스용 SAP NetWeaver HA 설치][sap-high-availability-installation-wsfc-shared-disk]

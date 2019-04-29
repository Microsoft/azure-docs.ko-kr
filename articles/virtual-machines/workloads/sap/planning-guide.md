---
title: SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현 | Microsoft 문서
description: SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/05/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 097b5e8ee69d945e0a9e24ba1c62b0ae82dd896b
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62732420"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure를 사용하여 회사에서는 긴 조달 주기를 거치지 않고도 최단 시간에 계산 및 스토리지 리소스를 가져올 수 있습니다. 회사에서는 Azure Virtual Machines 서비스를 사용하여 SAP NetWeaver 기반 애플리케이션과 같은 클래식 애플리케이션을 Azure에 배포하고 온-프레미스에서 사용할 수 있는 추가 리소스를 확보하지 않고도 안정성과 가용성을 확장할 수 있습니다. 또한 Azure Virtual Machines 서비스는 프레미스 간 연결도 지원하므로 기업에서 Azure Virtual Machines를 온-프레미스 도메인, 사설 클라우드 및 SAP 시스템 지형에 적극적으로 통합할 수 있습니다.
이 백서에서는 Microsoft Azure Virtual Machine의 기본 사항에 대해 설명하고 Azure의 SAP NetWeaver 설치에 대한 계획 연습 과정 및 구현 고려 사항을 제공하므로 Azure에서 SAP NetWeaver의 실제 배포를 시작하기 전에 이 문서를 읽으면 도움이 됩니다.
이 문서는 지정된 플랫폼에서 SAP 소프트웨어 설치 및 배포에 대한 기본 리소스를 나타내는 SAP 설치 설명서 및 SAP 정보를 보완합니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>요약
클라우드 컴퓨팅은 중소 기업에서 대기업 및 다국적 기업까지 IT 업계 내에서 점점 더 중요해지는 널리 사용되는 용어입니다.

Microsoft Azure는 다양한 새로운 가능성을 제공하는 Microsoft의 Cloud Services 플랫폼입니다. 이제 고객은 클라우드의 서비스로 애플리케이션을 신속하게 프로비전 및 프로비전 해제할 수 있으므로 기술 또는 예산 제한에 제한되지 않습니다. 하드웨어 인프라에 시간과 예산을 투자하는 대신 기업은 고객 및 사용자를 위한 애플리케이션, 비즈니스 프로세스 및 그 이점에 집중할 수 있습니다.

Microsoft Azure Virtual Machine 서비스와 함께 Microsoft는 포괄적인 IaaS(Infrastructure as a Service) 플랫폼을 제공합니다. SAP NetWeaver 기반 애플리케이션은 Azure Virtual Machines(IaaS)에서 지원됩니다. 이 백서는 플랫폼으로 선택한 Microsoft Azure 내에서 SAP NetWeaver 기반 애플리케이션을 계획하고 구현하는 방법에 대해 설명합니다.

또한 다음 두 가지 주요 측면을 집중적으로 설명합니다.

* 첫 번째 부분에서는 Azure의 SAP NetWeaver 기반 애플리케이션에 대해 지원되는 두 가지 배포 패턴을 설명합니다. 또한 SAP 배포를 고려한 Azure의 일반적인 사용에 대해서도 설명합니다.
* 두 번째 부분에서는 첫 번째 부분에서 설명하는 두 가지 다른 시나리오의 구현에 대해 자세히 설명합니다.

추가 리소스는 이 문서의 [리소스][planning-guide-1.2] 장을 참조하세요.

### <a name="definitions-upfront"></a>사전 정의
이 문서 전체에서는 다음과 같은 용어를 사용합니다.

* IaaS: 서비스 제공 인프라(Infrastructure as a Service)
* PaaS: 서비스로서의 플랫폼
* SaaS: 서비스 제공 소프트웨어(Software as a Service)
* SAP 구성 요소: ECC, BW, 솔루션 관리자 또는 S/4HANA 등의 개별 SAP 애플리케이션.  SAP 구성 요소는 기존의 ABAP 또는 Java 기술을 기반으로 하거나 비즈니스 개체와 같은 비NetWeaver 기반 애플리케이션을 기반으로 사용할 수 있습니다.
* SAP 환경: 하나 이상의 SAP 구성 요소가 논리적으로 그룹화되어 개발, QAS, 교육, DR 또는 프로덕션과 같은 비즈니스 기능을 수행합니다.
* SAP 자산: 고객의 IT 자산 중 SAP 자산 전체를 의미합니다. SAP 지형에는 모든 프로덕션 및 비프로덕션 환경이 포함됩니다.
* SAP 시스템: SAP ERP 개발 시스템, SAP BW 테스트 시스템, SAP CRM 프로덕션 시스템 등의 애플리케이션 계층과 DBMS 계층의 조합입니다. Azure 배포에서는 온-프레미스와 Azure 간에 이러한 두 계층을 나눌 수 없습니다. 즉, SAP 시스템은 온-프레미스에 배포되거나 Azure에 배포됨을 의미합니다. 그러나 Azure 또는 온-프레미스에는 SAP 지형의 서로 다른 시스템을 배포할 수 있습니다. 예를 들어 Azure에는 SAP CRM 개발 및 테스트 시스템을 배포할 수 있지만 온-프레미스에는 SAP CRM 프로덕션 시스템을 배포할 수 있습니다.
* 크로스-프레미스 또는 하이브리드: VM이 온-프레미스 데이터 센터와 Azure 간에 사이트-사이트, 다중 사이트 또는 ExpressRoute 방식으로 연결되는 Azure 구독에 배포되는 시나리오를 설명합니다. 공통 Azure 설명서에서 이러한 종류의 배포를 크로스-프레미스 또는 하이브리드 시나리오라고도 합니다. 연결하는 이유는 온-프레미스 도메인, 온-프레미스 Active Directory/OpenLDAP 및 온-프레미스 DNS를 Azure로 확장하기 위한 것입니다. 온-프레미스 배경은 구독의 Azure 자산으로 확장됩니다. 이렇게 확장된 VM은 온-프레미스 도메인에 속할 수 있습니다. 온-프레미스 도메인의 도메인 사용자는 서버에 액세스하고 이러한 VM에서 서비스(예: DBMS 서비스)를 실행할 수 있습니다. 온-프레미스에 배포된 VM과 Azure에 배포된 VM 간의 통신 및 이름 확인이 가능합니다. 이는 SAP 자산을 Azure에 배포하는 가장 일반적이며 거의 독점적인 사례입니다. 자세한 내용은 [이 문서][vpn-gateway-cross-premises-options] 및 [이 문서][vpn-gateway-site-to-site-create]를 참조하세요.

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 SAP 시스템을 실행 중인 Azure Virtual Machines가 온-프레미스 도메인의 멤버인 SAP 시스템의 크로스-프레미스 또는 하이브리드 배포가 지원됩니다. 일부 또는 전체 SAP 환경을 Azure로 배포하는 데 크로스-프레미스 또는 하이브리드 구성이 지원됩니다. Azure에서 전체 SAP 지형을 실행하려고 해도 이러한 VM이 온-프레미스 도메인 및 ADS/OpenLDAP에 포함되어야 합니다. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>리소스
Azure 설명서에서 SAP 워크로드에 대한 진입점은 [여기](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)에 나옵니다. 이 진입점으로 시작하면 다음 토픽을 다루는 문서가 많이 있습니다.

- Azure의 SAP NetWeaver 및 Business One
- Azure의 다양한 DBMS 시스템에 대한 SAP DBMS 가이드
- Azure의 SAP 워크로드에 대한 고가용성 및 재해 복구
- Azure에서 SAP HANA를 실행하기 위한 구체적 지침
- Azure의 SAP HANA(대규모 인스턴스) 관련 지침 


> [!IMPORTANT]
> 가능한 경우 참조하는 SAP 설치 가이드 또는 다른 SAP 설명서에 연결하는 링크가 사용됩니다(참조 InstGuide-01, <http://service.sap.com/instguides> 참조). 특정 SAP 기능전제 조건, 설치 프로세스 또는 세부 정보의 경우 Microsoft는 Microsoft Azure Virtual Machine에 설치되어 운영되는 특정 작업만 다루므로, 언제나 SAP 설명서 및 가이드를 꼼꼼히 읽는 것이 좋습니다.
>
>

다음 SAP 정보는 Azure의 SAP 항목과 관련이 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure의 SAP 애플리케이션: 지원되는 제품 및 크기 |
| [2015553] |Microsoft Azure의 SAP: 필수 구성 요소 지원 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [1409604] |Windows에서의 가상화: 고급 모니터링 |
| [2191498] |Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [1984787] |SUSE LINUX Enterprise Server 12: 설치 참고 사항 |
| [2002167] |Red Hat Enterprise Linux 7.x: 설치 및 업그레이드 |
| [2069760] |Oracle Linux 7.x SAP 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |

Linux용 SAP 정보를 모두 포함하는 [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)도 확인해 보세요.

Azure 구독의 일반적인 기본 제한 및 최대 제한은 [이 문서][azure-subscription-service-limits-subscription]에 있습니다.

## <a name="possible-scenarios"></a>가능한 시나리오
SAP는 기업 내에서 중요 업무용 애플리케이션 중 하나로 보는 경우가 많습니다. 이러한 애플리케이션의 아키텍처 및 작업은 대부분 복잡하며, 가용성 및 성능에 대한 요구를 충족하는 것이 매우 중요합니다.

따라서 기업에서는 선택한 클라우드 공급자와는 별개로, 공용 클라우드 환경에서 실행할 수 있는 애플리케이션을 신중히 고려해야 합니다.

공용 클라우드 내에서 SAP NetWeaver 기반 애플리케이션을 배포하기 위한 가능한 시스템 유형은 아래와 같습니다.

1. 중간 규모의 프로덕션 시스템
2. 개발 시스템
3. 테스트 시스템
4. 프로토타입 시스템
5. 학습/데모 시스템

Azure IaaS 또는 일반적인 IaaS에 SAP 시스템을 성공적으로 배포하려면 전형적인 외주 업체나 호스팅 서비스 공급자 및 IaaS 제품 간의 주요 차이점을 이해해야 합니다. 기존 호스팅 서비스 공급자 또는 외주 업체는 고객이 호스트하려는 작업에 맞게 인프라(네트워크, 저장소 및 서버 유형)를 조정하지만 IaaS 배포에 적합한 작업을 선택하는 것은 고객의 책임입니다.

첫 번째 단계로, 고객은 다음 항목을 확인해야 합니다.

* SAP에서 지원하는 Azure의 VM 유형
* Azure의 SAP 지원 제품/릴리스
* Azure의 특정 SAP 릴리스에 대해 지원되는 OS 및 DBMS 릴리스
* 여러 다른 Azure SKU에서 제공하는 SAP 처리량

이러한 질문에 대한 답변은 SAP 정보 [1928533]에서 읽을 수 있습니다.

두 번째 단계로, Azure 리소스 및 대역폭 제한을 온-프레미스 시스템의 실제 리소스 사용량과 비교해야 합니다. 따라서 고객은 다음 영역에서 SAP가 지원하는 Azure 유형의 다양한 기능에 익숙해져야 합니다.

* 여러 VM 유형의 CPU 및 메모리 리소스
* 여러 VM 유형의 IOPS 대역폭
* 여러 VM 유형의 네트워크 기능.

대부분의 데이터는 [여기(Linux)][virtual-machines-sizes-linux]와 [여기(Windows)][virtual-machines-sizes-windows]에 있습니다.

위의 링크에 나와 있는 제한은 상한입니다. 모든 리소스에 대해 제한이 있는 것은 아닙니다. 예를 들어 IOPS는 모든 상황에서 제공될 수 있습니다. 선택한 VM 유형의 CPU 및 메모리 리소스는 예외입니다. SAP에서 지원하는 VM 유형의 경우 CPU 및 메모리 리소스가 예약되므로 VM 내에서 언제든지 사용 가능합니다.

다른 IaaS 플랫폼과 같은 Microsoft Azure Platform은 다중 테넌트 플랫폼입니다. 결과적으로 저장소, 네트워크 및 기타 리소스가 테넌트 간에 공유됩니다. 한 테넌트가 다른 테넌트(시끄러운 이웃)의 성능에 심각하게 영향을 주지 못하게 하기 위해 지능적 제한 및 할당량 논리가 사용됩니다. Azure의 논리는 대역폭에서 비교적 작은 변화를 경험하게 하려는 것이지만 공유가 많이 되는 플랫폼의 경우 고객이 온-프레미스 배포에서 익숙해진 것보다 리소스/대역폭 가용성에서 더 큰 차이를 나타내는 경향이 있습니다. 결과적으로 네트워킹 또는 저장소 I/O(대기 시간 및 볼륨)와 관련해서 분당 대역폭 수준이 다른 것을 경험할 수 있습니다. Azure의 SAP 시스템이 온-프레미스 시스템의 경우보다 더 큰 차이를 경험할 가능성을 고려해야 합니다.

마지막 단계는 가용성 요구 사항을 평가하는 것입니다. 기본 Azure 인프라를 업데이트해야 하고 VM이 실행되는 호스트를 재부팅해야 하는 경우가 있을 수 있습니다. 이러한 경우 해당 호스트에서 실행되는 VM을 종료한 후 다시 시작할 수 있습니다. 이러한 유지 관리 시간은 특정 지역의 경우 핵심적인 업무 시간이 아니지만 다시 시작이 진행되는 몇 시간의 기간은 비교적 긴 시간입니다. 이러한 업데이트의 영향 중 일부 또는 전체를 완화하도록 Azure 플랫폼 내의 다양한 기술을 구성할 수 있습니다. Azure 플랫폼, DBMS 및 SAP 애플리케이션은 앞으로 이러한 다시 시작이 미치는 영향을 최소화하도록 개선될 것입니다.

Azure에 SAP 시스템을 성공적으로 배포하려면 온-프레미스 SAP 시스템 운영 체제, 데이터베이스 및 SAP 애플리케이션이 SAP Azure 지원 매트릭스에 표시되고, Azure 인프라가 제공할 수 있고 가용성 SLA Microsoft Azure 제공 서비스에서 작동될 수 있는 리소스에 잘 맞아야 합니다. 이러한 시스템이 파악되면 다음과 같은 두 가지 배포 시나리오 중 하나를 결정해야 합니다.



### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>크로스 프레미스 - 온-프레미스 네트워크에 완전히 통합될 필요가 있는 단일 또는 다중 SAP VM을 Azure에 배포
![사이트 간 연결이 있는 VPN(크로스 프레미스)][planning-guide-figure-300]

이 시나리오는 가능한 여러 배포 패턴을 포함하는 크로스-프레미스 시나리오입니다. 온-프레미스에서 SAP 지형의 일부를 실행하고 SAP 지형의 다른 부분을 Azure에서 실행하는 것으로 설명할 수 있습니다. SAP 구성 요소 중 일부가 Azure에서 실행된다는 사실의 모든 측면이 최종 사용자에게 투명하게 공개되어야 합니다. 따라서 SAP Transport Correction System(STMS), RFC 통신, 인쇄, 보안(예: SSO) 등은 Azure에서 실행되는 SAP 시스템에서 원활하게 작동합니다. 그러나 크로스-프레미스 시나리오도 고객의 도메인 및 DNS가 Azure로 확장된 상태로 Azure에서 전체 SAP 지형이 실행되는 시나리오를 설명합니다.

> [!NOTE]
> 이것은 생산성이 뛰어난 SAP 시스템을 실행하기 위해 지원되는 배포 시나리오입니다.
>
>

온-프레미스 네트워크를 Microsoft Azure에 연결하는 방법에 대한 자세한 내용은 [이 문서][vpn-gateway-create-site-to-site-rm-powershell]를 참조하세요.

> [!IMPORTANT]
> Azure 및 온-프레미스 고객 배포 간의 크로스-프레미스 시나리오에 대해 논의할 경우 전체 SAP 시스템의 세분성을 고려하게 됩니다. 크로스-프레미스 시나리오에서 *지원되지 않는* 시나리오는 다음과 같습니다.
>
> * 배포 방법마다 SAP 애플리케이션의 다른 계층을 실행합니다. 예를 들어 온-프레미스에서는 DBMS 계층을 실행하지만 Azure VM으로 배포된 VM에서는 SAP 애플리케이션 계층을 실행하거나 그 반대로 실행할 수 있습니다.
> * SAP 계층 구성 요소 일부는 Azure에 있고, 일부는 온-프레미스에 있습니다. 온-프레미스와 Azure VM 간에 SAP 애플리케이션 계층의 인스턴스를 분할하는 경우를 예로 들 수 있습니다.
> * 단일 시스템의 SAP 인스턴스를 실행하는 VM을 여러 Azure 영역에 배포하는 것은 지원되지 않습니다.
>
> 이러한 제한이 적용되는 이유는 단일 SAP 시스템 내에서, 특히 SAP 시스템의 애플리케이션 인스턴스와 DBMS 계층 사이에서 대기 시간이 짧은 고성능 네트워크를 보장하기 위해서입니다.
>
> 시스템 및 영역에 대 한 특수 계획 밀접 하 게 통합 되는 여러 SAP 시스템을 사용 하는 경우 발생 해야 합니다. 네트워크 대기 시간을 최소화 하기 위해 서로를 최대한 가깝게 이러한 시스템을 배포 해야 합니다. 고도로 통합 된 SAP 시스템의 예입니다.
> * SAP BW ERP, CRM, SRM; SAP OLTP 시스템에서 데이터 읽기 또는
> * SAP 시스템과 SAP 이외의; 간에 또는 여러 SPA 시스템 간에 데이터를 복제 하는 데 사용 되는 SAP SLT 또는
> * SAP S/4는 SAP ERP 시스템에 연결 등


### <a name="supported-os-and-database-releases"></a>지원되는 OS 및 데이터베이스 릴리스
* Azure Virtual Machines 서비스에 대해 지원되는 Microsoft 서버 소프트웨어는 이 문서에 나열되어 있습니다. <https://support.microsoft.com/kb/2721672>
* 지원되는 운영 체제 릴리스, SAP 소프트웨어와 연계해서 Azure Virtual Machine 서비스에서 지원되는 데이터베이스 시스템 릴리스는 SAP 정보 [1928533]에 설명되어 있습니다.
* Azure Virtual Machine 서비스에서 지원되는 SAP 애플리케이션 및 릴리스는 SAP 정보 [1928533]에 설명되어 있습니다.
* SAP용 Azure 시나리오에서는 64비트 이미지만 게스트 VM으로 실행될 수 있습니다. 결과적으로 64비트 SAP 애플리케이션 및 데이터베이스만 지원됩니다.

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure Virtual Machine 서비스
Microsoft Azure Platform은 Microsoft 데이터 센터에서 호스트되고 작동되는 인터넷 규모 클라우드 서비스 플랫폼입니다. 이 플랫폼에는 Microsoft Azure Virtual Machine Services(Infrastructure as a Service 또는 IaaS) 및 풍부한 PaaS(Platform as a Service) 기능 집합이 포함되어 있습니다.

Azure 플랫폼을 사용하면 사전에 기술 및 인프라를 구입할 필요가 줄어듭니다. 이 플랫폼은 웹 애플리케이션 및 연결된 애플리케이션을 호스트, 확장 및 관리하기 위한 주문형 컴퓨팅 및 저장소를 제공하여 애플리케이션 유지 관리 및 운영을 간소화합니다. 인프라 관리는 사용 요구에 맞는 종량제 가격 모델 옵션을 사용하도록 하여 고가용성 및 동적 확장을 충족하도록 설계된 플랫폼으로 자동화됩니다.

![Microsoft Azure Virtual Machine 서비스 위치 지정][planning-guide-figure-400]

Azure Virtual Machine 서비스를 사용하여 사용자 지정 서버 이미지를 Azure에 IaaS 인스턴스로 배포할 수 있습니다(그림 4 참조). Azure의 Virtual Machines는 Hyper-V VHD(가상 하드 드라이브)를 기준으로 하며 다른 운영 체제를 게스트 OS로 실행할 수 있습니다.

운영 측면에서 Azure Virtual Machine 서비스는 온-프레미스에 배포된 가상 머신과 유사한 환경을 제공합니다. 그러나 인프라를 조달, 운영 및 관리할 필요가 없다는 중요한 이점이 있습니다. 개발자와 관리자는 이러한 가상 머신 내의 운영 체제 이미지에 대해 모든 권한을 가집니다. 관리자는 이러한 가상 머신에 원격으로 로그인하여 소프트웨어 배포 작업뿐 아니라 유지 관리 및 문제 해결 작업을 수행할 수 있습니다. 배포 측면에서는 Azure VM의 크기 및 기능만 제한됩니다. 배포는 온-프레미스에서 수행될 수 있으므로 이러한 크기가 구성에서 그리 세밀한 부분이 아닐 수 있습니다. 다음을 조합한 다양한 VM 유형을 선택할 수 있습니다.

* vCPU 수
* 메모리
* 연결할 수 있는 VHD 수
* 네트워크 및 저장소 대역폭

제공되는 다양한 가상 머신의 크기 및 크기 제한은 [이 문서(Linux)][virtual-machines-sizes-linux]와 [이 문서(Windows)][virtual-machines-sizes-windows]의 표에서 확인할 수 있습니다.

각 Azure 지역에 모두 다른 VM 시리즈가 제공되는 것은 아닐 수 있습니다(Azure 지역에 대해서는 다음 장 참조). 또한 일부 VM 또는 VM 시리즈만 SAP용으로 인증되어 있습니다.

> [!IMPORTANT]
> SAP NetWeaver 기반 애플리케이션을 사용하는 경우 SAP 정보 [1928533] 에 나열된 VM 형식 및 구성 일부만 지원됩니다.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure 지역
Virtual Machines가 소위 *Azure 지역*으로 배포됩니다. Azure 지역은 지리적으로 근접한 하나 또는 여러 개의 데이터 센터일 수 있습니다. 전 세계의 지정학적 지역 대부분에 대해 Microsoft는 2개 이상의 Azure 지역을 유지합니다. 유럽의 경우 *North Europe* 및 *West Europe*이라는 Azure Region이 있습니다. 이러한 지정학적 지역 한 곳 내의 두 Azure 지역은 충분히 먼 거리로 구분되어 있으므로 자연 또는 기술적 재해가 같은 지정학적 지역의 두 Azure 지역에 영향을 주지 않습니다. Microsoft는 꾸준히 전 세계의 여러 다른 지정학적 지역에 새로운 Azure 지역을 구축하고 있으므로 이러한 지역의 수가 꾸준히 늘어나고 있으며 2015년 12월을 기준으로 이미 발표된 추가 지역을 포함하여 20개의 Azure 지역이 구축되었습니다. 고객은 중국의 두 Azure 지역을 포함하는 이러한 모든 지역에 SAP 시스템을 배포할 수 있습니다. Azure 지역에 대한 최신 정보를 보려면 <https://azure.microsoft.com/regions/> 웹 사이트를 참조하세요.

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Microsoft Azure Virtual Machine 개념
Microsoft Azure는 온-프레미스 가상화 솔루션과 비슷한 기능을 갖는 Virtual Machines를 호스트하기 위해 IaaS(Infrastructure as a Service) 솔루션을 제공합니다. Azure Portal, PowerShell 또는 CLI 내에서도 배포 및 관리 기능을 제공하는 Virtual Machines를 만들 수 있습니다.

Azure Resource Manager를 사용하면 선언적 템플릿을 통해 애플리케이션을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 애플리케이션 수명 주기의 각 단계 중에 애플리케이션을 반복해서 배포합니다.

Resource Manager 템플릿 사용에 대한 자세한 내용은 다음 항목에서 찾을 수 있습니다.

* [Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 머신 배포 및 관리](../../linux/create-ssh-secured-vm-from-template.md)
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 머신 관리][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

또 다른 흥미로운 기능은 Virtual Machines에서 이미지를 만들 수 있다는 것입니다. 이를 통해 요구 사항을 충족하는 Virtual Machines 인스턴스를 빠르게 배포할 수 있는 특정 리포지토리를 준비할 수 있습니다.

Virtual Machines에서 이미지를 만드는 방법에 대한 자세한 내용은 [이 문서(Linux)][virtual-machines-linux-capture-image-resource-manager] 및 [이 문서(Windows)][virtual-machines-windows-capture-image-resource-manager]에 있습니다.

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>장애 도메인
장애 도메인은 데이터 센터에 포함된 물리적 인프라와 밀접하게 관련되어 있는 실패의 물리적 단위를 나타내며, 물리적 블레이드나 랙이 장애 도메인으로 간주될 수 있는 경우 둘 간의 직접적인 일대일 매핑은 없습니다.

여러 Virtual Machines를 Microsoft Azure Virtual Machines 서비스에서 단일 SAP 시스템의 일부로 배포할 경우 Azure 패브릭 컨트롤러에 영향을 주어 애플리케이션을 여러 다른 장애 도메인으로 배포할 수 있으므로 Microsoft Azure SLA의 요구 사항을 충족할 수 있습니다. 그러나 Azure 배율 단위(수백 개의 Compute 노드 또는 Storage 노드 및 네트워킹 컬렉션)에 장애 도메인을 배포하거나 특정 장애 도메인에 VM을 할당하는 것은 직접 제어할 수 없는 영역입니다. Azure 패브릭 컨트롤러가 여러 다른 장애 도메인에 VM 집합을 배포하도록 하려면 배포 시에 Azure 가용성 집합을 하는 VM에 할당해야 합니다. Azure 가용성 집합에 대한 자세한 내용은 이 문서의 [Azure 가용성 집합][planning-guide-3.2.3] 장을 참조하세요.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>업그레이드 도메인
업그레이드 도메인은 여러 VM에서 실행되는 SAP 인스턴스를 구성하는 SAP 시스템 내의 VM이 업데이트되는 방식을 결정하는 데 도움이 되는 논리적 단위를 나타냅니다. 업그레이드가 수행될 때 Microsoft Azure는 이러한 업그레이드 도메인을 하나씩 업데이트합니다. 배포 시에 여러 업그레이드 도메인에 VM을 분산하여 잠재적인 가동 중지 시간으로부터 SAP 시스템을 일부 보호할 수 있습니다. 강제로 Azure가 여러 업그레이드 도메인에 SAP 시스템의 VM을 분산 배포하게 하려면 각 VM의 배포 시에 특정 특성을 설정해야 합니다. 장애 도메인과 마찬가지로 Azure 배율 단위는 여러 업그레이드 도메인으로 나뉩니다. Azure 패브릭 컨트롤러가 여러 다른 업그레이드 도메인에 VM 집합을 배포하도록 하려면 배포 시에 Azure 가용성 집합을 하는 VM에 할당해야 합니다. Azure 가용성 집합에 대한 자세한 내용은 아래의 [Azure 가용성 집합][planning-guide-3.2.3] 장을 참조하세요.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure 가용성 집합
단일 Azure 가용성 집합 내의 Azure Virtual Machines는 Azure 패브릭 컨트롤러에 의해 여러 장애 도메인 및 업그레이드 도메인에 배포됩니다. 여러 장애 도메인과 업그레이드 도메인에 배포하는 목적은 단일 장애 도메인 내에서 인프라 유지 관리를 수행하거나 오류가 발생하는 경우 SAP 시스템의 모든 VM이 종료되지 않도록 하기 위해서입니다. 기본적으로 VM은 가용성 집합에 속하지 않습니다. 가용성 집합에 VM을 포함하는 작업은 배포 시에 또는 VM의 재구성이나 재배포에 의해 나중에 정의합니다.

Azure 가용성 집합의 개념 및 가용성 집합이 장애 도메인 및 업그레이드 도메인과 관련되는 방식을 이해하려면 [이 문서][virtual-machines-manage-availability]를 참조하세요.

json 템플릿을 통해 Azure Resource Manager에 대한 가용성 집합을 정의하려면 [rest-api 사양](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json)을 참조하고 "가용성"을 검색합니다.

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>스토리지: Microsoft Azure Storage 및 데이터 디스크
Microsoft Azure Virtual Machines는 다양한 저장소 유형을 활용합니다. Azure Virtual Machine 서비스에서 SAP를 구현할 때는 다음 두 가지 주요 저장소 유형 간의 차이점을 이해하는 것이 중요합니다.

* 비영구 휘발성 저장소
* 영구 저장소

VM이 배포되면 Azure VM에서 비영구 디스크를 제공합니다. VM을 다시 부팅하면 해당 드라이브의 모든 콘텐츠가 초기화됩니다. 따라서 데이터베이스의 데이터 파일과 로그/다시 실행 파일은 어떤 경우에도 이러한 비영구 드라이브에 배치하면 안됩니다. 이러한 비영구 드라이브가 tempdb 및 temp 테이블 공간에 적합할 수 있는 일부 데이터베이스에는 예외가 있을 수 있습니다. 그러나 이러한 비영구 드라이브는 해당 VM 제품군의 처리량으로 제한되므로 A 시리즈 VM에는 해당 드라이브를 사용하지 마세요. 자세한 내용은 [Azure에서 Windows VM의 임시 드라이브 이해](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) 문서를 참조하세요.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Azure VM의 드라이브 D:\는 Azure 계산 노드의 일부 로컬 디스크에서 지원하는 비지속형 드라이브입니다. 비지속형이기 때문에 VM을 다시 부팅하면 D:\ 드라이브의 변경 내용이 손실됩니다. 즉 저장된 파일, 만들어진 디렉터리, 설치된 애플리케이션 등과 같은 "모든 변경 내용"이 손실됩니다.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM은 Azure 계산 노드의 로컬 디스크에서 지원하는 비지속형 드라이브의 /mnt/resource에 자동으로 탑재됩니다. 비지속형이기 때문에 VM을 다시 부팅하면 /mnt/resource의 변경 내용이 손실됩니다. 즉 저장된 파일, 만들어진 디렉터리, 설치된 애플리케이션 등과 같은 모든 변경 내용이 손실됩니다.
> 
> 

- - -

Microsoft Azure Storage는 영구 스토리지와 SAN 스토리지에서 확인되는 일반적인 수준의 보호 및 중복성을 제공합니다. Azure Storage를 기준으로 하는 디스크는 Azure Storage 서비스에 있는 VHD(가상 하드 디스크)입니다. 로컬 OS 디스크(Windows C:\, Linux/dev/sda1)는 Azure Storage에 저장되고, VM에 탑재된 추가 볼륨/디스크도 여기에 저장됩니다.

온-프레미스에서 기존 VHD를 업로드하거나 Azure 내에서 빈 VHD를 만든 후에 배포된 VM에 연결할 수 있습니다.

VHD를 만들거나 Azure Storage에 업로드한 후 기존 Virtual Machine에 탑재하고 연결하고 기존(분리된) VHD를 복사할 수 있습니다.

해당 VHD는 지속되므로 해당 VHD 내의 데이터 및 변경 내용은 재부팅한 후 Virtual Machine 인스턴스를 다시 만들어도 안전하게 유지됩니다. 인스턴스를 삭제하는 경우에도 이러한 VHD가 안전하게 유지되며 재배포될 수 있으며, OS가 아닌 디스크의 경우에도 다른 VM에 탑재할 수 있습니다.

Azure Storage에 관한 자세한 내용은 다음 항목에서 찾을 수 있습니다.

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure Standard Storage
Azure Standard Storage는 Azure IaaS가 출시되었을 때 사용할 수 있던 스토리지의 유형입니다. 이 저장소에서는 IOPS 할당량이 단일 디스크 단위로 적용되었습니다. 대기 시간도 온-프레미스에 호스트된 고성능 SAP 시스템에 대해 일반적으로 배포되는 SAN/NAS 디바이스와 동일한 수준이 아니었습니다. 그럼에도 불구하고 Azure Standard Storage는 Azure에 배포되는 수백 가지 SAP 시스템에 충분한 것으로 입증되었습니다.

Azure Standard Storage 계정에 저장되는 데이터는 저장된 실제 데이터, 스토리지 트랜잭션 볼륨, 아웃바운드 데이터 전송 및 선택한 중복성 옵션에 따라 비용이 청구됩니다. 많은 디스크를 최대 1TB의 크기로 만들 수 있지만 비어 있는 경우에는 무료입니다. VHD를 각각 100GB로 채우면 VHD가 만들어질 때의 보통 크기가 아니라 100GB를 저장하기 위한 비용이 청구됩니다.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Premium Storage
Azure Premium Storage는 다음을 제공하는 것을 목표로 도입되었습니다.

* 더 나은 I/O 대기 시간
* 향상된 처리량
* 변동이 작은 I/O 대기 시간

이를 위해 여러 가지 사항이 변경되었으며, 그 중에서 가장 중요한 두 가지 변경 사항은 다음과 같습니다.

* Azure Storage 노드에 SSD 디스크 사용
* Azure 계산 노드의 로컬 SSD에서 지원하는 새 캐시 읽기

해당 기능이 디스크(또는 VHD)의 크기에 따라 달라지지 않는 Standard Storage와 달리, Premium Storage에는 <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/> 문서에 나와 있는 각기 다른 세 가지 디스크 범주가 있습니다.

디스크별 IOPS 및 디스크 처리량은 디스크의 크기 범주에 따라 달라집니다.

Premium Storage의 경우 이러한 디스크에 저장된 실제 데이터 볼륨이 아니라 디스크 내에 저장된 데이터 양과는 관계없는 이러한 디스크의 크기 범주에 따라 비용이 부과됩니다.

표시된 크기 범주에 직접 매핑되지 않는 디스크를 Premium Storage에 만들 수도 있습니다. 특히 Standard Storage의 디스크를 Premium Storage에 복사하는 경우가 여기에 해당됩니다. 이러한 경우에는 다음으로 가장 큰 Premium Storage 디스크 옵션으로 매핑됩니다.

SAP에서 인증된 Azure VM 제품군의 대부분은 Premium Storage 및 또는 Azure 표준 및 Premium Storage 간의 혼합과 함께 사용할 수 있습니다.

[이 문서(Linux)][virtual-machines-sizes-linux]와 [이 문서(Windows)][virtual-machines-sizes-windows]에서 DS 시리즈 VM 부분을 확인하면 VM 수준의 세분성에 따라 Premium Storage 디스크에 대한 데이터 볼륨 제한이 있음을 알 수 있습니다. 다른 DS 시리즈 또는 GS 시리즈 VM에도 탑재할 수 있는 데이터 디스크 수와 관련해서 다른 제한 사항이 있습니다. 이러한 제한도 위에 언급된 문서에 설명되어 있습니다. 하지만 기본적으로 P30 디스크 32개를 단일 DS14 VM에 탑재한다고 해도 P30 디스크 최대 처리량의 32배를 얻을 수는 없습니다. 대신 이 문서에 설명된 것처럼 VM 수준의 최대 처리량에 따라 데이터 처리량이 제한됩니다.

Premium Storage에 관한 자세한 내용은 다음 항목에서 찾을 수 있습니다. <https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure Storage 계정

Azure에서 서비스 또는 VM을 배포할 때 VHD 및 VM 이미지의 배포는 Azure Storage 계정이라는 단위로 구성할 수 있습니다. Azure 배포를 계획할 때는 Azure의 제한 사항을 신중히 고려해야 합니다. 한쪽 측면에서는 Azure 구독당 Storage 계정 수가 제한되어 있습니다. 각 Azure Storage 계정은 많은 수의 VHD 파일을 보유할 수 있지만 Storage 계정당 총 IOPS 수의 한도는 고정되어 있습니다. DBMS 시스템에 수백 개의 SAP VM을 배포하여 많은 I/O 호출을 발생하는 경우 여러 Azure Storage 계정 간에 높은 IOPS DBMS VM을 배포하는 것이 좋습니다. 구독당 적용되는 Azure Storage 계정의 현재 제한을 초과하지 않도록 주의해야 합니다. 저장소는 SAP 시스템의 데이터베이스 배포에서 중요한 부분이므로 이 개념은 이미 언급한 [DBMS 배포 가이드][dbms-guide]에서 더 자세히 설명합니다.

Azure Storage 계정에 대한 자세한 내용은 [이 문서][storage-scalability-targets]에 있습니다. 이 문서를 읽으면 Azure Standard Storage 계정과 Premium Storage 계정에서 제한 사항이 서로 다르다는 것을 알게 될 것입니다. 주요 차이점은 이러한 Storage 계정 내에 저장할 수 있는 데이터의 볼륨입니다. Standard Storage에서 이 볼륨은 Premium Storage를 사용할 때보다 훨씬 더 큽니다. 그 밖에도 Standard Storage 계정은 IOPS에서 크게 제한되지만(**총 요청 속도** 열 참조) Azure Premium Storage 계정에는 이러한 제한이 없습니다. SAP 시스템, 특히 DBMS 서버의 배포를 설명할 때 이러한 차이점의 세부 정보 및 결과를 살펴보겠습니다.

Storage 계정 내에서는 여러 다른 VHD를 구성하고 분류하기 위해 다른 컨테이너를 만들 수 있습니다. 이러한 컨테이너는 다른 VM의 VHD를 분리하는 등의 용도로 사용됩니다. 단일 Azure Storage 계정 아래에서 컨테이너를 하나만 사용하거나 여러 개를 사용할 경우 성능상의 문제는 없습니다.

Azure 내에서 VHD 이름은 Azure 내에서 VHD에 대해 고유한 이름을 제공해야 하는 다음과 같은 명명 관계를 따릅니다.

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

위의 문자열은 Azure Storage에 저장된 VHD를 고유하게 식별해야 합니다.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Managed Disks

Managed Disks는 Azure Storage 계정에 저장된 VHD 대신 사용할 수 있는 Azure Resource Manager의 새로운 리소스 종류입니다. Managed Disks는 연결되어 있는 가상 머신의 가용성 집합에 맞게 자동으로 조정되므로, 가상 머신에서 실행되는 서비스와 가상 머신의 가용성을 높여 줍니다. 자세한 내용은 [개요 문서](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)를 참조하세요.

관리 디스크를 사용하면 가상 머신을 간편하고 배포 및 관리할 수 있으므로 사용하는 것이 좋습니다.
SAP는 현재 지원 프리미엄 관리 디스크만 지원합니다. 자세한 내용은 SAP Note [1928533]을 참조하세요.

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage 복원력

Microsoft Azure Storage는 최소 3개의 별도 스토리지 노드에 기본 VHD(OS 포함) 및 연결된 디스크 또는 Blob을 저장합니다. 이 사실을 LRS(로컬 중복 저장소)라고 합니다. LRS는 Azure의 모든 저장소 유형에 대한 기본값입니다. 

[ 가지 더 많은 중복 방법이 있으며, Azure Storage 복제](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)에서 모두 설명하고 있습니다.

> [!NOTE]
>데이터베이스 및 로그/다시 실행 파일을 저장하는 DBMS VM 및 디스크에 권장되는 스토리지 유형인 Azure Premium Storage에서는 LRS만 사용할 수 있습니다. 결과적으로 데이터베이스 데이터를 다른 Azure 지역 또는 다른 Azure 가용성 영역으로 복제할 수 있게 하려면 SQL Server Always On, Oracle Data Guard 또는 HANA System Replication과 같은 데이터베이스 메서드를 구성해야 합니다.


> [!NOTE]
> DBMS 배포의 경우 Azure Standard Storage에서 사용할 수 있는 지역 중복 스토리지의 사용은 성능에 심각한 영향을 미치고 VM에 연결된 여러 VHD에서 쓰기 순서를 따르지 않으므로 권장되지 않습니다. 여러 VHD에서 쓰기 순서를 따르지 않으면, 데이터베이스 및 로그/다시 실행 파일이 원본 VM 쪽의 여러 VHD에 분산되어 있는 경우(대부분의 경우) 복제 대상 쪽에는 일관성 없는 데이터베이스로 끝날 가능성이 높습니다.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure 네트워킹

Microsoft Azure는 구현하려는 모든 시나리오의 SAP 소프트웨어 매핑을 가능하게 하는 네트워크 인프라를 제공합니다. 해당 기능은 다음과 같습니다.

* 외부에서 Windows 터미널 서비스 또는 ssh/VNC를 통해 VM에 직접 액세스
* VM 내의 애플리케이션에서 사용되는 서비스 및 특정 포트에 액세스
* Azure VM으로 배포된 VM 그룹 간의 내부 통신 및 이름 확인
* 고객의 온-프레미스 네트워크와 Azure 네트워크 간의 크로스-프레미스 연결
* Azure 지역 간 연결 또는 Azure 사이트 간 데이터 센터 연결

자세한 내용은 <https://azure.microsoft.com/documentation/services/virtual-network/>를 참조하세요.

Azure에서 다양한 방식으로 이름 및 IP 확인을 구성할 수 있습니다. 사용자 고유의 DNS 서버를 설정하는 대신 사용할 수 있는 Azure DNS 서비스도 있습니다. 자세한 내용은 [이 문서][virtual-networks-manage-dns-in-vnet] 및 [이 페이지](https://azure.microsoft.com/services/dns/)에 있습니다.

크로스-프레미스 또는 하이브리드 시나리오의 경우 온-프레미스 AD/OpenLDAP/DNS를 VPN 또는 개인 연결을 통해 Azure로 확장했다는 사실에 의존합니다. 여기에 설명된 특정 시나리오에서는 Azure에 AD/OpenLDAP 복제본을 설치해야 할 수 있습니다.

네트워킹과 이름 확인은 SAP 시스템의 데이터베이스 배포에서 중요한 부분이므로 이 개념은 [DBMS 배포 가이드 ][dbms-guide]에서 더 자세히 설명합니다.

##### <a name="azure-virtual-networks"></a>Azure Virtual Networks

Azure Virtual Network를 구축하여 Azure DHCP 기능에 의해 할당된 개인 IP 주소의 주소 범위를 정의할 수 있습니다. 크로스-프레미스 시나리오에서 정의된 IP 주소 범위는 여전히 DHCP를 사용하여 Azure에서 할당됩니다. 그러나 도메인 이름 확인은 온-프레미스에서 수행되므로(VM을 온-프레미스 도메인의 일부로 가정) 다른 Azure Cloud Services 이외의 주소를 확인할 수 있습니다.

Azure의 모든 Virtual Machine을 Virtual Network에 연결해야 합니다.

자세한 내용은 [이 문서][resource-groups-networking] 및 [이 페이지](https://azure.microsoft.com/documentation/services/virtual-network/)에 있습니다.


> [!NOTE]
> 기본적으로 VM이 배포되면 Virtual Network 구성을 변경할 수 없습니다. TCP/IP 설정은 Azure DHCP 서버에 유지되어야 합니다. 기본 동작은 동적 IP 할당입니다.
>
>

가상 네트워크 카드의 MAC 주소는 변경될 수 있으며(예: 크기 조정) 이 경우 Windows 또는 Linux 게스트 OS는 새 네트워크 카드를 선택한 후 자동으로 DHCP를 사용하여 IP 및 DNS 주소를 할당합니다.

##### <a name="static-ip-assignment"></a>고정 IP 할당
Azure Virtual Network 내의 VM에 고정 또는 예약된 IP 주소를 할당할 수 있습니다. Azure Virtual Network의 VM을 실행하면 일부 시나리오에 필요한 경우 이 기능이 사용될 확률이 높아집니다. IP 할당은 VM이 실행 중인지 또는 종료되었는지에 관계없이 VM이 존재하는 동안 계속 유효합니다. 결과적으로 Virtual Network의 IP 주소 범위를 정의할 때 VM의 전체 수(실행 중인 VM 및 중지된 VM)를 고려해야 합니다. IP 주소는 VM 및 해당 네트워크 인터페이스가 삭제되거나 IP 주소가 다시 할당 취소될 때까지 할당된 상태로 유지됩니다. 자세한 내용은 [이 문서][virtual-networks-static-private-ip-arm-pportal]를 참조하세요.

> [!NOTE]
> Azure 평균을 통해 고정 IP 주소를 개별 vNIC에 할당해야 합니다. 게스트 OS 내에서 고정 IP 주소를 vNIC에 할당하지 않아야 합니다. Azure Backup 서비스와 같은 일부 Azure 서비스는 최소한 기본 vNIC가 고정 IP 주소가 아닌 DHCP로 설정된다는 사실에 의존합니다. [Azure 가상 머신 백업 문제 해결](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking) 문서도 참조하세요.
>
>

##### <a name="multiple-nics-per-vm"></a>VM당 여러 NIC

Azure Virtual Machine에 대해 여러 개의 vNIC(가상 네트워크 인터페이스 카드)를 정의할 수 있습니다. 여러 vNIC를 정의할 수 있으면 클라이언트 트래픽이 한 vNIC를 통해 라우팅되고 백 엔드 트래픽이 또 다른 vNIC를 통해 라우팅되는 네트워크 트래픽 분리를 설정할 수 있습니다. VM의 유형에 따라, vNIC의 수와 관련해서 다른 제한이 적용될 수 있습니다. 정확한 세부 정보, 기능 및 제한 사항은 다음 문서에서 찾을 수 있습니다.

* [여러 NIC가 있는 Windows VM 만들기][virtual-networks-multiple-nics-windows]
* [여러 NIC가 있는 Linux VM 만들기][virtual-networks-multiple-nics-linux]
* [템플릿을 사용하여 다중 NIC VM 배포][virtual-network-deploy-multinic-arm-template]
* [PowerShell을 사용하여 다중 NIC VM 배포][virtual-network-deploy-multinic-arm-ps]
* [Azure CLI를 사용하여 다중 NIC VM 배포][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>사이트 간 연결

크로스-프레미스에서는 투명한 영구 VPN 연결을 사용하여 Azure VM과 온-프레미스가 연결되어 있습니다. 이것이 Azure에서 가장 일반적인 SAP 배포 패턴이 될 것입니다. 그렇지만 Azure의 SAP 인스턴스에 대한 운영 절차 및 프로세스가 투명하게 진행되어야 합니다. 즉, 이러한 시스템에서 결과를 출력하고 SAP TMS(전송 관리 시스템)를 사용하여 Azure의 개발 시스템 변경 내용을 온-프레미스에 배포되는 테스트 시스템으로 전송할 수 있어야 합니다. 사이트 간 연결에 대한 추가 설명서는 [이 문서][vpn-gateway-create-site-to-site-rm-powershell]에 있습니다.

##### <a name="vpn-tunnel-device"></a>VPN 터널 디바이스

사이트 간 연결(온-프레미스 데이터에서 Azure 데이터 센터로의 연결)을 만들려면 VPN 디바이스를 얻은 후 구성하거나 Windows Server 2012에 소프트웨어 구성 요소로 도입된 RRAS(라우팅 및 원격 액세스 서비스)를 사용해야 합니다.

* [PowerShell을 사용하여 사이트 간 VPN 연결로 가상 네트워크 만들기][vpn-gateway-create-site-to-site-rm-powershell]
* [사이트 간 VPN Gateway 연결을 위한 VPN 디바이스 정보][vpn-gateway-about-vpn-devices]
* [VPN Gateway FAQ][vpn-gateway-vpn-faq]

![온-프레미스와 Azure 간의 사이트 간 연결][planning-guide-figure-600]

위의 그림에서는 Azure의 Virtual Network에서 사용하도록 예약된 IP 주소 하위 범위가 두 개의 Azure 구독에 있는 경우를 보여 줍니다. 온-프레미스 네트워크에서 Azure로의 연결은 VPN을 통해 설정됩니다.

#### <a name="point-to-site-vpn"></a>지점 및 사이트 간 VPN

지점 및 사이트 간 VPN에서는 모든 클라이언트 컴퓨터가 자체 VPN을 통해 Azure에 연결되어야 합니다. 우리가 보고 있는 SAP 시나리오의 경우 지점 및 사이트 간 연결이 가능하지 않습니다. 따라서 지점 및 사이트 간 VPN 연결에 대한 추가 참조는 제공되지 않습니다.

자세한 내용은 다음 항목에 있습니다.
* [Azure Portal을 사용하여 VNet에 지점 및 사이트 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [PowerShell을 사용하여 VNet에 지점 및 사이트 간 연결 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>다중 사이트 VPN

현재 Azure는 단일 Azure 구독에 대해 다중 사이트 VPN 연결을 만들 수 있도록 합니다. 이전에는 구독이 1개 있으면 사이트 간 VPN 연결 1개로 제한되었습니다. 이 제한은 단일 구독에 대한 다중 사이트 VPN 연결을 통해 해결되었습니다. 따라서 크로스-프레미스 구성을 통해 특정 구독에 대해 둘 이상의 Azure 지역을 사용할 수 있습니다.

추가 설명서는 [이 문서][vpn-gateway-create-site-to-site-rm-powershell]를 참조하세요.

#### <a name="vnet-to-vnet-connection"></a>VNet 간 연결

다중 사이트 VPN을 사용할 경우 각 지역에 별도의 Azure Virtual Network를 구성해야 합니다. 그러나 다른 지역의 소프트웨어 구성 요소가 서로 통신해야 하는 경우가 많습니다. 이상적으로 이러한 통신은 한 Azure 지역에서 온-프레미스로 라우팅되었다가 다시 여기서 다른 Azure 지역으로 라우팅되면 안 됩니다. 간단히 말해서 Azure는 한 지역의 Azure Virtual Network에서 다른 지역의 다른 Azure Virtual Network로의 연결을 구성할 수 있도록 합니다. 이 기능을 VNet 간 연결이라고 합니다. 이 기능에 대한 자세한 내용은 <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>를 참조하세요.

#### <a name="private-connection-to-azure-expressroute"></a>Azure ExpressRoute에 대한 비공개 연결

Microsoft Azure ExpressRoute를 사용하여 Azure 데이터 센터와 고객의 온-프레미스 인프라 또는 공동 배치 환경 간에 개인 연결을 만들 수 있습니다. 다양한 MPLS(패킷 전환) VPN 공급자 또는 기타 네트워크 서비스 공급자에서 ExpressRoute를 제공합니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. ExpressRoute 연결은 인터넷을 통한 일반 연결보다 더 높은 보안을 제공하고 여러 병렬 회로를 통해 안정성을 높이며 더 빠른 속도와 짧은 대기 시간을 제공합니다.

Azure ExpressRoute 및 제공 서비스에 대한 자세한 내용은 여기에서 찾을 수 있습니다.

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

ExpressRoute는 여기에 설명된 것처럼 단일 ExpressRoute 회로를 통해 여러 Azure 구독을 가능하게 합니다.

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>크로스 프레미스 강제 터널링
사이트 간, 지점 및 사이트 간 또는 ExpressRoute를 통해 온-프레미스 도메인에 가입된 VM의 경우 모든 사용자를 위해 인터넷 프록시 설정이 배포되는지 확인해야 합니다. 기본적으로 해당 VM에서 실행되는 소프트웨어나 브라우저를 사용하여 인터넷에 액세스하는 사용자는 회사 프록시를 거치지 않으며 Azure를 통해 인터넷에 바로 연결됩니다. 하지만 프록시를 확인하는 것은 소프트웨어 및 서비스의 책임이므로 프록시 설정만으로 트래픽이 회사 프록시를 통과해서 전송되도록 할 수 있는 것은 아닙니다. VM에서 실행되는 소프트웨어가 이러한 작업을 수행하지 않으며 관리자가 설정을 조작하므로 인터넷에 대한 트래픽은 다시 Azure에서 인터넷으로 직접 우회될 수 있습니다.

이러한 직접 인터넷 연결을 방지하기 위해 온-프레미스와 Azure 사이에 사이트 간 연결이 사용되는 강제 터널링을 구성할 수 있습니다. 강제 터널링 기능에 대한 자세한 설명은 <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>에 게시된 내용을 참조하세요.

ExpressRoute를 사용한 강제 터널링은 ExpressRoute BGP 피어링 세션을 통해 기본 경로를 보급한 고객으로 활성화됩니다.

#### <a name="summary-of-azure-networking"></a>Azure 네트워킹 요약

이 장에서는 Azure 네트워킹에 대한 여러 가지 중요 사항이 포함되어 있습니다. 중요 사항은 다음과 같이 요약할 수 있습니다.

* Azure Virtual Network를 사용하면 Azure 배포에 네트워크 구조를 넣을 수 있습니다. VNet은 서로 격리되거나 VNet 간의 네트워크 보안 그룹 트래픽의 도움으로 제어될 수 있습니다.
* Azure Virtual Network를 활용하여 VM에 IP 주소 범위를 할당하거나 VM에 고정 IP 주소 할당 가능
* 사이트 간 또는 지점 및 사이트 간 연결을 설정하려면 먼저 Azure Virtual Network를 만들어야 함
* 가상 컴퓨터를 배포한 후에는 VM에 할당된 Virtual Network를 더 이상 변경할 수 없음

### <a name="quotas-in-azure-virtual-machine-services"></a>Azure Virtual Machine 서비스의 할당량
저장소 및 네트워크 인프라가 Azure 인프라에서 다양한 서비스를 실행하는 VM 간에 공유된다는 사실을 명확히할 필요가 있습니다. 또한 고객 소유의 데이터 센터와 마찬가지로 일부 인프라 리소스가 과도하게 프로비전되는 경우도 발생하고 있습니다. Microsoft Azure Platform은 디스크, CPU, 네트워크 및 기타 할당량을 사용하여 리소스 소비를 제한하며 일관되고 명확한 성능을 유지합니다.  VM 유형(A5, A6, 등)마다 디스크 수, CPU, RAM, 네트워크에 대해 각기 다른 할당량을 갖습니다.

> [!NOTE]
> SAP에서 지원하는 VM 유형의 CPU 및 메모리 리소스는 호스트 노드에서 미리 할당됩니다. 즉, VM을 배포한 후 VM 유형에 따라 정의된 대로 호스트의 리소스를 사용할 수 있게 됩니다.
>
>

Azure 솔루션에 대해 SAP를 계획하고 크기를 조정할 때는 각 가상 머신 크기에 대한 할당량을 고려해야 합니다. VM 할당량에 대한 설명은 [여기(Linux)][virtual-machines-sizes-linux]와 [여기(Windows)][virtual-machines-sizes-windows]에 있습니다.

설명된 할당량은 이론적인 최대값을 나타냅니다.  디스크당 IOPS 제한은 작은 IO(8kb)로는 충족되지만 큰 IO(1Mb)로는 충족되지 않을 수 있습니다.  IOPS 제한은 단일 디스크의 세분성에 적용됩니다.

SAP 시스템이 Azure Virtual Machine 서비스 및 해당 기능에 적합한지 여부 또는 기존 시스템을 Azure에 배포하기 위해 다르게 구성해야 하는지 여부를 결정하기 위한 대략적인 의사 결정 트리로서, 아래의 의사 결정 트리를 사용할 수 있습니다.

![Azure에서 SAP를 배포하는 기능을 결정하는 의사 결정 트리][planning-guide-figure-700]

**1단계**: 시작하기 위한 가장 중요한 정보는 지정된 SAP 시스템의 SAPS 요구 사항입니다. SAP 시스템이 이미 2계층 구성으로 온-프레미스에 배포되더라도 SAPS 요구 사항을 DBMS 부분과 SAP 애플리케이션 부분으로 구분해야 합니다. 기존 시스템의 경우 사용 중인 하드웨어 관련된 SAPS를 기존 SAP 벤치마크를 기준으로 결정하거나 예측할 수 있습니다. 결과는 <https://sap.com/about/benchmark.html>에서 확인할 수 있습니다.
새로 배포된 SAP 시스템의 경우 시스템의 SAPS 요구 사항을 결정해야 하는 크기 조정 연습 과정을 거쳐야 합니다.
Azure의 SAP 크기 조정에 대한 내용은 이 블로그와 첨부 문서를 참조하세요. <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**2단계**: 기존 시스템의 경우 DBMS 서버의 I/O 볼륨 및 초당 I/O 작업 수를 측정해야 합니다. 새로 예정된 시스템의 경우는 새 시스템에 대한 크기 조정 연습 과정을 진행하면서 DBMS에 대한 I/O 요구 사항을 대략적으로 알 수 있습니다. 확실하지 않은 경우 결국 개념 증명을 수행해야 합니다.

**3단계**: DBMS 서버의 SAPS 요구 사항과 여러 다른 VM 형식의 Azure가 제공할 수 있는 SAPS를 비교합니다. 다른 Azure VM 형식의 SAPS 관련 정보는 SAP 정보 [1928533]에 나와 있습니다. 데이터베이스 계층이 대부분의 배포에서는 확장되지 않는 SAP NetWeaver 시스템의 계층이므로 먼저 DBMS VM을 중점적으로 설명합니다. 반면 SAP 애플리케이션 계층은 확장될 수 있습니다. SAP 지원 Azure VM 유형 중 어떤 유형도 필요한 SAPS를 제공할 수 없는 경우 계획된 SAP 시스템의 작업을 Azure에서 실행할 수 없습니다. 따라서 온-프레미스에 시스템을 배포해야 하거나 시스템에 대한 작업 볼륨을 변경해야 합니다.

**4단계**: [여기(Linux)][virtual-machines-sizes-linux]와 [여기(Windows)][virtual-machines-sizes-windows]서 설명하는 대로 Azure에서는 사용자가 Standard Storage 또는 Premium Storage 중 어느 것을 사용하든지 간에 디스크당 IOPS 할당량을 적용합니다. VM 유형에 따라 탑재 가능한 데이터 디스크 수가 달라집니다. 따라서 각 VM 유형에서 얻을 수 있는 최대 IOPS 수를 계산할 수 있습니다. 데이터베이스 파일 레이아웃에 따라 게스트 OS에서 단일 볼륨이 되도록 디스크를 스트라이프할 수 있습니다. 그러나 배포된 SAP 시스템의 현재 IOPS 볼륨이 Azure의 가장 큰 VM 유형에 대해 계산된 제한을 초과하고 더 많은 메모리로 보상할 수 없으면 SAP 시스템의 작업은 심각한 영향을 받을 수 있습니다. 이러한 경우 Azure에 시스템을 배포하지 않아야 하는 상황이 올 수 있습니다.

**5단계**: 특히 2계층 구성의 온-프레미스에 배포된 SAP 시스템을 3계층 구성의 Azure에서 구성해야 할 가능성이 있습니다. 이 단계에서는 SAP 애플리케이션 계층에 확장할 수 없고 다른 Azure VM 유형에서 제공하는 CPU 및 메모리 리소스에 적합하지 않은 구성 요소가 있는지 여부를 확인해야 합니다. 실제로 이러한 구성 요소가 있는 경우 SAP 시스템과 해당 작업을 Azure에 배포할 수 없습니다. 하지만 SAP 애플리케이션 구성 요소를 여러 Azure VM으로 확장할 수 있으면 시스템을 Azure에 배포할 수 있습니다.

**6단계**: DBMS 및 SAP 애플리케이션 계층 구성 요소를 Azure VM에서 실행할 수 있으면 다음과 관련해서 구성을 정의해야 합니다.

* Azure VM의 수
* 개별 구성 요소에 대한 VM 유형
* 충분한 IOPS를 제공하기 위한 DBMS VM의 VHD 수

## <a name="managing-azure-assets"></a>Azure 자산 관리

### <a name="azure-portal"></a>Azure portal

Azure Portal은 Azure VM 배포를 관리하기 위한 세 가지 인터페이스 중 하나입니다. 이미지에서 VM을 배포하는 등의 기본적인 관리 작업은 Azure Portal을 통해 수행할 수 있습니다. 또한 Storage 계정, Virtual Network 및 기타 Azure 구성 요소 만들기 작업은 Azure Portal에서 효율적으로 처리할 수 있는 작업이기도 합니다. 그러나 온-프레미스에서 Azure로 VHD를 업로드하거나, Azure 내에서 VHD를 복사하는 등의 기능을 수행하려면 타사 도구나 PowerShell 또는 CLI를 통한 관리가 필요합니다.

![Microsoft Azure Portal - Virtual Machine 개요][planning-guide-figure-800]


Virtual Machine 인스턴스에 대한 관리 및 구성 작업은 Azure Portal 내에서 수행할 수 있습니다.

Virtual Machine을 다시 시작하고 종료하는 것 외에 Virtual Machine 인스턴스용 데이터 디스크를 연결, 분리 및 생성하여 이미지 준비를 위해 인스턴스를 캡처하고 Virtual Machine 인스턴스의 크기를 구성할 수도 있습니다.

Azure Portal은 VM 및 기타 여러 Azure 서비스를 배포 및 구성하기 위한 기본 기능을 제공합니다. 그러나 Azure Portal에서 모든 기능을 제공하는 것은 아닙니다. Azure Portal에서는 다음과 같은 작업을 수행할 수 없습니다.

* Azure에 VHD 업로드
* VM 복사


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Microsoft Azure PowerShell cmdlet을 통한 관리

Windows PowerShell은 Azure에서 많은 수의 시스템을 배포하는 고객에 의해 널리 채택되어온 강력하고 확장 가능한 프레임워크입니다. 데스크톱, 랩톱 또는 전용 관리 스테이션에 PowerShell cmdlet을 설치한 후 원격으로 실행할 수 있습니다.

Azure PowerShell cmdlet을 사용할 수 있도록 로컬 데스크톱/랩톱을 설정하는 프로세스와 Azure 구독에서 이러한 cmdlet을 사용하도록 구성하는 방법은 [이 문서][powershell-install-configure]에서 설명하고 있습니다.

Azure PowerShell cmdlet을 설치, 업데이트 및 구성하는 방법에 대한 자세한 단계는 [배포 가이드의 이 장][deployment-guide-4.1]에서도 확인할 수 있습니다.

지금까지 고객이 경험한 바에 따르면 PS(PowerShell)는 분명히 VM을 배포하고 VM의 배포에서 사용자 지정 단계를 만들 수 있는 더 강력한 도구입니다. Azure에서 SAP 인스턴스를 실행하는 모든 고객은 PS cmdlet을 사용하여 Azure Portal에서 수행하는 관리 작업을 보완하거나 PS cmdlet만 사용해서 Azure의 배포를 관리하고 있습니다. Azure 관련 cmdlet은 2,000개가 넘는 Windows 관련 cmdlet과 동일한 명명 규칙을 공유하기 때문에 Windows 관리자가 이러한 cmdlet을 활용하는 것은 쉽습니다.

예제는 <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>에서 참조하세요.


SAP용 Azure 모니터링 확장(이 문서의 [SAP용 Azure 모니터링 솔루션][planning-guide-9.1] 장 참조)의 배포는 PowerShell 또는 CLI를 통해서만 가능합니다. 따라서 Azure에서 SAP NetWeaver 시스템을 배포 또는 관리하는 경우 PowerShell 또는 CLI를 반드시 설치하고 구성해야 합니다.  

Azure에서는 더 많은 기능을 제공하므로 cmdlet 업데이트를 요구하는 새로운 PS cmdlet이 추가될 예정입니다. 따라서 매월 한 번 이상 Azure 다운로드 사이트 <https://azure.microsoft.com/downloads/>를 확인하여 새 버전의 cmdlet이 있는지 확인하는 것이 좋습니다. 새 버전은 이전 버전이 그대로 유지된 상태로 설치됩니다.

Azure 관련 PowerShell 명령의 일반 목록은 <https://docs.microsoft.com/powershell/azure/overview>에서 확인하세요.

### <a name="management-via-microsoft-azure-cli-commands"></a>Microsoft Azure CLI 명령을 통한 관리

Linux를 사용하고 Azure 리소스를 관리하려는 고객의 경우에는 Powershell을 사용하지 못할 수 있습니다. Microsoft는 대안으로 Azure CLI를 제공합니다.
Azure CLI는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. Azure CLI는 Azure 포털에 있는 동일한 기능을 대부분 제공합니다.

설치 및 구성에 대한 내용과 CLI 명령을 사용하여 Azure 작업을 수행하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 클래식 CLI 설치][xplat-cli]
* [Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 머신 배포 및 관리][../../linux/create-ssh-secured-vm-from-template.md]
* [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure 클래식 CLI 사용][xplat-cli-azure-resource-manager]

또한 Azure CLI를 사용하여 SAP용 Azure 모니터링 확장을 배포하는 방법에 대해서는 [배포 가이드][planning-guide]의 [Linux VM용 Azure CLI][deployment-guide-4.5.2] 장도 읽어보세요.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Azure에서 SAP용 VM을 배포하는 다른 방법

이 장에서는 Azure에서 VM을 배포하는 다양한 방법을 알아봅니다. 이 장에서는 Azure의 VM 및 VHD의 처리뿐만 아니라 추가 준비 절차도 설명합니다.

### <a name="deployment-of-vms-for-sap"></a>SAP용 VM 배포

Microsoft Azure는 VM 및 관련 디스크를 배포하기 위한 여러 가지 방법을 제공합니다. 따라서 배포 방법에 따라 VM 준비가 달라질 수 있으므로 차이점을 이해해야 합니다. 일반적으로 다음과 같은 시나리오를 살펴보겠습니다.

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동

온-프레미스에서 Azure로 특정 SAP 시스템을 이동하려고 합니다. 이 작업은 OS, SAP 바이너리 및 DBMS 바이너리를 포함하는 VHD와 DBMS 데이터와 로그 파일이 있는 VHD를 Azure에 업로드하여 수행할 수 있습니다. [아래의 시나리오 2][planning-guide-5.1.2]와 달리 온-프레미스 환경에서 구성한 대로 Azure VM에서 호스트 이름, SAP SID 및 SAP 사용자 계정을 유지합니다. 그러므로 이미지 일반화는 필요하지 않습니다. 일반화되지 않은 VM 또는 VHD를 온-프레미스에서 준비하는 단계와 Azure로 업로드하는 방법에 대해서는 이 문서의 [일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동 준비][planning-guide-5.2.1] 장을 참조하세요. Azure에서 이러한 이미지를 배포하는 자세한 단계에 대해서는 [시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동][deployment-guide-3.4]([배포 가이드][deployment-guide]) 장을 읽어보세요.

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>고객별 이미지를 사용하여 VM 배포

OS 또는 DBMS 버전의 특정 패치 요구 사항으로 인해 Azure Marketplace에 제공된 이미지가 용도에 맞지 않을 수 있습니다. 따라서 자체 개인 OS/DBMS VM 이미지를 사용하는 VM을 만들어 이후 여러 번 배포할 수 있습니다. 중복성 유지를 위해 이러한 개인 이미지를 준비하려면 다음 항목을 고려해야 합니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> 자세한 내용은 다음 항목을 참조하세요. <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> sysprep 명령을 통해 Windows 설정(예: Windows SID 및 호스트 이름)을 온-프레미스 VM에서 추상화/일반화해야 합니다.
>
>
> ![Linux][Logo_Linux] Linux
>
> [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] 또는 [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle] 관련 문서에서 설명하는 단계를 수행하여 Azure에 업로드할 VHD를 준비합니다.
>
>

- - -
온-프레미스 VM(특히 2계층 시스템)에 SAP 콘텐츠를 이미 설치한 경우 Azure VM 배포 후 SAP Software Provisioning Manager에서 지원하는 인스턴스 이름 변경 절차를 통해 SAP 시스템 설정을 적용할 수 있습니다(SAP Note [1619720]). 온-프레미스 준비 단계 및 Azure로 일반화된 VM 업로드에 대해서는 이 문서의 [SAP용 고객별 이미지를 사용하여 VM 배포 준비][planning-guide-5.2.2] 및 [온-프레미스에서 Azure로 VHD 업로드][planning-guide-5.3.2] 장을 참조하세요. Azure에서 이러한 이미지를 배포하는 자세한 단계에 대해서는 [시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포][deployment-guide-3.3]([배포 가이드][deployment-guide]) 장을 참조하세요.

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Azure Marketplace에서 VM 배포

Azure Marketplace에서 Microsoft 또는 타사 제공 VM 이미지를 사용하여 VM을 배포하려는 경우가 있을 수 있습니다. Azure에서 VM을 배포한 후 온-프레미스 환경에서와 동일한 지침 및 도구를 사용하여 VM 내부에 SAP 소프트웨어 및/또는 DBMS를 설치합니다. 자세한 배포 설명을 보려면 [시나리오 1: SAP용 Azure Marketplace에서 VM 배포][deployment-guide-3.2]([배포 가이드][deployment-guide]) 장을 참조하세요.

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Azure용 SAP로 VM 준비

VM을 Azure에 업로드하기 전에 VM 및 VHD가 특정 요구 사항을 충족하는지 확인해야 합니다. 사용되는 배포 방법에 따라 약간의 차이가 있습니다.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동 준비

일반적인 배포 방법은 SAP 시스템을 실행하는 기존 VM을 온-프레미스에서 Azure로 이동하는 것입니다. 해당 VM 및 VM의 SAP 시스템은 동일한 호스트 이름과 거의 동일한 SAP SID를 사용하여 Azure에서 실행되어야 합니다. 이 경우 다중 배포를 위해 VM의 게스트 OS를 일반화해야 합니다. 온-프레미스 네트워크가 Azure로 확장된 경우(이 문서의 [크로스 프레미스 - 온-프레미스 네트워크에 완전히 통합될 필요가 있는 단일 또는 다중 SAP VM을 Azure에 배포][planning-guide-2.2] 장 참조) 온-프레미스 이전에 사용된 것과 같은 동일한 도메인 계정을 VM 내에서 사용할 수도 있습니다.

고유한 Azure VM 디스크를 준비할 때의 요구 사항은 다음과 같습니다.

* 원래 운영 체제가 포함된 VHD의 최대 크기는 127GB로 제한되어 있었습니다. 이 제한은 2015년 3월 말에 없어졌습니다. 이제 운영 체제를 포함하는 VHD도 다른 Azure Storage 호스트 VHD처럼 최대 크기가 1TB까지 가능합니다.
* 고정된 VHD 형식이어야 합니다. 동적 VHD 또는 VHDx 형식의 VHD는 Azure에서 아직 지원되지 않습니다. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* VM에 탑재되고 Azure에서 다시 VM으로 탑재되어야 하는 VHD도 고정 VHD 형식이어야 합니다. 데이터 디스크의 크기 제한에 대해 알아보려면 [이 문서(Linux)](../../linux/managed-disks-overview.md)와 [이 문서(Windows)](../../windows/managed-disks-overview.md)를 확인하세요. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* Microsoft 기술 지원 서비스에서 사용할 수 있거나 VM이 배포되고 적절한 추가 사용자가 사용할 수 있게 될 때까지 서비스 및 애플리케이션이 실행되기 위한 컨텍스트로 할당될 수 있는 관리자 권한의 다른 로컬 계정을 추가합니다.
* 특정 배포 시나리오에 필요할 수 있으므로 다른 로컬 계정을 추가합니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> 이 시나리오에서는 Azure에서 VM을 업로드 및 배포하기 위해 VM의 일반화(sysprep)가 필요하지 않습니다.
> D:\ 드라이브가 사용되지 않음을 확인합니다.
> 그리고 이 문서의 [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 장에서 설명한 대로 연결된 디스크에 대해 디스크 자동 탑재를 설정합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 이 시나리오에서는 Azure에서 VM을 업로드 및 배포하기 위해 VM의 일반화(waagent -deprovision)가 필요하지 않습니다.
> /mnt/resource가 사용되지 않는지와 모든 디스크가 uuid를 통해 탑재되는지 확인합니다. OS 디스크의 경우 부팅 로더 항목에 uuid 기반 탑재가 반영되는지도 확인합니다.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>SAP용 고객별 이미지를 사용하여 VM 배포 준비

일반화된 OS를 포함하는 VHD 파일은 Azure Storage 계정의 컨테이너에 저장되거나 관리 디스크 이미지로 저장됩니다. [시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포][deployment-guide-3.3]([배포 가이드][deployment-guide]) 장에서 설명한 대로 배포 템플릿 파일에서 VHD 또는 관리 디스크 이미지를 원본으로 참조하여 이러한 이미지에서 새 VM을 배포할 수 있습니다.

고유한 Azure VM 이미지를 준비할 때의 요구 사항은 다음과 같습니다.

* 원래 운영 체제가 포함된 VHD의 최대 크기는 127GB로 제한되어 있었습니다. 이 제한은 2015년 3월 말에 없어졌습니다. 이제 운영 체제를 포함하는 VHD도 다른 Azure Storage 호스트 VHD처럼 최대 크기가 1TB까지 가능합니다.
* 고정된 VHD 형식이어야 합니다. 동적 VHD 또는 VHDx 형식의 VHD는 Azure에서 아직 지원되지 않습니다. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* VM에 탑재되고 Azure에서 다시 VM으로 탑재되어야 하는 VHD도 고정 VHD 형식이어야 합니다. 데이터 디스크의 크기 제한에 대해 알아보려면 [이 문서(Linux)](../../windows/managed-disks-overview.md)와 [이 문서(Windows)](../../linux/managed-disks-overview.md)를 확인하세요. 동적 VHD는 PowerShell commandlet 또는 CLI를 사용하여 업로드할 경우 고정 VHD로 변환됩니다.
* 특정 배포 시나리오에 필요할 수 있으므로 다른 로컬 계정을 추가합니다.
* 이미지에 SAP NetWeaver의 설치가 포함되어 있고 Azure 배포 시에 호스트 이름을 원래 이름에서 다르게 변경할 가능성이 있는 경우 SAP Software Provisioning Manager DVD의 최신 버전을 템플릿으로 복사하는 것이 좋습니다. 이렇게 하면 새 복사가 시작되는 즉시, SAP 제공 이름 바꾸기 기능을 사용하여 배포된 VM 이미지 내에서 변경된 호스트 이름을 손쉽게 조정하고 SAP 시스템의 SID를 변경할 수 있습니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> D:\ 드라이브가 사용되지 않는지 확인하고 이 문서의 [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 장에서 설명한 대로 연결된 디스크에 대해 디스크 자동 탑재를 설정하세요.
>
> ![Linux][Logo_Linux] Linux
>
> /mnt/resource가 사용되지 않는지와 모든 디스크가 uuid를 통해 탑재되는지 확인합니다. OS 디스크의 경우 부팅 로더 항목에 uuid 기반 탑재가 반영되는지도 확인합니다.
>
>

- - -
* SAP GUI(관리 및 설치용)를 이러한 템플릿에 미리 설치할 수 있습니다.
* 크로스-프레미스 시나리오에서 VM을 성공적으로 실행하는 데 필요한 기타 소프트웨어가 VM의 이름 바꾸기 기능에서 문제 없이 작동되면 설치해도 됩니다.

VM이 범용으로 준비되고, 결과적으로 대상 Azure 배포 시나리오에서 사용할 수 없는 계정/사용자와 독립될 경우 이러한 이미지 일반화의 마지막 준비 단계가 수행됩니다.

##### <a name="generalizing-a-vm"></a>VM 일반화
- - -
> ![Windows][Logo_Windows] Windows
>
> 마지막 단계는 관리자 계정을 사용하여 VM에 로그인하는 것입니다. *관리자* 권한으로 Windows 명령 창을 엽니다. %windir%\windows\system32\sysprep로 이동하여 sysprep.exe를 실행합니다.
> 작은 창이 나타납니다. **일반화** 옵션을 선택하고(기본적으로 선택 취소됨) 종료 옵션을 기본 설정인 ‘다시 부팅'에서 '종료'로 변경해야 합니다. 이 절차에서는 sysprep 프로세스가 VM의 게스트 OS에서 온-프레미스로 실행된다고 가정합니다.
> Azure에서 이미 실행 중인 VM을 사용하여 절차를 수행하려면 [이 문서](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)에서 설명하는 단계를 따르세요.
>
> ![Linux][Logo_Linux] Linux
>
> [Linux 가상 컴퓨터를 캡처하여 Resource Manager 템플릿으로 사용하는 방법][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>온-프레미스와 Azure 간에 VM 및 VHD 전송
Azure Portal을 통해서는 Azure에 VM 이미지와 디스크를 업로드할 수 없으므로 Azure PowerShell cmdlet 또는 CLI를 사용해야 합니다. 'AzCopy' 도구를 사용할 수도 있습니다. 이 도구는 온-프레미스와 Azure 간에 VHD를 복사할 수 있습니다(양방향으로). 또한 Azure 지역 간에 VHD를 복사할 수도 있습니다. AzCopy의 다운로드 및 사용에 대해서는 [이 설명서][storage-use-azcopy]를 참조하세요.

세 번째 방법은 다양한 타사 GUI 기반 도구를 사용하는 것입니다. 그러나 이러한 도구가 Azure 페이지 Blob을 지원하는지 확인하세요. 여기서는 Azure 페이지 Blob 저장소를 사용해야 합니다(차이점은 <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs> 참조). 또한 Azure에서 제공하는 도구는 업로드해야 하는 VM 및 VHD를 압축하는 데 효율적입니다. 이러한 압축 효율성은 업로드 시간(온-프레미스 시설과 대상 Azure 배포 지역에서 인터넷으로의 업로드 링크에 따라 다름)을 단축하므로 중요합니다. 따라서 유럽 위치에서 미국의 Azure 데이터 센터로 VM 또는 VHD를 업로드하는 것은 같은 VM/VHD를 유럽의 Azure 데이터 센터로 업로드하는 것보다 더 오래 걸린다고 가정할 수 있습니다.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>온-프레미스에서 Azure로 VHD 업로드
온-프레미스 네트워크에서 기존 VM 또는 VHD를 업로드하려면 이러한 VM 또는 VHD가 이 문서의 [일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동 준비][planning-guide-5.2.1] 장에서 나열한 요구 사항을 충족해야 합니다.

이러한 VM은 일반화할 필요가 없으며 온-프레미스 쪽에서 종료된 이후의 상태 및 형태 그대로 업로드할 수 있습니다. 운영 체제에 포함하지 않는 추가 VHD도 마찬가지입니다.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>VHD를 업로드한 후 Azure 디스크로 만들기
이러한 경우 OS를 포함하거나 포함하지 않는 VHD를 업로드하려고 하며 VM을 데이터 디스크로 탑재하거나 OS 디스크로 사용할 것입니다. 이 작업은 다중 단계로 진행됩니다.

**PowerShell**

* 사용 하 여 구독에 로그인 *AzAccount 연결*
* 하 여 컨텍스트의 구독 설정 *집합 AzContext* 및 매개 변수 SubscriptionId 또는 SubscriptionName- <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* 사용 하 여 VHD 업로드 *추가 AzVhd* -Azure 저장소 계정을 참조 하세요. <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (선택 사항) 사용 하 여 VHD에서 관리 디스크를 만들려면 *새로 만들기-AzDisk* -참조 <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>
* 새 VM 구성의 OS 디스크를 VHD 또는 Managed Disk를 사용 하 여 설정할 *집합 AzVMOSDisk* -참조 <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
* 사용 하 여 VM 구성에서 새 VM을 만듭니다 *New-azvm* -참조 <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>
* 데이터 디스크를 사용 하 여 새 VM을 추가할 *추가 AzVMDataDisk* -참조 <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>

**Azure CLI**

* *az login*을 사용하여 구독에 로그인
* *az account set --subscription `<subscription name or id`>* 를 사용하여 구독 선택
* *az storage blob upload*를 사용하여 VHD를 업로드 - [Azure Storage에서 Azure CLI 사용][storage-azure-cli] 참조
* (선택 사항) *az disk create*를 사용하여 VHD에서 관리 디스크 만들기 - https://docs.microsoft.com/cli/azure/disk 참조
* *az vm create* 및 매개 변수 *--attach-os-disk*를 사용하여 업로드한 VHD 또는 관리 디스크를 OS 디스크로 지정해 새 VM 만들기
* *az vm disk attach* 및 매개 변수 *--new*를 사용하여 새 VM에 데이터 디스크 추가

**템플릿**

* Powershell 또는 Azure CLI를 사용하여 VHD 업로드
* (선택 사항) Powershell, Azure CLI 또는 Azure Portal을 사용하여 VHD에서 관리 디스크 만들기
* [이 예제 JSON 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json)에 표시된 대로 VHD를 참조하는 JSON 템플릿을 사용하거나, [이 예제 JSON 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)에 표시된 대로 관리 디스크를 사용하여 VM 배포

#### <a name="deployment-of-a-vm-image"></a>VM 이미지 배포
Azure VM 이미지로 사용하기 위해 온-프레미스 네트워크에서 기존 VM 또는 VHD를 업로드하려면 이러한 VM 또는 VHD가 이 문서의 [SAP용 고객별 이미지를 사용하여 VM 배포 준비][planning-guide-5.2.2] 장에서 나열한 요구 사항을 충족해야 합니다.

* Windows의 *sysprep* 또는 Linux의 *waagent -deprovision*을 사용하여 VM 일반화 - [Sysprep 기술 참조](https://technet.microsoft.com/library/cc766049.aspx)(Windows) 또는 [Linux 가상 머신을 캡처하여 Resource Manager 템플릿으로 사용하는 방법][capture-image-linux-step-2-create-vm-image] 참조
* 사용 하 여 구독에 로그인 *AzAccount 연결*
* 하 여 컨텍스트의 구독 설정 *집합 AzContext* 및 매개 변수 SubscriptionId 또는 SubscriptionName- <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>
* 사용 하 여 VHD 업로드 *추가 AzVhd* -Azure 저장소 계정을 참조 하세요. <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>
* (선택 사항) 사용 하 여 VHD에서 관리 디스크 이미지를 만들 *새로 만들기-AzImage* -참조 <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>
* 새 VM 구성의 OS 디스크를
  * 사용 하 여 VHD *집합 AzVMOSDisk-SourceImageUri-CreateOption fromImage* -참조 <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>
  * 관리 디스크 이미지로 *집합 AzVMSourceImage* -참조 <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>
* 사용 하 여 VM 구성에서 새 VM을 만듭니다 *New-azvm* -참조 <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>

**Azure CLI**

* Windows의 *sysprep* 또는 Linux의 *waagent -deprovision*을 사용하여 VM 일반화 - [Sysprep 기술 참조](https://technet.microsoft.com/library/cc766049.aspx)(Windows) 또는 [Linux 가상 머신을 캡처하여 Resource Manager 템플릿으로 사용하는 방법][capture-image-linux-step-2-create-vm-image] 참조
* *az login*을 사용하여 구독에 로그인
* *az account set --subscription `<subscription name or id`>* 를 사용하여 구독 선택
* *az storage blob upload*를 사용하여 VHD를 업로드 - [Azure Storage에서 Azure CLI 사용][storage-azure-cli] 참조
* (선택 사항) *az image create*를 사용하여 VHD에서 관리 디스크 이미지 만들기 - https://docs.microsoft.com/cli/azure/image 참조
* *az vm create* 및 매개 변수 *--image*를 사용하여 업로드한 VHD 또는 관리 디스크 이미지를 OS 디스크로 지정해 새 VM 만들기

**템플릿**

* Windows의 *sysprep* 또는 Linux의 *waagent -deprovision*을 사용하여 VM 일반화 - [Sysprep 기술 참조](https://technet.microsoft.com/library/cc766049.aspx)(Windows) 또는 [Linux 가상 머신을 캡처하여 Resource Manager 템플릿으로 사용하는 방법][capture-image-linux-step-2-create-vm-image] 참조
* Powershell 또는 Azure CLI를 사용하여 VHD 업로드
* (선택 사항) Powershell, Azure CLI 또는 Azure Portal을 사용하여 VHD에서 관리 디스크 이미지 만들기
* [이 예제 JSON 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json)에 표시된 대로 이미지 VHD를 참조하는 JSON 템플릿을 사용하거나, [이 예제 JSON 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json)에 표시된 대로 관리 디스크 이미지를 사용하여 VM 배포

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>온-프레미스에 VHD 또는 관리 디스크 다운로드
Azure Infrastructure as a Services는 VHD 및 SAP 시스템을 업로드만 할 수 있는 단방향 경로가 아닙니다. Azure에서 온-프레미스 환경으로도 SAP 시스템을 다시 이동할 수 있습니다.

다운로드하는 동안 VHD 또는 관리 디스크는 활성 상태일 수 없습니다. VM에 탑재된 디스크를 다운로드하는 경우에도 VM을 종료하고 할당을 취소해야 합니다. 온-프레미스에서 새 시스템을 설정하는 데 사용해야 하는 데이터베이스 콘텐츠만 다운로드하려고 하며 다운로드 도중 및 새 시스템을 설정하는 동안 Azure의 시스템이 계속 작동되게 하려면 디스크에 압축된 데이터베이스 백업을 수행하여 OS 기반 VM은 다운로드하지 않고 해당 디스크만 다운로드함으로써 가동 중지 시간이 길어지지 않도록 할 수 있습니다.

#### <a name="powershell"></a>PowerShell

* 관리 디스크 다운로드  
  먼저 관리 디스크의 기본 Blob에 액세스해야 합니다. 그런 다음 기본 Blob를 새 Storage 계정에 복사하고 이 Storage 계정에서 Blob를 다운로드할 수 있습니다.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* VHD 다운로드  
  SAP 시스템이 중지 되는 VM이 종료 하 고 VHD 디스크를 온-프레미스 환경 다시 다운로드 하려면 온-프레미스 대상에 저장 AzVhd PowerShell cmdlet을 사용할 수 있습니다. 이를 위해 Azure Portal의 '스토리지 섹션'에서 찾을 수 있는 VHD의 URL이 있어야 하며(Storage 계정 및 VHD가 만들어진 스토리지 컨테이너로 이동해야 함) VHD를 복사할 위치를 알아야 합니다.

  그런 다음, 매개 변수 SourceUri를 다운로드할 VHD의 URL로 정의하고 LocalFilePath를 VHD의 물리적 위치(해당 이름 포함)로 정의하여 이 명령을 활용할 수 있습니다. 명령은 다음과 같습니다.

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  한 저장 AzVhd cmdlet의 자세한 내용은 <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>합니다.

#### <a name="azure-cli"></a>Azure CLI
* 관리 디스크 다운로드  
  먼저 관리 디스크의 기본 Blob에 액세스해야 합니다. 그런 다음 기본 Blob를 새 Storage 계정에 복사하고 이 Storage 계정에서 Blob를 다운로드할 수 있습니다.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* VHD 다운로드   
  SAP 시스템이 중지되고 VM이 종료되면 온-프레미스 대상에 대해 Azure CLI 명령 _azure storage blob download_를 사용하여 VHD 디스크를 온-프레미스 환경으로 다시 다운로드할 수 있습니다. 이를 위해 Azure Portal의 '스토리지 섹션'에서 찾을 수 있는 VHD의 이름 및 컨테이너가 있어야 하며(Storage 계정 및 VHD가 만들어진 스토리지 컨테이너로 이동해야 함) VHD를 복사할 위치를 알아야 합니다.

  그런 다음, 다운로드할 VHD의 blob 및 container 매개 변수를 VHD의 URL로 정의하고 destination을 VHD의 물리적 대상 위치(해당 이름 포함)로 정의하여 이 명령을 활용할 수 있습니다. 명령은 다음과 같습니다.

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Azure 내에서 VM 및 디스크 전송

#### <a name="copying-sap-systems-within-azure"></a>Azure 내에서 SAP 시스템 복사

SAP 시스템 또는 SAP 애플리케이션 계층을 지원하는 전용 DBMS 서버까지도 SAP 데이터베이스의 바이너리 또는 데이터 및 로그 파일과 함께 OS를 포함하는 여러 디스크로 구성될 수 있습니다. 디스크를 복사하는 Azure의 기능과 디스크를 로컬 디스크에 저장하는 Azure의 기능 모두 여러 디스크의 스냅숏을 일관된 방식으로 만드는 동기화 메커니즘을 사용하지 않습니다. 따라서 복사 또는 저장된 디스크의 상태는 같은 VM에 탑재되는 경우에도 다를 수 있습니다. 즉, 여러 다른 디스크에 다른 데이터 및 로그 파일이 포함된 구체적인 경우에도 데이터베이스는 일관되지 않게 됩니다.

**결론: SAP 시스템 구성의 일부로 디스크를 복사하거나 저장하려면 SAP 시스템을 중지하고 배포된 VM도 종료해야 합니다. 그런 후에만 디스크 집합을 복사 또는 다운로드하여 Azure 또는 온-프레미스에서 SAP 시스템의 복사본을 만들 수 있습니다.**

데이터 디스크는 Azure Storage 계정에 VHD 파일로 저장할 수 있으며 가상 머신에 직접 연결되거나 이미지로 사용될 수 있습니다. 이 경우 VHD는 다른 위치로 복사된 후에 가상 머신에 연결됩니다. Azure에서 VHD 파일의 전체 이름은 Azure 내에서 고유해야 합니다. 이미 앞서 언급한 것처럼 이 이름은 다음과 같은 3부분으로 구성됩니다.

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

데이터 디스크를 관리 디스크로 사용할 수도 있습니다. 이 경우에는 관리 디스크를 사용하여 새 관리 디스크를 만든 후 가상 머신에 연결합니다. 관리 디스크의 이름은 리소스 그룹 내에서 고유해야 합니다.

##### <a name="powershell"></a>PowerShell

[이 문서][storage-powershell-guide-full-copy-vhd]에서 보여 주듯이 Azure PowerShell cmdlet을 사용하여 VHD를 복사할 수 있습니다. 새 관리 디스크를 만들려면 다음 예제에서와 같이 새 AzDiskConfig 및 새 AzDisk를 사용 합니다.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure CLI

[이 문서][storage-azure-cli-copy-blobs]에서 보여 주듯이 Azure CLI를 사용하여 VHD를 복사할 수 있습니다. 새 관리 디스크를 만들려면 다음 예제에 나와 있는 것처럼 *az disk create*를 사용합니다.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage 도구

* <https://storageexplorer.com/>

Azure Storage 탐색기의 전문가 버전은 아래 페이지에서 확인할 수 있습니다.

* <https://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

저장소 계정 내에서 VHD 자체를 복사하는 작업은 몇 초면 끝나는 프로세스입니다(지연 복사 및 기록 중 복사를 사용하여 스냅숏을 만드는 SAN 하드웨어와 유사). VHD 파일의 복사본을 만든 후에는 가상 머신에 연결하거나 VHD 복사본을 가상 머신에는 연결하기 위한 이미지로 사용할 수 있습니다.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>Azure CLI

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Azure Storage 계정 간 디스크 복사
Azure Portal에서는 이 작업을 수행할 수 없습니다. Azure PowerShell cmdlet, Azure CLI 또는 타사 저장소 브라우저를 사용할 수 있습니다. Storage 계정 간 및 Azure 구독 내에서 지역 간에 Blob을 비동기식으로 복사하는 기능이 있는 PowerShell cmdlet 또는 CLI 명령으로 Blob을 만들고 관리할 수 있습니다.

##### <a name="powershell"></a>PowerShell
구독 간에 VHD를 복사할 수도 있습니다. 자세한 내용은 [이 문서][storage-powershell-guide-full-copy-vhd]를 참조하세요.

PS cmdlet 논리의 기본 흐름은 다음과 같습니다.

* 저장소 계정 컨텍스트 만들기는 **소스** 사용 하 여 저장소 계정 *새로 만들기-AzStorageContext* -참조 <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* 저장소 계정 컨텍스트 만들기 합니다 **대상** 사용 하 여 저장소 계정 *새로 만들기-AzStorageContext* -참조 <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>
* 복사 시작

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* 루프의 복사본 상태 확인

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* 위에서 설명한 것처럼 가상 머신에 새 VHD를 연결합니다.

예제는 [이 문서][storage-powershell-guide-full-copy-vhd]를 참조하세요.

##### <a name="azure-cli"></a>Azure CLI
* 복사 시작

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* 복사가 여전히 루프에 있는지 상태 확인

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* 위에서 설명한 것처럼 가상 머신에 새 VHD를 연결합니다.

예제는 [이 문서][storage-azure-cli-copy-blobs]를 참조하세요.

### <a name="disk-handling"></a>디스크 처리

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>SAP 배포를 위한 VM/디스크 구조

이상적으로 VM 및 관련된 디스크의 구조를 처리하는 작업은 간단해야 합니다. 온-프레미스 설치에서 고객은 서버 설치를 구성하는 여러 가지 방법을 개발했습니다.

* OS와 DBMS 및/또는 SAP의 모든 바이너리가 들어 있는 하나의 기본 디스크 2015년 3월부터 이전의 127GB 제한이 없어지면서 이 디스크는 최대 1TB까지 포함할 수 있습니다.
* SAP 데이터베이스의 DBMS 로그 파일과 DBMS 임시 저장소 영역의 로그 파일(DBMS에서 지원하는 경우)이 포함된 하나 또는 여러 개의 디스크 데이터베이스 로그 IOPS 요구 수준이 높은 경우 필요한 IOPS 볼륨에 도달하기 위해 여러 디스크를 스트라이프해야 합니다.
* SAP 데이터베이스의 하나 또는 두 개의 데이터베이스 파일 및 DBMS 임시 데이터 파일(DBMS에서 지원하는 경우)을 포함하는 많은 수의 디스크

![SAP용 Azure IaaS VM의 참조 구성][planning-guide-figure-1300]


- - -
> ![Windows][Logo_Windows] Windows
>
> 많은 고객에게서 OS가 설치된 c:\ 드라이브에 SAP 및 DBMS 바이너리가 설치되지 않은 구성을 볼 수 있었습니다. 그 이유는 다양하지만 10~15년 전에는 근본적으로는 드라이브가 작고 OS 업그레이드를 하려면 추가 공간이 필요했기 때문일 것입니다. 두 조건이 오늘날에는 그렇게 많지 않은 편입니다. 현재는 c:\ 드라이브를 큰 볼륨의 디스크나 VM에 매핑할 수 있습니다. 구조 내에서 배포를 단순하게 유지하려면 Azure의 SAP NetWeaver 시스템에 대해 다음 배포 패턴을 따르는 것이 좋습니다.
>
> Windows 운영 체제 페이지 파일은 D: 드라이브(비영구 디스크)에 있어야 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> [이 문서][virtual-machines-linux-agent-user-guide]에서 설명한 대로 Linux의 /mnt/mnt/resource 아래에 Linux 스왑 파일을 배치합니다. 스왑 파일은 Linux 에이전트 /etc/waagent.conf의 구성 파일에서 구성할 수 있습니다. 다음 설정을 추가 또는 변경합니다.
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

변경 내용을 활성화하려면 다음을 사용하여 Linux 에이전트를 다시 시작해야 합니다.

```
sudo service waagent restart
```

권장 스왑 파일 크기에 대한 자세한 내용은 SAP 정보 [1597355] 를 읽어보세요.

- - -
이러한 디스크가 호스트되는 Azure Storage의 형식과 DBMS 데이터 파일에 사용되는 디스크의 수는 IOPS 요구 사항 및 필요한 대기 시간에 따라 결정되어야 합니다. 정확한 할당량에 대한 설명은 [이 문서(Linux)][virtual-machines-sizes-linux]와 [이 문서(Windows)][virtual-machines-sizes-windows]에 있습니다.

지난 2년 동안의 SAP 배포 경험을 통해 다음과 같은 몇 가지 교훈을 얻었습니다.

* 기존 고객 시스템에는 SAP 데이터베이스를 나타내는 다양한 크기의 데이터 파일이 있을 수 있으므로 여러 다른 데이터 파일에 대한 IOPS 트래픽이 항상 동일한 것은 아닙니다. 따라서 여러 디스크를 사용하여 데이터 파일 LUN을 분리해서 배치하는 것보다 RAID 구성을 사용하는 것이 더 나은 것으로 확인되었습니다. 특히 IOPS 속도가 DBMS 트랜잭션 로그에 대한 단일 디스크의 할당량에 도달하는 Azure Standard Storage에서 이러한 경우가 확인되었습니다. 이러한 시나리오에서는 Premium Storage를 사용하거나 소프트웨어 스트라이프를 사용하여 여러 Standard Storage 디스크를 집계하는 것이 좋습니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Linux에서 소프트웨어 RAID 구성][virtual-machines-linux-configure-raid]
> * [Azure에서 Linux VM에 LVM 구성][virtual-machines-linux-configure-lvm]
> * [Azure Storage 비밀 및 Linux I/O 최적화](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Premium Storage는 중요한 트랜잭션 로그 쓰기에서 특히 더 나은 성능을 나타냅니다. 프로덕션 수준의 성능을 제공할 것으로 기대되는 SAP 시나리오에서는 Azure Premium Storage를 활용할 수 있는 VM 시리즈를 사용하는 것이 강력히 권장됩니다.

OS와 SAP의 바이너리 및 데이터베이스(기본 VM)도 포함하는 디스크는 더 이상 127GB로 제한되지 않습니다. 이제 최대 1TB의 크기가 가능합니다. 이 크기는 SAP 일괄 작업 로그 등을 비롯하여 필요한 모든 파일을 보관하는 데 충분한 공간이어야 합니다.

특히 DBMS VM에 대한 추가 제안 사항과 자세한 내용은 [DBMS 배포 가이드][dbms-guide]를 참조하세요.

#### <a name="disk-handling"></a>디스크 처리

대부분의 시나리오에서는 VM으로 SAP 데이터베이스를 배포하기 위해 추가 디스크를 만들 필요가 있습니다. 이 문서의 [SAP 배포를 위한 VM/디스크 구조][planning-guide-5.5.1] 장에서 디스크 수에 대한 고려 사항을 설명했습니다. Azure Portal을 사용하면 기본 VM을 배포한 후 디스크를 연결 및 분리할 수 있습니다. VM이 가동 및 실행 중일 때와 중지될 때 디스크를 연결/분리할 수 있습니다. 디스크를 연결할 때 Azure Portal은 빈 디스크 또는 기존 디스크(이 시점에 다른 VM에 연결되지 않은)에 연결할 수 있도록 합니다.

**참고**: 디스크는 언제나 한 번에 하나의 VM에만 연결할 수 있습니다.

![Azure Standard Storage에서 디스크 연결/분리][planning-guide-figure-1400]

새 가상 머신을 배포하는 동안 관리 디스크를 사용할지 아니면 Azure Storage 계정에 디스크를 배치할지를 결정할 수 있습니다. Premium Storage를 사용하려는 경우에는 관리 디스크를 사용하는 것이 좋습니다.

다음으로는 비어 있는 새 디스크를 만들지 아니면 이전에 업로드되었으며 이제 VM에 연결해야 하는 기존 디스크를 선택할지를 결정해야 합니다.

**중요**: Azure Standard Storage에서 호스트 캐싱을 사용하지 **않도록** 합니다. 호스트 캐시 기본 설정을 기본값인 NONE으로 유지해야 합니다. Azure Premium Storage를 사용하는 경우 I/O 특성이 데이터베이스 데이터 파일에 대한 일반적인 I/O 트래픽으로 해석되는 경우 읽기 캐싱을 사용하도록 설정해야 합니다. 데이터베이스 트랜잭션 로그 파일의 경우 캐싱 없음이 권장됩니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Azure Portal에서 데이터 디스크를 연결하는 방법][virtual-machines-linux-attach-disk-portal]
>
> 디스크를 연결하는 경우 VM에 로그인하여 Windows 디스크 관리자를 열어야 합니다. [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 장에서 권장한 대로 자동 탑재를 사용하도록 설정하지 않으면 새로 연결된 볼륨을 온라인 상태로 만든 후 초기화해야 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 디스크가 연결되어 있으면 [이 문서][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]에서 설명한 대로 VM에 로그인하고 디스크를 초기화해야 합니다.
>
>

- - -
새 디스크가 비어 있는 디스크이면 포맷도 필요합니다. 포맷 시, 특히 DBMS 데이터 및 로그 파일의 경우, DBMS 완전 배포의 경우와 동일한 권장 지침이 적용됩니다.

이미 [Microsoft Azure Virtual Machine 개념][planning-guide-3.2] 장에서 언급한 대로 Azure Storage 계정은 I/O 볼륨, IOPS 및 데이터 볼륨 측면에서 무한 리소스를 제공하지 않습니다. 일반적으로 DBMS VM이 그 영향을 가장 많이 받게 됩니다. Azure Storage 계정 볼륨의 제한을 벗어나지 않기 위해서는 배포할 높은 I/O 볼륨 VM이 거의 없을 경우 각 VM에 대해 별도 Storage 계정을 사용하는 것이 가장 좋을 수 있습니다. 그렇지 않으면 각 단일 Storage 계정 제한에 도달하지 않으면서 개별 Storage 계정 간에 이러한 VM을 골고루 분산하는 방법을 찾아야 합니다. 자세한 내용은 [DBMS 배포 가이드][dbms-guide]를 참조하세요. 또한 순수한 SAP 애플리케이션 서버 VM 또는 결과적으로는 추가 VHD를 요구할 수 있는 기타 VHD의 경우에도 이러한 제한에 유의해야 합니다. 관리 디스크를 사용하는 경우에는 이러한 제한이 적용되지 않습니다. Premium Storage를 사용하려는 경우에는 관리 디스크를 사용하는 것이 좋습니다.

Storage 계정과 관련된 또 다른 주제는 Storage 계정의 VHD가 지역에서 복제되는지 여부입니다. 지역에서 복제 기능은 VM 수준이 아닌 Storage 계정 수준에서 사용하거나 사용하지 않도록 설정됩니다. 지역에서 복제 기능이 사용되도록 설정되면 Storage 계정 내의 VHD가 동일한 지역 내의 다른 Azure 데이터 센터로 복제됩니다. 이를 결정하기 전에 다음 제한 사항을 고려해야 합니다.

Azure 지역에서 복제 기능은 VM의 각 VHD에서 로컬로 작동하며, VM의 여러 VHD에 걸쳐 시간순으로 IO를 복제하지 않습니다. 따라서 기본 VM을 나타내는 VHD와 VM에 연결된 추가 VHD는 서로 독립적으로 복제됩니다. 즉, 여러 다른 VHD에서 변경된 내용은 동기화되지 않습니다. IO가 기록된 순서와는 별도로 복제된다는 사실은 여러 VHD에 걸쳐 데이터베이스가 분산되어 있는 데이터베이스 서버의 경우에는 지역에서 복제 기능이 의미가 없음을 나타냅니다. DBMS 외에도 다른 VHD에서 데이터를 쓰거나 조작하는 프로세스의 애플리케이션이나 변경 순서가 중요한 애플리케이션도 있을 수 있습니다. 이러한 경우가 요구되면 Azure의 지역에서 복제 기능을 사용하지 않도록 설정해야 합니다. 다른 VM 집합이 아닌 특정 VM 집합에 대해 지역에서 복제 기능이 필요한지 또는 이 기능을 원하는지에 따라, 이미 VM 및 관련된 VHD를 지역에서 복제 기능이 사용되거나 사용되지 않도록 설정된 다른 Storage 계정으로 분류할 수 있습니다.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>연결된 디스크에 대한 자동 탑재 설정
- - -
> ![Windows][Logo_Windows] Windows
>
> 자체 이미지나 디스크에서 만든 VM의 경우 automount 매개 변수를 확인하고 설정해야 합니다. 이 매개 변수를 설정하면 Azure에서 VM을 다시 시작하거나 재배포한 후에 VM이 연결/탑재된 드라이브를 자동으로 다시 탑재할 수 있습니다.
> Azure Marketplace에서 Microsoft가 제공하는 이미지의 경우 이 매개 변수가 설정되어 있습니다.
>
> 자동 탑재를 설정하려면 다음 항목에서 명령줄 실행 파일인 diskpart.exe의 설명서를 참조하세요.
>
> * [DiskPart 명령줄 옵션](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automount](https://technet.microsoft.com/library/cc753703.aspx)
>
> Windows 명령줄 창은 관리자 권한으로 열어야 합니다.
>
> 디스크를 연결하는 경우 VM에 로그인하여 Windows 디스크 관리자를 열어야 합니다. [연결된 디스크에 대한 자동 탑재 설정][planning-guide-5.5.3] 장에서 권장한 대로 자동 탑재를 사용하도록 설정하지 않으면 새로 연결된 볼륨을 온라인 상태로 만든 후 초기화해야 합니다.
>
> ![Linux][Logo_Linux] Linux
>
> [이 문서][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]에서 설명한 대로 새로 연결된 빈 디스크를 초기화해야 합니다.
> /etc/fstab에 새 디스크를 추가해야 합니다.
>
>

- - -
### <a name="final-deployment"></a>최종 배포

최종 배포 및 정확한 단계(특히 SAP 확장 모니터링의 배포와 관련된 정보)는 [배포 가이드][deployment-guide]를 참조하세요.

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Azure VM 내에서 실행되는 SAP 시스템에 액세스

SAP GUI를 사용하여 공용 인터넷을 통해 해당 시스템에 연결하려는 시나리오의 경우 다음 절차를 적용해야 합니다.

이 문서 뒷부분에서는 사이트 간 연결(VPN 터널) 또는 온-프레미스 시스템과 Azure 시스템 간의 Azure ExpressRoute 연결이 있는 프레미스 간 배포에서 SAP 시스템에 연결하는 다른 주요 시나리오가 나와 있습니다.

### <a name="remote-access-to-sap-systems"></a>SAP 시스템에 대한 원격 액세스

Azure Resource Manager를 사용할 경우 더 이상 이전의 클래식 모델과 같은 기본 엔드포인트가 없습니다. 다음과 같은 조건이 충족되면 Azure Resource Manager VM의 모든 포트는 열려 있습니다.

1. 서브넷 또는 네트워크 인터페이스에 대해 정의된 네트워크 보안 그룹이 없습니다. Azure VM으로의 네트워크 트래픽이 "네트워크 보안 그룹"을 통해 보호될 수 있습니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란?][virtual-networks-nsg]을 참조하세요.
2. 네트워크 인터페이스에 대해 정의된 Azure Load Balancer가 없습니다.   

[이 문서][virtual-machines-azure-resource-manager-architecture]에서 설명하는 클래식 모델과 ARM의 아키텍처 차이를 참조하세요.

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>인터넷을 통해 SAP 시스템 및 SAP GUI 연결 구성

이 항목에 대한 세부 정보를 설명하는 <https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx> 문서를 참조하세요.

#### <a name="changing-firewall-settings-within-vm"></a>VM 내의 방화벽 설정 변경

SAP 시스템으로의 인바운드 트래픽을 허용하도록 가상 머신에서 방화벽을 구성해야 할 수도 있습니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> 기본적으로 Azure 배포 VM 내의 Windows 방화벽은 켜져 있습니다. 이제 SAP 포트가 열리도록 허용해야 하며 그러지 않으면 SAP GUI에 연결할 수 없게 됩니다.
> 다음을 수행합니다.
>
> * 제어판\시스템 및 보안\Windows 방화벽의 **고급 설정**을 엽니다.
> * 인바운드 규칙을 마우스 오른쪽 단추로 클릭하고 **새 규칙**을 선택합니다.
> * 다음 마법사에서 새 **포트** 규칙을 만들도록 선택합니다.
> * 마법사의 다음 단계에서 TCP의 설정을 그대로 두고 열려는 포트 번호를 입력합니다. 여기서 SAP 인스턴스 ID는 00이므로 3200을 사용했습니다. 인스턴스의 인스턴스 번호가 다른 경우 이전에 해당 인스턴스 번호를 기준으로 정의한 포트가 열립니다.
> * 마법사의 다음 부분에서는 **연결 허용** 항목을 선택된 상태로 두어야 합니다.
> * 마법사의 다음 단계에서는 해당 규칙이 도메인, 개인 및 공용 네트워크에 적용될지 여부를 정의해야 합니다. 필요한 경우 요구에 맞게 조정합니다. 그러나 공용 네트워크를 통해 외부에서 SAP GUI에 연결하는 경우 해당 규칙을 공용 네트워크에 적용해야 합니다.
> * 마법사의 마지막 단계에서 규칙 이름을 지정하고 **마침**을 눌러 규칙을 저장합니다.
>
> 규칙은 즉시 적용됩니다.
>
> ![포트 규칙 정의][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Azure Marketplace의 Linux 이미지는 기본적으로 iptables 방화벽을 사용하도록 설정하지 않으며 SAP 시스템에 대한 연결이 작동합니다. Iptables 또는 다른 방화벽을 사용하도록 설정한 경우 iptables 또는 사용한 방화벽의 설명서를 참조하여 포트 32xx로의 인바운드 tcp 트래픽을 허용하세요(여기서 xx는 SAP 시스템의 시스템 번호임).
>
>

- - -
#### <a name="security-recommendations"></a>보안 권장 사항

SAP GUI는 실행 중인 어떤 SAP 인스턴스(포트 32xx)에도 직접 연결되지 않으며 먼저 열린 포트를 통해 SAP 메시지 서버 프로세스(포트 36xx)로 연결됩니다. 이전에는 애플리케이션 인스턴스에 대한 내부 통신을 위해 메시지 서버에서 동일한 포트가 사용되었습니다. 온-프레미스 애플리케이션 서버가 Azure의 메시지 서버와 우연히 통신하는 일을 방지하기 위해 통신 포트를 변경할 수 있습니다. SAP 메시지 서버와 해당 애플리케이션 인스턴스 간의 내부 통신을 온-프레미스 시스템에서 복제된 시스템(프로젝트 테스트를 위한 개발 복제본 등)의 다른 포트 번호로 변경할 것을 강력히 권장합니다. 이 작업은 기본 프로필 매개 변수를 사용하여 수행할 수 있습니다.

> rdisp/msserv_internal
>
>

에 설명 된 대로 [SAP Message Server에 대 한 보안 설정](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm)


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM

![Azure Cloud Services에 격리된 같은 VM 이름의 단일 VM SAP 데모 시스템 실행][planning-guide-figure-1700]

이 시나리오에서는 전체 학습/데모 시나리오가 단일 VM에 포함된 전형적인 학습/데모 시스템 시나리오를 구현하고 있습니다. VM 이미지 템플릿을 통해 배포했다고 가정합니다. 또한 이러한 여러 데모/학습 VM을 동일한 이름의 VM과 함께 배포해야 한다고 가정합니다. 전체 학습 시스템은 온-프레미스 자산에 연결 되어 있지 않은 및 하이브리드 배포에는 반대 됩니다.

여기서는 이 문서의 [Azure용 SAP로 VM 준비][planning-guide-5.2] 장 중 일부 섹션에서 설명한 대로 VM 이미지를 만들었다고 가정합니다.

시나리오를 구현하는 이벤트의 순서는 다음과 같습니다.

##### <a name="powershell"></a>PowerShell

* 모든 학습/데모 환경에 대한 새 리소스 그룹 만들기

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* 관리 디스크를 사용하지 않으려는 경우 새 저장소 계정 만들기

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* 모든 학습/데모 환경에 대해 새 가상 네트워크를 만들어 동일한 호스트 이름 및 IP 주소를 사용하도록 설정합니다. 가상 네트워크는 포트 3389로의 트래픽만 허용하여 SSH에 대해 원격 데스크톱 액세스 및 포트 22를 사용하도록 설정하는 네트워크 보안 그룹에 의해 보호됩니다.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* 인터넷에서 가상 컴퓨터에 액세스하는 데 사용할 수 있는 새 공용 IP 주소 만들기

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* 가상 컴퓨터에 대한 새 네트워크 인터페이스 만들기

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* 가상 머신을 만듭니다. 이 시나리오의 경우 모든 VM이 동일한 이름을 갖습니다. 해당 VM에 있는 SAP NetWeaver 인스턴스의 SAP SID도 동일하게 유지됩니다. Azure 리소스 그룹 내에서 VM의 이름은 고유해야 하지만 서로 다른 Azure 리소스 그룹에서는 동일한 이름의 VM을 실행할 수 있습니다. Windows의 기본 '관리자' 계정 또는 Linux의 '루트'는 유효하지 않습니다. 따라서 새 관리자 사용자 이름을 암호와 함께 정의해야 합니다. 또한 VM의 크기를 정의해야 합니다.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* 필요에 따라 디스크를 더 추가하고 필요한 콘텐츠를 복원합니다. 모든 Blob 이름(Blob URL)은 Azure 내에서 고유해야 합니다.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>CLI

다음 코드 예제는 Linux에서 사용할 수 있습니다. Windows의 경우 위에서 설명한 것처럼 PowerShell을 사용하거나, 예제를 조정하여 $rgName 대신 %rgName%을 사용하고 Windows 명령 *set*를 사용하여 환경 변수를 설정합니다.

* 모든 학습/데모 환경에 대한 새 리소스 그룹 만들기

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* 새 저장소 계정 만들기

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* 모든 학습/데모 환경에 대해 새 가상 네트워크를 만들어 동일한 호스트 이름 및 IP 주소를 사용하도록 설정합니다. 가상 네트워크는 포트 3389로의 트래픽만 허용하여 SSH에 대해 원격 데스크톱 액세스 및 포트 22를 사용하도록 설정하는 네트워크 보안 그룹에 의해 보호됩니다.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* 인터넷에서 가상 컴퓨터에 액세스하는 데 사용할 수 있는 새 공용 IP 주소 만들기

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* 가상 컴퓨터에 대한 새 네트워크 인터페이스 만들기

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* 가상 머신을 만듭니다. 이 시나리오의 경우 모든 VM이 동일한 이름을 갖습니다. 해당 VM에 있는 SAP NetWeaver 인스턴스의 SAP SID도 동일하게 유지됩니다. Azure 리소스 그룹 내에서 VM의 이름은 고유해야 하지만 서로 다른 Azure 리소스 그룹에서는 동일한 이름의 VM을 실행할 수 있습니다. Windows의 기본 '관리자' 계정 또는 Linux의 '루트'는 유효하지 않습니다. 따라서 새 관리자 사용자 이름을 암호와 함께 정의해야 합니다. 또한 VM의 크기를 정의해야 합니다.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* 필요에 따라 디스크를 더 추가하고 필요한 콘텐츠를 복원합니다. 모든 Blob 이름(Blob URL)은 Azure 내에서 고유해야 합니다.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Template

샘플 템플릿은 GitHub에서 azure 빠른 시작-템플릿 리포지토리에서 사용할 수 있습니다.

* [간단한 Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [간단한 Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [이미지의 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Azure 내에서 통신하는 VM 집합 구현

하이브리드가 아닌 이 시나리오는 데모/학습 시나리오를 나타내는 소프트웨어가 여러 VM에 걸쳐 분산되어 있는 학습 및 데모용 일반 시나리오입니다. 여러 VM에 설치된 다양한 구성 요소는 서로 통신해야 합니다. 또한 이 시나리오에서는 온-프레미스 네트워크 통신이나 크로스-프레미스 시나리오가 필요하지 않습니다.

이 시나리오는 이 문서의 [SAP NetWeaver 데모/학습 시나리오가 있는 단일 VM][planning-guide-7.1] 장에서 설명한 설치의 확장입니다. 이 경우 더 많은 가상 머신이 기존 리소스 그룹에 추가됩니다. 다음 예제에서 교육 환경은 SAP ASCS/SCS VM, DBMS가 실행되는 VM 및 SAP 애플리케이션 서버 인스턴스 VM으로 구성됩니다.

이 시나리오를 작성하기 전에 시나리오에서 이미 실행한 기본 설정을 고려해야 합니다.

#### <a name="resource-group-and-virtual-machine-naming"></a>리소스 그룹 및 Virtual Machine 이름 지정

모든 리소스 그룹 이름은 고유해야 합니다. `<rg-name`>-접미사와 같은 리소스의 고유한 이름 지정 체계를 개발합니다.

가상 머신 이름은 리소스 그룹 내에서 고유해야 합니다.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>여러 VM 간의 통신을 위한 네트워크 설정

![Azure Virtual Network 내의 VM 집합][planning-guide-figure-1900]

동일한 학습/데모 환경의 복제본과의 이름 충돌을 방지하려면 모든 환경에 대해 Azure Virtual Network를 만들어야 합니다. Azure에서 제공하는 DNS 이름 확인을 사용하거나 Azure 외부에서 자체 DNS 서버를 구성할 수도 있습니다(여기서는 자세히 다루지 않음). 이 시나리오에서는 자체 DNS를 구성하지 않습니다. 단일 Azure Virtual Network 내의 모든 가상 머신의 경우 호스트 이름을 통한 통신이 사용되도록 설정됩니다.

단지 리소스 그룹만이 아니라 가상 네트워크를 기준으로 학습 또는 데모 환경을 구분하는 이유는 다음과 같습니다.

* 설정된 SAP 지형에는 자체 AD/OpenLDAP가 필요하고 도메인 서버는 각 지형의 일부가 되어야 합니다.  
* 설정된 SAP 지형에는 고정된 IP 주소를 사용해야 하는 구성 요소가 있습니다.

Azure Virtual Networks 및 이러한 네트워크를 정의하는 방법에 대한 자세한 내용은 [이 문서][virtual-networks-create-vnet-arm-pportal]에 있습니다.

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>회사 네트워크 연결을 사용하여 SAP VM 배포(프레미스 간)

SAP 지형을 실행하고 고급 DBMS 서버용 운영 체제 미설치 영역, 애플리케이션 계층 및 보다 작은 2계층으로 구성된 SAP 시스템과 Azure IaaS에 대한 온-프레미스 가상화 환경 간에 배포를 나눌 수 있습니다. 기본 가정은 단일 SAP 지형 내의 SAP 시스템은 배포 형식에 관계없이 서로 간에, 그리고 회사에 배포된 여러 다른 소프트웨어 구성 요소와 통신해야 한다는 것입니다. SAP GUI를 사용하여 연결하는 최종 사용자와 다른 인터페이스를 사용하여 연결하는 사용자는 배포 형식에 따른 차이를 느끼지 못해야 합니다. 이러한 조건은 온-프레미스 Active Directory/OpenLDAP 및 DNS 서비스를 사이트 간/다중 사이트 연결 또는 개인 연결(예: Azure ExpressRoute)을 통해 Azure 시스템으로 확장할 때만 충족될 수 있습니다.



### <a name="scenario-of-an-sap-landscape"></a>SAP 지형 시나리오

크로스-프레미스 또는 하이브리드 시나리오는 아래 그래픽과 같이 대략적으로 나타낼 수 있습니다.

![온-프레미스와 Azure 자산 간의 사이트 간 연결][planning-guide-figure-2100]

위에 나와 있는 시나리오는 시나리오를 설명 합니다. 여기서 온-프레미스

최소 요구 사항은 Azure 서비스의 브라우저 액세스를 위해서는 SSL/TLS, 시스템 액세스를 위해서는 VPN 기반 연결과 같은 보안 통신 프로토콜의 사용입니다. 단, 회사에서는 회사 네트워크와 Azure 간의 VPN 연결을 다르게 처리하는 것을 전제로 합니다. 일부 회사에서는 모든 포트를 완전히 열어둘 수 있습니다. 또 다른 회사에서는 열어야 하는 포트를 정확하게 지정하려고 할 수 있습니다.

아래 표에는 일반적인 SAP 통신 포트가 나와 있습니다. 기본적으로 SAP 게이트웨이 포트만 열어도 충분합니다.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| 서비스 | 포트 이름 | 예: `<nn`> = 01 | 기본 범위(최소-최대) | 주석 |
| --- | --- | --- | --- | --- |
| 디스패처 |sapdp`<nn>` 참조: * |3201 |3200 - 3299 |SAP 디스패처, Windows 및 Java용 SAP GUI에서 사용 |
| 메시지 서버 |sapms`<sid`> 참조: ** |3600 |제한 없는 sapms`<anySID`> |sid = SAP-System-ID |
| 게이트웨이 |sapgw`<nn`> 참조: * |3301 |제한 없음 |SAP 게이트웨이, CPIC 및 RFC 통신에 사용 |
| SAP 라우터 |sapdp99 |3299 |제한 없음 |CI(중앙 인스턴스) 서비스 이름만 설치한 후에 /etc/services에서 임의 값으로 재할당될 수 있습니다. |

*) nn = SAP 인스턴스 번호

**) sid = SAP-System-ID

여러 SAP 제품에 필요한 포트 또는 SAP 제품 서비스에 대한 자세한 내용은 여기 <https://scn.sap.com/docs/DOC-17124>에서 확인할 수 있습니다.
이 문서를 사용할 경우 특정 SAP 제품 및 시나리오에 필요한 VPN 디바이스에서 전용 포트를 열 수 있습니다.

이러한 시나리오에서 VM을 배포할 경우 다른 보안 조치는 [네트워크 보안 그룹][virtual-networks-nsg]을 만들어 액세스 규칙을 정의할 수 있습니다.

### <a name="dealing-with-different-virtual-machine-series"></a>다른 Virtual Machine 시리즈 처리

Microsoft는 vCPU 수, 메모리 또는 좀 더 중요하게 VM이 실행되는 하드웨어가 다른 VM 유형을 추가했습니다. 이러한 모든 VM이 SAP에서 지원되는 것은 아닙니다(SAP 정보 [1928533]에서 지원되는 VM 형식 참조). 해당 VM 중 일부는 다른 호스트 하드웨어 세대에서 실행됩니다. 이러한 호스트 하드웨어 세대는 Azure 확장 단위 세분성에 따라 배포됩니다. 선택한 다른 형식의 VM을 같은 확장 단위로 실행할 수 없는 경우가 발생할 수 있습니다. 가용성 집합은 다른 하드웨어를 기반으로 확장 단위를 넓히는 능력이 제한되어 있습니다.  예를 들어 HA 구성에서 보조 DBMS 인스턴스를 실행하는 VM과 함께 가용성 집합에 있는 E64s_v3 VM에서 SAP DBMS 계층을 실행하는 경우 VM을 업그레이드할 수 있으므로 M 시리즈 VM으로 보조 VM을 중지하고 다시 시작할 수 없습니다. 이유는 M 시리즈 VM 및 Ev3 시리즈 VM이 다른 하드웨어 및 다른 확장 단위에서 실행 중이기 때문입니다. 새 가용성 집합을 만들고, 저장소를 삭제하지 않고 보조 Ev3 시리즈 VM을 삭제하고, M 시리즈 VM으로 VM을 새 가용성 집합에 다시 배포해야 합니다.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Azure의 SAP 인스턴스에서 로컬 네트워크 프린터로 인쇄

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>프레미스 간 시나리오에서 TCP/IP를 통해 인쇄

Azure VM에서 온-프레미스 TCP/IP 기반 네트워크 프린터를 설정하는 것은 VPN 사이트 간 터널 또는 ExpressRoute 연결을 설정했다고 가정할 경우 회사 네트워크에서 작업하는 경우와 전반적으로 동일합니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> 다음을 수행합니다.
>
> * 일부 네트워크 프린터에는 Azure VM에서 프린터를 쉽게 설정할 수 있도록 하는 구성 마법사가 포함되어 있습니다. 마법사 소프트웨어가 프린터와 함께 배포되지 않은 경우 프린터를 설정하는 "수동" 방법은 새 TCP/IP 프린터 포트를 만드는 것입니다.
> * [제어판] -&gt; [디바이스 및 프린터] -&gt; [프린터 추가] 열기
> * TCP/IP 주소 또는 호스트 이름을 사용하여 프린터 추가 선택
> * 프린터의 IP 주소 입력
> * 프린터 포트 표준 9100
> * 필요한 경우 적절한 프린터 드라이버를 수동으로 설치합니다.
>
> ![Linux][Logo_Linux] Linux
>
> * Windows의 경우처럼 네트워크 프린터를 설치하는 표준 절차를 따릅니다.
> * [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) 또는 [Red Hat 및 Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html)의 공용 Linux 가이드에서 프린터 추가 방법을 따르세요.
>
>

- - -
![네트워크 인쇄][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>프레미스 간 시나리오에서 SMB를 통한 호스트 기반 프린터(공유 프린터)

호스트 기반 프린터는 기본적으로 네트워크와 호환되지 않습니다. 그러나 전원이 켜진 컴퓨터에 프린터가 연결되기만 하면 네트워크 상의 컴퓨터 간에 호스트 기반 프린터가 공유될 수 있습니다. 회사 네트워크를 사이트 간 또는 ExpressRoute에 연결하고 로컬 프린터를 공유합니다. SMB 프로토콜은 이름 서비스로 DNS가 아닌 NetBIOS를 사용합니다. NetBIOS 호스트 이름은 DNS 호스트 이름과 다를 수 있습니다. NetBIOS 호스트 이름과 DNS 호스트 이름이 동일한 경우가 일반적인 경우입니다. DNS 도메인은 NetBIOS 네임스페이스에서는 의미가 없습니다. 따라서 DNS 호스트 이름과 DNS 도메인으로 이루어진 정규화된 DNS 호스트 이름은 NetBIOS 네임스페이스에서 사용하지 말아야 합니다.

프린터 공유는 네트워크에서 고유한 이름으로 식별됩니다.

* SMB 호스트의 호스트 이름(항상 필수)
* 공유의 이름(항상 필수)
* 프린터 공유가 SAP 시스템과 같은 도메인에 있지 않은 경우 도메인의 이름
* 또한 프린터 공유에 액세스하기 위해 사용자 이름과 암호가 필요할 수 있습니다.

방법:

- - -
> ![Windows][Logo_Windows] Windows
>
> 로컬 프린터를 공유합니다.
> Azure VM에서 Windows 탐색기를 열고 프린터의 공유 이름을 입력합니다.
> 프린터 설치 마법사가 설치 프로세스를 안내합니다.
>
> ![Linux][Logo_Linux] Linux
>
> 다음은 Linux의 네트워크 프린터 구성에 대한 설명서의 몇 가지 예입니다. 여기에는 Linux의 인쇄에 대한 장도 포함되어 있습니다. VM이 VPN에 속할 경우 Azure Linux VM의 경우와 동일한 방식으로 작동합니다.
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL 또는 Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>USB 프린터(프린터 전달)

Azure에서는 원격 세션에서 사용자가 로컬 프린터 디바이스에 액세스할 수 있도록 하는 원격 데스크톱 서비스의 기능을 사용할 수 없습니다.

- - -
> ![Windows][Logo_Windows] Windows
>
> Windows를 통한 인쇄에 대한 자세한 내용은 <https://technet.microsoft.com/library/jj590748.aspx>를 참조하세요.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>프레미스 간에 SAP Azure 시스템을 Correction and Transport System(TMS)에 통합

SAP Change and Transport System(TMS)은 지형 내에서 시스템 간에 전송 요청을 내보내고 가져오도록 구성되어야 합니다. QA(품질 보증) 및 PRD(생산 시스템)은 온-프레미스에 있지만 SAP 시스템의 DEV(개발) 인스턴스는 Azure에 있다고 가정해 보겠습니다. 또한 중앙 전송 디렉터리가 있다고 가정합니다.

##### <a name="configuring-the-transport-domain"></a>전송 도메인 구성

[전송 도메인 컨트롤러 구성](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)에 설명된 대로 전송 도메인 컨트롤러로 지정한 시스템에서 전송 도메인을 구성합니다. 시스템 사용자 TMSADM이 만들어지고 필수 RFC 대상이 생성됩니다. 트랜잭션 SM59를 사용하여 이러한 RFC 연결을 확인할 수 있습니다. 전송 도메인 전체에서 호스트 이름 확인을 사용할 수 있어야 합니다.

방법:

* 이 시나리오에서는 온-프레미스 QAS 시스템을 CTS 도메인 컨트롤러로 결정했습니다. 트랜잭션 STMS를 호출합니다. TMS 대화 상자가 나타납니다. 전송 도메인 구성 대화 상자가 표시됩니다. 이 대화 상자는 전송 도메인을 아직 구성하지 않은 경우에만 나타납니다.
* 자동으로 만들어진 사용자 TMSADM에 권한이 부여되었는지 확인합니다(SM59 -> ABAP 연결 -> TMSADM@E61.DOMAIN_E61 -> 세부 정보 -> 유틸리티(M) -> 권한 부여 테스트). 여기에 표시된 것처럼 트랜잭션 STMS의 초기 화면에는 이 SAP 시스템이 전송 도메인의 컨트롤러로 작동하는지 표시되어야 합니다.

![도메인 컨트롤러의 트랜잭션 STMS 초기 화면][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>전송 도메인에 SAP 시스템 포함

전송 도메인에 SAP 시스템을 포함하는 순서는 다음과 같습니다.

* Azure의 DEV 시스템에서 전송 시스템(클라이언트 000)으로 이동한 다음 트랜잭션 STMS를 호출합니다. 대화 상자에서 기타 구성을 선택하고 도메인에 시스템 포함 작업을 계속 진행합니다. 도메인 컨트롤러를 대상 호스트로 지정합니다([전송 도메인에 SAP 시스템 포함](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). 이제 시스템은 전송 도메인에 포함될 때까지 대기합니다.
* 보안상의 이유로 도메인 컨트롤러로 다시 돌아가 사용자 요청을 확인해야 합니다. 대기 중인 시스템의 시스템 개요 및 승인을 선택합니다. 그런 후 메시지를 확인하면 구성이 배포됩니다.

이제 이 SAP 시스템에는 전송 도메인의 다른 모든 SAP 시스템에 대해 필요한 정보가 포함됩니다. 동시에, 새 SAP 시스템의 주소 데이터가 다른 모든 SAP 시스템으로 전송되고 SAP 시스템은 전송 제어 프로그램의 전송 프로필에 입력됩니다. 도메인의 전송 디렉터리에 대한 액세스와 RFC가 작동하는지 여부를 확인합니다.

[시스템 변경 및 전송](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)설명서에 설명된 대로 전송 시스템 구성을 계속 진행합니다.

방법:

* 온-프레미스의 STMS가 올바르게 구성되어 있는지 확인합니다.
* 전송 도메인 컨트롤러의 호스트 이름을 Azure에서 가상 머신으로 확인하고 그 반대로도 확인할 수 있는지 검토합니다.
* 트랜잭션 STMS 호출 -> 기타 구성 -> 도메인에 시스템 포함
* 온-프레미스 TMS 시스템의 연결을 확인합니다.
* 전송 경로, 그룹 및 계층을 일반적인 방식으로 구성합니다.

사이트 간에 연결된 크로스-프레미스 시나리오에서 온-프레미스와 Azure 간의 대기 시간이 여전히 길 수 있습니다. 개발 및 테스트 시스템에서 프로덕션으로 개체를 전송하는 순서를 따르거나 전송 또는 지원 패키지를 다른 시스템에 적용하려는 경우 중앙 전송 디렉터리의 위치에 따라, 일부 시스템에서 중앙 전송 디렉터리에서 데이터를 읽거나 쓸 때 긴 대기 시간이 발생합니다. 이러한 상황은 데이터 센터 간에 멀리 떨어져 있는 서로 다른 데이터 센터에 여러 다른 시스템이 분산되어 있는 SAP 지형 구성과 유사합니다.

이러한 대기 시간 문제를 해결하고 시스템이 더 빠르게 전송 디렉터리에서 읽거나 전송 디렉터리로 쓸 수 있도록 하려면 두 개의 STMS 전송 도메인(온-프레미스용 1개와 Azure의 시스템을 포함하는 1개)을 설정하고 전송 도메인을 연결할 수 있습니다. SAP TMS에서 이 개념의 기본 원칙을 설명하는 <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm> 설명서를 참조하세요.

방법:

* 트랜잭션 STMS를 사용하여 각 위치(온-프레미스 및 Azure)에서 전송 도메인 설정 <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* 도메인 링크를 사용하여 도메인을 연결하고 두 도메인 간의 연결을 확인합니다.
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* 연결된 시스템으로 구성을 배포합니다.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>Azure 및 온-프레미스에 있는 SAP 인스턴스 간의 RFC 트래픽(프레미스 간)

온-프레미스 및 Azure에 있는 시스템 간에 RFC 트래픽이 작동해야 합니다. 연결을 설정하려면 대상 시스템에 대해 RFC 연결을 정의해야 하는 원본 시스템에서 트랜잭션 SM59를 호출합니다. 해당 구성은 RFC 연결의 표준 설정과 비슷합니다.

프레미스 간 시나리오에서는 서로 통신해야 하는 SAP 시스템을 실행하는 VM이 동일한 도메인에 있다고 가정합니다. 따라서 SAP 시스템 간의 RFC 연결을 설정하는 작업이 온-프레미스 시나리오의 설정 단계 및 입력과 다르지 않습니다.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Azure에 있는 SAP 인스턴스의 '로컬' 파일 공유에 액세스하기 또는 그 반대로 액세스하기

Azure에 있는 SAP 인스턴스는 회사 프레미스 내에 있는 파일 공유에 액세스해야 합니다. 또한 온-프레미스 SAP 인스턴스는 Azure에 있는 파일 공유에 액세스해야 합니다. 파일 공유를 사용하도록 설정하려면 로컬 시스템에서 사용 권한 및 공유 옵션을 구성해야 합니다. VPN의 포트 또는 Azure와 데이터 센터 간의 ExpressRoute 연결을 열어야 합니다.

## <a name="supportability"></a>지원 가능성

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>SAP용 Azure 모니터링 솔루션

Azure에서 업무에 중요한 SAP 시스템의 모니터링을 사용하도록 설정하기 위해 SAP 모니터링 도구인 SAPOSCOL 또는 SAP 호스트 에이전트가 SAP용 Azure 모니터링 확장을 통해 Azure Virtual Machine 서비스 호스트에서 데이터를 가져옵니다. SAP의 요구는 SAP 애플리케이션에 따라 크게 달라지므로 Microsoft는 일반적으로 Azure에 필요한 기능을 구현하지 않기로 결정했지만, 고객이 Azure에서 실행되는 Virtual Machines에 필요한 모니터링 구성 요소와 구성을 배포할 수 있도록 허용합니다. 그러나 모니터링 구성 요소의 배포 및 수명 주기 관리는 대부분 Azure에서 자동으로 수행됩니다.

#### <a name="solution-design"></a>솔루션 디자인

SAP 모니터링을 사용 가능하도록 하기 위해 개발된 솔루션은 Azure VM 에이전트 및 확장 프레임워크의 아키텍처를 기반으로 합니다. Azure VM 에이전트 및 확장 프레임워크는 VM 내에 있는 Azure VM 확장 갤러리에서 소프트웨어 애플리케이션을 설치할 수 있도록 허용합니다. 이 개념의 원리는 특수한 기능을 VM에 배포하고 배포 시에 이러한 소프트웨어를 구성할 수 있도록 하는 것입니다(SAP용 Azure 모니터링 확장과 같음).

Azure Portal에서 VM을 생성할 때 기본적으로 VM 내의 특정 Azure VM 확장을 처리할 수 있도록 하는 'Azure VM 에이전트'가 Windows에 주입됩니다. SUSE, Red Hat 또는 Oracle Linux의 경우 VM 에이전트가 이미 Azure Marketplace 이미지의 일부입니다. Linux VM을 온-프레미스에서 Azure로 업로드하는 경우 VM 에이전트를 수동으로 설치해야 합니다.

Azure에서 SAP용 모니터링 솔루션의 기본 구성 요소는 다음과 같습니다.

![Microsoft Azure 확장 구성 요소][planning-guide-figure-2400]

위의 블록 다이어그램에 나와 있는 것처럼 SAP용 모니터링 솔루션의 한 부분은 Azure VM 이미지 및 Azure Operations에 의해 관리되는 전역적으로 복제 리포지토리인 Azure 확장 갤러리에서 호스트됩니다. Azure Operations를 사용하여 SAP용 Azure 모니터링 확장의 새 버전을 게시하는 것은 Azure의 SAP 구현과 관련해서 작업하는 공동 SAP/MS 팀의 책임입니다.

새 Windows VM을 배포하는 경우 'Azure VM 에이전트'가 VM에 자동으로 추가됩니다. 이 에이전트의 기능은 SAP NetWeaver Systems의 모니터링을 위해 Azure 확장의 로드 및 구성을 조정하는 것입니다. Linux VM의 경우 Azure VM 에이전트가 이미 Azure Marketplace OS 이미지의 일부입니다.

그러나 고객이 실행해야 하는 단계는 여전히 남아 있습니다. 바로 성능 컬렉션의 활성화 및 구성입니다. '구성' 관련 프로세스는 PowerShell 스크립트 또는 CLI 명령에 의해 자동화됩니다. PowerShell 스크립트는 [배포 가이드][deployment-guide]에서 설명한 대로 Microsoft Azure 스크립트 센터에서 다운로드할 수 있습니다.

SAP용 Azure 모니터링 솔루션의 전반적인 아키텍처는 다음과 같습니다.

![SAP NetWeaver용 Azure 모니터링 솔루션][planning-guide-figure-2500]

**배포 중에 이러한 PowerShell cmdlet 또는 CLI 명령을 사용하는 정확한 방법 및 자세한 단계는 [배포 가이드][deployment-guide]에서 제공하는 지침을 따르세요.**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Azure 배치 SAP 인스턴스를 SAProuter에 통합

Azure에서 실행되는 SAP 인스턴스도 SAProuter에서 액세스할 수 있어야 합니다.

![SAP-라우터 네트워크 연결][planning-guide-figure-2600]

SAProuter를 사용하면 직접적인 IP 연결이 없는 경우에도 참여 시스템 간에 TCP/IP 통신이 가능해집니다. 이를 통해 네트워크 수준에서 통신 파트너 간의 종단 간 연결이 없어도 된다는 이점을 얻게 됩니다. SAProuter는 기본적으로 3299 포트에서 수신 대기합니다.
SAProuter를 통해 SAP 인스턴스를 연결하려면 연결을 시도할 때 SAProuter 문자열과 호스트 이름을 지정해야 합니다.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

지금까지 이 문서는 일반적으로 SAP NetWeaver 또는 SAP NetWeaver ABAP 스택에 중점을 두었습니다. 이 작은 섹션에서는 SAP Java 스택에 대한 특별한 고려 사항이 표시됩니다. SAP NetWeaver Java만을 기반으로 하는 가장 중요한 애플리케이션 중 하나는 SAP Enterprise Portal입니다. SAP PI 및 SAP Solution Manager와 같은 기타 SAP NetWeaver 기반 애플리케이션은 SAP NetWeaver ABAP 및 Java 스택을 모두 사용합니다. 따라서 SAP NetWeaver Java 스택과 관련된 특정 측면도 반드시 고려해야 합니다.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

크로스-프레미스 시나리오에서 배포하는 경우 Azure Virtual Machine의 SAP Portal 설정은 온-프레미스 설치와 다르지 않습니다. DNS는 온-프레미스에 의해 수행되므로 개별 인스턴스의 포트 설정은 구성된 온-프레미스로 수행될 수 있습니다. 지금까지 이 문서에 설명된 권장 사항 및 제한 사항은 일반적으로 SAP Enterprise Portal 또는 SAP NetWeaver Java 스택과 같은 애플리케이션에 적용됩니다.

![공개된 SAP 포털][planning-guide-figure-2700]

가상 머신 호스트가 사이트 간 VPN 터널 또는 ExpressRoute 통해 회사 네트워크에 연결되어 있는 동안 일부 고객의 특별한 배포 시나리오에서 SAP Enterprise Portal이 인터넷에 직접 노출됩니다. 이러한 시나리오의 경우 특정 포트가 열려 있고 방화벽이나 네트워크 보안 그룹에 의해 차단되지 않았는지 확인해야 합니다. 

초기 포털 URI는 http (s):`<Portalserver`>: 5XX00/irj 포트에서 SAP 별로 설명 된 대로 형식이 있는 <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>합니다.

![엔드포인트 구성][planning-guide-figure-2800]

SAP Enterprise Portal의 URL 및/또는 포트를 사용자 지정하려는 경우 다음 설명서를 확인하세요.

* [포털 URL 변경](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [기본 포트 번호, 포털 포트 번호 변경](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Azure Virtual Machines에서 실행되는 SAP NetWeaver의 HA(고가용성) 및 DR(재해 복구)

### <a name="definition-of-terminologies"></a>용어 정의

용어 **HA(고가용성)** 는 일반적으로 **동일한** 데이터 센터 내의 중복성, 내결함성 또는 장애 조치(failover) 보호 구성 요소를 통해 IT 서비스의 비즈니스 연속성을 제공함으로써 IT 작업 중단을 최소화하는 일련의 기술과 관련되어 있습니다. 우리의 경우에는 단일 Azure 지역 내부에서의 고가용성이 고려됩니다.

**DR(재해 복구)** 또한 IT 서비스 중단을 최소화하고 복구를 수행하는 것을 목표로 하지만 일반적으로 수백 킬로미터 거리에 있는 **여러 다른** 데이터 센터 간에 발생하게 됩니다. 우리의 경우에는 동일한 지정학적 지역 내의 다양한 Azure 지역 간이나 고객이 설정한 Azure 지역 사이를 의미합니다.

### <a name="overview-of-high-availability"></a>고가용성 개요

Azure의 SAP 고가용성에 대한 논의는 다음 두 부분으로 구분할 수 있습니다.

* **Azure 인프라 고가용성** - 계산(VM), 네트워크, 저장소 등의 HA와 이러한 HA가 SAP 애플리케이션 가용성 향상에 주는 이점
* **SAP 애플리케이션 고가용성** - 다음과 같은 SAP 소프트웨어 구성 요소의 HA
  * SAP 애플리케이션 서버
  * SAP ASCS/SCS 인스턴스
  * DB 서버

및 Azure 인프라 HA와 결합되는 방식

Azure의 SAP 고가용성은 온-프레미스 물리적 또는 가상 환경의 SAP 고가용성에 비해 몇 가지 차이점이 있습니다. SAP의 다음 백서에서는 가상화된 Windows 환경의 표준 SAP 고가용성 구성에 대해 설명합니다. <https://scn.sap.com/docs/DOC-44415> Windows의 경우와 마찬가지로 Linux용 sapinst 통합 SAP-HA 구성은 없습니다. Linux용 온-프레미스 SAP HA에 대한 자세한 내용은 <https://scn.sap.com/docs/DOC-8541>에서 확인할 수 있습니다.

### <a name="azure-infrastructure-high-availability"></a>Azure 인프라 고가용성

현재 단일 VM SLA는 99.9%입니다. 다양한 Azure SLA의 제품을 빌드해 보면 단일 VM의 가용성이 어떻게 나타나는지 이해하는 데 도움이 됩니다. <https://azure.microsoft.com/support/legal/sla/>

이 계산은 한 달이 30일 또는 43200분이라는 기준에서 출발합니다. 따라서 가동 중지 시간 0.05%는 21.6분에 해당합니다. 일반적으로 다양한 서비스의 가용성은 다음과 같이 배가됩니다.

(가용성 서비스 #1/100) * (가용성 서비스 #2/100) * (가용성 서비스 #3/100)  

결과는 다음과 같습니다.

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975 또는 전체 가용성 99.75%

#### <a name="virtual-machine-vm-high-availability"></a>VM(Virtual Machine) 고가용성

가상 머신의 가용성에 영향을 줄 수 있는 두 가지 유형의 Azure 플랫폼 이벤트인 계획된 유지 관리와 계획되지 않은 유지 관리가 있습니다.

* 계획된 유지 관리 이벤트는 가상 머신이 실행 중인 플랫폼 인프라의 전체적인 안정성, 성능 및 보안을 향상시키기 위해 Microsoft가 기본 Azure 플랫폼에 적용하는 주기적인 업데이트입니다.
* 계획되지 않은 유지 관리 이벤트는 가상 컴퓨터의 기반이 되는 하드웨어 또는 물리적 인프라에 어떠한 식으로든지 오류가 있을 때 발생합니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지될 때 Azure 플랫폼은 가상 머신을 호스트 중인 비정상 물리적 서버에서 정상 물리적 서버로 가상 머신을 자동으로 마이그레이션합니다. 이러한 이벤트는 흔치 않지만 가상 머신이 재부팅되도록 할 수도 있습니다.

자세한 내용은 <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability> 설명서를 참조하세요.

#### <a name="azure-storage-redundancy"></a>Azure Storage 중복성

Microsoft Azure Storage 계정의 데이터는 항상 내구성 및 고가용성을 보증하도록 복제되며 일시적인 하드웨어 오류가 발생하는 경우에도 Azure Storage SLA를 충족합니다.

Azure Storage는 기본적으로 데이터 이미지 3개를 유지하므로 여러 Azure 디스크에 있는 RAID5 또는 RAID1은 필요하지 않습니다.

자세한 내용은 <https://azure.microsoft.com/documentation/articles/storage-redundancy/> 문서를 참조하세요.

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Azure 인프라 VM 다시 시작을 활용하여 SAP 애플리케이션의 "고가용성" 확보

WSFC(Windows 서버 장애 조치(failover) 클러스터링) 또는 Linux의 Peacemaker(현재 SLES 12 이상에서만 지원됨) 등의 기능을 사용하지 않기로 결정하는 경우 Azure VM 다시 시작 기능을 활용하여 Azure 물리적 서버 인프라 및 전반적인 기본 Azure 플랫폼의 예정되었거나 갑작스러운 가동 중지 시간으로부터 SAP 시스템을 보호합니다.

> [!NOTE]
> Azure VM 다시 시작 기능은 애플리케이션이 아닌 VM을 일차적으로 보호합니다. VM 다시 시작 기능은 SAP 애플리케이션의 고가용성을 제공하지 않지만 특정 수준의 인프라 가용성을 제공하므로 간접적으로 SAP 시스템의 "고가용성"을 제공합니다. 또한 예정되었거나 갑작스러운 호스트 중단 후 VM을 다시 시작하는 데 걸리는 시간에 대한 SLA는 없습니다. 따라서 이러한 방식의 '고가용성'은 (A)SCS 또는 DBMS와 같은 SAP 시스템의 중요한 구성 요소에 적합하지 않습니다.
>
>

고가용성을 위한 또 다른 중요한 인프라 요소는 저장소입니다. 예를 들어 Azure Storage SLA에 따른 가용성은 99.9%입니다. 디스크가 있는 모든 VM을 단일 Azure Storage 계정에 배포하는 경우 Azure Storage를 사용할 수 없게 되어 해당 Azure Storage 계정에 배치된 모든 VM을 사용할 수 없게 되고 해당 VM 내에서 실행되는 모든 SAP 구성 요소로 사용할 수 없게 될 수 있습니다.  

모든 VM을 단일 Azure Storage 계정에 두는 대신, 각 VM에 대해 전용 Storage 계정을 사용할 수 있습니다. 이러한 방식에서는 여러 독립적인 Azure Storage 계정을 사용하여 전반적인 VM 및 SAP 애플리케이션 가용성이 증가하게 됩니다.

Azure 관리 디스크는 연결된 가상 머신의 장애 도메인에 자동으로 배치됩니다. 가용성 집합에 가상 머신 두 대를 배치하고 관리 디스크를 사용하는 경우 플랫폼에서 각기 다른 장애 도메인으로 관리 디스크를 분산시키는 작업도 수행합니다. Premium Storage를 사용하려는 경우에는 관리 디스크도 사용하는 것이 좋습니다.

Azure 인프라 HA 및 Storage 계정을 사용하는 SAP NetWeaver 시스템의 샘플 아키텍처는 다음과 같습니다.

![HA Azure 인프라를 활용하여 SAP 애플리케이션의 "더 높은" 가용성 확보][planning-guide-figure-2900]

Azure 인프라 HA 및 관리 디스크를 사용하는 SAP NetWeaver 시스템의 샘플 아키텍처는 다음과 같습니다.

![HA Azure 인프라를 활용하여 SAP 애플리케이션의 "더 높은" 가용성 확보][planning-guide-figure-2901]

중요한 SAP 구성 요소에 대해 현재까지 다음이 지원됩니다.

* SAP AS(애플리케이션 서버)의 고가용성

  SAP 애플리케이션 서버 인스턴스는 중복 구성 요소입니다. 각 SAP AS 인스턴스는 다른 Azure 장애 도메인 및 업그레이드 도메인([장애 도메인][planning-guide-3.2.1] 및 [업그레이드 도메인][planning-guide-3.2.2] 장 참조)에서 실행되는 자체 VM에 배포됩니다. 이는 Azure 가용성 집합을 사용하여 보장됩니다([Azure 가용성 집합][planning-guide-3.2.3] 장 참조). 각 SAP AS 인스턴스는 자체 Azure 저장소 계정에 배치됩니다.

  Azure Storage 계정 하나를 사용할 수 없게 되면 해당 SAP AS가 있는 단일 VM을 사용할 수 없게 됩니다. 그러나 단일 Azure 구독 내에서는 Azure Storage 계정 수가 제한됩니다. VM을 다시 부팅한 후 (A)SCS 인스턴스가 자동으로 시작되도록 하려면 [SAP 인스턴스에 대해 자동 시작 사용][planning-guide-11.5] 장에서 설명한 대로 (A)SCS 인스턴스의 시작 프로필에 Autostart 매개 변수를 설정해야 합니다.
  자세한 내용은 [SAP 애플리케이션 서버의 고가용성][planning-guide-11.4.1] 장을 참조하세요.

  관리 디스크를 사용하더라도 해당 디스크는 Azure Storage 계정에도 저장되므로 Storage가 중단되면 사용이 불가능해질 수 있습니다.

* *더 높은* 가용성

  여기서는 Azure VM 다시 시작 기능을 활용하여 SAP (A)SCS 인스턴스가 설치된 VM을 보호합니다. Azure 서버의 예정되었거나 갑작스러운 가동 중지 시간의 경우 VM은 사용 가능한 다른 서버에서 다시 시작됩니다. 앞서 언급한 것처럼 Azure VM 다시 시작 기능은 애플리케이션(이 경우 (A)SCS 인스턴스)이 아닌 VM을 주로 보호합니다. VM 다시 시작을 통해 SAP (A)SCS 인스턴스의 높은 가용성에 간접적으로 도달하게 됩니다. VM을 다시 부팅한 후 (A)SCS 인스턴스가 자동으로 시작되도록 하려면 [SAP 인스턴스에 대해 자동 시작 사용][planning-guide-11.5] 장에서 설명한 대로 (A)SCS 인스턴스의 시작 프로필에 Autostart 매개 변수를 설정해야 합니다. 즉, 단일 VM에서 SPOF(단일 실패 지점)로 실행되는 (A)SCS 인스턴스는 전체 SAP 지형의 가용성을 결정하는 요소가 됩니다.

* *더 높은* 가용성

  여기서는 SAP (A)SCS 인스턴스 사용 사례와 마찬가지로 Azure VM 다시 시작을 활용하여 DBMS 소프트웨어가 설치된 VM을 보호하고, VM 다시 시작을 통해 DBMS 소프트웨어의 "높은 가용성"을 획득합니다.
  단일 VM에서 실행되는 DBMS는 SPOF이기도 하며 전체 SAP 지형의 가용성을 결정하는 중요한 요소가 됩니다.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Azure IaaS의 SAP 애플리케이션 고가용성

전체 SAP 시스템의 고가용성을 달성하려면 모든 중요 SAP 시스템 구성 요소(예: 중복 SAP 애플리케이션 서버), SAP (A)SCS 인스턴스 및 DBMS와 같은 고유한 구성 요소(예: 단일 실패 지점)를 보호해야 합니다.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>SAP 애플리케이션 서버의 고가용성

SAP 애플리케이션 서버/대화 상자 인스턴스의 경우 특정 고가용성 솔루션을 고려할 필요가 없습니다. 고가용성은 중복성에 의해 획득되므로 다른 가상 머신에서 충분히 확보될 수 있습니다. 예정된 유지 관리 가동 중지와 동시에 VM이 업데이트되지 않도록 하려면 모두 동일한 Azure 가용성 집합에 두어야 합니다. Azure 배율 단위 내의 다른 업그레이드 도메인 및 장애 도메인을 기반으로 하는 기본 기능은 이미 [업그레이드 도메인][planning-guide-3.2.2] 챕터에서 살펴보았습니다. Azure 가용성 집합은 이 문서의 [Azure 가용성 집합][planning-guide-3.2.3] 장에서 설명했습니다.

Azure 배율 단위 내에서 Azure 가용성 집합이 사용할 수 있는 장애 도메인 및 업그레이드 도메인 수는 제한되어 있습니다. 즉, 여러 VM을 가용성 집합 하나에 포함하면 최종적으로는 동일한 장애 도메인이나 업그레이드 도메인에 VM이 여러 개 포함됩니다.

전용 VM에서 일부 SAP 애플리케이션 서버 인스턴스를 배포하고 업그레이드 도메인을 5개 받았다고 가정하면 결과적으로 다음과 같은 그림이 완성됩니다. 가용성 집합 내의 장애 및 업그레이드 도메인의 실제 최대 개수는 나중에 변경될 수 있습니다.

![Azure에 있는 SAP 애플리케이션 서버의 HA][planning-guide-figure-3000]

자세한 내용은 <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability> 설명서를 참조하세요.

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Azure에서 SAP Central Services에 대한 고가용성

Azure에서 SAP Central Services의 고가용성 아키텍처의 경우 항목 정보로 [SAP NetWeaver에 대한 고가용성 아키텍처 및 시나리오](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) 문서를 참조하세요. 이 문서는 특정 운영 체제에 대한 자세한 설명을 가리킵니다.

#### <a name="high-availability-for-the-sap-database-instance"></a>SAP 데이터베이스 인스턴스의 고가용성

일반적인 SAP DBMS HA 설치는 DBMS 고가용성 기능이 활성 DBMS 인스턴스에서 두 번째 VM의 수동 DBMS 인스턴스로 데이터를 복제하는 데 사용되는 경우 두 개의 DBMS VM을 기반으로 합니다.

일반적으로 DBMS 및 특정 DBMS에 대한 고가용성 및 재해 복구 기능은 [DBMS 배포 가이드][dbms-guide]에서 설명하고 있습니다.

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>전체 SAP 시스템의 종단 간 고가용성

Azure의 전체 SAP NetWeaver HA 아키텍처의 두 가지 예는 Windows용과 Linux용입니다.

비관리 디스크 전용: 아래에 설명된 개념은 많은 SAP 시스템을 배포하고 배포된 VM 수가 구독당 Storage 계정의 최대 제한을 초과할 경우 약간의 조정이 필요할 수 있습니다. 이러한 경우 VM의 VHD를 단일 Storage 계정 내에 결합해야 합니다. 일반적으로는 이를 위해 여러 다른 SAP 시스템에 있는 SAP 애플리케이션 계층 VM의 VHD를 결합할 수 있습니다.  여기서는 서로 다른 SAP 시스템에 있는 여러 DBMS VM의 다른 VHD를 단일 Azure Storage 계정에 결합했습니다. Azure Storage 계정의 IOPS 제한에 유의합니다(<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>).


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Windows의 HA

![Azure IaaS의 SQL Server를 사용한 SAP NetWeaver 애플리케이션 HA 아키텍처][planning-guide-figure-3200]

다음 Azure 구성은 인프라 문제 및 호스트 패치에 의한 영향을 최소화하기 위해 SAP NetWeaver 시스템에서 사용됩니다.

* 전체 시스템이 Azure에 배포됩니다(필수 - DBMS 계층, (A)SCS 인스턴스 및 전체 애플리케이션 계층이 같은 위치에서 실행되어야 함).
* 전체 시스템이 단일 Azure 구독 내에서 실행됩니다(필수).
* 전체 시스템이 단일 Azure Virtual Network 내에서 실행됩니다(필수).
* 모든 VM이 동일한 Virtual Network에 있는 경우에도 단일 SAP 시스템의 VM을 3개의 가용성 집합으로 구분할 수 있습니다.
* 각 계층(예: DBMS, ASCS, 애플리케이션 서버)은 전용 가용성 집합을 사용해야 합니다.
* 단일 SAP 시스템의 DBMS 인스턴스를 실행하는 모든 VM이 하나의 가용성 집합에 있습니다. SQL Server AlwaysOn 또는 Oracle Data Guard와 같은 네이티브 DBMS 고가용성 기능이 사용되므로 시스템당 둘 이상의 VM에서 DBMS 인스턴스가 실행된다고 가정할 수 있습니다.
* DBMS 인스턴스를 실행하는 모든 VM은 자체 저장소 계정을 사용합니다. DBMS 데이터 및 로그 파일은 데이터를 동기화하는 DBMS 고가용성 기능을 사용하여 한 저장소 계정에서 다른 저장소 계정으로 복제됩니다. 하나의 저장소 계정을 사용할 수 없는 경우 전체 SQL Server 서비스가 아니라 SQL Windows 클러스터 노드 하나만 사용할 수 없게 됩니다.
* 단일 SAP 시스템의 (A)SCS 인스턴스를 실행하는 모든 VM이 하나의 가용성 집합에 있습니다. 해당 VM의 내부에서 WSFC(Windows Sever 장애 조치(failover) 클러스터)가 (A)SCS 인스턴스를 보호하도록 구성됩니다.
* (A)SCS 인스턴스를 실행하는 모든 VM은 자체 저장소 계정을 사용합니다. (A)SCS 인스턴스 파일 및 SAP 전역 폴더는 SIOS DataKeeper 복제를 사용하여 한 저장소 계정에서 다른 저장소 계정으로 복제됩니다. 하나의 저장소 계정을 사용할 수 없는 경우 전체 (A)SCS 서비스가 아니라 (A)SCS Windows 클러스터 노드 하나만 사용할 수 없게 됩니다.
* SAP 애플리케이션 서버 계층을 나타내는 모든 VM은 세 번째 가용성 집합에 있습니다.
* SAP 애플리케이션 서버를 실행하는 모든 VM은 자체 저장소 계정을 사용합니다. 저장소 계정 하나를 사용할 수 없으면 하나의 SAP 애플리케이션 서버를 사용할 수 없게 되며 다른 SAP 애플리케이션 서버는 계속 실행됩니다.

다음 그림에는 관리 디스크를 사용하는 동일 지형이 나와 있습니다.

![Azure IaaS의 SQL Server를 사용한 SAP NetWeaver 애플리케이션 HA 아키텍처][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Linux의 HA

Azure의 Linux에 대한 SAP HA 아키텍처는 기본적으로 위에 설명된 것처럼 Windows의 경우와 동일합니다. 지원되는 고가용성 솔루션의 목록은 SAP Note [1928533]을 참조하세요.

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>SAP 인스턴스에 대해 자동 시작 사용

SAP는 VM 내에서 운영 체제가 시작된 후 즉시 SAP 인스턴스를 시작하기 위한 기능을 제공합니다. 정확한 단계는 SAP 기술 자료 문서 [1909114]에 설명되어 있습니다. 그러나 SAP는 인스턴스 다시 시작 순서가 제어되지 않고 둘 이상의 VM이 영향을 받거나 VM당 여러 인스턴스가 실행되었다고 가정하므로 이러한 설정을 사용하는 것을 더 이상 권장하지 않습니다. VM 하나에 단일 SAP 애플리케이션 서버 인스턴스가 있는 일반적인 Azure 시나리오와 단일 VM이 다시 시작되는 경우를 가정하면, 자동 시작은 중요하지 않으며 이 매개 변수를 다음에 추가하여 사용되도록 설정할 수 있습니다.

    Autostart = 1

SAP ABAP 및/또는 Java 인스턴스의 시작 프로필

> [!NOTE]
> Autostart 매개 변수에도 일부 단점이 있을 수 있습니다. 구체적으로 말하면, 이 매개 변수는 인스턴스의 관련 Windows/Linux 서비스가 시작될 때 SAP ABAP 또는 Java 인스턴스가 시작되도록 트리거합니다. 운영 체제가 부팅될 때가 가장 대표적인 예입니다. 그러나 SAP 서비스를 다시 시작하는 것도 SUM이나 기타 업데이트 또는 업그레이드와 같은 SAP 소프트웨어 수명 주기 관리 기능의 일반적인 측면에 해당합니다. 이러한 기능에서 항상 인스턴스가 자동으로 다시 시작될 것으로 예상되지는 아닙니다. 따라서 이러한 작업을 실행하기 전에 Autostart 매개 변수를 사용하지 않도록 설정해야 합니다. Autostart 매개 변수를 ASCS/SCS/CI 같이 클러스터링되는 SAP 인스턴스에는 사용하지 말아야 합니다.
>
>

SAP 인스턴스의 자동 시작과 관련된 자세한 내용은 다음 항목을 참조하세요.

* [Unix 서버 시작/중지에 따른 SAP 시작/중지](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [SAP NetWeaver 관리 에이전트 시작 및 중지](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [HANA 데이터베이스의 자동 시작을 사용하도록 설정하는 방법](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>대형 3계층 SAP 시스템
3계층 SAP 구성의 고가용성 측면은 이전 섹션에서 이미 설명되었습니다. 그러나 DBMS 서버 요구 사항이 너무 커서 Azure에 포함할 수 없는 시스템의 경우는 어떨까요? SAP 애플리케이션 계층을 Azure에 배포할 수 있을까요?

#### <a name="location-of-3-tier-sap-configurations"></a>3계층 SAP 구성의 위치
애플리케이션 계층 자체를 분할하거나 애플리케이션 및 DBMS 계층을 온-프레미스와 Azure 간에 분할하는 것은 지원되지 않습니다  SAP 시스템은 온-프레미스 또는 Azure에 완전히 배포됩니다. 또한 일부 애플리케이션 서버는 온-프레미스에 두고 다른 애플리케이션 서버는 Azure에 둘 수도 없습니다. 이것이 바로 논의를 시작할 부분입니다. 또한 SAP 시스템의 DBMS 구성 요소와 SAP 애플리케이션 서버 계층을 두 개의 서로 다른 Azure 지역에 배포하는 것도 지원되지 않습니다. 미국 서부에 DBMS를 배포하고 미국 중부에 SAP 애플리케이션 계층을 배포하는 경우를 예로 들 수 있습니다. 이러한 구성을 지원하지 않는 이유는 SAP NetWeaver 아키텍처의 대기 시간 민감도 때문입니다.

그러나 작년에 관련 작업을 거치면서 데이터 센터 파트너가 Azure 지역에 대한 공동 위치를 개발했습니다. 이러한 공동 위치는 대개 Azure 지역 내의 실제 Azure 데이터 센터와 가깝습니다. Azure에 대한 ExpressRoute를 사용해서 공동 위치에 짧은 거리를 유지하면서 자산을 연결하면 대기 시간이 2ms보다 적어질 수 있습니다. 이러한 경우 공동 위치에 DBMS 계층(SAN/NAS 저장소 포함)을 배치하고 Azure에 SAP 애플리케이션 계층을 배치할 수 있습니다. [HANA 큰 인스턴스입니다](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>SAP 시스템의 오프라인 Backup
선택한 SAP 구성(2계층 또는 3계층)에 따라, 백업이 필요할 수도 있습니다. 데이터베이스의 백업 외에 VM 자체의 내용도 있습니다. DBMS 관련 백업은 데이터베이스 메서드로 수행됩니다. 다른 데이터베이스에 대한 자세한 설명은 [DBMS 가이드][dbms-guide]에 있습니다. SAP 데이터는 이 섹션에 설명되는 오프라인 방식으로(데이터베이스 콘텐츠도 포함) 또는 다음 섹션에 설명되는 온라인 방식으로 백업될 수 있습니다.

오프라인 백업에서는 기본적으로 Azure Portal을 통해 VM을 종료해야 하며, VM에 연결된 모든 디스크 외의 기본 VM 디스크 복사본이 있어야 합니다. 이 경우 VM 및 관련 디스크의 지정 시간 이미지가 보존됩니다. '백업'을 다른 Azure Storage 계정으로 복사하는 것이 좋습니다. 따라서 이 문서의 [Azure Storage 계정 간 디스크 복사][planning-guide-5.4.2] 장에서 설명한 절차가 적용됩니다.
Azure Portal 사용을 종료하는 것 외에도, <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>에 설명된 대로 Powershell 또는 CLI를 사용하여 이 작업을 수행할 수 있습니다.

해당 상태가 복원될 때는 기본 VM뿐 아니라 기본 VM의 원본 디스크와 장착된 디스크도 삭제되고, 저장된 디스크가 관리 디스크의 리소스 그룹 또는 원래 Storage 계정으로 다시 복사된 후에 시스템이 다시 배포됩니다.
이 프로세스를 PowerShell에서 스크립트로 실행하는 방법의 예제는 <http://www.westerndevs.com/azure-snapshots/> 문서에 나와 있습니다.

위에 설명된 대로 VM 백업을 복원하면 새 하드웨어 키가 생성되므로 새 SAP 라이선스를 설치해야 합니다.

### <a name="online-backup-of-an-sap-system"></a>SAP 시스템의 온라인 백업

DBMS의 Backup은 [DBMS 가이드][dbms-guide]에서 설명한 대로 DBMS 관련 메서드를 사용하여 수행됩니다.

SAP 시스템 내의 다른 VM은 Azure Virtual Machine Backup 기능을 사용하여 백업할 수 있습니다. Azure Virtual Machine Backup은 Azure에서 전체 VM을 백업하는 표준 방법입니다. Azure Backup은 Azure에 백업을 저장하고 VM을 다시 복원할 수 있도록 합니다.

> [!NOTE]
> 2015년 12월을 기준으로 VM Backup은 SAP 라이선스에 사용되는 고유한 VM ID를 보존하지 않습니다. 즉, 복원된 VM은 저장된 이전 VM을 대신하는 것이 아니라 새 VM으로 간주되므로 VM 백업에서 복원하려면 새 SAP 라이선스 키를 설치해야 합니다.
>
> ![Windows][Logo_Windows] Windows
>
> 이론적으로 DBMS 시스템에서 Windows VSS(볼륨 섀도 복사본 서비스 <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>)를 지원하는 경우 데이터베이스를 실행하는 VM을 SQL Server 등 일관성 있는 방법으로 백업할 수 있습니다.
> 그러나 데이터베이스의 Azure VM 백업 기반의 특정 시간 복원은 가능하지 않습니다. 따라서 Azure VM Backup을 사용하지 않고 DBMS 기능을 사용하여 데이터베이스 백업을 수행하는 것이 좋습니다.
>
> Azure Virtual Machine Backup에 익숙해지려면 <https://docs.microsoft.com/azure/backup/backup-azure-vms>에서 시작하세요.
>
> Azure VM에 설치된 Microsoft Data Protection Manager와 Azure Backup을 함께 사용하여 데이터베이스 백업/복원할 수도 있습니다. 자세한 내용은 <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>을 참조하세요.  
>
> ![Linux][Logo_Linux] Linux
>
> Linux에는 Windows VSS와 동일한 기능이 없습니다. 따라서 파일 일치 백업만 가능하고 애플리케이션 일치 백업은 가능하지 않습니다. SAP DBMS 백업은 DBMS 기능을 사용해서 수행해야 합니다. SAP 관련 데이터를 포함하는 파일 시스템은 <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>에 설명된 대로 tar 등을 사용해서 저장할 수 있습니다.
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>프로덕션 SAP 지형에 대한 DR 사이트로 사용되는 Azure

2014년 중반부터 Hyper-V, System Center 및 Azure와 관련한 다양한 구성 요소가 확장되면서 Azure를 Hyper-V를 기준으로 온-프레미스에서 실행되는 VM에 대한 DR 사이트로 사용할 수 있게 되었습니다.

이 솔루션을 배포하는 방법을 자세히 설명하는 블로그는 <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>에 설명되어 있습니다.

## <a name="summary"></a>요약

Azure의 SAP 시스템 고가용성의 핵심 사항은 다음과 같습니다.

* 현재 온-프레미스 배포의 경우와 정확히 동일한 방식으로 SAP 단일 실패 지점을 보호할 수는 없습니다. 그 이유는 타사 소프트웨어를 사용하지 않을 경우 아직 Azure에서 공유 디스크 클러스터를 구축할 수 없기 때문입니다.
* DBMS 계층의 경우 공유 디스크 클러스터 기술에 의존하지 않는 DBMS 기능을 사용해야 합니다. 자세한 내용은 [DBMS 가이드][dbms-guide]에서 설명하고 있습니다.
* Azure 인프라의 장애 도메인 문제 또는 호스트 유지 관리의 영향을 최소화하려면 Azure 가용성 집합을 사용해야 합니다.
  * SAP 애플리케이션 계층에 대한 가용성 집합을 하나 유지하는 것이 좋습니다.
  * SAP DBMS 계층에 대한 별도의 가용성 집합을 유지하는 것이 좋습니다.
  * 다른 SAP 시스템의 VM에 대해 동일한 가용성 집합을 적용하는 것은 바람직하지 않습니다.
  * Premium 관리 디스크를 사용하는 것이 좋습니다.
* SAP DBMS 계층의 Backup 목적에 대해서는 [DBMS 가이드][dbms-guide]를 확인하세요.
* 간단한 대화 상자 인스턴스를 재배포하는 것이 더 빠르므로 SAP 대화 상자 인스턴스를 백업하는 것은 거의 의미가 없습니다.
* SAP 시스템의 전체 디렉터리를 포함하는 VM과 다양한 인스턴스의 모든 프로필을 백업하는 것은 도움이 되며 Windows Backup 또는 Linux의 tar 등을 사용하여 수행해야 합니다. Windows Server 2008(R2) 및 Windows Server 2012(R2) 간에는 차이가 있으며 좀 더 최신의 Windows Server 릴리스를 사용하여 백업하는 것이 더 쉬우므로 Windows Server 2012(R2)를 Windows 게스트 운영 체제로 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
문서를 읽어보세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Azure에서 SAP HANA 인프라 구성 및 작업](https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)

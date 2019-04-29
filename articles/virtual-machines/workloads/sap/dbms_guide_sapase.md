---
title: SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포 | Microsoft Docs
description: SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f50f013020c704ddc294a59f8c6c5dac24bbd5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835280"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md



이 문서에서는 Azure IaaS에서 SAP ASE를 배포할 때 고려할 여러 가지 영역을 다룹니다. 이 문서의 사전 조건으로, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)의 다른 가이드를 읽어야 합니다. 

## <a name="specifics-to-sap-ase-on-windows"></a>Windows의 SAP ASE에 대한 고유 정보
Microsoft Azure부터 기존 SAP ASE 응용 프로그램을 Azure Virtual Machines로 마이그레이션할 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 이러한 응용 프로그램을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 응용 프로그램에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Azure Virtual Machines의 SLA는 여기에서 찾을 수 있습니다. <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure는 가장 작은 SAP 시스템과 환경부터 수천 명의 사용자가 있는 대규모 SAP 시스템과 환경까지 실행할 수 있는 다양한 가상 머신 유형을 제공합니다. 서로 다른 SAP 인증 VM SKU의 SAP 크기 조정 SAPS 번호는 SAP Note [1928533]에서 제공됩니다.

[SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 제공된 Azure Storage 사용, SAP VM 또는 SAP 모니터링 배포와 관련된 설명 및 권장 사항은 SAP ASE 배포에도 적용됩니다.

### <a name="sap-ase-version-support"></a>SAP ASE 버전 지원
SAP는 현재 SAP ASE 버전 16.0을 SAP Business Suite 제품과 함께 사용하도록 지원합니다. SAP Business Suite 제품에서 사용되는 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 모든 업데이트는 <https://support.sap.com/swdc>에 있는 SAP Service Marketplace를 통해서만 제공됩니다.

Sybase 웹 사이트에서 직접 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 업데이트를 다운로드하지 마세요. 온-프레미스 및 Azure Virtual Machines에서 SAP 제품을 사용하도록 지원되는 패치에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

* [1590719]
* [1973241]

SAP ASE에서의 SAP Business Suite 실행에 대한 일반 정보는 [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM의 SAP 관련 SAP ASE 설치에 대한 SAP ASE 구성 지침
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE 배포의 구조
SAP ASE 실행 파일을 VM의 OS 디스크에 있는 시스템 드라이브(드라이브 C:\)에 배치 또는 설치해야 합니다. 일반적으로 대부분의 SAP ASE 시스템 및 도구 데이터베이스에는 높은 워크로드가 발생하지 않습니다. 따라서 시스템 및 도구 데이터베이스(master, model, saptools, sybmgmtdb, sybsystemdb)가 C:\ 드라이브에 남아 있을 수 있습니다. 

단, 임시 데이터베이스는 예외적으로 일부 SAP ERP 및 모든 BW 워크로드에서 더 많은 데이터 볼륨 또는 I/O 작업 볼륨을 필요로 할 수 있습니다. VM의 OS 디스크(드라이브 C:\))에서 제공할 수 없는 볼륨 또는 IOPS입니다.

설치하는 데 사용되는 SAPInst/SWPM 버전에 따라 SAP ASE 인스턴스 구성은 다음과 같이 표시될 수 있습니다.

* SAP ASE를 설치할 때 만들어지는 단일 SAP ASE tempdb
* SAP 설치 루틴에 의해 만들어지는 SAP ASE 및 추가 saptempdb를 설치하여 만들어지는 SAP ASE tempdb
* ERP/BW 특정 tempdb 요구 사항에 맞게 SAP ASE 및 수동으로 만든 추가 tempdb를 설치하여 만들어지는 SAP ASE tempdb(예: SAP Note [1752266])

특정 ERP 또는 모든 BW 워크로드의 성능상 이유로 추가로 생성된 tempdb 장치를 C:\가 아닌 다른 드라이브에 저장하는 것이 좋습니다. 추가 tempdb가 없는 경우 새로 만드는 것이 좋습니다(SAP Note [1752266]).

이러한 시스템의 경우, 추가로 생성된 tempdb에 대해 다음 단계를 실행해야 합니다.

* 첫 번째 tempdb 디바이스를 SAP 데이터베이스의 첫 번째 디바이스로 이동
* SAP 데이터베이스의 디바이스를 포함하는 각 VHD에 tempdb 디바이스 추가

이 구성을 사용하면 tempdb에서 시스템 드라이브에서 제공할 수 있는 것보다 더 많은 공간을 사용할 수 있습니다. 참조로 온-프레미스에서 실행 중인 기존 시스템에서 tempdb 디바이스 크기를 확인할 수 있습니다. 또는 이러한 구성은 시스템 드라이브에서 tempdb에 대해 제공할 수 있는 것보다 더 많은 IOPS 수를 사용합니다. 온-프레미스에서 실행 중인 시스템에서 tempdb에 대해 I/O 워크로드를 모니터링할 수 있습니다.

SAP ASE 디바이스를 VM의 D:\ 드라이브에 배치하지 마세요. SAP ASE의 경우, 이 조언은 tempdb에도 적용되며 tempdb에 저장된 개체가 임시 개체인 경우에도 마찬가지입니다.

데이터 및 트랜잭션 로그 파일 배포의 경우, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 제공된 설명 및 제안 사항이 적용됩니다. Windows 기반 배포의 경우, 충분한 IOPS, 처리량 및 볼륨이 있는 스트라이프 집합을 빌드하는 데는 Windows 저장소 공간을 사용하는 것이 좋습니다.  

#### <a name="impact-of-database-compression"></a>데이터베이스 압축의 영향
I/O 대역폭이 제한 요인이 될 수 있는 구성에서 측정마다 IOPS를 줄여 Azure와 같이 IaaS 시나리오에서 실행할 수 있는 워크로드를 늘릴 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인하는 것이 좋습니다.

Azure에 업로드하기 전에 압축을 적용하는 것이 좋은 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure Virtual Machines에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용 중인지 확인하는 방법은 SAP Note [1750510]을 참조하세요.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit를 사용하여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스 모니터링 및 관리를 위한 전체 기능은 DBACockpit의 Webdynpro 구현에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. webdynpro를 사용하도록 설정하고 필요한 경우 생성하려면 SAP Note [1245200] 을 참조하세요. 위의 Note 지침을 수행하면 http 및 https 연결에 사용할 포트와 함께 ICM(Internet Communication Manager)도 구성하게 됩니다. HTTP의 기본 설정은 다음과 같이 표시됩니다.

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

그리고 트랜잭션 DBACockpit에서 생성된 링크는 다음과 같이 표시됩니다.

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP 시스템을 호스트하는 Azure Virtual Machine이 AD 및 DNS에 연결된 방법에 따라 ICM이 DBACockpit를 여는 머신에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. ICM에서 프로필 매개 변수에 따라 정규화된 호스트 이름을 확인하는 방법을 이해하고 필요한 경우, icm/host_name_full 매개 변수를 명시적으로 설정하려면 SAP Note [773830]을 참조하세요.

온-프레미스와 Azure 간에 프레미스 간 연결이 없는 클라우드 전용 시나리오에서 VM을 배포한 경우 공용 IP 주소 및 도메인 레이블을 정의해야 합니다. VM의 공용 DNS 이름 형식은 다음과 같이 표시됩니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 이름과 관련된 자세한 내용은 [여기][virtual-machines-azurerm-versus-azuresm]에 있습니다.

SAP 프로필 매개 변수 icm/host_name_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure Portal의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [1558958]
* [1619967]
* [1882376]

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE에 대한 Backup/복구 고려 사항
SAP ASE를 Azure에 배포하는 경우 백업 방법을 검토해야 합니다. 프로덕션 이외의 시스템에서도 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure Storage에는 이제 세 개의 이미지가 있으므로 저장소 작동 중단을 보완하는 측면에서 백업의 중요성이 감소할 수 있습니다. 적절한 백업 및 복원 계획 유지 관리가 중요한 이유는 지정 시간 복구 기능을 제공하여 논리/수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 

Azure에서의 데이터베이스 백업 및 복원은 온-프레미스와 동일한 방식으로 진행됩니다. 다음 SAP Note를 참조하세요.

* [1588316]
* [1585981]

여기에는 덤프 구성 생성 및 백업 예약에 대한 자세한 내용이 나와 있습니다. 전략 및 요구 사항에 따라 데이터베이스를 구성하고 디스크 덤프를 기존 디스크 중 하나에 기록하거나 백업을 위해 다른 디스크를 추가할 수 있습니다. 오류가 발생할 경우, 데이터 손실의 위험을 줄이기 위해서는 데이터베이스 파일이 없는 디스크를 사용하는 것이 좋습니다.

데이터 및 LOB 압축 SAP ASE 외에도 백업 압축을 제공합니다. 데이터베이스 및 로그 덤프의 공간 사용량을 줄이려면 백업 압축을 사용하는 것이 좋습니다. 자세한 내용은 SAP Note [1588316]을 참조하세요. 백업 압축은 백업 또는 백업 덤프를 포함하는 VHD를 Azure Virtual Machine에서 온-프레미스로 다운로드하려고 할 때 전송되는 데이터 양을 줄이는 데도 효과적입니다.

데이터베이스 또는 로그 덤프 대상으로 D:\ 드라이브를 사용하지 마세요.

#### <a name="performance-considerations-for-backupsrestores"></a>Backup/복원에 대한 성능 고려 사항
완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 백업 압축에는 CPU 리소스가 사용됩니다. 이런 백업 압축의 CPU 사용량은 적은 수의 CPU 스레드로 VM에서 중요 역할을 수행할 수 있습니다. 따라서 다음과 같이 가정할 수 있습니다.

* 데이터베이스 디바이스를 저장하는 데 사용하는 디스크 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(스트라이프 디렉터리, 디스크) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 필요에 따라 사용/혼합할 수 있습니다.

* 해당 스트라이프 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 디스크에 백업 대상 볼륨을 스트라이프합니다.
* 둘 이상의 대상 디렉터리를 사용하여 덤프를 작성하는 SAP ASE 수준에서 덤프 구성을 만듭니다.

여러 개의 탑재된 디스크에 디스크 볼륨을 스트라이프하는 방법은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 설명했습니다. 여러 디렉터리를 사용하여 SAP ASE 덤프를 구성하는 방법에 대한 자세한 내용은 [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp)에서 덤프 구성을 만드는 데 사용되는 저장 프로시저 sp_config_dump에 대한 설명서를 참조하세요.

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM을 사용한 재해 복구
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SA Sybase Replication Server를 사용한 데이터 복제
SAP SRS(Sybase Replication Server)를 사용하면 SAP ASE에서 멀리 떨어진 위치에 데이터베이스 트랜잭션을 비동기적으로 전송하는 웜 대기 솔루션을 제공합니다. 

SRS 설치 및 작동은 Azure Virtual Machine 서비스에서 호스트되는 VM에서도 온-프레미스에서처럼 기능적으로 작동합니다.

SAP ASE HADR은 Azure Internal Load Balancer가 필요하지 않으며 OS 수준 클러스터링을 사용하지 않습니다. Azure Windows 및 Linux VM에서 작동합니다. SAP ASE HADR에 대한 자세한 내용은 [SAP ASE HADR 사용자 가이드](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)를 참조하세요.

## <a name="specifics-to-sap-ase-on-linux"></a>Linux의 SAP ASE에 대한 고유 정보
Microsoft Azure부터 기존 SAP ASE 애플리케이션을 쉽게 Azure Virtual Machines로 마이그레이션할 수 있습니다. Virtual Machine에서 SAP ASE를 사용하면 이러한 애플리케이션을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 애플리케이션에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Azure VM을 배포하는 경우 <https://azure.microsoft.com/support/legal/sla>에 있는 공식 SLA에 대해 알고 있어야 합니다.

SAP 크기 조정 정보 및 SAP 인증 VM SKU 목록은 SAP Note [1928533]을 참조하세요. Azure 가상 머신에 대한 SAP 크기 지정 추가 문서는 <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> 및 <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>에서 찾을 수 있습니다.

이 문서의 처음 네 챕터에서 설명한 것처럼 SAP 응용 프로그램과 함께 SAP ASE를 배포할 때는 Azure Storage 사용, SAP VM 또는 SAP 모니터링 배포와 관련된 설명 및 권장 사항이 적용됩니다.

다음 두 SAP Note에는 Linux의 ASE 및 클라우드의 ASE에 대한 일반 정보가 포함되어 있습니다.

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE 버전 지원
SAP는 현재 SAP ASE 버전 16.0을 SAP Business Suite 제품과 함께 사용하도록 지원합니다. SAP Business Suite 제품에서 사용되는 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 모든 업데이트는 <https://support.sap.com/swdc>에 있는 SAP Service Marketplace를 통해서만 제공됩니다.

온-프레미스 설치와 마찬가지로 Sybase 웹 사이트에서 직접 SAP ASE 서버 또는 JDBC 및 ODBC 드라이버에 대한 업데이트를 다운로드하지 마세요. 온-프레미스 및 Azure Virtual Machines에서 SAP Business Suite 제품을 사용하도록 지원되는 패치에 대한 자세한 내용은 다음 SAP Note를 참조하세요.

* [1590719]
* [1973241]

SAP ASE에서의 SAP Business Suite 실행에 대한 일반 정보는 [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Azure VM의 SAP 관련 SAP ASE 설치에 대한 SAP ASE 구성 지침
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE 배포의 구조
SAP ASE 실행 파일을 VM의 루트 파일 시스템(/sybase)에 배치 또는 설치해야 합니다. 일반적으로 대부분의 SAP ASE 시스템 및 도구 데이터베이스에는 높은 워크로드가 발생하지 않습니다. 따라서 시스템 및 도구 데이터베이스(master, model, saptools, sybmgmtdb, sybsystemdb)가 루트 파일 시스템에 저장될 수 있습니다. 

단, 임시 데이터베이스는 예외적으로 일부 SAP ERP 및 모든 BW 워크로드에서 더 많은 데이터 볼륨 또는 I/O 작업 볼륨을 필요로 할 수 있습니다. VM의 OS 디스크에서 제공할 수 없는 볼륨 또는 IOPS 

시스템을 설치하는 데 사용되는 SAPInst/SWPM의 버전에 따라 데이터베이스에 다음 항목이 포함될 수 있습니다.

* SAP ASE를 설치할 때 만들어지는 단일 SAP ASE tempdb
* SAP 설치 루틴에 의해 만들어지는 SAP ASE 및 추가 saptempdb를 설치하여 만들어지는 SAP ASE tempdb
* ERP/BW 특정 tempdb 요구 사항에 맞게 SAP ASE 및 수동으로 만든 추가 tempdb를 설치하여 만들어지는 SAP ASE tempdb(예: SAP Note [1752266])

특정 ERP 또는 모든 BW 워크로드의 성능상 이유로 추가로 생성된 tempdb 장치(SWPM을 통해 또는 수동으로)를 단일 Azure 데이터 디스크 또는 여러 Azure 데이터 디스크에 걸친 Linux RAID로 표현될 수 있는 별도의 파일 시스템에 저장하는 것이 좋습니다. 추가 tempdb가 없는 경우 새로 만드는 것이 좋습니다(SAP Note [1752266]).

이러한 시스템의 경우 추가로 생성된 tempdb에 대해 다음 단계를 수행해야 합니다.

* SAP 데이터베이스의 첫 번째 파일 시스템으로 첫 번째 tempdb 디렉터리 이동
* SAP 데이터베이스의 파일 시스템을 포함하는 각 디스크에 tempdb 디렉터리 추가

이 구성을 사용하면 tempdb에서 시스템 드라이브에서 제공할 수 있는 것보다 더 많은 공간을 사용할 수 있습니다. 참조로 온-프레미스에서 실행 중인 기존 시스템에서 tempdb 디바이스 크기를 확인할 수 있습니다. 또는 이러한 구성은 시스템 드라이브에서 tempdb에 대해 제공할 수 있는 것보다 더 많은 IOPS 수를 사용합니다. 온-프레미스에서 실행 중인 시스템에서 tempdb에 대해 I/O 워크로드를 모니터링할 수 있습니다.

SAP ASE 디렉터리를 VM의 /mnt 또는 /mnt/resource에 배치하지 마세요. SAP ASE의 경우, 이 조언은 tempdb에도 적용되며 tempdb에 저장된 개체가 임시 개체인 경우에도 마찬가지입니다. /mnt 또는 /mnt/resource는 기본 Azure VM 임시 공간이므로 영구적이지 않습니다. Azure VM 임시 공간에 대한 자세한 내용은 [이 문서][virtual-machines-linux-how-to-attach-disk]에 있습니다.

데이터 및 트랜잭션 로그 파일 배포의 경우, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 제공된 설명 및 제안 사항이 적용됩니다. Linux 기반 배포의 경우, 충분한 IOPS, 처리량 및 볼륨이 있는 스트라이프 집합을 빌드하는 데는 LVM 또는 MDADM을 사용하는 것이 좋습니다. 

#### <a name="impact-of-database-compression"></a>데이터베이스 압축의 영향
I/O 대역폭이 제한 요인이 될 수 있는 구성에서 측정마다 IOPS를 줄여 Azure와 같이 IaaS 시나리오에서 실행할 수 있는 워크로드를 늘릴 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인하는 것이 좋습니다.

Azure에 업로드하기 전에 압축을 적용하는 것이 좋은 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure Virtual Machines에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용 중인지 확인하는 방법은 SAP Note [1750510]을 참조하세요. 데이터베이스 압축에 대한 자세한 내용은 SAP Note [2121797]을 참조하세요.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit를 사용하여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스 모니터링 및 관리를 위한 전체 기능은 DBACockpit의 Webdynpro 구현에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. webdynpro를 사용하도록 설정하고 필요한 경우 생성하려면 SAP Note [1245200] 을 참조하세요. 위의 Note 지침을 수행하면 http 및 https 연결에 사용할 포트와 함께 ICM(Internet Communication Manager)도 구성하게 됩니다. Http에 대한 기본 설정은 다음과 같습니다.

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

트랜잭션 DBACockpit에서 생성된 링크는 다음과 같습니다.

> https:\//\<fullyqualifiedhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP 시스템을 호스트하는 Azure Virtual Machine이 AD 및 DNS에 연결된 방법에 따라 ICM이 DBACockpit를 여는 머신에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. ICM에서 프로필 매개 변수에 따라 정규화된 호스트 이름을 확인하는 방법을 이해하고 필요한 경우, icm/host_name_full 매개 변수를 명시적으로 설정하려면 SAP Note [773830]을 참조하세요.

온-프레미스와 Azure 간에 프레미스 간 연결이 없는 클라우드 전용 시나리오에서 VM을 배포한 경우 공용 IP 주소 및 도메인 레이블을 정의해야 합니다. VM의 공용 DNS 이름 형식은 다음과 같이 표시됩니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 이름과 관련된 자세한 내용은 [여기][virtual-machines-azurerm-versus-azuresm]에 있습니다.

SAP 프로필 매개 변수 icm/host_name_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure Portal의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [1558958]
* [1619967]
* [1882376]

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE에 대한 Backup/복구 고려 사항
SAP ASE를 Azure에 배포하는 경우 백업 방법을 검토해야 합니다. 프로덕션 이외의 시스템에서도 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure Storage에는 이제 세 개의 이미지가 있으므로 저장소 작동 중단을 보완하는 측면에서 백업의 중요성이 감소할 수 있습니다. 적절한 백업 및 복원 계획 유지 관리가 중요한 이유는 지정 시간 복구 기능을 제공하여 논리/수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 

Azure에서의 데이터베이스 백업 및 복원은 온-프레미스와 동일한 방식으로 진행됩니다. 다음 SAP Note를 참조하세요.

* [1588316]
* [1585981]

여기에는 덤프 구성 생성 및 백업 예약에 대한 자세한 내용이 나와 있습니다. 전략 및 요구 사항에 따라 데이터베이스를 구성하고 디스크 덤프를 기존 디스크 중 하나에 기록하거나 백업을 위해 다른 디스크를 추가할 수 있습니다. 오류가 발생하는 경우 데이터 손실의 위험을 줄이려면 데이터베이스 디렉터리/파일이 없는 디스크를 사용하는 것이 좋습니다.

데이터 및 LOB 압축 SAP ASE 외에도 백업 압축을 제공합니다. 데이터베이스 및 로그 덤프의 공간 사용량을 줄이려면 백업 압축을 사용하는 것이 좋습니다. 자세한 내용은 SAP Note [1588316]을 참조하세요. 백업 압축은 백업 또는 백업 덤프를 포함하는 VHD를 Azure Virtual Machine에서 온-프레미스로 다운로드하려고 할 때 전송되는 데이터 양을 줄이는 데도 효과적입니다.

Azure VM 임시 공간 /mnt 또는 /mnt/resource를 데이터베이스 또는 로그 덤프 대상으로 사용하지 마세요.

#### <a name="performance-considerations-for-backupsrestores"></a>Backup/복원에 대한 성능 고려 사항
완전 배포에서처럼 백업/복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 달라집니다. 백업 압축에는 CPU 리소스가 사용됩니다. 이런 백업 압축의 CPU 사용량은 적은 수의 CPU 스레드로 VM에서 중요 역할을 수행할 수 있습니다.  따라서 다음과 같이 가정할 수 있습니다.

* 데이터베이스 디바이스를 저장하는 데 사용하는 디스크 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* VM의 CPU 스레드 수가 적을수록 백업 압축에 대한 영향이 커집니다.
* 백업을 작성하는 대상(Linux 소프트웨어 RAID, 디스크) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 필요에 따라 사용/혼합할 수 있습니다.

* 해당 스트라이프 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 디스크에 백업 대상 볼륨을 스트라이프합니다.
* 둘 이상의 대상 디렉터리를 사용하여 덤프를 작성하는 SAP ASE 수준에서 덤프 구성을 만듭니다.

여러 개의 탑재된 디스크에 디스크 볼륨을 스트라이프하는 방법은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md)에서 설명했습니다. 여러 디렉터리를 사용하여 SAP ASE 덤프를 구성하는 방법에 대한 자세한 내용은 [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp)에서 덤프 구성을 만드는 데 사용되는 저장 프로시저 sp_config_dump에 대한 설명서를 참조하세요.

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM을 사용한 재해 복구
#### <a name="data-replication-with-sap-sybase-replication-server"></a>SA Sybase Replication Server를 사용한 데이터 복제
SAP SRS(Sybase Replication Server)를 사용하면 SAP ASE에서 멀리 떨어진 위치에 데이터베이스 트랜잭션을 비동기적으로 전송하는 웜 대기 솔루션을 제공합니다. 

SRS 설치 및 작동은 Azure Virtual Machine 서비스에서 호스트되는 VM에서도 온-프레미스에서처럼 기능적으로 작동합니다.

SAP Replication Server를 통한 ASE HADR은 지원되지 않습니다. 이러한 구성을 시도하려면 SAP ASE 16.03을 사용하는 것이 좋습니다. 이러한 구성을 설치하는 방법에 대한 자세한 내용은 이 [블로그](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/)를 참조하세요.

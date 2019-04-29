---
title: SAP 워크로드용 SQL Server Azure Virtual Machines DBMS 배포 | Microsoft Docs
description: SAP 워크로드용 SQL Server Azure Virtual Machines DBMS 배포
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
ms.date: 09/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0c12c75bd5c357613d55e04aed67c0cc901135e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835540"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SAP NetWeaver용 SQL Server Azure Virtual Machines DBMS 배포

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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




이 문서에서는 Azure IaaS에서 SAP 워크로드용 SQL Server를 배포할 때 고려해야 할 몇 가지 다른 영역에 대해 설명합니다. 이 문서의 전제 조건으로, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)의 다른 가이드를 참조해야 합니다. 



> [!IMPORTANT]
> 이 문서의 범위는 SQL Server의 Windows 버전입니다. SAP는 어떤 SAP 소프트웨어에서도 Linux 버전의 SQL Server를 지원하지 않습니다. 이 문서에서는 Microsoft Azure 플랫폼의 PaaS(Platform as a Service) 제품인 Microsoft Azure SQL Database에 대해 설명하지 않습니다. 이 문서에서는 Azure의 IaaS(서비스 제공 인프라)를 활용하여 Azure Virtual Machines에서 온-프레미스 배포에 대해 알려진 SQL Server 제품을 실행하는 방법에 대해 설명합니다. 이러한 두 환경에서의 데이터베이스 기능은 다르므로 서로 혼합하지 않아야 합니다. <https://azure.microsoft.com/services/sql-database/>도 참조하세요.
> 
>

일반적으로 최신 SQL Server 릴리스를 사용하여 Azure IaaS에서 SAP 작업을 실행하는 것이 좋습니다. 최신 SQL Server 릴리스는 Azure 서비스 및 기능 중 일부와 더 효율적으로 통합됩니다. 또는 Azure IaaS 인프라에서 작업을 최적화하도록 변경되었습니다.

계속하기 전에 [이 설명서][virtual-machines-sql-server-infrastructure-services]를 검토하는 것이 좋습니다.

다음 섹션에서는 위 링크에 속한 설명서의 일부를 묶어 설명합니다. SAP에 대한 정보도 언급되며 몇 가지 개념도 보다 자세히 설명합니다. 그러나 SQL Server 관련 문서를 읽기 전에 먼저 위의 문서를 통해 작업하는 것이 좋습니다.

계속하기 전에 다음과 같은 IaaS의 SQL Server 관련 정보를 참조하세요.

* **SQL 지원 버전**: SAP 고객의 경우 Microsoft Azure Virtual Machine에서 SQL Server 2008 R2 이상이 지원됩니다. 이전 버전은 지원되지 않습니다. 자세한 내용은 이 일반 [지원 설명](https://support.microsoft.com/kb/956893) 을 참조하세요. 일반적으로 SQL Server 2008은 Microsoft에서도 지원됩니다. 그러나 SQL Server 2008 R2에 SAP용 중요 기능이 도입되어 있으므로 SQL Server 2008 R2 이상 릴리스를 사용해야 합니다. 일반적으로 최신 SQL Server 릴리스를 사용하여 Azure IaaS에서 SAP 작업을 실행하는 것이 좋습니다. 최신 SQL Server 릴리스는 Azure 서비스 및 기능 중 일부와 더 효율적으로 통합됩니다. 또는 Azure IaaS 인프라에서 작업을 최적화하도록 변경되었습니다. 따라서 이 문서는 SQL Server 2016 및 SQL Server 2017로 제한됩니다.
* **SQL 성능**: Microsoft Azure에서 호스트하는 Virtual Machines는 다른 공용 클라우드 가상화 제품에 비해 뛰어난 성능을 제공하지만 개별 결과는 다를 수 있습니다. [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) 문서를 확인하세요.
* **Azure Marketplace의 이미지 사용**: 새 Microsoft Azure VM을 배포하는 가장 빠른 방법은 Azure Marketplace의 이미지를 사용하는 것입니다. Azure Marketplace에는 최신 SQL Server 릴리스가 포함된 이미지가 있습니다. SQL Server가 이미 설치된 이미지는 SAP NetWeaver 애플리케이션에 즉시 사용할 수 없습니다. 그 이유는 이러한 이미지 내에 SAP NetWeaver 시스템에 필요한 데이터 정렬이 아닌 기본 SQL Server 데이터 정렬이 설치되어 있기 때문입니다. 이러한 이미지를 사용하려면 [Microsoft Azure Marketplace에서 SQL Server 이미지 사용][dbms-guide-5.6] 챕터에서 설명하는 단계를 확인하세요. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>SAP 관련 SQL Server 배포용 VM/VHD 구조에 대한 권장 사항
일반적인 설명에 따라 SQL Server 실행 파일을 VM의 OS 디스크(드라이브 C:\)에 있는 시스템 드라이브에 배치 또는 설치해야 합니다.  일반적으로 대부분의 SQL Server 시스템 데이터베이스는 SAP NetWeaver 워크로드에서 높은 수준으로 사용되지 않습니다. 결과적으로 SQL Server의 시스템 데이터베이스(master, msdb 및 model)가 C:\ 드라이브에도 남아 있을 수 있습니다. 예외는 tempdb이며, SAP 워크로드의 경우 더 많은 데이터 볼륨 또는 I/O 작업 볼륨이 필요할 수 있습니다. I/O 워크로드는 OS VHD에 적용되지 않아야 합니다. 이러한 시스템의 경우 다음 단계를 수행해야 합니다.


* A 시리즈 VM, tempdb 데이터 및 로그 파일을 제외한 모든 SAP 인증 VM 유형(SAP Note [1928533] 참조)은 비영구 D:\ 드라이브에 배치할 수 있습니다. 
* 그럼에도 불구하고 여러 tempdb 데이터 파일을 사용하는 것이 좋습니다. D:\ 드라이브 볼륨은 VM 유형에 따라 다릅니다. 여러 VM의 D:\ 드라이브에 대한 정확한 크기는 [Azure에서 Windows 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 문서를 확인하세요.

이러한 구성을 사용하면 tempdb에서 시스템 드라이브에서 제공할 수 있는 것보다 더 많은 공간을 사용할 수 있습니다. 또한 비영구 D:\ 드라이브는 A- 시리즈 VM을 제외하고 더 효율적인 I/O 대기 시간과 처리량을 제공합니다. 적절한 tempdb 크기를 결정하기 위해 기존 시스템에서 tempdb 크기를 확인할 수 있습니다. 

>[!NOTE]
> tempdb 데이터 파일과 로그 파일을 만든 D:\ 드라이브의 폴더에 저장하는 경우 VM을 다시 부팅한 후에 해당 폴더가 있는지 확인해야 합니다. VM이 다시 부팅되면 D:\ 드라이브가 새로 초기화되므로 모든 파일과 디렉터리의 구조가 초기화됩니다. SQL Server 서비스가 시작되기 전에 D:\ 드라이브에 최종 디렉터리 구조를 다시 만들 수 있는 가능성은 [이 문서](https://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)에서 설명하고 있습니다.

SQL Server와 SAP 데이터베이스를 실행하고 tempdb 데이터 및 tempdb 로그 파일이 D:\ 드라이브에 배치된 VM 구성은 다음과 같습니다.

![SQL Server에 대한 간단한 VM 디스크 구성 다이어그램](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

위의 다이어그램에서는 간단한 경우를 보여 줍니다. [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md) 문서에서 쉽게 이해되지 않았던 것처럼 Premium Storage 디스크의 수와 크기는 다양한 요소에 따라 다릅니다. 그러나 일반적으로 다음과 같이 권장됩니다.

- 저장소 공간을 사용하여 SQL Server 데이터 파일을 포함하는 하나 또는 적은 수의 볼륨을 구성합니다. 이 구성을 지지하는 이유는 실제 상황에서 다른 I/O 워크로드와 다른 크기의 데이터베이스 파일이 포함된 수많은 SAP 데이터베이스가 있다는 것입니다.
- Storage 공간을 사용하여 충분한 IOPS와 SQL Server 트랜잭션 로그 파일을 제공합니다. 잠재적인 IOPS 워크로드는 종종 트랜잭션 로그 볼륨의 크기 조정을 위한 지침일 뿐이며 SQL Server 트랜잭션 볼륨의 잠재적 볼륨은 아닙니다
- 성능이 충분하면 tempdb에 D:\ 드라이브를 사용합니다. tmepdb가 D:\ 드라이브에 위치하여 전체 워크로드의 성능이 제한되는 경우 [이 문서](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)에서 권장하는 대로 tempdb를 별도의 Premium Storage 디스크로 이동해야 할 수도 있습니다.


### <a name="special-for-m-series-vms"></a>M 시리즈 VM에 대한 특별 고려 사항
Azure M 시리즈 VM의 경우 Azure Write Accelerator를 사용하면 Azure Premium Storage 성능에 비해 트랜잭션 로그에 대한 대기 시간 기록을 요인별로 줄일 수 있습니다. 따라서 SQL Server 트랜잭션 로그에 대한 볼륨을 만드는 VHD에 Azure Write Accelerator를 배포해야 합니다. 자세한 내용은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) 문서에서 참조할 수 있습니다.
  

### <a name="formatting-the-disks"></a>디스크 형식 설정
SQL Server의 경우 SQL Server 서버 데이터 및 로그 파일이 포함된 디스크의 NTFS 블록 크기는 64KB여야 합니다. D:\ 드라이브의 형식을 설정할 필요가 없습니다. 이 드라이브는 미리 포맷되어 있습니다.

파일의 내용을 제거하여 데이터베이스를 복원하거나 만들더라도 데이터 파일이 초기화되지 않도록 하려면 SQL Server 서비스가 실행되고 있는 사용자 컨텍스트에 특정 권한이 있는지 확인해야 합니다. 일반적으로 Windows 관리자 그룹의 사용자에게는 이러한 권한이 있습니다. SQL Server 서비스가 Windows 관리자가 아닌 사용자의 사용자 컨텍스트에서 실행되는 경우 해당 사용자에게 **볼륨 유지 관리 작업 수행** 사용자 권한을 할당해야 합니다.  자세한 내용은 이 Microsoft 기술 자료 문서(<https://support.microsoft.com/kb/2574695>)를 참조하세요.

### <a name="impact-of-database-compression"></a>데이터베이스 압축의 영향
I/O 대역폭이 제한 요인이 될 수 있는 구성에서 측정마다 IOPS를 줄여 Azure와 같이 IaaS 시나리오에서 실행할 수 있는 워크로드를 늘릴 수 있습니다. 따라서 아직 수행하지 않은 경우 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP와 Microsoft 모두에서 SQL Server 페이지 압축을 적용하는 것이 좋습니다.

Azure에 업로드하기 전에 데이터베이스 압축을 수행하는 이유는 두 가지입니다.

* 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터베이스 압축은 Azure Virtual Machines에서도 온-프레미스에서와 마찬가지로 잘 작동합니다. 기존 SAP NetWeaver SQL Server 데이터베이스를 압축하는 방법에 대한 자세한 내용은 [향상된 MSSCOMPRESS SAP 압축 도구](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/) 문서를 확인하세요. 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 이상 - Azure Blob Storage에 직접 데이터베이스 파일 저장
SQL Server 2014 이상 릴리스에서는 VHD의 '래퍼' 없이 Azure Blob Storage에 직접 데이터베이스 파일을 저장할 수 있습니다. 특히 표준 Azure Storage 또는 더 작은 VM 유형을 사용하면 이 유형의 배포에서는 일부 더 작은 VM 유형에 탑재할 수 있는 디스크 수를 제한하여 IOPS 제한을 극복할 수 있습니다. 이 배포 방법은 사용자 데이터베이스에는 적용되지만 SQL Server의 시스템 데이터베이스에는 적용되지 않습니다. SQL Server의 데이터 및 로그 파일에도 사용할 수 있습니다. SAP SQL Server 데이터베이스를 VHD에 '래핑'하는 대신 이 방법으로 배포하려는 경우 다음 사항에 유의하세요.

* 사용된 Storage 계정이 SQL Server가 실행 중인 VM을 배포하는 데 사용된 것과 동일한 Azure 지역에 있어야 합니다.
* 앞 부분에서 설명한 여러 Azure Storage 계정에 VHD 분산과 관련된 고려 사항이 이 배포 방법에도 적용됩니다. Azure Storage 계정의 제한에 대한 I/O 작업 수를 의미합니다.
* SQL Server 데이터 및 로그 파일을 나타내는 저장소 Blob에 대한 트래픽은 VM의 저장소 I/O 할당량을 계산하는 대신 특정 VM 유형의 VM 네트워크 대역폭으로 계산됩니다. 특정 VM 유형의 네트워크 및 저장소 대역폭은 [Azure에서 Windows 가상 머신에 대한 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 문서를 참조하세요.
* 네트워크 할당량을 통해 파일 I/O 푸시의 결과로 저장소 할당량을 주로 스트랜딩하고 이와 함께 VM의 전체 대역폭을 부분적으로만 사용합니다.
* Azure Premium Storage에서 유지되는 다른 디스크 크기에 대한 IOPS 및 I/O 처리량 성능 목표는 더 이상 적용되지 않습니다. 만든 Blob이 Azure Premium Storage에 있는 경우에도 마찬가지입니다. 목표는 [VM의 고성능 Premium Storage 및 관리 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) 문서에서 설명하고 있습니다. Azure Premium Storage에 저장된 Blob에 SQL Server 데이터 파일과 로그 파일을 직접 배치한 결과로 Azure Premium Storage의 VHD에 비해 성능 특성이 다를 수 있습니다.
* Azure Premium Storage 디스크에 사용할 수 있는 호스트 기반 캐싱은 Azure Blob에서 SQL Server 데이터 파일을 직접 배치할 때 사용할 수 없습니다.
* M 시리즈 VM에서는 Azure Write Accelerator를 사용하여 SQL Server 트랜잭션 로그 파일에 대한 밀리초 미만의 쓰기를 지원할 수 없습니다. 

이 기능에 대한 자세한 내용은 [Microsoft Azure의 SQL Server 데이터 파일](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)에서 찾을 수 있습니다.

프로덕션 시스템에 대한 권장 사항은 이 구성을 피하고, SQL Server 데이터 및 로그 파일을 Azure Blob에 직접 배치하는 대신 Azure Premium Storage VHD에 배치하도록 선택하는 것입니다.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 버퍼 풀 확장
SQL Server 2014에는 [버퍼 풀 확장](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017)이라는 새로운 기능이 도입되었습니다. 이 기능은 서버 또는 VM의 로컬 SSD에서 지원되는 보조 수준 캐시를 사용하여 메모리에서 유지되는 SQL Server의 버퍼 풀을 확장합니다. 버퍼 풀 확장을 사용하면 데이터의 더 큰 작업 집합을 '메모리 내에서' 유지할 수 있습니다. Azure Standard Storage 액세스에 비해 Azure VM의 로컬 SSD에 저장된 버퍼 풀 확장에 대한 액세스는 많은 요소를 더 빠르게 만듭니다. SQL Server 데이터 파일에 권장된 것처럼 버퍼 풀 확장을 Azure Premium Storage 읽기 캐시와 비교하면 버퍼 풀 확장에 상당한 이점이 없습니다. 이는 두 캐시(SQL Server 버퍼 풀 확장 및 Premium Storage 읽기 캐시) 모두에서 Azure 계산 노드의 로컬 디스크를 사용하기 때문입니다.

SAP 워크플로가 포함된 SQL Server 버퍼 풀 확장을 통해 얻은 경험은 엇갈리고 있으며, 아직도 모든 경우에 사용할지 여부에 대한 명확한 권장 사항이 허용되지 않고 있습니다. 이상적으로는 SAP 애플리케이션에 필요한 작업 집합이 주 메모리에 적합하다는 것입니다. 한편 Azure는 최대 4TB의 메모리가 있는 VM을 제공하므로 작업 집합을 메모리에서 유지할 수 있습니다. 따라서 버퍼 풀 확장은 일부 드문 경우에만 사용하도록 제한되며 일반적인 사례로 사용되지 않아야 합니다.  

## <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server에 대한 Backup/복구 고려 사항
SQL Server를 Azure에 배포하는 경우 백업 방법을 검토해야 합니다. 프로덕션 시스템이 아닌 경우에도 SQL Server에서 호스팅하는 SAP 데이터베이스는 정기적으로 백업해야 합니다. Azure Storage에는 이제 세 개의 이미지가 있으므로 스토리지 작동 중단을 보완하는 측면에서 백업의 중요성이 줄어들었습니다. 적절한 백업 및 복구 계획 유지 관리가 중요한 이유는 특정 시점 복구 기능을 제공하여 논리/수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 다시 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 

Azure에서 다양한 SQL Server 백업 가능성을 살펴보려면 [Azure Virtual Machines에서 SQL Server의 백업 및 복원](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery) 문서를 참조하세요. 이 문서에서는 여러 가지 가능성을 다루고 있습니다.

### <a name="manual-backups"></a>수동 백업
'수동' 백업을 수행할 수 있는 몇 가지 방법은 다음과 같습니다.

1. 직접 연결된 Azure 디스크에 기존 SQL Server 백업을 수행합니다. 이 방법의 경우 백업을 시스템 새로 고침에 빠르게 사용할 수 있고, 새 시스템을 기존 SAP 시스템의 복사본으로 구축할 수 있다는 이점이 있습니다.
2.  SQL Server 2012 CU4 이상에서는 데이터베이스를 Azure 저장소 URL에 백업할 수 있습니다.
3.  Azure Blob Storage의 데이터베이스 파일에 대한 파일-스냅숏 백업 이 방법은 SQL Server 데이터 및 로그 파일이 Azure Blob Storage에 있는 경우에만 작동합니다.

첫 번째 방법은 잘 알려져 있고도 온-프레미스 환경에서 대부분의 경우에 적용 합니다. 그럼에도 불구하고 사용자가 장기 백업 위치를 해결해야 합니다. 로컬로 연결된 Azure Storage에서 30일 이상 백업을 유지하지 않으려면, Azure Backup 서비스 또는 백업에 대한 액세스 및 보존 관리가 포함된 다른 타사 백업/복구 도구 중 하나를 사용해야 합니다. 또는 Windows 저장소 공간을 사용하여 Azure에서 대형 파일 서버를 구축합니다.

두 번째 방법은 [URL에 대한 SQL Server 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017) 문서에서 자세히 설명하고 있습니다. SQL Server의 다른 릴리스에서는 이 기능이 약간 다릅니다. 따라서 이러한 특정 SQL Server 릴리스와 관련하여 설명서를 확인해야 합니다. 이 문서에는 많은 제한 사항이 나와 있습니다. 다음에 대해 백업을 수행할 수 있습니다.

- 하나의 단일 Azure 페이지 Blob - 백업 크기가 1,000GB로 제한됩니다. 달성 가능한 처리량도 제한됩니다.
- 여러 Azure 블록 Blob(최대 64개) - 이론적인 백업 크기인 12TB를 사용할 수 있습니다. 그러나 고객 데이터베이스를 테스트한 결과에서 최대 백업 크기가 이론적인 제한보다 작을 수 있다고 밝혀졌습니다. 이 경우에는 백업 보존 및 백업 액세스를 관리해야 합니다.


### <a name="automated-backup-for-sql-server"></a>자동화된 SQL Server 백업
자동화된 백업은 Azure의 Windows VM에서 실행 중인 SQL Server Standard 및 Enterprise 버전에 대한 자동 백업 서비스를 제공합니다. 이 서비스는 Azure Portal에서 SQL Server Windows 가상 머신 이미지에 자동으로 설치되는 [SQL Server IaaS 에이전트 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)에서 제공됩니다. SQL Server가 설치된 사용자 고유의 OS 이미지를 배포하는 경우 VM 확장을 별도로 설치해야 합니다. 필요한 단계는 [이 문서](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)에서 설명하고 있습니다.

이 방법의 기능에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다.

- SQL Server 2014: [SQL Server 2014 Virtual Machines의 자동화된 Backup(Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Azure Virtual Machines의 자동화된 백업 v2(Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

설명서를 살펴보면 최신 SQL Server 릴리스의 기능이 향상되었음을 알 수 있습니다. 자동화된 SQL Server 백업에 대한 자세한 내용은 [Microsoft Azure에 대한 SQL Server Managed Backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017) 문서에서 설명하고 있습니다. 이론적인 백업 크기 제한은 12TB입니다.  자동화된 백업은 최대 12TB의 백업 크기에 적합한 방법입니다. 여러 Blob이 병렬로 기록되므로 100MB/초보다 큰 처리량을 기대할 수 있습니다. 
 

### <a name="azure-backup-for-sql-server-vms"></a>SQL Server VM용 Azure Backup
이 새로운 SQL Server 백업 방법은 2018년 6월부터 Azure Backup 서비스에서 공개 미리 보기로 제공됩니다. SQL Server를 백업하는 방법은 다른 타사 도구, 즉 SQL Server VSS/VDI 인터페이스를 사용하여 백업을 대상 위치로 스트림하는 것과 같습니다. 이 경우 대상 위치는 Azure Recovery Service 자격 증명 모음입니다.

중앙 백업 구성, 모니터링 및 관리의 많은 이점이 추가된 이 백업 방법에 대한 매우 자세한 내용은 [여기](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)에 나와 있습니다. 


### <a name="third-party-backup-solutions"></a>타사 백업 솔루션
매우 많은 SAP 고객은 Azure에서 실행되는 SAP 자산의 일부로 완전히 새로운 백업 솔루션을 처음부터 다시 시작하거나 도입할 수 없었습니다. 결과적으로 기존 백업 솔루션을 사용하고 Azure로 확장해야 했습니다. 기존 백업 솔루션을 Azure로 확장하는 것은 일반적으로 이 영역의 주요 공급업체 대부분에서 잘 수행되었습니다. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Microsoft Azure Marketplace에서 SQL Server 이미지 사용
Microsoft는 Azure Marketplace에서 이미 SQL Server를 포함하는 버전의 VM을 제공합니다. SQL Server 및 Windows에 대한 라이선스가 필요한 SAP 고객의 경우, 이러한 이미지를 사용하면 이미 SQL Server가 설치된 VM의 회전율을 높여 라이선스에 대한 요구 사항을 충족시킬 수 있습니다. SAP에 대한 이러한 이미지를 사용하려면 다음 사항을 고려해야 합니다.

* 평가판이 아닌 SQL Server 버전은 Azure Marketplace에서 배포한 'Windows 전용' VM보다 비용이 더 높습니다. 가격을 비교하려면 <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> 및 <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/> 문서를 참조하세요. 
* SAP에서 지원하는 SQL Server 릴리스만 사용할 수 있습니다.
* Azure Marketplace에서 제공되는 VM에 설치되는 SQL Server 인스턴스의 데이터 정렬은 SAP NetWeaver에서 SQL Server 인스턴스를 실행하는 데 필요한 데이터 정렬이 아닙니다. 다음 섹션의 지침을 사용하여 데이터 정렬을 변경할 수 있습니다.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Microsoft Windows/SQL Server VM의 SQL Server 데이터 정렬 변경
Azure Marketplace의 SQL Server 이미지는 SAP NetWeaver 애플리케이션에서 요구하는 데이터 정렬을 사용하도록 설정되어 있지 않으므로 배포 후 즉시 변경해야 합니다. SQL Server의 경우 이 정렬 변경은 VM을 배포하고 관리자가 배포된 VM에 로그인할 수 있게 되는 즉시 다음 단계를 통해 수행할 수 있습니다.

* 관리자 권한으로 Windows 명령 창을 엽니다.
* 디렉터리를 C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012로 변경합니다.
* 다음 명령을 실행합니다. Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`>은 갤러리를 통해 처음으로 VM을 배포할 때 관리자 계정으로 정의된 계정입니다.

이 프로세스는 몇 분밖에 안 걸립니다. 단계가 올바르게 수행되었는지 확인하려면 다음 단계를 수행하세요.

* SQL Server Management Studio를 엽니다.
* 쿼리 창을 엽니다.
* SQL Server master 데이터베이스에서 sp_helpsort 명령을 실행합니다.

다음과 같은 결과가 나와야 합니다.

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

결과가 다른 경우 SAP 배포를 중지하고 설치 명령이 예상대로 작동하지 않은 이유를 조사합니다. 위에서 언급한 것과 다른 SQL Server 코드 페이지를 사용하여 SAP NetWeaver 애플리케이션을 SQL Server 인스턴스에 배포할 수는 **없습니다** .

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Azure의 SAP용 SQL Server 고가용성
SAP용 Azure IaaS 배포에서 SQL Server를 사용하면 DBMS 계층을 고가용성으로 배포하기 위해 추가할 수 있는 여러 가지 다른 가능성이 있습니다. [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 이미 설명한 대로 Azure는 Azure 가용성 집합에 배포된 단일 VM과 한 쌍의 VM에 대해 서로 다른 가동 시간 SLA를 제공합니다. Azure 가용성 집합에서 배포가 필요한 프로덕션 배포에 대한 가동 시간 SLA를 추진하고 있다고 가정합니다. 이 경우 가용성 집합에 최소 2개의 VM을 배포해야 합니다. 한 VM에서 활성 SQL Server 인스턴스를 실행하며, 다른 하나의 VM에서는 수동 인스턴스를 실행합니다.

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Windows 스케일 아웃 파일 서버를 사용하는 SQL Server 클러스터링
Microsoft는 Windows Server 2016에서 [저장소 공간 직접 배포](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)를 도입했습니다. 저장소 공간 직접 배포에 따라 SQL Server FCI 클러스터링이 지원됩니다. 자세한 내용은 [Azure Virtual Machines에 SQL Server 장애 조치(Failover) 클러스터 인스턴스 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster) 문서에서 찾을 수 있습니다. 이 솔루션에는 클러스터 리소스의 가상 IP 주소를 처리하는 Azure 부하 분산 장치도 필요합니다. SQL Server 데이터베이스 파일은 저장소 공간에 저장됩니다. 따라서 Azure Premium Storage에 기반한 Windows 스토리지 공간을 구축해야 합니다. 이 솔루션은 아직 지원되지 않기 때문에 SAP 프로덕션 시나리오에서 이 솔루션을 사용하는 것으로 알려진 SAP 고객이 없습니다.  

### <a name="sql-server-log-shipping"></a>SQL Server 로그 전달
HA(고가용성) 방법 중 하나는 SQL Server 로그 전달입니다. HA 구성에 참여하는 VM에 이름 확인 작업이 있는 경우 아무 문제가 없으며 Azure 설정이 온-프레미스의 설정과 다르지 않습니다. 로그 전달 및 로그 전달 관련 원칙을 설정하는 것과 관련하여 SQL Server 로그 전달에 대한 자세한 내용은 [로그 전달 정보(SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017) 문서에서 찾을 수 있습니다.

SQL Server 로그 전달 기능은 Azure에서 거의 사용되지 않아 하나의 Azure 지역 내에서 고가용성을 달성할 수 없었습니다. 그러나 다음 시나리오에서 SAP 고객은 Azure와 함께 로그 전달을 성공적으로 사용하고 있었습니다.

- Azure 지역 간 재해 복구 시나리오
- Azure 지역에 온-프레미스에서 재해 복구 구성
- 온-프레미스에서 Azure로 시나리오를 cut over입니다. 이러한 경우 로그 전달은 Azure에서 새 DBMS 배포를 온-프레미스에서 진행 중인 프로덕션 시스템과 동기화하는 데 사용됩니다. 전환 시점에서 프로덕션이 종료되고, 마지막 및 최신 트랜잭션 로그 백업이 Azure DBMS 배포로 전송되었는지 확인합니다. 그러면 프로덕션에 대한 Azure DBMS 배포가 열립니다.  



### <a name="database-mirroring"></a>데이터베이스 미러링
SAP에서 지원하는 데이터베이스 미러링(SAP Note [965908]참조)은 SAP 연결 문자열의 장애 조치(failover) 파트너 정의를 사용합니다. 프레미스 간 사례의 경우 두 VM이 동일한 도메인에 있고 두 개의 SQL Server 인스턴스를 실행 중인 사용자 컨텍스트도 도메인 사용자이며 관련된 두 개의 SQL Server 인스턴스에 대한 충분한 권한이 있다고 가정합니다. 따라서 Azure에서의 데이터베이스 미러링 설정은 일반적인 온-프레미스 설정/구성 간에 다르지 않습니다.

클라우드 전용 배포 시 가장 쉬운 방법은 Azure에서 이러한 DBMS VM(및 이상적인 전용 SAP VM)을 한 도메인 내에 배치할 수 있도록 다른 도메인을 설정하는 것입니다.

도메인을 사용할 수 없는 경우 <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>에서 설명한 대로 데이터베이스 미러링 엔드포인트에 대한 인증서를 사용할 수도 있습니다.

Azure에서 데이터베이스 미러링을 설정하기 위한 자습서는 <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server>에서 찾을 수 있습니다. 

### <a name="sql-server-always-on"></a>SQL Server Always On
Always On은 SAP 온-프레미스에 대해 지원되므로(SAP Note [1772688]참조) Azure에서 SAP와 함께 지원됩니다. 이 시점에서 Azure는 온-프레미스에서 가능한 AD/DNS 개체를 만들 수 없으므로 SQL Server 가용성 그룹 수신기(Azure 가용성 집합과 다름)를 배포하는 것과 관련된 몇 가지 특별한 고려 사항이 있습니다. 따라서 Azure의 특정 동작을 극복하려면 몇 가지 다른 설치 단계가 필요합니다.

가용성 그룹 수신기를 사용하는 경우 몇 가지 고려 사항이 있습니다.

* 가용성 그룹 수신기는 Windows Server 2012 이상을 VM의 게스트 OS로 사용할 때만 사용할 수 있습니다. Windows Server 2012의 경우 <https://support.microsoft.com/kb/2854082> 패치가 적용되어 있는지 확인해야 합니다. 
* Windows Server 2008 R2의 경우 이 패치가 없으며, 연결 문자열에서 장애 조치 파트너를 지정하여 데이터베이스 미러링과 동일한 방식으로 Always On을 사용해야 합니다(SAP default.pfl 매개 변수 dbs/mss/server를 통해 수행 - SAP Note [965908] 참조).
* 가용성 그룹 수신기를 사용할 경우 데이터베이스 VM을 전용 부하 분산 장치에 연결해야 합니다. 두 VM이 우발적으로 동시에 종료되는 경우 Azure에서 새 IP 주소를 할당하지 않도록 하려면 Always On 구성에서 해당 VM의 네트워크 인터페이스에 고정 IP 주소를 할당해야 합니다(고정 IP 주소 정의는 [이 문서][virtual-networks-reserved-private-ip]에서 설명).
* 현재 기능의 Azure는 클러스터가 만들어진 노드와 동일한 IP 주소를 클러스터 이름에 할당하므로 클러스터에 특정 IP 주소를 할당해야 하는 WSFC 클러스터를 구성할 때는 특별한 단계가 필요합니다. 즉, 클러스터에 다른 IP 주소를 할당하기 위해서는 수동 단계를 수행해야 합니다.
* 가용성 그룹 수신기는 가용성 그룹의 기본 및 보조 복제본을 실행 중인 VM에 할당된 TCP/IP 엔드포인트를 사용하여 Azure에서 만들어집니다.
* 이러한 엔드포인트는 ACL로 보호해야 할 수 있습니다.

Azure VM에 SQL Server와 함께 Always On을 배포하는 방법에 대한 자세한 설명서는 다음과 같습니다.

- [Azure Virtual Machines의 SQL Server Always On 가용성 그룹 소개](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
- [다른 하위 지역의 Azure Virtual Machines에서 Always On 가용성 그룹 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
- [Azure에서 Always On 가용성 그룹에 대한 부하 분산 장치 구성](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)

>[!NOTE]
> 가용성 그룹 수신기의 가상 IP 주소로 에 대해 Azure 부하 분산 장치를 구성하는 경우 DirectServerReturn이 구성되어 있는지 확인합니다. 이 옵션을 구성하면 SAP 애플리케이션 계층과 DBMS 계층 간의 네트워크 왕복 대기 시간이 줄어듭니다. 

SQL Server Always On은 SAP 워크로드용 Azure 배포에 가장 일반적으로 사용되는 고가용성 및 재해 복구 기능입니다. 대부분의 고객은 단일 Azure 지역 내의 고가용성을 위해 Always On을 사용합니다. 배포가 두 개의 노드로만 제한되는 경우 두 가지 연결 옵션이 있습니다.

- 가용성 그룹 수신기 사용. 가용성 그룹 수신기를 사용하여 Azure 부하 분산 장치를 배포해야 합니다. 이는 일반적으로 기본적인 배포 방법입니다. SAP 애플리케이션은 단일 노드가 아니라 가용성 그룹 수신기에 연결되도록 구성됩니다.
- SQL Server 데이터베이스 미러링의 연결 매개 변수 사용. 이 경우 두 노드의 이름이 지정된 방식으로 SAP 애플리케이션의 연결을 구성해야 합니다. 이러한 SAP 쪽 구성에 대한 정확한 세부 정보는 SAP Note [#965908](https://launchpad.support.sap.com/#/notes/965908)에서 설명하고 있습니다. 이 옵션을 사용하면 가용성 그룹 수신기를 구성할 필요가 없습니다. 그리고 SQL Server 고가용성을 위한 Azure 부하 분산 장치도 없습니다. 결과적으로 SQL Server 인스턴스로 들어오는 트래픽이 Azure 부하 분산 장치를 통해 라우팅되지 않기 때문에 SAP 애플리케이션 계층과 DBMS 계층 간의 네트워크 대기 시간이 줄어듭니다. 그러나 이 옵션은 가용성 그룹이 두 인스턴스에 걸쳐 있도록 제한하는 경우에만 작동합니다. 

많은 고객이 Azure 지역 간의 추가 재해 복구 기능에 대해 SQL Server Always On 기능을 활용하고 있습니다. 일부 고객은 보조 복제본에서 백업을 수행하는 기능도 사용합니다. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server 투명한 데이터 암호화
Azure에서 SAP SQL Server 데이터베이스를 배포하는 경우 많은 고객이 SQL Server [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)를 사용하고 있습니다. SQL Server TDE 기능은 SAP에서 완벽하게 지원됩니다(SAP Note [#1380493](https://launchpad.support.sap.com/#/notes/1380493) 참조). 

### <a name="applying-sql-server-tde"></a>SQL Server TDE 적용
온-프레미스에서 실행되는 다른 DBMS에서 Azure에서 실행되는 Windows/SQL Server로 이기종 마이그레이션을 수행하는 경우, SQL Server에 빈 대상 데이터베이스를 미리 만들어야 합니다. 다음 단계로 SQL Server TDE 기능을 적용합니다. 한편 프로덕션 시스템은 온-프레미스에서 계속 실행되고 있습니다. 이 순서로 수행하려는 이유는 빈 데이터베이스를 암호화하는 프로세스에 시간이 상당히 오래 걸릴 수 있다는 것입니다. 그러면 SAP 가져오기 프로세스에서 가동 중지 시간 단계 동안 데이터를 암호화된 데이터베이스로 가져옵니다. 암호화된 데이터베이스로 가져오는 오버헤드는 가동 중지 시간 단계에서 내보내기 단계 이후에 데이터베이스를 암호화하는 것보다 시간으로 인한 영향이 훨씬 적습니다. 데이터베이스를 기반으로 하여 실행되는 SAP 워크로드를 사용하여 TDE를 적용하려고 할 때 부정적인 경험을 하게 됩니다. 따라서 TDE 배포를 특정 데이터베이스에서 SAP 워크로드 없이 수행해야 하는 작업으로 처리하는 것이 좋습니다.

Azure로 온-프레미스에서 SAP SQL Server 데이터베이스를 이동 하는 있는 경우에는 인프라 적용 가장 빠른 암호화를 가져올 수 있습니다에 테스트 하는 것이 좋습니다. 이를 위해 다음 사실에 유념하세요.

- 데이터베이스에 데이터 암호화를 적용하는 데 사용되는 스레드 수는 정의할 수 없습니다. 스레드 수는 주로 SQL Server 데이터 및 로그 파일이 분산되는 디스크 볼륨의 수에 따라 달라집니다. 즉, 더 명확한 볼륨(드라이브 문자)이 있을수록 암호화를 수행하기 위해 더 많은 스레드가 병렬로 참여하게 됩니다. 이러한 구성은 Azure VM에서 SQL Server 데이터베이스 파일용 저장소 공간을 하나 또는 여러 개를 구축하는 경우의 이전 디스크 구성 제안과 약간 충돌합니다. 볼륨 수가 적은 구성에서는 암호화를 실행하는 스레드 수가 적습니다. 단일 스레드 암호화는 64KB 익스텐트를 읽고, 암호화한 다음, 익스텐트가 암호화되었음을 알리는 레코드를 트랜잭션 로그 파일에 기록합니다. 결과적으로 트랜잭션 로그의 부하는 중간 수준입니다.
- 이전의 SQL Server 릴리스에서는 SQL Server 데이터베이스를 암호화할 때 백업 압축이 더 이상 효율적이지 못했습니다. 이 동작은 SQL Server 데이터베이스 온-프레미스를 암호화 하 고 Azure에서 데이터베이스를 복원 하는 Azure에 백업 복사를 계획할 때 문제가 발생 개발할 수 있습니다. SQL Server 백업 압축은 일반적으로 요인 4의 압축 비율을 달성합니다.
- SQL Server 2016에서 SQL Server는 효율적인 방식으로 암호화된 데이터베이스도 압축할 수 있는 새로운 기능을 도입했습니다. 자세한 내용은 [이 블로그](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/)를 참조하세요.
 
TDE 암호화 없이 거의 SAP 워크 로드에만 응용 프로그램 처리를 SAP 데이터베이스 온-프레미스에 TDE를 적용 하거나 Azure에서 이렇게 하려면 더 나은 인지에 특정 구성에서 테스트 해야 합니다. Azure에서는 TDE가 적용된 후에 인프라를 과도하게 프로비전하고 축소할 수 있다는 측면에서 유연성이 더 뛰어납니다.

### <a name="using-azure-key-vault"></a>Azure Key Vault 사용
Azure는 암호화 키를 저장하기 위해 [Key Vault](https://azure.microsoft.com/services/key-vault/) 서비스를 제공합니다. 다른 쪽의 SQL Server는 Azure Key Vault를 TDE 인증서 저장소로 활용할 수 있는 커넥터를 제공합니다.

SQL Server TDE에 Azure Key Vault를 사용하는 방법에 대한 자세한 내용은 다음 문서에 나와 있습니다.

- [Azure Key Vault(SQL Server)를 사용한 확장 가능 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017)
- [Azure Key Vault를 사용한 SQL Server TDE 확장 가능 키 관리 - 설정 단계](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017)
- [SQL Server 커넥터 유지 관리 및 문제 해결](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017)
- [SQL Server 투명한 데이터 암호화에 대한 고객의 자세한 질문 - TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/)


>[!IMPORTANT]
>특히 Azure Key Vault에서 SQL Server TDE를 사용하는 경우 SQL Server 2014, SQL Server 2016 및 SQL Server 2017의 최신 패치를 사용하는 것이 좋습니다. 이는 고객의 피드백에 따라 최적화 및 수정이 코드에 적용되었기 때문입니다. 예를 들어 [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm)를 확인하세요.
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Azure의 SAP용 SQL Server에 대한 일반적 요약
이 가이드에는 많은 권장 사항이 있으며 Azure 배포를 계획하기 전에 두 번 이상 읽는 것이 좋습니다. 하지만 일반적으로 Azure 관련 권장 사항에서 다음과 같은 최상위 일반 DBMS를 따라야 합니다.

1. SQL Server 2017과 같이 Azure에서 가장 많은 이점을 제공하는 최신 DBMS 릴리스를 사용합니다. 
2. 데이터 파일 레이아웃과 Azure 제한 사항 균형을 조정하도록 Azure에서 SAP 시스템 배경을 신중하게 계획합니다.
   * 디스크가 너무 많으면 안 되지만 필요한 IOPS에 도달할 수 있을 만큼 충분해야 합니다.
   * Managed Disks를 사용하지 않는 경우 IOPS는 Azure Storage 계정별로 제한되며, 해당 스토리지 계정도 각 Azure 구독 내에서 제한됩니다([자세한 내용][azure-subscription-service-limits]). 
   * 더 높은 처리량이 필요한 경우에만 디스크를 스트라이프합니다.
3. D:\ 드라이브는 비영구적이며 Windows 재부팅 시 이 드라이브의 내용이 모두 손실되므로 계속 유지해야 하는 소프트웨어를 설치하거나 파일을 저장하지 마세요.
4. Azure Standard Storage에 대해 디스크 캐싱을 사용하지 마세요.
5. Azure 지역에서 복제된 Azure Standard Storage 계정은 사용하지 않습니다.  DBMS 워크로드에 대한 로컬 중복을 사용합니다.
6. DBMS 공급업체의 HA/DR 솔루션을 사용하여 데이터베이스 데이터를 복제합니다.
7. 항상 이름 확인을 사용하고 IP 주소를 사용하지 마세요.
8. SQL Server TDE를 사용하여 최신 SQL Server 패치를 적용합니다.
9. 가능한 가장 높은 데이터베이스 압축을 사용합니다. 이는 SQL Server에 대한 페이지 압축입니다.
10. Azure Marketplace의 SQL Server 이미지를 사용할 때는 주의하세요. SQL Server 이미지를 사용하는 경우 SAP NetWeaver 시스템을 설치하기 전에 인스턴스 데이터 정렬을 변경해야 합니다.
11. [배포 가이드][deployment-guide]에서 설명한 대로 Azure용 SAP 호스트 모니터링을 설치 및 구성합니다.

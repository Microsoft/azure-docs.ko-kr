---
title: SAP 워크로드용 Oracle Azure Virtual Machines DBMS 배포 | Microsoft Docs
description: SAP 워크로드용 Oracle Azure Virtual Machines DBMS 배포
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
ms.date: 12/14/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5182b621779cf31f3c7da99674ab24fe6efe702d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835263"
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드용 Azure Virtual Machines DBMS 배포

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


이 문서에서는 Azure IaaS에서 SAP 워크로드용 Oracle Database를 배포할 때 고려해야 할 몇 가지 다른 영역에 대해 설명합니다. 이 문서를 읽기 전에 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)을 읽어보는 것이 좋습니다. [Azure의 SAP 워크로드 설명서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)의 다른 가이드를 참조하는 것도 좋습니다. 

SAP Note [2039619]에서 Azure Oracle의 SAP 실행을 위해 지원되는 Oracle 버전 및 해당 OS 버전에 대한 정보를 찾을 수 있습니다.

Oracle에서 SAP Business Suite를 실행하는 방법에 대한 일반적인 내용은 [Oracle의 SAP](https://www.sap.com/community/topic/oracle.html)에서 찾을 수 있습니다.
Oracle 소프트웨어는 Microsoft Azure에서 실행되도록 Oracle에서 지원합니다. Windows Hyper-V 및 Azure에 대한 일반 지원에 대한 자세한 내용은 [Oracle 및 Microsoft Azure FAQ](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)를 참조하세요. 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Oracle, SAP 및 Azure와 관련된 SAP Note 

다음 SAP 정보는 Azure의 SAP와 관련이 있습니다.

| Note 번호 | 제목 |
| --- | --- |
| [1928533] |Azure의 SAP 애플리케이션: 지원 제품 및 Azure VM 유형 |
| [2015553] |Microsoft Azure의 SAP: 필수 구성 요소 지원 |
| [1999351] |SAP용 고급 Azure 모니터링 문제 해결 |
| [2178632] |Microsoft Azure의 SAP용 주요 모니터링 메트릭 |
| [2191498] |Azure 포함 Linux의 SAP: 고급 모니터링 |
| [2039619] |Oracle Database를 사용하는 Microsoft Azure의 SAP 애플리케이션: 지원되는 제품 및 버전 |
| [2243692] |Microsoft Azure(IaaS) VM의 Linux: SAP 라이선스 문제 |
| [2069760] |Oracle Linux 7.x SAP 설치 및 업그레이드 |
| [1597355] |Linux에 대한 스왑 공간 권장 사항 |
| [2171857] |Oracle Database 12c - Linux에서 파일 시스템 지원 |
| [1114181] |Oracle Database 11g - Linux에서 파일 시스템 지원 |

Oracle 및 Azure의 SAP에서 지원하는 정확한 구성과 기능은 SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619)에서 설명하고 있습니다.

Windows 및 Oracle Linux는 Oracle 및 Azure의 SAP에서 지원되는 유일한 운영 체제입니다. 널리 사용되는 SLES 및 RHEL Linux 배포는 Azure에서 Oracle 구성 요소를 배포하는 데 지원되지 않습니다. Oracle 구성 요소에는 SAP 애플리케이션에서 Oracle DBMS에 연결하는 데 사용되는 Oracle 데이터베이스 클라이언트가 포함됩니다. 

SAP Note [#2039619](https://launchpad.support.sap.com/#/notes/2039619)에 따른 예외는 Oracle 데이터베이스 클라이언트를 사용하지 않는 SAP 구성 요소입니다. 이러한 SAP 구성 요소는 SAP의 독립 실행형 큐에 넣기, 메시지 서버, 큐에 넣기 복제 서비스, WebDispatcher 및 SAP 게이트웨이입니다.  

Oracle Linux에서 Oracle DBMS 및 SAP 애플리케이션 인스턴스를 실행하고 있더라도 SLES 또는 RHEL에서 SAP Central Services를 실행하고 Pacemaker 기반 클러스터를 사용하여 이 서비스를 보호할 수 있습니다. 고가용성 프레임워크인 Pacemaker는 Oracle Linux에서 지원되지 않습니다.

## <a name="specifics-for-oracle-database-on-windows"></a>Windows의 Oracle 데이터베이스에 대한 고유 정보

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Windows의 Azure VM에서 SAP 설치에 대한 Oracle 구성 지침

SAP 설치 설명서에 따라 모든 Oracle 관련 파일을 VM의 OS 디스크(드라이브 C:)에 대한 시스템 드라이버에 설치하거나 배치해서는 안 됩니다. 다양한 크기의 가상 머신이 연결된 디스크를 다양하게 지원할 수 있습니다. 가상 머신 유형이 적으면 지원할 수 있는 연결된 디스크 수도 적습니다. 

작은 VM이 있는 경우 Oracle 홈, 단계, "saptrace", "saparch", "sapbackup", "sapcheck" 또는 "sapreorg"를 OS 디스크에 설치/배치하는 것이 좋습니다. Oracle DBMS 구성 요소의 이러한 부분은 I/O 및 I/O 처리량에 무리는 아닙니다. 따라서 OS 디스크는 I/O 요구 사항을 처리할 수 있습니다. OS 디스크의 기본 크기는 127GB입니다. 

사용 가능한 여유 공간이 충분하지 않은 경우 2,048GB로 디스크 [크기를 조정](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)할 수 있습니다. Oracle 데이터베이스와 다시 실행 로그 파일을 별도 데이터 디스크에 저장해야 합니다. Oracle 임시 테이블스페이스가 포함된 예외가 있습니다. Tempfiles를 D:/(비영구 드라이브)에서 만들 수 있습니다. 또한 비영구 D:\ 드라이브는 A- 시리즈 VM을 제외하고 더 효율적인 I/O 대기 시간과 처리량을 제공합니다. 

tempfiles를 위한 적절한 양의 공간 크기를 결정하려면 기존 시스템의 tempfiles 크기를 확인할 수 있습니다.

### <a name="storage-configuration"></a>Storage 구성
NTFS로 포맷된 디스크를 사용하는 하나의 Oracle 인스턴스만 지원됩니다. 모든 데이터베이스 파일은 Managed Disks(권장) 또는 VHD의 NTFS 파일 시스템에 저장되어야 합니다. 이러한 디스크는 Azure VM에 탑재되며, [Azure 페이지 Blob 스토리지](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) 또는 [Azure Managed Disks](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)를 기준으로 합니다. 

[Azure Managed Disks](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)를 사용하는 것이 좋습니다. 또한 Oracle Database 배포에는 [프리미엄 SSD](../../windows/disks-types.md)를 사용하는 것이 좋습니다.

네트워크 드라이브 또는 Azure 파일 서비스와 같은 원격 공유는 Oracle Database 파일에 대해 지원되지 않습니다. 자세한 내용은 다음을 참조하세요.

- [Microsoft Azure 파일 서비스 소개](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Microsoft Azure 파일에 대한 연결 유지](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)


Azure 페이지 Blob 스토리지 또는 Managed Disks를 기준으로 하는 디스크를 사용하는 경우 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에 제공된 설명은 Oracle Database를 사용한 배포에도 적용됩니다.

Azure 디스크에 대한 IOPS 처리량의 할당량이 존재합니다. 이 개념은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 설명하고 있습니다. 정확한 할당량은 사용하는 VM 유형에 따라 달라집니다. VM 유형과 해당 할당량 목록은 [Azure의 Windows Virtual Machines 크기][virtual-machines-sizes-windows]에서 확인할 수 있습니다.

지원되는 Azure VM 유형을 식별하려면 SAP Note [1928533]을 참조하세요.

최소 구성은 다음과 같습니다. 

| 구성 요소 | 디스크 | 구성 | 스토리지 풀 |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA & mirrlogB | Premium | 없음 | 필요하지 않음 |
| \oracle\<SID>\origlogaB & mirrlogA | Premium | 없음 | 필요하지 않음 |
| \oracle\<SID>\sapdata1...n | Premium | 읽기 전용 | 사용할 수 있음 |
| \oracle\<SID>\oraarch | Standard | 없음 | 필요하지 않음 |
| Oracle 홈, saptrace, ... | OS 디스크 | | 필요하지 않음 |


온라인 다시 실행 로그를 호스팅하기 위한 디스크 선택은 IOPS 요구 사항에 따라 결정되어야 합니다. 크기, IOPS 및 처리량이 요구 사항을 충족하는 경우 모든 sapdata1...n(테이블스페이스)을 단일 탑재 디스크에 저장할 수 있습니다. 

성능 구성은 다음과 같습니다.

| 구성 요소 | 디스크 | 구성 | 스토리지 풀 |
| --- | ---| --- | --- |
| \oracle\<SID>\origlogaA | Premium | 없음 | 사용할 수 있음  |
| \oracle\<SID>\origlogaB | Premium | 없음 | 사용할 수 있음 |
| \oracle\<SID>\mirrlogAB | Premium | 없음 | 사용할 수 있음 |
| \oracle\<SID>\mirrlogBA | Premium | 없음 | 사용할 수 있음 |
| \oracle\<SID>\sapdata1...n | Premium | 읽기 전용 | 권장  |
| \oracle\SID\sapdata(n+1)* | Premium | 없음 | 사용할 수 있음 |
| \oracle\<SID>\oraarch* | Premium | 없음 | 필요하지 않음 |
| Oracle 홈, saptrace, ... | OS 디스크 | 필요하지 않음 |

*(n+1): SYSTEM, TEMP 및 UNDO 테이블스페이스를 호스트합니다. I/O 패턴의 시스템 및 Undo 테이블스페이스는 애플리케이션 데이터를 호스팅하는 다른 테이블스페이스와 다릅니다. 캐싱 없음이 시스템의 성능 및 Undo 테이블스페이스에 최적의 옵션입니다.

* oraarch: 성능 관점에서 스토리지 풀이 필요하지 않습니다. 더 많은 공간을 확보하는 데 사용할 수 있습니다.

더 많은 IOPS가 필요한 경우 Window 스토리지 풀(Windows Server 2012 이상에서만 사용 가능)을 사용하여 탑재된 여러 디스크를 하나의 큰 논리적 디바이스로 만드는 것이 좋습니다. 이 방법을 사용하면 디스크 공간 관리를 위한 관리 오버헤드를 간소화하고 탑재된 여러 디스크에 수동으로 파일을 배포하는 수고를 덜 수 있습니다.


#### <a name="write-accelerator"></a>Write Accelerator
Azure M 시리즈 VM의 경우 Azure Write Accelerator를 사용하면 Azure Premium Storage와 비교할 때 온라인 다시 실행 로그에 대기 시간 쓰기를 요소별로 줄일 수 있습니다. 온라인 다시 실행 로그 파일에 사용되는 Azure Premium Storage 기반의 디스크(VHD)에 대해 Azure Write Accelerator를 사용하도록 설정합니다. 자세한 내용은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)를 참조하세요.


### <a name="backuprestore"></a>백업/복원
백업/복원 기능의 경우 SAP BR*Tools for Oracle은 표준 Windows Server 운영 체제와 동일한 방식으로 지원됩니다. Oracle RMAN(Recovery Manager)에서도 디스크에 백업 및 디스크에서 복원이 지원됩니다.

또한 Azure Backup을 사용하여 애플리케이션 일치 VM 백업을 실행할 수 있습니다. [Azure에서 VM 백업 인프라 계획](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) 문서에서는 Azure Backup이 애플리케이션 일치 백업을 실행하기 위해 Windows VSS 기능을 사용하는 방식을 설명합니다. SAP이 Azure에서 지원하는 Oracle DBMS 릴리스는 백업에 VSS 기능을 활용할 수 있습니다. 자세한 내용은 Oracle 설명서, [VSS를 사용한 데이터베이스 백업 및 복구에 대한 기본 개념](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701)을 참조하세요.


### <a name="high-availability"></a>고가용성
높은 가용성 및 재해 복구를 위해 Oracle Data Guard가 지원됩니다. Data Guard에서 자동 장애 조치(failover)를 완수하려면 FSFA(Fast-Start Failover)를 사용해야 합니다. 관찰자(FSFA)가 장애 조치(failover)를 트리거합니다. FSFA를 사용하지 않는 경우 수동 장애 조치(failover) 구성만 사용할 수 있습니다.

Azure의 Oracle 데이터베이스 재해 복구에 대한 자세한 내용은 [Azure 환경의 Oracle Database 12c 데이터베이스 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery)를 참조하세요.

### <a name="accelerated-networking"></a>가속된 네트워킹
Windows의 Oracle 배포에서는 [Azure 가속 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)에 설명된 대로 가속 네트워킹을 사용하는 것이 좋습니다. 또한 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)도 고려하세요. 
### <a name="other"></a>기타
[SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서는 Azure 가용성 집합 및 SAP 모니터링을 비롯하여 Oracle Database를 사용하는 VM 배포와 관련된 기타 중요 개념을 설명합니다.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Oracle Linux의 Oracle Database에 대한 고유 정보
Oracle 소프트웨어는 Oracle Linux를 게스트 OS로 사용하여 Microsoft Azure에서 실행되도록 Oracle에서 지원합니다. Windows Hyper-V 및 Azure에 대한 일반 지원에 대한 자세한 내용은 [Azure 및 Oracle FAQ](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)를 참조하세요. 

Oracle Database를 활용하는 SAP 애플리케이션의 특정 시나리오도 지원됩니다. 자세한 내용은 문서의 다음 부분에서 설명합니다.

### <a name="oracle-version-support"></a>Oracle 버전 지원
Azure Virtual Machines에서 Oracle의 SAP 실행을 위해 지원되는 Oracle 버전 및 해당 OS 버전에 대한 자세한 내용은 SAP Note [2039619]에서 찾을 수 있습니다.

Oracle에서 SAP Business Suite를 실행하는 방법에 대한 일반적인 내용은 [Oracle의 SAP 커뮤니티 페이지](https://www.sap.com/community/topic/oracle.html)에서 찾을 수 있습니다.

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Linux의 Azure VM에서 SAP 설치에 대한 Oracle 구성 지침

SAP 설치 설명서에 따라 Oracle 관련 파일을 VM의 부팅 디스크용 시스템 드라이버에 설치하거나 배치해서는 안 됩니다. 다양한 크기의 가상 머신이 연결된 디스크를 다양하게 지원할 수 있습니다. 가상 머신 유형이 적으면 지원할 수 있는 연결된 디스크 수도 적습니다. 

이 경우에는 Oracle 홈, 단계, saptrace, saparch, sapbackup, sapcheck 또는 sapreorg를 부팅 디스크에 설치/배치하는 것이 좋습니다. Oracle DBMS 구성 요소의 이러한 부분은 I/O 및 I/O 처리량에 무리는 아닙니다. 따라서 OS 디스크는 I/O 요구 사항을 처리할 수 있습니다. OS 디스크의 기본 크기는 30GB입니다. Azure Portal, PowerShell 또는 CLI를 통해 부팅 디스크를 확장할 수 있습니다. 부팅 디스크를 확장한 후 Oracle 이진 파일에 대한 추가 파티션을 추가할 수 있습니다.


### <a name="storage-configuration"></a>Storage 구성

ext4, xfs 또는 Oracle ASM의 파일 시스템은 Azure에서 Oracle Database 파일에 대해 지원됩니다. 모든 데이터베이스 파일은 VHD 또는 Managed Disks 기반의 파일 시스템에 저장되어야 합니다. 이러한 디스크는 Azure VM에 탑재되며, [Azure 페이지 Blob 스토리지](<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) 또는 [Azure Managed Disks](../../windows/managed-disks-overview.md)를 기준으로 합니다.

Oracle Linux UEK 커널의 경우 [Azure 프리미엄 SSD](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-caching)를 지원하려면 최소 UEK 버전 4가 필요합니다.

[Azure Managed Disks](../../windows/managed-disks-overview.md)를 사용하는 것이 좋습니다. 또한 Oracle Database 배포에는 [Azure 프리미엄 SSD](../../windows/disks-types.md)를 사용하는 것이 좋습니다.

네트워크 드라이브 또는 Azure 파일 서비스와 같은 원격 공유는 Oracle Database 파일에 대해 지원되지 않습니다. 자세한 내용은  

- [Microsoft Azure 파일 서비스 소개](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)

- [Microsoft Azure 파일에 대한 연결 유지](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

Azure 페이지 Blob 스토리지 또는 Managed Disks를 기준으로 하는 디스크를 사용하는 경우 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에 제공된 설명은 Oracle Database를 사용한 배포에도 적용됩니다.

 Azure 디스크에 대한 IOPS 처리량의 할당량이 존재합니다. 이 개념은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 설명하고 있습니다. 정확한 할당량은 사용되는 VM 유형에 따라 다릅니다. VM 유형과 해당 할당량 목록은 [Azure의 Linux Virtual Machines 크기][virtual-machines-sizes-linux]를 참조하세요.

지원되는 Azure VM 유형을 식별하려면 SAP Note [1928533]을 참조하세요.

최소 구성

| 구성 요소 | 디스크 | 구성 | 제거* |
| --- | ---| --- | --- |
| /oracle/\<SID > / origlogaA & mirrlogB | Premium | 없음 | 필요하지 않음 |
| /oracle/\<SID > / origlogaB & mirrlogA | Premium | 없음 | 필요하지 않음 |
| /oracle/\<SID>/sapdata1...n | Premium | 읽기 전용 | 사용할 수 있음 |
| /oracle/\<SID > / oraarch | Standard | 없음 | 필요하지 않음 |
| Oracle 홈, saptrace, ... | OS 디스크 | | 필요하지 않음 |

*제거: RAID0를 사용한 LVM 스트라이프 또는 MDADM

Oracle의 온라인 다시 실행 로그를 호스팅하기 위한 디스크 선택은 IOPS 요구 사항에 따라야 합니다. 볼륨, IOPS 및 처리량이 요구 사항을 충족하는 경우 모든 sapdata1...n(테이블스페이스)을 단일 탑재 디스크에 저장할 수 있습니다. 

성능 구성

| 구성 요소 | 디스크 | 구성 | 제거* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Premium | 없음 | 사용할 수 있음  |
| /oracle/\<SID>/origlogaB | Premium | 없음 | 사용할 수 있음 |
| /oracle/\<SID>/mirrlogAB | Premium | 없음 | 사용할 수 있음 |
| /oracle/\<SID>/mirrlogBA | Premium | 없음 | 사용할 수 있음 |
| /oracle/\<SID>/sapdata1...n | Premium | 읽기 전용 | 권장  |
| /oracle/\<SID > / sapdata(n+1) * | Premium | 없음 | 사용할 수 있음 |
| /oracle/\<SID>/oraarch* | Premium | 없음 | 필요하지 않음 |
| Oracle 홈, saptrace, ... | OS 디스크 | 필요하지 않음 |

*제거: RAID0를 사용한 LVM 스트라이프 또는 MDADM

*(n+1): SYSTEM, TEMP 및 UNDO 테이블스페이스를 호스트합니다. I/O 패턴의 시스템 및 Undo 테이블스페이스는 애플리케이션 데이터를 호스팅하는 다른 테이블스페이스와 다릅니다. 캐싱 없음이 시스템의 성능 및 Undo 테이블스페이스에 최적의 옵션입니다.

* oraarch: 성능 관점에서 스토리지 풀이 필요하지 않습니다.


더 많은 IOPS가 필요한 경우 LVM(논리 볼륨 관리자) 또는 MDADM을 사용하여 탑재된 여러 디스크에 대해 하나의 큰 논리 볼륨을 만드는 것이 좋습니다. 자세한 내용은 LVM 또는 MDADM을 활용하는 방법에 대한 지침과 조언을 제공하는 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)을 참조하세요. 이 방법을 사용하면 디스크 공간 관리를 위한 관리 오버헤드를 간소화하고 탑재된 여러 디스크에 파일을 수동으로 배포하는 수고를 덜 수 있습니다.


#### <a name="write-accelerator"></a>Write Accelerator
Azure M 시리즈 VM의 경우, Azure Write Accelerator를 사용하면 Azure Premium Storage 성능에 비해 온라인 다시 실행 로그에 대기 시간 쓰기를 요소별로 줄일 수 있습니다. 온라인 다시 실행 로그 파일에 사용되는 Azure Premium Storage 기반의 디스크(VHD)에 대해 Azure Write Accelerator를 사용하도록 설정합니다. 자세한 내용은 [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)를 참조하세요.


### <a name="backuprestore"></a>백업/복원
백업/복원 기능의 경우 SAP BR*Tools for Oracle은 완전 복구 및 Hyper-V와 동일한 방법으로 지원됩니다. Oracle RMAN(Recovery Manager)에서도 디스크에 백업 및 디스크에서 복원이 지원됩니다.

또한 Azure 백업 및 복구 서비스를 사용하여 Oracle 데이터베이스를 백업하고 복구하는 방법에 대한 자세한 내용은 [Azure Linux 가상 머신의 Oracle Database 12c 데이터베이스 백업 및 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-backup-recovery)를 참조하세요.

### <a name="high-availability"></a>고가용성
높은 가용성 및 재해 복구를 위해 Oracle Data Guard가 지원됩니다. Data Guard에서 자동 장애 조치(failover)를 완수하려면 FSFA(Fast-Start Failover)를 사용해야 합니다. 관찰자 기능(FSFA)이 장애 조치(failover)를 트리거합니다. FSFA를 사용하지 않는 경우 수동 장애 조치(failover) 구성만 사용할 수 있습니다. 자세한 내용은 [Azure Linux 가상 머신에서 Oracle Data Guard 구현](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)을 참조하세요.


Azure의 Oracle 데이터베이스에 대한 재해 복구 측면은 [Azure 환경의 Oracle Database 12c 데이터베이스 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-disaster-recovery) 문서에 나와 있습니다.

### <a name="accelerated-networking"></a>가속된 네트워킹
Oracle Linux에서 Azure 가속 네트워킹에 대한 지원은 Oracle Linux 7 업데이트 5(Oracle Linux 7.5)와 함께 제공됩니다. 최신 Oracle Linux 7.5 릴리스로 업그레이드할 수 없는 경우 Oracle UEK 커널 대신 RHCK(RedHat Compatible Kernel)를 사용하여 해결할 수 있습니다. 

Oracle Linux 내에서 RHEL 커널을 사용하는 것은 SAP Note [#1565179](https://launchpad.support.sap.com/#/notes/1565179)에 따라 지원됩니다. Azure 가속 네트워킹의 경우 최소 RHCKL 커널 릴리스는 3.10.0-862.13.1.el7이어야 합니다. [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)과 함께 Oracle Linux의 UEK 커널을 사용하는 경우 Oracle UEK 커널 버전 5를 사용해야 합니다.

Azure Marketplace를 기준으로 하지 않는 이미지에서 VM을 배포하는 경우 다음 코드를 실행하여 VM에 추가 구성 파일을 복사해야 합니다. 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


### <a name="other"></a>기타
[SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서는 Azure 가용성 집합 및 SAP 모니터링을 비롯하여 Oracle Database를 사용하는 VM 배포와 관련된 기타 중요 개념을 설명합니다.

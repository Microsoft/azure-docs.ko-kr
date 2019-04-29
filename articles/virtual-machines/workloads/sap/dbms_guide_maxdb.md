---
title: Azure VM에서 SAP MaxDB, liveCache 및 Content Server 배포 | Microsoft Docs
description: Azure에서 SAP MaxDB, liveCache 및 Content Server 배포
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83319118c778d89749b1eb5d5fd792a5200c19c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836068"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Azure VM에서 SAP MaxDB, liveCache 및 Content Server 배포

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




이 문서에서는 Azure IaaS에서 MaxDB, liveCache 및 Content Server를 배포할 때 고려해야 할 몇 가지 다른 영역에 대해 설명합니다. 이 문서의 전제 조건으로, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)의 다른 가이드를 참조해야 합니다. 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>Windows에서의 SAP MaxDB 배포에 대한 세부 정보
### <a name="sap-maxdb-version-support-on-azure"></a>Azure에서 SAP MaxDB 버전 지원
SAP는 현재 Azure에서 SAP NetWeaver 기반 제품에 사용할 SAP MaxDB 버전 7.9 이상을 지원합니다. SAP NetWeaver 기반 제품과 함께 사용할 SAP MaxDB 서버 또는 JDBC 및 ODBC 드라이버에 대한 모든 업데이트는 <https://support.sap.com/swdc>에 있는 SAP Service Marketplace를 통해서만 제공됩니다.
SAP MaxDB에서 SAP NetWeaver를 실행하는 방법에 대한 일반적인 내용은 <https://www.sap.com/community/topic/maxdb.html>에서 찾을 수 있습니다.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>SAP MaxDB DBMS에 대해 지원되는 Microsoft Windows 버전 및 Azure VM 유형
Azure에서 지원되는 SAP MaxDB DBMS용 Microsoft Windows 버전을 찾으려면 다음을 참조하세요.

* [SAP PAM(제품 가용성 매트릭스)][sap-pam]
* SAP Note [1928533]

최신 버전의 Microsoft Windows 운영 체제인 Microsoft Windows 2016을 사용하는 것이 좋습니다.

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>사용 가능한 MaxDB용 SAP MaxDB 설명서
SAP Note [767598]

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM의 SAP 설치에 대한 SAP MaxDB 구성 지침
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>저장소 구성
SAP MaxDB에 대한 Azure 스토리지 모범 사례는 [RDBMS 배포를 위한 VM의 스토리지 구조](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#65fa79d6-a85f-47ee-890b-22e794f51a64) 챕터에서 설명한 일반 추천을 따릅니다.

> [!IMPORTANT]
> 다른 데이터베이스처럼 SAP MaxDB에도 데이터 및 로그 파일이 있습니다. 그러나 SAP MaxDB 용어에서 올바른 용어는 "볼륨"("파일" 아님)입니다. 예를 들어 SAP MaxDB 데이터 볼륨 및 로그 볼륨이 있습니다. 운영 체제 디스크 볼륨과 혼동하지 마세요. 
> 
> 

한마디로 다음을 수행해야 합니다.

* Azure Storage 계정을 사용하는 경우 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 지정한 대로 SAP MaxDB 데이터 및 로그 볼륨(데이터 및 로그 파일)을 유지하는 Azure Storage 계정을 **LRS(로컬 중복 저장소)** 로 설정합니다.
* 로그 볼륨(로그 파일)에 대한 IO 경로에서 SAP MaxDB 데이터 볼륨(데이터 파일)에 대한 IO 경로를 분리합니다. 즉, 하나의 논리 드라이브에 SAP MaxDB 데이터 볼륨(데이터 파일)을 설치하고, 다른 논리 드라이브에 SAP MaxDB 로그 볼륨(로그 파일)을 설치해야 합니다.
* [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 설명한 대로, SAP MaxDB 데이터 또는 로그 볼륨(데이터 및 로그 파일)을 사용하는지와 Azure Standard 또는 Azure Premium Storage를 사용하는지 여부에 따라 각 디스크에 적절한 캐싱 유형을 설정합니다.
* 디스크당 현재 IOPS 할당량이 요구 사항을 충족하는 경우 탑재된 단일 디스크의 모든 데이터 볼륨을 저장하고 다른 탑재된 단일 디스크에 모든 데이터베이스 로그 볼륨을 저장할 수 있습니다.
* 더 많은 IOPS 및/또는 공간이 필요한 경우 Microsoft Window 저장소 풀(Microsoft Windows Server 2012 이상에서만 사용 가능)을 사용하여 탑재된 여러 디스크에 대해 하나의 큰 논리적 디바이스를 만드는 것이 좋습니다. 자세한 내용은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)도 참조하세요. 이 방법을 사용하면 디스크 공간 관리를 위한 관리 오버헤드를 간소화하고 탑재된 여러 디스크에 파일을 수동으로 배포하는 수고를 덜 수 있습니다.
* MaxDB 배포에 Azure Premium Storage를 사용하는 것이 좋습니다. 

![SAP MaxDB DBMS에 대한 Azure IaaS VM의 참조 구성](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Backup 및 복원
Azure에 SAP MaxDB를 배포하는 경우 백업 방법을 검토해야 합니다. 생산성이 높은 시스템이 아니더라도 SAP MaxDB에서 호스트하는 SAP 데이터베이스를 정기적으로 백업해야 합니다. Azure Storage에는 세 개의 이미지가 유지되므로 스토리지 오류 및 더 중요한 작동 또는 관리 오류에 대한 시스템 보호 면에서 백업의 중요성이 줄어들었습니다. 적절한 백업 및 복원 계획 유지 관리가 중요한 이유는 지정 시간 복구 기능을 제공하여 논리 또는 수동 오류를 보완할 수 있기 때문입니다. 따라서 목표는 백업을 사용하여 데이터베이스를 특정 시점으로 복원하거나 기존 데이터베이스를 복사하여 Azure의 백업을 다른 시스템에 시딩하는 데 Azure의 백업을 사용하는 것입니다. 

Azure에서의 데이터베이스 백업 및 복원 방법은 온-프레미스 시스템에서와 동일합니다. 그러므로 SAP Note [767598]에 나열된 SAP MaxDB 설명서 중 하나에서 설명한 표준 SAP MaxDB 백업/복원 도구를 사용할 수 있습니다. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Backup 및 복원에 대한 성능 고려 사항
완전 배포에서와 같이 백업 및 복원 성능은 병렬로 읽을 수 있는 볼륨 수와 이러한 볼륨의 처리량에 따라 다릅니다. 따라서 다음을 가정할 수 있습니다.

* 데이터베이스 디바이스를 저장하는 데 사용하는 디스크 수가 적을수록 전반적인 읽기 처리량이 줄어듭니다.
* 백업을 작성하는 대상(스트라이프 디렉터리, 디스크) 수가 적을수록 처리량이 줄어듭니다.

작성할 대상 수를 늘리려면 두 가지 옵션을 사용할 수 있으며 필요에 따라 혼합할 수 있습니다.

* 백업에 대해 별도의 볼륨을 지정합니다.
* 해당 스트라이프 디스크 볼륨에 대한 IOPS 처리량을 개선하려면 탑재된 여러 디스크에 백업 대상 볼륨을 스트라이프합니다.
* 다음을 위한 별도의 전용 논리적 디스크 디바이스가 필요합니다.
  * SAP MaxDB 백업 볼륨(즉, 파일)
  * SAP MaxDB 데이터 볼륨(즉, 파일)
  * SAP MaxDB 로그 볼륨(즉, 파일)

탑재된 여러 개의 디스크에 볼륨을 스트라이핑하는 방법은 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)의 앞부분에서 설명했습니다. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>기타 고려 사항
Azure 가용성 집합 또는 SAP 모니터링과 같은 다른 모든 일반 영역도 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 설명한 대로 적용됩니다.  SAP MaxDB 데이터베이스가 있는 VM 배포도 마찬가지입니다.
다른 SAP MaxDB 관련 설정은 Azure VM에 투명하며 SAP Note [767598]에 나와 있는 다른 문서 및 다음 SAP Note에서 설명하고 있습니다.

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>Windows에서의 SAP liveCache 배포에 대한 세부 정보
### <a name="sap-livecache-version-support"></a>SAP liveCache 버전 지원
Azure Virtual Machines에서 지원되는 SAP liveCache의 최소 버전은 **EhP 2 for SAP SCM 7.0** 이상 릴리스용으로 출시된 **liveCache 7.9.08.31** 및 **LCA-Build 25**가 포함된 **SAP LC/LCAPPS 10.0 SP 25**입니다.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>SAP liveCache DBMS에 대해 지원되는 Microsoft Windows 버전 및 Azure VM 유형
Azure에서 지원되는 SAP liveCache용 Microsoft Windows 버전을 찾으려면 다음을 참조하세요.

* [SAP PAM(제품 가용성 매트릭스)][sap-pam]
* SAP Note [1928533]

최신 버전의 운영 체제인 Microsoft Windows Server를 사용하는 것이 좋습니다. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM의 SAP 설치에 대한 SAP liveCache 구성 지침
#### <a name="recommended-azure-vm-types-for-livecache"></a>liveCache에 권장되는 Azure VM 유형
SAP liveCache는 많은 계산을 수행하는 애플리케이션이므로 RAM과 CPU의 양 및 속도가 SAP liveCache 성능에 큰 영향을 줍니다. 

SAP에서 지원하는 Azure VM 형식(SAP Note [1928533])의 경우, VM에 할당된 모든 가상 CPU 리소스가 하이퍼바이저의 전용 물리적 CPU 리소스에 의해 지원됩니다. 오버프로비저닝이 발생되지 않습니다(따라서 CPU 리소스에 대한 경쟁 없음).

마찬가지로 SAP에서 지원하는 모든 Azure VM 인스턴스 유형의 경우 VM 메모리는 실제 메모리에 100% 매핑됩니다. 예를 들어 과도 프로비전(오버 커밋)이 사용되지 않습니다.

이러한 관점에서 가장 최근의 Dv2, Dv3, Ev3 및 M 시리즈 VM을 사용하는 것이 좋습니다. 다른 VM 유형의 선택은 liveCache에 필요한 메모리와 필요한 CPU 리소스에 따라 다릅니다. 다른 모든 DBMS 배포와 마찬가지로 성능이 중요한 볼륨에 Azure Premium Storage를 활용하는 것이 좋습니다.

#### <a name="storage-configuration-for-livecache-in-azure"></a>Azure에서 liveCache용 저장소 구성
SAP liveCache는 SAP MaxDB 기술을 기반으로 하므로 이 문서에서 설명된 SAP MaxDB에 대해 언급한 모든 Azure 저장소 모범 사례 권장 사항이 SAP liveCache에도 적용됩니다. 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>liveCache 시나리오 전용 Azure VM
SAP liveCache는 컴퓨팅 기능을 집중적으로 사용하므로 생산적인 사용을 위해 전용 Azure Virtual Machine에 배포하는 것이 좋습니다. 

![생산적인 사용 사례를 위한 liveCache용 전용 Azure VM](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>Azure에서 liveCache 백업 및 복원
성능 고려 사항을 포함한 백업 및 복원은 이미 이 문서의 SAP MaxDB 관련 챕터에서 설명하고 있습니다. 

#### <a name="other-considerations"></a>기타 고려 사항
다른 모든 일반 영역은 이미 SAP MaxDB 관련 챕터에서 설명하고 있습니다. 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>Azure의 Windows에서의 SAP Content Server 배포에 세부 정보
SAP Content Server는 다양한 형식의 전자 문서와 같은 콘텐츠를 저장하기 위한 별도의 서버 기반 구성 요소입니다. SAP Content Server는 기술 개발을 통해 제공되며 SAP 애플리케이션의 애플리케이션 간에 사용됩니다. 별도의 시스템에 설치됩니다. 일반적인 콘텐츠는 기술 웨어하우스 설명서 또는 mySAP PLM 문서 관리 시스템의 기술 드로잉 및 교육 자료입니다. 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Azure VM용 SAP Content Server 버전 지원
SAP에서 현재 다음을 지원합니다.

* **SAP Content Server** 버전 **6.50 이상**
* **SAP MaxDB 버전 7.9**
* **Microsoft IIS(인터넷 정보 서비스) 버전 8.0 이상**

최신 버전의 SAP Content Server 및 **Microsoft IIS**를 사용하는 것이 좋습니다. 

지원되는 최신 버전의 SAP Content Server 및 Microsoft IIS는 [SAP PAM(제품 가용성 매트릭스)][sap-pam]을 참조하세요.

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>SAP Content Server에 대해 지원되는 Microsoft Windows 버전 및 Azure VM 유형
Azure의 SAP Content Server에서 지원되는 Windows 버전을 확인하려면 다음을 참조하세요.

* [SAP PAM(제품 가용성 매트릭스)][sap-pam]
* SAP Note [1928533]

최신 버전의 Microsoft Windows Server를 사용하는 것이 좋습니다.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Azure VM의 SAP 설치에 대한 SAP Content Server 구성 지침
#### <a name="storage-configuration-for-content-server-in-azure"></a>Azure의 콘텐츠 서버에 대 한 저장소 구성
SAP MaxDB 데이터베이스에 파일을 저장하도록 SAP Content Server를 구성하는 경우 이 문서에서 SAP MaxDB에 대해 언급한 모든 Azure Storage 모범 사례 권장 사항이 SAP Content Server 시나리오에도 적용됩니다. 

파일 시스템에 파일을 저장하도록 SAP Content Server를 구성한 경우 전용 논리 드라이브를 사용하는 것이 좋습니다. Windows 저장소 공간을 사용하면 [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](dbms_guide_general.md)에서 설명한 대로 논리 디스크 크기 및 IOPS 처리량을 늘릴 수 있습니다. 

#### <a name="sap-content-server-location"></a>SAP Content Server 위치
SAP Content Server는 SAP 시스템이 배포된 것과 동일한 Azure VNET 및 Azure 지역에 배포되어야 합니다. SAP Content Server 구성 요소는 전용 Azure VM 또는 SAP 시스템이 실행 중인 동일한 VM에 배포할 수 있습니다. 

![SAP Content Server용 전용 Azure VM](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP Cache Server 위치
SAP Cache Server는 로컬에 있는 (캐시된) 문서에 대한 액세스를 제공하는 추가 서버 기반 구성 요소입니다. SAP Cache Server는 SAP Content Server 문서를 캐시합니다. 이 기능의 목적은 서로 다른 위치에서 문서를 두 번 이상 검색해야 하는 경우 네트워크 트래픽을 최적화하는 것입니다. 일반적으로 SAP Cache Server는 SAP Cache Server에 액세스하는 클라이언트와 물리적으로 가까워야 합니다. 

다음 두 가지 옵션이 있습니다.

1. **클라이언트가 백 엔드 SAP 시스템인 경우** SAP Content Server에 액세스하도록 백 엔드 SAP 시스템이 구성된 경우 해당 SAP 시스템은 클라이언트입니다. SAP 시스템과 SAP Content Server는 모두 같은 Azure 지역, 즉 동일한 Azure 데이터 센터에 배포되므로 물리적으로 서로 가깝습니다. 따라서 전용 SAP Cache Server를 사용할 필요가 없습니다. SAP UI 클라이언트(SAP GUI 또는 웹 브라우저)는 SAP 시스템에 직접 액세스하고 SAP 시스템은 SAP Content Server에서 문서를 검색합니다.
2. **클라이언트가 온-프레미스 웹 브라우저인 경우** 웹 브라우저에서 직접 액세스하도록 SAP Content Server를 구성할 수 있습니다. 이 경우 온-프레미스에서 실행하는 웹 브라우저는 SAP Content Server의 클라이언트입니다. 온-프레미스 데이터 센터와 Azure 데이터 센터는 서로 다른 물리적 위치에 있습니다(이상적으로는 서로 가까워야 함). 온-프레미스 데이터 센터는 Azure 사이트 간 VPN 또는 ExpressRoute를 통해 Azure에 연결됩니다. 두 옵션 모두 Azure에 대한 보안 VPN 네트워크 연결을 제공하지만 사이트 간 네트워크 연결은 온-프레미스 데이터 센터와 Azure 데이터 센터 간의 네트워크 대역폭 및 대기 시간 SLA를 제공하지 않습니다. 문서에 대한 액세스 속도를 향상시키기 위해 다음 중 하나를 수행할 수 있습니다.
   1. 온-프레미스 웹 브라우저 근처에 온-프레미스 SAP Cache Server를 설치합니다(아래 그림의 옵션).
   2. 빠른 속도와 짧은 대기 시간의 온-프레미스 데이터 센터와 Azure 데이터 센터 간 전용 네트워크 연결을 제공하는 Azure ExpressRoute를 구성합니다.

![SAP Cache Server 온-프레미스 설치 옵션](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup/복원
SAP MaxDB 데이터베이스에 파일을 저장하도록 SAP Content Server를 구성하는 경우 백업/복원 절차 및 성능 고려 사항은 이미 이 문서의 SAP MaxDB 관련 챕터에서 설명하고 있습니다. 

파일 시스템에 파일을 저장하도록 SAP Content Server를 구성하는 경우 문서가 위치한 전체 파일 구조의 수동 백업/복원을 실행할 수 있습니다. SAP MaxDB 백업/복원과 마찬가지로, 백업 목적을 위한 전용 디스크 볼륨을 사용하는 것이 좋습니다. 

#### <a name="other"></a>기타
다른 SAP Content Server 관련 설정은 Azure VM에 투명하며 다양한 문서 및 SAP Note에 설명되어 있습니다.

* <https://service.sap.com/contentserver> 
* SAP Note [1619726]  

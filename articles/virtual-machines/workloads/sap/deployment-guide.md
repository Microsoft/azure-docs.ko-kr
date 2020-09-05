---
title: SAP NetWeaver에 대한 Azure Virtual Machines 배포 | Microsoft Docs
description: Azure의 Linux 가상 머신에 SAP 소프트웨어를 배포하는 방법을 알아봅니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 6b66db639e0b22a7e0c45486465a1fa395aba33d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653514"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>SAP NetWeaver에 대한 Azure Virtual Machines 배포

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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (SAP용 Azure Virtual Machines DBMS 배포)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM 및 VHD에 대한 캐싱)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (소프트웨어 RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS 배포 구조)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM을 사용한 고가용성 및 재해 복구)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 이상)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 및 이전 버전)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Azure Marketplace의 SQL Server 이미지 사용)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Azure의 SAP용 SQL Server에 대한 일반적 요약)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS 관련 내용)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (스토리지 구성)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (백업 및 복원)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (백업 및 복원에 대한 성능 고려 사항)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (기타)
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

[deployment-guide]:deployment-guide.md (SAP용 Azure Virtual Machines 배포)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP 리소스)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (사용자 지정 이미지를 사용하여 VM 배포)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (시나리오 1: SAP용 Azure Marketplace에서 VM 배포)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스에서 VM 이동)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure에서의 SAP용 VM 배포 시나리오)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell cmdlet 배포)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP 관련 PowerShell cmdlet 다운로드 및 가져오기)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (온-프레미스 도메인에 VM 가입 - Windows에만 해당)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Azure VM 에이전트 다운로드, 설치 및 사용)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (SAP용 Azure 확장 구성)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Azure PowerShell를 사용 하 여 SAP 용 새 Azure 확장 구성)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Azure CLI를 사용 하 여 SAP 용 새 Azure 확장 구성)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (SAP용 Azure 확장 관련 준비 검사)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (SAP 용 새 Azure 확장에 대 한 준비 검사)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (SAP용 Azure 확장 구성 관련 상태 검사)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (SAP 용 새 Azure 확장 구성에 대 한 상태 검사)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (SAP용 Azure 확장 문제 해결)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (SAP 용 새 Azure 확장 문제 해결)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (SAP 용 Azure 확장 문제 해결-지원 문의)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (SAP 용 Azure 확장 문제 해결-설치 스크립트를 실행 합니다.)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (SAP 용 Azure 확장 문제 해결-sysprep 후 다시 배포)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( SAP 용 Azure 확장 문제 해결-인터넷 연결 수정)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (VM 확장 구성)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (프록시 구성)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (확인 및 문제 해결)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (SAP용 Azure Virtual Machines 계획 및 구현)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (리소스)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure Virtual Machines에서 실행되는 SAP NetWeaver의 고가용성 및 재해 복구)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP 애플리케이션 서버의 고가용성)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (SAP 인스턴스에 대해 자동 시작 사용)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (클라우드 전용 - 온-프레미스 고객 네트워크에 의존하지 않고 Azure에 가상 컴퓨터 배포)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (크로스 프레미스 - 온-프레미스 네트워크에 완전히 통합된 Azure에서 단일 또는 다중 SAP VM 배포)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure 지역)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (장애 도메인)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (업그레이드 도메인)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure 가용성 집합)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure 가상 머신 개념)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (고객별 이미지를 사용하여 VM 배포)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (일반화되지 않은 디스크를 사용하여 온-프레미스에서 Azure로 VM 이동 준비)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (SAP용 고객별 이미지를 사용하여 VM 배포 준비)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Azure용 SAP로 VM 준비)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure 디스크와 Azure 이미지 간의 차이)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (온-프레미스에서 Azure로 VHD 업로드)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure Storage 계정 간 디스크 복사)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP 배포를 위한 VM/VHD 구조)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (연결된 디스크에 대한 자동 탑재 설정)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (SAP용 Azure 모니터링 솔루션)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure 네트워킹)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (스토리지: Microsoft Azure Storage 및 데이터 디스크)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP 제품 가용성 매트릭스)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 머신 배포 및 관리)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Azure Resource Manager 및 PowerShell을 사용하여 가상 머신 관리)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines는 긴 조달 주기 없이 최소한의 시간 안에 컴퓨팅 및 스토리지 리소스를 필요로 하는 조직을 위한 솔루션입니다. Azure Virtual Machines를 사용하여 Azure에서 SAP NetWeaver 기반 애플리케이션 같은 기존 애플리케이션을 배포할 수 있습니다. 추가 온-프레미스 리소스 없이도 애플리케이션의 안정성과 가용성을 확장할 수 있습니다. Azure Virtual Machines는 크로스-프레미스 연결을 지원하므로 Azure Virtual Machines를 조직의 온-프레미스 도메인, 프라이빗 클라우드 및 SAP 시스템 지형에 통합할 수 있습니다.

이 문서에서는 대체 배포 옵션과 문제 해결 등 Azure에서 VM(가상 머신)에 SAP 애플리케이션을 배포하는 단계를 설명합니다. 이 문서는 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide]의 정보를 기반으로 합니다. 또한 SAP 소프트웨어를 설치 및 배포하기 위한 기본 리소스인 SAP 설치 설명서 및 SAP Note를 보완합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

SAP 소프트웨어 배포를 위해 Azure Virtual Machines를 설정하려면 여러 단계와 리소스가 필요합니다. 시작하기 전에 Azure에서 가상 머신에 SAP 소프트웨어를 설치하기 위한 필수 구성 요소를 충족하는지 확인합니다.

### <a name="local-computer"></a>수집

Windows 또는 Linux VM을 관리하려면 PowerShell 스크립트와 Azure Portal을 사용해야 합니다. 두 가지 도구 모두 Windows 7 또는 이후 버전의 Windows 실행하는 로컬 컴퓨터가 필요합니다. Linux VM만 관리하려는 경우 이 작업에 Linux 컴퓨터를 사용하려면 Azure CLI를 사용할 수 있습니다.

### <a name="internet-connection"></a>인터넷 연결

SAP 소프트웨어 배포에 필요한 도구와 스크립트를 다운로드하고 실행하려면 인터넷에 연결해야 합니다. 또한 SAP용 Azure 확장을 실행하는 Azure VM은 인터넷에 액세스해야 합니다. Azure VM이 Azure Virtual Network 또는 온-프레미스 도메인에 포함된 경우 관련 프록시 설정이 [프록시 구성][deployment-guide-configure-proxy]의 설명대로 설정되어 있는지 확인하세요.

### <a name="microsoft-azure-subscription"></a>Microsoft Azure 구독

활성 Azure 계정이 필요합니다.

### <a name="topology-and-networking"></a>토폴로지 및 네트워킹

Azure에서 SAP 배포의 토폴로지 및 아키텍처를 정의해야 합니다.

* 사용할 Azure Storage 계정
* SAP 시스템을 배포할 가상 네트워크
* SAP 시스템을 배포할 리소스 그룹
* SAP 시스템을 배포할 Azure 영역
* SAP 구성(2계층 또는 3계층)
* VM 크기 및 VM에 탑재할 추가 데이터 디스크 수
* SAP 수정과 전송 시스템(CTS) 구성

SAP 소프트웨어 배포 프로세스를 시작하기 전에 Azure Storage 계정(필요한 경우) 또는 Azure 가상 네트워크를 만들고 구성합니다. 이러한 리소스를 만들고 구성하는 방법에 대한 정보는 [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현][planning-guide]을 참조하세요.

### <a name="sap-sizing"></a>SAP 크기 조정

SAP 크기 조정을 위해 다음 정보를 알고 있어야 합니다.

* 예상되는 SAP 워크로드(예: SAP Quick Sizer 도구 사용의 경우) 및 SAPS(SAP Application Performance Standard) 번호
* SAP 시스템의 필요한 CPU 리소스 및 메모리 소비
* 필요한 초당 입력/출력(I/O) 작업 수
* Azure에서 서로 다른 VM 간의 결과적 통신에 필요한 네트워크 대역폭
* 온-프레미스 자산과 Azure가 배포된 SAP 시스템 간에 필요한 네트워크 대역폭

### <a name="resource-groups"></a>리소스 그룹

Azure Resource Manager에서 리소스 그룹을 사용하여 Azure 구독에서 모든 애플리케이션 리소스를 관리할 수 있습니다. 자세한 내용은 [Azure Resource Manager 개요][resource-group-overview]를 참조하세요.

## <a name="resources"></a>리소스

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP 리소스

SAP 소프트웨어 배포를 설정하는 경우 다음과 같은 SAP 리소스가 필요합니다.

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [1409604]는 Azure에서 Windows에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [2002167]는 Red Hat Enterprise Linux 7.x에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [2069760]은 Oracle Linux 7.x에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351] 에는 Sap 용 Azure 확장에 대 한 추가 문제 해결 정보가 있습니다.
* SAP Note [1597355]는 Linux의 스왑 공간에 대한 일반 정보를 포함하고 있습니다.
* [Azure의 SAP SCN 페이지](https://wiki.scn.sap.com/wiki/x/Pia7Gg)에는 뉴스 및 유용한 리소스의 컬렉션을 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Azure PowerShell][azure-ps]에 포함된 SAP 관련 PowerShell cmdlet입니다.
* [Azure CLI][azure-cli]에 포함된 SAP 관련 Azure CLI 명령입니다.

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows 리소스

다음 Microsoft 문서에서는 Azure에서 SAP 배포에 대해 설명합니다.

* [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP NetWeaver용 Azure Virtual Machines 배포(해당 문서)][deployment-guide]
* [SAP NetWeaver용 Azure Virtual Machines DBMS 배포][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure VM에서 SAP 소프트웨어에 대한 배포 시나리오

Azure에서 VM 및 연결된 디스크를 배포하기 위한 여러 옵션이 있습니다. 선택하는 배포 유형에 따라 배포를 위해 VM을 준비하는 단계가 다를 수 있으므로 배포 옵션 간의 차이점을 이해해야 합니다.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>시나리오 1: SAP용 Azure Marketplace에서 VM 배포

Azure Marketplace에서 Microsoft 또는 타사에서 제공하는 이미지를 사용하여 VM을 배포할 수 있습니다. Marketplace는 Windows Server 및 서로 다른 Linux 배포의 일부 표준 OS 이미지를 제공합니다. 또한 데이터베이스 관리 시스템(DMBS) SKU, 예를 들어 Microsoft SQL Server를 포함하고 있는 이미지를 배포할 수도 있습니다. DBMS SKU가 포함된 이미지 사용에 관한 자세한 내용은 [SAP NetWeaver용 Azure Virtual Machines DBMS 배포][dbms-guide]를 참조하세요.

다음 순서도는 Azure Marketplace에서 VM을 배포하기 위한 SAP 관련 단계 순서를 보여 줍니다.

![Azure Marketplace에서 VM 이미지를 사용하여 SAP 시스템용 VM 배포 순서도][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신 만들기

Azure Marketplace에서 이미지를 사용하여 새 가상 머신을 만드는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다.

1.  [https://editor.swagger.io](<https://portal.azure.com/#create/hub>) 로 이동합니다.  또는 Azure Portal 메뉴에서 **+새로 만들기**를 선택합니다.
1.  **Compute**를 선택한 다음, 배포할 운영 체제 유형을 선택합니다. 예를 들어, Windows Server 2012 R2, SUSE Linux Enterprise Server 12(SLES 12), Red Hat Enterprise Linux 7.2(RHEL 7.2) 또는 Oracle Linux 7.2입니다. 기본 목록 보기에는 지원되는 일부 운영 체제가 표시되지 않을 수도 있습니다. 전체 목록을 보려면 **모두 보기**를 선택합니다. SAP 소프트웨어 배포를 위한 지원되는 운영 체제에 대한 자세한 내용은 SAP Note [1928533]을 참조하세요.
1.  다음 페이지에서 약관을 검토합니다.
1.  **배포 모델 선택** 목록에서 **Resource Manager**를 선택합니다.
1.  **만들기**를 선택합니다.

마법사의 필수 매개 변수 설정을 통해 네트워크 인터페이스 및 스토리지 계정과 같은 모든 필요한 리소스와 함께 가상 머신을 만들 수 있습니다. 다음은 일부 매개 변수입니다.

1. **기본 사항**:
   * **Name**: 리소스 이름(가상 머신 이름)입니다.
   * **VM 디스크 유형**: OS 디스크의 디스크 유형을 선택합니다. 데이터 디스크로 Premium Storage를 사용하려는 경우 OS 디스크에도 Premium Storage를 사용하는 것이 좋습니다.
   * **사용자 이름 및 암호** 또는 **SSH 공개 키**: 사용자 이름과 프로비전 중에 만든 사용자 암호를 입력합니다. Linux 가상 컴퓨터의 경우 컴퓨터에 로그인하는 데 사용하는 공용 SSH(Secure Shell) 키를 입력할 수 있습니다.
   * **구독**: 새 가상 머신을 프로비전하는 데 사용할 구독을 선택합니다.
   * **리소스 그룹**: VM의 리소스 그룹 이름입니다. 새 리소스 그룹의 이름 또는 기존 리소스 그룹의 이름을 입력할 수 있습니다.
   * **위치**: 새 가상 머신을 배포할 위치입니다. 온-프레미스 네트워크에 가상 머신을 연결하려는 경우 온-프레미스 네트워크에 Azure를 연결하는 가상 네트워크의 위치를 선택해야 합니다. 자세한 내용은 [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현][planning-guide]의 [Microsoft Azure 네트워킹][planning-guide-microsoft-azure-networking]을 참조하세요.
1. **Size**:

     지원되는 VM 유형 목록은 SAP Note [1928533]을 참조하세요. Azure Premium Storage를 사용하려면 올바른 VM 유형을 선택해야 합니다. 모든 VM 유형이 Premium Storage를 지원하지는 않습니다. 자세한 내용은 Azure에서 sap [NetWeaver 계획 및 구현 Virtual Machines][planning-guide] [저장소: Microsoft Azure Storage 및 데이터 디스크][planning-guide-storage-microsoft-azure-storage-and-data-disks] 및 [azure storage for sap 워크 로드](./planning-guide-storage.md) 를 참조 하세요.

1. **설정**:
   * **스토리지**
     * **디스크 유형**: OS 디스크의 디스크 유형을 선택합니다. 데이터 디스크로 Premium Storage를 사용하려는 경우 OS 디스크에도 Premium Storage를 사용하는 것이 좋습니다.
     * **Managed Disks 사용**: Managed Disks를 사용하려는 경우 예를 선택합니다. Managed Disks에 대한 자세한 내용은 이 계획 가이드의 [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) 챕터를 참조하세요.
     * **스토리지 계정**: 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만듭니다. 모든 스토리지 유형이 SAP 애플리케이션 실행을 위해 작동하지는 않습니다. 스토리지 유형에 대한 자세한 내용은 [RDBMS 배포의 VM 스토리지 구조](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)를 참조하세요.
   * **Network**
     * **가상 네트워크** 및 **서브넷**: 인트라넷에 가상 머신을 통합하려면 온-프레미스 네트워크에 연결된 가상 네트워크를 선택합니다.
     * **공용 IP 주소**: 사용하려는 공용 IP 주소를 선택하거나 매개 변수를 입력하여 새 공용 IP 주소를 만듭니다. 인터넷에서 가상 머신에 액세스하는 공용 IP 주소를 사용할 수 있습니다. 또한 가상 머신에 안전하게 액세스하려면 네트워크 보안 그룹을 만들어야 합니다.
     * **네트워크 보안 그룹**: 자세한 내용은 [네트워크 보안 그룹으로 네트워크 트래픽 흐름 제어][virtual-networks-nsg]를 참조하세요.
   * **확장**: 확장을 배포에 추가하여 가상 머신 확장을 설치할 수 있습니다. 이 단계에서는 확장을 추가할 필요가 없습니다. SAP 지원에 필요한 확장은 나중에 설치됩니다. 이 가이드의 [SAP용 Azure 확장 구성][deployment-guide-4.5] 챕터를 참조하세요.
   * **고가용성**: 가용성 집합을 선택하거나 매개 변수를 입력하여 새 가용성 집합을 만듭니다. 자세한 내용은 [Azure 가용성 집합][planning-guide-3.2.3]을 참조하세요.
   * **Monitoring**
     * **부팅 진단**: 부팅 진단을 **사용 안 함**으로 선택할 수 있습니다.
     * **게스트 OS 진단**: 진단 모니터링에 대해 **사용 안 함**을 선택할 수 있습니다.

1. **요약**:

   선택 내용을 검토한 다음 **확인**을 선택합니다.

선택한 리소스 그룹에서 가상 머신이 배포됩니다.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>템플릿을 사용하여 가상 머신 만들기

[azure-quickstart-templates GitHub 리포지토리][azure-quickstart-templates-github]에 게시된 SAP 템플릿 중 하나를 사용하여 가상 머신을 만들 수 있습니다. 또한 [Azure Portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] 또는 [Azure CLI][virtual-machines-linux-tutorial]를 사용하여 가상 머신을 수동으로 만들 수도 있습니다.

* [**2계층 구성(단일 가상 머신) 템플릿**(sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  한 대의 가상 머신을 사용하여 2계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**2계층 구성(단일 가상 머신) 템플릿 - Managed Disks**(sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  한 대의 가상 머신과 Managed Disks를 사용하여 2계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**3계층 구성(여러 가상 머신) 템플릿**(sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  여러 대의 가상 머신을 사용하여 3계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**3계층 구성(여러 가상 머신) 템플릿 - Managed Disks**(sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  여러 대의 가상 머신과 Managed Disks를 사용하여 3계층 시스템을 만들려면 이 템플릿을 사용합니다.

Azure Portal에서 템플릿에 대한 다음 매개 변수를 입력합니다.

1. **기본 사항**:
   * **구독**: 템플릿을 배포하는 데 사용하는 구독입니다.
   * **리소스 그룹**: 템플릿을 배포하는 데 사용하는 리소스 그룹입니다. 새 리소스 그룹을 만들거나 구독에서 기존 리소스 그룹을 선택할 수 있습니다.
   * **위치**: 템플릿을 배포할 위치입니다. 기존 리소스 그룹을 선택한 경우 해당 리소스 그룹의 위치가 사용됩니다.

1. **설정**:
   * **SAP 시스템 ID**: SID(SAP 시스템 ID)입니다.
   * **OS 유형**: 배포할 운영 체제, 예를 들어 Windows Server 2012 R2, SUSE Linux Enterprise Server 12(SLES 12) 또는 Red Hat Enterprise Linux 7.2(RHEL 7.2) 또는 Oracle Linux 7.2입니다.

     목록 보기에는 지원되는 일부 운영 체제가 표시되지 않을 수도 있습니다. SAP 소프트웨어 배포를 위한 지원되는 운영 체제에 대한 자세한 내용은 SAP Note [1928533]을 참조하세요.
   * **SAP 시스템 크기**: SAP 시스템의 크기입니다.

     새 시스템에서 제공하는 SAP의 수입니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
   * **시스템 가용성**(3계층 템플릿만 해당): 시스템 가용성입니다.

     고가용성 설치에 적합한 구성의 경우 **HA**를 선택합니다. 두 데이터베이스 서버와 ABAP SAP 중앙 서비스(ASCS)에 대한 두 서버가 만들어집니다.
   * **스토리지 유형**(2계층 템플릿만 해당): 사용할 스토리지 유형입니다.

     더 큰 시스템의 경우 Azure Premium Storage를 사용하는 것이 좋습니다. 스토리지 유형에 대한 자세한 내용은 다음 리소스를 참조하세요.
      * [SAP DBMS 인스턴스용 Azure 프리미엄 SSD Storage 사용][2367194]
      * [RDBMS 배포를 위한 VM의 스토리지 구조](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure Virtual Machine 작업을 위한 고성능 스토리지][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage 소개][storage-introduction]
   * **관리자 사용자 이름** 및 **관리자 암호**: 사용자 이름 및 암호입니다.
     가상 머신에 로그인하기 위한 새 사용자가 만들어집니다.
   * **새 서브넷 또는 기존 서브넷**: 새 가상 네트워크 및 서브넷을 만들어야 하는지 또는 기존 서브넷을 사용해야 하는지를 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 **기존**을 선택합니다.
   * **서브넷 ID**: 서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 일반적으로 다음과 같이 표시합니다. /subscriptions/&lt;구독 id>/resourceGroups/&lt;리소스 그룹 이름>/providers/Microsoft.Network/virtualNetworks/&lt;가상 네트워크 이름>/subnets/&lt;서브넷 이름>

1. **사용 약관**:  
    약관을 검토하고 동의합니다.

1. **구매**를 선택합니다.

Azure Marketplace에서 이미지를 사용하는 경우 Azure VM 에이전트가 기본적으로 배포됩니다.

#### <a name="configure-proxy-settings"></a>프록시 설정 구성

온-프레미스 네트워크가 구성된 방법에 따라 VM에 프록시를 설정해야 할 수 있습니다. VM이 VPN 또는 ExpressRoute를 통해 온-프레미스 네트워크에 연결한 경우 VM이 인터넷에 액세스 못 할 수 있으며, 필요한 VM 확장을 다운로드할 수 없거나 Azure용 SAP 확장을 통해 SAP 호스트 에이전트용 Azure 인프라 정보를 수집할 수 없습니다. 자세한 내용은 [프록시 구성][deployment-guide-configure-proxy]을 참조하세요.

#### <a name="join-a-domain-windows-only"></a>도메인 가입(Windows에만 해당)

Azure 배포가 Azure 사이트 간 VPN 연결 또는 ExpressRoute를 통해 온-프레미스 Active Directory나 DNS 인스턴스에 연결된 경우(이 상태를 [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현][planning-guide]에서는 *크로스-프레미스*라 함) VM이 온-프레미스 도메인에 가입할 것으로 예상됩니다. 이 작업 관련 질문에 대한 자세한 내용은 [온-프레미스 도메인에 VM 가입(Windows에만 해당)][deployment-guide-4.3]을 참조하세요.

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>VM 확장 구성

SAP가 사용자 환경을 지원하도록 하려면 [SAP용 Azure 확장 구성][deployment-guide-4.5]의 설명한 대로 SAP용 Azure 확장을 설정합니다. [SAP 리소스][deployment-guide-2.2]에 나열된 리소스에서 SAP 필수 조건 및 SAP 커널과 SAP 호스트 에이전트의 필수 최소 버전을 확인합니다.

#### <a name="vm-extension-for-sap-check"></a>SAP 검사용 VM 확장

[확인 및 문제 해결][deployment-guide-troubleshooting-chapter]에 설명 된 대로 SAP 용 VM 확장이 작동 하는지 확인 합니다.

#### <a name="post-deployment-steps"></a>배포 후 단계

VM을 만들고 VM이 배포된 후 VM에 필수 소프트웨어 구성 요소를 설치 해야 합니다. 이 유형의 VM 배포에서 배포/소프트웨어 설치 순서 때문에 설치할 소프트웨어가 Azure 내에, 다른 VM 상에 또는 연결할 수 있는 디스크로 이미 사용할 수 있어야 합니다. 또는 온-프레미스 자산에 연결되는(설치 공유) 프레미스 간 시나리오 사용을 고려합니다.

Azure에서 VM을 배포한 후 온-프레미스 환경에서와 동일한 지침 및 도구를 사용하여 VM에 SAP 소프트웨어를 설치합니다. Azure VM에 SAP 소프트웨어를 설치할 때는 SAP 설치 미디어를 Azure VHD 또는 Managed Disks에 업로드 및 저장하거나 필요한 SAP 설치 미디어를 모두 포함하고 있는 파일 서버로 사용할 Azure VM을 만드는 것이 좋습니다.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>시나리오 2: SAP용 사용자 지정 이미지를 사용하여 VM 배포

다른 버전의 운영 체제 또는 DBMS는 패치 요구 사항이 다르므로 Azure Marketplace에서 찾을 수 있는 이미지가 요구에 맞지 않을 수 있습니다. 그 대신에 나중에 다시 배포할 수 있는 고유한 OS/DBMS VM 이미지를 사용하여 VM을 만드는 것이 좋습니다.
Linux에 대한 프라이빗 이미지를 만들려면 Windows에 대해 프라이빗 이미지를 만드는 방법과 다른 단계를 사용합니다.

---
> ![Windows][Logo_Windows] Windows
>
> 여러 가상 머신을 배포하는 데 사용할 수 있는 Windows 이미지를 준비하려면 Windows 설정(예: Windows SID 및 호스트 이름)을 온-프레미스 VM에서 추상화 또는 일반화해야 합니다. [sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10))을 사용하여 이 작업을 수행할 수 있습니다.
>
> ![Linux][Logo_Linux] Linux
>
> 여러 가상 머신을 배포하는 데 사용할 수 있는 Linux 이미지를 준비하려면 일부 Linux 설정을 온-프레미스 VM에서 추상화 또는 일반화해야 합니다. `waagent -deprovision`을 사용하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure에서 실행 중인 Linux 가상 머신 캡처][virtual-machines-linux-capture-image] 및 [Azure Linux 에이전트 사용자 가이드][virtual-machines-linux-agent-user-guide-command-line-options]를 참조하세요.
>
>

---
사용자 지정 이미지를 준비하고 만든 다음 해당 이미지를 사용하여 여러 새 VM을 만들 수 있습니다. 이 방법을 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide]에서 설명합니다. SAP Software Provisioning Manager를 사용하여 새 SAP 시스템을 설치하거나(가상 컴퓨터에 연결된 디스크에서 데이터베이스 백업을 복원) DBMS에서 지원하는 경우 Azure Storage에서 데이터베이스 백업을 직접 복원하여 데이터베이스 콘텐츠를 설치합니다. 자세한 내용은 [SAP NetWeaver에 대한 Azure Virtual Machines DBMS 배포][dbms-guide]를 참조하세요. 온-프레미스 VM(특히 2계층 시스템)에 SAP 시스템을 이미 설치한 경우 Azure VM을 배포한 후에 SAP Software Provisioning Manager에서 지원하는 시스템 이름 변경 절차를 사용하여 SAP 시스템 설정을 조정할 수 있습니다(SAP Note [1619720]). 그렇지 않은 경우 Azure VM 배포 후 SAP 소프트웨어를 설치할 수 있습니다.

다음 순서도는 사용자 지정 이미지에서 VM을 배포하기 위한 SAP 관련 단계 순서를 보여 줍니다.

![프라이빗 Marketplace에서 VM 이미지를 사용하여 SAP 시스템용 VM 배포 순서도][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신 만들기

Managed Disk 이미지에서 새 가상 머신을 만드는 가장 쉬운 방법은 Azure Portal을 사용하는 것입니다. Manage Disk 이미지를 만드는 방법에 대한 자세한 내용은 [Azure에서 일반화된 VM의 관리되는 이미지 캡처](../../windows/capture-image-resource.md)를 읽어보세요.

1.  [https://editor.swagger.io](<https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>) 로 이동합니다. 또는 Azure Portal 메뉴에서 **이미지**를 선택합니다.
1.  배포하려는 Managed Disk 이미지를 선택하고 **VM 만들기**를 클릭합니다.

마법사의 필수 매개 변수 설정을 통해 네트워크 인터페이스 및 스토리지 계정과 같은 모든 필요한 리소스와 함께 가상 머신을 만들 수 있습니다. 다음은 일부 매개 변수입니다.

1. **기본 사항**:
   * **Name**: 리소스 이름(가상 머신 이름)입니다.
   * **VM 디스크 유형**: OS 디스크의 디스크 유형을 선택합니다. 데이터 디스크로 Premium Storage를 사용하려는 경우 OS 디스크에도 Premium Storage를 사용하는 것이 좋습니다.
   * **사용자 이름 및 암호** 또는 **SSH 공개 키**: 사용자 이름과 프로비전 중에 만든 사용자 암호를 입력합니다. Linux 가상 컴퓨터의 경우 컴퓨터에 로그인하는 데 사용하는 공용 SSH(Secure Shell) 키를 입력할 수 있습니다.
   * **구독**: 새 가상 머신을 프로비전하는 데 사용할 구독을 선택합니다.
   * **리소스 그룹**: VM의 리소스 그룹 이름입니다. 새 리소스 그룹의 이름 또는 기존 리소스 그룹의 이름을 입력할 수 있습니다.
   * **위치**: 새 가상 머신을 배포할 위치입니다. 온-프레미스 네트워크에 가상 머신을 연결하려는 경우 온-프레미스 네트워크에 Azure를 연결하는 가상 네트워크의 위치를 선택해야 합니다. 자세한 내용은 [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현][planning-guide]의 [Microsoft Azure 네트워킹][planning-guide-microsoft-azure-networking]을 참조하세요.
1. **Size**:

     지원되는 VM 유형 목록은 SAP Note [1928533]을 참조하세요. Azure Premium Storage를 사용하려면 올바른 VM 유형을 선택해야 합니다. 모든 VM 유형이 Premium Storage를 지원하지는 않습니다. 자세한 내용은 Azure에서 sap [NetWeaver 계획 및 구현 Virtual Machines][planning-guide] [저장소: Microsoft Azure Storage 및 데이터 디스크][planning-guide-storage-microsoft-azure-storage-and-data-disks] 및 [azure storage for sap 워크 로드](./planning-guide-storage.md) 를 참조 하세요.

1. **설정**:
   * **스토리지**
     * **디스크 유형**: OS 디스크의 디스크 유형을 선택합니다. 데이터 디스크로 Premium Storage를 사용하려는 경우 OS 디스크에도 Premium Storage를 사용하는 것이 좋습니다.
     * **Managed Disks 사용**: Managed Disks를 사용하려는 경우 예를 선택합니다. Managed Disks에 대한 자세한 내용은 이 계획 가이드의 [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) 챕터를 참조하세요.
   * **Network**
     * **가상 네트워크** 및 **서브넷**: 인트라넷에 가상 머신을 통합하려면 온-프레미스 네트워크에 연결된 가상 네트워크를 선택합니다.
     * **공용 IP 주소**: 사용하려는 공용 IP 주소를 선택하거나 매개 변수를 입력하여 새 공용 IP 주소를 만듭니다. 인터넷에서 가상 머신에 액세스하는 공용 IP 주소를 사용할 수 있습니다. 또한 가상 머신에 안전하게 액세스하려면 네트워크 보안 그룹을 만들어야 합니다.
     * **네트워크 보안 그룹**: 자세한 내용은 [네트워크 보안 그룹으로 네트워크 트래픽 흐름 제어][virtual-networks-nsg]를 참조하세요.
   * **확장**: 확장을 배포에 추가하여 가상 머신 확장을 설치할 수 있습니다. 이 단계에서는 확장을 추가할 필요가 없습니다. SAP 지원에 필요한 확장은 나중에 설치됩니다. 이 가이드의 [SAP용 Azure 확장 구성][deployment-guide-4.5] 챕터를 참조하세요.
   * **고가용성**: 가용성 집합을 선택하거나 매개 변수를 입력하여 새 가용성 집합을 만듭니다. 자세한 내용은 [Azure 가용성 집합][planning-guide-3.2.3]을 참조하세요.
   * **Monitoring**
     * **부팅 진단**: 부팅 진단을 **사용 안 함**으로 선택할 수 있습니다.
     * **게스트 OS 진단**: 진단 모니터링에 대해 **사용 안 함**을 선택할 수 있습니다.

1. **요약**:

   선택 내용을 검토한 다음 **확인**을 선택합니다.

선택한 리소스 그룹에서 가상 머신이 배포됩니다.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>템플릿을 사용하여 가상 머신 만들기

Azure Portal에서 프라이빗 OS 이미지를 사용하여 배포를 만들려면 다음 SAP 템플릿 중 하나를 사용합니다. 이러한 템플릿은 [azure-quickstart-templates GitHub 리포지토리][azure-quickstart-templates-github]에 게시되어 있습니다. 또한 [PowerShell][virtual-machines-upload-image-windows-resource-manager]을 사용하여 가상 머신을 수동으로 만들 수도 있습니다.

* [**2계층 구성(단일 가상 머신) 템플릿**(sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  한 대의 가상 머신을 사용하여 2계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**2계층 구성(단일 가상 머신) 템플릿 - Managed Disk 이미지**(sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  한 대의 가상 컴퓨터와 Managed Disk 이미지를 사용하여 2계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**3계층 구성(다중 가상 머신) 템플릿**(sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  여러 대의 가상 머신 또는 고유한 OS 이미지를 사용하여 3계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**3계층 구성(다중 가상 머신) 템플릿 - Managed Disk 이미지**(sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  여러 대의 가상 머신, 고유한 OS 이미지 및 Managed Disk 이미지를 사용하여 3계층 시스템을 만들려면 이 템플릿을 사용합니다.

Azure Portal에서 템플릿에 대한 다음 매개 변수를 입력합니다.

1. **기본 사항**:
   * **구독**: 템플릿을 배포하는 데 사용하는 구독입니다.
   * **리소스 그룹**: 템플릿을 배포하는 데 사용하는 리소스 그룹입니다. 새 리소스 그룹을 만들거나 구독에서 기존 리소스 그룹을 선택할 수 있습니다.
   * **위치**: 템플릿을 배포할 위치입니다. 기존 리소스 그룹을 선택한 경우 해당 리소스 그룹의 위치가 사용됩니다.
1. **설정**:
   * **SAP 시스템 ID**: SAP 시스템 ID입니다.
   * **OS 유형**: 배포하려는 운영 체제 형식(Windows 또는 Linux)
   * **SAP 시스템 크기**: SAP 시스템의 크기입니다.

     새 시스템에서 제공하는 SAP의 수입니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
   * **시스템 가용성**(3계층 템플릿만 해당): 시스템 가용성입니다.

     고가용성 설치에 적합한 구성의 경우 **HA**를 선택합니다. ASCS용 2개의 데이터베이스 서버 및 2개의 서버가 생성됩니다.
   * **스토리지 유형**(2계층 템플릿만 해당): 사용할 스토리지 유형입니다.

     더 큰 시스템의 경우 Azure Premium Storage를 사용하는 것이 좋습니다. 스토리지 유형에 대한 자세한 내용은 다음 리소스를 참조하세요.
      * [SAP DBMS 인스턴스용 Azure 프리미엄 SSD Storage 사용][2367194]
      * [RDBMS 배포를 위한 VM의 스토리지 구조](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure Virtual Machine 워크로드를 위한 고성능 스토리지][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage 소개][storage-introduction]
   * **사용자 이미지 VHD URI**(관리되지 않는 디스크 이미지 템플릿만 해당): 프라이빗 OS 이미지 VHD의 URI(예: https://&lt;accountname&gt;.blob.core.windows.net/vhds/userimage.vhd)입니다.
   * **사용자 이미지 스토리지 계정**(관리되지 않는 디스크 이미지 템플릿만 해당): 프라이빗 OS 이미지가 저장된 스토리지 계정의 이름(예: https://&lt;accountname&gt;.blob.core.windows.net/vhds/userimage.vhd의 &lt;accountname&gt;)입니다.
   * **userImageId** (관리 되는 디스크 이미지 템플릿만): 사용 하려는 관리 되는 디스크 이미지의 ID
   * **관리자 사용자 이름** 및 **관리자 암호**: 사용자 이름 및 암호입니다.

     가상 머신에 로그인하기 위한 새 사용자가 만들어집니다.
   * **새 서브넷 또는 기존 서브넷**: 새 가상 네트워크 및 서브넷을 만들어야 하는지 또는 기존 서브넷을 사용해야 하는지를 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 **기존**을 선택합니다.
   * **서브넷 ID**: 서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 일반적으로 다음과 같이 표시합니다. /subscriptions/&lt;구독 id>/resourceGroups/&lt;리소스 그룹 이름>/providers/Microsoft.Network/virtualNetworks/&lt;가상 네트워크 이름>/subnets/&lt;서브넷 이름>

1. **사용 약관**:  
    약관을 검토하고 동의합니다.

1. **구매**를 선택합니다.

#### <a name="install-the-vm-agent-linux-only"></a>VM 에이전트 설치(Linux에만 해당)

이전 섹션에서 설명한 템플릿을 사용하려면 Linux 에이전트가 사용자 이미지에 이미 설치되어 있어야 하며, 그렇지 않으면 배포에 실패합니다. [Azure VM 에이전트 다운로드, 설치 및 사용][deployment-guide-4.4]의 설명대로 VM 에이전트를 다운로드하고 사용자 이미지에 설치합니다. 템플릿을 사용하지 않는 경우 나중에 VM 에이전트를 설치할 수도 있습니다.

#### <a name="join-a-domain-windows-only"></a>도메인 가입(Windows에만 해당)

Azure 배포가 Azure 사이트 간 VPN 연결 또는 Azure ExpressRoute를 통해 온-프레미스 Active Directory 또는 DNS 인스턴스에 연결된 경우(이 상태를 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide]에서는 *크로스-프레미스*라 함) VM이 온-프레미스 도메인에 가입할 것으로 예상됩니다. 이 단계 관련 고려사항에 대한 자세한 내용은 [온-프레미스 도메인에 VM 가입(Windows에만 해당)][deployment-guide-4.3]을 참조하세요.

#### <a name="configure-proxy-settings"></a>프록시 설정 구성

온-프레미스 네트워크가 구성된 방법에 따라 VM에 프록시를 설정해야 할 수 있습니다. VM이 VPN 또는 ExpresRoute를 통해 온-프레미스 네트워크에 연결 된 경우 VM이 인터넷에 액세스하지 못할 수 있으며, 필요한 VM 확장을 다운로드하거나 Azure용 SAP 확장을 통해 SAP 호스트 에이전트용 Azure 인프라 정보를 수집할 수 없습니다. [프록시 구성][deployment-guide-configure-proxy]을 참조하세요.

#### <a name="configure-azure-vm-extension-for-sap"></a>SAP용 Azure VM 확장 구성

SAP가 사용자 환경을 지원하도록 하려면 [SAP용 Azure 확장 구성][deployment-guide-4.5]의 설명한 대로 SAP용 Azure 확장을 설정합니다. [SAP 리소스][deployment-guide-2.2]에 나열된 리소스에서 SAP 필수 조건 및 SAP 커널과 SAP 호스트 에이전트의 필수 최소 버전을 확인합니다.

#### <a name="sap-vm-extension-check"></a>SAP VM 확장 검사

[확인 및 문제 해결][deployment-guide-troubleshooting-chapter]에 설명 된 대로 SAP 용 VM 확장이 작동 하는지 확인 합니다.


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>시나리오 3: SAP에서 일반화되지 않은 Azure VHD를 사용하여 온-프레미스 VM 이동

이 시나리오에서는 온-프레미스 환경에서 특정 SAP 시스템을 Azure로 이동하려고 합니다. OS, SAP 이진 파일 및 결과적 DBMS 이진 파일을 포함하고 있는 VHD와 함께 DBMS 데이터와 로그 파일이 있는 VHD를 Azure에 업로드하여 이 작업을 수행할 수 있습니다. [시나리오 2: SAP용 사용자 지정 이미지 VM][deployment-guide-3.3]을 배포하려면, 해당 항목들이 온-프레미스 환경에서 구성되었기 때문에 Azure VM의 호스트 이름, SAP SID 및 SAP 사용자 계정을 유지하세요. OS를 일반화할 필요가 없습니다. 이 시나리오는 SAP 지형의 일부는 온-프레미스를 실행하고 일부는 Azure에서 실행하는 프레미스 간 시나리오에 가장 자주 적용됩니다.

이 시나리오에서 VM 에이전트는 배포하는 동안 자동으로 설치되지 **않습니다.** Azure에서 SAP NetWeaver을 실행하려면 VM 에이전트와 SAP용 Azure 확장이 필요하므로 가상 머신을 만든 후에 두 구성 요소를 모두 수동으로 다운로드하여 설치하고 사용하도록 설정해야 합니다.

Azure VM 에이전트에 대한 자세한 내용은 다음 리소스를 참조하세요.

---
> ![Windows][Logo_Windows] Windows
>
> [Azure 가상 머신 에이전트 개요][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux 에이전트 사용자 가이드][virtual-machines-linux-agent-user-guide]
>
>

---

다음 순서도는 일반화되지 않은 Azure VHD를 사용하여 온-프레미스 VM을 이동하기 위한 단계의 순서를 보여 줍니다.

![VM 디스크를 사용하여 SAP 시스템용 VM 배포 순서도][deployment-guide-figure-400]

디스크가 Azure에 이미 업로드되고 정의되어 있는 경우([SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide] 참조) 다음 몇 섹션에서 설명하는 작업을 수행하세요.

#### <a name="create-a-virtual-machine"></a>가상 머신 만들기

Azure Portal을 통해 프라이빗 OS 디스크를 사용하여 배포를 만들려면 [azure-quickstart-templates GitHub 리포지토리][azure-quickstart-templates-github]에 게시된 SAP 템플릿을 사용하세요. 또한 PowerShell을 사용하여 가상 머신을 직접 만들 수도 있습니다.

* [**2계층 구성(단일 가상 머신) 템플릿**(sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  한 대의 가상 머신을 사용하여 2계층 시스템을 만들려면 이 템플릿을 사용합니다.
* [**2계층 구성(단일 가상 머신) 템플릿 - Managed Disk**(sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  한 대의 가상 머신과 Managed Disk를 사용하여 2계층 시스템을 만들려면 이 템플릿을 사용합니다.

Azure Portal에서 템플릿에 대한 다음 매개 변수를 입력합니다.

1. **기본 사항**:
   * **구독**: 템플릿을 배포하는 데 사용하는 구독입니다.
   * **리소스 그룹**: 템플릿을 배포하는 데 사용하는 리소스 그룹입니다. 새 리소스 그룹을 만들거나 구독에서 기존 리소스 그룹을 선택할 수 있습니다.
   * **위치**: 템플릿을 배포할 위치입니다. 기존 리소스 그룹을 선택한 경우 해당 리소스 그룹의 위치가 사용됩니다.
1. **설정**:
   * **SAP 시스템 ID**: SAP 시스템 ID입니다.
   * **OS 유형**: 배포하려는 운영 체제 형식(Windows 또는 Linux)
   * **SAP 시스템 크기**: SAP 시스템의 크기입니다.

     새 시스템에서 제공하는 SAP의 수입니다. 시스템에 필요한 SAP의 수를 모를 경우 SAP 기술 파트너 또는 시스템 통합자에 문의하세요.
   * **스토리지 유형**(2계층 템플릿만 해당): 사용할 스토리지 유형입니다.

     더 큰 시스템의 경우 Azure Premium Storage를 사용하는 것이 좋습니다. 스토리지 유형에 대한 자세한 내용은 다음 리소스를 참조하세요.
      * [SAP DBMS 인스턴스용 Azure 프리미엄 SSD Storage 사용][2367194]
      * [RDBMS 배포를 위한 VM의 스토리지 구조](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure Virtual Machine 작업을 위한 고성능 스토리지][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage 소개][storage-introduction]
   * **OS 디스크 VHD URI**(관리되지 않는 디스크 템플릿만 해당): 프라이빗 OS 디스크의 URI(예: https://&lt;accountname&gt;.blob.core.windows.net/vhds/osdisk.vhd)입니다.
   * **OS 디스크 관리 디스크 id** (관리 디스크 템플릿만): 관리 되는 디스크 OS 디스크의 id 인/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN
   * **새 서브넷 또는 기존 서브넷**: 새 가상 네트워크 및 서브넷을 만들어야 하는지 또는 기존 서브넷을 사용해야 하는지를 결정합니다. 온-프레미스 네트워크에 연결되어 있는 가상 네트워크가 이미 있는 경우 **기존**을 선택합니다.
   * **서브넷 ID**: 서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 일반적으로 다음과 같이 표시합니다. /subscriptions/&lt;구독 id>/resourceGroups/&lt;리소스 그룹 이름>/providers/Microsoft.Network/virtualNetworks/&lt;가상 네트워크 이름>/subnets/&lt;서브넷 이름>

1. **사용 약관**:  
    약관을 검토하고 동의합니다.

1. **구매**를 선택합니다.

#### <a name="install-the-vm-agent"></a>VM 에이전트 설치

이전 섹션에서 설명한 템플릿을 사용하려면 VM 에이전트가 OS 디스크에 설치되어 있어야 하며, 그렇지 않으면 배포에 실패합니다. [Azure VM 에이전트 다운로드, 설치 및 사용][deployment-guide-4.4]의 설명대로 VM 에이전트를 다운로드하여 VM에 설치하세요.

이전 섹션에서 설명한 템플릿을 사용하지 않는 경우 나중에 VM 에이전트를 설치할 수도 있습니다.

#### <a name="join-a-domain-windows-only"></a>도메인 가입(Windows에만 해당)

Azure 배포가 Azure 사이트 간 VPN 연결 또는 ExpressRoute를 통해 온-프레미스 Active Directory나 DNS 인스턴스에 연결된 경우(이 상태를 [SAP NetWeaver용 Azure Virtual Machines 계획 및 구현][planning-guide]에서는 *크로스-프레미스*라 함) VM이 온-프레미스 도메인에 가입할 것으로 예상됩니다. 이 작업 관련 질문에 대한 자세한 내용은 [온-프레미스 도메인에 VM 가입(Windows에만 해당)][deployment-guide-4.3]을 참조하세요.

#### <a name="configure-proxy-settings"></a>프록시 설정 구성

온-프레미스 네트워크가 구성된 방법에 따라 VM에 프록시를 설정해야 할 수 있습니다. VM이 VPN 또는 ExpresRoute를 통해 온-프레미스 네트워크에 연결 된 경우 VM이 인터넷에 액세스하지 못할 수 있으며, 필요한 VM 확장을 다운로드하거나 Azure용 SAP 확장을 통해 SAP 호스트 에이전트용 Azure 인프라 정보를 수집할 수 없습니다. [프록시 구성][deployment-guide-configure-proxy]을 참조하세요.

#### <a name="configure-azure-vm-extension-for-sap"></a>SAP용 Azure VM 확장 구성

SAP가 사용자 환경을 지원하도록 하려면 [SAP용 Azure 확장 구성][deployment-guide-4.5]의 설명한 대로 SAP용 Azure 확장을 설정합니다. [SAP 리소스][deployment-guide-2.2]에 나열된 리소스에서 SAP 필수 조건 및 SAP 커널과 SAP 호스트 에이전트의 필수 최소 버전을 확인합니다.

#### <a name="sap-vm-check"></a>SAP VM 검사

[확인 및 문제 해결][deployment-guide-troubleshooting-chapter]에 설명 된 대로 SAP 용 VM 확장이 작동 하는지 확인 합니다.

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>SAP용 Azure 확장의 구성 업데이트

다음 시나리오에서 SAP용 Azure 확장의 구성을 업데이트합니다.
* Microsoft/SAP 공동 팀은 VM 확장 기능을 추가했으며 더 많거나 적은 카운터를 요청합니다.
* Microsoft는 데이터를 제공하는 기본 Azure 인프라 신규 버전을 도입했으며, SAP용 Azure 확장은 이러한 변화에 적응할 것입니다.
* Azure VM에서 데이터 디스크를 추가로 탑재하거나 제거합니다. 이 시나리오에서 스토리지 관련 데이터의 컬렉션을 업데이트합니다. 엔드포인트를 추가 또는 삭제하거나 VM에 IP 주소를 할당하여 구성을 변경해도 확장 구성에 영향을 주지 않습니다.
* Azure VM의 크기를 변경합니다(예: 크기 A5에서 다른 VM 크기로).
* Azure VM에 새 네트워크 인터페이스를 추가합니다.

설정을 업데이트하려면 [SAP용 Azure 확장 구성][deployment-guide-4.5]의 단계를 수행하여 SAP용 Azure 확장 구성을 업데이트하세요.

## <a name="detailed-tasks-for-sap-software-deployment"></a>SAP 소프트웨어 배포에 대한 세부 작업

이 섹션에서는 구성 및 배포 프로세스에서 특정 작업을 수행하기 위한 세부 단계를 설명합니다.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell cmdlet 배포

[Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps) 문서에 설명 된 단계를 따릅니다.

일반적으로 매월 업데이트되는 PowerShell cmdlet에 대한 업데이트를 자주 확인하십시오. [이](/powershell/azure/install-az-ps#update-the-azure-powershell-module) 문서에 설명 된 단계를 수행 합니다. SAP Note [1928533] 또는 SAP Note [2015553]에 달리 명시되지 않은 한 Azure PowerShell cmdlet의 최신 버전을 사용하는 것이 좋습니다.

컴퓨터에 설치된 Azure PowerShell cmdlet의 버전을 확인하려면 다음 PowerShell 명령을 실행합니다.

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI 배포

[Azure CLI 설치](/cli/azure/install-azure-cli) 문서에 설명 된 단계를 수행 합니다.

일반적으로 매월 업데이트 되는 Azure CLI에 대한 업데이트를 자주 확인하십시오.

컴퓨터에 설치된 Azure CLI의 버전을 확인하려면 다음 명령을 실행합니다.

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>온-프레미스 도메인에 VM 가입(Windows에만 해당)

온-프레미스 Active Directory 및 DNS가 Azure로 확장되는 프레미스 간 시나리오에서 SAP VM을 배포하는 경우 VM은 온-프레미스 도메인에 가입될 것으로 예상됩니다. 온-프레미스 도메인에 VM을 가입하는 세부 단계 및 온-프레미스 도메인의 멤버가 되기 위해 필요한 추가 소프트웨어는 고객마다 다릅니다. 일반적으로 VM을 온-프레미스 도메인에 가입하려면 맬웨어 방지 소프트웨어와 같은 추가 소프트웨어 및 백업 또는 모니터링 소프트웨어를 설치해야 합니다.

이 시나리오에서 VM이 환경의 도메인에 가입할 때 인터넷 프록시 설정이 강제로 적용된 경우 게스트 VM의 Windows 로컬 시스템 계정(S-1-5-18)이 동일한 프록시 설정을 포함하고 있는지 확인해야 합니다. 가장 쉬운 방법은 도메인 내 시스템에 적용하는 도메인 그룹 정책을 사용하여 프록시를 강제 설정하는 것입니다.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM 에이전트 다운로드, 설치 및 사용

일반화되지 않은 OS 이미지(예: Windows 시스템 준비 도구 또는 sysprep)에서 배포된 가상 머신의 경우 Azure VM 에이전트를 수동으로 다운로드, 설치 및 사용하도록 설정해야 합니다.

Azure Marketplace에서 VM을 배포하는 경우 이 단계가 필요 없습니다. Azure Marketplace의 이미지는 Azure VM 에이전트를 이미 포함하고 있습니다.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Azure VM 에이전트 다운로드:
   1.  [Azure VM 에이전트 설치 관리자 패키지](https://go.microsoft.com/fwlink/?LinkId=394789)를 다운로드합니다.
   1.  VM 에이전트 MSI 패키지를 PC 또는 서버에 로컬로 저장합니다.
1. Azure VM 에이전트 설치:
   1.  RDP(원격 데스크톱 프로토콜)를 사용하여 배포된 Azure VM에 연결합니다.
   1.  VM에서 [Windows 탐색기] 창을 열고 VM 에이전트의 MSI 파일에 대한 대상 디렉터리를 선택합니다.
   1.  로컬 컴퓨터/서버에서 VM의 VM 에이전트 대상 디렉터리로 Azure VM 에이전트 설치 관리자 MSI 파일을 끕니다.
   1.  VM에서 MSI 파일을 두 번 클릭합니다.
1. 온-프레미스 도메인에 가입된 VM의 경우 [프록시 구성][deployment-guide-configure-proxy]의 설명대로 최종 인터넷 프록시 설정이 VM의 Windows 로컬 시스템 계정(S-1-5-18)에도 적용되는지 확인하세요. VM 에이전트를 이 컨텍스트에서 실행하고 Azure에 연결할 수 있어야 합니다.

사용자 개입 없이 Azure VM 에이전트를 업데이트해야 합니다. VM 에이전트가 자동으로 업데이트되며 VM을 다시 시작할 필요가 없습니다.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

다음 명령을 사용하여 Linux용 VM 에이전트를 설치하세요.

* **SUSE Linux Enterprise Server(SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **RHEL(Red Hat Enterprise Linux) 또는 Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

에이전트가 이미 설치된 경우 Azure Linux 에이전트를 업데이트하려면, [GitHub에서 VM의 Azure Linux 에이전트를 최신 버전으로 업데이트][virtual-machines-linux-update-agent]에서 설명한 단계를 수행합니다.

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>프록시 구성

Windows에서 프록시를 구성하기 위해 거치는 단계는 Linux에서 프록시를 구성하는 방법과 다릅니다.

#### <a name="windows"></a>Windows

로컬 시스템 계정이 인터넷에 액세스하려면 프록시 설정을 올바르게 설정해야 합니다. 그룹 정책으로 프록시 설정을 지정하지 않은 경우 로컬 시스템 계정에 대해 프록시 설정을 구성할 수 있습니다.

1. **시작**으로 이동하고 **gpedit.msc**를 입력한 다음 **Enter**를 선택합니다.
1. **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** > **Internet Explorer**를 선택합니다. **사용자 단위보다는 컴퓨터 단위로 프록시 설정 만들기**가 사용하지 않도록 설정되거나 구성되지 않았는지 확인합니다.
1. **제어판**에서 **네트워크 및 공유 센터** > **인터넷 옵션**으로 이동합니다.
1. **연결** 탭에서 **LAN 설정** 버튼을 선택합니다.
1. **자동으로 설정 검색** 확인란을 선택 취소합니다.
1. **LAN에 프록시 서버 사용**을 선택한 다음 프록시 주소 및 포트를 입력합니다.
1. **고급** 단추를 선택합니다.
1. **예외** 상자에 IP 주소 **168.63.129.16**을 입력합니다. **확인**을 선택합니다.

#### <a name="linux"></a>Linux

\\etc\\waagent.conf에 있는 Microsoft Azure 게스트 에이전트의 구성 파일에서 올바른 프록시를 구성합니다.

다음 매개 변수를 설정합니다.

1. **HTTP 프록시 호스트** 예를 들어 **proxy.corp.local**로 설정합니다.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP 프록시 포트** 예를 들어 **80**으로 설정합니다.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. 에이전트를 다시 시작합니다.

   ```console
   sudo service waagent restart
   ```

\\etc\\waagent.conf의 프록시 설정은 필요한 VM 확장에도 적용됩니다. Azure 리포지토리를 사용하려는 경우 이러한 리포지토리에 대한 트래픽이 온-프레미스 인트라넷을 통해 전달되지 않는지 확인합니다. 강제 터널링을 사용하도록 사용자 정의 경로를 만든 경우 사이트 간 VPN 연결을 통해서가 아니라 인터넷에 직접 리포지토리로 트래픽을 라우트하는 경로를 추가해야 합니다.

* **SLES**

  \\etc\\regionserverclnt.cfg에 나열된 IP 주소에 대한 경로도 추가해야 합니다. 다음 그림은 예를 보여 줍니다.

  ![강제 터널링][deployment-guide-figure-50]


* **RHEL**

  \\etc\\yum.repos.d\\rhui-load-balancers에 나열된 호스트의 IP 주소에 대한 경로도 추가해야 합니다. 예는 앞의 그림을 참조하세요.

* **Oracle Linux**

  Azure에서 Oracle Linux에 대한 리포지토리가 없습니다. Oracle Linux에 대한 사용자 고유의 리포지토리를 구성하거나 공용 리포지토리를 사용해야 합니다.

사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달][virtual-networks-udr-overview]을 참조하세요.

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>SAP용 Azure 확장 구성

> [!NOTE]
> 일반 지원 문: SAP 용 Azure 확장에 대 한 지원이 필요한 경우에는 항상 SAP의 구성 요소 BC-OP-MS-AZR-0017P-NT-MS-AZR-0017P에서 인시던트를 열어야 합니다.
> 공동 고객을 지원 하기 위해 SAP 지원 시스템에서 작업 하는 전담 Microsoft 지원 엔지니어가 있습니다.

[Azure의 SAP용 VM 배포 시나리오][deployment-guide-3]의 설명대로 VM을 준비한 경우, Azure VM 에이전트가 가상 머신에 설치됩니다. 다음 단계는 글로벌 Azure 데이터 센터의 Azure 확장 리포지토리에서 사용할 수 있는 SAP용 Azure 확장을 배포하는 것입니다. 자세한 내용은 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide-9.1]을 참조하세요.

SAP 용 Azure 확장의 새 버전을 릴리스 하는 중입니다. 새 확장은 가상 컴퓨터의 시스템 할당 id를 사용 하 여 연결 된 디스크, 네트워크 인터페이스 및 가상 컴퓨터 자체에 대 한 정보를 가져옵니다. 이러한 리소스에 액세스할 수 있도록 가상 컴퓨터의 시스템 id에는 가상 컴퓨터, OS 디스크, 데이터 디스크 및 네트워크 인터페이스에 대 한 읽기 권한이 있어야 합니다. 현재는 다음 시나리오 에서만 새 확장을 설치 하는 것이 좋습니다.

1. Terraform, Azure Resource Manager 템플릿 또는 Azure CLI 또는 다른 수단으로 확장을 설치 하려는 경우 또는 Azure PowerShell
1. SUSE SLES 15 이상에 확장을 설치 하려고 합니다.
1. Microsoft 또는 SAP 지원에서 새 확장을 설치 하 라는 메시지를 표시 합니다.
1. Azure Ultra Disk 또는 Standard Managed Disks를 사용 하려고 합니다.

이러한 시나리오의 경우 Azure PowerShell에 대 한 [Azure PowerShell를 사용 하 여 sap 용 새 Azure 확장 구성][deployment-guide-configure-new-extension-ps] 또는 Azure CLI에 대 한 [Azure CLI를 사용 하 여 Sap 용 새 azure 확장 구성][deployment-guide-configure-new-extension-cli] 장의 단계를 따르세요.

[Azure PowerShell][deployment-guide-4.5.1] 또는 [Azure CLI][deployment-guide-4.5.2] 를 수행 하 여 SAP 용 Azure 확장의 표준 버전을 설치 하 고 구성 합니다.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Linux 및 Windows VM용 Azure PowerShell

다음은 PowerShell을 사용하여 SAP용 Azure 확장을 설치하는 방법입니다.

1. 최신 버전의 Azure PowerShell cmdlet을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell cmdlet 배포][deployment-guide-4.1]를 참조하세요.  
1. 다음 PowerShell cmdlet을 실행합니다.
    사용 가능한 환경 목록을 보려면 cmdlet을 실행 `Get-AzEnvironment` 합니다. 전역 Azure를 사용하려는 경우 환경은 **AzureCloud**입니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud**를 선택 합니다.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

계정 데이터를 입력 하면 스크립트에서 필요한 확장을 배포 하 고 필요한 기능을 사용 하도록 설정 합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.
`Set-AzVMAEMExtension`에 대한 자세한 내용은 [Set-AzVMAEMExtension][msdn-set-Azvmaemextension]을 참조하세요.

![SAP 관련 Azure cmdlet Set-AzVMAEMExtension의 성공적인 실행][deployment-guide-figure-900]

`Set-AzVMAEMExtension`구성은 SAP용 호스트 데이터 수집을 구성하는 모든 단계를 수행합니다.

스크립트 출력에는 다음 정보가 포함됩니다.

* OS 디스크 및 모든 추가 데이터 디스크에 대한 확인이 구성됩니다.
* 다음 두 개의 메시지는 특정 Storage 계정에 대한 Storage 메트릭의 구성을 확인합니다.
* 출력 한 줄은 SAP용 VM 확장 구성의 실제 업데이트 상태를 나타냅니다.
* 출력의 또 다른 줄은 구성이 배포되거나 업데이트되었음을 확인해 줍니다.
* 출력의 마지막 줄은 정보 제공용이며, SAP용 VM 확장 구성 테스트 옵션을 보여 줍니다.
* SAP용 Azure VM 확장의 모든 단계의 성공적인 실행 여부와 Azure 인프라가 필요한 데이터를 제공하는지 확인하려면 [SAP용 Azure 확장 관련 준비 검사][deployment-guide-5.1]의 설명대로 SAP용 Azure 확장 준비 검사를 진행합니다.
* Azure Diagnostics가 관련 데이터를 수집하도록 15-30분 동안 기다립니다.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux VM용 Azure CLI

다음은 Azure CLI를 사용하여 SAP용 Azure 확장을 설치하는 방법입니다.

   1. [Azure 클래식 CLI 설치][azure-cli]의 설명대로 Azure 클래식 CLI를 설치합니다.
   1. Azure 계정으로 로그인합니다.

      ```console
      azure login
      ```

   1. Azure Resource Manager 모드로 전환합니다.

      ```console
      azure config mode arm
      ```

   1. SAP용 Azure 확장을 사용하도록 설정합니다.

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Azure CLI 2.0을 사용하여 설치

   1. [Azure CLI 2.0 설치][azure-cli-2]의 설명대로 Azure CLI 2.0을 설치합니다.
   1. Azure 계정으로 로그인합니다.

      ```azurecli
      az login
      ```

   1. Azure CLI AEM 확장 설치
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. 다음을 사용하여 확장 설치
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Azure Linux VM에서 SAP용 Azure 확장이 활성화되어 있는지 확인합니다. \\var\\lib\\AzureEnhancedMonitor\\PerfCounters 파일이 있는지 여부를 확인합니다. 파일이 존재하면 명령 프롬프트에서 이 명령을 실행하여 SAP용 Azure 확장이 수집한 정보를 표시합니다.

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   출력은 다음과 같이 표시됩니다.

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Azure PowerShell를 사용 하 여 SAP 용 새 Azure 확장 구성

SAP 용 새 VM 확장은 vm에 할당 된 관리 Id를 사용 하 여 VM의 모니터링 및 구성 데이터에 액세스 합니다. PowerShell을 사용 하 여 새로운 SAP 용 Azure 확장을 설치 하려면 먼저 VM에 이러한 id를 할당 하 고 해당 VM에서 사용 중인 모든 리소스 (예: 디스크 및 네트워크 인터페이스)에 대 한 id 액세스 권한을 부여 해야 합니다.

> [!NOTE]
> 다음 단계를 수행 하려면 리소스 그룹 또는 개별 리소스 (가상 머신, 데이터 디스크 등)에 대 한 소유자 권한이 있어야 합니다.

1. SAP Host Agent 7.21 PL 47 이상을 사용 해야 합니다.
1. 최신 버전의 SAP 용 VM 확장을 제거 해야 합니다. 동일한 가상 컴퓨터에 SAP 용 VM 확장의 두 버전을 모두 설치할 수는 없습니다.
1. 최신 버전의 Azure PowerShell cmdlet (최소 4.3.0)을 설치 했는지 확인 합니다. 자세한 내용은 [Azure PowerShell cmdlet 배포][deployment-guide-4.1]를 참조하세요.
1. 다음 PowerShell cmdlet을 실행합니다.
    사용 가능한 환경 목록을 보려면 cmdlet을 실행 `Get-AzEnvironment` 합니다. 전역 Azure를 사용하려는 경우 환경은 **AzureCloud**입니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud**를 선택 합니다.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Azure CLI를 사용 하 여 SAP 용 새 Azure 확장 구성

SAP 용 새 VM 확장은 vm에 할당 된 관리 Id를 사용 하 여 VM의 모니터링 및 구성 데이터에 액세스 합니다. Azure CLI를 사용 하 여 SAP 용 새 Azure 확장을 설치 하려면 먼저 VM에 이러한 id를 할당 하 고 해당 VM에서 사용 중인 모든 리소스 (예: 디스크 및 네트워크 인터페이스)에 대 한 id 액세스 권한을 부여 해야 합니다.

> [!NOTE]
> 다음 단계를 수행 하려면 리소스 그룹 또는 개별 리소스 (가상 머신, 데이터 디스크 등)에 대 한 소유자 권한이 있어야 합니다.

1. SAP Host Agent 7.21 PL 47 이상을 사용 해야 합니다.
1. 최신 버전의 SAP 용 VM 확장을 제거 해야 합니다. 동일한 가상 컴퓨터에 SAP 용 VM 확장의 두 버전을 모두 설치할 수는 없습니다.
1. [Azure CLI 2.0 설치][azure-cli-2]의 설명대로 Azure CLI 2.0을 설치합니다.

1. Azure 계정으로 로그인합니다.

   ```azurecli
   az login
   ```

1. [Azure CLI를 사용 하 여 AZURE vm에서 azure 리소스에 대 한 관리 되는 Id 구성][qs-configure-cli-windows-vm] 문서에 나오는 단계에 따라 VM에 대 한 시스템 할당 관리 id를 사용 하도록 설정 합니다. 사용자 할당 관리 Id는 SAP 용 VM 확장에서 지원 되지 않습니다. 그러나 시스템에 할당 된 id와 사용자 할당 id를 모두 사용 하도록 설정할 수 있습니다.

   예:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. 관리 id를 VM의 리소스 그룹 또는 모든 네트워크 인터페이스, 관리 디스크 및 VM 자체에 할당 합니다. 여기에 설명 된 대로 [Azure CLI를 사용 하 여 리소스에 대 한 관리 id 액세스 권한을 할당][howto-assign-access-cli] 합니다.

    예:

    ```azurecli
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. 다음 Azure CLI 명령을 실행 하 여 SAP 용 Azure 확장을 설치 합니다.
    확장은 현재 AzureCloud 에서만 지원 됩니다. Azure 중국 21Vianet, Azure Government 또는 기타 특수 환경은 아직 지원 되지 않습니다.

    ```azurecli
    # For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    #For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>확인 및 문제 해결

Azure VM을 배포하고 SAP용 Azure 확장 관련을 설정한 후, Azure 확장의 모든 구성 요소가 예상한 대로 작동하는지 확인합니다.

[SAP용 Azure 확장 관련 준비 검사][deployment-guide-5.1]의 설명대로 SAP용 Azure 확장 관련 준비 검사를 실행합니다. 모든 준비 검사 결과가 긍정적이고 모든 관련 성능 카운터가 정상으로 나타나면, SAP용 Azure 확장이 성공적으로 설정된 것입니다. [SAP 리소스][deployment-guide-2.2]의 SAP Notes의 설명대로 SAP 호스트 에이전트 설치를 진행할 수 있습니다. 준비 검사에서 카운터가 누락된 것으로 표시되는 경우 [SAP용 Azure 확장 구성 관련 상태 검사][deployment-guide-5.2]의 설명대로 SAP용 Azure 확장 구성 관련 상태 검사를 실행합니다. 자세한 문제 해결 옵션은 [SAP용 Azure 확장 문제 해결][deployment-guide-5.3]을 참조하세요.

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>SAP용 Azure 확장 관련 준비 검사

> [!NOTE]
> VM 확장에는 두 가지 버전이 있습니다. 이 장에서는 기본 VM 확장에 대해 설명 합니다. 새 VM 확장을 설치한 경우 [SAP 용 새 Azure 확장에 대 한 준비 검사][deployment-guide-5.1-new] 챕터를 참조 하세요.

이 검사에서는 SAP 애플리케이션 내부에 나타나는 모든 성능 메트릭이 기본 SAP용 Azure 확장에서 제공되는지 확인합니다.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM에서 준비 검사 실행

1. Azure Virtual Machine에 로그인합니다(관리자 계정 사용은 필요하지 않음).
1. 명령 프롬프트 창을 엽니다.
1. 명령 프롬프트에서 SAP용 Azure 확장의 설치 폴더로 디렉터리를 변경하세요. C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;버전>\\drop)로 변경합니다.

   확장에 대한 경로의 *버전*이 다를 수 있습니다. 설치 폴더에 여러 확장 버전의 폴더가 표시되는 경우 AzureEnhancedMonitoring Windows 서비스의 구성을 확인한 다음, *실행 파일 경로*로 나타난 폴더로 전환하세요.

   ![SAP용 Azure 확장을 실행하는 서비스의 속성][deployment-guide-figure-1000]

1. 명령 프롬프트에서 매개 변수 없이 **azperflib.exe**를 실행합니다.

   > [!NOTE]
   > Azperflib.exe는 루프에서 실행되고 60초마다 수집한 카운터를 업데이트합니다. 루프를 종료하려면 명령 프롬프트 창을 닫습니다.
   >
   >

SAP용 Azure 확장이 설치되어 있지 않거나 AzureEnhancedMonitoring 서비스가 실행되고 있지 않으면, 확장이 올바르게 구성되지 않은 것입니다. 확장 배포 방법 관련 자세한 내용은 [SAP용 Azure 확장 문제 해결][deployment-guide-5.3]을 참조하세요.

> [!NOTE]
> Azperflib.exe는 자체 용도로 사용할 수 없는 구성 요소입니다. 이 구성 요소는 VM 관련 Azure 인프라 데이터를 SAP 호스트 에이전트의 VM에 전달합니다.
> 

##### <a name="check-the-output-of-azperflibexe"></a>azperflib.exe의 출력 확인

Azperflib.exe 출력은 SAP용 Azure 성능 카운터가 모두 채워진 상태로 표시됩니다. 수집된 카운터 목록의 맨 아래에 있는 요약 및 상태 표시기가 SAP용 Azure 확장의 상태를 보여 줍니다.

![문제가 없음을 나타내는 azperflib.exe를 실행하여 표시된 상태 검사 출력][deployment-guide-figure-1100]
<a name="figure-11"></a>

빈 상태로 보고되는 **카운터 합계** 출력 및 이전 그림에 표시된 **상태 정보**에 대해 반환되는 결과를 확인합니다.

결과 값을 다음과 같이 해석합니다.

| Azperflib.exe 결과 값 | SAP용 Azure 확장 상태 |
| --- | --- |
| **API 호출 - 사용할 수 없음** | 사용할 수 없는 카운터는 가상 컴퓨터 구성에 적용할 수 없거나 오류입니다. **상태 정보**를 참조하세요. |
| **카운터 합계 - 비어 있음** |다음 두 Azure Storage 카운터는 비어 있을 수 있습니다. <ul><li>스토리지 읽기 작업 대기 시간 서버 밀리초</li><li>스토리지 읽기 작업 대기 시간 E2E 밀리초</li></ul>그 외의 카운터는 값이 있어야 합니다. |
| **상태 정보** |반환 상태가 **OK**를 표시하는 경우에만 OK입니다. |
| **진단** |상태 정보에 대한 자세한 정보입니다. |

**상태** 값이 **OK**가 아닌 경우, SAP용 Azure 확장 구성 상태 검사의 [지침을 따릅니다][deployment-guide-5.2].

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM에서 준비 검사 실행

1. SSH를 사용하여 Azure Virtual Machine에 연결합니다.

1. SAP용 Azure 확장의 출력을 확인합니다.

   a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters`을 실행합니다.

   **예상 결과**: 성능 카운터 목록을 반환합니다. 파일은 비어 있으면 안 됩니다.

   b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`을 실행합니다.

   **예상 결과**: 오류가 **없는** 한 줄을 반환합니다(예: **3;config;Error;;0;0;none;0;1456416792;tst-servercs;** ).

   다. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`을 실행합니다.

   **예상 결과**: 빈 상태로 반환하거나 존재하지 않습니다.

이전 검사가 성공하지 못한 경우 다음 추가 검사를 실행합니다.

1. waagent가 설치되고 사용하도록 설정되었는지 확인합니다.

   a.  `sudo ls -al /var/lib/waagent/`을 실행합니다.

     **예상 결과**: waagent 디렉터리의 내용을 나열합니다.

   b.  `ps -ax | grep waagent`을 실행합니다.

   **예상 결과**: `python /usr/sbin/waagent -daemon`과 유사한 하나의 항목을 표시합니다.

1. SAP용 Azure 확장이 설치되어 실행되는지 확인합니다.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`을 실행합니다.

   **예상 결과**: SAP용 Azure 확장 디렉터리의 콘텐츠를 나열합니다.

   b. `ps -ax | grep AzureEnhanced`을 실행합니다.

   **예상 결과**: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`과 유사한 하나의 항목을 표시합니다.

1. SAP Note [1031096] 에 설명된 대로 SAP 호스트 에이전트를 설치하고 `saposcol`의 출력을 확인합니다.

   a.  `/usr/sap/hostctrl/exe/saposcol -d`을 실행합니다.

   b.  `dump ccm`을 실행합니다.

   다.  **Virtualization_Configuration\Enhanced Monitoring Access** 메트릭이 **true**인지 여부를 확인합니다.

SAP NetWeaver ABAP 응용 프로그램 서버가 이미 설치 되어 있는 경우 트랜잭션 S T 06을 열고 모니터링이 사용 하도록 설정 되어 있는지 확인 합니다.

하나 이상의 검사에 실패한 경우 확장을 다시 배포하는 방법 관련 자세한 내용은 [SAP용 Azure 확장 문제 해결][deployment-guide-5.3]을 참조하세요.

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>SAP 용 새 Azure 확장에 대 한 준비 검사

> [!NOTE]
> VM 확장에는 두 가지 버전이 있습니다. 이 장에서는 새 VM 확장에 대해 설명 합니다. 기본 VM 확장을 설치한 경우 [SAP 용 Azure 확장에 대 한 준비 검사][deployment-guide-5.1]챕터를 참조 하세요.

이 검사에서는 SAP 애플리케이션 내부에 나타나는 모든 성능 메트릭이 기본 SAP용 Azure 확장에서 제공되는지 확인합니다.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM에서 준비 검사 실행

1. Azure Virtual Machine에 로그인합니다(관리자 계정 사용은 필요하지 않음).
1. 웹 브라우저를 열고로 이동 합니다. http://127.0.0.1:11812/azure4sap/metrics
1. 브라우저에서 가상 컴퓨터의 모니터링 데이터를 포함 하는 XML 파일을 표시 하거나 다운로드 해야 합니다. 그렇지 않은 경우 SAP 용 Azure 확장이 설치 되어 있는지 확인 합니다.

##### <a name="check-the-content-of-the-xml-file"></a>XML 파일의 내용 확인

에서 액세스할 수 있는 XML 파일에는 http://127.0.0.1:11812/azure4sap/metrics SAP 용으로 채워진 모든 Azure 성능 카운터가 포함 되어 있습니다. SAP 용 Azure 확장의 상태에 대 한 요약 및 상태 표시기도 포함 됩니다.

**공급자 상태 설명** 요소의 값을 확인 합니다. 값이 **양호**하지 않으면 [SAP 구성에 대 한 새 Azure 확장에 대 한 상태 검사][deployment-guide-5.2-new]의 지침을 따르세요.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM에서 준비 검사 실행

1. SSH를 사용하여 Azure Virtual Machine에 연결합니다.

1. 다음 명령의 출력을 확인 합니다.

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **예상 결과**: 가상 머신의 모니터링 정보, 해당 디스크 및 네트워크 인터페이스를 포함 하는 XML 문서를 반환 합니다.

이전 검사가 성공하지 못한 경우 다음 추가 검사를 실행합니다.

1. waagent가 설치되고 사용하도록 설정되었는지 확인합니다.

   a.  `sudo ls -al /var/lib/waagent/`을 실행합니다.

     **예상 결과**: waagent 디렉터리의 내용을 나열합니다.

   b.  `ps -ax | grep waagent`을 실행합니다.

   **예상 결과**: `python /usr/sbin/waagent -daemon`과 유사한 하나의 항목을 표시합니다.

1. SAP용 Azure 확장이 설치되어 실행되는지 확인합니다.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`을 실행합니다.

   **예상 결과**: SAP용 Azure 확장 디렉터리의 콘텐츠를 나열합니다.

   b. `ps -ax | grep AzureEnhanced`을 실행합니다.

   **예상 결과**: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`과 유사한 하나의 항목을 표시합니다.

1. SAP Note [1031096] 에 설명된 대로 SAP 호스트 에이전트를 설치하고 `saposcol`의 출력을 확인합니다.

   a.  `/usr/sap/hostctrl/exe/saposcol -d`을 실행합니다.

   b.  `dump ccm`을 실행합니다.

   다.  **Virtualization_Configuration\Enhanced Monitoring Access** 메트릭이 **true**인지 여부를 확인합니다.

SAP NetWeaver ABAP 응용 프로그램 서버가 이미 설치 되어 있는 경우 트랜잭션 S T 06을 열고 모니터링이 사용 하도록 설정 되어 있는지 확인 합니다.

이러한 검사 중 하나라도 실패 하는 경우 확장을 다시 배포 하는 방법에 대 한 자세한 내용은 [SAP 용 새 Azure 확장 문제 해결][deployment-guide-5.3-new]을 참조 하세요.

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>SAP용 Azure 확장 구성 관련 상태 검사

> [!NOTE]
> VM 확장에는 두 가지 버전이 있습니다. 이 장에서는 기본 VM 확장에 대해 설명 합니다. 새 VM 확장을 설치한 경우 [SAP 용 새 Azure 확장 구성에 대 한 상태 검사][deployment-guide-5.2-new]챕터를 참조 하세요.

[SAP용 Azure 확장 관련 준비 검사][deployment-guide-5.1]에 설명된 테스트대로 일부 인프라 데이터가 올바르게 전달되지 않으면, `Test-AzVMAEMExtension`cmdlet을 실행하여 Azure 인프라 및 SAP용 Azure 확장이 올바르게 구성되어 있는지 확인합니다.

1. [Azure PowerShell cmdlet 배포][deployment-guide-4.1]의 설명대로 Azure PowerShell cmdlet 최신 버전을 설치했는지 확인합니다.
1. 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록을 보려면 `Get-AzEnvironment` cmdlet을 실행합니다. 전역 Azure를 사용하려면**AzureCloud** 환경을 선택합니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud**를 선택 합니다.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. 선택한 가상 컴퓨터의 구성을 테스트하는 스크립트입니다.

   ![SAP용 Azure 확장의 성공적인 테스트 출력][deployment-guide-figure-1300]

모든 상태 검사 결과가 **OK**인지 확인합니다. 일부 검사가 **OK**를 표시하지 않는 경우 [SAP용 Azure 확장 구성][deployment-guide-4.5]의 설명대로 cmdlet 업데이트를 실행합니다. 15분 동안 기다렸다가 [SAP용 Azure 확장 관련 준비 검사][deployment-guide-5.1] 및 [SAP용 Azure 확장 구성 관련 상태 검사][deployment-guide-5.2]의 설명대로 검사를 반복합니다. 검사에서 여전히 일부 또는 모든 카운터에 문제가 있다고 표시되면, [SAP용 Azure 확장 문제 해결][deployment-guide-5.3]을 참조하세요.

> [!Note]
> 관리되는 표준 Azure 디스크를 사용하는 경우 일부 경고가 발생할 수 있습니다. 테스트에서 "확인"을 반환하는 대신 경고가 표시됩니다. 이 경고는 해당 디스크 형식의 경우 정상적이며 의도된 것입니다. 또한 [SAP용 Azure 확장 문제 해결][deployment-guide-5.3]을 참조하세요
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>SAP 용 새 Azure 확장 구성에 대 한 상태 검사

> [!NOTE]
> VM 확장에는 두 가지 버전이 있습니다. 이 장에서는 새 VM 확장에 대해 설명 합니다. 기본 VM 확장을 설치한 경우 [SAP 용 Azure 확장 구성에 대 한 상태 검사][deployment-guide-5.2]챕터를 참조 하세요.

[Sap 용 Azure 확장에 대 한 준비 검사][deployment-guide-5.1-new]에 설명 된 대로 일부 인프라 데이터가 올바르게 배달 되지 않는 경우 cmdlet을 실행 `Get-AzVMExtension` 하 여 Sap 용 azure 확장이 설치 되어 있는지 확인 합니다. 에서 `Test-AzVMAEMExtension` 아직 새 확장을 지원 하지 않습니다. 새 확장을 지 원하는 cmdlet은이 문서를 업데이트 합니다.

1. [Azure PowerShell cmdlet 배포][deployment-guide-4.1]의 설명대로 Azure PowerShell cmdlet 최신 버전을 설치했는지 확인합니다.
1. 다음 PowerShell cmdlet을 실행합니다. 사용 가능한 환경 목록을 보려면 `Get-AzEnvironment` cmdlet을 실행합니다. 전역 Azure를 사용하려면**AzureCloud** 환경을 선택합니다. Azure 중국 21Vianet의 경우 **AzureChinaCloud**를 선택 합니다.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Cmdlet은 선택한 가상 컴퓨터에서 SAP 용 VM 확장의 구성을 테스트 합니다.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>SAP용 Azure 확장 문제 해결

> [!NOTE]
> VM 확장에는 두 가지 버전이 있습니다. 이 장에서는 기본 VM 확장에 대해 설명 합니다. 새 VM 확장을 설치한 경우 [SAP 용 새 Azure 확장 문제 해결][deployment-guide-5.3-new]장을 참조 하세요.

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure 성능 카운터가 전혀 표시되지 않습니다.

AzureEnhancedMonitoring Windows 서비스에서 Azure의 성능 메트릭을 수집합니다. 서비스가 올바르게 설치되지 않은 경우 또는 VM에서 실행되지 않는 경우 성능 메트릭을 수집할 수 없습니다.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다

###### <a name="issue"></a>문제

설치 디렉터리 C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop이 비어 있습니다.

###### <a name="solution"></a>해결 방법

확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작하거나 `Set-AzVMAEMExtension` 구성 스크립트를 다시 실행해야 할 수 있습니다.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>SAP용 Azure 확장 서비스가 존재하지 않습니다

###### <a name="issue"></a>문제

AzureEnhancedMonitoring Windows 서비스가 존재하지 않습니다.

Azperflib.exe 출력에 오류가 발생합니다.

![SAP용 Azure 확장의 서비스가 실행되지 않음을 나타내는 Azperflib.exe의 실행][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>해결 방법

서비스가 존재하지 않으면 SAP용 Azure 확장이 제대로 설치되지 않은 것입니다. [Azure에서 SAP용 VM 배포 시나리오][deployment-guide-3]에서 배포 시나리오 설명 단계에 따라 확장을 다시 배포하세요.

확장을 배포하고 1시간 후 Azure VM 내에서 Azure 성능 카운터가 제공되는지 여부를 다시 확인합니다.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>SAP용 Azure 확장 관련 서비스가 존재하나 시작에 실패함

###### <a name="issue"></a>문제

AzureEnhancedMonitoring Windows 서비스가 존재하고 사용하도록 설정되었지만 시작할 수 없습니다. 자세한 내용은 애플리케이션 이벤트 로그를 확인합니다.

###### <a name="solution"></a>해결 방법

구성이 올바르지 않습니다. [SAP용 Azure 확장 구성][deployment-guide-4.5]의 설명대로 VM에서 SAP용 Azure 확장을 다시 시작하세요.

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 일부 Azure 성능 카운터가 없습니다.

AzureEnhancedMonitoring Windows 서비스에서 Azure의 성능 메트릭을 수집합니다. 이 서비스는 여러 원본에서 데이터를 가져옵니다. 일부 구성 데이터는 로컬로 수집되고 일부 성능 메트릭은 Azure Diagnostics에서 읽습니다. 스토리지 카운터는 스토리지 구독 수준에 대한 로깅에서 사용됩니다.

SAP Note [1999351]을 사용한 문제 해결로 문제가 해결되지 않으면 `Set-AzVMAEMExtension` 구성 스크립트를 다시 실행합니다. 사용하도록 설정한 후 바로 스토리지 분석 또는 진단 카운터가 생성되지 않을 수 있으므로 1시간 동안 기다려야 할 수 있습니다. 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에 대한 SAP 고객 지원 메시지를 엽니다.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure 성능 카운터가 전혀 표시되지 않습니다.

Azure의 성능 메트릭은 데몬에 의해 수집됩니다. 데몬이 실행되지 않는 경우 성능 메트릭은 전혀 수집할 수 없습니다.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다

###### <a name="issue"></a>문제

디렉터리 \\var\\lib\\waagent\\에 SAP용 Azure 확장의 하위 디렉터리가 존재하지 않습니다.

###### <a name="solution"></a>해결 방법

확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작하거나 `Set-AzVMAEMExtension` 구성 스크립트를 다시 실행해야 할 수 있습니다.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Set-AzVMAEMExtension 및 Test-AzVMAEMExtension을 실행하면 표준 Managed Disks를 지원하지 않는다는 경고 메시지가 표시됨

###### <a name="issue"></a>문제

Set-AzVMAEMExtension 또는 Test-AzVMAEMExtension을 실행하는 경우 다음과 같은 메시지가 표시됩니다.

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

앞서 설명한 대로 azperfli.exe를 실행하면 비정상 상태를 나타내는 결과를 얻을 수 있습니다. 

###### <a name="solution"></a>해결 방법

이 메시지는 표준 Azure Storage 계정에 대한 통계를 확인하기 위해 SAP용 SAP 확장에서 사용하는 API를 표준 Managed Disks가 전달하지 않아 발생합니다. 심각한 문제는 아닙니다. 표준 디스크 저장소 계정에 대 한 데이터 수집을 도입 하는 이유는 자주 발생 하는 입력 및 출력의 제한 이었습니다. 관리 디스크는 스토리지 계정에서 디스크 수를 제한하여 이러한 제한을 방지합니다. 따라서 해당 유형의 데이터 여부는 중요하지 않습니다.


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 일부 Azure 성능 카운터가 없습니다.

Azure에서 성능 메트릭은 여러 원본에서 데이터를 가져오는 데몬에 의해 수집됩니다. 일부 구성 데이터는 로컬로 수집되고 일부 성능 메트릭은 Azure Diagnostics에서 읽습니다. 스토리지 카운터는 스토리지 구독의 로그에서 제공됩니다.

알려진 문제의 전체 최신 목록은 SAP용 Azure 확장 관련 문제 해결 정보를 추가로 포함하는 SAP Note [1999351]을 참조하세요.

SAP Note [1999351] 문제 해결로 문제가 해결되지 않는 경우 [SAP용 Azure 확장 구성하기][deployment-guide-4.5]의 설명대로 `Set-AzVMAEMExtension`구성 스크립트를 다시 실행하세요. 사용하도록 설정한 후 바로 스토리지 분석 또는 진단 카운터가 생성되지 않을 수 있으므로 1시간 동안 기다려야 할 수 있습니다. 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에 대한 SAP 고객 지원 메시지를 엽니다.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>SAP 용 새 Azure 확장 문제 해결

> [!NOTE]
> VM 확장에는 두 가지 버전이 있습니다. 이 장에서는 새 VM 확장에 대해 설명 합니다. 기본 VM 확장을 설치한 경우 [SAP 용 Azure 확장 문제 해결][deployment-guide-5.3]장을 참조 하세요.

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure 성능 카운터가 전혀 표시되지 않습니다.

AzureEnhancedMonitoring 프로세스는 Azure에서 성능 메트릭을 수집 합니다. VM에서 프로세스가 실행 되 고 있지 않으면 성능 메트릭을 수집할 수 없습니다.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다

###### <a name="issue"></a>문제

설치 디렉터리 C: \\ 패키지 \\ 플러그 인 \\ AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; 버전> 비어 있습니다.

###### <a name="solution"></a>해결 방법

확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작 하거나 VM 확장을 다시 설치 해야 할 수 있습니다.

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 일부 Azure 성능 카운터가 없습니다.

AzureEnhancedMonitoring Windows 프로세스는 Azure에서 성능 메트릭을 수집 합니다. 프로세스는 여러 원본에서 데이터를 가져옵니다. 일부 구성 데이터는 로컬로 수집 되 고 일부 성능 메트릭은 Azure Monitor에서 읽습니다.

SAP Note [1999351] 을 사용 하 여 문제를 해결 해도 문제가 해결 되지 않으면 Linux 가상 머신에 대 한 ms-azr-0017p 구성 요소에서 sap 고객 지원 메시지를 엽니다. 로그 파일 C: \\ 패키지 \\ 플러그 인 \\ AzureCAT. MonitorX64Windows \\ &lt; 버전>\\logapp.txt를 인시던트에 연결 하세요.

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure 성능 카운터가 전혀 표시되지 않습니다.

Azure의 성능 메트릭은 데몬에 의해 수집됩니다. 데몬이 실행되지 않는 경우 성능 메트릭은 전혀 수집할 수 없습니다.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP용 Azure 확장의 설치 디렉터리가 비어 있습니다

###### <a name="issue"></a>문제

디렉터리 \\var\\lib\\waagent\\에 SAP용 Azure 확장의 하위 디렉터리가 존재하지 않습니다.

###### <a name="solution"></a>해결 방법

확장이 설치되지 않았습니다. (앞에서 설명한) 프록시 문제인지 여부를 결정합니다. 컴퓨터를 다시 시작 하거나 VM 확장을 다시 설치 해야 할 수 있습니다.

#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 일부 Azure 성능 카운터가 없습니다.

Azure에서 성능 메트릭은 여러 원본에서 데이터를 가져오는 데몬에 의해 수집됩니다. 일부 구성 데이터는 로컬로 수집 되 고 일부 성능 메트릭은 Azure Monitor에서 읽습니다.

알려진 문제의 전체 최신 목록은 SAP용 Azure 확장 관련 문제 해결 정보를 추가로 포함하는 SAP Note [1999351]을 참조하세요.

SAP Note [1999351] 을 사용 하 여 문제를 해결 해도 문제가 해결 되지 않는 경우 [Sap 용 Azure 확장 구성][deployment-guide-4.5]에 설명 된 대로 다시 확장을 설치 합니다. 문제가 지속되면 Windows용 BC-OP-NT-AZR 또는 Linux 가상 머신용 BC-OP-LNX-AZR 구성 요소에 대한 SAP 고객 지원 메시지를 엽니다. /Var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; version>/logapp.txt 로그 파일을 인시던트에 연결 하십시오.

## <a name="azure-extension-error-codes"></a>Azure 확장 오류 코드

| 오류 ID | 오류 설명 | 솔루션 |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | 앱 구성이 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | 앱 구성에 배포 ID가 없습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | 앱 구성에 RoleInstanceId가 없습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | 앱 구성에 RoleInstanceId가 없습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure 구성을 읽을 수 없습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | 앱 구성 파일이 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | 앱 구성에 VM 크기가 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | GlobalMemoryStatusEx 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | NIC 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | 디스크 매핑 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | 프로세서 이름 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | 디스크 매핑 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | config.xml 확장 구성 파일에 ' Disk type ' 메트릭이 없습니다. 일부 다른 카운터와 함께 ' Disk type '은 v 2.2.0.68 12/16/2015에서 도입 되었습니다. 12/16/2015 이전에 확장을 배포한 경우 이전 구성 파일을 사용 합니다. Azure 확장 프레임 워크는 확장을 최신 버전으로 자동으로 업그레이드 하지만 config.xml는 변경 되지 않은 상태로 유지 됩니다. 구성을 업데이트 하려면 최신 PowerShell 설치 스크립트를 다운로드 하 여 실행 합니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | 디스크 캐싱이 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | 디스크 SLA 처리량이 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 이상 | 디스크 SLA IOPS가 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | 디스크 매핑 카운터가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | 마지막 하드웨어 변경 카운터에 오류가 발생 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030-kids | NIC 카운터 실패 | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | VM의 sysprep로 인해 Windows SID가 변경 되었습니다. | [sysprep 후 다시 배포][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | 저장소 분석에 액세스 하지 못했습니다. <br /><br />새로 만든 VM에서 저장소 분석 데이터를 채울 때 한 시간에 최대 30 분이 걸릴 수 있으므로 시간이 지나면 오류가 사라질 수 있습니다. 오류가 계속 표시 되 면 설치 스크립트를 다시 실행 합니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | 스토리지 분석 카운터가 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | 스토리지 분석 하지 못했습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | 잘못 된 WAD 구성입니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | 예기치 않은 WAD 형식입니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | WAD 카운터를 찾을 수 없습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | 오래 된 WAD 카운터를 찾았습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | WAD 테이블을 읽을 수 없습니다. WAD 테이블에 대 한 연결이 없습니다. 다음과 같은 여러 원인이 있을 수 있습니다.<br /><br /> 1) 오래 된 구성 <br />2) Azure에 대 한 네트워크 연결 없음 <br />3) WAD 설치 문제 | [설치 스크립트 실행][deployment-guide-run-the-script]<br />[인터넷 연결 수정][deployment-guide-fix-internet-connection]<br />[지원 문의][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Perfmon NIC 메트릭이 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Perfmon 디스크 메트릭이 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | 일부 prefmon 메트릭이 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Perfmon에서 카운터를 만들지 못했습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | 구성 된 메트릭 공급자가 없습니다. | [지원 문의][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | 스토리지 분석 구성이 잘못 되었습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | 스토리지 분석 메트릭이 실패 했습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | 메트릭 공급자 중 하나가 실패 했습니다. | [설치 스크립트 실행][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | 공급자 스레드가 실패 했습니다. | [지원 문의][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>제공 된 솔루션에 대 한 자세한 지침

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>설치 스크립트 실행

이 가이드의 [SAP 용 Azure 확장 구성][deployment-guide-4.5] 장에서 설명 하는 단계에 따라 확장을 다시 설치 합니다. 일부 카운터를 프로 비전 하는 데 최대 30 분이 필요할 수 있습니다.

오류가 사라지지 않으면 [지원 담당자에 게 문의 하세요][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>지원에 문의

예기치 않은 오류 이거나 알려진 솔루션이 없습니다. C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) 또는/Var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) 폴더에 있는 AzureEnhancedMonitoring_service .log 파일을 수집 하 고 SAP 지원에 문의 하 여 추가 지원을 요청 합니다.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Sysprep 후 다시 배포

일반화 된 sysprep OS 이미지 (SAP 소프트웨어 포함)를 빌드할 계획인 경우이 이미지에 SAP 용 Azure 확장을 포함 하지 않는 것이 좋습니다. 일반화 된 OS 이미지의 새 인스턴스가 배포 된 후에 SAP 용 Azure 확장을 설치 해야 합니다.

그러나 일반화 된 및 sysprep OS 이미지에 이미 SAP 용 Azure 확장이 포함 되어 있는 경우 다음 해결 방법을 적용 하 여 새로 배포 된 VM 인스턴스에서 확장을 다시 구성할 수 있습니다.

* 새로 배포 된 VM 인스턴스에서 다음 폴더의 콘텐츠를 삭제 합니다.  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Runtimesettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \runtimesettings

* 이 가이드의 [SAP 용 Azure 확장 구성][deployment-guide-4.5] 장에서 설명 하는 단계에 따라 확장을 다시 설치 합니다.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>인터넷 연결 수정

SAP 용 Azure 확장을 실행 하는 Microsoft Azure 가상 머신에서 인터넷에 액세스 해야 합니다. 이 Azure VM이 Azure Virtual Network 또는 온-프레미스 도메인의 일부인 경우 관련 프록시 설정이 설정 되었는지 확인 합니다. 이러한 설정은 인터넷에 액세스할 LocalSystem 계정에 대해서도 유효해야 합니다. 이 가이드의 [프록시 구성][deployment-guide-configure-proxy] 챕터를 따르세요.

또한 Azure VM에 대 한 고정 IP 주소를 설정 해야 하는 경우 Azure VM 내에서 수동으로 설정 하지 말고 [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [Azure Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)를 사용 하 여 설정 합니다. 고정 IP는 Azure DHCP 서비스를 통해 전파 됩니다.

Azure VM 내에서 고정 IP 주소를 수동으로 설정 하는 것은 지원 되지 않으며 SAP 용 Azure 확장에 문제가 발생할 수 있습니다.

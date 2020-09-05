---
title: Azure NetApp Files (SMB)를 사용 하는 Windows의 SAP NW 용 Azure Vm HA | Microsoft Docs
description: SAP 응용 프로그램용 SMB (Azure NetApp Files)를 사용 하는 Windows의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: cd974377637f535383c4e099ac408bea88f887a4
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853105"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>SAP 응용 프로그램용 SMB (Azure NetApp Files)를 사용 하는 Windows의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)에서 [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 를 사용 하 여 가상 머신을 배포 하 고 구성 하 고, 클러스터 프레임 워크를 설치 하 고, Windows vm에 항상 사용 가능한 SAP NetWeaver 7.50 시스템을 설치 하는 방법을 설명 합니다.  

데이터베이스 레이어는 이 문서에서 자세히 다루지 않습니다. Azure [virtual network](../../../virtual-network/virtual-networks-overview.md) 가 이미 만들어져 있다고 가정 합니다.  

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp Files 설명서][anf-azure-doc] 
* SAP Note [1928533][1928533]에는 다음이 포함 됩니다.  
  * SAP 소프트웨어 배포에 지원 되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Windows의 Microsoft Azure에 필요한 SAP 커널 버전
* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) 에는 SMB 3.x 프로토콜의 SAP 지원 CA 기능에 대 한 필수 구성 요소가 나열 되어 있습니다.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) 에는 Windows 2012 및 2016에서 느리게 실행 되는 SAP transaction AL11에 대 한 문제 해결 정보가 포함 되어 있습니다.
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) 에는 SMB 3.0 프로토콜을 사용 하는 Windows Server에서 파일 공유의 투명 한 장애 조치 (failover) 기능에 대 한 정보가 있습니다.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) 에는 데이터 액세스 중 잘못 된 파일 시스템 성능/오류를 해결 하기 위한 권장 사항 (8.3 이름 생성 비활성화)이 있습니다.
* [Azure에서 SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치](./sap-high-availability-installation-wsfc-file-share.md) 
* [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오](./sap-high-availability-architecture-scenarios.md)
* [ASCS 클러스터 구성에서 프로브 포트 추가](sap-high-availability-installation-wsfc-file-share.md)
* [장애 조치 (Failover) 클러스터에 (A) SCS 인스턴스 설치](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)
* [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]

## <a name="overview"></a>개요

SAP는 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스를 클러스터링하기 위한 새로운 접근 방법을 개발했습니다. 이것은 클러스터 공유 디스크의 대안이 됩니다. 클러스터 공유 디스크를 사용 하는 대신 SMB 파일 공유를 사용 하 여 SAP 글로벌 호스트 파일을 배포할 수 있습니다. Azure NetApp Files은 Active Directory를 사용 하 여 NTFS ACL이 있는 SMBv3 (NFS와 함께)를 지원 합니다. Azure NetApp Files는 PaaS 서비스 이므로 자동으로 항상 사용 가능 합니다. 이러한 기능은 SAP global 용 SMB 파일 공유를 호스트 하는 데 적합 한 옵션 Azure NetApp Files 합니다.  
[AD (Azure Active Directory) 도메인 서비스](../../../active-directory-domain-services/overview.md) 와 [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 이 둘 다 지원 됩니다. Azure NetApp Files에서 기존 Active Directory 도메인 컨트롤러를 사용할 수 있습니다. 도메인 컨트롤러는 가상 머신으로 Azure에 있거나 Express 경로 또는 S2S VPN을 통해 온-프레미스에 있을 수 있습니다. 이 문서에서는 Azure VM에서 도메인 컨트롤러를 사용 합니다.  
SAP Netweaver 중앙 서비스의 HA(고가용성)를 위해서는 공유 스토리지가 필요합니다. 지금까지 Windows에서이를 위해 SOFS 클러스터를 빌드하거나 SIOS와 같은 클러스터 공유 디스크 s/w를 사용 해야 했습니다. 이제 Azure NetApp Files에 배포된 공유 스토리지를 사용하여 SAP Netweaver HA를 실현할 수 있습니다. 공유 저장소에 Azure NetApp Files를 사용 하면 SOFS 또는 SIOS가 필요 하지 않습니다.  

> [!NOTE]
> 파일 공유를 사용한 SAP ASCS/SCS 인스턴스의 클러스터링은 SAP 커널 7.49 이상이 적용된 SAP NetWeaver 7.40 이상에서 지원됩니다.  

![SMB 공유를 사용 하는 SAP ASCS/SCS HA 아키텍처](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 파일 공유에 대 한 필수 구성 요소는 다음과 같습니다.
* SMB 3.0 이상의 프로토콜
* 사용자 그룹 및 컴퓨터 $ computer 개체에 대 Active Directory 한 Acl (액세스 제어 목록)을 설정 하는 기능 Active Directory 합니다.
* 파일 공유는 HA를 사용 하도록 설정 해야 합니다.

이 참조 아키텍처에서 SAP Central services에 대 한 공유는 Azure NetApp Files에서 제공 됩니다.

![SMB 공유 세부 정보를 사용 하는 SAP ASCS/SCS HA 아키텍처](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 SMB 볼륨 만들기 및 탑재

Azure NetApp Files 사용 준비로 다음 단계를 수행 합니다.  

1. [Azure NetApp Files에 등록](../../../azure-netapp-files/azure-netapp-files-register.md) 하는 단계를 따르세요.  
2. [NetApp 계정 만들기](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) 에 설명 된 단계에 따라 Azure netapp 계정을 만듭니다.  
3. [용량 풀 설정의 지침](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) 에 따라 용량 풀을 설정 합니다.
4. Azure NetApp Files 리소스는 위임 된 서브넷에 상주해 야 합니다. 서브넷 위임의 지침에 따라 [Azure NetApp Files 하](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) 여 위임 된 서브넷을 만듭니다.  

   > [!IMPORTANT]
   > SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. [Active Directory 연결에 대 한 요구 사항을](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)검토 합니다.  

5. [Active Directory 연결 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) 에 설명 된 대로 Active Directory 연결을 만듭니다.  
6. Smb [볼륨 추가](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) 의 지침에 따라 smb Azure NetApp Files smb 볼륨을 만듭니다.  
7. Windows 가상 머신에 SMB 볼륨을 탑재 합니다.

> [!TIP]
> Azure NetApp Files 볼륨을 탑재 하는 방법에 대 한 지침을 찾을 수 있습니다. [Azure Portal](https://portal.azure.com/#home) 에서 Azure NetApp Files 개체로 이동 하는 경우 **볼륨** 블레이드를 클릭 한 다음 **탑재 지침**을 클릭 합니다.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Windows 장애 조치 (failover) 클러스터를 사용 하 여 SAP HA에 대 한 인프라 준비 

1. [Azure 내부 부하 분산 장치에 대 한 ASCS/SCS 부하 분산 규칙을 설정](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716)합니다.
2. [도메인에 Windows 가상 컴퓨터를 추가](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c)합니다.
3. [SAP ASCS/SCS 인스턴스의 두 클러스터 노드에 대 한 레지스트리 항목 추가](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [SAP ASCS/SCS 인스턴스에 대 한 Windows Server 장애 조치 (failover) 클러스터 설정](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Windows Server 2016를 사용 하는 경우 [Azure 클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness)를 구성 하는 것이 좋습니다.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>두 노드에 SAP ASCS 인스턴스 설치

SAP에서 다음 소프트웨어가 필요 합니다.
   * SAP 소프트웨어 프로 비전 관리자 (SWPM) 설치 도구 버전 SPS25 이상
   * SAP Kernel 7.49 이상
   * 클러스터형 [SAP ASCS/scs 인스턴스의 가상 호스트 이름 만들기](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097)에 설명 된 대로 클러스터형 SAP ASCS/scs 인스턴스의 가상 호스트 이름 (클러스터 네트워크 이름)을 만듭니다.

> [!NOTE]
> 파일 공유를 사용한 SAP ASCS/SCS 인스턴스의 클러스터링은 SAP 커널 7.49 이상이 적용된 SAP NetWeaver 7.40 이상에서 지원됩니다.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>첫 번째 ASCS/SCS 클러스터 노드에 ASCS/SCS 인스턴스를 설치 합니다.

1. 첫 번째 클러스터 노드에 SAP ASCS/SCS 인스턴스를 설치 합니다. SAP swpm 설치 도구를 시작한 다음 **Product**  >  **DBMS** > 설치 > 응용 프로그램 서버 abap (또는 Java) > 고가용성 시스템 > ascs/SCS 인스턴스 > 첫 번째 클러스터 노드로 이동 합니다.  

2. SWPM에서 클러스터 공유 구성으로 **파일 공유 클러스터** 를 선택 합니다.  
3. **SAP 시스템 클러스터 매개 변수**단계에서 메시지가 표시 되 면 **파일 공유 호스트 이름**으로 이미 만든 Azure NetApp Files SMB 공유의 호스트 이름을 입력 합니다.  이 예제에서 SMB 공유 호스트 이름은 **anfsmb-9562**입니다. 

   > [!IMPORTANT]
   > SWPM에서 필수 구성 요소 검사기의 결과로 지속적인 가용성 기능 조건이 충족 되지 않으면 [Windows에 더 이상 존재 하지 않는 공유 폴더에 액세스 하려고 할 때 지연 된 오류 메시지](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)의 지침에 따라 해결할 수 있습니다.  

   > [!TIP]
   > SWPM에서 필수 구성 요소 검사기의 결과로 교환 크기 조건이 충족 되지 않는 것으로 표시 되는 경우 내 컴퓨터>시스템 속성>성능 설정> 고급> 가상 메모리> 변경으로 이동 하 여 스왑 크기를 조정할 수 있습니다.  

4. PowerShell을 사용 하 여 SAP 클러스터 리소스, 프로브 포트를 구성 합니다 `SAP-SID-IP` . [프로브 포트 구성](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)에 설명 된 대로 SAP ASCS/SCS 클러스터 노드 중 하나에서이 구성을 실행 합니다.

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>두 번째 ASCS/SCS 클러스터 노드에 ASCS/SCS 인스턴스를 설치 합니다.

1. 두 번째 클러스터 노드에 SAP ASCS/SCS 인스턴스를 설치 합니다. SAP swpm 설치 도구를 시작한 다음 **Product**  >  **DBMS** > 설치 > 응용 프로그램 서버 abap (또는 Java) > 고가용성 시스템 > ascs/SCS 인스턴스 > 추가 클러스터 노드로 이동 합니다.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>DBMS 인스턴스 및 SAP 애플리케이션 서버 설치

다음을 설치 하 여 SAP 설치를 완료 합니다.

   * DBMS 인스턴스  
   * 기본 SAP 응용 프로그램 서버  
   * 추가 SAP 응용 프로그램 서버  

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS 인스턴스 장애 조치 (failover) 테스트 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>클러스터 노드 A에서 클러스터 노드 B로 장애 조치 (failover)
이 테스트 시나리오에서는 노드 A로 클러스터 노드 sapascs1를 참조 하 고 노드 B로 노드 sapascs2를 클러스터링 합니다.

1. 클러스터 리소스가 노드 A에서 실행 되 고 있는지 확인 합니다. ![ 그림 1: 장애 조치 (failover) 테스트 이전에 노드 A에서 실행 되는 Windows Server 장애 조치 (failover) 클러스터 리소스](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 클러스터 노드 A를 다시 시작 합니다. SAP 클러스터 리소스가 클러스터 노드 B로 이동 됩니다. ![ 그림 2: 장애 조치 (failover) 테스트 후 노드 B에서 실행 되는 Windows Server 장애 조치 (failover) 클러스터 리소스](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>잠금 항목 테스트

1. SAP 큐에서 복제 서버 (ERS)가 활성화 되어 있는지 확인 합니다.  
2. SAP 시스템에 로그온 하 고 transaction SU01을 실행 한 다음 변경 모드에서 사용자 ID를 엽니다. 그러면 SAP 잠금 항목이 생성 됩니다.  
3. SAP 시스템에 로그인 했을 때 transaction ST12로 이동 하 여 잠금 항목을 표시 합니다.  
4. 클러스터 노드 A에서 클러스터 노드 B로 ASCS 리소스를 장애 조치 (failover) 합니다.  
5. SAP ASCS/SCS 클러스터 리소스 장애 조치 (failover) 전에 생성 된 잠금 항목이 유지 되는지 확인 합니다.  

![그림 3: 장애 조치 (failover) 테스트 후 잠금 항목이 유지 됨](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

자세한 내용은 [ASCS에서 ERS로 큐에 대기 장애 조치 (Failover)](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS) 를 참조 하세요.
## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* 고가용성을 설정 하 고 SAP의 재해 복구를 계획 하는 방법을 알아보려면 
* HANA on Azure (큰 인스턴스)에 대 한 자세한 내용은 [azure의 SAP HANA (큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조 하세요.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.

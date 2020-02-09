---
title: Azure NetApp Files를 사용 하는 SUSE Linux Enterprise Server에서 SAP NetWeaver에 대 한 Azure Virtual Machines 고가용성 Microsoft Docs
description: Sap 응용 프로그램용 Azure NetApp Files를 사용 하는 SUSE Linux Enterprise Server의 SAP NetWeaver에 대 한 고가용성 가이드
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
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: 1a413ce55604ef8b5c3219e8de466fcc23d41bac
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990944"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>SAP 응용 프로그램용 Azure NetApp Files를 사용 하 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성

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

이 문서에서는 [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)를 사용 하 여 가상 머신을 배포 하 고, 가상 머신을 구성 하 고, 클러스터 프레임 워크를 설치 하 고, 항상 사용 가능한 SAP NetWeaver 7.50 시스템을 설치 하는 방법을 설명 합니다.
예제 구성, 설치 명령 등의 ASCS 인스턴스는 숫자 00, ERS 인스턴스 번호 01, 기본 응용 프로그램 인스턴스 (PAS)는 02 이며 응용 프로그램 인스턴스 (.AAS)는 03입니다. SAP 시스템 ID QAS 사용. 

이 문서에서는 Azure NetApp Files를 사용 하 여 SAP NetWeaver 응용 프로그램에 대 한 고가용성을 구현 하는 방법을 설명 합니다. 데이터베이스 계층은이 문서에 자세히 설명 되어 있지 않습니다.

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp Files 설명서][anf-azure-doc] 
* SAP Note [1928533][1928533], 다음 항목을 포함합니다.  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917][2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799][1944799]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498][2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692][2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787][1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* SAP Community WIKI] (https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 에는 Linux에 필요한 모든 SAP 노트가 있습니다.
* [Linux에서 SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드에는 온-프레미스에서 Netweaver HA 및 SAP HANA 시스템 복제를 설정 하는 데 필요한 모든 정보가 포함 되어 있습니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE 고가용성 확장 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]
* [Azure NetApp Files를 사용 하 여 Microsoft Azure에서 NetApp SAP 응용 프로그램][anf-sap-applications-azure]

## <a name="overview"></a>개요

SAP Netweaver central services에 대 한 HA (고가용성)에는 공유 저장소가 필요 합니다.
지금까지 SUSE Linux에서이를 위해 항상 사용 가능한 별도의 NFS 클러스터를 구축 해야 했습니다. 

이제 Azure NetApp Files에 배포 된 공유 저장소를 사용 하 여 SAP Netweaver HA를 달성할 수 있습니다. 공유 저장소에 Azure NetApp Files를 사용 하면 추가 [NFS 클러스터가](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)필요 하지 않습니다. Pacemaker는 SAP Netweaver central 서비스 (ASCS/SCS)의 HA에 계속 필요 합니다.


![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에서 [부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 는 가상 IP 주소를 사용 하는 데 필요 합니다. [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)를 사용 하는 것이 좋습니다. 아래 목록에서는(A)SCS 및 ERS 부하 분산 장치에 대한 구성이 나와 있습니다.

> [!IMPORTANT]
> Azure Vm에서 게스트 운영 체제로 SUSE Linux를 사용 하는 SAP ASCS/ERS의 다중 SID 클러스터링은 **지원 되지 않습니다**. 다중 SID 클러스터링은 하나의 Pacemaker 클러스터에서 서로 다른 Sid를 사용 하 여 여러 SAP ASCS/ERS 인스턴스를 설치 하는 방법을 설명 합니다.


### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * IP 주소 10.1.1.20
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>
* 부하 분산 규칙
  * 표준 Load Balancer 사용 하는 경우 **HA 포트** 를 선택 합니다.
  * 기본 Load Balancer 사용 하는 경우 다음 포트에 대 한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * IP 주소 10.1.1.21
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 621<strong>&lt;nr&gt;</strong>
* 부하 분산 규칙
  * 표준 Load Balancer 사용 하는 경우 **HA 포트** 를 선택 합니다.
  * 기본 Load Balancer 사용 하는 경우 다음 포트에 대 한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files 인프라 설정 

SAP NetWeaver에는 전송 및 프로필 디렉터리에 대한 공유 스토리지가 필요합니다.  Azure NetApp 파일 인프라의 설치를 계속 하기 전에 [Azure NetApp Files 설명서][anf-azure-doc]를 숙지 하세요. 선택한 Azure 지역에서 Azure NetApp Files를 제공 하는지 확인 합니다. 다음 링크는 azure 지역에서 Azure NetApp Files의 가용성을 보여 줍니다. [Azure 지역별 Azure NetApp Files 가용성][anf-avail-matrix].

Azure NetApp 파일은 여러 [azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용할 수 있습니다. Azure NetApp Files를 배포 하기 전에 [Azure NetApp 파일 등록 지침][anf-register]에 따라 Azure NetApp Files에 등록을 요청 합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포  

이 단계에서는 이미 [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 배포 했다고 가정 합니다. Azure NetApp Files 리소스가 탑재 되는 Azure NetApp Files 리소스 및 Vm은 동일한 Azure Virtual Network 또는 피어 링 Azure Virtual Networks에 배포 되어야 합니다.  

1. 아직 수행 하지 않은 경우 [Azure NetApp Files에 대 한 온 보 딩](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)을 요청 합니다.  

2. [Netapp 계정 만들기 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)에 따라 선택한 Azure 지역에서 netapp 계정을 만듭니다.  
3. 용량 풀을 [Azure NetApp Files 설정 하는 방법에 대 한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)에 따라 Azure NetApp Files 용량 풀을 설정 합니다.  
이 문서에 제공 된 SAP Netweaver 아키텍처는 단일 Azure NetApp Files 용량 풀, 프리미엄 SKU를 사용 합니다. Azure에서 SAP Netweaver 응용 프로그램 워크 로드에 대 한 Premium SKU를 Azure NetApp Files 하는 것이 좋습니다.  

4. [Azure NetApp Files에 서브넷 위임 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)에 설명 된 대로 Azure netapp 파일에 서브넷을 위임 합니다.  

5. [Azure NetApp Files 볼륨을 만들기 위한 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)에 따라 Azure NetApp Files 볼륨을 배포 합니다. 지정 된 Azure NetApp Files [서브넷](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)에 볼륨을 배포 합니다. Azure NetApp Files 리소스와 Azure Vm은 동일한 Azure Virtual Network 또는 피어 링 Azure Virtual Network에 있어야 합니다. 예를 들어 sapmnt<b>QAS</b>, usrsap<b>QAS</b>등은 볼륨 이름 및 sapmnt<b>QAS</b>, usrsap<b>QAS</b>등은 Azure NetApp Files 볼륨의 filepaths입니다.  

   1. volume sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>QAS</b>)
   2. volume usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>QAS</b>)
   3. 볼륨 usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>QAS</b>sys)
   4. volume usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>QAS</b>ers)
   5. 볼륨 트랜잭션 (nfs://10.1.0.4/trans)
   6. usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>QAS</b>pas)
   7. usrsap<b>QAS</b>.aas (nfs://10.1.0.4/usrsap<b>QAS</b>.aas)
   
이 예제에서는 Azure NetApp Files를 사용 하는 방법을 보여 주기 위해 모든 SAP Netweaver 파일 시스템에 대 한 Azure NetApp Files를 사용 했습니다. NFS를 통해 탑재 하지 않아도 되는 SAP 파일 시스템을 [Azure disk storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) 로 배포할 수도 있습니다. 이 예제에서 <b>a-e는</b> Azure NetApp Files에 있어야 하 고 <b>f-g</b> (즉,/usr/sap/<b></b><b>qas</b>/d<b>03</b><b>)를 Azure</b>disk storage로 배포할 수 있습니다. 

### <a name="important-considerations"></a>중요 고려 사항

SAP Netweaver on SUSE 고가용성 아키텍처에 대 한 Azure NetApp Files 고려 하는 경우 다음과 같은 중요 한 사항을 고려해 야 합니다.

- 최소 용량 풀은 4 TiB입니다. 용량 풀 크기는 1 TiB 증가할 수 있습니다.
- 최소 볼륨은 100 GiB
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재 되는 모든 가상 머신은 동일한 Azure Virtual Network 또는 동일한 지역의 [피어 링 가상 네트워크](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 에 있어야 합니다. 동일한 지역에서 VNET 피어 링을 통한 Azure NetApp Files 액세스는 이제 지원 됩니다. 전역 피어 링을 통한 Azure NetApp 액세스는 아직 지원 되지 않습니다.
- 선택한 가상 네트워크에는 Azure NetApp Files으로 위임 된 서브넷이 있어야 합니다.
- Azure NetApp Files에서 [내보내기 정책을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)제공 합니다. 허용 되는 클라이언트, 액세스 유형 (읽기 & 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능이 아직 영역을 인식 하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포 되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다. 
- Azure NetApp Files 볼륨을 NFSv3 또는 NFSv 4.1 볼륨으로 배포할 수 있습니다. SAP 응용 프로그램 계층 (ASCS/ERS, SAP 응용 프로그램 서버)에 대해 두 프로토콜을 모두 사용할 수 있습니다. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Azure Portal를 통해 수동으로 Linux Vm 배포

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. Vm을 배포 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 리소스 그룹 만들기
1. Virtual Network 만들기
1. ASCS에 대 한 가용성 집합 만들기  
   최대 업데이트 도메인 설정
1. Virtual Machine 1 만들기  
   SLES4SAP 12 SP3 이상을 사용 합니다 .이 예제에서는 SLES4SAP 12 SP3 이미지가 사용 됩니다.  
   ASCS에 대해 이전에 만든 가용성 집합 선택  
1. Virtual Machine 2 만들기  
   SLES4SAP 12 SP3 이상을 사용 합니다 .이 예제에서는 SLES4SAP 12 SP3 이미지가 사용 됩니다.  
   ASCS에 대해 이전에 만든 가용성 집합 선택  
1. SAP 응용 프로그램 인스턴스에 대 한 가용성 집합 만들기 (PAS, .AAS)    
   최대 업데이트 도메인 설정
1. 가상 컴퓨터 만들기 3  
   SLES4SAP 12 SP3 이상을 사용 합니다 .이 예제에서는 SLES4SAP 12 SP3 이미지가 사용 됩니다.  
   PAS/.AAS에 대해 이전에 만든 가용성 집합 선택   
1. 가상 컴퓨터 4 만들기  
   SLES4SAP 12 SP3 이상을 사용 합니다 .이 예제에서는 SLES4SAP 12 SP3 이미지가 사용 됩니다.  
   PAS/.AAS에 대해 이전에 만든 가용성 집합 선택  

## <a name="disable-id-mapping-if-using-nfsv41"></a>ID 매핑 사용 안 함 (NFSv 4.1을 사용 하는 경우)

NFSv 4.1 프로토콜을 사용 하 Azure NetApp Files 볼륨을 사용 하는 경우에만이 섹션의 지침을 적용할 수 있습니다. Azure NetApp Files NFSv 4.1 볼륨이 탑재 될 모든 Vm에서 구성을 수행 합니다.  

1. NFS 도메인 설정을 확인 합니다. 도메인이 기본 Azure NetApp Files 도메인으로 구성 되어 있는지 확인 합니다. 즉, **`defaultv4iddomain.com`** 이며 매핑이 없음으로 설정 되어 있는지 확인 **합니다.**  

    > [!IMPORTANT]
    > VM에서 `/etc/idmapd.conf`의 NFS 도메인을 Azure NetApp Files: **`defaultv4iddomain.com`** 의 기본 도메인 구성과 일치 하도록 설정 해야 합니다. NFS 클라이언트의 도메인 구성 (예: VM)과 NFS 서버 (예: Azure NetApp 구성)가 일치 하지 않는 경우 Vm에 탑재 된 Azure NetApp 볼륨의 파일에 대 한 권한이 `nobody`표시 됩니다.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`확인 합니다. **Y**로 설정 되어야 합니다. `nfs4_disable_idmapping` 있는 디렉터리 구조를 만들려면 mount 명령을 실행 합니다. 액세스는 커널/드라이버용으로 예약 되어 있기 때문에/dv/sd 아래에 있는 디렉터리는 수동으로 만들 수 없습니다.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>(A)SCS 설정

이 예제에서는 [Azure Portal](https://portal.azure.com/#home) 를 통해 리소스를 수동으로 배포 했습니다.

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Azure Portal를 통해 Azure Load Balancer 수동으로 배포

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. Vm을 배포 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 부하 분산 장치 만들기 (내부, 표준):  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS에 대 한 IP 주소 10.1.1.20
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예: **프런트 엔드). QAS. ASCS**)
         1. 할당을 정적으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.1.1.20**).
         1. 확인 클릭
      1. ASCS ERS에 대 한 IP 주소 10.1.1.21
         * "A"에서 위의 단계를 반복 하 여 **10.1.1.21** 및 프런트 엔드와 같은 사람에 대 한 IP 주소를 만듭니다 **. QAS. ERS**)
   1. 백 엔드 풀 만들기
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
         1. 새 백 엔드 풀의 이름을 입력 합니다 (예: **백 엔드). QAS**)
         1. 가상 머신 추가 클릭
         1. 가상 컴퓨터 선택
         1. (A) SCS 클러스터의 가상 머신 및 해당 IP 주소를 선택 합니다.
         1. 추가를 클릭합니다.
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브 (예: 상태)의 이름을 입력 합니다 **. QAS. ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS에 대 한 포트 621**01**
            * "C"에서 위의 단계를 반복 하 여 해당 ERS에 대 한 상태 프로브를 만듭니다 (예: 621**01** 및 **health). QAS. ERS**)
   1. 부하 분산 규칙
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고 부하 분산 규칙을 선택한 다음 추가를 클릭 합니다.
         1. 새 부하 분산 장치 규칙의 이름 (예: lb)을 입력 합니다 **. QAS. ASCS**)
         1. 이전에 만든 ASCS, 백 엔드 풀 및 상태 프로브에 대 한 프런트 엔드 IP 주소를 선택 합니다 (예: **프런트 엔드). QAS. ASCS**, **백 엔드. QAS** 및 **health. QAS. ASCS**)
         1. **HA 포트** 선택
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
         * 위의 단계를 반복 하 여 ERS에 대 한 부하 분산 규칙을 만듭니다 (예: **lb. QAS. ERS**)
1. 또는 시나리오에 기본 부하 분산 장치 (내부)가 필요한 경우 다음 단계를 수행 합니다.  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS에 대 한 IP 주소 10.1.1.20
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예: **프런트 엔드). QAS. ASCS**)
         1. 할당을 정적으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.1.1.20**).
         1. 확인 클릭
      1. ASCS ERS에 대 한 IP 주소 10.1.1.21
         * "A"에서 위의 단계를 반복 하 여 **10.1.1.21** 및 프런트 엔드와 같은 사람에 대 한 IP 주소를 만듭니다 **. QAS. ERS**)
   1. 백 엔드 풀 만들기
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
         1. 새 백 엔드 풀의 이름을 입력 합니다 (예: **백 엔드). QAS**)
         1. 가상 머신 추가 클릭
         1. ASCS에 대해 이전에 만든 가용성 집합을 선택 합니다. 
         1. (A)SCS 클러스터의 가상 머신 선택
         1. 확인 클릭
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브 (예: 상태)의 이름을 입력 합니다 **. QAS. ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS에 대 한 포트 621**01**
            * "C"에서 위의 단계를 반복 하 여 해당 ERS에 대 한 상태 프로브를 만듭니다 (예: 621**01** 및 **health). QAS. ERS**)
   1. 부하 분산 규칙
      1. TCP: 32**00**(ASCS용)
         1. 부하 분산 장치를 열고 부하 분산 규칙을 선택한 다음 추가를 클릭 합니다.
         1. 새 부하 분산 장치 규칙의 이름 (예: lb)을 입력 합니다 **. QAS. ASCS. 3200**)
         1. 이전에 만든 ASCS, 백 엔드 풀 및 상태 프로브에 대 한 프런트 엔드 IP 주소를 선택 합니다 (예: **프런트 엔드). QAS. ASCS**)
         1. 프로토콜로 **TCP**를 유지하고. 포트로 **3200** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. ASCS에 대한 추가 포트
         * "D"에서 위의 단계를 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 및 TCP (ascs)에 대해 반복 합니다.
      1. ASCS ERS에 대한 추가 포트
         * "D"에서 위의 단계를 33**01**, 5**01**13, 5**01**14, 5**01**16 및 TCP (ascs ERS)에 대 한 TCP로 반복 합니다.

      > [!Note]
      > 공용 IP 주소가 없는 Vm이 내부 (공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치 되는 경우 공용 엔드포인트으로의 라우팅을 허용 하기 위해 추가 구성을 수행 하지 않는 한 아웃 바운드 인터넷 연결이 없습니다. 아웃 바운드 연결을 설정 하는 방법에 대 한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용 하 여 Virtual Machines에 대 한 공용 엔드포인트 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)을 참조 하세요.  

      > [!IMPORTANT]
      > Azure Load Balancer 뒤에 배치 되는 Azure Vm에서 TCP 타임 스탬프를 사용 하도록 설정 하지 마세요. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브가 실패 합니다. **Tcp_timestamps** 매개 변수를 **0**으로 설정 합니다. 자세한 내용은 [Load Balancer 상태 프로브](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)를 참조 하세요.

### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 단계에 따라 이 (A)SCS 서버에 대한 기본 Pacemaker 클러스터를 만듭니다.

### <a name="installation"></a>설치

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** SUSE 커넥터 설치

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > 호스트 이름에 대시를 사용 하 **는 경우 알려진**문제는 **3.1.1** 패키지의 버전을 사용 하 여 수정 됩니다. 호스트 이름에 대시를 사용 하 여 클러스터 노드를 사용 하는 경우 최소 버전의 3.1.1 패키지를 사용 하 고 있는지 확인 합니다. 대시를 사용하면 클러스터가 작동하지 않습니다. 

   SAP SUSE 클러스터 커넥터의 새 버전을 설치했는지 확인합니다. 기존 버전은 sap_suse_cluster_connector라고 하며 새 버전은 **sap suse-cluster-connector**라고 합니다.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** SAP 리소스 에이전트 업데이트  
   
   이 문서에서 설명하는 새 구성을 사용하려면 리소스 에이전트 패키지용 패치가 필요합니다. 다음 명령을 사용하여 패치가 이미 설치되었는지를 확인할 수 있습니다.

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   다음과 같은 출력이 표시되어야 합니다.

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   grep 명령을 실행하여 IS_ERS 매개 변수를 찾을 수 없는 경우에는 [SUSE 다운로드 페이지](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)에 나와 있는 패치를 설치해야 합니다.

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

1. **[A]** 공유 디렉터리 만들기

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** `autofs` 구성

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   NFSv3를 사용 하는 경우 다음을 사용 하 여 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   NFSv 4.1을 사용 하는 경우 다음을 사용 하 여 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > 볼륨을 탑재할 때 Azure NetApp Files 볼륨의 NFS 프로토콜 버전과 일치 하는지 확인 합니다. Azure NetApp Files 볼륨이 NFSv3 볼륨으로 만들어지면 해당 NFSv3 구성을 사용 합니다. Azure NetApp Files 볼륨이 NFSv 4.1 볼륨으로 생성 되는 경우 지침에 따라 ID 매핑을 사용 하지 않도록 설정 하 고 해당 NFSv 4.1 구성을 사용 해야 합니다. 이 예제에서는 Azure NetApp Files 볼륨이 NFSv3 볼륨으로 생성 되었습니다.  
   
   `autofs` 다시 시작 하 여 새 공유를 탑재 합니다.
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** 스왑 파일 구성

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

   <pre><code>sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS 설치

1. **[1]** ASCS 인스턴스에 대한 가상 IP 리소스 및 상태 프로브 만들기

   > [!IMPORTANT]
   > 최신 테스트로 인해 netcat이 백로그로 인 한 요청 응답을 중지 하 고 하나의 연결만 처리할 수 있는 경우를 확인할 수 있습니다. Netcat 리소스는 Azure 부하 분산 장치 요청에 대 한 수신 대기를 중지 하 고 부동 IP는 사용할 수 없게 됩니다.  
   > 기존 Pacemaker 클러스터의 경우 [Azure 부하 분산 장치 검색 강화](https://www.suse.com/support/kb/doc/?id=7024128)의 지침에 따라 netcat을 socat로 바꾸는 것이 좋습니다. 변경 작업을 수행 하려면 짧은 가동 중지 시간이 필요 합니다.  

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:620<b>00</b>,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소 (예: <b>anftstsapvh</b>, <b>10.1.1.20</b> 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호 (예: <b>00</b>)에 매핑되는 가상 호스트 이름을 사용 하 여 첫 번째 노드에 SAP NetWeaver ascs를 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME를 사용 하 여 가상 호스트 이름을 사용 하 여 SAP를 설치할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   설치에서/usr/sap/**Qas**/ASCS**00**으로 하위 폴더를 만들지 못하면 ascs**00** 폴더의 소유자와 그룹을 설정 하 고 다시 시도 하세요. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** ERS 인스턴스에 대한 가상 IP 리소스 및 상태 검사 프로브 만들기

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1'\
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:621<b>01</b>,backlog=10,fork,reuseaddr /dev/null" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** SAP NetWeaver ERS 설치

   사용자에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름 (예: <b>anftstsapers</b>, <b>10.1.1.21</b> 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호 (예: <b>01</b>)를 사용 하 여 두 번째 노드에 SAP NetWeaver ERS를 루트로 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME를 사용 하 여 가상 호스트 이름을 사용 하 여 SAP를 설치할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   설치에서/usr/sap/**Qas**/ers**01**에 하위 폴더를 만들지 못하면 ers**01** 폴더의 소유자와 그룹을 설정 하 고 다시 시도 하세요.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** ASCS/SCS 및 ERS 인스턴스 프로필 조정
 
   * ASCS/SCS 프로필

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS 프로필

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** 연결 유지 구성

   SAP NetWeaver 애플리케이션 서버와 ASCS/SCS 간의 통신은 소프트웨어 부하 분산 장치를 통해 라우팅됩니다. 부하 분산 장치는 구성 가능한 시간 제한이 지나면 비활성 연결을 끊습니다. 이 연결 끊김을 방지하려면 SAP NetWeaver ASCS/SCS 프로필에서 매개 변수를 설정하고 Linux 시스템 설정을 변경해야 합니다. 자세한 내용은 [SAP Note 1410736][1410736] 을 참조 하세요.

   ASCS/SCS profile 매개 변수 enque/encni/set_so_keepalive는 마지막 단계에서 이미 추가된 상태입니다.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** 설치 후 SAP 사용자 구성

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** `sapservice` 파일에 ascs 및 ERS SAP services 추가

   ASCS 서비스 항목을 두 번째 노드에 추가하고 ERS 서비스 항목을 첫 번째 노드에 복사합니다.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** SAP 클러스터 리소스 만들기

ENSA1 (큐에 넣기 서버 1 아키텍처)를 사용 하는 경우 다음과 같이 리소스를 정의 합니다.

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Sap는 복제를 비롯 하 여 SAP NW 7.52에 대 한 지원 서비스를 도입 했습니다. ABAP Platform 1809부터 시작 하 여 큐에 넣기 서버 2가 기본적으로 설치 됩니다. 큐에 넣기 서버 2 지원에 대해서는 SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) 을 참조 하세요.
[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(큐에 넣기 서버 2 아키텍처)를 사용 하는 경우 다음과 같이 리소스를 정의 합니다.

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   이전 버전에서 업그레이드 하 고 큐에 넣기 서버 2로 전환 하는 경우 SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)을 참조 하세요. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a> SAP NetWeaver 애플리케이션 서버 준비 

일부 데이터베이스에서는 데이터베이스 인스턴스 설치가 애플리케이션 서버에서 실행되어야 합니다. 이러한 경우 애플리케이션 서버 가상 머신을 사용할 수 있도록 준비합니다.

아래 단계에서는 ASCS/SCS 및 HANA 서버와 다른 서버에 애플리케이션 서버를 설치한다고 가정합니다. 그 외의 경우에는 호스트 이름 확인을 구성하는 단계 등 아래의 일부 단계를 수행할 필요가 없습니다.

다음 항목에는 **[A]** -PAS와 .aas 모두에 적용 됩니다. **[P]** -pas 또는 **[S]** 에만 적용 되며, .aas에만 적용 됩니다.


1. **[A]** 운영 체제 구성

   더티 캐시의 크기를 줄입니다. 자세한 내용은 [큰 RAM이 있는 SLES 11/12 서버의 쓰기 성능 저하](https://www.suse.com/support/kb/doc/?id=7010287)를 참조하세요.

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

   ```bash
   sudo vi /etc/hosts
   ```

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** sapmnt 디렉터리를 만듭니다.

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** PAS 디렉터리 만들기

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** .aas 디렉터리를 만듭니다.

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** PAS에서 `autofs` 구성

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   NFSv3를 사용 하는 경우 다음을 사용 하 여 새 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/usrsap<b>qas</b>pas
   </code></pre>

   NFSv 4.1을 사용 하는 경우 다음을 사용 하 여 새 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.5</b>:/usrsap<b>qas</b>pas
   </code></pre>

   `autofs` 다시 시작 하 여 새 공유를 탑재 합니다.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** .aas에서 `autofs` 구성

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   NFSv3를 사용 하는 경우 다음을 사용 하 여 새 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   NFSv 4.1을 사용 하는 경우 다음을 사용 하 여 새 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   `autofs` 다시 시작 하 여 새 공유를 탑재 합니다.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** 스왑 파일 구성

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>데이터베이스 설치

이 예제에서는 SAP NetWeaver가 SAP HANA에 설치됩니다. 이 설치에 지원되는 모든 데이터베이스를 사용할 수 있습니다. Azure에 SAP HANA를 설치 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha]을 참조 하세요. 지원 되는 데이터베이스 목록은 [SAP Note 1928533][1928533]을 참조 하세요.

* SAP 데이터베이스 인스턴스 설치 실행

   데이터베이스에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 루트로 SAP NetWeaver 데이터베이스 인스턴스를 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 애플리케이션 서버 설치

다음 단계에 따라 SAP 애플리케이션 서버를 설치합니다.

1. **[A]** 응용 프로그램 서버 준비 위의 [SAP NetWeaver 응용 프로그램 서버 준비](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) 챕터에 있는 단계에 따라 응용 프로그램 서버를 준비 합니다.

2. **[A]** sap NetWeaver 응용 프로그램 서버 설치 기본 또는 추가 sap NetWeaver 응용 프로그램 서버를 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** 업데이트 SAP HANA 보안 저장소

   설치한 SAP HANA System Replication의 가상 이름을 가리키도록 SAP HANA 보안 저장소를 업데이트합니다.

   다음 명령을 실행하여 항목을 나열합니다.
   <pre><code>
   hdbuserstore List
   </code></pre>

   이 경우 모든 항목을 나열해야 하며 다음과 비슷합니다.
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   출력은 기본 항목의 IP 주소가 부하 분산 장치의 IP 주소가 아니라 가상 머신을 가리키고 있음을 나타냅니다. 이 항목은 부하 분산 장치의 가상 호스트 이름을 가리키도록 변경해야 합니다. 위의 출력에서와 동일한 포트 (위의 출력에**30313** )와 데이터베이스 이름 (**qas** )을 사용 해야 합니다.

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

다음 테스트는 [SUSE의 모범 사례 가이드][suse-ha-guide]에 있는 테스트 사례의 복사본입니다. 이 테스트는 작업자 편의를 위해 복사되었습니다. 또한 항상 모범 사례 가이드를 읽고 추가되었을 수 있는 모든 추가 테스트를 수행해야 합니다.

1. 테스트 HAGetFailoverConfig, HACheckConfig 및 HACheckFailoverConfig

   ASCS 인스턴스를 현재 실행 중인 노드에서 \<sapsid>adm으로 다음 명령을 실행합니다. 이러한 명령이 “실패: 메모리 부족”을 나타내며 실패할 경우 호스트 이름의 대시 때문일 수 있습니다. 이것은 알려진 문제로, SUSE에서는 sap-suse-cluster-connector 패키지에서 이 문제를 수정할 예정입니다.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. 수동으로 ASCS 인스턴스 마이그레이션

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   루트 권한으로 다음 명령을 실행하여 ASCS 인스턴스를 마이그레이션합니다.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. HAFailoverToNode 테스트

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   \<sapsid>adm으로 다음 명령을 실행하여 ASCS 인스턴스를 마이그레이션합니다.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. 노드 작동 중단 시뮬레이트 

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   ASCS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행합니다.

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   SBD를 사용하는 경우 종료된 노드에서 Pacemaker가 자동으로 시작되지 않습니다. 노드가 다시 시작된 후의 상태는 다음과 같습니다.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   다음 명령을 사용하여 종료된 노드에서 Pacemaker를 시작하고, SBD 메시지를 정리하고, 실패한 리소스를 정리합니다.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. ASCS 인스턴스의 수동 다시 시작 테스트

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   예를 들어 트랜잭션 su01에서 사용자를 편집하여 큐에 넣기 잠금을 만듭니다. ASCS 인스턴스가 실행 되는 노드에서 < sapsid\>adm으로 다음 명령을 실행 합니다. 이러한 명령은 ASCS 인스턴스를 중지했다가 다시 시작합니다. 큐에 넣기 서버 1 아키텍처를 사용 하는 경우이 테스트에서 큐에 넣기 잠금이 손실 될 것으로 예상 됩니다. 큐에 넣기 서버 2 아키텍처를 사용 하는 경우 큐에 대기 됩니다. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS 인스턴스는 이제 Pacemaker에서 사용되지 않도록 설정됩니다.

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   동일한 노드에서 ASCS 인스턴스를 다시 시작합니다.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   큐에 넣기 서버 복제 1 아키텍처를 사용 하 고 백 엔드를 다시 설정 해야 하는 경우 트랜잭션 su01의 큐에 넣기 잠금이 손실 됩니다. 테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. 메시지 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   루트 권한으로 다음 명령을 실행하여 메시지 서버 프로세스를 확인한 후 종료합니다.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   메시지 서버를 한 번만 종료 하면 `sapstart`의해 다시 시작 됩니다. 충분히 자주 종료하면 Pacemaker는 ASCS 인스턴스를 다른 노드로 이동합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. 큐에 넣기 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   ASCS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행하여 큐에 넣기 서버를 종료합니다.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS 인스턴스는 즉시 다른 노드로 장애 조치(Failover)되어야 합니다. 또한 ASCS 인스턴스가 시작된 후 ERS 인스턴스도 장애 조치(Failover)되어야 합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. 큐에 넣기 복제 서버 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   ERS 인스턴스를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행하여 큐에 넣기 복제 서버 프로세스를 종료합니다.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   명령을 한 번만 실행 하는 경우 `sapstart` 프로세스를 다시 시작 합니다. 자주 실행 하는 경우에는 `sapstart` 프로세스를 다시 시작 하지 않고 리소스가 중지 된 상태가 됩니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ERS 인스턴스의 리소스 상태를 정리합니다.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. 큐에 넣기 sapstartsrv 프로세스 종료

   테스트 시작 전 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   ASCS를 실행 중인 노드에서 루트 권한으로 다음 명령을 실행합니다.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   sapstartsrv 프로세스는 항상 Pacemaker 리소스 에이전트에 의해 다시 시작되어야 합니다. 테스트 후 리소스 상태:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP 용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP 용 Azure Virtual Machines 배포][deployment-guide]
* [SAP 용 Azure Virtual Machines DBMS 배포][dbms-guide]
* 고가용성을 설정 하 고 SAP의 재해 복구를 계획 하는 방법을 알아보려면 
* HANA on Azure (큰 인스턴스)에 대 한 자세한 내용은 [azure의 SAP HANA (큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조 하세요.
* Azure Vm에서 SAP HANA의 고가용성을 설정 하 고 재해 복구를 계획 하는 방법에 대 한 자세한 내용은 [azure Virtual Machines (vm)의 SAP HANA 고가용성][sap-hana-ha] 을 참조 하세요.

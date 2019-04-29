---
title: Azure Virtual Machines 고가용성 SAP NetWeaver NetApp Azure Files를 사용 하 여 SUSE Linux Enterprise Server에 대 한 | Microsoft Docs
description: SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 SAP NetWeaver-고가용성 가이드
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: 18bbeef833e1c82999e87451d279c0d3464af509
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711143"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>SAP 응용 프로그램에 대 한 Azure NetApp 파일을 사용 하 여 SUSE Linux Enterprise Server의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
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

이 문서에서는 가상 컴퓨터를 배포, 가상 컴퓨터를 구성, 클러스터 프레임 워크 및 고가용성 SAP NetWeaver 7.50 시스템을 설치 하는 방법을 설명를 사용 하 여 [Azure NetApp 파일 (공개 미리 보기)](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/)합니다.
예제 구성, 설치 명령 등, ASCS 인스턴스 번호 00, ERS 인스턴스 번호 01, 기본 응용 프로그램 인스턴스 (PA) 02 이며 응용 프로그램 인스턴스 AAS ()는 03 합니다. SAP 시스템 ID QAS 사용 됩니다. 

이 문서에서는 Azure NetApp 파일을 사용 하 여 SAP NetWeaver 응용 프로그램에 대 한 고가용성을 달성 하는 방법에 설명 합니다. 데이터베이스 계층은이 문서에서 자세히 적용 되지 않습니다.

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp 파일 설명서][anf-azure-doc] 
* SAP Note [1928533], 다음 항목을 포함합니다.  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드에는 Netweaver HA 및 SAP HANA 시스템 복제 온-프레미스를 설정하는 데 필요한 모든 정보가 포함됩니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE High Availability Extension 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]
* [NetApp Azure Files를 사용 하 여 Microsoft Azure의 NetApp SAP 응용 프로그램][anf-sap-applications-azure]

## <a name="overview"></a>개요

높은 availability(HA) 중앙 서비스가 SAP Netweaver에 대 한 공유 저장소가 필요 합니다.
SUSE Linux에서이 위해 지금 필요 했기 별도 항상 사용 가능한 NFS 클러스터를 구축 합니다. 

이제 목표를 달성할 수 SAP Netweaver HA NetApp 파일을 Azure에 배포 된 공유 저장소를 사용 하 여입니다. 공유 저장소가 필요 하지 않습니다.에 대 한 Azure NetApp 파일을 사용 하 여 추가 [NFS 클러스터](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)합니다. Pacemaker는 고가용성 SAP Netweaver 중앙 services(ASCS/SCS) 여전히 필요 합니다.


![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에는 [부하 분산 장치](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) 가상 IP 주소를 사용 하는 데 필요한 합니다. 아래 목록에서는(A)SCS 및 ERS 부하 분산 장치에 대한 구성이 나와 있습니다.

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * IP 주소 10.1.1.20
* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * 포트 620<strong>&lt;nr&gt;</strong>
* 부하 분산 규칙
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
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp 파일 인프라를 설정 

SAP NetWeaver에는 전송 및 프로필 디렉터리에 대한 공유 저장소가 필요합니다.  Azure NetApp 파일 인프라에 대 한 설치를 진행 하기 전에 숙지 합니다 [NetApp Azure Files 설명서][anf-azure-doc]합니다. 선택한 Azure 지역에 Azure NetApp 파일 제공 하는 경우를 확인 합니다. 다음 링크를 Azure 지역에 따라 NetApp 파일을 Azure의 가용성을 보여 줍니다. [Azure 지역에 따라 가용성 파일을 azure NetApp][anf-avail-matrix]합니다.

Azure NetApp 파일 기능은 여러 Azure 지역에서 공개 미리 보기로 제공에서 됩니다. NetApp Azure Files를 배포 하기 전에 Azure NetApp 파일 미리 보기에 등록을 수행 합니다 [Azure NetApp 파일 지침에 대 한 등록][anf-register]합니다. 

### <a name="deploy-azure-netapp-files-resources"></a>NetApp 파일 Azure 리소스 배포  

단계를 이미 배포한 가정 [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)합니다. Azure NetApp 파일 리소스 및 Azure NetApp 파일 리소스를 탑재할 Vm을 동일한 Azure Virtual Network에 배포 되어야 합니다는 것을 염두에 두십시오.  

1. 아직 수행 하지 않은, 경우 요청할 [NetApp Azure 미리 보기에 등록](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register)합니다.  

2. 다음 선택한 Azure 지역에서 NetApp 계정을 만들려면 합니다 [NetApp 계정을 만드는 지침은](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)합니다.  
3. 다음 Azure NetApp Files 용량 풀을 설정 합니다 [NetApp 파일 Azure 용량 풀을 설정 하는 방법에 대 한 지침](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)합니다.  
이 문서에서 제공 하는 SAP Netweaver 아키텍처는 단일 Azure NetApp Files 용량 풀, 프리미엄 SKU를 사용 합니다. Azure에서 SAP Netweaver 응용 프로그램 워크 로드에 대 한 Azure NetApp 파일 Premium SKU를 권장합니다.  

4. 에 설명 된 대로 Azure NetApp 파일에 서브넷을 위임 합니다 [지침은 위임할 Azure NetApp 파일에 서브넷](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)합니다.  

5. 다음 Azure NetApp 파일 볼륨을 배포 하는 [볼륨을 만들려면 Azure NetApp 파일에 대 한 지침](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes)합니다. 지정된 된 Azure NetApp 파일의 볼륨에 배포할 [서브넷](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets)합니다. Azure NetApp 파일 리소스 및 Azure Vm을 동일한 Azure Virtual Network에 있어야 하는 점을 염두에 두십시오. 예를 들어 sapmnt<b>QAS</b>, usrsap<b>QAS</b>등은 볼륨 이름 및 sapmnt<b>qas</b>, usrsap<b>qas</b>등 azure는 filepaths는 NetApp 파일 볼륨입니다.  

   1. 볼륨 sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. 볼륨 usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. 볼륨 usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. 볼륨 usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>qas</b>ers)
   5. 볼륨 trans (nfs://10.1.0.4/trans)
   6. 볼륨 usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>pa)
   7. 볼륨 usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
이 예제에서는 Azure NetApp 파일 사용할 수 있는 방법을 보여 주기 위해 모든 SAP Netweaver 파일 시스템에 대 한 Azure NetApp 파일을 사용 했습니다. NFS를 통해 탑재 하지 않아도 되는 SAP 파일 시스템으로 배포할 수도 있습니다 [Azure 디스크 저장소](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd) 합니다. 이 예제의 <b>a-e</b> Azure NetApp 파일에 있어야 합니다 및 <b>f g</b> (즉, r/sap/<b>QAS</b>/D<b>02</b>,r/sap/<b>QAS </b>/D<b>03</b>) Azure 디스크 저장소로 배포 될 수 있습니다. 

### <a name="important-considerations"></a>중요 고려 사항

SAP Netweaver 고가용성 SUSE 아키텍처에 대 한 Azure NetApp Files를 고려할 때 다음 중요 사항을 고려해 야 합니다.

- 최소 용량 풀 4 TiB는입니다. 용량 풀 크기 TiB 4의 배수 여야 합니다.
- 최소 볼륨이 100gib
- NetApp Azure Files 볼륨 탑재 될 모든 virtual machines와 azure NetApp 파일이 동일한 Azure Virtual Network 또는 이어야 합니다 [가상 네트워크 피어 링](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) 동일한 지역에 있습니다. 동일한 지역에서 VNET 피어 링을 통해 azure NetApp 파일 액세스는 이제 지원 됩니다. 전역 피어 링을 통해 azure NetApp 액세스가 아직 지원 되지 않습니다.
- 선택한 가상 네트워크에 Azure NetApp 파일에 위임 된 서브넷에 있어야 합니다.
- Azure NetApp 파일에는 현재 NFSv3만 지원 
- Azure NetApp 파일 제공 [정책 내보내기](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): 허용 된 클라이언트 액세스 형식 (읽기 및 쓰기, 읽기 전용 등)를 제어할 수 있습니다. 
- Azure NetApp 파일 기능은 아직 영역을 인식 합니다. 현재 Azure NetApp 파일 기능은 Azure 지역에서 모든 가용성 영역에서 배포 되지 않습니다. 일부 Azure 지역에서 잠재적인 대기 시간 영향에 주의 합니다. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Azure portal을 통해 Linux Vm을 수동으로 배포

먼저 Azure NetApp 파일 볼륨을 만드는 해야 합니다. Vm을 배포 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 리소스 그룹 만들기
1. Virtual Network 만들기
1. ASCS에 대 한 가용성 집합 만들기  
   최대 업데이트 도메인 설정
1. Virtual Machine 1 만들기  
   이상을 사용 SLES4SAP 12 SP3 이미지는이 예제의 SLES4SAP 12 SP3  
   ASCS에 대 한 이전에 만든 가용성 집합 선택  
1. Virtual Machine 2 만들기  
   이상을 사용 SLES4SAP 12 SP3 이미지는이 예제의 SLES4SAP 12 SP3  
   ASCS에 대 한 이전에 만든 가용성 집합 선택  
1. SAP 응용 프로그램 인스턴스 (PA, AAS)에 대 한 가용성 집합 만들기    
   최대 업데이트 도메인 설정
1. 3 가상 머신 만들기  
   이상을 사용 SLES4SAP 12 SP3 이미지는이 예제의 SLES4SAP 12 SP3  
   PAS/AAS에 대해 이전에 만든 가용성 집합 선택   
1. 가상 머신 4 만들기  
   이상을 사용 SLES4SAP 12 SP3 이미지는이 예제의 SLES4SAP 12 SP3  
   PAS/AAS에 대해 이전에 만든 가용성 집합 선택  

## <a name="setting-up-ascs"></a>(A)SCS 설정

이 예제에서는 리소스를 배포 된 수동으로 통해 합니다 [Azure portal](https://portal.azure.com/#home) 합니다.

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Azure portal을 통해 Azure Load Balancer를 수동으로 배포

먼저 Azure NetApp 파일 볼륨을 만드는 해야 합니다. Vm을 배포 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 부하 분산 장치(내부) 만들기  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS에 대 한 IP 주소 10.1.1.20
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예를 들어 **프런트 엔드입니다. QAS 합니다. ASCS**)
         1. 정적으로 할당을 설정 하 고 IP 주소를 입력 (예를 들어 **10.1.1.20**)
         1. 확인 클릭
      1. ASCS ERS에 대 한 IP 주소 10.1.1.21
         * ERS에 대 한 IP 주소를 만들려면 "a"에서 위 단계 반복 (예를 들어 **10.1.1.21** 고 **프런트 엔드입니다. QAS 합니다. ERS**)
   1. 백 엔드 풀 만들기
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
         1. 새 백 엔드 풀의 이름을 입력 합니다 (예를 들어 **백 엔드입니다. QAS**)
         1. 가상 머신 추가 클릭
         1. ASCS에 대 한 이전에 만든 가용성 집합 선택 
         1. (A)SCS 클러스터의 가상 머신 선택
         1. 확인 클릭
   1. 상태 프로브 만들기
      1. 포트: 620**00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름 입력 (예를 들어 **상태입니다. QAS 합니다. ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620**00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. 포트: 621**01** ASCS ERS에 대 한
            * "C" ERS에 대 한 상태 프로브를 만들려면 아래에서 위 단계 반복 (예: 621**01** 고 **상태입니다. QAS 합니다. ERS**)
   1. 부하 분산 규칙
      1. TCP: 32**00**(ASCS용)
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 선택 및 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름을 입력 합니다 (예를 들어 **lb 합니다. QAS 합니다. ASCS.3200**)
         1. ASCS, 백 엔드 풀 및 상태 프로브를 이전에 만든 프런트 엔드 IP 주소를 선택 합니다. (예를 들어 **프런트 엔드입니다. QAS 합니다. ASCS**)
         1. 프로토콜로 **TCP**를 유지하고. 포트로 **3200** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. ASCS에 대한 추가 포트
         * 36 포트에 대 한 "d"에서 위의 단계를 반복**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 및 ASCS 용 TCP
      1. ASCS ERS에 대한 추가 포트
         * 33 포트에 대 한 "d"에서 위의 단계를 반복**01**, 5**01**13, 5**01**14, 5**01**16 및 ASCS ERS에 대 한 TCP

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치 하는 Azure Vm에서 TCP 타임 스탬프를 사용 하지 마십시오. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브 실패 합니다. 매개 변수를 설정 **net.ipv4.tcp_timestamps** 하 **0**합니다. 자세한 내용은 참조 하십시오 [부하 분산 장치 상태 프로브](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview)합니다.

### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 단계에 따라 이 (A)SCS 서버에 대한 기본 Pacemaker 클러스터를 만듭니다.

### <a name="installation"></a>설치

다음 항목에는 접두사 **[A]**(모든 노드에 적용됨), **[1]**(노드 1에만 적용됨), **[2]**(노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** SUSE 커넥터 설치

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > 클러스터 노드의 호스트 이름에는 대시를 사용하지 마세요. 대시를 사용하면 클러스터가 작동하지 않습니다. 이것은 알려진 제한 사항이며 SUSE는 수정 프로그램을 개발 중입니다. 수정 프로그램은 sap-suse-cloud-connector 패키지의 패치로 릴리스될 예정입니다.

   SAP SUSE 클러스터 커넥터의 새 버전을 설치했는지 확인합니다. 기존 버전은 sap_suse_cluster_connector라고 하며 새 버전은 **sap suse-cluster-connector**라고 합니다.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
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

2. **[A]** autofs 구성

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   아래 코드를 사용하여 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > 현재 Azure NetApp 파일 NFSv3만 지원합니다. nfsvers 생략 하지 않음 = 3 스위치입니다.
   
   autofs를 다시 시작하여 새 공유를 탑재합니다.
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

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
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
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** SAP NetWeaver ASCS 설치  

   예를 들어 ASCS 용 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 첫 번째 노드에 루트로 SAP NetWeaver ASCS를 설치 <b>anftstsapvh</b>하십시오 <b>10.1.1.20</b> 및 예를 들어 부하 분산 장치의 프로브에 사용한 인스턴스 번호 <b>00</b>합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. SAP 가상 호스트 이름을 사용 하 여 설치 하려면 SAPINST_USE_HOSTNAME 매개 변수를 사용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   r/sap/하위 폴더를 만들려는 경우 설치에 실패**QAS**/ASCS**00**, 소유자와 ASCS의 그룹을 설정 해 보십시오**00** 폴더 및 다시 시도 합니다. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** ERS 인스턴스에 대한 가상 IP 리소스 및 상태 검사 프로브 만들기

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
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

   예를 들어 ers 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 두 번째 노드에 루트로 SAP NetWeaver ERS를 설치 <b>anftstsapers</b>하십시오 <b>10.1.1.21</b> 및 예를 들어 부하 분산 장치의 프로브에 사용한 인스턴스 번호 <b>01</b>합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. SAP 가상 호스트 이름을 사용 하 여 설치 하려면 SAPINST_USE_HOSTNAME 매개 변수를 사용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   r/sap/하위 폴더를 만들려는 경우 설치에 실패**QAS**/ERS**01**, 소유자 및 ERS의 그룹을 설정 해 보십시오**01** 폴더 및 다시 시도 합니다.

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

   SAP NetWeaver 애플리케이션 서버와 ASCS/SCS 간의 통신은 소프트웨어 부하 분산 장치를 통해 라우팅됩니다. 부하 분산 장치는 구성 가능한 시간 제한이 지나면 비활성 연결을 끊습니다. 이 연결 끊김을 방지하려면 SAP NetWeaver ASCS/SCS 프로필에서 매개 변수를 설정하고 Linux 시스템 설정을 변경해야 합니다. 자세한 내용은 [SAP Note 1410736][1410736]을 참조하세요.

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

8. **[1]** sapservice 파일에 ASCS 및 ERS SAP 서비스 추가

   ASCS 서비스 항목을 두 번째 노드에 추가하고 ERS 서비스 항목을 첫 번째 노드에 복사합니다.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** SAP 클러스터 리소스 만들기

큐에 넣기 서버 1 아키텍처 (ENSA1)를 사용 하는 경우 아래와 같이 리소스를 정의 합니다.

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

   큐에 넣기 서버 복제의 경우 SAP NW 7.52 기준으로 포함 하 여 2 도입 하는 SAP 지원 합니다. 큐에 넣기 서버 2 ABAP 플랫폼 1809부터 기본적으로 설치 됩니다. SAP 참고 참고 [2630416](https://launchpad.support.sap.com/#/notes/2630416) 큐에 넣기 서버 2 지원에 대 한 합니다.
큐에 넣기 서버 2 아키텍처를 사용 하는 경우 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), 리소스를 다음과 같이 정의 합니다.

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

   이전 버전에서 업그레이드 하 고 큐에 넣기 서버 2로 전환 하는 경우 sap 참고 참조 [2641019](https://launchpad.support.sap.com/#/notes/2641019)합니다. 

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

다음 항목 중 하나를 사용 하 여 접두사가 **[A]** -PA 및 AAS, 둘 다에 적용 **[P]** -PA에만 적용 하거나 **[S]** -AAS에만 적용 합니다.


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

1. **[A]**  Sapmnt 디렉터리를 만듭니다

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  PAS 디렉터리 만들기

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  AAS 디렉터리 만들기

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  PA에 autofs를 구성 합니다.

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   다음 코드를 사용하여 새 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   autofs를 다시 시작하여 새 공유를 탑재합니다.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  AAS에서 autofs 구성

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   다음 코드를 사용하여 새 파일을 만듭니다.

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   autofs를 다시 시작하여 새 공유를 탑재합니다.

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

이 예제에서는 SAP NetWeaver가 SAP HANA에 설치됩니다. 이 설치에 지원되는 모든 데이터베이스를 사용할 수 있습니다. Azure에서 SAP HANA를 설치하는 방법에 대한 자세한 내용은 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요. 지원되는 데이터베이스 목록은 [SAP Note 1928533][1928533]을 참조하세요.

* SAP 데이터베이스 인스턴스 설치 실행

   데이터베이스에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용 하 여 루트로 SAP NetWeaver 데이터베이스 인스턴스를 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 애플리케이션 서버 설치

다음 단계에 따라 SAP 애플리케이션 서버를 설치합니다.

1. **[A]**  챕터의 단계를 수행 하는 응용 프로그램 서버 준비 [SAP NetWeaver 응용 프로그램 서버 준비](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) 위의 응용 프로그램 서버를 준비 합니다.

2. **[A]**  SAP NetWeaver 설치 응용 프로그램 서버를 기본 또는 추가 SAP NetWeaver 응용 프로그램 서버를 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  업데이트 SAP HANA 보안 저장소

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

   출력은 기본 항목의 IP 주소가 부하 분산 장치의 IP 주소가 아니라 가상 머신을 가리키고 있음을 나타냅니다. 이 항목은 부하 분산 장치의 가상 호스트 이름을 가리키도록 변경해야 합니다. 동일한 포트를 사용 하도록 (**30313** 위의 출력에서) 및 데이터베이스 이름 (**QAS** 위의 출력에서).

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

다음 테스트는 테스트 사례를 복사 합니다 [모범 사례 가이드는 SUSE][suse-ha-guide]합니다. 이 테스트는 작업자 편의를 위해 복사되었습니다. 또한 항상 모범 사례 가이드를 읽고 추가되었을 수 있는 모든 추가 테스트를 수행해야 합니다.

1. HAGetFailoverConfig, HACheckConfig, 및 HACheckFailoverConfig 테스트

   ASCS 인스턴스를 현재 실행 중인 노드에서 \<sapsid>adm으로 다음 명령을 실행합니다. 이러한 명령이 실패: 메모리 부족을 나타내며 실패할 경우 호스트 이름의 대시 때문일 수 있습니다. 이것은 알려진 문제로, SUSE에서는 sap-suse-cluster-connector 패키지에서 이 문제를 수정할 예정입니다.

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

   예를 들어 트랜잭션 su01에서 사용자를 편집하여 큐에 넣기 잠금을 만듭니다. 로 다음 명령을 실행 합니다. < sapsid\>ASCS 인스턴스 실행 되 고 있는 노드에서 adm 합니다. 이러한 명령은 ASCS 인스턴스를 중지했다가 다시 시작합니다. 큐에 넣기 서버 1 아키텍처를 사용 하는 경우 큐에 넣기 잠금은이 테스트에서 손실 될 예정입니다. 큐에 넣기 서버 2 아키텍처를 사용 하는 큐에 넣기 유지 됩니다. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS 인스턴스는 이제 Pacemaker에서 사용되지 않도록 설정됩니다.

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   동일한 노드에서 ASCS 인스턴스를 다시 시작합니다.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   큐에 넣기 서버 1 복제 아키텍처를 사용 하는 경우 트랜잭션 su01의 큐에 넣기 잠금이 손실 여야 하며 백 엔드 재설정 해야 합니다. 테스트 후 리소스 상태:

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

   메시지 서버를 한 번만 종료하면 sapstart에 의해 다시 시작됩니다. 충분히 자주 종료하면 Pacemaker는 ASCS 인스턴스를 다른 노드로 이동합니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ASCS 및 ERS 인스턴스의 리소스 상태를 정리합니다.

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

   이 명령을 한 번만 실행하는 경우 sapstart가 프로세스 다시 시작합니다. 충분히 자주 실행하면 sapstart가 해당 프로세스를 다시 시작하지 않으며 리소스는 중지된 상태가 됩니다. 테스트 후에 다음 명령을 루트 권한으로 실행하여 ERS 인스턴스의 리소스 상태를 정리합니다.

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

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* 고가용성 및 재해 복구의 SAP에 대 한 계획을 설정 하는 방법을 알아보려면 
* HANA on Azure (큰 인스턴스)를 참조 하세요 [SAP HANA (큰 인스턴스) 고가용성 및 재해 복구 Azure에서](hana-overview-high-availability-disaster-recovery.md)합니다.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.

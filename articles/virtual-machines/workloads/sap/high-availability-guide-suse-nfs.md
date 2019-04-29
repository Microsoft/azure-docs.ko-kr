---
title: SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성 | Microsoft Docs
description: SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2019
ms.author: sedusch
ms.openlocfilehash: a91bc1cbb72427205cc558a4b5e655f4aa8083b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710735"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

이 문서에서는 가상 머신을 배포하고, 가상 머신을 구성하고, 클러스터 프레임워크를 설치하고, 가용성이 높은 SAP 시스템의 공유 데이터를 저장하는 데 사용할 수 있는 고가용성 NFS 서버를 설치하는 방법을 설명합니다.
이 가이드에서는 NW1 및 NW2라는 두 개의 SAP 시스템에 사용되는 고가용성 NFS 서버를 설정하는 방법에 대해 설명합니다. 예제에 포함된 리소스(예: 가상 머신, 가상 네트워크) 이름은 [SAP 파일 서버 템플릿][template-file-server]을 리소스 접두사인 **prod**와 함께 사용한 것으로 가정합니다.

먼저 다음 SAP 참고와 문서 읽기

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
* [Linux에서 SAP용 Azure Virtual Machines 배포(이 문서)][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE Linux Enterprise 고가용성 확장 12 SP3 모범 사례 가이드][sles-hae-guides]
  * DRBD 및 Pacemaker를 사용하는 고가용성 NFS 저장소
* [SAP 애플리케이션 12 SP3용 SUSE Linux Enterprise Server 모범 사례 가이드][sles-for-sap-bp]
* [SUSE High Availability Extension 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]

## <a name="overview"></a>개요

SAP NetWeaver의 가용성을 높이려면 NFS 서버가 필요합니다. NFS 서버는 별도의 클러스터에서 구성되며, 여러 SAP 시스템에서 사용할 수 있습니다.

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-nfs/ha-suse-nfs.png)

이 NFS 서버를 사용하는 모든 SAP 시스템에 대해 전용 가상 호스트 이름과 가상 IP 주소가 사용됩니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 아래 목록에는 부하 분산 장치 구성이 나와 있습니다.        

* 프런트 엔드 구성
  * NW1의 경우 IP 주소 10.0.0.4
  * NW2의 경우 IP 주소 10.0.0.5
* 백 엔드 구성
  * NFS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
* 프로브 포트
  * NW1의 경우 포트 61000
  * NW2의 경우 포트 61001
* 부하 분산 규칙
  * NW1의 경우 2049 TCP
  * NW1의 경우 2049 UDP
  * NW2의 경우 2049 TCP
  * NW2의 경우 2049 UDP

## <a name="set-up-a-highly-available-nfs-server"></a>고가용성 NFS 서버 설정

GitHub의 Azure 템플릿을 사용하여 필요한 Azure 리소스(가상 머신, 가용성 집합 및 부하 분산 장치 등)를 모두 배포하거나 수동으로 리소스를 배포할 수 있습니다.

### <a name="deploy-linux-via-azure-template"></a>Azure 템플릿을 통해 Linux 배포

Azure Marketplace에는 새 가상 머신을 배포하는 데 사용할 수 있는 SAP Applications 12용 SUSE Linux Enterprise Server의 이미지가 포함되어 있습니다.
GitHub에서 빠른 시작 템플릿 중 하나를 사용하여 필요한 모든 리소스를 배포할 수 있습니다. 템플릿에서 가상 머신, 부하 분산 장치, 가용성 집합 등을 배포합니다. 다음 단계를 따라 템플릿을 배포합니다.

1. Azure Portal에서 [SAP 파일 서버 템플릿][template-file-server]을 엽니다.   
1. 다음 매개 변수를 입력합니다.
   1. 리소스 접두사 -  
      사용할 접두사를 입력합니다. 이 값은 배포되는 리소스의 접두사로 사용됩니다.
   2. SAP 시스템 수 -  
      이 파일 서버를 사용할 SAP 시스템 수를 입력합니다. 이렇게 하면 프런트 엔드 구성, 부하 분산 규칙, 프로브 포트, 디스크 등이 필요한 만큼 배포됩니다.
   3. OS 종류 -  
      Linux 배포판 중 하나를 선택합니다. 이 예제에서는 SLES 12를 선택합니다.
   4. 관리자 사용자 이름 및 관리자 암호 -  
      컴퓨터에 로그온하는 데 사용할 수 있게 만들어진 새 사용자입니다.
   5. 서브넷 ID  
      서브넷이 VM을 할당하도록 정의된 기존 VNet에 VM을 배포하려는 경우 해당 서브넷의 ID 이름을 지정합니다. ID는 대개 /subscriptions/**&lt;구독 ID&gt;**/resourceGroups/**&lt;리소스 그룹 이름&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;가상 네트워크 이름&gt;**/subnets/**&lt;서브넷 이름&gt;** 과 같은 형식입니다.

### <a name="deploy-linux-manually-via-azure-portal"></a>Azure Portal을 통해 Linux를 수동으로 배포

먼저 NFS 클러스터에 대한 가상 머신을 만들어야 합니다. 그런 다음, 부하 분산 장치를 만들고 백 엔드 풀의 가상 머신을 사용합니다.

1. 리소스 그룹 만들기
1. Virtual Network 만들기
1. 가용성 집합 만들기  
   최대 업데이트 도메인 설정
1. Virtual Machine 1 만들기 SLES4SAP 12 SP3 이상 사용, 이 예제에서는 SAP 애플리케이션 12 SP3용 SLES4SAP 12 SP3 BYOS 이미지 SLES(BYOS)가 사용됨  
   이전에 만든 가용성 집합 선택  
1. Virtual Machine 2 만들기 SLES4SAP 12 SP3 이상 사용, 이 예제에서는 SLES4SAP 12 SP3 BYOS 이미지 사용  
   SAP 애플리케이션 12 SP3용 SLES(BYOS)가 사용됨  
   이전에 만든 가용성 집합 선택  
1. 두 가상 머신의 각 SAP 시스템에 하나의 데이터 디스크를 추가합니다.
1. 부하 분산 장치(내부) 만들기  
   1. 프런트 엔드 IP 주소 만들기
      1. NW1의 경우 IP 주소 10.0.0.4
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름 입력(예: **nw1-frontend**)
         1. 할당을 정적으로 설정하고 IP 주소 입력(예: **10.0.0.4**)
         1. 확인 클릭
      1. NW2의 경우 IP 주소 10.0.0.5
         * NW2에 대해 위 단계를 반복
   1. 백 엔드 풀 만들기
      1. NW1에 대한 NFS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
         1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
         1. 새 백 엔드 풀의 이름 입력(예: **nw1-backend**)
         1. 가상 머신 추가 클릭
         1. 이전에 만든 가용성 집합 선택
         1. NFS 클러스터의 가상 머신 선택
         1. 확인 클릭
      1. NW2에 대한 NFS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
         * 위의 단계를 반복하여 NW2용 백 엔드 풀 만들기
   1. 상태 프로브 만들기
      1. NW1의 경우 포트 61000
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름 입력(예: **nw1-hp**)
         1. 프로토콜로 TCP를 선택하고 포트 610**00**을 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. NW2의 경우 포트 61001
         * 위의 단계를 반복하여 NW2용 상태 프로브 만들기
   1. 부하 분산 규칙
      1. NW1의 경우 2049 TCP
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정한 다음, 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름 입력(예: **nw1-lb-2049**)
         1. 이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 선택(예: **nw1-frontend**)
         1. **TCP** 프로토콜을 유지하고 포트 **2049** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. NW1의 경우 2049 UDP
         * NW1의 UDP 및 포트 2049에 대하 위 단계 반복
      1. NW2의 경우 2049 TCP
         * NW2의 TCP 및 포트 2049에 대하 위 단계 반복
      1. NW2의 경우 2049 UDP
         * NW2의 UDP 및 포트 2049에 대하 위 단계 반복

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치 하는 Azure Vm에서 TCP 타임 스탬프를 사용 하지 마십시오. TCP 타임 스탬프를 사용 하도록 설정 하면 상태 프로브 실패 합니다. 매개 변수를 설정 **net.ipv4.tcp_timestamps** 하 **0**합니다. 자세한 내용은 참조 하십시오 [부하 분산 장치 상태 프로브](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview)합니다.

### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

이 NFS 서버에 대한 기본 Pacemaker 클러스터를 만들려면 [Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 단계를 따르세요.

### <a name="configure-nfs-server"></a>NFS 서버 구성

다음 항목에는 접두사 **[A]**(모든 노드에 적용됨), **[1]**(노드 1에만 적용됨), **[2]**(노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** NFS 서버를 사용하도록 설정

   루트 NFS 내보내기 항목 만들기

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** drbd 구성 요소 설치

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** drbd 디바이스용 파티션 만들기

   사용 가능한 데이터 디스크 모두 나열

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   예제 출력
   
   ```
   lun0  lun1
   ```

   모든 데이터 디스크에 대한 파티션 만들기

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** LVM 구성 만들기

   사용 가능한 파티션 모두 나열

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   예제 출력
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   모든 파티션에 대해 LVM 볼륨 만들기

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** drbd 구성

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   drbd.conf 파일에 다음 두 줄이 포함되어 있는지 확인

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   전역 drbd 구성 변경

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   handler 및 net 섹션에 다음 항목 추가

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** NFS drbd 디바이스 만들기

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   새 drbd 디바이스용 구성을 삽입하고 명령을 종료합니다.

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   새 drbd 디바이스용 구성을 삽입하고 명령을 종료합니다.

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   drbd 디바이스를 만들어 시작합니다.

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** 초기 동기화 건너뛰기

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** 주 노드 설정

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** 새 drbd 디바이스가 동기화될 때까지 대기

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** drbd 디바이스에서 파일 시스템 만들기

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** drbd 분할 브레인(split-brain) 검색 설정

   drbd를 사용하여 한 호스트에서 다른 호스트로 데이터를 동기화 할 때 소위 분할 브레인(split-brain)이 발생할 수 있습니다. 분할 브레인(split-brain)은 두 클러스터 노드가 drbd 디바이스를 기본 디바이스로 승격시키고 동기화되지 않는 시나리오입니다. 드문 상황이지만 분할 브레인(split-brain)을 가능한 한 빨리 처리하고 해결해야 합니다. 따라서 분할 브레인(split-brain)이 발생하면 알림을 받는 것이 중요합니다.

   분할 브레인(split-brain) 알림을 설정하는 방법은 [공식 drbd 설명서](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification)를 참조하세요.

   분할 브레인(split-brain) 시나리오에서 자동으로 복구할 수도 있습니다. 자세한 내용은 [분할 브레인(split-brain) 자동 복구 정책](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)을 참조하세요.
   
### <a name="configure-cluster-framework"></a>클러스터 프레임워크 구성

1. **[1]** 클러스터 구성에 SAP 시스템 NW1용 NFS drbd 디바이스 추가

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** 클러스터 구성에 SAP 시스템 NW2용 NFS drbd 디바이스 추가

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]** 유지 관리 모드 사용 중지
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP ASCS 및 데이터베이스 설치](high-availability-guide-suse.md)
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.

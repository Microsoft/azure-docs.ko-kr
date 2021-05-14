---
title: Azure(큰 인스턴스)에서 SAP HANA 설치 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)를 설치하는 방법입니다.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1c1397416943e3e25719204a5ccfd593559476e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557601"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Azure의 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법

이 문서를 읽기 전에 [HANA 대규모 인스턴스 공통 용어](hana-know-terms.md) 및 [HANA 대규모 인스턴스 SKU](hana-available-skus.md)를 숙지하세요.

SAP HANA 설치는 사용자의 책임입니다. Azure 가상 네트워크와 HANA 대규모 인스턴스 단위 간의 연결을 설정한 후 Azure의 SAP HANA(대규모 인스턴스) 설치를 새로 시작할 수 있습니다. 

> [!Note]
> SAP 정책에 따라, SAP HANA 설치 인증 시험인 Certified SAP Technology Associate 시험을 통과했거나 SAP 인증 SI(시스템 통합자)인 사람이 SAP HANA 설치를 수행해야 합니다.

HANA 2.0을 설치하려는 경우 [SAP Support Note # 2235581 - SAP HANA: 지원되는 운영 체제](https://launchpad.support.sap.com/#/notes/2235581/E)에서 OS가 설치할 SAP HANA 릴리스에서 지원되는지 확인하세요. HANA 2.0에서 지원되는 OS는 HANA 1.0에서 지원되는 OS보다 더 제한적입니다. 또한 관심있는 OS 릴리스가 이 게시 [목록](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 특정 HLI 단위에 대해 지원되는 것으로 나열되는지 확인해야 합니다. 단위를 클릭하여 해당 단위의 지원되는 OS 목록이 포함된 전체 세부 정보를 가져옵니다. 

HANA 설치를 시작하기 전에 다음을 유효성 검사합니다.
- [HLI 단위](#validate-the-hana-large-instance-units)
- [운영 체제 구성](#operating-system)
- [네트워크 구성](#networking)
- [스토리지 구성](#storage)


## <a name="validate-the-hana-large-instance-units"></a>HANA 대규모 인스턴스 단위 유효성 검사

Microsoft에서 HANA 대규모 인스턴스 단위를 받은 후 다음 설정의 유효성을 검사하고 필요에 따라 조정합니다.

HANA 대규모 인스턴스를 받고 이 인스턴스에 액세스 및 연결을 설정한 후 **첫 번째 단계** 는 인스턴스가 올바른 SKU 및 OS와 함께 표시되는지 Azure Portal에서 확인하는 것입니다. 검사를 수행하는 데 필요한 단계는 [Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어](./hana-li-portal.md)를 읽어보세요.

HANA 대규모 인스턴스를 받고 인스턴스에 대한 액세스 및 연결을 설정한 후 **두 번째 단계** 는 OS 공급자에게 인스턴스의 OS를 등록하는 것입니다. 이 단계에는 Azure의 VM에 배포된 SUSE SMT 인스턴스에 SUSE Linux OS를 등록하는 작업이 포함됩니다. 

HANA 대규모 인스턴스 단위는 SMT 인스턴스에 연결할 수 있습니다. 자세한 내용은 [SUSE Linux용 SMT 서버를 설정하는 방법](hana-setup-smt.md)을 참조하세요. 또는 연결해야 하는 Red Hat Subscription Manager에 Red Hat OS를 등록해야 합니다. 자세한 내용은 [Azure의 SAP HANA(대규모 인스턴스)란?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json)의 설명을 참조하세요. 

이 단계는 OS를 패치하는 데 필요하며 고객의 책임입니다. SUSE의 경우, [SMT 설치](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)와 관련된 이 페이지에서 SMT 설치 및 구성에 대한 문서를 확인하세요.

**세 번째 단계** 는 특정 OS 릴리스/버전의 새 패치 및 수정 사항을 확인하는 것입니다. HANA 대규모 인스턴스의 패치 수준이 최신 상태인지 확인합니다. 최신 패치가 포함되지 않은 경우가 있을 수 있습니다. HANA 대규모 인스턴스 단위를 받은 후 패치를 적용해야 하는지 여부를 확인해야 합니다.

**네 번째 단계** 는 특정 OS 릴리스/버전에서 SAP HANA 설치 및 구성에 대한 관련 SAP Note를 확인하는 것입니다. 개별 설치 시나리오에 따라 SAP Note 또는 구성에 대한 권장 사항이나 변경 내용이 달라지므로 Microsoft에서 항상 HANA 대규모 인스턴스 단위를 완벽하게 구성할 수 있는 것은 아닙니다. 

따라서 정확한 Linux 릴리스용 SAP HANA와 관련된 SAP Note를 반드시 읽어야 합니다. 또한 OS 릴리스/버전의 구성을 확인하고, 아직 수행하지 않은 경우 구성 설정을 적용합니다.

특히, 다음 매개 변수를 확인하고 다음과 같이 조정합니다.

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 및 RHEL 7.2부터 이러한 매개 변수를 /etc/sysctl.d 디렉터리의 구성 파일에 설정해야 합니다. 예를 들어 이름이 91-NetApp-HANA.conf인 구성 파일을 만들어야 합니다. 이전 SLES 및 RHEL 릴리스의 경우 /etc/sysctl.conf에서 이러한 매개 변수를 설정해야 합니다.

RHEL 6.3부터 모든 RHEL 릴리스에서는 다음 사항에 유의하세요. 
- sunrpc.tcp_slot_table_entries = 128 매개 변수를 /etc/modprobe.d/sunrpc-local.conf에서 설정해야 합니다. 파일이 없는 경우 먼저 다음 항목을 추가하여 파일을 만들어야 합니다. 
    - options sunrpc tcp_max_slot_table_entries=128

**다섯 번째 단계** 는 HANA 대규모 인스턴스 단위의 시스템 시간을 확인하는 것입니다. 인스턴스는 시스템 표준 시간대로 배포됩니다. 이 표준 시간대는 HANA 대규모 인스턴스 스탬프가 있는 Azure 지역의 위치를 나타냅니다. 소유한 인스턴스의 시스템 시간이나 표준 시간대를 변경할 수 있습니다. 

더 많은 인스턴스를 테넌트에 주문하는 경우 새로 전달된 인스턴스의 표준 시간대를 조정해야 합니다. Microsoft에서는 인계 후 인스턴스에서 사용자가 설정한 시스템 표준 시간대를 알 수 없습니다. 따라서 새로 배포된 인스턴스는 변경한 표준 시간대와 동일한 표준 시간대로 설정되지 않았을 수 있습니다. 필요한 경우 인계된 인스턴스의 표준 시간대를 조정하는 것은 고객의 책임입니다. 

**여섯 번째 단계** 는 etc/hosts를 확인하는 것입니다. 인계 시 블레이드에는 각기 다른 용도로 할당된 여러 IP 주소가 있습니다. etc/hosts 파일을 확인합니다. 기존 테넌트에 단위를 추가한 경우 새로 배포된 시스템의 etc/hosts가 이전에 제공된 시스템의 IP 주소로 올바르게 유지되지 않을 수 있습니다. 새로 배포된 인스턴스가 이전에 테넌트에 배포한 단위와 상호 작용하고 이름을 확인할 수 있도록 하는 것은 고객의 책임입니다. 


## <a name="operating-system"></a>운영 체제

제공되는 OS 이미지의 스왑 공간은 [SAP Support Note #1999997 - FAQ: SAP HANA 메모리](https://launchpad.support.sap.com/#/notes/1999997/E)(영문)에 따라 2GB로 설정됩니다. 고객이 다른 설정을 원하는 경우 직접 설정해야 합니다.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/)는 Azure의 SAP HANA(대규모 인스턴스)용으로 설치되는 Linux 배포판입니다. 이 특정 배포판은 SAP 관련 기능을 “바로 사용할 수 있게” 제공합니다(SLES에서 SAP를 효과적으로 실행하기 위해 미리 설정된 매개 변수 포함).

SLES의 SAP HANA 배포와 관련된 몇 가지 유용한 리소스(고가용성 설정, SAP 작업과 관련된 보안 강화 등)에 대해서는 SUSE 웹 사이트의 [리소스 라이브러리/백서](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) 및 SCN(SAP Community Network)의 [SUSE의 SAP](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)를 참조하세요.

다음은 SUSE의 SAP와 관련된 유용한 추가 링크입니다.

- [SUSE Linux의 SAP HANA 사이트](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 모범 사례: 복제 큐에 넣기 – SUSE Linux Enterprise 12의 SAP NetWeaver](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf)(영문)
- [ClamSAP - SAP용 SLES 바이러스 방지](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)(SLES 12 for SAP Applications 포함)(영문)

다음은 SLES 12의 SAP HANA 구현에 적용할 수 있는 SAP Support Note입니다.

- [SAP Support Note #1944799 – SLES 운영 체제 설치에 대한 SAP HANA 지침](http://service.sap.com/sap/support/notes/1944799)(영문)
- [SAP Support Note #2205917 – SAP HANA DB: SLES 12 for SAP Applications에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2205917/E)(영문)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: 설치 노트](https://launchpad.support.sap.com/#/notes/1984787)(영문)
- [SAP Support Note #171356 – SAP Software on Linux: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)(영문)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)

[SAP HANA용 Red Hat Enterprise Linux](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana)(영문)는 HANA 큰 인스턴스에서 SAP HANA를 실행하기 위한 또 다른 제품입니다. RHEL 7.2 및 7.3 릴리스를 사용할 수 있으며 지원됩니다. 

다음은 Red Hat의 SAP와 관련된 유용한 추가 링크입니다.
- [Red Hat Linux 사이트의 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)

다음은 Red Hat의 SAP HANA 구현에 적용할 수 있는 SAP Support Note입니다.

- [SAP Support Note #2009879 – RHEL(Red Hat Enterprise Linux) 운영 체제에 대한 SAP HANA 지침](https://launchpad.support.sap.com/#/notes/2009879/E)(영문)
- [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)(영문)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)
- [SAP Support Note #2228351 - Linux: RHEL 6 또는 SLES 11의 SAP HANA Database SPS 11 수정 번호 110 이상](https://launchpad.support.sap.com/#/notes/2228351)(영문)
- [SAP Support Note #2397039 - FAQ: RHEL의 SAP](https://launchpad.support.sap.com/#/notes/2397039)(영문)
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/2002167)(영문)

### <a name="time-synchronization"></a>시간 동기화

SAP NetWeaver 아키텍처를 기반으로 빌드된 SAP 애플리케이션은 SAP 시스템을 구성하는 다양한 구성 요소의 시간 차이에 민감합니다. 오류 제목이 ZDATE\_LARGE\_TIME\_DIFF인 SAP ABAP 짧은 덤프를 본 적이 있을 것입니다. 이러한 짧은 덤프는 서로 다른 서버 또는 VM의 시스템 시간 차이가 너무 큰 경우에 표시되기 때문입니다.

Azure의 SAP HANA(대규모 인스턴스)에서는 Azure에서 수행되는 시간 동기화가 대규모 인스턴스 스탬프의 컴퓨팅 단위에 적용되지 않습니다. 네이티브 Azure VM에서 SAP 애플리케이션을 실행하는 경우에는 이 동기화를 적용할 수 없습니다. Azure에서 시스템 시간이 적절하게 동기화되도록 하기 때문입니다. 

결과적으로 Azure VM에서 실행되는 SAP 애플리케이션 서버와 HANA 대규모 인스턴스에서 실행되는 SAP HANA 데이터베이스 인스턴스에서 사용할 수 있는 별도의 시간 서버를 설정해야 합니다. 대규모 인스턴스 스탬프의 스토리지 인프라는 NTP 서버와 시간 동기화됩니다.


## <a name="networking"></a>네트워킹
Azure 가상 네트워크를 설계하고 해당 가상 네트워크를 HANA 대규모 인스턴스에 연결할 때 다음 문서에 설명된 권장 사항을 따랐다고 가정합니다.

- [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](./hana-overview-architecture.md)
- [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md)

단일 단위의 네트워킹에 대해 알아야 할 몇 가지 세부 정보가 있습니다. 모든 HANA 대규모 인스턴스 단위에는 2개 또는 3개의 NIC 포트에 할당된 2개 또는 3개의 IP 주소가 포함되어 있습니다. HANA 확장 구성 및 HANA 시스템 복제 시나리오에는 3개의 IP 주소가 사용됩니다. 단위의 NIC에 할당된 IP 주소 중 하나가 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](./hana-overview-architecture.md)에 설명된 서버 IP 풀을 벗어납니다.

아키텍처의 이더넷 세부 정보에 대한 자세한 내용은 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.

## <a name="storage"></a>스토리지

Azure의 SAP HANA(대규모 인스턴스)에 대한 스토리지 레이아웃은 SAP 권장 지침에 따라 Azure의 SAP HANA `service management`에 의해 구성됩니다. 이 지침은 [SAP HANA 스토리지 요구 사항](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서에 나와 있습니다. 

각기 다른 HANA 대규모 인스턴스 SKU가 포함된 다양한 볼륨의 대략적인 크기는 [Azure의 SAP HANA(대규모 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md)에 문서화되어 있습니다.

스토리지 볼륨에 대한 명명 규칙은 아래 테이블에 나열되어 있습니다.

| 스토리지 사용 | 탑재 이름 | 볼륨 이름 | 
| --- | --- | ---|
| HANA data | /hana/data/SID/mnt0000\<m> | 스토리지 IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA log | /hana/log/SID/mnt0000\<m> | 스토리지 IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA log backup | /hana/log/backups | 스토리지 IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA shared | /hana/shared/SID | 스토리지 IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 스토리지 IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* 는 HANA 인스턴스 시스템 ID입니다. 

*Tenant* 는 테넌트 배포 시 작업의 내부 열거형입니다.

HANA usr/sap는 동일한 볼륨을 공유합니다. 탑재 지점의 명명법에는 HANA 인스턴스의 시스템 ID와 탑재 번호가 포함됩니다. 확장 배포의 경우 탑재가 하나(예: mnt00001)만 있습니다. 반면에 스케일 아웃 배포의 경우 보유한 작업자 및 마스터 노드 개수만큼 탑재가 표시됩니다. 

스케일 아웃 환경에서는 데이터, 로그 및 로그 백업 볼륨이 공유되고 스케일 아웃 구성의 각 노드에 연결됩니다. 여러 SAP 인스턴스로 이루어진 구성에서는 다른 볼륨 세트가 만들어져 HANA 대규모 인스턴스 단위에 연결됩니다. 시나리오에 대한 스토리지 레이아웃 정보는 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.

HANA 대규모 인스턴스 단위를 살펴보면 단위에서 HANA/data의 디스크 볼륨이 상당히 크고 HANA/log/backup 볼륨이 있다는 것을 발견합니다. HANA/data를 크게 만든 이유는 고객에게 제공되는 스토리지 스냅샷이 동일한 디스크 볼륨을 사용하기 때문입니다. 스토리지 스냅샷을 더 많이 수행할수록 할당된 스토리지 볼륨에서 더 많은 공간이 스냅샷에 사용됩니다. 

HANA/log/backup 볼륨은 데이터베이스 백업용 볼륨으로 지원되지 않습니다. HANA 트랜잭션 로그 백업용 백업 볼륨으로 사용되도록 크기가 설정되었습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조하세요. 

제공된 스토리지 외에도 1TB 단위로 추가 스토리지 용량을 구입할 수 있습니다. 이 추가 스토리지는 HANA 대규모 인스턴스에 새 볼륨으로 추가할 수 있습니다.

Azure의 SAP HANA(대규모 인스턴스) `service management`에 온보딩하는 동안 고객은 sidadm 사용자와 sapsys 그룹의 사용자 ID(UID) 및 그룹 ID(GID)를 지정합니다(예: 1000,500). SAP HANA 시스템을 설치하는 동안 동일한 값을 사용해야 합니다. 한 단위에 여러 개의 HANA 인스턴스를 배포하려고 하므로 다수의 볼륨 세트(인스턴스당 하나씩)를 갖게 됩니다. 결과적으로 배포 시 다음을 정의해야 합니다.

- 다양한 HANA 인스턴스의 SID(sidadm은 여기서 파생됨)
- 다양한 HANA 인스턴스의 메모리 크기. 인스턴스당 메모리 크기는 각각의 개별적인 볼륨 세트에서 볼륨 크기를 정의합니다.

스토리지 공급자 권장 사항에 따라 탑재된 모든 볼륨(부팅 LUN 제외)에 대해 다음 탑재 옵션이 구성됩니다.

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

탑재 지점은 다음 그림과 같이 /etc/fstab에 구성됩니다.

![HANA 큰 인스턴스 유닛의 탑재된 볼륨의 fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA 대규모 인스턴스 단위에 대한 df -h 명령의 출력은 다음과 같습니다.

![HANA 대규모 인스턴스 단위에 대한 명령 출력을 보여주는 스크린샷.](./media/hana-installation/image2_df_output.PNG)


큰 인스턴스 스탬프의 스토리지 컨트롤러와 노드는 NTP 서버와 동기화됩니다. NTP 서버를 기준으로 Azure의 SAP HANA(대규모 인스턴스) 단위와 Azure VM을 동기화하는 경우 Azure 또는 대규모 인스턴스 스탬프의 인프라 및 컴퓨팅 단위 간에 큰 시간 차이가 없어야 합니다.

아래 사용된 스토리지에 대해 SAP HANA를 최적화하려면 다음 SAP HANA 구성 매개 변수를 설정합니다.

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 버전 SPS12까지는 [SAP Note #2267798 - SAP HANA 데이터베이스의 구성](https://launchpad.support.sap.com/#/notes/2267798)에 설명된 대로 SAP HANA 데이터베이스 설치 중에 이러한 매개 변수를 설정할 수 있습니다.

hdbparam 프레임워크를 사용하여 SAP HANA 데이터베이스 설치 후 매개 변수를 구성할 수도 있습니다. 

HANA 대규모 인스턴스에서 사용된 스토리지에는 파일 크기 제한이 있습니다. 파일당 [크기 제한은 16TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)입니다. HANA는 EXT3 파일 시스템의 파일 크기 제한과 달리 HANA(대규모 인스턴스) 스토리지에 적용된 스토리지 제한을 암시적으로 인식하지 못합니다. 따라서 HANA는 16TB의 파일 크기 제한에 도달해도 새 데이터 파일을 자동으로 만들지 않습니다. HANA가 16TB를 초과하도록 파일을 확장하려고 하면, HANA가 오류를 보고하며 인덱스 서버 작동이 결국 중단됩니다.

> [!IMPORTANT]
> HANA가 HANA 대규모 인스턴스 스토리지의 16TB 파일 크기 제한을 초과하여 데이터 파일을 확장하지 않도록 하려면 SAP HANA의 global.ini 구성 파일에 다음 매개 변수를 설정해야 합니다.
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)도 참조
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)에 유의


SAP HANA 2.0에서는 hdbparam 프레임워크가 사용되지 않습니다. 따라서 SQL 명령을 사용하여 매개 변수를 설정해야 합니다. 자세한 내용은 [SAP Note #2399079: HANA 2에서 hdbparam 제거](https://launchpad.support.sap.com/#/notes/2399079)를 참조하세요.

아키텍처의 스토리지 레이아웃에 대한 자세한 내용은 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.


**다음 단계**

- [HLI의 HANA 설치](hana-example-installation.md)를 참조하세요.










































 







 

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
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce13c3bce7cdeb0f3e6dcf1f731be22d93a65587
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654602"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포

이 문서에서는 Azure IaaS에서 SAP ASE를 배포할 때 고려할 여러 가지 영역을 다룹니다. 이 문서의 사전 조건으로, [SAP 워크로드용 Azure Virtual Machines DBMS 배포 고려 사항](dbms_guide_general.md) 문서 및 [Azure의 SAP 워크로드 설명서](./get-started.md)의 다른 가이드를 읽어야 합니다. 이 문서에서는 Linux 및 Windows 운영 체제에서 실행 되는 SAP ASE에 대해 설명 합니다. Azure에서 지원 되는 최소 릴리스는 SAP ASE 16.0.02 (릴리스 16 지원 팩 2)입니다. 최신 버전의 SAP 및 최신 패치 수준을 배포 하는 것이 좋습니다.  최소 SAP ASE 16.0.03.07 (릴리스 16 지원 팩 3 패치 수준 7)을 권장 합니다.  최신 버전의 SAP는 [대상 ASE 16.0 릴리스 일정 및 CR 목록 정보](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)에서 찾을 수 있습니다.

Sap 응용 프로그램 또는 설치 미디어 위치를 사용 하는 릴리스 지원에 대 한 추가 정보는 다음 위치에 있는 SAP 제품 가용성 매트릭스에서 확인할 수 있습니다.

- [SAP support note #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP support note #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP support note #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP support note #1973241](https://launchpad.support.sap.com/#/notes/1973241)

설명: SAP 세계 내부 및 외부의 문서 전체에서 제품 이름은 Sybase ASE 또는 SAP ASE로 참조 되거나 경우에 따라 두 가지 경우 모두에 해당 합니다. 일관성을 유지 하기 위해이 설명서의 **SAP ASE** 이름을 사용 합니다.

## <a name="operating-system-support"></a>운영 체제 지원
SAP Product Availability 행렬에는 각 SAP 응용 프로그램에 대해 지원 되는 운영 체제 및 SAP 커널 조합이 포함 되어 있습니다.  Linux 배포판 SUSE 12. x, SUSE 4.x, Red Hat 4.x는 완전히 지원 됩니다.  Oracle Linux SAP ASE에 대 한 운영 체제는 지원 되지 않습니다.  사용 가능한 최신 Linux 릴리스를 사용 하는 것이 좋습니다. Windows 고객은 windows Server 2016 또는 Windows Server 2019 릴리스를 사용 해야 합니다.  Windows 2012와 같은 이전 버전의 Windows는 기술적으로 지원 되지만 항상 최신 Windows 버전을 권장 합니다.


## <a name="specifics-to-sap-ase-on-windows"></a>Windows의 SAP ASE에 대한 고유 정보
Microsoft Azure부터 기존 SAP ASE 애플리케이션을 Azure Virtual Machines로 마이그레이션할 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 이러한 애플리케이션을 Microsoft Azure로 쉽게 마이그레이션하여 엔터프라이즈 수준의 애플리케이션에 대한 배포, 관리 및 유지 관리의 총 소유 비용을 줄일 수 있습니다. Azure Virtual Machine에서 SAP ASE를 사용하면 관리자와 개발자가 온-프레미스와 동일한 개발 및 관리 도구를 사용할 수 있습니다.

Microsoft Azure는 가장 작은 SAP 시스템과 환경부터 수천 명의 사용자가 있는 대규모 SAP 시스템과 환경까지 실행할 수 있는 다양한 가상 머신 유형을 제공합니다. Sap [지원 참고 #1928533](https://launchpad.support.sap.com/#/notes/1928533)에서 SAP 인증 VM SKU의 sap 크기 조정 SAPS 번호를 제공 합니다.

Windows에 대 한 sap ASE 설치 설명서는 [windows 용 SAP Ase 설치 가이드](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html) 에서 찾을 수 있습니다.

메모리의 페이지 잠금은 SAP ASE 데이터베이스 버퍼의 페이징 해제를 방지 하는 설정입니다.  이 설정은 많은 메모리가 있는 사용량이 많은 시스템에 유용 합니다. 자세한 내용은 BC-DB-SYB에 문의 하세요. 


## <a name="linux-operating-system-specific-settings"></a>Linux 운영 체제 관련 설정
Linux Vm에서 프로필을 사용 하 여 실행 `saptune` SAP-ASE Linux는 기본적으로 사용 하도록 설정 되 고 명령을 사용 하 여 확인할 수 있어야 합니다.  

`cat /proc/meminfo` 

페이지 크기는 대개 2048 KB입니다. 자세한 내용은 [Linux의 큰 페이지](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) 문서를 참조 하세요. 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>SAP ASE 배포를 위한 VM 및 디스크 구조에 대 한 권장 사항

Sap NetWeaver 응용 프로그램에 대 한 SAP ASE는 [sap support #1928533 note](https://launchpad.support.sap.com/#/notes/1928533) 에 나열 된 모든 vm 유형에 대해 지원 됩니다. 보통 크기의 sap ASE 데이터베이스 서버에 사용 되는 일반적인 Vm 유형은 Esv3를 포함 합니다.  초대형 다중 테라바이트 데이터베이스는 M 시리즈 VM 유형을 활용할 수 있습니다. M 시리즈 쓰기 가속기를 사용 하 여 SAP ASE 트랜잭션 로그 디스크 쓰기 성능을 향상 시킬 수 있습니다. Sap ase에서 로그 쓰기를 수행 하는 방식 때문에 SAP ASE를 사용 하 여 신중 하 게 테스트 해야 쓰기 가속기.  [SAP support note #2816580](../../how-to-enable-write-accelerator.md) 검토 하 고 성능 테스트를 실행 하는 것이 좋습니다.  
쓰기 가속기은 트랜잭션 로그 디스크만을 위해 설계 되었습니다. 디스크 수준 캐시를 NONE으로 설정 해야 합니다. Azure 쓰기 가속기에 다른 DBMS와 유사한 향상 된 기능이 표시 되지 않는 경우에는 걱정할 필요가 없습니다. SAP ASE가 트랜잭션 로그에 기록 하는 방법에 따라 Azure 쓰기 가속기에서 가속이 거의 발생 하지 않을 수 있습니다.
데이터 장치 및 로그 장치에는 별도의 디스크를 권장 합니다.  시스템 데이터베이스는 `saptools` synonoa 이며 전용 디스크가 필요 하지 않으며 SAP 데이터베이스 데이터와 로그 장치를 포함 하는 디스크에 배치할 수 있습니다. 

![SAP ASE에 대 한 저장소 구성](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>파일 시스템, 스트라이프 크기 & IO 분산 
달리 구성 되지 않은 경우 SAP ASE는 데이터를 디스크 저장 장치에 순차적으로 기록 합니다. 즉, 4 개의 장치가 있는 빈 SAP ASE 데이터베이스에서 첫 번째 장치에만 데이터를 씁니다.  다른 디스크 장치는 첫 번째 장치가 꽉 찬 경우에만 기록 됩니다.  각 SAP ASE 장치에 대 한 읽기 및 쓰기 IO의 양은 다를 수 있습니다. 사용 가능한 모든 Azure 디스크에서 디스크 IO를 분산 하려면 Windows 저장소 공간 또는 Linux LVM2을 사용 해야 합니다. Linux에서는 XFS 파일 시스템을 사용 하 여 디스크를 포맷 하는 것이 좋습니다. LVM 스트라이프 크기는 성능 테스트를 사용 하 여 테스트 해야 합니다. 128 KB 스트라이프 크기가 좋은 시작점입니다. Windows에서는 NTFS 오스트레일리아 (할당 단위 크기)를 테스트 해야 합니다. 64 KB를 시작 값으로 사용할 수 있습니다. 

[Sap 적응 서버 Enterprise에서 자동 데이터베이스 공간 확장 구성](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) 및 [sap 지원 참고 #1815695](https://launchpad.support.sap.com/#/notes/1815695)문서에 설명 된 대로 자동 데이터베이스 확장을 구성 하는 것이 좋습니다. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure 가상 머신, 디스크 및 파일 시스템 구성에 대 한 샘플 SAP ASE 
아래 템플릿에는 Linux 및 Windows에 대 한 샘플 구성이 나와 있습니다. 가상 컴퓨터 및 디스크 구성을 확인 하기 전에 개별 VM의 네트워크 및 저장소 대역폭 할당량이 비즈니스 요구 사항을 충족 하기에 충분 한지 확인 합니다. 또한 Azure VM 유형에 따라 VM에 연결할 수 있는 디스크의 최대 수가 서로 다르다는 점에 유의 하세요. 예를 들어 E4s_v3 VM에는 48 m b/초 저장소 IO 처리량이 제한 됩니다. 데이터베이스 백업 작업에 필요한 저장소 처리량이 48 m b/초를 초과 하는 경우 더 많은 저장소 대역폭 처리량을 가진 더 큰 VM 유형이 피할 수 있습니다. Azure storage를 구성 하는 경우 특히 [Azure Premium storage](../../windows/premium-storage-performance.md) 를 사용 하 여 용량 GB 당 처리량 및 IOPS가 변경 됨을 염두에 두어야 합니다. [Azure에서 사용할 수 있는 디스크 형식](../../disks-types.md)에 대 한 문서에서이 항목을 참조 하세요. 특정 Azure VM 유형에 대 한 할당량은 메모리 액세스에 [최적화 된 가상 머신 크기](../../sizes-memory.md) 및 여기에 연결 된 문서에 설명 되어 있습니다. 

> [!NOTE]
>  DBMS 시스템을 온-프레미스에서 Azure로 이동 하는 경우 VM에 대 한 모니터링을 수행 하 고 CPU, 메모리, IOPS 및 저장소 처리량을 평가 하는 것이 좋습니다. 위에서 언급 한 문서에 설명 된 VM 할당량 제한과 관찰 된 최고 값을 비교 합니다.

아래에서 설명 하는 예제는 설명을 위해 제공 되며 개별 요구 사항에 따라 수정할 수 있습니다. SAP ASE의 설계로 인해 데이터 장치 수는 다른 데이터베이스의 경우와 마찬가지로 중요 하지 않습니다. 이 문서에 설명 된 데이터 장치 수는 가이드 전용입니다. 

SAP 솔루션 관리자와 같이 데이터베이스 크기가 50 GB-250 GB 사이인 작은 SAP ASE DB 서버에 대 한 구성의 예는 다음과 같을 수 있습니다.

| Configuration | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | E4s_v3 (4 개 vCPU/32gb RAM) | E4s_v3 (4 개 vCPU/32gb RAM) | --- |
| 가속 네트워킹 | 사용 하도록 설정 | 사용 하도록 설정 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 장치 개수 | 4 | 4 | ---|
| 로그 장치 개수 | 1 | 1 | --- |
| 임시 장치 개수 | 1 | 1 | SAP BW 작업에 대 한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 형식 블록 크기 | 워크 로드 테스트 필요 | 워크 로드 테스트 필요 | --- |
| # 및 데이터 디스크 유형 | Premium storage: 2 x P10 (RAID0) | Premium storage: 2 x P10 (RAID0)| Cache = 읽기 전용 |
| 로그 디스크의 유형 (#) | Premium storage: 1 x P20  | Premium storage: 1 x P20 | Cache = 없음 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 장치 개수 | 4 | 4| --- |
| 백업 디스크의 유형 (#) | 1 | 1 | --- |


데이터베이스 크기가 250 GB – 750 GB (예: 작은 SAP Business Suite 시스템) 사이인 medium SAP ASE DB 서버에 대 한 구성의 예는 다음과 같을 수 있습니다.

| Configuration | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | E16s_v3 (16 vCPU/128gb RAM) | E16s_v3 (16 vCPU/128gb RAM) | --- |
| 가속 네트워킹 | 사용 하도록 설정 | 사용 하도록 설정 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 장치 개수 | 8 | 8 | ---|
| 로그 장치 개수 | 1 | 1 | --- |
| 임시 장치 개수 | 1 | 1 | SAP BW 작업에 대 한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 형식 블록 크기 | 워크 로드 테스트 필요 | 워크 로드 테스트 필요 | --- |
| # 및 데이터 디스크 유형 | Premium storage: 4 x P20 (RAID0) | Premium storage: 4 x P20 (RAID0)| Cache = 읽기 전용 |
| 로그 디스크의 유형 (#) | Premium storage: 1 x P20  | Premium storage: 1 x P20 | Cache = 없음 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 장치 개수 | 4 | 4| --- |
| 백업 디스크의 유형 (#) | 1 | 1 | --- |

데이터베이스 크기가 750 GB – 2000 GB (예: 대형 SAP Business Suite 시스템) 사이인 작은 SAP ASE DB 서버에 대 한 구성의 예는 다음과 같을 수 있습니다.

| Configuration | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| 가속 네트워킹 | 사용 하도록 설정 | 사용 하도록 설정 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 장치 개수 | 16 | 16 | ---|
| 로그 장치 개수 | 1 | 1 | --- |
| 임시 장치 개수 | 1 | 1 | SAP BW 작업에 대 한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 형식 블록 크기 | 워크 로드 테스트 필요 | 워크 로드 테스트 필요 | --- |
| # 및 데이터 디스크 유형 | Premium storage: 4 x P30 (RAID0) | Premium storage: 4 x P30 (RAID0)| Cache = 읽기 전용 |
| 로그 디스크의 유형 (#) | Premium storage: 1 x P20  | Premium storage: 1 x P20 | Cache = 없음 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 장치 개수 | 4 | 4| --- |
| 백업 디스크의 유형 (#) | 1 | 1 | --- |


전체적으로 사용 되는 더 큰 SAP Business Suite 시스템과 같이 데이터베이스 크기가 2tb 이상인 작은 SAP ASE DB 서버에 대 한 구성의 예는 다음과 같을 수 있습니다.

| Configuration | Windows | Linux | 의견 |
| --- | --- | --- | --- |
| VM 유형 | M 시리즈 (1.0 ~ 4.0 TB RAM)  | M 시리즈 (1.0 ~ 4.0 TB RAM) | --- |
| 가속 네트워킹 | 사용 하도록 설정 | 사용 하도록 설정 | ---|
| SAP ASE 버전 | 16.0.03.07 이상 | 16.0.03.07 이상 | --- |
| 데이터 장치 개수 | 32 | 32 | ---|
| 로그 장치 개수 | 1 | 1 | --- |
| 임시 장치 개수 | 1 | 1 | SAP BW 작업에 대 한 추가 정보 |
| 운영 체제 | Windows Server 2019 | SUSE 12 SP4/15 SP1 또는 RHEL 7.6 | --- |
| 디스크 집계 | 스토리지 공간 | LVM2 | --- |
| 파일 시스템 | NTFS | XFS |
| 형식 블록 크기 | 워크 로드 테스트 필요 | 워크 로드 테스트 필요 | --- |
| # 및 데이터 디스크 유형 | Premium storage: 4 + x P30 (RAID0) | Premium storage: 4 + x P30 (RAID0)| Cache = 읽기 전용, Azure Ultra disk 고려 |
| 로그 디스크의 유형 (#) | Premium storage: 1 x P20  | Premium storage: 1 x P20 | 캐시 = 없음, Azure Ultra disk 고려 |
| ASE MaxMemory 매개 변수 | 실제 RAM의 90% | 실제 RAM의 90% | 단일 인스턴스 가정 |
| 백업 장치 개수 | 16 | 16 | --- |
| 백업 디스크의 유형 (#) | 4 | 4 | LVM2/저장소 공간 사용 |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Azure의 SAP ASE에 대 한 백업 & 복원 고려 사항
데이터 및 백업 장치의 수를 늘리는 경우 백업 및 복원 성능이 향상 됩니다. 위의 표에 표시 된 것 처럼 SAP ASE 백업 장치를 호스트 하는 Azure 디스크를 스트라이프 하는 것이 좋습니다. 백업 장치 및 디스크 수의 균형을 유지 하 고 백업 처리량이 총 VM 처리량 할당량의 40%-50%를 초과 하지 않도록 주의 해야 합니다. 기본적으로 SAP 백업 압축을 사용 하는 것이 좋습니다. 자세한 내용은 다음 문서에서 찾을 수 있습니다.

- [SAP support note #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP support note #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP support note #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

D:\ 드라이브 사용 안 함 또는 데이터베이스 또는 로그 덤프 대상으로 임시 공간을 확보 합니다.

### <a name="impact-of-database-compression"></a>데이터베이스 압축의 영향
I/o 대역폭이 제한 요인이 될 수 있는 구성에서 IOPS를 줄이면 Azure와 같은 IaaS 시나리오에서 실행할 수 있는 워크 로드를 스트레치 하는 데 도움이 될 수 있습니다. 따라서 기존 SAP 데이터베이스를 Azure에 업로드하기 전에 SAP ASE 압축이 사용되는지 확인하는 것이 좋습니다.

Azure에 업로드하기 전에 압축을 적용하는 것이 좋은 이유는 다음과 같습니다.

* Azure에 업로드할 데이터의 양이 적어집니다.
* 더 향상된 CPU가 있는 강력한 하드웨어 또는 더 높은 I/O 대역폭이나 낮은 I/O 대기 시간의 온-프레미스를 사용할 경우 압축 실행 시간이 짧아집니다.
* 데이터베이스 크기가 작을수록 디스크 할당 비용이 줄어들 수 있습니다.

데이터 및 LOB 압축은 Azure Virtual Machines에서 호스트되는 VM에서도 온-프레미스에서와 마찬가지로 작동합니다. 기존 SAP ASE 데이터베이스에서 압축이 이미 사용 중인지 확인 하는 방법에 대 한 자세한 내용은 [sap support note 1750510](https://launchpad.support.sap.com/#/notes/1750510)을 참조 하세요. SAP ASE 데이터베이스 압축에 대 한 자세한 내용은 [sap 지원](https://launchpad.support.sap.com/#/notes/2121797) 정보를 참조 하세요 #2121797

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure에서 SAP ASE의 고가용성 
HADR 사용자 가이드는 2 노드 SAP ASE "Always on" 솔루션의 설정 및 구성에 대해 자세히 설명 합니다.  또한 세 번째 재해 복구 노드도 지원 됩니다. SAP ASE는 공유 디스크 및 기본 OS 클러스터링 (부동 IP)을 비롯 하 여 사용 가능한 많은 구성을 지원 합니다. Azure에서 유일 하 게 지원 되는 구성은 부동 IP가 없는 오류 관리자를 사용 하는 것입니다.  부동 IP 주소 메서드는 Azure에서 작동 하지 않습니다.  SAP 커널은 "HA 인식" 응용 프로그램으로 기본 및 보조 SAP ASE 서버를 알고 있습니다. SAP ASE와 Azure 간에는 긴밀 하 게 통합 되지 않으므로 Azure 내부 부하 분산 장치는 사용 되지 않습니다. 따라서 표준 SAP ASE 설명서는 [SAP ASE HADR 사용자 가이드](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 에 따라 시작 해야 합니다. 

> [!NOTE]
> Azure에서 유일 하 게 지원 되는 구성은 부동 IP가 없는 오류 관리자를 사용 하는 것입니다.  부동 IP 주소 메서드는 Azure에서 작동 하지 않습니다. 

### <a name="third-node-for-disaster-recovery"></a>재해 복구를 위한 세 번째 노드
로컬 고가용성을 위해 SAP ASE를 항상 사용 하는 것 외에도, 다른 Azure 지역의 비동기 복제 노드로 구성을 확장할 수 있습니다. 이러한 시나리오에 대 한 설명서는 [여기](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)에서 찾을 수 있습니다.

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE 데이터베이스 암호화 & SSL 
SAP 소프트웨어 프로 비전 관리자 (SWPM)는 설치 중에 데이터베이스를 암호화 하는 옵션을 제공 합니다.  암호화를 사용 하려는 경우 SAP 전체 데이터베이스 암호화를 사용 하는 것이 좋습니다.  자세한 내용은 다음 항목에서 설명 합니다.

- [SAP support note #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP support note #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP support note #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP support note #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> SAP ASE 데이터베이스가 암호화 된 경우 백업 덤프 압축은 작동 하지 않습니다. [SAP support note](https://launchpad.support.sap.com/#/notes/2680905) 를 참조 하세요 #2680905 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure 배포 검사 목록의 SAP ASE
 
- SAP ASE 16.0.03.07 이상 배포
- FaultManager 및 SAPHostAgent의 최신 버전 및 패치로 업데이트
- Windows 2019, Suse 15.1 또는 Redhat 7.6 이상 등 사용 가능한 최신 인증 OS에 배포
- SAP 인증 Vm 사용-메모리 부족 Azure VM Sku (예: Es_v3) 또는 x 대규모 systems M 시리즈 VM Sku 권장
- 디스크 IOPS 및 VM의 총 VM 집계 처리량 할당량을 디스크 디자인과 일치 시킵니다.  충분 한 수의 디스크를 배포 합니다.
- 올바른 스트라이프 크기 및 파일 시스템을 사용 하 여 Windows 저장소 공간 또는 Linux LVM2를 사용 하는 디스크 집계
- 데이터, 로그, 임시 및 백업 목적으로 충분 한 수의 장치 만들기
- X-대기업 시스템에 UltraDisk 사용 고려 
- `saptune`LINUX OS에서 SAP ASE 실행 
- DB 암호화를 사용 하 여 데이터베이스 보호 – 수동으로 키 저장 Azure Key Vault 
- [Azure의 SAP 검사 목록](./sap-deployment-checklist.md) 완료 
- 로그 백업 및 전체 백업 구성 
- HA/DR 테스트, 백업/복원 및 스트레스 수행 & 볼륨 테스트 
- 자동 데이터베이스 확장이 작동 하는지 확인 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>DBACockpit를 사용 하 여 데이터베이스 인스턴스 모니터링
SAP ASE를 데이터베이스 플랫폼으로 사용하는 SAP 시스템의 경우 DBACockpit는 트랜잭션 DBACockpit에 포함된 브라우저 창 또는 Webdynpro로 액세스할 수 있습니다. 그러나 데이터베이스 모니터링 및 관리를 위한 전체 기능은 DBACockpit의 Webdynpro 구현 에서만 사용할 수 있습니다.

DBACockpit의 Webdynpro 구현에 의해 사용되는 모든 SAP NetWeaver 기능을 사용하려면 온-프레미스 시스템처럼 여러 단계가 필요합니다. 경우 사용을 사용 하도록 설정 하 고 필요한 항목을 생성 하려면 [SAP support note #1245200](https://launchpad.support.sap.com/#/notes/1245200) 를 따르세요. 위의 메모의 지침을 따르는 경우 `ICM` http 및 https 연결에 사용할 포트와 함께 인터넷 통신 관리자 ()도 구성 합니다. HTTP의 기본 설정은 다음과 같이 표시됩니다.

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

그리고 트랜잭션 DBACockpit에서 생성된 링크는 다음과 같이 표시됩니다.

> https: \/ / \<fullyqualifiedhostname> : 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

SAP 시스템을 호스트하는 Azure Virtual Machine이 AD 및 DNS에 연결된 방법에 따라 ICM이 DBACockpit를 여는 머신에서 확인할 수 있는 정규화된 호스트 이름을 사용하는지 확인해야 합니다. ICM에서 프로필 매개 변수에 따라 정규화 된 호스트 이름을 확인 하 고 필요한 경우 icm/host_name_full 매개 변수를 명시적으로 설정 하는 방법에 대 한 자세한 내용은 [SAP support note #773830](https://launchpad.support.sap.com/#/notes/773830) 를 참조 하세요.

온-프레미스와 Azure 간의 크로스-프레미스 연결 없이 클라우드 전용 시나리오에서 VM을 배포한 경우 공용 IP 주소와를 정의 해야 `domainlabel` 합니다. VM의 공용 DNS 이름 형식은 다음과 같이 표시됩니다.

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

DNS 이름과 관련 된 자세한 내용은 [여기] [azurerm-azuresm]를 참조 하세요.

SAP 프로필 매개 변수 icm/host_name_full을 Azure VM의 DNS 이름으로 설정할 경우 링크는 다음과 같습니다.

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

이 경우 다음을 확인해야 합니다.

* Azure Portal의 네트워크 보안 그룹에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.
* Windows 방화벽 구성에 ICM과 통신하는 데 사용되는 TCP/IP 포트에 대한 인바운드 규칙을 추가합니다.

사용 가능한 모든 수정 사항을 자동으로 가져오려면 SAP 버전에 적용할 수 있는 수정 사항 모음 SAP Note를 정기적으로 적용하는 것이 좋습니다.

* [SAP support note #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP support note #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP support note #1882376](https://launchpad.support.sap.com/#/notes/1882376)

SAP ASE의 DBA Cockpit에 대한 자세한 내용은 다음 SAP Note에서 확인할 수 있습니다.

* [SAP support note #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP support note #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP support note #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP support note #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP support note #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP support note #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP support note #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP support note #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>SAP ASE에 대 한 유용한 링크, 참고 & 백서
[SAP ASE 16.0.03.07 설명서](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) 의 시작 페이지에는 다음과 같은 문서를 사용 하는 다양 한 문서에 대 한 링크가 있습니다.

- SAP ASE 학습 교육-관리 & 모니터링
- SAP ASE 학습 교육-설치 & 업그레이드

유용 합니다. 또 다른 유용한 문서는 sap [적응 서버 엔터프라이즈 모범 사례의 마이그레이션 및 런타임에 대 한 Sap 응용 프로그램](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf)입니다.

기타 유용한 SAP 지원 정보는 다음과 같습니다.

- [SAP support note #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP support note #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP support note #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP support note #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP support note #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP support note #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP support note #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP support note #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP support note #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP support note #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP support note #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP support note #1588316](https://launchpad.support.sap.com/#/notes/158831) 

기타 정보는에 게시 됩니다. 

- [SAP 적응 서버 엔터프라이즈의 SAP 응용 프로그램](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE 정보 센터](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE Always on과 세 번째 DR 노드 설정](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

월간 뉴스레터는 [SAP 지원 정보](https://launchpad.support.sap.com/#/notes/2381575) 를 통해 게시 됩니다 #2381575 


## <a name="next-steps"></a>다음 단계
[Azure의 SAP 워크 로드 문서: 계획 및 배포 검사 목록](./sap-deployment-checklist.md) 을 확인 합니다.

---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09e5a6c5eee21e5432c4cf96a63fd2337307954a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211442"
---
Azure 공유 디스크 (미리 보기)는 azure 관리 디스크를 여러 Vm (가상 머신)에 동시에 연결할 수 있도록 하는 Azure managed disks에 대 한 새로운 기능입니다. 관리 디스크를 여러 Vm에 연결 하면 새 클러스터 된 응용 프로그램을 배포 하거나 기존 클러스터형 응용 프로그램을 Azure로 마이그레이션할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

클러스터의 Vm은 scsi PR ( [Scsi 영구 예약](https://www.t10.org/members/w_spc3.htm) )을 사용 하 여 클러스터 된 응용 프로그램에서 선택한 예약에 따라 연결 된 디스크를 읽거나 쓸 수 있습니다. SCSI PR은 SAN (저장 영역 네트워크) 온-프레미스에서 실행 되는 응용 프로그램에서 활용 하는 잘 알려진 업계 표준입니다. 관리 디스크에서 SCSI PR을 사용 하도록 설정 하면 이러한 응용 프로그램을 있는 그대로 Azure로 마이그레이션할 수 있습니다.

공유 디스크가 설정 된 Managed disks는 여러 Vm에서 액세스할 수 있는 공유 블록 저장소를 제공 합니다 .이 저장소는 Lun (논리 단위 번호)으로 노출 됩니다. 그런 다음 Lun은 대상 (디스크)에서 초기자 (VM)에 제공 됩니다. 이러한 Lun은 DAS (직접 연결 된 저장소) 또는 VM에 대 한 로컬 드라이브 처럼 보입니다.

공유 디스크가 설정 된 Managed disks는 SMB/NFS를 사용 하 여 액세스할 수 있는 완전히 관리 되는 파일 시스템을 기본적으로 제공 하지 않습니다. 클러스터 노드 통신 뿐만 아니라 쓰기 잠금을 처리 하는 WSFC (Windows Server 장애 조치 (Failover) 클러스터) 또는 Pacemaker와 같은 클러스터 관리자를 사용 해야 합니다.

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>샘플 작업

### <a name="windows"></a>Windows

대부분의 Windows 기반 클러스터링은 클러스터 노드 통신용 핵심 인프라를 모두 처리 하 여 응용 프로그램이 병렬 액세스 패턴을 활용할 수 있도록 하는 WSFC에서 빌드됩니다. WSFC를 사용 하면 Windows Server 버전에 따라 CSV 및 비 CSV 기반 옵션을 모두 사용할 수 있습니다. 자세한 내용은 [장애 조치 (failover) 클러스터 만들기](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)를 참조 하세요.

WSFC에서 실행 되는 일부 인기 있는 응용 프로그램은 다음과 같습니다.

- SQL Server 장애 조치 (Failover) 클러스터 인스턴스 (FCI)
- 스케일 아웃 파일 서버 (SoFS)
- 일반적으로 사용 되는 파일 서버 (IW 작업)
- 원격 데스크톱 서버 사용자 프로필 디스크 (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux 클러스터는 [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker)와 같은 클러스터 관리자를 활용할 수 있습니다. Pacemaker는 [Corosync](http://corosync.github.io/corosync/)를 빌드하여 항상 사용 가능한 환경에 배포 된 응용 프로그램에 대해 클러스터 통신을 사용 하도록 설정 합니다. 몇 가지 일반적인 클러스터형 파일 시스템에는 [ocfs2](https://oss.oracle.com/projects/ocfs2/) 및 [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2)가 포함 됩니다. [Fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) 및 [sg_persist](https://linux.die.net/man/8/sg_persist)와 같은 유틸리티를 사용 하 여 예약 및 등록을 조작할 수 있습니다.

## <a name="persistent-reservation-flow"></a>영구 예약 흐름

다음 다이어그램에서는 SCSI PR을 활용 하 여 한 노드에서 다른 노드로 장애 조치 (failover)를 수행할 수 있는 샘플 2 노드 클러스터 된 데이터베이스 응용 프로그램을 보여 줍니다.

![2 개 노드 클러스터 클러스터에서 실행 중인 응용 프로그램이 디스크에 대 한 액세스를 처리 하 고 있습니다.](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

흐름은 다음과 같습니다.

1. Azure VM1 및 V M 2 둘 다에서 실행 되는 클러스터 된 응용 프로그램은 디스크에 대 한 읽기 또는 쓰기 의도를 등록 합니다.
1. 그런 다음 VM1의 응용 프로그램 인스턴스는 디스크에 쓰기 위한 전용 예약을 사용 합니다.
1. 이 예약은 Azure 디스크에 적용 되며 이제 데이터베이스는 디스크에 독점적으로 쓸 수 있습니다. V M 2의 응용 프로그램 인스턴스에서 쓰기가 실패 하 게 됩니다.
1. VM1의 응용 프로그램 인스턴스가 중단 되 면 V M 2의 인스턴스가 이제 데이터베이스 장애 조치 (failover)를 시작 하 고 디스크를 사용할 수 있습니다.
1. 이제이 예약이 Azure 디스크에 적용 되 고 디스크는 더 이상 VM1의 쓰기를 허용 하지 않습니다. V M 2의 쓰기만을 허용 합니다.
1. 클러스터형 응용 프로그램은 데이터베이스 장애 조치 (failover)를 완료 하 고 V M 2의 요청을 처리할 수 있습니다.

다음 다이어그램은 기계 학습 모델의 학습 등 병렬 프로세스를 실행 하기 위해 디스크에서 데이터를 읽는 여러 노드로 구성 된 다른 일반적인 클러스터 된 작업을 보여 줍니다.

![4 개의 노드 VM 클러스터, 각 노드는 쓰기를 위한 것입니다. 응용 프로그램은 쓰기 결과를 올바르게 처리 하기 위해 단독 예약을 사용 합니다.](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

흐름은 다음과 같습니다.

1. 모든 Vm에서 실행 되는 클러스터 된 응용 프로그램은 디스크에 대 한 읽기 또는 쓰기 의도를 등록 합니다.
1. VM1의 응용 프로그램 인스턴스는 다른 Vm에서 디스크에 대 한 읽기를 열 때 전용 예약을 사용 하 여 디스크에 기록 합니다.
1. 이 예약은 Azure 디스크에 적용 됩니다.
1. 이제 클러스터의 모든 노드에서 디스크를 읽을 수 있습니다. 클러스터의 모든 노드를 대신 한 노드만 디스크에 결과를 기록 합니다.
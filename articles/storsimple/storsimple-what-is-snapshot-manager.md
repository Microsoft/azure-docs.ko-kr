---
title: StorSimple 스냅숏 관리자란? | Microsoft Docs
description: StorSimple 스냅숏 관리자와 그 아키텍처, 해당 기능을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789617"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager 소개

## <a name="overview"></a>개요
StorSimple 스냅숏 관리자는 Microsoft Azure StorSimple 환경에서 데이터 보호 및 백업 관리를 간소화하는 Microsoft Management Console(MMC) 스냅인입니다. StorSimple 스냅숏 관리자를 사용하면 데이터 센터와 클라우드에서 단일 통합 저장소 솔루션으로 Microsoft Azure StorSimple 데이터를 관리할 수 있으므로 백업 프로세스가 간소화되고 비용이 절감됩니다.

이 개요에서는 StorSimple 스냅숏 관리자를 소개하고, 해당 기능을 설명하며, Microsoft Azure StorSimple에서의 역할을 살펴봅니다. 

StorSimple 디바이스, StorSimple Manager 서비스, StorSimple Snapshot Manager, SharePoint용 StorSimple 어댑터 등을 포함한 전체 Microsoft Azure StorSimple 시스템에 대한 개요는 [StorSimple 8000 시리즈: 하이브리드 클라우드 스토리지 솔루션](storsimple-overview.md)을 참조하세요. 

> [!NOTE]
> * StorSimple 스냅숏 관리자를 사용하여 Microsoft Azure StorSimple 가상 배열 (StorSimple 온-프레미스 가상 디바이스라고도 함)를 관리할 수 없습니다.
> * StorSimple 디바이스에 StorSimple 업데이트 2를 설치하려는 경우 StorSimple Snapshot Manager의 최신 버전을 다운로드하여 **StorSimple 업데이트 2를 설치하기 전에**설치해야 합니다. StorSimple Snapshot Manager의 최신 버전은 이전 버전과 호환되며 릴리스된 모든 버전의 Microsoft Azure StorSimple에서 작동합니다. StorSimple Snapshot Manager의 이전 버전을 사용하고 있다면 업데이트해야 합니다(새 버전을 설치하기 전에 이전 버전을 제거할 필요 없음).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple 스냅숏 관리자 용도 및 아키텍처
StorSimple 스냅숏 관리자는 로컬 및 클라우드 데이터의 일관된 지정 시간 백업 복사본을 만드는 데 사용할 수 있는 중앙 관리 콘솔을 제공합니다. 예를 들어 콘솔을 사용하여 다음을 수행할 수 있습니다.

* 볼륨을 구성, 백업 및 삭제합니다.
* 백업된 데이터가 애플리케이션과 일관되도록 볼륨 그룹을 구성합니다.
* 미리 정해진 일정에 따라 데이터가 백업되도록 백업 정책을 관리합니다.
* 클라우드에 저장하고 재해 복구에 사용할 수 있는 로컬 및 클라우드 스냅숏을 만듭니다.

StorSimple Snapshot Manager는 호스트의 VSS 공급자에 등록된 애플리케이션 목록을 가져옵니다. 그런 다음, 애플리케이션에 일관된 백업을 만들려면 애플리케이션에서 사용하는 볼륨을 확인하고 구성할 볼륨 그룹을 제안합니다. StorSimple Snapshot Manager는 애플리케이션에 일관된 백업 복사본을 생성하기 위해 이러한 볼륨 그룹을 사용합니다. 모든 관련 파일과 데이터베이스가 동기화되고 특정 시점에서 애플리케이션의 실제 상태를 나타날 때 애플리케이션이 일관성 있는 것입니다. 

StorSimple 스냅숏 관리자 백업은 마지막 백업 이후의 변경 내용만 캡처하는 증분 스냅숏의 형태입니다. 결과적으로, 백업 저장소가 덜 필요 하고 신속하게 생성 및 복원할 수 있습니다. StorSimple Snapshot Manager는 스냅숏이 애플리케이션과 일관된 데이터를 캡처하도록 Windows 볼륨 섀도 복사본 서비스(VSS)를 사용합니다. 자세한 내용은 Windows 볼륨 섀도 복사본 서비스와 통합에서 제공합니다. StorSimple 스냅숏 관리자를 사용하여 백업 일정을 만들거나 필요에 따라 즉시 백업을 수행할 수 있습니다. 백업에서 데이터를 복원해야 하는 경우, StorSimple 스냅숏 관리자에서 로컬 또는 클라우드 스냅숏의 카탈로그 중에서 선택할 수 있습니다. Azure StorSimple은 필요에 따라 필요한 데이터만을 복원하므로 복원 작업 중의 데이터 가용성 지연을 방지합니다.

![StorSimple 스냅숏 관리자 아키텍처](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple 스냅숏 관리자 아키텍처** 

## <a name="support-for-multiple-volume-types"></a>여러 볼륨 유형에 대한 지원
StorSimple 스냅숏 관리자를 사용하여 다음과 같은 유형의 볼륨을 백업할 수 있습니다. 

* **기본 볼륨** – 기본 볼륨은 기본 디스크의 단일 파티션입니다. 
* **단순 볼륨** – 단순 볼륨은 단일 동적 디스크의 디스크 공간을 포함하는 동적 볼륨입니다. 단순 볼륨은 디스크의 단일 영역 또는 동일한 디스크에 연결되는 여러 지역으로 구성되어 있습니다. 단순 볼륨은 동적 디스크에서만 만들 수 있습니다. 단순 볼륨은 내결함성이 없습니다.
* **동적 볼륨** – 동적 볼륨은 동적 디스크에서 만든 볼륨입니다. 동적 디스크는 데이터베이스를 사용하여 컴퓨터의 동적 디스크에 담긴 볼륨 관련 정보를 추적합니다. 
* **미러링 있는 동적 볼륨** – 미러링 있는 동적 볼륨은 RAID 1 아키텍처에 구축됩니다. RAID 1에서는 동일한 데이터가 둘 이상의 디스크에 기록되어 미러링 세트를 이룹니다. 그러면 요청된 데이터가 포함된 어느 디스크에서나 읽기 요청을 처리할 수 있습니다.
* **클러스터 공유 볼륨** – 클러스터 공유 볼륨(CSV)에서는 장애 조치(failover) 클러스터의 다중 노드가 동일한 디스크에 대해 동시에 읽거나 씁니다. 드라이버 소유권 변경, 마운트, 마운트 해제 또는 볼륨 제거 없이, 한 노드에서 다른 노드로의 장애 조치가 신속하게 이루어질 수 있습니다. 

> [!IMPORTANT]
> CSV와 비 CSV를 동일한 스냅숏에 혼용하지 않습니다. 한 스냅숏에서의 CSV와 비 CSV 혼용은 지원되지 않습니다. 
> 
> 

StorSimple 스냅숏 관리자를 사용하여 전체 볼륨 그룹을 복원하거나, 개별 볼륨을 복제하고 개별 파일을 복구할 수 있습니다.

* [볼륨 및 볼륨 그룹](#volumes-and-volume-groups) 
* [Backup 유형 및 백업 정책](#backup-types-and-backup-policies) 

StorSimple 스냅숏 관리자 기능 및 사용 방법에 대한 자세한 내용은 [StorSimple 스냅숏 관리자 사용자 인터페이스](storsimple-use-snapshot-manager.md)를 참조하세요.

## <a name="volumes-and-volume-groups"></a>볼륨 및 볼륨 그룹
StorSimple 스냅숏 관리자를 사용하여 볼륨을 만든 다음 볼륨 그룹에 구성합니다. 

StorSimple Snapshot Manager는 볼륨 그룹을 사용하여 애플리케이션과 일관된 백업 사본을 만듭니다. 모든 관련 파일과 데이터베이스가 동기화되고 특정 시점에서 애플리케이션의 실제 상태를 나타날 때 애플리케이션이 일관성 있는 것입니다. 볼륨 그룹( *일관성 그룹*이라고도 함)은 백업 또는 복원 작업의 기초가 됩니다.

볼륨 그룹은 볼륨 컨테이너와는 다릅니다. 볼륨 컨테이너는 클라우드 저장소 계정과, 암호화 및 대역폭 소비 등과 같은 기타 특성을 공유합니다. 단일 볼륨 컨테이너는 최대 256개의 씬 프로비저닝된 StorSimple 볼륨을 포함할 수 있습니다. 볼륨 컨테이너에 대한 자세한 내용은 [볼륨 컨테이너 관리](storsimple-manage-volume-containers.md)에서 제공합니다. 볼륨 그룹은 백업 작업을 촉진하기 위해 구성할 수 있는 볼륨 컬렉션입니다. 다른 볼륨 컨테이너에 속한 두 볼륨을 선택하고, 두 항목을 단일 볼륨 그룹에 넣은 다음 해당 볼륨 그룹의 백업 정책을 만든 경우 각 볼륨은 해당 저장소 계정을 사용하여 적합한 볼륨 컨테이너에 백업됩니다.

> [!NOTE]
> 볼륨 그룹의 모든 볼륨은 단일 클라우드 서비스 공급자에서 가져와야 합니다.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Windows 볼륨 섀도 복사본 서비스와의 통합
StorSimple Snapshot Manager는 스냅숏이 애플리케이션과 일관된 데이터를 캡처하기 위해 Windows 볼륨 섀도 복사본 서비스(VSS)를 사용합니다. VSS는 VSS 인식 애플리케이션과의 통신을 통해 증분 스냅숏 생성을 조정하여 애플리케이션 일관성을 높입니다. VSS는 스냅숏을 만들 때 애플리케이션이 일시 비활성 상태인지 또는 정지되었는지 확인합니다. 

VSS의 StorSimple Snapshot Manager 구현은 SQL 서버 및 일반 NTFS 볼륨에서 작동합니다. 프로세스는 다음과 같습니다. 

1. 요청자(일반적으로 StorSimple Snapshot Manager 같은 데이터 관리 및 보호 솔루션)나 백업 애플리케이션이 VSS를 호출하고 대상 애플리케이션에서기록기 소프트웨어로부터 정보를 수집하도록 요청합니다.
2. VSS는 데이터 설명을 검색하는 기록기 요소에 연결합니다. 기록기가 백업 대상 데이터의 설명을 반환합니다. 
3. VSS가 애플리케이션 백업을 준비하기 위해 기록기에 신호를 보냅니다. 기록기는 열려 있는 트랜잭션을 완료하고, 트랜잭션 로그를 업데이트 하는 등의 작업을 수행하여 백업을 위해 데이터를 준비한 다음, VSS에 알립니다.
4. VSS는 기록기가 일시적으로 애플리케이션의 데이터 저장을 중지하도록 지시하고, 섀도 복사본을 만드는 동안 해당 볼륨에 어떤 데이터도 기록되지 않게 합니다. 이 단계로 데이터 일관성을 유지하며 60초 이하가 걸립니다.
5. VSS가 공급자에게 섀도 복사본을 만들도록 지시합니다. 소프트웨어 또는 하드웨어 기반의 공급자가, 현재 실행 중인 볼륨을 관리하며 요청에 따라 해당 볼륨의 섀도 복사본을 만듭니다. 공급자가 섀도 복사본을 만들고 완료되면 VSS에 알립니다.
6. VSS가 기록기에 연결하여 I/O를 재개할 수 있고, 섀도 복사본 생성 중에 I/O가 성공적으로 일시 중지되었음을 애플리케이션에 알립니다. 
7. 복사본을 성공적으로 만들었으면 VSS가 복사본 위치를 요청자에게 반환합니다. 
8. 섀도 복사본을 만드는 동안 데이터를 쓸 경우 백업 일관성이 없어집니다. VSS가 섀도 복사본을 지우고 요청자에게 알립니다. 요청자는 백업 프로세스를 자동으로 반복하거나 관리자에게 알려 나중에 다시 시도할 수 있습니다.

다음 그림을 참조하세요.

![VSS 프로세스](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows 볼륨 섀도 복사본 서비스 프로세스** 

## <a name="backup-types-and-backup-policies"></a>Backup 유형 및 백업 정책
StorSimple 스냅숏 관리자를 사용하여 데이터를 백업하고 로컬 및 클라우드에 저장할 수 있습니다. StorSimple 스냅숏 관리자를 사용하여 즉시 데이터를 백업하거나, 백업 정책을 사용하여 자동 백업 수행을 위한 일정을 만들 수 있습니다. Backup 정책을 통해 보유할 스냅숏 수를 지정할 수도 있습니다. 

### <a name="backup-types"></a>Backup 유형
StorSimple 스냅숏 관리자를 사용하여 다음 백업 유형을 만들 수 있습니다.

* **로컬 스냅숏** – 로컬 스냅숏은 StorSimple 디바이스에 저장되는 볼륨 데이터의 지정 시간 복사본입니다. 일반적으로 이러한 유형의 백업은 신속하게 만들고 복원할 수 있습니다. 로컬 스냅숏은 로컬 백업 사본처럼 사용할 수 있습니다.
* **클라우드 스냅숏** - 클라우드 스냅숏은 클라우드에 저장된 볼륨 데이터의 지정 시간 복사본입니다. 클라우드 스냅숏은 다른 오프 사이트 저장소 시스템에 복제된 스냅숏과 같습니다. 클라우드 스냅숏은 특히 재해 복구 상황에서 유용합니다.

### <a name="on-demand-and-scheduled-backups"></a>주문 및 예약 백업
StorSimple 스냅숏 관리자를 사용하여 즉시 생성되는 일회성 백업을 시작하거나, 백업 정책을 사용하여 반복 백업 작업을 예약할 수 있습니다.

백업 정책은 정기 백업 예약에 사용할 수 있는 자동 규칙 집합입니다. 백업 정책을 사용하면 특정 볼륨 그룹의 스냅숏을 만들기 위한 빈도와 매개 변수를 정의할 수 있습니다. 정책을 사용하면 로컬과 클라우드 스냅숏 모두에 대해 시작 및 만료 날짜, 시간, 빈도와 존 요구 사항을 지정할 수 있습니다. 정책은 정의한 직후에 적용됩니다. 

StorSimple 스냅숏 관리자를 사용하여 필요할 때마다 백업 정책을 구성 또는 재구성합니다. 

만드는 각 백업 정책에 대해 다음 정보를 구성합니다.

* **이름** – 선택한 백업 정책의 고유 이름입니다.
* **유형** – 백업 정책의 유형, 즉 로컬 스냅숏 또는 클라우드 스냅숏입니다.
* **볼륨 그룹** – 선택한 백업 정책이 할당된 볼륨 그룹입니다.
* **보존** – 보존할 백업 복사본의 수입니다. **모두** 확인란을 선택하면 볼륨당 최대 백업 복사본 수에 도달할 때까지 모든 백업 복사본을 유지하며, 이 시점이 되면 정책이 실패하고 오류 메시지가 생성됩니다. 또는 보존할 백업 수(1~64)를 지정할 수 있습니다.
* **날짜** – 백업 정책을 만든 날짜입니다.

백업 정책 구성에 대한 내용은 [StorSimple 스냅숏 관리자를 사용한 백업 정책 만들기 및 관리](storsimple-snapshot-manager-manage-backup-policies.md)에서 제공합니다.

### <a name="backup-job-monitoring-and-management"></a>Backup 작업 모니터링 및 관리
StorSimple 스냅숏 관리자를 사용하여 차후의 백업 작업, 예약된 백업 작업 및 완료된 백업 작업을 모니터링하고 관리할 수 있습니다. 또한 StorSimple 스냅숏 관리자에서는 최대 64개의 완료된 백업에 대한 카탈로그를 제공합니다. 카탈로그를 사용하여 볼륨 또는 개별 파일을 찾아 복원할 수 있습니다. 

백업 작업 모니터링에 대한 내용은 [StorSimple 스냅숏 관리자를 사용한 백업 작업 보기 및 관리](storsimple-snapshot-manager-manage-backup-jobs.md)에서 제공합니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅숏 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 자세히 알아봅니다.
* [StorSimple 스냅숏 관리자 다운로드](https://www.microsoft.com/download/details.aspx?id=44220)


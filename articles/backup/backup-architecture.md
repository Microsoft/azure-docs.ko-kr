---
title: 아키텍처 개요
description: Azure Backup 서비스에서 사용하는 아키텍처, 구성 요소 및 프로세스에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1081de6b467b896bd8cc62b84c9a67c329b11e02
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824035"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup 아키텍처 및 구성 요소

[Azure Backup 서비스](backup-overview.md) 를 사용 하 여 Microsoft Azure 클라우드 플랫폼으로 데이터를 백업할 수 있습니다. 이 문서에는 Azure Backup 아키텍처, 구성 요소 및 프로세스가 요약되어 있습니다.

## <a name="what-does-azure-backup-do"></a>Azure Backup의 기능은 무엇인가요?

Azure Backup은 온-프레미스 컴퓨터 및 Azure VM (가상 컴퓨터) 인스턴스에서 실행 되는 데이터, 컴퓨터 상태 및 작업을 백업 합니다. 여러 Azure Backup 시나리오가 있습니다.

## <a name="how-does-azure-backup-work"></a>Azure Backup은 어떻게 작동하나요?

여러 가지 방법을 사용 하 여 컴퓨터 및 데이터를 백업할 수 있습니다.

- **온-프레미스 머신 백업**:
  - MARS (Azure Backup Microsoft Azure Recovery Services) 에이전트를 사용 하 여 온-프레미스 Windows 컴퓨터를 Azure에 직접 백업할 수 있습니다. Linux 컴퓨터는 지원되지 않습니다.
  - 온-프레미스 컴퓨터를 백업 서버 (System Center Data Protection Manager (DPM) 또는 Microsoft Azure Backup 서버 (MABS)에 백업할 수 있습니다. 그런 다음 백업 서버를 Azure의 Recovery Services 자격 증명 모음에 백업할 수 있습니다.

- **Azure Vm 백업**:
  - Azure VM을 직접 백업할 수 있습니다. Azure Backup VM에서 실행 되는 Azure VM 에이전트에 대 한 백업 확장을 설치 합니다. 이 확장은 전체 VM을 백업합니다.
  - MARS 에이전트를 실행하여 Azure VM에서 특정 파일 및 폴더를 백업할 수 있습니다.
  - Azure Vm은 Azure에서 실행 되는 MABS에 백업할 수 있으며, 그런 다음, MABS를 Recovery Services 자격 증명 모음에 백업할 수 있습니다.

[백업할 수](backup-overview.md) 있는 기능과 [지원 되는 백업 시나리오](backup-support-matrix.md)에 대해 자세히 알아보세요.

## <a name="where-is-data-backed-up"></a>데이터는 어디에 백업되나요?

Azure Backup Recovery Services 자격 증명 모음에 백업 된 데이터를 저장 합니다. 자격 증명 모음은 백업 복사본, 복구 시점 및 백업 정책과 같은 데이터를 저장 하는 데 사용 되는 Azure의 온라인 저장소 엔터티입니다.

Recovery Services 자격 증명 모음에는 다음과 같은 기능이 있습니다.

- 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다.
- 각 Azure 구독에서 최대 500개의 자격 증명 모음을 만들 수 있습니다.
- Azure Vm 및 온-프레미스 컴퓨터를 포함 하 여 자격 증명 모음에서 백업 된 항목을 모니터링할 수 있습니다.
- Azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 사용 하 여 자격 증명 모음 액세스를 관리할 수 있습니다.
- 자격 증명 모음의 데이터가 중복성을 위해 복제되는 방법을 지정합니다.
  - **LRS (로컬 중복 저장소)**: 데이터 센터의 오류 로부터 보호 하기 위해 LRS를 사용할 수 있습니다. LRS는 스토리지 배율 단위에 데이터를 복제합니다. [자세히 알아보기](../storage/common/storage-redundancy.md).
  - **GRS (지역 중복 저장소)**: 지역 전체의 작동 중단을 방지 하기 위해 GRS를 사용할 수 있습니다. GRS은 데이터를 보조 지역으로 복제 합니다. [자세히 알아보기](../storage/common/storage-redundancy.md).
  - 기본적으로 Recovery Services 자격 증명 모음은 GRS를 사용 합니다.

## <a name="backup-agents"></a>백업 에이전트

Azure Backup는 백업 중인 컴퓨터의 유형에 따라 서로 다른 백업 에이전트를 제공 합니다.

**에이전트** | **세부 정보**
--- | ---
**MARS 에이전트** | <ul><li>는 개별 온-프레미스 Windows Server 컴퓨터에서 실행 되어 파일, 폴더 및 시스템 상태를 백업 합니다.</li> <li>Azure Vm에서를 실행 하 여 파일, 폴더 및 시스템 상태를 백업 합니다.</li> <li>Dpm/mabs 서버를 실행 하 여 DPM/MABS 로컬 저장소 디스크를 Azure에 백업 합니다.</li></ul>
**Azure VM 확장** | 자격 증명 모음에 백업하기 위해 Azure VM에서 실행됩니다.

## <a name="backup-types"></a>Backup 유형

다음 표에서는 다양 한 유형의 백업 및 사용 되는 경우를 설명 합니다.

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체** | 전체 백업은 전체 데이터 원본을 포함 합니다. 차등 또는 증분 백업 보다 더 많은 네트워크 대역폭을 사용 합니다. | 초기 백업에 사용됩니다.
**큰** |  차등 백업은 초기 전체 백업 이후 변경 된 블록을 저장 합니다. 는 더 적은 양의 네트워크와 저장소를 사용 하며 변경 되지 않은 데이터의 중복 복사본을 유지 하지 않습니다.<br/><br/> 이후 백업 간에 변경 되지 않은 데이터 블록은 전송 되 고 저장 되기 때문에 비효율적입니다. | Azure Backup에서 사용되지 않습니다.
**대비** | 증분 백업은 이전 백업 이후에 변경 된 데이터 블록만 저장 합니다. 스토리지 및 네트워크 효율성이 높습니다. <br/><br/> 증분 백업에서는 전체 백업을 보완 하지 않아도 됩니다. | DPM/MABS에서 디스크 백업에 사용되며 Azure에 대한 모든 백업에서 사용됩니다. SQL Server 백업에는 사용 되지 않습니다.

## <a name="sql-server-backup-types"></a>SQL Server 백업 유형

다음 표에서는 SQL Server 데이터베이스에 사용 되는 다양 한 유형의 백업 및 사용 빈도에 대해 설명 합니다.

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체 백업** | 전체 데이터베이스 백업은 전체 데이터베이스를 백업합니다. 특정 데이터베이스 또는 파일 그룹 또는 파일 집합에 있는 모든 데이터를 포함 합니다. 전체 백업에도 해당 데이터를 복구할 수 있는 충분 한 로그가 포함 되어 있습니다. | 많으면, 하루에 하나의 전체 백업을 트리거할 수 있습니다.<br/><br/> 매일 또는 매주 간격으로 전체 백업을 수행 하도록 선택할 수 있습니다.
**차등 백업** | 차등 백업은 가장 최근의 이전 전체 데이터 백업을 기반으로 합니다.<br/><br/> 이 백업은 전체 백업 이후 변경된 데이터만 캡처합니다. |  많으면, 하루에 하나의 차등 백업을 트리거할 수 있습니다.<br/><br/> 전체 백업과 차등 백업을 같은 날에 구성할 수 없습니다.
**트랜잭션 로그 백업** | 로그 백업을 사용하면 특정 시간(초 단위)까지의 특정 시점 복원이 가능합니다. | 많으면, 15분마다 트랜잭션 로그 백업을 구성할 수 있습니다.

### <a name="comparison-of-backup-types"></a>백업 유형 비교

스토리지 사용량, RTO(복구 시간 목표) 및 네트워크 사용량은 각 백업 유형마다 다릅니다. 다음 이미지는 백업 유형을 비교 하 여 보여 줍니다.

- 데이터 원본 A는 매월 백업 되는 10 개의 저장소 블록 A1-A10으로 구성 됩니다.
- 블록 A2, A3, A4 및 A9는 첫 번째 달에 변경되고 블록 A5는 다음 달에 변경됩니다.
- 차등 백업의 경우 두 번째 달에 변경된 블록 A2, A3, A4 및 A9가 백업됩니다. 세 번째 달에 이와 동일한 블록이 변경된 블록 A5와 함께 다시 백업됩니다. 변경된 블록은 다음 전체 백업이 발생할 때가지 계속 백업됩니다.
- 증분 백업의 경우 두 번째 달에는 블록 A2, A3, A4 및 A9가 변경 된 것으로 표시 되 고 전송 됩니다. 세 번째 달에는 변경된 블록 A5만 표시되고 전송됩니다.

![백업 방법의 비교를 보여 주는 이미지](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>백업 기능

다음 표에는 다양 한 유형의 백업에 대해 지원 되는 기능이 요약 되어 있습니다.

**기능** | **파일 및 폴더에 대 한 직접 백업 (MARS 에이전트 사용)** | **Azure VM 백업** | **DPM/MABS를 사용 하는 컴퓨터 또는 앱**
--- | --- | --- | ---
자격 증명 모음에 백업 | ![예][green] | ![예][green] | ![예][green]
DPM/MABS 디스크에 백업한 다음 Azure에 백업 | | | ![예][green]
백업을 위해 전송된 데이터 압축 | ![예][green] | 데이터를 전송할 때 압축이 사용되지 않습니다. 스토리지가 약간 증가하지만 복원 속도가 더 빠릅니다.  | ![예][green]
증분 백업 실행 |![예][green] |![예][green] |![예][green]
중복 제거된 디스크 백업 | | | ![부분적으로][yellow]<br/><br/> 온-프레미스에 배포된 DPM/MABS 서버에만 해당합니다.

![테이블 키](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>백업 정책 기본 정보

- 자격 증명 모음당 백업 정책을 만듭니다.
- Azure vm, azure vm의 SQL, azure vm 및 Azure 파일 공유의 SAP HANA 작업의 백업에 대 한 백업 정책을 만들 수 있습니다. Mars 에이전트를 사용 하는 파일 및 폴더 백업에 대 한 정책은 MARS 콘솔에 지정 됩니다.
  - Azure 파일 공유
- 다양한 리소스에 정책을 할당할 수 있습니다. 여러 Azure VM을 보호하는 데 Azure VM 백업 정책을 사용할 수 있습니다.
- 두 구성 요소로 이루어진 정책
  - 일정: 백업을 수행하는 경우
  - 보존: 각 백업을 보존해야 할 기간
- 특정 시점을 사용하여 "매일" 또는 "매주"로 일정을 정의할 수 있습니다.
- "매일", "매주", "매월", "매년" 단위로 백업 지점에 대한 보존을 정의할 수 있습니다.
  - "매주"는 특정 요일에 백업 하는 것을 의미 합니다.
  - "월간"는 해당 월의 특정 날짜에 백업을 의미 합니다.
  - "매년"은 특정 연도의 특정 날에 백업 하는 것을 의미 합니다.
- "월간", "매년" 백업 지점의 보존은 LTR (장기 보존) 이라고 합니다.
- 자격 증명 모음을 만들면 "DefaultPolicy"도 만들어지고 리소스를 백업 하는 데 사용할 수 있습니다.
- 백업 정책의 보존 기간에 대 한 변경 내용은 새 복구 지점이 아닌 모든 이전 복구 소급 적용 됩니다.

### <a name="additional-reference"></a>추가 참조

- Azure VM 컴퓨터: 정책을 [만들고](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) [수정](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) 하는 방법입니다.
- Azure VM 컴퓨터의 SQL Server 데이터베이스: 정책을 [만들고](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) [수정](./manage-monitor-sql-database-backup.md#modify-policy) 하는 방법입니다.
- Azure 파일 공유: 정책을 [만들고](./backup-afs.md) [수정](./manage-afs-backup.md#modify-policy) 하는 방법입니다.
- SAP HANA: 정책을 [만들고](./backup-azure-sap-hana-database.md#create-a-backup-policy) [수정](./sap-hana-db-manage.md#change-policy) 하는 방법입니다.
- MARS: 정책을 [만들고](./backup-windows-with-mars-agent.md#create-a-backup-policy) [수정](./backup-azure-manage-mars.md#modify-a-backup-policy) 하는 방법입니다.
- [워크 로드 유형에 따라 백업 예약에 대 한 제한이 있나요?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [보존 정책을 변경하는 경우 기존 복구 지점은 어떻게 되나요?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>아키텍처: 기본 제공 Azure VM 백업

1. Azure VM에 대 한 백업을 사용 하도록 설정 하면 지정한 일정에 따라 백업이 실행 됩니다.
1. 첫 번째 백업 중에 VM이 실행 되는 경우 백업 확장이 VM에 설치 됩니다.
    - Windows VM의 경우 VMSnapshot 확장이 설치됩니다.
    - Linux Vm의 경우 VMSnapshot Linux 확장이 설치 됩니다.
1. 확장은 저장소 수준 스냅숏을 사용 합니다.
    - 을 실행 하는 Windows Vm의 경우 VSS (Windows 볼륨 섀도 복사본 서비스)를 사용 하 여 VM의 앱 일치 스냅숏을 사용 합니다. 기본적으로 백업은 전체 VSS 백업을 수행 합니다. Backup에서 앱 일관성이 있는 스냅샷을 만들 수 없는 경우에는 일관성 있는 파일 스냅샷을 만듭니다.
    - Linux Vm의 경우 Backup은 파일에 일관 된 스냅숏을 사용 합니다. 앱 일치 스냅숏의 경우 사전/사후 스크립트를 수동으로 사용자 지정 해야 합니다.
    - 각 VM 디스크를 병렬로 백업하여 백업이 최적화됩니다. 백업 중인 각 디스크에 대해 Azure Backup은 디스크의 블록을 읽고 변경된 데이터만 저장합니다.
1. 스냅샷을 만든 후에는 데이터가 자격 증명 모음으로 전송됩니다.
    - 마지막 백업 이후에 변경 된 데이터 블록만 복사 됩니다.
    - 데이터가 암호화되지 않습니다. Azure Backup Azure Disk Encryption를 사용 하 여 암호화 된 Azure Vm을 백업할 수 있습니다.
    - 스냅샷 데이터가 자격 증명 모음에 즉시 복사되지 않을 수도 있습니다. 사용량이 많은 시간에 백업은 약간의 시간이 걸릴 수 있습니다. 일별 백업 정책에서 VM의 총 백업 시간은 24시간 미만입니다.
1. 자격 증명 모음에 데이터가 전송 된 후에는 복구 지점이 생성 됩니다. 기본적으로 스냅숏은 삭제 되기 2 일 동안 보존 됩니다. 이 기능을 통해 이러한 스냅숏에서 복원 작업을 수행할 수 있으므로 복원 시간이 줄어듭니다. 자격 증명 모음에서 데이터를 다시 변환 하 고 복사 하는 데 필요한 시간을 줄일 수 있습니다. [Azure Backup 인스턴트 복원 기능](./backup-instant-restore-capability.md)을 참조 하세요.

Azure Vm을 백업 하기 위해 인터넷 연결을 명시적으로 허용 하지 않아도 됩니다.

![Azure VM의 백업](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>아키텍처: 온-프레미스 Windows Server 컴퓨터 또는 Azure VM 파일 또는 폴더의 직접 백업

1. 시나리오를 설정 하려면 컴퓨터에 MARS 에이전트를 다운로드 하 여 설치 합니다. 그런 다음 백업할 항목, 백업 실행 시기 및 Azure에 유지 되는 기간을 선택 합니다.
1. 초기 백업은 백업 설정에 따라 실행 됩니다.
1. MARS 에이전트는 VSS를 사용 하 여 백업을 위해 선택한 볼륨의 특정 시점 스냅숏을 만듭니다.
    - MARS 에이전트는 Windows 시스템 쓰기 작업만 사용 하 여 스냅숏을 캡처합니다.
    - 에이전트는 응용 프로그램 VSS 기록기를 사용 하지 않으므로 앱 일치 스냅숏을 캡처하지 않습니다.
1. VSS를 사용 하 여 스냅숏을 만든 후 MARS 에이전트는 백업을 구성할 때 지정한 캐시 폴더에 VHD (가상 하드 디스크)를 만듭니다. 에이전트는 또한 각 데이터 블록에 대 한 체크섬을 저장 합니다.
1. 요청 시 백업을 실행 하지 않는 한 증분 백업은 지정한 일정에 따라 실행 됩니다.
1. 증분 백업에서는 변경된 파일이 식별되고 새 VHD가 만들어집니다. VHD는 압축 되 고 암호화 된 후 자격 증명 모음으로 전송 됩니다.
1. 증분 백업이 완료 된 후에는 새 VHD가 초기 복제 후 생성 된 VHD와 병합 됩니다. 이 병합 된 VHD는 진행 중인 백업을 비교 하는 데 사용할 최신 상태를 제공 합니다.

![MARS 에이전트를 사용 하 여 온-프레미스 Windows Server 컴퓨터의 백업](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>아키텍처: DPM/MABS에 백업

1. 보호 하려는 컴퓨터에 DPM 또는 MABS 보호 에이전트를 설치 합니다. 그런 다음 DPM 보호 그룹에 컴퓨터를 추가 합니다.
    - 온-프레미스 머신을 보호하려면 DPM 또는 MABS 서버가 온-프레미스에 있어야 합니다.
    - Azure VM을 보호하려면 Azure VM으로 실행되는 MABS 서버가 Azure에 있어야 합니다.
    - DPM/MABS를 사용 하 여 백업 볼륨, 공유, 파일 및 폴더를 보호할 수 있습니다. 또한 컴퓨터의 시스템 상태 (운영 체제 미 설치)를 보호 하 고 앱 인식 백업 설정을 사용 하 여 특정 앱을 보호할 수 있습니다.
1. DPM/MABS에서 컴퓨터 또는 앱에 대 한 보호를 설정 하는 경우 단기 저장소 및 온라인 보호를 위해 Azure에 대 한 MABS/DPM 로컬 디스크에 백업 하도록 선택 합니다. 또한 로컬 DPM/MABS 저장소에 대 한 백업을 실행 해야 하는 경우와 Azure에 대 한 온라인 백업을 실행 해야 하는 경우를 지정 합니다.
1. 보호 되는 작업의 디스크는 지정 된 일정에 따라 로컬 MABS/DPM 디스크에 백업 됩니다.
1. Dpm/MABS 디스크는 DPM/MABS 서버에서 실행 중인 MARS 에이전트에 의해 자격 증명 모음에 백업 됩니다.

![DPM 또는 MABS로 보호 되는 컴퓨터 및 워크 로드 백업](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM 스토리지

Azure VM은 디스크를 사용하여 운영 체제, 앱 및 데이터를 저장합니다. 각 Azure VM에는 두 개 이상의 디스크 (운영 체제용 디스크 및 임시 디스크)가 있습니다. Azure Vm에는 앱 데이터에 대 한 데이터 디스크도 있을 수 있습니다. 디스크는 VHD로 저장됩니다.

- Vhd는 Azure의 표준 또는 프리미엄 저장소 계정에 페이지 blob으로 저장 됩니다.
  - **표준 저장소:** 대기 시간이 중요 하지 않은 워크 로드를 실행 하는 Vm에 대 한 안정적이 고 경제적인 디스크 지원입니다. Standard storage는 표준 SSD (반도체 드라이브) 디스크 또는 표준 HDD (하드 디스크 드라이브) 디스크를 사용할 수 있습니다.
  - **Premium storage:** 고성능 디스크 지원. 프리미엄 SSD 디스크를 사용합니다.
- 디스크에는 다음과 같은 여러 성능 계층이 있습니다.
  - **표준 HDD 디스크:** Hdd에서 지원 되며 비용 효율적인 저장소에 사용 됩니다.
  - **표준 SSD 디스크:** 프리미엄 SSD 디스크와 표준 HDD 디스크의 요소를 결합 합니다. 는 HDD 보다 더 일관 된 성능과 안정성을 제공 하지만 여전히 비용 효율적입니다.
  - **프리미엄 SSD 디스크:** Ssd에서 지원 되며 i/o를 많이 사용 하는 워크 로드를 실행 하는 Vm에 대해 고성능 및 낮은 대기 시간을 제공 합니다.
- 다음과 같은 관리 또는 비관리 디스크가 있습니다.
  - **관리 되지 않는 디스크:** Vm에서 사용 하는 기존의 디스크 유형입니다. 이러한 디스크의 경우 사용자 고유의 스토리지 계정을 만든 다음, 디스크를 만들 때 지정합니다. 그런 다음 Vm에 대 한 저장소 리소스를 최대화 하는 방법을 파악 해야 합니다.
  - **관리 디스크:** Azure에서 저장소 계정을 만들고 관리 합니다. 디스크 크기와 성능 계층을 지정 하면 Azure에서 관리 디스크를 만듭니다. 디스크를 추가 하 고 Vm을 확장 하면 Azure에서 저장소 계정을 처리 합니다.

디스크 저장소 및 Vm의 사용 가능한 디스크 유형에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Linux Vm에 대 한 Azure managed disks](../virtual-machines/managed-disks-overview.md)
- [Vm에 사용 가능한 디스크 유형](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Premium storage를 사용 하 여 Azure Vm 백업 및 복원

Azure Backup에서 premium storage를 사용 하 여 Azure Vm을 백업할 수 있습니다.

- Premium storage를 사용 하 여 Vm을 백업 하는 과정에서 백업 서비스는 저장소 계정에 *azurebackup-* 이라는 임시 준비 위치를 만듭니다. 준비 위치의 크기는 복구 지점 스냅숏의 크기와 같습니다.
- Premium Storage 계정에 임시 준비 위치를 수용할 충분한 여유 공간이 있어야 합니다. 자세한 내용은 [프리미엄 페이지 blob storage 계정에 대 한 확장성 목표](../storage/blobs/scalability-targets-premium-page-blobs.md)를 참조 하세요. 준비 위치를 수정하지 마세요.
- 백업 작업이 완료되면 준비 위치가 삭제됩니다.
- 준비 위치에 사용되는 스토리지의 가격은 모든 [Premium Storage 가격 책정](../virtual-machines/disks-types.md#billing)과 일관성이 있습니다.

Premium storage를 사용 하 여 Azure Vm을 복원 하는 경우 프리미엄 또는 표준 저장소로 복원할 수 있습니다. 일반적으로 premium storage로 복원 합니다. 그러나 VM에서 파일의 하위 집합만 필요한 경우 표준 저장소로 복원 하는 것이 비용 효율적일 수 있습니다.

### <a name="back-up-and-restore-managed-disks"></a>관리 디스크 백업 및 복원

관리 디스크를 사용 하 여 Azure Vm을 백업할 수 있습니다.

- 다른 Azure VM과 동일한 방식으로 관리 디스크를 사용하여 VM을 백업합니다. 가상 머신 설정에서 직접 VM을 백업하거나, Recovery Services 자격 증명 모음에서 VM에 대한 백업을 사용하도록 설정할 수 있습니다.
- Managed Disks에 VM 백업은 Managed Disks를 기반으로 구축된 RestorePoint 컬렉션을 통해 지원됩니다.
- Azure Backup은 Azure Disk Encryption를 사용 하 여 암호화 된 관리 디스크를 사용 하 여 Vm을 백업 하는 기능도 지원 합니다.

관리 디스크를 사용 하 여 Vm을 복원 하는 경우 관리 되는 디스크를 사용 하는 전체 VM 또는 저장소 계정으로 복원할 수 있습니다.

- 복원 프로세스 중에 Azure는 관리 디스크를 처리 합니다. 저장소 계정 옵션을 사용 하는 경우 복원 프로세스 중에 생성 되는 저장소 계정을 관리 합니다.
- 암호화 된 관리 되는 VM을 복원 하는 경우 복원 프로세스를 시작 하기 전에 키 자격 증명 모음에 VM의 키 및 비밀이 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- 지원 매트릭스를 검토 하 여 [백업 시나리오에 대해 지원 되는 기능 및 제한 사항에 대해 알아보세요](backup-support-matrix.md).
- 다음 시나리오 중 하나에 대 한 백업을 설정 합니다.
  - [Azure VM 백업](backup-azure-arm-vms-prepare.md):
  - 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
  - Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
  - Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

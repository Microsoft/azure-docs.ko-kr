---
title: Azure VMware 솔루션에 대 한 Azure Backup Server 설정
description: Azure Backup Server를 사용 하 여 가상 컴퓨터를 백업 하도록 Azure VMware 솔루션 환경을 설정 합니다.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 0dd2b16254e697a08d0ff542a5ddcb3fc7e4103d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750624"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Azure VMware 솔루션에 대 한 Azure Backup Server 설정

Azure Backup Server는 BCDR (비즈니스 연속성 및 재해 복구) 전략에 기여 하는 강력한 엔터프라이즈 백업 및 복구 시스템입니다. Azure VMware 솔루션 미리 보기 중에 Azure Backup Server를 사용 하 여 VM (가상 머신) 수준 백업만 구성할 수 있습니다. 

백업 데이터를 저장할 수 있는 Azure Backup Server:

- **디스크**: 단기 저장소의 경우 Azure Backup Server는 데이터를 디스크 풀에 백업 합니다.
- **Azure**: 단기 및 장기 저장소 오프-프레미스의 경우 Azure Backup를 사용 하 여 디스크 풀에 저장 된 Azure Backup Server 데이터를 Microsoft Azure 클라우드에 백업할 수 있습니다.

작동 중단이 발생 하 고 원본 데이터를 사용할 수 없는 경우 Azure Backup Server를 사용 하 여 데이터를 원본 또는 대체 위치로 쉽게 복원할 수 있습니다. 이렇게 하면 계획 되거나 예기치 않은 문제로 인해 원본 데이터를 사용할 수 없는 경우 데이터를 대체 위치로 쉽게 복원할 수 있습니다.

이 문서에서는 Azure Backup Server를 사용 하 여 Vm을 백업 하도록 Azure VMware 솔루션 환경을 준비 하는 방법을 설명 합니다. 다음 단계를 안내 합니다. 

> [!div class="checklist"]
> * 권장 VM 디스크 유형 및 사용할 크기를 결정 합니다.
> * 복구 지점이 저장 되는 Recovery Services 자격 증명 모음을 만듭니다.
> * Recovery Services 자격 증명 모음에 대 한 저장소 복제를 설정 합니다.
> * Azure Backup Server에 저장소를 추가 합니다.

## <a name="supported-vmware-features"></a>지원되는 VMware 기능

- **에이전트 없는 백업:** 가상 컴퓨터를 백업 하려면 vCenter 또는 ESXi 서버에 에이전트를 설치 하지 않아도 됩니다. Azure Backup Server 대신 Azure Backup Server를 사용 하 여 VMware 서버를 인증 하는 데 사용 되는 IP 주소 또는 FQDN (정규화 된 도메인 이름) 및 로그인 자격 증명을 제공 하면 됩니다.
- **클라우드 통합 백업:** Azure Backup Server는 디스크 및 클라우드로 워크 로드를 보호 합니다. Azure Backup Server의 백업 및 복구 워크플로를 사용 하 여 장기 보존 및 오프 사이트 백업을 관리할 수 있습니다.
- **VCenter에서 관리 하는 vm을 검색 하 고 보호 합니다.** Azure Backup Server vCenter 또는 ESXi 서버에 배포 된 Vm을 검색 하 고 보호 합니다. 또한 Azure Backup Server는 vCenter에서 관리 하는 Vm을 검색 하 여 대량 배포를 보호할 수 있도록 합니다.
- **폴더 수준 autoprotection:** vCenter를 사용 하면 vm 폴더에서 vm을 구성할 수 있습니다. Azure Backup Server는 이러한 폴더를 검색 하 고이를 사용 하 여 모든 하위 폴더를 포함 하는 폴더 수준에서 Vm을 보호할 수 있습니다. 폴더를 보호 하는 경우 해당 폴더의 Vm을 보호할 뿐만 아니라 나중에 추가 된 Vm도 보호 합니다 Azure Backup Server. Azure Backup Server는 매일 새 Vm을 검색 하 고 자동으로 보호 합니다. 재귀 폴더에서 Vm을 구성 하는 경우 Azure Backup Server는 재귀 폴더에 배포 된 새 Vm을 자동으로 검색 하 고 보호 합니다.
- **Azure Backup Server는 클러스터 내에서 VMotioned vm을 계속 보호 합니다.** Vm은 클러스터 내에서 부하 분산을 위해 vMotioned 자동으로 VM 보호 Azure Backup Server를 검색 하 고 계속 합니다.
- **필요한 파일을 더 빠르게 복구:** 전체 VM을 복구 하지 않고 Windows VM에서 파일 또는 폴더를 복구할 수 Azure Backup Server.

## <a name="limitations"></a>제한 사항

- Azure Backup Server v3의 업데이트 롤업 1을 설치 해야 합니다.
- 첫 번째 Azure Backup Server 백업 하기 전에 사용자 스냅숏을 백업할 수 없습니다. Azure Backup Server 첫 번째 백업을 완료 한 후 사용자 스냅숏을 백업할 수 있습니다.
- Azure Backup Server 통과 디스크와 물리적 원시 장치 매핑 (pRDMs)을 사용 하 여 VMware Vm을 보호할 수 없습니다.
- Azure Backup Server VMware vApps를 검색 하거나 보호할 수 없습니다.

Azure VMware 솔루션에 대 한 Azure Backup Server를 설정 하려면 다음 단계를 완료 해야 합니다.

- 필수 구성 요소 및 환경을 설정 합니다.
- Recovery Services 자격 증명 모음을 만듭니다.
- Azure Backup Server를 다운로드 하 고 설치 합니다.
- Azure Backup Server에 저장소를 추가 합니다.

### <a name="deployment-architecture"></a>배포 아키텍처

Azure Backup Server는 azure VMware 솔루션 Vm을 보호 하기 위해 Azure IaaS (infrastructure as a service) VM으로 배포 됩니다.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Azure Backup Server는 azure VMware 솔루션 Vm을 보호 하기 위해 Azure IaaS (infrastructure as a service) VM으로 배포 됩니다." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure Backup Server 환경의 필수 구성 요소

Azure 환경에 Azure Backup Server를 설치 하는 경우이 섹션의 권장 사항을 고려 합니다.

### <a name="azure-virtual-network"></a>Azure Virtual Network

[Azure에서 VMware 사설 클라우드에 대 한 네트워킹을 구성](tutorial-configure-networking.md)했는지 확인 합니다.

### <a name="determine-the-size-of-the-virtual-machine"></a>가상 컴퓨터의 크기를 확인 합니다.

이전 단계에서 만든 가상 네트워크에 Windows 가상 머신을 만들어야 합니다. Azure Backup Server를 실행 하기 위한 서버를 선택 하는 경우 Windows Server 2019 Datacenter의 갤러리 이미지를 사용 하 여 시작 합니다. [Azure Portal에서 첫 번째 Windows 가상 컴퓨터 만들기](../virtual-machines/windows/quick-create-portal.md) 자습서에서는 azure를 사용한 적이 없는 경우에도 azure에서 권장 VM으로 시작 합니다.

다음 표에는 각 Azure Backup Server 가상 컴퓨터 크기에 대 한 보호 된 작업의 최대 수가 요약 되어 있습니다. 이 정보는 작업 크기 및 변동의 정식 값을 사용한 내부 성능 및 확장 테스트를 기준으로 한 결과입니다. 실제 작업 크기는 클 수 있지만 Azure Backup Server 가상 컴퓨터에 연결 된 디스크에서 수용 해야 합니다.

| 최대 보호 된 작업 | 평균 작업 크기 | 평균 작업 변동(매일) | 최소 저장소 IOPS | 권장 디스크 유형/크기      | 권장 VM 크기 |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100GB                | 순 변동 5%                   | 2,000             | 표준 HDD (디스크당 8 TB 이상 크기)  | A4V2       |
| 40                      | 150GB                | 순 변동 10%                  | 4,500             | 프리미엄 SSD * (디스크당 1tb 이상 크기) | DS3_V2     |
| 60                      | 200GB                | 순 변동 10%                  | 10500            | 프리미엄 SSD * (디스크당 8 TB 이상 크기) | DS3_V2     |

* 필요한 IOPs를 얻으려면 최소 권장 크기 또는 더 높은 크기의 디스크를 사용 합니다. 크기가 작은 디스크는 더 낮은 IOPs를 제공 합니다.

> [!NOTE]
> Azure Backup Server는 단일 용도의 전용 서버에서 실행하도록 설계되었습니다. 다음을 수행 하는 컴퓨터에 Azure Backup Server을 설치할 수 없습니다.
> * 는 도메인 컨트롤러로 실행 됩니다.
> * 에는 응용 프로그램 서버 역할이 설치 되어 있습니다.
> * 는 System Center Operations Manager 관리 서버입니다.
> * Exchange Server를 실행 합니다.
> * 는 클러스터의 노드입니다.

### <a name="disks-and-storage"></a>디스크 및 스토리지

Azure Backup Server에는 설치를 위한 디스크가 필요 합니다. 여기에는 시스템 파일, 설치 파일, 필수 구성 요소 소프트웨어, 데이터베이스 파일, 저장소 풀의 전용 디스크가 포함 됩니다.

| 요구 사항                      | 권장 크기  |
|----------------------------------|-------------------------|
| Azure Backup Server 설치                | 설치 위치: 3gb<br />데이터베이스 파일 드라이브: 900MB<br />시스템 드라이브: SQL Server 설치를 위한 1GB<br /><br />또한 보관할 때 파일 카탈로그를 임시 설치 위치에 복사 하는 Azure Backup Server에 대 한 공간이 필요 합니다.      |
| 스토리지 풀용 디스크<br />(기본 볼륨 사용, 동적 디스크에 있을 수 없음) | 보호 된 데이터 크기의 2 ~ 3 배입니다.<br />자세한 저장소 계산은 [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)를 참조 하세요.   |

새 관리 되는 데이터 디스크를 기존 Azure VM에 연결 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 WINDOWS VM에 관리 되는 데이터 디스크 연결](../virtual-machines/windows/attach-managed-disk-portal.md)을 참조 하세요.

> [!NOTE]
> 단일 Azure Backup Server는 저장소 풀에 대해 120 TB의 소프트 제한이 있습니다.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>로컬 디스크 및 Azure에 백업 데이터 저장

Azure에 백업 데이터를 저장 하면 Azure Backup Server VM의 백업 인프라가 줄어듭니다. 운영 복구 (백업)의 경우 Azure Backup Server는 VM에 연결 된 Azure 디스크에 백업 데이터를 저장 합니다. 디스크 및 저장소 공간이 VM에 연결 된 후 Azure Backup Server는 저장소를 관리 합니다. 백업 데이터 저장소의 양은 각 Azure VM에 연결 된 디스크의 수와 크기에 따라 달라 집니다. Azure VM의 각 크기에는 연결할 수 있는 최대 디스크 수가 있습니다. 예를 들어 A2는 네 개의 디스크이 고, A3은 8 개의 디스크 이며, A4는 디스크 16 개입니다. 또한 디스크의 크기와 수에 따라 총 백업 저장소 풀 용량이 결정 됩니다.

> [!IMPORTANT]
> 5 일 넘게 Azure Backup Server 연결 된 디스크에는 작업 복구 데이터를 보존 *하지* 않아야 합니다. 데이터가 5 일 보다 오래 된 경우 Recovery Services 자격 증명 모음에 저장 합니다.

Azure에서 백업 데이터를 저장하려면 Recovery Services 자격 증명 모음을 만들거나 사용합니다. Azure Backup Server 워크 로드를 백업 하기 위해 준비 하는 경우 [Recovery Services 자격 증명 모음을 구성](#create-a-recovery-services-vault)합니다. 일단 구성 되 면 온라인 백업 작업이 실행 될 때마다 자격 증명 모음에 복구 지점이 생성 됩니다. 각 Recovery Services 자격 증명 모음에는 최대 9999 개의 복구 지점이 포함 됩니다. 생성 된 복구 지점의 수와 보존 기간에 따라 여러 해 동안 백업 데이터를 보존할 수 있습니다. 예를 들어 월별 복구 지점이 생성 되 고 5 년 동안 보존 될 수 있습니다.

> [!IMPORTANT]
> Azure에 백업 데이터를 보내거나 로컬로 유지할지 여부에 관계 없이 Recovery Services 자격 증명 모음에 Azure Backup Server을 등록 해야 합니다.

### <a name="scale-deployment"></a>배포 크기 조정

배포의 크기를 조정하려는 경우 다음 옵션을 사용할 수 있습니다.

- 강화: 시리즈에서 DS3 시리즈로 Azure Backup Server VM의 크기를 늘리고 로컬 저장소를 **늘립니다.**
- **데이터 오프 로드**: 오래 된 데이터를 Azure로 전송 하 고 Azure Backup Server 컴퓨터에 연결 된 저장소에 최신 데이터만 보존 합니다.
- **규모 확장**: 워크 로드를 보호 하기 위해 더 많은 Azure Backup Server 컴퓨터를 추가 합니다.

### <a name="net-framework"></a>.NET Framework

VM에 .NET Framework 3.5 SP1 이상이 설치 되어 있어야 합니다.

### <a name="join-a-domain"></a>도메인에 가입

Azure Backup Server VM을 도메인에 가입 해야 하 고, VM에 대 한 관리자 권한이 있는 도메인 사용자가 Azure Backup Server을 설치 해야 합니다.

미리 보기 시점에는 지원 되지 않지만 Azure VM에 배포 된 Azure Backup Server는 Azure VMware 솔루션의 Vm에서 워크 로드를 백업할 수 있습니다. 작업은 백업 작업을 사용 하도록 설정 하기 위해 동일한 도메인에 있어야 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 시간에 따라 생성 된 복구 위치를 저장 하는 저장소 엔터티입니다. 또한 보호 된 항목과 연결 된 백업 정책도 포함 됩니다.

1. [Azure Portal](https://portal.azure.com/)에서 구독에 로그인합니다.

1. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.

   ![왼쪽 메뉴에서 모든 서비스를 선택 합니다.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. **모든 서비스** 대화 상자에서 **Recovery Services** 를 입력 하 고 목록에서 **Recovery Services 자격 증명 모음** 을 선택 합니다.

   ![을 입력 하 고 Recovery Services 자격 증명 모음을 선택 합니다.](../backup/media/backup-create-rs-vault/all-services.png)

   구독의 Recovery Services 자격 증명 모음 목록이 표시됩니다.

1. **Recovery Services 자격 증명 모음** 대시보드에서 **추가**를 선택합니다.

   ![Recovery Services 자격 증명 모음을 추가 합니다.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   **Recovery Services 자격 증명 모음** 대화 상자가 열립니다.

1. **이름**, **구독**, **리소스 그룹**및 **위치**에 대 한 값을 입력 합니다.

   ![Recovery Services 자격 증명 모음을 구성 합니다.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**: 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 고유해야 합니다. 최소 50 2 개 이상의 문자를 포함 하는 이름을 지정 합니다. 이름은 문자로 시작해야 하며, 문자, 숫자, 하이픈만 포함할 수 있습니다.
   - **구독**: 사용할 구독을 선택합니다. 단일 구독의 멤버인 경우 해당 이름이 표시됩니다. 사용할 구독을 잘 모르는 경우 기본(제안된) 구독을 사용합니다. 회사 또는 학교 계정이 둘 이상의 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
   - **리소스 그룹**: 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. 구독에서 사용 가능한 리소스 그룹 목록을 보려면 **기존 그룹 사용**을 선택한 다음, 드롭다운 목록에서 리소스를 선택합니다. 새 리소스 그룹을 만들려면 **새로 만들기**를 선택하고 이름을 입력합니다.
   - **위치**: 자격 증명 모음에 대한 지리적 지역을 선택합니다. Azure VMware 솔루션 가상 머신을 보호 하는 자격 증명 모음을 만들려면 Azure VMware 솔루션 사설 클라우드와 동일한 지역에 자격 증명 모음이 *있어야* 합니다.

1. Recovery Services 자격 증명 모음을 만들 준비가 되면 **만들기**를 선택합니다.

   ![Recovery Services 자격 증명 모음을 만듭니다.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Recovery Services 자격 증명 모음을 만드는 데 어느 정도 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 모퉁이에 있는 **알림** 영역에서 상태 알림을 모니터링 합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에서 볼 수 있습니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

   ![백업 자격 증명 모음 목록을 새로 고칩니다.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>스토리지 복제 설정

저장소 복제 옵션을 사용 하면 지역 중복 저장소 (기본값)와 로컬 중복 저장소 중에서 선택할 수 있습니다. 지역 중복 저장소는 저장소 계정의 데이터를 보조 지역으로 복사 하 여 데이터를 지속적으로 만듭니다. 로컬 중복 저장소는 내구성이 크지 않은 저렴 한 옵션입니다. 지역 중복 및 로컬 중복 저장소 옵션에 대해 자세히 알아보려면 [Azure Storage 중복성](../storage/common/storage-redundancy.md)을 참조 하세요.

> [!IMPORTANT]
> 자격 증명 모음에서 백업을 구성 하기 전에 Recovery Services 자격 증명 모음에 대 한 **저장소 복제 유형 로컬 중복/지역 중복** 설정을 변경 해야 합니다. 백업을 구성한 후에는이를 수정 하는 옵션을 사용할 수 없으며 저장소 복제 유형을 변경할 수 없습니다.

1. **Recovery Services 자격**증명 모음에서 새 자격 증명 모음을 선택 합니다. 

1. **설정** 아래에서 **속성**을 선택합니다. **백업 구성**에서 **업데이트**를 선택 합니다.

1. 저장소 복제 유형을 선택 하 고 **저장**을 선택 합니다.

   ![새 자격 증명 모음에 대 한 저장소 구성을 설정 합니다.](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-the-software-package"></a>소프트웨어 패키지 다운로드 및 설치

이 섹션의 단계에 따라 소프트웨어 패키지를 다운로드, 추출 및 설치 합니다.

### <a name="download-the-software-package"></a>소프트웨어 패키지 다운로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Recovery Services 자격 증명 모음이 이미 열려 있는 경우 다음 단계를 계속 진행 합니다. Recovery Services 자격 증명 모음이 열려 있지 않지만 Azure Portal에 있는 경우 주 메뉴에서 **찾아보기**를 선택 합니다.

   1. 리소스 목록에서 **Recovery Services**를 입력합니다.

   1. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services 자격 증명 모음**이 표시되면 이를 선택합니다.

   ![Recovery Services 자격 증명 모음 만들기 1 단계](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Recovery Services 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

   선택한 자격 증명 모음 대시보드가 열립니다.

   ![선택한 자격 증명 모음 대시보드가 열립니다.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   **설정** 옵션은 기본적으로 열립니다. 닫혀 있는 경우 **설정** 을 선택 하 여 엽니다.

   ![설정 옵션은 기본적으로 열립니다. 닫혀 있는 경우 설정을 선택 하 여 엽니다.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. **백업** 을 선택 하 여 **시작** 마법사를 엽니다.

   ![백업을 선택 하 여 시작 마법사를 엽니다.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. 열리는 창에서 다음을 수행 합니다.

   1. **워크 로드가 실행 되는 위치** 메뉴에서 **온-프레미스**를 선택 합니다.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="워크 로드가 실행 되는 위치":::

   1. **백업할 항목** 을 선택 하십시오. 메뉴에서 Azure Backup Server를 사용 하 여 보호 하려는 작업을 선택 합니다.

   1. **인프라 준비** 를 선택 하 Azure Backup Server 및 자격 증명 모음 자격 증명을 다운로드 하 고 설치 합니다.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="인프라 준비":::

1. **인프라 준비** 창이 열리면 다음을 수행 합니다.

   1. **다운로드** 링크를 선택 하 Azure Backup Server를 설치 합니다.

   1. **이미 다운로드 했거나 최신 Azure Backup Server 설치** 확인란을 선택 하 여 자격 증명 모음 자격 증명을 다운로드 한 다음 **다운로드**를 선택 합니다. Azure Backup Server를 Recovery Services 자격 증명 모음에 등록 하는 동안 자격 증명 모음 자격 증명을 사용 합니다. 링크를 누르면 소프트웨어 패키지를 다운로드 하는 다운로드 센터로 이동 합니다.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="인프라 준비-Azure Backup Server":::

1. 다운로드 페이지에서 모든 파일을 선택 하 고 **다음**을 선택 합니다.

   > [!NOTE]
   > 모든 파일을 동일한 폴더에 다운로드 해야 합니다. 파일의 다운로드 크기는 3gb 보다 크므로 다운로드를 완료 하는 데 최대 60 분이 걸릴 수 있습니다. 

   ![다운로드 페이지에서 모든 파일을 선택 하 고 다음을 선택 합니다.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>소프트웨어 패키지 추출

다른 서버에 소프트웨어 패키지를 다운로드 한 경우 Azure Backup Server를 배포 하기 위해 만든 가상 컴퓨터에 파일을 복사 합니다.

> [!WARNING]
> 설치 파일을 추출하려면 4GB 이상의 사용 가능한 공간이 필요합니다.

1. 모든 파일을 다운로드 한 후 **MicrosoftAzureBackupInstaller.exe** 를 두 번 클릭 하 여 **Microsoft Azure Backup** 설치 마법사를 열고 **다음**을 선택 합니다.

1. 파일의 압축을 풀 위치를 선택 하 고 **다음**을 선택 합니다.

1. 추출 **을 선택 하** 여 추출 프로세스를 시작 합니다.

   ![추출을 선택 하 여 추출 프로세스를 시작 합니다.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. 압축이 풀리면 **setup.exe를 실행 ** 하는 옵션을 선택한 다음 **마침**을 선택 합니다.

> [!TIP]
> 소프트웨어 패키지를 추출한 폴더에서 setup.exe 파일을 찾을 수도 있습니다.

### <a name="install-the-software-package"></a>소프트웨어 패키지 설치

1. 설치 **의 설치 창**에서 **Microsoft Azure Backup** 을 선택 하 여 설치 마법사를 엽니다.

   ![설치의 설치 창에서 Microsoft Azure Backup을 선택 하 여 설치 마법사를 엽니다.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. **시작** 화면에서 **다음** 을 선택 하 여 **필수 구성 요소 확인** 페이지로 이동 합니다.

1. **확인을 다시** 선택 하 여 Azure Backup Server에 대 한 하드웨어 및 소프트웨어 필수 구성 요소가 충족 되는지 확인 합니다. 성공적으로 충족 되 면 **다음**을 선택 합니다.

   ![ 확인을 다시 선택 하 여 Azure Backup Server에 대 한 하드웨어 및 소프트웨어 필수 구성 요소가 충족 되는지 확인 합니다. 성공적으로 충족 되 면 다음을 선택 합니다.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Azure Backup Server 설치 패키지는 필요한 적절 한 SQL Server 이진 파일과 함께 제공 됩니다. 새 Azure Backup Server 설치를 시작 하는 경우 **이 설치 옵션을 사용 하 여 SQL Server의 새 인스턴스 설치** 를 선택 합니다. 그런 다음 **확인 및 설치**를 선택 합니다.

   ![Azure Backup Server 설치 패키지는 필요한 적절 한 SQL Server 이진 파일과 함께 제공 됩니다.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > 사용자 고유의 SQL Server 인스턴스를 사용 하려는 경우 지원 되는 SQL Server 버전은 SQL Server 2014 SP1 이상, 2016 및 2017입니다. 모든 SQL Server 버전은 Standard 또는 Enterprise 64비트여야 합니다. Azure Backup Server은 원격 SQL Server 인스턴스에서 작동 하지 않습니다. Azure Backup Server에서 사용하는 인스턴스는 로컬이어야 합니다. Azure Backup Server에 기존 SQL Server 인스턴스를 사용 하는 경우 설치 프로그램은 SQL Server의 명명 된 *인스턴스만* 사용 하도록 지원 합니다.

   컴퓨터를 다시 시작 하 라는 권장 사항을 사용 하 여 오류가 발생 하는 경우이 작업을 수행 하 고 **다시 확인**을 선택 합니다. SQL Server 구성 문제가 있는 경우 SQL Server 지침에 따라 SQL Server를 다시 구성 합니다. 그런 다음 SQL Server의 기존 인스턴스를 사용 하 여 Azure Backup Server 설치 또는 업그레이드를 다시 시도 합니다.

   **수동 구성**

   사용자 고유의 SQL Server 인스턴스를 사용 하는 경우 sysadmin 역할에 builtin\Administrators를 master 데이터베이스에 추가 해야 합니다.

   **SQL Server 2017를 사용 하는 SSRS 구성**

   SQL Server 2017의 고유한 인스턴스를 사용 하는 경우 SSRS (SQL Server 2017 Reporting Services)를 수동으로 구성 해야 합니다. SSRS 구성 후 SSRS의 **Isinitialized** 속성이 **True**로 설정 되어 있는지 확인 합니다. 이 속성이 **True**로 설정 된 경우 AZURE BACKUP SERVER는 ssrs가 이미 구성 되어 있는 것으로 가정 하 고 ssrs 구성을 건너뜁니다.

   SSRS 구성 상태를 확인 하려면 다음 명령을 실행 합니다.

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   SSRS 구성에 대해 다음 값을 사용합니다.

   * **서비스 계정**: **기본 제공 계정을 사용 하** 는 것은 **네트워크 서비스**여야 합니다.
   * **웹 서비스 URL**: **가상 디렉터리** 를 **ReportServer_ \<SQLInstanceName> **해야 합니다.
   * **데이터베이스**: **DatabaseName** 은 **ReportServer $ \<SQLInstanceName> **여야 합니다.
   * **웹 포털 URL**: **가상 디렉터리** 를 **Reports_ \<SQLInstanceName> **해야 합니다.

   SSRS 구성에 대해 [자세히 알아봅니다](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017).

   > [!NOTE]
   > [Microsoft Online Services 약관](https://www.microsoft.com/licensing/product-licensing/products) (OST)은 Azure Backup Server의 데이터베이스로 사용 되는 SQL Server에 대 한 라이선스를 제어 합니다. OST에 따라 Azure Backup Server와 함께 제공 되는 SQL Server는 Azure Backup Server 데이터베이스로만 사용할 수 있습니다.

1. 설치가 완료 되 면 **다음**을 선택 합니다.

1. Microsoft Azure Backup Server 파일의 설치 위치를 지정 하 고 **다음**을 선택 합니다.

   > [!NOTE]
   > Azure에 백업 하려면 스크래치 위치가 필요 합니다. 스크래치 위치가 클라우드로 백업할 계획된 데이터의 5%인지 확인하세요. 디스크 보호의 경우 설치가 완료 된 후 별도의 디스크를 구성 해야 합니다. 저장소 풀에 대 한 자세한 내용은 [저장소 풀 및 디스크 저장소 구성](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))을 참조 하세요.

   ![Microsoft Azure Backup Server 파일의 설치 위치를 지정 하 고 다음을 선택 합니다.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. 제한 된 로컬 사용자 계정에 대 한 강력한 암호를 입력 하 고 **다음**을 선택 합니다.

   ![제한 된 로컬 사용자 계정에 대 한 강력한 암호를 입력 하 고 다음을 선택 합니다.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Microsoft 업데이트를 사용 하 여 업데이트를 확인할 지 여부를 선택 하 고 **다음**을 선택 합니다.

   > [!NOTE]
   > Windows 및 Azure Backup Server와 같은 기타 제품에 대 한 보안 및 중요 업데이트를 제공 하는 Microsoft 업데이트로 리디렉션하 Windows 업데이트 하는 것이 좋습니다.

   ![Microsoft 업데이트를 사용 하 여 업데이트를 확인할 지 여부를 선택 하 고 다음을 선택 합니다.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. **설정 요약**을 검토 하 고 **설치**를 선택 합니다.

   설치는 단계적으로 발생합니다. 첫 번째 단계는 Microsoft Azure Recovery Services 에이전트를 설치 하 고, 두 번째 단계는 인터넷 연결을 확인 합니다. 인터넷 연결을 사용할 수 있는 경우 설치를 계속할 수 있습니다. 그렇지 않으면 인터넷에 연결 하는 데 프록시 세부 정보를 제공 해야 합니다. 최종 단계는 필수 구성 요소 소프트웨어를 확인 합니다. 설치 되지 않은 경우 누락 된 소프트웨어는 Microsoft Azure Recovery Services 에이전트와 함께 설치 됩니다.

1. **찾아보기** 를 선택 하 여 자격 증명 모음을 찾아 Recovery Services 자격 증명 모음에 컴퓨터를 등록 한 후 **다음**을 선택 합니다.

1. Azure와 온-프레미스 간에 전송 된 데이터를 암호화 하거나 암호 해독 하는 데 통과할 암호문을 선택 합니다.

   > [!TIP]
   > 자동으로 암호를 생성하거나 최소 16자인 고유의 암호를 제공할 수 있습니다.

1. 암호를 저장할 위치를 입력 하 고 **다음** 을 선택 하 여 서버를 등록 합니다.

   > [!IMPORTANT]
   > 전달 구를 로컬 서버가 아닌 안전한 위치에 저장 합니다. Azure Key Vault를 사용 하 여 암호문을 저장 하는 것이 좋습니다.

   Microsoft Azure Recovery Services 에이전트 설치가 완료 되 면 설치 단계가 SQL Server 및 Azure Backup Server 구성 요소의 설치 및 구성으로 이동 합니다.

   ![Microsoft Azure Recovery Services 에이전트 설치가 완료 되 면 설치 단계가 SQL Server 및 Azure Backup Server 구성 요소의 설치 및 구성으로 이동 합니다.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. 설치 단계가 완료 되 면 **닫기**를 선택 합니다.

### <a name="install-update-rollup-1"></a>업데이트 롤업 1 설치

작업을 보호 하려면 Azure Backup Server v3에 대 한 업데이트 롤업 1을 설치 해야 합니다. Azure Backup Server v3 업데이트 롤업 1에 대 한 버그 수정 및 설치 지침 목록을 보려면 기술 자료 문서 [4534062](https://support.microsoft.com/en-us/help/4534062/)를 참조 하십시오.

## <a name="add-storage-to-azure-backup-server"></a>Azure Backup Server에 스토리지 추가

Azure Backup Server v3은에서 제공 하는 Modern Backup Storage 지원 합니다.

-  저장소 절감 50%
-  3 배 더 빠른 백업
-  더 효율적인 저장소.
-  워크 로드 인식 저장소.

### <a name="volumes-in-azure-backup-server"></a>Azure Backup Server 볼륨

필요한 저장소 용량이 포함 된 데이터 디스크를 아직 추가 하지 않은 경우 Azure Backup Server 가상 컴퓨터에 추가 합니다.

Azure Backup Server v3은 저장소 볼륨만 허용 합니다. 볼륨을 추가 하면 Azure Backup Server에 필요한 Modern Backup Storage ReFS (복원 파일 시스템)로 볼륨을 포맷 합니다.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Azure Backup Server 디스크 저장소에 볼륨 추가

1. **관리** 창에서 저장소를 다시 검사 한 다음 **추가**를 선택 합니다. 

1. 저장소 풀에 추가할 사용 가능한 볼륨에서 선택 합니다. 

1. 사용 가능한 볼륨을 추가한 후에는 해당 볼륨을 관리 하는 데 도움이 되는 친숙 한 이름을 지정 합니다. 

1. Azure Backup Server에서 Modern Backup Storage의 이점을 사용할 수 있도록 이러한 볼륨을 ReFS로 포맷 하려면 **확인** 을 선택 합니다.

![사용 가능한 볼륨 추가](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>다음 단계

Azure Backup Server를 사용 하 여 Azure VMware 솔루션에서 실행 되는 VMware Vm의 백업을 구성 하는 방법을 알아보려면 다음 자습서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [Azure VMware 솔루션 Vm의 백업 구성](backup-avs-vms-with-mabs.md)

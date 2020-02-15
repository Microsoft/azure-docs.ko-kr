---
title: Azure Site Recovery를 사용 하 여 Azure Vm 복제 문제 해결
description: Azure Site Recovery를 사용 하 여 Azure VM 재해 복구에서 복제 문제 해결
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.openlocfilehash: e5e52c6e8560c7369054cfc9fcf2ba4c405671e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190801"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Azure VM 재해 복구에서 복제 문제 해결

이 문서에서는 지역 간에 Azure 가상 머신을 복제 및 복구할 때 Azure Site Recovery에서 발생하는 일반적인 문제를 설명합니다. 일반적인 문제를 해결 하는 방법에 대해서도 설명 합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](site-recovery-support-matrix-azure-to-azure.md)를 참조하세요.

Azure Site Recovery은 원본 지역에서 재해 복구 지역으로 데이터를 일관 되 게 복제 합니다. 또한 5 분 마다 크래시 일치 복구 지점을 만듭니다. Site Recovery에서 60분 동안 복구 지점을 만들 수 없는 경우 다음 정보를 알려줍니다.

오류 메시지: "지난 60 분 동안 VM에 사용 가능한 크래시 일치 복구 지점이 없습니다."</br>
오류 ID: 153007

다음 섹션에서는 원인 및 해결 방법을 설명합니다.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>원본 가상 머신의 높은 데이터 변경률

원본 가상 컴퓨터의 데이터 변경률이 지원 되는 제한 보다 높은 경우 Azure Site Recovery 이벤트를 만듭니다. 변동으로 인해 문제가 발생 했는지 여부를 확인 하려면 **복제 된 항목** > **VM** > **이벤트-지난 72 시간**으로 이동 합니다.
"지원 되는 한도를 초과 하는 데이터 변경 률" 이벤트가 표시 됩니다.

![너무 높은 데이터 변경 률을 보여 주는 Azure Site Recovery 페이지](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

이벤트를 선택하면 정확한 디스크 정보가 표시됩니다.

![데이터 변경 률 이벤트 세부 정보를 표시 하는 페이지입니다.](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 제한

다음 테이블에는 Azure Site Recovery 제한이 제공됩니다. 이러한 제한은 테스트를 기반으로 하지만 모든 가능한 응용 프로그램 i/o (입/출력) 조합을 처리할 수는 없습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.

고려해 야 할 두 가지 제한이 있습니다. 디스크당 데이터 변동 및 가상 컴퓨터당 데이터 변동 예를 들어 다음 표의 Premium P20 디스크를 살펴보겠습니다. 단일 VM의 경우 디스크 마다 5 m b/초 Site Recovery를 처리할 수 있으며, 이러한 디스크는 최대 5 개까지 처리할 수 있습니다. Site Recovery은 VM 당 총 변동 수가 25MB/s로 제한 됩니다.

**복제 스토리지 대상** | **원본 디스크의 평균 I/O 크기** |**원본 디스크의 평균 데이터 변동** | **원본 데이터 디스크의 일별 총 데이터 변동**
---|---|---|---
Standard Storage | 8KB | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB  | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |  디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |10MB/초 | 디스크당 842GB

### <a name="solution"></a>해결 방법

Azure Site Recovery는 디스크 유형에 따라 데이터 변경 비율에 대 한 제한이 있습니다. 이 문제가 되풀이 또는 임시 인지 확인 하려면 영향을 받는 가상 머신의 데이터 변경 률을 확인 하세요. 원본 가상 머신으로 이동하여 **모니터링** 아래의 메트릭을 찾은 다음, 이 스크린샷과 같이 메트릭을 추가합니다.

![데이터 변경 률을 찾기 위한 3 단계 프로세스를 표시 하는 페이지입니다.](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **메트릭 추가**를 선택하고 **OS 디스크 쓰기 바이트/초** 및 **데이터 디스크 쓰기 바이트/초**를 추가합니다.
1. 스크린샷에 표시된 것처럼 스파이크를 모니터링합니다.
1. OS 디스크 및 결합된 모든 데이터 디스크에서 발생하는 총 쓰기 작업을 보여줍니다. 이러한 메트릭은 디스크별 수준에서 정보를 제공하지는 않지만 데이터 변동의 총 패턴을 나타냅니다.

데이터 변경 률이 급증 하는 경우 데이터 버스트로 인해 발생할 수 있습니다. 데이터 변경 률이 10mb/s (Premium의 경우) 또는 2 m b/초 (표준) 보다 큰 경우 복제가 처리 됩니다. 변동 수가 지원 되는 한도를 초과 하는 경우 다음 옵션 중 하나를 고려 하십시오.

- 높은 데이터 변경 률을 유발 하는 디스크 제외: 먼저 복제를 사용 하지 않도록 설정 합니다. 그런 다음 [PowerShell](./azure-to-azure-exclude-disks.md)을 사용 하 여 디스크를 제외할 수 있습니다.
- 재해 복구 저장소 디스크의 계층 변경:이 옵션은 디스크 데이터 변동이 20 m b/초 미만인 경우에만 가능 합니다. P10 디스크를 사용 하는 VM의 데이터 변동이 8 m b/초 보다 크고 10mb/s 보다 작은 것으로 가정 합니다. 고객이 보호 중에 대상 스토리지에 대해 P30 디스크를 사용할 수 있는 경우 문제를 해결할 수 있습니다. 이 솔루션은 프리미엄 Managed Disks를 사용 하는 컴퓨터에만 사용할 수 있습니다. 다음 단계를 수행하세요.

    1. 영향을 받는 복제 된 컴퓨터의 **디스크로** 이동 하 여 복제본 디스크 이름을 복사 합니다.
    1. Managed disk의이 복제본으로 이동 합니다.
    1. SAS URL이 생성 되었다는 **개요** 에 배너가 표시 될 수 있습니다. 이 배너를 선택 하 고 내보내기를 취소 합니다. 배너가 표시 되지 않으면이 단계를 무시 합니다.
    1. SAS URL이 해지 되는 즉시 관리 디스크에 대 한 **구성** 으로 이동 합니다. 원본 디스크에서 관찰 된 변동 율 Site Recovery 지원 하도록 크기를 늘립니다.

## <a name="Network-connectivity-problem"></a>네트워크 연결 문제

### <a name="network-latency-to-a-cache-storage-account"></a>캐시 스토리지 계정의 네트워크 대기 시간

Site Recovery는 복제된 데이터를 캐시 스토리지 계정으로 보냅니다. 가상 컴퓨터의 데이터를 캐시 저장소 계정에 업로드 하는 것이 3 초 이면 네트워크 대기 시간이 발생할 수 있습니다.

대기 시간과 관련 된 문제를 확인 하려면 [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)를 사용 합니다. 이 명령줄 유틸리티를 사용 하 여 가상 머신에서 캐시 저장소 계정으로 데이터를 업로드할 수 있습니다. 대기 시간이 긴 경우 NVA (네트워크 가상 어플라이언스)를 사용 하 여 Vm에서 아웃 바운드 네트워크 트래픽을 제어 하 고 있는지 확인 합니다. 모든 복제 트래픽이 NVA를 통과하는 경우 어플라이언스가 제한될 수 있습니다.

복제 트래픽이 NVA로 이동하지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만드는 것이 좋습니다. 자세한 내용은 [네트워크 가상 어플라이언스 구성](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)을 참조하세요.

### <a name="network-connectivity"></a>네트워크 연결

Site Recovery 복제가 작동 하려면 VM이 특정 Url 또는 IP 범위에 대 한 아웃 바운드 연결을 제공 해야 합니다. VM이 방화벽 뒤에 있거나 NSG (네트워크 보안 그룹) 규칙을 사용 하 여 아웃 바운드 연결을 제어할 수 있습니다. 이 경우 문제가 발생할 수 있습니다. 모든 URL이 연결되었는지 확인하려면 [Site Recovery URL에 대한 아웃바운드 연결](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)을 참조하세요.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>오류 ID 153006-지난 "X" 분 내에 VM에 사용할 수 있는 앱 일치 복구 지점이 없습니다.

다음은 가장 일반적인 몇 가지 문제입니다.

#### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL server 2008/2008 r 2의 알려진 문제

**해결 방법**: SQL server 2008/2008 r 2와 관련 된 알려진 문제가 있습니다. 2008 r 2를 [SQL Server 호스트 하는 서버에 대 한 Azure Site Recovery 에이전트 또는 기타 구성 요소가 아닌 VSS 백업 실패](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)문서를 참조 하세요.

#### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>AUTO_CLOSE Db를 사용 하 여 모든 버전의 SQL Server 인스턴스를 호스트 하는 서버에서 Azure Site Recovery 작업이 실패 함

**해결 방법**: [AUTO_CLOSE db를 사용 하 SQL Server 인스턴스를 호스트 하는 서버에서 Azure Site Recovery 작업이 실패 하는 것과 같은 구성 요소가 아닌 VSS 백업](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)문서를 참조 하세요.


#### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 및 2017의 알려진 문제

**해결 방법**: [SQL Server 2016 및 2017에서 구성 요소가 아닌 기반 백업을 사용 하 여 가상 컴퓨터를 백업 하는 경우 오류 발생](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)문서를 참조 하세요.

#### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Azure Storage Spaces Direct 구성을 사용 하 고 있습니다.

**해결 방법**: Azure Site Recovery 스토리지 공간 다이렉트 구성에 대 한 응용 프로그램 일치 복구 지점을 만들 수 없습니다. [복제 정책을 구성](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)합니다.

### <a name="more-causes-because-of-vss-related-issues"></a>VSS 관련 문제로 인해 더 많은 원인이 있습니다.

추가로 문제를 해결 하려면 원본 컴퓨터의 파일을 확인 하 여 오류에 대 한 정확한 오류 코드를 확인 합니다.

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

파일에서 오류를 찾으려면 편집기에서 vacp.exe 파일을 열어 "vacpError" 문자열을 검색 합니다.

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

앞의 예제에서 **2147754994** 는이 문장 뒤의 오류에 대 한 정보를 알려 주는 오류 코드입니다.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 기록기가 설치 되어 있지 않습니다. 오류 2147221164

**해결 방법**: 응용 프로그램 일관성 태그를 생성 하려면 Azure Site Recovery 볼륨 섀도 복사본 서비스 (VSS)를 사용 합니다. Site Recovery은 해당 작업에 대 한 VSS 공급자를 설치 하 여 응용 프로그램 일관성 스냅숏을 만듭니다. Azure Site Recovery이 VSS 공급자를 서비스로 설치 합니다. VSS 공급자가 설치 되지 않은 경우 응용 프로그램 일관성 스냅숏 만들기가 실패 합니다. 오류 ID 0x80040154 "클래스가 등록 되지 않았습니다."가 표시 됩니다. [VSS 기록기 설치 문제 해결](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)문서를 참조 하세요.

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 기록기를 사용할 수 없음-오류 2147943458

**해결 방법**: 응용 프로그램 일관성 태그를 생성 하기 위해 Azure Site Recovery VSS를 사용 합니다. Site Recovery은 해당 작업에 대 한 VSS 공급자를 설치 하 여 응용 프로그램 일관성 스냅숏을 만듭니다. 이 VSS 공급자는 서비스로 설치 됩니다. VSS 공급자 서비스를 사용 하도록 설정 하지 않은 경우 응용 프로그램 일관성 스냅숏 만들기가 실패 합니다. "지정 된 서비스를 사용할 수 없으며 시작할 수 없습니다 (0x80070422)" 오류가 표시 됩니다.

VSS를 사용 하지 않도록 설정 하는 경우:

- VSS 공급자 서비스의 시작 유형이 **자동**으로 설정 되어 있는지 확인 합니다.
- 다음 서비스를 다시 시작 합니다.
   - VSS 서비스
   - Azure Site Recovery VSS 공급자
   - VDS 서비스

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS 공급자 NOT_REGISTERED-오류 2147754756

**해결 방법**: 응용 프로그램 일관성 태그를 생성 하기 위해 Azure Site Recovery VSS를 사용 합니다. Azure Site Recovery VSS 공급자 서비스가 설치 되어 있는지 확인 합니다.

다음 명령을 사용 하 여 VSS 공급자를 다시 설치 합니다.
1. 기존 공급자 제거: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Uninstall .cmd
1. VSS 공급자 다시 설치: C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install .cmd

VSS 공급자 서비스의 시작 유형이 **자동**으로 설정 되어 있는지 확인 합니다.

다음 서비스를 다시 시작 합니다.
- VSS 서비스
- Azure Site Recovery VSS 공급자
- VDS 서비스

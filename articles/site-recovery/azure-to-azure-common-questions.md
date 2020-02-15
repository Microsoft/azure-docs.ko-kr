---
title: Azure Site Recovery 사용 하는 Azure VM 재해 복구에 대 한 일반적인 질문
description: 이 문서에서는 Azure Site Recovery를 사용 하는 경우 Azure VM 재해 복구에 대 한 일반적인 질문에 답변 합니다.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 99fed1d2b1246e4c099f275708f694e5d7ea2f22
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190831"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>일반적인 질문: Azure에서 Azure로 재해 복구

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하는 경우 azure vm을 다른 azure 지역으로 복구 하는 방법에 대 한 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?

[Vm에 대 한 Azure Site Recovery 가격 책정을](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)검토 합니다.

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery의 무료 계층은 어떻게 작동하나요?

Azure Site Recovery로 보호되는 모든 인스턴스는 처음 31일 동안 무료로 보호됩니다. 이 기간이 지나면 각 인스턴스에 대 한 보호는 [Azure Virtual Machines에 대 한 Azure Site Recovery 가격 책정](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)에 있습니다.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>처음 31일 동안 다른 Azure 요금이 부과되나요?

예. 보호 된 인스턴스의 처음 31 일 동안 Azure Site Recovery는 무료 이지만 Azure Storage, 저장소 트랜잭션 및 데이터 전송에 대 한 요금이 부과 될 수 있습니다. 복구 된 가상 머신에도 Azure 계산 요금이 발생할 수 있습니다. 가격 책정에 대 한 자세한 내용은 [Azure Site Recovery 가격](https://azure.microsoft.com/pricing/details/site-recovery)책정을 참조 하세요.

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure Virtual Machines 재해 복구에 대 한 모범 사례는 무엇 인가요?

1. [Azure 간 아키텍처 이해](azure-to-azure-architecture.md)
1. [지원되는 구성 및 지원되지 않는 구성 검토](azure-to-azure-support-matrix.md)
1. [Azure VM에 대한 재해 복구 설정](azure-to-azure-how-to-enable-replication.md)
1. [테스트 장애 조치 실행](azure-to-azure-tutorial-dr-drill.md)
1. [주 지역에 대한 장애 조치 및 장애 복구](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>대상 지역에서 용량을 어떻게 보장 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분 한 인프라 용량을 계획 하 고 있습니다. 장애 조치 (failover)를 시작할 때 팀은 Site Recovery로 보호 되는 VM 인스턴스가 대상 지역에 배포 되도록 하는 데도 도움이 됩니다.

## <a name="replication"></a>복제

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Azure 디스크 암호화를 통해 지원되는 VM을 복제할 수 있나요?

예. Site Recovery은 Azure Disk Encryption 사용 하도록 설정 된 Vm의 재해 복구를 지원 합니다. 복제를 사용 하도록 설정 하면 Azure는 필요한 모든 디스크 암호화 키와 암호를 원본 지역의 사용자 컨텍스트에서 대상 영역으로 복사 합니다. 적절 한 권한이 없는 경우 보안 관리자는 스크립트를 사용 하 여 키와 비밀을 복사할 수 있습니다.

- Site Recovery은 Windows를 실행 하는 Azure Vm에 대 한 Azure Disk Encryption를 지원 합니다.
- Site Recovery는 Azure Active Directory (Azure AD)를 필요로 하는 스키마가 있는 Azure Disk Encryption 버전 0.1를 지원 합니다. Site Recovery는 Azure AD가 필요 하지 않은 버전 1.1도 지원 합니다. [Azure disk encryption에 대 한 확장 schemata에 대해 자세히 알아보세요](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
  - Azure Disk Encryption 버전 1.1의 경우 managed disks가 있는 Windows Vm을 사용 해야 합니다.
  - 암호화 된 Vm에 대 한 복제 사용에 대해 [자세히 알아보세요](azure-to-azure-how-to-enable-replication-ade-vms.md) .

### <a name="can-i-replicate-vms-to-another-subscription"></a>VM을 다른 구독에 복제할 수 있나요?

예, Azure VM을 동일한 Azure AD 테넌트의 다른 구독에 복제할 수 있습니다.

복제 시 다른 구독을 선택 하 여 [구독 간](https://azure.microsoft.com/blog/cross-subscription-dr) 재해 복구를 구성 합니다.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>영역이 고정된 Azure VM을 다른 지역에 복제할 수 있나요?

예, [영역이 고정된 VM](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)을 다른 지역에 복제할 수 있습니다.

### <a name="can-i-exclude-disks"></a>디스크를 제외할 수 있나요?

예, PowerShell을 사용하여 보호 시 디스크를 제외할 수 있습니다. 자세한 내용은 [복제에서 디스크를 제외 하는 방법](azure-to-azure-exclude-disks.md)을 참조 하세요.

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>복제 된 Vm에 새 디스크를 추가 하 고 해당 디스크에 대 한 복제를 사용 하도록 설정할 수 있나요?

예, 복제 된 Vm에 새 디스크를 추가 하 고 이러한 디스크에 대 한 복제를 사용 하도록 설정 하는 것은 managed disks가 있는 Azure Vm 복제를 사용 하도록 설정 된 Azure VM에 새 디스크를 추가 하면 VM에 대 한 복제 상태에 경고가 표시 됩니다. 이 경고는 VM에서 하나 이상의 디스크를 보호할 수 있음을 명시 합니다. 추가 된 디스크에 대해 복제를 사용 하도록 설정할 수 있습니다.

- 추가 된 디스크에 대 한 보호를 사용 하도록 설정 하면 초기 복제 후에 경고가 사라집니다.
- 디스크에 대해 복제를 사용 하도록 설정 하지 않으면 경고를 해제할 수 있습니다.
- 추가 된 디스크 및 복제를 사용 하는 VM을 장애 조치 (failover) 하는 경우 복제 지점이 있습니다. 복제 지점은 복구에 사용할 수 있는 디스크를 표시 합니다. 

예를 들어 VM에 단일 디스크가 있고 새 디스크를 추가 한다고 가정해 보겠습니다. 디스크를 추가 하기 전에 생성 된 복제 지점이 있을 수 있습니다. 이 복제 지점에 "디스크 2 개 중 1 개"로 구성 된 것으로 표시 됩니다.

Site Recovery는 복제 된 VM에서 디스크의 "핫 제거"를 지원 하지 않습니다. VM 디스크를 제거 하는 경우 VM에 대 한 복제를 사용 하지 않도록 설정 했다가 다시 사용 하도록 설정 해야 합니다.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

Azure VM을 다른 Azure 지역에 복제할 때는 복제가 계속 진행됩니다. 자세한 내용은 [Azure 간 복제 아키텍처](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)를 참조하세요.

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>한 지역 내에서 가상 머신을 복제할 수 있나요? Vm을 마이그레이션하려면이 기능이 필요 합니다.

Azure-Azure 디스크 복구 솔루션을 사용 하 여 지역 내에서 Vm을 복제할 수 없습니다.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>VM 인스턴스를 Azure 지역에 복제할 수 있나요?

Site Recovery를 사용 하 여 동일한 지리적 클러스터 내의 두 지역 간에 Vm을 복제 하 고 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의하여 정의됩니다. 자세한 내용은 Site Recovery [지역 지원 행렬](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)을 참조하세요.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery에 인터넷 연결이 필요한가요?

아니요, Site Recovery 인터넷 연결이 필요 하지 않습니다. 하지만 [AZURE VM 재해 복구의 네트워킹](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)에서 설명한 대로 Site Recovery URL 및 IP 범위에 대 한 액세스 권한이 필요 합니다.

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>별도의 계층에 대 한 별도의 리소스 그룹을 포함 하는 응용 프로그램을 복제할 수 있나요?

예, 응용 프로그램을 복제 하 고 재해 복구 구성을 별도의 리소스 그룹에 유지할 수 있습니다.

예를 들어 응용 프로그램의 각 계층 응용 프로그램, 데이터베이스 및 웹이 별도의 리소스 그룹에 있는 경우 [복제 마법사](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) 를 세 번 선택 하 여 모든 계층을 보호 해야 합니다. 이러한 세 계층은 3 개의 다른 리소스 그룹으로 복제 Site Recovery.

## <a name="replication-policy"></a>복제 정책

### <a name="what-is-a-replication-policy"></a>복제 정책은 무엇인가요?

복제 정책은 복구 지점의 보존 기록에 대 한 설정을 정의 합니다. 또한 정책은 앱 일치 스냅숏의 빈도를 정의 합니다. 기본적으로 Azure Site Recovery는 다음 기본 설정을 사용하여 새 복제 정책을 만듭니다.

- 복구 지점의 보존 기록의 경우 24시간으로 설정합니다.
- 앱 일치 스냅샷 빈도의 경우 60분으로 설정합니다.

[복제 설정에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>크래시 일치 복구 지점은 무엇인가요?

크래시 일관성 복구 지점에는 스냅숏 중에 서버에서 전원 코드를 가져온 것 처럼 디스크에 있는 데이터가 있습니다. 크래시 일치 복구 지점에는 스냅숏을 만들 때 메모리에 있던 내용이 포함 되지 않습니다.

현재 대부분의 애플리케이션은 크래시 일치 스냅샷을 제대로 복구할 수 있습니다. 크래시 일치 복구 지점은 데이터베이스가 없는 운영 체제와 파일 서버, DHCP 서버, 인쇄 서버 등의 애플리케이션에 일반적으로 적합합니다.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>충돌 일치 복구 지점 생성 빈도는 어느 정도인가요?

Site Recovery는 5분 마다 크래시 일치 복구 지점을 만듭니다.

### <a name="what-is-an-application-consistent-recovery-point"></a>애플리케이션 일치 복구 지점은 무엇인가요?

애플리케이션 일치 복구 지점은 애플리케이션 일치 스냅샷에서 만들어집니다. 응용 프로그램 일치 복구 지점은 메모리 및 프로세스의 모든 트랜잭션에서 데이터를 캡처하는 동시에 충돌에 일관성이 있는 스냅숏과 동일한 데이터를 캡처합니다.

추가 콘텐츠로 인해 응용 프로그램에 일관 된 스냅숏이 가장 많이 사용 되며 가장 오랜 시간이 걸립니다. 애플리케이션 일치 복구 지점은 데이터베이스 운영 체제와 SQL 서버 등의 애플리케이션에 권장됩니다.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>애플리케이션 일치 복구 지점이 애플리케이션 성능에 미치는 영향은 무엇입니까?

응용 프로그램 일치 복구 지점은 메모리 및 프로세스의 모든 데이터를 캡처합니다. 복구 지점은 해당 데이터를 캡처하기 때문에 응용 프로그램을 정지 하려면 Windows에서 볼륨 섀도 복사본 서비스와 같은 프레임 워크가 필요 합니다. 캡처 프로세스가 빈번 하 게 발생 하는 경우 워크 로드가 이미 사용 중인 경우 성능에 영향을 줄 수 있습니다. 비 데이터베이스 작업의 경우 앱 일치 복구 지점의 빈도를 낮게 사용 하지 않는 것이 좋습니다. 데이터베이스 워크 로드의 경우에도 1 시간으로 충분 합니다.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>애플리케이션 일치 복구 지점 생성의 최소 빈도는 어느 정도인가요?

최소 빈도가 1 시간인 응용 프로그램 일치 복구 지점을 만들 수 Site Recovery.

### <a name="how-are-recovery-points-generated-and-saved"></a>복구 지점은 어떻게 생성 및 저장되나요?

Site Recovery에서 복구 지점이 생성 되는 방식을 이해 하기 위해 복제 정책의 예를 살펴보겠습니다. 이 복제 정책에는 24 시간 보존 기간 및 앱 일치 빈도 스냅숏이 1 시간 인 복구 지점이 있습니다.

Site Recovery는 5분 마다 크래시 일치 복구 지점을 만듭니다. 이 빈도는 변경할 수 없습니다. 지난 1 시간 동안에는 12 개의 크래시 일치 지점 및 1 개의 앱 일치 지점에서 선택할 수 있습니다. 시간이 지나면 Site Recovery는 지난 1 시간 이상 모든 복구 지점을 정리 하 고 시간당 복구 지점을 1 개만 저장 합니다.

다음 스크린샷은 예제를 보여줍니다. 스크린샷:

- 지난 시간에는 5 분의 빈도로 복구 지점이 있습니다.
- 지난 시간 외에 Site Recovery 복구 지점은 1 개만 유지 됩니다.

   ![생성된 복구 지점 목록](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24 시간 동안의 복제 정책이 있습니다. Site Recovery에서 24 시간 넘게 복구 지점의 생성을 방지 하는 문제가 발생 하는 경우 어떻게 되나요? 이전 복구 지점이 손실되나요?

아니요, Site Recovery는 이전 복구 지점을 모두 유지합니다. 복구 지점의 보존 기간에 따라 Site Recovery 새 지점을 생성 하는 경우에만 가장 오래 된 지점을 대체 합니다. 문제로 인해 새 복구 지점이 생성 되지 Site Recovery. 새 복구 지점이 있을 때까지 이전 모든 지점은 보존 기간에 도달한 후에도 유지 됩니다.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>VM에서 복제를 사용하도록 설정한 후 복제 정책을 변경하려면 어떻게 할까요?

**Site Recovery 자격 증명 모음** > **Site Recovery 인프라** > **복제 정책**으로 이동합니다. 편집 하려는 정책을 선택 하 고 변경 내용을 저장 합니다. 변경 내용은 모든 기존 복제에도 적용됩니다.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>모든 복구 지점이 VM의 전체 복사본인가요, 아니면 차등 복사본인가요?

생성되는 첫 번째 복구 지점에 전체 복사본이 있습니다. 모든 연속 복구 지점에는 델타 변경 내용이 있습니다.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>복구 지점의 보존 기간을 늘리면 스토리지 비용도 증가하나요?

예, 보존 기간을 24 시간에서 72 시간으로 늘리면 Site Recovery 추가 48 시간 동안 복구 지점이 저장 됩니다. 추가된 시간에 대해 스토리지 요금이 부과됩니다. 예를 들어, 단일 복구 지점에는 10gb의 델타 변경 ($0.16/월)이 있을 수 있습니다. 추가 요금은 월 $1.60 × 48이 됩니다.

## <a name="multi-vm-consistency"></a>다중 VM 일관성

### <a name="what-is-multi-vm-consistency"></a>다중 VM 일관성 이란?

다중 VM 일관성은 복제 된 모든 가상 컴퓨터에서 복구 지점이 일치 하는지 확인 합니다.

Site Recovery은 모든 컴퓨터의 복제 그룹을 만드는 **다중 VM 일관성** 옵션을 제공 합니다.

가상 컴퓨터를 장애 조치 (failover) 하면 공유 크래시 일관성 및 앱 일치 복구 지점이 유지 됩니다.

[다중 VM 일관성을 사용 하도록 설정](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)하려면 자습서를 진행 하세요.

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>다중 VM 일관성 복제 그룹 내에서 단일 가상 머신을 장애 조치 (failover) 할 수 있나요?

**다중 VM 일관성** 옵션을 선택 하면 응용 프로그램이 그룹 내의 모든 가상 컴퓨터에 종속 되어 있음을 확인할 수 있습니다. 단일 가상 머신 장애 조치 (failover)는 허용 되지 않습니다.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>다중 VM 일관성 복제 그룹의 일부분으로 복제할 수 있는 가상 머신 수는 몇 개입니까?

복제 그룹에서 16개의 가상 머신을 함께 복제할 수 있습니다.

### <a name="when-should-i-enable-multi-vm-consistency"></a>다중 VM 일관성을 사용 해야 하는 경우

다중 VM 일관성은 CPU를 많이 사용 하므로 사용 하도록 설정 하면 워크 로드 성능에 영향을 줄 수 있습니다. 컴퓨터가 동일한 워크 로드를 실행 하 고 여러 컴퓨터에서 일관성을 유지 해야 하는 경우에만 다중 VM 일관성을 사용 합니다. 예를 들어 응용 프로그램에 두 개의 SQL Server 인스턴스와 두 개의 웹 서버가 있는 경우 SQL Server 인스턴스에만 다중 VM 일관성을 유지 해야 합니다.

## <a name="failover"></a>장애 조치

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Azure Vm의 대상 지역에서 용량을 어떻게 보장 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분 한 인프라 용량을 계획 하 고 있습니다. 장애 조치 (failover)를 시작할 때 팀은 Site Recovery로 보호 되는 VM 인스턴스가 대상 지역에 배포 되도록 하는 데도 도움이 됩니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

자동이 아닙니다. 포털에서 한 번의 클릭으로 장애 조치 (failover)를 시작 하거나 [PowerShell](azure-to-azure-powershell.md) 을 사용 하 여 장애 조치 (failover)를 트리거할 수 있습니다.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>장애 조치 (failover) 후 공용 IP 주소를 유지할 수 있나요?

장애 조치 (failover) 후에는 프로덕션 응용 프로그램의 공용 IP 주소를 유지할 수 없습니다.

장애 조치 (failover) 프로세스의 일부로 워크 로드를 가져오는 경우 작업에 Azure 공용 IP 리소스를 할당 해야 합니다. Azure 공용 IP 리소스는 대상 지역에서 사용할 수 있어야 합니다. Azure 공용 IP 리소스를 수동으로 할당 하거나 복구 계획을 사용 하 여 자동화할 수 있습니다. [장애 조치 (failover) 후 공용 IP 주소를 설정](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)하는 방법을 알아봅니다.  

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>장애 조치 (failover) 중에 개인 IP 주소를 유지할 수 있나요?

예, 개인 IP 주소를 유지할 수 있습니다. 기본적으로 Azure VM에 재해 복구를 사용하도록 설정하면 Site Recovery는 원본 리소스 설정에 따라 대상 리소스를 만듭니다. 고정 IP 주소를 사용 하 여 구성 된 Azure Virtual Machines의 경우, Site Recovery 사용 하지 않는 경우 대상 VM에 대해 동일한 IP 주소를 프로 비전 하려고 합니다.
[장애 조치 중에 IP 주소를 유지 하는](site-recovery-retain-ip-azure-vm-failover.md)방법에 대해 알아봅니다.

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>장애 조치 (failover) 후 서버에 새 IP 주소가 할당 된 이유는 무엇 인가요?

Site Recovery는 장애 조치(failover) 시 IP 주소를 제공하려고 시도합니다. 다른 가상 머신에서 해당 IP 주소를 가져가는 경우 Site Recovery는 대상으로 사용 가능한 다음 IP 주소를 설정합니다.

[가상 네트워크에 대 한 IP 주소 지정 및 네트워크 매핑을 설정 하는](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)방법에 대해 자세히 알아보세요.

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>**최신(가장 낮은 RPO)** 복구 지점은 무엇인가요?

**최신 (가장 낮은 RPO)** 옵션은 먼저 Site Recovery 전송 된 모든 데이터를 처리 합니다. 서비스는 데이터를 처리 한 후 VM으로 장애 조치 (failover) 하기 전에 각 VM에 대 한 복구 지점을 만듭니다. 이 옵션은 가장 낮은 RPO (복구 지점 목표)를 제공 합니다. 장애 조치 (failover) 후 생성 된 VM에는 장애 조치 (failover)가 트리거될 때 Site Recovery로 복제 된 모든 데이터가 포함 됩니다.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**최신(가장 낮은 RPO)** 복구 지점이 장애 조치(failover) RTO에 영향을 주나요?

예. Site Recovery는 장애 조치 (failover) 전에 보류 중인 모든 데이터를 처리 하므로이 옵션은 다른 옵션과 비교 하 여 더 높은 RTO (복구 시간 목표)를 포함 합니다.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>복구 지점에서 **가장 최근 처리됨** 옵션은 무엇을 의미하나요?

**가장 최근에 처리** 된 옵션은 계획의 모든 vm을 Site Recovery 처리 된 최신 복구 지점으로 장애 조치 (failover) 합니다. 특정 VM에 대한 최신 복구 지점을 보려면 VM 설정에서 **최신 복구 지점**을 선택하세요. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO를 제공합니다.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>주 지역에서 예기치 않은 중단이 발생 하면 어떻게 되나요?

중단 후에 장애 조치(failover)를 트리거할 수 있습니다. 장애 조치 (failover)를 수행 하기 위해 주 지역에서 연결할 필요는 없습니다 Site Recovery.

### <a name="what-is-an-rto-of-a-vm-failover"></a>VM 장애 조치 (failover)의 RTO는 무엇 인가요?

Site Recovery [의 RTO SLA는 2 시간](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)입니다. 그러나 대부분의 경우에는 몇 분 내에 가상 컴퓨터를 장애 조치 (failover) Site Recovery 합니다. 장애 조치 (failover) 작업으로 이동 하 여 RTO를 계산 하 여 VM을 가져오는 데 걸린 시간을 표시할 수 있습니다. 복구 계획 RTO의 경우 다음 섹션을 참조 하세요.

## <a name="recovery-plans"></a>복구 계획

### <a name="what-is-a-recovery-plan"></a>복구 플랜은 무엇인가요?

Site Recovery의 복구 플랜은 VM의 장애 조치(failover) 복구를 조정합니다. 복구 플랜을 통해 복구가 일관성 있게 정확하고, 반복 가능하며, 자동화되도록 유지할 수 있습니다. 복구 계획은 다음 요구 사항을 해결 합니다.

- 함께 장애 조치(failover)되는 가상 머신의 그룹 정의
- 애플리케이션이 정확하게 작동하도록 가상 머신 간의 종속성 정의
- 가상 머신 장애 조치(failover) 이외의 작업도 수행할 수 있도록 사용자 지정 수동 작업과 함께 복구 자동화

[복구 계획을 만드는 방법에](site-recovery-create-recovery-plans.md)대해 자세히 알아보세요.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>복구 플랜에서 순서를 지정하려면 어떻게 하나요?

복구 플랜에서 여러 개의 그룹을 만들어 순서를 지정할 수 있습니다. 모든 그룹을 한 번에 장애 조치합니다. 동일한 그룹의 일부인 가상 컴퓨터는 함께 장애 조치 (failover) 되 고 다른 그룹에 의해 장애 조치 (failover) 됩니다. 복구 계획을 사용하여 애플리케이션을 모델링하는 방법을 알아보려면 [복구 계획](recovery-plan-overview.md#model-apps)을 참조하세요.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>복구 플랜의 RTO를 확인하려면 어떻게 할까요?

복구 플랜의 RTO를 확인하려면 복구 플랜을 테스트 장애 조치(failover)를 수행하고 **Site Recovery 작업**으로 이동합니다.
다음 예제에서는 **SAPTestRecoveryPlan**작업을 참조 하세요. 작업은 모든 가상 컴퓨터를 장애 조치 (failover) 하 고 지정 된 작업을 수행 하는 데 8 분 59 초 걸렸습니다.

![Site Recovery 작업 목록](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>복구 플랜에 자동화 Runbook을 추가할 수 있나요?

예, Azure Automation Runbook을 복구 플랜에 통합할 수 있습니다. [Runbook Azure Automation 추가](site-recovery-runbook-automation.md)에 대해 자세히 알아보세요.

## <a name="reprotection-and-failback"></a>다시 보호 및 장애 복구

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>주 지역에서 재해 복구 지역으로 장애 조치 (failover) 합니다. DR 지역의 Vm이 자동으로 보호 되나요?

아니요. Azure VM을 한 지역에서 다른 지역으로 [장애 조치(failover)](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)할 경우 VM은 DR 지역에서 보호되지 않는 상태로 부팅됩니다. VM을 주 지역으로 장애 복구(failback)하려면 보조 지역에서 VM을 [다시 보호](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)해야 합니다.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>다시 보호 시 Site Recovery에서 보조 지역의 전체 데이터를 주 지역에 복제하나요?

상황에 따라 다릅니다. 원본 지역 VM이 있으면 원본 디스크와 대상 디스크 간의 변경 내용만 동기화 됩니다. Site Recovery는 디스크를 비교하여 차등을 계산한 다음, 데이터를 전송합니다. 이 프로세스는 일반적으로 몇 시간 정도 걸립니다. 다시 보호 하는 동안 발생 하는 상황에 대 한 자세한 내용은 [주 지역으로 장애 조치 (failover) 된 AZURE VM 인스턴스](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)다시 보호를 참조 하세요.

### <a name="how-much-time-does-it-take-to-fail-back"></a>장애 복구(failback)하는 데 시간이 얼마나 걸리나요?

다시 보호 후에 장애 복구는 주 지역에서 보조 지역으로 장애 조치 (failover) 하는 데 걸리는 시간과 동일 합니다.

## <a name="capacity"></a>수용

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Azure Vm의 대상 지역에서 용량을 어떻게 보장 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분 한 인프라 용량을 계획 하 고 있습니다. 장애 조치 (failover)를 시작할 때 팀은 Site Recovery로 보호 되는 VM 인스턴스가 대상 지역에 배포 되도록 하는 데도 도움이 됩니다.

### <a name="does-site-recovery-work-with-reserved-instances"></a>예약 된 인스턴스를 사용할 Site Recovery 있나요?

예, 재해 복구 지역에서 [예약 된 Azure vm](https://azure.microsoft.com/pricing/reserved-vm-instances/) 을 구입할 수 있으며 Site Recovery 장애 조치 (failover) 작업에서 사용 됩니다. 추가 구성은 필요하지 않습니다.

## <a name="security"></a>보안

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?

아니요, Site Recovery는 복제 된 데이터를 가로채 않으며 Vm에서 실행 되는 항목에 대 한 정보를 포함 하지 않습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.  

Site Recovery는 ISO 27001:2013, 27018, HIPAA 및 DPA 인증을 받았습니다. 서비스에서 SOC2 및 FedRAMP JAB 평가를 진행 중입니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?

예, [Azure에서](https://docs.microsoft.com/azure/storage/storage-service-encryption) 전송 중 및 미사용 암호화의 암호화가 모두 지원 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure-azure 지원 요구 사항을 검토](azure-to-azure-support-matrix.md)합니다.
- [Azure 간 복제를 설정](azure-to-azure-tutorial-enable-replication.md)합니다.
- 이 문서를 읽은 후 질문이 있으면 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 게시해 주세요.

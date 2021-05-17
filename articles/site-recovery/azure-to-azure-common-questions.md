---
title: Azure Site Recovery를 사용한 Azure VM 재해 복구에 대한 일반적인 질문
description: 이 문서에서는 Azure Site Recovery를 사용할 경우의 Azure VM 재해 복구에 대한 일반적인 질문에 답변합니다.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: 5309fd60640c45ade42bab4c5727cf1f0a8d9d70
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025478"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>일반적인 질문: Azure 간 재해 복구

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용 하 여 다른 azure 지역에 azure vm의 재해 복구에 대 한 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="how-is-site-recovery-priced"></a>Site Recovery 비용은 어떻게 책정하는가요?

Azure VM 재해 복구에 대 한 [비용](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) 에 대해 알아봅니다.

### <a name="how-does-the-free-tier-work"></a>무료 계층은 어떻게 작동 하나요?

Site Recovery로 보호 되는 모든 인스턴스는 처음 31 일 동안 무료로 사용할 수 있습니다. 이 기간이 지나면 각 인스턴스에 대 한 보호 요금은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/site-recovery/)에 요약 되어 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=site-recovery)를 사용 하 여 비용을 예측할 수 있습니다.

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>처음 31 일 동안 다른 Azure 요금이 발생 하나요?

예. 보호된 인스턴스를 처음으로 사용하는 31일 동안 Azure Site Recovery는 무료이지만, Azure Storage, Storage 트랜잭션 및 데이터 전송 요금이 부과될 수 있습니다. 복구 된 VM에도 Azure 계산 요금이 부과 될 수 있습니다. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Azure VM 재해 복구를 시작할 어떻게 할까요? 있나요?

1. Azure VM 재해 복구 아키텍처를 [이해](azure-to-azure-architecture.md) 합니다.
2. 지원 요구 사항을 [검토](azure-to-azure-support-matrix.md)합니다.
3. Azure Vm에 대 한 재해 복구를 [설정](azure-to-azure-how-to-enable-replication.md) 합니다.
4. 테스트 장애 조치 (failover)를 사용 하 여 [재해 복구 훈련을 실행](azure-to-azure-tutorial-dr-drill.md) 합니다.
5. 보조 Azure 지역에 대 한 [전체 장애 조치 (failover)를 실행](azure-to-azure-tutorial-failover-failback.md) 합니다.
6. 보조 지역에서 주 지역으로 [장애 복구 (failback](azure-to-azure-tutorial-failback.md) ) 합니다.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>대상 지역에서 용량을 확인 하려면 어떻게 해야 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분 한 인프라 용량을 계획 합니다. 장애 조치 (failover)를 시작 하는 경우 팀은 Site Recovery 의해 보호 되는 VM 인스턴스가 대상 지역에 배포 되도록 하는 데도 도움이 됩니다.

## <a name="replication"></a>복제

### <a name="can-i-replicate-vms-with-disk-encryption"></a>디스크 암호화를 사용 하 여 Vm을 복제할 수 있나요?

예. Site Recovery은 Azure Disk Encryption (ADE)를 사용 하도록 설정 된 Vm의 재해 복구를 지원 합니다. 복제를 사용 하도록 설정 하면 Azure는 필요한 모든 디스크 암호화 키와 암호를 원본 지역의 사용자 컨텍스트에서 대상 지역으로 복사 합니다. 필요한 권한이 없는 경우 보안 관리자는 스크립트를 사용 하 여 키와 비밀을 복사할 수 있습니다.

- Site Recovery은 Windows를 실행 하는 Azure Vm에 대해 ADE를 지원 합니다.
- Site Recovery 지원:
    - Azure Active Directory (Azure AD)를 필요로 하는 스키마가 있는 ADE 버전 0.1.
    - Azure AD가 필요 하지 않은 ADE 버전 1.1. 버전 1.1의 경우 Windows Azure Vm에는 관리 디스크가 있어야 합니다.
    - [자세히 알아보기](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). 확장 스키마 정보

암호화된 VM에 대해 복제를 사용하도록 설정하는 방법을 [자세히 알아보세요](azure-to-azure-how-to-enable-replication-ade-vms.md).

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>다른 리소스 그룹에서 automation 계정을 선택할 수 있나요?

복제 된 Azure Vm에서 실행 되는 모바일 서비스 확장에 대 한 업데이트를 관리 하기 위해 Site Recovery 하는 경우 azure automation 계정을 통해 Azure 서비스에서 사용 되는 글로벌 runbook을 배포 합니다. Site Recovery 만드는 automation 계정을 사용 하거나 기존 automation 계정을 사용 하도록 선택할 수 있습니다. 

현재는 포털에서 자격 증명 모음과 동일한 리소스 그룹에 있는 automation 계정만 선택할 수 있습니다. PowerShell을 사용 하 여 다른 리소스 그룹에서 automation 계정을 선택할 수 있습니다. [자세히 알아보기](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>자격 증명 모음 리소스 그룹에 없는 고객 automation 계정을 사용 하는 경우 기본 runbook을 삭제할 수 있나요?

예, 필요 하지 않은 경우 삭제할 수 있습니다. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>VM을 다른 구독에 복제할 수 있나요?

예, 동일한 Azure AD 테 넌 트 내의 모든 구독에 Azure Vm을 복제할 수 있습니다. Vm에 대 한 재해 복구를 사용 하도록 설정 하는 경우 기본적으로 표시 되는 대상 구독은 원본 VM의 것입니다. 대상 구독을 수정할 수 있으며, 리소스 그룹 및 가상 네트워크와 같은 기타 설정이 선택한 구독에서 자동으로 채워집니다.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>가용성 영역에서 다른 지역으로 Vm을 복제할 수 있나요?

예, 가용성 영역에 있는 Vm을 다른 Azure 지역에 복제할 수 있습니다. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>영역이 아닌 Vm을 동일한 지역 내의 영역에 복제할 수 있나요? 

이는 포털에서 지원 되지 않습니다. REST API/PowerShell을 사용 하 여이 작업을 수행할 수 있습니다.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>배열로 영역 설정 Vm을 동일한 지역의 다른 영역으로 복제할 수 있나요?

이에 대 한 지원은 몇 개 지역으로 제한 됩니다. [자세히 알아보기](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>디스크를 복제에서 제외할 수 있나요?

예, PowerShell을 사용 하 여 복제를 설정할 때 디스크를 제외할 수 있습니다. [자세히 알아보기](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>복제 된 Vm에 추가 된 새 디스크를 복제할 수 있나요?

Managed disks가 있는 복제 된 Vm의 경우 새 디스크를 추가 하 고 해당 디스크에 대해 복제를 사용 하도록 설정할 수 있습니다. 새 디스크를 추가 하는 경우 복제 된 VM은 VM에 있는 하나 이상의 디스크를 보호할 수 있다는 경고 메시지를 표시 합니다. 

- 추가 된 디스크에 대해 복제를 사용 하도록 설정 하면 초기 복제 후에 경고가 사라집니다.
- 디스크에 대해 복제를 사용 하도록 설정 하지 않으려는 경우 경고를 해제할 수 있습니다.
- 추가 된 디스크를 사용 하 여 VM을 장애 조치 (failover) 하는 경우 복제 지점은 복구에 사용할 수 있는 디스크를 표시 합니다. 예를 들어 한 디스크를 사용 하는 VM에 두 번째 디스크를 추가 하는 경우 추가 전에 만든 복제 지점이 "디스크 2 개 중 1 개"로 표시 됩니다.

Site Recovery는 복제 된 VM에서 디스크의 "핫 제거"를 지원 하지 않습니다. VM 디스크를 제거 하는 경우 VM에 대 한 복제를 사용 하지 않도록 설정 하 고 다시 활성화 해야 합니다.

### <a name="how-often-can-i-replicate-to-azure"></a>Azure에 얼마나 자주 복제할 수 있나요?

Azure VM을 다른 Azure 지역에 복제하는 경우에는 복제가 연속적입니다. 복제 작동 방식에 [대해 자세히 알아보세요](./azure-to-azure-architecture.md#replication-process) .

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>한 지역 내에서 가상 머신을 복제할 수 있나요? 

Site Recovery를 사용 하 여 지역 내에서 디스크를 복제할 수 없습니다.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>VM 인스턴스를 Azure 지역에 복제할 수 있나요?

동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의하여 정의됩니다. 지역 지원에 [대해 자세히 알아보세요](./azure-to-azure-support-matrix.md#region-support) .

### <a name="does-site-recovery-need-internet-connectivity"></a>인터넷 연결이 필요 Site Recovery?

아니요, 하지만 Vm은 Site Recovery Url 및 IP 범위에 액세스할 수 있어야 합니다. [자세히 알아보기](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>리소스 그룹 전체에 계층화 된 응용 프로그램을 복제할 수 있나요?

예, 앱을 복제 하 고 재해 복구 구성을 별도의 리소스 그룹에 유지할 수 있습니다.

예를 들어 앱의 다른 리소스 그룹에 3 개의 계층 (응용 프로그램/데이터베이스/웹)이 있는 경우 모든 계층을 보호 하기 위해 복제를 세 번 사용 하도록 설정 해야 합니다. Site Recovery 3 개의 계층을 세 개의 다른 리소스 그룹으로 복제 합니다.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>리소스 그룹에서 스토리지 계정을 이동할 수 있나요?

아니요, 지원 되지 않습니다. 실수로 저장소 계정을 다른 리소스 그룹으로 이동 하 고 원래 리소스 그룹을 삭제 하는 경우 이전 리소스 그룹과 같은 이름으로 새 리소스 그룹을 만든 다음 저장소 계정을이 리소스 그룹으로 이동할 수 있습니다.

## <a name="replication-policy"></a>복제 정책

### <a name="what-is-a-replication-policy"></a>복제 정책은 무엇인가요?

복제 정책은 복구 지점의 보존 기록과 앱 일치 스냅숏의 빈도를 정의 합니다.  Site Recovery은 다음과 같이 기본 복제 정책을 만듭니다.

- 24 시간 동안 복구 지점이 유지 됩니다.
- 4 시간 마다 앱 일치 스냅숏을 사용 합니다.

복제 설정에 [대해 자세히 알아보세요](azure-to-azure-how-to-enable-replication.md#customize-target-resources) .

### <a name="whats-a-crash-consistent-recovery-point"></a>크래시 일치 복구 지점은 무엇 인가요?

크래시 일관성 복구 지점에는 스냅숏 중에 서버에서 전원 코드를 가져온 것 처럼 디스크에 있는 데이터가 포함 됩니다. 스냅숏이 만들어질 때 메모리에 있던 항목은 포함 되지 않습니다.

현재 대부분의 앱은 충돌에 일관성이 있는 스냅숏에서 잘 복구할 수 있습니다. 크래시 일치 복구 지점은 일반적으로 데이터베이스 이외의 운영 체제와 파일 서버, DHCP 서버 및 인쇄 서버와 같은 앱에서 충분 합니다.

Site Recovery은 5 분 마다 충돌 일치 복구 지점을 자동으로 만듭니다.

### <a name="whats-an-application-consistent-recovery-point"></a>응용 프로그램 일치 복구 지점 이란?

앱 일치 복구 지점은 앱 일치 스냅샷에서 생성됩니다. 또한 충돌에 일관성이 있는 스냅숏과 동일한 데이터를 캡처하고 메모리의 데이터와 프로세스의 모든 트랜잭션을 캡처합니다.

추가 콘텐츠로 인해 앱 일치 스냅숏이 가장 많이 사용 되며 가장 긴 시간이 걸립니다. 데이터베이스 운영 체제에 대 한 앱 일치 복구 지점과 SQL Server 같은 앱을 권장 합니다. Windows의 경우 응용 프로그램 일치 스냅숏은 VSS (볼륨 섀도 복사본 서비스)를 사용 합니다.

### <a name="do-app-consistent-recovery-points-impact-performance"></a>앱 일치 복구 지점이 성능에 영향을 주는지 여부

 앱 일치 복구 지점은 메모리 및 프로세스의 모든 데이터를 캡처하기 때문에 자주 캡처하는 경우 워크 로드가 이미 사용 중인 경우 성능에 영향을 줄 수 있습니다. 데이터베이스 이외의 작업에는 너무 자주 캡처하지 않는 것이 좋습니다. 데이터베이스 워크 로드의 경우에도 1 시간으로 충분 합니다.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>앱 일치 복구 지점이 생성 되는 최소 빈도는 무엇 인가요?

Site Recovery는 최소 빈도로 1 시간의 앱 일치 복구 지점이 생성 될 수 있습니다.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Linux Vm에 대 한 앱 일치 복제를 사용할 수 있나요?

예. Linux 용 모바일 에이전트는 앱 일관성을 위한 사용자 지정 스크립트를 지원 합니다. 사전 및 사후 옵션을 포함 하는 사용자 지정 스크립트는 에이전트에서 사용 됩니다. [자세히 알아보기](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>복구 지점은 어떻게 생성 및 저장되나요?

Site Recovery에서 복구 지점이 생성 되는 방식을 이해 하기 위해 예제를 사용 하겠습니다. 

- 복제 정책은 24 시간 동안 복구 지점이 유지 되 고 1 시간 마다 앱 일치 빈도 스냅숏을 사용 합니다.
- Site Recovery은 5 분 마다 크래시 일치 복구 지점을 만듭니다. 사용자가 이 빈도를 변경할 수는 없습니다.
- Site Recovery은 1 시간 후에 복구 지점을 정리 하 고 시간당 1 포인트를 저장 합니다.

따라서 지난 1 시간 동안 그래픽에 표시 된 것 처럼 12 개의 크래시 일치 지점 및 하나의 앱 일치 지점 중에서 선택할 수 있습니다.

   ![생성된 복구 지점 목록](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>복구할 수 있는 가장 오랜 복구 지점은 어떻게 되나요?

사용할 수 있는 가장 오래된 복구 지점은 72시간입니다.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Site Recovery 24 시간 넘게 복구 지점이 생성 되지 않으면 어떻게 되나요? 

복제 정책이 24 시간이 고 Site Recovery 24 시간 넘게 복구 지점이 생성 되지 않으면 이전 복구 지점은 유지 됩니다. Site Recovery 새 점을 생성 하는 경우 가장 오래 된 요소만 바꿉니다. 새 복구 지점이 있을 때까지 모든 이전 지점은 보존 기간에 도달한 후에도 유지 됩니다.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>복제를 사용 하도록 설정한 후 복제 정책을 변경할 수 있나요?

예. 자격 증명 모음 > **Site Recovery 인프라**  >  **복제 정책** 에서 정책을 선택 하 고 편집 합니다. 변경 내용은 기존 정책에도 적용 됩니다.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>모든 복구 지점이 완전 한 VM 복사본 입니까?

생성되는 첫 번째 복구 지점에 전체 복사본이 있습니다. 연속 복구 지점은 델타 변경 내용을 포함 합니다.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>복구 지점 보존이 증가 하면 저장소 비용이 증가 하나요?

예. 예를 들어 24 시간에서 72으로 보존 기간을 늘리면 Site Recovery 추가 48 시간 동안 복구 지점이 저장 됩니다. 추가 된 시간에 저장소 변경 내용이 적용 됩니다. 예를 들어, 단일 복구 지점에 10gb의 델타 변경 내용이 있는 경우 월별 $0.16의 GB 당 요금이 부과 되는 경우 추가 요금은 월 $1.60 × 48가 됩니다.

## <a name="multi-vm-consistency"></a>다중 VM 일관성

### <a name="what-is-multi-vm-consistency"></a>다중 VM 일관성이란 무엇인가요?

다중 VM 일관성은 복제 된 가상 컴퓨터에서 복구 지점이 일관 되도록 합니다.

- 다중 VM 일관성을 사용 하도록 설정 하면 Site Recovery 옵션을 사용 하도록 설정 하 여 모든 컴퓨터의 복제 그룹을 만듭니다. 
- 복제 그룹의 컴퓨터를 장애 조치 (failover) 하는 경우 공유 크래시 일관성 및 앱 일치 복구 지점이 있습니다.

다중 VM 일관성을 사용 하도록 설정 하는 방법을 [알아봅니다](azure-to-azure-tutorial-enable-replication.md#enable-replication) .

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>복제 그룹에서 단일 VM을 장애 조치 (failover) 할 수 있나요?

아니요. 다중 VM 일관성을 사용 하도록 설정 하면 앱이 복제 그룹의 모든 Vm에 대 한 종속성을 가지 며 단일 VM 장애 조치 (failover)가 허용 되지 않는 것으로 유추 됩니다.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>그룹에서 함께 복제할 수 있는 VM 수는 몇 개입니까?

복제 그룹에서 16 개의 Vm을 함께 복제할 수 있습니다.

### <a name="when-should-i-enable-multi-vm-consistency"></a>다중 VM 일관성을 언제 사용하도록 설정해야 하나요?

다중 VM 일관성은 CPU를 많이 사용 하며 사용 하도록 설정 하면 워크 로드 성능에 영향을 줄 수 있습니다. Vm에서 동일한 작업을 실행 하 고 여러 컴퓨터에서 일관성을 유지 해야 하는 경우에만 사용 하도록 설정 합니다. 예를 들어 응용 프로그램에 두 개의 SQL Server 인스턴스와 두 개의 웹 서버가 있는 경우 SQL Server 인스턴스에만 다중 VM 일관성을 사용 하도록 설정 합니다.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>복제 그룹에 복제 VM을 추가할 수 있나요?

VM에 대 한 복제를 사용 하도록 설정 하는 경우 새 복제 그룹이 나 기존 그룹에 추가할 수 있습니다. 이미 그룹에 복제 중인 VM은 추가할 수 없습니다. 
 
## <a name="failover"></a>장애 조치 

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>대상 지역에서 용량을 확인 하려면 어떻게 해야 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분 한 인프라 용량을 계획 합니다. 장애 조치 (failover)를 시작 하면 Site Recovery로 보호 되는 VM 인스턴스를 대상 지역에 배포할 수 있는지 확인 하는 데도 도움이 됩니다.

### <a name="is-failover-automatic"></a>장애 조치(failover)는 자동입니까?

자동이 아닙니다. 포털에서 한 번의 클릭으로 장애 조치 (failover)를 시작 하거나  [PowerShell](azure-to-azure-powershell.md) 을 사용 하 여 장애 조치 (failover)를 트리거할 수 있습니다.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>장애 조치 (failover) 후 공용 IP 주소를 유지할 수 있나요?

장애 조치 (failover) 후에는 프로덕션 앱에 대 한 공용 IP 주소를 유지할 수 없습니다.

장애 조치 (failover) 프로세스의 일부로 워크 로드를 가져오는 경우 Azure 공용 IP 주소 리소스를 할당 해야 합니다. 리소스는 대상 지역에서 사용할 수 있어야 합니다. Azure 공용 IP 주소 리소스를 수동으로 할당 하거나 복구 계획을 사용 하 여 자동화할 수 있습니다. 장애 조치 (failover) 후 공용 IP 주소를 설정 하는 방법을 [알아봅니다](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) .

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>장애 조치 (failover) 후 개인 IP 주소를 유지할 수 있나요?

예. 기본적으로 Azure Vm에 대 한 재해 복구를 사용 하도록 설정 하면 Site Recovery는 원본 리소스 설정에 따라 대상 리소스를 만듭니다. 고정 IP 주소를 사용하여 구성된 Azure VM의 경우 Site Recovery는 대상 VM에 동일한 IP 주소(사용 중이 아닌 경우)를 프로비전하려고 시도합니다.
장애 조치 (failover) 후 IP 주소 유지 [에 대해 자세히 알아보세요](site-recovery-retain-ip-azure-vm-failover.md) .

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>장애 조치 (failover) 후 VM이 새 IP 주소를 할당 하는 이유는 무엇 인가요?

Site Recovery는 장애 조치(failover) 시 IP 주소를 제공하려고 시도합니다. 다른 VM에서 해당 주소를 사용 하는 경우 Site Recovery은 대상으로 사용 가능한 다음 IP 주소를 설정 합니다.

가상 네트워크에 대 한 IP 주소 지정 및 네트워크 매핑을 설정 하는 [방법에 대해 자세히 알아보세요](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) .

### <a name="whats-the-latest-recovery-point"></a>*최신* 복구 지점은 무엇 인가요?

*최신 (가장 낮은 RPO)* 복구 지점 옵션은 다음을 수행 합니다.

1. 먼저 Site Recovery로 전송 된 모든 데이터를 처리 합니다.
2. 서비스는 데이터를 처리 한 후 VM으로 장애 조치 (failover) 하기 전에 각 VM에 대 한 복구 지점을 만듭니다. 이 옵션은 가장 낮은 RPO(복구 지점 목표)를 제공합니다.
3. 장애 조치 (failover) 후 생성 된 VM에는 장애 조치 (failover)가 트리거될 때에서 Site Recovery로 복제 된 모든 데이터가 포함 됩니다.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>*최신* 복구 지점이 장애 조치 RTO에 영향을 미칩니까?

예. Site Recovery는 장애 조치 (failover) 전에 보류 중인 모든 데이터를 처리 하므로이 옵션은 다른 옵션 보다 더 높은 RTO (복구 시간 목표)를 포함 합니다.

### <a name="whats-the-latest-processed-recovery-option"></a>*가장 최근에 처리* 된 복구 옵션은 무엇 인가요?

*가장 최근에 처리* 된 옵션은 다음을 수행 합니다.

1. Site Recovery에서 처리 된 최신 복구 지점으로 모든 Vm을 장애 조치 (failover) 합니다. 이 옵션은 처리되지 않은 데이터를 처리하는 데 시간이 투입되지 않으므로 낮은 RTO를 제공합니다.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>주 지역에서 예기치 않은 중단이 발생 하면 어떻게 되나요?

장애 조치 (failover)를 시작할 수 있습니다. Site Recovery는 주 지역에서의 연결이 없어도 장애 조치를 수행할 수 있습니다.

### <a name="what-is-the-rto-of-a-vm-failover"></a>VM 장애 조치 (failover)의 RTO는 무엇 인가요?

Site Recovery의 RTO SLA는 [2 시간](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)입니다. 대부분의 경우 Site Recovery은 몇 분 내에 Vm을 장애 조치 (failover) 합니다. RTO를 계산 하려면 장애 조치 (failover) 작업을 검토 하 여 VM을 가져오는 데 걸린 시간을 표시 합니다. 

## <a name="recovery-plans"></a>복구 계획

### <a name="whats-a-recovery-plan"></a>복구 계획 이란?

Site Recovery의 [복구 계획](site-recovery-create-recovery-plans.md) 은 vm의 장애 조치 (failover) 및 복구를 오케스트레이션 합니다. 복구를 지속적으로 정확 하 게 정확 하 게 유지 하 고 자동화 하는 데 도움이 됩니다. 메서드는 다음 작업을 수행합니다.

- 함께 장애 조치 (failover) 되는 Vm 그룹을 정의 합니다.
- 응용 프로그램을 정확 하 게 가져오도록 Vm 간의 종속성을 정의 합니다.
- 는 VM 장애 조치 이외의 작업에 대 한 사용자 지정 수동 작업의 옵션을 사용 하 여 복구를 자동화 합니다. 


### <a name="how-does-sequencing-work"></a>시퀀싱은 어떻게 작동 하나요?

복구 계획에서는 시퀀싱을 위해 VM의 여러 그룹을 만들 수 있습니다. 그룹은 한 번에 하나씩 장애 조치 (failover) 되므로 동일한 그룹에 속하는 Vm은 함께 장애 조치 (failover) 됩니다. [자세히 알아보기](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>복구 플랜의 RTO를 확인하려면 어떻게 할까요?

복구 계획의 RTO를 확인 하려면 복구 계획에 대해 테스트 장애 조치 (failover)를 수행 합니다. **Site Recovery 작업** 에서 테스트 장애 조치 (failover) 기간을 확인 합니다. 예제 스크린샷에서 **SAPTestRecoveryPlan** 테스트 장애 조치 (failover) 작업에는 8 분 59 초가 소요 되었습니다.

![RTO에 대 한 테스트 장애 조치 (failover) 기간을 보여 주는 작업 나열](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>자동화 runbook을 복구 계획에 추가할 수 있나요?

예. [자세히 알아보기](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>다시 보호 및 장애 복구

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>장애 조치 (failover) 후에 보조 지역의 Vm이 자동으로 보호 되나요? 

아니요. Vm을 한 지역에서 다른 지역으로 장애 조치 (failover) 하는 경우 Vm은 대상 재해 복구 지역에서 보호 되지 않은 상태로 시작 됩니다. 보조 지역에서 vm을 다시 보호 하려면 주 지역으로 [복제를 다시](./azure-to-azure-how-to-reprotect.md) 사용 하도록 설정 합니다.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>다시 보호 하는 경우 모든 데이터가 보조 지역에서 주 지역으로 복제 되나요? 

그것은 사정 나름이에요. 원본 지역 VM이 있으면 원본 디스크와 대상 디스크 간의 변경 내용만 동기화 됩니다. Site Recovery는 디스크를 다른 것과 비교한 다음 데이터를 전송 합니다. 이 프로세스는 일반적으로 몇 시간 정도 걸립니다. [자세히 알아보기](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>장애 복구 (failback)는 얼마나 걸립니까?

다시 보호 후에 장애 복구는 주 지역에서 보조 지역으로 장애 조치 (failover) 하는 데 걸리는 시간과 동일 합니다.

## <a name="capacity"></a><a name="capacity"></a>용량

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>대상 지역에서 용량을 확인 하려면 어떻게 해야 하나요?

Site Recovery 팀 및 Azure 용량 관리 팀은 충분한 인프라 용량을 계획하고 있습니다. 장애 조치 (failover)를 시작 하면 Site Recovery로 보호 되는 VM 인스턴스를 대상 지역에 배포할 수 있는지 확인 하는 데도 도움이 됩니다.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery를 예약 인스턴스에 사용할 수 있나요?

예, 재해 복구 지역에서 [예약 된 Azure vm](https://azure.microsoft.com/pricing/reserved-vm-instances/) 을 구입할 수 있으며 Site Recovery 장애 조치 (failover) 작업에서이 vm을 사용할 수 있습니다. 추가 구성은 필요하지 않습니다.

## <a name="security"></a>보안

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>복제 데이터가 Site Recovery 서비스로 전송되나요?

아니요, Site Recovery는 복제된 데이터를 가로채지 않으며, VM에서 실행되는 항목에 대한 정보를 갖지 않습니다. 복제 오케스트레이션 및 장애 조치(failover)에 필요한 메타데이터만 Site Recovery 서비스로 전송됩니다.

Site Recovery는 ISO 27001:2013, 27018, HIPAA 및 DPA 인증을 받았습니다. 이 서비스에 대해 SOC2 및 FedRAMP JAB 평가가 진행 중입니다.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery는 복제를 암호화합니까?

예, 전송 중 암호화 및 [Azure의 저장소 데이터 암호화](../storage/common/storage-service-encryption.md)가 모두 지원됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 간 지원 요구 사항을 검토합니다](azure-to-azure-support-matrix.md).
- [Azure 간 복제를 설정합니다](azure-to-azure-tutorial-enable-replication.md).
- 이 문서를 읽은 후 질문이 있으면 [Azure Recovery Services에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-site-recovery.html)에 게시해 주세요.

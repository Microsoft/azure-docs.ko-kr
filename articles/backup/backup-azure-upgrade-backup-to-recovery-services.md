---
title: Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음 업그레이드
description: Recovery Services 자격 증명 모음으로 Azure Backup 자격 증명 모음을 업그레이드하는 지침 및 지원 정보입니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 1/4/2018
ms.author: sogup
ms.openlocfilehash: b4ecebc6bef7f49a23455c7a85f25680df087a95
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848370"
---
# <a name="upgrade-a-backup-vault-to-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음 업그레이드

이 문서에서는 Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음을 업그레이드하는 방법을 설명합니다. 업그레이드 프로세스는 실행 중인 백업 작업에 영향을 주지 않고 백업 데이터는 손실되지 않습니다. Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음을 업그레이드하는 주요 목적은 다음과 같습니다.
- Recovery Services 자격 증명 모음에 Backup 자격 증명 모음의 기능이 모두 유지됩니다.
- Recovery Services 자격 증명 모음은 보안 향상, 모니터링 통합, 빠른 복원 및 항목 수준의 복원을 포함하여 Backup 자격 증명 모음보다 더 많은 기능을 제공합니다.
- 향상되고 간소화된 포털에서 백업 항목을 관리합니다.
- 새로운 기능은 Recovery Services 자격 증명 모음에만 적용됩니다.

## <a name="impact-to-operations-during-upgrade"></a>업그레이드 중에 작업에 대한 영향

Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음을 업그레이드하더라도 데이터 평면 작업에는 영향을 주지 않습니다. 모든 백업 작업이 정상적으로 계속되고 모든 활성 복원 작업도 중단없이 계속됩니다. 업그레이드 중에는 관리 작업에 짧은 가동 중지 시간이 발생하고 새 항목을 보호하거나 임시 백업 작업을 만들 수 없습니다. IaaS VM에 대한 복원 작업은 업그레이드 중에 실행되지 않습니다. 자격 증명 모음 업그레이드를 완료하는 데 한 시간 정도가 걸립니다. 작업이 완료되면 Recovery Services 자격 증명 모음은 Backup 자격 증명 모음을 대체합니다.

## <a name="changes-to-your-automation-and-tool-after-upgrading"></a>업그레이드 후에 자동화 및 도구 변경

자격 증명 모음 업그레이드를 위한 인프라를 준비하는 동안 기존 자동화 또는 도구를 업데이트하여 업그레이드 후에 작업을 계속하도록 해야 합니다.
[리소스 관리자 배포 모델](backup-client-automation.md)에 대한 PowerShell cmdlet 참조를 확인하세요.


## <a name="before-you-upgrade"></a>업그레이드하기 전에

Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음을 업그레이드하기 전에 다음과 같은 문제를 확인합니다.

- **최소 에이전트 버전**: 자격 증명 모음을 업그레이드하려면 MARS(Microsoft Azure Recovery Services) 에이전트가 2.0.9083.0 버전 이상인지 확인합니다. MARS 에이전트가 2.0.9083.0보다 오래된 경우 업그레이드 프로세스를 시작하기 전에 에이전트를 업데이트합니다.
- **인스턴스 기반 청구 모델**: Recovery Services 자격 증명 모음은 인스턴스 기반 청구 모델만을 지원합니다. 백업 자격 증명 모음이 이전 저장소 기반 청구 모델을 사용하는 경우 업그레이드하는 동안 청구 모델을 변환합니다.
- **진행 중인 백업 구성 작업 없음**: 업그레이드하는 동안 관리 평면에 대한 액세스가 제한됩니다. 모든 관리 평면 작업을 완료한 다음 업그레이드를 시작합니다.

## <a name="using-powershell-scripts-to-upgrade-your-vaults"></a>PowerShell 스크립트를 사용하여 자격 증명 모음 업그레이드

PowerShell 스크립트를 사용하여 Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음을 업그레이드할 수 있습니다. 자격 증명 모음 업그레이드를 트리거하는 데 필요한 PowerShell 구성 요소가 있는지 확인합니다. Backup 자격 증명 모음에 대한 PowerShell 스크립트는 Recovery Services 자격 증명 모음에서 작동하지 않습니다. 자격 증명 모음을 업그레이드하기 위한 환경을 준비합니다.

1. [WMF(Windows Management Framework) 버전 5](https://www.microsoft.com/download/details.aspx?id=50395) 이상 설치 또는 업그레이드
2. [Azure PowerShell MSI 설치](https://github.com/Azure/azure-powershell/releases/download/v3.8.0-April2017/azure-powershell.3.8.0.msi)
3. [PowerShell 스크립트](https://aka.ms/vaultupgradescript2)를 다운로드하여 자격 증명 모음 업그레이드

### <a name="run-the-powershell-script"></a>PowerShell 스크립트 실행

다음 스크립트를 사용하여 자격 증명 모음 업그레이드 다음 샘플 스크립트에서는 매개 변수를 설명합니다.

RecoveryServicesVaultUpgrade-1.0.2.ps1 **-SubscriptionID** `<subscriptionID>` **-VaultName** `<vaultname>` **-Location** `<location>` **-ResourceType** `BackupVault` **-TargetResourceGroupName** `<rgname>`

**SubscriptionID** - 업그레이드되는 자격 증명 모음 구독 ID 번호입니다.<br/>
**VaultName** - 업그레이드되는 Backup 자격 증명 모음 이름입니다.<br/>
**Location** - 자격 증명 모음이 업그레이드되는 위치입니다.<br/>
**ResourceType** - BackupVault를 사용합니다.<br/>
**TargetResourceGroupName** - 자격 증명 모음을 리소스 관리자 기반 배포로 업그레이드하기 때문에 리소스 그룹을 지정합니다. 기존 리소스 그룹을 사용하거나 새 이름을 제공하여 새 리소스 그룹을 만들 수 있습니다. 리소스 그룹의 이름이 잘못된 경우 새 리소스 그룹을 만들 수 있습니다. 리소스 그룹에 대해 자세히 알아보려면 [리소스 그룹에 대한 개요](../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.

>[!NOTE]
> 리소스 그룹 이름에는 제약이 있습니다. 지침을 준수해야 합니다. 그렇지 않으면 자격 증명 모음 업그레이드에 실패할 수 있습니다.
>
>**Azure US Government** 고객은 스크립트를 실행하는 동안 환경을 "AzureUSGovernment"로 설정해야 합니다.
>**Azure China** 고객은 스크립트를 실행하는 동안 환경을 "AzureChinaCloud"로 설정해야 합니다.

다음 코드 조각은 PowerShell 명령이 표시되는 예제입니다.

```
RecoveryServicesVaultUpgrade.ps1 -SubscriptionID 53a3c692-5283-4f0a-baf6-49412f5ebefe -VaultName "TestVault" -Location "Australia East" -ResourceType BackupVault -TargetResourceGroupName "ContosoRG"
```

매개 변수 없이 스크립트를 실행할 수도 있고 모든 필수 매개 변수에 대한 입력을 제공하도록 요구합니다.

PowerShell 스크립트는 자격 증명을 입력하라는 메시지를 표시합니다. 자격 증명을 두 번 입력합니다. 한 번은 서비스 관리자 계정에서, 두 번째는 Resource Manager 계정에서 입력합니다.

### <a name="pre-requisites-checking"></a>필수 구성 요소 확인
Azure 자격 증명을 입력하면 Azure에서는 환경이 다음과 같은 전제 조건을 충족하는지 확인합니다.

- **최소 에이전트 버전** - Recovery Services 자격 증명 모음으로 백업 자격 증명 모음을 업그레이드하려면 적어도 MARS 에이전트 버전 2.0.9083.0이 필요합니다. 항목이 2.0.9083.0보다 이전인 에이전트를 사용하여 백업 자격 증명 모음에 등록하는 경우 필수 구성 요소 검사에 실패합니다. 필수 구성 요소 확인이 실패하면 에이전트를 업데이트하고 자격 증명 모음을 다시 업그레이드합니다. 최신 에이전트 버전은 [https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe](https://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe)에서 다운로드할 수 있습니다.
- **진행 중인 구성 작업**: 누군가가 업그레이드할 Backup 자격 증명 모음에 대한 작업을 구성하거나 항목을 등록하는 경우 필수 구성 요소 검사가 실패합니다. 구성을 완료하거나 항목의 등록을 마치고 자격 증명 모음 업그레이드 프로세스를 시작합니다.
- **스토리지 기반 청구 모델**: Recovery Services 자격 증명 모음은 인스턴스 기반 청구 모델을 지원합니다. 저장소 기반 청구 모델을 사용하는 Backup 자격 증명 모음에 자격 증명 모음 업그레이드를 실행하면 자격 증명 모음과 함께 요금 청구 모델을 업그레이드하라는 메시지가 표시됩니다. 그렇지 않으면 청구 모델을 먼저 업데이트한 다음 자격 증명 모음 업그레이드를 실행할 수 있습니다.
- Recovery Services 자격 증명 모음의 리소스 그룹을 식별합니다. Resource Manager 배포 기능을 활용하려면 리소스 그룹에서 Recovery Services 자격 증명 모음을 저장해야 합니다. 사용할 리소스 그룹을 모르는 경우 이름을 지정하고 업그레이드 프로세스에서 리소스 그룹을 만듭니다. 또한 업그레이드 프로세스는 새 리소스 그룹과 자격 증명 모음을 연결합니다.

업그레이드 프로세스가 필수 구성 요소 확인을 완료하면 프로세스는 자격 증명 모음 업그레이드를 시작하라는 메시지를 표시합니다. 이를 확인한 후에 업그레이드 프로세스가 완료되는 데 사용자 자격 증명 모음의 크기에 따라 일반적으로 15~20분 정도가 걸립니다. 큰 자격 증명 모음이 있는 경우 업그레이드에 최대 90분이 걸릴 수 있습니다.

## <a name="managing-your-recovery-services-vaults"></a>Recovery Services 자격 증명 모음 관리

다음 화면에서는 Azure Portal에서 Backup 자격 증명 모음에서 업그레이드된 새 Recovery Services 자격 증명 모음을 표시합니다. 첫 번째 화면에서는 자격 증명 모음에 대한 주요 엔터티를 표시하는 자격 증명 모음 대시보드를 보여줍니다.

![백업 자격 증명 모음에서 업그레이드된 Recovery Services 자격 증명 모음의 예](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

두 번째 화면에서는 Recovery Services 자격 증명 모음을 사용하기 시작하는 데 사용할 수 있는 도움말 링크를 보여줍니다.

![빠른 시작 블레이드의 도움말 링크](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>업그레이드 후 단계
Recovery Services 자격 증명 모음은 백업 정책에서 표준 시간대 정보를 지정하도록 지원합니다. 자격 증명 모음이 성공적으로 업그레이드되면 자격 증명 모음 설정 메뉴에서 Backup 정책으로 이동하고 자격 증명 모음에 구성된 각 정책에 대한 표준 시간대 정보를 업데이트합니다. 이 화면에서는 정책을 만든 경우 사용되는 로컬 표준 시간대당 지정된 백업 일정 시간을 표시합니다.

## <a name="enhanced-security"></a>향상된 보안

Recovery Services 자격 증명 모음에 Backup 자격 증명 모음을 업그레이드할 때 해당 자격 증명 모음에 대한 보안 설정은 자동으로 켜집니다. 보안 설정이 켜지면 백업 삭제 또는 암호 변경과 같은 특정 작업에는 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN이 필요합니다. 향상된 보안에 대한 자세한 내용은 [하이브리드 백업을 보호하는 보안 기능](backup-azure-security-feature.md) 문서를 참조하세요.

향상된 보안이 사용 설정된 경우 볼트에서 복구 지점 데이터가 삭제되고 최대 14일 후까지 데이터가 유지됩니다. 고객에게 이 보안 데이터의 저장에 대한 비용이 청구됩니다. 보안 이터 보존은 Azure Backup 에이전트, Azure Backup Server 및 System Center Data Protection Manager에 대해 가져온 복구 지점에 적용됩니다.

## <a name="gather-data-on-your-vault"></a>자격 증명 모음에서 데이터 수집

Recovery Services 자격 증명 모음으로 업그레이드하면 Azure Backup(IaaS VM 및 MARS(Microsoft Azure Recovery Services)의 경우)에 대한 보고서를 구성하고 Power BI를 사용하여 보고서에 액세스할 수 있습니다. 데이터 수집에 대한 자세한 내용은 [Azure Backup 보고서 구성](backup-azure-configure-reports.md)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-the-upgrade-plan-affect-my-ongoing-backups"></a>업그레이드 계획이 진행 중인 백업에 영향을 주나요?
아니요. 진행 중인 백업은 업그레이드 전후에도 중단되지 않고 계속됩니다.

### <a name="if-i-dont-plan-on-upgrading-soon-what-happens-to-my-vaults"></a>자격 증명 모음을 가까운 시일 안에 업그레이드할 계획이 없다면 어떻게 되나요?
새로운 기능이 모두 Recovery Services 자격 증명 모음에만 적용되기 때문에 자격 증명 모음을 업그레이드하는 것이 좋습니다. 2017년 9월 1일부터 Microsoft는 백업 자격 증명 모음을 Recovery Services 자격 증명 모음으로 자동 업그레이드하기 시작합니다. 2017년 11월 30일 이후에는 PowerShell을 사용하여 더 이상 Backup 자격 증명 모음을 만들 수 없습니다. 자격 증명 모음은 이 기간 중 언제든지 자동으로 업그레이드될 수 있습니다. 자격 증명 모음을 가능한 한 빨리 업그레이드하는 것이 좋습니다.

### <a name="what-does-this-upgrade-mean-for-my-existing-tooling"></a>이 업그레이드로 기존 도구는 어떻게 되나요?
도구를 Resource Manager 배포 모델로 업데이트합니다. Recovery Services 자격 증명 모음은 Resource Manager 배포 모델에서 사용하기 위해 만들었습니다. Resource Manager 배포 모델을 계획하고 자격 증명 모음의 차이점을 고려하는 것이 중요합니다.

### <a name="during-the-upgrade-is-there-much-downtime"></a>업그레이드하는 동안 작동 중단 시간이 많이 발생하나요?
업그레이드하는 리소스의 수에 따라 달라집니다. 소규모 배포(보호된 인스턴스가 수십 개)의 경우 전체 업그레이드는 20분이 걸리지 않습니다. 대규모 배포의 경우에 최대 1시간이 걸립니다.

### <a name="can-i-roll-back-after-upgrading"></a>업그레이드한 후에 롤백할 수 있나요?
아니요. 리소스를 성공적으로 업그레이드한 후에는 롤백이 지원되지 않습니다.

### <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-upgrade"></a>내 구독 또는 리소스에서 업그레이드가 가능한지 확인할 수 있나요?
예. 업그레이드의 첫 번째 단계는 리소스를 업그레이드할 수 있는지 유효성을 검사합니다. 필수 구성 요소의 유효성 검사 작업이 실패하는 경우, 업그레이드를 완료할 수 없는 모든 원인에 대한 모든 메시지가 나타납니다.

### <a name="can-i-upgrade-my-csp-based-backup-vault"></a>CSP 기반 백업 자격 증명 모음을 업그레이드할 수 있나요?
아니요. CSP 기반 백업 자격 증명 모음을 현재 업그레이드할 수 없습니다. 다음 릴리스에서 CSP 기반 Backup 자격 증명 모음을 업그레이드하는 지원을 추가할 예정입니다.

### <a name="can-i-view-my-classic-vault-post-upgrade"></a>클래식 자격 증명 모음을 업그레이드한 이후를 볼 수 있나요?
아니요. 클래식 자격 증명을 업그레이드한 이후를 보거나 관리할 수 없습니다. 자격 증명 모음에 있는 모든 관리 작업에 새 Azure Portal을 사용할 수만 있습니다.

### <a name="my-upgrade-failed-but-the-machine-that-held-the-agent-requiring-updating-doesnt-exist-anymore-what-do-i-do-in-such-a-case"></a>내 업그레이드에 실패했지만 업데이트가 필요한 에이전트를 보유한 머신이 더 이상 존재하지 않습니다. 이러한 경우 어떻게 해야 하나요?
장기 보존을 위해 이 컴퓨터의 백업 저장소를 사용해야 하는 경우 자격 증명 모음을 업그레이드할 수 없습니다. 이후 릴리스에서 이러한 자격 증명 모음을 업그레이드하는 지원을 추가할 예정입니다.
더 이상 이 컴퓨터의 백업을 저장하지 않아도 되는 경우 자격 증명 모음에서 이 컴퓨터의 등록을 취소하고 업그레이드를 다시 시도하세요.

### <a name="why-cant-i-see-the-jobs-information-for-my-resources-after-upgrade"></a>업그레이드 이후에 리소스에 대한 작업 정보를 볼 수 없는 이유는 무엇인가요?
백업(MARS 에이전트 및 IaaS)에 대한 모니터링은 Recovery Services 자격 증명 모음으로 백업 자격 증명 모음을 업그레이드할 때 얻을 수 있는 새로운 기능입니다. 모니터링 정보는 서비스와 동기화되는 데 최대 12시간이 걸립니다.

### <a name="how-do-i-report-an-issue"></a>문제를 보고하려면 어떻게 해야 하나요?
자격 증명 모음 업그레이드가 일부 실패하면 오류에 나열된 OperationId를 적어둡니다. Microsoft 지원은 문제를 해결하기 위해 사전 예방적으로 작동합니다. 지원에 문의하거나 구독 ID, 자격 증명 모음 이름 및 OperationId를 적은 이메일을 rsvaultupgrade@service.microsoft.com으로 보내주세요. 최대한 빨리 문제를 해결하려고 합니다. Microsoft에서 분명히 지시하지 않은 경우 작업을 다시 시도하지 마세요.


## <a name="next-steps"></a>다음 단계
다음과 같은 경우에 다음 문서를 사용합니다.</br>
[IaaS VM 백업](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server 백업](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server 백업](backup-configure-vault.md)

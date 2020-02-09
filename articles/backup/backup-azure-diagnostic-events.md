---
title: Recovery Services 자격 증명 모음에 대한 진단 설정 사용
description: Azure Backup에 대해 이전 및 새 진단 이벤트를 사용 하는 방법을 설명 하는 문서입니다.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 875adb82aeeb56b378a84ca01e716c7189abc64f
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281104"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services 자격 증명 모음용 진단 설정 사용

Azure Backup은 수집 하 여 분석, 경고 및 보고를 위해 사용할 수 있는 진단 이벤트를 보냅니다. 

자격 증명 모음으로 이동 하 고 **진단 설정** 메뉴 항목을 클릭 하 여 Azure Portal를 통해 Recovery Services 자격 증명 모음에 대한 진단 설정을 구성할 수 있습니다. **+ 진단 설정 추가** 를 클릭 하면 하나 이상의 진단 이벤트를 저장소 계정, 이벤트 허브 또는 LA (Log Analytics) 작업 영역으로 보낼 수 있습니다.

![진단 설정 블레이드](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup 사용자에 게 제공 되는 진단 이벤트

Azure Backup는 다음과 같은 진단 이벤트를 제공 합니다. 각 진단 이벤트는 특정 백업 관련 아티팩트의 집합에 대한 자세한 데이터를 제공 합니다.
* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage 

[Azure Backup 진단 이벤트에 대한 데이터 모델](https://aka.ms/diagnosticsdatamodel)

이러한 이벤트에 대한 데이터는 저장소 계정, LA 작업 영역 또는 이벤트 허브로 보낼 수 있습니다. 이 데이터를 LA 작업 영역으로 보내는 경우 **진단 설정** 화면에서 **리소스 특정** 설정/해제를 선택 해야 합니다 (아래 섹션의 자세한 내용 참조).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Log Analytics에서 진단 설정 사용 (LA)

Azure Log Analytics 로드맵에 맞춰 이제 Azure Backup를 사용 하 여 백업에 대한 전용 LA 테이블로 자격 증명 모음 진단 데이터를 보낼 수 있습니다. 이러한 [테이블을 리소스 관련 테이블](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)이라고 합니다.

자격 증명 모음 진단 데이터를 LA에 게 보내려면:
1.  자격 증명 모음으로 이동 하 고 **진단 설정**을 클릭 합니다. **+ 진단 설정 추가**를 클릭 합니다.
2.  진단 설정에 이름을 지정 합니다.
3.  **Log Analytics 보내기** 상자를 선택 하 고 Log Analytics 작업 영역을 선택 합니다.
4.  설정/해제에서 **특정 리소스** 를 선택 하 고 다음 6 개 이벤트 ( **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**및 **AddonAzureBackupStorage**)를 확인 합니다.
5.  **Save**를 클릭합니다.

![리소스 특정 모드](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

데이터가 LA 작업 영역으로 이동 하면 이러한 각 이벤트에 대한 전용 테이블이 작업 영역에 생성 됩니다. 이러한 테이블을 직접 쿼리 하 고 필요한 경우 이러한 테이블 간에 조인이 나 공용 구조체를 수행할 수도 있습니다.

> [!IMPORTANT]
> 위의 6 개 이벤트, 즉 CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy 및 AddonAzureBackupStorage는 리소스 특정 모드 **에서만** 지원 됩니다. **Azure 진단 모드에서 이러한 6 개의 이벤트에 대한 데이터를 보내려고 하면 LA 작업 영역으로 데이터가 전달 되지 않습니다.**

## <a name="legacy-event"></a>레거시 이벤트

일반적으로 자격 증명 모음에 대한 모든 백업 관련 진단 데이터는 ' AzureBackupReport ' 라는 단일 이벤트에 포함 되었습니다. 위에서 설명한 여섯 가지 이벤트는 기본적으로 AzureBackupReport에 포함 된 모든 데이터를 분해 하는 것입니다. 

현재 사용자가이 이벤트에 대한 기존 사용자 지정 쿼리를가지고 있는 경우 (예: 사용자 지정 로그 경고, 사용자 지정 시각화 등) 이전 버전과의 호환성을 위해 AzureBackupReport 이벤트를 계속 지원 합니다. 그러나 로그 쿼리에서의 모든 새 진단 설정에 대해 새 이벤트를 선택 하는 것이 좋습니다. 이렇게 하면 데이터를 로그 쿼리에서 더 쉽게 사용할 수 있고 스키마와 구조를 보다 효율적으로 검색할 수 있으므로 두 수집 모두에서 성능이 향상 됩니다. 대기 시간과 쿼리 시간입니다. Azure 진단 모드 사용에 대한 지원은 결국 단계적으로 진행 되므로 새 이벤트를 선택 하면 나중에 복잡 한 마이그레이션을 피할 수 있습니다.

새 테이블의 데이터를 사용 하기 위해 사용자 지정 쿼리를 모두 마이그레이션할 때까지 AzureBackupReport 및 6 개의 새 이벤트에 대한 별도 진단 설정을 만들도록 선택할 수 있습니다. 아래 그림에서는 두 개의 진단 설정이 있는 자격 증명 모음의 예를 보여 줍니다. **Setting1** 라는 첫 번째 설정은 azurediagnostics 모드의 LA 작업 영역에 AzureBackupReport 이벤트 데이터를 보냅니다. **Setting2** 라는 두 번째 설정은 리소스 특정 모드의 LA 작업 영역에 6 개의 새 Azure Backup 이벤트 데이터를 보냅니다.

![두 가지 설정](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport 이벤트는 Azure 진단 모드 **에서만** 지원 됩니다. **리소스 특정 모드에서이 이벤트에 대한 데이터를 보내려고 하면 LA 작업 영역으로 데이터가 전달 되지 않습니다.**

> [!NOTE]
> Azure 진단/리소스에 대한 토글은 사용자가 **Log Analytics 보내기를 확인 하**는 경우에만 표시 됩니다. 저장소 계정 또는 이벤트 허브로 데이터를 전송 하려면 사용자는 필요한 대상을 선택 하 고 추가 입력 없이 원하는 이벤트를 선택 하면 됩니다. 앞으로는 레거시 이벤트 AzureBackupReport를 선택 하지 않는 것이 좋습니다.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Azure Site Recovery 이벤트를 Log Analytics (LA)로 보내는 사용자

Azure Backup 및 Azure Site Recovery 이벤트는 동일한 Recovery Services 자격 증명 모음에서 전송 됩니다. Azure Site Recovery 현재 리소스 관련 테이블에 등록 되지 않으므로 LA에 게 Azure Site Recovery 이벤트를 보내려고 하는 사용자는 Azure 진단 모드만 사용 하도록 지시 **됩니다 (아래** 이미지 참조). **Azure Site Recovery 이벤트에 대한 리소스 특정 모드를 선택 하면 필요한 데이터가 LA 작업 영역으로 전송 되지 않습니다**.

![Site Recovery 이벤트](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

위의 미묘한 차이를 요약 하면 다음과 같습니다.

* 이미 Azure 진단를 사용 하 여 LA 진단이 설정 되어 있고 그 위에 사용자 지정 쿼리를 작성 한 경우 새 이벤트의 데이터를 사용 하도록 쿼리를 마이그레이션할 때까지 해당 설정을 **그대로**유지 합니다.
* 권장 사항에 따라 새 테이블에 등록 하려는 경우에는 **새** 진단 설정을 만들고 **리소스 특정** 을 선택한 후 위에 지정 된 대로 6 개의 새 이벤트를 선택 합니다.
* 현재 LA에 Azure Site Recovery 이벤트를 보내는 경우 이러한 이벤트에 대해 리소스 특정 모드를 선택 **하지 마세요** . 그렇지 않으면 이러한 이벤트의 데이터가 LA 작업 영역으로 이동 하지 않습니다. 대신, **추가 진단 설정을**만들고 **Azure 진단**를 선택한 다음 관련 Azure Site Recovery 이벤트를 선택 합니다.

아래 이미지는 자격 증명 모음에 대한 세 가지 진단 설정이 있는 사용자의 예를 보여 줍니다. **Setting1** 라는 첫 번째 설정은 AzureBackupReport 이벤트에서 azurediagnostics 모드로 LA 작업 영역으로 데이터를 보냅니다. **Setting2** 라는 두 번째 설정에서는 6 개의 새 Azure Backup 이벤트에서 리소스 특정 모드의 LA 작업 영역으로 데이터를 보냅니다. **Setting3**라는 세 번째 설정은 Azure 진단 모드에서 LA 작업 영역에 Azure Site Recovery 이벤트의 데이터를 보냅니다.

![3 가지 설정](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>다음 단계

[진단 이벤트에 대한 Log Analytics 데이터 모델 알아보기](https://aka.ms/diagnosticsdatamodel)

---
title: Azure Backup 보호 된 워크 로드 모니터링
description: 이 문서에서는 Azure Portal를 사용 하 여 Azure Backup 작업에 대한 모니터링 및 알림 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ea5102a95a9bef17f25219e00dec4654bf7f06d6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172872"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup 워크 로드 모니터링

Azure Backup는 백업 요구 사항 및 인프라 토폴로지 (온-프레미스 vs Azure)를 기반으로 여러 백업 솔루션을 제공 합니다. 모든 백업 사용자 또는 관리자는 모든 솔루션에서 발생 하는 상황을 확인 하 고 중요 한 시나리오에서 알릴 예정입니다. 이 문서에서는 Azure Backup 서비스에서 제공 하는 모니터링 및 알림 기능에 대해 자세히 설명 합니다.

## <a name="backup-jobs-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 작업

Azure Backup은 Azure Backup으로 보호 되는 워크 로드에 대한 기본 제공 모니터링 및 경고 기능을 제공 합니다. Recovery Services 자격 증명 모음 설정에서 **모니터링** 섹션은 작성 된 작업 및 경고를 제공 합니다.

![RS 자격 증명 모음 내장 모니터링](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

작업은 백업 구성, 백업, 복원, 삭제 등의 작업을 수행 하는 경우에 생성 됩니다.

다음 Azure Backup 솔루션의 작업이 여기에 표시 됩니다.

- Azure VM 백업
- Azure 파일 백업
- SQL과 같은 Azure 워크 로드 백업
- Azure Backup 에이전트(MAB)

System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup 서버 (MABS)의 작업은 표시 되지 않습니다.

> [!NOTE]
> Azure Vm 내 SQL 백업 같은 azure 워크 로드에는 상당한 수의 백업 작업이 있습니다. 예를 들어 로그 백업은 15 분 마다 실행할 수 있습니다. 따라서 이러한 DB 워크 로드의 경우 사용자가 트리거한 작업만 표시 됩니다. 예약 된 백업 작업은 표시 되지 않습니다.

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음의 백업 경고

경고는 사용자가 관련 작업을 수행할 수 있도록 사용자에 게 알림을 제공 하는 경우 주로 발생 합니다. **백업 경고** 섹션에는 Azure Backup 서비스에 의해 생성 된 경고가 표시 됩니다. 이러한 경고는 서비스에서 정의 되며 사용자가 경고를 만들 수 없습니다.

### <a name="alert-scenarios"></a>경고 시나리오

다음 시나리오는 했어야 시나리오로 서비스에 의해 정의 됩니다.

- 백업/복원 실패
- Azure Backup 에이전트(MAB)에 대한 경고와 함께 백업 성공
- 데이터 삭제로 보호 중지/데이터 삭제로 보호 중지

### <a name="exceptions-when-an-alert-is-not-raised"></a>경고가 발생 하지 않는 경우의 예외

오류가 발생 하더라도 경고가 발생 하지 않는 경우 다음과 같은 몇 가지 예외가 있습니다.

- 사용자가 실행 중인 작업을 명시적으로 취소 함
- 다른 백업 작업이 진행 중 이므로 작업이 실패 합니다 (이전 작업이 완료 될 때까지 기다려야 함).
- 백업 된 Azure VM이 더 이상 존재 하지 않으므로 VM 백업 작업이 실패 합니다.

위의 예외는 이러한 작업 (주로 사용자 트리거됨)의 결과가 portal/PS/CLI 클라이언트에 즉시 표시 되는 것을 이해 하는 데 사용 됩니다. 따라서 사용자는 즉시 인식 되며 알림이 필요 하지 않습니다.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>다음 Azure Backup 솔루션의 경고가 여기에 표시 됩니다.

- Azure VM 백업
- Azure 파일 백업
- SQL 같은 Azure 워크로드 백업
- Azure Backup 에이전트(MAB)

> [!NOTE]
> System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup 서버 (MABS)의 경고는 여기에 표시 되지 않습니다.

### <a name="alert-types"></a>경고 유형

경고 심각도에 따라 다음과 같은 세 가지 유형으로 경고를 정의할 수 있습니다.

- **중요**: 원칙적으로 모든 백업 또는 복구 오류 (예약 또는 사용자 트리거)가 경고를 생성 하 고 중요 한 경고로 표시 되며 백업 삭제와 같은 작업을 수행 합니다.
- **경고**: 백업 작업이 성공 하지만 경고가 거의 없으면 경고 경고로 표시 됩니다.
- **정보**: 현재는 Azure Backup 서비스에서 어떠한 정보 경고도 생성 하지 않습니다.

## <a name="notification-for-backup-alerts"></a>백업 경고에 대한 알림

> [!NOTE]
> 알림 구성은 Azure Portal을 통해서만 수행할 수 있습니다. PS/CLI/REST API/Azure Resource Manager 템플릿 지원은 지원 되지 않습니다.

경고가 발생 하면 사용자에 게 알림이 제공 됩니다. Azure Backup는 전자 메일을 통해 기본 제공 알림 메커니즘을 제공 합니다. 경고가 생성 될 때 알림을 받을 개별 메일 주소 또는 메일 그룹을 지정할 수 있습니다. 각 경고에 대한 알림을 받을지 또는 매시간 다이제스트로 그룹화 한 다음 알림을 받을 지를 선택할 수도 있습니다.

![RS Vault 내장 전자 메일 알림](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> SQL 백업에 대한 경고는 통합 되 고 전자 메일은 처음 발생 한 경우에만 전송 됩니다. 그러나 사용자가 경고를 비활성화 하는 경우 다음에 발생 하면 다른 전자 메일이 트리거됩니다.

알림이 구성 되 면 환영 또는 소개 전자 메일을 받게 됩니다. 이렇게 하면 경고가 발생할 때 Azure Backup이 주소로 전자 메일을 보낼 수 있습니다.<br>

빈도가 매시간 다이제스트로 설정 되 고 경고가 발생 하 고 한 시간 내에 해결 된 경우 예정 된 매시간 다이제스트의 일부가 되지 않습니다.

> [!NOTE]
>
> - **데이터 삭제로 보호 중지** 와 같은 파괴적인 작업을 수행 하면 경고가 발생 하 고 복구 서비스 자격 증명 모음에 대해 알림이 구성 되지 않은 경우에도 전자 메일이 구독 소유자, 관리자 및 공동 관리자에 게 전송 됩니다.
> - 성공한 작업에 대한 알림을 구성 하려면 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)을 사용 합니다.

## <a name="inactivating-alerts"></a>비활성화 경고

활성 경고를 비활성화/해결 하려면 비활성화할 경고에 해당 하는 목록 항목을 클릭 하면 됩니다. 이렇게 하면 경고에 대한 자세한 정보를 표시 하는 화면이 열리고 맨 위에 ' 비활성화 ' 단추가 표시 됩니다. 이 단추를 클릭 하면 경고 상태가 ' 비활성 '으로 변경 됩니다. 해당 경고에 해당 하는 목록 항목을 마우스 오른쪽 단추로 클릭 하 고 ' 비활성화 '를 선택 하 여 경고를 비활성화할 수도 있습니다.

![RS Vault 경고 비활성화](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용 하 여 Azure backup 워크 로드 모니터링](backup-azure-monitoring-use-azuremonitor.md)

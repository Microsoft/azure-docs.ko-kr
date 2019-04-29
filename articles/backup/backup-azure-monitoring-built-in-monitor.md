---
title: 'Azure Backup: Azure 백업 모니터링 워크 로드 보호'
description: Azure portal을 사용 하 여 Azure Backup 워크 로드를 모니터링 합니다.
services: backup
author: pvrk
manager: shivamg
keywords: Azure Backup; 경고;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 8d3e3257f16fe4e0f846c2268bfefc2771387de6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809036"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup 워크 로드를 모니터링합니다.

Azure Backup은 백업 요구 사항 및 인프라 토폴로지 (온-프레미스 및 Azure)를 기반으로 하는 여러 백업 솔루션을 제공 합니다. 모든 백업 사용자나 관리자 모든 솔루션에서 진행 상황을 표시 및 중요 한 시나리오에서 알림을 받을 예상 합니다. 이 문서에서는 Azure Backup 서비스에서 제공 하는 모니터링 및 알림 기능을 자세히 설명 합니다.

## <a name="backup-jobs-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 백업 작업

Azure Backup은 기본 제공 모니터링 및 경고는 Azure Backup으로 보호 되는 워크 로드에 대 한 기능을 제공 합니다. Recovery services에서 자격 증명 모음 설정 합니다 **모니터링** 섹션에서는 기본 제공 작업 및 경고를 제공 합니다.

![RS 기본 제공 모니터링 하는 자격 증명 모음](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

작업에는 백업, 백업, 복원, 백업 삭제 및 등을 구성 하는 등 작업을 수행할 때 생성 됩니다.

다음 Azure Backup 솔루션에서 작업은 다음과 같습니다.

  - Azure VM 백업
  - Azure 파일 백업
  - SQL 등 azure 워크 로드 백업
  - Azure Backup 에이전트 (MAB)

작업에서 System Center Data Protection Manager (SC-DPM)에서 Microsoft Azure Backup Server (MABS) 표시 되지 않습니다.

> [!NOTE]
> Azure Vm 내에서 SQL 백업 등 azure 워크 로드 백업 작업을 상당히 많은 경우 예를 들어 15 분 마다 로그 백업을 실행할 수 있습니다. 따라서 이러한 DB 워크 로드의 경우 트리거는 사용자만 작업 표시 됩니다. 예약 된 백업 작업이 표시 되지 않습니다.

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 백업 경고

경고는 관련 취할 수 있도록 사용자에 게 알려줍니다 있는 시나리오에 주로입니다. 합니다 **Backup 경고** 섹션에서는 Azure Backup 서비스에 의해 생성 된 경고를 보여 줍니다. 이러한 경고 서비스에서 정의 되 고 사용자에는 사용자 지정 수 없습니다. 모든 경고를 생성 합니다.

### <a name="alert-scenarios"></a>경고 시나리오
다음 시나리오는 경고 시나리오도 서비스에 의해 정의 됩니다.

  - 백업/복원 실패
  - 백업 성공, 경고 있음
  - 데이터 삭제를 사용 하 여 데이터/중지 보호를 유지 하는 사용 하 여 보호 중지

### <a name="exceptions-when-an-alert-is-not-raised"></a>예외는 경고가 발생 하는 경우
오류 발생 시 경고가 발생 하지 않습니다 하는 경우 몇 가지 예외 사항이은:

  - 사용자는 명시적으로 실행 중인 작업 취소
  - 다른 백업 작업이 진행 중 (이전 작업이 완료 될 때까지 기다리는 해야 하므로 여기에서 작동 하려면 아무) 이므로 작업 실패
  - 백업 Azure VM이 더 이상 존재 하기 때문에 VM 백업 작업 실패

위와 같이 예외는 표시 되는 이러한 작업 (주로 사용자 트리거)의 결과 즉시 포털/PS/CLI 클라이언트에서 이해에서 설계 되었습니다. 따라서 사용자는 즉시 인식 하 고 알림이 필요 하지 않습니다.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>다음 Azure Backup 솔루션에서 경고는 다음과 같습니다.

  - Azure VM 백업
  - Azure 파일 백업
  - SQL 등 azure 워크 로드 백업
  - Azure Backup 에이전트 (MAB)

> [!NOTE]
> 경고에서 System Center Data Protection Manager (SC-DPM)에서 Microsoft Azure Backup Server (MABS) 여기 표시 되지 않습니다.

### <a name="alert-types"></a>경고 유형
경고 심각도 따라 경고를 세 가지 유형에서 정의할 수 있습니다.

  - **높음**: 원칙, 백업 또는 복구 오류 (예약 된 또는 트리거한 사용자) 경고의 생성을 초래에 백업에 중요 한 알림이 및 삭제와 같은 안전 하지 않은 작업으로 표시 됩니다.
  - **경고**: 백업 작업이 성공 하지만 몇 가지 경고를 사용 하 여 경고로 나열 됩니다.
  - **정보 제공 용 이므로**: 현재 Azure Backup 서비스에서 정보 경고가 생성 됩니다.

## <a name="notification-for-backup-alerts"></a>백업 경고에 대 한 알림

> [!NOTE]
> 알림 구성만 Azure 포털을 통해 수행할 수 있습니다. PS/CLI/REST API/Azure Resource Manager 템플릿을 지원 되지 않습니다.

경고 발생 되 면 사용자 알림이 표시 됩니다. Azure Backup은 전자 메일을 통해 기본 제공된 알림 메커니즘을 제공 합니다. 개별 전자 메일 주소 또는 메일 그룹 경고가 생성 될 때 알림을 받도록 지정할 수 있습니다. 각 경고에 대 한 알림 받기 또는을 그룹화 하는 시간별 요약 및 다음 알림을 받을 여부를 선택할 수도 있습니다.

![RS Vault 기본 제공된 전자 메일 알림](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

알림 구성 되 면 시작 또는 기본 전자 메일을 받게 됩니다. Azure Backup를 보낼 수 있는지 전자 메일이 주소 이러한 경고가 발생 하는 경우 확인 합니다.<br>

빈도 매시간 다이제스트로 설정 된 경고가 발생 되 고 1 시간 이내에 확인을 예정 된 시간별 요약 포함 되지 않습니다.

> [!NOTE]
> 
> * 와 같은 작업을 하는 경우 **데이터 삭제를 사용 하 여 보호를 중지** 는 수행 하 고, 경고가 발생 하 고 복구 서비스에 대 한 알림을 구성 되지 않은 경우에 구독 소유자, 관리자 및 공동 관리자 전자 메일이 전송 됩니다 자격 증명 모음입니다.
> * 성공한 작업 사용에 대 한 알림을 구성할 [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace)합니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용 하 여 Azure 백업 작업 모니터링](backup-azure-monitoring-use-azuremonitor.md)

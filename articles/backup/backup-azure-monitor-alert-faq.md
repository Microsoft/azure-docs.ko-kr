---
title: 경고 및 보고서 모니터링 FAQ
description: 이 문서에서는 Azure Backup 모니터링 경고 및 Azure Backup 보고서에 대한 일반적인 질문에 대한 대답을 검색 합니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989572"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup 모니터링 경고-FAQ

이 문서에서는 Azure Backup 모니터링 및 보고에 대한 일반적인 질문에 답변 합니다.

## <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>보고 데이터가 LA (Log Analytics) 작업 영역으로 이동이 시작 되었는지 확인 어떻게 할까요??

구성 된 LA 작업 영역으로 이동 하 여 **로그** 메뉴 항목으로 이동 하 고 CoreAzureBackup 쿼리를 실행 합니다. 1을 사용 합니다. 반환 되는 레코드가 표시 되는 경우 데이터가 작업 영역으로 이동 하기 시작 했음을 의미 합니다. 초기 데이터 푸시는 최대 24 시간이 걸릴 수 있습니다.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>LA 작업 영역에 대한 데이터 푸시의 빈도는 어떻게 되나요?

자격 증명 모음의 진단 데이터는 일정 시간 동안 Log Analytics 작업 영역에 펌프 됩니다. 모든 이벤트는 Recovery Services 자격 증명 모음에서 푸시된 후 20 분에서 30 분 Log Analytics 작업 영역에 도착 합니다. 지연에 대한 자세한 내용은 다음과 같습니다.

* 모든 솔루션에서 백업 서비스의 기본 제공 경고는 생성 되는 즉시 푸시됩니다. 따라서 일반적으로 20 분에서 30 분 후에 Log Analytics 작업 영역에 나타납니다.
* 모든 솔루션에서 주문형 백업 작업 및 복원 작업은 완료 되는 즉시 푸시됩니다.
* SQL 백업을 제외한 모든 솔루션에 대해 예약 된 백업 작업은 완료 되는 즉시 푸시됩니다.
* SQL 백업의 경우 로그 백업이 15 분 마다 발생할 수 있으므로 로그를 포함 하 여 완료 된 모든 예약 된 백업 작업에 대한 정보는 6 시간 마다 일괄 처리 되 고 푸시됩니다.
* 모든 솔루션에서 백업 항목, 정책, 복구 지점, 저장소 등의 기타 정보는 하루에 한 번 이상 푸시됩니다.
* 백업 구성 변경 (예: 정책 변경 또는 정책 편집)은 관련 된 모든 백업 정보의 푸시를 트리거합니다.

### <a name="how-long-can-i-retain-reporting-data"></a>보고 데이터는 얼마나 오래 보관할 수 있나요?

LA 작업 영역을 만든 후에는 데이터를 최대 2 년 동안 보존 하도록 선택할 수 있습니다. 기본적으로 LA 작업 영역에는 31 일간의 데이터가 보존 됩니다.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>LA 작업 영역을 구성한 후 보고서의 모든 데이터를 볼 수 있나요?

 진단 설정을 구성한 후 생성 된 모든 데이터는 LA 작업 영역으로 푸시되 고 보고서에서 사용할 수 있습니다. 진행 중인 작업은 보고를 위해 푸시되지 않으며, 작업이 완료 되거나 실패 하면 보고서로 전송 됩니다.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>여러 자격 증명 모음 및 구독의 보고서를 볼 수 있나요?

예, 자격 증명 모음 및 구독 및 지역에서 보고서를 볼 수 있습니다. 데이터는 단일 LA 작업 영역 또는 LA 작업 영역 그룹에 있을 수 있습니다.

### <a name="can-i-view-reports-across-tenants"></a>테넌트 간에 보고서를 볼 수 있나요?

고객의 구독 또는 LA 작업 영역에 대한 위임 된 액세스 권한이 있는 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) 사용자 인 경우 백업 보고서를 사용 하 여 모든 테넌트의 데이터를 볼 수 있습니다.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 백업 에이전트 작업 상태가 포털에 반영되려면 얼마나 오래 걸리나요?

Azure Portal에서 Azure 백업 에이전트 작업 상태를 반영하는 데 최대 15분이 걸릴 수 있습니다.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>백업 작업이 실패하는 경우 알림이 발생하는 데 얼마나 오래 걸리나요?

알림은 Azure 백업 실패 후 20분 이내에 발생합니다.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>알림이 구성된 경우 이메일이 전송되지 않는 경우가 있나요?

예. 알림이 전송되지 않는 경우는 다음과 같습니다.

* 알림이 매시간으로 구성되고 알림이 발생하고 한 시간 이내에 해결되는 경우
* 작업이 취소되는 경우
* 진행 중인 원래 백업 작업으로 인해 두 번째 백업 작업이 실패하는 경우

## <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure 백업 에이전트 작업 상태가 포털에 반영되려면 얼마나 오래 걸리나요?

Azure Portal에서 Azure 백업 에이전트 작업 상태를 반영하는 데 최대 15분이 걸릴 수 있습니다.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>백업 작업이 실패하는 경우 알림이 발생하는 데 얼마나 오래 걸리나요?

알림은 Azure 백업 실패 후 20분 이내에 발생합니다.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>알림이 구성된 경우 이메일이 전송되지 않는 경우가 있나요?

예. 다음 상황에서는 알림이 전송 되지 않습니다.

* 알림이 매시간으로 구성되고 알림이 발생하고 한 시간 이내에 해결되는 경우
* 작업이 취소되는 경우
* 진행 중인 원래 백업 작업으로 인해 두 번째 백업 작업이 실패하는 경우

## <a name="next-steps"></a>다음 단계

다른 FAQ를 읽어보세요.

* Azure VM에 대한 [일반 질문](backup-azure-vm-backup-faq.md)
* Azure Backup 에이전트에 대한 [일반 질문](backup-azure-file-folder-backup-faq.md)

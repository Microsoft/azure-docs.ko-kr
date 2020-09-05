---
title: 경고 및 보고서 모니터링 FAQ
description: 이 문서에서는 Azure Backup 모니터링 경고 및 Azure Backup 보고서에 대 한 일반적인 질문에 대 한 대답을 검색 합니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: cf6929b9b926a6e6469f3fa789a19e60d5883d21
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181496"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Azure Backup 모니터링 경고-FAQ

이 문서에서는 Azure Backup 모니터링 및 보고에 대 한 일반적인 질문에 답변 합니다.

## <a name="configure-azure-backup-reports"></a>Azure Backup 보고서 구성

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>보고 데이터가 LA (Log Analytics) 작업 영역으로 이동이 시작 되었는지 확인 어떻게 할까요??

구성한 LA 작업 영역으로 이동 합니다. **로그** 메뉴 항목으로 이동 하 여 쿼리를 실행 `CoreAzureBackup | take 1` 합니다. 반환 되는 레코드가 표시 되는 경우 데이터가 작업 영역으로 이동 하기 시작 했음을 의미 합니다. 초기 데이터 푸시는 최대 24 시간이 걸릴 수 있습니다.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>LA 작업 영역에 대 한 데이터 푸시의 빈도는 어떻게 되나요?

자격 증명 모음에서 Log Analytics 작업 영역으로 전달되는 진단 데이터가 일부 지연되었습니다. 모든 이벤트는 Recovery Services 자격 증명 모음에서 푸시되고 20~30분 후에 Log Analytics 작업 영역에 도착합니다. 지연에 대한 세부 정보는 다음과 같습니다.

* 모든 솔루션에서 백업 서비스의 기본 제공 경고는 생성되는 즉시 푸시됩니다. 따라서 일반적으로 20~30분 후에는 Log Analytics 작업 영역에 표시됩니다.
* 모든 솔루션에서 주문형 백업 작업 및 복원 작업은 완료되는 즉시 푸시됩니다.
* SQL 백업을 제외한 모든 솔루션의 경우, 완료되는 즉시 예약된 백업 작업이 푸시됩니다.
* SQL 백업의 경우, 로그 백업이 15분 간격으로 발생할 수 있으므로, 로그를 포함하여 모든 완료된 예약 백업 작업에 대한 정보가 6시간 간격으로 일괄 처리되고 푸시됩니다.
* 모든 솔루션에서 백업 항목, 정책, 복구 지점, 스토리지 등과 같은 다른 정보는 최소한 하루 한 번 이상 푸시됩니다.
* 백업 구성에 변경(예: 정책 변경 또는 정책 편집)이 있으면 모든 관련 백업 정보의 푸시가 트리거됩니다.

### <a name="how-long-can-i-retain-reporting-data"></a>보고 데이터는 얼마나 오래 보관할 수 있나요?

LA 작업 영역을 만든 후에는 데이터를 최대 2 년 동안 보존 하도록 선택할 수 있습니다. 기본적으로 LA 작업 영역에는 31 일간의 데이터가 보존 됩니다.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>LA 작업 영역을 구성한 후 보고서의 모든 데이터를 볼 수 있나요?

 진단 설정을 구성한 후 생성 된 모든 데이터는 LA 작업 영역으로 푸시되 고 보고서에서 사용할 수 있습니다. 진행 중인 작업은 보고를 위해 푸시되지 않으며, 완료되거나 실패하면 보고서로 전송됩니다.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>여러 자격 증명 모음 및 구독의 보고서를 볼 수 있나요?

예, 자격 증명 모음 및 구독 및 지역에서 보고서를 볼 수 있습니다. 데이터는 단일 LA 작업 영역 또는 LA 작업 영역 그룹에 있을 수 있습니다.

### <a name="can-i-view-reports-across-tenants"></a>테 넌 트 간에 보고서를 볼 수 있나요?

고객의 구독 또는 LA 작업 영역에 대 한 위임 된 액세스 권한이 있는 [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) 사용자 인 경우 백업 보고서를 사용 하 여 모든 테 넌 트의 데이터를 볼 수 있습니다.

## <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Azure Backup 에이전트 작업 상태가 포털에 반영 될 때까지 걸리는 시간

Azure Portal Azure Backup 에이전트 작업 상태를 반영 하는 데 최대 15 분이 걸릴 수 있습니다.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>백업 작업이 실패하는 경우 알림이 발생하는 데 얼마나 오래 걸리나요?

경고는 Azure Backup 실패 후 20 분 이내에 발생 합니다.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>알림이 구성된 경우 이메일이 전송되지 않는 경우가 있나요?

예. 다음 상황에서는 알림이 전송 되지 않습니다.

* 알림이 매시간으로 구성되고 알림이 발생하고 한 시간 이내에 해결되는 경우
* 작업이 취소되는 경우
* 진행 중인 원래 백업 작업으로 인해 두 번째 백업 작업이 실패하는 경우

## <a name="next-steps"></a>다음 단계

다른 FAQ를 읽어보세요.

* Azure VM에 대한 [일반 질문](backup-azure-vm-backup-faq.md)
* Azure Backup 에이전트에 대한 [일반 질문](backup-azure-file-folder-backup-faq.md)

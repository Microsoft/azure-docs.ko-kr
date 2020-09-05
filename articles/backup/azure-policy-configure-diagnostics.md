---
title: 대규모로 자격 증명 모음 진단 설정 구성
description: Azure Policy를 사용 하 여 지정 된 범위의 모든 자격 증명 모음에 대 한 Log Analytics 진단 설정 구성
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 751ce0233fb232ef5d276c88a8c2429a2975b2f9
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179983"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>대규모로 자격 증명 모음 진단 설정 구성

Azure Backup에서 제공 하는 보고 솔루션은 LA (Log Analytics)를 활용 합니다. 지정 된 자격 증명 모음의 데이터를 LA로 보내려면 해당 자격 증명 모음에 대 한 [진단 설정을](./backup-azure-diagnostic-events.md) 만들어야 합니다.

일반적으로 자격 증명 모음 마다 진단 설정을 수동으로 추가 하는 작업은 복잡할 수 있습니다. 또한 만들어진 모든 새 자격 증명 모음에 대해이 자격 증명 모음에 대 한 보고서를 보려면 진단 설정을 사용 하도록 설정 해야 합니다.

(LA를 대상으로 하는) 진단 설정 만들기를 간소화 하기 위해 Azure Backup 기본 제공 [Azure Policy](../governance/policy/index.yml)를 제공 합니다. 이 정책은 지정 된 구독 또는 리소스 그룹의 모든 자격 증명 모음에 LA 진단 설정을 추가 합니다. 다음 섹션에서는이 정책을 사용 하는 방법에 대 한 지침을 제공 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 정책은 특정 구독의 모든 Recovery Services 자격 증명 모음 (또는 구독 내의 리소스 그룹)에 한 번에 적용할 수 있습니다. 정책을 할당 하는 사용자에 게 정책이 할당 된 구독에 대 한 **소유자** 액세스 권한이 있어야 합니다.

* 사용자가 지정 하는 LA 작업 영역 (진단 데이터가 전송 되는)은 정책이 할당 된 자격 증명 모음과 다른 구독에 있을 수 있습니다. 사용자는 지정 된 LA 작업 영역이 존재 하는 구독에 대 한 **독자**, **참가자** 또는 **소유자** 의 액세스 권한이 있어야 합니다.

* 관리 그룹 범위는 현재 지원 되지 않습니다.

* 기본 제공 정책은 현재 국가별 클라우드에서 사용할 수 없습니다.

## <a name="assigning-the-built-in-policy-to-a-scope"></a>범위에 기본 제공 정책 할당

필요한 범위에서 자격 증명 모음에 대 한 정책을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인 하 여 **정책** 대시보드로 이동 합니다.
2. 왼쪽 메뉴에서 **정의** 를 선택 하 여 Azure 리소스의 모든 기본 제공 정책 목록을 가져옵니다.
3. **범주 = 모니터링**에 대 한 목록을 필터링 합니다. **[Preview]: Recovery Services 자격 증명 모음에 대 한 진단 설정을 배포 하 여 리소스 관련 범주의 Log Analytics 작업 영역을**찾습니다.

    ![정책 정의 창](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 정책 이름을 선택 합니다. 이 정책에 대 한 세부 정의로 리디렉션됩니다.

    ![세부 정책 정의](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 창 위쪽에서 **할당** 단추를 선택 합니다. 그러면 **정책 할당** 창으로 리디렉션됩니다.

6. **기본**에서 **범위** 필드 옆에 있는 세 개의 점을 선택 합니다. 그러면 적용할 정책의 구독을 선택할 수 있는 올바른 컨텍스트 창이 열립니다. 또한 필요에 따라 리소스 그룹을 선택 하 여 특정 리소스 그룹의 자격 증명 모음에만 정책이 적용 되도록 할 수 있습니다.

    ![정책 할당 기본 사항](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. **매개 변수**에서 다음 정보를 입력 합니다.

    * **프로필 이름** -정책에 의해 생성 된 진단 설정에 할당 되는 이름입니다.
    * **Log Analytics 작업 영역** -진단 설정이 연결 되어야 하는 Log Analytics 작업 영역입니다. 정책 할당 범위에서 모든 자격 증명 모음의 진단 데이터는 지정 된 LA 작업 영역으로 푸시됩니다.

    * **제외 태그 이름 (선택 사항) 및 제외 태그 값 (선택 사항)** -정책 할당에서 특정 태그 이름 및 값을 포함 하는 자격 증명 모음을 제외 하도록 선택할 수 있습니다. 예를 들어 ' isTest ' 태그가 ' 예 ' 값으로 설정 된 자격 증명 모음에 진단 설정을 추가 **하지 않으려면 제외** **태그 이름** 필드에 ' istest '를 입력 하 고 **제외 태그 값** 필드에 ' y e s '를 입력 해야 합니다. 이러한 두 필드 중 하나 (또는 둘 다)를 비워 두면 정책이 포함 하는 태그에 관계 없이 모든 관련 자격 증명 모음에 정책이 적용 됩니다.

    ![정책 할당 매개 변수](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **수정 작업 만들기** -정책을 범위에 할당 하면 해당 범위에서 만든 모든 새 자격 증명 모음이 자동으로 구성 됩니다 (자격 증명 모음이 생성 된 시간부터 30 분 이내). 범위의 기존 자격 증명 모음에 진단 설정을 추가 하려면 정책 할당 시간에 수정 작업을 트리거할 수 있습니다. 수정 작업을 트리거하려면 **수정 작업 만들기**확인란을 선택 합니다.

    ![정책 할당 수정](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. **검토 + 만들기** 탭으로 이동 하 여 **만들기**를 선택 합니다.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>재구성 작업이 자격 증명 모음에 적용 되는 조건은 무엇입니까?

수정 작업은 정책 정의에 따라 비규격 자격 증명 모음에 적용 됩니다. 다음 조건 중 하나를 충족 하는 경우 자격 증명 모음이 비규격 상태입니다.

* 자격 증명 모음에 대 한 진단 설정이 없습니다.
* 자격 증명 모음에 대 한 진단 설정이 있지만이 설정에는 LA를 대상으로 하는 **모든** 리소스 관련 이벤트와 설정/해제에서 선택한 **리소스가** 없습니다.

따라서 사용자가 AzureDiagnostics 모드에서 AzureBackupReport 이벤트를 사용 하는 자격 증명 모음을 사용 하는 경우에도 (백업 보고서에서 지원 됨), 리소스 특정 모드가 진단 설정 [생성에 권장](./backup-azure-diagnostic-events.md#legacy-event)되는 방법 이므로 수정 작업은이 자격 증명 모음에 계속 적용 됩니다.

또한 사용자에 게 6 개의 리소스 관련 이벤트 중 일부만 사용 하도록 설정 된 자격 증명 모음이 있는 경우에는 6 개의 리소스 관련 이벤트를 모두 사용 하도록 설정한 경우에만 백업 보고서가 예상 대로 작동 하므로이 자격 증명 모음에 대 한 수정 작업이 적용 됩니다.

> [!NOTE]
>
> 자격 증명 모음에 **리소스 특정 범주 하위 집합** 을 사용 하는 기존 진단 설정이 있고, 특정 LA 작업 영역에 데이터를 보내도록 구성 된 경우 (예를 들어 ' 작업 영역 x '), 정책 할당에 제공 된 대상 LA 작업 영역이 **같은** ' 작업 영역 x ' 인 경우 재구성 작업이 실패 합니다 (해당 자격 증명 모음에만 해당).
>
>동일한 리소스에 대해 서로 다른 두 진단 설정에서 사용 하도록 설정 된 이벤트가 일부 형식에서 **겹치면** 설정에 대상과 동일한 LA 작업 영역이 있을 수 없기 때문입니다. 관련 자격 증명 모음으로 이동 하 고 대상으로 다른 LA 작업 영역을 사용 하 여 진단 설정을 구성 하 여이 오류를 수동으로 해결 해야 합니다.
>
> 이 경우 기존 설정에서 사용 하도록 설정 된 이벤트와 수정 작업에서 만든 설정에서 사용 하도록 설정 된 이벤트 사이에 겹치는 부분이 없기 때문에,이 경우에는 수정 작업을 수행할 수 **없습니다** .

## <a name="next-steps"></a>다음 단계

* [백업 보고서를 사용 하는 방법 알아보기](./configure-reports.md)
* [Azure Policy에 대 한 자세한 정보](../governance/policy/index.yml)
* [Azure Policy를 사용 하 여 제공 범위에서 모든 Vm에 대 한 백업을 자동으로 사용 하도록 설정 합니다.](./backup-azure-auto-enable-backup.md)

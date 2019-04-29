---
title: 메트릭 경고를 통해 Azure Automation Runbook 모니터링
description: 이 문서에서는 메트릭을 기반으로 Azure Automation Runbook을 모니터링하는 단계를 안내합니다.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7932d057a348957d369ba325044055ac8dfe3428
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119897"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>메트릭 경고로 Runbook 모니터링

이 문서에서는 Runbook의 완료 상태에 따라 경고를 만드는 방법을 설명합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

 https://portal.azure.com 에서 Azure에 로그인

## <a name="create-alert"></a>경고 만들기

경고를 사용하면 모니터링할 조건 및 조건이 충족될 때 수행할 작업을 정의할 수 있습니다.

Azure Portal에서 Automation 계정으로 이동합니다. **모니터링**에서 **경고**를 선택하고 **+ 새 경고 규칙**을 클릭합니다. 대상의 범위는 이미 사용자의 Automation 계정에 정의되어 있습니다.

### <a name="configure-alert-criteria"></a>경고 기준 구성

1. **+ 기준 추가**를 클릭합니다. **신호 유형**에 대한 **메트릭**을 선택하고, 테이블에서 **총 작업 수**를 선택합니다.

2. **신호 논리 구성** 페이지에서는 경고를 트리거하는 논리를 정의합니다. 과거 그래프 아래를 보면 **Runbook 이름** 및 **상태**의 두 가지 차원이 제공됩니다. 차원은 결과 필터링에 사용할 수 있는 메트릭에 대한 서로 다른 속성입니다. **Runbook 이름**에서는 경고할 Runbook을 선택하거나 모든 Runbook에 대해 경고하도록 비워 둡니다. **상태**에서는 모니터링할 상태를 드롭다운에서 선택합니다. 드롭다운에 표시되는 Runbook 이름과 상태 값은 지난 주에 실행된 작업에만 해당합니다.

   드롭다운에 없는 상태 또는 Runbook에 대해 경고하려면 차원 옆에 있는 **\+** 를 클릭합니다. 그러면 최근에 해당 차원에 대해 전송되지 않은 사용자 지정 값을 입력할 수 있는 대화 상자가 열립니다. 속성에 없는 값을 입력하면 경고가 트리거되지 않습니다.

   > [!NOTE]
   > 이름을 적용 하지 않으면 합니다 **RunbookName** 차원, 숨겨진된 시스템 runbook을 포함 하는 상태 조건을 충족 하는 모든 runbook의 경우 알림을 받습니다.

3. **경고 논리** 아래에서 경고의 조건 및 임계값을 정의합니다. 정의한 조건의 미리 보기가 그 아래에 표시됩니다.

4. **다음 기준으로 평가**에서 쿼리의 시간 간격과 쿼리 실행 빈도를 선택합니다. 예를 들어 **기간**으로 **지난 5분**을 선택하고 **빈도**로 **1분마다**를 선택하면 경고는 지난 5분 동안 기준을 충족한 Runbook 수를 찾습니다. 이 쿼리는 1분마다 실행되며, 정의한 경고 조건을 5분이라는 기간 내에 더 이상 찾을 수 없으면 경고에서 자체적으로 확인합니다. 여기까지 마쳤으면 **완료**를 클릭합니다.

   ![경고를 사용할 리소스 선택](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>경고 세부 정보 정의

1. **2. 경고 세부 정보 정의**에서 경고의 표시 이름과 설명을 입력합니다. 경고 조건에 맞는 **심각도**를 설정합니다. 0부터 5까지 다섯 가지 심각도가 있습니다. 경고는 심각도에 관계 없이 동일하게 처리되며, 비즈니스 논리에 맞게 심각도를 일치시킬 수 있습니다.

1. 섹션 맨 아래에는 완료 시 규칙을 사용하도록 설정하는 단추가 있습니다. 기본적으로 생성 시 규칙이 사용됩니다. 아니요를 선택하면 경고를 만들 수 있으며, 경고가 **사용 안 함** 상태로 생성됩니다. Azure Monitor의 **규칙** 페이지에서 경고를 선택하고 준비가 되면 **사용**을 클릭하여 경고를 사용할 수 있습니다.

### <a name="define-the-action-to-take"></a>수행할 작업 정의

1. **3. 작업 그룹 정의**에서 **+ 새 작업 그룹**을 클릭합니다. 작업 그룹은 하나 이상의 경고에서 사용할 수 있는 작업의 그룹입니다. 여기에는 이메일 알림, Runbook, 웹후크 등이 포함되며 이에 국한되지 않습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

1. **작업 그룹 이름** 상자에 표시 이름과 약식 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

1. **작업 유형** 아래의 **작업** 섹션에서 **이메일/SMS/푸시/음성**을 선택합니다.

1. **이메일/SMS/푸시/음성** 페이지에서 이름을 지정합니다. **이메일** 확인란을 선택하고 사용할 유효한 이메일 주소를 입력합니다.

   ![이메일 작업 그룹 구성](./media/automation-alert-activity-log/add-action-group.png)

1. **이메일/SMS/푸시/음성** 페이지에서 **확인**을 클릭하여 페이지를 닫고, **확인**을 클릭하여 **작업 그룹 추가** 페이지를 닫습니다. 이 페이지에서 지정한 이름이 **작업 이름**으로 저장됩니다.

1. 완료되면 **저장**을 클릭합니다. 그러면 Runbook이 특정 상태로 완료되면 경고를 보내는 규칙이 만들어집니다.

> [!NOTE]
> 작업 그룹에 이메일 주소를 추가하면 작업 그룹에 주소가 추가되었음을 알리는 알림 이메일이 전송됩니다.

## <a name="notification"></a>알림

경고 조건이 충족되면 작업 그룹은 정의된 작업을 실행합니다. 이 문서의 예제에서는 이메일이 전송됩니다. 다음 이미지는 경고가 트리거된 후 수신하는 이메일의 예제입니다.

![전자 메일 경고](./media/automation-alert-activity-log/alert-email.png)

메트릭이 더 이상 정의된 임계값을 초과하지 않으면 경고가 비활성화되고 작업 그룹은 정의된 작업을 실행합니다. 이메일 작업 유형을 선택하면 확인되었음을 알리는 확인 이메일이 전송됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 계속 진행하여 Automation 계정에 경고를 통합하는 다른 방법을 알아보세요.

> [!div class="nextstepaction"]
> [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)

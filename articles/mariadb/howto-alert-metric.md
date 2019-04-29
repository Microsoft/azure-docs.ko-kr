---
title: Azure Portal에서 Azure Database for MariaDB에 대한 메트릭 경고 구성
description: 이 문서에서는 Azure Portal에서 Azure Database for MariaDB의 메트릭 경고를 구성 및 액세스하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/07/2019
ms.openlocfilehash: ce9804a4ecad4cd0f2e797038dc10c71b50de9bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041090"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mariadb"></a>Azure Portal을 사용하여 Azure Database for MariaDB의 메트릭에 대한 경고를 설정합니다.

이 문서에서는 Azure Portal을 사용하여 Azure Database for MariaDB를 설정하는 방법을 보여 줍니다. Azure 서비스의 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

이 경고는 특정 메트릭의 값이 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 경고가 트리거됩니다.

트리거되면 다음 작업을 수행하도록 경고를 구성할 수 있습니다.
* 서비스 관리자 및 공동 관리자에게 메일 알림을 보냅니다.
* 사용자가 지정한 추가 메일 주소로 메일을 보냅니다.
* 웹후크 호출

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric"></a>메트릭에 대한 경고 규칙 만들기
1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 Azure Database for MariaDB 서버를 선택합니다.

2. 다음과 같이 사이드바의 **모니터링** 섹션에서 **경고**를 선택합니다.

   ![경고 규칙 선택](./media/howto-alert-metric/2-alert-rules.png)

3. **메트릭 경고 추가**(+ 아이콘)를 선택합니다.

4. 아래와 같이 **규칙 만들기** 페이지가 열립니다. 필수 정보를 입력합니다.

   ![메트릭 경고 양식 추가](./media/howto-alert-metric/4-add-rule-form.png)

5. **조건** 섹션에서 **조건 추가**를 선택합니다.

6. 신호 목록에서 경고를 발생할 메트릭을 선택합니다. 이 예제에서는 "스토리지 비율"을 선택합니다.
   
   ![메트릭 선택](./media/howto-alert-metric/6-configure-signal-logic.png)

7. **조건**(예: "보다 큼"), **임계값**(예: 85%), **시간 집계**, 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간**(예: "지난 30분") 및 **빈도**를 포함하는 경고 논리를 구성합니다.
   
   완료되면 **완료**를 선택합니다.

   ![메트릭 선택](./media/howto-alert-metric/7-set-threshold-time.png)

8. **작업 그룹** 섹션에서 **새로 만들기**를 선택하여 경고 알림을 받을 새 그룹을 만듭니다.

9. 이름, 약식 이름, 구독 및 리소스 그룹을 사용하여 "작업 그룹 추가" 양식을 채웁니다.

10. **이메일/SMS/푸시/음성** 작업 유형을 구성합니다.
    
    "이메일 Azure Resource Manager 역할"을 선택하여 알림을 받을 구독 소유자, 참가자 및 읽기 권한자를 선택합니다.
   
    필요에 따라 경고가 발생했을 때 호출하려면 **Webhook** 필드에 유효한 URI를 입력합니다.

    완료되면 **확인**을 선택합니다.

    ![작업 그룹](./media/howto-alert-metric/10-action-group-type.png)

11. 경고 규칙 이름, 설명 및 심각도를 지정합니다.

    ![작업 그룹](./media/howto-alert-metric/11-name-description-severity.png) 

12. **경고 규칙 만들기**를 선택하여 경고를 만듭니다.

    앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="manage-your-alerts"></a>경고 관리
경고를 만든 후 해당 경고를 선택하고 다음 작업을 수행할 수 있습니다.

* 이 경고와 관련된 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인.
* 경고 규칙을 **편집** 또는 **삭제**.
* 알림 수신을 일시적으로 중지하거나 다시 시작하려면 경로를 **사용 안 함** 또는 **사용**으로 설정.


## <a name="next-steps"></a>다음 단계
* [경고에서의 webhook 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)에 대해 자세히 알아봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.

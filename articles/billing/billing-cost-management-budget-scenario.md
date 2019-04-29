---
title: Azure 청구 및 비용 관리 예산 시나리오 | Microsoft Docs
description: Azure Automation을 사용하여 특정 예산 임계값에 기반하여 VM을 종료하는 방법을 알아 봅니다.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: erikre
ms.openlocfilehash: 4bf76ac0bdd59764815f18a40a3e243d7cf9d920
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617390"
---
# <a name="manage-costs-with-azure-budgets"></a>Azure 예산으로 비용 관리

비용 제어는 클라우드에서 투자 가치를 극대화하는 데 중요한 구성 요소입니다. 비용 표시, 보고 및 비용 기반 오케스트레이션이 지속적인 비즈니스 운영에 중요한 몇 가지 시나리오가 있습니다. [Azure Cost Management API](https://docs.microsoft.com/rest/api/consumption/)는 이러한 각 시나리오를 지원하기 위한 API 집합을 제공합니다. API는 사용량 세부 정보를 제공하므로 세부적인 인스턴스 레벨의 비용을 볼 수 있습니다.

예산은 일반적으로 비용 관리의 일부로 사용되며, Azure에서 예산 범위를 지정할 수 있습니다. 예를 들어 구독, 리소스 그룹 또는 리소스의 컬렉션에 따라 예산 보기의 범위를 좁힐 수 있습니다. 예산 임계값에 도달하면 이메일을 통해 알려주기 위해 예산 API를 사용할 뿐만 아니라, [Azure Monitor 작업 그룹](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)을 사용하여 예산 이벤트의 결과로 오케스트레이션된 작업 집합을 트리거할 수 있습니다.

중요하지 않은 워크로드를 실행하는 고객의 일반적인 예산 시나리오는 예산에 맞게 비용을 관리하고 월간 청구서를 살펴 볼 때 예측 가능한 비용에 이르게 될 때에도 발생할 수 있습니다. 이 시나리오의 경우 Azure 환경에 포함된 일부 리소스를 비용에 기반하여 오케스트레이션해야 합니다. 이 시나리오에서 구독에 월간 $1000의 예산이 설정되었으며, 몇 가지 오케스트레이션을 트리거하도록 알림 임계값도 설정되었습니다. 이 시나리오는 비용 임계값이 최저 80%이며, 이 임계값에 도달하면 리소스 그룹 **Optional**의 모든 VM이 중지됩니다. 그다음으로, 100% 비용 임계값에서 모든 VM 인스턴스가 중지됩니다.
이 시나리오를 구성하려면 이 자습서의 각 섹션에 나와 있는 단계를 수행하여 다음 작업을 완료해야 합니다.

이 자습서에 포함된 이러한 작업으로 다음을 수행할 수 있습니다.

- Webhook를 사용하여 VM을 중지하기 위해 Azure Automation Runbook을 만듭니다.
- 예산 임계값에 기반하여 트리거될 Azure 논리 앱을 만들고 올바른 매개 변수로 Runbook을 호출합니다.
- 예산 임계값이 충족되면 Azure 논리 앱을 트리거하도록 구성될 Azure Monitor 작업 그룹을 만듭니다.
- 원하는 임계값으로 Azure 예산을 만들고 작업 그룹에 연결합니다.

## <a name="create-an-azure-automation-runbook"></a>Azure Automation Runbook 만들기

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)은 리소스 관리 작업의 대부분을 스크립팅하고 이러한 작업을 예약 또는 주문형으로 실행할 수 있는 서비스입니다. 이 시나리오의 일부로, VM을 중지하는 데 사용될 [Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)을 만듭니다. [갤러리](https://docs.microsoft.com/azure/automation/automation-runbook-gallery)에서 [Azure V2 VM 중지](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 그래픽 Runbook을 사용하여 이 시나리오를 빌드하게 됩니다. 이 Runbook을 Azure 계정으로 가져와서 게시하면 예산 임계값 도달 시 VM을 중지할 수 있습니다.

### <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure의 왼쪽 위 모서리에서 **리소스 만들기** 단추를 클릭합니다.
3. **관리 도구** > **자동화**를 선택합니다.
   > [!NOTE]
   > Azure 계정이 없으면 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
4. 계정 정보를 입력합니다. Azure에 대한 인증을 간소화하는 데 필요한 설정을 자동으로 사용하도록 설정하기 위해 **Azure 실행 계정 만들기**에서 **예**를 선택합니다.
5. 완료되면 **만들기**를 클릭하여 Automation 계정 배포를 시작합니다.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Azure V2 VM 중지 Runbook 가져오기

[Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types)을 사용하여 갤러리에서 [Azure V2 VM 중지](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 그래픽 Runbook을 가져옵니다.

1.  Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  **모든 서비스** > **Automation 계정**을 선택하여 Automation 계정을 엽니다. 그런 다음, 사용자의 Automation 계정을 선택합니다.
3.  **프로세스 Automation** 섹션에서 **Runbook 갤러리**를 클릭합니다.
4.  **갤러리 원본**을 **스크립트 센터**로 설정하고 **확인**을 선택합니다.
5.  Azure Portal 내에서 [Azure V2 VM 중지](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 갤러리 항목을 찾아 선택합니다.
6.  **가져오기** 단추를 클릭하여 **가져오기** 블레이드를 표시하고 **확인**을 선택합니다. Runbook 개요 블레이드가 표시됩니다.
7.  Runbook 가져오기 프로세스가 완료되면 **편집**을 선택하여 그래픽 Runbook 편집기와 게시 옵션을 표시합니다.

    ![Azure - 그래픽 Runbook 편집](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  **게시** 단추를 선택하여 Runbook을 게시한 다음, 확인 메시지가 표시되면 **예**를 선택합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 예에서는 Runbook을 방금 만들었으므로 게시된 버전이 없습니다.

    Runbook을 게시하는 방법에 대한 자세한 내용은 [그래픽 Runbook 만들기](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical)를 참조하세요.

## <a name="create-webhooks-for-the-runbook"></a>Runbook에 대한 Webhook 만들기

[Azure V2 VM 중지](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) 그래픽 Runbook을 사용하여 단일 HTTP 요청을 통해 Azure Automation에서 Runbook을 시작하도록 두 개의 Webhook를 만듭니다. 첫 번째 Webhook는 선택적 VM을 중지할 수 있도록 리소스 그룹 이름을 매개 변수로 사용하여 80%의 예산 임계값에서 Runbook을 호출합니다. 그런 다음, 두 번째 Webhook는 매개 변수 없이(100%에서) Runbook을 호출하여 남은 VM 인스턴스가 모두 중지됩니다.

1. [Azure Portal](https://portal.azure.com/)의 **Runbook** 페이지에서 해당 Runbook의 개요 블레이드를 표시하는 **StopAzureV2Vm** Runbook을 클릭합니다.
2. 페이지 맨 위에서 **Webhook**를 클릭하여 **Webhook 추가** 블레이드를 엽니다.
3. **새 Webhook 만들기**를 클릭하여 **새 Webhook 만들기** 블레이드를 엽니다.
4. Webhook의 **이름**을 **Optional**로 설정합니다. **사용** 속성은 **예**이어야 합니다. **만료 시간** 값은 변경할 필요가 없습니다. Webhook 속성에 대한 자세한 내용은 [Webhook 세부 정보](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook)를 참조하십시오.
5. URL 값 옆에서 복사 아이콘을 눌러 Webhook의 URL을 복사합니다.
   > [!IMPORTANT]
   > **Optional**로 명명된 Webhook의 URL을 안전한 곳에 저장합니다. 이 자습서의 뒷부분에서 이 URL을 사용할 것입니다. 보안상의 이유로 Webhook를 만들고 나면 URL을 다시 보거나 검색할 수 없습니다.
6. **확인**을 클릭하여 새 Webhook를 만듭니다.
7. **매개 변수 및 실행 설정 구성**을 클릭하여 Runbook에 대한 매개 변수 값을 봅니다.
   > [!NOTE]
   > runbook에 필수 매개 변수가 있으면 값을 제공 하지 않는 한 webhook를 만들 수 없습니다.
8. **확인**을 클릭하여 Webhook 매개 변수 값을 수락합니다.
9. **만들기** 를 클릭하여 webhook를 만듭니다.
10. 다음으로, 위의 단계에 따라 **Complete**로 명명된 두 번째 Webhook를 만듭니다.
    > [!IMPORTANT]
    > 이 자습서의 뒷부분에서 사용할 수 있도록 두 Webhook URL을 반드시 저장해 두세요. 보안상의 이유로 Webhook를 만들고 나면 URL을 다시 보거나 검색할 수 없습니다.

이제 저장해 둔 URL을 사용하여 각각 사용 가능한 두 개의 구성된 Webhook가 있습니다.

![Webhook - Optional 및 Complete](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

이제 Azure Automation 설정이 완료되었습니다. 간단한 Postman 테스트로 Webhook를 테스트하여 제대로 작동하는지 유효성을 검사할 수 있습니다. 그런 다음에는 오케스트레이션을 위한 논리 앱을 만들어야 합니다.

## <a name="create-an-azure-logic-app-for-orchestration"></a>오케스트레이션을 위해 Azure 논리 앱 만들기

Logic Apps를 사용하면 프로세스를 워크플로로 빌드, 예약 및 자동화하여 기업 또는 조직의 앱, 데이터, 시스템 및 서비스를 통합할 수 있습니다. 이 시나리오에서 만드는 [논리 앱](https://docs.microsoft.com/azure/logic-apps/)은 단순히 사용자가 만든 자동화 Webhook를 호출하는 것 보다 약간 더 많은 것을 하게 됩니다.

지정된 임계값이 충족되면 알림을 트리거하도록 예산을 설정할 수 있습니다. 알림을 받을 수 있는 여러 임계값을 제공할 수 있으며 논리 앱은 사용자가 충족된 임계값에 기반하여 다양한 작업을 수행할 수 있음을 보여 줍니다. 이 예에서는 여러 알림을 받는 시나리오를 설정합니다. 첫 번째 알림은 예산의 80%에 도달했을 때이고 두 번째 알림은 예산의 100%에 도달했을 때입니다. 이 논리 앱은 리소스 그룹의 모든 VM을 종료하는 데 사용됩니다. 먼저, **Optional** 임계값이 80%에 도달한 다음, 두 번째 임계값에 도달하면 구독의 모든 VM은 종료됩니다.

논리 앱을 사용하면 HTTP 트리거에 대한 샘플 스키마를 제공할 수 있지만, **Content-Type** 헤더를 설정해야 합니다. 작업 그룹에 Webhook에 대한 사용자 지정 헤더가 없으므로, 별도의 단계에서 페이로드를 구분 분석해야 합니다. **구문 분석** 작업을 사용하며 샘플 페이로드를 사용하여 제공합니다.

### <a name="create-the-logic-app"></a>논리 앱 만들기

논리 앱은 여러 작업을 수행합니다. 다음 목록은 논리 앱이 수행할 상위 수준의 작업 집합을 제공합니다.
- HTTP 요청을 받으면 인식합니다.
- 전달된 JSON 데이터를 구분 분석하여 임계값에 도달했는지 판별합니다.
- 조건문을 사용하여 임계값 크기가 80%인지, 또는 예산 범위보다 크되 100%을 넘지 않는지 확인합니다.
    - 이 임계값 크기에 도달하면 **Optional**로 명명된 Webhook를 사용하여 HTTP POST를 보냅니다. 이 작업은 "Optional" 그룹의 VM을 종료합니다.
- 조건문을 사용하여 임계값 크기에 도달했거나 예산 값의 100%를 초과했는지 여부를 확인합니다.
    - 이 임계값 크기에 도달하면 **Complete**로 명명된 Webhook를 사용하여 HTTP POST를 보냅니다. 이 작업은 나머지 모든 VM을 종료합니다.

위의 단계를 수행할 논리 앱을 만들려면 다음 단계가 필요합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **통합** > **논리 앱**을 선택합니다.

    ![Azure - 논리 앱 리소스 선택](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  **논리 앱 만들기** 블레이드에서 논리 앱을 만들기 위해 필요한 세부 정보를 제공하고 **대시보드에 고정**을 선택한 다음, **만들기**를 클릭합니다.

    ![Azure - 논리 앱 만들기](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Azure가 논리 앱을 배포하면 **Logic Apps 디자이너**가 열리고 소개 비디오 및 많이 사용되는 트리거가 포함된 블레이드를 보여줍니다.

### <a name="add-a-trigger"></a>트리거 추가

모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 트리거가 발생될 때마다 Logic Apps 엔진은 워크플로를 시작하고 실행하는 논리 앱 인스턴스를 만듭니다. 작업은 트리거 후 발생하는 모든 단계입니다.

1.  **Logic Apps 디자이너** 블레이드의 **템플릿**에서 **비어 있는 논리 앱**을 선택합니다.
2.  **Logic Apps 디자이너** 검색창에 "http 요청"을 입력하여 [트리거](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts)를 추가하고 **Request – HTTP request is received**로 명명된 트리거를 찾아 선택합니다.

    ![Azure - 논리 앱 - Http 트리거](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  **새 단계** > **작업 추가**를 선택합니다.

    ![Azure - 새 단계 - 작업 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  **Logic Apps 디자이너** 검색창에서 "JSON 구문 분석"을 검색하여 **데이터 작업 - JSON 구문 분석** [작업](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts)을 찾아 선택합니다.

    ![Azure - 논리 앱 - JSON 구문 분석 작업 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  JSON 구문 분석 페이로드에 대한 **콘텐츠** 이름으로 “Payload”를 입력하거나, 동적 콘텐츠의 “Body” 태그를 사용합니다.
6.  **JSON 구분 분석** 상자에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

    ![Azure - 논리 앱 - 샘플 JSON 데이터를 사용하여 스키마 생성](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  텍스트 상자에 다음 JSON 샘플 페이로드를 붙여넣습니다. `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    텍스트 상자는 다음과 같이 나타납니다.

    ![Azure - 논리 앱 - 샘플 JSON 페이로드](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  **Done**을 클릭합니다.

### <a name="add-the-first-conditional-action"></a>첫 번째 조건문 작업 추가

조건문을 사용하여 임계값 크기가 80%인지, 또는 예산 범위보다 크되 100%을 넘지 않는지 확인합니다. 이 임계값 크기에 도달하면 **Optional**로 명명된 Webhook를 사용하여 HTTP POST를 보냅니다. 이 작업은 **Optional** 그룹의 VM을 종료합니다.

1.  **새 단계** > **조건 추가**를 선택합니다.

    ![Azure - 논리 앱 - 조건 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  **조건** 상자에서 **값 선택**이 포함된 텍스트 상자를 클릭하여 사용 가능한 값 목록을 표시합니다.

    ![Azure - 논리 앱 - 조건 상자](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  목록 맨 위에서 **식**을 클릭하고 식 편집기에 다음 식을 입력합니다. `float()`

    ![Azure - 논리 앱 - Float 식](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  **동적 콘텐츠**를 클릭하고 괄호() 안에 커서를 놓은 다음, 목록에서 **NotificationThresholdAmount**를 선택하여 전체 식을 채웁니다.

    식은 다음과 같습니다.<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  **확인**을 선택하여 식을 설정합니다.
6.  **조건**의 드롭다운 상자에서 **다음보다 크거나 같음**을 선택합니다.
7.  조건의 **값 선택** 상자에 `.8`을 입력합니다.

    ![Azure - 논리 앱 - 값이 있는 Float 식](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  조건 상자 안에 있는 **추가** > **행 추가**를 클릭하여 조건의 추가 부분을 추가합니다.
9.  **조건** 상자에서 **값 선택**이 포함된 텍스트 상자를 클릭합니다.
10. 목록 맨 위에서 **식**을 클릭하고 식 편집기에 다음 식을 입력합니다. `float()`
11. **동적 콘텐츠**를 클릭하고 괄호() 안에 커서를 놓은 다음, 목록에서 **NotificationThresholdAmount**를 선택하여 전체 식을 채웁니다.
12. **확인**을 선택하여 식을 설정합니다.
13. **조건**의 드롭다운 상자에서 **다음보다 작음**을 선택합니다.
14. 조건의 **값 선택** 상자에 `1`을 입력합니다.

    ![Azure - 논리 앱 - 값이 있는 Float 식](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. **If true** 상자에서 **작업 추가**를 선택합니다. 선택적 VM을 종료하는 HTTP POST 작업을 추가하려고 합니다.

    ![Azure - 논리 앱 - 작업 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. **HTTP**를 입력하여 HTTP 작업을 검색하고 **HTTP – HTTP** 작업을 선택합니다.

    ![Azure - 논리 앱 - HTTP 작업 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. **메서드** 값으로 **Post**를 선택합니다.
18. 이 자습서 초반에 만든 **Optional**로 명명된 Webhook의 URL을 **Uri** 값으로 입력합니다.

    ![Azure - 논리 앱 - HTTP 작업 URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. **If true** 상자에서 **작업 추가**를 선택합니다. 받는 사람에게 선택적 VM이 종료되었음을 알리는 이메일을 보낼 이메일 작업을 추가합니다.
20. "이메일 보내기"를 검색하고 사용하는 이메일 서비스에 따라 *이메일 보내기* 작업을 선택합니다.

    ![Azure - 논리 앱 - 이메일 보내기 작업](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Microsoft 개인 계정인 경우 **Outlook.com**을 선택합니다. Azure 회사 또는 학교 계정인 경우 **Office 365 Outlook**을 선택합니다. 연결이 없는 경우 이메일 계정에 로그인하라는 메시지가 표시됩니다. Logic Apps는 전자 메일 계정에 대한 연결을 만듭니다.

    전자 메일 정보에 액세스하도록 논리 앱을 허용해야 합니다.

    ![Azure - 논리 앱 - 액세스 알림](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. 받는 사람에게 선택적 VM이 종료되었음을 알리는 전자 메일에 대해 **받는 사람**, **제목** 및 **본문** 텍스트를 추가합니다. **BudgetName** 및 **NotificationThresholdAmount** 동적 콘텐츠를 사용하여 제목과 본문 필드를 채웁니다.

    ![Azure - 논리 앱 - 전자 메일 세부 정보](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>두 번째 조건문 작업 추가

조건문을 사용하여 임계값 크기에 도달했거나 예산 값의 100%를 초과했는지 여부를 확인합니다. 이 임계값 크기에 도달하면 **Complete**로 명명된 Webhook를 사용하여 HTTP POST를 보냅니다. 이 작업은 나머지 모든 VM을 종료합니다.

1.  **새 단계** > **조건 추가**를 선택합니다.

    ![Azure - 논리 앱 - 추가 작업](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  **조건** 상자에서 **값 선택**이 포함된 텍스트 상자를 클릭하여 사용 가능한 값 목록을 표시합니다.
3.  목록 맨 위에서 **식**을 클릭하고 식 편집기에 다음 식을 입력합니다. `float()`
4.  **동적 콘텐츠**를 클릭하고 괄호() 안에 커서를 놓은 다음, 목록에서 **NotificationThresholdAmount**를 선택하여 전체 식을 채웁니다.

    식은 다음과 같습니다.<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  **확인**을 선택하여 식을 설정합니다.
6.  **조건**의 드롭다운 상자에서 **다음보다 크거나 같음**을 선택합니다.
7.  조건의 **값 선택** 상자에 `1`을 입력합니다.

    ![Azure - 논리 앱 - 조건 값 설정](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  **If true** 상자에서 **작업 추가**를 선택합니다. 나머지 모든 VM을 종료하는 HTTP POST 작업을 추가하려고 합니다.

    ![Azure - 논리 앱 - 작업 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  **HTTP**를 입력하여 HTTP 작업을 검색하고 **HTTP – HTTP** 작업을 선택합니다.
10. **메서드** 값으로 **Post**를 선택합니다.
11. 이 자습서 초반에 만든 **Complete**로 명명된 Webhook의 URL을 **Uri** 값으로 입력합니다.

    ![Azure - 논리 앱 - 작업 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. **If true** 상자에서 **작업 추가**를 선택합니다. 받는 사람에게 나머지 VM이 종료되었음을 알리는 전자 메일을 보낼 전자 메일 작업을 추가합니다.
13. "이메일 보내기"를 검색하고 사용하는 이메일 서비스에 따라 *이메일 보내기* 작업을 선택합니다.
14. 받는 사람에게 선택적 VM이 종료되었음을 알리는 전자 메일에 대해 **받는 사람**, **제목** 및 **본문** 텍스트를 추가합니다. **BudgetName** 및 **NotificationThresholdAmount** 동적 콘텐츠를 사용하여 제목과 본문 필드를 채웁니다.

    ![Azure - 논리 앱 - 전자 메일 보내기 세부 정보](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. **Logic App 디자이너** 블레이드의 맨 위에서 **저장**을 클릭합니다.

### <a name="logic-app-summary"></a>논리 앱 요약

완료 시 논리 앱은 다음 모습과 같습니다. 임계값 기반 오케스트레이션이 필요하지 않은 시나리오의 대부분 기초에서는 자동화 스크립트를 **Monitor**에서 바로 호출하고 **논리 앱** 단계를 건너뛸 수 있습니다.

   ![Azure - 논리 앱 - 전체 보기](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

논리 앱을 저장했을 때 호출할 수 있는 URL이 생성되었습니다. 이 URL을 이 자습서의 다음 섹션에서 사용할 것입니다.

## <a name="create-an-azure-monitor-action-group"></a>Azure Monitor 작업 그룹 만들기

작업 그룹은 사용자가 정의한 알림 기본 설정 컬렉션입니다. 경고가 트리거되면 특정 작업 그룹은 경고 알림을 받을 수 있습니다. Azure 경고는 특정 조건에 따라 사전에 알림을 보내 조치를 취한 기회를 제공합니다. 경고는 메트릭 및 로그를 포함하여 여러 출처의 데이터를 사용할 수 있습니다.

작업 그룹은 예산과 통합할 유일한 엔드포인트입니다. 여러 채널에서 알림을 설정할 수 있지만, 이 시나리오의 경우 이 자습서 초반에 만든 논리 앱에 초점을 맞추려고 합니다.

### <a name="create-an-action-group-in-azure-monitor"></a>Azure Monitor에서 작업 그룹 만들기

작업 그룹을 만들면 이 자습서 초반에 만든 논리 앱으로 연결됩니다.

1.  아직 [Azure Portal](https://portal.azure.com/)에 로그인하지 않은 경우 로그인하여 **모든 서비스** > **Monitor**를 선택합니다.
2.  **설정** 섹션에서 **작업 그룹**을 선택합니다.
3.  **작업 그룹** 블레이드에서 **작업 그룹 추가**를 선택합니다.
4.  다음 사항을 추가하고 확인합니다.
    - 작업 그룹 이름
    - 짧은 이름
    - 구독
    - 리소스 그룹

    ![Azure - 논리 앱 - 작업 그룹 추가](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  **작업 그룹 추가** 창에서 LogicApp 작업을 추가합니다. 작업 이름을 **Budget-BudgetLA**로 지정합니다. **논리 앱** 창에서 **구독**과 **리소스 이름**을 선택합니다. 그런 다음, 이 자습서 초반에 만든 **논리 앱**을 선택합니다.
6.  **확인**을 클릭하여 논리 앱을 설정합니다. 그런 다음, **작업 그룹 추가** 창에서 **확인**을 선택하여 작업 그룹을 만듭니다.

효과적으로 예산을 오케스트레이션하는 데 필요한 모든 지원 구성 요소를 완료했습니다. 이제는 예산을 만들고 사용자가 만든 작업 그룹을 사용하도록 구성하면 됩니다.

## <a name="create-the-azure-budget"></a>Azure 예산 만들기

사용 하 여 Azure 포털는 예산 범위를 만들 수 있습니다 합니다 [예산 기능](../cost-management/tutorial-acm-create-budgets.md) Cost Management에서. 또는 REST Api, Powershell cmdlet을 사용 하는 예산 범위를 만들거나 CLI를 사용할 수 있습니다. 다음 절차는 REST API를 사용합니다. REST API를 호출하기 전에 권한 부여 토큰이 필요합니다. 권한 부여 토큰을 만들기 위해 [ARMClient](https://github.com/projectkudu/ARMClient) 프로젝트를 사용할 수 있습니다. **ARMClient**를 사용하면 Azure Resource Manager에 사용자를 인증하고 API를 호출하도록 토큰을 받을 수 있습니다.

### <a name="create-an-authentication-token"></a>인증 토큰 만들기

1.  GitHub에서 [ARMClient](https://github.com/projectkudu/ARMClient) 프로젝트로 이동합니다.
2.  리포지토리를 복제하여 로컬 복사본을 얻습니다.
3.  Visual Studio에서 프로젝트를 열고 빌드합니다.
4.  빌드가 성공하면 실행 파일은 *\bin\debug* 폴더에 있어야 합니다.
5.  ARMClient를 실행합니다. 명령 프롬프트를 열고 프로젝트 루트에서 *\bin\debug* 폴더로 이동합니다.
6.  로그인하여 인증하려면 명령 프롬프트에 다음 명령을 입력합니다.<br>
    `ARMClient login prod`
7.  출력에서 **구독 guid**를 복사합니다.
8.  권한 부여 토큰을 클립보드에 복사하려면 명령 프롬프트에 다음 명령을 입력합니다. 단, 반드시 위의 단계에서 복사한 구독 ID를 사용하세요. <br>
    `ARMClient token <subscription GUID from previous step>`

    위의 단계가 완료되면 다음 메시지가 표시됩니다.<br>
    **토큰을 클립보드에 복사했습니다.**
9.  이 자습서의 다음 섹션의 단계에 사용할 토큰을 저장합니다.

### <a name="create-the-budget"></a>예산 만들기

다음으로, Azure Consumption REST API를 호출하여 예산을 만들도록 **Postman**을 구성합니다. Postman은 API 개발 환경입니다. 환경 및 컬렉션 파일을 Postman으로 가져옵니다. 컬렉션에는 Azure Consumption REST API를 호출하는 HTTP 요청의 그룹화된 정의가 포함되어 있습니다. 환경 파일에는 컬렉션에 사용되는 변수가 포함되어 있습니다.

1.  [Postman REST 클라이언트](https://www.getpostman.com/)를 다운로드한 후 열어 REST API를 실행합니다.
2.  Postman에서 새 요청을 만듭니다.

    ![Postman - 새 요청 만들기](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  새 요청에 아무 것도 없도록 새 요청을 컬렉션으로 저장합니다.

    ![Postman - 새 요청 저장](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  `Get`에서 `Put` 작업으로 요청을 변경합니다.
5.  `{subscriptionId}`를 이 자습서의 이전 섹션에서 사용한 **구독 ID**로 바꿔 다음 URL을 수정합니다. 또한, `{budgetName}`에 대한 값으로 "SampleBudget"을 포함하도록 URL을 수정합니다. `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Postman에서 **헤더** 탭을 선택합니다.
7.  "권한 부여"로 명명된 새 **키**를 추가합니다.
8.  **값**을 마지막 섹션의 끝 부분에서 ArmClient를 사용하여 만든 토큰으로 설정합니다.
9.  Postman에서 **본문** 탭을 선택합니다.
10. **원시** 단추 옵션을 선택합니다.
11. 하지만 텍스트 상자에 붙여를 아래 샘플 예산 정의 바꿔야 합니다 **subscriptionid**, **budgetname**, 및 **actiongroupname** 매개 변수를 프로그램 구독 ID, 예산에 대 한 고유 이름 및 URL과 요청 본문에서 만든 작업 그룹 이름:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. **보내기**를 눌러 요청을 보냅니다.

이제 [예산 API](https://docs.microsoft.com/rest/api/consumption/budgets)를 호출하는 데 필요한 모든 요소를 갖추었습니다. 예산 API 참조에는 다음을 포함해 특정 요청에 대한 추가적인 세부 정보가 있습니다.
    - **budgetName** - 여러 예산이 지원됩니다.  예산 이름은 고유해야 합니다.
    - **범주** - **비용** 또는 **사용량**이어야 합니다. API는 비용 및 사용량 예산을 모두 지원합니다.
    - **timeGrain** -월별, 분기별 또는 연간 예산입니다. 기간의 끝에서 크기가 다시 설정됩니다.
    - **필터** -필터를 사용하면 선택한 범위 내 측정 리소스 집합으로 예산을 좁힐 수 있습니다. 예를 들어 필터는 구독 수준 예산에 대한 리소스 그룹의 컬렉션일 수 있습니다.
    - **알림** – 알림 세부 정보 및 임계값을 결정합니다. 여러 임계값을 설정하고 알림을 받을 이메일 주소 또는 작업 그룹을 제공할 수 있습니다.

## <a name="summary"></a>요약

이 자습서에 따라 다음 방법에 대해 알아보았습니다.
- VM을 중지하도록 Azure Automation Runbook을 만드는 방법
- 예산 임계값에 기반하여 트리거될 Azure 논리 앱을 만들고 올바른 매개 변수로 관련 Runbook을 호출합니다.
- 예산 임계값이 충족되면 Azure 논리 앱을 트리거하도록 구성될 Azure Monitor 작업 그룹을 만드는 방법
- 원하는 임계값으로 Azure 예산을 만들고 작업 그룹에 연결하는 방법

이제 사용자가 구성한 예산 임계값에 도달하면 VM이 종료되는 구독에 완벽하게 기능하는 예산이 마련되었습니다.

## <a name="next-steps"></a>다음 단계

- Azure 청구 시나리오에 대한 자세한 내용은 [청구 및 비용 관리 자동화 시나리오](billing-cost-management-automation-scenarios.md)를 참조하세요.

---
title: 자습서 - Azure 예산 만들기 및 관리
description: 이 자습서는 사용자가 소비하는 Azure 서비스 비용을 계획하고 설명하는 데 도움이 됩니다.
author: bandersmsft
ms.author: banders
ms.date: 08/20/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 739d300faf77564891b2b783a3eb1cca50373015
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718356"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>자습서: Azure 예산 만들기 및 관리

Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 예산을 사용하여 특정 기간 중 사용자가 소비 또는 구독하는 Azure 서비스를 설명할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 사용자가 만든 예산 임계값이 초과된 경우 알림만 트리거되고 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다.

비용 및 사용량 데이터는 일반적으로 20시간 이내에 사용할 수 있으며 예산은 12-14시간마다 이러한 비용에 대해 계산됩니다. 예산 임계값에 도달하면 일반적으로 계산 후 1시간 이내에 이메일 알림이 전송됩니다.

예산은 미래의 만료 날짜를 선택한 경우 동일한 예산 금액에 대해 기간(월별, 분기별 또는 연간)이 끝나면 자동으로 다시 설정됩니다. 동일한 예산 금액으로 다시 설정하므로, 미래 기간에 대해 예산 책정된 통화 금액이 다를 경우 별도의 예산을 만들어야 합니다.

이 자습서의 예제는 Azure EA(Enterprise Agreement) 구독에 대한 예산을 만들고 편집하는 과정을 안내합니다.

[Azure Portal을 사용하여 구독에 예산을 적용](https://www.youtube.com/watch?v=UrkHiUx19Po) 비디오를 통해 Azure에서 예산을 만들어 지출을 모니터링하는 방법을 확인하세요. 다른 비디오를 시청하려면 [Cost Management YouTube 채널](https://www.youtube.com/c/AzureCostManagement)을 방문하세요.

>[!VIDEO https://www.youtube.com/embed/UrkHiUx19Po]

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * PowerShell을 사용하여 예산 만들기 및 편집
> * Azure Resource Manager 템플릿을 사용하여 예산 만들기

## <a name="prerequisites"></a>필수 구성 요소

예산은 다음과 같은 유형의 Azure 계정 유형 및 범위에 대해 지원됩니다.

- Azure RBAC(Azure 역할 기반 액세스 제어) 범위
    - 관리 그룹
    - Subscription
- 기업계약 범위
    - 청구 계정
    - department
    - 등록 계정
- 개별 계약
    - 청구 계정
- Microsoft 고객 계약 범위
    - 청구 계정
    - 청구 프로필
    - 청구서 섹션
    - Customer
- AWS 범위
    - 외부 계정
    - 외부 구독


예산을 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다.

새 구독이 있는 경우 즉시 예산을 만들거나 다른 Cost Management 기능을 사용할 수 없습니다. 모든 Cost Management 기능을 사용하려면 최대 48시간이 걸릴 수 있습니다.

Azure EA 구독의 경우 예산을 보는 읽기 권한이 있어야 합니다. 예산을 만들고 관리하려면 기여자 사용 권한이 있어야 합니다.

다음 Azure 사용 권한 또는 범위는 사용자 및 그룹별 예산에 대한 구독에 따라 지원됩니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

- 소유자 – 구독에 대한 예산을 만들고, 수정하거나 삭제할 수 있습니다.
- 기여자 및 Cost Management 기여자 – 자신의 예산을 만들거나, 수정하거나, 삭제할 수 있습니다. 다른 사용자가 만든 예산에 대한 예산 금액을 수정할 수 있습니다.
- 읽기 권한자 및 Cost Management 읽기 권한자 - 사용 권한이 있는 예산을 볼 수 있습니다.

Cost Management 데이터에 대한 사용 권한을 할당하는 방법에 대한 자세한 내용은 [Cost Management 데이터에 대한 액세스 할당](../../cost-management/assign-access-acm-data.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

- [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-budget-in-the-azure-portal"></a>Azure Portal에서 예산 만들기

월별, 분기별 또는 연간 기간에 대한 Azure 구독 예산을 만들 수 있습니다.

예산을 만들거나 보려면 Azure Portal에서 원하는 범위를 열고 메뉴에서 **예산**을 선택합니다. 예를 들어 **구독**으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **예산**을 선택합니다. **범위** 필을 사용하여 예산에서 관리 그룹과 같은 다른 범위로 전환합니다. 범위에 대한 자세한 내용은 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

예산을 만들면 예산 대비 현재 지출을 간단하게 보여 줍니다.

**추가**를 선택합니다.

![이미 생성된 예산 목록을 보여주는 예제](./media/tutorial-acm-create-budgets/budgets01.png)

**예산 만들기** 창에서 표시된 범위가 올바른지 확인합니다. 추가하려는 필터를 선택합니다. 필터를 사용하면 구독에 있는 리소스 그룹 또는 가상 머신과 같은 서비스 등과 같이 특정 비용에 대한 예산을 만들 수 있습니다. 비용 분석에 사용할 수 있는 모든 필터를 예산에도 적용할 수도 있습니다.

범위 및 필터를 확인한 후에는 예산 이름을 입력합니다. 그런 다음, 월별, 분기별 또는 연간 예산 재설정 기간을 선택합니다. 이 재설정 기간은 예산으로 분석되는 시간 범위를 결정합니다. 예산으로 평가되는 비용은 각각의 새로운 기간이 시작될 때 0부터 시작합니다. 분기별 예산을 만드는 경우 월별 예산과 동일한 방식으로 진행되지만, 해당 분기에 대한 예산 금액이 분기에 속한 3개월에 고르게 나누어진다는 차이점이 있습니다. 연간 예산 금액은 1년 12개월에 걸쳐 모두 고르게 나누어집니다.

종량제, MSDN 또는 Visual Studio 구독이 있는 경우 송장 청구 기간이 달력상 월과 다를 수 있습니다. 구독 유형 및 리소스 그룹의 경우 청구서 기간 또는 월별에 맞춰 조정된 예산을 만들 수 있습니다. 청구서 기간에 맞게 조정된 예산을 만들려면 **청구 월**, **청구 분기** 또는 **청구 연도**의 재설정 기간을 선택합니다. 월별로 조정된 예산을 만들려면 **월별**, **분기별** 또는 **연간**의 재설정 기간을 선택합니다.

다음으로, 예산이 무효화되고 비용 평가가 중지되는 만료 날짜를 확인합니다.

지금까지의 예산에서 선택한 필드에 따라 예산에 사용할 임계값을 선택하는 데 도움이 되는 그래프가 표시됩니다. 제안된 예산은 향후 기간에 발생할 수 있는 가장 높은 예상 비용을 기준으로 합니다. 예산 금액을 변경할 수 있습니다.

![월별 비용 데이터로 예산 생성을 보여주는 예제 ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

예산 금액을 구성한 후 **다음**을 선택하여 예산 경고를 구성합니다. 예산은 1개 이상의 비용 임계값(예산의 %) 및 해당하는 이메일 주소가 필요합니다. 필요에 따라 단일 예산에 최대 5개의 임계값과 5개의 이메일 주소를 포함할 수 있습니다. 예산 임계값에 도달하면 일반적으로 계산 후 1시간 이내에 이메일 알림이 전송됩니다.

이메일을 받으려면 이메일이 정크 메일 폴더로 이동하지 않도록 승인된 보낸 사람 목록에 azure-noreply@microsoft.com을 추가합니다. 알림에 대한 자세한 내용은 [비용 경고 사용](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md)을 참조하세요.

아래 예제에서 예산의 90%에 도달했을 때 이메일 경고가 생성되었습니다. 예산 API를 사용하여 예산을 만드는 경우 사용자가 경고를 수신하도록 역할을 할당할 수도 있습니다. 사용자에게 역할을 할당하는 것은 Azure Portal에서 지원되지 않습니다. Azure 예산 API에 대한 자세한 내용은 [예산 API](/rest/api/consumption/budgets)를 참조하세요.

경고 제한은 제공된 예산 임계값의 0.01~1000% 범위를 지원합니다.

![경고 조건을 보여주는 예제](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

예산을 만들면 비용 분석에 표시됩니다. 지출 추세에 대한 예산을 보는 것은 [비용 및 지출을 분석](../../cost-management/quick-acm-cost-analysis.md)하기 위해 시작하는 첫 단계 중 하나입니다.

![비용 분석에 표시된 예산 및 지출 예제](./media/tutorial-acm-create-budgets/cost-analysis.png)

앞의 예제에서는 구독에 대한 예산을 만들었습니다. 리소스 그룹에 대한 예산을 만들 수도 있습니다. 리소스 그룹에 대한 예산을 만들려는 경우 **Cost Management + 청구**&gt;**구독**&gt;으로 이동하여 구독을 선택하고 > **리소스 그룹**에서 > 리소스 그룹을 선택하고 > **예산**에서 > 예산을 **추가**합니다.

### <a name="create-a-budget-for-combined-azure-and-aws-costs"></a>Azure 및 AWS 비용 통합에 대한 예산 만들기

관리 그룹을 통합 및 연결된 계정과 함께 커넥터에 할당하여 Azure 및 AWS 비용을 함께 그룹화할 수 있습니다. 동일한 관리 그룹에 Azure 구독을 할당합니다. 그런 다음, 통합 비용에 대한 예산을 만듭니다.

1. Cost Management에서 **예산**을 선택합니다.
1. **추가**를 선택합니다.
1. **범위 변경**을 선택한 다음, 관리 그룹을 선택합니다.
1. 완료될 때까지 예산 작성을 계속 진행합니다.

## <a name="costs-in-budget-evaluations"></a>예산 평가의 비용

이제 예산 비용 평가에 예약 인스턴스 및 구매 데이터가 포함됩니다. 요금이 적용되는 경우 요금을 평가에 통합할 때 경고를 받을 수 있습니다. [Azure Portal](https://portal.azure.com)에 로그인하여 예산 임계값이 새 비용을 고려하도록 제대로 구성되어 있는지 확인하는 것이 좋습니다. Azure 청구 요금은 변경되지 않습니다. 이제 예산은 더욱 완전한 비용 세트를 기준으로 평가됩니다. 요금이 적용되지 않으면 예산 동작이 변경되지 않은 상태로 유지됩니다.

자사의 Azure 사용 요금에 대해서만 예산이 평가되도록 새 비용을 필터링하려면 다음 필터를 예산에 추가합니다.

- 게시자 유형: Azure
- 요금 유형: 사용

예산 비용 평가는 실제 비용을 기준으로 합니다. 여기에는 할부 상환이 포함되지 않습니다. 예산에서 사용할 수 있는 필터링 옵션에 대한 자세한 내용은 [그룹화 및 필터링 옵션 이해](group-filter.md)를 참조하세요.

## <a name="trigger-an-action-group"></a>작업 그룹 트리거

구독 또는 리소스 그룹 범위에 대한 예산을 만들거나 편집하는 경우 작업 그룹을 호출하도록 구성할 수 있습니다. 작업 그룹은 예산 임계값이 충족되면 다양한 작업을 수행할 수 있습니다. 작업 그룹은 현재 구독 및 리소스 그룹 범위에서만 지원됩니다. 작업 그룹에 대해 자세히 알아보려면 [Azure Portal에서 작업 그룹 만들기 및 관리](../../azure-monitor/platform/action-groups.md)를 참조하세요. 작업 그룹에서 예산 기반 자동화를 사용하는 방법에 대한 자세한 내용은 [Azure 예산으로 비용 관리](../manage/cost-management-budget-scenario.md)를 참조하세요.

작업 그룹을 만들거나 업데이트하려면 예산을 만들거나 편집하는 동안 **작업 그룹 관리**를 선택합니다.

![관리 작업 그룹을 표시하는 예산 만들기의 예제](./media/tutorial-acm-create-budgets/manage-action-groups01.png)

다음으로, **작업 그룹 추가**를 선택하고 작업 그룹을 만듭니다.

![작업 그룹 추가 상자의 이미지](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

작업 그룹을 만든 후에는 상자를 닫아 예산으로 돌아갑니다.

개별 임계값이 충족될 때 작업 그룹을 사용하도록 예산을 구성합니다. 최대 5개의 서로 다른 임계값이 지원됩니다.

![경고 조건에 대한 작업 그룹 선택을 보여주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

다음 예제에서는 50%, 75% 및 100%로 설정된 예산 임계값을 보여줍니다. 각각은 지정된 작업 그룹 내에서 지정된 작업을 트리거하도록 구성됩니다.

![다양한 작업 그룹 및 동작 유형을 사용하여 구성된 경고 조건을 보여주는 예제](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

작업 그룹에 대한 예산 통합은 일반 경고 스키마를 사용하지 않는 작업 그룹에 대해서만 작동합니다. 스키마를 사용하지 않도록 설정하는 방법에 대한 자세한 내용은 [일반 경고 스키마를 사용하도록 설정하려면 어떻게 할까요?](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)를 참조하세요.

## <a name="create-and-edit-budgets-with-powershell"></a>PowerShell을 사용하여 예산 만들기 및 편집

EA 고객은 Azure PowerShell 모듈을 사용하여 프로그래밍 방식으로 예산을 만들고 편집할 수 있습니다.  최신 버전의 Azure PowerShell을 다운로드하려면 다음 명령을 실행합니다.

```azurepowershell-interactive
install-module -name Az
```

다음 예제 명령은 예산을 만듭니다.

```azurepowershell-interactive
#Sign into Azure Powershell with your account

Connect-AzAccount

#Select a subscription to to monitor with a budget

select-AzSubscription -Subscription "Your Subscription"

#Create an action group email receiver and corresponding action group

$email1 = New-AzActionGroupReceiver -EmailAddress test@test.com -Name EmailReceiver1
$ActionGroupId = (Set-AzActionGroup -ResourceGroupName YourResourceGroup -Name TestAG -ShortName TestAG -Receiver $email1).Id

#Create a monthly budget that sends an email and triggers an Action Group to send a second email. Make sure the StartDate for your monthly budget is set to the first day of the current month. Note that Action Groups can also be used to trigger automation such as Azure Functions or Webhooks.

New-AzConsumptionBudget -Amount 100 -Name TestPSBudget -Category Cost -StartDate 2020-02-01 -TimeGrain Monthly -EndDate 2022-12-31 -ContactEmail test@test.com -NotificationKey Key1 -NotificationThreshold 0.8 -NotificationEnabled -ContactGroup $ActionGroupId
```

## <a name="create-a-budget-with-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 예산 만들기

Azure Resource Manager 템플릿을 사용하여 예산을 만들 수 있습니다. 템플릿을 사용하려면 [Azure Resource Manager 템플릿을 사용하여 예산 만들기](quick-create-budget-template.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

예산을 만들고, 더 이상 사용되지 않는 경우 세부 정보를 보고 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal에서 예산 만들기
> * PowerShell을 사용하여 예산 만들기 및 편집
> * Azure Resource Manager 템플릿을 사용하여 예산 만들기

비용 관리 데이터에 대한 되풀이 내보내기를 만들려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [내보낸 데이터를 만들고 관리](tutorial-export-acm-data.md)

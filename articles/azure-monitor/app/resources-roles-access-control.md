---
title: Azure Application Insights에서 리소스, 역할 및 액세스 제어 | Microsoft Docs
description: 조직 Insights의 소유자, 참여자 및 읽기 권한자입니다.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cd1e69ecd121380353ad6400d473d572b7b7bb3e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076687"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights에서 리소스, 역할 및 액세스 제어

Azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)를 사용 하 여 azure [Application Insights][start]에서 데이터에 대 한 읽기 및 업데이트 액세스를 제어할 수 있습니다.

> [!IMPORTANT]
> 리소스 자체가 아닌 애플리케이션 리소스가 속한 **리소스 그룹 또는 구독** 의 사용자에게 액세스 권한을 할당합니다. **Application Insights 구성 요소 기여자** 역할을 할당합니다. 이렇게 하면 애플리케이션 리소스와 함께 웹 테스트 및 경고에 대한 액세스를 통합적으로 제어할 수 있습니다. [자세히 알아보기](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>리소스, 그룹 및 구독

먼저 몇 가지를 정의하겠습니다.

* **리소스** - Microsoft Azure 서비스의 인스턴스입니다. Application Insights는 애플리케이션에서 보낸 원격 분석 데이터를 수집, 분석 및 표시합니다.  다른 유형의 Azure 리소스로는 웹 앱, 데이터베이스 및 VM이 있습니다.
  
    리소스를 보려면 [Azure Portal][portal]열고 로그인 한 후 모든 리소스를 클릭 합니다. 리소스를 찾으려면 필터 필드에 리소스 이름의 일부를 입력합니다.
  
    ![Azure 리소스 목록](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**리소스 그룹**][group] - 모든 리소스가 하나의 그룹에 속해 있습니다. 그룹은 특히 액세스 제어에 대한 관련 리소스를 간편하게 관리할 수 있는 방법입니다. 예를 들어 한 리소스 그룹에 Web App, 앱을 모니터링할 Application Insights 리소스, 내보낸 데이터를 보관할 스토리지 리소스를 모두 집어넣을 수 있습니다.

* [**구독**](https://portal.azure.com) - Application Insights 또는 다른 Azure 리소스를 사용하려면 Azure 구독에 로그인합니다. 모든 리소스 그룹은 Azure 구독에 속합니다. 여기에서 패키지를 선택하고, 조직 구독인 경우 멤버와 해당 액세스 권한을 선택합니다.
* [**Microsoft 계정**][account] - Microsoft Azure 구독, XBox Live, Outlook.com 및 기타 Microsoft 서비스에 로그인하는 데 사용하는 사용자 이름 및 암호입니다.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> 리소스 그룹의 액세스 제어

애플리케이션에 대해 만든 리소스 외에도 경고 및 웹 테스트에 대한 별도의 리소스가 숨겨져 있다는 사실을 이해해야 합니다. Application Insights 리소스와 동일한 [리소스 그룹](#resource-group) 에 연결 됩니다. 웹 사이트 또는 스토리지 같은 다른 Azure 서비스를 여기에 넣었을 수도 있습니다.

## <a name="to-provide-access-to-another-user"></a>다른 사용자에 대한 액세스 권한 제공

구독 또는 리소스 그룹에 대한 소유자 권한이 있어야 합니다.

사용자에게 [Microsoft 계정][account]이 있거나 해당 [조직의 Microsoft 계정](../../active-directory/fundamentals/sign-up-organization.md)에 액세스해야 합니다. 개별 사용자에 대한 액세스 권한을 제공할 수 있으며 Azure Active Directory에 정의된 사용자 그룹에 대한 액세스 권한도 제공할 수 있습니다.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>리소스 그룹 또는 리소스 자체로 직접 이동

왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

![Azure Portal의 액세스 제어 단추 스크린샷](./media/resources-roles-access-control/0001-access-control.png)

**역할 할당 추가** 선택

![빨간색으로 강조 표시된 추가 단추를 사용하는 액세스 제어 메뉴의 스크린샷](./media/resources-roles-access-control/0002-add.png)

아래 **권한 추가** 보기는 주로 Application Insights 리소스에 관련된 것으로, 리소스 그룹과 같은 상위 수준에서 액세스 제어 권한을 보는 경우 추가 Application Insights 중심이 아닌 추가 역할을 볼 수 있습니다.

모든 Azure 역할 기반 액세스 제어 기본 제공 역할에 대한 정보를 보려면 [공식 참조 콘텐츠](../../role-based-access-control/built-in-roles.md)를 사용합니다.

![액세스 제어 사용자 역할 목록의 스크린샷](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>역할 선택

해당하는 경우 연결된 공식 참조 설명서를 연결합니다.

| 역할 | 리소스 그룹에서 할 수 있는 일 |
| --- | --- |
| [소유자](../../role-based-access-control/built-in-roles.md#owner) |사용자 액세스를 포함하여 모든 것을 변경할 수 있습니다. |
| [기여자](../../role-based-access-control/built-in-roles.md#contributor) |모든 리소스를 포함하여 모든 것을 편집할 수 있습니다. |
| [Application Insights 구성 요소 기여자](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Application Insights 리소스를 편집할 수 있습니다. |
| [판독기](../../role-based-access-control/built-in-roles.md#reader) |모든 것을 볼 수 있지만 변경할 수는 없습니다. |
| [Application Insights 스냅샷 디버거](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | 사용자에게 Application Insights 스냅샷 디버거 기능을 사용할 수 있는 권한을 부여합니다. 이 역할은 소유자 또는 기여자 역할 모두에 포함되지 않습니다. |
| Azure Service Deploy 릴리스 관리 기여자 | Azure Service Deploy를 통해 배포하는 서비스에 대한 기여자 역할입니다. |
| [데이터 제거자](../../role-based-access-control/built-in-roles.md#data-purger) | 개인 데이터 제거를 위한 특별 역할입니다. 자세한 내용은 [개인 데이터에 대한 지침](../platform/personal-data-mgmt.md)을 참조하세요.   |
| ExpressRoute 관리자 | 기본 경로를 삭제하고 관리할 수 있습니다.|
| [Log Analytics 기여자](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics 참가자는 모든 모니터링 데이터를 읽고 모니터링 설정을 편집할 수 있습니다. 모니터링 설정 편집에는 VM에 VM 확장 추가, Azure Storage에서 로그 컬렉션을 구성할 수 있는 스토리지 계정 키 읽기, Automation 계정 생성 및 구성, 솔루션 추가 및 모든 Azure 리소스에 대한 Azure 진단을 구성하는 기능도 포함되어 있습니다.  |
| [Log Analytics 독자](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics 독자는 모든 Azure 리소스에 대한 Azure 진단의 구성 보기를 비롯하여 모니터링 설정 보기 및 모든 모니터링 데이터를 보고 검색할 수 있습니다. |
| masterreader | 사용자가 모든 것을 볼 수 있지만 변경은 할 수 없도록 허용합니다. |
| [Monitoring Contributor](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | 모든 모니터링 데이터를 읽고 모니터링 설정을 업데이트할 수 있습니다.|
| [모니터링 메트릭 게시자](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Azure 리소스에 대한 게시 메트릭을 사용하도록 설정합니다. |
| [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader) | 모든 모니터링 데이터를 읽을 수 있습니다. |
| 리소스 정책 참가자(미리 보기) | 리소스 정책을 생성/수정하고, 지원 티켓을 만들고, 리소스/계층 구조를 읽을 수 있는 권한을 가진 EA의 백필된 사용자입니다.  |
| [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) | 사용자가 Azure 리소스에 다른 사용자에 대한 액세스를 관리할 수 있도록 허용합니다.|
| [웹 사이트 기여자](../../role-based-access-control/built-in-roles.md#website-contributor) | 웹 사이트(웹 계획은 제외)를 관리할 수 있지만 액세스할 수는 없습니다.|

'편집'에는 다음 항목을 만들고, 삭제하고, 업데이트하는 작업이 포함됩니다.

* 리소스
* 웹 테스트
* 경고
* 연속 내보내기

#### <a name="select-the-user"></a>사용자 선택

원하는 사용자가 디렉터리에 없는 경우 Microsoft 계정이 있는 사람을 초대할 수 있습니다.
Outlook.com, OneDrive, Windows Phone 또는 XBox Live를 사용하는 사람은 Microsoft 계정을 갖고 있습니다.

## <a name="related-content"></a>관련 콘텐츠

* [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>역할 멤버 자격을 확인하기 위한 PowerShell 쿼리

특정 역할은 알림 및 이메일 알림에 연결할 수 있으므로 지정된 역할에 속하는 사용자의 목록을 생성할 수 있는 것이 유용할 수 있습니다. 이러한 목록 유형을 생성하는 데 도움이 되도록 사용자의 특정 필요에 맞게 조정할 수 있는 다음과 같은 샘플 쿼리가 제공됩니다.

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>관리자 역할 + 기여자 역할을 위한 전체 구독 쿼리

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>소유자 및 기여자에 대한 특정 Application Insights 리소스의 컨텍스트 내에서 쿼리

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>소유자 및 기여자에 대한 특정 리소스 그룹의 컨텍스트 내에서 쿼리

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

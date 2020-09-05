---
title: 대규모로 Azure 센티널 작업 영역 관리
description: 위임 된 고객 리소스에서 Azure 센티널을 효과적으로 관리 하는 방법을 알아봅니다.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: 328c55afc141a7f2efd85104453342b62eae0bb2
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050814"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>대규모로 Azure 센티널 작업 영역 관리

서비스 공급자로 서 [Azure Lighthouse](../overview.md)에 여러 고객 테 넌 트를 등록 했을 수 있습니다. Azure Lighthouse를 사용 하면 서비스 공급자가 Azure Active Directory 여러 Azure AD (Azure AD) 테 넌 트에서 한 번에 대규모 작업을 수행 하 여 관리 작업을 보다 효율적으로 만들 수 있습니다.

Azure 센티널은 보안 분석과 위협 인텔리전스를 제공 하 여 경고 검색, 위협 가시성, 자동 관리 검색 및 위협 대응을 위한 단일 솔루션을 제공 합니다. Azure Lighthouse를 사용 하면 대규모 테 넌 트 간에 여러 Azure 센티널 작업 영역을 관리할 수 있습니다. 이렇게 하면 여러 작업 영역에서 쿼리를 실행 하거나 통합 문서를 만들어 연결 된 데이터 원본의 데이터를 시각화 하 고 모니터링 하 여 통찰력을 얻을 수 있습니다. 쿼리 및 플레이 북와 같은 IP는 관리 테 넌 트에 남아 있지만 고객 테 넌 트의 보안 관리를 수행 하는 데 사용할 수 있습니다.

이 항목에서는 테 넌 트 간 표시 유형 및 관리 되는 보안 서비스에 대해 확장 가능한 방법으로 [Azure 센티널](../../sentinel/overview.md) 을 사용 하는 방법에 대 한 개요를 제공 합니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만이 지침은 [Azure Lighthouse를 사용 하 여 여러 테 넌 트를 관리](../concepts/enterprise.md)하는 기업에도 적용 됩니다.

## <a name="architectural-considerations"></a>아키텍처 고려 사항

Azure 센티널을 사용 하 여 보안 서비스 제공 서비스를 빌드하려고 하는 MSSP (관리 보안 서비스 공급자)의 경우 개별 고객 테 넌 트 내에 배포 된 여러 Azure 센티널 작업 영역을 중앙에서 모니터링, 관리 및 구성 하기 위해 단일 SOC (보안 작업 센터)가 필요할 수 있습니다. 마찬가지로, 여러 Azure AD 테 넌 트가 있는 기업은 테 넌 트에 배포 된 여러 Azure 센티널 작업 영역을 중앙에서 관리 하려고 할 수 있습니다.

이 중앙 배포 모델의 장점은 다음과 같습니다.

- 데이터의 소유권은 관리 되는 각 테 넌 트에 남아 있습니다.
- 지리적 경계 내에 데이터를 저장 하기 위한 요구 사항을 지원 합니다.
- 여러 고객에 대 한 데이터가 동일한 작업 영역에 저장 되지 않기 때문에 데이터 격리를 보장 합니다.
- 관리 되는 테 넌 트의 데이터 반출을 방지 하 여 데이터 호환성을 보장 합니다.
- 관련 비용은 관리 테 넌 트를 관리 하는 대신 관리 되는 각 테 넌 트에 부과 됩니다.
- Azure 센티널 (예: Azure AD 활동 로그, Office 365 로그 또는 Microsoft 위협 방지 경고)과 통합 된 모든 데이터 원본 및 데이터 커넥터의 데이터는 각 고객 테 넌 트 내에 유지 됩니다.
- 네트워크 대기 시간을 줄입니다.
- 새 자회사 또는 고객을 쉽게 추가 하거나 제거할 수 있습니다.

## <a name="granular-role-based-access-control-rbac"></a>세분화 된 역할 기반 액세스 제어 (RBAC)

MSSP에서 관리 하는 각 고객 구독은 [Azure Lighthouse로 등록](onboard-customer.md)되어야 합니다. 이렇게 하면 관리 테 넌 트의 지정 된 사용자가 고객 테 넌 트에 배포 된 Azure 센티널 작업 영역에 액세스 하 고 관리 작업을 수행할 수 있습니다.

권한 부여를 만들 때 관리 하는 테 넌 트의 사용자, 그룹 또는 서비스 사용자에 게 Azure 센티널 기본 제공 역할을 할당할 수 있습니다.

- [Azure 센티널 판독기](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure 센티널 응답자](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure 센티널 기여자](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

추가 기능을 수행 하기 위해 추가 기본 제공 역할을 할당할 수도 있습니다. Azure 센티널에서 사용할 수 있는 특정 역할에 대 한 자세한 내용은 [Azure 센티널의 권한](../../sentinel/roles.md)을 참조 하세요.

고객을 등록 하면 지정 된 사용자는 관리 테 넌 트에 로그인 하 고 할당 된 역할을 사용 하 여 [고객의 Azure 센티널 작업 영역에 직접 액세스할](../../sentinel/multiple-tenants-service-providers.md) 수 있습니다.

## <a name="view-and-manage-incidents-across-workspaces"></a>작업 영역에서 인시던트 보기 및 관리

여러 고객에 대 한 Azure 센티널 리소스를 관리 하는 경우 한 번에 여러 테 넌 트의 여러 작업 영역에서 인시던트를 보고 관리할 수 있습니다. 자세한 내용은 [한 번에 여러 작업 영역에서 인시던트를 사용](../../sentinel/multiple-workspace-view.md) 하 고 작업 [영역 및 테 넌 트 간에 Azure 센티널 확장](../../sentinel/extend-sentinel-across-workspaces-tenants.md)을 참조 하세요.

> [!NOTE]
> 관리 되는 테 넌 트의 사용자에 게 관리 되는 모든 작업 영역에 대 한 읽기 및 쓰기 권한이 할당 되었는지 확인 합니다. 사용자에 게 일부 작업 영역에 대 한 읽기 권한만 있는 경우 해당 작업 영역에서 인시던트를 선택 하는 경우 경고 메시지가 표시 될 수 있으며, 사용자는 해당 인시던트 나 사용자가 선택한 다른 사용자 (다른 사용자에 대 한 권한이 있는 경우에도)를 수정할 수 없습니다.

## <a name="configure-playbooks-for-mitigation"></a>완화를 위해 플레이 북 구성

[Playbooks](../../sentinel/tutorial-respond-threats-playbook.md) 는 경고가 트리거될 때 자동 완화에 사용할 수 있습니다. 이러한 플레이 북은 수동으로 실행 하거나 특정 경고가 트리거될 때 자동으로 실행 될 수 있습니다. 플레이 북은 보안 위협에 대 한 응답으로 작업을 수행 해야 하는 테 넌 트의 사용자에 따라 구성 된 응답 절차를 사용 하 여 관리 테 넌 트 또는 고객 테 넌 트에서 배포할 수 있습니다.

## <a name="create-cross-tenant-workbooks"></a>다중 테 넌 트 통합 문서 만들기

[Azure 센티널의 통합 문서를 Azure Monitor](../../sentinel/overview.md#workbooks) 하 여 연결 된 데이터 원본의 데이터를 시각화 하 고 모니터링 하 여 통찰력을 얻을 수 있습니다. Azure 센티널에서 기본 제공 통합 문서 템플릿을 사용 하거나 시나리오에 대 한 사용자 지정 통합 문서를 만들 수 있습니다.

관리 테 넌 트에 통합 문서를 배포 하 고 고객 테 넌 트 간에 데이터를 모니터링 하 고 쿼리 하는 규모에 맞는 대시보드를 만들 수 있습니다. 자세한 내용은 [작업 영역 간 모니터링](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks)을 참조 하세요. 일부 기능은 [여러 작업 영역에서 지원 되지 않습니다](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

해당 고객과 관련 된 시나리오에 대해 관리 하는 개별 테 넌 트에 직접 통합 문서를 배포할 수도 있습니다.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Azure 센티널 작업 영역에서 Log Analytics 및 구하기 쿼리를 실행 합니다.

검색 [쿼리](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting)를 포함 하 여 관리 테 넌 트에서 중앙에서 위협 검색을 위한 Log Analytics 쿼리를 만들고 저장 합니다. 이러한 쿼리는 Union 연산자와 workspace () 식을 사용 하 여 모든 고객의 Azure 센티널 작업 영역에서 실행할 수 있습니다. 자세한 내용은 [작업 영역 간 쿼리](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying)를 참조 하세요.

## <a name="use-automation-for-cross-workspace-management"></a>작업 영역 간 관리를 위한 자동화 사용

자동화를 사용 하 여 여러 Azure 센티널 작업 영역을 관리 하 고, [구하기 쿼리](../../sentinel/hunting.md), playbooks 및 통합 문서를 구성할 수 있습니다. 자세한 내용은 [자동화를 사용 하 여 작업 영역 간 관리](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation)를 참조 하세요.

일부 기능은 [현재 여러 작업 영역에서 지원 되지 않습니다](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="manage-security-of-office-365-environments"></a>Office 365 환경 보안 관리

Azure Lighthouse를 Azure 센티널과 함께 사용 하 여 테 넌 트 간의 Office 365 환경 보안을 관리 합니다. 먼저, Exchange 및 SharePoint (OneDrive 포함)의 사용자 및 관리 활동에 대 한 정보를 관리 되는 테 넌 트 내에서 Azure 센티널 작업 영역으로 수집 수 있도록 [관리 되는 테 넌 트에서 Office 365 데이터 커넥터를 사용 하도록 설정 해야 합니다](../../sentinel/connect-office-365.md) . 여기에는 작업을 수행한 사용자에 대 한 정보와 함께 파일 다운로드, 보낸 액세스 요청, 그룹 이벤트 변경, 사서함 작업 등의 동작에 대 한 세부 정보가 포함 됩니다. [Office 365 DLP 경고](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) 는 기본 제공 office 365 커넥터의 일부로도 지원 됩니다.

[MCAS (Microsoft Cloud App Security) 커넥터](../../sentinel/connect-cloud-app-security.md) 를 사용 하 여 경고를 스트림 하 고 Azure 센티널에 로그를 Cloud Discovery 수 있습니다. 이를 통해 클라우드 앱에 대 한 가시성을 확보 하 고 정교한 분석을 통해 사이버 위협 대를 식별 및 공격 하 고 데이터가 이동 하는 방식을 제어할 수 있습니다. MCAS의 활동 로그 [는 CEF (Common Event Format)를 사용 하 여 사용](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849)될 수 있습니다.

Office 365 데이터 커넥터를 설정한 후 통합 문서에서 데이터 보기 및 분석, 쿼리를 사용 하 여 사용자 지정 경고 만들기, 위협에 대응 하도록 플레이 북 구성 등의 테 넌 트 간 Azure 센티널 기능을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 센티널](../../sentinel/overview.md)에 대해 알아봅니다.
- [Azure 센티널 가격 책정 페이지](https://azure.microsoft.com/pricing/details/azure-sentinel/)를 검토 합니다.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.


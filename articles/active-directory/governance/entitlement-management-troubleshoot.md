---
title: 자격 관리 문제 해결-Azure AD
description: Azure Active Directory 자격 관리에 대 한 문제 해결에 도움이 되는 몇 가지 항목에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 216cff03ac6ce64dee9aae1e9daa4a86385eeb0e
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783334"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Azure AD 자격 관리 문제 해결

이 문서에서는 Azure AD (Azure Active Directory) 자격 관리의 문제를 해결 하기 위해 확인 해야 하는 몇 가지 항목을 설명 합니다.

## <a name="administration"></a>관리

* 권한 관리를 구성할 때 액세스 거부 메시지가 표시 되 고 전역 관리자 인 경우 디렉터리에 [Azure AD Premium P2 (또는 EMS E5) 라이선스가](entitlement-management-overview.md#license-requirements)있는지 확인 합니다.

* Access 패키지를 만들거나 볼 때 액세스 거부 메시지가 표시 되 고 사용자가 카탈로그 작성자 그룹의 멤버인 경우 첫 번째 액세스 패키지를 만들기 전에 [카탈로그를 만들어야](entitlement-management-catalog-create.md) 합니다.

## <a name="resources"></a>리소스

* 애플리케이션의 역할은 애플리케이션 자체에서 정의되며 Azure AD에서 관리됩니다. 응용 프로그램에 리소스 역할이 없으면 권한 부여 관리에서 사용자를 **기본 액세스** 역할에 할당 합니다.

    Azure Portal는 응용 프로그램으로 선택할 수 없는 서비스에 대 한 서비스 사용자를 표시할 수도 있습니다.  특히 **Exchange online** 및 **SharePoint online** 은 디렉터리에 리소스 역할이 있는 응용 프로그램이 아니라 서비스 이므로 액세스 패키지에 포함 될 수 없습니다.  대신 그룹 기반 라이선스를 사용하여 해당 서비스에 액세스해야 하는 사용자에게 적절한 라이선스를 설정합니다.

* 그룹이 액세스 패키지의 리소스가 되려면 Azure AD에서 수정할 수 있어야 합니다.  Azure AD에서 소유자 또는 구성원 특성을 변경할 수 없으므로 온-프레미스 Active Directory에서 시작된 그룹을 리소스로 할당할 수 없습니다.   Exchange Online에서 배포 그룹으로 시작된 그룹도 Azure AD에서 수정할 수 없습니다. 

* SharePoint Online 문서 라이브러리와 개별 문서를 리소스로 추가할 수 없습니다.  대신, [AZURE AD 보안 그룹](../fundamentals/active-directory-groups-create-azure-portal.md)을 만들고, 해당 그룹과 사이트 역할을 액세스 패키지에 포함 하 고, SharePoint Online에서 해당 그룹을 사용 하 여 문서 라이브러리나 문서에 대 한 액세스를 제어 합니다.

* 액세스 패키지를 사용하여 관리하려는 리소스에 이미 할당된 사용자가 있는 경우 해당 사용자에게 적절한 정책을 사용하여 액세스 패키지를 할당해야 합니다. 예를 들어 이미 사용자가 있는 그룹을 액세스 패키지에 포함하려는 경우가 있습니다. 그룹의 사용자가 계속 액세스해야 하는 경우 사용자에게 그룹에 대한 액세스 권한을 잃지 않도록 액세스 패키지에 대한 적절한 정책이 있어야 합니다. 사용자에게 해당 리소스를 포함하는 액세스 패키지를 요청하거나 사용자를 액세스 패키지에 직접 할당하는 방법으로 액세스 패키지를 할당할 수 있습니다. 자세한 내용은 [액세스 패키지에 대 한 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)을 참조 하세요.

* 팀의 구성원을 제거 하면 해당 구성원도 Microsoft 365 그룹에서 제거 됩니다. 팀 채팅 기능을 제거하면 작업이 지연될 수 있습니다. 자세한 내용은 [그룹 멤버 자격](/microsoftteams/office-365-groups#group-membership)을 참조 하세요.

* 디렉터리가 다중 지역에 대해 구성되지 않았는지 확인합니다. 권한 관리는 현재 SharePoint Online에 대한 다중 지역 위치를 지원하지 않습니다. SharePoint Online 사이트는 권한 관리로 제어되는 기본 지리적 위치에 있어야 합니다. 자세한 내용은 [OneDrive 및 SharePoint Online의 다중 지역 기능](/Office365/Enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-office-365)을 참조 하세요.

## <a name="access-packages"></a>액세스 패키지

* 액세스 패키지 또는 정책을 삭제 하 고 활성 할당 이라는 오류 메시지가 표시 되는 경우 할당 된 사용자가 표시 되지 않으면 최근에 삭제 된 사용자에 게 아직 할당이 있는지 여부를 확인 합니다. 사용자가 삭제 된 후 30 일 기간 동안 사용자 계정을 복원할 수 있습니다.   

## <a name="external-users"></a>외부 사용자

* 외부 사용자가 액세스 패키지에 대 한 액세스를 요청 하려는 경우 액세스 패키지에 대 한 **내 액세스 포털 링크** 를 사용 하 고 있는지 확인 합니다. 자세한 내용은 [액세스 패키지를 요청 하는 공유 링크를](entitlement-management-access-package-settings.md)참조 하세요. 외부 사용자가 **myaccess.microsoft.com** 를 방문 하 고 전체 내 액세스 포털 링크를 사용 하지 않는 경우 조직에 있지 않고 해당 조직에서 사용할 수 있는 액세스 패키지가 표시 됩니다.

* 외부 사용자가 액세스 패키지에 대 한 액세스를 요청 하거나 리소스에 액세스할 수 없는 경우 [외부 사용자에 대 한 설정을](entitlement-management-external-users.md#settings-for-external-users)확인 해야 합니다.

* 이전에 디렉터리에서 로그인하지 않은 새 외부 사용자가 SharePoint Online 사이트를 포함하는 액세스 패키지를 수신하는 경우 해당 계정이 SharePoint Online에서 프로비저닝될 때까지 해당 액세스 패키지는 완전히 전달되지 않음으로 표시됩니다. 설정 공유에 대 한 자세한 내용은 [SharePoint Online 외부 공유 설정 검토](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings)를 참조 하세요.

## <a name="requests"></a>요청

* 사용자가 액세스 패키지에 대 한 액세스를 요청 하려는 경우 액세스 패키지에 대 한 **내 액세스 포털 링크** 를 사용 해야 합니다. 자세한 내용은 [액세스 패키지를 요청 하는 공유 링크를](entitlement-management-access-package-settings.md)참조 하세요.

* in-private 또는 incognito 모드로 설정된 브라우저에서 내 액세스 포털을 열면 로그인 동작과 충돌할 수 있습니다. 내 액세스 포털을 방문하는 경우 브라우저를 in-private 또는 incognito 모드로 사용하지 않는 것이 좋습니다.

* 아직 디렉터리에 없는 사용자가 내 액세스 포털에 로그인하여 액세스 패키지를 요청하는 경우 해당 조직의 계정을 사용하여 인증해야 합니다. 조직 계정은 리소스 디렉터리 또는 액세스 패키지의 정책 중 하나에 포함된 디렉터리의 계정일 수 있습니다. 사용자 계정이 조직 계정이 아니거나 인증한 디렉터리가 정책에 포함되지 않은 경우에는 사용자에게 액세스 패키지가 표시되지 않습니다. 자세한 내용은 [액세스 패키지에 대 한 액세스 요청](entitlement-management-request-access.md)을 참조 하세요.

* 사용자가 리소스 디렉터리에 로그인하지 못하도록 차단된 경우에는 내 액세스 포털에서 액세스를 요청할 수 없습니다. 사용자가 액세스를 요청하려면 먼저 사용자의 프로필에서 로그인 차단을 제거해야 합니다. 로그인 블록을 제거 하려면 Azure Portal에서 **Azure Active Directory** **를 클릭 하 고 사용자를 클릭**한 다음 사용자를 클릭 하 고 **프로필**을 클릭 합니다. **설정** 섹션을 편집 하 고 **블록 로그인** 을 **아니요**로 변경 합니다. 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자의 프로필 정보 추가 또는 업데이트](../fundamentals/active-directory-users-profile-azure-portal.md)를 참조 하세요.  [Id 보호 정책](../identity-protection/howto-identity-protection-remediate-unblock.md)으로 인해 사용자가 차단 되었는지 여부도 확인할 수 있습니다.

* 내 액세스 포털에서 사용자가 요청자 이자 승인자 인 경우 **승인** 페이지에서 액세스 패키지에 대 한 요청이 표시 되지 않습니다. 이 동작은 의도적인 것으로, 사용자가 자신의 요청을 승인할 수 없습니다. 요청 중인 액세스 패키지에 정책에 구성된 추가 승인자가 있는지 확인합니다. 자세한 내용은 [액세스 패키지에 대 한 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)을 참조 하세요.

### <a name="view-a-requests-delivery-errors"></a>요청의 배달 오류 보기

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 보려는 요청을 선택 합니다.

    요청에 배달 오류가 있으면 요청 **상태가 배달 되지 않거나** **부분적으로 전달**됩니다.

    배달 오류가 있으면 요청 세부 정보 창에 배달 오류 수가 표시 됩니다.

1. 개수를 클릭 하면 요청의 모든 배달 오류가 표시 됩니다.

### <a name="reprocess-a-request"></a>요청 다시 처리

액세스 패키지 다시 처리 요청을 트리거한 후에 오류가 발생 하는 경우 시스템에서 요청을 다시 처리 하는 동안 기다려야 합니다. 시스템이 여러 시간 동안 다시 처리 되도록 시도 하므로이 시간 동안 다시 처리를 강제할 수 없습니다. 

**배달 실패** 또는 **부분적으로 배달** 된 상태와 1 주일 미만의 완료 된 날짜를 포함 하는 요청을 다시 처리할 수 있습니다. 그렇지 않으면 다시 **처리** 단추가 회색으로 표시 됩니다.

![다시 처리 단추가 회색으로 표시 됨](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- 평가판 기간 동안 오류가 해결 되 면 요청 상태가 **배달**중으로 변경 됩니다. 이 요청은 사용자의 추가 작업 없이 다시 처리 됩니다.

- 평가판 기간 동안 오류가 수정 되지 않은 경우 요청이 **배달** 되지 않거나 **부분적으로 배달**될 수 있습니다. 그런 다음 다시 **처리** 단추를 사용할 수 있습니다. 요청을 다시 처리 하는 데 7 일이 있습니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 다시 처리 하려는 요청을 클릭 합니다.

1. 요청 정보 창에서 **요청 다시 처리**를 클릭 합니다.

    ![실패 한 요청 다시 처리](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>보류 중인 요청 취소

아직 배달 되지 않았거나 배달이 실패 한 보류 중인 요청만 취소할 수 있습니다. 그렇지 않으면 **취소** 단추가 회색으로 표시 됩니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. **요청**을 클릭 합니다.

1. 취소 하려는 요청을 클릭 합니다.

1. 요청 정보 창에서 **요청 취소**를 클릭 합니다.

## <a name="multiple-policies"></a>여러 정책

* 자격 관리는 최소 권한 모범 사례를 따릅니다. 사용자가 적용 되는 여러 정책을 포함 하는 액세스 패키지에 대 한 액세스를 요청 하는 경우 권한 관리에는 보다 엄격한 특정 정책이 일반 정책 보다 우선 순위가 지정 되도록 하는 논리가 포함 됩니다. 정책이 일반적인 경우 권한 부여 관리에서 요청자에 게 정책을 표시 하지 않거나 더 엄격한 정책을 자동으로 선택할 수 있습니다.

* 예를 들어 두 정책이 요청자에 게 적용 되는 내부 직원에 대해 두 개의 정책이 있는 액세스 패키지를 생각해 보겠습니다. 첫 번째 정책은 요청자를 포함 하는 특정 사용자를 위한 것입니다. 두 번째 정책은 요청자가 멤버인 디렉터리의 모든 사용자를 위한 것입니다. 이 시나리오에서는 더 엄격 하므로 요청자에 대해 첫 번째 정책이 자동으로 선택 됩니다. 요청자에 게 두 번째 정책을 선택할 수 있는 옵션이 제공 되지 않았습니다.

* 여러 정책이 적용 되는 경우 자동으로 선택 되는 정책이 나 요청자에 게 표시 되는 정책은 다음 우선 순위 논리를 기반으로 합니다.

    | 정책 우선 순위 | 범위 |
    | --- | --- |
    | P1 | 디렉터리 또는 특정 연결 된 조직의 특정 사용자 및 그룹 |
    | P2 | 디렉터리의 모든 구성원 (게스트 제외) |
    | P3 | 디렉터리의 모든 사용자 (게스트 포함) 또는 연결 된 특정 조직 |
    | P4 | 모든 연결 된 조직 또는 모든 사용자 (연결 된 모든 조직 + 모든 새 외부 사용자) |
    
    높은 우선 순위 범주에 속하는 정책이 있으면 낮은 우선 순위 범주가 무시 됩니다. 동일한 우선 순위의 여러 정책이 요청자에 게 표시 되는 방법에 대 한 예는 [정책 선택](entitlement-management-request-access.md#select-a-policy)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [외부 사용자를 위한 액세스 제어](entitlement-management-external-users.md)
- [자격 관리에서 사용자가 액세스 하는 방법에 대 한 보고서 보기](entitlement-management-reports.md)
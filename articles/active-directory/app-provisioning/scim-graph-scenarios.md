---
title: SCIM, Microsoft Graph 및 Azure AD를 사용 하 여 사용자를 프로 비전 하 고 데이터를 사용 하 여 앱 보강
description: SCIM과 Microsoft Graph를 함께 사용 하 여 사용자를 프로 비전 하 고 필요한 데이터를 사용 하 여 응용 프로그램을 보강 합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: b69e2c9b12b2db34f3eb70e54d2c6aede6b54784
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235505"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM 및 Microsoft Graph를 함께 사용 하 여 사용자를 프로 비전 하 고 필요한 데이터를 사용 하 여 응용 프로그램 보강

**대상 사용자:** 이 문서는 Azure Active Directory (Azure AD)와 통합할 응용 프로그램을 빌드하는 개발자를 대상으로 합니다. Zoom, ServiceNow 및 DropBox와 같은 Azure AD와 이미 통합 된 응용 프로그램을 사용 하려는 경우이 문서를 건너뛰고 응용 프로그램별 [자습서](../saas-apps/tutorial-list.md) 를 검토 하거나 [프로 비전 서비스의 작동 방식을](./how-provisioning-works.md)검토할 수 있습니다.

**일반적인 시나리오**

Azure AD는 프로 비전에 사용할 수 있는 기본 제공 서비스와 응용 프로그램을 빌드하는 확장 가능한 플랫폼을 제공 합니다. 의사 결정 트리는 개발자가 [Scim](https://aka.ms/scimoverview) 및 [Microsoft Graph](/graph/overview) 를 사용 하 여 프로 비전을 자동화 하는 방법을 간략하게 설명 합니다. 

> [!div class="checklist"]
> * 내 응용 프로그램에서 자동으로 사용자 만들기
> * 더 이상 액세스할 수 없을 때 응용 프로그램에서 사용자를 자동으로 제거
> * 프로 비전을 위해 여러 id 공급자와 응용 프로그램 통합
> * 팀, Outlook 및 Office와 같은 Microsoft 서비스의 데이터를 사용 하 여 응용 프로그램을 보강 합니다.
> * Azure AD에서 사용자 및 그룹을 자동으로 만들고, 업데이트 하 고, 삭제 Active Directory

![SCIM 그래프 의사 결정 트리](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>시나리오 1: 내 앱에서 자동으로 사용자 만들기
현재 IT 관리자는 수동으로 사용자 계정을 만들거나 CSV 파일을 내 응용 프로그램에 주기적으로 업로드 하 여 사용자를 프로 비전 합니다. 이 프로세스는 고객에 게 시간이 많이 걸리고 내 응용 프로그램의 채택을 저하 시킵니다. 사용자를 만들기 위한 이름, 메일, userPrincipalName 등의 기본 사용자 정보만 있으면 됩니다. 

**권장 사항**: 
* 고객이 다양 한 IdPs를 사용 하 고 서로 통합 될 동기화 엔진을 유지 관리 하지 않으려는 경우 SCIM 규격 [/사용자](https://aka.ms/scimreferencecode) 끝점을 지원 합니다. 고객은이 끝점을 사용 하 여 Azure AD 프로 비전 서비스와 통합 하 고 액세스 해야 할 때 자동으로 사용자 계정을 만들 수 있습니다. 끝점은 한 번에 빌드할 수 있으며 모든 IdPs와 호환 됩니다. SCIM을 사용 하 여 사용자를 만드는 방법에 대 한 아래의 예제 요청을 확인 하세요.
* Azure AD의 사용자 개체와 Microsoft의 다른 데이터에서 사용자 데이터를 찾은 경우 사용자 프로 비전을 위한 SCIM 끝점을 빌드하고 Microsoft Graph를 호출 하 여 나머지 데이터를 가져오는 것이 좋습니다. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>시나리오 2: 내 앱에서 자동으로 사용자 제거
응용 프로그램을 사용 하는 고객은 보안에 중점을 두 며 직원 들이 더 이상 필요 하지 않은 경우 계정을 제거 하기 위한 거 버 넌 스 요구 응용 프로그램에서 프로 비전 해제를 자동화 하려면 어떻게 해야 하나요?

**권장 사항:** SCIM 규격/사용자 끝점을 지원 합니다. Azure AD 프로 비전 서비스는 사용자에 게 더 이상 액세스 권한이 없는 경우 사용 하지 않도록 설정 하 고 삭제 하는 요청을 보냅니다. 사용자 비활성화 및 삭제를 모두 지 원하는 것이 좋습니다. 비활성화 및 삭제 요청은 다음과 같이 표시 됩니다. 

사용자 사용 안 함
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
사용자 삭제
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>시나리오 3: 내 앱에서 그룹 멤버 자격 관리 자동화
응용 프로그램은 다양 한 리소스에 액세스 하기 위해 그룹을 사용 하 고, 고객은 Azure AD에 있는 그룹을 다시 사용 하려고 합니다. Azure AD에서 그룹을 가져오는 방법 및 멤버 자격 변경으로 계속 업데이트 하려면 어떻게 해야 하나요?  

**권장 사항:** SCIM 규격/그룹 [끝점](https://aka.ms/scimreferencecode)을 지원 합니다. Azure AD 프로 비전 서비스는 응용 프로그램에서 그룹을 만들고 멤버 자격 업데이트를 관리 하는 것을 담당 합니다. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>시나리오 4: 팀, Outlook 및 OneDrive와 같은 Microsoft 서비스의 데이터로 내 앱 보강
내 응용 프로그램은 Microsoft 팀에 기본 제공 되며 메시지 데이터에 의존 합니다. 또한 OneDrive의 사용자에 대 한 파일을 저장 합니다. 이러한 서비스 및 Microsoft의 데이터를 사용 하 여 응용 프로그램을 보강 하려면 어떻게 해야 하나요?

**권장 사항:** Microsoft 데이터에 액세스할 수 있는 진입점은 [Microsoft Graph](/graph/) 입니다. 각 워크 로드는 필요한 데이터를 사용 하 여 Api를 노출 합니다. 위의 시나리오에 대해 [Scim 프로 비전](./use-scim-to-provision-users-and-groups.md) 과 함께 Microsoft graph를 사용할 수 있습니다. SCIM을 사용 하 여 graph를 호출 하는 동안 기본 사용자 특성을 응용 프로그램에 프로 비전 하 여 필요한 다른 데이터를 가져올 수 있습니다. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>시나리오 5: 팀, Outlook 및 Azure AD와 같은 Microsoft 서비스의 변경 내용 추적
팀과 Outlook 메시지에 대 한 변경 내용을 추적 하 고 실시간으로 응답할 수 있어야 합니다. 이러한 변경 내용을 내 응용 프로그램에 푸시 하려면 어떻게 해야 하나요?

**권장 사항:** Microsoft Graph는 다양 한 리소스에 대 한 [변경 알림](/graph/webhooks) 및 [변경 내용 추적](/graph/delta-query-overview) 기능을 제공 합니다. 변경 알림에 대 한 다음 제한 사항에 유의 하세요.
- 이벤트 수신기가 이벤트를 승인 하지만 어떤 이유로 든 동작 하지 않으면 이벤트가 손실 될 수 있습니다.
- 변경 내용이 수신 되는 순서는 시간순이 아닐 수도 있습니다.
- 변경 알림은 항상 위의 이유로 [리소스 데이터](/graph/webhooks-with-resource-data) 를 포함 하지 않습니다. 개발자는 동기화 시나리오에 대 한 변경 추적과 함께 변경 알림을 사용 하는 경우가 많습니다. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>시나리오 6: Azure AD에서 사용자 및 그룹 프로 비전
내 응용 프로그램은 Azure AD에서 고객이 필요로 하는 사용자에 대 한 정보를 만듭니다. 채용 직원, 사용자를 위한 전화 번호를 만드는 커뮤니케이션 앱 또는 Azure AD에서 중요 한 데이터를 생성 하는 다른 앱을 관리 하는 것 보다 HR 응용 프로그램 일 수 있습니다. Azure AD의 사용자 레코드를 해당 데이터로 채울 어떻게 할까요? 있나요? 

**권장 사항** Microsoft graph는 사용자를 Azure AD에 프로 비전 하기 위해 현재와 통합할 수 있는/사용자 및/Tgroups 끝점을 노출 합니다. Azure Active Directory는 해당 사용자를 Active Directory에 다시 작성 하는 것을 지원 하지 않습니다. 

> [!NOTE]
> Microsoft에는 Workday 및 SuccessFactors와 같은 HR 응용 프로그램에서 데이터를 가져오는 프로 비전 서비스가 있습니다. 이러한 통합은 Microsoft에서 빌드하고 관리 합니다. 새 HR 응용 프로그램을 서비스에 온 보 딩 하기 위해 [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)에서 요청할 수 있습니다. 

## <a name="related-articles"></a>관련 문서

- [동기화 Microsoft Graph 설명서 검토](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [사용자 지정 SCIM 앱을 Azure AD와 통합](use-scim-to-provision-users-and-groups.md)
---
title: Azure Active Directory Connect Health 작업
description: 이 문서에서는 Azure AD Connect Health를 배포한 후 수행할 수 있는 추가 작업에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdcf5ded2f2cf49048b70dedb11f25e67766e938
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177621"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health 작업
이 항목에서는 Azure AD(Azure Active Directory) Connect Health를 사용하여 수행할 수 있는 다양한 작업에 대해 설명합니다.

## <a name="enable-email-notifications"></a>메일 알림 사용
ID 인프라가 정상 상태가 아님을 나타내는 경고가 표시되면 메일 알림을 보내도록 Azure AD Connect Health 서비스를 구성할 수 있습니다. 이 작업은 경고가 생성된 경우 및 해결된 경우에 수행됩니다.

![Azure AD Connect Health 메일 알림 설정 스크린샷](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> 메일 알림은 기본적으로 사용됩니다.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Azure AD Connect Health 메일 알림을 사용하도록 설정하려면
1. 메일 알림을 받으려는 서비스의 **경고** 블레이드를 엽니다.
2. 작업 모음에서 **알림 설정**을 클릭합니다.
3. 메일 알림 스위치에서 **켜기**를 선택합니다.
4. 모든 전역 관리자가 메일 알림을 수신하도록 하려는 경우 확인란을 선택합니다.
5. 다른 메일 주소로 메일 알림을 받으려는 경우 **추가 메일 받는 사람** 상자에 주소를 지정합니다. 이 목록에서 메일 주소를 제거하려면 항목을 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.
6. 변경을 완료하려면 **저장**을 클릭합니다. 변경 내용은 저장한 후에만 적용됩니다.

>[!NOTE] 
> 백 엔드 서비스에서 동기화 요청을 처리 하는 데 문제가 있는 경우이 서비스는 오류에 대 한 세부 정보와 함께 테 넌 트의 관리 담당자 전자 메일 주소로 알림 전자 메일을 보냅니다. 특정 한 경우에는 이러한 메시지의 양이 너무 크므로 이러한 메시지를 전송 하는 방식을 변경 하는 고객의 의견을 들었습니다. 
>
> 발생할 때마다 모든 동기화 오류에 대 한 메시지를 전송 하는 대신 백 엔드 서비스가 반환한 모든 오류의 일일 다이제스트를 전송 합니다. 이를 통해 고객은 보다 효율적인 방식으로 이러한 오류를 처리 하 고 중복 된 오류 메시지 수를 줄일 수 있습니다.
>
> 2020 년 1 월 15 일에이 변경 내용이 구현 될 예정입니다.

## <a name="delete-a-server-or-service-instance"></a>서버 또는 서비스 인스턴스 삭제

>[!NOTE] 
> 삭제 단계에는 Azure AD 프리미엄 라이선스가 필요합니다.

서버를 모니터링 대상에서 제거하려는 경우도 있습니다. Azure AD Connect Health 서비스에서 서버를 제거하기 위해 알아야 할 사항은 다음과 같습니다.

서버를 삭제하는 경우 다음 사항에 유의하세요.

* 이 작업을 수행하면 해당 서버에서 추가 데이터 수집이 중지됩니다. 모니터링 서비스에서 이 서버가 제거됩니다. 이 작업을 수행한 후에는 이 서버에 대한 새 경고나 모니터링 데이터, 사용량 현황 분석 데이터를 볼 수 없습니다.
* 이 작업을 수행해도 서버에서 Health Agent가 제거되지는 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 서버의 Health Agent와 관련된 오류가 표시될 수도 있습니다.
* 이 작업을 수행해도 이 서버에서 이미 수집된 데이터는 삭제되지 않습니다. 해당 데이터는 Azure 데이터 보존 정책에 따라 삭제됩니다.
* 이 작업을 수행한 후 동일한 서버를 다시 모니터링하려는 경우 이 서버에서 Health Agent를 제거한 후 다시 설치해야 합니다.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Azure AD Connect Health 서비스에서 서버 삭제

>[!NOTE] 
> 삭제 단계에는 Azure AD 프리미엄 라이선스가 필요합니다.

AD FS(Active Directory Federation Services)용 Azure AD Connect Health 및 Azure AD Connect(동기화):

1. **서버 목록** 블레이드에서 제거할 서버 이름을 선택하여 **서버** 블레이드를 엽니다.
2. **서버** 블레이드의 작업 모음에서 **삭제**를 클릭합니다.
![Azure AD Connect Health 서버 삭제 스크린샷](./media/how-to-connect-health-operations/DeleteServer2.png)
3. 확인 상자에 서버 이름을 입력하여 확인합니다.
4. **삭제**를 클릭합니다.

Azure Active Directory Domain Services용 Azure AD Connect Health:

1. **도메인 컨트롤러** 대시보드를 엽니다.
2. 제거할 도메인 컨트롤러를 선택합니다.
3. 작업 모음에서 **선택한 항목 삭제**를 클릭합니다.
4. 서버 삭제 작업을 확인합니다.
5. **삭제**를 클릭합니다.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Azure AD Connect Health 서비스에서 서비스 인스턴스 삭제
서비스 인스턴스를 제거하려는 경우도 있습니다. Azure AD Connect Health 서비스에서 서비스 인스턴스를 제거하기 위해 알아야 할 사항은 다음과 같습니다.

서비스 인스턴스를 삭제하는 경우 다음 사항에 유의하세요.

* 이 작업을 수행하면 현재 서비스 인스턴스가 모니터링 서비스에서 제거됩니다.
* 이 작업을 수행해도 이 서비스 인스턴스의 일부로 모니터링된 서버에서 Health Agent가 제거되지는 않습니다. 이 단계를 수행하기 전에 Health Agent를 제거하지 않은 경우 서버의 Health Agent와 관련된 오류가 표시될 수도 있습니다.
* 이 서비스 인스턴스의 모든 데이터는 Azure 데이터 보존 정책에 따라 삭제됩니다.
* 이 작업을 수행한 후 서비스를 모니터링하려는 경우 모든 서버에서 Health Agent를 제거한 후 다시 설치합니다. 이 작업을 수행한 후 동일한 서버를 다시 모니터링하려는 경우 해당 서버에서 Health Agent를 제거한 후 다시 설치하고 등록합니다.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Azure AD Connect Health 서비스에서 서비스 인스턴스를 삭제하려면
1. **서비스 목록** 블레이드에서 제거하려는 서비스 식별자(팜 이름)를 선택하여 **서비스** 블레이드를 엽니다. 
2. **서비스** 블레이드의 작업 모음에서 **삭제**를 클릭합니다. 
![Azure AD Connect Health 서비스 삭제 스크린샷](./media/how-to-connect-health-operations/DeleteServer.png)
3. 확인 상자에 서비스 이름(예: sts.contoso.com)을 입력하여 확인합니다.
4. **삭제**를 클릭합니다.
   <br><br>

[//]: # (RBAC 섹션의 시작)
## <a name="manage-access-with-azure-rbac"></a>Azure RBAC를 사용 하 여 액세스 관리
Azure AD Connect Health에 대 한 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/role-assignments-portal.md) 는 전역 관리자가 아닌 사용자 및 그룹에 대 한 액세스를 제공 합니다. Azure RBAC는 의도 된 사용자 및 그룹에 역할을 할당 하 고 디렉터리 내에서 전역 관리자를 제한 하는 메커니즘을 제공 합니다.

### <a name="roles"></a>역할
Azure AD Connect Health는 다음과 같은 기본 제공 역할을 지원합니다.

| 역할 | 사용 권한 |
| --- | --- |
| 소유자 |소유자는 *액세스를 관리*(예: 사용자 또는 그룹에 역할 할당)하고, 포털에서 *모든 정보를 확인*(예: 경고 보기)하며, Azure AD Connect Health 내에서 *설정을 변경*(예: 메일 알림)할 수 있습니다. <br>기본적으로 Azure AD 전역 관리자에게 이 역할이 할당되며, 이 설정은 변경할 수 없습니다. |
| 참가자 |참가자는 포털에서 *모든 정보를 확인*(예: 경고 보기)하고, Azure AD Connect Health 내에서 *설정을 변경*(예: 메일 알림)할 수 있습니다. |
| 판독기 |구독자는 Azure AD Connect Health 내의 포털에서 *모든 정보를 확인*(예: 경고 보기)할 수 있습니다. |

다른 모든 역할(예: 사용자 액세스 관리자 또는 DevTest 랩 사용자)은 포털 환경에서 사용할 수 있는 경우에도 Azure AD Connect Health 내의 액세스 권한에 영향을 주지 않습니다.

### <a name="access-scope"></a>액세스 범위
Azure AD Connect Health는 다음 두 수준에서 액세스 관리를 지원합니다.

* **모든 서비스 인스턴스**: 대부분의 경우에서 권장되는 경로입니다. Azure AD Connect Health에서 모니터링되는 모든 역할 유형에서 모든 서비스 인스턴스(예: AD FS 팜)에 대한 액세스를 제어합니다.
* **서비스 인스턴스**: 역할 유형에 따라 또는 서비스 인스턴스별로 액세스를 분리해야 하는 경우도 있습니다. 이 경우 서비스 인스턴스 수준에서 액세스를 관리할 수 있습니다.  

사용 권한은 최종 사용자가 디렉터리 또는 서비스 인스턴스 수준에서 액세스 권한을 가진 경우에 부여됩니다.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>사용자 또는 그룹에 Azure AD Connect Health에 대한 액세스 허용
다음 단계에서는 액세스를 허용하는 방법을 보여 줍니다.
#### <a name="step-1-select-the-appropriate-access-scope"></a>1단계: 적절한 액세스 범위 선택
Azure AD Connect Health 내에서 *모든 서비스 인스턴스* 수준으로 사용자에게 액세스를 허용하려면 Azure AD Connect Health에서 주 블레이드를 엽니다.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>2단계: 사용자 및 그룹 추가, 역할 할당
1. **구성** 섹션에서 **사용자**를 클릭합니다.<br>
   ![Azure AD Connect Health 리소스 사이드바 스크린샷](./media/how-to-connect-health-operations/startRBAC.png)
2. **추가**를 선택합니다.
3. **역할 선택** 창에서 역할(예: **소유자**)을 선택합니다.<br>
   ![Azure AD Connect Health 및 Azure RBAC 구성 메뉴의 스크린샷](./media/how-to-connect-health-operations/RBAC_add.png)
4. 사용자 또는 그룹을 대상으로 한 이름 또는 식별자를 입력합니다. 동시에 하나 이상의 사용자 또는 그룹을 선택할 수 있습니다. **선택**을 클릭합니다.
   ![Azure AD Connect Health 및 Azure RBAC 역할 목록의 스크린샷](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. **확인**을 선택합니다.<br>
6. 역할 할당이 완료되면 사용자 및 그룹이 목록에 표시됩니다.<br>
   ![Azure AD Connect Health 및 Azure RBAC와 새 사용자가 강조 표시 된 스크린샷](./media/how-to-connect-health-operations/RBAC_user_list.png)

이제 할당된 역할에 따라 나열된 사용자 및 그룹에 액세스 권한이 있습니다.

> [!NOTE]
> * 전역 관리자는 항상 모든 작업에 대한 모든 권한을 갖지만 전역 권리자 계정은 앞의 목록에 표시되지 않습니다.
> * 사용자 초대 기능은 Azure AD Connect Health 내에서 지원되지 않습니다.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>3단계: 사용자 또는 그룹을 사용하여 블레이드 위치 공유
1. 사용 권한을 할당하면 사용자가 [여기](https://aka.ms/aadconnecthealth)로 이동하여 Azure AD Connect Health에 액세스할 수 있습니다.
2. 블레이드에서 사용자는 블레이드 또는 블레이드의 서로 다른 부분을 대시보드에 고정할 수 있습니다. **대시보드에 고정** 아이콘을 클릭하면 됩니다.<br>
   ![고정 아이콘이 강조 표시 된 Azure AD Connect Health 및 Azure RBAC pin 블레이드 스크린샷](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> 구독자 역할이 할당된 사용자는 Azure Marketplace에서 Azure AD Connect Health 확장을 가져올 수 없습니다. 사용자가 가져오기에 필요한 "만들기" 작업을 수행할 수 없습니다. 사용자는 앞의 링크로 이동하여 블레이드에 액세스할 수 있습니다. 이후 사용의 경우 사용자는 대시보드에 블레이드를 고정할 수 있습니다.
>
>

### <a name="remove-users-or-groups"></a>사용자 또는 그룹 제거
Azure AD Connect Health 및 Azure RBAC에 추가 된 사용자 또는 그룹을 제거할 수 있습니다. 사용자 또는 그룹을 마우스 오른쪽 단추로 클릭하고 **제거**를 선택하면 됩니다.<br>
![제거 강조 표시 된 Azure AD Connect Health 및 Azure RBAC의 스크린샷](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (RBAC 섹션의 끝)

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [에이전트 설치 Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [AD FS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adfs.md)
* [동기화에 Azure AD Connect Health 사용](how-to-connect-health-sync.md)
* [AD DS와 함께 Azure AD Connect Health 사용](how-to-connect-health-adds.md)
* [Azure AD Connect Health FAQ](reference-connect-health-faq.md)
* [Azure AD Connect Health 버전 내역](reference-connect-health-version-history.md)

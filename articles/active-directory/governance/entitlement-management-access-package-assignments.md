---
title: Azure AD 자격 관리에서 액세스 패키지에 대 한 할당 보기, 추가 및 제거-Azure Active Directory
description: Azure Active Directory 자격 관리에서 액세스 패키지에 대 한 할당을 확인, 추가 및 제거 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120437"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 할당 보기, 추가 및 제거

Azure AD 자격 관리에서 패키지, 정책 및 상태에 대 한 액세스 권한이 할당 된 사용자를 볼 수 있습니다. 액세스 패키지에 적절 한 정책이 있는 경우 사용자를 액세스 패키지에 직접 할당할 수도 있습니다. 이 문서에서는 액세스 패키지에 대 한 할당을 확인, 추가 및 제거 하는 방법을 설명 합니다.

## <a name="view-who-has-an-assignment"></a>할당 된 사용자 보기

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 활성 할당 목록을 보려면 **할당** 을 클릭 합니다.

    ![액세스 패키지에 대 한 할당 목록](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 특정 할당을 클릭 하 여 추가 세부 정보를 확인 합니다.

1. 모든 리소스 역할이 제대로 프로 비전 되지 않은 할당 목록을 보려면 필터 상태를 클릭 하 고 **배달**중을 선택 합니다.

    **요청** 페이지에서 사용자의 해당 요청을 찾아 배달 오류에 대 한 추가 세부 정보를 볼 수 있습니다.

1. 만료 된 할당을 보려면 필터 상태를 클릭 하 고 **만료 됨**을 선택 합니다.

1. 필터링 된 목록의 CSV 파일을 다운로드 하려면 **다운로드**를 클릭 합니다.

### <a name="viewing-assignments-programmatically"></a>프로그래밍 방식으로 할당 보기

Microsoft Graph를 사용 하 여 액세스 패키지에서 할당을 검색할 수도 있습니다.  위임 된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할의 사용자는 API를 호출 하 여 [accessPackageAssignments를 나열할](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)수 있습니다.

## <a name="directly-assign-a-user"></a>사용자를 직접 할당

경우에 따라 사용자가 액세스 패키지를 요청 하는 프로세스를 진행할 필요가 없도록 특정 사용자를 액세스 패키지에 직접 할당할 수 있습니다. 사용자를 직접 할당 하려면 액세스 패키지에 관리자 직접 할당을 허용 하는 정책이 있어야 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당**을 클릭 합니다.

1. **새 할당** 을 클릭 하 여 액세스 패키지에 사용자 추가를 엽니다.

    ![할당-액세스 패키지에 사용자 추가](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. **사용자 추가** 를 클릭 하 여이 액세스 패키지를 할당 하려는 사용자를 선택 합니다.

1. **정책 선택** 목록에서 사용자의 향후 요청 및 수명 주기를 관리 하 고 추적할 정책을 선택 합니다. 선택한 사용자에 게 다른 정책 설정이 있도록 하려면 **새 정책 만들기** 를 클릭 하 여 새 정책을 추가 합니다.

1. 선택한 사용자의 할당을 시작할 날짜와 시간을 설정 하 고 종료 합니다. 종료 날짜를 제공 하지 않으면 정책의 수명 주기 설정이 사용 됩니다.

1. 필요에 따라 레코드 유지를 위한 직접 할당에 대 한 근거를 제공 합니다.

1. **추가** 를 클릭 하 여 선택한 사용자를 액세스 패키지에 직접 할당 합니다.

    몇 분 후에 **새로 고침** 을 클릭 하 여 할당 목록에서 사용자를 확인 합니다.

### <a name="directly-assigning-users-programmatically"></a>프로그래밍 방식으로 사용자 직접 할당

또한 Microsoft Graph를 사용 하 여 사용자를 액세스 패키지에 직접 할당할 수 있습니다.  위임 된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할의 사용자는 API를 호출 하 여 [accessPackageAssignmentRequest를 만들](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)수 있습니다.

## <a name="remove-an-assignment"></a>할당 제거

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당**을 클릭 합니다.
 
1. 액세스 패키지에서 제거 하려는 할당의 사용자 옆에 있는 확인란을 클릭 합니다. 

1. 왼쪽 창의 맨 위에 있는 **제거** 단추를 클릭 합니다. 
 
    ![할당-액세스 패키지에서 사용자 제거](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    할당을 제거 했다는 알림이 표시 됩니다. 

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대 한 변경 요청 및 설정](entitlement-management-access-package-request-policy.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)

---
title: Azure Portal에서 사용자 할당 관리 id 관리-Azure AD
description: 사용자 할당 관리 id에 역할을 만들고, 나열 하 고, 삭제 하 고, 할당 하는 방법에 대 한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5045c81f280d9d7687a466aed2e918d21eec697
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266390"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당

Azure 리소스에 대 한 관리 id는 Azure Active Directory에서 관리 id를 사용 하 여 Azure 서비스를 제공 합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure Portal를 사용 하 여 사용자 할당 관리 id에 역할을 만들거나 나열, 삭제 또는 할당 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

1. 사용자가 할당한 관리 ID를 만들려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 되는 id*를 입력 하 고 **서비스**에서 **관리 id**를 클릭 합니다.
3. **추가**를 클릭하고 **사용자가 할당한 관리 ID 만들기** 창 아래에서 다음 필드에 값을 입력합니다.
    - **구독**: 구독을 선택 하 여 사용자 할당 관리 id를 만듭니다.
    - **리소스 그룹**: 사용자 할당 관리 id를 만들 리소스 그룹을 선택 하거나 **새로 만들기** 를 클릭 하 여 새 리소스 그룹을 만듭니다.
    - **지역**: 사용자 할당 관리 id (예: **미국 서 부**)를 배포할 지역을 선택 합니다.
    - **이름**: 사용자 할당 관리 id의 이름 (예: UAI1)입니다.
    ![사용자 할당 관리 ID 만들기](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. **검토 + 상자** 를 클릭 하 여 변경 내용을 검토 합니다.
5. **만들기**를 클릭합니다.

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

1. 사용자 할당 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, 서비스 아래에서 **관리 ID**를 클릭합니다.
3. 구독의 사용자 할당 관리 ID 목록이 반환됩니다.  사용자 할당 관리 ID 세부 정보를 보려면 해당 이름을 클릭합니다.

![사용자 할당 관리 ID 나열](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 ID를 삭제해도 ID가 할당된 VM이나 리소스에서는 ID가 제거되지 않습니다.  사용자 할당 ID를 VM에서 제거하려는 경우 [VM에서 사용자 할당 관리 ID 제거](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)를 참조하세요.

1. 사용자 할당 관리 ID를 삭제하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 사용자 할당 관리 ID를 선택하고 **삭제**를 클릭합니다.
3. 확인 상자 아래에서 **예**를 선택합니다.

![사용자 할당 관리 ID 삭제](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID에 역할 할당 

사용자 할당 관리 ID에 역할을 할당하려면 계정에 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할 할당이 필요합니다.

1. 사용자 할당 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 검색 상자에 *관리 ID*를 입력하고, 서비스 아래에서 **관리 ID**를 클릭합니다.
3. 구독의 사용자 할당 관리 ID 목록이 반환됩니다.  역할을 할당하려는 사용자 할당 관리 ID를 선택합니다.
4. **액세스 제어(IAM)**, **역할 할당 추가**를 차례로 선택합니다.

   ![사용자 할당 관리 ID 시작](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. 역할 할당 추가 블레이드에서 다음 값을 구성한 후 **저장**을 클릭 합니다.
   - **역할** - 할당할 역할
   - **다음에 대한 액세스 할당** - 사용자 할당 관리 ID를 할당할 리소스
   - **선택** - 액세스를 할당할 구성원
   
   ![사용자 할당 관리 ID IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)
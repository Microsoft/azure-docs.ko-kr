---
title: PIM용 Microsoft Graph API(미리 보기) - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)용 Microsoft Graph API(미리 보기)를 사용하는 방법에 대한 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95b0b026b75b9b77c94451245ac4f18d487fc2e4
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783521"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>Privileged Identity Management용 Microsoft Graph API(미리 보기)

Azure Active Directory용 [Microsoft Graph API](/graph/overview)를 사용하여 Privileged Identity Management 작업을 수행할 수 있습니다. 이 문서에서는 Privileged Identity Management용 Microsoft Graph API를 사용하기 위한 중요한 개념을 설명합니다.

Microsoft Graph API에 대한 자세한 내용은 [Azure AD Privileged Identity Management API 참고 자료](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)를 참조하세요.

> [!IMPORTANT]
> Microsoft Graph의 /베타 버전 아래에 있는 API는 미리 보기 상태이며 변경될 가능성이 있습니다. 프로덕션 애플리케이션에서는 이러한 API의 사용이 지원되지 않습니다.

## <a name="required-permissions"></a>필요한 사용 권한

Privileged Identity Management용 Microsoft Graph API를 호출하려면 다음 권한이 **하나 이상** 있어야 합니다.

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>권한 설정

Privileged Identity Management용 Microsoft Graph API를 호출하는 애플리케이션에는 필요한 권한이 있어야 합니다. 필요한 권한을 지정하는 가장 쉬운 방법은 [Azure AD 동의 프레임워크](../develop/consent-framework.md)를 사용하는 것입니다.

### <a name="set-permissions-in-graph-explorer"></a>Graph Explorer에서 권한 설정

Graph Explorer를 사용하여 호출을 테스트하는 경우 도구에서 사용 권한을 지정할 수 있습니다.

1. [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에 글로벌 관리자로 로그인합니다.

1. **권한 수정**을 클릭합니다.

    ![Graph Explorer - 권한 수정](./media/pim-apis/graph-explorer.png)

1. 포함하려는 권한 옆에 있는 확인란을 선택합니다. `PrivilegedAccess.ReadWrite.AzureAD`는 아직 Graph Explorer에서 사용할 수 없습니다.

    ![Graph Explorer - 권한 수정](./media/pim-apis/graph-explorer-modify-permissions.png)

1. **권한 수정**을 클릭하여 권한 변경 사항을 적용합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD Privileged Identity Management API 참고 자료](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)
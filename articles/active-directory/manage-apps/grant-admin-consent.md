---
title: 응용 프로그램에 테넌트 전체 관리자 동의 부여-Azure AD
description: 응용 프로그램에 로그인 할 때 최종 사용자에 게 동의 여부를 묻지 않도록 응용 프로그램에 테넌트 차원의 동의를 부여 하는 방법을 알아봅니다.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480919"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>응용 프로그램에 대 한 테넌트 전체 관리자 동의 부여

응용 프로그램에 대 한 테넌트 전체 관리자 동의를 부여 하 여 사용자 환경을 간소화 하는 방법을 알아봅니다. 이 문서에서는이를 수행 하는 다양 한 방법을 제공 합니다. 이러한 방법은 Azure AD(Azure Active Directory) 테넌트의 모든 최종 사용자에게 적용됩니다.

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

테넌트 전체 관리자 동의를 부여 하려면 [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [응용 프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)으로 로그인 해야 합니다.

> [!IMPORTANT]
> 응용 프로그램에 테넌트 전체 관리자 동의가 부여 되 면 사용자 할당을 요구 하도록 구성 되지 않은 한 모든 사용자가 앱에 로그인 할 수 있습니다. 응용 프로그램에 로그인 할 수 있는 사용자를 제한 하려면 사용자 할당을 요구 하 고 응용 프로그램에 사용자 또는 그룹을 할당 합니다. 자세한 내용은 [사용자 및 그룹을 할당하는 메서드](methods-for-assigning-users-and-groups.md)를 참조하세요.

> [!WARNING]
> 응용 프로그램에 대 한 테넌트 전체 관리자 동의를 부여 하면 앱 및 앱의 게시자에 게 조직의 데이터에 대 한 액세스 권한이 부여 됩니다. 동의를 부여 하기 전에 응용 프로그램에서 요청 하는 권한을 신중 하 게 검토 합니다.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure Portal에서 관리자 동의를 부여 합니다.

### <a name="grant-admin-consent-in-enterprise-apps"></a>엔터프라이즈 앱에서 관리자 동의를 부여 합니다.

응용 프로그램이 테넌트에 이미 프로 비전 된 경우 *엔터프라이즈 응용 프로그램* 을 통해 테넌트 전체 관리자 동의를 부여할 수 있습니다. 예를 들어 응용 프로그램에 하나 이상의 사용자가 이미 동의한 경우 테넌트에서 앱을 프로 비전 할 수 있습니다. 자세한 내용은 [Azure Active Directory에 응용 프로그램을 추가 하는 방법 및 이유](../develop/active-directory-how-applications-are-added.md)를 참조 하세요.

**엔터프라이즈 응용 프로그램**에 나열 된 앱에 대 한 테넌트 전체 관리자 동의를 부여 하려면:

1. [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [응용 프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2. **Azure Active Directory** 다음 **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 테넌트 전체 관리자 동의를 부여 하려는 응용 프로그램을 선택 합니다.
4. **권한** 을 선택 하 고 **관리자 동의 부여**를 클릭 합니다.
5. 응용 프로그램에 필요한 권한을 신중 하 게 검토 합니다.
6. 응용 프로그램에 필요한 사용 권한에 동의 하는 경우 동의를 부여 합니다. 그렇지 않은 경우 **취소** 를 클릭 하거나 창을 닫습니다.

### <a name="grant-admin-consent-in-app-registrations"></a>앱 등록에서 관리자 동의를 부여 합니다.

조직에서 개발한 응용 프로그램의 경우 또는 Azure AD 테넌트에 직접 등록 된 응용 프로그램의 경우 Azure Portal에서 **앱 등록** 의 테넌트 전체 관리자 동의를 부여할 수도 있습니다.

**앱 등록**에서 테넌트 전체 관리자 동의를 부여 하려면 다음을 수행 합니다.

1. [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [응용 프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)또는 [클라우드 응용 프로그램 관리자 권한](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
2. **Azure Active Directory** 선택한 다음 **앱 등록**합니다.
3. 테넌트 전체 관리자 동의를 부여 하려는 응용 프로그램을 선택 합니다.
4. **API 권한** 을 선택한 다음 **관리자 동의 부여**를 클릭 합니다.
5. 응용 프로그램에 필요한 권한을 신중 하 게 검토 합니다.
6. 응용 프로그램에 필요한 사용 권한에 동의 하는 경우 동의를 부여 합니다. 그렇지 않은 경우 **취소** 를 클릭 하거나 창을 닫습니다.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의를 부여 하기 위한 URL 생성

위에서 설명한 방법 중 하나를 사용 하 여 테넌트 전체 관리자 동의를 부여 하는 경우 Azure Portal에서 창이 열리며,이를 통해 테넌트 전체 관리자 동의를 확인 합니다. 응용 프로그램의 클라이언트 ID (응용 프로그램 ID 라고도 함)를 알고 있으면 동일한 URL을 작성 하 여 테넌트 전체 관리자 동의를 부여할 수 있습니다.

테넌트 전체 관리자 동의 URL은 다음 형식을 따릅니다.

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

각 항목이 나타내는 의미는 다음과 같습니다.

* `{client-id}`는 응용 프로그램의 클라이언트 ID (앱 ID 라고도 함)입니다.
* `{tenant-id}` 조직의 테넌트 ID 또는 확인 된 도메인 이름입니다.

언제나 처럼 동의를 부여 하기 전에 응용 프로그램에서 요청 하는 권한을 신중 하 게 검토 합니다.

## <a name="next-steps"></a>다음 단계

[최종 사용자가 응용 프로그램에 동의 하는 방식 구성](configure-user-consent.md)

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[Microsoft id 플랫폼에서 사용 권한 및 동의](../develop/active-directory-v2-scopes.md)

[StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)

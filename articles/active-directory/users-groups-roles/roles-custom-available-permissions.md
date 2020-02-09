---
title: 사용 가능한 사용자 지정 관리자 역할 권한-Azure AD | Microsoft Docs
description: Id 관리를 위임 하기 위한 사용자 지정 관리자 역할 권한
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025158"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Azure Active Directory의 응용 프로그램 등록 하위 형식 및 권한

이 문서에는 Azure Active Directory (Azure AD)에서 사용자 지정 역할 정의에 대해 현재 사용 가능한 앱 등록 권한이 포함 되어 있습니다.

## <a name="permissions-for-managing-single-directory-applications"></a>단일 디렉터리 응용 프로그램 관리 권한

사용자 지정 역할에 대 한 사용 권한을 선택할 때 단일 디렉터리 응용 프로그램을 관리 하기 위해 액세스 권한을 부여할 수 있는 옵션이 있습니다. 단일 디렉터리 응용 프로그램은 응용 프로그램이 등록 된 Azure AD 조직의 사용자만 사용할 수 있습니다. 단일 디렉터리 응용 프로그램은 지원 되는 **계정 유형이** "이 조직 디렉터리의 계정 전용 계정"으로 설정 된 것으로 정의 됩니다. Graph API에서 단일 디렉터리 응용 프로그램은 signInAudience 속성을 "AzureADMyOrg"로 설정 합니다.

단일 디렉터리 응용 프로그램만 관리할 수 있는 액세스 권한을 부여 하려면 하위 형식 **응용 프로그램. myOrganization**에서 아래 권한을 사용 합니다. 예를 들어 microsoft. directory/applications. myOrganization/기본/업데이트입니다.

일반 용어 하위 유형, 권한 및 속성 집합의 의미에 대 한 설명은 [사용자 지정 역할 개요](roles-custom-overview.md) 를 참조 하세요. 다음 정보는 응용 프로그램 등록에만 적용 됩니다.

### <a name="create-and-delete"></a>만들기 및 삭제

응용 프로그램 등록을 만들 수 있는 기능을 부여 하는 데 사용할 수 있는 두 가지 권한은 각기 다른 동작입니다.

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft. directory/응용 프로그램/createAsOwner

이 권한을 할당 하면 만들어진 앱 등록의 첫 번째 소유자로 작성자가 추가 되 고 생성 된 앱 등록이 작성자의 250 생성 된 개체 할당량에 따라 계산 됩니다.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft. 디렉터리/응용 프로그램/만들기

이 권한을 할당 하면 만들어진 앱 등록의 첫 번째 소유자로 작성자가 추가 되지 않으며 만들어진 앱 등록이 작성자의 250 created 개체 할당량에 대해 계산 되지 않습니다. 디렉터리 수준 할당량이 적중 될 때까지 담당자가 앱 등록을 만들 수 없도록 하는 것이 없으므로이 사용 권한을 신중 하 게 사용 합니다. 두 사용 권한이 모두 할당 된 경우이 권한이 우선적으로 적용 됩니다.

두 권한이 모두 할당 되 면/create 권한이 우선적으로 적용 됩니다. /CreateAsOwner 권한은 자동으로 작성자를 첫 번째 소유자로 추가 하지 않지만 Graph Api 또는 PowerShell cmdlet을 사용 하는 경우 앱 등록을 만드는 동안 소유자를 지정할 수 있습니다.

만들기 권한 **새 등록** 명령에 대 한 액세스 권한을 부여 합니다.

[이러한 권한은 새 등록 포털 명령에 대 한 액세스 권한을 부여 합니다.](./media/roles-create-custom/new-custom-role.png)

앱 등록을 삭제 하는 기능을 부여 하는 데 사용할 수 있는 두 가지 권한이 있습니다.

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

하위 형식에 관계 없이 앱 등록을 삭제할 수 있는 기능을 부여 합니다. 즉, 단일 테넌트 및 다중 테넌트 응용 프로그램입니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft. directory/응용 프로그램. myOrganization/삭제

조직 또는 단일 테넌트 응용 프로그램 (myOrganization 하위 형식)의 계정에만 액세스할 수 있는 앱 등록을 삭제할 수 있는 기능을 부여 합니다.

![이러한 권한은 앱 등록 삭제 명령에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 만들기 권한이 포함 된 역할을 할당 하는 경우 디렉터리 범위에서 역할 할당을 수행 해야 합니다. 리소스 범위에서 할당 된 create 권한은 앱 등록을 만드는 기능을 부여 하지 않습니다.

### <a name="read"></a>읽기

조직의 모든 구성원 사용자는 기본적으로 앱 등록 정보를 읽을 수 있습니다. 그러나 게스트 사용자 및 응용 프로그램 서비스 사용자는 사용할 수 없습니다. 게스트 사용자 또는 응용 프로그램에 역할을 할당할 계획인 경우 적절 한 읽기 권한을 포함 해야 합니다.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft. directory/applications/allProperties/read

자격 증명과 같은 상황에서 읽을 수 없는 속성 외부에서 단일 테넌트 및 다중 테넌트 응용 프로그램의 모든 속성을 읽을 수 있습니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft. directory/응용 프로그램. myOrganization/allProperties/read

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/applications/allProperties/read와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft. 디렉터리/응용 프로그램/소유자/읽기

단일 테넌트 및 다중 테넌트 응용 프로그램에서 소유자 속성을 읽을 수 있는 기능을 부여 합니다. 응용 프로그램 등록 소유자 페이지의 모든 필드에 대 한 액세스 권한을 부여 합니다.

![이 사용 권한은 앱 등록 소유자 페이지에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft. 디렉터리/응용 프로그램/표준/읽기

표준 응용 프로그램 등록 속성을 읽을 수 있는 액세스 권한을 부여 합니다. 여기에는 응용 프로그램 등록 페이지의 속성이 포함 됩니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft. directory/응용 프로그램. myOrganization/표준/읽기

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/applications/standard/read와 동일한 사용 권한을 부여 합니다.

### <a name="update"></a>업데이트

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft. directory/응용 프로그램/m 속성/업데이트

단일 디렉터리 및 다중 디렉터리 응용 프로그램의 모든 속성을 업데이트할 수 있습니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft. directory/응용 프로그램. myOrganization/allProperties/update

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/applications/allProperties/update와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

단일 디렉터리 및 다중 디렉터리 응용 프로그램에서 지원 되는 계정 유형 (signInAudience) 속성을 업데이트 하는 기능입니다.

![이 권한은 인증 페이지에서 앱 등록 지원 계정 유형 속성에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft. directory/응용 프로그램. myOrganization/사용자/업데이트

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/applications/사용자/업데이트와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

단일 테넌트 및 다중 테넌트 응용 프로그램에서 회신 URL, 로그 아웃 URL, 암시적 흐름 및 게시자 도메인 속성을 업데이트할 수 있습니다. 지원 되는 계정 유형을 제외한 응용 프로그램 등록 인증 페이지의 모든 필드에 대 한 액세스 권한을 부여 합니다.

![앱 등록 인증에 대 한 액세스 권한을 부여 하지만 지원 되는 계정 유형은 지원 하지 않습니다.](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft. directory/응용 프로그램. myOrganization/인증/업데이트

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/응용 프로그램/인증/업데이트와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

단일 테넌트 및 다중 테넌트 응용 프로그램의 이름, 로고, 홈페이지 URL, 서비스 약관 URL 및 개인정보 처리 방침 URL 속성을 업데이트 하는 기능입니다. 응용 프로그램 등록 브랜딩 페이지의 모든 필드에 대 한 액세스 권한을 부여 합니다.

![이 사용 권한은 앱 등록 브랜딩 페이지에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft. directory/응용 프로그램. myOrganization/기본/업데이트

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/applications/basic/update와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

단일 테넌트 및 다중 테넌트 응용 프로그램에서 인증서 및 클라이언트 암호 속성을 업데이트 하는 기능입니다. 응용 프로그램 등록 인증서 & 암호 페이지의 모든 필드에 대 한 액세스 권한을 부여 합니다.

![이 권한은 앱 등록 인증서 & 암호 페이지에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft. directory/응용 프로그램. myOrganization/자격 증명/업데이트

는 단일 디렉터리 응용 프로그램에 대해서만 microsoft. directory/응용 프로그램/자격 증명/업데이트와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

단일 테넌트 및 다중 테넌트의 owner 속성을 업데이트 하는 기능입니다. 응용 프로그램 등록 소유자 페이지의 모든 필드에 대 한 액세스 권한을 부여 합니다.

![이 사용 권한은 앱 등록 소유자 페이지에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft. directory/응용 프로그램. myOrganization/소유자/업데이트

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/응용 프로그램/소유자/업데이트와 동일한 권한을 부여 합니다.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

단일 테넌트 및 다중 테넌트 응용 프로그램에 대해 위임 된 권한, 응용 프로그램 권한, 권한 있는 클라이언트 응용 프로그램, 필요한 권한 및 승인 속성을 업데이트할 수 있습니다. 는 동의를 수행할 수 있는 기능을 부여 하지 않습니다. 응용 프로그램 등록 API 권한의 모든 필드에 대 한 액세스 권한을 부여 하 고 API 페이지를 표시 합니다.

![이 사용 권한은 앱 등록 API 사용 권한 페이지에 대 한 액세스 권한을 부여 합니다.](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![이 권한은 앱 등록에 대 한 액세스 권한을 부여 하 여 API 페이지를 노출 합니다.](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft. directory/응용 프로그램. myOrganization/사용 권한/업데이트

는 단일 테넌트 응용 프로그램에 대해서만 microsoft. directory/응용 프로그램/사용 권한/업데이트와 동일한 권한을 부여 합니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal, AZURE AD PowerShell 및 Graph API](roles-create-custom.md) 를 사용 하 여 사용자 지정 역할을 만듭니다.
- [사용자 지정 역할에 대 한 할당 보기](roles-view-assignments.md)

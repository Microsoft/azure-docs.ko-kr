---
title: Azure AD 특성 매핑 사용자 지정 | Microsoft Docs
description: Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 842e5c4771fcac0b8011dda1df11c646bf8f070c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207199"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>사용자 프로 비전 특성 사용자 지정-Azure Active Directory에서 SaaS 응용 프로그램에 대 한 매핑

Microsoft Azure AD는 Salesforce, G Suite 및 기타와 같은 타사 SaaS 응용 프로그램에 대 한 사용자 프로비저닝을 지원 합니다. 타사 SaaS 응용 프로그램에 대해 사용자 프로 비전을 사용 하도록 설정 하면 Azure Portal는 특성 매핑을 통해 해당 특성 값을 제어 합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성 된 특성 및 특성 매핑 집합이 있습니다. 일부 앱은 그룹과 같은 다른 유형의 개체를 사용자와 함께 관리 합니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존 특성 매핑을 변경 또는 삭제 하거나 새 특성 매핑을 만들 수 있습니다.

## <a name="editing-user-attribute-mappings"></a>사용자 특성 매핑 편집

사용자 프로 비전의 **매핑** 기능에 액세스 하려면 다음 단계를 수행 합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인 합니다.
1. 왼쪽 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다. 갤러리에서 추가 된 앱을 포함 하 여 구성 된 모든 앱의 목록이 표시 됩니다.
1. 앱을 선택 하 여 보고서를 보고 앱 설정을 관리할 수 있는 앱 관리 창을 로드 합니다.
1. **프로 비전** 을 선택 하 여 선택한 앱에 대 한 사용자 계정 프로 비전 설정을 관리 합니다.
1. **매핑** 을 확장 하 여 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 보고 편집 합니다. 대상 응용 프로그램에서 지 원하는 경우이 섹션에서 필요에 따라 그룹 및 사용자 계정 프로 비전을 구성할 수 있습니다.

   ![매핑을 사용 하 여 사용자 특성 보기 및 편집](./media/customize-application-attributes/21.png)

1. 관련 **특성 매핑** 화면을 열려면 **매핑** 구성을 선택 합니다. 일부 특성 매핑은 SaaS 응용 프로그램이 제대로 작동 하는 데 필요 합니다. 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.

   ![특성 매핑을 사용 하 여 앱에 대 한 특성 매핑 구성](./media/customize-application-attributes/22.png)

   이 스크린샷에서는 Salesforce에서 관리 되는 개체의 **Username** 특성이 연결 된 Azure Active Directory 개체의 **userPrincipalName** 값으로 채워지는 것을 볼 수 있습니다.

1. 기존 **특성 매핑을** 선택 하 여 **특성 편집** 화면을 엽니다. 여기에서 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 편집할 수 있습니다.

   ![특성 편집을 사용 하 여 사용자 특성 편집](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해

특성 매핑으로 타사 SaaS 애플리케이션에서 특성이 채워지는 법을 제어합니다.
4 가지의 다른 매핑 형식이 지원됩니다.

- **직접** – 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.
- **상수** – 대상 특성이 지정 된 특정 문자열로 채워집니다.
- **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다.
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)을 참조하세요.
- **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있는 경우에는 사용자가 지정한 기본값으로 채워집니다.

이러한 네 가지 기본 형식과 함께 사용자 지정 특성 매핑은 선택적 **기본값** 할당의 개념을 지원 합니다. 기본 값 할당은 Azure AD 또는 대상 개체에 값이 없는 경우 대상 특성을 값으로 채워야 합니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.

### <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서는 특성 매핑 형식 속성을 이미 소개 했습니다.
이 속성과 함께 특성 매핑은 다음 특성도 지원 합니다.

- **원본 특성** - 원본 시스템의 사용자 특성(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성(예: ServiceNow).
- **Null (선택 사항) 인 경우 기본값** 은 원본 특성이 null 인 경우 대상 시스템에 전달 될 값입니다. 이 값은 사용자를 만들 때만 프로 비전 됩니다. 기존 사용자를 업데이트할 때 "null 인 경우 기본값"이 프로 비전 되지 않습니다. 예를 들어 대상 시스템의 모든 기존 사용자를 특정 직함 (원본 시스템에서 null 인 경우)으로 프로 비전 하려는 경우 다음 [식을](../app-provisioning/functions-for-customizing-application-data.md)사용할 수 있습니다. Switch (IsPresent ([jobTitle]), "DefaultValue", "True", [jobTitle]) 원본 시스템에서 null 인 경우 "기본값"을 프로 비전 할 항목으로 바꾸어야 합니다. 
- **이 특성을 사용 하 여 개체 일치** – 원본 및 대상 시스템 간에 사용자를 고유 하 게 식별 하는 데이 매핑을 사용할지 여부를 지정 합니다. 일반적으로 대상 응용 프로그램의 사용자 이름 필드에 매핑되는 Azure AD의 userPrincipalName 또는 mail 특성에 대해 설정 됩니다.
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 여러 개가 있는 경우이 필드에 정의 된 순서 대로 평가 됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.
- **이 매핑 적용**
  - **항상** – 사용자 만들기 및 업데이트 작업 모두에이 매핑을 적용 합니다.
  - **만드는 동안에만** -이 매핑은 사용자 생성 작업에만 적용 됩니다.

## <a name="matching-users-in-the-source-and-target--systems"></a>원본 및 대상 시스템의 사용자 일치
Azure AD 프로 비전 서비스는 "최적의" 시나리오 (사용자가 대상 시스템에서 종료 하지 않음) 및 "brownfield" 시나리오 (사용자가 대상 시스템에 이미 있는 경우)에 배포할 수 있습니다. 두 시나리오를 모두 지원 하기 위해 프로 비전 서비스는 일치 하는 특성의 개념을 사용 합니다. 일치 특성을 사용 하면 원본에서 사용자를 고유 하 게 식별 하 고 대상의 사용자와 일치 하는 방법을 결정할 수 있습니다. 배포 계획의 일부로 원본 및 대상 시스템에서 사용자를 고유 하 게 식별 하는 데 사용할 수 있는 특성을 식별 합니다. 참고 사항:

- **일치 하는 특성은 고유 해야 합니다.** 고객은 userPrincipalName, 메일 또는 개체 ID와 같은 특성을 일치 하는 특성으로 사용 하는 경우가 많습니다.
- **여러 특성을 일치 하는 특성으로 사용할 수 있습니다.** 사용자를 일치 시킬 때 평가할 여러 특성을 정의 하 고, 해당 사용자가 평가 되는 순서 (UI에서 일치 하는 우선 순위로 정의 됨)를 정의할 수 있습니다. 예를 들어 세 개의 특성을 일치 하는 특성으로 정의 하 고 처음 두 특성을 평가한 후 사용자가 고유 하 게 일치 하는 경우 서비스는 세 번째 특성을 평가 하지 않습니다. 서비스는 지정 된 순서 대로 일치 하는 특성을 평가 하 고 일치 항목이 발견 되 면 계산을 중지 합니다.  
- **원본 및 대상의 값이 정확 하 게 일치 하지** 않아도 됩니다. 대상의 값은 원본에 있는 값의 몇 가지 간단한 함수 일 수 있습니다. 따라서 소스와 대상의 emailAddress 특성을 가질 수 있으며 일부 문자를 일부 상수 값으로 대체 하는 emailAddress 특성의 함수를 사용 하 여 일치 시킬 수 있습니다.  
- **특성의 조합을 기반으로 하는 일치가 지원 되지 않습니다.** 대부분의 응용 프로그램에서는 두 가지 속성을 기반으로 하는 쿼리를 지원 하지 않습니다. 따라서 특성의 조합을 기준으로 일치 시킬 수 없습니다. 다른 속성 뒤의 단일 속성을 평가할 수 있습니다.
- **모든 사용자에 게 하나 이상의 일치 하는 특성에 대 한 값이 있어야 합니다.** 일치 하는 특성을 하나 정의 하는 경우 모든 사용자에 게 원본 시스템의 해당 특성 값이 있어야 합니다. 예를 들어 userPrincipalName을 일치 하는 특성으로 정의 하는 경우 모든 사용자에 게 userPrincipalName가 있어야 합니다. 일치 하는 특성 (예: extensionAttribute1 및 메일)을 여러 개 정의 하는 경우 모든 사용자가 일치 하는 특성을 가질 필요가 없습니다. 사용자 한 명에 게는 extensionAttribute1 있지만 메일은 없지만 다른 사용자는 메일을 포함할 수 있지만 extensionAttribute1는 없습니다. 
- **대상 응용 프로그램은 일치 하는 특성에 대 한 필터링을 지원 해야 합니다.** 응용 프로그램 개발자는 해당 사용자 또는 그룹 API에서 특성의 하위 집합에 대 한 필터링을 허용 합니다. 갤러리의 응용 프로그램의 경우 기본 특성 매핑이 대상 응용 프로그램의 API에서 필터링을 지 원하는 특성에 대 한 것을 확인 합니다. 대상 응용 프로그램에 대해 일치 하는 기본 특성을 변경 하는 경우 타사 API 설명서를 참조 하 여 특성을 필터링 할 수 있는지 확인 합니다.  

## <a name="editing-group-attribute-mappings"></a>그룹 특성 매핑 편집

ServiceNow, Box 및 G Suite와 같은 선택한 수의 응용 프로그램은 그룹 개체 및 사용자 개체를 프로 비전 하는 기능을 지원 합니다. 그룹 개체에는 그룹 멤버와 함께 표시 이름 및 전자 메일 별칭과 같은 그룹 속성이 포함 될 수 있습니다.

![프로 비전 된 그룹 및 사용자 개체가 있는 ServiceNow를 보여 주는 예제](./media/customize-application-attributes/24.png)

**매핑**아래에서 그룹 매핑을 선택 하 고 **특성 매핑** 화면에서 **사용** 을 원하는 옵션으로 설정 하 여 그룹 프로 비전을 선택적으로 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

그룹 개체의 일부로 프로비전된 특성은 이전에 설명한 사용자 개체와 동일한 방식으로 사용자 지정할 수 있습니다. 

> [!TIP]
> 그룹 개체(속성 및 멤버) 프로비전은 애플리케이션에 [그룹을 할당](../manage-apps/assign-user-or-group-access-portal.md)하는 것과 별개의 개념입니다. 애플리케이션에 그룹을 할당할 수 있지만 그룹에 포함된 사용자 개체만 프로비전할 수 있습니다. 할당에서 그룹을 사용하기 위해 전체 그룹 개체를 프로비전할 필요는 없습니다.

## <a name="editing-the-list-of-supported-attributes"></a>지원되는 특성 목록 편집

지정된 애플리케이션에 지원되는 사용자 특성은 미리 구성됩니다. 대부분의 응용 프로그램의 사용자 관리 Api는 스키마 검색을 지원 하지 않습니다. 따라서 Azure AD 프로 비전 서비스는 응용 프로그램을 호출 하 여 지원 되는 특성 목록을 동적으로 생성할 수 없습니다.

그러나 일부 응용 프로그램은 사용자 지정 특성을 지원 하 고 Azure AD 프로 비전 서비스는 사용자 지정 특성을 읽고 쓸 수 있습니다. Azure Portal에 정의를 입력 하려면 **특성 매핑** 화면 아래쪽에 있는 **고급 옵션 표시** 확인란을 선택 하 고 앱 **에 대 한 특성 목록 편집** 을 선택 합니다.

특성 목록의 사용자 지정을 지원하는 애플리케이션 및 시스템은 다음과 같습니다.

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory([Azure AD Graph API 기본 특성](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) 및 사용자 지정 디렉터리 확장이 지원됩니다.)
- [SCIM 2.0](https://tools.ietf.org/html/rfc7643)을 지원하는 앱([코어 스키마](https://tools.ietf.org/html/rfc7643)에 정의된 특성을 추가해야 함)

> [!NOTE]
> 애플리케이션과 시스템의 스키마를 사용자 지정했으며, 사용자 지정 특성이 정의된 방식에 대한 실무 지식이 있는 관리자일 경우에만 지원되는 특성 목록을 편집하는 것이 좋습니다. 애플리케이션이나 시스템에서 제공하는 API 및 개발자 도구를 잘 알아야 하는 경우가 많습니다.

지원되는 특성 목록을 편집할 때는 다음과 같은 속성이 제공됩니다.

- **이름** - 대상 개체의 스키마에 정의된 특성의 시스템 이름입니다.
- **형식** -대상 개체의 스키마에 정의 된 대로 특성에 저장 되는 데이터 형식으로, 다음 형식 중 하나일 수 있습니다.
  - *이진* - 특성에 이진 데이터가 포함됩니다.
  - *부울* - 특성에 True 또는 False 값이 포함됩니다.
  - *날짜/시간* - 특성에 날짜 문자열이 포함됩니다.
  - *정수* - 특성에 정수가 포함됩니다.
  - *참조* - 특성에 대상 애플리케이션의 다른 테이블에 저장된 값을 참조하는 ID가 포함됩니다.
  - *문자열* - 특성에 텍스트 문자열이 포함됩니다.
- **기본 키 여부** -특성이 대상 개체의 스키마에서 기본 키 필드로 정의 되는지 여부입니다.
- **필수 여부** -대상 응용 프로그램 또는 시스템에서 특성을 채워야 하는지 여부입니다.
- **다중 값 여부** -특성에서 다중 값을 지원 하는지 여부입니다.
- **대소문자 구분 여부** -특성 값이 대/소문자 구분 방식으로 평가 되는지 여부입니다.
- **API 식** -특정 프로 비전 커넥터 (예: Workday)에 대 한 설명서에서 작업을 수행 하도록 지시 하지 않는 한를 사용 하지 마세요.
- 참조 된 **개체 특성** -참조 형식 특성인 경우이 메뉴에서 특성에 연결 된 값을 포함 하는 대상 응용 프로그램의 테이블 및 특성을 선택할 수 있습니다. 예를 들어 "Department"라는 특성의 저장된 값이 별도의 "Departments" 테이블에 있는 개체를 참조하는 경우 "Departments.Name"을 선택합니다. 지정 된 응용 프로그램에 대해 지원 되는 참조 테이블 및 기본 ID 필드는 미리 구성 되어 있으며 현재 Azure Portal를 사용 하 여 편집할 수 없지만 [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)를 사용 하 여 편집할 수 있습니다.

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>SCIM 규격 응용 프로그램에 사용자 지정 확장 특성 프로 비전
SCIM RFC는 핵심 사용자 및 그룹 스키마를 정의 하는 동시에 스키마를 확장 하 여 응용 프로그램의 요구를 충족할 수 있도록 합니다. SCIM 응용 프로그램에 사용자 지정 특성을 추가 하려면 다음을 수행 합니다.
   1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인 하 고, **엔터프라이즈 응용 프로그램**을 선택 하 고, 응용 프로그램을 선택 하 고, **프로 비전**을 선택 합니다.
   2. **매핑**아래에서 사용자 지정 특성을 추가 하려는 개체 (사용자 또는 그룹)를 선택 합니다.
   3. 페이지 맨 아래에서 **고급 옵션 표시**를 선택 합니다.
   4. **AppName에 대 한 특성 목록 편집을**선택 합니다.
   5. 특성 목록의 맨 아래에 제공 된 필드에 사용자 지정 특성에 대 한 정보를 입력 합니다. 그런 다음 **특성 추가**를 선택 합니다.

SCIM 응용 프로그램의 경우 특성 이름은 아래 예제에 표시 된 패턴을 따라야 합니다. "CustomExtensionName" 및 "CustomAttribute"는 응용 프로그램의 요구 사항에 따라 사용자 지정할 수 있습니다. 예를 들어 urn: ietf: params: scim: 스키마: extension: 2.0: CustomExtensionName: CustomAttribute 또는 urn: ietf: params: scim: 스키마: extension: CustomExtensionName: 2.0: CustomAttributeName: value

이러한 지침은 SCIM 사용 응용 프로그램에만 적용 됩니다. ServiceNow 및 Salesforce와 같은 응용 프로그램은 SCIM을 사용 하 여 Azure AD와 통합 되지 않으므로 사용자 지정 특성을 추가할 때이 특정 네임 스페이스를 요구 하지 않습니다.

사용자 지정 특성은 참조 특성 또는 다중 값 특성 일 수 없습니다. 사용자 지정 다중 값 확장 특성은 현재 갤러리의 응용 프로그램에 대해서만 지원 됩니다.  
 
**확장 특성이 있는 사용자의 예제 표현:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>SCIM 앱에 역할 프로 비전
다음 단계를 사용 하 여 응용 프로그램에 사용자의 역할을 프로 비전 합니다. 아래 설명은 사용자 지정 SCIM 응용 프로그램에만 적용 됩니다. Salesforce 및 ServiceNow와 같은 갤러리 응용 프로그램의 경우 미리 정의 된 역할 매핑을 사용 합니다. 아래 글머리 기호는 AppRoleAssignments 특성을 응용 프로그램에서 예상 하는 형식으로 변환 하는 방법을 설명 합니다.

- Azure AD의 appRoleAssignment을 응용 프로그램의 역할에 매핑 하려면 [식을](../app-provisioning/functions-for-customizing-application-data.md)사용 하 여 특성을 변환 해야 합니다. 정규식을 사용 하 여 역할 세부 정보를 구문 분석 하지 않고도 appRoleAssignment 특성을 role 특성에 **직접 매핑할 수 없습니다** . 

- **SingleAppRoleAssignment** 
  - **사용 시기:** 단일 역할을 사용자에 게 프로 비전 하 고 주 역할을 지정 하려면 SingleAppRoleAssignment을 사용 합니다. 
  - **구성 방법:** 위에 설명 된 단계를 사용 하 여 특성 매핑 페이지로 이동 하 고 SingleAppRoleAssignment을 사용 하 여 roles 특성에 매핑합니다. 선택할 수 있는 역할 특성에는 다음 세 가지가 있습니다. (역할 [primary eq "True"]. 표시, 역할 [primary eq "True]. 형식 및 역할 [primary eq" True "]. value). 모든 역할 특성을 매핑에 포함 하도록 선택할 수 있습니다. 둘 이상의를 포함 하려면 새 매핑을 추가 하 고 대상 특성으로 포함 하면 됩니다.  
  
  ![SingleAppRoleAssignment 추가](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **고려해 야 할 사항**
    - 사용자에 게 여러 역할이 할당 되지 않았는지 확인 합니다. 프로 비전 되는 역할을 보장할 수 없습니다.
    
  - **예제 출력** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **사용 시기:** AppRoleAssignmentsComplex 식을 사용 하 여 사용자에 대 한 여러 역할을 프로 비전 합니다. 
  - **구성 방법:** 위의 설명에 따라 지원 되는 특성 목록을 편집 하 여 역할에 대 한 새 특성을 포함 합니다. 
  
    ![역할 추가](./media/customize-application-attributes/add-roles.png)<br>

    그런 다음 AppRoleAssignmentsComplex 식을 사용 하 여 아래 이미지에 표시 된 것 처럼 사용자 지정 역할 특성에 매핑합니다.

    ![AppRoleAssignmentsComplex 추가](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **고려해 야 할 사항**
    - 모든 역할은 primary = false로 프로 비전 됩니다.
    - 게시물에는 역할 형식이 포함 되어 있습니다. PATCH 요청에 형식이 포함 되어 있지 않습니다. POST 및 PATCH 요청에서 형식을 전송 하기 위해 노력 하 고 있습니다.
    
  - **예제 출력** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>다중 값 특성 프로 비전
PhoneNumbers 및 전자 메일 등의 특정 특성은 다양 한 형식의 전화 번호 또는 전자 메일을 지정 해야 할 수 있는 다중 값 특성입니다. 다중 값 특성에 대해 아래 식을 사용 합니다. 특성 유형을 지정 하 고 값에 대 한 해당 Azure AD 사용자 특성에 매핑할 수 있습니다. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>기본 특성 및 특성 매핑 복원

기존 매핑을 다시 시작 하 고 기본 상태로 다시 설정 해야 하는 경우 **기본 매핑 복원** 확인란을 선택 하 고 구성을 저장할 수 있습니다. 이렇게 하면 응용 프로그램이 응용 프로그램 갤러리에서 Azure AD 테 넌 트에 방금 추가 된 것 처럼 모든 매핑 및 범위 지정 필터가 설정 됩니다.

이 옵션을 선택 하면 프로 비전 서비스가 실행 되는 동안 모든 사용자를 다시 동기화 하는 것이 효과적입니다.

> [!IMPORTANT]
> 이 옵션을 호출 하기 전에 **프로 비전 상태** 를 **Off** 로 설정 하는 것이 좋습니다.

## <a name="what-you-should-know"></a>알아야 할 사항

- Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다.
- 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다.
- 권장 되는 최선의 방법은 특성 매핑의 연속 변경 횟수를 최소로 유지 하는 것입니다.
- 사진을 동기화 할 형식을 지정할 수 없으므로 지금은 앱에 프로 비전 할 사진 특성을 추가할 수 없습니다. [사용자 의견](https://feedback.azure.com/forums/169401-azure-active-directory) 에 기능을 요청할 수 있습니다.
- Is소프트 Deleted 특성은 종종 응용 프로그램에 대 한 기본 매핑의 일부입니다. Issoft 삭제는 다음 네 가지 시나리오 중 하나에서 true 일 수 있습니다. 응용 프로그램에서 할당 되지 않아서 사용자가 범위를 벗어났습니다. 범위 지정 필터를 충족 하지 않거나 사용자가 Azure AD에서 일시 삭제 되었거나 속성 AccountEnabled가 false로 설정 되어 있기 때문에 사용자가 범위를 벗어났습니다.  사용자의 경우). 
- Azure AD 프로 비전 서비스에서 null 값 프로 비전을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
- [특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)

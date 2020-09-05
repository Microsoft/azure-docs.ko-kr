---
title: Azure AD 특성 매핑 사용자 지정 | Microsoft Docs
description: Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 04/03/2019
ms.author: kenwith
ms.openlocfilehash: 5040fca85857cd131731d67c543c08fb1114ccee
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235227"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비저닝 특성 매핑 사용자 지정

Microsoft Azure AD는 Salesforce, G Suite 등과 같은 타사 SaaS 애플리케이션에 대한 사용자 프로비저닝을 지원합니다. 타사 SaaS 애플리케이션에 대해 사용자 프로비저닝을 사용하도록 설정하면 Azure Portal은 특성 매핑을 통해 해당 특성 값을 제어합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 및 특성 매핑 세트가 있습니다. 일부 앱은 사용자 외에도 다른 유형의 개체(예: 그룹)를 관리합니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다.

## <a name="editing-user-attribute-mappings"></a>사용자 특성 매핑 편집

다음 단계를 수행하여 사용자 프로비저닝의 **매핑** 기능에 액세스합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인합니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 갤러리에서 추가된 앱을 포함하여 구성된 모든 앱 목록이 표시됩니다.
1. 앱을 선택하여 보고서를 보고 앱 설정을 관리할 수 있는 앱 관리 창을 로드합니다.
1. **프로비저닝**을 선택하여 선택한 앱의 사용자 계정 프로비저닝 설정을 관리합니다.
1. **매핑**를 확장하여 Azure AD와 대상 애플리케이션 사이에 흐르는 사용자 특성을 살펴보고 편집합니다. 대상 애플리케이션에서 지원하는 경우 이 섹션에서 그룹 및 사용자 계정의 프로비저닝을 선택적으로 구성할 수 있습니다.

   ![매핑을 사용하여 사용자 특성 보기 및 편집](./media/customize-application-attributes/21.png)

1. **매핑** 구성을 선택하여 관련된 **특성 매핑** 화면을 엽니다. 일부 특성 매핑은 SaaS 애플리케이션에서 올바르게 작동하려면 필수입니다. 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.

   ![특성 매핑을 사용하여 앱에 대한 특성 매핑 구성](./media/customize-application-attributes/22.png)

   이 스크린샷에서는 Salesforce에서 관리된 개체의 **Username** 특성이 연결된 Azure Active Directory 개체의 **userPrincipalName** 값으로 채워지는 것을 볼 수 있습니다.

1. 기존 **특성 매핑**을 선택하여 **특성 편집** 화면을 엽니다. 여기에서는 Azure AD와 대상 애플리케이션 사이에 흐르는 사용자 특성을 편집할 수 있습니다.

   ![특성 편집을 사용하여 사용자 특성 편집](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해

특성 매핑으로 타사 SaaS 애플리케이션에서 특성이 채워지는 법을 제어합니다.
4 가지의 다른 매핑 형식이 지원됩니다.

- **직접** – 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.
- **상수** – 대상 특성이 지정한 특정 문자열로 채워집니다.
- **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다.
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)을 참조하세요.
- **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정한 기본 값으로 채워집니다.

이러한 4가지 기본 유형과 함께 사용자 지정 특성 매핑은 **기본** 값 할당(선택 사항)의 개념을 지원합니다. 기본 값 할당은 Azure AD 또는 대상 개체에 값이 없는 경우 대상 특성에 값이 채워지도록 합니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.

### <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서 특성 매핑 유형 속성을 이미 소개했습니다.
이 속성과 함께 특성 매핑은 다음 특성도 지원합니다.

- **원본 특성** - 원본 시스템의 사용자 특성입니다(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성입니다(예: ServiceNow)의 필드 간에 내용이 동기화되도록 지정하기 위해 구성할 수 있습니다.
- **null인 경우 기본값(선택 사항)** - 원본 속성이 null인 경우 대상 시스템에 전달될 값입니다. 이 값은 사용자가 생성될 때만 프로비저닝됩니다. 기존 사용자를 업데이트할 때는 "null 인 경우 기본값"이 프로비저닝되지 않습니다. 예를 들어, 대상 시스템의 모든 기존 사용자에게 특정 직책을 프로비저닝하려면(원본 시스템에서 null인 경우), 다음 [식](../app-provisioning/functions-for-customizing-application-data.md)을 사용합니다. Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). "Default Value"를 원본 시스템에서 null인 경우 프로비저닝하려는 값으로 바꿔야 합니다. 
- **이 특성을 사용하여 개체 일치** – 원본과 대상 시스템 사이에서 사용자를 고유하게 식별하는 데 이 매핑을 사용할지 여부를 나타냅니다. 일반적으로 대상 애플리케이션에서 username 필드에 매핑되는 Azure AD의 userPrincipalName 또는 메일 특성에서 설정됩니다.
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 여러 개가 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다. 일치 특성은 원하는 만큼 설정할 수 있지만 일치 특성으로 사용하는 특성이 실제로 고유하며 일치 특성이 되어야 하는지 여부를 고려해야 합니다. 일반적으로 고객의 구성에는 일치 특성이 1 또는 2개 있습니다. 
- **이 매핑 적용**
  - **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.
  - **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

## <a name="matching-users-in-the-source-and-target--systems"></a>원본 및 대상 시스템의 일치하는 사용자
Azure AD 프로비저닝 서비스는 "greenfield" 시나리오(대상 시스템에 사용자가 존재하지 않는 경우)와 "brownfield" 시나리오(대상 시스템에 사용자가 이미 존재하는 경우) 모두에 배포할 수 있습니다. 두 시나리오를 모두 지원하기 위해 프로비저닝 서비스는 일치 특성이라는 개념을 사용합니다. 일치 특성을 사용하면 원본에서 사용자를 고유하게 식별하고 대상의 사용자와 일치시키는 방식을 결정할 수 있습니다. 배포 계획의 일부로 원본 및 대상 시스템에서 사용자를 고유하게 식별하는 데 사용할 수 있는 특성을 식별합니다. 참고 사항:

- **일치 특성은 고유해야 합니다.** 고객은 userPrincipalName, mail 또는 object ID와 같은 특성을 일치 특성으로 사용하는 경우가 많습니다.
- **여러 특성을 일치 특성으로 사용할 수 있습니다.** 사용자를 일치시킬 때 평가할 여러 특성을 정의하고, 사용자가 평가되는 순서(UI에서 일치 우선 순위로 정의됨)를 정의할 수 있습니다. 예를 들어 특성 3개를 일치 특성으로 정의하고 처음 특성 2개를 평가한 후 사용자가 고유하게 일치하면, 서비스는 3번째 특성을 평가하지 않습니다. 서비스는 일치 특성을 지정한 순서대로 평가하고 일치 항목을 찾으면 평가를 중지합니다.  
- **원본과 대상의 값이 정확하게 일치하지 않아도 됩니다.** 대상의 값은 원본에 있는 값의 간단한 함수일 수 있습니다. 따라서 원본에 emailAddress 특성이 있고 대상에 userPrincipalName이 있으면 일부 문자를 상수 값으로 대체하는 emailAddress 특성의 함수를 사용하여 일치시킬 수 있습니다.  
- **특성의 조합에 기반하여 일치시키는 것은 지원되지 않습니다.** 대부분의 애플리케이션은 두 가지 속성을 기반으로 하는 쿼리를 지원하지 않습니다. 따라서 특성 조합을 기반으로 일치시킬 수 없습니다. 단일 속성을 차례로 평가하는 것은 가능합니다.
- **모든 사용자에게 하나 이상의 일치 특성에 대한 값이 있어야 합니다.** 일치 특성 하나를 정의하면 원본 시스템의 모든 사용자에게 해당 특성에 대한 값이 하나 있어야 합니다. 예를 들어 userPrincipalName을 일치 특성으로 정의하면 모든 사용자에게 userPrincipalName이 있어야 합니다. 일치 특성을 여러 개(예: extensionAttribute1 및 mail) 정의하면, 모든 사용자가 동일한 일치 특성을 가질 필요가 없습니다. 한 사용자에게는 extensionAttribute1이 있고 mail은 없으며, 다른 사용자에게는 mail이 있고 extensionAttribute1이 없을 수 있습니다. 
- **대상 애플리케이션이 일치 특성에 대한 필터링을 지원해야 합니다.** 애플리케이션 개발자가 사용자 또는 그룹 API에서 특성의 하위 집합에 대한 필터링을 허용합니다. 갤러리의 애플리케이션의 경우, 기본 특성 매핑이 대상 애플리케이션의 API가 필터링을 지원하는 특성에 대한 것인지 확인합니다. 대상 애플리케이션의 기본 일치 특성을 변경하는 경우, 타사 API 설명서를 참조하여 특성을 필터링할 수 있는지 확인합니다.  

## <a name="editing-group-attribute-mappings"></a>그룹 특성 매핑 편집

ServiceNow, Box 및 G Suite와 같은 일부 애플리케이션은 그룹 개체 및 사용자 개체를 프로비저닝하는 기능을 지원합니다. 그룹 개체에는 표시 이름 및 이메일 별칭과 같은 그룹 속성과 그룹 멤버를 포함할 수 있습니다.

![프로비저닝된 그룹 및 사용자 개체가 있는 ServiceNow를 보여주는 예제](./media/customize-application-attributes/24.png)

**매핑**에서 그룹 매핑을 선택하고 **특성 매핑** 화면에서 원하는 옵션에 대해 **사용**을 설정하여 그룹 프로비저닝을 선택적으로 사용하거나 사용하지 않도록 설정할 수 있습니다.

그룹 개체의 일부로 프로비전된 특성은 이전에 설명한 사용자 개체와 동일한 방식으로 사용자 지정할 수 있습니다. 

> [!TIP]
> 그룹 개체(속성 및 멤버) 프로비전은 애플리케이션에 [그룹을 할당](../manage-apps/assign-user-or-group-access-portal.md)하는 것과 별개의 개념입니다. 애플리케이션에 그룹을 할당할 수 있지만 그룹에 포함된 사용자 개체만 프로비전할 수 있습니다. 할당에서 그룹을 사용하기 위해 전체 그룹 개체를 프로비전할 필요는 없습니다.

## <a name="editing-the-list-of-supported-attributes"></a>지원되는 특성 목록 편집

지정된 애플리케이션에 지원되는 사용자 특성은 미리 구성됩니다. 대부분의 애플리케이션 사용자 관리 API는 스키마 검색을 지원하지 않습니다. 따라서 Azure AD 프로비저닝 서비스가 애플리케이션을 호출하여 지원되는 특성 목록을 동적으로 생성할 수 없습니다.

하지만 일부 애플리케이션은 사용자 지정 특성을 지원하며 Azure AD 프로비저닝 서비스는 사용자 지정 특성을 읽고 쓸 수 있습니다. Azure Portal에 정의를 입력하려면 **특성 매핑** 화면 아래쪽에서 **고급 옵션 표시** 확인란을 선택한 다음, 앱**의 특성 목록 편집**을 선택합니다.

특성 목록의 사용자 지정을 지원하는 애플리케이션 및 시스템은 다음과 같습니다.

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory([Azure AD Graph API 기본 특성](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) 및 사용자 지정 디렉터리 확장이 지원됩니다.)
- [SCIM 2.0](https://tools.ietf.org/html/rfc7643)을 지원하는 앱([코어 스키마](https://tools.ietf.org/html/rfc7643)에 정의된 특성을 추가해야 함)

> [!NOTE]
> 애플리케이션과 시스템의 스키마를 사용자 지정했으며, 사용자 지정 특성이 정의된 방식에 대한 실무 지식이 있는 관리자일 경우에만 지원되는 특성 목록을 편집하는 것이 좋습니다. 애플리케이션이나 시스템에서 제공하는 API 및 개발자 도구를 잘 알아야 하는 경우가 많습니다.

지원되는 특성 목록을 편집할 때는 다음과 같은 속성이 제공됩니다.

- **이름** - 대상 개체의 스키마에 정의된 특성의 시스템 이름입니다.
- **형식** - 대상 개체의 스키마에 정의된 특성이 저장하는 데이터 형식이며, 다음 형식 중 하나일 수 있습니다.
  - *이진* - 특성에 이진 데이터가 포함됩니다.
  - *부울* - 특성에 True 또는 False 값이 포함됩니다.
  - *날짜/시간* - 특성에 날짜 문자열이 포함됩니다.
  - *정수* - 특성에 정수가 포함됩니다.
  - *참조* - 특성에 대상 애플리케이션의 다른 테이블에 저장된 값을 참조하는 ID가 포함됩니다.
  - *문자열* - 특성에 텍스트 문자열이 포함됩니다.
- **기본 키 여부** - 대상 개체의 스키마에 특성이 기본 키 필드로 정의되어 있는지 여부입니다.
- **필수 여부** - 대상 애플리케이션 또는 시스템에서 특성을 채워야 하는지 여부입니다.
- **다중 값 여부** - 특성이 다중 값을 지원하는지 여부입니다.
- **대소문자 구분 여부** - 특성이 대/소문자를 구분하여 평가되는지 여부입니다.
- **API 식** - 특정 프로비저닝 커넥터(예: Workday)에 대한 설명서에 달리 지정된 경우 외에는 사용하지 마세요.
- **참조된 개체 특성** - 참조 형식 특성인 경우 이 메뉴를 사용하면 대상 애플리케이션에서 특성과 연결된 값이 포함된 테이블과 특성을 선택할 수 있습니다. 예를 들어 "Department"라는 특성의 저장된 값이 별도의 "Departments" 테이블에 있는 개체를 참조하는 경우 "Departments.Name"을 선택합니다. 지정된 애플리케이션에 대해 지원되는 참조 테이블 및 기본 ID 필드는 미리 구성되며, 현재 Azure Portal을 사용하여 편집할 수는 없지만 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)를 사용하여 편집할 수는 있습니다.

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>SCIM 규격 애플리케이션에 사용자 지정 확장 특성 프로비저닝
SCIM RFC는 핵심 사용자 및 그룹 스키마를 정의하는 동시에 애플리케이션의 요구를 충족하기 위한 스키마 확장을 허용합니다. SCIM 애플리케이션에 사용자 지정 특성을 추가하려면 다음을 수행합니다.
   1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인하여 **엔터프라이즈 애플리케이션**을 선택한 다음, 애플리케이션을 선택하고 **프로비저닝**을 선택합니다.
   2. **매핑**에서 사용자 지정 특성을 추가할 개체(사용자 또는 그룹)를 선택합니다.
   3. 페이지 맨 아래에서 **고급 옵션 표시**를 선택합니다.
   4. **AppName의 특성 목록 편집**을 선택합니다.
   5. 특성 목록의 맨 아래에, 제공된 필드에 사용자 지정 특성에 대한 정보를 입력합니다. 그런 다음, **특성 추가**를 선택합니다.

SCIM 애플리케이션의 경우 특성 이름이 아래 예제에 표시된 패턴을 따라야 합니다. "CustomExtensionName"과 "CustomAttribute"를 애플리케이션의 요구 사항에 따라 사용자 지정할 수 있습니다. 예를 들면 다음과 같습니다.  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

이러한 지침은 SCIM 사용 애플리케이션에만 적용 됩니다. ServiceNow 및 Salesforce와 같은 애플리케이션은 SCIM을 사용하여 Azure AD와 통합되지 않으므로 사용자 지정 특성을 추가할 때 특정 네임스페이스가 필요하지 않습니다.

사용자 지정 특성은 참조 특성 또는 다중값 특성일 수 없습니다. 사용자 지정 다중값 확장 특성은 현재 갤러리의 애플리케이션에 대해서만 지원됩니다.  
 
**확장 특성이 있는 사용자 표현 예:**

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


## <a name="provisioning-a-role-to-a-scim-app"></a>SCIM 앱에 역할 프로비저닝
아래 단계를 사용하여 사용자의 역할을 애플리케이션에 프로비저닝합니다. 아래 설명은 사용자 지정 SCIM 애플리케이션에만 해당합니다. Salesforce 및 ServiceNow와 같은 갤러리 애플리케이션은 미리 정의된 역할 매핑을 사용합니다. 아래 글머리 기호는 AppRoleAssignments 특성을 애플리케이션에 필요한 형식으로 변환하는 방법을 설명합니다.

- Azure AD의 appRoleAssignment를 애플리케이션의 역할에 매핑하려면 [식](../app-provisioning/functions-for-customizing-application-data.md)를 사용하여 특성을 변환해야 합니다. 역할 세부 정보를 구문 분석하는 식을 사용하지 않고 appRoleAssignment 특성을 역할 특성에 **직접 매핑하면 안 됩니다**. 

- **SingleAppRoleAssignment** 
  - **사용 시기:** SingleAppRoleAssignment 식은 사용자에게 단일 역할을 프로비저닝하고 기본 역할을 지정하는 경우에 사용합니다. 
  - **구성 방법:** 위에서 설명한 단계를 사용하여 특성 매핑 페이지로 이동하고 SingleAppRoleAssignment 식을 사용하여 역할 특성에 매핑합니다. 세 가지 역할 특성 중에 선택할 수 있습니다(roles[primary eq "True"].display, roles[primary eq "True].type, and roles[primary eq "True"].value). 매핑에 역할 특성 중 일부 또는 전부를 포함하도록 선택할 수 있습니다. 둘 이상을 포함하려면 새 매핑을 추가하여 대상 속성으로 포함하면 됩니다.  
  
  ![SingleAppRoleAssignment 추가](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **고려할 사항**
    - 사용자에게 여러 역할이 할당되지 않아야 합니다. 어떤 역할이 프로비저닝될지 보장할 수 없습니다.
    
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
  - **사용 시기:** AppRoleAssignmentsComplex 식은 한 명의 사용자에게 여러 역할을 프로비저닝하는 경우 사용합니다. 
  - **구성 방법:** 위의 설명에 따라 지원되는 특성 목록을 편집하여 역할에 대한 새로운 특성을 포함합니다. 
  
    ![역할 추가](./media/customize-application-attributes/add-roles.png)<br>

    그런 다음, AppRoleAssignmentsComplex 식을 사용하여 사용자 지정 역할 특성에 매핑합니다. 아래 이미지를 참조하세요.

    ![AppRoleAssignmentsComplex 추가](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **고려할 사항**
    - 모든 역할은 primary = false로 프로비저닝됩니다.
    - POST에 역할 형식이 포함됩니다. PATCH 요청에는 형식이 포함되지 않습니다. POST 및 PATCH 요청 모두에서 형식을 보낼 수 있도록 노력하고 있습니다.
    
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

  


## <a name="provisioning-a-multi-value-attribute"></a>다중값 특성 프로비저닝
phoneNumbers 및 emails와 같은 특정 특성은 다양한 유형의 전화 번호나 이메일을 지정해야 하는 다중값 특성입니다. 다중값 특성에는 아래 식을 사용합니다. 특성 유형을 지정하고 이것을 값에 해당하는 Azure AD 사용자 특성에 매핑할 수 있습니다. 

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

새로 시작하여 기존 매핑을 다시 기본 상태로 설정해야 하는 경우 **기본 매핑 복원** 확인란을 선택하고 구성을 선택하면 됩니다. 이렇게 하면 애플리케이션 갤러리에서 Azure AD 테넌트에 애플리케이션이 방금 추가된 것처럼 모든 매핑 및 범위 지정 필터가 설정됩니다.

이 옵션을 선택하면 프로비저닝 서비스가 실행 중일 때 모든 사용자의 재동기화가 효과적으로 적용됩니다.

> [!IMPORTANT]
> 이 옵션을 호출하기 전에 **프로비저닝 상태**를 반드시 **꺼짐**으로 설정하는 것이 좋습니다.

## <a name="what-you-should-know"></a>알아야 할 사항

- Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다.
- 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다.
- 특성 매핑에 대한 연속 변경 횟수를 최소로 유지하는 것이 가장 좋은 방법입니다.
- 사진을 동기화할 형식을 지정할 수 없으므로 앱에 프로비저닝할 사진 특성을 추가하는 기능이 현재는 지원되지 않습니다 [사용자의 소리](https://feedback.azure.com/forums/169401-azure-active-directory)에서 기능을 요청할 수 있습니다.
- IsSoftDeleted 특성은 애플리케이션에 대한 기본 매핑의 일부인 경우가 많습니다. IsSoftdeleted는 4가지 시나리오(애플리케이션에서 할당되지 않아서 사용자가 범위를 벗어난 경우, 범위 지정 필터를 충족하지 않아서 사용자가 범위를 벗어난 경우, 사용자가 Azure AD에서 일시 삭제된 경우, 사용자에 대해 AccountEnabled 속성이 false로 설정된 경우) 중 하나에 해당할 수 있습니다. 특성 매핑에서 IsSoftDeleted 특성을 제거하지 않는 것이 좋습니다.
- Azure AD 프로비저닝 서비스는 null 값 프로비저닝을 지원하지 않습니다.
- 기본 키(일반적으로 "ID")는 특성 매핑에서 대상 특성으로 포함되어서는 안 됩니다. 
- 일반적으로 역할 특성은 직접 매핑보다는 식을 사용하여 매핑해야 합니다. 역할 매핑에 대한 자세한 내용은 위의 섹션을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
- [특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
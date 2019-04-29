---
title: Azure AD 특성 매핑 사용자 지정 | Microsoft Docs
description: Azure Active Directory의 Saas 앱에 대한 어떤 특성 매핑이 있고 어떻게 비즈니스 요구 사항에 맞게 수정하는지를 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: celested
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b63118a7c5fae49edebe4ae4976a1362781ae8cf
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759781"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory에서 SaaS 애플리케이션에 대한 사용자 프로비전 특성 매핑 사용자 지정
Microsoft Azure AD는 Salesforce, G Suite 등과 같은 타사 SaaS 응용 프로그램에 사용자 프로 비전에 대 한 지원을 제공 합니다. 타사 SaaS 응용 프로그램에 대 한 사용자 프로 비전을 사용 하는 경우 Azure portal 특성 매핑을 통해 그 특성 값을 제어 합니다.

특성 및 특성-Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간의 매핑 집합을 미리 구성 된 경우 일부 앱은 다른 유형의 개체 그룹과 같은 사용자와 함께 관리 합니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 변경 하 또는 기존 특성 매핑을 삭제 하거나 새 특성 매핑을 만들 수 있습니다.
 
## <a name="editing-user-attribute-mappings"></a>사용자 특성 매핑 편집

에 액세스 하려면 다음이 단계를 수행 합니다 **매핑을** 사용자 프로 비전의 기능:

1. 에 로그인 합니다 [Azure Active Directory 포털](https://aad.portal.azure.com)합니다.

1. 선택 **엔터프라이즈 응용 프로그램** 왼쪽된 창에서. 갤러리에서 추가 된 앱을 비롯 한 모든 구성 된 앱의 목록이 표시 됩니다.

1. 보고서를 확인 하 고 앱 설정을 관리할 수 있는 해당 앱 관리 창에 로드 하려면 앱을 선택 합니다.

1. 선택 **프로 비전** 선택한 앱에 대 한 설정을 프로 비전 하는 사용자 계정을 관리 합니다.

1. 확장 **매핑을** Azure AD 사이 흐르는 사용자 특성 보기 및 편집 하 고 대상 응용 프로그램입니다. 대상 응용 프로그램에서 지원할 경우이 섹션에서는 필요에 따라 그룹 및 사용자 계정을 프로 비전을 구성할 수 있습니다.

   ![Salesforce](./media/customize-application-attributes/21.png) 

1. 선택는 **매핑을** 구성 관련 열려는 **특성 매핑** 화면. 일부 특성 매핑이 제대로 작동 하려면 SaaS 응용 프로그램에서 필요 합니다. 필수 특성인 경우 **삭제** 기능을 사용할 수 없습니다.

   ![Salesforce](./media/customize-application-attributes/22.png)

   이 스크린샷에서 볼 수 있습니다 합니다 **사용자 이름** Salesforce에서 관리 되는 개체의 특성이 채워집니다 합니다 **userPrincipalName** 연결된 된 Azure Active Directory 개체의 값입니다.

1. 기존 선택 **특성 매핑** 열려는 합니다 **특성 편집** 화면. 여기에서 Azure AD 사이 흐르는 사용자 특성을 편집할 수 있습니다 하 고 대상 응용 프로그램입니다.

   ![Salesforce](./media/customize-application-attributes/23.png)


### <a name="understanding-attribute-mapping-types"></a>특성 매핑 유형 이해
특성 매핑으로 타사 SaaS 애플리케이션에서 특성이 채워지는 법을 제어합니다. 4 가지의 다른 매핑 형식이 지원됩니다.

* **직접** – 대상 특성이 Azure AD에서 연결된 개체의 특성 값으로 채워집니다.
* **상수** – 대상 특성이 지정한 특정 문자열로 채워집니다.
* **식** - 대상 특성이 스크립트 방식의 식의 결과에 따라 채워집니다. 
  자세한 내용은 [Azure Active Directory의 특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)을 참조하세요.
* **None** - 대상 특성이 수정되지 않고 남아있습니다. 그러나 대상 특성이 비어 있으면 지정 된 기본 값으로 채워집니다.

이러한 네 가지 기본 형식과 함께 사용자 지정 특성 매핑을 지원 선택적인 개념이 **기본** 값 할당 합니다. 기본 값 할당 하면 없으면 값을 Azure AD 또는 대상 개체의 대상 특성 값으로 채워집니다. 이 값을 비워두는 것이 가장 일반적인 구성입니다.


### <a name="understanding-attribute-mapping-properties"></a>특성 매핑 속성 이해

이전 섹션에서 이미 특성 매핑 유형 속성에 도입 되었습니다.
이 속성을 함께 특성 매핑은 다음 특성도 지원:

- **원본 특성** - 원본 시스템의 사용자 특성입니다(예: Azure Active Directory).
- **대상 특성** – 대상 시스템의 사용자 특성입니다(예: ServiceNow)의 필드 간에 내용이 동기화되도록 지정하기 위해 구성할 수 있습니다.
- **이 특성을 사용 하 여 개체 일치** – 원본 및 대상 시스템 간에 사용자를 고유 하 게 식별 하이 매핑을 사용할지 여부를 선택 합니다. 일반적으로 대상 응용 프로그램에서 사용자 이름 필드에 매핑되는 Azure AD의 userPrincipalName 또는 메일 특성에 설정 됩니다.
- **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 여러 가지를 하는 경우이 필드에 정의 된 순서에는 평가 됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.
- **이 매핑 적용**
    - **항상** – 사용자 만들기 시이 매핑을 적용 하 고 작업을 업데이트 합니다.
    - **만드는 동안에** -사용자 만들기 작업 시에만이 매핑을 적용 합니다.


## <a name="editing-group-attribute-mappings"></a>그룹 특성 매핑 편집

ServiceNow, Box 및 G Suite 등의 응용 프로그램의 그룹 개체와 사용자 개체 프로 비전 하는 기능을 지원 합니다. 그룹 개체는 표시 이름과 같은 그룹 속성을 포함 하 고 그룹 멤버와 함께 별칭, 전자 메일 수 있습니다.

![ServiceNow](./media/customize-application-attributes/24.png)

그룹 프로 비전 필요에 따라 사용 하도록 설정 하거나 해제할 수에서 그룹 매핑을 선택 하 여 **매핑을**, 및 설정을 **사용** 에서 원하는 옵션으로는 **특성 매핑** 화면입니다.

그룹 개체의 일부로 프로비전된 특성은 이전에 설명한 사용자 개체와 동일한 방식으로 사용자 지정할 수 있습니다. 

>[!TIP]
>그룹 개체(속성 및 멤버) 프로비전은 애플리케이션에 [그룹을 할당](assign-user-or-group-access-portal.md)하는 것과 별개의 개념입니다. 애플리케이션에 그룹을 할당할 수 있지만 그룹에 포함된 사용자 개체만 프로비전할 수 있습니다. 할당에서 그룹을 사용하기 위해 전체 그룹 개체를 프로비전할 필요는 없습니다.


## <a name="editing-the-list-of-supported-attributes"></a>지원되는 특성 목록 편집

지정된 애플리케이션에 지원되는 사용자 특성은 미리 구성됩니다. 대부분의 응용 프로그램의 사용자 관리 Api는 스키마 검색을 지원 하지 않습니다. 따라서 Azure AD 프로 비전 서비스 응용 프로그램에 대 한 호출을 통해 지원 되는 특성 목록을 동적으로 생성할 수 없습니다. 

그러나 일부 응용 프로그램에는 사용자 지정 특성을 지원 하 고 Azure AD 프로 비전 서비스 읽기 및 사용자 지정 특성을 쓸 수 있습니다. 해당 정의 Azure portal에 입력 하려면 선택 합니다 **고급 옵션 표시** 맨 아래에 있는 확인란을 **특성 매핑** 화면을 선택한 후 **에대한특성목록편집** 앱.

특성 목록의 사용자 지정을 지원하는 애플리케이션 및 시스템은 다음과 같습니다.

* Salesforce
* ServiceNow
* Workday
* Azure Active Directory([Azure AD Graph API 기본 특성](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) 및 사용자 지정 디렉터리 확장이 지원됩니다.)
* [SCIM 2.0](https://tools.ietf.org/html/rfc7643)을 지원하는 앱([코어 스키마](https://tools.ietf.org/html/rfc7643)에 정의된 특성을 추가해야 함)

>[!NOTE]
>애플리케이션과 시스템의 스키마를 사용자 지정했으며, 사용자 지정 특성이 정의된 방식에 대한 실무 지식이 있는 관리자일 경우에만 지원되는 특성 목록을 편집하는 것이 좋습니다. 애플리케이션이나 시스템에서 제공하는 API 및 개발자 도구를 잘 알아야 하는 경우가 많습니다. 

지원되는 특성 목록을 편집할 때는 다음과 같은 속성이 제공됩니다.

* **이름** - 대상 개체의 스키마에 정의된 특성의 시스템 이름입니다. 
* **형식** -데이터 형식 특성 저장소, 다음 형식 중 하나일 수 있는 대상 개체의 스키마에 정의 된 대로:
   * *이진* - 특성에 이진 데이터가 포함됩니다.
   * *부울* - 특성에 True 또는 False 값이 포함됩니다.
   * *날짜/시간* - 특성에 날짜 문자열이 포함됩니다.
   * *정수* - 특성에 정수가 포함됩니다.
   * *참조* - 특성에 대상 애플리케이션의 다른 테이블에 저장된 값을 참조하는 ID가 포함됩니다.
   * *문자열* - 특성에 텍스트 문자열이 포함됩니다. 
* **기본 키 여부** -여부 특성은 대상 개체의 스키마에 기본 키 필드로 정의 됩니다.
* **필수 여부** -특성 인지 대상 응용 프로그램 또는 시스템에서 채울 필요가 있습니다.
* **다중 값 여부** -여부 특성 값을 여러 개 지원 합니다.
* **대소문자 구분 여부** -여부 특성 값은 대/소문자 구분 방식으로 평가 됩니다.
* **API 식** -특정 프로 비전 커넥터 (예: Workday)에 대 한 설명서에서 지시한 경우가 아니면 사용 하지 마세요.
* **참조 된 개체 특성** -경우 참조 형식 특성을이 메뉴를 사용 하면 특성과 연결 된 값이 포함 된 대상 응용 프로그램에서 테이블 및 특성을 선택 합니다. 예를 들어 "Department"라는 특성의 저장된 값이 별도의 "Departments" 테이블에 있는 개체를 참조하는 경우 "Departments.Name"을 선택합니다. 참조 테이블 및 지정된 된 응용 프로그램에 대 한 지원 되는 기본 ID 필드는 미리 구성 되며 현재 Azure portal을 사용 하 여 편집할 수 없습니다 하며 사용 하 여 편집할 수 있습니다 합니다 [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)합니다.

새 특성을 추가하려면 지원되는 특성 목록의 끝 부분으로 스크롤하고, 제공된 입력을 사용하여 위의 필드를 채우고, **특성 추가**를 선택합니다. 특성을 다 추가했으면 **저장**을 선택합니다. 그런 다음 다시 로드 해야 합니다 **프로 비전** 특성 매핑 편집기에서 사용할 수 있으려면 새 특성에 대 한 탭 합니다.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>기본 특성 및 특성 매핑 복원

통해 시작 및 기존 매핑을 다시 기본 상태로 재설정 해야 할를 선택할 수 있습니다 합니다 **기본 매핑을 복원** 확인란을 선택 하 고 구성을 저장 합니다. 이렇게 응용 프로그램이 방금 추가 된 Azure AD 테 넌 트 응용 프로그램 갤러리에서 처럼 모든 매핑이 설정 합니다. 

프로 비전 서비스가 실행 중일 때이 옵션을 선택 하면 모든 사용자의 다시 동기화를 효과적으로 적용 합니다. 

>[!IMPORTANT]
>강력한 좋습니다 **프로 비전 상태** 로 설정할 **해제** 이 옵션을 호출 하기 전에 합니다.


## <a name="what-you-should-know"></a>알아야 할 사항

* Microsoft Azure AD는 동기화 프로세스의 효과적인 구현을 제공합니다. 초기화된 환경에서 동기화 주기 중에는 업데이트가 필요한 개체만 처리됩니다. 

* 특성 매핑 업데이트는 동기화 주기의 성능에 영향을 줍니다. 특성 매핑 구성의 업데이트는 모든 관리된 개체를 다시 평가해야 합니다. 

* 좋습니다 최소한에 특성 매핑에 대 연속 변경 횟수를 유지 하는 경우


## <a name="next-steps"></a>다음 단계

* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](user-provisioning.md)
* [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)



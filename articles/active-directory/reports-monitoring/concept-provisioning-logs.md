---
title: Azure Active Directory 포털에서 로그 프로 비전 (미리 보기) | Microsoft Docs
description: Azure Active Directory 포털의 프로 비전 활동 보고서 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612791"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Azure Active Directory 포털에서 보고서 프로 비전 (미리 보기)

Azure AD(Azure Active Directory)의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **활동** 
    - **로그인** – 관리되는 애플리케이션 및 사용자 로그인 활동의 사용량에 대한 정보입니다.
    - **감사 로그** - [감사 로그](concept-audit-logs.md)는 사용자 및 그룹 관리, 관리되는 애플리케이션 및 디렉터리 활동에 대한 시스템 활동 정보를 제공합니다.
    - **로그 프로 비전** -Azure AD 프로 비전 서비스에서 프로 비전 하는 사용자, 그룹 및 역할에 대 한 시스템 활동을 제공 합니다. 

- **보안** 
    - **위험한 로그인** - [위험한 로그인](concept-risky-sign-ins.md)은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](concept-user-at-risk.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이 항목에서는 프로 비전 보고서의 개요를 제공 합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?
* 보안 관리자, 보안 읽기 권한자, 보고서 구독자, 응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자 역할의 사용자
* 글로벌 관리자


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>프로 비전 작업에 액세스 하는 데 필요한 Azure AD 라이선스는 무엇 인가요?

모든 프로 비전 활동 보고서를 보려면 테넌트에 연결 된 Azure AD Premium 라이선스가 있어야 합니다. [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요. 

## <a name="provisioning-logs"></a>프로비저닝 로그

프로 비전 로그는 다음 질문에 대 한 답변을 제공 합니다.

* ServiceNow에서 성공적으로 생성 된 그룹은 무엇 인가요?
* Amazon Web Services에서 역할을 가져오는 방법
* DropBox에서 어떤 사용자를 만들지 못했습니다.

[Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 블레이드의 **모니터링** 섹션에서 **프로 비전** 로그를 선택 하 여 프로 비전 로그에 액세스할 수 있습니다. 일부 프로 비전 레코드가 포털에 표시 되는 데 최대 2 시간이 걸릴 수 있습니다.

![프로 비전 로그](./media/concept-provisioning-logs/access-provisioning-logs.png "프로비저닝 로그")


프로 비전 로그에는 다음을 보여 주는 기본 목록 보기가 있습니다.

- Id
- 작업
- 원본 시스템
- 대상 시스템
- 상태
- 날짜


![기본 열](./media/concept-provisioning-logs/default-columns.png "기본 열")

도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![열 선택](./media/concept-provisioning-logs/column-chooser.png "열 선택")

그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![사용 가능한 열](./media/concept-provisioning-logs/available-columns.png "사용 가능한 열")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![자세한 정보](./media/concept-provisioning-logs/steps.png "Filter")


## <a name="filter-provisioning-activities"></a>프로 비전 작업 필터링

보고 된 데이터를 자신에 게 적합 한 수준으로 좁히려면 다음 기본 필드를 사용 하 여 프로 비전 데이터를 필터링 할 수 있습니다. 필터의 값은 테넌트에 따라 동적으로 채워집니다. 예를 들어 테넌트에 생성 이벤트가 없는 경우 create에 대 한 필터 옵션이 없습니다.

- ID
- 액션(Action)
- 원본 시스템
- 대상 시스템
- 가동 상태
- Date


![Filter](./media/concept-provisioning-logs/filter.png "Filter")

**Id** 필터를 사용 하면 관심 있는 이름이 나 id를 지정할 수 있습니다. 이 id는 사용자, 그룹, 역할 또는 다른 개체 일 수 있습니다. 개체의 이름 또는 ID를 기준으로 검색할 수 있습니다. ID는 시나리오에 따라 달라 집니다. 예를 들어 Azure AD에서 SalesForce로 개체를 프로 비전 할 때 원본 ID는 Azure AD에서 사용자의 개체 ID이 고 TargetID는 Salesforce의 사용자 ID입니다. Workday에서 Active Directory로 프로 비전 할 때 원본 ID는 Workday 작업자 직원 ID입니다. 사용자 이름은 항상 Id 열에 표시 되지 않을 수 있습니다. 항상 하나의 ID가 있습니다. 

**원본 시스템** 필터를 사용 하 여 id가 프로 비전 되는 위치를 지정할 수 있습니다. 예를 들어 Azure AD에서 ServiceNow로 개체를 프로 비전 할 때 원본 시스템은 Azure AD입니다. 

**대상 시스템** 필터를 사용 하 여 id가 프로 비전 되는 위치를 지정할 수 있습니다. 예를 들어 Azure AD에서 ServiceNow로 개체를 프로 비전 할 때 대상 시스템은 ServiceNow입니다. 

**상태** 필터를 사용하면 다음을 선택할 수 있습니다.

- 모두
- 성공
- 실패
- 생략

**작업** 필터를 사용 하 여를 필터링 할 수 있습니다.

- 생성 
- 업데이트
- 삭제
- 사용 안 함
- 기타

**날짜** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.  
가능한 값은 다음과 같습니다.

- 1개월
- 7 일
- 30일
- 24시간
- 사용자 지정 시간 간격

사용자 지정 시간 프레임을 선택 하는 경우 시작 날짜와 종료 날짜를 구성할 수 있습니다.


기본 필드 외에도 선택한 경우 필터에 다음 필드를 포함할 수 있습니다.

- **작업 id** -고유한 작업 id는 프로 비전을 사용 하도록 설정한 각 응용 프로그램과 연결 됩니다.   

- **주기 ID** -프로 비전 주기를 고유 하 게 식별 합니다. 이 ID를 공유 하 여이 이벤트가 발생 한 주기를 조회할 수 있습니다.

- **ID** -프로 비전 이벤트의 고유 식별자입니다. 을 지원 하기 위해이 ID를 공유 하 여 프로 비전 이벤트를 조회할 수 있습니다.   



  

## <a name="provisioning-details"></a>프로 비전 세부 정보 

프로 비전 목록 보기에서 항목을 선택 하면이 항목에 대 한 자세한 정보를 볼 수 있습니다.
세부 정보는 다음 범주에 따라 그룹화 됩니다.

- 단계

- 문제 해결 및 권장 사항

- 수정 된 속성

- 요약


![Filter](./media/concept-provisioning-logs/provisioning-tabs.png "탭")



### <a name="steps"></a>단계

**단계** 탭에서는 개체를 프로 비전 하는 데 필요한 단계를 간략하게 설명 합니다. 개체 프로 비전은 다음 네 단계로 구성 될 수 있습니다. 

- 개체 가져오기
- 개체가 범위 내에 있는지 확인
- 원본과 대상 간 개체 일치
- 프로 비전 개체 (작업 수행-만들기, 업데이트, 삭제 또는 사용 안 함)



![Filter](./media/concept-provisioning-logs/steps.png "Filter")


### <a name="troubleshoot-and-recommendations"></a>문제 해결 및 권장 사항


**문제 해결 및 권장 사항** 탭에서는 오류 코드 및 이유를 제공 합니다. 오류 정보는 오류가 발생 하는 경우에만 사용할 수 있습니다. 


### <a name="modified-properties"></a>수정 된 속성

**수정 된 속성** 은 이전 값 및 새 값을 표시 합니다. 이전 값이 없는 경우에는 이전 값 열이 비어 있습니다. 


### <a name="summary"></a>요약

**요약** 탭에서는 원본 및 대상 시스템의 개체에 대 한 변경 내용과 식별자에 대 한 개요를 제공 합니다. 

## <a name="what-you-should-know"></a>알아야 할 사항

- 프리미엄 버전이 있는 경우 30 일 동안, 무료 버전이 있는 경우 7 일 동안 보고 된 프로 비전 데이터를 저장 하는 Azure Portal.

- 변경 ID 특성을 고유 식별자로 사용할 수 있습니다. 예를 들어 제품 지원과 상호 작용할 때 유용 합니다.

- 현재 프로 비전 데이터를 다운로드할 수 있는 옵션은 없습니다.

- 현재 log analytics에 대 한 지원이 없습니다.

- 앱의 컨텍스트에서 프로 비전 로그에 액세스 하는 경우 감사 로그가 수행 하는 방식으로 이벤트를 특정 앱으로 자동 필터링 하지 않습니다.

## <a name="error-codes"></a>오류 코드

다음 표를 사용 하 여 프로 비전 로그에서 찾을 수 있는 오류를 해결 하는 방법을 보다 잘 이해할 수 있습니다. 누락 된 오류 코드의 경우이 페이지의 맨 아래에 있는 링크를 사용 하 여 피드백을 제공 합니다. 

|오류 코드|설명|
|---|---|
|충돌, EntryConflict|Azure AD 또는 응용 프로그램에서 충돌 하는 특성 값을 수정 하거나 충돌 하는 사용자 계정을 일치 시키고 사용 해야 하는 경우 일치 하는 특성 구성을 검토 합니다. 일치 특성을 구성 하는 방법에 대 한 자세한 내용은 다음 [설명서](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 를 검토 하세요.|
|TooManyRequests|대상 앱은 오버 로드 되어 너무 많은 요청을 받기 때문에 사용자를 업데이트 하려는 시도를 거부 했습니다. 수행할 작업이 없습니다. 이 시도는 자동으로 사용 중지 됩니다. Microsoft도이 문제에 대해 알려줍니다.|
|InternalServerError |대상 앱에서 예기치 않은 오류를 반환 했습니다. 대상 응용 프로그램에 서비스 문제가 있어이 작업을 수행할 수 없습니다. 이 시도는 자동으로 40 분 후에 사용 중지 됩니다.|
|InsufficientRights, MethodNotAllowed, NotPermitted, 권한 없음| Azure AD가 대상 응용 프로그램을 사용 하 여 인증할 수 있었지만 업데이트를 수행할 수 있는 권한이 없습니다. 대상 응용 프로그램 및 해당 응용 프로그램 [자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)에서 제공 하는 지침을 검토 하세요.|
|UnprocessableEntity|대상 응용 프로그램에서 예기치 않은 응답을 반환 했습니다. 대상 응용 프로그램의 구성이 올바르지 않거나, 대상 응용 프로그램에 서비스 문제가 있어이 작업을 수행할 수 없습니다.|
|WebExceptionProtocolError |대상 응용 프로그램에 연결 하는 동안 HTTP 프로토콜 오류가 발생 했습니다. 수행할 작업이 없습니다. 이 시도는 자동으로 40 분 후에 사용 중지 됩니다.|
|InvalidAnchor|프로 비전 서비스에서 이전에 만들었거나 일치 한 사용자가 더 이상 존재 하지 않습니다. 사용자가 존재 하는지 확인 합니다. 모든 사용자의 다시 일치를 강제로 수행 하려면 MS Graph API를 사용 하 여 [작업을 다시 시작](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http)합니다. 프로 비전을 다시 시작 하면 초기 주기가 트리거되고이를 완료 하는 데 시간이 걸릴 수 있습니다. 또한 프로 비전 서비스에서 작동 하는 데 사용 하는 캐시를 삭제 합니다. 즉, 테넌트의 모든 사용자와 그룹을 다시 평가 하 고 특정 프로 비전 이벤트를 삭제할 수 있습니다.|
|NotImplemented | 대상 앱에서 예기치 않은 응답을 반환 했습니다. 앱의 구성이 올바르지 않거나, 대상 앱에 서비스 문제가 있어이 작업을 수행할 수 없습니다. 대상 응용 프로그램 및 해당 응용 프로그램 [자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)에서 제공 하는 지침을 검토 하세요. |
|MandatoryFieldsMissing, MissingValues |필요한 값이 없으므로 사용자를 만들 수 없습니다. 원본 레코드에서 누락 된 특성 값을 수정 하거나 일치 하는 특성 구성을 검토 하 여 필수 필드가 생략 되지 않도록 하십시오. 일치 하는 특성 구성에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) .|
|SchemaAttributeNotFound |대상 응용 프로그램에 존재 하지 않는 특성이 지정 되었으므로 작업을 수행할 수 없습니다. 특성 사용자 지정에 대 한 [설명서](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 를 참조 하 여 구성이 올바른지 확인 합니다.|
|InternalError |Azure AD 프로 비전 서비스 내에서 내부 서비스 오류가 발생 했습니다. 수행할 작업이 없습니다. 이 시도는 40 분 내에 자동으로 다시 시도 됩니다.|
|InvalidDomain |잘못 된 도메인 이름을 포함 하는 특성 값으로 인해 작업을 수행할 수 없습니다. 사용자의 도메인 이름을 업데이트 하거나 대상 응용 프로그램의 허용 목록에 추가 합니다. |
|시간 제한 |대상 응용 프로그램이 응답 하는 데 너무 오래 걸려서 작업을 완료할 수 없습니다. 수행할 작업이 없습니다. 이 시도는 40 분 내에 자동으로 다시 시도 됩니다.|
|LicenseLimitExceeded|이 사용자에 대해 사용할 수 있는 라이선스가 없어서 대상 응용 프로그램에서 사용자를 만들 수 없습니다. 대상 응용 프로그램에 대 한 추가 라이선스를 구입 하거나, 사용자 할당 및 특성 매핑 구성을 검토 하 여 올바른 사용자가 올바른 특성으로 할당 되었는지 확인 합니다.|
|DuplicateTargetEntries  |구성 된 일치 특성을 사용 하 여 대상 응용 프로그램에서 둘 이상의 사용자를 찾았지만 작업을 완료할 수 없습니다. 대상 응용 프로그램에서 중복 사용자를 제거 하거나 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)에 설명 된 대로 특성 매핑을 다시 구성 하십시오.|
|DuplicateSourceEntries | 구성 된 일치 특성을 가진 사용자를 둘 이상 찾았지만 작업을 완료할 수 없습니다. 중복 사용자를 제거 하거나 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)에 설명 된 대로 특성 매핑을 다시 구성 하십시오.|

## <a name="next-steps"></a>다음 단계

* [사용자 프로 비전 상태를 확인 합니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Azure AD 갤러리 응용 프로그램에 사용자 프로 비전을 구성 하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)



---
title: Azure Active Directory 포털의 감사 작업 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 감사 작업 보고서 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01c9449a996929eb20acf35897a100dfbb722e82
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229925"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 감사 작업 보고서 

Azure AD(Azure Active Directory) 보고서를 통해 사용자 환경의 작동 방법을 결정하는 데 필요한 모든 정보를 얻을 수 있습니다.

보고 아키텍처는 다음과 같은 구성 요소로 구성됩니다.

- **작업** 
    - **로그인** – [로그인 보고서](concept-sign-ins.md)는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.
    - **감사 로그** - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- **보안** 
    - **위험한 로그인** - [위험한 로그인](../identity-protection/overview-identity-protection.md) 은 사용자 계정의 합법적인 소유자가 아닌 사용자가 수행 했을 수 있는 로그인 시도에 대 한 표시기입니다. 
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](../identity-protection/overview-identity-protection.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이 문서에서는 감사 보고서에 대한 개요를 제공합니다.
 
## <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?

* **보안 관리자**, **보안 읽기 권한자**, **보고서 구독자** , **전역 읽기 권한자** 또는 **전역 관리자** 역할의 사용자

## <a name="audit-logs"></a>감사 로그

Azure AD 감사 로그는 규정 준수를 위한 시스템 활동의 기록을 제공합니다. 감사 보고서에 액세스 하려면 **Azure Active Directory**의 **모니터링** 섹션에서 **감사 로그** 를 선택 합니다. 감사 로그의 대기 시간은 최대 1 시간 일 수 있으므로 작업을 완료 한 후에 감사 작업 데이터가 포털에 표시 되는 데 시간이 오래 걸릴 수 있습니다.



감사 로그에는 다음 항목을 보여 주는 기본 목록 보기가 있습니다.

- 발생 날짜와 시간
- 발생을 기록 하는 서비스입니다.
- 활동의 범주 및 이름 (*항목*) 
- 활동의 상태 (성공 또는 실패)입니다.
- 대상
- 작업의 초기자/행위자(누가)

![감사 로그](./media/concept-audit-logs/listview.png "감사 로그")

도구 모음에서 **열** 을 클릭 하 여 목록 보기를 사용자 지정할 수 있습니다.

![감사 로그](./media/concept-audit-logs/columns.png "감사 로그")

그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![감사 로그](./media/concept-audit-logs/columnselect.png "감사 로그")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![감사 로그](./media/concept-audit-logs/details.png "감사 로그")


## <a name="filtering-audit-logs"></a>감사 로그 필터링

다음 필드에서 감사 데이터를 필터링할 수 있습니다.

- 서비스
- 범주
- 활동
- 상태
- 대상
- 초기자(작업자)
- 날짜 범위

![감사 로그](./media/concept-audit-logs/filter.png "감사 로그")

**서비스** 필터를 사용 하면 다음 서비스의 드롭다운 목록에서 선택할 수 있습니다.

- 모두
- AAD 관리 UX
- 액세스 검토
- 계정 프로비전
- 애플리케이션 프록시
- 인증 방법
- B2C
- 조건부 액세스
- 핵심 디렉터리
- 자격 관리
- 하이브리드 인증
- ID 보호
- 사용자 초대
- MIM 서비스
- MyApps
- PIM
- 셀프 서비스 그룹 관리
- 셀프 서비스 암호 관리
- 사용 조건

**범주** 필터를 사용 하면 다음 필터 중 하나를 선택할 수 있습니다.

- 모두
- AdministrativeUnit
- ApplicationManagement
- 인증
- 권한 부여
- 연락처
- 디바이스
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- 레이블
- 기타
- PermissionGrantPolicy
- 정책
- ResourceManagement
- RoleManagement
- UserManagement

**활동** 필터는 사용자가 선택한 범주 및 활동 리소스 종류를 기준으로 합니다. 보려는 특정 활동을 선택하거나 모두 선택할 수 있습니다. 

Graph API를 사용 하 여 모든 감사 작업 목록을 가져올 수 있습니다. `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**상태** 필터를 사용 하면 감사 작업 상태를 기준으로 필터링 할 수 있습니다. 상태는 다음 중 하나일 수 있습니다.

- 모두
- Success
- 실패

**대상** 필터를 사용 하면 이름 또는 UPN (사용자 계정 이름)을 기준으로 특정 대상을 검색할 수 있습니다. 대상 이름 및 UPN은 대/소문자를 구분 합니다. 

**시작** 필터를 사용 하 여 행위자의 이름 또는 UPN (universal principal name)이 시작 되는 항목을 정의할 수 있습니다. 이름 및 UPN은 대/소문자를 구분 합니다.

**날짜 범위** 필터를 사용 하면 반환 된 데이터에 대 한 기간을 정의할 수 있습니다.  
가능한 값은 다음과 같습니다.

- 7 일
- 24시간
- 사용자 지정

사용자 지정 시간 범위를 선택하면 시작 시간과 종료 시간을 구성할 수 있습니다.

**다운로드** 단추를 선택 하 여 필터링 된 데이터를 최대 25만 개의 레코드로 다운로드 하도록 선택할 수도 있습니다. 로그는 CSV 또는 JSON 형식으로 다운로드할 수 있습니다. 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다.

![감사 로그](./media/concept-audit-logs/download.png "감사 로그")

## <a name="audit-logs-shortcuts"></a>감사 로그 바로 가기

**Azure Active Directory** 외에도 Azure Portal에서는 감사 데이터에 대한 다음 두 개의 추가 진입점을 제공합니다.

- 개요
- Enterprise 애플리케이션

### <a name="users-and-groups-audit-logs"></a>사용자 및 그룹 감사 로그

사용자 및 그룹 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

- 어떤 종류의 업데이트가 사용자에게 적용되나요?

- 얼마나 많은 사용자가 변경되었나요?

- 얼마나 많은 암호가 변경되었나요?

- 관리자가 디렉터리에서 무엇을 수행했나요?

- 추가된 그룹은 무엇인가요?

- 멤버 자격이 변경된 그룹이 있나요?

- 그룹의 소유자가 변경되었나요?

- 어떤 라이선스가 그룹 또는 사용자에 할당되었나요?

사용자와 관련 된 감사 데이터만 검토 하려는 경우에는 **사용자** 탭의 **모니터링** 섹션에 있는 **감사 로그** 에서 필터링 된 보기를 찾을 수 있습니다. 이 진입점에는 미리 선택 된 범주로 **Usermanagement** 가 있습니다.

![감사 로그](./media/concept-audit-logs/users.png "감사 로그")

그룹과 관련 된 감사 데이터만 검토 하려는 경우 **그룹** 탭의 **모니터링** 섹션에 있는 **감사 로그** 에서 필터링 된 보기를 찾을 수 있습니다. 이 진입점에는 미리 선택 된 범주의 **Groupmanagement** 가 있습니다.

![감사 로그](./media/concept-audit-logs/groups.png "감사 로그")

### <a name="enterprise-applications-audit-logs"></a>Enterprise 애플리케이션 감사 로그

애플리케이션 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

* 추가되거나 업데이트된 애플리케이션은 무엇인가요?
* 제거된 애플리케이션은 무엇인가요?
* 애플리케이션에 대한 서비스 주체가 변경되었나요?
* 애플리케이션의 이름이 변경되었나요?
* 누가 애플리케이션에 동의했나요?

애플리케이션과 관련된 감사 데이터를 검토하려면 **Enterprise 애플리케이션** 블레이드의 **활동** 섹션에 있는 **감사 로그**에서 필터링된 보기를 찾을 수 있습니다. 이 진입점에는 **응용 프로그램 유형**으로 미리 선택 된 **엔터프라이즈 응용 프로그램이** 있습니다.

![감사 로그](./media/concept-audit-logs/enterpriseapplications.png "감사 로그")

## <a name="office-365-activity-logs"></a>Office 365 활동 로그

[Microsoft 365 관리 센터](/office365/admin/admin-overview/about-the-admin-center)에서 Office 365 활동 로그를 볼 수 있습니다. Office 365 활동 및 Azure AD 활동 로그가 많은 디렉터리 리소스를 공유 하는 경우에도 Microsoft 365 관리 센터 에서만 Office 365 활동 로그의 전체 보기를 제공 합니다. 

[Office 365 관리 api](/office/office-365-management-api/office-365-management-apis-overview)를 사용 하 여 프로그래밍 방식으로 office 365 활동 로그에 액세스할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 감사 활동 참조](reference-audit-activities.md)
- [Azure AD 보고서 보존 참조](reference-reports-data-retention.md)
- [Azure AD 로그 대기 시간 참조](reference-reports-latencies.md)
---
title: Azure Portal에서 사용자 활동 보고서 찾기 Microsoft Docs
description: Azure Portal에서 Azure Active Directory 사용자 작업 보고서가 어디에 위치하는지 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b705cf6ac6fd25c7794db7651db5bc5c5c0e790c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230622"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal에서 작업 보고서 찾기

이 문서에서는 Azure Portal에서 Azure AD(Azure Active Directory) 사용자 작업 보고서를 찾는 방법을 알아봅니다.

## <a name="audit-logs-report"></a>감사 로그 보고서

감사 로그 보고서는 애플리케이션 작업의 여러 보고서를 컨텍스트 기반 보고에 대한 단일 보기로 결합합니다. 감사 로그 보고서에 액세스하려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 오른쪽 위 모서리에서 디렉터리를 선택한 다음, 왼쪽 탐색 창에서 **Azure Active Directory** 블레이드를 선택합니다.
3. Azure Active Directory 블레이드의 **활동** 섹션에서 **감사 로그**를 선택합니다. 

    ![감사 로그](./media/howto-find-activity-reports/482.png "감사 로그")

감사 로그 보고서는 다음과 같은 보고서를 통합합니다.

* 감사 보고서
* 암호 재설정 활동
* 암호 재설정 등록 활동
* 셀프 서비스 그룹 작업
* Office 365 그룹 이름 변경
* 계정 프로비전 활동
* 암호 롤오버 상태
* 계정 프로비전 오류

### <a name="filtering-on-audit-logs"></a>감사 로그 필터링

감사 보고서에서 고급 필터링을 사용 하 여 **범주** 필터에 지정 하는 방식으로 감사 데이터의 특정 범주에 액세스할 수 있습니다. 예를 들어 사용자와 관련 된 모든 활동을 보려면 **Usermanagement** 범주를 선택 합니다. 

범주는 다음과 같습니다.

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
- 기타
- 정책
- ResourceManagement
- RoleManagement
- UserManagement

**서비스** 드롭다운 필터를 사용 하 여 특정 서비스에 대해 필터링 할 수도 있습니다. 예를 들어 셀프 서비스 암호 관리와 관련 된 모든 감사 이벤트를 가져오려면 **셀프 서비스 암호 관리** 필터를 선택 합니다.

서비스는 다음과 같습니다.

- 모두
- 액세스 검토
- 계정 프로비전 
- 응용 프로그램 SSO
- 인증 방법
- B2C
- 조건부 액세스
- 핵심 디렉터리
- 자격 관리
- ID 보호
- 사용자 초대
- PIM
- 셀프 서비스 그룹 관리
- 셀프 서비스 암호 관리
- 사용 조건

## <a name="sign-ins-report"></a>로그인 보고서 

**로그인** 보기는 모든 사용자 로그인 뿐만 아니라 **애플리케이션 사용량** 보고서를 포함합니다. **Enterprise 애플리케이션** 개요의 **관리** 섹션에서도 애플리케이션 사용량 정보를 볼 수 있습니다.

로그인 보고서에 액세스하려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 오른쪽 위 모서리에서 디렉터리를 선택한 다음, 왼쪽 탐색 창에서 **Azure Active Directory** 블레이드를 선택합니다.
3. Azure Active Directory 블레이드의 **활동** 섹션에서 **로그인**을 선택합니다. 

    ![로그인 보기](./media/howto-find-activity-reports/483.png "로그인 보기")


### <a name="filtering-on-application-name"></a>애플리케이션 이름 필터링

로그인 보고서를 사용하여 사용자 이름 또는 애플리케이션 이름을 필터링하여 애플리케이션 사용량에 대한 세부 정보를 볼 수 있습니다.

![로그인 이벤트 필터링 페이지](./media/howto-find-activity-reports/07.png "로그인 이벤트 필터링 페이지")

## <a name="security-reports"></a>보안 보고서

### <a name="anomalous-activity-reports"></a>비정상적인 활동 보고서

비정상적인 활동 보고서는 Azure AD에서 검색 하 고 보고할 수 있는 보안 관련 위험 검색에 대 한 정보를 제공 합니다.

다음 표에서는 Azure AD 비정상 작업 보안 보고서 및 Azure Portal의 해당 위험 검색 유형을 나열 합니다. 자세한 내용은 [Azure Active Directory 위험 탐지](../identity-protection/overview-identity-protection.md)를 참조하세요.  


| Azure AD 비정상 작업 보고서 |  Id 보호 위험 검색 유형|
| :--- | :--- |
| 자격 증명이 손실된 사용자 | 유출된 자격 증명 |
| 비정상적인 로그인 작업 | 비정상적 위치로 불가능한 이동 |
| 감염 가능성이 있는 디바이스에서 로그인 | 감염된 디바이스에서 로그인|
| 알 수 없는 원본에서 로그인 | 익명 IP 주소에서 로그인 |
| 의심스러운 작업이 있는 IP 주소에서 로그인 | 의심스러운 작업이 있는 IP 주소에서 로그인 |
| - | 알 수 없는 위치에서 로그인 |

다음 Azure AD 비정상 작업 보안 보고서는 Azure Portal에서 위험 감지로 포함 되지 않습니다.

* 여러 번의 실패 후 로그인
* 여러 지역에서의 로그인


### <a name="detected-risk-detections"></a>검색 된 위험 검색

[Azure Portal](https://portal.azure.com) **Azure Active Directory** 블레이드의 **보안** 섹션에서 검색 된 위험 검색에 대 한 보고서에 액세스할 수 있습니다. 검색 된 위험 검색은 다음 보고서에서 추적 됩니다.   

- [위험에 노출된 사용자](../identity-protection/overview-identity-protection.md)
- [위험한 로그인](../identity-protection/overview-identity-protection.md)

    ![보안 보고서](./media/howto-find-activity-reports/04.png "보안 보고서")

## <a name="troubleshoot-issues-with-activity-reports"></a>활동 보고서 관련 문제 해결

### <a name="missing-data-in-the-downloaded-activity-logs"></a>다운로드된 활동 로그에서 데이터 누락

#### <a name="symptoms"></a>증상 

활동 로그(감사 또는 로그인)를 다운로드했고 선택한 시간에 대한 모든 레코드가 표시되지 않습니다. 그 이유는 무엇일까요? 

 ![보고](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>원인

Azure Portal에서 활동 로그를 다운로드 하는 경우 가장 최근의 가장 먼저 정렬 된 25만 레코드로 크기를 제한 합니다. 

#### <a name="resolution"></a>해결 방법

[Azure AD Reporting API](concept-reporting-api.md)를 활용하여 특정 시점에서 최대 백만 개의 레코드를 가져올 수 있습니다.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Azure Portal에서 최근 작업의 감사 데이터 누락

#### <a name="symptoms"></a>증상

Azure Portal에서 일부 작업을 수행했고 `Activity logs > Audit Logs` 블레이드에서 해당 작업에 대한 감사 로그가 표시될 것을 예상했지만 찾을 수 없습니다.

 ![보고](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>원인

작업이 활동 로그에 즉시 나타나지 않습니다. 아래 표에는 활동 로그의 대기 시간 숫자가 나열되어 있습니다. 

| 보고서 | 대기 시간(P95) | 대기 시간(P99) |
|--------|---------------|---------------|
| 디렉터리 감사 | 2분 | 5분 |
| 로그인 작업 | 2분 | 5분 |

#### <a name="resolution"></a>해결 방법

15분에서 2시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 2시간 후에도 로그가 보이지 않으면 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)해 주시면 저희 쪽에서 살펴보겠습니다.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Azure AD 로그인 활동 로그에서 최근 사용자 로그인 로그 누락

#### <a name="symptoms"></a>증상

최근에 Azure Portal에 로그인했고 `Activity logs > Sign-ins` 블레이드에 이 작업에 대한 로그인 로그가 표시될 것으로 예상했지만 찾을 수 없습니다.

 ![보고](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>원인

작업이 활동 로그에 즉시 나타나지 않습니다. 아래 표에는 활동 로그의 대기 시간 숫자가 나열되어 있습니다. 

| 보고서 | 대기 시간(P95) | 대기 시간(P99) |
|--------|---------------|---------------|
| 디렉터리 감사 | 2분 | 5분 |
| 로그인 작업 | 2분 | 5분 |

#### <a name="resolution"></a>해결 방법

15분에서 2시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 2시간 후에도 로그가 보이지 않으면 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)해 주시면 저희 쪽에서 살펴보겠습니다.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Azure Portal에서 30일이 초과된 보고서 데이터를 볼 수 없습니다.

#### <a name="symptoms"></a>증상

Azure Portal에서 30일이 초과된 로그인 및 감사 데이터를 볼 수 없습니다. 그 이유는 무엇일까요? 

 ![보고](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>원인

Azure Active Directory 작업은 사용자 라이선스에 따라 다음 기간 동안 작업 보고서를 저장합니다.

| 보고서           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---
| 디렉터리 감사  | 7 일        | 30일             | 30일             |
| 로그인 작업 | 사용할 수 없습니다. 로그인 작업은 개별 사용자 프로필 블레이드에서 7일 동안 액세스할 수 있습니다. | 30일 | 30일             |

자세한 내용은 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)을 참조하세요.  

#### <a name="resolution"></a>해결 방법

데이터를 30일 넘게 보존하는 두 가지 옵션이 있습니다. [Azure AD Reporting API](concept-reporting-api.md)를 사용하여 프로그래밍 방식으로 데이터를 검색하고 데이터베이스에 저장합니다. 또는 감사 로그를 Splunk 또는 SumoLogic 같은 타사 SIEM 시스템에 통합합니다.

## <a name="next-steps"></a>다음 단계

* [감사 로그 개요](concept-audit-logs.md)
* [로그인 개요](concept-sign-ins.md)
* [위험 이벤트 개요](../identity-protection/overview-identity-protection.md)
---
title: Azure Active Directory 포털의 로그인 작업 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 로그인 작업 보고서 소개
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
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e84fb04d967311fede3be7f20b45f01fb442e69e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228769"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 로그인 작업 보고서

Azure AD(Azure Active Directory)의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **작업** 
    - **로그인** – 관리되는 애플리케이션 및 사용자 로그인 활동의 사용량에 대한 정보입니다.
    - **감사 로그**  -  [감사 로그](concept-audit-logs.md) 는 사용자 및 그룹 관리, 관리 되는 응용 프로그램 및 디렉터리 작업에 대 한 시스템 작업 정보를 제공 합니다.
- **보안** 
    - **위험한 로그인** - [위험한 로그인](../identity-protection/overview-identity-protection.md) 은 사용자 계정의 합법적인 소유자가 아닌 사용자의 로그인 시도에 대 한 표시기입니다.
    - **위험 플래그가 지정된 사용자** - [위험한 사용자](../identity-protection/overview-identity-protection.md)는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이 문서에서는 로그인 보고서의 개요를 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?

* 보안 관리자, 보안 읽기 권한자, 전역 읽기 권한자 및 보고서 구독자 역할의 사용자
* 글로벌 관리자
* 모든 사용자(비관리자)가 자신의 로그인에 액세스할 수 있습니다. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>로그인 작업에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?

로그인 활동 보고서는 [모든 버전의 AZURE AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) 에서 사용할 수 있으며 Microsoft Graph API를 통해 액세스할 수도 있습니다.

## <a name="sign-ins-report"></a>로그인 보고서

사용자 로그인 보고서는 다음과 같은 질문에 대한 답변을 제공합니다.

* 사용자의 로그인 패턴이란?
* 한 주 동안 얼마나 많은 사용자가 로그인했나요?
* 이러한 로그인의 상태란?

[Azure Portal](https://portal.azure.com) 메뉴에서 **Azure Active Directory**를 선택 하거나 모든 페이지에서 **Azure Active Directory** 을 검색 하 여 선택 합니다.

![Azure Active Directory 선택](./media/concept-sign-ins/select-azure-active-directory.png "Azure Active Directory")

**모니터링**에서 **로그인** 을 선택 하 여 [로그인 보고서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)를 엽니다.

![로그인 작업](./media/concept-sign-ins/monitoring-sign-ins-in-azure-active-directory.png "로그인 작업")

일부 로그인 레코드가 포털에 표시 되는 데 최대 2 시간이 걸릴 수 있습니다.

> [!IMPORTANT]
> 로그인 보고서에는 **대화형** 로그인, 즉 사용자 이름과 암호를 사용하는 수동 로그인만 표시됩니다. 비대화형 로그인(서비스 간 인증)은 로그인 보고서에 표시되지 않습니다. 

로그인 로그에는 다음 항목을 보여주는 기본 목록 보기가 있습니다.

- 로그인 날짜
- 관련된 사용자
- 사용자가 로그인 한 응용 프로그램
- 로그인 상태
- 위험 검색 상태
- MFA(Multi-Factor Authentication) 요구 사항 상태

![로그인 작업](./media/concept-sign-ins/sign-in-activity.png "로그인 작업")

도구 모음에서 **열** 을 클릭 하 여 목록 보기를 사용자 지정할 수 있습니다.

![로그인 작업](./media/concept-sign-ins/19.png "로그인 작업")

**열** 대화 상자를 사용 하 여 선택 가능한 특성에 액세스할 수 있습니다. 로그인 보고서에는 지정 된 로그인 요청에 대해 둘 이상의 값이 있는 필드를 열로 사용할 수 없습니다. 예를 들어 인증 정보, 조건부 액세스 데이터 및 네트워크 위치에 대해 true입니다.   

![로그인 작업](./media/concept-sign-ins/columns.png "로그인 작업")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![로그인 작업](./media/concept-sign-ins/basic-sign-in.png "로그인 작업")

> [!NOTE]
> 이제 고객은 모든 로그인 보고서를 통해 조건부 액세스 정책의 문제를 해결할 수 있습니다. 고객은 로그인 레코드에 대 한 **조건부 액세스** 탭을 클릭 하 여 조건부 액세스 상태를 검토 하 고 로그인에 적용 된 정책과 각 정책에 대 한 결과에 대 한 세부 정보를 확인할 수 있습니다.
> 자세한 내용은 [모든 로그인의 CA 정보에 대한 질문과 대답](reports-faq.md#conditional-access)을 참조하세요.



## <a name="filter-sign-in-activities"></a>로그인 활동 필터링

먼저 보고 된 데이터를 자신에 게 적합 한 수준으로 축소 합니다. 둘째, 날짜 필드를 기본 필터로 사용 하 여 로그인 데이터를 필터링 합니다. Azure AD는 설정할 수 있는 광범위 한 추가 필터를 제공 합니다.

![로그인 작업](./media/concept-sign-ins/04.png "로그인 작업")

**요청 id** -관심 있는 요청의 ID입니다.

**사용자** -관심 있는 사용자의 이름 또는 UPN (사용자 계정 이름)입니다.

**응용 프로그램** -대상 응용 프로그램의 이름입니다.
 
**상태** -관심 있는 로그인 상태:

- Success

- 실패

- 장애가


**Ip 주소** -테 넌 트에 연결 하는 데 사용 되는 장치의 ip 주소입니다.

**위치** -연결이 시작 된 위치입니다.

- City

- 시/도

- 국가/지역


**리소스** -로그인에 사용 되는 서비스의 이름입니다.


**리소스 id** -로그인에 사용 되는 서비스의 id입니다.


**클라이언트 앱** -테 넌 트에 연결 하는 데 사용 되는 클라이언트 앱의 유형입니다.

![클라이언트 앱 필터](./media/concept-sign-ins/client-app-filter.png)


|이름|최신 인증|Description|
|---|:-:|---|
|인증 된 SMTP| |POP 및 IMAP 클라이언트에서 전자 메일 메시지를 보내는 데 사용 됩니다.|
|자동 검색| |Outlook 및 EAS 클라이언트에서 Exchange Online의 사서함을 찾아 연결 하는 데 사용 됩니다.|
|Exchange ActiveSync| |이 필터는 EAS 프로토콜이 시도 된 모든 로그인 시도를 표시 합니다.|
|브라우저|![확인](./media/concept-sign-ins/check.png)|웹 브라우저를 사용 하 여 사용자의 모든 로그인 시도를 표시 합니다.|
|Exchange ActiveSync| | Exchange ActiceSync를 사용 하 여 Exchange Online에 연결 하는 클라이언트 앱이 있는 사용자의 모든 로그인 시도를 표시 합니다.|
|Exchange Online PowerShell| |원격 PowerShell을 사용 하 여 Exchange Online에 연결 하는 데 사용 됩니다. Exchange Online PowerShell에 대 한 기본 인증을 차단 하는 경우 Exchange Online PowerShell 모듈을 사용 하 여 연결 해야 합니다. 자세한 내용은 [다단계 인증을 사용하여 Exchange Online PowerShell에 연결](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)을 참조하세요.|
|Exchange 웹 서비스| |Outlook, Mac 용 Outlook 및 타사 앱에서 사용 하는 프로그래밍 인터페이스입니다.|
|IMAP4| |전자 메일을 검색 하는 데 IMAP를 사용 하는 레거시 메일 클라이언트입니다.|
|HTTP를 통한 MAPI| |Outlook 2010 이상에서 사용 됩니다.|
|모바일 앱 및 데스크톱 클라이언트|![확인](./media/concept-sign-ins/check.png)|모바일 앱 및 데스크톱 클라이언트를 사용 하는 사용자의 로그인 시도를 모두 표시 합니다.|
|오프 라인 주소록| |Outlook에서 다운로드 하 여 사용 하는 주소 목록 컬렉션의 복사본입니다.|
|Outlook Anywhere (RPC over HTTP)| |Outlook 2016 이전 버전에서 사용 됩니다.|
|Outlook 서비스| |Windows 10 용 메일 및 일정 앱에서 사용 됩니다.|
|POP3| |POP3를 사용 하 여 전자 메일을 검색 하는 레거시 메일 클라이언트입니다.|
|보고 웹 서비스| |Exchange Online에서 보고서 데이터를 검색 하는 데 사용 됩니다.|
|기타 클라이언트| |클라이언트 앱이 포함 되어 있지 않거나 알 수 없는 사용자의 로그인 시도를 모두 표시 합니다.|



**운영** 체제-테 넌 트에 로그온 하는 데 사용 되는 장치에서 실행 중인 운영 체제입니다. 


**장치 브라우저** -브라우저에서 연결이 시작 된 경우이 필드를 사용 하 여 브라우저 이름을 기준으로 필터링 할 수 있습니다.


**상관 관계 id** -활동의 상관 관계 id입니다.




**조건부 액세스** -적용 된 조건부 액세스 규칙의 상태

- **적용 되지 않음**: 로그인 하는 동안 사용자 및 응용 프로그램에 정책이 적용 되지 않습니다.

- **성공**: 로그인 하는 동안 사용자 및 응용 프로그램에 적용 되는 하나 이상의 조건부 액세스 정책 (반드시 다른 조건을 충족 하지는 않음)입니다. 

- **실패**: 로그인에서 하나 이상의 조건부 액세스 정책에 대 한 사용자 및 응용 프로그램 조건을 충족 하 고 grant 컨트롤이 충족 되지 않거나 액세스를 차단 하도록 설정 되었습니다.









## <a name="download-sign-in-activities"></a>로그인 활동 다운로드

**다운로드** 옵션을 클릭 하 여 가장 최근 25만 레코드의 CSV 또는 JSON 파일을 만듭니다. Azure Portal 외부에서 작업 하려는 경우 [로그인 데이터 다운로드](quickstart-download-sign-in-report.md) 로 시작 합니다.  

![다운로드](./media/concept-sign-ins/71.png "다운로드")

> [!IMPORTANT]
> 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다.  


## <a name="sign-ins-data-shortcuts"></a>로그인 데이터 바로 가기

Azure AD와 Azure Portal는 모두 로그인 데이터를 위한 추가 진입점을 제공 합니다.

- ID 보안 보호 개요
- 사용자
- 그룹
- Enterprise 애플리케이션

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID 보안 보호에서 사용자 로그인 데이터

**Id 보안 보호** 개요 페이지의 사용자 로그인 그래프에는 로그인의 주간 집계가 표시 됩니다. 기간의 기본값은 30 일입니다.

![로그인 작업](./media/concept-sign-ins/06.png "로그인 작업")

로그인 그래프에서 한 날짜를 클릭하면 해당 날짜의 로그인 활동에 대한 개요가 표시됩니다.

로그인 활동 목록의 각 행에는 다음 내용이 표시됩니다.

* 누가 로그인했나요?
* 어떤 애플리케이션이 로그인할 대상이었나요?
* 로그인의 상태는 어떻습니까?
* 로그인의 MFA 상태는 어떻습니까?

항목을 클릭하면 로그인 작업에 대한 세부 정보가 표시됩니다.

- 사용자 ID
- 사용자
- 사용자 이름
- 애플리케이션 UI
- 애플리케이션
- 클라이언트
- 위치
- IP 주소
- 날짜
- 필요한 MFA
- 로그인 상태

> [!NOTE]
> IP 주소는 IP 주소와 해당 주소가 실제로 연결된 컴퓨터 간에 확실한 연결이 없는 경우와 같은 방법으로 발급됩니다. IP 주소 매핑은 클라이언트 디바이스가 실제로 사용되는 위치에서 종종 매우 먼 중앙 풀에서 모바일 공급자 또는 VPN이 IP 주소를 발급한다는 사실로 인해 복잡해집니다. 현재로서는 Azure AD 보고서에서 IP 주소를 실제 위치로 변환하는 것은 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보를 바탕으로 하는 최상의 노력입니다.

**사용자** 페이지에서 **활동** 섹션의 **로그인**을 클릭하면 모든 사용자 로그인에 대한 전체 개요가 표시됩니다.

![로그인 작업](./media/concept-sign-ins/08.png "로그인 작업")

## <a name="usage-of-managed-applications"></a>관리되는 애플리케이션의 사용량

로그인 데이터의 애플리케이션 중심 보기를 사용하여 다음과 같은 질문에 대답할 수 있습니다.

* 누가 내 애플리케이션을 사용하나요?
* 조직에서 상위 3 개의 응용 프로그램은 무엇 인가요?
* 최신 응용 프로그램이 어떻게 작동 하나요?

이 데이터에 대 한 진입점은 조직에서 상위 3 개의 응용 프로그램입니다. 데이터는 **엔터프라이즈 응용 프로그램**의 **개요** 섹션에 있는 지난 30 일 보고서 내에 포함 되어 있습니다.

![로그인 작업](./media/concept-sign-ins/10.png "로그인 작업")

앱 사용 그래프에는 지정 된 기간 동안 상위 3 개 응용 프로그램에 대 한 로그인의 주간 집계가 있습니다. 시간에 대한 기본값은 30일입니다.

![로그인 작업](./media/concept-sign-ins/graph-chart.png "로그인 작업")

원하면 특정 애플리케이션에 포커스를 설정할 수 있습니다.

![보고](./media/concept-sign-ins/single-app-usage-graph.png "보고")

앱 사용량 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

**로그인** 옵션을 선택하면 애플리케이션에 대한 모든 로그인 이벤트의 전체적인 개요를 보여 줍니다.

## <a name="office-365-activity-logs"></a>Office 365 활동 로그

[Microsoft 365 관리 센터](/office365/admin/admin-overview/about-the-admin-center)에서 Office 365 활동 로그를 볼 수 있습니다. Office 365 활동 및 Azure AD 활동 로그가 많은 디렉터리 리소스를 공유 한다는 점을 고려해 야 합니다. Microsoft 365 관리 센터 에서만 Office 365 활동 로그의 전체 보기를 제공 합니다. 

[Office 365 관리 api](/office/office-365-management-api/office-365-management-apis-overview)를 사용 하 여 프로그래밍 방식으로 office 365 활동 로그에 액세스할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [로그인 활동 보고서 오류 코드](reference-sign-ins-error-codes.md)
* [Azure AD 데이터 보존 정책](reference-reports-data-retention.md)
* [Azure AD 보고서 대기 시간](reference-reports-latencies.md)
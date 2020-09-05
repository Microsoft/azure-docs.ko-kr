---
title: 인증 방법 사용 & 정보-Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정 및 Multi-Factor Authentication 인증 방법 사용에 대 한 보고
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f75c08f0a390e798435b70bbe2409ec4a9732214
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717169"
---
# <a name="authentication-methods-usage--insights-preview"></a>인증 방법 사용 & insights (미리 보기)

사용 & insights를 사용 하면 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정과 같은 기능에 대 한 인증 방법이 조직에서 작동 하는 방식을 이해할 수 있습니다. 이 보고 기능은 사용자의 조직에 등록 되는 방법 및 사용 방법을 이해할 수 있는 수단을 제공 합니다.

## <a name="permissions-and-licenses"></a>권한 및 라이선스

다음 역할은 사용 및 정보에 액세스할 수 있습니다.

- 전역 관리자
- 보안 Reader
- 보안 관리자
- 보고서 구독자

사용 및 정보에 액세스 하는 데 추가 라이선스가 필요 하지 않습니다. Azure Multi-Factor Authentication 및 SSPR (셀프 서비스 암호 재설정) 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 찾을 수 있습니다.

## <a name="how-it-works"></a>작동 방법

인증 방법 사용 및 정보에 액세스 하려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory**  >  **암호 재설정**  >  **사용 & insights**로 이동 합니다.
1. **등록** 또는 **사용** 개요에서 필요에 따라 필터링 할 미리 필터링 된 보고서를 열도록 선택할 수 있습니다.

![사용 & insights 개요](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

사용 & 정보에 직접 액세스 하려면로 이동 [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) 합니다. 이 링크를 누르면 등록 개요가 표시 됩니다.

등록 된 사용자, 사용자 설정 및 사용자 지원 타일에는 사용자에 대 한 다음과 같은 등록 데이터가 표시 됩니다.

- 등록 됨: 사용자가 조직의 SSPR 또는 Multi-Factor Authentication 정책을 충족 하기에 충분 한 인증 방법을 등록 하는 경우 등록 된 것으로 간주 됩니다.
- 사용: SSPR 정책 범위에 있는 경우 사용자가 사용 하도록 설정 된 것으로 간주 됩니다. 그룹에 대해 SSPR를 사용 하는 경우 사용자가 해당 그룹에 있는 경우 사용 하도록 설정 된 것으로 간주 됩니다. 모든 사용자에 대해 SSPR를 사용 하도록 설정한 경우에는 테 넌 트의 모든 사용자 (게스트 제외)가 사용 하도록 설정 된 것으로 간주 됩니다.
- 지원: 사용자가 등록 되 고 사용 하도록 설정 된 경우 사용자가 지원 되는 것으로 간주 됩니다. 이 상태는 필요한 경우 언제 든 지 SSPR를 수행할 수 있음을 의미 합니다.

이러한 타일을 클릭 하거나 여기에 표시 된 정보를 클릭 하면 미리 필터링 된 등록 세부 정보 목록으로 이동 됩니다.

**등록** 탭의 **등록 차트에는 인증** 방법에 따라 성공 및 실패 한 인증 방법 등록 수가 표시 됩니다. [ **사용량** ] 탭의 [ **다시 설정** ] 차트는 인증 방법에의 한 암호 재설정 흐름 중 성공 및 실패 한 인증 수를 표시 합니다.

차트 중 하나를 클릭 하면 미리 필터링 된 등록 또는 다시 설정 이벤트 목록으로 이동 됩니다.

상단 오른쪽 모서리에 있는 컨트롤을 사용 하 여 등록에 표시 된 감사 데이터의 날짜 범위를 변경 하 고 차트를 24 시간, 7 일 또는 30 일로 다시 설정할 수 있습니다.

### <a name="registration-details"></a>등록 세부 정보

**등록 된 사용자**, **사용 하도록 설정**된 사용자 또는 **사용자** 가 사용할 수 있는 타일 또는 정보를 클릭 하면 등록 세부 정보로 이동 합니다.

등록 세부 정보 보고서에는 각 사용자에 대 한 다음 정보가 표시 됩니다.

- 이름
- 사용자 이름
- 등록 상태 (모두, 등록 됨, 등록 되지 않음)
- 활성화 상태 (모두, 사용, 사용 안 함)
- 가능 상태 (모두, 지원 안 함)
- 메서드 (앱 알림, 앱 코드, 전화 통화, SMS, 전자 메일, 보안 질문)

목록의 맨 위에 있는 컨트롤을 사용 하 여 사용자를 검색 하 고 표시 된 열을 기준으로 사용자 목록을 필터링 할 수 있습니다.

### <a name="reset-details"></a>세부 정보 다시 설정

등록을 클릭 하거나 차트를 다시 설정 하면 다시 설정 세부 정보로 이동 합니다.

세부 정보 다시 설정 보고서는 지난 30 일 동안 다음과 같은 등록 및 다시 설정 이벤트를 표시 합니다.

- 이름
- 사용자 이름
- 기능 (모두, 등록, 다시 설정)
- 인증 방법 (앱 알림, 앱 코드, 전화 통화, Office 통화, SMS, 전자 메일, 보안 질문)
- 상태 (모두, 성공, 실패)

목록의 맨 위에 있는 컨트롤을 사용 하 여 사용자를 검색 하 고 표시 된 열을 기준으로 사용자 목록을 필터링 할 수 있습니다.

## <a name="limitations"></a>제한 사항

이러한 보고서에 표시 되는 데이터는 최대 60 분까지 지연 됩니다. 최신 데이터를 식별 하기 위해 Azure Portal에 "마지막 새로 고침" 필드가 있습니다.

사용량 및 통찰력 데이터는 azure AD 로그인 보고서에 포함 된 Azure Multi-Factor Authentication 활동 보고서 또는 정보에 대 한 대체가 아닙니다.

현재 외부 사용자를 제외 하도록 보고서를 필터링 할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [인증 방법 사용 보고서 API 사용](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [조직에 대 한 인증 방법 선택](concept-authentication-methods.md)
- [결합 된 등록 환경](concept-registration-mfa-sspr-combined.md)
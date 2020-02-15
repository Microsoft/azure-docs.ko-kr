---
title: 조건부 액세스 정책의 세션 컨트롤-Azure Active Directory
description: Azure AD 조건부 액세스 정책의 세션 컨트롤은 무엇 인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c794e6fe751bc1c12b90353ef7b285f31a2331
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192143"
---
# <a name="conditional-access-session"></a>조건부 액세스: 세션

조건부 액세스 정책 내에서 관리자는 세션 컨트롤을 사용 하 여 특정 클라우드 응용 프로그램 내에서 제한 된 환경을 사용 하도록 설정할 수 있습니다.

![Multi-factor authentication을 요구 하는 권한 부여 컨트롤을 사용 하는 조건부 액세스 정책](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>응용 프로그램 적용 제한

조직에서는이 컨트롤을 사용 하 여 Azure AD에서 선택한 클라우드 앱에 장치 정보를 전달 하도록 요구할 수 있습니다. 디바이스 정보를 사용하면 클라우드 앱이 규정 준수 또는 도메인 조인 디바이스에서 연결이 초기화되는지 여부를 알 수 있습니다. 이 컨트롤은 선택한 클라우드 앱으로 SharePoint Online 및 Exchange Online만 지원합니다. 선택하면 클라우드 앱에서는 디바이스 정보를 사용하여 사용자에게 디바이스 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.

앱 적용 제한 사용 및 구성에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [SharePoint Online을 사용하여 제한된 액세스를 사용하도록 설정](https://docs.microsoft.com/sharepoint/control-access-from-unmanaged-devices)
- [Exchange Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>조건부 액세스 응용 프로그램 제어

조건부 액세스 앱 제어는 역방향 프록시 아키텍처를 사용 하며 Azure AD 조건부 액세스와 고유 하 게 통합 됩니다. Azure AD 조건부 액세스를 사용 하면 특정 조건에 따라 조직의 앱에 대 한 액세스 제어를 적용할 수 있습니다. 조건은 사용자 또는 사용자 그룹을 정의 하 고 조건부 액세스 정책이 적용 되는 위치 및 네트워크 (위치 및 네트워크)를 정의 합니다. 조건을 확인 한 후 액세스 및 세션 컨트롤을 적용 하 여 조건부 액세스 앱 제어 데이터를 보호할 수 있는 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 으로 사용자를 라우팅할 수 있습니다.

조건부 액세스 앱 제어를 사용 하 여 액세스 및 세션 정책에 따라 실시간으로 사용자 앱 액세스 및 세션을 모니터링 하 고 제어할 수 있습니다. 액세스 및 세션 정책은 Cloud App Security 포털 내에서 필터를 추가로 구체화 하 고 사용자에 대해 수행할 작업을 설정 하는 데 사용 됩니다. 액세스 및 세션 정책을 사용 하면 다음을 수행할 수 있습니다.

- 데이터 exfiltration 방지: 관리 되지 않는 장치와 같은 중요 한 문서의 다운로드, 잘라내기, 복사 및 인쇄를 차단할 수 있습니다.
- 다운로드 시 보호: 중요 한 문서의 다운로드를 차단 하는 대신 문서가 Azure Information Protection로 레이블을 지정 하 고 보호 하도록 요구할 수 있습니다. 이 작업을 수행 하면 문서가 보호 되 고 잠재적으로 위험한 세션에서 사용자 액세스가 제한 됩니다.
- 레이블이 지정 되지 않은 파일 업로드 방지: 중요 한 파일을 업로드, 배포 및 사용 하기 전에 파일에 올바른 레이블 및 보호가 있는지 확인 하는 것이 중요 합니다. 사용자가 콘텐츠를 분류 하기 전까지 중요 한 내용이 포함 된 레이블이 없는 파일이 업로드 되지 않도록 차단할 수 있습니다.
- 사용자 세션의 준수 모니터링: 위험한 사용자가 앱에 로그인 하 고 해당 작업이 세션 내에서 기록 되 면 해당 사용자가 모니터링 됩니다. 사용자 동작을 조사 하 고 분석 하 여 나중에 세션 정책을 적용 해야 하는 위치와 조건을 파악할 수 있습니다.
- 액세스 차단: 몇 가지 위험 요소에 따라 특정 앱 및 사용자에 대 한 액세스를 세부적으로 차단할 수 있습니다. 예를 들어 장치 관리의 형태로 클라이언트 인증서를 사용 하는 경우이를 차단할 수 있습니다.
- 사용자 지정 활동 차단: 일부 앱은 위험을 발생 시키는 고유한 시나리오를 포함 합니다. 예를 들어 Microsoft 팀 이나 여유 시간과 같은 앱에서 중요 한 내용이 포함 된 메시지를 보낼 수 있습니다. 이러한 종류의 시나리오에서는 중요 한 콘텐츠에 대 한 메시지를 검색 하 고 실시간으로 차단할 수 있습니다.

자세한 내용은 [추천 앱에 대 한 조건부 액세스 앱 제어 배포](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)문서를 참조 하세요.

## <a name="sign-in-frequency-preview"></a>로그인 빈도 (미리 보기)

로그인 빈도는 리소스에 액세스 하려고 할 때 사용자에 게 다시 로그인 하 라는 메시지가 표시 되는 기간을 정의 합니다.

로그인 빈도 설정은 표준에 따라 OAUTH2 또는 OIDC 프로토콜을 구현한 앱과 함께 작동 합니다. 다음 웹 응용 프로그램을 포함 하 여 Windows, Mac 및 모바일 용 Microsoft 네이티브 앱 대부분은 설정을 준수 합니다.

- Word, Excel, PowerPoint Online
- OneNote 온라인
- Office.com
- O365 관리 포털
- Exchange Online
- SharePoint 및 OneDrive
- 팀 웹 클라이언트
- Dynamics CRM Online
- Azure portal

자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)문서를 참조 하세요.

## <a name="persistent-browser-session-preview"></a>영구 브라우저 세션 (미리 보기)

영구 브라우저 세션을 사용 하면 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다.

자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)

---
title: Azure Active Directory 보고 API에 대 한 필수 구성 요소 | Microsoft Docs
description: Azure AD Reporting API에 액세스하기 위한 필수 구성 요소에 대해 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a8a0efe16b4ab2ea7b8a647284a3449741ac02
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226967"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소

[Azure AD(Azure Active Directory) Reporting API](./concept-reporting-api.md)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 프로그래밍 언어 및 도구에서 이러한 Api를 호출할 수 있습니다.

Reporting API는 [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) 를 사용하여 Web API에 대한 액세스 권한을 부여합니다.

reporting API에 액세스하도록 준비하려면 다음을 수행해야 합니다.

1. [역할 할당](#assign-roles)
2. [라이선스 요구 사항](#license-requirements)
3. [애플리케이션 등록](#register-an-application)
4. [권한 부여](#grant-permissions)
5. [구성 설정 수집](#gather-configuration-settings)

## <a name="assign-roles"></a>역할 할당

API를 통해 보고 데이터에 액세스하려면 다음 역할 중 하나를 할당받아야 합니다.

- 보안 Reader

- 보안 관리자

- 전역 관리자

## <a name="license-requirements"></a>라이선스 요구 사항

테 넌 트의 로그인 보고서에 액세스 하려면 Azure AD 테 넌 트에 연결 된 Azure AD Premium 라이선스가 있어야 합니다. 모든 Azure AD 테 넌 트의 로그인 보고서에 액세스 하려면 Azure AD Premium P1 (또는 이상) 라이선스가 필요 합니다. 또는 디렉터리 형식이 Azure AD B2C 경우 추가 라이선스 요구 사항 없이 API를 통해 로그인 보고서에 액세스할 수 있습니다. 


## <a name="register-an-application"></a>애플리케이션 등록

스크립트를 사용 하 여 보고 API에 액세스 하는 경우에도 등록이 필요 합니다. 등록은 권한 부여 호출에 필요한 **응용 프로그램 ID**를 제공 하며 코드에서 토큰을 받을 수 있도록 합니다.

Azure AD 보고 API에 액세스하도록 디렉터리를 구성하려면 Azure AD 테넌트에서 **글로벌 관리자** 디렉터리 역할의 멤버이기도 한 Azure 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인해야 합니다.

> [!IMPORTANT]
> 이 같은 관리자 권한이 있는 자격 증명 하에서 실행 중인 애플리케이션은 매우 강력할 수 있으므로, 애플리케이션의 ID 및 비밀 자격 증명을 안전하게 보관해야 합니다.
> 

**Azure AD 애플리케이션을 등록하려면:**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **앱 등록**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. **앱 등록** 페이지에서 **새 등록**을 선택 합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **응용 프로그램 등록** 페이지:

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. **이름** 텍스트 상자에서 `Reporting API application`을 입력합니다.

    b. **지원 되는 계정 유형**으로 **이 조직 에서만 계정을**선택 합니다.

    c. **리디렉션 URL** 선택 **웹** 텍스트 상자에을 입력 `https://localhost` 합니다.

    d. **등록**을 선택합니다. 


## <a name="grant-permissions"></a>권한 부여 

액세스하려는 API에 따라 앱에 다음 권한을 부여해야 합니다.  

| API | 사용 권한 |
| --- | --- |
| Windows Azure Active Directory | 디렉터리 데이터 읽기 |
| Microsoft Graph | 모든 감사 로그 데이터 읽기 |


![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/36.png)

다음 섹션에는 두 API에 대한 단계가 나열됩니다. API 중 하나에 액세스하지 않으려는 경우 관련 단계를 건너뛰면 됩니다.

**API를 사용하도록 애플리케이션에 권한을 부여하려면:**


1. **API 사용 권한을** 선택한 다음 **사용 권한을 추가**합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. **Api 권한 요청 페이지**에서 **지원 레거시 API** **Azure Active Directory 그래프**를 찾습니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. **필요한 권한** 페이지에서 **응용 프로그램 사용 권한**을 선택 하 고 **디렉터리** 확인란 디렉터리를 확장 **합니다. readall**을 선택 합니다.  **권한 추가**를 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. **보고 Api 응용 프로그램-Api 권한** 페이지에서 **관리자 동의 부여**를 선택 합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 참고: **Microsoft Graph** 는 API 등록 중에 기본적으로 추가 됩니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>구성 설정 수집 

이 섹션에서는 디렉터리에서 다음 설정을 가져오는 방법을 보여 줍니다.

- 도메인 이름
- 클라이언트 ID
- 클라이언트 암호

Reporting API에 대한 호출을 구성하는 경우 이 값이 필요합니다. 

### <a name="get-your-domain-name"></a>도메인 이름 가져오기

**도메인 이름을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Active Directory**를 선택합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **Azure Active Directory** 페이지에서 **사용자 지정 도메인 이름**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 도메인 목록에서 도메인 이름을 복사합니다.


### <a name="get-your-applications-client-id"></a>애플리케이션의 클라이언트 ID 가져오기

**애플리케이션의 클라이언트 ID를 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **앱 등록** 페이지에서 애플리케이션을 선택합니다.

3. 애플리케이션 페이지에서 **애플리케이션 ID**로 이동하고 **복사하려면 클릭**을 선택합니다.

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>애플리케이션의 클라이언트 비밀 가져오기
 API를 사용 하 여 감사 로그에 액세스 하거나 로그인 하는 동안 오류가 발생 하지 않도록 합니다.

**애플리케이션의 클라이언트 비밀을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  **앱 등록** 페이지에서 애플리케이션을 선택합니다.

3.  **API 응용 프로그램** 페이지에서 **인증서 및 암호** 를 선택 하 고 **클라이언트 암호** 섹션에서 **+ 새 클라이언트 암호**를 클릭 합니다. 

    ![애플리케이션 등록](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. **클라이언트 암호 추가** 페이지에서 다음을 추가 합니다.

    a. **설명** 텍스트 상자에서 `Reporting API`를 입력합니다.

    b. **만료**로 **In 2 years**(2년)를 선택합니다.

    다. **저장**을 클릭합니다.

    d. 키 값을 복사합니다.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>보고 API의 오류 문제 해결

이 섹션에서는 Microsoft Graph API를 사용 하 여 작업 보고서에 액세스 하는 동안 발생할 수 있는 일반적인 오류 메시지와 해결 단계를 나열 합니다.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>오류: Microsoft Graph에서 사용자 역할을 가져오지 못했습니다.

 Graph explorer를 사용 하 여 로그인 할 때 오류가 발생 하지 않도록 하려면 Graph Explorer UI의 로그인 단추를 모두 사용 하 여 계정에 로그인 합니다. 

![그래프 탐색기](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>오류: Microsoft Graph에서 프리미엄 라이선스 검사를 수행 하지 못했습니다. 

Graph 탐색기를 사용하여 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 왼쪽 탐색에 있는 계정 아래에서 **권한 수정**을 선택하고 **Tasks.ReadWrite** 및 **Directory.Read.All**을 선택합니다. 

![권한 수정 UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>오류: 테 넌 트가 B2C이 아니거나 테 넌 트에 프리미엄 라이선스가 없습니다.

로그인 보고서에 액세스하려면 Azure Active Directory P1(Premium 1) 라이선스가 필요합니다. 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure AD P1 라이선스를 사용하여 테넌트에게 사용이 허가되었는지 확인합니다.

### <a name="error-the-allowed-roles-does-not-include-user"></a>오류: 허용 되는 역할에는 사용자가 포함 되지 않습니다. 

 API를 사용 하 여 감사 로그에 액세스 하거나 로그인 하는 동안 오류가 발생 하지 않도록 합니다. 계정이 Azure Active Directory 테 넌 트에서 **보안 읽기 권한자** 또는 **보고서 구독자** 역할의 일부 인지 확인 합니다.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>오류: 애플리케이션에 AAD ‘디렉터리 데이터 읽기’ 권한이 없음 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>오류: 응용 프로그램에 Microsoft Graph API ' 모든 감사 로그 데이터 읽기 ' 권한이 없습니다.

[Azure Active Directory 보고 API에 액세스 하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md) 의 단계에 따라 응용 프로그램이 올바른 권한 집합으로 실행 되 고 있는지 확인 합니다. 

## <a name="next-steps"></a>다음 단계

* [인증서와 함께 Azure Active Directory reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md).
* [감사 API 참조](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [로그인 활동 보고서 API 참조](/graph/api/resources/signin?view=graph-rest-beta)
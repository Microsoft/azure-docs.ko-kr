---
title: Azure Active Directory Reporting API에서 오류 해결 | Microsoft Docs
description: Azure Active Directory Reporting API를 호출하는 동안 오류에 대한 해결 방법을 제공합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: abc8badf261e631dd6ceb7af9a6a0cb3676ae25d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231013"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Azure Active Directory Reporting API에서 오류 해결

이 문서에서는 Microsoft Graph API를 사용 하 여 활동 보고서에 액세스 하는 동안 발생할 수 있는 일반적인 오류 메시지와 해결 단계를 나열 합니다.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Microsoft Graph V2 엔드포인트에 액세스하는 동안 500 HTTP 내부 서버 오류 발생

현재 Microsoft Graph v2 엔드포인트가 지원되지 않습니다. Microsoft Graph v1 엔드포인트를 사용하여 활동 로그에 액세스해야 합니다.

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>오류: 테넌트가 B2C가 아니고 테넌트에 Premium 라이선스가 없음

로그인 보고서에 액세스하려면 Azure Active Directory P1(Premium 1) 라이선스가 필요합니다. 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure AD P1 라이선스를 사용하여 테넌트에게 사용이 허가되었는지 확인합니다.

### <a name="error-user-is-not-in-the-allowed-roles"></a>오류: 사용자가 허용된 역할에 없음 

API를 사용하여 감사 로그 또는 로그인에 액세스하는 동안 이 오류 메시지가 표시되면 Azure Active Directory 테넌트의 **보안 읽기 권한자** 또는 **보고서 구독자** 역할이 계정에 포함되어 있는지 확인합니다. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>오류: 애플리케이션에 AAD ‘디렉터리 데이터 읽기’ 권한이 없음 

[Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>오류: 응용 프로그램에 Microsoft Graph API ' 모든 감사 로그 데이터 읽기 ' 권한이 없습니다.

[Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)의 단계에 따라 애플리케이션이 적합한 권한 집합으로 실행되고 있는지 확인합니다. 

## <a name="next-steps"></a>다음 단계

[감사 API 참조 사용](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
 [로그인 활동 보고서 API 참조 사용](/graph/api/resources/signin?view=graph-rest-beta)
---
title: Azure AD reporting API 시작 | Microsoft Docs
description: Azure Active Directory Reporting API를 시작하는 방법
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2e3f98bd96c09fe741a24a1332443c876e2e61
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89225913"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API 시작하기

Azure Active Directory는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 애플리케이션에 대한 유용한 정보를 포함하는 다양한 [보고서](overview-reports.md)를 제공합니다. 

Azure AD 보고서에 대한 Microsoft Graph API를 사용하면 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스할 수 있습니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

이 문서에서는 액세스하는 방법을 포함하여 보고 API 개요를 제공합니다.

문제가 발생하면 [Azure Active Directory에 대한 지원을 받는 방법](../fundamentals/active-directory-troubleshooting-support-howto.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

사용자 작업으로 또는 사용자 작업 없이 보고 API에 액세스하려면 다음이 필요합니다.

1. 역할(보안 읽기 권한자, 보안 관리자, 전역 관리자) 할당
2. 애플리케이션 등록
3. 권한 부여
4. 구성 설정 수집

자세한 지침은 [Azure Active Directory reporting API에 액세스하기 위한 전제 조건](howto-configure-prerequisites-for-reporting-api.md)을 참조하세요. 

## <a name="api-endpoints"></a>API 엔드포인트 

감사 로그에 대한 Microsoft Graph API 엔드포인트는 `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits`이며 로그인에 대한 Microsoft Graph API 엔드포인트는 `https://graph.microsoft.com/v1.0/auditLogs/signIns`입니다. 자세한 내용은 [감사 API 참조](/graph/api/resources/directoryaudit?view=graph-rest-1.0) 및 [로그인 API 참조](/graph/api/resources/signIn?view=graph-rest-1.0)를 참조하세요.

또한 [ID 보호 위험 검색 API](/graph/api/resources/identityriskevent?view=graph-rest-beta)를 사용하여 Microsoft Graph를 통해 보안 검색에 프로그래밍 방식으로 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory ID 보호 및 Microsoft Graph 시작](../identity-protection/howto-identity-protection-graph-api.md)을 참조하세요. 
  
## <a name="apis-with-microsoft-graph-explorer"></a>Microsoft Graph 탐색기를 사용한 API

[Microsoft Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 로그인 및 감사 API 데이터를 확인할 수 있습니다. Graph 탐색기 UI에서 두 로그인 단추를 사용하여 계정에 로그인하고 표시된 것처럼 테넌트에 대해 **AuditLog.Read.All** 및 **Directory.Read.All** 권한을 설정합니다.   

![Graph 탐색기](./media/concept-reporting-api/graph-explorer.png)

![권한 수정 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>인증서를 사용하여 Azure AD Reporting API에 액세스 

사용자 개입 없이 보고 데이터를 검색하려는 경우 인증서와 함께 Azure AD Reporting API를 사용합니다.

자세한 지침은 [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

 * [보고서 API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md) 
 * [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](tutorial-access-api-with-certificates.md)
 * [Azure AD Reporting API의 오류 문제 해결](troubleshoot-graph-api.md)
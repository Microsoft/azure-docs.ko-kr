---
title: 다중 테 넌 트 응용 프로그램에 대 한 자동 사용자 프로 비전 사용-Azure AD
description: 자동화 된 프로 비전을 사용 하도록 설정 하는 독립 소프트웨어 공급 업체를 위한 가이드
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7ffdef43aa0fdcaab9e8ceae519cef9dfe5cdf6e
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235641"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>다중 테 넌 트 응용 프로그램에 대 한 자동 사용자 프로 비전 사용

자동 사용자 프로 비전은 software as (software as a service) 응용 프로그램과 같은 대상 시스템에서 사용자 id의 생성, 유지 관리 및 제거를 자동화 하는 프로세스입니다.

## <a name="why-enable-automatic-user-provisioning"></a>자동 사용자 프로비저닝을 사용 해야 하는 이유

사용자의 첫 번째 로그인에서 사용자를 프로 비전 해야 하기 전에 사용자 레코드가 응용 프로그램에 있어야 하는 응용 프로그램입니다. 서비스 공급자와 고객에 게 누릴 수 있는 이점이 있습니다.

### <a name="benefits-to-you-as-the-service-provider"></a>서비스 공급자의 혜택

* Microsoft id 플랫폼을 사용 하 여 응용 프로그램의 보안을 강화 합니다.

* 응용 프로그램을 도입 하기 위해 실제 및 인식 되는 고객의 노력을 줄입니다.

* SCIM (도메인 간 Id 관리) 기반 프로 비전을 위해 시스템을 사용 하 여 자동 사용자 프로 비전을 위해 여러 id 공급자 (IdPs)와 통합 하는 비용을 줄입니다.

* 고객이 사용자 프로 비전 문제를 해결 하는 데 도움이 되는 풍부한 로그를 제공 하 여 지원 비용을 줄입니다.

* [AZURE AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps)에서 응용 프로그램의 표시 여부를 높입니다.

* 앱 자습서 페이지에서 우선 순위가 지정 된 목록을 가져옵니다.

### <a name="benefits-to-your-customers"></a>고객에 대 한 혜택

* 응용 프로그램에 대 한 액세스 권한을 자동으로 제거 하 여 역할을 변경 하거나 응용 프로그램에 대 한 구성을 유지 하 여 보안을 강화 합니다.

* 수동 프로 비전과 관련 된 인적 오류 및 반복적인 작업을 방지 하 여 응용 프로그램에 대 한 사용자 관리를 간소화 합니다.

* 사용자 지정 개발 된 프로 비전 솔루션을 호스트 하 고 유지 관리 하는 비용을 줄입니다.

## <a name="choose-a-provisioning-method"></a>프로비전 메서드 선택

Azure AD는 응용 프로그램에 대 한 자동 사용자 프로비저닝을 사용할 수 있도록 여러 통합 경로를 제공 합니다.

* [AZURE Ad 프로 비전 서비스](../app-provisioning/user-provisioning.md) 는 azure ad에서 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전 해제 (아웃 바운드 프로 비전) 및 응용 프로그램에서 azure ad로 (인바운드 프로 비전)를 관리 합니다. 서비스는 응용 프로그램에서 제공 하는 SCIM (도메인 간 Id 관리) 사용자 관리 API 끝점에 대 한 시스템에 연결 합니다.

* [Microsoft Graph](/graph/)사용 하는 경우 응용 프로그램은 Microsoft Graph API를 쿼리하여 Azure AD에서 응용 프로그램에 대 한 사용자 및 그룹의 인바운드 및 아웃 바운드 프로 비전을 관리 합니다.

* 응용 프로그램에서 페더레이션에 SAML를 사용 하는 경우에는 SAML JIT (Security Assertion Markup Language Just-in-time) 사용자 프로 비전을 사용 하도록 설정할 수 있습니다. SAML 토큰에 전송 된 클레임 정보를 사용 하 여 사용자를 프로 비전 합니다.

응용 프로그램에 사용할 통합 옵션을 확인 하려면 상위 수준 비교 표를 참조 하 고 각 옵션에 대 한 자세한 정보를 확인 하십시오.

| 자동 프로비저닝을 사용 하도록 설정 또는 향상 된 기능| Azure AD 프로 비전 서비스 (SCIM 2.0)| Microsoft Graph API (OData v 4.0)| SAML JIT |
|---|---|---|---|
| Azure AD의 사용자 및 그룹 관리| √| √| 사용자만 |
| 온-프레미스 Active Directory에서 동기화 된 사용자 및 그룹 관리| √*| √*| 사용자만 해당 * |
| O365 데이터 (팀, SharePoint, 전자 메일, 일정, 문서 등)에 대 한 액세스를 프로 비전 하는 동안 사용자 및 그룹 외의 데이터에 액세스| X+| √| X |
| 비즈니스 규칙에 따라 사용자 만들기, 읽기 및 업데이트| √| √| √ |
| 비즈니스 규칙에 따라 사용자 삭제| √| √| X |
| Azure Portal의 모든 응용 프로그램에 대 한 자동 사용자 프로 비전 관리| √| X| √ |
| 여러 id 공급자 지원| √| X| √ |
| 게스트 계정 지원 (B2B)| √| √| √ |
| 엔터프라이즈가 아닌 계정 지원 (B2C)| X| √| √ |

<sup>*</sup> – AD에서 Azure AD로 사용자를 동기화 하려면 Azure AD Connect 설치 해야 합니다.  
<sup>+</sup >– 프로 비전에 SCIM을 사용 하는 경우 다른 용도로 응용 프로그램을 Microsoft Graph와 통합 하는 것을 배제 하지 않습니다.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD 프로 비전 서비스 (SCIM)

Azure AD 프로 비전 서비스는 다양 한 id 공급자 (IdPs) 및 응용 프로그램 (예: 여유 시간, G Suite, Dropbox)에서 지원 되는 프로 비전을 위한 산업 표준인 [Scim](https://aka.ms/SCIMOverview)을 사용 합니다. Scim 호환 IdP SCIM 끝점에 연결할 수 있으므로 Azure AD 외에 IdPs를 지원 하려는 경우 Azure AD 프로 비전 서비스를 사용 하는 것이 좋습니다. 간단한/User 끝점을 빌드하면 사용자의 동기화 엔진을 유지 관리할 필요 없이 프로 비전을 사용 하도록 설정할 수 있습니다. 

Azure AD 프로 비전 서비스 사용자의 SCIM 방법에 대 한 자세한 내용은 다음을 참조 하세요. 

* [SCIM 표준에 대 한 자세한 정보](https://aka.ms/SCIMOverview)

* [SCIM(System for Cross-Domain Identity Management)을 사용하여 사용자 및 그룹을 Azure Active Directory에서 애플리케이션으로 자동 프로비전](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Azure AD SCIM 구현 이해](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>프로 비전을 위한 Microsoft Graph

프로 비전에 Microsoft Graph를 사용 하는 경우 Graph에서 사용할 수 있는 모든 리치 사용자 데이터에 액세스할 수 있습니다. 사용자 및 그룹의 세부 정보 외에도 사용자의 역할, 관리자 및 부하 직원, 소유 및 등록 된 장치, [Microsoft Graph](/graph/api/overview?view=graph-rest-1.0)에서 사용할 수 있는 수백 개의 기타 데이터 부분과 같은 추가 정보를 가져올 수 있습니다. 

1500만 개 이상의 조직 및 fortune 500 회사의 90%는 Office 365, Microsoft Azure, Enterprise Mobility Suite 또는 Microsoft 365와 같은 Microsoft 클라우드 서비스를 구독 하는 동안 Azure AD를 사용 합니다. Microsoft Graph를 사용 하 여 직원 온 보 딩 (종료), 프로필 유지 관리 등의 관리 워크플로와 앱을 통합할 수 있습니다. 

프로 비전에 Microsoft Graph를 사용 하는 방법에 대해 자세히 알아보세요.

* [Microsoft Graph 홈페이지](https://developer.microsoft.com/graph)

* [Microsoft Graph 개요](/graph/overview)

* [Microsoft Graph Auth 개요](/graph/auth/)

* [Microsoft Graph 시작](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>프로 비전에 SAML JIT 사용

응용 프로그램에 처음 로그인 할 때만 사용자를 프로 비전 하려는 경우 사용자를 자동으로 프로 비전 해제 하지 않아도 되는 경우 SAML JIT는 옵션입니다. 응용 프로그램은 saml JIT를 사용 하는 페더레이션 프로토콜로 SAML 2.0을 지원 해야 합니다.

SAML JIT는 SAML 토큰의 클레임 정보를 사용 하 여 응용 프로그램에서 사용자 정보를 만들고 업데이트 합니다. 고객은 필요에 따라 Azure AD 응용 프로그램에서 이러한 필수 클레임을 구성할 수 있습니다. 경우에 따라 고객이이 기능을 사용할 수 있도록 응용 프로그램 쪽에서 JIT 프로 비전을 사용 하도록 설정 해야 합니다. SAML JIT는 사용자를 만들고 업데이트 하는 데 유용 하지만 응용 프로그램에서 사용자를 삭제 하거나 비활성화할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [응용 프로그램에 Single Sign-on 사용](../manage-apps/isv-sso-content.md)

* Microsoft의 사이트에 대 한 설명서를 만들려면 [응용 프로그램 목록과](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) 파트너를 Microsoft에 제출 하세요.

* [Microsoft 파트너 네트워크 (무료)에 참여 하 고 시장 출시 계획을 만듭니다](https://partner.microsoft.com/explore/commercial).

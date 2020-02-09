---
title: 다중 테넌트 응용 프로그램에 대해 SSO를 사용 하도록 설정
description: Azure active Directory와 통합에 독립 소프트웨어 공급 업체에 대 한 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795174"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>다중 테넌트 응용 프로그램에서 Single Sign-on을 사용 하도록 설정  

구매 또는 구독을 통해 다른 회사에서 사용 하 여 응용 프로그램을 제공 하는 경우 응용 프로그램이 사용할 수 있도록 자신의 Azure 테넌트 내의 고객에 게 합니다. 이 다중 테넌트 응용 프로그램 만들기 라고 합니다. 이 개념의 개요를 참조 하세요 [Azure에서 다중 테넌트 응용 프로그램](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) 하 고 [Azure Active Directory의 테넌트](../develop/single-and-multi-tenant-apps.md)합니다.

## <a name="what-is-single-sign-on"></a>Single Sign-on 이란

single sign-on (SSO) 사용자가 Azure Active Directory 및 다른 id를 사용 하 여 응용 프로그램에 로그온 하는 경우 보안과 편리 하 게 추가 합니다. 응용 프로그램 SSO가 설정 된 경우 사용자가 해당 응용 프로그램에 액세스 하려면 별도 자격 증명을 입력할 필요가 없습니다. Single sign-on의 전체 설명을 보려면. [Azure Active Directory에서 응용 프로그램에서 Single sign-on 참조](what-is-single-sign-on.md)합니다.

## <a name="why-enable-single-sign-on-in-your-application"></a>응용 프로그램에서 Single Sign-on 사용 이유?

다중 테넌트 응용 프로그램에서 SSO를 사용 하도록 설정 하면 많은 장점이 있습니다. 응용 프로그램에 SSO를 사용 하는 경우

* 앱이 수백만 개 조직 Azure Active Directory를 사용 하 여 검색할 수 있는 Azure Marketplace에서 응용 프로그램을 나열할 수 있습니다.
  * 통해 신속 하 게 Azure AD를 사용 하 여 응용 프로그램을 구성할 수 있습니다.

* Office.com의 Microsoft Search 내에서 Office 365 앱 갤러리, Office 365 앱 시작 관리자에 응용 프로그램을 검색할 수 있습니다.

* 응용 프로그램 Microsoft Graph REST API를 사용 하 여 Microsoft Graph에서 사용할 수 있는 사용자 생산성을 높이 데이터에 액세스할 수 있습니다.

* 고객에 게 쉽게 만들어 지원 비용을 절감 합니다.
  * 상호 고객 eases 도입에 대 한 Azure AD 팀과 응용 프로그램별 설명서 coproduced 합니다.
  * 한 번의 클릭 SSO을 사용 하는 경우 고객의 IT 관리자가 조직에서 사용 하기 위해 응용 프로그램을 구성 하는 방법을 알아봅니다 필요가 없습니다.

* 직원 및 게스트 id의 인증 및 권한 부여를 완전히 관리할 수는 고객에 게 제공 합니다.

  * 이러한 id의 고객 소유자를 사용 하 여 모든 계정 관리 및 규정 준수 책임을 배치합니다.

  * 비즈니스 요구 사항에 맞게 특정 id 공급자, 그룹 또는 사용자에 대 한 SSO를 사용할지 여부를 제공 하는 기능.

* Adoptability 고 marketability 늘릴 있습니다. 요구 (또는 목표로) 여러 대기업과 직원 모든 응용 프로그램에 원활한 SSO 환경을 유지 합니다. SSO를 손쉽게 지정 하는 것이 중요 합니다.

* 최종 사용자 사용을 늘리고 수익을 높일 수 있는 최종 사용자 마찰을 줄일 수 있습니다.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>게시 된 응용 프로그램에서 Single Sign-on을 사용 하도록 설정 하는 방법

1. [다중 테넌트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜을 선택](isv-choose-multi-tenant-federation.md)합니다.
1. 응용 프로그램에서 SSO 구현
   - 참조 [인증 패턴에 대 한 지침](../develop/v2-app-types.md)
   - 참조 [Azure active Directory 코드 샘플](../develop/sample-v2-code.md) OIDC 및 OAuth 프로토콜에 대 한
1. [Azure 테넌트 만들기](isv-tenant-multi-tenant-app.md) 응용 프로그램 및 테스트
1. [만들기 및 게시 사이트에 대 한 SSO 설명서](isv-create-sso-documentation.md)합니다.
1. [응용 프로그램 목록을 제출](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) 및 Microsoft 사이트에서 문서를 만들려면 Microsoft와 파트너입니다.
1. [Microsoft 파트너 네트워크 (무료)에 가입으로 서, 마케팅 계획을 만들고](https://partner.microsoft.com/en-us/explore/commercial#gtm)합니다.

---
title: 다중 테넌트 응용 프로그램에 대 한 Azure 테넌트 만들기
description: Azure Active Directory와의 통합에 대 한 독립 소프트웨어 공급 업체 지침
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812621"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>다중 테넌트 응용 프로그램에 대 한 Azure 테넌트 만들기  

다중 테넌트 응용 프로그램에 대 한 액세스를 제공 하려면 Azure Active Directory 테넌트를 만들어 응용 프로그램을 등록 하 고 고객 id의 페더레이션을 사용 하도록 설정 해야 합니다. [다중 테넌트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜 선택을](isv-choose-multi-tenant-federation.md)참조 하세요. 이 테넌트를 사용 하면 고객의 Azure AD 환경과 비슷한 환경에서 응용 프로그램과 페더레이션을 테스트할 수 있습니다.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>다중 테넌트 응용 프로그램을 호스트 하는 비용

Azure Active Directory는 여러 버전에서 사용할 수 있습니다. [자세한 기능 비교를 참조](https://azure.microsoft.com/pricing/details/active-directory/)하세요.

Azure 구독 및 Azure active directory를 무료로 만들고 기본 기능을 사용할 수 있습니다.

## <a name="create-your-tenant"></a>테넌트 만들기

1. 테넌트를 만듭니다. [개발 환경 설정을](../develop/quickstart-create-new-tenant.md)참조 하세요.

2. 응용 프로그램에 대 한 Single Sign-On 액세스를 설정 하 고 테스트 합니다.

   a. **OIDC 또는 Oath 응용 프로그램의**경우 [응용 프로그램](../develop/quickstart-register-app.md) 을 다중 테넌트 응용 프로그램으로 등록 합니다. 지원 되는 계정 유형에서 조직 디렉터리 및 개인 Microsoft 계정 옵션의 계정을 선택 합니다.

   b. **Saml 및 WS 급지됨 기반 응용 프로그램의**경우 Azure AD에서 일반 saml 템플릿을 사용 하 여 [saml 기반 Single sign-on](configure-single-sign-on-non-gallery-applications.md) 응용 프로그램을 구성 합니다.

필요한 경우 [단일 테넌트 응용 프로그램을 다중 테넌트로 변환할](../develop/howto-convert-app-to-be-multi-tenant.md) 수도 있습니다.

## <a name="next-steps"></a>다음 단계

[응용 프로그램에서 SSO 통합](isv-sso-content.md)

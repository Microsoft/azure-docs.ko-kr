---
title: v1.0의 애플리케이션 유형 | Azure
description: Azure Active Directory v2.0 엔드포인트에서 지원되는 앱 유형 및 시나리오에 대해 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: b91cc5249b386a465ea98b5e81b199323481a733
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165151"
---
# <a name="application-types-in-v10"></a>v1.0의 애플리케이션 유형

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure AD(Azure Active Directory)는 모두 업계 표준 프로토콜 OAuth 2.0 또는 OpenID Connect를 기반으로 하는 다양한 최신 앱 아키텍처에 대한 인증을 지원합니다.

다음 다이어그램은 이 시나리오 및 애플리케이션 유형, 다양한 구성 요소를 추가할 수 있는 방법 등을 보여 줍니다.

![애플리케이션 종류 및 시나리오](./media/authentication-scenarios/application-types-scenarios.png)

다음은 Azure AD에서 지원되는 다섯 가지 기본 애플리케이션 시나리오입니다.

- **[SPA(단일 페이지 애플리케이션)](single-page-application.md)** : 사용자가 Azure AD로 보호된 단일 페이지 애플리케이션에 로그인해야 합니다.
- **[웹 브라우저-웹 애플리케이션](web-app.md)** : 사용자가 Azure AD로 보호된 웹 애플리케이션에 로그인해야 합니다.
- **[네이티브 애플리케이션-웹 API](native-app.md)** : 전화, 태블릿 또는 PC에서 실행되는 네이티브 애플리케이션이 사용자를 인증해야 Azure AD로 보호된 웹 API에서 리소스를 가져올 수 있습니다.
- **[웹 애플리케이션-웹 API](web-api.md)** : 웹 애플리케이션이 Azure AD로 보호된 웹 API에서 리소스를 가져와야 합니다.
- **[디먼 또는 서버 애플리케이션-웹 API](service-to-service.md)** : 웹 사용자 인터페이스가 없는 서버 애플리케이션 또는 디먼 애플리케이션이 Azure AD로 보호된 웹 API에서 리소스를 가져와야 합니다.

코드 작업을 시작하기 전에 링크를 따라 각 앱 유형에 대해 자세히 알아보고 대략적인 시나리오를 파악하세요. v1.0 엔드포인트 또는 v2.0 엔드포인트에서 작동하는 특정 앱을 작성할 때 알아야 할 차이점을 살펴볼 수도 있습니다.

> [!NOTE]
> v2.0 엔드포인트는 일부 Azure AD 시나리오 및 기능만 지원합니다. v2.0 엔드포인트를 사용해야 하는지 확인하려면 [v2.0 제한 사항](../develop/active-directory-v2-limitations.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.

다양한 언어 및 플랫폼을 사용하여 여기에 설명된 앱과 시나리오를 개발할 수 있습니다. 코드 샘플 가이드 [시나리오별 v1.0 코드 샘플](sample-v1-code.md) 및 [시나리오별 v2.0 코드 샘플](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)에 있는 전체 코드 샘플에서 모두 지원됩니다. 해당 [GitHub 샘플 리포지토리](https://github.com/Azure-Samples?q=active-directory)에서 바로 코드 샘플을 다운로드할 수도 있습니다.

또한 애플리케이션에 엔드투엔드 시나리오의 특정 부분이나 세그먼트가 필요한 경우 대부분 해당 기능을 개별적으로 추가할 수 있습니다. 예를 들어 웹 API를 호출하는 네이티브 애플리케이션이 있는 경우 웹 API도 호출하는 웹 애플리케이션을 쉽게 추가할 수 있습니다.

## <a name="app-registration"></a>앱 등록

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Azure AD v1.0 엔드포인트를 사용하는 앱 등록

Azure AD로 인증을 아웃소싱하는 애플리케이션을 디렉터리에 등록해야 합니다. 이 단계에서는 애플리케이션이 위치한 URL, 인증 후 응답을 보낼 URL, 애플리케이션을 식별하는 URI 등을 포함하여 애플리케이션에 대한 정보를 Azure AD에 등록합니다. 이러한 정보가 필요한 주된 이유는 다음과 같습니다.

* Azure AD는 로그온을 처리하거나 토큰을 교환할 때 애플리케이션과 통신해야 합니다. Azure AD와 애플리케이션 간에 전달되는 정보에 포함되는 항목은 다음과 같습니다.
  
  * **애플리케이션 ID URI** - 애플리케이션에 대한 식별자입니다. 이 값은 인증 과정에 호출자가 토큰을 필요로 하는 애플리케이션이 어느 것인지 나타내기 위해 Azure AD로 전송됩니다. 또한 이 값은 의도된 대상임을 애플리케이션에서 알 수 있도록 토큰에 포함됩니다.
  * **회신 URL** 및 **리디렉션 URI** - 웹 API 또는 웹 애플리케이션의 경우 회신 URL은 Azure AD에서 인증 응답(인증이 성공한 경우 토큰 포함)을 보내는 위치입니다. 네이티브 애플리케이션의 경우 리디렉션 URI는 Azure AD가 OAuth 2.0 요청에서 사용자-에이전트를 리디렉션하는 고유 식별자입니다.
  * **애플리케이션 ID** - 애플리케이션이 등록될 때 Azure AD에서 생성하는 애플리케이션의 ID입니다. 인증 코드 또는 토큰을 요청하면 인증 중에 애플리케이션 ID 및 키가 Azure AD로 보내집니다.
  * **키** - Azure AD를 인증하여 웹 API를 호출하면 애플리케이션 ID와 함께 보내지는 키입니다.
* Azure AD는 애플리케이션에 디렉터리 데이터, 조직의 다른 애플리케이션 등에 액세스하는 데 필요한 권한이 있는지 확인해야 합니다.

자세한 내용은 [앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)방법을 참조하세요.

## <a name="single-tenant-and-multi-tenant-apps"></a>단일 테넌트 및 다중 테넌트 앱

Azure AD를 사용하여 개발 및 통합할 수 있는 두 가지 범주의 애플리케이션이 있다는 것을 이해하면 프로비저닝이 더 분명해집니다.

* **단일 테넌트 애플리케이션** - 단일 테넌트 애플리케이션은 단일 조직에서 사용하기 위한 것입니다. 일반적으로 엔터프라이즈 개발자가 작성한 LoB(기간 업무) 애플리케이션이 이에 해당합니다. 단일 테넌트 애플리케이션은 하나의 디렉터리에 있는 사용자들만 액세스해야 하므로 하나의 디렉터리에서만 프로비전해야 합니다. 이 애플리케이션은 일반적으로 조직 내에서 개발자가 등록합니다.
* **다중 테넌트 애플리케이션** - 다중 테넌트 애플리케이션은 하나의 조직이 아니라 많은 조직에서 사용하기 위한 것입니다. 일반적으로 ISV(Independent Software Vendor)가 작성한 SaaS(Software-as-a-Service) 애플리케이션이 이에 해당합니다. 다중 테넌트 애플리케이션은 사용될 각 디렉터리에서 프로비저닝해야 하며, 그러려면 사용자나 관리자가 등록에 동의해야 합니다. 이러한 동의 프로세스는 애플리케이션이 디렉터리에 등록되고 Graph API 또는 다른 웹 API에 대한 액세스 권한이 제공되면 시작됩니다. 다른 조직의 사용자나 관리자가 애플리케이션을 사용하기 위해 등록할 때는 애플리케이션에서 요구되는 권한을 표시하는 대화 상자가 표시됩니다. 이 사용자 또는 관리자는 애플리케이션에 동의할 수 있으며, 이 경우 애플리케이션에서 주어진 데이터에 액세스할 수 있게 되고 최종적으로 디렉터리에서 애플리케이션이 등록됩니다. 자세한 내용은 [동의 프레임 워크 개요](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)를 참조하세요.

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>단일 테넌트 또는 다중 테넌트 응용 프로그램 개발 시의 추가 고려 사항

단일 테넌트 애플리케이션 대신 다중 테넌트 애플리케이션을 개발할 때 고려할 몇 가지 추가 사항이 있습니다. 예를 들어 여러 디렉터리에 있는 사용자에게 애플리케이션을 사용할 수 있도록 만들려면 사용자가 속한 테넌트를 확인할 수 있는 메커니즘이 필요합니다. 단일 테넌트 애플리케이션은 한 사용자에 대한 고유한 자체 디렉터리만 검토하면 되지만, 다중 테넌트 애플리케이션은 Azure AD의 모든 디렉터리에서 특정 사용자를 식별해야 합니다. 이 작업을 수행할 수 있도록, Azure AD는 테넌트 특정 엔드포인트를 제공하지 않고 다중 테넌트 애플리케이션이 로그인 요청을 디렉션할 수 있는 공통 인증 엔드포인트를 제공합니다. 이 엔드포인트는 Azure AD의 모든 디렉터리에 대해 https://login.microsoftonline.com/common이지만 테넌트 특정 엔드포인트는 https://login.microsoftonline.com/contoso.onmicrosoft.com일 수 있습니다. 공통 엔드포인트는 애플리케이션을 개발할 때 특히 중요하게 고려해야 합니다. 로그인, 로그아웃, 토큰 유효성 검사 도중에 다중 테넌트를 처리하는 필수 논리가 있어야 하기 때문입니다.

현재 단일 테넌트 애플리케이션을 개발 중이지만 이 애플리케이션을 다수의 조직에서 사용할 수 있도록 만들려는 경우 Azure AD에서 애플리케이션 및 해당 구성을 쉽게 변경하여 다중 테넌트를 지원하도록 만들 수 있습니다. 또한 단일 테넌트 애플리케이션의 인증을 제공하든 아니면 다중 테넌트 애플리케이션의 인증을 제공하든 상관없이, Azure AD는 모든 디렉터리의 모든 토큰에 동일한 서명 키를 사용합니다.

이 문서에 나오는 각 시나리오에는 해당 프로비전 요구 사항을 설명하는 하위 섹션이 있습니다. Azure AD에서 애플리케이션을 프로비전하는 방법 및 단일 테넌트 애플리케이션과 다중 테넌트 애플리케이션 간의 차이점에 대한 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](../develop/single-and-multi-tenant-apps.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요. 계속 읽으면서 Azure AD의 일반적인 애플리케이션 시나리오를 이해하시기 바랍니다.

## <a name="next-steps"></a>다음 단계

- 다른 Azure AD [인증 기본 사항](v1-authentication-scenarios.md)에 대해 자세히 알아보기

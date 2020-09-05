---
title: Azure AD에서 서비스 사용자 & 앱 Microsoft
titleSuffix: Microsoft identity platform
description: Azure Active Directory의 애플리케이션 및 서비스 주체 개체 간의 관계에 대해 알아봅니다.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 0b6a6eac04711b564d602408a57b92f833fb5d5d
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782440"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션 및 서비스 주체 개체

이 문서에서는 Azure Active Directory의 응용 프로그램 등록, 응용 프로그램 개체 및 서비스 주체, 사용 방법 및 서로 관련 된 방법을 설명 합니다. 응용 프로그램의 응용 프로그램 개체와 해당 서비스 주체 개체 간의 관계를 보여 주기 위해 다중 테 넌 트 예제 시나리오도 제공 됩니다.

## <a name="application-registration"></a>애플리케이션 등록
Id 및 액세스 관리 기능을 Azure AD에 위임 하기 위해 응용 프로그램을 Azure AD [테 넌 트](developer-glossary.md#tenant)에 등록 해야 합니다. Azure AD에 응용 프로그램을 등록 하면 Azure AD와 통합 될 수 있도록 응용 프로그램에 대 한 id 구성이 생성 됩니다. [Azure Portal][AZURE-Portal]에 앱을 등록 하는 경우이는 단일 테 넌 트 (테 넌 트 에서만 액세스할 수 있음) 또는 다중 테 넌 트 (다른 테 넌 트에서 액세스할 수 있음) 인지 여부를 선택 하 고 필요에 따라 액세스 토큰이 전송 되는 리디렉션 URI를 설정할 수 있습니다.

:::image type="content" source="media/app-objects-and-service-principals/app-registration.png" alt-text="응용 프로그램 등록 창 Azure Portal의 스크린샷":::

앱 등록을 완료 하면 홈 테 넌 트 또는 디렉터리 내에 상주 하는 앱의 전역적으로 고유한 인스턴스 (응용 프로그램 개체)가 있습니다.  앱에 대 한 전역적으로 고유한 ID (앱 또는 클라이언트 ID)도 있습니다.  그런 다음 포털에서 비밀 또는 인증서 및 범위를 추가 하 여 앱이 작동 하도록 하 고 로그인 대화 상자에서 앱의 브랜딩을 사용자 지정할 수 있습니다.

포털에서 응용 프로그램을 등록 하는 경우 응용 프로그램 개체 및 서비스 주체 개체는 홈 테 넌 트에 자동으로 만들어집니다.  Microsoft Graph Api를 사용 하 여 응용 프로그램을 등록/만들 경우에는 별도의 단계를 통해 서비스 주체 개체를 만들 수 있습니다.

## <a name="application-object"></a>애플리케이션 개체
Azure AD 응용 프로그램은 응용 프로그램이 등록 된 Azure AD 테 넌 트에 상주 하는 응용 프로그램 개체 (응용 프로그램의 "홈" 테 넌 트 라고도 함)로 정의 됩니다.  응용 프로그램 개체는 하나 이상의 서비스 주체 개체를 만들기 위해 템플릿이나 청사진으로 사용 됩니다.  응용 프로그램이 사용 되는 모든 테 넌 트에 서비스 주체가 생성 됩니다. 개체 지향 프로그래밍의 클래스와 마찬가지로, 응용 프로그램 개체에는 생성 된 모든 서비스 사용자 (또는 응용 프로그램 인스턴스)에 적용 되는 몇 가지 정적 속성이 있습니다.

응용 프로그램 개체는 응용 프로그램에 액세스 하기 위해 서비스에서 토큰을 발급 하는 방법, 응용 프로그램에서 액세스 해야 하는 리소스 및 응용 프로그램에서 수행할 수 있는 작업의 세 가지 측면을 설명 합니다.

[Azure Portal][AZURE-Portal] 의 **앱 등록** 블레이드는 홈 테 넌 트에서 응용 프로그램 개체를 나열 하 고 관리 하는 데 사용 됩니다.

![앱 등록 블레이드](./media/app-objects-and-service-principals/app-registrations-blade.png)

Microsoft Graph [응용 프로그램 엔터티][MS-Graph-App-Entity] 는 응용 프로그램 개체의 속성에 대 한 스키마를 정의 합니다.

## <a name="service-principal-object"></a>서비스 주체 개체
Azure AD 테넌트에 의해 보안이 유지되는 리소스에 액세스하려면 액세스해야 하는 엔터티를 보안 주체로 나타내야 합니다. 이 요구 사항은 사용자 (사용자 계정) 및 응용 프로그램 (서비스 사용자) 모두에 적용 됩니다. 보안 주체는 Azure AD 테넌트의 사용자/애플리케이션에 대한 액세스 정책 및 권한을 정의합니다. 이를 통해 로그인 동안의 사용자/애플리케이션의 인증 및 리소스 액세스 동안의 권한 부여 같은 핵심 기능이 허용됩니다.

서비스 사용자는 단일 테 넌 트 또는 디렉터리에 있는 전역 응용 프로그램 개체의 로컬 표현 또는 응용 프로그램 인스턴스입니다. 서비스 주체는 응용 프로그램 개체에서 만든 구체적인 인스턴스이고 해당 응용 프로그램 개체의 특정 속성을 상속 합니다.  응용 프로그램이 사용 되는 각 테 넌 트에 서비스 주체가 만들어지고 전역적으로 고유한 앱 개체를 참조 합니다.  서비스 주체 개체는 앱이 특정 테 넌 트에서 실제로 수행할 수 있는 작업, 앱에 액세스할 수 있는 사용자 및 앱이 액세스할 수 있는 리소스를 정의 합니다.

애플리케이션이 테넌트의 리소스에 액세스하기 위한 권한을 받으면(등록 또는 [동의](developer-glossary.md#consent) 시) 서비스 주체 개체가 만들어집니다. [Azure PowerShell](howto-authenticate-service-principal-powershell.md), Azure CLI, [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?view=graph-rest-1.0&tabs=http), [Azure Portal][AZURE-Portal]및 기타 도구를 사용 하 여 테 넌 트에서 서비스 사용자 개체를 만들 수도 있습니다.  포털을 사용 하는 경우 응용 프로그램을 등록할 때 서비스 주체가 자동으로 생성 됩니다.

포털의 **엔터프라이즈 응용 프로그램** 블레이드는 테 넌 트에서 서비스 사용자를 나열 하 고 관리 하는 데 사용 됩니다. 서비스 사용자의 권한, 사용자 동의한 권한, 해당 동의를 수행한 사용자 및 로그인 정보 등을 볼 수 있습니다.

![엔터프라이즈 앱 블레이드](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

Microsoft Graph [serviceprincipal 엔터티][MS-Graph-Sp-Entity] 는 서비스 주체 개체의 속성에 대 한 스키마를 정의 합니다.

## <a name="relationship-between-application-objects-and-service-principals"></a>응용 프로그램 개체와 서비스 주체 간의 관계

응용 프로그램 개체는 모든 테 넌 트에서 사용할 응용 프로그램의 *전역* 표현이 며, 서비스 주체는 특정 테 넌 트에서 사용 하기 위한 *로컬* 표현입니다.

애플리케이션 개체는 해당 서비스 주체 개체 만들기에 사용하기 위해 공통의 기본 속성이 *파생*되는 템플릿으로 제공됩니다. 따라서 애플리케이션 개체는 소프트웨어 애플리케이션과 1:1 관계가 있으며 해당 서비스 주체 개체와 1:다 관계가 있습니다.

애플리케이션이 사용될 각 테넌트에 서비스 주체를 만들어서 테넌트가 보호하는 리소스에 대한 로그인 및/또는 액세스를 위한 ID를 설정할 수 있어야 합니다. 단일 테넌트 애플리케이션에는 해당 홈 테넌트에 하나의 서비스 주체만 있으며 애플리케이션 등록 중에 생성하고 동의합니다. 다중 테넌트 웹 애플리케이션/API에도 해당 테넌트의 사용자가 사용을 동의한 각 테넌트에 생성된 하나의 서비스 주체가 있습니다.

> [!NOTE]
> 애플리케이션 개체에 대해 이뤄진 모든 변경은 또한 애플리케이션의 홈 테넌트(그것이 등록된 테넌트)에만 있는 해당 서비스 주체 개체에도 반영됩니다. 다중 테넌트 애플리케이션의 경우 [애플리케이션 액세스 패널](https://myapps.microsoft.com)을 통해 액세스를 제거하고 다시 액세스 권한이 부여될 때까지 애플리케이션 개체의 변경 내용은 모든 소비자 테넌트의 서비스 주체 개체에 반영되지 않습니다.
>
> 또한 기본적으로 네이티브 애플리케이션이 다중 테넌트로 등록됩니다.

## <a name="example"></a>예제

다음 다이어그램은 **HR 앱**이라는 샘플 다중 테넌트 애플리케이션의 컨텍스트에서 애플리케이션의 애플리케이션 개체와 해당 서비스 주체 개체를 보여 줍니다. 이 예제 시나리오에는 다음 세 가지 Azure AD 테넌트가 있습니다.

- **Adatum** - **HR 앱** 을 개발한 회사에서 사용 하는 테 넌 트
- **Contoso** - **HR 앱** 의 소비자 인 contoso 조직에서 사용 하는 테 넌 트
- **Fabrikam** - **HR 앱**의 또 다른 소비자인 Fabrikam 조직에서 사용하는 테넌트

![앱 개체와 서비스 주체 개체 간의 관계](./media/app-objects-and-service-principals/application-objects-relationship.svg)

이 예제 시나리오는 다음과 같이 이루어져 있습니다.

| 단계 | 설명 |
|------|-------------|
| 1    | 애플리케이션의 홈 테넌트에서 애플리케이션 및 서비스 주체 개체를 만드는 과정입니다. |
| 2    | Contoso 관리자와 Fabrikam 관리자가 전적으로 동의한 경우 서비스 주체 개체가 회사의 Azure AD 테넌트에 생성되고 관리자가 부여한 사용 권한이 할당됩니다. 또한 사용자가 개별 사용에 대한 동의를 할 수 있게 HR 앱이 구성/설계될 수 있습니다. |
| 3    | HR 애플리케이션의 각 소비자 테넌트(예: Contoso 및 Fabrikam)에 고유한 서비스 주체 개체가 제공됩니다. 각각은 런타임에 애플리케이션 인스턴스의 사용을 나타내며, 이는 해당 관리자가 동의한 사용 권한으로 관리됩니다. |

## <a name="next-steps"></a>다음 단계

- [Microsoft Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer) 를 사용 하 여 응용 프로그램 및 서비스 주체 개체를 모두 쿼리할 수 있습니다.
- OData [응용 프로그램 엔터티로][MS-Graph-App-Entity]표시 되는 Microsoft Graph API, [Azure Portal의][AZURE-Portal] 응용 프로그램 매니페스트 편집기 또는 [Azure AD PowerShell cmdlet](/powershell/azure/?view=azureadps-2.0)을 사용 하 여 응용 프로그램의 응용 프로그램 개체에 액세스할 수 있습니다.
- OData [serviceprincipal 엔터티][MS-Graph-Sp-Entity]를 통해 표시 된 대로 Microsoft Graph API 또는 [Azure AD PowerShell cmdlet](/powershell/azure/?view=azureadps-2.0)을 통해 응용 프로그램의 서비스 주체 개체에 액세스할 수 있습니다.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
---
title: Azure Active Directory 테넌트 만들기 | Microsoft Docs
description: Azure AD 테넌트를 만들어 애플리케이션 등록 및 빌드에 사용하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 74747e1bc5247e2a95a180b7361eafe8ca795bc2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160709"
---
# <a name="quickstart-set-up-a-tenant"></a>빠른 시작: 테넌트 설정

Microsoft ID 플랫폼을 통해 개발자는 다양한 사용자 지정 Microsoft 365 환경 및 ID를 대상으로 하는 앱을 빌드할 수 있습니다. Microsoft ID 플랫폼 사용을 시작하려면 앱을 등록 및 관리하고, Microsoft 365 데이터에 액세스하고, 사용자 지정 조건부 액세스 및 테넌트 제한을 배포할 수 있는 Azure AD 테넌트라고도 하는 환경에 액세스해야 합니다.

테넌트는 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다.

각 Azure AD 테넌트는 고유하고 다른 Azure AD 테넌트와 별도이며 회사 및 학교 ID, 고객 ID(Azure AD B2C 테넌트인 경우) 및 앱 등록의 고유한 표현을 갖습니다. 테넌트 내의 앱 등록은 오직 테넌트 또는 모든 테넌트 내의 계정의 인증을 허용할 수 있습니다.

## <a name="determining-environment-type"></a>환경 유형 결정

두 가지 유형의 환경을 만들 수 있습니다. 필요한 것을 결정하는 것은 오로지 앱에서 인증하는 사용자의 형식을 기반으로 합니다.

* 회사 및 학교(Azure AD 계정) 또는 Microsoft 계정(예: outlook.com 및 live.com)
* 소셜 및 로컬 계정(Azure AD B2C)

빠른 시작은 빌드하려는 앱의 유형에 따라 두 가지 시나리오로 나뉩니다. ID 형식 대상 지정에 도움이 더 필요한 경우 [Microsoft ID 플랫폼 정보](about-microsoft-identity-platform.md)를 살펴보세요.

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>회사 및 학교 계정 또는 개인 Microsoft 계정

### <a name="use-an-existing-tenant"></a>기존 테넌트 사용

대부분의 개발자는 이미 Azure AD 테넌트(예: Microsoft 365 또는 Azure 구독)와 연결된 서비스 또는 구독을 통해 테넌트를 확보하고 있습니다.

1. 테넌트를 확인하려면 애플리케이션을 관리하는 데 사용하려는 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 오른쪽 위 모서리를 확인합니다. 테넌트가 있으면 자동으로 로그인되고 계정 이름 바로 아래에 테넌트 이름이 표시됩니다.
   * Azure Portal의 오른쪽 위에 있는 사용자의 계정 이름을 가리켜 사용자의 이름, 이메일, 디렉터리/테넌트 ID(GUID) 및 사용자의 도메인을 표시합니다.
   * 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다.

> [!TIP]
> 테넌트 ID를 찾아야 하는 경우 다음을 수행할 수 있습니다.
> * 사용자의 계정 이름을 가리켜 디렉터리/테넌트 ID를 가져오거나
> * Azure Portal에서 **Azure Active Directory > 속성 > 디렉터리 ID**를 선택합니다.

계정과 연결된 기존 테넌트가 없으면, 계정 이름 아래에 GUID가 표시되며 다음 섹션의 단계를 따를 때까지 앱 등록과 같은 작업을 수행할 수 없습니다.

### <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기

아직 Azure AD 테넌트가 없거나 개발을 위한 새 테넌트를 만들려면 [빠른 시작](../fundamentals/active-directory-access-create-new-tenant.md)을 참조하거나 [디렉터리 만들기 환경](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)을 따르세요. 새 테넌트를 만들려면 다음 정보를 제공해야 합니다.

- **조직 이름**
- **초기 도메인** - *.onmicrosoft.com의 일부가 됩니다. 나중에 더 도메인을 사용자 지정할 수 있습니다.
- **국가 또는 지역**

> [!NOTE]
> 테넌트의 이름을 지정할 때는 영숫자를 사용하세요. 특수 문자는 허용되지 않습니다. 이름은 256자를 초과하면 안 됩니다.

## <a name="social-and-local-accounts"></a>소셜 및 로컬 계정

소셜 및 로컬 계정에 로그인하는 앱 빌드를 시작하려면 Azure AD B2C 테넌트를 만들어야 합니다. 시작하려면 [Azure AD B2C 테넌트 만들기](../../active-directory-b2c/tutorial-create-tenant.md)를 따릅니다.

## <a name="next-steps"></a>다음 단계

* 코딩 빠른 시작을 시도하고 사용자 인증을 시작합니다. 
* 더 자세한 코드 샘플은 설명서의 **자습서** 섹션을 참조하세요.
* 클라우드에 앱을 배포하시겠습니까? [Azure에 컨테이너 배포](https://docs.microsoft.com/azure/index#pivot=products&panel=containers)를 참조하세요. 

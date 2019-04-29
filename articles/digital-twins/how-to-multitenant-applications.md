---
title: Azure Digital Twins에서 다중 테넌트 애플리케이션 사용 | Microsoft Docs
description: Azure Digital Twins에 대한 다중 테넌트 Azure Active Directory 애플리케이션을 구성하는 방법
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: mavoge
ms.openlocfilehash: 2b4f9bf87122f047e496dca1dbd425db8ad7c16c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926054"
---
# <a name="enable-multitenant-applications-with-azure-digital-twins"></a>Azure Digital Twins에서 다중 테넌트 애플리케이션 사용

Azure Digital Twins를 빌드하는 솔루션 개발자는 단일 서비스 또는 솔루션을 사용하여 여러 고객을 지원하려고 할 수 있습니다. 사실 *다중 테넌트* 애플리케이션은 가장 일반적인 Azure Digital Twins 구성 중 하나입니다.

이 문서에서는 여러 Azure Active Directory 테넌트 및 고객을 지원하도록 Azure Digital Twins 앱을 구성하는 방법을 설명합니다.

## <a name="multitenancy"></a>다중 테넌시

*다중 테넌트* 리소스는 여러 고객을 지원하는 단일 프로비전 인스턴스입니다. 각 고객은 고유한 독립 데이터 및 권한을 갖고 있습니다. 각 고객의 환경은 애플리케이션의 해당 "보기"가 고유하도록 다른 고객의 환경에서 격리됩니다.

다중 테넌시에 대한 자세한 내용은 [Azure의 다중 테넌트 애플리케이션](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)을 참조하세요.

## <a name="problem-scenario"></a>문제 시나리오

이 시나리오에서는 Azure Digital Twins 솔루션을 빌드하는 개발자(**개발자**) 및 해당 솔루션을 사용하는 고객(**고객**)을 고려합니다.

- **개발자**에게는 Azure Active Directory 테넌트를 사용하는 Azure 구독이 있습니다.
- **개발자**는 Azure 구독에 Azure Digital Twins 인스턴스를 배포합니다. Azure Active Directory는 **개발자**의 Azure Active Directory 테넌트에 서비스 사용자를 자동으로 생성했습니다.
- **개발자**의 Azure Active Directory 테넌트 내의 사용자는 Azure Digital Twins 서비스에서 [OAuth 2.0 토큰을 획득할](./security-authenticating-apis.md) 수 있습니다.
- **개발자**는 이제 Azure Digital Twins 관리 API와 직접 통합되는 모바일 앱을 만듭니다.
- **개발자**는 **고객**이 모바일 애플리케이션을 사용할 수 있도록 허용합니다.
- **고객**은 **개발자**의 애플리케이션 내에서 Azure Digital Twins 관리 API를 사용할 수 있도록 권한이 부여되어야 합니다.

문제:

- **고객**이 **개발자**의 애플리케이션에 로그인하면 앱은 **고객**의 사용자에 대한 토큰을 취득하여 Azure Digital Twins 관리 API에 인증할 수 없습니다.
- Azure Digital Twins가 **고객**의 디렉터리 내에서 인식되지 않음을 나타내는 예외가 Azure Active Directory에서 발급됩니다.

## <a name="problem-solution"></a>문제 솔루션

이전 문제 시나리오를 해결하려면 **고객**의 Azure Active Directory 테넌트 내에서 Azure Digital Twins 서비스 사용자를 만드는 다음 작업이 필요합니다.

- **고객**에게 Azure Active Directory 테넌트를 사용하는 Azure 구독이 아직 없는 경우:

  - **고객**의 Azure Active Directory 테넌트 관리자는 [종량제 Azure 구독](https://azure.microsoft.com/offers/ms-azr-0003p/)을 취득해야 합니다.
  - 그런 다음, **고객**의 Azure Active Directory 테넌트 관리자는 [테넌트를 새 구독에 연결](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)해야 합니다.

- [Azure Portal](https://portal.azure.com)에서 **고객**의 Azure Active Directory 테넌트 관리자는 다음 단계를 수행합니다.

  1. **구독**을 엽니다.
  1. **개발자**의 애플리케이션에서 사용할 수 있는 Azure Active Directory 테넌트를 포함한 구독을 선택합니다.

     ![Azure Active Directory 구독][1]

  1. **리소스 공급자**를 선택합니다.
  1. **Microsoft.IoTSpaces**를 검색합니다.
  1. **등록**을 선택합니다.

     ![Azure Active Directory 리소스 공급 기업][2]
  
## <a name="next-steps"></a>다음 단계

- Azure Digital Twins에서 사용자 정의 함수를 사용하는 방법에 대한 자세한 내용은 [Azure Digital Twins 사용자 정의 함수를 만드는 방법](./how-to-user-defined-functions.md)을 참조하세요.

- 역할 기반 액세스 제어를 사용하여 역할 할당에서 애플리케이션을 추가로 보호하는 방법을 알아보려면 [Azure Digital Twins 역할 기반 액세스 제어를 만들고 관리하는 방법](./security-create-manage-role-assignments.md)을 참조하세요.

<!-- Images -->
[1]: media/multitenant/ad-subscriptions.png
[2]: media/multitenant/ad-resource-providers.png

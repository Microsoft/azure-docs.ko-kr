---
title: Azure AD 인증 및 국가별 클라우드 | Azure
titleSuffix: Microsoft identity platform
description: 국가별 클라우드의 앱 등록 및 인증 엔드포인트에 관해 알아봅니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: negoe
ms.reviewer: negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: d27498748f03fb101d165351ec80c77d57f78f1a
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141502"
---
# <a name="national-clouds"></a>국가별 클라우드

국가별 클라우드는 물리적으로 격리된 Azure 인스턴스입니다. 이러한 Azure 지역은 지리적 경계 내에서 데이터 상주, 주권 및 준수 요구 사항이 적용되도록 설계되었습니다.

글로벌 클라우드를 포함하여 Azure AD(Azure Active Directory)는 다음 국가별 클라우드에 배포됩니다.  

- Azure Government
- Azure Germany
- Azure China 21Vianet

국가별 클라우드는 고유하며 Azure 글로벌과는 별개의 환경입니다. 이러한 환경을 위한 애플리케이션을 개발하는 동안 주요 차이점을 알고 있어야 합니다. 애플리케이션 등록, 토큰 획득 및 엔드포인트 구성 등의 차이점이 있습니다.

## <a name="app-registration-endpoints"></a>앱 등록 엔드포인트

각 국가별 클라우드마다 별도의 Azure Portal이 있습니다. 국가별 클라우드에서 Microsoft ID 플랫폼과 애플리케이션을 통합하려면 환경별 각 Azure Portal에서 애플리케이션을 별도로 등록해야 합니다.

다음 표에는 각 국가별 클라우드에 대한 애플리케이션을 등록하는 데 사용되는 Azure AD 엔드포인트의 기본 URL이 나열되어 있습니다.

| 국가별 클라우드 | Azure AD 포털 엔드포인트 |
|----------------|--------------------------|
| 미국 정부의 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 21Vianet으로 운영되는 Azure AD China | `https://portal.azure.cn` |
| Azure AD(글로벌 서비스) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 인증 엔드포인트

모든 국가별 클라우드는 각 환경의 사용자를 개별적으로 인증하며 별도의 인증 엔드포인트를 포함합니다.

다음 표에는 각 국가별 클라우드용 토큰을 얻는 데 사용되는 Azure AD 엔드포인트의 기본 URL이 나와 있습니다.

| 국가별 클라우드 | Azure AD 인증 엔드포인트 |
|----------------|-------------------------|
| 미국 정부의 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 21Vianet으로 운영되는 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD(글로벌 서비스)| `https://login.microsoftonline.com` |

적절한 지역별 기본 URL을 사용하여 Azure AD 권한 부여 또는 토큰 엔드포인트에 대한 요청을 구성할 수 있습니다. 예를 들어 Azure Germany의 경우

  - 권한 부여 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/v2.0/authorize`입니다.
  - 토큰 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/v2.0/token`입니다.

단일 테넌트 애플리케이션의 경우 이전 URL의 "common"을 테넌트 ID 또는 이름으로 바꿉니다. 예제는 `https://login.microsoftonline.de/contoso.com`입니다.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

국가별 클라우드 환경에서 Microsoft Graph API를 호출하는 방법을 알아보려면 [국가별 클라우드 배포의 Microsoft Graph](/graph/deployments)로 이동하세요.

> [!IMPORTANT]
> 글로벌 서비스의 특정 지역에서 제공되는 특정 서비스와 기능이 모든 국가별 클라우드에서 제공되지 않을 수도 있습니다. 사용 가능한 서비스를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)으로 이동하세요.

Microsoft ID 플랫폼을 사용하여 애플리케이션을 빌드하는 방법에 대해 알아보려면 [MSAL(Microsoft 인증 라이브러리) 자습서](msal-national-cloud.md)를 따르세요. 특히 이 앱은 사용자를 로그인하고 Microsoft Graph API를 호출하는 액세스 토큰을 가져옵니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure 독일](../../germany/index.yml)
- [Azure AD 인증 기본 사항](./authentication-vs-authorization.md)
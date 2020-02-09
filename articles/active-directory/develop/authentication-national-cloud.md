---
title: 국가별 클라우드 & Azure AD 인증 | Microsoft
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
ms.custom: aaddev
ms.openlocfilehash: 20a053369149dc29d6485c49bb091a75bb9fb591
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698019"
---
# <a name="national-clouds"></a>국가별 클라우드

국가별 클라우드는 실제로 격리 된 Azure 인스턴스입니다. 이러한 Azure 지역은 지리적 경계 내에서 데이터 상주, 주권 및 준수 요구 사항이 적용되도록 설계되었습니다.

글로벌 클라우드를 포함 하 여 Azure AD (Azure Active Directory)는 다음 국가 클라우드에 배포 됩니다.  

- Azure Government
- Azure Germany
- Azure China 21Vianet

국가별 클라우드는 고유 하며 Azure global과는 별개의 환경입니다. 이러한 환경을 위해 응용 프로그램을 개발 하는 동안 주요 차이점을 알고 있어야 합니다. 응용 프로그램 등록, 토큰 획득 및 엔드포인트 구성 등의 차이점이 있습니다.

## <a name="app-registration-endpoints"></a>앱 등록 엔드포인트

국가별 클라우드 각각에 대해 별도의 Azure Portal 있습니다. 응용 프로그램을 국가별 클라우드의 Microsoft id 플랫폼과 통합 하려면 환경에 고유한 각 Azure Portal에 응용 프로그램을 별도로 등록 해야 합니다.

다음 표에는 각 국가 클라우드의 응용 프로그램을 등록 하는 데 사용 되는 Azure AD 엔드포인트에 대 한 기본 Url이 나열 되어 있습니다.

| 국가별 클라우드 | Azure AD 포털 엔드포인트 |
|----------------|--------------------------|
| 미국 정부의 Azure AD | `https://portal.azure.us` |
| Azure AD Germany | `https://portal.microsoftazure.de` |
| 21Vianet으로 운영되는 Azure AD China | `https://portal.azure.cn` |
| Azure AD(글로벌 서비스) |`https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Azure AD 인증 엔드포인트

모든 국가별 클라우드는 각 환경의 사용자를 개별적으로 인증하며 별도의 인증 엔드포인트를 포함합니다.

다음 표에는 각 국가별 클라우드의 토큰을 획득 하는 데 사용 되는 Azure AD 엔드포인트에 대 한 기본 Url이 나열 되어 있습니다.

| 국가별 클라우드 | Azure AD 인증 엔드포인트 |
|----------------|-------------------------|
| 미국 정부의 Azure AD | `https://login.microsoftonline.us` |
| Azure AD Germany| `https://login.microsoftonline.de` |
| 21Vianet으로 운영되는 Azure AD China | `https://login.chinacloudapi.cn` |
| Azure AD(글로벌 서비스)| `https://login.microsoftonline.com` |

적절 한 지역별 기본 URL을 사용 하 여 Azure AD 인증 또는 토큰 엔드포인트에 대 한 요청을 구성할 수 있습니다. 예를 들어 Azure Germany의 경우

  - 권한 부여 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/authorize`입니다.
  - 토큰 공통 엔드포인트는 `https://login.microsoftonline.de/common/oauth2/token`입니다.

단일 테넌트 응용 프로그램의 경우 이전 Url의 "일반"을 테넌트 ID 또는 이름으로 바꿉니다. 예제는 `https://login.microsoftonline.de/contoso.com`입니다.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

국가별 클라우드 환경에서 Microsoft Graph Api를 호출 하는 방법에 대 한 자세한 내용은 [국가별 클라우드 배포의 Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/deployments)를 참조 하세요.

> [!IMPORTANT]
> 일부 국가별 클라우드에서는 글로벌 서비스의 특정 지역에 있는 특정 서비스 및 기능을 사용 하지 못할 수 있습니다. 사용할 수 있는 서비스를 확인 하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast)을 참조 하세요.

Microsoft id 플랫폼을 사용 하 여 응용 프로그램을 빌드하는 방법에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리) 자습서](msal-national-cloud.md)를 참조 하세요. 특히이 앱은 사용자를 로그인 하 고 Microsoft Graph API를 호출 하는 액세스 토큰을 가져옵니다.

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure 독일](https://docs.microsoft.com/azure/germany/)
- [Azure AD 인증 기본 사항](authentication-scenarios.md)

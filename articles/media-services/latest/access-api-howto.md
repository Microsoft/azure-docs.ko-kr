---
title: Azure AD 인증 시작
description: Azure AD(Azure Active Directory) 인증에 액세스하여 Azure Media Services API를 사용하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00808c25ac84da852cce6169fb210767ee2b56cf
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265884"
---
# <a name="get-credentials-to-access-media-services-api"></a>Media Services API에 액세스할 수 있는 자격 증명 가져오기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure AD 인증을 사용하여 Azure Media Services API에 액세스할 때 두 가지 인증 옵션이 제공됩니다.

- **서비스 주체 인증**(권장)

    서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로 서비스)을 실행하는 앱입니다.
- **사용자 인증**

    Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 

이 문서에서는 Media Services API에 액세스하기 위한 자격 증명을 가져오는 단계를 설명합니다. 다음 탭 중에서 선택합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](create-account-howto.md)를 참조하세요.

## <a name="portal"></a>[포털](#tab/portal/)

### <a name="api-access"></a>API 액세스

**API 액세스** 페이지에서 API에 연결하는 데 사용할 인증 방법을 선택할 수 있습니다. 또한 이 페이지는 API에 연결하는 데 필요한 값을 제공합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Media Services 계정을 선택합니다.
2. 왼쪽 탐색 모음에서 **API 액세스 (새)** 블레이드를 선택 합니다.
3. **Media Services API에 연결**에서 연결하려는 Media Services API 버전(V3이 서비스의 최신 버전)을 선택합니다.

### <a name="service-principal-authentication--recommended"></a>서비스 주체 인증(권장)

Azure AD(Azure Active Directory) 앱 및 비밀을 사용하여 서비스를 인증합니다. Media Services API를 호출하는 중간 계층 서비스에 권장됩니다. 예로는 Web Apps, Functions, Logic Apps, API, 마이크로 서비스가 있습니다. 권장되는 인증 방법입니다.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Azure AD 앱 및 비밀 관리

**AAD 앱 및 비밀 관리** 섹션을 사용하여 새 Azure AD 앱을 선택하거나 만들고 비밀을 생성할 수 있습니다. 보안상의 이유로 블레이드가 닫힌 후에는 비밀을 표시할 수 없습니다. 애플리케이션은 인증에 애플리케이션 ID 및 비밀을 사용하여 미디어 서비스에 대한 유효한 토큰을 가져옵니다.

Azure AD 테넌트로 애플리케이션을 등록하고 애플리케이션을 Azure 구독의 역할에 할당할 수 있는 충분한 권한이 있는지 확인하세요. 자세한 내용은 [필요한 권한](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)을 참조하세요.

#### <a name="connect-to-media-services-api"></a>Media Services API에 연결

**Media Services API에 연결**은 서비스 주체 애플리케이션에 연결하는 데 사용하는 값을 제공합니다. 텍스트 값을 가져오거나 JSON 또는 XML 블록을 복사할 수 있습니다.

### <a name="user-authentication"></a>사용자 인증

이 옵션은 앱을 사용하여 Media Services 리소스와 상호 작용하는 Azure Active Directory의 직원 또는 구성원을 인증하는 데 사용할 수 있습니다. 대화형 애플리케이션은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시합니다. 이 인증 방법은 관리 애플리케이션에만 사용해야 합니다.

#### <a name="connect-to-media-services-api"></a>Media Services API에 연결

자격 증명을 복사하여 **Media Services API에 연결** 섹션에서 사용자 애플리케이션을 연결합니다. 텍스트 값을 가져오거나 JSON 또는 XML 블록을 복사할 수 있습니다.

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>다음 단계

[자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)

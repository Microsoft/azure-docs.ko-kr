---
title: Azure Portal을 사용하여 Azure AD 인증 시작 | Microsoft Docs
description: Azure Portal을 사용하여 Azure AD(Azure Active Directory) 인증에 액세스하고 Azure Media Services API를 사용하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3d6c494232456b4819c9275d66fa6f7ab5de9b77
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260733"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure AD 인증 시작

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](../latest/index.yml)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Azure Portal을 사용하여 Azure AD(Azure Active Directory) 인증에 액세스하고 Azure Media Services API에 액세스하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 계정. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.

Azure Media Services와 함께 Azure AD 인증을 사용할 때 두 가지 인증 옵션이 제공됩니다.

- **서비스 사용자 인증**. 서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로 서비스)을 실행하는 앱입니다.
- **사용자 인증**. Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 

## <a name="access-the-media-services-api"></a>Media Services API 액세스

이 페이지에서 API에 연결 하는 데 사용 하려는 인증 방법을 선택할 수 있습니다. 또한 이 페이지는 API에 연결하는 데 필요한 값을 제공합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Media Services 계정을 선택합니다.
2. Media Services API에 연결하는 방법을 선택합니다.
3. **MEDIA SERVICES api에 연결**에서 연결 하려는 Media Services api 버전을 선택 합니다.

## <a name="service-principal-authentication--recommended"></a>서비스 주체 인증(권장)

Azure AD(Azure Active Directory) 앱 및 비밀을 사용하여 서비스를 인증합니다. Media Services API를 호출하는 중간 계층 서비스에 권장됩니다. 예로는 Web Apps, Functions, Logic Apps, API, 마이크로 서비스가 있습니다. 권장되는 인증 방법입니다.

### <a name="manage-your-azure-ad-app-and-secret"></a>Azure AD 앱 및 비밀 관리

**AAD 앱 및 비밀 관리** 섹션을 사용하여 새 Azure AD 앱을 선택하거나 만들고 비밀을 생성할 수 있습니다. 보안상의 이유로 블레이드가 닫힌 후에는 비밀을 표시할 수 없습니다. 애플리케이션은 인증에 애플리케이션 ID 및 비밀을 사용하여 미디어 서비스에 대한 유효한 토큰을 가져옵니다.

Azure AD 테넌트로 애플리케이션을 등록하고 애플리케이션을 Azure 구독의 역할에 할당할 수 있는 충분한 권한이 있는지 확인하세요. 자세한 내용은 [필요한 권한](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)을 참조하세요.

### <a name="connect-to-media-services-api"></a>Media Services API에 연결

**Media Services API에 연결**은 서비스 주체 애플리케이션에 연결하는 데 사용하는 값을 제공합니다. 텍스트 값을 가져오거나 JSON 또는 XML 블록을 복사할 수 있습니다.

## <a name="user-authentication"></a>사용자 인증

이 옵션은 앱을 사용하여 Media Services 리소스와 상호 작용하는 Azure Active Directory의 직원 또는 구성원을 인증하는 데 사용할 수 있습니다. 대화형 애플리케이션은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시합니다. 이 인증 방법은 관리 애플리케이션에만 사용해야 합니다.

### <a name="connect-to-media-services-api"></a>Media Services API에 연결

자격 증명을 복사하여 **Media Services API에 연결** 섹션에서 사용자 애플리케이션을 연결합니다. 텍스트 값을 가져오거나 JSON 또는 XML 블록을 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-portal-upload-files.md) 시작

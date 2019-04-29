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
ms.openlocfilehash: 37c5a97d5f7a872e916f223f2cdfc98bcdec3cad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61130752"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure AD 인증 시작

Azure Portal을 사용하여 Azure AD(Azure Active Directory) 인증에 액세스하고 Azure Media Services API에 액세스하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

- Azure 계정. 계정이 없는 경우 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.
- [Azure AD 인증으로 Azure Media Services API 액세스 개요](media-services-use-aad-auth-to-access-ams-api.md)를 검토해야 합니다. 

Azure Media Services와 함께 Azure AD 인증을 사용할 때 두 가지 인증 옵션이 제공됩니다.

- **사용자 인증**. Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 
- **서비스 주체 인증**. 서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로 서비스)을 실행하는 앱입니다.

> [!IMPORTANT]
> 현재 Media Services는 Azure Access Control 서비스 인증 모델을 지원합니다. 그러나 Access Control 권한 부여는 2018년 6월 1일부로 더 이상 사용되지 않을 예정입니다. 가능한 빨리 Azure AD 인증 모델로 마이그레이션하는 것이 좋습니다.

## <a name="select-the-authentication-method"></a>인증 방법 선택

1. [Azure Portal](https://portal.azure.com/)에서 Media Services 계정을 선택합니다.
2. Media Services API에 연결하는 방법을 선택합니다.

    ![연결 방법 선택 페이지](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>사용자 인증

사용자 인증 옵션을 사용하여 Media Service API에 연결하려면 클라이언트 앱에서 다음 매개 변수가 있는 Azure AD 토큰을 요청해야 합니다.  

* Azure AD 테넌트 엔드포인트
* Media Services 리소스 URI
* Media Services(원시) 애플리케이션 클라이언트 ID 
* Media Services(원시) 애플리케이션 리디렉션 URI 
* REST Media Services의 리소스 URI

**Media Services API 및 사용자 인증** 페이지에서 이러한 매개 변수에 대한 값을 가져올 수 있습니다. 

![사용자 인증으로 연결 페이지](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Media Services Microsoft .NET SDK를 사용하여 Media Services API에 연결하는 경우 SDK의 일부로 필요한 값이 제공됩니다. 자세한 내용은 [Azure AD 인증을 사용하여 .NET으로 Azure Media Services API 액세스](media-services-dotnet-get-started-with-aad.md)를 참조하세요.

Media Services .NET 클라이언트 SDK를 사용하지 않는 경우 앞에서 설명한 매개 변수를 사용하여 Azure AD 토큰 요청을 수동으로 만들어야 합니다. 자세한 내용은 [Azure AD 인증 라이브러리를 사용하여 Azure AD 토큰 가져오기](../../active-directory/develop/active-directory-authentication-libraries.md)를 참조하세요.

## <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 옵션을 사용하여 Media Services API에 연결하려면 중간 계층 앱(웹 API 또는 웹 애플리케이션)에서 다음 매개 변수가 있는 Azure AD 토큰을 요청해야 합니다.  

* Azure AD 테넌트 엔드포인트
* Media Services 리소스 URI 
* REST Media Services의 리소스 URI
* Azure AD 애플리케이션 값: **클라이언트 ID** 및 **클라이언트 암호**

**서비스 주체로 Media Services API에 연결** 페이지에서 이러한 매개 변수에 대한 값을 가져올 수 있습니다. 이 페이지에서 새 Azure AD 애플리케이션을 만들거나 기존 항목을 선택합니다. Azure AD 앱을 선택한 후에는 클라이언트 ID(애플리케이션 ID)를 가져오고 클라이언트 암호(키) 값을 생성합니다. 

![서비스 주체로 연결 페이지](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

**서비스 주체** 블레이드가 열리면 다음 기준을 충족하는 첫 번째 Azure AD 애플리케이션이 선택됩니다.

- 등록된 Azure AD 애플리케이션입니다.
- 계정에 대한 참가자 또는 소유자 역할 기반 Access Control 권한을 포함합니다.

Azure AD 앱을 만들거나 선택한 후에는 클라이언트 암호(키) 및 클라이언트 ID(애플리케이션 ID)를 만들고 복사할 수 있습니다. 이 시나리오에서 액세스 토큰을 가져오는 데는 클라이언트 암호 및 클라이언트 ID가 필요합니다.

도메인에 Azure AD 앱을 만들기 위한 권한이 없는 경우 블레이드의 Azure AD 앱 컨트롤이 표시되지 않고 경고 메시지가 표시됩니다.

Media Services .NET SDK를 사용하여 Media Services API에 연결하는 경우 [Azure AD 인증을 사용하여 .NET으로 Azure Media Services API 액세스](media-services-dotnet-get-started-with-aad.md)를 참조하세요.

Media Services .NET 클라이언트 SDK를 사용하지 않는 경우 앞에서 설명한 매개 변수를 사용하여 Azure AD 토큰 요청을 수동으로 만들어야 합니다. 자세한 내용은 [Azure AD 인증 라이브러리를 사용하여 Azure AD 토큰 가져오기](../../active-directory/develop/active-directory-authentication-libraries.md)를 참조하세요.

### <a name="get-the-client-id-and-client-secret"></a>클라이언트 ID 및 클라이언트 암호 가져오기

기존 Azure AD 앱을 선택하거나 새 항목을 만드는 옵션을 선택하면 다음 단추가 나타납니다.

![[사용 권한 관리] 단추 및 [애플리케이션 관리] 단추](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Azure AD 애플리케이션 블레이드를 열려면 **애플리케이션 관리**를 클릭합니다. **애플리케이션 관리** 블레이드에서 앱의 클라이언트 ID(애플리케이션 ID)를 가져올 수 있습니다. 클라이언트 암호(키)를 생성하려면 **키**를 선택합니다.

![애플리케이션 블레이드 키 관리 옵션](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>사용 권한 및 애플리케이션 관리

Azure AD 애플리케이션을 선택한 후에는 애플리케이션 및 사용 권한을 관리할 수 있습니다. 다른 애플리케이션에 액세스하도록 Azure AD 애플리케이션을 설정하려면 **사용 권한 관리**를 클릭합니다. 키 및 회신 URL 변경과 같은 관리 작업이나 애플리케이션의 매니페스트를 편집하려면 **애플리케이션 관리**를 클릭합니다.

### <a name="edit-the-apps-settings-or-manifest"></a>앱의 설정 또는 매니페스트 편집

앱의 설정 또는 매니페스트를 편집하려면 **애플리케이션 관리**를 클릭합니다.

![애플리케이션 관리 페이지](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-portal-upload-files.md) 시작

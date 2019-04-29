---
title: Azure Active Directory 인증으로 Azure Media Services API 액세스 | Microsoft Docs
description: Azure Media Services API에 대한 액세스를 인증하는 데 Azure AD(Azure Active Directory)를 사용하기 위해 수행할 단계와 개념에 대해 알아봅니다.
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
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: d80a58f1886ecc1ca2a735881fc5822f2fc0c53b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826142"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Azure AD 인증을 사용하여 Azure Media Services API 액세스  

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

Azure Media Services API는 RESTful API입니다. 이 API와 REST API 또는 제공되는 클라이언트 SDK를 사용하여 미디어 리소스에 대한 작업을 수행할 수 있습니다. Azure Media Services는 Microsoft .NET용 Media Services 클라이언트 SDK를 제공합니다. Media Services 리소스 및 Media Services API에 액세스할 수 있는 권한을 부여하려면 먼저 인증을 거쳐야 합니다. 

Media Services는 [Azure AD(Azure Active Directory) 기반 인증](../../active-directory/fundamentals/active-directory-whatis.md)을 지원합니다. Azure Media REST 서비스의 경우 REST API 요청을 하는 사용자 또는 애플리케이션이 리소스에 액세스하기 위해 **기여자** 또는 **소유자** 역할을 포함해야 합니다. 자세한 내용은 [Azure Portal에서 역할 기반 Access Control 시작](../../role-based-access-control/overview.md)을 참조하세요.  

이 문서에서는 REST 또는 .NET API를 사용하여 Media Services API에 액세스하는 방법에 대한 개요를 제공합니다.

> [!NOTE]
> 액세스 제어 권한 부여는 2018 년 6 월 1 일에 사용 되지 않았습니다.

## <a name="access-control"></a>액세스 제어

Azure Media REST 요청이 성공하기 위해서는 호출하는 사용자에게 액세스를 시도하는 Media Services 계정에 대한 참가자 또는 소유자 역할이 있어야 합니다.  
소유자 역할이 있는 사용자만 새 사용자 또는 앱에 미디어 리소스(계정) 액세스 권한을 제공할 수 있습니다. 참가자 역할은 미디어 리소스만 액세스할 수 있습니다.
권한이 없는 요청은 상태 코드 401로 실패합니다. 이 오류 코드가 표시되면 사용자에게 사용자의 Media Services 계정에 대한 참가자 또는 소유자 역할이 할당되어 있는지 확인합니다. Azure Portal에서 확인할 수 있습니다. 미디어 계정을 검색한 후 **액세스 제어** 탭을 클릭합니다. 

![액세스 제어 탭](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>인증 형식 
 
Azure Media Services와 함께 Azure AD 인증을 사용할 때 두 가지 인증 옵션이 제공됩니다.

- **사용자 인증**. Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 사용자의 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 
- **서비스 주체 인증**. 서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업을 실행하는 앱입니다. 예로는 웹앱, 함수 앱, 논리 앱, API 및 마이크로 서비스가 있습니다.

### <a name="user-authentication"></a>사용자 인증 

사용자 인증 방법을 사용해야 하는 애플리케이션은 관리 또는 모니터링 원시 앱(모바일 앱, Windows 앱 및 콘솔 애플리케이션)입니다. 이러한 유형의 솔루션은 다음 시나리오 중 하나에서 서비스에 대해 사용자 개입하려는 경우 유용합니다.

- 인코딩 작업에 대한 대시보드 모니터링.
- 라이브 스트림에 대한 대시보드 모니터링.
- 데스크톱 또는 모바일 사용자가 Media Services 계정에서 리소스를 관리할 수 있는 관리 애플리케이션.

> [!NOTE]
> 이 인증 방법은 소비자 지향 애플리케이션에 사용할 수 없습니다. 

먼저, 원시 애플리케이션은 Azure AD에서 액세스 토큰을 획득한 후 Media Services REST API에 대해 HTTP 요청을 수행할 때 이 토큰을 사용합니다. 액세스 토큰을 요청의 요청 헤더에 추가합니다. 

다음 다이어그램은 일반적인 대화형 애플리케이션 인증 흐름을 보여 줍니다. 

![원시 앱 다이어그램](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

이전 다이어그램에서 숫자는 요청 흐름을 시간 순서로 보여 줍니다.

> [!NOTE]
> 사용자 인증 방법을 사용하는 경우 모든 앱에서 동일한 (기본) 원시 애플리케이션 클라이언트 ID 및 원시 애플리케이션 리디렉션 URI를 공유합니다. 

1. 사용자에게 자격 증명을 묻는 메시지를 표시합니다.
2. 다음 매개 변수로 Azure AD 액세스 토큰을 요청합니다.  

   * Azure AD 테넌트 엔드포인트.

       Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자의 이름 위로 커서를 둡니다.
   * Media Services 리소스 URI. 

       이 URI는 동일한 Azure 환경(예: https://rest.media.azure.net))에 있는 Media Services 계정에 대해서는 동일합니다.

   * Media Services(원시) 애플리케이션 클라이언트 ID.
   * Media Services(원시) 애플리케이션 리디렉션 URI.
   * REST Media Services의 리소스 URI.
        
       REST API 끝점을 나타내는 URI(예: https://test03.restv2.westus.media.azure.net/api/))입니다.

     이러한 매개 변수 값을 가져오려면 사용자 인증 옵션과 [Azure Portal을 사용하여 Azure AD 인증 설정 액세스](media-services-portal-get-started-with-aad.md)를 참조하세요.

3. Azure AD 액세스 토큰이 클라이언트로 전송됩니다.
4. 클라이언트는 Azure AD 액세스 토큰과 함께 Azure Media REST API로 요청을 보냅니다.
5. 클라이언트는 Media Services에서 데이터를 다시 가져옵니다.

Media Services .NET 클라이언트 SDK를 사용하여 REST 요청과 통신하기 위해 Azure AD 인증을 사용하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 .NET으로 Media Services API 액세스](media-services-dotnet-get-started-with-aad.md)를 참조하세요. 

Media Services .NET 클라이언트 SDK를 사용하지 않는 경우 2단계에서 설명한 매개 변수를 사용하여 Azure AD 액세스 토큰 요청을 수동으로 만들어야 합니다. 자세한 내용은 [Azure AD 인증 라이브러리를 사용하여 Azure AD 토큰 가져오기](../../active-directory/develop/active-directory-authentication-libraries.md)를 참조하세요.

### <a name="service-principal-authentication"></a>서비스 주체 인증

이 인증 방법을 일반적으로 사용하는 애플리케이션은 중간 계층 서비스 및 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 및 마이크로 서비스)을 실행하는 앱입니다. 이 인증 방법은 리소스를 관리하는 데 서비스 계정을 사용하려는 대화형 애플리케이션에도 적합합니다.

소비자 시나리오를 빌드하는 데 서비스 주체 인증 방법을 사용하는 경우 일반적으로 모바일 또는 데스크톱 애플리케이션에서 직접 처리되지 않고 중간 계층에서 일부 API를 통해 인증이 처리됩니다. 

이 방법을 사용하려면 자체 테넌트에 Azure AD 애플리케이션 및 서비스 주체를 만듭니다. 애플리케이션을 만든 후 Media Services 계정에 앱 기여자 또는 소유자 역할 액세스 권한을 제공합니다. Azure Portal에서 Azure CLI를 사용하거나 PowerShell 스크립트로 이 작업을 수행할 수 있습니다. 기존 Azure AD 애플리케이션을 사용할 수도 있습니다. [Azure Portal](media-services-portal-get-started-with-aad.md)에서 Azure AD 앱 및 서비스 주체를 등록 및 관리할 수 있습니다. [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) 또는 [PowerShell](media-services-powershell-create-and-configure-aad-app.md)을 사용하여 이 작업을 수행할 수도 있습니다. 

![중간 계층 앱](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Azure AD 애플리케이션을 만든 후 다음 설정에 대한 값을 가져옵니다. 인증을 위해서는 다음 값이 필요합니다.

- 클라이언트 ID 
- 클라이언트 암호 

이전 그림에서 숫자는 요청 흐름을 시간 순서로 보여 줍니다.
    
1. 중간 계층 앱(웹 API 또는 웹 애플리케이션)은 다음 매개 변수가 있는 Azure AD 액세스 토큰을 요청합니다.  

   * Azure AD 테넌트 엔드포인트.

       Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자의 이름 위로 커서를 둡니다.
   * Media Services 리소스 URI. 

       이 URI는 동일한 Azure 환경(예: https://rest.media.azure.net))에 있는 Media Services 계정에 대해서는 동일합니다.

   * REST Media Services의 리소스 URI.

       REST API 끝점을 나타내는 URI(예: https://test03.restv2.westus.media.azure.net/api/))입니다.

   * Azure AD 애플리케이션 값: 클라이언트 ID 및 클라이언트 암호.
    
     이러한 매개 변수 값을 가져오려면 서비스 주체 인증 옵션과 [Azure Portal을 사용하여 Azure AD 인증 설정 액세스](media-services-portal-get-started-with-aad.md)를 참조하세요.

2. Azure AD 액세스 토큰이 중간 계층으로 전송됩니다.
4. 중간 계층은 Azure AD 토큰과 함께 Azure Media REST API로 요청을 보냅니다.
5. 중간 계층은 Media Services에서 데이터를 다시 가져옵니다.

Media Services .NET 클라이언트 SDK를 사용하여 REST 요청과 통신하기 위해 Azure AD 인증을 사용하는 방법에 대한 자세한 내용은 [Azure AD 인증을 사용하여 .NET으로 Azure Media Services API 액세스](media-services-dotnet-get-started-with-aad.md)를 참조하세요. 

Media Services .NET 클라이언트 SDK를 사용하지 않는 경우 1단계에서 설명한 매개 변수를 사용하여 Azure AD 토큰 요청을 수동으로 만들어야 합니다. 자세한 내용은 [Azure AD 인증 라이브러리를 사용하여 Azure AD 토큰 가져오기](../../active-directory/develop/active-directory-authentication-libraries.md)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

예외: “원격 서버에서 오류를 반환했습니다. (401) 권한 없음.”

해결 방법: Media Services REST 요청이 성공하기 위해서는 호출하는 사용자에게 액세스를 시도하는 Media Services 계정에 대한 기여자 또는 소유자 역할이 있어야 합니다. 자세한 내용은 [액세스 제어](media-services-use-aad-auth-to-access-ams-api.md#access-control) 섹션을 참조하세요.

## <a name="resources"></a>리소스

다음 문서는 Azure AD 인증 개념을 간략히 설명합니다. 

- [Azure AD로 해결된 인증 시나리오](../../active-directory/develop/authentication-scenarios.md)
- [Azure AD에서 애플리케이션 추가, 업데이트 또는 제거](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [PowerShell을 사용하여 역할 기반 Access Control 구성 및 관리](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>다음 단계

* Azure Portal을 사용하여 [Azure AD 인증에 액세스하고 Azure Media Services API를 사용](media-services-portal-get-started-with-aad.md)합니다.
* Azure AD 인증을 사용하여 [.NET으로 Azure Media Services API에 액세스](media-services-dotnet-get-started-with-aad.md)합니다.


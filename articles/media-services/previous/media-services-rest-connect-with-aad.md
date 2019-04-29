---
title: Azure AD 인증을 사용하여 REST로 Azure Media Services API 액세스 | Microsoft Docs
description: REST를 사용하여 Azure Active Directory 인증으로 Azure Media Services API를 액세스하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: 4b6bd97d7e87832f774f7a09f7e0deeb4047e695
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598725"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Azure AD 인증을 사용하여 REST로 Media Services API 액세스

Azure Media Services와 함께 Azure AD 인증을 사용할 때 다음 두 가지 방법 중 하나로 인증할 수 있습니다.

- **사용자 인증**은 Azure Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 
- **서비스 주체 인증**은 서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로서비스)을 실행하는 앱입니다.

    이 자습서에서는 Azure AD **서비스 주체** 인증을 사용하여 REST를 통해 AMS API에 액세스하는 방법을 설명합니다. 

    > [!NOTE]
    > **서비스 주체**는 Azure Media Services에 연결하는 대다수 애플리케이션에 사용하는 것이 좋은 모범 사례입니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal에서 인증 정보 가져오기
> * Postman을 사용하여 액세스 토큰 가져오기
> * 액세스 토큰을 사용하여 **Assets** API 테스트


> [!IMPORTANT]
> 현재 Media Services는 Azure Access Control 서비스 인증 모델을 지원합니다. 하지만 Access Control 인증은 2018년 6월 1일부로 사용 중단되었습니다. 가능한 빨리 Azure AD 인증 모델로 마이그레이션하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- [Azure Portal을 사용하여 Azure Media Services 계정을 만듭니다](media-services-portal-create-account.md).
- [Azure AD 인증을 사용하여 Azure Media Services API 액세스 개요](media-services-use-aad-auth-to-access-ams-api.md) 문서를 검토합니다.
- [Postman](https://www.getpostman.com/) REST 클라이언트를 설치하여 이 문서에 나와 있는 REST API를 실행합니다. 

    이 자습서에서는 **Postman**을 사용하지만 어떤 REST 도구든 사용할 수 있습니다. 다른 대안은 다음과 같습니다. **Visual Studio Code**와 REST 플러그 인을 함께 사용하거나, **Telerik Fiddler**를 사용할 수도 있습니다. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>Azure Portal에서 인증 정보 가져오기

### <a name="overview"></a>개요

Media Services API에 액세스하려면 다음 데이터 요소를 수집해야 합니다.

|설정|예|설명|
|---|-------|-----|
|Azure Active Directory 테넌트 도메인|microsoft.onmicrosoft.com|STS(보안 토큰 서비스) 끝점처럼 Azure AD는 <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> 형식을 사용하여 만들어집니다. Azure AD는 리소스(액세스 토큰)에 액세스하기 위해 JWT를 발급합니다.|
|REST API 엔드포인트|<https://amshelloworld.restv2.westus.media.azure.net/api/>|애플리케이션에서 모든 Media Services REST API 호출이 수행되는 엔드포인트입니다.|
|클라이언트 ID(애플리케이션 ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Azure AD 애플리케이션(클라이언트) ID입니다. 액세스 토큰을 가져오려면 클라이언트 ID가 필요합니다. |
|클라이언트 암호|+mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq+Dbim0=|Azure AD 애플리케이션 키(클라이언트 암호)입니다. 액세스 토큰을 가져오려면 클라이언트 암호가 필요합니다.|

### <a name="get-aad-auth-info-from-the-azure-portal"></a>Azure Portal에서 AAD 인증 정보 가져오기

정보를 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. AMS 인스턴스로 이동합니다.
3. **API 액세스**를 선택합니다.
4. **서비스 주체를 사용하여 Azure Media Services API에 연결**을 클릭합니다.

    ![API 액세스](./media/connect-with-rest/connect-with-rest01.png)

5. 기존 **Azure AD 애플리케이션**을 선택하거나 아래와 같이 새 애플리케이션을 만듭니다.

    > [!NOTE]
    > Azure Media REST 요청이 성공하려면, 호출하는 사용자가 액세스하려는 Media Services 계정에 대한 **참가자** 또는 **소유자** 역할을 가지고 있어야 합니다. “원격 서버에서 (401) 권한 없음 오류를 반환했습니다.”라는 예외가 표시되면 [액세스 제어](media-services-use-aad-auth-to-access-ams-api.md#access-control)를 참조하세요.

    새 AD 앱을 만들어야 하는 경우 다음 단계를 수행합니다.
    
   1. **새로 만들기**를 누릅니다.
   2. 이름을 입력합니다.
   3. **새로 만들기**를 다시 누릅니다.
   4. **저장**을 누릅니다.

      ![API 액세스](./media/connect-with-rest/new-app.png)

      새 앱이 페이지에 표시됩니다.

6. **클라이언트 ID**(애플리케이션 ID)를 가져옵니다.
    
   1. 애플리케이션을 선택합니다.
   2. 오른쪽 창에서 **클라이언트 ID**를 가져옵니다. 

      ![API 액세스](./media/connect-with-rest/existing-client-id.png)

7. 애플리케이션의 **키**(클라이언트 암호) 가져오기 

   1. **애플리케이션 관리** 단추를 클릭합니다. 클라이언트 ID 정보는 **애플리케이션 ID** 아래에 있습니다. 
   2. **키**를 누릅니다.
    
       ![API 액세스](./media/connect-with-rest/manage-app.png)
   3. **설명**과 **만료 날짜**를 입력하고 **저장**을 눌러 앱 키(클라이언트 암호)를 생성합니다.
    
       **저장** 단추를 누르면 키 값이 표시됩니다. 블레이드에서 나가기 전에 키 값을 복사합니다.

   ![API 액세스](./media/connect-with-rest/connect-with-rest03.png)

코드에서 나중에 사용할 수 있도록 web.config 또는 app.config 파일에 AD 연결 매개 변수의 값을 추가할 수 있습니다.

> [!IMPORTANT]
> **클라이언트 키**는 중요한 암호이므로 키 자격 증명 모음에서 적절하게 보호하거나 프로덕션 환경에서 암호화해야 합니다.

## <a name="get-the-access-token-using-postman"></a>Postman을 사용하여 액세스 토큰 가져오기

이 섹션에서는 **Postman**을 사용하여 JWT 전달자 토큰(액세스 토큰)을 반환하는 REST API를 실행하는 방법을 설명합니다. Media Services REST API를 호출하려면 호출에 "Authorization" 헤더를 추가한 다음, 이 자습서의 다음 섹션에 나와 있는 것처럼 각 호출에 "Bearer *your_access_token*" 값을 추가해야 합니다. 

1. **Postman**을 엽니다.
2. **POST**를 선택합니다.
3. 다음 형식을 사용하여 테넌트 이름을 포함하는 URL을 입력합니다. 테넌트 이름은 **.onmicrosoft.com**으로 끝나야 하며 URL은 **oauth2/token**으로 끝나야 합니다. 

    https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token

4. **Headers** 탭을 선택합니다.
5. "Key/Value" 데이터 표를 사용하여 **Headers** 정보를 입력합니다. 

    ![데이터 표](./media/connect-with-rest/headers-data-grid.png)

    Postman 창 오른쪽의 **Bulk Edit** 링크를 클릭하고 다음 코드를 붙여 넣어도 됩니다.

        Content-Type:application/x-www-form-urlencoded
        Keep-Alive:true

6. **Body** 탭을 누릅니다.
7. "Key/Value" 데이터 표를 사용하여 본문 정보를 입력합니다. 클라이언트 ID와 암호 값은 적절한 값으로 바꿉니다. 

    ![데이터 표](./media/connect-with-rest/data-grid.png)

    Postman 창 오른쪽의 **Bulk Edit**를 클릭하고 다음 본문을 붙여 넣어도 됩니다. 클라이언트 ID와 암호 값은 적절한 값으로 바꿉니다.

        grant_type:client_credentials
        client_id:{Your Client ID that you got from your Azure AD Application}
        client_secret:{Your client secret that you got from your Azure AD Application's Keys}
        resource:https://rest.media.azure.net

8. **보내기**를 누릅니다.

    ![토큰 가져오기](./media/connect-with-rest/connect-with-rest04.png)

반환되는 응답에는 AMS API에 액세스하려면 사용해야 하는 **액세스 토큰**이 포함되어 있습니다.

## <a name="test-the-assets-api-using-the-access-token"></a>액세스 토큰을 사용하여 **Assets** API 테스트

이 섹션에서는 **Postman**을 사용하여 **Assets** API에 액세스하는 방법을 설명합니다.

1. **Postman**을 엽니다.
2. **GET**을 선택합니다.
3. REST API 끝점 붙여넣기(예: https://amshelloworld.restv2.westus.media.azure.net/api/Assets))
4. **Authorization** 탭을 선택합니다. 
5. **Bearer Token**을 선택합니다.
6. 이전 섹션에서 만든 토큰을 붙여 넣습니다.

    ![토큰 가져오기](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > Postman UX는 Mac과 PC 간에 서로 다를 수 있습니다. Mac 버전의 **Authorization** 섹션 드롭다운에 "Bearer Token" 옵션이 없으면 Mac 클라이언트에서 **Authorization** 헤더를 수동으로 추가해야 합니다.

   ![Auth 헤더](./media/connect-with-rest/auth-header.png)

7. **Headers**를 선택합니다.
5. Postman 창 오른쪽의 **Bulk Edit** 링크를 클릭합니다.
6. 다음 헤더를 붙여 넣습니다.

        x-ms-version:2.15
        Accept:application/json
        Content-Type:application/json
        DataServiceVersion:3.0
        MaxDataServiceVersion:3.0

7. **보내기**를 누릅니다.

반환되는 응답에는 계정 내의 자산이 포함됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Media Services 액세스를 위한 Azure AD 인증: 둘 다 REST API를 통해](https://github.com/willzhan/WAMSRESTSoln)의 샘플 코드를 사용해 보세요.
* [.NET을 사용하여 파일 업로드](media-services-dotnet-upload-files.md)

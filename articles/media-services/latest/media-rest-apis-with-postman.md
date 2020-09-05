---
title: Azure Media Services v3 REST API 호출에 대해 Postman 구성
description: 이 문서에서는 AMS(Azure Media Services) REST API를 호출하는 데 사용할 수 있도록 Postman을 구성하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1232c47040a5577731121ee0bb17eb264240e765
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264881"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Media Services v3 REST API 호출에 대해 Postman 구성

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 AMS(Azure Media Services) REST API를 호출하는 데 사용할 수 있도록 **Postman**을 구성하는 방법을 보여 줍니다. 이 문서에서는 환경 및 컬렉션 파일을 **Postman**으로 가져오는 방법을 보여 줍니다. 컬렉션에는 AMS(Azure Media Services) REST API를 호출하는 HTTP 요청의 그룹화된 정의가 포함되어 있습니다. 환경 파일에는 컬렉션에 사용되는 변수가 포함되어 있습니다.

개발을 시작 하기 전에 [Media Services V3 api를 사용 하 여 개발](media-services-apis-overview.md)을 검토 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 
- [API에 액세스](./access-api-howto.md)하는 데 필요한 정보 가져오기
- [Postman](https://www.getpostman.com/) REST 클라이언트를 설치하여 일부 AMS REST 자습서에 표시되는 REST API를 실행합니다. 

    지금은 **Postman**을 사용하고 있지만 어떤 REST 도구도 괜찮습니다. 다른 대안은 REST 플러그 인 또는 **Telerik Fiddler**를 사용 하는 **Visual Studio Code** 입니다. 

> [!IMPORTANT]
> [명명 규칙](media-services-apis-overview.md#naming-conventions)을 검토합니다.

## <a name="download-postman-files"></a>Postman 파일 다운로드

Postman 컬렉션 및 환경 파일이 포함된 GitHub 리포지토리를 복제합니다.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman 구성

### <a name="configure-the-environment"></a>환경 구성 

1. **Postman** 앱을 엽니다.
2. 화면 오른쪽에서 **환경 관리** 옵션을 선택합니다.

    ![환경 관리](./media/develop-with-postman/postman-import-env.png)
4. **환경 관리** 대화 상자에서 **가져오기**를 클릭합니다.
2. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`을 복제할 때 다운로드한 `Azure Media Service v3 Environment.postman_environment.json` 파일을 찾습니다.
6. **Azure Media Service v3 Environment** 환경이 추가되었습니다.

    > [!Note]
    > 액세스 변수를 위의 **Media Services API 액세스** 섹션에서 가져온 값으로 업데이트합니다.

7. 선택한 파일을 두 번 클릭하고 API 액세스 단계에 따라 가져온 값을 입력합니다.
8. 대화 상자를 닫습니다.
9. 드롭다운에서 **Azure Media Service v3 Environment** 환경을 선택합니다.

    ![환경 선택](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>컬렉션 구성

1. **가져오기**를 클릭하여 컬렉션 파일을 가져옵니다.
1. `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`을 복제할 때 다운로드한 `Media Services v3.postman_collection.json` 파일을 찾습니다.
3. **Media Services v3.postman_collection.json** 파일을 선택합니다.

    ![파일 가져오기](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Azure AD 토큰 가져오기 

AMS v3 리소스 조작을 시작하려면 먼저 서비스 주체 인증을 위한 Azure AD 토큰을 가져오고 설정해야 합니다.

1. Postman 앱의 왼쪽 창에서 "1 단계: AAD Auth 토큰 가져오기"를 선택 합니다.
2. 그런 다음, "서비스 주체 인증을 위한 Azure AD 토큰 가져오기"를 선택합니다.
3. **보내기**를 누릅니다.

    다음 **POST** 작업을 보냅니다.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. 응답은 토큰과 함께 반환되며 "AccessToken" 환경 변수를 토큰 값으로 설정합니다.  

    ![AAD 토큰 가져오기](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>문제 해결 

* 응용 프로그램이 "HTTP 504: 게이트웨이 시간 초과"로 인해 실패 하는 경우 위치 변수가 Media Services 계정의 예상 위치 이외의 값으로 명시적으로 설정 되지 않았는지 확인 합니다. 
* "계정을 찾을 수 없음" 오류가 발생 하는 경우 본문 JSON 메시지의 location 속성이 Media Services 계정이 있는 위치로 설정 되었는지 확인 합니다. 

## <a name="see-also"></a>참고 항목

- [Media Services로 필터 만들기 - REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager 기반 REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>다음 단계

- [REST를 사용하여 파일을 스트리밍](stream-files-tutorial-with-rest.md)합니다.  
- [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)

---
title: Azure Media Services REST API 호출에 대해 Postman 구성
description: 이 문서에서는 Media Services REST API 호출에 대해 Postman을 구성 하는 방법을 설명 합니다.
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
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: 4cdc3efc766daff38b566e71f06dc4db0f38fe7a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255514"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Media Services v2 REST API 호출에 대해 Postman 구성

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](../latest/index.yml)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

이 자습서에서는 AMS(Azure Media Services) REST API를 호출하는 데 사용할 수 있도록 **Postman**을 구성하는 방법을 보여 줍니다. 이 자습서에서는 환경 및 컬렉션 파일을 **Postman**으로 가져오는 방법을 보여 줍니다. 컬렉션에는 AMS(Azure Media Services) REST API를 호출하는 HTTP 요청의 그룹화된 정의가 포함되어 있습니다. 환경 파일에는 컬렉션에 사용되는 변수가 포함되어 있습니다.

이 환경 및 컬렉션은 Azure Media Services REST API로 다양한 작업을 수행하는 방법을 보여 주는 문서에서 사용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Postman](https://www.getpostman.com/) REST 클라이언트를 설치하여 일부 AMS REST 자습서에 표시되는 REST API를 실행합니다. 

    지금은 **Postman**을 사용하고 있지만 어떤 REST 도구도 괜찮습니다. 다른 대안은 REST 플러그 인 또는 **Telerik Fiddler**를 사용 하는 **Visual Studio Code** 입니다. 

## <a name="configure-the-environment"></a>환경 구성 

1. AMS 자습서에 사용되는 환경 변수를 포함하는 .json 파일을 만듭니다. 파일 이름을 지정합니다(예: **AzureMediaServices.postman_environment.json**). 파일을 열고 [이 코드 목록](postman-environment.md)에서 Postman 환경을 정의하는 코드를 붙여 넣습니다. 
2. **Postman**을 엽니다.
3. 화면 오른쪽에서 **환경 관리** 옵션을 선택합니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-create-env.png)
4. **환경 관리** 대화 상자에서 **가져오기**를 클릭합니다.
5. **AzureMediaServices.postman_environment.json** 파일을 찾아 선택합니다.
6. **AzureMedia** 환경이 추가됩니다.
7. 대화 상자를 닫습니다.
8. **AzureMedia** 환경을 선택합니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>컬렉션 구성

1. Media Services에 파일을 업로드하는 데 필요한 모든 작업이 있는 **Postman** 컬렉션이 포함된 .json 파일을 만듭니다. 파일 이름을 지정합니다(예: **AzureMediaServicesOperations.postman_collection.json**). 파일을 열고 [이 코드 목록](postman-collection.md)에서 **Postman** 컬렉션을 정의하는 코드를 붙여 넣습니다.
2. **가져오기**를 클릭하여 컬렉션 파일을 가져옵니다.
3. **AzureMediaServicesOperations.postman_collection.json** 파일을 선택합니다.

    ![파일 업로드](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>다음 단계

[자산 업로드](media-services-rest-upload-files.md) 문서를 확인합니다.  

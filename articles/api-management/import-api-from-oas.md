---
title: Azure Portal을 사용하여 OpenAPI 사양 가져오기 | Microsoft Docs
description: API Management를 사용하여 OpenAPI 사양 API를 가져온 다음, Azure 및 개발자 포털에서 API를 테스트하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 346156c624a4c8b1c1d6cc490fc0559f364b4b9d
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905570"
---
# <a name="import-an-openapi-specification"></a>OpenAPI 사양 가져오기

이 문서에서는 https://conferenceapi.azurewebsites.net?format=json 에 있는 "OpenAPI 사양" 백 엔드 API를 가져오는 방법을 보여줍니다. 이 백 엔드 API는 Microsoft에서 제공하고 Azure에서 호스트됩니다. 또한 APIM API를 테스트하는 방법도 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * "OpenAPI 사양" 백 엔드 API 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

## <a name="prerequisites"></a>사전 요구 사항

다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>백 엔드 API 가져오기 및 게시

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API**를 선택합니다.
2. **새 API 추가** 목록에서 **OpenAPI 사양**을 선택합니다.

    ![OpenAPI 사양](./media/import-api-from-oas/oas-api.png)
3. API 설정을 입력합니다. 생성 중에 값을 설정하거나 나중에 **설정** 탭으로 이동하여 값을 설정할 수 있습니다. 설정은 [첫 번째 API 가져오기 및 게시](import-and-publish.md#-import-and-publish-a-backend-api) 자습서에 설명되어 있습니다.
4. **만들기**를 선택합니다.

> [!NOTE]
> API 가져오기 제한 사항은 [다른 문서](api-management-api-import-restrictions.md)에 설명되어 있습니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)

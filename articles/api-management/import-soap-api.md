---
title: Azure Portal을 사용하여 SOAP API 가져오기 | Microsoft Docs
description: SOAP API의 표준 XML 표현을 가져온 다음, Azure 및 개발자 포털에서 API를 테스트하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 583179c4678b43bef25174835ec2701d571b37b6
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905653"
---
# <a name="import-soap-api"></a>SOAP API 가져오기

이 문서에서는 SOAP API의 표준 XML 표시를 가져오는 방법을 보여 줍니다. 이 문서에서는 API Management API를 테스트하는 방법도 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * SOAP API 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

## <a name="prerequisites"></a>사전 요구 사항

다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>백 엔드 API 가져오기 및 게시

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API**를 선택합니다.
2. **새 API 추가** 목록에서 **WSDL**을 선택합니다.

    ![Soap api](./media/import-soap-api/wsdl-api.png)
3. **WSDL 사양**에 SOAP API가 상주하는 URL을 입력합니다.
4. **SOAP 통과** 라디오 단추는 기본적으로 선택됩니다. 이 선택 항목으로 API는 SOAP로 노출되려고 합니다. 소비자는 SOAP 규칙을 사용해야 합니다. API를 "restify"하려는 경우 [SOAP API 가져오기 및 REST로 변환](restify-soap-api.md)의 단계를 따릅니다.

    ![통과](./media/import-soap-api/pass-through.png)
5. Tab 키를 누릅니다.

    다음 필드는 SOAP API의 표시 이름, 이름, 설명과 같은 정보로 채워집니다.
6. API URL 접미사를 추가합니다. 접미사는 이 API Management 인스턴스에서 이 특정 API를 식별하는 이름입니다. 접미사는 이 API Management 인스턴스에서 고유해야 합니다.
7. API를 제품에 연결하여 API를 게시합니다. 이 경우 "*Unlimited*" 제품이 사용됩니다.  API를 게시하고 개발자가 사용할 수 있게 하려면 제품에 추가합니다. API를 만드는 동안 이 작업을 수행할 수도 있고 나중에 설정할 수도 있습니다.

    제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. API Management 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.

    기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

    * **Starter**
    * **무제한**   
8. 다른 API 설정을 입력합니다. 생성 중에 값을 설정하거나 나중에 **설정** 탭으로 이동하여 값을 설정할 수 있습니다. 설정은 [첫 번째 API 가져오기 및 게시](import-and-publish.md#-import-and-publish-a-backend-api) 자습서에 설명되어 있습니다.
9. **만들기**를 선택합니다.

### <a name="test-the-new-api-in-the-administrative-portal"></a>관리 포털에서 새 API 테스트

관리 포털에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다.  

1. 이전 단계에서 만든 API를 선택합니다.
2. **테스트** 탭을 누릅니다.
3. 작업을 선택합니다.

    페이지에 쿼리 매개 변수에 대한 필드와 헤더 필드가 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. API Management 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다. 
1. **보내기**를 누릅니다.

    백 엔드는 **200 정상** 및 일부 데이터로 응답합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
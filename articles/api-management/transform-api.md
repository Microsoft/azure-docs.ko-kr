---
title: Azure API Management로 API 변환 및 보호 | Microsoft Docs
description: 할당량 및 제한(속도 제한) 정책을 사용하여 API를 보호하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/26/2019
ms.author: apimpm
ms.openlocfilehash: 4c3cc572dd9629605414cd88d7735c2b31f92249
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85851261"
---
# <a name="transform-and-protect-your-api"></a>API 변환 및 보호

이 자습서에서는 프라이빗 백 엔드 정보를 노출시키지 있도록 API를 변환하는 방법을 보여 줍니다. 예를 들어 백 엔드에서 실행되는 기술 스택에 대한 정보를 숨기려고 할 수 있습니다. 또한 API HTTP 응답의 본문에 표시되는 원래 URL을 숨기고, 대신 APIM 게이트웨이로 리디렉션하려고 할 수도 있습니다.

이 자습서에서는 Azure API Management로 속도 제한을 구성하여 백 엔드 API에 대한 보호를 추가하기가 얼마나 쉬운지 보여 줍니다. 예를 들어 개발자가 과도하게 사용하지 않도록 API 호출 횟수를 제한하려고 할 수 있습니다. 자세한 내용은 [API Management 정책](api-management-policies.md)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> -   API를 변환하여 응답 헤더 제거
> -   API 응답 본문에 있는 원래 URL을 APIM 게이트웨이 URL로 바꾸기
> -   속도 제한 정책(제한)을 추가하여 API 보호
> -   변환 테스트

![정책](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>필수 구성 요소

-   [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
-   [Azure API Management의 정책 개념](api-management-howto-policies.md)을 이해합니다.
-   다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
-   또한 [첫 번째 API 가져오기 및 게시](import-and-publish.md) 자습서를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>API를 변환하여 응답 헤더 제거

이 섹션에서는 사용자에게 표시하지 않으려는 HTTP 헤더를 숨기는 방법을 보여 줍니다. 이 예제에서는 HTTP 응답에서 다음과 같은 헤더가 삭제되었습니다.

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>원래 응답 테스트

원래 응답을 확인하려면 다음을 수행합니다.

1. APIM 서비스 인스턴스에서 **API**(**API MANAGEMENT** 아래)를 선택합니다.
2. API 목록에서 **Demo Conference API**를 선택합니다.
3. 화면 위쪽에 있는 **테스트** 탭을 클릭합니다.
4. **GetSpeakers** 작업을 선택합니다.
5. 화면 아래쪽에 있는 **보내기** 단추를 누릅니다.

원래 응답은 다음과 비슷합니다.

![정책](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>변환 정책 설정

![아웃바운드 정책 설정](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png)

1. **데모 회의 API**를 선택합니다.
2. 화면 맨 위에서 **디자인** 탭을 선택합니다.
3. **모든 작업**을 선택합니다.
4. **아웃바운드 처리** 섹션에서 **</>** 아이콘을 클릭합니다.
5. **&lt;아웃바운드&gt;** 요소 내부에 커서를 놓습니다.
6. 오른쪽 창에서 **변환 정책** 아래에 있는 **+ HTTP 헤더 설정**을 두 번 클릭합니다(2개의 정책 조각 삽입).

   ![정책](./media/transform-api/transform-api.png)

7. 다음과 같이 **\<outbound>** 코드를 수정합니다.

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   ![정책](./media/transform-api/set-policy.png)

8. **저장** 단추를 클릭합니다.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>API 응답 본문에 있는 원래 URL을 APIM 게이트웨이 URL로 바꾸기

이 섹션에서는 API HTTP 응답의 본문에 표시되는 원래 URL을 숨기고, 대신 APIM 게이트웨이로 리디렉션하는 방법을 보여 줍니다.

### <a name="test-the-original-response"></a>원래 응답 테스트

원래 응답을 확인하려면 다음을 수행합니다.

1. **데모 회의 API**를 선택합니다.
2. 화면 위쪽에 있는 **테스트** 탭을 클릭합니다.
3. **GetSpeakers** 작업을 선택합니다.
4. 화면 아래쪽에 있는 **보내기** 단추를 누릅니다.

    원래 응답은 다음과 같습니다.

    ![정책](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>변환 정책 설정

1.  **데모 회의 API**를 선택합니다.
2.  **모든 작업**을 선택합니다.
3.  화면 맨 위에서 **디자인** 탭을 선택합니다.
4.  **아웃바운드 처리** 섹션에서 **</>** 아이콘을 클릭합니다.
5.  **&lt;아웃 바운드&gt;** 요소 내에 커서를 놓고 오른쪽 위 모서리에 있는 **코드 조각 표시** 단추를 클릭합니다.
6.  오른쪽 창의 **변환 정책** 아래에서 **콘텐츠의 URL 마스킹**을 클릭합니다.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>속도 제한 정책(제한)을 추가하여 API 보호

이 섹션에서는 속도 제한을 구성하여 백 엔드 API에 대한 보호를 추가하는 방법을 보여 줍니다. 예를 들어 개발자가 과도하게 사용하지 않도록 API 호출 횟수를 제한하려고 할 수 있습니다. 이 예제에서 제한은 각 구독 ID에 대해 15초당 3회 호출로 설정됩니다. 개발자는 15초 후에 API 호출을 다시 시도할 수 있습니다.

![인바운드 정책 설정](./media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png)

1.  **데모 회의 API**를 선택합니다.
2.  **모든 작업**을 선택합니다.
3.  화면 맨 위에서 **디자인** 탭을 선택합니다.
4.  **인바운드 처리** 섹션에서 **</>** 아이콘을 클릭합니다.
5.  **&lt;인바운드&gt;** 요소 내부에 커서를 놓습니다.
6.  오른쪽 창의 **액세스 제한 정책**에서 **+ 키당 호출 속도 제한**을 클릭합니다.
7.  **rate-limit-by-key** 코드( **\<inbound\>** 요소)를 다음 코드로 수정합니다.

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>변환 테스트

이 시점에서 코드 편집기에서 코드를 살펴보면 정책은 다음과 같습니다.

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <find-and-replace from="://conferenceapi.azurewebsites.net:443" to="://apiphany.azure-api.net/conference"/>
        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

이 섹션의 나머지 부분에서는 이 문서에서 설정한 정책 변환을 테스트합니다.

### <a name="test-the-stripped-response-headers"></a>삭제된 응답 헤더 테스트

1. **데모 회의 API**를 선택합니다.
2. **테스트** 탭을 선택합니다.
3. **GetSpeakers** 작업을 클릭합니다.
4. **보내기**를 누릅니다.

    다음에 보이는 것처럼 헤더가 삭제되었습니다.

    ![정책](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>대체된 URL 테스트

1. **데모 회의 API**를 선택합니다.
2. **테스트** 탭을 선택합니다.
3. **GetSpeakers** 작업을 클릭합니다.
4. **보내기**를 누릅니다.

    보이는 것처럼 URL이 대체되었습니다.

    ![정책](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>속도 제한 테스트

1. **데모 회의 API**를 선택합니다.
2. **테스트** 탭을 선택합니다.
3. **GetSpeakers** 작업을 클릭합니다.
4. **보내기**를 계속해서 3번 누릅니다.

    요청을 3번 보낸 후에 **429 요청이 너무 많음** 응답이 표시됩니다.

5. 15초 정도 기다렸다가 **보내기**를 다시 누릅니다. 이번에는 **200 정상** 응답이 표시됩니다.

    ![제한](./media/transform-api/test-throttling.png)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> -   API를 변환하여 응답 헤더 제거
> -   API 응답 본문에 있는 원래 URL을 APIM 게이트웨이 URL로 바꾸기
> -   속도 제한 정책(제한)을 추가하여 API 보호
> -   변환 테스트

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [API 모니터링](api-management-howto-use-azure-monitor.md)

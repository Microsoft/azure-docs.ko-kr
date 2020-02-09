---
title: HTTPS 엔드포인트 | Azure Marketplace
description: HTTPS 엔드포인트에 대한 잠재 고객 관리 구성
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812281"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>HTTPS 엔드포인트를 사용하여 잠개 고객 관리 구성

파트너 센터에서 Azure Marketplace 및 AppSource 리드를 받기 위해 CRM (고객 관계 관리) 시스템을 명시적으로 지원 하지 않는 경우 MS Flow에서 HTTPS 엔드포인트을 사용 하 여 이러한 잠재 고객을 처리할 수 있습니다. HTTPS 엔드포인트을 사용 하 여 이러한 잠재 고객을 전자 메일 알림으로 전송 하거나 MS Flow에서 지 원하는 CRM (고객 관계 관리) 시스템에 기록할 수 있습니다. 이 문서의 지침에서는 Microsoft Flow를 사용 하 여 새 흐름을 만드는 기본 프로세스를 안내 하 고,이를 통해 잠재 고객 관리 > **HTTPS 엔드포인트 url** 필드에 대 한 게시 포털에 입력 하는 HTTP 게시 url이 생성 됩니다. 또한 온라인에서 찾을 수 있는 [Postman](https://www.getpostman.com/downloads/) 이라는 도구를 사용 하 여 흐름을 테스트 하는 방법에 대 한 지침도 포함 되어 있습니다.

## <a name="create-a-flow-using-microsoft-flow"></a>Microsoft Flow를 사용하여 흐름 만들기

1. [Flow](https://flow.microsoft.com/) 웹 페이지를 엽니다. **로그인**을 선택 하거나 아직 계정이 없는 경우 **등록** 을 선택 하 여 무료 흐름 계정을 만듭니다.

2. 로그인하고 메뉴 모음에서 **내 흐름**을 선택합니다.

3. 선택 **+ 자동-비어 있음에서**선택 합니다.

    ![내 흐름 + 자동-비어 있음](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. *자동화 된 흐름 빌드* 창에서 **건너뛰기**를 선택 합니다. 

    ![자동화 된 흐름 빌드-건너뛰기](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. **커넥터 및 트리거 검색** 필드에서 “요청”을 입력하여 요청 커넥터를 찾습니다.
6. *트리거*에서 **HTTP 요청을 받은 경우**를 선택합니다. 

    ![요청 커넥터-트리거](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. *HTTP 요청을 받은 경우* 창에서 json 스키마를 복사 하 여 **요청 본문 json 스키마** 텍스트 상자에 붙여넣습니다. 이 스키마는 Microsoft에서 잠재 고객 데이터를 포함 하는 데 사용 됩니다.

    ![요청 커넥터-트리거](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON 스키마**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>구성의이 시점에서 CRM 시스템에 연결 하거나 전자 메일 알림을 구성 하도록 선택할 수 있습니다. 선택한 내용에 따라 나머지 지침을 따릅니다.

### <a name="to-connect-to-a-crm-system"></a>CRM 시스템에 연결하려면

1. **+ 새 단계**를 선택합니다.
2. *커넥터 및 작업 검색*을 클릭 하 여 선택한 CRM 시스템을 선택 하 고 작업 섹션에서 새 레코드를 *만드는 작업으로* 선택 합니다. 다음 화면 캡처는 **Dynamics 365-** 새 레코드를 예제로 만듭니다.

    ![새 레코드 만들기](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. CRM 시스템에 연결 된 **조직 이름을** 제공 합니다. **엔터티 이름** 드롭다운 목록에서 **잠재 고객**을 선택합니다.

    ![잠재 고객 선택](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow에는 잠재 고객 정보를 입력하기 위한 양식이 나와 있습니다. 동적 콘텐츠 추가를 선택 하 여 입력 요청에서 항목을 매핑할 수 있습니다. 다음 화면 캡처에는 **OfferTitle**이 예제로 나와 있습니다.

    ![동적 콘텐츠 추가](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. 원하는 필드를 매핑한 후 **저장**을 선택하여 흐름을 저장합니다. Http POST URL은 만들어지고 *http 요청을 받을 때* 창에서 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용 하 여이 URL을 복사 합니다 .이는 전체 URL의 일부를 실수로 누락 하지 않도록 하는 것이 중요 합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요 하므로이 URL을 저장 합니다.

    ![HTTP 요청을 받을 때입니다.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>이메일 알림을 설정하려면

1. 이제 JSON 스키마를 완료 했으므로 **+ 새 단계**를 선택 합니다.
2. **작업 선택**에서 **작업**을 선택합니다.
3. **작업**아래에서 **전자 메일 보내기 (Office 365 Outlook)** 를 선택 합니다.

    >[!Note]
    >다른 전자 메일 공급자를 사용 하 여를 검색 하 고 대신 *전자 메일 알림 (메일)* 을 작업으로 보내기를 선택 합니다.

    ![이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. **전자 메일 보내기** 창에서 다음 필수 필드를 구성 합니다.

   - 잠재 고객이 전송 될 유효한 전자 메일 **주소를 하나** 이상 입력 합니다.
   - **제목** - Flow에서는 다음 화면 캡처의 **LeadSource**와 같이 동적 콘텐츠를 추가하는 옵션이 제공됩니다. 먼저 필드 이름을 입력 한 다음 팝업 창에서 동적 콘텐츠 선택 목록을 클릭 합니다. 

        >[!Note] 
        > 필드 이름을 추가 하는 경우 ":"을 사용 하 여 각 작업을 수행 하 고를 입력 하 여 새 행을 만들 수 있습니다. 필드 이름을 추가한 후에는 동적 선택 목록에서 연결 된 각 매개 변수를 추가할 수 있습니다.

        ![동적 콘텐츠를 사용하여 이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **본문** -동적 콘텐츠 선택 목록에서 전자 메일 본문에 원하는 정보를 추가 합니다. 예를 들어, LastName, FirstName, Email 및 Company입니다. <br> <br> 이메일 알림 설정을 완료하면 다음 화면 캡처에 나오는 예제와 같이 표시됩니다.


       ![이메일 작업 추가](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. **저장**을 선택하여 흐름을 마칩니다. Http POST URL은 만들어지고 *http 요청을 받을 때* 창에서 액세스할 수 있습니다. HTTP POST URL의 오른쪽에 있는 복사 컨트롤을 사용 하 여이 URL을 복사 합니다 .이는 전체 URL의 일부를 실수로 누락 하지 않도록 하는 것이 중요 합니다. 게시 포털에서 잠재 고객 관리를 구성할 때 필요 하므로이 URL을 저장 합니다.

   ![HTTP 게시 URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>테스트

온라인으로 다운로드할 수 있는 [Postman](https://app.getpostman.com/app/download/win64)이라는 도구를 사용 하 여 모든 것이 예상 대로 작동 하는지 테스트할 수 있습니다. 이는 Windows에서 사용할 수 있습니다. 

1. Postman을 시작 하 고 **새** > **요청** 을 선택 하 여 테스트 도구를 설정 합니다. 

   ![테스트 도구 설정 요청](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. *저장 요청* 양식을 입력 한 다음 만든 폴더에 **저장** 합니다.

   ![요청 저장](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. 드롭다운 목록에서 **POST** 를 선택 합니다. 

   ![내 흐름 테스트](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. MS Flow에서 만든 흐름에서 *요청 Url 입력*이라고 표시 된 HTTP POST URL을 붙여 넣습니다.

   ![HTTP POST URL 붙여넣기](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. 흐름 메뉴 모음에서 **내 흐름** 으로 이동 하 여 [흐름](https://flow.microsoft.com/) 으로 돌아가서 잠재 고객을 보내기 위해 만든 흐름을 찾습니다.  흐름 이름 옆의 3 점을 선택 하 고 **편집**을 선택 합니다.

   ![내 흐름-편집](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. 오른쪽 위 모서리에서 **테스트** 를 선택 하 고 "트리거 동작을 수행 합니다."를 선택한 다음 **테스트**를 선택 합니다. 화면 맨 위에 테스트가 시작 되었음을 나타내는 표시가 표시 됩니다.

   ![테스트 흐름-트리거](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Postman 앱으로 돌아가서 HTTPS URL을 붙여 넣은 옆의 **보내기** 를 선택 합니다.

   ![내 흐름 테스트-보내기](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. 흐름으로 돌아가서 결과를 확인 합니다. 모든 항목이 예상 대로 작동 하는 경우 성공 했음을 나타내는 메시지가 표시 됩니다.

   ![흐름 확인 결과](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. 또한 전자 메일을 수신 해야 합니다. 전자 메일 받은 편지함을 확인 합니다. 

    >[!Note] 
    >테스트에서 전자 메일을 볼 수 없는 경우 스팸 및 정크 폴더를 확인 합니다. 아래에서 전자 메일 알림을 구성할 때 추가한 필드 레이블만 확인할 수 있습니다. 제품에서 실제 잠재 고객이 생성 된 경우 본문 및 제목 줄에서 잠재 고객 연락처의 실제 정보를 볼 수도 있습니다.

   ![받은 전자 메일](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>잠재 고객을 HTTPS 엔드포인트로 보내도록 제안 구성

게시 포털에서 제품에 대 한 리드 관리 정보를 구성할 준비가 되 면 다음 단계를 수행 합니다.

1. 제품에 대 한 **제품 설치** 페이지로 이동 합니다.
2. 리드 관리 섹션에서 **연결** 을 선택 합니다.
3. 연결 정보 팝업 창에서 **리드 대상** 에 대해 **https 엔드포인트** 을 선택 하 고 이전 단계를 수행 하 여 만든 흐름의 HTTP POST URL을 **HTTPS 엔드포인트 url** 필드에 붙여넣습니다.
4. **저장**을 선택합니다. 

>[!Note] 
>제안에 대 한 잠재 고객을 받으려면 먼저 나머지 제품을 구성 하 고 게시 해야 합니다.

잠재 고객이 생성되면 Microsoft는 Flow로 잠재 고객을 보냅니다. 그러면 이 잠재 고객은 사용자가 구성한 CRM 시스템 또는 이메일 주소로 라우팅됩니다.

![리드 관리-연결](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![연결 정보](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![연결 정보](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)


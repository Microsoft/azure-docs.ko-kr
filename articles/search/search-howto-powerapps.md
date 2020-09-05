---
title: '자습서: Power Apps에서 쿼리'
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱스에 연결하고, 쿼리를 보내며, 결과를 렌더링하는 Power App을 빌드하는 방법에 대한 단계별 지침입니다.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 887017f60deb832bd5c53f28bde4b57a3d82bde5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918046"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>자습서: Power Apps에서 Cognitive Search 인덱스 쿼리

Power Apps의 빠른 애플리케이션 개발 환경을 활용하여 Azure Cognitive Search에서 검색 가능한 콘텐츠에 대한 사용자 지정 앱을 만듭니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Cognitive Search에 연결
> * 쿼리 요청 설정
> * 캔버스 앱에서 결과 시각화

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 엽니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Power Apps 계정](http://make.powerapps.com)

* [Hotels-sample 인덱스](search-get-started-portal.md)

* [쿼리 API 키](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - 사용자 지정 커넥터 만들기

Power Apps의 커넥터는 데이터 원본 연결입니다. 이 단계에서는 클라우드의 검색 인덱스에 연결하는 사용자 지정 커넥터를 만듭니다.

1. Power Apps에 [로그인](http://make.powerapps.com)합니다.

1. 왼쪽에서 **데이터** > **사용자 지정 커넥터**를 차례로 펼칩니다.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="사용자 지정 커넥터 메뉴" border="true":::

1. **+ 새 사용자 지정 커넥터**, **빈 페이지에서 만들기**를 차례로 선택합니다.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="빈 페이지에서 만들기 메뉴" border="true":::

1. 사용자 지정 커넥터 이름(예: *AzureSearchQuery*)을 지정한 다음, **계속**을 클릭합니다.

1. [일반] 페이지에서 다음 정보를 입력합니다.

   * 아이콘 배경색(예: #007ee5)
   * 설명(예: "Azure Cognitive Search에 대한 커넥터")
   * [호스트]에서 검색 서비스 URL(예: `<yourservicename>.search.windows.net`)을 입력해야 합니다.
   * 기준 URL에 대해 "/"만 입력하면 됩니다.

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="일반 정보 대화 상자" border="true":::

1. [보안] 페이지에서 **인증 유형**으로 *API 키*를 설정하고, 매개 변수 레이블과 매개 변수 이름을 모두 *api-key*로 설정합니다. **매개 변수 위치**에 대해 아래와 같이 *헤더*를 선택합니다.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="인증 유형 옵션" border="true":::

1. [정의] 페이지에서 **+ 새 작업**을 선택하여 인덱스를 쿼리하는 작업을 만듭니다. 요약 값으로 "쿼리"를 입력하고, 작업 ID의 이름을 입력합니다. *"검색 인덱스 쿼리"* 와 같은 설명을 입력합니다.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="새 작업 옵션" border="true":::

1. 아래로 스크롤합니다. [요청]에서 **+ 샘플에서 가져오기** 단추를 선택하여 검색 서비스에 대한 쿼리 요청을 구성합니다.

   * `GET` 동사를 선택합니다.

   * URL에 대해 검색 인덱스에 대한 쿼리 샘플을 입력합니다(모든 문서를 반환하는 경우 `search=*`, 필드를 선택할 수 있도록 하는 경우 `$select=`). API 버전이 필요합니다. 완전히 지정된 URL은 다음과 같습니다. `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * [헤더]에 대해 `Content-Type`을 입력합니다. 

     **Power Apps**는 구문을 사용하여 쿼리에서 매개 변수를 추출합니다. 검색 필드는 명시적으로 정의되었습니다. 

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="샘플에서 가져오기" border="true":::

1. **가져오기**를 클릭하여 요청을 자동으로 채웁니다. 각 매개 변수 옆에 있는 **...** 기호를 클릭하여 매개 변수 메타데이터 설정을 완료합니다. 각 매개 변수를 업데이트한 후 **뒤로**를 클릭하여 [요청] 페이지로 돌아갑니다.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="샘플에서 가져오기 대화 상자" border="true":::

1. *search*: `*`를 **기본값**으로, **필수**를 *False*로, **표시 유형**을 *none*으로 설정합니다. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="search 매개 변수 메타데이터" border="true":::

1. *select*: `HotelName,Description,Address/City`를 **기본값**으로, **필수**를 *False*로, **표시 유형**을 *none*으로 설정합니다.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="매개 변수 메타데이터 선택" border="true":::

1. *api-version*: `2020-06-30`을 **기본값**으로, **필수**를 *True*로, **표시 유형**을 *internal*로 설정합니다.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="version 매개 변수 메타데이터" border="true":::

1. *Content-Type*: `application/json`로 설정합니다.

1. 이러한 변경이 수행되었으면 **Swagger 편집기** 보기로 전환합니다. 매개 변수 섹션에 표시되는 구성은 다음과 같습니다.

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. **3. 요청** 단계로 돌아가서 [응답] 섹션까지 아래로 스크롤합니다. **"기본 응답 추가"** 를 클릭합니다. 이는 Power Apps에서 응답 스키마를 인식하는 데 도움이 되므로 매우 중요합니다. 

1. 응답 샘플을 붙여넣습니다. 응답 샘플은 Azure Portal에서 검색 탐색기를 통해 쉽게 캡처할 수 있습니다. 검색 탐색기에서 요청에 대해 수행한 것과 동일한 쿼리를 입력해야 하지만 **$top=2**를 추가하여 결과를 두 개의 문서로만 제한합니다(`search=*&$select=HotelName,Description,Address/City&$top=2`). 

   Power Apps는 스키마를 검색하는 데 몇 가지 결과만 필요합니다.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > 입력할 수 있는 JSON 응답에는 문자 제한이 있으므로 붙여넣기 전에 JSON을 단순화하는 것이 좋습니다. 값 자체보다 응답의 스키마와 형식이 더 중요합니다. 예를 들어 [설명] 필드를 첫 번째 문장으로만 단순화할 수 있습니다.

1. 오른쪽 위에서 **커넥터 만들기**를 클릭합니다.

## <a name="2---test-the-connection"></a>2 - 연결 테스트

커넥터가 처음 만들어지면 테스트를 위해 [사용자 지정 커넥터] 목록에서 다시 열어야 합니다. 나중에 추가 업데이트를 수행하는 경우 마법사 내에서 테스트할 수 있습니다.

이 작업에는 [쿼리 API 키](search-security-api-keys.md#find-existing-keys)가 필요합니다. 테스트 실행 또는 앱에 포함 여부에 관계없이 연결을 만들 때마다 커넥터에는 Azure Cognitive Search에 연결하는 데 사용되는 쿼리 API 키가 필요합니다.

1. 맨 왼쪽에서 **사용자 지정 커넥터**를 클릭합니다.

1. 이름을 기준으로 커넥터를 검색합니다(이 자습서에서는 "AzureSearchQuery").

1. 커넥터를 선택하고, 작업 목록을 펼친 다음, **속성 보기**를 선택합니다.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="속성 보기" border="true":::

1. 오른쪽 위에서 **편집**을 선택합니다.

1. **4. 테스트**를 선택하여 테스트 페이지를 엽니다.

1. [테스트 작업]에서 **+ 새 연결**을 클릭합니다.

1. 쿼리 API 키를 입력합니다. 이는 인덱스에 대한 읽기 전용 액세스를 위한 Azure Cognitive Search 쿼리입니다. Azure Portal에서 [키를 확인](search-security-api-keys.md#find-existing-keys)할 수 있습니다. 

1. [작업]에서 **테스트 작업** 단추를 클릭합니다. 성공하면 200 상태가 표시되고, 응답 본문에는 검색 결과를 설명하는 JSON이 표시됩니다.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON 응답" border="true":::

## <a name="3---visualize-results"></a>3 - 결과 시각화

이 단계에서는 검색 상자, 검색 단추 및 결과 표시 영역이 있는 Power App을 만듭니다. Power App은 최근에 만든 사용자 지정 커넥터에 연결하여 Azure Search에서 데이터를 가져옵니다.

1. 왼쪽에서 **앱** >  **+ 새 앱** > **캔버스**를 차례로 펼칩니다.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="캔버스 앱 만들기" border="true":::

1. 애플리케이션 유형을 선택합니다. 이 자습서에서는 **휴대폰 레이아웃**이 있는 **빈 앱**을 만듭니다. **Power Apps Studio**가 표시됩니다.

1. Studio에서 **데이터 원본** 탭을 선택하고, 방금 만든 새 커넥터를 클릭합니다. 여기서는 *AzureSearchQuery*라고 합니다. **연결 추가**를 클릭합니다.

   쿼리 API 키를 입력합니다.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="커넥터 연결" border="true":::

    이제 *AzureSearchQuery*는 애플리케이션에서 사용할 수 있는 데이터 원본입니다.

1. **탭 삽입**에서 몇 가지 컨트롤을 캔버스에 추가합니다.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="컨트롤 삽입" border="true":::

1. 다음 요소를 삽입합니다.

   * "쿼리:" 값이 있는 텍스트 레이블
   * 텍스트 입력 요소(*txtQuery*라고 함, 기본값: "*")
   * "검색" 텍스트가 있는 단추 
   * 호출되는 세로 갤러리(*galleryResults*라고 함)

    캔버스는 다음과 같습니다.

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="컨트롤 레이아웃" border="true":::

1. **검색 단추**에서 쿼리를 실행하도록 하려면 다음 작업을 **OnSelect**에 붙여넣습니다.

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   다음 스크린샷에서는 **OnSelect** 작업에 대한 수식 입력줄을 보여 줍니다.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="단추 OnSelect" border="true":::

   이 작업을 수행하면 단추에서 *txtQuery* 텍스트 상자의 텍스트를 쿼리 용어로 사용하여 *azResult*라는 새 컬렉션을 검색 쿼리의 결과로 업데이트합니다.

   > [!NOTE]
   > "'ClearCollect' 함수에 잘못된 함수가 있습니다."라는 수식 구문 오류가 발생하는 경우 다음 작업을 시도하세요.
   > 
   > * 먼저 커넥터 참조가 올바른지 확인합니다. 커넥터 이름을 지우고, 커넥터 이름 입력을 시작합니다. Intellisense에서 올바른 커넥터와 동사가 제안됩니다.
   > 
   > * 오류가 지속되면 커넥터를 삭제하고 다시 만듭니다. 여러 커넥터 인스턴스가 있는 경우 앱에서 잘못된 커넥터를 사용하고 있을 수 있습니다.
   > 

1. 이전 단계를 완료할 때 만든 *azResult* 컬렉션에 세로 갤러리 컨트롤을 연결합니다. 

   갤러리 컨트롤을 선택하고, 속성 창에서 다음 작업을 수행합니다.

   * **DataSource**를 *azResult*로 설정합니다.
   * 인덱스의 데이터 형식에 따라 적합한 **레이아웃**을 선택합니다. 여기서는 *제목, 부제목 및 본문* 레이아웃을 사용했습니다.
   * **필드 편집**에서 시각화하려는 필드를 선택합니다.

    커넥터를 정의할 때 결과 샘플을 제공했으므로 앱에서 인덱스에 사용할 수 있는 필드를 인식합니다.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="갤러리 필드" border="true":::   
 
1. **F5** 키를 눌러 앱을 미리 봅니다.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="최종 앱" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Power Apps를 사용하면 사용자 지정 앱의 빠른 애플리케이션 개발이 가능합니다. 이제 검색 인덱스에 연결하는 방법을 알고 있으므로 사용자 지정 Power App에서 풍부한 시각화 환경을 만드는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Power Apps 학습 카탈로그](/powerapps/learning-catalog/get-started)
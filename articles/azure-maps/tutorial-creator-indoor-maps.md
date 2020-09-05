---
title: Creator를 사용하여 실내 맵 만들기
description: Azure Maps Creator를 사용하여 실내 맵을 만듭니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bf2fbb48c34631bc74a3b712e135b618a1718d8e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88688092"
---
# <a name="use-creator-to-create-indoor-maps"></a>Creator를 사용하여 실내 맵 만들기

이 자습서에서는 실내 맵을 만드는 방법을 보여 줍니다. 이 자습서에서 API를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 실내 맵 그리기 패키지 업로드
> * 그리기 패키지를 맵 데이터로 변환
> * 맵 데이터에서 데이터 세트 만들기
> * 데이터 세트의 데이터에서 타일 세트 만들기
> * Azure Maps WFS(웹 기능 서비스) API를 쿼리하여 맵 기능에 대해 알아보기
> * 맵 기능 및 데이터 세트의 데이터를 사용하여 기능 상태 세트 만들기
> * 기능 상태 세트 업데이트

## <a name="prerequisites"></a>사전 요구 사항

실내 맵을 만들려면 다음을 수행합니다.

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Creator 리소스를 만듭니다](how-to-manage-creator.md).
4. [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)을 다운로드합니다.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

>[!IMPORTANT]
> 이 문서의 API url은 작성자 리소스의 위치에 따라 조정 해야 할 수 있습니다. 자세한 내용은 [Creator Services에](how-to-manage-creator.md#access-to-creator-services)대 한 액세스를 참조 하세요.

## <a name="upload-a-drawing-package"></a>그리기 패키지 업로드

[데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)를 사용하여 그리기 패키지를 Azure Maps 리소스에 업로드합니다.

데이터 업로드 API는 여기에 정의된 패턴을 구현하는 장기 실행 트랜잭션입니다. 작업이 완료되면 `udid`를 사용하여 변환하기 위한 업로드된 패키지에 액세스합니다. 아래 단계에 따라 `udid`를 가져옵니다.

1. Postman 앱을 엽니다. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **컬렉션**을 선택합니다.  컬렉션 이름을 지정하고, **만들기** 단추를 선택합니다.

2. 요청을 만들려면 **새로 만들기**를 다시 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. 이전 단계에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

3. 작성기 탭에서 **POST** HTTP 메서드를 선택하고, 다음 URL을 입력하여 그리기 패키지를 Azure Maps 서비스에 업로드합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. **헤더** 탭에서 `Content-Type` 키의 값을 지정합니다. 그리기 패키지는 압축된 폴더이므로 `application/octet-stream` 값을 사용합니다. **본문** 탭에서 **이진**을 선택합니다. **파일 선택**을 클릭하고 그리기 패키지를 선택합니다.

     ![data-management](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. 파란색 **보내기** 단추를 클릭하고, 요청이 처리될 때까지 기다립니다. 요청이 완료되면 응답의 **헤더** 탭으로 이동합니다. **위치** 키의 값(`status URL`)을 복사합니다.

6. API 호출의 상태를 확인하려면 `status URL`에 대한 **GET** HTTP 요청을 만듭니다. 인증을 위해 기본 구독 키를 URL에 추가해야 합니다. **GET** 요청은 다음 URL 처럼 표시 됩니다.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. **GET** HTTP 요청이 성공적으로 완료되면 `resourceLocation`이 반환됩니다. `resourceLocation`에는 업로드된 콘텐츠에 대한 고유한 `udid`가 포함되어 있습니다. 필요에 따라 다음 단계에서 `resourceLocation` URL을 사용하여 이 리소스에서 메타데이터를 검색할 수 있습니다.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. 콘텐츠 메타데이터를 검색하려면 7단계에서 검색된 `resourceLocation` URL에 대한 **GET** HTTP 요청을 만듭니다. 인증을 위해 기본 구독 키를 URL에 추가해야 합니다. **GET** 요청은 다음 URL과 같습니다.

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. **GET** HTTP 요청이 성공적으로 완료되면 7단계의 `resourceLocation`에 지정된 `udid`, 향후 콘텐츠에 액세스/다운로드할 위치 및 만든 날짜/업데이트한 날짜, 크기 등과 같은 콘텐츠에 대한 기타 메타데이터가 응답 본문에 포함되어 있습니다. 전체 응답의 예제는 다음과 같습니다.

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>그리기 패키지 변환

 이제 그리기 패키지가 업로드되었으므로 업로드된 패키지에 대해 `udid`를 사용하여 패키지를 맵 데이터로 변환합니다. Conversion API는 [여기](creator-long-running-operation.md)에 정의된 패턴을 구현하는 장기 실행 트랜잭션을 사용합니다. 작업이 완료되면 `conversionId`를 사용하여 변환된 데이터에 액세스합니다. 아래 단계에 따라 `conversionId`를 가져옵니다.

1. **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. **저장**을 클릭합니다.

2. 작성기 탭에서 **POST** HTTP 메서드를 선택하고, 다음 URL을 입력하여 업로드된 그리기 패키지를 맵 데이터로 변환합니다. 업로드된 패키지에 대해 `udid`를 사용합니다.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > 이 문서의 API url은 작성자 리소스의 위치에 따라 조정 해야 할 수 있습니다. 자세한 내용은 [Creator Services에](how-to-manage-creator.md#access-to-creator-services)대 한 액세스를 참조 하세요.

3. **보내기** 단추를 클릭하고, 요청이 처리될 때까지 기다립니다. 요청이 완료되면 응답의 **헤더** 탭으로 이동하여 **위치** 키를 찾습니다. 변환 요청에 대한 `status URL`인 **위치** 키의 값을 복사합니다. 다음 단계에서이를 사용 합니다.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="위치 키의 값 복사":::

4. 작성기 탭에서 새 **GET** HTTP 메서드를 시작합니다. Azure Maps 기본 구독 키를 `status URL`에 추가합니다. 3 단계에서 복사한에서 **GET** 요청을 수행 `status URL` 합니다. 는 `status URL` 다음 URL과 유사 합니다.

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0
    ```

    변환 프로세스가 아직 완료되지 않은 경우 다음 JSON 응답과 같은 내용이 표시될 수 있습니다.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. 요청이 성공적으로 완료되면 응답 본문에 성공 상태 메시지가 표시됩니다.  변환된 패키지에 대한 `resourceLocation` URL에서 `conversionId`를 복사합니다. `conversionId`는 다른 API에서 변환된 맵 데이터에 액세스하는 데 사용됩니다.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Postman 애플리케이션은 기본적으로 HTTP 장기 실행 요청을 지원하지 않습니다. 따라서 상태 URL에서 **GET** 요청을 수행하는 동안 시간이 오래 걸릴 수 있습니다.  약 30초 정도 기다린 후 성공 또는 실패가 응답에 표시될 때까지 **보내기** 단추를 다시 클릭합니다.

그리기 패키지 샘플은 오류 또는 경고 없이 변환되어야 합니다. 그러나 사용자 고유의 그리기 패키지에서 오류 또는 경고를 받으면 [그리기 오류 시각화 도우미](drawing-error-visualizer.md)에 대한 링크가 JSON 응답에 제공됩니다. 그리기 오류 시각화 도우미를 사용하면 오류 및 경고에 대한 세부 정보를 검사할 수 있습니다. 변환 오류 및 경고를 해결하는 방법에 대한 추천 사항을 받으려면 [그리기 변환 오류 및 경고](drawing-conversion-error-codes.md)를 참조하세요.

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>데이터 세트 만들기

데이터 세트는 건물, 수준 및 방과 같은 맵 기능의 컬렉션입니다. 데이터 세트를 만들려면 [데이터 세트 만들기 API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)를 사용합니다. 데이터 세트 만들기 API는 변환된 그리기 패키지에 대해 `conversionId`를 사용하고, 만든 데이터 세트에 대한 `datasetId`를 반환합니다. 아래 단계에서는 데이터 세트를 만드는 방법을 보여 줍니다.

1. Postman 애플리케이션에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. 페이지 맨 아래에 있는 **저장**

2. 새 데이터 세트를 만들기 위해 [데이터 세트 만들기 API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)에 대한 **POST** 요청을 수행합니다. 요청을 제출하기 전에 변환 프로세스의 5단계에서 가져온 `conversionId`를 사용하여 구독 키와 `conversionId`를 모두 추가합니다.  요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **헤더** 응답의 **위치** 키에서 `statusURL`을 가져옵니다.

4. `datasetId`를 가져오는 **GET** 요청을 `statusURL`에서 수행합니다. 인증을 위해 Azure Maps 기본 구독 키를 추가합니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. **GET** HTTP 요청이 성공적으로 완료되면 만든 데이터 세트에 대한 `datasetId`가 응답 헤더에 포함됩니다. `datasetId`를 복사합니다. `datasetId`는 타일 세트를 만드는 데 사용해야 합니다.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>타일 세트 만들기

타일 세트는 맵에서 렌더링하는 벡터 타일 세트입니다. 타일 세트는 기존 데이터 세트에서 만들어집니다. 그러나 타일 세트는 원본 데이터 세트와 독립적입니다. 데이터 세트를 삭제해도 타일 세트는 계속 존재합니다. 타일 세트를 만들려면 다음 단계를 수행합니다.

1. Postman 애플리케이션에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. 페이지 맨 아래에 있는 **저장**

2. 작성기 탭에서 **POST**를 수행합니다. 요청 URL은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. `statusURL`에서 타일 세트에 대한 **GET** 요청을 수행합니다. 인증을 위해 Azure Maps 기본 구독 키를 추가합니다. 요청은 다음 URL과 같습니다.

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key=<Azure-Maps-Primary-Subscription-key>
    ```

4. **GET** HTTP 요청이 성공적으로 완료되면 만든 타일 세트에 대한 `tilesetId`가 응답 헤더에 포함됩니다. `tilesetId`를 복사합니다.

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>WFS API를 사용하여 데이터 세트 쿼리

 데이터 세트는 [WFS API](https://docs.microsoft.com/rest/api/maps/wfs)를 사용하여 쿼리할 수 있습니다. WFS API를 사용하면 기능 컬렉션, 특정 컬렉션 또는 기능 **ID**가 있는 특정 기능을 쿼리할 수 있습니다. 기능 **ID**는 데이터 세트 내에서 기능을 고유하게 식별합니다. 예를 들어 지정된 상태 세트에서 업데이트해야 하는 기능 상태를 식별하는 데 사용됩니다.

1. Postman 애플리케이션에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. 페이지 맨 아래에 있는 **저장**

2. 데이터 세트의 컬렉션 목록을 표시하는 **GET** 요청을 수행합니다. `<dataset-id>`를 `datasetId`로 바꿉니다. 자리 표시자 대신 Azure Maps 기본 키를 사용합니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. 응답 본문은 GeoJSON 형식으로 전달되며 데이터 세트의 모든 컬렉션을 포함합니다. 간단히 하기 위해 다음 예제에서는 `unit` 컬렉션만 보여 줍니다. 모든 컬렉션이 포함된 예제를 보려면 [WFS 컬렉션 설명 API](https://docs.microsoft.com/rest/api/maps/wfs/collectiondescriptionpreview)를 참조하세요. 컬렉션에 대해 자세히 알아보려면 `link` 요소 내의 URL을 클릭하면 됩니다.

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. `unit` 기능 컬렉션에 대한 **GET** 요청을 수행합니다.  `{datasetId}`를 `datasetId`로 바꿉니다. 자리 표시자 대신 Azure Maps 기본 키를 사용합니다. 응답 본문에는 `unit` 컬렉션의 모든 기능이 포함됩니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. 동적으로 수정할 수 있는 스타일 속성이 있는 단위 기능에 대한 기능 `id`를 복사합니다.  단위 점유율 상태 및 온도를 동적으로 업데이트할 수 있으므로 다음 섹션에서 이 기능 `id`를 사용합니다. 다음 예제에서 기능 `id`는 "UNIT26"입니다. 이 기능의 스타일 속성을 상태라고 하며, 이 기능을 사용하여 상태 세트를 만듭니다.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>기능 상태 세트 만들기

1. Postman 애플리케이션에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. **요청 이름** 입력하고 컬렉션을 선택합니다. 페이지 맨 아래에 있는 **저장**

2. [상태 세트 만들기 API](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview)에 대한 **POST** 요청을 수행합니다. 수정하려는 상태가 포함된 데이터 세트의 `datasetId`를 사용합니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **POST** 요청의 **헤더**에서 `Content-Type`을 `application/json`으로 설정합니다. `occupied` 및 `temperature` *상태*에 대한 변경 내용을 반영하려면 **본문**에서 아래 스타일을 제공합니다. 완료되면 **보내기**를 클릭합니다.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. 응답 본문에서 `statesetId`를 복사합니다.

5. 상태를 업데이트하는 **POST** 요청을 만듭니다. Azure Maps 구독 키를 사용하여 statesetId 및 기능 `ID`를 전달합니다. 요청은 다음 URL과 같습니다.

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **POST** 요청의 **헤더**에서 `Content-Type`을 `application/json`으로 설정합니다. **POST** 요청의 **본문**에서 JSON을 복사하여 아래 샘플에 붙여넣습니다.

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

    >[!NOTE]
    > 게시된 타임스탬프가 이전 요청의 타임스탬프 이후인 경우에만 업데이트가 저장됩니다. 이전에 구성한 모든 키 이름을 전달할 수 있습니다.

7. 업데이트에 성공하면 `200 OK` HTTP 상태 코드를 받습니다. [동적 스타일 지정](indoor-map-dynamic-styling.md)을 실내 맵에 구현한 경우 업데이트가 지정된 타임스탬프에 렌더링된 맵에 표시됩니다.

[기능 상태 가져오기 API](https://docs.microsoft.com/rest/api/maps/featurestate/getstatespreview)를 사용하면 기능 `ID`를 사용하여 기능의 상태를 검색할 수 있습니다. 또한 [기능 상태 삭제 API](https://docs.microsoft.com/rest/api/maps/featurestate/deletestatesetpreview)를 사용하여 상태 세트 및 해당 리소스를 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 실내 맵 그리기 패키지 업로드
> * 그리기 패키지를 맵 데이터로 변환
> * 맵 데이터에서 데이터 세트 만들기
> * 데이터 세트의 데이터에서 타일 세트 만들기
> * Azure Maps WFS 서비스를 쿼리하여 맵 기능에 대해 알아보기
> * 맵 기능 및 데이터 세트의 데이터를 사용하여 기능 상태 세트 만들기
> * 기능 상태 세트 업데이트

이제 다음 가이드로 이동하는 데 필요한 기술을 갖추었습니다.

> [!div class="nextstepaction"]
> [실내 맵 모듈 사용](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [실내 맵 동적 스타일 지정 구현](indoor-map-dynamic-styling.md)

이 문서에서 설명하는 다양한 Azure Maps 서비스에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [데이터 업로드](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 변환](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [데이터 세트](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [타일 세트](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [기능 상태 세트](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS 서비스](creator-indoor-maps.md#web-feature-service-api)

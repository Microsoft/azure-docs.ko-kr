---
title: 결과 정렬에 대한 C# 자습서
titleSuffix: Azure Cognitive Search
description: 이 자습서에서는 검색 결과를 정렬하는 방법을 보여줍니다. 이전의 호텔 프로젝트를 기반으로 기본 속성, 보조 속성을 정렬하며 부스팅 기준을 추가하는 점수 매기기 프로필을 포함합니다.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121557"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C# 자습서: 결과 정렬 - Azure Cognitive Search

자습서 시리즈의 이 시점까지는 결과가 반환되어 기본 순서로 표시됩니다. 이는 데이터가 위치한 순서이거나 기본 _점수 매기기 프로필_이 정의되었을 수 있으며, 이 프로필은 정렬 매개 변수를 지정하지 않은 경우에 사용됩니다. 이 자습서에서는 기본 속성에 따라 결과를 정렬하는 방법을 살펴본 다음, 기본 속성이 동일한 결과에 대해 보조 속성에 대한 해당 선택 항목을 정렬하는 방법에 대해 살펴보겠습니다. 최종적인 예제에서는 숫자 값에 따라 정렬하는 대신 사용자 지정 점수 매기기 프로필에 따라 정렬하는 방법을 보여 줍니다. _복합 형식_의 표시에 대해서도 좀 더 자세히 살펴봅니다.

반환된 결과를 쉽게 비교하기 위해 이 프로젝트는 [C# 자습서: 검색 결과 페이지 매김 - Azure Cognitive Search](tutorial-csharp-paging.md) 자습서에서 만든 페이지 매김 프로젝트를 기반으로 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 단일 속성 기준의 결과 정렬
> * 여러 속성 기준의 결과 정렬
> * 지리적 지점으로부터의 거리 기준의 결과 필터링
> * 점수 매기기 프로필 기준의 결과 정렬

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

[C# 자습서: 검색 결과 페이지 매김 - Azure Cognitive Search](tutorial-csharp-paging.md) 프로젝트를 가동하여 실행합니다. 이 프로젝트는 사용자 고유의 버전이거나 GitHub: [첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples)의 버전을 설치해도 됩니다.

## <a name="order-results-based-on-one-property"></a>단일 속성 기준의 결과 정렬

호텔 등급과 같은 하나의 속성을 기준으로 결과를 정렬하는 경우 정렬된 결과를 원할 뿐만 아니라 정렬이 올바른지도 확인하기를 원합니다. 즉 등급에 따라 정렬하는 경우 등급을 보기에 표시해야 합니다.

이 자습서에서는 각 호텔에 대한 결과 표시, 가장 저렴한 객실 요금 및 가장 비싼 객실 요금도 조금 더 추가할 것입니다. 정렬에 대해 자세히 살펴보면서 정렬하는 항목이 보기에도 표시되는지 확인하기 위해 값도 추가할 것입니다.

정렬을 사용하도록 설정하기 위해 모델을 수정할 필요가 없습니다. 보기와 컨트롤러는 업데이트해야 합니다. 먼저 홈 컨트롤러를 여세요.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>검색 매개 변수에 OrderBy 속성 추가

1. 단일 숫자 속성을 기준으로 결과를 정렬하는 데 필요한 모든 작업은 **OrderBy** 매개 변수를 속성 이름으로 설정하는 것입니다. **Index(SearchData model)** 메서드에서 다음 줄을 검색 매개 변수에 추가합니다.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > 기본 순서는 오름차순이지만, 이를 명확히 하기 위해 **asc**를 속성에 추가할 수 있습니다. 내림차순은 **desc**를 추가하여 지정합니다.

2. 이제 앱을 실행하고 일반적인 검색 용어를 입력합니다. 사용자가 아닌 개발자에게는 결과를 쉽게 확인할 수 있는 방법이 없으므로 결과가 올바른 순서일 수도 있고 아닐 수도 있습니다!

3. 결과가 등급에 따라 정렬된다는 것을 명확히 지정해 보겠습니다. 먼저 hotel.css 파일의 **box1** 및 **box2** 클래스를 다음 클래스로 바꿉니다(이러한 클래스는 모두 이 자습서에 필요한 새 클래스임).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >브라우저는 일반적으로 css 파일을 캐시합니다. 그러면 기존 css 파일을 사용하여 편집한 내용이 무시될 수 있습니다. 이 문제와 관련하여 버전 매개 변수가 포함된 쿼리 문자열을 링크에 추가하는 것이 좋습니다. 다음은 그 예입니다.
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >브라우저에서 이전 css 파일을 사용한다고 생각되면 버전 번호를 업데이트합니다.

4. **Index(SearchData model)** 메서드에서 **Rating** 속성을 **Select** 매개 변수에 추가합니다.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. 보기(index.cshtml)를 열고, 렌더링 루프( **&lt;!-- Show the hotel data(호텔 데이터를 표시합니다). --&gt;** )를 다음 코드로 바꿉니다.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. 등급은 표시된 첫 번째 페이지와 무한 스크롤을 통해 호출되는 후속 페이지에서 모두 사용할 수 있어야 합니다. 이 두 상황 중에서 후자의 경우 컨트롤러의 **Next** 작업과 보기의 **scrolled** 함수를 모두 업데이트해야 합니다. 컨트롤러부터 **Next** 메서드를 다음 코드로 변경합니다. 이 코드는 등급 텍스트를 만들고 전달합니다.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. 이제 등급 텍스트를 표시하도록 보기의 **scrolled** 함수를 업데이트합니다.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. 이제 앱을 다시 실행합니다. "wifi"와 같은 일반적인 용어를 검색하여 결과가 호텔 등급의 내림차순으로 정렬되는지 확인합니다.

    ![등급 기준 정렬](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    여러 호텔의 등급이 동일하므로 데이터를 찾은 순서로 다시 정렬되어 표시됩니다. 이는 임의적인 순서입니다.

    두 번째 수준의 정렬 추가를 살펴보기 전에 객실 요금의 범위를 표시하는 몇 가지 코드를 추가해 보겠습니다. 이 코드를 _복합 형식_에서 데이터를 추출하는 두 표시에 모두 추가합니다. 또한 가격 기준으로 정렬한 결과(아마도 가장 저렴한 가격이 첫 번째임)에 대해서도 논의할 수 있습니다.

### <a name="add-the-range-of-room-rates-to-the-view"></a>보기에 객실 요금 범위 추가

1. 가장 저렴하고 가장 비싼 객실 요금이 포함된 속성을 Hotel.cs 모델에 추가합니다.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. 홈 컨트롤러에서 **Index(SearchData model)** 작업이 완료되면 객실 요금을 계산합니다. 임시 데이터를 저장한 후 계산을 추가합니다.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. 컨트롤러의 **Index(SearchData model)** 작업 메서드에서 **Rooms** 속성을 **Select** 매개 변수에 추가합니다.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. 보기의 렌더링 루프를 변경하여 결과의 첫 번째 페이지에 대한 요금 범위를 표시합니다.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. 이후의 결과 페이지에 대한 요금 범위를 전달하도록 홈 컨트롤러의 **Next** 메서드를 변경합니다.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. 객실 요금 텍스트를 처리하도록 보기의 **scrolled** 함수를 업데이트합니다.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. 앱을 실행하고 객실 요금 범위가 표시되는지 확인합니다.

    ![객실 요금 범위 표시](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

객실이 이미 요금을 기준으로 정렬된 경우에도 검색 매개 변수의 **OrderBy** 속성은 가장 저렴한 객실 요금을 제공하기 위해 **Rooms.BaseRate**와 같은 항목을 허용하지 않습니다. 이 경우 객실은 요금에 따라 정렬되지 않습니다. 객실 요금에 따라 정렬된 샘플 데이터 세트의 호텔을 표시하려면 홈 컨트롤러에서 결과를 정렬하고 이러한 결과를 원하는 순서대로 보기에 보내야 합니다.

## <a name="order-results-based-on-multiple-values"></a>여러 값 기준의 결과 정렬

이제 문제는 동일한 등급의 호텔을 구분하는 방법입니다. 한 가지 좋은 방법은 호텔을 마지막으로 리모델링한 시간을 기준으로 정렬하는 것입니다. 즉 호텔을 더 최근에 리모델링할수록 해당 호텔이 결과에 더 높은 순서로 표시됩니다.

1. 두 번째 수준의 정렬을 추가하려면 **Index(SearchData model)** 메서드에서 **LastRenovationDate** 속성을 포함하도록 **OrderBy** 및 **Select** 속성을 변경합니다.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >**OrderBy** 목록에는 임의 개수의 속성을 입력할 수 있습니다. 호텔의 등급과 리모델링 날짜가 동일한 경우 호텔을 구분하기 위해 세 번째 속성을 입력할 수 있습니다.

2. 다시 한번, 보기에서 리모델링 날짜를 확인하여 정렬이 올바른지 확인해야 합니다. 리모델링과 같은 경우에는 아마도 1년이 필요할 것입니다. 보기의 렌더링 루프를 다음 코드로 변경합니다.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. 마지막 리모델링 날짜의 연도 구성 요소를 전달하도록 홈 컨트롤러의 **Next** 메서드를 변경합니다.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. 리모델링 텍스트를 표시하도록 보기의 **scrolled** 함수를 변경합니다.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. 앱을 실행합니다. "pool"(수영장) 또는 "view"(전망)와 같은 일반적인 용어를 검색하여 동일한 등급의 호텔이 이제 리모델링 날짜의 내림차순으로 표시되는지 확인합니다.

    ![리모델링 날짜 기준 정렬](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>지리적 지점으로부터의 거리 기준의 결과 필터링

등급 및 리모델링 날짜는 내림차순으로 가장 잘 표시되는 속성의 예입니다. 사전순 목록은 오름차순을 잘 사용하는 예입니다(예: 하나의 **OrderBy** 속성만 있고 **HotelName**으로 설정된 경우 사전순으로 표시됨). 그러나 샘플 데이터의 경우 지리적 지점과의 거리가 더 적합할 것입니다.

지리적 거리를 기준으로 결과를 표시하려면 몇 가지 단계가 필요합니다.

1. 경도, 위도 및 반지름 매개 변수가 있는 필터를 입력하여 지정된 지점에서 지정된 반지름 밖에 있는 모든 호텔을 필터링합니다. 경도는 먼저 POINT 함수에 지정됩니다. 반지름은 킬로미터 단위입니다.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. 위의 필터는 거리를 기준으로 결과를 정렬하지 _않으며_ 이상값만 제거할 뿐입니다. 결과를 정렬하려면 geoDistance 메서드를 지정하는 **OrderBy** 설정을 입력합니다.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Azure Cognitive Search에서 거리 필터를 사용하여 결과를 반환했지만 데이터와 지정된 지점 사이를 계산한 거리는 반환되지 _않습니다_. 이 값을 결과에 표시하려면 보기 또는 컨트롤러에서 해당 값을 다시 계산합니다.

    다음 코드는 두 개의 lat/lon(위도/경도) 지점 사이의 거리를 계산합니다.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. 이제 이러한 개념을 함께 연결해야 합니다. 그러나 이러한 코드 조각은 자습서에서 어느 정도까지 진행하는 코드이며, 지도 기반 앱을 빌드하는 것은 독자를 위한 연습용으로 남겨집니다. 이 예제를 좀 더 수행하려면 반지름이 있는 도시 이름을 입력하거나 지도에서 지점을 찾아 반지름을 선택하는 것이 좋습니다. 이러한 옵션을 자세히 알아보려면 다음 리소스를 참조하세요.

* [Azure Maps 설명서](https://docs.microsoft.com/azure/azure-maps/)
* [Azure Maps 검색 서비스를 사용하여 주소 찾기](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>점수 매기기 프로필 기준의 결과 정렬

지금까지 자습서에 나와 있는 예제에서는 _정확한_ 정렬 프로세스를 제공하면서 숫자 값(등급, 리모델링 날짜, 지리적 거리)을 기준으로 정렬하는 방법을 보여 주었습니다. 그러나 일부 검색과 일부 데이터에서 자체적으로 두 데이터 요소를 쉽게 비교할 수 없습니다. Azure Cognitive Search에는 _점수 매기기_라는 개념이 있습니다. _점수 매기기 프로필_은 더 복잡하고 정성적인 비교를 제공하는 데 사용할 수 있는 데이터 세트에 대해 지정할 수 있습니다. 예를 들어 텍스트 기반 데이터를 비교하여 먼저 표시할 데이터를 결정할 때 가장 유용합니다.

점수 매기기 프로필은 일반적으로 사용자가 정의하는 것이 아니라 데이터 세트의 관리자가 정의합니다. 호텔 데이터에 대한 몇 가지 점수 매기기 프로필이 설정되어 있습니다. 점수 매기기 프로필이 정의된 방법을 살펴본 다음, 이 프로필을 검색하는 코드를 작성해 보겠습니다.

### <a name="how-scoring-profiles-are-defined"></a>점수 매기기 프로필을 정의하는 방법

점수 매기기 프로필의 세 가지 예제를 살펴보고, 각 프로필이 결과 순서에 _미치는_ 영향을 알아보겠습니다. 이러한 프로필은 데이터 관리자가 작성합니다. 하지만 앱 개발자는 이를 작성하지는 않지만 구문을 살펴보면 도움이 됩니다.

1. 다음은 **OrderBy** 또는 **ScoringProfile** 매개 변수를 지정하지 않을 때 사용되는 호텔 데이터 세트에 대한 기본 점수 매기기 프로필입니다. 검색 텍스트가 호텔 이름, 설명 또는 태그(편의 시설) 목록에 있는 경우 이 프로필은 호텔에 대한 _점수_를 높입니다. 점수 매기기의 가중치가 특정 필드를 선호하는 방법을 확인합니다. 검색 텍스트가 아래에 나열되지 않은 다른 필드에 나타나면 가중치가 1이 됩니다. 분명히 점수가 높을수록 보기에서 해당 결과가 더 먼저 나타납니다.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. 제공된 매개 변수에 하나 이상의 태그("편의 시설"이라고 함) 목록이 포함된 경우 다음 점수 매기기 프로필은 점수를 크게 높입니다. 이 프로필의 요점은 텍스트가 포함된 매개 변수를 _제공해야 한다_는 것입니다. 매개 변수가 비어 있거나 제공되지 않으면 오류가 throw됩니다.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. 이 세 번째 예제에서 등급은 점수를 크게 높입니다. 마지막 리모델링 날짜도 점수를 높이지만, 해당 데이터가 현재 날짜의 730일(2년) 이내인 경우에만 점수를 높일 수 있습니다.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    이제 이러한 프로필이 생각하는 대로 작동하는지 확인해 보겠습니다!

### <a name="add-code-to-the-view-to-compare-profiles"></a>보기에 프로필을 비교하는 코드 추가

1. index.cshtml 파일을 열고, &lt;body&gt; 섹션을 다음 코드로 바꿉니다.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. SearchData.cs 파일을 열고, **SearchData** 클래스를 다음 코드로 바꿉니다.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. hotels.css 파일을 열고, 다음 HTML 클래스를 추가합니다.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>컨트롤러에 점수 매기기 프로필을 지정하는 코드 추가

1. 홈 컨트롤러 파일을 엽니다. 다음 **using** 문(목록 만들기 지원)을 추가합니다.

    ```cs
    using System.Linq;
    ```

2.  다음 예제에서는 초기 보기를 반환하는 것 외에도 좀 더 많은 작업을 수행하기 위해 **Index**에 대한 초기 호출이 필요합니다. 이 메서드는 이제 보기에 표시할 최대 20개의 편의 시설을 검색합니다.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. 패싯을 임시 스토리지에 저장하고, 임시 스토리지에서 이를 복구하여 모델을 채우는 두 개의 프라이빗 메서드가 필요합니다.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. 필요에 따라 **OrderBy** 및 **ScoringProfile** 매개 변수를 설정해야 합니다. 기존 **Index(SearchData model)** 메서드를 다음으로 바꿉니다.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    각 **switch**의 선택 항목에 대한 주석을 빠짐없이 참조합니다.

5. 여러 속성 기준의 정렬에 대한 이전 섹션의 추가 코드를 완료한 경우 **Next** 작업을 변경할 필요가 없습니다.

### <a name="run-and-test-the-app"></a>앱 실행 및 테스트

1. 앱을 실행합니다. 보기에는 편의 시설의 전체 세트가 표시됩니다.

2. 정렬의 경우 "숫자 등급순"을 선택하면 이 자습서에서 이미 구현한 숫자순 정렬이 제공되며, 동일한 등급의 호텔 중에서는 리모델링 날짜를 사용하여 결정합니다.

![등급 기준의 "beach"(해변) 정렬](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. 이제 "편의 시설순" 프로필을 사용해 봅니다. 다양한 편의 시설을 선택하고, 결과 목록에서 해당 편의 시설을 갖춘 호텔이 승격되는지 확인합니다.

![프로필 기준의 "beach" 정렬](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. "리모델링 날짜/등급순 프로필"을 시도하여 예상대로 나타나는지 확인합니다. 최근에 리모델링한 호텔만 _freshness_(신선도)를 높여야 합니다.

### <a name="resources"></a>리소스

자세한 내용은 [Azure Cognitive Search 인덱스에 점수 매기기 프로필 추가](https://docs.microsoft.com/azure/search/index-add-scoring-profiles)를 참조하세요.

## <a name="takeaways"></a>핵심 내용

이 프로젝트에서 다음 핵심 내용을 기억하세요.

* 사용자는 가장 관련성이 높은 검색 결과를 먼저 정렬한다고 예상합니다.
* 데이터는 정렬하기 쉽도록 구조화해야 합니다. 데이터가 추가 코드 없이 정렬할 수 있도록 구조화되지 않았으므로 "가장 저렴한 항목"을 기준으로 먼저 쉽게 정렬할 수 없었습니다.
* 더 높은 수준의 정렬에서 동일한 값이 있는 결과를 구분하기 위해 다양한 정렬 수준이 있을 수 있습니다.
* 당연히 일부 결과는 오름차순(예: 한 지점에서 떨어진 거리), 일부는 내림차순(예: 손님 등급)으로 정렬됩니다.
* 데이터 세트에 대해 숫자 비교를 사용할 수 없거나 충분히 스마트하지 않은 경우에 점수 매기기 프로필을 정의할 수 있습니다. 각 결과의 점수를 매기면 결과를 지능적으로 정렬하고 표시하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 C# 자습서를 완료했으므로 Azure Cognitive Search API에 대한 유용한 지식을 갖추었습니다.

추가 참조 및 자습서를 보려면 [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure) 또는 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/)의 다른 자습서를 찾아보는 것이 좋습니다.

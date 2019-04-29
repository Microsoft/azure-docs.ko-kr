---
title: 필터링, 정렬, 페이징 Media Services 엔터티-Azure | Microsoft Docs
description: 이 문서에서는 Azure Media Services 엔터티의 필터링, 순서 지정, 페이징에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732368"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services 엔터티 필터링, 순서 지정, 페이징

Media Services에서 지원하는 Media Services v3 엔터티에 대한 OData 쿼리 옵션은 다음과 같습니다. 

* $filter 
* $orderby 
* $top 
* $skiptoken 

연산자 설명:

* Eq = 같음
* Ne = 같지 않음
* Ge = 크거나 같음
* Le = 작거나 같음
* Gt = 보다 큼
* Lt = 보다 작음

날짜/시간 형식의 엔터티 속성은 언제나 UTC 형식입니다.

## <a name="page-results"></a>페이지 결과

쿼리 응답에 많은 항목이 포함된 경우 서비스에서 "\@odata.nextLink" 속성을 반환하여 결과의 다음 페이지를 가져옵니다. 전체 결과 집합을 통해 페이지에 사용할 수 있습니다. 페이지 크기는 구성할 수 없습니다. 페이지 크기는 엔터티 형식에 따라 달라집니다. 자세한 내용은 다음 개별 섹션을 읽으세요.

(해당 변경 내용이 다운로드되지 않은 컬렉션의 일부인 경우)컬렉션을 통해 페이징하는 동안 엔터티가 생성되거나 삭제되면 변경 내용이 반환된 결과에 반영됩니다. 

> [!TIP]
> 항상 다음 링크를 사용하여 컬렉션을 열거하고, 특정 페이지 크기에 따라 달라지지 않아야 합니다.

## <a name="assets"></a>자산

### <a name="filteringordering"></a>필터링/순서

다음 표에서는 [자산](https://docs.microsoft.com/rest/api/media/assets) 속성에 필터림 및 순서 지정 옵션을 적용하는 방법을 보여줍니다. 

|이름|Filter|순서|
|---|---|---|
|id|||
|이름|eq, gt, lt| 오름차순 및 내림차순|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq, gt, lt| 오름차순 및 내림차순|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|형식|||

다음 C# 예제에서는 만든 날짜로 필터링합니다.

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>페이지 매김 

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 1000입니다.

#### <a name="c-example"></a>C# 예제

다음 C# 예제에서는 계정의 모든 자산을 통해 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>REST 예제

다음 예제에서 $skiptoken이 사용되는 위치를 잘 보세요. *amstestaccount*를 해당하는 계정 이름으로 바꾸고 *api-version* 값을 최신 버전으로 설정합니다.

다음과 같은 자산 목록을 요청하면:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

다음과 유사한 응답을 받게 됩니다.

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

그 후 가져오기 요청을 보내서 다음 페이지를 요청합니다.

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

더 많은 REST 예제는 [자산 - 목록](https://docs.microsoft.com/rest/api/media/assets/list)을 참조하세요.

## <a name="content-key-policies"></a>콘텐츠 키 정책

### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 [콘텐츠 키 정책](https://docs.microsoft.com/rest/api/media/contentkeypolicies) 속성에 적용하는 방법이 나와 있습니다. 

|이름|Filter|순서|
|---|---|---|
|id|||
|이름|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.created |eq, ne, ge, le,  gt, lt|오름차순 및 내림차순|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.options |||
|properties.policyId|eq, ne||
|형식|||

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

다음 C# 예제에서는 계정의 모든 **콘텐츠 키 정책**을 열거하는 방법을 보여 줍니다.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST 예제는 [콘텐츠 키 정책 - List](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)를 참조하세요.

## <a name="jobs"></a>교육

### <a name="filteringordering"></a>필터링/순서

다음 표에는 [작업](https://docs.microsoft.com/rest/api/media/jobs) 속성에 이러한 옵션을 적용하는 방법이 나와 있습니다. 

| 이름    | Filter                        | 순서 |
|---------|-------------------------------|-------|
| 이름                    | eq            | 오름차순 및 내림차순|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| 오름차순 및 내림차순|
| properties.lastModified | gt, ge, lt, le | 오름차순 및 내림차순| 

### <a name="pagination"></a>페이지 매김

작업 페이지 매김은 Media Services v3에서 지원됩니다.

다음 C# 예제에서는 계정의 모든 작업을 통해 열거하는 방법을 보여줍니다.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST 예제는 [작업 - 목록](https://docs.microsoft.com/rest/api/media/jobs/list)을 참조하세요.

## <a name="streaming-locators"></a>스트리밍 로케이터

### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 StreamingLocator 속성에 적용하는 방법이 나와 있습니다. 

|이름|Filter|순서|
|---|---|---|
|id |||
|이름|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le,  gt, lt|오름차순 및 내림차순|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|형식   |||

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

다음 C# 예제에서는 계정의 모든 StreamingLocators를 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

REST 예제의 경우 [스트리밍 로케이터 - List](https://docs.microsoft.com/rest/api/media/streaminglocators/list)를 참조하세요.

## <a name="streaming-policies"></a>스트리밍 정책

### <a name="filteringordering"></a>필터링/순서

다음 표에는 이러한 옵션을 StreamingPolicy 속성에 적용하는 방법이 나와 있습니다. 

|이름|Filter|순서|
|---|---|---|
|id|||
|이름|eq, ne, ge, le, gt, lt|오름차순 및 내림차순|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le,  gt, lt|오름차순 및 내림차순|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|형식|||

### <a name="pagination"></a>페이지 매김

네 개의 활성화된 정렬 순서 각각에 대해 페이지 매김이 지원됩니다. 현재 페이지 크기는 10입니다.

다음 C# 예제에서는 계정의 모든 StreamingPolicies를 열거하는 방법을 보여줍니다.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST 예제의 경우 [스트리밍 정책 - 목록](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)을 참조하세요.

## <a name="transform"></a>변환

### <a name="filteringordering"></a>필터링/순서

다음 표에는 [변환](https://docs.microsoft.com/rest/api/media/transforms) 속성에 이러한 옵션을 적용하는 방법이 나와 있습니다. 

| 이름    | Filter                        | 순서 |
|---------|-------------------------------|-------|
| 이름                    | eq            | 오름차순 및 내림차순|
| properties.created      | gt, ge, lt, le| 오름차순 및 내림차순|
| properties.lastModified | gt, ge, lt, le | 오름차순 및 내림차순|

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-dotnet-quickstart.md)

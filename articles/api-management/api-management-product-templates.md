---
title: Azure API Management의 제품 템플릿 | Microsoft Docs
description: Azure API Management 개발자 포털에서 제품 페이지의 콘텐츠를 사용자 지정하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 14090e21fb7c6ca07fe63220ffd1d44d483ac869
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61088191"
---
# <a name="product-templates-in-azure-api-management"></a>Azure API Management의 제품 템플릿

Azure API Management는 해당 콘텐츠를 구성하는 템플릿 집합을 사용하여 개발자 포털 페이지의 콘텐츠를 사용자 지정하는 기능을 제공합니다. 이러한 템플릿에서 [DotLiquid](http://dotliquidmarkup.org/) 구문 및 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) 및 제공된 지역화 [String 리소스](api-management-template-resources.md#strings), [Glyph 리소스](api-management-template-resources.md#glyphs) 및 [Page 컨트롤](api-management-page-controls.md)의 집합과 같은 편집기를 사용하여 필요에 따라 페이지 콘텐츠를 유연하게 구성할 수 있습니다.  
  
 이 섹션의 템플릿을 통해 개발자 포털에서 제품 페이지의 콘텐츠를 사용자 지정할 수 있습니다.  
  
-   [제품 목록](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  다음 문서에는 샘플 기본 템플릿이 포함되어 있지만 지속적인 향상으로 인해 변경될 수 있습니다. 원하는 개별 템플릿으로 이동하여 개발자 포털에서 라이브 기본 템플릿을 볼 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)을 참조하세요.  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> 제품 목록  
 **제품 목록** 템플릿을 통해 개발자 포털에서 제품 목록 페이지의 본문을 사용자 지정할 수 있습니다.  
  
 ![제품 목록](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>기본 템플릿  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>컨트롤  
 `Product list` 템플릿에서 다음 [페이지 컨트롤](api-management-page-controls.md)을 사용할 수 있습니다.  
  
-   [페이징 컨트롤](api-management-page-controls.md#paging-control)  
  
-   [검색 컨트롤](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>데이터 모델  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|Paging|[페이징](api-management-template-data-model-reference.md#Paging) 엔터티.|제품 컬렉션에 대한 페이징 정보입니다.|  
|Filtering|[필터링](api-management-template-data-model-reference.md#Filtering) 엔터티.|제품 목록 페이지에 대한 필터링 정보입니다.|  
|Products|[제품](api-management-template-data-model-reference.md#Product) 엔터티의 컬렉션입니다.|현재 사용자에게 표시되는 제품입니다.|  
  
### <a name="sample-template-data"></a>샘플 템플릿 데이터  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="Product"></a> 제품  
 **제품** 템플릿을 통해 개발자 포털에서 제품 페이지의 본문을 사용자 지정할 수 있습니다.  
  
 ![개발자 포털 제품 페이지](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>기본 템플릿  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>컨트롤  
 `Product list` 템플릿에서 다음 [페이지 컨트롤](api-management-page-controls.md)을 사용할 수 있습니다.  
  
-   [구독 단추](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>데이터 모델  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|Product|[Product](api-management-template-data-model-reference.md#Product)|지정된 제품.|  
|IsDeveloperSubscribed|부울|현재 사용자가 이 제품을 구독하는지 여부입니다.|  
|SubscriptionState|number|구독의 상태입니다. 가능한 상태는 다음과 같습니다.<br /><br /> -   `0 - suspended` – 구독이 차단되고 구독자는 제품의 API를 호출할 수 없습니다.<br />-   `1 - active` – 구독이 활성화되어 있습니다.<br />-   `2 - expired` - 구독이 만료 날짜에 도달되었고 비활성화되었습니다.<br />-   `3 - submitted` - 구독 요청이 개발자에 의해 발생했지만 아직 승인 또는 거부되지 않았습니다.<br />-   `4 - rejected` – 구독 요청이 관리자에 의해 거부되었습니다.<br />-   `5 - cancelled` - 구독이 개발자 또는 관리자에 의해 취소되었습니다.|  
|Limits|array|이 속성은 사용되지 않으며 사용할 수 없습니다.|  
|DelegatedSubscriptionEnabled|부울|[위임](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/)이 이 구독에 대해 활성화되었는지 여부입니다.|  
|DelegatedSubscriptionUrl|문자열|위임을 사용하는 경우 위임된 구독 URL입니다.|  
|IsAgreed|부울|제품에 약관이 있는 경우 현재 사용자가 약관에 동의했는지 여부입니다.|  
|Subscriptions|[구독 요약](api-management-template-data-model-reference.md#SubscriptionSummary) 엔터티의 컬렉션입니다.|제품에 대한 구독입니다.|  
|Apis|[API](api-management-template-data-model-reference.md#API) 엔터티의 컬렉션입니다.|이 제품의 API입니다.|  
|CannotAddBecauseSubscriptionNumberLimitReached|부울|현재 사용자가 구독 제한과 관련하여 이 제품을 구독할 수 있는지 여부입니다.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|부울|현재 사용자가 허용 또는 허용되지 않는 여러 구독과 관련하여 이 제품을 구독할 수 있는지 여부입니다.|  
  
### <a name="sample-template-data"></a>샘플 템플릿 데이터  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>다음 단계
템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 참조하세요.
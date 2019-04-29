---
title: Azure API Management의 애플리케이션 템플릿 | Microsoft Docs
description: Azure API Management의 개발자 포털에서 애플리케이션 페이지의 콘텐츠를 사용자 지정하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 732fdf3f9210a1484895e0b43e061b4bbc586b43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657939"
---
# <a name="application-templates-in-azure-api-management"></a>Azure API Management의 애플리케이션 템플릿
Azure API Management는 해당 콘텐츠를 구성하는 템플릿 집합을 사용하여 개발자 포털 페이지의 콘텐츠를 사용자 지정하는 기능을 제공합니다. 이러한 템플릿에서 [DotLiquid](http://dotliquidmarkup.org/) 구문 및 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) 및 제공된 지역화 [String 리소스](api-management-template-resources.md#strings), [Glyph 리소스](api-management-template-resources.md#glyphs) 및 [Page 컨트롤](api-management-page-controls.md)의 집합과 같은 선택한 편집기를 사용하여 필요에 따라 페이지 콘텐츠를 유연하게 구성할 수 있습니다.  
  
 이 섹션의 템플릿을 사용하여 개발자 포털의 애플리케이션 페이지의 콘텐츠를 사용자 지정할 수 있습니다.  
  
-   [애플리케이션 목록](#ProductList)  
  
-   [애플리케이션](#Application)  
  
> [!NOTE]
>  다음 문서에는 샘플 기본 템플릿이 포함되어 있지만 지속적인 향상으로 인해 변경될 수 있습니다. 원하는 개별 템플릿으로 이동하여 개발자 포털에서 라이브 기본 템플릿을 볼 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)을 참조하세요.  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> 애플리케이션 목록  
 **애플리케이션 목록** 템플릿을 사용하여 개발자 포털에서 애플리케이션 목록 페이지의 본문을 사용자 지정할 수 있습니다.  
  
 ![애플리케이션 목록 페이지 개발자 포털 템플릿](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "APIM 애플리케이션 목록 페이지 개발자 포털 템플릿")  
  
### <a name="default-template"></a>기본 템플릿  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>데이터 모델  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Paging`|[페이징](api-management-template-data-model-reference.md#Paging) 엔터티입니다.|애플리케이션 컬렉션에 대한 페이징 정보입니다.|  
|`Applications`|[애플리케이션](api-management-template-data-model-reference.md#Application) 엔터티의 컬렉션입니다.|현재 사용자에게 표시되는 애플리케이션입니다.|  
|`CategoryName`|문자열|애플리케이션의 범주입니다.|  
  
### <a name="sample-template-data"></a>샘플 템플릿 데이터  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="Application"></a> 애플리케이션  
 **애플리케이션** 템플릿을 사용하여 개발자 포털에서 애플리케이션 페이지의 본문을 사용자 지정할 수 있습니다.  
  
 ![애플리케이션 페이지 개발자 포털 템플릿](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "APIM 애플리케이션 페이지 개발자 포털 템플릿")  
  
### <a name="default-template"></a>기본 템플릿  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>컨트롤  
 `Application` 템플릿에서는 [컨트롤 페이지](api-management-page-controls.md) 사용을 허용하지 않습니다.  
  
### <a name="data-model"></a>데이터 모델  
 [애플리케이션](api-management-template-data-model-reference.md#Application) 엔터티입니다.  
  
### <a name="sample-template-data"></a>샘플 템플릿 데이터  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>다음 단계
템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 참조하세요.

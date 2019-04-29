---
title: 템플릿을 사용하여 Azure API Management 개발자 포털 사용자 지정 | Microsoft Docs
description: 템플릿을 사용하여 Azure API Management 개발자 포털을 사용자 지정하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 00d5e3df78e85d19a519786dad1a1b176ad7fa08
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837234"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>템플릿을 사용하여 Azure API Management 개발자 포털을 사용자 지정하는 방법

Azure API Management에는 개발자 포털을 사용자 지정하는 기본적인 방법이 세 가지 있습니다.

* [페이지 레이아웃 요소 및 정적 페이지의 콘텐츠 편집][modify-content-layout]
* [개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트][customize-styles]
* [포털에서 생성한 페이지에 사용되는 템플릿 수정][portal-templates](이 가이드에서 설명함)

템플릿은 시스템에서 생성된 개발자 포털 페이지(예: API 문서, 제품, 사용자 인증 등)의 콘텐츠를 사용자 지정하는 데 사용됩니다. [DotLiquid](http://dotliquidmarkup.org/) 구문 및 제공된 지역화 문자열 리소스, 아이콘 및 페이지 컨트롤을 사용하여 필요에 따라 페이지 콘텐츠를 유연하게 구성할 수 있습니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>개발자 포털 템플릿 개요

관리자로 로그인되어 있는 동안 **개발자 포털**에서 템플릿을 편집합니다. 여기로 이동하려면 우선 Azure Portal을 열고 API Management 인스턴스의 서비스 도구 모음에서 **개발자 포털**을 클릭하세요.

개발자 포털 템플릿에 액세스하려면 왼쪽의 사용자 지정 아이콘을 클릭하여 사용자 지정 메뉴를 표시한 다음 **템플릿**을 클릭합니다.

![개발자 포털 템플릿][api-management-customize-menu]

템플릿 목록에 개발자 포털의 다양한 페이지에 적용할 수 있는 여러 범주의 템플릿이 표시됩니다. 각 템플릿은 서로 다르지만 편집 및 변경 내용 게시 방법 단계는 동일합니다. 템플릿을 편집하려면 해당 템플릿 이름을 클릭합니다.

![개발자 포털 템플릿][api-management-templates-menu]

템플릿을 클릭하면 해당 템플릿을 통해 사용자 지정할 수 있는 개발자 포털 페이지로 이동합니다. 이 예제에서는 **제품 목록** 템플릿이 표시됩니다. **제품 목록** 템플릿은 빨간색 사각형으로 표시된 화면 영역을 제어합니다.

![제품 목록 템플릿][api-management-developer-portal-templates-overview]

**사용자 프로필** 템플릿과 같은 일부 템플릿은 동일한 페이지의 다양한 부분을 사용자 지정합니다.

![사용자 프로필 템플릿][api-management-user-profile-templates]

각 개발자 포털 템플릿의 편집기에는 페이지 맨 아래에 표시되는 두 개의 섹션이 있습니다. 왼쪽에는 템플릿의 편집 창이 표시되고 오른쪽에는 템플릿의 데이터 모델이 표시됩니다.

템플릿 편집 창에는 개발자 포털에서 해당 페이지의 모양과 동작을 제어하는 태그가 포함되어 있습니다. 템플릿의 태그는 [DotLiquid](http://dotliquidmarkup.org/) 구문을 사용합니다. [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)가 DotLiquid용 편집기로 많이 사용됩니다. 편집 중 템플릿에 대한 변경 사항은 실시간으로 브라우저에 표시되지만 템플릿을 [저장](#to-save-a-template) 및 [게시](#to-publish-a-template)할 때까지 고객에게는 표시되지 않습니다.

![템플릿 태그][api-management-template]

**템플릿 데이터** 창에서는 특정 템플릿에서 사용할 수 있는 엔터티의 데이터 모델에 대한 지침을 제공합니다. 개발자 포털에 현재 표시된 라이브 데이터를 표시하여 이 가이드를 제공합니다. **템플릿 데이터** 창의 오른쪽 위에 있는 사각형을 클릭하여 템플릿 창을 확장할 수 있습니다.

![템플릿 데이터 모델][api-management-template-data]

이전 예제에는 다음 예제와 같이 **템플릿 데이터** 창에 표시된 데이터에서 가져온 두 개의 제품이 개발자 포털에 표시되어 있습니다.

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
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
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

**제품 목록** 템플릿의 태그는 데이터를 처리하고 제품 컬렉션에 대해 반복하여 정보 및 각 개별 제품에 대한 링크를 표시함으로써 원하는 결과를 제공합니다. 태그의 `<search-control>` 및 `<page-control>` 요소를 참조하세요. 이러한 요소는 페이지에서 검색 및 페이징 컨트롤 표시를 제어합니다. `ProductsStrings|PageTitleProducts`은(는) 페이지에 대한 `h2` 머리글 텍스트를 포함하는 지역화된 문자열 참조입니다. 개발자 포털 템플릿에서 사용할 수 있는 문자열 리소스, 페이지 컨트롤 및 아이콘 목록은 [API Management 개발자 포털 템플릿 참조](api-management-developer-portal-templates-reference.md)를 참조하세요.

```html
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

## <a name="to-save-a-template"></a>템플릿을 저장하려면
템플릿을 저장하려면 템플릿 편집기에서 저장을 클릭합니다.

![템플릿 저장][api-management-save-template]

저장된 변경 내용은 게시할 때까지 개발자 포털에 적용되지 않습니다.

## <a name="to-publish-a-template"></a>템플릿을 게시하려면
저장된 템플릿은 개별적으로 또는 모두 함께 게시할 수 있습니다. 개별 템플릿을 게시하려면 템플릿 편집기에서 게시를 클릭합니다.

![템플릿 게시][api-management-publish-template]

**예** 를 클릭하여 확인하면 개발자 포털에 템플릿이 적용됩니다.

![게시 확인][api-management-publish-template-confirm]

현재 게시되지 않은 모든 템플릿 버전을 게시하려면 템플릿 목록에서 **게시** 를 클릭합니다. 게시되지 않은 템플릿은 템플릿 이름 뒤에 별표가 표시됩니다. 이 예제에는 **제품 목록** 및 **제품** 템플릿이 게시되어 있습니다.

![템플릿 게시][api-management-publish-templates]

확인하려면 **사용자 지정 항목 게시** 를 클릭합니다.

![게시 확인][api-management-publish-customizations]

새로 게시된 템플릿은 개발자 포털에서 즉시 적용됩니다.

## <a name="to-revert-a-template-to-the-previous-version"></a>템플릿을 이전 버전으로 되돌리려면
템플릿을 이전에 게시된 버전으로 되돌리려면 템플릿 편집기에서 되돌리기를 클릭합니다.

![템플릿 되돌리기][api-management-revert-template]

**예** 를 클릭하여 확인합니다.

![Confirm][api-management-revert-template-confirm]

되돌리기 작업이 완료되면 이전에 게시된 템플릿 버전이 개발자 포털에 적용됩니다.

## <a name="to-restore-a-template-to-the-default-version"></a>템플릿을 기본 버전으로 복원하려면
템플릿을 기본 버전으로 복원하는 작업은 두 단계로 이루어집니다. 먼저 템플릿을 복원한 다음 복원된 버전을 게시해야 합니다.

단일 템플릿을 기본 버전으로 복원하려면 템플릿 편집기에서 복원을 클릭합니다.

![템플릿 되돌리기][api-management-reset-template]

**예** 를 클릭하여 확인합니다.

![Confirm][api-management-reset-template-confirm]

모든 템플릿을 기본 버전으로 복원하려면 템플릿 목록에서 **기본 템플릿 복원** 을 클릭합니다.

![템플릿 복원][api-management-restore-templates]

그런 다음 [템플릿을 게시하려면](#to-publish-a-template)단계에 따라 복원된 템플릿을 개별적으로 또는 한 번에 게시해야 합니다.

## <a name="next-steps"></a>다음 단계
개발자 포털 템플릿, 문자열 리소스, 아이콘 및 페이지 컨트롤에 대한 참조 정보는 [API Management 개발자 포털 템플릿 참조](api-management-developer-portal-templates-reference.md)를 참조하세요.

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png

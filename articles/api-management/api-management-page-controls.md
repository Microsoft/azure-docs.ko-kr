---
title: Azure API Management 페이지 컨트롤 | Microsoft Docs
description: Azure API Management에서 개발자 포털 템플릿에 사용할 수 있는 페이지 컨트롤러에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656985"
---
# <a name="azure-api-management-page-controls"></a>Azure API Management 페이지 컨트롤
Azure API Management는 개발자 포털 템플릿에 사용할 수 있는 다음 컨트롤을 제공합니다.  
  
컨트롤을 사용하려면 개발자 포털 템플릿의 원하는 위치에 둡니다. [app-actions](#app-actions) 컨트롤과 같은 일부 컨트롤에는 매개 변수가 있습니다(다음 예 참조).  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 매개 변수의 값은 템플릿에 대한 데이터 모델의 일부로 전달됩니다. 대부분 경우 제대로 작동하도록 각 컨트롤에 대해 제공된 예제에 붙여넣을 수 있습니다. 매개 변수 값에 대한 자세한 내용은 컨트롤을 사용할 수 있는 각 템플릿에 대한 데이터 모델 섹션을 참조할 수 있습니다.  
  
 템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)을 참조하세요.  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>개발자 포털 템플릿 페이지 컨트롤  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> app-actions  
 `app-actions` 컨트롤은 개발자 포털의 사용자 프로필 페이지에서 애플리케이션과 상호 작용하기 위한 사용자 인터페이스를 제공합니다.  
  
 ![app&#45;actions control](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-actions control")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>매개 변수  
  
|매개 변수|설명|  
|---------------|-----------------|  
|appId|애플리케이션의 ID입니다.|  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `app-actions` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [애플리케이션](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>basic-signin  
 `basic-signin` 컨트롤은 개발자 포털의 로그인 페이지에서 사용자 로그인 정보를 수집하기 위한 컨트롤을 제공합니다.  
  
 ![basic&#45;signin 컨트롤](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-signin 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>매개 변수  
 없음.  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `basic-signin` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [로그인](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> paging-control  
 `paging-control`은 개발자 포털 페이지에서 항목 목록을 표시하는 페이징 기능을 제공합니다.  
  
 ![페이징 컨트롤](./media/api-management-page-controls/APIM-paging-control.png "APIM 페이징 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>매개 변수  
 없음.  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `paging-control` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [API 목록](api-management-api-templates.md#APIList)  
  
-   [문제 목록](api-management-issue-templates.md#IssueList)  
  
-   [제품 목록](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> 공급자  
 `providers` 컨트롤은 개발자 포털의 로그인 페이지에서 인증 공급자 선택을 위한 컨트롤을 제공합니다.  
  
 ![공급자 컨트롤](./media/api-management-page-controls/APIM-providers-control.png "APIM 공급자 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>매개 변수  
 없음.  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `providers` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [로그인](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 `search-control`은 개발자 포털 페이지에서 항목 목록을 표시하는 검색 기능을 제공합니다.  
  
 ![검색 컨트롤](./media/api-management-page-controls/APIM-search-control.png "APIM 검색 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>매개 변수  
 없음.  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `search-control` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [API 목록](api-management-api-templates.md#APIList)  
  
-   [제품 목록](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> sign-up  
 `sign-up` 컨트롤은 개발자 포털의 등록 페이지에서 사용자 프로필 정보를 수집하기 위한 컨트롤을 제공합니다.  
  
 ![등록&#45;컨트롤](./media/api-management-page-controls/APIM-sign-up-control.png "APIM 등록 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>매개 변수  
 없음.  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `sign-up` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [등록](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscribe-button  
 `subscribe-button`은 사용자의 제품 구독에 대한 컨트롤을 제공합니다.  
  
 ![subscribe&#45;button 컨트롤](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM subscribe-button 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>매개 변수  
 없음.  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `subscribe-button` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [Product](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> subscription-cancel  
 `subscription-cancel` 컨트롤은 개발자 포털의 사용자 프로필 페이지에서 제품 구독 취소를 위한 컨트롤을 제공합니다.  
  
 ![subscription&#45;cancel 컨트롤](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM subscription-cancel 컨트롤")  
  
### <a name="usage"></a>사용 현황  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>매개 변수  
  
|매개 변수|설명|  
|---------------|-----------------|  
|subscriptionId|취소할 구독의 ID입니다.|  
|cancelUrl|구독 취소 URL입니다.|  
  
### <a name="developer-portal-templates"></a>개발자 포털 템플릿  
 `subscription-cancel` 컨트롤은 다음과 같은 개발자 포털 템플릿에서 사용할 수 있습니다.  
  
-   [Product](api-management-product-templates.md#Product)

## <a name="next-steps"></a>다음 단계
템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 참조하세요.
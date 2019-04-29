---
title: Azure API Management 템플릿 데이터 모델 참조 | Microsoft Docs
description: Azure API Management에서 개발자 포털 템플릿용 데이터 모델에서 사용되는 일반적인 항목에 대한 엔터티 및 유형 표현에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 3c2384b536235554fed7c1cf1a08b7c665f513a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094525"
---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API Management 템플릿 데이터 모델 참조
이 항목에서는 Azure API Management에서 개발자 포털 템플릿용 데이터 모델에서 사용되는 일반적인 항목에 대한 엔터티 및 유형 표현을 설명합니다.  
  
 템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)을 참조하세요.  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

개발자 포털은 소비 계층에서 사용할 수 없습니다.

## <a name="reference"></a>참조

-   [API](#API)  
-   [API 요약](#APISummary)  
-   [애플리케이션](#Application)  
-   [첨부 파일](#Attachment)  
-   [코드 샘플](#Sample)  
-   [Comment](#Comment)  
-   [필터링](#Filtering)  
-   [머리글](#Header)  
-   [HTTP 요청](#HTTPRequest)  
-   [HTTP 응답](#HTTPResponse)  
-   [문제](#Issue)  
-   [작업](#Operation)  
-   [작업 메뉴](#Menu)  
-   [작업 메뉴 항목](#MenuItem)  
-   [페이징](#Paging)  
-   [매개 변수](#Parameter)  
-   [Product](#Product)  
-   [공급자](#Provider)  
-   [표현](#Representation)  
-   [구독](#Subscription)  
-   [구독 요약](#SubscriptionSummary)  
-   [사용자 계정 정보](#UserAccountInfo)  
-   [사용자 로그인](#UseSignIn)  
-   [사용자 등록](#UserSignUp)  
  
##  <a name="API"></a> API  
 `API` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`id`|문자열|리소스 식별자. 현재 API Management 서비스 인스턴스 내의 API를 고유하게 식별합니다. 값은 `{id}`가 API 식별자인 `apis/{id}` 형식의 유효한 상대 URL입니다. 이 속성은 읽기 전용입니다.|  
|`name`|문자열|API 이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`description`|문자열|API에 대한 설명입니다. 비어 있지 않아야 합니다. HTML 서식 지정 태그를 포함할 수 있습니다. 최대 길이는 1000자입니다.|  
|`serviceUrl`|문자열|이 API를 구현하는 백 엔드 서비스의 절대 URL입니다.|  
|`path`|문자열|API Management 서비스 인스턴스 내의 이 API 및 모든 해당 리소스 경로를 고유하게 식별하는 상대 URL입니다. 이 API에 대한 공용 URL을 형성하는 서비스 인스턴스를 만드는 동안 지정된 API 엔드포인트 기준 URL에 추가됩니다.|  
|`protocols`|숫자의 배열|이 API의 작업을 호출할 수 있는 프로토콜을 설명합니다. 허용되는 값은 `1 - http` 및 `2 - https` 또는 둘 다입니다.|  
|`authenticationSettings`|[권한 부여 서버 인증 설정](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|이 API에 포함된 인증 설정의 컬렉션입니다.|  
|`subscriptionKeyParameterNames`|object|구독 키가 포함된 쿼리 및/또는 헤더 매개 변수에 대한 사용자 지정 이름을 지정하는 데 사용할 수 있는 선택적 속성입니다. 이 속성이 있으면 다음 두 속성 중 하나 이상을 포함해야 합니다.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API 요약  
 `API summary` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`id`|문자열|리소스 식별자. 현재 API Management 서비스 인스턴스 내의 API를 고유하게 식별합니다. 값은 `{id}`가 API 식별자인 `apis/{id}` 형식의 유효한 상대 URL입니다. 이 속성은 읽기 전용입니다.|  
|`name`|문자열|API 이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`description`|문자열|API에 대한 설명입니다. 비어 있지 않아야 합니다. HTML 서식 지정 태그를 포함할 수 있습니다. 최대 길이는 1000자입니다.|  
  
##  <a name="Application"></a> 애플리케이션  
 `application` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|문자열|애플리케이션의 고유 식별자입니다.|  
|`Title`|문자열|애플리케이션의 제목입니다.|  
|`Description`|문자열|애플리케이션의 설명입니다.|  
|`Url`|URI|애플리케이션에 대한 URI입니다.|  
|`Version`|문자열|애플리케이션에 대한 버전 정보입니다.|  
|`Requirements`|문자열|애플리케이션에 대한 요구 사항의 설명입니다.|  
|`State`|number|애플리케이션의 현재 상태입니다.<br /><br /> - 0 - 등록됨<br /><br /> - 1 - 제출됨<br /><br /> - 2 - 게시됨<br /><br /> - 3 - 거부됨<br /><br /> - 4 - 게시 취소됨|  
|`RegistrationDate`|DateTime|애플리케이션이 등록된 날짜 및 시간입니다.|  
|`CategoryId`|number|애플리케이션(금융, 엔터테인먼트 등)의 범주입니다.|  
|`DeveloperId`|문자열|애플리케이션을 제출한 개발자의 고유 식별자입니다.|  
|`Attachments`|[첨부 파일](#Attachment) 엔터티의 컬렉션입니다.|스크린샷 또는 아이콘과 같은 애플리케이션에 대한 첨부 파일입니다.|  
|`Icon`|[첨부 파일](#Attachment)|애플리케이션에 대한 아이콘입니다.|  
  
##  <a name="Attachment"></a> 첨부 파일  
 `attachment` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`UniqueId`|문자열|첨부 파일의 고유한 식별자입니다.|  
|`Url`|문자열|리소스의 URL입니다.|  
|`Type`|문자열|첨부 파일의 형식입니다.|  
|`ContentType`|문자열|첨부 파일의 미디어 형식입니다.|  
  
##  <a name="Sample"></a> 코드 샘플  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`title`|문자열|작업의 이름입니다.|  
|`snippet`|문자열|이 속성은 사용되지 않으며 사용할 수 없습니다.|  
|`brush`|문자열|코드 샘플을 표시할 때 사용할 템플릿을 강조하는 코드 구문입니다. 허용되는 값은 `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby` 및 `csharp`입니다.|  
|`template`|문자열|이 코드 샘플 템플릿의 이름입니다.|  
|`body`|문자열|코드 조각의 코드 샘플 부분에 대한 자리 표시자입니다.|  
|`method`|문자열|작업의 HTTP 메서드입니다.|  
|`scheme`|문자열|작업 요청에 사용할 프로토콜입니다.|  
|`path`|문자열|작업의 경로입니다.|  
|`query`|문자열|정의된 매개 변수가 있는 쿼리 문자열 예제입니다.|  
|`host`|문자열|이 작업을 포함하는 API에 대한 API Management 서비스 게이트웨이의 URL입니다.|  
|`headers`|[헤더](#Header) 엔터티의 컬렉션입니다.|이 작업에 대한 헤더입니다.|  
|`parameters`|[매개 변수](#Parameter) 엔터티의 컬렉션입니다.|이 작업에 대해 정의된 매개 변수입니다.|  
  
##  <a name="Comment"></a> 주석  
 `API` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|number|주석의 ID입니다.|  
|`CommentText`|문자열|주석의 본문입니다. HTML을 포함할 수 있습니다.|  
|`DeveloperCompany`|문자열|개발자의 회사 이름입니다.|  
|`PostedOn`|DateTime|의견이 게시된 날짜 및 시간입니다.|  
  
##  <a name="Issue"></a> 문제  
 `issue` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|문자열|문제의 고유한 식별자입니다.|  
|`ApiID`|문자열|이 문제가 보고된 API에 대한 ID입니다.|  
|`Title`|문자열|문제의 제목입니다.|  
|`Description`|문자열|문제에 대한 설명입니다.|  
|`SubscriptionDeveloperName`|문자열|문제를 보고한 개발자의 이름입니다.|  
|`IssueState`|문자열|문제의 현재 상태입니다. 가능한 값은 Proposed, Opened, Closed입니다.|  
|`ReportedOn`|DateTime|문제가 보고된 날짜 및 시간입니다.|  
|`Comments`|[주석](#Comment) 엔터티의 컬렉션입니다.|이 문제에 대한 주석입니다.|  
|`Attachments`|[첨부 파일](api-management-template-data-model-reference.md#Attachment) 엔터티의 컬렉션입니다.|문제에 대한 첨부 파일입니다.|  
|`Services`|[API](#API) 엔터티의 컬렉션입니다.|문제를 정리하는 사용자가 구독한 API입니다.|  
  
##  <a name="Filtering"></a> 필터링  
 `filtering` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Pattern`|문자열|검색 용어가 없는 경우 현재 검색 용어 또는 `null`입니다.|  
|`Placeholder`|문자열|지정된 검색 용어가 없는 경우 검색 상자에 표시할 텍스트입니다.|  
  
##  <a name="Header"></a> 헤더  
 이 섹션에서는 `parameter` 표현을 설명합니다.  
  
|자산|Type|설명|  
|--------------|-----------------|----------|  
|`name`|문자열|매개 변수 이름입니다.|  
|`description`|문자열|매개 변수 설명입니다.|  
|`value`|문자열|헤더 값입니다.|  
|`typeName`|문자열|헤더 값의 데이터 형식입니다.|  
|`options`|문자열|옵션입니다.|  
|`required`|부울|필수 헤더인지 여부입니다.|  
|`readOnly`|부울|헤더가 읽기 전용인지 여부입니다.|  
  
##  <a name="HTTPRequest"></a> HTTP 요청  
 이 섹션에서는 `request` 표현을 설명합니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`description`|문자열|작업 요청 설명입니다.|  
|`headers`|[헤더](#Header) 엔터티의 배열입니다.|요청 헤더입니다.|  
|`parameters`|[매개 변수](#Parameter)의 배열|작업 요청 매개 변수의 컬렉션입니다.|  
|`representations`|[표현](#Representation)의 배열|작업 요청 표현의 컬렉션입니다.|  
  
##  <a name="HTTPResponse"></a> HTTP 응답  
 이 섹션에서는 `response` 표현을 설명합니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`statusCode`|양의 정수|작업 응답 상태 코드입니다.|  
|`description`|문자열|작업 응답 설명입니다.|  
|`representations`|[표현](#Representation)의 배열|작업 응답 표현의 컬렉션입니다.|  
  
##  <a name="Operation"></a> 작업  
 `operation` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`id`|문자열|리소스 식별자. 현재 API Management 서비스 인스턴스 내의 작업을 고유하게 식별합니다. 값은 `{aid}`가 API 식별자이고 `{id}`가 작업 식별자인 `apis/{aid}/operations/{id}` 형식의 유효한 상대 URL입니다. 이 속성은 읽기 전용입니다.|  
|`name`|문자열|작업의 이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`description`|문자열|작업에 대한 설명입니다. 비어 있지 않아야 합니다. HTML 서식 지정 태그를 포함할 수 있습니다. 최대 길이는 1000자입니다.|  
|`scheme`|문자열|이 API의 작업을 호출할 수 있는 프로토콜을 설명합니다. 허용되는 값은 `http`, `https` 또는 `http` 및 `https` 모두입니다.|  
|`uriTemplate`|문자열|이 작업에 대한 대상 리소스를 식별하는 상대 URL 템플릿입니다. 매개 변수를 포함할 수 있습니다. 예제: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|문자열|API를 호스팅하는 API Management 게이트웨이 URL입니다.|  
|`httpMethod`|문자열|작업 HTTP 메서드입니다.|  
|`request`|[HTTP 요청](#HTTPRequest)|요청 세부 정보를 포함하는 엔터티입니다.|  
|`responses`|[HTTP 응답](#HTTPResponse)의 배열|작업 [HTTP 응답](#HTTPResponse) 엔터티의 배열입니다.|  
  
##  <a name="Menu"></a> 작업 메뉴  
 `operation menu` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`ApiId`|문자열|현재 API의 ID입니다.|  
|`CurrentOperationId`|문자열|현재 작업의 ID입니다.|  
|`Action`|문자열|메뉴 형식입니다.|  
|`MenuItems`|[작업 메뉴 항목](#MenuItem) 엔터티의 컬렉션입니다.|현재 API에 대한 작업입니다.|  
  
##  <a name="MenuItem"></a> 작업 메뉴 항목  
 `operation menu item` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|문자열|작업의 ID입니다.|  
|`Title`|문자열|작업에 대한 설명입니다.|  
|`HttpMethod`|문자열|작업의 Http 메서드입니다.|  
  
##  <a name="Paging"></a> 페이징  
 `paging` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Page`|number|현재 페이지 번호입니다.|  
|`PageSize`|number|단일 페이지에 표시될 최대 결과입니다.|  
|`TotalItemCount`|number|표시할 항목의 수입니다.|  
|`ShowAll`|부울|단일 페이지에 모든 결과를 표시할지 여부입니다.|  
|`PageCount`|number|결과의 페이지 수입니다.|  
  
##  <a name="Parameter"></a> 매개 변수  
 이 섹션에서는 `parameter` 표현을 설명합니다.  
  
|자산|Type|설명|  
|--------------|-----------------|----------|  
|`name`|문자열|매개 변수 이름입니다.|  
|`description`|문자열|매개 변수 설명입니다.|  
|`value`|문자열|매개 변수 값입니다.|  
|`options`|문자열의 배열|쿼리 매개 변수 값에 대해 정의된 값입니다.|  
|`required`|부울|매개 변수가 필요한지 여부를 지정합니다.|  
|`kind`|number|이 매개 변수가 경로 매개 변수(1) 또는 쿼리 문자열 매개 변수(2)인지 여부입니다.|  
|`typeName`|문자열|매개 변수 유형입니다.|  
  
##  <a name="Product"></a> 제품  
 `product` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|문자열|리소스 식별자. 현재 API Management 서비스 인스턴스 내의 제품을 고유하게 식별합니다. 값은 `{pid}`가 제품 식별자인 `products/{pid}` 형식의 유효한 상대 URL입니다. 이 속성은 읽기 전용입니다.|  
|`Title`|문자열|제품의 이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`Description`|문자열|제품에 대한 설명입니다. 비어 있지 않아야 합니다. HTML 서식 지정 태그를 포함할 수 있습니다. 최대 길이는 1000자입니다.|  
|`Terms`|문자열|제품 사용 약관입니다. 제품을 구독하려는 개발자에게 표시되며 구독 프로세스를 완료하기 전에 이러한 약관에 동의해야 합니다.|  
|`ProductState`|number|제품 게시 여부를 지정합니다. 게시된 제품은 개발자 포털에서 개발자가 검색할 수 있습니다. 게시되지 않은 제품은 관리자에게만 보입니다.<br /><br /> 제품 상태에 대해 허용되는 값은 다음과 같습니다.<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|부울|사용자가 동시에 이 제품에 여러 구독을 소유할 수 있는지 여부를 지정합니다.|  
|`MultipleSubscriptionsCount`|number|사용자가 동시에 가질 수 있는 이 제품의 최대 구독 수입니다.|  
  
##  <a name="Provider"></a> 공급자  
 `provider` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Properties`|문자열 사전|이 인증 공급자에 대한 속성입니다.|  
|`AuthenticationType`|문자열|공급자 유형입니다. (Azure Active Directory, Facebook 로그인, Google 계정, Microsoft 계정, Twitter).|  
|`Caption`|문자열|공급자의 표시 이름입니다.|  
  
##  <a name="Representation"></a> 표현  
 이 섹션에서는 `representation`을 설명합니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`contentType`|문자열|이 표현에 대한 등록된 또는 사용자 지정 콘텐츠 형식을 지정합니다(예: `application/xml`).|  
|`sample`|문자열|표현의 예제입니다.|  
  
##  <a name="Subscription"></a> 구독  
 `subscription` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|문자열|리소스 식별자. 현재 API Management 서비스 인스턴스 내의 구독을 고유하게 식별합니다. 값은 `{sid}`가 구독 식별자인 `subscriptions/{sid}` 형식의 유효한 상대 URL입니다. 이 속성은 읽기 전용입니다.|  
|`ProductId`|문자열|구독된 제품의 제품 리소스 식별자입니다. 값은 `{pid}`가 제품 식별자인 `products/{pid}` 형식의 유효한 상대 URL입니다.|  
|`ProductTitle`|문자열|제품의 이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`ProductDescription`|문자열|제품에 대한 설명입니다. 비어 있지 않아야 합니다. HTML 서식 지정 태그를 포함할 수 있습니다. 최대 길이는 1000자입니다.|  
|`ProductDetailsUrl`|문자열|제품 세부 정보에 대한 상대 URL입니다.|  
|`state`|문자열|구독의 상태입니다. 가능한 상태는 다음과 같습니다.<br /><br /> - `0 - suspended` – 구독이 차단되고 구독자는 제품의 API를 호출할 수 없습니다.<br /><br /> - `1 - active` – 구독이 활성화되어 있습니다.<br /><br /> - `2 - expired` - 구독이 만료 날짜에 도달되었고 비활성화되었습니다.<br /><br /> - `3 - submitted` - 구독 요청이 개발자에 의해 발생했지만 아직 승인 또는 거부되지 않았습니다.<br /><br /> - `4 - rejected` – 구독 요청이 관리자에 의해 거부되었습니다.<br /><br /> - `5 - cancelled` - 구독이 개발자 또는 관리자에 의해 취소되었습니다.|  
|`DisplayName`|문자열|구독의 표시 이름입니다.|  
|`CreatedDate`|datetime|ISO 8601 형식(`2014-06-24T16:25:00Z`)의 구독이 만들어진 날짜입니다.|  
|`CanBeCancelled`|부울|현재 사용자가 구독을 취소할 수 있는지 여부입니다.|  
|`IsAwaitingApproval`|부울|구독이 승인 대기 중인지 여부입니다.|  
|`StartDate`|datetime|ISO 8601 형식(`2014-06-24T16:25:00Z`)의 구독에 대한 시작 날짜입니다.|  
|`ExpirationDate`|datetime|ISO 8601 형식(`2014-06-24T16:25:00Z`)의 구독에 대한 만료 날짜입니다.|  
|`NotificationDate`|datetime|ISO 8601 형식(`2014-06-24T16:25:00Z`)의 구독에 대한 알림 날짜입니다.|  
|`primaryKey`|문자열|기본 구독 키입니다. 최대 길이는 256자입니다.|  
|`secondaryKey`|문자열|보조 구독 키입니다. 최대 길이는 256자입니다.|  
|`CanBeRenewed`|부울|현재 사용자가 구독을 갱신할 수 있는지 여부입니다.|  
|`HasExpired`|부울|구독이 만료되었는지 여부입니다.|  
|`IsRejected`|부울|구독 요청이 거부되었는지 여부입니다.|  
|`CancelUrl`|문자열|구독을 취소하는 상대 Url입니다.|  
|`RenewUrl`|문자열|구독을 갱신하는 상대 Url입니다.|  
  
##  <a name="SubscriptionSummary"></a> 구독 요약  
 `subscription summary` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Id`|문자열|리소스 식별자. 현재 API Management 서비스 인스턴스 내의 구독을 고유하게 식별합니다. 값은 `{sid}`가 구독 식별자인 `subscriptions/{sid}` 형식의 유효한 상대 URL입니다. 이 속성은 읽기 전용입니다.|  
|`DisplayName`|문자열|구독의 표시 이름|  
  
##  <a name="UserAccountInfo"></a> 사용자 계정 정보  
 `user account info` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`FirstName`|문자열|이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`LastName`|문자열|성입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`Email`|문자열|메일 주소입니다. 비어 있지 않아야 하며 서비스 인스턴스 내에서 고유해야 합니다. 최대 길이는 254자입니다.|  
|`Password`|문자열|사용자 계정 암호입니다.|  
|`NameIdentifier`|문자열|사용자 전자 메일과 동일한 계정 식별자입니다.|  
|`ProviderName`|문자열|인증 공급자 이름입니다.|  
|`IsBasicAccount`|부울|이 계정이 전자 메일 및 암호를 사용하여 등록된 경우 true이며 공급자를 사용하여 등록된 경우 false입니다.|  
  
##  <a name="UseSignIn"></a> 사용자 로그인  
 `user sign in` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`Email`|문자열|메일 주소입니다. 비어 있지 않아야 하며 서비스 인스턴스 내에서 고유해야 합니다. 최대 길이는 254자입니다.|  
|`Password`|문자열|사용자 계정 암호입니다.|  
|`ReturnUrl`|문자열|사용자가 로그인을 클릭하는 페이지의 URL입니다.|  
|`RememberMe`|부울|현재 사용자의 정보를 저장할지 여부입니다.|  
|`RegistrationEnabled`|부울|등록이 활성화되어 있는지 여부입니다.|  
|`DelegationEnabled`|부울|위임된 로그인이 활성화되어 있는지 여부입니다.|  
|`DelegationUrl`|문자열|활성화된 경우 url의 위임된 로그인입니다.|  
|`SsoSignUpUrl`|문자열|있는 경우 사용자에 대한 Single Sign-On URL입니다.|  
|`AuxServiceUrl`|문자열|현재 사용자가 관리자인 경우 Azure Portal의 서비스 인스턴스에 대한 링크입니다.|  
|`Providers`|[공급자](#Provider) 엔터티의 컬렉션|이 사용자에 대한 인증 공급자입니다.|  
|`UserRegistrationTerms`|문자열|로그인하기 전에 사용자가 동의해야 하는 약관입니다.|  
|`UserRegistrationTermsEnabled`|부울|약관이 활성화되었는지 여부입니다.|  
  
##  <a name="UserSignUp"></a> 사용자 등록  
 `user sign up` 엔터티에는 다음과 같은 속성이 있습니다.  
  
|자산|Type|설명|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|부울|[등록](api-management-page-controls.md#sign-up)등록 제어에서 사용되는 값입니다.|  
|`Password`|문자열|사용자 계정 암호입니다.|  
|`PasswordVerdictLevel`|number|[등록](api-management-page-controls.md#sign-up)등록 제어에서 사용되는 값입니다.|  
|`UserRegistrationTerms`|문자열|로그인하기 전에 사용자가 동의해야 하는 약관입니다.|  
|`UserRegistrationTermsOptions`|number|[등록](api-management-page-controls.md#sign-up)등록 제어에서 사용되는 값입니다.|  
|`ConsentAccepted`|부울|[등록](api-management-page-controls.md#sign-up)등록 제어에서 사용되는 값입니다.|  
|`Email`|문자열|메일 주소입니다. 비어 있지 않아야 하며 서비스 인스턴스 내에서 고유해야 합니다. 최대 길이는 254자입니다.|  
|`FirstName`|문자열|이름입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`LastName`|문자열|성입니다. 비어 있지 않아야 합니다. 최대 길이는 100자입니다.|  
|`UserData`|문자열|[등록](api-management-page-controls.md#sign-up) 제어에서 사용되는 값입니다.|  
|`NameIdentifier`|문자열|[등록](api-management-page-controls.md#sign-up)등록 제어에서 사용되는 값입니다.|  
|`ProviderName`|문자열|인증 공급자 이름입니다.|

## <a name="next-steps"></a>다음 단계
템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 참조하세요.

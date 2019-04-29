---
title: Azure API Management 개발자 포털 템플릿 | Microsoft Docs
description: Azure API Management에서 템플릿 집합을 사용하여 개발자 포털 페이지의 콘텐츠를 사용자 지정하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 0f339984a9db1a337f97e6db24571004cc34f679
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657648"
---
# <a name="developer-portal-templates"></a>개발자 포털 템플릿

Azure API Management는 해당 콘텐츠를 구성하는 템플릿 집합을 사용하여 개발자 포털 페이지의 콘텐츠를 사용자 지정하는 기능을 제공합니다. 이러한 템플릿에서 [DotLiquid](http://dotliquidmarkup.org/) 구문 및 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers) 및 제공된 지역화 [String 리소스](api-management-template-resources.md#strings), [Glyph 리소스](api-management-template-resources.md#glyphs) 및 [Page 컨트롤](api-management-page-controls.md)의 집합과 같은 선택한 편집기를 사용하여 필요에 따라 페이지 콘텐츠를 유연하게 구성할 수 있습니다.  
  
템플릿 작업에 대한 자세한 내용은 [템플릿을 사용하여 API Management 개발자 포털을 사용자 지정하는 방법](api-management-developer-portal-templates.md)을 참조하세요.  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="DeveloperPortalTemplates"></a> 개발자 포털 템플릿  
  
-   [API](api-management-api-templates.md)  
    -   [API 목록](api-management-api-templates.md#APIList)  
    -   [작업](api-management-api-templates.md#Product)  
    -   [코드 샘플](api-management-api-templates.md#CodeSamples)  
        -   [Curl](api-management-api-templates.md#Curl)  
        -   [C#](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [JavaScript](api-management-api-templates.md#JavaScript)  
        -   [Objective C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [제품](api-management-product-templates.md)  
    -   [제품 목록](api-management-product-templates.md#ProductList)  
    -   [Product](api-management-product-templates.md#Product)  
-   [애플리케이션](api-management-application-templates.md)  
    -   [애플리케이션 목록](api-management-application-templates.md#ProductList)  
    -   [애플리케이션](api-management-application-templates.md#Application)  
-   [문제](api-management-issue-templates.md)  
    -   [문제 목록](api-management-issue-templates.md#IssueList)  
-   [사용자 프로필](api-management-user-profile-templates.md)  
    -   [프로필](api-management-user-profile-templates.md#Profile)  
    -   [구독](api-management-user-profile-templates.md#Subscriptions)  
    -   [애플리케이션](api-management-user-profile-templates.md#Applications)  
    -   [계정 정보 업데이트](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [페이지](api-management-page-templates.md)  
    -   [로그인](api-management-page-templates.md#SignIn)  
    -   [등록](api-management-page-templates.md#SignUp)  
    -   [페이지를 찾을 수 없음](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>다음 단계  

+ [템플릿 참조](api-management-developer-portal-templates-reference.md)  
+ [데이터 모델 참조](api-management-template-data-model-reference.md)  
+ [페이지 컨트롤](api-management-page-controls.md)  
+ [템플릿 리소스](api-management-template-resources.md)
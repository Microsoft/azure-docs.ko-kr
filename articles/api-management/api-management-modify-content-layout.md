---
title: Azure API Management에서 개발자 포털의 페이지 콘텐츠 수정 | Microsoft Docs
description: Azure API Management에서 개발자 포털의 페이지 콘텐츠를 편집하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: fd9902a83a813daffc52e089e6e8bfc4137c5b8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656650"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Azure API Management에서 개발자 포털의 페이지 콘텐츠 및 레이아웃 수정
Azure API Management에는 개발자 포털을 사용자 지정하는 기본적인 방법이 세 가지 있습니다.

* [페이지 레이아웃 요소 및 정적 페이지의 콘텐츠 편집][modify-content-layout](이 가이드에 설명되어 있음)
* [개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트][customize-styles]
* [포털에서 생성된 페이지에 사용된 템플릿 수정][portal-templates](예: API 문서, 제품, 사용자 인증 등)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="page-structure"> </a>개발자 포털 페이지의 구조

개발자 포털은 콘텐츠 관리 시스템을 기반으로 합니다. 모든 페이지의 레이아웃은 위젯이라는 작은 페이지 요소의 집합을 기반으로 작성됩니다.

![개발자 포털 페이지 구조][api-management-customization-widget-structure]

모든 위젯은 편집이 가능합니다.
* 각 페이지 고유의 핵심 콘텐츠는 "콘텐츠" 위젯에 있습니다. 페이지를 편집한다는 것은 이 위젯의 콘텐츠를 편집한다는 의미입니다.
* 모든 페이지 레이아웃 요소는 나머지 위젯에 포함됩니다. 이러한 위젯의 변경 내용은 모든 페이지에 적용됩니다. 이것을 "레이아웃 위젯"이라고 합니다.

일상적인 페이지 편집에서 콘텐츠 위젯만 편집하는 경우가 자주 있으며, 이로 인해 각 개별 페이지의 콘텐츠가 달라집니다.

## <a name="modify-layout-widget"> </a>레이아웃 위젯의 콘텐츠 수정

개발자 포털은 Azure Portal에서 액세스할 수 있습니다.

1. API Management 인스턴스의 도구 모음에서 **개발자 포털**을 클릭합니다.
2. 위젯의 내용을 편집하려면 왼쪽에 있는 **개발자** 포털 메뉴에서 두 개의 페인트 브러시로 이루어진 아이콘을 클릭합니다.
3. 헤더의 내용을 수정하려면 왼쪽의 목록에서 **헤더**로 스크롤합니다.

    위젯은 필드 내에서 편집할 수 있습니다.
4. 변경 내용을 게시할 준비가 완료되면 페이지 맨 아래에서 **게시**를 클릭합니다.

이제 개발자 포털 내의 모든 페이지에서 새 머리글을 볼 수 있습니다.

## <a name="next-steps"> </a>다음 단계
* [개발자 포털 전반의 페이지 요소에 사용된 스타일 업데이트][customize-styles]
* [포털에서 생성된 페이지에 사용된 템플릿 수정][portal-templates](예: API 문서, 제품, 사용자 인증 등)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png

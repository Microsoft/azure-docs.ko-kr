---
title: 관리 되는 개발자 포털 액세스 및 사용자 지정-Azure API Management | Microsoft Docs
description: API Management에서 관리 되는 버전의 개발자 포털을 사용 하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/22/2019
ms.author: apimpm
ms.openlocfilehash: 8629d07830622770c3b30dacdd1fabc8417d7f52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454427"
---
# <a name="access-and-customize-developer-portal"></a>개발자 포털 액세스 및 사용자 지정

개발자 포털은 Api 설명서를 사용 하 여 자동으로 생성 되 고 완전히 사용자 지정할 수 있는 웹 사이트입니다. API 소비자는 api를 검색 하 고, api를 사용 하 고, 액세스를 요청 하는 방법을 배울 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 개발자 포털의 관리 되는 버전에 액세스
> * 관리 인터페이스 탐색
> * 콘텐츠 사용자 지정
> * 변경 내용 게시
> * 게시 된 포털 보기

개발자 포털에 대한 자세한 내용은 [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)에서 찾을 수 있습니다.

![API Management 개발자 포털-관리자 모드](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>선행 조건

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- Azure API Management 인스턴스를 가져오고 게시합니다. 자세한 내용은 [가져오기 및 게시](import-and-publish.md) 를 참조 하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>관리자 권한으로 포털에 액세스

아래 단계에 따라 포털의 관리 되는 버전에 액세스 합니다.

1. Azure Portal에서 API Management 서비스 인스턴스로 이동 합니다.
1. 위쪽 탐색 모음에서 **개발자 포털** 단추를 클릭 합니다. 포털의 관리 버전을 포함 하는 새 브라우저 탭이 열립니다.

## <a name="understand-the-portals-administrative-interface"></a>포털의 관리 인터페이스 이해

### <a name="default-content"></a>기본 콘텐츠 

처음으로 포털에 액세스 하는 경우 기본 콘텐츠가 백그라운드에서 자동으로 프로 비전 됩니다. 기본 콘텐츠는 포털의 기능을 소개 하 고 포털을 개인 설정 하는 데 필요한 사용자 지정의 양을 최소화 하도록 설계 되었습니다. [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)에서 포털 콘텐츠에 포함 된 내용에 대해 자세히 알아볼 수 있습니다.

### <a name="visual-editor"></a>시각적 개체 편집기

시각적 편집기를 사용 하 여 포털의 콘텐츠를 사용자 지정할 수 있습니다. 왼쪽의 메뉴 섹션을 사용 하 여 페이지, 미디어, 레이아웃, 메뉴, 스타일 또는 웹 사이트 설정을 만들거나 수정할 수 있습니다. 맨 아래에 있는 메뉴 항목을 사용 하면 뷰포트 (예: 모바일 또는 데스크톱) 간을 전환 하거나, 인증 된 사용자 또는 익명 사용자에 게 표시 되는 포털 요소를 확인 하거나, 작업을 저장 하거나 실행 취소할 수 있습니다.

더하기 기호가 있는 파란색 아이콘을 클릭 하 여 페이지에 행을 추가할 수 있습니다. 텍스트, 이미지 또는 Api 목록과 같은 위젯은 더하기 기호가 있는 회색 아이콘을 눌러 추가할 수 있습니다. 끌어서 놓기 상호 작용을 사용 하 여 페이지에서 항목을 다시 정렬할 수 있습니다. 

### <a name="layouts-and-pages"></a>레이아웃 및 페이지

![페이지 및 레이아웃](media/api-management-howto-developer-portal-customize/pages-layouts.png)

레이아웃은 페이지가 표시 되는 방식을 정의 합니다. 예를 들어 기본 콘텐츠에서 두 가지 레이아웃이 있습니다. 하나는 홈 페이지에, 다른 하나는 나머지 모든 페이지에 적용 됩니다.

레이아웃은 URL 템플릿과 페이지 URL을 일치 시켜 페이지에 적용 됩니다. 예를 들어 `/wiki/*` URL 템플릿이 있는 레이아웃은 URL의 `/wiki/` 세그먼트가 있는 모든 페이지에 적용 됩니다. `/wiki/getting-started`, `/wiki/styles`등

위의 이미지에서 레이아웃에 속하는 콘텐츠는 파란색으로 표시 되 고 페이지는 빨강으로 표시 됩니다. 메뉴 섹션은 각각 표시 됩니다.

### <a name="styling-guide"></a>스타일 지정 가이드

![스타일 지정 가이드](media/api-management-howto-developer-portal-customize/styling-guide.png)

스타일 지정 가이드는 디자이너를 염두에 두면 생성 된 패널입니다. 포털의 모든 시각적 요소를 감독 하 고 스타일을 지정할 수 있습니다. 스타일 지정은 계층적입니다. 많은 요소가 다른 요소의 속성을 상속 합니다. 예를 들어 단추 요소는 텍스트와 배경에 색을 사용 합니다. 단추의 색을 변경 하려면 원래 색 변형을 변경 해야 합니다.

변형을 편집 하려면 해당 변형을 클릭 하 고 맨 위에 표시 되는 연필 아이콘을 선택 합니다. 팝업 창에서 변경을 수행한 후 닫습니다.

### <a name="save-button"></a>저장 단추

![저장 단추](media/api-management-howto-developer-portal-customize/save-button.png)

포털에서 변경 내용을 적용할 때마다 아래쪽의 메뉴에서 **저장** 단추를 눌러 수동으로 저장 해야 합니다. 변경 내용을 저장 하면 수정 된 콘텐츠가 API Management 서비스에 자동으로 업로드 됩니다.

## <a name="customize-the-portals-content"></a>포털 콘텐츠 사용자 지정

방문자가 포털을 사용할 수 있도록 설정 하기 전에 자동으로 생성 된 콘텐츠를 개인 설정 해야 합니다. 권장 되는 변경 내용에는 홈 페이지의 레이아웃, 스타일 및 콘텐츠가 포함 됩니다.

> [!NOTE]
> 통합 고려 사항으로 인해 `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`등의 다른 URL에서 다음 페이지를 제거 하거나 이동할 수 없습니다. `/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>홈 페이지

기본 **홈** 페이지는 더미 콘텐츠로 채워집니다. 콘텐츠를 사용 하 여 전체 섹션을 제거 하거나 구조를 유지 하 고 요소를 하나씩 조정할 수 있습니다. 생성 된 텍스트와 이미지를 사용자 고유로 바꾸고 링크가 원하는 위치를 가리키는지 확인 합니다.

### <a name="layouts"></a>레이아웃

탐색 모음에서 자동으로 생성 된 로고를 사용자 고유의 이미지로 바꿉니다.

### <a name="styling"></a>설정과

스타일을 조정할 필요가 없지만 특정 요소를 조정 하는 것이 좋습니다. 예를 들어 브랜드 색과 일치 하도록 기본 색을 변경 합니다.

### <a name="customization-example"></a>사용자 지정 예제

아래 비디오에서는 포털의 콘텐츠를 편집 하 고, 웹 사이트의 모양을 사용자 지정 하 고, 변경 내용을 게시 하는 방법을 보여 줍니다.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish"> </a>포털 게시

방문객이 포털 및 최신 변경을 방문자에 게 제공 하려면 게시 해야 합니다.

1. **저장** 아이콘을 클릭 하 여 변경 내용을 저장 했는지 확인 합니다.
1. 메뉴의 **작업** 섹션에서 **웹 사이트 게시** 를 클릭 합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.  
    ![게시 포털](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> 사용자 지정 도메인을 할당 하 고, id 공급자를 업데이트 하 고, 위임을 설정 하 고, 로그인 및 제품 용어를 지정 하는 등의 서비스 구성 변경을 API Management 후 포털을 다시 게시 해야 합니다.

## <a name="visit-the-published-portal"></a>게시 된 포털 방문

포털을 게시 한 후에는 관리 패널과 동일한 URL (예: `https://contoso-api.developer.azure-api.net`에 액세스할 수 있습니다. 별도의 브라우저 세션 (incognito/개인 검색 모드)에서 외부 방문자로 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

개발자 포털에 대해 자세히 알아보세요.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)

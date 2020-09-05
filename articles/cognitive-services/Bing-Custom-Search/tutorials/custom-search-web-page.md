---
title: '자습서: 사용자 지정 검색 웹 페이지 만들기 - Bing Custom Search'
titleSuffix: Azure Cognitive Services
description: 이 자습서를 통해 사용자 지정 Bing 검색 인스턴스를 구성하고 웹 페이지에 통합하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: da1ada27b9420643f6f0b15f0986c245ba585acd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85921938"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>자습서: Custom Search 웹 페이지 빌드

Bing Custom Search를 사용하면 관심 있는 토픽에 대한 맞춤형 검색 경험을 만들 수 있습니다. 예를 들어 검색 환경을 제공하는 무술 웹 사이트를 소유하고 있는 경우 Bing에서 검색하는 도메인, 하위 사이트 및 웹 페이지를 지정할 수 있습니다. 사용자는 관련 없는 콘텐츠를 포함할 수 있는 일반적인 검색 결과를 페이징할 필요 없이 관심 있는 콘텐츠에 맞게 조정된 검색 결과를 보게 됩니다. 

이 자습서에서는 사용자 지정 검색 인스턴스를 구성하고 새로운 웹 페이지에 통합하는 방법을 보여 줍니다.

설명하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> - 사용자 지정 검색 인스턴스 만들기
> - 활성 항목 추가
> - 차단된 항목 추가
> - 고정된 항목 추가
> - 웹 페이지에 사용자 지정 검색 통합

## <a name="prerequisites"></a>필수 구성 요소

- 자습서를 따르려면 Bing Custom Search API에 대한 구독 키가 필요합니다.  키를 가져오려면 Azure Portal에서 [Bing Custom Search 리소스를 만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch).
- Visual Studio 2017 이상이 아직 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다.

## <a name="create-a-custom-search-instance"></a>사용자 지정 검색 인스턴스 만들기

Bing Custom Search 인스턴스를 만들려면

1. 인터넷 브라우저를 엽니다.  
  
2. 사용자 지정 검색 [포털](https://customsearch.ai)로 이동합니다.  
  
3. MSA(Microsoft 계정)를 사용하여 포털에 로그인합니다. MSA가 없는 경우 **Microsoft 계정 만들기**를 클릭합니다. 포털을 처음 사용하는 경우 포털에서 데이터에 액세스할 수 있는 권한을 요청합니다. **예**를 클릭합니다.  
  
4. 로그인한 후 **새 사용자 지정 검색**을 클릭합니다. **새 사용자 지정 검색 인스턴스 만들기** 창에서 의미 있는 이름을 입력하고 검색에서 반환하는 콘텐츠 형식을 설명합니다. 언제든지 이름을 변경할 수 있습니다.  
  
   ![새 사용자 지정 검색 인스턴스 만들기 상자 스크린샷](../media/newCustomSrch.png)  
  
5. 확인을 클릭하고 URL 및 URL의 하위 페이지를 포함할지 여부를 지정합니다.  
  
   ![URL 정의 페이지 스크린샷](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>활성 항목 추가

특정 웹 사이트 또는 URL의 결과를 포함하려면 **활성** 탭에 추가합니다.

1. **구성** 페이지에서 **활성** 탭을 클릭하고 검색에 포함할 웹 사이트 하나 이상의 URL을 입력합니다.

    ![정의 편집기 활성 탭 스크린샷](../media/customSrchEditor.png)

2. 인스턴스가 결과를 반환하는지 확인하려면 오른쪽 미리 보기 창에 쿼리를 입력합니다. Bing은 인덱싱된 공용 웹 사이트에 대해서만 결과를 반환합니다.

## <a name="add-blocked-entries"></a>차단된 항목 추가

특정 웹 사이트 또는 URL의 결과를 제외하려면 **차단됨** 탭에 추가합니다.

1. **구성** 페이지에서 **차단됨** 탭을 클릭하고 검색에서 제외할 웹 사이트 하나 이상의 URL을 입력합니다.

    ![정의 편집기 차단됨 탭 스크린샷](../media/blockedCustomSrch.png)


2. 인스턴스가 차단된 웹 사이트의 결과를 반환하지 않는지 확인하려면 오른쪽 미리 보기 창에 쿼리를 입력합니다. 

## <a name="add-pinned-entries"></a>고정된 항목 추가

특정 웹 페이지를 검색 결과의 맨 위에 고정하려면 웹 페이지와 쿼리 용어를 **고정됨** 탭에 추가합니다. **고정됨** 탭에는 특정 쿼리의 맨 위 결과로 표시되는 웹 페이지를 지정하는 웹 페이지 및 쿼리 용어 쌍 목록이 포함됩니다. 사용자의 쿼리 문자열이 고정의 일치 조건을 기준으로 하는 고정의 쿼리 문자열과 일치하는 경우에만 웹 페이지가 고정됩니다. 인덱싱된 웹 페이지만 검색에 표시됩니다. 자세한 내용은 [사용자 지정 보기 정의](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results)를 참조하세요.

1. **구성** 페이지에서 **고정됨** 탭을 클릭하고 맨 위 결과로 반환되도록 할 웹 페이지 및 웹 페이지의 쿼리 용어를 입력합니다.  
  
2. 기본적으로 사용자의 쿼리 문자열이 고정의 쿼리 문자열과 일치해야 Bing에서 웹 페이지를 맨 위 결과로 반환합니다. 일치 조건을 변경하려면 연필 모양 아이콘을 클릭하여 고정을 편집하고, **쿼리 일치 조건** 열에서 정확하게 일치를 클릭한 후에 애플리케이션에 적합한 일치 조건을 선택합니다.  
  
    ![정의 편집기 고정됨 탭 스크린샷](../media/pinnedCustomSrch.png)
  
3. 인스턴스가 지정한 웹 페이지를 맨 위 결과로 반환하는지 확인하려면 오른쪽 미리 보기 창에서 고정한 쿼리 용어를 입력합니다.

## <a name="configure-hosted-ui"></a>호스트된 UI 구성

Custom Search는 사용자 지정 검색 인스턴스의 JSON 응답을 렌더링하는 호스트된 UI를 제공합니다. UI 환경을 정의하려면

1. **호스트된 UI** 탭을 클릭합니다.  
  
2. 레이아웃을 선택합니다.  
  
   ![호스트된 UI 레이아웃 선택 단계 스크린샷](./media/custom-search-hosted-ui-select-layout.png)  
  
3. 색 테마를 선택합니다.  
  
   ![호스트된 UI 색 테마 선택 스크린샷](./media/custom-search-hosted-ui-select-color-theme.png)  

   웹앱과 더 효율적으로 통합되도록 색 테마를 미세 조정해야 하는 경우 **사용자 지정 테마**를 클릭합니다. 레이아웃 테마에는 일부 색 구성만 적용됩니다. 색을 변경하려면 해당 텍스트 상자에 색의 RGB HEX 값(예: #366eb8)을 입력합니다. 또는 색 단추를 클릭하고 적합한 음영을 클릭합니다. 색을 선택할 때는 항상 접근성을 고려해야 합니다.
  
   ![호스트된 UI 색 테마 사용자 지정 스크린샷](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. 추가 구성 옵션을 지정합니다.  
  
   ![호스트된 UI 추가 구성 단계 스크린샷](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   고급 구성을 가져오려면 **고급 구성 표시**를 클릭합니다. 그러면 *링크 대상* 등의 구성이 웹 검색 옵션에 추가되고, *필터 사용* 등의 구성이 이미지 및 비디오 옵션에 추가되며, *검색 상자 텍스트 개체 틀* 등의 구성이 기타 옵션에 추가됩니다.

   ![호스트된 UI 고급 구성 단계 스크린샷](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. 드롭다운 목록에서 구독 키를 선택합니다. 구독 키를 수동으로 입력할 수도 있습니다.
  
   ![호스트된 UI 추가 구성 단계 스크린샷](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>호스트된 UI 사용

호스트된 UI를 사용하는 방법에는 두 가지가 있습니다.  

- 옵션 1: 제공된 JavaScript 코드 조각을 애플리케이션에 통합합니다.
- 옵션 2: 제공된 HTML 엔드포인트를 사용합니다.

이 자습서의 나머지 부분에서는 **옵션 1: JavaScript 코드 조각**을 설명합니다.  

## <a name="set-up-your-visual-studio-solution"></a>Visual Studio 솔루션 설정

1. 컴퓨터에서 **Visual Studio**를 엽니다.  
  
2. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다.  
  
3. **새 프로젝트** 창에서 **Visual C#/Web/ASP.NET Core 웹 애플리케이션**을 선택하고, 프로젝트 이름을 지정한 다음, **확인**을 클릭합니다.  
  
   ![새 프로젝트 창의 스크린샷](./media/custom-search-new-project.png)  
  
4. **새 ASP.NET Core 웹 애플리케이션** 창에서 **웹 애플리케이션**을 선택하고 **확인**을 클릭합니다.  
  
   ![새 프로젝트 창의 스크린샷](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>index.cshtml 편집

1. **솔루션 탐색기**에서 **페이지**를 확장하고 **index.cshtml**을 두 번 클릭하여 파일을 엽니다.  
  
   ![페이지가 확장되고 index.cshtml이 선택된 솔루션 탐색기 스크린샷](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. index.cshtml에서 줄 7부터 아래의 모든 내용을 삭제합니다.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. 줄 바꿈 요소와 컨테이너 역할을 하는 div를 추가합니다.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. **호스트된 UI** 페이지에서 아래의 **UI 사용** 섹션으로 스크롤합니다. *엔드포인트*를 클릭하여 JavaScript 코드 조각에 액세스합니다. **프로덕션**과 **호스트된 UI** 탭을 차례로 클릭하여 코드 조각으로 이동할 수도 있습니다.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. 추가한 컨테이너에 스크립트 요소를 붙여넣습니다.  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. **솔루션 탐색기**에서 **wwwroot**를 마우스 오른쪽 단추로 클릭하고 **브라우저에서 보기**를 클릭합니다.  
  
   ![wwwroot 바로 가기 메뉴에서 브라우저에서 보기를 선택하는 솔루션 탐색기 스크린샷](./media/custom-search-webapp-view-in-browser.png)  

새 사용자 지정 검색 웹 페이지는 다음과 유사해야 합니다.

![사용자 지정 검색 웹 페이지 스크린샷](./media/custom-search-webapp-browse-index.png)

검색을 수행하면 이러한 결과가 렌더링됩니다.

![사용자 지정 검색 결과 스크린샷](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Custom Search 엔드포인트 호출(C#)](../call-endpoint-csharp.md)

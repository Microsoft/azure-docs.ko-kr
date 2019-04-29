---
title: Bing 검색-Azure Logic Apps에 연결
description: Bing Search REST API 및 Azure Logic Apps 소식
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952607"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Bing Search 및 Azure Logic Apps 소식

이 아티클에서는 Bing Search 커넥터를 사용하여 논리 앱 내에서 Bing Search를 통해 뉴스, 비디오 및 다른 항목을 찾을 수 있습니다. 이런 방식으로 검색 결과를 처리하기 위해 작업 및 워크플로를 자동화하고 해당 항목을 다른 작업에 사용할 수 있는 논리 앱을 만들 수 있습니다. 

예를 들어 검색 조건에 따라 뉴스 항목을 찾고, Twitter에서 해당 항목을 Twitter 피드의 트윗으로 게시할 수 있습니다.

Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.
커넥터 관련 기술 정보는 <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">Bing Search 커넥터 참조</a>를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Cognitive Services 계정](../cognitive-services/cognitive-services-apis-create-account.md)

* [Bing Search API 키](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)는 논리 앱에서 Bing Search API에 대한 액세스 권한을 제공합니다.

* Event Hubs에 액세스하려는 논리 앱입니다. Bing Search 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Bing Search 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

1. Azure Portal 또는 Visual Studio에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 이 예에서는 Azure Portal을 사용합니다.

2. 검색 상자에 필터로 "Bing Search"를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다.

   이 예제에서는이 트리거를 사용합니다. **Bing 검색-새 뉴스 기사**

   ![Bing Search 트리거 찾기](./media/connectors-create-api-bing-search/add-trigger.png)

3. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Bing Search 연결을 만듭니다](#create-connection).
또는 연결이 이미 존재하는 경우 트리거에 대한 필요한 정보를 제공합니다.

   이 예제에서는 Bing Search에서 일치하는 뉴스 기사를 반환하는 기준을 제공합니다.

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | Search Query | 예 | <*search-words*> | 사용하려는 검색 키워드를 입력합니다. |
   | 시장 | 예 | <*locale*> | 검색 로캘입니다. 기본값은 "en-US"이지만 다른 값을 선택할 수 있습니다. |
   | 안전 검색 | 예 | <*search-level*> | 성인물을 제외하기 위한 필터 수준입니다. 기본값은 "보통"이지만 다른 수준을 선택할 수 있습니다. |
   | 카운트 | 아닙니다. | <*results-count*> | 지정된 결과 수만 반환합니다. 기본값은 20이지만 다른 값을 지정할 수 있습니다. 반환된 결과의 실제 수는 지정된 수 미만일 수 있습니다. |
   | Offset | 아닙니다. | <*skip-value*> | 결과를 반환하기 전에 건너뛸 결과 수입니다. |
   |||||

   예를 들면 다음과 같습니다.

   ![트리거 설정](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. 트리거에서 결과를 확인하려는 간격 및 빈도를 선택합니다.

5. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

6. 이제 트리거 결과와 함께 수행하려는 작업에 대한 논리 앱에 하나 이상의 작업을 계속해서 추가합니다.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Bing Search 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 이 예제에서는 논리 앱은 지정된 조건과 일치하는 뉴스 기사를 반환하는 Bing Search 트리거를 시작합니다.

1. Azure Portal 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 사용합니다.

2. 트리거 또는 작업에서 **새 단계** > **작업 추가**를 선택합니다.

   이 예제에서는이 트리거를 사용합니다.

   **Bing 검색-새 뉴스 기사**

   ![작업 추가](./media/connectors-create-api-bing-search/add-action.png)

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 
   표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에 필터로 "Bing Search"를 입력합니다.
작업 목록에서 원하는 작업을 선택합니다.

   이 예제에서는이 작업을 사용합니다.

   **Bing 검색-쿼리에서 목록 뉴스**

   ![Bing Search 작업 찾기](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. 연결 세부 정보를 묻는 메시지가 표시되면 [이제 Bing Search 연결을 만듭니다](#create-connection). 또는 연결이 이미 존재하는 경우 작업에 대한 필요한 정보를 제공합니다.

   이 예제에서는 트리거 결과의 하위 집합을 반환하는 기준을 제공합니다.

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | Search Query | 예 | <*search-expression*> | 트리거 결과를 쿼리하는 식을 입력합니다. 동적 콘텐츠 목록의 필드에서 선택하거나 식 작성기를 사용하여 식을 만들 수 있습니다. |
   | 시장 | 예 | <*locale*> | 검색 로캘입니다. 기본값은 "en-US"이지만 다른 값을 선택할 수 있습니다. |
   | 안전 검색 | 예 | <*search-level*> | 성인물을 제외하기 위한 필터 수준입니다. 기본값은 "보통"이지만 다른 수준을 선택할 수 있습니다. |
   | 카운트 | 아닙니다. | <*results-count*> | 지정된 결과 수만 반환합니다. 기본값은 20이지만 다른 값을 지정할 수 있습니다. 반환된 결과의 실제 수는 지정된 수 미만일 수 있습니다. |
   | Offset | 아닙니다. | <*skip-value*> | 결과를 반환하기 전에 건너뛸 결과 수입니다. |
   |||||

   예를 들어 범주 이름이 "tech"라는 단어를 포함하는 결과를 찾는다고 가정합니다.

   1. **검색 쿼리** 상자를 클릭하여 동적 콘텐츠 목록을 표시합니다. 
   이 목록에서 **식**을 선택하여 식 작성기를 표시합니다. 

      ![Bing Search 트리거](./media/connectors-create-api-bing-search/bing-search-action.png)

      이제 식을 만들기 시작할 수 있습니다.

   2. 함수 목록에서 **contains()** 함수를 선택하면 식 상자에 표시됩니다. **동적 콘텐츠**를 클릭하여 필드 목록이 다시 표시되지만 커서가 괄호 안에 있는지 확인합니다.

      ![함수 선택](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. 필드 목록에서 매개 변수로 변환하는 **범주**를 선택합니다. 
   첫 번째 매개 변수 및 쉼표 뒤에 쉼표를 추가하고 `'tech'`라는 단어를 추가합니다. 

      ![필드 선택](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. 작업을 완료하면 **확인**을 선택합니다.

      이제 식은 다음 형식으로 **검색 쿼리** 상자에 표시됩니다.

      ![완료된 식](./media/connectors-create-api-bing-search/resolved-expression.png)

      코드 보기에서 이 식은 다음 형식으로 표시됩니다.

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Bing Search에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 연결 정보에 대한 메시지가 표시되면 다음과 같은 세부 정보를 입력합니다.

   | 자산 | 필수 | Value | 설명 |
   |----------|----------|-------|-------------|
   | 연결 이름 | 예 | <*connection-name*> | 연결에 만들 이름 |
   | API 버전 | 예 | <*API-version*> | 기본적으로 Bing Search API 버전은 현재 버전으로 설정됩니다. 필요에 따라 이전 버전을 선택할 수 있습니다. |
   | API 키 | 예 | <*API-key*> | 이전에 가져온 Bing Search API 키입니다. 키가 없는 경우 [이제 API 키](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api)를 가져옵니다. |  
   |||||  

   예를 들면 다음과 같습니다.

   ![연결 만들기](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. 작업을 완료하면 **만들기**를 선택합니다.

## <a name="connector-reference"></a>커넥터 참조

트리거, 작업 및 커넥터의 OpenAPI에 설명 된 대로 제한 등의 기술 세부 정보에 대 한 (이전의 Swagger) 파일, 참조를 [커넥터의 참조 페이지](/connectors/bingsearch/)합니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

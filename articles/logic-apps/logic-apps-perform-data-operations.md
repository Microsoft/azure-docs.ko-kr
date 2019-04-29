---
title: 데이터 작업 수행 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 데이터 출력 및 형식을 변환, 관리 및 조작합니다.
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 93c24f88fcd6a002493933ef71c5c80bd2ff8c10
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120182"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Azure Logic Apps에서 데이터 작업 수행

이 문서에서는 다음 태스크 등에 대한 작업을 추가하여 논리 앱에서 데이터를 사용하는 방법을 보여 줍니다.

* 배열에서 테이블을 만듭니다.
* 조건에 따라 다른 배열에서 배열을 만듭니다.
* JSON(JavaScript Object Notation) 개체 속성에서 사용자에게 친숙한 토큰을 만들어 워크플로에서 이러한 속성을 쉽게 사용할 수 있습니다.

여기서 원하는 작업을 찾을 수 없으면 Logic Apps에서 제공하는 다양한 [데이터 조작 함수](../logic-apps/workflow-definition-language-functions-reference.md)를 찾아보세요. 

다음 표에는 사용할 수 있는 작업이 요약되어 원본 데이터 형식에 따라 구성되어 있지만, 각 설명은 사전순으로 표시됩니다.

**배열 작업** 

배열의 데이터를 사용할 수 있는 작업은 다음과 같습니다.

| 액션(Action) | 설명 | 
|--------|-------------| 
| [**CSV 테이블 만들기**](#create-csv-table-action) | 배열에서 CSV(쉼표로 구분된 값) 테이블을 만듭니다. | 
| [**HTML 테이블 만들기**](#create-html-table-action) | 배열에서 HTML 테이블을 만듭니다. | 
| [**배열 필터링**](#filter-array-action) | 지정된 필터 또는 조건에 따라 배열에서 배열 하위 집합을 만듭니다. | 
| [**조인**](#join-action) | 배열의 모든 항목에서 문자열을 만들고, 각 항목을 지정된 문자로 구분합니다. | 
| [**선택**](#select-action) | 다른 배열의 모든 항목에 대해 지정된 속성에서 배열을 만듭니다. | 
||| 

**JSON 작업**

JSON(JavaScript Object Notation) 형식으로 데이터를 사용할 수 있는 작업은 다음과 같습니다.

| 액션(Action) | 설명 | 
|--------|-------------| 
| [**Compose**](#compose-action) | 다양한 데이터 형식이 있을 수 있는 여러 입력에서 메시지 또는 문자열을 만듭니다. 그런 다음, 동일한 입력을 반복적으로 입력하는 대신, 이 문자열을 단일 입력으로 사용할 수 있습니다. 예를 들어 다양한 입력에서 단일 JSON 메시지를 만들 수 있습니다. | 
| [**JSON 구문 분석**](#parse-json-action) | JSON 콘텐츠의 속성에 대해 사용자에게 친숙한 데이터 토큰을 만들어 논리 앱에서 해당 속성을 더 쉽게 사용할 수 있습니다. | 
||| 

더 복잡한 JSON 변환을 만들려면 [Liquid 템플릿을 사용하여 고급 JSON 변환 수행](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서의 예제를 수행하려면 다음 항목이 필요합니다.

* Azure 구독. 아직 Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* 데이터 작업을 위한 작업이 필요한 논리 앱 

  논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* 논리 앱의 첫 번째 단계인 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts) 

  데이터 작업은 작업으로만 사용할 수 있으므로, 이러한 작업을 사용하려면 먼저 트리거로 논리 앱을 시작하고 원하는 출력을 만드는 데 필요한 다른 작업을 포함합니다.

<a name="compose-action"></a>

## <a name="compose-action"></a>작성 작업

여러 입력에서 JSON 개체와 같은 단일 출력을 생성하려면 **데이터 작업 - 작성** 작업을 사용할 수 있습니다. 입력에는 정수, 부울, 배열, JSON 개체 및 Azure Logic Apps에서 지원하는 다른 원시 형식(예: 이진 및 XML)과 같은 다양한 형식이 있습니다. 그런 다음, **작성** 작업 이후에 수행하는 작업에서 출력을 사용할 수 있습니다. **작성** 작업을 사용하면 논리 앱의 워크플로를 작성하는 동안 동일한 입력을 반복적으로 입력하지 않도록 할 수도 있습니다. 

예를 들어 사람의 이름과 성을 저장하는 문자열 변수 및 사람의 연령을 저장하는 정수 변수와 같은 여러 변수에서 JSON 메시지를 생성할 수 있습니다. 여기서 **작성** 작업에 허용되는 입력은 다음과 같습니다.

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

그리고 만드는 출력은 다음과 같습니다.

`{"age":35,"fullName":"Owens,Sophie"}`

이 예제를 시도하려면 Logic Apps 디자이너를 사용하여 다음 단계를 수행합니다. 또는 코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **Compose** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 기본 워크플로 정의: [데이터 작업 코드 예제-구성](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 하나의 **되풀이** 트리거 및 여러 **변수 초기화** 작업과 함께 사용합니다. 
   이러한 작업은 두 개의 문자열 변수와 하나의 정수 변수를 만들기 위해 설정됩니다. 나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

2. 출력을 만들려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-compose-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "작성"을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **작성**

   !["작성" 작업 선택](./media/logic-apps-perform-data-operations/select-compose-action.png)

4. **입력** 상자에서 출력을 만들기 위해 원하는 입력을 제공합니다. 

   이 예제에서 **입력** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   ![작성에 대한 입력 선택](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   완료된 **작성** 작업 예제는 다음과 같습니다. 

   ![완료된 "작성" 작업](./media/logic-apps-perform-data-operations/finished-compose-action.png)

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [작성 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)을 참조하세요. 

### <a name="test-your-logic-app"></a>논리 앱 테스트

**작성** 작업에서 예상된 결과를 만드는지 확인하려면 **작성** 작업의 출력이 포함된 알림을 자신에게 보냅니다.

1. 논리 앱에서 **작성** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **작성** 작업 아래에서 **출력**을 선택합니다. 

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문과 제목에 **출력** 필드가 포함됩니다.

   !["이메일 보내기" 작업의 "출력" 필드](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["작성" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>CSV 테이블 만들기 작업

배열에 JSON(JavaScript Object Notation) 개체의 속성과 값이 있는 CSV(쉼표로 구분된 값) 테이블을 만들려면 **데이터 작업 - CSV 테이블 만들기** 작업을 사용합니다. 그런 다음, **CSV 테이블 만들기** 작업 이후에 수행하는 작업에서 결과 테이블을 사용할 수 있습니다. 

코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **CSV 테이블 만들기** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 기본 워크플로 정의: [데이터 작업 코드 예제-CSV 테이블 만들기](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 각각 하나의 **되풀이** 트리거 및 **변수 초기화** 작업과 함께 사용합니다. 
   이 작업은 초기 값이 JSON 형식의 일부 속성과 값이 있는 배열인 변수를 만들기 위해 설정됩니다. 
   나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. CSV 테이블을 만들려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "csv 테이블 만들기"를 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **CSV 테이블 만들기**

   !["CSV 테이블 만들기" 작업 선택](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

4. **원본** 상자에서 테이블을 만들기 위해 원하는 배열 또는 식을 제공합니다. 

   이 예제에서 **원본** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   ![CSV 테이블 만들기에 대한 배열 출력 선택](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   완료된 **CSV 테이블 만들기** 작업 예제는 다음과 같습니다. 

   ![완료된 "CSV 테이블 만들기" 작업](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

   기본적으로 이 작업은 배열 항목에 따라 열을 자동으로 만듭니다. 
   열 헤더와 값을 수동으로 만들려면 **고급 옵션 표시**를 선택합니다. 
   사용자 지정 값만 제공하려면 **열**을 **사용자 지정**으로 변경합니다. 
   사용자 지정 열 헤더도 제공하려면 **헤더 포함**을 **예**로 변경합니다. 

   > [!TIP]
   > JSON 개체의 속성에 대해 사용자에게 친숙한 토큰을 만들어 해당 속성을 입력으로 사용할 수 있도록 **CSV 테이블 만들기** 작업을 호출하기 전에 [JSON 구문 분석](#parse-json-action)을 사용합니다.

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [테이블 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)을 참조하세요.

### <a name="test-your-logic-app"></a>논리 앱 테스트

**CSV 테이블 만들기** 작업에서 예상된 결과를 만드는지 확인하려면 **CSV 테이블 만들기** 작업의 출력이 포함된 알림을 자신에게 보냅니다.

1. 논리 앱에서 **CSV 테이블 만들기** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **CSV 테이블 만들기** 작업 아래에서 **출력**을 선택합니다. 

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문에 **출력** 필드가 포함됩니다.

   !["이메일 보내기" 작업의 "출력" 필드](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["CSV 만들기 테이블" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML 테이블 만들기 작업

배열에 JSON(JavaScript Object Notation) 개체의 속성과 값이 있는 HTML 테이블을 만들려면 **데이터 작업 - HTML 테이블 만들기** 작업을 사용합니다. 그런 다음, **HTML 테이블 만들기** 작업 이후에 수행하는 작업에서 결과 테이블을 사용할 수 있습니다.

코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **HTML 테이블 만들기** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 기본 워크플로 정의: [데이터 작업 코드 예제-HTML 테이블 만들기](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 각각 하나의 **되풀이** 트리거 및 **변수 초기화** 작업과 함께 사용합니다. 
   이 작업은 초기 값이 JSON 형식의 일부 속성과 값이 있는 배열인 변수를 만들기 위해 설정됩니다. 
   나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

2. HTML 테이블을 만들려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "html 테이블 만들기"를 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **HTML 테이블 만들기**

   !["HTML 테이블 만들기" 작업 선택](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

4. **원본** 상자에서 테이블을 만들기 위해 원하는 배열 또는 식을 제공합니다. 

   이 예제에서 **원본** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   ![HTML 테이블 만들기에 대한 배열 출력 선택](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   완료된 **HTML 테이블 만들기** 작업 예제는 다음과 같습니다. 

   ![완료된 "HTML 테이블 만들기" 작업](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

   기본적으로 이 작업은 배열 항목에 따라 열을 자동으로 만듭니다. 
   열 헤더와 값을 수동으로 만들려면 **고급 옵션 표시**를 선택합니다. 
   사용자 지정 값만 제공하려면 **열**을 **사용자 지정**으로 변경합니다. 
   사용자 지정 열 헤더도 제공하려면 **헤더 포함**을 **예**로 변경합니다. 

   > [!TIP]
   > JSON 개체의 속성에 대해 사용자에게 친숙한 토큰을 만들어 해당 속성을 입력으로 사용할 수 있도록 **HTML 테이블 만들기** 작업을 호출하기 전에 [JSON 구문 분석](#parse-json-action)을 사용합니다.

5. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [테이블 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)을 참조하세요.

### <a name="test-your-logic-app"></a>논리 앱 테스트

**HTML 테이블 만들기** 작업에서 예상된 결과를 만드는지 확인하려면 **HTML 테이블 만들기** 작업의 출력이 포함된 알림을 자신에게 보냅니다.

1. 논리 앱에서 **HTML 테이블 만들기** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **HTML 테이블 만들기** 작업 아래에서 **출력**을 선택합니다. 

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문에 **출력** 필드가 포함됩니다.

   !["이메일 보내기" 작업의 "출력" 필드](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)
   
   > [!NOTE]
   > 이메일 작업에 HTML 테이블 출력이 포함되는 경우 이메일 작업의 고급 옵션에서 **HTML임** 속성을 **예**로 설정해야 합니다. 이렇게 하면 이메일 작업에서 HTML 테이블의 형식을 올바르게 지정합니다.

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["HTML 테이블 만들기" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>배열 필터링 작업

기존 배열에서 특정 조건을 충족하는 항목이 있는 더 작은 배열을 만들려면 **데이터 작업 - 배열 필터링** 작업을 사용합니다. 그런 다음, **배열 필터링** 작업 이후에 수행하는 작업에서 필터링된 배열을 사용할 수 있습니다. 

> [!NOTE]
> 조건에서 사용하는 모든 필터 텍스트는 대/소문자를 구분합니다. 또한 이 작업은 배열 항목의 형식 또는 구성 요소를 변경할 수 없습니다. 
> 
> **배열 필터링** 작업에서 배열 출력을 사용하는 작업의 경우 해당 작업에서 배열을 입력으로 받아들여야 하거나 출력 배열을 호환되는 다른 형식으로 변환해야 할 수 있습니다. 

코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **배열 필터링** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 기본 워크플로 정의: [데이터 작업 코드 예제-필터 배열](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 각각 하나의 **되풀이** 트리거 및 **변수 초기화** 작업과 함께 사용합니다. 
   이 작업은 초기 값이 몇 가지 샘플 정수가 있는 배열인 변수를 만들기 위해 설정됩니다. 나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   > [!NOTE]
   > 이 예제에서는 간단한 정수 배열을 사용하지만 이 작업은 개체의 속성과 값에 따라 필터링할 수 있는 JSON 개체 배열에 특히 유용합니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

2. 필터링된 배열을 만들려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "배열 필터링"을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **배열 필터링**

   !["배열 필터링" 작업 선택](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

4. **원본** 상자에서 필터링하려는 배열 또는 식을 제공합니다. 

   이 예제에서 **원본** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   ![필터링된 배열 만들기에 대한 배열 출력 선택](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

5. 조건에 대해 비교할 배열 항목을 지정하고, 비교 연산자를 선택하고, 비교 값을 지정합니다.

   이 예제에서는 배열의 각 항목에 액세스하는 데 **item()** 함수를 사용하는 한편, **배열 필터링** 작업에서 값이 1보다 큰 배열 항목을 검색합니다.
   
   ![완료된 "배열 필터링" 작업](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

6. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [쿼리 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)을 참조하세요.

### <a name="test-your-logic-app"></a>논리 앱 테스트

**배열 필터링** 작업에서 예상된 결과를 만드는지 확인하려면 **배열 필터링** 작업의 출력이 포함된 알림을 자신에게 보냅니다.

1. 논리 앱에서 **배열 필터링** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **식**을 선택합니다. **배열 필터링** 작업에서 배열 출력을 가져오려면 **배열 필터링** 작업 이름이 포함된 다음 식을 입력합니다.

   ```
   @actionBody('Filter_array')
   ```

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문에 **actionBody('Filter_array')** 식의 출력이 포함됩니다.

   !["이메일 보내기" 작업의 작업 출력](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["배열 필터링" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>조인 작업

배열의 모든 항목이 있고 특정 구분 기호 문자로 해당 항목이 구분된 문자열을 만들려면 **데이터 작업 - 조인** 작업을 사용합니다. 그런 다음, **조인** 작업 이후에 수행하는 작업에서 문자열을 사용할 수 있습니다.

코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **조인** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 워크플로 정의 기본: [데이터 작업 코드 예제-조인](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 각각 하나의 **되풀이** 트리거 및 **변수 초기화** 작업과 함께 사용합니다. 
   이 작업은 초기 값이 몇 가지 샘플 정수가 있는 배열인 변수를 만들기 위해 설정됩니다. 
   나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

2. 배열에서 문자열을 만들려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-join-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "조인"을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **Join**

   !["데이터 작업 - 조인" 작업 선택](./media/logic-apps-perform-data-operations/select-join-action.png)

4. **원본** 상자에서 조인하려는 항목이 있는 배열을 문자열로 제공합니다. 

   이 예제에서 **원본** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.  

   ![문자열 만들기에 대한 배열 출력 선택](./media/logic-apps-perform-data-operations/configure-join-action.png)

5. **Join with** 상자에서 각 배열 항목을 구분하는 데 사용하려는 문자를 입력합니다. 

   이 예제에서는 구분 기호로 콜론(:)을 사용합니다.

   ![구분 문자 제공](./media/logic-apps-perform-data-operations/finished-join-action.png)

6. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [조인 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)을 참조하세요.

### <a name="test-your-logic-app"></a>논리 앱 테스트

**조인** 작업에서 예상된 결과를 만드는지 확인하려면 **조인** 작업의 출력이 포함된 알림을 자신에게 보냅니다. 

1. 논리 앱에서 **조인** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **조인** 작업 아래에서 **출력**을 선택합니다. 

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문에 **출력** 필드가 포함됩니다.

   !["이메일 보내기" 작업의 "출력" 필드](./media/logic-apps-perform-data-operations/send-email-join-action.png)

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["Join" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/join-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>JSON 구문 분석 작업

JSON(JavaScript Object Notation) 콘텐츠의 속성을 참조하거나 액세스하려면 **데이터 작업 - JSON 구문 분석** 작업을 사용하여 사용자에게 친숙한 필드 또는 토큰을 만들 수 있습니다.
이렇게 하면 논리 앱에 대한 입력을 지정할 때 동적 콘텐츠 목록에서 해당 속성을 선택할 수 있습니다. 이 작업의 경우 JSON 콘텐츠 또는 페이로드 샘플에서 JSON 스키마를 제공하거나 생성할 수 있습니다.

코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **JSON 구문 분석** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 기본 워크플로 정의 : [데이터 작업 코드 예-JSON 구문 분석](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 각각 하나의 **되풀이** 트리거 및 **변수 초기화** 작업과 함께 사용합니다. 
   이 작업은 초기 값이 속성과 값이 있는 JSON 개체인 변수를 만들기 위해 설정됩니다. 
   나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

2. JSON 콘텐츠를 구문 분석하려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "json 구문 분석"을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **JSON 구문 분석**

   !["JSON 구문 분석" 작업 선택](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

4. **콘텐츠** 상자에서 구문 분석하려는 JSON 콘텐츠를 제공합니다. 

   이 예제에서 **콘텐츠** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   ![JSON 구문 분석 작업에 대한 JSON 개체 선택](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

5. 구문 분석하려는 JSON 콘텐츠를 설명하는 JSON 스키마를 입력합니다. 

   이 예제에서 JSON 스키마는 다음과 같습니다.

   ![구문 분석하려는 JSON 개체에 대한 JSON 스키마를 제공합니다.](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   스키마가 없는 경우 구문 분석 중인 JSON 콘텐츠 또는 *페이로드*에서 해당 스키마를 생성할 수 있습니다. 
   
   1. **JSON 구문 분석** 작업에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

   2. **샘플 JSON 페이로드를 입력하거나 붙여넣기** 아래에서 JSON 콘텐츠를 제공한 다음, **완료**를 선택합니다.

      ![스키마 생성에 대한 JSON 콘텐츠 입력](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

6. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [JSON 구문 분석](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조하세요.

### <a name="test-your-logic-app"></a>논리 앱 테스트

**JSON 구문 분석** 작업에서 예상된 결과를 만드는지 확인하려면 **JSON 구문 분석** 작업의 출력이 포함된 알림을 자신에게 보냅니다.

1. 논리 앱에서 **JSON 구문 분석** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **JSON 구문 분석** 작업 아래에서 구문 분석된 JSON 콘텐츠로부터 속성을 선택할 수 있습니다.

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문에 **FirstName**, **LastName** 및 **Email** 필드가 포함됩니다.

   !["이메일 보내기" 작업의 JSON 속성](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   완료된 이메일 작업은 다음과 같습니다.

   ![완료된 이메일 작업](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["Join" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>작업 선택

기존 배열의 값에서 작성된 JSON 개체가 있는 배열을 만들려면 **데이터 작업 - 선택** 작업을 사용합니다. 예를 들어 각 JSON 개체에 필요한 속성과 원본 배열의 값을 이러한 속성에 매핑하는 방법을 지정하여 정수 배열의 각 값에 대한 JSON 개체를 만들 수 있습니다. 그리고 이러한 JSON 개체의 구성 요소는 변경할 수 있지만, 출력 배열에는 항상 원본 배열과 동일한 수의 항목이 있습니다.

> [!NOTE]
> **선택** 작업에서 배열 출력을 사용하는 작업의 경우 해당 작업에서 배열을 입력으로 받아들여야 하거나 출력 배열을 호환되는 다른 형식으로 변환해야 할 수 있습니다. 

코드 보기 편집기에서 작업을 원할 경우 예제를 복사할 수 있습니다 **선택** 하 고 **변수 초기화** 고유한 논리 앱에이 문서에서 작업 정의 워크플로 정의 기본: [데이터 작업 코드 예제-선택](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a> 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 

   이 예제에서는 Azure Portal과 논리 앱을 각각 하나의 **되풀이** 트리거 및 **변수 초기화** 작업과 함께 사용합니다. 
   이 작업은 초기 값이 몇 가지 샘플 정수가 있는 배열인 변수를 만들기 위해 설정됩니다. 
   나중에 논리 앱을 테스트할 때 트리거가 실행될 때까지 기다리지 않고 수동으로 앱을 실행할 수 있습니다.

   ![논리 앱 샘플 시작](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

2. 배열을 만들려는 논리 앱에서 다음 단계 중 하나를 수행합니다. 

   * 마지막 단계 아래에 작업을 추가하려면 **새 단계** > **작업 추가**를 선택합니다.

     ![작업 추가](./media/logic-apps-perform-data-operations/add-select-action.png)

   * 단계 사이에 작업을 추가하려면 더하기 기호(+)가 나타날 때까지 연결 화살표 위로 마우스를 이동합니다. 
   더하기 기호를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에서 필터로 "선택"을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **Select**

   !["선택" 작업 선택](./media/logic-apps-perform-data-operations/select-select-action.png)

4. **원본** 상자에서 원하는 원본 배열을 지정합니다.

   이 예제에서 **원본** 상자 내부를 클릭하면 이전에 만든 변수를 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   ![선택 작업에 대한 원본 배열 선택](./media/logic-apps-perform-data-operations/configure-select-action.png)

5. **맵** 상자의 왼쪽 열에서 원본 배열의 각 값을 할당하려는 속성 이름을 제공합니다. 오른쪽 열에서 속성을 할당하려는 값을 나타내는 식을 지정합니다.

   이 예제에서는 각 배열 항목에 액세스하는 식에서 **item()** 함수를 사용하여 정수 배열의 각 값을 할당하기 위한 속성 이름으로 "Product_ID"를 지정합니다. 

   ![만들려는 배열에 대한 JSON 개체 속성 및 값을 지정합니다.](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   완료된 작업은 다음과 같습니다.

   ![완료된 선택 작업](./media/logic-apps-perform-data-operations/finished-select-action.png)

6. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

기본 워크플로 정의에서 이 작업에 대한 자세한 내용은 [선택 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조하세요.

### <a name="test-your-logic-app"></a>논리 앱 테스트

**선택** 작업에서 예상된 결과를 만드는지 확인하려면 **선택** 작업의 출력이 포함된 알림을 자신에게 보냅니다.

1. 논리 앱에서 **선택** 작업의 결과를 보낼 수 있는 작업을 추가합니다.

2. 해당 작업에서 결과를 표시하려는 아무 위치를 클릭합니다. 동적 콘텐츠 목록이 열리면 **식**을 선택합니다. **선택** 작업에서 배열 출력을 가져오려면 **선택** 작업 이름이 포함된 다음 식을 입력합니다.

   ```
   @actionBody('Select')
   ```

   이 예제에서는 **Office 365 Outlook - 이메일 보내기** 작업을 사용하고, 이메일의 본문에 **actionBody('Select')** 식의 출력이 포함됩니다.

   !["이메일 보내기" 작업의 작업 출력](./media/logic-apps-perform-data-operations/send-email-select-action.png)

3. 이제 논리 앱을 수동으로 실행합니다. 디자이너 도구 모음에서 **실행**을 선택합니다. 

   사용한 이메일 커넥터에 따라 다음과 같은 결과가 표시됩니다.

   !["선택" 작업 결과가 포함된 이메일](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.

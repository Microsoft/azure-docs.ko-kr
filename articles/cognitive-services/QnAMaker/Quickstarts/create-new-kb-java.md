---
title: '빠른 시작: 기술 자료 만들기 - REST, Java - QnA Maker'
description: 이 Java REST 기반 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시 되는 샘플 QnA Maker 기술 자료를 프로그래밍 방식으로 만드는 과정을 안내 합니다.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 3a20198e1fce7b72befb0963a4f1eb7a5e7e3f08
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259798"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-java"></a>빠른 시작: Java를 사용하여 QnA Maker 기술 자료 만들기

이 빠른 시작에서는 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/knowledge-base.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의되어 있습니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [기술 자료 만들기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [작업 세부 정보 가져오기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[참조 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Java 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.

[샘플 코드](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/create-knowledge-base/CreateKB.java)는 Java가 포함된 QnA Maker GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="create-a-knowledge-base-file"></a>기술 자료 파일 만들기

`CreateKB.java` 파일 만들기

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`CreateKB.java`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="dependencies":::

## <a name="add-the-required-constants"></a>필요한 상수 추가
앞부분의 필수 종속성 뒤에서, QnA Maker에 액세스하는 데 필요한 `CreateKB` 클래스에 필수 상수를 추가합니다.

[QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 리소스 이름을 검색하려면 QnA Maker에 대한 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.

다음 값을 설정합니다.

* `<your-qna-maker-subscription-key>` - **키**는 32자 문자열이며 빠른 시작 페이지의 Azure Portal, QnA Maker 리소스에서 사용할 수 있습니다. 이 키는 예측 끝점 키와 다릅니다.
* `<your-resource-name>` - **리소스 이름**은 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 형식으로 제작하기 위한 제작 엔드포인트 URL을 구성하는 데 사용됩니다. 이 리소스 이름은 예측 끝점을 쿼리 하는 데 사용 된 이름과 동일 하지 않습니다.

클래스를 종료하기 위해 마지막 중괄호를 추가할 필요가 없습니다. 이 빠른 시작의 마지막에 나오는 최종 코드 조각입니다.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="constants":::


## <a name="add-the-kb-model-definition-classes"></a>KB 모델 정의 클래스 추가
상수 뒤에서, 모델 정의 개체를 JSON으로 직렬화하는 다음 클래스 및 함수를 `CreateKB` 클래스 내부에 추가합니다.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="model":::

## <a name="add-supporting-functions"></a>지원하는 함수 추가

`CreateKB` 함수 내부에 다음 지원 함수를 추가합니다.

1. JSON을 읽을 수 있는 형식으로 출력하는 다음 함수를 추가합니다.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="pretty":::

2. HTTP 응답을 관리하는 다음 클래스를 추가합니다.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="response":::

3. QnA Maker API에 대한 POST 요청을 만드는 다음 메서드를 추가합니다. `Ocp-Apim-Subscription-Key`는 인증에 사용되는 QnA Maker 서비스 키입니다.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="post":::

4. QnA Maker API에 대한 GET 요청을 만드는 다음 메서드를 추가합니다.

    :::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get":::

## <a name="add-a-method-to-create-the-kb"></a>KB를 만드는 메서드 추가
Post 메서드를 호출하여 KB를 만드는 다음 메서드를 추가합니다.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="create_kb":::

다음 API 호출은 작업 ID를 포함한 JSON 응답을 반환합니다. 작업 ID를 사용하여 KB가 성공적으로 만들어졌는지 결정합니다.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-a-method-to-get-status"></a>상태를 가져오는 메서드 추가
만들기 상태를 확인하는 다음 메서드를 추가합니다.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="get_status":::

성공하거나 실패할 때까지 호출을 반복합니다.

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-a-main-method"></a>main 메서드 추가
main 메서드는 KB를 만든 후 상태를 폴링합니다. POST 응답 헤더 필드 중 **Location**에 작업 ID가 반환되며, GET 요청 시 경로의 일부에 사용됩니다. `while` 루프는 완료되지 않으면 상태를 재시도합니다.

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/CreateKB.java" id="main":::

## <a name="compile-and-run-the-program"></a>프로그램 컴파일 및 실행

1. gson 라이브러리가 `./libs` 디렉터리에 있는지 확인합니다. 명령줄에서 파일을 컴파일합니다 `CreateKB.java` .

    ```bash
    javac -cp ".;libs/*" CreateKB.java
    ```

2. 명령줄에서 다음 명령을 입력 하 여 프로그램을 실행 합니다. 그러면 KB를 만들기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

    ```bash
    java -cp ",;libs/*" CreateKB
    ```

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

---
title: '빠른 시작: 기술 자료 만들기 - REST, C# - QnA Maker'
description: 이 C# REST 기반 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 343dc5f0b8475a9fad3d9834f97cff0d0f4c75f6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260087"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c-with-rest"></a>빠른 시작: REST를 사용 하 여 c #을 사용 하 여 QnA Maker에서 기술 자료 만들기

이 빠른 시작에서는 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만들고 게시하는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/knowledge-base.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의되어 있습니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [기술 자료 만들기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [작업 세부 정보 가져오기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[참조 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [C # 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/create-knowledge-base/QnaQuickstartCreateKnowledgebase/Program.cs)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)
* [QnA Maker 리소스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `qna-maker-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```dotnetcli
dotnet new console -n qna-maker-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```dotnetcli
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

Program.cs의 맨 위에서 단일 using 문을 다음 줄로 바꾸어 프로젝트에 필요한 종속성을 추가합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="dependencies":::

## <a name="add-the-required-constants"></a>필요한 상수 추가

Program 클래스의 맨 위에서 QnA Maker에 액세스하기 위한 필수 상수를 추가합니다.

환경 변수에서 다음 값을 설정합니다.

* `QNA_MAKER_SUBSCRIPTION_KEY` - **키**는 32자 문자열이며 빠른 시작 페이지의 Azure Portal, QnA Maker 리소스에서 사용할 수 있습니다. 이는 예측 엔드포인트 키와 동일하지 않습니다.
* `QNA_MAKER_ENDPOINT` - **엔드포인트**는 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 형식의 작성을 위한 URL입니다. 이는 예측 엔드포인트를 쿼리하는 데 사용되는 URL과 동일하지 않습니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="constants":::

## <a name="add-the-kb-definition"></a>KB 정의 추가

다음 KB 정의를 상수 뒤에 추가합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="kb":::

## <a name="add-supporting-functions-and-structures"></a>지원하는 함수 및 구조 추가
프로그램 클래스 안에 다음 코드 블록을 추가합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="support":::

## <a name="add-a-post-request-to-create-kb"></a>KB를 만들기 위한 POST 요청 추가

다음 코드는 QnA Maker API에 KB를 만들도록 HTTPS 요청을 하고 응답을 수신합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="post_create_kb":::

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

## <a name="add-get-request-to-determine-creation-status"></a>만들기 상태를 확인하기 위한 GET 요청 추가

작업의 상태를 확인합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get":::

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="get_status":::

다음 API 호출은 작업 상태를 포함한 JSON 응답을 반환합니다.

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

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

## <a name="add-createkb-method"></a>CreateKB 메서드 추가

다음 메서드는 KB를 만들고 상태 확인을 반복합니다.  _만들기_ **Operation ID**는 POST 응답 헤더 필드 **Location**에 반환된 다음, GET 요청의 경로로 사용됩니다. KB를 만드는 데 시간이 걸릴 수 있으므로 상태가 성공 또는 실패 할 때까지, 상태 확인을 위해 호출을 반복해야 합니다. 작업이 성공하면 KB ID가 **resourceLocation**으로 반환됩니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="create_kb":::

## <a name="add-the-createkb-method-to-main"></a>Main에 CreateKB 메서드 추가

CreateKB 메서드를 호출하도록 Main 메서드를 변경합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/create-kb.cs" id="main":::

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 빌드하고 실행합니다. 그러면 자동으로 KB를 만들기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다.


[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

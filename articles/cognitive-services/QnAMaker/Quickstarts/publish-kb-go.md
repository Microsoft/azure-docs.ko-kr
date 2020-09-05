---
title: '빠른 시작: 기술 자료, REST, 이동 QnA Maker 게시'
description: 이 Go REST 기반 빠른 시작은 기술 자료를 게시하고 애플리케이션 또는 채팅 봇에서 호출할 수 있는 엔드포인트를 만듭니다.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: b94b09fcb3bfff2eeacabaaa49eb5e4c751ec79d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267754"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>빠른 시작: Go를 사용하여 QnA Maker 기술 자료 게시

이 REST 기반 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Cognitive Search 인덱스에 푸시하고, 애플리케이션 또는 채팅 봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [게시](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 이 API는 요청 본문에 어떤 정보도 요구하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.

* QnA Maker KB(기술 자료) ID는 아래와 같이 `kbid` 쿼리 문자열 매개 변수의 URL에 있습니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](create-new-kb-csharp.md)에서 사용하면 됩니다.

> [!NOTE]
> 전체 솔루션 파일은 [**Azure-Samples/cognitive-services-qnamaker-go** GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)에서 사용할 수 있습니다.

## <a name="create-a-go-file"></a>Go 파일 만들기

VSCode를 열고 `publish-kb.go`라는 새 파일을 만듭니다.

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`publish-kb.go`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>main 함수 만들기

필요한 종속성 뒤에 다음 클래스를 추가합니다.

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>KB를 게시하기 위한 POST 요청 추가

다음 코드를 추가 하 여 기술 자료를 게시 하 고 응답을 수신 하는 QnA Maker API에 대 한 HTTPS 요청을 수행 합니다.

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

API 호출은 성공적인 게시에 대해 응답 본문에 내용이 없는 204 상태를 반환합니다. 이 코드는 204 응답에 대한 내용을 추가합니다.

다른 응답의 경우 해당 응답은 변경되지 않은 채 반환됩니다.

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

파일을 컴파일하는 다음 명령을 실행합니다. 명령 프롬프트에는 빌드 성공에 대한 정보가 반환되지 않습니다.

```bash
go build publish-kb.go
```

프로그램을 실행하려면 명령줄에 다음 명령을 입력합니다. 이 명령은 기술 자료를 게시한 후 성공 또는 오류에 대한 204를 출력하기 위한 요청을 QnA Maker API에 보냅니다.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

기술 자료가 게시된 후 [답변을 생성할 엔드포인트 URL](./get-answer-from-knowledge-base-go.md)이 필요합니다.

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

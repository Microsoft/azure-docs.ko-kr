---
title: '빠른 시작: 기술 자료에서 답변 찾기 - REST, Python - QnA Maker'
description: 이 Python REST 기반 빠른 시작은 기술 자료에서 프로그래밍 방식으로 답변을 가져오는 방법을 안내합니다.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 645ad24b2b275f9a14fda82746347a6f64c735dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268009"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-python"></a>빠른 시작: Python을 사용 하 여 기술 자료에서 질문에 대 한 답변 가져오기

이 빠른 시작에서는 게시된 QnA Maker 기술 자료에서 프로그래밍 방식으로 답변을 가져오는 방법을 안내합니다. 기술 자료에는 FAQ와 같은 [데이터 원본](../Concepts/knowledge-base.md)의 질문과 답변이 있습니다. [질문](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)은 QnA Maker 서비스로 전송됩니다. [응답](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)은 예상되는 상위 답변을 포함합니다.

[참조 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/get-answer/get-answer-3x.py)

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.6 이상](https://www.python.org/downloads/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키를 검색하려면 QnA Maker 리소스에 대한 Azure 대시보드의 **리소스 관리** 아래에서 **키**를 선택합니다.
* **게시** 페이지 설정. 게시된 기술 자료가 없는 경우 빈 기술 자료를 만든 다음, **설정** 페이지에서 기술 자료를 가져온 후 게시합니다. [이 기본 기술 자료](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)를 다운로드하고 사용할 수 있습니다.

    게시 페이지 설정에는 POST 경로 값, 호스트 값 및 EndpointKey 값이 포함됩니다.

    ![게시 설정](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-python-file"></a>Python 파일 만들기

VSCode를 열고 `get-answer-3x.py`라는 새 파일을 만듭니다.

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`get-answer-3x.py` 파일 맨 위에 프로젝트에 필요한 종속성을 추가합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="dependencies":::

<!--TBD - reword this following paragraph -->

호스트 및 경로는 **게시** 페이지에 표시되는 방식과 다릅니다. python 라이브러리는 호스트에서 라우팅를 허용하지 않기 때문입니다. **게시** 페이지에 호스트 일부로 표시되는 라우팅은 해당 경로로 이동되었습니다.

## <a name="add-the-required-constants"></a>필요한 상수 추가

QnA Maker에 액세스하는 데 필요한 상수를 추가합니다. 이러한 값은 기술 자료를 게시한 후 **게시** 페이지에 표시됩니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="constants":::

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>질문을 보내고 답변을 가져오기 위한 POST 요청 추가

다음 코드에서는 기술 자료로 질문을 보내기 위한 QnA Maker API에 대한 HTTPS 요청을 수행한 후 답변을 수신합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/query-kb.py" id="main":::

`Authorization` 헤더의 값에는 문자열 `EndpointKey`가 포함됩니다.

## <a name="run-the-program"></a>프로그램 실행

명령줄에서 프로그램을 실행합니다. 자동으로 해당 요청이 QnA Maker API로 전송되고, 응답이 콘솔 창에 출력됩니다.

다음과 같이 파일을 실행합니다.

```bash
python get-answer-3x.py
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[요청](../how-to/metadata-generateanswer-usage.md#generateanswer-request) 및 [응답](../how-to/metadata-generateanswer-usage.md#generateanswer-response)에 대한 자세한 정보

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

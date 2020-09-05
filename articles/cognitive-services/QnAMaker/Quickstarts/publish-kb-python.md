---
title: '빠른 시작: 기술 자료, REST, Python-QnA Maker 게시'
description: 이 Python REST 기반 빠른 시작은 기술 자료를 게시하고 애플리케이션 또는 채팅 봇에서 호출할 수 있는 엔드포인트를 만듭니다.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: 6ba263633c6ee638ea742c47e36be4d184cdea75
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266445"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>빠른 시작: Python을 사용하여 QnA Maker 기술 자료 업데이트

이 REST 기반 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Cognitive Search 인덱스에 푸시하고, 애플리케이션 또는 채팅 봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이 빠른 시작에서 호출하는 QnA Maker REST API는 다음과 같습니다.
* [게시](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 이 API는 요청 본문에 어떤 정보도 요구하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.7](https://www.python.org/downloads/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.
* QnA Maker KB(기술 자료) ID는 아래와 같이 `kbid` 쿼리 문자열 매개 변수의 URL에 있습니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](../how-to/create-knowledge-base.md)에서 사용하면 됩니다.

> [!NOTE]
> 전체 솔루션 파일은 [**Azure-Samples/cognitive-services-qnamaker-python** GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base)에서 사용할 수 있습니다.

## <a name="create-a-knowledge-base-python-file"></a>기술 자료 Python 파일 만들기

`publish-kb-3x.py`라는 파일을 만듭니다.

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`publish-kb-3x.py`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="dependencies":::

## <a name="add-required-constants"></a>필요한 상수 추가

앞서의 필요한 종속성 뒤에 QnA Maker에 액세스하는 데 필요한 상수를 추가합니다. 사용자 고유의 값으로 대체합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>기술 자료를 게시하기 위한 POST 요청 추가

필요한 상수 뒤에 다음 코드를 추가합니다. 이 코드는 기술 자료로 질문을 보내기 위한 QnA Maker API에 대한 HTTPS 요청을 수행한 후 답변을 수신합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/publish-kb.py" id="main":::

API 호출은 성공적인 게시에 대해 응답 본문에 내용이 없는 204 상태를 반환합니다. 이 코드는 204 응답에 대한 내용을 추가합니다.

다른 응답의 경우 해당 응답은 변경되지 않은 채 반환됩니다.

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 실행하려면 명령줄에 다음 명령을 입력합니다. 이 명령은 기술 자료를 게시한 후 성공 또는 오류에 대한 204를 출력하기 위한 요청을 QnA Maker API에 보냅니다.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

기술 자료가 게시된 후 [답변을 생성할 엔드포인트 URL](./get-answer-from-knowledge-base-python.md)이 필요합니다.

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

[QnA Maker 개요](../Overview/overview.md)

---
title: '빠른 시작: 기술 자료 만들기 - REST, Python - QnA Maker'
description: 이 Python REST 기반 빠른 시작에서는 Cognitive Services API 계정의 Azure 대시보드에 표시될 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만드는 방법을 안내합니다.
ms.date: 12/16/2019
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-python
ms.topic: how-to
ms.openlocfilehash: afee82b66f9803333e27f029ecb487a47ba5dd9e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259730"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>빠른 시작: Python을 사용하여 QnA Maker 기술 자료 만들기

이 빠른 시작에서는 QnA Maker 기술 자료 샘플을 프로그래밍 방식으로 만들고 게시하는 방법을 안내합니다. QnA Maker는 [데이터 원본](../Concepts/knowledge-base.md)에서 반구조화된 콘텐츠(예: FAQ)의 질문과 답변을 자동으로 추출합니다. 기술 자료 모델은 API 요청 본문에 전송된 JSON에 정의되어 있습니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [기술 자료 만들기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)
* [작업 세부 정보 가져오기](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)

[참조 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)  |  [Python 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.7](https://www.python.org/downloads/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.

## <a name="create-a-knowledge-base-python-file"></a>기술 자료 Python 파일 만들기

`create-new-knowledge-base-3x.py`라는 파일을 만듭니다.

## <a name="add-the-required-dependencies"></a>필수 종속성 추가

`create-new-knowledge-base-3x.py`의 맨 위에 프로젝트에 필요한 종속성을 추가하는 다음 줄을 추가합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="dependencies":::

## <a name="add-the-required-constants"></a>필요한 상수 추가
앞서의 필요한 종속성 뒤에 QnA Maker에 액세스하는 데 필요한 상수를 추가합니다. `<your-qna-maker-subscription-key>` 및 `<your-resource-name>`의 값을 고유한 QnA Maker 키 및 리소스 이름으로 바꿉니다.

Program 클래스의 맨 위에서 QnA Maker에 액세스하기 위한 필수 상수를 추가합니다.

다음 값을 설정합니다.

* `<your-qna-maker-subscription-key>` - **키**는 32자 문자열이며 빠른 시작 페이지의 Azure Portal, QnA Maker 리소스에서 사용할 수 있습니다. 이는 예측 엔드포인트 키와 동일하지 않습니다.
* `<your-resource-name>` - **리소스 이름**은 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 형식으로 제작하기 위한 제작 엔드포인트 URL을 구성하는 데 사용됩니다. 이는 예측 엔드포인트를 쿼리하는 데 사용되는 URL과 동일하지 않습니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="constants":::

## <a name="add-the-kb-model-definition"></a>KB 모델 정의 추가

다음 KB 모델 정의를 상수 뒤에 추가합니다. 모델이 정의 뒤에 있는 문자열로 변환됩니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="model":::

## <a name="add-supporting-function"></a>지원 함수 추가

JSON을 읽을 수 있는 형식으로 출력하는 다음 함수를 추가합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="pretty":::

## <a name="add-function-to-create-kb"></a>KB를 만드는 함수 추가

기술 자료를 만들도록 HTTP POST 요청을 하는 다음 함수를 추가합니다.
이 API 호출은 헤더 필드 **Location**에 ID를 포함하는 JSON 응답을 반환합니다. 작업 ID를 사용하여 KB가 성공적으로 만들어졌는지 결정합니다. `Ocp-Apim-Subscription-Key`는 인증에 사용되는 QnA Maker 서비스 키입니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="create_kb":::

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

## <a name="add-function-to-check-creation-status"></a>만들기 상태를 확인하는 함수 추가

다음 함수는 URL 경로 끝부분의 작업 ID에 전송되는 만들기 상태를 확인합니다. `check_status` 호출은 기본 _while_ 반복 내부에서 이루어집니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="get_status":::

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

## <a name="add-main-code-block"></a>기본 코드 블록 추가
다음 반복은 작업이 완료될 때까지 주기적으로 만들기 작업 상태를 폴링합니다.

:::code language="python" source="~/cognitive-services-quickstart-code/python/QnAMaker/rest/create-kb.py" id="main":::

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 실행하려면 명령줄에 다음 명령을 입력합니다. 그러면 KB를 만들기 위한 요청을 QnA Maker API에 보낸 다음, 30초마다 결과를 폴링합니다. 각 응답이 콘솔 창에 출력됩니다.

```bash
python create-new-knowledge-base-3x.py
```

기술 자료가 생성되면 QnA Maker 포털의 [내 기술 자료](https://www.qnamaker.ai/Home/MyServices) 페이지에서 볼 수 있습니다. 보고 싶은 기술 자료 이름을 선택합니다(예: QnA Maker FAQ).

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

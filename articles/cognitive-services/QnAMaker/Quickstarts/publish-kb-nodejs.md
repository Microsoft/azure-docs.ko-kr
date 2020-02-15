---
title: '빠른 시작: Node.js용 REST API 포함 QnA Maker'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Node.js용 QnA Maker REST API를 시작하는 방법을 보여줍니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 3df0a486baa2184a58ac33c24a5d13973885890f
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109512"
---
# <a name="quickstart-qna-maker-rest-apis-for-nodejs"></a>빠른 시작: Node.js용 QnA Maker REST API

Node.js용 QnA Maker REST API를 시작합니다. 다음 단계에 따라 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.

Node.js용 QnA Maker REST API를 사용하여 다음을 수행합니다.

* 기술 자료 만들기
* 기술 자료 바꾸기
* 기술 자료 게시
* 기술 자료 삭제
* 기술 자료 다운로드
* 작업의 상태 가져오기

[참조 설명서](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) | [Node.js 샘플](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/tree/master/documentation-samples/quickstarts/rest-api)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 현재 버전의 [Node.js](https://nodejs.org)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 QnA Maker용 리소스를 만듭니다.

리소스에서 키를 가져온 후 `QNAMAKER_RESOURCE_KEY` 및 `QNAMAKER_AUTHORING_ENDPOINT`라는 리소스에 대해 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). Azure Portal에서 리소스의 **빠른 시작** 페이지에 있는 키와 엔드포인트 값을 사용합니다.

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir myapp && cd myapp
```

`npm init -y` 명령을 실행하여 노드 `package.json` 파일을 만듭니다.

```console
npm init -y
```

`reqeuestretry` 및 `request` NPM 패키지를 추가합니다.

```console
npm install requestretry request --save
```

## <a name="code-examples"></a>코드 예제

이 코드 조각은 Node.js용 QnA Maker REST API를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [기술 자료 만들기](#create-a-knowledge-base)
* [기술 자료 바꾸기](#replace-a-knowledge-base)
* [기술 자료 게시](#publish-a-knowledge-base)
* [기술 자료 삭제](#delete-a-knowledge-base)
* [기술 자료 다운로드](#download-the-knowledge-base)
* [작업의 상태 가져오기](#get-status-of-an-operation)

## <a name="add-the-dependencies"></a>종속성 추가

`rest-apis.js`라는 파일을 만들고 HTTP 요청을 수행하도록 다음 _requires_ 문을 추가합니다.

```javascript
const request = require("requestretry");
```

## <a name="add-azure-resource-information"></a>Azure 리소스 정보 추가

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

다음 환경 값을 설정합니다.

* `QNAMAKER_RESOURCE_KEY` - **키**는 32자 문자열이며 빠른 시작 페이지의 Azure Portal, QnA Maker 리소스에서 사용할 수 있습니다. 이는 예측 엔드포인트 키와 동일하지 않습니다.
* `QNAMAKER_AUTHORING_ENDPOINT` - `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` 형식의 작성 엔드포인트에는 **리소스 이름**이 포함됩니다. 이는 예측 엔드포인트를 쿼리하는 데 사용되는 URL과 동일하지 않습니다.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=authorization)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음과 같은 JSON 개체에서 생성된 질문과 답변 쌍을 저장합니다.

* **편집 콘텐츠**.
* **파일** - 권한이 필요없는 로컬 파일입니다.
* **URL** - 공개적으로 사용할 수 있는 URL입니다.

[REST API를 사용하여 기술 자료를 생성](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)합니다.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=createKb)]

## <a name="replace-a-knowledge-base"></a>기술 자료 바꾸기

[REST API를 사용하여 기술 자료를 바꿉니다](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=replaceKb)]

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

기술 자료를 게시합니다. 이 프로세스는 HTTP 쿼리 예측 엔드포인트에서 기술 자료를 사용할 수 있도록 설정합니다.

[REST API를 사용하여 기술 자료를 게시](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish)합니다.


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=publish)]

## <a name="download-the-knowledge-base"></a>기술 자료 다운로드

[REST API를 사용하여 기술 자료를 다운로드](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)합니다.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=download)]

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 사용을 완료하면 삭제합니다.

[REST API를 사용하여 기술 자료를 삭제](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete)합니다.

[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=deleteKb)]

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

생성 프로세스와 같이 오래 실행되는 프로세스는 별도의 REST API 호출로 확인해야 하는 작업 ID를 반환합니다. 이 함수는 응답 생성 본문을 사용합니다. 중요한 키는 폴링을 계속해야 할지 여부를 결정하는 `operationState`입니다.

[REST API를 사용하여 기술 자료에서 작업을 모니터링](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails)합니다.


[!code-javascript[Add Azure resources from environment variables](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/rest-api/rest-api.js?name=operationDetails)]


## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `node rest-apis.js` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node rest-apis.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[자습서: KB에서 만들기 및 대답](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API란?](../Overview/overview.md)
* [기술 자료 편집](../how-to/edit-knowledge-base.md)
* [사용량 현황 분석 가져오기](../how-to/get-analytics-knowledge-base.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/rest-api/rest-api.js)에서 확인할 수 있습니다.
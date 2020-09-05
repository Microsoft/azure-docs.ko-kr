---
title: Node.js를 사용하여 발언 가져오기 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 작성 API를 사용하여 CSV 형식의 기존 데이터에서 프로그래밍 방식으로 LUIS 앱을 빌드하는 방법에 대해 알아봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18, devx-track-javascript
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 3df1ccadb69f02272c44a0ba574805c816151dc7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520943"
---
# <a name="build-a-luis-app-programmatically-using-nodejs"></a>Node.js를 사용하여 프로그래밍 방식으로 LUIS 앱 빌드

LUIS는 [LUIS](luis-reference-regions.md) 웹 사이트에서 수행하는 모든 작업을 수행하는 프로그래밍 방식의 API를 제공합니다. 따라서 기존 데이터가 있는 경우 시간을 절약할 수 있으며 직접 정보를 입력할 때보다 더 빠르게 프로그래밍 방식으로 LUIS 앱을 만들 수 있습니다.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="prerequisites"></a>사전 요구 사항

* [LUIS](luis-reference-regions.md) 웹 사이트에 로그인하고 계정 설정에서 [작성 키](luis-how-to-azure-subscription.md#authoring-key)를 찾습니다. 이 키를 사용하여 작성 API를 호출합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.
* 이 문서는 사용자 요청에 대 한 가상 회사의 로그 파일에 대 한 CSV로 시작 합니다. [여기](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv)에서 다운로드하세요.
* NPM을 사용하는 최신 Node.js를 설치합니다. [여기](https://nodejs.org/en/download/)에서 다운로드하세요.
* **[권장]** IntelliSense 및 디버깅용 Visual Studio Code를 [여기](https://code.visualstudio.com/)에서 무료로 다운로드하세요.

이 문서의 모든 코드는 [Azure 샘플 Language Understanding GitHub 리포지토리에서](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/examples/build-app-programmatically-csv)사용할 수 있습니다.

## <a name="map-preexisting-data-to-intents-and-entities"></a>의도 및 엔터티에 기존 데이터 매핑
LUIS를 사용하여 만들지 않은 시스템이 있는 경우에도 사용자가 수행하려는 다양한 작업에 매핑되는 텍스트 데이터가 있으면 사용자 입력의 기존 범주에서 LUIS의 의도로 매핑할 수 있습니다. 사용자가 말한 내용에서 중요한 단어나 구를 식별할 수 있는 경우, 이러한 단어가 엔터티에 매핑될 수 있습니다.

파일을 엽니다 [`IoT.csv`](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/IoT.csv) . 여기에는 사용자 쿼리가 분류된 방식, 사용자가 말한 내용, 쿼리에서 가져온 유용한 정보가 있는 일부 열을 포함하여 가상 홈 자동화 서비스에 대한 사용자 쿼리 로그가 포함됩니다.

![기존 데이터의 CSV 파일](./media/luis-tutorial-node-import-utterances-csv/csv.png)

**RequestType** 열이 의도가 되고 **Request** 열에는 예제 발화가 표시되는 것을 확인할 수 있습니다. 다른 필드가 발화에서 나타나는 경우 엔터티가 될 수 있습니다. 의도, 엔터티 및 예제 발화가 있으므로 간단한 샘플 앱에 대한 요구 사항이 충족됩니다.

## <a name="steps-to-generate-a-luis-app-from-non-luis-data"></a>비 LUIS 데이터에서 LUIS 앱을 생성하는 단계
CSV 파일에서 새 LUIS 앱을 생성하려면 다음을 수행합니다.

* CSV 파일에서 데이터를 구문 분석합니다.
    * Authoring API를 사용하여 LUIS에 업로드할 수 있는 형식으로 변환합니다.
    * 구문 분석된 데이터에서 의도 및 엔터티에 대한 정보를 수집합니다.
* 다음에 대한 API 작성 호출을 만듭니다.
    * 앱을 만듭니다.
    * 구문 분석된 데이터에서 수집된 의도 및 엔터티를 추가합니다.
    * LUIS 앱을 만든 다음에는 구문 분석된 데이터에서 예제 발화를 추가할 수 있습니다.

`index.js` 파일의 마지막 부분에서 이 프로그램 흐름을 확인할 수 있습니다. 이 코드를 복사하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/index.js)하여 `index.js`에 저장합니다.

   [!code-javascript[Node.js code for calling the steps to build a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/index.js)]


## <a name="parse-the-csv"></a>CSV 구문 분석

CSV에서 발화를 포함하는 열 항목을 LUIS에서 이해할 수 있는 JSON 형식으로 구문 분석해야 합니다. 이 JSON 형식에는 발화의 의도를 식별하는 `intentName` 필드가 포함되어야 합니다. 또한 발화에 엔터티가 없는 경우, 비어 있을 수 있는 `entityLabels` 필드도 포함되어야 합니다.

예를 들어, “Turn on the lights”에 대한 항목은 다음 JSON에 매핑됩니다.

```json
        {
            "text": "Turn on the lights",
            "intentName": "TurnOn",
            "entityLabels": [
                {
                    "entityName": "Operation",
                    "startCharIndex": 5,
                    "endCharIndex": 6
                },
                {
                    "entityName": "Device",
                    "startCharIndex": 12,
                    "endCharIndex": 17
                }
            ]
        }
```

이 예제에서 `intentName`은 CSV 파일에서 **Request** 열 머리글 아래의 사용자 요청에서 가져오고 `entityName`은 키 정보가 있는 다른 열에서 가져옵니다. 예를 들어, **Operation** 또는 **Device**에 대한 항목이 있고 해당 문자열이 실제 요청에도 나타나면 엔터티로 레이블을 지정할 수 있습니다. 다음 코드는 이 구문 분석 프로세스를 보여 줍니다. 이 코드를 복사하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_parse.js)하여 `_parse.js`에 저장할 수 있습니다.

   [!code-javascript[Node.js code for parsing a CSV file to extract intents, entities, and labeled utterances](~/samples-luis/examples/build-app-programmatically-csv/_parse.js)]



## <a name="create-the-luis-app"></a>LUIS 앱 만들기
데이터를 JSON으로 구문 분석했으면 LUIS 앱에 추가합니다. 다음은 LUIS 앱을 만드는 코드입니다. 이 코드를 복사하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_create.js)하여 `_create.js`에 저장합니다.

   [!code-javascript[Node.js code for creating a LUIS app](~/samples-luis/examples/build-app-programmatically-csv/_create.js)]


## <a name="add-intents"></a>의도 추가
앱이 있으면 의도를 추가해야 합니다. 다음은 LUIS 앱을 만드는 코드입니다. 이 코드를 복사하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_intents.js)하여 `_intents.js`에 저장합니다.

   [!code-javascript[Node.js code for creating a series of intents](~/samples-luis/examples/build-app-programmatically-csv/_intents.js)]


## <a name="add-entities"></a>엔터티 추가
다음은 LUIS 앱에 엔터티를 추가하는 코드입니다. 이 코드를 복사하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_entities.js)하여 `_entities.js`에 저장합니다.

   [!code-javascript[Node.js code for creating entities](~/samples-luis/examples/build-app-programmatically-csv/_entities.js)]



## <a name="add-utterances"></a>발언 추가
LUIS 앱에서 엔터티 및 의도를 정의했으면 발화를 추가할 수 있습니다. 다음 코드에서는 한 번에 최대 100개까지 발화를 추가할 수 있는 [Utterances_AddBatch](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09) API를 사용합니다.  이 코드를 복사하거나 [다운로드](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/examples/build-app-programmatically-csv/_upload.js)하여 `_upload.js`에 저장합니다.

   [!code-javascript[Node.js code for adding utterances](~/samples-luis/examples/build-app-programmatically-csv/_upload.js)]


## <a name="run-the-code"></a>코드 실행


### <a name="install-nodejs-dependencies"></a>Node.js 종속성 설치
터미널/명령줄의 NPM에서 Node.js 종속성을 설치합니다.

```console
> npm install
```

### <a name="change-configuration-settings"></a>구성 설정 변경
이 애플리케이션을 사용하려면 index.js 파일의 값을 고유한 엔드포인트 키로 변경하고 앱에 지정할 이름을 제공해야 합니다. 앱의 문화권을 설정하거나 버전 번호를 변경할 수도 있습니다.

index.js 파일을 열고 파일의 맨 위에서 이러한 값을 변경하세요.


```javascript
// Change these values
const LUIS_programmaticKey = "YOUR_AUTHORING_KEY";
const LUIS_appName = "Sample App";
const LUIS_appCulture = "en-us";
const LUIS_versionId = "0.1";
```

### <a name="run-the-script"></a>스크립트 실행
터미널/명령줄에서 Node.js로 스크립트를 실행합니다.

```console
> node index.js
```

또는

```console
> npm start
```

### <a name="application-progress"></a>애플리케이션 진행률
애플리케이션이 실행 중이면 명령줄에 진행률이 표시됩니다. 명령줄 출력에는 LUIS의 응답 형식이 포함됩니다.

```console
> node index.js
intents: ["TurnOn","TurnOff","Dim","Other"]
entities: ["Operation","Device","Room"]
parse done
JSON file should contain utterances. Next step is to create an app with the intents and entities it found.
Called createApp, created app with ID 314b306c-0033-4e09-92ab-94fe5ed158a2
Called addIntents for intent named TurnOn.
Called addIntents for intent named TurnOff.
Called addIntents for intent named Dim.
Called addIntents for intent named Other.
Results of all calls to addIntent = [{"response":"e7eaf224-8c61-44ed-a6b0-2ab4dc56f1d0"},{"response":"a8a17efd-f01c-488d-ad44-a31a818cf7d7"},{"response":"bc7c32fc-14a0-4b72-bad4-d345d807f965"},{"response":"727a8d73-cd3b-4096-bc8d-d7cfba12eb44"}]
called addEntity for entity named Operation.
called addEntity for entity named Device.
called addEntity for entity named Room.
Results of all calls to addEntity= [{"response":"6a7e914f-911d-4c6c-a5bc-377afdce4390"},{"response":"56c35237-593d-47f6-9d01-2912fa488760"},{"response":"f1dd440c-2ce3-4a20-a817-a57273f169f3"}]
retrying add examples...

Results of add utterances = [{"response":[{"value":{"UtteranceText":"turn on the lights","ExampleId":-67649},"hasError":false},{"value":{"UtteranceText":"turn the heat on","ExampleId":-69067},"hasError":false},{"value":{"UtteranceText":"switch on the kitchen fan","ExampleId":-3395901},"hasError":false},{"value":{"UtteranceText":"turn off bedroom lights","ExampleId":-85402},"hasError":false},{"value":{"UtteranceText":"turn off air conditioning","ExampleId":-8991572},"hasError":false},{"value":{"UtteranceText":"kill the lights","ExampleId":-70124},"hasError":false},{"value":{"UtteranceText":"dim the lights","ExampleId":-174358},"hasError":false},{"value":{"UtteranceText":"hi how are you","ExampleId":-143722},"hasError":false},{"value":{"UtteranceText":"answer the phone","ExampleId":-69939},"hasError":false},{"value":{"UtteranceText":"are you there","ExampleId":-149588},"hasError":false},{"value":{"UtteranceText":"help","ExampleId":-81949},"hasError":false},{"value":{"UtteranceText":"testing the circuit","ExampleId":-11548708},"hasError":false}]}]
upload done
```




## <a name="open-the-luis-app"></a>LUIS 앱 열기
스크립트가 완료되면 [LUIS](luis-reference-regions.md)에 로그인하여 **내 앱**에서 직접 만든 LUIS 앱을 볼 수 있습니다. **TurnOn**, **TurnOff** 및 **None** 의도에서 추가한 발화를 확인할 수 있습니다.

![TurnOn 의도](./media/luis-tutorial-node-import-utterances-csv/imported-utterances-661.png)


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 웹 사이트에서 앱 테스트 및 학습](luis-interactive-test.md)

## <a name="additional-resources"></a>추가 자료

이 애플리케이션 예제에서는 다음 LUIS API를 사용합니다.
- [앱 만들기](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)
- [의도 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0c)
- [엔터티 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0e)
- [길이 발언 추가](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c09)

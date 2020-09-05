---
title: 개발자 리소스-Language Understanding
description: Sdk, REST Api, CLI는 프로그래밍 언어로 LUIS (Language Understanding) 앱을 개발 하는 데 도움이 됩니다. Azure 리소스를 관리 하 고 예측을 LUIS.
ms.topic: reference
ms.date: 05/19/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 759111ee7e77ce00fec3762b1a20494c2771ef8f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931202"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Language Understanding에 대 한 SDK, REST 및 CLI 개발자 리소스 (LUIS)

Sdk, REST Api, CLI는 프로그래밍 언어로 LUIS (Language Understanding) 앱을 개발 하는 데 도움이 됩니다. Azure 리소스를 관리 하 고 예측을 LUIS.

## <a name="azure-resource-management"></a>Azure 리소스 관리

Azure Cognitive Services 관리 계층을 사용 하 여 Language Understanding 또는 인지 서비스 리소스를 만들고, 편집 하 고, 나열 하 고, 삭제할 수 있습니다.

도구를 기반으로 하는 참조 설명서를 찾습니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 작성 및 예측 요청

Language Understanding 서비스는 만들어야 하는 Azure 리소스에서 액세스할 수 있습니다. 두 가지 리소스가 있습니다.

* 작성, 편집, 학습 및 게시에 대 한 학습을 위해 **제작** 리소스를 사용 합니다.
* 런타임에 **예측** 을 사용 하 여 사용자의 텍스트를 보내고 예측을 받습니다.

[V3 예측 끝점](luis-migration-api-v3.md)에 대해 알아봅니다.

[Cognitive Services 샘플 코드](https://github.com/Azure-Samples/cognitive-services-quickstart-code) 를 사용 하 여 가장 일반적인 작업을 학습 하 고 사용 합니다.

### <a name="rest-specifications"></a>REST 사양

모든 [AZURE rest 사양과](https://github.com/Azure/azure-rest-api-specs)함께 [LUIS REST 사양은](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS)GitHub에서 공개적으로 사용할 수 있습니다.

### <a name="rest-apis"></a>REST API

작성 및 예측 끝점 API는 REST Api에서 사용할 수 있습니다.

|Type|버전|
|--|--|
|작성|[[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[preview V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|예측|[[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST 끝점

LUIS에는 현재 두 가지 유형의 끝점이 있습니다.

* 학습 끝점에서 **제작**
* 런타임 끝점에 대 한 쿼리 **예측** 입니다.

|목적|URL|
|--|--|
|학습 끝점에서 V2 제작|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|학습 끝점에 대 한 V3 제작|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 예측-런타임 끝점의 모든 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 예측-런타임 끝점의 버전 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 예측-런타임 끝점에서 슬롯 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

다음 표에서는 앞의 표에서 중괄호 `{}`로 표시된 매개 변수에 대해 설명합니다.

|매개 변수|목적|
|--|--|
|`your-resource-name`|Azure 리소스 이름|
|`q` 또는 `query`|채팅 봇과 같은 클라이언트 애플리케이션에서 보낸 발언 텍스트|
|`version`|10 자 버전 이름|
|`slot`| `production` 또는 `staging`|

### <a name="rest-query-string-parameters"></a>REST 쿼리 문자열 매개 변수

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>앱 스키마

[앱 스키마](app-schema-definition.md) 를 또는 형식으로 가져오고 내보냅니다 `.json` `.lu` .

### <a name="language-based-sdks"></a>언어 기반 Sdk

|언어 |참조 설명서|패키지|빠른 시작|
|--|--|--|--|
|C#|[작성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[예측](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 제작](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[작성](quickstart-sdk.md?pivots=programming-language-csharp)<br>[쿼리 예측](quickstart-sdk.md?pivots=programming-language-csharp)|
|Go|[작성 및 예측](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[작성 및 예측](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 작성](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 예측](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[작성](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[예측](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 작성](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 예측](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[작성](quickstart-sdk.md?pivots=programming-language-javascript)<br>[예측](quickstart-sdk.md?pivots=programming-language-javascript)|
|Python|[작성 및 예측](quickstart-sdk.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[작성](quickstart-sdk.md?pivots=programming-language-python)<br>[예측](quickstart-sdk.md?pivots=programming-language-python)|


### <a name="containers"></a>컨테이너

LUIS (Language Understanding)는 온-프레미스 및 포함 된 앱 버전을 제공 하는 [컨테이너](luis-container-howto.md) 를 제공 합니다.

### <a name="export-and-import-formats"></a>내보내기 및 가져오기 형식

Language Understanding은 JSON 형식, `.LU` ([ludown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) 형식 및 Language Understanding 컨테이너에 대해 압축 된 패키지에서 앱 및 해당 모델을 관리 하는 기능을 제공 합니다.

이러한 형식의 가져오기 및 내보내기는 Api 및 LUIS 포털에서 사용할 수 있습니다. 포털은 앱 목록 및 버전 목록의 일부로 가져오기 및 내보내기를 제공 합니다.

## <a name="workshops"></a>워크숍

* GitHub: (워크숍) [대화형-AI: NLU USING LUIS](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>연속 통합 도구

* GitHub: (미리 보기) [DevOps를 사용 하 여 LUIS 앱 개발 방법](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [Nlu. DevOps](https://github.com/microsoft/NLU.DevOps) -NLU 서비스에 대 한 지속적인 통합 및 배포를 지 원하는 도구입니다.

## <a name="bot-framework-tools"></a>Bot 프레임 워크 도구

Bot framework는 다양 한 언어의 [SDK](https://github.com/Microsoft/botframework) 로 제공 되며 [Azure Bot Service](https://dev.botframework.com/)를 사용 하는 서비스로 제공 됩니다.

Bot framework는 다음을 비롯 하 여 Language Understanding에 도움이 되는 [여러 가지 도구](https://github.com/microsoft/botbuilder-tools) 를 제공 합니다.

* [Ludown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -빌드 LUIS 언어 markdown 파일을 사용 하 여 모델 이해
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -LUIS.ai 응용 프로그램 만들기 및 관리
* [디스패치](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-부모 및 자식 앱 관리
* [Luisgen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -LUIS 의도 및 엔터티에 대 한 c #/Typescript 클래스 지원 자동 생성
* [Bot framework 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator/releases) -봇 개발자가 BOT framework SDK를 사용 하 여 빌드한 봇을 테스트 하 고 디버그할 수 있도록 하는 데스크톱 응용 프로그램입니다.
* [Bot Framework 작성기](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) -개발자와 멀티 disciplinary 팀이 Microsoft bot framework로 봇 및 대화형 환경을 빌드하는 통합 개발 도구입니다.
* [Bot Framework 샘플](https://github.com/microsoft/botbuilder-samples) -#C, JavaScript, TypeScript 및 Python
## <a name="next-steps"></a>다음 단계

* 일반적인 [HTTP 오류 코드](luis-reference-response-codes.md) 에 대 한 자세한 정보
* 모든 Api 및 Sdk에 대 한 [참조 설명서](https://docs.microsoft.com/azure/index)
* [Bot framework](https://github.com/Microsoft/botbuilder-dotnet) 및 [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [인지 컨테이너](../cognitive-services-container-support.md)

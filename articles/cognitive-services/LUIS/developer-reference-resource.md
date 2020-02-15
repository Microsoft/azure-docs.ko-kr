---
title: 개발자 리소스-Language Understanding
description: Sdk, REST Api, CLI는 프로그래밍 언어로 LUIS (Language Understanding) 앱을 개발 하는 데 도움이 됩니다. Azure 리소스를 관리 하 고 예측을 LUIS.
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: fda4301a0851e6a36cbb6493dcf48293b2c5db37
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152689"
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

|형식|버전|
|--|--|
|제작|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[preview V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|예측|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>REST 끝점

LUIS에는 현재 두 가지 유형의 끝점이 있습니다.

* 학습 끝점에서 제작
* 런타임 끝점에 대 한 쿼리 예측입니다.

|목적|URL|
|--|--|
|학습 끝점에서 제작|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|V2 Runtime-런타임 끝점의 모든 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|V3 런타임-런타임 끝점에서 버전 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|V3 런타임-런타임 끝점에서 슬롯 예측|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

다음 표에서는 앞의 표에서 중괄호 `{}`로 표시된 매개 변수에 대해 설명합니다.

|매개 변수|목적|
|--|--|
|`your-resource-name`|Azure 리소스 이름|
|`q` 또는 `query`|채팅 봇과 같은 클라이언트 애플리케이션에서 보낸 발언 텍스트|
|`version`|10 자 버전 이름|
|`slot`| `production` 또는 `staging`|

### <a name="language-based-sdks"></a>언어 기반 Sdk

|언어 |참조 설명서|패키지|샘플|빠른 시작|
|--|--|--|--|--|
|C#|[작성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[예측](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 제작](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[앱 만들기 및 관리](sdk-authoring.md?pivots=programming-language-csharp)<br>[쿼리 예측 끝점](sdk-query-prediction-endpoint.md)|
|이동|[작성 및 예측](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST를 사용 하 여 작성 및 예측](luis-get-started-get-intent-from-rest.md)|
|Java|[작성 및 예측](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 작성](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 예측](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[작성 및 예측](luis-get-started-get-intent-from-rest.md)
|Node.js|[작성](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[예측](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 작성](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[NPM 예측](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[REST를 사용 하 여 작성 및 예측](luis-get-started-get-intent-from-rest.md)|
|Python|[작성 및 예측](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[작성](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[작성](sdk-authoring.md?pivots=programming-language-python)<br>[REST를 사용한 예측](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>컨테이너

LUIS (Language Understanding)는 온-프레미스 및 포함 된 앱 버전을 제공 하는 [컨테이너](luis-container-howto.md) 를 제공 합니다.

### <a name="export-and-import-formats"></a>내보내기 및 가져오기 형식

Language Understanding은 JSON 형식으로 앱 및 해당 모델을 관리 하는 기능, `.LU` ([Ludown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) 형식 및 Language Understanding 컨테이너의 압축 된 패키지를 제공 합니다.

이러한 형식의 가져오기 및 내보내기는 Api 및 LUIS 포털에서 사용할 수 있습니다. 포털은 앱 목록 및 버전 목록의 일부로 가져오기 및 내보내기를 제공 합니다.

## <a name="other-tools-and-sdks"></a>기타 도구 및 Sdk

Bot framework는 다양 한 언어의 [SDK](https://github.com/Microsoft/botframework) 로 제공 되며 [Azure Bot Service](https://dev.botframework.com/)를 사용 하는 서비스로 제공 됩니다.

Bot framework는 다음을 비롯 하 여 Language Understanding에 도움이 되는 [여러 가지 도구](https://github.com/microsoft/botbuilder-tools) 를 제공 합니다.

* [Ludown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -빌드 LUIS 언어 markdown 파일을 사용 하 여 모델 이해
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -LUIS.ai 응용 프로그램 만들기 및 관리
* [디스패치](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-부모 및 자식 앱 관리
* [Luisgen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -LUIS 의도 및 C#엔터티에 대 한 백업/Typescript 클래스를 자동으로 생성 합니다.
* [Bot framework 에뮬레이터](https://github.com/Microsoft/BotFramework-Emulator/releases) -봇 개발자가 BOT framework SDK를 사용 하 여 빌드한 봇을 테스트 하 고 디버그할 수 있도록 하는 데스크톱 응용 프로그램입니다.
* [Bot Framework 작성기](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) -개발자와 멀티 disciplinary 팀이 Microsoft bot framework로 봇 및 대화형 환경을 빌드하는 통합 개발 도구입니다.

## <a name="next-steps"></a>다음 단계

* 일반적인 [HTTP 오류 코드](luis-reference-response-codes.md) 에 대 한 자세한 정보
* 모든 Api 및 Sdk에 대 한 [참조 설명서](https://docs.microsoft.com/azure/index#pivot=sdkstools)
* [Bot framework](https://github.com/Microsoft/botbuilder-dotnet) 및 [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [인지 컨테이너](../cognitive-services-container-support.md)

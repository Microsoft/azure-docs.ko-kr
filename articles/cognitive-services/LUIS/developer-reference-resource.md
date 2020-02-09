---
title: 개발자 리소스-Language Understanding
titleSuffix: Azure Cognitive Services
description: 개발자는 Language Understanding에 대 한 REST Api와 Sdk를 모두 포함 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: diberry
ms.openlocfilehash: 2f351ac570080c83e78697bbca94340bb96cbcf7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716140"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding에 대 한 개발자 리소스

개발자는 Language Understanding에 대 한 REST Api와 Sdk를 모두 사용할 수 있습니다.

## <a name="azure-resource-management"></a>Azure 리소스 관리

Azure Cognitive Services 관리 계층을 사용 하 여 Language Understanding 또는 인지 서비스 리소스를 만들고, 편집 하 고, 나열 하 고, 삭제할 수 있습니다.

도구를 기반으로 하는 참조 설명서를 찾습니다.

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding 작성 및 예측 요청

Language Understanding 서비스는 만들어야 하는 Azure 리소스에서 액세스할 수 있습니다. 작성 및 예측 엔드포인트 리소스 라는 두 가지 리소스가 있습니다. 이러한 리소스를 모두 사용 하 여 LUIS 리소스를 제어할 수 있습니다.

[V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 알아봅니다.

### <a name="rest-apis"></a>REST API

작성 및 예측 엔드포인트 API는 REST Api에서 사용할 수 있습니다.

|Type|버전|
|--|--|
|제작|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[preview V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|예측|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>언어 기반 Sdk

|언어 |참조 설명서|패키지|샘플|빠른 시작|
|--|--|--|--|--|
|C#|[작성](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[예측](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 제작](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[앱 만들기 및 관리](sdk-authoring.md?pivots=programming-language-csharp)<br>[쿼리 예측 엔드포인트](sdk-query-prediction-endpoint.md)|
|Go|[작성 및 예측](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[작성](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[예측](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST를 사용 하 여 작성 및 예측](luis-get-started-get-intent-from-rest.md)|
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
* [Bot emulator](https://github.com/Microsoft/BotFramework-Emulator/releases) -봇 개발자가 BOT Framework SDK를 사용 하 여 빌드한 봇을 테스트 하 고 디버그할 수 있도록 하는 데스크톱 응용 프로그램입니다.


## <a name="next-steps"></a>다음 단계

* 일반적인 [HTTP 오류 코드](luis-reference-response-codes.md) 에 대 한 자세한 정보
* 모든 Api 및 Sdk에 대 한 [참조 설명서](https://docs.microsoft.com/azure/index#pivot=sdkstools)
* [Bot framework](https://github.com/Microsoft/botbuilder-dotnet) 및 [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [인지 컨테이너](../cognitive-services-container-support.md)
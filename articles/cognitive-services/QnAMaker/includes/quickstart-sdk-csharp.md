---
title: '빠른 시작: .NET용 QnA Maker 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 QnA Maker 클라이언트 라이브러리를 시작하는 방법을 보여줍니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.
ms.topic: quickstart
ms.date: 06/18/2020
ms.openlocfilehash: eecbf162f3bd043bf4aabe9796ee095667e0cc39
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246130"
---
.NET 용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

 * 기술 자료 만들기
 * 기술자료 업데이트
 * 기술 자료 게시
 * 예측 런타임 엔드포인트 키 가져오기
 * 장기 실행 작업 대기
 * 기술 자료 다운로드
 * 답변 구하기
 * 기술 자료 삭제

[참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure 구독을 보유한 후에는 Azure Portal에서 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)를 만들어 작성 키와 리소스 이름을 가져옵니다. 배포 후 **리소스로 이동**을 선택합니다.
    * 애플리케이션을 QnA Maker API에 연결하려면 만드는 리소스의 키와 리소스 이름이 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 리소스 이름을 붙여넣을 것입니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio를 사용하여 **솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택하여 .NET Core 애플리케이션을 만들고 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기**를 선택하고, **시험판 포함**을 선택하고, `Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker`를 검색합니다. `2.0.0-preview.1` 버전, **설치**를 차례로 선택합니다.

#### <a name="cli"></a>[CLI](#tab/cli)

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `qna-maker-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs*라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n qna-maker-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
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

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 QnA Maker 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 2.0.0-preview.1
```


---

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/SDK-based-quickstart/Program.cs)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `using` 지시문을 추가합니다.

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Dependencies&highlight=1-2)]

애플리케이션의 `Main` 메서드에서 다음 섹션에 나와 있는 변수와 코드를 추가하여 이 빠른 시작의 일반적인 작업을 사용합니다.

> [!IMPORTANT]
> Azure Portal로 이동하여 필수 구성 요소에서 만든 QnA Maker 리소스의 키와 엔드포인트를 찾습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다.
> 기술 자료를 만들려면 전체 키가 필요합니다. 엔드포인트의 리소스 이름만 필요합니다. 형식은 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`입니다.
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)는 보안 키 스토리지를 제공합니다.

[!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=Resourcevariables)]


## <a name="object-models"></a>개체 모델

QnA Maker는 다음과 같은 두 가지 개체 모델을 사용합니다.
* **[QnAMakerClient](#qnamakerclient-object-model)** 는 기술 자료를 만들고, 관리하고, 게시하고, 다운로드하는 개체입니다.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 은 GenerateAnswer API를 사용하여 기술 자료를 쿼리하고 Train API([활성 학습](../concepts/active-learning-suggestions.md)의 일부로)를 사용하여 새로운 제안된 질문을 보내는 개체입니다.

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 개체 모델

QnA Maker 클라이언트는 키가 포함된 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) 개체입니다.

클라이언트를 만든 후 [기술 자료](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) 속성을 사용하여 기술 자료를 생성, 관리 및 게시합니다.

JSON 개체를 전송하여 기술 자료를 관리합니다. 즉각적인 작업의 경우 메서드는 일반적으로 상태를 나타내는 JSON 개체를 반환합니다. 장기 실행 작업의 경우 응답은 작업 ID입니다. [요청의 상태](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)를 확인하려면 해당 작업 ID와 함께 [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) 메서드를 호출합니다.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 개체 모델

예측 QnA Maker 클라이언트는 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) 개체입니다. 이 개체는 기술 자료가 게시된 후에 제작 클라이언트 호출(`client.EndpointKeys.GetKeys`)에서 반환된 예측 런타임 키를 포함합니다.

[GenerateAnswer](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions) 메서드를 사용하여 쿼리 런타임에서 답변을 가져옵니다.

## <a name="code-examples"></a>코드 예제

이 코드 조각은 .NET용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [제작 클라이언트 인증](#authenticate-the-client-for-authoring-the-knowledge-base)
* [기술 자료 만들기](#create-a-knowledge-base)
* [기술 자료 업데이트](#update-a-knowledge-base)
* [기술 자료 다운로드](#download-a-knowledge-base)
* [기술 자료 게시](#publish-a-knowledge-base)
* [기술 자료 삭제](#delete-a-knowledge-base)
* [쿼리 런타임 키 가져오기](#get-query-runtime-key)
* [작업의 상태 가져오기](#get-status-of-an-operation)
* [쿼리 런타임 클라이언트 인증](#authenticate-the-runtime-for-generating-an-answer)
* [기술 자료에서 답변 생성](#generate-an-answer-from-the-knowledge-base)



## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>기술 자료 제작을 위한 클라이언트 인증

키를 사용하여 클라이언트 개체를 인스턴스화하고, 이를 리소스와 함께 사용하여 엔드포인트를 구성하고 엔드포인트 및 키를 사용하여 [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet)를 만듭니다. [ServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.rest.serviceclientcredentials?view=azure-dotnet) 개체를 만듭니다.

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠**의 경우 [QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) 개체를 사용합니다.
    * 메타데이터 및 후속 프롬프트를 사용하려면 이 데이터가 개별 QnA 쌍 수준에 추가되기 때문에 편집 컨텍스트를 사용합니다.
* **파일**의 경우 [FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) 개체를 사용합니다. FileDTO에는 파일에 연결하기 위한 파일 이름과 공용 URL이 포함됩니다.
* **URL**의 경우 문자열 목록을 사용하여 공개적으로 사용 가능한 URL을 나타냅니다.

생성 단계에는 기술 자료에 대한 속성도 포함되어 있습니다.
* `defaultAnswerUsedForExtraction` - 응답을 찾을 수 없을 때 반환됩니다.
* `enableHierarchicalExtraction` - 추출된 QnA 쌍 간의 프롬프트 관계를 자동으로 만듭니다.
* `language` - 리소스의 첫 번째 기술 자료를 만들 때 Azure Search 인덱스에서 사용할 언어를 설정합니다.

[CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) 메서드를 호출한 다음, 반환된 작업 ID를 [MonitorOperation](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다.

다음 코드의 마지막 줄은 MonitorOperation의 응답에서 기술 자료 ID를 반환합니다.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=CreateKBMethod&highlight=31)]

기술 자료를 성공적으로 만들려면 위의 코드에서 참조되는 [`MonitorOperation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

기술 자료 ID와 [add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet), [update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet) 및 [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO 개체를 포함하는 [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet)를 [UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) 메서드로 전달하여 기술 자료를 업데이트할 수 있습니다. 성공적으로 업데이트되었는지 확인하려면 [MonitorOperation](#get-status-of-an-operation) 메서드를 사용합니다.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=UpdateKBMethod&highlight=8)]

기술 자료를 성공적으로 업데이트하려면 위의 코드에서 참조되는 [`MonitorOperation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="download-a-knowledge-base"></a>기술 자료 다운로드

[DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) 메서드를 사용하여 [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) 목록으로 데이터베이스를 다운로드할 수 있습니다. 이 메소드의 결과는 파일이 아니기 때문에 **설정** 페이지에서 QnA Maker 포털의 내보내기와 동일하지 _않습니다_.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DownloadKB&highlight=3)]

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) 메서드를 사용하여 기술 자료를 게시할 수 있습니다. 현재 저장되고 학습되며 기술 자료 ID에서 참조되는 모델을 사용하며, 이를 엔드포인트에 게시합니다. 이는 기술 자료를 쿼리하기 위해 필요한 단계입니다.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=PublishKB&highlight=3)]



## <a name="get-query-runtime-key"></a>쿼리 런타임 키 가져오기

기술 자료가 게시된 후에는 런타임 쿼리를 위한 쿼리 런타임 키가 필요합니다. 이는 원래 클라이언트 개체를 만드는 데 사용되는 키와 동일하지 않습니다.

[EndpointKeys](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.endpointkeys.getkeyswithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_EndpointKeys_GetKeysWithHttpMessagesAsync_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) 메서드를 사용하여 [EndpointKeysDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.endpointkeysdto?view=azure-dotnet) 클래스를 가져옵니다.

개체에서 반환된 키 속성 중 하나를 사용하여 기술 자료를 쿼리합니다.

[!code-csharp[Get query runtime key](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GetQueryEndpointKey&highlight=3)]

기술 자료를 쿼리하려면 런타임 키가 필요합니다.

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>응답을 생성하기 위한 런타임 인증

기술 자료를 쿼리하는 [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet)를 만들어 답변을 생성하거나 활성 학습으로부터 학습합니다.

[!code-csharp[Authenticate the runtime](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=AuthorizationQuery)]

QnAMakerRuntimeClient를 사용하여 다음을 수행합니다.
* 기술 자료에서 답변을 가져와서
* [활성 학습](../concepts/active-learning-suggestions.md)에 대한 기술 자료에 새로운 제안 질문을 보냅니다.

## <a name="generate-an-answer-from-the-knowledge-base"></a>기술 자료에서 답변 생성

[RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) 메서드를 사용하여 게시된 기술 자료에서 답변을 생성합니다. 이 메서드는 기술 자료 ID 및 [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet)을 허용합니다. 채팅 봇에서 사용할 [Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) 및 [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet)와 같은 QueryDTO의 추가 속성에 액세스합니다.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=GenerateAnswer&highlight=3)]

이는 기술 자료를 쿼리하는 간단한 예제입니다. 고급 쿼리 시나리오를 이해하려면 [기타 쿼리 예제](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)를 검토하세요.

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) 메서드를 사용하여 기술 자료를 삭제할 수 있습니다.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=DeleteKB&highlight=3)]

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 [작업 ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId)를 사용하여 폴링합니다(다시 시도 논리 사용).

다음 코드 블록의 _loop_ 및 _Task.Delay_는 다시 시도 논리를 시뮬레이션하는 데 사용됩니다. 아래는 사용자의 자체 다시 시도 논리로 바꾸어야 합니다.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/SDK-based-quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/SDK-based-quickstart)에서 확인할 수 있습니다.

---
title: Content Moderator .NET 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 통해 .NET용 Content Moderator 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-dotnet
ms.openlocfilehash: 65465c97f4457756fb376219caeb9cb066a2ec77
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929122"
---
.NET용 Content Moderator 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Content Moderator는 자료에서 모욕적이거나, 위험하거나, 원치 않는 텍스트, 이미지 및 비디오 콘텐츠를 확인하는 인지 서비스입니다. 이러한 자료가 발견되면 서비스가 콘텐츠에 적절한 레이블(플래그)을 적용합니다. 그러면 앱이 플래그가 지정된 콘텐츠를 처리하여 규정을 준수하거나 원하는 사용자 환경을 유지할 수 있습니다.

.NET용 Content Moderator 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [텍스트 조정](#moderate-text)
* [이미지 조정](#moderate-images)
* [검토 만들기](#create-a-review)

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [샘플](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Content Moderator용 리소스를 만듭니다. 다음도 가능합니다.

* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

리소스에서 키를 가져온 후에는 각각 `CONTENT_MODERATOR_SUBSCRIPTION_KEY` 및 `CONTENT_MODERATOR_ENDPOINT`라는 키 및 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 텍스트 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다. 

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `content-moderator-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n content-moderator-quickstart
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

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 다음 `using` 문을 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

**Program** 클래스에서 리소스의 엔드포인트 위치와 키에 대한 변수를 환경 변수로 만듭니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Content Moderator 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

## <a name="object-model"></a>개체 모델

Content Moderator .NET 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|이 클래스는 모든 Content Moderator 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|이 클래스는 성인 콘텐츠, 개인 정보 또는 사람 얼굴에 대한 이미지를 분석하는 기능을 제공합니다.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|이 클래스는 언어, 욕설, 오류 및 개인 정보에 대한 텍스트를 분석하는 기능을 제공합니다.|
|[검토](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|이 클래스는 작업, 사용자 지정 워크플로 및 사용자 검토를 만드는 메서드를 포함하여 검토 API의 기능을 제공합니다.|

## <a name="code-examples"></a>코드 예제


여기에 나와 있는 코드 조각에서는 .NET용 Content Moderator 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [텍스트 조정](#moderate-text)
* [이미지 조정](#moderate-images)
* [검토 만들기](#create-a-review)

## <a name="authenticate-the-client"></a>클라이언트 인증

새 메서드에서 클라이언트 개체를 엔드포인트와 키로 인스턴스화합니다. 모든 시나리오에 다른 클라이언트가 필요한 것은 아니지만, 코드를 체계적으로 유지하는 데 도움이 될 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>텍스트 조정

다음 코드에서는 Content Moderator 클라이언트를 사용하여 텍스트 본문을 분석하고 결과를 콘솔에 출력합니다. **Program** 클래스의 루트에서 다음과 같이 입력 및 출력 파일을 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

그런 다음, 프로젝트의 루트에서 *TextFile.txt* 파일을 추가합니다. 사용자 고유의 텍스트를 이 파일에 추가하거나 다음 샘플 텍스트를 사용합니다.

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

`Main` 메서드에 다음 메서드 호출을 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

그런 다음, **Program** 클래스 내 아무 위치에서 텍스트 조정 메서드를 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>이미지 조정

다음 코드에서는 [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) 개체와 함께 Content Moderator 클라이언트를 사용하여 원격 이미지의 성인 및 외설 콘텐츠를 분석합니다.

> [!NOTE]
> 로컬 이미지의 콘텐츠를 분석할 수도 있습니다. 로컬 이미지에서 작동하는 메서드 및 작업에 대한 [참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_)를 확인하세요.

### <a name="get-sample-images"></a>샘플 이미지 가져오기

다음과 같이 입력 및 출력 파일을 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

그런 다음, 프로젝트의 루트에서 입력 파일 *ImageFiles.txt*를 만듭니다. 이 파일에서 분석할 이미지의 URL을 각 줄에 하나씩 추가합니다. 다음 샘플 이미지를 사용할 수 있습니다.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

입력 및 출력 파일을 `Main` 메서드의 다음 메서드 호출에 전달합니다. 나중에 이 메서드를 정의할 것입니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

### <a name="define-helper-class"></a>도우미 클래스 정의

**Program** 클래스 내에 다음 클래스 정의를 추가합니다. 이 내부 클래스는 이미지 조정 결과를 처리합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>이미지 조정 메서드 정의

다음 메서드는 텍스트 파일의 이미지 URL을 반복하고, **EvaluationData** 인스턴스를 만들고, 이미지의 성인/외설 콘텐츠, 텍스트 및 사람 얼굴을 분석합니다. 그런 다음, 최종 **EvaluationData** 인스턴스를 목록에 추가하고, 반환된 전체 데이터 목록을 콘솔에 기록합니다.

#### <a name="iterate-through-image-urls"></a>이미지 URL 반복

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>콘텐츠 분석

Content Moderator가 차단하는 이미지 특성에 대한 자세한 내용은 [이미지 조정 개념](../../image-moderation-api.md) 가이드를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>파일에 조정 결과 쓰기

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>검토 만들기

Content Moderator .NET 클라이언트 라이브러리를 사용하여 사용자 중재자가 검토할 수 있도록 콘텐츠를 [검토 도구](https://contentmoderator.cognitive.microsoft.com)에 제공할 수 있습니다. 검토 도구에 대한 자세한 내용은 [검토 도구 개념 가이드](../../review-tool-user-guide/human-in-the-loop.md)를 참조하세요.

이 섹션의 메서드는 검토 도구의 웹 포털을 통해 사용자 입력을 받은 후 [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) 클래스를 사용하여 검토를 작성하고, 해당 ID를 검색하고, 해당 세부 정보를 확인합니다. 이 모든 정보는 출력 텍스트 파일에 기록됩니다. 다음과 같이 `Main` 메서드에서 메서드를 호출합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>샘플 이미지 가져오기

**Program** 클래스의 루트에서 다음 배열을 선언합니다. 이 변수는 검토를 작성하는 데 사용할 샘플 이미지를 참조합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>리뷰 자격 증명 가져오기

[검토 도구](https://contentmoderator.cognitive.microsoft.com)에 로그인하고 팀 이름을 검색합니다. 그런 다음, 팀 이름을 **Program** 클래스의 적당한 변수에 할당합니다. 필요에 따라 검토 작업에 대한 업데이트를 받도록 콜백 엔드포인트를 설정할 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>도우미 클래스 정의

**Program** 클래스 내에 다음 클래스 정의를 추가합니다. 이 클래스는 검토 도구에 제출되는 단일 검토 인스턴스를 나타내는 데 사용됩니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>도우미 메서드 정의

**Program** 클래스에 다음 메서드를 추가합니다. 이 메서드는 검토 쿼리 결과를 출력 텍스트 파일에 기록합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>검토 작성 메서드 정의

검토 작성 및 쿼리를 처리하는 메서드를 정의할 준비가 끝났습니다. 새 메서드 **CreateReviews**를 추가하고, 다음 지역 변수를 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>검토 도구에 검토 게시

그런 다음, 단일 일괄 처리에서 특정 샘플 이미지를 반복하고, 메타데이터를 추가하고, 검토 도구로 보내는 다음 코드를 추가합니다. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

API 호출에서 반환된 개체에는 업로드된 각 이미지의 고유 ID 값이 포함되어 있습니다. 다음 코드는 이러한 ID를 구문 분석한 다음, 일괄 처리에서 Content Moderator에 각 이미지의 상태를 쿼리하는 데 사용합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>검토 세부 정보 가져오기

다음 코드를 사용하면 프로그램이 사용자 입력을 대기합니다. 런타임에 이 단계에 도달하면 직접 [검토 도구](https://contentmoderator.cognitive.microsoft.com)로 이동하여 샘플 이미지가 업로드되었는지 확인하고, 샘플 이미지와 상호 작용할 수 있습니다. 검토와 상호 작용하는 방법에 대한 자세한 내용은 [검토 방법 가이드](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)를 참조하세요. 모두 마쳤으면 아무 키를 눌러 프로그램을 계속 진행하고 검토 프로세스 결과를 검색할 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

이 시나리오에서 콜백 엔드포인트를 사용한 경우 다음과 같은 형식의 이벤트를 받습니다.

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>애플리케이션 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Content Moderator .NET 라이브러리를 사용하여 조정 작업을 수행하는 방법을 알아보았습니다. 다음으로, 개념 가이드를 참조하여 이미지 또는 다른 미디어의 조정에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [이미지 조정 개념](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs)에서 확인할 수 있습니다.

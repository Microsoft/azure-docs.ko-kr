---
title: '빠른 시작: 기술 자료, REST, c # QnA Maker 게시'
description: 이 C# REST 기반 빠른 시작은 기술 자료를 게시하고 애플리케이션 또는 채팅 봇에서 호출할 수 있는 엔드포인트를 만듭니다.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-csharp
ms.topic: how-to
ms.openlocfilehash: df8ca85ff0d59817a939936aa6738910bd354996
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267023"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>빠른 시작: C#를 사용하여 QnA Maker 기술 자료 게시

이 REST 기반 빠른 시작에서는 KB(기술 자료)를 프로그래밍 방식으로 게시하는 방법을 안내합니다. 게시는 최신 버전의 기술 자료를 전용 Azure Cognitive Search 인덱스에 푸시하고, 애플리케이션 또는 채팅 봇에서 호출할 수 있는 엔드포인트를 만듭니다.

이 빠른 시작에서 호출하는 QnA Maker API는 다음과 같습니다.
* [게시](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - 이 API는 요청 본문에 어떤 정보도 요구하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 최신 [**Visual Studio Community Edition**](https://www.visualstudio.com/downloads/)
* [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md)가 있어야 합니다. 키와 엔드포인트(리소스 이름 포함)를 검색하려면 Azure Portal에서 리소스에 대해 **빠른 시작**을 선택합니다.
* QnA Maker KB(기술 자료) ID는 아래와 같이 `kbid` 쿼리 문자열 매개 변수의 URL에 있습니다.

    ![QnA Maker 기술 자료 ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    아직 기술 자료가 없는 경우 샘플을 만들어서 빠른 시작: [새 기술 자료 만들기](create-new-kb-csharp.md)에서 사용하면 됩니다.

> [!NOTE]
> 전체 솔루션 파일은 [ **Azure-Samples/qnamaker-csharp** GitHub 리포지토리에서](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/quickstarts/publish-knowledge-base)사용할 수 있습니다.

## <a name="create-knowledge-base-project"></a>기술 자료 프로젝트 만들기

1. Visual Studio 2019 Community 버전을 엽니다.
1. 새 **콘솔 앱 (.Net Core)** 프로젝트를 만들고 프로젝트의 이름을로 `QnaMakerQuickstart` 합니다. 나머지 설정에 대해 기본값을 수락합니다.

## <a name="add-required-dependencies"></a>필요한 종속성 추가

Program.cs의 맨 위에서 단일 using 문을 다음 줄로 바꾸어 프로젝트에 필요한 종속성을 추가합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="dependencies":::

## <a name="add-required-constants"></a>필요한 상수 추가

**Program** 클래스에서 QnA Maker에 액세스하는 데 필요한 상수를 추가합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="constants":::

## <a name="add-the-main-method-to-publish-the-knowledge-base"></a>Main 메서드를 추가하여 기술 자료 게시

필요한 상수 뒤에 다음 코드를 추가합니다. 이 코드는 기술 자료로 질문을 보내기 위한 QnA Maker API에 대한 HTTPS 요청을 수행한 후 답변을 수신합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/QnAMaker/rest/publish-kb.cs" id="post":::

API 호출은 성공적인 게시에 대해 응답 본문에 내용이 없는 204 상태를 반환합니다.

## <a name="build-and-run-the-program"></a>프로그램 빌드 및 실행

프로그램을 빌드하고 실행합니다. 그러면 자동으로 기술 자료를 게시하기 위한 요청을 QnA Maker API에 보낸 다음, 응답이 콘솔 창에 출력됩니다.

기술 자료가 게시되면 클라이언트 애플리케이션 또는 챗봇을 사용하여 엔드포인트에서 쿼리할 수 있습니다.

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

기술 자료가 게시된 후 [답변을 생성할 엔드포인트 URL](./get-answer-from-knowledge-base-csharp.md)이 필요합니다.

> [!div class="nextstepaction"]
> [QnA Maker(V4) REST API 참조](https://go.microsoft.com/fwlink/?linkid=2092179)

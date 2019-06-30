---
title: 'Azure Cosmos DB 디자인 패턴: 소셜 미디어 앱'
description: Azure Cosmos DB 및 기타 Azure 서비스의 저장소 유연성을 활용하여 소셜 네트워크에 대한 디자인 패턴을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 45e27b37ca7a1718674914fbe9203b7dc64475b1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342100"
---
# <a name="going-social-with-azure-cosmos-db"></a>Azure Cosmos DB를 사용하여 소셜 네트워크 디자인

광범위하게 상호 연결된 사회에서 살고 있다는 것은 삶의 어느 시점에서 **소셜 네트워크**의 일부가 된다는 것을 의미합니다. 소셜 네트워크를 사용하여 친구, 동료, 가족 등과 연락하거나, 때로는 공통의 관심사를 가진 사람들과 열정을 공유합니다.

여러분이 엔지니어나 개발자라면 이러한 네트워크가 어떻게 사용자 데이터를 저장하고 상호 연결하는지 궁금하게 생각할 것입니다. 또는 특정 틈새 시장을 위한 새로운 소셜 네트워크를 만들거나 설계하는 일을 맡을 수도 있습니다. 특히 이 모든 데이터가 어떻게 저장되는지가 매우 궁금할 때 더욱 그렇습니다.

사진, 동영상 또는 음악과 같은 관련 미디어와 함께 문서를 게시할 수 있는 새롭고 참신한 소셜 네트워크를 만든다고 가정해 보겠습니다. 사용자는 게시물에 의견을 달고 평점을 매길 수 있습니다. 기본 웹 사이트 방문 페이지에는 사용자가 보고 상호 작용하는 게시물 피드가 있을 것입니다. 처음에는 이 방법이 복잡하지 않게 보이겠지만, 간단히 하기 위해 해당 위치에서 멈추겠습니다. (관계에 의해 영향을 받는 사용자 지정 사용자 피드를 분석할 수 있지만 이 문서의 목적에 맞지 않습니다.)

그렇다면 이 데이터는 어디에 어떻게 저장될까요?

아마도 여러분은 SQL 데이터베이스에 대한 경험이 있거나 [데이터의 관계형 모델링](https://en.wikipedia.org/wiki/Relational_model)에 대한 개념을 알고 있을 것입니다. 따라서 다음과 같은 그림을 그릴 수 있을 것입니다.

![상대 관계형 모델을 보여 주는 다이어그램](./media/social-media-apps/social-media-apps-sql.png)

완전히 정규화되고 세련된 데이터 구조로, 확장되지 않습니다.

저는 평생을 SQL Database와 함께 했으니 걱정하실 필요가 없습니다. 이 데이터베이스는 유용하지만, 패턴, 사례 및 소프트웨어 플랫폼처럼 모든 시나리오에 완벽하지는 않습니다.

이 시나리오에서 SQL이 최선의 선택이 아닌 이유는 무엇일까요? 단일 게시물의 구조를 살펴보겠습니다. 웹 사이트나 애플리케이션에 이 게시물을 나타내려면 테이블 조인 8개(!)로 쿼리를 수행해야 합니다. 단지 게시물 하나를 나타내기 위해서 말입니다. 이제 동적으로 로드되어 화면에 나타나는 게시물 스트림을 그려보면 어디를 향하고 있는지 볼 수 있습니다.

이 많은 조인으로 수천 개의 쿼리를 해결할 정도의 엄청난 SQL 인스턴스를 사용하여 콘텐츠를 제공할 수 있습니다. 그렇지만 보다 간단한 솔루션이 있는데 그래야 하는 이유가 있을까요?

## <a name="the-nosql-road"></a>NoSQL

이 문서에서는 Azure의 NoSQL 데이터베이스 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)를 사용하여 비용 효과적으로 소셜 플랫폼의 데이터를 모델링하는 과정을 안내합니다. 또한 [Gremlin API](../cosmos-db/graph-introduction.md) 등의 다른 Azure Cosmos DB 기능을 사용하는 방법도 알려줍니다. [NoSQL](https://en.wikipedia.org/wiki/NoSQL) 접근 방식을 사용하여 데이터를 JSON 형식으로 저장하고 [역정규화](https://en.wikipedia.org/wiki/Denormalization)를 적용하면 이전의 복잡한 게시물을 단일 [문서](https://en.wikipedia.org/wiki/Document-oriented_database)로 변환할 수 있습니다.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

또한 조인 없이 단일 쿼리로 이를 실현할 수 있습니다. 이 쿼리는 훨씬 간단하고 쉽고 저렴하며, 보다 적은 리소스로 더 나은 결과를 얻을 수 있는 방법입니다.

Azure Cosmos DB는 모든 속성이 자체 자동 인덱싱을 통해 인덱싱되도록 합니다. 자동 인덱싱은 [사용자 지정](index-policy.md)할 수도 있습니다. 스키마 없는 접근 방식을 사용하여 다양한 동적 구조로 문서를 저장할 수 있습니다. 내일 갑자기 게시물에 범주 또는 관련 해시 태그 목록을 포함하고 싶을 수 있습니다. Cosmos DB는 사용자의 추가 작업 없이도, 추가된 특성을 사용하여 새 문서를 처리합니다.

부모 속성을 사용하여 게시물에 대한 의견을 다른 게시물로 처리할 수 있습니다. (이 경우 개체 매핑이 간소화됩니다.)

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

또한 모든 소셜 상호 작용을 별도의 개체에 카운터로 저장할 수 있습니다.

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

피드를 만들려면 주어진 연관성 순으로 게시물 ID 목록을 유지할 수 있는 문서를 만들기만 하면 됩니다.

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

게시물이 만든 날짜별로 정렬되는 "최신" 스트림이 있을 수 있습니다. 또는 지난 24시간 동안 더 많은 '좋아요'가 달린 게시물을 포함하는 "인기" 스트림이 있을 수 있습니다. 팔로워와 관심 분야와 같은 논리에 따라 각 사용자의 사용자 지정 스트림을 구현할 수도 있습니다. 그래도 게시물 목록인 것은 마찬가지입니다. 이러한 목록을 빌드하는 방법이 중요하지만 읽기 성능이 그대로 유지되어야 합니다. Cosmos DB를 사용 하 여 단일 쿼리를 실행 하는 이러한 목록 중 하나를 획득 하 되 면 합니다 [키워드의](sql-query-keywords.md#in) 페이지의 게시물을 한 번에 가져오려고 합니다.

피드 스트림은 [Azure App Services](https://azure.microsoft.com/services/app-service/)의 백그라운드 프로세스인 [Webjobs](../app-service/webjobs-create.md)를 사용하여 빌드할 수 있습니다. 게시물을 만든 후 [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)를 통해 트리거되고 고유한 사용자 지정 논리를 기반으로 스트림 내에 게시물 전파를 구현하는 [Azure Storage](https://azure.microsoft.com/services/storage/), [큐](../storage/queues/storage-dotnet-how-to-use-queues.md) 및 Webjobs를 사용하여 백그라운드 처리를 트리거할 수 있습니다.

이 동일한 기술을 사용하여 궁극적으로 일관된 환경을 만들어 게시물에 대한 평점 및 좋아요를 지연된 방식으로 처리할 수 있습니다.

팔로워는 더 복잡합니다. Cosmos DB에는 문서 크기 제한이 있으며 크기가 큰 문서의 읽기/쓰기는 애플리케이션의 확장성에 영향을 줄 수 있습니다. 다음 구조를 사용하여 팔로워를 저장하는 방법을 고려할 수 있습니다.

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

이 구조는 수천 명의 팔로워가 있는 사용자에게 적절할 수 있습니다. 그렇지만 일부 유명인이 랭크에 조인하면 결국 이 접근 방식은 큰 문서에 도달해 문서 크기 용량이 부족하게 될 수 있습니다.

이 문제를 해결하기 위해 혼합된 접근 방식을 사용할 수 있습니다. 사용자 통계 문서의 일부로 팔로워의 수를 저장할 수 있습니다.

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md)를 사용하여 팔로워의 실제 그래프를 저장하고, 각 사용자에 대한 [꼭짓점](http://mathworld.wolfram.com/GraphVertex.html)을 만들고, "A가 B를 팔로우" 관계를 유지하는 [가장자리](http://mathworld.wolfram.com/GraphEdge.html)를 만들 수 있습니다. Gremlin API를 사용하여 특정 사용자의 팔로워를 가져오고 공통점이 있는 사람들을 제안하는 좀 더 복잡한 쿼리를 만들 수 있습니다. 그래프에 사람들이 좋아하는 콘텐츠 범주를 추가하면 스마트 콘텐츠 검색이 포함된 환경을 만들어서 팔로우하는 사람들이 좋아하는 제안하거나 공통점이 많을 것 같은 사람을 찾을 수 있습니다.

사용자 통계 문서는 여전히 UI 또는 빠른 프로필 미리 보기에서 카드를 만드는 데 사용할 수 있습니다.

## <a name="the-ladder-pattern-and-data-duplication"></a>“사다리” 패턴 및 데이터 중복

게시물을 참조하는 JSON 문서에서 볼 수 있듯이 하나의 사용자가 여러 번 발생합니다. 예상할 수 있는 것처럼 이러한 중복은 이러한 역정규화가 주어진 경우 사용자를 나타내는 정보가 여러 곳에 표시될 수 있음을 의미합니다.

쿼리를 더 빠르게 처리하기 위해 데이터 중복을 허용하게 됩니다. 그 부작용으로 인한 문제는 일부 작업으로 인해 사용자의 데이터가 변경된 경우 해당 사용자가 지금까지 수행한 모든 활동을 찾아서 모두 업데이트해야 한다는 점입니다. 실용적으로 들리지 않죠, 그렇죠?

각 활동에 대해 애플리케이션에 표시하는 사용자의 주요 특성을 식별하여 문제를 해결할 수 있습니다. 게시물을 애플리케이션에 시각적으로 표시하고 만든 사람의 이름과 사진만 표시했을 뿐인데 “createdBy” 특성에 해당 사용자의 모든 데이터가 저장되는 이유는 무엇일까요? 각 의견에 대해 사용자의 사진만 표시하면 사용자의 나머지 정보는 필요 없습니다. 바로 여기에 “사다리 패턴”이 적용됩니다.

다음 사용자 정보를 예로 들어 보겠습니다.

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

이 정보를 보면 중요한 정보와 중요하지 않은 정보를 신속하게 감지할 수 있으므로 다음과 같은 “사다리”가 만들어집니다.

![사다리 패턴의 다이어그램](./media/social-media-apps/social-media-apps-ladder.png)

가장 작은 단계는 사용자를 식별하는 정보의 최소 조각인 UserChunk로서, 데이터 중복에 사용됩니다. 중복된 데이터의 크기를 “표시”할 정보로만 줄이면 대량 업데이트의 가능성이 줄어듭니다.

중간 단계를 사용자라고 합니다. 이는 Cosmos DB에 대한 대부분의 성능 종속 쿼리에 사용되고 가장 자주 액세스되며 중요한 전체 데이터입니다. 여기에는 UserChunk가 나타내는 정보가 포함됩니다.

가장 큰 단계는 Extended User입니다. 여기에는 중요한 사용자 정보와 빠르게 읽을 필요가 없거나 일시적으로 사용되는(예: 로그인 프로세스) 기타 데이터가 포함됩니다. 이 데이터를 Cosmos DB 외부, Azure SQL Database 또는 Azure Storage 테이블에 저장할 수 있습니다.

사용자를 분할하고 심지어 이 정보를 여러 곳에 저장하는 이유는 무엇일까요? 성능 면에서 문서가 클수록 쿼리 비용이 많이 들기 때문입니다. 소셜 네트워크에 대한 모든 성능 종속 쿼리를 수행하려면 문서에 적절한 정보만 포함하여 작게 유지합니다. 사용 현황 분석 및 빅 데이터 이니셔티브를 위한 전체 프로필 편집, 로그인 및 데이터 마이닝과 같은 일시적인 시나리오를 위해 기타 추가 정보를 저장합니다. 데이터 마이닝을 위한 데이터 수집은 Azure SQL Database에서 실행되므로 더 느리지만 실제로는 신경 쓸 필요가 없습니다. 그렇지만 사용자의 경험이 짧고 풍부하지 못할 경우에는 문제가 될 수 있습니다. Cosmos DB에 저장된 사용자는 다음 코드와 같이 표시됩니다.

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

그리고 게시물은 다음과 유사합니다.

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

청크 특성이 영향을 받는 편집이 수행될 경우 영향을 받는 문서를 쉽게 찾을 수 있습니다. 인덱싱된 특성을 가리키는 쿼리(예: `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`)만 사용한 후 청크를 업데이트합니다.

## <a name="the-search-box"></a>검색 상자

다행히 사용자는 많은 콘텐츠를 생성합니다. 우리는 콘텐츠 스트림에 없을 수 있는 콘텐츠를 검색하고 찾을 수 있는 기능을 제공할 수 있어야 합니다. 만든 사람을 추적하지 않거나 6개월 전에 게시한 오래된 게시물을 찾으려고 할 수 있기 때문입니다.

Azure Cosmos DB를 사용하기 때문에 코드를 전혀 입력하지 않고도 [Azure Search](https://azure.microsoft.com/services/search/)를 통해 몇 분 이내에 검색 프로세스 및 UI 이외의 검색 엔진을 쉽게 구현할 수 있습니다.

이 프로세스는 왜 그렇게 쉬울까요?

Azure Search는 데이터 리포지토리에 후크되는 백그라운드 프로세스인 [인덱서](https://msdn.microsoft.com/library/azure/dn946891.aspx)를 호출하여 인덱스에서 개체를 자동으로 추가, 업데이트 또는 제거하는 기능을 구현하기 때문입니다. Azure Search는 [Azure SQL Database 인덱서](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure Blob 인덱서](../search/search-howto-indexing-azure-blob-storage.md) 그리고 다행히도 [Azure Cosmos DB 인덱서](../search/search-howto-index-documentdb.md)를 지원합니다. Cosmos DB에서 Azure Search로 정보를 전환하는 것은 간단합니다. 두 기술은 모두 정보를 JSON 형식으로 저장하므로 [인덱스를 만들고](../search/search-create-index-portal.md) 인덱싱할 문서에서 특성을 매핑하면 됩니다. 끝났습니다. 데이터의 크기에 따라, 모든 콘텐츠는 클라우드 인프라에서 최상의 Search-as-a-Service 솔루션을 통해 몇 분 내에 검색될 수 있습니다.

Azure Search에 대한 자세한 내용은 [Hitchhiker의 검색 가이드](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)를 참조하세요.

## <a name="the-underlying-knowledge"></a>기본 지식

매일 증가하는 이 모든 콘텐츠를 저장한 후 다음의 정보를 확인할 수 있습니다. 사용자의 이 모든 정보 스트림으로 무엇을 수행할 수 있나요?

대답은 간단합니다. 작업에 적용하고 학습합니다.

그렇다면 무엇을 배울 수 있을까요? 몇 가지 쉬운 예를 들면 [감정 분석](https://en.wikipedia.org/wiki/Sentiment_analysis), 사용자의 선호도에 따른 콘텐츠 추천 또는 소셜 네트워크에서 게시된 모든 콘텐츠가 가족에게 안전하도록 보장하는 자동화된 콘텐츠 중재자 등이 있습니다.

이제 간단한 데이터베이스 및 파일에서 이러한 패턴과 정보를 추출하려면 수학 박사가 필요하다고 생각하겠지만 그렇지 않습니다.

[Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx)의 일부인 [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)은 완전히 관리되는 클라우드 서비스로서, 간단한 끌어서 놓기 인터페이스에서 알고리즘을 사용하여 워크플로를 만들거나, [R](https://en.wikipedia.org/wiki/R_\(programming_language\))에서 사용자 고유의 알고리즘을 코딩하거나, 이미 빌드되고 즉시 사용 가능한 API(예: [텍스트 분석](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator 또는 [추천](https://gallery.azure.ai/Solution/Recommendations-Solution))를 사용할 수 있도록 해줍니다.

이러한 Machine Learning 시나리오를 위해 [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)를 사용하여 다양한 원본에서 정보를 수집할 수 있습니다. [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/)을 사용하여 정보를 처리하고, Azure Machine Learning에서 처리할 수 있는 출력을 생성할 수도 있습니다.

또 다른 옵션은 [Azure Cognitive Services](https://www.microsoft.com/cognitive-services)를 사용하여 사용자의 콘텐츠를 분석하는 것입니다. [텍스트 분석 API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)로 무엇을 작성하는지 분석하여 콘텐츠를 보다 정확하게 이해할 수 있을 뿐만 아니라, 원치 않는 콘텐츠 또는 성인물 콘텐츠를 검색하고 [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api)를 사용하여 그에 따라 동작할 수 있습니다. Cognitive Services는 사용하기 위해 Machine Learning의 지식이 필요하지 않은 많은 기본 제공 솔루션을 포함합니다.

## <a name="a-planet-scale-social-experience"></a>전 세계적인 규모의 소셜 환경

마지막으로 해결해야 하는 중요한 사항은 **확장성**입니다. 아키텍처를 디자인할 때 각 구성 요소는 자체적으로 크기가 조정될 수 있어야 합니다. 결국 더 많은 데이터를 처리해야 하거나 더 큰 지리적 범위를 포괄하려고 할 수 있습니다. 다행스럽게도 Cosmos DB를 사용하여 **턴키 환경**에서 이러한 두 가지 작업을 모두 수행할 수 있습니다.

Cosmos DB는 기본적으로 동적 분할을 지원합니다. 문서에 특성으로 정의되는 지정된 **파티션 키**를 기준으로 파티션을 자동으로 만듭니다. 올바른 파티션 키를 정의하는 작업은 디자인 타임에 수행해야 합니다. 자세한 내용은 [Azure Cosmos DB 분할](partitioning-overview.md)을 참조하세요.

소셜 환경의 경우 쿼리 및 쓰기 방식에 따라 분할 전략을 조정해야 합니다. (예를 들어, 동일한 파티션 내의 읽기는 적절하지만, 쓰기를 여러 파티션에 분산하는 "핫 스폿"은 피해야 합니다.) 임시 키(일/월/주) 기준, 콘텐츠 범주, 지리적 지역 또는 사용자별 파티션을 옵션으로 사용할 수 있습니다. 실제로 소셜 환경에서의 데이터 쿼리 방식 및 표시 방식에 따라 옵션이 결정됩니다.

Cosmos DB는 모든 파티션에 투명하게 쿼리([집계](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/) 포함)를 실행하므로 데이터의 증가에 따라 논리를 추가할 필요가 없습니다.

시간이 지나면 결국 트래픽이 증가하고 리소스 사용([RU](request-units.md) 또는 요청 단위)도 증가합니다. 사용자 기반이 커지면 좀 더 자주 읽고 쓰게 됩니다. 사용자 기반은 더 많은 콘텐츠를 만들고 읽기 시작합니다. 따라서 **처리량 크기 조정** 기능이 반드시 필요합니다. RU를 늘리는 것은 쉽습니다. Azure Portal에서 몇 번 클릭하거나 [API를 통해 명령을 실행](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer)하여 수행할 수 있습니다.

![파티션 키 확장 및 정의](./media/social-media-apps/social-media-apps-scaling.png)

상황이 계속 좋아질 수 있습니다. 다른 지역, 국가 또는 대륙의 사용자들이 여러분의 플랫폼을 알게 되고 사용하게 된다고 가정해보세요. 대단한 일이지요.

그렇지만 잠시 기다리세요. 사용자가 해당 플랫폼을 최적 상태로 사용하고 있지 않다는 것을 알게 될 것입니다. 사용자들이 운영 지역에서 멀리 떨어져 있어서 대기 시간이 상당히 길 수 있습니다. 그렇다고 사용자들이 중단하는 것을 원하는 것은 아닐 것입니다. **글로벌 도달률을 확장**하는 쉬운 방법이 있다면 얼마나 좋을까요. 방법이 있습니다!

Cosmos DB를 사용하면 몇 번의 클릭으로 투명하게 [데이터를 전역으로 복제](../cosmos-db/tutorial-global-distribution-sql-api.md)하고 [클라이언트 코드](../cosmos-db/tutorial-global-distribution-sql-api.md)에서 사용 가능한 지역 중 하나를 자동으로 선택할 수 있습니다. 즉, 이 프로세스에서는 [여러 장애 조치(failover)
지역](high-availability.md)을 사용할 수 있습니다.

전역적으로 데이터를 복제하는 경우 클라이언트에서 활용할 수 있는지 확인합니다. 웹 프런트 엔드를 사용하거나 모바일 클라이언트의 API에 액세스하는 경우 확장된 글로벌 범위를 지원하는 성능 구성을 사용하여 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)를 배포하고 원하는 모든 지역에서 Azure App Service를 복제할 수 있습니다. 클라이언트가 프런트 엔드 또는 API에 액세스하는 경우 가장 가까운 App Service에 라우팅되고 따라서 로컬 Cosmos DB 복제본에 연결됩니다.

![소셜 플랫폼에 전역적 적용 범위 추가](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>결론

이 문서에서는 저렴한 비용 서비스를 사용하여 Azure에서 소셜 네트워크를 완벽하게 만들 수 있는 대안을 살펴보았습니다. 그 결과 다중 계층 스토리지 솔루션 및 "사다리"라는 데이터 배포를 사용하는 것이 권장됩니다.

![소셜 네트워킹에 대한 Azure 서비스 간 상호 작용의 다이어그램](./media/social-media-apps/social-media-apps-azure-solution.png)

이러한 종류의 시나리오에 대한 묘책은 없습니다. 이는 뛰어난 환경을 빌드할 수 있도록 해주는 유용한 서비스의 조합으로 생성되는 시너지입니다. 예를 들어 뛰어난 소셜 애플리케이션을 제공하는 Azure Cosmos DB의 속도와 자유로움, Azure Search와 같은 최고 수준의 검색 솔루션에 숨은 인텔리전스, 언어에 관계없는 애플리케이션뿐 아니라 강력한 백그라운드 프로세스를 호스트하는 Azure App Services의 유연성, 방대한 양의 데이터를 저장하기 위한 확장 가능한 Azure Storage 및 Azure SQL Database, 프로세스에 대한 피드백을 제공할 수 있는 지식과 인텔리전스를 만들고 올바른 사용자에게 올바른 콘텐츠를 제공하도록 도와주는 Azure Machine Learning의 분석 기능 등이 조합된 결과입니다.

## <a name="next-steps"></a>다음 단계

Cosmos DB의 사용 사례에 대한 자세한 내용은 [일반적인 Cosmos DB 사용 사례](use-cases.md)를 참조하세요.

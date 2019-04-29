---
title: Azure Stream Analytics를 사용하여 실시간 Twitter 감정 분석
description: 이 아티클에서는 실시간 Twitter 감정 분석에 대해 Stream Analytics를 사용하는 방법을 설명합니다. 이벤트 생성부터 라이브 대시보드의 데이터에 이르는 단계별 지침이 포함되어 있습니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: abb2a89f41340e8e2e26fa36cc20b790341618d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60763360"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics에서 실시간 Twitter 감정 분석

> [!IMPORTANT] 
> Twitter 애플리케이션 만들기는 [apps.twitter.com](https://apps.twitter.com/)을 통해 더 이상 제공되지 않습니다. 이 자습서는 새 Twitter API를 포함하도록 업데이트 중입니다.

실시간 Twitter 이벤트를 Azure Event Hubs로 가져와서 소셜 미디어 분석을 위한 감정 분석 솔루션을 구축하는 방법을 알아봅니다. 그런 다음 Azure Stream Analytics 쿼리를 작성하여 데이터를 분석하고 나중에 사용할 수 있도록 결과를 저장하거나 대시보드 및 [Power BI](https://powerbi.com/)를 사용하여 실시간으로 통찰력 있는 정보를 제공할 수 있습니다.

소셜 미디어 분석 도구는 조직에서 추세 항목을 이해하는 데 도움이 됩니다. 추세 항목은 소셜 미디어에 많은 게시물을 발생시키는 주제 및 입장입니다. *의견 마이닝*이라는 감정 분석은 소셜 미디어 분석 도구를 사용하여 제품, 아이디어 등에 대한 자세를 결정합니다. 

실시간 Twitter 추세 분석은 분석 도구의 아주 좋은 예입니다. 특정 키워드(해시태그)를 수신 대기하고 피드에 대한 감정 분석을 개발할 수 있는 해시 태그 구독 모델을 사용하기 때문입니다.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>시나리오: 소셜 미디어 실시간 감정 분석

뉴스 미디어 웹 사이트를 보유하는 기업은 독자와 직접적으로 관련이 있는 사이트 콘텐츠를 부각시켜 경쟁 우위를 확보하는 데 관심이 있습니다. 이러한 기업은 Twitter 데이터에 대해 실시간으로 감정을 분석하여 독자와 관련된 항목에 대한 소셜 미디어 정보를 사용합니다.

Twitter에서 실시간으로 추세를 분석할 토픽을 식별하기 위해 기업에서는 주요 토픽에 대한 트윗 볼륨 및 감정에 대한 실시간 분석이 필요합니다. 즉, 이 소셜 미디어 피드를 기반으로 하는 감정 분석 엔진이 필요합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서에서는 Twitter에 연결하고 특정 해시태그(설정 가능)가 있는 트윗을 찾는 클라이언트 애플리케이션을 사용합니다. 애플리케이션을 실행하고 Azure Streaming Analytics를 사용하여 트윗을 분석하려면 다음이 필요합니다.

* Azure 구독
* Twitter 계정 
* Twitter 애플리케이션 및 해당 애플리케이션에 대한 [OAuth 액세스 토큰](https://dev.twitter.com/oauth/overview/application-owner-access-tokens). 나중에 Twitter 애플리케이션을 만드는 방법에 대한 대략적인 지침을 제공합니다.
* Twitter 피드를 읽어 오는 TwitterWPFClient 애플리케이션. 이 애플리케이션을 가져오려면 GitHub에서 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 파일을 다운로드한 후 컴퓨터의 폴더로 패키지의 압축을 풉니다. 소스 코드를 확인하고 디버거에서 애플리케이션을 실행하려면 [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient)에서 소스 코드를 가져올 수 있습니다. 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Streaming Analytics 입력을 위한 이벤트 허브 만들기

애플리케이션 예제에서 이벤트를 생성하여 Azure Event Hub에 푸시합니다. Azure Event Hubs는 Stream Analytics을 위해 이벤트를 수집하는 기본 방법입니다. 자세한 내용은 [Azure Event Hubs 설명서](../event-hubs/event-hubs-what-is-event-hubs.md)를 참조하세요.


### <a name="create-an-event-hub-namespace-and-event-hub"></a>이벤트 허브 네임스페이스 및 이벤트 허브 만들기
이 절차에서는 이벤트 허브 네임스페이스를 먼저 만든 후 이벤트 허브를 해당 네임스페이스에 추가합니다. 이벤트 허브 네임스페이스는 관련된 이벤트 버스 인스턴스를 논리적으로 그룹화하는 데 사용됩니다. 

1. Azure Portal에 로그인하고 **리소스 만들기** > **사물 인터넷** > **이벤트 허브**를 클릭합니다. 

2. **네임스페이스 만들기** 블레이드에서 네임스페이스 이름을 입력합니다(예: `<yourname>-socialtwitter-eh-ns`). 네임스페이스에 어떤 이름이든 사용할 수 있지만 이름은 URL에 대해 유효해야 하며 Azure 전체에서 고유해야 합니다. 
    
3. 구독을 선택하고 리소스 그룹을 만들거나 선택한 후 **만들기**를 클릭합니다. 

    ![이벤트 허브 네임스페이스 만들기](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. 네임스페이스에서 배포를 완료했으면 Azure 리소스 목록에서 이벤트 허브 네임스페이스를 찾습니다. 

5. 새 네임스페이스를 클릭하고 네임스페이스 블레이드에서 **+&nbsp;이벤트 허브**를 클릭합니다. 

    ![새 이벤트 허브를 만들기 위한 이벤트 허브 추가 단추](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. 새 이벤트 허브 이름을 `socialtwitter-eh`로 지정합니다. 다른 이름을 사용할 수 있습니다. 이 경우 나중에 이름이 필요하기 때문에 메모해 둡니다. 이벤트 허브에 다른 옵션을 설정하지 않아도 됩니다.

    ![새 이벤트 허브를 만들기 위한 블레이드](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. **만들기**를 클릭합니다.


### <a name="grant-access-to-the-event-hub"></a>이벤트 허브에 대한 액세스 권한 부여

프로세스에서 이벤트 허브로 데이터를 보낼 수 있으려면 이벤트 허브에 적절한 액세스 권한을 허용하는 정책이 있어야 합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1.  이벤트 네임스페이스 블레이드에서 **Event Hubs**를 클릭하고 새 이벤트 허브의 이름을 클릭합니다.

2.  이벤트 허브 블레이드에서 **공유 액세스 정책**을 클릭한 후 **+&nbsp;추가**를 클릭합니다.

    >[!NOTE]
    >이벤트 허브 네임스페이스가 아니라 이벤트 허브로 작업하고 있는지 확인합니다.

3.  **클레임**에 대해 `socialtwitter-access`라는 이름의 정책을 추가하고 **관리**를 선택합니다.

    ![새 이벤트 허브 액세스 정책을 만들기 위한 블레이드](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  **만들기**를 클릭합니다.

5.  정책이 배포되었으면 공유 액세스 정책 목록에서 클릭합니다.

6.  **CONNECTION STRING-PRIMARY KEY**로 레이블이 지정된 상자를 찾고 연결 문자열 옆의 복사 단추를 클릭합니다. 
    
    ![액세스 정책에서 기본 연결 문자열 키 복사](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  연결 문자열을 텍스트 편집기에 붙여넣습니다. 다음 섹션에서 몇 가지 편집을 수행한 후 이 연결 문자열이 필요합니다.

    연결 문자열은 다음과 같습니다.

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    연결 문자열에는 세미콜론으로 구분된 여러 키-값 쌍이 포함되어 있습니다(`Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` 및 `EntityPath`).  

    > [!NOTE]
    > 보안을 위해 예제에 있는 연결 문자열의 일부가 제거되었습니다.

8.  텍스트 편집기에서 연결 문자열의 `EntityPath` 쌍을 제거합니다(앞에 나오는 세미콜론을 반드시 제거). 작업을 완료한 후 연결 문자열은 다음과 같습니다.

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Twitter 클라이언트 애플리케이션 구성 및 시작
클라이언트 애플리케이션이 Twitter에서 트윗 이벤트를 직접 가져옵니다. 이렇게 하려면 Twitter Streaming API를 호출할 수 있는 권한이 필요합니다. 해당 사용 권한을 구성하려면 고유한 자격 증명(예: OAuth 토큰)을 생성하는 애플리케이션을 Twitter에서 만듭니다. 그런 다음 API를 호출할 때 이러한 자격 증명을 사용하도록 클라이언트 애플리케이션을 구성할 수 있습니다. 

### <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기
이 자습서에 사용할 수 있는 Twitter 애플리케이션이 아직 없는 경우 만들 수 있습니다. Twitter 계정이 이미 있어야 합니다.

> [!NOTE]
> Twitter에서 애플리케이션을 만들고 키, 비밀 및 토큰을 가져오는 정확한 프로세스는 변경될 수 있습니다. 이러한 지침이 Twitter 사이트에 표시되는 내용과 일치하지 않으면 Twitter 개발자 설명서를 참조하세요.

1. [Twitter 애플리케이션 관리 페이지](https://apps.twitter.com/)로 이동합니다. 

2. 새 애플리케이션을 만듭니다. 

   * 웹 사이트 URL에 대한 유효한 URL을 지정합니다. 라이브 사이트일 필요는 없습니다. (`localhost`만 지정할 수는 없음)
   * 콜백 필드는 비워 둡니다. 이 자습서에 사용할 클라이언트 애플리케이션에는 콜백이 필요하지 않습니다.

     ![Twitter에서 애플리케이션 만들기](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. 필요에 따라 애플리케이션 권한을 읽기 전용으로 변경합니다.

4. 애플리케이션이 만들어지면 **키 및 액세스 토큰** 페이지로 이동합니다.

5. 액세스 토큰 및 액세스 토큰 암호를 생성하는 단추를 클릭합니다.

다음 절차에 필요하기 때문에 이 정보를 보관해 두세요.

>[!NOTE]
>Twitter 애플리케이션에 대한 키 및 비밀은 Twitter 계정에 대한 액세스를 제공합니다. 이 정보는 Twitter 암호처럼 중요하게 취급합니다. 예를 들어 이 정보를 다른 사람에게 제공하는 애플리케이션에 포함하지 마세요. 


### <a name="configure-the-client-application"></a>클라이언트 애플리케이션 구성
Microsoft에서는 특정 항목 집합에 대한 트윗 이벤트를 수집하기 위해 [Twitter의 스트리밍 API](https://dev.twitter.com/streaming/overview)를 사용하여 Twitter 데이터에 연결하는 클라이언트 애플리케이션을 만들었습니다. 애플리케이션에서는 각 트윗에 다음 감정을 할당하는 [Sentiment140](http://help.sentiment140.com/) 오픈 소스 도구를 사용합니다.

* 0 = 부정적
* 2 = 중립
* 4 = 긍정적

트윗 이벤트에 감정 값이 할당된 후에는 이전에 만든 이벤트 허브에 푸시됩니다.

애플리케이션이 실행되기 전에 Twitter 키 및 이벤트 허브 연결 문자열과 같은 사용자의 특정 정보가 필요합니다. 다음과 같은 방법으로 구성 정보를 제공할 수 있습니다.

* 애플리케이션을 실행한 다음 애플리케이션의 UI를 사용하여 키, 비밀 및 연결 문자열을 입력합니다. 이 작업을 수행하는 경우 현재 세션에 대한 구성 정보가 사용되지만 저장되지는 않습니다.
* 애플리케이션의 .config 파일을 편집하고 여기서 값을 설정합니다. 이 방법은 구성 정보를 유지하지만 잠재적으로 중요한 정보는 컴퓨터에 일반 텍스트에 저장됨을 의미합니다.

다음 절차에서는 두 가지 방법을 모두 설명합니다. 

1. 필수 조건에 나와 있는 것처럼 [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) 애플리케이션을 다운로드하여 압축을 해제해야 합니다.

2. 실행시간(현재 세션에 대해서만)에 값을 설정하려면 `TwitterWPFClient.exe` 애플리케이션을 실행합니다. 애플리케이션에 메시지가 표시되면 다음 값을 입력합니다.

    * Twitter 사용자 키(API 키)
    * Twitter 사용자 암호(API 암호)
    * Twitter 액세스 토큰
    * Twitter 액세스 토큰 암호
    * 이전에 저장한 연결 문자열 정보. `EntityPath` 키-값 쌍을 제거한 연결 문자열을 사용해야 합니다.
    * 감정을 확인하려는 Twitter 키워드입니다.

   ![TwitterWpfClient 애플리케이션 실행 중, 가려진 설정 표시](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. 값을 영구적으로 설정하려면 텍스트 편집기를 사용하여 TwitterWpfClient.exe.config 파일을 엽니다. 그런 다음 `<appSettings>` 요소에서 다음을 수행합니다.

   * `oauth_consumer_key`를 Twitter 사용자 키(API 키)로 설정합니다. 
   * `oauth_consumer_secret`을 Twitter 사용자 암호(API 암호)로 설정합니다.
   * `oauth_token`을 Twitter 액세스 토큰으로 설정합니다.
   * `oauth_token_secret`을 Twitter 액세스 토큰 암호로 설정합니다.

     뒷부분의 `<appSettings>` 요소에서 다음과 같이 변경합니다.

   * `EventHubName`을 이벤트 허브 이름으로 설정합니다(즉, 엔터티 경로 값).
   * `EventHubNameConnectionString`을 연결 문자열로 설정합니다. `EntityPath` 키-값 쌍을 제거한 연결 문자열을 사용해야 합니다.

     `<appSettings>` 섹션은 다음 예제와 유사합니다. (이해를 돕고 보안을 위해 일부 줄을 래핑하고 일부 문자는 제거했습니다.)

     ![텍스트 편집기에서 TwitterWpfClient 애플리케이션 구성 파일, Twitter 키 및 비밀, 이벤트 허브 연결 문자열 정보 표시](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. 애플리케이션을 아직 시작하지 않은 경우 지금 TwitterWpfClient.exe를 실행합니다. 

5. 녹색 시작 단추를 선택하여 소셜 정서를 수집합니다. **CreatedAt**, **Topic** 및 **SentimentScore** 값이 이벤트 허브로 전송 중인 트윗 이벤트가 표시됩니다.

    ![TwitterWpfClient 애플리케이션 실행 중, 트윗 목록 표시](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >오류가 표시되고 창 하단에 트윗 스트림이 표시되지 않으면 키 및 비밀을 두 번 클릭합니다. 또한 연결 문자열을 확인합니다(`EntityPath` 키 및 값이 포함되지 않는지 확인).


## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

이제 Twitter에서 실시간으로 트윗 이벤트가 스트리밍되며 이러한 이벤트를 실시간으로 분석하도록 Stream Analytics 작업을 설정할 수 있습니다.

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 차례로 클릭합니다.

2. 작업 이름을 `socialtwitter-sa-job`으로 지정하고 구독, 리소스 그룹 및 위치를 지정합니다.

    최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.

    ![새 Stream Analytics 작업 만들기](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. **만들기**를 클릭합니다.

    작업이 만들어지고 포털에 작업 세부 정보가 표시됩니다.


## <a name="specify-the-job-input"></a>작업 입력 지정

1. Stream Analytics 작업에서 작업 블레이드의 중간에 있는 **작업 토폴로지** 아래에서 **입력**을 클릭합니다. 

2. **입력** 블레이드에서 **+&nbsp;추가**를 클릭한 후 블레이드를 다음 값으로 채웁니다.

   * **입력 별칭**: 이름 `TwitterStream`을 사용합니다. 다른 이름을 사용하는 경우 나중에 필요하므로 기록해 둡니다.
   * **원본 유형**: **데이터 스트림**을 선택합니다.
   * **원본**: **이벤트 허브**를 선택합니다.
   * **가져오기 옵션**: **현재 구독에서 이벤트 허브 사용**을 선택합니다. 
   * **Service Bus 네임스페이스**: 이전에 만든 이벤트 허브 네임스페이스(`<yourname>-socialtwitter-eh-ns`)를 선택합니다.
   * **이벤트 허브**: 이전에 만든 이벤트 허브를 선택합니다(`socialtwitter-eh`).
   * **이벤트 허브 정책 이름**: 이전에 만든 액세스 정책을 선택합니다(`socialtwitter-access`).

     ![Streaming Analytics 작업에 대한 새 입력 만들기](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. **만들기**를 클릭합니다.


## <a name="specify-the-job-query"></a>작업 쿼리 지정

Stream Analytics는 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)를 참조하세요.  이 자습서에서는 Twitter 데이터에 대해 여러 쿼리를 작성하고 테스트하도록 도와줍니다.

항목 간의 멘션 수를 비교하기 위해 [연속 창](https://msdn.microsoft.com/library/azure/dn835055.aspx)을 활용하여 5초마다 항목별 멘션 수를 수집합니다.

1. 없는 경우 **입력** 블레이드를 닫습니다.

2. **개요** 블레이드에서 쿼리 상자의 오른쪽 위에 있는 **쿼리 편집**을 클릭합니다. Azure에서는 작업에 대해 구성된 입력 및 출력을 나열하고 데이터가 출력으로 전송됨에 따라 사용자가 입력 스트림을 변환하는 쿼리를 작성할 수 있도록 합니다.

3. TwitterWpfClient 애플리케이션이 실행되고 있는지 확인합니다. 

3. **쿼리** 블레이드에서 `TwitterStream` 입력 옆에 있는 점을 클릭한 후 **입력의 샘플 데이터**를 선택합니다.

    ![Streaming Analytics 작업 항목에 대해 샘플 데이터를 사용하는 메뉴 옵션, “입력의 샘플 데이터” 선택됨](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    그러면 입력 스트림을 읽는 시간에 따라 정의된, 가져올 샘플 데이터의 양을 지정할 수 있는 블레이드가 열립니다.

4. **분**을 3으로 설정한 후 **확인**을 클릭합니다. 
    
    ![입력 스트림을 샘플링하는 옵션, “3분” 선택됨](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure에서는 입력 스트림의 3분 분량의 데이터를 샘플링하고 샘플 데이터가 준비되면 이를 사용자에게 알려줍니다. (짧은 시간이 소요됩니다.) 

    샘플 데이터는 임시로 저장되었다가 쿼리 창이 열려 있는 동안 사용할 수 있습니다. 쿼리 창을 닫으면 샘플 데이터가 무시되고 새로운 샘플 데이터 집합을 만들어야 합니다. 

5. 코드 편집기에서 쿼리를 다음으로 변경합니다.

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    입력에 대한 별칭으로 `TwitterStream`을 사용하지 않은 경우 쿼리에서 `TwitterStream`에 대한 별칭을 대체합니다.  

    이 쿼리에서는 **TIMESTAMP BY** 키워드를 사용하여 임시 계산에서 사용할 페이로드에 타임스탬프 필드를 지정합니다. 이 필드를 지정하지 않으면 각 이벤트가 이벤트 허브에 도착한 시간을 사용하여 창 작업이 수행됩니다. 자세한 내용은 [Stream Analytics 쿼리 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)에서 “도착 시간과 애플리케이션 시간” 섹션을 참조하세요.

    또한 이 쿼리는 **System.Timestamp** 속성을 사용하여 각 창 끝부분의 타임스탬프에도 액세스합니다.

5. **테스트**를 클릭합니다. 샘플링된 데이터에 대해 쿼리가 실행됩니다.
    
6. **저장**을 클릭합니다. 그러면 Streaming Analytics 작업의 일부로 쿼리를 저장합니다. (샘플 데이터를 저장하지 않음)


## <a name="experiment-using-different-fields-from-the-stream"></a>스트림에서 서로 다른 필드를 사용하여 실험 

다음 표는 JSON 스트리밍 데이터의 일부인 필드를 나열합니다. 쿼리 편집기에서 마음껏 테스트하세요.

|JSON 속성 | 정의|
|--- | ---|
|CreatedAt | 트윗을 만든 시간|
|항목 | 지정된 키워드와 일치하는 항목|
|SentimentScore | Sentiment140의 관심도|
|작성자 | 트윗을 보낸 Twitter 핸들|
|Text | 트윗의 전체 본문|


## <a name="create-an-output-sink"></a>출력 싱크 만들기

이제 이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했습니다. 마지막 단계는 작업에 대한 출력 싱크를 정의하는 것입니다.  

이 자습서에서는 작업 쿼리에서 집계된 트윗 이벤트를 Azure Blob Storage에 기록합니다.  또한 특정 애플리케이션 요구 사항에 따라 Azure SQL Database, Azure Table Storage, Event Hubs 또는 Power BI에 결과를 푸시할 수도 있습니다.

## <a name="specify-the-job-output"></a>작업 출력 지정

1. **작업 토폴로지** 섹션에서 **출력** 상자를 클릭합니다. 

2. **출력** 블레이드에서 **+&nbsp;추가**를 클릭한 후 블레이드를 다음 값으로 채웁니다.

   * **출력 별칭**: 이름 `TwitterStream-Output`을 사용합니다. 
   * **싱크**: **Blob Storage**를 선택합니다.
   * **가져오기 옵션**: **현재 구독의 Blob Storage 사용**을 선택합니다.
   * **Storage 계정**. **새 저장소 계정 만들기**를 선택합니다.
   * **Storage 계정**(두 번째 상자). `YOURNAMEsa`를 입력합니다. 여기서 `YOURNAME`은 사용자 이름 또는 다른 고유 문자열입니다. 이름으로 소문자 및 숫자만 사용할 수 있으며 Azure 전체에서 고유해야 합니다. 
   * **컨테이너**. [https://slack.botframework.com](`socialtwitter`) 을 입력합니다.
     스토리지 계정 이름 및 컨테이너 이름을 다음과 같이 함께 사용하여 Blob Storage에 대한 URI를 제공해야 합니다. 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Stream Analytics 작업에 대한 “새 출력” 블레이드](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. **만들기**를 클릭합니다. 

    Azure에서 저장소 계정을 만들고 키를 자동으로 생성합니다. 

5. **출력** 블레이드를 닫습니다. 


## <a name="start-the-job"></a>작업 시작

작업 입력, 쿼리 및 출력이 지정됩니다. Stream Analytics 작업을 시작할 준비가 되었습니다.

1. TwitterWpfClient 애플리케이션이 실행되고 있는지 확인합니다. 

2. 작업 블레이드에서 **시작**을 클릭합니다.

    ![Stream Analytics 작업 시작](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. **작업 시작** 블레이드에서 **작업 출력 시작 시간**으로 **지금**을 선택한 후 **시작**을 클릭합니다. 

    ![Stream Analytics 작업에 대한 “작업 시작” 블레이드](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure에서 사용자에게 작업이 시작된 때를 알려주고 작업 블레이드에 상태가 **실행 중**으로 표시됩니다.

    ![작업 실행 중](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>정서 분석에 대한 출력 보기

작업이 실시간 Twitter 스트림을 실행 및 처리하면 감정 분석에 대한 출력을 볼 수 있습니다.

[Azure Storage 탐색기](https://storageexplorer.com/) 또는 [Azure 탐색기](https://www.cerebrata.com/products/azure-explorer/introduction)와 같은 도구를 사용하여 작업 출력을 실시간으로 볼 수 있습니다. 여기에서 [Power BI](https://powerbi.com/)를 사용하여 다음 스크린샷과 같은 사용자 지정된 대시보드를 포함하도록 애플리케이션을 확장할 수 있습니다.

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>추세 항목을 확인하는 다른 쿼리 만들기

Twitter 감정을 이해하는 데 사용할 수 있는 다른 쿼리는 [슬라이딩 윈도우](https://msdn.microsoft.com/library/azure/dn835051.aspx)를 기반으로 합니다. 인기 항목을 식별하기 위해 지정된 기간 동안 멘션의 임계값을 초과한 항목을 찾아봅니다.

이 자습서에서는 마지막 5초 이내에 20번 넘게 멘션된 항목을 확인합니다.

1. 작업 블레이드에서 **중지**를 클릭하여 작업을 중지합니다. 

2. **작업 토폴로지** 섹션에서 **쿼리** 상자를 클릭합니다. 

3. 쿼리를 다음으로 변경합니다.

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. **저장**을 클릭합니다.

5. TwitterWpfClient 애플리케이션이 실행되고 있는지 확인합니다. 

6. **시작**을 클릭하여 새 쿼리로 작업을 다시 시작합니다.


## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

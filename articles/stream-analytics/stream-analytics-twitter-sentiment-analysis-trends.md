---
title: Azure Stream Analytics를 사용하여 실시간 Twitter 감정 분석
description: 이 아티클에서는 실시간 Twitter 감정 분석에 대해 Stream Analytics를 사용하는 방법을 설명합니다. 이벤트 생성부터 라이브 대시보드의 데이터에 이르는 단계별 지침이 포함되어 있습니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 7023651b09abc8c3124c7bf71608018d5cb72e25
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162011"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Azure Stream Analytics에서 실시간 Twitter 감정 분석

이 문서에서는 실시간 Twitter 이벤트를 Azure Event Hubs로 가져와서 소셜 미디어 감정 분석 솔루션을 빌드하는 방법을 배웁니다. Azure Stream Analytics 쿼리를 작성 하 여 데이터를 분석 하 고 나중에 사용 하기 위해 결과를 저장 하거나 실시간으로 정보를 제공 하는 [Power BI](https://powerbi.com/) 대시보드를 만듭니다.

소셜 미디어 분석 도구는 조직에서 추세 항목을 이해하는 데 도움이 됩니다. 추세 항목은 소셜 미디어에 대 한 많은 양의 게시물이 있는 주제 및 태도입니다. 감정 분석 ( *의견 마이닝*이 라고도 함)은 소셜 미디어 분석 도구를 사용 하 여 제품 또는 아이디어에 대 한 태도를 확인 합니다. 

실시간 Twitter 추세 분석은 분석 도구의 좋은 예입니다. 해시 태그 구독 모델을 사용 하 여 특정 키워드 (해시 태그)를 수신 하 고 피드의 감정 분석을 개발할 수 있기 때문입니다.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>시나리오: 소셜 미디어 실시간 감정 분석

뉴스 미디어 웹 사이트를 보유하는 기업은 독자와 직접적으로 관련이 있는 사이트 콘텐츠를 부각시켜 경쟁 우위를 확보하는 데 관심이 있습니다. 이러한 기업은 Twitter 데이터에 대해 실시간으로 감정을 분석하여 독자와 관련된 항목에 대한 소셜 미디어 정보를 사용합니다.

Twitter에서 실시간으로 추세를 분석할 토픽을 식별하기 위해 기업에서는 주요 토픽에 대한 트윗 볼륨 및 감정에 대한 실시간 분석이 필요합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드에서는 Twitter에 연결 하 고 특정 해시 태그 (설정할 수 있음)가 있는 트 윗를 찾는 클라이언트 응용 프로그램을 사용 합니다. Azure Streaming Analytics를 사용 하 여 응용 프로그램을 실행 하 고 트 윗를 분석 하려면 다음이 있어야 합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* [Twitter](https://twitter.com) 계정.

* Twitter 피드를 읽는 TwitterClientCore 응용 프로그램입니다. 이 응용 프로그램을 가져오려면 [Twitterclientcore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore)를 다운로드 합니다.

* [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)를 설치 합니다.

## <a name="create-an-event-hub-for-streaming-input"></a>스트리밍 입력에 대 한 이벤트 허브 만들기

애플리케이션 예제에서 이벤트를 생성하여 Azure Event Hub에 푸시합니다. Azure Event Hubs는 Stream Analytics에 대 한 이벤트 수집의 기본 방법입니다. 자세한 내용은 [Azure Event Hubs 설명서](../event-hubs/event-hubs-what-is-event-hubs.md)를 참조하세요.

### <a name="create-an-event-hub-namespace-and-event-hub"></a>이벤트 허브 네임스페이스 및 이벤트 허브 만들기
이 섹션에서는 이벤트 허브 네임 스페이스를 만들고 해당 네임 스페이스에 이벤트 허브를 추가 합니다. 이벤트 허브 네임스페이스는 관련된 이벤트 버스 인스턴스를 논리적으로 그룹화하는 데 사용됩니다. 

1. Azure Portal에 로그인 하 고 **리소스 만들기**를 선택 합니다. 다음. **Event Hubs** 를 검색 하 고 **만들기**를 선택 합니다.

2. **네임 스페이스 만들기** 페이지에서 네임 스페이스 이름을 입력 합니다. 네임 스페이스에는 임의의 이름을 사용할 수 있지만 이름은 URL에 대해 유효 해야 하며 Azure에서 고유 해야 합니다. 
    
3. 가격 책정 계층 및 구독을 선택 하 고 리소스 그룹을 만들거나 선택 합니다. 그런 다음 위치를 선택 하 고 **만들기**를 선택 합니다. 
 
4. 네임 스페이스의 배포가 완료 되 면 리소스 그룹으로 이동 하 여 Azure 리소스 목록에서 이벤트 허브 네임 스페이스를 찾습니다. 

5. 새 네임 스페이스에서 **+&nbsp;Event Hub**를 선택 합니다. 

6. 새 이벤트 허브의 이름을 새 이벤트 허브 *로*합니다. 다른 이름을 사용할 수 있습니다. 이 경우 나중에 이름이 필요하기 때문에 메모해 둡니다. 이벤트 허브에 다른 옵션을 설정하지 않아도 됩니다.
 
7. **만들기**를 선택합니다.

### <a name="grant-access-to-the-event-hub"></a>이벤트 허브에 대한 액세스 권한 부여

프로세스에서 이벤트 허브로 데이터를 전송 하려면 먼저 이벤트 허브에 액세스를 허용 하는 정책이 필요 합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1.  Event hubs 네임 스페이스 왼쪽의 탐색 모음에서 **엔터티** 섹션에 있는 **Event Hubs**를 선택 합니다. 그런 다음 방금 만든 이벤트 허브를 선택 합니다.

2.  왼쪽의 탐색 모음에 있는 **설정**아래에 있는 **공유 액세스 정책** 을 선택 합니다.

    >[!NOTE]
    >이벤트 허브 네임 스페이스 및 이벤트 허브에 대 한 공유 액세스 정책 옵션이 있습니다. 전체 이벤트 허브 네임 스페이스가 아니라 event hub의 컨텍스트에서 작업 하 고 있는지 확인 합니다.

3.  액세스 정책 페이지에서 **+ 추가**를 선택 합니다. 그런 다음 **정책 이름** 에 대해 연결 된 *액세스 권한* 을 입력 하 고 **관리** 확인란을 선택 합니다.
 
4.  **만들기**를 선택합니다.

5.  정책이 배포 된 후에는 공유 액세스 정책 목록에서 정책을 선택 합니다.

6.  **연결 문자열 기본 키** 라는 상자를 찾아 연결 문자열 옆의 복사 단추를 선택 합니다.
 
7.  연결 문자열을 텍스트 편집기에 붙여넣습니다. 약간의 편집을 수행한 후 다음 섹션에 대 한이 연결 문자열이 필요 합니다.

   연결 문자열은 다음과 같습니다.
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   연결 문자열에는 세미콜론으로 구분된 여러 키-값 쌍이 포함되어 있습니다(`Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` 및 `EntityPath`).  

   > [!NOTE]
   > 보안을 위해 예제에 있는 연결 문자열의 일부가 제거되었습니다.

8.  텍스트 편집기에서 연결 문자열의 `EntityPath` 쌍을 제거합니다(앞에 나오는 세미콜론을 반드시 제거). 작업을 완료한 후 연결 문자열은 다음과 같습니다.
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=
   ```

## <a name="configure-and-start-the-twitter-client-application"></a>Twitter 클라이언트 애플리케이션 구성 및 시작

클라이언트 애플리케이션이 Twitter에서 트윗 이벤트를 직접 가져옵니다. 이렇게 하려면 Twitter Streaming API를 호출할 수 있는 권한이 필요합니다. 해당 사용 권한을 구성하려면 고유한 자격 증명(예: OAuth 토큰)을 생성하는 애플리케이션을 Twitter에서 만듭니다. 그런 다음 API를 호출할 때 이러한 자격 증명을 사용하도록 클라이언트 애플리케이션을 구성할 수 있습니다. 

### <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기
이 방법 가이드에 사용할 수 있는 Twitter 응용 프로그램이 아직 없는 경우 새로 만들 수 있습니다. Twitter 계정이 이미 있어야 합니다.

> [!NOTE]
> Twitter에서 애플리케이션을 만들고 키, 비밀 및 토큰을 가져오는 정확한 프로세스는 변경될 수 있습니다. 이러한 지침이 Twitter 사이트에 표시되는 내용과 일치하지 않으면 Twitter 개발자 설명서를 참조하세요.

1. 웹 브라우저에서 [개발자용 Twitter](https://developer.twitter.com/en/apps)로 이동하여 **앱 만들기**를 선택합니다. Twitter 개발자 계정을 신청해야 한다는 메시지가 표시될 수 있습니다. 자유롭게 수행할 수 있으며, 응용 프로그램이 승인 되 면 확인 전자 메일이 표시 됩니다. 개발자 계정이 승인될 때까지 며칠이 걸릴 수 있습니다.

   ![Twitter 애플리케이션 세부 정보](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter 애플리케이션 세부 정보")

2. **애플리케이션 만들기** 페이지에서 새 앱에 대한 세부 정보를 제공한 다음, **Twitter 애플리케이션 만들기**를 선택합니다.

   ![Twitter 애플리케이션 세부 정보](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter 애플리케이션 세부 정보")

3. 애플리케이션 페이지에서 **키 및 토큰** 탭을 선택하고, **소비자 API 키** 및 **소비자 API 비밀 키** 값을 복사합니다. 또한 **액세스 토큰 및 액세스 토큰 비밀** 아래에서 **만들기**를 선택하여 액세스 토큰을 생성합니다. **액세스 토큰** 및 **액세스 토큰 비밀**에 대한 값을 복사합니다.

   Twitter 애플리케이션에 대해 검색한 값을 저장합니다. 나중에 값이 필요 합니다.

> [!NOTE]
> Twitter 애플리케이션에 대한 키 및 비밀은 Twitter 계정에 대한 액세스를 제공합니다. 이 정보는 Twitter 암호처럼 중요하게 취급합니다. 예를 들어 이 정보를 다른 사람에게 제공하는 애플리케이션에 포함하지 마세요. 

### <a name="configure-the-client-application"></a>클라이언트 애플리케이션 구성

Microsoft에서는 특정 항목 집합에 대한 트윗 이벤트를 수집하기 위해 [Twitter의 스트리밍 API](https://dev.twitter.com/streaming/overview)를 사용하여 Twitter 데이터에 연결하는 클라이언트 애플리케이션을 만들었습니다.

애플리케이션이 실행되기 전에 Twitter 키 및 이벤트 허브 연결 문자열과 같은 사용자의 특정 정보가 필요합니다.

1. 필수 조건에 나열 된 대로 [Twitterclientcore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) 응용 프로그램을 다운로드 했는지 확인 합니다.

2. 텍스트 편집기를 사용 하 여 *app.config* 파일을 엽니다. `<appSettings>` 요소를 다음과 같이 변경 합니다.

   * `oauth_consumer_key`를 Twitter 소비자 키 (API 키)로 설정 합니다. 
   * `oauth_consumer_secret`를 Twitter 소비자 비밀 (API 비밀 키)로 설정 합니다.
   * `oauth_token`를 Twitter 액세스 토큰으로 설정 합니다.
   * `oauth_token_secret`를 Twitter 액세스 토큰 암호로 설정 합니다.
   * `EventHubNameConnectionString`을 연결 문자열로 설정합니다. `EntityPath` 키-값 쌍을 제거한 연결 문자열을 사용해야 합니다.
   * `EventHubName`을 이벤트 허브 이름 (엔터티 경로 값)으로 설정 합니다.

3. 명령줄을 열고 TwitterClientCore 앱이 있는 디렉터리로 이동 합니다. 명령 `dotnet build`를 사용 하 여 프로젝트를 빌드합니다. 그런 다음 명령 `dotnet run`를 사용 하 여 앱을 실행 합니다. 앱은 트 윗를 이벤트 허브로 보냅니다.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

이제 Twitter에서 실시간으로 트윗 이벤트가 스트리밍되며 이러한 이벤트를 실시간으로 분석하도록 Stream Analytics 작업을 설정할 수 있습니다.

1. Azure Portal에서 리소스 그룹으로 이동 하 고 **+ 추가**를 선택 합니다. 그런 다음 **Stream Analytics 작업** 을 검색 하 고 **만들기**를 선택 합니다.

2. 작업 이름을 `socialtwitter-sa-job`으로 지정하고 구독, 리소스 그룹 및 위치를 지정합니다.

    최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.

3. **만들기**를 선택합니다. 그런 다음 배포가 완료 되 면 작업으로 이동 합니다.

## <a name="specify-the-job-input"></a>작업 입력 지정

1. Stream Analytics 작업에서 **작업 토폴로지**아래의 왼쪽 메뉴에 있는 **입력** 을 선택 합니다.

2. +&nbsp;선택 하 여 **이벤트 허브** > **스트림 입력을 추가** 합니다. 다음 정보를 사용 하 여 **새 입력** 양식을 작성 합니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |입력 별칭| *TwitterStream* | 입력에 대 한 별칭을 입력 합니다. |
   |Subscription  | \<구독\> |  사용할 Azure 구독을 선택합니다. |
   |이벤트 허브 네임스페이스 | *global.asa-twitter-eventhub* |
   |이벤트 허브 이름 | *비-eh* | *기존 사용*을 선택 합니다. 그런 다음 만든 이벤트 허브를 선택 합니다.|
   |이벤트 압축 유형| GZip | 데이터 압축 유형입니다.|

   나머지 기본값을 그대로 유지 하 고 **저장**을 선택 합니다.

## <a name="specify-the-job-query"></a>작업 쿼리 지정

Stream Analytics는 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)를 참조하세요. 이 방법 가이드를 통해 Twitter 데이터에 대해 여러 쿼리를 작성 하 고 테스트할 수 있습니다.

항목 간의 멘션 수를 비교하기 위해 [연속 창](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics)을 활용하여 5초마다 항목별 멘션 수를 수집합니다.

1. 작업 **개요**에서 쿼리 상자의 오른쪽 위 근처에 있는 **쿼리 편집** 을 선택 합니다. Azure는 작업에 대해 구성 된 입력 및 출력을 나열 하 고 출력으로 전송 될 때 입력 스트림을 변환 하는 쿼리를 만들 수 있도록 합니다.

2. 쿼리 편집기에서 쿼리를 다음으로 변경 합니다.

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. 메시지의 이벤트 데이터가 쿼리 아래의 **입력 미리 보기** 창에 표시 됩니다. **뷰가** **JSON**으로 설정 되어 있는지 확인 합니다. 데이터가 표시 되지 않으면 데이터 생성기가 이벤트 허브로 이벤트를 보내고 입력의 압축 유형으로 **GZip** 을 선택 했는지 확인 합니다.

4. **쿼리 테스트** 를 선택 하 고 쿼리 아래의 **테스트 결과** 창에서 결과를 확인 합니다.

5. 코드 편집기에서 쿼리를 다음으로 변경 하 고 **쿼리 테스트**를 선택 합니다.

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. 이 쿼리는 *Azure*키워드를 포함 하는 모든 트 윗를 반환 합니다.

## <a name="create-an-output-sink"></a>출력 싱크 만들기

이제 이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했습니다. 마지막 단계는 작업에 대한 출력 싱크를 정의하는 것입니다.  

이 방법 가이드에서는 작업 쿼리에서 집계 된 트 윗 이벤트를 Azure Blob storage에 작성 합니다.  또한 특정 애플리케이션 요구 사항에 따라 Azure SQL Database, Azure Table Storage, Event Hubs 또는 Power BI에 결과를 푸시할 수도 있습니다.

## <a name="specify-the-job-output"></a>작업 출력 지정

1. 왼쪽 탐색 메뉴의 **작업 토폴로지** 섹션에서 **출력**을 선택 합니다. 

2. **출력** 페이지에서 **+&nbsp;추가** 및 **Blob storage/Data Lake Storage Gen2**를 클릭 합니다.

   * **출력 별칭**: 이름으로 `TwitterStream-Output`를 사용합니다. 
   * **가져오기 옵션**: **구독에서 저장소 선택을**선택 합니다.
   * **Storage 계정**. 사용자의 스토리지 계정을 선택합니다.
   * **컨테이너**. **새로 만들기** 를 선택 하 고 `socialtwitter`을 입력 합니다.
   
4. **저장**을 선택합니다.   

## <a name="start-the-job"></a>작업 시작

작업 입력, 쿼리 및 출력이 지정됩니다. Stream Analytics 작업을 시작할 준비가 되었습니다.

1. TwitterClientCore 응용 프로그램이 실행 되 고 있는지 확인 합니다. 

2. 작업 개요에서 **시작**을 선택 합니다.

3. **작업 시작** 페이지에서 **작업 출력 시작 시간**으로 **지금** 을 선택 하 고 **시작**을 선택 합니다.

## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
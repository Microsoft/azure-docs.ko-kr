---
title: '자습서: Azure Databricks를 사용하여 스트리밍 데이터에 대한 변칙 검색'
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API 및 Azure Databricks를 사용하여 데이터의 변칙을 모니터링하는 방법을 알아봅니다.
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 95ab400d645a2a2761e39c191cdb85e49e1c7a27
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245607"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>자습서: Azure Databricks를 사용하여 스트리밍 데이터에 대한 변칙 검색

[Azure Databricks](https://azure.microsoft.com/services/databricks/)는 빠르고 쉬우며 협업이 가능한 Apache Spark 기반 분석 서비스입니다. Azure Cognitive Services의 일부인 Anomaly Detector API는 시계열 데이터를 모니터링하는 방법을 제공합니다. 이 자습서에서는 Azure Databricks를 사용하여 거의 실시간으로 데이터 스트림에 대한 변칙 검색을 실행합니다. Azure Event Hubs를 사용하여 Twitter 데이터를 수집하고, Spark Event Hubs 커넥터를 사용하여 이 데이터를 Azure Databricks로 가져옵니다. 그런 다음, API를 사용하여 스트림된 데이터의 변칙을 검색합니다.

다음 그림에서는 애플리케이션 흐름을 보여줍니다.

![Event Hubs 및 Cognitive Services가 있는 Azure Databricks](../media/tutorials/databricks-cognitive-services-tutorial.png "Event Hubs 및 Cognitive Services가 있는 Azure Databricks")

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Databricks 작업 영역 만들기
> * Azure Databricks에 Spark 클러스터 만들기
> * 스트리밍 데이터에 액세스하는 Twitter 앱 만들기
> * Azure Databricks에 노트북 만들기
> * Event Hubs 및 Twitter API에 대한 라이브러리 연결
> * Anomaly Detector 리소스 만들기 및 액세스 키 검색
> * Event Hubs에 트윗 보내기
> * Event Hubs에서 트윗 읽기
> * 트윗에서 변칙 검색 실행

> [!Note]
> * 이 자습서에서는 Anomaly Detector API에 추천되는 [솔루션 아키텍처](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/)를 구현하는 방법을 소개합니다.
> * 이 자습서는 Anomaly Detector API 또는Azure Databricks의 무료 계층(`F0`) 구독을 사용하여 수행할 수 없습니다.

Azure 구독이 없는 경우 [Azure 구독](https://azure.microsoft.com/free/cognitive-services)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Event Hubs 네임스페이스](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) 및 이벤트 허브

- Event Hubs 네임스페이스에 액세스하기 위한 [연결 문자열](../../../event-hubs/event-hubs-get-connection-string.md). 연결 문자열은 다음과 비슷한 형식이어야 합니다.

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`입니다.

- Event Hubs에 대한 공유 액세스 정책 이름 및 정책 키

네임스페이스와 이벤트 허브를 만드는 방법에 대한 자세한 내용은 Azure Event Hubs [빠른 시작](../../../event-hubs/event-hubs-create.md)을 참조하세요.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

이 섹션에서는 [Azure Portal](https://portal.azure.com/)을 사용하여 Azure Databricks 작업 영역을 만듭니다.

1. Azure Portal에서 **리소스 만들기** > **분석** > **Azure Databricks**를 차례로 선택합니다.

    ![Azure Portal의 Databricks](../media/tutorials/azure-databricks-on-portal.png "Azure Portal의 Databricks")

3. **Azure Databricks 서비스** 아래에서 다음 값을 입력하여 Databricks 작업 영역을 만듭니다.


    |속성  |Description  |
    |---------|---------|
    |**작업 영역 이름**     | Databricks 작업 영역의 이름을 제공합니다.        |
    |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
    |**리소스 그룹**     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../../../azure-resource-manager/management/overview.md)를 참조하세요. |
    |**위치**     | **미국 동부 2** 또는 사용 가능한 다른 지역 중 하나를 선택합니다. 지역 가용성은 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.        |
    |**가격 책정 계층**     |  **표준** 또는 **프리미엄** 중에서 선택합니다. **평가판**은 선택하지 마세요. 이러한 계층에 대한 자세한 내용은 [Databricks 가격 페이지](https://azure.microsoft.com/pricing/details/databricks/)를 참조하세요.       |

    **만들기**를 선택합니다.

4. 작업 영역 생성에는 몇 분 정도가 소요됩니다.

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks에서 Spark 클러스터 만들기

1. Azure Portal에서 사용자가 만든 Databricks 작업 영역으로 이동한 다음, **작업 영역 시작**을 선택합니다.

2. Azure Databricks 포털로 리디렉션됩니다. 포털에서 **새 클러스터**를 선택합니다.

    ![Azure의 Databricks](../media/tutorials/databricks-on-azure.png "Azure의 Databricks")

3. **새 클러스터** 페이지에서 클러스터를 만들기 위한 값을 제공합니다.

    ![Azure에서 Databricks Spark 클러스터 만들기](../media/tutorials/create-databricks-spark-cluster.png "Azure에서 Databricks Spark 클러스터 만들기")

    다음 항목 이외의 다른 모든 기본값을 허용합니다.

   * 클러스터의 이름을 입력합니다.
   * 이 문서에서는 **5.2** 런타임을 사용하여 클러스터를 만듭니다. **5.3** 런타임은 선택하지 마세요.
   * **비활성 \_\_분 후 종료** 확인란이 선택되어 있는지 확인합니다. 클러스터가 사용되지 않는 경우 클러스터를 종료하는 기간(분)을 제공합니다.

     **클러스터 만들기**를 선택합니다.
4. 클러스터를 만드는 데 몇 분 정도 걸립니다. 클러스터가 실행되면 노트북을 클러스터에 첨부하고 Spark 작업을 실행할 수 있습니다.

## <a name="create-a-twitter-application"></a>Twitter 애플리케이션 만들기

트윗 스트림을 받으려면 Twitter에 애플리케이션을 만들어야 합니다. 다음 단계에 따라 Twitter 애플리케이션을 만들고 이 자습서를 완료하는 데 필요한 값을 기록합니다.

1. 웹 브라우저에서 [Twitter 애플리케이션 관리](https://apps.twitter.com/)로 이동하고 **새 앱 만들기**를 선택합니다.

    ![Twitter 애플리케이션 만들기](../media/tutorials/databricks-create-twitter-app.png "Twitter 애플리케이션 만들기")

2. **애플리케이션 만들기** 페이지에서 새 앱에 대한 세부 정보를 제공한 다음, **Twitter 애플리케이션 만들기**를 선택합니다.

    ![Twitter 애플리케이션 세부 정보](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter 애플리케이션 세부 정보")

3. 애플리케이션 페이지에서 **키 및 액세스 토큰** 탭을 선택하고, **소비자 키** 및 **소비자 비밀**에 대한 값을 복사합니다. 또한 **내 액세스 토큰 만들기**를 선택하여 액세스 토큰을 생성합니다. **액세스 토큰** 및 **액세스 토큰 비밀**에 대한 값을 복사합니다.

    ![Twitter 애플리케이션 세부 정보](../media/tutorials/twitter-app-key-secret.png "Twitter 애플리케이션 세부 정보")

Twitter 애플리케이션에 대해 검색한 값을 저장합니다. 이러한 값은 자습서의 뒷부분에서 필요합니다.

## <a name="attach-libraries-to-spark-cluster"></a>Spark 클러스터에 라이브러리 연결

이 자습서에서는 Twitter API를 사용하여 트윗을 Event Hubs에 보냅니다. 또한 [Apache Spark Event Hubs 커넥터](https://github.com/Azure/azure-event-hubs-spark)를 사용하여 Azure Event Hubs에 데이터를 읽고 씁니다. 이러한 API를 클러스터의 일부로 사용하려면 Azure Databricks에 라이브러리로 추가한 다음, 이를 Spark 클러스터와 연결합니다. 다음 지침에서는 라이브러리를 작업 영역의 **공유** 폴더에 추가하는 방법을 보여 줍니다.

1. Azure Databricks 작업 영역에서 **작업 영역**을 선택한 다음, **공유**를 마우스 오른쪽 단추로 클릭합니다. 바로 가기 메뉴에서 **만들기** > **라이브러리**를 차례로 선택합니다.

   ![라이브러리 추가 대화 상자](../media/tutorials/databricks-add-library-option.png "라이브러리 추가 대화 상자")

2. [새 라이브러리] 페이지에서 **원본**에 대해 **Maven**을 선택합니다. **좌표**에 대해 추가하려는 패키지에 대한 좌표를 입력합니다. 이 자습서에서 사용된 라이브러리에 대한 Maven 코디네이트는 다음과 같습니다.

   * Spark Event Hubs 커넥터 - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven 좌표 제공](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven 좌표 제공")

3. **만들기**를 선택합니다.

4. 라이브러리를 추가한 폴더를 선택한 다음, 라이브러리 이름을 선택합니다.

    ![추가할 라이브러리 선택](../media/tutorials/select-library.png "추가할 라이브러리 선택")

5. 라이브러리 페이지에 클러스터가 없으면 **클러스터**를 선택하고 만든 클러스터를 실행합니다. 상태가 '실행 중'으로 표시될 때까지 기다린 다음, 라이브러리 페이지로 돌아갑니다.
라이브러리 페이지에서 해당 라이브러리를 사용할 클러스터를 선택한 다음, **설치**를 선택합니다. 라이브러리가 클러스터와 성공적으로 연결되는 즉시 상태가 **설치됨**으로 변경됩니다.

    ![클러스터에 라이브러리 설치](../media/tutorials/databricks-library-attached.png "클러스터에 라이브러리 설치")

6. `twitter4j-core:4.0.7` Twitter 패키지에 대해 이러한 단계를 반복합니다.

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services 액세스 키 가져오기

이 자습서에서는 [Azure Cognitive Services Anomaly Detector API](../overview.md)를 사용하여 트윗 스트림에서 거의 실시간으로 변칙 검색을 실행합니다. API를 사용하기 전에 Azure에서 Anomaly Detector 리소스를 만들고, Anomaly Detector API를 사용하는 액세스 키를 검색해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **+ 리소스 만들기**를 선택합니다.

3. Azure Marketplace 아래에서 **AI + 기계 학습** > **모두 보기** > **Cognitive Services - 자세히** > **Anomaly Detector**를 차례로 선택합니다. 또는 [이 링크](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)를 사용하여 **만들기** 대화 상자로 직접 이동할 수 있습니다.

    ![Anomaly Detector 리소스 만들기](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Anomaly Detector 리소스 만들기")

4. **만들기** 대화 상자에서 다음 값을 제공합니다.

    |값 |Description  |
    |---------|---------|
    |Name     | Anomaly Detector 리소스의 이름입니다.        |
    |Subscription     | 리소스가 연결되는 Azure 구독입니다.        |
    |위치     | Azure 위치입니다.        |
    |가격 책정 계층     | 서비스에 대한 가격 책정 계층입니다. Anomaly Detector 가격 책정에 대한 자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)를 참조하세요.        |
    |Resource group     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 선택할지 여부를 지정합니다.        |


     **만들기**를 선택합니다.

5. 리소스가 만들어지면 **개요** 탭에서 스크린샷과 같이 **엔드포인트** URL을 복사하여 저장합니다. 그런 다음, **액세스 키 표시**를 선택합니다.

    ![액세스 키 표시](../media/tutorials/cognitive-services-get-access-keys.png "액세스 키 표시")

6. **키** 아래에서 사용하려는 키에 대한 복사 아이콘을 선택합니다. 액세스 키를 저장합니다.

    ![액세스 키 복사](../media/tutorials/cognitive-services-copy-access-keys.png "액세스 키 복사")

## <a name="create-notebooks-in-databricks"></a>Databricks에 노트북 만들기

이 섹션에서는 다음 이름을 사용하여 Databricks 작업 영역에 두 개의 노트북을 만듭니다

- **SendTweetsToEventHub** - Twitter에서 트윗을 가져와서 Event Hubs로 스트리밍하는 데 사용하는 생산자 노트북입니다.
- **AnalyzeTweetsFromEventHub** - Event Hubs에서 트윗을 읽고 변칙 검색을 실행하는 데 사용하는 소비자 Notebook입니다.

1. Azure Databricks 작업 영역의 왼쪽 창에서 **작업 영역**을 선택합니다. **작업 영역** 드롭다운에서 **만들기**, **Notebook**을 차례로 선택합니다.

    ![Databricks에서 Notebook 만들기](../media/tutorials/databricks-create-notebook.png "Databricks에서 Notebook 만들기")

2. **Notebook 만들기** 대화 상자에서 이름으로 **SendTweetsToEventHub**를 입력하고, 언어로 **Scala**를 선택하고, 이전에 만든 Spark 클러스터를 선택합니다.

    ![Databricks에서 Notebook 만들기](../media/tutorials/databricks-notebook-details.png "Databricks에서 Notebook 만들기")

    **만들기**를 선택합니다.

3. 단계를 반복하여 **AnalyzeTweetsFromEventHub** 노트북을 만듭니다.

## <a name="send-tweets-to-event-hubs"></a>Event Hubs에 트윗 보내기

**SendTweetsToEventHub** 노트북에서 다음 코드를 붙여넣고, 자리 표시자를 이전에 만든 Event Hubs 네임스페이스 및 Twitter 애플리케이션에 대한 값으로 바꿉니다. 이 Notebook은 "Azure"라는 키워드를 사용하여 트윗에서 만든 시간과 "좋아요" 횟수를 추출하여 Event Hubs로 실시간으로 스트림합니다.

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }

  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

노트북을 실행하려면 **Shift+Enter**를 누릅니다. 다음 코드 조각과 같은 출력이 표시됩니다. 출력의 각 이벤트는 타임스탬프와 Event Hubs에 수집된 "좋아요"의 횟수를 조합한 것입니다.

```output
    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...
```

## <a name="read-tweets-from-event-hubs"></a>Event Hubs에서 트윗 읽기

**AnalyzeTweetsFromEventHub** Notebook에서 다음 코드를 붙여넣고, 자리 표시자를 이전에 만든 Anomaly Detector 리소스의 값으로 바꿉니다. 이 노트북은 **SendTweetsToEventHub** 노트북을 사용하여 이전에 Event Hubs로 스트리밍한 트윗을 읽습니다.

먼저 Anomaly Detector를 호출하는 클라이언트를 작성합니다.
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

노트북을 실행하려면 **Shift+Enter**를 누릅니다. 다음 코드 조각과 같은 출력이 표시됩니다.

```scala
import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection
defined class Point
defined class Series
defined class AnomalySingleResponse
defined class AnomalyBatchResponse
defined object AnomalyDetector
```

그런 다음, 나중에 사용할 집계 함수를 준비합니다.
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)

  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))

  override def dataType: DataType = BooleanType

  override def deterministic: Boolean = false

  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }

  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }

  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }

  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }


      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)

      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }

    return None
  }
}

```

노트북을 실행하려면 **Shift+Enter**를 누릅니다. 다음 코드 조각과 같은 출력이 표시됩니다.

```scala
import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap
defined class AnomalyDetectorAggregationFunction
```

그런 다음, 변칙 검색을 위해 이벤트 허브에서 데이터를 로드합니다. 자리 표시자를 이전에 만든 Azure Event Hubs에 대한 값으로 바꿉니다.

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

이제 출력은 다음 이미지와 비슷합니다. 실시간 데이터이므로 테이블의 날짜가 이 자습서의 날짜와 다를 수 있습니다.
![이벤트 허브에서 데이터 로드](../media/tutorials/load-data-from-eventhub.png "이벤트 허브에서 데이터 로드")

이제 Apache Spark용 Event Hubs 커넥터를 사용하여 거의 실시간으로 Azure Event Hubs의 데이터를 Azure Databricks로 스트림했습니다. Spark에 Event Hubs 커넥터를 사용하는 방법에 대한 자세한 내용은 [커넥터 설명서](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs)를 참조하세요.



## <a name="run-anomaly-detection-on-tweets"></a>트윗에서 변칙 검색 실행

이 섹션에서는 Anomaly Detector API를 사용하여 받은 트윗에 대해 변칙 검색을 실행합니다. 이 섹션에서는 코드 조각을 동일한 **AnalyzeTweetsFromEventHub** 노트북에 추가합니다.

변칙 검색을 수행하려면 먼저 시간별 메트릭 수를 집계해야 합니다.
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window.
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
이제 출력은 다음 코드 조각과 비슷합니다.
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

그런 다음, 집계된 출력 결과를 델타로 가져옵니다. 변칙 검색에는 더 긴 기록 창이 필요하므로 검색하려는 지점에 대한 기록 데이터를 유지하기 위해 델타를 사용합니다.
"[Placeholder: table name]"을 만드는 정규화된 델타 테이블 이름(예: "tweets")으로 바꿉니다. "[Placeholder: folder name for checkpoints]"를 이 코드를 실행할 때마다 고유한 문자열 값(예: "etl-from-eventhub-20190605")으로 바꿉니다.
Azure Databricks의 Delta Lake에 대한 자세한 내용은 [Delta Lake 가이드](https://docs.azuredatabricks.net/delta/index.html)를 참조하세요.


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

"[Placeholder: table name]"을 위에서 선택한 것과 동일한 델타 테이블 이름으로 바꿉니다.
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
출력은 다음과 같습니다.
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

이제 집계된 시계열 데이터가 델타로 지속적으로 수집됩니다. 그런 다음, 최신 지점의 변칙을 검색하는 시간당 작업을 예약할 수 있습니다.
"[Placeholder: table name]"을 위에서 선택한 것과 동일한 델타 테이블 이름으로 바꿉니다.

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
결과는 다음과 같습니다.

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

이것으로 끝입니다. Azure Databricks를 사용하면 데이터를 Azure Event Hubs에 성공적으로 스트림하고, Event Hubs 커넥터를 통해 스트림 데이터를 사용한 다음, 스트리밍 데이터에 대한 변칙 검색을 거의 실시간으로 실행했습니다.
이 자습서에서는 세분성이 매시간이지만, 이 세분성은 필요에 따라 언제든지 변경할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

자습서 실행이 완료되면 클러스터를 종료할 수 있습니다. 이렇게 하려면 Azure Databricks 작업 영역의 왼쪽 창에서 **클러스터**를 선택합니다. 종료하려는 클러스터에서는 커서를 **작업** 열 아래의 줄임표 위로 이동하고, **종료** 아이콘을 선택한 다음, **확인**을 선택합니다.

![Databricks 클러스터 중지](../media/tutorials/terminate-databricks-cluster.png "Databricks 클러스터 중지")

클러스터를 수동으로 종료하지 않은 경우 클러스터를 만드는 중에 **비활성 \_\_분 후 종료** 확인란을 선택하면 자동으로 중지됩니다. 이 경우 지정한 시간 동안 클러스터가 비활성 상태이면 클러스터가 자동으로 중지됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Databricks를 사용하여 Azure Event Hubs로 데이터를 스트리밍한 다음, Event Hubs에서 스트리밍 데이터를 실시간으로 읽는 방법을 알아보았습니다. Anomaly Detector API를 호출하고 Power BI Desktop을 사용하여 변칙을 시각화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
>[Power BI Desktop을 사용한 일괄 처리 변칙 검색](batch-anomaly-detection-powerbi.md)

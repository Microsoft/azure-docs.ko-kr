---
title: 자습서 - Azure Portal을 사용하여 Stream Analytics 작업 만들기 및 관리
description: 이 자습서에서는 Azure Stream Analytics를 사용하여 전화 통화 스트림의 사기성 호출을 분석하는 방법에 대한 엔드투엔드 데모를 제공합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: 577a80f04ad186ab1575fa78db3fa59402d6058f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "83697388"
---
# <a name="tutorial-analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>자습서: Stream Analytics를 사용하여 전화 통화 데이터 분석 및 Power BI 대시보드에서 결과 시각화

이 자습서에서는 Azure Stream Analytics를 사용하여 전화 통화 데이터를 분석하는 방법을 설명합니다. 클라이언트 애플리케이션에서 생성한 전화 통화 데이터에는 일부 사기성 호출이 포함되어 있으며 이러한 호출은 Stream Analytics 작업을 통해 필터링됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 샘플 전화 통화 데이터를 생성하여 Azure Event Hubs로 보내기
> * Stream Analytics 작업 만들기
> * 작업 입력 및 출력 구성
> * 사기성 호출을 필터링하는 쿼리 정의
> * 작업을 테스트 및 시작
> * Power BI에서 결과 시각화

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 작업을 수행합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Portal](https://portal.azure.com/)에 로그인합니다.
* Microsoft 다운로드 센터에서 전화 통화 이벤트 생성기 앱 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)을 다운로드하거나 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)에서 소스 코드를 가져옵니다.
* Power BI 계정이 필요합니다.

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기

Stream Analytics가 사기성 호출 데이터 스트림을 분석하려면 Azure로 데이터를 보내야 합니다. 이 자습서에서는 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)를 사용하여 Azure로 데이터를 보낼 것입니다.

다음 단계에 따라 이벤트 허브를 만들고 해당 이벤트 허브로 호출 데이터를 전송합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **리소스 만들기** > **사물 인터넷** > **Event Hubs**를 선택합니다.

   ![포털에서 Azure Event Hub 만들기](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. 다음 값으로 **네임스페이스 만들기** 창을 채웁니다.

   |**설정**  |**제안 값** |**설명**  |
   |---------|---------|---------|
   |속성     | myEventHubsNS        |  이벤트 허브 네임스페이스를 식별하는 고유 이름입니다.       |
   |Subscription     |   \<Your subscription\>      |   이벤트 허브를 만들 Azure 구독을 선택합니다.      |
   |Resource group     |   MyASADemoRG      |  **새로 만들기**를 선택하고 계정의 새로운 리소스 그룹 이름을 입력합니다.       |
   |위치     |   미국 서부2      |    이벤트 허브 네임스페이스를 배포할 수 있는 위치입니다.     |

4. 나머지 설정에서는 기본 옵션을 사용하고 **만들기**를 선택합니다.

   ![Azure Portal에서 이벤트 허브 네임스페이스 만들기](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. 네임스페이스가 배포를 마치면 **모든 리소스**로 이동하여 Azure 리소스 목록에서 *myEventHubsNS*를 찾습니다. *myEventHubsNS*를 선택하여 엽니다.
6. **+이벤트 허브**를 선택하고 **이름**으로 *MyEventHub* 또는 원하는 다른 이름을 입력합니다. 나머지 설정에서는 기본 옵션을 사용하고 **만들기**를 선택합니다. 배포가 성공할 때까지 기다립니다.

   ![Azure Portal의 이벤트 허브 구성](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>이벤트 허브에 대한 액세스 부여 및 연결 문자열 가져오기

애플리케이션에서 Event Hubs로 데이터를 보낼 수 있으려면 이벤트 허브에 적절한 액세스 권한을 허용하는 정책이 있어야 합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1. 이전 단계인 MyEventHub*에서 만든 이벤트 허브로 이동합니다. **설정** 아래에서 **공유 액세스 정책**을 선택한 다음, **+ 추가**를 선택합니다.

2. 정책 이름을 **MyPolicy**로 지정하고 **관리** 확인란을 선택합니다. 그런 다음 **만들기**를 선택합니다.

   ![이벤트 허브 공유 액세스 정책 만들기](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. 정책이 만들어지면 정책을 선택하여 열고, **연결 문자열 – 기본 키**를 찾습니다. 연결 문자열 옆에 있는 파란색 **복사** 단추를 선택합니다.

   ![공유 액세스 정책 연결 문자열 저장](media/stream-analytics-manage-job/save-connection-string.png)

4. 연결 문자열을 텍스트 편집기에 붙여넣습니다. 그 다음 섹션에서 이 연결 문자열이 필요합니다.

   연결 문자열은 다음과 비슷합니다.

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   연결 문자열에는 **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey**, **EntityPath** 등 여러 개의 키-값 쌍이 세미콜론으로 구분되어 포함됩니다.

## <a name="start-the-event-generator-application"></a>이벤트 생성기 애플리케이션 시작

TelcoGenerator 앱을 시작하기 전에, 앞에서 만든 Azure Event Hubs로 데이터를 전송하도록 앱을 구성해야 합니다.

1. [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) 파일의 콘텐츠를 추출합니다.
2. 원하는 텍스트 편집기에서 `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` 파일을 엽니다(.config 파일이 여러 개 있으므로 올바른 파일을 열어야 함).

3. 구성 파일의 `<appSettings>` 요소를 다음 세부 정보로 업데이트합니다.

   * *EventHubName* 키의 값을 연결 문자열의 EntityPath 값으로 설정합니다.
   * *Microsoft.ServiceBus.ConnectionString* 키 값을 EntityPath 값을 뺀 연결 문자열로 설정합니다(앞에 나오는 세미콜론을 반드시 제거함).

4. 파일을 저장합니다.
5. 명령 창을 열고 TelcoGenerator 애플리케이션 압축을 푼 폴더로 변경합니다. 다음 명령을 입력합니다.

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   이 명령은 다음 매개 변수를 사용합니다.
   * 시간당 호출 데이터 레코드 수.
   * 사기 확률 - 즉, 앱에서 사기성 호출을 시뮬레이션해야 하는 빈도. 값 0.2는 호출 레코드의 약 20%가 사기성으로 나타남을 의미합니다.
   * 기간(시간) - 앱을 실행해야 하는 시간. 명령줄에서 프로세스(**Ctrl+C**)를 종료하여 언제든지 앱을 중지할 수도 있습니다.

   몇 초 후 앱에서 이벤트 허브로 데이터를 전송함에 따라 화면에 전화 통화 레코드가 표시되기 시작합니다. 이 전화 통화 데이터에는 다음 필드가 포함되어 있습니다.

   |**레코드**  |**정의**  |
   |---------|---------|
   |CallrecTime    |  호출 시작 시간에 대한 타임스탬프       |
   |SwitchNum     |  호출 연결에 사용되는 전화 스위치. 이 예에서는 스위치는 발신 국가/지역(미국, 중국, 영국, 독일 또는 오스트레일리아)를 나타내는 문자열입니다.       |
   |CallingNum     |  호출자의 전화번호.       |
   |CallingIMSI     |  국제 모바일 구독자 ID(IMSI) 호출자의 고유 식별자.       |
   |CalledNum     |   호출 수신자의 전화번호.      |
   |CalledIMSI     |  국제 모바일 구독자 ID(IMSI) 호출 수신자의 고유 식별자.       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

이제 호출 이벤트 스트림이 생겼으니, 이벤트 허브에서 데이터를 읽는 Stream Analytics 작업을 만들 수 있습니다.

1. Stream Analytics 작업을 만들려면 [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 선택합니다.

3. **새 Stream Analytics 작업** 창을 다음 값으로 채웁니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |작업 이름     |  ASATutorial       |   이벤트 허브 네임스페이스를 식별하는 고유 이름입니다.      |
   |Subscription    |  \<Your subscription\>   |   작업을 만들 Azure 구독을 선택합니다.       |
   |Resource group   |   MyASADemoRG      |   **기존 항목 사용**을 선택하고 계정의 새로운 리소스 그룹 이름을 입력합니다.      |
   |위치   |    미국 서부2     |      작업을 배포할 수 있는 위치입니다. 최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.      |
   |호스팅 환경    | 클라우드        |     Stream Analytics 작업은 클라우드 또는 에지에 배포할 수 있습니다. 클라우드를 사용하면 Azure 클라우드에 배포할 수 있고, 에지를 사용하면 IoT Edge 디바이스에 배포할 수 있습니다.    |
   |스트리밍 단위     |    1       |      스트리밍 단위는 작업을 실행하는 데 필요한 컴퓨팅 리소스를 나타냅니다. 기본적으로 이 값은 1로 설정됩니다. 스트리밍 단위 크기를 조정하는 방법에 대한 자세한 내용은 [스트리밍 단위의 이해 및 크기 조정](stream-analytics-streaming-unit-consumption.md) 문서를 참조하세요.      |

4. 나머지 설정에서는 기본 옵션을 사용하고 **만들기**를 선택한 후 배포가 완료될 때까지 기다립니다.

   ![Azure Stream Analytics 작업 만들기](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>작업 입력 구성

다음 단계는 이전 섹션에서 만든 이벤트 허브를 사용하여 작업이 데이터를 읽을 입력 원본을 정의하는 것입니다.

1. Azure Portal에서 **모든 리소스** 창을 열고 *ASATutorial* Stream Analytics 작업을 찾습니다.

2. Stream Analytics 작업 창의 **작업 토폴로지** 섹션에서 **입력** 옵션을 선택합니다.

3. **+ 스트림 입력 추가**를 선택하고 **이벤트 허브**를 선택합니다. 다음 값으로 창을 채웁니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |입력 별칭     |  CallStream       |  입력을 식별하는 표시 이름. 입력 별칭은 영숫자 문자, 하이픈, 밑줄만 사용할 수 있으며 길이가 3자에서 63자 사이여야 합니다.       |
   |Subscription    |   \<Your subscription\>      |   이벤트 허브를 만든 Azure 구독을 선택합니다. 이벤트 허브는 Stream Analytics 작업과 같은 구독일 수도 있고 다른 구독일 수도 있습니다.       |
   |이벤트 허브 네임스페이스    |  myEventHubsNS       |  이전 섹션에서 만든 이벤트 허브 네임스페이스를 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 허브 네임스페이스가 드롭다운에 나열됩니다.       |
   |이벤트 허브 이름    |   MyEventHub      |  이전 섹션에서 만든 이벤트 허브를 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 허브가 드롭다운에 나열됩니다.       |
   |이벤트 허브 정책 이름   |  MyPolicy       |  이전 섹션에서 만든 이벤트 허브 공유 액세스 정책을 선택합니다. 현재 구독에서 사용할 수 있는 모든 이벤트 정책이 드롭다운에 나열됩니다.       |

4. 나머지 설정에서는 기본 옵션을 사용하고 **저장**을 선택합니다.

   ![Azure Stream Analytics 입력 구성](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>작업 출력 구성

마지막 단계는 변환된 데이터를 기록할 수 있는 작업의 출력 싱크를 정의하는 것입니다. 이 자습서에서는 Power BI를 사용하여 데이터를 출력하고 시각화합니다.

1. Azure Portal에서 **모든 리소스** 창을 열고 *ASATutorial* Stream Analytics 작업을 찾습니다.

2. Stream Analytics 작업 창의 **작업 토폴로지** 섹션에서 **출력** 옵션을 선택합니다.

3. **+ 추가** > **Power BI**를 선택합니다. 다음 세부 정보로 양식을 채우고 **권한 부여**를 선택합니다.

   |**설정**  |**제안 값**  |
   |---------|---------|
   |출력 별칭  |  MyPBIoutput  |
   |데이터 세트 이름  |   ASAdataset  |
   |테이블 이름 |  ASATable  |

   ![Stream Analytics 출력 구성](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. **권한 부여**를 선택하면 팝업 창이 열리고, Power BI 계정에 인증하려면 자격 증명을 입력하라는 메시지가 표시됩니다. 권한 부여가 완료되면 설정을 **저장**합니다.

## <a name="define-a-query-to-analyze-input-data"></a>입력 데이터를 분석하는 쿼리 정의

다음 단계는 실시간으로 데이터를 분석하는 변환을 만드는 것입니다. [Stream Analytics 쿼리 언어](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)를 사용하여 변환 쿼리를 정의합니다. 이 자습서에 사용된 쿼리는 전화 데이터에서 사기성 호출을 검색합니다.

이 예제에서는 동일한 사용자가 5초 이내에 다른 위치에서 사기성 호출을 만듭니다. 예를 들어 동일한 사용자가 미국 및 오스트레일리아에서 동시에 합법적으로 전화를 걸 수 없습니다. Stream Analytics 작업에 대한 변환 쿼리를 정의하려면:

1. Azure Portal에서 **모든 리소스** 창을 열고 앞에서 만든 **ASATutorial** Stream Analytics 작업으로 이동합니다.

2. Stream Analytics 작업 창의 **작업 토폴로지** 섹션에서 **쿼리** 옵션을 선택합니다. 쿼리 창에는 작업에 대해 구성된 입력 및 출력이 나열되며, 입력 스트림을 변환하는 쿼리를 만들 수 있습니다.

3. 편집기의 기존 쿼리를 다음 쿼리로 바꿉니다. 이 쿼리는 호출 데이터를 5초 간격으로 자체 조인합니다.

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   사기성 호출을 검사하려면 `CallRecTime` 값에 따라 스트리밍 데이터를 자체 조인하면 됩니다. 그 후 `CallingIMSI` 값(발신 번호)이 동일하지만 `SwitchNum` 값(발신 국가/지역)은 다른 호출 레코드를 찾을 수 있습니다. 스트리밍 데이터에 JOIN 작업을 사용할 경우 조인은 일치하는 행이 시간상으로 얼마나 분리할 수 있는지 정도에 대한 몇 가지 한도를 제공해야 합니다. 스트리밍 데이터가 무한하기 때문에 관계에 대한 시간 범위는 [DATEDIFF](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics) 함수를 사용하여 조인의 **ON** 절 내에서 지정됩니다.

   이 쿼리는 **DATEDIFF** 함수를 제외하고 일반 SQL 조인과 흡사합니다. 이 쿼리에 사용되는 **DATEDIFF** 함수는 Stream Analytics로 한정되며, `ON...BETWEEN` 절 내부에 나타나야 합니다.

4. 쿼리를 **저장**합니다.

   ![포털에서 Stream Analytics 쿼리 정의](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>쿼리 테스트

쿼리 편집기에서 샘플 데이터를 사용하여 쿼리를 테스트할 수 있습니다. 쿼리를 테스트하려면 다음 단계를 실행합니다.

1. TelcoGenerator 앱이 실행 중이고 전화 호출 레코드를 생성하고 있는지 확인합니다.

2. **쿼리** 창에서 *CallStream* 입력 옆에 있는 점을 선택한 다음, **입력의 샘플 데이터**를 선택합니다.

3. **분**을 3으로 설정하고 **확인**을 선택합니다. 입력 스트림에서 3분 분량의 데이터가 샘플링되며 샘플 데이터가 준비되면 알림이 제공됩니다. 알림 표시줄에서 샘플링 상태를 볼 수 있습니다.

   샘플 데이터는 임시로 저장되었다가 쿼리 창이 열려 있는 동안 사용할 수 있습니다. 쿼리 창을 닫으면 샘플 데이터가 무시되고, 샘플 데이터를 테스트하려면 새로운 샘플 데이터 집합을 만들어야 합니다. 또는 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)의 샘플 데이터 JSON 파일을 사용한 다음, 해당 JSON 파일을 업로드하여 *CallStream* 입력의 샘플 데이터로 사용할 수 있습니다.

   ![Stream Analytics에 대한 입력 데이터를 샘플링하는 방법의 시각적 개체](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. **테스트**를 선택하여 쿼리를 테스트합니다. 다음 결과가 보일 것입니다.

   ![Stream Analytics 쿼리 테스트의 출력](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>작업을 시작하고 출력을 시각화

1. 작업을 시작하려면 작업의 **개요** 창으로 이동하고 **시작**을 선택합니다.

2. 작업 출력 시작 시간으로 **지금**을 선택하고 **시작**을 선택합니다. 알림 표시줄에서 작업 상태를 볼 수 있습니다.

3. 작업이 성공하면 [Power BI](https://powerbi.com/)로 이동하여 회사 또는 학교 계정으로 로그인합니다. Stream Analytics 작업 쿼리가 결과를 출력 중이면 앞에서 만든 *ASAdataset* 데이터 세트가 **데이터 세트** 탭에 있는 것입니다.

4. Power BI 작업 영역에서 **+ 만들기**를 선택하여 *사기성 호출*이라는 새 대시보드를 만듭니다.

5. 창 맨 위에서 **타일 추가**를 선택합니다. **사용자 지정 스트리밍 데이터**를 선택하고 **다음**을 선택합니다. **데이터 세트** 아래에서 **ASAdataset**를 선택합니다. **시각화 유형** 드롭다운에서 **카드**를 선택하고 **필드**에 **사기성 호출**을 추가합니다. **다음**을 선택하여 타일 이름을 입력하고, **적용**을 선택하여 타일을 만듭니다.

   ![Power BI 대시보드 타일 만들기](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. 다음 옵션을 사용하여 5단계를 다시 수행합니다.
   * 시각화 유형으로 꺾은선형 차트를 선택합니다.
   * 축을 추가하고 **windowend**를 선택합니다.
   * 값을 추가하고 **fraudulentcalls**를 선택합니다.
   * **표시할 시간 창**에 지난 10분을 선택합니다.

7. 두 타일이 모두 추가되면 대시보드가 아래 예제처럼 보입니다. 이벤트 허브 발신자 애플리케이션 및 Stream Analytics 애플리케이션이 실행 중이면 새 데이터가 도착할 때마다 Power BI 대시보드가 주기적으로 업데이트됩니다.

   ![Power BI 대시보드에서 결과 보기](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>웹 애플리케이션에 Power BI 대시보드 포함

자습서의 이 부분에서는 Power BI 팀에서 만든 [ASP.NET](https://asp.net/) 웹 애플리케이션을 사용하여 대시보드를 포함할 것입니다. 대시보드를 포함하는 방법에 대한 자세한 내용은 [Power BI를 통해 포함](https://docs.microsoft.com/power-bi/developer/embedding) 문서를 참조하세요.

애플리케이션을 설정하려면 [PowerBI-개발자-샘플](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub 리포지토리로 이동하여 **사용자 소유 데이터** 섹션 아래의 지침을 따릅니다(**통합-웹앱** 하위 섹션 아래에 있는 리디렉션 및 홈페이지 URL 사용). 우리는 대시보드 예제를 사용할 예정이므로 [GitHub 리포지토리](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/integrate-web-app)에 있는 **통합-웹앱** 샘플 코드를 사용합니다.
브라우저에서 애플리케이션을 실행한 후에는 다음 단계에 따라 앞에서 만든 대시보드를 웹 페이지에 포함합니다.

1. **Power BI에 로그인**을 선택합니다. 그러면 Power BI 계정에 대시보드에 대한 애플리케이션 액세스 권한이 부여됩니다.

2. **대시보드 가져오기** 단추를 선택합니다. 그러면 계정의 대시보드가 테이블에 표시됩니다. 앞에서 만든 대시보드 이름(**powerbi-embedded-dashboard**)을 찾아서 해당 **EmbedUrl**을 복사합니다.

3. 마지막으로 **EmbedUrl**을 해당 텍스트 필드에 붙여넣고 **대시보드 포함**을 선택합니다. 이제 웹 애플리케이션에 동일한 대시보드가 포함된 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 간단한 Stream Analytics 작업을 만들고, 들어오는 데이터를 분석하고, 그 결과를 Power BI 대시보드에 표시했습니다. Stream Analytics 작업에 대해 자세히 알아보려면 그 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Stream Analytics 작업에서 Azure Functions 실행](stream-analytics-with-azure-functions.md)

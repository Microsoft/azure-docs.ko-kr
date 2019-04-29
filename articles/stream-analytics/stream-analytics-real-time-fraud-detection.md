---
title: Azure Stream Analytics를 사용하여 실시간 부정 행위 감지
description: Stream Analytics으로 실시간 부정 행위 감지 솔루션을 만드는 방법에 대해 알아봅니다. 이벤트 허브를 사용하여 실시간 이벤트를 처리합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a13d3b24cd7845de144183d9f2ea825e0e24219f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818197"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Azure Stream Analytics 사용 시작: 실시간 사기 감지

이 자습서에서는 Azure Stream Analytics를 사용하는 방법에 대한 종단 간 일러스트레이션을 제공합니다. 다음 방법에 대해 알아봅니다. 

* 스트리밍 이벤트를 Azure Event Hubs의 인스턴스로 전환합니다. 이 자습서에서는 휴대폰 메타데이터 레코드 스트림을 시뮬레이트하는 앱을 사용합니다.

* SQL과 유사한 Stream Analytics 쿼리를 작성하여 데이터, 집계 정보를 변환하고 패턴을 찾아냅니다. 쿼리를 사용하여 들어오는 스트림을 검사하고 사기성일 수 있는 호출을 찾아내는 방법을 살펴봅니다.

* 추가 정보를 분석할 수 있는 출력 싱크(저장소)로 결과를 보냅니다. 이 경우 의심스러운 호출 데이터를 Azure Blob Storage로 보냅니다.

이 자습서에서는 전화 통화 데이터를 기준으로 실시간 사기 감지의 예를 사용합니다. 설명된 기법은 신용 카드 사기 또는 ID 도용 같은 다른 유형의 부정 행위 감지에도 적용 가능합니다. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>시나리오: 실시간으로 통신 및 SIM 사기 감지

통신 회사에는 많은 양의 들어오는 호출 데이터가 있습니다. 회사에서는 고객에게 사기성 호출을 알리거나 고객이 특정 번호에 대한 서비스를 종료할 수 있도록 실시간으로 사기성 호출을 감지하려고 합니다. SIM 사기의 한 유형으로, 지리적으로 다른 위치에서 동일한 ID로 동시에 여러 호출을 이루어지는 경우가 있습니다. 이러한 유형의 사기 행위를 감지하기 위해 회사는 들어오는 전화 레코드를 검토하고 특정 패턴을 찾아냅니다. 이 경우 서로 다른 국가에서 동시에 호출이 발생하는 패턴입니다. 이 범주에 속하는 모든 전화 레코드는 후속 분석을 위해 저장소에 기록됩니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 샘플 전화 통화 메타데이터를 생성하는 클라이언트 앱을 사용하여 전화 통화 데이터를 시뮬레이션합니다. 앱에서 생성하는 일부 레코드는 사기성 호출과 유사합니다. 

시작하기 전에 다음이 필요합니다.

* Azure 계정.
* 호출 이벤트 생성기 앱 [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip): Microsoft 다운로드 센터에서 다운로드할 수 있습니다. 컴퓨터의 폴더에 이 패키지의 압축을 풉니다. 소스 코드를 확인하고 디버거에서 앱을 실행하려면 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)에서 앱 소스 코드를 가져올 수 있습니다. 

    >[!NOTE]
    >Windows에서 다운로드한 .zip 파일을 차단할 수 있습니다. 압축을 풀 수 없는 경우 해당 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. “이 파일은 다른 컴퓨터로부터 왔으며 사용자의 컴퓨터를 보호하기 위해 차단되었을 수도 있습니다.”라는 메시지가 표시되면 **차단 해제** 옵션을 선택하여 **적용**을 클릭합니다.

Streaming Analytics 작업 결과를 확인하려면 Azure Blob Storage 컨테이너의 내용을 보기 위한 도구도 필요합니다. Visual Studio를 사용하는 경우 [Visual Studio용 Azure 도구](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) 또는 [Visual Studio 클라우드 탐색기](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer)를 사용할 수 있습니다. 또는 [Azure Storage 탐색기](https://storageexplorer.com/) 또는 [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage)과 같은 독립 실행형 도구를 설치할 수 있습니다. 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>이벤트를 수집하는 Azure Event Hub 만들기

데이터 스트림을 분석하기 위해 Azure로 *수집*합니다. 데이터를 수집하는 일반적인 방법은 [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)를 사용하는 것이며, 이를 통해 초당 수백만 이벤트를 수집한 다음 이벤트 정보를 처리 및 저장할 수 있습니다. 이 자습서에서는 이벤트 허브를 만든 후 호출 이벤트 생성기 앱에서 호출 데이터를 이벤트 허브로 보내도록 합니다. 이벤트 허브에 대한 자세한 내용은 [Azure Service Bus 설명서](https://docs.microsoft.com/azure/service-bus/)를 참조하세요.

>[!NOTE]
>이 절차의 보다 자세한 버전은 [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조하세요. 

### <a name="create-a-namespace-and-event-hub"></a>네임스페이스 및 이벤트 허브 만들기
이 절차에서는 이벤트 허브 네임스페이스를 먼저 만든 후 이벤트 허브를 해당 네임스페이스에 추가합니다. 이벤트 허브 네임스페이스는 관련된 이벤트 버스 인스턴스를 논리적으로 그룹화하는 데 사용됩니다. 

1. Azure Portal에 로그인하고 **리소스 만들기** > **사물 인터넷** > **이벤트 허브**를 클릭합니다. 

2. **네임스페이스 만들기** 창에서 네임스페이스 이름을 입력합니다(예: `<yourname>-eh-ns-demo`). 네임스페이스에 어떤 이름이든 사용할 수 있지만 이름은 URL에 대해 유효해야 하며 Azure 전체에서 고유해야 합니다. 
    
3. 구독을 선택하고 리소스 그룹을 만들거나 선택한 후 **만들기**를 클릭합니다.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. 네임스페이스에서 배포를 완료했으면 Azure 리소스 목록에서 이벤트 허브 네임스페이스를 찾습니다. 

5. 새 네임스페이스를 클릭하고 네임스페이스 창에서 **이벤트 허브**를 클릭합니다.

   ![새 이벤트 허브를 만들기 위한 이벤트 허브 추가 단추](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. 새 이벤트 허브 이름을 `asa-eh-frauddetection-demo`로 지정합니다. 다른 이름을 사용할 수 있습니다. 이 경우 나중에 이름이 필요하기 때문에 메모해 둡니다. 지금 당장은 이벤트 허브에 다른 옵션을 설정하지 않아도 됩니다.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. **만들기**를 클릭합니다.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>이벤트 허브에 대한 액세스 부여 및 연결 문자열 가져오기

프로세스에서 이벤트 허브로 데이터를 보낼 수 있으려면 이벤트 허브에 적절한 액세스 권한을 허용하는 정책이 있어야 합니다. 액세스 정책은 권한 부여 정보를 포함하는 연결 문자열을 생성합니다.

1.  이벤트 네임스페이스 창에서 **이벤트 허브**를 클릭하고 새 이벤트 허브의 이름을 클릭합니다.

2.  이벤트 허브 창에서 **공유 액세스 정책**을 클릭한 후 **+&nbsp;추가**를 클릭합니다.

    >[!NOTE]
    >이벤트 허브 네임스페이스가 아니라 이벤트 허브로 작업하고 있는지 확인합니다.

3.  **클레임**에 대해 `sa-policy-manage-demo`라는 이름의 정책을 추가하고 **관리**를 선택합니다.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  **만들기**를 클릭합니다.

5.  정책이 배포되었으면 공유 액세스 정책 목록에서 클릭합니다.

6.  **CONNECTION STRING-PRIMARY KEY**로 레이블이 지정된 상자를 찾고 연결 문자열 옆의 복사 단추를 클릭합니다. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  연결 문자열을 텍스트 편집기에 붙여넣습니다. 다음 섹션에서 몇 가지 편집을 수행한 후 이 연결 문자열이 필요합니다.

    연결 문자열은 다음과 같습니다.

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    연결 문자열에는 세미콜론으로 구분된 여러 키-값 쌍이 포함되어 있습니다(`Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` 및 `EntityPath`).  

## <a name="configure-and-start-the-event-generator-application"></a>이벤트 생성기 애플리케이션 구성 및 시작

TelcoGenerator 앱을 시작하기 전에 호출 레코드를 만든 이벤트 허브에 전송할 수 있도록 구성해야 합니다.

### <a name="configure-the-telcogenerator-app"></a>TelcoGenerator 앱 구성

1. 연결 문자열을 복사한 편집기에서 `EntityPath` 값을 기록해둔 후 `EntityPath` 쌍을 제거합니다(앞에 있는 세미콜론을 반드시 제거). 

2. TelcoGenerator.zip 파일의 압축을 푼 폴더에서 편집기로 telcodatagen.exe.config 파일을 엽니다. (둘 이상의 .config 파일이 있으므로 해당 파일을 열어야 함)

3. `<appSettings>` 요소에서 다음을 수행합니다.

   * `EventHubName` 키 값을 이벤트 허브 이름으로 설정합니다(즉, 엔터티 경로 값).
   * `Microsoft.ServiceBus.ConnectionString` 키 값을 연결 문자열로 설정합니다. 

   `<appSettings>` 섹션은 다음 예제와 같습니다. (쉽게 이해할 수 있도록 줄이 래핑되고 권한 부여 토큰에서 일부 문자를 제거했음)

   ![TelcoGenerator 구성 파일이 이벤트 허브 이름 및 연결 문자열을 표시함](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. 파일을 저장합니다. 

### <a name="start-the-app"></a>앱 시작
1.  명령 창을 열고 TelcoGenerator 앱이 압축 해제된 폴더로 변경합니다.
2.  다음 명령을 입력합니다.

        ```cmd
        telcodatagen.exe 1000 0.2 2
        ```

    매개 변수는 다음과 같습니다. 

    * 시간당 CDR 수. 
    * SIM 카드 사기 확률: 앱에서 사기성 호출을 시뮬레이트하게 되는 전체 호출 대비 백분율로서 빈도 값 0.2는 호출 레코드의 약 20%가 사기성으로 나타남을 의미합니다.
    * 기간(시간). 앱이 실행되는 시간. 명령줄에서 Ctrl + C를 눌러 언제든지 앱을 중지할 수도 있습니다.

    몇 초 후 앱에서 이벤트 허브로 데이터를 전송함에 따라 화면에 전화 통화 레코드가 표시되기 시작합니다.

이 실시간 사기 감지 애플리케이션에서 사용할 수 있는 일부 키 필드는 다음과 같습니다.

|**레코드**|**정의**|
|----------|--------------|
|`CallrecTime`|호출 시작 시간에 대한 타임스탬프 |
|`SwitchNum`|호출 연결에 사용되는 전화 스위치. 이 예에서는 스위치는 발신 국가(미국, 중국, 영국, 독일 또는 오스트레일리아)를 나타내는 문자열입니다. |
|`CallingNum`|호출자의 전화번호. |
|`CallingIMSI`|국제 모바일 구독자 ID(IMSI) 호출자의 고유 식별자 |
|`CalledNum`|호출 수신자의 전화번호. |
|`CalledIMSI`|국제 모바일 구독자 ID(IMSI) 호출 수신자의 고유 식별자입니다. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>스트리밍 데이터를 관리하는 Stream Analytics 작업 만들기

이제 호출 이벤트 스트림이 있고 Stream Analytics 작업을 설정할 수 있습니다. 이 작업은 설정한 이벤트 허브에서 데이터를 읽습니다. 

### <a name="create-the-job"></a>작업 만들기 

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 차례로 클릭합니다.

2. 작업 이름을 `asa_frauddetection_job_demo`로 지정하고 구독, 리소스 그룹 및 위치를 지정합니다.

    최상의 성능을 위해 동일한 지역에 작업 및 이벤트 허브를 배치하는 것이 좋으며 지역 간에 데이터를 전송하는 데 비용을 지불하지 않아도 됩니다.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. **만들기**를 클릭합니다.

    작업이 만들어지고 포털에 작업 세부 정보가 표시됩니다. 아직 아무 것도 실행되지 않지만 시작하려면 작업을 구성해야 합니다.

### <a name="configure-job-input"></a>작업 입력 구성

1. 대시보드 또는 **모든 리소스** 창에서 `asa_frauddetection_job_demo` Stream Analytics 작업을 찾아 선택합니다. 
2. Stream Analytics 작업 창의 **개요** 섹션에서 **입력** 상자를 클릭합니다.

   ![Streaming Analytics 작업 창에서 토폴로지 아래 입력 상자](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. **스트림 입력 추가**를 클릭하고 **이벤트 허브**를 선택합니다. 그런 후 다음 정보로 새 입력 페이지를 채웁니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |입력 별칭  |  CallStream   |  작업의 입력을 식별하는 이름을 입력합니다.   |
   |구독   |  \<구독\> |  만든 이벤트 허브가 있는 Azure 구독을 선택합니다.   |
   |이벤트 허브 네임스페이스  |  asa-eh-ns-demo |  이벤트 허브 네임스페이스의 이름을 입력합니다.   |
   |이벤트 허브 이름  | asa-eh-frauddetection-demo | 이벤트 허브의 이름을 선택합니다.   |
   |이벤트 허브 정책 이름  | asa-policy-manage-demo | 이전에 만든 액세스 정책을 선택합니다.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. **만들기**를 클릭합니다.

## <a name="create-queries-to-transform-real-time-data"></a>실시간 데이터를 변환하는 쿼리 만들기

이 시점에는 들어오는 데이터 스트림을 읽도록 설정된 Stream Analytics 작업이 있습니다. 다음 단계는 실시간으로 데이터를 분석하는 쿼리를 만드는 것입니다. Stream Analytics는 실시간 처리에 대해 변환을 설명하는 간단하고 선언적인 쿼리 모델을 지원합니다. 쿼리는 Stream Analytics와 관련된 일부 확장 기능을 포함한 SQL과 유사한 언어를 사용합니다. 

간단한 쿼리로 들어오는 모든 데이터를 읽을 수 있습니다. 그러나 보통은 데이터에서 특정 데이터 또는 관계를 찾는 쿼리를 작성합니다. 자습서의 이 섹션에서는 분석을 위해 입력 스트림을 변환할 수 있는 몇 가지 방법에 대해 알아보기 위해 여러 쿼리를 작성 및 테스트합니다. 

여기서 작성한 쿼리는 화면에 변환된 데이터를 표시하기만 합니다. 이후 섹션에서는 출력 싱크 및 변환된 데이터를 해당 싱크에 기록하는 쿼리를 구성합니다.

이 언어에 대한 자세한 내용은 [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/dn834998.aspx)를 참조하세요.

### <a name="get-sample-data-for-testing-queries"></a>쿼리를 테스트하기 위한 샘플 데이터 가져오기

TelcoGenerator 앱은 호출 레코드를 이벤트 허브로 보내고 Stream Analytics 작업은 이벤트 허브에서 읽어오도록 구성됩니다. 쿼리로 작업을 테스트하여 올바르게 읽고 있는지 확인할 수 있습니다. Azure 콘솔에서 쿼리를 테스트하려면 샘플 데이터가 필요합니다. 이 연습에서는 이벤트 허브로 들어오는 스트림에서 샘플 데이터를 추출합니다.

1. TelcoGenerator 앱이 실행 중이고 호출 레코드를 생성하고 있는지 확인합니다.
2. 포털에서 Streaming Analytics 작업 창으로 돌아갑니다. (창을 닫은 경우 **모든 리소스** 창에서 `asa_frauddetection_job_demo` 검색)
3. **쿼리** 상자를 클릭합니다. Azure에서는 작업에 대해 구성된 입력 및 출력을 나열하고 데이터가 출력으로 전송됨에 따라 사용자가 입력 스트림을 변환하는 쿼리를 작성할 수 있도록 합니다.
4. **쿼리** 창에서 `CallStream` 입력 옆에 있는 점을 클릭한 후 **입력의 샘플 데이터**를 선택합니다.

   ![Streaming Analytics 작업 항목에 대해 샘플 데이터를 사용하는 메뉴 옵션, “입력의 샘플 데이터” 선택됨](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. **분**을 3으로 설정한 후 **확인**을 클릭합니다. 
    
   ![입력 스트림을 샘플링하는 옵션, “3분” 선택됨](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure에서는 입력 스트림의 3분 분량의 데이터를 샘플링하고 샘플 데이터가 준비되면 이를 사용자에게 알려줍니다. (짧은 시간이 소요됩니다.) 

샘플 데이터는 임시로 저장되었다가 쿼리 창이 열려 있는 동안 사용할 수 있습니다. 쿼리 창을 닫으면 샘플 데이터가 무시되고 새로운 샘플 데이터 집합을 만들어야 합니다. 

대안으로, 안에 샘플 데이터가 있는 .json 파일을 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)에서 가져온 후 `CallStream` 입력에 대한 샘플 데이터로 사용할 .json 파일을 업로드할 수 있습니다. 

### <a name="test-using-a-pass-through-query"></a>통과 쿼리를 사용하여 테스트

모든 이벤트를 보관하려는 경우 통과 쿼리를 사용하여 이벤트의 페이로드에서 모든 필드를 읽을 수 있습니다.

1. 쿼리 창에서 다음 쿼리를 입력합니다.
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >SQL처럼 키워드는 대/소문자를 구분하지 않고 공백은 중요하지 않습니다.

    쿼리에서 `CallStream`은 입력을 만들 때 지정한 별칭입니다. 다른 별칭을 사용하는 경우 대신 해당 이름을 사용합니다.

2. **테스트**를 클릭합니다.

    Stream Analytics 작업이 샘플 데이터에 대해 쿼리를 실행하고 창 맨 아래 출력을 표시합니다. 결과에는 이벤트 허브 및 Stream Analytics 작업이 올바르게 구성되었다고 표시됩니다. (설명했듯이, 나중에 쿼리에서 데이터를 쓸 수 있는 출력 싱크를 만듭니다.)

   ![Stream Analytics 작업 출력, 73개 레코드가 생성된 것으로 표시](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    표시되는 레코드의 정확한 수는 3분 샘플에 캡처된 레코드 수에 따라 달라집니다.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>열 프로젝션을 사용하여 필드 수 줄이기

대부분의 경우 분석 시에 입력 스트림의 모든 열이 필요하지는 않습니다. 쿼리를 사용하여 통과 쿼리에서보다 더 작은 집합의 반환된 필드를 프로젝션할 수 있습니다.

1. 코드 편집기에서 쿼리를 다음으로 변경합니다.

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. **테스트**를 다시 클릭합니다. 

   ![프로젝션에 대한 Stream Analytics 작업 출력이 25개 레코드를 표시함](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>지역별로 수신 전화 수 계산: 집계가 있는 연속 창

지역당 들어오는 호출의 수를 계산한다고 가정합니다. 스트리밍 데이터에서 개수와 같은 집계 함수를 수행하려는 경우 데이터 스트림 자체가 사실상 무한하므로 스트림을 temporal 단위로 분할해야 합니다. Streaming Analytics [window 함수](stream-analytics-window-functions.md)를 사용하여 이 작업을 수행합니다. 그런 다음 해당 창 내에서 데이터를 단위로 작업할 수 있습니다.

이 변환의 경우 겹치지 않는 temporal 창 시퀀스를 원하며 각 창에는 그룹화 및 집계할 수 있는 불연속 데이터 집합이 있습니다. 이러한 형식의 창을 *연속 창*이라고 합니다. 연속 창 내에서 호출이 시작된 국가를 나타내는 `SwitchNum`으로 그룹화된 들어오는 호출 수를 가져올 수 있습니다. 

1. 코드 편집기에서 쿼리를 다음으로 변경합니다.

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    이 쿼리에서는 `FROM` 절에 `Timestamp By` 키워드를 사용하여 연속 창을 정의하는 데 사용할 입력 스트림의 타임스탬프 필드를 지정합니다. 이 경우 창은 각 레코드의 `CallRecTime` 필드에 따라 데이터를 세그먼트로 나눕니다. (이 필드를 지정하지 않으면 창 작업에서 각 이벤트가 이벤트 허브에 도착한 시간을 사용합니다.) [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)에서 “도착 시간과 애플리케이션 시간”을 참조하세요. 

    프로젝션에는 각 창의 끝에 대한 타임스탬프를 반환하는 `System.Timestamp`가 포함됩니다. 

    연속 창을 사용할 것인지를 지정 하려면 사용 합니다 [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) 함수는 `GROUP BY` 절. 함수에서 시간 단위(마이크로초에서 하루까지) 및 창 크기(단위 수)를 지정합니다. 이 예제에서 연속 창은 5초 간격으로 구성되므로 5초 분량의 호출에 대한 국가별 개수를 가져옵니다.

2. **테스트**를 다시 클릭합니다. 결과에서 **WindowEnd** 아래 타임스탬프가 5초 단위로 증가하는 것을 알 수 있습니다.

   ![13개 레코드를 표시하는 집계에 대한 Stream Analytics 작업 출력](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>셀프 조인을 사용하여 SIM 사기 감지

이 예제에서는 5초 이내에 서로 다른 위치에서 동일한 사용자로부터 발생한 호출을 사기성 있는 사용으로 간주합니다. 예를 들어 동일한 사용자가 미국 및 오스트레일리아에서 동시에 합법적으로 전화를 걸 수 없습니다. 

이러한 경우를 확인하려면 스트리밍 데이터의 셀프 조인을 사용하여 `CallRecTime` 값에 따라 스트림을 셀프 조인합니다. 그런 다음 `CallingIMSI` 값(발신 번호)이 동일하지만 `SwitchNum` 값(발신 국가)은 다른 호출 레코드를 찾을 수 있습니다.

스트리밍 데이터에 조인을 사용할 경우 조인은 일치하는 행이 시간상으로 얼마나 분리할 수 있는지 정도에 대한 몇 가지 한도를 제공해야 합니다. (앞에서 설명한 대로 스트리밍 데이터는 사실상 무한함) 관계에 대한 시간 범위는 조인의 `ON` 절 내부에 `DATEDIFF` 함수를 사용하여 지정됩니다. 이 경우 조인은 호출 데이터의 5초 간격을 기준으로 합니다.

1. 코드 편집기에서 쿼리를 다음으로 변경합니다. 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    이 쿼리는 조인에서 `DATEDIFF` 함수를 제외하고 SQL 조인과 유사합니다. 이 `DATEDIFF` 버전은 Streaming Analytics에 국한되며 `ON...BETWEEN` 절에 표시되어야 합니다. 매개 변수는 시간 단위(이 예제에서는 초)와 조인의 두 원본에 대한 별칭입니다. 표준 SQL `DATEDIFF` 함수와는 다릅니다.

    `WHERE` 절에는 사기성 호출에 플래그를 지정하는 조건이 포함되며 발신 스위치는 동일하지 않습니다. 

2. **테스트**를 다시 클릭합니다. 

   ![셀프 조인에 대한 Stream Analytics 작업 출력, 6개 레코드가 생성된 것으로 표시](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. **저장**을 클릭하여 셀프 조인 쿼리를 Streaming Analytics 작업의 일부로 저장합니다. (샘플 데이터를 저장하지 않음)

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>변환된 데이터를 저장할 출력 싱크 만들기

이벤트 스트림, 이벤트를 수집할 이벤트 허브 입력 및 스트림 변환을 수행할 쿼리를 정의했습니다. 마지막 단계는 작업의 출력 싱크(즉, 변환된 스트림을 기록할 위치)를 정의하는 것입니다. 

SQL Server Database, Table Storage, Data Lake Storage, Power BI 및 다른 이벤트 허브 등 많은 리소스를 출력 싱크로 사용할 수 있습니다. 이 자습서에서는 구조화되지 않은 데이터를 수용하므로 추가 분석을 위한 이벤트 정보를 수집하는 데 일반적으로 사용되는 Azure Blob Storage에 스트림을 기록합니다.

기존 Blob Storage 계정이 있는 경우 기존 계정을 사용할 수 있습니다. 이 자습서에서는 새 저장소 계정을 만드는 방법을 배웁니다.

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage 계정 만들기

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **저장소** > **저장소 계정**을 선택합니다. 스토리지 계정 작업 페이지에서 **이름**을"asaehstorage"로 설정하고, **위치**는 "미국 동부"로 설정하고, **리소스 그룹**을 "asa-eh-ns-rg"로 설정합니다(향상된 성능을 위해 스트리밍 작업과 동일한 리소스 그룹의 스토리지 계정 호스트). 나머지 설정은 해당 기본값으로 유지할 수 있습니다.  

   ![Azure Portal에서 스토리지 계정 만들기](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Azure Portal에서 Streaming Analytics 작업 창으로 돌아갑니다. (창을 닫은 경우 **모든 리소스** 창에서 `asa_frauddetection_job_demo` 검색)

3. **작업 토폴로지** 섹션에서 **출력** 상자를 클릭합니다.

4. **출력** 창에서 **추가**를 클릭하고 **Blob Storage**를 선택합니다. 그런 후 다음 정보로 새 출력 페이지를 채웁니다.

   |**설정**  |**제안 값**  |**설명**  |
   |---------|---------|---------|
   |출력 별칭  |  CallStream-FraudulentCalls   |  작업의 출력을 식별하는 이름을 입력합니다.   |
   |구독   |  \<구독\> |  만든 저장소 계정이 있는 Azure 구독을 선택합니다. 동일한 또는 다른 구독에 저장소 계정이 있을 수 있습니다. 이 예제에서는 동일한 구독에 저장소 계정을 만들었다고 가정합니다. |
   |Storage 계정  |  asaehstorage |  만든 저장소 계정의 이름을 입력합니다. |
   |컨테이너  | asa-fraudulentcalls-demo | 새로 만들기를 선택하고 컨테이너 이름을 입력합니다. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. **저장**을 클릭합니다. 


## <a name="start-the-streaming-analytics-job"></a>Streaming Analytics 작업 시작

이제 작업이 구성됩니다. 입력(이벤트 허브), 변환(사기성 호출을 찾는 쿼리), 출력(Blob Storage)을 지정했습니다. 이제 작업을 시작할 수 있습니다. 

1. TelcoGenerator 앱이 실행되고 있는지 확인합니다.

2. 작업 창에서 **시작**을 클릭합니다. **작업 시작** 창에서 작업 출력 시작 시간으로 **지금**을 선택합니다. 

   ![Stream Analytics 작업 시작](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>변환된 데이터 검사

이제 Streaming Analytics 작업을 완료합니다. 작업에서 전화 통화 메타데이터 스트림을 검사하여 실시간으로 사기성 전화 통화를 찾고 이러한 사기성 통화에 대한 정보를 저장소에 기록합니다. 

이 자습서를 완료하기 위해 Streaming Analytics 작업으로 캡처된 데이터를 살펴보려 할 수 있습니다. 데이터는 청크(파일)로 Azure Blob Storage에 기록됩니다. Azure Blob Storage를 읽는 데 아무 도구나 사용할 수 있습니다. 필수 조건 섹션에서 설명한 대로, Visual Studio에서 Azure 확장을 사용하거나 [Azure Storage 탐색기](https://storageexplorer.com/) 또는 [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage)과 같은 도구를 사용할 수 있습니다. 

Blob Storage에서 파일 내용을 검사할 때 다음과 같은 항목이 표시됩니다.

   ![Streaming Analytics 출력이 있는 Azure Blob Storage](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>리소스 정리

사기 감지 시나리오를 진행하고 이 자습서에서 만든 리소스를 사용하는 추가 문서가 있습니다. 계속하려면 **다음 단계** 아래의 제안 사항을 참조하세요.

그러나 작업을 수행했고 만든 리소스가 필요하지 않는 경우 불필요한 Azure 비용이 발생하지 않도록 해당 항목을 삭제할 수 있습니다. 이 경우 다음을 수행하는 것이 좋습니다.

1. Streaming Analytics 작업을 중지합니다. **작업** 창의 맨 위에서 **중지**를 클릭합니다.
2. Telco Generator 앱을 중지합니다. 앱을 시작한 명령 창에서 Ctrl + C를 누릅니다.
3. 이 자습서에 대해 새 Blob Storage 계정을 만든 경우 계정을 삭제합니다. 
4. Streaming Analytics 작업을 삭제합니다.
5. 이벤트 허브를 삭제합니다.
6. 이벤트 허브 네임스페이스를 삭제합니다.

## <a name="get-support"></a>지원 받기

추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 통해 이 자습서를 진행할 수 있습니다.

* [Stream Analytics 및 Power BI: 스트리밍 데이터에 대한 실시간 분석 대시보드](stream-analytics-power-bi-dashboard.md). 이 문서에서는 실시간 시각화 및 분석을 위해 Stream Analytics 작업의 TelCo 출력을 Power BI로 보내는 방법을 보여 줍니다.

일반적인 Stream Analytics에 대한 자세한 내용은 다음 문서를 살펴보세요.

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

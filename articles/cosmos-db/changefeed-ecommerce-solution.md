---
title: Azure Cosmos DB 변경 피드를 사용하여 실시간 데이터 분석 시각화
description: 이 문서에서는 소매 회사에서 변경 피드를 사용하여 사용자 패턴을 파악하고 실시간 데이터 분석 및 시각화를 수행하는 방법에 대해 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: 379c7913f803c599865df080524da5c3fb1d0e52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60893581"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Azure Cosmos DB 변경 피드를 사용하여 실시간 데이터 분석 시각화

Azure Cosmos DB 변경 피드는 레코드가 만들어지거나 수정될 때 Azure Cosmos DB 컨테이너에서 해당 레코드를 지속적이고 증분적으로 가져오는 메커니즘입니다. 변경 피드 지원은 컨테이너의 변경 내용을 수신하여 작동합니다. 그런 다음 변경된 문서가 수정된 순서로 정렬된 목록이 출력됩니다. 변경 피드에 대한 자세한 내용은 [변경 피드 사용](change-feed.md) 문서를 참조하세요. 

이 문서에서는 전자상거래 회사에서 변경 피드를 사용하여 사용자 패턴을 파악하고 실시간 데이터 분석 및 시각화를 수행하는 방법에 대해 설명합니다. 사용자가 항목을 조회하거나, 자신의 카트에 항목을 추가하거나, 항목을 구입하는 것과 같은 이벤트를 분석합니다. 이러한 이벤트 중 하나가 발생하면 새 레코드가 만들어지고 변경 피드에서 해당 레코드를 기록합니다. 변경 피드는 회사 성과 및 활동을 분석하는 메트릭을 시각화하는 일련의 단계를 트리거합니다. 시각화할 수 있는 샘플 메트릭에는 수익, 고유 사이트 방문자, 가장 인기 있는 항목 및 카트에 추가하거나 구입한 항목을 비교하여 조회되는 평균 가격이 포함됩니다. 이러한 샘플 메트릭을 통해 전자 상거래 회사는 사이트 인기도를 평가하고 광고 및 가격 전략을 개발하며, 투자할 재고와 관련된 의사 결정을 내릴 수 있습니다.

시작하기 전에 솔루션에 대한 비디오를 시청하는 데 관심이 있으면 다음 비디오를 참조하세요.

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>솔루션 구성 요소
다음 다이어그램에서는 솔루션과 관련된 데이터 흐름과 구성 요소를 나타냅니다.

![프로젝트 시각적 개체](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **데이터 생성:** 데이터 시뮬레이터는 사용자가 항목을 조회하고, 자신의 카트에 항목을 추가하고, 항목을 구입하는 것과 같은 이벤트를 나타내는 소매 데이터를 생성하는 데 사용됩니다. 데이터 생성기를 사용하여 큰 샘플 데이터 집합을 생성할 수 있습니다. 생성된 샘플 데이터에는 다음 형식의 문서가 포함되어 있습니다.
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** 생성된 데이터는 Azure Cosmos DB 컬렉션에 저장됩니다.  

3. **변경 피드:** 변경 피드는 Azure Cosmos DB 컬렉션의 변경 내용을 수신 대기합니다. 새 문서가 컬렉션에 추가될 때마다, 즉 사용자가 항목을 조회하거나 자신의 카트에 항목을 추가하거나 항목을 구입하는 것과 같은 이벤트가 발생하면 변경 피드에서 [Azure Function](../azure-functions/functions-overview.md)을 트리거합니다.  

4. **Azure Function:** Azure Function은 새 데이터를 처리하여 [Azure Event Hub](../event-hubs/event-hubs-about.md)로 보냅니다.  

5. **Event Hub:** Azure Event Hub는 이러한 이벤트를 저장하고 추가 분석을 수행하기 위해 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)로 보냅니다.  

6. **Azure Stream Analytics:** Azure Stream Analytics는 이벤트를 처리하고 실시간 데이터 분석을 수행하기 위한 쿼리를 정의합니다. 그런 다음, 이 데이터는 [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop)로 보내집니다.  

7. **Power BI:** Power BI는 Azure Stream Analytics에서 보낸 데이터를 시각화하는 데 사용됩니다. 메트릭이 실시간으로 변하는 상황을 확인할 수 있는 대시보드를 작성할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

* Microsoft.NET Framework 4.7.1 이상

* Microsoft.NET Core 2.1 이상

* Visual Studio(유니버설 Windows 플랫폼 개발, .NET 데스크톱 개발, ASP.NET 및 웹 개발 워크로드 포함)

* Microsoft Azure 구독

* Microsoft Power BI 계정

* GitHub에서 [Azure Cosmos DB 변경 피드 랩](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample)을 다운로드합니다. 

## <a name="create-azure-resources"></a>Azure 리소스 만들기 

솔루션에 필요한 Azure 리소스, 즉 Azure Cosmos DB, Storage 계정, Event Hub, Stream Analytics를 만듭니다. Azure Resource Manager 템플릿을 통해 이러한 리소스를 배포합니다. 이러한 리소스를 배포하려면 다음 단계를 수행합니다. 

1. Windows PowerShell 실행 정책을 **무제한**으로 설정합니다. 이렇게 하려면 **Windows PowerShell을 관리자 권한으로** 열고 다음 명령을 실행합니다.

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. 이전 단계에서 다운로드한 GitHub 리포지토리에서 **Azure Resource Manager** 폴더로 이동하여 **parameters.json**이라는 파일을 엽니다.  

3. **parameters.json** 파일에 표시된 대로 cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name 매개 변수에 대한 값을 제공합니다. 나중에 각 리소스에 지정한 이름을 사용해야 합니다.  

4. **Windows PowerShell**에서 **Azure Resource Manager** 폴더로 이동하고 다음 명령을 실행합니다.

   ```powershell
   .\deploy.ps1
   ```
5. 메시지가 표시되면 리소스 그룹 이름에 대해 Azure **구독 ID**, **changefeedlab**을 입력하고 배포 이름에 대해 **run1**을 입력합니다. 리소스 배포를 시작하여 완료하는 데 최대 10분이 걸릴 수 있습니다.

## <a name="create-a-database-and-the-collection"></a>데이터베이스 및 컬렉션 만들기

이제 전자 상거래 사이트 이벤트를 저장할 컬렉션을 만들어 보겠습니다. 사용자가 항목을 조회하거나 자신의 카트에 항목을 추가하거나 항목을 구입하면, 컬렉션에서 작업("viewed(조회)", "added(추가)" 또는 "purchased(구입)"), 관련 항목의 이름, 관련 항목의 가격 및 관련 사용자 카트의 ID 번호가 포함된 레코드를 받습니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하고, 템플릿 배포를 통해 만들어진 **Azure Cosmos DB 계정**을 찾습니다.  

2. **데이터 탐색기** 창에서 **새 컬렉션**을 선택하고 다음 세부 정보로 양식을 채웁니다.  

   * **데이터베이스 ID** 필드에 대해 **새로 만들기**를 선택한 다음, **changefeedlabdatabase**를 입력합니다. **데이터베이스 처리량 프로비전** 상자는 선택하지 않은 상태로 둡니다.  
   * **컬렉션 ID** 필드에 대해 **changefeedlabcollection**을 입력합니다.  
   * **파티션 키** 필드에 대해 **/Item**을 입력합니다. 대/소문자를 구분하므로 정확하게 입력해야 합니다.  
   * **처리량** 필드에 대해 **10000**을 입력합니다.  
   * **확인** 단추를 선택합니다.  

3. 다음으로, 변경 피드 처리를 위해 **leases**(임대)라는 또 다른 컬렉션을 만듭니다. leases 컬렉션은 여러 작업자 간의 변경 피드 처리를 조정합니다. 임대는 개별 컬렉션을 사용하여 저장됩니다(각 파티션마다 하나의 임대).  

4. **데이터 탐색기** 창으로 돌아가서 **새 컬렉션**을 선택하고, 다음 세부 정보로 양식을 채웁니다.

   * **데이터베이스 ID** 필드에 대해 **기존 항목 사용**을 선택한 다음, **changefeedlabdatabase**를 입력합니다.  
   * **컬렉션 ID** 필드에 대해 **leases**를 입력합니다.  
   * **저장소 용량**에 대해 **고정**을 선택합니다.  
   * **처리량** 필드는 기본값으로 설정된 채로 둡니다.  
   * **확인** 단추를 선택합니다.

## <a name="get-the-connection-string-and-keys"></a>연결 문자열 및 키 가져오기

### <a name="get-the-azure-cosmos-db-connection-string"></a>Azure Cosmos DB 연결 문자열 가져오기

1. [Azure Portal](https://portal.azure.com/)로 이동하고, 템플릿 배포를 통해 만들어진 **Azure Cosmos DB 계정**을 찾습니다.  

2. **키** 창으로 이동하고, [기본 연결 문자열]을 복사하여 랩 전체에서 액세스할 수 있는 메모장이나 다른 문서에 붙여넣습니다. **Cosmos DB 연결 문자열**이라는 레이블을 지정해야 합니다. 문자열은 나중에 코드에 복사해야 하므로 메모를 작성하여 저장한 위치를 적어 둡니다.

### <a name="get-the-storage-account-key-and-connection-string"></a>저장소 계정 키 및 연결 문자열 가져오기

Azure Storage 계정을 사용하면 사용자가 데이터를 저장할 수 있습니다. 이 랩에서는 저장소 계정을 사용하여 Azure Function에서 사용하는 데이터를 저장합니다. 컬렉션이 수정되면 Azure Function이 트리거됩니다.

1. 리소스 그룹으로 돌아가서 이전에 만든 저장소 계정을 엽니다.  

2. 왼쪽 메뉴에서 **액세스 키**를 선택합니다.  

3. **키 1**에 있는 값을 메모장 또는 랩 전체에서 액세스할 수 있는 다른 문서에 복사합니다. **키**에는 **저장소 키**, **연결 문자열**에는 **저장소 연결 문자열**이라는 레이블을 지정해야 합니다. 이러한 문자열은 나중에 코드에 복사해야 하므로 메모를 작성하여 저장한 위치를 적어 둡니다.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Event Hub 네임스페이스 연결 문자열 가져오기

Azure Event Hub는 이벤트 데이터를 받고, 저장하고, 처리하고, 전달합니다. 이 랩에서는 새 이벤트가 발생할 때마다, 즉 사용자가 항목을 조회하거나, 사용자의 카트에 추가하거나, 구입할 때마다 Azure Event Hub에서 문서를 받은 다음, 해당 문서를 Azure Stream Analytics로 전달합니다.

1. 리소스 그룹으로 돌아가서 이전 실습에서 만들고 이름을 지정한 **Event Hub 네임스페이스**를 엽니다.  

2. 왼쪽 메뉴에서 **공유 액세스 정책**을 선택합니다.  

3. **RootManageSharedAccessKey**를 선택합니다. **연결 문자열 - 기본 키**를 메모장이나 랩 전체에서 액세스할 수 있는 다른 문서에 복사합니다. **Event Hub 네임스페이스** 연결 문자열이라는 레이블을 지정해야 합니다. 문자열은 나중에 코드에 복사해야 하므로 메모를 작성하여 저장한 위치를 적어 둡니다.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>변경 피드를 읽도록 Azure Function 설정

Cosmos DB 컬렉션에서 새 문서가 만들어지거나 현재 문서가 수정되면 변경 피드에서 수정된 문서를 컬렉션 변경 기록에 자동으로 추가합니다. 이제 변경 피드를 처리하는 Azure Function을 작성하고 실행합니다. 만든 컬렉션에서 문서가 만들어지거나 수정되면 변경 피드에서 Azure Function을 트리거합니다. 그러면 Azure Function에서 수정된 문서를 Event Hub로 보냅니다.

1. 디바이스에서 복제한 리포지토리로 돌아갑니다.  

2. **ChangeFeedLabSolution.sln**이라는 파일을 마우스 오른쪽 단추로 클릭하고, **Visual Studio에서 열기**를 선택합니다.  

3. Visual Studio에서 **local.settings.json**으로 이동합니다. 그런 다음, 앞에서 기록한 값을 사용하여 빈 칸을 채웁니다.  

4. **ChangeFeedProcessor.cs**로 이동합니다. **실행** 함수에 대한 매개 변수에서 다음 작업을 수행합니다.  

   * **여기에 컬렉션 이름을 입력하십시오** 텍스트를 사용자의 컬렉션 이름으로 바꿉니다. 이전 지침을 따른 경우 컬렉션 이름은 changefeedlabcollection입니다.  
   * **여기에 임대 컬렉션 이름을 입력하십시오** 텍스트를 사용자의 임대 컬렉션 이름으로 바꿉니다. 이전 지침을 따른 경우 임대 컬렉션 이름은 **leases**입니다.  
   * Visual Studio 위쪽의 녹색 화살표 왼쪽에 있는 [시작 프로젝트] 상자에서 **ChangeFeedFunction**이 표시되는지 확인합니다.  
   * 페이지 위쪽의 **시작**을 선택하여 프로그램을 실행합니다.  
   * 콘솔 앱에서 "작업 호스트가 시작되었습니다"라고 표시되면 함수가 실행되고 있음을 확인할 수 있습니다.

## <a name="insert-data-into-azure-cosmos-db"></a>Azure Cosmos DB에 데이터 삽입 

변경 피드에서 전자 상거래 사이트의 새 작업을 처리하는 방법을 보려면, 제품 카탈로그의 항목을 조회하는 사용자를 나타내는 데이터를 시뮬레이션하고, 자신의 카트에 해당 항목을 추가하고, 해당 카트의 항목을 구입해야 합니다. 이 데이터는 임의적이며, 전자 상거래 사이트의 데이터를 복제하기 위한 것입니다.

1. [파일 탐색기]에서 리포지토리로 돌아가서 **ChangeFeedFunction.sln**을 마우스 오른쪽 단추로 클릭하여 새 Visual Studio 창에서 이 파일을 다시 엽니다.  

2. **App.config** 파일로 이동합니다. `<appSettings>` 블록 내에서 이전에 검색한 Azure Cosmos DB 계정의 엔드포인트와 고유한 **기본 키**를 추가합니다.  

3. **컬렉션** 및 **데이터베이스** 이름을 추가합니다. 이름을 달리 지정하지 않는 한 이러한 이름은 **changefeedlabcollection** 및 **changefeedlabdatabase**여야 합니다.

   ![연결 문자열 업데이트](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. 편집된 모든 파일에 변경 내용을 저장합니다.  

5. Visual Studio 위쪽의 녹색 화살표 왼쪽에 있는 **시작 프로젝트** 상자에서 **DataGenerator**가 표시되는지 확인합니다. 그런 다음, 페이지 위쪽의 **시작**을 선택하여 프로그램을 실행합니다.  
 
6. 프로그램이 실행될 때까지 기다립니다. 별은 데이터가 들어오고 있다는 것을 의미합니다! 프로그램을 계속 실행합니다. 많은 양의 데이터가 수집되는 것이 중요합니다.  

7. [Azure Portal](https://portal.azure.com/), 리소스 그룹 내의 Cosmos DB 계정, **데이터 탐색기**로 차례로 이동하면 **changefeedlabcollection**에 가져온 임의 데이터가 표시됩니다.
 
   ![포털에서 생성된 데이터](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>스트림 분석 작업 설정

Azure Stream Analytics는 스트리밍 데이터를 실시간으로 처리할 수 있도록 완벽하게 관리되는 클라우드 서비스입니다. 이 랩에서는 스트림 분석을 사용하여 Event Hub에서 새 이벤트(예: 항목을 조회하거나 카트에 추가하거나 구입하는 경우)를 처리하고, 해당 이벤트를 실시간 데이터 분석에 통합하고, 시각화를 위해 Power BI로 보냅니다.

1. [Azure Portal](https://portal.azure.com/)에서 리소스 그룹, **streamjob1**(이전 실습에서 만든 스트림 분석 작업)로 차례로 이동합니다.  

2. 아래와 같이 **입력**을 선택합니다.  

   ![입력 만들기](./media/changefeed-ecommerce-solution/create-input.png)

3. **+ 스트림 입력 추가**를 선택합니다. 그런 다음, 드롭다운 메뉴에서 **Event Hub**를 선택합니다.  

4. 새 입력 양식을 다음 세부 정보로 채웁니다.

   * **입력** 별칭 필드에서 **입력**을 입력합니다.  
   * **구독에서 Event Hub를 선택합니다** 옵션을 선택합니다.  
   * **구독** 필드를 사용자의 구독으로 설정합니다.  
   * **Event Hub 네임스페이스** 필드에서 이전 실습 중에 만든 Event Hub 네임스페이스의 이름을 입력합니다.  
   * **Event Hub 이름** 필드에서 **기존 항목 사용** 옵션을 선택하고, 드롭다운 메뉴에서 **event-hub1**을 선택합니다.  
   * **Event Hub 정책** 이름 필드를 기본값으로 둡니다.  
   * **이벤트 serialization 형식**을 **JSON**으로 둡니다.  
   * **인코딩 필드**는 **UTF-8**로 설정된 채로 둡니다.  
   * **이벤트 압축 유형** 필드는 **없음**으로 설정된 채로 둡니다.  
   * **저장** 단추를 선택합니다.

5. 스트림 분석 작업 페이지로 돌아가서 **출력**을 선택합니다.  

6. **+추가**를 선택합니다. 그런 다음, 드롭다운 메뉴에서 **Power BI**를 선택합니다.  

7. 새 Power BI 출력을 만들어 평균 가격을 시각화하려면 다음 작업을 수행합니다.

   * **출력 별칭** 필드에서 **averagePriceOutput**을 입력합니다.  
   * **그룹 작업 영역** 필드를 **작업 영역을 로드하도록 연결에 권한 부여**로 설정된 채로 둡니다.  
   * **데이터 세트 이름** 필드에서 **averagePrice**를 입력합니다.  
   * **테이블 이름** 필드에서 **averagePrice**를 입력합니다.  
   * **권한 부여** 단추를 선택한 다음, 지침에 따라 Power BI에 대한 연결 권한을 부여합니다.  
   * **저장** 단추를 선택합니다.  

8. 그런 다음, **streamjob1**로 돌아가서 **쿼리 편집**을 선택합니다.

   ![쿼리 편집](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. 다음 쿼리를 쿼리 창에 붙여넣습니다. **AVERAGE PRICE**(평균 가격) 쿼리는 사용자가 조회하는 모든 항목, 자신의 카트에 추가한 모든 항목 및 구입한 모든 항목의 평균 가격을 계산합니다. 이 메트릭은 전자 상거래 회사에서 판매 가격 및 투자할 재고를 결정하는 데 도움이 됩니다. 예를 들어 조회하는 항목의 평균 가격이 구입한 항목의 평균 가격보다 훨씬 높은 경우 회사에서 평균 가격이 낮은 항목을 재고에 추가하도록 선택할 수 있습니다.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. 그런 다음, 왼쪽 위 모서리에서 **저장**을 선택합니다.  

11. 이제 **streamjob1**로 돌아가서 페이지 위쪽의 **시작** 단추를 선택합니다. Azure Stream Analytics에서 시작하는 데 몇 분이 걸릴 수 있지만 결국에는 "시작 중"에서 "실행 중"으로 변경됩니다.

## <a name="connect-to-power-bi"></a>Power BI에 연결

Power BI는 데이터를 분석하고 인사이트를 공유하는 비즈니스 분석 도구 제품군입니다. 분석된 데이터를 전략적으로 시각화할 수 있는 방법의 좋은 예입니다.

1. Power BI에 로그인하고, 페이지의 왼쪽에서 메뉴를 열어 **내 작업 영역**으로 이동합니다.  

2. 오른쪽 위 모서리에서 **+ 만들기**를 선택한 다음, **대시보드**를 선택하여 대시보드를 만듭니다.  

3. 오른쪽 위에서 **+ 타일 추가**를 선택합니다.  

4. **사용자 지정 스트리밍 데이터**를 선택한 다음, **다음** 단추를 선택합니다.  
 
5. **데이터 세트**에서 **averagePrice**를 선택한 다음, **다음**을 선택합니다.  

6. **시각화 형식** 필드의 드롭다운 메뉴에서 **묶은 가로 막대형 차트**를 선택합니다. **축** 아래에서 작업을 추가합니다. **범례**는 아무 것도 추가하지 않고 건너뜁니다. 그런 다음, **값**이라는 다음 섹션 아래에서 **avg**를 추가합니다. **다음**을 선택하고, 차트 제목을 지정한 다음, **적용**을 선택합니다. 대시보드에 새 차트가 표시됩니다!  

7. 이제 더 많은 메트릭을 시각화하려면 **streamjob1**로 돌아가서 다음 필드를 통해 세 개의 출력을 추가로 만들 수 있습니다.

   a. **출력 별칭:** incomingRevenueOutput, 데이터 세트 이름: incomingRevenue, 테이블 이름: incomingRevenue  
   b. **출력 별칭:** top5Output, 데이터 세트 이름: top5, 테이블 이름: top5  
   다. **출력 별칭:** uniqueVisitorCountOutput, 데이터 세트 이름: uniqueVisitorCount, 테이블 이름: uniqueVisitorCount

   그런 다음, **쿼리 편집**을 선택하고 이미 작성한 쿼리 **위에** 다음 쿼리를 붙여넣습니다.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   TOP 5(상위 5개) 쿼리는 구입 횟수에 따라 순위가 지정된 상위 5개 항목을 계산합니다. 이 메트릭은 전자 상거래 회사에서 가장 인기 있고 회사의 광고, 가격 책정 및 재고 결정에 영향을 줄 수 있는 항목을 평가하는 데 도움이 됩니다.

   REVENUE(수익) 쿼리는 매분마다 구입한 모든 항목의 가격을 합하여 수익을 계산합니다. 이 메트릭은 전자 상거래 회사에서 재무 성과를 평가하고 하루 중에 가장 많은 수익에 기여하는 시간도 파악하는 데 도움이 됩니다. 이는 전체 회사 전략, 특히 마케팅에 영향을 줄 수 있습니다.

   UNIQUE VISITORS(고유 방문자) 쿼리는 고유한 카트 ID를 검색하여 5초마다 사이트에 있는 고유 방문자의 수를 계산합니다. 이 메트릭은 전자 상거래 회사에서 사이트 활동을 평가하고 더 많은 고객을 확보하는 방법을 전략화하는 데 도움이 됩니다.

8. 이제 이러한 데이터 세트에 대한 타일을 추가할 수도 있습니다.

   * [상위 5개]의 경우 항목을 축으로, 개수를 값으로 사용하여 묶은 세로 막대형 차트를 작성하는 것이 좋습니다.  
   * [수익]의 경우 시간을 축으로, 가격의 합계를 값으로 사용하여 꺾은선형 차트를 작성하는 것이 좋습니다. 가능한 많은 정보를 제공하기 위해 표시할 시간 범위가 최대한 커야 합니다.  
   * [고유 방문자]의 경우 고유 방문자 수를 값으로 사용하여 카드 시각화를 작성하는 것이 좋습니다.

   샘플 대시보드에서 이러한 차트가 표시되는 방식은 다음과 같습니다.

   ![시각화](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>선택 사항: 전자 상거래 사이트를 통해 시각화

이제 새 데이터 분석 도구를 사용하여 실제 전자 상거래 사이트에 연결하는 방법을 살펴보겠습니다. 전자 상거래 사이트를 구축하려면 Azure Cosmos DB 데이터베이스를 사용하여 제품 범주(여성, 남성, 남녀 공용) 목록, 제품 카탈로그 및 가장 인기 있는 항목 목록을 저장합니다.

1. [Azure Portal](https://portal.azure.com/), **Cosmos DB 계정**, **데이터 탐색기**로 차례로 다시 이동합니다.  

   **changefeedlabdatabase** - **products** 및 **categories** 아래에 [고정] 저장소 용량이 있는 두 개의 컬렉션을 추가합니다.

   **changefeedlabdatabase** 아래에 파티션 키로 **topItems** 및 **/Item**이라는 다른 컬렉션을 추가합니다.

2. **topItems** 컬렉션을 선택하고 **배율 및 설정** 아래에서 topItems가 매 30초마다 업데이트되도록 **TTL(Time to live)** 을 **30초**로 설정합니다.

   ![TTL(Time to live)](./media/changefeed-ecommerce-solution/time-to-live.png)

3. **topItems** 컬렉션을 가장 자주 구입한 항목으로 채우려면 **streamjob1**로 돌아가서 새 **출력**을 추가합니다. **Cosmos DB**를 선택합니다.

4. 아래와 같이 필수 필드를 채웁니다.

   ![Cosmos 출력](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. 랩의 이전 단계에서 선택적 TOP 5 쿼리를 추가한 경우 5a단계로 진행합니다. 그렇지 않은 경우 5b단계로 진행합니다.

   5a. **streamjob1**에서 **쿼리 편집**을 선택하고, Azure Stream Analytics 쿼리 편집기에서 다음 쿼리를 TOP 5 쿼리 아래이지만 나머지 쿼리의 위에 붙여넣습니다.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. **streamjob1**에서 **쿼리 편집**을 선택하고, Azure Stream Analytics 쿼리 편집기에서 다음 쿼리를 다른 모든 쿼리 위에 붙여넣습니다.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. **EcommerceWebApp.sln**을 열고, **솔루션 탐색기**에서 **Web.config** 파일로 이동합니다.  

7. `<appSettings>` 블록 내의 **여기에 URI를 입력하십시오** 및 **여기에 기본 키를 입력하십시오**에서 이전에 저장한 **URI**와 **기본 키**를 추가합니다. 그런 다음, **데이터베이스 이름**과 **컬렉션 이름**을 표시된 대로 추가합니다. 이름을 달리 지정하지 않는 한 이러한 이름은 **changefeedlabdatabase** 및 **changefeedlabcollection**여야 합니다.

   **제품 컬렉션 이름**, **범주 컬렉션 이름** 및 **상위 항목 컬렉션 이름**을 표시된 대로 채웁니다. 이름을 달리 지정하지 않는 한 이러한 이름은 **products, categories 및 topItems**이어야 합니다.  

8. **EcommerceWebApp.sln** 내에서 **체크 아웃 폴더**로 이동하여 엽니다. 그런 다음, 해당 폴더 내에서 **Web.config** 파일을 엽니다.  

9. 앞에서 표시된 위치에 저장한 **URI**와 **기본 키**를 `<appSettings>` 블록 내에 추가합니다. 그런 다음, **데이터베이스 이름**과 **컬렉션 이름**을 표시된 대로 추가합니다. (이름을 달리 지정하지 않는 한 이러한 이름은 **changefeedlabdatabase** 및 **changefeedlabcollection**이어야 합니다.)  

10. 페이지 위쪽의 **시작**을 눌러 프로그램을 실행합니다.  

11. 이제 전자 상거래 사이트를 사용해 볼 수 있습니다. 항목을 조회하거나 자신의 카트에 항목을 추가하거나 카트에 있는 항목의 수량을 변경하거나 항목을 구입하는 경우, 이러한 이벤트는 Cosmos DB 변경 피드를 통해 Event Hub, ASA 및 Power BI로 전달됩니다. DataGenerator를 계속 실행하여 중요한 웹 트래픽 데이터를 생성하고 전자 상거래 사이트에서 현실적인 "핫 제품" 집합을 제공하는 것이 좋습니다.

## <a name="delete-the-resources"></a>리소스 삭제

이 랩에서 만든 리소스를 삭제하려면 [Azure Portal](https://portal.azure.com/)에서 리소스 그룹으로 이동한 다음, 페이지 위쪽의 메뉴에서 **리소스 그룹** 삭제를 선택하고, 제시되는 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계 
  
* 변경 피드에 대해 자세히 알아보기 위해 [Azure Cosmos DB에서 변경 피드 지원 사용](change-feed.md)을 참조하세요. 
* Azure Cosmos DB를 사용하여 의료 기관용 [피드 알림 솔루션 변경](change-feed-hl7-fhir-logic-apps.md)합니다.

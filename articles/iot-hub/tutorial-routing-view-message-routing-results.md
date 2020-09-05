---
title: Azure IoT Hub 메시지 라우팅 결과 보기(.NET) | Microsoft Docs
description: 자습서의 1부를 사용하여 모든 리소스를 설정한 후 메시지를 Azure Stream Analytics로 라우팅하고 PowerBI에서 결과를 볼 수 있는 기능을 추가합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: fb86b25fc24ea505ae258cc44d5554a019723af6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019179"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>자습서: 2부 - 라우팅된 메시지 보기

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>메시지 라우팅에 대한 규칙

다음은 메시지 라우팅에 대한 규칙입니다. 이 자습서의 1부에서 설정되었으며, 두 번째 부분에서는 사용 방법을 볼 수 있습니다.

|값 |결과|
|------|------|
|level="storage" |Azure Storage에 씁니다.|
|level="critical" |Service Bus 큐에 씁니다. Logic App은 큐의 메시지를 검색하고 Office 365를 사용하여 메시지를 이메일로 전송합니다.|
|기본값 |Power BI를 사용하여 이 데이터를 표시합니다.|

이제 메시지가 라우팅될 리소스를 만들고, 앱을 실행하여 허브에 메시지를 보내고, 실제 라우팅을 살펴봅니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기  

Service Bus 큐는 중요한 것으로 지정된 메시지를 받는 데 사용됩니다. Service Bus 큐를 모니터링하고 메시지가 큐에 추가될 때 이메일을 전송하는 Logic App을 설정합니다.

1. [Azure Portal](https://portal.azure.com)에서 **+ 리소스 만들기**를 선택합니다. 검색 상자에 **논리 앱**을 입력하고 Enter 키를 클릭합니다. 표시되는 검색 결과에서 Logic App을 선택한 다음, **만들기**를 선택하여 **논리 앱 만들기** 창으로 계속 진행합니다. 필드를 입력합니다.

   **Name**: 이 필드는 논리 앱의 이름입니다. 이 자습서에서는 **ContosoLogicApp**을 사용합니다.

   **구독**: Azure 구독을 선택합니다.

   **리소스 그룹**: **기존 항목 사용**을 선택하고, 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다.

   **위치**: 사용자의 위치를 사용합니다. 이 자습서에서는 **미국 서부**를 사용합니다.

   **Log Analytics**: 이 토글은 해제해야 합니다.

   ![논리 앱 만들기 화면](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   **만들기**를 선택합니다. 앱을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

2. 이제 Logic App으로 이동합니다. Logic App을 가장 쉽게 가져오려면 **리소스 그룹**을 선택하고, 사용자의 리소스 그룹을 선택(이 자습서에서는 **ContosoResources** 사용)한 다음, 리소스의 목록에서 Logic App을 선택합니다. 

    Logic Apps 디자이너 페이지가 표시됩니다(전체 페이지를 표시하려면 오른쪽으로 스크롤해야 함). Logic Apps 디자이너 페이지에서 **빈 Logic App +** 이라는 타일이 표시될 때까지 아래로 스크롤한 후 선택합니다. 기본 탭은 "사용자용"입니다. 이 창이 비어 있으면 **모두**를 선택하여 사용 가능한 커넥터와 트리거를 모두 표시합니다.

3. 커넥터 목록에서 **Service Bus**를 선택합니다.

   ![커넥터 목록](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. 트리거의 목록이 표시됩니다. **메시지가 큐에 수신되는 경우(자동 완성)/Service Bus**를 선택합니다.

   ![Service Bus에 대한 트리거 목록](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. 다음 화면에서 연결 이름을 입력합니다. 이 자습서에서는 **ContosoConnection**을 사용합니다.

   ![Service Bus 큐에 대한 연결 설정](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Service Bus 네임스페이스를 선택합니다. 이 자습서에서는 **ContosoSBNamespace**를 사용합니다. 네임스페이스를 선택하면 포털이 Service Bus 네임스페이스를 쿼리하여 키를 검색합니다. **RootManageSharedAccessKey**를 선택하고 **만들기**를 선택합니다.

   ![연결 설정을 마치는 중](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. 다음 화면에서 큐의 이름을 드롭다운 목록에서 선택합니다(이 자습서에서는 **contososbqueue** 사용). 필드의 나머지 부분에 기본값을 사용할 수 있습니다.

   ![큐 옵션](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. 이제 메시지가 큐에 수신되면 이메일을 보내는 동작을 설정합니다. Logic Apps 디자이너에서 **+ 새 단계**를 선택하여 단계를 추가한 다음, **모두**를 선택하여 사용 가능한 모든 옵션을 표시합니다. **동작 선택** 창에서 **Office 365 Outlook**을 찾아서 선택합니다. 작업 화면에서 **이메일 보내기 / Office 365 Outlook**을 선택합니다.  

   ![Office365 옵션](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. 연결을 설정할 Office 365 계정에 로그인합니다. 시간이 초과되면 다시 시도합니다. 이메일을 받는 사람에 대한 이메일 주소를 지정합니다. 또한 제목을 지정하고 본문에 받는 사람이 확인할 메시지를 입력합니다. 테스트를 위해 받는 사람으로 사용자의 이메일 주소를 입력합니다.

   **동적 콘텐츠 추가**를 선택하여 포함할 수 있는 메시지의 콘텐츠를 표시합니다. **콘텐츠**를 선택합니다. 이메일에 메시지가 포함됩니다.

   ![논리 앱에 대한 이메일 옵션](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. **저장**을 선택합니다. 그런 다음, Logic Apps 디자이너를 닫습니다.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics 설정

Power BI 시각화에서 데이터를 보려면 먼저 데이터를 검색하는 Stream Analytics 작업을 설정합니다. **수준**이 **일반**인 메시지만 기본 엔드포인트에 전송되고, Power BI 시각화에 대한 Stream Analytics 작업에 의해 검색됩니다.

### <a name="create-the-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 선택합니다.

2. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다. 이 자습서에서는 **contosoJob**을 사용합니다.

   **구독**: 이 자습서를 위해 사용 중인 Azure 구독입니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다. 이 자습서에서는 **ContosoResources**를 사용합니다.

   **위치**: 설치 스크립트에서 사용되는 것과 동일한 위치를 사용합니다. 이 자습서에서는 **미국 서부**를 사용합니다.

   ![Stream Analytics 작업 만들기](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. **만들기**를 선택하여 작업을 만듭니다. 배포하는 데 몇 분이 걸릴 수 있습니다.

    작업으로 돌아가려면 **리소스 그룹**을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다. 리소스 그룹을 선택한 후 리소스 목록에 있는 Stream Analytics 작업을 선택합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. **작업 토폴로지**에서 **입력**을 선택합니다.

2. **입력** 창에서 **스트림 입력 추가**를 선택하고 IoT Hub를 선택합니다. 나타난 화면에서 다음 필드를 입력합니다.

   **입력 별칭**: 이 자습서에서는 **contosoinputs**를 사용합니다.

   **구독에서 IoT Hub를 선택합니다**. 이 라디오 단추 옵션을 선택합니다.

   **구독**: 이 자습서를 위해 사용 중인 Azure 구독을 선택합니다.

   **IoT Hub**: IoT 허브를 선택합니다. 이 자습서에서는 **ContosoTestHub**를 사용합니다.

   **엔드포인트**: **메시징**을 선택합니다. (작업 모니터링을 선택하는 경우 전송하는 데이터가 아닌 IoT Hub에 대한 원격 분석 데이터를 얻게 됩니다.) 

   **공유 액세스 정책 이름**: **서비스**를 선택합니다. 포털에서 사용자를 위한 공유 액세스 정책 키가 채워집니다.

   **소비자 그룹**: 이 자습서의 1부에서 설정한 소비자 그룹을 선택합니다. 이 자습서에서는 **contosoconsumers**를 사용합니다.
   
   나머지 필드는 기본값을 그대로 사용합니다. 

   ![Stream Analytics 작업에 대한 입력 설정](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. **저장**을 선택합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지**에서 **출력**을 선택합니다.

2. **출력** 창에서 **추가**를 선택한 다음, **Power BI**를 선택합니다. 나타난 화면에서 다음 필드를 입력합니다.

   **출력 별칭**: 출력에 대한 고유 별칭입니다. 이 자습서에서는 **contosooutputs**를 사용합니다. 

   **데이터 세트 이름**: Power BI에서 사용할 데이터 세트의 이름입니다. 이 자습서에서는 **contosodataset**을 사용합니다. 

   **테이블 이름**: Power BI에서 사용할 테이블의 이름입니다. 이 자습서에서는 **contosotable**을 사용합니다.

   나머지 필드는 기본값을 그대로 사용합니다.

3. **권한 부여**를 선택하고 사용자의 Power BI 계정에 로그인합니다. (두 번 이상 시도가 필요할 수 있습니다.)

   ![Stream Analytics 작업에 대한 출력 설정](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. **저장**을 선택합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지**에서 **쿼리**를 선택합니다.

2. `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다. 이 자습서에서는 **contosoinputs**를 사용합니다.

3. `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다. 이 자습서에서는 **contosooutputs**를 사용합니다.

   ![Stream Analytics 작업에 대한 쿼리 설정](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. **저장**을 선택합니다.

5. 쿼리 창을 닫습니다. 리소스 그룹의 리소스 보기로 돌아갑니다. Stream Analytics 작업을 선택합니다. 이 자습서에서는 이를 **contosoJob**이라고 합니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업에서 **시작** > **지금** > **시작**을 차례로 선택합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨**에서 **실행 중**으로 변경됩니다.

Power BI 보고서를 설정하려면 디바이스를 만들고 디바이스 시뮬레이션 애플리케이션을 실행한 후 Power BI를 설정하기 위한 데이터가 필요합니다.

## <a name="run-simulated-device-app"></a>시뮬레이션된 디바이스 앱 실행

이 자습서의 1부에서 IoT 디바이스를 사용하여 시뮬레이션할 디바이스를 설정합니다. 이 섹션에서는 IoT 허브에 디바이스-클라우드 메시지를 보내는 디바이스를 시뮬레이션하는 .NET 콘솔 앱을 다운로드합니다(1부에서 앱과 리소스를 아직 다운로드하지 않았다고 가정).

이 애플리케이션은 다양한 메시지 라우팅 메서드에 대해 메시지를 보냅니다. 이 다운로드에는 전체 Azure Resource Manager 템플릿과 매개 변수 파일은 물론 Azure CLI와 PowerShell 스크립트가 포함된 폴더도 있습니다.

이 자습서의 1부에서 리포지토리의 파일을 다운로드하지 않은 경우 [IoT 디바이스 시뮬레이션](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)에서 다운로드하세요. 이 링크를 선택하면 여러 애플리케이션이 포함된 리포지토리가 다운로드됩니다. 필요한 솔루션은 iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln입니다. 

솔루션 파일(IoT_SimulatedDevice.sln)을 두 번 클릭하여 Visual Studio에서 코드를 연 다음, Program.cs를 엽니다. `{your hub name}`을 IoT Hub 호스트 이름으로 대체합니다. IoT Hub 호스트 이름의 형식은 **{iot-hub-name}.azure-devices.net**입니다. 이 자습서의 경우 허브 호스트 이름은 **ContosoTestHub.azure-devices.net**입니다. 다음으로, `{your device key}`를 이전에 시뮬레이션된 디바이스를 설정할 때 저장했던 디바이스 키로 대체합니다. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>실행 및 테스트

콘솔 애플리케이션을 실행합니다. 잠시 기다립니다. 애플리케이션의 콘솔 화면에서 전송되는 메시지를 볼 수 있습니다.

앱은 1초마다 새로운 디바이스-클라우드 메시지를 IoT Hub에 보냅니다. 메시지에는 디바이스 ID, 온도, 습도 및 메시지 수준(기본값이 `normal`)과 함께 JSON 직렬화된 개체가 포함됩니다. `critical` 또는 `storage` 수준을 임의로 할당하므로 메시지는 스토리지 계정 또는 Service Bus 큐로 라우팅됩니다(이메일을 보내도록 Logic App을 트리거). 기본값(`normal`) 판독값이 다음에 설정하는 BI 보고서에 표시됩니다.

모든 설정이 올바르면 다음과 같은 결과가 표시됩니다.

1. 중요한 메시지에 대한 이메일을 가져오기 시작합니다.

   ![결과 이메일](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   이 결과는 다음 내용이 참이라는 의미입니다. 

   * Service Bus 큐에 대한 라우팅이 제대로 작동합니다.
   * Service Bus 큐의 메시지를 검색하는 Logic App이 제대로 작동합니다.
   * Outlook에 대한 Logic App 커넥터가 제대로 작동합니다. 

2. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹**을 선택하고 리소스 그룹을 선택합니다. 이 자습서에서는 **ContosoResources**를 사용합니다. 

    스토리지 계정을 선택하고 **Containers**를 선택한 다음, 컨테이너를 선택합니다. 이 자습서에서는 **contosoresults**를 사용합니다. 폴더를 확인하고 하나 이상의 파일이 표시될 때까지 디렉터리를 드릴다운할 수 있습니다. 이러한 파일 중 하나를 열면 스토리지 계정으로 라우팅된 항목이 포함되어 있습니다. 

   ![스토리지의 결과 파일](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

이 결과는 다음 내용이 참이라는 의미입니다.

   * 스토리지 계정에 대한 라우팅이 제대로 작동합니다.

이제 애플리케이션이 계속 실행 중인 상태에서 기본 라우팅을 통해 들어오는 메시지를 표시하도록 Power BI 시각화를 설정합니다.

## <a name="set-up-the-power-bi-visualizations"></a>Power BI 시각화 설정

1. [Power BI](https://powerbi.microsoft.com/) 계정에 로그인합니다.

2. **작업 영역**으로 이동하고 Stream Analytics 작업에 대한 출력을 만들 때 설정한 작업 영역을 선택합니다. 이 자습서에서는 **My Workspace**를 사용합니다. 

3. **데이터 세트**를 선택합니다. 데이터 세트가 없으면 몇 분 기다린 후 다시 확인합니다.

   Stream Analytics 작업에 대한 출력을 만들 때 지정한 나열된 데이터 세트가 표시됩니다. 이 자습서에서는 **contosodataset**을 사용합니다. (데이터 세트를 처음 표시할 때는 5-10분이 걸릴 수 있습니다.)

4. **작업**에서 첫 번째 아이콘을 선택하여 보고서를 만듭니다.

   ![작업 및 보고서 아이콘이 강조 표시된 Power BI 작업 영역](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. 시간이 지남에 따라 실시간 온도를 표시하는 꺾은선형 차트를 만듭니다.

   * 보고서 만들기 페이지에서 꺾은선형 차트 아이콘을 선택하여 꺾은선형 차트를 추가합니다.

     ![시각화 및 필드](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * **필드** 창에서 Stream Analytics 작업의 출력을 만들 때 지정한 테이블을 확장합니다. 이 자습서에서는 **contosotable**을 사용합니다.

   * **시각화** 창에서 **EventEnqueuedUtcTime**을 **축**으로 끌어갑니다.

   * **온도**를 **값**으로 끌어갑니다.

   꺾은선형 차트가 만들어집니다. x축은 UTC 표준 시간대의 날짜와 시간을 표시하고, y축은 센서의 온도를 표시합니다.

6. 시간이 지남에 따라 실시간 습도를 표시하는 꺾은선형 차트를 만듭니다. 두 번째 차트를 설정하려면 첫 번째 차트에서 동일한 프로세스를 수행하여 **EventEnqueuedUtcTime**을 x축(**Axis**)에 배치하고 **습도**를 y축(**Values**)에 배치합니다.

   ![두 개의 차트가 있는 최종 Power BI 보고서](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. 보고서를 저장하려면 **저장**을 선택하고, 메시지가 표시되면 보고서의 이름을 입력합니다.

두 차트 모두에서 데이터를 볼 수 있습니다. 이 결과는 다음 내용이 참이라는 의미입니다.

   * 기본 엔드포인트에 대한 라우팅이 제대로 작동합니다.
   * Azure Stream Analytics 작업이 제대로 스트리밍됩니다.
   * Power BI 시각화가 제대로 설정되어 있습니다.

Power BI 창 위쪽의 새로 고침 단추를 선택하면 차트를 새로 고쳐서 가장 최근 데이터를 볼 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리 

이 자습서의 두 부분을 통해 만든 Azure 리소스를 모두 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, Service Bus 네임스페이스 및 큐, Logic App, 스토리지 계정 및 리소스 그룹 자체가 제거됩니다. Power BI 리소스를 제거하고 자습서 중에 보낸 이메일을 지울 수도 있습니다.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Power BI 시각화에서 리소스 정리

[Power BI](https://powerbi.microsoft.com/) 계정에 로그인합니다. 작업 영역으로 이동합니다. 이 자습서에서는 **My Workspace**를 사용합니다. Power BI 시각화를 제거하려면 데이터 세트로 이동하고 휴지통 아이콘을 선택하여 데이터 세트를 삭제합니다. 이 자습서에서는 **contosodataset**을 사용합니다. 데이터 세트를 제거하면 보고서도 제거됩니다.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다. `$resourceGroup`은 이 자습서의 시작 부분에서 **ContosoResources**로 다시 설정되었습니다.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>PowerShell을 사용하여 리소스 정리

리소스 그룹을 제거하려면 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용합니다. `$resourceGroup`은 이 자습서의 시작 부분에서 **ContosoResources**로 다시 설정되었습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>테스트 이메일 정리

디바이스 애플리케이션이 실행되는 동안 Logic App을 통해 생성된 수신함의 이메일 수량을 삭제할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

2부분으로 구성된 이 자습서에서는 다음 작업을 수행하여 여러 대상에 IoT Hub 메시지를 라우팅하는 메시지 라우팅을 사용하는 방법을 알아보았습니다.  

**1부: 리소스 만들기, 메시지 라우팅 설정**
> [!div class="checklist"]
> * IoT Hub, 스토리지 계정, Service Bus 큐 및 시뮬레이션된 디바이스와 같은 리소스를 설정합니다.
> * 스토리지 계정 및 Service Bus 큐에 대한 IoT Hub에서 엔드포인트 및 메시지 경로를 구성합니다.

**2부: 허브에 메시지 보내기, 라우팅된 결과 보기**
> [!div class="checklist"]
> * 메시지가 Service Bus 큐에 추가될 때 트리거되어 이메일을 전송하는 Logic App을 만듭니다.
> * 다양한 라우팅 옵션에 대한 허브로 메시지를 전송하는 IoT 디바이스를 시뮬레이션하는 앱을 다운로드하여 실행합니다.
> * 기본 엔드포인트에 전송된 데이터에 대한 Power BI 시각화를 만듭니다.
> * 다음에서 결과를 확인합니다.
> * ...Service Bus 큐 및 이메일에서
> * ...스토리지 계정에서
> * ...Power BI 시각화에서

IoT 디바이스의 상태를 관리하는 방법에 대해 알아보려면 다음 자습서로 이동합니다. 

> [!div class="nextstepaction"]
> [IoT Hub를 사용하여 메트릭 및 진단 로그의 설정 및 사용](tutorial-use-metrics-and-diags.md)

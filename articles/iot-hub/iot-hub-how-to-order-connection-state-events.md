---
title: 장치 연결 이벤트의 순서에 따라 fr-fr Azure IoT Hub w/Azure Cosmos DB
description: 이 문서에서는 최신 연결 상태를 유지하기 위해 Azure Cosmos DB를 사용하여 Azure IoT Hub의 디바이스 연결 이벤트를 정렬하고 기록하는 방법을 설명합니다.
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: 210c2e74305ba99b4ac3a12625d0b7f5fc47ba43
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954263"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Azure Cosmos DB를 사용하여 Azure IoT Hub의 디바이스 연결 이벤트 정렬

Azure Event Grid를 사용하면 이벤트 기반 애플리케이션을 빌드하고 비즈니스 솔루션에서 IoT 이벤트를 쉽게 통합할 수 있습니다. 이 문서에서는 Cosmos DB에 최신 디바이스 연결 상태를 저장하고 추적하는 데 사용할 수 있는 설정을 설명합니다. 디바이스 연결됨 및 디바이스 연결 끊김 이벤트에 사용할 수 있는 시퀀스 번호를 사용하고 Cosmos DB에 최신 상태를 저장합니다. Cosmos DB의 컬렉션에 대해 실행되는 애플리케이션 논리인 저장 프로시저를 사용하려고 합니다.

시퀀스 번호는 16진수 숫자에 대한 문자열 표현입니다. 문자열 비교를 사용하여 더 큰 숫자를 식별할 수 있습니다. 문자열을 16진수로 변환하면 숫자는 256비트 숫자가 됩니다. 시퀀스 번호는 절대적으로 증가하며 최신 이벤트는 다른 이벤트보다 번호가 높습니다. Azure Event Grid에서는 이벤트 정렬이 지원되지 않기 때문에 시퀀스 번호는 디바이스를 자주 연결하고 연결을 끊는 경우, 다운 스트림 작업을 트리거하는 데 최신 이벤트만 사용되도록 하려는 경우에 유용합니다.

## <a name="prerequisites"></a>선행 조건

* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)에 만들 수 있습니다.

* 활성 Azure Cosmos DB SQL API 계정입니다. 아직 만들지 않았다면, [데이터베이스 계정 만들기](../cosmos-db/create-sql-api-java.md#create-a-database-account)를 연습으로 참조하세요.

* 데이터베이스의 컬렉션입니다. [컬렉션 추가](../cosmos-db/create-sql-api-java.md#add-a-container)를 연습으로 참조하세요. 컬렉션을 만들 때 파티션 키에 대 한 `/id`를 사용 합니다.

* Azure에 있는 IoT Hub. 아직 만들지 않았다면, 연습으로 [IoT Hub 시작](iot-hub-csharp-csharp-getstarted.md)을 참조하세요.

## <a name="create-a-stored-procedure"></a>저장 프로시저 만들기

먼저 저장 프로시저를 만들어서 들어오는 이벤트의 시퀀스 번호를 비교하고 데이터베이스의 디바이스마다 최신 이벤트를 기록하는 논리를 실행하도록 설정합니다.

1. Cosmos DB SQL API에서 **데이터 탐색기** > **항목** > **새 저장 프로시저**를 선택합니다.

   ![저장 프로시저 만들기](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. 저장 프로시저 ID에 **LatestDeviceConnectionState** 를 입력 하 고 **저장 프로시저 본문**에 다음을 붙여넣습니다. 이 코드가 저장 프로시저 본문에 있는 기존 코드를 대체해야 합니다. 이 코드는 가장 높은 시퀀스 번호를 식별하여 디바이스 ID당 하나의 행을 유지하고 해당 디바이스 ID의 최신 연결 상태를 기록합니다.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. 저장 프로시저 저장:

    ![저장 프로시저 저장](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>논리 앱 만들기

먼저 논리 앱을 만들고 가상 컴퓨터에 대한 리소스 그룹을 모니터링하는 Event Grid 트리거를 추가합니다.

### <a name="create-a-logic-app-resource"></a>논리 앱 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에서 **+ 리소스 만들기**를 선택 하 고 **통합** , **논리 앱**을 차례로 선택 합니다.

   ![논리 앱 만들기](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. 구독에서 논리 앱에 고유한 이름을 제공하고 동일한 구독과 리소스 그룹 및 위치를 IoT 허브로 선택합니다.

   ![새 논리 앱](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. **만들기**를 선택하여 논리 앱을 만듭니다.

   이제 논리 앱에 대한 Azure 리소스를 만들었습니다. Azure에서 논리 앱을 배포하면 Logic Apps 디자이너에서 일반적인 패턴에 대한 템플릿을 표시하므로 더 빨리 시작할 수 있습니다.

   > [!NOTE]
   > 논리 앱을 찾아 열려면 **리소스 그룹** 을 선택 하 고이 방법에 사용할 리소스 그룹을 선택 합니다. 그런 다음 새 논리 앱을 선택 합니다. 그러면 논리 앱 디자이너가 열립니다.

4. 논리 앱 디자이너에서 일반적인 트리거가 표시 될 때까지 오른쪽으로 스크롤합니다. **템플릿**아래에서 **빈 논리 앱** 을 선택 하 여 논리 앱을 처음부터 빌드할 수 있습니다.

### <a name="select-a-trigger"></a>트리거 선택

트리거는 논리 앱을 시작하는 특정 이벤트입니다. 이 자습서에서는 워크플로를 시작하는 트리거를 HTTP를 통해 요청을 받습니다.

1. 커넥터 및 트리거 검색 표시줄에 **HTTP** 를 입력 하 고 enter 키를 누릅니다.

2. 트리거로 **요청 - HTTP 요청을 수신한 경우**를 선택합니다.

   ![HTTP 요청 트리거 선택](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

   ![샘플 페이로드를 사용하여 스키마 생성](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. 다음 샘플 JSON 코드를 텍스트 상자에 붙여넣기한 다음 **완료**를 선택합니다.

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![샘플 JSON 페이로드 붙여넣기](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. **요청할 때 Content-Type 헤더 집합을 애플리케이션/json에 포함해야 합니다**라고 표시되는 팝업 알림을 받을 수도 있습니다. 이 제안을 안전하게 무시하고 다음 섹션으로 이동할 수 있습니다.

### <a name="create-a-condition"></a>조건 만들기

논리 앱 워크플로에서 조건은 특정 조건을 통과한 후에 특정 작업을 실행하는 데 도움이 됩니다. 조건이 충족되면 원하는 작업을 정의할 수 있습니다. 이 자습서에서는 eventType이 디바이스 연결됨인지 또는 디바이스 연결 끊김인지를 확인하는 것이 조건입니다. 작업은 데이터베이스에 저장된 프로시저를 실행하는 것입니다.

1. **+ 새 단계** , **기본 제공**을 차례로 선택한 다음 **조건**을 찾아 선택 합니다. **값 선택** 을 클릭 하 고 동적 콘텐츠 (선택할 수 있는 필드)를 표시 하는 상자를 표시 합니다. 아래 표시 된 대로 필드를 입력 하 여 연결 된 장치 및 장치 연결이 끊어진 이벤트에 대해서만이 작업을 실행 합니다.

   * 값 선택: **eventType** --이 필드를 클릭할 때 표시 되는 동적 콘텐츠의 필드에서 선택 합니다.
   * **에서로 끝날**"이 (가) 같음"으로 변경 합니다.
   * 값 선택: **nected**.

     ![조건 채우기](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. True 인 **경우** 대화 상자에서 **작업 추가**를 클릭 합니다.
  
   ![작업이 true이면](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Cosmos DB를 검색 하 고 **Azure Cosmos DB 저장 프로시저 실행** 을 선택 합니다.

   ![CosmosDB 검색](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Cosmosdb를 입력 하 여 **연결 이름** 에 **연결** 하 고 테이블에서 항목을 선택한 다음, **만들기**를 선택 합니다. **저장 프로시저 실행** 패널이 표시 됩니다. 필드에 대 한 값을 입력 합니다.

   **데이터베이스 ID**: ToDoList

   **컬렉션 ID**: 항목

   **프로시저 작업 ID**: LatestDeviceConnectionState

5. **새 매개 변수 추가**를 선택 합니다. 드롭다운 목록에서 **저장 프로시저에 대 한** **파티션 키** 및 매개 변수 옆의 확인란을 선택 하 고 화면에서 다른 위치를 클릭 합니다. 파티션 키 값에 대 한 필드와 저장 프로시저의 매개 변수에 대 한 필드를 추가 합니다.

   ![논리 앱 작업 채우기](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. 이제 아래와 같이 파티션 키 값 및 매개 변수를 입력 합니다. 표시 된 것 처럼 대괄호와 큰따옴표를 입력 해야 합니다. 여기에서 사용할 수 있는 유효한 값을 가져오려면 **동적 콘텐츠 추가** 를 클릭 해야 할 수도 있습니다.

   ![논리 앱 작업 채우기](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. **각각에 대해**표시 되는 창의 위쪽에서 **이전 단계의 출력 선택**에서 **본문이** 선택 되어 있는지 확인 합니다.

   ![논리 앱 채우기](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. 논리 앱을 저장합니다.

### <a name="copy-the-http-url"></a>HTTP URL 복사

Logic Apps Designer를 나가기 전에 논리 앱이 트리거에 대해 수신 대기하는 URL을 복사합니다. 이 URL을 사용하여 Event Grid를 구성합니다.

1. **HTTP 요청을 받을 경우** 트리거 구성 상자를 클릭하여 확장합니다.

2. **HTTP POST URL**의 값을 그 옆에 있는 복사 단추를 선택하여 복사합니다.

   ![HTTP POST URL 복사](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. 다음 섹션에서 참조할 수 있도록 이 URL을 저장합니다.

## <a name="configure-subscription-for-iot-hub-events"></a>IoT Hub 이벤트에 대한 구독 구성

이 섹션에서는 일어나는 순서대로 이벤트를 게시하도록 Azure IoT Hub 를 구성합니다.

1. Azure Portal에서 IoT Hub로 이동합니다.

2. **이벤트**를 선택합니다.

   ![Event Grid 세부 정보 열기](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. **+ 이벤트 구독**을 선택 합니다.

   ![새 이벤트 구독 만들기](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. **이벤트 구독 정보**입력: 설명이 포함 된 이름을 입력 하 고 **Event Grid 스키마**를 선택 합니다.

5. **이벤트 유형** 필드를 입력 합니다. 드롭다운 목록에서 **장치에 연결 됨** 을 선택 하 고 메뉴에서 **장치 연결 끊기** 를 선택 합니다. 화면에서 다른 곳을 클릭 하 여 목록을 닫고 선택 내용을 저장 합니다.

   ![찾을 이벤트 유형 설정](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. **엔드포인트 세부 정보**에서 엔드포인트 유형을 **웹 후크에** 선택 하 고 엔드포인트 선택을 클릭 한 다음 논리 앱에서 복사한 URL을 붙여넣고 선택 항목을 확인 합니다.

   ![엔드포인트 url 선택](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. 그러면 폼이 다음 예제와 같이 표시 됩니다.

   ![샘플 이벤트 구독 양식](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   **만들기**를 선택하여 이벤트 구독을 저장합니다.

## <a name="observe-events"></a>이벤트 관찰

이벤트 구독을 설정했으면 디바이스를 연결하여 테스트해 보겠습니다.

### <a name="register-a-device-in-iot-hub"></a>IoT Hub에 디바이스 등록

1. IoT 허브에서 **IoT 디바이스**를 선택합니다.

2. 창 위쪽에서 **+ 추가** 를 선택 합니다.

3. **디바이스 ID**로 `Demo-Device-1`를 입력합니다.

4. **저장**을 선택합니다.

5. 다른 디바이스 ID로 여러 디바이스를 추가할 수 있습니다.

   ![허브에 추가 된 장치](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. 장치를 다시 클릭 합니다. 이제 연결 문자열 및 키가 채워집니다. 나중에 사용하기 위해 **연결 문자열 -- 기본 키**를 복사합니다.

   ![장치에 대 한 ConnectionString](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi 시뮬레이터 시작

Raspberry Pi 웹 시뮬레이터를 사용하여 디바이스 연결을 시뮬레이션해 보겠습니다.

[Raspberry Pi 시뮬레이터 시작](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Raspberry Pi 웹 시뮬레이터에서 샘플 애플리케이션 실행

그러면 디바이스 연결됨 이벤트가 트리거됩니다.

1. 코딩 영역에서 15 줄의 자리 표시자를 이전 섹션의 끝 부분에서 저장 한 Azure IoT Hub 장치 연결 문자열로 바꿉니다.

   ![장치 연결 문자열에 붙여넣기](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. **실행**을 선택 하 여 응용 프로그램을 실행 합니다.

IoT hub로 전송 되는 센서 데이터와 메시지를 보여 주는 다음 출력과 유사한 내용이 표시 됩니다.

   ![애플리케이션 실행](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   **중지**를 클릭하여 시뮬레이터를 중지하고 **디바이스 연결 끊김** 이벤트를 트리거합니다.

샘플 애플리케이션을 실행하고 센서 데이터를 수집하여 IoT Hub로 전송했습니다.

### <a name="observe-events-in-cosmos-db"></a>Cosmos DB에서 이벤트 관찰

Cosmos DB 문서에서 실행된 저장 프로시저의 결과를 볼 수 있습니다. 모양은 다음과 같습니다. 각 행에는 디바이스당 최신 디바이스 연결 상태가 포함됩니다.

   ![결과를 내는 방법](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Azure CLI 사용

[Azure Portal](https://portal.azure.com)을 사용하는 대신 Azure CLI를 사용하여 IoT Hub 단계를 설정할 수 있습니다. 자세한 내용은 [이벤트 구독 만들기](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) 및 [IoT 디바이스 만들기](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create)에 대한 Azure 명령줄 인터페이스 페이지를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 Azure 구독에 요금을 부과하는 리소스를 사용했습니다. 자습서를 시도 하 고 결과를 테스트 하는 작업이 완료 되 면 유지 하지 않으려는 리소스를 사용 하지 않도록 설정 하거나 삭제 합니다.

논리 앱에 대한 작업을 잃지 않으려면 삭제하는 대신에 비활성화합니다.

1. 논리 앱으로 이동

2. **개요** 블레이드에서 **삭제** 또는 **사용 안 함**을 선택합니다.

    구독마다 하나의 무료 Azure IoT Hub를 가질 수 있습니다. 이 자습서에 대한 무료 허브를 만든 경우 요금이 부과되는 것을 막기 위해 삭제할 필요가 없습니다.

3. IoT Hub로 이동

4. **개요** 블레이드에서 **삭제**를 선택합니다.

    IoT 허브를 유지하더라도 만든 이벤트 구독을 삭제하는 것이 좋습니다.

5. IoT 허브에서 **Event Grid**를 선택합니다.

6. 제거하려는 이벤트 구독을 선택합니다.

7. **삭제**를 선택합니다.

Azure Portal에서 Azure Cosmos DB 계정을 제거하려면 계정 이름을 마우스 오른쪽 단추로 클릭하고 **계정 삭제**를 클릭합니다. 자세한 지침은 [Azure Cosmos DB 계정 삭제](https://docs.microsoft.com/azure/cosmos-db/manage-account)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Event Grid를 사용하여 IoT Hub 이벤트에 대응하여 작업 트리거](../iot-hub/iot-hub-event-grid.md)에 대해 자세히 알아보기

* [IoT Hub 이벤트 자습서를 시도해 봅니다.](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Event Grid](../event-grid/overview.md)로 수행할 수 있는 다른 작업에 대해 알아보기
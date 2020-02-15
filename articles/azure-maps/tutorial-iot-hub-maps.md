---
title: '자습서: IoT 공간 분석 구현 | Microsoft Azure Maps'
description: IoT Hub를 Microsoft Azure Maps 서비스 API와 통합합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24295e27a3b94f6960777a8704fdf448697da4e1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987289"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>자습서: Azure Maps를 사용하여 IoT 공간 분석 구현

공간과 시간에서 발생하는 관련 이벤트를 추적하고 캡처하는 것은 일반적인 IoT 시나리오입니다. 차량 관리, 자산 추적, 모바일 및 스마트 도시 애플리케이션 시나리오를 예로 들 수 있습니다. 이 자습서에서는 Azure Maps API 사용을 위한 솔루션 패턴을 안내합니다. 관련 이벤트는 Event Grid에서 제공하는 이벤트 구독 모델을 사용하여 IoT Hub에 의해 캡처됩니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * IoT Hub를 만듭니다.
> * 데이터 업로드 API를 사용하여 Azure Maps, 데이터 서비스에서 지오펜스 영역을 업로드합니다.
> * Azure Maps 공간 분석을 기반으로 하는 비즈니스 논리를 구현하는 Azure Functions에서 함수를 만듭니다.
> * Event Grid를 통해 Azure 함수에서 IoT 디바이스 원격 분석 이벤트를 구독합니다.
> * IoT Hub 메시지 라우팅을 사용하여 원격 분석 이벤트를 필터링합니다.
> * 관련 이벤트 데이터를 저장할 스토리지 계정을 만듭니다.
> * 차량 내부 IoT 디바이스를 시뮬레이션합니다.
    

## <a name="use-case"></a>사용 사례

이 솔루션은 자동차 임대 회사에서 렌터카의 이벤트를 모니터링하고 기록하는 시나리오를 보여줍니다. 자동차 임대 회사는 종종 특정 지리적 지역에서 자동차를 임대하며, 임대하는 동안 해당 위치를 추적해야 합니다. 선택한 지리적 지역을 벗어나는 자동차 인스턴스를 기록해야 합니다. 데이터를 기록하면 정책, 요금 및 기타 비즈니스 사안이 올바르게 처리됩니다.

사용 사례에서 렌터카에는 원격 분석 데이터를 Azure IoT Hub로 정기적으로 보내는 IoT 디바이스가 장착되어 있습니다. 원격 분석에는 현재 위치가 포함되며, 자동차의 엔진이 작동하고 있는지 여부가 표시됩니다. 디바이스 위치 스키마는 [지리 공간 데이터에 대한 IoT 플러그 앤 플레이 스키마](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md)를 준수합니다. 렌터카의 디바이스 원격 분석 스키마는 다음과 같습니다.

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": { 
            "location": { 
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            } 
        } 
    }
}
```

차량 내 디바이스 원격 분석을 사용하여 목표를 달성해 보겠습니다. 우리의 목표는 지오펜싱 규칙을 실행하고, 자동차가 이동했다는 이벤트를 받을 때마다 응답하는 것입니다. 이렇게 하기 위해 Event Grid를 통해 IoT Hub의 디바이스 원격 분석 이벤트를 구독할 것입니다. Event Grid를 구독하는 여러 가지 방법이 있으며, 이 자습서에서는 Azure Functions를 사용합니다. Azure Functions는 Event Grid에 게시된 이벤트에 응답합니다. 또한 Azure Maps 공간 분석을 기반으로 하는 자동차 임대 비즈니스 논리를 구현합니다. Azure 함수 내 코드는 차량이 지오펜스를 벗어났는지 여부를 검사합니다. 차량이 지오펜스를 벗어나면 Azure 함수는 차량의 현재 위치와 연결된 주소 같은 추가 정보를 수집합니다. 또한 이 함수는 이벤트 상황에 대해 정확하게 설명할 수 있도록 의미 있는 이벤트 데이터를 데이터 BLOB 스토리지에 저장하는 논리를 구현합니다. 이벤트 환경은 자동차 임대 회사 및 임대 고객에게 도움이 될 수 있습니다.

다음 다이어그램에서는 시스템에 대한 개략적인 개요를 제공합니다.

 
  <center>

  ![시스템 개요](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

아래 그림에서 지오펜스 영역은 파란색으로, 렌터카 경로는 녹색 선으로 표시되어 있습니다.

  ![지오펜스 경로](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>사전 요구 사항 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 자습서의 단계를 수행하려면 먼저 Azure Portal에서 리소스 그룹을 만들어야 합니다. 리소스 그룹을 만들려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **리소스 그룹**을 선택합니다.
    
   ![리소스 그룹](./media/tutorial-iot-hub-maps/resource-group.png)

3. **리소스 그룹**에서 **추가**를 선택합니다.
    
   ![리소스 그룹 추가](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. 다음 속성 값을 입력합니다.
    * **구독:** Azure 구독을 선택합니다.
    * **리소스 그룹:** 리소스 그룹 이름으로 "ContosoRental"을 입력합니다.
    * **지역:** 리소스 그룹에 대한 지역을 선택합니다.  

    ![리소스 그룹 세부 정보](./media/tutorial-iot-hub-maps/resource-details.png)

    **검토 + 만들기**를 선택하고, 다음 페이지에서 **만들기**를 선택합니다.

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기 

Azure Maps 공간 분석에 기반한 비즈니스 논리를 구현하려면 만든 리소스 그룹에 Azure Maps 계정을 만들어야 합니다. [계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps)의 지침에 따라 S1 가격 책정 계층을 사용하여 Azure Maps 계정 구독을 만들고, [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)의 단계를 수행하여 계정의 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](how-to-manage-authentication.md)를 참조하세요.



### <a name="create-a-storage-account"></a>스토리지 계정 만들기

이벤트 데이터를 기록하기 위해, 범용 **v2storage** 계정을 "ContosoRental" 리소스 그룹에 만들어 데이터를 BLOB으로 저장합니다. 스토리지 계정을 만들려면 [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)의 지침을 따릅니다. 다음으로, Blob을 저장할 컨테이너를 만들어야 합니다. 이렇게 하려면 다음 단계를 수행합니다.

1. 스토리지 계정에서 [컨테이너]로 이동합니다.

    ![Blob](./media/tutorial-iot-hub-maps/blobs.png)

2. 왼쪽 위의 컨테이너 단추를 클릭하고, 컨테이너의 이름을 "contoso-rental-logs"로 지정하고, "확인"을 클릭합니다.

    ![Blob 컨테이너](./media/tutorial-iot-hub-maps/blob-container.png)

3. 스토리지 계정의 **액세스 키** 블레이드로 이동하여 "스토리지 계정 이름"과 "액세스 키"를 복사합니다. 이후 단계에서 필요합니다.

    ![액세스 키](./media/tutorial-iot-hub-maps/access-keys.png)


이제 이벤트 데이터를 기록할 스토리지 계정과 컨테이너가 마련되었습니다. 다음으로 IoT 허브를 만들겠습니다.

### <a name="create-an-iot-hub"></a>IoT Hub 만들기

IoT Hub는 클라우드에서 IoT 애플리케이션과 이를 통해 관리되는 디바이스 간의 양방향 통신용 중앙 메시지 허브로 작동하는 관리되는 서비스입니다. 디바이스 원격 분석 메시지를 Event Grid로 라우팅하려면 "ContosoRental" 리소스 그룹 내에 IoT Hub를 만듭니다. 자동차의 엔진 상태를 기준으로 메시지를 필터링할 메시지 경로 통합을 설정합니다. 또한 자동차가 이동 중일 때마다 디바이스 원격 분석 메시지를 Event Grid 보낼 것입니다.

> [!Note] 
> 디바이스 원격 분석 이벤트를 Event Grid에 게시하는 IoT Hub의 기능은 공개 미리 보기로 있습니다. 공개 미리 보기 기능은 **미국 동부, 미국 서부, 서유럽, Azure Government, Azure 중국 21Vianet** 및 **Azure 독일**을 제외한 모든 지역에서 사용할 수 있습니다. 

[IoT Hub 만들기 섹션](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)의 단계에 따라 IoT Hub를 만듭니다.


### <a name="register-a-device"></a>디바이스 등록 

IoT Hub에 연결하려면 디바이스를 등록해야 합니다. 디바이스를 IoT Hub에 등록하려면 다음 단계를 수행합니다.

1. IoT Hub에서 "IoT 디바이스" 블레이드를 클릭하고 "새로 만들기"를 클릭합니다.

    ![디바이스 추가](./media/tutorial-iot-hub-maps/add-device.png)

2. 디바이스 만들기 페이지에서 IoT 디바이스 이름을 지정하고 "저장"을 클릭합니다.
    
    ![디바이스 등록](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>지오펜스 업로드

[Postman 애플리케이션](https://www.getpostman.com)을 사용하여 Azure Maps 데이터 업로드 API를 통해 Azure Maps 서비스에 [지오펜스를 업로드](https://docs.microsoft.com/azure/azure-maps/geofence-geojson)합니다. 자동차가 이 지오펜스 외부에 있는 모든 이벤트가 기록됩니다.

Postman 앱을 열고, 아래의 단계에 따라 Azure Maps 데이터 업로드 API를 사용하여 지오펜스를 업로드합니다.  

1. Postman 앱에서 new | Create new(새로 만들기 | 새로 만들기)를 차례로 클릭하고 Request(요청)를 선택합니다. 지오펜스 데이터 업로드에 대한 요청 이름을 입력하고, 저장할 컬렉션 또는 폴더를 선택한 다음, [저장]을 클릭합니다.

    ![Postman을 사용하여 지오펜스 업로드](./media/tutorial-iot-hub-maps/postman-new.png)

2. 작성기 탭에서 POST HTTP 메서드를 선택하고, 다음 URL을 입력하여 POST 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 경로의 `dataFormat` 매개 변수에 대한 "geojson" 값은 업로드되는 데이터의 형식을 나타냅니다.

3. **매개 변수**를 클릭하고 POST 요청 URL에 사용할 다음 키/값 쌍을 입력합니다. subscription-key 값을 Azure Maps 키로 바꿉니다.
   
    ![키 값 매개 변수 Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. **본문**을 클릭하고 **원시** 입력 형식을 선택한 다음, 드롭다운 목록에서 입력 형식으로 **JSON (애플리케이션/텍스트)** 을 선택합니다. [여기](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)서 JSON 데이터 파일을 열고, Json을 본문 섹션에 업로드할 데이터로 복사하고, **보내기**를 클릭합니다.
    
    ![데이터 게시](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. 응답 헤더를 검토합니다. 요청이 성공하면 **Location**(위치) 헤더에 업로드 요청의 현재 상태를 확인하기 위한 상태 URI가 포함됩니다. 상태 URI는 다음과 같은 형식입니다. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 상태 URI를 복사하고, 해당 값이 Azure Maps 계정 구독 키인 `subscription-key` 매개 변수를 이 상태 URI에 추가합니다. 상태 URI 형식은 아래와 같습니다.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. `udId`를 가져오려면 Postman 앱에서 새 탭을 열고, 작성기 탭에서 GET HTTP 메서드를 선택하고, 상태 URI에서 GET 요청을 수행합니다. 데이터 업로드가 성공하면 응답 본문에 udId가 있습니다. 나중에 사용할 수 있도록 udId를 복사합니다.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


다음으로, Azure Function을 "ContosoRental" 리소스 그룹 내에 만든 다음, 디바이스 원격 분석 메시지를 필터링하도록 IoT Hub에서 메시지 경로를 설정합니다.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure Function 만들기 및 Event Grid 구독 추가

Azure Functions는 컴퓨팅 인프라를 명시적으로 프로비저닝하거나 관리할 필요 없이 요청 시 코드를 실행할 수 있는 서버리스 컴퓨팅 서비스입니다. Azure Functions에 대한 자세한 내용은 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 설명서를 살펴보세요. 이 함수에서 구현하는 논리는 차량 내부 디바이스 원격 분석에서 제공되는 데이터를 사용하여 지오펜스 상태를 평가하는 것입니다. 지정한 자동차가 지오펜스 외부에 있는 경우 이 함수는 지정한 위치 좌표를 사람이 이해할 수 있는 거리 주소로 변환하는 [검색 주소 역방향 가져오기 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)를 통해 위치 주소와 같은 추가 정보를 수집합니다. 그런 다음, 모든 관련 이벤트 정보가 Blob 저장소에 저장됩니다. 아래의 5단계에서는 이러한 논리를 구현하는 실행 코드를 가리킵니다. 아래의 단계에 따라 데이터 로그를 스토리지 계정의 Blob 컨테이너에 보내고 Event Grid 구독을 이 컨테이너에 추가하는 Azure Function을 만듭니다.

1. Azure Portal 대시보드에서 리소스 만들기를 선택합니다. 사용 가능한 리소스 종류 목록에서 **컴퓨팅**을 선택한 다음, **함수 앱**을 선택합니다.

    ![리소스 만들기](./media/tutorial-iot-hub-maps/create-resource.png)

2. **함수 앱** 만들기 페이지에서 함수 앱의 이름을 지정합니다. **리소스 그룹**에서 **기존 항목 사용**을 선택하고, 드롭다운 목록에서 "ContosoRental"을 선택합니다. 런타임 스택으로 ".NET Core"를 선택합니다. **스토리지**에서 **기존 항목 사용**을 선택하고, 드롭다운 목록에서 "contosorentaldata"를 선택한 다음, **검토 + 만들기**를 선택합니다.
    
    ![앱 만들기](./media/tutorial-iot-hub-maps/rental-app.png)

2. 함수 앱 세부 정보를 검토하고, "만들기"를 선택합니다.

3. 앱이 만들어지면 함수를 이 앱에 추가해야 합니다. 함수 앱으로 이동하고, **새 함수**를 클릭하여 함수를 추가하고, 개발 환경으로 **포털 내**를 선택한 다음, **계속**을 선택합니다.

    ![함수 만들기](./media/tutorial-iot-hub-maps/function.png)

4. **추가 템플릿**을 선택하고, **템플릿 완료 및 보기**를 클릭합니다. 

5. **Azure Event Grid 트리거**가 있는 템플릿을 선택합니다. 메시지가 표시되면 확장을 설치하고, 함수 이름을 지정하고, **만들기**를 선택합니다.

    ![함수 템플릿](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. [C# 코드](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)를 함수에 복사하고, **저장**을 클릭합니다.
 
7. C# 스크립트에서 다음 매개 변수를 바꿉니다.
    * **SUBSCRIPTION_KEY**를 Azure Maps 계정 기본 구독 키로 바꿉니다.
    * **UDID**를 업로드한 지오펜스의 udId로 바꿉니다. 
    * 스크립트의 **CreateBlobAsync** 함수에서 이벤트당 Blob을 데이터 스토리지 계정에 만듭니다. **ACCESS_KEY**, **ACCOUNT_NAME** 및 **STORAGE_CONTAINER_NAME**을 스토리지 계정의 액세스 키, 계정 이름 및 데이터 스토리지 컨테이너로 바꿉니다.

10. **Event Grid 구독 추가**를 클릭합니다.
    
    ![Event Grid 추가](./media/tutorial-iot-hub-maps/add-egs.png)

11. 구독 세부 정보를 채우고, **이벤트 구독 세부 정보** 아래에서 구독 이름을 지정하고, 이벤트 스키마에 대해 "Event Grid 스키마"를 선택합니다. **토픽 세부 정보**에서 토픽 형식으로 "Azure IoT Hub 계정"을 선택합니다. 리소스 그룹을 만드는 데 사용한 것과 동일한 구독을 선택하고, "리소스 그룹"으로 "ContosoRental"을 선택하고, "리소스"로 만든 IoT Hub를 선택합니다. 이벤트 유형으로 **디바이스 원격 분석**을 선택합니다. 이러한 옵션이 선택되면 "항목 종류"가 자동으로 "IoT Hub"로 변경됩니다.

    ![Event Grid 구독](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>IoT Hub 메시지 라우팅을 사용하여 이벤트 필터링

Event Grid 구독이 Azure Function에 추가되면 이제 IoT Hub의 **메시지 라우팅** 블레이드에서 Event Grid에 대한 기본 메시지 경로를 볼 수 있습니다. 메시지 라우팅을 사용하면 디바이스 원격 분석 메시지, 디바이스 수명 주기 이벤트 및 디바이스 쌍 변경 이벤트와 같은 다양한 데이터 형식을 다양한 엔드포인트로 라우팅할 수 있습니다. IoT Hub 메시지 라우팅에 대한 자세한 내용은 [IoT Hub 메시지 라우팅 사용](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)을 참조하세요.

![Hub-EG 경로](./media/tutorial-iot-hub-maps/hub-route.png)

예제 시나리오에서는 렌터카가 이동하는 위치를 나타내는 모든 메시지를 필터링하려고 합니다. 이러한 디바이스 원격 분석 이벤트를 Event Grid에 게시하려면 라우팅 쿼리를 사용하여 `Engine` 속성이 **"ON"** 인 이벤트를 필터링합니다. IoT 디바이스-클라우드 메시지를 쿼리하는 다양한 방법이 있습니다. 메시지 라우팅 구문에 대한 자세한 내용은 [IoT Hub 메시지 라우팅](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax)을 참조하세요. 라우팅 쿼리를 만들려면 **RouteToEventGrid** 경로를 클릭하고, **라우팅 쿼리**를 **"Engine='ON'"** 으로 바꾸고, **저장**을 클릭합니다. 이제 IoT Hub는 엔진이 ON(켜기)으로 설정된 디바이스 원격 분석만 게시합니다.

![Hub-EG 필터](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>IoT Hub에 원격 분석 데이터 보내기

Azure Function이 시작되어 실행되면 이제 원격 분석 데이터를 IoT Hub에 보내 Event Grid로 라우팅할 수 있습니다. C# 애플리케이션을 사용하여 렌터카의 차량 내부 디바이스에 대한 위치 데이터를 시뮬레이션해 봅시다. 애플리케이션을 실행하려면 개발 머신에서 .NET Core SDK 2.1.0 이상이 필요합니다. 아래의 단계에 따라 시뮬레이션된 원격 분석 데이터를 IoT Hub에 보냅니다.

1. [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# 프로젝트를 다운로드합니다. 

2. 원하는 텍스트 편집기에서 simulatedCar.cs 파일을 열고, `connectionString`의 값을 디바이스 등록 시 저장한 값으로 바꾸고, 변경 내용을 파일에 저장합니다.
 
3. 머신에 .NET Core가 설치되어 있는지 확인합니다. 로컬 터미널 창에서 C# 프로젝트의 루트 폴더로 이동하고, 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 패키지를 설치합니다.
    
    ```cmd/sh
    dotnet restore
    ```

4. 동일한 터미널에서 다음 명령을 실행하여 렌터카 시뮬레이션 애플리케이션을 빌드하고 실행합니다.

    ```cmd/sh
    dotnet run
    ```

  로컬 터미널은 다음과 같습니다.

  ![터미널 출력](./media/tutorial-iot-hub-maps/terminal.png)

이제 Blob 스토리지 컨테이너를 열면 자동차가 지오펜스 외부에 있었던 위치에 대한 4개의 Blob을 볼 수 있습니다.

![Blob 입력](./media/tutorial-iot-hub-maps/blob.png)

아래의 지도에서는 자동차가 지오펜스 외부에 있었고 일정한 시간 간격으로 기록된 4개의 지점을 보여 줍니다.

![위치 이탈 지점을 보여 주는 지도](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서 사용되는 Azure Maps API를 살펴보려면 다음을 참조하세요.

* [검색 주소 역방향 가져오기](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [지오펜스 가져오기](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Azure Maps REST API의 전체 목록은 다음을 참조하세요.

* [Azure Maps REST API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

IoT 플러그 앤 플레이에 대한 자세한 내용은 다음을 참조하세요.

* [IoT 플러그 앤 플레이](https://docs.microsoft.com/azure/iot-pnp)

IoT용 Azure Certified인 디바이스 목록을 가져오려면 다음을 방문하세요.

* [Azure Certified 디바이스](https://catalog.azureiotsolutions.com/)

디바이스에서 클라우드로 또는 그 반대로 원격 분석을 보내는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [디바이스에서 원격 분석 데이터 보내기](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

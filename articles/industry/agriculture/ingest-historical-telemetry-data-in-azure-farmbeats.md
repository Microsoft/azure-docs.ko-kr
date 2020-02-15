---
title: 기록 원격 분석 데이터 수집
description: 이 문서에서는 기록 원격 분석 데이터를 수집 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0d220d1d88d9d761d9f0eba6187abefb372681be
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131892"
---
# <a name="ingest-historical-telemetry-data"></a>기록 원격 분석 데이터 수집

이 문서에서는 Azure FarmBeats에 기록 센서 데이터를 수집 하는 방법을 설명 합니다.

수집 (IoT) 리소스 사물 인터넷의 기록 데이터 (예: 장치 및 센서)는 FarmBeats에서 일반적인 시나리오입니다. 장치 및 센서에 대 한 메타 데이터를 만든 다음 기록 데이터를 정규 형식으로 FarmBeats에 수집 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행 하기 전에 IoT 장치에서 FarmBeats 및 수집 된 기록 데이터를 설치 했는지 확인 합니다.
또한 다음 단계에 설명 된 대로 파트너 액세스를 사용 하도록 설정 해야 합니다.

## <a name="enable-partner-access"></a>파트너 액세스 사용

Azure FarmBeats 인스턴스에 파트너 통합을 사용 하도록 설정 해야 합니다. 이 단계에서는 Azure FarmBeats 인스턴스에 대 한 액세스 권한이 있는 장치 파트너로 클라이언트를 만들고 이후 단계에서 필요한 다음 값을 제공 합니다.

- API 끝점: Datahub URL입니다 (예: https://\<datahub >).
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호
- EventHub 연결 문자열

다음 단계를 수행합니다.

>[!NOTE]
> 다음 단계를 수행 하려면 관리자 여야 합니다.

1. [Zip 파일](https://aka.ms/farmbeatspartnerscriptv2)을 다운로드 하 고 로컬 드라이브에 압축을 풉니다. Zip 파일 안에는 파일이 하나 있습니다.
2. https://portal.azure.com/에 로그인 하 고 Azure Active Directory-> 앱 등록으로 이동 합니다.

3. FarmBeats 배포의 일부로 만들어진 앱 등록을 클릭 합니다. FarmBeats Datahub와 동일한 이름을 갖게 됩니다.

4. "API 표시"를 클릭 하 > "클라이언트 응용 프로그램 추가"를 클릭 하 고 **04b07795-8ddb-461a-bbee-02f9e1bf7b46** 를 입력 한 후 "범위 권한 부여"를 선택 합니다. 그러면 아래 단계를 수행 하기 위해 Azure CLI (Cloud Shell)에 대 한 액세스 권한이 제공 됩니다.

5. Cloud Shell을 엽니다. 이 옵션은 Azure Portal의 오른쪽 위 모퉁이에 있는 도구 모음에서 사용할 수 있습니다.

    ![Azure Portal 도구 모음](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. 환경이 **PowerShell**로 설정 되었는지 확인 합니다. 기본적으로 Bash로 설정 됩니다.

    ![PowerShell 도구 모음 설정](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Cloud Shell 인스턴스의 1 단계에서 파일을 업로드 합니다.

    ![업로드 도구 모음 단추](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. 파일이 업로드 된 디렉터리로 이동 합니다. 기본적으로 파일은 사용자 이름의 홈 디렉터리에 업로드 됩니다.

9. 다음 스크립트를 실행합니다. 스크립트는 Azure Active Directory > 개요 페이지에서 가져올 수 있는 테 넌 트 ID를 요청 합니다.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. 화면의 지시에 따라 **API 끝점**, **테 넌 트 ID**, **클라이언트 ID**, **클라이언트 암호**및 **EventHub 연결 문자열**의 값을 캡처합니다.
## <a name="create-device-or-sensor-metadata"></a>장치 또는 센서 메타 데이터 만들기

 이제 필수 자격 증명이 있으므로 장치 및 센서를 정의할 수 있습니다. 이렇게 하려면 FarmBeats Api를 호출 하 여 메타 데이터를 만듭니다. 위의 섹션에서 만든 클라이언트 앱으로 Api를 호출 해야 합니다.

 FarmBeats Datahub에는 장치 또는 센서 메타 데이터를 생성 및 관리할 수 있도록 하는 다음과 같은 Api가 있습니다. 파트너는 메타 데이터를 읽고, 만들고, 업데이트 하는 데에만 액세스할 수 있습니다. **파트너는 삭제를 허용 하지 않습니다.**

- /**DeviceModel**: DeviceModel는 장치 (예: 제조업체 및 장치 유형 (예: 게이트웨이 또는 노드)의 메타 데이터에 해당 합니다.
- /**장치**: 장치는 팜에 있는 물리적 장치에 해당 합니다.
- /**SensorModel**: SensorModel 제조업체, 센서 유형 (예: 아날로그 또는 디지털) 및 센서 측정 (예: 주변 온도 및 압력)과 같은 센서의 메타 데이터에 해당 합니다.
- /**센서**: 센서는 값을 기록 하는 실제 센서에 해당 합니다. 센서는 일반적으로 장치 ID를 사용 하 여 장치에 연결 됩니다.  


|        DeviceModel   |  제안   |
| ------- | -------             |
|     유형 (노드, 게이트웨이)        |          장치 노드 또는 게이트웨이의 유형입니다.      |
|          제조업체            |         제조업체의 이름입니다.    |
|  ProductCode                    |  장치 제품 코드 또는 모델 이름 또는 번호입니다. 예: EnviroMonitor # 6800.  |
|            포트          |     포트 이름 및 유형이 며 디지털 또는 아날로그 유형입니다.
|     이름                 |  리소스를 식별 하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다.
      설명     | 모델에 대 한 의미 있는 설명을 제공 합니다.
|    속성          |    제조업체의 추가 속성입니다.   |
|    **디바이스**             |                      |
|   DeviceModelId     |     연결 된 장치 모델의 ID입니다.  |
|  HardwareId          | MAC 주소와 같은 장치에 대 한 고유 ID입니다.
|  ReportingInterval        |   보고 간격 (초)입니다.
|  위치            |  장치 위도 (-90 ~ + 90), 경도 (-180 ~ 180) 및 권한 상승 (미터).   
|ParentDeviceId       |    이 장치가 연결 된 부모 장치의 ID입니다. 예를 들어 게이트웨이에 연결 된 노드입니다. 노드에는 게이트웨이로 parentDeviceId가 있습니다.  |
|    이름            | 리소스를 식별 하는 이름입니다. 장치 파트너는 파트너 측의 장치 이름과 일치 하는 이름을 보내야 합니다. 파트너 장치 이름이 사용자 정의 된 경우 동일한 사용자 정의 이름을 FarmBeats에 전파 해야 합니다.|
|     설명       |      의미 있는 설명을 제공 합니다. |
|     속성    |  제조업체의 추가 속성입니다.
|     **SensorModel**        |          |
|       유형 (아날로그, 디지털)          |      센서 유형 (아날로그 또는 디지털)입니다.       |
|          제조업체            |       센서의 제조업체입니다.     |
|     ProductCode| 제품 코드 또는 모델 이름 또는 번호입니다. 예를 들어 RS-CO2-N01입니다. |
|       SensorMeasures > 이름       | 센서 측정값의 이름입니다. 소문자만 지원 됩니다. 다른 깊이에서 측정 하려면 깊이를 지정 합니다. 예를 들어 soil_moisture_15cm 합니다. 이 이름은 원격 분석 데이터와 일치 해야 합니다.  |
|          SensorMeasures > 데이터 형식       |원격 분석 데이터 형식입니다. 현재는 double이 지원 됩니다.|
|    SensorMeasures > 형식    |센서 원격 분석 데이터의 측정 유형입니다. 시스템 정의 형식은 AmbientTemperature, CO2, Depth, ElectricalConductivity, LeafWetness, Length, LiquidLevel, Nitrate, O2, PH, Phosphate, PointInTime, Potassium, 압력, RainGauge, RelativeHumidity, Salinity, SoilMoisture,,,,,,,,,, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, Uvradiation, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration,와 동일 합니다. 추가 정보를 추가 하려면/ExtendedType API를 참조 하세요.|
|        SensorMeasures > Unit              | 센서 원격 분석 데이터의 단위입니다. 시스템 정의 단위는 NoUnit, 섭씨, 화씨, 온도, Rankine, 파스칼, 수, 인치, 피트, Mm, 센티미터, 미터, 인치, 피트, 마일, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, 학위, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, 백분율, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, 리터, MilliLiter, Seconds,, MicroMolPerMeterSquaredPerSecond, InchesPerHour,,,를 참조 하세요.|
|    SensorMeasures > AggregationType    |  값은 없음, 평균, 최대값, 최소값 또는 StandardDeviation 일 수 있습니다.  |
|          이름            | 리소스를 식별 하는 이름입니다. 예를 들어 모델 이름 또는 제품 이름입니다.  |
|    설명        | 모델에 대 한 의미 있는 설명을 제공 합니다.  |
|   속성       |  제조업체의 추가 속성입니다.  |
|    **수신**      |          |
| HardwareId          |   제조업체에서 설정한 센서의 고유 ID입니다. |
|  SensorModelId     |    연결 된 센서 모델의 ID입니다.   |
| 위치          |  센서 위도 (-90 ~ + 90), 경도 (-180 ~ 180) 및 권한 상승 (미터).|
|   포트 > 이름        |  장치에서 센서가 연결 된 포트의 이름 및 유형입니다. 이 이름은 장치 모델에 정의 된 이름과 동일 해야 합니다. |
|    DeviceID  |    센서가 연결 된 장치의 ID입니다.     |
| 이름            |   리소스를 식별 하는 이름입니다. 예를 들어 센서 이름 또는 제품 이름과 모델 번호 또는 제품 코드를 사용할 수 있습니다.|
|    설명      | 의미 있는 설명을 제공 합니다. |
|    속성        |제조업체의 추가 속성입니다. |

개체에 대 한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조 하십시오.

### <a name="api-request-to-create-metadata"></a>메타 데이터를 만들기 위한 API 요청

API 요청을 만들려면 HTTP (POST) 메서드와 API 서비스에 대 한 URL을 결합 하 고 요청에 대 한 쿼리, 데이터 전송, 생성 또는 삭제를 위해 리소스에 대 한 URI를 결합 합니다. 그런 다음 하나 이상의 HTTP 요청 헤더를 추가 합니다. API 서비스에 대 한 URL은 API 끝점, 즉 Datahub URL (https://\<해당 하는 datahub >)입니다.  

### <a name="authentication"></a>인증

FarmBeats Datahub는 이전 섹션에서 생성 된 다음 자격 증명을 필요로 하는 전달자 인증을 사용 합니다.

- 클라이언트 ID
- 클라이언트 암호
- 테넌트 ID

호출자는 이러한 자격 증명을 사용 하 여 액세스 토큰을 요청할 수 있습니다. 토큰은 다음과 같이 헤더 섹션의 후속 API 요청에서 전송 되어야 합니다.

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

다음 샘플 Python 코드는 FarmBeats에 대 한 후속 API 호출에 사용할 수 있는 액세스 토큰을 제공 합니다. 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 요청 헤더**

FarmBeats Datahub에 대 한 API 호출을 수행할 때 지정 해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.

- **Content-type**: application/json
- **권한 부여**: 전달자 < 액세스-토큰 >
- **수락**: application/json

### <a name="input-payload-to-create-metadata"></a>메타 데이터를 만들기 위한 입력 페이로드

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

장치

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
센서

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
다음 샘플 요청은 장치를 만듭니다. 이 요청에는 요청 본문이 있는 페이로드로 입력 JSON이 있습니다.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Api는 생성 된 각 인스턴스에 대 한 고유 Id를 반환 합니다. 해당 하는 원격 분석 메시지를 보내려면 Id를 유지 해야 합니다.

### <a name="send-telemetry"></a>원격 분석 전송

이제 FarmBeats에서 장치 및 센서를 만들었으므로 연결 된 원격 분석 메시지를 보낼 수 있습니다.

### <a name="create-a-telemetry-client"></a>원격 분석 클라이언트 만들기

처리를 위해 Azure Event Hubs에 원격 분석을 보내야 합니다. Azure Event Hubs는 연결 된 장치 및 응용 프로그램에서 실시간 데이터 (원격 분석) 수집을 가능 하 게 하는 서비스입니다. FarmBeats에 원격 분석 데이터를 보내려면 FarmBeats에서 이벤트 허브로 메시지를 보내는 클라이언트를 만듭니다. 원격 분석을 보내는 방법에 대 한 자세한 내용은 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)를 참조 하세요.

### <a name="send-a-telemetry-message-as-the-client"></a>원격 분석 메시지를 클라이언트로 보내기

Event Hubs 클라이언트로 설정 된 연결이 있으면 JSON으로 이벤트 허브에 메시지를 보낼 수 있습니다.

다음은 원격 분석을 지정 된 이벤트 허브에 클라이언트로 보내는 샘플 Python 코드입니다.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

기록 센서 데이터 형식을 Azure FarmBeats에서 인식 하는 정식 형식으로 변환 합니다. 정식 메시지 형식은 다음과 같습니다.

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

해당 장치 및 센서를 추가한 후 이전 섹션에서 설명한 대로 원격 분석 메시지에서 장치 ID 및 센서 ID를 가져옵니다.

원격 분석 메시지의 예는 다음과 같습니다.


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```

## <a name="troubleshooting"></a>문제 해결

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>센서에서 기록/스트리밍 데이터를 수집 후 원격 분석 데이터를 볼 수 없음

**증상**: 장치 또는 센서가 배포 되 고 FarmBeats 및 수집 원격 분석에서 장치/센서를 EventHub에 만들었지만 FarmBeats에서 원격 분석 데이터를 가져오거나 볼 수 없습니다.

**수정 동작**:

1. 파트너 등록이 올바르게 완료 되었는지 확인-datahub swagger로 이동 하 고,/파트너 API로 이동 하 고, Get을 수행 하 고, 파트너가 등록 되었는지 확인 하 여이를 확인할 수 있습니다. 그렇지 않은 경우 파트너를 추가 하려면 [여기의 단계](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) 를 따르세요.
2. 파트너 클라이언트 자격 증명을 사용 하 여 메타 데이터 (DeviceModel, Device, SensorModel, 센서)를 만들었는지 확인 합니다.
3. 아래 지정 된 대로 올바른 원격 분석 메시지 형식을 사용 했는지 확인 합니다.

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>다음 단계

REST API 기반 통합 정보에 대 한 자세한 내용은 [REST API](rest-api-in-azure-farmbeats.md)을 참조 하세요.

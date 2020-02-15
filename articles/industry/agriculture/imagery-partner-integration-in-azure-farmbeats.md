---
title: 이미지 파트너 통합
description: 이 문서에서는 이미지 파트너 통합에 대해 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131863"
---
# <a name="imagery-partner-integration"></a>이미지 파트너 통합

이 문서에서는 Azure FarmBeats Translator 구성 요소를 사용 하 여 FarmBeats에 이미지 데이터를 보내는 방법을 설명 합니다. Agricultural 이미지 데이터는 multispectral 카메라, 위성, 드 론 등의 다양 한 원본에서 생성할 수 있습니다. Agricultural 이미지 파트너는 FarmBeats와 통합 하 여 고객에 게 팜에 대해 사용자가 생성 한 맵을 제공할 수 있습니다.

데이터를 사용할 수 있게 되 면 FarmBeats Accelerator를 통해 시각화할 수 있으며, agricultural 비즈니스 또는 고객 시스템 통합자를 통해 데이터 fusion 및 기계 학습/인공 지능 (ML/AI) 모델을 빌드하는 데 사용할 수 있습니다.

FarmBeats는 다음을 수행할 수 있는 기능을 제공 합니다.

- /ExtendedType Api를 사용 하 여 사용자 지정 이미지 형식, 원본 및 파일 형식을 정의 합니다.
- /Sha/장면 및/SceneFile Api를 통해 다양 한 소스에서 이미지 데이터를 수집 합니다.

다음 정보는 FarmBeats 시스템에 대 한 모든 형태의 이미지를 가져오는 데 중점을 둔 것입니다.

**드 론 이미지** 섹션을 선택 하면 드 론 orthomosaic의 고해상도 이미지를 표시 하는 팝업이 열립니다. 파트너 소프트웨어에 액세스 하 여 드 론 항공편을 계획 하 고 원시 데이터를 가져올 수 있습니다. 경로 계획 및 orthomosaic 이미지 중철 파트너의 소프트웨어를 계속 사용 합니다.

Drone 파트너는 고객이 고객 계정을 Azure의 FarmBeats 인스턴스와 연결 하도록 설정 해야 합니다.

FarmBeats를 연결 하려면 드 론 파트너 소프트웨어에서 다음 자격 증명을 사용 해야 합니다.

- API 끝점
- 테넌트 ID
- 클라이언트 ID
- 클라이언트 암호

## <a name="api-development"></a>API 개발

Api에는 Swagger 기술 설명서가 포함 되어 있습니다. Api 및 해당 요청 또는 응답에 대 한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조 하십시오.

## <a name="authentication"></a>인증

FarmBeats는 Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD)를 사용 합니다. Azure App Service는 기본 제공 인증 및 권한 부여를 지원 합니다. 

Azure AD에 대한 자세한 내용은 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)를 참조하세요.   

FarmBeats Datahub는 다음 자격 증명이 필요한 전달자 인증을 사용 합니다.

- 클라이언트 ID
- 클라이언트 암호
- 테넌트 ID

위의 자격 증명을 사용 하 여 호출자는 다음과 같이 후속 API 요청에서 전송 되어야 하는 액세스 토큰을 요청할 수 있습니다.

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

다음 Python 코드 샘플은 액세스 토큰을 검색 합니다. 그런 다음 FarmBeats에 대 한 후속 API 호출에 토큰을 사용할 수 있습니다.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>HTTP 요청 헤더

FarmBeats Datahub에 대 한 API 호출을 수행할 때 지정 해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.

**머리글** | **설명 및 예제**
--- | ---
Content-Type  | 요청 형식 (Content-type: application/<format>)입니다. FarmBeats Datahub Api의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여 | API 호출을 수행 하는 데 필요한 액세스 토큰을 지정 합니다. 권한 부여: 전달자 < 액세스-토큰 >
Accept  | 응답 형식입니다. FarmBeats Datahub Api의 경우 형식은 JSON입니다. 수락: application/json


## <a name="api-requests"></a>API 요청

REST API 요청을 만들려면 다음을 결합 합니다.

- HTTP 메서드 (GET, POST 및 PUT)입니다.
- API 서비스에 대 한 URL입니다.
- 쿼리, 데이터 전송, 업데이트 또는 삭제에 대 한 리소스 URI입니다.
- 하나 이상의 HTTP 요청 헤더입니다.

필요에 따라 필터에 대 한 GET 호출에 쿼리 매개 변수를 포함 하 고, 크기를 제한 하 고, 응답의 데이터를 정렬할 수 있습니다.

다음 샘플 요청은 장치 목록을 가져오는 것입니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

대부분 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요 합니다.

다음 샘플 요청은 장치를 만드는 것입니다. 이 샘플에는 요청 본문을 포함 하는 입력 JSON이 있습니다.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>데이터 형식

JSON은 임의의 데이터 구조에 대 한 간단한 텍스트 표현을 제공 하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [JSON org](https://JSON.org)를 참조 하세요.

## <a name="ingest-imagery-into-farmbeats"></a>FarmBeats에 이미지 수집

파트너에 게 FarmBeats Datahub에 연결 하기 위한 자격 증명이 있는 경우 파트너는 Translator 구성 요소에서 다음 단계를 수행 합니다.

1.  업로드할 이미지 유형에 따라 다음 필드에 대해 새 확장 유형을 만듭니다.

    - **장면 소스**: 예: drone_partner_name
    - **장면 유형**(예: 드 론)
    - **장면 파일 형식**(예: chlorophyll index)
    - **장면 파일 콘텐츠 형식**(예: image/tiff)

2.  /팜 API를 호출 하 여 Azure FarmBeats 시스템 내에서 팜 목록을 가져옵니다.
3.  고객에 게 팜 목록에서 단일 팜을 선택할 수 있는 기능을 제공 합니다.

    파트너 시스템은 경로 계획 및 단일 비행 및 이미지 수집을 수행할 파트너 소프트웨어 내의 팜을 표시 해야 합니다.

4.  /장면 API를 호출 하 고 고유한 장면 ID를 사용 하 여 새 장면을 만드는 데 필요한 세부 정보를 제공 합니다.
5.  FarmBeats 시스템에서 선택한 팜의 컨텍스트에서 필요한 이미지를 FarmBeats Datahub에 업로드 하는 blob SAS URL을 수신 합니다.

API 호출에 대 한 자세한 흐름은 다음과 같습니다.

### <a name="step-1-extendedtype"></a>1 단계: ExtendedType

/ExtendedType API를 확인 하 여 FarmBeats에서 형식 및 파일 원본을 사용할 수 있는지 확인 합니다. 이렇게 하려면/ExtendedType API에 대 한 GET을 호출 합니다.

시스템 정의 값은 다음과 같습니다.

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

이 단계는 일회성 설정입니다. 이 새 장면 형식의 범위는 Azure FarmBeats가 설치 된 구독으로 제한 됩니다.

예를 들어 SceneSource: "SlantRange"를 추가 하려면 "SceneSource" 입력 페이로드를 사용 하 여/ExtendedType API의 ID를 입력 합니다.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

녹색 필드는 시스템 정의 장면 원본 값에 새로 추가 된 기능입니다.

### <a name="step-2-get-farm-details"></a>2 단계: 팜 세부 정보 가져오기

장면 (tiff 또는 .csv 파일)은 팜의 컨텍스트에 있습니다. /Ss API에 대해 GET을 수행 하 여 팜 세부 정보를 가져와야 합니다. API는 FarmBeats에서 사용할 수 있는 팜 목록을 반환 합니다. 데이터를 수집 하려는 팜을 선택할 수 있습니다.

/팜 응답 가져오기:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>3 단계: 장면 ID 만들기 (POST 호출)

지정 된 정보를 사용 하 여 장면이 연결 된 날짜, 시퀀스 및 팜 ID를 제공 하는 새 장면 (tiff 또는 .csv 파일)을 만듭니다. 장면에 연결 된 메타 데이터는 기간 및 측정 유형을 포함 하는 속성에서 정의할 수 있습니다.

새 장면을 만들면 팜에 연결 된 새 장면 ID가 만들어집니다. 장면 ID를 만든 후에는 사용자가 동일한를 사용 하 여 새 파일 (tiff 또는 .csv)을 만들고 파일의 내용을 저장할 수 있습니다.

/장면 API에 대 한 POST 호출의 예제 입력 페이로드는 다음과 같습니다.

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API 응답:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**장면 파일 만들기**

3 단계에서 반환 된 장면 ID는 장면 파일에 대 한 입력입니다. 장면 파일은 24 시간 동안 유효한 SAS URL 토큰을 반환 합니다.

사용자가 프로그래밍 방식으로 이미지 스트림을 업로드 해야 하는 경우에는 blob storage SDK를 사용 하 여 장면 파일 ID, 위치 및 URL을 사용 하 여 메서드를 정의할 수 있습니다.

/SceneFile API의 POST 호출에 대 한 예제 입력 페이로드:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API 응답:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

/SceneFile API에 대 한 POST 호출에서는 SAS 업로드 URL을 반환 합니다 .이 URL은 Azure Blob storage 클라이언트 또는 라이브러리를 사용 하 여 .csv 또는 tiff 파일을 업로드 하는 데 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

REST API 기반 통합 정보에 대 한 자세한 내용은 [REST API](rest-api-in-azure-farmbeats.md)을 참조 하세요.

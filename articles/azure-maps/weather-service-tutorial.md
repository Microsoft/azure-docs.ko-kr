---
title: '자습서: Azure Notebooks(Python)를 사용하여 센서 데이터와 날씨 예측 데이터 조인 | Microsoft Azure Maps'
description: 이 자습서에서는 Azure Notebooks(Python)를 사용하여 Microsoft Azure Maps Weather Service의 날씨 예측 데이터와 센서 데이터를 조인하는 방법을 보여줍니다.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6d49a305a9b2e02d9e9d743ff8f076f453a08fcb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989623"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>자습서: Azure Notebooks(Python)를 사용하여 센서 데이터와 날씨 예측 데이터 조인

풍력은 기후 변화에 맞서기 위해 화석 연료를 대체할 수 있는 에너지원입니다. 바람은 본질적으로 일관되지 않으므로 풍력 연산자는 ML(기계 학습) 모델을 빌드하여 풍력 용량을 예측해야 합니다. 이 예측은 전기 수요를 충족하고 그리드 안정성을 보장하기 위해 필요합니다. 이 자습서에서는 Azure Maps 날씨 예측 데이터가 날씨 판독값이 있는 데모 데이터와 결합되는 방식을 안내합니다. 날씨 예측 데이터는 Azure Maps 날씨 서비스를 호출하여 요청됩니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 클라우드의 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)에서 데이터 파일로 작업합니다.
> * 파일에서 데모 데이터를 로드합니다.
> * Python에서 Azure Maps REST API를 호출합니다.
> * 위치 데이터를 지도에 렌더링합니다.
> * Azure Maps [일일 예측](https://aka.ms/AzureMapsWeatherDailyForecast) 날씨 데이터를 사용하여 데모 데이터를 보강합니다.
> * 예측 데이터를 그래프로 표시합니다.


## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 먼저 다음을 수행해야 합니다.

1. [계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps)의 지침에 따라 S0 가격 책정 계층에서 Azure Maps 계정 구독을 만듭니다.
2. 계정에 대한 기본 구독 키를 가져오려면 [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)의 지침을 따릅니다.


Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

Azure Notebook에 익숙해지고 시작하는 방법을 알아보려면 [Azure Notebook 만들기](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook)의 지침을 따르세요.

> [!Note]
> 이 프로젝트의 Jupyter Notebook 파일은 [기상도 Juppyter Notebook 리포지토리](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)에서 다운로드할 수 있습니다.

## <a name="load-the-required-modules-and-frameworks"></a>필요한 모듈 및 프레임워크 로드

필요한 모든 모듈과 프레임워크를 로드하려면 다음 스크립트를 실행합니다.

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>날씨 데이터 가져오기

이 자습서에서는 4개의 서로 다른 풍력 터빈에 설치된 센서의 날씨 데이터 판독값을 활용합니다. 샘플 데이터는 30일의 날씨 판독값으로 구성됩니다. 이러한 판독값은 각 터빈 위치 근처의 날씨 데이터 센터에서 수집됩니다. 데모 데이터에는 기온, 풍속 및 풍향에 대한 데이터 판독값이 포함되어 있습니다. 데모 데이터는 [여기](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)서 다운로드할 수 있습니다. 다음 스크립트는 데모 데이터를 Azure Notebook으로 가져옵니다.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>일일 예측 데이터 요청

시나리오에서는 각 센서 위치에 대한 일일 예측을 요청하려고 합니다. 다음 스크립트는 Azure Maps 날씨 서비스의 [일일 예측 API](https://aka.ms/AzureMapsWeatherDailyForecast)를 호출하여 현재 날짜로부터 다음 15일간의 각 풍력 터빈에 대한 일일 날씨 예측을 가져옵니다.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

다음 스크립트는 Azure Maps [지도 이미지 가져오기 서비스](https://docs.microsoft.com/rest/api/maps/render/getmapimage)를 호출하여 터빈 위치를 지도에 렌더링합니다.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![터빈 위치](./media/weather-service-tutorial/location-map.png)


날씨 데이터 센터의 스테이션 ID를 기반으로 하는 데모 데이터를 사용하여 예측 데이터를 그룹화합니다. 이 그룹화는 예측 데이터를 사용하여 데모 데이터를 보강합니다. 

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

다음 테이블에는 터빈 위치 중 하나에 대한 기록 데이터와 예측 데이터가 결합되어 있습니다.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![그룹화된 데이터](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>예측 데이터 그리기

예측된 값을 예측한 날짜와 비교하여 표시합니다. 이 플롯을 사용하면 다음 15일 동안 풍속 및 풍향 변화를 확인할 수 있습니다.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

아래 그래프는 예측 데이터를 시각화한 것입니다. 풍속 변화의 경우 왼쪽 그래프를 참조하세요. 풍향 변화의 경우 오른쪽 그래프를 참조하세요. 이 데이터는 데이터가 요청된 날짜로부터 다음 15일 동안의 예측입니다.

<center>

![풍속 플롯](./media/weather-service-tutorial/speed-date-plot.png) ![풍향 플롯](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Maps REST API를 호출하여 날씨 예측 데이터를 가져오는 방법을 알아보았습니다. 또한 데이터를 그래프로 시각화하는 방법을 알아보았습니다.

Azure Notebooks 내에서 Azure Maps REST API를 호출하는 방법에 대한 자세한 내용은 [Azure Notebooks를 사용한 EV 라우팅](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing)을 참조하세요.

이 자습서에서 사용되는 Azure Maps API를 살펴보려면 다음을 참조하세요.

* [일일 예측](https://aka.ms/AzureMapsWeatherDailyForecast)
* [렌더링 - 지도 이미지 가져오기](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Azure Maps REST API의 전체 목록은 [Azure Maps REST API](https://docs.microsoft.com/azure/azure-maps/consumption-model)를 참조하세요.

Azure Notebooks에 대한 자세한 내용은 [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)를 참조하세요.

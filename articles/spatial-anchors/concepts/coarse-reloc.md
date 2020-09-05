---
title: 거친 Relocalization
description: 정교 하지 않은 재 지역화를 사용 하 여 가까운 앵커를 찾는 방법을 알아봅니다.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 02f5c570b7400266ac648eaa257fb76d26819a40
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003187"
---
# <a name="coarse-relocalization"></a>광역 위치 재결정

정교 하지 않은 지역화는 질문에 대 한 초기 답변을 제공 하는 기능입니다. *여기서 장치는* 언제 입니까? 응답은 정확 하지 않지만 대신 다음 형식으로 되어 있습니다. *이러한 앵커에 근접 하 고 있습니다. 그 중 하나를 찾아 보세요*.

정교 하지 않은 다시 지역화는 생성 및 앵커 쿼리와 함께 다양 한 장치 센서 판독값을 연결 하는 방식으로 작동 합니다. 옥외 시나리오에서 센서 데이터는 일반적으로 장치의 GPS (글로벌 위치 시스템) 위치입니다. GPS를 사용할 수 없거나 신뢰할 수 없는 경우 (예: 실내) 센서 데이터는 범위의 WiFi 액세스 지점과 Bluetooth 신호로 구성 됩니다. 수집 된 모든 센서 데이터는 공간 인덱스를 유지 관리 하는 데 기여 합니다 .이는 Azure 공간 앵커에서 장치의 약 100 미터 이내에 있는 앵커를 빠르게 결정 하는 데 사용 됩니다.

거칠게 다시 지역화에서 사용 하도록 설정 된 앵커의 빠른 조회는 전 세계 규모의 지리적으로 분산 된 컬렉션 (즉, 수백만 개의 지리적으로 분산 된) 앵커로 지원 되는 응용 프로그램 개발을 간소화 합니다. 고정 관리의 복잡성은 모두 숨겨져 있으므로 놀라운 응용 프로그램 논리에 더 집중할 수 있습니다. 모든 앵커는 Azure 공간 앵커에 의해 백그라운드에서 수행 됩니다.

## <a name="collected-sensor-data"></a>수집 된 센서 데이터

앵커 서비스로 보낼 수 있는 센서 데이터는 다음 중 하나입니다.

* GPS 위치: 위도, 경도, 고도.
* 범위 내 WiFi 액세스 지점의 신호 강도입니다.
* 범위에 있는 Bluetooth 오류 신호 강도입니다.

일반적으로 응용 프로그램은 GPS, WiFi 또는 데이터에 액세스 하기 위한 장치 특정 권한을 획득 해야 합니다. 또한 위의 일부 센서 데이터는 특정 플랫폼에서 설계에 의해 제공 되지 않습니다. 이러한 상황을 고려 하 여 센서 데이터 컬렉션은 선택 사항이 며 기본적으로 해제 되어 있습니다.

## <a name="set-up-the-sensor-data-collection"></a>센서 데이터 수집 설정

먼저 센서 지문 공급자를 만들고 세션에서이를 인식 하도록 합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

다음에는 거칠게 다시 지역화에 사용할 센서를 결정 해야 합니다. 이 결정은 개발 중인 응용 프로그램에 따라 달라 지 며, 다음 표의 권장 사항을 잘 알고 있어야 합니다.

|                 | 실내 | 야외 |
|-----------------|---------|----------|
| **GPS**         | 꺼짐 | 설정 |
| **무선**        | 켜기 | 설정 (선택 사항) |
| **오류 있는 오류** | 설정 (주의 사항, 아래 참조) | 끄기 |

### <a name="enabling-gps"></a>GPS 사용

응용 프로그램에 이미 장치의 GPS 위치에 액세스할 수 있는 권한이 있다고 가정 하면이를 사용 하도록 Azure 공간 앵커를 구성할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

응용 프로그램에서 GPS를 사용 하는 경우 하드웨어에서 제공 하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 낮은 빈도 (1 Hz 미만)
* 불안정/잡음이 있습니다 (평균 7-m 표준 편차).

일반적으로 장치 OS와 Azure 공간 앵커는 이러한 문제를 완화 하기 위해 원시 GPS 신호에 대 한 몇 가지 필터링 및 보 외의 작업을 수행 합니다. 이러한 추가 처리에는 수렴에 추가 시간이 필요 하기 때문에 최상의 결과를 위해서는 다음을 수행 해야 합니다.

* 응용 프로그램에서 가능한 한 빨리 센서 지문 공급자를 만듭니다.
* 여러 세션 사이에서 센서 지문 공급자를 유지 합니다.
* 여러 세션 간에 센서 지문 공급자 공유

앵커 세션 외부에서 센서 지문 공급자를 사용 하려는 경우 센서 추정치를 요청 하기 전에 먼저 시작 해야 합니다. 예를 들어 다음 코드는 맵의 장치 위치를 실시간으로 업데이트 하는 작업을 처리 합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>WiFi 사용

응용 프로그램에 이미 장치의 WiFi 상태에 액세스할 수 있는 권한이 있다고 가정 하면이를 사용 하도록 Azure 공간 앵커를 구성할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

응용 프로그램에서 WiFi를 사용 하는 경우 하드웨어에서 제공 하는 판독값은 일반적으로 다음과 같습니다.

* 비동기 및 낮은 빈도 (0.1 Hz 미만)
* 잠재적으로 OS 수준에서 제한 됩니다.
* 불안정/잡음이 있습니다 (평균 3-dBm 표준 편차).

Azure 공간 앵커는 이러한 문제를 완화 하려는 시도에서 세션 중에 필터링 된 WiFi 신호 강도 맵을 빌드하려고 합니다. 최상의 결과를 위해서는 다음을 수행 해야 합니다.

* 첫 번째 앵커를 배치 하기 전에 세션을 만듭니다.
* 세션을 가능한 한 오랫동안 활성 상태로 유지 합니다. 즉, 모든 앵커와 쿼리를 하나의 세션으로 만듭니다.

### <a name="enabling-bluetooth-beacons"></a>Bluetooth 오류 신호 사용

응용 프로그램에 이미 장치의 Bluetooth 상태에 대 한 액세스 권한이 있다고 가정 하 여 Azure 공간 앵커를 사용 하도록 구성할 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

탐지 장치는 일반적으로 Uuid 및 MAC 주소를 포함 하 여 모든 항목을 구성할 수 있는 다양 한 장치입니다. 이러한 유연성은 사용자의 Uuid에 의해 고유 하 게 식별 되는 신호를 고려 하 여 Azure 공간 앵커에 문제가 될 수 있습니다. 이러한 고유성이 이러한 고유성이 보장 되지 않을 경우 공간 wormholes이 발생할 가능성이 높습니다. 최상의 결과를 위해서는 다음을 수행 해야 합니다.

* 고유 Uuid를 오류 신호에 할당 합니다.
* 일반적으로 grid와 같은 일반 패턴으로 배포 합니다.
* 고유한 오류 신호 Uuid 목록을 센서 지문 공급자에 전달 합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure 공간 앵커는 알려진 오류 신호 근접 Uuid 목록에 있는 Bluetooth 오류 표시만 추적 합니다. 허용 되는 Uuid를 포함 하도록 프로그래밍 된 악성 신호는 여전히 서비스 품질에 부정적인 영향을 줄 수 있습니다. 이러한 이유로 해당 배포를 제어할 수 있는 큐 레이트 공간 에서만 오류 신호를 사용 해야 합니다.

## <a name="querying-with-sensor-data"></a>센서 데이터로 쿼리

연결 된 센서 데이터로 앵커를 만든 후에는 장치에서 보고 한 센서 판독값을 사용 하 여 검색을 시작할 수 있습니다. 더 이상 찾을 것으로 예상 되는 앵커 목록에 서비스를 제공할 필요가 없습니다. 대신, 서비스에서 온보드 센서에서 보고 하는 장치의 위치를 알 수 있도록 합니다. 그런 다음 Azure 공간 앵커는 장치에 가까운 앵커 집합을 파악 하 여 시각적으로 일치 시 키 려 고 합니다.

쿼리에서 센서 데이터를 사용 하도록 하려면 "주변 장치" 조건을 만들어 시작 합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

`DistanceInMeters`매개 변수는 콘텐츠를 검색 하기 위해 앵커 그래프를 탐색 하는 정도를 제어 합니다. 모든 미터의 일정 한 밀도에서 앵커를 사용 하 여 일부 공간을 채운 경우를 가정 합니다. 또한 장치의 카메라는 단일 앵커를 관찰 하 고 서비스에서 성공적으로 찾았습니다. 현재 관찰 중인 단일 앵커 대신 가까이 배치 된 모든 앵커를 검색 하는 것이 가장 좋습니다. 사용자가 배치한 앵커를 그래프에 연결 했다고 가정할 경우, 서비스는 그래프의 가장자리를 따라 주변 앵커를 모두 검색할 수 있습니다. 그래프 트래버스의 양은에 의해 제어 됩니다. `DistanceInMeters` 찾은 앵커와 연결 된 모든 앵커를 지정 하는 것이 보다 가깝습니다 `DistanceInMeters` .

의 값이 크면 `MaxResultCount` 성능에 부정적인 영향을 줄 수 있습니다. 응용 프로그램에 대 한 적절 한 값으로 설정 합니다.

마지막으로 센서 기반 조회를 사용 하도록 세션에 지시 해야 합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>예상 결과

소비자 등급 GPS 장치는 일반적으로 매우 정확 하지 않습니다. [Zandenbergen 및 바 Beau (2011)][6] 의 연구는 지원 되는 gps (-gps)을 사용 하 여 모바일 폰의 정확도를 평균 7 미터 (매우 큰 값)로 보고 합니다. 이러한 측정 오류를 고려 하 여 서비스는 해당 앵커를 GPS 공간의 확률 배포판으로 처리 합니다. 따라서 앵커는 현재 (즉, 신뢰도가 95 이상인 경우) 해당 true, 알 수 없는 GPS 위치를 포함 하는 공간의 영역입니다.

GPS를 사용 하 여 쿼리할 때 동일한 추론을 적용 합니다. 장치는 진정한 알려지지 않은 GPS 위치 주변의 다른 공간 신뢰 지역으로 표시 됩니다. 주변 앵커를 검색 하면 아래 이미지에 나와 있는 것 처럼 신뢰 지역이 장치의 신뢰 지역에 *충분히 가까운* 앵커를 쉽게 찾을 수 있습니다.

![GPS를 사용 하 여 앵커 후보 선택](media/coarse-reloc-gps-separation-distance.png)

두 앵커 생성 모두와 쿼리 중에 GPS 신호의 정확도는 반환 되는 앵커 집합에 큰 영향을 미칩니다. 이와 대조적으로, WiFi/오류 신호를 기반으로 하는 쿼리는 쿼리와 공통적으로 하나 이상의 액세스 지점/신호를 포함 하는 모든 앵커를 고려 합니다. 이러한 의미에서 WiFi/오류 신호를 기반으로 하는 쿼리 결과는 주로 액세스 요소/오류 신호 및 환경적 장애물의 물리적 범위에 따라 결정 됩니다.

아래 표는 각 센서 유형에 대해 예상 되는 검색 공간을 예상 합니다.

| 센서      | 검색 공간 반지름 (근사값) | 세부 정보 |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | 다른 요소 간의 GPS 불확실성에 의해 결정 됩니다. 보고 된 숫자는 7 미터의-GPS를 사용 하는 휴대폰의 GPS 정확도에 대해 예상 됩니다. |
| WiFi        | 50 m-100 m | 무선 액세스 지점의 범위에 따라 결정 됩니다. 빈도, 전송기 강도, 물리적 장애물, 간섭 등에 따라 달라 집니다. |
| 오류 있는 오류 |  70 m | 신호 범위에 따라 결정 됩니다. 빈도, 전송 강도, 물리적 장애물, 간섭 등에 따라 달라 집니다. |

## <a name="per-platform-support"></a>플랫폼 당 지원

다음 표에서는 플랫폼별 주의 사항과 함께 지원 되는 각 플랫폼에서 수집 된 센서 데이터를 요약 하 여 보여 줍니다.

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | N/A | [Locationmanager][3] API (GPS 및 네트워크 모두)를 통해 지원 됨 | [Cllocationmanager][4] api를 통해 지원 됨 |
| **무선**        | 3 초 마다 한 번의 검색 속도로 지원 됨 | 지원됨. API 수준 28부터 WiFi 검색은 2 분 마다 4 개의 호출로 제한 됩니다. Android 10에서는 개발자 설정 메뉴에서 제한을 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [Android 설명서][5]를 참조 하세요. | 해당 없음-공용 API 없음 |
| **오류 있는 오류** | [Eddystone][1] 및 [ibeacon 장치로][2] 제한 | [Eddystone][1] 및 [ibeacon 장치로][2] 제한 | [Eddystone][1] 및 [ibeacon 장치로][2] 제한 |

## <a name="next-steps"></a>다음 단계

앱에서 거칠게 relocalization를 사용 합니다.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9

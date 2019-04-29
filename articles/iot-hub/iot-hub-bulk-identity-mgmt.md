---
title: Azure IoT Hub 디바이스 ID 가져오기 및 내보내기 | Microsoft Docs
description: Azure IoT 서비스 SDK를 사용하여 ID 레지스트리에 대한 대량 작업을 수행하고 디바이스 ID를 가져오기 및 내보내기를 수행하는 방법입니다. 가져오기 작업을 사용하여 대량으로 디바이스 ID를 생성, 업데이트 및 삭제할 수 있습니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: 274b77644326cbf73696aae77b48afcbc63aa4c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322794"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>IoT Hub 장치 id 대량에서 가져오기 및 내보내기

각 IoT Hub에는 서비스에서 장치마다 리소스를 만드는 데 사용할 수 있는 ID 레지스트리가 있습니다. 또한 ID 레지스트리를 통해 디바이스 지향 엔드포인트에 대한 액세스를 제어할 수 있습니다. 이 문서에서는 ID 레지스트리에서 디바이스 ID를 대량으로 가져오고 내보내는 방법에 대해 설명합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

가져오기 및 내보내기 작업은 사용자가 IoT Hub에 대해 대량 서비스 작업을 실행할 수 있는 *작업* 상황에서 이루어집니다.

**RegistryManager** 클래스는 **Job** 프레임워크를 사용하는 **ExportDevicesAsync** 및 **ImportDevicesAsync** 메서드를 포함합니다. 이러한 메서드를 사용하면 전체 IoT Hub ID 레지스트리를 내보내고, 가져오고, 동기화할 수 있습니다.

이 항목에서는 **RegistryManager** 클래스 및 **Job** 시스템을 사용하여 IoT Hub의 ID 레지스트리로 디바이스를 대량으로 내보내거나 이러한 레지스트리에서 디바이스를 대량으로 가져오는 작업에 대해 설명합니다. 또한 Azure IoT Hub Device Provisioning Service를 사용하여 사용자 개입 없이, 하나 이상의 IoT Hub에 대해 무인 Just-In-Time 프로비저닝을 수행할 수도 있습니다. 자세한 내용은 [프로비저닝 서비스 설명서](/azure/iot-dps)를 참조하세요.


## <a name="what-are-jobs"></a>작업이란?

ID 레지스트리 작업은 다음 작업을 수행할 때 **작업** 시스템을 사용합니다.

* 표준 런타임 작업에 비해 잠재적으로 실행 시간이 깁니다.

* 사용자에게 많은 양의 데이터를 반환합니다.

단일 API 호출을 기다리거나 작업 결과를 차단하는 대신에 작업은 비동기적으로 해당 IoT Hub에 대한 **작업**을 만듭니다. 그런 다음 즉시 **JobProperties** 개체를 반환합니다.

다음 C# 코드 조각은 작업을 만드는 방법을 보여 줍니다.

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> C# 코드에서 **RegistryManager** 클래스를 사용하려면 프로젝트에 **Microsoft.Azure.Devices** NuGet 패키지를 추가합니다. **RegistryManager** 클래스는 **Microsoft.Azure.Devices** 네임스페이스에 있습니다.

**RegistryManager** 클래스를 사용하면 반환된 **JobProperties** 메타데이터를 사용하는 **작업**의 상태를 쿼리할 수 있습니다. **RegistryManager** 클래스의 인스턴스를 만들려면 **CreateFromConnectionString** 메서드를 사용합니다.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

IoT Hub에 대한 연결 문자열을 찾으려면 Azure Portal에서 다음을 수행합니다.

- IoT Hub로 이동

- **공유 액세스 정책**을 선택합니다.

- 필요한 사용 권한을 고려하여 정책을 선택합니다.

- 화면 오른쪽의 패널에서 연결 문자열을 복사합니다.

다음 C# 코드 조각은 매 5초마다 폴링하여 작업이 실행을 마쳤는지 여부를 확인하는 방법을 보여 줍니다.

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>내보내기 디바이스

**ExportDevicesAsync** 메서드를 사용하여 [공유 액세스 서명](../storage/common/storage-security-guide.md#data-plane-security)을 사용하는 [Azure Storage](../storage/index.yml) Blob 컨테이너에 전체 IoT Hub ID 레지스트리를 내보냅니다.

이 메서드를 사용하면 사용자가 제어하는 blob 컨테이너에 디바이스 정보의 신뢰할 수 있는 백업을 만들 수 있습니다.

**ExportDevicesAsync** 메서드에 매개변수 두 개를 지정해야 합니다.

* Blob 컨테이너의 URI가 포함된 *문자열* . 이 URI는 컨테이너에 대한 쓰기 액세스 권한을 부여하는 SAS 토큰을 포함해야 합니다. 작업은 직렬화된 내보내기 디바이스 데이터를 저장하기 위해 이 컨테이너에 블록 blob를 만듭니다. SAS 토큰은 이러한 사용 권한을 포함해야 합니다.

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* 내보내기 데이터에서 인증 키를 제외하려는지 여부를 나타내는 *부울* 값입니다. **false**인 경우 인증 키가 내보내기 출력에 포함됩니다. 그렇지 않으면 키는 **null**로 내보내집니다.

다음 C# 코드 조각은 내보내기 데이터에 디바이스 인증 키를 포함하고 있는 내보내기 작업을 시작한 다음 완료를 폴링하는 방법을 보여 줍니다.

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

작업은 출력을 제공된 blob 컨테이너에 이름 **devices.txt**의 블록 blob로 저장합니다. 출력 데이터는 JSON 직렬화된 디바이스 데이터로 구성되며 한 디바이스가 한 줄에 표시됩니다.

다음 예제에서는 출력 데이터를 보여줍니다.

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

디바이스에 쌍으로 된 데이터가 있는 경우 해당 쌍 데이터를 디바이스 데이터와 함께 내보냅니다. 다음 예제는 이러한 형식을 보여줍니다. "twinETag" 줄의 모든 데이터는 끝까지 쌍으로 된 데이터입니다.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

코드에서 이 데이터에 액세스해야 하는 경우 **ExportImportDevice** 클래스를 사용하여 이 데이터를 쉽게 역직렬화할 수 있습니다. 다음 C# 코드 조각은 이전에 블록 Blob에 내보낸 디바이스 정보를 읽는 방법을 보여 줍니다.

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>디바이스 가져오기

**RegistryManager** 클래스의 **ImportDevicesAsync** 메서드를 사용하여 IoT Hub ID 레지스트리에서 대량 가져오기 및 동기화 작업을 수행할 수 있습니다. **ExportDevicesAsync** 메서드와 마찬가지로 **ImportDevicesAsync** 메서드도 **작업** 프레임워크를 사용합니다.

ID 레지스트리에 새 디바이스를 프로비전할 뿐만 아니라 기존 디바이스를 업데이트 및 삭제할 수도 있으므로 **ImportDevicesAsync** 메서드를 사용할 때 주의합니다.

> [!WARNING]
> 가져오기 작업은 실행 취소할 수 없습니다. ID 레지스트리를 대량 변경하기 전에 항상 **ExportDevicesAsync** 메서드를 사용하여 기존 데이터를 다른 Blob 컨테이너에 백업합니다.

**ImportDevicesAsync** 메서드에 매개 변수 두 개를 선택합니다.

* 작업에 대한 *입력*으로 [Azure Storage](../storage/index.yml) Blob 컨테이너의 URI가 포함된 *문자열*. 이 URI는 컨테이너에 대한 읽기 액세스 권한을 부여하는 SAS 토큰을 포함해야 합니다. 이 컨테이너에는 ID 레지스트리에 가져올 직렬화된 디바이스 데이터가 포함된 **devices.txt** 이름의 Blob이 있어야 합니다. 가져오기 데이터는 **ExportImportDevice** 작업이 **devices.txt** Blob을 생성할 때 사용하는 것과 같은 JSON 형식의 디바이스 정보를 포함해야 합니다. SAS 토큰은 이러한 사용 권한을 포함해야 합니다.

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* 작업에서 *출력*으로 사용할 [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) blob 컨테이너의 URI가 포함된 *문자열*. 작업은 이 컨테이너에 완료된 가져오기 **작업**에서 나온 오류 정보를 저장하기 위한 블록 blob를 생성합니다. SAS 토큰은 이러한 사용 권한을 포함해야 합니다.

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> 두 매개 변수가 같은 blob 컨테이너를 가리킬 수 있습니다. 단순히 별도 매개 변수를 사용하여 출력 컨테이너에 추가 사용 권한이 필요할 때 데이터를 더 강력하게 제어할 수 있습니다.

다음 C# 코드 조각은 가져오기 작업을 시작하는 방법을 보여 줍니다.

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

디바이스 쌍에 데이터를 가져오는 데도 이 메서드를 사용할 수 있습니다. 데이터 입력의 형식은 **ExportDevicesAsync** 섹션에 나온 형식과 같습니다. 이러한 방식으로 내보낸 데이터를 다시 가져올 수 있습니다. **$metadata**는 선택 사항입니다.

## <a name="import-behavior"></a>가져오기 동작

**ImportDevicesAsync** 메서드를 사용하여 ID 레지스트리에서 다음 대량 작업을 수행할 수 있습니다.

* 새 디바이스 대량 등록
* 기존 디바이스의 대량 삭제
* 대량으로 상태 변경(디바이스를 사용 또는 사용하지 않도록 설정)
* 새 디바이스 인증 키의 대량 할당
* 디바이스 인증 키의 대량 자동 다시 생성
* 쌍으로 된 데이터의 대량 업데이트

단일 **ImportDevicesAsync** 호출 내에서 이전 작업의 조합을 수행할 수 있습니다. 예를 들어 새 디바이스를 등록하는 동시에 기존 디바이스를 삭제 또는 업데이트할 수 있습니다. **ExportDevicesAsync** 메서드와 함께 사용하는 경우 모든 디바이스를 한 IoT Hub에서 다른 IoT Hub로 완전히 마이그레이션할 수 있습니다.

가져오기 파일에 쌍으로 된 메타데이터가 있는 경우 이 메타데이터는 쌍의 기존 메타데이터를 덮어씁니다. 가져오기 파일에 쌍 메타데이터가 포함되지 않은 경우 `lastUpdateTime` 메타데이터는 현재 시간을 사용하여 업데이트됩니다.

각 디바이스에 대한 가져오기 직렬화 데이터에 선택적 **importMode** 속성을 사용하여 가져오기 프로세스를 디바이스별로 제어합니다. **importMode** 속성에 다음과 같은 옵션이 있습니다.

| importMode | 설명 |
| --- | --- |
| **createOrUpdate** |지정 된 장치가 없으면 **ID**, 새로 등록 됩니다. <br/>디바이스가 이미 존재하는 경우 **ETag** 값과 관계 없이 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br> 사용자는 디바이스 데이터와 함께 쌍으로 된 데이터를 선택적으로 지정할 수 있습니다. 쌍의 ETag을 지정하는 경우 디바이스의 ETag에서 독립적으로 처리됩니다. 기존 쌍의 ETag와 일치하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **create** |지정 된 장치가 없으면 **ID**, 새로 등록 됩니다. <br/>디바이스에 이미 존재하는 경우 오류가 로그 파일에 기록됩니다. <br> 사용자는 디바이스 데이터와 함께 쌍으로 된 데이터를 선택적으로 지정할 수 있습니다. 쌍의 ETag을 지정하는 경우 디바이스의 ETag에서 독립적으로 처리됩니다. 기존 쌍의 ETag와 일치하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **update** |장치가 이미 존재 하는 지정 된 경우 **ID**를 관계 없이 제공된 된 입력된 데이터를 사용 하 여 기존 정보를 덮어씁니다를 **ETag** 값입니다. <br/>디바이스가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **updateIfMatchETag** |장치가 이미 존재 하는 지정 된 경우 **ID**, 경우에 제공 된 입력된 데이터가 기존 정보를 덮어씁니다를 **ETag** 일치 합니다. <br/>디바이스가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. <br/>**ETag** 가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. |
| **createOrUpdateIfMatchETag** |지정 된 장치가 없으면 **ID**, 새로 등록 됩니다. <br/>디바이스가 이미 존재하는 경우 **ETag** 가 일치하는 경우에만 제공된 입력 데이터가 기존 정보를 덮어씁니다. <br/>**ETag** 가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. <br> 사용자는 디바이스 데이터와 함께 쌍으로 된 데이터를 선택적으로 지정할 수 있습니다. 쌍의 ETag을 지정하는 경우 디바이스의 ETag에서 독립적으로 처리됩니다. 기존 쌍의 ETag와 일치하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **delete** |장치가 이미 존재 하는 지정 된 경우 **ID**를 관계 없이 삭제 됩니다 합니다 **ETag** 값입니다. <br/>디바이스가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. |
| **deleteIfMatchETag** |장치가 이미 존재 하는 지정 된 경우 **ID**, 경우에 삭제 되는 **ETag** 일치 합니다. 디바이스가 존재하지 않는 경우 오류가 로그 파일에 기록됩니다. <br/>ETag가 일치하지 않는 경우 불일치 오류가 로그 파일에 기록됩니다. |

> [!NOTE]
> 직렬화 데이터가 디바이스에 대한 **importMode** 플래그를 명시적으로 정의하는 경우 가져오기 작업 중에 기본적으로 **createOrUpdate**를 가정합니다.

## <a name="import-devices-example--bulk-device-provisioning"></a>디바이스 가져오기 예제 – 대량 디바이스 프로비저닝

다음 C# 코드 예제에서는 다음과 같은 여러 디바이스 ID를 생성하는 방법을 보여 줍니다.

* 인증 키를 포함합니다.
* 블록 Blob에 해당 디바이스 정보를 씁니다.
* 디바이스를 ID 레지스트리로 가져옵니다.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>디바이스 가져오기 예제 – 대량 삭제

다음 코드 샘플은 이전 코드 샘플을 사용하여 추가한 디바이스를 삭제하는 방법을 보여 줍니다.

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>컨테이너 SAS URI 가져오기

다음 코드 샘플은 blob 컨테이너에 대한 읽기, 쓰기 및 삭제 사용 권한을 가진 [SAS URI](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)를 생성하는 방법을 보여 줍니다.

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT Hub의 ID 레지스트리에 대한 대량 작업을 수행하는 방법을 살펴보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT Hub 메트릭](iot-hub-metrics.md)
* [작업 모니터링](iot-hub-operations-monitoring.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)

IoT Hub Device Provisioning Service를 사용하여 무인 Just-In-Time 프로비저닝을 수행하는 방법을 알아보려면 다음을 참조하세요. 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)

---
title: 사용자 지정 모델 관리
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 내에서 모델을 만들고, 편집 하 고, 삭제 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3a2b3bfa8553e7c350c08fa7e1a7376ca08d9644
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079779"
---
# <a name="manage-azure-digital-twins-models"></a>Azure Digital Twins 모델 관리

[**DigitalTwinsModels api**](how-to-use-apis-sdks.md), [.net (c #) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)또는 [azure digital twins CLI](how-to-use-cli.md)를 사용 하 여 azure digital twins 인스턴스에서 알고 있는 [모델](concepts-models.md) 을 관리할 수 있습니다. 

관리 작업에는 모델 업로드, 유효성 검사, 검색 및 삭제가 포함 됩니다. 

## <a name="create-models"></a>모델 만들기

Azure 디지털 쌍에 대 한 모델은 DTDL로 작성 되 고 *.* i n i 파일로 저장 됩니다. DTDL 문서 작성을 용이 하 게 하는 구문 유효성 검사 및 기타 기능을 제공 하는 [Visual Studio Code](https://code.visualstudio.com/)에 사용할 수 있는 [dtdl 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) 도 있습니다.

병원에서 디지털를 디지털로 나타내는 예를 생각해 보세요. 각 방에는 washing을 모니터링 하는 스마트 soap 디스펜서와 실내를 통한 트래픽을 모니터링 하는 센서가 포함 되어 있습니다.

솔루션에 대 한 첫 번째 단계는 병원의 측면을 나타내는 모델을 만드는 것입니다. 이 시나리오의 환자 공간은 다음과 같이 설명할 수 있습니다.

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> 모델을 정의 하 고 저장 하는 json 파일에 대 한 샘플 본문으로, 클라이언트 프로젝트의 일부로 업로드 됩니다. 반면에 REST API 호출은 위에 있는 것과 같은 모델 정의의 배열을 사용 `IEnumerable<string>` 합니다 .이는 .NET SDK의에 매핑됩니다. 따라서 REST API에서이 모델을 직접 사용 하려면 대괄호로 묶습니다.

이 모델은 환자 객실의 이름과 고유 ID를 정의 하 고 방문자 수 및 핸드 씻어 상태를 나타내는 속성을 정의 합니다. 이러한 카운터는 동작 센서 및 smart soap dispensers에서 업데이트 되며 함께 사용 하 여 *수동 씻어 백분율* 속성을 계산 합니다. 또한이 모델은이 *대화방* 모델을 기반으로 하는 [디지털](concepts-twins-graph.md) 쌍을 실제 장치에 연결 하는 데 사용 되는 관계 *hasdevices*를 정의 합니다.

이 방법을 수행 하면 병원의 wards, 영역 또는 병원 자체에 대 한 모델을 정의할 수 있습니다.

### <a name="validate-syntax"></a>구문 유효성 검사

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>Api를 사용 하 여 모델 관리

다음 섹션에서는 [Azure Digital Twins api 및 sdk](how-to-use-apis-sdks.md)를 사용 하 여 다양 한 모델 관리 작업을 완료 하는 방법을 보여 줍니다.

> [!NOTE]
> 아래 예제에는 간결 하 게 하는 오류 처리가 포함 되지 않습니다. 그러나 프로젝트 내에서 try/catch 블록에 서비스 호출을 래핑하는 것이 좋습니다.

> [!TIP] 
> 모든 SDK 메서드는 동기 및 비동기 버전으로 제공 됩니다. 페이징 호출의 경우 비동기 메서드는 `AsyncPageable<T>` 동기 버전이 반환 되는 동안를 반환 합니다 `Pageable<T>` .

### <a name="upload-models"></a>모델 업로드

모델을 만든 후에는 Azure Digital Twins 인스턴스에 업로드할 수 있습니다.

> [!TIP]
> Azure Digital Twins 인스턴스에 업로드 하기 전에 오프 라인으로 모델의 유효성을 검사 하는 것이 좋습니다. 방법: 모델을 서비스에 업로드 하기 전에 모델을 검사 하기 위해 [*모델을 구문 분석 및 유효성 검사*](how-to-parse-models.md) 에 설명 된 [dtdl 클라이언트 쪽 파서 라이브러리](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) 및 [dtdl 유효성 검사기 샘플](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) 을 사용할 수 있습니다.

모델을 업로드할 준비가 되 면 다음 코드 조각을 사용할 수 있습니다.

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

`CreateModels`메서드가 하나의 단일 트랜잭션에서 여러 파일을 허용 하는지 확인 합니다. 다음은이를 보여 주는 샘플입니다.

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

모델 파일에는 두 개 이상의 모델이 포함 될 수 있습니다. 이 경우에는 모델을 JSON 배열에 배치 해야 합니다. 예를 들면 다음과 같습니다.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
업로드할 때 서비스에서 모델 파일의 유효성을 검사 합니다.

### <a name="retrieve-models"></a>모델 검색

Azure Digital Twins 인스턴스에 저장 된 모델을 나열 하 고 검색할 수 있습니다. 

이에 대 한 옵션은 다음과 같습니다.
* 모든 모델 검색
* 단일 모델 검색
* 종속성이 있는 단일 모델 검색
* 모델에 대 한 메타 데이터 검색

다음은 몇 가지 예제 호출입니다.

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

API를 호출 하 여 모든 반환 개체를 검색 합니다 `ModelData` . `ModelData` 이름, DTMI 및 모델 생성 날짜와 같이 Azure Digital Twins 인스턴스에 저장 된 모델에 대 한 메타 데이터를 포함 합니다. `ModelData`또한 개체는 모델 자체를 선택적으로 포함 합니다. 매개 변수에 따라 검색 호출을 사용 하 여 메타 데이터만 검색할 수 있습니다 (예를 들어 사용 가능한 도구의 UI 목록을 표시 하려는 시나리오 또는 전체 모델).

이 호출은 요청 된 모델 뿐만 아니라 `RetrieveModelWithDependencies` 요청 된 모델이 종속 된 모든 모델을 반환 합니다.

모델은 업로드 된 문서 양식으로 정확히 반환 될 필요는 없습니다. Azure Digital Twins는 반환 폼이 의미상 동일 함을 보장 합니다. 

### <a name="update-models"></a>모델 업데이트

모델을 인스턴스에 업로드 하 고 나면 모델 인터페이스 전체를 변경할 수 없습니다. 이는 기존의 모델 "편집"이 없음을 의미 합니다.

대신 Azure Digital Twins에서 모델을 변경 하려는 경우이 작업을 수행 하는 방법은 동일한 모델의 **최신 버전** 을 업로드 하는 것입니다. 미리 보기 중에는 모델 버전을 이동 하는 경우에만 필드를 제거 하 고 새 필드를 추가 하는 것은 허용 되지 않습니다. 새 필드를 추가 하려면 [새 모델을 만들어야](#create-models)합니다.

기존 모델의 새 버전을 만들려면 원래 모델의 DTDL로 시작 합니다. 변경할 필드를 업데이트 합니다.

그런 다음 모델의 필드를 업데이트 하 여이를 최신 버전의 모델로 표시 `id` 합니다. 모델 ID의 마지막 섹션인은 `;` 모델 번호를 나타냅니다. 이제이 모델의 더 업데이트 된 버전 임을 나타내려면 값의 끝에 있는 숫자를 `id` 현재 버전 번호 보다 큰 숫자로 늘립니다.

예를 들어 이전 모델 ID가 다음과 같이 나타납니다.

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

이 모델의 버전 2는 다음과 같습니다.

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

그런 다음 새 버전의 모델을 인스턴스에 업로드 합니다. 이전 버전의 대신이 모델을 사용 하 여 만든 새 쌍는 업데이트 된 버전을 사용 합니다.

### <a name="remove-models"></a>모델 제거

모델은 다음 두 가지 방법 중 하나로 서비스에서 제거 될 수도 있습니다.
* **서비스** 해제: 모델을 서비스 해제 한 후에는 더 이상 새 디지털 쌍을 만드는 데 사용할 수 없습니다. 이미이 모델을 사용 하는 기존 digital 쌍는 영향을 받지 않으므로 속성 변경, 관계 추가 또는 삭제와 같은 항목을 사용 하 여 업데이트할 수 있습니다.
* **삭제** : 솔루션에서 모델을 완전히 제거 합니다. 이 모델을 사용 하는 모든 쌍은 더 이상 유효한 모델과 연결 되지 않으므로 모델이 없는 것 처럼 처리 됩니다. 여전히 이러한 쌍을 읽을 수 있지만 다른 모델에 다시 할당할 때까지 업데이트를 수행할 수 없습니다.

이러한 기능은 개별 기능이 며 서로에 게 영향을 주지 않지만 모델을 점진적으로 제거 하는 데 함께 사용할 수 있습니다. 

#### <a name="decommissioning"></a>서비스 해제

모델을 서비스 해제 하는 코드는 다음과 같습니다.

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

모델의 서비스 해제 상태는 `ModelData` 모델 검색 api에서 반환 하는 레코드에 포함 됩니다.

#### <a name="deletion"></a>삭제

인스턴스의 모든 모델을 한 번에 삭제 하거나 개별적으로 수행할 수 있습니다.

모든 모델을 삭제 하는 방법에 대 한 예제는 [*자습서: 샘플 클라이언트 앱을 사용 하 여 기본 사항 탐색*](tutorial-command-line-app.md)에서 사용한 샘플 앱을 다운로드 합니다. *CommandLoop.cs* 파일은 함수에서이를 수행 `CommandDeleteAllModels` 합니다.

이 섹션의 나머지 부분에서는 모델 삭제를 자세히 설명 하 고 개별 모델에 대해이 작업을 수행 하는 방법을 보여 줍니다.

##### <a name="before-deletion-deletion-requirements"></a>삭제 전: 삭제 요구 사항

일반적으로 모델은 언제 든 지 삭제할 수 있습니다.

예외는 다른 모델이 `extends` 관계가 나 구성 요소로 종속 된 모델입니다. 예를 들어 *ConferenceRoom* 모델이 *방* 모델을 확장 하 고 *acunit* 모델을 구성 요소로 포함 하는 경우 *ConferenceRoom* 가 해당 참조를 제거할 때까지 *room* 또는 *acunit* 을 삭제할 수 없습니다. 

종속 모델을 업데이트 하 여 종속성을 제거 하거나 종속 모델을 완전히 삭제 하 여이 작업을 수행할 수 있습니다.

##### <a name="during-deletion-deletion-process"></a>삭제 중: 삭제 프로세스

모델이 즉시 삭제 하는 요구 사항을 충족 하는 경우에도 몇 가지 단계를 수행 하 여 남아 있는 쌍의 의도 하지 않은 결과를 방지 하는 것이 좋습니다. 프로세스를 관리 하는 데 도움이 되는 몇 가지 단계는 다음과 같습니다.
1. 먼저 모델을 서비스 해제 합니다.
2. 서비스에서 서비스를 해제 하기 전에 전송 된 마지막 분의 쌍 만들기 요청을 처리 했는지 확인 하려면 몇 분 정도 기다립니다.
3. 모델에의 한 wins 쿼리를 사용 하 여 현재 서비스 해제 된 모델을 사용 하는 모든 쌍을 확인 합니다.
4. 더 이상 필요 하지 않은 경우 쌍를 삭제 하거나 필요한 경우 새 모델에 패치 합니다. 모델을 삭제 한 후에는 모델 없이 쌍이 되도록 선택할 수도 있습니다. 이 상태의 의미에 대해서는 다음 섹션을 참조 하십시오.
5. 몇 분 후에 변경 내용이 적용 되었는지 확인 합니다.
6. 모델 삭제 

모델을 삭제 하려면 다음 호출을 사용 합니다.
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>삭제 후: 모델 없이 Twins

모델을 삭제 한 후에는 모델을 사용 하 던 모든 디지털 쌍이 이제 모델 없이 사용 되는 것으로 간주 됩니다. 이 상태의 모든 쌍 목록을 제공할 수 있는 쿼리가 없습니다. 그러나 삭제 된 모델에 의해 쌍이 영향을 받는 항목을 확인 하기 위해 여전히 해당 쌍을 쿼리할 *수* 있습니다.

모델을 포함 하지 않는 쌍로 수행할 수 있는 작업과 수행할 수 없는 작업에 대 한 개요는 다음과 같습니다.

수행할 **수 있는** 작업:
* 쌍 쿼리
* 속성 읽기
* 나가는 관계 읽기
* 들어오는 관계를 추가 및 삭제 합니다 .에서와 같이 다른 쌍이이 쌍 *에 대 한* 관계를 형성할 수 있습니다.
  - `target`관계 정의의는 삭제 된 모델의 DTMI를 계속 반영할 수 있습니다. 정의 된 대상이 없는 관계는 여기에서 작동할 수도 있습니다.
* 관계 삭제
* 쌍을 삭제 합니다.

수행할 **수 없는** 작업은 다음과 같습니다.
* 나가는 관계 편집 (에서로,이 쌍 *에서* 다른 쌍으로의 관계)
* 속성 편집

##### <a name="after-deletion-re-uploading-a-model"></a>삭제 후: 모델 다시 업로드

모델을 삭제 한 후 나중에 삭제 한 ID와 동일한 ID를 사용 하 여 새 모델을 업로드 하도록 결정할 수 있습니다. 이 경우 발생 하는 작업은 다음과 같습니다.
* 솔루션 상점의 관점에서이는 완전히 새로운 모델을 업로드 하는 것과 같습니다. 서비스는 이전에 업로드 된 것을 기억할 필요가 없습니다.   
* 삭제 된 모델을 참조 하는 그래프에 남아 있는 쌍이 있으면 더 이상 분리 되지 않습니다. 새 정의를 사용 하 여이 모델 ID를 다시 사용할 수 있습니다. 그러나 모델에 대 한 새 정의가 삭제 된 모델 정의와 다를 경우 이러한 쌍은 삭제 된 정의와 일치 하 고 새 속성과 관계가 없을 수 있습니다.

Azure Digital 쌍는이 상태를 방지 하지 않으므로 모델 정의 스위치를 통해 유효 하 게 유지 하기 위해 적절 한 wins를 적절 하 게 패치 해야 합니다.

## <a name="manage-models-with-cli"></a>CLI를 사용 하 여 모델 관리

Azure Digital Twins CLI를 사용 하 여 모델을 관리할 수도 있습니다. 명령은 [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

모델을 기반으로 디지털 쌍을 만들고 관리 하는 방법을 참조 하세요.
* [*방법: digital 쌍 관리*](how-to-manage-twin.md)
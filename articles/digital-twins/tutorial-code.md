---
title: 클라이언트 앱 코딩
titleSuffix: Azure Digital Twins
description: .NET(C#) SDK를 사용하여 클라이언트 앱의 최소 코드를 작성하는 자습서입니다.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 52a22dd215769208b60f180b576ae5763d67eade
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723472"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Azure Digital Twins API를 사용하여 코딩

Azure Digital Twins를 사용하는 개발자는 Azure Digital Twins 서비스 인스턴스와 상호 작용하기 위한 클라이언트 애플리케이션을 작성하는 것이 일반적입니다. 이 개발자 중심 자습서에서는 [.NET(C#)용 Azure IoT Digital Twin 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)를 사용하여 Azure Digital Twins 서비스에 대한 프로그래밍을 소개합니다. C# 콘솔 클라이언트 앱을 작성하는 과정을 처음부터 단계별로 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 설정 및 프로젝트 작업에 명령줄을 사용합니다. 따라서 원하는 코드 편집기를 사용하여 연습 과정을 진행할 수 있습니다.

시작하기 위해 필요한 사항:
* 코드 편집기
* 개발 머신에 설치된 **.NET Core 3.1**. [.NET Core 3.1 다운로드](https://dotnet.microsoft.com/download/dotnet-core/3.1)에서 여러 플랫폼을 위한 .NET Core SDK의 이 버전을 다운로드할 수 있습니다.

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>프로젝트 설정

Azure Digital Twins 인스턴스로 이동할 준비가 되면 클라이언트 앱 프로젝트 설정을 시작합니다. 

머신에서 명령 프롬프트 또는 다른 콘솔 창을 열고, 이 자습서를 진행하는 동안 작업을 저장할 빈 프로젝트 디렉터리를 만듭니다. 디렉터리 이름을 원하는 대로 지정합니다(예: *DigitalTwinsCodeTutorial*).

새 디렉터리로 이동합니다.

프로젝트 디렉터리에서 빈 .NET 콘솔 앱 프로젝트를 만듭니다. 명령 창에서 다음 명령을 실행하여 콘솔에 사용할 최소 C# 프로젝트를 만듭니다.

```cmd/sh
dotnet new console
```

그러면 대부분의 코드를 작성할 *Program.cs*라는 파일을 포함하여 디렉터리 내에 여러 파일이 만들어집니다.

다음으로, Azure Digital Twins를 사용하는 데 필요한 두 개의 종속성을 추가합니다.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.1.1
```

첫 번째 종속성은 [.NET용 Azure IoT Digital Twin 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)입니다. 두 번째 종속성은 Azure에 대한 인증에 유용한 도구를 제공합니다.

자습서 전체에서 명령 창을 계속 사용하므로 명령 창을 열어 두세요.

## <a name="get-started-with-project-code"></a>프로젝트 코드 시작

이 섹션에서는 Azure Digital Twins에서 작동하는 새로운 앱 프로젝트의 코드 작성을 시작합니다. 다음과 같은 작업을 살펴봅니다.
* 서비스에 대한 인증
* 모델 업로드
* 오류 catch
* 디지털 트윈 만들기
* 관계 만들기
* 디지털 트윈 쿼리

자습서의 끝 부분에 전체 코드를 보여주는 섹션도 있습니다. 이 섹션을 참조로 사용하여 진행하면서 프로그램을 확인할 수 있습니다.

시작하려면 코드 편집기에서 *Program.cs* 파일을 엽니다. 다음과 같은 최소 코드 템플릿이 표시됩니다.

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

먼저 코드 맨 위에 `using` 줄을 몇 개 추가하여 필요한 종속성을 가져옵니다.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

그런 다음에는 이 파일에 코드를 추가하여 기능을 작성합니다. 

### <a name="authenticate-against-the-service"></a>서비스에 대한 인증

이 앱이 수행할 첫 번째 작업은 Azure Digital Twins 서비스에 대해 인증하는 것입니다. 그런 다음, 서비스 클라이언트 클래스를 만들어 SDK 함수에 액세스할 수 있습니다.

인증하려면 다음과 같은 세 가지 정보가 필요합니다.
* 구독을 위한 *디렉터리(테넌트) ID*
* 이전에 Azure Digital Twins 인스턴스를 설정할 때 생성된 *애플리케이션(클라이언트) ID*
* Azure Digital Twins 인스턴스의 *hostName*

>[!TIP]
> *디렉터리(테넌트) ID*를 모르는 경우 [Azure Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 가져올 수 있습니다.
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

*Program.cs*에서 "Hello, World!" 출력 줄 아래의 `Main` 메서드에 다음 코드를 붙여넣습니다. `adtInstanceUrl`을 Azure Digital Twins 인스턴스 *호스트 이름*으로, `clientId`를 *애플리케이션 ID*로, `tenantId`를 *디렉터리 ID*로 값을 설정합니다.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

파일을 저장합니다. 

이 예제에서는 대화형 브라우저 자격 증명을 사용합니다.
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

이 유형의 자격 증명을 사용하면 브라우저 창이 열리고 Azure 자격 증명을 제공하라는 메시지가 표시됩니다. 

>[!NOTE]
> 다른 유형의 자격 증명에 대한 자세한 내용은 [Microsoft ID 플랫폼 인증 라이브러리](../active-directory/develop/reference-v2-libraries.md) 설명서를 참조하세요.

명령 창에서 다음 명령을 사용하여 코드를 실행합니다. 

```cmd/sh
dotnet run
```

그러면 처음 실행될 때 종속성이 복원된 다음, 프로그램이 실행됩니다. 
* 오류가 발생하지 않으면 *Service client created - ready to go*가 출력됩니다.
* 이 프로젝트에는 아직 오류 처리가 없으므로 문제가 생기면 코드에 의해 throw된 예외가 표시됩니다.

### <a name="upload-a-model"></a>모델 업로드

Azure Digital Twins에는 내장 도메인 어휘가 없습니다. Azure Digital Twins에서 나타낼 수 있는 환경의 요소 유형은 사용자가 **모델**을 사용하여 정의합니다. [모델](concepts-models.md)은 개체 지향 프로그래밍 언어의 클래스와 유사하며, 나중에 [디지털 트윈](concepts-twins-graph.md)이 따르고 인스턴스화할 수 있도록 사용자 정의 템플릿을 제공합니다. 이 템플릿은 **DTDL(Digital Twins Definition Language)** 이라는 JSON과 유사한 언어로 작성됩니다.

Azure Digital Twins 솔루션을 만드는 첫 번째 단계는 DTDL 파일에 하나 이상의 모델을 정의하는 것입니다.

프로젝트를 만든 디렉터리에서 *SampleModel.json*이라는 새 *.json* 파일을 만듭니다. 다음 파일 본문을 붙여넣습니다. 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> 이 자습서의 진행을 위해 Visual Studio를 사용하는 경우, 새로 만든 JSON 파일을 선택하고 속성 검사기에서 *출력 디렉터리로 복사* 속성을 *변경된 내용만 복사* 또는 *항상 복사*로 설정합니다. 이렇게 하면 자습서의 나머지 부분에서 **F5**를 사용하여 프로그램을 실행할 때 Visual Studio에서 기본 경로를 사용하여 JSON 파일을 찾을 수 있습니다.

> [!TIP] 
> DTDL이 유효한지 확인하기 위해 모델 문서를 검사하는 데 사용할 수 있는 언어 독립적 [DTDL 유효성 검사기 샘플](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)이 있습니다. 이 파일은 DTDL 파서 라이브러리를 기반으로 합니다. DTDL 파서 라이브러리에 대한 자세한 내용은 [*방법: 모델 구문 분석 및 유효성 검사*](how-to-parse-models.md)를 참조하세요.

다음으로 *Program.cs*에 코드를 추가하여 방금 만든 모델을 Azure Digital Twins 인스턴스에 업로드합니다.

먼저, 파일 맨 위에 몇 개의 `using`문을 추가합니다.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

다음으로 비동기 실행을 허용하도록 `Main` 메서드 시그니처를 변경하여 C# 서비스 SDK에서 비동기 메서드를 사용할 준비를 합니다. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> SDK도 모든 호출의 동기 버전을 제공하므로 `async`를 반드시 사용해야 하는 것은 아닙니다. 이 자습서에서는 `async`를 사용합니다.

다음은 Azure Digital Twins 서비스와 상호 작용하는 코드의 첫 번째 비트입니다. 이 코드는 디스크에서 만든 DTDL 파일을 로드한 다음, 이 파일을 Azure Digital Twins 서비스 인스턴스에 업로드합니다. 

앞에서 추가한 인증 코드 아래에 다음 코드를 붙여넣습니다.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

명령 창에서 다음 명령을 사용하여 프로그램을 실행합니다. 

```cmd/sh
dotnet run
```
"모델 업로드"는 출력에 인쇄되지만 모델이 성공적으로 업로드되었는지 여부를 나타내는 출력은 아직 없습니다.

모델이 실제로 성공적으로 업로드되었는지를 나타내는 print 문을 추가하려면 이전 섹션 바로 뒤에 다음 코드를 추가합니다.

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

이 새 코드를 테스트하기 위해 프로그램을 다시 실행하기 전에, 이 프로그램을 마지막으로 실행할 때 모델을 업로드했음을 기억하세요. Azure Digital Twins에서는 동일한 모델을 두 번 업로드할 수 없으므로 프로그램을 다시 실행할 때 예외가 표시됩니다.

이제 명령 창에서 다음 명령을 사용하여 프로그램을 다시 실행합니다.

```cmd/sh
dotnet run
```

프로그램에서 예외가 throw됩니다. 이미 업로드된 모델을 업로드하려고 하면 서비스에서 REST API를 통해 "잘못된 요청" 오류를 반환합니다. 따라서 Azure Digital Twins 클라이언트 SDK는 성공 이외의 모든 서비스 반환 코드에 대해 예외를 throw합니다. 

다음 섹션에서는 이러한 예외와 코드에서 이 예외를 처리하는 방법에 대해 설명합니다.

### <a name="catch-errors"></a>오류 catch

프로그램의 크래시를 방지하기 위해 모델 업로드 코드에 예외 코드를 추가할 수 있습니다. try/catch 처리기에서 기존 클라이언트 호출 `client.CreateModelsAsync`를 다음과 같이 래핑합니다.

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

명령 창에서 `dotnet run`을 사용하여 프로그램을 실행하면 오류 코드가 다시 표시됩니다. 출력은 다음과 같습니다.

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

이 자습서에서는 이 지점부터 try/catch 처리기에서 서비스 메서드에 대한 모든 호출을 래핑합니다.

### <a name="create-digital-twins"></a>디지털 트윈 만들기

Azure Digital Twins에 모델을 업로드했으므로 이제 이 모델 정의를 사용하여 **디지털 트윈**을 만들 수 있습니다. [디지털 트윈](concepts-twins-graph.md)은 모델의 인스턴스이며, 비즈니스 환경 내의 엔터티(예: 농장의 센서, 건물의 방, 자동차의 조명)를 나타냅니다. 이 섹션에서는 이전에 업로드한 모델에 따라 몇 개의 디지털 트윈을 만듭니다.

`System.Text.Json`에 기본 제공 .NET JSON 직렬 변환기가 필요하므로 맨 위에 새로운 `using` 문을 추가합니다.

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

그런 다음 `Main` 메서드의 끝에 다음 코드를 추가하여 이 모델을 기반으로 세 개의 디지털 트윈을 만들고 초기화합니다.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

명령 창에서 `dotnet run`을 사용하여 프로그램을 실행합니다. 그런 다음, 반복하여 프로그램을 다시 실행합니다. 

트윈이 두 번째로 만들어지는 경우, 첫 번째 실행 후에 트윈이 이미 존재해도 오류가 throw되지 않습니다. 모델 생성과 달리 트윈 생성은 REST 수준에서 *upsert* 의미 체계를 사용하는 *PUT* 호출입니다. 즉 트윈이 이미 존재하는데 트윈을 다시 만들려고 하면 트윈이 바로 대체됩니다. 오류가 필요하지 않습니다.

### <a name="create-relationships"></a>관계 만들기

다음으로, 앞에서 만든 트윈 간의 **관계**를 만들어 **트윈 그래프**로 연결할 수 있습니다. [트윈 그래프](concepts-twins-graph.md)는 전체 환경을 나타내는 데 사용됩니다.

관계를 만들려면 SDK에서 관계 기본 형식에 대한 `using` 문을 추가합니다. 이미 추가된 경우 건너뜁니다.
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

다음으로 `Program` 클래스의 `Main` 메서드 아래에 새로운 정적 메서드를 추가합니다.
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

그런 다음, `Main` 메서드의 끝에 다음 코드를 추가하여 `CreateRelationship` 코드를 호출합니다.
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

명령 창에서 `dotnet run`을 사용하여 프로그램을 실행합니다.

Azure Digital Twins에서는 동일한 ID를 가진 관계가 이미 있는 경우 이러한 관계를 만들 수 없으므로, 프로그램을 여러 번 실행하면 관계 만들기에 대한 예외가 표시됩니다. 이 코드는 예외를 catch하여 무시합니다. 

### <a name="list-relationships"></a>관계 목록

다음으로 추가할 코드를 사용하면 내가 만든 관계의 목록을 볼 수 있습니다.

`Program` 클래스에 다음 새 메서드를 추가합니다.

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

그런 다음, `Main` 메서드의 끝에 다음 코드를 추가하여 `ListRelationships` 코드를 호출합니다.

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

명령 창에서 `dotnet run`을 사용하여 프로그램을 실행합니다. 내가 만든 모든 관계의 목록이 표시됩니다.

### <a name="query-digital-twins"></a>디지털 트윈 쿼리

Azure Digital Twins의 주요 기능은 환경에 대한 질문에 답하기 위해 트윈 그래프를 쉽고 효율적으로 [쿼리](concepts-query-language.md)하는 기능입니다.

이 자습서에서 추가할 코드의 마지막 섹션은 Azure Digital Twins 인스턴스에 대해 쿼리를 실행합니다. 이 예제에 사용된 쿼리는 인스턴스의 모든 디지털 트윈을 반환합니다.

`Main` 메서드의 끝에 다음 코드를 추가합니다.

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

명령 창에서 `dotnet run`을 사용하여 프로그램을 실행합니다. 이 인스턴스의 모든 디지털 트윈이 출력에 표시됩니다.

## <a name="complete-code-example"></a>전체 코드 예제

자습서의 이 지점에는 Azure Digital Twins에 대한 기본 작업을 수행할 수 있는 전체 클라이언트 앱이 있습니다. 참조용으로 *Program.cs*의 프로그램 코드 전체가 아래에 나와 있습니다.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>리소스 정리
 
이 자습서에서 사용한 인스턴스를 다음 자습서인 [*자습서: 샘플 클라이언트 앱으로 기본 사항 살펴보기*](tutorial-command-line-app.md)에서 다시 사용할 수 있습니다. 다음 자습서를 계속 진행하려는 경우 여기에서 설정한 Azure Digital Twins 인스턴스를 유지할 수 있습니다.
 
이 자습서에서 만든 리소스가 더 이상 필요하지 않으면 다음 단계에 따라 삭제합니다.

[Azure Cloud Shell](https://shell.azure.com)을 사용하면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령으로 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 그러면 리소스 그룹과 Azure Digital Twins 인스턴스가 제거됩니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

Azure Cloud Shell을 열고 다음 명령을 실행하여 리소스 그룹과 그 안에 포함된 모든 항목을 삭제합니다.

```azurecli-interactive
az group delete --name <your-resource-group>
```

그리고 다음 명령을 사용하여 클라이언트 앱에 대해 만든 Azure Active Directory 앱 등록을 삭제합니다.

```azurecli
az ad app delete --id <your-application-ID>
```

마지막으로, 로컬 컴퓨터에서 만든 프로젝트 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 .NET 콘솔 클라이언트 애플리케이션을 처음부터 새로 만들었습니다. Azure Digital Twins 인스턴스에서 기본 작업을 수행하기 위해 이 클라이언트 앱의 코드를 작성했습니다.

다음 자습서를 계속 진행하여 이러한 샘플 클라이언트 앱으로 수행할 수 있는 작업을 살펴봅니다. 

> [!div class="nextstepaction"]
> [*자습서: 샘플 클라이언트 앱으로 기본 사항 살펴보기*](tutorial-command-line-app.md)

또한 방법 문서에서 추가 관리 작업을 학습하여 이 자습서에서 작성한 코드에 추가하거나, 개념 설명서를 검토하여 자습서에서 작업한 요소에 대한 자세한 정보를 알아볼 수 있습니다.
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)
* [*개념: 사용자 지정 모델*](concepts-models.md)

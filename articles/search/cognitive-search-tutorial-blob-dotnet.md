---
title: Azure Blob에서 AI를 사용하는 C# 자습서
titleSuffix: Azure Cognitive Search
description: C# 및 Azure Cognitive Search .NET SDK를 사용하여 Blob 스토리지의 콘텐츠에서 텍스트를 추출하고 자연어를 처리하는 예제를 단계별로 살펴봅니다.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 09273cf901830e850acca05c57c0b110ffd7e28a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002865"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 Azure Blob에서 AI 생성 검색 가능 콘텐츠

Azure Blob Storage에 비정형 텍스트 또는 이미지가 있는 경우 [AI 보강 파이프라인](cognitive-search-concept-intro.md)은 정보를 추출하여 전체 텍스트 검색 또는 지식 마이닝 시나리오에 유용한 새 콘텐츠를 만들 수 있습니다. 이 C# 자습서에서는 이미지에 OCR(광학 인식)을 적용하고 자연어 처리를 수행하여 쿼리, 패싯 및 필터에 활용할 수 있는 새로운 필드를 만듭니다.

이 자습서에서는 C# 및 [.NET SDK](/dotnet/api/overview/azure/search)를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Blob Storage에서 애플리케이션 파일과 이미지로 시작합니다.
> * OCR, 텍스트 추출, 언어 감지, 엔터티 및 핵심 문구 인식을 추가하는 파이프라인을 정의합니다.
> * 출력(원시 콘텐츠 및 파이프라인에서 생성된 이름-값 쌍)을 저장할 인덱스를 정의합니다.
> * 파이프라인을 실행하여 변환 및 분석을 시작하고 인덱스를 생성하고 로드합니다.
> * 전체 텍스트 검색과 풍부한 쿼리 구문을 사용하여 결과를 검색합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 엽니다.

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 체험 검색 서비스에서는 인덱스, 인덱서 및 데이터 원본이 각각 3개로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 시작하기 전에 새 리소스를 수용할 수 있는 공간이 서비스에 있는지 확인하세요.

## <a name="download-files"></a>파일 다운로드

1. 이 [OneDrive 폴더](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)를 열고, 왼쪽 위 모서리에서 **다운로드**를 클릭하여 파일을 컴퓨터에 복사합니다. 

1. 마우스 오른쪽 단추로 Zip 파일을 클릭하고, **압축 풀기**를 선택합니다. 다양한 형식의 14개 파일이 있습니다. 이 연습에서는 7개 파일을 사용합니다.

이 자습서의 소스 코드를 다운로드할 수도 있습니다. 소스 코드는 [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) 리포지토리의 tutorial-ai-enrichment 폴더에 있습니다.

## <a name="1---create-services"></a>1 - 서비스 만들기

이 자습서에서는 Azure Cognitive Search를 인덱싱 및 쿼리에 사용하고 AI 보강을 위해 백 엔드의 Cognitive Services를 사용하고 Azure Blob Storage를 사용하여 데이터를 제공합니다. 이 자습서는 Cognitive Services에 인덱서당 하루 20개의 트랜잭션을 무료로 할당하기 때문에, 검색 및 스토리지 서비스만 만들면 됩니다.

가능하면, 근접성과 관리 효율성을 위해 두 가지 모두를 동일한 지역과 리소스 그룹에 만듭니다. 실제로 Azure Storage 계정은 모든 지역에 있을 수 있습니다.

### <a name="start-with-azure-storage"></a>Azure Storage 시작

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, **+ 리소스 만들기**를 클릭합니다.

1. *스토리지 계정*을 검색하고, Microsoft의 스토리지 계정 제품을 선택합니다.

   ![스토리지 계정 만들기](media/cognitive-search-tutorial-blob/storage-account.png "Storage 계정 만들기")

1. [기본 사항] 탭의 필수 항목은 다음과 같습니다. 다른 모든 항목에는 기본값을 적용합니다.

   + **리소스 그룹**. 기존 서비스를 선택하거나 새 서비스를 만들지만 동일한 그룹을 모든 서비스에 사용하여 전체적으로 관리할 수 있습니다.

   + **스토리지 계정 이름**. 동일한 유형의 여러 리소스가 있을 수 있다고 생각되면 유형 및 지역별로 구분할 수 있는 이름(예: *blobstoragewestus*)을 사용합니다. 

   + **위치** - 가능하면 Azure Cognitive Search 및 Cognitive Services에 사용되는 것과 동일한 위치를 선택합니다. 단일 위치에는 대역폭 요금이 부과되지 않습니다.

   + **계정 종류**. 기본값인 *StorageV2(범용 v2)* 를 선택합니다.

1. **검토 + 만들기**를 클릭하여 서비스를 만듭니다.

1. 서비스가 만들어지면 **리소스로 이동**을 클릭하여 [개요] 페이지를 엽니다.

1. **Blob** 서비스를 클릭합니다.

1. **+ 컨테이너**를 클릭하여 컨테이너를 만들고, 이름을 *cog-search-demo*로 지정합니다.

1. *cog-search-demo*를 선택한 다음, **업로드**를 클릭하여 다운로드 파일을 저장한 폴더를 엽니다. 14개 파일을 모두 선택하고 **확인**을 클릭하여 업로드합니다.

   ![샘플 파일 업로드](media/cognitive-search-quickstart-blob/sample-data.png "샘플 파일 업로드")

1. Azure Storage를 나가기 전에 Azure Cognitive Search에서 연결을 만들 수 있도록 연결 문자열을 가져옵니다. 

   1. 스토리지 계정의 [개요] 페이지로 돌아갑니다(*blobstoragewestus*를 예로 사용했음). 
   
   1. 왼쪽 탐색 창에서 **액세스 키**를 선택하고, 연결 문자열 중 하나를 복사합니다. 

   연결 문자열은 다음 예제와 비슷한 URL입니다.

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. 연결 문자열을 메모장에 저장합니다. 이는 나중에 데이터 원본 연결을 설정할 때 필요합니다.

### <a name="cognitive-services"></a>Cognitive Services

AI 보강은 자연어와 이미지 처리를 위한 Text Analytics 및 Computer Vision을 포함한 Cognitive Services를 통해 지원됩니다. 실제 프로토타입 또는 프로젝트를 완료하는 것이 목표라면 이 시점에서 Cognitive Services를 인덱싱 작업에 연결할 수 있도록 Azure Cognitive Search와 동일한 지역에 프로비저닝합니다.

그러나 Azure Cognitive Search는 백그라운드에서 Cognitive Services에 연결하여 인덱서 실행당 20개의 체험 트랜잭션을 제공할 수 있으므로 이 연습에서는 리소스 프로비저닝을 건너뛸 수 있습니다. 이 자습서에서는 14개의 트랜잭션을 사용하므로 체험 할당이 충분합니다. 대규모 프로젝트의 경우 종량제 S0 계층에서 Cognitive Services를 프로비저닝할 계획입니다. 자세한 내용은 [Cognitive Services 연결](cognitive-search-attach-cognitive-services.md)을 참조하세요.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

세 번째 구성 요소는 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search입니다. 이 연습은 체험 계층을 사용하여 완료할 수 있습니다. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL을 가져옵니다.

Azure Cognitive Search 서비스와 상호 작용하려면 서비스 URL과 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   쿼리 키도 가져옵니다. 쿼리 요청은 읽기 전용 액세스로 발급하는 것이 좋습니다.

   ![서비스 이름과 관리자 및 쿼리 키 확인](media/search-get-started-nodejs/service-name-and-keys.png)

유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="2---set-up-your-environment"></a>2 - 환경 설정

먼저 Visual Studio를 열고 .NET Core에서 실행할 수 있는 새 콘솔 앱 프로젝트를 만듭니다.

### <a name="install-nuget-packages"></a>NuGet 패키지 설치

[Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)는 HTTP 및 JSON의 세부 정보를 처리하지 않고도 인덱스, 데이터 원본, 인덱서 및 기술 세트를 관리하고, 문서를 업로드 및 관리하고, 쿼리를 실행할 수 있게 하는 몇 가지 클라이언트 라이브러리로 구성됩니다. 이러한 클라이언트 라이브러리는 모두 NuGet 패키지로 배포됩니다.

이 프로젝트의 경우 `Microsoft.Azure.Search` NuGet 패키지 버전 9 이상을 설치합니다.

1. 브라우저에서 [Microsoft.Azure.Search NuGet 패키지 페이지](https://www.nuget.org/packages/Microsoft.Azure.Search)로 이동합니다.

1. 최신 버전(9 이상)을 선택합니다.

1. 패키지 관리자 명령을 복사합니다.

1. 패키지 관리자 콘솔을 엽니다. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다. 

1. 이전 단계에서 복사한 명령을 붙여넣고 실행합니다.

다음으로, 최신 `Microsoft.Extensions.Configuration.Json` NuGet 패키지를 설치합니다.

1. **도구** > **NuGet 패키지 관리자** > **솔루션용 NuGet 패키지 관리...** 를 선택합니다. 

1. **찾아보기**를 클릭하고 `Microsoft.Extensions.Configuration.Json` NuGet 패키지를 검색합니다. 

1. 패키지를 선택하고, 프로젝트를 선택하고, 안정적인 최신 버전인지 확인한 다음, **설치**를 선택합니다.

### <a name="add-service-connection-information"></a>서비스 연결 정보 추가

1. 솔루션 탐색기에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **추가** > **새 항목...** 을 차례로 선택합니다. 

1. 파일 이름을 `appsettings.json`으로 지정하고 **추가**를 선택합니다. 

1. 이 파일을 출력 디렉터리에 포함합니다.
    1. `appsettings.json`을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 
    1. **출력 디렉터리로 복사**의 값을 **변경된 내용만 복사**로 변경합니다.

1. 아래 JSON을 새 JSON 파일에 복사합니다.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
검색 서비스 및 Blob 스토리지 계정 정보를 추가합니다. 이 정보는 이전 섹션에 표시된 서비스 프로비전 단계에서 가져올 수 있습니다.

**SearchServiceName**의 경우 전체 URL이 아닌 짧은 서비스 이름을 입력합니다.

### <a name="add-namespaces"></a>네임스페이스 추가

`Program.cs`에서 다음 네임스페이스를 추가합니다.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>클라이언트 만들기

`Main` 아래에 `SearchServiceClient` 클래스의 인스턴스를 만듭니다.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`는 애플리케이션의 구성 파일(appsettings.json)에 저장된 값을 사용하여 `SearchServiceClient`를 새로 만듭니다.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> `SearchServiceClient` 클래스는 검색 서비스에 대한 연결을 관리합니다. 너무 많은 연결이 열리는 것을 방지하기 위해 되도록 애플리케이션에서 단일 `SearchServiceClient` 인스턴스를 공유합니다. 해당 메서드는 스레드로부터 안전하므로 이러한 공유를 사용할 수 있습니다.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>오류 발생 시 프로그램을 종료하는 함수 추가

이 자습서는 인덱싱 파이프라인의 각 단계를 이해하는 데 도움을 주기 위해 작성되었습니다. 프로그램에서 데이터 원본, 기술 세트, 인덱스 또는 인덱서를 만들 수 없도록 하는 중요한 문제가 있는 경우 프로그램에서 오류 메시지를 출력하고 종료하여 문제를 이해하고 해결할 수 있도록 합니다.

프로그램이 종료되어야 하는 시나리오를 처리하려면 `Main`에 `ExitProgram`을 추가합니다.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - 파이프라인 만들기

Azure Cognitive Search에서 AI 처리는 인덱싱(또는 데이터 수집) 중에 발생합니다. 이 연습 부분에서는 데이터 원본, 인덱스 정의, 기술 세트, 인덱서의 네 가지 개체를 만듭니다. 

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

`SearchServiceClient`에는 `DataSources` 속성이 있습니다. 이 속성은 Azure Cognitive Search 데이터 원본을 생성, 나열, 업데이트 또는 삭제하는 데 필요한 모든 메서드를 제공합니다.

`serviceClient.DataSources.CreateOrUpdate(dataSource)`를 호출하여 새 `DataSource` 인스턴스를 만듭니다. `DataSource.AzureBlobStorage`에서는 데이터 원본 이름, 연결 문자열 및 Blob 컨테이너 이름을 지정해야 합니다.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

요청이 성공하면 메서드에서 만들어진 데이터 원본을 반환합니다. 잘못된 매개 변수와 같이 요청에 문제가 있는 경우 메서드에서 예외를 throw합니다.

이제 `Main`에 방금 추가한 `CreateOrUpdateDataSource` 함수를 호출하는 줄을 추가합니다.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

솔루션을 빌드하고 실행합니다. 이번이 첫 번째 요청이므로 Azure Portal에서 데이터 원본이 Azure Cognitive Search에 생성되었는지 확인합니다. 검색 서비스 대시보드 페이지에서 데이터 원본 타일에 새 항목이 있는지 확인합니다. 포털 페이지가 새로 고침될 때까지 몇 분 정도 기다려야 할 수도 있습니다.

  ![포털의 데이터 원본 타일](./media/cognitive-search-tutorial-blob/data-source-tile.png "포털의 데이터 원본 타일")

### <a name="step-2-create-a-skillset"></a>2단계: 기술 집합 만들기

이 섹션에서는 데이터에 적용하려는 일단의 보강 단계를 정의합니다. 각 보강 단계는 *기술*이라고 하며, 보강 단계 집합은 *기술 집합*이라고 합니다. 이 자습서에서는 다음과 같은 [기본 제공 인식 기술](cognitive-search-predefined-skills.md)을 기술 세트에 사용합니다.

+ [광학 문자 인식](cognitive-search-skill-ocr.md): 이미지 파일의 인쇄 및 필기 텍스트를 인식합니다.

+ [텍스트 병합기](cognitive-search-skill-textmerger.md): 필드 컬렉션의 텍스트를 단일 필드로 통합합니다.

+ [언어 감지](cognitive-search-skill-language-detection.md): 콘텐츠의 언어를 식별합니다.

+ [텍스트 나누기](cognitive-search-skill-textsplit.md): 핵심 구 추출 기술과 엔터티 인식 기술을 호출하기 전에 큰 콘텐츠를 작은 청크로 나눕니다. 핵심 구 추출 및 엔터티 인식은 50,000자 이하의 입력을 허용합니다. 일부 샘플 파일은 이 제한에 맞게 분할해야 합니다.

+ [엔터티 인식](cognitive-search-skill-entity-recognition.md): Blob 컨테이너의 콘텐츠에서 조직 이름을 추출합니다.

+ [핵심 구 추출](cognitive-search-skill-keyphrases.md): 상위 핵심 구를 추출합니다.

초기 처리 중에 Azure Cognitive Search는 각 문서를 분해하여 다른 파일 형식의 콘텐츠를 읽습니다. 원본 파일에서 발생하는 텍스트는 각 문서에 대해 생성되는 ```content``` 필드에 배치됩니다. 따라서 이 텍스트를 사용하려면 입력을 ```"/document/content"```로 설정합니다. 

출력을 인덱스에 매핑할 수도 있고, 다운스트림 기술의 입력으로 사용할 수도 있고, 언어 코드처럼 둘 다 할 수도 있습니다. 인덱스에서 언어 코드는 필터링에 유용합니다. 입력으로써의 언어 코드는 단어 분리에 대한 언어적 규칙을 알려주기 위해 텍스트 분석 기술에 사용됩니다.

기술 집합 기본에 대한 자세한 내용은 [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)을 참조하세요.

### <a name="ocr-skill"></a>OCR 기술

**OCR** 기술은 이미지에서 텍스트를 추출합니다. 이 기술은 normalized_images 필드가 있다고 가정합니다. 이 필드를 생성하기 위해 자습서의 뒷부분에서 인덱서 정의의 ```"imageAction"``` 구성을 ```"generateNormalizedImages"```로 설정합니다.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>병합 기술

이 섹션에서는 문서 콘텐츠 필드를 OCR 기술로 생성된 텍스트와 병합하는 **병합** 기술을 만듭니다.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>언어 감지 기술

**언어 감지** 기술은 입력 텍스트의 언어를 감지하고 요청에 제출된 모든 문서에 대한 단일 언어 코드를 보고합니다. **언어 감지** 기술의 출력을 **텍스트 나누기** 기술에 대한 입력의 일부로 사용합니다.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>텍스트 나누기 기술

아래 **나누기** 기술은 텍스트를 페이지별로 나누고 `String.Length`에서 측정할 때 페이지 길이를 4,000자로 제한합니다. 알고리즘은 텍스트를 최대 `maximumPageLength` 크기의 청크로 나누려고 시도합니다. 이 경우 알고리즘은 문장 경계에서 문장을 나누는 데 최선을 다할 것이므로 청크의 크기는 `maximumPageLength`보다 약간 작을 수 있습니다.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>엔터티 인식 기술

다음 `EntityRecognitionSkill` 인스턴스는 `organization` 범주 유형을 인식하도록 설정되어 있습니다. **엔터티 인식** 기술은 `person` 및 `location` 범주 유형도 인식할 수 있습니다.

"context" 필드는 별표를 포함하여 ```"/document/pages/*"```로 설정되어 있으며, 이는 ```"/document/pages"``` 아래의 각 페이지에 대해 보강 단계가 호출됨을 의미합니다.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>핵심 구 추출 기술

방금 만든 `EntityRecognitionSkill` 인스턴스와 마찬가지로 문서의 각 페이지에 대해 **핵심 구 추출** 기술이 호출됩니다.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>기술 집합 빌드 및 만들기

만든 기술을 사용하여 `Skillset`를 빌드합니다.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

`Main`에 다음 줄을 추가합니다.

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>3단계: 인덱스 만들기

이 섹션에서는 검색 가능한 인덱스에 포함할 필드 및 각 필드의 검색 특성을 지정하여 인덱스 스키마를 정의합니다. 필드에는 형식이 있으며 필드가 사용되는 방식(검색 가능, 정렬 가능 등)을 결정하는 특성을 가질 수 있습니다. 인덱스의 필드 이름은 원본의 필드 이름을 동일하게 일치시키는 데 필요하지 않습니다. 이후 단계에서 인덱서의 필드 매핑을 원본-대상 연결 필드에 추가할 것입니다. 이 단계에서는 검색 애플리케이션과 관련된 필드 명명 규칙을 사용하여 인덱스를 정의합니다.

이 연습에서는 다음 필드와 필드 형식을 사용합니다.

| 필드 이름 | 필드 형식 |
| --- | --- |
| id | Edm.String |
| content | Edm.String |
| languageCode | Edm.String |
| keyPhrases | List<Edm.String> |
| organizations | List<Edm.String> |

#### <a name="create-demoindex-class"></a>DemoIndex 클래스 만들기

이 인덱스에 대한 필드는 모델 클래스를 사용하여 정의됩니다. 모델 클래스의 각 속성에는 해당 인덱스 필드의 검색 관련 동작을 결정하는 특성이 있습니다. 

모델 클래스를 새 C# 파일에 추가합니다. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **추가** > **새 항목...** 을 차례로 선택하고, "클래스"를 선택하고, 파일 이름을 `DemoIndex.cs`로 지정한 다음, **추가**를 선택합니다.

`Microsoft.Azure.Search` 및 `Microsoft.Azure.Search.Models` 네임스페이스의 형식을 사용하도록 지정해야 합니다.

아래의 모델 클래스 정의를 `DemoIndex.cs`에 추가하고, 인덱스를 만들 동일한 네임스페이스에 이를 포함시킵니다.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

이제 모델 클래스를 `Program.cs`에 다시 정의했으므로 인덱스 정의를 매우 쉽게 만들 수 있습니다. 이 인덱스의 이름은 `demoindex`입니다. 동일한 이름의 인덱스가 이미 있으면 삭제됩니다.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

테스트 중에 인덱스를 두 번 이상 만들려고 시도할 수 있습니다. 이로 인해 인덱스를 만들려고 하기 전에 해당 인덱스가 이미 있는지 확인합니다.

`Main`에 다음 줄을 추가합니다.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

다음 using 문을 추가하여 명확하지 않은 참조를 확인합니다.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

인덱스 정의에 대한 자세한 내용은 [인덱스 만들기(Azure Cognitive Search REST API)](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="step-4-create-and-run-an-indexer"></a>4단계: 인덱서 만들기 및 실행

지금까지 데이터 원본, 기술 집합 및 인덱스를 만들었습니다. 이러한 세 가지 구성 요소는 각 조각을 서로 연결하여 단일 다단계 작업으로 만드는 [인덱서](search-indexer-overview.md)의 일부가 됩니다. 각 구성 요소를 인덱서에 서로 연결하려면 필드 매핑을 정의해야 합니다.

+ fieldMappings은 기술 세트보다 먼저 처리되며, 데이터 원본의 원본 필드를 인덱스의 대상 필드로 매핑합니다. 필드 이름과 유형이 양쪽 끝에서 동일하면 매핑이 필요 없습니다.

+ outputFieldMappings는 기술 세트 후에 처리되며, 문서 크래킹 또는 보강을 통해 만들기 전에는 존재하지 않는 sourceFieldNames를 참조합니다. targetFieldName은 인덱스의 필드입니다.

입력을 출력에 후크할 뿐만 아니라 필드 매핑을 사용하여 데이터 구조를 평면화할 수도 있습니다. 자세한 내용은 [검색 가능한 인덱스에 보강된 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)을 참조하세요.

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
`Main`에 다음 줄을 추가합니다.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

인덱서 만드는 데 약간의 시간이 걸릴 것입니다. 데이터 집합이 작아도 분석 기술은 계산을 많이 수행합니다. 이미지 분석 같은 일부 기술은 오래 실행됩니다.

> [!TIP]
> 인덱서를 만들면 파이프라인이 호출됩니다. 데이터 도달, 입력 및 출력 매핑 또는 작업 순서에 문제가 있으면 이 단계에 나타납니다.

### <a name="explore-creating-the-indexer"></a>인덱서 만들기 검색

코드에서 ```"maxFailedItems"```를 -1로 설정하며, 이는 데이터를 가져오는 동안 오류를 무시하도록 인덱싱 엔진에 지시합니다. 데모 데이터 원본에는 문서가 몇 개 없기 때문에 이렇게 하면 도움이 됩니다. 데이터 원본의 크기가 큰 경우에는 0보다 큰 값으로 설정해야 합니다.

또한 ```"dataToExtract"```는 ```"contentAndMetadata"```으로 설정되어 있습니다. 이 명령문은 다른 파일 형식의 콘텐츠 및 각 파일과 관련된 메타데이터를 자동으로 추출하도록 인덱서에 지시합니다.

콘텐츠가 추출되면 데이터 원본에서 찾은 이미지의 텍스트를 추출하도록 `imageAction`을 설정할 수 있습니다. OCR 기술 및 텍스트 병합 기술과 함께 ```"generateNormalizedImages"``` 구성으로 설정된 ```"imageAction"```은 이미지에서 텍스트(예: 트래픽 중지 기호에서 "중지"라는 단어)를 추출하여 콘텐츠 필드의 일부로 포함시키도록 인덱서에 지시합니다. 이 동작은 문서에 포함된 이미지(예: PDF 내 이미지)뿐 아니라 JPG 파일 같은 데이터 원본의 이미지에도 적용됩니다.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - 인덱싱 모니터링

인덱서가 정의되면 사용자가 요청을 제출할 때 인덱서가 자동으로 실행됩니다. 사용자가 정의한 인식 기술에 따라 인덱싱이 예상보다 오래 걸릴 수 있습니다. 인덱서가 아직 실행되고 있는지 확인하려면 `GetStatus` 메서드를 사용합니다.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo`는 인덱서의 현재 상태 및 실행 기록을 나타냅니다.

경고는 일부 원본 파일 및 기술 조합에서 일반적이며 항상 문제를 나타내는 것은 아닙니다. 이 자습서의 경고는 심각하지 않습니다(예: JPEG 파일의 텍스트 입력이 없음).

`Main`에 다음 줄을 추가합니다.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - 검색

인덱싱이 완료되면 개별 필드의 콘텐츠를 반환하는 쿼리를 실행할 수 있습니다. 기본적으로 Azure Cognitive Search는 상위 50개 결과를 반환합니다. 샘플 데이터가 작기 때문에 기본값으로 충분합니다. 그러나 더 큰 데이터 집합으로 작업할 경우 쿼리 문자열에 더 많은 결과를 반환하는 매개 변수를 포함해야 합니다. 자세한 지침은 [Azure Cognitive Search에서 결과를 페이징하는 방법](search-pagination-page-layout.md)을 참조하세요.

확인 단계로 모든 필드에 대한 인덱스를 쿼리합니다.

`Main`에 다음 줄을 추가합니다.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient`는 애플리케이션의 구성 파일(appsettings.json)에 저장된 값을 사용하여 `SearchIndexClient`를 새로 만듭니다. 검색 서비스 쿼리 API 키가 사용되며 관리자 키는 사용되지 않습니다.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

다음 코드를 `Main`에 추가합니다. 첫 번째 try-catch는 각 필드의 이름, 유형 및 특성과 함께 인덱스 정의를 반환합니다. 두 번째는 매개 변수가 있는 쿼리이며, 여기서 `Select`는 결과에 포함할 필드(예: `organizations`)를 지정합니다. 검색 문자열이 `"*"`이면 단일 필드의 모든 콘텐츠를 반환합니다.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

추가 필드 반복: 이 연습의 콘텐츠, 언어 코드, 핵심 구 및 조직. 쉼표로 구분된 목록을 사용하여 [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) 속성을 통해 여러 필드를 반환할 수 있습니다.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

이 자습서의 샘플 코드는 기존 개체가 있는지 확인하고 코드를 다시 실행할 수 있도록 삭제합니다.

포털을 사용하여 인덱스, 인덱서, 데이터 원본 및 기술 세트를 삭제할 수도 있습니다.

## <a name="takeaways"></a>핵심 내용

이 자습서에서는 데이터 원본, 기술 집합, 인덱스 및 인덱서라는 구성 요소 부분을 만들어서 강화된 인덱싱 파이프라인을 빌드하는 기본 단계를 살펴보았습니다.

기술 세트 정의 및 입/출력을 통해 기술을 서로 연결하는 메커니즘과 함께 [미리 빌드된 기술](cognitive-search-predefined-skills.md)을 소개했습니다. 인덱서 정의의 `outputFieldMappings`는 보강된 값을 Azure Cognitive Search 서비스의 파이프라인에서 검색 가능한 인덱스로 라우팅하는 데 필요하다는 것도 배웠습니다.

마지막으로, 결과를 테스트하고 추가 반복을 위해 시스템을 다시 설정하는 방법을 배웠습니다. 인덱스에 대한 쿼리를 실행하면 보강된 인덱싱 파이프라인에서 만든 출력이 반환된다는 것을 배웠습니다. 인덱서 상태를 확인하는 방법과 파이프라인을 다시 실행하기 전에 어떤 개체를 삭제해야 하는지도 배웠습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 [모든 리소스] 또는 [리소스 그룹] 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AI 보강 파이프라인의 모든 개체에 대해 알아보았으면, 기술 세트 정의와 개별적인 기술을 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [기술 세트를 만드는 방법](cognitive-search-defining-skillset.md)
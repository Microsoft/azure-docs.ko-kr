---
title: .Net SDK를 사용하여 Azure Cosmos DB SQL API 계정으로 데이터 대량 가져오기
description: 데이터 가져오기에 필요한 프로비저닝된 처리량(RU/초)을 최적화하는 .NET 콘솔 애플리케이션을 빌드하여 데이터를 Azure Cosmos DB로 가져오거나 수집하는 방법 알아보기
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: b16402f809da18588b26995e7129d2f27575b48d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019252"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>.NET SDK를 사용하여 Azure Cosmos DB SQL API 계정으로 데이터 대량 가져오기

이 자습서에서는 Azure Cosmos DB으로 데이터를 가져오는 데 필요한 프로비저닝된 처리량(RU/초)을 최적화하는 .NET 콘솔 애플리케이션을 빌드하는 방법을 설명합니다. 이 문서에서는 샘플 데이터 원본에서 데이터를 읽고 Azure Cosmos 컨테이너로 가져옵니다.
이 자습서에서는 .NET Framework 또는 .NET Core를 대상으로 하는 [버전 3.0 이상](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) Azure Cosmos DB .NET SDK를 사용합니다.

이 자습서에서는 다음 내용을 다룹니다.

> [!div class="checklist"]
> * Azure Cosmos 계정 만들기
> * 프로젝트 구성
> * 대량 지원을 사용하도록 설정한 Azure Cosmos 계정에 연결
> * 동시 만들기 작업을 통해 데이터 가져오기 수행

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 지침을 따르기 전에 다음 리소스가 있는지 확인하세요.

* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). `dotnet --version`을 실행하여 작업 환경에서 사용할 수 있는 버전을 확인할 수 있습니다.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>1단계: Azure Cosmos DB 계정 만들기

Azure Portal에서 [Azure Cosmos DB SQL API 계정을 만들거나](create-cosmosdb-resources-portal.md)[Azure Cosmos DB 에뮬레이터](local-emulator.md)를 사용하여 계정을 만들 수 있습니다.

## <a name="step-2-set-up-your-net-project"></a>2단계: .NET 프로젝트 설정

로컬 컴퓨터에서 Windows 명령 프롬프트 또는 터미널 창을 엽니다. 명령 프롬프트 또는 터미널에서 다음 섹션의 명령을 모두 실행합니다. 다음 dotnet new 명령을 실행하여 이름이 *bulk-import-demo*인 새 앱을 만듭니다. `--langVersion` 매개 변수는 생성된 프로젝트 파일의 *LangVersion* 속성을 설정합니다.

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

이 빌드의 예상 출력은 다음과 같습니다.

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>3단계: Azure Cosmos DB 패키지 추가

애플리케이션 디렉터리에 있는 동안 dotnet add package 명령을 사용하여 .NET Core용 Azure Cosmos DB 클라이언트 라이브러리를 설치합니다.

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>4단계: Azure Cosmos 계정 자격 증명 가져오기

샘플 애플리케이션은 Azure Cosmos 계정을 인증해야 합니다. 인증을 받으려면 Azure Cosmos 계정 자격 증명을 애플리케이션에 전달해야 합니다. 다음 단계를 수행하여 Azure Cosmos 계정 자격 증명을 가져옵니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1.  Azure Cosmos 계정으로 이동합니다.
1.  **키** 창을 열고 계정의 **URI** 및 **기본 키**를 복사합니다.

Azure Cosmos DB 에뮬레이터를 사용하는 경우 [이 문서에서 에뮬레이터 자격 증명](local-emulator.md#authenticating-requests)을 가져옵니다.

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>5단계: 대량 실행을 지원하여 CosmosClient 개체 초기화

생성된 `Program.cs` 파일을 코드 편집기에서 엽니다. 대량 실행이 설정된 CosmosClient의 새 인스턴스를 만들고 이 인스턴스를 사용하여 Azure Cosmos DB에 대한 작업을 수행합니다. 

먼저 기본 `Main` 메서드를 덮어쓰고 전역 변수를 정의합니다. 이러한 전역 변수에는 엔드포인트 및 인증 키, 데이터베이스 이름, 만들 컨테이너 및 대량으로 삽입할 항목의 수가 포함됩니다. 작업 환경에 따라 엔드포인트 URL 및 인증 키 값을 바꾸어야 합니다. 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

`Main` 메서드 내에 다음 코드를 추가하여 CosmosClient 개체를 초기화합니다.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

대량 실행을 사용하도록 설정하면 CosmosClient는 내부적으로 동시 작업을 단일 서비스 호출로 그룹화합니다. 이러한 방식으로 여러 파티션에 서비스 호출을 분산하고 마지막으로 개별 결과를 원래 호출자에게 할당하여 처리량 사용률을 최적화합니다.

그런 다음, 컨테이너를 만들어 모든 항목을 저장할 수 있습니다.  `/pk`를 파티션 키로 정의하고, 프로비저닝된 처리량을 50000 RU/s로 정의하고, 모든 필드를 제외하여 쓰기 처리량을 최적화하는 사용자 지정 인덱싱 정책을 정의합니다. CosmosClient 초기화 문 뒤에 다음 코드를 추가합니다.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>6단계: 동시 작업 목록 채우기

대량 실행 지원을 활용하려면 데이터 원본 및 수행하려는 작업을 기준으로 비동기 작업 목록을 만들고 `Task.WhenAll`을 사용하여 동시에 실행합니다.
먼저 "가짜" 데이터를 사용하여 데이터 모델에서 항목 목록을 생성하는 것부터 시작해 보겠습니다. 실제 애플리케이션에서는 항목이 원하는 데이터 원본에서 제공됩니다.

먼저 dotnet add package 명령을 사용하여 솔루션에 가짜 패키지를 추가합니다.

   ```bash
   dotnet add package Bogus
   ```

저장하려는 항목의 정의를 지정합니다. `Program.cs` 파일 내에 `Item` 클래스를 정의해야 합니다.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

다음으로 `Program` 클래스 내에 도우미 함수를 만듭니다. 이 도우미 함수는 삽입하도록 정의한 항목 수를 가져오고 임의의 데이터를 생성합니다.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

항목을 읽고 `System.Text.Json` 클래스를 사용하여 스트림 인스턴스로 serialize합니다. 자동 생성된 데이터의 특성 때문에 데이터를 스트림으로 serialize합니다. 항목 인스턴스를 직접 사용할 수도 있지만 스트림으로 변환하면 CosmosClient에서 스트림 API의 성능을 활용할 수 있습니다. 일반적으로 파티션 키를 알고 있으면 데이터를 직접 사용할 수 있습니다. 


데이터를 스트림 인스턴스로 변환하려면 `Main` 메서드 내에서 컨테이너를 만든 직후에 다음 코드를 추가합니다.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

그런 다음, 데이터 스트림을 사용하여 동시 작업을 만들고 작업 목록을 채워 컨테이너에 항목을 삽입합니다. 이 작업을 수행하려면 `Program` 클래스에 다음 코드를 추가합니다.

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

이러한 모든 동시 시점 작업은 소개 섹션에 설명된 대로 대량으로 함께 실행됩니다.

## <a name="step-7-run-the-sample"></a>7단계: 샘플 실행

샘플을 실행하기 위해 간단히 `dotnet` 명령을 사용하면 됩니다.

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>전체 샘플 가져오기

이 자습서의 단계를 완료할 시간이 없거나 코드 샘플만 다운로드하려는 경우 [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer)에서 가져올 수 있습니다.

프로젝트를 복제한 후 [Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25) 내에서 원하는 자격 증명을 업데이트해야 합니다.

리포지토리 디렉터리로 변경하고 `dotnet`을 사용하여 샘플을 실행할 수 있습니다.

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 단계를 수행했습니다.

> [!div class="checklist"]
> * Azure Cosmos 계정 만들기
> * 프로젝트 구성
> * 대량 지원을 사용하도록 설정한 Azure Cosmos 계정에 연결
> * 동시 만들기 작업을 통해 데이터 가져오기 수행

이제 다음 자습서를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
>[SQL API를 사용하여 Azure Cosmos DB 쿼리](tutorial-query-sql-api.md)
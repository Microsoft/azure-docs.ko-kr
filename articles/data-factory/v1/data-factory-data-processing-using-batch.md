---
title: Data Factory 및 Batch를 사용하여 대규모 데이터 세트 처리
description: Azure Batch의 병렬 처리 기능을 사용하여 Azure Data Factory 파이프라인에서 대용량 데이터를 처리하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d0e608e1afae77afd44d7351b7c3f1f269bd8a8
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998087"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Data Factory 및 Batch를 사용하여 대규모 데이터 세트 처리
> [!NOTE]
> 이 문서는 일반 공급되는 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용하는 경우, [Data Factory의 사용자 지정 작업](../transform-data-using-dotnet-custom-activity.md)을 참조하세요.

이 문서에서는 예약된 자동 방식으로 대규모 데이터 세트를 이동 및 처리하는 샘플 솔루션의 아키텍처에 대해 설명합니다. 또한 Data Factory 및 Azure Batch를 사용하여 솔루션을 구현하는 엔드투엔드 연습 과정을 제공합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서는 전체 샘플 솔루션의 연습을 포함하기 때문에 일반적인 문서보다 깁니다. Batch 및 Data Factory를 처음 사용하는 경우 이러한 서비스 및 작동 방식에 대해 알아볼 수 있습니다. 서비스에 대해 알고 있고 솔루션을 디자인/구성하는 경우 문서의 아키텍처 섹션에 집중하면 됩니다. 프로토타입 또는 솔루션을 개발하는 경우 연습에서 단계별 지침을 사용해 보는 것이 좋습니다. 이 콘텐츠 및 사용 방법에 대한 사용자의 의견을 환영합니다.

첫째, Data Factory 및 Batch 서비스가 클라우드에서 대용량 데이터 세트를 처리할 수 있는 방법을 살펴보겠습니다.     


## <a name="why-azure-batch"></a>Azure Batch를 사용해야 하는 이유
 클라우드에서 Batch를 사용하여 대규모 병렬 및 HPC(고성능 컴퓨팅) 애플리케이션을 효율적으로 실행할 수 있습니다. Batch는 계산 집약적 작업이 관리되는 VM(가상 머신) 컬렉션에서 실행되도록 예약하는 플랫폼 서비스입니다. 작업의 요구 사항을 충족하기 위해 컴퓨팅 리소스의 크기를 조정할 수 있습니다.

Batch 서비스를 통해 애플리케이션을 병렬로 규모에 따라 실행하도록 Azure 컴퓨팅 리소스를 정의합니다. 주문형 또는 예약된 작업을 실행할 수 있습니다. HPC 클러스터, 개별 VM, 가상 네트워크 또는 복잡한 작업 및 태스크 예약 인프라를 수동으로 만들거나 구성하거나 관리할 필요가 없습니다.

 Batch에 대해 잘 모르는 경우 다음 문서를 통해 이 문서에 설명된 솔루션의 아키텍처/구현을 이해할 수 있습니다.   

* [Batch의 기본 사항](../../azure-sql/database/sql-database-paas-overview.md)
* [Batch 기능 개요](../../batch/batch-service-workflow-features.md)

필요에 따라 Batch에 대해 자세히 알아보려면 [Batch 설명서](https://docs.microsoft.com/azure/batch/)를 참조하세요.

## <a name="why-azure-data-factory"></a>Azure Data Factory를 사용해야 하는 이유
데이터 팩터리는 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. Data Factory를 사용하여 온-프레미스 및 클라우드 데이터 저장소에서 중앙 집중식 데이터 저장소로 데이터를 이동하는 관리되는 데이터 파이프라인을 만들 수 있습니다. 예제는 Azure Blob Storage입니다. Azure HDInsight 및 Azure Machine Learning과 같은 서비스를 사용하여 데이터를 처리/변환하는 데 Data Factory를 사용할 수 있습니다. 데이터 파이프라인을 예약된 방식(예: 매시간, 매일 및 매주)으로 실행되도록 예약할 수도 있습니다. 한 번에 파이프라인을 모니터링하고 관리하여 문제를 식별하고 조치를 취할 수 있습니다.

  Data Factory에 대해 잘 모르는 경우 다음 문서를 통해 이 문서에 설명된 솔루션의 아키텍처/구현을 이해할 수 있습니다.  

* [데이터 팩터리 소개](data-factory-introduction.md)
* [첫 번째 데이터 파이프라인 작성](data-factory-build-your-first-pipeline.md)   

필요에 따라 Data Factory에 대해 자세히 알아보려면 [Data Factory 설명서](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)를 참조하세요.

## <a name="data-factory-and-batch-together"></a>Data Factory 및 Batch
Data Factory는 기본 제공 작업을 포함합니다. 예를 들어 복사 작업은 원본 데이터 저장소에서 대상 데이터 저장소로 데이터를 복사/이동하는 데 사용됩니다. Hive 작업은 Azure에서 Hadoop 클러스터(HDInsight)를 사용하여 데이터를 처리하는 데 사용됩니다. 지원되는 변환 작업 목록은 [데이터 변환 작업](data-factory-data-transformation-activities.md)을 참조하세요.

고유 논리를 사용하여 데이터를 이동하거나 처리하도록 사용자 지정 .NET 작업을 만들 수도 있습니다. HDInsight 클러스터 또는 VM의 Batch 풀에서 이러한 작업을 실행할 수 있습니다. Batch를 사용할 경우 제공하는 수식에 따라 자동으로 크기가 조정되도록(워크로드에 따라 VM 추가 또는 제거) 풀을 구성할 수 있습니다.     

## <a name="architecture-of-a-sample-solution"></a>샘플 솔루션 아키텍처
  이 문서에 설명된 아키텍처는 간단한 솔루션에 사용됩니다. 금융 서비스, 이미지 처리와 렌더링 및 유전자 분석에 의한 위험 모델링과 같은 복잡한 시나리오와도 관련됩니다.

다이어그램은 Data Factory에서 데이터 이동 및 처리를 조정하는 방법을 보여줍니다. Batch에서 병렬 방식으로 데이터를 처리하는 방법도 보여줍니다. 쉽게 참조할 수 있도록 다이어그램을 다운로드하고 인쇄합니다(11x17인치 또는 A3 크기). 다이어그램을 인쇄할 수 있도록 액세스하려면 [Batch 및 Data Factory를 사용하여 HPC 및 데이터 오케스트레이션](https://go.microsoft.com/fwlink/?LinkId=717686)을 참조하세요.

[![대규모 데이터 처리 다이어그램](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

다음 목록은 프로세스의 기본 단계를 제공합니다. 솔루션에는 엔드투엔드 솔루션을 빌드하는 코드와 설명이 포함되어 있습니다.

* **컴퓨팅 노드(VM)의 풀과 함께 Batch를 구성합니다.** 노드 수와 각 노드의 크기를 지정할 수 있습니다.

* 데이터를 이동하고 변환하는 작업을 사용하여 Blob Storage, Batch 컴퓨팅 서비스, 입력/출력 데이터 및 워크플로/파이프라인을 나타내는 엔터티로 구성된 **Data Factory 인스턴스를 만듭니다**.

* **Data Factory 파이프라인에서 사용자 지정 .NET 작업을 만듭니다.** 작업은 Batch 풀에서 실행되는 사용자 코드입니다.

* **Azure Storage에 Blob으로 대량의 입력 데이터를 저장합니다.** 데이터는 논리 조각(일반적으로 시간으로)으로 나뉩니다.

* **Data Factory는 보조 위치에 병렬로 처리되는 데이터를 복사합니다** .

* **Data Factory는 Batch에서 할당한 풀을 사용하여 사용자 지정 작업을 실행합니다.** 데이터 팩터리는 작업을 동시에 실행할 수 있습니다. 각 작업은 데이터 조각을 처리합니다. 결과는 스토리지에 저장됩니다.

* 앱을 통해 배포하거나 다른 도구에서 추가로 처리하기 위한 목적으로 **Data Factory는 최종 결과를 세 번째 위치로 이동시킵니다**.

## <a name="implementation-of-the-sample-solution"></a>샘플 솔루션의 구현
샘플 솔루션은 의도적으로 간단합니다. Data Factory와 Batch를 사용하여 데이터 세트를 처리하는 방법을 알려주도록 디자인되었습니다. 솔루션은 시계열에 구성된 입력 파일에서 검색 단어 "Microsoft"의 발생 수를 계산합니다. 그러면 출력 파일에 개수를 출력합니다.

**시간:** Azure, Data Factory 및 Batch의 기본 사항에 익숙하고 다음과 같은 필수 구성 요소를 완료했다면 이 솔루션이 완료되는 데 1~2시간이 소요됩니다.

### <a name="prerequisites"></a>사전 요구 사항
#### <a name="azure-subscription"></a>Azure 구독
Azure 구독이 없는 경우 신속하게 평가판 계정을 만들 수 있습니다. 자세한 내용은 [평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

#### <a name="azure-storage-account"></a>Azure Storage 계정
스토리지 계정을 사용하여 이 자습서에 있는 데이터를 저장합니다. 스토리지 계정이 없는 경우 [스토리지 계정 만들기](../../storage/common/storage-account-create.md)를 참조하세요. 샘플 솔루션은 Blob Storage를 사용합니다.

#### <a name="azure-batch-account"></a>Azure Batch 계정
[Azure Portal](https://portal.azure.com/)을 사용하여 Batch 계정을 만듭니다. 자세한 내용은 [Batch 계정 만들기 및 관리](../../batch/batch-account-create-portal.md)를 참조하세요. Batch 계정 이름 및 계정 키를 적어둡니다. [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) cmdlet을 사용하여 Batch 계정을 만들 수도 있습니다. 이 cmdlet 사용 방법에 대한 자세한 지침은 [Batch PowerShell cmdlet 시작](../../batch/batch-powershell-cmdlets-get-started.md)을 참조하세요.

샘플 솔루션은 Batch를 사용하여(간접적으로 Data Factory 파이프라인을 통해) VM의 관리되는 컬렉션인 컴퓨팅 노드의 풀에서 병렬 방식으로 데이터를 처리합니다.

#### <a name="azure-batch-pool-of-virtual-machines"></a>가상 머신의 Azure Batch 풀
적어도 2개의 컴퓨팅 노드를 포함한 Batch 풀을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 왼쪽 메뉴의 **찾아보기** 및 **Batch 계정**을 차례로 선택합니다.

1. Batch 계정을 선택하여 **Batch 계정** 블레이드를 엽니다.

1. **풀** 타일을 선택합니다.

1. **풀** 블레이드에서 도구 모음의 **추가** 단추를 선택하여 풀을 추가합니다.

   a. 풀에 대한 ID(**풀 ID**)를 입력합니다. 풀의 ID를 적어둡니다. Data Factory 솔루션을 만들 때 필요합니다.

   b. **운영 체제 제품군** 설정에 **Windows Server 2012 R2**를 지정합니다.

   다. **노드 가격 책정 계층**을 선택합니다.

   d. **대상 전용** 설정 값으로 **2**를 입력합니다.

   e. **노드당 최대 작업** 설정 값으로 **2**를 입력합니다.

   f. **확인**을 선택하여 풀을 만듭니다.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
[Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) 또는 [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)(ClumsyLeaf Software)를 사용하여 Storage 프로젝트의 데이터를 검사하고 변경합니다. 클라우드 호스팅 애플리케이션의 로그에 있는 데이터를 검사하고 변경할 수도 있습니다.

1. 프라이빗 액세스로 **mycontainer**라는 컨테이너를 만듭니다(익명 액세스 없음).

1. CloudXplorer를 사용하는 경우 다음과 같은 구조의 폴더와 하위 폴더를 만듭니다.

   ![폴더 및 하위 폴더 구조](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` 및 `outputfolder`는 `mycontainer`에서 최상위 폴더입니다. `inputfolder` 폴더에는 날짜-시간 스탬프(YYYY-MM-DD-HH)를 포함하는 하위 폴더가 있습니다.

   Storage Explorer를 사용하는 경우 다음 단계에서 `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` 등과 같은 이름이 지정된 파일을 업로드합니다. 이 단계는 자동으로 폴더를 만듭니다.

1. 키워드 **Microsoft**가 있는 콘텐츠를 사용하여 컴퓨터에 텍스트 파일 **file.txt**를 만듭니다. "테스트 사용자 지정 작업 Microsoft 테스트 사용자 지정 작업 Microsoft"를 예로 들 수 있습니다.

1. Blob Storage의 다음 입력 폴더에 파일을 업로드합니다.

   ![입력 폴더](./media/data-factory-data-processing-using-batch/image4.png)

   Azure Storage Explorer를 사용하는 경우 **file.txt** 파일을 **mycontainer**에 업로드합니다. 도구 모음의 **복사**를 선택하여 Blob의 복사본을 만듭니다. **Blob 복사** 대화 상자에서 **대상 Blob 이름**을 `inputfolder/2015-11-16-00/file.txt`로 변경합니다. 이 단계를 반복하여 `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` 등을 만듭니다. 이 작업은 자동으로 폴더를 만듭니다.

1. `customactivitycontainer`라는 다른 컨테이너를 만듭니다. 이 컨테이너에 사용자 지정 작업 zip 파일을 업로드합니다.

#### <a name="visual-studio"></a>Visual Studio
Data Factory 솔루션에서 사용될 사용자 지정 Batch 활동을 만들려면 Visual Studio 2012 이상을 설치합니다.

### <a name="high-level-steps-to-create-the-solution"></a>솔루션을 만들기 위한 대략적인 단계
1. 데이터 처리 논리를 포함하는 사용자 지정 활동을 만듭니다.

1. 사용자 지정 작업을 사용하는 Data Factory를 만듭니다.

### <a name="create-the-custom-activity"></a>사용자 지정 작업 만들기
Data Factory 사용자 지정 작업은 이 샘플 솔루션의 핵심입니다. 샘플 솔루션은 Batch를 사용하여 사용자 지정 작업을 실행합니다. 사용자 지정 작업을 개발하고 Data Factory 파이프라인에서 사용하는 방법에 대한 정보는 [Data Factory 파이프라인에서 사용자 지정 작업 사용](data-factory-use-custom-activities.md)을 참조하세요.

Data Factory 파이프라인에서 사용할 수 있는 .NET 사용자 지정 작업을 만들려면 IDotNetActivity 인터페이스를 구현하는 클래스를 포함하는 .NET 클래스 라이브러리 프로젝트를 만듭니다. 이 인터페이스는 Execute라는 하나의 메서드만 포함합니다. 해당 메서드의 서명은 다음과 같습니다.

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

이 메서드에는 이해해야 하는 몇 가지 주요 구성 요소가 포함됩니다.

* 이 메서드는 다음과 같은 네 개의 매개 변수를 사용합니다.

  * **linkedServices**. 이 매개 변수는 입/출력 데이터 원본(예: Blob Storage)을 데이터 팩터리에 연결하는 연결된 서비스의 열거형 목록입니다. 이 샘플에서는 입력 및 출력 모두에 사용되는 Azure Storage 형식의 연결된 서비스가 하나만 있습니다.
  * **데이터 세트**. 이 매개 변수는 데이터 세트의 열거형 목록입니다. 이 매개 변수를 사용하여 입력 및 출력 데이터 세트에 정의된 위치 및 스키마를 가져올 수 있습니다.
  * **activity**. 이 매개 변수는 현재 컴퓨팅 엔터티를 나타냅니다. 이 경우 Batch 서비스입니다.
  * **logger**. 로거를 사용하여 파이프라인에서 "사용자" 로그로 노출할 디버그 주석을 기록할 수 있습니다.
* 이 메서드는 나중에 사용자 지정 작업을 함께 연결하는 데 사용할 수 있는 사전을 반환합니다. 이 기능은 아직 구현되지 않았기 때문에, 메서드로부터 빈 사전이 반환됩니다.

#### <a name="procedure-create-the-custom-activity"></a>프로시저: 사용자 지정 작업 만들기
1. Visual Studio에서 .NET 클래스 라이브러리 프로젝트를 만듭니다.

   a. Start Visual Studio 2012/2013/2015.

   b. **File** > **New** > **Project**를 선택합니다.

   다. **템플릿**을 확장하고 **Visual C\#** 를 선택합니다. 이 연습에서는 C\#를 사용하지만 다른 .NET 언어를 사용하여 사용자 지정 작업을 개발할 수도 있습니다.

   d. 오른쪽의 프로젝트 형식 목록에서 **클래스 라이브러리**를 선택합니다.

   e. **이름**에 **MyDotNetActivity**를 입력합니다.

   f. **C:\\ADF**를 **위치**로 선택합니다. 존재하지 않는 경우 폴더 **ADF**를 만듭니다.

   g. **확인**을 선택하여 프로젝트를 만듭니다.

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.

1. 패키지 관리자 콘솔에서 다음 명령을 실행하여 Microsoft.Azure.Management.DataFactories를 가져옵니다.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. **Azure Storage** NuGet 패키지를 프로젝트로 가져옵니다. 이 샘플에서 Blob Storage API를 사용하므로 이 패키지가 필요합니다.

    ```powershell
    Install-Package Az.Storage
    ```
1. 다음 using 지시문을 프로젝트의 원본 파일에 추가합니다.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. 네임스페이스의 이름을 **MyDotNetActivityNS**로 변경합니다.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. 클래스 이름을 **MyDotNetActivity**로 변경하고 다음과 같이 **IDotNetActivity** 인터페이스에서 클래스를 파생합니다.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. **IDotNetActivity** 인터페이스의 **Execute** 메서드를 **MyDotNetActivity** 클래스에 구현(추가)합니다. 다음 샘플 코드를 메서드에 복사합니다. 이 메서드에 사용되는 논리에 대한 설명은 [메서드 실행](#execute-method) 섹션을 참조하세요.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. 클래스에 다음과 같은 도우미 메서드를 추가합니다. 이 메서드는 **Execute** 메서드로 호출됩니다. 가장 중요한 점은 **Calculate** 메서드가 각 Blob을 반복하는 코드를 격리한다는 것입니다.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    GetFolderPath 메서드는 데이터 세트가 가리키는 폴더로 경로를 반환하고 GetFileName 메서드는 데이터 세트가 가리키는 BLOB/파일의 이름을 반환합니다.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Calculate 메서드는 입력 파일(폴더에서 BLOB)에서 "Microsoft" 키워드의 인스턴스 수를 계산합니다. 검색 용어 "Microsoft"는 코드에 하드 코딩됩니다.

1. 프로젝트를 컴파일합니다. 메뉴에서 **빌드**, **솔루션 빌드**를 차례로 선택합니다.

1. Windows 탐색기를 시작하고, **bin\\debug** 또는 **bin\\release** 폴더로 이동합니다. 선택하는 폴더하는 빌드의 형식에 따라 달라집니다.

1. **\\bin\\Debug** 폴더의 이진을 모두 포함하는 **MyDotNetActivity.zip** Zip 파일을 만듭니다. 오류가 발생할 경우 문제를 발생시킨 소스 코드의 줄 번호 같은 추가 정보를 받을 수 있도록 MyDotNetActivity.**pdb** 파일을 포함할 수 있습니다.

   ![Bin\Debug 폴더 목록](./media/data-factory-data-processing-using-batch/image5.png)

1. ADFTutorialDataFactory의 연결된 서비스 StorageLinkedService가 사용하는 Blob Storage의 Blob 컨테이너 `customactivitycontainer`에 Blob으로 **MyDotNetActivity.zip**을 업로드합니다. 아직 없는 경우 `customactivitycontainer` Blob 컨테이너를 만듭니다.

#### <a name="execute-method"></a>메서드 실행
이 섹션에서는 Execute 메서드에서 코드에 대한 자세한 내용을 제공합니다.

1. 입력 컬렉션을 반복하는 멤버는 [Microsoft.WindowsAzure.Storage.Blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) 네임스페이스에 있습니다. Blob 컬렉션을 반복하려면 **BlobContinuationToken** 클래스를 사용해야 합니다. 본질적으로 루프를 종료하기 위한 메커니즘으로 토큰과 함께 do-while 루프를 사용해야 합니다. 자세한 내용은 [.NET에서 Blob Storage를 사용하는 방법](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요. 기본 루프는 다음과 같습니다.

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   자세한 내용은 [ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) 메서드에 대한 설명서를 참조하세요.

1. Blob 집합을 진행하는 코드는 논리적으로 do-while 루프 안으로 들어갑니다. **Execute** 메서드에서 do-while 루프는 **Calculate**라는 메서드로 Blob 목록을 전달합니다. 이 메서드는 **output** 이라는 문자열 변수를 반환하는데, 이는 세그먼트에서 모든 Blob을 반복한 결과입니다.

   **Calculate** 메서드에 전달된 Blob에서 검색 용어 "Microsoft" 발생 수를 반환합니다.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. **Calculate** 메서드가 작업을 완료한 후에 새 Blob에 작성되어야 합니다. 처리된 모든 BLOB 집합에 대해 결과를 새 BLOB에 작성할 수 있습니다. 새 BLOB를 작성하려면 먼저 출력 데이터 세트를 찾습니다.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. 이 코드는 **GetFolderPath** 도우미 메서드도 호출하여 폴더 경로(스토리지 컨테이너 이름)를 검색합니다.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   GetFolderPath 메서드는 DataSet 개체를 AzureBlobDataSet로 캐스팅하며 여기에는 FolderPath라는 속성이 포함됩니다.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. 이 코드는 파일 이름(BLOB 이름)을 검색할 **GetFileName** 메서드를 호출합니다. 이 코드는 폴더 경로를 가져오는 데 사용되는 이전 코드와 유사합니다.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. 파일 이름은 URI 개체를 만들어 기록합니다. URI 생성자는 컨테이너 이름을 반환하는 **BlobEndpoint** 속성을 사용합니다. 출력 BLOB URI를 생성하기 위해 폴더 경로 및 파일 이름이 추가됩니다.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. 파일 이름이 작성된 후에 **Calculate** 메서드에서 새 Blob으로 출력 문자열을 작성할 수 있습니다.

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>데이터 팩터리 만들기
[사용자 지정 작업 만들기](#create-the-custom-activity) 섹션에서 사용자 지정 작업을 만들고 이진 파일과 함께 zip 파일을 업로드하고 PDB 파일을 Blob 컨테이너에 업로드했습니다. 이 섹션에서는 사용자 지정 작업을 사용하는 파이프라인으로 Data Factory를 만듭니다.

사용자 지정 작업에 대한 입력 데이터 세트은 Blob Storage에서 입력 폴더(`mycontainer\\inputfolder`)의 Blob(파일)을 나타냅니다. 이 작업에 대한 출력 데이터 세트은 Blob Storage에서 출력 폴더(`mycontainer\\outputfolder`)의 출력 Blob을 나타냅니다.

입력 폴더에 있는 하나 이상의 파일을 삭제합니다.

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

예를 들어 각 폴더에 다음과 같은 콘텐츠로 하나의 파일(file.txt)을 삭제합니다.

```
test custom activity Microsoft test custom activity Microsoft
```

각 입력 폴더는 폴더에 2개 이상의 파일이 포함된 경우에도 Data Factory의 조각에 해당합니다. 각 조각이 파이프라인으로 처리될 때 사용자 지정 작업은 해당 조각에 대한 입력 폴더에서 모든 BLOB를 반복합니다.

동일한 콘텐츠를 가진 5개의 출력 파일이 표시됩니다. 예를 들어 2015-11-16-00 폴더의 파일 처리에서 출력 파일은 다음 내용을 포함합니다.

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

동일한 콘텐츠를 가진 여러 개의 파일(file.txt, file2.txt, file3.txt)을 입력 폴더로 삭제하면 출력 파일에 다음 내용이 표시됩니다. 각 폴더(2015-11-16-00 등)는 폴더에 여러 개의 입력 파일이 있더라도 이 샘플에 있는 분할 영역에 해당합니다.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

출력 파일은 이제 조각(2015-11-16-00)과 연결된 폴더의 각 입력 파일(Blob)에 하나씩 세 개의 줄을 가집니다.

각 작업 실행에 대한 작업(task)이 만들어집니다. 이 샘플에서는 파이프라인에 하나의 작업만 있습니다. 조각이 파이프라인에 의해 처리될 때 사용자 지정 작업은 조각을 처리하도록 Batch에서 실행됩니다. 5개 조각(각 조각은 여러 Blob 또는 파일을 가질 수 있음)이 있기 때문에 Batch에서 5개의 작업이 생성됩니다. 작업이 Batch에서 실행될 때 사용자 지정 작업이 실행됩니다.

다음 연습에서는 추가 정보를 제공합니다.

#### <a name="step-1-create-the-data-factory"></a>1단계: 데이터 팩터리 만들기
1. [Azure Portal](https://portal.azure.com/)에 로그인한 후에 다음 단계를 수행합니다.

   a. 왼쪽 메뉴에서 **새로 만들기**를 선택합니다.

   b. **새** 블레이드에서 **데이터 + 분석**을 선택합니다.

   다. **데이터 분석** 블레이드에서 **Data Factory**를 선택합니다.

1. **새 Data Factory** 블레이드에서 이름으로 **CustomActivityFactory**를 입력합니다. 데이터 팩터리 이름은 전역적으로 고유해야 합니다. "Data Factory 이름 CustomActivityFactory를 사용할 수 없습니다."라는 오류를 수신하는 경우 Data Factory의 이름을 변경합니다. 예를 들어 yournameCustomActivityFactory를 사용하여 Data Factory를 다시 만듭니다.

1. **리소스 그룹 이름**을 선택하여 기존 리소스 그룹을 선택하거나 리소스 그룹을 만듭니다.

1. Data Factory를 만들려는 구독 및 지역을 제대로 만들었는지 확인합니다.

1. **새 Data Factory** 블레이드에서 **만들기**를 선택합니다.

1. Data Factory가 포털의 대시보드에 생성됩니다.

1. Data Factory 만들기를 완료한 후에는 **Data Factory** 페이지가 표시되며 여기에 Data Factory의 내용이 표시됩니다.

   ![Data Factory 페이지](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2단계: 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 컴퓨팅 서비스를 Data Factory에 연결합니다. 이 단계에서는 스토리지 계정 및 Batch 계정을 Data Factory에 연결합니다.

#### <a name="create-an-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
1. **CustomActivityFactory**에 대한 **Data Factory** 블레이드에서 **작성 및 배포** 타일을 선택합니다. 데이터 팩터리 편집기가 표시됩니다.

1. 명령 모음에서 **새 데이터 저장소**를 선택하고 **Azure Storage**를 선택합니다. 편집기에 Storage 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다.

   ![새 데이터 저장소](./media/data-factory-data-processing-using-batch/image7.png)

1. **account name**을 스토리지 계정 이름으로 바꿉니다. **account key**를 스토리지 계정의 액세스 키로 바꿉니다. 스토리지 액세스 키를 가져오는 방법을 알아보려면 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요.

1. 명령 모음에서 **배포**를 선택하여 연결된 서비스를 배포합니다.

   ![배포](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Azure Batch 연결된 서비스 만들기
이 단계에서는 Data Factory 사용자 지정 작업을 실행하는 데 사용될 Batch 계정에 대한 연결된 서비스를 만듭니다.

1. 명령 모음에서 **새 컴퓨팅**을 선택하고 **Azure Batch**를 선택합니다. 편집기에 Batch 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다.

1. JSON 스크립트에서:

   a. **계정 이름**을 Batch 계정의 이름으로 대체합니다.

   b. **액세스 키**를 Batch 계정의 액세스 키로 대체합니다.

   다. **poolName** 속성에 대한 풀 ID를 입력합니다. 이 속성의 경우 풀 이름 또는 풀 ID 중 하나를 지정할 수 있습니다.

   d. **batchUri** JSON 속성에 대한 배치 URI를 입력합니다.

      > [!IMPORTANT]
      > **Batch 계정** 블레이드의 URL 형식은 \<accountname\> 다음과 같습니다.. \<region\> batch.azure.com. JSON 스크립트의 **batchUri** 속성의 경우 URL에서 a88"accountname."**을 제거해야 합니다. 예제는 `"batchUri": "https://eastus.batch.azure.com"`입니다.
      >
      >

      ![Batch 계정 블레이드](./media/data-factory-data-processing-using-batch/image9.png)

      **poolName** 속성의 경우 풀 이름 대신 풀 ID를 지정할 수도 있습니다.

      > [!NOTE]
      > Data Factory 서비스는 HDInsight에 대해서와 마찬가지로 Batch에 대한 주문형 옵션을 지원하지 않습니다. Data Factory에서 고유한 Batch 풀을 사용할 수 있습니다.
      >
      >

   e. **linkedServiceName** 속성에 대해 **StorageLinkedService**를 지정합니다. 이 연결된 서비스는 이전 단계에서 만들었습니다. 이 스토리지는 파일 및 로그에 대한 준비 영역으로 사용됩니다.

1. 명령 모음에서 **배포**를 선택하여 연결된 서비스를 배포합니다.

#### <a name="step-3-create-datasets"></a>3단계: 데이터 세트 만들기
이 단계에서는 입력 및 출력 데이터를 나타낼 데이터 세트를 만듭니다.

#### <a name="create-the-input-dataset"></a>입력 데이터 세트 만들기
1. Data Factory 편집기의 도구 모음에서 **새 데이터 세트** 단추를 선택합니다. 드롭 다운 목록에서 **Azure Blob Storage**를 선택합니다.

1. 오른쪽 창의 JSON 스크립트를 다음 JSON 코드 조각으로 바꿉니다.

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    이 연습에서는 나중에 시작 시간 2015-11-16T00:00:00Z 및 종료 시간 2015-11-16T05:00:00Z로 파이프라인을 만듭니다. 매시간 데이터를 생성하도록 예약됩니다. 따라서 5개의 입/출력 조각이 있습니다(**00**:00:00 -\> **05**:00:00 사이).

    입력 데이터 세트의 **빈도** 및 **간격**은 **시간** 및 **1**로 설정되며 이는 입력 조각이 매시간 제공됨을 의미합니다.

    각 조각에 대한 시작 시간은 이전의 JSON 코드 조각에서 **SliceStart** 시스템 변수로 표시됩니다. 각 조각에 대한 시작 시간은 다음과 같습니다.

    | **조각** | **시작 시간**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    **folderPath**는 조각 시작 시간(**SliceStart**)의 연도, 월, 일 및 시간 부분을 사용하여 계산됩니다. 입력 폴더가 조각에 매핑되는 방식은 다음과 같습니다.

    | **조각** | **시작 시간**          | **입력 폴더**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. 도구 모음에서 **배포**를 선택하여 **InputDataset** 테이블을 만들고 배포합니다.

#### <a name="create-the-output-dataset"></a>출력 데이터 세트 만들기
이 단계에서는 출력 데이터를 나타내는 다른 AzureBlob 형식의 데이터 세트를 만듭니다.

1. Data Factory 편집기의 도구 모음에서 **새 데이터 세트** 단추를 선택합니다. 드롭 다운 목록에서 **Azure Blob Storage**를 선택합니다.

1. 오른쪽 창의 JSON 스크립트를 다음 JSON 코드 조각으로 바꿉니다.

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    각 입력 조각에 대해 출력 BLOB/파일이 생성됩니다. 각 조각에 대해 출력 파일의 이름을 지정하는 방법은 다음과 같습니다. `mycontainer\\outputfolder`라는 하나의 출력 폴더에 모든 출력 파일이 생성됩니다.

    | **조각** | **시작 시간**          | **출력 파일**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    입력 폴더(예: 2015-11-16-00)에 있는 모든 파일은 시작 시간(2015-11-16-00)의 조각 중 일부입니다. 이 조각을 처리할 때 사용자 지정 작업은 각 파일을 검색하고 검색 용어 "Microsoft"의 항목 수와 함께 출력 파일에 줄을 생성합니다. 폴더 2015-11-16-00에 세 개의 파일이 있는 경우 출력 파일(2015-11-16-00.txt)에 세 줄이 있게 됩니다.

1. 도구 모음에서 **배포**를 선택하여 **OutputDataset**을 만들고 배포합니다.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>4단계: 사용자 지정 작업을 사용하여 파이프라인 만들기 및 실행
이 단계에서는 이전에 만든 하나의 작업, 사용자 지정 작업을 사용하여 파이프라인을 만듭니다.

> [!IMPORTANT]
> **file.txt**를 Blob 컨테이너의 입력 폴더에 업로드하지 않은 경우 파이프라인을 만들기 전에 업로드합니다. **isPaused** 속성은 파이프라인 JSON에서 false로 설정되었으므로 파이프라인은 즉시 과거의 **시작** 날짜로 실행합니다.
>
>

1. Data Factory 편집기의 명령 모음에서 **새 파이프라인**을 선택합니다. 명령이 보이지 않으면 표시할 줄임표 기호를 선택합니다.

1. 오른쪽 창의 JSON 스크립트를 다음 JSON 코드 조각으로 바꿉니다.

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   다음 사항에 유의하세요.

   * 파이프라인에는 **DotNetActivity** 형식인 하나의 작업만이 있습니다.
   * **AssemblyName**은 DLL **MyDotNetActivity.dll** 이름으로 설정합니다.
   * **EntryPoint**는 **MyDotNetActivityNS.MyDotNetActivity**로 설정합니다. 기본적으로 \<namespace\> 입니다.\<classname\> 코드에서
   * **PackageLinkedService**가 사용자 지정 작업 zip 파일을 포함하는 Blob Storage를 가리키는 **StorageLinkedService**로 설정됩니다. 입/출력 파일 및 사용자 지정 작업 zip 파일에 대해 서로 다른 스토리지 계정을 사용하는 경우 다른 Storage 연결된 서비스를 만들어야 합니다. 이 문서에서는 동일한 스토리지 계정을 사용한다고 가정합니다.
   * **PackageFile**은 **customactivitycontainer/MyDotNetActivity.zip**으로 설정합니다. 형식 \<containerforthezip\> / \<nameofthezip.zip\> 입니다.
   * 사용자 지정 작업은 입력으로 **InputDataset**, 출력으로 **OutputDataset**을 사용합니다.
   * 사용자 지정 작업의 **linkedServiceName** 속성은 **AzureBatchLinkedService**를 가리키며 Data Factory에 사용자 지정 작업을 Batch에서 실행해야 함을 알려줍니다.
   * **동시성** 설정은 중요합니다. Batch 풀에 2개 이상의 컴퓨팅 노드가 있더라도 기본값(1)을 사용하는 경우 조각은 차례로 처리됩니다. 따라서 Batch의 병렬 처리 기능을 활용하지 못합니다. **동시성**을 더 높은 값, 예를 들어 2로 설정한 경우 2조각(Batch에서 2개의 작업에 해당)은 동시에 처리될 수 있습니다. 이 경우에 Batch 풀의 두 VM이 모두 활용됩니다. 동시성 속성을 적절하게 설정합니다.
   * 기본적으로 VM에서 언제든지 하나의 작업(조각)이 실행됩니다. 기본적으로 **VM 당 최대 작업**은 Batch 풀에 대해 1로 설정됩니다. 필수 구성 요소의 일부로 이 속성이 2로 설정된 풀을 만들었습니다. 따라서 두 개의 Data factory 조각이 VM에서 동시에 실행될 수 있습니다.
     - **isPaused** 속성은 기본적으로 false로 설정됩니다. 이 예제에서는 조각이 이전에 시작되므로 파이프라인이 즉시 실행됩니다. 파이프라인을 일시 중지하려면 이 속성을 **true**로 설정하고 다시 시작하려면 **false**로 다시 설정할 수 있습니다.
     -   **시작** 및 **끝** 시간은 5시간 간격입니다. 5개의 조각이 파이프라인에 의해 생성되도록 조각은 매시간 생성됩니다.

1. 명령 모음에서 **배포**를 선택하여 파이프라인을 배포합니다.

#### <a name="step-5-test-the-pipeline"></a>5단계: 파이프라인 테스트
이 단계에서는 입력 폴더에 파일을 삭제하여 파이프라인을 테스트합니다. 각 입력 폴더에 하나의 파일을 사용하여 파이프라인을 테스트하기 시작합니다.

1. Azure Portal의 **Data Factory** 블레이드에서 **다이어그램**을 클릭합니다.

   ![다이어그램](./media/data-factory-data-processing-using-batch/image10.png)

1. **다이어그램** 뷰에서 **InputDataset** 입력 데이터 세트를 두 번 클릭합니다.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. 모든 5조각이 준비된 상태로 **InputDataset** 블레이드가 표시됩니다. 각 조각에 대해 **SLICE START TIME** 및 **SLICE END TIME**입니다.

   ![입력 조각 시작 및 종료 시간](./media/data-factory-data-processing-using-batch/image12.png)

1. **다이어그램 뷰**에서 **OutputDataset**을 선택합니다.

1. 5개의 출력 조각이 생성된 경우 **준비** 상태로 표시됩니다.

   ![출력 조각 시작 및 종료 시간](./media/data-factory-data-processing-using-batch/image13.png)

1. 포털을 사용하여 조각과 연결된 작업을 보고 각 조각이 실행된 VM을 확인합니다. 자세한 내용은 [Data Factory 및 Batch 통합](#data-factory-and-batch-integration) 섹션을 참조하세요.

1. 출력 파일이 Blob Storage의 `outputfolder`에 있는 `mycontainer` 아래에 표시됩니다.

   ![스토리지의 출력 파일](./media/data-factory-data-processing-using-batch/image15.png)

   각 입력 조각에 대해 하나씩 5개의 출력 파일이 나열됩니다. 각 출력 파일은 다음 출력과 유사한 콘텐츠를 포함합니다.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   다음 다이어그램에서는 Data Factory 조각이 Batch의 작업에 매핑하는 방법을 보여줍니다. 이 예제에서는 하나의 조각은 하나의 실행만 가집니다.

   ![조각 매핑 다이어그램](./media/data-factory-data-processing-using-batch/image16.png)

1. 이제 폴더의 여러 파일을 시도하세요. **2015-11-06-01** 폴더의 file.txt와 동일한 콘텐츠를 가진 **file2.txt**, **file3.txt**, **file4.txt** 및 **file5.txt** 파일을 만듭니다.

1. 출력 폴더에서 출력 파일 **2015-11-16-01.txt**를 삭제합니다.

1. **OutputDataset** 블레이드에서 **조각 시작 시간**이 **11/16/2015 01:00:00 AM**으로 설정된 조각을 마우스 오른쪽 단추를 클릭합니다. **실행**을 선택하여 조각을 다시 실행/다시 처리합니다. 이제 조각에 하나의 파일 대신 5개의 파일이 있습니다.

    ![다음을 실행합니다.](./media/data-factory-data-processing-using-batch/image17.png)

1. 조각이 실행되고 해당 상태가 **준비** 상태가 된 후 이 조각에 대한 출력 파일(**2015-11-16-01.txt**)의 콘텐츠를 확인합니다. 출력 파일이 Blob Storage의 `outputfolder`에 있는 `mycontainer` 아래에 표시됩니다. 조각의 각 파일에 대한 줄이 있어야 합니다.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> 5개의 입력 파일을 시도하기 전에 출력 파일 2015-11-16-01.txt를 삭제하지 않은 경우 이전 조각 실행에서 한 줄이 표시되고 현재 조각 실행에서 5줄이 표시됩니다. 기본적으로 콘텐츠는 이미 있는 경우 출력 파일에 추가됩니다.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory 및 Batch 통합
Data Factory 서비스는 Batch에 `adf-poolname:job-xxx`라는 이름으로 작업을 만듭니다.

![Batch 작업](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

조각의 각 작업 실행에 대한 작업(task)이 작업(job)에 만들어집니다. 10개 조각이 처리를 위해 준비된 경우 이 작업에 10개 작업이 만들어집니다. 풀에 여러 컴퓨팅 노드가 있는 경우 병렬로 실행 중인 두 개 이상의 조각을 포함할 수 있습니다. 컴퓨팅 노드당 최대 작업이 1보다 크게 설정된 경우 두 개 이상의 조각이 동일한 컴퓨팅에서 실행될 수 있습니다.

이 예제에는 5개 조각이 있으므로 Batch에 5개의 작업이 있게 됩니다. Data Factory의 파이프라인 JSON에서 **동시성**을 **5**로 설정하고, **2**개의 VM이 있는 Batch 풀에서 **VM당 최대 작업**을 **2**로 설정하면 작업이 빠르게 실행됩니다. (작업에 대한 시작 시간과 종료 시간을 확인합니다.)

포털을 사용하여 Batch 작업 및 조각 연결된 작업을 보고 각 조각이 실행된 VM을 확인합니다.

![Batch 작업 태스크](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>파이프라인 디버깅
디버깅은 몇 가지 기본적인 방법으로 구성됩니다.

1. 입력 조각이 **Ready**로 설정되지 않은 경우 입력 폴더 구조가 올바르고 file.txt가 입력 폴더에 있는지 확인합니다.

   ![입력 폴더 구조](./media/data-factory-data-processing-using-batch/image3.png)

1. 사용자 지정 작업의 **Execute** 메서드에서 **IActivityLogger** 개체를 사용하여 문제 해결에 도움이 되는 정보를 기록합니다. 기록된 메시지는 user\_0.log 파일에 표시됩니다.

   **OutputDataset** 블레이드에서 조각을 선택하여 조각에 대한 **데이터 조각** 블레이드를 확인합니다. **활동 실행** 아래에서 해당 조각에 대한 하나의 활동 실행이 표시됩니다. 명령 모음에서 **실행**을 선택하면 동일한 조각에 대해 다른 활동 실행을 시작할 수 있습니다.

   활동 실행을 선택하면 로그 파일 목록과 함께 **활동 실행 세부 정보** 블레이드가 표시됩니다. 기록된 메시지는 user\_0.log 파일에 표시됩니다. 오류가 발생하면 파이프라인/작업 JSON에서 재시도 횟수가 3으로 설정되므로 세 개의 작업 실행이 표시됩니다. 활동 실행을 선택하면 문제 해결을 위해 검토할 수 있는 로그 파일이 표시됩니다.

   ![OutputDataset 및 데이터 조각 블레이드](./media/data-factory-data-processing-using-batch/image18.png)

   로그 파일 목록에서 **user-0.log**를 선택합니다. 오른쪽 패널에서 **IActivityLogger.Write** 메서드를 사용한 결과가 표시됩니다.

   ![활동 실행 세부 정보 블레이드](./media/data-factory-data-processing-using-batch/image19.png)

   또한 system-0.log에서 시스템 오류 메시지 및 예외를 확인합니다.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Zip 파일에 **PDB** 파일을 포함하여 오류 발생 시 오류 세부 정보에 호출 스택과 같은 정보가 포함되도록 합니다.

1. 사용자 지정 작업에 대한 zip 파일의 모든 파일은 하위 폴더가 없는 최상위여야 합니다.

   ![사용자 지정 작업 zip 파일 목록](./media/data-factory-data-processing-using-batch/image20.png)

1. **assemblyName**(MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile**(customactivitycontainer/MyDotNetActivity.zip) 및 **packageLinkedService**(zip 파일을 포함하는 Blob Storage를 가리켜야 함)가 올바른 값으로 설정되었는지 확인합니다.

1. 오류를 해결했고 조각을 다시 처리하려면 **OutputDataset** 블레이드에서 조각을 마우스 오른쪽 단추로 클릭하고 **실행**을 선택합니다.

   ![OutputDataset 블레이드 실행 옵션](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > 컨테이너는 `adfjobs`라는 Blob Storage에 있습니다. 이 컨테이너는 자동으로 삭제되지 않지만 솔루션 테스트를 마친 후 안전하게 삭제할 수 있습니다. 마찬가지로 Data Factory 솔루션은 `adf-\<pool ID/name\>:job-0000000001`이라는 Batch 작업을 만듭니다. 원하는 경우 솔루션을 테스트한 후 이 작업을 삭제할 수 있습니다.
   >
   >
1. 사용자 지정 작업은 패키지에서 **app.config** 파일을 사용하지 않습니다. 따라서 코드가 구성 파일에서 연결 문자열을 읽는 경우 런타임 시 작동하지 않습니다. Batch를 사용하는 경우 모범 사례는 Azure Key Vault에서 암호를 보유하는 것입니다. 그런 다음 인증서 기반 서비스 주체를 사용하여 키 자격 증명 모음을 보호하고 인증서를 Batch 풀에 배포합니다. .NET 사용자 지정 활동은 런타임에 키 자격 증명 모음의 암호에 액세스할 수 있습니다. 이 일반 솔루션은 연결 문자열뿐 아니라 모든 형식의 암호로 확장될 수 있습니다.

    쉬운 해결 방법이 있지만 가장 좋은 방법은 아닙니다. 연결 문자열 설정을 사용 하 여 SQL Database 연결 된 서비스를 만들 수 있습니다. 그런 다음, 연결된 서비스를 사용하는 데이터 세트를 만들고 사용자 지정 .NET 작업에 더미 입력 데이터 세트로 데이터 세트를 연결할 수 있습니다. 그런 다음 사용자 지정 활동 코드에서 연결된 서비스의 연결 문자열에 액세스할 수 있습니다. 런타임 시 제대로 작동해야 합니다.  

#### <a name="extend-the-sample"></a>샘플 확장
Data Factory 및 Batch 기능에 대한 자세한 내용을 보려면 이 샘플을 확장할 수 있습니다. 예를 들어 서로 다른 시간 범위에서 조각을 처리하려면 다음 단계를 수행합니다.

1. 다음 하위 폴더를 `inputfolder`에 추가합니다. 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 및 2015-11-16-09. 해당 폴더에 입력 파일을 배치합니다. `2015-11-16T05:00:00Z`에서 `2015-11-16T10:00:00Z`(으)로 파이프라인에 대한 종료 시간을 변경합니다. **다이어그램** 보기에서 **InputDataset**을 두 번 클릭하고 입력 조각이 준비되었는지 확인합니다. **OutputDataset**을 두 번 클릭하여 출력 조각의 상태를 봅니다. **준비** 상태에 있는 경우 출력 파일에 대한 출력 폴더를 확인합니다.

1. 솔루션, 특히 Batch에서 발생하는 처리의 성능에 미치는 영향을 이해하려면 **동시성** 설정을 증가 또는 감소시킵니다. **동시성** 설정에 대한 자세한 내용은 “4단계: 사용자 지정 작업을 사용하여 파이프라인 만들기 및 실행”을 참조하세요.

1. 상위/하위 **VM당 최대 작업**을 가진 풀을 만듭니다. 만든 새 풀을 사용하려면 Data Factory 솔루션에서 Batch 연결된 서비스를 업데이트합니다. **VM당 최대 작업** 설정에 대한 자세한 내용은 “4단계: 사용자 지정 작업을 사용하여 파이프라인 만들기 및 실행”을 참조하세요.

1. **자동 크기 조정** 기능이 있는 Batch 풀을 만듭니다. Batch 풀에서 자동으로 컴퓨팅 노드 크기를 조정하는 것은 애플리케이션에서 사용하는 처리 능력을 동적으로 조정하는 것입니다.

    샘플 수식은 다음과 같은 동작을 수행합니다. 풀이 처음으로 만들어질 때 하나의 VM을 시작합니다. $PendingTasks 메트릭은 실행 중이거나 큐에 대기 중인 활성 상태의 작업 수를 정의합니다. 이 수식은 지난 180초 동안에서 보류 중인 작업의 평균 수를 찾은 후 그에 따라 TargetDedicated를 설정합니다. 또한 TargetDedicated가 25개의 VM을 초과하지 않도록 합니다. 새 작업이 제출되면 풀은 자동으로 증가합니다. 작업이 완료되면 VM은 하나씩 비워지고 자동 크기 조정은 해당 VM을 축소합니다. startingNumberOfVMs 및 maxNumberofVMs을 요구에 맞게 조정할 수 있습니다.

    자동 크기 조정 수식:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   자세한 내용은 [Batch 풀에서 자동으로 컴퓨팅 노드 크기 조정](../../batch/batch-automatic-scaling.md)을 참조하세요.

   풀이 기본 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)을 사용하는 경우 Batch 서비스가 사용자 지정 작업을 실행하기 전에 VM을 준비하는 데 15~30분이 소요될 수 있습니다. 풀이 다른 autoScaleEvaluationInterval을 사용하는 경우 Batch 서비스는 autoScaleEvaluationInterval +10분이 소요될 수 있습니다.

1. 샘플 솔루션에서 **Execute** 메서드는 출력 데이터 조각을 생성하도록 입력 데이터 조각을 처리하는 **Calculate** 메서드를 호출합니다. 사용자 고유 메서드를 작성하여 입력 데이터를 처리하고 **Execute** 메서드의 **Calculate** 메서드 호출을 사용자 메서드에 호출로 대체할 수 있습니다.

### <a name="next-steps-consume-the-data"></a>다음 단계: 데이터 사용
데이터를 처리한 후 Power BI와 같은 온라인 도구에서 사용할 수 있습니다. 다음은 Power BI 및 Azure에서 사용하는 방법을 이해하는 데 도움을 주는 링크입니다.

* [Power BI에서 데이터 세트 탐색](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Power BI Desktop 시작](https://docs.microsoft.com/power-bi/fundamentals/desktop-getting-started)
* [Power BI에서 데이터 새로 고침](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure 및 Power BI: 기본 개요](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>참조
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Data Factory 서비스 소개](data-factory-introduction.md)
  * [Data Factory 시작](data-factory-build-your-first-pipeline.md)
  * [Data Factory 파이프라인에서 사용자 지정 작업 사용](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Batch의 기본 사항](../../azure-sql/database/sql-database-paas-overview.md)
  * [일괄 처리 기능 개요](../../batch/batch-service-workflow-features.md)
  * [Azure Portal에서 Batch 계정 만들기 및 관리](../../batch/batch-account-create-portal.md)
  * [.NET용 Batch 클라이언트 라이브러리 시작](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

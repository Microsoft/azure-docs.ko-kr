---
title: Azure Data Factory 파이프라인에서 사용자 지정 작업 사용
description: 사용자 지정 작업을 만들고 Azure Data Factory 파이프라인에서 사용하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 0ddc235064d99e9d6385ab48e78f893952eefa15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61254647"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Azure Data Factory 파이프라인에서 사용자 지정 작업 사용
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-use-custom-activities.md)
> * [버전 2(현재 버전)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 사용자 지정 작업](../transform-data-using-dotnet-custom-activity.md)을 참조하세요.

Azure Data Factory 파이프라인에서 사용할 수 있는 두 가지 작업 유형이 있습니다.

- [데이터 이동 작업](data-factory-data-movement-activities.md)은 [지원되는 원본 및 싱크 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 간에 데이터를 이동하는 작업입니다.
- [데이터 변환 작업](data-factory-data-transformation-activities.md)은 Azure HDInsight, Azure Batch, Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 변환하는 작업입니다.

Data Factory에서 지원되지 않는 데이터 저장소에서 다른 위치로 또는 그 반대로 데이터를 이동하려면 고유의 데이터 이동 논리가 포함된 **사용자 지정 작업**을 만들어서 파이프라인에 해당 작업을 사용합니다. 마찬가지로, Data Factory에서 지원되지 않는 방식으로 데이터를 변환/처리하려면 고유의 데이터 변환 논리가 포함된 사용자 지정 작업을 만들어서 파이프라인에 해당 작업을 사용합니다.

가상 머신의 **Azure Batch**풀에서 실행할 사용자 지정 작업을 구성할 수 있습니다. Azure Batch를 사용할 때는 기존 Azure Batch 풀만 사용할 수 있습니다.

다음 연습에서는 사용자 지정 .NET 작업을 만들어서 해당 사용자 작업을 파이프라인에 사용하는 단계별 지침을 제공합니다. 이 연습에서는 **Azure Batch** 연결된 서비스를 사용합니다.

> [!IMPORTANT]
> - 사용자 지정 작업에서 데이터 관리 게이트웨이를 사용하여 온-프레미스 데이터 원본에 액세스할 수는 없습니다. 현재 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)에서는 Data Factory의 복사 작업 및 저장 프로시저 작업만 지원합니다.

## <a name="walkthrough-create-a-custom-activity"></a>연습: 사용자 지정 작업 만들기
### <a name="prerequisites"></a>필수 조건
* Visual Studio 2012/2013/2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Azure Batch 필수 조건
이 연습에서는 Azure Batch를 계산 리소스로 사용하여 사용자 지정 .NET 작업을 실행할 것입니다. **Azure Batch**는 클라우드에서 대규모 병렬 및 HPC(고성능 컴퓨팅) 애플리케이션을 효율적으로 실행하기 위한 플랫폼 서비스입니다. Azure Batch는 **가상 머신의 관리되는 컬렉션**에서 실행되는 계산 집약적 작업을 예약하고, 작업 요구에 맞게 계산 리소스의 규모를 자동으로 조정할 수 있습니다. Azure Batch 서비스의 개요에 대한 자세한 내용은 [Azure Batch 기본 사항][batch-technical-overview] 문서를 참조하세요.

자습서를 위해 VM 풀과 함께 Azure Batch 계정을 만듭니다. 단계는 다음과 같습니다.

1. **Azure Portal** 을 사용하여 [Azure Batch 계정](https://portal.azure.com)을 만듭니다. 지침은 [Azure Batch 계정 만들기 및 관리][batch-create-account] 문서를 참조하세요.
2. Azure Batch 계정 이름, 계정 키, URI 및 풀 이름을 적어둡니다. Azure Batch 연결된 서비스를 만드는 데 필요합니다.
    1. Azure Batch 계정의 홈 페이지에서 `https://myaccount.westus.batch.azure.com` 형식의 **URL**이 표시됩니다. 이 예제에서 **myaccount**는 Azure Batch 계정 이름입니다. 연결된 서비스 정의에서 사용하는 URI는 계정 이름이 없는 URL입니다. 예: `https://<region>.batch.azure.com`
    2. 왼쪽 메뉴에서 **키**를 클릭하고 **기본 액세스 키**를 복사합니다.
    3. 기존 풀을 사용하려면 메뉴에서 **풀**을 클릭하고 풀의 **ID**를 메모해둡니다. 기존 풀이 없는 경우 다음 단계로 이동합니다.
2. **Azure Batch 풀**을 만듭니다.

   1. [Azure Portal](https://portal.azure.com)에서 왼쪽 메뉴의 **찾아보기**, **Batch 계정**을 차례로 클릭합니다.
   2. Azure Batch 계정을 선택하여 **Batch 계정** 블레이드를 엽니다.
   3. **풀** 타일을 클릭합니다.
   4. **풀** 블레이드에서 도구 모음의 추가 단추를 클릭하여 풀을 추가합니다.
      1. 풀에 대한 ID(풀 ID)를 입력합니다. Data Factory 솔루션을 만들 때 필요하므로 **풀의 ID**를 메모해둡니다.
      2. 운영 체제 제품군 설정에 **Windows Server 2012 R2** 를 지정합니다.
      3. **노드 가격 책정 계층**을 선택합니다.
      4. **대상 전용** 설정 값으로 **2**를 입력합니다.
      5. **노드당 최대 작업** 설정 값으로 **2**를 입력합니다.
   5. **확인** 을 클릭하여 풀을 만듭니다.
   6. 풀의 **ID**를 메모해둡니다

### <a name="high-level-steps"></a>대략적인 단계
이 연습의 일환으로 수행하는 두 가지 대략적인 단계는 다음과 같습니다.

1. 간단한 데이터 변환/처리 논리가 포함된 사용자 지정 작업을 만듭니다.
2. 사용자 지정 작업을 사용하는 파이프라인으로 Azure Data Factory를 만듭니다.

### <a name="create-a-custom-activity"></a>사용자 지정 작업 만들기
.NET 사용자 지정 작업을 만들려면 **IDotNetActivity** 인터페이스를 구현하는 클래스를 사용하는 **.NET 클래스 라이브러리** 프로젝트를 만들어야 합니다. 이 인터페이스는 [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx)라는 메서드 하나만 포함하며 서명은 다음과 같습니다.

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

이 메서드는 다음과 같은 네 개의 매개 변수를 사용합니다.

- **linkedServices**. 이 속성은 작업에 대한 입력/출력으로 참조되는 데이터 저장소 연결된 서비스의 열거형 목록입니다.
- **데이터 세트**. 이 속성은 작업에 대한 입력/출력 데이터 세트의 열거형 목록입니다. 이 매개 변수를 사용하여 입력 및 출력 데이터 세트에 정의된 위치 및 스키마를 가져올 수 있습니다.
- **activity**. 이 속성은 현재 작업을 나타냅니다. 사용자 지정 작업과 연결된 확장된 속성에 액세스하려면 사용할 수 있습니다. 자세한 내용은 [확장 속성 액세스](#access-extended-properties)를 참조하세요.
- **logger**. 이 개체를 사용하면 파이프라인에 대한 사용자 로그로 노출할 디버그 주석을 기록할 수 있습니다.

이 메서드는 나중에 사용자 지정 작업을 함께 연결하는 데 사용할 수 있는 사전을 반환합니다. 이 기능은 아직 구현되지 않았기 때문에, 메서드로부터 빈 사전이 반환됩니다.

### <a name="procedure"></a>절차
1. **.NET 클래스 라이브러리** 프로젝트를 만듭니다.
   <ol type="a">
     <li><b>Visual Studio 2017</b> 또는 <b>Visual Studio 2015</b> 또는 <b>Visual Studio 2013</b> 또는 <b>Visual Studio 2012</b>를 시작합니다.</li>
     <li><b>File</b>을 클릭하고 <b>New</b>를 가리킨 다음 <b>프로젝트</b>를 클릭합니다.</li>
     <li><b>템플릿</b>을 확장하고 <b>Visual C#</b>를 선택합니다. 이 연습에서는 C#을 사용하지만 다른 .NET 언어를 사용하여 사용자 지정 작업을 개발할 수도 있습니다.</li>
     <li>오른쪽의 프로젝트 형식 목록에서 <b>클래스 라이브러리</b>를 선택합니다. VS 2017에서 <b>클래스 라이브러리(.NET Framework)</b> 를 선택합니다.</li>
     <li><b>이름</b>에 <b>MyDotNetActivity</b>를 입력합니다.</li>
     <li><b>위치</b>에 <b>C:\ADFGetStarted</b>를 선택합니다.</li>
     <li><b>확인</b>을 클릭하여 프로젝트를 만듭니다.</li>
   </ol>

2. **도구**를 클릭하고 **NuGet 패키지 관리자**를 가리킨 다음 **패키지 관리자 콘솔**을 클릭합니다.

3. 패키지 관리자 콘솔에서 다음 명령을 실행하여 **Microsoft.Azure.Management.DataFactories**를 가져옵니다.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. **Azure Storage** NuGet 패키지를 프로젝트로 가져옵니다.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory 서비스 시작 관리자에는 4.3 버전의 WindowsAzure.Storage가 필요합니다. 사용자 지정 작업 프로젝트에서 이후 버전의 Azure Storage 어셈블리에 대한 참조를 추가한 경우 작업을 실행할 때 오류가 표시됩니다. 이 오류를 해결하려면 [Appdomain 격리](#appdomain-isolation) 섹션을 참조하세요.
5. 다음 **using** 문을 프로젝트의 원본 파일에 추가합니다.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. **namespace**의 이름을 **MyDotNetActivityNS**로 변경합니다.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. 클래스 이름을 **MyDotNetActivity**로 변경하고 다음 코드 조각에서 보여 주듯이 **IDotNetActivity** 인터페이스에서 해당 클래스를 파생합니다.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. **IDotNetActivity** 인터페이스의 **Execute** 메서드를 **MyDotNetActivity** 클래스에 구현(추가)하고 다음 샘플 코드를 메서드에 복사합니다.

    다음 샘플은 데이터 조각과 연결된 각 Blob의 검색 단어("Microsoft") 발생 횟수를 계산합니다.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
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

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. 다음과 같은 도우미 메서드를 추가합니다.

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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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

    GetFolderPath 메서드는 데이터 세트가 가리키는 폴더로 경로를 반환하고 GetFileName 메서드는 데이터 세트가 가리키는 BLOB/파일의 이름을 반환합니다. {Year}, {Month}, {Day} 등의 변수를 사용하여 folderPath를 정의하면 이 메서드는 변수를 런타임 값으로 바꾸지 않고 문자열을 있는 그대로 반환합니다. SliceStart, SliceEnd 등에 대한 액세스에 대해 자세히 알아보려면 [확장 속성 액세스](#access-extended-properties) 를 참조하세요.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Calculate 메서드는 입력 파일(폴더에서 BLOB)에서 Microsoft 키워드의 인스턴스 수를 계산합니다. 검색 용어("Microsoft")는 코드에 하드 코딩됩니다.
10. 프로젝트를 컴파일합니다. 메뉴에서 **빌드**, **솔루션 빌드**를 차례로 클릭합니다.

    > [!IMPORTANT]
    > 4.5.2 버전의 .NET Framework를 프로젝트의 대상 프레임워크로 설정합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭하여 대상 프레임워크를 설정합니다. 데이터 팩터리는 .NET Framework 4.5.2 이후 버전에 대해 컴파일된 사용자 지정 작업을 지원하지 않습니다.

11. **Windows 탐색기**를 시작하고 빌드 유형에 따라 **bin\debug** 또는 **bin\release** 폴더로 이동합니다.
12. \<project folder\>\bin\Debug 폴더의 이진을 모두 포함하는 **MyDotNetActivity.zip** Zip 파일을 만듭니다. 오류가 있는 경우 문제를 발생시킨 소스 코드의 줄 번호 같은 추가 정보를 받을 수 있도록 **MyDotNetActivity.pdb** 파일을 포함합니다.

    > [!IMPORTANT]
    > 사용자 지정 작업에 대한 zip 파일의 모든 파일은 하위 폴더가 없는 **최상위** 여야 합니다.

    ![이진 출력 파일](./media/data-factory-use-custom-activities/Binaries.png)
14. 명명된 Blob 컨테이너 **customactivitycontainer**가 아직 없는 경우 새로 만듭니다.
15. MyDotNetActivity.zip을 AzureStorageLinkedService에서 참조되는 **범용** Azure Blob Storage(핫/쿨 Blob Storage 아님)의 customactivitycontainer에 Blob으로 업로드합니다.

> [!IMPORTANT]
> Data Factory 프로젝트를 포함하는 Visual Studio의 솔루션에 이 .NET 작업 프로젝트를 추가하고 Data Factory 애플리케이션 프로젝트의 .NET 작업 프로젝트에 참조를 추가하는 경우에는 zip 파일을 만들고 범용 Azure Blob Storage에 업로드하는 마지막 두 단계를 수행할 필요가 없습니다. Visual Studio를 사용하여 데이터 팩터리 엔터티를 게시하면 이러한 단계가 게시 프로세스에서 자동으로 수행됩니다. 자세한 내용은 [Visual Studio의 데이터 팩터리 프로젝트](#data-factory-project-in-visual-studio) 섹션을 참조하세요.

## <a name="create-a-pipeline-with-custom-activity"></a>사용자 지정 작업을 포함하는 파이프라인 만들기
사용자 지정 작업을 만들어 이진 파일이 있는 zip 파일을 **범용** Azure Storage 계정의 Blob 컨테이너에 업로드했습니다. 이 섹션에서는 사용자 지정 작업을 사용하는 파이프라인으로 Azure Data Factory를 만듭니다.

사용자 지정 작업에 대한 입력 데이터 세트는 Blob Storage에서 adftutorial 컨테이너의 customactivityinput 폴더에 있는 Blob(파일)을 나타냅니다. 작업에 대한 출력 데이터 세트는 Blob Storage에서 adftutorial 컨테이너의 customactivityoutput 폴더에 있는 출력 Blob을 나타냅니다.

다음 내용이 포함된 **file.txt** 파일을 만들고 **adftutorial** 컨테이너의 **customactivityinput** 폴더에 업로드합니다. 아직 없는 경우 adftutorial 컨테이너를 만듭니다.

```
test custom activity Microsoft test custom activity Microsoft
```

입력 폴더는 폴더에 2개 이상의 파일이 포함된 경우에도 Azure Data Factory의 조각에 해당합니다. 각 조각이 파이프라인으로 처리될 때 사용자 지정 작업은 해당 조각에 대한 입력 폴더에서 모든 BLOB를 반복합니다.

adftutorial\customactivityoutput 폴더에 1개 이상의 줄(입력 폴더에서 Blob 수와 동일)이 포함된 하나의 출력 파일이 표시됩니다.

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


이 섹션에서 수행하는 단계는 다음과 같습니다.

1. **데이터 팩터리**를 만듭니다.
2. 사용자 지정 작업이 실행되는 VM의 Azure Batch 풀과 입/출력 Blob을 보유하는 Azure Storage에 대한 **연결된 서비스**를 만듭니다.
3. 사용자 지정 작업의 입력 및 출력을 나타내는 입력 및 출력 **데이터 세트**를 만듭니다.
4. 사용자 지정 작업을 사용하는 **파이프라인**을 만듭니다.

> [!NOTE]
> 아직 **file.txt** 을 만들어서 Blob 컨테이너에 업로드하지 않았으면 지금 합니다. 이전 섹션의 지침을 참조하세요.

### <a name="step-1-create-the-data-factory"></a>1단계: 데이터 팩터리 만들기
1. Azure Portal에 로그인한 후에 다음 단계를 수행합니다.
   1. 왼쪽 창에서 **리소스 만들기**를 클릭합니다.
   2. **새** 블레이드에서 **데이터 + 분석**을 클릭합니다.
   3. **데이터 분석** 블레이드에서 **Data Factory**를 클릭합니다.

      ![새 Azure Data Factory 메뉴](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. **새 Data Factory** 블레이드에서 이름으로 **CustomActivityFactory**를 입력합니다. Azure Data Factory 이름은 전역적으로 고유해야 합니다. 만약 **"CustomActivityFactory" Data Factory 이름은 사용할 수 없습니다**라는 오류 메시지가 표시되면 데이터 팩터리 이름을 변경하고(예: **yournameCustomActivityFactory**) 다시 만들어 봅니다.

    ![새 Azure Data Factory 블레이드](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. **리소스 그룹 이름**을 클릭하여 기존 리소스 그룹을 선택하거나 리소스 그룹을 만듭니다.
4. **subscription** 및 Data Factory를 만들려는 **region**을 제대로 사용하고 있는지 확인합니다.
5. **새 Data Factory** 블레이드에서 **만들기**를 클릭합니다.
6. Azure 포털의 **대시보드** 에 생성된 데이터 팩터리가 표시됩니다.
7. 데이터 팩터리 만들기를 완료한 후에는 Data Factory 블레이드가 표시되며 여기에 데이터 팩터리의 내용이 표시됩니다.

    ![데이터 팩터리 블레이드](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>2단계: 연결된 서비스 만들기
연결된 서비스는 데이터 저장소 또는 컴퓨팅 서비스를 Azure Data Factory에 연결합니다. 이 단계에서는 Azure Storage 계정 및 Azure Batch 계정을 데이터 팩터리에 연결합니다.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage 연결된 서비스 만들기
1. **CustomActivityFactory**에 대한 **Data Factory** 블레이드에서 **작성 및 배포 타일**을 클릭합니다. 데이터 팩터리 편집기가 표시됩니다.
2. 명령 모음에서 **새 데이터 저장소**를 클릭하고 **Azure 저장소**를 선택합니다. 편집기에 Azure Storage 연결된 서비스를 만들기 위한 JSON 스크립트가 표시됩니다.

    ![새 데이터 저장소 - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. `<accountname>`을 Azure Storage 계정 이름으로 바꾸고 `<accountkey>`를 Azure Storage 계정의 액세스 키로 바꿉니다. 저장소 액세스 키를 확보하는 방법을 알아보려면 [저장소 액세스 키 보기, 복사 및 다시 생성](../../storage/common/storage-account-manage.md#access-keys)을 참조하세요.

    ![Azure Storage 연결 서비스](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

#### <a name="create-azure-batch-linked-service"></a>Azure Batch 연결된 서비스 만들기
1. Data Factory Editor의 도구 모음에서 **... 추가**를 클릭하고 **새 계산**을 클릭한 다음, 메뉴에서 **Azure Batch**를 선택합니다.

    ![새 계산 - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. JSON 스크립트를 다음과 같이 변경합니다.

   1. **accountName** 속성의 Azure Batch 계정 이름을 지정합니다. **Azure Batch 계정 블레이드**의 **URL**은 `http://accountname.region.batch.azure.com` 형식을 사용합니다. JSON의 **batchUri** 속성에 대해 URL에서 `accountname.`을 제거하고 `accountName` JSON 속성에 대해 `accountname`을 사용합니다.
   2. **accessKey** 속성에 대한 Azure Batch 계정 키를 지정합니다.
   3. **poolName** 속성에 대한 필수 조건의 일부로 만든 풀의 이름을 지정합니다. 풀 이름 대신 풀 ID를 지정할 수도 있습니다.
   4. **batchUri** 속성에 대한 Azure Batch URI를 지정합니다. 예: `https://westus.batch.azure.com`.
   5. **AzureStorageLinkedService** for the **linkedServiceName** 속성의 Azure 배치 계정 이름을 지정합니다.

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       **poolName** 속성의 경우 풀 이름 대신 풀 ID를 지정할 수도 있습니다.

### <a name="step-3-create-datasets"></a>3단계: 데이터 세트 만들기
이 단계에서는 입력 및 출력 데이터를 나타낼 데이터 세트를 만듭니다.

#### <a name="create-input-dataset"></a>입력 데이터 세트 만들기
1. Data Factor의 **편집기**에서 **... 추가**를 클릭하고 **새 데이터 세트**를 클릭하고 **Azure Blob Storage**를 선택합니다.
2. 오른쪽 창의 JSON을 다음 JSON 코드 조각으로 바꿉니다.

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
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

   이 연습의 뒷부분에서는 파이프라인을 만드는데, 시작 시간은 2016-11-16T00:00:00Z이고 종료 시간은 2016-11-16T05:00:00Z입니다. 매시간 데이터를 생성하도록 예약되어 있어 5개의 입/출력 조각이 있습니다(**00**:00:00 -> **05**:00:00 범위).

   입력 데이터 세트의 **frequency** 및 **interval**은 **Hour** 및 **1**로 설정되며 이는 입력 조각이 매시간 제공됨을 의미합니다. 이 샘플에서는 intputfolder에서와 동일한 파일(file.txt)입니다.

   다음은 각 조각에 대한 시작 시간이며 위의 JSON 코드 조각에서 SliceStart 시스템 변수로 표현됩니다.
3. 도구 모음에서 **배포**를 클릭하여 **InputDataset**을 만들고 배포합니다. 편집기의 제목 표시줄에 **테이블이 성공적으로 생성됨** 메시지가 표시되는지 확인합니다.

#### <a name="create-an-output-dataset"></a>출력 데이터 세트 만들기
1. **데이터 팩터리 편집기**의 명령 모음에서 **... 추가**를 클릭하고 **새 데이터 세트**를 클릭한 다음, **Azure Blob Storage**를 선택합니다.
2. 오른쪽 창의 JSON 스크립트를 다음 JSON 스크립트로 바꿉니다.

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     출력 위치는 **adftutorial/customactivityoutput/** 이고 출력 파일 이름은 yyyy-MM-dd-HH.txt입니다. 여기서 yyyy-MM-dd-HH는 조각이 생성되는 년, 월, 일 및 시입니다. 자세한 내용은 [개발자 참조][adf-developer-reference](영문)를 참조하세요.

    각 입력 조각에 대해 출력 BLOB/파일이 생성됩니다. 각 조각에 대해 출력 파일의 이름을 지정하는 방법은 다음과 같습니다. 출력 파일은 모두 **adftutorial\customactivityoutput**이라는 하나의 출력 폴더에 생성됩니다.

   | 조각 | 시작 시간 | 출력 파일 |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    입력 폴더에 있는 모든 파일은 위에 언급된 시작 시간의 조각 중 일부입니다. 이 조각을 처리할 때 사용자 지정 작업은 각 파일을 검색하고 검색 용어("Microsoft") 항목 수와 함께 출력 파일에 줄을 생성합니다. inputfolder에 세 개의 파일이 있는 경우 출력 파일에 각 시간별 조각에 해당하는 세 개의 줄이 생깁니다. 예: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt 등.
3. **OutputDataset**을 배포하려면 명령 모음에서 **배포**를 클릭합니다.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>사용자 지정 작업을 사용하는 파이프라인 만들기 및 실행
1. Data Factory Editor의 도구 모음에서 **... 추가**를 클릭한 다음 명령 모음에서 **새 파이프라인**을 클릭합니다.
2. 오른쪽 창의 JSON을 다음 JSON 스크립트로 바꿉니다.

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    다음 사항에 유의하세요.

   * Azure Batch 풀의 VM 2대에서 2개 조각을 동시에 처리하도록 **동시성**이 **2**로 설정됩니다.
   * 작업 섹션에는 작업이 하나 있고 유형은 **DotNetActivity**입니다.
   * **AssemblyName**은 DLL 이름인 **MyDotnetActivity.dll**로 설정됩니다.
   * **EntryPoint**는 **MyDotNetActivityNS.MyDotNetActivity**로 설정합니다.
   * **PackageLinkedService**는 사용자 지정 작업 Zip 파일을 포함하는 Blob 스토리지를 가리키는 **AzureStorageLinkedService**로 설정합니다. 입/출력 파일 및 사용자 지정 작업 zip 파일에 대해 서로 다른 Azure Storage 계정을 사용하는 경우 다른 Azure Storage 연결된 서비스를 만듭니다. 이 문서에서는 동일한 Azure Storage 계정을 사용 중이라고 가정합니다.
   * **PackageFile**은 **customactivitycontainer/MyDotNetActivity.zip**으로 설정합니다. containerforthezip/nameofthezip.zip 형식입니다.
   * 사용자 지정 작업은 입력으로 **InputDataset**, 출력으로 **OutputDataset**을 사용합니다.
   * 사용자 지정 활동의 linkedServiceName 속성은 **AzureBatchLinkedService**를 가리키며 Azure Data Factory에 사용자 지정 작업을 Azure 배치 VM에서 실행해야 함을 알려줍니다.
   * **isPaused** 속성은 기본적으로 **false**로 설정합니다. 이 예제에서는 조각이 이전에 시작되므로 파이프라인이 즉시 실행됩니다. 파이프라인을 일시 중지하려면 이 속성을 true로 설정하고 다시 시작하려면 false로 다시 설정할 수 있습니다.
   * **start** 시간과 **end** 시간은 **5**시간 차이이며 파이프라인이 작동하면서 매시간 5개 조각이 생성됩니다.
3. 파이프라인을 배포하려면 명령 모음에서 **배포**를 클릭합니다.

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링
1. Azure 포털의 Data Factory 블레이드에서 **다이어그램**을 클릭합니다.

    ![다이어그램 타일](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. 다이어그램 뷰에서 OutputDataset을 클릭합니다.

    ![다이어그램 뷰](./media/data-factory-use-custom-activities/diagram.png)
3. 5개의 출력 조각이 준비 상태로 표시됩니다. 준비 상태가 아닌 경우 아직 생성되지 않았습니다.

   ![출력 분할](./media/data-factory-use-custom-activities/OutputSlices.png)
4. 출력 파일이 **adftutorial** 컨테이너의 Blob Storage에 생성되었는지 확인합니다.

   ![사용자 지정 작업의 출력][image-data-factory-output-from-custom-activity]
5. 출력 파일을 열면 다음과 유사한 출력이 표시됩니다.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. [Azure 포털][azure-preview-portal] 또는 Azure PowerShell cmdlet을 사용하여 데이터 팩터리, 파이프라인 및 데이터 집합을 모니터링합니다. 포털을 통해서나 cmdlet을 사용하여 다운로드할 수 있는 로그(특히 user-0.log)에 있는 사용자 지정 활동의 코드에서 **ActivityLogger** 의 메시지를 확인할 수 있습니다.

   ![사용자 지정 작업의 로그 다운로드][image-data-factory-download-logs-from-custom-activity]

데이터 세트 및 파이프라인 모니터링에 대한 자세한 단계는 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 를 참조하세요.

## <a name="data-factory-project-in-visual-studio"></a>Visual Studio의 데이터 팩터리 프로젝트
Azure Portal 대신 Visual Studio를 사용하여 데이터 팩터리 엔터티를 만들고 게시할 수 있습니다. Visual Studio를 사용하여 데이터 팩터리 엔터티를 만들고 게시하는 방법에 대한 자세한 내용은 [Visual Studio를 사용하여 첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline-using-vs.md) 및 [Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-activity-tutorial-using-visual-studio.md) 문서를 참조하세요.

Visual Studio에서 데이터 팩터리 프로젝트를 만드는 경우 다음과 같은 추가 단계를 수행합니다.

1. 사용자 지정 작업 프로젝트가 포함된 Visual Studio 솔루션에 데이터 팩터리 프로젝트를 추가합니다.
2. 데이터 팩터리 프로젝트에서 .NET 작업 프로젝트에 대한 참조를 추가합니다. 데이터 팩터리 프로젝트를 마우스 오른쪽 단추로 클릭하고, **추가**를 가리킨 다음 **참조**를 클릭합니다.
3. **참조 추가** 대화 상자에서 **MyDotNetActivity** 프로젝트를 선택하고 **확인**을 클릭합니다.
4. 솔루션을 빌드하여 게시합니다.

    > [!IMPORTANT]
    > 데이터 팩터리 엔터티를 게시하면 zip 파일이 자동으로 생성되어 Blob 컨테이너 customactivitycontainer에 업로드됩니다. Blob 컨테이너가 없으면 자동으로 생성됩니다.

## <a name="data-factory-and-batch-integration"></a>Data Factory 및 Batch 통합
Data Factory 서비스가 Azure Batch에 **adf-poolname:job-xxx**라는 이름으로 작업을 만듭니다. 왼쪽 메뉴에서 **작업**을 클릭합니다.

![Azure Data Factory - Batch 작업](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

조각의 각 작업 실행에 대한 작업(task)이 만들어집니다. 처리를 위해 준비된 5개 조각이 있는 경우 이 작업(job)에 5개 작업(task)이 만들어집니다. Batch 풀에 여러 계산 노드가 있는 경우 두 개 이상의 조각을 병렬로 실행할 수 있습니다. 계산 노드당 최대 작업이 1보다 크게 설정된 경우에도 동일한 계산에 실행 중인 두 개 이상의 조각을 포함할 수 있습니다.

![Azure Data Factory - Batch 작업 태스크](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

다음 다이어그램에서는 Azure Data Factory 및 Batch 작업 간의 관계를 보여 줍니다.

![데이터 팩터리 및 Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>오류 문제 해결
문제 해결은 몇 가지 기본적인 방법으로 구성됩니다.

1. 다음과 같은 오류가 표시되면 범용 Azure Blob Storage를 사용하는 대신 핫/쿨 Blob Storage를 사용할 수 있습니다. zip 파일을 **범용 Azure Storage 계정**에 업로드합니다.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. 다음과 같은 오류가 나타나면 CS 파일에서 클래스 이름이 파이프라인 JSON에서 **EntryPoint** 속성에 대해 지정한 이름과 일치하는지 확인합니다. 이 연습에서는 클래스 이름이 MyDotNetActivity이고, JSON의 EntryPoint는 MyDotNetActivityNS.**MyDotNetActivity**입니다.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   이름이 일치하는 경우 zip 파일의 **루트 폴더** 에 모든 이진 파일이 있는지 확인합니다. 즉, zip 파일을 열 때 하위 폴더가 아닌 루트 폴더에 모든 파일이 있어야 합니다.
3. 입력 조각이 **Ready**로 설정되지 않은 경우 입력 폴더 구조가 올바르고 **file.txt**가 입력 폴더에 있는지 확인합니다.
3. 사용자 지정 작업의 **Execute** 메서드에서 **IActivityLogger** 개체를 사용하여 문제 해결에 도움이 되는 정보를 기록합니다. 기록된 메시지는 사용자 로그 파일(다음과 같은 이름의 파일 하나 또는 여러 개: user-0.log, user-1.log, user-2.log 등)에 표시됩니다.

   **OutputDataset** 블레이드에서 조각에 대한 **데이터 조각** 블레이드를 보려면 해당 조각을 클릭합니다. 해당 조각에 대한 **작업 실행** 이 표시됩니다. 해당 조각에 대한 하나의 작업 실행이 표시됩니다. 명령 모음에서 실행을 클릭하면 동일한 조각에 대해 다른 작업 실행을 시작할 수 있습니다.

   작업 실행을 클릭하면 로그 파일 목록과 함께 **작업 실행 세부 정보** 블레이드가 표시됩니다. 기록된 메시지는 user_0.log 파일에 표시됩니다. 오류가 발생하면 파이프라인/작업 JSON에서 재시도 횟수가 3으로 설정되므로 세 개의 작업 실행이 표시됩니다. 작업 실행을 클릭하면 문제 해결을 위해 검토할 수 있는 로그 파일이 표시됩니다.

   로그 파일 목록에서 **user-0.log**를 클릭합니다. 오른쪽 패널은 **IActivityLogger.Write** 메서드를 사용한 결과입니다. 일부 메시지가 보이지 않으면 user_1.log, user_2.log 등 비슷한 이름의 로그 파일이 더 있는지 확인합니다. 로그 파일이 더 없으면 마지막 메시지가 기록된 후에 코드가 실패한 것일 수 있습니다.

   또한 **system-0.log**에서 시스템 오류 메시지 및 예외를 확인합니다.
4. Zip 파일에 **PDB** 파일을 포함하여 오류 발생 시 오류 세부 정보에 **호출 스택**과 같은 정보가 포함되도록 합니다.
5. 사용자 지정 작업에 대한 zip 파일의 모든 파일은 하위 폴더가 없는 **최상위** 여야 합니다.
6. **assemblyName**(MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile**(customactivitycontainer/MyDotNetActivity.zip) 및 **packageLinkedService**(zip 파일을 포함하는 **범용** Azure Blob Storage를 가리켜야 함)가 올바른 값으로 설정되었는지 확인합니다.
7. 오류를 해결했고 조각을 다시 처리하려면 **OutputDataset** 블레이드에서 조각을 마우스 오른쪽 단추로 클릭하고 **실행**을 클릭합니다.
8. 다음 오류가 표시되면 4.3.0 이후 버전의 Azure Storage 패키지를 사용하고 있는 것입니다. Data Factory 서비스 시작 관리자에는 4.3 버전의 WindowsAzure.Storage가 필요합니다. 이후 버전의 Azure Storage 어셈블리를 사용해야 하는 경우 해결 방법은 [Appdomain 격리](#appdomain-isolation) 섹션을 참조하세요.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    4.3.0 버전의 Azure Storage 패키지를 사용할 수 있는 경우 4.3.0 이후 버전의 Azure Storage 패키지에 대한 기존 참조를 제거합니다. 그런 다음 NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    프로젝트를 빌드합니다. bin\Debug 폴더에서 4.3.0 이후 버전의 Azure.Storage 어셈블리를 삭제합니다. 이진 파일 및 PDB 파일이 포함된 zip 파일을 만듭니다. Blob 컨테이너(customactivitycontainer)에서 이전 zip 파일을 새 zip 파일로 바꿉니다. 실패한 조각을 다시 실행합니다(조각을 마우스 오른쪽 단추로 클릭하고 실행을 클릭).
8. 사용자 지정 작업은 패키지에서 **app.config** 파일을 사용하지 않습니다. 따라서 코드가 구성 파일에서 연결 문자열을 읽는 경우 런타임 시 작동하지 않습니다. Azure Batch를 사용할 경우 **Azure KeyVault**에 모든 암호를 저장하고, 인증서 기반 서비스 주체를 사용하여 **KeyVault**을 보호하고, 인증서를 Azure Batch 풀에 배포하는 것이 좋습니다. 그러면 .NET 사용자 지정 활동은 런타임에 주요 자격 증명 모음의 암호에 액세스할 수 있습니다. 이 솔루션은 일반 솔루션이며 연결 문자열뿐 아니라 모든 유형의 암호로 확장될 수 있습니다.

   최상은 아니지만 좀 더 쉬운 해결 방법이 있습니다. 즉 연결 문자열 설정을 사용하여 **Azure SQL 연결 서비스**를 만들고, 이 서비스를 사용하는 데이터 세트를 만든 다음 사용자 지정 .NET 작업에 이 데이터 세트를 더미 입력 데이터 세트로 연결하면 됩니다. 그런 다음 사용자 지정 활동 코드에서 연결된 서비스의 연결 문자열에 액세스할 수 있습니다.

## <a name="update-custom-activity"></a>사용자 지정 작업 업데이트
사용자 지정 작업의 코드를 업데이트하는 경우 코드를 작성하고 새 이진이 포함된 zip 파일을 Blob Storage로 업로드합니다.

## <a name="appdomain-isolation"></a>Appdomain 격리
Data Factory 시작 관리자에서 사용하는 어셈블리 버전의 제약을 받지 않는 사용자 지정 작업을 만드는 방법을 보여주는 [크로스 AppDomain 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample)을 참조하세요. 예: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x 등)

## <a name="access-extended-properties"></a>확장 속성 액세스
다음 샘플과 같이 작업 JSON에서 확장 속성을 선언할 수 있습니다.

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

이 예제에는 두 가지 확장 속성 **SliceStart** 및 **DataFactoryName**이 있습니다. SliceStart의 값은 SliceStart 시스템 변수를 기반으로 합니다. 지원되는 시스템 변수 목록은 [시스템 변수](data-factory-functions-variables.md) 를 참조하세요. DataFactoryName의 값은 CustomActivityFactory로 하드 코드됩니다.

**Execute** 메서드에서 이러한 확장 속성에 액세스하려면 다음 코드와 유사한 코드를 사용합니다.

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch의 자동 확장
**자동 크기 조정** 기능으로 Azure Batch 풀을 만들 수 있습니다. 예를 들어 보류 중인 작업의 수에 따라 전용 VM 0개 및 자동 크기 조정 수식을 사용하여 Azure 배치 풀을 만들 수 있습니다.

샘플 수식은 다음과 같은 동작을 수행합니다. 풀이 처음으로 만들어지면 VM 1개로 시작합니다. $PendingTasks 메트릭은 실행되거나 큐에 대기 중인 활성 상태의 작업 수를 정의합니다.  이 수식은 지난 180초 동안에서 보류 중인 작업의 평균 수를 찾은 후 그에 따라 TargetDedicated를 설정합니다. 또한 TargetDedicated가 25개의 VM을 초과하지 않도록 합니다. 따라서 새 작업이 제출되면 풀이 자동으로 커지고, 작업이 완료되면 VM은 하나씩 사용 가능한 상태로 해제된 후 자동 크기 조정에 따라 해당 VM이 축소됩니다. startingNumberOfVMs 및 maxNumberofVMs은 요구에 맞게 조정될 수 있습니다.

자동 크기 조정 수식:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

자세한 내용은 [Azure Batch 풀에서 자동으로 계산 노드 크기 조정](../../batch/batch-automatic-scaling.md)을 참조하세요.

풀에 기본 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)이 사용되는 경우, Batch 서비스가 사용자 지정 작업을 실행하기 전에 VM을 준비하는 데 15~30분이 소요될 수 있습니다.  풀에 다른 autoScaleEvaluationInterval이 사용되는 경우, Batch 서비스는 autoScaleEvaluationInterval +10분이 소요될 수 있습니다.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>.NET SDK를 사용하여 사용자 지정 작업 만들기
이 문서의 연습에서는 Azure Portal을 사용하여 사용자 지정 작업을 사용하는 파이프라인이 포함된 데이터 팩터리를 만듭니다. 다음 코드는 .NET SDK를 대신 사용하여 데이터 팩터리를 만드는 방법을 보여 줍니다. SDK를 사용하여 프로그래밍 방식으로 파이프라인을 만드는 방법에 대한 자세한 내용은 [.NET API를 사용하여 복사 작업이 포함된 파이프라인 만들기](data-factory-copy-activity-tutorial-using-dotnet-api.md) 문서를 참조하세요.

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Visual Studio에서 사용자 지정 작업 디버깅
GitHub의 [Azure Data Factory - 로컬 환경](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) 샘플은 Visual Studio 내에서 사용자 지정 .NET 작업을 디버깅할 수 있는 도구를 포함하고 있습니다.

## <a name="sample-custom-activities-on-github"></a>GitHub의 샘플 사용자 지정 작업
| 샘플 | 사용자 지정 작업의 기능 |
| --- | --- |
| [HTTP 데이터 다운로더](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Data Factory의 사용자 지정 C# 작업을 사용하여 HTTP 엔드포인트에서 Azure Blob Storage로 데이터를 다운로드합니다. |
| [Twitter 감성 분석 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Azure Machine Learning Studio 모델을 호출하고 감정 분석, 채점, 예측 등을 수행합니다. |
| [R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |R이 이미 설치된 HDInsight 클러스터에서 RScript.exe를 실행하여 R 스크립트를 호출합니다. |
| [크로스 AppDomain .NET 작업](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Data Factory 시작 관리자가 사용한 것과 다른 버전의 어셈블리를 사용합니다. |
| [Azure Analysis Services에서 모델 다시 처리](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Azure Analysis Services에서 모델을 다시 처리합니다. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

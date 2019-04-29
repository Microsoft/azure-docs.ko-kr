---
title: Media Services.NET SDK를 사용하여 자산 및 관련 엔터티 관리
description: Media Services SDK for .NET을 사용하여 자산 및 관련 엔터티를 관리하는 방법을 알아봅니다.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235428"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Media Services.NET SDK를 사용하여 자산 및 관련 엔터티 관리
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST (영문)](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

이 항목에서는 .NET를 사용하여 Azure Media Services 엔터티를 관리하는 방법을 보여 줍니다.

2017년 4월 1일부터 레코드의 총 수가 최고 할당량 미만인 경우에도 사용자 계정에 있는 90일이 지난 작업 레코드는 연결된 태스크 레코드와 함께 자동으로 삭제됩니다. 예를 들어, 2017년 4월 1일에는 계정에 있는 2016년 12월 31일 이전의 모든 작업 레코드가 자동으로 삭제됩니다. 작업/태스크 정보를 보관해야 하는 경우에는 이 항목에 설명된 코드를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 

## <a name="get-an-asset-reference"></a>자산 참조 가져오기
자주 수행하는 작업은 Media Services에서 기존 자산에 대한 참조를 가져오는 것입니다. 다음 코드 예제에서는 자산 ID를 기준으로 서버 컨텍스트 개체에 대한 Assets 컬렉션에서 자산 참조를 가져오는 방법을 보여 줍니다. 다음 코드 예제에서는 Linq 쿼리를 사용하여 기존 IAsset 개체에 대한 참조를 가져옵니다.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>모든 자산 나열
저장소에 포함된 자산 수가 증가하면 자산을 나열하는 것이 좋습니다. 다음 코드 예제에서는 서버 컨텍스트 개체에 대한 Assets 컬렉션을 반복하는 방법을 보여 줍니다. 각 자산이 포함된 코드 예제에서는 일부 속성 값을 콘솔에도 씁니다. 예를 들어 각 자산에는 많은 미디어 파일이 포함될 수 있습니다. 코드 예제에서는 각 자산과 관련된 모든 파일을 씁니다.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>작업 참조 가져오기

Media Services 코드로 작업을 처리할 때 종종 ID에 따라 기존 작업에 대한 참조를 가져와야 합니다. 다음 코드 예제에서는 Jobs 컬렉션에서 IJob 개체에 대한 참조를 가져오는 방법을 보여 줍니다.

실행 시간이 긴 인코딩 작업을 시작할 때 작업 참조를 가져와야 하고 스레드에서 작업 상태를 확인해야 합니다. 이 경우 메서드가 스레드에서 반환될 때 작업에 대한 새로 고쳐진 참조를 검색해야 합니다.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>작업 및 자산 나열
중요한 관련 작업은 Media Services에서 관련 작업이 있는 자산을 나열하는 것입니다. 다음 코드 예제에서는 각 IJob 개체를 나열하는 방법을 보여 주고, 각 작업에 대해 작업 관련 속성, 모든 관련 작업, 모든 입력 자산 및 모든 출력 자산을 표시합니다. 이 예제의 코드는 다양한 다른 작업에 유용할 수 있습니다. 예를 들어 이전에 실행한 하나 이상의 인코딩 작업에서 출력 자산을 나열하려고 하면 이 코드에서는 출력 자산에 액세스하는 방법을 보여 줍니다. 출력 자산에 대한 참조가 있으면 콘텐츠를 다운로드하거나 URL을 제공하여 다른 사용자나 애플리케이션에 콘텐츠를 배달할 수 있습니다. 

자산 배달 옵션에 대한 자세한 내용은 [Media Services SDK for .NET을 사용하여 자산 배달](media-services-deliver-streaming-content.md)을 참조하세요.

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>모든 액세스 정책 나열
Media Services에서 자산 또는 해당 파일에 대한 액세스 정책을 정의할 수 있습니다. 액세스 정책은 파일 또는 자산에 대한 권한을 정의합니다(액세스 유형 및 기간). Media Services 코드에서는 일반적으로 IAccessPolicy 개체를 만들고 기존 자산에 연결하여 액세스 정책을 정의합니다. Media Services에서 자산에 직접 액세스할 수 있는 ILocator 개체를 만든 다음 이 설명서 시리즈와 함께 제공되는 Visual Studio 프로젝트에는 액세스 정책과 로케이터를 만들고 자산에 할당하는 방법을 보여 주는 몇 가지 코드 예제가 들어 있습니다.

다음 코드 예제에서는 서버의 모든 액세스 정책을 나열하는 방법과 각 액세스 정책과 관련된 권한 유형을 보여 줍니다. 액세스 정책을 보는 또 다른 유용한 방법은 서버의 모든 ILocator 개체를 나열하는 것이고, 이렇게 하면 각 로케이터에 대해 AccessPolicy 속성을 사용하여 관련 액세스 정책을 나열할 수 있습니다.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>액세스 정책 제한 

>[!NOTE]
> 다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 배치되는 로케이터에 대한 정책) 동일한 정책 ID를 사용해야 합니다. 

예를 들어, 애플리케이션에서 한 번만 실행하는 다음 코드와 일반 정책 집합을 만들 수 있습니다. 나중에 사용할 로그 파일에 ID를 기록할 수 있습니다.

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

그런 다음 다음과 같이 코드에서 기존 ID를 사용할 수 있습니다.

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>모든 로케이터 나열
로케이터는 로케이터의 관련 액세스 정책에 정의된 대로 자산에 대한 권한과 함께 자산에 액세스하는 직접 경로를 제공하는 URL입니다. 각 자산은 Locators 속성에서 자산과 연결된 ILocator 개체의 컬렉션을 포함할 수 있습니다. 서버 컨텍스트는 모든 로케이터가 포함된 Locators 컬렉션도 포함합니다.

다음 코드 예제에서는 서버의 모든 로케이터를 나열합니다. 각 로케이터에 대해 관련 자산 및 액세스 정책에 대한 ID를 표시합니다. 또한 자산에 대한 전체 경로, 권한 유형 및 만료 날짜를 표시합니다.

자산에 대한 로케이터 경로는 자산에 대한 기준 URL일 뿐입니다. 사용자 또는 애플리케이션이 이동할 수 있는 개별 파일에 대한 직접 경로를 만들려면 코드에서 로케이터 경로에 특정 파일 경로를 추가해야 합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [Media Services SDK for .NET을 사용하여 자산 배달](media-services-deliver-streaming-content.md)항목을 참조하세요.

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>대용량 엔터티 컬렉션 열거
엔터티를 쿼리할 때 한 번에 반환되는 엔터티 수는 최대 1000개입니다. 공용 REST v2에서는 쿼리 결과를 1000개로 제한하기 때문입니다. 대용량 엔터티 컬렉션을 열거할 때 Skip 및 Take를 사용해야 합니다. 

다음 함수는 제공된 Media Services 계정에서 모든 작업을 반복합니다. Media Services는 작업 컬렉션의 작업 1000개를 반환합니다. 이 함수는 Skip 및 Take를 사용하여 모든 작업이 연결되었는지 확인합니다(계정에 1000개가 넘는 작업이 있는 경우).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>자산 삭제
다음 예제에서는 자산을 삭제합니다.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>작업 삭제
작업을 삭제하려면 State 속성에 표시된 작업의 상태를 확인해야 합니다. 완료되거나 취소된 작업을 삭제할 수 있지만, 큐에 대기됨, 예약됨 또는 처리 중 등의 특정 상태에 있는 작업을 먼저 취소해야 해당 작업을 삭제할 수 있습니다.

다음 코드 예제에서는 작업 상태를 확인하고 상태가 완료됨 또는 취소됨일 때 삭제하는 방법으로 작업을 삭제하는 메서드를 보여 줍니다. 이 코드는 작업에 대한 참조를 가져오는 방법에 대한 이 항목의 이전 섹션에 따라 달라집니다. 작업 참조 가져오기

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>액세스 정책 삭제
다음 코드 예제에서는 정책 ID에 따라 액세스 정책에 대한 참조를 가져오고 정책을 삭제하는 방법을 보여 줍니다.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


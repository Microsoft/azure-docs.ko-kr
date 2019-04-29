---
title: Microsoft Azure StorSimple 데이터 관리자 작업에 .NET SDK 사용 | Microsoft Docs
description: .NET SDK를 사용하여 StorSimple 데이터 관리자 작업을 시작하는 방법에 대해 알아보기
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 80f01a926b94deebab59f8ef91bfc36a4600b5f0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632359"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>.NET SDK를 사용 하 여 데이터 변환 시작 하려면

## <a name="overview"></a>개요

이 문서에서는 StorSimple 데이터 관리자 서비스 내에서 데이터 변환 기능을 사용하여 StorSimple 디바이스 데이터를 변환하는 방법에 대해 설명합니다. 그러면 변환된 데이터는 클라우드에 있는 다른 Azure 서비스에서 사용됩니다.

두 가지 방법으로 데이터 변환 작업을 시작할 수 있습니다.

- .NET SDK 사용
- Azure Automation Runbook 사용
 
  이 문서에서는 샘플 .NET 콘솔 애플리케이션을 만들어 데이터 변환 작업을 시작하고 완료하기 위해 추적하는 방법을 자세히 설명합니다. 자동화를 통해 데이터 변환을 시작하는 방법에 대한 자세한 내용을 보려면 [Azure Automation Runbook을 사용하여 데이터 변환 작업 트리거](storsimple-data-manager-job-using-automation.md)로 이동합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목이 있어야 합니다.
*   다음을 실행하는 컴퓨터:

    - Visual Studio 2012, 2013, 2015 또는 2017.

    - Azure Powershell. [Azure Powershell을 다운로드합니다](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   리소스 그룹 내 StorSimple 데이터 관리자에 올바르게 구성된 작업 정의
*   모든 필수 dll입니다. 이러한 dll을 [GitHub 리포지토리](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)에서 다운로드합니다.
*   GitHub 리포지토리의 [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) 스크립트입니다.

## <a name="step-by-step-procedure"></a>단계별 절차

데이터 변환 작업을 시작하려면 다음 단계를 수행하여 .NET을 사용합니다.

1. 구성 매개 변수를 검색하려면 다음 단계를 수행합니다.
    1. `C:\DataTransformation` 위치에서 GitHub 리포지토리 스크립트의 `Get-ConfigurationParams.ps1`을 다운로드합니다.
    1. GitHub 리포지토리의 `Get-ConfigurationParams.ps1` 스크립트를 실행합니다. 다음 명령을 입력합니다.

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        ActiveDirectoryKey 및 AppName의 값을 전달할 수 있습니다.

2. 이 스크립트는 다음 값을 출력합니다.
    * 클라이언트 ID
    * 테넌트 ID
    * Active Directory 키(위에서 입력한 것과 동일)
    * 구독 ID

        ![구성 매개 변수 스크립트 출력](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Visual Studio 2012, 2013 또는 2015를 사용하여 C# .NET 콘솔 애플리케이션을 만듭니다.

    1. **Visual Studio 2012/2013/2015**을 실행합니다.
    1. **파일 > 새로 만들기 > 프로젝트**를 선택합니다.

        ![프로젝트 1 만들기](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. **설치됨 > 템플릿 > Visual C# > 콘솔 응용 프로그램**을 선택합니다.
    3. **이름**으로 **DataTransformationApp**을 입력합니다.
    4. **위치**로 **C:\DataTransformation**을 선택합니다.
    6. **확인**을 클릭하여 프로젝트를 만듭니다.

        ![프로젝트 2 만들기](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. 이제 만든 프로젝트에서 [dlls 폴더](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)에서 **참조**로 나타난 모든 DLL을 추가합니다. dll 파일을 추가하려면 다음 단계를 수행합니다.

   1. Visual Studio에서 **보기 > 솔루션 탐색기**로 이동합니다.
   2. 데이터 변환 앱 프로젝트의 왼쪽에 있는 화살표를 클릭합니다. **참조**를 클릭하고 **참조 추가**를 마우스 오른쪽 단추로 선택합니다.
    
       ![dll 1 추가](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. 패키지 폴더의 위치로 이동하고 모든 DLL을 선택한 다음 **추가**를 클릭한 다음 **확인**을 클릭합니다.

       ![dll 2 추가](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. 다음 코드는 데이터 변환 작업 인스턴스를 초기화합니다. **Main 메서드**에 이 코드를 추가합니다. 앞에서 가져온 대로 구성 매개 변수 값을 바꿉니다. **ResourceGroupName** 및 **ResourceName**의 값에 연결합니다. **ResourceGroupName**은 작업 정의가 구성된 StorSimple 데이터 관리자와 연결됩니다. **ResourceName**은 StorSimple 데이터 관리자 서비스의 이름입니다.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. 작업 정의를 실행해야 하는 매개 변수를 지정합니다.

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    (또는)

    런타임 시 작업 정의 매개 변수를 변경하려는 경우 다음 코드를 추가합니다.

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. 초기화 후에 작업 정의에서 다음 코드를 추가하여 데이터 변환 작업을 트리거합니다. 적절한 **작업 정의 이름**에 연결합니다.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    코드를 붙여넣은 후 솔루션을 빌드합니다. 다음은 데이터 변환 작업 인스턴스를 초기화하는 코드 조각의 스크린샷입니다.

   ![데이터 변환 작업을 초기화하는 코드 조각](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. 이 작업은 루트 디렉터리와 일치하는 데이터와 StorSimple 볼륨 내의 파일 필터를 변환하고 지정된 컨테이너/파일 공유에 배치합니다. 파일을 변환하는 경우 메시지는 컨테이너/파일 공유와 동일한 저장소 계정에 있고 작업 정의와 동일한 이름을 갖는 저장소 큐에 추가됩니다. 이 메시지를 트리거로 사용하여 파일의 추가 처리를 시작할 수 있습니다.

10. 작업이 트리거되면 다음 코드를 사용하여 완성을 위해 작업을 추적합니다. 작업을 실행하기 위해 이 코드를 반드시 추가해야 하는 것은 아닙니다.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    다음은 .NET을 사용하여 작업을 트리거하는 데 사용되는 전체 코드 샘플의 스크린샷입니다.

    ![.NET 작업을 트리거하는 전체 코드 조각](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).

---
title: .NET을 사용하여 작업 진행 상태 모니터링
description: 이벤트 처리기 코드를 사용하여 작업 진행률을 추적하고 상태 업데이트를 보내는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ee720ed6-8ce5-4434-b6d6-4df71fca224e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 73c9fa82cefc192b237e3880c1e745b2412a323a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235273"
---
# <a name="monitor-job-progress-using-net"></a>.NET을 사용하여 작업 진행 상태 모니터링 
> [!div class="op_single_selector"]
> * [포털](media-services-portal-check-job-progress.md)
> * [.NET](media-services-check-job-progress.md)
> * [REST (영문)](media-services-rest-check-job-progress.md)
> 
> 

작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. StateChanged 이벤트 처리기를 정의하거나(이 항목에서 설명된 대로) Azure Queue Storage를 사용해 Media Services 작업 알림을 모니터링하여( [이](media-services-dotnet-check-job-progress-with-queues.md) 항목에서 설명된 대로) 진행률을 확인할 수 있습니다.

## <a name="define-statechanged-event-handler-to-monitor-job-progress"></a>작업 진행 상태를 모니터링 하는 StateChanged 이벤트 처리기를 정의합니다.
다음 코드 예제는 StateChanged 이벤트 처리기를 정의합니다. 이 이벤트 처리기는 작업 진행 상태를 추적하고 상태에 따라 업데이트된 상태를 제공합니다. 또한 다음 코드는 LogJobStop 메서드를 정의합니다. 이 도우미 메서드는 오류 세부 정보를 기록합니다.

```csharp
    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);

        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("********************");
                Console.WriteLine("Job is finished.");
                Console.WriteLine("Please wait while local tasks or downloads complete...");
                Console.WriteLine("********************");
                Console.WriteLine();
                Console.WriteLine();
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
            case JobState.Error:
                // Cast sender as a job.
                IJob job = (IJob)sender;
                // Display or log error details as needed.
                LogJobStop(job.Id);
                break;
            default:
                break;
        }
    }

    private static void LogJobStop(string jobId)
    {
        StringBuilder builder = new StringBuilder();
        IJob job = GetJob(jobId);

        builder.AppendLine("\nThe job stopped due to cancellation or an error.");
        builder.AppendLine("***************************");
        builder.AppendLine("Job ID: " + job.Id);
        builder.AppendLine("Job Name: " + job.Name);
        builder.AppendLine("Job State: " + job.State.ToString());
        builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
        builder.AppendLine("Media Services account name: " + _accountName);
        builder.AppendLine("Media Services account location: " + _accountLocation);
        // Log job errors if they exist.  
        if (job.State == JobState.Error)
        {
            builder.Append("Error Details: \n");
            foreach (ITask task in job.Tasks)
            {
                foreach (ErrorDetail detail in task.ErrorDetails)
                {
                    builder.AppendLine("  Task Id: " + task.Id);
                    builder.AppendLine("    Error Code: " + detail.Code);
                    builder.AppendLine("    Error Message: " + detail.Message + "\n");
                }
            }
        }
        builder.AppendLine("***************************\n");
        // Write the output to a local file and to the console. The template 
        // for an error output file is:  JobStop-{JobId}.txt
        string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
        WriteToFile(outputFile, builder.ToString());
        Console.Write(builder.ToString());
    }

    private static string JobIdAsFileName(string jobID)
    {
        return jobID.Replace(":", "_");
    }
```


## <a name="next-step"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


---
title: Azure Blob Storage에서 변경 피드 처리 (미리 보기) | Microsoft Docs
description: .NET 클라이언트 응용 프로그램에서 변경 피드 로그를 처리 하는 방법을 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 06/18/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.custom: devx-track-csharp
ms.openlocfilehash: cc70204a56c3a853c6c6c7f0e1573c71854c0f19
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018963"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob Storage에서 변경 피드 처리 (미리 보기)

변경 피드는 저장소 계정의 blob 및 blob 메타 데이터에 발생 하는 모든 변경 내용에 대 한 트랜잭션 로그를 제공 합니다. 이 문서에서는 blob 변경 피드 프로세서 라이브러리를 사용 하 여 변경 피드 레코드를 읽는 방법을 보여 줍니다.

변경 피드에 대 한 자세한 내용은 [Azure Blob Storage에서 피드 변경 (미리 보기)](storage-blob-change-feed.md)을 참조 하세요.

> [!NOTE]
> 변경 피드는 공개 미리 보기 상태 이며 제한 된 지역에서 사용할 수 있습니다. 알려진 문제 및 제한과 함께이 기능에 대 한 자세한 내용은 [Azure Blob Storage에서 변경 피드 지원](storage-blob-change-feed.md)을 참조 하세요. 변경 피드 프로세서 라이브러리는 현재까지 변경 될 수 있으며,이 라이브러리가 일반 공급 될 때 변경 될 수 있습니다.

## <a name="get-the-blob-change-feed-processor-library"></a>Blob 변경 피드 프로세서 라이브러리 가져오기

1. 명령 창을 엽니다 (예: Windows PowerShell).
2. 프로젝트 디렉터리에서 [ **Azure.. changefeed** NuGet 패키지](https://www.nuget.org/packages/Azure.Storage.Blobs.ChangeFeed/)를 설치 합니다.

```console
dotnet add package Azure.Storage.Blobs --version 12.5.1
dotnet add package Azure.Storage.Blobs.ChangeFeed --version 12.0.0-preview.4
```
## <a name="read-records"></a>레코드 읽기

> [!NOTE]
> 변경 피드는 저장소 계정에 있는 변경할 수 없는 읽기 전용 엔터티입니다. 원하는 수의 응용 프로그램은 각자의 편의를 위해 동시에 독립적으로 변경 피드를 읽고 처리할 수 있습니다. 응용 프로그램에서 레코드를 읽을 때 레코드는 변경 피드에서 제거 되지 않습니다. 사용 되는 각 판독기의 읽기 또는 반복 상태는 응용 프로그램 에서만 독립적이 고 유지 관리 됩니다.

이 예제에서는 변경 피드의 모든 레코드를 반복 하 고, 목록에 추가한 다음 해당 목록을 호출자에 게 반환 합니다.
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

이 예제에서는 목록의 각 레코드에서 몇 가지 값을 콘솔에 출력 합니다. 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>저장 된 위치에서 레코드 읽기 재개

변경 피드에 읽기 위치를 저장 한 다음 나중에 레코드 반복을 다시 시작할 수 있습니다. 변경 피드 커서를 가져와서 읽기 위치를 저장할 수 있습니다. 커서는 **문자열** 이며 응용 프로그램은 파일 또는 데이터베이스와 같은 응용 프로그램의 디자인에 적합 한 방식으로 해당 문자열을 저장할 수 있습니다.

이 예에서는 변경 피드의 모든 레코드를 반복 하 고, 목록에 추가 하 고, 커서를 저장 합니다. 목록 및 커서가 호출자에 게 반환 됩니다. 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>레코드의 스트림 처리

변경 피드에 커밋될 때 변경 피드 레코드를 처리 하도록 선택할 수 있습니다. [사양](storage-blob-change-feed.md#specifications)을 참조 하세요. 변경 이벤트는 평균 60 초 동안 변경 피드에 게시 됩니다. 폴링 간격을 지정할 때이 기간을 사용 하 여 새로운 변경을 폴링하는 것이 좋습니다.

이 예에서는 정기적으로 변경을 폴링합니다.  변경 레코드가 있는 경우이 코드는 해당 레코드를 처리 하 고 변경 피드 커서를 저장 합니다. 이렇게 하면 프로세스가 중지 되었다가 다시 시작 되는 경우 응용 프로그램은 커서를 사용 하 여 마지막으로 중지 된 레코드의 처리를 다시 시작할 수 있습니다. 이 예제에서는 커서를 로컬 응용 프로그램 구성 파일에 저장 하지만 응용 프로그램은 시나리오에 가장 적합 한 형식으로 저장할 수 있습니다. 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>시간 범위 내에서 레코드 읽기

특정 시간 범위 내에 속하는 레코드를 읽을 수 있습니다. 이 예에서는 변경 피드의 모든 레코드를 반복 하 여 2 2020 년 3 월 일 오전 3:00에 해당 하는 변경 피드의 모든 레코드를 반복 하 고, 2:00 7 2020 AM을 목록에 추가한 다음, 해당 목록을 호출자에 게 반환 합니다.

### <a name="selecting-segments-for-a-time-range"></a>시간 범위에 대 한 세그먼트 선택

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2020, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 8, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

사용자가 제공 하는 시작 시간은 가장 가까운 시간으로 반올림 되 고 종료 시간은 가장 가까운 시간으로 반올림 됩니다. 사용자가 시작 시간 이전에 발생 한 이벤트와 종료 시간 이후에 발생 한 이벤트를 볼 수 있습니다. 또한 시작 시간과 종료 시간 사이에 발생 하는 일부 이벤트는 표시 되지 않습니다. 이벤트는 시작 시간 이전 시간 또는 종료 시간 이후의 시간 동안 기록 될 수 있기 때문입니다.

## <a name="next-steps"></a>다음 단계

변경 피드 로그에 대해 자세히 알아보세요. [Azure Blob Storage에서 피드 변경 (미리 보기)을](storage-blob-change-feed.md) 참조 하세요.

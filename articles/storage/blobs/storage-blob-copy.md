---
title: .NET-Azure Storage를 사용 하 여 blob 복사
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정에서 blob을 복사 하는 방법에 대해 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 144ad1a2f9d9b94deca1462d217aa16baca75846
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018895"
---
# <a name="copy-a-blob-with-net"></a>.NET을 사용 하 여 blob 복사

이 문서에서는 Azure Storage 계정으로 blob을 복사 하는 방법을 보여 줍니다. 또한 비동기 복사 작업을 중단 하는 방법을 보여 줍니다. 예제 코드는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 합니다.

## <a name="about-copying-blobs"></a>Blob 복사 정보

동일한 저장소 계정 내에서 blob을 복사 하는 경우 동기 작업입니다. 계정 간에 복사 하는 경우이 작업은 비동기 작업입니다. [Startcopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) 및 [startcopyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) 메서드는 상태를 확인 하거나 복사 작업을 중단 하는 데 사용 되는 복사본 ID 값을 반환 합니다.

복사 작업의 원본 blob은 블록 blob, 추가 blob, 페이지 blob 또는 스냅숏으로 지정할 수 있습니다. 대상 blob가 이미 있으면 원본 blob와 동일한 blob 유형이어야 합니다. 기존 대상 blob을 덮어씁니다.

복사 작업이 진행 되는 동안에는 대상 blob을 수정할 수 없습니다. 대상 blob는 처리되지 않은 blob 복사 작업을 하나만 포함할 수 있습니다. 즉, blob은 여러 보류 중인 복사 작업의 대상이 될 수 없습니다.

전체 원본 blob 또는 파일은 항상 복사 됩니다. 바이트 범위 또는 블록 집합은 복사할 수 없습니다.

Blob을 복사 하는 경우 해당 시스템 속성은 동일한 값을 사용 하 여 대상 blob에 복사 됩니다.

모든 blob 유형에 대해 대상 blob의 [Copystate. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) 속성을 확인 하 여 복사 작업의 상태를 가져올 수 있습니다. 복사가 완료되면 최종 blob가 커밋됩니다.

복사 작업은 다음 형식 중 하나를 사용할 수 있습니다.

- 다른 이름을 사용해서 원본 blob를 대상 blob에 복사할 수 있습니다. 대상 blob은 동일한 blob 유형 (블록, 추가 또는 페이지)의 기존 blob 이거나 복사 작업으로 만들어진 새 blob 일 수 있습니다.
- 원본 blob을 동일한 이름의 대상 blob에 복사 하 여 대상 blob을 효과적으로 바꿀 수 있습니다. 이러한 복사 작업은 커밋되지 않은 모든 블록을 제거 하 고 대상 blob의 메타 데이터를 덮어씁니다.
- Azure 파일 서비스의 원본 파일을 대상 blob에 복사할 수 있습니다. 대상 blob은 기존 블록 blob 이거나 복사 작업을 통해 생성 되는 새 블록 blob 일 수 있습니다. 파일에서 페이지 blob으로 복사 또는 blob 추가는 지원 되지 않습니다.
- 기본 Blob에 대해 스냅샷을 복사할 수 있습니다. 스냅샷의 수준을 기본 Blob 위치로 올리면 이전 Blob 버전을 복원할 수 있습니다.
- 스냅샷을 다른 이름으로 대상 Blob에 복사할 수 있습니다. 복사된 대상 Blob는 쓰기 가능한 Blob이고 스냅숏이 아닙니다.

## <a name="copy-a-blob"></a>Blob 복사

Blob을 복사 하려면 다음 메서드 중 하나를 호출 합니다.

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

다음 코드 예제에서는 이전에 만든 blob에 대 한 참조를 가져와 동일한 컨테이너의 새 blob에 복사 합니다.

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Blob 복사 작업 중단

복사 작업을 중단 하면 블록 blob, 추가 blob 및 페이지 blob에 대 한 대상 blob의 길이가 0이 됩니다. 그러나 대상 blob에 대 한 메타 데이터에는 원본 blob에서 복사 되거나 [startcopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) 또는 [startcopyasync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) 호출에서 명시적으로 설정 된 새 값이 포함 됩니다. 복사 하기 전에 원래 메타 데이터를 유지 하려면 또는를 호출 하기 전에 대상 blob의 스냅숏을 만듭니다 `StartCopy` `StartCopyAsync` .

진행 중인 blob 복사 작업을 중단 하면 대상 blob의 [Copystate. 상태가](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) [copystate](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)로 설정 됩니다.

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) 및 [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) 메서드는 진행 중인 blob 복사 작업을 취소 하 고 길이가 0이 고 전체 메타 데이터가 포함 된 대상 blob을 그대로 유지 합니다.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

다음 항목에서는 blob을 복사 하 고 Azure REST Api를 사용 하 여 진행 중인 복사 작업을 중단 하는 방법에 대해 설명 합니다.

- [Blob 복사](/rest/api/storageservices/copy-blob)
- [Blob 복사 중단](/rest/api/storageservices/abort-copy-blob)

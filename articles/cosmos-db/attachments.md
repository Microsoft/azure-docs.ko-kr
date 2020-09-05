---
title: 첨부 파일 Azure Cosmos DB
description: 이 문서에서는 Azure Cosmos DB 첨부 파일의 개요를 제공 합니다.
author: aliuy
ms.author: andrl
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/07/2020
ms.reviewer: sngun
ms.openlocfilehash: 27d9297809d2bd028918885a88faf55e8314b71d
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817721"
---
# <a name="azure-cosmos-db-attachments"></a>첨부 파일 Azure Cosmos DB

Azure Cosmos DB 첨부 파일은 외부 blob 또는 미디어 파일을 사용 하 여 연결 된 메타 데이터에 대 한 참조를 포함 하는 특수 한 항목입니다.

Azure Cosmos DB는 두 가지 유형의 첨부 파일을 지원 합니다.

* **관리 되지 않는 첨부 파일** 은 외부 서비스 (예: Azure Storage, OneDrive 등)에 저장 된 blob에 대 한 URI 참조를 둘러싸는 래퍼입니다. 이 방법은 표준 Azure Cosmos DB 항목에 URI 속성을 저장 하는 것과 비슷합니다.
* **관리 되는 첨부 파일** 은 Azure Cosmos DB에서 내부적으로 관리 및 저장 되 고 시스템 생성 mediaLink를 통해 노출 되는 blob입니다.


> [!NOTE]
> 첨부 파일은 레거시 기능입니다. 이 기능을 이미 사용 중인 경우 해당 지원 범위는 계속 해 서 기능을 제공 합니다.
> 
> 첨부 파일을 사용 하는 대신 blob 데이터를 저장 하는 데 사용 되는 용도의 blob 저장소 서비스로 Azure Blob Storage를 사용 하는 것이 좋습니다. 참조 URI 링크와 함께 blob와 관련 된 메타 데이터를 Azure Cosmos DB 항목 속성으로 계속 저장할 수 있습니다. Azure Cosmos DB에이 데이터를 저장 하면 Azure Blob Storage에 저장 된 blob에 대 한 메타 데이터 및 링크를 쿼리할 수 있습니다.
> 
> Microsoft는 완전히 사용 중단 첨부 파일 전에 최소 36 개월 통지를 제공 하기 위해 최선을 다하고 있으며,이는 추가 날짜에 발표 될 예정입니다.

## <a name="known-limitations"></a>알려진 제한 사항

Azure Cosmos DB의 관리 되는 첨부 파일은 표준 항목에 대해 지원 되는 것과 다릅니다 .이는 무제한의 확장성, 글로벌 배포 및 다른 Azure 서비스와의 통합을 제공 합니다.

- 첨부 파일은 모든 버전의 Azure Cosmos DB Sdk에서 지원 되지 않습니다.
- 관리 되는 첨부 파일은 데이터베이스 계정 마다 2gb의 저장소로 제한 됩니다.
- 관리 되는 첨부 파일은 Azure Cosmos DB의 전역 배포와 호환 되지 않으며 지역 간에 복제 되지 않습니다.

## <a name="migrating-attachments-to-azure-blob-storage"></a>첨부 파일을 Azure Blob Storage로 마이그레이션

다음 단계를 수행 하 여 Azure Cosmos DB 첨부 파일을 Azure Blob Storage로 마이그레이션하는 것이 좋습니다.

1. 원본 Azure Cosmos DB 컨테이너의 첨부 파일 데이터를 대상 Azure Blob Storage 컨테이너에 복사 합니다.
2. 대상 Azure Blob Storage 컨테이너에서 업로드 된 blob 데이터의 유효성을 검사 합니다.
3. 해당 하는 경우 Azure Cosmos DB 데이터 집합 내의 문자열 속성으로 Azure Blob Storage에 포함 된 blob에 대 한 URI 참조를 추가 합니다.
4. 새 Azure Blob Storage 컨테이너에서 blob을 읽고 쓰도록 응용 프로그램 코드를 리팩터링 합니다.

다음 dotnet 코드 샘플은 Azure Cosmos DB의 .NET SDK v2 및 Azure Blob Storage .NET SDK v12를 사용 하 여 마이그레이션 흐름의 일부로 Azure Cosmos DB에서 Azure Blob storage로 첨부 파일을 복사 하는 방법을 보여 줍니다. `<placeholder values>`원본 Azure Cosmos DB 계정 및 대상 Azure Blob storage 컨테이너에 대 한을 (를) 대체 해야 합니다.

```csharp

using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;

namespace attachments
{
    class Program
    {
        private static string cosmosAccount = "<Your_Azure_Cosmos_account_URI>";
        private static string cosmosKey = "<Your_Azure_Cosmos_account_PRIMARY_KEY>";
        private static string cosmosDatabaseName = "<Your_Azure_Cosmos_database>";
        private static string cosmosCollectionName = "<Your_Azure_Cosmos_collection>";
        private static string storageConnectionString = "<Your_Azure_Storage_connection_string>";
        private static string storageContainerName = "<Your_Azure_Storage_container_name>";
        private static DocumentClient cosmosClient = new DocumentClient(new Uri(cosmosAccount), cosmosKey);
        private static BlobServiceClient storageClient = new BlobServiceClient(storageConnectionString);
        private static BlobContainerClient storageContainerClient = storageClient.GetBlobContainerClient(storageContainerName);

        static void Main(string[] args)
        {
            CopyAttachmentsToBlobsAsync().Wait();
        }

        private async static Task CopyAttachmentsToBlobsAsync()
        {
            Console.WriteLine("Copying Azure Cosmos DB Attachments to Azure Blob Storage ...");

            int totalCount = 0;
            string docContinuation = null;

            // Iterate through each item (document in v2) in the Azure Cosmos DB container (collection in v2) to look for attachments.
            do
            {
                FeedResponse<dynamic> response = await cosmosClient.ReadDocumentFeedAsync(
                    UriFactory.CreateDocumentCollectionUri(cosmosDatabaseName, cosmosCollectionName),
                    new FeedOptions
                    {
                        MaxItemCount = -1,
                        RequestContinuation = docContinuation
                    });
                docContinuation = response.ResponseContinuation;

                foreach (Document document in response)
                {
                    string attachmentContinuation = null;

                    // Iterate through each attachment within the item (if any).
                    do
                    {
                        FeedResponse<Attachment> attachments = await cosmosClient.ReadAttachmentFeedAsync(
                            document.SelfLink,
                            new FeedOptions
                            {
                                PartitionKey = new PartitionKey(document.Id),
                                RequestContinuation = attachmentContinuation
                            }
                        );
                        attachmentContinuation = attachments.ResponseContinuation;

                        foreach (var attachment in attachments)
                        {
                            // Download the attachment in to local memory.
                            MediaResponse content = await cosmosClient.ReadMediaAsync(attachment.MediaLink);

                            byte[] buffer = new byte[content.ContentLength];
                            await content.Media.ReadAsync(buffer, 0, buffer.Length);

                            // Upload the locally buffered attachment to blob storage
                            string blobId = String.Concat(document.Id, "-", attachment.Id);

                            Azure.Response<BlobContentInfo> uploadedBob = await storageContainerClient.GetBlobClient(blobId).UploadAsync(
                                new MemoryStream(buffer, writable: false),
                                true
                            );

                            Console.WriteLine("Copied attachment ... Item Id: {0} , Attachment Id: {1}, Blob Id: {2}", document.Id, attachment.Id, blobId);
                            totalCount++;
                        }

                    } while (!string.IsNullOrEmpty(attachmentContinuation));
                }
            }
            while (!string.IsNullOrEmpty(docContinuation));

            Console.WriteLine("Finished copying {0} attachments to blob storage", totalCount);
        }
    }
}

```

## <a name="next-steps"></a>다음 단계

- [Azure Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet) 시작
- [Azure Cosmos DB의 .NET SDK v2](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.attachment?view=azure-dotnet) 를 통해 첨부 파일 사용에 대 한 참조 가져오기
- [Azure Cosmos DB의 JAVA SDK v2](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.attachment?view=azure-java-stable) 를 통해 첨부 파일 사용에 대 한 참조 가져오기
- [Azure Cosmos DB의 REST API](https://docs.microsoft.com/rest/api/cosmos-db/attachments) 를 통해 첨부 파일 사용에 대 한 참조 가져오기

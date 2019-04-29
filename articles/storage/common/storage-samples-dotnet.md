---
title: .NET을 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. .NET 저장소 클라이언트 라이브러리를 사용하여 BLOB, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: c4dcb3f42d97624e66258228ce27e130a05cac70
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101392"
---
# <a name="azure-storage-samples-using-net"></a>.NET을 사용한 Azure Storage 샘플

## <a name="net-sample-index"></a>.NET 샘플 인덱스

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">엔드포인트</th><th style="font-size:110%">시나리오</th><th style="font-size:110%">샘플 코드</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Blob 추가</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">CloudBlobContainer.GetAppendBlobReference 메서드 예제</a></td> 
</tr> 
<tr> 
<td>블록 Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage 사진 갤러리 웹 애플리케이션</a></td>
</tr> 
<tr> 
<td>클라이언트 쪽 암호화</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">BLOB 암호화 샘플</a></td>
</tr> 
<tr> 
<td>Blob 복사</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr> 
<tr> 
<td>컨테이너 만들기</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage 사진 갤러리 웹 애플리케이션</a></td>
</tr> 
<tr> 
<td>Blob 삭제</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage 사진 갤러리 웹 애플리케이션</a></td>
</tr> 
<tr> 
<td>컨테이너 삭제</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr> 
<tr> 
<td>BLOB 메타데이터/속성/통계</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr> 
<tr> 
<td>컨테이너 ACL/메타데이터/속성</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage 사진 갤러리 웹 애플리케이션</a></td>
</tr> 
<tr> 
<td>페이지 범위 가져오기</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr> 
<tr> 
<td>BLOB/컨테이너 임대</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr> 
<tr> 
<td>BLOB/컨테이너 나열</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">BLOB 시작</a></td>
</tr> 
<tr> 
<td>페이지 Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">BLOB 시작</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr>   
<tr> 
<td>서비스 속성</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB 시작</a></td>
</tr>           
<tr> 
<td>Blob 스냅숏</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">증분 스냅숏을 사용하여 Azure Virtual Machine 디스크 백업</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>파일</b></td>
<td>공유/디렉터리/파일 만들기</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr>
<tr> 
<td>공유/디렉터리/파일 삭제</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">.NET에서 Azure 파일 서비스 시작</a></td> 
</tr> 
<tr> 
<td>디렉터리 속성/메타데이터</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr> 
<tr> 
<td>파일 다운로드</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr> 
<tr> 
<td>파일 속성/메타데이터/메트릭</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr> 
<tr> 
<td>파일 서비스 속성</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr> 
<tr> 
<td>디렉터리 및 파일 나열</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr>
<tr> 
<td>공유 나열</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr>
<tr> 
<td>속성/메타데이터/통계 공유</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage 샘플</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>큐</b></td>
<td>메시지 추가</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td>클라이언트 쪽 암호화</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET 큐 클라이언트 쪽 암호화</a></td> 
</tr> 
<tr> 
<td>큐 만들기</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td>메시지/큐 삭제</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td>메시지 보기</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td>큐 ACL/메타데이터/통계</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td>큐 서비스 속성</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td>메시지 업데이트</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.NET에서 Azure 큐 서비스 시작</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>테이블</b></td>
<td>테이블 만들기</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션</a></td> 
</tr> 
<tr> 
<td>엔터티/테이블 삭제</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">.NET에서 Azure Table Storage 시작</a></td> 
</tr> 
<tr> 
<td>엔터티 삽입/병합/바꾸기</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션</a></td> 
</tr> 
<tr> 
<td>엔터티 쿼리</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">.NET에서 Azure Table Storage 시작</a></td> 
</tr> 
<tr> 
<td>쿼리 테이블</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">.NET에서 Azure Table Storage 시작</a></td> 
</tr> 
<tr> 
<td>테이블 ACL/속성</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">.NET에서 Azure Table Storage 시작</a></td> 
</tr> 
<tr> 
<td>엔터티 업데이트</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Azure 코드 샘플 라이브러리

코드 샘플 라이브러리를 보려면 다운로드하여 로컬로 실행할 수 있는 Azure Storage에 대한 샘플이 포함되어 있는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=storage) 라이브러리로 이동합니다. 코드 샘플 라이브러리에서 .zip 형식으로 샘플 코드를 제공합니다. 또는 각 샘플에 대한 GitHub 리포지토리를 찾아 복제할 수 있습니다.

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

* [.NET에서 Azure Blob 서비스 시작](../blobs/storage-dotnet-how-to-use-blobs.md)
* [.NET에서 Azure 큐 서비스 시작](../storage-dotnet-how-to-use-queues.md)
* [.NET에서 Azure 테이블 서비스 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET에서 Azure 파일 서비스 시작](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

* Java: [Java를 사용한 Azure Storage 샘플](storage-samples-java.md)
* 모든 기타 언어: [Azure Storage 샘플](../storage-samples.md)

---
title: Java를 사용한 Azure Storage 샘플 | Microsoft Docs
description: Azure Storage에 대한 샘플 코드 및 애플리케이션을 확인하고 다운로드하여 실행합니다. Java 저장소 클라이언트 라이브러리를 사용하여 BLOB, 큐, 테이블 및 파일에 대한 예제 시작을 검색합니다.
services: storage
author: seguler
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: d216313ae88837df395c64a3ba9e707a5bca06a5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101883"
---
# <a name="azure-storage-samples-using-java"></a>Java를 사용한 Azure Storage 샘플

## <a name="java-sample-index"></a>Java 샘플 인덱스

다음 테이블에서는 샘플 리포지토리 및 각 샘플에서 다루는 시나리오에 대한 개요를 제공합니다. GitHub에서 해당 샘플 코드를 보려면 링크를 클릭합니다.

<table style="font-size:90%"><thead><tr><th style="font-size:110%">엔드포인트</th><th style="font-size:110%">시나리오</th><th style="font-size:110%">샘플 코드</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>Blob</b></td>
<td>Blob 추가</td> 
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td> 
</tr> 
<tr> 
<td>블록 Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>클라이언트 쪽 암호화</td>
<td><a href="https://github.com/Azure-Samples/storage-java-client-side-encryption">Java에서 Azure 클라이언트 쪽 암호화 시작</a></td>
</tr> 
<tr> 
<td>Blob 복사</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>컨테이너 만들기</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>Blob 삭제</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>컨테이너 삭제</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>BLOB 메타데이터/속성/통계</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>컨테이너 ACL/메타데이터/속성</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>페이지 범위 가져오기</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-test/src/com/microsoft/azure/storage/blob/CloudPageBlobTests.java">페이지 BLOB 테스트 샘플</a></td>
</tr> 
<tr> 
<td>BLOB/컨테이너 임대</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>BLOB/컨테이너 나열</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td>페이지 Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513">SAS 테스트 샘플</a></td>
</tr>   
<tr> 
<td>서비스 속성</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java">Java에서 Azure Blob Service 시작</a></td>
</tr>           
<tr> 
<td>Blob 스냅숏</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java">Java에서 Azure Blob Service 시작</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>파일</b></td>
<td>공유/디렉터리/파일 만들기</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Java에서 Azure File Service 시작</a></td> 
</tr>
<tr> 
<td>공유/디렉터리/파일 삭제</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Java에서 Azure File Service 시작</a></td> 
</tr> 
<tr> 
<td>디렉터리 속성/메타데이터</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Java에서 Azure File Service 시작</a></td> 
</tr> 
<tr> 
<td>파일 다운로드</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Java에서 Azure File Service 시작</a></td> 
</tr> 
<tr> 
<td>파일 속성/메타데이터/메트릭</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Java에서 Azure File Service 시작</a></td> 
</tr> 
<tr> 
<td>파일 서비스 속성</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Java에서 Azure File Service 시작</a></td> 
</tr> 
<tr> 
<td>디렉터리 및 파일 나열</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Java에서 Azure File Service 시작</a></td> 
</tr>
<tr> 
<td>공유 나열</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java">Java에서 Azure File Service 시작</a></td> 
</tr>
<tr> 
<td>속성/메타데이터/통계 공유</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java">Java에서 Azure File Service 시작</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>큐</b></td>
<td>메시지 추가</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/queue/gettingstarted/QueueBasics.java">Storage Java 클라이언트 라이브러리 샘플</a></td> 
</tr> 
<tr> 
<td>클라이언트 쪽 암호화</td> 
<td><a href="https://github.com/Azure/azure-storage-java/blob/master/microsoft-azure-storage-samples/src/com/microsoft/azure/storage/encryption/queue/gettingstarted/QueueGettingStarted.java">Storage Java 클라이언트 라이브러리 샘플</a></td> 
</tr> 
<tr> 
<td>큐 만들기</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Java에서 Azure Queue Service 시작</a></td> 
</tr> 
<tr> 
<td>메시지/큐 삭제</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Java에서 Azure Queue Service 시작</a></td> 
</tr> 
<tr> 
<td>메시지 보기</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Java에서 Azure Queue Service 시작</a></td> 
</tr> 
<tr> 
<td>큐 ACL/메타데이터/통계</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Java에서 Azure Queue Service 시작</a></td> 
</tr> 
<tr> 
<td>큐 서비스 속성</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java">Java에서 Azure Queue Service 시작</a></td> 
</tr> 
<tr> 
<td>메시지 업데이트</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java">Java에서 Azure Queue Service 시작</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>테이블</b></td>
<td>테이블 만들기</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
<tr> 
<td>엔터티/테이블 삭제</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
<tr> 
<td>엔터티 삽입/병합/바꾸기</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
<tr> 
<td>엔터티 쿼리</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
<tr> 
<td>쿼리 테이블</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
<tr> 
<td>테이블 ACL/속성</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
<tr> 
<td>엔터티 업데이트</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java">Java에서 Azure Table Service 시작</a></td> 
</tr> 
</tbody> 
</table>
<br/>

## <a name="azure-code-samples-library"></a>Azure 코드 샘플 라이브러리

코드 샘플 라이브러리를 보려면 다운로드하여 로컬로 실행할 수 있는 Azure Storage에 대한 샘플이 포함되어 있는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=storage) 라이브러리로 이동합니다. 코드 샘플 라이브러리에서 .zip 형식으로 샘플 코드를 제공합니다. 또는 각 샘플에 대한 GitHub 리포지토리를 찾아 복제할 수 있습니다.

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>시작 가이드

Azure Storage 클라이언트 라이브러리를 설치하고 시작하는 방법에 대한 지침은 찾고 있는 경우 다음 가이드를 확인해 보세요.

* [Java에서 Azure Blob Service 시작](../blobs/storage-quickstart-blobs-java.md)
* [Java에서 Azure Queue Service 시작](../queues/storage-java-how-to-use-queue-storage.md)
* [Java에서 Azure Table Service 시작](../../cosmos-db/table-storage-how-to-use-java.md)
* [Java에서 Azure File Service 시작](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>다음 단계

다른 언어용 샘플에 대한 정보:

* .NET: [.NET을 사용한 Azure Storage 샘플](storage-samples-dotnet.md)
* 모든 기타 언어: [Azure Storage 샘플](storage-samples.md)

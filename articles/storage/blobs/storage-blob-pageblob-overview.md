---
title: Azure 페이지 Blob의 개요 | Microsoft Docs
description: 샘플 스크립트를 통한 사용 사례를 포함한 Azure 페이지 Blob의 개요 및 해당 이점
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 542c9374b70cd765ed27dd4dd158ad81035269f0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018844"
---
# <a name="overview-of-azure-page-blobs"></a>Azure 페이지 Blob의 개요

Azure Storage에는 블록 Blob, 추가 Blob 및 페이지 Blob의 세 가지 유형의 Blob Storage가 제공됩니다. 블록 Blob은 블록으로 구성되며 텍스트 또는 이진 파일을 저장하고 큰 파일을 효율적으로 업로드하는 데 적합합니다. 추가 Blob도 블록으로 구성되지만 추가 작업에 최적화되어 있으므로 로깅 시나리오에 적합합니다. 페이지 Blob의 전체 크기는 512바이트 페이지(최대 8TB)로 구성되고 빈번한 임의의 읽기/쓰기 작업에 맞게 고안되었습니다. 페이지 Blob은 Azure IaaS 디스크의 기반이 됩니다. 이 문서에서는 페이지 Blob의 기능 및 이점을 중점적으로 설명합니다.

페이지 Blob은 임의의 바이트 범위에 대한 읽기/쓰기 기능을 제공하는 512바이트 페이지의 컬렉션입니다. 따라서 페이지 Blob은 가상 머신과 데이터베이스의 OS 및 데이터 디스크와 같은 인덱스 기반 및 스파스 데이터 구조를 저장하는 데 적합합니다. 예를 들어 Azure SQL DB는 해당 데이터베이스에 대한 기본 영구적 스토리지로 페이지 Blob을 사용합니다. 또한 페이지 Blob은 범위 기반 업데이트가 포함된 파일에도 자주 사용됩니다.  

Azure 페이지 Blob의 주요 기능은 해당 REST 인터페이스, 기본 스토리지의 내구성 및 Azure로 원활한 마이그레이션 기능입니다. 이러한 기능은 다음 섹션에서 자세히 설명합니다. 또한 Azure 페이지 Blob은 현재 두 가지 유형의 스토리지(Premium Storage 및 Standard Storage)에서 지원됩니다. Premium Storage는 고성능 저장소 시나리오에 적합 한 프리미엄 페이지 blob을 만들기 위해 일관 된 고성능 및 짧은 대기 시간을 요구 하는 워크 로드를 위해 특별히 설계 되었습니다. 표준 저장소 계정은 대기 시간을 구분 하지 않는 워크 로드를 실행 하는 데 더 비용 효율적입니다.

## <a name="sample-use-cases"></a>샘플 사용 사례

Azure IaaS 디스크로 시작하는 페이지 Blob에 대한 몇 가지 사용 사례를 설명하겠습니다. Azure 페이지 Blob은 Azure IaaS 가상 디스크 플랫폼의 백본입니다. Azure OS 및 데이터 디스크는 둘 다 Azure Storage 플랫폼에서 데이터를 지속적으로 유지하고 최대 성능을 위해 가상 머신에 전달되는 가상 디스크로 구현됩니다. Azure 디스크는 Hyper-V [VHD 형식](https://technet.microsoft.com/library/dd979539.aspx)으로 유지되고 Azure Storage에 [페이지 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)으로 저장됩니다. Azure IaaS VM의 가상 디스크 사용 외에도 페이지 Blob은 Azure SQL DB 서비스와 같은 PaaS 및 DBaaS 시나리오를 사용할 수 있습니다. 이 서비스는 현재 SQL 데이터를 저장하기 위해 페이지 Blob을 사용하여 데이터베이스에 대한 임의의 빠른 읽기/쓰기 작업이 가능합니다. 또 다른 예로 공동 작업 비디오 편집 애플리케이션의 공동 미디어 액세스를 위해 PaaS 서비스를 사용하는 경우 페이지 Blob을 통해 임의의 미디어 위치에 빠르게 액세스할 수 있습니다. 또한 사용자가 여러 명인 동일한 미디어를 빠르고 효율적으로 편집하고 병합할 수 있습니다. 

Azure Site Recovery, Azure Backup과 같은 자사의 Microsoft 서비스뿐만 아니라 많은 타사 개발자는 페이지 Blob의 REST 인터페이스를 사용하여 업계를 주도하는 혁신을 구현했습니다. 다음은 Azure에서 구현되는 몇 가지 고유한 시나리오입니다. 

* 애플리케이션 지향 증분 스냅샷 관리: 애플리케이션은 페이지 Blob 스냅샷 및 REST API를 활용하여 데이터의 비용이 중복되지 않게 애플리케이션 검사점을 저장할 수 있습니다. Azure Storage는 전체 Blob을 복사할 필요가 없는 페이지 Blob에 대한 로컬 스냅샷을 지원합니다. 또한 이러한 공용 스냅샷 API를 사용하면 스냅샷 간 델타 액세스 및 복사가 가능합니다.
* 온-프레미스에서 클라우드로 응용 프로그램 및 데이터 실시간 마이그레이션: 온-프레미스 데이터를 복사 하 고 REST Api를 사용 하 여 온-프레미스 VM이 계속 실행 되는 동안 Azure 페이지 blob에 직접 쓸 수 있습니다. 대상을 따라잡으면 해당 데이터를 사용하여 신속하게 Azure VM에 장애 조치(failover)할 수 있습니다. 이렇게 하면 VM을 계속 사용 하면서 데이터 마이그레이션이 백그라운드에서 수행 되 고 장애 조치 (failover)에 필요한 가동 중지 시간이 짧은 시간 (분) 이므로, 이러한 방식으로 온-프레미스에서 클라우드로 Vm 및 가상 디스크를 마이그레이션할 수 있습니다.
* [SAS 기반](../common/storage-sas-overview.md) 공유 액세스: 동시성 제어를 지원하는 다중 판독기 및 단일 작성기와 같은 시나리오를 사용할 수 있습니다.

## <a name="pricing"></a>가격 책정

페이지 blob과 함께 제공 되는 두 가지 저장소 유형 모두에는 고유한 가격 책정 모델이 있습니다. 프리미엄 페이지 blob은 managed disks 가격 책정 모델을 따르고, 표준 페이지 blob은 사용 된 크기와 각 트랜잭션에 대해 요금이 청구 됩니다. 자세한 내용은 [Azure 페이지 blob 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/page-blobs/)를 참조 하세요.

## <a name="page-blob-features"></a>페이지 Blob 기능

### <a name="rest-api"></a>REST API

[페이지 Blob을 사용하여 개발](storage-dotnet-how-to-use-blobs.md)을 시작하려면 다음 문서를 참조하세요. 예를 들어 .NET용 스토리지 클라이언트 라이브러리를 사용하여 페이지 Blob에 액세스하는 방법을 살펴보겠습니다. 

다음 다이어그램은 계정, 컨테이너 및 페이지 Blob 간의 전반적인 관계를 설명합니다.

![계정, 컨테이너 및 페이지 blob 간의 관계를 보여 주는 스크린샷](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>지정된 크기의 빈 페이지 Blob 만들기

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

먼저, 컨테이너에 대 한 참조를 가져옵니다. 페이지 blob을 만들려면 [Getpageblobclient](/dotnet/api/azure.storage.blobs.specialized.specializedblobextensions.getpageblobclient) 메서드를 호출한 다음 [Pageblobclient. create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) 메서드를 호출 합니다. 만들 blob의 최대 크기를 전달 합니다. 이 크기는 512 바이트의 배수 여야 합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

페이지 blob을 만들려면 먼저 다음 예제와 같이 저장소 계정에 대 한 blob 저장소 (그림 1의*paccount* )와 함께 저장소 계정에 대 한 blob 저장소에 액세스 하기 위한 기본 URI를 사용 하 여 **CloudBlobClient** **개체를** 만듭니다. 그런 다음이 예제에서는 **CloudBlobContainer** 개체에 대 한 참조를 만든 다음, 아직 없는 경우 컨테이너 (*testvhds*)를 만드는 방법을 보여 줍니다. 그런 다음, **CloudBlobContainer** 개체를 사용하여 액세스할 페이지 Blob 이름(os4.vhd)을 지정하여 **CloudPageBlob** 개체에 대한 참조를 만듭니다. 페이지 blob을 만들려면 만들 blob의 최대 크기를 전달 하 여 [Cloudpageblob](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)을 호출 합니다. blobSize** 는 512바이트의 배수여야 합니다.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>페이지 Blob 크기 조정

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

페이지 blob을 만든 후 크기를 조정 하려면 [resize](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize?view=azure-dotnet) 메서드를 사용 합니다. 요청 크기는 512바이트의 배수여야 합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

페이지 blob을 만든 후 크기를 조정 하려면 [resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) 메서드를 사용 합니다. 요청 크기는 512바이트의 배수여야 합니다.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>페이지 Blob에 페이지 쓰기

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

페이지를 쓰려면 [Pageblobclient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) 메서드를 사용 합니다.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

페이지를 쓰려면 [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) 메서드를 사용합니다.  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

이 메서드를 사용하면 최대 4MB의 순차적 페이지 집합을 작성할 수 있습니다. 작성되는 오프셋은 512바이트 경계(startingOffset % 512 == 0)에서 시작하고 512바이트 경계 -1에서 종료해야 합니다. 

순차적 페이지 집합에 대한 쓰기 요청이 Blob Service에서 성공하고 내구성 및 복원력을 위해 복제되는 즉시 쓰기가 커밋되고 클라이언트에 성공이 다시 반환됩니다.  

아래 다이어그램은 2개의 별도 쓰기 작업을 보여 줍니다.

![별도의 두 쓰기 옵션을 보여 주는 다이어그램입니다.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  오프셋 0에서 시작하는 쓰기 작업(1024바이트) 
2.  오프셋 4096에서 시작하는 쓰기 작업(1024바이트) 

#### <a name="reading-pages-from-a-page-blob"></a>페이지 Blob에서 페이지 읽기

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

페이지를 읽으려면 [Pageblobclient. Download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) 메서드를 사용 하 여 페이지 blob에서 바이트 범위를 읽습니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

페이지를 읽으려면 [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) 메서드를 사용하여 페이지 Blob에서 바이트 범위를 읽습니다. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

이 메서드를 사용하면 전체 Blob 또는 Blob의 임의 오프셋에서 시작하는 바이트 범위를 다운로드할 수 있습니다. 읽을 때 오프셋은 512의 배수로 시작할 필요가 없습니다. NUL 페이지에서 바이트를 읽을 때 서비스는 0바이트를 반환합니다.

다음 그림에서는 오프셋이 256이 고 범위 크기가 4352 인 읽기 작업을 보여 줍니다. 반환 된 데이터는 주황색으로 강조 표시 됩니다. NUL 페이지에 대해 0이 반환 됩니다.

![256 오프셋 및 4352의 범위 크기를 사용 하는 읽기 작업을 보여 주는 다이어그램](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

빈약하게 채워진 Blob이 있는 경우 유효한 페이지 영역만 다운로드하여 0바이트 송신에 대한 요금을 방지하고 다운로드 대기 시간을 줄이는 것이 좋습니다.  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

데이터가 지원 되는 페이지를 확인 하려면 [Pageblobclient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges)를 사용 합니다. 그런 다음 반환되는 범위를 열거하고 각 범위의 데이터를 다운로드할 수 있습니다. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

데이터가 지원하는 페이지를 파악하려면 [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges)를 사용합니다. 그런 다음 반환되는 범위를 열거하고 각 범위의 데이터를 다운로드할 수 있습니다. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>페이지 Blob 임대

Blob 임대 작업은 Blob에 대한 쓰기 및 삭제 작업 잠금을 설정하고 관리합니다. 이 작업은 한 번에 하나의 클라이언트만 Blob에 쓸 수 있도록 여러 클라이언트에서 페이지 Blob에 액세스하는 시나리오에서 유용합니다. 예를 들어 Azure 디스크는 이 임대 메커니즘을 활용하여 디스크가 단일 VM에서만 관리되도록 합니다. 잠금 기간은 15~60초 또는 무한할 수 있습니다. 자세한 내용은 [여기](/rest/api/storageservices/lease-blob) 설명서를 참조하세요.

페이지 blob은 다양 한 REST Api 외에도 공유 액세스, 내구성 및 향상 된 보안을 제공 합니다. 다음 단락에서 이러한 이점에 대해 자세하게 설명합니다. 

### <a name="concurrent-access"></a>동시 액세스

페이지 Blob REST API 및 해당 임대 메커니즘을 통해 애플리케이션에서 여러 클라이언트의 페이지 Blob에 액세스할 수 있습니다. 예를 들어 여러 사용자와 스토리지 개체를 공유하는 분산 클라우드 서비스를 빌드해야 한다고 가정해 보겠습니다. 여러 사용자에게 대규모의 이미지 컬렉션을 제공하는 웹 애플리케이션이 될 수 있습니다. 이를 구현하는 한 가지 옵션은 디스크가 연결된 VM을 사용하는 것입니다. 이 방법의 단점은 (i) 단일 VM에만 디스크를 연결할 수 있는 제약 조건으로 인해 확장성, 유연성이 제한되고 위험이 증가된다는 점입니다. VM 또는 VM에서 실행되는 서비스에 문제가 있는 경우 임대가 만료되거나 중단될 때까지 임대로 인해 이미지에 액세스할 수 없게 됩니다. (ii) IaaS VM에 대한 추가 비용이 발생합니다. 

다른 옵션은 Azure Storage REST API를 통해 직접 페이지 Blob을 사용하는 것입니다. 이 옵션은 비용이 많이 드는 IaaS VM이 필요하지 않으며, 여러 클라이언트에서 직접 액세스할 수 있는 완벽한 유연성을 제공하고, 디스크를 연결/분리할 필요가 없도록 하여 클래식 배포 모델을 간소화하며, VM에서 문제가 발생할 위험을 제거합니다. 또한 임의의 읽기/쓰기 작업에 대해 디스크와 동일한 수준의 성능을 제공합니다.

### <a name="durability-and-high-availability"></a>내구성 및 고가용성

Standard Storage 및 Premium Storage는 둘 다 내구성과 고가용성이 보장되도록 페이지 Blob 데이터가 항상 복제되는 지속성 스토리지입니다. Azure Storage 중복에 대한 자세한 내용은 이 [설명서](../common/storage-redundancy.md)를 참조하세요. Azure는 IaaS 디스크 및 페이지 blob에 대 한 엔터프라이즈급 내구성을 지속적으로 제공 하 고 업계 최고의 [연간 실패율로 실패율](https://en.wikipedia.org/wiki/Annualized_failure_rate)을 제공 합니다.

### <a name="seamless-migration-to-azure"></a>Azure로 원활한 마이그레이션

Azure는 자체 사용자 지정 백업 솔루션 구현에 관심이 있는 고객 및 개발자에게 델타만 보유하는 증분 스냅샷도 제공합니다. 이 기능은 초기 전체 복사의 비용을 방지하여 백업 비용을 크게 낮출 수 있습니다. 이 기능은 효율적으로 차등 데이터를 읽고 복사하는 기능과 더불어 개발자가 기능을 한층 더 혁신할 수 있도록 해주는 또 하나의 강력한 기능입니다. 이를 통해 Azure에서 최상의 백업 및 DR(재해 복구) 환경을 제공할 수 있습니다. DR에 대한 증분 데이터를 쉽게 복사하는 데 사용할 수 있는 [페이지 범위 가져오기](/rest/api/storageservices/get-page-ranges) API 및 [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob) API와 함께 [Blob 스냅샷](/rest/api/storageservices/snapshot-blob)을 사용하여 Azure에서 VM에 대한 사용자 고유의 백업 또는 DR 솔루션을 설정할 수 있습니다. 

게다가 많은 엔터프라이즈에는 온-프레미스 데이터 센터에서 이미 실행 중인 중요한 워크로드가 있습니다. 워크로드를 클라우드로 마이그레이션하는 데 우려 사항 중 하나는 해당 데이터를 복사하는 데 필요한 가동 중단 시간과 전환 후 예측하지 못한 문제 발생 위험입니다. 대부분의 경우 가동 중단 시간은 클라우드로의 마이그레이션을 위한 쇼스타퍼(showstopper)일 수 있습니다. Azure는 페이지 Blob REST API를 사용하여 중요한 워크로드의 중단을 최소화하면서 클라우드를 마이그레이션하여 이 문제를 해결합니다. 

스냅샷을 만드는 방법 및 스냅샷에서 페이지 Blob을 복원하는 방법에 대한 예제는 [증분 스냅샷을 사용하여 백업 프로세스 설정](../../virtual-machines/windows/incremental-snapshots.md) 문서를 참조하세요.

---
title: 리소스 파일 만들기 및 사용-Azure Batch
description: 다양 한 입력 소스에서 배치 리소스 파일을 만드는 방법을 알아봅니다. 이 문서에서는 VM을 만들고 VM에 추가 하는 방법에 대한 몇 가지 일반적인 방법을 설명 합니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: labrenne
ms.openlocfilehash: dd8046891362cf4d4d7eed805fbc13d0f784a99c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019267"
---
# <a name="creating-and-using-resource-files"></a>리소스 파일 만들기 및 사용

Azure Batch 태스크에는 처리 해야 하는 일종의 데이터가 필요 합니다. 리소스 파일은 작업을 통해 Batch VM (가상 머신)에이 데이터를 제공 하는 수단입니다. 모든 유형의 작업은 작업, 시작 태스크, 작업 준비 태스크, 작업 릴리스 태스크 등의 리소스 파일을 지원 합니다. 이 문서에서는 리소스 파일을 만들고 VM에 추가 하는 방법에 대한 몇 가지 일반적인 방법을 설명 합니다.  

리소스 파일은 VM에 데이터를 배치 하는 메커니즘 이지만 데이터 유형과 사용 방법의 유연성이 뛰어납니다. 그러나 다음과 같은 몇 가지 일반적인 사용 사례가 있습니다.

1. 시작 작업에서 리소스 파일을 사용 하 여 각 VM에 공용 파일 프로 비전
1. 작업으로 처리할 입력 데이터 프로 비전

예를 들어 일반적인 파일은 작업에서 실행 되는 응용 프로그램을 설치 하는 데 사용 되는 시작 작업의 파일 일 수 있습니다. 입력 데이터는 원시 이미지 또는 비디오 데이터 이거나 일괄 처리로 처리 될 정보 일 수 있습니다.

## <a name="types-of-resource-files"></a>리소스 파일 형식

리소스 파일을 생성 하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 리소스 파일에 대한 생성 프로세스는 원래 데이터가 저장 되는 위치에 따라 달라 집니다.

리소스 파일을 만드는 옵션:

- [저장소 컨테이너 URL](#storage-container-url): Azure의 모든 저장소 컨테이너에서 리소스 파일을 생성 합니다.
- [저장소 컨테이너 이름](#storage-container-name): 일괄 처리에 연결 된 Azure 저장소 계정의 컨테이너 이름에서 리소스 파일을 생성 합니다.
- [웹 엔드포인트](#web-endpoint): 유효한 모든 HTTP URL에서 리소스 파일을 생성 합니다.

### <a name="storage-container-url"></a>저장소 컨테이너 URL

저장소 컨테이너 URL을 사용 하면 올바른 권한을 사용 하 여 Azure의 모든 저장소 컨테이너에 있는 파일에 액세스할 수 있습니다.

이 C# 예제에서는 파일이 blob 저장소로 Azure 저장소 컨테이너에 이미 업로드 되었습니다. 리소스 파일을 만드는 데 필요한 데이터에 액세스 하려면 먼저 저장소 컨테이너에 액세스할 수 있어야 합니다.

저장소 컨테이너에 액세스할 수 있는 올바른 권한을 사용 하 여 SAS (공유 액세스 서명) URI를 만듭니다. SAS의 만료 시간 및 사용 권한을 설정 합니다. 이 경우 시작 시간은 지정 되지 않으므로 SAS가 즉시 유효 하 게 되어 생성 된 후 2 시간이 지나면 만료 됩니다.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 컨테이너 액세스의 경우 `Read` 및 `List` 권한이 모두 있어야 하 고 blob 액세스를 사용 하려면 `Read` 권한만 있으면 됩니다.

사용 권한이 구성 되 면 SAS 토큰을 만들고 저장소 컨테이너에 대한 액세스를 위한 SAS URL의 형식을 지정 합니다. 저장소 컨테이너에 대해 형식이 지정 된 SAS URL을 사용 하 여 [`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)를 사용 하 여 리소스 파일을 생성 합니다.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

SAS URL을 생성 하는 대신, Azure Blob 저장소에서 컨테이너 및 해당 blob에 대한 익명의 공용 읽기 액세스를 사용 하도록 설정할 수 있습니다. 이렇게 하면 계정 키를 공유 하지 않고 SAS를 요구 하지 않고 해당 리소스에 대한 읽기 전용 액세스 권한을 부여할 수 있습니다. 공용 읽기 액세스는 일반적으로 익명 읽기 액세스에 특정 blob을 항상 사용할 수 있도록 하려는 시나리오에 사용 됩니다. 이 시나리오가 솔루션에 적합 한 경우 blob에 대한 [익명 액세스](../storage/blobs/storage-manage-access-to-resources.md) 문서를 참조 하 여 blob 데이터에 대한 액세스를 관리 하는 방법에 대해 자세히 알아보세요.

### <a name="storage-container-name"></a>저장소 컨테이너 이름

SAS URL을 구성 하 고 만드는 대신 Azure storage 컨테이너의 이름을 사용 하 여 blob 데이터에 액세스할 수 있습니다. 사용 되는 저장소 컨테이너는 Batch 계정에 연결 된 Azure storage 계정 (autostorage-keys 계정 이라고 함)에 있어야 합니다. Autostorage-keys 계정의 저장소 컨테이너 이름을 사용 하면 저장소 컨테이너에 액세스 하는 SAS URL 구성 및 생성을 무시할 수 있습니다.

이 예제에서는 리소스 파일을 만드는 데 사용 되는 데이터가 Batch 계정에 연결 된 Azure Storage 계정에 이미 있다고 가정 합니다. Autostorage-keys 계정이 없는 경우 계정을 만들고 연결 하는 방법에 대한 자세한 내용은 [Batch 계정 만들기](batch-account-create-portal.md) 의 단계를 참조 하세요.

연결 된 저장소 계정을 사용 하 여 저장소 컨테이너에 대한 SAS URL을 만들고 구성할 필요가 없습니다. 대신, 연결 된 저장소 계정에 저장소 컨테이너의 이름을 제공 합니다.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>웹 엔드포인트

Azure Storage에 업로드 되지 않은 데이터는 리소스 파일을 만드는 데에도 사용할 수 있습니다. 입력 데이터를 포함 하는 유효한 HTTP URL을 지정할 수 있습니다. URL은 Batch API에 제공 된 다음 데이터를 사용 하 여 리소스 파일을 만듭니다.

다음 C# 예제에서는 입력 데이터가 가상의 GitHub 엔드포인트에서 호스팅됩니다. API는 유효한 웹 엔드포인트에서 파일을 검색 하 고 작업에서 사용할 리소스 파일을 생성 합니다. 이 시나리오에는 자격 증명이 필요 하지 않습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

각 Azure Batch 태스크는 파일을 다르게 사용 하므로 Batch에서 작업에 대한 파일을 관리 하는 데 사용할 수 있는 옵션이 있습니다. 다음 시나리오는 포괄적이 지는 않지만 대신 몇 가지 일반적인 상황을 다루고 권장 사항을 제공 합니다.

### <a name="many-resource-files"></a>많은 리소스 파일

Batch 작업에는 모두 동일한 공용 파일을 사용 하는 여러 작업이 포함 될 수 있습니다. 일반적인 작업 파일이 여러 작업에서 공유 되는 경우 리소스 파일을 사용 하는 대신 응용 프로그램 패키지를 사용 하 여 파일을 포함 하는 것이 더 나은 옵션 일 수 있습니다. 응용 프로그램 패키지는 다운로드 속도 최적화를 제공 합니다. 또한 응용 프로그램 패키지의 데이터는 태스크 간에 캐시 되므로 작업 파일이 자주 변경 되지 않으면 응용 프로그램 패키지가 솔루션에 적합 한 것일 수 있습니다. 응용 프로그램 패키지를 사용 하면 여러 리소스 파일을 수동으로 관리 하거나 SAS Url을 생성 하 여 Azure Storage의 파일에 액세스할 필요가 없습니다. Batch는 응용 프로그램 패키지를 저장 하 고 계산 노드에 배포 하는 Azure Storage 백그라운드에서 작동 합니다.

각 태스크에 해당 작업에 고유한 많은 파일이 있는 경우 리소스 파일은 대부분 최상의 옵션입니다. 고유한 파일을 사용 하는 작업은 업데이트 하거나 교체 해야 하는 경우가 많으며 응용 프로그램 패키지 콘텐츠를 사용 하는 것이 쉽지 않습니다. 리소스 파일은 개별 파일의 업데이트, 추가 또는 편집에 대한 추가 유연성을 제공 합니다.

### <a name="number-of-resource-files-per-task"></a>작업당 리소스 파일 수

태스크에 여러 개의 리소스 파일이 지정 된 경우 Batch에서 태스크가 너무 커서 태스크가 거부 될 수 있습니다. 작업 자체에서 리소스 파일 수를 최소화 하 여 작업을 작게 유지 하는 것이 가장 좋습니다.

태스크에 필요한 파일 수를 최소화할 방법이 없는 경우 리소스 파일의 저장소 컨테이너를 참조 하는 단일 리소스 파일을 만들어 작업을 최적화할 수 있습니다. 이렇게 하려면 리소스 파일을 Azure Storage 컨테이너에 넣고 리소스 파일의 다른 "컨테이너" 모드를 사용 합니다. Blob 접두사 옵션을 사용 하 여 작업에 대해 다운로드할 파일 컬렉션을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 리소스 파일에 대한 대 안으로 [응용 프로그램 패키지](batch-application-packages.md) 에 대해 알아봅니다.
- 리소스 파일에 컨테이너를 사용 하는 방법에 대한 자세한 내용은 [컨테이너 작업](batch-docker-container-workloads.md)을 참조 하세요.
- 작업에서 출력 데이터를 수집 하 고 저장 하는 방법을 알아보려면 [작업 및 태스크 출력 유지](batch-task-output.md)를 참조 하세요.
- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
